---
title: Microsoft Commercial Market 'e SaaS teklifini test etme ve yayımlama
description: Iş Ortağı Merkezi 'ni kullanarak SaaS teklifinizi önizlemeye sunun, teklifinizin önizlemesini yapın, test edin ve Microsoft Commercial Market 'te yayımlayın.
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: mingshen-ms
ms.author: mingshen
ms.date: 03/25/2021
ms.openlocfilehash: aeab671c9283d64f9c1ca37cf184b80b1eca8f35
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105045255"
---
# <a name="how-to-test-and-publish-a-saas-offer-to-the-commercial-marketplace"></a>Ticari Market 'e SaaS teklifini test etme ve yayımlama

Bu makalede, Iş Ortağı Merkezi 'ni yayımlama için SaaS teklifinizi göndermek, teklifinizin önizlemesi, test etmek ve ardından ticari Market 'te canlı yayımlamak için nasıl kullanacağınız açıklanmaktadır. Yayımlamak istediğiniz bir teklifi önceden oluşturmuş olmanız gerekir.

> [!NOTE]
> Üretim (ÜRETIM) teklifinizi yayımlamadan önce test etmek için düşük riskli bir yöntem olarak ayrı bir test ve geliştirme (DEV) teklifi oluşturmanızı öneririz. (ÜRETIM) teklifinizi yayımlamadan önce GELIŞTIRME teklifini oluşturmak ve test etmek için aşağıdaki adımları izleyin.

## <a name="submit-your-offer-for-publishing"></a>Teklifinizi yayımlama için gönderme

