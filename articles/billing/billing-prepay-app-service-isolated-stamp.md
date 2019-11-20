---
title: Ayrılmış kapasite ile Azure App Service Yalıtılmış Damga Pulu Ücreti maliyetlerinden tasarruf etme
description: Ayrılmış kapasite ile Azure App Service Yalıtılmış Damga Pulu Ücreti maliyetlerinden nasıl tasarruf edebileceğinizi öğrenin.
services: billing
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: banders
ms.openlocfilehash: 5161f18499c082b7064eec2be612557ba09eec97
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/11/2019
ms.locfileid: "70806366"
---
# <a name="save-costs-for-azure-app-service-isolated-stamp-fee-with-reserved-capacity"></a>Ayrılmış kapasite ile Azure App Service Yalıtılmış Damga Pulu Ücreti maliyetlerinden tasarruf etme

Üç yıllık bir süre için damga kullanımınız için rezervasyon taahhüdünde bulunarak Azure App Service Yalıtılmış Damga Pulu Ücretlerinden para tasarrufu sağlayabilirsiniz. Yalıtılmış Damga Pulu Ücreti ayrılmış kapasitesi satın almak için, damga pulunun dağıtılacağı Azure bölgesini ve satın alınacak damga pulu sayısını seçmeniz gerekir.

Rezervasyon satın aldığınızda, rezervasyonun öznitelikleriyle eşleşen Yalıtılmış Damga Pulu Ücreti kullanımı artık kullandıkça öde fiyatlarıyla ücretlendirilmez. Rezervasyon, ayrılmış kapasite kapsamı ve bölgesiyle eşleşen Yalıtılmış damga pullarına otomatik olarak uygulanır. Yalıtılmış bir damga puluna rezervasyon atamanız gerekmez. Rezervasyon, çalışanlara uygulanmaz, bu nedenle damga puluyla ilişkili diğer tüm kaynaklar ayrı olarak ücretlendirilir.

Ayrılmış kapasitenin süresi dolduğunda, Yalıtılmış Damga Pulları çalışmaya devam eder, ancak kullandıkça öde fiyatlarıyla faturalanır. Rezervasyonlar otomatik olarak yenilenmez.

## <a name="determine-the-right-reservation-to-purchase"></a>Satın alınacak doğru rezervasyonu belirleme

Bir rezervasyon satın alarak, ayrılmış miktarları gelecek üç yıl boyunca kullanma taahhüdü vermiş olursunuz. Düzenli olarak kullandığınız ve gelecekte kullanabileceğiniz App Service Yalıtılmış Damga Pulu sayısını belirlemek için kullanım verilerinizi denetleyin.

Buna ek olarak, Yalıtılmış Damga Pulunun Linux veya Windows ölçümünü nasıl gösterdiğini anladığınızdan emin olun.

- Boş bir Yalıtılmış Damga Pulu varsayılan olarak Windows damga pulu ölçümünü gösterir. Örneğin, hiçbir çalışan dağıtılmamış şekilde. Damga pulu üzerinde Windows çalışanları dağıtılırsa bu ölçümü göstermeye devam eder.
- Linux çalışanı dağıtırsanız ölçüm, Linux damga pulu ölçümüne değişir.
- Damga pulu, hem Linux hem de Windows çalışanlarının dağıtılması durumunda Windows ölçümünü gösterir.

Bu nedenle damga pulu ölçümü, damga pulunun ömrü boyunca Windows ve Linux arasında değişebilir.

Damga pulu üzerinde bir veya daha fazla Windows çalışanınız varsa Windows damga pulu rezervasyonu satın alın. Yalnızca damga pulunda _sırf_ Linux çalışanları olmasını planlıyorsanız Linux damga pulu rezervasyonu satın almanız gerekir.

## <a name="buy-isolated-stamp-reserved-capacity"></a>Yalıtılmış Damga Pulu ayrılmış kapasitesi satın alma

[Azure portalında](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22AppService%22%7D) Yalıtılmış Damga Pulu ayrılmış kapasitesi satın alabilirsiniz. [Peşin olarak veya aylık ödemelerle](billing-monthly-payments-reservations.md) rezervasyon ödemesi yapın. Ayrılmış kapasite satın almak için, en az bir kurumsal abonelik için sahip rolüne veya kullandıkça öde fiyatlarına tabi bireysel bir aboneliğe sahip olmanız gerekir.

