---
title: Azure Spring Cloud 'da sorunları kendi kendine tanılama ve çözme
description: Azure Spring Cloud 'daki sorunları kendi kendine tanılamayı ve çözmeyi öğrenin.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 05/29/2020
ms.custom: devx-track-java
ms.openlocfilehash: 03b29e6ddd16731073b33fef14d5aafb54df2abe
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107227866"
---
# <a name="self-diagnose-and-solve-problems-in-azure-spring-cloud"></a>Azure Spring Cloud 'da kendi kendine tanılama ve sorunları çözme

**Bu makale şu şekilde geçerlidir:** ✔️ Java ✔️ C #

Azure yay bulutu Tanılaması, yapılandırma olmadan uygulamanızın sorunlarını gidermeye yönelik etkileşimli bir deneyimdir. Azure yay Bulut Tanılaması sorunları tanımlar ve sorun gidermeye ve sorunları çözmeye yardımcı olan bilgileri size rehberlik eder.

## <a name="prerequisites"></a>Önkoşullar
Bu sertifikayı tamamlayabilmeniz için şunlar gerekir:

* Azure aboneliği. Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.
* Dağıtılmış bir Azure yay bulut hizmeti örneği. Kullanmaya başlamak için [Azure CLI aracılığıyla uygulama dağıtmaya yönelik hızlı başlangıç hızımızı](spring-cloud-quickstart.md) izleyin.
* Hizmet örneğiniz için en az bir uygulama zaten oluşturulmuş.

## <a name="navigate-to-the-diagnostics-page"></a>Tanılama sayfasına gidin
1. Azure portalında oturum açın.
2. Azure yay bulutuna **genel bakış** sayfasına gidin.
3. Sol gezinti bölmesinde **Tanıla ve sorunları çöz** ' ü seçin.

 ![Tanıla, Çöz iletişim kutusu](media/spring-cloud-diagnose/diagnose-solve-dialog.png)

 ## <a name="search-logged-issues"></a>Günlüğe kaydedilen sorunları ara
Bir sorunu bulmak için, bir anahtar sözcük yazarak veya çözüm grubu ' na tıklayarak bu kategoride araştırma yapabilirsiniz.

 ![Arama sorunları](media/spring-cloud-diagnose/search-detectors.png)

Yapılandırma **sunucusu durum denetimi**, **yapılandırma sunucusu sistem** durumu veya **yapılandırma sunucusu güncelleştirme geçmişi** seçimi çeşitli sonuçlar görüntüler.

![Sorun seçenekleri](media/spring-cloud-diagnose/detectors-options.png)

Hedef algılayıcısının bulun ve yürütmek için tıklayın. Algılayıcısının yürütülmesi gerçekleştirildikten sonra tanılama özeti gösterilir. Algılayıcı listesine geri dönmek için tanılama ayrıntılarını denetlemek üzere **tam raporu görüntüle** ' yi veya **kutucuk menüsü düğmesini göster** ' i seçin.

 ![Özet tanılama](media/spring-cloud-diagnose/summary-diagnostics.png)

Tanılama ayrıntıları sayfasında, tanılama zaman aralığını denetleyici ile sağ üst köşedeki şekilde değiştirebilirsiniz. Daha fazla ölçüm veya günlük görmek için her bir tanılamayı değiştirin. Ölçümler ve Günlükler için 15 dakikalık bir gecikme olabilir.

 ![Tanılama ayrıntıları](media/spring-cloud-diagnose/diagnostics-details.png)

Bazı sonuçlar ilgili belgeleri içerir.

 ![İlgili ayrıntılar](media/spring-cloud-diagnose/related-details.png)

## <a name="next-steps"></a>Sonraki adımlar
* [Uyarıları ve eylem gruplarını kullanarak Spring Cloud kaynaklarını izleme](spring-cloud-tutorial-alerts-action-groups.md)
* [Azure Spring Cloud Hizmeti için güvenlik denetimleri](spring-cloud-concept-security-controls.md)