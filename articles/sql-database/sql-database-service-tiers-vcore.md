---
title: Sanal çekirdek modeline genel bakış
description: Sanal çekirdek satın alma modeli, işlem ve depolama kaynaklarını bağımsız olarak ölçeklendirmenize, şirket içi performansı eşleşmenize ve fiyatı iyileştirmenize olanak tanır.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 11/04/2019
ms.openlocfilehash: b9de02bf0836727ac88b78194641238621e87a79
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73821052"
---
# <a name="vcore-model-overview"></a>Sanal çekirdek modeline genel bakış

Sanal çekirdek (vCore) modeli çeşitli avantajlar sağlar:

- Daha yüksek işlem, bellek, GÇ ve depolama sınırları.
- İş yükünün işlem ve bellek gereksinimlerini daha iyi eşleştirmek için donanım oluşturma üzerinde denetim.
- [Azure hibrit avantajı (AHB)](sql-database-azure-hybrid-benefit.md) ve [ayrılmış örnek (RI)](sql-database-reserved-capacity.md)için fiyatlandırma iskontoları.
- İşlem gücüne yönelik donanım ayrıntılarında daha büyük saydamlık; Şirket içi dağıtımlardan geçiş planlamayı kolaylaştırır.

## <a name="service-tiers"></a>Hizmet katmanları

Sanal çekirdek modelindeki hizmet katmanı seçenekleri Genel Amaçlı, İş Açısından Kritik ve Hyperscale içerir. Hizmet katmanı genellikle depolama mimarisini, boşluk ve GÇ sınırlarını ve kullanılabilirlik ve olağanüstü durum kurtarma ile ilgili iş sürekliliği seçeneklerini tanımlar.

