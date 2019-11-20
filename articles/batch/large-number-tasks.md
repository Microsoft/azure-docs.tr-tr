---
title: Çok sayıda görev gönderme-Azure Batch | Microsoft Docs
description: Tek bir Azure Batch işinde çok fazla sayıda görevi verimli bir şekilde gönderme
services: batch
documentationcenter: ''
author: laurenhughes
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 08/24/2018
ms.author: lahugh
ms.custom: ''
ms.openlocfilehash: fc47b18dd51bbaa48d950515cccfe618d9e58426
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/16/2019
ms.locfileid: "74132761"
---
# <a name="submit-a-large-number-of-tasks-to-a-batch-job"></a>Bir Batch işine çok sayıda görev gönderme

Büyük ölçekli Azure Batch iş yüklerini çalıştırdığınızda, tek bir işte on binlerce, yüzlerce binlerce veya hatta daha fazla görev göndermek isteyebilirsiniz. 

Bu makale, çok sayıda görevi tek bir Batch işine göre önemli ölçüde artan bir şekilde göndermek için rehberlik ve bazı kod örnekleri sunar. Görevler gönderildikten sonra, iş için belirttiğiniz havuzda işlenmek üzere toplu Işlem kuyruğu girer.

## <a name="use-task-collections"></a>Görev koleksiyonlarını kullanma

Batch API 'Leri, bir işi tek seferde bir *koleksiyona bir koleksiyon*olarak eklemek için yöntemler sağlar. Çok sayıda görev eklerken, görevleri koleksiyon olarak eklemek için uygun yöntemleri veya aşırı yüklemeleri kullanmanız gerekir. Genellikle, işiniz için bir giriş dosyası veya parametre kümesi üzerinde yineleme yaparken görevleri tanımlayarak görev koleksiyonu oluşturursunuz.

Tek bir çağrıda ekleyebileceğiniz görev koleksiyonunun en büyük boyutu, kullandığınız Batch API 'sine bağlıdır:

* Aşağıdaki Batch API 'Leri, koleksiyonu **100 görevle**sınırlandırır. Sınır, görevlerin boyutuna bağlı olarak daha küçük olabilir. Örneğin, görevlerde çok sayıda kaynak dosyası veya ortam değişkeni varsa.

    * [REST API](/rest/api/batchservice/task/addcollection)
    * [Python API’si](/python/api/azure-batch/azure.batch.operations.TaskOperations?view=azure-python)
    * [Node.js API’si](/javascript/api/@azure/batch/task?view=azure-node-latest)

  Bu API 'Leri kullanırken, görev sayısını koleksiyon sınırını karşılayacak şekilde bölmek ve görevlerin eklenmesi durumunda hataları ve yeniden denemeleri işlemek için mantık sağlamanız gerekir. Bir görev koleksiyonu eklemek için çok büyükse istek bir hata oluşturur ve daha az görevle yeniden denenmelidir.

