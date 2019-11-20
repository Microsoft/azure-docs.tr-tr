---
title: Azure Power BI çalışma alanı koleksiyonları'nda bir rapor ekleme | Microsoft Docs
description: Uygulamanıza Power BI çalışma alanı koleksiyonları'nda olan bir raporu katıştırma hakkında bilgi edinin.
services: power-bi-workspace-collections
ms.service: power-bi-embedded
author: rkarlin
ms.author: rkarlin
ms.topic: article
ms.workload: powerbi
ms.date: 09/20/2017
ms.openlocfilehash: 4e125e57c290688d5a65f6d70143100cbd3462cb
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67669058"
---
# <a name="embed-a-report-in-power-bi-workspace-collections"></a>Power BI çalışma alanı koleksiyonları'nda bir rapor ekleme

Uygulamanıza Power BI çalışma alanı koleksiyonları'nda olan bir raporu katıştırma hakkında bilgi edinin.

> [!IMPORTANT]
> Power BI Çalışma Alanı Koleksiyonları kullanım dışı bırakılmıştır ve Haziran 2018'e kadar veya anlaşmanızda belirtilen süre boyunca kullanılabilecektir. Uygulamanızda kesinti yaşanmaması için Power BI Embedded'a geçirmeyi planlamanız önerilir. Verilerinizi Power BI Embedded'a nasıl taşıyacağınızı öğrenmek için bkz. [Power BI Çalışma Alanı Koleksiyonları'nı Power BI Embedded'a geçirme](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

Aslında bir raporu uygulamanıza eklemek nasıl atacağız. Bu, var olan bir rapor içinde bir çalışma alanı çalışma alanı koleksiyonunuz zaten varsayılır. Bu adım henüz yapmadıysanız bkz [Power BI çalışma alanı koleksiyonları ile çalışmaya başlama](get-started.md).

