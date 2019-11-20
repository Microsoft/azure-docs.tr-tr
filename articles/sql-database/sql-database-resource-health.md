---
title: Veritabanı sistem durumunu izlemek için Azure Kaynak Durumu kullanma
description: SQL veritabanı sistem durumunu izlemek için Azure Kaynak Durumu kullanın, bir Azure sorunu SQL kaynaklarınızı etkilediğinde desteği tanılamanıza ve destek almanıza yardımcı olur.
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: jrasnik, carlrab
ms.date: 02/26/2019
ms.openlocfilehash: 1cf14c9e133b7e6e3e0b5219eb9e16bd3a0178dc
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73821166"
---
# <a name="use-resource-health-to-troubleshoot-connectivity-for-azure-sql-database"></a>Azure SQL veritabanı ile ilgili bağlantı sorunlarını gidermek için Kaynak Durumu kullanma

## <a name="overview"></a>Genel Bakış

SQL veritabanı için [kaynak durumu](../service-health/resource-health-overview.md#get-started) , SQL kaynaklarınızı etkileyen bir Azure sorunu hakkında tanılamanıza ve destek almanıza yardımcı olur. Kaynaklarınızın güncel ve geçmiş durumu hakkında bilgiler sağlar ve sorunları azaltmaya yardımcı olur. Kaynak Durumu, Azure hizmet sorunları ile ilgili yardıma ihtiyacınız olduğunda teknik destek sağlar.

![Genel Bakış](./media/sql-database-resource-health/sql-resource-health-overview.jpg)

## <a name="health-checks"></a>Sistem durumu denetimleri

Kaynak Durumu, kaynak üzerinde oturum açma işlemlerini ve başarısız olduğunu inceleyerek SQL kaynağınızın sistem durumunu belirler. Şu anda, SQL DB kaynağınız için Kaynak Durumu yalnızca sistem hatası nedeniyle oturum açma başarısızlıklarını inceler ve Kullanıcı hatası değildir. Kaynak Durumu durumu her 1-2 dakikada bir güncelleştirilir.

## <a name="health-states"></a>Sağlık durumları

### <a name="available"></a>Kullanılabilir

**Mevcut** durumu, SQL kaynağınızın sistem hataları nedeniyle kaynak durumu oturum açma hatalarının algılanmadığı anlamına gelir.

![Kullanılabilir](./media/sql-database-resource-health/sql-resource-health-available.jpg)

### <a name="degraded"></a>Düzeyi düşürüldü

Durumun **Düşürüldü** olması, Kaynak Durumu'nun oturum açma işlemlerinin çoğunun başarılı olduğunu ancak arada başarısız girişler de olduğunu gösterir. Bunlar, büyük olasılıkla geçici oturum açma hatalardır. Geçici oturum açma hatalarından kaynaklanan bağlantı sorunlarının etkisini azaltmak için lütfen kodunuzda [yeniden deneme mantığını](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors) uygulayın.

![Düzeyi düşürüldü](./media/sql-database-resource-health/sql-resource-health-degraded.jpg)

### <a name="unavailable"></a>Kullanılamaz

**Kullanım dışı** durumu kaynak durumu, SQL kaynağınız için tutarlı oturum açma hatalarının algılandığı anlamına gelir. Kaynağınız uzun bir süre boyunca bu durumda kalırsa lütfen desteğe başvurun.

![Kullanılamaz](./media/sql-database-resource-health/sql-resource-health-unavailable.jpg)

### <a name="unknown"></a>Bilinmiyor

**Bilinmeyen** sistem durumu, kaynak durumu 10 dakikadan uzun süre boyunca bu kaynakla ilgili bilgi almadığını gösterir. Bu durum kaynağın durumunun kesin bir göstergesi olmamasına karşın, sorun giderme sürecinde önemli bir veri noktasıdır. Kaynak beklendiği gibi çalışıyorsa, kaynağın durumu birkaç dakika sonra kullanılabilir olarak değişir. Kaynakla ilgili sorun yaşıyorsanız, bilinmeyen sistem durumu, platformda bir olayın kaynağı etkilemesini önerebilir.

![Bilinmiyor](./media/sql-database-resource-health/sql-resource-health-unknown.jpg)

## <a name="historical-information"></a>Geçmiş bilgileri

Kaynak Durumu sistem durumu geçmişi bölümünde 14 güne kadar sistem durumu geçmişine erişebilirsiniz. Bu bölümde, Kaynak Durumu tarafından bildirilen aşağı sürelerle ilgili kapalı kalma süresi (varsa) de bulunur. Şu anda Azure, SQL veritabanı kaynağınız için iki dakikalık ayrıntı düzeyiyle kapalı kalma süresini gösteriyor. Gerçek kapalı kalma süresi büyük olasılıkla bir dakikadan kısadır (ortalama 8 sn).

### <a name="downtime-reasons"></a>Kesinti süresi nedenleri

SQL veritabanınız kapalı kalma yaşdığında, bir neden belirlenmesi için analiz gerçekleştirilir. Kullanılabilir olduğunda, Kaynak Durumu sistem durumu geçmişi bölümünde kesinti süresi nedeni raporlanır. Kapalı kalma süresinin nedenleri genellikle bir olaydan 30 dakika sonra yayımlanır.

#### <a name="planned-maintenance"></a>Planlı bakım

Azure altyapısı, veri merkezindeki donanım veya yazılım bileşenlerinin planlı bakımını düzenli olarak gerçekleştirir. Veritabanı bakım sırasında kaldığında SQL, bazı mevcut bağlantıları sonlandırabilir ve yenilerini reddedebilir. Planlı bakım sırasında karşılaşılan oturum açma hatalarıyla genellikle geçici ve [yeniden deneme mantığı](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors) etkisini azaltmaya yardımcı olur. Oturum açma hatalarıyla karşılaşmaya devam ederseniz lütfen desteğe başvurun.

#### <a name="reconfiguration"></a>Yeniden yapılandırma

Yeniden yapılandırma geçici koşullar olarak değerlendirilir ve zaman zaman beklenir. Bu olaylar, Yük Dengeleme veya yazılım/donanım hatalarıyla tetiklenebilir. Bulut veritabanına bağlanan tüm istemci üretim uygulamaları, bu durumların azaltılmasına yardımcı olacağından ve genellikle hataları son kullanıcıya saydam hale getirmek için sağlam bir bağlantı [yeniden deneme mantığı](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors)uygulamalıdır.

## <a name="next-steps"></a>Sonraki adımlar

- [Geçici hatalar için yeniden deneme mantığı](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors) hakkında daha fazla bilgi edinin
- [SQL bağlantı hatalarını giderme, tanılama ve önleme](./sql-database-connectivity-issues.md)
- [Kaynak durumu uyarılarını yapılandırma](../service-health/resource-health-alert-arm-template-guide.md) hakkında daha fazla bilgi edinin
- [Kaynak durumu](../service-health/resource-health-overview.md) bir genel bakış alın
- [Kaynak Durumu SSS](../service-health/resource-health-faq.md)
