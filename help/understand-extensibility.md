---
title: Entenda sobre estender [!DNL Asset Compute Service].
description: Quando e como estender [!DNL Asset Compute Service] a funcionalidade para fazer o processamento de ativos personalizados.
translation-type: tm+mt
source-git-commit: 54afa44d8d662ee1499a385f504fca073ab6c347
workflow-type: tm+mt
source-wordcount: '275'
ht-degree: 4%

---


# Introdução à extensibilidade {#introduction-to-extensibilty}

Muitos requisitos de execução, como conversão em formatos e redimensionamento de imagens, são abordados por Perfil de [processamento [!DNL Experience Manager] em Cloud Service](https://docs.adobe.com/content/help/en/experience-manager-cloud-service/assets/asset-microservices-overview.html). Os requisitos de negócios mais complexos podem precisar de uma solução personalizada que atenda às necessidades de uma organização. [!DNL Asset Compute Service] pode ser estendido criando aplicativos personalizados chamados de Perfis de processamento em [!DNL Experience Manager]. Esses aplicativos personalizados atendem aos casos [de uso](https://docs.adobe.com/content/help/br/experience-manager-cloud-service/assets/manage/asset-microservices-configure-and-use.html)suportados.

>[!NOTE]
>
>[!DNL Asset Compute Service] está disponível apenas para uso com [!DNL Experience Manager] um Cloud Service.

Os aplicativos personalizados são aplicativos [do Project Firefly](https://github.com/AdobeDocs/project-firefly) sem cabeçalho. A extensão [!DNL Asset Compute Service] com aplicativos personalizados é simplificada por meio do SDK [do](https://github.com/adobe/asset-compute-sdk) Asset Compute e da ferramenta para desenvolvedores do Project Firefly. Isso permite que os desenvolvedores se concentrem na lógica comercial. Criar aplicativos personalizados é tão simples quanto criar uma ação simples do Adobe I/O Runtime sem servidor. É uma única função JavaScript Node.js. O exemplo [do aplicativo personalizado](https://github.com/adobe/asset-compute-example-workers/blob/master/projects/worker-basic/worker-basic.js) básico ilustra isso.

## Pré-requisitos e requisitos de provisionamento {#prerequisites-and-provisioning}

Certifique-se de atender aos seguintes pré-requisitos:

* As ferramentas do Project Firefly são instaladas em sua máquina.
* Uma [!DNL Experience Cloud] organização. Mais informações [aqui](https://github.com/AdobeDocs/project-firefly/blob/master/getting_started/setup.md#acquire-access-and-credentials).
* A Organização da experiência deve ter [!DNL Experience Manager] como um Cloud Service habilitado.
* [!DNL Adobe Experience Cloud] organização é parte do programa de pré-visualização do [!DNL Project Firefly] desenvolvedor. Consulte [como solicitar acesso](https://github.com/AdobeDocs/project-firefly/blob/master/overview/getting_access.md).
* Certifique-se de uma função de desenvolvedor ou de permissões de administrador na organização para o desenvolvedor.
* Verifique se a CLI [de E/S do](https://github.com/adobe/aio-cli) Adobe está instalada localmente.

<!-- TBD for later:

* What all accesses and licenses are required?
* What all permissions are required to create, debug, and deploy custom applications?
* How do developers get access and provision the required apps?
* What is repository management?
* Anything on security and data transfer?
* What about handling personal or sensitive information?
* Custom application SLA is dependent on SLAs of various services it depends on.
* Document how the devs can get to know the KPIs of their custom applications. The KPIs are dependent on the performance at Adobe's side, amongst other things.
-->