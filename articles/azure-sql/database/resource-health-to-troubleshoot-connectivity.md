---
title: Veritabanı sistem durumunu izlemek için Azure Kaynak Durumu kullanma
description: Azure SQL veritabanı ve Azure SQL yönetilen örnek sistem durumunu izlemek için Azure Kaynak Durumu kullanın, bir Azure sorunu SQL kaynaklarınızı etkilediğinde desteği tanılamanıza ve destek almanıza yardımcı olur.
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: wiassaf, sstein
ms.date: 03/24/2021
ms.openlocfilehash: 4434c796a41126444b8785440427123e88e0b528
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105107797"
---
# <a name="use-resource-health-to-troubleshoot-connectivity-for-azure-sql-database-and-azure-sql-managed-instance"></a>Azure SQL veritabanı ve Azure SQL yönetilen örneği ile ilgili bağlantı sorunlarını gidermek için Kaynak Durumu kullanma
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure SQL veritabanı ve Azure SQL yönetilen örneği için [kaynak durumu](../../service-health/resource-health-overview.md#get-started) , SQL kaynaklarınızı etkileyen bir Azure sorunu olduğunda tanılamanıza ve destek almanıza yardımcı olur. Kaynaklarınızın güncel ve geçmiş durumu hakkında bilgiler sağlar ve sorunları azaltmaya yardımcı olur. Kaynak Durumu, Azure hizmeti sorunlarıyla ilgili yardıma ihtiyacınız olduğunda teknik destek sağlar.

![Genel Bakış](./media/resource-health-to-troubleshoot-connectivity/sql-resource-health-overview.jpg)

## <a name="health-checks"></a>Sistem durumu denetimleri

Kaynak Durumu, kaynak üzerinde oturum açma işlemlerini ve başarısız olduğunu inceleyerek SQL kaynağınızın sistem durumunu belirler. Şu anda, SQL veritabanı kaynağınız için Kaynak Durumu yalnızca sistem hatası nedeniyle oturum açma hataları incelenir ve Kullanıcı hatası değildir. Kaynak Durumu durumu her 1 ile 2 dakikada bir güncelleştirilir.

## <a name="health-states"></a>Sağlık durumları

### <a name="available"></a>Kullanılabilir

**Mevcut** durumu, SQL kaynağınızın sistem hataları nedeniyle kaynak durumu oturum açma hataları algılamadığı anlamına gelir.

![Kullanılabilir](./media/resource-health-to-troubleshoot-connectivity/sql-resource-health-available.jpg)

### <a name="degraded"></a>Düzeyi düşürüldü

Durumun **Düşürüldü** olması, Kaynak Durumu'nun oturum açma işlemlerinin çoğunun başarılı olduğunu ancak arada başarısız girişler de olduğunu gösterir. Bunlar, büyük olasılıkla geçici oturum açma hatalardır. Geçici oturum açma hatalarından kaynaklanan bağlantı sorunlarının etkisini azaltmak için kodunuzda [yeniden deneme mantığını](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors) uygulayın.

![Düzeyi düşürüldü](./media/resource-health-to-troubleshoot-connectivity/sql-resource-health-degraded.jpg)

### <a name="unavailable"></a>Kullanılamaz

**Kullanım dışı** durumu kaynak durumu, SQL kaynağınız için tutarlı oturum açma hatalarının algılandığı anlamına gelir. Kaynağınız uzun bir süre boyunca bu durumda kalırsa desteğe başvurun.

![Kullanılamaz](./media/resource-health-to-troubleshoot-connectivity/sql-resource-health-unavailable.jpg)

### <a name="unknown"></a>Bilinmiyor

**Bilinmeyen** sistem durumu, kaynak durumu 10 dakikadan uzun süre boyunca bu kaynakla ilgili bilgi almadığını gösterir. Bu durum kaynağın durumunun kesin bir göstergesi olmamasına karşın, sorun giderme sürecinde önemli bir veri noktasıdır. Kaynak beklendiği gibi çalışıyorsa, kaynağın durumu birkaç dakika sonra kullanılabilir olarak değişir. Kaynakla ilgili sorun yaşıyorsanız, bilinmeyen sistem durumu, platformda bir olayın kaynağı etkilemesini önerebilir.

![Bilinmiyor](./media/resource-health-to-troubleshoot-connectivity/sql-resource-health-unknown.jpg)

## <a name="historical-information"></a>Geçmiş bilgileri

Kaynak Durumu sistem durumu geçmişi bölümünde 14 güne kadar sistem durumu geçmişine erişebilirsiniz. Bu bölümde, Kaynak Durumu tarafından bildirilen aşağı sürelerle ilgili kapalı kalma süresi (varsa) de bulunur. Şu anda Azure, veritabanı kaynağınız için kapalı kalma süresini iki dakikalık bir ayrıntı düzeyinde gösterir. Gerçek kapalı kalma süresi büyük olasılıkla bir dakikadan kısadır. Ortalama 8 saniyedir.

### <a name="downtime-reasons"></a>Kesinti süresi nedenleri

Veritabanınız kesinti yaşandığında, bir neden belirlenmesi için analiz gerçekleştirilir. Kullanılabilir olduğunda, Kaynak Durumu sistem durumu geçmişi bölümünde kesinti süresi nedeni raporlanır. Kesinti nedenleri genellikle bir olay sonrasında 45 dakika içinde yayımlanır.

#### <a name="planned-maintenance"></a>Planlı bakım

Azure altyapısı düzenli olarak planlı bakım gerçekleştirir: veri merkezindeki donanım veya yazılım bileşenlerinin yükseltilmesi. Veritabanı bakım sırasında kaldığında Azure SQL, bazı mevcut bağlantıları sonlandırabilir ve yenilerini reddedebilir. Planlanan bakım sırasında karşılaşılan oturum açma hatalarının genellikle geçicidir ve [yeniden deneme mantığı](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors) etkiyi azaltmaya yardımcı olur. Oturum açma hatalarıyla karşılaşmaya devam ederseniz desteğe başvurun.

#### <a name="reconfiguration"></a>Yeniden yapılandırma

Yeniden yapılandırma geçici koşullar olarak değerlendirilir ve zaman zaman beklenir. Bu olaylar, Yük Dengeleme veya yazılım/donanım hatalarıyla tetiklenebilir. Bulut veritabanına bağlanan tüm istemci üretim uygulamaları, bu durumların azaltılmasına yardımcı olacağından ve genellikle hataları son kullanıcıya saydam hale getirmek için sağlam bir bağlantı [yeniden deneme mantığı](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors)uygulamalıdır.

## <a name="next-steps"></a>Sonraki adımlar

- [Geçici hatalar için yeniden deneme mantığı](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors)hakkında daha fazla bilgi edinin.
- [SQL bağlantı hatalarını giderin, tanılayın ve önleyin](troubleshoot-common-connectivity-issues.md).
- [Kaynak durumu uyarılarını yapılandırma](../../service-health/resource-health-alert-arm-template-guide.md)hakkında daha fazla bilgi edinin.
- [Kaynak durumu](../../service-health/resource-health-overview.md)bir genel bakış alın.
- [Sss kaynak durumu](../../service-health/resource-health-faq.md)gözden geçirin.
