---
title: Arquitetura de [!DNL Asset Compute Service]
description: Como  [!DNL Asset Compute Service] API, aplicativos e SDK trabalham juntos para fornecer um serviço de processamento de ativos nativo em nuvem.
exl-id: 658ee4b7-5eb1-4109-b263-1b7d705e49d6
source-git-commit: f15b9819d3319d22deccdf7e39c0f72728baaa39
workflow-type: tm+mt
source-wordcount: '478'
ht-degree: 0%

---

# Arquitetura de [!DNL Asset Compute Service] {#overview}

O [!DNL Asset Compute Service] é construído sobre a plataforma Adobe [!DNL `I/O Runtime`] sem servidor. Ele oferece suporte aos serviços de conteúdo da Adobe Sensei para ativos. O cliente que invoca (somente [!DNL Experience Manager] como [!DNL Cloud Service] tem suporte) é fornecido com as informações geradas pela Adobe Sensei que ele buscou para o ativo. As informações retornadas estão no formato JSON.

[!DNL Asset Compute Service] pode ser estendido criando aplicativos personalizados com base em [!DNL Adobe Developer App Builder]. Esses aplicativos personalizados são [!DNL Project Adobe Developer App Builder] aplicativos headless e executam tarefas como adicionar ferramentas de conversão personalizadas ou chamar APIs externas para executar operações de imagem.

[!DNL Project Adobe Developer App Builder] é uma estrutura para compilar e implantar aplicativos Web personalizados no Adobe [!DNL `I/O Runtime`]. Para criar aplicativos personalizados, os desenvolvedores podem aproveitar o [!DNL React Spectrum] (kit de ferramentas da interface do usuário do Adobe), criar microsserviços, criar eventos personalizados e orquestrar APIs. Consulte [documentação do Adobe Developer App Builder](https://developer.adobe.com/app-builder/docs/overview).

A base na qual a arquitetura se baseia inclui:

* A modularidade dos aplicativos, contendo apenas o que é necessário para uma determinada tarefa, permite dissociar os aplicativos uns dos outros e mantê-los mais leves.

* O conceito sem servidor do [!DNL Adobe I/O] Runtime oferece vários benefícios: processamento assíncrono, altamente escalável, isolado e baseado em trabalho, que é perfeito para o processamento de ativos.

* O armazenamento binário em nuvem fornece os recursos necessários para armazenar e acessar arquivos de ativos e representações individualmente, sem exigir permissões de acesso total ao armazenamento, usando referências de URL pré-assinadas. A aceleração de transferência, o armazenamento em cache de CDN e a co-localização de aplicativos de computação com o armazenamento em nuvem permitem o acesso ideal ao conteúdo de baixa latência. As nuvens do AWS e do Azure são compatíveis.

![Arquitetura do Serviço de Asset compute](assets/architecture-diagram.png)

*Figura: arquitetura do [!DNL Asset Compute Service] e como ele se integra ao [!DNL Experience Manager], armazenamento e aplicativo de processamento.*

A arquitetura consiste nas seguintes partes:

* **Uma camada de API e orquestração** recebe solicitações (no formato JSON) que instruem o serviço a transformar um ativo de origem em várias representações. As solicitações são assíncronas e retornam com uma ID de ativação que é a ID do trabalho. As instruções são puramente declarativas e, para todo o trabalho de processamento padrão (por exemplo, geração de miniaturas, extração de texto), os consumidores especificam apenas o resultado desejado, mas não os aplicativos que lidam com determinadas representações. Os recursos genéricos da API, como autenticação, análise, limitação de taxa, são tratados usando o Gateway da API de Adobe na frente do serviço e gerenciam todas as solicitações que vão para o Tempo de Execução [!DNL Adobe I/O]. O roteamento de aplicativos é feito dinamicamente pela camada de orquestração. Os clientes definem aplicativos personalizados para representações específicas, que vêm com seu próprio conjunto de parâmetros exclusivos. A execução do aplicativo pode ser totalmente paralelizada, pois são funções sem servidor separadas no Adobe [!DNL `I/O Runtime`].

* **Aplicativos para processar ativos** especializados em determinados tipos de formatos de arquivo ou representações de destino. Conceitualmente, um aplicativo é como o conceito pipe UNIX®: um arquivo de entrada é transformado em um ou mais arquivos de saída.

* **Uma [biblioteca de aplicativos comum](https://github.com/adobe/asset-compute-sdk)** manipula tarefas comuns. Por exemplo, download do arquivo de origem, upload das representações, relatórios de erros, envio de eventos e monitoramento. Esse design garante que o desenvolvimento de aplicativos permaneça simples, seguindo o conceito de &quot;sem servidor&quot;, com interações limitadas ao sistema de arquivos local.

<!-- TBD:

* About the YAML file?
* minimize description to custom applications
* remove all internal stuff (e.g. Photoshop application, API Gateway) from text and diagram
* update diagram to focus on 3rd party custom applications ONLY
* Explain important transactions/handshakes?
* Flow of assets/control? See the illustration on the Nui diagrams wiki.
* Illustrations. See the SVG shared by Alex.
* Exceptions? Limitations? Call-outs? Gotchas?
* Do we want to add what basic processing is not available currently, that is expected by existing AEM customers?
-->
