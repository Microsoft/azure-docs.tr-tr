---
title: Azure depolama Blobları için geçici silme | Microsoft Docs
description: Azure Storage artık BLOB nesnelerine geçici silme olanağı sunarak, verileri yanlışlıkla değiştirildiğinde veya bir uygulama ya da başka bir depolama hesabı kullanıcısı tarafından daha kolay bir şekilde kurtarabilmeniz için daha kolay bir şekilde silinebilir.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 6f6aa90553f3a69d2d287c7d59e166884a1a8f66
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113738"
---
# <a name="soft-delete-for-azure-storage-blobs"></a>Azure depolama Blobları için geçici silme

Azure Storage artık BLOB nesnelerine geçici silme olanağı sunarak, verileri yanlışlıkla değiştirildiğinde veya bir uygulama ya da başka bir depolama hesabı kullanıcısı tarafından daha kolay bir şekilde kurtarabilmeniz için daha kolay bir şekilde silinebilir.

## <a name="how-soft-delete-works"></a>Geçici silmenin çalışması

Etkin olduğunda, geçici silme, Bloblar veya blob anlık görüntüleri silindiğinde verilerinizi kaydetmenizi ve kurtarmanızı sağlar. Bu koruma, bir üzerine yazma sonucu olarak silinen blob verilerini genişletir.

Veriler silindiğinde, kalıcı olarak silinmesi yerine geçici olarak silinen bir duruma geçer. Geçici silme açık olduğunda ve verilerin üzerine yazdığınızda, üzerine yazılan verilerin durumunu kaydetmek için geçici olarak silinen bir anlık görüntü oluşturulur. Geçici olarak listelenmediği takdirde geçici olarak silinen nesneler görünmez. Geçici silinen verilerin kullanım süresi kalıcı olarak sonlanmadan önce verilerin kaç kere kurtarılabileceğini yapılandırabilirsiniz.

