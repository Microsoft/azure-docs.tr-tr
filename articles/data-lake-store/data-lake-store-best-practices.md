---
title: Azure Data Lake Storage 1. kullanmaya yönelik en iyi uygulamalar | Microsoft Docs
description: Azure Data Lake Storage 1. kullanımı ile ilgili veri alımı, tarih güvenliği ve performans hakkında en iyi uygulamaları öğrenin (daha önce Azure Data Lake Store olarak bilinir)
services: data-lake-store
documentationcenter: ''
author: sachinsbigdata
manager: mtillman
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.date: 06/27/2018
ms.author: sachins
ms.openlocfilehash: 9a5c5f9a4033b70a664071d6077a69f38c905093
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96452224"
---
# <a name="best-practices-for-using-azure-data-lake-storage-gen1"></a>Azure Data Lake Storage 1. kullanmak için en iyi uygulamalar

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Bu makalede, Azure Data Lake Storage 1. ile çalışmaya yönelik en iyi yöntemler ve konular hakkında bilgi edineceksiniz. Bu makalede, Data Lake Storage 1. için güvenlik, performans, dayanıklılık ve izleme hakkında bilgi verilmektedir. Data Lake Storage 1. önce, Azure HDInsight gibi hizmetlerde gerçekten büyük verilerle çalışma karmaşıktır. Birden çok BLOB depolama hesabında verileri parçalara çıkaran ve bu ölçekte en iyi performansı elde etmek zorunda kaldık. Data Lake Storage 1., boyut ve performansa ilişkin sabit limitlerin çoğu kaldırılır. Ancak, Data Lake Storage 1. ile en iyi performansı elde edebilmeniz için bu makalenin kapsamakta olduğu bazı noktalar vardır.

## <a name="security-considerations"></a>Güvenlik konuları

Azure Data Lake Storage 1., Azure Active Directory (Azure AD) kullanıcıları, grupları ve hizmet sorumluları için POSIX erişim denetimleri ve ayrıntılı denetim sağlar. Bu erişim denetimleri, varolan dosya ve klasörlere ayarlanabilir. Erişim denetimleri, yeni dosyalara veya klasörlere uygulanabilen varsayılanlar oluşturmak için de kullanılabilir. İzinler varolan klasörlere ve alt nesnelere ayarlandığında, izinlerin her bir nesne üzerinde yinelemeli olarak yayılması gerekir. Çok sayıda dosya varsa, bu izinleri yayan uzun sürebilir. Geçen süre, saniye başına işlenen 30-50 nesne arasında değişebilir. Bu nedenle, klasör yapısını ve Kullanıcı gruplarını uygun şekilde planlayın. Aksi takdirde, verilerle çalışırken beklenmeyen gecikmeler ve sorunlara neden olabilir.

100.000 alt nesne içeren bir klasörünüz olduğunu varsayalım. Saniye başına işlenen 30 nesneden daha düşük bir sınır alırsanız tüm klasörün iznini güncelleştirmek için bir saat sürebilir. Data Lake Storage 1. ACL 'Ler hakkında daha fazla ayrıntı [Azure Data Lake Storage 1. Içindeki erişim denetiminde](data-lake-store-access-control.md)bulunmaktadır. ACL 'Leri yinelemeli olarak atamaya yönelik gelişmiş performans için Azure Data Lake Command-Line aracını kullanabilirsiniz. Araç birden çok iş parçacığı ve özyinelemeli gezinme mantığı oluşturarak milyonlarca dosyaya ACL 'Leri hızlıca uygulayın. Araç Linux ve Windows için kullanılabilir ve bu araç için [Belgeler](https://github.com/Azure/data-lake-adlstool) ve [indirmeler](https://aka.ms/adlstool-download) GitHub 'da bulunabilir. Aynı performans geliştirmeleri, Data Lake Storage 1. [.net](data-lake-store-data-operations-net-sdk.md) ve [Java](data-lake-store-get-started-java-sdk.md) SDK 'ları ile yazılmış kendi araçlarınızla etkinleştirilebilir.

### <a name="use-security-groups-versus-individual-users"></a>Bireysel kullanıcılara karşı güvenlik gruplarını kullanın

