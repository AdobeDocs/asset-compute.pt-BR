---
title: Introdução ao [!DNL Asset Compute Service]
description: "[!DNL Asset Compute Service] O é um serviço de processamento de ativos em nuvem que reduz a complexidade e melhora a escalabilidade."
exl-id: f8c89f65-5a94-44f3-aaac-4612ae291101
source-git-commit: c6f747ebd6d1b17834f1af0837609a148804f8a9
workflow-type: tm+mt
source-wordcount: '278'
ht-degree: 1%

---

# Visão geral do [!DNL Asset Compute Service] {#overview}

[!DNL Asset Compute Service] O é um serviço escalonável e extensível de [!DNL Adobe Experience Cloud] para processar ativos digitais. Ele pode transformar imagens, vídeos, documentos e outros formatos de arquivo em diferentes representações, incluindo miniaturas, texto e metadados extraídos e arquivos.

Os desenvolvedores têm a capacidade de plug-in de aplicativos de ativos personalizados (também chamados de trabalhadores personalizados) para atender a casos de uso personalizados. O serviço funciona no Adobe [!DNL I/O Runtime]. É extensível por meio de [!DNL Adobe Developer App Builder] aplicativos headless escritos em Node.js. Eles podem fazer operações personalizadas, como chamar APIs externas para executar operações de imagem ou aproveitar [!DNL Adobe Sensei] suporte.

[!DNL Adobe Developer App Builder] O é uma estrutura para criar e implantar aplicativos Web personalizados no Adobe [!DNL I/O Runtime] para estender as soluções da Adobe Experience Cloud. Para criar aplicativos personalizados, os desenvolvedores podem aproveitar [!DNL React Spectrum] (Kit de ferramentas do Adobe UI), crie microsserviços, eventos personalizados e organize APIs. Consulte [documentação do Adobe Developer App Builder](https://developer.adobe.com/app-builder/docs/overview/).

>[!NOTE]
>
>Atualmente, a [!DNL Asset Compute Service] pode ser usado somente via [!DNL Experience Manager] as a [!DNL Cloud Service]. Os administradores criam perfis de processamento que podem chamar o [!DNL Asset Compute Service] para transferir ativos para processamento. Consulte [uso de microsserviços de ativos e perfis de processamento](https://experienceleague.adobe.com/pt-br/docs/experience-manager-cloud-service/content/assets/manage/asset-microservices-configure-and-use).

## Casos de uso compatíveis do [!DNL Asset Compute Service] {#possible-use-cases-benefits}

[!DNL Asset Compute Service] O oferece suporte a alguns casos de uso comerciais comuns, como processamento básico de imagens, conversões específicas de aplicativos Adobe e criação de aplicativos personalizados que organizam requisitos comerciais complexos.

Você pode usar o [!DNL Asset Compute] serviço da web para gerar miniaturas para diferentes tipos de arquivos, renderizações de imagem de alta qualidade para o [formatos de arquivo compatíveis](https://experienceleague.adobe.com/en/docs/experience-manager-cloud-service/content/assets/file-format-support). Consulte [casos de uso compatíveis por meio da configuração personalizada](https://experienceleague.adobe.com/pt-br/docs/experience-manager-cloud-service/content/assets/manage/asset-microservices-configure-and-use).

>[!NOTE]
>
>O serviço não fornece armazenamento de ativos. Os usuários o fornecem e fornecem referências a locais de arquivos de origem e representação no armazenamento na nuvem.

<!-- TBD: Should this be mentioned in the docs?

|Asset Compute Service does not do this|Expectations from implementing client|
|---|---|
| Binary uploads or API-based asset ingestion. | Use other methods to ingest assets. |
| Store binaries or any persisted data across processing requests.| Each request is independent so treat it as a standalone request by sharing binary and processing instructions. |
| Store any configurations such as processing rules or settings for a user or an organization's account. | Add processing request to each request/instruction. |
| Direct event handling of asset creation events from storage systems and processing completed notifications, and errors. | Use [!DNL Adobe I/O] Events and other methods. |

-->

>[!MORELIKETHIS]
>
>* [Visão geral do processamento de ativos com os microsserviços de ativos no [!DNL Adobe Experience Manager] as a [!DNL Cloud Service]](https://experienceleague.adobe.com/pt-br/docs/experience-manager-cloud-service/content/assets/asset-microservices-overview).
>* [Documentação do Adobe Developer App Builder](https://developer.adobe.com/app-builder/docs/overview).
>* [Formatos de arquivo compatíveis com o processamento](https://experienceleague.adobe.com/en/docs/experience-manager-cloud-service/content/assets/file-format-support).

<!-- **TBD:**
* Clarify the service can only be used within AEM as Cloud Service. The docs provided as context for custom application developers. Not to be used as a standalone service.
  ** and API as that plays a role in custom applications (accepting standard params, invoking Nui itself in the future, etc. (this is an outlook))

* link to aem as cloud service docs on asset ingestion and customization with processing profiles.
-->