Geçici silme, geriye dönük olarak uyumludur, bu sayede bu özelliğin korumalarının avantajlarından yararlanmak için uygulamalarınızda herhangi bir değişiklik yapmanız gerekmez. Ancak, [veri kurtarma](#recovery) yeni bir **geri alma blob** API 'si sunar.

### <a name="configuration-settings"></a>Yapılandırma ayarları

Yeni bir hesap oluşturduğunuzda, geçici silme varsayılan olarak kapalıdır. Geçici silme, var olan depolama hesapları için de varsayılan olarak kapalıdır. Bir depolama hesabının ömrü boyunca dilediğiniz zaman özelliği açık ve kapalı yapabilirsiniz.

Özellik devre dışı bırakıldığında geçici olarak silinen verilere erişebilir ve bunları kurtarabilirsiniz, ancak özellik önceden açıldığında geçici olarak silinen verilerin kaydedildiğini kabul edebilirsiniz. Geçici silme özelliğini etkinleştirdiğinizde, saklama süresini de yapılandırmanız gerekir.

Saklama süresi, geçici olarak silinen verilerin saklanacağı ve kurtarılmasına uygun olan süreyi gösterir. Açıkça silinen Bloblar ve BLOB anlık görüntüleri için, veri silindiğinde Bekletme dönemi saati başlar. Verilerin üzerine yazıldığında geçici silme özelliği tarafından oluşturulan geçici olarak silinen anlık görüntüler için, bu saat, anlık görüntü oluşturulduğunda başlar. Şu anda, 1 ila 365 gün boyunca geçici olarak silinen verileri tutabilirsiniz.

Geçici silme bekletme süresini istediğiniz zaman değiştirebilirsiniz. Güncelleştirilmiş bir saklama süresi, yalnızca yeni silinen veriler için geçerlidir. Daha önce silinen veriler, verilerin silindiği zaman yapılandırılan bekletme dönemine göre sona erer. Geçici olarak silinen bir nesneyi silmeye çalışmak, süre sonu süresini etkilemez.

### <a name="saving-deleted-data"></a>Silinen veriler kaydediliyor

Geçici silme, Blobların veya blob anlık görüntülerinin silindiği veya üzerine yazıldığı birçok durumda verilerinizi korur.

Blob **'u koy,** **PUT bloğu**, put **bloğu listesi**veya **kopyalama** blobu kullanılarak bir blob üzerine yazıldığında, yazma işlemi öncesinde blob 'un durumunun bir anlık görüntüsü otomatik olarak oluşturulur. Bu anlık görüntü, geçici olarak silinmiş bir anlık görüntüdür; geçici olarak silinen nesneler açıkça listelenmediyse görünmez. Geçici silinen nesneleri nasıl listeleyeceğinizi öğrenmek için [Kurtarma](#recovery) bölümüne bakın.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-overwrite.png)

*Geçici olarak silinen veriler gri, etkin veriler mavi olur. Daha eski veriler altında daha yeni yazılmış veriler görüntülenir. B1 ile B0 'ın üzerine yazıldığında, B0 'nin geçici olarak silinmiş bir anlık görüntüsü oluşturulur. B2 ile B1 üzerine yazıldığında, B1 'nin geçici olarak silinen bir anlık görüntüsü oluşturulur.*

> [!NOTE]  
> Geçici silme yalnızca hedef Blobun hesabı için açık olan kopyalama işlemleri için korumayı geçersiz kılar.

> [!NOTE]  
> Geçici silme, arşiv katmanındaki Bloblar için korumanın üzerine yazmaz. Arşivdeki bir Blobun herhangi bir katmanda yeni bir blob ile üzerine yazılırsa, üzerine yazılan Blobun kalıcı olarak zaman aşımına uğradı.

Bir anlık görüntüde **silme blobu** çağrıldığında, bu anlık görüntü geçici olarak silinmiş olarak işaretlenir. Yeni bir anlık görüntü oluşturulmaz.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-explicit-delete-snapshot.png)

*Geçici olarak silinen veriler gri, etkin veriler mavi olur. Daha eski veriler altında daha yeni yazılmış veriler görüntülenir. **Anlık görüntü blobu** çağrıldığında, B0 bir anlık görüntü haline gelir ve B1, Blobun etkin durumudur. B0 anlık görüntüsü silindiğinde, geçici olarak silinmiş olarak işaretlenir.*

**BLOB silme** bir temel blob 'da (kendi anlık görüntü olmayan herhangi bir BLOB) çağrıldığında, bu blob geçici olarak silindi olarak işaretlenir. Önceki davranışla tutarlı, etkin anlık görüntülere sahip bir bloba **silme blobu** çağırma bir hata döndürüyor. Geçici olarak silinen anlık görüntülerle blob üzerinde **silme blobu** çağırma bir hata döndürmez. Geçici silme açıkken bir blobu ve tüm anlık görüntülerini tek işlemle silebilirsiniz. Bunu yaptığınızda temel blob ve anlık görüntüler geçici olarak silinir.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-explicit-include.png)

*Geçici olarak silinen veriler gri, etkin veriler mavi olur. Daha eski veriler altında daha yeni yazılmış veriler görüntülenir. Burada, B2 ve ilişkili tüm anlık görüntüleri silmek için bir **silme blobu** çağrısı yapılır. Etkin blob, B2 ve tüm ilişkili anlık görüntüler, geçici olarak silinmiş olarak işaretlenir.*

> [!NOTE]  
> Geçici olarak silinen bir Blobun üzerine yazıldığında, yazma işleminden önce blob 'un durumunun geçici olarak silinmiş bir anlık görüntüsü otomatik olarak oluşturulur. Yeni blob, üzerine yazılan Blobun katmanını devralır.

Geçici silme, verileri kapsayıcı veya hesap silme durumlarında veya blob meta verilerinin ve BLOB özelliklerinin üzerine yazıldığında kaydetmez. Bir depolama hesabını hatalı silinmeye karşı korumak için Azure Resource Manager kullanarak bir kilit yapılandırabilirsiniz. Daha fazla bilgi edinmek için lütfen [beklenmeyen değişikliklerin oluşmasını engellemek üzere kaynakları kilitle](../../azure-resource-manager/resource-group-lock-resources.md) Azure Resource Manager makalesine bakın.

Aşağıdaki tabloda, geçici silme açıkken beklenen davranışın ayrıntıları verilmiştir:

| REST API işlemi | Kaynak türü | Açıklama | Davranış değişikliği |
|--------------------|---------------|-------------|--------------------|
| [Delete](/rest/api/storagerp/StorageAccounts/Delete) | Hesap | İçerdiği tüm kapsayıcılar ve BLOB 'lar dahil olmak üzere depolama hesabını siler.                           | Değişiklik yok. Silinen hesaptaki kapsayıcılar ve Bloblar kurtarılamaz. |
| [Kapsayıcıyı sil](/rest/api/storageservices/delete-container) | Kapsayıcı | Kapsayıcı, içerdiği tüm Bloblar dahil olmak üzere siler. | Değişiklik yok. Silinen kapsayıcıdaki Bloblar kurtarılamaz. |
| [Blobu koy](/rest/api/storageservices/put-blob) | Blok, ekleme ve sayfa Blobları | Yeni bir blob oluşturur veya bir kapsayıcı içinde var olan bir blobu değiştirir | Var olan bir Blobun değiştirmek için kullanılırsa, çağrıdan önce blob 'un durumunun bir anlık görüntüsü otomatik olarak oluşturulur. Bu, daha önce ve yalnızca aynı türdeki bir blob (blok, ekleme veya sayfa) ile değiştiriliyorsa, daha önce geçici olarak silinen bir blob için de geçerlidir. Farklı türdeki bir Blobun değiştirildiyse, var olan tüm geçici silinen verilerin geçerliliği kalıcı olarak dolacak. |
| [Blobu Sil](/rest/api/storageservices/delete-blob) | Blok, ekleme ve sayfa Blobları | Silinmek üzere bir blob veya blob anlık görüntüsü işaretler. Çöp toplama sırasında blob veya anlık görüntü daha sonra silinir | Blob anlık görüntüsünü silmek için kullanılırsa, bu anlık görüntü geçici olarak silinmiş olarak işaretlenir. Bir blobu silmek için kullanılırsa, bu blob geçici olarak silindi olarak işaretlenir. |
| [Blobu Kopyala](/rest/api/storageservices/copy-blob) | Blok, ekleme ve sayfa Blobları | Kaynak blobu, aynı depolama hesabındaki veya başka bir depolama hesabındaki bir hedef bloba kopyalar. | Var olan bir Blobun değiştirmek için kullanılırsa, çağrıdan önce blob 'un durumunun bir anlık görüntüsü otomatik olarak oluşturulur. Bu, daha önce ve yalnızca aynı türdeki bir blob (blok, ekleme veya sayfa) ile değiştiriliyorsa, daha önce geçici olarak silinen bir blob için de geçerlidir. Farklı türdeki bir Blobun değiştirildiyse, var olan tüm geçici silinen verilerin geçerliliği kalıcı olarak dolacak. |
| [Yerleştirme bloğu](/rest/api/storageservices/put-block) | Blok Blobları | Bir blok blobunun parçası olarak kaydedilecek yeni bir blok oluşturur. | Etkin olan bir Blobun blok yürütmek için kullanılırsa değişiklik yapılmaz. Geçici olarak silinen bir Blobun blok yürütmek için kullanılırsa, yeni bir blob oluşturulur ve geçici olarak silinen Blobun durumunu yakalamak için bir anlık görüntü oluşturulur. |
| [Öbek listesini yerleştirme](/rest/api/storageservices/put-block-list) | Blok Blobları | Blok Blobu oluşturan blok kimlikleri kümesini belirterek bir blobu kaydeder. | Var olan bir Blobun değiştirmek için kullanılırsa, çağrıdan önce blob 'un durumunun bir anlık görüntüsü otomatik olarak oluşturulur. Bu, daha önce ve yalnızca Blok Blobu ise, daha önce geçici olarak silinmiş bir blob için de geçerlidir. Farklı türdeki bir Blobun değiştirildiyse, var olan tüm geçici silinen verilerin geçerliliği kalıcı olarak dolacak. |
| [Yerleştirme sayfası](/rest/api/storageservices/put-page) | Sayfa Blobları | Sayfa Blobuna bir sayfa aralığı yazar. | Değişiklik yok. Bu işlem kullanılarak üzerine yazılan veya temizlenmemiş Sayfa Blobu verileri kaydedilmez ve kurtarılamaz. |
| [Ekleme bloğu](/rest/api/storageservices/append-block) | Blob ekleme | Bir ekleme Blobunun sonuna bir veri bloğu Yazar | Değişiklik yok. |
| [Blob özelliklerini ayarla](/rest/api/storageservices/set-blob-properties) | Blok, ekleme ve sayfa Blobları | Blob için tanımlanan sistem özellikleri için değerleri ayarlar. | Değişiklik yok. Üzerine yazılan blob özellikleri kurtarılabilir değil. |
| [Blob meta verilerini ayarla](/rest/api/storageservices/set-blob-metadata) | Blok, ekleme ve sayfa Blobları | Belirtilen blob için Kullanıcı tanımlı meta verileri bir veya daha fazla ad-değer çifti olarak ayarlar. | Değişiklik yok. Üzerine yazılan blob meta verileri kurtarılamaz. |

Bir sayfa Blobun aralıklarının üzerine yazmak veya onları temizlemek için "yerleştirme sayfası" çağırmanın otomatik olarak anlık görüntü üretmeyecek olduğunu fark etmek önemlidir. Sanal makine diskleri sayfa Blobları tarafından desteklenir ve veri yazmak için **PUT sayfasını** kullanır.

### <a name="recovery"></a>Kurtarma

Geçici olarak silinen bir temel blob üzerinde [geri alma blobu](/rest/api/storageservices/undelete-blob) işleminin çağrılması, onu ve ilişkili tüm yazılımla silinen anlık görüntüleri etkin olarak geri yükler. Etkin bir temel blob üzerinde `Undelete Blob` işlemini çağırmak, tüm ilişkili geçici silinen anlık görüntüleri etkin olarak geri yükler. Anlık görüntüler etkin olarak geri yüklendiğinde, Kullanıcı tarafından oluşturulan anlık görüntüler gibi görünür. Bunlar temel Blobun üzerine yazmaz.

Bir blobu belirli bir geçici silinen anlık görüntüye geri yüklemek için, temel bloba `Undelete Blob` çağırabilirsiniz. Ardından, anlık görüntüyü şimdi etkin Blobun üzerine kopyalayabilirsiniz. Ayrıca, anlık görüntüyü yeni bir bloba kopyalayabilirsiniz.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-recover.png)

