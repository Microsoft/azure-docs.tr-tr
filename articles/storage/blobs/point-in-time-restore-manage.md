---
title: Blok Blobu verilerinde bir zaman içinde geri yükleme gerçekleştirin
titleSuffix: Azure Storage
description: Belirli bir noktada bir blok Blobları kümesini önceki durumlarına geri yüklemek için noktadan noktaya geri yüklemeyi nasıl kullanacağınızı öğrenin.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 01/29/2021
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: e8c926c2fbc5b19f67fb78d321ee3293c73be939
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107869356"
---
# <a name="perform-a-point-in-time-restore-on-block-blob-data"></a>Blok Blobu verilerinde bir zaman içinde geri yükleme gerçekleştirin

Blok bloblarının bir veya daha fazla kümesini önceki bir duruma geri yüklemek için zaman noktası geri yüklemesi kullanabilirsiniz. Bu makalede, bir depolama hesabı için noktadan noktaya geri yüklemenin nasıl etkinleştirileceği ve geri yükleme işleminin nasıl gerçekleştirileceği açıklanır.

Noktadan noktaya geri yükleme hakkında daha fazla bilgi edinmek için bkz. [blok Blobları Için noktadan noktaya geri yükleme](point-in-time-restore-overview.md).

> [!CAUTION]
> Zaman içinde geri yükleme, yalnızca blok Bloblarındaki işlemleri geri yüklemeyi destekler. Kapsayıcılardaki işlemler geri yüklenemez. [Kapsayıcıyı silme](/rest/api/storageservices/delete-container) işlemini çağırarak depolama hesabından bir kapsayıcıyı silerseniz, o kapsayıcı geri yükleme işlemiyle geri yüklenemez. Bir kapsayıcının tamamını silmek yerine, daha sonra geri yüklemek istiyorsanız ayrı Blobları silin. Ayrıca, Microsoft, yanlışlıkla silinmeye karşı korunmak için kapsayıcılar ve Blobların geçici silme işlemini etkinleştirmeyi önerir. Daha fazla bilgi için bkz. [kapsayıcılar Için geçici silme (Önizleme)](soft-delete-container-overview.md) ve [Bloblar için geçici silme](soft-delete-blob-overview.md).

## <a name="enable-and-configure-point-in-time-restore"></a>Zaman içinde nokta geri yüklemeyi etkinleştirme ve yapılandırma

Zaman içinde nokta geri yüklemeyi etkinleştirmeden ve yapılandırmadan önce, depolama hesabı için önkoşulları etkinleştirin: geçici silme, akış değiştirme ve BLOB sürümü oluşturma. Bu özelliklerin her birini etkinleştirme hakkında daha fazla bilgi için şu makalelere bakın:

- [Bloblar için geçici silmeyi etkinleştirme](./soft-delete-blob-enable.md)
- [Değişiklik akışını etkinleştirme ve devre dışı bırakma](storage-blob-change-feed.md#enable-and-disable-the-change-feed)
- [Blob sürüm oluşturmayı etkinleştirme ve yönetme](versioning-enable.md)

> [!IMPORTANT]
> Geçici silme, akışı değiştirme ve BLOB sürümü oluşturma özelliğinin etkinleştirilmesi ek ücretler oluşmasına neden olabilir. Daha fazla bilgi için bkz. [Bloblar Için geçici silme](soft-delete-blob-overview.md), [Azure Blob depolamada akış desteğini değiştirme](storage-blob-change-feed.md)ve [BLOB sürümü oluşturma](versioning-overview.md).

# <a name="azure-portal"></a>[Azure portalı](#tab/portal)

Azure portal zaman içinde nokta geri yüklemeyi yapılandırmak için aşağıdaki adımları izleyin:

1. Azure portalda depolama hesabınıza gidin.
1. **Ayarlar** altında **veri koruma**' yı seçin.
1. Belirli bir noktaya geri yüklemeyi **Aç ' ı** seçin. Bu seçeneği belirlediğinizde Bloblar, sürüm oluşturma ve değişiklik akışı için geçici silme de etkinleştirilir.
1. Zaman içinde bir noktaya geri yükleme için en fazla geri yükleme noktasını gün cinsinden ayarlayın. Bu sayı, blob geçici silme için belirtilen saklama süresinden en az bir gün daha az olmalıdır.
1. Yaptığınız değişiklikleri kaydedin.

Aşağıdaki görüntüde, yedi gün önce geri yükleme noktası içeren bir zaman noktası geri yüklemesi için yapılandırılmış bir depolama hesabı ve BLOB geçici silme işlemi için 14 günlük bir bekletme dönemi gösterilmektedir.

:::image type="content" source="media/point-in-time-restore-manage/configure-point-in-time-restore-portal.png" alt-text="Azure portal zaman içinde nokta geri yükleme 'nin nasıl yapılandırılacağını gösteren ekran görüntüsü":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell ile noktadan noktaya geri yüklemeyi yapılandırmak için önce [az. Storage](https://www.powershellgallery.com/packages/Az.Storage) Module sürüm 2.6.0 veya üstünü yükleyin. Ardından, depolama hesabı için noktadan noktaya geri yüklemeyi etkinleştirmek üzere [Enable-AzStorageBlobRestorePolicy](/powershell/module/az.storage/enable-azstorageblobrestorepolicy) komutunu çağırın.

Aşağıdaki örnek, geçici silme ve geçici silme bekletme süresini ayarlar, akış ve sürüm oluşturmayı, sonra da zaman içinde geri yüklemeyi mümkün bir şekilde değiştirir. Örneği çalıştırırken, açılı ayraçlar içindeki değerleri kendi değerlerinizle değiştirmeyi unutmayın:

```powershell
# Set resource group and account variables.
$rgName = "<resource-group>"
$accountName = "<storage-account>"

# Enable blob soft delete with a retention of 14 days.
Enable-AzStorageBlobDeleteRetentionPolicy -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -RetentionDays 14

# Enable change feed and versioning.
Update-AzStorageBlobServiceProperty -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -EnableChangeFeed $true `
    -IsVersioningEnabled $true

# Enable point-in-time restore with a retention period of 7 days.
# The retention period for point-in-time restore must be at least
# one day less than that set for soft delete.
Enable-AzStorageBlobRestorePolicy -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -RestoreDays 7

# View the service settings.
Get-AzStorageBlobServiceProperty -ResourceGroupName $rgName `
    -StorageAccountName $accountName
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLı ile zaman içinde nokta geri yüklemeyi yapılandırmak için önce Azure CLı sürüm 2.2.0 veya üstünü yükleyin. Ardından, depolama hesabı için bir süre sonra geri yüklemeyi ve diğer gerekli veri koruma ayarlarını etkinleştirmek üzere [az Storage Account blob-Service-Properties Update](/cli/azure/storage/account/blob-service-properties#az_storage_account_blob_service_properties_update) komutunu çağırın.

Aşağıdaki örnek, geçici silme işlemi yapmanızı sağlar ve geçici silme bekletme dönemini 14 gün olarak ayarlar, akış ve sürüm oluşturmayı değiştirebilir ve bir geri yükleme süresi olan 7 günlük bir geri yükleme süresi sağlar. Örneği çalıştırırken, açılı ayraçlar içindeki değerleri kendi değerlerinizle değiştirmeyi unutmayın:

```azurecli
az storage account blob-service-properties update \
    --resource-group <resource_group> \
    --account-name <storage-account> \
    --enable-delete-retention true \
    --delete-retention-days 14 \
    --enable-versioning true \
    --enable-change-feed true \
    --enable-restore-policy true \
    --restore-days 7
```

---

## <a name="choose-a-restore-point"></a>Geri yükleme noktası seçin

Geri yükleme noktası, verilerin geri yüklendiği tarih ve saat. Azure Storage her zaman geri yükleme noktası olarak bir UTC Tarih/saat değeri kullanır. Ancak Azure portal, geri yükleme noktasını yerel saat içinde belirtmenizi sağlar ve sonra geri yükleme işlemini gerçekleştirmek için bu tarih/saat değerini UTC Tarih/saat değerine dönüştürür.

PowerShell veya Azure CLı ile geri yükleme işlemi gerçekleştirdiğinizde, geri yükleme noktasını UTC Tarih/saat değeri olarak belirtmeniz gerekir. Geri yükleme noktası UTC saat değeri yerine yerel saat değeri ile belirtilirse, geri yükleme işlemi bazı durumlarda beklenen şekilde hala çalışmayabilir. Örneğin, yerel saatleriniz UTC 'den beş saat ise, yerel saat değeri belirtildiğinde, belirttiğiniz değerin beş saat daha önceki bir geri yükleme noktası elde edilir. Bu beş saatlik dönemde geri yüklenecek aralıktaki verilerde herhangi bir değişiklik yapılmadıysa, geri yükleme işlemi hangi zaman değerinin sağlandığı bağımsız olarak aynı sonuçlara neden olur. Beklenmedik sonuçlara engel olmak için geri yükleme noktası için UTC saati belirtilmesi önerilir.

## <a name="perform-a-restore-operation"></a>Geri yükleme işlemi gerçekleştirme

Depolama hesabındaki tüm kapsayıcıları geri yükleyebilir veya bir veya daha fazla kapsayıcıda bir dizi blobu geri yükleyebilirsiniz. Bir dizi blob, sözlük düzeninde sözcüıgrafik olarak tanımlanır. Her geri yükleme işlemi için On adede kadar lexıgraf aralığı desteklenir. Aralığın başlangıcı dahil değildir ve aralığın sonu hariç olur.

Başlangıç aralığı ve bitiş aralığı için belirtilen kapsayıcı deseninin en az üç karakter içermesi gerekir. Bir blob adından kapsayıcı adını ayırmak için kullanılan eğik çizgi (/), bu en düşük değere doğru sayılmaz.

Bir lexıgraf aralığında joker karakterler desteklenmez. Herhangi bir joker karakter standart karakter olarak değerlendirilir.

`$root` `$web` Geri yükleme işlemine geçirilen bir aralığa açıkça belirtilerek ve kapsayıcılardaki Blobları geri yükleyebilirsiniz. `$root`Ve `$web` kapsayıcıları yalnızca açık olarak belirtilmişse geri yüklenir. Diğer sistem kapsayıcıları geri yüklenemez.

Yalnızca blok Blobları geri yüklenir. Sayfa Blobları ve ekleme Blobları geri yükleme işlemine dahil edilmez. Ekleme Blobları ile ilgili sınırlamalar hakkında daha fazla bilgi için bkz. [blok Blobları Için noktadan noktaya geri yükleme](point-in-time-restore-overview.md).

> [!IMPORTANT]
> Geri yükleme işlemi gerçekleştirdiğinizde, Azure depolama, işlem süresince geri yüklenen aralıklardaki bloblarda veri işlemlerini engeller. Birincil konumda okuma, yazma ve silme işlemleri engellenir. Bu nedenle, Azure portal kapsayıcıları gibi işlemler geri yükleme işlemi devam ederken beklendiği gibi gerçekleştirilemeyebilir.
>
> Depolama hesabı coğrafi olarak çoğaltılırsa, ikincil konumdaki okuma işlemleri geri yükleme işlemi sırasında devam edebilir.
>
> Bir veri kümesini geri yüklemek için geçen süre, geri yükleme döneminde yapılan yazma ve silme işlemlerinin sayısına bağlıdır. Örneğin, 1.000.000 nesne başına 3.000 nesne ve gün başına silinen 1.000 olan bir hesap, geçmişte 30 günlük bir noktaya geri yüklemek için yaklaşık iki saat gerektirir. Geçmişte 90 günden fazla bir bekletme dönemi ve geri yükleme bu değişiklik oranına sahip bir hesap için önerilmez.

### <a name="restore-all-containers-in-the-account"></a>Hesaptaki tüm kapsayıcıları geri yükle

Depolama hesabındaki tüm kapsayıcıları geri yükleyerek belirli bir zaman noktasında bunları önceki durumlarına getirebilirsiniz.

# <a name="azure-portal"></a>[Azure portalı](#tab/portal)

Depolama hesabındaki tüm kapsayıcıları ve Blobları Azure portal geri yüklemek için şu adımları izleyin:

1. Depolama hesabınızın kapsayıcı listesine gidin.
1. Araç çubuğunda **kapsayıcıları geri yükle**' yi ve ardından **Tümünü geri yükle**' yi seçin.
1. **Tüm kapsayıcıları geri yükle** bölmesinde, bir tarih ve saat sağlayarak geri yükleme noktasını belirtin.
1. Kutuyu işaretleyerek devam etmek istediğinizi onaylayın.
1. Geri yükleme işlemini başlatmak için **geri yükle** ' yi seçin.

    :::image type="content" source="media/point-in-time-restore-manage/restore-all-containers-portal.png" alt-text="Tüm kapsayıcıların belirtilen geri yükleme noktasına nasıl geri yükleneceğini gösteren ekran görüntüsü":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Depolama hesabındaki tüm kapsayıcıları ve Blobları PowerShell ile geri yüklemek için **restore-AzStorageBlobRange** komutunu çağırın ve geri yükleme noktasını UTC Tarih/saat değeri olarak belirtin. Varsayılan olarak **restore-AzStorageBlobRange** komutu zaman uyumsuz olarak çalışır ve geri yükleme işleminin durumunu denetlemek Için kullanabileceğiniz **PSBlobRestoreStatus** türünde bir nesne döndürür.

Aşağıdaki örnek, depolama hesabındaki kapsayıcıları, mevcut andan önce 12 saat öncesine zaman uyumsuz olarak geri yükler ve geri yükleme işleminin bazı özelliklerini denetler:

```powershell
# Specify -TimeToRestore as a UTC value
$restoreOperation = Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).ToUniversalTime().AddHours(-12)

# Get the status of the restore operation.
$restoreOperation.Status
# Get the ID for the restore operation.
$restoreOperation.RestoreId
# Get the restore point in UTC time.
$restoreOperation.Parameters.TimeToRestore
```

Geri yükleme işlemini zaman uyumlu olarak çalıştırmak için, komutuna **-waitfortam** parametresini ekleyin. **-Waitfortamamlana** parametresi mevcut olduğunda, PowerShell işlemin GERI yükleme kimliğini içeren bir ileti görüntüler ve ardından geri yükleme işlemi tamamlanana kadar yürütmeyi engeller. Geri yükleme işlemi için gereken süre uzunluğunun geri yüklenecek veri miktarına bağlı olduğunu ve büyük bir geri yükleme işleminin tamamlanması bir saate kadar sürebileceğini aklınızda bulundurun.

```powershell
Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddHours(-12) -WaitForComplete
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Depolama hesabındaki tüm kapsayıcıları ve Blobları Azure CLı ile geri yüklemek için [az Storage blob restore](/cli/azure/storage/blob#az_storage_blob_restore) komutunu çağırın ve geri yükleme noktasını UTC Tarih/saat değeri olarak belirtin.

Aşağıdaki örnek, depolama hesabındaki tüm kapsayıcıları belirli bir tarih ve saatten 12 saat öncesine zaman uyumsuz olarak geri yükler. Geri yükleme işleminin durumunu denetlemek için [az Storage Account Show](/cli/azure/storage/account#az_storage_account_show)öğesini çağırın:

```azurecli
az storage blob restore \
    --resource-group <resource_group> \
    --account-name <storage-account> \
    --time-to-restore 2021-01-14T06:31:22Z \
    --no-wait
```

Geri yükleme işleminin özelliklerini denetlemek için [az Storage Account Show](/cli/azure/storage/account#az_storage_account_show) ve expand **blobRestoreStatus** özelliğini çağırın. Aşağıdaki örnek, **Status** özelliğinin nasıl kontrol alınacağını gösterir.

```azurecli
az storage account show \
    --name <storage-account> \
    --resource-group <resource_group> \ 
    --expand blobRestoreStatus \
    --query blobRestoreStatus.status \
    --output tsv
```

**Az Storage blob restore** komutunu eşzamanlı olarak çalıştırmak ve geri yükleme işlemi tamamlanana kadar yürütmeyi engellemek için, `--no-wait` parametresini atlayın.

---

### <a name="restore-ranges-of-block-blobs"></a>Blok bloblarının aralıklarını geri yükleme

Tek bir kapsayıcı içinde veya birden çok kapsayıcı içindeki blob türlerini bir veya daha fazla geri yükleyebilir ve bu Blobları belirli bir noktada önceki durumlarına döndürebilirsiniz.

# <a name="azure-portal"></a>[Azure portalı](#tab/portal)

Azure portal bir veya daha fazla kapsayıcıda bir blob aralığını geri yüklemek için şu adımları izleyin:

1. Depolama hesabınızın kapsayıcı listesine gidin.
1. Geri yüklenecek kapsayıcıyı veya kapsayıcıları seçin.
1. Araç çubuğunda **kapsayıcıları geri yükle**' yi seçin ve ardından seçili ' ı **geri yükleyin**.
1. **Seçili kapsayıcıları geri yükle** bölmesinde, bir tarih ve saat sağlayarak geri yükleme noktasını belirtin.
1. Geri yüklenecek aralıkları belirtin. Blob önekinden kapsayıcı adını belirtmek için bir eğik çizgi (/) kullanın.
1. Varsayılan olarak, **Seçili kapsayıcıları geri yükle** bölmesi, kapsayıcıdaki tüm Blobları içeren bir aralığı belirtir. Kapsayıcının tamamını geri yüklemek istemiyorsanız bu aralığı silin. Varsayılan Aralık aşağıdaki görüntüde gösterilmiştir.

    :::image type="content" source="media/point-in-time-restore-manage/delete-default-blob-range.png" alt-text="Özel Aralık belirtmeden önce silinecek varsayılan blob aralığını gösteren ekran görüntüsü":::

1. Kutuyu işaretleyerek devam etmek istediğinizi onaylayın.
1. Geri yükleme işlemini başlatmak için **geri yükle** ' yi seçin.

Aşağıdaki görüntüde bir dizi Aralık üzerinde geri yükleme işlemi gösterilmektedir.

:::image type="content" source="media/point-in-time-restore-manage/restore-multiple-container-ranges-portal.png" alt-text="Bir veya daha fazla kapsayıcıda blob aralıklarının nasıl geri yükleneceğini gösteren ekran görüntüsü":::

Görüntüde gösterilen geri yükleme işlemi aşağıdaki eylemleri gerçekleştirir:

- *Kapsayıcı1*'in tüm içeriğini geri yükler.
- *Container2* içindeki *blob5* ile lexıgraf Range *blob1* ile Blobları geri yükler. Bu Aralık, *blob1*, *blob11*, *blob100*, *blob2* vb. gibi adlara sahip Blobları geri yükler. Aralığın sonu dışlamalı olduğu için, adları *blob4* ile başlayan Blobları geri yükler, ancak adları *blob5* ile başlayan Blobları geri yüklemez.
- *Container3* ve *container4* içindeki tüm Blobları geri yükler. Aralığın sonu dışlamalı olduğundan, bu Aralık *container5* geri yüklemez.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Tek bir blob aralığını geri yüklemek için **restore-AzStorageBlobRange** komutunu çağırın ve parametresi için bir lexıgraf kapsayıcı ve BLOB adı belirtin `-BlobRestoreRange` . Örneğin, *kapsayıcı1* adlı tek bir kapsayıcıdaki Blobları geri yüklemek için, *kapsayıcı1* ile başlayan ve *container2* ile biten bir Aralık belirleyebilirsiniz. Başlangıç ve bitiş aralıklarında adlı kapsayıcılar için bir gereksinim yok. Aralığın sonu dışlamalı olduğundan, depolama hesabı *container2* adlı bir kapsayıcı içerse de, yalnızca *kapsayıcı1* adlı kapsayıcı geri yüklenecektir:

```powershell
$range = New-AzStorageBlobRangeToRestore -StartRange container1 `
    -EndRange container2
```

Geri yüklenecek bir kapsayıcıdaki Blobların bir alt kümesini belirtmek için, blob öneki düzeninden kapsayıcı adını ayırmak için bir eğik çizgi (/) kullanın. Örneğin, aşağıdaki Aralık, adları *d* ile *f* ile başlayan tek bir kapsayıcıdaki Blobları seçer:

```powershell
$range = New-AzStorageBlobRangeToRestore -StartRange container1/d `
    -EndRange container1/g
```

Ardından, **geri yükleme-AzStorageBlobRange** komutuna olan aralığı belirtin. Parametre için bir UTC **Tarih saat** değeri sağlayarak geri yükleme noktasını belirtin `-TimeToRestore` . Aşağıdaki örnek, belirtilen aralıktaki Blobları mevcut andan 3 güne geri yükler:

```powershell
# Specify -TimeToRestore as a UTC value
Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -BlobRestoreRange $range `
    -TimeToRestore (Get-Date).AddDays(-3)
```

Varsayılan olarak **restore-AzStorageBlobRange** komutu zaman uyumsuz olarak çalışır. Bir geri yükleme işlemini zaman uyumsuz olarak başlattığınızda, PowerShell hemen işlem için bir özellik tablosu görüntüler:  

```powershell
Status     RestoreId                            FailureReason Parameters.TimeToRestore     Parameters.BlobRanges
------     ---------                            ------------- ------------------------     ---------------------
InProgress 459c2305-d14a-4394-b02c-48300b368c63               2020-09-15T23:23:07.1490859Z ["container1/d" -> "container1/g"]
```

Blok bloblarının birden çok aralığını geri yüklemek için, parametre için bir Aralık dizisi belirtin `-BlobRestoreRange` . Aşağıdaki örnek, *kapsayıcı1* ve *container4* ' nin tüm içeriğini 24 saat önce geri yüklemek için iki aralığı belirtir ve sonucu bir değişkene kaydeder:

```powershell
# Specify a range that includes the complete contents of container1.
$range1 = New-AzStorageBlobRangeToRestore -StartRange container1 `
    -EndRange container2
# Specify a range that includes the complete contents of container4.
$range2 = New-AzStorageBlobRangeToRestore -StartRange container4 `
    -EndRange container5

$restoreOperation = Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddHours(-24) `
    -BlobRestoreRange @($range1, $range2)

# Get the status of the restore operation.
$restoreOperation.Status
# Get the ID for the restore operation.
$restoreOperation.RestoreId
# Get the blob ranges specified for the operation.
$restoreOperation.Parameters.BlobRanges
```

Geri yükleme işlemini eşzamanlı olarak çalıştırmak ve tamamlanana kadar yürütmeyi engellemek için, komutuna **-waitfortam** parametresini ekleyin.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Blob 'ların bir aralığını geri yüklemek için [az Storage blob restore](/cli/azure/storage/blob#az_storage_blob_restore) komutunu çağırın ve parametresi için bir lexicografik aralığı ve BLOB adı belirtin `--blob-range` . Birden çok Aralık belirtmek için, `--blob-range` her ayrı aralığın parametresini sağlayın.

Örneğin, *kapsayıcı1* adlı tek bir kapsayıcıdaki Blobları geri yüklemek için, *kapsayıcı1* ile başlayan ve *container2* ile biten bir Aralık belirleyebilirsiniz. Başlangıç ve bitiş aralıklarında adlı kapsayıcılar için bir gereksinim yok. Aralığın sonu dışlamalı olduğundan, depolama hesabı *container2* adlı bir kapsayıcı içerse bile, yalnızca *kapsayıcı1* adlı kapsayıcı geri yüklenecektir.

Geri yüklenecek bir kapsayıcıdaki Blobların bir alt kümesini belirtmek için, blob öneki düzeninden kapsayıcı adını ayırmak için bir eğik çizgi (/) kullanın. Aşağıda gösterilen örnek, zaman uyumsuz olarak adları ile başlayan bir kapsayıcıdaki Blobları bir aralıktaki geri yükler `d` `f` .

```azurecli
az storage blob restore \
    --account-name <storage-account> \
    --time-to-restore 2021-01-14T06:31:22Z \
    --blob-range container1 container2
    --blob-range container3/d container3/g
    --no-wait
```

**Az Storage blob restore** komutunu eşzamanlı olarak çalıştırmak ve geri yükleme işlemi tamamlanana kadar yürütmeyi engellemek için, `--no-wait` parametresini atlayın.

---

## <a name="next-steps"></a>Sonraki adımlar

- [Blok Blobları için noktadan noktaya geri yükleme](point-in-time-restore-overview.md)
- [Geçici silme](./soft-delete-blob-overview.md)
- [Akışı Değiştir](storage-blob-change-feed.md)
- [Blob sürümü oluşturma](versioning-overview.md)