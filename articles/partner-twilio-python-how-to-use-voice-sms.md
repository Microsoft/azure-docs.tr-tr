---
title: Ses ve SMS için Twilio kullanma (Python) | Microsoft Docs
description: Azure 'da bir telefon araması yapmayı ve Twilio API hizmetiyle SMS iletisi göndermenizi öğrenin. Python 'da yazılan kod örnekleri.
services: ''
documentationcenter: python
author: georgewallace
ms.assetid: 561bc75b-4ac4-40ba-bcba-48e901f27cc3
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 02/19/2015
ms.author: gwallace
ms.openlocfilehash: edbc9eef6b5f0af2e70152b66228cdf09ef31110
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72242195"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-python"></a>Python 'da ses ve SMS özellikleri için Twilio kullanma
Bu kılavuzda, Azure 'da Twilio API hizmetiyle ortak programlama görevlerinin nasıl gerçekleştirileceği gösterilmektedir. Kapsanan senaryolar, telefon araması yapmayı ve kısa mesaj hizmeti (SMS) iletisi göndermeyi içerir. Twilio hakkında daha fazla bilgi edinmek ve uygulamalarınızda sesli ve SMS kullanma hakkında daha fazla bilgi için [sonraki adımlar](#NextSteps) bölümüne bakın.

## <a id="WhatIs"></a>Twilio nedir?
Twilio, iş iletişiminin geleceği güçleştir, geliştiricilerin uygulamalara ses, VoIP ve mesajlaşma ekleme imkanı sağlar. Bulut tabanlı, genel bir ortamda gereken tüm altyapıyı sanallaştırırlar ve bu, Twilio Communications API platformu aracılığıyla kullanıma sunmaktadır. Uygulamalar derleme ve ölçeklenebilir basittir. Kullandıkça Öde fiyatlandırmasıyla ilgili esneklik ve bulut güvenilirliği avantajlarından yararlanın.

**Twilio Voice** , uygulamalarınızın telefon araması yapmasına ve almasına izin verir.
**TWILIO SMS** , uygulamanızın metin iletileri göndermesini ve almasını sağlar.
**Twilio istemcisi** , herhangi bir telefonda, tabletten veya tarayıcıdan VoIP çağrısı yapmanıza ve WebRTC 'yi desteketmenize olanak tanır.

## <a id="Pricing"></a>Twilio fiyatlandırması ve özel teklifler
Twilio hesabınızı yükselttiğinizde Azure müşterileri, Twilio kredisi için [özel bir teklif][special_offer] $10 alır. Bu Twilio kredisi herhangi bir Twilio kullanımına uygulanabilir (telefon numaranız ve iletinizin ya da çağrı hedefinin konumuna bağlı olarak, 1.000 SMS ileti veya en fazla 1000 gelen ses dakikası alma için $10 kredi eşdeğeri). Bu [Twilio][special_offer] kredilerini kullanın ve kullanmaya başlayın.

Twilio, Kullandıkça Öde hizmetidir. Bir kurulum ücreti yoktur ve hesabınızı dilediğiniz zaman kapatabilirsiniz. Daha fazla ayrıntı için [Twilio fiyatlandırmayı][twilio_pricing]bulabilirsiniz.

## <a id="Concepts"></a>Tiren
Twilio API 'si, uygulamalar için ses ve SMS işlevselliği sağlayan bir Reststeme API 'sidir. İstemci kitaplıkları birden çok dilde kullanılabilir; bir liste için bkz. [TWILIO API Libraries][twilio_libraries].

Twilio API 'sinin önemli yönleri, Twilio Verbs ve Twilio Markup Language (TwiML).

### <a id="Verbs"></a>Twilio fiilleri
API, Twilio fiillerini kullanır; Örneğin, **&lt;Deyin @ no__t-2** fiili, Twilio 'e bir çağrıda bir ileti teslim etmek için bildirir.

Aşağıda, Twilio fiillerinin bir listesi verilmiştir. [Twilio biçimlendirme dili belgeleri][twiml]aracılığıyla diğer fiiller ve yetenekler hakkında bilgi edinin.

* **&lt;Dial @ no__t-2**: çağrıyı başka bir telefona bağlar.
* **&lt;Topla @ no__t-2**: telefon tuş takımında girilen sayısal rakamları toplar.
* **&lt;Kapatması @ no__t-2**: bir çağrıyı sonlandırır.
* **&lt;Pause @ no__t-2**: belirtilen saniye sayısı için sessizce bekler.
* **&lt;Play @ no__t-2**: bir ses dosyası çalar.
* **&lt;Queue @ no__t-2**: ' i çağıranlar kuyruğuna ekleyin.
* **&lt;kayıt @ no__t-2**: arayanın sesini kaydeder ve kaydı içeren bir dosyanın URL 'sini döndürür.
* **&lt;Redirect @ no__t-2**: farklı bir URL 'de TwiML 'ye bir çağrının veya SMS 'nin denetimini aktarır.
* **&lt;Reddetme @ no__t-2**: size faturalandırma olmadan Twilio numaranızı gelen çağrıyı reddeder.
* **&lt;Deyin @ no__t-2**: bir çağrıda yapılan metni konuşmaya dönüştürür.
* **&lt;Sms @ no__t-2**: SMS iletisi gönderir.

