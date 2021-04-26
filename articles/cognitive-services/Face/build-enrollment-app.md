---
title: Yüz hizmetine Kullanıcı eklemek için tepki verme uygulaması oluşturma
titleSuffix: Azure Cognitive Services
description: Geliştirme ortamınızı ayarlamayı ve müşterilerden onay almak için bir yüz uygulaması dağıtmayı öğrenin.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 11/17/2020
ms.author: pafarley
ms.openlocfilehash: 39a74c7f3d5fb8f8b60a66947fcce9837ed6ee13
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107505114"
---
# <a name="build-a-react-app-to-add-users-to-a-face-service"></a>Yüz hizmetine Kullanıcı eklemek için tepki verme uygulaması oluşturma

Bu kılavuzda, örnek yüz kaydı uygulamasını kullanmaya nasıl başlacağınız gösterilmektedir. Uygulama, bir yüz tanıma hizmetine Kullanıcı ekleme ve yüksek doğruluk tabanlı yüz verileri alma konusunda anlamlı bir izin almak için en iyi uygulamaları gösterir. Tümleşik bir sistem bunun gibi bir uygulama kullanarak, yüz verilerine göre Touchless erişim denetimi, kimlik doğrulama, katılımcı izleme, kişiselleştirme bilgi noktası veya kimlik doğrulaması sağlar.

Başlatıldığında, uygulama kullanıcılara ayrıntılı bir onay ekranı gösterir. Kullanıcı onay veriyorsa, uygulama bir Kullanıcı adı ve parola sorar ve sonra cihazın kamerasını kullanarak yüksek kaliteli bir yüz görüntüsünü yakalar.

Örnek uygulama, JavaScript ve tepki verme yerel çerçevesi kullanılarak yazılır. Bu, şu anda Android cihazlarda dağıtılabilir; daha sonra daha fazla dağıtım seçeneği geliyor.

## <a name="prerequisites"></a>Önkoşullar 