*Geçici olarak silinen veriler gri, etkin veriler mavi olur. Daha eski veriler altında daha yeni yazılmış veriler görüntülenir. Burada, blob B üzerinde **geri alma blobu** çağrılır, böylece temel blob, B1 ve tüm ilişkili anlık görüntüler, burada yalnızca B0 etkin olarak geri yüklenir. İkinci adımda, B0 temel blob üzerinden kopyalanır. Bu kopyalama işlemi B1 'nin geçici olarak silinmiş bir anlık görüntüsünü oluşturur.*

Geçici silinen Blobları ve BLOB anlık görüntülerini görüntülemek için, silinen verileri **liste bloblarına**dahil etme seçeneğini belirleyebilirsiniz. Yalnızca geçici olarak silinen temel Blobları görüntülemeyi veya Ayrıca, geçici olarak silinen blob anlık görüntülerini da dahil etmek seçebilirsiniz. Tüm geçici silinen veriler için, verilerin ne zaman silindiğini ve verilerin kalıcı olarak süresi dolmadan önce geçen gün sayısını görüntüleyebilirsiniz.

### <a name="example"></a>Örnek

Aşağıda, yumuşak silme açıkken *HelloWorld* adlı bir blobu karşıya yükleyen, üzerine yazan, anlık görüntülerle, silen ve geri yükleyen bir .net betiğinin konsol çıktısı verilmiştir:

