---
title: API HTTP [!DNL Asset Compute Service]
description: "[!DNL Asset Compute Service] API HTTP para criar aplicativos personalizados."
exl-id: 4b63fdf9-9c0d-4af7-839d-a95e07509750
source-git-commit: f15b9819d3319d22deccdf7e39c0f72728baaa39
workflow-type: tm+mt
source-wordcount: '2862'
ht-degree: 2%

---

# API HTTP [!DNL Asset Compute Service] {#asset-compute-http-api}

O uso da API é limitado a fins de desenvolvimento. A API é fornecida como um contexto ao desenvolver aplicativos personalizados. [!DNL Adobe Experience Manager] como [!DNL Cloud Service] usa a API para transmitir as informações de processamento para um aplicativo personalizado. Para obter mais informações, consulte [Usar microsserviços de ativos e Perfis de processamento](https://experienceleague.adobe.com/pt-br/docs/experience-manager-cloud-service/content/assets/manage/asset-microservices-configure-and-use).

>[!NOTE]
>
>[!DNL Asset Compute Service] está disponível apenas para uso com [!DNL Experience Manager] as a [!DNL Cloud Service].

Qualquer cliente da API HTTP [!DNL Asset Compute Service] deve seguir este fluxo de alto nível:

1. Um cliente é provisionado como um projeto [!DNL Adobe Developer Console] em uma organização IMS. Cada cliente separado (sistema ou ambiente) requer seu próprio projeto separado para separar o fluxo de dados do evento.

1. Um cliente gera um token de acesso para a conta técnica usando a [Autenticação JWT (Conta de Serviço)](https://developer.adobe.com/developer-console/docs/guides/).

1. Um cliente chama [`/register`](#register) apenas uma vez para recuperar a URL do diário.

1. Um cliente chama [`/process`](#process-request) para cada ativo para o qual deseja gerar representações. A chamada é assíncrona.

1. Um cliente sonda regularmente o diário para [receber eventos](#asynchronous-events). Ele recebe eventos para cada representação solicitada quando a representação é processada com êxito (`rendition_created` tipo de evento) ou se há um erro (`rendition_failed` tipo de evento).

O módulo [adobe-asset-compute-client](https://github.com/adobe/asset-compute-client) facilita o uso da API no código Node.js.

## Autenticação e autorização {#authentication-and-authorization}

Todas as APIs exigem autenticação de token de acesso. As solicitações devem definir os seguintes cabeçalhos:

1. Cabeçalho `Authorization` com token de portador, que é o token de conta técnica, recebido via [JWT exchange](https://developer.adobe.com/developer-console/docs/guides/) do projeto Adobe Developer Console. Os [escopos](#scopes) estão documentados abaixo.

<!-- TBD: Change the existing URL to a new path when a new path for docs is available. The current path contains master word that is not an inclusive term. Logged ticket in Adobe I/O's GitHub repo to get a new URL.
-->

1. Cabeçalho `x-gw-ims-org-id` com a ID da organização IMS.

1. `x-api-key` com a ID de cliente do projeto [!DNL Adobe Developers Console].

### Escopos {#scopes}

Verifique os seguintes escopos para o token de acesso:

* `openid`
* `AdobeID`
* `asset_compute`
* `read_organizations`
* `event_receiver`
* `event_receiver_api`
* `adobeio_api`
* `additional_info.roles`
* `additional_info.projectedProductContext`

Esses escopos exigem que o projeto [!DNL Adobe Developer Console] seja inscrito nos serviços `Asset Compute`, `I/O Events` e `I/O Management API`. O detalhamento de escopos individuais é:

* Básico
   * escopos: `openid,AdobeID`

* Asset compute
   * metascope: `asset_compute_meta`
   * escopos: `asset_compute,read_organizations`

* Adobe [!DNL `I/O Events`]
   * metascope: `event_receiver_api`
   * escopos: `event_receiver,event_receiver_api`

* Adobe [!DNL `I/O Management API`]
   * metascope: `ent_adobeio_sdk`
   * escopos: `adobeio_api,additional_info.roles,additional_info.projectedProductContext`

## Registro {#register}

Cada cliente do [!DNL Asset Compute service] - um projeto [!DNL Adobe Developer Console] exclusivo que assinou o serviço - deve [se registrar](#register-request) antes de fazer solicitações de processamento. A etapa de registro retorna o diário de eventos exclusivo necessário para recuperar os eventos assíncronos do processamento de representação.

No final de seu ciclo de vida, um cliente pode [cancelar o registro](#unregister-request).

### Registrar solicitação {#register-request}

Esta chamada de API configura um cliente [!DNL Asset Compute] e fornece a URL do diário de eventos. Esse processo é uma operação idempotente e só precisa ser chamado uma vez para cada cliente. Ele pode ser chamado novamente para recuperar o URL do diário.

| Parâmetro | Valor |
|--------------------------|------------------------------------------------------|
| Método | `POST` |
| Caminho | `/register` |
| Cabeçalho `Authorization` | Todos os [cabeçalhos relacionados à autorização](#authentication-and-authorization). |
| Cabeçalho `x-request-id` | Opcional, definido pelos clientes para um identificador completo exclusivo das solicitações de processamento em todos os sistemas. |
| Corpo da solicitação | Deve estar vazio. |

### Registrar resposta {#register-response}

| Parâmetro | Valor |
|-----------------------|------------------------------------------------------|
| Tipo MIME | `application/json` |
| Cabeçalho `X-Request-Id` | Igual ao cabeçalho da solicitação `X-Request-Id` ou gerado exclusivamente. Use para identificar solicitações entre sistemas, solicitações de suporte ou ambas. |
| Corpo da resposta | Um objeto JSON com campos `journal`, `ok` ou `requestId`. |

Os códigos de status HTTP são:

* **200 Êxito**: quando a solicitação é bem-sucedida. A URL `journal` recebe notificações sobre os resultados do processamento assíncrono iniciado por meio de `/process`. Ele alerta `rendition_created` eventos após a conclusão bem-sucedida ou `rendition_failed` eventos se o processo falhar.

  ```json
  {
      "ok": true,
      "journal": "https://api.adobe.io/events/organizations/xxxxx/integrations/xxxx/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "requestId": "1234567890"
  }
  ```

* **401 Não autorizado**: ocorre quando a solicitação não tem [autenticação](#authentication-and-authorization) válida. Um exemplo pode ser um token de acesso inválido ou uma chave de API inválida.

* **403 Proibido**: ocorre quando a solicitação não tem uma [autorização](#authentication-and-authorization) válida. Um exemplo pode ser um token de acesso válido, mas o projeto do Adobe Developer Console (conta técnica) não está inscrito em todos os serviços necessários.

* **429 Muitas solicitações**: ocorre quando este cliente ou sobrecarrega o sistema. Os clientes devem tentar novamente com um [retrocesso exponencial](https://en.wikipedia.org/wiki/Exponential_backoff). O corpo está vazio.
* **4xx erro**: quando houve qualquer outro erro de cliente e o registro falhou. Normalmente, uma resposta JSON como essa é retornada, embora isso não seja garantido para todos os erros:

  ```json
  {
      "ok": false,
      "requestId": "1234567890",
      "message": "error message"
  }
  ```

* **5xx erro**: ocorre quando ocorre qualquer outro erro no lado do servidor e o registro falha. Normalmente, uma resposta JSON como essa é retornada, embora isso não seja garantido para todos os erros:

  ```json
  {
      "ok": false,
      "requestId": "1234567890",
      "message": "error message"
  }
  ```

### Cancelar registro da solicitação {#unregister-request}

Esta chamada de API cancela o registro de um cliente [!DNL Asset Compute]. Após esse cancelamento de registro, não será mais possível chamar `/process`. O uso da chamada de API para um cliente não registrado ou para um cliente a ser registrado retorna um erro `404`.

| Parâmetro | Valor |
|--------------------------|------------------------------------------------------|
| Método | `POST` |
| Caminho | `/unregister` |
| Cabeçalho `Authorization` | Todos os [cabeçalhos relacionados à autorização](#authentication-and-authorization). |
| Cabeçalho `x-request-id` | Opcional. Os clientes podem configurá-lo para um identificador completo exclusivo das solicitações de processamento em todos os sistemas. |
| Corpo da solicitação | Vazio. |

### Cancelar registro da resposta {#unregister-response}

| Parâmetro | Valor |
|-----------------------|------------------------------------------------------|
| Tipo MIME | `application/json` |
| Cabeçalho `X-Request-Id` | Igual ao cabeçalho da solicitação `X-Request-Id` ou gerado exclusivamente. Use para identificar solicitações entre sistemas ou solicitações de suporte. |
| Corpo da resposta | Um objeto JSON com `ok` e `requestId` campos. |

Os códigos de status são:

* **200 Success**: ocorre quando o registro e o diário são encontrados e removidos.

  ```json
  {
      "ok": true,
      "requestId": "1234567890"
  }
  ```

* **401 Não autorizado**: ocorre quando a solicitação não tem [autenticação](#authentication-and-authorization) válida. Um exemplo pode ser um token de acesso inválido ou uma chave de API inválida.

* **403 Proibido**: ocorre quando a solicitação não tem uma [autorização](#authentication-and-authorization) válida. Um exemplo pode ser um token de acesso válido, mas o projeto do Adobe Developer Console (conta técnica) não está inscrito em todos os serviços necessários.

* **404 Não encontrado**: este status aparece quando as credenciais fornecidas têm registro cancelado ou são inválidas.

  ```json
  {
      "ok": true,
      "requestId": "1234567890"
  }
  ```

* **429 Muitas solicitações**: ocorre quando o sistema está sobrecarregado. Os clientes devem tentar novamente com um [retrocesso exponencial](https://en.wikipedia.org/wiki/Exponential_backoff). O corpo está vazio.

* **4xx erro**: ocorre quando ocorreu qualquer outro erro de cliente e o cancelamento de registro falhou. Normalmente, uma resposta JSON como essa é retornada, embora isso não seja garantido para todos os erros:

  ```json
  {
      "ok": false,
      "requestId": "1234567890",
      "message": "error message"
  }
  ```

* **5xx erro**: ocorre quando ocorre qualquer outro erro no lado do servidor e o registro falha. Normalmente, uma resposta JSON como essa é retornada, embora isso não seja garantido para todos os erros:

  ```json
  {
      "ok": false,
      "requestId": "1234567890",
      "message": "error message"
  }
  ```

## Processar solicitação {#process-request}

A operação `process` envia um trabalho que transforma um ativo de origem em várias representações, com base nas instruções na solicitação. Notificações sobre conclusão bem-sucedida (tipo de evento `rendition_created`) ou quaisquer erros (tipo de evento `rendition_failed`) são enviadas a um diário de Eventos que deve ser recuperado usando [`/register`](#register) uma vez antes de fazer qualquer número de `/process` solicitações. Solicitações formadas incorretamente falham imediatamente com um código de erro 400.

Os binários são referenciados usando URLs, como URLs pré-assinados do Amazon AWS S3 ou URLs SAS do Armazenamento Azure Blob. Usado para ler o ativo `source` (`GET` URLs) e gravar as representações (`PUT` URLs). O cliente é responsável por gerar esses URLs pré-assinados.

| Parâmetro | Valor |
|--------------------------|------------------------------------------------------|
| Método | `POST` |
| Caminho | `/process` |
| Tipo MIME | `application/json` |
| Cabeçalho `Authorization` | Todos os [cabeçalhos relacionados à autorização](#authentication-and-authorization). |
| Cabeçalho `x-request-id` | Opcional. Os clientes podem definir um identificador completo exclusivo para rastrear solicitações de processamento em todos os sistemas. |
| Corpo da solicitação | Deve estar no formato JSON da solicitação do processo, conforme descrito abaixo. Ele fornece instruções sobre qual ativo processar e quais representações gerar. |

### Processar solicitação JSON {#process-request-json}

O corpo da solicitação de `/process` é um objeto JSON com este esquema de alto nível:

```json
{
    "source": "",
    "renditions" : []
}
```

Os campos disponíveis são:

| Nome | Tipo | Descrição | Exemplo |
|--------------|----------|-------------|---------|
| `source` | `string` | URL do ativo de origem que é processado. Opcional, com base no formato de representação solicitado (por exemplo, `fmt=zip`). | `"http://example.com/image.jpg"` |
| `source` | `object` | Descrição do ativo de origem que é processado. Consulte a descrição dos [campos de objeto do Source](#source-object-fields) abaixo. Opcional com base no formato de representação solicitado (por exemplo, `fmt=zip`). | `{"url": "http://example.com/image.jpg", "mimeType": "image/jpeg" }` |
| `renditions` | `array` | Representações a serem geradas a partir do arquivo de origem. Cada objeto de representação oferece suporte a uma [instrução de representação](#rendition-instructions). Obrigatório. | `[{ "target": "https://....", "fmt": "png" }]` |

O `source` pode ser um `<string>` que é visto como uma URL ou pode ser um `<object>` com um campo adicional. As seguintes variantes são semelhantes:

```json
"source": "http://example.com/image.jpg"
```

```json
"source": {
    "url": "http://example.com/image.jpg"
}
```

### Campos de objeto do Source {#source-object-fields}

| Nome | Tipo | Descrição | Exemplo |
|-----------|----------|-------------|---------|
| `url` | `string` | URL do ativo de origem a ser processado. Obrigatório. | `"http://example.com/image.jpg"` |
| `name` | `string` | Nome do arquivo do ativo Source. Uma extensão de arquivo no nome pode ser usada se nenhum tipo MIME for detectado. Ele tem prioridade sobre o nome de arquivo especificado no caminho do URL. E tem prioridade sobre o nome do arquivo no cabeçalho `content-disposition` do recurso binário. O padrão é &quot;file&quot;. | `"image.jpg"` |
| `size` | `number` | Tamanho do arquivo de ativo do Source em bytes. Tem precedência sobre o cabeçalho `content-length` do recurso binário. | `10234` |
| `mimetype` | `string` | Tipo MIME do arquivo de ativo do Source. Tem prioridade sobre o cabeçalho `content-type` do recurso binário. | `"image/jpeg"` |

### Um exemplo completo de solicitação `process` {#complete-process-request-example}

```json
{
    "source": "https://www.adobe.com/content/dam/acom/en/lobby/lobby-bg-bts2017-logged-out-1440x860.jpg",
    "renditions" : [{
            "name": "image.48x48.png",
            "target": "https://some-presigned-put-url-for-image.48x48.png",
            "fmt": "png",
            "width": 48,
            "height": 48
        },{
            "name": "image.200x200.jpg",
            "target": "https://some-presigned-put-url-for-image.200x200.jpg",
            "fmt": "jpg",
            "width": 200,
            "height": 200
        },{
            "name": "cqdam.xmp.xml",
            "target": "https://some-presigned-put-url-for-cqdam.xmp.xml",
            "fmt": "xmp"
        },{
            "name": "cqdam.text.txt",
            "target": "https://some-presigned-put-url-for-cqdam.text.txt",
            "fmt": "text"
    }]
}
```

## Processar resposta {#process-response}

A solicitação `/process` retorna imediatamente com êxito ou falha com base na validação básica da solicitação. O processamento de ativos real ocorre de forma assíncrona.

| Parâmetro | Valor |
|-----------------------|------------------------------------------------------|
| Tipo MIME | `application/json` |
| Cabeçalho `X-Request-Id` | Igual ao cabeçalho da solicitação `X-Request-Id` ou gerado exclusivamente. Use para identificar solicitações entre sistemas ou solicitações de suporte. |
| Corpo da resposta | Um objeto JSON com `ok` e `requestId` campos. |

Códigos de status:

* **200 Êxito**: se a solicitação foi enviada com êxito. O JSON de resposta inclui `"ok": true`:

  ```json
  {
      "ok": true,
      "requestId": "1234567890"
  }
  ```

* **400 Solicitação inválida**: se a solicitação estiver estruturada incorretamente, por exemplo, se não tiver campos obrigatórios na carga JSON. O JSON de resposta inclui `"ok": false`:

  ```json
  {
      "ok": false,
      "requestId": "1234567890",
      "message": "error message"
  }
  ```

* **401 Não autorizado**: quando a solicitação não tem [autenticação](#authentication-and-authorization) válida. Um exemplo pode ser um token de acesso inválido ou uma chave de API inválida.
* **403 Proibido**: quando a solicitação não tem [autorização](#authentication-and-authorization) válida. Um exemplo pode ser um token de acesso válido, mas o projeto do Adobe Developer Console (conta técnica) não está inscrito em todos os serviços necessários.
* **429 Muitas solicitações**: ocorre quando o sistema está sobrecarregado, seja devido a este cliente específico ou devido à demanda geral. Os clientes podem tentar novamente com um [retrocesso exponencial](https://en.wikipedia.org/wiki/Exponential_backoff). O corpo está vazio.
* **4xx erro**: quando havia qualquer outro erro de cliente. Normalmente, uma resposta JSON como essa é retornada, embora isso não seja garantido para todos os erros:

  ```json
  {
      "ok": false,
      "requestId": "1234567890",
      "message": "error message"
  }
  ```

* **5xx erro**: quando havia qualquer outro erro do lado do servidor. Normalmente, uma resposta JSON como essa é retornada, embora isso não seja garantido para todos os erros:

  ```json
  {
      "ok": false,
      "requestId": "1234567890",
      "message": "error message"
  }
  ```

Provavelmente, a maioria dos clientes está inclinada a repetir a mesma solicitação com [retirada exponencial](https://en.wikipedia.org/wiki/Exponential_backoff) em qualquer erro *exceto*, problemas de configuração como 401 ou 403 ou solicitações inválidas como 400. Além da limitação de taxa regular por meio de respostas 429, uma interrupção ou limitação temporária de serviço pode resultar em erros 5xx. Em seguida, seria aconselhável tentar novamente após um período.

Todas as respostas JSON (se presentes) incluem o `requestId`, que é o mesmo valor que o cabeçalho `X-Request-Id`. O Adobe recomenda a leitura do cabeçalho, pois ele está sempre presente. O `requestId` também é retornado em todos os eventos relacionados às solicitações de processamento como `requestId`. Os clientes não devem fazer nenhuma suposição sobre o formato dessa cadeia de caracteres. É um identificador de sequência de caracteres opaco.

## Aceitar o pós-processamento {#opt-in-to-post-processing}

O [SDK do Asset Compute](https://github.com/adobe/asset-compute-sdk) dá suporte a um conjunto de opções básicas de pós-processamento de imagem. Os trabalhadores personalizados podem aceitar explicitamente o pós-processamento definindo o campo `postProcess` no objeto de representação como `true`.

Os casos de uso compatíveis são:

* Cortar é uma representação de um retângulo cujos limites por crop.w, crop.h, crop.x e crop.y estão definidos. Os detalhes de corte estão especificados no campo `instructions.crop` do objeto de representação.
* Redimensionar imagens usando largura, altura ou ambos. O `instructions.width` e `instructions.height` o definem no objeto de representação. Para redimensionar usando apenas largura ou altura, defina apenas um valor. O serviço de computação conserva a taxa de proporção.
* Defina a qualidade de uma imagem JPEG. O `instructions.quality` o define no objeto de representação. Um nível de qualidade de 100 representa a qualidade mais alta, enquanto números mais baixos significam uma diminuição na qualidade.
* Criar imagens entrelaçadas. O `instructions.interlace` o define no objeto de representação.
* Defina DPI para ajustar o tamanho renderizado para fins de publicação em desktop, ajustando a escala aplicada aos pixels. O `instructions.dpi` o define no objeto de representação para alterar a resolução de dpi. Entretanto, para redimensionar a imagem para que ela tenha o mesmo tamanho em uma resolução diferente, use as instruções de `convertToDpi`.
* Redimensionar a imagem de forma que sua largura ou altura renderizada permaneça a mesma que a original na resolução de destino especificada (DPI). O `instructions.convertToDpi` o define no objeto de representação.

## Inserir marca d&#39;água em ativos {#add-watermark}

O [SDK do Asset compute](https://github.com/adobe/asset-compute-sdk) oferece suporte à adição de uma marca d&#39;água aos arquivos de imagem PNG, JPEG, TIFF e GIF. A marca d&#39;água é adicionada seguindo as instruções de representação no objeto `watermark` na representação.

A marca d&#39;água é feita durante o pós-processamento da representação. Para adicionar ativos de marca d&#39;água, o trabalhador personalizado [opta pelo pós-processamento](#opt-in-to-post-processing) definindo o campo `postProcess` no objeto de representação como `true`. Se o trabalhador não aceitar, a marca d&#39;água não será aplicada, mesmo se o objeto de marca d&#39;água estiver definido no objeto de representação na solicitação.

## Instruções de representação {#rendition-instructions}

Estas são as opções disponíveis para a matriz `renditions` em [`/process`](#process-request).

### Campos comuns {#common-fields}

| Nome | Tipo | Descrição | Exemplo |
|-------------------|----------|-------------|---------|
| `fmt` | `string` | O formato de destino das representações também pode ser `text` para extração de texto e `xmp` para extração de metadados XMP como xml. Ver [formatos com suporte](https://experienceleague.adobe.com/pt-br/docs/experience-manager-cloud-service/content/assets/file-format-support) | `png` |
| `worker` | `string` | URL de um [aplicativo personalizado](develop-custom-application.md). Deve ser uma URL `https://`. Se esse campo estiver presente, um aplicativo personalizado criará a representação. Qualquer outro campo de representação definido é usado no aplicativo personalizado. | `"https://1234.adobeioruntime.net`<br>`/api/v1/web`<br>`/example-custom-worker-master/worker"` |
| `target` | `string` | O URL para o qual a representação gerada deve ser carregada usando o PUT HTTP. | `http://w.com/img.jpg` |
| `target` | `object` | Informações de upload de URL pré-assinado de várias partes para a representação gerada. Estas informações são para [AEM/Carregamento binário direto do Oak](https://jackrabbit.apache.org/oak/docs/features/direct-binary-access.html) com este [comportamento de carregamento multiparte](https://jackrabbit.apache.org/oak/docs/apidocs/org/apache/jackrabbit/api/binary/BinaryUpload.html).<br>Campos:<ul><li>`urls`: matriz de cadeias de caracteres, uma para cada URL de parte pré-assinada</li><li>`minPartSize`: o tamanho mínimo a ser usado para uma parte = url</li><li>`maxPartSize`: o tamanho máximo a ser usado para uma parte = url</li></ul> | `{ "urls": [ "https://part1...", "https://part2..." ], "minPartSize": 10000, "maxPartSize": 100000 }` |
| `userData` | `object` | Opcional. O cliente controla o espaço reservado e o transmite como está para eventos de representação. Permite que um cliente adicione informações personalizadas para identificar eventos de representação. Eles não devem ser modificados ou usados em aplicativos personalizados, pois os clientes podem alterá-los a qualquer momento. | `{ ... }` |

### Campos específicos da representação {#rendition-specific-fields}

Para obter uma lista de formatos de arquivo com suporte no momento, consulte [formatos de arquivo com suporte](https://experienceleague.adobe.com/pt-br/docs/experience-manager-cloud-service/content/assets/file-format-support).

| Nome | Tipo | Descrição | Exemplo |
|-------------------|----------|-------------|---------|
| `*` | `*` | Campos personalizados avançados podem ser adicionados compreendidos por um [aplicativo personalizado](develop-custom-application.md). | |
| `embedBinaryLimit` | `number` em bytes | Quando o tamanho do arquivo da representação é menor que o valor especificado, ele é incluído no evento enviado após a conclusão de sua criação. O tamanho máximo permitido para a incorporação é de 32 KB (32 x 1024 bytes). Se uma representação for maior que o limite `embedBinaryLimit`, ela será colocada em um local no armazenamento na nuvem e não será incorporada ao evento. | `3276` |
| `width` | `number` | Largura em pixels. somente para representações de imagem. | `200` |
| `height` | `number` | Altura em pixels. somente para representações de imagem. | `200` |
|                   |          | A proporção será sempre mantida se: <ul> <li> `width` e `height` estão especificados, então a imagem se ajusta ao tamanho, mantendo a proporção </li><li> Se apenas `width` ou `height` for especificado, a imagem resultante usará a dimensão correspondente, mantendo a taxa de proporção</li><li> Se `width` ou `height` não for especificado, o tamanho original do pixel da imagem será usado. Depende do tipo de origem. Para alguns formatos, como arquivos PDF, um tamanho padrão é usado. Pode haver um limite de tamanho máximo.</li></ul> | |
| `quality` | `number` | Especifique a qualidade do jpeg no intervalo de `1` a `100`. Aplicável somente para representações de imagem. | `90` |
| `xmp` | `string` | Usado apenas pelo writeback de metadados XMP, é codificado em base64 XMP para gravar de volta na representação especificada. | |
| `interlace` | `bool` | Crie um PNG entrelaçado ou GIF ou JPEG progressivo definindo-o como `true`. Não tem efeito em outros formatos de arquivo. | |
| `jpegSize` | `number` | Tamanho aproximado do arquivo JPEG em bytes. Ele substitui qualquer configuração `quality`. Não tem efeito em outros formatos. | |
| `dpi` | `number` ou `object` | Defina x e y DPI. Para simplificar, também pode ser definido como um único número, que é usado para x e y. Ela não tem efeito na própria imagem. | `96` ou `{ xdpi: 96, ydpi: 96 }` |
| `convertToDpi` | `number` ou `object` | x e y DPI reexemplificam os valores mantendo o tamanho físico. Para simplificar, também pode ser definido como um único número, que é usado para x e y. | `96` ou `{ xdpi: 96, ydpi: 96 }` |
| `files` | `array` | Lista de arquivos a serem incluídos no arquivo ZIP (`fmt=zip`). Cada entrada pode ser uma string de URL ou um objeto com os campos:<ul><li>`url`: URL para baixar arquivo</li><li>`path`: armazenar arquivo neste caminho no ZIP</li></ul> | `[{ "url": "https://host/asset.jpg", "path": "folder/location/asset.jpg" }]` |
| `duplicate` | `string` | Tratamento duplicado para arquivos ZIP (`fmt=zip`). Por padrão, vários arquivos armazenados no mesmo caminho no ZIP geram um erro. Configurar `duplicate` como `ignore` resulta somente no primeiro ativo a ser armazenado e no restante a ser ignorado. | `ignore` |
| `watermark` | `object` | Contém instruções sobre a [marca d&#39;água](#watermark-specific-fields). |  |

### Campos específicos de marca d&#39;água {#watermark-specific-fields}

O formato PNG é usado como marca d&#39;água.

| Nome | Tipo | Descrição | Exemplo |
|-------------------|----------|-------------|---------|
| `scale` | `number` | Escala da marca d&#39;água, entre `0.0` e `1.0`. `1.0` significa que a marca d&#39;água tem sua escala original (1:1) e os valores mais baixos reduzem o tamanho da marca d&#39;água. | Um valor de `0.5` significa metade do tamanho original. |
| `image` | `url` | URL do arquivo PNG a ser usado para marca d&#39;água. | |

## Eventos assíncronos {#asynchronous-events}

Quando o processamento de uma representação for concluído ou quando ocorrer um erro, um evento será enviado para um Adobe [!DNL `I/O Events Journal`]. Os clientes devem escutar a URL do diário fornecida por meio de [`/register`](#register). A resposta do diário inclui uma matriz `event` que consiste em um objeto para cada evento, do qual o campo `event` inclui a carga do evento real.

O tipo de Adobe [!DNL `I/O Events`] para todos os eventos de [!DNL Asset Compute Service] é `asset_compute`. O diário é inscrito automaticamente apenas neste tipo de evento e não há mais nenhum requisito para filtrar com base no tipo de Evento [!DNL Adobe Developer]. Os tipos de evento específicos do serviço estão disponíveis na propriedade `type` do evento.

### Tipos de evento {#event-types}

| Evento | Descrição |
|---------------------|-------------|
| `rendition_created` | Enviado para cada representação processada e carregada com sucesso. |
| `rendition_failed` | Enviado para cada representação que falhou no processamento ou upload. |

### Atributos do evento {#event-attributes}

| Atributo | Tipo | Evento | Descrição |
|-------------|----------|---------------|-------------|
| `date` | `string` | `*` | Carimbo de data/hora quando o evento foi enviado no formato estendido simplificado [ISO-8601](https://en.wikipedia.org/wiki/ISO_8601), conforme definido pelo JavaScript [Date.toISOString()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date/toISOString). |
| `requestId` | `string` | `*` | A ID da solicitação original para `/process`, igual ao cabeçalho `X-Request-Id`. |
| `source` | `object` | `*` | O `source` da solicitação `/process`. |
| `userData` | `object` | `*` | O `userData` da representação da solicitação `/process`, se definida. |
| `rendition` | `object` | `rendition_*` | O objeto de representação correspondente passado em `/process`. |
| `metadata` | `object` | `rendition_created` | As propriedades [metadata](#metadata) da representação. |
| `errorReason` | `string` | `rendition_failed` | Falha de representação [motivo](#error-reasons), se houver. |
| `errorMessage` | `string` | `rendition_failed` | O texto que fornece mais detalhes sobre a falha de representação, se houver. |

### Metadados {#metadata}

| Propriedade | Descrição |
|--------|-------------|
| `repo:size` | O tamanho da representação em bytes. |
| `repo:sha1` | O resumo sha1 da representação. |
| `dc:format` | O tipo MIME da representação. |
| `repo:encoding` | A codificação de conjunto de caracteres da representação, caso seja um formato baseado em texto. |
| `tiff:ImageWidth` | A largura da representação em pixels. Presente somente para representações de imagem. |
| `tiff:ImageLength` | O comprimento da representação em pixels. Presente somente para representações de imagem. |

### Motivos de erro {#error-reasons}

| Motivo | Descrição |
|---------|-------------|
| `RenditionFormatUnsupported` | O formato de representação solicitado não tem suporte para a origem fornecida. |
| `SourceUnsupported` | A origem específica não é compatível, mesmo que o tipo seja. |
| `SourceCorrupt` | Os dados de origem estão corrompidos. Inclui arquivos vazios. |
| `RenditionTooLarge` | Não foi possível carregar a representação usando as URLs pré-assinadas fornecidas em `target`. O tamanho de representação real está disponível como metadados em `repo:size` e é usado pelo cliente para processar novamente essa representação com o número correto de URLs pré-assinadas. |
| `GenericError` | Qualquer outro erro inesperado. |
