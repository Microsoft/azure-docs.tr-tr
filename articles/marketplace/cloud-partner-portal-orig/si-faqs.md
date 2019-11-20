---
title: Satıcı öngörüleri SSS
description: Bulut İş Ortağı Portalı 'nin satıcı öngörüleri özelliği hakkında sık sorulan sorular.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: pabutler
ms.openlocfilehash: b015d21005448d24d24923ba424a10a4af0d47b1
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73821609"
---
<a name="seller-insights-faq"></a>Satıcı öngörüleri SSS
===================

Bu makalede, içindeki genel Kullanıcı yordamlarına yönelik yönergeler ve satıcı öngörüleri hakkında sorular sunulmaktadır.


<a name="find-definitions-for-the-values-in-the-downloaded-transaction-file"></a>İndirilen işlem dosyasındaki değerler için tanımları bul
------------------------------------------------------------------

İşlem dosyasındaki ölçüm değerlerinin tanımları, [satıcı öngörüleri tanımlarında](./si-insights-definitions-v4.md)bulunur.


<a name="see-customer-details-of-transactions-for-which-ive-been-paid"></a>Ücretli işlemlerin müşteri ayrıntılarına bakın
-------------------------------------------------------------

Ödeme modülünden işlemlerinizi indirdikten sonra, **ödeme durumu**etiketli sütunu bulun ve filtreyi yalnızca "ücretli" değeri görüntüleyecek şekilde uygulayın. Müşteri ayrıntılarını içeren aşağıdaki sütunlar görüntülenir: **Şirket adı**, **müşteri e-postası**, **Müşteri ülkesi**, **Müşteri durumu**ve **Müşteri posta kodu**.


<a name="calculate-my-open-accounts-receivable"></a>Açık hesaplarımı hesapla
-------------------------------------

Ödeme modülünden işlemlerinizi indirdikten sonra, **ödeme durumu**etiketli sütunu bulun ve filtreyi yalnızca "yaklaşan ödeme" ve "ödeme Için hazır değil" değerini görüntüleyecek şekilde uygulayın. Ardından, **ödeme tutarı (PC)** olarak etiketlenen sütunu toplayın.


<a name="calculate-revenue-by-customer-usage-period"></a>Müşteri kullanım dönemine göre geliri hesapla
------------------------------------------

Ödeme modülünden işlemlerinizi indirdikten sonra, **Işlem durumu**etiketli sütunu bulun ve "ücretli" değeri filtreleyin.   Listelenen her işlem için, **ödeme tutarı (PC)** olarak etiketlenmiş olan sütun, ücretli miktarı temsil eder.  Hareketle ilişkili kullanım süresini tahmin etmek için, hareketin geçerli olduğu dönem için son kullanım gününün bir kapanış günü olan sütun **ücreti tarihini**kullanın.


<a name="calculate-your-bad-debt"></a>Hatalı borcunu hesaplayın
---------------------

Ödeme modülünden işlemlerinizi indirdikten sonra, **son toplama durumu**etiketli sütunu bulun ve filtreyi yalnızca "yazma kapalı" değerini görüntüleyecek şekilde uygulayın. Ardından, **ödeme tutarı (PC)** olarak etiketlenen sütunu toplayın.


<a name="view-payout-or-customer-contact-information"></a>Ödeme veya müşteri iletişim bilgilerini görüntüleme
-------------------------------------------

"Katkıda bulunan" rolüyle değil, "sahip" rolüne sahip bir kullanıcı olarak oturum açın. Ödeme ve müşteri bilgilerini yalnızca sahip rolü görebilir. Kullanıcı rolleri hakkında daha fazla bilgi için [kullanıcıları yönetme](./cloud-partner-portal-manage-users.md)makalesine ulaşabilirsiniz.


<a name="calculate-my-advance-payouts"></a>Ön ödelerimi hesapla
----------------------------

