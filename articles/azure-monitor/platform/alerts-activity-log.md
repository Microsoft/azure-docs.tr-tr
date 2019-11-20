---
title: Azure Izleyici 'de etkinlik günlüğü uyarıları oluşturma, görüntüleme ve yönetme
description: Azure portal, bir Azure Resource Manager şablonu ve Azure PowerShell kullanarak etkinlik günlüğü uyarıları oluşturun.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: rboucher
ms.author: robb
ms.date: 06/25/2019
ms.openlocfilehash: 6c133f99ce31d4994753aba11cee90cfc1b5afc9
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72552807"
---
# <a name="create-view-and-manage-activity-log-alerts-by-using-azure-monitor"></a>Azure Izleyici 'yi kullanarak etkinlik günlüğü uyarıları oluşturma, görüntüleme ve yönetme  

## <a name="overview"></a>Genel Bakış
Etkinlik günlüğü uyarıları, uyarıda belirtilen koşullara uyan yeni bir etkinlik günlüğü olayı gerçekleştiğinde etkinleştirilen uyarılardır.

Bu uyarılar Azure kaynaklarına yöneliktir ve bir Azure Resource Manager şablonu kullanılarak oluşturulabilir. Ayrıca, Azure portal oluşturulabilir, güncelleştirilemeyebilir veya silinebilirler. Genellikle, Azure aboneliğinizdeki kaynaklarda belirli değişiklikler gerçekleştiğinde bildirim almak için etkinlik günlüğü uyarıları oluşturursunuz. Uyarılar genellikle belirli kaynak grupları veya kaynakları kapsamlandırılır. Örneğin, **Myüretim resourcegroup** örnek kaynak grubundaki herhangi bir sanal makine silindiğinde bildirim almak isteyebilirsiniz. Ya da aboneliğinizdeki bir kullanıcıya herhangi bir yeni rol atanmışsa bildirim almak isteyebilirsiniz.

> [!IMPORTANT]
> Hizmet durumu bildirimi uyarıları, etkinlik günlüğü uyarısı oluşturma arabirimi aracılığıyla oluşturulamıyor. Hizmet durumu bildirimlerini oluşturma ve kullanma hakkında daha fazla bilgi edinmek için bkz. [hizmet durumu bildirimlerinde etkinlik günlüğü uyarıları alma](alerts-activity-log-service-notifications.md).

Uyarı kuralları oluştururken, aşağıdakilerden emin olun:

- Kapsamdaki abonelik, uyarının oluşturulduğu abonelikten farklı değildir.
- Ölçütler, uyarının yapılandırıldığı düzey, durum, arayan, kaynak grubu, kaynak KIMLIĞI veya kaynak türü olay kategorisi olmalıdır.
- Uyarı yapılandırması JSON öğesinde "anyOf" koşulu veya iç içe geçmiş koşullar yoktur. Temel olarak, daha fazla "allOf" veya "anyOf" koşulları olmadan yalnızca bir "allOf" koşuluna izin verilir.
- Kategori "Yönetici" olduğunda, uyarısında yukarıdaki ölçütlerden en az birini belirtmeniz gerekir. Etkinlik günlüklerinde her olay oluşturulduğunda etkinleştiren bir uyarı oluşturmeyebilirsiniz.


## <a name="azure-portal"></a>Azure portalı

Etkinlik günlüğü uyarı kuralları oluşturmak ve değiştirmek için Azure portal kullanabilirsiniz. Bu deneyim, ilgilendiğiniz belirli olaylar için sorunsuz uyarı oluşturma sağlamak üzere bir Azure etkinlik günlüğü ile tümleşiktir.

### <a name="create-with-the-azure-portal"></a>Azure portal ile oluşturun

Aşağıdaki yordamı kullanın.

1. Azure portal **izleme**  > **uyarıları**' nı seçin.
2. **Uyarılar** penceresinin sol üst köşesindeki **Yeni uyarı kuralı** ' nı seçin.

     ![Yeni uyarı kuralı](media/alerts-activity-log/AlertsPreviewOption.png)

     **Kural oluştur** penceresi görüntülenir.

      ![Yeni uyarı kuralı seçenekleri](media/alerts-activity-log/create-new-alert-rule-options.png)

