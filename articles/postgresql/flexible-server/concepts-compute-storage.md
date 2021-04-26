---
title: İşlem ve depolama seçenekleri-PostgreSQL için Azure veritabanı-esnek sunucu
description: Bu makalede PostgreSQL için Azure veritabanı 'nda işlem ve depolama seçenekleri (esnek sunucu) açıklanmaktadır.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/19/2021
ms.openlocfilehash: 93a439c814b6bac630c5231f46d7a022a131687c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105608179"
---
# <a name="compute-and-storage-options-in-azure-database-for-postgresql---flexible-server"></a>PostgreSQL için Azure veritabanı 'nda işlem ve depolama seçenekleri-esnek sunucu

> [!IMPORTANT]
> PostgreSQL için Azure veritabanı-esnek sunucu önizlemededir

PostgreSQL için Azure veritabanı sunucusu oluşturmak için üç farklı fiyatlandırma katmanlarından birini kullanabilirsiniz: Burstable, Genel Amaçlı ve bellek için Iyileştirilmiş. Fiyatlandırma katmanları, sağlanan sanal çekirdekler, sanal çekirdek başına bellek ve verileri depolamak için kullanılan depolama teknolojisi miktarına göre farklılaştırılabilir. Tüm kaynaklar PostgreSQL sunucu düzeyinde sağlanır. Sunucuda bir veya daha fazla veritabanı olabilir.

| Kaynak/katman | **Burstable** | **Genel Amaçlı** | **Bellek için Iyileştirilmiş** |
|:---|:----------|:--------------------|:---------------------|
| Sanal çekirdek | 1, 2 | 2, 4, 8, 16, 32, 48, 64 | 2, 4, 8, 16, 32, 48, 64 |
| Sanal çekirdek başına bellek | Değişken | 4 GB | 6,75 ila 8 GB |
| Depolama boyutu | 32 GB ila 16 TB | 32 GB ila 16 TB | 32 GB ila 16 TB |
| Veritabanı yedekleme saklama süresi | 7-35 gün | 7-35 gün | 7-35 gün |

Fiyatlandırma katmanını seçmek için, başlangıç noktası olarak aşağıdaki tabloyu kullanın.

| Fiyatlandırma katmanı | Hedef iş yükleri |
|:-------------|:-----------------|
| Burstable | Her zaman sürekli olarak tam CPU gerektirmeyen iş yükleri için idealdir. |
| Genel Amaçlı | Ölçeklenebilir g/ç aktarım hızı ile dengeli işlem ve bellek gerektiren iş yüklerinin çoğu. Örnek olarak web uygulamalarını, mobil uygulamaları ve diğer kurumsal uygulamaları barındıran sunucular verilebilir.|
| Bellek İçin İyileştirilmiş | Daha hızlı işlem işleme ve daha yüksek eşzamanlılık için bellek içi performans gerektiren yüksek performanslı veritabanı iş yükleri. Örnek olarak gerçek zamanlı verileri işleyen ve yüksek performanslı işlem tabanlı ya da analiz uygulamalarının sunucuları verilebilir.|

