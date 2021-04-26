---
title: Değişiklikleri engellemek için kaynakları kilitle
description: Kullanıcıların tüm kullanıcılar ve roller için bir kilit uygulayarak Azure kaynaklarını güncelleştirmesini ve silmesini engelleyin.
ms.topic: conceptual
ms.date: 04/07/2021
ms.custom: devx-track-azurecli
ms.openlocfilehash: 71637318a60e66bf5000de2f564d740cc101cc60
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768732"
---
# <a name="lock-resources-to-prevent-unexpected-changes"></a>Beklenmeyen değişiklikleri önlemek için kaynakları kilitleme

Yönetici olarak, kuruluşunuzdaki diğer kullanıcıların yanlışlıkla önemli kaynakları silmesini veya değiştirmesini engellemek için bir abonelik, kaynak grubu veya kaynağı kilitleyebilmeniz gerekir. Kilit, kullanıcının sahip olabileceği tüm izinleri geçersiz kılar.

Kilit düzeyini **CanNotDelete** veya **ReadOnly** olarak ayarlayabilirsiniz. Portalda, kilitler sırasıyla **silme** ve **salt okuma** olarak adlandırılır.

* **Cannotdelete** , yetkili kullanıcıların bir kaynağı hala okuyabilecekleri ve değiştirebilecekleri anlamına gelir, ancak kaynakları silemez.
* **ReadOnly** , yetkili kullanıcıların bir kaynağı okuyabilecekleri anlamına gelir, ancak kaynakları silemez veya güncelleştiremez. Bu kilidi uygulamak, tüm yetkili kullanıcıları **okuyucu** rolü tarafından verilen izinlerle kısıtlamak için benzerdir.

## <a name="how-locks-are-applied"></a>Kilitlerin uygulanma şekli

Üst kapsamda bir kilit uyguladığınızda, bu kapsamdaki tüm kaynaklar aynı kilidi alır. Daha sonra eklediğiniz kaynaklar bile kilidi üst öğeden alır. Devralmada en kısıtlayıcı kilit öncelik kazanır.

