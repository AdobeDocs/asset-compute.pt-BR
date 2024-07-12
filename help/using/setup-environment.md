---
title: Definir o ambiente de desenvolvimento necessário para  [!DNL Asset Compute Service]
description: Configuração do ambiente de desenvolvedor para  [!DNL Asset Compute Service]  para começar a criar e testar o código personalizado.
exl-id: 91c12889-01d8-4757-9bdd-f73c491cd9d5
source-git-commit: c6f747ebd6d1b17834f1af0837609a148804f8a9
workflow-type: tm+mt
source-wordcount: '324'
ht-degree: 1%

---

# Configurar um ambiente de desenvolvedor {#create-dev-environment}

Para criar uma configuração que permita o desenvolvimento do [!DNL Asset Compute Service], siga estes requisitos e instruções.

1. [Adquirir acesso e credenciais](https://developer.adobe.com/app-builder/docs/getting_started/#acquire-access-and-credentials) para [!DNL Adobe Developer App Builder].

1. [Configurar o ambiente local](https://developer.adobe.com/app-builder/docs/getting_started/#local-environment-set-up) e as ferramentas necessárias.

1. Outras ferramentas que ajudam você a começar a desenvolver sem problemas são:

   * [Git](https://git-scm.com/)
   * [Área de Trabalho do Docker](https://www.docker.com/get-started)
   * [NodeJS](https://nodejs.org) (v14 LTS, versões ímpares não são recomendadas) e [NPM](https://www.npmjs.com). O usuário do OS X HomeBrew pode fazer `brew install node` para instalar ambos. Caso contrário, baixe-o da [página de download do NodeJS](https://nodejs.org/en/)
   * Um IDE que é bom para NodeJS, o Adobe recomenda [Visual Studio Code (VS Code)](https://code.visualstudio.com), pois é o IDE suportado para o depurador. Você pode usar qualquer outro IDE como um editor de código, mas o uso avançado (por exemplo, depurador) ainda não é suportado
   * Instalar o Adobe mais recente [[!DNL aio-cli]](https://github.com/adobe/aio-cli) (`aio`)
   <!-- - install using `npm install -g @adobe/aio-cli@7.1.0` -->

1. Certifique-se de atender aos [pré-requisitos](/help/using/understand-extensibility.md#prerequisites-and-provisioning)

<!--
>[!NOTE]
>
>For now, use [!DNL Adobe I/O] CLI v7.1.0 of and do not use [!DNL Adobe I/O] CLI v8.
-->

## Configurar um projeto do App Builder {#create-App-Builder-project}

1. Verifique se há uma função de administrador do sistema ou desenvolvedor na organização [!DNL Experience Cloud]. Um administrador do sistema, no [Admin Console](https://adminconsole.adobe.com/overview), configura esta função.

1. Faça logon no [Adobe Developer Console](https://developer.adobe.com/console/user/servicesandapis). Certifique-se de fazer parte da mesma organização [!DNL Experience Cloud] que o [!DNL Experience Manager] como uma integração [!DNL Cloud Service]. Para obter mais informações sobre o Adobe Developer Console, vá para [Documentação do console](https://developer.adobe.com/developer-console/docs/guides/).

1. [Criar um projeto do App Builder](https://developer.adobe.com/app-builder/docs/getting_started/first_app/). Clique em **[!UICONTROL Criar novo projeto]** > **[!UICONTROL Projeto a partir do modelo]**. Selecione App Builder. Ele cria um novo Projeto do App Builder com dois espaços de trabalho: `Production` e `Stage`. Adicione mais espaços de trabalho, por exemplo `Development`, conforme necessário.

1. No Projeto do App Builder, selecione um espaço de trabalho e assine os serviços necessários para o Asset Compute. Clique em **Adicionar ao Projeto** > **API** e adicione os serviços `Asset Compute`, `IO Events` e `IO Events Management`. Ao adicionar a primeira API, ele solicita a criação de uma chave privada. Salve essas informações em sua máquina, pois você precisa dessa chave para testar seu aplicativo personalizado com a ferramenta de desenvolvedor.

## Próxima etapa {#next-step}

Com o ambiente configurado, você está pronto para [criar um aplicativo personalizado](develop-custom-application.md).

<!-- More ideas:
 
* Any steps in the beginning that lead to gotchas later should be called out for caution? For example,
  * don't change some defaults initially
  * know risks when deviating from standard path
  * naming conventions to follow
  * Retrieve and format credentials (YAML file details)

TBD: When aio-cli v8 bugs are resolved, update the AIO CLI install command to remove v7.x reference and instruct users to use the latest version. See CQDOC-18346.

-->