1. [Iş Ortağı Merkezi](https://partner.microsoft.com/dashboard/commercial-marketplace/overview)'nde ticari Market panosunda oturum açın.
1. **Genel bakış** sayfasında, yayımlamak istediğiniz teklifi seçin.
1. Portalın sağ üst köşesinde, **gözden geçir ve Yayımla**' yı seçin.
2. Her bir sayfanın **durum** sütununun **tamamlandığını** belirttiğinizden emin olun. Olası üç durum aşağıdaki gibidir:

   - **Başlatılmamış** – sayfa tamamlanmadı.
   - **Tamamlanmamış** – sayfada gerekli bilgiler eksik veya düzeltilmesi gereken hatalar var. Sayfaya dönüp güncelleştirmeniz gerekir.
   - **Tamam** – sayfa tamamlanmıştır. Gerekli tüm veriler sağlanmış ve hata yok.

1. Sayfaların herhangi birinde **tamamlanmamış** bir durum varsa, sayfa adını seçin, sorunu giderin, sayfayı kaydedin ve ardından **gözden geçir ve yeniden Yayımla** ' yı seçerek bu sayfaya geri dönün.
1. Tüm sayfalar tamamlandıktan sonra, **sertifika notları** kutusunda, uygulamanızın doğru şekilde test edilmesini sağlamak için sertifika ekibine test yönergeleri sağlayın. Uygulamanızı anlamak için yardımcı olan tüm ek notları sağlayın.
1. Teklifinizin yayımlama işlemini başlatmak için **Yayımla**' yı seçin. **Teklifin genel bakış** sayfası açılır ve teklifin **Yayımlama durumunu** gösterir.

Teklifinizin yayımlama durumu, yayın sürecinde gezindiğinde değişir. Bu işlemle ilgili ayrıntılı bilgi için bkz. [doğrulama ve yayımlama adımları](review-publish-offer.md#validation-and-publishing-steps).

## <a name="preview-and-test-your-offer"></a>Teklifinizi önizleyin ve test edin

Teklif, oturum açma isteğiniz için hazırlandığınızda size teklif önizlemeyi gözden geçirmenizi ve onaylamanızı isteyen bir e-posta göndereceğiz. Teklifinizin Yayımcı oturum açma aşamasına ulaşmış olup olmadığını görmek için tarayıcınızda **teklif genel bakış** sayfasını da yenileyebilirsiniz. Varsa, **canlı çalış** düğmesi ve önizleme bağlantıları kullanılabilir hale gelir. Teklifinizi oluştururken seçtiğiniz seçeneklere bağlı olarak Microsoft AppSource önizlemesi, Azure Marketi önizlemesi veya her ikisi için bir bağlantı olacaktır. Teklifinizi Microsoft aracılığıyla satmayı seçerseniz, önizleme izleyicisine eklenmiş olan herkes, bu aşamada gereksinimlerinizi karşıladığından emin olmak için teklifinizin Alım ve dağıtımını test edebilir.

Aşağıdaki ekran görüntüsünde, bir SaaS teklifinin **teklif genel bakış** sayfası gösterilir ve **canlı çalış** düğmesinin altında iki önizleme bağlantısı vardır. Bu sayfada göreceğiniz doğrulama adımları, teklifi oluştururken yaptığınız seçimlere bağlı olarak değişir.

![Iş Ortağı Merkezi 'nde teklif için teklif genel bakış sayfasını gösterir. Canlı çalış düğmesi ve önizleme bağlantıları gösterilir. Doğrulama raporunu görüntüle bağlantısı da otomatik doğrulama altında gösterilir.](./media/review-publish-offer/publish-status-saas.png)

Teklifinizi önizlemek için aşağıdaki adımları kullanın.

1. **Teklif genel bakış** sayfasında, **canlı çalış** düğmesinin altında bir önizleme bağlantısı seçin.

1. Uçtan uca satın alma ve kurulum akışını doğrulamak için, önizleme aşamasında olan planları teklifinizdeki satın alın. İlk olarak, bir ücreti işlememenizi sağlamak için Microsoft 'u bir [destek bileti](https://aka.ms/marketplacesupport) ile bilgilendirin.

1. SaaS teklifiniz, [ticari Market ölçüm hizmeti kullanılarak ölçülen faturalandırmayı](./partner-center-portal/saas-metered-billing.md)destekliyorsa, [Market ölçümlü faturalandırma API 'lerinde](./partner-center-portal/marketplace-metering-service-apis.md#development-and-testing-best-practices)ayrıntılı test en iyi yöntemlerini gözden geçirin ve izleyin.

1. Teklifinizi canlı yayımlamadan önce teklifinizin API 'lerle sorunsuz bir şekilde tümleştirildiğinden emin olmak için [, Microsoft Commercial Market ' teki API 'nin sürüm 2](./partner-center-portal/pc-saas-fulfillment-api-v2.md#development-and-testing) ' deki test talimatlarını gözden geçirin ve izleyin.

1. Teklif doğrulama adımı uyarılarla sonuçlanmış ise, **teklif genel bakış** sayfasında bir **Görünüm doğrulama raporu** bağlantısı görüntülenir. **Canlı çalış** düğmesini seçmeden önce raporu gözden geçirip sorunları ele aldığınızdan emin olun. Aksi takdirde sertifika büyük olasılıkla başarısız olur ve teklifinizin canlı hale gitmesini geciktirecek.

1. Teklifin önizlemesine ve test edilmesine sonra değişiklik yapmanız gerekiyorsa, yeni bir önizleme yayınlamak için düzenleyebilir ve yeniden gönderebilirsiniz. Daha fazla bilgi için bkz. [ticari Market 'te mevcut teklifi güncelleştirme](./partner-center-portal/update-existing-offer.md).

## <a name="publish-your-offer-live"></a>Teklifinizi canlı yayımlayın

Önizlemeinizdeki tüm testleri tamamladıktan sonra, teklifinizi ticari Market 'te canlı yayımlamak için **canlı ol** ' u seçin. Teklifiniz ticari Market 'te zaten yaşıyorsanız, yaptığınız güncelleştirmeler **canlı git**' i seçene kadar canlı olmayacaktır.

> [!IMPORTANT]
> Bir [geliştirme/test teklifi](create-saas-dev-test-offer.md)Için **canlı çalış** ' ı seçmeyin.

Teklifinizi ticari Market 'te kullanılabilir yapmayı seçtiğinizden, canlı teklifin yalnızca teklifin önizleme sürümü gibi yapılandırıldığından emin olmak için bir dizi son doğrulama denetimi gerçekleştirdik. Bu doğrulama denetimleri hakkında daha fazla bilgi için bkz. [Yayımlama aşaması](review-publish-offer.md#publish-phase).

Bu doğrulama denetimleri tamamlandıktan sonra teklifiniz Market 'te canlı olacaktır.

## <a name="next-steps"></a>Sonraki adımlar

- [Iş Ortağı Merkezi 'nde ticari Market için analitik raporlara erişin](./partner-center-portal/analytics.md)
