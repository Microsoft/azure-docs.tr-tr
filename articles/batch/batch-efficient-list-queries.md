---
title: Etkili liste sorguları tasarlama-Azure Batch | Microsoft Docs
description: Havuzlar, işler, görevler ve işlem düğümleri gibi Batch kaynakları hakkında bilgi isteğinde bulunduğunuzu filtreleyerek performansı artırın.
services: batch
documentationcenter: .net
author: laurenhughes
manager: gwallace
editor: ''
ms.assetid: 031fefeb-248e-4d5a-9bc2-f07e46ddd30d
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 12/07/2018
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: 37d34267220cbb7ceabfc823f6facd651969fbd4
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70095157"
---
# <a name="create-queries-to-list-batch-resources-efficiently"></a>Toplu Işlem kaynaklarını etkili bir şekilde listelemek için sorgular oluşturma

Burada, [Batch .net][api_net] kitaplığı ile işleri, görevleri, işlem düğümlerini ve diğer kaynakları sorguladığınızda hizmet tarafından döndürülen veri miktarını azaltarak Azure Batch uygulamanızın performansını nasıl artıracağınızı öğreneceksiniz.

Neredeyse tüm Batch uygulamalarının, genellikle düzenli aralıklarla Batch hizmetini sorgulayan bir tür izleme veya başka işlem gerçekleştirmesi gerekir. Örneğin, bir işte kalan sıraya alınmış görevler olup olmadığını anlamak için, işteki her görevde veri almanız gerekir. Havuzunuzdaki düğümlerin durumunu öğrenmek için, havuzdaki her düğümde veri almanız gerekir. Bu makalede, bu tür sorguların en verimli şekilde nasıl yürütüleceği açıklanmaktadır.

> [!NOTE]
> Batch hizmeti, bir işteki görevleri saymaya ilişkin genel senaryolar ve Batch havuzundaki işlem düğümlerini saymak için özel API desteği sağlar. Bunlar için bir liste sorgusu kullanmak yerine, [görev sayısını Al][rest_get_task_counts] ve [Havuz düğüm sayılarını Listele][rest_get_node_counts] işlemlerini çağırabilirsiniz. Bu işlemler liste sorgusundan daha verimlidir, ancak daha sınırlı bilgi döndürür. Bkz. [görevleri ve işlem düğümlerini duruma göre sayma](batch-get-resource-counts.md). 


## <a name="meet-the-detaillevel"></a>Ayrıntı düzeyini karşılayın
Bir üretim toplu Iş uygulamasında, işler, görevler ve işlem düğümleri gibi varlıklar binlerce sayı olabilir. Bu kaynaklarla ilgili bilgileri istediğinizde, potansiyel olarak büyük miktarda veri, Batch hizmetinden her bir sorgudaki uygulamanıza "tel çapraz" olmalıdır. Bir sorgu tarafından döndürülen öğelerin sayısını ve bilgi türünü sınırlayarak, sorgularınızın hızını ve bu nedenle uygulamanızın performansını artırabilirsiniz.

Bu [Batch .net][api_net] API kod parçacığı, her görevin *Tüm* özellikleriyle birlikte bir işle ilişkili *her* görevi listeler:

```csharp
// Get a collection of all of the tasks and all of their properties for job-001
IPagedEnumerable<CloudTask> allTasks =
    batchClient.JobOperations.ListTasks("job-001");
```

Bununla birlikte, sorgunuza bir "ayrıntı düzeyi" uygulayarak daha verimli bir liste sorgusu gerçekleştirebilirsiniz. Bunu, [JobOperations. ListTasks][net_list_tasks] yöntemine bir [Odatadetaillevel][odata] nesnesi sağlayarak yapabilirsiniz. Bu kod parçacığı yalnızca, tamamlanan görevlerin KIMLIK, komut satırı ve işlem düğümü bilgi özelliklerini döndürür:

```csharp
// Configure an ODATADetailLevel specifying a subset of tasks and
// their properties to return
ODATADetailLevel detailLevel = new ODATADetailLevel();
detailLevel.FilterClause = "state eq 'completed'";
detailLevel.SelectClause = "id,commandLine,nodeInfo";

// Supply the ODATADetailLevel to the ListTasks method
IPagedEnumerable<CloudTask> completedTasks =
    batchClient.JobOperations.ListTasks("job-001", detailLevel);
```

Bu örnek senaryoda, işte binlerce görev varsa, ikinci sorgudaki sonuçlar genellikle birinciden çok daha hızlı döndürülür. Batch .NET API 'SI ile öğeleri listeettiğiniz zaman ODATADetailLevel kullanımı hakkında daha fazla bilgi [aşağıda](#efficient-querying-in-batch-net)verilmiştir.

> [!IMPORTANT]
> Uygulamanızın en yüksek verimliliğini ve performansını sağlamak için .NET API listesi çağrılarınız için *her zaman* bir ODATADetailLevel nesnesi vermenizi öneririz. Ayrıntı düzeyi belirterek, Batch hizmeti yanıt sürelerini azaltmaya, ağ kullanımını iyileştirmenize ve istemci uygulamaları tarafından bellek kullanımını en aza indirmenize yardımcı olabilirsiniz.
> 
> 

## <a name="filter-select-and-expand"></a>Filtrele, seç ve Genişlet
[Batch .net][api_net] ve [Batch Rest][api_rest] API 'leri, bir listede döndürülen öğelerin sayısını ve her biri için döndürülen bilgi miktarını azaltmaya olanak sağlar. Bunu, liste sorguları gerçekleştirirken **filtre**, **seçme**ve **dizeleri Genişlet** ' i belirterek yapabilirsiniz.

### <a name="filter"></a>Filtre
Filtre dizesi döndürülen öğelerin sayısını azaltan bir ifadedir. Örneğin, yalnızca bir iş için çalışan görevleri listeleyin veya yalnızca görevleri çalıştırmaya hazırlamış olan işlem düğümlerini listeleyin.

* Filtre dizesi bir veya daha fazla ifadeden oluşur ve bir özellik adı, işleç ve değer içeren bir ifade içerir. Belirtime özellikler, her bir özellik için desteklenen işleçler gibi, Sorgulayabileceğiniz her bir varlık türüne özeldir.
* Birden çok ifade mantıksal işleçler `and` ve `or`kullanılarak birleştirilebilir.
* Bu örnek filtre dizesinde yalnızca çalışan "render" görevleri listelenir: `(state eq 'running') and startswith(id, 'renderTask')`.

### <a name="select"></a>Seçim
Select String her öğe için döndürülen özellik değerlerini sınırlandırır. Özellik adlarının bir listesini belirtirsiniz ve sorgu sonuçlarındaki öğeler için yalnızca bu özellik değerleri döndürülür.

* Select dizesi, özellik adlarının virgülle ayrılmış bir listesinden oluşur. Sorgulamakta olduğunuz varlık türü için özelliklerden herhangi birini belirtebilirsiniz.
* Bu örnek Select String, her görev için yalnızca üç özellik değerinin döndürülüp döndürülmeyeceğini belirtir: `id, state, stateTransitionTime`.

### <a name="expand"></a>Expand
Expand dize, belirli bilgileri almak için gerekli olan API çağrılarının sayısını azaltır. Bir genişletme dizesi kullandığınızda her öğe hakkında daha fazla bilgi tek bir API çağrısıyla elde edilebilir. İlk olarak, varlıkların listesini almak ve sonra listedeki her öğe için bilgi istemek yerine, tek bir API çağrısında aynı bilgileri elde etmek için bir genişletme dizesi kullanırsınız. Daha az API çağrısı, daha iyi performans anlamına gelir.

