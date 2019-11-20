---
title: Azure Data Lake Storage 2. kullanmaya yönelik en iyi uygulamalar | Microsoft Docs
description: Azure Data Lake Storage 2. kullanımı ile ilgili veri alımı, tarih güvenliği ve performans hakkında en iyi uygulamaları öğrenin (daha önce Azure Data Lake Store olarak bilinir)
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: normesta
ms.reviewer: sachins
ms.openlocfilehash: ac4e126c7ecbd1fc781db74e5b19635b273bbb34
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/13/2019
ms.locfileid: "72299664"
---
# <a name="best-practices-for-using-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage 2. kullanmak için en iyi uygulamalar

Bu makalede, Azure Data Lake Storage 2. ile çalışmaya yönelik en iyi yöntemler ve konular hakkında bilgi edineceksiniz. Bu makalede, Data Lake Storage 2. için güvenlik, performans, dayanıklılık ve izleme hakkında bilgi verilmektedir. Data Lake Storage 2. önce, Azure HDInsight gibi hizmetlerde gerçekten büyük verilerle çalışma karmaşıktır. Birden çok BLOB depolama hesabında verileri parçalara çıkaran ve bu ölçekte en iyi performansı elde etmek zorunda kaldık. Data Lake Storage 2., her bir dosya boyutunu en fazla 5 TB kadar destekler ve performans için sabit limitlerin çoğu kaldırılmıştır. Ancak, Data Lake Storage 2. ile en iyi performansı elde edebilmeniz için bu makalenin kapsamakta olduğu bazı noktalar vardır.

## <a name="security-considerations"></a>Güvenlikle ilgili dikkat edilmesi gerekenler

Azure Data Lake Storage 2., Azure Active Directory (Azure AD) kullanıcıları, grupları ve hizmet sorumluları için POSIX erişim denetimleri sunmaktadır. Bu erişim denetimleri var olan dosyalara ve dizinlere ayarlanabilir. Erişim denetimleri, yeni dosyalara veya dizinlere otomatik olarak uygulanabilecek varsayılan izinleri oluşturmak için de kullanılabilir. Data Lake Storage 2. ACL 'Ler hakkında daha fazla ayrıntı [Azure Data Lake Storage 2. Içindeki erişim denetiminde](storage-data-lake-storage-access-control.md)bulunmaktadır.

### <a name="use-security-groups-versus-individual-users"></a>Bireysel kullanıcılara karşı güvenlik gruplarını kullanın

Data Lake Storage 2. büyük verilerle çalışırken, Azure HDInsight gibi hizmetlerin verilerle çalışmasına olanak tanımak için bir hizmet sorumlusu kullanılması olasıdır. Ancak, bireysel kullanıcıların da verilere erişmesi gereken durumlar olabilir. Her durumda, tek tek kullanıcıları dizin ve dosyalara atamak yerine Azure Active Directory [güvenlik grupları](../common/storage-auth-aad.md) kullanmayı kesin olarak düşünün.

