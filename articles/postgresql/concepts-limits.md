---
title: Sınırlar-PostgreSQL için Azure veritabanı-tek sunucu
description: Bu makalede PostgreSQL için Azure veritabanı 'nda, bağlantı ve depolama altyapısı seçenekleri gibi tek bir sunucu sınırları açıklanmaktadır.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/28/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 7cee2be3c1d7a97b87560873d6cef07ec361ee99
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105605187"
---
# <a name="limits-in-azure-database-for-postgresql---single-server"></a>PostgreSQL için Azure veritabanı 'nın sınırları-tek sunucu
Aşağıdaki bölümlerde, veritabanı hizmetindeki kapasite ve işlevsel sınırlar açıklanır. Kaynak (işlem, bellek, depolama) katmanları hakkında bilgi edinmek istiyorsanız [fiyatlandırma katmanları](concepts-pricing-tiers.md) makalesine bakın.


## <a name="maximum-connections"></a>En fazla bağlantı sayısı
Fiyatlandırma Katmanı ve sanal çekirdek başına en fazla bağlantı sayısı aşağıda gösterilmiştir. Azure sistemi, PostgreSQL için Azure veritabanı sunucusunu izlemek üzere beş bağlantı gerektirir. 

|**Fiyatlandırma Katmanı**| **Sanal çekirdek**| **En Çok Bağlantı** | **En fazla kullanıcı bağlantısı** |
|---|---|---|---|
|Temel| 1| 55 | 50|
|Temel| 2| 105 | 100|
|Genel Amaçlı| 2| 150| 145|
|Genel Amaçlı| 4| 250| 245|
|Genel Amaçlı| 8| 480| 475|
|Genel Amaçlı| 16| 950| 945|
|Genel Amaçlı| 32| 1500| 1495|
|Genel Amaçlı| 64| 1900| 1895|
|Bellek İçin İyileştirilmiş| 2| 300| 295|
|Bellek İçin İyileştirilmiş| 4| 500| 495|
|Bellek İçin İyileştirilmiş| 8| 960| 955|
|Bellek İçin İyileştirilmiş| 16| 1900| 1895|
|Bellek İçin İyileştirilmiş| 32| 1987| 1982|

Bağlantılar sınırı aştığında, şu hatayı alabilirsiniz:
> ÖNEMLI: ne yazık ki çok fazla istemci var

> [!IMPORTANT]
> En iyi deneyim için, bağlantıları verimli bir şekilde yönetmek üzere pgBouncer gibi bir bağlantı havuzlayıcı kullanmanızı öneririz.

