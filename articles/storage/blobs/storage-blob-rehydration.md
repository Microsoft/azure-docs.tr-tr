---
title: Arşiv katmanından blob verilerini yeniden doldurma
description: Blob verilerine erişebilmek için bloblarınızı arşiv depolamadan yeniden doldurma. Arşivlenmiş bir blobu çevrimiçi katmana kopyalayın.
services: storage
author: twooley
ms.author: twooley
ms.date: 03/11/2021
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: hux
ms.openlocfilehash: aaea21dca5304a7a75b24bd7f974712db38d1815
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106276782"
---
# <a name="rehydrate-blob-data-from-the-archive-tier"></a>Arşiv katmanından blob verilerini yeniden doldurma

Blob, arşiv erişim katmanında olduğunda çevrimdışı olarak kabul edilir ve okunamaz veya değiştirilemez. Blob meta verileri çevrimiçi ve kullanılabilir durumda kalır ve bu da blobu ve özelliklerini listelemenize olanak sağlar. Blob verilerini okuma ve değiştirme yalnızca sık erişimli veya seyrek erişimli gibi çevrimiçi katmanlarla kullanılabilir. Arşiv erişim katmanında depolanan verileri almak ve erişmek için iki seçenek vardır.

1. [Arşivlenmiş bir blob 'u çevrimiçi katmana yeniden](#rehydrate-an-archived-blob-to-an-online-tier) doldurma- [BLOB katmanını ayarla](/rest/api/storageservices/set-blob-tier) işlemini kullanarak katmanını değiştirerek sık veya seyrek erişimli bir arşiv blobu.
2. [Arşivlenmiş bir blobu çevrimiçi katmana kopyalama](#copy-an-archived-blob-to-an-online-tier) - [BLOB kopyalama](/rest/api/storageservices/copy-blob) işlemini kullanarak arşiv blobunun yeni bir kopyasını oluşturun. Farklı bir blob adı ve sık erişimli veya seyrek erişimli hedef katmanı belirtin.

 Katmanlar hakkında daha fazla bilgi için bkz. [Azure Blob depolama: sık erişimli, seyrek erişimli ve arşiv erişim katmanları](storage-blob-storage-tiers.md).

## <a name="rehydrate-an-archived-blob-to-an-online-tier"></a>Arşivlenmiş bir Blobun çevrimiçi bir katmana yeniden doldurma

[!INCLUDE [storage-blob-rehydration](../../../includes/storage-blob-rehydrate-include.md)]

### <a name="lifecycle-management"></a>Yaşam döngüsü yönetimi

Blob 'u yeniden doldurma, `Last-Modified` zamanı değiştirmez. [Yaşam döngüsü yönetimi](storage-lifecycle-management-concepts.md) özelliğinin kullanılması, bir Blobun yeniden Sarmalanan bir senaryo oluşturabilir, sonra `Last-Modified` zaman, ilke için eşik kümesinin ötesinde bir yaşam döngüsü yönetimi ilkesi, blobu arşive geri taşımalıdır. Bu senaryoyu önlemek için, *[arşivlenmiş bir blobu bir çevrimiçi katmana kopyalama](#copy-an-archived-blob-to-an-online-tier)* yöntemine kullanın. Copy yöntemi, bir blob 'un güncelleştirilmiş bir zamana sahip yeni bir örneğini oluşturur `Last-Modified` ve yaşam döngüsü yönetim ilkesini tetiklemez.

## <a name="monitor-rehydration-progress"></a>İzleme yeniden doldurma ilerlemesi

Yeniden doldurma sırasında, **Arşiv durumu** özniteliğini denetlemek için blob özelliklerini Al işlemini kullanın ve katman değişikliğinin ne zaman tamamlandığını onaylayın. Durum, hedef katmana göre "rehydrate-pending-to-hot" veya "rehydrate-pending-to-cool" olabilir. Tamamlandıktan sonra arşiv durumu özelliği kaldırılır ve **Erişim Katmanı** blob özelliği sık veya seyrek erişimli bu yeni katmanı gösterir.

## <a name="copy-an-archived-blob-to-an-online-tier"></a>Arşivlenmiş blobu çevrimiçi katmana kopyalama

Arşiv blobundan yeniden doldurma yapmak istemiyorsanız, bir [blobu kopyalama](/rest/api/storageservices/copy-blob) işlemi yapabilirsiniz. İş üzerinde çalışmanız için çevrimiçi sık erişimli veya seyrek erişimli katmanda yeni bir blob oluşturulduğunda, özgün Blobun arşiv 'de değiştirilmemiş olarak kalır. Blobu **kopyalama** işleminde, blob kopyanızın oluşturulmasını istediğiniz önceliği belirtmek için isteğe bağlı *x-MS-rehibulunan öncelik* özelliğini standart veya yüksek olarak ayarlayabilirsiniz.

Bir Blobun arşivden kopyalanması, seçili olan yeniden doldurma önceliğine bağlı olarak tamamlanması saatler sürebilir. Arka planda **kopyalama blobu** işlemi, seçili hedef katmanda yeni bir çevrimiçi blob oluşturmak için Arşiv kaynak blobunu okur. Blob 'ları listelediğinizde yeni blob görünebilir, ancak kaynak arşiv blobundan okuma tamamlanana ve veriler yeni çevrimiçi hedef bloba yazıldıktan sonra veriler kullanılamaz. Yeni blob bağımsız bir kopya olarak ve üzerinde yapılan herhangi bir değişiklik veya silme, kaynak Arşivi blob 'unu etkilemez.

> [!IMPORTANT]
> Hedef üzerinde kopyalama başarıyla tamamlanana kadar kaynak blobu silmeyin. Kaynak blobu silinirse, hedef blobu kopyalamayı tamamlamayabilir ve boş olur. Kopyalama işleminin durumunu öğrenmek için *x-MS-Copy-Status durumunu* kontrol edebilirsiniz.

Arşiv blob 'ları yalnızca aynı depolama hesabı içindeki çevrimiçi hedef katmanlara kopyalanabilir. Bir arşiv blobunun başka bir arşiv blobuna kopyalanması desteklenmez. Aşağıdaki tabloda, **kopyalama blobu** işleminin özellikleri gösterilmektedir.

|                                           | **Etkin katman kaynağı**   | **Cool katmanı kaynağı** | **Arşiv katmanı kaynağı**    |
| ----------------------------------------- | --------------------- | -------------------- | ------------------- |
| **Etkin katman hedefi**                  | Desteklenir             | Desteklenir            | Aynı hesap içinde desteklenir; bekleyen yeniden doldurma               |
| **Cool katmanı hedefi**                 | Desteklenir             | Desteklenir            | Aynı hesap içinde desteklenir; bekleyen yeniden doldurma               |
| **Arşiv katmanı hedefi**              | Desteklenir             | Desteklenir            | Desteklenmeyen         |

## <a name="pricing-and-billing"></a>Fiyatlandırma ve Faturalama

Blob 'ların arşiv dışına, sık veya seyrek erişimli katmanlara yeniden doldurma işlemi, okuma işlemleri ve veri alımı olarak ücretlendirilir. Yüksek önceliğin kullanılması, standart önceliğe kıyasla daha yüksek işlem ve veri alımı maliyetlerine sahiptir. Yüksek öncelikli yeniden doldurma işlemi faturanızda ayrı bir satır öğesi olarak gösterilir. Birkaç gigabaytlık bir arşiv blobu döndürmek için yüksek öncelikli bir istek 5 saatten fazla sürerse, yüksek öncelikli alma ücreti üzerinden ücretlendirilmezsiniz. Ancak, yeniden doldurma diğer isteklere göre önceliklendirildiğinden standart alma ücretleri yine de geçerlidir.

Blobların arşivden sık veya seyrek erişimli katmanlara kopyalanması, okuma işlemleri ve veri alımı olarak ücretlendirilir. Yeni blob kopyasının oluşturulması için bir yazma işlemi ücretlendirilir. Kaynak blobu arşiv katmanında değiştirilmemiş olarak kaldığı için, erken silme ücretleri bir çevrimiçi bloba kopyalanırken uygulanmaz. Seçilirse, yüksek öncelikli alma ücretleri uygulanır.

Arşiv katmanındaki Bloblar en az 180 gün önce depolanmalıdır. Arşivlenmiş blob 'ları 180 günden önce silmek veya yeniden doldurma işlemi erken silme ücretleri oluşturacak.

> [!NOTE]
> Blok Blobları ve veri yeniden doldurma fiyatlandırması hakkında daha fazla bilgi için bkz. [Azure Storage fiyatlandırması](https://azure.microsoft.com/pricing/details/storage/blobs/). Giden veri aktarımı ücretleri hakkında daha fazla bilgi için bkz. [veri aktarımları fiyatlandırma ayrıntıları](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="quickstart-scenarios"></a>Hızlı Başlangıç senaryoları

### <a name="rehydrate-an-archive-blob-to-an-online-tier"></a>Bir arşiv blobunu çevrimiçi katmana yeniden doldurma
# <a name="portal"></a>[Portal](#tab/azure-portal)
1. [Azure portalında](https://portal.azure.com) oturum açın.

1. Azure portal, **tüm kaynakları** arayıp seçin.

1. Depolama hesabınızı seçin.

1. Kapsayıcınızı seçin ve ardından blobu seçin.

1. **BLOB özellikleri**' nde **Katmanı Değiştir**' i seçin.

1. **Sık** veya **seyrek erişimli erişim** katmanını seçin. 

1. **Standart** veya **yüksek** bir yeniden doldurma önceliği seçin.

1. Alt kısımdaki **Kaydet** ' i seçin.

![Depolama hesabı katmanını Değiştir ](media/storage-tiers/blob-access-tier.png)
 ![ yeniden doldurma durumu](media/storage-tiers/rehydrate-status.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Bir arşiv Blobun blob katmanını değiştirmek için aşağıdaki PowerShell betiği kullanılabilir. `$rgName`Değişken, kaynak grubu adınızla başlatılmalıdır. `$accountName`Değişken, depolama hesabı adınızla başlatılmalıdır. `$containerName`Değişken, kapsayıcı adınızla başlatılmalıdır. `$blobName`Değişken, blob adınızla başlatılmalıdır. 
```powershell
#Initialize the following with your resource group, storage account, container, and blob names
$rgName = ""
$accountName = ""
$containerName = ""
$blobName = ""

#Select the storage account and get the context
$storageAccount =Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

#Select the blob from a container
$blob = Get-AzStorageBlob -Container $containerName -Blob $blobName -Context $ctx

#Change the blob’s access tier to Hot using Standard priority rehydrate
$blob.ICloudBlob.SetStandardBlobTier("Hot", "Standard")
```
---

### <a name="copy-an-archive-blob-to-a-new-blob-with-an-online-tier"></a>Bir arşiv blobunu çevrimiçi bir katman ile yeni bir bloba kopyalama
Aşağıdaki PowerShell betiği, aynı depolama hesabı içindeki yeni bir bloba arşiv blobu kopyalamak için kullanılabilir. `$rgName`Değişken, kaynak grubu adınızla başlatılmalıdır. `$accountName`Değişken, depolama hesabı adınızla başlatılmalıdır. `$srcContainerName`Ve `$destContainerName` değişkenleri kapsayıcı adlarınızla başlatılmalıdır. `$srcBlobName`Ve `$destBlobName` değişkenlerinin blob adlarınızla başlatılması gerekir. 
```powershell
#Initialize the following with your resource group, storage account, container, and blob names
$rgName = ""
$accountName = ""
$srcContainerName = ""
$destContainerName = ""
$srcBlobName = ""
$destBlobName = ""

#Select the storage account and get the context
$storageAccount =Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

#Copy source blob to a new destination blob with access tier hot using standard rehydrate priority
Start-AzStorageBlobCopy -SrcContainer $srcContainerName -SrcBlob $srcBlobName -DestContainer $destContainerName -DestBlob $destBlobName -StandardBlobTier Hot -RehydratePriority Standard -Context $ctx
```

## <a name="next-steps"></a>Sonraki Adımlar

* [BLOB depolama katmanları hakkında bilgi edinin](storage-blob-storage-tiers.md)
* [Blob depolama ve GPv2 hesaplarında bölgeye göre sık erişimli, seyrek erişimli ve arşiv fiyatlarını denetleme](https://azure.microsoft.com/pricing/details/storage/)
* [Azure Blob depolama yaşam döngüsünü yönetme](storage-lifecycle-management-concepts.md)
* [Veri aktarımı fiyatlandırmasını denetleme](https://azure.microsoft.com/pricing/details/data-transfers/)