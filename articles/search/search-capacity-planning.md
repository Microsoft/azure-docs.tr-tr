---
title: Sorgu ve Dizin iş yükleri için ölçek kapasitesi
titleSuffix: Azure Cognitive Search
description: Azure Bilişsel Arama 'de, her kaynağın faturalandırılabilir arama birimlerinde fiyatlandırıldıkları bölüm ve çoğaltma bilgisayar kaynaklarını ayarlayın.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 4020a40b87c32bdbd07e390a0d04769cb3d47f7d
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74112135"
---
# <a name="scale-up-partitions-and-replicas-to-add-capacity-for-query-and-index-workloads-in-azure-cognitive-search"></a>Azure Bilişsel Arama sorgu ve Dizin iş yükleri için kapasite eklemek üzere bölümleri ve çoğaltmaları ölçeklendirin

[Bir fiyatlandırma katmanı seçip](search-sku-tier.md) [bir arama hizmeti](search-create-service-portal.md)sağladıktan sonra, bir sonraki adım isteğe bağlı olarak hizmetiniz tarafından kullanılan çoğaltma veya bölüm sayısını artırmadır. Her katman, sabit sayıda faturalandırma birimi sunar. Bu makalede, sorgu yürütme, dizin oluşturma ve depolama için gereksinimlerinizi dengeleyen en iyi yapılandırmayı elde etmek üzere bu birimlerin nasıl tahsis edilebileceği açıklanmaktadır.

