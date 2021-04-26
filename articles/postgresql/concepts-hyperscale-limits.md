---
title: Sınırlar ve sınırlamalar – hiper ölçek (Citus)-PostgreSQL için Azure veritabanı
description: Hiper ölçek (Citus) sunucu grupları için geçerli sınırlar
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: cfd57c60c4bfd60976eb28822c696412cabda212
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "107023995"
---
# <a name="azure-database-for-postgresql--hyperscale-citus-limits-and-limitations"></a>PostgreSQL için Azure veritabanı – Hyperscale (Citus) sınırları ve sınırlamaları

Aşağıdaki bölümde, hiper ölçek (Citus) hizmetindeki kapasite ve işlevsel sınırlar açıklanmaktadır.

## <a name="maximum-connections"></a>En fazla bağlantı sayısı

Her PostgreSQL bağlantısı (hatta boş olanlar) en az 10 MB bellek kullanır, bu nedenle eşzamanlı bağlantıların sınırlandırması önemlidir. Düğümlerin sağlıklı kalmasını sağlamak için seçtiğimiz sınırlar şunlardır:

* Düzenleyici düğümü
   * En fazla bağlantı: 300
   * En fazla kullanıcı bağlantısı: 297
* Çalışan düğümü
   * En fazla bağlantı: 600
   * En fazla kullanıcı bağlantısı: 597

> [!NOTE]
> [Önizleme özelliklerinin](hyperscale-preview-features.md) etkinleştirildiği bir sunucu grubunda, düzenleyiciye yönelik bağlantı sınırları biraz farklıdır:
>
> * Düzenleyici düğüm en fazla bağlantıları
>    * 0-3 sanal çekirdekler için 300
>    * 4-15 sanal çekirdekler için 500
>    * 16 + sanal çekirdekler için 1000

Bu limitlerin ötesine bağlanma denemeleri hata vererek başarısız olur. Sistem, izleme düğümleri için üç bağlantı ayırır. bu nedenle, bağlantı toplamı, Kullanıcı sorguları için en az üç bağlantı mevcuttur.

### <a name="connection-pooling"></a>Bağlantı havuzu

Yeni bağlantıların kurulması zaman alır. Bu, çok sayıda kısa süreli bağlantı isteyen birçok uygulama için geçerlidir. Boştaki işlemleri azaltmak ve var olan bağlantıları yeniden kullanmak için hem bağlantı havuzlayıcı kullanılması önerilir. Daha fazla bilgi edinmek için [Blog gönderimizi](https://techcommunity.microsoft.com/t5/azure-database-for-postgresql/not-all-postgres-connection-pooling-is-equal/ba-p/825717)ziyaret edin.

Kendi bağlantı havuzlarınızı çalıştırabilir veya Azure tarafından yönetilen PgBouncer kullanabilirsiniz.

#### <a name="managed-pgbouncer-preview"></a>Managed PgBouncer (Önizleme)

> [!IMPORTANT]
> Hiperscale (Citus) ile yönetilen PgBouncer bağlantı havuzlayıcı Şu anda önizleme aşamasındadır. Önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir.
>
> Diğer yeni özelliklerin bir listesini, [hiper ölçek için Önizleme özellikleri (Citus)](hyperscale-preview-features.md)sayfasına bakabilirsiniz.

PgBouncer gibi bağlantı havuzlayıcılar, daha fazla istemcinin aynı anda düzenleyici düğümüne bağlanmasına izin verir. Uygulamalar havuza bağlanır ve havuzlayıcı komutları hedef veritabanına geçirir.

İstemciler PgBouncer aracılığıyla bağlandığında, veritabanında etkin şekilde çalışabilecek bağlantı sayısı değişmez. Bunun yerine, PgBouncer, bağlantıları daha fazla sıraya alır ve veritabanı hazırsanız çalıştırır.

Hiper ölçek (Citus) artık sunucu grupları için bir PgBouncer (önizlemede) için yönetilen bir örnek sunmaktadır. En fazla 2.000 eşzamanlı istemci bağlantısını destekler.
PgBouncer üzerinden bağlanmak için şu adımları izleyin:

1. Azure portal sunucu grubunuzun **bağlantı dizeleri** sayfasına gidin.
2. CheckBox **Pgbouncer bağlantı dizelerini** etkinleştirin. (Listelenen bağlantı dizeleri değişecektir.)
3. Yeni dizeyle bağlanacak istemci uygulamalarını güncelleştirin.

## <a name="storage-scaling"></a>Depolama Ölçeklendirmesi

Düzenleyici ve çalışan düğümlerinde depolamanın ölçeği ölçeklendirilebilir (artırılabilir), ancak ölçeği azalabilir (azaltılmış).

## <a name="storage-size"></a>Depolama boyutu

Koordinatör ve çalışan düğümlerinde en fazla 2 TiB depolama desteklenir. Düğüm ve küme boyutları için [Yukarıdaki](concepts-hyperscale-configuration-options.md#compute-and-storage) kullanılabilir depolama SEÇENEKLERINE ve IOPS hesaplamasına bakın.

## <a name="database-creation"></a>Veritabanı oluşturma

Azure portal, hiper ölçek (Citus) sunucu grubu başına tam olarak bir veritabanına bağlanmak için kimlik bilgileri sağlar `citus` . Şu anda başka bir veritabanı oluşturulmasına izin verilmez ve CREATE DATABASE komutu bir hata vererek başarısız olur.

## <a name="columnar-storage"></a>Sütunlu depolama

Hiperscale (Citus) Şu anda [sütunlu tablolarla](concepts-hyperscale-columnar.md)bu sınırlamalara sahiptir:

* Sıkıştırma, bellekte değil, diskte
* Yalnızca Append (GÜNCELLEŞTIRME/SILME desteği yok)
* Boşluk geri kazanma (örneğin, geri alınmış işlemler yine de disk alanı tüketiyor olabilir)
* Dizin desteği, Dizin taraması veya bit eşlem Dizin taramaları yok
* Hiçbir tidtaramalar yok
* Örnek tarama yok
* BILDIRIM desteği yok (büyük değerler satır içi desteklenir)
* ÇAKıŞMA deyimleri ÜZERINDE desteklenmez (hiçbir hedef belirtilmemiş hiçbir şey olmayan eylemler hariç).
* Demet kilitleri desteği yok (SELECT... PAYLAŞMA IÇIN... SEÇENEĞINI BELIRLEYIN. GÜNCELLEŞTIRME IÇIN)
* Seri hale getirilebilir yalıtım düzeyi desteği yok
* PostgreSQL için destek sunucu sürümleri yalnızca 12 +
* Yabancı anahtarlar, benzersiz kısıtlamalar veya dışlama kısıtlamaları desteği yoktur
* Mantıksal kod çözme desteği yok
* Düğüm içi paralel taramalar desteği yok
* SONRASıNDA için destek yok... HER satır tetikleyicisi IÇIN
* GÜNLÜĞE EDILMEMIŞ sütunlu tablolar yok
* GEÇICI sütunlu tablo yok

## <a name="next-steps"></a>Sonraki adımlar

[Portalda bir Hyperscale (Citus) sunucu grubu oluşturmayı](quickstart-create-hyperscale-portal.md)öğrenin.
