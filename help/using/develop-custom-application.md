---
title: Desenvolver para  [!DNL Asset Compute Service]
description: Criar aplicativos personalizados usando o  [!DNL Asset Compute Service].
exl-id: a0c59752-564b-4bb6-9833-ab7c58a7f38e
source-git-commit: 94fd8c0888185f64825046b7999655e9501a71fe
workflow-type: tm+mt
source-wordcount: '1489'
ht-degree: 0%

---

# Desenvolver um aplicativo personalizado {#develop}

Antes de começar a desenvolver um aplicativo personalizado:

* Verifique se todos os [pré-requisitos](/help/using/understand-extensibility.md#prerequisites-and-provisioning) foram atendidos.
* Instale as [ferramentas de software necessárias](/help/using/setup-environment.md#create-dev-environment).
* Consulte [Configurar o ambiente](setup-environment.md) para verificar se você está pronto para criar um aplicativo personalizado.

## Criar um aplicativo personalizado {#create-custom-application}

Verifique se o [Adobe aio-cli](https://github.com/adobe/aio-cli) está instalado localmente.

1. Para criar um aplicativo personalizado, [crie um projeto do App Builder](https://developer.adobe.com/app-builder/docs/getting_started/first_app/#4-bootstrapping-new-app-using-the-cli). Para fazer isso, execute o `aio app init <app-name>` no terminal.

   Se você ainda não tiver feito logon, este comando solicitará que um navegador entre no [Adobe Developer Console](https://developer.adobe.com/console/user/servicesandapis) com sua Adobe ID. Consulte [aqui](https://developer.adobe.com/app-builder/docs/getting_started/first_app/#3-signing-in-from-cli) para obter mais informações sobre como entrar pela cli.

   A Adobe recomenda que você faça logon primeiro. Se tiver problemas, siga as instruções [para criar um aplicativo sem fazer logon](https://developer.adobe.com/app-builder/docs/getting_started/first_app/#42-developer-is-not-logged-in-as-enterprise-organization-user).

1. Depois de fazer logon, siga os prompts na CLI e selecione os `Organization`, `Project` e `Workspace` a serem usados para o aplicativo. Escolha o projeto e o espaço de trabalho criados quando você [configurou o ambiente](setup-environment.md). Quando solicitado `Which extension point(s) do you wish to implement ?`, certifique-se de selecionar `DX Asset Compute Worker`:

   ```sh
   $ aio app init <app-name>
   Retrieving information from [!DNL Adobe I/O] Console.
   ? Select Org My Adobe Org
   ? Select Project MyAdobe Developer App BuilderProject
   ? Which extension point(s) do you wish to implement ? (Press <space> to select, <a>
   to toggle all, <i> to invert selection)
   ❯◯ DX Experience Cloud SPA
   ◯ DX Asset Compute Worker
   ```

1. Quando solicitado com `Which Adobe I/O App features do you want to enable for this project?`, selecione `Actions`. Desmarque a opção `Web Assets`, pois os ativos da Web usam diferentes verificações de autenticação e autorização.

   ```bash
   ? Which Adobe I/O App features do you want to enable for this project?
   select components to include (Press <space> to select, <a> to toggle all, <i> to invert selection)
   ❯◉ Actions: Deploy Runtime actions
   ◯ Events: Publish to Adobe I/O Events
   ◯ Web Assets: Deploy hosted static assets
   ◯ CI/CD: Include GitHub Actions based workflows for Build, Test and Deploy
   ```

1. Quando solicitado `Which type of sample actions do you want to create?`, certifique-se de selecionar `Adobe Asset Compute Worker`:

   ```bash
   ? Which type of sample actions do you want to create?
   Select type of actions to generate
   ❯◉ Adobe Asset Compute Worker
   ◯ Generic
   ```

1. Siga o restante dos prompts e abra o novo aplicativo no Visual Studio Code (ou seu editor de código favorito). Ele contém o andaime e o código de amostra de um aplicativo personalizado.

   Leia aqui sobre os [principais componentes de um aplicativo App Builder](https://developer.adobe.com/app-builder/docs/getting_started/first_app/#5-anatomy-of-an-app-builder-application).

   O aplicativo modelo usa o [Asset Compute SDK](https://github.com/adobe/asset-compute-sdk#asset-compute-sdk) da Adobe para fazer upload, download e orquestração de representações de aplicativos, de modo que os desenvolvedores só precisam implementar a lógica de aplicativo personalizado. Na pasta `actions/<worker-name>`, o arquivo `index.js` é onde o código de aplicativo personalizado deve ser adicionado.

Consulte [exemplos de aplicativos personalizados](#try-sample) para obter exemplos e ideias de aplicativos personalizados.

### Adicionar credenciais {#add-credentials}

Conforme você faz logon ao criar o aplicativo, a maioria das credenciais do App Builder é coletada no arquivo ENV. No entanto, o uso da ferramenta de desenvolvedor requer credenciais adicionais.

<!-- TBD: Check if manual setup of credentials is required.
Manual set up of credentials is removed from troubleshooting and best practices page. Link was broken.
If you did not log in, refer to our troubleshooting guide to [set up credentials manually](troubleshooting.md).
-->

#### Credenciais de armazenamento da ferramenta do desenvolvedor {#developer-tool-credentials}

A ferramenta para desenvolvedores avaliarem aplicativos personalizados usando o [!DNL Asset Compute service] requer o uso de um contêiner de armazenamento na nuvem. Esse container é essencial para armazenar arquivos de teste e para o recebimento e a apresentação de representações produzidas pelos aplicativos.

>[!NOTE]
>
>Este contêiner é separado do armazenamento na nuvem de [!DNL Adobe Experience Manager] como [!DNL Cloud Service]. Ela só se aplica ao desenvolvimento e teste com a ferramenta de desenvolvedor do Asset Compute.

Verifique se você tem acesso a um [contêiner de armazenamento na nuvem com suporte](https://github.com/adobe/asset-compute-devtool#prerequisites). Esse contêiner é usado coletivamente por vários desenvolvedores para diferentes projetos sempre que necessário.

#### Adicionar credenciais ao arquivo ENV {#add-credentials-env-file}

Insira as credenciais subsequentes da ferramenta de desenvolvimento no arquivo `.env`. O arquivo está localizado na raiz do projeto do App Builder:
<!--
1. Add the absolute path to the private key file created while adding services to your App Builder Project:

    ```conf
    ASSET_COMPUTE_PRIVATE_KEY_FILE_PATH=
    ```

   >[!NOTE]
   >
   >JWT is deprecated and Private Key is not available for download. While we are working on updating the testing tools, note that custom workers created using OAuth can be deployed but devtools would not work.
-->
1. Baixe o arquivo da Adobe Developer Console. Vá para a raiz do projeto e clique em &quot;Baixar tudo&quot; no canto superior direito. O arquivo é baixado com `<namespace>-<workspace>.json` como o nome do arquivo. Siga uma das seguintes opções:

   * Renomeie o arquivo como `console.json` e mova-o para a raiz do seu projeto.
   * Como opção, adicione o caminho absoluto ao arquivo JSON de integração do Adobe Developer Console. Este arquivo é o mesmo arquivo [`console.json`](https://developer.adobe.com/app-builder/docs/getting_started/first_app/#42-developer-is-not-logged-in-as-enterprise-organization-user) que foi baixado no espaço de trabalho do projeto.

     ```conf
     ASSET_COMPUTE_INTEGRATION_FILE_PATH=
     ```

1. Adicione credenciais de armazenamento do S3 ou do Azure. Você só precisa de acesso a uma solução de armazenamento na nuvem.

   ```conf
   # S3 credentials
   S3_BUCKET=
   AWS_ACCESS_KEY_ID=
   AWS_SECRET_ACCESS_KEY=
   AWS_REGION=
   
   # Azure Storage credentials
   AZURE_STORAGE_ACCOUNT=
   AZURE_STORAGE_KEY=
   AZURE_STORAGE_CONTAINER_NAME=
   ```

>[!TIP]
>
>O arquivo `config.json` contém credenciais. A partir do projeto, adicione o arquivo JSON ao arquivo `.gitignore` para impedir seu compartilhamento. O mesmo se aplica aos arquivos `.env` e `.aio`.

## Executar o aplicativo {#run-custom-application}

Antes de executar o aplicativo com a ferramenta de desenvolvedor do Asset Compute, configure corretamente as [credenciais](#developer-tool-credentials).

Para executar o aplicativo na ferramenta de desenvolvedor, use o comando `aio app run`. Ele implanta a ação no Adobe [!DNL I/O Runtime] e inicia a ferramenta de desenvolvimento no computador local. Essa ferramenta é usada para testar solicitações de aplicativos durante o desenvolvimento. Este é um exemplo de solicitação de representação:

```json
"renditions": [
    {
        "worker": "https://1234_my_namespace.adobeioruntime.net/api/v1/web/example-custom-worker-master/worker",
        "name": "image.jpg"
    }
]
```

>[!NOTE]
>
>Não use o sinalizador `--local` com o comando `run`. Ele não funciona com aplicativos personalizados [!DNL Asset Compute] e com a ferramenta de desenvolvedor do Asset Compute. Os aplicativos personalizados são chamados pelo serviço [!DNL Asset Compute], que não pode acessar ações em execução nos computadores locais do desenvolvedor.

Veja [aqui](test-custom-application.md) como testar e depurar seu aplicativo. Quando terminar de desenvolver o aplicativo personalizado, [implante o aplicativo personalizado](deploy-custom-application.md).

## Experimente o aplicativo de amostra fornecido pelo Adobe {#try-sample}

Veja a seguir exemplos de aplicativos personalizados:

* [worker-basic](https://github.com/adobe/asset-compute-example-workers/tree/master/projects/worker-basic)
* [imagens-de-animais-de-trabalho](https://github.com/adobe/asset-compute-example-workers/tree/master/projects/worker-animal-pictures)

### Aplicativo personalizado de modelo {#template-custom-application}

O [worker-basic](https://github.com/adobe/asset-compute-example-workers/tree/master/projects/worker-basic) é um aplicativo modelo. Ele gera uma representação simplesmente copiando o arquivo de origem. O conteúdo deste aplicativo é o modelo recebido ao escolher `Adobe Asset Compute` na criação do aplicativo aio.

O arquivo de aplicativo, [`worker-basic.js`](https://github.com/adobe/asset-compute-example-workers/blob/master/projects/worker-basic/worker-basic.js) usa [`asset-compute-sdk`](https://github.com/adobe/asset-compute-sdk#overview) para baixar o arquivo de origem, orquestrar cada processamento de representação e carregar as representações resultantes de volta para o armazenamento na nuvem.

O [`renditionCallback`](https://github.com/adobe/asset-compute-sdk#rendition-callback-for-worker-required) definido dentro do código do aplicativo é onde executar toda a lógica de processamento do aplicativo. O retorno de chamada de representação em `worker-basic` simplesmente copia o conteúdo do arquivo de origem para o arquivo de representação.

```javascript
const { worker } = require('@adobe/asset-compute-sdk');
const fs = require('fs').promises;

exports.main = worker(async (source, rendition) => {
    // copy source to rendition to transfer 1:1
    await fs.copyFile(source.path, rendition.path);
});
```

## Chamar uma API externa {#call-external-api}

No código do aplicativo, é possível fazer chamadas de API externas para ajudar no processamento do aplicativo. Um exemplo de arquivo de aplicativo que chama uma API externa está abaixo.

```javascript
exports.main = worker(async function (source, rendition) {

    const response = await fetch('https://adobe.com', {
        method: 'GET',
        Authorization: params.AUTH_KEY
    })
});
```

Por exemplo, o [`worker-animal-pictures`](https://github.com/adobe/asset-compute-example-workers/blob/master/projects/worker-animal-pictures/worker-animal-pictures.js#L46) faz uma solicitação de busca para uma URL estática da Wikimedia usando a biblioteca [`node-httptransfer`](https://github.com/adobe/node-httptransfer#node-httptransfer).

<!-- TBD: Revisit later to see if this note is required.
>[!NOTE]
>
>For extra authorization for these API calls, see [custom authorization checks](#custom-authorization-checks).
-->

### Envio de parâmetros personalizados {#pass-custom-parameters}

Você pode passar parâmetros definidos pelo cliente pelos objetos de representação. Eles podem ser referenciados dentro do aplicativo em [`rendition` instruções](https://github.com/adobe/asset-compute-sdk#rendition). Um exemplo de um objeto de representação é:

```json
"renditions": [
    {
        "worker": "https://1234_my_namespace.adobeioruntime.net/api/v1/web/example-custom-worker-master/worker",
        "name": "image.jpg",
        "my-custom-parameter": "my-custom-parameter-value"
    }
]
```

Este é um exemplo de um arquivo de aplicativo que acessa um parâmetro personalizado:

```javascript
exports.main = worker(async function (source, rendition) {

    const customParam = rendition.instructions['my-custom-parameter'];
    console.log('Custom paramter:', customParam);
    // should print out `Custom parameter: "my-custom-parameter-value"`
});
```

O `example-worker-animal-pictures` passa um parâmetro personalizado [`animal`](https://github.com/adobe/asset-compute-example-workers/blob/master/projects/worker-animal-pictures/worker-animal-pictures.js#L39) para determinar qual arquivo buscar na Wikimedia.

## Suporte para autenticação e autorização {#authentication-authorization-support}

Por padrão, os aplicativos personalizados do Asset Compute vêm com verificações de Autorização e Autenticação para o projeto do App Builder. Habilitado ao configurar a anotação `require-adobe-auth` como `true` em `manifest.yml`.

### Acessar outras APIs do Adobe {#access-adobe-apis}

<!-- TBD: Revisit this section. Where do we document console workspace creation?
-->

Adicione os serviços de API ao espaço de trabalho do Console [!DNL Asset Compute] criado na configuração. Esses serviços fazem parte do token de acesso JWT gerado por [!DNL Asset Compute Service]. O token e outras credenciais podem ser acessados dentro do objeto de ação de aplicativo `params`.

```javascript
const accessToken = params.auth.accessToken; // JWT token for Technical Account with entitlements from the console workspace to the API service
const clientId = params.auth.clientId; // Technical Account client Id
const orgId = params.auth.orgId; // Experience Cloud Organization
```

### Transmita credenciais para sistemas de terceiros {#pass-credentials-for-tp}

Para manipular credenciais para outros serviços externos, passe-as como parâmetros padrão nas ações. Eles são criptografados automaticamente em trânsito. Para obter mais informações, consulte [criando ações no guia do desenvolvedor do Adobe I/O Runtime](https://developer.adobe.com/runtime/docs/guides/using/creating_actions/). Em seguida, defina-as usando variáveis de ambiente durante a implantação. Esses parâmetros podem ser acessados no objeto `params` dentro da ação.

Definir os parâmetros padrão dentro de `inputs` em `manifest.yml`:

```yaml
packages:
  __APP_PACKAGE__:
    actions:
      worker:
        function: 'index.js'
        runtime: 'nodejs:10'
        web: true
        inputs:
           secretKey: $SECRET_KEY
        annotations:
          require-adobe-auth: true
```

A expressão `$VAR` lê o valor de uma variável de ambiente chamada `VAR`.

Durante o desenvolvimento, você pode atribuir o valor no arquivo local `.env`. O motivo é porque `aio` importa automaticamente variáveis de ambiente de `.env` arquivos, juntamente com as variáveis definidas pelo shell iniciador. Neste exemplo, o arquivo `.env` tem a seguinte aparência:

```CONF
#...
SECRET_KEY=secret-value
```

Para implantação em produção, é possível definir as variáveis de ambiente no sistema CI, por exemplo, usando segredos em Ações do GitHub. Por fim, acesse os parâmetros padrão dentro do aplicativo da seguinte maneira:

```javascript
const key = params.secretKey;
```

## Dimensionamento de aplicativos {#sizing-workers}

Um aplicativo é executado em um contêiner no Adobe [!DNL I/O Runtime] com [limites](https://developer.adobe.com/runtime/docs/guides/using/system_settings/) que podem ser configurados por meio do `manifest.yml`:

```yaml
    actions:
      myworker:
        function: /actions/myworker/index.js
        limits:
          timeout: 300000
          memorySize: 512
          concurrency: 1
```

Devido ao processamento extensivo feito pelos aplicativos do Asset Compute, você deve ajustar esses limites para obter o desempenho ideal (grande o suficiente para lidar com ativos binários) e eficiência (não desperdiçar recursos devido à memória do contêiner não usada).

O tempo limite padrão para ações em Tempo de Execução é de um minuto, mas pode ser aumentado definindo o limite de `timeout` (em milissegundos). Se você espera processar arquivos maiores, aumente esse tempo. Considere o tempo total necessário para baixar a origem, processar o arquivo e fazer upload da representação. Se uma ação atingir o tempo limite, ou seja, não retornar a ativação antes do tempo limite especificado, o Tempo de execução descartará o contêiner e não o reutilizará.

Por natureza, os aplicativos Asset Compute tendem a ser vinculados à rede e à entrada ou saída de disco. O arquivo de origem deve ser baixado primeiro. O processamento geralmente consome muitos recursos e as representações resultantes são carregadas novamente.

Você pode especificar a memória alocada para um contêiner de ação em megabytes usando o parâmetro `memorySize`. Atualmente, esse parâmetro também define quanto acesso ao CPU o contêiner recebe e, mais importante, é um elemento essencial do custo de usar o Tempo de execução (contêineres maiores custam mais). Use um valor maior aqui quando o processamento exigir mais memória ou CPU, mas tenha cuidado para não desperdiçar recursos, pois quanto maior for o contêiner, menor será a taxa de transferência geral.

Além disso, é possível controlar a simultaneidade da ação em um contêiner usando a configuração `concurrency`. Essa configuração é o número de ativações simultâneas que um único contêiner (da mesma ação) obtém. Nesse modelo, o container de ação é como um servidor Node.js que recebe várias solicitações simultâneas, até esse limite. O `memorySize` padrão no tempo de execução está definido como 200 MB, ideal para ações App Builder menores. Para aplicativos do Asset Compute, esse padrão pode ser excessivo devido ao processamento local mais intenso e ao uso de disco. Alguns aplicativos, dependendo de sua implementação, também podem não funcionar bem com a atividade simultânea. O Asset Compute SDK garante que as ativações sejam separadas gravando arquivos em pastas exclusivas diferentes.

Teste aplicativos para encontrar os números ideais para `concurrency` e `memorySize`. Contêineres maiores = limite de memória mais alto pode permitir mais simultaneidade, mas também pode ser um desperdício para tráfego mais baixo.
