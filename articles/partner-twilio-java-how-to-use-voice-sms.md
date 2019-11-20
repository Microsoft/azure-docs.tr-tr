---
title: Ses ve SMS için Twilio kullanma (Java) | Microsoft Docs
description: Azure 'da bir telefon araması yapmayı ve Twilio API hizmetiyle SMS iletisi göndermenizi öğrenin. Java 'da yazılan kod örnekleri.
services: ''
documentationcenter: java
author: georgewallace
ms.assetid: f3508965-5527-4255-9d51-5d5f926f4d43
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 11/25/2014
ms.author: gwallace
ms.openlocfilehash: 18e93ce18ed746612996399dc1aeb258abd26165
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/20/2019
ms.locfileid: "69637218"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-java"></a>Java 'daki Voice ve SMS özellikleri için Twilio kullanma
Bu kılavuzda, Azure 'da Twilio API hizmetiyle ortak programlama görevlerinin nasıl gerçekleştirileceği gösterilmektedir. Kapsanan senaryolar, telefon araması yapmayı ve kısa mesaj hizmeti (SMS) iletisi göndermeyi içerir. Twilio hakkında daha fazla bilgi edinmek ve uygulamalarınızda sesli ve SMS kullanma hakkında daha fazla bilgi için [sonraki adımlar](#NextSteps) bölümüne bakın.

## <a id="WhatIs"></a>Twilio nedir?
Twilio, ses ve SMS uygulamaları oluşturmak için mevcut Web dillerinizi ve becerilerinizi kullanmanıza imkan tanıyan bir telefon Web hizmeti API 'sidir. Twilio bir üçüncü taraf hizmetidir (bir Microsoft ürünü değil, Azure özelliği değil).

**Twilio Voice** , uygulamalarınızın telefon araması yapmasına ve almasına izin verir. **TWILIO SMS** , uygulamalarınızın SMS iletileri oluşturup almasına izin verir. **Twilio istemcisi** , uygulamalarınızın Mobil bağlantılar da dahil olmak üzere var olan Internet bağlantılarını kullanarak sesli iletişim kurmasını sağlar.

