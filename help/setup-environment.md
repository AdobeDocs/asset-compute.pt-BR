---
title: Definir o ambiente de desenvolvimento necessário para [!DNL Asset Compute Service]
description: Configuração do ambiente do desenvolvedor para [!DNL Asset Compute Service] para começar a criar e testar o código personalizado.
exl-id: 91c12889-01d8-4757-9bdd-f73c491cd9d5
source-git-commit: 2dde177933477dc9ac2ff5a55af1fd2366e18359
workflow-type: tm+mt
source-wordcount: '357'
ht-degree: 3%

---

# Configurar um ambiente de desenvolvedor {#create-dev-environment}

Para criar uma configuração que permita desenvolver para [!DNL Asset Compute Service], siga estes requisitos e instruções.

1. [Adquirir acesso e credenciais](https://developer.adobe.com/app-builder/docs/getting_started/#acquire-access-and-credentials) para [!DNL Adobe Developer App Builder].

1. [Configurar o ambiente local](https://developer.adobe.com/app-builder/docs/getting_started/#local-environment-set-up) e as ferramentas necessárias.

1. Outras ferramentas que ajudam você a começar a desenvolver sem problemas são:

   * [Git](https://git-scm.com/)
   * [Desktop Docker](https://www.docker.com/get-started)
   * [NodeJS](https://nodejs.org) (v14 LTS, versões ímpares não são recomendadas) e [NPM](https://www.npmjs.com). O usuário do OSX HomeBrew pode fazer `brew install node` para instalar ambos. Caso contrário, baixe-o do [Página de download do NodeJS](https://nodejs.org/en/)
   * Um IDE que seja bom para o NodeJS, recomendamos [Visual Studio Code (Código VS)](https://code.visualstudio.com) como é o IDE compatível com o depurador. Você pode usar qualquer outro IDE como um editor de código, mas o uso avançado (por exemplo, depurador) ainda não é suportado
   * Instalar o mais recente[[!DNL Adobe I/O] CLI](https://github.com/adobe/aio-cli) (`aio`)

   <!-- - install using `npm install -g @adobe/aio-cli@7.1.0` -->

1. Certifique-se de atender aos [pré-requisitos](/help/understand-extensibility.md#prerequisites-and-provisioning)

<!--
>[!NOTE]
>
>For now, use [!DNL Adobe I/O] CLI v7.1.0 of and do not use [!DNL Adobe I/O] CLI v8.
-->

## Configurar um projeto do App Builder {#create-App-Builder-project}

1. Verifique a função de administrador do sistema ou desenvolvedor no [!DNL Experience Cloud] organização. Isso é configurado por um administrador do sistema na [Admin Console](https://adminconsole.adobe.com/overview).

1. Faça logon na [Console do Adobe Developer](https://console.adobe.io/). Certifique-se de fazer parte da mesma [!DNL Experience Cloud] organização como a [!DNL Experience Manager] as a [!DNL Cloud Service] integração. Para obter mais informações sobre o Console do Adobe Developer, consulte [Documentação do console](https://www.adobe.io/apis/experienceplatform/console/docs.html).

1. [Criar um projeto do App Builder](https://developer.adobe.com/app-builder/docs/getting_started/first_app/). Clique em **[!UICONTROL Criar novo projeto]** > **[!UICONTROL Projeto do modelo]**. Selecione Construtor de aplicativos. Ele cria um novo projeto do App Builder com dois espaços de trabalho: `Production` e `Stage`. Adicionar mais espaços de trabalho, por exemplo `Development`, conforme necessário.

1. No Projeto do App Builder, selecione um espaço de trabalho e assine os serviços necessários para o Asset compute. Clique em **Adicionar ao projeto** > **API** e adicionar `Asset Compute`, `IO Events`, e `IO Events Management` serviços. Ao adicionar a primeira API, ele solicita a criação de uma chave privada. Salve essas informações em sua máquina, pois você precisa dessa chave para testar seu aplicativo personalizado com a ferramenta de desenvolvedor.

## Próxima etapa {#next-step}

Agora que seu ambiente está configurado, você está pronto para [criar um aplicativo personalizado](develop-custom-application.md).

<!-- More ideas:
 
* Any steps in the beginning that lead to gotchas later should be called out for caution? For example,
  * don't change some defaults initially
  * know risks when deviating from standard path
  * naming conventions to follow
  * Retrieve and format credentials (YAML file details)

TBD: When aio-cli v8 bugs are resolved, update the AIO CLI install command to remove v7.x reference and instruct users to use the latest version. See CQDOC-18346.

-->
