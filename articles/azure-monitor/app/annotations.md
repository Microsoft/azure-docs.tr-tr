---
title: Application Insights için sürüm ek açıklamaları | Microsoft Docs
description: Application Insights içinde Ölçüm Gezgini grafiklerinize dağıtım veya yapı işaretçileri ekleyin.
ms.topic: conceptual
ms.date: 08/14/2020
ms.openlocfilehash: 9132e65e4705fd9125d97a5e095fe5f0850229a2
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "107011059"
---
# <a name="annotations-on-metric-charts-in-application-insights"></a>Application Insights 'de ölçüm grafiklerindeki ek açıklamalar

Ek açıklamalar, yeni bir derlemeyi veya diğer önemli olayları dağıttığınız yeri gösterir. Ek açıklamalar, değişikliklerinizin uygulamanızın performansı üzerinde herhangi bir etkiye sahip olup olmadığını görmenizi kolaylaştırır. [Azure Pipelines](/azure/devops/pipelines/tasks/) yapı sistemi tarafından otomatik olarak oluşturulabilir. Ayrıca, PowerShell 'den oluşturarak istediğiniz herhangi bir olayı işaretlemek için ek açıklamalar da oluşturabilirsiniz.

## <a name="release-annotations-with-azure-pipelines-build"></a>Azure Pipelines derlemesi olan sürüm ek açıklamaları

Sürüm ek açıklamaları, Azure DevOps 'un bulut tabanlı Azure Pipelines hizmetinin bir özelliğidir.

### <a name="install-the-annotations-extension-one-time"></a>Ek açıklama uzantısını (bir kez) yükler

Sürüm ek açıklamaları oluşturabilmek için Visual Studio Market sağlanan birçok Azure DevOps uzantısını yüklemeniz gerekir.