## <a id="Pricing"></a>Twilio fiyatlandırması ve özel teklifler
Twilio fiyatlandırması hakkında bilgi [Twilio fiyatlandırması][twilio_pricing]adresinde bulunabilir. Azure müşterileri özel bir [teklif][special_offer]alır: ücretsiz kredi olarak 1000 metin veya 1000 gelen dakika. Bu teklif için kaydolmak veya daha fazla bilgi edinmek için lütfen adresini ziyaret [https://ahoy.twilio.com/azure][special_offer]edin.

## <a id="Concepts"></a>Tiren
Twilio API 'si, uygulamalar için ses ve SMS işlevselliği sağlayan bir Reststeme API 'sidir. İstemci kitaplıkları birden çok dilde kullanılabilir; bir liste için bkz. [TWILIO API Libraries][twilio_libraries].

Twilio API 'sinin önemli yönleri, Twilio Verbs ve Twilio Markup Language (TwiML).

### <a id="Verbs"></a>Twilio fiilleri
API, Twilio fiillerini kullanır; Örneğin, Twilio, **&lt;bir&gt;** çağrıda bir iletiyi sessiz olarak göndermek için söyleyin.

Aşağıda, Twilio fiillerinin bir listesi verilmiştir.

* Çevir:  **&lt;&gt;** Çağrıyı başka bir telefona bağlar.
* Topla:  **&lt;&gt;** Telefon tuş takımında girilen sayısal rakamları toplar.
* Kapat:  **&lt;&gt;** Bir çağrıyı sonlandırır.
* Oynat:  **&lt;&gt;** Bir ses dosyası çalar.
* Kuyruk:  **&lt;&gt;** ' İ çağıranlar kuyruğuna ekleyin.
* Duraklat:  **&lt;&gt;** Belirtilen saniye sayısı için sessizce bekler.
* Kayıt:  **&lt;&gt;** Arayanın sesini kaydeder ve kaydı içeren bir dosyanın URL 'sini döndürür.
* **Yenidenyönlendir&gt;: &lt;** Farklı bir URL 'de TwiML 'ye bir çağrının veya SMS 'nin denetimini aktarır.
* Reddet:  **&lt;&gt;** Size faturalandırma olmadan Twilio numaranız için gelen çağrıyı reddeder.
* Şunu söyleyin:  **&lt;&gt;** Bir çağrıda yapılan metni konuşmaya dönüştürür.
* SMS:  **&lt;&gt;** SMS iletisi gönderir.

### <a id="TwiML"></a>TwiML
TwiML, bir çağrıyı veya SMS 'yi nasıl işleyebileceğini bilgilendirmek için Twilio fiillerini temel alan XML tabanlı yönergelerin bir kümesidir.

Örnek olarak, aşağıdaki TwiML Merhaba Dünya metni dönüştürür **!** konuşma.

```xml
    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World!</Say>
    </Response>
```

Uygulamanız Twilio API 'sini çağırdığında, API parametrelerinden biri TwiML yanıtını döndüren URL 'dir. Geliştirme amacıyla, uygulamalarınız tarafından kullanılan TwiML yanıtlarını sağlamak için Twilio tarafından sağlanmış URL 'Leri kullanabilirsiniz. Ayrıca, TwiML yanıtlarını oluşturmak için kendi URL 'nizi barındırabilir ve **Twimlresponse** nesnesini kullanmak diğer bir seçenektir.

Twilio fiilleri, öznitelikleri ve TwiML hakkında daha fazla bilgi için bkz. [twiml][twiml]. Twilio API 'SI hakkında daha fazla bilgi için bkz. [TWILIO API][twilio_api].

## <a id="CreateAccount"></a>Twilio hesabı oluşturma
Bir Twilio hesabı almaya hazırsanız, [TRY Twilio][try_twilio]' de kaydolun. Ücretsiz bir hesapla başlayabilir ve hesabınızı daha sonra yükseltebilirsiniz.

Bir Twilio hesabı için kaydolduğunuzda, bir hesap KIMLIĞI ve bir kimlik doğrulama belirteci alırsınız. Twilio API çağrıları yapmak için her ikisi de gerekecektir. Hesabınıza yetkisiz erişimi engellemek için kimlik doğrulama belirtecinizi güvende tutun. Hesap KIMLIĞINIZ ve kimlik doğrulama belirteciniz, sırasıyla **Hesap SID 'si** ve **kimlik doğrulama belirteci**etiketli alanlarda [Twilio konsolunda][twilio_console]görüntülenebilir.

## <a id="create_app"></a>Java uygulaması oluşturma
1. Twilio JAR 'yi edinin ve Java derleme yolunuza ve WAR dağıtım derlemenize ekleyin. [https://github.com/twilio/twilio-java][twilio_java]' De, GitHub kaynaklarını indirebilir ve kendi jar 'nizi oluşturabilir veya önceden oluşturulmuş bir jar indirebilirsiniz (bağımlılıkları olan veya olmayan).
2. JDK 'nin **CAcert** anahtar deposu 'un MD5 parmak izi 67 olan Equifax güvenli sertifika yetkilisi sertifikasını içerdiğinden emin olun: CB: 9D: C0:13:24:8A: 82:9B: B2:17:1e: D1:1B: EC: D4 (seri numarası 35: de: F4: CF ve SHA1 parmak izi D2:32:09: ad: 23 :D 3:14:23:21:: 74: E4:0D: 7F: 9D: 62:13:97:86:63:3A). Bu, Twilio API 'lerini kullandığınızda çağrılan, [https://api.twilio.com][twilio_api_service] hizmet için sertifika yetkilisi (CA) sertifikasıdır. JDK 'nin **CAcert** anahtar deposu 'un doğru CA sertifikasını içerdiğini sağlama hakkında bilgi için bkz. [Java CA sertifika deposuna sertifika ekleme][add_ca_cert].

Java için Twilio istemci kitaplığı 'nı kullanmaya yönelik ayrıntılı yönergeler, [Azure 'Da Java uygulamasında Twilio kullanarak nasıl telefon araması][howto_phonecall_java]yapılacağını bulabilirsiniz.

## <a id="configure_app"></a>Uygulamanızı Twilio kitaplıklarını kullanacak şekilde yapılandırma
Kodunuzun içinde, uygulamanızda kullanmak istediğiniz Twilio paketleri veya sınıfları için kaynak dosyalarınızın en üstüne **içeri aktarma** deyimleri ekleyebilirsiniz.

Java kaynak dosyaları için:

```java
    import com.twilio.*;
    import com.twilio.rest.api.*;
    import com.twilio.type.*;
    import com.twilio.twiml.*;
```

Java sunucu sayfası (JSP) kaynak dosyaları için:

```java
    import="com.twilio.*"
    import="com.twilio.rest.api.*"
    import="com.twilio.type.*"
    import="com.twilio.twiml.*"
 ```
 
Kullanmak istediğiniz Twilio paketlerine veya sınıfa bağlı olarak, **içeri aktarma** deyimleriniz farklı olabilir.

## <a id="howto_make_call"></a>Nasıl Yapılır: Giden bir çağrı yapın
Aşağıda, **çağrı** sınıfını kullanarak nasıl giden bir çağrının yapılacağı gösterilmektedir. Bu kod ayrıca Twilio biçimlendirme dili (TwiML) yanıtını döndürmek için Twilio tarafından sağlanmış bir site kullanır. **Kimden** ve telefon numaraları için değerlerinizi değiştirin ve kodu çalıştırmadan önce Twilio hesabınızın telefon numarasını doğrulayın .

