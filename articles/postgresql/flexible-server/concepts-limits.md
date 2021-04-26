---
title: Sınırlar-PostgreSQL için Azure veritabanı-esnek sunucu
description: Bu makalede PostgreSQL için Azure veritabanı 'nın (bağlantı ve depolama motoru seçenekleri gibi) esnek sunucu sınırları açıklanmaktadır.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 351c959a4d3b6cc53064b9d1b65c1282647f308e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105605408"
---
# <a name="limits-in-azure-database-for-postgresql---flexible-server"></a>PostgreSQL için Azure veritabanı sınırları-esnek sunucu

> [!IMPORTANT]
> PostgreSQL için Azure veritabanı-esnek sunucu önizlemededir

Aşağıdaki bölümlerde, veritabanı hizmetindeki kapasite ve işlevsel sınırlar açıklanır. Kaynak (işlem, bellek, depolama) katmanları hakkında bilgi edinmek istiyorsanız, bkz. [işlem ve depolama](concepts-compute-storage.md) makalesi.

## <a name="maximum-connections"></a>En fazla bağlantı sayısı

Fiyatlandırma Katmanı ve sanal çekirdek başına en fazla bağlantı sayısı aşağıda gösterilmiştir. Azure sistemi, PostgreSQL için Azure veritabanı 'nı izlemek üzere üç bağlantı gerektirir-esnek sunucu.

| SKU adı             | Sanal çekirdek | Bellek boyutu | En Çok Bağlantı | En fazla kullanıcı bağlantısı |
|----------------------|--------|-------------|-----------------|----------------------|
| **Burstable**        |        |             |                 |                      |
| B1ms                 | 1      | 2 GiB       | 50              | 47                   |
| B2s                  | 2      | 4 GiB       | 100             | 97                   |
| **Genel Amaçlı**  |        |             |                 |                      |
| D2s_v3               | 2      | 8 GiB       | 214             | 211                  |
| D4s_v3               | 4      | 16 GiB      | 429             | 426                  |
| D8s_v3               | 8      | 32 GiB      | 859             | 856                  |
| D16s_v3              | 16     | 64 GiB      | 1718            | 1715                 |
| D32s_v3              | 32     | 128 GiB     | 3437            | 3434                 |
| D48s_v3              | 48     | 192 GiB     | 5000            | 4997                 |
| D64s_v3              | 64     | 256 GiB     | 5000            | 4997                 |
| **Bellek için Iyileştirilmiş** |        |             |                 |                      |
| E2s_v3               | 2      | 16 GiB      | 1718            | 1715                 |
| E4s_v3               | 4      | 32 GiB      | 3437            | 3434                 |
| E8s_v3               | 8      | 64 GiB      | 5000            | 4997                 |
| E16s_v3              | 16     | 128 GiB     | 5000            | 4997                 |
| E32s_v3              | 32     | 256 GiB     | 5000            | 4997                 |
| E48s_v3              | 48     | 384 GiB     | 5000            | 4997                 |
| E64s_v3              | 64     | 432 GiB     | 5000            | 4997                 |

Bağlantılar sınırı aştığında, şu hatayı alabilirsiniz:
> ÖNEMLI: ne yazık ki çok fazla istemci var

> [!IMPORTANT]
> En iyi deneyim için, bağlantıları verimli bir şekilde yönetmek üzere PgBouncer gibi bir bağlantı havuzlayıcı kullanmanızı öneririz.

