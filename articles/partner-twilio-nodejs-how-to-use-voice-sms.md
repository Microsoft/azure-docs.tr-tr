---
title: Azure 'da Voice, VoIP ve SMS mesajlaşması için Twilio kullanma
description: Azure 'da bir telefon araması yapmayı ve Twilio API hizmetiyle SMS iletisi göndermenizi öğrenin. Node. js içinde yazılan kod örnekleri.
services: ''
documentationcenter: nodejs
author: georgewallace
ms.assetid: f558cbbd-13d2-416f-b9b1-33a99c426af9
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 11/25/2014
ms.author: gwallace
ms.openlocfilehash: 164bedffcf9a1aca9f1fa46dea254fb928abcf04
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/20/2019
ms.locfileid: "69637262"
---
# <a name="using-twilio-for-voice-voip-and-sms-messaging-in-azure"></a>Azure 'da Voice, VoIP ve SMS mesajlaşması için Twilio kullanma
Bu kılavuzda, Azure 'da Twilio ve Node. js ile iletişim kuran uygulamalar oluşturma işlemlerinin nasıl yapılacağı gösterilmiştir.

<a id="whatis"/>

## <a name="what-is-twilio"></a>Twilio nedir?
Twilio, geliştiricilerin telefon aramaları yapmasını ve almasını, SMS mesajları göndermesini ve almasını ve VoIP 'e, tarayıcı tabanlı ve yerel mobil uygulamalara çağrı yapmasını kolaylaştıran bir API platformudur. Bu, içinde kullanılmadan önce bunun nasıl çalıştığını kısaca ele alalım.

### <a name="receiving-calls-and-text-messages"></a>Aramalar ve metin Iletileri alma
Twilio, geliştiricilerin hem çağrı hem de SMS mesajları göndermek ve almak için kullanılabilecek [programlanabilir telefon numaralarını satın][purchase_phone] almasına olanak tanır. Bir Twilio numarası gelen bir çağrı veya metin aldığında, Twilio, Web uygulamanızı bir HTTP POST veya GET isteği göndererek, çağrının veya metnin nasıl işleneceği hakkında yönergeler ister. Sunucunuz [Twiml][twiml]Ile Twilio HTTP isteğine yanıt verir. Bu, bir çağrının veya metnin nasıl işleneceği hakkında yönergeler içeren basıt bir XML etiketleri kümesidir. Yalnızca birkaç dakika içinde TwiML örnekleri görülecektir.

### <a name="making-calls-and-sending-text-messages"></a>Çağrı yapma ve SMS mesajı gönderme
Geliştiriciler Twilio Web hizmeti API 'sine HTTP istekleri yaparak, SMS mesajları gönderebilir veya giden telefon aramaları başlatabilir. Giden çağrılar için, geliştirici bağlantı kurulduktan sonra giden çağrının nasıl işleneceği için TwiML yönergeleri döndüren bir URL de belirtmelidir.

### <a name="embedding-voip-capabilities-in-ui-code-javascript-ios-or-android"></a>Kullanıcı arabirimi kodunda VoIP özelliklerini katıştırma (JavaScript, iOS veya Android)
Twilio, herhangi bir Masaüstü Web tarayıcısını, iOS uygulamasını veya Android uygulamasını bir VoIP telefonuna dönüştürmek için bir istemci tarafı SDK 'Sı sağlar. Bu makalede, tarayıcıda çağıran VoIP kullanma konusuna odaklanacağız. Tarayıcıda çalışan *Twilio JavaScript SDK 'sına* ek olarak, JavaScript istemcisine "yetenek belirteci" vermek için bir sunucu tarafı uygulamanın (node. js uygulamamız) kullanılması gerekir. [Twilio dev blogu üzerinde][voipnode]Node. js ile VoIP kullanma hakkında daha fazla bilgi edinebilirsiniz.

<a id="signup"/>

## <a name="sign-up-for-twilio-microsoft-discount"></a>Twilio 'ye kaydolma (Microsoft Indirim)
Twilio hizmetlerini kullanmadan önce [bir hesap için kaydolmanız][signup]gerekir. Microsoft Azure müşteriler özel bir indirim elde [edin. buraya kaydolduğunuzdan emin olun][signup]!

<a id="azuresite"/>

## <a name="create-and-deploy-a-nodejs-azure-website"></a>Node. js Azure Web sitesi oluşturma ve dağıtma
Daha sonra, Azure üzerinde çalışan bir Node. js web sitesi oluşturmanız gerekecektir. [Bu işlemi gerçekleştirmek için resmi belgeler burada yer alır][azure_new_site]. Yüksek düzeyde, şunları yapabilirsiniz:

* Bir Azure hesabına kaydolma, zaten yoksa
* Yeni bir Web sitesi oluşturmak için Azure Yönetici Konsolu 'nu kullanma
* Kaynak denetimi desteği ekleme (git 'i kullandığınızı varsayacağız)
* Basit Node. `server.js` js web uygulaması ile dosya oluşturma
* Bu basit uygulamayı Azure 'a dağıtma

<a id="twiliomodule"/>

## <a name="configure-the-twilio-module"></a>Twilio modülünü yapılandırma
Daha sonra, Twilio API 'sini kullanan basit bir Node. js uygulaması yazmaya başlayacağız. Başlamadan önce, Twilio hesabı kimlik bilgilerinizi yapılandırmamız gerekir.

### <a name="configuring-twilio-credentials-in-system-environment-variables"></a>Sistem ortam değişkenlerinde Twilio kimlik bilgilerini yapılandırma
Kimliği doğrulanmış istekleri Twilio arka ucuna karşı yapmak için, Twilio hesabımızın Kullanıcı adı ve parola olarak işlev gösteren hesap SID 'SI ve kimlik doğrulama belirtecimize ihtiyacımız var. Bunları Azure 'da node modülü ile kullanmak üzere yapılandırmanın en güvenli yolu, doğrudan Azure Yönetici Konsolu 'nda ayarlayabileceğiniz sistem ortam değişkenleri aracılığıyla yapılır.

Node. js web sitenizi seçin ve "YAPıLANDıR" bağlantısına tıklayın.  Bir bit aşağı kaydırırsanız, uygulamanız için yapılandırma özelliklerini ayarlayabileceğiniz bir alan görürsünüz.  Twilio hesabı kimlik bilgilerinizi ([Twilio konsolunuzun içinde bulunur][twilio_console]) gösterildiği gibi girin; bunları `TWILIO_ACCOUNT_SID` sırasıyla, ve `TWILIO_AUTH_TOKEN`olarak girdiğinizden emin olun:

![Azure Yönetici Konsolu][azure-admin-console]

Bu değişkenleri yapılandırdıktan sonra, Azure konsolu 'nda uygulamanızı yeniden başlatın.

### <a name="declaring-the-twilio-module-in-packagejson"></a>Twilio modülünü Package. JSON içinde bildirme
Daha sonra, [NPM]aracılığıyla düğüm modülü bağımlılıklarımızı yönetmek için bir Package. JSON oluşturmamız gerekiyor. `server.js` *Azure/Node. js* öğreticisinde oluşturduğunuz dosyayla aynı düzeyde adlı `package.json`bir dosya oluşturun.  Bu dosyanın içine şunu girin:

```json
{
  "name": "application-name",
  "version": "0.0.1",
  "private": true,
  "scripts": {
    "start": "node server"
  },
  "dependencies": {
    "body-parser": "^1.16.1",
    "ejs": "^2.5.5",
    "errorhandler": "^1.5.0",
    "express": "^4.14.1",
    "morgan": "^1.8.1",
    "twilio": "^2.11.1"
  }
}
```

Bu, Twilio modülünü bağımlılık olarak, popüler [Express Web çerçevesi][express] ve EJS şablon altyapısını bildirir.  Artık tüm bunlar için bir kod yazalım!

<a id="makecall"/>

## <a name="make-an-outbound-call"></a>Giden bir çağrı yapın
Seçtiğimiz bir sayıya çağrı oluşturacak basit bir form oluşturalım. `server.js`Açın ve aşağıdaki kodu girin. Burada "CHANGE_ME" ifadesinin olduğunu ve Azure Web sitenizin adını buraya yerleştirdiğini aklınızda bulabilirsiniz:

```javascript
// Module dependencies
const express = require('express');
const path = require('path');
const http = require('http');
const twilio = require('twilio');
const logger = require('morgan');
const bodyParser = require('body-parser');
const errorHandler = require('errorhandler');
const accountSid = process.env.TWILIO_ACCOUNT_SID;
const authToken = process.env.TWILIO_AUTH_TOKEN;
// Create Express web application
const app = express();

// Express configuration
app.set('port', process.env.PORT || 3000);
app.set('views', __dirname + '/views');
app.set('view engine', 'ejs');
app.use(logger('tiny'));
app.use(bodyParser.urlencoded({ extended: false }))
app.use(bodyParser.json())
app.use(express.static(path.join(__dirname, 'public')));

if (app.get('env') !== 'production') {
  app.use(errorHandler());
}

// Render an HTML user interface for the application's home page
app.get('/', (request, response) => response.render('index'));

// Handle the form POST to place a call
app.post('/call', (request, response) => {
  var client = twilio(accountSid, authToken);

  client.makeCall({
    // make a call to this number
    to:request.body.number,

    // Change to a Twilio number you bought - see:
    // https://www.twilio.com/console/phone-numbers/incoming
    from:'+15558675309',

    // A URL in our app which generates TwiML
    // Change "CHANGE_ME" to your app's name
    url:'https://CHANGE_ME.azurewebsites.net/outbound_call'
  }, () => {
      // Go back to the home page
      response.redirect('/');
  });
});

// Generate TwiML to handle an outbound call
app.post('/outbound_call', (request, response) => {
  var twiml = new twilio.TwimlResponse();

  // Say a message to the call's receiver
  twiml.say('hello - thanks for checking out Twilio and Azure', {
      voice:'woman'
  });

  response.set('Content-Type', 'text/xml');
  response.send(twiml.toString());
});

// Start server
app.listen(app.get('port'), function(){
  console.log(`Express server listening on port ${app.get('port')}`);
});
```

Sonra, bu dizinin içinde adlı `views` bir dizin oluşturun, aşağıdaki içeriklerle adlı `index.ejs` bir dosya oluşturun:

```html
<!DOCTYPE html>
<html>
<head>
  <title>Twilio Test</title>
  <style>
    input { height:20px; width:300px; font-size:18px; margin:5px; padding:5px; }
  </style>
</head>
<body>
  <h1>Twilio Test</h1>
  <form action="/call" method="POST">
      <input placeholder="Enter a phone number" name="number"/>
      <br/>
      <input type="submit" value="Call the number above"/>
  </form>
</body>
</html>
```

Şimdi, Web sitenizi Azure 'a dağıtın ve evinizin açın. Telefon numaranızı metin alanına girebilmeniz ve Twilio numaranızı bir çağrı alabilmesi gerekir!

<a id="sendmessage"/>

## <a name="send-an-sms-message"></a>SMS Iletisi gönder
Şimdi bir kısa mesaj göndermek için bir kullanıcı arabirimi ve form işleme mantığı ayarlayalım. Açın ve son `app.post`çağrısından sonra aşağıdaki kodu ekleyin: `server.js`

```javascript
app.post('/sms', (request, response) => {
  const client = twilio(accountSid, authToken);

  client.sendSms({
      // send a text to this number
      to:request.body.number,

      // A Twilio number you bought - see:
      // https://www.twilio.com/console/phone-numbers/incoming
      from:'+15558675309',

      // The body of the text message
      body: request.body.message

  }, () => {
      // Go back to the home page
      response.redirect('/');
  });
});
```

' `views/index.ejs`De, bir sayı ve bir kısa mesaj göndermek için ilk birinin altına başka bir form ekleyin:

```html
<form action="/sms" method="POST">
  <input placeholder="Enter a phone number" name="number"/>
  <br/>
  <input placeholder="Enter a message to send" name="message"/>
  <br/>
  <input type="submit" value="Send text to the number above"/>
</form>
```

Uygulamanızı Azure 'a yeniden dağıtın ve artık bu formu gönderebilmeniz ve kendinize (ya da en yakın arkadaşlarınızın herhangi birine) bir kısa mesaj gönderebilmelisiniz!

<a id="nextsteps"/>

## <a name="next-steps"></a>Sonraki Adımlar
Artık, iletişim kuran uygulamalar oluşturmak için Node. js ve Twilio kullanma hakkında temel bilgileri öğrendiniz. Ancak bu örnekler, Twilio ve Node. js ile mümkün olduğunca fazla şeyi baştan halce. Node. js ile Twilio kullanma hakkında daha fazla bilgi için aşağıdaki kaynaklara göz atın:

* [Resmi modül belgeleri][docs]
* [Node. js uygulamalarıyla VoIP hakkında öğretici][voipnode]
* [Votr-Node. js ve Couşdb (üç bölümden oluşan) ile gerçek zamanlı bir SMS oylama uygulaması][votr]
* [Node. js ile tarayıcıda programlama ile eşleştirme][pair]

Azure 'da Node. js ve Twilio 'ı beğeneceğinizi umuyoruz!

[purchase_phone]: https://www.twilio.com/console/phone-numbers/search
[twiml]: https://www.twilio.com/docs/api/twiml
[signup]: https://ahoy.twilio.com/azure
[azure_new_site]: app-service/app-service-web-get-started-nodejs.md
[twilio_console]: https://www.twilio.com/console
[NPM]: https://npmjs.org
[express]: https://expressjs.com
[voipnode]: https://www.twilio.com/blog/2013/04/introduction-to-twilio-client-with-node-js.html
[docs]: https://www.twilio.com/docs/libraries/reference/twilio-node/
[votr]: https://www.twilio.com/blog/2012/09/building-a-real-time-sms-voting-app-part-1-node-js-couchdb.html
[pair]: https://www.twilio.com/blog/2013/06/pair-programming-in-the-browser-with-twilio.html
[azure-admin-console]: ./media/partner-twilio-nodejs-how-to-use-voice-sms/twilio_1.png
