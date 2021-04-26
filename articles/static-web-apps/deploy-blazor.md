---
title: "Öğretici: Azure static Web Apps 'de Blazor ile statik bir Web uygulaması oluşturma"
description: Blazor ile Azure statik Web Apps web sitesi oluşturmayı öğrenin.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 04/09/2021
ms.author: cshoe
ms.openlocfilehash: 5b8a94409f082177192847f2c65d44a513ecdbe4
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107305047"
---
# <a name="tutorial-building-a-static-web-app-with-blazor-in-azure-static-web-apps"></a>Öğretici: Azure static Web Apps 'de Blazor ile statik bir Web uygulaması oluşturma

Azure statik Web Apps bir GitHub deposundan uygulama oluşturarak bir Web sitesini üretim ortamına yayınlar. Bu öğreticide, Azure statik Web uygulamalarına Azure portal kullanarak bir Web uygulaması dağıtırsınız.

Azure aboneliğiniz yoksa [ücretsiz bir deneme hesabı oluşturun](https://azure.microsoft.com/free).

## <a name="prerequisites"></a>Önkoşullar

- [GitHub](https://github.com) hesabı
- [Azure](https://portal.azure.com) hesabı

## <a name="application-overview"></a>Uygulamaya genel bakış

Azure statik Web Apps, sunucusuz bir arka uç tarafından desteklenen statik Web uygulamaları oluşturmanıza olanak tanır. Aşağıdaki öğreticide, hava durumu verileri döndüren C# Blazor Web uygulamasının nasıl dağıtılacağı gösterilmektedir.

:::image type="content" source="./media/deploy-blazor/blazor-app-complete.png" alt-text="Blazor uygulamasını Tamam":::

Bu öğreticide öne çıkan uygulama üç farklı Visual Studio projeden oluşur:

- **API**: statik uygulamaya Hava durumu BILGILERI sağlayan API uç noktasını uygulayan C# Azure işlevleri uygulaması. **Dalgalı değer işlevi** bir nesne dizisi döndürür `WeatherForecast` .

- **İstemci**: ön uç Blazor Web derleme projesi. Tüm yolların _index.html_ dosyasına sunulmasını sağlamak için bir [geri dönüş yolu](#fallback-route) uygulanır.

- **Paylaşılan**: hem API hem de istemci projeleri tarafından başvurulan ve verilerin API uç noktasından ön uç Web uygulamasına akmasını sağlayan ortak sınıfları barındırır. [`WeatherForecast`](https://github.com/staticwebdev/blazor-starter/blob/main/Shared/WeatherForecast.cs)Sınıf her iki uygulama arasında paylaşılır.

Bu projeler birlikte, bir API arka ucu tarafından desteklenen tarayıcıda çalışan bir Blazor Web derleme uygulaması oluşturmak için gereken bölümleri oluşturur.

## <a name="fallback-route"></a>Geri dönüş yolu

Uygulama, uygulamanın belirli rotalarıyla eşlenen _/Counter_ ve _/fetchdata_ gibi URL 'leri kullanıma sunar. Bu uygulama tek sayfalı bir uygulama olarak uygulandığından, her yol _index.html_ dosyası olarak sunulur. Herhangi bir yol için isteğin bir [geri dönüş yolunun](./routes.md#fallback-routes) _index.htm_ döndürülmemesini sağlamak için, istemci projesinin _Wwwroot_ klasöründe bulunan _staticwebapp.config.jsdosya üzerinde_ bulunur.

```json
{
  "routes": [
    {
      "route": "/*",
      "serve": "/index.html",
      "statusCode": 200
    }
  ]
}
```

Yukarıdaki yapılandırma, uygulamadaki herhangi bir yola yönelik isteklerin _index.html_ sayfasını döndürdüğünden emin olmanızı sağlar.

## <a name="create-a-repository"></a>Depo oluşturma

Bu makalede, başlamanızı kolaylaştırmak için bir GitHub şablon deposu kullanılmaktadır. Şablon, Azure statik Web Apps dağıtılan bir başlangıç uygulamasını sunar.

1. GitHub 'da oturum açtığınızdan emin olun ve yeni bir depo oluşturmak için aşağıdaki konuma gidin:
   - [https://github.com/staticwebdev/blazor-starter/generate](https://github.com/login?return_to=/staticwebdev/blazor-starter/generate)
1. Deponuzu **-First-static-blazor-App** olarak adlandırın

## <a name="create-a-static-web-app"></a>Statik web uygulaması oluşturma

Artık depo oluşturulduğuna göre Azure portal bir statik Web uygulaması oluşturun.

1. [Azure portalına](https://portal.azure.com) gidin
1. **Kaynak oluştur** ' u seçin
1. **Statik Web Uygulamaları** için arama yapın
1. **Statik Web Apps seçin (Önizleme)**
1. **Oluştur**’u seçin

_Temel bilgiler_ bölümünde yeni uygulamanızı yapılandırıp bir GitHub deposuna bağlayarak başlayın.

:::image type="content" source="media/deploy-blazor/basics.png" alt-text="Temel bilgiler sekmesi":::

1. _Azure aboneliğinizi_ seçin
1. Yeni bir _kaynak grubu_ seçin veya oluşturun
1. Uygulamayı **My-First-static-blazor-App** olarak adlandırın
   - Geçerli karakterler şunlardır: `a-z` (büyük/küçük harf duyarsız), `0-9` ve `-`.
1. Size en yakın _bölgeyi_ seçin
1. **Ücretsiz** _SKU 'yu_ seçin
1. GitHub ile **oturum açma** seçeneğini belirleyin ve GitHub ile kimlik doğrulaması yapın

GitHub ile oturum açtıktan sonra depo bilgilerini girin.

:::image type="content" source="media/deploy-blazor/repository-details.png" alt-text="Depo ayrıntıları":::

1. Tercih ettiğiniz _kuruluşu_ seçin
1. _Depo_ açılır listesinden **My-First-static-blazor-App** ' i seçin
1. _Dal_ açılan listesinden **Main** ' i seçin

   Herhangi bir depo görmüyorsanız, GitHub 'da Azure statik Web Apps yetkilendirmeniz gerekebilir. GitHub deponuza gidin ve **yetkili OAuth uygulamaları > ayarlar > uygulamalar**' a gidip **Azure statik Web Apps**' yı seçin ve ardından **ver**' i seçin. Kuruluş depolarında, izinleri vermek için kuruluşun sahibi olmanız gerekir.

1. _Derleme ayrıntıları_ bölümünde, Blazor özgü yapılandırma ayrıntılarını ekleyin.

   - _Derleme ön ayarları_ açılır listesinden **Blazor** ' ı seçin ve tüm varsayılan değerleri saklayın.

1. **Gözden geçir ve oluştur**’u seçin.

   :::image type="content" source="media/deploy-blazor/review-create.png" alt-text="İnceleme oluştur düğmesi":::

1. **Oluştur**’u seçin.

   :::image type="content" source="media/deploy-blazor/create-button.png" alt-text="Oluştur düğmesi":::

1. **Kaynağa git**’i seçin.

   :::image type="content" source="media/deploy-blazor/resource-button.png" alt-text="Kaynağa git düğmesi":::

## <a name="view-the-website"></a>Web sitesini görüntüleme

Statik uygulama dağıtmanın iki yönü vardır. Bunların ilki uygulamanızı oluşturan temel alınan Azure kaynaklarının sağlanmasıdır. İkincisi de, uygulamanızı derleyip yayımlayan GitHub Actions iş akışıdır.

Yeni statik sitenize gidebilmeniz için önce dağıtım derlemesinin çalıştırılması gerekir.

Statik Web Apps genel bakış penceresinde web uygulamanızla etkileşime başlamanıza yardımcı olacak bir dizi bağlantı görüntülenir.

:::image type="content" source="./media/deploy-blazor/overview-window.png" alt-text="Genel bakış penceresi":::

1. Yazılı başlığa tıkladığınızda, _GitHub eylemlerinizin durumunu denetlemek için buraya tıklayın_ . Bu işlem sizi deponuza karşı çalışan GitHub eylemlerine götürür. Dağıtım işinin tamamlandığını doğruladıktan sonra, oluşturulan URL aracılığıyla Web sitenize gidebilirsiniz.

2. GitHub eylemleri iş akışı tamamlandıktan sonra, Web sitesini yeni sekmede açmak için _URL_ bağlantısını seçebilirsiniz.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu uygulamayı kullanmaya devam edemeyecekinizde, Azure statik Web Apps örneğini aşağıdaki adımlarla silebilirsiniz:

1. [Azure portalı](https://portal.azure.com) açın
1. En üst arama çubuğundan **My-First-static-blazor-App** araması yapın
1. Uygulama adı üzerinde seçin
1. **Sil** düğmesini seçin
1. Silme eylemini onaylamak için **Evet** ' i seçin

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Kimlik doğrulaması ve yetkilendirme](./authentication-authorization.md)
