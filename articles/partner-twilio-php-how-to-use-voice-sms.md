---
title: Ses ve SMS için Twilio kullanma (PHP) | Microsoft Docs
description: Azure 'da bir telefon araması yapmayı ve Twilio API hizmetiyle SMS iletisi göndermenizi öğrenin. PHP 'de yazılan kod örnekleri.
documentationcenter: php
services: ''
author: georgewallace
ms.assetid: 007f22e3-ac75-4868-8315-da000c2e0dd0
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 11/25/2014
ms.author: gwallace
ms.openlocfilehash: bf1ab01b39d594002bc5e677ffe6c3049fbb91ce
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95521028"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-php"></a>PHP 'de ses ve SMS özellikleri için Twilio kullanma
Bu kılavuzda, Azure 'da Twilio API hizmetiyle ortak programlama görevlerinin nasıl gerçekleştirileceği gösterilmektedir. Kapsanan senaryolar, telefon araması yapmayı ve kısa mesaj hizmeti (SMS) iletisi göndermeyi içerir. Twilio hakkında daha fazla bilgi edinmek ve uygulamalarınızda sesli ve SMS kullanma hakkında daha fazla bilgi için [sonraki adımlar](#NextSteps) bölümüne bakın.

## <a name="what-is-twilio"></a><a id="WhatIs"></a>Twilio nedir?
Twilio, iş iletişiminin geleceği güçleştir, geliştiricilerin uygulamalara ses, VoIP ve mesajlaşma ekleme imkanı sağlar. Bulut tabanlı, genel bir ortamda gereken tüm altyapıyı sanallaştırırlar ve bu, Twilio Communications API platformu aracılığıyla kullanıma sunmaktadır. Uygulamalar derleme ve ölçeklenebilir basittir. Kullandıkça Öde fiyatlandırmasıyla ilgili esneklik ve bulut güvenilirliği avantajlarından yararlanın.

**Twilio Voice** , uygulamalarınızın telefon araması yapmasına ve almasına izin verir. **TWILIO SMS** , uygulamanızın metin iletileri göndermesini ve almasını sağlar. **Twilio istemcisi** , herhangi bir telefonda, tabletten veya tarayıcıdan VoIP çağrısı yapmanıza ve WebRTC 'yi desteketmenize olanak tanır.

