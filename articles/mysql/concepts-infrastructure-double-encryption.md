---
title: Altyapı Çift şifreleme-MySQL için Azure veritabanı
description: Hizmet tarafından yönetilen anahtarlarla ikinci bir şifreleme katmanı eklemek için altyapı çift şifrelemesini kullanma hakkında bilgi edinin.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 6/30/2020
ms.openlocfilehash: e9182a2a0b88f85af5305f5794fec2ffe7935701
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98631742"
---
# <a name="azure-database-for-mysql-infrastructure-double-encryption"></a>MySQL için Azure veritabanı altyapısı Çift şifreleme

MySQL için Azure veritabanı, Microsoft 'un yönetilen anahtarlarını kullanarak verileri bekleyen verilerin depolama [şifrelemesini](concepts-security.md#at-rest) kullanır. Yedeklemeler de dahil olmak üzere veriler diskte şifrelenir ve bu şifreleme her zaman açıktır ve devre dışı bırakılamaz. Şifreleme, FIPS 140-2 tarafından doğrulanan şifreleme modülünü ve Azure depolama şifrelemesi için AES 256 bit şifre kullanır.

Altyapı çift şifrelemesi, hizmet tarafından yönetilen anahtarlar kullanılarak ikinci bir şifreleme katmanı ekler. FIPS 140-2 tarafından doğrulanan şifreleme modülünü, farklı bir şifreleme algoritması kullanır. Bu, bekleyen veriler için ek bir koruma katmanı sağlar. Altyapı çift şifrelemede kullanılan anahtar, MySQL için Azure veritabanı hizmeti tarafından da yönetilir. Ek şifreleme katmanı performans etkisine sahip olabileceği için altyapı çift şifrelemesi varsayılan olarak etkinleştirilmemiştir.

> [!NOTE]
> Bu özellik yalnızca MySQL için Azure veritabanı 'nda "Genel Amaçlı" ve "bellek için Iyileştirilmiş" fiyatlandırma katmanlarında desteklenir.

Altyapı katmanı şifrelemesi, depolama cihazına veya ağ kablolarına en yakın katmanda uygulanma avantajına sahiptir. MySQL için Azure veritabanı, hizmet tarafından yönetilen anahtarlar kullanılarak iki şifreleme katmanını uygular. Hizmet katmanında Teknik olarak yine de devam ediyor olsa da, bekleyen verileri depolayan donanıma çok yakın. İsteğe bağlı olarak, sağlanan MySQL sunucusu için [müşteri tarafından yönetilen anahtarı](concepts-data-encryption-mysql.md) kullanarak bekleyen veri şifrelemeyi etkinleştirebilirsiniz. 

Altyapı katmanlarında uygulama, anahtarların çeşitliliğe de sahiptir. Altyapı, makine ve ağların farklı kümelerinin farkında olmalıdır. Bu nedenle, altyapı saldırılarının ve çeşitli donanım ve ağ hatalarından oluşan yarıçapı en aza indirmek için farklı anahtarlar kullanılır. 

> [!NOTE]
> Altyapı çift şifrelemenin kullanılması, ek şifreleme işlemi nedeniyle MySQL için Azure veritabanı sunucusu üzerinde performans etkisine sahip olacaktır.

## <a name="benefits"></a>Avantajlar

MySQL için Azure veritabanı 'nın altyapı çift şifrelemesi aşağıdaki avantajları sağlar:

1. **Ek şifreleme uygulaması çeşitlemesi** -donanım tabanlı şifrelemeye yapılan planlı geçiş, yazılım tabanlı uygulamaya ek olarak donanım tabanlı bir uygulama sağlayarak uygulamaları daha fazla zaman ayırarak.
2. **Uygulama hataları** : altyapı katmanında iki şifreleme katmanı, düz metin verileri sunan daha büyük katmanlarda önbelleğe alma veya bellek yönetimi hatalarını korur. Ayrıca, iki katman, genellikle şifreleme uygulamasındaki hatalara karşı da güvence altına alır.

