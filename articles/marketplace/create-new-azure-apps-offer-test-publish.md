---
title: Azure uygulaması teklifini test etme ve yayımlama
description: Azure uygulama teklifinizi önizlemede görüntülemek için Iş Ortağı Merkezi 'ni kullanın, teklifinizin önizlemesini yapın, test edin ve Microsoft Commercial Market 'te yayımlayın.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 11/06/2020
ms.openlocfilehash: 461f9354bb3a6eae0af186de8fe9f39c6b5fff2c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96620928"
---
# <a name="how-to-test-and-publish-an-azure-application-offer"></a>Azure uygulaması teklifini test etme ve yayımlama

Bu makalede, Iş Ortağı Merkezi 'nin yayımlama için Azure uygulama teklifinizi göndermek, teklifinizin önizlemesi, test etmek ve ardından ticari Market 'te canlı yayımlamak için nasıl kullanılacağı açıklanmaktadır. Yayımlamak istediğiniz bir teklifi önceden oluşturmuş olmanız gerekir.

## <a name="submit-your-offer-for-publishing"></a>Teklifinizi yayımlama için gönderme

1. [Iş Ortağı Merkezi](https://partner.microsoft.com/dashboard/commercial-marketplace/overview)'nde ticari Market panosunda oturum açın.
1. **Genel bakış** sayfasında, yayımlamak istediğiniz teklifi seçin.
1. Portalın sağ üst köşesinde, **gözden geçir ve Yayımla**' yı seçin.
1. Her bir sayfanın **durum** sütununun **tamamlandığını** belirttiğinizden emin olun. Olası üç durum aşağıdaki gibidir:
    - **Başlatılmamış** – sayfa tamamlanmadı.
    - **Tamamlanmamış** – sayfada gerekli bilgiler eksik veya düzeltilmesi gereken hatalar var. Sayfaya dönüp güncelleştirmeniz gerekir.
    - **Tamam** – sayfa tamamlanmıştır. Gerekli tüm veriler sağlanmış ve hata yok.
1. Sayfaların herhangi birinde **tamamlanmamış** bir durum varsa, sayfa adını seçin, sorunu giderin, sayfayı kaydedin ve ardından **gözden geçir ve yeniden Yayımla** ' yı seçerek bu sayfaya geri dönün.
1. Tüm sayfalar tamamlandıktan sonra, **sertifika notları** kutusunda, uygulamanızın doğru şekilde test edilmesini sağlamak için sertifika ekibine test yönergeleri sağlayın. Uygulamanızı anlamak için yardımcı olan tüm ek notları sağlayın.
1. Teklifinizin yayımlama işlemini başlatmak için **Yayımla**' yı seçin. **Teklifin genel bakış** sayfası açılır ve teklifin **Yayımlama durumunu** gösterir.

Teklifinizin yayımlama durumu, yayın sürecinde gezindiğinde değişir. Bu işlemle ilgili ayrıntılı bilgi için bkz. [doğrulama ve yayımlama adımları](review-publish-offer.md#validation-and-publishing-steps).

## <a name="preview-and-test-your-offer"></a>Teklifinizi önizleyin ve test edin

Teklif, oturum açma isteğiniz için hazırlandığınızda size teklif önizlemeyi gözden geçirmenizi ve onaylamanızı isteyen bir e-posta göndereceğiz. Teklifinizin Yayımcı oturum açma aşamasına ulaşmış olup olmadığını görmek için tarayıcınızda **teklif genel bakış** sayfasını da yenileyebilirsiniz. Varsa, **canlı çalış** düğmesi ve Önizleme bağlantısı kullanılabilir olacaktır. Teklifinizi Microsoft aracılığıyla satmayı seçerseniz, önizleme izleyicisine eklenmiş olan herkes, bu aşamada gereksinimlerinizi karşıladığından emin olmak için teklifinizin Alım ve dağıtımını test edebilir.

Aşağıdaki ekran görüntüsünde, **canlı çalış** düğmesinin altında iki önizleme bağlantısı bulunan bir teklif için **teklif genel bakış** sayfası gösterilmektedir. Bu sayfada göreceğiniz doğrulama adımları, teklifi oluştururken yaptığınız seçimlere bağlı olarak değişir.

[![Iş Ortağı Merkezi 'nde teklif için teklif genel bakış sayfasını gösterir. Canlı çalış düğmesi ve önizleme bağlantıları gösterilir.](media/create-new-azure-app-offer/azure-app-publish-status.png)](media/create-new-azure-app-offer/azure-app-publish-status.png#lightbox)

Teklifinizi önizlemek için aşağıdaki adımları kullanın.

1. **Teklif genel bakış** sayfasında, **canlı çalış** düğmesinin altında bir önizleme bağlantısı seçin. 

1. Uçtan uca satın alma ve kurulum akışını doğrulamak için, önizleme aşamasında teklifinizi satın alın. İlk olarak, bir ücreti işlememenizi sağlamak için Microsoft 'u bir [destek bileti](https://aka.ms/marketplacesupport) ile bilgilendirin.

1. Azure uygulamanız, [ticari Market ölçüm hizmeti kullanılarak ölçülen faturalandırmayı](./partner-center-portal/azure-app-metered-billing.md)destekliyorsa, [Market ölçülen faturalandırma API 'lerinde](./partner-center-portal/marketplace-metering-service-apis.md#development-and-testing-best-practices)ayrıntılı test en iyi yöntemlerini gözden geçirin ve izleyin.

1. Teklifin önizlemesine ve test edilmesine sonra değişiklik yapmanız gerekiyorsa, yeni bir önizleme yayınlamak için düzenleyebilir ve yeniden gönderebilirsiniz. Daha fazla bilgi için bkz. [ticari Market 'te mevcut teklifi güncelleştirme](./partner-center-portal/update-existing-offer.md).

## <a name="publish-your-offer-live"></a>Teklifinizi canlı yayımlayın

Önizlemeinizdeki tüm testleri tamamladıktan sonra, teklifinizi ticari Market 'te canlı yayımlamak için **canlı ol** ' u seçin.

   > [!TIP]
   > Teklifiniz ticari Market 'te zaten yaşıyorsanız, yaptığınız güncelleştirmeler **canlı git**' i seçene kadar canlı olmayacaktır.

Teklifinizi ticari Market 'te kullanılabilir yapmayı seçtiğinizden, canlı teklifin yalnızca teklifin önizleme sürümü gibi yapılandırıldığından emin olmak için bir dizi son doğrulama denetimi gerçekleştirdik. Bu doğrulama denetimleri hakkında daha fazla bilgi için bkz. [Yayımlama aşaması](review-publish-offer.md#publish-phase).

Bu doğrulama denetimleri tamamlandıktan sonra teklifiniz Market 'te canlı olacaktır.

### <a name="errors-and-review-feedback"></a>Hatalar ve gözden geçirme geri bildirimi

Yayımlama işlemindeki **el ile doğrulama** adımı, teklifinizin kapsamlı bir şekilde gözden geçirilmesini ve ilgili teknik varlıklarının (özellikle de Azure Resource Manager şablonu) sorunların genellikle çekme ISTEğI (PR) bağlantıları olarak sunulduğunu gösterir. Bu PR 'ler nasıl görüntüleneceği ve yanıtlanmasına ilişkin bir açıklama, bkz. [İnceleme geri bildirimini işleme](partner-center-portal/azure-apps-review-feedback.md).

Yayımlama adımlarında bir veya daha fazla hata varsa, teklifinizi yeniden yayımlamadan önce bunları düzeltin.

## <a name="next-step"></a>Sonraki adım

- [Iş Ortağı Merkezi 'nde ticari Market için analitik raporlara erişin](partner-center-portal/analytics.md)
- [Azure uygulama teklifinizi](create-new-azure-apps-offer-marketing.md) Microsoft ile ortak satış aracılığıyla satma ve CSP programlarını kullanarak yeniden satma hakkında bilgi edinin.
