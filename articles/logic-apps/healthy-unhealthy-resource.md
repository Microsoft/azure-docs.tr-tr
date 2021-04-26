---
title: Azure Güvenlik Merkezi 'nde mantıksal uygulamaları izlemek için günlüğü ayarlama
description: Tanılama günlüğü oluşturarak Azure Güvenlik Merkezi 'nde Logic Apps kaynaklarınızın sistem durumunu izleyin.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm
ms.topic: conceptual
ms.date: 12/07/2020
ms.openlocfilehash: 42b33a5b96de7334f8310b040052c633342f5e05
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101712394"
---
# <a name="set-up-logging-to-monitor-logic-apps-in-azure-security-center"></a>Azure Güvenlik Merkezi 'nde mantıksal uygulamaları izlemek için günlüğü ayarlama

Logic Apps kaynaklarınızı [Microsoft Azure Güvenlik Merkezi](../security-center/security-center-introduction.md)'nde izlerken, [mantıksal uygulamalarınızın varsayılan ilkeleri takip edilip edilmeyeceğini gözden](#view-logic-apps-health-status)geçirebilirsiniz. Azure, günlüğü etkinleştirdikten ve günlüklerin hedefini doğru şekilde ayarladıktan sonra bir Logic Apps kaynağının sistem durumunu gösterir. Bu makalede, tanılama günlüğü 'nün nasıl yapılandırılacağı ve tüm mantıksal uygulamalarınızın sağlıklı kaynaklar olduğundan emin olma açıklanmaktadır.

