---
title: Ses ve SMS için Twilio kullanma (Ruby) | Microsoft Docs
description: Azure 'da bir telefon araması yapmayı ve Twilio API hizmetiyle SMS iletisi göndermenizi öğrenin. Ruby ile yazılan kod örnekleri.
services: ''
documentationcenter: ruby
author: georgewallace
ms.assetid: 60e512f6-fa47-47c0-aedc-f19bb72a1158
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: article
ms.date: 11/25/2014
ms.author: gwallace
ms.openlocfilehash: 49203195bf7746d0bff1b9543d1641f69ab23359
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95542686"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-ruby"></a>Ruby 'de ses ve SMS özellikleri için Twilio kullanma
Bu kılavuzda, Azure 'da Twilio API hizmetiyle ortak programlama görevlerinin nasıl gerçekleştirileceği gösterilmektedir. Kapsanan senaryolar, telefon araması yapmayı ve kısa mesaj hizmeti (SMS) iletisi göndermeyi içerir. Twilio hakkında daha fazla bilgi edinmek ve uygulamalarınızda sesli ve SMS kullanma hakkında daha fazla bilgi için [sonraki adımlar](#NextSteps) bölümüne bakın.

## <a name="what-is-twilio"></a><a id="WhatIs"></a>Twilio nedir?
Twilio, ses ve SMS uygulamaları oluşturmak için mevcut Web dillerinizi ve becerilerinizi kullanmanıza imkan tanıyan bir telefon Web hizmeti API 'sidir. Twilio bir üçüncü taraf hizmetidir (bir Microsoft ürünü değil, Azure özelliği değil).

**Twilio Voice** , uygulamalarınızın telefon araması yapmasına ve almasına izin verir. **TWILIO SMS** , uygulamalarınızın SMS iletileri oluşturup almasına izin verir. **Twilio istemcisi** , uygulamalarınızın Mobil bağlantılar da dahil olmak üzere var olan Internet bağlantılarını kullanarak sesli iletişim kurmasını sağlar.

