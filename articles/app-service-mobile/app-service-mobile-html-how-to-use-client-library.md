---
title: Azure için JavaScript SDK 'sını kullanma Mobile Apps
description: Azure Mobile Apps için v kullanma
services: app-service\mobile
documentationcenter: javascript
author: elamalani
manager: crdun
editor: ''
ms.assetid: 53b78965-caa3-4b22-bb67-5bd5c19d03c4
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: html
ms.devlang: javascript
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 8a22de6b3e518bb3c9392d7fd64411969b32f33f
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388726"
---
# <a name="how-to-use-the-javascript-client-library-for-azure-mobile-apps"></a>Azure için JavaScript istemci kitaplığı 'nı kullanma Mobile Apps
[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

> [!NOTE]
> Visual Studio App Center mobil uygulama dağıtımında merkezi konumdaki uçtan uca ve tümleşik hizmetleri destekler. Geliştiriciler Sürekli Tümleştirme ve Teslim işlem hattını ayarlamak için **Oluşturma**, **Test** ve **Dağıtım** hizmetlerini kullanabilir. Uygulama dağıtıldıktan sonra, geliştiriciler **Analiz** ve **Tanılama** hizmetlerini kullanarak uygulamanın durumunu ve kullanımını izleyebilir, **Gönderme** hizmetini kullanarak kullanıcılarla etkileşim kurabilir. Geliştiriciler ayrıca kullanıcıların kimliğini doğrulamak için **Kimlik Doğrulaması**'ndan ve uygulama verilerini bulutta kalıcı hale getirmek ve eşitlemek için **Veri** hizmetinden yararlanabilir.
>
> Mobil uygulamanızda bulut hizmetlerini tümleştirmek istiyorsanız bugün [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) kaydolun.

## <a name="overview"></a>Genel Bakış
Bu kılavuz, [Azure Mobile Apps için JavaScript SDK 'Sı]kullanarak genel senaryolar gerçekleştirmenizi öğretir. Azure Mobile Apps 'yi yeni kullanıyorsanız, ilk olarak [azure Mobile Apps hızlı başlangıç] tamamladıktan sonra arka uç oluşturun ve bir tablo oluşturun. Bu kılavuzda, HTML/JavaScript Web uygulamalarında mobil arka ucunu kullanmaya odaklanıyoruz.

## <a name="supported-platforms"></a>Desteklenen platformlar
Tarayıcı desteğini büyük tarayıcıların güncel ve son sürümleriyle sınırlandırıyoruz: Google Chrome, Microsoft Edge, Microsoft Internet Explorer ve Mozilla Firefox.  SDK 'nın herhangi bir görece modern tarayıcıyla çalışmasını bekledik.

Paket Universal JavaScript modülü olarak dağıtılır, bu nedenle Globals, AMD ve CommonJS biçimlerini destekler.

## <a name="Setup"></a>Kurulum ve Önkoşullar
Bu kılavuzda, bir tablo ile arka uç oluşturduğunuzu varsayılmaktadır. Bu kılavuzda, tablonun bu öğreticilerde bulunan tablolarla aynı şemaya sahip olduğu varsayılır.

Azure Mobile Apps JavaScript SDK 'sını yüklemek `npm` komutu aracılığıyla yapılabilir:

```
npm install azure-mobile-apps-client --save
```

Kitaplık Ayrıca, Browserbelirt ve WebPack gibi CommonJS ortamlarında ve bir AMD kitaplığı olarak bir ES2015 modülü olarak da kullanılabilir.  Örnek:

```javascript
// For ECMAScript 5.1 CommonJS
var WindowsAzure = require('azure-mobile-apps-client');
// For ES2015 modules
import * as WindowsAzure from 'azure-mobile-apps-client';
```

Doğrudan CDN 'umuza indirerek SDK 'nın önceden oluşturulmuş bir sürümünü de kullanabilirsiniz:

```html
<script src="https://zumo.blob.core.windows.net/sdk/azure-mobile-apps-client.min.js"></script>
```

[!INCLUDE [app-service-mobile-html-js-library](../../includes/app-service-mobile-html-js-library.md)]

## <a name="auth"></a>Nasıl yapılır: kullanıcıların kimliğini doğrulama
Azure App Service, farklı dış kimlik sağlayıcıları kullanarak uygulama kullanıcılarının kimlik doğrulamasını ve yetkilendiriyi destekler: Facebook, Google, Microsoft hesabı ve Twitter. Belirli işlemlere erişimi yalnızca kimliği doğrulanmış kullanıcılarla kısıtlamak için tablolar üzerinde izinler belirleyebilirsiniz. Ayrıca, sunucu betiklerine yetkilendirme kuralları uygulamak için kimliği doğrulanmış kullanıcıların kimliğini de kullanabilirsiniz. Daha fazla bilgi için [kimlik doğrulaması ile çalışmaya başlama] öğreticisine bakın.

İki kimlik doğrulama akışı desteklenir: sunucu akışı ve istemci akışı.  Sunucu akışı, sağlayıcının web kimlik doğrulama arabirimini temel aldığından en basit kimlik doğrulama deneyimini sağlar. İstemci akışı, sağlayıcıya özgü SDK 'Ları temel aldığından, tek oturum açma gibi cihaza özgü yetenekler ile daha derin tümleştirme sağlar.

[!INCLUDE [app-service-mobile-html-js-auth-library](../../includes/app-service-mobile-html-js-auth-library.md)]

### <a name="configure-external-redirect-urls"></a>Nasıl yapılır: mobil App Service dış yeniden yönlendirme URL 'Leri için yapılandırma.
Birçok JavaScript uygulaması türü, OAuth UI akışlarını işlemek için bir geri döngü özelliği kullanır.  Bu yetenekler şunlardır:

* Hizmetinizi yerel olarak çalıştırma
* Ionic çerçevesiyle canlı yeniden yükleme kullanma
* Kimlik doğrulaması için App Service yeniden yönlendiriliyor.

Varsayılan olarak, App Service kimlik doğrulaması yalnızca mobil uygulama arka ucunuzun erişimine izin verecek şekilde yapılandırıldığından, yerel olarak çalıştırmak sorunlara yol açabilir. Sunucuyu yerel olarak çalıştırırken kimlik doğrulamasını etkinleştirmek üzere App Service ayarlarını değiştirmek için aşağıdaki adımları kullanın:

1. [Azure portalda]’nda oturum açın
2. Mobil uygulama arka ucunuza gidin.
3. **GELIŞTIRME araçları** menüsünde **Kaynak Gezgini** ' ni seçin.
4. Yeni bir sekmede veya penceresinde mobil uygulama arka ucunuzun kaynak Gezginini açmak için **Git** ' e tıklayın.
5. Uygulamanız için **config** > **authsettings öğesine tıklayın** düğümünü genişletin.
6. Kaynağı düzenlemeyi etkinleştirmek için **Düzenle** düğmesine tıklayın.
7. Null olması gereken **Allowedexternalredirecturls** öğesini bulun. URL 'nizi bir diziye ekleyin:

         "allowedExternalRedirectUrls": [
             "http://localhost:3000",
             "https://localhost:3000"
         ],

    Dizideki URL 'leri hizmetinizin URL 'Leriyle değiştirin, bu örnekte yerel Node. js örnek hizmeti için `http://localhost:3000` kullanılır. Ayrıca, uygulamanızın nasıl yapılandırıldığına bağlı olarak Ripple hizmeti veya diğer bir URL için `http://localhost:4400` ' ı da kullanabilirsiniz.
8. Sayfanın üst kısmında **oku/yaz**' a tıklayın ve ardından değişikliklerinizi kaydetmek için **Yerleştir** ' e tıklayın.

CORS beyaz listesi ayarlarına aynı geri döngü URL 'Lerini de eklemeniz gerekir:

1. [Azure portalda]geri gidin.
2. Mobil uygulama arka ucunuza gidin.
3. **API** menüsünde **CORS** ' ye tıklayın.
4. Her URL 'YI boş **Izin verilen** kaynaklar metin kutusuna girin.  Yeni bir metin kutusu oluşturulur.
5. **Kaydet** 'e tıklayın

Arka uç güncelleştirildikten sonra uygulamanızdaki yeni geri döngü URL 'Lerini kullanabilirsiniz.

<!-- URLs. -->
[Azure Mobile Apps hızlı başlangıç]: app-service-mobile-cordova-get-started.md
[Kimlik doğrulaması ile çalışmaya başlama]: app-service-mobile-cordova-get-started-users.md
[Add authentication to your app]: app-service-mobile-cordova-get-started-users.md

[Azure portalda]: https://portal.azure.com/
[Azure Mobile Apps için JavaScript SDK 'Sı]: https://www.npmjs.com/package/azure-mobile-apps-client
[Query object documentation]: https://msdn.microsoft.com/library/azure/jj613353.aspx