- Kurumsal abonelikler için, [EA portal](https://ea.azure.com/)’da **Ayrılmış Örnek Ekle** seçeneği etkinleştirilmelidir. Veya ayar devre dışı bırakılırsa EA Yöneticisi olmanız gerekir.
- Bulut Çözümü Sağlayıcısı (CSP) programı için yalnızca yönetici aracılar veya satış temsilcileri, SQL Veri Ambarı ayrılmış kapasitesi satın alabilir.

**Satın almak için:**

1. [Azure portalına](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22AppService%22%7D) gidin.
1. Bir abonelik seçin. Ayrılmış kapasitenin ödemesini yapmak için kullanılan aboneliği seçmek amacıyla **Abonelik** listesini kullanın. Ayrılmış kapasite maliyetleri, aboneliğin ödeme yöntemiyle ücretlendirilir. Abonelik türü bir kurumsal anlaşma (teklif numarası: MS-AZR-0017P veya MS-AZR-0148P) ya da Kullandıkça Öde (teklif numarası: MS-AZR-0003P veya MS-AZR-0023P) ya da CSP aboneliği.
    - Kurumsal abonelik için ücretler kaydın maddi işlem bakiyesinden düşülür ve fazla kullanım olarak ücretlendirilir.
    - Kullandıkça Öde aboneliğinde ücretler, aboneliğin kredi kartı veya fatura ödeme yöntemi ile faturalandırılır.
1. Bir **Kapsam** seçerek bir abonelik kapsamı seçin.
    - **Tek kaynak grubu kapsamı**: Yalnızca seçilen kaynak grubunda eşleşen kaynaklara rezervasyon indirimini uygular.
    - **Tek abonelik kapsamı**: Yalnızca seçilen abonelikte eşleşen kaynaklara rezervasyon indirimini uygular.
    - **Paylaşılan kapsam**: Faturalama bağlamında bulunan uygun aboneliklerdeki eşleşen kaynaklara rezervasyon indirimini uygular. Kurumsal Anlaşma müşterileri için faturalama bağlamı kayıttır. Kullandıkça öde fiyatlarına tabi bireysel abonelikler için faturalama kapsamı, hesap yöneticisi tarafından oluşturulan tüm uygun aboneliklerdir.
1. Ayrılmış kapasite kapsamındaki bir Azure bölgesi seçmek için bir **Bölge** seçin ve rezervasyonu sepete ekleyin.
1. Bir Yalıtılmış Plan türü seçin ve **Seç**’e tıklayın.  
    ![Örnek ](./media/billing-prepay-app-service-isolated-stamp/app-service-isolated-stamp-select.png)
1. Ayrılacak App Service Yalıtılmış damga pulu miktarını girin. Örneğin, üç miktarı, size bir bölge için üç ayrılmış damga pulu sunar. **İleri: Gözden Geçirme + Satın Alma** seçeneğine tıklayın.
1. Gözden geçirin ve **Şimdi satın al** seçeneğine tıklayın.

Satın aldıktan sonra, satın alma durumunu görüntülemek ve istediğiniz zaman izlemek için [Rezervasyonlar](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade)’a gidin.

## <a name="cancel-exchange-or-refund-reservations"></a>Rezervasyonları iptal etme, değiştirme veya para iadesi alma

Belirli sınırlamalarla rezervasyonları iptal edebilir, değiştirebilir veya para iadesi alabilirsiniz. Daha fazla bilgi için bkz. [Azure Ayrılmış Sanal Makine Örnekleri için self servis değişimler ve para iadeleri](billing-azure-reservations-self-service-exchange-and-refund.md).

## <a name="discount-application-shown-in-usage-data"></a>Kullanım verilerinde gösterilen indirim uygulaması

Kullanım verileriniz, kullanım için sıfır geçerli fiyata sahip olup rezervasyon indirimi alır. Kullanım verileri, her rezervasyondaki her damga pulu örneği için rezervasyon indirimini gösterir.

Kullanım verilerinde rezervasyon indiriminin nasıl gösterileceği hakkında daha fazla bilgi için, Kurumsal Anlaşma (EA) müşterisiyseniz bkz. [Kurumsal Anlaşma rezervasyon maliyetleri ve kullanım bilgilerini alma](billing-understand-reserved-instance-usage-ea.md). Aksi takdirde bkz. [Kullandıkça öde fiyatlarına tabi bireysel aboneliğiniz için Azure rezervasyon kullanımını anlama](billing-understand-reserved-instance-usage.md).

## <a name="next-steps"></a>Sonraki adımlar

- Azure Ayrılmış Sanal Makine Örnekleri hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:
  - [Azure Ayrılmış Sanal Makine Örnekleri nedir?](billing-save-compute-costs-reservations.md)
  - [Azure App Service Yalıtılmış Damga Pulu rezervasyon indiriminin nasıl uygulandığını anlama](billing-reservation-discount-app-service-isolated-stamp.md)
  - [Kurumsal kaydınız için rezervasyon kullanımını anlama](billing-understand-reserved-instance-usage-ea.md)
