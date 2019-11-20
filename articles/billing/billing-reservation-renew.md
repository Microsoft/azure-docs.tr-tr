---
title: Azure rezervasyonlarını otomatik olarak yenileme
description: Rezervasyon indirimleri almaya devam etmek için Azure rezervasyonlarını nasıl otomatik olarak yenileyebileceğinizi öğrenin.
services: billing
author: bandersmsft
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: 5ff0fab9b273a1efc05fefaf19ac5f6fe3867527
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: HT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71718789"
---
# <a name="automatically-renew-reservations"></a>Rezervasyonları otomatik olarak yenileme

Mevcut bir rezervasyonun süresi dolduğunda otomatik olarak bir değiştirme satın almak için rezervasyonları yenileyebilirsiniz. Otomatik yenileme, rezervasyon indirimleri almanın kolay bir yolunu sağlar. Ayrıca bir rezervasyonun süre sonunu yakından izlemek zorunda kalmanızı da gerektirmez. Otomatik yenileme sayesinde, kendiniz yenileme yapmak zorunda kalmadan tasarruf avantajlarını kaybetmemiş olursunuz. Yenileme ayarı varsayılan olarak kapalıdır. Yenileme ayarını, mevcut rezervasyonun süre sonuna kadar istediğiniz zaman etkinleştirebilir veya devre dışı bırakabilirsiniz.

Bir rezervasyon yenilendiğinde, mevcut rezervasyonun süresi dolarsa yeni bir rezervasyon oluşturulur. Mevcut rezervasyonun süresi uzatılmaz.

İstediğiniz zaman otomatik yenilemeyi kabul edebilirsiniz. Yenileme fiyatı, mevcut rezervasyonun süresi dolmadan 30 gün önce kullanılabilir. Rezervasyon süre sonundan en az 30 gün önce yenilemeyi etkinleştirdiğinizde, süre sonundan 30 gün önce size yenileme maliyetlerini açıklayan bir e-posta gönderilir. Rezervasyon fiyatı, yenileme fiyatını kilitlediğiniz zaman ile yenileme zamanı arasında değişebilir. Bu durumda yenileme maliyeti, iki maliyetin en düşük olanıdır. Rezervasyon miktarı üzerinde değişiklik yapabilirsiniz. Bunu yaparsanız yenileme, miktar değişikliği sırasında belirlenen piyasa fiyatını kullanacak şekilde güncelleştirilir.

Yenileme yükümlülüğü yoktur ve mevcut rezervasyon süresi dolmadan önce istediğiniz zaman yenilemeyi geri çevirebilirsiniz.

## <a name="set-up-renewal"></a>Yenilemeyi ayarlama

Azure portalına gidip **Rezervasyonlar**’ı seçin.

1. Rezervasyonu seçin.
2. **Yenileme**’ye tıklayın.
3. **Süre sonunda otomatik olarak yeni ayırma satın al** seçeneğini belirleyin.  
  ![Rezervasyon yenilemeyi gösteren örnek](./media/billing-reservation-renew/reservation-renewal.png)

## <a name="if-you-dont-renew"></a>Yenilemezseniz

Hizmetleriniz normal şekilde çalışmaya devam eder. Rezervasyon süresi dolduktan sonra kullanımınız için kullandıkça öde fiyatlarıyla ücretlendirilirsiniz.

## <a name="required-renewal-permissions"></a>Gerekli yenileme izinleri

Bir rezervasyonu yenilemek için aşağıdaki koşullar gereklidir:

- Mevcut rezervasyonun sahibi olmanız gerekir.
- Rezervasyon tek bir abonelik veya kaynak grubu kapsamındaysa aboneliğin sahibi olmanız gerekir.
- Paylaşılan bir kapsamı varsa aboneliğin sahibi olmanız gerekir.

## <a name="default-renewal-settings"></a>Varsayılan yenileme ayarları

Varsayılan olarak yenileme, süresi dolan rezervasyondan tüm özellikleri devralır. Rezervasyon yenileme satın alımı, aynı SKU, bölge, kapsam, faturalama aboneliği, dönem ve miktarı içerir.

Ancak tasarruflarınızı iyileştirmek için yenileme rezervasyon satın alma miktarını güncelleştirebilirsiniz.

## <a name="when-the-new-reservation-is-purchased"></a>Yeni rezervasyon satın alındığında

Mevcut rezervasyonun süresi dolduğunda yeni bir rezervasyon satın alınır. İki rezervasyon arasındaki gecikmeleri önlemeye çalışırız. Devamlılık sayesinde maliyetlerinizin öngörülebilir olması sağlanır ve indirim almaya devam edersiniz.

## <a name="changing-parent-reservation-after-setting-renewal"></a>Yenilemeyi ayarladıktan sonra üst rezervasyonu değiştirme

Süresi dolan rezervasyon üzerinde aşağıdaki değişikliklerden herhangi birini yaparsanız rezervasyon yenileme iptal edilir:

- Bölme
- Birleştir
- Rezervasyonu bir hesaptan diğerine aktarma
- Rezervasyonu bir WebDirect aboneliğinden bir kurumsal anlaşma (EA) aboneliğine veya başka bir satın alma yöntemine aktarma
- Kaydı yenileme

Yeni rezervasyon, yenileme sırasında süresi dolan rezervasyondan kapsamı ve örnek boyutu esneklik ayarını devralır.

## <a name="new-reservation-permissions"></a>Yeni rezervasyon izinleri

Azure, süresi dolan rezervasyondan yeni rezervasyona izinleri kopyalar. Ek olarak, rezervasyon satın alımının abonelik hesabı yöneticisinin yeni rezervasyona erişimi de vardır.

## <a name="potential-renewal-problems"></a>Olası yenileme sorunları

Azure aşağıdaki durumlarda yenilemeyi işleme alamaz:

- Ödeme tahsil edilemiyorsa
- Yenileme sırasında bir sistem hatası oluşursa
- Süresi dolan SKU, yenileme sırasında etkin değilse
- Kurumsal Anlaşma farklı bir Kurumsal Anlaşma şeklinde yenilendiyse

Yukarıdaki koşullardan herhangi birinin oluşması durumunda bir e-posta bildirimi alırsınız ve yenileme devre dışı bırakılır.

## <a name="renewal-notification"></a>Yenileme bildirimi

Satın alma yönteminize bağlı olarak e-postalar farklı kişilere gönderilir:

- Kurumsal Anlaşma müşterileri: E-postalar, Kurumsal Anlaşma portalında belirlenen bildirim kişilerine gönderilir.
- Kullandıkça öde fiyatlarına tabi bireysel abonelik müşterileri: E-postalar, hesap yöneticisi olarak ayarlanan kullanıcılara gönderilir.
- Bulut Çözümü Sağlayıcısı müşterileri: E-postalar, iş ortağı bildirim kişisine gönderilir.

## <a name="next-steps"></a>Sonraki adımlar
- Azure Ayrılmış Sanal Makine Örnekleri hakkında daha fazla bilgi edinmek için bkz. [Azure Ayrılmış Sanal Makine Örnekleri nedir?](billing-save-compute-costs-reservations.md)
