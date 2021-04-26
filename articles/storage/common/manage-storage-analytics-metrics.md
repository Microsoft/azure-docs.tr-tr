---
title: Azure Depolama Analizi ölçümlerini etkinleştirin ve yönetin (klasik) | Microsoft Docs
description: Azure Depolama Analizi ölçümlerini etkinleştirme, düzenleme ve görüntüleme hakkında bilgi edinin.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 01/29/2021
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.custom: monitoring
ms.openlocfilehash: 784929e50d25a07ae92cf388be5ac14f6fa820a6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99221647"
---
# <a name="enable-and-manage-azure-storage-analytics-metrics-classic"></a>Azure Depolama Analizi ölçümlerini etkinleştirin ve yönetin (klasik)

[Azure depolama Analizi](storage-analytics.md) blob 'lar, kuyruklar ve tablolar için tüm depolama hizmetleri için ölçümler sağlar. Hesabınız için hangi ölçümlerin kaydedileceğini yapılandırmak ve ölçüm verilerinizin görsel sunumlarını sağlayan grafikleri yapılandırmak için [Azure Portal](https://portal.azure.com) kullanabilirsiniz. Bu makalede, ölçümlerin nasıl etkinleştirileceği ve yönetileceği gösterilmektedir. Günlükleri nasıl etkinleştireceğinizi öğrenmek için bkz. [Azure depolama Analizi günlüklerini etkinleştirme ve yönetme (klasik)](manage-storage-analytics-logs.md).

