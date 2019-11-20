---
title: İşlem düğümlerinde işleri hazırlamak ve işleri gerçekleştirmek için görevler oluşturma-Azure Batch | Microsoft Docs
description: İşlem düğümlerine Azure Batch veri aktarımını en aza indirmek ve iş tamamlandığında düğüm Temizleme için görevleri serbest bırakmak için iş düzeyinde hazırlık görevlerini kullanın.
services: batch
documentationcenter: .net
author: laurenhughes
manager: gwallace
editor: ''
ms.assetid: 63d9d4f1-8521-4bbb-b95a-c4cad73692d3
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 02/27/2017
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: 2dbdbc8b13a75b72ca09a319c6925d0835a52e13
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70095127"
---
# <a name="run-job-preparation-and-job-release-tasks-on-batch-compute-nodes"></a>Toplu işlem düğümlerinde iş hazırlama ve iş bırakma görevlerini çalıştırma

 Azure Batch işi genellikle görevleri yürütülmeden önce bir kurulum biçimi gerektirir ve görevleri tamamlandığında iş sonrası bakım gerektirir. İşlem düğümleriniz için ortak görev giriş verilerini indirmeniz veya iş tamamlandıktan sonra Azure depolama 'ya görev çıkış verilerini yüklemeniz gerekebilir. Bu işlemleri gerçekleştirmek için **iş hazırlama** ve **iş sürüm** görevleri kullanabilirsiniz.

## <a name="what-are-job-preparation-and-release-tasks"></a>İş hazırlama ve sürüm görevleri nelerdir?
Bir işin görevleri çalıştırılmadan önce, iş hazırlama görevi, en az bir görevi çalıştırmak için zamanlanan tüm işlem düğümlerinde çalışır. İş tamamlandıktan sonra iş bırakma görevi, havuzdaki en az bir görevi yürüten her düğümde çalışır. Normal Batch görevlerinde olduğu gibi, bir iş hazırlama veya bırakma görevi çalıştırıldığında çağrılacak komut satırını belirtebilirsiniz.

İş hazırlama ve bırakma görevleri, dosya indirme ([kaynak dosyaları][net_job_prep_resourcefiles]), yükseltilmiş yürütme, özel ortam değişkenleri, en uzun yürütme süresi, yeniden deneme sayısı ve dosya saklama süresi gibi tanıdık toplu görev özellikleri sunar.

Aşağıdaki bölümlerde, [Batch .net][api_net] kitaplığı 'Nda bulunan [JobPreparationTask][net_job_prep] ve [jobreleasetask][net_job_release] sınıflarını nasıl kullanacağınızı öğreneceksiniz.

> [!TIP]
> İş hazırlama ve bırakma görevleri özellikle bir işlem düğümü havuzunun iş çalıştırmaları arasında devam ettiği ve birçok iş tarafından kullanıldığı "paylaşılan havuz" ortamlarında yararlıdır.
> 
> 

## <a name="when-to-use-job-preparation-and-release-tasks"></a>İş hazırlama ve bırakma görevlerinin ne zaman kullanılacağı
İş hazırlama ve iş bırakma görevleri aşağıdaki durumlara uygun şekilde uygundur:

**Ortak görev verilerini indir**

Batch işleri genellikle iş görevleri için giriş olarak ortak bir veri kümesi gerektirir. Örneğin, günlük risk analizi hesaplamalarında, Pazar verileri iş açısından özeldir, ancak işteki tüm görevler için ortaktır. Bu Pazar verileri genellikle birkaç gigabayt, her bir işlem düğümüne yalnızca bir kez indirilmelidir, böylece düğüm üzerinde çalışan herhangi bir görev tarafından kullanılabilir. İşin diğer görevlerinin yürütülmesinden önce bu verileri her düğüme indirmek için bir **iş hazırlama görevi** kullanın.

**İşi ve görev çıkışını silme**

Bir havuzun işlem düğümlerinin iş arasında kullanımdan kaldırılacağı bir "paylaşılan havuz" ortamında, çalışma arasında iş verilerini silmeniz gerekebilir. Düğümlerdeki disk alanından tasarruf etmeniz veya kuruluşunuzun güvenlik ilkelerini karşılamanız gerekebilir. Bir iş hazırlama görevi tarafından indirilen veya Görev yürütülürken oluşturulan verileri silmek için bir **iş bırakma görevi** kullanın.

