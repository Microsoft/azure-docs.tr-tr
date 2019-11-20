---
title: Azure sayfa bloblarına genel bakış | Microsoft Docs
description: Örnek betiklerle kullanım örnekleri de dahil olmak üzere Azure sayfa Blobları ve bunların avantajları hakkında genel bakış.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 05/13/2019
ms.author: tamram
ms.reviewer: wielriac
ms.subservice: blobs
ms.openlocfilehash: 060e1d01e5f078bad9852ae35d0af9142192a7b6
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/13/2019
ms.locfileid: "68985626"
---
# <a name="overview-of-azure-page-blobs"></a>Azure sayfa bloblarına genel bakış

Azure depolama, üç tür BLOB depolama alanı sunar: Blob 'Ları, ekleme Bloblarını ve sayfa bloblarını engelleyin. Blok Blobları bloklardan oluşur ve metin veya ikili dosyaları depolamak ve büyük dosyaları verimli bir şekilde yüklemek için idealdir. Ekleme Blobları bloklarından da oluşur, ancak bunlar, oturum açma senaryolarında ideal hale getirilmeleri için, ekleme işlemleri için iyileştirilmiştir. Sayfa Blobları toplam boyut olarak 8 TB 'a kadar 512 baytlık sayfalardan oluşur ve sık rastgele okuma/yazma işlemleri için tasarlanmıştır. Sayfa Blobları, Azure IaaS disklerinin temelidir. Bu makalede, sayfa bloblarının özelliklerinin ve avantajlarından faydalanılır.

Sayfa Blobları, rastgele bayt aralıklarını okuma/yazma özelliği sağlayan 512 baytlık sayfaların koleksiyonudur. Bu nedenle, sayfa Blobları, sanal makineler ve veritabanları için işletim sistemi ve veri diskleri gibi dizin tabanlı ve seyrek veri yapılarını depolamak için idealdir. Örneğin, Azure SQL DB, veritabanı için temeldeki kalıcı depolama olarak sayfa bloblarını kullanır. Ayrıca, sayfa Blobları, Aralık tabanlı güncelleştirmeler içeren dosyalar için de kullanılır.  

Azure sayfa bloblarının temel özellikleri, REST arabirimidir, temeldeki depolamanın dayanıklılığı ve Azure 'a sorunsuz geçiş özellikleri sağlar. Bu özellikler, sonraki bölümde daha ayrıntılı olarak ele alınmıştır. Ayrıca, Azure sayfa Blobları Şu anda iki tür depolama üzerinde desteklenmektedir: Premium Depolama ve standart depolama. Premium Depolama özellikle yüksek performans ve düşük gecikme gerektiren iş yükleri için tasarlanmıştır ve Premium sayfa Blobları yüksek performanslı depolama senaryoları için idealdir. Standart depolama hesapları, gecikme süresine duyarsız iş yüklerini çalıştırmaya daha uygun maliyetli bir hesapdır.

## <a name="sample-use-cases"></a>Örnek kullanım örnekleri