3. **Uyarı koşulunu tanımla**altında, aşağıdaki bilgileri sağlayın ve **bitti**' yi seçin:

   - **Uyarı hedefi:** Yeni uyarının hedefini görüntülemek ve seçmek için, **aboneliğe göre filtrele**  / **kaynak türüne göre filtrele**' yi kullanın. Görünen listeden kaynağı veya kaynak grubunu seçin.

     > [!NOTE]
     > 
     > Yalnızca izlenen kaynak, kaynak grubu veya bir etkinlik günlüğü sinyali için tüm aboneliğin [Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) seçebilirsiniz. 

     **Uyarı hedefi örnek görünümü**

     ![Hedef seçin](media/alerts-activity-log/select-target.png)

   - **Hedef ölçütleri**altında **Ölçüt Ekle**' yi seçin. Hedefe yönelik tüm kullanılabilir sinyaller görüntülenir ve bu, çeşitli **etkinlik günlüğü**kategorilerinden bunları içerir. Kategori adı, **Izleme hizmeti** adına eklenir.

   - Tür **etkinlik günlüğü**için mümkün olan çeşitli işlemlerin gösterildiği listeden sinyal seçin.

     Bu hedef sinyal için günlük geçmişi zaman çizelgesini ve ilgili uyarı mantığını seçebilirsiniz:

     **Ölçüt ekleme ekranı**

     ![Ölçüt Ekle](media/alerts-activity-log/add-criteria.png)

     - **Geçmiş zamanı**: Seçili işlem için kullanılabilir olaylar son 6, 12 veya 24 saat veya geçen haftaya göre çizilebilirler.

     - **Uyarı mantığı**:

       - **Olay düzeyi**: etkinliğin önem derecesi: _ayrıntılı_, _bilgilendirici_, _Uyarı_, _hata_veya _kritik_.
       - **Durum**: olay durumu: _başlatıldı_, _başarısız_veya _başarılı_.
       - **Olay tarafından başlatılan olay**: çağıran olarak da bilinir. İşlemi gerçekleştiren kullanıcının e-posta adresi veya Azure Active Directory tanımlayıcısı.

       Bu örnek sinyal grafiğinde uygulanan uyarı mantığı vardır:

       ![Ölçüt seçildi](media/alerts-activity-log/criteria-selected.png)

4. **Uyarı ayrıntılarını tanımla**altında aşağıdaki ayrıntıları sağlayın:

    - **Uyarı kuralı adı**: yeni uyarı kuralının adı.
    - **Açıklama**: yeni uyarı kuralının açıklaması.
    - **Uyarıyı kaynak grubuna kaydet**: Bu yeni kuralı kaydetmek istediğiniz kaynak grubunu seçin.

5. **Eylem grubu**altında, açılan menüden, bu yeni uyarı kuralına atamak istediğiniz eylem grubunu belirtin. Ya da [Yeni bir eylem grubu oluşturun](../../azure-monitor/platform/action-groups.md) ve yeni kurala atayın. Yeni bir grup oluşturmak için **+ Yeni Grup**' u seçin.

6. Kuralları oluşturduktan sonra etkinleştirmek için, **oluşturma sırasında kuralı etkinleştir** **seçeneğini belirleyin.**
7. **Uyarı kuralı oluştur**' u seçin.

    Etkinlik günlüğü için yeni uyarı kuralı oluşturulur ve pencerenin sağ üst köşesinde bir onay iletisi görüntülenir.

    Bir kuralı etkinleştirebilir, devre dışı bırakabilir, düzenleyebilir veya silebilirsiniz. Etkinlik günlüğü kurallarını yönetme hakkında daha fazla bilgi edinin.