Ödeme modülünden işlemlerinizi indirdikten sonra, **Işlem türü**etiketli sütunu bulun ve filtreyi yalnızca "ücret" değerini görüntüleyecek şekilde uygulayın. Ardından, **Son koleksiyon durumu**etiketli sütunu bulun ve filtreyi yalnızca "sürüyor" değerini görüntüleyecek şekilde uygulayın. Son olarak, müşterinin koleksiyonundan önce size ödenen tüm geliştirmeleri hesaplamak için **ödeme tutarı (PC)** sütununu toplayın.


<a name="calculate-customer-refunds"></a>Müşteri para iadelerini hesapla
--------------------------

Ödeme modülünden işlemlerinizi indirdikten sonra, **son toplama durumu**etiketli sütunu bulun ve filtreyi yalnızca "iadesi" değerini görüntüleyecek şekilde uygulayın. Müşterileriniz için işlenen tüm para iadelerini hesaplamak için **ücret tutarı (PC)** sütununu toplayın.


<a name="identify-which-transactions-involved-a-microsoft-channel-partner"></a>Hangi işlemlerin Microsoft kanal Iş ortağı ile ilgili olduğunu belirler
----------------------------------------------------------------

"Satıcı aracılığıyla kurumsal" ve "bulut çözümü sağlayıcısı" değerlerini görüntüleyecek şekilde filtrelenmiş **Azure lisans türündeki** tüm işlemler, bir Microsoft kanal iş ortağı içerir. İş ortağı hakkında daha fazla bilgi için, **satıcı adı** ve **satıcı e-** postalarını, ödeme modülü indirme ve müşteri modülünün indirilmesi ' nde bulabilirsiniz.


<a name="identify-trial-usage-and-trial-conversions"></a>Deneme kullanımı ve deneme dönüşümlerini tanımla
------------------------------------------

Sipariş, kullanım ve ödeme modülü İndirmeleri **artık deneme süresi** , bu belirli bir sıra için ne zaman sona erdiğini anlamanıza yardımcı olur. Deneme kullanımı ve siparişlerini görmek için, indirmelerde **SKU faturalandırma türü** sütununu bulun ve filtreyi yalnızca "deneme" değerini görüntülemek için uygulayın. Deneme dönüştürmelerini görmek için indirmelerin **deneme bitiş tarihi** sütununu bulun ve yalnızca **deneme bitiş tarihi sona erdiğinde** ve **Iptal tarihi** sütununun, **deneme bitiş tarihinden**sonra boş veya sonrasında olan siparişleri görüntülemek için filtreyi uygulayın.


<a name="when-is-my-monthly-payout-calculated"></a>Aylık ödeme ne zaman hesaplanır
------------------------------------

Ödemeler, önceki ayın son takvim günü için hazır olan tüm miktarlar için her ayın 15. gününde size verilir. Microsoft, ayın üçüncü gününde, önceki ayın ödeme tutarını hesaplar ve geri yükleme işleminizdeki tüm geçerli ücret hareketlerini, **ödeme durumu** sütununda "yaklaşan ödeme" ile güncelleştirir. Bu işlemler, ödeme isteği banka hesabınıza gönderilinceye kadar bu durumda kalır. bu süre, ödeme **durumu** "ücretli" olarak güncelleştirilir ve "ödeme tarihi" ödeme isteğini gönderdiğimiz tarihi gösterecek şekilde güncellenecektir. z.


<a name="calculate-customer-acquisition-and-loss"></a>Müşteri alımı ve kaybını hesapla
---------------------------------------

Müşterinin teklifinizden birini ilk kez satın aldığı tarihi, müşteri indirideki **Tarih alındı** sütununu bularak görebilirsiniz. Benzer şekilde, müşterinin indirileceği **Tarih kayıp** sütununu bularak, sizin tarafınızdan yayımlanmış herhangi bir teklifi olmayan tarihi görebilirsiniz.


<a name="finding-more-help"></a>Daha fazla yardım bulma
-----------------

- [Satıcı öngörüleri tanımları](./si-insights-definitions-v4.md) -ölçümler ve veriler için tanımları bulun

- [Satıcı öngörüleri ile çalışmaya](./si-getting-started.md) başlama-satıcı öngörüleri özelliğine giriş.

