---
title: Ses ve SMS için Twilio kullanma (.NET) | Microsoft Docs
description: Azure 'da bir telefon araması yapmayı ve Twilio API hizmetiyle SMS iletisi göndermenizi öğrenin. .NET dilinde yazılan kod örnekleri.
services: ''
documentationcenter: .net
author: georgewallace
ms.assetid: 74d4f3c9-f1cb-4968-b744-36b32cd0e834
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 04/24/2015
ms.author: gwallace
ms.openlocfilehash: 22b33d7b4b0ff69a2e751cadff70453f73ed4f8e
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/21/2019
ms.locfileid: "69876813"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-from-azure"></a>Azure 'dan ses ve SMS özellikleri için Twilio kullanma
Bu kılavuzda, Azure 'da Twilio API hizmetiyle ortak programlama görevlerinin nasıl gerçekleştirileceği gösterilmektedir. Kapsanan senaryolar, telefon araması yapmayı ve kısa mesaj hizmeti (SMS) iletisi göndermeyi içerir. Twilio hakkında daha fazla bilgi edinmek ve uygulamalarınızda sesli ve SMS kullanma hakkında daha fazla bilgi için [sonraki adımlar](#NextSteps) bölümüne bakın.

## <a id="WhatIs"></a>Twilio nedir?
Twilio, iş iletişiminin geleceği güçleştir, geliştiricilerin uygulamalara ses, VoIP ve mesajlaşma ekleme imkanı sağlar. Bulut tabanlı, genel bir ortamda gereken tüm altyapıyı sanallaştırırlar ve bu, Twilio Communications API platformu aracılığıyla kullanıma sunmaktadır. Uygulamalar derleme ve ölçeklenebilir basittir. Kullandıkça Öde fiyatlandırmasıyla esneklikten yararlanın ve bulut güveninden yararlanın.

**Twilio Voice** , uygulamalarınızın telefon araması yapmasına ve almasına izin verir. **TWILIO SMS** , uygulamalarınızın SMS iletileri göndermesini ve almasını sağlar. **Twilio istemcisi** , herhangi bir telefonda, tabletten veya tarayıcıdan VoIP çağrısı yapmanıza ve WebRTC 'yi desteketmenize olanak tanır.

