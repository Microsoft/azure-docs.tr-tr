---
title: Twilio adresinden telefon araması yapma (.NET) | Microsoft Docs
description: Azure 'da bir telefon araması yapmayı ve Twilio API hizmetiyle SMS iletisi göndermenizi öğrenin. .NET dilinde yazılan kod örnekleri.
services: ''
documentationcenter: .net
author: georgewallace
editor: ''
ms.assetid: 789185ad-69dc-4e9e-a936-42e0a25315c8
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 05/04/2016
ms.author: gwallace
ms.openlocfilehash: 27b4f3cdd8f622a97cfc0853f79bb77d76673dcf
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/20/2019
ms.locfileid: "69636146"
---
# <a name="how-to-make-a-phone-call-using-twilio-in-a-web-role-on-azure"></a>Azure 'da bir Web rolünde Twilio kullanarak telefon araması yapma
Bu kılavuzda, Azure 'da barındırılan bir Web sayfasından çağrı yapmak için Twilio nasıl kullanılacağı gösterilmektedir. Elde edilen uygulama, aşağıdaki ekran görüntüsünde gösterildiği gibi kullanıcıdan verilen sayı ve iletiyle bir çağrı yapmasını ister.

![Twilio ve ASP.NET kullanarak Azure çağrı formu][twilio_dotnet_basic_form]

## <a name="twilio-prereqs"></a>Önkoşullar
Bu konudaki kodu kullanmak için aşağıdakileri yapmanız gerekir:

1. [Twilio konsolundan][twilio_console]bir Twilio hesabı ve kimlik doğrulama belirteci alın. Twilio kullanmaya başlamak için, adresinde [https://www.twilio.com/try-twilio][try_twilio]kaydolun. Fiyatlandırma 'yi ' de değerlendirebilirsiniz [https://www.twilio.com/pricing][twilio_pricing]. Twilio tarafından sunulan API hakkında daha fazla bilgi için bkz [https://www.twilio.com/voice/api][twilio_api].
2. *Twilio .NET kitaplığı* 'nı Web rolünüzün içine ekleyin. Bu konunun ilerleyen kısımlarında **Twilio kitaplıklarını web rolü projenize eklemek için**bkz.

[Azure 'da temel bir Web rolü][azure_webroles_get_started]oluşturma hakkında bilgi sahibi olmanız gerekir.

## <a name="howtocreateform"></a>Nasıl Yapılır: Çağrı yapmak için bir Web formu oluşturma
<a id="use_nuget"></a>Twilio kitaplıklarını web rolü projenize eklemek için:

1. Çözümünüzü Visual Studio'da açın.
2. **Başvurular**' a sağ tıklayın.
3. **NuGet Paketlerini Yönet**' e tıklayın.
4. **Çevrimiçi**' e tıklayın.
5. Çevrimiçi ara kutusuna *Twilio*yazın.
6. Twilio paketindeki **Install** (Aç) düğmesine tıklayın.

Aşağıdaki kod, bir çağrı yapmak için Kullanıcı verilerini almak üzere bir Web formu oluşturmayı gösterir. Bu örnekte, **twıocyüksek** adlı bir ASP.NET Web rolü oluşturulur.

```aspx
<%@ Page Title="Home Page" Language="C#" MasterPageFile="~/Site.master"
    AutoEventWireup="true" CodeBehind="Default.aspx.cs"
    Inherits="WebRole1._Default" %>

<asp:Content ID="HeaderContent" runat="server" ContentPlaceHolderID="HeadContent">
</asp:Content>
<asp:Content ID="BodyContent" runat="server" ContentPlaceHolderID="MainContent">
    <div>
        <asp:BulletedList ID="varDisplay" runat="server" BulletStyle="NotSet">
        </asp:BulletedList>
    </div>
    <div>
        <p>Fill in all fields and click <b>Make this call</b>.</p>
        <div>
            To:<br /><asp:TextBox ID="toNumber" runat="server" /><br /><br />
            Message:<br /><asp:TextBox ID="message" runat="server" /><br /><br />
            <asp:Button ID="callpage" runat="server" Text="Make this call"
                onclick="callpage_Click" />
        </div>
    </div>
</asp:Content>
```

## <a id="howtocreatecode"></a>Nasıl Yapılır: Çağrıyı yapmak için kodu oluşturma
Kullanıcı formu tamamladığında çağrılan aşağıdaki kod, çağrı iletisini oluşturur ve çağrıyı oluşturur. Bu örnekte, kod formundaki düğmenin OnClick olay işleyicisinde çalıştırılır. (Twilio hesabınızı ve kimlik doğrulama belirtecinizi, öğesine `accountSID` atanan yer tutucu değerleri yerine ve `authToken` aşağıdaki kodda kullanın.)

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Web;
using System.Web.UI;
using System.Web.UI.WebControls;
using Twilio;
using Twilio.Http;
using Twilio.Types;
using Twilio.Rest.Api.V2010;

namespace WebRole1
{
    public partial class _Default : System.Web.UI.Page
    {
        protected void Page_Load(object sender, EventArgs e)
        {

        }

        protected void callpage_Click(object sender, EventArgs e)
        {
            // Call processing happens here.

            // Use your account SID and authentication token instead of
            // the placeholders shown here.
            var accountSID = "ACNNNNNNNNNNNNNNNNNNNNNNNNNNNNNN";
            var authToken =  "NNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNN";

            // Instantiate an instance of the Twilio client.
            TwilioClient.Init(accountSID, authToken);

            // Retrieve the account, used later to retrieve the
            var account = AccountResource.Fetch(accountSID);

            this.varDisplay.Items.Clear();

            if (this.toNumber.Text == "" || this.message.Text == "")
            {
                this.varDisplay.Items.Add(
                        "You must enter a phone number and a message.");
            }
            else
            {
                // Retrieve the values entered by the user.
                var to = PhoneNumber(this.toNumber.Text);
                var from = new PhoneNumber("+14155992671");
                var myMessage = this.message.Text;

                // Create a URL using the Twilio message and the user-entered
                // text. You must replace spaces in the user's text with '%20'
                // to make the text suitable for a URL.
                var url = $"https://twimlets.com/message?Message%5B0%5D={myMessage.Replace(" ", "%20")}";
                var twimlUri = new Uri(url);

                // Display the endpoint, API version, and the URL for the message.
                this.varDisplay.Items.Add($"Using Twilio endpoint {
                }");
                this.varDisplay.Items.Add($"Twilioclient API Version is {apiVersion}");
                this.varDisplay.Items.Add($"The URL is {url}");

                // Place the call.
                var call = CallResource.create(to, from, url: twimlUri);
                this.varDisplay.Items.Add("Call status: " + call.Status);
            }
        }
    }
}
```

Çağrı yapılır ve Twilio uç noktası, API sürümü ve çağrı durumu görüntülenir. Aşağıdaki ekran görüntüsünde bir örnek çalıştıranın çıktısı gösterilmektedir.

![Twilio ve ASP.NET kullanarak Azure çağrı yanıtı][twilio_dotnet_basic_form_output]

TwiML hakkında daha fazla bilgiyi adresinde [https://www.twilio.com/docs/api/twiml][twiml]bulabilirsiniz. Deyin [https://www.twilio.com/docs/api/twiml/say][twilio_say] &lt; vediğerTwilioyüklemlerihakkındadahafazla&gt; bilgi adresinde bulabilirsiniz.

## <a id="nextsteps"></a>Sonraki adımlar
Bu kod, Azure 'da bir ASP.NET Web rolünde Twilio kullanarak temel işlevselliği göstermek için verilmiştir. Üretim sırasında Azure 'a dağıtım yapmadan önce, daha fazla hata işleme veya diğer özellik eklemek isteyebilirsiniz. Örneğin:

* Bir Web formu kullanmak yerine, telefon numaralarını depolamak ve metin çağırmak için Azure Blob depolamayı veya bir Azure SQL veritabanı örneğini kullanabilirsiniz. Blob 'Ları Azure 'da kullanma hakkında daha fazla bilgi için bkz. [.net 'Te Azure Blob depolama hizmetini kullanma][howto_blob_storage_dotnet]. SQL veritabanı kullanımı hakkında bilgi için bkz. [.NET uygulamalarında Azure SQL veritabanı 'nı kullanma][howto_sql_azure_dotnet].
* Formunuzdaki değerleri sabit `RoleEnvironment.getConfigurationSettings` kodlamak yerine dağıtımınızın yapılandırma ayarlarından Twilio hesap kimliği ve kimlik doğrulama belirtecini almak için kullanabilirsiniz. `RoleEnvironment` Sınıfı hakkında daha fazla bilgi için bkz. [Microsoft. windowsazure. serviceruntime ad alanı][azure_runtime_ref_dotnet].
* Adresindeki [https://www.twilio.com/docs/security][twilio_docs_security]Twilio güvenlik kılavuzlarını okuyun.
* Twilio hakkında daha fazla bilgi [https://www.twilio.com/docs][twilio_docs]edinin.

## <a name="seealso"></a>Ayrıca bkz.
* [Azure 'dan ses ve SMS özellikleri için Twilio kullanma](twilio-dotnet-how-to-use-for-voice-sms.md)

[twilio_console]: https://www.twilio.com/console
[twilio_pricing]: https://www.twilio.com/pricing
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_api]: https://www.twilio.com/voice/api
[verify_phone]: https://www.twilio.com/console/phone-numbers/verified

[twilio_dotnet_basic_form]: ./media/partner-twilio-cloud-services-dotnet-phone-call-web-role/WA_twilio_dotnet_basic_form.png
[twilio_dotnet_basic_form_output]: ./media/partner-twilio-cloud-services-dotnet-phone-call-web-role/WA_twilio_dotnet_basic_form_output.png

[twiml]: https://www.twilio.com/docs/api/twiml



[howto_twilio_voice_sms_dotnet]: /develop/net/how-to-guides/twilio/

[howto_blob_storage_dotnet]: https://www.windowsazure.com/develop/net/how-to-guides/blob-storage/

[howto_sql_azure_dotnet]: https://www.windowsazure.com/develop/net/how-to-guides/sql-database/


[twilio_docs_security]: https://www.twilio.com/docs/security
[twilio_docs]: https://www.twilio.com/docs
[twilio_say]: https://www.twilio.com/docs/api/twiml/say


[azure_runtime_ref_dotnet]: https://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.serviceruntime.aspx
[azure_webroles_get_started]: https://docs.microsoft.com/azure/cloud-services/cloud-services-dotnet-get-started
