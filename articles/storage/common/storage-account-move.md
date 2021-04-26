---
title: Bir Azure Depolama hesabını başka bir bölgeye taşıma | Microsoft Docs
description: Bir Azure Depolama hesabını başka bir bölgeye nasıl taşıyabileceğinizi gösterir.
services: storage
author: normesta
ms.service: storage
ms.subservice: common
ms.topic: how-to
ms.date: 05/11/2020
ms.author: normesta
ms.reviewer: dineshm
ms.openlocfilehash: 1900326bf03c6a32f25c7a019d8bd1e460735bd6
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107505607"
---
# <a name="move-an-azure-storage-account-to-another-region"></a>Bir Azure Depolama hesabını başka bir bölgeye taşıma

Bir depolama hesabını taşımak için diğer bölgede depolama hesabının bir kopyasını oluşturun. Daha sonra, AzCopy veya seçtiğiniz başka bir aracı kullanarak verilerinizi bu hesaba taşıyın.

Bu makalede şunları yapmayı öğreneceksiniz:

> [!div class="checklist"]
> 
> * Bir şablonu dışarı aktarın.
> * Hedef bölge ve depolama hesabı adını ekleyerek şablonu değiştirin.
> * Yeni depolama hesabı oluşturmak için şablonu dağıtın.
> * Yeni depolama hesabını yapılandırın.
> * Verileri yeni depolama hesabına taşıyın.
> * Kaynak bölgedeki kaynakları silin.

## <a name="prerequisites"></a>Önkoşullar

- Hesabınızın kullandığı hizmetlerin ve özelliklerin hedef bölgede desteklendiğinden emin olun.

- Önizleme özellikleri için, aboneliğinizin hedef bölge için allowlistelendiğinden emin olun.

<a id="prepare"></a>

## <a name="prepare"></a>Hazırlama

Başlamak için Kaynak Yöneticisi şablonu dışarı aktarın ve değiştirin. 

### <a name="export-a-template"></a>Şablonu dışarı aktarma

Bu şablon, depolama hesabınızı tanımlayan ayarlar içerir. 

# <a name="portal"></a>[Portal](#tab/azure-portal)

Şablonu Azure portalını kullanarak dışarı aktarmak için:

1. [Azure portalında](https://portal.azure.com) oturum açın.

2. **Tüm kaynaklar** ' ı seçin ve ardından depolama hesabınızı seçin.

3. > **Otomasyonu**  >  **dışarı aktarma şablonu**' nu seçin.

4. **Şablonu dışarı aktar** dikey penceresinde **İndir** ' i seçin.

5. Portaldan indirdiğiniz. zip dosyasını bulun ve bu dosyayı seçtiğiniz bir klasöre ayıklayın.

   Bu ZIP dosyası, şablonu dağıtmak için şablon ve betikleri oluşturan. JSON dosyalarını içerir.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Bir şablonu PowerShell kullanarak dışarı aktarmak için:

1. [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) komutuyla Azure aboneliğinizde oturum açın ve ekrandaki yönergeleri izleyin:

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

Depolama hesabı adını ve bölgesini değiştirerek şablonu düzenleyin.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Azure portal kullanarak şablonu dağıtmak için:

1. Azure portal **kaynak oluştur**' u seçin.

2. **Market 'Te ara**' te, **şablon dağıtımı** yazın ve ardından **ENTER** tuşuna basın.

3. **Şablon dağıtımı** seçin.

    ![Azure Resource Manager şablon kitaplığı](./media/storage-account-move/azure-resource-manager-template-library.png)

4. **Oluştur**’u seçin.

5. **Düzenleyicide kendi şablonunuzu oluşturun öğesini** seçin.

6. **Dosya Yükle**' yi seçin ve ardından son bölümde indirdiğiniz dosyanın **template.js** yüklemek için yönergeleri izleyin.

7. Dosyadaki **template.js** , depolama hesabı adının varsayılan değerini ayarlayarak hedef depolama hesabını adlandırın. Bu örnek, depolama hesabı adının varsayılan değerini olarak ayarlar `mytargetaccount` .
    
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
    Bölge konum kodlarını almak için bkz. [Azure konumları](https://azure.microsoft.com/global-infrastructure/locations/).  Bir bölgenin kodu, alanı olmayan bölge adıdır, **Orta ABD**  =  **merkezileştirme**.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell kullanarak şablonu dağıtmak için:

1. Dosyadaki **template.js** , depolama hesabı adının varsayılan değerini ayarlayarak hedef depolama hesabını adlandırın. Bu örnek, depolama hesabı adının varsayılan değerini olarak ayarlar `mytargetaccount` .
    
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

2. Dosyadakitemplate.js **konum** özelliğini hedef bölgeye  düzenleyin. Bu örnek, hedef bölgeyi olarak ayarlar `eastus` .

    ```json
    "resources": [{
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2019-04-01",
         "name": "[parameters('storageAccounts_mysourceaccount_name')]",
         "location": "eastus"
         }]          
    ```

    [Get-AzLocation](/powershell/module/az.resources/get-azlocation) komutunu çalıştırarak bölge kodlarını elde edebilirsiniz.

    ```azurepowershell-interactive
    Get-AzLocation | format-table 
    ```
---

<a id="move"></a>

## <a name="move"></a>Taşı

Hedef bölgede yeni bir depolama hesabı oluşturmak için şablonu dağıtın. 

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. **template.js** dosyaya kaydedin.

2. Özellik değerlerini girin veya seçin:

- **Abonelik**: bir Azure aboneliği seçin.

- **Kaynak grubu**: **Yeni Oluştur**'u seçin ve kaynak grubuna bir ad verin.

- **Konum**: bir Azure konumu seçin.

3. **Yukarıda belirtilen hüküm ve koşulları kabul ediyorum** onay kutusuna tıklayın ve ardından **satın al düğmesini seçin** .

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription)ile hedef genel IP 'yi dağıtmak ISTEDIĞINIZ abonelik kimliğini edinin:

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

Bazı özellikler şablona aktarılmayacağından onları yeni depolama hesabına ayrıca eklemeniz gerekir. 

Aşağıdaki tabloda bu özelliklerin yanı sıra bunları yeni depolama hesabınıza ekleme konusunda yönergeler verilmiştir.

| Özellik    | Rehber    |
|--------|-----------|
| **Yaşam döngüsü yönetim ilkeleri** | [Azure Blob depolama yaşam döngüsünü yönetme](../blobs/storage-lifecycle-management-concepts.md) |
| **Statik web siteleri** | [Azure depolama 'da statik bir Web sitesi barındırma](../blobs/storage-blob-static-website-how-to.md) |
| **Olay abonelikleri** | [Blob depolama olaylarına yanıt verme](../blobs/storage-blob-event-overview.md) |
| **Uyarılar** | [Azure Izleyici 'yi kullanarak etkinlik günlüğü uyarıları oluşturma, görüntüleme ve yönetme](../../azure-monitor/alerts/alerts-activity-log.md) |
| **Content Delivery Network (CDN)** | [HTTPS üzerinden özel etki alanlarıyla bloblara erişmek için Azure CDN kullanma](../blobs/storage-https-custom-domain-cdn.md) |

> [!NOTE] 
> Kaynak depolama hesabı için bir CDN ayarlarsanız, mevcut CDN 'nizin kaynağını yeni hesabınızın birincil blob hizmeti uç noktası (veya birincil statik Web sitesi uç noktası) olarak değiştirmeniz yeterlidir. 

### <a name="move-data-to-the-new-storage-account"></a>Verileri yeni depolama hesabına taşıma

AzCopy, verilerinizi üzerine taşımak için tercih edilen bir araçtır. Performans için iyileştirilmiştir.  AzCopy'nin daha hızlı olmasının nedenlerinden biri, verilerin doğrudan depolama sunucuları arasında kopyalanması ve bu sayede bilgisayarınızın ağ bant genişliğinin harcanmamasıdır. AzCopy'yi komut satırında veya özel betik içinde kullanabilirsiniz. Bkz. [AzCopy ile çalışmaya başlama](/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

Verilerinizi üzerine taşımak için Azure Data Factory de kullanabilirsiniz. Sezgisel bir kullanıcı arabirimi sağlar. Azure Data Factory kullanmak için şu bağlantılardan herhangi birine bakın:. 

  - [Azure Data Factory kullanarak Azure Blob depolama alanına veya oradan veri kopyalama](/azure/data-factory/connector-azure-blob-storage)
  - [Azure Data Factory kullanarak Azure Data Lake Storage 2. Nesil'e veya oradan veri kopyalama](/azure/data-factory/connector-azure-data-lake-storage)
  - [Azure Data Factory kullanarak Azure Dosyalar depolama alanına veya oradan veri kopyalama](/azure/data-factory/connector-azure-file-storage)
  - [Azure Data Factory kullanarak Azure Tablo depolama alanına veya oradan veri kopyalama](/azure/data-factory/connector-azure-table-storage)

---

## <a name="discard-or-clean-up"></a>Atma veya temizleme

Dağıtımdan sonra, baştan başlamak istiyorsanız, hedef depolama hesabını silebilir ve bu makalenin [hazırlama](#prepare) ve [taşıma](#move) bölümünde açıklanan adımları yineleyebilirsiniz.

Değişiklikleri göndermek ve depolama hesabını taşıma işlemini tamamlamak için kaynak depolama hesabını silin.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Azure portalını kullanarak depolama hesabını kaldırma:

1. Azure portal, sol taraftaki menüyü genişleterek hizmet menüsünü açın **ve depolama hesapları ' nı seçerek depolama** hesaplarınızın listesini görüntüleyin.

2. Silinecek hedef depolama hesabını bulun ve listenin sağ tarafındaki **daha fazla** düğmesine (**...**) sağ tıklayın.

3. **Sil**' i seçin ve onaylayın.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Kaynak grubunu ve yeni depolama hesabı da dahil olmak üzere ilişkili kaynaklarını kaldırmak için [Remove-AzStorageAccount](/powershell/module/az.storage/remove-azstorageaccount) komutunu kullanın:

```powershell
Remove-AzStorageAccount -ResourceGroupName  $resourceGroup -AccountName $storageAccount
```
---

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, bir Azure Depolama hesabını bir bölgeden diğerine taşıdı ve kaynak kaynakları temizledi.  Azure 'da bölgeler ve olağanüstü durum kurtarma arasında kaynakları taşıma hakkında daha fazla bilgi edinmek için bkz:


- [Kaynakları yeni bir kaynak grubuna veya aboneliğe taşıma](../../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [Azure VM’lerini başka bir bölgeye taşıma](../../site-recovery/azure-to-azure-tutorial-migrate.md)