```java
    // Use your account SID and authentication token instead
    // of the placeholders shown here.
    String accountSID = "your_twilio_account_SID";
    String authToken = "your_twilio_authentication_token";

    // Initialize the Twilio client.
    Twilio.init(accountSID, authToken);

    // Use the Twilio-provided site for the TwiML response.
    URI uri = new URI("https://twimlets.com/message" +
            "?Message%5B0%5D=Hello%20World%21");

    // Declare To and From numbers
    PhoneNumber to = new PhoneNumber("NNNNNNNNNN");
    PhoneNumber from = new PhoneNumber("NNNNNNNNNN");

    // Create a Call creator passing From, To and URL values
    // then make the call by executing the create() method
    Call.creator(to, from, uri).create();
```

**Call. Creator** metoduna geçirilen parametreler hakkında daha fazla bilgi için bkz [https://www.twilio.com/docs/api/rest/making-calls][twilio_rest_making_calls].

Belirtildiği gibi, bu kod TwiML yanıtını döndürmek için Twilio tarafından sağlanmış bir site kullanır. Bunun yerine, TwiML yanıtı sağlamak için kendi sitenizi kullanabilirsiniz; daha fazla bilgi için bkz. [Azure 'Da Java uygulamasında TwiML yanıtları sağlama](#howto_provide_twiml_responses).

## <a id="howto_send_sms"></a>Nasıl Yapılır: SMS iletisi gönder
Aşağıda **ileti** sınıfı kullanılarak SMS iletisi gönderme gösterilmektedir. Bu **4155992671**, Twilio tarafından SMS iletileri göndermek için deneme hesapları için sağlanmaktadır. Kodu çalıştırmadan önce, Twilio hesabınız için **-** Number için doğrulama yapılmalıdır.

```java
    // Use your account SID and authentication token instead
    // of the placeholders shown here.
    String accountSID = "your_twilio_account_SID";
    String authToken = "your_twilio_authentication_token";

    // Initialize the Twilio client.
    Twilio.init(accountSID, authToken);

    // Declare To and From numbers and the Body of the SMS message
    PhoneNumber to = new PhoneNumber("+14159352345"); // Replace with a valid phone number for your account.
    PhoneNumber from = new PhoneNumber("+14158141829"); // Replace with a valid phone number for your account.
    String body = "Where's Wallace?";

    // Create a Message creator passing From, To and Body values
    // then send the SMS message by calling the create() method
    Message sms = Message.creator(to, from, body).create();
```

**İleti. Creator** yöntemine geçirilen parametreler hakkında daha fazla bilgi için bkz [https://www.twilio.com/docs/api/rest/sending-sms][twilio_rest_sending_sms].

## <a id="howto_provide_twiml_responses"></a>Nasıl Yapılır: Kendi web sitenizde TwiML yanıtları sağlayın
Uygulamanız Twilio API 'sine bir çağrı başlattığında (örneğin, **Callcreator. Create** yöntemi aracılığıyla), Isteğiniz TwiML yanıtı döndürmesi beklenen bir URL 'ye gönderilir. Yukarıdaki örnek Twilio tarafından sağlanmış URL 'YI [https://twimlets.com/message][twimlet_message_url]kullanır. (TwiML, Web Hizmetleri tarafından kullanılmak üzere tasarlanırken, tarayıcınızda TwiML 'yi görüntüleyebilirsiniz. Örneğin, boş [https://twimlets.com/message][twimlet_message_url] [https://twimlets.com/message?Message%5B0%5D=Hello%20World%21][twimlet_message_url_hello_world] **&lt; bir yanıt&gt;** **öğesinigörmekiçinöğesinetıklayın;başkabirörnekolarak,şunuiçerenbiryanıtöğesinigörmekiçin&lt;&gt;** **&lt; Deyin&gt;** öğesi.)

Twilio tarafından sağlanmış URL 'ye güvenmek yerine, HTTP yanıtlarını döndüren kendi URL sitenizi oluşturabilirsiniz. Siteyi, HTTP yanıtlarını döndüren herhangi bir dilde oluşturabilirsiniz; Bu konuda, URL 'YI bir JSP sayfasında barındırabileceksiniz varsayılmaktadır.

Aşağıdaki JSP sayfası Merhaba Dünya belirten bir TwiML yanıtı ile sonuçlanır **!** çağırın.

```xml
    <%@ page contentType="text/xml" %>
    <Response>
        <Say>Hello World!</Say>
    </Response>
```

Aşağıdaki JSP sayfası, bazı metinleri belirten, birkaç duraklatan oluşan ve Twilio API sürümü ve Azure rolü adı hakkında bilgi bildiren bir TwiML yanıtı ile sonuçlanır.

```xml
    <%@ page contentType="text/xml" %>
    <Response>
        <Say>Hello from Azure!</Say>
        <Pause></Pause>
        <Say>The Twilio API version is <%= request.getParameter("ApiVersion") %>.</Say>
        <Say>The Azure role name is <%= System.getenv("RoleName") %>.</Say>
        <Pause></Pause>
        <Say>Good bye.</Say>
    </Response>
```

**Apiversion** parametresi, Twilio ses ISTEKLERINDE (SMS istekleri değil) kullanılabilir. Twilio ses ve SMS istekleri için kullanılabilir istek parametrelerini görmek için sırasıyla bkz <https://www.twilio.com/docs/api/twiml/twilio_request> . ve. <https://www.twilio.com/docs/api/twiml/sms/twilio_request> **RoleName** ortam değişkeni, Azure dağıtımının bir parçası olarak kullanılabilir. ( **System. getenv**'den alınabilmeleri için özel ortam değişkenleri eklemek Istiyorsanız, [çeşitli rol yapılandırma ayarlarındaki][misc_role_config_settings]ortam değişkenleri bölümüne bakın.)

JSP sayfanız TwiML yanıtları sağlamak üzere ayarlandıktan sonra, **Call. Creator** metoduna geçirilen URL olarak JSP sayfasının URL 'sini kullanın. Örneğin, Azure 'da barındırılan bir hizmete dağıtılan MyTwiML adlı bir Web uygulamanız varsa ve JSP sayfasının adı mytwiml. jsp ise, URL, aşağıda gösterildiği gibi **Call. Creator** öğesine geçirilebilir:

```java
    // Declare To and From numbers and the URL of your JSP page
    PhoneNumber to = new PhoneNumber("NNNNNNNNNN");
    PhoneNumber from = new PhoneNumber("NNNNNNNNNN");
    URI uri = new URI("http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.jsp");

    // Create a Call creator passing From, To and URL values
    // then make the call by executing the create() method
    Call.creator(to, from, uri).create();
```

TwiML ile yanıt vermeye yönelik başka bir seçenek de **com. Twilio. twiml** paketinde bulunan **VoiceResponse** sınıfı aracılığıyla yapılır.

Java ile Azure 'da Twilio kullanma hakkında daha fazla bilgi için bkz. [Azure 'Da Java uygulamasında Twilio kullanarak telefon araması yapma][howto_phonecall_java].

## <a id="AdditionalServices"></a>Nasıl Yapılır: Ek Twilio hizmetlerini kullanma
Burada gösterilen örneklere ek olarak Twilio, Azure uygulamanızdan ek Twilio işlevsellikten yararlanmak için kullanabileceğiniz web tabanlı API 'Ler sunar. Tüm ayrıntılar için [TWILIO API belgelerine][twilio_api_documentation]bakın.

## <a id="NextSteps"></a>Sonraki Adımlar
Twilio hizmetinin temellerini öğrendiğinize göre artık daha fazla bilgi edinmek için bu bağlantıları izleyin:

* [Twilio güvenlik yönergeleri][twilio_security_guidelines]
* [Twilio nasıl ve örnek kodu][twilio_howtos]
* [Twilio hızlı başlangıç öğreticileri][twilio_quickstarts]
* [GitHub üzerinde Twilio][twilio_on_github]
* [Twilio desteğiyle konuşun][twilio_support]

[twilio_java]: https://github.com/twilio/twilio-java
[twilio_api_service]: https://api.twilio.com
[add_ca_cert]: java-add-certificate-ca-store.md
[howto_phonecall_java]: partner-twilio-java-phone-call-example.md
[misc_role_config_settings]: https://msdn.microsoft.com/library/windowsazure/hh690945.aspx
[twimlet_message_url]: https://twimlets.com/message
[twimlet_message_url_hello_world]: https://twimlets.com/message?Message%5B0%5D=Hello%20World%21
[twilio_rest_making_calls]: https://www.twilio.com/docs/api/rest/making-calls
[twilio_rest_sending_sms]: https://www.twilio.com/docs/api/rest/sending-sms
[twilio_pricing]: https://www.twilio.com/pricing
[special_offer]: https://ahoy.twilio.com/azure
[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: https://www.twilio.com/docs/api/twiml
[twilio_api]: https://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_console]:  https://www.twilio.com/console
[verify_phone]: https://www.twilio.com/console/phone-numbers/verified#
[twilio_api_documentation]: https://www.twilio.com/docs
[twilio_security_guidelines]: https://www.twilio.com/docs/security
[twilio_howtos]: https://www.twilio.com/docs/howto
[twilio_on_github]: https://github.com/twilio
[twilio_support]: https://www.twilio.com/help/contact
[twilio_quickstarts]: https://www.twilio.com/docs/quickstart
