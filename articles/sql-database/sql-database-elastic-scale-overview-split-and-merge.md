---
title: Ölçeği genişletilen bulut veritabanları arasında veri taşıma
description: Elastik veritabanı API 'Leri kullanarak parçaların nasıl düzenleneceğini ve verileri şirket içinde barındırılan bir hizmet aracılığıyla nasıl taşıyabileceğinizi açıklar.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: 00f579017ce4dd79e913565ee27698398b5feb38
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73823598"
---
# <a name="moving-data-between-scaled-out-cloud-databases"></a>Ölçeği genişletilen bulut veritabanları arasında veri taşıma

Bir hizmet geliştiricisi olarak yazılımınız varsa ve uygulamanız büyük ölçüde talep alıyorsa, büyümeye uyum sağlaması gerekir. Daha fazla veritabanı (parça) ekleyebilirsiniz. Veri bütünlüğünü kesintiye uğratmadan verileri yeni veritabanlarına nasıl dağıtırın? Verileri kısıtlanmış veritabanlarından yeni veritabanlarına taşımak için **bölünmüş birleştirme aracını** kullanın.  

Bölünmüş birleştirme aracı bir Azure Web hizmeti olarak çalışır. Yönetici veya geliştirici, farklı veritabanları (parçalar) arasında parçaları (parçalardan veriler) taşımak için aracı kullanır. Araç, hizmet meta veri veritabanını korumak için parça eşleme yönetimini kullanır ve tutarlı eşlemeler sağlar.

![Genel Bakış][1]

## <a name="download"></a>İndir

[Microsoft. Azure. SqlDatabase. Elayapışscale. Service. SplitMerge](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge/)

## <a name="documentation"></a>Belgeler

1. [Elastik veritabanı bölünmüş birleştirme araç öğreticisi](sql-database-elastic-scale-configure-deploy-split-and-merge.md)
2. [Bölünmüş birleştirme güvenlik yapılandırması](sql-database-elastic-scale-split-merge-security-configuration.md)
3. [Bölünmüş birleştirme güvenlik konuları](sql-database-elastic-scale-split-merge-security-configuration.md)
4. [Parça eşleme yönetimi](sql-database-elastic-scale-shard-map-management.md)
5. [Ölçeği genişletilen mevcut veritabanlarını geçirme](sql-database-elastic-convert-to-use-elastic-tools.md)
6. [Elastik veritabanı araçları](sql-database-elastic-scale-introduction.md)
7. [Elastik veritabanı araçları sözlüğü](sql-database-elastic-scale-glossary.md)

## <a name="why-use-the-split-merge-tool"></a>Ayırma-birleştirme aracını neden kullanmalısınız?

- **Yapılandırmada**

  Uygulamaların tek bir Azure SQL DB veritabanı sınırlarının ötesine esnek bir şekilde genişlemek gerekir. Bütünlüğü korurken verileri gerektiğinde yeni veritabanlarına taşımak için aracını kullanın.

- **Büyütmek için Böl**

  Patlayıcı büyümeyi işlemek için genel kapasiteyi artırmak amacıyla, verileri birleştirerek ve kapasite ihtiyaçları karşılanana kadar artımlı olarak daha fazla veritabanına dağıtarak ek kapasite oluşturun. Bu, **bölünmüş** özelliğin ana bir örneğidir.

- **Küçültme ile Birleştir**

  Bir işletmenin mevsimsel doğası nedeniyle kapasite 'nin küçültülmesi gerekir. Araç, iş yavaştığı sırada ölçeği daha az ölçek birimlerine ölçeklendirmenize imkan tanır. Elastik ölçek bölünmüş birleştirme hizmetindeki ' Merge ' özelliği bu gereksinimi kapsıyor.

- **Parçalamayı taşıyarak etkin noktaları yönetme**

  Veritabanı başına birden çok kiracı ile, parçaların parçalara ayrılması bazı parçalar üzerinde kapasite performans sorunlarına neden olabilir. Bu, parçaları yeniden ayırmayı veya meşgul parçaların yeni veya daha az kullanılan parçalara taşınmasını gerektirir.

## <a name="concepts--key-features"></a>Kavramlar & temel özellikler

