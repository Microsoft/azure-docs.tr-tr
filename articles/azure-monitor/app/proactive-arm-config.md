---
title: Akıllı algılama kuralı ayarları-Azure Application Insights
description: Azure Resource Manager şablonlarıyla Azure Application Insights akıllı algılama kuralları yönetimini ve yapılandırmasını otomatikleştirin
ms.topic: conceptual
author: harelbr
ms.author: harelbr
ms.date: 02/14/2021
ms.reviewer: mbullwin
ms.openlocfilehash: e3a7b71cd8975957754ba014ecc700484c27a6d7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101726130"
---
# <a name="manage-application-insights-smart-detection-rules-using-azure-resource-manager-templates"></a>Azure Resource Manager şablonları kullanarak Application Insights akıllı algılama kurallarını yönetme

Application Insights içindeki akıllı algılama kuralları [Azure Resource Manager şablonlar](../../azure-resource-manager/templates/template-syntax.md)kullanılarak yönetilebilir ve yapılandırılabilir.
Bu yöntem, Azure Resource Manager otomasyonu ile yeni Application Insights kaynakları dağıtıldığında veya mevcut kaynakların ayarlarını değiştirirken kullanılabilir.

## <a name="smart-detection-rule-configuration"></a>Akıllı algılama kuralı yapılandırması

Akıllı algılama kuralı için aşağıdaki ayarları yapılandırabilirsiniz:
- Kural etkinse (varsayılan değer **true**'dur.)
- E-postaların, aboneliğin [Izleme okuyucusu](../../role-based-access-control/built-in-roles.md#monitoring-reader) ile ilişkili kullanıcılara gönderilmesi ve bir algılama bulunduğunda [katkıda bulunan rollerinin izlenmesi](../../role-based-access-control/built-in-roles.md#monitoring-contributor) gerekir (varsayılan değer **true**'dur.)
- Bir algılama bulunduğunda bildirim alması gereken ek e-posta alıcıları.
    -  _Önizleme_ olarak Işaretlenen akıllı algılama kuralları için e-posta yapılandırması kullanılamaz.

Azure Resource Manager aracılığıyla kural ayarlarını yapılandırmaya izin vermek için, akıllı algılama kuralı yapılandırması artık **ProactiveDetectionConfigs** adlı Application Insights kaynağında iç kaynak olarak kullanılabilir.
En fazla esneklik için, her akıllı algılama kuralı benzersiz bildirim ayarlarıyla yapılandırılabilir.

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
        "Application_Type": "web"
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
        "Application_Type": "web"
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
        "Application_Type": "web"
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
            "customEmails": ["alice@contoso.com", "bob@contoso.com"],
            "enabled": true
          }
        }
      ]
    }

```


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

### <a name="failure-anomalies-alert-rule"></a>Hata bozuklukları uyarı kuralı

Bu Azure Resource Manager şablonu, önem derecesi 2 olan bir hata anomali uyarı kuralını yapılandırmayı gösterir.

> [!NOTE]
> Hata bozuklukları küresel bir hizmettir, bu nedenle genel konumda kural konumu oluşturulur.

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
> Bu Azure Resource Manager şablonu hata anomali uyarı kuralı için benzersizdir ve bu makalede açıklanan diğer klasik akıllı algılama kurallarından farklıdır. Hata Bozuklumlarını el ile yönetmek istiyorsanız bu işlem, Azure Izleyici uyarıları ' nda yapılır, ancak diğer tüm akıllı algılama kuralları Kullanıcı arabiriminin akıllı algılama bölmesinde yönetilir.

## <a name="next-steps"></a>Sonraki Adımlar

Otomatik algılama hakkında daha fazla bilgi edinin:

- [Hata anomalileri](./proactive-failure-diagnostics.md)
- [Bellek sızıntıları](./proactive-potential-memory-leak.md)
- [Performans anomalileri](./proactive-performance-diagnostics.md)

