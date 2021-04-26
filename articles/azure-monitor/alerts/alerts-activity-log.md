---
title: Azure Izleyici 'de etkinlik günlüğü uyarıları oluşturma, görüntüleme ve yönetme
description: Azure portal, bir Azure Resource Manager şablonu ve Azure PowerShell kullanarak etkinlik günlüğü uyarıları oluşturun.
ms.topic: conceptual
ms.date: 06/25/2019
ms.openlocfilehash: 647378d7e93ab383441b363315a84cea8a5ab773
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107772550"
---
# <a name="create-view-and-manage-activity-log-alerts-by-using-azure-monitor"></a>Azure Izleyici 'yi kullanarak etkinlik günlüğü uyarıları oluşturma, görüntüleme ve yönetme  

## <a name="overview"></a>Genel Bakış

Etkinlik günlüğü uyarıları, uyarıda belirtilen koşullara uyan yeni bir etkinlik günlüğü olayı gerçekleştiğinde etkinleştirilen uyarılardır.

Bu uyarılar Azure kaynaklarına yöneliktir ve bir Azure Resource Manager şablonu kullanılarak oluşturulabilir. Ayrıca, Azure portal oluşturulabilir, güncelleştirilemeyebilir veya silinebilirler. Genellikle, Azure aboneliğinizdeki kaynaklarda belirli değişiklikler gerçekleştiğinde bildirim almak için etkinlik günlüğü uyarıları oluşturursunuz. Uyarılar genellikle belirli kaynak grupları veya kaynakları kapsamlandırılır. Örneğin, **Myüretim resourcegroup** örnek kaynak grubundaki herhangi bir sanal makine silindiğinde bildirim almak isteyebilirsiniz. Ya da aboneliğinizdeki bir kullanıcıya herhangi bir yeni rol atanmışsa bildirim almak isteyebilirsiniz.

> [!IMPORTANT]
> Hizmet durumu bildirimi uyarıları, etkinlik günlüğü uyarısı oluşturma arabirimi aracılığıyla oluşturulamıyor. Hizmet durumu bildirimlerini oluşturma ve kullanma hakkında daha fazla bilgi edinmek için bkz. [hizmet durumu bildirimlerinde etkinlik günlüğü uyarıları alma](../../service-health/alerts-activity-log-service-notifications-portal.md).

Uyarı kuralları oluştururken, aşağıdakilerden emin olun:

- Kapsamdaki abonelik, uyarının oluşturulduğu abonelikten farklı değildir.
- Ölçütler, uyarının yapılandırıldığı düzey, durum, arayan, kaynak grubu, kaynak KIMLIĞI veya kaynak türü olay kategorisi olmalıdır.
- Yalnızca bir "allOf" koşuluna izin verilir.
- Birden çok alan üzerinde birden fazla koşula izin vermek için ' AnyOf ' kullanılabilir (örneğin, "durum" ya da "alt durumlar" alanları belirli bir değere eşitse). ' AnyOf ' öğesinin kullanımı, bir ARM şablon dağıtımı kullanarak uyarı kuralının oluşturulmasıyla sınırlı olduğunu unutmayın.
- ' ContainsAny ' aynı alanın birden fazla değerine izin vermek için kullanılabilir (örneğin, "işlem" ' DELETE ' veya ' Değiştir ' değerine eşitse). ' ContainsAny ' kullanılması, bir ARM şablon dağıtımı kullanarak uyarı kuralının oluşturulmasıyla sınırlı olduğunu unutmayın.
- Kategori "Yönetici" olduğunda, uyarısında yukarıdaki ölçütlerden en az birini belirtmeniz gerekir. Etkinlik günlüklerinde her olay oluşturulduğunda etkinleştiren bir uyarı oluşturmeyebilirsiniz.
- Etkinlik günlüğü uyarı kategorisinde olaylar için uyarılar oluşturulamıyor.

## <a name="azure-portal"></a>Azure portalı

Etkinlik günlüğü uyarı kuralları oluşturmak ve değiştirmek için Azure portal kullanabilirsiniz. Bu deneyim, ilgilendiğiniz belirli olaylar için sorunsuz uyarı oluşturma sağlamak üzere bir Azure etkinlik günlüğü ile tümleşiktir.