Uygulamanızı Power BI çalışma alanı koleksiyonları ile kolayca oluşturmak için .NET (C#) veya Node.js SDK'sını JavaScript kullanabilirsiniz.

## <a name="using-the-access-keys-to-use-rest-apis"></a>Erişim anahtarlarını REST API'lerini kullanma

REST API'sini çağırmak için belirli bir çalışma alanı koleksiyonu için Azure portalından alabilirsiniz erişim anahtarı geçirebilirsiniz. Daha fazla bilgi için [Power BI çalışma alanı koleksiyonları ile çalışmaya başlama](get-started.md).

## <a name="get-a-report-id"></a>Rapor kimliklerinden

Her bir erişim belirteci, bir raporda temel alır. Eklemek istediğiniz raporu için belirli rapor kimliğini almanız gerekir. Bu temel alınarak çağrıları yapılabilir [Get Reports](https://msdn.microsoft.com/library/azure/mt711510.aspx) REST API. Bu rapor kimliği ve ekleme URL'sini döndürür. Bu, Power BI .NET SDK'sını kullanarak veya REST API'sini doğrudan çağırmak yapılabilir.

### <a name="using-the-power-bi-net-sdk"></a>Power BI .NET SDK'sını kullanma

.NET SDK'sı kullanırken, Azure portalından aldığınız erişim anahtarını temel alan bir belirteç kimlik bilgileri oluşturmanız gerekir. Bu, yüklemenizi gerektirir [Power BI API NuGet paketi](https://www.nuget.org/profiles/powerbi).

**NuGet paketi yüklemesi**

```powershell
Install-Package Microsoft.PowerBI.Api
```

**C# kodu**

```csharp
using Microsoft.PowerBI.Api.V1;
using Microsoft.Rest;

var credentials = new TokenCredentials("{access key}", "AppKey");
var client = new PowerBIClient(credentials);
client.BaseUri = new Uri(https://api.powerbi.com);

var reports = (IList<Report>)client.Reports.GetReports(workspaceCollectionName, workspaceId).Value;

// Select the report that you want to work with from the collection of reports.
```

### <a name="calling-the-rest-api-directly"></a>REST API'sini doğrudan çağırmak

```csharp
System.Net.WebRequest request = System.Net.WebRequest.Create("https://api.powerbi.com/v1.0/collections/{collectionName}/workspaces/{workspaceId}/Reports") as System.Net.HttpWebRequest;

request.Method = "GET";
request.ContentLength = 0;
request.Headers.Add("Authorization", String.Format("AppKey {0}", accessToken.Value));

using (var response = request.GetResponse() as System.Net.HttpWebResponse)
{
    using (var reader = new System.IO.StreamReader(response.GetResponseStream()))
    {
        // Work with JSON response to get the report you want to work with.
    }

}
```

## <a name="create-an-access-token"></a>Erişim belirteci oluşturma

Power BI çalışma alanı koleksiyonları kullanım ekleme belirteçleri, JSON Web belirteçlerini imzalı HMAC olduğu. Belirteçler, Power BI çalışma alanı koleksiyonunuz erişim anahtarı ile imzalanmıştır. Ekleme belirteçleri, varsayılan olarak, bir uygulamaya ekleme için bir rapor salt okunur erişim sağlamak için kullanılır. Ekleme belirteçleri için belirli bir rapor verilir ve bir ekleme URL'si ile ilişkili olmalıdır.

Erişim anahtarlarını belirteçleri oturum/şifrelemek için kullanılan erişim belirteçlerini sunucuda oluşturulması gerekir. Bir erişim belirteci oluşturma hakkında daha fazla bilgi için bkz: [kimlik doğrulama ve yetkilendirme ile Power BI çalışma alanı koleksiyonları](app-token-flow.md). Ayrıca inceleyebilirsiniz [CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN) yöntemi. Ne için Power BI .NET SDK kullanarak gibi görünür bir örnek aşağıda verilmiştir.

Daha önce aldığınız rapor kimliği kullanın. Ekleme belirtecini oluşturulduktan sonra javascript açısından kullanabileceğiniz belirteci oluşturmak için erişim anahtarı kullanır. *PowerBIToken sınıfı* , yüklemenizi gerektirir [Power BI çekirdek NuGut paketi](https://www.nuget.org/packages/Microsoft.PowerBI.Core/).

**NuGet paketi yüklemesi**

```powershell
Install-Package Microsoft.PowerBI.Core
```

**C# kodu**

```csharp
using Microsoft.PowerBI.Security;

// rlsUsername, roles and scopes are optional.
embedToken = PowerBIToken.CreateReportEmbedToken(workspaceCollectionName, workspaceId, reportId, rlsUsername, roles, scopes);

var token = embedToken.Generate("{access key}");
```

### <a name="adding-permission-scopes-to-embed-tokens"></a>Ekleme belirteçleri için izin kapsamları ekleme

Ekleme belirteçlerinin kullanırken, erişim izni verdiğiniz kaynak kullanımını kısıtlamak isteyebilirsiniz. Bu nedenle, kapsamlı izinlere sahip bir belirteç oluşturabilirsiniz. Daha fazla bilgi için [kapsamları](app-token-flow.md#scopes)

## <a name="embed-using-javascript"></a>JavaScript kullanarak ekleme

Erişim belirteci ve rapor Kimliğini aldıktan sonra biz JavaScript kullanarak rapor ekleyebilir. Bu, NuGet yüklemenizi gerektirir [Power BI JavaScript paket](https://www.nuget.org/packages/Microsoft.PowerBI.JavaScript/). EmbedUrl yalnızca olacaktır https://embedded.powerbi.com/appTokenReportEmbed.

> [!NOTE]
> Kullanabileceğiniz [JavaScript rapor ekleme örneği](https://microsoft.github.io/PowerBI-JavaScript/demo/) işlevselliğini test etmek için. Ayrıca kod örnekleri için kullanılabilen farklı işlemleri sağlar.

**NuGet paketi yüklemesi**

```powershell
Install-Package Microsoft.PowerBI.JavaScript
```

**JavaScript kodu**

```html
<script src="/scripts/powerbi.js"></script>
<div id="reportContainer"></div>

<script>
var embedConfiguration = {
    type: 'report',
    accessToken: 'eyJ0eXAiO...Qron7qYpY9MI',
    id: '5dac7a4a-4452-46b3-99f6-a25915e0fe55',
    embedUrl: 'https://embedded.powerbi.com/appTokenReportEmbed'
};

var $reportContainer = $('#reportContainer');
var report = powerbi.embed($reportContainer.get(0), embedConfiguration);
</script>
```

### <a name="set-the-size-of-embedded-elements"></a>Katıştırılmış öğeleri boyutunu ayarlama

Rapor kapsayıcısının boyutuna bağlı olarak otomatik olarak katıştırılır. Gömülü bir öğe varsayılan boyutu geçersiz kılmak için bir CSS sınıfı özniteliği veya satır içi stil genişlik ve yükseklik eklemeniz yeterlidir.

## <a name="see-also"></a>Ayrıca bkz.

[Bir örnek ile kullanmaya başlama](get-started-sample.md)  
[Power BI Çalışma Alanı Koleksiyonları ile kimlik doğrulaması ve yetkilendirme](app-token-flow.md)  
[CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN)  
[JavaScript Örnek Ekleme](https://microsoft.github.io/PowerBI-JavaScript/demo/)  
[Power BI JavaScript paket](https://www.nuget.org/packages/Microsoft.PowerBI.JavaScript/)  
[Power BI API NuGet paketi](https://www.nuget.org/profiles/powerbi)
[Power BI çekirdek NuGut paketi](https://www.nuget.org/packages/Microsoft.PowerBI.Core/)  
[Power BI-CSharp Git deposu](https://github.com/Microsoft/PowerBI-CSharp)  
[Power BI düğümlü Git deposu](https://github.com/Microsoft/PowerBI-Node)  

Başka sorunuz mu var? [Power BI Topluluğu'nu deneyin](https://community.powerbi.com/)