**Günlük tutma**

Görevlerinizin oluşturduğu günlük dosyalarının bir kopyasını tutmak veya hatalı uygulamalar tarafından oluşturulabilecek döküm dosyalarını korumak isteyebilirsiniz. Bu verileri sıkıştırmak ve bir [Azure depolama][azure_storage] hesabına yüklemek için bu gibi durumlarda **iş serbest bırakma görevi** kullanın.

> [!TIP]
> Günlüklerin ve diğer iş ve görev çıktısı verilerinin kalıcı hale getirilmesi için başka bir yöntem de [Azure Batch dosya kuralları](batch-task-output.md) kitaplığını kullanmaktır.
> 
> 

## <a name="job-preparation-task"></a>İş hazırlama görevi
Bir işin görevlerinin yürütülmesinden önce Batch, görevi çalıştırmak için zamanlanan her bir işlem düğümünde iş hazırlama görevini yürütür. Varsayılan olarak, Batch hizmeti, düğüm üzerinde yürütülmek üzere zamanlanan görevleri çalıştırmadan önce iş hazırlama görevinin tamamlanmasını bekler. Ancak, hizmeti beklememe için de yapılandırabilirsiniz. Düğüm yeniden başlatılırsa, iş hazırlama görevi yeniden çalışır, ancak bu davranışı da devre dışı bırakabilirsiniz.

İş hazırlama görevi yalnızca bir görevi çalıştırmak için zamanlanan düğümlerde yürütülür. Bu, bir düğüme bir görevin atanmadığı durumlarda bir hazırlık görevinin gereksiz şekilde yürütülmesini önler. Bu durum, bir iş için görev sayısı bir havuzdaki düğüm sayısından az olduğunda meydana gelebilir. Aynı zamanda, görev sayısı toplam olası eşzamanlı görevden daha düşükse bazı düğümleri boşta bırakan, [eşzamanlı görev yürütme](batch-parallel-node-tasks.md) etkinleştirildiğinde da geçerlidir. Boştaki düğümlerde iş hazırlama görevini çalıştırmayan zaman, veri aktarımı ücretlerine göre daha az para harcamanız sağlayabilirsiniz.

> [!NOTE]
> [JobPreparationTask][net_job_prep_cloudjob] , her bir Işin başlangıcında JobPreparationTask çalıştırdığı [Cloudpool. startTask][pool_starttask] öğesinden farklıdır, ancak startTask yalnızca bir işlem düğümü bir havuza katıldığında veya yeniden başlatıldığında yürütülür.
> 
> 

## <a name="job-release-task"></a>İş bırakma görevi
Bir iş tamamlandı olarak işaretlendikten sonra, havuzda en az bir görevi yürüten her düğüm üzerinde iş bırakma görevi yürütülür. Bir işi sonlandırma isteği vererek tamamlandı olarak işaretlersiniz. Batch hizmeti daha sonra iş durumunu *sonlandırılıyor*, işle ilgili etkin veya çalışan görevleri sonlandırır ve iş serbest bırakma görevini çalıştırır. Sonra iş *tamamlandı* durumuna gider.

> [!NOTE]
> İş silme işi de iş bırakma görevini yürütür. Ancak, bir iş zaten sonlandırılırsa, iş daha sonra silinirse yayın görevi ikinci kez çalıştırılmaz.