### <a name="create-with-the-azure-portal"></a>Azure portal ile oluşturun

Aşağıdaki yordamı kullanın.

1. Azure Portal **izleme**  >  **uyarıları**' nı seçin.
2. **Uyarılar** penceresinin sol üst köşesindeki **Yeni uyarı kuralı** ' nı seçin.

     ![Yeni uyarı kuralı](media/alerts-activity-log/AlertsPreviewOption.png)

     **Kural oluştur** penceresi görüntülenir.

      ![Yeni uyarı kuralı seçenekleri](media/alerts-activity-log/create-new-alert-rule-options.png)

3. **Uyarı koşulunu tanımla** altında, aşağıdaki bilgileri sağlayın ve **bitti**' yi seçin:

   - **Uyarı hedefi:** Yeni uyarının hedefini görüntülemek ve seçmek için,   /  **kaynak türüne göre abonelik filtresi filtresini** kullanın. Görünen listeden kaynağı veya kaynak grubunu seçin.

     > [!NOTE]
     > 
     > Yalnızca izlenen kaynak, kaynak grubu veya bir etkinlik günlüğü sinyali için tüm aboneliğin [Azure Resource Manager](../../azure-resource-manager/management/overview.md) seçebilirsiniz. 

     **Uyarı hedefi örnek görünümü**

     ![Hedef seçin](media/alerts-activity-log/select-target.png)

   - **Hedef ölçütleri** altında **Ölçüt Ekle**' yi seçin. Hedefe yönelik tüm kullanılabilir sinyaller görüntülenir ve bu, çeşitli **etkinlik günlüğü** kategorilerinden bunları içerir. Kategori adı, **Izleme hizmeti** adına eklenir.

   - Tür **etkinlik günlüğü** için mümkün olan çeşitli işlemlerin gösterildiği listeden sinyal seçin.

     Bu hedef sinyal için günlük geçmişi zaman çizelgesini ve ilgili uyarı mantığını seçebilirsiniz:

     **Ölçüt ekleme ekranı**

     ![Ölçüt Ekle](media/alerts-activity-log/add-criteria.png)
     
     > [!NOTE]
     > 
     >  Yüksek kaliteli ve etkili kurallara sahip olmak için kurallara "tüm yönetim" sinyaliyle en az bir koşul daha ekleyeceğiz. 
     > Uyarının tanımının bir parçası olarak, açılan kutudan birini doldurmanız gerekir: "olay düzeyi", "durum" veya "tarafından başlatılan" ve kural daha belirgin olacaktır.

     - **Geçmiş zamanı**: Seçili işlem için kullanılabilir olaylar son 6, 12 veya 24 saat veya geçen haftaya göre çizilebilirler.

     - **Uyarı mantığı**:

       - **Olay düzeyi**: etkinliğin önem derecesi: _ayrıntılı_, _bilgilendirici_, _Uyarı_, _hata_ veya _kritik_.
       - **Durum**: olay durumu: _başlatıldı_, _başarısız_ veya _başarılı_.
       - **Olay tarafından başlatılan olay**: çağıran olarak da bilinir. İşlemi gerçekleştiren kullanıcının e-posta adresi veya Azure Active Directory tanımlayıcısı.

       Bu örnek sinyal grafiğinde uygulanan uyarı mantığı vardır:

       ![Ölçüt seçildi](media/alerts-activity-log/criteria-selected.png)

4. **Uyarı ayrıntılarını tanımla** altında aşağıdaki ayrıntıları sağlayın:

    - **Uyarı kuralı adı**: yeni uyarı kuralının adı.
    - **Açıklama**: yeni uyarı kuralının açıklaması.
    - **Uyarıyı kaynak grubuna kaydet**: Bu yeni kuralı kaydetmek istediğiniz kaynak grubunu seçin.

5. **Eylem grubu** altında, açılan menüden, bu yeni uyarı kuralına atamak istediğiniz eylem grubunu belirtin. Ya da [Yeni bir eylem grubu oluşturun](./action-groups.md) ve yeni kurala atayın. Yeni bir grup oluşturmak için **+ Yeni Grup**' u seçin.