[Depolama Için Azure Izleyicisi 'ni](../../azure-monitor/insights/storage-insights-overview.md) (Önizleme) incelemenizi öneririz. Azure Storage hizmetlerinizin performansı, kapasitesi ve kullanılabilirliğinden oluşan Birleşik bir görünüm sunarak Azure depolama hesaplarınızın kapsamlı bir şekilde izlenmesini sağlayan bir Azure Izleyici özelliğidir. Her şeyi etkinleştirmenizi veya yapılandırmanızı gerektirmez ve bu ölçümleri önceden tanımlı etkileşimli grafiklerden ve diğer Görselleştirmelerde hemen görüntüleyebilirsiniz.

> [!NOTE]
> Azure portal izleme verilerini inceleme ile ilişkili maliyetler vardır. Daha fazla bilgi için bkz. [Depolama Analizi](storage-analytics.md).
>
> Premium performans bloğu BLOB depolama hesapları Depolama Analizi ölçümleri desteklemez. Premium performans bloğu BLOB depolama hesaplarıyla ölçümleri görüntülemek istiyorsanız Azure [izleyici 'de Azure depolama ölçümlerini](../blobs/monitor-blob-storage.md)kullanmayı göz önünde bulundurun.
>
> Azure depolama ile ilgili sorunları tanımlamak, tanılamak ve sorunlarını gidermek için Depolama Analizi ve diğer araçları kullanma hakkında ayrıntılı bir kılavuz için bkz. [izleme, tanılama ve sorun giderme Microsoft Azure depolama](storage-monitoring-diagnosing-troubleshooting.md).
>

<a id="Enable-metrics"></a>

## <a name="enable-metrics"></a>Ölçümleri etkinleştir

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. [Azure Portal](https://portal.azure.com), **depolama hesapları**' nı ve ardından depolama hesabı adını seçerek hesap panosunu açın.

2. Menü dikey penceresinin **izleme (klasik)** bölümünde **Tanılama ayarları (klasik)** seçeneğini belirleyin.
   
   ![Izleme (klasik) bölümünde Tanılama ayarları (klasik) seçeneğini vurgulayan ekran görüntüsü.](./media/manage-storage-analytics-metrics/storage-enable-metrics-00.png)

3. İzlemek istediğiniz her bir **hizmet** için ölçüm verilerinin **türünü** ve verilerin **bekletme ilkesini** seçin. Ayrıca, **durumu** **kapalı** olarak ayarlayarak izlemeyi devre dışı bırakabilirsiniz.

   > [!div class="mx-imgBorder"]
   > ![Azure portal günlüğe kaydetmeyi yapılandırın.](./media/manage-storage-analytics-logs/enable-diagnostics.png) 

   Veri saklama ilkesini ayarlamak için, **bekletme (gün)** kaydırıcısını taşıyın veya tutulacak veri günü sayısını 1 ile 365 arasında girin. Yeni depolama hesapları için varsayılan değer yedi gündür. Bir bekletme ilkesi ayarlamak istemiyorsanız, sıfır girin. Bekletme ilkesi yoksa, izleme verilerini silmek size ait olur.

   > [!WARNING]
   > Meetiler, hesabınızda veri olarak depolanır. Ölçüm verileri hesabınızda zaman içinde birikir ve bu da depolama maliyetini artırabilir. Yalnızca küçük bir süre için ölçüm verilerine ihtiyacınız varsa, veri saklama ilkesini değiştirerek maliyetlerinizi azaltabilirsiniz. Eski ölçüm verileri (bekletme ilkenizde daha eski veriler) sistem tarafından silinir. Hesabınız için ölçüm verilerini ne kadar süreyle saklamak istediğinize bağlı olarak bir bekletme ilkesi ayarlamayı öneririz. Daha fazla bilgi için bkz. [depolama ölçümlerinde faturalandırma](storage-analytics-metrics.md#billing-on-storage-metrics) .
   >

4. İzleme yapılandırmasını tamamladığınızda **Kaydet**' i seçin.

Varsayılan ölçüm kümesi, **genel bakış** dikey penceresindeki grafiklerde ve **ölçümler (klasik)** dikey penceresinde görüntülenir. Bir hizmet için ölçümleri etkinleştirdikten sonra, verilerin grafiklerde görünmesi bir saate kadar sürebilir. Grafikte hangi ölçümlerin görüntülendiğini yapılandırmak için herhangi bir ölçüm grafiğinde **Düzenle** seçeneğini belirleyebilirsiniz.

**Durumu** **kapalı** olarak ayarlayarak ölçüm toplamayı ve günlüğe kaydetmeyi devre dışı bırakabilirsiniz.

> [!NOTE]
> Azure depolama, depolama hesabınızın ölçümlerini depolamak için [Tablo Depolaması](storage-introduction.md#table-storage) kullanır ve ölçümleri hesabınızdaki tablolarda depolar. Daha fazla bilgi için, bkz. [Ölçümler nasıl depolanır](storage-analytics-metrics.md#how-metrics-are-stored).

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Bir Windows PowerShell komut penceresi açın.

2. `Connect-AzAccount` komutuyla Azure aboneliğinizde oturum açın ve ekrandaki yönergeleri izleyin.

   ```powershell
   Connect-AzAccount
   ```

3. Kimliğiniz birden fazla abonelikle ilişkiliyse, etkin aboneliğinizi ayarlayın.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   `<subscription-id>`Yer tutucu değerini ABONELIĞINIZIN kimliğiyle değiştirin.

5. Kullanmak istediğiniz depolama hesabını tanımlayan depolama hesabı bağlamını alın.

   ```powershell
   $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
   $ctx = $storageAccount.Context
   ```

   * `<resource-group-name>`Yer tutucu değerini kaynak grubunuzun adıyla değiştirin.

   * `<storage-account-name>`Yer tutucu değerini depolama hesabınızın adıyla değiştirin. 

6. Depolama hesabınızdaki depolama ölçümlerini yapılandırmak için yerel makinenizde PowerShell kullanabilirsiniz. Geçerli ayarları değiştirmek için **set-AzStorageServiceMetricsProperty** cmdlet 'ini Azure PowerShell kullanın. 

   Aşağıdaki komut, Depolama hesabınızdaki blob hizmeti için dakika ölçümlerinde, bekletme dönemi beş güne ayarlanmış olarak geçer.

   ```powershell
   Set-AzStorageServiceMetricsProperty -MetricsType Minute -ServiceType Blob -MetricsLevel ServiceAndApi  -RetentionDays 5 -Context $ctx
   ```   

   Bu cmdlet şu parametreleri kullanır:  

   - **ServiceType**: olası değerler **BLOB**, **kuyruk**, **tablo** ve **dosyadır**.
   - **Metricstype**: olası değerler **saat** ve **dakikadır**.  
   - **Metricslevel**: olası değerler şunlardır:
      - **Hiçbiri**: izlemeyi kapatır.
      - **Hizmet**: blob, kuyruk, tablo ve dosya hizmetleri için toplanan giriş ve çıkış, kullanılabilirlik, gecikme süresi ve başarı yüzdeleri gibi ölçümleri toplar.
      - **ServiceAndApi**: hizmet ölçümlerine ek olarak, Azure depolama hizmeti API 'sindeki her depolama işlemi için aynı ölçüm kümesini toplar.

   Aşağıdaki komut, varsayılan depolama hesabınızdaki blob hizmeti için geçerli saatlik ölçüm düzeyini ve bekletme günlerini alır:  

   ```powershell
   Get-AzStorageServiceMetricsProperty -MetricsType Hour -ServiceType Blob -Context $storagecontext.Context
   ```  

   Azure PowerShell cmdlet 'lerinin Azure aboneliğinizle çalışacak şekilde nasıl yapılandırılacağı ve kullanılacak varsayılan depolama hesabının nasıl ayarlanacağı hakkında bilgi için, bkz. [Azure PowerShell 'Yi yükleyip yapılandırma](/powershell/azure/).  

### <a name="net-v12"></a>[.NET V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/Monitoring.cs" id="snippet_EnableDiagnosticLogs":::

Depolama ölçümlerini yapılandırmak için .NET dili kullanma hakkında daha fazla bilgi için bkz. [.net Için Azure Storage istemci kitaplıkları](/dotnet/api/overview/azure/storage).  

REST API kullanarak depolama ölçümlerini yapılandırma hakkında genel bilgi için bkz. [depolama Analizi etkinleştirme ve yapılandırma](/rest/api/storageservices/Enabling-and-Configuring-Storage-Analytics). 

### <a name="net-v11"></a>[.NET v11](#tab/dotnet11)  

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);  
var queueClient = storageAccount.CreateCloudQueueClient();  
var serviceProperties = queueClient.GetServiceProperties();  

serviceProperties.HourMetrics.MetricsLevel = MetricsLevel.Service;  
serviceProperties.HourMetrics.RetentionDays = 10;  

queueClient.SetServiceProperties(serviceProperties);  
```  

Depolama ölçümlerini yapılandırmak için .NET dili kullanma hakkında daha fazla bilgi için bkz. [.net Için Azure Storage istemci kitaplıkları](/dotnet/api/overview/azure/storage).  

REST API kullanarak depolama ölçümlerini yapılandırma hakkında genel bilgi için bkz. [depolama Analizi etkinleştirme ve yapılandırma](/rest/api/storageservices/Enabling-and-Configuring-Storage-Analytics). 

---

<a id="view-metrics"></a>

## <a name="view-metrics-in-a-chart"></a>Bir grafikteki ölçümleri görüntüleme

Depolama hesabınızı izlemek için Depolama Analizi ölçümleri yapılandırdıktan sonra, Depolama Analizi ölçümleri Depolama hesabınızdaki bir dizi iyi bilinen tabloya kaydeder. [Azure Portal](https://portal.azure.com)saatlik ölçümleri görüntülemek için grafikleri yapılandırabilirsiniz.

Ölçüm grafiğinde görüntülenecek depolama ölçümlerini seçmek için aşağıdaki yordamı kullanın.

1. Azure portal bir depolama ölçümü grafiğini görüntüleyerek başlayın. Grafikleri, **depolama hesabı dikey** penceresinde ve **ölçümler (klasik)** dikey penceresinde bulabilirsiniz.

   Bu örnekte, **depolama hesabı dikey penceresinde** görüntülenen aşağıdaki grafiği kullanır:

   ![Azure portal grafik seçimi](./media/manage-storage-analytics-metrics/stg-customize-chart-00.png)

2. Grafiği düzenlemek için grafiğin içinde herhangi bir yere tıklayın.

3. Ardından, grafikte görüntülenecek ölçümlerin **zaman aralığını** ve ölçümlerini görüntülemek istediğiniz **hizmeti** (blob, kuyruk, tablo, dosya) seçin. Burada, önceki hafta ölçümleri blob hizmeti için görüntülenmek üzere seçilir:

   ![Grafik düzenleme dikey penceresinde zaman aralığı ve hizmet seçimi](./media/manage-storage-analytics-metrics/storage-edit-metric-time-range.png)

4. Grafikte görüntülenmesini istediğiniz bireysel **ölçümleri** seçin ve ardından **Tamam**' a tıklayın.

   ![Grafik düzenleme dikey penceresinde bireysel ölçüm seçimi](./media/manage-storage-analytics-metrics/storage-edit-metric-selections.png)

Grafik ayarlarınız, depolama hesabındaki izleme verilerinin toplanmasını, toplanmasını veya depolanmasını etkilemez.

#### <a name="metrics-availability-in-charts"></a>Grafiklerde ölçüm kullanılabilirliği

Kullanılabilir ölçümlerin listesi, açılan listede seçtiğiniz hizmete ve düzenlemekte olduğunuz grafiğin birim türüne göre değişir. Örneğin, yalnızca yüzde cinsinden birimleri görüntüleyen bir grafik düzenliyorsanız, *Percentnetworkerror* ve *Percentkısıtlar lingerror* gibi yüzde ölçümleri seçebilirsiniz:

![Azure portal hata yüzdesi grafiği iste](./media/manage-storage-analytics-metrics/stg-customize-chart-04.png)

#### <a name="metrics-resolution"></a>Ölçüm çözümlemesi

**Tanılamada** seçtiğiniz ölçümler, hesabınız için kullanılabilir olan ölçümlerin çözümlenme durumunu belirler:

* **Toplama** izlemesi, giriş/çıkış, kullanılabilirlik, gecikme süresi ve başarı yüzdeleri gibi ölçümler sağlar. Bu ölçümler blob, tablo, dosya ve kuyruk hizmetlerinden toplanır.
* **API başına** , hizmet düzeyi toplamalarının yanı sıra ayrı depolama işlemleri için kullanılabilen ölçümler sayesinde daha fazla çözüm sağlar.

## <a name="download-metrics-to-archive-or-analyze-locally"></a>Yerel olarak arşivlemek veya analiz etmek için ölçümleri indirin

Daha uzun vadeli depolama için ölçümleri indirmek veya yerel olarak analiz etmek istiyorsanız, bir araç kullanmanız veya tabloları okumak için kod yazmanız gerekir. Depolama hesabınızdaki tüm tabloları listeleyerek tablolar görünmez, ancak bunlara doğrudan ad ile erişebilirsiniz. Birçok depolama tarama aracı bu tabloları algılar ve bunları doğrudan görüntülemenizi sağlar. Kullanılabilir araçların listesi için bkz. [Azure Storage istemci araçları](./storage-explorers.md).

|Ölçümler|Tablo adları|Notlar| 
|-|-|-|  
|Saatlik ölçümler|$MetricsHourPrimaryTransactionsBlob<br /><br /> $MetricsHourPrimaryTransactionsTable<br /><br /> $MetricsHourPrimaryTransactionsQueue<br /><br /> $MetricsHourPrimaryTransactionsFile|15 Ağustos 2013 ' den önceki sürümlerde bu tablolar şu şekilde bilinmektedir:<br /><br /> $MetricsTransactionsBlob<br /><br /> $MetricsTransactionsTable<br /><br /> $MetricsTransactionsQueue<br /><br /> Dosya hizmeti ölçümleri 5 Nisan 2015 sürümünden itibaren kullanılabilir.|  
|Dakikalık ölçümler|$MetricsMinutePrimaryTransactionsBlob<br /><br /> $MetricsMinutePrimaryTransactionsTable<br /><br /> $MetricsMinutePrimaryTransactionsQueue<br /><br /> $MetricsMinutePrimaryTransactionsFile|Yalnızca PowerShell veya programlı programlama kullanılarak etkinleştirilebilir.<br /><br /> Dosya hizmeti ölçümleri 5 Nisan 2015 sürümünden itibaren kullanılabilir.|  
|Kapasite|$MetricsCapacityBlob|Yalnızca blob hizmeti.|  

Bu tablolara yönelik şemaların tam ayrıntıları için bkz. [ölçüm tablosu şeması depolama Analizi](/rest/api/storageservices/storage-analytics-metrics-table-schema). Aşağıdaki örnek satırlar yalnızca kullanılabilir sütunların bir alt kümesini gösterir, ancak depolama ölçümlerinin bu ölçümleri kaydetme yolunun bazı önemli özelliklerini gösterir:  

|PartitionKey|RowKey|Zaman damgası|TotalRequests|TotalBillableRequests|TotalIngress|TotalEgress|Kullanılabilirlik|AverageE2ELatency|AverageServerLatency|PercentSuccess| 
|-|-|-|-|-|-|-|-|-|-|-|  
|20140522T1100|kullanıcısını Bütün|2014-05-22T11:01:16.7650250 Z|7|7|4003|46801|100|104,4286|6,857143|100|  
|20140522T1100|kullanıcısını Queryenlikler|2014-05-22T11:01:16.7640250 Z|5|5|2694|45951|100|143,8|7.8|100|  
|20140522T1100|kullanıcısını QueryEntity|2014-05-22T11:01:16.7650250 Z|1|1|538|633|100|3|3|100|  
|20140522T1100|kullanıcısını UpdateEntity|2014-05-22T11:01:16.7650250 Z|1|1|771|217|100|9|6|100|  

Bu dakikalık ölçüm verileri örneğinde, bölüm anahtarı dakika çözünürlüğündeki zamanı kullanır. Satır anahtarı, satırda depolanan bilgi türünü tanımlar. Bilgiler, erişim türünden ve istek türünden oluşur:  

-   Erişim türü **Kullanıcı veya** **sistemdir**; burada **Kullanıcı** , depolama hizmetine yönelik tüm Kullanıcı isteklerini ifade eder ve **sistem** , depolama Analizi tarafından yapılan isteklere başvurur.  
-   İstek türü **Tümü**, bu durumda bir Özet satırdır veya **Queryentity** veya **updateentity** gibi belirli API 'yi tanımlar.  

Bu örnek veri, tek bir dakikalık (11 ' den başlayarak) tüm kayıtları gösterir. bu nedenle, **Queryentities** isteklerinin sayısı artı **Queryentity** Isteklerinin sayısı artı **updateentity** isteklerinin sayısı yedi ' a kadar bir değer ekler. Bu toplam, **Kullanıcı: ALL** satırında gösterilir. Benzer şekilde, Kullanıcı üzerindeki ortalama uçtan uca gecikme 104,4286 ' u türetebilirsiniz **:** ((143,8 * 5) + 3 + 9)/7.  

## <a name="view-metrics-data-programmatically"></a>Ölçüm verilerini program aracılığıyla görüntüleme

Aşağıdaki listede, bir dizi dakika için dakikalık ölçümlere erişen örnek C# kodu gösterilmektedir ve sonuçlar bir konsol penceresinde görüntülenir. Kod örneği, depolamada ölçüm tablolarına erişimi kolaylaştıran **CloudAnalyticsClient** sınıfını Içeren Azure Storage istemci kitaplığı sürüm 4. x veya üstünü kullanır. 

> [!NOTE]
> **CloudAnalyticsClient** sınıfı, .net Için Azure Blob depolama istemci kitaplığı V12 ' na dahil değildir. **31 ağustos 2023** ' de, *Klasik ölçümler* olarak da bilinen depolama Analizi ölçümler kullanımdan kaldırılacak. Daha fazla bilgi için [resmi duyuruya](https://azure.microsoft.com/updates/azure-storage-classic-metrics-will-be-retired-on-31-august-2023/) bakın. Klasik ölçümler kullanıyorsanız, bu tarihten önce Azure Izleyici 'de ölçümlere geçiş yapmanızı öneririz. 

```csharp
private static void PrintMinuteMetrics(CloudAnalyticsClient analyticsClient, DateTimeOffset startDateTime, DateTimeOffset endDateTime)  
{  
 // Convert the dates to the format used in the PartitionKey.  
 var start = startDateTime.ToUniversalTime().ToString("yyyyMMdd'T'HHmm");  
 var end = endDateTime.ToUniversalTime().ToString("yyyyMMdd'T'HHmm");  

 var services = Enum.GetValues(typeof(StorageService));  
 foreach (StorageService service in services)  
 {  
     Console.WriteLine("Minute Metrics for Service {0} from {1} to {2} UTC", service, start, end);  
     var metricsQuery = analyticsClient.CreateMinuteMetricsQuery(service, StorageLocation.Primary);  
     var t = analyticsClient.GetMinuteMetricsTable(service);  
     var opContext = new OperationContext();  
     var query =  
             from entity in metricsQuery  
             // Note, you can't filter using the entity properties Time, AccessType, or TransactionType  
             // because they are calculated fields in the MetricsEntity class.  
             // The PartitionKey identifies the DataTime of the metrics.  
             where entity.PartitionKey.CompareTo(start) >= 0 && entity.PartitionKey.CompareTo(end) <= 0   
             select entity;  

     // Filter on "user" transactions after fetching the metrics from Azure Table storage.  
     // (StartsWith is not supported using LINQ with Azure Table storage.)  
     var results = query.ToList().Where(m => m.RowKey.StartsWith("user"));  
     var resultString = results.Aggregate(new StringBuilder(), (builder, metrics) => builder.AppendLine(MetricsString(metrics, opContext))).ToString();  
     Console.WriteLine(resultString);  
 }  
}  

private static string MetricsString(MetricsEntity entity, OperationContext opContext)  
{  
 var entityProperties = entity.WriteEntity(opContext);  
 var entityString =  
         string.Format("Time: {0}, ", entity.Time) +  
         string.Format("AccessType: {0}, ", entity.AccessType) +  
         string.Format("TransactionType: {0}, ", entity.TransactionType) +  
         string.Join(",", entityProperties.Select(e => new KeyValuePair<string, string>(e.Key.ToString(), e.Value.PropertyAsObject.ToString())));  
 return entityString;  
}  
```  

<a id="add-metrics-to-dashboard"></a>

## <a name="add-metrics-charts-to-the-portal-dashboard"></a>Portal panosuna ölçüm grafikleri ekleme

Portal panonuza herhangi bir depolama hesabınız için Azure depolama ölçümleri grafikleri ekleyebilirsiniz.

1. Panonuzu [Azure Portal](https://portal.azure.com)görüntülerken **panoyu Düzenle** ' yi seçin.
1. **Kutucuk galerisinde**, türe **göre kutucukları bul**' u seçin  >  .
1. **Tür**  >  **depolama hesapları**' nı seçin.
1. **Kaynaklar**' da, ölçümlerini panoya eklemek istediğiniz depolama hesabını seçin.
1. **Kategori**  >  **izlemeyi** seçin.
1. Görüntülenmesini istediğiniz ölçüm için grafik kutucuğunu panonuza sürükleyip bırakın. Panoda görüntülenmesini istediğiniz tüm ölçümler için tekrarlayın. Aşağıdaki görüntüde, "blob 'Lar-toplam istek" grafiği bir örnek olarak vurgulanır, ancak tüm grafikler panonuza yerleştirme için kullanılabilir.

   ![Azure portal kutucuk Galerisi](./media/manage-storage-analytics-metrics/storage-customize-dashboard.png)
1. Grafik ekleme işiniz bittiğinde panonun en üst kısmındaki **özelleştirmeyi bitti** ' yi seçin.

Panonuza grafik ekledikten sonra ölçüm grafiklerini özelleştirme bölümünde açıklandığı gibi daha fazla özelleştirme yapabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

* Depolama Analizi hakkında daha fazla bilgi edinmek için bkz. Depolama Analizi [depolama Analizi](storage-analytics.md) .
* [Depolama Analizi günlüklerini yapılandırın](manage-storage-analytics-logs.md).
* Ölçüm şeması hakkında daha fazla bilgi edinin. Bkz. [depolama Analizi ölçümleri tablosu şeması](/rest/api/storageservices/storage-analytics-metrics-table-schema).