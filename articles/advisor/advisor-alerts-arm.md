---
title: Kaynak Yöneticisi şablonu kullanarak yeni öneriler için Azure Advisor uyarıları oluşturma
description: Azure Resource Manager şablonu kullanarak Azure Advisor 'dan yeni öneriler için uyarı ayarlamayı öğrenin (ARM şablonu).
ms.topic: quickstart
ms.custom: subject-armqs, devx-track-azurepowershell
ms.date: 06/29/2020
ms.openlocfilehash: 716ab104ce6517aeb554b42522e5906829877259
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765678"
---
# <a name="quickstart-create-azure-advisor-alerts-on-new-recommendations-using-an-arm-template"></a>Hızlı başlangıç: ARM şablonunu kullanarak yeni önerilerle Azure Advisor uyarıları oluşturma

Bu makalede bir Azure Resource Manager şablonu (ARM şablonu) kullanarak Azure Advisor 'a yönelik yeni öneriler için nasıl uyarı ayarlanacağı gösterilmektedir.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Azure Advisor kaynaklarınızdan biri için yeni bir öneri algıladığında, [Azure etkinlik günlüğünde](../azure-monitor/essentials/platform-logs-overview.md)bir olay depolanır. Bu olaylara yönelik uyarıları, önerisine özgü uyarılar oluşturma deneyimini kullanarak Azure Advisor 'dan ayarlayabilirsiniz. Uyarıları almak istediğiniz kaynakları belirtmek için bir abonelik ve isteğe bağlı olarak bir kaynak grubu seçebilirsiniz.

Aşağıdaki özellikleri kullanarak öneri türlerini de belirleyebilirsiniz:

- Kategori
- Etki düzeyi
- Öneri türü

Ayrıca, bir uyarının tetiklendiği zaman gerçekleşecek eylemi de yapılandırabilirsiniz:

- Mevcut bir eylem grubu seçiliyor
- Yeni bir eylem grubu oluşturuluyor

Eylem grupları hakkında daha fazla bilgi edinmek için bkz. [Eylem grupları oluşturma ve yönetme](../azure-monitor/alerts/action-groups.md).

> [!NOTE]
> Danışman uyarıları Şu anda yalnızca yüksek kullanılabilirlik, performans ve maliyet önerileri için kullanılabilir. Güvenlik önerileri desteklenmez.

## <a name="prerequisites"></a>Ön koşullar

- Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.
- Komutları yerel bilgisayarınızdan çalıştırmak için Azure CLı veya Azure PowerShell modüllerini yüklemelisiniz. Daha fazla bilgi için bkz. [Azure CLI](/cli/azure/install-azure-cli) 'yı yükleyip [Azure PowerShell yüklemesi](/powershell/azure/install-az-ps).

## <a name="review-the-template"></a>Şablonu gözden geçirme

Aşağıdaki şablon, bir e-posta hedefi olan bir eylem grubu oluşturur ve hedef abonelik için tüm hizmet durumu bildirimlerine izin vermez. Bu şablonu *CreateAdvisorAlert.js* olarak kaydedin.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "actionGroups_name": {
      "defaultValue": "advisorAlert",
      "type": "string"
    },
    "activityLogAlerts_name": {
      "defaultValue": "AdvisorAlertsTest",
      "type": "string"
    },
    "emailAddress": {
      "defaultValue": "<email address>",
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
      "comments": "Azure Advisor Activity Log Alert",
      "type": "microsoft.insights/activityLogAlerts",
      "apiVersion": "2017-04-01",
      "name": "[parameters('activityLogAlerts_name')]",
      "location": "Global",
      "scale": null,
      "tags": {},
      "properties": {
        "scopes": [
          "[variables('alertScope')]"
        ],
        "condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "Recommendation"
            },
            {
              "field": "properties.recommendationCategory",
              "equals": "Cost"
            },
            {
              "field": "properties.recommendationImpact",
              "equals": "Medium"
            },
            {
              "field": "operationName",
              "equals": "Microsoft.Advisor/recommendations/available/action"
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
      },
      "dependsOn": [
        "[resourceId('microsoft.insights/actionGroups', parameters('actionGroups_name'))]"
      ]
    }
  ]
}
```

Şablon iki kaynağı tanımlar:

- [Microsoft. Insights/actionGroups](/azure/templates/microsoft.insights/actiongroups)
- [Microsoft. Insights/activityLogAlerts](/azure/templates/microsoft.insights/activityLogAlerts)

## <a name="deploy-the-template"></a>Şablonu dağıtma

CLı ve PowerShell kullanarak aşağıdaki örnekler gibi [BIR ARM şablonunu dağıtmak](../azure-resource-manager/templates/deploy-portal.md) için herhangi bir standart yöntemi kullanarak şablonu dağıtın. **Kaynak grubu** için örnek değerleri ve **emadresi** , ortamınız için uygun değerlerle değiştirin. Çalışma alanı adı tüm Azure abonelikleri arasında benzersiz olmalıdır.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az login
az deployment group create --name CreateAdvisorAlert --resource-group my-resource-group --template-file CreateAdvisorAlert.json --parameters emailAddress='user@contoso.com'
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName my-subscription
New-AzResourceGroupDeployment -Name CreateAdvisorAlert -ResourceGroupName my-resource-group -TemplateFile CreateAdvisorAlert.json -emailAddress user@contoso.com
```

---

## <a name="validate-the-deployment"></a>Dağıtımı doğrulama

Çalışma alanının, aşağıdaki komutlardan birini kullanarak oluşturulduğunu doğrulayın. **Kaynak grubu** için örnek değerleri yukarıda kullandığınız değerle değiştirin.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az monitor activity-log alert show --resource-group my-resource-group --name AdvisorAlertsTest
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
Get-AzActivityLogAlert -ResourceGroupName my-resource-group -Name AdvisorAlertsTest
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

- [Etkinlik günlüğü uyarılarına genel bir bakış](../azure-monitor/alerts/alerts-overview.md)elde edin ve uyarıları alma hakkında bilgi edinin.
- [Eylem grupları](../azure-monitor/alerts/action-groups.md)hakkında daha fazla bilgi edinin.
