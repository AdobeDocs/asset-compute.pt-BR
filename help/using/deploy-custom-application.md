---
title: Implantar [!DNL Asset Compute Service] aplicativo personalizado
description: Implantar [!DNL Asset Compute Service] aplicativo personalizado.
exl-id: a68d4f59-8a8f-43b2-8bc6-19320ac8c9ef
source-git-commit: f15b9819d3319d22deccdf7e39c0f72728baaa39
workflow-type: tm+mt
source-wordcount: '170'
ht-degree: 0%

---

# Implantar um aplicativo personalizado {#deploy-custom-application}

Para implantar seu aplicativo, use [implantação do aplicativo aio](https://github.com/adobe/aio-cli#aio-appdeploy) comando. No terminal, o comando exibe um URL para acessar o aplicativo personalizado. A URL está no formato `https://[namespace].adobeio-static.net/api/v1/web/[appname]-[appversion]/[workername]`.

Para obter o mesmo URL sem reimplantar a aplicação, use [`aio app get-url`](https://github.com/adobe/aio-cli#aio-app-get-url-action) comando.

Use o URL em uma [Processando perfil no [!DNL Experience Manager] as a [!DNL Cloud Service]](https://experienceleague.adobe.com/pt-br/docs/experience-manager-cloud-service/content/assets/manage/asset-microservices-configure-and-use) para integrar seu aplicativo com o [!DNL Experience Manager] as a [!DNL Cloud Service].

Verifique se o projeto e o espaço de trabalho do App Builder correspondem à [!DNL Experience Manager] as a [!DNL Cloud Service] ambiente no qual você deseja usar sua ação. Ele tem ambientes diferentes para desenvolvimento, preparo e produção. Você pode verificar o ambiente verificando `AIO_runtime_*` credenciais definidas no arquivo ENV, na raiz do aplicativo Adobe Developer App Builder. Por exemplo, para implantar em um `Stage` espaço de trabalho, o `AIO_runtime_namespace` é do formato `xxxxxx_xxxxxxxxx_stage`. Para integrar com o [!DNL Experience Manager] as a [!DNL Cloud Service] Ambiente de produção, use URLs de aplicativos do seu Adobe Developer App Builder `Production` espaço de trabalho.

>[!CAUTION]
>
>Não use um espaço de trabalho pessoal em [!DNL Experience Manager] ambientes.

>[!MORELIKETHIS]
>
>* [Entender e gerenciar ambientes no [!DNL Experience Manager] as a [!DNL Cloud Service]](https://experienceleague.adobe.com/en/docs/experience-manager-cloud-service/content/implementing/using-cloud-manager/manage-environments).
