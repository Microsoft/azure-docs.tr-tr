---
title: Google Tag Manager geliştirici portalı ile tümleştirme
titleSuffix: Azure API Management
description: Azure API Management 'de yönetilen veya şirket içinde barındırılan geliştirici portalınıza Google Tag Manager nasıl ekleyeceğinizi öğrenin.
author: dlepow
ms.author: apimpm
ms.date: 03/25/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: c209eb782787146d947b4684d41c5d1e9bb6364e
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741900"
---
# <a name="integrate-google-tag-manager-to-api-management-developer-portal"></a>Google Tag Manager API Management geliştirici portalına tümleştirme

[Google Tag Manager](https://developers.google.com/tag-manager) , Google tarafından oluşturulan bir etiket yönetim sistemidir. Web sitelerini izlemek ve analiz etmek için kullanılan JavaScript ve HTML etiketlerini yönetmek için bunu kullanabilirsiniz. Örneğin, Google Analytics, heatmaps veya chatbots gibi LiveChat 'i bütünleştirmek için Google Tag Manager kullanabilirsiniz.

Azure API Management 'de yönetilen veya şirket içinde barındırılan geliştirici portalınıza Google Tag Manager eklemek için bu makaledeki adımları izleyin.

## <a name="add-google-tag-manager-to-your-portal"></a>Portala Google Tag Manager ekleyin

Yönetilen veya şirket içinde barındırılan geliştirici portalınıza Google Tag Manager atamak için aşağıdaki adımları izleyin.

> [!IMPORTANT]
> Adım 1 ve 2 yönetilen portallar için gerekli değildir. Yönetilen bir portalınız varsa 4. adıma atlayın.

1. Geliştirici portalının en son sürümü için [yerel bir ortam](developer-portal-self-host.md#step-1-set-up-local-environment) ayarlayın.

1. [Google Tag Manager Için Paperbits](https://github.com/paperbits/paperbits-gtm)eklemek üzere **NPM** paketini yükledikten sonra:

    ```sh
    npm install @paperbits/gtm --save
    ```

1. `startup.publish.ts` `src` Klasöründeki dosyada, GTM modülünü içeri ve dışarı kaydedin:

    ```typescript
    import { GoogleTagManagerPublishModule } from "@paperbits/gtm/gtm.publish.module";
    ...
    injector.bindModule(new GoogleTagManagerPublishModule());
    ```
1. Portalın yapılandırmasını alın:

    ```http
    GET /contentTypes/document/contentItems/configuration
    ```

    ```json
    {
        "nodes": [
            {
                "site": {
                    "title": "Microsoft Azure API Management - developer portal",
                    "description": "Discover APIs, learn how to use them, try them out interactively, and sign up to acquire keys.",
                    "keywords": "Azure, API Management, API, developer",
                    "faviconSourceId": null,
                    "author": "Microsoft Azure API Management"
                }
            }
        ]
    }
    ```

1. Site yapılandırmasını önceki adımdan Google Tag Manager yapılandırma ile genişletin:

    ```http
    PUT /contentTypes/document/contentItems/configuration
    ```

    ```json
    {
        "nodes": [
            {
                "site": { ... },
                "integration": {
                    "googleTagManager": {
                        "containerId": "GTM-..."
                    }
                }
            }
        ]
    }
    ```

## <a name="next-steps"></a>Sonraki adımlar

- [Portal dağıtımlarını otomatikleştirin](automate-portal-deployments.md)
- [Geliştirici portalını kendi kendine barındırma](developer-portal-self-host.md)