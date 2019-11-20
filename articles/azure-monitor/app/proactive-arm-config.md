---
title: Azure Application Insights akıllı algılama kuralı ayarlarını Azure Resource Manager şablonlarla yapılandırma | Microsoft Docs
description: Azure Resource Manager şablonlarıyla Azure Application Insights akıllı algılama kuralları yönetimini ve yapılandırmasını otomatikleştirin
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: harelbr
ms.author: harelbr
ms.date: 06/26/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 8b55271b39bf2a65dababbef58f7389ca07d57d8
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72818840"
---
# <a name="manage-application-insights-smart-detection-rules-using-azure-resource-manager-templates"></a>Azure Resource Manager şablonları kullanarak Application Insights akıllı algılama kurallarını yönetme

Application Insights içindeki akıllı algılama kuralları [Azure Resource Manager şablonlar](../../azure-resource-manager/resource-group-authoring-templates.md)kullanılarak yönetilebilir ve yapılandırılabilir.
Bu yöntem, Azure Resource Manager otomasyonu ile yeni Application Insights kaynakları dağıtıldığında veya mevcut kaynakların ayarlarını değiştirirken kullanılabilir.

## <a name="smart-detection-rule-configuration"></a>Akıllı algılama kuralı yapılandırması

Akıllı algılama kuralı için aşağıdaki ayarları yapılandırabilirsiniz:
- Kural etkinse (varsayılan değer **true**'dur.)
- E-postaların, aboneliğin [Izleme okuyucusu](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) ile ilişkili kullanıcılara gönderilmesi ve bir algılama bulunduğunda [katkıda bulunan rollerinin izlenmesi](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) gerekir (varsayılan değer **true**'dur.)
- Bir algılama bulunduğunda bildirim alması gereken ek e-posta alıcıları.
    -  _Önizleme_olarak Işaretlenen akıllı algılama kuralları için e-posta yapılandırması kullanılamaz.

Azure Resource Manager aracılığıyla kural ayarlarını yapılandırmaya izin vermek için, akıllı algılama kuralı yapılandırması artık **ProactiveDetectionConfigs**adlı Application Insights kaynağında iç kaynak olarak kullanılabilir.
En fazla esneklik için, her akıllı algılama kuralı benzersiz bildirim ayarlarıyla yapılandırılabilir.

## 

## <a name="examples"></a>Örnekler

Aşağıda, Azure Resource Manager şablonları kullanarak akıllı algılama kurallarının ayarlarının nasıl yapılandırılacağı gösterilmektedir.
Tüm örnekler, " _MyApplication"_ adlı bir Application Insights kaynağına ve dahili olarak _"longdependencyduration"_ olarak adlandırılan "uzun bağımlılık süresi akıllı algılama kuralına" başvurur.
Application Insights kaynak adını değiştirdiğinizden ve ilgili akıllı algılama kuralını dahili adı olarak belirttiğinizden emin olun. Her akıllı algılama kuralı için karşılık gelen iç Azure Resource Manager adlarının bir listesi için aşağıdaki tabloyu kontrol edin.

### <a name="disable-a-smart-detection-rule"></a>Akıllı algılama kuralını devre dışı bırakma

```json
{
      "apiVersion": "2018-05-01-preview",
      "name": "myApplication",
      "type": "Microsoft.Insights/components",
      "location": "[resourceGroup().location]",
      "properties": {
        "ApplicationId": "myApplication"
      },
      "resources": [
        {
          "apiVersion": "2018-05-01-preview",
          "name": "longdependencyduration",
          "type": "ProactiveDetectionConfigs",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Insights/components', 'myApplication')]"
          ],
          "properties": {
            "name": "longdependencyduration",
            "sendEmailsToSubscriptionOwners": true,
            "customEmails": [],
            "enabled": false
          }
        }
      ]
    }
```

### <a name="disable-sending-email-notifications-for-a-smart-detection-rule"></a>Akıllı algılama kuralı için e-posta bildirimleri göndermeyi devre dışı bırak

```json
{
      "apiVersion": "2018-05-01-preview",
      "name": "myApplication",
      "type": "Microsoft.Insights/components",
      "location": "[resourceGroup().location]",
      "properties": {
        "ApplicationId": "myApplication"
      },
      "resources": [
        {
          "apiVersion": "2018-05-01-preview",
          "name": "longdependencyduration",
          "type": "ProactiveDetectionConfigs",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Insights/components', 'myApplication')]"
          ],
          "properties": {
            "name": "longdependencyduration",
            "sendEmailsToSubscriptionOwners": false,
            "customEmails": [],
            "enabled": true
          }
        }
      ]
    }
```

### <a name="add-additional-email-recipients-for-a-smart-detection-rule"></a>Akıllı algılama kuralı için ek e-posta alıcıları ekleme

```json
{
      "apiVersion": "2018-05-01-preview",
      "name": "myApplication",
      "type": "Microsoft.Insights/components",
      "location": "[resourceGroup().location]",
      "properties": {
        "ApplicationId": "myApplication"
      },
      "resources": [
        {
          "apiVersion": "2018-05-01-preview",
          "name": "longdependencyduration",
          "type": "ProactiveDetectionConfigs",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Insights/components', 'myApplication')]"
          ],
          "properties": {
            "name": "longdependencyduration",
            "sendEmailsToSubscriptionOwners": true,
            "customEmails": ['alice@contoso.com', 'bob@contoso.com'],
            "enabled": true
          }
        }
      ]
    }

```

### <a name="failure-anomalies-v2-non-classic-alert-rule"></a>Hata Anoıes v2 (klasik olmayan) uyarı kuralı

Bu Azure Resource Manager şablonu, 2 önem derecesine sahip bir hata anomali v2 uyarı kuralını yapılandırmayı gösterir. Hata Anoparçaları uyarı kuralının bu yeni sürümü, yeni Azure uyarı platformunun bir parçasıdır ve [Klasik uyarı](https://azure.microsoft.com/updates/classic-alerting-monitoring-retirement/)kullanımdan kaldırma işleminin bir parçası olarak kullanımdan kaldırılan klasik sürümü değiştirir.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "microsoft.alertsmanagement/smartdetectoralertrules",
            "apiVersion": "2019-03-01",
            "name": "Failure Anomalies - my-app",
            "location": "global", 
            "properties": {
                  "description": "Failure Anomalies notifies you of an unusual rise in the rate of failed HTTP requests or dependency calls.",
                  "state": "Enabled",
                  "severity": "2",
                  "frequency": "PT1M",
                  "detector": {
                  "id": "FailureAnomaliesDetector"
                  },
                  "scope": ["/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/MyResourceGroup/providers/microsoft.insights/components/my-app"],
                  "actionGroups": {
                        "groupIds": ["/subscriptions/00000000-1111-2222-3333-444444444444/resourcegroups/MyResourceGroup/providers/microsoft.insights/actiongroups/MyActionGroup"]
                  }
            }
        }
    ]
}
```

> [!NOTE]
> Bu Azure Resource Manager şablonu, hata Anoileri v2 uyarı kuralı için benzersizdir ve bu makalede açıklanan diğer klasik akıllı algılama kurallarından farklıdır.   

## <a name="smart-detection-rule-names"></a>Akıllı algılama kuralı adları

Aşağıda, portalda göründükleri şekilde, Azure Resource Manager şablonunda kullanılması gereken dahili adlarıyla birlikte akıllı algılama kuralı adlarının bir tablosu verilmiştir.

> [!NOTE]
> _Önizleme_ olarak işaretlenen akıllı algılama kuralları, e-posta bildirimlerini desteklemez. Bu nedenle, bu kurallar için yalnızca _etkin_ özelliği ayarlayabilirsiniz. 

| Azure portal kural adı | İç ad
|:---|:---|
| Yavaş sayfa yükleme süresi | yavaşpageloadtime |
| Yavaş sunucu yanıt süresi | yavaşsunucuresponsetime |
| Uzun bağımlılık süresi | longdependencyduration |
| Sunucu yanıt süresi düşüklüğüne | korlationınserverresponsetime |
| Bağımlılık süresinde düşme | degradationindependencyduration |
| İzleme önem oranına göre düşme (Önizleme) | extension_traceseveritydetector |
| Özel durum biriminde olağan dışı artış (Önizleme) | extension_exceptionchangeextension |
| Olası bellek sızıntısı algılandı (Önizleme) | extension_memoryleakextension |
| Olası güvenlik sorunu algılandı (Önizleme) | extension_securityextensionspackage |
| Günlük veri hacminde olağan dışı artış (Önizleme) | extension_billingdatavolumedailyspikeextension |

## <a name="next-steps"></a>Sonraki Adımlar

Otomatik algılama hakkında daha fazla bilgi edinin:

- [Hata anormallikleri](../../azure-monitor/app/proactive-failure-diagnostics.md)
- [Bellek sızıntıları](../../azure-monitor/app/proactive-potential-memory-leak.md)
- [Performans anormallikleri](../../azure-monitor/app/proactive-performance-diagnostics.md)