## <a id="Pricing"></a>Twilio fiyatlandırması ve özel teklifler
Azure müşterileri özel bir [teklif](https://www.twilio.com/azure)alır: Twilio hesabınızı yükselttiğinizde Twilio kredisi $10. Bu Twilio kredisi herhangi bir Twilio kullanımına uygulanabilir (telefon numaranız ve iletinizin ya da çağrı hedefinin konumuna bağlı olarak, 1.000 SMS ileti veya en fazla 1000 gelen ses dakikası alma için $10 kredi eşdeğeri). Bu Twilio kredilerini kullanın ve [Twilio.com/Azure](https://twilio.com/azure)adresinden başlayın.

Twilio, Kullandıkça Öde hizmetidir. Bir kurulum ücreti yoktur ve hesabınızı dilediğiniz zaman kapatabilirsiniz. Daha fazla ayrıntı için [Twilio fiyatlandırmayı](https://www.twilio.com/voice/pricing)bulabilirsiniz.

## <a id="Concepts"></a>Tiren
Twilio API 'si, uygulamalar için ses ve SMS işlevselliği sağlayan bir Reststeme API 'sidir. İstemci kitaplıkları birden çok dilde kullanılabilir; bir liste için bkz. [TWILIO API Libraries][twilio_libraries].

Twilio API 'sinin önemli yönleri, Twilio Verbs ve Twilio Markup Language (TwiML).

### <a id="Verbs"></a>Twilio fiilleri
API, Twilio fiillerini kullanır; Örneğin, Twilio, **&lt;bir&gt;** çağrıda bir iletiyi sessiz olarak göndermek için söyleyin.

Aşağıda, Twilio fiillerinin bir listesi verilmiştir.  [Twilio biçimlendirme dili belgeleri](https://www.twilio.com/docs/api/twiml)aracılığıyla diğer fiiller ve yetenekler hakkında bilgi edinin.

* `<Dial>`: Çağrıyı başka bir telefona bağlar.
* `<Gather>`: Telefon tuş takımında girilen sayısal rakamları toplar.
* `<Hangup>`: Bir çağrıyı sonlandırır.
* `<Play>`: Bir ses dosyası çalar.
* `<Pause>`: Belirtilen saniye sayısı için sessizce bekler.
* `<Record>`: Arayanın sesini kaydeder ve kaydı içeren bir dosyanın URL 'sini döndürür.
* `<Redirect>`: Farklı bir URL 'de TwiML 'ye bir çağrının veya SMS 'nin denetimini aktarır.
* `<Reject>`: Faturalandırma olmadan Twilio numaranız için gelen çağrıyı reddeder
* `<Say>`: Bir çağrıda yapılan metni konuşmaya dönüştürür.
* `<Sms>`: SMS iletisi gönderir.

### <a name="twiml"></a>TwiML
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

## <a id="CreateAccount"></a>Twilio hesabı oluşturma
Bir Twilio hesabı almaya hazırsanız, [TRY Twilio][try_twilio]' de kaydolun. Ücretsiz bir hesapla başlayabilir ve hesabınızı daha sonra yükseltebilirsiniz.

Bir Twilio hesabı için kaydolduğunuzda, bir hesap KIMLIĞI ve bir kimlik doğrulama belirteci alırsınız. Twilio API çağrıları yapmak için her ikisi de gerekecektir. Hesabınıza yetkisiz erişimi engellemek için kimlik doğrulama belirtecinizi güvende tutun. Hesap KIMLIĞINIZ ve kimlik doğrulama belirteciniz, sırasıyla **Hesap SID 'si** ve **kimlik doğrulama belirteci**etiketli alanlarda [Twilio hesabı sayfasında][twilio_account]görüntülenebilir.

## <a id="create_app"></a>Azure uygulaması oluşturma
Twilio özellikli bir uygulamayı barındıran bir Azure uygulaması, diğer hiçbir Azure uygulamasından farklı değildir. Twilio .NET kitaplığını ekler ve rolü Twilio .NET kitaplıklarını kullanacak şekilde yapılandırırsınız.
İlk Azure projesi oluşturma hakkında daha fazla bilgi için bkz. [Visual Studio Ile Azure projesi oluşturma][vs_project].

## <a id="configure_app"></a>Uygulamanızı Twilio kitaplıklarını kullanacak şekilde yapılandırma
Twilio, bir dizi .NET yardımcı kitaplığı sağlar ve bu sayede Twilio REST API ve Twilio Istemcisiyle etkileşimde bulunmak için basit ve kolay yollar sunar.

Twilio .NET geliştiricileri için beş kitaplık sağlar:

| Kitaplık | Açıklama |
| --- | --- |
| Twilio. API | Kolay bir .NET kitaplığında Twilio REST API sarmalayan çekirdek Twilio kitaplığı. Bu kitaplık .NET, Silverlight ve Windows Phone 7 ' de kullanılabilir. |
| Twilio. TwiML | TwiML biçimlendirmesi oluşturmak için .NET kolay bir yol sağlar. |
| Twilio. MVC | ASP.NET MVC kullanan geliştiriciler için, bu kitaplık bir TwilioController, TwiML eylem sonucu ve istek doğrulama özniteliği içerir. |
| Twilio. WebMatrix | Microsoft 'un ücretsiz WebMatrix geliştirme aracı kullanan geliştiriciler için, bu kitaplık çeşitli Twilio eylemleri için Razor söz dizimi yardımcıları içerir. |
| Twilio. Client. Capability | Twilio Istemci JavaScript SDK 'Sı ile kullanmak için yetenek belirteci oluşturucuyu içerir. |

> [!Important]
> Tüm kitaplıklar .NET 3,5, Silverlight 4 veya Windows Phone 7 veya üstünü gerektirir.

Bu kılavuzda sağlanan örnekler Twilio. API kitaplığını kullanır.

Kitaplıklar, Visual Studio 2010 ' de 2015 ' ye kadar olan [NuGet Paket Yöneticisi uzantısı kullanılarak yüklenebilir](https://www.twilio.com/docs/csharp/install) .  Kaynak kodu, kitaplıkları kullanmaya yönelik tüm belgeleri içeren bir wiki içeren [GitHub][twilio_github_repo]üzerinde barındırılır.

Microsoft Visual Studio 2010, varsayılan olarak NuGet 'in 1,2 sürümünü yüklüyor. Twilio kitaplıklarını yükleme, NuGet veya üzeri sürüm 1,6 gerektirir. NuGet 'i yükleme veya güncelleştirme hakkında daha fazla bilgi [https://nuget.org/][nuget]için bkz.

> [!NOTE]
> NuGet 'in en son sürümünü yüklemek için, önce Visual Studio Uzantı Yöneticisi 'Ni kullanarak yüklü sürümü kaldırmanız gerekir. Bunu yapmak için, Visual Studio 'Yu yönetici olarak çalıştırmanız gerekir. Aksi takdirde, Kaldır düğmesi devre dışıdır.
>
>

### <a id="use_nuget"></a>Twilio kitaplıklarını Visual Studio projenize eklemek için:
1. Çözümünüzü Visual Studio'da açın.
2. **Başvurular**' a sağ tıklayın.
3. **NuGet Paketlerini Yönet...** öğesine tıklayın.
4. **Çevrimiçi**' e tıklayın.
5. Çevrimiçi ara kutusuna *Twilio*yazın.
6. Twilio paketindeki **Install** (Aç) düğmesine tıklayın.

## <a id="howto_make_call"></a>Nasıl Yapılır: Giden bir çağrı yapın
Aşağıda, **Callresource** sınıfını kullanarak nasıl giden bir çağrının yapılacağı gösterilmektedir. Bu kod ayrıca Twilio biçimlendirme dili (TwiML) yanıtını döndürmek için Twilio tarafından sağlanmış bir site kullanır. Değerlerini bir veya daha fazla telefon numarası ile değiştirin ve kodu çalıştırmadan önce Twilio hesabınızın telefon numarasını doğrulayın.

```csharp
// Use your account SID and authentication token instead
// of the placeholders shown here.
const string accountSID = "your_twilio_account";
const string authToken = "your_twilio_authentication_token";

// Initialize the TwilioClient.
TwilioClient.Init(accountSID, authToken);

// Use the Twilio-provided site for the TwiML response.
var url = "https://twimlets.com/message";
url = $"{url}?Message%5B0%5D=Hello%20World";

// Set the call From, To, and URL values to use for the call.
// This sample uses the sandbox number provided by
// Twilio to make the call.
var call = CallResource.Create(
    to: new PhoneNumber("+NNNNNNNNNN"),
    from: new PhoneNumber("NNNNNNNNNN"),
    url: new Uri(url));
    }
```

**Callresource. Create** yöntemine geçirilen parametreler hakkında daha fazla bilgi için bkz [https://www.twilio.com/docs/api/rest/making-calls][twilio_rest_making_calls].

Belirtildiği gibi, bu kod TwiML yanıtını döndürmek için Twilio tarafından sağlanmış bir site kullanır. Bunun yerine, TwiML yanıtını sağlamak için kendi sitenizi kullanabilirsiniz. Daha fazla bilgi için [nasıl yapılır: Kendi web sitenizde](#howto_provide_twiml_responses)twiml yanıtları sağlayın.

## <a id="howto_send_sms"></a>Nasıl Yapılır: SMS iletisi gönder
Aşağıdaki ekran görüntüsünde, **Messageresource** sınıfı kullanılarak SMS iletisinin nasıl gönderileceği gösterilmektedir. **Kimden** numarası, TWILIO tarafından SMS iletileri göndermek için deneme hesapları için sağlanır. Kodu çalıştırmadan önce, Twilio hesabınız için **-** Number için doğrulama yapılmalıdır.

```csharp
// Use your account SID and authentication token instead
// of the placeholders shown here.
const string accountSID = "your_twilio_account";
const string authToken = "your_twilio_authentication_token";

// Initialize the TwilioClient.
TwilioClient.Init(accountSID, authToken);

try
{
    // Send an SMS message.
    var message = MessageResource.Create(
        to: new PhoneNumber("+12069419717"),
        from: new PhoneNumber("+14155992671"),
        body: "This is my SMS message.");
}
catch (TwilioException ex)
{
    // An exception occurred making the REST call
    Console.WriteLine(ex.Message);
}
```

## <a id="howto_provide_twiml_responses"></a>Nasıl Yapılır: Kendi web sitenizde TwiML yanıtları sağlayın
Uygulamanız Twilio API 'sine bir çağrı başlattığında (örneğin, **Callresource. Create** yöntemi-Twilio) Isteğinizi TwiML yanıtı döndürmesi beklenen bir URL 'ye gönderir. Örnek [: Giden bir çağrının](#howto_make_call) yanıtı döndürmek için Twilio tarafından sağlanmış URL [https://twimlets.com/message][twimlet_message_url] 'yi kullanmasını sağlayın.

> [!NOTE]
> TwiML, Web Hizmetleri tarafından kullanılmak üzere tasarlanırken, tarayıcınızda TwiML 'yi görüntüleyebilirsiniz. Örneğin, boş `<Response>` bir [https://twimlets.com/message][twimlet_message_url] öğeyi görmek için öğesine tıklayın; başka bir örnek olarak, [https://twimlets.com/message?Message%5B0%5D=Hello%20World](https://twimlets.com/message?Message%5B0%5D=Hello%20World) bir &lt; deyin&gt; öğesi `<Response>` içeren bir öğeyi görmek için tıklayın.
>

Twilio tarafından sağlanmış URL 'ye güvenmek yerine, HTTP yanıtlarını döndüren kendi URL sitenizi oluşturabilirsiniz. Siteyi, HTTP yanıtlarını döndüren herhangi bir dilde oluşturabilirsiniz. Bu konu, URL 'YI bir ASP.NET genel işleyicisinden barındırdığınızı varsayar.

Aşağıdaki ASP.NET Işleyicisi, çağrıda **Merhaba Dünya** bildiren bir TwiML yanıtı öğelerini.

```csharp
using System.Text;
using System.Web;

namespace WebRole1
{
    /// <summary>
    /// Summary description for Handler1
    /// </summary>
    public class Handler1 : IHttpHandler
    {
        public void ProcessRequest(HttpContext context)
        {
            const string twiMLResponse =
                "<Response><Say>Hello World.</Say></Response>";

            context.Response.Clear();
            context.Response.ContentType = "text/xml";
            context.Response.ContentEncoding = Encoding.UTF8;
            context.Response.Write(twiMLResponse);
            context.Response.End();
        }

        public bool IsReusable
        {
            get
            {
                return false;
            }
        }
    }
}
```
    
Yukarıdaki örnekte görebileceğiniz gibi, TwiML yanıtı yalnızca bir XML belgesidir. Twilio. TwiML kitaplığı sizin için TwiML 'yi oluşturacak sınıfları içerir. Aşağıdaki örnek, yukarıda gösterildiği gibi eşdeğer yanıtı üretir, ancak **VoiceResponse** sınıfını kullanır.

```csharp
using System.Web;
using Twilio.TwiML;

namespace WebRole1
{
    /// <summary>
    /// Summary description for Handler1
    /// </summary>
    public class Handler1 : IHttpHandler
    {

        public void ProcessRequest(HttpContext context)
        {
            var twiml = new VoiceResponse();
            twiml.Say("Hello World.");

            context.Response.Clear();
            context.Response.ContentType = "text/xml";
            context.Response.Write(twiml.ToString());
            context.Response.End();
        }

        public bool IsReusable
        {
            get
            {
                return false;
            }
        }
    }
}
```

TwiML hakkında daha fazla bilgi için bkz [https://www.twilio.com/docs/api/twiml](https://www.twilio.com/docs/api/twiml).

TwiML yanıtları sağlamak için bir yol ayarladıktan sonra, bu URL 'YI **Callresource. Create** metoduna geçirebilirsiniz. Örneğin, bir Azure bulut hizmetine dağıtılan MyTwiML adlı bir Web uygulamanız varsa ve ASP.NET Işleyicinizin adı mytwiml. ashx ise, URL şu kod örneğinde gösterildiği gibi **Callresource. Create** öğesine geçirilebilir:

```csharp
// This sample uses the sandbox number provided by Twilio to make the call.
// Place the call.
var call = CallResource.Create(
    to: new PhoneNumber("+NNNNNNNNNN"),
    from: new PhoneNumber("NNNNNNNNNN"),
    url: new Uri("http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.ashx"));
    }
```

Azure 'da ASP.NET ile Twilio kullanma hakkında daha fazla bilgi için bkz. [Azure 'da bir Web rolünde nasıl bir telefon araması yapılır?][howto_phonecall_dotnet].

[!INCLUDE [twilio-additional-services-and-next-steps](../includes/twilio-additional-services-and-next-steps.md)]

[howto_phonecall_dotnet]: partner-twilio-cloud-services-dotnet-phone-call-web-role.md

[twimlet_message_url]: https://twimlets.com/message

[twilio_rest_making_calls]: https://www.twilio.com/docs/api/rest/making-calls

[vs_project]:https://docs.microsoft.com/visualstudio/azure/vs-azure-tools-azure-project-create
[nuget]:https://nuget.org/
[twilio_github_repo]:https://github.com/twilio/twilio-csharp

[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: https://www.twilio.com/docs/api/twiml
[twilio_api]: https://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_account]:  https://www.twilio.com/console
[verify_phone]: https://www.twilio.com/console/phone-numbers/verified
