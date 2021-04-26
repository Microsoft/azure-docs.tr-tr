---
title: Yapılandırma seçenekleri – Hyperscale (Citus)-PostgreSQL için Azure veritabanı
description: Düğüm hesaplama, depolama ve bölgeler dahil olmak üzere hiper ölçek (Citus) sunucu grubu seçenekleri.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.custom: references_regions
ms.date: 04/07/2021
ms.openlocfilehash: 1dd0666c2946896ed324fb3986bb7946890b73de
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107388712"
---
# <a name="azure-database-for-postgresql--hyperscale-citus-configuration-options"></a>PostgreSQL için Azure veritabanı – Hyperscale (Citus) yapılandırma seçenekleri

## <a name="compute-and-storage"></a>İşlem ve depolama
 
İşlem ve depolama ayarlarını, çalışan düğümleri ve bir hiper ölçek (Citus) sunucu grubundaki düzenleyici düğümü için bağımsız olarak seçebilirsiniz.  İşlem kaynakları, temel alınan donanımın mantıksal CPU 'sunu temsil eden sanal çekirdekler olarak sağlanır. Sağlama için depolama boyutu, Hiperscale (Citus) sunucu grubunuzdaki düzenleyici ve çalışan düğümlerinin kullanabildiği kapasiteyi ifade eder. Depolama veritabanı dosyalarını, geçici dosyaları, işlem günlüklerini ve Postgres sunucu günlüklerini içerir.

### <a name="standard-tier"></a>Standart katmanı
 
| Kaynak              | Çalışan düğümü           | Düzenleyici düğümü      |
|-----------------------|-----------------------|-----------------------|
| İşlem, sanal çekirdekler       | 4, 8, 16, 32, 64      | 4, 8, 16, 32, 64      |
| VCore başına bellek, GiB | 8                     | 4                     |
| Depolama boyutu, TiB     | 0,5, 1, 2             | 0,5, 1, 2             |
| Depolama türü          | Genel amaçlı (SSD) | Genel amaçlı (SSD) |
| IOPS                  | En fazla 3 ıOPS/GiB      | En fazla 3 ıOPS/GiB      |

Tek bir hiper ölçek (Citus) düğümündeki Toplam RAM miktarı, seçilen sayıda sanal çekirdek temel alır.

| Sanal çekirdek | Bir çalışan düğümü, GiB RAM | Düzenleyici düğümü, GiB RAM |
|--------|--------------------------|---------------------------|
| 4      | 32                       | 16                        |
| 8      | 64                       | 32                        |
| 16     | 128                      | 64                        |
| 32     | 256                      | 128                       |
| 64     | 432                      | 256                       |

Sağladığınız toplam depolama miktarı, her çalışan ve düzenleyici düğümü için kullanılabilen g/ç kapasitesini de tanımlar.

| Depolama boyutu, TiB | Maksimum ıOPS |
|-------------------|--------------|
| 0,5               | 1536        |
| 1                 | 3072        |
| 2                 | 6.148        |

Tüm Hiperscale (Citus) kümesi için, toplanan ıOPS aşağıdaki değerlere göre çalışır:

| Çalışan düğümleri | 0,5 TiB, toplam ıOPS | 1 TiB, toplam ıOPS | 2 TiB, toplam ıOPS |
|--------------|---------------------|-------------------|-------------------|
| 2            | 3072               | 6144             | 12.296            |
| 3            | 4.608               | 9.216             | 18.444            |
| 4            | 6144               | 12.288            | 24.592            |
| 5            | 7.680               | 15.360            | 30.740            |
| 6            | 9.216               | 18.432            | 36.888            |
| 7            | 10.752              | 21.504            | 43.036            |
| 8            | 12.288              | 24.576            | 49.184            |
| 9            | 13.824              | 27.648            | 55.332            |
| 10           | 15.360              | 30.720            | 61.480            |
| 11           | 16.896              | 33.792            | 67.628            |
| 12           | 18.432              | 36.864            | 73.776            |
| 13           | 19.968              | 39.936            | 79.924            |
| 14           | 21.504              | 43.008            | 86.072            |
| 15           | 23.040              | 46.080            | 92.220            |
| 16           | 24.576              | 49.152            | 98.368            |
| 17           | 26.112              | 52.224            | 104.516           |
| 18           | 27.648              | 55.296            | 110.664           |
| 19           | 29.184              | 58.368            | 116.812           |
| 20           | 30.720              | 61.440            | 122.960           |

