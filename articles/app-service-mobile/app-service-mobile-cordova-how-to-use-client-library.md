---
title: Azure için Apache Cordova eklentisi 'ni kullanma Mobile Apps
description: Azure için Apache Cordova eklentisi 'ni kullanma Mobile Apps
services: app-service\mobile
documentationcenter: javascript
author: elamalani
manager: crdun
editor: ''
ms.assetid: a56a1ce4-de0c-4f3c-8763-66252c52aa59
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-html
ms.devlang: javascript
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 25bff77da6c2bd89916fe40ebb153ff9631e62eb
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388799"
---
# <a name="how-to-use-apache-cordova-client-library-for-azure-mobile-apps"></a>Azure için Apache Cordova istemci kitaplığı 'nı kullanma Mobile Apps
[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

> [!NOTE]
> Visual Studio App Center mobil uygulama dağıtımında merkezi konumdaki uçtan uca ve tümleşik hizmetleri destekler. Geliştiriciler Sürekli Tümleştirme ve Teslim işlem hattını ayarlamak için **Oluşturma**, **Test** ve **Dağıtım** hizmetlerini kullanabilir. Uygulama dağıtıldıktan sonra, geliştiriciler **Analiz** ve **Tanılama** hizmetlerini kullanarak uygulamanın durumunu ve kullanımını izleyebilir, **Gönderme** hizmetini kullanarak kullanıcılarla etkileşim kurabilir. Geliştiriciler ayrıca kullanıcıların kimliğini doğrulamak için **Kimlik Doğrulaması**'ndan ve uygulama verilerini bulutta kalıcı hale getirmek ve eşitlemek için **Veri** hizmetinden yararlanabilir.
>
> Mobil uygulamanızda bulut hizmetlerini tümleştirmek istiyorsanız bugün [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) kaydolun.

## <a name="overview"></a>Genel Bakış
Bu kılavuz, [Azure Mobile Apps Apache Cordova eklentisi]kullanarak genel senaryoları gerçekleştirmenizi öğretir. Azure Mobile Apps 'yi yeni kullanıyorsanız, ilk olarak [azure Mobile Apps hızlı başlangıç] tamamladıktan sonra arka uç oluşturun, tablo oluşturun ve önceden oluşturulmuş bir Apache Cordova projesini indirin. Bu kılavuzda, istemci tarafı Apache Cordova eklentisine odaklanıyoruz.

## <a name="supported-platforms"></a>Desteklenen platformlar
Bu SDK, iOS, Android ve Windows cihazlarında Apache Cordova v 6.0.0 ve üstünü destekler.  Platform desteği aşağıdaki gibidir:

* Android API 19-24 (KitKat üzerinden Nougat).
* iOS sürümleri 8,0 ve üzeri.
* Windows Phone 8,1.
* Evrensel Windows Platformu.

## <a name="Setup"></a>Kurulum ve Önkoşullar
Bu kılavuzda, bir tablo ile arka uç oluşturduğunuzu varsayılmaktadır. Bu kılavuzda, tablonun bu öğreticilerde bulunan tablolarla aynı şemaya sahip olduğu varsayılır. Bu kılavuzda, kodunuza Apache Cordova eklentisi eklediğinizi de varsayılmaktadır.  Bunu yapmadıysanız, komut satırında projenize Apache Cordova eklentisini ekleyebilirsiniz:

```
cordova plugin add cordova-plugin-ms-azure-mobile-apps
```

[ilk Apache Cordova uygulamanız]oluşturma hakkında daha fazla bilgi için belgelerine bakın.

## <a name="ionic"></a>Ionic v2 uygulaması ayarlama

Bir Ionic v2 projesini doğru şekilde yapılandırmak için öncelikle temel bir uygulama oluşturun ve Cordova eklentisini ekleyin:

```
ionic start projectName --v2
cd projectName
ionic plugin add cordova-plugin-ms-azure-mobile-apps
```

İstemci nesnesini oluşturmak için `app.component.ts` ' a aşağıdaki satırları ekleyin:

```typescript
declare var WindowsAzure: any;
var client = new WindowsAzure.MobileServiceClient("https://yoursite.azurewebsites.net");
```

Projeyi artık tarayıcıda derleyip çalıştırabilirsiniz:

```
ionic platform add browser
ionic run browser
```

Azure Mobile Apps Cordova eklentisi hem Ionic v1 hem de v2 uygulamalarını destekler.  Yalnızca Ionic v2 uygulamaları `WindowsAzure` nesnesi için ek bildirim gerektirir.

[!INCLUDE [app-service-mobile-html-js-library.md](../../includes/app-service-mobile-html-js-library.md)]

## <a name="auth"></a>Nasıl yapılır: kullanıcıların kimliğini doğrulama
Azure App Service, farklı dış kimlik sağlayıcıları kullanarak uygulama kullanıcılarının kimlik doğrulamasını ve yetkilendiriyi destekler: Facebook, Google, Microsoft hesabı ve Twitter. Belirli işlemlere erişimi yalnızca kimliği doğrulanmış kullanıcılarla kısıtlamak için tablolar üzerinde izinler belirleyebilirsiniz. Ayrıca, sunucu betiklerine yetkilendirme kuralları uygulamak için kimliği doğrulanmış kullanıcıların kimliğini de kullanabilirsiniz. Daha fazla bilgi için [kimlik doğrulaması ile çalışmaya başlama] öğreticisine bakın.

Apache Cordova uygulamasında kimlik doğrulaması kullanırken, aşağıdaki Cordova eklentileri kullanılabilir olmalıdır:

* [Cordova-eklenti-cihaz]
* [Cordova-Plugin-ınappbrowser]

İki kimlik doğrulama akışı desteklenir: sunucu akışı ve istemci akışı.  Sunucu akışı, sağlayıcının web kimlik doğrulama arabirimini temel aldığından en basit kimlik doğrulama deneyimini sağlar. İstemci akışı, sağlayıcıya özel cihaza özgü SDK 'lara dayandığından, tek oturum açma gibi cihaza özgü yetenekler ile daha derin tümleştirme sağlar.

[!INCLUDE [app-service-mobile-html-js-auth-library.md](../../includes/app-service-mobile-html-js-auth-library.md)]

### <a name="configure-external-redirect-urls"></a>Nasıl yapılır: mobil App Service dış yeniden yönlendirme URL 'Leri için yapılandırma.
Birçok Apache Cordova uygulama türü, OAuth Kullanıcı arabirimi akışlarını işlemek için bir geri döngü özelliği kullanır.  Kimlik doğrulama hizmeti yalnızca varsayılan olarak hizmetinize nasıl yararlanacağını öğrendiğinden, localhost üzerinde OAuth Kullanıcı arabirimi akışları sorunlara neden olur.  Sorunlu OAuth Kullanıcı arabirimi akışlarına örnek olarak şunlar verilebilir:

* Ripple öykünücüsü.
* Ionic ile canlı yeniden yükleme.
* Mobil arka ucunu yerel olarak çalıştırma
* Mobil arka ucu, kimlik doğrulaması sağlamaktan farklı bir Azure App Service çalışıyor.

Yapılandırmaya yerel ayarlarınızı eklemek için aşağıdaki yönergeleri izleyin:

1. [Azure portalda]’nda oturum açın
2. **Tüm kaynaklar** veya **uygulama hizmetleri** ' ni seçin ve ardından mobil uygulamanızın adına tıklayın.
3. **Araçlar** 'a tıklayın
4. GÖZLEMLEME menüsünde **Kaynak Gezgini** ' ne tıklayın ve ardından **Git**' e tıklayın.  Yeni bir pencere veya sekme açılır.
5. Sol taraftaki gezinmede sitenizin **config**, **authsettings öğesine tıklayın** düğümlerini genişletin.
6. **Düzenle** 'ye tıklayın
7. "AllowedExternalRedirectUrls" öğesini arayın.  Null ya da bir değerler dizisine ayarlanmış olabilir.  Değeri şu değere değiştirin:

         "allowedExternalRedirectUrls": [
             "http://localhost:3000",
             "https://localhost:3000"
         ],

    URL 'Leri hizmetinizin URL 'Leriyle değiştirin.  Örnekler arasında `http://localhost:3000` (node. js örnek hizmeti için) veya `http://localhost:4400` (Ripple hizmeti için) verilebilir.  Ancak, bu URL 'Ler örnektir; örneklerde bahsedilen hizmetler de dahil olmak üzere durumunuz farklı olabilir.
8. Ekranın sağ üst köşesindeki **oku/yaz** düğmesine tıklayın.
9. Yeşil **PUT** düğmesine tıklayın.

Ayarlar bu noktada kaydedilir.  Ayarlar kaydetme işlemi tamamlanana kadar tarayıcı penceresini kapatmayın.
Ayrıca, bu geri döngü URL 'Lerini App Service CORS ayarlarına ekleyin:

1. [Azure portalda]’nda oturum açın
2. **Tüm kaynaklar** veya **uygulama hizmetleri** ' ni seçin ve ardından mobil uygulamanızın adına tıklayın.
3. Ayarlar dikey penceresi otomatik olarak açılır.  Aksi takdirde, **Tüm ayarlar**' a tıklayın.
4. API menüsünde **CORS** ' ye tıklayın.
5. Belirtilen kutuya eklemek istediğiniz URL 'YI girin ve ENTER 'a basın.
6. Gerektiğinde ek URL 'Ler girin.
7. Ayarları kaydetmek için **Kaydet**’e tıklayın.

Yeni ayarların etkili olması yaklaşık 10-15 saniye sürer.

## <a name="register-for-push"></a>Nasıl yapılır: anında iletme bildirimleri için kaydolma
Anında iletme bildirimlerini işlemek için [PhoneGap-eklenti-gönderim] ' i yükleme.  Bu eklenti, komut satırında `cordova plugin add` komutu kullanılarak veya Visual Studio içindeki Git eklentisi yükleyicisi aracılığıyla kolayca eklenebilir.  Apache Cordova uygulamanızda aşağıdaki kod, cihazınızı anında iletme bildirimleri için kaydeder:

```javascript
var pushOptions = {
    android: {
        senderId: '<from-gcm-console>'
    },
    ios: {
        alert: true,
        badge: true,
        sound: true
    },
    windows: {
    }
};
pushHandler = PushNotification.init(pushOptions);

pushHandler.on('registration', function (data) {
    registrationId = data.registrationId;
    // For cross-platform, you can use the device plugin to determine the device
    // Best is to use device.platform
    var name = 'gcm'; // For android - default
    if (device.platform.toLowerCase() === 'ios')
        name = 'apns';
    if (device.platform.toLowerCase().substring(0, 3) === 'win')
        name = 'wns';
    client.push.register(name, registrationId);
});

pushHandler.on('notification', function (data) {
    // data is an object and is whatever is sent by the PNS - check the format
    // for your particular PNS
});

pushHandler.on('error', function (error) {
    // Handle errors
});
```

Sunucudan anında iletme bildirimleri göndermek için Notification Hubs SDK 'sını kullanın.  Anında iletme bildirimlerini hiçbir şekilde doğrudan istemcilerden göndermeyin. Bunun yapılması, Notification Hubs veya PNS 'ye karşı bir hizmet reddi saldırısı tetiklenmesi için kullanılabilir.  PNS, bu tür saldırılardan kaynaklanan trafiğinizi yasaklayamaz.

## <a name="more-information"></a>Daha fazla bilgi

[API belgelerimizde](https://azure.github.io/azure-mobile-apps-js-client/)ayrıntılı API ayrıntılarını bulabilirsiniz.

<!-- URLs. -->
[Azure portalda]: https://portal.azure.com
[Azure Mobile Apps hızlı başlangıç]: app-service-mobile-cordova-get-started.md
[Kimlik doğrulaması ile çalışmaya başlama]: app-service-mobile-cordova-get-started-users.md
[Add authentication to your app]: app-service-mobile-cordova-get-started-users.md

[Azure Mobile Apps Apache Cordova eklentisi]: https://www.npmjs.com/package/cordova-plugin-ms-azure-mobile-apps
[ilk Apache Cordova uygulamanız]: https://cordova.apache.org/#getstarted
[phonegap-facebook-plugin]: https://github.com/wizcorp/phonegap-facebook-plugin
[PhoneGap-eklenti-gönderim]: https://www.npmjs.com/package/phonegap-plugin-push
[Cordova-eklenti-cihaz]: https://www.npmjs.com/package/cordova-plugin-device
[Cordova-Plugin-ınappbrowser]: https://www.npmjs.com/package/cordova-plugin-inappbrowser
[Query object documentation]: https://msdn.microsoft.com/library/azure/jj613353.aspx
