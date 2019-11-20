---
title: Görünüm arasında geçiş yapmak ve düzenleme modundan Power BI çalışma alanı koleksiyonları'ndaki raporlar için | Microsoft Docs
description: Görünüm arasında geçiş yapmak ve raporlarınızı Power BI çalışma alanı koleksiyonları içinde modunu düzenleme hakkında bilgi edinin.
services: power-bi-workspace-collections
ms.service: power-bi-embedded
author: rkarlin
ms.author: rkarlin
ms.topic: article
ms.workload: powerbi
ms.date: 09/20/2017
ms.openlocfilehash: 327f2fdcd4d1bc9e71e3aabb3541c6fd30f02811
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672361"
---
# <a name="toggle-between-view-and-edit-mode-for-reports-in-power-bi-workspace-collections"></a>Düzenleme modundan Power BI çalışma alanı koleksiyonları'raporların ve görünüm arasında geçiş

Görünüm arasında geçiş yapmak ve raporlarınızı Power BI çalışma alanı koleksiyonları içinde modunu düzenleme hakkında bilgi edinin.

> [!IMPORTANT]
> Power BI Çalışma Alanı Koleksiyonları kullanım dışı bırakılmıştır ve Haziran 2018'e kadar veya anlaşmanızda belirtilen süre boyunca kullanılabilecektir. Uygulamanızda kesinti yaşanmaması için Power BI Embedded'a geçirmeyi planlamanız önerilir. Verilerinizi Power BI Embedded'a nasıl taşıyacağınızı öğrenmek için bkz. [Power BI Çalışma Alanı Koleksiyonları'nı Power BI Embedded'a geçirme](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

## <a name="creating-an-access-token"></a>Bir erişim belirteci oluşturma

Hem görüntüleme hem de rapor düzenleme olanağı sunan bir erişim belirteci oluşturmanız gerekir. Düzenle ve bir raporu kaydetmek için ihtiyacınız **Report.ReadWrite** belirteci izni. Daha fazla bilgi için [kimlik doğrulama ve yetkilendirme Power BI çalışma alanı koleksiyonları'nda](app-token-flow.md).

> [!NOTE]
> Bu, düzenlemek ve değişiklikler için var olan bir raporu kaydetmek sağlar. Ayrıca destekleme işlevi isterseniz **Kaydet**, ek izinler sağlamanız gerekir. Daha fazla bilgi için [kapsamları](app-token-flow.md#scopes).

```csharp
using Microsoft.PowerBI.Security;

// rlsUsername and roles are optional
string scopes = "Report.ReadWrite";
PowerBIToken embedToken = PowerBIToken.CreateReportEmbedTokenForCreation(workspaceCollectionName, workspaceId, datasetId, null, null, scopes);

var token = embedToken.Generate("{access key}");
```

## <a name="embed-configuration"></a>Yapılandırma ekleme

İzinler ve bir viewMode kaydetme görmek için sağlamanız gereken düğmesi düzenleme modunda. Daha fazla bilgi için [yapılandırma ayrıntılarını ekleme](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Embed-Configuration-Details).

Örneğin, JavaScript içinde:

```html
   <div id="reportContainer"></div>

    <script>
    // Get models. Models, it contains enums that can be used.
    var models = window['powerbi-client'].models;

    // Embed configuration used to describe the what and how to embed.
    // This object is used when calling powerbi.embed.
    // This also includes settings and options such as filters.
    // You can find more information at https://github.com/Microsoft/PowerBI-JavaScript/wiki/Embed-Configuration-Details.
    var config= {
        type: 'report',
        accessToken: 'eyJ0eXAiO...Qron7qYpY9MI',
        embedUrl: 'https://embedded.powerbi.com/appTokenReportEmbed',
        id:  '5dac7a4a-4452-46b3-99f6-a25915e0fe55',
        permissions: models.Permissions.ReadWrite /*both save & save as buttons will be visible*/,
        viewMode: models.ViewMode.View,
        settings: {
            filterPaneEnabled: true,
            navContentPaneEnabled: true
        }
    };

    // Get a reference to the embedded report HTML element
    var reportContainer = $('#reportContainer')[0];

    // Embed the report and display it within the div container.
    var report = powerbi.embed(reportContainer, config);
    </script>
```

Bu görünüm modunda göre Raporu eklemek için gösterir **viewMode** ayarlamak **modelleri. ViewMode.View**.

## <a name="view-mode"></a>Görünüm modu

Görünüm moduna geçiş, açıksa düzenleme modu için aşağıdaki JavaScript kullanabilirsiniz.

```javascript
// Get a reference to the embedded report HTML element
var reportContainer = $('#reportContainer')[0];

// Get a reference to the embedded report.
report = powerbi.get(reportContainer);

// Switch to view mode.
report.switchMode("view");

```

## <a name="edit-mode"></a>Düzenleme modu

Görünüm modu değilseniz düzenleme moduna geçmek için aşağıdaki JavaScript kullanabilirsiniz.

```javascript
// Get a reference to the embedded report HTML element
var reportContainer = $('#reportContainer')[0];

// Get a reference to the embedded report.
report = powerbi.get(reportContainer);

// Switch to edit mode.
report.switchMode("edit");

```

## <a name="see-also"></a>Ayrıca bkz.

[Bir örnek ile kullanmaya başlama](get-started-sample.md)  
[Rapor ekleme](embed-report.md)  
[Power BI Çalışma Alanı Koleksiyonları ile kimlik doğrulaması ve yetkilendirme](app-token-flow.md)  
[CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN)  
[JavaScript Örnek Ekleme](https://microsoft.github.io/PowerBI-JavaScript/demo/)  
[Power BI-CSharp Git deposu](https://github.com/Microsoft/PowerBI-CSharp)  
[Power BI düğümlü Git deposu](https://github.com/Microsoft/PowerBI-Node)  

Başka sorunuz mu var? [Power BI Topluluğu'nu deneyin](https://community.powerbi.com/)