Kaynak Yapılandırması, [Temel katmanda](https://aka.ms/azuresearchbasic) bir hizmeti veya [Standart ya da depolama için iyileştirilmiş katmanlardan](search-limits-quotas-capacity.md)birini ayarlarken kullanılabilir. Bu katmanlardaki hizmetler için, kapasite her bir bölüm ve çoğaltmanın bir SU olarak sayılabileceği *arama birimlerinin* (SUs) artışlarla satın alınır. 

Daha az sayıda SUs sonucu, orantılı bir şekilde daha düşüktür. Hizmet ayarlandığı sürece faturalandırma yürürlüğe girer. Geçici olarak bir hizmet kullanmıyorsanız, faturalandırma yapmanın tek yolu hizmeti silip daha sonra gerektiğinde yeniden oluşturmaktır.

> [!Note]
> Bir hizmetin silinmesi, üzerindeki her şeyi siler. Kalıcı arama verilerini yedeklemek ve geri yüklemek için Azure Bilişsel Arama 'da hiçbir tesis yoktur. Mevcut bir dizini yeni bir hizmette yeniden dağıtmak için, ilk olarak oluşturmak ve yüklemek üzere kullanılan programı çalıştırmanız gerekir. 

## <a name="terminology-replicas-and-partitions"></a>Terminoloji: çoğaltmalar ve bölümler
Çoğaltmalar ve bölümler, bir arama hizmetini geri yükleyen birincil kaynaklardır.

| Kaynak | Tanım |
|----------|------------|
|*Bölümler* | Okuma/yazma işlemleri için dizin depolama ve g/ç sağlar (örneğin, bir dizini yeniden oluşturma veya yenileme).|
|*Çoğaltmaları* | Arama hizmeti örnekleri, öncelikli olarak sorgu işlemlerinin yükünü dengelemek için kullanılır. Her çoğaltma her zaman bir dizinin kopyasını barındırır. 12 çoğaltmadıysanız, hizmete yüklenen her dizinin 12 kopyasına sahip olursunuz.|

> [!NOTE]
> Bir çoğaltmada hangi dizinlerin çalıştırılacağını doğrudan değiştirmek veya yönetmek için bir yol yoktur. Her yinelemede her bir dizinin bir kopyası, hizmet mimarisinin bir parçasıdır.
>


## <a name="how-to-allocate-replicas-and-partitions"></a>Çoğaltmaları ve bölümleri ayırma
Azure Bilişsel Arama, bir hizmete başlangıçta bir bölümden ve bir çoğaltmadan oluşan en az düzeyde kaynak ayrılır. Bunu destekleyen katmanlar için, daha fazla depolama ve g/ç gerekiyorsa bölümleri artırarak hesaplama kaynaklarını artımlı olarak ayarlayabilir veya daha büyük sorgu birimleri veya daha iyi performans için daha fazla çoğaltma ekleyebilirsiniz. Tek bir hizmetin tüm iş yüklerini (Dizin oluşturma ve sorgular) işlemek için yeterli kaynak olması gerekir. Birden çok hizmet arasında iş yüklerini bölülemez.

Çoğaltmaları ve bölümleri ayırmayı artırmak veya değiştirmek için Azure portal kullanmanızı öneririz. Portal, en fazla limitlerin altında kalacak izin verilen birleşimler üzerinde sınırlar uygular. Betik tabanlı veya kod tabanlı bir sağlama yaklaşımına ihtiyacınız varsa [Azure PowerShell](search-manage-powershell.md) veya [Yönetim REST API](https://docs.microsoft.com/rest/api/searchmanagement/services) alternatif çözümlerdir.

Genellikle, özellikle de hizmet işlemleri sorgu iş yükleri altına sunulduğunda arama uygulamalarının bölümden daha fazla çoğaltma yapması gerekir. [Yüksek kullanılabilirlik](#HA) bölümünde bunun neden olduğu açıklanmaktadır.

1. [Azure Portal](https://portal.azure.com/) oturum açın ve arama hizmetini seçin.

2. **Ayarlar**' da, çoğaltmaları ve bölümleri değiştirmek için **Ölçek** sayfasını açın. 

   Aşağıdaki ekran görüntüsünde, bir çoğaltma ve bölüm ile sağlanan standart bir hizmet gösterilmektedir. Alttaki formül, kaç arama birimi kullanıldığını gösterir (1). Birim fiyatı $100 (gerçek fiyat değil) ise, bu hizmeti çalıştırmanın aylık maliyeti, ortalama olarak $100 ' dir.

   ![Geçerli değerleri gösteren sayfayı Ölçeklendir](media/search-capacity-planning/1-initial-values.png "Geçerli değerleri gösteren sayfayı Ölçeklendir")

3. Bölüm sayısını artırmak veya azaltmak için kaydırıcıyı kullanın. Alttaki formül, kaç tane arama birimi kullanıldığını gösterir.

   Bu örnek, iki çoğaltmalarla ve her bir bölümden oluşan kapasiteyi iki katına çıkarır. Arama Birimi sayısına dikkat edin; Fatura formülü bölümler (2 x 2) ile çarpıldığı için artık dördü vardır. Katlama kapasitesi, hizmeti çalıştırmanın maliyetini iki katına çıkarır. Arama birimi maliyeti $100 ise, yeni aylık fatura artık $400 olacaktır.

   Her katmanın birim maliyetlerine göre geçerli olan [fiyatlandırma sayfasını](https://azure.microsoft.com/pricing/details/search/)ziyaret edin.

   ![Çoğaltmalar ve bölümler ekleme](media/search-capacity-planning/2-add-2-each.png "Çoğaltmalar ve bölümler ekleme")

3. Değişiklikleri onaylamak için **Kaydet** ' e tıklayın.

   ![Ölçek ve faturalandırma Değişikliklerini Onayla](media/search-capacity-planning/3-save-confirm.png "Ölçek ve faturalandırma Değişikliklerini Onayla")

   Kapasite değişikliklerinin tamamlanması birkaç saat sürer. İşlem başladıktan sonra iptal edemezsiniz ve çoğaltma ve bölüm ayarlamaları için gerçek zamanlı izleme yoktur. Ancak, değişiklikler devam ederken aşağıdaki ileti görünür kalır.

   ![Portalda durum iletisi](media/search-capacity-planning/4-updating.png "Portalda durum iletisi")


> [!NOTE]
> Bir hizmet sağlandıktan sonra, daha yüksek bir SKU 'ya yükseltilemez. Yeni katmanda bir arama hizmeti oluşturmanız ve dizinlerinizi yeniden yüklemeniz gerekir. Hizmet sağlama konusunda yardım için bkz. [portalda Azure bilişsel arama hizmeti oluşturma](search-create-service-portal.md) .
>
>

<a id="chart"></a>

## <a name="partition-and-replica-combinations"></a>Bölüm ve çoğaltma birleşimleri

Temel bir hizmet, en fazla üç SUs sınırı için tam olarak bir bölüme ve en fazla üç çoğaltmaya sahip olabilir. Yalnızca ayarlanabilir kaynak çoğaltmadır. Sorgularda yüksek kullanılabilirlik için en az iki çoğaltma gerekir.

Tüm standart ve depolama için Iyileştirilmiş arama hizmetleri, 36-SU sınırına tabi olan aşağıdaki çoğaltma ve bölüm birleşimlerini kabul edebilir. 

|   | **1 Bölüm** | **2 Bölüm** | **3 Bölüm** | **4 Bölüm** | **6 Bölüm** | **12 Bölüm** |
| --- | --- | --- | --- | --- | --- | --- |
| **1 çoğaltma** |1 SU |2 SU |3 SU |4 SU |6 SU |12 SU |
| **2 çoğaltma** |2 SU |4 SU |6 SU |8 SU |12 SU |24 SU |
| **3 çoğaltma** |3 SU |6 SU |9 SU |12 SU |18 SU |36 SU |
| **4 çoğaltma** |4 SU |8 SU |12 SU |16 SU |24 SU |Yok |
| **5 çoğaltma** |5 SU |10 SU |15 SU |20 SU |30 SU |Yok |
| **6 çoğaltma** |6 SU |12 SU |18 SU |24 SU |36 SU |Yok |
| **12 çoğaltma** |12 SU |24 SU |36 SU |Yok |Yok |Yok |

SUs, fiyatlandırma ve kapasite Azure Web sitesinde ayrıntılı olarak açıklanmıştır. Daha fazla bilgi için bkz. [fiyatlandırma ayrıntıları](https://azure.microsoft.com/pricing/details/search/).

> [!NOTE]
> Çoğaltma ve bölüm sayısı eşit olarak 12 ' ye bölünür (özellikle, 1, 2, 3, 4, 6, 12). Bunun nedeni, Azure Bilişsel Arama her bir dizini tüm bölümler arasında eşit kısımlara yayılabilecek şekilde 12 parçalara böler. Örneğin, hizmetinizin üç bölümü varsa ve bir dizin oluşturursanız, her bölüm dizin dört bölümden oluşur. Azure Bilişsel Arama Dizin oluşturma işlemi, sonraki sürümlerde değişikliğe tabi olan bir uygulama ayrıntısı. Sayı bugün 12 olsa da, bu sayının gelecekte her zaman 12 olması beklenmemelidir.
>


<a id="HA"></a>

## <a name="high-availability"></a>Yüksek kullanılabilirlik
Ölçeği hızla ve nispeten daha hızlı olduğundan, genellikle bir bölüm ve bir veya iki çoğaltma ile başlayıp daha sonra, sorgu birimleri oluşturma olarak ölçeklendirmeniz önerilir. Sorgu iş yükleri birincil olarak çoğaltmalar üzerinde çalışır. Daha fazla üretilen iş veya yüksek kullanılabilirliğe ihtiyacınız varsa, muhtemelen ek çoğaltmalar yapmanız gerekir.

Yüksek kullanılabilirlik için genel öneriler şunlardır:

* Salt okuma iş yüklerinin yüksek kullanılabilirliği için iki çoğaltma (sorgular)

* Okuma/yazma iş yüklerinin yüksek kullanılabilirliği için üç veya daha fazla çoğaltma (tek belgeler için sorgular ve dizin oluşturma eklenir, güncelleştirilir veya silinir)

Azure Bilişsel Arama için hizmet düzeyi sözleşmeleri (SLA), sorgu işlemlerine ve belge ekleme, güncelleştirme veya silme işlemlerinden oluşan Dizin güncelleştirmelerine yöneliktir.

Temel katman, bir bölümde ve üç çoğaltmadan çıkar. Hem dizin oluşturma hem de sorgu işleme için talepteki dalgalanmaların hemen yanıt vermesini istiyorsanız Standart katmanlardan birini göz önünde bulundurun.  Depolama gereksinimlerinizin sorgu aktarım süresinden çok daha hızlı bir şekilde büyümesini fark ederseniz, depolama için Iyileştirilmiş katmanlardan birini düşünün.

### <a name="index-availability-during-a-rebuild"></a>Yeniden derleme sırasında dizin kullanılabilirliği

Azure Bilişsel Arama için yüksek kullanılabilirlik, bir dizini yeniden oluşturmayı içermeyen sorgularla ve Dizin güncelleştirmeleriyle ilgilidir. Bir alanı siler, bir veri türünü değiştirirseniz veya bir alanı yeniden adlandırırsanız, dizini yeniden oluşturmanız gerekecektir. Dizini yeniden oluşturmak için dizini silmeniz, dizini yeniden oluşturmanız ve verileri yeniden yüklemeniz gerekir.

> [!NOTE]
> Dizini yeniden oluşturmadan Azure Bilişsel Arama dizinine yeni alanlar ekleyebilirsiniz. Yeni alanın değeri, dizinde bulunan tüm belgeler için null olacaktır.

Yeniden oluşturma sırasında dizin kullanılabilirliğini sürdürmek için, aynı hizmette farklı bir ada sahip bir dizinin kopyasına veya farklı bir hizmette aynı ada sahip bir dizinin kopyasına sahip olmanız ve ardından kodunuzda yeniden yönlendirme veya yük devretme mantığı sağlamanız gerekir.

## <a name="disaster-recovery"></a>Olağanüstü durum kurtarma
Şu anda olağanüstü durum kurtarma için yerleşik bir mekanizma yoktur. Bölüm veya çoğaltmaları eklemek, olağanüstü durum kurtarma hedeflerini karşılamak için yanlış stratejidir. En yaygın yaklaşım, başka bir bölgede ikinci bir arama hizmeti ayarlayarak hizmet düzeyinde artıklık eklemektir. Dizin yeniden oluşturma sırasında kullanılabilirliğinde olduğu gibi, yeniden yönlendirme veya yük devretme mantığı koddan gelmelidir.

## <a name="increase-query-performance-with-replicas"></a>Çoğaltmalarla sorgu performansını artırma
Sorgu gecikmesi, ek çoğaltmaların gerekli olduğu bir göstergedir. Genellikle, sorgu performansını iyileştirmeye yönelik ilk adım bu kaynağı daha fazla eklemektir. Çoğaltmaları eklerken, daha büyük sorgu iş yüklerini desteklemek ve isteklerin çoklu çoğaltmalar üzerinden yük dengelenmesi için dizinin ek kopyaları çevrimiçi hale getirilir.

Saniyedeki sorgu sayısı (QPS) için sabit tahminler sağlayamıyoruz: sorgu performansı, sorgunun karmaşıklığına ve rekabet iş yüklerine bağlıdır. Çoğaltmaları ekleme işlemi daha iyi performansa neden olsa da, sonuç kesinlikle doğrusal değildir: üç çoğaltma eklemek Üçlü üretilen iş garantisi vermez.

İş yükleriniz için QPS tahmini konusunda rehberlik için bkz. [Azure bilişsel arama performans ve iyileştirme konuları](search-performance-optimization.md).

## <a name="increase-indexing-performance-with-partitions"></a>Bölümler ile dizin oluşturma performansını artırma
Neredeyse gerçek zamanlı veri yenileme gerektiren uygulamalarda, çoğaltmalara göre orantılı daha fazla bölüm gerekecektir. Bölüm ekleme okuma/yazma işlemlerini çok sayıda bilgi işlem kaynağına yayar. Ayrıca ek dizinleri ve belgeleri depolamak için daha fazla disk alanı sağlar.

Daha büyük dizinler sorgu için daha uzun sürer. Bu nedenle, bölümlerdeki her artımlı artışın çoğaltmalarda daha küçük ancak orantılı bir artış gerektirdiğini fark edebilirsiniz. Sorgularınızın ve sorgu biriminizin karmaşıklığı, sorgu yürütmenin ne kadar hızlı bir şekilde bir şekilde çalıştığını çarpanlara katacaktır.


## <a name="next-steps"></a>Sonraki adımlar

[Azure Bilişsel Arama için bir fiyatlandırma katmanı seçin](search-sku-tier.md)