```bash
Upload:
- HelloWorld (is soft deleted: False, is snapshot: False)

Overwrite:
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)

Snapshot:
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)

Delete (including snapshots):
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: True, is snapshot: False)

Undelete:
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)

Copy a snapshot over the base blob:
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)
```

Bu çıktıyı üreten uygulamanın işaretçisi için [sonraki adımlar](#next-steps) bölümüne bakın.

## <a name="pricing-and-billing"></a>Fiyatlandırma ve Faturalama

Tüm geçici silinen veriler, etkin verilerle aynı hızda faturalandırılır. Yapılandırılan saklama süresinden sonra kalıcı olarak silinen veriler için ücretlendirilmeyecektir. Anlık görüntülere ve bunların nasıl tahakkuk ettikleri hakkında daha ayrıntılı bilgi için lütfen bkz. [anlık görüntülerin nasıl tahakkuk ettirildiğini anlama](storage-blob-snapshots.md).

Otomatik anlık görüntü oluşturma ile ilgili işlemler için faturalandırılmaz. Yazma işlemleri için geri alma **BLOB** işlemleri için faturalandırılırsınız.

Genel olarak Azure Blob depolama fiyatları hakkında daha fazla ayrıntı için [Azure Blob depolama fiyatlandırma sayfasına](https://azure.microsoft.com/pricing/details/storage/blobs/)göz atın.

Geçici silme özelliğini ilk kez açtığınızda, özelliğin faturanızı nasıl etkileyeceğini daha iyi anlamak için küçük bir bekletme süresi kullanmanızı öneririz.

## <a name="get-started"></a>Başlarken

Aşağıdaki adımlarda, geçici silme ile çalışmaya başlama gösterilmektedir.

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

Geçici silme özelliğini etkinleştirmek için **BLOB hizmeti**altındaki **geçici silme** seçeneğine gidin. Ardından **etkin** ' e tıklayın ve geçici olarak silinen verileri sürdürmek istediğiniz gün sayısını girin.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-configuration.png)

Geçici silinen Blobları görüntülemek için **silinen Blobları göster** onay kutusunu seçin.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-view-soft-deleted.png)

Belirli bir blob için geçici olarak silinen anlık görüntüleri görüntülemek için, blobu seçin ve **anlık görüntüleri görüntüle**' ye tıklayın.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-view-soft-deleted-snapshots.png)

**Silinen anlık görüntüleri göster** onay kutusunun seçili olduğundan emin olun.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-view-soft-deleted-snapshots-check.png)