Bir PostgreSQL bağlantısı bile boşta, yaklaşık 10 MB bellek içerebilir. Ayrıca, yeni bağlantılar oluşturmak zaman alır. Çoğu uygulama, bu durumu çözer birçok kısa süreli bağlantı ister. Sonuç olarak gerçek iş yükünüz için daha az kaynak kullanılabilir ve performansı azaltıldı. Bağlantı havuzu, boştaki bağlantıları azaltmak ve var olan bağlantıları yeniden kullanmak için kullanılabilir. Daha fazla bilgi edinmek için [Blog gönderimizi](https://techcommunity.microsoft.com/t5/azure-database-for-postgresql/not-all-postgres-connection-pooling-is-equal/ba-p/825717)ziyaret edin.

## <a name="functional-limitations"></a>İşlevsel sınırlamalar

### <a name="scale-operations"></a>Ölçeklendirme işlemleri

- Sunucu depolamanın ölçeklendirilmesi için sunucu yeniden başlatması gerekir.
- Sunucu depolaması yalnızca 2x artışlarla ölçeklendirilebilir, Ayrıntılar için bkz. [işlem ve depolama](concepts-compute-storage.md) .
- Azalan sunucu depolama boyutu şu anda desteklenmiyor.

### <a name="server-version-upgrades"></a>Sunucu sürümü yükseltmeleri

- Ana veritabanı altyapısı sürümleri arasında otomatik geçiş Şu anda desteklenmiyor. Bir sonraki ana sürüme yükseltmek isterseniz, bir dökümünü alın ve yeni altyapı sürümüyle oluşturulmuş bir sunucuya [geri yükleyin](../howto-migrate-using-dump-and-restore.md) .

### <a name="storage"></a>Depolama

- Yapılandırıldıktan sonra, depolama boyutu küçültülemez. İstenen depolama boyutuna sahip yeni bir sunucu oluşturmanız, el ile [döküm uygulamanız ve veritabanınızı geri yüklemeniz](../howto-migrate-using-dump-and-restore.md) ve veritabanınızı yeni sunucuya geçirmeniz gerekir.
- Şu anda, depolama otomatik büyüme özelliği kullanılamıyor. Lütfen kullanımı izleyin ve depolama alanını daha yüksek bir boyuta yükseltin. 
- Depolama alanı kullanımı %95 ' e ulaştığında veya kullanılabilir kapasite 5 GiB 'den küçükse, disk dolu durumlarla ilişkili hataları önlemek için sunucu otomatik olarak **salt okuma moduna** geçiş yapılır. 
- `storage used` `storage percent` Depolama boyutunu artırma gibi işlemleri daha etkili bir şekilde gerçekleştirmek için bazı eşikleri aştığında ya da bu uyarılar için uyarı kuralları ayarlamanızı öneririz. Örneğin, depolama alanı yüzdesi %80 kullanımı aşarsa bir uyarı ayarlayabilirsiniz.
  
### <a name="networking"></a>Ağ

- VNET 'e ve dışına taşıma Şu anda desteklenmiyor.
- VNET içinde dağıtım ile genel erişimin birleştirilmesi Şu anda desteklenmiyor.
- Güvenlik duvarı kuralları VNET üzerinde desteklenmez, bunun yerine ağ güvenlik grupları kullanılabilir.
- Genel erişim veritabanı sunucuları, genel İnternet 'e bağlanabilir (örneğin,) `postgres_fdw` ve bu erişim kısıtlanamaz. VNET tabanlı sunucuların ağ güvenlik grupları kullanılarak sınırlı giden erişimi olabilir.

### <a name="high-availability-ha"></a>Yüksek kullanılabilirlik (HA)

- Zone-Redundant HA Şu anda Burstable sunucular için desteklenmiyor.
- Sunucunuz HA bekleme moduna çağrıldığında veritabanı sunucusu IP adresi değişir. Sunucu IP adresi yerine DNS kaydını kullandığınızdan emin olun.
- Mantıksal çoğaltma, bir HA yapılandırılmış esnek sunucu ile yapılandırıldıysa, bekleme sunucusuna yük devretme durumunda mantıksal çoğaltma yuvaları, bekleme sunucusuna kopyalanmaz. 
- Sınırlamalar dahil, bölgesel olarak yedekli HA hakkında daha fazla bilgi için lütfen [Concepts-ha belgeleri](concepts-high-availability.md) sayfasına bakın.

### <a name="availability-zones"></a>Kullanılabilirlik alanları

- Sunucuları farklı bir kullanılabilirlik bölgesine el ile taşıma işlemi şu anda desteklenmiyor.
- HA bekleme sunucusunun kullanılabilirlik bölgesi el ile yapılandırılamaz.

### <a name="postgres-engine-extensions-and-pgbouncer"></a>Postgres altyapısı, Uzantılar ve PgBouncer

- Postgres 10 ve üzeri desteklenmez. Eski Postgres sürümlerini kullanmanız gerekiyorsa [tek sunuculu](../overview-single-server.md) seçeneğini kullanmanızı öneririz.
- Uzantı desteği şu anda Postgres `contrib` uzantılarıyla sınırlıdır.
- Yerleşik PgBouncer bağlantı havuzlayıcı Şu anda bir sanal ağ içindeki veritabanı sunucularında veya Burstable sunucular için kullanılamaz.

### <a name="stopstart-operation"></a>İşlemi Durdur/Başlat

- Sunucu yedi günden uzun bir süre için durdurulamıyor.

### <a name="scheduled-maintenance"></a>Zamanlanan bakım

- Zaten planlanmış bir yükseltmeden önce bakım penceresini beş günden daha az değiştirmek bu yükseltmeyi etkilemez. Değişiklikler yalnızca bir sonraki zamanlanmış bakımda etkili olur.

### <a name="backing-up-a-server"></a>Sunucu yedekleme

- Yedeklemeler sistem tarafından yönetilir, şu anda bu yedeklemeleri el ile çalıştırmanın bir yolu yoktur. `pg_dump`Bunun yerine kullanmanızı öneririz.
- Yedeklemeler her zaman anlık görüntü tabanlı tam yedeklemelerdir (değişiklik yedeklemeleri değil), büyük olasılıkla yedekleme depolama kullanımı daha yüksektir. İşlem günlüklerinin (ön günlükleri yaz-WAL) tam/değişiklik yedeklemelerinden ayrı olduğunu ve sürekli olarak arşivlendiğini unutmayın.

### <a name="restoring-a-server"></a>Bir sunucuyu geri yükleme

- Süre içinde geri yükleme özelliğini kullanırken yeni sunucu, temel aldığı sunucuyla aynı işlem ve depolama yapılandırmasıyla oluşturulur.
- Bir yedekten geri yükleme yaptığınızda, VNET tabanlı veritabanı sunucuları aynı VNET 'e geri yüklenir.
- Geri yükleme sırasında oluşturulan yeni sunucu, özgün sunucuda var olan güvenlik duvarı kurallarına sahip değildir. Yeni sunucu için Güvenlik Duvarı kurallarının ayrı olarak oluşturulması gerekir.
- Silinen bir sunucunun geri yüklenmesi desteklenmez.
- Çapraz bölge geri yükleme desteklenmez.

### <a name="other-features"></a>Diğer özellikler

* Azure AD kimlik doğrulaması henüz desteklenmiyor. Azure AD kimlik doğrulaması gerektiriyorsa [tek sunuculu](../overview-single-server.md) seçeneğini kullanmanızı öneririz.
* Okuma çoğaltmaları henüz desteklenmiyor. Okuma çoğaltmalarının gerekli olması için [tek sunucu](../overview-single-server.md) seçeneğini kullanmanızı öneririz.
* Kaynakları başka bir aboneliğe taşıma desteklenmiyor. 


## <a name="next-steps"></a>Sonraki adımlar

- [İşlem ve depolama seçeneklerinde nelerin kullanılabildiğini](concepts-compute-storage.md) anlayın
- [Desteklenen PostgreSQL veritabanı sürümleri](concepts-supported-versions.md) hakkında bilgi edinin
- [Azure Portal kullanarak PostgreSQL Için Azure veritabanı 'nda bir sunucunun nasıl yedeklendiğini ve geri yükleneceğini](how-to-restore-server-portal.md) inceleyin