Bir güvenlik grubuna izinler atandıktan sonra, gruptan kullanıcı ekleme veya kaldırma işlemi Data Lake Storage 2. güncelleştirme gerektirmez. Bu Ayrıca, erişim denetim listesi (ACL) başına en fazla erişim denetimi girişi sayısını aşmamak için de yardımcı olur. Şu anda bu sayı 32, (her zaman dosya ve dizinle ilişkili olan dört adet POSIX stili ACL 'Ler dahil): sahip olan Kullanıcı, sahip olan Grup, maske ve diğer. Toplam 64 erişim denetimi girişi için her dizin iki tür ACL, erişim ACL 'SI ve varsayılan ACL olabilir. Bu ACL 'Ler hakkında daha fazla bilgi için bkz. [Azure Data Lake Storage 2. Access Control](data-lake-storage-access-control.md).

### <a name="security-for-groups"></a>Gruplar için güvenlik

Siz veya kullanıcılarınızın hiyerarşik ad alanı etkinleştirilmiş bir depolama hesabındaki verilere erişmesi gerektiğinde, Azure Active Directory güvenlik grupları kullanmak en iyisidir. İle başlamak için önerilen bazı gruplar, kapsayıcının köküne yönelik olarak **Readonlyusers**, **Writeaccessusers**ve **fullaccessusers** olabilir ve hatta anahtar alt dizinlerindekiler için ayrı ayrı olabilir. Daha sonra eklenebilen ancak henüz tanımlanmayan başka bir Kullanıcı grubu varsa, belirli klasörlere erişimi olan kukla güvenlik grupları oluşturmayı düşünebilirsiniz. Güvenlik grubunun kullanılması, binlerce dosyaya yeni izinler atarken uzun işlem süresini önlemenize olanak sağlar.

### <a name="security-for-service-principals"></a>Hizmet sorumluları için güvenlik

Azure Active Directory hizmet sorumluları, genellikle Data Lake Storage 2. verilere erişmek için Azure Databricks gibi hizmetler tarafından kullanılır. Birçok müşteri için, tek bir Azure Active Directory hizmet sorumlusu yeterli olabilir ve Data Lake Storage 2. kapsayıcısının kökünde tam izinlere sahip olabilir. Diğer müşteriler, bir kümenin verilere tam erişimi olan ve yalnızca okuma erişimi olan başka bir kümenin olduğu farklı hizmet ilkelerine sahip birden çok küme gerektirebilir. 

### <a name="enable-the-data-lake-storage-gen2-firewall-with-azure-service-access"></a>Azure hizmet erişimi ile Data Lake Storage 2. güvenlik duvarını etkinleştirme

Data Lake Storage 2., bir güvenlik duvarını açma ve yalnızca Azure hizmetlerine erişimi sınırlandırma seçeneğini destekler, bu da dış saldırıların vektörünü sınırlamak için önerilir. Güvenlik Duvarı, güvenlik duvarı üzerinden Azure portal bir depolama hesabında **etkinleştirilebilir @no__t-** 1**güvenlik DUVARıNı etkinleştir (açık)**  > **Azure hizmetleri seçeneklerine erişime izin verir** .

Depolama hesabınıza Azure Databricks erişmek için, Azure Databricks sanal ağınıza dağıtın ve ardından bu sanal ağı güvenlik duvarınızdan ekleyin. Bkz. [Azure Storage güvenlik duvarlarını ve sanal ağları yapılandırma](https://docs.microsoft.com/azure/storage/common/storage-network-security).

## <a name="resiliency-considerations"></a>Dayanıklılık konuları

Data Lake Storage 2. veya herhangi bir bulut hizmeti ile bir sistemi mimarmaya çalışırken, kullanılabilirlik gereksinimlerinizi göz önünde bulundurmanız ve Hizmette oluşabilecek kesintilere yanıt verebilmeniz gerekir. Bir sorun belirli bir örneğe veya bölge genelinde yerelleştirilebilir, bu nedenle her ikisi için bir plana sahip olmak önemlidir. Kurtarma zamanı hedefine ve iş yükünüz için kurtarma noktası hedefi SLA 'larına bağlı olarak yüksek kullanılabilirlik ve olağanüstü durum kurtarma için daha fazla veya daha az ısrarlı bir strateji seçebilirsiniz.

### <a name="high-availability-and-disaster-recovery"></a>Yüksek erişilebilirlik ve felaketten kurtarma

Yüksek kullanılabilirlik (HA) ve olağanüstü durum kurtarma (DR) bazen birlikte birleştirilebilir, ancak özellikle de verilere geldiklerinde, her birinin biraz farklı bir stratejisi vardır. Data Lake Storage 2., yerelleştirilmiş donanım hatalarıyla karşı koruma kapsamında 3x çoğaltmasını zaten işliyor. Ayrıca, ZRS veya GZRS (Önizleme) gibi diğer çoğaltma seçenekleri, GRS & RA-GRS, DR 'yi iyileştirirken HA 'yi geliştirir. Bir HA planı oluştururken, hizmet kesintisi durumunda iş yükünün, ayrı olarak çoğaltılan bir örneğe yerel olarak veya yeni bir bölgede geçiş yaparak en son verilere en kısa sürede erişmesi gerekir.

Bir DR stratejisinde, bir bölgenin çok önemli bir hatasının olası bir olayına hazırlanmak için, GRS veya RA-GRS çoğaltmasını kullanarak verilerin farklı bir bölgeye çoğaltılması da önemlidir. Ayrıca, geri dönebileceğiniz düzenli anlık görüntüler oluşturmak isteyebileceğiniz veri bozulması gibi kenar durumları için gereksinimlerinizi de göz önünde bulundurmanız gerekir. Verilerin önem ve boyutuna bağlı olarak, 1-, 6 ve 24 saatlik dönemlerin Delta anlık görüntülerini, risk toleransları uyarınca göz önünde bulundurun.

Data Lake Storage 2. ile veri dayanıklılığı için, HA/DR gereksinimlerinizi karşılayan GRS veya RA-GRS aracılığıyla verilerinizi coğrafi çoğaltmanız önerilir. Ayrıca, Data Lake Storage 2. kullanan uygulamanın, izleme Tetikleyicileri veya başarısız denemelerin uzunluğu aracılığıyla ikincil bölgeye otomatik olarak yük devretmek veya el ile müdahale için yöneticilere en azından bir bildirim göndermek için yollar göz önünde bulundurmanız gerekir. Yük devretme zorunluluğunu getirir ve bir hizmetin tekrar çevrimiçi duruma gelmesini beklemek gerektiğini aklınızda bulundurun.

### <a name="use-distcp-for-data-movement-between-two-locations"></a>İki konum arasındaki veri taşıma için Distcp kullanma

Dağıtılmış kopya için kısa, DistCp, Hadoop ile birlikte gelen ve iki konum arasında dağıtılmış veri hareketi sağlayan bir Linux komut satırı aracıdır. İki konum Data Lake Storage 2., IBU veya S3 olabilir. Bu araç, tüm düğümlerde genişletmek için bir Hadoop kümesinde (örneğin, HDInsight) MapReduce işlerini kullanır. Distcp, büyük verileri özel ağ sıkıştırma araçları olmadan taşımanın en hızlı yolu olarak kabul edilir. Distcp Ayrıca, yalnızca iki konum arasında değişimleri güncelleştirme, otomatik yeniden denemeleri işleme ve işlem dinamik ölçeklendirmeyi oluşturma seçeneği sunar. Bu yaklaşım, tek bir dizinde çok büyük dosyaları olan ve yalnızca değiştirilen verilerin üzerine kopyalamak istediğiniz Hive/Spark tabloları gibi şeyleri çoğaltmak için inanılmaz etkilidir. Bu nedenlerden dolayı, büyük veri depoları arasında veri kopyalamak için en önerilen araç Distcp 'dir.

Kopyalama işleri, Linux cron işlerinin yanı sıra sıklık ve veri Tetikleyicileri kullanılarak Apache Oozie iş akışları tarafından tetiklenebilir. Yoğun çoğaltma işleri için, özellikle kopyalama işleri için ayarlanabilir ve ölçeklendirilebilen ayrı bir HDInsight Hadoop kümesi başlatılması önerilir. Bu, kopya işlerinin kritik işleri etkilememesini sağlar. Çoğaltma çok fazla sıklıkta çalışıyorsa, küme her bir iş arasında bile uygulanabilir. İkincil bölgeye yük devrettikten sonra, yeni verileri geri aldıktan sonra birincil Data Lake Storage 2. hesaba geri çoğaltmak için ikincil bölgede başka bir kümenin de aynı olduğundan emin olun. Distcp kullanma örnekleri için bkz. [Azure depolama Blobları ve Data Lake Storage 2. arasında veri kopyalamak Için Distcp kullanma](../blobs/data-lake-storage-use-distcp.md).

### <a name="use-azure-data-factory-to-schedule-copy-jobs"></a>Kopyalama işlerini zamanlamak için Azure Data Factory kullanma

Azure Data Factory kopyalama işlerini bir kopyalama etkinliği kullanılarak zamanlamak için de kullanılabilir ve kopyalama Sihirbazı aracılığıyla bir sıklık üzerinde de ayarlanabilir. Azure Data Factory bulut veri taşıma birimi (DMU) sınırının olduğunu ve sonunda büyük veri iş yükleri için üretilen iş/işlem miktarını büyük bir şekilde tutacağını unutmayın. Ayrıca, Azure Data Factory Şu anda Data Lake Storage 2. hesapları arasında Delta güncelleştirmeleri sunmaz, bu nedenle Hive tabloları gibi dizinler çoğaltmak için tam bir kopya gerektirir. Data Factory ile kopyalama hakkında daha fazla bilgi için [Data Factory makalesine](../../data-factory/load-azure-data-lake-storage-gen2.md) başvurun.

## <a name="monitoring-considerations"></a>İzleme konuları

Data Lake Storage 2., Azure portal Data Lake Storage 2. hesabının ve Azure Izleyici 'nin altındaki ölçümleri sağlar. Data Lake Storage 2. kullanılabilirliği Azure portal görüntülenir. Bir Data Lake Storage 2. hesabının en güncel kullanılabilirliğini almak için, kullanılabilirliği doğrulamak üzere kendi yapay testlerinizi çalıştırmanız gerekir. Toplam depolama kullanımı, okuma/yazma istekleri ve giriş/çıkış gibi diğer ölçümler, uygulamaları izleyerek yararlanılabilir olarak kullanılabilir ve eşikler (örneğin, ortalama gecikme süresi veya dakika başına hata sayısı) aşıldığında uyarıları tetikleyebilirler.

## <a name="directory-layout-considerations"></a>Dizin düzeni konuları

Verileri bir veri Gölü içine kaydederken, güvenlik, bölümleme ve işlemenin etkili bir şekilde kullanılabilmesi için verilerin yapısını önceden planlamanız önemlidir. Tüm büyük veri iş yükleri için aşağıdaki önerilerin birçoğu geçerlidir. Her iş yükünün, verilerin tüketilme konusunda farklı gereksinimleri vardır, ancak aşağıda IoT ve Batch senaryolarıyla çalışırken göz önünde bulundurmanız gereken bazı ortak düzenler verilmiştir.

### <a name="iot-structure"></a>IoT yapısı

IoT iş yüklerinde, çok sayıda ürüne, cihaza, kuruluşa ve müşterilere yayılan veri deposunda yer alan harika bir veri olabilir. Sağ Akış tüketicilerine yönelik verilerin kuruluş, güvenlik ve verimli işlenmesi için Dizin düzeninin önceden planlanmak önemlidir. Göz önünde bulundurmanız gereken genel bir şablon aşağıdaki düzen olabilir:

    {Region}/{SubjectMatter(s)}/{yyyy}/{mm}/{dd}/{hh}/

Örneğin, UK içindeki bir uçak altyapısının giriş telemetrisi aşağıdaki yapıya benzeyebilir:

    UK/Planes/BA1293/Engine1/2017/08/11/12/

Tarihi dizin yapısının sonuna yerleştirmek için önemli bir neden vardır. Belirli bölgeleri veya ilgili konuyu kullanıcılar/gruplar için kilitlemek istiyorsanız, bunu POSIX izinleriyle kolayca yapabilirsiniz. Aksi takdirde, belirli bir güvenlik grubunu yalnızca UK verileri veya belirli düzlemleri görüntülemek üzere kısıtlamak için, her saat dizininde bulunan çok sayıda dizin için, önde gelen Tarih yapısıyla ayrı bir izin olması gerekir. Ayrıca, tarih yapısının önde olması, dizin sayısını zaman içinde geçen zamanda üstel olarak artırır.

### <a name="batch-jobs-structure"></a>Toplu iş işleri yapısı

Bir üst düzeyden, toplu işlemede yaygın olarak kullanılan bir yaklaşım, verileri bir "ın" dizinine ulaşmalıdır. Ardından, veriler işlendikten sonra, yeni verileri, aşağı akış işlemlerinin kullanacağı bir "Out" dizinine yerleştirin. Bu dizin yapısı bazen tek tek dosyalarda işleme gerektiren işler için görülür ve büyük veri kümeleri üzerinde çok fazla paralel işleme gerektirmeyebilir. Yukarıda önerilen IoT yapısı gibi, iyi bir dizin yapısı, bölge ve konu açısından önemli (örneğin, kuruluş, ürün/üretici) gibi şeyler için üst düzey dizinlere sahiptir. Bu yapı, kuruluşunuz genelinde verilerin güvenliğini sağlamaya ve iş yüklerinizde verilerin daha iyi yönetilmesini sağlar. Ayrıca, işleme göre daha iyi kuruluş, filtrelenmiş aramalar, güvenlik ve Otomasyon sağlamak için yapıdaki tarih ve saati göz önünde bulundurun. Tarih yapısına yönelik ayrıntı düzeyi, verilerin karşıya yüklendiği veya işlendiği aralığa göre belirlenir; Örneğin, saatlik, günlük, hatta aylık.

Bazen veri bozulması veya beklenmeyen biçimler nedeniyle dosya işleme başarısız olur. Bu gibi durumlarda, dizin yapısı bir **/Bad** klasöründen daha fazla inceleme için dosyaları taşımak için faydalanabilir. Toplu iş, el ile müdahale için bu *Hatalı* dosyaların raporlamasını veya bildirimini de işleyebilir. Aşağıdaki şablon yapısını göz önünde bulundurun:

    {Region}/{SubjectMatter(s)}/In/{yyyy}/{mm}/{dd}/{hh}/
    {Region}/{SubjectMatter(s)}/Out/{yyyy}/{mm}/{dd}/{hh}/
    {Region}/{SubjectMatter(s)}/Bad/{yyyy}/{mm}/{dd}/{hh}/

Örneğin, bir pazarlama firması, müşteri güncelleştirmelerinin Kuzey Amerika içindeki istemcilerinden günlük veri ayıklar. İşlenmeden önce ve sonra aşağıdaki kod parçacığına benzeyebilir:

    NA/Extracts/ACMEPaperCo/In/2017/08/14/updates_08142017.csv
    NA/Extracts/ACMEPaperCo/Out/2017/08/14/processed_updates_08142017.csv

Toplu iş verilerinin doğrudan Hive veya geleneksel SQL veritabanları gibi veritabanlarına işlendiği durumlarda, çıkış zaten Hive tablosu veya dış için ayrı bir klasöre gitdiğinden bir **/ın** veya **/Out** klasörü gerekmez veritabanınızı. Örneğin, müşterilerden günlük ayıklamalar ilgili klasörlerine giderek Azure Data Factory, Apache Oozie veya Apache Airflow gibi bir şey tarafından düzenleme, günlük bir Hive veya Spark işinin verileri bir Hive tablosuna işlemesi ve yazması için tetikleyecektir.