Sunucu oluşturduktan sonra, işlem katmanı, sanal çekirdek sayısı ve depolama boyutu, saniyeler içinde yukarı veya aşağı değişebilir. Ayrıca, yedekleme bekletme süresini yukarı veya aşağı bağımsız olarak ayarlayabilirsiniz. Daha fazla bilgi için bkz. [kaynakları ölçeklendirme](#scale-resources) bölümü.

## <a name="compute-tiers-vcores-and-server-types"></a>İşlem katmanları, sanal çekirdek ve sunucu türleri

İşlem kaynakları katmana, sanal çekirdekleri ve bellek boyutuna göre seçilebilir. Sanal çekirdekler, temel alınan donanımın mantıksal CPU 'sunu temsil eder.

Kullanılabilir sunucu türlerinin ayrıntılı özellikleri şunlardır:

| SKU adı             | Sanal çekirdek | Bellek boyutu | Desteklenen ıOPS üst sınırı | Desteklenen en fazla g/ç bant genişliği |
|----------------------|--------|-------------|--------------------|-----------------------------|
| **Burstable**        |        |             |                    |                             |
| B1ms                 | 1      | 2 GiB       | 640                | 15 MiB/sn                  |
| B2s                  | 2      | 4 GiB       | 1280               | 15 MiB/sn                  |
| **Genel Amaçlı**  |        |             |                    |                             |
| D2s_v3               | 2      | 8 GiB       | 3200               | 48 MIB/sn                  |
| D4s_v3               | 4      | 16 GiB      | 6400               | 96 MIB/sn                  |
| D8s_v3               | 8      | 32 GiB      | 12800              | 192 MIB/sn                 |
| D16s_v3              | 16     | 64 GiB      | 18000              | 384 MIB/sn                 |
| D32s_v3              | 32     | 128 GiB     | 18000              | 750 MIB/sn                 |
| D48s_v3              | 48     | 192 GiB     | 18000              | 750 MIB/sn                 |
| D64s_v3              | 64     | 256 GiB     | 18000              | 750 MIB/sn                 |
| **Bellek için Iyileştirilmiş** |        |             |                    |                             |
| E2s_v3               | 2      | 16 GiB      | 3200               | 48 MIB/sn                  |
| E4s_v3               | 4      | 32 GiB      | 6400               | 96 MIB/sn                  |
| E8s_v3               | 8      | 64 GiB      | 12800              | 192 MIB/sn                 |
| E16s_v3              | 16     | 128 GiB     | 18000              | 384 MIB/sn                 |
| E32s_v3              | 32     | 256 GiB     | 18000              | 750 MIB/sn                 |
| E48s_v3              | 48     | 384 GiB     | 18000              | 750 MIB/sn                 |
| E64s_v3              | 64     | 432 GiB     | 18000              | 750 MIB/sn                 |

## <a name="storage"></a>Depolama

Sağladığınız depolama alanı, PostgreSQL için Azure veritabanı sunucunuza sunulan depolama kapasitesi miktarıdır. Depolama alanı veritabanı dosyaları, geçici dosyalar, işlem günlükleri ve PostgreSQL sunucu günlükleri için kullanılır. Sağladığınız toplam depolama miktarı, sunucunuz için kullanılabilir olan g/ç kapasitesini de tanımlar.

Depolama aşağıdaki sabit boyutlarda kullanılabilir:

| Disk boyutu | IOPS |
|:---|:---|
| 32 GiB | Sağlanan 120, en fazla 3.500 |
| 64 GiB | Sağlanan 240, en fazla 3.500 |
| 128 GiB | Sağlanan 500, en fazla 3.500 |
| 256 GiB | Sağlanan 1100, en fazla 3.500 |
| 512 GiB | Sağlanan 2300, en fazla 3.500 |
| 1 TiB | 5.000 |
| 2 TiB | 7.500 |
| 4 TiB | 7.500 |
| 8 TiB | 16.000 |
| 16 TiB | 18.000 |

IOPS 'nin sanal makine türü tarafından da kısıtlandığı unutulmamalıdır. Sunucu türünden bağımsız bir depolama boyutu seçebiliyor olsanız da, özellikle az sayıda sanal çekirdeğe sahip bir sunucu seçtiğinizde depolamanın sağladığı tüm ıOPS 'leri kullanamazsınız.

Sunucu oluşturma sırasında ve sonrasında ek depolama kapasitesi ekleyebilirsiniz.

>[!NOTE]
> Depolama yalnızca yukarı ölçeklenebilen, aşağı doğru değil.

G/ç tüketiminizi Azure portal veya Azure CLı komutlarını kullanarak izleyebilirsiniz. İzlenecek ilgili ölçümler [depolama sınırı, depolama yüzdesi, kullanılan depolama alanı ve yüzde GÇ](concepts-monitoring.md)' dır.

### <a name="maximum-iops-for-your-configuration"></a>Yapılandırmanız için maksimum ıOPS

|SKU adı            |GiB 'de depolama boyutu                       |32 |64 |128 |256 |512  |1.024|2.048|4.096|8,192 |16.384|
|--------------------|------------------------------------------|---|---|----|----|-----|-----|-----|-----|------|------|
|                    |Maksimum ıOPS                              |120|240|500 |1100|2300 |5000 |7.500 |7.500 |16000 |18000 |
|**Burstable**       |                                          |   |   |    |    |     |     |     |     |      |      |
|B1ms                |640 ıOPS                                  |120|240|500 |640 *|640 * |640 * |640 * |640 * |640 *  |640 *  |
|B2s                 |1280 ıOPS                                 |120|240|500 |1100|1280 *|1280 *|1280 *|1280 *|1280 * |1280 * |
|**Genel Amaçlı** |                                          |   |   |    |    |     |     |     |     |      |      |
|D2s_v3              |3200 ıOPS                                 |120|240|500 |1100|2300 |3200 *|3200 *|3200 *|3200 * |3200 * |
|D4s_v3              |6.400 ıOPS                                |120|240|500 |1100|2300 |5000 |6400 *|6400 *|6400 * |6400 * |
|D8s_v3              |12.800 ıOPS                               |120|240|500 |1100|2300 |5000 |7.500 |7.500 |12800 *|12800 *|
|D16s_v3             |18.000 ıOPS                               |120|240|500 |1100|2300 |5000 |7.500 |7.500 |16000 |18000 |
|D32s_v3             |18.000 ıOPS                               |120|240|500 |1100|2300 |5000 |7.500 |7.500 |16000 |18000 |
|D48s_v3             |18.000 ıOPS                               |120|240|500 |1100|2300 |5000 |7.500 |7.500 |16000 |18000 |
|D64s_v3             |18.000 ıOPS                               |120|240|500 |1100|2300 |5000 |7.500 |7.500 |16000 |18000 |
|**Bellek için Iyileştirilmiş**|                                          |   |   |    |    |     |     |     |     |      |      |
|E2s_v3              |3200 ıOPS                                 |120|240|500 |1100|2300 |3200 *|3200 *|3200 *|3200 * |3200 * |
|E4s_v3              |6.400 ıOPS                                |120|240|500 |1100|2300 |5000 |6400 *|6400 *|6400 * |6400 * |
|E8s_v3              |12.800 ıOPS                               |120|240|500 |1100|2300 |5000 |7.500 |7.500 |12800 *|12800 *|
|E16s_v3             |18.000 ıOPS                               |120|240|500 |1100|2300 |5000 |7.500 |7.500 |16000 |18000 |
|E32s_v3             |18.000 ıOPS                               |120|240|500 |1100|2300 |5000 |7.500 |7.500 |16000 |18000 |
|E48s_v3             |18.000 ıOPS                               |120|240|500 |1100|2300 |5000 |7.500 |7.500 |16000 |18000 |
|E64s_v3             |18.000 ıOPS                               |120|240|500 |1100|2300 |5000 |7.500 |7.500 |16000 |18000 |

, İle işaretlendiğinde \* , IOPS SEÇTIĞINIZ VM türüyle sınırlıdır. Aksi takdirde ıOPS, seçilen depolama boyutuyla sınırlıdır.

>[!NOTE]
> Disk düzeyinde burdıya nedeniyle ölçümlerde daha yüksek ıOPS görebilirsiniz. Daha fazla ayrıntı için lütfen [belgelere](../../virtual-machines/disk-bursting.md#disk-level-bursting) bakın. 

### <a name="maximum-io-bandwidth-mibsec-for-your-configuration"></a>Yapılandırmanız için en fazla g/ç bant genişliği (MIB/sn)

|SKU adı            |Depolama boyutu, GiB                             |32 |64 |128 |256 |512  |1.024|2.048|4.096|8,192 |16.384|
|--------------------|----------------------------------------------|---|---|----|----|-----|-----|-----|-----|------|------|
|                    |**Depolama bant genişliği, MIB/sn**                |25 |50 |100 |125 |150  |200  |250  |250  |500   |750   |
|**Burstable**       |                                              |   |   |    |    |     |     |     |     |      |      |
|B1ms                |10 MIB/sn                                    |(|(|( |( |(  |(  |(  |(  |(   |(   |
|B2s                 |15 MiB/sn                                    |aşamaz|aşamaz|aşamaz |aşamaz |aşamaz  |aşamaz  |aşamaz  |aşamaz  |aşamaz   |aşamaz   |
|**Genel Amaçlı** |                                              |   |   |    |    |     |     |     |     |      |      |
|D2s_v3              |48 MIB/sn                                    |25 |48 *|48 * |48 * |48 *  |48 *  |48 *  |48 *  |48 *   |48 *   |
|D4s_v3              |96 MIB/sn                                    |25 |50 |96 * |96 * |96 *  |96 *  |96 *  |96 *  |96 *   |96 *   |
|D8s_v3              |192 MIB/sn                                   |25 |50 |100 |125 |150  |192 * |192 * |192 * |192 *  |192 *  |
|D16s_v3             |384 MIB/sn                                   |25 |50 |100 |125 |150  |200  |250  |250  |384 *  |384 *  |
|D32s_v3             |750 MIB/sn                                   |25 |50 |100 |125 |150  |200  |250  |250  |500   |750   |
|D48s_v3             |750 MIB/sn                                   |25 |50 |100 |125 |150  |200  |250  |250  |500   |750   |
|D64s_v3             |750 MIB/sn                                   |25 |50 |100 |125 |150  |200  |250  |250  |500   |750   |
|**Bellek için Iyileştirilmiş**|                                              |   |   |    |    |     |     |     |     |      |      |
|E2s_v3              |48 MIB/sn                                    |25 |48 *|48 * |48 * |48 *  |48 *  |48 *  |48 *  |48 *   |48 *   |
|E4s_v3              |96 MIB/sn                                    |25 |50 |96 * |96 * |96 *  |96 *  |96 *  |96 *  |96 *   |96 *   |
|E8s_v3              |192 MIB/sn                                   |25 |50 |100 |125 |150  |192 * |192 * |192 * |192 *  |192 *  |
|E16s_v3             |384 MIB/sn                                   |25 |50 |100 |125 |150  |200  |250  |250  |384 *  |384 *  |
|E32s_v3             |750 MIB/sn                                   |25 |50 |100 |125 |150  |200  |250  |250  |500   |750   |
|E48s_v3             |750 MIB/sn                                   |25 |50 |100 |125 |150  |200  |250  |250  |500   |750   |
|E64s_v3             |750 MIB/sn                                   |25 |50 |100 |125 |150  |200  |250  |250  |500   |750   |

Bir \* g/ç bant genişliği ile işaretlendiğinde, SEÇTIĞINIZ VM türüyle sınırlı olur. Aksi takdirde g/ç bant genişliği seçili depolama boyutuyla sınırlıdır.

### <a name="reaching-the-storage-limit"></a>Depolama sınırına ulaşma

Depolama sınırına ulaştığınızda, sunucu hataları döndürbaşlayacaktır ve diğer değişiklikleri engeller. Bu da yedeklemeler ve WAL Arşivi gibi diğer işletimsel etkinliklerle ilgili sorunlara neden olabilir.

Bu durumdan kaçınmak için, depolama alanı kullanımı %95 ' e ulaştığında veya kullanılabilir kapasite 5 GiB 'den küçükse, sunucu otomatik olarak **salt okuma moduna** geçiş yapılır.

Kullanımda olan disk alanını etkin bir şekilde izlemenizi ve depolama alanının dışında disk boyutunu artırmayı öneririz. Sunucu depoağınızın disk dışına yaklaştığı durumlarda size bildirimde bulunan bir uyarı oluşturabilirsiniz, böylece disk tükenme sorunları yaşamaktan kaçınabilirsiniz. Daha fazla bilgi için bkz. [Uyarı ayarlama](howto-alert-on-metrics.md)hakkında belge.


### <a name="storage-auto-grow"></a>Depolama otomatik büyüme

Depolama otomatik büyüme, esnek sunucu için henüz kullanılamıyor.

## <a name="backup"></a>Backup

Hizmet, sunucunuzun yedeklerini otomatik olarak alır. 7 ile 35 gün arasında bir bekletme dönemi seçebilirsiniz. [Kavramlar makalesinde](concepts-backup-restore.md)yedeklemeler hakkında daha fazla bilgi edinin.

## <a name="scale-resources"></a>Kaynakları ölçeklendirme

Sunucunuzu oluşturduktan sonra, sanal çekirdekleri, işlem katmanını, depolama miktarını ve yedekleme saklama süresini bağımsız olarak değiştirebilirsiniz. Sanal çekirdek sayısı yukarı veya aşağı ölçeklendirilebilir. Yedekleme saklama süresi 7 ile 35 gün arasında ölçeklendirilebilir veya kapatılabilir. Depolama boyutu yalnızca artırılabilir. Kaynakların ölçeklendirilmesi portal veya Azure CLı aracılığıyla yapılabilir.

> [!NOTE]
> Depolama boyutu yalnızca artırılabilir. Artdıktan sonra daha küçük bir depolama boyutuna dönemezsiniz.

Vçekirdeklerinin veya işlem katmanının sayısını değiştirdiğinizde yeni sunucu türünün etkili olması için sunucu yeniden başlatılır. Sistem yeni sunucuya geçerken yeni bağlantı kurulamaz ve tüm işlenmemiş işlemler geri alınır. Bu süre değişir, ancak çoğu durumda bir dakikadan daha kısadır. Depolamanın ölçeklendirilmesi aynı şekilde işe yarar ve ayrıca kısa bir yeniden başlatma gerektirir.

Yedekleme saklama süresini değiştirmek, çevrimiçi bir işlemdir.

## <a name="pricing"></a>Fiyatlandırma

En güncel fiyatlandırma bilgileri için bkz. hizmet [fiyatlandırma sayfası](https://azure.microsoft.com/pricing/details/PostgreSQL/). İstediğiniz yapılandırmanın maliyetini görmek için [Azure Portal](https://portal.azure.com/#create/Microsoft.PostgreSQLServer) , seçtiğiniz seçeneklere göre **fiyatlandırma katmanı** sekmesindeki aylık maliyeti gösterir. Azure aboneliğiniz yoksa, tahmini bir fiyat almak için Azure Fiyatlandırma hesaplayıcısı ' nı kullanabilirsiniz. [Azure Fiyatlandırma Hesaplayıcı](https://azure.microsoft.com/pricing/calculator/) Web sitesinde, **öğe Ekle**' yi seçin, **veritabanları** kategorisini genişletin ve seçenekleri özelleştirmek Için **PostgreSQL için Azure veritabanı** ' nı seçin.

## <a name="next-steps"></a>Sonraki adımlar

- [Portalda bir PostgreSQL sunucusu oluşturmayı](how-to-manage-server-portal.md)öğrenin.
- [Hizmet limitleri](concepts-limits.md)hakkında bilgi edinin.