- **Müşteri tarafından barındırılan hizmetler**

  Bölünmüş birleştirme, müşteri tarafından barındırılan bir hizmet olarak dağıtılır. Hizmeti Microsoft Azure aboneliğinizde dağıtmanız ve barındırmalısınız. NuGet 'den indirdiğinizde yüklediğiniz paket, belirli dağıtımınız için bilgilerle tamamlanacak bir yapılandırma şablonu içerir. Ayrıntılar için [bölünmüş birleştirme öğreticisine](sql-database-elastic-scale-configure-deploy-split-and-merge.md) bakın. Hizmet Azure aboneliğinizde çalıştığından, hizmetin güvenlik yönlerinin çoğunu denetleyebilir ve yapılandırabilirsiniz. Varsayılan şablon, SSL, sertifika tabanlı istemci kimlik doğrulaması, depolanan kimlik bilgileri için şifreleme, DoS koruma ve IP kısıtlamaları yapılandırma seçeneklerini içerir. Aşağıdaki belge [bölünmüş birleştirme güvenlik yapılandırmasındaki](sql-database-elastic-scale-split-merge-security-configuration.md)güvenlik yönleri hakkında daha fazla bilgi edinebilirsiniz.

  Varsayılan dağıtılan hizmet bir çalışan ve bir Web rolüyle çalışır. Her biri Azure Cloud Services a1 VM boyutunu kullanır. Paketi dağıttığınızda bu ayarları değiştiremeyeceğiniz sürece, çalışan bulut hizmetindeki başarılı bir dağıtımdan sonra (Azure portal aracılığıyla) bu ayarları değiştirebilirsiniz. Çalışan rolünün teknik nedenlerle tek bir örnek için yapılandırılmamalıdır.

- **Parça eşleme tümleştirmesi**

  Bölünmüş birleştirme hizmeti, uygulamanın parça eşlemesiyle etkileşime girer. Aralıkları ayırmak veya birleştirmek için bölünmüş birleştirme hizmetini kullanırken ya da parçalar arasındaki parçaları taşımak için, hizmet otomatik olarak parça eşlemini güncel tutar. Bunu yapmak için hizmet, uygulamanın parça eşleme Yöneticisi veritabanına bağlanır ve bölme/birleştirme/taşıma isteği ilerlemesiyle aralıkları ve eşlemeleri tutar. Bu, bölünmüş birleştirme işlemleri yapıldığında parça haritasının her zaman güncel bir görünüm sunmasını sağlar. Bölünmüş, birleştirme ve parçalanm taşıma işlemleri, kaynak parçalardan hedef parça olarak bir toplu iş izin verlim grubu taşıyarak uygulanır. Parça taşıma işlemi sırasında, parçalara geçerli toplu iş, parça eşlemesinde çevrimdışı olarak işaretlenir ve **Openconnectionforkey** API kullanılarak verilere bağımlı yönlendirme bağlantıları için kullanılamaz.

- **Tutarlı kıardlet bağlantıları**

  Veri taşıma işlemi yeni bir parçalar toplu işi için başladığında, parça eşleme API 'Leri, parçalara yönelik olan ve parça Haritası API 'Lerinden sonraki bağlantılar, veri taşıma işlemi olduğu sürece engellenir. Tutarsızlıklardan kaçınmak için devam ediyor. Aynı parça üzerindeki diğer parçalardaki bağlantılar da sonlandırılabilir, ancak yeniden denemeye hemen sonra başarılı olur. Toplu işlem taşındıktan sonra, parçalar hedef parça için çevrimiçi olarak yeniden işaretlenir ve kaynak veriler kaynak parçadan kaldırılır. Bu hizmet, tüm parçalar taşınana kadar her toplu işlem için bu adımları gider. Bu, tüm bölünmüş/birleştirme/taşıma işleminin kursu sırasında birkaç bağlantı sonlandırma işlemine yol açacaktır.  