### <a id="TwiML"></a>TwiML
TwiML, bir çağrıyı veya SMS 'yi nasıl işleyebileceğini bilgilendirmek için Twilio fiillerini temel alan XML tabanlı yönergelerin bir kümesidir.

Örnek olarak, aşağıdaki TwiML metin **Merhaba Dünya** konuşmaya dönüştürür.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
      <Say>Hello World</Say>
    </Response>

Uygulamanız Twilio API 'sini çağırdığında, API parametrelerinden biri TwiML yanıtını döndüren URL 'dir. Geliştirme amacıyla, uygulamalarınız tarafından kullanılan TwiML yanıtlarını sağlamak için Twilio tarafından sağlanmış URL 'Leri kullanabilirsiniz. Ayrıca, TwiML yanıtlarını oluşturmak için kendi URL 'nizi barındırabilir ve `TwiMLResponse` nesnesini kullanmak diğer bir seçenektir.

Twilio fiilleri, öznitelikleri ve TwiML hakkında daha fazla bilgi için bkz. [twiml][twiml]. Twilio API 'SI hakkında daha fazla bilgi için bkz. [TWILIO API][twilio_api].

## <a id="CreateAccount"></a>Twilio hesabı oluşturma
Bir Twilio hesabı almaya hazırsanız, [TRY Twilio][try_twilio]' de kaydolun. Ücretsiz bir hesapla başlayabilir ve hesabınızı daha sonra yükseltebilirsiniz.

Bir Twilio hesabı için kaydolduğunuzda, bir hesap SID 'SI ve bir kimlik doğrulama belirteci alırsınız. Twilio API çağrıları yapmak için her ikisi de gerekecektir. Hesabınıza yetkisiz erişimi engellemek için kimlik doğrulama belirtecinizi güvende tutun. Hesap SID 'SI ve kimlik doğrulama belirteciniz, sırasıyla **Hesap SID 'si** ve **kimlik doğrulama belirteci**etiketli alanlarda [Twilio konsolunda][twilio_console]görüntülenebilir.

