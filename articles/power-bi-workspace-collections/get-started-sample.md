---
title: Bir örnek ile kullanmaya başlama
description: Bu makalede, sizi, Power BI çalışma alanı koleksiyonları get başlatılan örneğe tanıtacağız.
services: power-bi-workspace-collections
ms.service: power-bi-embedded
author: rkarlin
ms.author: rkarlin
ms.topic: article
ms.workload: powerbi
ms.date: 09/25/2017
ms.openlocfilehash: 9b45dd6bcc9aa3abf2d5078b54e51531cae4ec5f
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67669112"
---
# <a name="get-started-with-power-bi-workspace-collections-sample"></a>Power BI çalışma alanı koleksiyonları örnek ile kullanmaya başlama

İle **Microsoft Power BI çalışma alanı koleksiyonları**, Power BI raporlarını doğrudan, web veya mobil uygulamalarınızla tümleştirebilirsiniz. Bu makalede, biz size tanıtan **Power BI çalışma alanı koleksiyonları** get kullanmaya başlama örnek.

> [!IMPORTANT]
> Power BI Çalışma Alanı Koleksiyonları kullanım dışı bırakılmıştır ve Haziran 2018'e kadar veya anlaşmanızda belirtilen süre boyunca kullanılabilecektir. Uygulamanızda kesinti yaşanmaması için Power BI Embedded'a geçirmeyi planlamanız önerilir. Verilerinizi Power BI Embedded'a nasıl taşıyacağınızı öğrenmek için bkz. [Power BI Çalışma Alanı Koleksiyonları'nı Power BI Embedded'a geçirme](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

Aşağıdaki kaynaklar kaydetmek istediğiniz tüm daha fazla ayrıntıya önce: Bunlar, örnek uygulama ve kendi uygulamalarınızı halinde Power BI raporlarını çok tümleştirmesi yardımcı.