Iş sürümü görevleri, Batch hizmeti tarafından sonlandırılmadan önce en fazla 15 dakika boyunca çalıştırılabilir. Daha fazla bilgi için [REST API başvuru belgelerine](https://docs.microsoft.com/rest/api/batchservice/job/add#jobreleasetask)bakın.
> 
> 

## <a name="job-prep-and-release-tasks-with-batch-net"></a>Batch .NET ile iş PREP ve sürüm görevleri
İş hazırlama görevini kullanmak için, işinizin [Cloudjob. JobPreparationTask][net_job_prep_cloudjob] özelliğine bir [JobPreparationTask][net_job_prep] nesnesi atayın. Benzer şekilde, bir [jobreleasetask][net_job_release] başlatın ve işin yayın görevini ayarlamak için Iş 'Nin [Cloudjob. jobreleasetask][net_job_prep_cloudjob] özelliğine atayın.

Bu kod parçacığında, `myBatchClient` bir [batchclient][net_batch_client]örneğidir ve `myPool` Batch hesabı içindeki mevcut bir havuzudur.

```csharp
// Create the CloudJob for CloudPool "myPool"
CloudJob myJob =
    myBatchClient.JobOperations.CreateJob(
        "JobPrepReleaseSampleJob",
        new PoolInformation() { PoolId = "myPool" });

// Specify the command lines for the job preparation and release tasks
string jobPrepCmdLine =
    "cmd /c echo %AZ_BATCH_NODE_ID% > %AZ_BATCH_NODE_SHARED_DIR%\\shared_file.txt";
string jobReleaseCmdLine =
    "cmd /c del %AZ_BATCH_NODE_SHARED_DIR%\\shared_file.txt";

// Assign the job preparation task to the job
myJob.JobPreparationTask =
    new JobPreparationTask { CommandLine = jobPrepCmdLine };

// Assign the job release task to the job
myJob.JobReleaseTask =
    new JobReleaseTask { CommandLine = jobReleaseCmdLine };

await myJob.CommitAsync();
```

Daha önce belirtildiği gibi, yayın görevi bir iş sonlandırıldığı veya silindiği zaman yürütülür. [JobOperations. TerminateJobAsync][net_job_terminate]ile bir işi sonlandırın. [JobOperations. DeleteJobAsync][net_job_delete]ile bir iş silin. Genellikle, görevleri tamamlandığında veya tanımladığınız zaman aşımıyla erişildiğinde bir işi sonlandırır veya silmeniz gerekir.

```csharp
// Terminate the job to mark it as Completed; this will initiate the
// Job Release Task on any node that executed job tasks. Note that the
// Job Release Task is also executed when a job is deleted, thus you
// need not call Terminate if you typically delete jobs after task completion.
await myBatchClient.JobOperations.TerminateJobAsync("JobPrepReleaseSampleJob");
```

## <a name="code-sample-on-github"></a>GitHub 'da kod örneği
İş hazırlama ve bırakma görevlerini eylemde görmek için GitHub 'da [JobPrepRelease][job_prep_release_sample] örnek projesine göz atın. Bu konsol uygulaması şunları yapar:

1. İki düğüm içeren bir havuz oluşturur.
2. İş hazırlama, yayınlama ve standart görevlerle bir iş oluşturur.
3. Önce düğüm KIMLIĞINI düğümün "paylaşılan" dizinindeki bir metin dosyasına yazan iş hazırlama görevini çalıştırır.
4. Her düğümde görev KIMLIĞINI aynı metin dosyasına yazan bir görev çalıştırır.
5. Tüm görevler tamamlandıktan sonra (veya zaman aşımına ulaşıldığında), her bir düğümün metin dosyasının içeriğini konsola yazdırır.
6. İş tamamlandığında, dosyayı düğümden silmek için iş bırakma görevini çalıştırır.
7. Yürütüldüğü her düğüm için iş hazırlama ve bırakma görevlerinin çıkış kodlarını yazdırır.
8. İşin ve/veya havuzun silinme onayına izin vermek için yürütmeyi duraklatır.

Örnek uygulamanın çıktısı aşağıdakine benzer:

```
Attempting to create pool: JobPrepReleaseSamplePool
Created pool JobPrepReleaseSamplePool with 2 nodes
Checking for existing job JobPrepReleaseSampleJob...
Job JobPrepReleaseSampleJob not found, creating...
Submitting tasks and awaiting completion...
All tasks completed.

Contents of shared\job_prep_and_release.txt on tvm-2434664350_1-20160623t173951z:
-------------------------------------------
tvm-2434664350_1-20160623t173951z tasks:
  task001
  task004
  task005
  task006

Contents of shared\job_prep_and_release.txt on tvm-2434664350_2-20160623t173951z:
-------------------------------------------
tvm-2434664350_2-20160623t173951z tasks:
  task008
  task002
  task003
  task007

Waiting for job JobPrepReleaseSampleJob to reach state Completed
...

tvm-2434664350_1-20160623t173951z:
  Prep task exit code:    0
  Release task exit code: 0

tvm-2434664350_2-20160623t173951z:
  Prep task exit code:    0
  Release task exit code: 0

Delete job? [yes] no
yes
Delete pool? [yes] no
yes

Sample complete, hit ENTER to exit...
```

> [!NOTE]
> Yeni bir havuzdaki düğümlerin değişken oluşturma ve başlangıç saati nedeniyle (bazı düğümler diğer öğelerden önce görevler için hazırlanıyor), farklı bir çıktı görebilirsiniz. Özellikle, görevler hızlı bir şekilde tamamlandığından, havuzun düğümlerinden biri iş görevlerinin tümünü yürütebilir. Bu durumda, iş hazırlama ve bırakma görevlerinin hiçbir görev yürüten düğüm için mevcut olmadığını fark edeceksiniz.
> 
> 

### <a name="inspect-job-preparation-and-release-tasks-in-the-azure-portal"></a>Azure portal iş hazırlama ve sürüm görevlerini inceleyin
Örnek uygulamayı çalıştırdığınızda, işin ve görevlerinin özelliklerini görüntülemek için [Azure Portal][portal] kullanabilir veya hatta iş görevleri tarafından değiştirilen paylaşılan metin dosyasını indirebilirsiniz.

Aşağıdaki ekran görüntüsünde, örnek uygulamanın çalıştırılmasından sonra Azure portal **hazırlık görevleri dikey** penceresi gösterilmektedir. Görevlerinizi tamamladıktan sonra (ancak iş ve havuzunuzu silmeden önce) *Jobprepreleasesamplejob* özelliklerine gidin ve özelliklerini görüntülemek için **hazırlık görevleri** veya **Sürüm görevleri** ' ne tıklayın.

![Azure portal iş hazırlama özellikleri][1]

## <a name="next-steps"></a>Sonraki adımlar
### <a name="application-packages"></a>Uygulama paketleri
İş hazırlama görevinin yanı sıra, görev yürütme için işlem düğümlerini hazırlamak üzere Batch 'in [uygulama paketleri](batch-application-packages.md) özelliğini de kullanabilirsiniz. Bu özellik özellikle bir yükleyici çalıştırmak gerektirmeyen uygulamaları, çok sayıda (100 +) dosyayı içeren uygulamaları veya katı sürüm denetimi gerektiren uygulamaları dağıtmak için yararlıdır.

### <a name="installing-applications-and-staging-data"></a>Uygulamaları yükleme ve verileri hazırlama
Bu MSDN Forum gönderisi, düğümleri çalışan görevler için hazırlama yöntemlerine ilişkin bir genel bakış sağlar:

[Toplu işlem düğümlerinde uygulamalar ve hazırlama verileri yükleme][forum_post]

Azure Batch takım üyelerinden biri tarafından yazılan, işlem düğümlerine uygulama ve veri dağıtmak için kullanabileceğiniz çeşitli teknikler ele alınmaktadır.

[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_listjobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[api_rest]: https://msdn.microsoft.com/library/azure/dn820158.aspx
[azure_storage]: https://azure.microsoft.com/services/storage/
[portal]: https://portal.azure.com
[job_prep_release_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/JobPrepRelease
[forum_post]: https://social.msdn.microsoft.com/Forums/en-US/87b19671-1bdf-427a-972c-2af7e5ba82d9/installing-applications-and-staging-data-on-batch-compute-nodes?forum=azurebatch
[net_batch_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudjob]:https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_job_prep]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobpreparationtask.aspx
[net_job_prep_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobpreparationtask.aspx
[net_job_prep_resourcefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobpreparationtask.resourcefiles.aspx
[net_job_delete]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.deletejobasync.aspx
[net_job_terminate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.terminatejobasync.aspx
[net_job_release]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobreleasetask.aspx
[net_job_release_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobreleasetask.aspx
[pool_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.starttask.aspx

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

[1]: ./media/batch-job-prep-release/portal-jobprep-01.png
