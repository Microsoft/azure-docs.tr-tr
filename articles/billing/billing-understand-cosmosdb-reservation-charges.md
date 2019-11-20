---
title: Rezervasyon indiriminin Azure Cosmos DB’ye nasıl uygulandığını anlama | Microsoft Docs
description: Rezervasyon indiriminin Azure Cosmos DB’de sağlanan işleme hızına (RU/sn) nasıl uygulandığını öğrenin.
services: cosmos-db
author: rimman
manager: kfile
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/30/2019
ms.author: banders
ms.reviewer: sngun
ms.openlocfilehash: d10418e5095a3331c343f805d2c8385aa4974ffc
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: HT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/01/2019
ms.locfileid: "71701805"
---
# <a name="understand-how-the-reservation-discount-is-applied-to-azure-cosmos-db"></a>Rezervasyon indiriminin Azure Cosmos DB’ye nasıl uygulandığını anlama

Bir Azure Cosmos DB ayrılmış kapasitesi satın almanızın ardından, rezervasyonun öznitelikleriyle ve miktarıyla eşleşen Azure Cosmos DB’ye otomatik olarak rezervasyon indirimi uygulanır. Azure Cosmos DB kaynakları için sağlanan işleme hızı rezervasyon tarafından karşılanır. Yazılım, ağ, depolama veya önceden tanımlanmış kapsayıcı ücretleri karşılanmaz.

## <a name="how-reservation-discount-is-applied"></a>Rezervasyon indiriminin uygulanması

Rezervasyon indirimi “*kullanılmadığı takdirde hakkı kaybedilen*” bir özelliktir. Bu nedenle, herhangi bir saat için eşleşen kaynaklarınız yoksa, o saate ait rezervasyon miktarını kaybedersiniz. Kullanılmayan ayrılmış saatleri devredemezsiniz.

Bir kaynağı kapattığınızda rezervasyon indirimi, belirtilen kapsamdaki başka bir eşleşen kaynağa otomatik olarak uygulanır. Belirtilen kapsamda eşleşen kaynak bulunamazsa, ayrılan saatler *kaybedilir*.

## <a name="reservation-discount-applied-to-azure-cosmos-db-accounts"></a>Azure Cosmos DB hesaplarına uygulanan rezervasyon indirimi

Saniye başına birim (RU/sn) cinsinden [sağlanan işleme hızı](../cosmos-db/request-units.md) için saatlik olarak rezervasyon indirimi uygulanır. Saatin tamamı boyunca çalıştırılmayan Azure Cosmos DB kaynakları için, rezervasyon indirimi rezervasyon öznitelikleriyle eşleşen diğer Cosmos DB kaynaklarına otomatik olarak uygulanır. İndirim, eş zamanlı olarak çalıştırılan Azure Cosmos DB kaynaklarına uygulanabilir. Rezervasyon öznitelikleriyle eşleşen ve saatin tamamı boyunca çalıştırılan Cosmos DB kaynaklarınız yoksa, ilgili saat için rezervasyon indiriminden tam olarak yararlanmazsınız.

İndirimler katmanlıdır. Daha yüksek istek birimleri olan rezervasyonlar daha fazla indirim sağlar.