6. Kuralları oluşturduktan sonra etkinleştirmek için, **oluşturma sırasında kuralı etkinleştir** **seçeneğini belirleyin.**
7. **Uyarı kuralı oluşturma**’yı seçin.

    Etkinlik günlüğü için yeni uyarı kuralı oluşturulur ve pencerenin sağ üst köşesinde bir onay iletisi görüntülenir.

    Bir kuralı etkinleştirebilir, devre dışı bırakabilir, düzenleyebilir veya silebilirsiniz. Etkinlik günlüğü kurallarını yönetme hakkında daha fazla bilgi edinin.


Bir etkinlik günlüğünde hangi uyarı kurallarının oluşturulabileceği koşullarını anlamak için basit bir benzerleme vurguladı, [Azure Portal Etkinlik günlüğü](../essentials/activity-log.md#view-the-activity-log)aracılığıyla olayları keşfetmeye veya filtrelemeye yöneliktir. **Azure izleyici-etkinlik günlüğü** ekranında, gerekli olayı filtreleyebilir veya bulabilir ve ardından **etkinlik günlüğü uyarısı Ekle** düğmesini kullanarak bir uyarı oluşturabilirsiniz. Ardından, daha önce gösterilen 4 ile 7 arasındaki adımları uygulayın.
    
 ![Etkinlik günlüğünden uyarı Ekle](media/alerts-activity-log/add-activity-log.png)
    

### <a name="view-and-manage-in-the-azure-portal"></a>Azure portal görüntüleyin ve yönetin

1. Azure Portal **izleme**  >  **uyarıları**' nı seçin. Pencerenin sol üst köşesindeki **Uyarı kurallarını yönet** ' i seçin.

    ![Ekran görüntüsü, arama kutusuyla vurgulanan etkinlik günlüğünü gösterir.](media/alerts-activity-log/manage-alert-rules.png)

    Kullanılabilir kuralların listesi görüntülenir.

2. Değiştirilecek etkinlik günlüğü kuralını arayın.

    ![Etkinlik günlüğü uyarı kurallarında ara](media/alerts-activity-log/searth-activity-log-rule-to-edit.png)

    Düzenlemek istediğiniz etkinlik kuralını bulmak için kullanılabilir filtreler, _abonelik_, _kaynak grubu_,  _kaynak_, _sinyal türü_ veya _durum_' u kullanabilirsiniz.

   > [!NOTE]
   > 
   > Yalnızca **açıklamayı**, **hedef ölçütlerini** ve **eylem gruplarını** düzenleyebilirsiniz.

3. Kuralı seçin ve kural seçeneklerini düzenlemek için çift tıklayın. Gerekli değişiklikleri yapıp **Kaydet**' i seçin.

   ![Uyarı kurallarını yönetin](media/alerts-activity-log/activity-log-rule-edit-page.png)

4. Bir kuralı etkinleştirebilir, devre dışı bırakabilir veya silebilirsiniz. 2. adım bölümünde açıklandığı gibi kuralı seçtikten sonra pencerenin üst kısmında uygun seçeneği belirleyin.


## <a name="azure-resource-manager-template"></a>Azure Resource Manager şablonu
Bir Azure Resource Manager şablonu kullanarak bir etkinlik günlüğü uyarı kuralı oluşturmak için, türünde bir kaynak oluşturursunuz `microsoft.insights/activityLogAlerts` . Ardından ilgili tüm özellikleri doldurursunuz. Etkinlik günlüğü uyarı kuralı oluşturan bir şablon aşağıda verilmiştir:

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
Önceki örnek JSON, örneğin Bu izlenecek yol için sampleActivityLogAlert.jsve [Azure portal Azure Resource Manager](../../azure-resource-manager/templates/deploy-portal.md)kullanılarak dağıtılabilecek şekilde kaydedilebilir.

  > [!NOTE]
  > 
  > En üst düzey etkinlik günlüğü uyarılarının tanımlandığına dikkat edin.
  > Yani, birkaç abonelikte uyarı tanımlama seçeneği yoktur, bu nedenle tanım abonelik başına uyarı olmalıdır.

Aşağıdaki alanlar, koşullar alanları için Azure Resource Manager şablonunda kullanabileceğiniz seçeneklerdir: "Kaynak Durumu", "Advisor" ve "Service Health" seçeneklerinin özel alanları için ek özellikler alanları olduğunu unutmayın. 
1. RESOURCEID: uyarının oluşturulması gereken etkinlik günlüğü olayında etkilenen kaynağın kaynak KIMLIĞI.
2. Kategori: etkinlik günlüğü olayında kategori. Örneğin: yönetim, ServiceHealth, ResourceHealth, otomatik ölçeklendirme, güvenlik, öneri, Ilke.
3. arayan: etkinlik günlüğü olayının işlemini gerçekleştiren kullanıcının e-posta adresi veya Azure Active Directory tanımlayıcısı.
4. düzey: etkinliğin oluşturulması gereken etkinlik günlüğü olayında etkinliğin düzeyi. Örneğin: kritik, hata, uyarı, bilgilendirici, ayrıntılı.
5. operationName: etkinlik günlüğü olaydaki işlemin adı. Örneğin: Microsoft. resources/dağıtımlar/Write
6. resourceGroup: etkinlik günlüğü olayında etkilenen kaynağın kaynak grubunun adı.
7. resourceProvider: [Azure kaynak sağlayıcıları ve türleri açıklaması](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fazure-resource-manager%2Fmanagement%2Fresource-providers-and-types&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C90b7c2308c0647c0347908d7c9a2918d%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637199572373543634&sdata=4RjpTkO5jsdOgPdt%2F%2FDOlYjIFE2%2B%2BuoHq5%2F7lHpCwQw%3D&reserved=0). Kaynak sağlayıcılarını Azure hizmetleriyle eşleyen bir liste için bkz. [Azure hizmetleri Için kaynak sağlayıcıları](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fazure-resource-manager%2Fmanagement%2Fazure-services-resource-providers&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C90b7c2308c0647c0347908d7c9a2918d%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637199572373553639&sdata=0ZgJPK7BYuJsRifBKFytqphMOxMrkfkEwDqgVH1g8lw%3D&reserved=0).
8. durum: etkinlik olaydaki işlemin durumunu açıklayan dize. Örneğin: başlatıldı, devam ediyor, başarılı, başarısız, etkin, çözümlenmiş
9. alt durum: genellikle karşılık gelen REST çağrısının HTTP durum kodudur, ancak alt durumu tanımlayan diğer dizeleri de içerebilir.   Örneğin: Tamam (HTTP durum kodu: 200), oluşturulan (HTTP durum kodu: 201), kabul edildi (HTTP durum kodu: 202), Içerik yok (HTTP durum kodu: 204), hatalı Istek (http durum kodu: 400), bulunamadı (http durum kodu: 404), çakışma (HTTP durum kodu: 409), Iç sunucu hatası (http durum kodu: 500), hizmet kullanılamıyor (http durum kodu: 503), ağ geçidi zaman aşımı (http durum kodu: 504).
10. resourceType: olaydan etkilenen kaynağın türü. Örneğin: Microsoft. resources/dağıtımlar

Örnek:

```json
"condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "Administrative"
            },
            {
              "field": "resourceType",
              "equals": "Microsoft.Resources/deployments"
            }
          ]
        }

```
[Burada](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fazure-monitor%2Fplatform%2Factivity-log-schema&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C90b7c2308c0647c0347908d7c9a2918d%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637199572373563632&sdata=6QXLswwZgUHFXCuF%2FgOSowLzA8iOALVgvL3GMVhkYJY%3D&reserved=0)bulabileceğiniz etkinlik günlüğü alanları hakkında daha fazla bilgi edinebilirsiniz.



> [!NOTE]
> Yeni etkinlik günlüğü uyarı kuralının etkin olması 5 dakikaya kadar sürebilir.

## <a name="rest-api"></a>REST API 
[Azure Izleyici etkinlik günlüğü uyarıları API 'si](/rest/api/monitor/activitylogalerts) bir REST API. Azure Resource Manager REST API tam uyumludur. PowerShell aracılığıyla Kaynak Yöneticisi cmdlet 'i veya Azure CLı kullanılarak kullanılabilir.

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

### <a name="deploy-the-resource-manager-template-with-powershell"></a>Kaynak Yöneticisi şablonunu PowerShell ile dağıtma
Önceki [Azure Resource Manager şablonu](#azure-resource-manager-template) bölümünde gösterilen örnek kaynak yöneticisi şablonunu dağıtmak üzere PowerShell 'i kullanmak için şu komutu kullanın:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile sampleActivityLogAlert.json -TemplateParameterFile sampleActivityLogAlert.parameters.json
```

Burada sampleActivityLogAlert.parameters.js, uyarı kuralı oluşturmak için gereken parametreler için belirtilen değerleri içerir.

### <a name="use-activity-log-powershell-cmdlets"></a>Etkinlik günlüğü PowerShell cmdlet 'lerini kullanma

Etkinlik günlüğü uyarıları için kullanılabilir adanmış PowerShell cmdlet 'leri vardır:

- [Set-AzActivityLogAlert](/powershell/module/az.monitor/set-azactivitylogalert): yeni bir etkinlik günlüğü uyarısı oluşturur veya var olan bir etkinlik günlüğü uyarısını güncelleştirir.
- [Get-AzActivityLogAlert](/powershell/module/az.monitor/get-azactivitylogalert): bir veya daha fazla etkinlik günlüğü uyarı kaynağını alır.
- [Enable-AzActivityLogAlert](/powershell/module/az.monitor/enable-azactivitylogalert): var olan bir etkinlik günlüğü uyarısını etkinleştirir ve etiketlerini ayarlar.
- [Disable-AzActivityLogAlert](/powershell/module/az.monitor/disable-azactivitylogalert): var olan bir etkinlik günlüğü uyarısını devre dışı bırakır ve etiketlerini ayarlar.
- [Remove-AzActivityLogAlert](/powershell/module/az.monitor/remove-azactivitylogalert): bir etkinlik günlüğü uyarısını kaldırır.

## <a name="azure-cli"></a>Azure CLI

Ayarla [az Monitor Activity-Log Alert](/cli/azure/monitor/activity-log/alert) altında ADANMıŞ Azure CLI komutları, etkinlik günlüğü uyarı kurallarını yönetmek için kullanılabilir.

Yeni bir etkinlik günlüğü uyarı kuralı oluşturmak için bu sırayla aşağıdaki komutları kullanın:

1. [az Monitor etkinlik-günlük uyarısı oluştur](/cli/azure/monitor/activity-log/alert#az_monitor_activity_log_alert_create): yeni bir etkinlik günlüğü uyarı kuralı kaynağı oluşturun.
1. [az Monitor Activity-Log uyarı Scope](/cli/azure/monitor/activity-log/alert/scope): oluşturulan etkinlik günlüğü uyarı kuralı için kapsam ekleyin.
1. [az Monitor etkinlik-günlük Uyarı eylemi-grup](/cli/azure/monitor/activity-log/alert/action-group): etkinlik günlüğü uyarı kuralına bir eylem grubu ekleyin.

Bir etkinlik günlüğü uyarı kuralı kaynağını almak için [az Monitor Activity-Log uyarı Show](/cli/azure/monitor/activity-log/alert#az_monitor_activity_log_alert_show
)Azure CLI komutunu kullanın. Bir kaynak grubundaki tüm etkinlik günlüğü uyarı kuralı kaynaklarını görüntülemek için [az Monitor Activity-Log uyarı List](/cli/azure/monitor/activity-log/alert#az_monitor_activity_log_alert_list)' i kullanın.
Etkinlik günlüğü uyarı kuralı kaynakları Azure CLı komutu kullanılarak kaldırılabilir, [az Monitor Activity-Log Alert Delete](/cli/azure/monitor/activity-log/alert#az_monitor_activity_log_alert_delete).

## <a name="next-steps"></a>Sonraki adımlar

- [Etkinlik günlükleri için Web kancası şeması](./activity-log-alerts-webhook.md)hakkında bilgi edinin.
- [Etkinlik günlüklerine genel bakış](./activity-log-alerts.md)konusunu okuyun.
- [Eylem grupları](./action-groups.md)hakkında daha fazla bilgi edinin.  
- [Hizmet durumu bildirimleri](../../service-health/service-notifications.md)hakkında bilgi edinin.