Bir etkinlik günlüğünde hangi uyarı kurallarının oluşturulabileceği koşullarını anlamak için basit bir benzerleme vurguladı, [Azure Portal Etkinlik günlüğü](activity-log-view.md#azure-portal)aracılığıyla olayları keşfetmeye veya filtrelemeye yöneliktir. **Azure izleyici-etkinlik günlüğü** ekranında, gerekli olayı filtreleyebilir veya bulabilir ve ardından **etkinlik günlüğü uyarısı Ekle** düğmesini kullanarak bir uyarı oluşturabilirsiniz. Ardından, daha önce gösterilen 4 ile 7 arasındaki adımları uygulayın.
    
 ![Etkinlik günlüğünden uyarı Ekle](media/alerts-activity-log/add-activity-log.png)
    

### <a name="view-and-manage-in-the-azure-portal"></a>Azure portal görüntüleyin ve yönetin

1. Azure portal **izleme**  > **uyarıları**' nı seçin. Pencerenin sol üst köşesindeki **Uyarı kurallarını yönet** ' i seçin.

    ![Uyarı kurallarını yönet](media/alerts-activity-log/manage-alert-rules.png)

    Kullanılabilir kuralların listesi görüntülenir.

2. Değiştirilecek etkinlik günlüğü kuralını arayın.

    ![Etkinlik günlüğü uyarı kurallarında ara](media/alerts-activity-log/searth-activity-log-rule-to-edit.png)

    Düzenlemek istediğiniz etkinlik kuralını bulmak için kullanılabilir filtreler, _abonelik_, _kaynak grubu_, _kaynak_, _sinyal türü_veya _durum_' u kullanabilirsiniz.

   > [!NOTE]
   > 
   > Yalnızca **açıklamayı**, **hedef ölçütlerini**ve **eylem gruplarını**düzenleyebilirsiniz.

3. Kuralı seçin ve kural seçeneklerini düzenlemek için çift tıklayın. Gerekli değişiklikleri yapıp **Kaydet**' i seçin.

   ![Uyarı kurallarını yönet](media/alerts-activity-log/activity-log-rule-edit-page.png)

4. Bir kuralı etkinleştirebilir, devre dışı bırakabilir veya silebilirsiniz. 2\. adım bölümünde açıklandığı gibi kuralı seçtikten sonra pencerenin üst kısmında uygun seçeneği belirleyin.


## <a name="azure-resource-manager-template"></a>Azure Resource Manager şablonu
Bir Azure Resource Manager şablonu kullanarak bir etkinlik günlüğü uyarısı oluşturmak için `microsoft.insights/activityLogAlerts` türünde bir kaynak oluşturursunuz. Ardından ilgili tüm özellikleri doldurursunuz. Etkinlik günlüğü uyarısı oluşturan bir şablon aşağıda verilmiştir:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "activityLogAlertName": {
      "type": "string",
      "metadata": {
        "description": "Unique name (within the Resource Group) for the Activity log alert."
      }
    },
    "activityLogAlertEnabled": {
      "type": "bool",
      "defaultValue": true,
      "metadata": {
        "description": "Indicates whether or not the alert is enabled."
      }
    },
    "actionGroupResourceId": {
      "type": "string",
      "metadata": {
        "description": "Resource Id for the Action group."
      }
    }
  },
  "resources": [   
    {
      "type": "Microsoft.Insights/activityLogAlerts",
      "apiVersion": "2017-04-01",
      "name": "[parameters('activityLogAlertName')]",      
      "location": "Global",
      "properties": {
        "enabled": "[parameters('activityLogAlertEnabled')]",
        "scopes": [
            "[subscription().id]"
        ],        
        "condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "Administrative"
            },
            {
              "field": "operationName",
              "equals": "Microsoft.Resources/deployments/write"
            },
            {
              "field": "resourceType",
              "equals": "Microsoft.Resources/deployments"
            }
          ]
        },
        "actions": {
          "actionGroups":
          [
            {
              "actionGroupId": "[parameters('actionGroupResourceId')]"
            }
          ]
        }
      }
    }
  ]
}
```
Önceki örnek JSON, örneğin, Bu izlenecek yol için sampleActivityLogAlert. JSON gibi kaydedilebilir ve [Azure portal Azure Resource Manager](../../azure-resource-manager/resource-group-template-deploy-portal.md)kullanılarak dağıtılabilir.

> [!NOTE]
> Yeni etkinlik günlüğü uyarı kuralının etkin olması 5 dakikaya kadar sürebilir.

## <a name="rest-api"></a>REST API 
[Azure Izleyici etkinlik günlüğü uyarıları API 'si](https://docs.microsoft.com/rest/api/monitor/activitylogalerts) bir REST API. Azure Resource Manager REST API tam uyumludur. PowerShell aracılığıyla Kaynak Yöneticisi cmdlet 'i veya Azure CLı kullanılarak kullanılabilir.

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

### <a name="deploy-the-resource-manager-template-with-powershell"></a>Kaynak Yöneticisi şablonunu PowerShell ile dağıtma
Önceki [Azure Resource Manager şablonu](#azure-resource-manager-template) bölümünde gösterilen örnek kaynak yöneticisi şablonunu dağıtmak üzere PowerShell 'i kullanmak için şu komutu kullanın:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile sampleActivityLogAlert.json -TemplateParameterFile sampleActivityLogAlert.parameters.json
```