- **Kıardlet kullanılabilirliğini yönetme**

  Yukarıda bahsedildiği gibi, yukarıdaki geçerli yığın toplu işi ile bağlantı altına alınanı sınırlamak, bir kerede tek bir yığın toplu işi için kullanım dışı bir toplu iş grubu kapsamını kısıtlar. Bu, bir bölme veya birleştirme işlemi sırasında tüm parçalar için tüm parçalar çevrimdışı kaldığı bir yaklaşım üzerinde tercih edilir. Tek seferde farklı parçalamayı izin veren olarak tanımlanan bir toplu işin boyutu, bir yapılandırma parametresidir. Uygulamanın kullanılabilirlik ve performans gereksinimlerine bağlı olarak, her bölünmüş ve birleştirme işlemi için tanımlanabilir. Parça eşlemesinde kilitlemekte olan aralığın, belirtilen toplu iş boyutundan daha büyük olabileceğini unutmayın. Bunun nedeni, hizmetin, verilerdeki parçalı anahtar değerlerinin gerçek sayısının, toplu iş boyutuyla yaklaşık olarak eşleşmesini sağlayan Aralık boyutunu seçer. Bu, özellikle de ayrılmış parça anahtarları için özel olarak dikkat edilmesi açısından önemlidir.

- **Meta veri depolama**

  Bölünmüş birleştirme hizmeti, durumunu korumak ve istek işleme sırasında günlükleri tutmak için bir veritabanı kullanır. Kullanıcı bu veritabanını aboneliklerinde oluşturur ve hizmet dağıtımı için yapılandırma dosyasında bağlantı dizesini sağlar. Kullanıcının kuruluşundaki Yöneticiler ayrıca, istek ilerlemesini gözden geçirmek ve olası hatalarla ilgili ayrıntılı bilgileri araştırmak için bu veritabanına bağlanabilir.

- **Parçalı tanıma**

  Bölünmüş birleştirme hizmeti, (1) parçalı tablolar, (2) başvuru tabloları ve (3) normal tablo arasında ayrım yapar. Bölünmüş/birleştirme/taşıma işleminin semantiği, kullanılan tablo türüne bağlıdır ve aşağıdaki gibi tanımlanır:

  - **Parçalı tablolar**

    Bölünmüş, birleştirme ve taşıma işlemleri, parçalanarak kaynaktan hedef parçasına geçiş yapmanızı sağlar. Genel isteğin başarıyla tamamlanmasından sonra, bu parçalar kaynak üzerinde artık mevcut değildir. Hedef tabloların hedef parça üzerinde mevcut olması gerektiğini ve işlem işlenmeden önce hedef aralıktaki verileri içermemesi gerektiğini unutmayın.

  - **Başvuru tabloları**

    Başvuru tablolarında, bölünmüş, birleştirme ve taşıma işlemleri verileri kaynaktan hedef parçaya kopyalar. Ancak, hedefte bu tabloda zaten bir satır varsa, belirli bir tablo için hedef parça üzerinde hiçbir değişiklik gerçekleşmediğini unutmayın. İşlenmek üzere herhangi bir başvuru tablosu kopyalama işlemi için tablo boş olmalıdır.

  - **Diğer tablolar**

    Diğer tablolar, bir bölünmüş ve birleştirme işleminin kaynağında ya da hedefinde bulunabilir. Bölünmüş birleştirme hizmeti, veri taşıma veya kopyalama işlemleri için bu tabloları yoksayar. Ancak, kısıtlamalar söz konusu olduğunda bu işlemleri kesintiye uğratabilecekleri unutulmamalıdır.

    Başvuru ile parçalı tablolardaki bilgiler, parça eşlemesindeki `SchemaInfo` API 'Leri tarafından sağlanır. Aşağıdaki örnek, belirli bir parça eşleme Yöneticisi nesnesinde bu API 'lerin kullanımını göstermektedir:

    ```csharp
    // Create the schema annotations
    SchemaInfo schemaInfo = new SchemaInfo();

    // Reference tables
    schemaInfo.Add(new ReferenceTableInfo("dbo", "region"));
    schemaInfo.Add(new ReferenceTableInfo("dbo", "nation"));

    // Sharded tables
    schemaInfo.Add(new ShardedTableInfo("dbo", "customer", "C_CUSTKEY"));
    schemaInfo.Add(new ShardedTableInfo("dbo", "orders", "O_CUSTKEY"));
    // Publish
    smm.GetSchemaInfoCollection().Add(Configuration.ShardMapName, schemaInfo);
    ```

    ' Region ' ve ' Nation ' tabloları başvuru tabloları olarak tanımlanır ve bölünmüş/birleştirme/taşıma işlemleri ile kopyalanır. sırasıyla ' Customer ' ve ' Orders ', parçalı tablolar olarak tanımlanmıştır. `C_CUSTKEY` ve `O_CUSTKEY` parçalı anahtar olarak görev yapar.

