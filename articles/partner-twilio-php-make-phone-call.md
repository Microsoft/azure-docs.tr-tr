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
ms.openlocfilehash: fb1623c4a409f1c6cba94bad56d773e166d2b182
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/20/2019
ms.locfileid: "69637316"
---
# <a name="how-to-make-a-phone-call-using-twilio-in-a-php-application-on-azure"></a>Azure 'da PHP uygulamasında Twilio kullanarak telefon araması yapma
Aşağıdaki örnekte, Azure 'da barındırılan bir PHP Web sayfasından çağrı yapmak için Twilio nasıl kullanabileceğiniz gösterilmektedir. Elde edilen uygulama, aşağıdaki ekran görüntüsünde gösterildiği gibi kullanıcıdan telefon araması değerlerini ister.

![Twilio ve PHP kullanarak Azure çağrı formu][twilio_php]

Bu konudaki kodu kullanmak için aşağıdakileri yapmanız gerekir:

1. [Twilio konsolınızdan][twilio_console]bir Twilio hesabı ve kimlik doğrulama belirteci alın. Twilio kullanmaya başlamak için fiyatlandırmayı [https://www.twilio.com/pricing][twilio_pricing]değerlendirin. Bir deneme hesabı [https://www.twilio.com/try-twilio][try_twilio]için ' de kaydolabilirsiniz.
2. [Php Için Twilio kitaplığını](https://github.com/twilio/twilio-php) edinin veya BIR PEAR paketi olarak yükler. Daha fazla bilgi için bkz. [Readme dosyası](https://github.com/twilio/twilio-php/blob/master/README.md).
3. PHP için Azure SDK 'sını yükler. 
<!-- For an overview of the SDK and instructions on installing it, see [Set up the Azure SDK for PHP](app-service-web/web-sites-php-mysql-deploy-use-git.md) -->

## <a name="create-a-web-form-for-making-a-call"></a>Çağrı yapmak için bir Web formu oluşturma
Aşağıdaki HTML kodu, bir çağrı yapmak için Kullanıcı verilerini alan bir Web sayfasının (**callform. html**) nasıl oluşturulacağını gösterir:

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
Aşağıdaki kod, Kullanıcı **callform. html**tarafından gösterilen formu gönderdiğinde çağrılan **MakeCall. php**oluşturmayı gösterir. Aşağıda gösterilen kod çağrı iletisini oluşturur ve çağrıyı oluşturur. Ayrıca, Twilio hesabınızı ve kimlik doğrulama belirtecinizi, **$Sid** atanan yer tutucu değerler yerine [Twilio konsolundan][twilio_console] kullandığınızdan emin olun ve aşağıdaki kodda **$token** .

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

Bu çağrıyı yapmanın yanı sıra, aşağıdaki görüntüde gösterildiği gibi, **MakeCall. php** bazı çağrı meta verilerini görüntüler. Çağrı meta verileri hakkında daha fazla bilgi için [https://www.twilio.com/docs/api/rest/call#instance-properties][twilio_call_properties]bkz.

![Twilio ve PHP kullanarak Azure çağrı yanıtı][twilio_php_response]

## <a name="run-the-application"></a>Uygulamayı çalıştırma
Bir sonraki adım, [uygulamanızı git Ile Azure Web Apps dağıtmaktır](app-service/app-service-web-get-started-php.md) (Bununla birlikte, ilişkili tüm bilgiler olmasa da). 

## <a name="next-steps"></a>Sonraki adımlar
Bu kod, Azure 'da PHP 'de Twilio kullanarak temel işlevselliği göstermek için verilmiştir. Üretim sırasında Azure 'a dağıtım yapmadan önce, daha fazla hata işleme veya diğer özellik eklemek isteyebilirsiniz. Örneğin:

* Bir Web formu kullanmak yerine, telefon numaralarını depolamak ve metin çağırmak için Azure Storage bloblarını veya SQL veritabanını kullanabilirsiniz. PHP 'de Azure Storage bloblarını kullanma hakkında daha fazla bilgi için bkz. [php uygulamalarıyla Azure Storage kullanma][howto_blob_storage_php]. PHP 'de SQL veritabanı kullanma hakkında daha fazla bilgi için bkz. [php UYGULAMALARıYLA SQL Database kullanma][howto_sql_azure_php].
* **MakeCall. php** kodu, çağrıya nasıl devam edeceğine Twilio[https://twimlets.com/message][twimlet_message_url]bildiren bir Twilio işaretleme dili (twiml) yanıtı sağlamak için Twilio tarafından sağlanmış URL () kullanır. Örneğin, döndürülen twiml, metnin çağrı alıcısına konuşmasına neden `<Say>` olan bir fiil içerebilir. Twilio tarafından sağlanmış URL 'yi kullanmak yerine, Twilio 'ın isteğine yanıt vermek için kendi hizmetinizi oluşturabilirsiniz; daha fazla bilgi için bkz. [php 'de ses ve SMS özellikleri Için Twilio kullanma][howto_twilio_voice_sms_php]. Twiml hakkında daha fazla bilgi adresinde [https://www.twilio.com/docs/api/twiml][twiml]bulunabilir ve hakkında daha fazla bilgi ve diğer Twilio [https://www.twilio.com/docs/api/twiml/say][twilio_say]fiilleri hakkında `<Say>` daha fazla bilgi bulabilirsiniz.
* Adresindeki [https://www.twilio.com/docs/security][twilio_docs_security]Twilio güvenlik kılavuzlarını okuyun.

Twilio hakkında daha fazla bilgi için bkz [https://www.twilio.com/docs][twilio_docs].

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
[howto_blob_storage_php]: https://azure.microsoft.com/documentation/articles/storage-php-how-to-use-blobs/
[howto_sql_azure_php]: https://azure.microsoft.com/documentation/articles/sql-database-php-how-to-use/
[twilio_call_properties]: https://www.twilio.com/docs/api/rest/call#instance-properties
[twilio_docs_security]: https://www.twilio.com/docs/security
[twilio_docs]: https://www.twilio.com/docs
[twilio_say]: https://www.twilio.com/docs/api/twiml/say
[ssl_validation]: http://readthedocs.org/docs/twilio-php/en/latest/usage/rest.html
[twilio_php]: ./media/partner-twilio-php-make-phone-call/WA_TwilioPHPCallForm.jpg
[twilio_php_response]: ./media/partner-twilio-php-make-phone-call/WA_TwilioPHPMakeCall.jpg
[twilio_php_github]: https://github.com/twilio/twilio-php
