---
title: Program aracılığıyla Azure Stream Analytics işlerini izleme ve yönetme
description: Bu makalede REST API 'Leri, Azure SDK veya PowerShell aracılığıyla oluşturulan Stream Analytics işlerin programlı bir şekilde nasıl izleneceği açıklanır.
author: jseb225
ms.author: jeanb
ms.service: stream-analytics
ms.topic: how-to
ms.date: 04/20/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: dda1b6b652870440e7228e6640bb555f4c2e4a63
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98015085"
---
# <a name="programmatically-create-a-stream-analytics-job-monitor"></a>Program aracılığıyla Stream Analytics iş İzleyicisi oluşturma

Bu makalede bir Stream Analytics işi için izlemenin nasıl etkinleştirileceği gösterilmektedir. REST API 'Leri, Azure SDK veya PowerShell aracılığıyla oluşturulan Stream Analytics işlerin varsayılan olarak etkin izleme özelliği yoktur. İşin Izleyici sayfasına gidip Etkinleştir düğmesine tıklayarak ve bu makaledeki adımları izleyerek bu işlemi otomatikleştirebileceğiniz Azure portal el ile etkinleştirebilirsiniz. İzleme verileri, Stream Analytics işiniz için Azure portal ölçüm alanında görünür.

## <a name="prerequisites"></a>Önkoşullar

Bu işleme başlamadan önce, aşağıdaki önkoşullara sahip olmanız gerekir:

* Visual Studio 2019 veya 2015
* [Azure .NET SDK](https://azure.microsoft.com/downloads/) indirildi ve yüklendi
* İzlemenin etkinleştirilmesi gereken mevcut bir Stream Analytics işi

## <a name="create-a-project"></a>Proje oluşturma

1. Visual Studio C# .NET konsol uygulaması oluşturun.
2. Paket Yöneticisi konsolunda, NuGet paketlerini yüklemek için aşağıdaki komutları çalıştırın. Birincisi Azure Stream Analytics Management .NET SDK 'dır. İkinci bir tane, izlemeyi etkinleştirmek için kullanılacak Azure Izleyici SDK 'sına sahiptir. Son bir kimlik doğrulaması için kullanılacak Azure Active Directory istemcsahiptir.
   
   ```powershell
   Install-Package Microsoft.Azure.Management.StreamAnalytics
   Install-Package Microsoft.Azure.Insights -Pre
   Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
   ```
3. Aşağıdaki appSettings bölümünü App.config dosyasına ekleyin.
   
   ```csharp
   <appSettings>
     <!--CSM Prod related values-->
     <add key="ResourceGroupName" value="RESOURCE GROUP NAME" />
     <add key="JobName" value="YOUR JOB NAME" />
     <add key="StorageAccountName" value="YOUR STORAGE ACCOUNT"/>
     <add key="ActiveDirectoryEndpoint" value="https://login.microsoftonline.com/" />
     <add key="ResourceManagerEndpoint" value="https://management.azure.com/" />
     <add key="WindowsManagementUri" value="https://management.core.windows.net/" />
     <add key="AsaClientId" value="1950a258-227b-4e31-a9cf-717495945fc2" />
     <add key="RedirectUri" value="urn:ietf:wg:oauth:2.0:oob" />
     <add key="SubscriptionId" value="YOUR AZURE SUBSCRIPTION ID" />
     <add key="ActiveDirectoryTenantId" value="YOUR TENANT ID" />
   </appSettings>
   ```
   *SubscriptionID* ve *activedirectorytenantıd* değerlerini Azure aboneliğiniz ve kiracı kimlikleri ile değiştirin. Aşağıdaki PowerShell cmdlet 'ini çalıştırarak bu değerleri alabilirsiniz:
   
   ```powershell
   Get-AzureAccount
   ```
4. Aşağıdaki using bildirimlerini projedeki kaynak dosyasına (Program.cs) ekleyin.
   
   ```csharp
     using System;
     using System.Configuration;
     using System.Threading;
     using Microsoft.Azure;
     using Microsoft.Azure.Management.Insights;
     using Microsoft.Azure.Management.Insights.Models;
     using Microsoft.Azure.Management.StreamAnalytics;
     using Microsoft.Azure.Management.StreamAnalytics.Models;
     using Microsoft.IdentityModel.Clients.ActiveDirectory;
   ```
5. Kimlik doğrulama Yardımcısı yöntemi ekleyin.

   ```csharp   
   public static string GetAuthorizationHeader()
   {
      AuthenticationResult result = null;
      var thread = new Thread(() =>
      {
         try
         {
             var context = new AuthenticationContext(
                ConfigurationManager.AppSettings["ActiveDirectoryEndpoint"] +
                ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);
             result = context.AcquireToken(
                 resource: ConfigurationManager.AppSettings["WindowsManagementUri"],
                 clientId: ConfigurationManager.AppSettings["AsaClientId"],
                 redirectUri: new Uri(ConfigurationManager.AppSettings["RedirectUri"]),
                 promptBehavior: PromptBehavior.Always);
         }
         catch (Exception threadEx)
         {
             Console.WriteLine(threadEx.Message);
         }
     });

     thread.SetApartmentState(ApartmentState.STA);
     thread.Name = "AcquireTokenThread";
     thread.Start();
     thread.Join();
   
     if (result != null)
     {
         return result.AccessToken;
     }
         throw new InvalidOperationException("Failed to acquire token");
   }
   ```

## <a name="create-management-clients"></a>Yönetim istemcileri oluşturma

Aşağıdaki kod, gerekli değişkenleri ve yönetim istemcilerini ayarlar.

   ```csharp
    string resourceGroupName = "<YOUR AZURE RESOURCE GROUP NAME>";
    string streamAnalyticsJobName = "<YOUR STREAM ANALYTICS JOB NAME>";

    // Get authentication token
    TokenCloudCredentials aadTokenCredentials =
        new TokenCloudCredentials(
            ConfigurationManager.AppSettings["SubscriptionId"],
            GetAuthorizationHeader());

    Uri resourceManagerUri = new
    Uri(ConfigurationManager.AppSettings["ResourceManagerEndpoint"]);

    // Create Stream Analytics and Insights management client
    StreamAnalyticsManagementClient streamAnalyticsClient = new
    StreamAnalyticsManagementClient(aadTokenCredentials, resourceManagerUri);
    InsightsManagementClient insightsClient = new
    InsightsManagementClient(aadTokenCredentials, resourceManagerUri);
   ```

## <a name="enable-monitoring-for-an-existing-stream-analytics-job"></a>Mevcut bir Stream Analytics işi için izlemeyi etkinleştir

Aşağıdaki kod, **mevcut** bir Stream Analytics işi için izlemeyi mümkün bir şekilde sunar. Kodun ilk bölümü, belirli bir Stream Analytics iş hakkında bilgi almak için Stream Analytics hizmetine karşı bir GET isteği gerçekleştirir. Stream Analytics işi için izlemeyi etkinleştirmek üzere öngörülere bir PUT isteği gönderen ve kodun ikinci yarısında bulunan *ID* ÖZELLIĞINI (Get isteğinden alınan) bir parametre olarak kullanır.

> [!WARNING]
> Daha önce Azure portal veya aşağıdaki kodla program aracılığıyla farklı bir Stream Analytics işi için izlemeyi etkinleştirdiyseniz, **izlemeyi daha önce etkinleştirdiğiniz sırada kullandığınız depolama hesabı adını sağlamanızı öneririz.**
> 
> Depolama hesabı, içinde Stream Analytics işinizi oluşturduğunuz bölgeye bağlanır, özellikle işin kendisi için değil.
> 
> Aynı bölgedeki tüm Stream Analytics işleri (ve diğer tüm Azure kaynakları), izleme verilerini depolamak için bu depolama hesabını paylaşır. Farklı bir depolama hesabı sağlarsanız, diğer Stream Analytics işleriniz veya diğer Azure kaynaklarınızın izlenmesinde istenmeyen yan etkilere neden olabilir.
> 
> Aşağıdaki kodda değiştirmek için kullandığınız depolama hesabı adı, `<YOUR STORAGE ACCOUNT NAME>` izlemeyi etkinleştirirken Stream Analytics işle aynı abonelikte olan bir depolama hesabı olmalıdır.
> 
> 
>    ```csharp
>    // Get an existing Stream Analytics job
>     JobGetParameters jobGetParameters = new JobGetParameters()
>     {
>         PropertiesToExpand = "inputs,transformation,outputs"
>     };
>     JobGetResponse jobGetResponse = streamAnalyticsClient.StreamingJobs.Get(resourceGroupName, streamAnalyticsJobName, jobGetParameters);
>
>    // Enable monitoring
>    ServiceDiagnosticSettingsPutParameters insightPutParameters = new ServiceDiagnosticSettingsPutParameters()
>    {
>            Properties = new ServiceDiagnosticSettings()
>           {
>               StorageAccountName = "<YOUR STORAGE ACCOUNT NAME>"
>           }
>    };
>   insightsClient.ServiceDiagnosticSettingsOperations.Put(jobGetResponse.Job.Id, insightPutParameters);
>   ```


## <a name="get-support"></a>Destek alın

Daha fazla yardım için, [Azure Stream Analytics Için Microsoft Q&soru sayfasını](/answers/topics/azure-stream-analytics.html)deneyin.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Stream Analytics giriş](stream-analytics-introduction.md)
* [Azure Akış Analizi'ni kullanmaya başlama](stream-analytics-real-time-fraud-detection.md)
* [Azure Akış Analizi işlerini ölçeklendirme](stream-analytics-scale-jobs.md)
* [Azure Akış Analizi Sorgu Dili Başvurusu](/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Akış Analizi Yönetimi REST API'si Başvurusu](/rest/api/streamanalytics/)