* Select dizesine benzer şekilde, genişletme dizesi belirli verilerin liste sorgu sonuçlarına dahil edilip edilmeyeceğini denetler.
* Expand dize yalnızca liste işleri, iş zamanlamaları, görevler ve havuzlar üzerinde kullanıldığında desteklenir. Şu anda yalnızca istatistik bilgilerini destekler.
* Tüm özellikler gerekli olduğunda ve Select String belirtilmediğinde, istatistik bilgilerini almak için genişletme dizesi kullanılmalıdır. Bir SELECT dizesi, özelliklerin bir alt kümesini almak için kullanılırsa, `stats` Select dizesinde belirtilebilir ve genişletme dizesinin belirtilmesi gerekmez.
* Bu örnek dize genişletme, listedeki her öğe için İstatistik bilgilerinin döndürülmesini belirtir: `stats`.

> [!NOTE]
> Üç sorgu dizesi türünden birini oluştururken (Filter, Select ve Expand), özellik adlarının ve durumunun REST API öğelerinin karşılıklarıyla eşleştiğinden emin olmanız gerekir. Örneğin, .NET [cloudtask](/dotnet/api/microsoft.azure.batch.cloudtask) sınıfıyla çalışırken, .NET özelliği [Cloudtask. State](/dotnet/api/microsoft.azure.batch.cloudtask.state#Microsoft_Azure_Batch_CloudTask_State)olsa bile durum yerine **State** belirtmeniz gerekir. .NET ve REST API 'Ler arasındaki özellik eşlemeleri için aşağıdaki tablolara bakın.
> 
> 

### <a name="rules-for-filter-select-and-expand-strings"></a>Filter, Select ve Expand dizeleri için kurallar
* Filtre içindeki Özellikler adları, Select ve Genişlet dizeleri Batch [.net][api_net] veya diğer Batch SDK 'larından birini kullandığınızda bile, [toplu Rest][api_rest] API 'sinde olduğu gibi görünmelidir.
* Tüm özellik adları büyük/küçük harfe duyarlıdır, ancak özellik değerleri büyük/küçük harfe duyarsızdır.
* Tarih/saat dizeleri iki biçimden biri olabilir ve önünde `DateTime`olmalıdır.
  
  * W3C-DTF biçim örneği:`creationTime gt DateTime'2011-05-08T08:49:37Z'`
  * RFC 1123 biçim örneği:`creationTime gt DateTime'Sun, 08 May 2011 08:49:37 GMT'`
* Boolean dizeleri ya `false`da `true` .
* Geçersiz bir özellik veya işleç belirtilirse, bir `400 (Bad Request)` hata ortaya kaydedilir.

## <a name="efficient-querying-in-batch-net"></a>Batch .NET 'te verimli sorgulama
[Batch .net][api_net] API 'si Içinde, [Odatadetaillevel][odata] sınıfı filtre sağlamak için kullanılır, seçme ve dizeleri listelemek için dizeleri genişletme. ODataDetailLevel sınıfı, oluşturucuda belirtime üç genel dize özelliklerine sahiptir veya doğrudan nesne üzerinde ayarlanır. Ardından, ODataDetailLevel nesnesini [Listpools][net_list_pools], [ListJobs][net_list_jobs]ve [ListTasks][net_list_tasks]gibi çeşitli liste işlemlerine bir parametre olarak geçitirsiniz.

* [Odatadetaillevel][odata]. [FilterClause][odata_filter]: Döndürülen öğelerin sayısını sınırlayın.
* [Odatadetaillevel][odata]. [Selectclause][odata_select]: Hangi özellik değerlerinin her öğe ile döndürüleceğini belirtin.
* [Odatadetaillevel][odata]. [Expandclause][odata_expand]: Her öğe için ayrı çağrılar yerine tek bir API çağrısındaki tüm öğeler için veri alın.

Aşağıdaki kod parçacığı, Batch hizmetini belirli bir havuz kümesinin istatistikleri için etkin bir şekilde sorgulamak üzere Batch .NET API 'sini kullanır. Bu senaryoda Batch kullanıcısının hem test hem de üretim havuzları vardır. Test havuzu kimlikleri "test" önekini alır ve üretim havuzu kimlikleri "prod" önekini alır. Kod parçacığında, *Mybatchclient* , [batchclient](/dotnet/api/microsoft.azure.batch.batchclient) sınıfının düzgün başlatılmış bir örneğidir.

```csharp
// First we need an ODATADetailLevel instance on which to set the filter, select,
// and expand clause strings
ODATADetailLevel detailLevel = new ODATADetailLevel();

// We want to pull only the "test" pools, so we limit the number of items returned
// by using a FilterClause and specifying that the pool IDs must start with "test"
detailLevel.FilterClause = "startswith(id, 'test')";

// To further limit the data that crosses the wire, configure the SelectClause to
// limit the properties that are returned on each CloudPool object to only
// CloudPool.Id and CloudPool.Statistics
detailLevel.SelectClause = "id, stats";

// Specify the ExpandClause so that the .NET API pulls the statistics for the
// CloudPools in a single underlying REST API call. Note that we use the pool's
// REST API element name "stats" here as opposed to "Statistics" as it appears in
// the .NET API (CloudPool.Statistics)
detailLevel.ExpandClause = "stats";

// Now get our collection of pools, minimizing the amount of data that is returned
// by specifying the detail level that we configured above
List<CloudPool> testPools =
    await myBatchClient.PoolOperations.ListPools(detailLevel).ToListAsync();
```

> [!TIP]
> Select ve expand yan tümceleri ile yapılandırılan [Odatadetaillevel][odata] örneği, döndürülen veri miktarını sınırlamak Için [Pooloperations. getpool](/dotnet/api/microsoft.azure.batch.pooloperations.getpool#Microsoft_Azure_Batch_PoolOperations_GetPool_System_String_Microsoft_Azure_Batch_DetailLevel_System_Collections_Generic_IEnumerable_Microsoft_Azure_Batch_BatchClientBehavior__)gibi uygun alma yöntemlerine de geçirilebilir.
> 
> 

## <a name="batch-rest-to-net-api-mappings"></a>Toplu iş geri kalanı .NET API eşlemeleri
Filtre içindeki Özellik adları, Select ve Genişlet dizeleri, hem ad hem de durum ' da REST API karşılamalarını yansıtmalıdır. Aşağıdaki tablolar, .NET ve REST API karşılıkları arasında eşlemeler sağlar.

### <a name="mappings-for-filter-strings"></a>Filtre dizeleri için eşlemeler
* **.Net liste yöntemleri**: Bu sütundaki .NET API yöntemlerinin her biri, bir [Odatadetaillevel][odata] nesnesini parametre olarak kabul eder.
* **Rest liste istekleri**: Bu sütunda bağlantılı her bir REST API sayfası, *filtre* dizelerinde izin verilen özellikleri ve işlemleri belirten bir tablo içerir. Bu özellik adlarını ve işlemlerini bir [Odatadetaillevel. FilterClause][odata_filter] dizesi oluşturduğunuzda kullanacaksınız.

| .NET liste yöntemleri | REST liste istekleri |
| --- | --- |
| [CertificateOperations. ListCertificates][net_list_certs] |[Bir hesaptaki sertifikaları listeleme][rest_list_certs] |
| [CloudTask. ListNodeFiles][net_list_task_files] |[Bir görevle ilişkili dosyaları listeleme][rest_list_task_files] |
| [JobOperations. ListJobPreparationAndReleaseTaskStatus][net_list_jobprep_status] |[İş için iş hazırlama ve iş bırakma görevlerinin durumunu listeleyin][rest_list_jobprep_status] |
| [JobOperations. ListJobs][net_list_jobs] |[Bir hesaptaki işleri listeleyin][rest_list_jobs] |
| [JobOperations. ListNodeFiles][net_list_nodefiles] |[Bir düğümdeki dosyaları listeleme][rest_list_nodefiles] |
| [JobOperations. ListTasks][net_list_tasks] |[Bir işle ilişkili görevleri listeleyin][rest_list_tasks] |
| [JobScheduleOperations. Listjobzamanlamalar][net_list_job_schedules] |[Bir hesaptaki iş zamanlamalarını listeleme][rest_list_job_schedules] |
| [JobScheduleOperations. ListJobs][net_list_schedule_jobs] |[İş zamanlamasıyla ilişkili işleri listeleyin][rest_list_schedule_jobs] |
| [PoolOperations. ListComputeNodes][net_list_compute_nodes] |[Bir havuzdaki işlem düğümlerini listeleme][rest_list_compute_nodes] |
| [PoolOperations. ListPools][net_list_pools] |[Bir hesaptaki havuzları listeleme][rest_list_pools] |

### <a name="mappings-for-select-strings"></a>Seçme dizeleri için eşlemeler
* **Batch .net türleri**: Batch .NET API türleri.
* **REST API varlıkları**: Bu sütundaki her sayfa, türün REST API özellik adlarını listeeden bir veya daha fazla tablo içerir. Bu özellik adları, *Select* dizeleri oluşturduğunuzda kullanılır. [Odatadetaillevel. SelectClause][odata_select] dizesi oluştururken aynı özellik adlarını kullanacaksınız.

| Batch .NET türleri | REST API varlıkları |
| --- | --- |
| [Sertifika][net_cert] |[Bir sertifika hakkında bilgi edinme][rest_get_cert] |
| [CloudJob][net_job] |[İş hakkında bilgi alın][rest_get_job] |
| [CloudJobSchedule][net_schedule] |[İş zamanlaması hakkında bilgi alın][rest_get_schedule] |
| [ComputeNode][net_node] |[Bir düğüm hakkında bilgi edinme][rest_get_node] |
| [CloudPool][net_pool] |[Havuz hakkında bilgi edinme][rest_get_pool] |
| [CloudTask][net_task] |[Bir görev hakkında bilgi edinme][rest_get_task] |

## <a name="example-construct-a-filter-string"></a>Örnek: bir filtre dizesi oluşturun
[Odatadetaillevel. FilterClause][odata_filter]için bir filtre dizesi oluşturduğunuzda, gerçekleştirmek istediğiniz liste işlemine karşılık gelen REST API belge sayfasını bulmak için yukarıdaki "Filtre dizeleri için eşlemeler" altındaki tabloya başvurun. Filtrelenebilir özellikleri ve bu sayfadaki ilk multirow tablosunda desteklenen işleçlerini bulacaksınız. Çıkış kodu sıfır olmayan tüm görevleri almak isterseniz, örneğin, [bir işle ilişkili görevleri listede][rest_list_tasks] bu satır ilgili özellik dizesini ve izin verilen işleçleri belirler:

| Özellik | İzin verilen işlemler | Type |
|:--- |:--- |:--- |
| `executionInfo/exitCode` |`eq, ge, gt, le , lt` |`Int` |

Bu nedenle, sıfır dışında çıkış koduna sahip tüm görevleri listelemek için filtre dizesi şöyle olur:

`(executionInfo/exitCode lt 0) or (executionInfo/exitCode gt 0)`

## <a name="example-construct-a-select-string"></a>Örnek: Select String oluşturun
[Odatadetaillevel. SelectClause][odata_select]oluşturmak için, "dizeleri seç için eşlemeler" altındaki tabloya bakın ve listelerken varlık türüne karşılık gelen REST API sayfasına gidin. Seçilebilir özellikleri ve bu sayfadaki ilk multirow tablosunda desteklenen işleçlerini bulacaksınız. Bir listedeki her bir görev için yalnızca KIMLIĞI ve komut satırını almak istiyorsanız, [bir görevle ilgili bilgi edinmek][rest_get_task]için bu satırları uygulanabilir tabloda bulabilirsiniz:

| Özellik | Type | Notlar |
|:--- |:--- |:--- |
| `id` |`String` |`The ID of the task.` |
| `commandLine` |`String` |`The command line of the task.` |

Yalnızca listelenen her görevle ilgili KIMLIĞI ve komut satırını dahil etmek için seçim dizesi şöyle olacaktır:

`id, commandLine`

## <a name="code-samples"></a>Kod örnekleri
### <a name="efficient-list-queries-code-sample"></a>Etkili liste sorguları kod örneği
Etkili liste sorgulama 'nın bir uygulamadaki performansı nasıl etkileyebileceğini görmek için GitHub 'daki [verimlilik Entlistqueries][efficient_query_sample] örnek projesine göz atın. Bu C# konsol uygulaması, bir işe çok sayıda görev oluşturur ve ekler. Daha sonra, [JobOperations. ListTasks][net_list_tasks] yöntemine birden çok çağrı yapar ve döndürülecek veri miktarını değiştirmek için farklı özellik değerleriyle yapılandırılan [Odatadetaillevel][odata] nesnelerini geçirir. Aşağıdakine benzer bir çıktı üretir:

```
Adding 5000 tasks to job jobEffQuery...
5000 tasks added in 00:00:47.3467587, hit ENTER to query tasks...

4943 tasks retrieved in 00:00:04.3408081 (ExpandClause:  | FilterClause: state eq 'active' | SelectClause: id,state)
0 tasks retrieved in 00:00:00.2662920 (ExpandClause:  | FilterClause: state eq 'running' | SelectClause: id,state)
59 tasks retrieved in 00:00:00.3337760 (ExpandClause:  | FilterClause: state eq 'completed' | SelectClause: id,state)
5000 tasks retrieved in 00:00:04.1429881 (ExpandClause:  | FilterClause:  | SelectClause: id,state)
5000 tasks retrieved in 00:00:15.1016127 (ExpandClause:  | FilterClause:  | SelectClause: id,state,environmentSettings)
5000 tasks retrieved in 00:00:17.0548145 (ExpandClause: stats | FilterClause:  | SelectClause: )

Sample complete, hit ENTER to continue...
```

Geçen zamanlarda gösterildiği gibi, özellikleri ve döndürülen öğelerin sayısını sınırlayarak sorgu yanıt sürelerini önemli ölçüde düşürebilirsiniz. Bu ve diğer örnek projeleri GitHub 'daki [Azure-Batch-Samples][github_samples] deposunda bulabilirsiniz.

### <a name="batchmetrics-library-and-code-sample"></a>Batchölçümler kitaplığı ve kod örneği
Yukarıdaki Verimlilientlistqueries kod örneğine ek olarak, [Azure-Batch-Samples][github_samples] GitHub deposunda [batchölçümler][batch_metrics] projesini bulabilirsiniz. Batchölçümler örnek projesi, Batch API 'SI kullanılarak Azure Batch iş ilerlemesini verimli bir şekilde izlemeyi gösterir.

[Batchölçümler][batch_metrics] örneği, kendi projelerinizle birleştirebileceğiniz bir .NET sınıf kitaplığı projesi ve kitaplığın kullanımını sağlamak ve göstermek için basit bir komut satırı programı içerir.

Proje içindeki örnek uygulama aşağıdaki işlemleri gösterir:

1. Yalnızca ihtiyacınız olan özellikleri indirmek için belirli öznitelikleri seçme
2. Son sorgudan bu yana yalnızca değişiklikleri indirmek için durum geçiş süreleriyle filtreleme

Örneğin, aşağıdaki yöntem Batchölçümlerini kitaplığında görüntülenir. Sorgulanan varlıklar için yalnızca `id` ve `state` özelliklerinin alınması gerektiğini belirten bir odatadetaillevel döndürür. Ayrıca, belirtilen `DateTime` parametrenin döndürülmesinden sonra yalnızca durumu değişmiş olan varlıkların de belirtir.

```csharp
internal static ODATADetailLevel OnlyChangedAfter(DateTime time)
{
    return new ODATADetailLevel(
        selectClause: "id, state",
        filterClause: string.Format("stateTransitionTime gt DateTime'{0:o}'", time)
    );
}
```

## <a name="next-steps"></a>Sonraki adımlar
### <a name="parallel-node-tasks"></a>Paralel düğüm görevleri
[Eşzamanlı düğüm görevleriyle Azure Batch işlem kaynağı kullanımının en üst düzeye çıkarmak](batch-parallel-node-tasks.md) , Batch uygulama performansı ile ilgili başka bir makaledir. Bazı iş yükü türleri, paralel görevleri daha büyük ancak daha az işlem düğümlerine yürütmenin avantajlarından yararlanabilir. Bu tür bir senaryoya ilişkin ayrıntılar için makalesindeki [Örnek senaryoya](batch-parallel-node-tasks.md#example-scenario) göz atın.


[api_net]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch
[api_net_listjobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[api_rest]: https://docs.microsoft.com/rest/api/batchservice/
[batch_metrics]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchMetrics
[efficient_query_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/EfficientListQueries
[github_samples]: https://github.com/Azure/azure-batch-samples
[odata]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.aspx
[odata_ctor]: https://msdn.microsoft.com/library/azure/dn866178.aspx
[odata_expand]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.expandclause.aspx
[odata_filter]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.filterclause.aspx
[odata_select]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.selectclause.aspx

[net_list_certs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.certificateoperations.listcertificates.aspx
[net_list_compute_nodes]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listcomputenodes.aspx
[net_list_job_schedules]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobscheduleoperations.listjobschedules.aspx
[net_list_jobprep_status]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobpreparationandreleasetaskstatus.aspx
[net_list_jobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[net_list_nodefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listnodefiles.aspx
[net_list_pools]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listpools.aspx
[net_list_schedule_jobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobscheduleoperations.listjobs.aspx
[net_list_task_files]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.listnodefiles.aspx
[net_list_tasks]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listtasks.aspx

[rest_list_certs]: https://msdn.microsoft.com/library/azure/dn820154.aspx
[rest_list_compute_nodes]: https://msdn.microsoft.com/library/azure/dn820159.aspx
[rest_list_job_schedules]: https://msdn.microsoft.com/library/azure/mt282174.aspx
[rest_list_jobprep_status]: https://msdn.microsoft.com/library/azure/mt282170.aspx
[rest_list_jobs]: https://msdn.microsoft.com/library/azure/dn820117.aspx
[rest_list_nodefiles]: https://msdn.microsoft.com/library/azure/dn820151.aspx
[rest_list_pools]: https://msdn.microsoft.com/library/azure/dn820101.aspx
[rest_list_schedule_jobs]: https://msdn.microsoft.com/library/azure/mt282169.aspx
[rest_list_task_files]: https://msdn.microsoft.com/library/azure/dn820142.aspx
[rest_list_tasks]: https://msdn.microsoft.com/library/azure/dn820187.aspx

[rest_get_cert]: https://msdn.microsoft.com/library/azure/dn820176.aspx
[rest_get_job]: https://msdn.microsoft.com/library/azure/dn820106.aspx
[rest_get_node]: https://msdn.microsoft.com/library/azure/dn820168.aspx
[rest_get_pool]: https://msdn.microsoft.com/library/azure/dn820165.aspx
[rest_get_schedule]: https://msdn.microsoft.com/library/azure/mt282171.aspx
[rest_get_task]: https://msdn.microsoft.com/library/azure/dn820133.aspx

[net_cert]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.certificate.aspx
[net_job]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_node]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.aspx
[net_pool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_schedule]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjobschedule.aspx
[net_task]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx

[rest_get_task_counts]: /rest/api/batchservice/get-the-task-counts-for-a-job
[rest_get_node_counts]: /rest/api/batchservice/account/listpoolnodecounts