Data Lake Storage 1. büyük verilerle çalışırken, büyük olasılıkla hizmet sorumlusu Azure HDInsight gibi hizmetlerin verilerle çalışmasına izin vermek için kullanılır. Ancak, bireysel kullanıcıların da verilere erişmesi gereken durumlar olabilir. Bu gibi durumlarda, klasörler ve dosyalar için bireysel kullanıcı atamak yerine Azure Active Directory [güvenlik grupları](data-lake-store-secure-data.md#create-security-groups-in-azure-active-directory) kullanmanız gerekir.

Bir güvenlik grubuna izinler atandıktan sonra, gruptan kullanıcı ekleme veya kaldırma işlemi Data Lake Storage 1. güncelleştirme gerektirmez. Bu Ayrıca, [32 erişim ve varsayılan ACL](../azure-resource-manager/management/azure-subscription-service-limits.md#data-lake-storage-limits) 'lerin sınırını aşmamak için de yardımcı olur ( [sahip olan Kullanıcı](data-lake-store-access-control.md#the-owning-user), sahip olan [Grup](data-lake-store-access-control.md#the-owning-group), [maske](data-lake-store-access-control.md#the-mask)ve diğer), her zaman ilişkili olan dört adet POSIX stili ACL 'leri içerir.

### <a name="security-for-groups"></a>Gruplar için güvenlik

Açıklandığı gibi, kullanıcıların Data Lake Storage 1. erişmesi gerektiğinde Azure Active Directory güvenlik grupları kullanmak en iyisidir. İle başlamak için önerilen bazı gruplar, hesabın köküne yönelik olarak **Readonlyusers**, **Writeaccessusers** ve **fullaccessusers** olabilir ve hatta anahtar alt klasörleri için ayrı ayrı olabilir. Daha sonra eklenebilen ancak henüz tanımlanmayan başka bir Kullanıcı grubu varsa, belirli klasörlere erişimi olan kukla güvenlik grupları oluşturmayı düşünebilirsiniz. Güvenlik grubunun kullanılması, daha sonra binlerce dosyaya yeni izinler atamak için uzun bir işlem süresine gerek duymamasını sağlar.

### <a name="security-for-service-principals"></a>Hizmet sorumluları için güvenlik

Azure Active Directory hizmet sorumluları, genellikle Azure HDInsight gibi hizmetler tarafından Data Lake Storage 1. verilere erişim için kullanılır. Birden çok iş yükü genelinde erişim gereksinimlerine bağlı olarak, kuruluşun içinde ve dışında güvenlik sağlamak için bazı hususlar olabilir. Birçok müşteri için, tek bir Azure Active Directory hizmet sorumlusu yeterli olabilir ve Data Lake Storage 1. hesabının kökünde tam izinlere sahip olabilir. Diğer müşteriler, bir kümenin verilere tam erişimi olan ve yalnızca okuma erişimi olan başka bir kümenin olduğu farklı hizmet ilkelerine sahip birden çok küme gerektirebilir. Güvenlik gruplarında olduğu gibi, bir Data Lake Storage 1. hesabı oluşturulduktan sonra, tahmin edilen her senaryo (okuma, yazma, tam) için bir hizmet sorumlusu yapmayı düşünebilirsiniz.

### <a name="enable-the-data-lake-storage-gen1-firewall-with-azure-service-access"></a>Azure hizmet erişimi ile Data Lake Storage 1. güvenlik duvarını etkinleştirme

Data Lake Storage 1., bir güvenlik duvarını açma ve yalnızca Azure hizmetlerine erişimi sınırlandırma seçeneğini destekler, bu da dış yetkisiz erişimlere karşı daha küçük bir saldırı vektörü için önerilir. Güvenlik Duvarı,   >  Azure hizmetleri seçeneklerine erişime izin veren güvenlik duvarı 'nı **Etkinleştir (açık)** güvenlik duvarı aracılığıyla Azure Portal Data Lake Storage 1. hesabında etkinleştirilebilir  >   .

![Data Lake Storage 1. güvenlik duvarı ayarları](./media/data-lake-store-best-practices/data-lake-store-firewall-setting.png "Data Lake Storage 1. güvenlik duvarı ayarları")

Güvenlik duvarı etkinleştirildikten sonra yalnızca HDInsight, Data Factory, Azure SYNAPSE Analytics gibi Azure hizmetlerinin Data Lake Storage 1. erişimi vardır. Azure tarafından kullanılan iç ağ adresi çevirisi nedeniyle Data Lake Storage 1. güvenlik duvarı, belirli hizmetlerin IP ile kısıtlanması ve yalnızca Azure dışındaki uç noktalar için şirket içi gibi kısıtlamalar için tasarlanmıştır.

## <a name="performance-and-scale-considerations"></a>Performans ve ölçek konuları

Data Lake Storage 1. en güçlü özelliklerinden biri, veri işleme üzerindeki sabit sınırları kaldırmasının bir sıdır. Limitlerin kaldırılması, müşterilerin veri boyutunu ve eşlik eden performans gereksinimlerini, verilerin parçalara girmesi gerekmeden büyütmesine olanak sağlar. Data Lake Storage 1. performansını iyileştirmeye yönelik en önemli noktalara birisi, paralellik verildiğinde en iyi şekilde gerçekleştirdiğine yöneliktir.

### <a name="improve-throughput-with-parallelism"></a>Paralellik ile üretilen işi geliştirme

En iyi okuma/yazma işleme için çekirdek başına 8-12 iş parçacığı vermeyi düşünün. Bunun nedeni, tek bir iş parçacığında okuma/yazma işlemlerini engellemeyi ve daha fazla iş parçacığı VM üzerinde daha yüksek eşzamanlılık verebilir. Düzeylerin sağlıklı ve paralellik düzeyinin arttırılabiliyor olması için, VM 'nin CPU kullanımını izlemenin emin olun.

### <a name="avoid-small-file-sizes"></a>Küçük dosya boyutlarından kaçının

Data Lake Storage 1. POSIX izinleri ve denetimi, çok sayıda küçük dosya ile çalışırken görünür hale gelen bir yükle birlikte gelir. En iyi uygulama olarak, Data Lake Storage 1. binlerce veya milyonlarca küçük dosya yazmak için verilerinizi daha büyük dosyalara toplu olarak yazmanız gerekir. Küçük dosya boyutlarının önlenmesinin daha birçok avantajı olabilir, örneğin:

* Birden çok dosya genelinde kimlik doğrulama denetimlerini kısın
* Azaltılmış açık dosya bağlantıları
* Daha hızlı kopyalama/çoğaltma
* Data Lake Storage 1. POSIX izinleri güncelleştirilirken işlenecek daha az dosya

Hangi hizmet ve iş yüklerinin verileri kullandığını bağlı olarak, dosyalar için göz önünde bulundurmanız gereken boyut 256 MB veya daha fazladır. Data Lake Storage 1. giriş sırasında dosya boyutları toplu olarak oluşturulanamazlar, bu dosyaları daha büyük olanlara birleştiren ayrı bir sıkıştırma işine sahip olabilirsiniz. Dosya boyutları hakkında daha fazla bilgi ve Data Lake Storage 1. verileri düzenleme hakkında daha fazla bilgi için bkz. [veri kümesini yapı](data-lake-store-performance-tuning-guidance.md#structure-your-data-set).

### <a name="large-file-sizes-and-potential-performance-impact"></a>Büyük dosya boyutları ve olası performans etkisi

Data Lake Storage 1., en iyi performansı elde etmek ve verileri okurken işleme bağlı olarak, petabaytlarca 'e kadar olan büyük dosyaları desteklese de, ortalama 2 GB üzerine gitmek ideal olmayabilir. Örneğin, konumlar veya farklı depolama hesapları arasında veri kopyalamak için **Distcp** kullanılırken, dosyalar eşleme görevlerini belirlemede kullanılan en yoğun ayrıntı düzeyi düzeyidir. Bu nedenle, her biri 1 TB olan 10 dosyayı kopyalıyorsanız, en fazla 10 eşleme ayrılır. Ayrıca, mapgıl atanmış çok sayıda dosyalarınız varsa, ilk olarak Map, büyük dosyaları taşımak için paralel olarak çalışır. Ancak, iş yalnızca birkaç mapbir eşleme ayrılmaya başladığında, büyük bir dosyaya atanmış tek bir Eşleyici ile takılmış olabilirsiniz. Microsoft, bu sorunu ileride yapılacak Hadoop sürümlerinde ele almak için iyileştirmeler geliştirmeye yönelik geliştirmeler gönderdi.

Dikkate almanız gereken başka bir örnek Azure Data Lake Analytics Data Lake Storage 1. ile kullanmaktır. Extractor tarafından gerçekleştirilen işlemeye bağlı olarak, bölünememiş bazı dosyalar (örneğin, XML, JSON) 2 GB 'tan fazla performans üzerinde zarar verebilir. Dosyaların bir Extractor (örneğin, CSV) tarafından bölünebileceği durumlarda, büyük dosyalar tercih edilir.

### <a name="capacity-plan-for-your-workload"></a>İş yükünüz için kapasite planı

Azure Data Lake Storage 1. blob depolama hesaplarına yerleştirilmiş sabit GÇ azaltma sınırlarını kaldırır. Ancak yine de göz önünde bulundurulmaları gereken geçici sınırlar vardır. Varsayılan giriş/çıkış azaltma sınırları çoğu senaryonun ihtiyaçlarını karşılar. İş yükünüzün limitlerin artması gerekiyorsa, Microsoft desteğiyle çalışın. Ayrıca, prototip azaltma limitlerinin üretim sırasında isabet olmaması için kavram kanıtı aşamasında sınırlara göz atın. Bu durumda, Microsoft mühendislik ekibinin el ile artışını beklemek gerekebilir. GÇ azaltma gerçekleşirse, Azure Data Lake Storage 1.429 hata kodunu döndürür ve ideal bir üstel geri alma ilkesiyle yeniden denenmelidir.

### <a name="optimize-writes-with-the-data-lake-storage-gen1-driver-buffer"></a>Data Lake Storage 1. sürücü arabelleği ile "yazmaları" iyileştirin

Hadoop 'dan Data Lake Storage 1. yazarken performansı iyileştirmek ve ıOPS azaltmak için, yazma işlemlerini mümkün olduğunca Data Lake Storage 1. sürücü arabelleği boyutuna yakın şekilde gerçekleştirin. Apache Storm veya Spark akış iş yüklerini kullanarak akış yaparken olduğu gibi, reçeteye göre bellek boyutunu aşmamak için deneyin. HDInsight/Hadoop 'dan Data Lake Storage 1. yazarken, Data Lake Storage 1.4 MB 'lık bir arabelleğe sahip bir sürücüye sahip olduğundan emin olmanız önemlidir. Birçok dosya sistemi sürücüsü gibi, bu arabellek 4 MB boyutuna ulaşmadan el ile boşaltılabilir. Aksi takdirde, sonraki yazma, arabelleğin en büyük boyutunu aşarsa depolama alanına hemen temizlenir. Mümkün olduğunda, ilkeyi sayı veya zaman penceresine göre eşitlerken/boşaltılırken bir taşmanın veya önemli bir düzeyde çalıştırmanın olmaması gerekir.

## <a name="resiliency-considerations"></a>Dayanıklılık konuları

Data Lake Storage 1. veya herhangi bir bulut hizmeti ile bir sistemi mimarmaya çalışırken, kullanılabilirlik gereksinimlerinizi göz önünde bulundurmanız ve Hizmette oluşabilecek kesintilere yanıt verebilmeniz gerekir. Bir sorun belirli bir örneğe veya bölge genelinde yerelleştirilebilir, bu nedenle her ikisi için bir plana sahip olmak önemlidir. **Kurtarma zamanı hedefine** ve iş yükünüz için **Kurtarma noktası hedefi** SLA 'larına bağlı olarak yüksek kullanılabilirlik ve olağanüstü durum kurtarma için daha fazla veya daha az ısrarlı bir strateji seçebilirsiniz.

### <a name="high-availability-and-disaster-recovery"></a>Yüksek kullanılabilirlik ve olağanüstü durum kurtarma

Yüksek kullanılabilirlik (HA) ve olağanüstü durum kurtarma (DR) bazen birlikte birleştirilebilir, ancak özellikle de verilere geldiklerinde, her birinin biraz farklı bir stratejisi vardır. Data Lake Storage 1., yerelleştirilmiş donanım hatalarıyla karşı koruma kapsamında 3x çoğaltmasını zaten işliyor. Ancak, bölgeler arasında çoğaltma yerleşik olmadığından, bunu kendiniz yönetmeniz gerekir. Bir HA planı oluştururken, hizmet kesintisi durumunda iş yükünün, ayrı olarak çoğaltılan bir örneğe yerel olarak veya yeni bir bölgede geçiş yaparak en son verilere en kısa sürede erişmesi gerekir.

Bir DR stratejisinde, bir bölgenin çok önemli bir hatasının olası bir olayına hazırlanmak için verilerin farklı bir bölgeye çoğaltılması da önemlidir. Bu veriler başlangıçta çoğaltılan HA verileriyle aynı olabilir. Ancak, geri dönebileceğiniz düzenli anlık görüntüler oluşturmak isteyebileceğiniz veri bozulması gibi uç durumlar için gereksinimlerinizi de göz önünde bulundurmanız gerekir. Verilerin önem ve boyutuna bağlı olarak, risk toleranslarını temel alarak, 1-, 6-ve 24 saatlik dönemlerin Delta anlık görüntülerini yerel ve/veya ikincil depoda göz önünde bulundurun.

Data Lake Storage 1. ile veri dayanıklılığı için, verilerinizi, her saat ideal olarak, HA/DR gereksinimlerinizi karşılayan bir sıklık ile ayrı bir bölgeye çoğaltmanız önerilir. Bu çoğaltma sıklığı, ana sistemle ve daha iyi bir kurtarma noktası hedefi (RPO) ile rekabet eden bir işleme ihtiyacı olabilecek büyük veri taşımalarını en aza indirir. Ayrıca, Data Lake Storage 1. kullanan uygulamanın, izleme Tetikleyicileri veya başarısız denemelerin uzunluğu aracılığıyla ikincil hesaba yük devretmek veya el ile müdahale için yöneticilere en azından bir bildirim göndermek için yollar göz önünde bulundurmanız gerekir. Yük devretme zorunluluğunu getirir ve bir hizmetin tekrar çevrimiçi duruma gelmesini beklemek gerektiğini aklınızda bulundurun. Verilerin çoğaltılması tamamlanmamışsa bir yük devretme, verilerin olası veri kaybına, tutarsızlığa veya karmaşık birleştirmeye neden olabilir.

Aşağıda Data Lake Storage 1. hesapları arasında çoğaltmayı düzenlemek için önerilen üç seçenek ve bunların her biri arasındaki önemli farklılıklar aşağıda verilmiştir.

|  |Distcp  |Azure Data Factory  |AdlCopy  |
|---------|---------|---------|---------|
|**Ölçek sınırları**     | Çalışan düğümlerine göre sınırlanmış        | Maksimum bulut veri taşıma birimi ile sınırlıdır        | Analiz birimlerine göre bağlıydı        |
|**Değişimleri kopyalamayı destekler**     |   Yes      | Hayır         | Hayır         |
|**Yerleşik düzenleme**     |  Hayır (Oozie Airflow veya cron işleri kullanın)       | Yes        | Hayır (Azure Otomasyonu veya Windows Görev Zamanlayıcı kullanın)         |
|**Desteklenen dosya sistemleri**     | ADL,,, CSıB, S3, GS, CFS        |Çok sayıda, bkz. [Bağlayıcılar](../data-factory/connector-azure-blob-storage.md).         | Adl 'den adl 'ye, ADL 'ye (yalnızca aynı bölge)        |
|**İşletim sistemi desteği**     |Hadoop çalıştıran herhangi bir işletim sistemi         | Yok          | Windows 10         |

### <a name="use-distcp-for-data-movement-between-two-locations"></a>İki konum arasındaki veri taşıma için Distcp kullanma

Dağıtılmış kopya için kısa, Distcp, Hadoop ile birlikte gelen ve iki konum arasında dağıtılmış veri hareketi sağlayan bir Linux komut satırı aracıdır. İki konum Data Lake Storage 1., IBU, IDB veya S3 olabilir. Bu araç, tüm düğümlerde genişletmek için bir Hadoop kümesinde (örneğin, HDInsight) MapReduce işlerini kullanır. Distcp, büyük verileri özel ağ sıkıştırma araçları olmadan taşımanın en hızlı yolu olarak kabul edilir. Distcp Ayrıca, yalnızca iki konum arasında değişimleri güncelleştirme, otomatik yeniden denemeleri işleme ve işlem dinamik ölçeklendirmeyi oluşturma seçeneği sunar. Bu yaklaşım, tek bir dizinde çok büyük dosyaları olan ve yalnızca değiştirilen verilerin üzerine kopyalamak istediğiniz Hive/Spark tabloları gibi şeyleri çoğaltmak için inanılmaz etkilidir. Bu nedenlerden dolayı, büyük veri depoları arasında veri kopyalamak için en önerilen araç Distcp 'dir.

Kopyalama işleri, Linux cron işlerinin yanı sıra sıklık ve veri Tetikleyicileri kullanılarak Apache Oozie iş akışları tarafından tetiklenebilir. Yoğun çoğaltma işleri için, özellikle kopyalama işleri için ayarlanabilir ve ölçeklendirilebilen ayrı bir HDInsight Hadoop kümesi başlatılması önerilir. Bu, kopya işlerinin kritik işleri etkilememesini sağlar. Çoğaltma çok fazla sıklıkta çalışıyorsa, küme her bir iş arasında bile uygulanabilir. İkincil bölgeye yük devrettikten sonra, yeni verileri geri aldıktan sonra birincil Data Lake Storage 1. hesaba geri çoğaltmak için ikincil bölgede başka bir kümenin de aynı olduğundan emin olun. Distcp kullanma örnekleri için bkz. [Azure depolama Blobları ve Data Lake Storage 1. arasında veri kopyalamak Için Distcp kullanma](data-lake-store-copy-data-wasb-distcp.md).

### <a name="use-azure-data-factory-to-schedule-copy-jobs"></a>Kopyalama işlerini zamanlamak için Azure Data Factory kullanma

Azure Data Factory kopyalama işlerini bir **kopyalama etkinliği** kullanılarak zamanlamak için de kullanılabilir ve **Kopyalama Sihirbazı** aracılığıyla bir sıklık üzerinde de ayarlanabilir. Azure Data Factory bulut veri taşıma birimi (DMU) sınırının olduğunu ve sonunda büyük veri iş yükleri için üretilen iş/işlem miktarını büyük bir şekilde tutacağını unutmayın. Ayrıca, Azure Data Factory Şu anda Data Lake Storage 1. hesapları arasında Delta güncelleştirmeleri sunmaz, bu nedenle Hive tabloları gibi klasörler çoğaltmak için tam bir kopya gerektirir. Data Factory ile kopyalama hakkında daha fazla bilgi için [kopyalama etkinliği ayarlama kılavuzuna](../data-factory/copy-activity-performance.md) bakın.

### <a name="adlcopy"></a>AdlCopy

AdlCopy, verileri yalnızca aynı bölgedeki iki Data Lake Storage 1. hesabıyla kopyalamanızı sağlayan bir Windows komut satırı aracıdır. AdlCopy Aracı, kopyalama işinizi çalıştırmak için tek başına bir seçenek veya Azure Data Lake Analytics hesabı kullanma seçeneği sunar. Bu, güçlü bir çoğaltmanın aksine isteğe bağlı kopyalar için derlenmiş olsa da, aynı bölgedeki Data Lake Storage 1. hesaplara dağıtılmış kopyalama yapmak için başka bir seçenek sunar. Güvenilirlik için, tüm üretim iş yükleri için Premium Data Lake Analytics seçeneğinin kullanılması önerilir. Tek başına sürüm, meşgul yanıtları döndürebilir ve sınırlı ölçek ve izleme içerir.

Distcp gibi, AdlCopy 'ın Azure Otomasyonu veya Windows Görev Zamanlayıcı gibi bir şekilde incelenmesi gerekir. Data Factory olduğu gibi, AdlCopy yalnızca güncelleştirilmiş dosyaları kopyalamayı desteklemez, ancak var olan dosyaların yeniden kopyalanmasını ve üzerine yazılmasına neden olmaz. Daha fazla bilgi ve AdlCopy kullanma örnekleri için bkz. [Azure Storage Bloblarından veri kopyalama Data Lake Storage 1.](data-lake-store-copy-data-azure-storage-blob.md).

## <a name="monitoring-considerations"></a>İzleme konuları

Data Lake Storage 1. ayrıntılı tanılama günlükleri ve denetimi sağlar. Data Lake Storage 1., Azure portal Data Lake Storage 1. hesabı ve Azure Izleyici altında bazı temel ölçümler sağlar. Data Lake Storage 1. kullanılabilirliği Azure portal görüntülenir. Ancak, bu ölçüm yedi dakikada bir yenilenir ve genel kullanıma sunulan bir API aracılığıyla sorgulanamaz. Bir Data Lake Storage 1. hesabının en güncel kullanılabilirliğini almak için, kullanılabilirliği doğrulamak üzere kendi yapay testlerinizi çalıştırmanız gerekir. Toplam depolama alanı kullanımı, okuma/yazma istekleri ve giriş/çıkış gibi diğer ölçümler yenilenmesi 24 saate kadar sürebilir. Bu nedenle, Hadoop komut satırı araçları aracılığıyla daha güncel ölçümler el ile hesaplanmalıdır veya günlük bilgileri toplama yapılmalıdır. En son depolama kullanımını almanın en hızlı yolu, bu bir Hadoop kümesi düğümünden (örneğin, baş düğüm) Bu bir bu komutu çalıştırıyor:

```console
hdfs dfs -du -s -h adl://<adlsg1_account_name>.azuredatalakestore.net:443/
```

### <a name="export-data-lake-storage-gen1-diagnostics"></a>Data Lake Storage 1. tanılamayı dışarı aktar

Data Lake Storage 1. 'de aranabilir günlüklere erişim almanın en hızlı yolu, Data Lake Storage 1. hesabının **Tanılama** dikey penceresinde günlük aktarımına **Log Analytics** etkinleştirmektir. Bu, zaman ve içerik filtreleriyle gelen günlüklere anında erişim sağlar ve 15 dakikalık aralıklar içinde tetiklenen uyarı seçenekleri (e-posta/Web kancası) ile birlikte sağlanır. Yönergeler için bkz. [Azure Data Lake Storage 1. için tanılama günlüklerine erişme](data-lake-store-diagnostic-logs.md).

Daha fazla gerçek zamanlı uyarı ve günlüklerin nereye kaydedileceği hakkında daha fazla denetim için, bir kuyruğa gerçek zamanlı bildirimler göndermek amacıyla içeriğin tek tek veya bir zaman penceresinde çözümlenebileceği Azure EventHub ' ye günlükleri dışarı aktarmayı düşünün. [Mantıksal uygulama](../connectors/connectors-create-api-azure-event-hubs.md) gibi ayrı bir uygulama, uyarıları uygun kanalla tüketebilir ve iletişim kurabilir, ayrıca Newrelik, dataköpek veya AppDynamics gibi izleme araçlarına ölçümler gönderebilir. Alternatif olarak, Elam arama gibi bir üçüncü taraf araç kullanıyorsanız, günlükleri BLOB depolama alanına aktarabilir ve [Azure logstash eklentisini](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob) kullanarak verileri elaba araması, kibana ve logstash (elk) yığınınıza ekleyebilirsiniz.

### <a name="turn-on-debug-level-logging-in-hdinsight"></a>HDInsight 'ta hata ayıklama düzeyinde günlüğe yazmayı aç

Data Lake Storage 1. günlük aktarma açık değilse, Azure HDInsight, Log4J aracılığıyla [Data Lake Storage 1. için istemci tarafı günlüğe yazmayı](data-lake-store-performance-tuning-mapreduce.md) etkinleştirmek için bir yol sağlar. Aşağıdaki özelliği, **ambarı**  >  **Yarn**  >  **yapılandırması**  >  **Gelişmiş Yarn-Log4J yapılandırmalarını** ayarlamanız gerekir:

`log4j.logger.com.microsoft.azure.datalake.store=DEBUG`

Özellik ayarlandıktan ve düğümler yeniden başlatıldıktan sonra Data Lake Storage 1. tanılama, düğümlerdeki YARN günlüklerine yazılır (/TMP/ \<user\> /yarn.log) ve hatalar veya daraltma (HTTP 429 hata kodu) gibi önemli ayrıntılar izlenebilir. Aynı bilgiler aynı zamanda Azure Izleyici günlüklerinde veya Data Lake Storage 1. hesabının [Tanılama](data-lake-store-diagnostic-logs.md) dikey penceresinde her bir günlüklerin gönderildiği yerde de izlenebilir. En azından istemci tarafı oturum açma özelliğinin açık olması veya işlemsel görünürlük ve daha kolay hata ayıklama için Data Lake Storage 1. ile günlük aktarımı seçeneğinin kullanılması önerilir.

### <a name="run-synthetic-transactions"></a>Yapay işlemleri Çalıştır

Şu anda, Azure portal Data Lake Storage 1. için hizmet kullanılabilirliği ölçüsünün 7 dakikalık yenileme penceresi vardır. Ayrıca, genel kullanıma sunulan bir API kullanılarak sorgulanamaz. Bu nedenle, dakika kullanılabilirliği sağlayabilecek Data Lake Storage 1. yapay işlemler yapan temel bir uygulama oluşturmanız önerilir. Örneğin, Data Lake Storage 1. bir okuma, oluşturma ve güncelleştirme gerçekleştirmek ve sonuçları izleme çözümünüze göndermek için bir WebJob, Logic App ya da Azure İşlev Uygulaması oluşturabilir. İşlemler geçici bir klasörde yapılabilir ve sonra, gereksinimlere bağlı olarak, her 30-60 saniyede bir çalışan test sonrasında silinebilir.

## <a name="directory-layout-considerations"></a>Dizin düzeni konuları

Verileri bir veri Gölü içine kaydederken, güvenlik, bölümleme ve işlemenin etkili bir şekilde kullanılabilmesi için verilerin yapısını önceden planlamanız önemlidir. Aşağıdaki önerilerin birçoğu Azure Data Lake Storage 1., BLOB depolama veya "ile aynı olsun, kullanılabilir. Her iş yükünün, verilerin tüketilme konusunda farklı gereksinimleri vardır, ancak aşağıda IoT ve Batch senaryolarıyla çalışırken göz önünde bulundurmanız gereken bazı ortak düzenler verilmiştir.

### <a name="iot-structure"></a>IoT yapısı

IoT iş yüklerinde, çok sayıda ürüne, cihaza, kuruluşa ve müşterilere yayılan veri deposunda yer alan harika bir veri olabilir. Sağ Akış tüketicilerine yönelik verilerin kuruluş, güvenlik ve verimli işlenmesi için Dizin düzeninin önceden planlanmak önemlidir. Göz önünde bulundurmanız gereken genel bir şablon aşağıdaki düzen olabilir:

```console
{Region}/{SubjectMatter(s)}/{yyyy}/{mm}/{dd}/{hh}/
```

Örneğin, UK içindeki bir uçak altyapısının giriş telemetrisi aşağıdaki yapıya benzeyebilir:

```console
UK/Planes/BA1293/Engine1/2017/08/11/12/
```

Tarihi klasör yapısının sonuna yerleştirmek için önemli bir neden vardır. Belirli bölgeleri veya ilgili konuyu kullanıcılar/gruplar için kilitlemek istiyorsanız, bunu POSIX izinleriyle kolayca yapabilirsiniz. Aksi takdirde, belirli bir güvenlik grubunu yalnızca UK verileri veya belirli düzlemleri görüntülemek üzere kısıtlamak için, her saat klasörü altındaki çok sayıda klasör için ayrı bir izin olması gereken tarih yapısına sahip olması gerekir. Ayrıca, tarih yapısının önde olması, klasör sayısını zaman içinde geçen zaman üstel olarak artırır.

### <a name="batch-jobs-structure"></a>Toplu iş işleri yapısı

Bir üst düzeyden, toplu işlemede yaygın olarak kullanılan bir yaklaşım, verileri bir "ın" klasöründe arakullanmaktır. Ardından, veriler işlendikten sonra, yeni verileri, aşağı akış işlemlerinin kullanacağı bir "Out" klasörüne koyun. Bu dizin yapısı bazen tek tek dosyalarda işleme gerektiren işler için görülür ve büyük veri kümeleri üzerinde çok fazla paralel işleme gerektirmeyebilir. Yukarıda önerilen IoT yapısı gibi, iyi bir dizin yapısı, bölge ve konu açısından önemli (örneğin, kuruluş, ürün/üretici) gibi şeyler için üst düzey klasörlere sahiptir. Bu yapı, kuruluşunuz genelinde verilerin güvenliğini sağlamaya ve iş yüklerinizde verilerin daha iyi yönetilmesini sağlar. Ayrıca, işleme göre daha iyi kuruluş, filtrelenmiş aramalar, güvenlik ve Otomasyon sağlamak için yapıdaki tarih ve saati göz önünde bulundurun. Tarih yapısına yönelik ayrıntı düzeyi, verilerin karşıya yüklendiği veya işlendiği aralığa göre belirlenir; Örneğin, saatlik, günlük, hatta aylık.

Bazen veri bozulması veya beklenmeyen biçimler nedeniyle dosya işleme başarısız olur. Bu gibi durumlarda, dizin yapısı bir **/Bad** klasöründen daha fazla inceleme için dosyaları taşımak için faydalanabilir. Toplu iş, el ile müdahale için bu *Hatalı* dosyaların raporlamasını veya bildirimini de işleyebilir. Aşağıdaki şablon yapısını göz önünde bulundurun:

```console
{Region}/{SubjectMatter(s)}/In/{yyyy}/{mm}/{dd}/{hh}/
{Region}/{SubjectMatter(s)}/Out/{yyyy}/{mm}/{dd}/{hh}/
{Region}/{SubjectMatter(s)}/Bad/{yyyy}/{mm}/{dd}/{hh}/
```

Örneğin, bir pazarlama firması, müşteri güncelleştirmelerinin Kuzey Amerika içindeki istemcilerinden günlük veri ayıklar. İşlenmeden önce ve sonra aşağıdaki kod parçacığına benzeyebilir:

```console
NA/Extracts/ACMEPaperCo/In/2017/08/14/updates_08142017.csv
NA/Extracts/ACMEPaperCo/Out/2017/08/14/processed_updates_08142017.csv
```

Toplu iş verilerinin doğrudan Hive veya geleneksel SQL veritabanları gibi veritabanlarına işlendiği durumlarda, çıkış zaten Hive tablosu veya dış veritabanı için ayrı bir klasöre geçtiğinde bir **/ın** veya **/Out** klasörüne gerek yoktur. Örneğin, müşterilerden günlük ayıklamalar ilgili klasörlerine giderek Azure Data Factory, Apache Oozie veya Apache Airflow gibi bir şey tarafından düzenleme, günlük bir Hive veya Spark işinin verileri bir Hive tablosuna işlemesi ve yazması için tetikleyecektir.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Data Lake Storage 1. genel bakış](data-lake-store-overview.md)
* [Azure Data Lake Storage 1. Access Control](data-lake-store-access-control.md)
* [Azure Data Lake Storage 1. güvenlik](data-lake-store-security-overview.md)
* [Performans için Azure Data Lake Storage 1. ayarlama](data-lake-store-performance-tuning-guidance.md)
* [Azure Data Lake Storage 1. ile HDInsight Spark kullanımı için performans ayarlama Kılavuzu](data-lake-store-performance-tuning-spark.md)
* [HDInsight Hive Azure Data Lake Storage 1. ile kullanılması için performans ayarlama Kılavuzu](data-lake-store-performance-tuning-hive.md)
* [Data Lake Storage 1. ile HDInsight kümeleri oluşturma](data-lake-store-hdinsight-hadoop-use-portal.md)
