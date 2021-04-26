---
title: SendGrid e-posta hizmetini kullanma (.NET) | Microsoft Docs
description: Azure 'da SendGrid e-posta hizmeti ile e-posta gönderme hakkında bilgi edinin. Kod örnekleri C# dilinde yazılmıştır ve .NET API kullanır.
services: ''
documentationcenter: .net
author: georgewallace
ms.author: gwallace
editor: ''
ms.assetid: 21bf4028-9046-476b-9799-3d3082a0f84c
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/15/2017
ms.reviewer: dx@sendgrid.com
ms.custom: devx-track-dotnet
ms.openlocfilehash: ae816d2be592ab774500d1cfe8f2f6a7b7905b91
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98196563"
---
# <a name="how-to-send-email-using-sendgrid-with-azure"></a>Azure ile SendGrid kullanarak e-posta gönderme
## <a name="overview"></a>Genel Bakış
Bu kılavuzda, Azure 'da SendGrid e-posta hizmetiyle ortak programlama görevlerinin nasıl gerçekleştirileceği gösterilmektedir. Örnekler C dilinde yazılır \# ve 1,3 .NET Standard destekler. Kapsanan senaryolar, e-posta oluşturma, e-posta gönderme, ek ekleme ve çeşitli posta ve izleme ayarlarını etkinleştirme içerir. SendGrid ve e-posta gönderme hakkında daha fazla bilgi için [sonraki adımlar][Next steps] bölümüne bakın.

## <a name="what-is-the-sendgrid-email-service"></a>SendGrid e-posta hizmeti nedir?
SendGrid, özel tümleştirmeyi kolaylaştıran esnek API 'lerle birlikte güvenilir [işlem e-posta teslimi], ölçeklenebilirlik ve gerçek zamanlı çözümlemeler sağlayan [bulut tabanlı bir e-posta hizmetidir] . Ortak SendGrid kullanım örnekleri şunları içerir:

* Müşterilere alındıları veya satın alma teyitlerini otomatik olarak gönderme.
* Müşteriler aylık ilanlara ve promosyonlar göndermek için dağıtım listelerini yönetme.
* Engellenen e-posta ve müşteri katılımı gibi şeyler için gerçek zamanlı ölçümler toplama.
* Müşteri sorguları iletiliyor.
* Gelen e-postaları işleme.