- **Bilgi tutarlılığı**

  Bölünmüş birleştirme hizmeti, tablolar arasındaki bağımlılıkları analiz eder ve başvuru tablolarının ve parçalanmalarına yönelik işlemleri hazırlamak için yabancı anahtar birincil anahtar ilişkilerini kullanır. Genel olarak, başvuru tabloları ilk olarak bağımlılık sırasında kopyalanır, sonra da her bir toplu iş içindeki bağımlılıkları sırasıyla silinir. Bu, hedef parçada FK-PK kısıtlamalarının yeni veriler ulaştığı için kabul edilecek şekilde gereklidir.

- **Parça Haritası tutarlılığı ve nihai tamamlama**

  Başarısızlık durumunda, bölünmüş birleştirme hizmeti herhangi bir kesinti ve amaçlar sonrasında devam eden istekleri tamamladıktan sonra işlemleri sürdürür. Ancak, hedef parça, onarım ötesinde kayıp veya tehlikeye atıldığında, kurtarılamaz durumlar olabilir. Bu koşullarda, taşınması beklenen bazı parçalar kaynak parça üzerinde olmaya devam edebilir. Hizmet, yalnızca gerekli veriler hedefe başarıyla kopyalandıktan sonra eşlemelerin güncelleştirilmesini sağlar. Parçalarla ilgili tüm veriler hedefe kopyalandıktan sonra ve karşılık gelen eşlemeler başarıyla güncelleştirildikten sonra yalnızca kaynakta silinir. Silme işlemi, hedef parça üzerinde zaten çevrimiçi olduğu sürece arka planda gerçekleşir. Bölünmüş birleştirme hizmeti, parça eşlemesinde depolanan eşlemelerin doğruluğunu her zaman sağlar.

## <a name="the-split-merge-user-interface"></a>Bölünmüş birleştirme Kullanıcı arabirimi

Bölünmüş birleştirme hizmeti paketi bir çalışan rolü ve bir Web rolü içerir. Web rolü, bölünmüş birleştirme isteklerini etkileşimli bir şekilde göndermek için kullanılır. Kullanıcı arabiriminin ana bileşenleri şunlardır:

- **İşlem türü**

  İşlem türü, bu istek için hizmet tarafından gerçekleştirilen işlem türünü denetleyen bir radyo düğmesidir. Bölünmüş, birleştirme ve taşıma senaryoları arasından seçim yapabilirsiniz. Ayrıca, daha önce gönderilen bir işlemi iptal edebilirsiniz. Aralık parça haritaları için bölünmüş, birleştirme ve taşıma isteklerini kullanabilirsiniz. Liste parça haritaları yalnızca taşıma işlemlerini destekler.

- **Parça Haritası**

  İstek parametrelerinin sonraki bölümü parça eşlemesiyle ilgili bilgileri ve parça eşlemenizi barındıran veritabanını içerir. Özellikle, shardmap 'i barındıran Azure SQL veritabanı sunucusunun ve veritabanının adını, parça eşleme veritabanına bağlanmak için kimlik bilgilerini ve son olarak parça haritasının adını belirtmeniz gerekir. Şu anda işlem yalnızca tek bir kimlik bilgileri kümesi kabul eder. Bu kimlik bilgilerinin parça eşlemesinde ve parçaları üzerindeki Kullanıcı verilerinde değişiklik yapmak için yeterli izinlere sahip olması gerekir.

- **Kaynak aralığı (bölme ve birleştirme)**

  Bölünmüş ve birleştirme işlemi, düşük ve yüksek anahtarını kullanarak bir aralığı işler. Sınırsız bir yüksek anahtar değeri olan bir işlem belirtmek için, "yüksek anahtar en yüksek" onay kutusunu işaretleyin ve yüksek anahtar alanını boş bırakın. Belirttiğiniz Aralık anahtarı değerlerinin, parça Haritalarınızın eşlemesiyle bir eşleme ve sınırlarının tam olarak eşleşmesi gerekmez. Tüm hizmette herhangi bir Aralık sınırı belirtmezseniz, en yakın aralığı sizin için otomatik olarak çıkarmaz. Belirli bir parça eşlemesindeki geçerli eşlemeleri almak için GetMappings. ps1 PowerShell betiğini kullanabilirsiniz.