Bir PostgreSQL bağlantısı bile boşta, yaklaşık 10 GB bellek içerebilir. Ayrıca, yeni bağlantılar oluşturmak zaman alır. Çoğu uygulama, bu durumu çözer birçok kısa süreli bağlantı ister. Sonuç olarak gerçek iş yükünüz için daha az kaynak kullanılabilir ve performansı azaltıldı. Boştaki bağlantıları azaltan ve var olan bağlantıları yeniden kullanan bir bağlantı havuzlayıcı bunun önlenmesine yardımcı olur. Daha fazla bilgi edinmek için [Blog gönderimizi](https://techcommunity.microsoft.com/t5/azure-database-for-postgresql/not-all-postgres-connection-pooling-is-equal/ba-p/825717)ziyaret edin.

## <a name="functional-limitations"></a>İşlevsel sınırlamalar
### <a name="scale-operations"></a>Ölçeklendirme işlemleri
- Temel fiyatlandırma katmanlarına ve bu katmanlara dinamik ölçeklendirme Şu anda desteklenmiyor.
- Azalan sunucu depolama boyutu şu anda desteklenmiyor.

### <a name="server-version-upgrades"></a>Sunucu sürümü yükseltmeleri
- Ana veritabanı altyapısı sürümleri arasında otomatik geçiş Şu anda desteklenmiyor. Bir sonraki ana sürüme yükseltmek isterseniz, bir dökümünü alın ve yeni altyapı sürümüyle oluşturulmuş bir sunucuya [geri yükleyin](./howto-migrate-using-dump-and-restore.md) .

> PostgreSQL sürüm 10 ' dan önce, [PostgreSQL sürüm oluşturma ilkesinin](https://www.postgresql.org/support/versioning/) birinci _veya_ ikinci numaradan artış olacak _ana sürüm_ yükseltmesini kabul eden (örneğin, 9,5 ila 9,6 _ana_ sürüm yükseltmesi olarak kabul edildiği) göz önünde bulundurulmalıdır.
> Sürüm 10 ' dan itibaren, yalnızca ilk sayıdaki değişiklik büyük bir sürüm yükseltmesi olarak kabul edilir (örneğin, 10,0 ile 10,1 arası, _küçük_ bir sürüm yükseltirsiniz ve 10 ila 11 _ana_ sürüm yükseltmesiyle).

### <a name="vnet-service-endpoints"></a>Sanal ağ hizmet uç noktaları
- VNet hizmet uç noktaları için destek yalnızca Genel Amaçlı ve bellek için Iyileştirilmiş sunucular içindir.

### <a name="restoring-a-server"></a>Bir sunucuyu geri yükleme
- INR özelliği kullanılırken yeni sunucu, temel aldığı sunucuyla aynı fiyatlandırma katmanı yapılandırmalarına sahip olarak oluşturulur.
- Geri yükleme sırasında oluşturulan yeni sunucu, özgün sunucuda var olan güvenlik duvarı kurallarına sahip değildir. Güvenlik Duvarı kurallarının bu yeni sunucu için ayrı olarak ayarlanması gerekir.
- Silinen bir sunucunun geri yüklenmesi desteklenmez.

### <a name="utf-8-characters-on-windows"></a>Windows 'da UTF-8 karakterleri
- Bazı senaryolarda UTF-8 karakterleri Windows üzerinde açık kaynak PostgreSQL içinde tam olarak desteklenmez. Bu, PostgreSQL için Azure veritabanı 'nı etkiler. Daha fazla bilgi için lütfen [PostgreSQL-Archive hata #15476](https://www.postgresql-archive.org/BUG-15476-Problem-on-show-trgm-with-4-byte-UTF-8-characters-td6056677.html) iş parçacığına bakın.

### <a name="gss-error"></a>GSS hatası
**GSS** ile ilgili bir hata görürseniz, büyük olasılıkla Azure Postgres tek sunucusunun henüz tam olarak desteklemediği daha yeni bir istemci/sürücü sürümü kullanıyorsunuzdur. Bu hatanın [JDBC sürücü sürümleri 42.2.15 ve 42.2.16](https://github.com/pgjdbc/pgjdbc/issues/1868)’yı etkilediği bilinmektedir.
   - Güncelleştirmeyi Kasım sonuna kadar tamamlamayı planlıyoruz. Bu arada çalışan bir sürücü sürümü kullanmayı göz önünde bulundurun.
   - Ya da GSS isteğini devre dışı bırakmayı düşünün.  `gssEncMode=disable` gibi bir bağlantı parametresi kullanın.

### <a name="storage-size-reduction"></a>Depolama boyutu azaltma
Depolama boyutu küçültülemez. İstenen depolama boyutuna sahip yeni bir sunucu oluşturmanız, el ile [döküm uygulamanız ve veritabanınızı geri yüklemeniz](./howto-migrate-using-dump-and-restore.md) ve veritabanınızı yeni sunucuya geçirmeniz gerekir.

## <a name="next-steps"></a>Sonraki adımlar
- [Her fiyatlandırma katmanında nelerin kullanılabildiğini](concepts-pricing-tiers.md) anlayın
- [Desteklenen PostgreSQL veritabanı sürümleri](concepts-supported-versions.md) hakkında bilgi edinin
- [Azure Portal kullanarak PostgreSQL Için Azure veritabanı 'nda bir sunucunun nasıl yedeklendiğini ve geri yükleneceğini](howto-restore-server-portal.md) inceleyin
