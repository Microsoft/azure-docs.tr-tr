---
title: Azure dosya paylaşımı oluşturma
titleSuffix: Azure Files
description: Azure portal, PowerShell veya Azure CLı kullanarak Azure dosya paylaşma oluşturma.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 04/05/2021
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurecli, references_regions
ms.openlocfilehash: 9caabb8dc7f09e4ef3852d9269d178c086744779
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107789816"
---
# <a name="create-an-azure-file-share"></a>Azure dosya paylaşımı oluşturma
Bir Azure dosya paylaşımının oluşturulması için, nasıl kullanacağınız hakkında üç soruyu yanıtlamanız gerekir:

- **Azure dosya paylaşımınıza ilişkin performans gereksinimleri nelerdir?**  
    Azure dosyaları, sabit disk tabanlı (HDD tabanlı) donanımda barındırılan standart dosya paylaşımları ve katı hal disk tabanlı (SSD tabanlı) donanımda barındırılan Premium dosya paylaşımları sunar.

- **Azure dosya paylaşımınıza yönelik yedeklilik gereksinimleriniz nelerdir?**  
    Standart dosya paylaşımları yerel olarak yedekli (LRS), bölgesel olarak yedekli (ZRS), coğrafi olarak yedekli (GRS) veya coğrafi bölge-yedekli (GZRS) depolama alanı sunar, ancak büyük dosya paylaşımı özelliği yalnızca yerel olarak yedekli ve bölge yedekli dosya paylaşımlarında desteklenir. Premium dosya paylaşımları hiçbir coğrafi artıklık biçimini desteklemez.

    Premium dosya paylaşımları, bölgelerin bir alt kümesinde yerel olarak artıklık ve bölge yedekliliği ile kullanılabilir. Premium dosya paylaşımlarının bölgede şu anda kullanılabilir olup olmadığını öğrenmek için bkz. Azure için [bölgeye göre kullanılabilir ürünler](https://azure.microsoft.com/global-infrastructure/services/?products=storage) sayfası. ZRS 'yi destekleyen bölgeler hakkında daha fazla bilgi için bkz. [Azure Storage yedekliği](../common/storage-redundancy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

- **Dosya paylaşımıyla ne kadar boyut gerekir?**  
    Yerel ve bölgesel olarak yedekli depolama hesaplarında Azure dosya paylaşımları 100 TiB 'ye kadar yayılabilir, ancak coğrafi ve coğrafi bölge yedekli depolama hesaplarında Azure dosya paylaşımları yalnızca 5 TiB 'ye kadar yayılabilir. 

Bu üç seçenek hakkında daha fazla bilgi için bkz. [Azure dosyaları dağıtımı Için planlama](storage-files-planning.md).

## <a name="prerequisites"></a>Önkoşullar
- Bu makalede, zaten bir Azure aboneliği oluşturmuş olduğunuz varsayılmaktadır. Aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.
- Azure PowerShell kullanmayı düşünüyorsanız, [en son sürümü yükleyebilirsiniz](/powershell/azure/install-az-ps).
- Azure CLı 'yı kullanmayı planlıyorsanız [en son sürümü yükleyebilirsiniz](/cli/azure/install-azure-cli).

## <a name="create-a-storage-account"></a>Depolama hesabı oluşturma
Azure dosya paylaşımları, paylaşılan bir depolama havuzunu temsil eden üst düzey nesneler olan *depolama hesaplarına* dağıtılır. Bu depolama havuzu, birden çok dosya paylaşımını dağıtmak için kullanılabilir. 

Azure, müşterilerin sahip olabileceği farklı depolama senaryoları için birden çok depolama hesabı türünü destekler, ancak Azure dosyaları için iki ana depolama hesabı türü vardır. Oluşturmanız gereken depolama hesabı türü, standart bir dosya paylaşma veya Premium dosya paylaşımının oluşturulmasını isteyip istemediğinize bağlıdır: 

- **Genel amaçlı sürüm 2 (GPv2) depolama hesapları**: GPv2 depolama hesapları, Azure dosya paylaşımlarını standart/sabit disk tabanlı (HDD tabanlı) donanımda dağıtmanıza olanak tanır. GPv2 depolama hesapları, Azure dosya paylaşımlarını depolamanın yanı sıra blob kapsayıcıları, kuyrukları veya tabloları gibi diğer depolama kaynaklarını da depolayabilirler. Dosya paylaşımları, işlem için iyileştirilmiş (varsayılan), sık veya seyrek katmanlara dağıtılabilir.

- **FileStorage depolama hesapları**: FileStorage depolama hesapları, Azure dosya paylaşımlarını Premium/katı hal disk tabanlı (SSD tabanlı) donanımda dağıtmanıza olanak tanır. FileStorage hesapları yalnızca Azure dosya paylaşımlarını depolamak için kullanılabilir; başka depolama kaynakları (blob kapsayıcıları, kuyruklar, tablolar vb.) bir FileStorage hesabında dağıtılabilir.

# <a name="portal"></a>[Portal](#tab/azure-portal)
Azure portal aracılığıyla bir depolama hesabı oluşturmak için panodan **+ kaynak oluştur** ' u seçin. Elde edilen Azure Marketi araması penceresinde, **depolama hesabı** ' nı arayın ve elde edilen arama sonucunu seçin. Bu, depolama hesapları için bir genel bakış sayfasına yol açacaktır; depolama hesabı oluşturma Sihirbazı ile devam etmek için **Oluştur** ' u seçin.

![Tarayıcıda depolama hesabı hızlı oluşturma seçeneğinin ekran görüntüsü](media/storage-how-to-create-file-share/create-storage-account-0.png)

#### <a name="basics"></a>Temel Bilgiler
Bir depolama hesabı oluşturmak için tamamlanacak ilk bölüm **temel bilgiler** olarak etiketlenir. Bu, depolama hesabı oluşturmak için gerekli tüm alanları içerir. GPv2 depolama hesabı oluşturmak için, **performans** radyo düğmesinin *Standart* olarak ayarlandığından ve **Hesap türü** açılan listesinin *StorageV2 (genel amaçlı v2)* olarak seçildiğinden emin olun.

:::image type="content" source="media/storage-how-to-create-file-share/files-create-smb-share-performance-standard.png" alt-text="Standart seçili ve hesap türü storagev2 seçiliyken performans radyo düğmesinin ekran görüntüsü.":::

Bir FileStorage depolama hesabı oluşturmak için **performans** radyo düğmesinin *Premium* olarak ayarlandığından ve **Premium hesap türü** aşağı açılan listesinde **dosya paylaşımları** seçildiğinden emin olun.

:::image type="content" source="media/storage-how-to-create-file-share/files-create-smb-share-performance-premium.png" alt-text="Premium seçiliyken ve dosya depolama seçiliyken hesap türüyle performans radyo düğmesinin ekran görüntüsü.":::

Diğer temelleri alanları, depolama hesabı seçiminizden bağımsızdır:
- **Depolama hesabı adı**: oluşturulacak depolama hesabı kaynağının adı. Bu ad genel olarak benzersiz olmalıdır, aksi takdirde istediğiniz ad olabilir. SMB aracılığıyla bir Azure dosya paylaşımının bağladığınızda, depolama hesabı adı sunucu adı olarak kullanılır.
- **Konum**: dağıtım yapılacak depolama hesabının bölgesi. Bu, kaynak grubuyla ilişkili bölge veya kullanılabilir başka bir bölge olabilir.
- **Çoğaltma**: Bu, çoğaltma olarak etiketlendiği halde bu alan aslında **yedeklilik** anlamına gelir; Bu, istenen artıklık düzeyindedir: yerel olarak yedekli (LRS), bölge artıklığı (ZRS), coğrafi yedeklilik (GRS) ve coğrafi bölge-artıklığı (GZRS). Bu açılan liste ayrıca, Azure dosya paylaşımları için uygulanan okuma erişimli coğrafi yedeklilik (RA-GRS) ve Okuma Erişimli Coğrafi bölge yedekliliği (RA-GZRS) içerir; Bu seçilenlerle bir depolama hesabında oluşturulan herhangi bir dosya paylaşımının sırasıyla coğrafi olarak yedekli veya coğrafi bölge-yedekli olması gerekir. 

#### <a name="networking"></a>Ağ
Ağ bölümü, ağ seçeneklerini yapılandırmanızı sağlar. Bu ayarlar, depolama hesabının oluşturulması için isteğe bağlıdır ve isterseniz daha sonra yapılandırılabilir. Bu seçenekler hakkında daha fazla bilgi için bkz. [Azure dosyaları ağ iletişimi konuları](storage-files-networking-overview.md).

#### <a name="data-protection"></a>Veri koruma
Veri koruma bölümü, Depolama hesabınızdaki Azure dosya paylaşımları için geçici silme ilkesini yapılandırmanıza olanak tanır. Blob 'lar, kapsayıcılar için geçici silme ile ilgili diğer ayarlar, kapsayıcılar, sürüm oluşturma ve değişiklik akışı için yalnızca Azure Blob depolama için geçerlidir.

#### <a name="advanced"></a>Gelişmiş
Gelişmiş bölüm, Azure dosya paylaşımları için çeşitli önemli ayarlar içerir:

- **Güvenli aktarım gerekli**: Bu alan, depolama hesabının depolama hesabıyla iletişim için şifreleme gerektirip gerektirmediğini belirtir. SMB 2,1 desteğine ihtiyacınız varsa, bunu devre dışı bırakmanız gerekir.

    :::image type="content" source="media/storage-how-to-create-file-share/files-create-smb-share-secure-transfer.png" alt-text="Depolama hesabı için Gelişmiş ayarlarda etkin olan güvenli aktarım ekran görüntüsü.":::

- **Büyük dosya paylaşımları**: Bu alan, 100 TİB 'ye yayılan dosya paylaşımları için depolama hesabı sağlar. Bu özelliği etkinleştirmek, depolama hesabınızı yalnızca yerel olarak yedekli ve bölgesel olarak yedekli depolama seçenekleriyle sınırlandırır. Büyük dosya paylaşımları için GPv2 depolama hesabı etkinleştirildikten sonra, büyük dosya paylaşımı özelliğini devre dışı bırakayükleyemezsiniz. Tüm Premium dosya paylaşımları 100 TiB 'ye kadar ölçeklenebileceğinden, FileStorage depolama hesapları (Premium dosya paylaşımları için depolama hesapları) bu seçeneğe sahip değildir. 

    :::image type="content" source="media/storage-how-to-create-file-share/files-create-smb-share-large-file-shares.png" alt-text="Depolama hesabının gelişmiş dikey penceresindeki büyük dosya paylaşma ayarının ekran görüntüsü.":::

Gelişmiş sekmesinde bulunan diğer ayarlar (Azure Data Lake Storage Gen 2 için hiyerarşik ad alanı, varsayılan blob katmanı, NFSv3 for blob Storage vb.) Azure dosyaları için uygulanmaz.

> [!Important]  
> Blob erişim katmanının seçilmesi dosya paylaşımının katmanını etkilemez.

#### <a name="tags"></a>Etiketler
Etiketler, birden fazla kaynağa ve kaynak grubuna aynı etiketi uygulayarak kaynakları kategorilere ayırmanıza ve birleştirilmiş faturalandırmayı görüntülemenize olanak tanıyan ad/değer çiftleridir. Bunlar isteğe bağlıdır ve depolama hesabı oluşturulduktan sonra uygulanabilir.

#### <a name="review--create"></a>Gözden geçirme ve oluşturma
Depolama hesabı oluşturmaya yönelik son adım, **gözden geçir + oluştur** sekmesinde **Oluştur** düğmesini seçmesidir. Bir depolama hesabı için gerekli tüm alanlar doldurulmamışsa bu düğme kullanılamaz.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
PowerShell kullanarak bir depolama hesabı oluşturmak için `New-AzStorageAccount` cmdlet 'ini kullanacağız. Bu cmdlet 'in birçok seçeneği vardır; yalnızca gerekli seçenekler gösterilir. Gelişmiş seçenekler hakkında daha fazla bilgi edinmek için [ `New-AzStorageAccount` cmdlet belgelerine](/powershell/module/az.storage/new-azstorageaccount)bakın.

Depolama hesabının ve sonraki dosya paylaşımının oluşturulmasını basitleştirmek için, çeşitli parametreleri değişkenlerde depolayacağız. Değişken içeriğini istediğiniz değerle değiştirebilirsiniz, ancak depolama hesabı adının genel olarak benzersiz olması gerektiğini unutmayın.

```powershell
$resourceGroupName = "myResourceGroup"
$storageAccountName = "mystorageacct$(Get-Random)"
$region = "westus2"
```

Standart Azure dosya paylaşımlarını depolayabilen bir depolama hesabı oluşturmak için aşağıdaki komutu kullanacağız. `-SkuName`Parametresi, istenen artıklık türü ile ilgilidir; coğrafi olarak yedekli veya coğrafi bölge yedekli depolama hesabı istiyorsanız parametreyi de kaldırmanız gerekir `-EnableLargeFileShare` .

```powershell
$storAcct = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -SkuName Standard_LRS `
    -Location $region `
    -Kind StorageV2 `
    -EnableLargeFileShare
```

Premium Azure dosya paylaşımlarını depolayabilen bir depolama hesabı oluşturmak için aşağıdaki komutu kullanacağız. `-SkuName`Parametresinin hem hem de `Premium` istenen artıklık düzeyini yerel olarak yedekli () içerecek şekilde değiştirildiğini unutmayın `LRS` . `-Kind` `FileStorage` Bunun yerine, `StorageV2` bir GPv2 depolama hesabı yerine Premium dosya paylaşımlarının bir FileStorage depolama hesabında oluşturulması gerekir.

```powershell
$storAcct = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -SkuName Premium_LRS `
    -Location $region `
    -Kind FileStorage 
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Azure CLı kullanarak bir depolama hesabı oluşturmak için az Storage Account Create komutunu kullanacağız. Bu komutun birçok seçeneği vardır; yalnızca gerekli seçenekler gösterilir. Gelişmiş seçenekler hakkında daha fazla bilgi için, bkz. [ `az storage account create` komut belgeleri](/cli/azure/storage/account).

Depolama hesabının ve sonraki dosya paylaşımının oluşturulmasını basitleştirmek için, çeşitli parametreleri değişkenlerde depolayacağız. Değişken içeriğini istediğiniz değerle değiştirebilirsiniz, ancak depolama hesabı adının genel olarak benzersiz olması gerektiğini unutmayın.

```azurecli
resourceGroupName="myResourceGroup"
storageAccountName="mystorageacct$RANDOM"
region="westus2"
```

Standart Azure dosya paylaşımlarını depolayabilen bir depolama hesabı oluşturmak için aşağıdaki komutu kullanacağız. `--sku`Parametresi, istenen artıklık türü ile ilgilidir; coğrafi olarak yedekli veya coğrafi bölge yedekli depolama hesabı istiyorsanız parametreyi de kaldırmanız gerekir `--enable-large-file-share` .

```azurecli
az storage account create \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --kind StorageV2 \
    --sku Standard_ZRS \
    --enable-large-file-share \
    --output none
```

Premium Azure dosya paylaşımlarını depolayabilen bir depolama hesabı oluşturmak için aşağıdaki komutu kullanacağız. `--sku`Parametresinin hem hem de `Premium` istenen artıklık düzeyini yerel olarak yedekli () içerecek şekilde değiştirildiğini unutmayın `LRS` . `--kind` `FileStorage` Bunun yerine, `StorageV2` bir GPv2 depolama hesabı yerine Premium dosya paylaşımlarının bir FileStorage depolama hesabında oluşturulması gerekir.

```azurecli
az storage account create \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --kind FileStorage \
    --sku Premium_LRS \
    --output none
```

---

## <a name="create-a-file-share"></a>Dosya paylaşımı oluşturma
Depolama hesabınızı oluşturduktan sonra, dosya paylaşımınızın oluşturulması gerekir. Bu işlem, genellikle bir Premium dosya paylaşımının veya standart dosya paylaşımının kullanılıp kullanılmadığından bağımsız olarak aynıdır. Aşağıdaki farklılıkları göz önünde bulundurmanız gerekir.

Standart dosya paylaşımları standart katmanlardan birine dağıtılabilir: işlem için iyileştirilmiş (varsayılan), sık veya seyrek. Bu, depolama hesabının **BLOB erişim katmanından** etkilenmemiş bir dosya paylaşma katmanıdır (Bu özellik yalnızca Azure Blob depolama ile Ilgilidir; Azure dosyalarıyla ilişkili değildir). Dağıtım katmanını dağıtıldıktan sonra istediğiniz zaman değiştirebilirsiniz. Premium dosya paylaşımları doğrudan herhangi bir Standart katmana dönüştürülemez.

> [!Important]  
> GPv2 depolama hesabı türleri içindeki katmanlar arasında dosya paylaşımlarını taşıyabilirsiniz (işlem için iyileştirilmiş, sık erişimli ve seyrek erişimli). Katmanlar arasında paylaşma işlemleri işlemler arası işlemler: bir Hotter katmanından daha soğuk bir katmana geçiş yapmak, daha soğuk bir katmandan bir dosya için daha soğuk katman yazma işlemi ücreti uygular, ancak daha soğuk bir katmanda bulunan her dosya için seyrek katman okuma işlemi ücreti olur.

**Quota** özelliği, Premium ve standart dosya paylaşımları arasında biraz farklılık gösterir:

- Standart dosya paylaşımları için bu, son kullanıcıların gidebileceği Azure dosya paylaşımının üst sınırıdır. Bir kota belirtilmemişse standart dosya paylaşımı 100 TiB 'ye yayılabilir (veya büyük dosya paylaşımları özelliği bir depolama hesabı için ayarlanmamışsa, 5 TiB).

- Premium dosya paylaşımları için kota **sağlanan boyut** anlamına gelir. Sağlanan boyut, fiili kullanımdan bağımsız olarak faturalandırılacaksınız. Premium dosya paylaşımının nasıl planlanacağı hakkında daha fazla bilgi için bkz. [Premium dosya paylaşımlarını sağlama](understanding-billing.md#provisioned-model).

# <a name="portal"></a>[Portal](#tab/azure-portal)
Depolama hesabınızı yeni oluşturduysanız, **Kaynağa Git**' i seçerek dağıtım ekranından bu sayfaya gidebilirsiniz. Depolama hesabında bir kez, depolama hesabı için içerik tablosundaki **dosya paylaşımlarını** seçin.

Dosya paylaşımı listesinde, bu depolama hesabında daha önce oluşturduğunuz herhangi bir dosya paylaşımını görmeniz gerekir; henüz bir dosya paylaşımı oluşturulmadıysa boş bir tablo. Yeni bir dosya paylaşma oluşturmak için **+ dosya paylaşma** ' yı seçin.

Yeni dosya paylaşma dikey penceresi ekranda görünmelidir. Yeni dosya paylaşma dikey penceresindeki alanları tamamlayarak bir dosya paylaşma oluşturun:

- **Ad**: oluşturulacak dosya paylaşımının adı.
- **Kota**: Standart dosya paylaşımları için dosya paylaşımının kotası; Premium dosya paylaşımları için dosya paylaşımının sağlanan boyutu.
- **Katmanlar**: bir dosya paylaşımının seçili katmanı. Bu alan yalnızca **genel amaçlı (GPv2) depolama hesabında** kullanılabilir. İşlem için iyileştirilmiş, sık veya seyrek seçim yapabilirsiniz. Paylaşımın katmanı herhangi bir zamanda değiştirilebilir. Geçiş işlemi tamamlandıktan sonra istenirse, işlem giderlerini en aza indirmek ve sonra isterseniz daha düşük bir katmana geçiş yapmak için, geçiş sırasında mümkün olan en yoğun katmanını seçmeniz önerilir.

Yeni paylaşımın oluşturulmasını bitirilirken **Oluştur** ' u seçin.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Cmdlet 'i ile bir Azure dosya paylaşma oluşturabilirsiniz [`New-AzRmStorageShare`](/powershell/module/az.storage/New-AzRmStorageShare) . Aşağıdaki PowerShell komutları, `$resourceGroupName` `$storageAccountName` Azure PowerShell ile depolama hesabı oluşturma bölümünde değişkenleri ve yukarıda tanımlanan şekilde ayarlamış olduğunuz varsayılır. 

Aşağıdaki örnek, parametresini kullanarak bir açık katmanla bir dosya paylaşımının oluşturulmasını gösterir `-AccessTier` . Bu, örnekte gösterildiği gibi Önizleme az. Storage Module kullanılmasını gerektirir. Bir katman belirtilmemişse, GA az. Storage modülünü kullandığınız için ya da bu komutu dahil olmadığınızdan, standart dosya paylaşımları için varsayılan katman işlem için iyileştirilmiştir.

> [!Important]  
> Premium dosya paylaşımları için parametresi, `-QuotaGiB` dosya paylaşımının sağlanan boyutunu ifade eder. Dosya paylaşımının sağlanan boyutu, kullanımdan bağımsız olarak, faturalandırılacak tutardır. Standart dosya paylaşımları, sağlanan boyut yerine kullanıma göre faturalandırılır.

```powershell
# Assuming $resourceGroupName and $storageAccountName from earlier in this document have already
# been populated. The access tier parameter may be TransactionOptimized, Hot, or Cool for GPv2 
# storage accounts. Standard tiers are only available in standard storage accounts. 
$shareName = "myshare"

New-AzRmStorageShare `
        -ResourceGroupName $resourceGroupName `
        -StorageAccountName $storageAccountName `
        -Name $shareName `
        -AccessTier TransactionOptimized `
        -QuotaGiB 1024 | `
    Out-Null
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Komutuyla bir Azure dosya paylaşma oluşturabilirsiniz [`az storage share-rm create`](/cli/azure/storage/share-rm#az_storage_share_rm_create) . Aşağıdaki Azure CLı komutları, `$resourceGroupName` `$storageAccountName` Azure CLI ile depolama hesabı oluşturma bölümünde değişkenleri ve yukarıda tanımlanan şekilde ayarlamış olduğunuz varsayılır.

> [!Important]  
> Premium dosya paylaşımları için parametresi, `--quota` dosya paylaşımının sağlanan boyutunu ifade eder. Dosya paylaşımının sağlanan boyutu, kullanımdan bağımsız olarak, faturalandırılacak tutardır. Standart dosya paylaşımları, sağlanan boyut yerine kullanıma göre faturalandırılır.

```azurecli
shareName="myshare"

az storage share-rm create \
    --resource-group $resourceGroupName \
    --storage-account $storageAccountName \
    --name $shareName \
    --access-tier "TransactionOptimized" \
    --quota 1024 \
    --output none
```

---

> [!Note]  
> Dosya paylaşımınızın adı küçük harflerden oluşmalıdır. Dosya paylaşımlarının ve dosyaların adlandırılması hakkında tüm ayrıntılara ulaşmak için bkz. [Paylaşım, dizin, dosya ve meta verileri adlandırma ve bunlara başvuruda bulunma](/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata).

### <a name="change-the-tier-of-an-azure-file-share"></a>Azure dosya paylaşımının katmanını değiştirme
**Genel amaçlı v2 (GPv2) depolama hesabında** dağıtılan dosya paylaşımları, işlem için iyileştirilmiş, sık erişimli veya Cool katmanlarında olabilir. Azure dosya paylaşımının katmanını, yukarıda açıklanan işlem maliyetlerine bağlı olarak dilediğiniz zaman değiştirebilirsiniz.

# <a name="portal"></a>[Portal](#tab/azure-portal)
Ana depolama hesabı sayfasında **dosya paylaşımları**  ' nı seçin **dosya paylaşımları** etiketli kutucuğu seçin (depolama hesabı Için içindekiler tablosu aracılığıyla **dosya paylaşımlarına** da gidebilirsiniz).

:::image type="content" source="media/storage-files-quick-create-use-windows/click-files.png" alt-text="Depolama hesabı dikey penceresinin ekran görüntüsü, dosya paylaşımları seçildi.":::

Dosya paylaşımlarının Tablo listesinde, katmanını değiştirmek istediğiniz dosya paylaşımını seçin. Dosya paylaşımının Genel Bakış sayfasında, menüden **Katmanı Değiştir** ' i seçin.

![Dosya paylaşma Genel Bakış sayfasının Katmanı Değiştir düğmesi vurgulanmış bir ekran görüntüsü](media/storage-how-to-create-file-share/change-tier-0.png)

Elde edilen iletişim kutusunda istenen katmanı seçin: işlem için iyileştirilmiş, sık erişimli veya seyrek erişimli.

![Katmanı Değiştir iletişim kutusunun ekran görüntüsü](media/storage-how-to-create-file-share/change-tier-1.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Aşağıdaki PowerShell cmdlet 'i, `$resourceGroupName` `$storageAccountName` `$shareName` Bu belgenin önceki bölümlerinde açıklandığı gibi, değişkenlerini ayarlamış olduğunuzu varsayar.

```PowerShell
Update-AzRmStorageShare `
    -ResourceGroupName $resourceGroupName `
    -StorageAccountName $storageAccountName `
    -Name $shareName `
    -AccessTier Cool
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Aşağıdaki Azure CLı komutu `$resourceGroupName` , `$storageAccountName` `$shareName` Bu belgenin önceki bölümlerinde açıklandığı gibi,, ve değişkenlerini ayarlamış olduğunuzu varsayar.

```azurecli
az storage share-rm update \
    --resource-group $resourceGroupName \
    --storage-account $storageAccountName \
    --name $shareName \
    --access-tier "Cool"
```

---

## <a name="next-steps"></a>Sonraki adımlar
- [Azure dosyaları dağıtımını planlayın](storage-files-planning.md) veya [Azure dosya eşitleme dağıtımını planlayın](../file-sync/file-sync-planning.md). 
- [Ağa genel bakış](storage-files-networking-overview.md).
- [Windows](storage-how-to-use-files-windows.md), [MacOS](storage-how-to-use-files-mac.md)ve [Linux](storage-how-to-use-files-linux.md)'ta bir dosya paylaşımının bağlantısını yapın ve bağlayın.