---
title: Kaynak Yöneticisi şablonu kullanarak Azure hizmet bildirimlerinde etkinlik günlüğü uyarıları alma
description: Azure hizmeti gerçekleştiğinde SMS, e-posta veya Web Kancası aracılığıyla bildirim alın.
ms.date: 06/29/2020
ms.topic: quickstart
ms.custom:
- subject-armqs
- mode-arm
ms.openlocfilehash: 730c023de61275d95fe594642149770af42b34b9
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107535745"
---
# <a name="quickstart-create-activity-log-alerts-on-service-notifications-using-an-arm-template"></a>Hızlı başlangıç: ARM şablonu kullanarak hizmet bildirimlerinde etkinlik günlüğü uyarıları oluşturma

Bu makalede, bir Azure Resource Manager şablonu (ARM şablonu) kullanarak hizmet durumu bildirimleri için etkinlik günlüğü uyarılarını ayarlama konusu gösterilmektedir.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Hizmet durumu bildirimleri [Azure etkinlik günlüğünde](../azure-monitor/essentials/platform-logs-overview.md)depolanır. Etkinlik günlüğünde depolanan büyük miktarda bilgi hacmi verildiğinde, hizmet durumu bildirimlerinde uyarıları görüntülemeyi ve ayarlamayı kolaylaştıran ayrı bir kullanıcı arabirimi vardır.

Azure, Azure aboneliğinize hizmet durumu bildirimleri gönderdiğinde bir uyarı alabilirsiniz. Uyarıyı temel alarak yapılandırabilirsiniz:

- Hizmet durumu bildirimi sınıfı (hizmet sorunları, planlı bakım, sağlık danışma belgeleri).
- Abonelik etkilendi.
- Hizmet (ler) i etkilendi.
- Bu bölgeler etkilendi.

> [!NOTE]
> Hizmet durumu bildirimleri, kaynak sistem durumu olaylarıyla ilgili bir uyarı göndermez.

Ayrıca, uyarının kime gönderileceğini de yapılandırabilirsiniz:

- Mevcut bir eylem grubu seçin.
- Yeni bir eylem grubu oluşturun (gelecekteki uyarılar için kullanılabilir).

Eylem grupları hakkında daha fazla bilgi edinmek için bkz. [Eylem grupları oluşturma ve yönetme](../azure-monitor/alerts/action-groups.md).

## <a name="prerequisites"></a>Ön koşullar

- Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.
- Komutları yerel bilgisayarınızdan çalıştırmak için Azure CLı veya Azure PowerShell modüllerini yüklemelisiniz. Daha fazla bilgi için bkz. [Azure CLI](/cli/azure/install-azure-cli) 'yı yükleyip [Azure PowerShell yüklemesi](/powershell/azure/install-az-ps).

## <a name="review-the-template"></a>Şablonu gözden geçirme