Rezervasyon alımı, tüm bölgelere bölgesel talebe bağlı fiyatlandırmayla eşdeğer oranda indirim uygular. Her bölgenin rezervasyon indirim oranlarını görmek için bu makaledeki [Bölgeye göre rezervasyon indirimi](#reservation-discount-per-region) bölümüne bakın.

## <a name="reservation-discount-per-region"></a>Bölgeye göre rezervasyon indirimi
Rezervasyon indirimi Azure Cosmos DB işleme hızı maliyetlerine saat bazında uygulanır. Tek abonelik veya kayıtlı/hesap kapsamında uygulanır. Rezervasyon indirimi farklı bölgelerde ölçüm kullanımına aşağıdaki oranlarla uygulanır:

|Ölçüm açıklaması  |Bölge |Oran  |
|---------|---------|---------|
|Azure Cosmos DB - 100 RU/sn/saat - AP Güneydoğu  |  AP Güneydoğu    |   1      |
|Azure Cosmos DB - 100 RU/sn/saat - AP Doğu |   AP Doğu   |    1     |
|Azure Cosmos DB - 100 RU/sn/saat - AV Kuzey|  AV Kuzey       |    1     |
|Azure Cosmos DB - 100 RU/sn/saat - KR Güney|    KR Güney     |     1    |
|Azure Cosmos DB - 100 RU/sn/saat - AV Batı|    AV Batı     |      1   |
|Azure Cosmos DB - 100 RU/sn/saat - KR Orta|   KR Orta    |       1  |
|Azure Cosmos DB - 100 RU/sn/saat - Birleşik Krallık Güney|   Birleşik Krallık Güney      |     1    |
|Azure Cosmos DB - 100 RU/sn/saat - Birleşik Krallık Batı|   Birleşik Krallık Batı      |    1     |
|Azure Cosmos DB - 100 RU/sn/saat - Birleşik Krallık Kuzey |   Birleşik Krallık Kuzey    |     1    |
|Azure Cosmos DB - 100 RU/sn/saat - Birleşik Krallık Güney 2|   Birleşik Krallık Güney 2      |     1    |
|Azure Cosmos DB - 100 RU/sn/saat - ABD Doğu 2|  ABD Doğu 2     |     1    |
|Azure Cosmos DB - 100 RU/sn/saat - ABD Orta Kuzey|   ABD Orta Kuzey      |     1    |
|Azure Cosmos DB - 100 RU/sn/saat - ABD Batı|   ABD Batı      |     1    |
|Azure Cosmos DB - 100 RU/sn/saat - ABD Orta| ABD Orta        |     1    |
|Azure Cosmos DB - 100 RU/sn/saat - ABD Batı 2|   ABD Batı 2      |      1   |
|Azure Cosmos DB - 100 RU/sn/saat - ABD Orta Batı|   ABD Orta Batı      |       1  |
|Azure Cosmos DB - 100 RU/sn/saat - ABD Doğu|   ABD Doğu      |  1       |
|Azure Cosmos DB - 100 RU/sn/saat - Güney Afrika Kuzey|     Güney Afrika Kuzey    |   1      |
|Azure Cosmos DB - 100 RU/sn/saat - Güney Afrika Batı |    Güney Afrika Batı      |    1     |
|Azure Cosmos DB - 100 RU/sn/saat - IN Güney|    IN Güney     |    1,0375    |
|Azure Cosmos DB - 100 RU/sn/saat - CA Doğu|   CA Doğu      |    1.1      |
|Azure Cosmos DB - 100 RU/sn/saat - JA Doğu|   JA Doğu      |    1,125     |
|Azure Cosmos DB - 100 RU/sn/saat - JA Batı|     JA Batı    |   1,125       |
|Azure Cosmos DB - 100 RU/sn/saat - IN Batı|     IN Batı    |    1,1375     |
|Azure Cosmos DB - 100 RU/sn/saat - IN Orta|    IN Orta     |  1,1375       |
|Azure Cosmos DB - 100 RU/sn/saat - AU Doğu|     AU Doğu    |   1,15       |
|Azure Cosmos DB - 100 RU/sn/saat - CA Orta|  CA Orta       |   1.2       |
|Azure Cosmos DB - 100 RU/sn/saat - FR Orta|   FR Orta      |    1,25      |
|Azure Cosmos DB - 100 RU/sn/saat - BR Güney|  BR Güney       |   1,5      |
|Azure Cosmos DB - 100 RU/sn/saat - AU Orta|   AU Orta      |   1,5      |
|Azure Cosmos DB - 100 RU/sn/saat - AU Orta 2| AU Orta 2        |    1,5     |
|Azure Cosmos DB - 100 RU/sn/saat - FR Güney|    FR Güney     |    1,625     |

## <a name="scenarios-that-show-how-the-reservation-discount-is-applied"></a>Rezervasyon indiriminin nasıl uygulandığını gösteren senaryolar

Rezervasyon için aşağıdaki gereksinimleri göz önünde bulundurun:

* Gerekli işleme hızı: 50.000 RU/sn  
* Kullanılan bölgeler: 2

Bu durumda bu iki bölgede isteğe bağlı ücretlerinizin toplamı 100 RU/sn ölçümü için 500 miktarında olur. Toplam RU/sn tüketimi saat başı 100.000’dir.

**Senaryo 1**

Örneğin, ABD Orta Kuzey ve ABD Batı bölgelerine Azure Cosmos DB dağıtmanız gerektiğini varsayalım. Her bölgenin işleme hızı tüketimi 50.000 RU/sn'dir. 100.000 RU/sn’lik bir rezervasyon alımı, isteğe bağlı ücretlerinizi tamamen dengeleyecektir.

Rezervasyonun kapsadığı indirim şu şekilde hesaplanır: işleme hızı tüketimi * bölgenin_rezervasyon_indirimi_oranı. ABD Orta Kuzey ve ABD Batı bölgelerinin rezervasyon indirimi oranı 1’dir. Bu nedenle indirimli toplam RU/sn 100.000 olur. Bu değer şu şekilde hesaplanır: 50.000 * 1 + 50.000 * 1 = 100.000 RU/sn. Normal kullandıkça öde fiyatlarıyla ek ücret ödemeniz gerekmez.

|Ölçüm açıklaması | Bölge |İşleme hızı tüketimi (RU/sn) |RU/sn’ye uygulanan rezervasyon indirimi |
|---------|---------|---------|---------|
|Azure Cosmos DB - 100 RU/sn/saat - ABD Orta Kuzey  |   ABD Orta Kuzey  | 50.000  | 50.000  |
|Azure Cosmos DB - 100 RU/sn/saat - ABD Batı  |  ABD Batı   |  50.000  |  50.000 |

**Senaryo 2**

Örneğin, AU Orta 2 ve FR Güney bölgelerine Azure Cosmos DB dağıtmanız gerektiğini varsayalım. Her bölgenin işleme hızı tüketimi 50.000 RU/sn'dir. 100.000 RU/sn’lik bir rezervasyon alımı aşağıdaki gibi uygulanabilir (ilk olarak AU Orta 2 kullanımının indirimli olduğunu varsayarsak):

|Ölçüm açıklaması | Bölge |İşleme hızı tüketimi (RU/sn) |RU/sn’ye uygulanan rezervasyon indirimi |
|---------|---------|---------|---------|
|Azure Cosmos DB - 100 RU/sn/saat - AU Orta 2  |  AU Orta 2   |  50.000  |  50.000   |
|Azure Cosmos DB - 100 RU/sn/saat - FR Güney  |  FR Güney   |  50.000 |  15.384  |

AU Orta 2 bölgesindeki 50.000 birim kullanımı, faturalandırılabilir (veya normalleştirilmiş) 75.000 RU/sn’lik kullanıma karşılık gelir. Bu değer şu şekilde hesaplanır: işleme hızı tüketimi * o_bölgenin_rezervasyon_indirimi. Hesaplama, 75.000 RU/sn’lik faturalandırılabilir veya normalleştirilmiş kullanıma eşittir. Bu değer şu şekilde hesaplanır: 50.000 * 1,5 = 75.000 RU/sn.

100.000 RU/sn’lik rezervasyon alımı, AU Orta 2’de 75.000 RU/sn’yi dengeler. FR Güney bölgesine 25.000 RU/sn bırakır. Geri kalan 25.000 RU/sn’nin 15.384 RU/sn’si FR Güney bölgesine rezervasyon indirimi olarak uygulanır. İndirim değeri şu şekilde hesaplanır: 25.000 / 1,625 = 15.384 RU/sn. FR Güney bölgesindeki geri kalan 34.616 RU/sn normal kullandıkça öde fiyatlarına göre ücretlendirilir.

Azure faturama sistemi, rezervasyon faturalama avantajını işlenen ve rezervasyon yapılandırmasıyla eşleşen ilk örneğe atayacaktır. Örneğin, bu durumda AU Orta 2 bölgesine atanır.

Faturalama kullanım raporlarında Azure rezervasyonlarınızın uygulanmasını anlamak ve görüntülemek için bkz. [Azure rezervasyon kullanımınızı anlama](../billing/billing-understand-reserved-instance-usage-ea.md).

## <a name="need-help-contact-us"></a>Yardıma mı ihtiyacınız var? Bize ulaşın.

Sorularınız varsa ya da yardıma gereksinim duyuyorsanız [destek isteği oluşturun](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Sonraki adımlar

Azure rezervasyonları hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:

* [Azure rezervasyonları nedir?](../billing/billing-save-compute-costs-reservations.md)  
* [Azure Cosmos DB ayrılmış kapasitesiyle Azure Cosmos DB kaynakları için önceden ödeme yapma](../cosmos-db/cosmos-db-reserved-capacity.md)  
* [Azure SQL Veritabanı ayrılmış kapasitesi ile SQL Veritabanı işlem kaynakları için ön ödeme yapma](../sql-database/sql-database-reserved-capacity.md)  
* [Azure rezervasyonlarını yönetme](../billing/billing-manage-reserved-vm-instance.md)  
* [Kullandıkça Öde aboneliğiniz için rezervasyon kullanımını anlama](../billing/billing-understand-reserved-instance-usage.md)  
* [Kurumsal kaydınız için rezervasyon kullanımını anlama](../billing/billing-understand-reserved-instance-usage-ea.md)
* [CSP abonelikleri için rezervasyon kullanımını anlama](https://docs.microsoft.com/partner-center/azure-reservations)
