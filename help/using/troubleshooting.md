---
title: Solução de problemas [!DNL Asset Compute Service]
description: Solucione problemas e depure aplicativos personalizados usando o [!DNL Asset Compute Service].
exl-id: 017fff91-e5e9-4a30-babf-5faa1ebefc2f
source-git-commit: 63f83ff33ac6cd090fac4f6db18000155f464643
workflow-type: tm+mt
source-wordcount: '273'
ht-degree: 0%

---

# Solução de problemas {#troubleshoot}

Algumas dicas de solução de problemas genéricas que podem ajudar você a solucionar problemas com o Asset Compute Service são:

* Certifique-se de que o aplicativo JavaScript não falhe na inicialização. Essas falhas geralmente estão relacionadas a uma biblioteca ausente ou a uma dependência.
* Verifique se todas as dependências a serem instaladas estão referenciadas no arquivo `package.json` do aplicativo.
* Verifique se os erros que possam vir da limpeza na falha não geram seus próprios erros que ocultam o problema original.

* Ao iniciar a ferramenta de desenvolvedor pela primeira vez com uma nova integração do [!DNL Asset Compute Service], a primeira solicitação de processamento poderá falhar se o Diário de eventos do Asset Compute não estiver totalmente configurado. Aguarde algum tempo para que o diário seja configurado antes de enviar outra solicitação.
* Verifique se todas as APIs necessárias - Asset Compute, Adobe [!DNL I/O Events], Gerenciamento de Eventos e Tempo de Execução - estão incluídas no Adobe [!DNL `I/O Project`] e Workspace para evitar erros de solicitação `/register` ou `/process`.

## Problemas de logon por meio do Adobe [!DNL aio-cli] {#login-via-aio-cli}

Se tiver problemas para fazer logon no [!DNL Adobe Developer Console] [por meio do Adobe [!DNL aio-cli]](https://developer.adobe.com/app-builder/docs/get_started/app_builder_get_started/first-app#3-signing-in-from-cli), adicione manualmente as credenciais necessárias para desenvolver, testar e implantar seu aplicativo personalizado:

1. Navegue até o projeto e o espaço de trabalho do Adobe Developer App Builder no [Adobe Developer Console](https://developer.adobe.com/console/user/servicesandapis) e pressione **[!UICONTROL Baixar]** no canto superior direito. Abra este arquivo e salve este JSON em um local seguro em sua máquina.

1. Navegue até o arquivo ENV no aplicativo Adobe Developer App Builder.

1. Adicione as credenciais [!DNL I/O Runtime] do Adobe. Obtenha as credenciais do Adobe [!DNL I/O Runtime] do JSON baixado. As credenciais estão em `project.workspace.services.runtime`. Adicionar as credenciais de Tempo de Execução [!DNL Adobe I/O] nas variáveis `AIO_runtime_XXX`:

   ```json
   AIO_runtime_auth=
   AIO_runtime_namespace=
   ```

1. Adicione o caminho absoluto ao JSON baixado na Etapa 1:

   ```json
       ASSET_COMPUTE_INTEGRATION_FILE_PATH=
   ```

1. Configure o restante das [credenciais necessárias](develop-custom-application.md) para a ferramenta de desenvolvedor.

<!-- TBD for later:
Add any best practices for developers in this section:
* Any items to take care of when creating projects.
* Any naming conventions, reserved keywords, etc.?
* Any terms that can become a source of confusion later based on our OOTB naming.

* If required, add limitations for custom applications and spin those off as best practices.
* Do NOT borrow any content from https://git.corp.adobe.com/nui/nui/blob/master/doc/worker_api.md. It is outdated and irrelevant for 3rd party custom applications.
-->