- **Bölünmüş kaynak davranışı (bölünmüş)**

  Bölme işlemleri için, kaynak aralığın bölüneceği noktayı tanımlayın. Bunu, bölünmesinin gerçekleşmesini istediğiniz yere parçalama anahtarı vererek yapabilirsiniz. Aralığın alt kısmının (bölünmüş anahtar hariç) mi taşınacağını yoksa üst parçanın (bölünmüş anahtar dahil) mi taşınacağını isteyip istemediğinizi belirtin radyo düğmesini kullanın.

- **Kaynak KIM (taşıma)**

  Taşıma işlemleri, bölünmüş veya birleştirme işlemlerinden farklıdır, bu da kaynağı tanımlamaya yönelik bir Aralık gerektirmez. Taşıma kaynağı yalnızca taşımayı planladığınız parçalama anahtar değeri tarafından tanımlanır.

- **Hedef parça (bölünmüş)**

  Bölünmüş işlemin kaynağı hakkında bilgi sağladıktan sonra, hedef için Azure SQL veritabanı sunucusu ve veritabanı adı sağlayarak verilerin nereye kopyalanmasını istediğinizi tanımlamanız gerekir.

- **Hedef Aralık (birleştirme)**

  Birleştirme işlemleri, parçalamayı var olan bir parçaya taşır. Mevcut parçayı, birleştirmek istediğiniz mevcut aralığın Aralık sınırlarını sağlayarak belirlersiniz.

- **Toplu iş boyutu**

  Toplu iş boyutu, veri taşıma sırasında aynı anda çevrimdışı olacak parçalar sayısını denetler. Bu, kısma izin veren uzun süreli kapalı kalma süresine duyarlı olduğunuzda daha küçük değerler kullanabileceğiniz bir tamsayı değeridir. Daha büyük değerler, belirli bir parçanın çevrimdışı olmasına karşın performansı iyileştirebilecek süreyi artıracaktır.

- **İşlem KIMLIĞI (Iptal)**

  Artık gerekli olmayan devam eden bir işlem varsa, bu alanda işlem KIMLIĞI sağlayarak işlemi iptal edebilirsiniz. İşlem KIMLIĞINI istek durumu tablosundan alabilirsiniz (bkz. Bölüm 8,1) veya isteği gönderdiğiniz Web tarayıcısında çıkış.

## <a name="requirements-and-limitations"></a>Gereksinimler ve sınırlamalar

Bölünmüş birleştirme hizmetinin geçerli uygulanması aşağıdaki gereksinimlere ve sınırlamalara tabidir:

- Parçaların bu parçalar üzerinde bir bölünmüş birleştirme işleminden önce bulunması ve parça eşlemesinde kayıtlı olması gerekir.
- Hizmet, işlemlerinin bir parçası olarak tabloları veya diğer veritabanı nesnelerini otomatik olarak oluşturmaz. Bu, tüm parçalı tablolar ve başvuru tabloları için şemanın, herhangi bir bölme/birleştirme/taşıma işleminden önce hedef parça üzerinde bulunması gerektiği anlamına gelir. Özel olarak bulunan parçalı tablolar, yeni parçaların bölünmüş/birleştirme/taşıma işlemi tarafından ekleneceği aralıkta boş olması gerekir. Aksi takdirde, işlem hedef parça üzerinde ilk tutarlılık denetimi başarısız olur. Ayrıca, başvuru verileri yalnızca başvuru tablosu boş ise ve başvuru tablolarında diğer eşzamanlı yazma işlemleriyle ilgili tutarlılık garantisi yoksa kopyalanır. Bunu öneririz: bölünmüş/birleştirme işlemlerini çalıştırırken, başka bir yazma işlemi başvuru tablolarında değişiklik yapalım.
- Hizmet, büyük parçalar için performansı ve güvenilirliği artırmak üzere parçalama anahtarını içeren benzersiz bir dizin veya anahtarla belirlenen satır kimliğini kullanır. Bu, hizmetin verileri yalnızca parçalı anahtar değerinden daha ayrıntılı bir şekilde taşımasına olanak tanır. Bu, işlem sırasında gereken maksimum günlük alanı ve kilitleme miktarını azaltmaya yardımcı olur. Bu tabloyu bölme/birleştirme/taşıma istekleri ile kullanmak istiyorsanız, belirli bir tabloda bulunan parçalı anahtar dahil olmak üzere benzersiz bir dizin veya birincil anahtar oluşturmayı düşünün. Performans nedenleriyle, parçalı anahtar anahtar veya dizinde önde gelen sütun olmalıdır.
- İstek işleme sürecinde, bazı kıardı verileri hem kaynak hem de hedef parça üzerinde bulunabilir. Bu, kıarde hareketi sırasında hatalara karşı korunmak için gereklidir. Bölünmüş birleştirme ile parça eşleme tümleştirmesi, parça eşlemesindeki **Openconnectionforkey** yöntemi kullanılarak veriye bağımlı yönlendirme API 'leri üzerinden bağlantıların tutarsız bir ara durum görmemesini sağlar. Ancak, **Openconnectionforkey** metodunu kullanmadan kaynak veya hedef parçalara bağlanırken, ayırma/birleştirme/taşıma istekleri devam edildiğinde tutarsız ara durumlar görünebilir. Bu bağlantılar, bağlantının temelindeki zamanlamaya veya parçaya göre kısmi veya yinelenen sonuçları gösterebilir. Bu kısıtlama Şu anda elastik ölçekli çok parçalı-sorgular tarafından yapılan bağlantıları içerir.
- Bölünmüş birleştirme hizmeti için meta veri veritabanı farklı roller arasında paylaşılmamalıdır. Örneğin, hazırlama aşamasında çalışan bölünmüş birleştirme hizmetinin bir rolü, üretim rolünden farklı bir meta veri veritabanına işaret ediyor olmalıdır.