## <a name="twilio-pricing-and-special-offers"></a><a id="Pricing"></a>Twilio fiyatlandırması ve özel teklifler
Twilio fiyatlandırması hakkında bilgi [Twilio fiyatlandırması][twilio_pricing]adresinde bulunabilir. Azure müşterileri özel bir [teklif][special_offer]alır: ücretsiz kredi olarak 1000 metin veya 1000 gelen dakika. Bu teklif için kaydolmak veya daha fazla bilgi edinmek için lütfen adresini ziyaret edin [https://ahoy.twilio.com/azure][special_offer] .  

## <a name="concepts"></a><a id="Concepts"></a>Kavramlar
Twilio API 'si, uygulamalar için ses ve SMS işlevselliği sağlayan bir Reststeme API 'sidir. İstemci kitaplıkları birden çok dilde kullanılabilir; bir liste için bkz. [TWILIO API Libraries][twilio_libraries].

### <a name="twiml"></a><a id="TwiML"></a>TwiML
TwiML, bir çağrıyı veya SMS 'nin nasıl işleyeceğini bildiren bir Twilio XML tabanlı yönergeler kümesidir.

Örnek olarak, aşağıdaki TwiML metin **Merhaba Dünya** konuşmaya dönüştürür.

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<Response>
    <Say>Hello World</Say>
</Response>
```

Tüm TwiML belgelerinin `<Response>` kök öğesi vardır. Buradan, Twilio fiillerini kullanarak uygulamanızın davranışını tanımlayabilirsiniz.

### <a name="twiml-verbs"></a><a id="Verbs"></a>TwiML fiilleri
Twilio fiilleri, Twilio **ne yapılacağını söyleyen** XML etiketlerdir. Örneğin, Twilio, bir çağrıda bir iletiyi sessiz olarak göndermek için **&lt; söyleyin &gt;** . 

Aşağıda, Twilio fiillerinin bir listesi verilmiştir.

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

Twilio fiilleri, öznitelikleri ve TwiML hakkında daha fazla bilgi için bkz. [twiml][twiml]. Twilio API 'SI hakkında daha fazla bilgi için bkz. [TWILIO API][twilio_api].

## <a name="create-a-twilio-account"></a><a id="CreateAccount"></a>Twilio hesabı oluşturma
Bir Twilio hesabı almaya hazırsanız, [TRY Twilio][try_twilio]' de kaydolun. Ücretsiz bir hesapla başlayabilir ve hesabınızı daha sonra yükseltebilirsiniz.

Bir Twilio hesabı için kaydolduğunuzda, uygulamanız için ücretsiz telefon numarası alırsınız. Ayrıca hesap SID 'SI ve kimlik doğrulama belirteci de alacaksınız. Twilio API çağrıları yapmak için her ikisi de gerekecektir. Hesabınıza yetkisiz erişimi engellemek için kimlik doğrulama belirtecinizi güvende tutun. Hesap SID 'SI ve kimlik doğrulama belirteciniz, sırasıyla **Hesap SID 'si** ve **kimlik doğrulama belirteci** etiketli alanlarda [Twilio hesabı sayfasında][twilio_account]görüntülenebilir.

### <a name="verify-phone-numbers"></a><a id="VerifyPhoneNumbers"></a>Telefon numaralarını doğrulama
Twilio tarafından verilen sayının yanı sıra, sizin oluşturduğunuz (örneğin, cep telefonunuz veya ev telefonu numaranız) sayıları uygulamalarınızda kullanmak üzere da doğrulayabilirsiniz. 

Telefon numarasını doğrulama hakkında daha fazla bilgi için bkz. [sayıları yönetme][verify_phone].

## <a name="create-a-ruby-application"></a><a id="create_app"></a>Ruby uygulaması oluşturma
Twilio hizmetini kullanan ve Azure 'da çalışan bir Ruby uygulaması, Twilio hizmetini kullanan başka bir Ruby uygulamasından farklı değildir. Twilio hizmetleri yeniden hazırlanmışken ve Ruby 'den birkaç şekilde çağrılabilecek olsa da, bu makale [Ruby Için Twilio yardımcı kitaplığı][twilio_ruby]ile Twilio hizmetlerinin nasıl kullanılacağına odaklanacaktır.

İlk olarak, yeni Ruby Web uygulamanız için bir konak görevi görecek [Yeni bir Azure Linux sanal makinesi ayarlayın][azure_vm_setup] . Bir raya uygulamasının oluşturulmasıyla ilgili adımları yoksayın, VM 'yi ayarlamanız yeterlidir. 80 dış bağlantı noktası ve bir iç bağlantı 5000 noktası olan bir uç nokta oluşturmanız emin olun.

Aşağıdaki örneklerde, Ruby için çok basit bir Web çerçevesi olan [Sinatra][sinatra]' ı kullanacağız. Ancak Ruby on rayda dahil olmak üzere herhangi bir diğer Web çerçevesi ile Ruby için Twilio yardımcı kitaplığını kullanabilirsiniz.

Yeni sanal makinenize SSH ekleyin ve yeni uygulamanız için bir dizin oluşturun. Bu dizinin içinde, Gemfile adlı bir dosya oluşturun ve aşağıdaki kodu buna kopyalayın:

```bash
source 'https://rubygems.org'
gem 'sinatra'
gem 'thin'
```

Komut satırı çalıştırın `bundle install` . Bu işlem yukarıdaki bağımlılıkları yükler. Ardından adlı bir dosya oluşturun `web.rb` . Bu, Web uygulamanız için kodun bulunacağı yerdir. Aşağıdaki kodu içine yapıştırın:

```ruby
require 'sinatra'

get '/' do
    "Hello Monkey!"
end
```

Bu noktada komutunu çalıştırabilirsiniz `ruby web.rb -p 5000` . Bu, 5000 numaralı bağlantı noktasında küçük bir Web sunucusu kullanacaktır. Azure VM 'niz için ayarladığınız URL 'YI ziyaret ederek tarayıcınızda bu uygulamaya gözatabilmelisiniz. Web uygulamanıza tarayıcıda ulabilmeniz için, bir Twilio uygulaması oluşturmaya başlamaya hazırsınız demektir.

## <a name="configure-your-application-to-use-twilio"></a><a id="configure_app"></a>Uygulamanızı Twilio kullanacak şekilde yapılandırma
Web uygulamanızı `Gemfile` Şu satırı içerecek şekilde güncelleştirerek Twilio kitaplığını kullanacak şekilde yapılandırabilirsiniz:

```bash
gem 'twilio-ruby'
```

Komut satırında komutunu çalıştırın `bundle install` . Şimdi açın `web.rb` ve bu satırı en üstte dahil edin:

```ruby
require 'twilio-ruby'
```

Artık, Web uygulamanızda Ruby için Twilio yardımcı kitaplığını kullanmak üzere hazırsınız.

## <a name="how-to-make-an-outgoing-call"></a><a id="howto_make_call"></a>Nasıl yapılır: giden çağrı yapma
Aşağıda, giden bir çağrının nasıl yapılacağı gösterilmektedir. Temel kavramlar, REST API çağrısı yapmak ve TwiML oluşturmak için Ruby için Twilio yardımcı kitaplığı kullanmayı içerir. **Kimden** **ve telefon numaraları için** değerlerinizi değiştirin ve kodu çalıştırmadan önce Twilio **hesabınızın telefon numarasını** doğrulayın.

Bu işlevi şu şekilde ekleyin `web.md` :

```ruby
# Set your account ID and authentication token.
sid = "your_twilio_account_sid";
token = "your_twilio_authentication_token";

# The number of the phone initiating the call.
# This should either be a Twilio number or a number that you've verified
from = "NNNNNNNNNNN";

# The number of the phone receiving call.
to = "NNNNNNNNNNN";

# Use the Twilio-provided site for the TwiML response.
url = "http://yourdomain.cloudapp.net/voice_url";

get '/make_call' do
    # Create the call client.
    client = Twilio::REST::Client.new(sid, token);

    # Make the call
    client.account.calls.create(to: to, from: from, url: url)
end

post '/voice_url' do
    "<Response>
        <Say>Hello Monkey!</Say>
    </Response>"
end
```

`http://yourdomain.cloudapp.net/make_call`Bir tarayıcıda açarsanız, telefon çağrısını yapmak Için TWILIO API 'sine yapılan çağrıyı tetikler. İçindeki ilk iki parametre `client.account.calls.create` oldukça kendi kendine açıklayıcıdır: çağrının sayısı `from` ve çağrının numarası `to` . 

Üçüncü parametresi ( `url` ), Twilio, çağrı bağlandığında ne yapacaklarına ilişkin yönergeler almak için gereken URL 'dir. Bu durumda, `http://yourdomain.cloudapp.net` basit bir TwiML belgesi döndüren BIR URL () ayarladık ve bir `<Say>` metin okuma yapmak için fiili kullanır ve çağrıyı alan kişiye "Merhaba maymun" söyleyin.

## <a name="how-to-receive-an-sms-message"></a><a id="howto_receive_sms"></a>Nasıl yapılır: SMS iletisi alma
Önceki örnekte, bir **giden** telefon araması başlattık. Bu kez, **gelen** SMS iletisini işlemek için kayıt sırasında Twilio verdiği telefon numarasını kullanalım.

İlk olarak, [Twilio panonuzda][twilio_account]oturum açın. Üst gezinti çubuğunda "sayılar" a tıklayın ve sonra sağladınız Twilio numarası ' na tıklayın. Yapılandırabilmeniz için iki URL görürsünüz. Bir ses Isteği URL 'SI ve SMS Istek URL 'SI. Bunlar, bir telefon araması yapıldığında veya numaraya SMS gönderildiğinde Twilio tarafından çağrı yapan URL 'lardır. URL 'Ler, "Web kancaları" olarak da bilinir.

Gelen SMS iletilerini işlemek istiyoruz, bu nedenle URL 'yi şu şekilde güncelleştirelim `http://yourdomain.cloudapp.net/sms_url` . Devam edin ve sayfanın altındaki Değişiklikleri Kaydet ' e tıklayın. Şimdi, artık `web.rb` uygulamamız bu uygulamayı işleyecek şekilde programlayalım:

```ruby
post '/sms_url' do
    "<Response>
        <Message>Hey, thanks for the ping! Twilio and Azure rock!</Message>
    </Response>"
end
```

Değişikliği yaptıktan sonra, Web uygulamanızı yeniden başlattığınızdan emin olun. Şimdi telefonunuzu yanınıza alın ve Twilio numaranız için SMS gönderin. "Selam, ping için teşekkürler!" yazan bir SMS yanıtını hemen almalısınız. Twilio ve Azure rock! ".

## <a name="how-to-use-additional-twilio-services"></a><a id="additional_services"></a>Nasıl yapılır: ek Twilio hizmetlerini kullanma
Burada gösterilen örneklere ek olarak Twilio, Azure uygulamanızdan ek Twilio işlevsellikten yararlanmak için kullanabileceğiniz web tabanlı API 'Ler sunar. Tüm ayrıntılar için [TWILIO API belgelerine][twilio_api_documentation]bakın.

### <a name="next-steps"></a><a id="NextSteps"></a>Sonraki adımlar
Twilio hizmetinin temellerini öğrendiğinize göre artık daha fazla bilgi edinmek için bu bağlantıları izleyin:

* [Twilio güvenlik yönergeleri][twilio_security_guidelines]
* [Twilio HowTos ve örnek kod][twilio_howtos]
* [Twilio hızlı başlangıç öğreticileri][twilio_quickstarts] 
* [GitHub üzerinde Twilio][twilio_on_github]
* [Twilio desteğiyle konuşun][twilio_support]

[twilio_ruby]: https://www.twilio.com/docs/ruby/install





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
[sinatra]: http://www.sinatrarb.com/
[azure_vm_setup]: /previous-versions/azure/virtual-machines/linux/classic/ruby-rails-web-app