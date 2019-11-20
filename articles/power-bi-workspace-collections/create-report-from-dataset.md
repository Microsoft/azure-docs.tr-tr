---
title: Power BI çalışma alanı koleksiyonları'nda bir veri kümesinden yeni rapor oluşturma | Microsoft Docs
description: Power BI çalışma alanı koleksiyonu raporları artık kendi uygulamanızda bir veri kümesinden oluşturulabilir.
services: power-bi-workspace-collections
ms.service: power-bi-embedded
author: rkarlin
ms.author: rkarlin
ms.topic: article
ms.workload: powerbi
ms.date: 09/20/2017
ms.openlocfilehash: 2034c62a17b71b92b43a7afd794c2c172288d58c
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672458"
---
# <a name="create-a-new-report-from-a-dataset-in-power-bi-workspace-collections"></a>Power BI çalışma alanı koleksiyonları'nda bir veri kümesinden yeni rapor oluşturma

Power BI çalışma alanı koleksiyonu raporları artık kendi uygulamanızda bir veri kümesinden oluşturulabilir.

> [!IMPORTANT]
> Power BI Çalışma Alanı Koleksiyonları kullanım dışı bırakılmıştır ve Haziran 2018'e kadar veya anlaşmanızda belirtilen süre boyunca kullanılabilecektir. Uygulamanızda kesinti yaşanmaması için Power BI Embedded'a geçirmeyi planlamanız önerilir. Verilerinizi Power BI Embedded'a nasıl taşıyacağınızı öğrenmek için bkz. [Power BI Çalışma Alanı Koleksiyonları'nı Power BI Embedded'a geçirme](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

Kimlik doğrulama yöntemi, bir raporu katıştırma benzerdir. Belirli bir veri kümesine erişim belirteçleri dayanır. PowerBI.com için kullanılan belirteçleri, Azure Active Directory (AAD) verilir. Power BI çalışma alanı koleksiyonu belirteçleri, kendi uygulama tarafından verilir.

Katıştırılmış Rapor oluştururken, verilen belirteçler için belirli bir veri kümesi var. Belirteçleri her benzersiz belirtece sahip olmak için ekleme URL'si ile aynı öğede ilişkili olmalıdır. Bir katıştırılmış raporu oluşturmak için *Dataset.Read ve Workspace.Report.Create* kapsamları erişim belirtecinde sağlanmalıdır.

## <a name="create-access-token-needed-to-create-new-report"></a>Yeni bir rapor oluşturmak için gereken erişim belirteci oluşturma

Power BI çalışma alanı koleksiyonları kullanan bir ekleme belirteci, HMAC olduğu JSON Web belirteçlerini imzalanmış. Belirteçler, Power BI çalışma alanı koleksiyonunuz erişim anahtarı ile imzalanmıştır. Ekleme belirteçleri, varsayılan olarak, bir uygulamaya ekleme için bir rapor salt okunur erişim sağlamak için kullanılır. Ekleme belirteçleri için belirli bir rapor verilir ve bir ekleme URL'si ile ilişkili olmalıdır.

Erişim anahtarlarını belirteçleri oturum/şifrelemek için kullanılan erişim belirteçlerini sunucuda oluşturulması gerekir. Bir erişim belirteci oluşturma hakkında daha fazla bilgi için bkz: [kimlik doğrulama ve yetkilendirme ile Power BI çalışma alanı koleksiyonları](app-token-flow.md). Ayrıca inceleyebilirsiniz [CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN) yöntemi. Ne için Power BI .NET SDK kullanarak gibi görünür bir örnek aşağıda verilmiştir.

Bu örnekte, yeni bir rapor oluşturmak istediğimiz bizim veri kümesi kimliği sahibiz. Biz de kapsamları eklemenize gerek *Dataset.Read ve Workspace.Report.Create*.