Rol tabanlı erişim denetiminin aksine, yönetim kilitlerini tüm kullanıcılar ve rollere kısıtlama getirmek için kullanırsınız. Kullanıcılar ve roller için izinleri ayarlama hakkında bilgi edinmek için bkz. [Azure rol tabanlı erişim denetimi (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md).

Resource Manager kilitleri yalnızca yönetim düzleminde gerçekleşen ve `https://management.azure.com` adresine gönderilen işlemlere uygulanır. Kilitler kaynakların kendi işlevlerini gerçekleştirmesine bir kısıtlama getirmez. Kaynak değişiklikleri kısıtlanır ama kaynak işlemleri kısıtlanmaz. Örneğin, bir SQL veritabanı mantıksal sunucusu üzerinde salt okunur bir kilit, sunucuyu silmenizi veya değiştirmenizi önler. Bu, sunucusundaki veritabanlarında veri oluşturmanızı, güncelleştirmenizi veya silmenizi engellemez. Bu işlemler `https://management.azure.com` hedefine gönderilmediğinden veri işlemlerine izin verilir.

## <a name="considerations-before-applying-locks"></a>Kilitleri uygulamadan önce dikkat edilecek noktalar

Kilitleri uygulamak, kaynağı değiştirmeyen bazı işlemler gerçekten kilit tarafından engellenen eylemler gerektirdiğinden beklenmedik sonuçlara neden olabilir. Kilitler Azure Resource Manager API 'sine POST isteği gerektiren tüm işlemleri engeller. Kilitlerle engellenen işlemlerin bazı yaygın örnekleri şunlardır:

* **Depolama hesabında** salt okunurdur bir kilit, kullanıcıların hesap anahtarlarını listelemesine engel olur. Azure depolama [listesi anahtarları](/rest/api/storagerp/storageaccounts/listkeys) işlemi, depolama hesabındaki verilere yönelik tüm erişimleri sağlayan hesap anahtarlarına erişimi korumak IÇIN bir post isteği aracılığıyla işlenir. Bir depolama hesabı için salt okunurdur bir kilit yapılandırıldığında, hesap anahtarlarına sahip olmayan kullanıcıların blob veya kuyruk verilerine erişmek için Azure AD kimlik bilgilerini kullanması gerekir. Salt okunurdur bir kilit, depolama hesabı kapsamındaki Azure RBAC rollerinin atanmasını veya bir veri kapsayıcısını (blob kapsayıcısı ya da kuyruğu) engeller.

* Bir **depolama hesabındaki** bir Delete kilidi, bu hesabın içindeki verilerin silinmesini veya değiştirilmesini engellemez. Bu tür kilit yalnızca depolama hesabının silinmesini önler ve bu depolama hesabı içindeki blob, kuyruk, tablo veya dosya verilerini korumaz. 

* Bir **depolama hesabındaki** salt okuma kilidi, söz konusu hesabın içindeki verilerin silinmesini veya değiştirilmesini engellemez. Bu tür kilit yalnızca depolama hesabının silinmesini veya değiştirilmesini önler ve bu depolama hesabı içindeki blob, kuyruk, tablo veya dosya verilerini korumaz. 

* Bir **App Service** kaynağı üzerinde salt okunurdur bir kilit, bu etkileşim yazma erişimi gerektirdiğinden Visual Studio Sunucu Gezgini kaynak için dosya görüntülemesini engeller.

* Bir **App Service planı** içeren bir **kaynak grubundaki** salt okunurdur bir kilit [, planı ölçeklendirmeden veya bir şekilde ölçeklendirmanıza](../../app-service/manage-scale-up.md)engel olur.

* Bir **sanal makine** içeren bir **kaynak grubundaki** salt okunurdur bir kilit, tüm kullanıcıların sanal makineyi başlatmasını veya yeniden başlatmasını önler. Bu işlemler bir POST isteği gerektirir.

* Bir **kaynak grubundaki** bir Delete kilidi, Azure Resource Manager [otomatik olarak geçmiş dağıtımlar silmesini](../templates/deployment-history-deletions.md) engeller. Geçmişte 800 dağıtıma ulaşırsanız, dağıtımlarınız başarısız olur.

* **Azure Backup hizmeti** tarafından oluşturulan **kaynak grubundaki** bir Delete kilidi, yedeklemelerin başarısız olmasına neden olur. Hizmet en fazla 18 geri yükleme noktasını destekler. Kilitlendiğinde, yedekleme hizmeti geri yükleme noktalarını temizleyemiyor. Daha fazla bilgi için bkz. [sık sorulan sorular-Azure sanal makinelerini yedekleme](../../backup/backup-azure-vm-backup-faq.yml).

* Bir **abonelikte** salt okunurdur bir kilit **Azure Advisor** 'ın düzgün çalışmasını engeller. Danışman, sorgularının sonuçlarını depolayamadı.

## <a name="who-can-create-or-delete-locks"></a>Kilitleri kimler oluşturabilir veya silebilir

Yönetim kilitleri oluşturmak veya silmek için, veya eylemlere erişiminizin olması gerekir `Microsoft.Authorization/*` `Microsoft.Authorization/locks/*` . Yerleşik rollerden yalnızca **Sahip** ve **Kullanııcı Erişiimi Yöneticisi** bu eylemleri kullanabilir.

## <a name="managed-applications-and-locks"></a>Yönetilen uygulamalar ve kilitler

Azure Databricks gibi bazı Azure Hizmetleri, hizmeti uygulamak için [yönetilen uygulamalar](../managed-applications/overview.md) kullanır. Bu durumda, hizmet iki kaynak grubu oluşturur. Bir kaynak grubu, hizmete genel bir bakış içerir ve kilitlenmez. Diğer kaynak grubu, hizmet altyapısını içerir ve kilitlidir.

Altyapı kaynak grubunu silmeye çalışırsanız, kaynak grubunun kilitli olduğunu belirten bir hata alırsınız. Altyapı kaynak grubu için kilidi silmeye çalışırsanız, kilidin bir sistem uygulamasına ait olduğundan, kilitlenmekte olduğunu belirten bir hata alırsınız.

Bunun yerine, altyapı kaynak grubunu da silen hizmeti silin.

Yönetilen uygulamalar için, dağıttığınız hizmeti seçin.

![Hizmet seçin](./media/lock-resources/select-service.png)

Hizmetin **yönetilen kaynak grubu** için bir bağlantı içerdiğine dikkat edin. Bu kaynak grubu altyapıyı barındırır ve kilitlidir. Doğrudan silinemez.

![Yönetilen grubu göster](./media/lock-resources/show-managed-group.png)

Kilitli altyapı kaynak grubu dahil olmak üzere hizmetin her şeyi silmek için, hizmet için **Sil** ' i seçin.

![Hizmeti Sil](./media/lock-resources/delete-service.png)

## <a name="configure-locks"></a>Kilitleri yapılandırma

### <a name="portal"></a>Portal

[!INCLUDE [resource-manager-lock-resources](../../../includes/resource-manager-lock-resources.md)]

### <a name="arm-template"></a>ARM şablonu

Kilit dağıtmak için bir Azure Resource Manager şablonu (ARM şablonu) kullanılırken, kilidin kapsamını ve dağıtımın kapsamını bilmeniz gerekir. Bir kaynak grubunu veya aboneliği kilitlemek gibi dağıtım kapsamında bir kilit uygulamak için kapsam özelliğini ayarlayın. Dağıtım kapsamındaki bir kaynağı kilitlerken Scope özelliğini ayarlayın.

Aşağıdaki şablon, dağıtıldığı kaynak grubuna bir kilit uygular. Kilit kapsamı, dağıtımın kapsamıyla eşleştiğinden kilit kaynağında bir Scope özelliği olmadığına dikkat edin. Bu şablon, kaynak grubu düzeyinde dağıtılır.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {  
    },
    "resources": [
        {
            "type": "Microsoft.Authorization/locks",
            "apiVersion": "2016-09-01",
            "name": "rgLock",
            "properties": {
                "level": "CanNotDelete",
                "notes": "Resource Group should not be deleted."
            }
        }
    ]
}
```

Bir kaynak grubu oluşturmak ve kilitlemek için, abonelik düzeyinde aşağıdaki şablonu dağıtın.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "rgName": {
            "type": "string"
        },
        "rgLocation": {
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Resources/resourceGroups",
            "apiVersion": "2019-10-01",
            "name": "[parameters('rgName')]",
            "location": "[parameters('rgLocation')]",
            "properties": {}
        },
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2020-06-01",
            "name": "lockDeployment",
            "resourceGroup": "[parameters('rgName')]",
            "dependsOn": [
                "[resourceId('Microsoft.Resources/resourceGroups/', parameters('rgName'))]"
            ],
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {},
                    "variables": {},
                    "resources": [
                        {
                            "type": "Microsoft.Authorization/locks",
                            "apiVersion": "2016-09-01",
                            "name": "rgLock",
                            "properties": {
                                "level": "CanNotDelete",
                                "notes": "Resource group and its resources should not be deleted."
                            }
                        }
                    ],
                    "outputs": {}
                }
            }
        }
    ],
    "outputs": {}
}
```