Daha fazla bilgi için, [https://sendgrid.com](https://sendgrid.com) veya SendGrid 'In [C# Kitaplığı][sendgrid-csharp] GitHub deposu ' nu ziyaret edin.

## <a name="create-a-sendgrid-account"></a>SendGrid hesabı oluşturma
[!INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="reference-the-sendgrid-net-class-library"></a>SendGrid .NET sınıf kitaplığına başvurma
[SendGrid NuGet paketi](https://www.nuget.org/packages/Sendgrid) , SENDGRID API 'sini almanın ve uygulamanızı tüm bağımlılıklarla yapılandırmanın en kolay yoludur. NuGet, Microsoft Visual Studio 2015 ve üzeri sürümlerde bulunan bir Visual Studio uzantısıdır ve bu da kitaplıkları ve araçları yüklemeyi ve güncelleştirmeyi kolaylaştırır.

> [!NOTE]
> Visual Studio 2015 'den önceki bir sürümünü çalıştırıyorsanız NuGet yüklemek için, ' yi ziyaret edin [https://www.nuget.org](https://www.nuget.org) ve **NuGet 'i yükleyebilirsiniz** düğmesine tıklayın.
>
>

Uygulamanıza SendGrid NuGet paketini yüklemek için aşağıdakileri yapın:

1. **Yeni proje** ' ye tıklayın ve bir **şablon** seçin.

   ![Yeni proje oluşturma][create-new-project]
2. **Çözüm Gezgini**, **Başvurular**' a sağ tıklayın ve ardından **NuGet Paketlerini Yönet**' e tıklayın.

   ![SendGrid NuGet paketi][SendGrid-NuGet-package]
3. **SendGrid** için arama yapın ve sonuçlar listesinden **SendGrid** öğesini seçin.
4. Bu makalede gösterilen nesne modeliyle ve API 'lerle çalışabilmeniz için sürüm açılan menüsünden NuGet paketinin en son kararlı sürümünü seçin.

   ![SendGrid paketi][sendgrid-package]
5. Yüklemeyi **gerçekleştirmek için yükleme** ' ye tıklayın ve ardından bu iletişim kutusunu kapatın.

SendGrid 'in .NET sınıf kitaplığına **SendGrid** adı verilir. Aşağıdaki ad alanlarını içerir:

* SendGrid 'in API 'SI ile iletişim kurmak için **SendGrid** .
* E-postaların nasıl gönderileceğini belirten SendGridMessage nesnelerini kolayca oluşturmak üzere yardımcı yöntemler için **SendGrid. yardımcılar. Mail** .

Aşağıdaki kod ad alanı bildirimlerini, içindeki SendGrid e-posta hizmetine programlı bir şekilde erişmek istediğiniz herhangi bir C# dosyasının üst kısmına ekleyin.

```csharp
using SendGrid;
using SendGrid.Helpers.Mail;
```

## <a name="how-to-create-an-email"></a>Nasıl yapılır: e-posta oluşturma
Bir e-posta iletisi oluşturmak için **Sendgridmessage** nesnesini kullanın. İleti nesnesi oluşturulduktan sonra e-posta göndericisi, e-posta alıcısı ve e-postanın konusu ve gövdesi dahil olmak üzere özellikleri ve yöntemleri ayarlayabilirsiniz.

Aşağıdaki örnek, tam olarak doldurulmuş bir e-posta nesnesinin nasıl oluşturulacağını gösterir:

```csharp
var msg = new SendGridMessage();

msg.SetFrom(new EmailAddress("dx@example.com", "SendGrid DX Team"));

var recipients = new List<EmailAddress>
{
    new EmailAddress("jeff@example.com", "Jeff Smith"),
    new EmailAddress("anna@example.com", "Anna Lidman"),
    new EmailAddress("peter@example.com", "Peter Saddow")
};
msg.AddTos(recipients);

msg.SetSubject("Testing the SendGrid C# Library");

msg.AddContent(MimeType.Text, "Hello World plain text!");
msg.AddContent(MimeType.Html, "<p>Hello World!</p>");
```

**SendGrid** türü tarafından desteklenen tüm özellikler ve yöntemler hakkında daha fazla bilgi için bkz. GitHub üzerinde [SendGrid-CSharp][sendgrid-csharp] .

## <a name="how-to-send-an-email"></a>Nasıl yapılır: e-posta gönderme
Bir e-posta iletisi oluşturduktan sonra SendGrid 'in API 'sini kullanarak gönderebilirsiniz. Alternatif olarak, kullanabilirsiniz [. NET 'in yerleşik kitaplığı][NET-library].

E-posta göndermek için SendGrid API anahtarınızı sağlamanız gerekir. API anahtarlarının nasıl yapılandırılacağı hakkında ayrıntılara ihtiyacınız varsa lütfen SendGrid 'in API anahtarları [belgelerini][documentation]ziyaret edin.

Bu kimlik bilgilerini, uygulama ayarları ' na tıklayarak ve uygulama ayarları altına anahtar/değer çiftleri ekleyerek Azure portal aracılığıyla saklayabilirsiniz.

![Azure Uygulama ayarları][azure_app_settings]

Ardından, bunlara aşağıdaki şekilde erişebilirsiniz:

```csharp
var apiKey = System.Environment.GetEnvironmentVariable("SENDGRID_APIKEY");
var client = new SendGridClient(apiKey);
```

Aşağıdaki örneklerde, bir konsol uygulamasıyla SendGrid Web API 'sini kullanarak bir e-posta iletisinin nasıl gönderileceği gösterilmektedir.

```csharp
using System;
using System.Threading.Tasks;
using SendGrid;
using SendGrid.Helpers.Mail;

namespace Example
{
    internal class Example
    {
        private static void Main()
        {
            Execute().Wait();
        }

        static async Task Execute()
        {
            var apiKey = System.Environment.GetEnvironmentVariable("SENDGRID_APIKEY");
            var client = new SendGridClient(apiKey);
            var msg = new SendGridMessage()
            {
                From = new EmailAddress("test@example.com", "DX Team"),
                Subject = "Hello World from the SendGrid CSharp SDK!",
                PlainTextContent = "Hello, Email!",
                HtmlContent = "<strong>Hello, Email!</strong>"
            };
            msg.AddTo(new EmailAddress("test@example.com", "Test User"));
            var response = await client.SendEmailAsync(msg);
        }
    }
}
```

## <a name="how-to-send-email-from-asp-net-core-api-using-mailhelper-class"></a>Nasıl yapılır: MailHelper sınıfını kullanarak ASP .NET Core API 'sinden e-posta gönderme

Aşağıdaki örnek, `MailHelper` ad alanı sınıfını kullanarak ASP .NET Core API 'sinden birden çok kişiye tek bir e-posta göndermek için kullanılabilir `SendGrid.Helpers.Mail` . Bu örnekte ASP .NET Core 1,0 kullandık. 

Bu örnekte, API anahtarı, `appsettings.json` Yukarıdaki örneklerde gösterildiği gibi Azure Portal geçersiz kılınabilen dosyada depolandı.

Dosyanın içeriği şuna benzemelidir `appsettings.json` :

```json
{
  "Logging": {
    "IncludeScopes": false,
    "LogLevel": {
      "Default": "Debug",
      "System": "Information",
      "Microsoft": "Information"
    }
  },
  "SENDGRID_API_KEY": "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
}
```

İlk olarak, aşağıdaki kodu `Startup.cs` .NET Core API projesi dosyasına eklememiz gerekir. Bu, `SENDGRID_API_KEY` `appsettings.json` API denetleyicisindeki bağımlılık ekleme 'yı kullanarak dosyadan erişebilmemiz için gereklidir. `IConfiguration`Arabirim, aşağıdaki yönteme eklendikten sonra denetleyicinin oluşturucusuna eklenebilir `ConfigureServices` . Dosya içeriği, `Startup.cs` gerekli kodu ekledikten sonra aşağıdaki gibi görünür:

```csharp
    public IConfigurationRoot Configuration { get; }

    public void ConfigureServices(IServiceCollection services)
    {
        // Add mvc here
        services.AddMvc();
        services.AddSingleton<IConfiguration>(Configuration);
    }
```

Denetleyicide, arabirimden ekleme sonra `IConfiguration` , `CreateSingleEmailToMultipleRecipients` `MailHelper` birden çok alıcıya tek bir e-posta göndermek için sınıfının yöntemini kullanabiliriz. Yöntemi adlı bir ek Boolean parametresini kabul eder `showAllRecipients` . Bu parametre e-posta alıcılarının e-posta üstbilgisinin to bölümünde e-posta alıcılarının her bir e-posta adresini görüp göremeyeceğini denetlemek için kullanılabilir. Denetleyicinin örnek kodu aşağıdaki gibi olmalıdır 

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using SendGrid;
using SendGrid.Helpers.Mail;
using Microsoft.Extensions.Configuration;

namespace SendgridMailApp.Controllers
{
    [Route("api/[controller]")]
    public class NotificationController : Controller
    {
       private readonly IConfiguration _configuration;

       public NotificationController(IConfiguration configuration)
       {
         _configuration = configuration;
       }

       [Route("SendNotification")]
       public async Task PostMessage()
       {
          var apiKey = _configuration.GetSection("SENDGRID_API_KEY").Value;
          var client = new SendGridClient(apiKey);
          var from = new EmailAddress("test1@example.com", "Example User 1");
          List<EmailAddress> tos = new List<EmailAddress>
          {
              new EmailAddress("test2@example.com", "Example User 2"),
              new EmailAddress("test3@example.com", "Example User 3"),
              new EmailAddress("test4@example.com","Example User 4")
          };

          var subject = "Hello world email from Sendgrid ";
          var htmlContent = "<strong>Hello world with HTML content</strong>";
          var displayRecipients = false; // set this to true if you want recipients to see each others mail id 
          var msg = MailHelper.CreateSingleEmailToMultipleRecipients(from, tos, subject, "", htmlContent, false);
          var response = await client.SendEmailAsync(msg);
      }
   }
}
```

## <a name="how-to-add-an-attachment"></a>Nasıl yapılır: ek ekleme
Ekler, **AddAttachment** yöntemini çağırarak ve eklemek istediğiniz dosya adı ve Base64 olarak kodlanmış içeriği en düşük düzeyde belirtilerek bir iletiye eklenebilir. Eklemek istediğiniz her dosya için veya **AddAttachments** yöntemini kullanarak bu yöntemi çağırarak birden çok eki dahil edebilirsiniz. Aşağıdaki örnekte, bir iletiye ek ekleme gösterilmektedir:

```csharp
var banner2 = new Attachment()
{
    Content = Convert.ToBase64String(raw_content),
    Type = "image/png",
    Filename = "banner2.png",
    Disposition = "inline",
    ContentId = "Banner 2"
};
msg.AddAttachment(banner2);
```

## <a name="how-to-use-mail-settings-to-enable-footers-tracking-and-analytics"></a>Nasıl yapılır: altbilgileri, izlemeyi ve analizlerini etkinleştirmek için posta ayarlarını kullanma
SendGrid, posta ayarlarını ve izleme ayarlarını kullanarak ek e-posta işlevselliği sağlar. Bu ayarlar, tıklama izleme, Google Analytics, abonelik izleme vb. gibi belirli işlevleri etkinleştirmek için bir e-posta iletisine eklenebilir. Uygulamaların tam listesi için bkz. [Ayarlar belgeleri][settings-documentation].

Uygulamalar, **Sendgridmessage** sınıfının bir parçası olarak uygulanan yöntemler kullanılarak **SendGrid** e-posta iletilerine uygulanabilir. Aşağıdaki örnekler, alt bilgiyi gösterir ve izleme filtrelerini tıklama:

Aşağıdaki örnekler, alt bilgiyi gösterir ve izleme filtrelerini tıklama:

### <a name="footer-settings"></a>Altbilgi ayarları

```csharp
    msg.SetFooterSetting(
        true,
        "Some Footer HTML",
        "<strong>Some Footer Text</strong>");
```

### <a name="click-tracking"></a>İzleme ' ye tıklayın

```csharp
msg.SetClickTracking(true);
```

## <a name="how-to-use-additional-sendgrid-services"></a>Nasıl yapılır: ek SendGrid Hizmetleri kullanma
SendGrid, Azure uygulamanızda ek işlevlerden yararlanmak için kullanabileceğiniz çeşitli API 'Ler ve Web kancaları sunar. Daha fazla ayrıntı için bkz. [SendGrid API başvurusu][SendGrid API documentation].

## <a name="next-steps"></a>Sonraki adımlar
SendGrid e-posta hizmetinin temellerini öğrendiğinize göre, daha fazla bilgi edinmek için bu bağlantıları izleyin.

* SendGrid C \# kitaplığı deposu: [SendGrid-CSharp][sendgrid-csharp]
* SendGrid API belgeleri: <https://sendgrid.com/docs>

[Next steps]: #next-steps
[What is the SendGrid Email Service?]: #whatis
[Create a SendGrid Account]: #createaccount
[Reference the SendGrid .NET Class Library]: #reference
[How to: Create an Email]: #createemail
[How to: Send an Email]: #sendemail
[How to: Add an Attachment]: #addattachment
[How to: Use Filters to Enable Footers, Tracking, and Analytics]: #usefilters
[How to: Use Additional SendGrid Services]: #useservices

[create-new-project]: ./media/sendgrid-dotnet-how-to-send-email/new-project.png
[SendGrid-NuGet-package]: ./media/sendgrid-dotnet-how-to-send-email/reference.png
[sendgrid-package]: ./media/sendgrid-dotnet-how-to-send-email/sendgrid-package.png
[azure_app_settings]: ./media/sendgrid-dotnet-how-to-send-email/azure-app-settings.png
[sendgrid-csharp]: https://github.com/sendgrid/sendgrid-csharp
[SMTP vs. Web API]: https://sendgrid.com/docs/Integrate/index.html
[App Settings]: https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html
[SendGrid API documentation]: https://sendgrid.com/docs/api-reference/
[NET-library]: https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html#-Using-NETs-Builtin-SMTP-Library
[documentation]: https://sendgrid.com/docs/Classroom/Send/api_keys.html
[settings-documentation]: https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html

[bulut tabanlı e-posta hizmeti]: https://sendgrid.com/solutions
[işlem e-posta teslimi]: https://sendgrid.com/use-cases/transactional-email