### <a name="basic-tier-preview"></a>Temel katman (Önizleme)

> [!IMPORTANT]
> Hiperscale (Citus) temel katmanı Şu anda önizlemededir.  Önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir.
>
> Diğer yeni özelliklerin bir listesini, [hiper ölçek için Önizleme özellikleri (Citus)](hyperscale-preview-features.md)sayfasına bakabilirsiniz.

Hyperscale (Citus) [temel katmanı](concepts-hyperscale-tiers.md) , tek bir düğümü olan bir sunucu grubudur.  Koordinatör ve çalışan düğümleri arasında ayrım olmadığından işlem ve depolama kaynaklarını seçmek daha az karmaşıktır.

| Kaynak              | Kullanılabilir seçenekler     |
|-----------------------|-----------------------|
| İşlem, sanal çekirdekler       | 2, 4, 8               |
| VCore başına bellek, GiB | 4                     |
| Depolama boyutu, GiB     | 128, 256, 512         |
| Depolama türü          | Genel amaçlı (SSD) |
| IOPS                  | En fazla 3 ıOPS/GiB      |

Tek bir hiper ölçek (Citus) düğümündeki Toplam RAM miktarı, seçilen sayıda sanal çekirdek temel alır.

| Sanal çekirdek | GiB RAM |
|--------|---------|
| 2      | 8       |
| 4      | 16      |
| 8      | 32      |

Sağladığınız toplam depolama miktarı, temel katman düğümü için kullanılabilen g/ç kapasitesini de tanımlar.

| Depolama boyutu, GiB | Maksimum ıOPS |
|-------------------|--------------|
| 128               | 384          |
| 256               | 768          |
| 512               | 1536        |

## <a name="regions"></a>Bölgeler
Hiper ölçek (Citus) sunucu grupları aşağıdaki Azure bölgelerinde kullanılabilir:

* Kuzey ve Güney Amerika: 
    * Güney Brezilya
    * Orta Kanada
    * Central US
    * Doğu ABD *
    * Doğu ABD 2
    * Orta Kuzey ABD
    * Batı ABD 2
* Asya Pasifik:
    * Doğu Avustralya
    * Doğu Japonya
    * Güney Kore - Orta
    * Güneydoğu Asya
* 'Ya
    * Orta Fransa
    * Kuzey Avrupa
    * Güney Birleşik Krallık
    * West Europe

( \* = [Önizleme özelliklerini](hyperscale-preview-features.md)destekler)

Bu bölgelerden bazıları başlangıçta tüm Azure aboneliklerinde etkinleştirilmemiş olabilir. Yukarıdaki listeden bir bölge kullanmak ve bunu aboneliğinizde görmezseniz veya bu listede olmayan bir bölge kullanmak istiyorsanız, bir [destek isteği](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)açın.

## <a name="pricing"></a>Fiyatlandırma
En güncel fiyatlandırma bilgileri için bkz. hizmet [fiyatlandırma sayfası](https://azure.microsoft.com/pricing/details/postgresql/).
İstediğiniz yapılandırmanın maliyetini görmek için [Azure Portal](https://portal.azure.com/#create/Microsoft.PostgreSQLServer) , seçtiğiniz seçeneklere göre **yapılandırma** sekmesindeki aylık maliyeti gösterir. Azure aboneliğiniz yoksa, tahmini bir fiyat almak için Azure Fiyatlandırma hesaplayıcısı ' nı kullanabilirsiniz. [Azure Fiyatlandırma Hesaplayıcı](https://azure.microsoft.com/pricing/calculator/) Web sitesinde, **öğe Ekle**' yi seçin, **veritabanları** kategorisini genişletin ve ardından seçenekleri özelleştirmek Için **PostgreSQL Için Azure veritabanı – hiper ölçek (Citus)** öğesini seçin.
 
## <a name="next-steps"></a>Sonraki adımlar
[Portalda bir Hyperscale (Citus) sunucu grubu oluşturmayı](quickstart-create-hyperscale-portal.md)öğrenin.
