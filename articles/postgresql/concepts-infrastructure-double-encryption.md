---
title: Altyapı Çift şifreleme-PostgreSQL için Azure veritabanı
description: Hizmet tarafından yönetilen anahtarlarla ikinci bir şifreleme katmanı eklemek için altyapı çift şifrelemesini kullanma hakkında bilgi edinin.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: conceptual
ms.date: 6/30/2020
ms.openlocfilehash: 83635b732318a4ada76d1d71c1ce419cae8b35e9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98630156"
---
# <a name="azure-database-for-postgresql-infrastructure-double-encryption"></a>PostgreSQL için Azure veritabanı altyapısı Çift şifreleme

PostgreSQL için Azure veritabanı, Microsoft 'un yönetilen anahtarlarını kullanarak verileri bekleyen verilerin depolama [şifrelemesini](concepts-security.md#at-rest) kullanır. Yedeklemeler de dahil olmak üzere veriler diskte şifrelenir ve bu şifreleme her zaman açıktır ve devre dışı bırakılamaz. Şifreleme, FIPS 140-2 tarafından doğrulanan şifreleme modülünü ve Azure depolama şifrelemesi için AES 256 bit şifre kullanır.

Altyapı çift şifrelemesi, hizmet tarafından yönetilen anahtarlar kullanılarak ikinci bir şifreleme katmanı ekler. FIPS 140-2 tarafından doğrulanan şifreleme modülünü, farklı bir şifreleme algoritması kullanır. Bu, bekleyen veriler için ek bir koruma katmanı sağlar. Altyapı çift şifrelemede kullanılan anahtar, PostgreSQL için Azure veritabanı hizmeti tarafından da yönetilir. Ek şifreleme katmanı performans etkisine sahip olabileceği için altyapı çift şifrelemesi varsayılan olarak etkinleştirilmemiştir.

> [!NOTE]
> Bu özellik yalnızca PostgreSQL için Azure veritabanı 'nda "Genel Amaçlı" ve "bellek için Iyileştirilmiş" fiyatlandırma katmanlarında desteklenir.

Altyapı katmanı şifrelemesi, depolama cihazına veya ağ kablolarına en yakın katmanda uygulanma avantajına sahiptir. PostgreSQL için Azure veritabanı, hizmet tarafından yönetilen anahtarlar kullanılarak iki şifreleme katmanını uygular. Hizmet katmanında Teknik olarak yine de devam ediyor olsa da, bekleyen verileri depolayan donanıma çok yakın. İsteğe bağlı olarak, sağlanan PostgreSQL sunucusu için [müşteri tarafından yönetilen anahtar](concepts-data-encryption-postgresql.md) kullanarak bekleyen veri şifrelemeyi etkinleştirebilirsiniz.  

Altyapı katmanlarında uygulama, anahtarların çeşitliliğe de sahiptir. Altyapı, makine ve ağların farklı kümelerinin farkında olmalıdır. Bu nedenle, altyapı saldırılarının ve çeşitli donanım ve ağ hatalarından oluşan yarıçapı en aza indirmek için farklı anahtarlar kullanılır. 

> [!NOTE]
> Altyapı çift şifrelemenin kullanılması, ek şifreleme işlemi nedeniyle PostgreSQL için Azure veritabanı sunucusunda performans etkisine sahip olur.

## <a name="benefits"></a>Avantajlar

PostgreSQL için Azure veritabanı 'nın altyapı çift şifrelemesi aşağıdaki avantajları sağlar:

1. **Ek şifreleme uygulaması çeşitlemesi** -donanım tabanlı şifrelemeye yapılan planlı geçiş, yazılım tabanlı uygulamaya ek olarak donanım tabanlı bir uygulama sağlayarak uygulamaları daha fazla zaman ayırarak.
2. **Uygulama hataları** : altyapı katmanında iki şifreleme katmanı, düz metin verileri sunan daha büyük katmanlarda önbelleğe alma veya bellek yönetimi hatalarını korur. Ayrıca, iki katman, genellikle şifreleme uygulamasındaki hatalara karşı da güvence altına alır.