||**Genel amaçlı**|**İş açısından kritik**|**Hiper ölçekli**|
|---|---|---|---|
|En iyi kullanım alanı:|Birçok iş yükü. Bütçeye dayalı, dengeli ve ölçeklenebilir işlem ve depolama seçenekleri sunar. |, Birkaç yalıtılmış çoğaltma kullanarak ve en yüksek g/ç performansı sunan iş uygulamalarına en yüksek esnekliği sağlar.|Yüksek düzeyde ölçeklenebilir depolama ve okuma ölçeği gereksinimlerine sahip iş yüklerinin çoğu.  , Birden fazla yalıtılmış veritabanı çoğaltmasının yapılandırılmasına izin vererek daha yüksek esnekliği hatalara olanak sağlar. |
|Depolama|Uzak depolamayı kullanır.<br/>**Tek veritabanı ve elastik havuz sağlanan işlem**:<br/>5 GB – 4 TB<br/>**Sunucusuz işlem**:<br/>5 GB-3 TB<br/>**Yönetilen örnek**: 32 GB-8 TB |Yerel SSD depolama kullanır.<br/>**Tek veritabanı ve elastik havuz sağlanan işlem**:<br/>5 GB – 8 TB<br/>**Yönetilen örnek**:<br/>32 GB-4 TB |Gerektiğinde depolamanın esnek otomatik büyümesi. 100 TB 'a kadar depolamayı destekler. Yerel ara havuz önbelleği ve yerel veri depolaması için yerel SSD depolama kullanır. Son uzun süreli veri deposu olarak Azure uzak depolama kullanır. |
|G/ç verimlilik (yaklaşık)|**Tek veritabanı ve elastik havuz**: 500 IOPS, vCore başına en fazla 40000 IOPS.<br/>**Yönetilen örnek**: [dosyanın boyutuna](../virtual-machines/windows/premium-storage-performance.md#premium-storage-disk-sizes)bağlıdır.|en fazla 320.000 IOPS 'ye kadar vCore başına 5000 ıOPS|Hiper ölçek, birden çok düzeyde önbelleğe alma özelliği olan çok katmanlı bir mimaridir. Etkin IOPS iş yüküne bağlı olacaktır.|
|Kullanılabilirlik|1 çoğaltma, okuma ölçeğinde çoğaltmalar yok|3 çoğaltma, 1 [okuma ölçeği çoğaltma](sql-database-read-scale-out.md),<br/>bölge yedekli yüksek kullanılabilirlik (HA)|1 okuma-yazma çoğaltması, artı 0-4 [okuma ölçekli çoğaltmalar](sql-database-read-scale-out.md)|
|Yedeklemeler|[Okuma Erişimli Coğrafi olarak yedekli depolama (RA-GRS)](../storage/common/storage-designing-ha-apps-with-ragrs.md), 7-35 gün (varsayılan olarak 7 gün)|[RA-GRS](../storage/common/storage-designing-ha-apps-with-ragrs.md), 7-35 gün (varsayılan olarak 7 gün)|Azure uzak depolama 'da anlık görüntü tabanlı yedeklemeler. Geri yükleme bu anlık görüntüleri hızlı kurtarma için kullanır. Yedeklemeler anında gerçekleşir ve işlem g/ç performansını etkilemez. Geri yükleme işlemleri hızlıdır ve veri boyutu (saatler veya günler yerine dakikalar içinde).|
|Bellek içi|Desteklenmiyor|Destekleniyor|Desteklenmiyor|
|||


### <a name="choosing-a-service-tier"></a>Hizmet katmanı seçme

Belirli bir iş yükünüz için bir hizmet katmanı seçme hakkında bilgi için aşağıdaki makalelere bakın:

- [Genel amaçlı hizmet katmanını seçme](sql-database-service-tier-general-purpose.md#when-to-choose-this-service-tier)
- [İş Açısından Kritik hizmet katmanını seçme](sql-database-service-tier-business-critical.md#when-to-choose-this-service-tier)
- [Hiper ölçek hizmet katmanını seçme](sql-database-service-tier-hyperscale.md#who-should-consider-the-hyperscale-service-tier)


## <a name="compute-tiers"></a>İşlem katmanları

Sanal çekirdek modelindeki işlem katmanı seçenekleri sağlanan ve sunucusuz işlem katmanlarını içerir.


### <a name="provisioned-compute"></a>Sağlanan işlem

Sağlanan işlem katmanı, iş yükü etkinliğinden bağımsız olarak sürekli olarak sağlanan belirli bir işlem kaynakları ve bir saatlik sabit fiyata sağlanan işlem miktarına göre faturalandırılır.


### <a name="serverless-compute"></a>Sunucusuz işlem

[Sunucusuz işlem katmanı](sql-database-serverless.md) , işlem kaynaklarını iş yükü etkinliğine göre otomatik olarak ölçeklendirir ve saniye başına kullanılan işlem miktarına göre faturalandırılır.



## <a name="hardware-generations"></a>Donanım nesilleri

VCore modelindeki donanım oluşturma seçenekleri arasında gen 4/5, M serisi (Önizleme) ve Fsv2-Series (Önizleme) bulunur. Donanım oluşturma genellikle işlem ve bellek sınırlarını ve iş yükünün performansını etkileyen diğer özellikleri tanımlar.

### <a name="gen4gen5"></a>4\. nesil/5. nesil

- 4\. nesil/5. nesil donanımı, dengeli işlem ve bellek kaynakları sağlar ve Fsv2 serisi veya d serisi tarafından sağlanan daha yüksek bellek, daha yüksek sanal çekirdek veya daha hızlı tek bir sanal çekirdek gereksinimlerine sahip olmayan veritabanı iş yükleri için uygundur.

4\. nesil/5. nesil kullanılabildiği bölgelerde, bkz. [4. nesil/5. nesil kullanılabilirliği](#gen4gen5-1).

### <a name="fsv2-seriespreview"></a>Fsv2 serisi (Önizleme)

- Fsv2-Series, en düşük CPU gecikme süresi ve yüksek hızda yoğun iş yükleri sağlayan, işlem için iyileştirilmiş bir donanım seçeneğidir.
- Fsv2 serisi, iş yüküne bağlı olarak, 5. nesil 'den vCore başına daha fazla CPU performansı sunabilir ve 72 vCore boyutu 5. nesil üzerindeki 80 sanal çekirdekten daha az maliyet sağlamak için daha fazla CPU performansı sağlayabilir. 
- Fsv2, Diğer donanımlardan sanal çekirdek başına daha az bellek ve tempdb sağlar, bu sınırlara duyarlı iş yükleri bunun yerine 5. nesil veya d serisini düşünmek isteyebilir.  

Fsv2-Series 'in kullanılabildiği bölgeler için bkz. [Fsv2 serisi kullanılabilirliği](#fsv2-series).


### <a name="m-seriespreview"></a>A serisi (Önizleme)

- D serisi, 5. nesil tarafından sağlanenden daha fazla bellek ve daha fazla işlem sınırı gerektiren iş yükleri için bellek için iyileştirilmiş bir donanım seçeneğidir.
- A serisi, vCore başına 29 GB ve 128 sanal çekirdek sağlar. bu da, 5. nesil ile 8X arasındaki bellek sınırını neredeyse 4 TB 'a yükseltir.

Bir abonelik ve bölge için, e serisi donanım etkinleştirmek üzere bir destek isteği açık olmalıdır. Destek talebi onaylanırsa, e serisi seçme ve sağlama deneyimi diğer donanım oluşumları için aynı düzeni izler. D serisi kullanılabilir olan bölgelerde, bkz. [d serisi kullanılabilirlik](#m-series).


### <a name="compute-and-memory-specifications"></a>İşlem ve bellek belirtimleri


|Donanım oluşturma  |İşlem  |Bellek  |
|:---------|:---------|:---------|
|4\. nesil     |-Intel E5-2673 v3 (Haswell) 2,4 GHz işlemcileri<br>-En fazla 24 sanal çekirdek sağlama (1 sanal çekirdek = 1 fiziksel çekirdek)  |-Sanal çekirdek başına 7 GB<br>-168 GB 'a kadar sağlama|
|Gen5     |**Sağlanan işlem**<br>-Intel E5-2673 v4 (çok Iyi) 2,3 GHz işlemcileri<br>-En fazla 80 sanal çekirdek sağlama (1 sanal çekirdek = 1 hiper iş parçacığı)<br><br>**Sunucusuz işlem**<br>-Intel E5-2673 v4 (çok Iyi) 2,3 GHz işlemcileri<br>-16 sanal çekirdeğe kadar otomatik ölçeklendirme (1 sanal çekirdek = 1 hiper iş parçacığı)|**Sağlanan işlem**<br>-vCore başına 5,1 GB<br>-408 GB 'a kadar sağlama<br><br>**Sunucusuz işlem**<br>-VCore başına 24 GB 'a kadar otomatik ölçeklendirme<br>-En fazla 48 GB 'a kadar otomatik ölçeklendirme|
|Fsv2-serisi     |-Intel Xeon Platinum 8168 (ufuk Gölü) işlemcileri<br>-Sürekli olarak 3,4 GHz 'nin tüm Core Turbo saat hızına ve en fazla 3,7 GHz bir adet tek çekirdekli Turbo saat hızına sahiptir.<br>-Sağlama 72 sanal çekirdekler (1 sanal çekirdek = 1 hiper iş parçacığı)|-vCore başına 1,9 GB<br>-Sağlama 136 GB|
|M serisi     |-Intel Xeon E7-8890 v3 2,5 GHz işlemcileri<br>-Sağlama 128 sanal çekirdekler (1 sanal çekirdek = 1 hiper iş parçacığı)|-vCore başına 29 GB<br>-Sağlama 3,7 TB|


Kaynak limitleri hakkında daha fazla bilgi için bkz. [tek veritabanları (sanal çekirdek) Için kaynak limitleri](sql-database-vcore-resource-limits-single-databases.md)veya [elastik havuzlar (Vcore) için kaynak sınırları](sql-database-vcore-resource-limits-elastic-pools.md).

### <a name="selecting-a-hardware-generation"></a>Donanım oluşturma seçme

Azure portal, bir SQL veritabanı veya havuzu için oluşturma sırasında donanım oluşturmayı seçebilir veya var olan bir SQL veritabanının veya havuzunun donanım oluşturma işlevini değiştirebilirsiniz.

**Bir SQL veritabanı veya havuzu oluştururken bir donanım oluşturma seçmek için**

Ayrıntılı bilgi için bkz. [SQL veritabanı oluşturma](sql-database-single-database-get-started.md).

**Temel bilgiler** sekmesinde, **işlem + depolama** bölümünde **Veritabanını yapılandır** bağlantısını seçin ve ardından **yapılandırma bağlantısını değiştir** ' i seçin:

  ![veritabanı yapılandırma](media/sql-database-service-tiers-vcore/configure-sql-database.png)

İstediğiniz donanım üretimini seçin:

  ![donanım seçin](media/sql-database-service-tiers-vcore/select-hardware.png)


**Mevcut bir SQL veritabanının veya havuzunun donanım oluşturmayı değiştirmek için**

Bir veritabanı için genel bakış sayfasında, **fiyatlandırma katmanı** bağlantısını seçin:

  ![donanımı değiştirme](media/sql-database-service-tiers-vcore/change-hardware.png)

Bir havuz için genel bakış sayfasında **Yapılandır**' ı seçin.

Yapılandırmayı değiştirmek için adımları izleyin ve önceki adımlarda açıklandığı gibi donanım üretimini seçin.

### <a name="hardware-availability"></a>Donanım kullanılabilirliği

#### <a name="gen4gen5"></a>4\. nesil/5. nesil

Yeni 4. nesil veritabanları artık Avustralya Doğu veya Brezilya Güney bölgelerinde desteklenmez. 

5\. nesil, dünya çapındaki birçok bölgede kullanılabilir.

#### <a name="fsv2-series"></a>Fsv2-serisi

Fsv2 serisi şu bölgelerde kullanılabilir: Avustralya Orta, Avustralya Orta 2, Avustralya Doğu, Avustralya Güneydoğu, Brezilya Güney, Kanada Orta, Doğu Asya, Doğu ABD, Fransa Orta, Hindistan Orta, Hindistan Batı, Kore Orta, Kore Güney, Kuzey Avrupa, Güney Afrika Kuzey, Güneydoğu Asya, UK Güney, UK Batı, Batı Avrupa, Batı ABD 2.


#### <a name="m-series"></a>M serisi

A serisi şu bölgelerde kullanılabilir: Doğu ABD, Kuzey Avrupa, Batı Avrupa, Batı ABD 2.
Ayrıca, ek bölgelerde da sınırlı kullanılabilirlik olabilir. Burada listelenenden farklı bir bölge isteyebilirsiniz, ancak farklı bir bölgede yerine getirilmesi mümkün olmayabilir.

Bir abonelikte d serisi kullanılabilirliği etkinleştirmek için [Yeni bir destek isteği](#create-a-support-request-to-enable-m-series)kaydederek erişim istenmesi gerekir.


##### <a name="create-a-support-request-to-enable-m-series"></a>D serisini etkinleştirmek için bir destek isteği oluşturun: 

1. Portalda **Yardım + Destek** ' i seçin.
2. **Yeni destek isteği**’ni seçin.

**Temel bilgiler** sayfasında, aşağıdakileri sağlayın:

1. **Sorun türü**için **hizmet ve abonelik sınırları (kotalar)** öğesini seçin.
2. **Abonelik** Için = e serisi etkinleştirmek üzere aboneliği seçin.
3. **Kota türü**için **SQL veritabanı**' nı seçin.
4. **Ayrıntılar** sayfasına gitmek için **İleri ' yi** seçin.

**Ayrıntılar** sayfasında, aşağıdakileri sağlayın:

5. **Sorun ayrıntıları** bölümünde, **ayrıntıları sağla** bağlantısını seçin. 
6. **SQL veritabanı kota türü** Için, **ı serisi**seçin.
7. **Bölge**Için, e serisi etkinleştirmek üzere bölgeyi seçin.
    D serisi kullanılabilir olan bölgelerde, bkz. [d serisi kullanılabilirlik](#m-series).

Onaylanan destek istekleri genellikle 5 iş günü içinde yerine getirilir.


## <a name="next-steps"></a>Sonraki adımlar

- Bir SQL veritabanı oluşturmak için, bkz. [Azure Portal kullanarak SQL veritabanı oluşturma](sql-database-single-database-get-started.md).
- Tek veritabanları için kullanılabilen belirli işlem boyutları ve depolama boyutu seçimleri için bkz. [tek veritabanları Için SQL veritabanı sanal çekirdek tabanlı kaynak sınırları](sql-database-vcore-resource-limits-single-databases.md).
- Elastik havuzlara yönelik belirli işlem boyutları ve depolama boyutu seçimleri için bkz. [elastik havuzlar Için SQL veritabanı sanal çekirdek tabanlı kaynak sınırları](sql-database-vcore-resource-limits-elastic-pools.md).
- Fiyatlandırma ayrıntıları için bkz. [Azure SQL Veritabanı fiyatlandırma sayfası](https://azure.microsoft.com/pricing/details/sql-database/single/).
