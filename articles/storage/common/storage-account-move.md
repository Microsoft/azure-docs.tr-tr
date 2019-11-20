---
title: Bir Azure Depolama hesabını başka bir bölgeye taşıma | Microsoft Docs
description: Bir Azure Depolama hesabını başka bir bölgeye nasıl taşıyabileceğinizi gösterir.
services: storage
author: normesta
ms.service: storage
ms.subservice: common
ms.topic: article
ms.date: 09/27/2019
ms.author: normesta
ms.reviewer: dineshm
ms.openlocfilehash: 8ce949ac997ba7ee38cb057752d89f4b4d22388f
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73838715"
---
# <a name="move-an-azure-storage-account-to-another-region"></a>Bir Azure Depolama hesabını başka bir bölgeye taşıma

Bir depolama hesabını taşımak için, başka bir bölgede depolama hesabınızın bir kopyasını oluşturun. Daha sonra, AzCopy veya seçtiğiniz başka bir aracı kullanarak verilerinizi bu hesaba taşıyın.

Bu makalede aşağıdakileri nasıl yapacağınızı öğreneceksiniz:

> [!div class="checklist"]
> 
> * Bir şablonu dışarı aktarın.
> * Hedef bölge ve depolama hesabı adını ekleyerek şablonu değiştirin.
> * Yeni depolama hesabı oluşturmak için şablonu dağıtın.
> * Yeni depolama hesabını yapılandırın.
> * Verileri yeni depolama hesabına taşıyın.
> * Kaynak bölgedeki kaynakları silin.

## <a name="prerequisites"></a>Ön koşullar

- Hesabınızın kullandığı hizmet ve özelliklerin hedef bölgede desteklendiğinden emin olun.

- Önizleme özellikleri için, aboneliğinizin hedef bölge için beyaz listeye kaydedildiğinden emin olun.

<a id="prepare" />

## <a name="prepare"></a>Hazırlama

Başlamak için Kaynak Yöneticisi şablonu dışarı aktarın ve değiştirin. 

### <a name="export-a-template"></a>Bir şablonu dışarı aktarma

Bu şablon, depolama hesabınızı tanımlayan ayarları içerir. 

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

Azure portal kullanarak bir şablonu dışarı aktarmak için:

1. [Azure portalında](https://portal.azure.com) oturum açın.

2. **Tüm kaynaklar** ' ı seçin ve ardından depolama hesabınızı seçin.

3. > **Ayarları** > **şablonu dışarı aktar**' ı seçin.

4. **Şablonu dışarı aktar** dikey penceresinde **İndir** ' i seçin.

5. Portaldan indirdiğiniz. zip dosyasını bulun ve bu dosyayı seçtiğiniz bir klasöre ayıklayın.

   Bu ZIP dosyası, şablonu dağıtmak için şablon ve betikleri oluşturan. JSON dosyalarını içerir.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Bir şablonu PowerShell kullanarak dışarı aktarmak için:

1. [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) komutuyla Azure aboneliğinizde oturum açın ve ekrandaki yönergeleri izleyin:

   ```azurepowershell-interactive
   Connect-AzAccount
   ```
2. Kimliğiniz birden fazla abonelikle ilişkiliyse, etkin aboneliğinizi taşımak istediğiniz depolama hesabının aboneliğine ayarlayın.

   ```azurepowershell-interactive
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

3. Kaynak depolama hesabınızın şablonunu dışarı aktarın. Bu komutlar geçerli dizininize bir JSON şablonu kaydeder.

   ```azurepowershell-interactive
   $resource = Get-AzResource `
     -ResourceGroupName <resource-group-name> `
     -ResourceName <storage-account-name> `
     -ResourceType Microsoft.Storage/storageAccounts
   Export-AzResourceGroup `
     -ResourceGroupName <resource-group-name> `
     -Resource $resource.ResourceId
   ```

---

### <a name="modify-the-template"></a>Şablonu değiştirme 

Depolama hesabı adını ve bölgesini değiştirerek şablonu değiştirin.

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

Azure portal kullanarak şablonu dağıtmak için:

1. Azure portalda **Kaynak oluştur**’u seçin.

2. **Market içinde ara** alanına **şablon dağıtımı** yazın ve **ENTER** tuşuna basın.

3. **Şablon dağıtımı**'nı seçin.

    ![Azure Resource Manager şablon kitaplığı](./media/storage-account-move/azure-resource-manager-template-library.png)

4. **Oluştur**'u seçin.

5. **Düzenleyicide kendi şablonunuzu oluşturun**'u seçin.

6. **Dosya Yükle**' yi seçin ve ardından son bölümde indirdiğiniz **Template. JSON** dosyasını yüklemek için yönergeleri izleyin.

7. **Template. JSON** dosyasında, depolama hesabı adının varsayılan değerini ayarlayarak hedef depolama hesabını adlandırın. Bu örnek, depolama hesabı adının varsayılan değerini `mytargetaccount`olarak ayarlar.
    
    ```json
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageAccounts_mysourceaccount_name": {
            "defaultValue": "mytargetaccount",
            "type": "String"
        }
    },
 
8. Edit the **location** property in the **template.json** file to the target region. This example sets the target region to `centralus`.

    ```json
    "resources": [{
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2019-04-01",
         "name": "[parameters('storageAccounts_mysourceaccount_name')]",
         "location": "centralus"
         }]          
    ```
    Bölge konum kodlarını almak için bkz. [Azure konumları](https://azure.microsoft.com/global-infrastructure/locations/).  Bölge için kod, boşluk içermeyen bölge adıdır, **Orta ABD** = **merkezileştirme**.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell kullanarak şablonu dağıtmak için:

1. **Template. JSON** dosyasında, depolama hesabı adının varsayılan değerini ayarlayarak hedef depolama hesabını adlandırın. Bu örnek, depolama hesabı adının varsayılan değerini `mytargetaccount`olarak ayarlar.
    
    ```json
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageAccounts_mysourceaccount_name": {
            "defaultValue": "mytargetaccount",
            "type": "String"
        }
    },
    ``` 

2. **Template. JSON** dosyasındaki **Location** özelliğini hedef bölgeye düzenleyin. Bu örnekte, hedef bölge `eastus`olarak ayarlanır.

    ```json
    "resources": [{
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2019-04-01",
         "name": "[parameters('storageAccounts_mysourceaccount_name')]",
         "location": "eastus"
         }]          
    ```

    [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) komutunu çalıştırarak bölge kodlarını elde edebilirsiniz.

    ```azurepowershell-interactive
    Get-AzLocation | format-table 
    ```
---

<a id="move" />

## <a name="move"></a>Taşı

Hedef bölgede yeni bir depolama hesabı oluşturmak için şablonu dağıtın. 

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

1. **Template. JSON** dosyasını kaydedin.

2. Özellik değerlerini girin veya seçin:

- **Abonelik**: Bir Azure aboneliği seçin.

- **Kaynak grubu**: **Yeni Oluştur**'u seçin ve kaynak grubuna bir ad verin.

- **Konum**: bir Azure konumu seçin.

3. **Yukarıda belirtilen hüküm ve koşulları kabul ediyorum** onay kutusuna tıklayın ve ardından **satın al düğmesini seçin** .

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

1. [Get-AzSubscription](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-2.5.0)ile hedef genel IP 'yi dağıtmak ISTEDIĞINIZ abonelik kimliğini edinin:

   ```azurepowershell-interactive
   Get-AzSubscription
   ```

2. Şablonunuzu dağıtmak için şu komutları kullanın:

   ```azurepowershell-interactive
   $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
   $location = Read-Host -Prompt "Enter the location (i.e. centralus)"

   New-AzResourceGroup -Name $resourceGroupName -Location "$location"
   New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "<name of your local template file>"  
   ```
---

### <a name="configure-the-new-storage-account"></a>Yeni depolama hesabını yapılandırma

Bazı özellikler bir şablona aktarmayacaktır, bu nedenle bunları yeni depolama hesabına eklemeniz gerekecektir. 

Aşağıdaki tablo, bu özellikleri yeni depolama hesabınıza ekleme kılavuzlarıyla birlikte listelemektedir.

| Özellik    | Rehber    |
|--------|-----------|
| **Yaşam döngüsü yönetim ilkeleri** | [Azure Blob depolama yaşam döngüsünü yönetme](../blobs/storage-lifecycle-management-concepts.md) |
| **Statik Web siteleri** | [Azure depolama 'da statik bir Web sitesi barındırma](../blobs/storage-blob-static-website-how-to.md) |
| **Olay abonelikleri** | [Blob depolama olaylarına yanıt verme](../blobs/storage-blob-event-overview.md) |
| **Uyarılar** | [Azure Izleyici 'yi kullanarak etkinlik günlüğü uyarıları oluşturma, görüntüleme ve yönetme](../../azure-monitor/platform/alerts-activity-log.md) |
| **Content Delivery Network (CDN)** | [HTTPS üzerinden özel etki alanlarıyla bloblara erişmek için Azure CDN kullanma](../blobs/storage-https-custom-domain-cdn.md) |

> [!NOTE] 
> Kaynak depolama hesabı için bir CDN ayarlarsanız, mevcut CDN 'nizin kaynağını yeni hesabınızın birincil blob hizmeti uç noktası (veya birincil statik Web sitesi uç noktası) olarak değiştirmeniz yeterlidir. 

### <a name="move-data-to-the-new-storage-account"></a>Verileri yeni depolama hesabına taşı

Verilerinizi üzerine taşımanın bazı yolları aşağıda verilmiştir.

: heavy_check_mark: **Azure Depolama Gezgini**

  Kullanımı kolay ve küçük veri kümeleri için uygundur. Kapsayıcıları ve dosya paylaşımlarını kopyalayabilir ve sonra bunları hedef hesaba yapıştırabilirsiniz.

  Bkz. [Azure Depolama Gezgini](https://azure.microsoft.com/features/storage-explorer/);

: heavy_check_mark: **AzCopy**

  Bu tercih edilen yaklaşımdır. Performans için en iyi duruma getirilmiştir.  Daha hızlı bir şekilde, verilerin doğrudan depolama sunucuları arasında kopyalandığı, AzCopy 'in bilgisayarınızın ağ bant genişliğini kullanmadığı bir yoldur. Komut satırında veya özel bir betiğin parçası olarak AzCopy kullanın.

  Bkz. [AzCopy ile çalışmaya başlama](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

: heavy_check_mark: **Azure Data Factory** 

  Bu aracı yalnızca AzCopy 'in geçerli sürümünde desteklenmeyen işlevlere ihtiyacınız varsa kullanın. Örneğin, AzCopy 'ın geçerli sürümünde blob 'ları hiyerarşik bir ad alanı olan hesaplar arasında kopyalayamazsınız. Ayrıca AzCopy dosya erişim denetim listelerini veya dosya zaman damgalarını korumaz (örneğin: oluşturma ve değiştirme zaman damgaları). 

  Şu bağlantılara bakın:
  - [Azure Data Factory kullanarak Azure Blob depolama alanına veya veri kopyalama](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage)
  - [Azure Data Factory kullanarak Azure Data Lake Storage 2. veri kopyalama](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)
  - [Azure Data Factory kullanarak Azure dosya depolamadan veri kopyalama](https://docs.microsoft.com/azure/data-factory/connector-azure-file-storage)
  - [Azure Data Factory kullanarak Azure Tablo depolamadan veri kopyalama](https://docs.microsoft.com/azure/data-factory/connector-azure-table-storage)

---

## <a name="discard-or-clean-up"></a>Atın veya temizleyin

Dağıtımdan sonra, baştan başlamak istiyorsanız, hedef depolama hesabını silebilir ve bu makalenin [hazırlama](#prepare) ve [taşıma](#move) bölümünde açıklanan adımları yineleyebilirsiniz.

Değişiklikleri uygulamak ve depolama hesabını taşımayı tamamlamak için, kaynak depolama hesabını silin.

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

Azure portal kullanarak bir depolama hesabını kaldırmak için:

1. Azure portal, sol taraftaki menüyü genişleterek hizmet menüsünü açın **ve depolama hesapları ' nı seçerek depolama** hesaplarınızın listesini görüntüleyin.

2. Silinecek hedef depolama hesabını bulun ve listenin sağ tarafındaki **daha fazla** düğmesine ( **...** ) sağ tıklayın.

3. **Sil**' i seçin ve onaylayın.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Kaynak grubunu ve yeni depolama hesabı da dahil olmak üzere ilişkili kaynaklarını kaldırmak için [Remove-AzStorageAccount](/powershell/module/az.resources/remove-azstorageaccount) komutunu kullanın:

```powershell
Remove-AzStorageAccount -ResourceGroupName  $resourceGroup -AccountName $storageAccount
```
---

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, bir Azure Depolama hesabını bir bölgeden diğerine taşıdı ve kaynak kaynakları temizledi.  Azure 'da bölgeler ve olağanüstü durum kurtarma arasında kaynakları taşıma hakkında daha fazla bilgi edinmek için bkz:


- [Kaynakları yeni kaynak grubuna veya aboneliğe taşıma](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Azure VM’lerini başka bir bölgeye taşıma](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