Azure IaaS disklerinden başlayarak sayfa Blobları için birkaç kullanım durumu tartışalım. Azure sayfa Blobları, Azure IaaS için sanal diskler platformunun omurgası. Hem Azure işletim sistemi hem de veri diskleri, verilerin Azure depolama platformunda sürekli olarak kalıcı olduğu sanal diskler olarak uygulanır ve daha sonra en yüksek performans için sanal makinelere dağıtılır. Azure diskleri, Hyper-V [VHD biçiminde](https://technet.microsoft.com/library/dd979539.aspx) kalıcı hale getirilir ve Azure Storage 'da bir [Sayfa Blobu](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs#about-page-blobs) olarak depolanır. Sayfa Blobları, Azure IaaS VM 'Leri için sanal disklerin kullanılmasına ek olarak, SQL verilerini depolamak için şu anda sayfa Blobları kullanan PaaS ve DBaaS senaryolarını da etkinleştirir ve bu veritabanı için hızlı rastgele okuma yazma işlemlerini etkinleştirir. Ortak video düzenlemesi uygulamalarına yönelik paylaşılan medya erişimi için bir PaaS hizmetiniz varsa, sayfa Blobları medyadaki rastgele konumlara hızlı erişim sağlar. Aynı medyayı birden çok kullanıcı tarafından hızlı ve verimli bir şekilde düzenlemenizi ve birleştirmeyi de sağlar. 

Azure Site Recovery, Azure Backup gibi birinci taraf Microsoft hizmetlerinin yanı sıra birçok üçüncü taraf geliştirici, sayfa blobunun REST arabirimini kullanarak sektör lideri yeniliklere sahiptir. Azure 'da uygulanan benzersiz senaryolardan bazıları aşağıda verilmiştir: 

* Uygulamayla yönlendirilen Artımlı anlık görüntü yönetimi: Uygulamalar, verilerin maliyetli bir şekilde çoğaltılmasını etkilemeden uygulama kontrol noktalarını kaydetmek için Sayfa Blobu anlık görüntülerinin ve REST API 'Lerinden yararlanabilir. Azure depolama, tüm Blobun kopyalanmasını gerektirmeyen sayfa Blobları için yerel anlık görüntüleri destekler. Bu genel anlık görüntü API 'leri, anlık görüntüler arasında değişimleri erişimini ve kopyalanmasını da sağlar.
* Şirket içinden buluta dinamik uygulama ve veri geçişi: Şirket içi sanal makine çalışmaya devam ederken, şirket içi verileri kopyalayın ve REST API 'Lerini doğrudan bir Azure sayfa blobuna yazmak için kullanın. Hedef yakalandıktan sonra, bu verileri kullanarak Azure VM 'ye hızlı bir şekilde yük devreleyebilirsiniz. Bu şekilde, VM 'yi kullanmaya devam ederken veri geçişinin arka planda oluşması ve yük devretme için gereken kapalı kalma süresi kısa (dakika) olacağı için sanal makinelerinizi Şirket içinden buluta, en az kapalı kalma süresine geçirebilirsiniz.
* Eşzamanlılık denetimi desteğiyle birden çok okuyucular ve tek yazıcı gibi senaryolara izin veren [SAS tabanlı](../common/storage-sas-overview.md) paylaşılan erişim.

## <a name="page-blob-features"></a>Sayfa blobu özellikleri

### <a name="rest-api"></a>REST API

[Sayfa bloblarını kullanarak geliştirmeye](storage-dotnet-how-to-use-blobs.md)başlamak için aşağıdaki belgeye başvurun. Örnek olarak, .NET için depolama Istemci kitaplığı 'nı kullanarak sayfa bloblarına erişme bölümüne bakın. 

Aşağıdaki diyagramda hesap, kapsayıcılar ve sayfa Blobları arasındaki genel ilişkiler açıklanmaktadır.

![Hesap, kapsayıcılar ve sayfa Blobları arasındaki ilişkileri gösteren ekran görüntüsü](./media/storage-blob-pageblob-overview/storage-blob-pageblob-overview-figure1.png)

#### <a name="creating-an-empty-page-blob-of-a-specified-size"></a>Belirtilen boyuttaki boş bir Sayfa Blobu oluşturuluyor

Bir Sayfa Blobu oluşturmak için ilk olarak, depolama hesabınız için blob depolamaya (Şekil 1 ' de*pbaccount* ) ve şu şekilde gösterildiği gibi **Storagecredentialsaccountandkey** NESNESINE erişmek için temel URI Ile bir **cloudblobclient** nesnesi oluşturacağız. Aşağıdaki örnek. Örnek daha sonra **Cloudblobcontainer** nesnesine bir başvuru oluşturmayı ve daha önce mevcut değilse kapsayıcıyı (*testvhd*'ler) oluşturmayı gösterir. Daha sonra **Cloudblobcontainer** nesnesini kullanarak erişmek için Sayfa Blobu adını (OS4. vhd) belirterek **cloudpageblob** nesnesine bir başvuru oluşturun. Sayfa Blobu oluşturmak için, oluşturulacak blob 'un en büyük boyutunu geçirerek [Cloudpageblob. Create](/dotnet/api/microsoft.azure.storage.blob.cloudpageblob.create)' ı çağırın. *Blobsize* , 512 baytlık bir katı olmalıdır.

```csharp
using Microsoft.Azure;
using Microsoft.Azure.Storage;
using Microsoft.Azure.Storage.Blob;

long OneGigabyteAsBytes = 1024 * 1024 * 1024;
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the blob client.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Retrieve a reference to a container.
CloudBlobContainer container = blobClient.GetContainerReference("testvhds");

// Create the container if it doesn't already exist.
container.CreateIfNotExists();

CloudPageBlob pageBlob = container.GetPageBlobReference("os4.vhd");
pageBlob.Create(16 * OneGigabyteAsBytes);
```

#### <a name="resizing-a-page-blob"></a>Sayfa Blobu yeniden boyutlandırma

Oluşturulduktan sonra bir sayfa blobunu yeniden boyutlandırmak için, [Resize](/dotnet/api/microsoft.azure.storage.blob.cloudpageblob.resize) yöntemini kullanın. İstenen boyut, 512 baytlık bir katı olmalıdır.

```csharp
pageBlob.Resize(32 * OneGigabyteAsBytes);
```

#### <a name="writing-pages-to-a-page-blob"></a>Sayfa blobuna sayfa yazma

Sayfa yazmak için [Cloudpageblob. WritePages](/dotnet/api/microsoft.azure.storage.blob.cloudpageblob.beginwritepages) metodunu kullanın.  Bu, 4Mb 'a kadar sıralı bir sayfa kümesi yazmanızı sağlar. Yazılan sınırın 512 baytlık bir sınır (Startingkayması% 512 = = 0) üzerinde başlaması ve bir 512 sınır-1 üzerinde bitmesi gerekir.  Aşağıdaki kod örneği bir blob için **Writepages** 'in nasıl çağrılacağını göstermektedir:

```csharp
pageBlob.WritePages(dataStream, startingOffset); 
```

Blob hizmetinde ardışık bir sayfa kümesi için yazma isteği başarılı olur ve dayanıklılık ve dayanıklılık için çoğaltılırsa, yazma işlemi tamamlanır ve istemciye başarılı döndürülür.  

Aşağıdaki diyagramda, 2 ayrı yazma işlemi gösterilmektedir:

![](./media/storage-blob-pageblob-overview/storage-blob-pageblob-overview-figure2.png)

1.  0 uzunluklu 1024 bayt uzaklığında başlayan bir yazma işlemi 
2.  4096 uzunluğuna başlayan bir yazma işlemi 1024 

#### <a name="reading-pages-from-a-page-blob"></a>Sayfa blobundan sayfaları okuma

Sayfaları okumak için, sayfa blobundan bir bayt aralığı okumak üzere [Cloudpageblob. DownloadRangeToByteArray](/dotnet/api/microsoft.azure.storage.blob.icloudblob.downloadrangetobytearray) yöntemini kullanın. Bu, Blobun herhangi bir aralıktan başlayarak tam Blobu veya bayt aralığını indirmelerini sağlar. Okurken, fark 512 ' in katı olarak başlamak zorunda değildir. Bir NUL sayfasından bayt okurken, hizmet sıfır bayt döndürür.

```csharp
byte[] buffer = new byte[rangeSize];
pageBlob.DownloadRangeToByteArray(buffer, bufferOffset, pageBlobOffset, rangeSize); 
```

Aşağıdaki şekilde, 256 ve Aralık boyutu 4352 olan bir okuma işlemi gösterilmektedir. Döndürülen veriler Turuncu renkle vurgulanır. NUL sayfaları için sıfırlar döndürülür.

![](./media/storage-blob-pageblob-overview/storage-blob-pageblob-overview-figure3.png)

Daha seyrek doldurulmuş bir Blobun varsa, sıfır baytlık yumurtın ve indirme gecikmesini azaltmak için yalnızca geçerli sayfa bölgelerini indirmek isteyebilirsiniz.  Hangi sayfaların veri tarafından desteklenen olduğunu anlamak için [Cloudpageblob. GetPageRanges](/dotnet/api/microsoft.azure.storage.blob.cloudpageblob.getpageranges)kullanın. Ardından döndürülen aralıkları numaralandırabilirsiniz ve verileri her bir aralığa indirebilirsiniz. 

```csharp
IEnumerable<PageRange> pageRanges = pageBlob.GetPageRanges();

foreach (PageRange range in pageRanges)
{
    // Calculate the range size
    int rangeSize = (int)(range.EndOffset + 1 - range.StartOffset);

    byte[] buffer = new byte[rangeSize];

    // Read from the correct starting offset in the page blob and
    // place the data in the bufferOffset of the buffer byte array
    pageBlob.DownloadRangeToByteArray(buffer, bufferOffset, range.StartOffset, rangeSize); 

    // Then use the buffer for the page range just read
}
```

#### <a name="leasing-a-page-blob"></a>Sayfa Blobu kiralamaya

Kira blobu işlemi, blob üzerinde yazma ve silme işlemleri için bir kilit oluşturur ve yönetir. Bu işlem, tek seferde yalnızca bir istemcinin bloba yazılabilir olmasını sağlamak için birden fazla istemciden bir sayfa blobuna erişildiği senaryolarda faydalıdır. Örneğin, Azure diskleri, diskin yalnızca tek bir VM tarafından yönetildiğinden emin olmak için bu kira mekanizmasından yararlanır. Kilit süresi 15 ile 60 saniye arasında olabilir veya sonsuz olabilir. Daha ayrıntılı bilgi için [buraya](/rest/api/storageservices/lease-blob) bakın.

Zengin REST API 'Lerine ek olarak, sayfa Blobları da paylaşılan erişim, dayanıklılık ve gelişmiş güvenlik sağlar. Bu avantajları sonraki paragraflarda daha ayrıntılı olarak ele alınacaktır. 

### <a name="concurrent-access"></a>Eşzamanlı erişim

Sayfa Blobları REST API ve Kiralama mekanizması, uygulamaların birden fazla istemciden sayfa blobuna erişmesini sağlar. Örneğin, birden çok kullanıcıyla depolama nesneleri paylaşan bir dağıtılmış bulut hizmeti oluşturmanız gerektiğini varsayalım. Bu, çok sayıda kullanıcıya büyük bir görüntü koleksiyonunu sunan bir Web uygulaması olabilir. Bunu uygulamaya yönelik bir seçenek, eklenen disklere sahip bir sanal makine kullanmaktır. Bu sayede, (i) bir diskin yalnızca tek bir VM 'ye iliştirilebildiği kısıtlama, esneklik ve daha fazla riskleri sınırlayan kısıtlamalar vardır. VM 'de veya VM 'de çalışan hizmette bir sorun varsa, kira süresi dolana veya kesilene kadar görüntü erişilemez olur; ve (II) IaaS VM 'ye sahip olmanın ek maliyeti. 

Alternatif bir seçenek, sayfa bloblarını doğrudan Azure depolama REST API 'Leri aracılığıyla kullanmaktır. Bu seçenek, maliyetli IaaS sanal makineleri gereksinimini ortadan kaldırır, birden fazla istemciden doğrudan erişim esnekliği sunar, diskleri iliştirme/ayır ve VM 'deki sorunlar riskini ortadan kaldırarak klasik dağıtım modelini basitleştirir. Ayrıca, bir disk olarak rastgele okuma/yazma işlemleri için aynı performans düzeyini sağlar

### <a name="durability-and-high-availability"></a>Dayanıklılık ve yüksek kullanılabilirlik

Hem standart hem de Premium Depolama, bellek ve yüksek kullanılabilirlik sağlamak için Sayfa Blobu verilerinin her zaman çoğaltılacağı dayanıklı bir depodur. Azure depolama artıklığı hakkında daha fazla bilgi için bu [belgelere](../common/storage-redundancy.md)bakın. Azure, IaaS diskleri ve sayfa Blobları için sürekli olarak kurumsal düzeyde dayanıklılık dağıtmıştır ve sektörde önde gelen sıfır [yıllık bir hata oranı](https://en.wikipedia.org/wiki/Annualized_failure_rate)içerir.

### <a name="seamless-migration-to-azure"></a>Sorunsuz bir şekilde Azure 'a geçiş

Azure, kendi özelleştirilmiş yedekleme çözümünü uygulamayla ilgilenen müşteriler ve geliştiriciler için, yalnızca deltas 'ı tutan Artımlı anlık görüntüler sunar. Bu özellik ilk tam kopyanın maliyetini önler ve bu da yedekleme maliyetini önemli ölçüde düşürür. Değişiklik verilerini verimli bir şekilde okuma ve kopyalama özelliği sayesinde, geliştiricilerin Azure 'da en iyi şekilde bir en iyi yedekleme ve olağanüstü durum kurtarma (DR) deneyimi sunan geliştiricilerden daha da daha fazla yenilik sağlayan başka bir güçlü özelliktir. [BLOB anlık görüntüsü](/rest/api/storageservices/snapshot-blob) kullanarak Azure 'daki sanal makinelerinize yönelik yedekleme veya Dr çözümünüzü, [Get sayfa aralıkları](/rest/api/storageservices/get-page-ranges) API 'Si ve [artımlı kopyalama blob](/rest/api/storageservices/incremental-copy-blob) API 'si ile birlikte, Dr için artımlı verileri kolayca kopyalamak için kullanabileceğiniz şekilde ayarlayabilirsiniz. 

Üstelik, birçok kuruluş, şirket içi veri merkezlerinde zaten çalışmakta olan kritik iş yükleri vardır. İş yükünü buluta geçirmek için, ana kaygıdan biri verileri kopyalamak için gereken kapalı kalma süresi ve geçiş işleminden sonra öngörülemeyen sorunların riski olacaktır. Çoğu durumda, kapalı kalma süresi buluta geçiş için bir showstober olabilir. REST API sayfa bloblarını kullanarak Azure, kritik iş yüklerine en düşük kesintilerle bulut geçişini etkinleştirerek bu sorunu giderir. 

Anlık görüntü alma ve bir sayfa blobunu anlık görüntüden geri yükleme örnekleri için, lütfen [Artımlı anlık görüntü kullanarak bir yedekleme işlemi kurma](../../virtual-machines/windows/incremental-snapshots.md) makalesini inceleyin.