## <a id="create_app"></a>Python uygulaması oluşturma
Twilio hizmetini kullanan ve Azure 'da çalışan bir Python uygulaması, Twilio hizmetini kullanan diğer hiçbir Python uygulamasından farklı değildir. Twilio Hizmetleri REST tabanlı olduğundan ve Python 'dan birkaç şekilde çağrılabilecek olsa da, bu makale [GitHub 'Dan Python Için Twilio kitaplığı][twilio_python]ile Twilio hizmetlerinin nasıl kullanılacağına odaklanacaktır. Python için Twilio kitaplığını kullanma hakkında daha fazla bilgi için bkz. [https://www.twilio.com/docs/libraries/python][twilio_lib_docs].

İlk olarak, yeni Python web uygulamanız için bir konak görevi gören [yeni bir Azure Linux VM 'yi ayarlama] [azure_vm_setup]. Sanal makine çalışmaya başladıktan sonra, uygulamanızı aşağıda açıklandığı gibi genel bir bağlantı noktasında kullanıma sunacaksınız.

### <a name="add-an-incoming-rule"></a>Gelen kuralı ekleme
  1. [Ağ güvenlik grubu] [azure_nsg] sayfasına gidin.
  2. Sanal makinenize karşılık gelen ağ güvenlik grubunu seçin.
  3. **80 numaralı bağlantı noktası**için ekleme ve **giden kuralı** . Herhangi bir adresten gelen bir adrese izin vermeyi unutmayın.

### <a name="set-the-dns-name-label"></a>DNS adı etiketini ayarla
  1. [Genel IP Adresleri] [azure_ips] sayfasına gidin.
  2. Sanal makinenize karşılık gelen genel IP 'yi seçin.
  3. **Yapılandırma** bölümünde **DNS adı etiketini** ayarlayın. Bu örnekte, *-Domain-Label*. centralus.cloudapp.Azure.com gibi bir şey görünür

Sanal makineye SSH üzerinden bağlantı kurabildikten sonra tercih ettiğiniz Web çerçevesini ( [Flask](http://flask.pocoo.org/) ve [Docgo](https://www.djangoproject.com)olan Python 'da en iyi şekilde bilinen ikisi) yükleyebilirsiniz. Bunlardan birini yalnızca `pip install` komutunu çalıştırarak yükleyebilirsiniz.

Sanal makineyi yalnızca bağlantı noktası 80 üzerinde trafiğe izin verecek şekilde yapılandırdığımızda aklınızda bulundurun. Bu nedenle, uygulamayı bu bağlantı noktasını kullanacak şekilde yapılandırdığınızdan emin olun.

## <a id="configure_app"></a>Uygulamanızı Twilio kitaplıklarını kullanacak şekilde yapılandırma
Uygulamanızı Python için Twilio kitaplığını iki şekilde kullanacak şekilde yapılandırabilirsiniz:

* Python için Twilio kitaplığını bir PIP paketi olarak yükler. Aşağıdaki komutlarla yüklenebilir:
   
        $ pip install twilio

    Veya

* GitHub ' dan ([https://github.com/twilio/twilio-python][twilio_python]) Python için Twilio kitaplığını indirin ve bunu şu şekilde yükleyin:

        $ python setup.py install

Python için Twilio kitaplığını yükledikten sonra, Python dosyalarınızda `import` ' ı bir kez daha kullanabilirsiniz:

        import twilio

Daha fazla bilgi için bkz. [twilio_github_readme](https://github.com/twilio/twilio-python/blob/master/README.md).

## <a id="howto_make_call"></a>Nasıl yapılır: giden çağrı yapma
Aşağıda, giden bir çağrının nasıl yapılacağı gösterilmektedir. Bu kod ayrıca Twilio biçimlendirme dili (TwiML) yanıtını döndürmek için Twilio tarafından sağlanmış bir site kullanır. **From_number** ve **to_number** telefon numaralarının değerlerini yerine koyun ve kodu çalıştırmadan önce Twilio hesabınız için **from_number** telefon numarasını doğruladığınıza emin olun.

    from urllib.parse import urlencode

    # Import the Twilio Python Client.
    from twilio.rest import TwilioRestClient

    # Set your account ID and authentication token.
    account_sid = "your_twilio_account_sid"
    auth_token = "your_twilio_authentication_token"

    # The number of the phone initiating the call.
    # This should either be a Twilio number or a number that you've verified
    from_number = "NNNNNNNNNNN"

    # The number of the phone receiving call.
    to_number = "NNNNNNNNNNN"

    # Use the Twilio-provided site for the TwiML response.
    url = "https://twimlets.com/message?"

    # The phone message text.
    message = "Hello world."

    # Initialize the Twilio client.
    client = TwilioRestClient(account_sid, auth_token)

    # Make the call.
    call = client.calls.create(to=to_number,
                               from_=from_number,
                               url=url + urlencode({'Message': message}))
    print(call.sid)

Belirtildiği gibi, bu kod TwiML yanıtını döndürmek için Twilio tarafından sağlanmış bir site kullanır. Bunun yerine, TwiML yanıtı sağlamak için kendi sitenizi kullanabilirsiniz; daha fazla bilgi için, [kendi web sitenizde TwiML yanıtları sağlama](#howto_provide_twiml_responses)konusuna bakın.

## <a id="howto_send_sms"></a>Nasıl yapılır: SMS iletisi gönderme
Aşağıda `TwilioRestClient` sınıfı kullanılarak SMS iletisinin nasıl gönderileceği gösterilmektedir. **From_number** numarası, TWILIO tarafından SMS iletilerini göndermek için deneme hesapları için sağlanır. Kodu çalıştırmadan önce **to_number** numarası Twilio hesabınız için doğrulanmalıdır.

    # Import the Twilio Python Client.
    from twilio.rest import TwilioRestClient

    # Set your account ID and authentication token.
    account_sid = "your_twilio_account_sid"
    auth_token = "your_twilio_authentication_token"

    from_number = "NNNNNNNNNNN"  # With trial account, texts can only be sent from your Twilio number.
    to_number = "NNNNNNNNNNN"
    message = "Hello world."

    # Initialize the Twilio client.
    client = TwilioRestClient(account_sid, auth_token)

    # Send the SMS message.
    message = client.messages.create(to=to_number,
                                     from_=from_number,
                                     body=message)

## <a id="howto_provide_twiml_responses"></a>Nasıl yapılır: kendi web sitenizde TwiML yanıtları sağlama
Uygulamanız Twilio API 'sine bir çağrı başlattığında, Twilio isteği TwiML yanıtı döndürmesi beklenen bir URL 'ye gönderir. Yukarıdaki örnekte, Twilio tarafından sağlanmış URL [https://twimlets.com/message][twimlet_message_url]kullanılır. (TwiML, Twilio tarafından kullanılmak üzere tasarlanırken tarayıcıda görüntüleyebilirsiniz. Örneğin, boş bir `<Response>` öğesini görmek için [https://twimlets.com/message][twimlet_message_url] ' e tıklayın; diğer bir örnek olarak, `<Say>` öğesi içeren bir `<Response>` öğesini görmek için [https://twimlets.com/message?Message%5B0%5D=Hello%20World][twimlet_message_url_hello_world] ' e tıklayın.)

Twilio tarafından sağlanmış URL 'ye güvenmek yerine, HTTP yanıtlarını döndüren kendi sitenizi oluşturabilirsiniz. Siteyi, XML yanıtları döndüren herhangi bir dilde oluşturabilirsiniz; Bu konu başlığı altında, TwiML 'yi oluşturmak için Python kullanacağınız varsayılır.

Aşağıdaki örneklerde, çağrısında **Merhaba Dünya** belirten bir TwiML yanıtı çıktı alınacaktır.

Flask ile:

    from flask import Response
    @app.route("/")
    def hello():
        xml = '<Response><Say>Hello world.</Say></Response>'
        return Response(xml, mimetype='text/xml')

Docgo ile:

    from django.http import HttpResponse
    def hello(request):
        xml = '<Response><Say>Hello world.</Say></Response>'
        return HttpResponse(xml, content_type='text/xml')

Yukarıdaki örnekte görebileceğiniz gibi, TwiML yanıtı yalnızca bir XML belgesidir. Python için Twilio kitaplığı, sizin için TwiML oluşturacak sınıfları içerir. Aşağıdaki örnek, yukarıda gösterildiği gibi eşdeğer yanıtı üretir, ancak Python için Twilio kitaplığındaki `twiml` modülünü kullanır:

    from twilio import twiml

    response = twiml.Response()
    response.say("Hello world.")
    print(str(response))

TwiML hakkında daha fazla bilgi için bkz. [https://www.twilio.com/docs/api/twiml][twiml_reference].

Python uygulamanızı TwiML yanıtları sağlamak üzere ayarladıktan sonra, `client.calls.create` metoduna geçirilen URL olarak uygulamanın URL 'sini kullanın. Örneğin, Azure 'da barındırılan bir hizmete dağıtılan **Mytwiml** adlı bir Web uygulamanız varsa, aşağıdaki örnekte gösterildiği gibi URL 'sini Web kancası olarak kullanabilirsiniz:

    from twilio.rest import TwilioRestClient

    account_sid = "your_twilio_account_sid"
    auth_token = "your_twilio_authentication_token"
    from_number = "NNNNNNNNNNN"
    to_number = "NNNNNNNNNNN"
    url = "http://your-domain-label.centralus.cloudapp.azure.com/MyTwiML/"

    # Initialize the Twilio client.
    client = TwilioRestClient(account_sid, auth_token)

    # Make the call.
    call = client.calls.create(to=to_number,
                               from_=from_number,
                               url=url)
    print(call.sid)

## <a id="AdditionalServices"></a>Nasıl yapılır: ek Twilio hizmetlerini kullanma
Burada gösterilen örneklere ek olarak Twilio, Azure uygulamanızdan ek Twilio işlevsellikten yararlanmak için kullanabileceğiniz web tabanlı API 'Ler sunar. Tüm ayrıntılar için [TWILIO API belgelerine][twilio_api]bakın.

## <a id="NextSteps"></a>Sonraki adımlar
Twilio hizmetinin temellerini öğrendiğinize göre artık daha fazla bilgi edinmek için bu bağlantıları izleyin:

* [Twilio güvenlik yönergeleri][twilio_security_guidelines]
* [Twilio kılavuzlar ve örnek kod][twilio_howtos]
* [Twilio hızlı başlangıç öğreticileri][twilio_quickstarts]
* [GitHub üzerinde Twilio][twilio_on_github]
* [Twilio desteğiyle konuşun][twilio_support]

[special_offer]: https://ahoy.twilio.com/azure
[twilio_python]: https://github.com/twilio/twilio-python
[twilio_lib_docs]: https://www.twilio.com/docs/libraries/python
[twilio_github_readme]: https://github.com/twilio/twilio-python/blob/master/README.md

[twimlet_message_url]: https://twimlets.com/message
[twimlet_message_url_hello_world]: https://twimlets.com/message?Message%5B0%5D=Hello%20World
[twiml_reference]: https://www.twilio.com/docs/api/twiml
[twilio_pricing]: https://www.twilio.com/pricing

[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: https://www.twilio.com/docs/api/twiml
[twilio_api]: https://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_console]:  https://www.twilio.com/console
[twilio_security_guidelines]: https://www.twilio.com/docs/security
[twilio_howtos]: https://www.twilio.com/docs/howto
[twilio_on_github]: https://github.com/twilio
[twilio_support]: https://www.twilio.com/help/contact
[twilio_quickstarts]: https://www.twilio.com/docs/quickstart