## <a name="billing"></a>Faturalandırma

Bölünmüş birleştirme hizmeti Microsoft Azure aboneliğinizde bir bulut hizmeti olarak çalışır. Bu nedenle, bulut hizmetleri için ücretler hizmet örneğiniz için geçerlidir. Bölünmüş/birleştirme/taşıma işlemleri sıklıkla gerçekleştirmediğiniz takdirde, bölünmüş birleştirme bulut hizmetinizi silmeniz önerilir. Bu, çalışan veya dağıtılan bulut hizmeti örneklerinin maliyetlerini kaydeder. Her bölme veya birleştirme işlemini gerçekleştirmeniz gerektiğinde, kolayca çalıştırılabilir yapılandırmayı yeniden dağıtabilir ve başlatabilirsiniz.

## <a name="monitoring"></a>İzleme

### <a name="status-tables"></a>Durum tabloları

Bölünmüş birleştirme hizmeti, tamamlanan ve devam eden isteklerin izlenmesi için meta veri deposu veritabanında **RequestStatus** tablosu sağlar. Tablo, bölünmüş birleştirme hizmetinin bu örneğine gönderilen her bölünmüş birleştirme isteği için bir satır listeler. Her istek için aşağıdaki bilgileri verir:

- **İlişkin**

  İsteğin başlatıldığı saat ve tarih.

- **OperationId**

  İsteği benzersiz bir şekilde tanımlayan GUID. Bu istek hala devam ederken işlemi iptal etmek için de kullanılabilir.

- **Durum**

  İsteğin geçerli durumu. Devam eden istekler için, isteğin olduğu geçerli aşamayı de listeler.

- **CancelRequest**

  İsteğin iptal edilip edilmeyeceğini gösteren bayrak.

- **Lemesine**

  İşlem için tamamlanma yüzdesi tahmini. 50 değeri, işlemin yaklaşık %50 tamamlandığını gösterir.

- **Ayrıntılar**

  Daha ayrıntılı ilerleme raporu sağlayan bir XML değeri. Satır kümeleri kaynaktan hedefe kopyalanırken ilerleme raporu düzenli olarak güncelleştirilir. Hatalar veya özel durumlar söz konusu olduğunda, bu sütunda hata hakkında daha ayrıntılı bilgiler de yer alır.

### <a name="azure-diagnostics"></a>Azure Tanılama

Bölünmüş birleştirme hizmeti, izleme ve Tanılama için Azure SDK 2,5 temel alınarak Azure Tanılama kullanır. Tanılama yapılandırmasını burada açıklandığı gibi denetlersiniz: [Azure Cloud Services ve sanal makinelerde tanılamayı etkinleştirme](../cloud-services/cloud-services-dotnet-diagnostics.md). İndirme paketi iki tanılama yapılandırması içerir-biri web rolü için bir diğeri de çalışan rolü içindir. Günlük performans sayaçlarını, IIS günlüklerini, Windows olay günlüklerini ve bölünmüş birleştirme uygulama olay günlüklerini içeren tanımları içerir.

