---
title: İzleme ve Azure Stream Analytics işleri programlama yoluyla yönetme
description: Bu makalede, Stream Analytics işleri REST API'leri, Azure SDK veya PowerShell oluşturulan program aracılığıyla izlemek açıklar.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/20/2017
ms.openlocfilehash: bf84a5b89e4769e37c45714a30d5d98300a4328d
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67612247"
---
# <a name="programmatically-create-a-stream-analytics-job-monitor"></a>Program aracılığıyla bir Stream Analytics işi İzleyicisi oluşturma

Bu makalede, bir Stream Analytics işi için izlemeyi etkinleştirmek gösterilmektedir. REST API'leri, Azure SDK veya PowerShell oluşturulan bir Stream Analytics işlerini izleme varsayılan olarak etkin yok. El ile Azure portalında iş izleme sayfasına giderek ve etkinleştir düğmesine tıklayarak etkinleştirebilmeniz için veya bu makaledeki adımları izleyerek bu işlemi otomatikleştirebilirsiniz. İzleme verilerini, Azure portalını kullanarak Stream Analytics işine ilişkin ölçümleri alanında gösterilir.

## <a name="prerequisites"></a>Önkoşullar

Bu işlem başlamadan önce aşağıdaki önkoşullara sahip olmalıdır:

* Visual Studio 2019 veya 2015
* [Azure .NET SDK'sı](https://azure.microsoft.com/downloads/) indirilmesi ve yüklenmesi
* İzleme etkin olmasını gerektiren var olan bir Stream Analytics işi

## <a name="create-a-project"></a>Proje oluşturma

1. Visual Studio C# .NET konsol uygulaması oluşturun.
2. Paket Yöneticisi Konsolu'nda NuGet paketlerini yüklemek için aşağıdaki komutları çalıştırın. Azure Stream Analytics yönetim .NET SDK'sı ilk hesaptır. İkinci izlemeyi etkinleştirmek için kullanılan Azure İzleyici SDK'sı olur. Son kimlik doğrulaması için kullanılacak Azure Active Directory istemcisidir.
   
   ```powershell
   Install-Package Microsoft.Azure.Management.StreamAnalytics
   Install-Package Microsoft.Azure.Insights -Pre
   Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
   ```
3. App.config dosyasına aşağıdaki appSettings bölümünü ekleyin.
   
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
   Değerleri Değiştir *Subscriptionıd* ve *ActiveDirectoryTenantId* Azure abonelik ve Kiracı kimliklerine sahip. Aşağıdaki PowerShell cmdlet'ini çalıştırarak bu değerleri alabilirsiniz:
   
   ```powershell
   Get-AzureAccount
   ```
4. Aşağıdaki using deyimlerini projedeki kaynak dosyasına (Program.cs).
   
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
5. Bir kimlik doğrulama Yardımcısını yöntemi ekleyin.

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

Aşağıdaki kod gereken değişkenleri ve yönetim istemcilerin ayarlar.

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

## <a name="enable-monitoring-for-an-existing-stream-analytics-job"></a>Var olan bir Stream Analytics işi için izlemeyi etkinleştir

Aşağıdaki kod etkinleştirir için izleme bir **mevcut** Stream Analytics işi. Kodun ilk bölümünü belirli bir Stream Analytics işi hakkında bilgi almak için Stream Analytics hizmetinde bir GET isteği yapar. Kullandığı *kimliği* (GET istekten alınan) özelliği için Put yöntemini PUT gönderir kod yarısını istek Insights hizmeti için Stream Analytics işi için izlemeyi etkinleştirmek için ikinci bir parametre olarak.

> [!WARNING]
> Daha önce farklı Stream Analytics işi, Azure portalından veya programlama aracılığıyla için izleme etkinleştirdiyseniz, kod, aşağıda **olduğunda kullanılan aynı depolama hesabı adını sağlamanızı öneririz, daha önce izleme etkin.**
> 
> Depolama hesabı bölgeye, Stream Analytics işinizi oluşturduğunuz, iş için özel olarak bağlanır.
> 
> Tüm Stream Analytics işleri (ve diğer Azure kaynakları) aynı bölgede paylaşmak izleme verilerini depolamak için bu depolama hesabı. Farklı bir depolama hesabı sağlarsanız, bu, bir Stream Analytics işleri veya diğer Azure kaynakları izlemesini istenmeyen yan etkilere neden olabilir.
> 
> Depolama hesabı adını değiştirmek için kullandığınız `<YOUR STORAGE ACCOUNT NAME>` aşağıdaki kodda için izlemeyi etkinleştirme Stream Analytics işiyle aynı Abonelikteki bir depolama hesabı olmalıdır.
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

Daha fazla yardım için deneyin bizim [Azure Stream Analytics forumumuzu](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Stream analytics'e giriş](stream-analytics-introduction.md)
* [Azure Akış Analizi'ni kullanmaya başlama](stream-analytics-real-time-fraud-detection.md)
* [Azure Akış Analizi işlerini ölçeklendirme](stream-analytics-scale-jobs.md)
* [Azure Akış Analizi Sorgu Dili Başvurusu](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Akış Analizi Yönetimi REST API'si Başvurusu](https://msdn.microsoft.com/library/azure/dn835031.aspx)