* Aşağıdaki API 'Ler, gönderim istemcisinde yalnızca RAM kullanılabilirliğiyle sınırlı olan çok daha büyük görev koleksiyonlarını destekler. Bu API 'Ler, görev koleksiyonunu alt düzey API 'Ler için "parçalara ayırır" ve görevlerin eklenmesi başarısız olursa yeniden denemeler için saydam bir şekilde işler.

    * [.NET API’si](/dotnet/api/microsoft.azure.batch.cloudjob.addtaskasync?view=azure-dotnet)
    * [Java API’si](/java/api/com.microsoft.azure.batch.protocol.tasks.addcollectionasync?view=azure-java-stable)
    * Batch CLı şablonlarıyla [Azure Batch CLI uzantısı](batch-cli-templates.md)
    * [Python SDK uzantısı](https://pypi.org/project/azure-batch-extensions/)

## <a name="increase-throughput-of-task-submission"></a>Görev gönderimi verimini artırma

Bir işe büyük bir görev koleksiyonu eklemek biraz zaman alabilir. Örneğin, .NET API aracılığıyla 20.000 görevi eklemek için en fazla 1 dakikalık bir işlem olabilir. Batch API 'sine ve iş yüküne bağlı olarak, aşağıdakilerden birini veya birkaçını değiştirerek görev aktarım hızını geliştirebilirsiniz:

* **Görev boyutu** -büyük görevleri ekleme daha küçük olanlar eklemekten daha uzun sürer. Bir koleksiyondaki her görevin boyutunu azaltmak için, görev komut satırını basitleştirebilir, ortam değişkenlerinin sayısını azaltabilir veya görev yürütme gereksinimlerini daha verimli bir şekilde işleyebilirsiniz. Örneğin, çok sayıda kaynak dosyası kullanmak yerine, havuzdaki bir [Başlangıç görevini](batch-api-basics.md#start-task) kullanarak görev bağımlılıklarını yükler veya bir [uygulama paketi](batch-application-packages.md) veya [Docker kapsayıcısı](batch-docker-container-workloads.md)kullanın.

* **Paralel Işlem sayısı** -Batch API 'sine bağlı olarak, Batch istemcisi tarafından en fazla eşzamanlı işlem sayısını artırarak aktarım hızını artırın. Bu ayarı, .NET API 'sindeki [BatchClientParallelOptions. Maxdegreeofparalellik](/dotnet/api/microsoft.azure.batch.batchclientparalleloptions.maxdegreeofparallelism) özelliğini veya Batch Python SDK uzantısında [taskoperations. add_collection](/python/api/azure-batch/azure.batch.operations.TaskOperations?view=azure-python) gibi yöntemlerin `threads` parametresini kullanarak yapılandırın. (Bu özellik yerel Batch Python SDK 'sında kullanılamaz.) Varsayılan olarak, bu özellik 1 olarak ayarlanır, ancak işlem verimini geliştirmek için daha yüksek ayarlanır. Ağ bant genişliğini ve bazı CPU performansını tüketerek artan aktarım hızına sahip olursunuz. Görev işleme hızı, `MaxDegreeOfParallelism` veya `threads`100 katına kadar artar. Uygulamada, 100 altındaki eşzamanlı işlem sayısını ayarlamanız gerekir. 
 
  Batch şablonları ile Azure Batch CLı uzantısı, kullanılabilir çekirdek sayısına bağlı olarak eşzamanlı işlem sayısını otomatik olarak arttırır, ancak bu özellik CLı 'da yapılandırılamaz. 

* **Http bağlantı limitleri** -eşzamanlı http bağlantısı sayısı, çok sayıda görev eklerken Batch istemcisinin performansını kısıtlayabilir. HTTP bağlantılarının sayısı belirli API 'lerle sınırlıdır. .NET API ile geliştirilirken, örneğin, [ServicePointManager. DefaultConnectionLimit](/dotnet/api/system.net.servicepointmanager.defaultconnectionlimit) özelliği varsayılan olarak 2 olarak ayarlanır. Sayıyı, paralel işlem sayısından daha fazla veya daha büyük bir sayıya artırmanız önerilir.

## <a name="example-batch-net"></a>Örnek: Batch .NET

Aşağıdaki C# kod parçacıkları, Batch .NET API 'si kullanılarak çok sayıda görev eklenirken yapılandırılacak ayarları gösterir.

Görev aktarım hızını artırmak için [Batchclient](/dotnet/api/microsoft.azure.batch.batchclient?view=azure-dotnet)'ın [Maxdegreeofparallelilik](/dotnet/api/microsoft.azure.batch.batchclientparalleloptions.maxdegreeofparallelism) özelliğinin değerini artırın. Örneğin:

```csharp
BatchClientParallelOptions parallelOptions = new BatchClientParallelOptions()
  {
    MaxDegreeOfParallelism = 15
  };
...
```
[Addtaskasync](/dotnet/api/microsoft.azure.batch.cloudjob.addtaskasync?view=azure-dotnet) veya [AddTask](/dotnet/api/microsoft.azure.batch.cloudjob.addtask?view=azure-dotnet
) yönteminin uygun aşırı yüklemesini kullanarak işe bir görev koleksiyonu ekleyin. Örneğin:

```csharp
// Add a list of tasks as a collection
List<CloudTask> tasksToAdd = new List<CloudTask>(); // Populate with your tasks
...
await batchClient.JobOperations.AddTaskAsync(jobId, tasksToAdd, parallelOptions);
```


## <a name="example-batch-cli-extension"></a>Örnek: Batch CLı uzantısı

[Batch CLI şablonlarıyla](batch-cli-templates.md)Azure Batch CLI uzantılarını kullanarak, bir [görev fabrikası](https://github.com/Azure/azure-batch-cli-extensions/blob/master/doc/taskFactories.md)IÇEREN bir iş şablonu JSON dosyası oluşturun. Görev fabrikası, tek bir görev tanımındaki bir iş için ilgili görevlerin bir koleksiyonunu yapılandırır.  

Aşağıda, çok sayıda görev içeren tek boyutlu bir parametrik tarama işi için örnek bir iş şablonu verilmiştir; Bu durumda 250.000. Görev komut satırı basit bir `echo` komuttur.

```json
{
    "job": {
        "type": "Microsoft.Batch/batchAccounts/jobs",
        "apiVersion": "2016-12-01",
        "properties": {
            "id": "myjob",
            "constraints": {
                "maxWallClockTime": "PT5H",
                "maxTaskRetryCount": 1
            },
            "poolInfo": {
                "poolId": "mypool"
            },
            "taskFactory": {
                "type": "parametricSweep",
                "parameterSets": [
                    {
                        "start": 1,
                        "end": 250000,
                        "step": 1
                    }
                ],
                "repeatTask": {
                    "commandLine": "/bin/bash -c 'echo Hello world from task {0}'",
                    "constraints": {
                        "retentionTime":"PT1H"
                    }
                }
            },
            "onAllTasksComplete": "terminatejob"
        }
    }
}
```
Şablonuyla bir işi çalıştırmak için bkz. [CLI şablonlarını Azure Batch ve dosya aktarımını kullanma](batch-cli-templates.md).

## <a name="example-batch-python-sdk-extension"></a>Örnek: Batch Python SDK uzantısı

Azure Batch Python SDK uzantısını kullanmak için önce Python SDK 'Yı ve uzantıyı yüklemeniz gerekir:

```
pip install azure-batch
pip install azure-batch-extensions
```

SDK uzantısını kullanan bir `BatchExtensionsClient` ayarlayın:

```python

client = batch.BatchExtensionsClient(
    base_url=BATCH_ACCOUNT_URL, resource_group=RESOURCE_GROUP_NAME, batch_account=BATCH_ACCOUNT_NAME)
...
```

Bir işe eklemek için bir görev koleksiyonu oluşturun. Örneğin:


```python
tasks = list()
# Populate the list with your tasks
...
```

Task [. add_collection](/python/api/azure-batch/azure.batch.operations.TaskOperations?view=azure-python)kullanarak görev koleksiyonu ekleyin. Eşzamanlı işlemlerin sayısını artırmak için `threads` parametresini ayarlayın:

```python
try:
    client.task.add_collection(job_id, threads=100)
except Exception as e:
    raise e
```

Batch Python SDK uzantısı, bir görev fabrikasının JSON belirtimini kullanarak işe görev parametreleri eklemeyi de destekler. Örneğin, önceki Batch CLı şablonu örneğinde olduğu gibi bir parametrik tarama için iş parametrelerini yapılandırın:

```python
parameter_sweep = {
    "job": {
        "type": "Microsoft.Batch/batchAccounts/jobs",
        "apiVersion": "2016-12-01",
        "properties": {
            "id": "myjob",
            "poolInfo": {
                "poolId": "mypool"
            },
            "taskFactory": {
                "type": "parametricSweep",
                "parameterSets": [
                    {
                        "start": 1,
                        "end": 250000,
                        "step": 1
                    }
                ],
                "repeatTask": {
                    "commandLine": "/bin/bash -c 'echo Hello world from task {0}'",
                    "constraints": {
                        "retentionTime": "PT1H"
                    }
                }
            },
            "onAllTasksComplete": "terminatejob"
        }
    }
}
...
job_json = client.job.expand_template(parameter_sweep)
job_parameter = client.job.jobparameter_from_json(job_json)
```

İş parametrelerini işe ekleyin. Eşzamanlı işlemlerin sayısını artırmak için `threads` parametresini ayarlayın:

```python
try:
    client.job.add(job_parameter, threads=50)
except Exception as e:
    raise e
```

## <a name="next-steps"></a>Sonraki adımlar

* [Batch CLI şablonlarıyla](batch-cli-templates.md)Azure Batch CLI uzantısını kullanma hakkında daha fazla bilgi edinin.
* [Batch Python SDK uzantısı](https://pypi.org/project/azure-batch-extensions/)hakkında daha fazla bilgi edinin.