## <a name="deploy-diagnostics"></a>Tanılamayı dağıt

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager modülü Azure SQL veritabanı tarafından hala desteklenmektedir, ancak gelecekteki tüm geliştirmeler az. SQL modülüne yöneliktir. Bu cmdlet 'ler için bkz. [Azurerd. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Az Module ve Azurerd modüllerinde komutların bağımsız değişkenleri önemli ölçüde aynıdır.

NuGet paketi tarafından sunulan Web ve çalışan rolleri için tanılama yapılandırmasını kullanarak izlemeyi ve tanılamayı etkinleştirmek için Azure PowerShell kullanarak aşağıdaki komutları çalıştırın:

```powershell
    $storage_name = "<YourAzureStorageAccount>"
    $key = "<YourAzureStorageAccountKey"
    $storageContext = New-AzStorageContext -StorageAccountName $storage_name -StorageAccountKey $key  
    $config_path = "<YourFilePath>\SplitMergeWebContent.diagnostics.xml"
    $service_name = "<YourCloudServiceName>"
    Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name -Slot Production -Role "SplitMergeWeb"
    $config_path = "<YourFilePath>\SplitMergeWorkerContent.diagnostics.xml"
    $service_name = "<YourCloudServiceName>"
    Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name -Slot Production -Role "SplitMergeWorker"
```

Tanılama ayarlarını yapılandırma ve dağıtma hakkında daha fazla bilgiye buradan ulaşabilirsiniz: [Azure Cloud Services ve sanal makinelerde tanılamayı etkinleştirme](../cloud-services/cloud-services-dotnet-diagnostics.md).

## <a name="retrieve-diagnostics"></a>Tanılamayı al