Bunların birleşimi, şifrelemeye saldırmak için kullanılan yaygın tehditlere ve zayıf yönleriyle karşı güçlü koruma sağlar.

## <a name="supported-scenarios-with-infrastructure-double-encryption"></a>Altyapı çift Şifrelemeli desteklenen senaryolar

PostgreSQL için Azure veritabanı tarafından sunulan şifreleme özellikleri birlikte kullanılabilir. Aşağıda kullanabileceğiniz çeşitli senaryoların bir özeti verilmiştir:

|  ##   | Varsayılan şifreleme | Altyapıyı çift şifreleme | Müşteri tarafından yönetilen anahtarları kullanarak veri şifreleme  |
|:------|:------------------:|:--------------------------------:|:--------------------------------------------:|
| 1     | *Evet*              | *Hayır*                             | *Hayır*                                         |
| 2     | *Evet*              | *Evet*                            | *Hayır*                                         |
| 3     | *Evet*              | *Hayır*                             | *Evet*                                        |
| 4     | *Evet*              | *Evet*                            | *Evet*                                        |
|       |                    |                                  |                                              |

> [!Important]
> - Ek altyapı şifreleme katmanı nedeniyle, Senaryo 2 ve 4 ' ün PostgreSQL için Azure veritabanı sunucusunda performans etkisi olacaktır.
> - PostgreSQL için Azure veritabanı için altyapı ikili şifrelemeye yönelik yapılandırmaya yalnızca sunucu oluşturma sırasında izin verilir. Sunucu sağlandıktan sonra depolama şifrelemesini değiştiremezsiniz. Ancak, altyapı çift şifrelemesi ile oluşturulan/olmayan sunucu için müşteri tarafından yönetilen anahtarları kullanarak veri şifrelemeyi etkinleştirebilirsiniz.

## <a name="limitations"></a>Sınırlamalar

PostgreSQL için Azure veritabanı için, hizmet tarafından yönetilen anahtarı kullanarak altyapı Çift şifreleme desteği aşağıdaki sınırlamalara sahiptir:

* Bu işlevselliğe yönelik destek, **genel amaçlı** ve bellek için **iyileştirilmiş** fiyatlandırma katmanlarında sınırlandırılmıştır.
* Bu özellik yalnızca bölge ve sunucularda desteklenir ve 16 TB 'a kadar depolamayı destekler. 16 TB 'a kadar depolamayı destekleyen Azure bölgelerinin listesi için [depolama belgelerine](concepts-pricing-tiers.md#storage)bakın.

    > [!NOTE]
    > - Yukarıda listelenen bölgelerde oluşturulan tüm **Yeni** PostgreSQL sunucuları, müşteri yöneticisi anahtarlarıyla veri şifrelemeyi de destekler. Bu durumda, zaman noktası geri yükleme (ıNR) veya okuma çoğaltmaları aracılığıyla oluşturulan sunucular "yeni" olarak nitelemez.
    > - Sağlanan sunucunuzun 16 TB 'a kadar destekleyip desteklemediğini doğrulamak için, portalda fiyatlandırma katmanı dikey penceresine gidebilir ve depolama kaydırıcısının 16 TB 'a taşınıp taşınamayacağını görebilirsiniz. Kaydırıcıyı yalnızca 4 TB 'a kadar taşıyabiliyorsanız, sunucunuz müşterinin yönettiği anahtarlarla şifrelemeyi desteklemiyor olabilir; Ancak, veriler hizmet tarafından yönetilen anahtarlar kullanılarak her zaman şifrelenir. Sorularınız varsa lütfen öğesine ulaşın AskAzureDBforPostgreSQL@service.microsoft.com .

## <a name="next-steps"></a>Sonraki adımlar

[PostgreSQL Için Azure veritabanı Için altyapı çift şifrelemeyi ayarlamayı](howto-double-encryption.md)öğrenin.
