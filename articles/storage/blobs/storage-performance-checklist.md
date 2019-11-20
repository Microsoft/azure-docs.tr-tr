---
title: BLOB depolama için performans ve ölçeklenebilirlik denetim listesi-Azure depolama
description: Yüksek performanslı uygulamalar geliştirirken blob depolamayla birlikte kullanılmak üzere kanıtlanmış uygulamaların denetim listesi.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 24d601dc2116b7daf315bb3c6f20c4dc0b6f6ce5
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72382053"
---
# <a name="performance-and-scalability-checklist-for-blob-storage"></a>BLOB depolama için performans ve ölçeklenebilirlik denetim listesi

Microsoft, BLOB depolama ile yüksek performanslı uygulamalar geliştirmeye yönelik bir dizi kanıtlanmış uygulama geliştirmiştir. Bu denetim listesi, geliştiricilerin performansı iyileştirmek için izleyebildiği önemli uygulamaları tanımlar. Uygulamanızı tasarlarken ve işlem boyunca bu uygulamaları göz önünde bulundurun.

Azure depolama kapasitesi, işlem hızı ve bant genişliği için ölçeklenebilirlik ve performans hedefleri içerir. Azure depolama ölçeklenebilirlik hedefleri hakkında daha fazla bilgi için bkz. [depolama hesapları Için Azure Storage ölçeklenebilirlik ve performans hedefleri](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="checklist"></a>Denetim Listesi

Bu makale, blob Storage uygulamanızı geliştirirken izleyebileceğiniz bir denetim listesi halinde performans için kanıtlanmış uygulamaları düzenler.

| Bitti | Kategori | Tasarım değerlendirmesi |
| --- | --- | --- |
| &nbsp; |Ölçeklenebilirlik hedefleri |[Uygulamanızı en fazla depolama hesabı sayısından daha fazla kullanmak üzere tasarlayabilmeniz gerekebilir mi?](#maximum-number-of-storage-accounts) |
| &nbsp; |Ölçeklenebilirlik hedefleri |[Kapasite ve işlem sınırlarına yaklaşmaktan kaçınıyorsunuz musunuz?](#capacity-and-transaction-targets) |
| &nbsp; |Ölçeklenebilirlik hedefleri |[Aynı anda tek bir bloba erişen çok sayıda istemci var mı?](#multiple-clients-accessing-a-single-blob-concurrently) |
| &nbsp; |Ölçeklenebilirlik hedefleri |[Uygulamanız tek bir blob için ölçeklenebilirlik hedefleri içinde kalıyor mu?](#bandwidth-and-operations-per-blob) |
| &nbsp; |Bölümleme |[Adlandırma kuralınızın daha iyi yük dengelemesini sağlamak üzere tasarlandı mı?](#partitioning) |
| &nbsp; |Networking (Ağ İletişimi) |[İstemci tarafı cihazlarda gereken performansa ulaşmak için yeterli yüksek bant genişliği ve düşük gecikme süresi var mı?](#throughput) |
| &nbsp; |Networking (Ağ İletişimi) |[İstemci tarafı cihazların yüksek kaliteli bir ağ bağlantısı var mı?](#link-quality) |
| &nbsp; |Networking (Ağ İletişimi) |[İstemci uygulaması, depolama hesabıyla aynı bölgede mi?](#location) |
| &nbsp; |Doğrudan istemci erişimi |[Azure Storage 'a doğrudan erişim sağlamak için paylaşılan erişim imzaları (SAS) ve çıkış noktaları arası kaynak paylaşımı (CORS) kullanıyor musunuz?](#sas-and-cors) |
| &nbsp; |Önbelleğe Alma |[Uygulamanızın önbelleğe alınması sık erişilen ve nadiren değiştirilen verilerinize mı sahip?](#reading-data) |
| &nbsp; |Önbelleğe Alma |[Uygulamanız, güncelleştirmeleri istemcide önbelleğe alarak ve daha sonra bunları daha büyük kümelere karşıya yükleyerek toplu olarak güncelleştirir mı?](#uploading-data-in-batches) |
| &nbsp; |.NET yapılandırması |[En iyi performans için .NET Core 2,1 veya üstünü mi kullanıyorsunuz?](#use-net-core) |
| &nbsp; |.NET yapılandırması |[İstemcinizi yeterli sayıda eşzamanlı bağlantı kullanacak şekilde yapılandırdınız mı?](#increase-default-connection-limit) |
| &nbsp; |.NET yapılandırması |[.NET uygulamaları için, .NET 'i yeterli sayıda iş parçacığı kullanacak şekilde yapılandırdınız mı?](#increase-minimum-number-of-threads) |
| &nbsp; |Paralellik |[Paralellik 'in, istemci yeteneklerini aşırı yüklemeden veya ölçeklenebilirlik hedeflerine yaklaşımak için uygun şekilde bağlanmış olduğunu merak etmeniz gerekir mi?](#unbounded-parallelism) |
| &nbsp; |Araçlar |[Microsoft tarafından sağlanmış istemci kitaplıklarının ve araçlarının en son sürümlerini kullanıyor musunuz?](#client-libraries-and-tools) |
| &nbsp; |Yeniden deneme sayısı |[Daraltma hataları ve zaman aşımları için üstel geri alma ile yeniden deneme İlkesi kullanıyor musunuz?](#timeout-and-server-busy-errors) |
| &nbsp; |Yeniden deneme sayısı |[Uygulamanız yeniden denenmeyen hatalara karşı yeniden denemeyi önler mi?](#non-retryable-errors) |
| &nbsp; |Blobları kopyalama |[Blob 'ları en verimli şekilde kopyaladığınızı biliyor musunuz?](#blob-copy-apis) |
| &nbsp; |Blobları kopyalama |[Toplu kopyalama işlemleri için AzCopy 'ın en son sürümünü kullanıyor musunuz?](#use-azcopy) |
| &nbsp; |Blobları kopyalama |[Büyük hacimli verileri içeri aktarmak için Azure Data Box ailesini kullanıyor musunuz?](#use-azure-data-box) |
| &nbsp; |İçerik dağıtımı |[İçerik dağıtımı için CDN kullanıyor musunuz?](#content-distribution) |
| &nbsp; |Meta verileri kullan |[Blob 'lar hakkında sık kullanılan meta verileri meta verilerinde depoluyorsanız mi?](#use-metadata) |
| &nbsp; |Hızlı karşıya yükleme |[Bir blobu hızlıca karşıya yüklemeye çalışırken blokları paralel olarak karşıya yüklüyor musunuz?](#upload-one-large-blob-quickly) |
| &nbsp; |Hızlı karşıya yükleme |[Çok sayıda blobu hızlıca yüklemeye çalışırken blob 'ları paralel olarak karşıya yüklüyor musunuz?](#upload-many-blobs-quickly) |
| &nbsp; |Blob türü |[Uygun durumlarda sayfa Blobları mı kullanıyorsunuz, blob 'ları mi engelliyor?](#choose-the-correct-type-of-blob) |

## <a name="scalability-targets"></a>Ölçeklenebilirlik hedefleri

Uygulamanız ölçeklenebilirlik hedeflerinin herhangi birini yaklaşırsa veya aşarsa, daha fazla işlem gecikmeleri veya azaltmasıyla karşılaşabilirler. Azure Storage uygulamanızı kısıtsalken, hizmet 503 (sunucu meşgul) veya 500 (Işlem zaman aşımı) hata kodları döndürmeye başlar. Ölçeklenebilirlik hedefleri sınırları içinde kalarak bu hatalardan kaçınmak, uygulamanızın performansını artırmanın önemli bir parçasıdır.

Kuyruk hizmeti ölçeklenebilirlik hedefleri hakkında daha fazla bilgi için bkz. [Azure Storage ölçeklenebilirlik ve performans hedefleri](/azure/storage/common/storage-scalability-targets?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#azure-blob-storage-scale-targets).

### <a name="maximum-number-of-storage-accounts"></a>En fazla depolama hesabı sayısı

Belirli bir abonelik/bölge birleşimi için izin verilen en fazla depolama hesabı sayısına yaklaşdıysanız, senaryonuzu değerlendirin ve aşağıdaki koşullardan herhangi birinin uygulanıp uygulanmadığını saptayın:

- Yönetilmeyen diskleri depolamak ve bu diskleri sanal makinelerinize (VM 'Ler) eklemek için depolama hesapları kullanıyor musunuz? Bu senaryo için, Microsoft yönetilen disklerin kullanılmasını önerir. Yönetilen diskler, tek tek depolama hesapları oluşturma ve yönetmeye gerek kalmadan otomatik olarak ve sizin için ölçeklendirilir. Daha fazla bilgi için bkz. [Azure yönetilen disklere giriş](../../virtual-machines/windows/managed-disks-overview.md)
- Veri yalıtımı amacıyla müşteri başına bir depolama hesabı kullanıyor musunuz? Bu senaryo için, Microsoft tüm depolama hesabı yerine her müşteri için bir blob kapsayıcısı kullanılmasını önerir. Azure depolama artık kapsayıcı temelinde rol tabanlı erişim denetimi (RBAC) rolleri atamanıza olanak tanır. Daha fazla bilgi için, [Azure Portal RBAC Ile Azure Blob ve kuyruk verilerine erişim verme](../common/storage-auth-aad-rbac-portal.md)bölümüne bakın.
- Giriş, çıkış, saniye başına g/ç işlemi (ıOPS) veya kapasiteyi artırmak için birden fazla depolama hesabı kullanıyor musunuz? Bu senaryoda, Microsoft, mümkünse iş yükünüz için gereken depolama hesabı sayısını azaltmak üzere depolama hesapları için artan limitlerin avantajlarından yararlanmanızı öneriyor. Depolama Hesabınıza yönelik daha fazla limit istemek için [Azure desteğine](https://azure.microsoft.com/support/options/) başvurun. Daha fazla bilgi için bkz. daha [büyük, daha yüksek ölçekli depolama hesapları duyurusu](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/).

### <a name="capacity-and-transaction-targets"></a>Kapasite ve işlem hedefleri

Uygulamanız tek bir depolama hesabı için ölçeklenebilirlik hedeflerine yaklaşıyorsa, aşağıdaki yaklaşımlardan birini benimsede düşünün:  

- Uygulamanız işlem hedefini ziyaret etse, yüksek işlem ücretleri ve düşük ve tutarlı gecikme için iyileştirilmiş Blok Blob depolama hesapları kullanmayı düşünün. Daha fazla bilgi için bkz. [Azure depolama hesabına genel bakış](../common/storage-account-overview.md).
- Uygulamanızın ölçeklenebilirlik hedefini yaklaşımını veya aşmasına neden olan iş yükünü yeniden değerlendirin. Daha az bant genişliği veya kapasite veya daha az işlem kullanmak için farklı bir şekilde tasarlayabilirsiniz mi?
- Uygulamanız ölçeklenebilirlik hedeflerinin birini aşmanız gerekiyorsa, birden çok depolama hesabı oluşturun ve uygulama verilerinizi bu birden çok depolama hesabı genelinde bölümleyin. Bu kalıbı kullanırsanız, daha sonra yük dengelemeye yönelik daha fazla depolama hesabı ekleyebilmeniz için uygulamanızı tasarlayadığınızdan emin olun. Depolama hesaplarının kendileri, depolanan veri, işlem yapılan veya aktarılan veri açısından kullanımınız dışında bir ücret içermez.
- Uygulamanız bant genişliği hedeflerine yaklaşıyorsa, verileri Azure depolama 'ya göndermek için gereken bant genişliğini azaltmak için istemci tarafındaki verileri sıkıştırmayı göz önünde bulundurun.
    Verilerin sıkıştırılması bant genişliğini kaydedebilir ve ağ performansını iyileştireken performansı olumsuz etkileyebilir. İstemci tarafında veri sıkıştırma ve açma için ek işleme gereksinimlerinin performans etkisini değerlendirin. Sıkıştırılmış verilerin depolanması sorun gidermeyi daha zor hale getirir, çünkü verileri standart araçlar kullanarak görüntülemek daha zor olabilir.
- Uygulamanız ölçeklenebilirlik hedeflerine yaklaşıyorsa, yeniden denemeler için bir üstel geri alma kullandığınızdan emin olun. Bu makalede açıklanan önerileri uygulayarak ölçeklenebilirlik hedeflerine ulaşmaktan kaçınmak en iyisidir. Ancak, yeniden denemeler için bir üstel geri alma kullanılması, uygulamanızın hızlı bir şekilde yeniden denenmesini engelleyecek ve bu da azaltmayı daha kötüleşmektedir. Daha fazla bilgi için [zaman aşımı ve sunucu meşgul hataları](#timeout-and-server-busy-errors)başlıklı bölüme bakın.

### <a name="multiple-clients-accessing-a-single-blob-concurrently"></a>Aynı anda tek bir bloba erişen birden çok istemci

Aynı anda tek bir bloba erişen çok sayıda istemciniz varsa, blob başına ve depolama hesabı ölçeklenebilirlik hedefleri başına her ikisini de göz önünde bulundurmanız gerekir. Tek bir bloba erişebilen istemcilerin tam sayısı, blobu aynı anda isteyen istemci sayısı, BLOB boyutu ve ağ koşulları gibi etkenlere bağlı olarak değişir.

Blob, bir Web sitesinden sunulan resimler veya videolar gibi bir CDN aracılığıyla dağıtılıyorsa, CDN kullanabilirsiniz. Daha fazla bilgi için [içerik dağıtımı](#content-distribution)başlıklı bölüme bakın.

Verilerin gizli olduğu bilimsel benzetimler gibi diğer senaryolarda, iki seçeneğiniz vardır. Birincisi, iş yükünüzün erişimini, blob 'a aynı anda erişilmek üzere bir süre içinde erişilmek üzere dengelemenize yönelik olur. Alternatif olarak, blob başına toplam ıOPS 'yi ve depolama hesaplarını artırmak için blobu birden fazla depolama hesabına geçici olarak kopyalayabilirsiniz. Sonuçlar uygulamanızın davranışına göre farklılık gösterir, bu nedenle tasarım sırasında eşzamanlılık desenlerini test ettiğinizden emin olun.

### <a name="bandwidth-and-operations-per-blob"></a>Blob başına bant genişliği ve işlemler

Tek bir blob, saniyede en fazla 500 isteği destekler. Aynı blobu okuması gereken birden fazla istemciniz varsa ve bu sınırı aşarsanız bir Blok Blobu depolama hesabı kullanmayı göz önünde bulundurun. Blok Blobu depolama hesabı, saniye başına istek hızı veya g/ç işlemi (ıOPS) sağlar.

Blob üzerinde işlemler dağıtmak için, Azure CDN gibi bir içerik teslim ağı (CDN) da kullanabilirsiniz. Azure CDN hakkında daha fazla bilgi için bkz. [Azure CDN genel bakış](../../cdn/cdn-overview.md).  

## <a name="partitioning"></a>Bölümleme

Azure Storage 'ın BLOB verilerinizi nasıl bölümleyip performansı geliştirmek için nasıl yararlı olduğunu anlamak. Azure depolama, verileri birden çok bölüme yayılan verilerden daha hızlı bir şekilde tek bir bölümde sunabilir. Bloblarınızı uygun şekilde adlandırarak, okuma isteklerinin verimliliğini artırabilirsiniz.

BLOB depolama, ölçekleme ve yük dengeleme için Aralık tabanlı bölümleme şeması kullanır. Her Blobun, tam blob adından (hesap + kapsayıcı + blob) oluşan bir bölüm anahtarına sahiptir. Bölüm anahtarı, blob verilerini aralıklar halinde bölümlemek için kullanılır. Aralıklar daha sonra BLOB depolama arasında yük dengelenebilir.

Aralık tabanlı bölümleme, sözcük temelli sıralamayı (örneğin, *mypayroll*, *myperformance*, *MyEmployees*, vb.) veya zaman damgalarını (*log20160101*, *log20160102*, log20160102) kullanan adlandırma kuralları anlamına gelir.vb.) aynı bölüm sunucusunda birlikte bulunan bölümlerin oluşmasına neden olma olasılığı yüksektir. , artan yükün daha küçük aralıklar halinde bölünmeleri gerekir. Aynı bölüm sunucusundaki Blobların birlikte bulunması performansı geliştirir, bu nedenle performans iyileştirmesinin önemli bir parçası, Blobları en verimli şekilde düzenleyen bir şekilde adlandırmayı içerir.

Örneğin, bir kapsayıcı içindeki tüm Bloblar, bu bloblarda yük, Bölüm aralıklarının daha fazla yeniden dengelenmesini gerektirene kadar tek bir sunucu tarafından sunulabilir. Benzer şekilde, adları sözcük temelli sırada düzenlenmiş, bu hesapların bir veya tümünün birden çok bölüm sunucusuna bölünmesi gerekene kadar tek bir sunucu tarafından sunulabilir.

Her yük dengeleme işlemi, işlem sırasında depolama çağrılarının gecikmesini etkileyebilir. Hizmetin, bölüm anahtar aralığı ' nda kullanıma alınana ve yeniden dengeleyene kadar, bir bölüme bir bölüme ani trafik artışını işleme yeteneği, tek bir bölüm sunucusunun ölçeklenebilirliği ile sınırlıdır.

Bu tür işlemlerin sıklığını azaltmak için bazı en iyi yöntemleri izleyebilirsiniz.  

- Mümkünse, standart depolama hesapları ve Premium Depolama hesapları için 256 KiB 'den büyük bir blob veya blok boyutu kullanın. Daha büyük blob veya blok boyutları yüksek verimlilik blok bloblarını otomatik olarak etkinleştirir. Yüksek aktarım hızı blok Blobları, Bölüm adlandırmasının etkilenmedikleri yüksek performanslı alma işlemi sağlar.
- Hesaplar, kapsayıcılar, Bloblar, tablolar ve kuyruklar için kullandığınız adlandırma kuralını inceleyin. Gereksinimlerinize en uygun bir karma işlevi kullanarak hesap, kapsayıcı veya blob adlarını üç basamaklı bir karmaya önek olarak kabul etmeyi düşünün.
- Verilerinizi zaman damgaları veya sayısal tanımlayıcılar kullanarak düzenlediğinizde, salt bir Append (veya yalnızca sonuna kadar) trafik deseninin kullanmadığınız emin olun. Bu desenler, Aralık tabanlı bölümleme sistemi için uygun değildir. Bu desenler, tek bir bölüme giden ve sistemi etkin yük dengelemeden sınırlayan tüm trafiğe yol açabilir.

    Örneğin, *YYYYMMDD*gibi bir zaman damgasıyla blob kullanan günlük işlemlere sahipseniz, bu günlük işlem için tüm trafik tek bir bölüm sunucusu tarafından sunulan tek bir bloba yönlendirilir. Blob başına limitlerin ve bölüm başına limitlerinin gereksinimlerinizi karşılayıp karşılamadığını düşünün ve gerekirse bu işlemi birden çok bloba bölmek için göz önünde bulundurun. Benzer şekilde, zaman serisi verilerini Tablolarınızda depolarsanız, tüm trafik anahtar ad alanının son bölümüne yönlendirilebilir. Sayısal kimlikler kullanıyorsanız, KIMLIĞI üç basamaklı bir karma ile önek yapın. Zaman damgaları kullanıyorsanız, zaman damgasına önek olarak saniye değerini ekleyin, örneğin, *ssyyyymmdd*. Uygulamanız düzenli olarak listeleme ve sorgulama işlemleri gerçekleştiriyorsa, sorgu sayısını sınırlayan bir karma işlev seçin. Bazı durumlarda, rastgele bir ön ek yeterli olabilir.
  
- Azure depolama 'da kullanılan bölümleme şeması hakkında daha fazla bilgi için bkz. [Azure Storage: güçlü tutarlılığı olan yüksek oranda kullanılabilir bir bulut depolama hizmeti](https://sigops.org/sosp/sosp11/current/2011-Cascais/printable/11-calder.pdf).

## <a name="networking"></a>Networking (Ağ İletişimi)

Uygulamanın fiziksel ağ kısıtlamalarının performans üzerinde önemli bir etkisi olabilir. Aşağıdaki bölümlerde, kullanıcıların karşılaşabileceği bazı sınırlamalar açıklanır.  

### <a name="client-network-capability"></a>İstemci ağ özelliği

Bant genişliği ve ağ bağlantısının kalitesi, aşağıdaki bölümlerde açıklandığı gibi uygulama performansı açısından önemli rolleri oynar.

#### <a name="throughput"></a>İşleme

Bant genişliği için genellikle bu sorun istemcinin yeteneklerine yöneliktir. Daha büyük Azure örneklerinin NIC 'Leri daha fazla kapasiteye sahip olduğundan, tek bir makineden daha yüksek ağ sınırlarına ihtiyaç duyuyorsanız daha büyük bir örnek veya daha fazla VM kullanmayı düşünmelisiniz. Azure depolama 'yı şirket içi bir uygulamadan erişiyorsanız, aynı kural geçerlidir: istemci cihazının ağ yeteneklerini ve Azure depolama konumuna ağ bağlantısını anlayın ve bunları gerektiği gibi geliştirebilirsiniz veya uygulamanın özellikleri içinde çalışması.

#### <a name="link-quality"></a>Bağlantı kalitesi

Her türlü ağ kullanımında olduğu gibi, ağ koşullarının hatalara ve paket kaybına neden olacağını aklınızda bulundurun.  WireShark veya NetMon kullanmak bu sorunu tanılamanıza yardımcı olabilir.  

### <a name="location"></a>Konum

Dağıtılmış bir ortamda, istemciyi sunucuya eklemek en iyi performansı sağlar. En düşük gecikme süresi ile Azure depolama erişimi için, istemciniz için en iyi konum aynı Azure bölgesi içindedir. Örneğin, Azure Storage kullanan bir Azure Web uygulamanız varsa, bunları her ikisi de ABD Batı veya Asya Güneydoğu gibi tek bir bölge içinde bulun. Ortak bulma kaynakları, tek bir bölgedeki bant genişliği kullanımı ücretsizdir gecikme süresini ve maliyeti azaltır.  

İstemci uygulamaları Azure depolama 'ya erişebilse ancak mobil cihaz uygulamaları veya şirket içi kurumsal hizmetler gibi Azure 'da barındırılandıklarında, bu istemcilere yakın bir bölgede depolama hesabını bulma gecikme süresini azaltabilir. İstemcileriniz büyük ölçüde dağıtılırsa (örneğin, Kuzey Amerika ve bazıları Avrupa 'da), her bölge için bir depolama hesabı kullanmayı düşünün. Uygulamanın depoladığı veriler bireysel kullanıcılara özgü ise ve depolama hesapları arasında veri çoğaltmayı gerektirmiyorsa, bu yaklaşım daha kolay bir şekilde uygulanır.

Blob içeriğinin geniş bir dağıtımı için, Azure CDN gibi bir içerik teslim ağı kullanın. Azure CDN hakkında daha fazla bilgi için bkz. [Azure CDN](../../cdn/cdn-overview.md).  

## <a name="sas-and-cors"></a>SAS ve CORS

Kullanıcının Web tarayıcısında veya mobil telefon uygulamasında çalışan JavaScript gibi bir kodu, Azure depolama 'daki verilere erişmek için yetkilendirmeniz gerektiğini varsayalım. Bir yaklaşım, proxy görevi gören bir hizmet uygulaması oluşturmaktır. Kullanıcının cihazının kimliği, hizmet ile doğrulanır, bu da Azure depolama kaynaklarına erişim yetkisi verir. Bu şekilde, depolama hesabı anahtarlarınızı güvenli olmayan cihazlarda açığa çıkarmaktan kaçınabilirsiniz. Ancak, bu yaklaşım, kullanıcının cihazı ile Azure depolama arasında aktarılan tüm verilerin hizmet uygulamasından geçmesi gerektiğinden, hizmet uygulamasına önemli bir ek yük koyar.

Paylaşılan erişim imzalarını (SAS) kullanarak Azure depolama için bir hizmet uygulaması proxy 'si kullanmaktan kaçınabilirsiniz. SAS kullanarak, sınırlı erişim belirtecini kullanarak, kullanıcının cihazını doğrudan Azure depolama 'ya istek yapmasını sağlayabilirsiniz. Örneğin, bir Kullanıcı uygulamanıza fotoğraf yüklemek isterse, hizmet uygulamanız SAS oluşturup kullanıcının cihazına gönderebilir. SAS belirteci, belirli bir zaman aralığı için bir Azure depolama kaynağına yazma izni verebilir ve sonrasında SAS belirtecinin süresi dolar. SAS hakkında daha fazla bilgi için bkz. [paylaşılan erişim imzaları (SAS) kullanarak Azure depolama kaynaklarına sınırlı erişim verme](../common/storage-sas-overview.md).  

Genellikle, bir Web tarayıcısı, başka bir etki alanına yazma işlemleri gibi belirli işlemleri gerçekleştirmek için bir etki alanında Web sitesi tarafından barındırılan bir sayfada JavaScript 'e izin vermez. Aynı-kaynak ilkesi olarak bilinen bu ilke, bir sayfadaki kötü amaçlı kodun başka bir Web sayfasındaki verilere erişim sağlamasını önler. Ancak, bulutta bir çözüm oluşturulurken aynı kaynak ilkesi bir kısıtlama olabilir. Çıkış noktaları arası kaynak paylaşımı (CORS), hedef etki alanının kaynak etki alanında yer alan isteklere güvendiği tarayıcıyla iletişim kurmasını sağlayan bir tarayıcı özelliğidir.

Örneğin, Azure 'da çalışan bir Web uygulamasının bir kaynak için bir Azure depolama hesabına yönelik bir istek oluşturduğunu varsayalım. Web uygulaması, kaynak etki alanıdır ve depolama hesabı hedef etki alanıdır. Azure depolama hizmetlerinden herhangi biri için CORS 'yi, kaynak etki alanından gelen istekleri Azure depolama tarafından güvenilen Web tarayıcısıyla iletişim kuracak şekilde yapılandırabilirsiniz. CORS hakkında daha fazla bilgi için bkz. [Azure depolama için çıkış noktaları arası kaynak paylaşımı (CORS) desteği](/rest/api/storageservices/Cross-Origin-Resource-Sharing--CORS--Support-for-the-Azure-Storage-Services).  
  
Hem SAS hem de CORS, Web uygulamanızda gereksiz yükün oluşmasını önlemenize yardımcı olabilir.  

## <a name="caching"></a>Önbelleğe Alma

Önbelleğe alma, performans açısından önemli bir rol oynar. Aşağıdaki bölümlerde, önbelleğe alma en iyi yöntemleri ele alınmaktadır.

### <a name="reading-data"></a>Verileri okuma

Genel olarak, verileri bir kez okumak tercih edilir. Bir kullanıcıya içerik olarak sunulacak şekilde Azure depolama 'dan 50 MIB blobu alan bir Web uygulaması örneğini göz önünde bulundurun. İdeal olarak, uygulama blobu diske yerel olarak önbelleğe alır ve ardından önbelleğe alınmış sürümü sonraki Kullanıcı istekleri için alır.

Bir Blobun bu yana değiştirilmediyse bir blob almanın önlenmesi için bir yol, değiştirme zamanına ilişkin bir koşullu üst bilgiyle alma işlemini nitelendirmaktır. Son değiştirme zamanı, blob 'un önbelleğe alındığı zamandan sonra ise, blob alınır ve yeniden önbelleğe alınır. Aksi takdirde, önbelleğe alınan BLOB en iyi performans için alınır.

Ayrıca, blob 'u aldıktan sonra kısa bir süre boyunca blob 'un değişmeden kaldığını varsaymak için uygulamanızı tasarlamaya karar verebilirsiniz. Bu durumda, uygulamanın, bu Aralık süresince Blobun değiştirilip değiştirilmediğini denetlemesi gerekmez.

Uygulama tarafından sık kullanılan yapılandırma verileri, arama verileri ve diğer veriler, önbelleğe alma için iyi adaylardır.  

Koşullu üstbilgileri kullanma hakkında daha fazla bilgi için bkz. [BLOB hizmeti işlemleri için koşullu üstbilgiler belirtme](/rest/api/storageservices/specifying-conditional-headers-for-blob-service-operations).  

### <a name="uploading-data-in-batches"></a>Toplu işlemlere veri yükleme

Bazı senaryolarda, verileri yerel olarak toplayabilir ve sonra her bir veri parçasını hemen karşıya yüklemek yerine bir toplu işe düzenli olarak yükleyebilirsiniz. Örneğin, bir Web uygulamasının etkinliklerin günlük dosyasını koruduğunu varsayalım. Uygulama, her etkinliğin ayrıntılarını bir tabloya (birçok depolama işlemi gerektiren) veya bir yerel günlük dosyasına kaydedebilir ve sonra tüm etkinlik ayrıntılarını bir blob 'a ayrılmış bir dosya olarak düzenli olarak karşıya yükleyebilir. Her günlük girdisi boyutu 1 KB ise, tek bir işlemde binlerce girişi karşıya yükleyebilirsiniz. Tek bir işlem, boyutu 64 MIB 'ye kadar olan bir Blobun yüklemeyi destekler. Uygulama geliştiricisi, istemci cihazı veya karşıya yükleme hatalarının olasılığını tasarlaması gerekir. Etkinlik verilerinin tek bir etkinlik yerine bir zaman aralığı için indirilmesi gerekiyorsa, blob Storage 'ın kullanılması tablo depolama üzerinden önerilir.

## <a name="net-configuration"></a>.NET yapılandırması

.NET Framework kullanılıyorsa, bu bölümde önemli performans iyileştirmeleri yapmak için kullanabileceğiniz çeşitli hızlı yapılandırma ayarları listelenir.  Diğer dilleri kullanıyorsanız, seçtiğiniz dilde benzer kavramların uygulayıp uygulamamın olup olmadığını kontrol edin.  

### <a name="use-net-core"></a>.NET Core kullanın

Performans geliştirmelerinden faydalanmak için .NET Core 2,1 veya sonraki bir sürümü ile Azure depolama uygulamalarınızı geliştirin. Mümkünse .NET Core 3. x kullanılması önerilir.

.NET Core 'da performans iyileştirmeleri hakkında daha fazla bilgi için aşağıdaki blog gönderilerine bakın:

- [.NET Core 3,0 ' de performans Iyileştirmeleri](https://devblogs.microsoft.com/dotnet/performance-improvements-in-net-core-3-0/)
- [.NET Core 2,1 ' de performans Iyileştirmeleri](https://devblogs.microsoft.com/dotnet/performance-improvements-in-net-core-2-1/)

### <a name="increase-default-connection-limit"></a>Varsayılan bağlantı sınırını artır

.NET sürümünde aşağıdaki kod, varsayılan bağlantı sınırını (genellikle bir istemci ortamında veya bir sunucu ortamındaki on iki) 100 ' e yükseltir. Genellikle, değerini uygulamanız tarafından kullanılan iş parçacığı sayısı için yaklaşık olarak ayarlamanız gerekir. Herhangi bir bağlantıyı açmadan önce bağlantı sınırını ayarlayın.

```csharp
ServicePointManager.DefaultConnectionLimit = 100; //(Or More)  
```

Diğer programlama dilleri için, bağlantı sınırının nasıl ayarlanacağını öğrenmek için belgelere bakın.  

Daha fazla bilgi için [Web Hizmetleri: eşzamanlı bağlantılar](https://blogs.msdn.microsoft.com/darrenj/2005/03/07/web-services-concurrent-connections/)' a bakın.  

### <a name="increase-minimum-number-of-threads"></a>En az iş parçacığı sayısını artır

Zaman uyumsuz görevlerle birlikte zaman uyumlu çağrılar kullanıyorsanız, iş parçacığı havuzundaki iş parçacığı sayısını artırmak isteyebilirsiniz:

```csharp
ThreadPool.SetMinThreads(100,100); //(Determine the right number for your application)  
```

Daha fazla bilgi için bkz [. ThreadPool. SetMinThreads](/dotnet/api/system.threading.threadpool.setminthreads) metodu.  

## <a name="unbounded-parallelism"></a>Sınırlandırılmamış paralellik

Paralellik performansı performans için harika olsa da, sınırsız paralellik kullanma konusunda dikkatli olun, yani iş parçacığı sayısı veya paralel istekler üzerinde hiçbir sınır uygulanmaz. Verileri karşıya yükleme veya indirme, aynı depolama hesabındaki birden çok bölüme erişmek veya aynı bölümdeki birden çok öğeye erişmek için paralel istekleri sınırlandırdığınızdan emin olun. Paralellik sınırsız ise, uygulamanız istemci cihazının yeteneklerini veya depolama hesabının ölçeklenebilirlik hedeflerini aşacağından, daha uzun gecikme süreleri ve azaltma olanakları elde edebilir.  

## <a name="client-libraries-and-tools"></a>İstemci kitaplıkları ve araçları

En iyi performans için, her zaman Microsoft tarafından sunulan en son istemci kitaplıklarını ve araçları kullanın. Azure depolama istemci kitaplıkları, çeşitli diller için kullanılabilir. Azure depolama, PowerShell ve Azure CLı 'yı da destekler. Microsoft bu istemci kitaplıklarını ve araçları göz önünde bulundurularak etkin bir şekilde geliştirir, en son hizmet sürümleriyle güncel tutar ve kendini kanıtlamış performans uygulamalarının çoğunu dahili olarak işlemesini sağlar. Daha fazla bilgi için bkz. [Azure depolama başvurusu belgeleri](/azure/storage/#reference).

## <a name="handle-service-errors"></a>Hizmet hatalarını işleme

Hizmet bir isteği işleye, Azure Storage bir hata döndürüyor. Belirli bir senaryoda Azure depolama tarafından döndürülebilecek hataları anlamak performansı iyileştirmek için yararlıdır.

### <a name="timeout-and-server-busy-errors"></a>Zaman aşımı ve sunucu meşgul hataları

Ölçeklenebilirlik sınırlarına yaklaşırsa, Azure Storage uygulamanızı kısıtlayabilir. Bazı durumlarda, Azure Storage bazı geçici bir durum nedeniyle isteği işleyemeyebilir. Her iki durumda da hizmet 503 (sunucu meşgul) veya 500 (zaman aşımı) hatası döndürebilir. Bu hatalar, hizmet daha yüksek aktarım hızına izin vermek için veri bölümlerinin yeniden dengelenmesi durumunda da meydana gelebilir. İstemci uygulaması genellikle bu hatalardan birine neden olan işlemi yeniden dener. Bununla birlikte, ölçeklenebilirlik hedeflerini aştığından Azure Storage uygulamanızı azaltsa veya hizmet isteği başka bir nedenle hizmet veremese bile, agresif yeniden denemeler sorunu kötüleşme edebilir. Bir üstel geri deneme ilkesi kullanılması önerilir ve istemci kitaplıkları varsayılan olarak bu davranışa sahiptir. Örneğin, uygulamanız 2 saniye sonra, 4 saniye, sonra 10 saniye, 30 saniye sonra yeniden deneyebilir ve ardından tamamen daha fazla verebilir. Bu şekilde, uygulamanız exacerbating davranışı yerine hizmetin yükünü önemli ölçüde azaltır.  

Bağlantı hataları, azaltma sonucu olmadığı ve geçici olması beklenen için hemen yeniden denenebilir.  

### <a name="non-retryable-errors"></a>Yeniden denenmeyen hatalar

İstemci kitaplıkları, hangi hataların yeniden deneneceği ve ne işleyebileceği hakkında bir tanıma yeniden denemeler gerçekleştirir. Ancak, Azure Storage REST API doğrudan arıyorsanız, yeniden denenmemelisiniz bazı hatalar vardır. Örneğin, 400 (Hatalı Istek) hatası, istemci uygulamanın beklenen biçimde olmadığı için işlenemeyen bir istek gönderdiğini gösterir. Bu istek her seferinde aynı yanıtı elde eder, bu nedenle yeniden denenme noktası yoktur. Azure Storage REST API doğrudan arıyorsanız, olası hatalardan haberdar olun ve yeniden denenip denenmeyeceğini unutmayın.

Azure Storage hata kodları hakkında daha fazla bilgi için bkz. [durum ve hata kodları](/rest/api/storageservices/status-and-error-codes2).

## <a name="copying-and-moving-blobs"></a>Blob 'ları kopyalama ve taşıma

Azure depolama, blob 'ları bir depolama hesabında, depolama hesapları arasında ve şirket içi sistemler ile bulut arasında kopyalama ve taşıma için çeşitli çözümler sağlar. Bu bölümde, performans üzerindeki etkileri açısından bu seçeneklerin bazıları açıklanmaktadır. BLOB depolama alanına veya blob depolamadan veri aktarma hakkında daha fazla bilgi için bkz. [veri aktarımı için bir Azure çözümü seçme](../common/storage-choose-data-transfer-solution.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

### <a name="blob-copy-apis"></a>Blob kopyalama API 'Leri

Blob 'ları depolama hesaplarına kopyalamak için [URL 'Den yerleştirme](/rest/api/storageservices/put-block-from-url) işlemini kullanın. Bu işlem, verileri herhangi bir URL kaynağından bir blok blobuna eşzamanlı olarak kopyalar. @No__t-0 işleminin kullanılması, depolama hesaplarına veri geçirirken gereken bant genişliğini önemli ölçüde azaltabilir. Kopyalama işlemi hizmet tarafında gerçekleştiğinden, verileri indirmeniz ve yeniden yüklemeniz gerekmez.

Aynı depolama hesabı içindeki verileri kopyalamak için [BLOB kopyalama](/rest/api/storageservices/Copy-Blob) işlemini kullanın. Verileri aynı depolama hesabı içinde kopyalamak genellikle hızla tamamlanır.  

### <a name="use-azcopy"></a>AzCopy’yi kullanma

AzCopy komut satırı yardımcı programı, blob 'ların depolama hesaplarına, üzerinden ve bunların tamamında toplu aktarımı için basit ve verimli bir seçenektir. AzCopy, bu senaryo için iyileştirilmiştir ve yüksek aktarım ücretleri elde edebilir. AzCopy sürüm 10, blob verilerini depolama hesapları arasında kopyalamak için `Put Block From URL` işlemini kullanır. Daha fazla bilgi için bkz. [AzCopy ile v10 arasındaki kullanarak Azure depolama 'ya veri kopyalama veya taşıma](/azure/storage/common/storage-use-azcopy-v10).  

### <a name="use-azure-data-box"></a>Azure Data Box kullan

Büyük hacimli verileri blob depolamaya aktarmak için Azure Data Box ailesini çevrimdışı aktarımlar için kullanmayı düşünün. Microsoft tarafından sağlanan Data Box cihazları zamana, ağ kullanılabilirliğine veya maliyetlere göre sınırlı olduğunuzda büyük miktarlarda verileri Azure 'a taşımak için iyi bir seçimdir. Daha fazla bilgi için bkz. [Azure veri kutusu belgeleri](/azure/databox/).

## <a name="content-distribution"></a>İçerik dağıtımı

Bazen bir uygulamanın aynı içeriği aynı içeriğe (örneğin, bir Web sitesinin giriş sayfasında kullanılan bir ürün tanıtımı Videosu) aynı veya birden çok bölgede yer aldığı şekilde sunması gerekir. Bu senaryoda blob içeriğini coğrafi olarak dağıtmak için Azure CDN gibi bir Content Delivery Network (CDN) kullanın. Tek bir bölgede bulunan ve diğer bölgelere düşük gecikme süresiyle içerik teslim edebilen bir Azure depolama hesabının aksine, Azure CDN dünyanın dört bir yanındaki birden fazla veri merkezinde sunucu kullanır. Ayrıca, bir CDN genellikle tek bir depolama hesabından çok daha yüksek çıkış limitlerini destekleyebilir.  

Azure CDN hakkında daha fazla bilgi için bkz. [Azure CDN](../../cdn/cdn-overview.md).

## <a name="use-metadata"></a>Meta verileri kullan

Blob hizmeti, blob özellikleri veya meta veri içerebilen baş isteklerini destekler. Örneğin, uygulamanız bir fotoğraftan Exif (exchangable görüntü biçimi) verilerine ihtiyaç duyuyorsa fotoğrafı alabilir ve ayıklayabilir. Bant genişliğini kaydetmek ve performansı artırmak için, uygulama fotoğrafı karşıya yüklerken uygulamanız Exif verilerini Blobun meta verilerinde saklayabilir. Daha sonra Exif verilerini meta verilerde yalnızca bir HEAD isteği kullanarak alabilirsiniz. Yalnızca meta veri alma, blob 'un tam içeriği değil, önemli bant genişliği kazandırır ve Exif verilerini ayıklamak için gereken işlem süresini azaltır. Blob başına 8 kıb meta verilerin depolanabileceğini aklınızda bulundurun.  

## <a name="upload-blobs-quickly"></a>Blobları hızlıca karşıya yükleyin

Blob 'ları hızlı bir şekilde yüklemek için önce bir blob veya çok fazla yükleme yapılıp yapılmayacağını belirlemelisiniz. Senaryonuza bağlı olarak kullanılacak doğru yöntemi öğrenmek için aşağıdaki kılavuzu kullanın.  

### <a name="upload-one-large-blob-quickly"></a>Büyük bir blobu hızlıca karşıya yükle

Tek bir büyük blobu hızlı bir şekilde karşıya yüklemek için bir istemci uygulaması, blokları veya sayfalarını paralel olarak, tek blob 'lar ve depolama hesabı için bir bütün olarak ölçeklenebilirlik hedefleri açısından karşıya yükleyebilir. Azure Storage istemci kitaplıkları, yüklemeyi paralel olarak destekler. Örneğin, .NET veya Java 'da izin verilen eşzamanlı isteklerin sayısını belirtmek için aşağıdaki özellikleri kullanabilirsiniz. Desteklenen diğer dillere yönelik istemci kitaplıkları benzer seçenekler sağlar.

- .NET için [Blobrequestoptions. ParallelOperationThreadCount](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.paralleloperationthreadcount) özelliğini ayarlayın.
- Java/Android için [Blobrequestoptions. setConcurrentRequestCount (Final Integer concurrentRequestCount)](/java/api/com.microsoft.azure.storage.blob._blob_request_options.setconcurrentrequestcount) metodunu çağırın.

### <a name="upload-many-blobs-quickly"></a>Birçok blobı hızlıca karşıya yükleyin

Birçok blobu hızlıca karşıya yüklemek için Blobları paralel olarak karşıya yükleyin. Paralel olarak karşıya yüklemek, her seferinde paralel blok yüklemesine sahip tek Blobları karşıya yüklemeden daha hızlıdır. bu nedenle, yükleme işlemini depolama hizmetinin birden çok bölümü arasında yayar. AzCopy, yüklemeler için varsayılan olarak paralel olarak çalışır ve bu senaryo için önerilir. Daha fazla bilgi için bkz. [AzCopy ile çalışmaya başlama](../common/storage-use-azcopy-v10.md).  

## <a name="choose-the-correct-type-of-blob"></a>Doğru blob türünü seçin

Azure depolama, blok bloblarını, ekleme bloblarını ve sayfa bloblarını destekler. Belirli bir kullanım senaryosunda, blob türü seçiminiz çözümünüzün performansını ve ölçeklenebilirliğini etkileyecektir.

Blok Blobları, büyük miktarlarda verileri verimli bir şekilde yüklemek istediğinizde uygundur. Örneğin, blob depolamaya fotoğraf veya video yükleyen bir istemci uygulaması blok bloblarını hedefleyebilir.

Ekleme Blobları bloklardan oluşan blok bloblarına benzerdir. Bir ekleme blobu değiştirdiğinizde, bloklar yalnızca Blobun sonuna eklenir. Ekleme Blobları, bir uygulamanın mevcut bir bloba veri eklemesi gerektiğinde günlüğe kaydetme gibi senaryolar için yararlıdır.

Sayfa Blobları, uygulamanın verilerde rastgele yazma işlemleri yapması gerekiyorsa uygundur. Örneğin, Azure sanal makine diskleri sayfa Blobları olarak depolanır. Daha fazla bilgi için bkz. [blok bloblarını, ekleme bloblarını ve sayfa Bloblarını anlama](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs).  

## <a name="next-steps"></a>Sonraki adımlar

- [Depolama hesapları için Azure depolama ölçeklenebilirlik ve performans hedefleri](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
- [Durum ve hata kodları](/rest/api/storageservices/Status-and-Error-Codes2)