> [!TIP]
> Logic Apps hizmetinin geçerli durumunu bulmak için, kullanılabilir her bölgedeki farklı ürün ve hizmetlerin durumunu listeleyen [Azure durum sayfasını](https://status.azure.com/)gözden geçirin.

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği. Aboneliğiniz yoksa, başlamadan önce [ücretsiz bir Azure hesabı oluşturun](https://azure.microsoft.com/free/) .
* [Tanılama günlüğü etkin](#enable-diagnostic-logging)olan mevcut mantıksal uygulamalar.
* Mantıksal uygulamanız için günlüğe kaydetmeyi etkinleştirmek için gereken bir Log Analytics çalışma alanı. Çalışma alanınız yoksa, önce [çalışma alanınızı oluşturun](../azure-monitor/logs/quick-create-workspace.md).

## <a name="enable-diagnostic-logging"></a>Tanılama günlüğünü etkinleştirme

Logic Apps 'in kaynak sistem durumunu görüntüleyebilmeniz için önce [tanılama günlüğü 'nü ayarlamanız](monitor-logic-apps-log-analytics.md)gerekir. Zaten bir Log Analytics çalışma alanınız varsa, mantıksal uygulamanızı oluştururken veya mevcut mantıksal uygulamalarda günlüğe kaydetmeyi etkinleştirebilirsiniz.

> [!TIP]
> Varsayılan öneri, Logic Apps için tanılama günlüklerini etkinleştirmektir. Ancak, mantıksal uygulamalarınız için bu ayarı denetlersiniz. Mantıksal uygulamalarınız için tanılama günlüklerini etkinleştirdiğinizde, güvenlik olaylarını çözümlemeye yardımcı olması için bu bilgileri kullanabilirsiniz.

### <a name="check-diagnostic-logging-setting"></a>Tanılama günlüğü ayarını denetle

Mantıksal uygulamalarınızın tanılama günlüğü 'nün etkin olup olmadığından emin değilseniz, güvenlik merkezi 'ne bakabilirsiniz:

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Arama çubuğunda **Güvenlik Merkezi**' ni girin ve seçin.
1. Güvenlik Merkezi Pano menüsünde, **genel** altında **öneriler**' i seçin.
1. Güvenlik önerileri tablosunda, **denetimi etkinleştir ve** &gt; **Logic Apps ' de günlüğe kaydetme tanılama günlüklerini** bul ve Seç ' in güvenlik denetimleri tablosunda etkinleştirilmesi gerekir.
1. Öneri sayfasında, **Düzeltme adımları** bölümünü genişletin ve seçenekleri gözden geçirin. **Hızlı düzeltmesini** seçerek Logic Apps tanılamayı etkinleştirebilirsiniz! düğmesini seçerek veya el ile düzeltme yönergelerini izleyerek.

## <a name="view-logic-apps-health-status"></a>Mantıksal uygulamaların sistem durumunu görüntüleme

[Tanılama günlüğünü](#enable-diagnostic-logging)etkinleştirdikten sonra, güvenlik merkezi 'nde mantıksal uygulamalarınızın sistem durumunu görebilirsiniz.

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Arama çubuğunda **Güvenlik Merkezi**' ni girin ve seçin.
1. Güvenlik Merkezi Pano menüsünde, **genel** altında **Stok**' ı seçin.
1. Sayım sayfasında, varlık listenizi yalnızca Logic Apps kaynakları gösterecek şekilde filtreleyin. Sayfa menüsünde, **kaynak türleri** &gt; **mantıksal uygulamalar**' ı seçin.

   **Sağlıksız kaynaklar** sayacı, güvenlik merkezi 'nin sağlıksız olduğunu düşündüğü Logic Apps sayısını gösterir.
1.  Logic Apps kaynakları listesinde **öneriler** sütununu gözden geçirin. Belirli bir mantıksal uygulamanın sistem durumu ayrıntılarını gözden geçirmek için bir kaynak adı seçin veya üç nokta düğmesini (**...**) &gt; seçin **Kaynağı görüntüleyin**.
1.  Olası kaynak sistem durumu sorunlarını gidermek için, mantıksal uygulamalarınız için listelenen adımları izleyin.

Tanılama günlüğü zaten etkinse günlüklerinizin hedefle ilgili bir sorun olabilir. [Farklı tanılama günlüğü hedefleriyle ilgili sorunları nasıl düzelteceğinizi](#fix-diagnostic-logging-for-logic-apps)inceleyin.

## <a name="fix-diagnostic-logging-for-logic-apps"></a>Mantıksal uygulamalar için tanılama günlüğünü çözme

[Mantıksal uygulamalarınız Güvenlik Merkezi 'nde sağlıksız olarak listeleniyorsa](#view-logic-apps-health-status), Azure Portal veya Azure CLI aracılığıyla mantıksal uygulamanızı kod görünümünde açın. Ardından, tanılama günlüklerinizin hedef yapılandırmasını kontrol edin: [azure Log Analytics](#log-analytics-and-event-hubs-destinations), [Azure Event Hubs](#log-analytics-and-event-hubs-destinations)veya [bir Azure depolama hesabı](#storage-account-destination).

### <a name="log-analytics-and-event-hubs-destinations"></a>Log Analytics ve Event Hubs hedefleri

Logic Apps tanılama günlüklerinizin hedefi olarak Log Analytics veya Event Hubs kullanırsanız, aşağıdaki ayarları kontrol edin. 

1. Tanılama günlüklerini etkinleştirdiğini onaylamak için Tanılama ayarları `logs.enabled` alanının olarak ayarlandığından emin olun `true` . 
1. Bunun yerine, hedef olarak bir depolama hesabı ayarlamadıysanız emin olmak için `storageAccountId` alanın olarak ayarlandığından emin olun `false` .

Örnek:

```json
"allOf": [
    {
        "field": "Microsoft.Insights/diagnosticSettings/logs.enabled",
        "equals": "true"
    },
    {
        "anyOf": [
            {
                "field": "Microsoft.Insights/diagnosticSettings/logs[*].retentionPolicy.enabled",
                "notEquals": "true"
            },
            {
                "field": "Microsoft.Insights/diagnosticSettings/storageAccountId",
                "exists": false
            }
        ]
    }
] 
```

### <a name="storage-account-destination"></a>Depolama hesabı hedefi

Logic Apps tanılama günlüklerinizin hedefi olarak bir depolama hesabı kullanırsanız, aşağıdaki ayarları kontrol edin.

1. Tanılama günlüklerini etkinleştirdiğini onaylamak için Tanılama ayarları `logs.enabled` alanının olarak ayarlandığından emin olun `true` .
1. Tanılama günlüklerinizi için bir bekletme ilkesi etkinleştirdiğini onaylamak için `retentionPolicy.enabled` alanın olarak ayarlandığından emin olun `true` .
1. 0-365 günlük bir saklama süresi ayarlamanızı onaylamak için `retentionPolicy.days` alanın 0 ile 365 arasında bir sayı olarak ayarlandığını kontrol edin.

```json
"allOf": [
    {
        "field": "Microsoft.Insights/diagnosticSettings/logs[*].retentionPolicy.enabled",
        "equals": "true"
    },
    {
        "anyOf": [
            {
                "field": "Microsoft.Insights/diagnosticSettings/logs[*].retentionPolicy.days",
                "equals": "0"
            },
            {
                "field": "Microsoft.Insights/diagnosticSettings/logs[*].retentionPolicy.days",
                "equals": "[parameters('requiredRetentionDays')]"
            }
          ]
    },
    {
        "field": "Microsoft.Insights/diagnosticSettings/logs.enabled",
        "equals": "true"
    }
]
```