* [Örnek çalışma web uygulaması](https://go.microsoft.com/fwlink/?LinkId=761493)
* [Power BI çalışma alanı koleksiyonları API Başvurusu](https://msdn.microsoft.com/library/azure/mt711507.aspx)
* [Power BI .NET SDK'sını](https://go.microsoft.com/fwlink/?LinkId=746472) (NuGet aracılığıyla kullanılabilir)
* [JavaScript rapor ekleme örneği](https://microsoft.github.io/PowerBI-JavaScript/demo)

> [!NOTE]
> Yapılandırabilirsiniz ve Power BI çalışma alanı koleksiyonları Al çalıştırma başlatıldı örnek önce en az bir tane oluşturmak ihtiyacınız **çalışma alanı koleksiyonu** Azure aboneliğinizdeki. Nasıl oluşturulacağını öğrenmek için bir **çalışma alanı koleksiyonu** Azure portalında [Power BI çalışma alanı koleksiyonları ile çalışmaya başlama](get-started.md).

## <a name="configure-the-sample-app"></a>Örnek uygulamayı yapılandırma

Şimdi Visual Studio geliştirme ortamınızı ayarlama örnek uygulama çalıştırmak için gerekli bileşenleri erişmek için nasıl ayarlanacağı gösterilmektedir.

1. İndirip sıkıştırmasını [Power BI çalışma alanı koleksiyonları - bir raporu web uygulamasıyla tümleştirmek](https://go.microsoft.com/fwlink/?LinkId=761493) GitHub üzerinde örnek.
2. Açık **Powerbı embedded.sln** Visual Studio'da. Yürütme gerekebilir **Update-Package** bu çözümde kullanılan paketler güncelleştirmek için NuGet Paket Yöneticisi konsolunda komutu.
3. Çözümü oluşturun.
4. Çalıştırma **ProvisionSample** konsol uygulaması. Örnek konsol uygulamasında, bir çalışma alanı sağlama ve PBIX dosyasını içeri aktarın.
5. Yeni bir sağlama **çalışma**, 1 seçeneğini belirleyin **koleksiyonu Yönetimi**ve seçeneği 6'da, ardından **yeni bir çalışma alanı sağlanamadı**
6. Yeni bir içeri aktarmak için **rapor**, seçin, 2. seçenek **rapor Yönetim**seçip 3 seçeneği **PBIX Desktop içeri aktarma dosyası bir çalışma alanına**.

7. Girin, **çalışma alanı koleksiyonu** adı ve **erişim anahtarı**. Bunlar alabileceğiniz **Azure portalında**. Nasıl alınacağı hakkında daha fazla bilgi için **erişim anahtarı**, bkz: [Power BI API'si erişim anahtarlarını görüntüle](get-started.md#view-power-bi-api-access-keys) , Microsoft Power BI Embedded ile çalışmaya başlama.

    ![Azure portalı içindeki erişim anahtarları](media/get-started-sample/azure-portal.png)
8. Kopyalayıp yeni oluşturulan kaydedin **çalışma alanı kimliği** bu makalenin sonraki bölümlerinde kullanılacak. Sonra **çalışma alanı kimliği** olan oluşturuldu, bunu bulabilirsiniz **Azure portalında**.

    ![Azure portalındaki çalışma alanı kimliği](media/get-started-sample/workspace-id.png)
9. Bir PBIX dosyasına aktarmak için **çalışma**, seçenek belirleyin **6. Mevcut bir çalışma Import PBIX Desktop dosyasına**. Bir PBIX kullanışlı dosya yoksa, indirebileceğiniz [Retail Analysis Sample PBIX](https://go.microsoft.com/fwlink/?LinkID=780547).
10. İstenirse, için kolay bir ad girin, **veri kümesi**.

Benzer bir yanıt görmeniz gerekir:

```
Checking import state... Publishing
Checking import state... Succeeded
```

> [!NOTE]
> PBIX dosyanızı doğrudan sorgu herhangi bir bağlantı içeriyorsa, bağlantı dizelerini güncelleştirmek üzere seçeneği 7'yi çalıştırın.

Bu noktada, içeri aktarılan bir Power BI PBIX rapor sahip, **çalışma**. Şimdi ne çalıştırılacak bakalım **Power BI çalışma alanı koleksiyonları** kullanmaya başlama örnek web uygulamasını edinin.

## <a name="run-the-sample-web-app"></a>Örnek web uygulamasını çalıştırma

Web uygulaması örneği içeri aktarılan raporlar işleyen bir örnek uygulamadır, **çalışma**. Web uygulaması örneği yapılandırma aşağıda verilmiştir.

1. İçinde **Power BI embedded** Visual Studio çözümünü sağ **EmbedSample** seçin ve web uygulaması **başlangıç projesi olarak ayarla**.
2. İçinde **web.config**, **EmbedSample** web uygulaması, Düzen **appSettings**: **AccessKey**, **WorkspaceCollection** adı ve **Workspaceıd**.

    ```xml
    <appSettings>
        <add key="powerbi:AccessKey" value="" />
        <add key="powerbi:ApiUrl" value="https://api.powerbi.com" />
        <add key="powerbi:WorkspaceCollection" value="" />
        <add key="powerbi:WorkspaceId" value="" />
    </appSettings>
    ```
3. Çalıştırma **EmbedSample** web uygulaması.

Bir kez çalıştırdığınız **EmbedSample** web uygulaması, sol gezinti bölmesinin içermelidir bir **raporları** menüsü. İçeri aktardığınız rapor görüntülemek için genişletin **raporları**ve bir raporu tıklatın. İçeri aktardığınız varsa [Retail Analysis Sample PBIX](https://go.microsoft.com/fwlink/?LinkID=780547), örnek web uygulamasını şu şekilde görünür:

![Örnek uygulama içinde örnek sol gezinti](media/get-started-sample/sample-left-nav.png)

Bir rapor tıkladıktan sonra **EmbedSample** web uygulaması görünmelidir bir şey:

![Uygulama içinde görüntüleyen örnek rapor](media/get-started-sample/sample-web-app.png)

## <a name="explore-the-sample-code"></a>Örnek kodu inceleyin

**Microsoft Power BI çalışma alanı koleksiyonları** örnektir nasıl tümleştireceğinizi gösteren bir örnek web uygulaması **Power BI** uygulamanıza raporlar. En iyi yöntemleri göstermek için bir Model-View-Controller (MVC) tasarım deseni kullanır. Bu bölüm içinde keşfedebilirsiniz örnek kod bölümlerini vurgular **Power BI embedded** web uygulaması çözümü. Model-View-Controller (MVC) deseni, etki alanı, sunu ve kullanıcı girişi üç ayrı sınıf uygulamasına göre eylemleri modelleme ayırır: Model, Görünüm ve denetimi. MVC hakkında daha fazla bilgi için bkz: [ASP.NET hakkında bilgi edinin](https://www.asp.net/mvc).

**Microsoft Power BI çalışma alanı koleksiyonları** örnek kod gibi ayrılmış. Örnek kod kolayca bulabilmesi için her bölüm embedded.sln Powerbı çözüm dosya adını içerir.

> [!NOTE]
> Bu bölümde, kodu nasıl yazılmıştır gösteren örnek kodu bir özetidir. Tam bir örnek görüntülemek için lütfen Power BI embedded.sln çözümünü Visual Studio'da yükleyin.

### <a name="model"></a>Model

Örnek sahip bir **ReportsViewModel** ve **ReportViewModel**.

**ReportsViewModel.cs**: Power BI raporları temsil eder.

```csharp
public class ReportsViewModel
{
    public List<Report> Reports { get; set; }
}
```

**ReportViewModel.cs**: Power BI raporuna temsil eder.

```csharp
public class ReportViewModel
{
    public IReport Report { get; set; }

    public string AccessToken { get; set; }
}
```

### <a name="connection-string"></a>Bağlantı dizesi

Bağlantı dizesi şu biçimde olmalıdır:

```
Data Source=tcp:MyServer.database.windows.net,1433;Initial Catalog=MyDatabase
```

Genel sunucu ve veritabanı kullanarak başarısız öznitelikler. Örneğin: Server=tcp:MyServer.database.windows.net,1433;Database=MyDatabase,

### <a name="view"></a>Görünüm

**Görünümü** Power BI gösterimini yöneten **raporları** ve Power BI **rapor**.

**Reports.cshtml**: Üzerinden yineleme yapma **Model.Reports** oluşturmak için bir **ActionLink**. **ActionLink** şu şekilde oluşur:

| Bölümü | Açıklama |
| --- | --- |
| Başlık |Raporun adı. |
| QueryString |Bir bağlantı Raporu Kimliği |
```cshtml
<div id="reports-nav" class="panel-collapse collapse">
    <div class="panel-body">
        <ul class="nav navbar-nav">
            @foreach (var report in Model.Reports)
            {
                var reportClass = Request.QueryString["reportId"] == report.Id ? "active" : "";
                <li class="@reportClass">
                    @Html.ActionLink(report.Name, "Report", new { reportId = report.Id })
                </li>
            }
        </ul>
    </div>
</div>
```
Report.cshtml: Ayarlama **Model.AccessToken**ve Lambda ifadesi **PowerBIReportFor**.

```cshtml
@model ReportViewModel

...

<div class="side-body padding-top">
    @Html.PowerBIAccessToken(Model.AccessToken)
    @Html.PowerBIReportFor(m => m.Report, new { style = "height:85vh" })
</div>
```

### <a name="controller"></a>Denetleyici

**DashboardController.cs**: PowerBIClient geçirme oluşturur bir **uygulama belirteci**. Bir JSON Web Token (JWT) oluşturulduğu **imzalama anahtarı** almak için **kimlik bilgilerini**. **Kimlik bilgilerini** bir örneğini oluşturmak için kullanılan **PowerBIClient**. Örneğini oluşturduktan sonra **PowerBIClient**, GetReports() ve GetReportsAsync() çağırabilirsiniz.


CreatePowerBIClient()

```csharp
private IPowerBIClient CreatePowerBIClient()
{
    var credentials = new TokenCredentials(accessKey, "AppKey");
    var client = new PowerBIClient(credentials)
    {
        BaseUri = new Uri(apiUrl)
    };

    return client;
}
```

ActionResult Reports()

```csharp
public ActionResult Reports()
{
    using (var client = this.CreatePowerBIClient())
    {
        var reportsResponse = client.Reports.GetReports(this.workspaceCollection, this.workspaceId);

        var viewModel = new ReportsViewModel
        {
            Reports = reportsResponse.Value.ToList()
        };

        return PartialView(viewModel);
    }
}
```

Görev\<actionresult öğesini > raporu (dize Reportıd)

```csharp
public async Task<ActionResult> Report(string reportId)
{
    using (var client = this.CreatePowerBIClient())
    {
        var reportsResponse = await client.Reports.GetReportsAsync(this.workspaceCollection, this.workspaceId);
        var report = reportsResponse.Value.FirstOrDefault(r => r.Id == reportId);
        var embedToken = PowerBIToken.CreateReportEmbedToken(this.workspaceCollection, this.workspaceId, report.Id);

        var viewModel = new ReportViewModel
        {
            Report = report,
            AccessToken = embedToken.Generate(this.accessKey)
        };

        return View(viewModel);
    }
}
```

### <a name="integrate-a-report-into-your-app"></a>Bir raporu uygulamanızla tümleştirmek

Sonra bir **rapor**, kullandığınız bir **IFrame** Power BI katıştırmak için **rapor**. Bir kod parçacığı içinde powerbi.js gelen işte **Microsoft Power BI çalışma alanı koleksiyonları** örnek.

```javascript
init: function() {
    var embedUrl = this.getEmbedUrl();
    var iframeHtml = '<iframe style="width:100%;height:100%;" src="' + embedUrl + 
        '" scrolling="no" allowfullscreen="true"></iframe>';
    this.element.innerHTML = iframeHtml;
    this.iframe = this.element.childNodes[0];
    this.iframe.addEventListener('load', this.load.bind(this), false);
}
```

## <a name="filter-reports-embedded-in-your-application"></a>Uygulamanıza filtre raporları

Bir URL söz dizimini kullanarak eklenmiş bir raporu filtreleyebilirsiniz. Bunu yapmak için eklediğiniz bir **$filter** sorgu dizesi parametresi ile bir **eq** iFrame src URL'nizle belirtilen filtre işleci. Filtre sorgu söz dizimi şu şekildedir:

```
https://app.powerbi.com/reportEmbed
?reportId=d2a0ea38-...-9673-ee9655d54a4a&
$filter={tableName/fieldName}%20eq%20'{fieldValue}'
```

> [!NOTE]
> {tableName/fieldName} boşluk ya da özel karakter içeremez. {fieldValue} tek bir kategorik değer kabul eder.  

## <a name="see-also"></a>Ayrıca bkz.

[Microsoft Power BI çalışma alanı koleksiyonu senaryoları](scenarios.md)  
[Power BI Çalışma Alanı Koleksiyonları ile kimlik doğrulaması ve yetkilendirme](app-token-flow.md)  
[Rapor ekleme](embed-report.md)  
[Veri kümesinden yeni rapor oluşturma](create-report-from-dataset.md)  
[Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)  
[JavaScript Örnek Ekleme](https://microsoft.github.io/PowerBI-JavaScript/demo/)  

Başka sorunuz mu var? [Power BI Topluluğu'nu deneyin](https://community.powerbi.com/)