* Bir Azure aboneliği – [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/cognitive-services/).  
* Azure aboneliğiniz olduktan sonra, anahtarınızı ve uç noktanızı almak için Azure portal [bir yüz kaynağı oluşturun](https://portal.azure.com/#create/Microsoft.CognitiveServicesFace) . Dağıtıldıktan sonra **Kaynağa Git**' i seçin.  
  * Uygulamanızı Yüz Tanıma API'si bağlamak için oluşturduğunuz kaynaktaki anahtar ve uç nokta gerekir.  
  * Yerel geliştirme ve test için, API anahtarını ve uç noktayı yapılandırma dosyasına yapıştırabilirsiniz. Son dağıtım için, API anahtarını güvenli bir konumda depolayın ve kodda hiçbir şekilde saklayın.  

> [!IMPORTANT]
> Bu abonelik anahtarları bilişsel hizmet API 'nize erişmek için kullanılır. Anahtarlarınızı paylaşmayın. Azure Key Vault kullanarak güvenli bir şekilde depolayın. Ayrıca, bu anahtarların düzenli olarak yeniden oluşturulması önerilir. API çağrısı yapmak için yalnızca bir anahtar gereklidir. İlk anahtarı yeniden oluştururken, hizmete devam eden erişim için ikinci anahtarı kullanabilirsiniz.

## <a name="set-up-the-development-environment"></a>Geliştirme ortamını ayarlama

1. [Örnek uygulama](https://github.com/azure-samples/cognitive-services-FaceAPIEnrollmentSample)için Git deposunu kopyalayın.
1. Geliştirme ortamınızı ayarlamak için, doğal <a href="https://reactnative.dev/docs/environment-setup"  title=" Yerel belgelere tepki verme doğal belgelerini izleyin "  target="_blank"> </a> . Geliştirme işletim sistemi olarak **Yerel CLI hızlı** başlangıcı ' nı seçin ve hedef işletim sistemi olarak **Android** ' i seçin. Bağımlılıklar ve **Android geliştirme ortamı** **yükleme** bölümlerini doldurun.
1. [Visual Studio Code](https://code.visualstudio.com/)gibi tercih ettiğiniz metin düzenleyicisinde env.jsdosyası açın ve uç nokta ve anahtarınızı ekleyin. Bitiş noktanıza ve anahtarınıza, kaynağınızın **genel bakış** sekmesi altına Azure Portal ulaşabilirsiniz. Bu adım yalnızca yerel test amaçlıdır &mdash; Yüz Tanıma API'si anahtarınızı uzak deponuza iade etmeyin.
1. Android Studio veya kendi Android cihazınızdan Android sanal cihaz öykünücüsünü kullanarak uygulamayı çalıştırın. Uygulamanızı fiziksel bir cihazda test etmek için ilgili yanıt verme <a href="https://reactnative.dev/docs/running-on-device"  title=" Yerel belgelerini Izleyin "  target="_blank"> Yerel belgelere tepki verin </a> .  


## <a name="create-a-user-add-experience"></a>Kullanıcı ekleme deneyimi oluşturma  

Örnek uygulamayı ayarladığınıza göre, bunu kendi gereksinimlerinize uyarlayabilirsiniz.

Örneğin, izin sayfanıza durumunuza özgü bilgileri eklemek isteyebilirsiniz:

> [!div class="mx-imgBorder"]
> ![uygulama onay sayfası](./media/enrollment-app/1-consent-1.jpg)

Hizmet, görüntünün müşteriyi eklemek veya yüz tanıma tanımayı denemek için yeterli kalite olup olmadığı konusunda seçim yapmanıza yardımcı olacak görüntü kalitesi denetimleri sağlar. Bu uygulama, cihazın kamerasından çerçevelere erişme, en yüksek kaliteli çerçeveleri seçme ve algılanan yüzü Yüz Tanıma API'si hizmetine ekleme işlemlerinin nasıl yapılacağını gösterir. 

Birçok yüz tanıma sorunu, düşük kaliteli başvuru görüntülerinin oluşmasına neden olmuştur. Model performansını düşürebilen bazı faktörler şunlardır:
* Yüz boyutu (kameradan uzak yüzler)
* Yüz yönü (kameradan kapatılan veya uzaklaşmış yüzler)
* Görüntünün kötü açık veya çok fazla gürültü olabileceği zayıf aydınlatma koşulları (düşük hafif veya geri aydınlatma)
* Haya veya kalın gözlük gibi aksesuarlar dahil, occlusiyon (kısmen gizli veya engellenmemiş yüzler)
* Bulanıklaştırma (fotoğraf çekilirken hızlı hareket eden gibi). 

> [!div class="mx-imgBorder"]
> ![Uygulama görüntüsü yakalama yönerge sayfası](./media/enrollment-app/4-instruction.jpg)

Uygulamanın Ayrıca Kullanıcı bilgilerini silme ve yeniden ekleme seçeneği sunan işlevleri de içerdiğine dikkat edin.

> [!div class="mx-imgBorder"]
> ![Profil Yönetimi sayfası](./media/enrollment-app/10-manage-2.jpg)

Uygulamanın işlevselliğini tam deneyimi kapsayacak şekilde genişletmek için, uygulanacak ek özellikler ve en iyi uygulamalar için [genel bakışı](enrollment-overview.md) okuyun.

## <a name="deploy-the-app"></a>Uygulamayı dağıtma

### <a name="android"></a>Android

İlk olarak, uygulamanızın üretim dağıtımı için uygun olduğundan emin olun: uygulama kodundan tüm anahtarları veya parolaları kaldırın ve [en iyi güvenlik uygulamalarını](../cognitive-services-security.md?tabs=command-line%2ccsharp)takip ettiğinizden emin olun.

Uygulamanızı üretime bırakmaya hazırsanız, Android uygulamaları için paket dosyası biçimi olan, serbest kullanıma uygun bir APK dosyası oluşturacaksınız. Bu APK dosyası bir özel anahtarla imzalanmalıdır. Bu yayın derlemesi ile uygulamayı doğrudan cihazlarınıza dağıtmaya başlayabilirsiniz. 

<a href="https://developer.android.com/studio/publish/preparing#publishing-build"  title=" "  target="_blank"> </a> Özel anahtar oluşturmayı, uygulamanızı imzalamayı ve bir APK yayını oluşturmayı öğrenmek için yayın hazırlığı için hazırlanma belgelerini izleyin.  

İmzalı bir APK oluşturduktan sonra uygulamanızı yayımlama <a href="https://developer.android.com/studio/publish"  title=" "  target="_blank"> </a> hakkında daha fazla bilgi edinmek için uygulamanızı yayımlama belgelerinize bakın.

## <a name="next-steps"></a>Sonraki adımlar  

Bu kılavuzda, geliştirme ortamınızı ayarlamayı ve örnek uygulamayı kullanmaya başlamanızı öğrendiniz. Yerel olarak tepki verme konusunda yeni başladıysanız, daha fazla arka plan bilgisi edinmek için Başlarken [belgelerini](https://reactnative.dev/docs/getting-started) okuyabilirsiniz. Ayrıca, [Yüz Tanıma API'si](Overview.md)konusunda kendinizi tanımak faydalı olabilir. Geliştirmeye başlamadan önce Kullanıcı ekleme hakkındaki diğer bölümleri okuyun.