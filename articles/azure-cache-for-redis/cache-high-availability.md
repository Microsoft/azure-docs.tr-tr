---
title: Redsıs için Azure önbelleği için yüksek kullanılabilirlik
description: Redsıs yüksek kullanılabilirlik özellikleri ve seçenekleri için Azure önbelleği hakkında bilgi edinin
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 02/08/2021
ms.author: yegu
ms.openlocfilehash: 6c44c87221442797f063877385ac5eb7f8585850
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107719106"
---
# <a name="high-availability-for-azure-cache-for-redis"></a>Redsıs için Azure önbelleği için yüksek kullanılabilirlik

Redin için Azure önbelleğinde yerleşik yüksek kullanılabilirlik vardır. Yüksek kullanılabilirlik mimarisinin hedefi, temel alınan sanal makineler (VM 'Ler) planlı veya plansız kesintilerden etkilendiğinde bile, yönetilen Redis örneğinizin çalışır durumda olduğundan emin olmaktır. Tek bir VM 'de redin barındırılmasına göre daha fazla yüzde oranı sunar.

Redin için Azure Cache, bir önbellek için *düğüm* olarak adlandırılan birden çok VM kullanarak yüksek kullanılabilirlik uygular. Bu düğümleri, veri çoğaltma ve yük devretme ile eşgüdümlü bir şekilde gerçekleşecek şekilde yapılandırır. Redsıs yazılım düzeltme eki uygulama gibi bakım işlemlerini de düzenler. Standart, Premium ve Enterprise katmanlarında çeşitli yüksek kullanılabilirlik seçenekleri mevcuttur:

| Seçenek | Açıklama | Kullanılabilirlik | Standart | Premium | Kurumsal |
| ------------------- | ------- | ------- | :------: | :---: | :---: |
| [Standart çoğaltma](#standard-replication)| Otomatik yük devretme ile tek bir veri merkezinde çift düğümlü çoğaltılan yapılandırma | % 99,9 ( [ayrıntılara](https://azure.microsoft.com/support/legal/sla/cache/v1_0/)bakın) |✔|✔|-|
| [Bölge yedekliliği](#zone-redundancy) | Otomatik yük devretme ile AZs genelinde çok düğümlü çoğaltılan yapılandırma | % 99,99 ' e kadar ( [ayrıntılara](https://azure.microsoft.com/support/legal/sla/cache/v1_0/)bakın) |-|Önizleme|✔|
| [Coğrafi çoğaltma](#geo-replication) | Kullanıcı denetimli yük devretmeyle, iki bölgedeki bağlantılı önbellek örnekleri | % 99,999 ' e kadar ( [ayrıntılara](https://azure.microsoft.com/support/legal/sla/cache/v1_0/)bakın) |-|✔|Önizleme|

## <a name="standard-replication"></a>Standart çoğaltma

Standart veya Premium katmanda redin için bir Azure önbelleği, varsayılan olarak bir dizi Redsıs sunucusu üzerinde çalışır. İki sunucu adanmış VM 'lerde barındırılır. Açık kaynaklı redo, veri yazma isteklerini yalnızca bir sunucunun işlemesini sağlar. Bu sunucu, diğer *çoğaltma* sırasında *birincil* düğümdür. Sunucu düğümlerini sağlamasını gerçekleştirdikten sonra Redsıs için Azure önbelleği, bunlara birincil ve çoğaltma rolleri atar. Birincil düğüm, genellikle yazma hizmetinin yanı sıra Redsıs istemcilerinden gelen okuma isteklerini de sorumludur. Bir yazma işleminde, iç belleğine yeni bir anahtar ve bir anahtar güncelleştirmesi kaydeder ve istemciye anında yanıt verir. İşlemi zaman uyumsuz olarak çoğaltmaya iletir.

:::image type="content" source="media/cache-high-availability/replication.png" alt-text="Veri çoğaltma Kurulumu":::
   
>[!NOTE]
>Normalde, redin istemcisi tüm okuma ve yazma istekleri için bir Redsıs önbelleğinde birincil düğümle iletişim kurar. Belirli Reddir istemcileri, çoğaltma düğümünden okunacak şekilde yapılandırılabilir.
>
>

Redsıs önbelleğinde birincil düğüm kullanılamıyorsa, çoğaltma kendisini otomatik olarak yeni birincil olacak şekilde yükseltir. Bu işleme *Yük devretme* adı verilir. Çoğaltma, birincil düğümün hızlı bir şekilde kurtarmasının önüne alınmadan önce yeterince uzun süre bekleyecektir. Yük devretme Işlemi gerçekleştiğinde, redin için Azure önbelleği yeni bir VM sağlar ve bunu çoğaltma düğümü olarak önbelleğe birleştirir. Çoğaltma, birincil ile tam veri eşitlemesi gerçekleştirerek önbellek verilerinin başka bir kopyasına sahip olur.

Bir birincil düğüm, Redsıs yazılımı veya işletim sistemi güncelleştirmesi gibi planlı bir bakım etkinliğinin bir parçası olarak hizmetten geçebilir. Ayrıca, temeldeki donanımda, yazılımda veya ağda oluşan hatalarda oluşan planlanmamış olaylar nedeniyle çalışmayı durdurabilir. [Redin Için Azure önbelleği Için yük devretme ve düzeltme eki uygulama](cache-failover.md) , Redsıs yük devretmeleri türleri hakkında ayrıntılı bir açıklama sağlar. Redo için bir Azure önbelleği, ömrü boyunca birçok yük devretmenize devam edecektir. Yüksek kullanılabilirlik mimarisi, bu değişiklikleri bir önbellekte mümkün olduğunca saydam hale getirmek için tasarlanmıştır.

>[!NOTE]
>Aşağıdaki önizleme olarak kullanılabilir.
>
>

Ayrıca, redde için Azure önbelleği Premium katmanda ek çoğaltma düğümlerine izin verir. [Çoklu çoğaltma önbelleği](cache-how-to-multi-replicas.md) , en çok üç çoğaltma düğümü ile yapılandırılabilir. Daha fazla çoğaltmanın olması, birincil olarak yedeklenen ek düğümler nedeniyle genellikle dayanıklılığı geliştirir. Daha fazla çoğaltmalarla birlikte, Redsıs örneği için Azure önbelleği yine de veri merkezi veya AZ düzeyli bir kesinti tarafından ciddi bir şekilde etkilenebilir. Birden çok kopyayı, [bölge artıklarıyla](#zone-redundancy)birlikte kullanarak önbellek kullanılabilirliğini artırabilirsiniz.

## <a name="zone-redundancy"></a>Bölge yedekliliği

Redsıs için Azure önbelleği, Premium ve Enterprise katmanlarında bölgesel olarak yedekli yapılandırmaların kullanılmasını destekler. Bölgesel olarak [yedekli önbellek](cache-how-to-zone-redundancy.md) , düğümlerini aynı bölgedeki farklı [Azure kullanılabilirlik alanları](../availability-zones/az-overview.md) arasında yerleştirebilir. Tek bir hata noktası olarak veri merkezini veya AZ kesintiyi ortadan kaldırır ve önbelleğinizin genel kullanılabilirliğini artırır.

### <a name="premium-tier"></a>Premium katmanı

>[!NOTE]
>Bu, önizleme olarak kullanılabilir.
>
>

Aşağıdaki diyagramda Premium katmanının bölgesel olarak yedekli yapılandırması gösterilmektedir:

:::image type="content" source="media/cache-high-availability/zone-redundancy.png" alt-text="Bölge artıklığı kurulumu":::
   
Reds için Azure Cache, bir bölgenin yedekli önbelleğindeki düğümleri seçtiğiniz Azlara göre hepsini bir kez kez dağıtır. Başlangıçta birincil olarak hangi düğümün çalışacağını de belirler.

Bölge yedekli önbelleği otomatik yük devretme sağlar. Geçerli birincil düğüm kullanılamadığında, çoğaltmalardan biri üzerinden alınır. Yeni birincil düğüm farklı bir AZ ise uygulamanız daha yüksek önbellek yanıt süresi ile karşılaşabilir. Azlar coğrafi olarak ayrılır. Bir AZ diğerine geçiş yapmak, uygulamanızın ve önbelleğinin barındırıldığı konum arasındaki fiziksel mesafeyi değiştirir. Bu değişiklik, uygulamadan önbelleğe gidiş dönüş ağ gecikmelerinin etkilerini etkiler. Ek gecikme süresinin çoğu uygulama için kabul edilebilir bir Aralık içinde olması beklenir. Uygulamanızı, bölgesel olarak yedekli bir önbellekte iyi gerçekleştirebileceği şekilde test etmenizi öneririz.

### <a name="enterprise-tiers"></a>Kurumsal katmanlar

Bir açık kurumsal katmandaki bir önbellek Redsıs kurumsal kümesi üzerinde çalışır. Her zaman çekirdek oluşturmak için tek sayıda sunucu düğümü gerektirir. Varsayılan olarak, her biri ayrılmış bir VM 'de barındırılan üç düğümden oluşur. Kurumsal önbellekte iki adet aynı boyutlu *veri düğümü* ve bir küçük *çekirdek düğümü* vardır. Enterprise Flash önbelleğinde üç boyutlu veri düğümü vardır. Kurumsal küme Redsıs verilerini dahili olarak bölümlere böler. Her bölümde *birincil* ve en az bir *çoğaltma* bulunur. Her veri düğümü bir veya daha fazla bölüm barındırır. Kurumsal küme, herhangi bir bölümün birincil ve çoğaltmasının aynı veri düğümüne hiçbir şekilde birlikte olmamasını sağlar. Bölümler, verileri zaman uyumsuz iken ilgili çoğaltmalara göre çoğaltır.

Bir veri düğümü kullanılamaz hale geldiğinde veya ağ bölünmesi gerçekleştiğinde, [Standart çoğaltmada](#standard-replication) açıklanacak olan bir yük devretme gerçekleşecektir. Kurumsal küme, hangi kalan düğümlerin yeni bir çekirdeğe katılacağı tespit etmek için çekirdek tabanlı bir model kullanır. Ayrıca, gerektiğinde bu düğümlerde bulunan çoğaltma bölümlerini de yükseltir.

## <a name="geo-replication"></a>Coğrafi çoğaltma

[Coğrafi çoğaltma](cache-how-to-geo-replication.md) , genellikle iki Azure bölgesini kapsayan redsıs örnekleri için iki veya daha fazla Azure önbelleğini bağlamaya yönelik bir mekanizmadır. 

### <a name="premium-tier"></a>Premium katmanı

>[!NOTE]
>Premium katmanda coğrafi çoğaltma, genellikle olağanüstü durum kurtarma için tasarlanmıştır.
>
>

İki Premium katman önbelleği örneği, [coğrafi çoğaltma](cache-how-to-geo-replication.md) yoluyla, önbellek verilerinizi farklı bir bölgeye yedekleyebilmeniz için bağlanabilir. Birlikte bağlandıktan sonra, bir örnek birincil bağlantılı önbellek ve diğeri ikincil bağlantılı önbellek olarak atanır. Yalnızca birincil önbellek okuma ve yazma isteklerini kabul eder. Birincil önbelleğe yazılan veriler ikincil önbelleğe çoğaltılır. Bir uygulama, birincil ve ikincil önbellekler için ayrı uç noktalar aracılığıyla önbelleğe erişir. Uygulamanın, birden fazla Azure bölgesinde dağıtıldığında tüm yazma isteklerini birincil önbelleğe gönderebilmesi gerekir. Birincil veya ikincil önbellekten okuyabilir. Genel olarak, gecikme süresini azaltmak için uygulamanızın işlem örneklerinin en yakın önbelleklerden okunmasını istersiniz. İki önbellek örneği arasındaki veri aktarımı TLS ile korunmaktadır.

Coğrafi çoğaltma, uygulamanızın geri kalanı birincil bölgede kalırsa bölgeler arasında eklenen ağ gidiş dönüş süresi nedeniyle otomatik yük devretme sağlamaz. İkincil önbelleğin bağlantısını kaldırarak yük devretmeyi yönetmeniz ve başlatmanız gerekir. Bu, bunu yeni birincil örnek olarak yükseltir.

### <a name="enterprise-tiers"></a>Kurumsal katmanlar

>[!NOTE]
>Bu, önizleme olarak kullanılabilir.
>
>

Kurumsal katmanlar, coğrafi çoğaltmanın [etkin coğrafi çoğaltma](cache-how-to-active-geo-replication.md)olarak adlandırılan daha gelişmiş bir biçimini destekler. Redsıs kurumsal yazılımı, çakışma içermeyen çoğaltılan veri türlerini kullanarak, birden fazla önbellek örneğine yazma işlemini destekler ve gerekirse değişiklikleri birleştirme ve çakışmaları çözme işlemleri gerçekleştirir. Farklı Azure bölgelerindeki iki veya daha fazla kurumsal katman önbelleği örneği, etkin bir coğrafi olarak çoğaltılan önbellek oluşturacak şekilde eklenebilir. Bu tür bir önbelleği kullanan bir uygulama, coğrafi olarak dağıtılmış önbellek örneklerini ilgili uç noktalar aracılığıyla okuyabilir ve yazabilir. En düşük gecikme süresini sağlayan her bir işlem örneğine en yakın olanı kullanması gerekir. Uygulamanın Ayrıca, önbellek örneklerini izlemesi ve örneklerden biri kullanılamaz hale gelirse başka bir bölgeye geçmesi gerekir. Etkin coğrafi çoğaltmanın nasıl çalıştığı hakkında daha fazla bilgi için bkz. [Active-active Geo-Distriubtion (CRDTs tabanlı)](https://redislabs.com/redis-enterprise/technology/active-active-geo-distribution/).

## <a name="next-steps"></a>Sonraki adımlar

Redsıs yüksek kullanılabilirlik seçenekleri için Azure önbelleğini yapılandırma hakkında daha fazla bilgi edinin.

* [Redsıs Premium hizmet katmanları için Azure önbelleği](cache-overview.md#service-tiers)
* [Redsıs için Azure önbelleğine çoğaltmalar ekleme](cache-how-to-multi-replicas.md)
* [Redsıs için Azure önbelleği için bölge yedekliliği etkinleştirin](cache-how-to-zone-redundancy.md)
* [Redsıs için Azure önbelleği için Coğrafi çoğaltmayı ayarlama](cache-how-to-geo-replication.md)