1. [Azure DevOps](https://azure.microsoft.com/services/devops/) projenizde oturum açın.
   
1. Visual Studio Market [Sürüm ek açıklaması uzantısı](https://marketplace.visualstudio.com/items/ms-appinsights.appinsightsreleaseannotations) sayfasında, Azure DevOps kuruluşunuzu seçin ve ardından uzantıyı Azure DevOps kuruluşunuza **eklemek için Ekle** ' yi seçin.
   
   ![Bir Azure DevOps organizasyonu seçin ve ardından Install ' ı seçin.](./media/annotations/1-install.png)
   
Yalnızca Azure DevOps kuruluşunuz için uzantıyı bir kez yüklemeniz gerekir. Artık kuruluşunuzdaki tüm projeler için sürüm ek açıklamalarını yapılandırabilirsiniz.

### <a name="configure-release-annotations"></a>Sürüm ek açıklamalarını Yapılandır

Azure Pipelines sürüm şablonlarınızın her biri için ayrı bir API anahtarı oluşturun.

1. [Azure Portal](https://portal.azure.com) oturum açın ve uygulamanızı izleyen Application Insights kaynağını açın. Ya da bir tane yoksa, [Yeni bir Application Insights kaynağı oluşturun](./app-insights-overview.md).
   
1. **API erişimi** sekmesini açın ve **Application Insights kimliği**' ni kopyalayın.
   
   ![API erişimi altında uygulama KIMLIĞINI kopyalayın.](./media/annotations/2-app-id.png)

1. Ayrı bir tarayıcı penceresinde, Azure Pipelines dağıtımlarınızı yöneten yayın şablonunu açın veya oluşturun.
   
1. **Görev Ekle**' yi seçin ve sonra menüden **Application Insights sürüm ek açıklaması** görevi ' ni seçin.
   
   ![Görev Ekle ' yi seçin ve sürüm ek açıklaması Application Insights seçin.](./media/annotations/3-add-task.png)

   > [!NOTE]
   > Sürüm ek açıklaması görevi şu anda yalnızca Windows tabanlı aracıları desteklemektedir; Linux, macOS veya diğer aracı türleri üzerinde çalışmaz.
   
1. **Uygulama kimliği**' nin altında, **API erişimi** sekmesinden kopyaladığınız Application Insights kimliğini yapıştırın.
   
   ![Application Insights KIMLIĞINI yapıştırın](./media/annotations/4-paste-app-id.png)
   
1. Application Insights **API erişimi** penceresine geri döndüğünüzde, **API anahtarı oluştur**' u seçin. 
   
   ![API erişimi sekmesinde, API anahtarı oluştur ' u seçin.](./media/annotations/5-create-api-key.png)
   
1. **API anahtarı oluştur** penceresinde bir açıklama yazın, **ek açıklamaları yaz**' ı seçin ve ardından **anahtar oluştur**' u seçin. Yeni anahtarı kopyalayın.
   
   ![API anahtarı Oluştur penceresinde bir açıklama yazın, ek açıklamaları yaz ' ı seçin ve ardından anahtar oluştur ' u seçin.](./media/annotations/6-create-api-key.png)
   
1. Yayın Şablonu penceresinde, **değişkenler** sekmesinde, yeni API anahtarı için bir değişken tanımı oluşturmak üzere **Ekle** ' yi seçin.

1. **Ad**' ın altında, değer ' in altında, `ApiKey` **API erişimi** sekmesinden kopyaladığınız API anahtarını yapıştırın. 
   
   ![Azure DevOps değişkenleri sekmesinde Ekle ' yi seçin, ApiKey değişkenini adlandırın ve değer altına API anahtarını yapıştırın.](./media/annotations/7-paste-api-key.png)
   
1. Şablonu kaydetmek için ana yayın şablonu penceresinde **Kaydet** ' i seçin.


   > [!NOTE]
   > API anahtarları için sınırlar [REST API hız limitleri belgelerinde](https://dev.applicationinsights.io/documentation/Authorization/Rate-limits)açıklanmıştır.

## <a name="view-annotations"></a>Ek açıklamaları görüntüle


   > [!NOTE]
   > Sürüm ek açıklamaları Şu anda Application Insights ölçüm bölmesinde kullanılamıyor

Şimdi yeni bir sürüm dağıtmak için yayın şablonunu kullandığınızda Application Insights için bir ek açıklama gönderilir. Ek açıklamalar aşağıdaki konumlarda görüntülenebilir:

Sürüm ek açıklamalarını el ile oluşturma becerisinin bulunduğu **kullanım** bölmesi:

![Bir dönem boyunca görünen Kullanıcı ziyaretlerinin sayısına sahip çubuk grafiğinin ekran görüntüsü. Yayın ek açıklamaları, grafiğin üzerinde, bir yayının gerçekleştiği andaki süreyi belirten yeşil onay işaretleri olarak görünür](./media/annotations/usage-pane.png)

Görselleştirmenin x ekseni üzerinde saat gösterdiği herhangi bir günlük tabanlı çalışma kitabı sorgusunda.

![Ek açıklamaların gösterildiği zaman serisi günlük tabanlı sorguyla çalışma kitabı bölmesinin ekran görüntüsü](./media/annotations/workbooks-annotations.png)

Çalışma kitabınızda ek açıklamaların etkinleştirilmesi için **Gelişmiş ayarlar** ' a gidin ve **ek açıklamaları göster**' i seçin.

![Sözcüklerden oluşan Gelişmiş Ayarlar menüsünün ekran görüntüsü, ek açıklamaları göster seçeneğinin yanındaki onay işaretiyle vurgulanır.](./media/annotations/workbook-show-annotations.png)

İstek sahibi, kaynak denetimi dalı, yayın işlem hattı ve ortam dahil olmak üzere sürüm hakkındaki ayrıntıları açmak için herhangi bir ek açıklama işaretleyicisi seçin.

## <a name="create-custom-annotations-from-powershell"></a>PowerShell 'den özel ek açıklamalar oluşturma
Azure DevOps kullanmadan istediğiniz herhangi bir işlemden ek açıklama oluşturmak için CreateReleaseAnnotation PowerShell betiğini kullanabilirsiniz.

> [!IMPORTANT]
> PowerShell 7,1 kullanıyorsanız, `-SkipHttpErrorCheck` 26. satırın sonuna ekleyin. Örneğin: `$request = Invoke-WebRequest -Uri $fwLink -MaximumRedirection 0 -UseBasicParsing -ErrorAction Ignore -SkipHttpErrorCheck`.

1. CreateReleaseAnnotation.ps1 yerel bir kopyasını oluşturun:

    ```powershell
    
    # Copyright (c) Microsoft Corporation. All rights reserved. 
    # Licensed under the MIT License. See License.txt in the project root for license information. 
    
    # Sample usage .\CreateReleaseAnnotation.ps1 -applicationId "<appId>" -apiKey "<apiKey>" -releaseFilePath "<path to .exe with file version>" -releaseProperties @{"ReleaseDescription"="Release with annotation";"TriggerBy"="John Doe"}
    param(
        [parameter(Mandatory = $true)][string]$applicationId,
        [parameter(Mandatory = $true)][string]$apiKey,
        [parameter(Mandatory = $true)][string]$releaseFilePath,
        [parameter(Mandatory = $false)]$releaseProperties
    )
    
    $releaseName = (Get-Item $releaseFilePath).VersionInfo.FileVersion
    Write-Host "Creating release annotation $releaseName in ApplicationInsights" -ForegroundColor Cyan
    
    # background info on how fwlink works: After you submit a web request, many sites redirect through a series of intermediate pages before you finally land on the destination page.
    # So when calling Invoke-WebRequest, the result it returns comes from the final page in any redirect sequence. Hence, I set MaximumRedirection to 0, as this prevents the call to 
    # be redirected. By doing this, we get a response with status code 302, which indicates that there is a redirection link from the response body. We grab this redirection link and 
    # construct the url to make a release annotation.
    # Here's how this logic is going to works
    # 1. Client send http request, such as:  http://go.microsoft.com/fwlink/?LinkId=625115
    # 2. FWLink get the request and find out the destination URL for it, such as:  http://www.bing.com
    # 3. FWLink generate a new http response with status code “302” and with destination URL “http://www.bing.com”. Send it back to Client.
    # 4. Client, such as a powershell script, knows that status code “302” means redirection to new a location, and the target location is “http://www.bing.com”
    function GetRequestUrlFromFwLink($fwLink)
    {
        $request = Invoke-WebRequest -Uri $fwLink -MaximumRedirection 0 -UseBasicParsing -ErrorAction Ignore
        if ($request.StatusCode -eq "302") {
            return $request.Headers.Location
        }
        
        return $null
    }
    
    function CreateAnnotation($grpEnv)
    {
        $retries = 1
        $success = $false
        while (!$success -and $retries -lt 6) {
            $location = "$grpEnv/applications/$applicationId/Annotations?api-version=2015-11"
                
            Write-Host "Invoke a web request for $location to create a new release annotation. Attempting $retries"
            set-variable -Name createResultStatus -Force -Scope Local -Value $null
            set-variable -Name createResultStatusDescription -Force -Scope Local -Value $null
            set-variable -Name result -Force -Scope Local
    
            try {
                $result = Invoke-WebRequest -Uri $location -Method Put -Body $bodyJson -Headers $headers -ContentType "application/json; charset=utf-8" -UseBasicParsing
            } catch {
                if ($_.Exception){
                    if($_.Exception.Response) {
                        $createResultStatus = $_.Exception.Response.StatusCode.value__
                        $createResultStatusDescription = $_.Exception.Response.StatusDescription
                    }
                    else {
                        $createResultStatus = "Exception"
                        $createResultStatusDescription = $_.Exception.Message
                    }
                }
            }
    
            if ($result -eq $null) {
                if ($createResultStatus -eq $null) {
                    $createResultStatus = "Unknown"
                }
                if ($createResultStatusDescription -eq $null) {
                    $createResultStatusDescription = "Unknown"
                }
            }
            else {
                    $success = $true                     
            }
    
            if ($createResultStatus -eq 409 -or $createResultStatus -eq 404 -or $createResultStatus -eq 401) # no retry when conflict or unauthorized or not found
            {
                break
            }
    
            $retries = $retries + 1
            sleep 1
        }
    
        $createResultStatus
        $createResultStatusDescription
        return
    }
    
    # Need powershell version 3 or greater for script to run
    $minimumPowershellMajorVersion = 3
    if ($PSVersionTable.PSVersion.Major -lt $minimumPowershellMajorVersion) {
       Write-Host "Need powershell version $minimumPowershellMajorVersion or greater to create release annotation"
       return
    }
    
    $currentTime = (Get-Date).ToUniversalTime()
    $annotationDate = $currentTime.ToString("MMddyyyy_HHmmss")
    set-variable -Name requestBody -Force -Scope Script
    $requestBody = @{}
    $requestBody.Id = [GUID]::NewGuid()
    $requestBody.AnnotationName = $releaseName
    $requestBody.EventTime = $currentTime.GetDateTimeFormats("s")[0] # GetDateTimeFormats returns an array
    $requestBody.Category = "Deployment"
    
    if ($releaseProperties -eq $null) {
        $properties = @{}
    } else {
        $properties = $releaseProperties    
    }
    $properties.Add("ReleaseName", $releaseName)
    
    $requestBody.Properties = ConvertTo-Json($properties) -Compress
    
    $bodyJson = [System.Text.Encoding]::UTF8.GetBytes(($requestBody | ConvertTo-Json))
    $headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
    $headers.Add("X-AIAPIKEY", $apiKey)
    
    set-variable -Name createAnnotationResult1 -Force -Scope Local -Value $null
    set-variable -Name createAnnotationResultDescription -Force -Scope Local -Value ""
    
    # get redirect link from fwlink
    $requestUrl = GetRequestUrlFromFwLink("http://go.microsoft.com/fwlink/?prd=11901&pver=1.0&sbp=Application%20Insights&plcid=0x409&clcid=0x409&ar=Annotations&sar=Create%20Annotation")
    if ($requestUrl -eq $null) {
        $output = "Failed to find the redirect link to create a release annotation"
        throw $output
    }
    
    $createAnnotationResult1, $createAnnotationResultDescription = CreateAnnotation($requestUrl)
    if ($createAnnotationResult1) 
    {
         $output = "Failed to create an annotation with Id: {0}. Error {1}, Description: {2}." -f $requestBody.Id, $createAnnotationResult1, $createAnnotationResultDescription
         throw $output
    }
    
    $str = "Release annotation created. Id: {0}." -f $requestBody.Id
    Write-Host $str -ForegroundColor Green
    
    ```
   
1. Application Insights KIMLIĞINIZI almak ve Application Insights **API erişimi** sekmesinizden bir API anahtarı oluşturmak için yukarıdaki yordamdaki adımları kullanın.
   
1. Aşağıdaki kodla PowerShell betiğini çağırın ve açılı ayraçlı yer tutucuları değerlerinizle değiştirin. `-releaseProperties`İsteğe bağlıdır. 
   
   ```powershell
   
        .\CreateReleaseAnnotation.ps1 `
         -applicationId "<applicationId>" `
         -apiKey "<apiKey>" `
         -releaseName "<releaseName>" `
         -releaseProperties @{
             "ReleaseDescription"="<a description>";
             "TriggerBy"="<Your name>" }
   ```

Örneğin, geçmiş için ek açıklamalar oluşturmak üzere betiği değiştirebilirsiniz.


## <a name="next-steps"></a>Sonraki adımlar

* [İş öğeleri Oluştur](./diagnostic-search.md#create-work-item)
* [PowerShell ile Automation](./powershell.md)
