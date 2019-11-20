---
title: Power BI çalışma alanı koleksiyonları'nda raporları kaydetme | Microsoft Docs
description: Power BI çalışma alanı koleksiyonları içinde raporları kaydetme hakkında bilgi edinin. Bu, başarılı bir şekilde çalışması için uygun izinleri gerektirir.
services: power-bi-workspace-collections
ms.service: power-bi-embedded
author: rkarlin
ms.author: rkarlin
ms.topic: article
ms.workload: powerbi
ms.date: 09/20/2017
ms.openlocfilehash: 1e23bd22f2b89ef42f4b27f5e4ca2590be7fe5a7
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67668893"
---
# <a name="save-reports-in-power-bi-workspace-collections"></a>Power BI çalışma alanı koleksiyonları'nda raporları kaydetme

Power BI çalışma alanı koleksiyonları içinde raporları kaydetme hakkında bilgi edinin. Raporları kaydetme başarıyla çalışması için uygun izinleri gerektirir.

> [!IMPORTANT]
> Power BI Çalışma Alanı Koleksiyonları kullanım dışı bırakılmıştır ve Haziran 2018'e kadar veya anlaşmanızda belirtilen süre boyunca kullanılabilecektir. Uygulamanızda kesinti yaşanmaması için Power BI Embedded'a geçirmeyi planlamanız önerilir. Verilerinizi Power BI Embedded'a nasıl taşıyacağınızı öğrenmek için bkz. [Power BI Çalışma Alanı Koleksiyonları'nı Power BI Embedded'a geçirme](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

Power BI çalışma alanı koleksiyonları içinde varolan raporları düzenleyin ve kaydedin. Ayrıca, yeni bir rapor oluşturmak ve oluşturmak için yeni bir rapor olarak kaydedin.

Bir raporu kaydetmek için önce belirli bir rapor için bir belirteç doğru kapsamlar ile oluşturmanız gerekir:

* Report.ReadWrite etkinleştirmek için kapsam gereklidir
* Kaydetme etkinleştirmek için Report.Read ve Workspace.Report.Copy kapsamlar gereklidir
* Kaydet ve kaydetme Report.ReadWrite ve Workspace.Report.Copy gerektiğinden etkinleştirmek için

Sırasıyla sağ etkinleştirmek için rapor eklediğinizde ekleme yapılandırması doğru izin sağlamak için ihtiyacınız olan dosya menüsü düğmeleri olarak kaydetme/Kaydet:

* modeller. Permissions.ReadWrite
* modeller. Permissions.Copy
* modeller. Permissions.All

> [!NOTE]
> Erişim belirtecinizi uygun kapsamları da gerekir. Daha fazla bilgi için [kapsamları](app-token-flow.md#scopes).

## <a name="embed-report-in-edit-mode"></a>Rapor düzenleme modunda ekleme

Bu nedenle yalnızca doğru özellikleri ekleme yapılandırmasında geçirin ve powerbi.embed() çağırmak için uygulamanızın içinde düzenleme modunda bir rapor eklemek istediğiniz varsayalım. İzinler ve kaydetme bakın ve düzenleme modundayken düğmeleri olarak kaydetmek için bir viewMode sağlayın. Daha fazla bilgi için [yapılandırma ayrıntılarını ekleme](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Embed-Configuration-Details).

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
        permissions: models.Permissions.All /*both save & save as buttons will be visible*/,
        viewMode: models.ViewMode.Edit,
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

Artık bir raporu düzenleme modunda uygulamanıza eklenir.

## <a name="save-report"></a>Raporu Kaydet

Rapor düzenleme modunda izinleri ve doğru belirteci ile ekleme sonra raporu Dosya menüsünden veya javascript kaydedebilirsiniz:

```javascript
 // Get a reference to the embedded report.
    report = powerbi.get(reportContainer);

 // Save report
    report.save();
```

## <a name="save-as"></a>Farklı kaydet

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
> Yalnızca sonra *Kaydet* oluşturulan yeni bir rapor. Kaydetme işleminden sonra tuval hala eski rapor düzenleme modu ve yeni rapor göstermez. Oluşturulan yeni raporu ekleyin. Yeni rapor ekleme, rapor oluşturuldukça yeni bir erişim belirteci gerektirir.

Ardından sonra yeni rapor gerekir bir *Kaydet*. Yeni rapor yükleme, herhangi bir raporu katıştırma için benzerdir.

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

## <a name="see-also"></a>Ayrıca bkz.

[Bir örnek ile kullanmaya başlama](get-started-sample.md)  
[Rapor ekleme](embed-report.md)  
[Veri kümesinden yeni rapor oluşturma](create-report-from-dataset.md)  
[Power BI Çalışma Alanı Koleksiyonları ile kimlik doğrulaması ve yetkilendirme](app-token-flow.md)  
[Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)  
[JavaScript Örnek Ekleme](https://microsoft.github.io/PowerBI-JavaScript/demo/)  

Başka sorunuz mu var? [Power BI Topluluğu'nu deneyin](https://community.powerbi.com/)