## <a name="twilio-pricing-and-special-offers"></a><a id="Pricing"></a>Twilio fiyatlandırması ve özel teklifler
Azure müşterileri özel bir [teklif](https://www.twilio.com/azure)alır: Twilio hesabınızı yükselttiğinizde Twilio kredisi $10. Bu Twilio kredisi herhangi bir Twilio kullanımına uygulanabilir (telefon numaranız ve iletinizin ya da çağrı hedefinin konumuna bağlı olarak, 1.000 SMS ileti veya en fazla 1000 gelen ses dakikası alma için $10 kredi eşdeğeri). Bu Twilio kredilerini kullanın ve kullanmaya başlayın: [https://ahoy.twilio.com/azure](https://ahoy.twilio.com/azure) .

Twilio, Kullandıkça Öde hizmetidir. Bir kurulum ücreti yoktur ve hesabınızı dilediğiniz zaman kapatabilirsiniz. Daha fazla ayrıntı için [Twilio fiyatlandırmayı][twilio_pricing]bulabilirsiniz.

## <a name="concepts"></a><a id="Concepts"></a>Kavramlar
Twilio API 'si, uygulamalar için ses ve SMS işlevselliği sağlayan bir Reststeme API 'sidir. İstemci kitaplıkları birden çok dilde kullanılabilir; bir liste için bkz. [TWILIO API Libraries][twilio_libraries].

Twilio API 'sinin önemli yönleri, Twilio Verbs ve Twilio Markup Language (TwiML).

### <a name="twilio-verbs"></a><a id="Verbs"></a>Twilio fiilleri
API, Twilio fiillerini kullanır; Örneğin, Twilio, bir çağrıda bir iletiyi sessiz olarak göndermek için **&lt; söyleyin &gt;** .

Aşağıda, Twilio fiillerinin bir listesi verilmiştir. [Twilio biçimlendirme dili belgeleri](https://www.twilio.com/docs/api/twiml)aracılığıyla diğer fiiller ve yetenekler hakkında bilgi edinin.

* **&lt; Çevir &gt;**: çağrıyı başka bir telefona bağlar.
* **&lt; Topla &gt;**: telefon tuş takımında girilen sayısal rakamları toplar.
* **&lt; Kapat &gt;**: bir çağrıyı sonlandırır.
* **&lt; Oynat &gt;**: bir ses dosyası çalar.
* **&lt; Duraklat &gt;**: belirtilen saniye sayısı için sessizce bekler.
* **&lt; Kayıt &gt;**: arayanın sesini kaydeder ve kaydı içeren bir dosyanın URL 'sini döndürür.
* **&lt; Yeniden &gt; yönlendir**: bir çağrının veya SMS denetiminin DENETIMINI, farklı bir URL 'de twiml 'ye aktarır.
* **&lt; Reddet &gt;**: Twilio numaranızı Faturalandırmadan gelen çağrıyı reddeder
* **&lt; Deyin &gt;**: bir çağrıda yapılan metni konuşmaya dönüştürür.
* **&lt; SMS &gt;**: SMS iletisi gönderir.

### <a name="twiml"></a><a id="TwiML"></a>TwiML
TwiML, bir çağrıyı veya SMS 'yi nasıl işleyebileceğini bilgilendirmek için Twilio fiillerini temel alan XML tabanlı yönergelerin bir kümesidir.

Örnek olarak, aşağıdaki TwiML metin **Merhaba Dünya** konuşmaya dönüştürür.

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<Response>
    <Say>Hello World</Say>
</Response>
```

Uygulamanız Twilio API 'sini çağırdığında, API parametrelerinden biri TwiML yanıtını döndüren URL 'dir. Geliştirme amacıyla, uygulamalarınız tarafından kullanılan TwiML yanıtlarını sağlamak için Twilio tarafından sağlanmış URL 'Leri kullanabilirsiniz. Ayrıca, TwiML yanıtlarını oluşturmak için kendi URL 'nizi barındırabilir ve **Twimlresponse** nesnesini kullanmak diğer bir seçenektir.

Twilio fiilleri, öznitelikleri ve TwiML hakkında daha fazla bilgi için bkz. [twiml][twiml]. Twilio API 'SI hakkında daha fazla bilgi için bkz. [TWILIO API][twilio_api].

## <a name="create-a-twilio-account"></a><a id="CreateAccount"></a>Twilio hesabı oluşturma
Bir Twilio hesabı almaya hazırsanız, [TRY Twilio][try_twilio]' de kaydolun. Ücretsiz bir hesapla başlayabilir ve hesabınızı daha sonra yükseltebilirsiniz.

Bir Twilio hesabı için kaydolduğunuzda, bir hesap KIMLIĞI ve bir kimlik doğrulama belirteci alırsınız. Twilio API çağrıları yapmak için her ikisi de gerekecektir. Hesabınıza yetkisiz erişimi engellemek için kimlik doğrulama belirtecinizi güvende tutun. Hesap KIMLIĞINIZ ve kimlik doğrulama belirteciniz, sırasıyla **Hesap SID 'si** ve **kimlik doğrulama belirteci** etiketli alanlarda [Twilio hesabı sayfasında][twilio_account]görüntülenebilir.

## <a name="create-a-php-application"></a><a id="create_app"></a>PHP uygulaması oluşturma
Twilio hizmetini kullanan ve Azure 'da çalışan bir PHP uygulaması, Twilio hizmetini kullanan diğer PHP uygulamasından farklı değildir. Twilio Hizmetleri REST tabanlı olmakla birlikte PHP 'den birkaç şekilde çağrılabilir. Bu makale, Twilio hizmetlerinin [GitHub 'DAN php Için Twilio kitaplığı][twilio_php]ile nasıl kullanılacağına odaklanacaktır. PHP için Twilio kitaplığını kullanma hakkında daha fazla bilgi için bkz [https://www.twilio.com/docs/libraries/php][twilio_lib_docs] ..

Azure 'da bir Twilio/PHP uygulaması oluşturma ve dağıtmaya yönelik ayrıntılı yönergeler, [Azure 'da BIR php uygulamasında Twilio kullanarak nasıl telefon araması][howto_phonecall_php]yapılacağını bulabilirsiniz.

## <a name="configure-your-application-to-use-twilio-libraries"></a><a id="configure_app"></a>Uygulamanızı Twilio kitaplıklarını kullanacak şekilde yapılandırma
Uygulamanızı PHP için Twilio kitaplığını iki şekilde kullanacak şekilde yapılandırabilirsiniz:

1. GitHub () ' dan PHP için Twilio kitaplığını indirin [https://github.com/twilio/twilio-php][twilio_php] ve **hizmet** dizinini uygulamanıza ekleyin.
   
    -VEYA-
2. PHP için Twilio kitaplığını bir PEAR paketi olarak yükler. Aşağıdaki komutlarla yüklenebilir:

    ```bash
    $ pear channel-discover twilio.github.com/pear
    $ pear install twilio/Services_Twilio
    ```

PHP için Twilio kitaplığını yükledikten sonra, kitaplığa başvurmak için PHP dosyalarınızın en üstüne bir **require_once** açıklaması ekleyebilirsiniz:

```php
require_once 'Services/Twilio.php';
```

Daha fazla bilgi için bkz. [https://github.com/twilio/twilio-php/blob/master/README.md][twilio_github_readme].

## <a name="how-to-make-an-outgoing-call"></a><a id="howto_make_call"></a>Nasıl yapılır: giden çağrı yapma
Aşağıda, **Services_Twilio** sınıfını kullanarak nasıl giden bir çağrının yapılacağı gösterilmektedir. Bu kod ayrıca Twilio biçimlendirme dili (TwiML) yanıtını döndürmek için Twilio tarafından sağlanmış bir site kullanır. **Kimden** **ve telefon numaraları için** değerlerinizi değiştirin ve kodu çalıştırmadan önce Twilio **hesabınızın telefon numarasını** doğrulayın.

```php
// Include the Twilio PHP library.
require_once 'Services/Twilio.php';

// Library version.
$version = "2010-04-01";

// Set your account ID and authentication token.
$sid = "your_twilio_account_sid";
$token = "your_twilio_authentication_token";

// The number of the phone initiating the call.
$from_number = "NNNNNNNNNNN";

// The number of the phone receiving call.
$to_number = "NNNNNNNNNNN";

// Use the Twilio-provided site for the TwiML response.
$url = "https://twimlets.com/message";

// The phone message text.
$message = "Hello world.";

// Create the call client.
$client = new Services_Twilio($sid, $token, $version);

//Make the call.
try
{
    $call = $client->account->calls->create(
        $from_number, 
        $to_number,
        $url.'?Message='.urlencode($message)
    );
}
catch (Exception $e) 
{
    echo 'Error: ' . $e->getMessage();
}
```

Belirtildiği gibi, bu kod TwiML yanıtını döndürmek için Twilio tarafından sağlanmış bir site kullanır. Bunun yerine, TwiML yanıtı sağlamak için kendi sitenizi kullanabilirsiniz; daha fazla bilgi için, [kendi web sitenizde TwiML yanıtları sağlama](#howto_provide_twiml_responses)konusuna bakın.

* **Note**: TLS/SSL sertifika doğrulama hatalarını gidermek için bkz. [https://www.twilio.com/docs/api/errors][ssl_validation] 

## <a name="how-to-send-an-sms-message"></a><a id="howto_send_sms"></a>Nasıl yapılır: SMS iletisi gönderme
Aşağıda, **Services_Twilio** SıNıFıNı kullanarak SMS iletisinin nasıl gönderileceği gösterilmektedir. **Kimden** numarası, TWILIO tarafından SMS iletileri göndermek için deneme hesapları için sağlanır. Kodu çalıştırmadan önce, Twilio hesabınız için **-** Number için doğrulama yapılmalıdır.

```php
// Include the Twilio PHP library.
require_once 'Services/Twilio.php';

// Library version.
$version = "2010-04-01";

// Set your account ID and authentication token.
$sid = "your_twilio_account_sid";
$token = "your_twilio_authentication_token";


$from_number = "NNNNNNNNNNN"; // With trial account, texts can only be sent from your Twilio number.
$to_number = "NNNNNNNNNNN";
$message = "Hello world.";

// Create the call client.
$client = new Services_Twilio($sid, $token, $version);

// Send the SMS message.
try
{
    $client->$client->account->messages->sendMessage($from_number, $to_number, $message);
}
catch (Exception $e) 
{
    echo 'Error: ' . $e->getMessage();
}
```

## <a name="how-to-provide-twiml-responses-from-your-own-website"></a><a id="howto_provide_twiml_responses"></a>Nasıl yapılır: kendi web sitenizde TwiML yanıtları sağlama
Uygulamanız Twilio API 'sine bir çağrı başlattığında, Twilio isteği TwiML yanıtı döndürmesi beklenen bir URL 'ye gönderir. Yukarıdaki örnek Twilio tarafından sağlanmış URL 'YI kullanır [https://twimlets.com/message][twimlet_message_url] . (TwiML, Twilio tarafından kullanılmak üzere tasarlanırken, bunu tarayıcınızda görüntüleyebilirsiniz. Örneğin, boş bir [https://twimlets.com/message][twimlet_message_url] öğeyi görmek için öğesine tıklayın `<Response>` ; başka bir örnek olarak, [https://twimlets.com/message?Message%5B0%5D=Hello%20World][twimlet_message_url_hello_world] bir öğesi içeren bir öğeyi görmek için tıklayın `<Response>` `<Say>` .)

Twilio tarafından sağlanmış URL 'ye güvenmek yerine, HTTP yanıtlarını döndüren kendi sitenizi oluşturabilirsiniz. Siteyi, XML yanıtları döndüren herhangi bir dilde oluşturabilirsiniz; Bu konu, TwiML 'yi oluşturmak için PHP 'yi kullandığınız varsayılmaktadır.

Aşağıdaki PHP sayfası, çağrının **Merhaba Dünya** belirten TwiML yanıtı ile sonuçlanır.

```xml
<?php    
    header("content-type: text/xml");    
    echo "<?xml version=\"1.0\" encoding=\"UTF-8\"?>\n";
?>
<Response>    
    <Say>Hello world.</Say>
</Response>
```

Yukarıdaki örnekte görebileceğiniz gibi, TwiML yanıtı yalnızca bir XML belgesidir. PHP için Twilio kitaplığı sizin için TwiML 'yi oluşturacak sınıfları içerir. Aşağıdaki örnek yukarıda gösterildiği gibi eşdeğer yanıtı üretir ancak PHP için Twilio kitaplığındaki **Services \_ Twilio \_ twiml** sınıfını kullanır:

```php
require_once('Services/Twilio.php');

$response = new Services_Twilio_Twiml();
$response->say("Hello world.");
print $response;
```

TwiML hakkında daha fazla bilgi için bkz [https://www.twilio.com/docs/api/twiml][twiml_reference] .. 

PHP sayfanızı TwiML yanıtları sağlamak üzere ayarladıktan sonra, metoda geçirilen URL olarak PHP sayfasının URL 'sini kullanın  `Services_Twilio->account->calls->create`  . Örneğin, Azure 'da barındırılan bir hizmete dağıtılan **mytwiml** adlı bir Web uygulamanız varsa ve php sayfasının adı **mytwiml. php** ise, URL aşağıdaki örnekte gösterildiği gibi  **Services_Twilio >hesap->çağrılar->oluştur**  ' a geçirilebilir:

```php
require_once 'Services/Twilio.php';

$sid = "your_twilio_account_sid";
$token = "your_twilio_authentication_token";
$from_number = "NNNNNNNNNNN";
$to_number = "NNNNNNNNNNN";
$url = "http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.php";

// The phone message text.
$message = "Hello world.";

$client = new Services_Twilio($sid, $token, "2010-04-01");

try
{
    $call = $client->account->calls->create(
        $from_number, 
        $to_number,
        $url.'?Message='.urlencode($message)
    );
}
catch (Exception $e) 
{
    echo 'Error: ' . $e->getMessage();
}
```

PHP ile Azure 'da Twilio kullanma hakkında daha fazla bilgi için bkz. [Azure 'DA php uygulamasında Twilio kullanarak telefon araması yapma][howto_phonecall_php].

## <a name="how-to-use-additional-twilio-services"></a><a id="AdditionalServices"></a>Nasıl yapılır: ek Twilio hizmetlerini kullanma
Burada gösterilen örneklere ek olarak Twilio, Azure uygulamanızdan ek Twilio işlevsellikten yararlanmak için kullanabileceğiniz web tabanlı API 'Ler sunar. Tüm ayrıntılar için [TWILIO API belgelerine][twilio_api_documentation]bakın.

## <a name="next-steps"></a><a id="NextSteps"></a>Sonraki adımlar
Twilio hizmetinin temellerini öğrendiğinize göre artık daha fazla bilgi edinmek için bu bağlantıları izleyin:

* [Twilio güvenlik yönergeleri][twilio_security_guidelines]
* [Twilio nasıl ve örnek kodu][twilio_howtos]
* [Twilio hızlı başlangıç öğreticileri][twilio_quickstarts] 
* [GitHub üzerinde Twilio][twilio_on_github]
* [Twilio desteğiyle konuşun][twilio_support]

[twilio_php]: https://github.com/twilio/twilio-php
[twilio_lib_docs]: https://www.twilio.com/docs/libraries/php
[twilio_github_readme]: https://github.com/twilio/twilio-php/blob/master/README.md
[ssl_validation]: https://www.twilio.com/docs/api/errors
[twilio_api_service]: https://api.twilio.com
[howto_phonecall_php]: partner-twilio-php-make-phone-call.md
[twilio_voice_request]: https://www.twilio.com/docs/api/twiml/twilio_request
[twilio_sms_request]: https://www.twilio.com/docs/api/twiml/sms/twilio_request
[misc_role_config_settings]: /previous-versions/azure/hh690945(v=azure.100)
[twimlet_message_url]: https://twimlets.com/message
[twimlet_message_url_hello_world]: https://twimlets.com/message?Message%5B0%5D=Hello%20World
[twiml_reference]: https://www.twilio.com/docs/api/twiml
[twilio_pricing]: https://www.twilio.com/pricing
[special_offer]: https://ahoy.twilio.com/azure
[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: https://www.twilio.com/docs/api/twiml
[twilio_api]: https://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_account]:  https://www.twilio.com/user/account
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#
[twilio_api_documentation]: https://www.twilio.com/api
[twilio_security_guidelines]: https://www.twilio.com/docs/security
[twilio_howtos]: https://www.twilio.com/docs/howto
[twilio_on_github]: https://github.com/twilio
[twilio_support]: https://www.twilio.com/help/contact
[twilio_quickstarts]: https://www.twilio.com/docs/quickstart