sampleActivityLogAlert. Parameters. JSON, uyarı kuralı oluşturma için gereken parametreler için belirtilen değerleri içerir.

### <a name="use-activity-log-powershell-cmdlets"></a>Etkinlik günlüğü PowerShell cmdlet 'lerini kullanma

Etkinlik günlüğü uyarıları için kullanılabilir adanmış PowerShell cmdlet 'leri vardır:

- [Set-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Set-AzActivityLogAlert): yeni bir etkinlik günlüğü uyarısı oluşturur veya var olan bir etkinlik günlüğü uyarısını güncelleştirir.
- [Get-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Get-AzActivityLogAlert): bir veya daha fazla etkinlik günlüğü uyarı kaynağını alır.
- [Enable-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Enable-AzActivityLogAlert): var olan bir etkinlik günlüğü uyarısını etkinleştirir ve etiketlerini ayarlar.
- [Disable-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Disable-AzActivityLogAlert): var olan bir etkinlik günlüğü uyarısını devre dışı bırakır ve etiketlerini ayarlar.
- [Remove-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Remove-AzActivityLogAlert): bir etkinlik günlüğü uyarısını kaldırır.

## <a name="azure-cli"></a>Azure CLI

Ayarla [az Monitor Activity-Log Alert](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert) altında ADANMıŞ Azure CLI komutları, etkinlik günlüğü uyarı kurallarını yönetmek için kullanılabilir.

Yeni bir etkinlik günlüğü uyarı kuralı oluşturmak için bu sırayla aşağıdaki komutları kullanın:

1. [az Monitor etkinlik-günlük uyarısı oluştur](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-create): yeni bir etkinlik günlüğü uyarı kuralı kaynağı oluşturun.
1. [az Monitor Activity-Log uyarı Scope](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert/scope): oluşturulan etkinlik günlüğü uyarı kuralı için kapsam ekleyin.
1. [az Monitor etkinlik-günlük Uyarı eylemi-grup](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert/action-group): etkinlik günlüğü uyarı kuralına bir eylem grubu ekleyin.

Bir etkinlik günlüğü uyarı kuralı kaynağını almak için [az Monitor Activity-Log uyarı Show](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-show
)Azure CLI komutunu kullanın. Bir kaynak grubundaki tüm etkinlik günlüğü uyarı kuralı kaynaklarını görüntülemek için [az Monitor Activity-Log uyarı List](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-list)' i kullanın.
Etkinlik günlüğü uyarı kuralı kaynakları Azure CLı komutu kullanılarak kaldırılabilir, [az Monitor Activity-Log Alert Delete](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-delete).

## <a name="next-steps"></a>Sonraki adımlar

- [Etkinlik günlükleri için Web kancası şeması](../../azure-monitor/platform/activity-log-alerts-webhook.md)hakkında bilgi edinin.
- [Etkinlik günlüklerine genel bakış](../../azure-monitor/platform/activity-log-alerts.md)konusunu okuyun.
- [Eylem grupları](../../azure-monitor/platform/action-groups.md)hakkında daha fazla bilgi edinin.  
- [Hizmet durumu bildirimleri](../../azure-monitor/platform/service-notifications.md)hakkında bilgi edinin.