Bunların birleşimi, şifrelemeye saldırmak için kullanılan yaygın tehditlere ve zayıf yönleriyle karşı güçlü koruma sağlar.

## <a name="supported-scenarios-with-infrastructure-double-encryption"></a>Altyapı çift Şifrelemeli desteklenen senaryolar

MySQL için Azure veritabanı tarafından sunulan şifreleme özellikleri birlikte kullanılabilir. Aşağıda kullanabileceğiniz çeşitli senaryoların bir özeti verilmiştir:

|  ##   | Varsayılan şifreleme | Altyapıyı çift şifreleme | Müşteri tarafından yönetilen anahtarları kullanarak veri şifreleme  |
|:------|:------------------:|:--------------------------------:|:--------------------------------------------:|
| 1     | *Evet*              | *Hayır*                             | *Hayır*                                         |
| 2     | *Evet*              | *Evet*                            | *Hayır*                                         |
| 3     | *Evet*              | *Hayır*                             | *Evet*                                        |
| 4     | *Evet*              | *Evet*                            | *Evet*                                        |
|       |                    |                                  |                                              |

> [!Important]
> - 2. senaryo, ek altyapı şifreleme katmanı nedeniyle MySQL için Azure veritabanı sunucusu için iş yükü türüne göre yüzde 5-10 ' luk bir aktarım hızını ortaya çıkarabilir.
> - MySQL için Azure veritabanı için altyapı çift şifrelemeye yönelik yapılandırma yalnızca sunucu oluşturma sırasında kullanılabilir. Sunucu sağlandıktan sonra depolama şifrelemesini değiştiremezsiniz. Ancak, altyapı çift şifrelemesi ile oluşturulan/olmayan sunucu için müşteri tarafından yönetilen anahtarları kullanarak veri şifrelemeyi etkinleştirebilirsiniz.

## <a name="limitations"></a>Sınırlamalar

MySQL için Azure veritabanı 'nda, hizmet tarafından yönetilen anahtar kullanılarak altyapı Çift şifreleme desteği aşağıdaki sınırlamalara sahiptir:

* Bu işlevselliğe yönelik destek, **genel amaçlı** ve bellek için **iyileştirilmiş** fiyatlandırma katmanlarında sınırlandırılmıştır.
* Bu özellik yalnızca bölge ve sunucularda desteklenir ve 16 TB 'a kadar depolamayı destekler. 16 TB 'a kadar depolamayı destekleyen Azure bölgelerinin listesi için [depolama belgelerine](concepts-pricing-tiers.md#storage)bakın.

    > [!NOTE]
    > - Yukarıda listelenen bölgelerde oluşturulan tüm **Yeni** MySQL sunucuları, müşteri yöneticisi anahtarlarıyla veri şifrelemeyi de destekler. Bu durumda, zaman noktası geri yükleme (ıNR) veya okuma çoğaltmaları aracılığıyla oluşturulan sunucular "yeni" olarak nitelemez.
    > - Sağlanan sunucunuzun 16 TB 'a kadar destekleyip desteklemediğini doğrulamak için, portalda fiyatlandırma katmanı dikey penceresine gidebilir ve depolama kaydırıcısının 16 TB 'a taşınıp taşınamayacağını görebilirsiniz. Kaydırıcıyı yalnızca 4 TB 'a kadar taşıyabiliyorsanız, sunucunuz müşterinin yönettiği anahtarlarla şifrelemeyi desteklemiyor olabilir; Ancak, veriler hizmet tarafından yönetilen anahtarlar kullanılarak her zaman şifrelenir. Sorularınız varsa lütfen öğesine ulaşın AskAzureDBforMySQL@service.microsoft.com .

## <a name="next-steps"></a>Sonraki adımlar

[MySQL Için Azure veritabanı Için altyapı çift şifrelemeyi ayarlamayı](howto-double-encryption.md)öğrenin.
