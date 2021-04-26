---
title: Application Insights geliştirici portalı ile tümleştirme
titleSuffix: Azure API Management
description: Application Insights yönetilen veya şirket içinde barındırılan geliştirici portalınızla nasıl tümleştirileceğini öğrenin.
author: dlepow
ms.author: apimpm
ms.date: 03/25/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: 5e1c9caa55d0b3b7820f766a30c878fdc01f5137
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741899"
---
# <a name="integrate-application-insights-to-developer-portal"></a>Application Insights geliştirici portalı ile tümleştirme

Azure Izleyici 'nin popüler bir özelliği Application Insights. Geliştiriciler ve DevOps uzmanları için genişletilebilir bir uygulama performans yönetimi (APM) hizmetidir. Geliştirici portalınızı izlemek ve performans anormallarını algılamak için kullanın. Application Insights, geliştirici portalınızı ziyaret ederken hangi kullanıcıların gerçekten ne yaptığını öğrenmenize yardımcı olacak güçlü analiz araçları içerir.

## <a name="add-application-insights-to-your-portal"></a>Portala Application Insights ekleyin

Yönetilen veya şirket içinde barındırılan geliştirici portalınıza Application Insights atamak için aşağıdaki adımları izleyin.

> [!IMPORTANT]
> Adım 1 ve 2 yönetilen portallar için gerekli değildir. Yönetilen bir portalınız varsa 4. adıma atlayın.

1. Geliştirici portalının en son sürümü için [yerel bir ortam](developer-portal-self-host.md#step-1-set-up-local-environment) ayarlayın.

1. [Azure Için Paperbits](https://github.com/paperbits/paperbits-azure)eklemek üzere **NPM** paketini yüklerken:

    ```console
    npm install @paperbits/azure --save
    ```

1. `startup.publish.ts` `src` Klasöründeki dosyada Application Insights modülünü içeri ve dışarı kaydedin:

    ```typescript
    import { AppInsightsPublishModule } from "@paperbits/azure";
    ...
    injector.bindModule(new AppInsightsPublishModule());
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

1. Site yapılandırmasını önceki adımdan Application Insights yapılandırma ile genişletin:

    ```http
    PUT /contentTypes/document/contentItems/configuration
    ```

    ```json
    {
        "nodes": [
            {
                "site": { ... },
                "integration": {
                    "appInsights": {
                        "instrumentationKey": "xxxxxxxx-xxxx-xxxx-xxxxxxxxxxxxxxxxx"
                    }
                }
            }
        ]
    }
    ```

## <a name="next-steps"></a>Sonraki adımlar

Geliştirici portalı hakkında daha fazla bilgi edinin:

- [Azure API Management geliştirici portalına genel bakış](api-management-howto-developer-portal.md)
- [Portal dağıtımlarını otomatikleştirin](automate-portal-deployments.md)
- [Geliştirici portalını kendi kendine barındırma](developer-portal-self-host.md)