Sunucu Gezgini ağacının Azure bölümünde Visual Studio Sunucu Gezgini tanılamalara kolayca erişebilirsiniz. Bir Visual Studio örneği açın ve menü çubuğunda görüntüle ' ye tıklayın ve Sunucu Gezgini. Azure aboneliğinize bağlanmak için Azure simgesine tıklayın. Ardından Azure-> Storage-> `<your storage account>`-> tabloları-> WADLogsTable ' a gidin. Daha fazla bilgi için bkz. [Sunucu Gezgini](https://msdn.microsoft.com/library/x603htbk.aspx).

![WADLogsTable][2]

Yukarıdaki şekilde vurgulanan WADLogsTable, bölünmüş birleştirme hizmetinin uygulama günlüğünden alınan ayrıntılı olayları içerir. İndirilen paketin varsayılan yapılandırmasının bir üretim dağıtımına yönelik olduğunu unutmayın. Bu nedenle, günlüklerin ve sayaçların hizmet örneklerinden çekilme aralığı büyük (5 dakika). Test ve geliştirme için, Web veya çalışan rolünün tanılama ayarlarını gereksinimlerinize göre ayarlayarak aralığı azaltın. Visual Studio Sunucu Gezgini role (yukarıya bakın) sağ tıklayın ve ardından tanılama yapılandırma ayarları iletişim kutusunda aktarım dönemini ayarlayın:

![Yapılandırma][3]

## <a name="performance"></a>Performans

Genel olarak, Azure SQL veritabanı 'nda daha yüksek ve daha iyi performans hizmeti katmanlarından daha iyi performans beklenmelidir. Daha yüksek hizmet katmanları için yüksek GÇ, CPU ve bellek ayırmaları, bölünmüş birleştirme hizmetinin kullandığı toplu kopyalama ve silme işlemlerine yarar. Bu nedenle, tanımlanmış, sınırlı bir süre için hizmet katmanını yalnızca bu veritabanları için artırın.

Hizmet, normal işlemlerinin bir parçası olarak doğrulama sorguları da gerçekleştirir. Bu doğrulama sorguları, hedef aralıktaki beklenmeyen veri varlığını denetler ve herhangi bir bölünmüş/birleştirme/taşıma işleminin tutarlı bir durumdan başlamasını sağlamaktır. Bu sorgular, işlem kapsamı ve istek tanımının bir parçası olarak belirtilen toplu iş boyutu tarafından tanımlanan temel anahtar aralıkları üzerinde çalışır. Bu sorgular, önde gelen sütun olarak parçalı anahtar içeren bir dizin bulunduğunda en iyi şekilde gerçekleştirilir.

Ayrıca, önde gelen sütun olarak parçalı anahtar içeren bir benzersizlik özelliği, hizmetin, günlük alanı ve bellek bakımından kaynak tüketimini sınırlayan iyileştirilmiş bir yaklaşım kullanmasına izin verir. Büyük veri boyutlarını taşımak için bu benzersizlik özelliği gereklidir (genellikle 1 GB üzerinde).

## <a name="how-to-upgrade"></a>Yükseltme

1. [Bölünmüş birleştirme hizmeti dağıtma](sql-database-elastic-scale-configure-deploy-split-and-merge.md)bölümündeki adımları izleyin.
2. Bölünmüş birleştirme dağıtımınız için bulut hizmeti yapılandırma dosyanızı, yeni yapılandırma parametrelerini yansıtacak şekilde değiştirin. Yeni bir gerekli parametre, şifreleme için kullanılan sertifikayla ilgili bilgiler. Bunu yapmanın kolay bir yolu, yeni yapılandırma şablonu dosyasını mevcut yapılandırmanıza karşı indirerek karşılaştırmaktır. Hem Web hem de çalışan rolü için "DataEncryptionPrimaryCertificateThumbprint" ve "DataEncryptionPrimary" ayarlarını eklediğinizden emin olun.
3. Güncelleştirmeyi Azure 'a dağıtmak için, şu anda çalışan tüm bölünmüş birleştirme işlemlerinin tamamlandığından emin olun. Bu, devam eden istekler için bölünmüş birleştirme meta veri veritabanındaki RequestStatus ve Pendingiş akışları tablolarını sorgulayarak kolayca yapabilirsiniz.
4. Yeni paket ve güncelleştirilmiş hizmet yapılandırma dosyanız ile Azure aboneliğinizde bölünmüş birleştirme için mevcut bulut hizmeti dağıtımınızı güncelleştirin.

Yükseltilecek birleştirme için yeni bir meta veri veritabanı sağlamanız gerekmez. Yeni sürüm, var olan meta veri veritabanınızı yeni sürüme otomatik olarak yükseltir.

## <a name="best-practices--troubleshooting"></a>En iyi yöntemler & sorun giderme

- Test kiracısı tanımlayın ve çeşitli parçalar arasında test kiracısıyla en önemli bölünmüş/birleştirme/taşıma işlemlerinizi yapın. Tüm meta verilerin parça haritanızda doğru tanımlandığından ve işlemlerin kısıtlamaları veya yabancı anahtarları ihlal etmediğinden emin olun.
- Veri boyutuyla ilgili sorunlarla karşılaşmatığınızdan emin olmak için, test kiracı veri boyutunu en büyük kiracınızın en büyük veri boyutunun üzerinde tutun. Bu, tek bir kiracının etrafında taşınması için gereken süre üst sınırını değerlendirmenize yardımcı olur.
- Şemanızın silme izni verdiğinden emin olun. Bölünmüş birleştirme hizmeti, veriler başarıyla hedefe kopyalandıktan sonra kaynak parçadan verileri kaldırabilme özelliği gerektirir. Örneğin, **silme tetikleyicileri** hizmetin kaynaktaki verileri silmesini engelleyebilir ve işlemlerin başarısız olmasına neden olabilir.
- Parçalı anahtar, birincil anahtarınızdaki veya benzersiz dizin tanımınızda önde gelen sütun olmalıdır. Bu, bölünmüş veya birleştirme doğrulama sorguları için en iyi performansı ve her zaman parçalı anahtar aralıklarında her zaman çalışan gerçek veri taşıma ve silme işlemlerini sağlar.
- Bölünmüş birleştirme hizmetinizi, veritabanlarınızın bulunduğu bölge ve veri merkezinde birlikte bulun.

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->
[1]:./media/sql-database-elastic-scale-overview-split-and-merge/split-merge-overview.png
[2]:./media/sql-database-elastic-scale-overview-split-and-merge/diagnostics.png
[3]:./media/sql-database-elastic-scale-overview-split-and-merge/diagnostics-config.png