Kaynak grubu içindeki bir **kaynağa** bir kilit uygularken kapsam özelliğini ekleyin. Kapsamı, kilitlenecek kaynağın adı olarak ayarlayın.

Aşağıdaki örnek, Web sitesinde bir App Service planı, bir Web sitesi ve bir kilit oluşturan bir şablon gösterir. Kilidin kapsamı Web sitesine ayarlanır.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "hostingPlanName": {
      "type": "string"
    },
    "location": {
        "type": "string",
        "defaultValue": "[resourceGroup().location]"
    }
  },
  "variables": {
    "siteName": "[concat('ExampleSite', uniqueString(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Web/serverfarms",
      "apiVersion": "2020-06-01",
      "name": "[parameters('hostingPlanName')]",
      "location": "[parameters('location')]",
      "sku": {
        "tier": "Free",
        "name": "f1",
        "capacity": 0
      },
      "properties": {
        "targetWorkerCount": 1
      }
    },
    {
      "type": "Microsoft.Web/sites",
      "apiVersion": "2020-06-01",
      "name": "[variables('siteName')]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
      ],
      "properties": {
        "serverFarmId": "[parameters('hostingPlanName')]"
      }
    },
    {
      "type": "Microsoft.Authorization/locks",
      "apiVersion": "2016-09-01",
      "name": "siteLock",
      "scope": "[concat('Microsoft.Web/sites/', variables('siteName'))]",
      "dependsOn": [
        "[resourceId('Microsoft.Web/sites', variables('siteName'))]"
      ],
      "properties": {
        "level": "CanNotDelete",
        "notes": "Site should not be deleted."
      }
    }
  ]
}
```

### <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell ile dağıtılan kaynakları [New-AzResourceLock](/powershell/module/az.resources/new-azresourcelock) komutunu kullanarak kilitlersiniz.

Bir kaynağı kilitlemek için kaynağın adını, kaynak türünü ve kaynak grubu adını belirtin.

```azurepowershell-interactive
New-AzResourceLock -LockLevel CanNotDelete -LockName LockSite -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName exampleresourcegroup
```

Bir kaynak grubunu kilitlemek için kaynak grubunun adını sağlayın.

```azurepowershell-interactive
New-AzResourceLock -LockName LockGroup -LockLevel CanNotDelete -ResourceGroupName exampleresourcegroup
```

Bir kilit hakkında bilgi almak için [Get-AzResourceLock](/powershell/module/az.resources/get-azresourcelock)kullanın. Aboneliğinizdeki tüm kilitleri almak için şunu kullanın:

```azurepowershell-interactive
Get-AzResourceLock
```

Bir kaynağın tüm kilitlerini almak için şunu kullanın:

```azurepowershell-interactive
Get-AzResourceLock -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName exampleresourcegroup
```

Bir kaynak grubuna yönelik tüm kilitleri almak için şunu kullanın:

```azurepowershell-interactive
Get-AzResourceLock -ResourceGroupName exampleresourcegroup
```

Bir kaynağın kilidini silmek için şunu kullanın:

```azurepowershell-interactive
$lockId = (Get-AzResourceLock -ResourceGroupName exampleresourcegroup -ResourceName examplesite -ResourceType Microsoft.Web/sites).LockId
Remove-AzResourceLock -LockId $lockId
```

Bir kaynak grubunun kilidini silmek için şunu kullanın:

```azurepowershell-interactive
$lockId = (Get-AzResourceLock -ResourceGroupName exampleresourcegroup).LockId
Remove-AzResourceLock -LockId $lockId
```

### <a name="azure-cli"></a>Azure CLI

Dağıtılan kaynakları, [az Lock Create](/cli/azure/lock#az_lock_create) komutunu kullanarak Azure CLI ile kilitlersiniz.

Bir kaynağı kilitlemek için kaynağın adını, kaynak türünü ve kaynak grubu adını belirtin.

```azurecli
az lock create --name LockSite --lock-type CanNotDelete --resource-group exampleresourcegroup --resource-name examplesite --resource-type Microsoft.Web/sites
```

Bir kaynak grubunu kilitlemek için kaynak grubunun adını sağlayın.

```azurecli
az lock create --name LockGroup --lock-type CanNotDelete --resource-group exampleresourcegroup
```

Bir kilit hakkında bilgi almak için [az Lock List](/cli/azure/lock#az_lock_list)kullanın. Aboneliğinizdeki tüm kilitleri almak için şunu kullanın:

```azurecli
az lock list
```

Bir kaynağın tüm kilitlerini almak için şunu kullanın:

```azurecli
az lock list --resource-group exampleresourcegroup --resource-name examplesite --namespace Microsoft.Web --resource-type sites --parent ""
```

Bir kaynak grubuna yönelik tüm kilitleri almak için şunu kullanın:

```azurecli
az lock list --resource-group exampleresourcegroup
```

Bir kaynağın kilidini silmek için şunu kullanın:

```azurecli
lockid=$(az lock show --name LockSite --resource-group exampleresourcegroup --resource-type Microsoft.Web/sites --resource-name examplesite --output tsv --query id)
az lock delete --ids $lockid
```

Bir kaynak grubunun kilidini silmek için şunu kullanın:

```azurecli
lockid=$(az lock show --name LockSite --resource-group exampleresourcegroup  --output tsv --query id)
az lock delete --ids $lockid
```

### <a name="rest-api"></a>REST API

Dağıtılan kaynakları, [Yönetim kilitleri için REST API](/rest/api/resources/managementlocks)ile kilitleyebilin. REST API, kilitleri oluşturup silmenizi ve var olan kilitler hakkında bilgi almanızı sağlar.

Bir kilit oluşturmak için şunu çalıştırın:

```http
PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/locks/{lock-name}?api-version={api-version}
```

Kapsam bir abonelik, kaynak grubu veya kaynak olabilir. Kilit adı, kilidi çağırmak istediğiniz her şey olur. API sürümü için **2016-09-01** kullanın.

İstekte, kilidin özelliklerini belirten bir JSON nesnesi ekleyin.

```json
{
  "properties": {
  "level": "CanNotDelete",
  "notes": "Optional text notes."
  }
}
```

## <a name="next-steps"></a>Sonraki adımlar

* Kaynaklarınızı mantıksal olarak organize etme hakkında bilgi edinmek için bkz. [kaynakları düzenlemek için etiketleri kullanma](tag-resources.md).
* Özelleştirilmiş ilkelerle, aboneliğiniz genelinde kısıtlama ve kurallar uygulayabilirsiniz. Daha fazla bilgi için bkz. [Azure İlkesi nedir?](../../governance/policy/overview.md).
* Kuruluşların abonelikleri etkili bir şekilde yönetmek için Resource Manager'ı nasıl kullanabileceği hakkında yönergeler için bkz. [Azure kurumsal iskelesi: öngörücü abonelik idaresi](/azure/architecture/cloud-adoption-guide/subscription-governance).