*PowerBIToken sınıfı* , yüklemenizi gerektirir [Power BI çekirdek NuGut paketi](https://www.nuget.org/packages/Microsoft.PowerBI.Core/).

**NuGet paketi yüklemesi**

```powershell
Install-Package Microsoft.PowerBI.Core
```

**C# kodu**

```csharp
using Microsoft.PowerBI.Security;

// rlsUsername and roles are optional
string scopes = "Dataset.Read Workspace.Report.Create";
PowerBIToken embedToken = PowerBIToken.CreateReportEmbedTokenForCreation(workspaceCollectionName, workspaceId, datasetId, null, null, scopes);

var token = embedToken.Generate("{access key}");
```

## <a name="create-a-new-blank-report"></a>Yeni boş bir rapor oluşturun

Yeni bir rapor oluşturmak için oluşturma yapılandırmasını sağlanmalıdır. Bu erişim belirteci, embedURL ve raporun, karşılarında oluşturmak istiyoruz Datasetıd içermelidir. Bu, nuget yüklemenizi gerektirir [Power BI JavaScript paket](https://www.nuget.org/packages/Microsoft.PowerBI.JavaScript/). EmbedUrl yalnızca olacaktır https://embedded.powerbi.com/appTokenReportEmbed.

> [!NOTE]
> Kullanabileceğiniz [JavaScript rapor ekleme örneği](https://microsoft.github.io/PowerBI-JavaScript/demo/) işlevselliğini test etmek için. Ayrıca kod örnekleri için kullanılabilen farklı işlemleri sağlar.

**NuGet paketi yüklemesi**

```powershell
Install-Package Microsoft.PowerBI.JavaScript
```

**JavaScript kodu**

```html
<div id="reportContainer"></div>

<script>
var embedCreateConfiguration = {
        accessToken: 'eyJ0eXAiO...Qron7qYpY9MI',
        embedUrl: 'https://embedded.powerbi.com/appTokenReportEmbed',
        datasetId: '5dac7a4a-4452-46b3-99f6-a25915e0fe55',
    };
    
    // Grab the reference to the div HTML element that will host the report
    var reportContainer = $('#reportContainer')[0];

    // Create report
    var report = powerbi.createReport(reportContainer, embedCreateConfiguration);
</script>
```

Çağırma *powerbi.createReport()* içinde görünen boş bir tuval düzenleme modunda yapar *div* öğesi.

![Yeni bir boş rapor](media/create-report-from-dataset/create-new-report.png)

## <a name="save-new-reports"></a>Yeni raporları kaydetme

Rapor, çağırana kadar oluşturulmaz **Kaydet** işlemi. Bu dosya menüsünden veya JavaScript yapılabilir.

```javascript
 // Get a reference to the embedded report.
    report = powerbi.get(reportContainer);
    
    var saveAsParameters = {
        name: "newReport"
    };

    // SaveAs report
    report.saveAs(saveAsParameters);
```

> [!IMPORTANT]
> Yeni bir rapor yalnızca sonra oluşturulduğunda **Kaydet** çağrılır. Kaydettikten sonra tuval düzenleme modu ve rapor yine de veri kümesi gösterir. Yeni raporu diğer herhangi bir raporda olduğu gibi yeniden gerekir.

![Dosya menüsü - kaydetme olarak](media/create-report-from-dataset/save-new-report.png)

## <a name="load-the-new-report"></a>Yeni rapor

Yani uygulama normal bir raporda katıştırır aynı şekilde ekleme gerek yeni raporla etkileşim kurmak için yeni bir belirteç özellikle yeni rapor için verilmiş olması gerekir ve ardından ekleme yöntemini çağırın.

```html
<div id="reportContainer"></div>
<script>
var embedConfiguration = {
        accessToken: 'eyJ0eXAiO...Qron7qYpY9MJ',
        embedUrl: 'https://embedded.powerbi.com/appTokenReportEmbed',
        reportId: '5dac7a4a-4452-46b3-99f6-a25915e0fe54',
    };
    
    // Grab the reference to the div HTML element that will host the report
    var reportContainer = $('#reportContainer')[0];

    // Embed report
    var report = powerbi.embed(reportContainer, embedConfiguration);
</script>
```

## <a name="automate-save-and-load-of-a-new-report-using-the-saved-event"></a>Kaydetme otomatikleştirin ve "kaydedildi" olayını kullanarak yeni bir rapor yükleme

"Farklı Kaydet" işlemini otomatik hale getirmek için ve ardından yeni bir rapor yüklenirken "kaydedildi" olayının kullanabilir. Bu olay, ne zaman tetiklenir kaydetme işlemi tamamlandığında ve yeni Reportıd, rapor adı, eski Reportıd (varsa) içeren bir Json nesnesi döndürür ve Farklı Kaydet işlemi varsa veya kaydedebilirsiniz.

```json
{
  "reportObjectId": "5dac7a4a-4452-46b3-99f6-a25915e0fe54",
  "reportName": "newReport",
  "saveAs": true,
  "originalReportObjectId": null
}
```

"Kaydedildi" olayı dinleyebilir işlemini otomatikleştirmek için yeni Reportıd almak, yeni belirteç oluştur ve ile yeni bir rapor ekleme.

```html
<div id="reportContainer"></div>
<script>
var embedCreateConfiguration = {
        accessToken: 'eyJ0eXAiO...Qron7qYpY9MI',
        embedUrl: 'https://embedded.powerbi.com/appTokenReportEmbed',
        datasetId: '5dac7a4a-4452-46b3-99f6-a25915e0fe55',
    };
    
    // Grab the reference to the div HTML element that will host the report
    var reportContainer = $('#reportContainer')[0];

    // Create report
    var report = powerbi.createReport(reportContainer, embedCreateConfiguration);


   var saveAsParameters = {
        name: "newReport"
    };

    // SaveAs report
    report.saveAs(saveAsParameters);

    // report.on will add an event handler which prints to Log window.
    report.on("saved", function(event) {
        
         // get new Token
         var newReportId =  event.detail.reportObjectId;

        // create new Token. This is a function that the application should provide
        var newToken = createAccessToken(newReportId,scopes /*provide the wanted scopes*/);
        
        
    var embedConfiguration = {
        accessToken: newToken ,
        embedUrl: 'https://embedded.powerbi.com/appTokenReportEmbed',
        reportId: newReportId,
    };

    // Embed report
    var report = powerbi.embed(reportContainer, embedConfiguration);
       
   // report.off removes a given event handler if it exists.
   report.off("saved");
    });
</script>
```

## <a name="see-also"></a>Ayrıca bkz.

[Bir örnek ile kullanmaya başlama](get-started-sample.md)  
[Raporları kaydetme](save-reports.md)  
[Rapor ekleme](embed-report.md)  
[Power BI Çalışma Alanı Koleksiyonları ile kimlik doğrulaması ve yetkilendirme](app-token-flow.md)  
[Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)  
[JavaScript Örnek Ekleme](https://microsoft.github.io/PowerBI-JavaScript/demo/)  
[Power BI çekirdek NuGut paketi](https://www.nuget.org/packages/Microsoft.PowerBI.Core/)  
[Power BI JavaScript paket](https://www.nuget.org/packages/Microsoft.PowerBI.JavaScript/)  

Başka sorunuz mu var? [Power BI Topluluğu'nu deneyin](https://community.powerbi.com/)
