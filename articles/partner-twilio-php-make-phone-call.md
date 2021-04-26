---
title: Twilio 'dan telefon görüşmesi yapma (PHP) | Microsoft Docs
description: Azure 'da bir telefon araması yapmayı ve Twilio API hizmetiyle SMS iletisi göndermenizi öğrenin. Örnekler PHP uygulaması içindir.
documentationcenter: php
services: ''
author: georgewallace
ms.assetid: 44e35adc-be06-4700-beee-8c9e2c20c540
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 11/25/2014
ms.author: gwallace
ms.openlocfilehash: ef0642b26898249d78f5103e8a8f80198887bed9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95542533"
---
# <a name="how-to-make-a-phone-call-using-twilio-in-a-php-application-on-azure"></a>Azure 'da PHP uygulamasında Twilio kullanarak telefon araması yapma
Aşağıdaki örnekte, Azure 'da barındırılan bir PHP Web sayfasından çağrı yapmak için Twilio nasıl kullanabileceğiniz gösterilmektedir. Elde edilen uygulama, aşağıdaki ekran görüntüsünde gösterildiği gibi kullanıcıdan telefon araması değerlerini ister.

![Twilio ve PHP kullanarak Azure çağrı formu][twilio_php]

Bu konudaki kodu kullanmak için aşağıdakileri yapmanız gerekir:

1. [Twilio konsolınızdan][twilio_console]bir Twilio hesabı ve kimlik doğrulama belirteci alın. Twilio kullanmaya başlamak için fiyatlandırmayı değerlendirin [https://www.twilio.com/pricing][twilio_pricing] . Bir deneme hesabı için ' de kaydolabilirsiniz [https://www.twilio.com/try-twilio][try_twilio] .
2. [Php Için Twilio kitaplığını](https://github.com/twilio/twilio-php) edinin veya BIR PEAR paketi olarak yükler. Daha fazla bilgi için bkz. [Readme dosyası](https://github.com/twilio/twilio-php/blob/master/README.md).
3. PHP için Azure SDK 'sını yükler. 
<!-- For an overview of the SDK and instructions on installing it, see [Set up the Azure SDK for PHP](./app-service/quickstart-php.md) -->

## <a name="create-a-web-form-for-making-a-call"></a>Çağrı yapmak için bir Web formu oluşturma
Aşağıdaki HTML kodu, bir çağrı yapmak için Kullanıcı verilerini alan bir Web sayfasının (**callform.html**) nasıl oluşturulacağını gösterir:

```html
<!DOCTYPE html>
<html>
<head>
  <title>Automated call form</title>
</head>
<body>
  <h1>Automated Call Form</h1>
  <p>Fill in all fields and click <b>Make this call</b>.</p>
  <form action="makecall.php" method="post">
    <table>
      <tr>
        <td>To:</td>
        <td><input name="callTo" size="50" type="text" value=""></td>
      </tr>
      <tr>
        <td>From:</td>
        <td><input name="callFrom" size="50" type="text" value=""></td>
      </tr>
      <tr>
        <td>Call message:</td>
        <td><input name="callText" size="100" type="text" value="Hello. This is the call text. Good bye."></td>
      </tr>
      <tr>
        <td colspan="2"><input type="submit" value="Make this call"></td>
      </tr>
    </table>
  </form><br>
</body>
</html>
```

## <a name="create-the-code-to-make-the-call"></a>Çağrıyı yapmak için kodu oluşturma
Aşağıdaki kod, Kullanıcı **callform.html** tarafından gösterilen formu gönderdiğinde çağrılan **MakeCall. php**'nin nasıl oluşturulacağını gösterir. Aşağıda gösterilen kod çağrı iletisini oluşturur ve çağrıyı oluşturur. Ayrıca, Twilio hesabınızı ve kimlik doğrulama belirtecinizi, **$Sid** atanan yer tutucu değerler yerine [Twilio konsolundan][twilio_console] kullandığınızdan emin olun ve aşağıdaki kodda **$token** .

```html
<html>
<head><title>Making call...</title></head>
<body>
<p>Your call is being made.</p>

<?php
require_once 'path/to/vendor/autoload.php';

$sid   = "your_account_sid";
$token = "your_authentication_token";

$from_number = $_POST['callFrom']; // Calls must be made from a registered Twilio number.
$to_number   = $_POST['callTo'];
$message     = $_POST['callText'];

$client = new Twilio\Rest\Client($sid, $token);

$call = $client->calls->create(
            $to_number,
            $from_number,
            array('url' => https://twimlets.com/message?Message=' . urlencode($message))
        );

echo "Call status: " . $call->status . "<br />";
echo "URI resource: " . $call->uri . "<br />";
?>
</body>
</html>
```

Bu çağrıyı yapmanın yanı sıra, aşağıdaki görüntüde gösterildiği gibi, **MakeCall. php** bazı çağrı meta verilerini görüntüler. Çağrı meta verileri hakkında daha fazla bilgi için bkz [https://www.twilio.com/docs/api/rest/call#instance-properties][twilio_call_properties] ..

![Twilio ve PHP kullanarak Azure çağrı yanıtı][twilio_php_response]

## <a name="run-the-application"></a>Uygulamayı çalıştırma
Bir sonraki adım, [uygulamanızı git Ile Azure Web Apps dağıtmaktır](app-service/quickstart-php.md) (Bununla birlikte, ilişkili tüm bilgiler olmasa da). 

## <a name="next-steps"></a>Sonraki adımlar
Bu kod, Azure 'da PHP 'de Twilio kullanarak temel işlevselliği göstermek için verilmiştir. Üretim sırasında Azure 'a dağıtım yapmadan önce, daha fazla hata işleme veya diğer özellik eklemek isteyebilirsiniz. Örnek:

* Bir Web formu kullanmak yerine, telefon numaralarını depolamak ve metin çağırmak için Azure Storage bloblarını veya SQL veritabanını kullanabilirsiniz. PHP 'de Azure Storage bloblarını kullanma hakkında daha fazla bilgi için bkz. [php uygulamalarıyla Azure Storage kullanma][howto_blob_storage_php]. PHP 'de SQL veritabanı kullanma hakkında daha fazla bilgi için bkz. [php UYGULAMALARıYLA SQL Database kullanma][howto_sql_azure_php].
* **MakeCall. php** kodu, [https://twimlets.com/message][twimlet_message_url] çağrıya nasıl devam edeceğine Twilio bildiren bir Twilio Işaretleme dili (twiml) yanıtı sağlamak için TWILIO tarafından sağlanmış URL () kullanır. Örneğin, döndürülen TwiML, `<Say>` metnin çağrı alıcısına konuşmasına neden olan bir fiil içerebilir. Twilio tarafından sağlanmış URL 'yi kullanmak yerine, Twilio 'ın isteğine yanıt vermek için kendi hizmetinizi oluşturabilirsiniz; daha fazla bilgi için bkz. [php 'de ses ve SMS özellikleri Için Twilio kullanma][howto_twilio_voice_sms_php]. TwiML hakkında daha fazla bilgi adresinde bulunabilir [https://www.twilio.com/docs/api/twiml][twiml] ve hakkında daha fazla bilgi `<Say>` ve diğer Twilio fiilleri hakkında daha fazla bilgi bulabilirsiniz [https://www.twilio.com/docs/api/twiml/say][twilio_say] .
* Adresindeki Twilio güvenlik kılavuzlarını okuyun [https://www.twilio.com/docs/security][twilio_docs_security] .

Twilio hakkında daha fazla bilgi için bkz [https://www.twilio.com/docs][twilio_docs] ..

## <a name="see-also"></a>Ayrıca Bkz.
* [PHP 'de ses ve SMS özellikleri için Twilio kullanma](partner-twilio-php-how-to-use-voice-sms.md)

[twilio_console]: https://www.twilio.com/console
[twilio_pricing]: https://www.twilio.com/pricing
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_api]: https://www.twilio.com/docs/api
[verify_phone]: https://www.twilio.com/console/phone-numbers/verified
[twimlet_message_url]: https://twimlets.com/message
[twiml]: https://www.twilio.com/docs/api/twiml
[twilio_api_service]: https://api.twilio.com
[build_php_azure_app]: http://azurephp.interoperabilitybridges.com/articles/build-and-deploy-a-windows-azure-php-application
[howto_twilio_voice_sms_php]: partner-twilio-php-how-to-use-voice-sms.md
[howto_blob_storage_php]: ./storage/blobs/storage-quickstart-blobs-php.md
[howto_sql_azure_php]: ./azure-sql/database/connect-query-content-reference-guide.md
[twilio_call_properties]: https://www.twilio.com/docs/api/rest/call#instance-properties
[twilio_docs_security]: https://www.twilio.com/docs/security
[twilio_docs]: https://www.twilio.com/docs
[twilio_say]: https://www.twilio.com/docs/api/twiml/say
[ssl_validation]: http://readthedocs.org/docs/twilio-php/en/latest/usage/rest.html
[twilio_php]: ./media/partner-twilio-php-make-phone-call/WA_TwilioPHPCallForm.jpg
[twilio_php_response]: ./media/partner-twilio-php-make-phone-call/WA_TwilioPHPMakeCall.jpg
[twilio_php_github]: https://github.com/twilio/twilio-php