Geçici olarak silinen bir blob veya anlık görüntüye tıkladığınızda, yeni blob özelliklerine dikkat edin. Bu, nesnenin ne zaman silindiğini ve BLOB ya da blob anlık görüntüsünün kalıcı olarak zaman aşımına erene kadar kaç gün kaldığını gösterir. Geçici olarak silinen nesne bir anlık görüntü değilse, geri alma seçeneğiniz de olur.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-properties.png)

Bir Blobun silinmesinin, ilişkili tüm anlık görüntüleri de silmeyi unutmayın. Etkin bir blob için geçici olarak silinen anlık görüntüleri geri almak için bloba tıklayın ve **tüm anlık görüntüleri geri al**seçeneğini belirleyin.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-undelete-all-snapshots.png)

Bir Blobun anlık görüntülerini geri aldıktan sonra, bir anlık görüntüyü kök Blobun üzerine kopyalamak için **Yükselt** ' e tıklayabilir ve böylece blobu anlık görüntüye geri yükleyebilirsiniz.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-promote-snapshot.png)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Geçici silme özelliğini etkinleştirmek için bir blob istemcisinin hizmet özelliklerini güncelleştirin. Aşağıdaki örnek, bir abonelikteki hesapların bir alt kümesi için geçici silme imkanı sunar:

```powershell
Set-AzContext -Subscription "<subscription-name>"
$MatchingAccounts = Get-AzStorageAccount | where-object{$_.StorageAccountName -match "<matching-regex>"}
$MatchingAccounts | Enable-AzStorageDeleteRetentionPolicy -RetentionDays 7
```
Aşağıdaki komutu kullanarak geçici silmenin açık olduğunu doğrulayabilirsiniz:

```powershell
$MatchingAccounts | Get-AzStorageServiceProperty -ServiceType Blob
```

Yanlışlıkla silinen Blobları kurtarmak için, bu bloblarda geri alma işlemini çağırın. **Geri alma blobu**çağırma, hem etkin hem de geçici olarak silinen bloblarda, ilişkili tüm yazılımla silinen anlık görüntüleri etkin olarak geri yükleyeceğini unutmayın. Aşağıdaki örnek, bir kapsayıcıdaki tüm geçici silinen ve etkin bloblarda geri alma işlemini çağırır:

```powershell
# Create a context by specifying storage account name and key
$ctx = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

# Get the blobs in a given container and show their properties
$Blobs = Get-AzStorageBlob -Container $StorageContainerName -Context $ctx -IncludeDeleted
$Blobs.ICloudBlob.Properties

# Undelete the blobs
$Blobs.ICloudBlob.Undelete()
```
Geçerli geçici silme bekletme ilkesini bulmak için aşağıdaki komutu kullanın:

```azurepowershell-interactive
   $account = Get-AzStorageAccount -ResourceGroupName myresourcegroup -Name storageaccount
   Get-AzStorageServiceProperty -ServiceType Blob -Context $account.Context
```

# <a name="clitabazure-cli"></a>[CLI](#tab/azure-CLI)

Geçici silme özelliğini etkinleştirmek için bir blob istemcisinin hizmet özelliklerini güncelleştirin:

```azurecli-interactive
az storage blob service-properties delete-policy update --days-retained 7  --account-name mystorageaccount --enable true
```

Geçici silme özelliğinin açık olduğunu doğrulamak için aşağıdaki komutu kullanın: 

```azurecli-interactive
az storage blob service-properties delete-policy show --account-name mystorageaccount 
```

# <a name="pythontabpython"></a>[Python](#tab/python)

Geçici silme özelliğini etkinleştirmek için bir blob istemcisinin hizmet özelliklerini güncelleştirin:

```python
# Make the requisite imports
from azure.storage.blob import BlockBlobService
from azure.storage.common.models import DeleteRetentionPolicy

# Initialize a block blob service
block_blob_service = BlockBlobService(
    account_name='<enter your storage account name>', account_key='<enter your storage account key>')

# Set the blob client's service property settings to enable soft delete
block_blob_service.set_blob_service_properties(
    delete_retention_policy=DeleteRetentionPolicy(enabled=True, days=7))
```

# <a name="nettabnet"></a>[.NET](#tab/net)

Geçici silme özelliğini etkinleştirmek için bir blob istemcisinin hizmet özelliklerini güncelleştirin:

```csharp
// Get the blob client's service property settings
ServiceProperties serviceProperties = blobClient.GetServiceProperties();

// Configure soft delete
serviceProperties.DeleteRetentionPolicy.Enabled = true;
serviceProperties.DeleteRetentionPolicy.RetentionDays = RetentionDays;

// Set the blob client's service property settings
blobClient.SetServiceProperties(serviceProperties);
```

Yanlışlıkla silinen Blobları kurtarmak için, bu bloblarda geri alma işlemini çağırın. **Geri alma blobu**çağırma, hem etkin hem de geçici olarak silinen bloblarda, ilişkili tüm yazılımla silinen anlık görüntüleri etkin olarak geri yükleyeceğini unutmayın. Aşağıdaki örnek, bir kapsayıcıdaki tüm geçici silinen ve etkin bloblarda geri alma işlemini çağırır:

```csharp
// Recover all blobs in a container
foreach (CloudBlob blob in container.ListBlobs(useFlatBlobListing: true, blobListingDetails: BlobListingDetails.Deleted))
{
       await blob.UndeleteAsync();
}
```

Belirli bir blob sürümüne kurtarmak için, ilk olarak bir Blobun çağrısını geri alın ve ardından istenen anlık görüntüyü blob üzerinden kopyalayın. Aşağıdaki örnek, Blok Blobu en son oluşturulan anlık görüntüye kurtarır:

```csharp
// Undelete
await blockBlob.UndeleteAsync();

// List all blobs and snapshots in the container prefixed by the blob name
IEnumerable<IListBlobItem> allBlobVersions = container.ListBlobs(
    prefix: blockBlob.Name, useFlatBlobListing: true, blobListingDetails: BlobListingDetails.Snapshots);

// Restore the most recently generated snapshot to the active blob    
CloudBlockBlob copySource = allBlobVersions.First(version => ((CloudBlockBlob)version).IsSnapshot &&
    ((CloudBlockBlob)version).Name == blockBlob.Name) as CloudBlockBlob;
blockBlob.StartCopy(copySource);
```

---

## <a name="special-considerations"></a>Özel durumlar

Verilerinizin yanlışlıkla değiştirilmesi veya bir uygulama ya da başka bir depolama hesabı kullanıcısı tarafından silinmesi olasılığı varsa, geçici silme özelliğini açmak önerilir. Sık sık üzerine yazılan veriler için geçici silme özelliğinin etkinleştirilmesi, depolama kapasitesi ücretlerine neden olabilir ve Bloblar listelenirken gecikme süresini artırabilir. Sık kullanılan verileri, geçici olarak silmenin devre dışı bırakıldığı ayrı bir depolama hesabında depolayarak bu ek maliyeti ve gecikmeyi azaltabilirsiniz. 

## <a name="faq"></a>SSS

### <a name="for-which-storage-services-can-i-use-soft-delete"></a>Hangi depolama hizmetleri için geçici silme kullanabilirim?

Şu anda, geçici silme yalnızca blob (nesne) depolaması için kullanılabilir.

### <a name="is-soft-delete-available-for-all-storage-account-types"></a>Tüm depolama hesabı türleri için geçici silme kullanılabilir mi?

Evet, geçici silme, BLOB depolama hesapları için ve genel amaçlı (GPv1 ve GPv2) depolama hesaplarındaki Bloblar için kullanılabilir. Hem standart hem de Premium hesap türleri desteklenir. Geçici silme, kapsamakta olan sayfa Blobları olan yönetilmeyen diskler için kullanılabilir. Geçici silme, yönetilen diskler için kullanılamaz.

### <a name="is-soft-delete-available-for-all-storage-tiers"></a>Tüm depolama katmanları için geçici silme kullanılabilir mi?

Evet, geçici silme, sık erişimli, seyrek erişimli ve arşiv dahil tüm depolama katmanları için kullanılabilir. Ancak, geçici silme arşiv katmanındaki Bloblar için korumayı geçersiz kılamaz.

### <a name="can-i-use-the-set-blob-tier-api-to-tier-blobs-with-soft-deleted-snapshots"></a>Blob katmanı API 'sini, geçici olarak silinen anlık görüntülerle katman Blobları olarak kullanabilir miyim?

Evet. Geçici olarak silinen anlık görüntüler orijinal katmanda kalır, ancak temel blob yeni katmana taşınır. 

### <a name="premium-storage-accounts-have-a-per-blob-snapshot-limit-of-100-do-soft-deleted-snapshots-count-toward-this-limit"></a>Premium Depolama hesaplarında 100 blob anlık görüntü sınırı vardır. Geçici olarak silinen anlık görüntü sayısı bu sınıra doğru mı?

Hayır, geçici olarak silinen anlık görüntüler bu sınıra doğru sayılmaz.

### <a name="can-i-turn-on-soft-delete-for-existing-storage-accounts"></a>Mevcut depolama hesapları için geçici silmeyi açabilir miyim?

Evet, geçici silme, hem mevcut hem de yeni depolama hesapları için yapılandırılabilir.

### <a name="if-i-delete-an-entire-account-or-container-with-soft-delete-turned-on-will-all-associated-blobs-be-saved"></a>Geçici silme özelliği açık olan bir hesabın tamamını veya kapsayıcıyı silersem, ilişkili tüm Bloblar kaydedilecek mi?

