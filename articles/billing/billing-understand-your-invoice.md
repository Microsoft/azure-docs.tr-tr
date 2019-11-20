---
title: Azure faturanızı anlama | Microsoft Docs
description: Azure aboneliğinizin kullanımını ve faturasını okuma ve anlama hakkında bilgi edinin
services: ''
documentationcenter: ''
author: bandersmsft
manager: jureid
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/31/2017
ms.author: banders
ms.openlocfilehash: 77c1a85136b2117af7396b8eec2d8b92b335d61d
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/11/2019
ms.locfileid: "60369973"
---
# <a name="understand-terms-on-your-microsoft-azure-invoice"></a>Microsoft Azure faturanızdaki terimleri anlayın

Faturada ücretlerinizin özeti ve ödeme yönergeleri yer alır. [Azure portalından](https://portal.azure.com/) Taşınabilir Belge Biçiminde (.pdf) indirilebilir veya e-postayla gönderilebilir. Daha fazla bilgi için bkz. [Azure faturanızı ve günlük kullanım verilerinizi edinme](billing-download-azure-invoice-daily-usage-date.md).

Dikkat edilecek birkaç şey:

-   Ücretsiz deneme aboneliği kullanıyorsanız kullanım ayrıntılarına Azure portalından erişebilirsiniz ancak fatura düzenlenmez.

-   Mevcut faturanızda bir önceki fatura döneminin sonundaki en fazla 24 saatlik kullanım dahil edilebilir.

-   Uluslararası müşterilerin fatura ekstrelerinde listelenen ücretler yalnızca tahmin amaçlıdır. Bankaların döviz kuru dönüştürme maliyetleri farklı olabilir.

>[!VIDEO https://www.youtube.com/embed/jWG1lyJe3Mg]

## <a name="detailed-terms-and-descriptions-of-your-invoice"></a>Faturanızın ayrıntılı terimleri ve açıklamaları
Aşağıdaki bölümlerde, faturanızda gördüğünüz önemli terimler ve her bir terimin açıklaması listelenmektedir.

### <a name="account-information"></a>Hesap bilgileri

Faturanın hesap bilgileri bölümü, ilk sayfanın en üstünde bulunur ve profiliniz ve aboneliğiniz ile ilgili bilgileri gösterir.

![Faturanın hesap bilgileri bölümü](./media/billing-understand-your-invoice/1.png)

| Sözleşme Dönemi | Açıklama |
| --- | --- |
| Müşteri Satınalma Siparişi No. |İzleme için atadığınız isteğe bağlı bir satınalma siparişi numarası |
| Fatura No. |İzleme amacıyla kullanılan, Microsoft tarafından oluşturulan benzersiz bir fatura numarası |
| Fatura döngüsü |Bu faturanın kapsadığı tarih aralığı |
| Fatura tarihi |Faturanın oluşturulduğu tarih, genellikle Faturalama döneminin sona erdiği günden bir sonraki gündür |
| Ödeme yöntemi |Hesapta kullanılan ödeme türü (ör. fatura veya kredi kartı) |
| Fatura adresi |Hesap için listelenen fatura adresi |
| Abonelik teklifi (“Kullandıkça Öde”) |Satın alınan abonelik teklifinin türü (Kullandıkça Öde, BizSpark Plus, Azure Pass vb.). Daha fazla bilgi için bkz. [Azure teklifi türleri](https://azure.microsoft.com/support/legal/offer-details/). |
| Hesap sahibinin e-postası | Microsoft Azure hesabının kayıtlı olduğu hesaba ait e-posta adresi. <br /><br />E-posta adresini değiştirmek için bkz. [İletişim e-postası, adres ve telefon numarası gibi Azure hesabınızın profil bilgilerini değiştirme](billing-how-to-change-azure-account-profile.md). |

### <a name="understand-the-invoice-summary"></a>Fatura özetini anlama
Faturanın **Fatura Özeti** bölümünde, son faturalama döneminizden bu yana toplam işlem tutarları ve geçerli kullanım ücretleriniz listelenir.

![Fatura özeti bölümü](./media/billing-understand-your-invoice/2.png)

Abonelik adı (“Üretim Depolama Alanı”), bu fatura için aboneliğin adıdır.

#### <a name="understand-the-previous-charges"></a>Önceki ücretleri anlama
Faturanın önceki bakiye, ödemeler ve kalan bakiye bölümünde son faturalama döneminizden bu yana yapılan işlemler özetlenir.

| Sözleşme Dönemi | Açıklama |
| --- | --- |
| Önceki bakiye |Son faturalama döneminizden kalan ödenmemiş toplam tutar |
| Ödemeler |Son faturalama döneminize uygulanan toplam ödemeler ve krediler |
| Borç-alacak bakiyesi (önceki fatura döngüsünden) |Son faturalama döneminizden bu yana hesabınızdaki krediler veya kalan bakiye |

#### <a name="understand-the-current-charges"></a>Geçerli ücretleri anlama
Faturanın Geçerli Ücretler bölümünde, geçerli faturalama dönemi için aylık ücretlerinizle ilgili ayrıntılar gösterilir.

| Sözleşme Dönemi | Açıklama |
| --- | --- |
| Kullanım ücretleri |Kullanım ücretleri, geçerli faturalama dönemi için bir abonelikteki toplam aylık ücretlerdir|
| İndirimler |Geçerli faturalama döneminize uygulanan hizmet indirimleri|
| Düzeltmeler |Geçerli faturalama döneminize uygulanan çeşitli krediler (Ücretsiz Kullanım, Krediler vb.) veya kalan ücretler.<br/><br/>Örneğin, Visual Studio Enterprise with MSDN teklifine sahipseniz, aylık kredi görürsünüz. Aboneliğinizi iptal ederseniz, abonelik teklifinizle aldığınız aylık krediyi aşan aylık kullanım ücretlerini görürsünüz. Ücretler, geçerli faturalama döneminizin başlangıcından abonelik iptal tarihine kadar uygulanır. |

#### <a name="sold-to-and-payment-instructions"></a>Alıcı ve ödeme yönergeleri

Aşağıdaki tabloda, faturanızın ikinci sayfasında gösterilen alıcı ve ödeme yönergeleri açıklanmaktadır.

| Sözleşme Dönemi |Açıklama |
| --- | --- |
| Kime Satıldı |Hesapta bulunan profil adresi. <br/><br/>Adresi değiştirmeniz gerekirse bkz. [İletişim e-postası, adres ve telefon numarası gibi Azure hesabınızın profil bilgilerini değiştirme](billing-how-to-change-azure-account-profile.md).|
| Ödeme yönergeleri |Ödeme yöntemine (örneğin, kredi kartı veya fatura ile) bağlı olarak ödeme yapma yönergeleri. |

#### <a name="usage-charges"></a>Kullanım Ücretleri

Faturanın Kullanım ücretleri bölümünde, ücretlerinizin ölçüm düzeyi bilgileri görüntülenir.

![Kullanım ücretleri bölümü](./media/billing-understand-your-invoice/3.png)

Aşağıdaki tabloda, faturanızda gösterilen kullanım ücretleri sütun üst bilgileri açıklanmaktadır.

| Sözleşme Dönemi |Açıklama |
| --- | --- |
| Adı |Kullanım için en üst düzey hizmeti belirtir |
| Tür |Fiyatı etkileyebilecek Azure hizmet türünü tanımlar |
| Kaynak |Kullanılmakta olan ölçüm için ölçü birimini belirtir |
| Bölge |Veri merkezi konumuna bağlı olarak ücretlendirilen belirli hizmetler için veri merkezinin konumunu belirtir |
| Kullanılan |Faturalama dönemi boyunca kullanılan ölçüm miktarı |
| Dahil |Geçerli faturalama döneminize ücretsiz olarak dahil edilen ölçüm miktarı |
| Faturalanabilir |Kullanılan Miktar ve Dahil Edilen Miktar arasındaki farkı gösterir. Bu miktar için faturalandırılırsınız. Teklife hiçbir miktarın dahil olmadığı Kullandıkça Öde teklifleri için bu toplam, Kullanılan miktarla aynıdır |
| Fiyat |Faturalanabilen birim başına ücretlendirildiğiniz fiyat |
| Değer |Fazla Kullanım Miktarı sütununun Fiyat sütunuyla çarpılmasıyla elde edilen sonucu gösterir. Kullanılan Miktar, Dahil Edilen Miktarı aşmazsa bu sütunda bir ücret yoktur. |
| Ara Toplam |Bu faturalama dönemi için tüm vergi öncesi ücretlerinizin toplamı |
| Genel Toplam |Bu faturalama dönemi için tüm vergi sonrası ücretlerinizin toplamı |

## <a name="how-do-i-make-sure-that-the-charges-in-my-invoice-are-correct"></a>Faturamdaki ücretlerin doğru olduğundan nasıl emin olabilirim?
Faturanızda daha fazla ayrıntısına ulaşmak istediğiniz bir ücret varsa bkz. [Microsoft Azure için faturanızı anlama](billing-understand-your-bill.md).

## <a name="need-help-contact-us"></a>Yardıma mı ihtiyacınız var? Bize ulaşın.

Sorularınız varsa ya da yardıma gereksinim duyuyorsanız [destek isteği oluşturun](https://go.microsoft.com/fwlink/?linkid=2083458).