Aşağıdaki şablon, bir e-posta hedefi olan bir eylem grubu oluşturur ve hedef abonelik için tüm hizmet durumu bildirimlerine izin vermez. Bu şablonu *CreateServiceHealthAlert.js* olarak kaydedin.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "actionGroups_name": {
      "type": "String",
      "defaultValue": "SubHealth"
    },
    "activityLogAlerts_name": {
      "type": "String",
      "defaultValue": "ServiceHealthActivityLogAlert"
    },
    "emailAddress": {
      "type": "string"
    }
  },
  "variables": {
    "alertScope": "[concat('/','subscriptions','/',subscription().subscriptionId)]"
  },
  "resources": [
    {
      "comments": "Action Group",
      "type": "microsoft.insights/actionGroups",
      "apiVersion": "2019-06-01",
      "name": "[parameters('actionGroups_name')]",
      "location": "Global",
      "scale": null,
      "dependsOn": [],
      "tags": {},
      "properties": {
        "groupShortName": "[parameters('actionGroups_name')]",
        "enabled": true,
        "emailReceivers": [
          {
            "name": "[parameters('actionGroups_name')]",
            "emailAddress": "[parameters('emailAddress')]"
          }
        ],
        "smsReceivers": [],
        "webhookReceivers": []
      }
    },
    {
      "comments": "Service Health Activity Log Alert",
      "type": "microsoft.insights/activityLogAlerts",
      "apiVersion": "2017-04-01",
      "name": "[parameters('activityLogAlerts_name')]",
      "location": "Global",
      "scale": null,
      "dependsOn": [
        "[resourceId('microsoft.insights/actionGroups', parameters('actionGroups_name'))]"
      ],
      "tags": {},
      "properties": {
        "scopes": [
          "[variables('alertScope')]"
        ],
        "condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "ServiceHealth"
            },
            {
              "field": "properties.incidentType",
              "equals": "Incident"
            }
          ]
        },
        "actions": {
          "actionGroups": [
            {
              "actionGroupId": "[resourceId('microsoft.insights/actionGroups', parameters('actionGroups_name'))]",
              "webhookProperties": {}
            }
          ]
        },
        "enabled": true,
        "description": ""
      }
    }
  ]
}
```

Şablon iki kaynağı tanımlar:

- [Microsoft. Insights/actionGroups](/azure/templates/microsoft.insights/actiongroups)
- [Microsoft. Insights/activityLogAlerts](/azure/templates/microsoft.insights/activityLogAlerts)

## <a name="deploy-the-template"></a>Şablonu dağıtma

CLı ve PowerShell kullanarak aşağıdaki örnekler gibi [BIR ARM şablonunu dağıtmak](../azure-resource-manager/templates/deploy-portal.md) için herhangi bir standart yöntemi kullanarak şablonu dağıtın. **Kaynak grubu** ve **emadresi** için örnek değerleri, ortamınız için uygun değerlerle değiştirin.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az login
az deployment group create --name CreateServiceHealthAlert --resource-group my-resource-group --template-file CreateServiceHealthAlert.json --parameters emailAddress='user@contoso.com'
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName my-subscription
New-AzResourceGroupDeployment -Name CreateServiceHealthAlert -ResourceGroupName my-resource-group -TemplateFile CreateServiceHealthAlert.json -emailAddress user@contoso.com
```

---

## <a name="validate-the-deployment"></a>Dağıtımı doğrulama

Çalışma alanının, aşağıdaki komutlardan birini kullanarak oluşturulduğunu doğrulayın. **Kaynak grubu** için örnek değerleri yukarıda kullandığınız değerle değiştirin.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az monitor activity-log alert show --resource-group my-resource-group --name ServiceHealthActivityLogAlert
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
Get-AzActivityLogAlert -ResourceGroupName my-resource-group -Name ServiceHealthActivityLogAlert
```

---

## <a name="clean-up-resources"></a>Kaynakları temizleme

Sonraki hızlı başlangıç ve öğreticilerle çalışmaya devam etmeyi planlıyorsanız, bu kaynakları yerinde bırakmak isteyebilirsiniz. Artık gerekli değilse, uyarı kuralını ve ilgili kaynakları silen kaynak grubunu silin. Azure CLı veya Azure PowerShell kullanarak kaynak grubunu silmek için

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az group delete --name my-resource-group
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
Remove-AzResourceGroup -Name my-resource-group
```

---

## <a name="next-steps"></a>Sonraki adımlar

- [Azure hizmet durumu uyarılarını ayarlamaya yönelik en iyi uygulamalar](https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUa)hakkında bilgi edinin.
- [Azure hizmet durumu için mobil anında iletme bildirimleri ayarlamayı](https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUw)öğrenin.
- [Mevcut sorun yönetimi sistemleri için Web kancası bildirimlerinin nasıl yapılandırılacağını](service-health-alert-webhook-guide.md)öğrenin.
- [Hizmet durumu bildirimleri](service-notifications.md)hakkında bilgi edinin.
- [Bildirim hızı sınırlaması](../azure-monitor/alerts/alerts-rate-limiting.md)hakkında bilgi edinin.
- [Etkinlik günlüğü uyarısı Web kancası şemasını](../azure-monitor/alerts/activity-log-alerts-webhook.md)gözden geçirin.
- [Etkinlik günlüğü uyarılarına genel bir bakış](../azure-monitor/alerts/alerts-overview.md)elde edin ve uyarıları alma hakkında bilgi edinin.
- [Eylem grupları](../azure-monitor/alerts/action-groups.md)hakkında daha fazla bilgi edinin.
