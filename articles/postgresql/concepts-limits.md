---
title: PostgreSQL için Azure veritabanı 'nın sınırları-tek sunucu
description: Bu makalede PostgreSQL için Azure veritabanı 'nda, bağlantı ve depolama altyapısı seçenekleri gibi tek bir sunucu sınırları açıklanmaktadır.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/25/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: b9cef4753b6fd324b38d7254139fe288463a0c0c
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74123902"
---
# <a name="limits-in-azure-database-for-postgresql---single-server"></a>PostgreSQL için Azure veritabanı 'nın sınırları-tek sunucu
Aşağıdaki bölümlerde, veritabanı hizmetindeki kapasite ve işlevsel sınırlar açıklanır. Kaynak (işlem, bellek, depolama) katmanları hakkında bilgi edinmek istiyorsanız [fiyatlandırma katmanları](concepts-pricing-tiers.md) makalesine bakın.


## <a name="maximum-connections"></a>Bağlantı sayısı üst sınırı
Fiyatlandırma katmanı ve sanal çekirdek başına bağlantıları sayısı aşağıdaki gibidir: 

|**Fiyatlandırma Katmanı**| **Sanal çekirdek**| **En fazla bağlantı sayısı** | **En fazla kullanıcı bağlantısı** |
|---|---|---|---|
|Temel| 1| 55 | 50|
|Temel| 2| 105 | 100|
|Genel Amaçlı| 2| 150| 145|
|Genel Amaçlı| 4| 250| 245|
|Genel Amaçlı| 8| 480| 475|
|Genel Amaçlı| 16| 950| 945|
|Genel Amaçlı| 32| 1500| 1495|
|Genel Amaçlı| 64| 1900| 1895|
|Bellek için İyileştirilmiş| 2| 300| 295|
|Bellek için İyileştirilmiş| 4| 500| 495|
|Bellek için İyileştirilmiş| 8| 960| 955|
|Bellek için İyileştirilmiş| 16| 1900| 1895|
|Bellek için İyileştirilmiş| 32| 1987| 1982|

Bağlantı sınırı aştıklarında aşağıdaki hata iletisini alabilirsiniz:
> ÖNEMLI: ne yazık ki çok fazla istemci var

Azure sistemi, PostgreSQL için Azure veritabanı sunucusunu izlemek üzere beş bağlantı gerektirir. 

## <a name="functional-limitations"></a>İşlev sınırlamaları
### <a name="scale-operations"></a>Ölçeklendirme işlemleri
- Temel fiyatlandırma katmanları gelen ve giden dinamik ölçeklendirme şu anda desteklenmiyor.
- Azalan sunucu depolama boyutu şu anda desteklenmiyor.

### <a name="server-version-upgrades"></a>Sunucu sürümü yükseltme
- Ana veritabanı altyapısı sürümleri arasında otomatik geçişi şu anda desteklenmiyor. Bir sonraki ana sürümüne yükseltmek istiyorsanız, olması bir [döküm ve geri yükleme](./howto-migrate-using-dump-and-restore.md) yeni altyapısı sürümü ile oluşturulmuş bir sunucu için.

> PostgreSQL sürüm 10 ' dan önce, [PostgreSQL sürüm oluşturma ilkesinin](https://www.postgresql.org/support/versioning/) birinci _veya_ ikinci numaradan artış olacak _ana sürüm_ yükseltmesini kabul eden (örneğin, 9,5 ila 9,6 _ana_ sürüm yükseltmesi olarak kabul edildiği) göz önünde bulundurulmalıdır.
> Sürüm 10 ' dan itibaren, yalnızca ilk sayıdaki değişiklik büyük bir sürüm yükseltmesi olarak kabul edilir (örneğin, 10,0 ile 10,1 arası, _küçük_ bir sürüm yükseltirsiniz ve 10 ila 11 _ana_ sürüm yükseltmesiyle).

### <a name="vnet-service-endpoints"></a>Sanal ağ hizmet uç noktaları
- Yalnızca genel amaçlı ve bellek için iyileştirilmiş sunucuları için sanal ağ hizmet uç noktaları desteğidir.

### <a name="restoring-a-server"></a>Bir sunucuyu geri yükleme
- INR özelliği kullanılırken yeni sunucu, temel aldığı sunucuyla aynı fiyatlandırma katmanı yapılandırmalarına sahip olarak oluşturulur.
- Geri yükleme sırasında oluşturulan yeni sunucu, özgün sunucuda var olan güvenlik duvarı kurallarına sahip değildir. Güvenlik Duvarı kurallarının bu yeni sunucu için ayrı olarak ayarlanması gerekir.
- Silinen bir sunucuya geri yükleme desteklenmiyor.

### <a name="utf-8-characters-on-windows"></a>Windows 'da UTF-8 karakterleri
- Bazı senaryolarda UTF-8 karakterleri Windows üzerinde açık kaynak PostgreSQL içinde tam olarak desteklenmez. Bu, PostgreSQL için Azure veritabanı 'nı etkiler. Daha fazla bilgi için lütfen [PostgreSQL-Archive hata #15476](https://www.postgresql-archive.org/BUG-15476-Problem-on-show-trgm-with-4-byte-UTF-8-characters-td6056677.html) iş parçacığına bakın.

## <a name="next-steps"></a>Sonraki adımlar
- [Her fiyatlandırma katmanında nelerin kullanılabildiğini](concepts-pricing-tiers.md) anlayın
- [Desteklenen PostgreSQL veritabanı sürümleri](concepts-supported-versions.md) hakkında bilgi edinin
- [Azure Portal kullanarak PostgreSQL Için Azure veritabanı 'nda bir sunucunun nasıl yedeklendiğini ve geri yükleneceğini](howto-restore-server-portal.md) inceleyin