Hayır, tüm bir hesabı veya kapsayıcıyı silerseniz, ilişkili tüm Bloblar kalıcı olarak silinir. Bir depolama hesabını yanlışlıkla silmelerden koruma hakkında daha fazla bilgi için bkz. [kaynakları kilitleme, beklenmeyen değişiklikleri önleme](../../azure-resource-manager/resource-group-lock-resources.md).

### <a name="can-i-view-capacity-metrics-for-deleted-data"></a>Silinen veriler için kapasite ölçümlerini görüntüleyebilir miyim?

Geçici olarak silinen veriler, toplam depolama hesabı kapasitenizin bir parçası olarak dahil edilmiştir. Depolama kapasitesini izleme ve izleme hakkında daha fazla bilgi için bkz. [depolama Analizi](../common/storage-analytics.md).

### <a name="if-i-turn-off-soft-delete-will-i-still-be-able-to-access-soft-deleted-data"></a>Geçici silme özelliğini devre dışı bırakırsanız, yine de geçici olarak silinen verilere erişebilecek mıyım?

Evet, geçici silme devre dışı bırakıldığında, hala olmayan geçici olarak silinen verilere erişebilir ve bunları kurtarabilirsiniz.

### <a name="can-i-read-and-copy-out-soft-deleted-snapshots-of-my-blob"></a>Blobun geçici olarak silinen anlık görüntülerini okuyabilir ve kopyalayabilir miyim?  

Evet, ancak önce blob üzerinde geri alma işlemini çağırmanız gerekir.

### <a name="is-soft-delete-available-for-all-blob-types"></a>Tüm blob türleri için geçici silme kullanılabilir mi?

Evet, blok Blobları, ekleme Blobları ve sayfa Blobları için geçici silme kullanılabilir.

### <a name="is-soft-delete-available-for-virtual-machine-disks"></a>Sanal makine diskleri için geçici silme kullanılabilir mi?  

Hafif silme, hem Premium hem de standart yönetilmeyen diskler için kullanılabilir ve bu, kapakların altındaki sayfa Blobları olur. Geçici **silme, yalnızca** **BLOB silme**, **BLOB koyma**, **öbek listeleme**, yerleştirme ve **kopyalama blobu** işlemleri ile silinen verileri kurtarmanıza yardımcı olur. **PUT sayfasına** yapılan bir çağrı ile üzerine yazılan veriler kurtarılamaz.

Azure sanal makinesi, **yerleştirme**için çağrılar kullanılarak yönetilmeyen bir diske yazar. bu nedenle, Azure VM 'den yönetilmeyen bir diske yazma işlemini geri almak için geçici silme kullanmak desteklenen bir senaryo değildir.

### <a name="do-i-need-to-change-my-existing-applications-to-use-soft-delete"></a>Geçici silme kullanmak için mevcut uygulamalarımı değiştirmem gerekiyor mu?

Kullanmakta olduğunuz API sürümüne bakılmaksızın geçici silme avantajından yararlanmak mümkündür. Ancak, geçici olarak silinen blob 'ları ve BLOB anlık görüntülerini listelemek ve kurtarmak için REST API veya daha büyük [Depolama Hizmetleri](https://docs.microsoft.com/rest/api/storageservices/Versioning-for-the-Azure-Storage-Services) sürüm 2017-07-29 ' i kullanmanız gerekir. Microsoft, her zaman Azure Storage API 'sinin en son sürümünü kullanmasını önerir.

## <a name="next-steps"></a>Sonraki adımlar

* [.NET örnek kodu](https://github.com/Azure-Samples/storage-dotnet-blob-soft-delete)
* [Blob hizmeti REST API](/rest/api/storageservices/blob-service-rest-api)
* [Azure depolama çoğaltma](../common/storage-redundancy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [RA-GRS kullanarak yüksek oranda kullanılabilir uygulamalar tasarlama](../common/storage-designing-ha-apps-with-ragrs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Azure Storage 'da olağanüstü durum kurtarma ve depolama hesabı yük devretme (Önizleme)](../common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
