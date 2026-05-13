---
title: Implantar  [!DNL Asset Compute Service] aplicativo personalizado
description: Implantar  [!DNL Asset Compute Service] aplicativo personalizado.
exl-id: a68d4f59-8a8f-43b2-8bc6-19320ac8c9ef
TQID: https://experienceleague.adobe.com/JN29pTaNB93DKALUqIbXhwswlzHiYQSowFZAbgHA5TA
product_v2:
  - id: d09181b5-a36a-43de-ba01-36641440bc43
  - id: fd1f54a9-f50c-467d-8956-cebbaf4f3eb8
role_v2:
  - id: ff6a42d2-313e-452e-93a6-792e4fad9ff8
source-git-commit: 2510f77fed8d0f0708e09f32d0b13a437d2ede4f
workflow-type: tm+mt
source-wordcount: 210
ht-degree: 7%

---

# Implantar um aplicativo personalizado {#deploy-custom-application}

Para implantar seu aplicativo, use o comando [aio app deploy](https://github.com/adobe/aio-cli#aio-appdeploy). No terminal, o comando exibe um URL para acessar o aplicativo personalizado. A URL tem o formato `https://[namespace].adobeio-static.net/api/v1/web/[appname]-[appversion]/[workername]`.

Para obter a mesma URL sem reimplantar o aplicativo, use o comando [`aio app get-url`](https://github.com/adobe/aio-cli#aio-app-get-url-action).

Use a URL em um [Perfil de Processamento no [!DNL Experience Manager] as a [!DNL Cloud Service]](https://experienceleague.adobe.com/pt-br/docs/experience-manager-cloud-service/content/assets/manage/asset-microservices-configure-and-use) para integrar seu aplicativo ao [!DNL Experience Manager] as a [!DNL Cloud Service].

Verifique se o projeto e o espaço de trabalho do App Builder correspondem ao ambiente [!DNL Experience Manager] as a [!DNL Cloud Service] no qual você deseja usar sua ação. Ele tem ambientes diferentes para desenvolvimento, preparo e produção. Você pode verificar o ambiente verificando as credenciais do `AIO_runtime_*` definidas dentro do arquivo ENV na raiz do aplicativo Adobe Developer App Builder. Por exemplo, para implantar em um espaço de trabalho `Stage`, `AIO_runtime_namespace` tem o formato `xxxxxx_xxxxxxxxx_stage`. Para integrar com o [!DNL Experience Manager] como um ambiente de Produção do [!DNL Cloud Service], use URLs de aplicativo do espaço de trabalho do Adobe Developer App Builder `Production`.

>[!CAUTION]
>
>Não use um espaço de trabalho pessoal em ambientes críticos [!DNL Experience Manager].

>[!MORELIKETHIS]
>
>* [Entender e gerenciar ambientes no [!DNL Experience Manager] as a [!DNL Cloud Service]](https://experienceleague.adobe.com/en/docs/experience-manager-cloud-service/content/implementing/using-cloud-manager/manage-environments).
