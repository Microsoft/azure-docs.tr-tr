---
title: Azure Machine Learning işlem hatlarını tetikleme
titleSuffix: Azure Machine Learning
description: Tetiklenen işlem hatları, veri işleme, eğitim ve izleme gibi rutin, zaman alan görevleri otomatikleştirmenizi sağlar.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: laobri
author: lobrien
ms.date: 01/29/2021
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 3ecf4458b052f4fdc0eb2e6e697b0468c71ce9c2
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102519665"
---
# <a name="trigger-machine-learning-pipelines"></a>Makine öğrenimi işlem hatlarını tetikleme

Bu makalede, Azure 'da çalışması için programlı olarak bir işlem hattı zamanlamayı öğreneceksiniz. Geçen süreyi veya dosya sistemi değişikliklerini temel alarak bir zamanlama oluşturabilirsiniz. Zaman tabanlı zamanlamalar, veri kayması için izleme gibi rutin görevlerden yararlanmak için kullanılabilir. Değişiklik tabanlı zamanlamalar, karşıya yüklenen yeni veriler veya düzenlenmekte olan eski veriler gibi düzensiz veya öngörülemeyen değişikliklere tepki vermek için kullanılabilir. Zamanlama oluşturmayı öğrendikten sonra, bunların nasıl alınacağını ve devre dışı bırakılacağı hakkında bilgi edineceksiniz. Son olarak, işlem hatlarını çalıştırmak için diğer Azure hizmetlerini, Azure mantıksal uygulaması ve Azure Data Factory kullanmayı öğreneceksiniz. Azure mantıksal uygulaması, daha karmaşık tetikleyici Logic veya davranışına olanak sağlar. Azure Data Factory işlem hatları, daha büyük bir veri düzenleme işlem hattının parçası olarak makine öğrenimi işlem hattını aramanızı sağlar.

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği. Azure aboneliğiniz yoksa [ücretsiz bir hesap](https://aka.ms/AMLFree)oluşturun.

* Python için Azure Machine Learning SDK 'nın yüklendiği bir Python ortamı. Daha fazla bilgi için bkz [. eğitim ve dağıtım için yeniden kullanılabilir ortamları oluşturma ve yönetme Azure Machine Learning.](how-to-use-environments.md)

* Yayımlanmış bir işlem hattına sahip Machine Learning çalışma alanı. [Azure MACHINE LEARNING SDK ile makine öğrenimi işlem hatları oluşturma ve çalıştırma](./how-to-create-machine-learning-pipelines.md)sırasında yerleşik bir yapılandırma kullanabilirsiniz.

## <a name="trigger-pipelines-with-azure-machine-learning-sdk-for-python"></a>Python için Azure Machine Learning SDK ile işlem hatlarını tetikleme

Bir işlem hattını zamanlamak için çalışma alanınıza bir başvuru, yayımlanan işlem hattının tanımlayıcısı ve zamanlamayı oluşturmak istediğiniz denemenin adı gerekir. Aşağıdaki kodla bu değerleri alabilirsiniz:

```Python
import azureml.core
from azureml.core import Workspace
from azureml.pipeline.core import Pipeline, PublishedPipeline
from azureml.core.experiment import Experiment

ws = Workspace.from_config()

experiments = Experiment.list(ws)
for experiment in experiments:
    print(experiment.name)

published_pipelines = PublishedPipeline.list(ws)
for published_pipeline in  published_pipelines:
    print(f"{published_pipeline.name},'{published_pipeline.id}'")

experiment_name = "MyExperiment" 
pipeline_id = "aaaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee" 
```

## <a name="create-a-schedule"></a>Zamanlama Oluştur

Bir işlem hattını yineleme temelinde çalıştırmak için bir zamanlama oluşturacaksınız. Bir işlem hattını `Schedule` , denemeyi ve bir tetikleyiciyi ilişkilendirir. Tetikleyici, `ScheduleRecurrence` çalışma veya değişiklik için izlenecek bir dizin belirten bir veri deposu yolu arasındaki beklemeyi açıklayan bir olabilir. Her iki durumda da, işlem hattı tanımlayıcısı ve zamanlamanın oluşturulacağı denemenin adı gerekir.

Python dosyanızın en üstünde, `Schedule` ve sınıflarını içeri aktarın `ScheduleRecurrence` :

```python

from azureml.pipeline.core.schedule import ScheduleRecurrence, Schedule
```

### <a name="create-a-time-based-schedule"></a>Zamana dayalı zamanlama oluşturma

`ScheduleRecurrence`Oluşturucunun, `frequency` Şu dizelerden biri olması gereken bir bağımsız değişkeni vardır: "Minute", "Hour", "Day", "Week" veya "month". Ayrıca `interval` , zamanlamanın ne kadar süre sonra geçmesi gerektiğini belirten bir tamsayı bağımsız değişkeni gerektirir `frequency` . İsteğe bağlı bağımsız değişkenler, [SCHEDULERECURRENCE SDK belgeleri](/python/api/azureml-pipeline-core/azureml.pipeline.core.schedule.schedulerecurrence)içinde açıklandığı gibi başlangıç zamanları hakkında daha fazla bilgi sağlar.

`Schedule`Her 15 dakikada bir çalışmayı başlatan bir oluştur:

```python
recurrence = ScheduleRecurrence(frequency="Minute", interval=15)
recurring_schedule = Schedule.create(ws, name="MyRecurringSchedule", 
                            description="Based on time",
                            pipeline_id=pipeline_id, 
                            experiment_name=experiment_name, 
                            recurrence=recurrence)
```

### <a name="create-a-change-based-schedule"></a>Değişiklik tabanlı zamanlama oluşturma

Dosya değişiklikleri tarafından tetiklenen işlem hatları, zaman tabanlı zamanlamalardan daha verimli olabilir. Bir dosya değiştirilmeden önce bir şey yapmak istediğinizde veya bir veri dizinine yeni bir dosya eklendiğinde, bu dosyayı önceden işleyebilirsiniz. Veri deposundaki herhangi bir değişikliği veya veri deposu içindeki belirli bir dizin içindeki değişiklikleri izleyebilirsiniz. Belirli bir dizini izlemenize sonra, bu dizinin alt dizinlerindeki değişiklikler bir çalıştırmayı _tetiklemez_ .

Bir dosya-reaktif oluşturmak için `Schedule` , `datastore` çağır [. Create](/python/api/azureml-pipeline-core/azureml.pipeline.core.schedule.schedule#create-workspace--name--pipeline-id--experiment-name--recurrence-none--description-none--pipeline-parameters-none--wait-for-provisioning-false--wait-timeout-3600--datastore-none--polling-interval-5--data-path-parameter-name-none--continue-on-step-failure-none--path-on-datastore-none---workflow-provider-none---service-endpoint-none-)çağrısında parametresini ayarlamanız gerekir. Bir klasörü izlemek için `path_on_datastore` bağımsız değişkenini ayarlayın.

`polling_interval`Bağımsız değişkeni, veri deposunun değişiklikler için denetlenme sıklığını dakika cinsinden belirtmenize olanak tanır.

İşlem hattı bir [DataPath](/python/api/azureml-core/azureml.data.datapath.datapath) [pipelineparametresiyle](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelineparameter)oluşturulmuşsa, bağımsız değişkenini ayarlayarak bu değişkeni değiştirilen dosyanın adı olarak ayarlayabilirsiniz `data_path_parameter_name` .

```python
datastore = Datastore(workspace=ws, name="workspaceblobstore")

reactive_schedule = Schedule.create(ws, name="MyReactiveSchedule", description="Based on input file change.",
                            pipeline_id=pipeline_id, experiment_name=experiment_name, datastore=datastore, data_path_parameter_name="input_data")
```

### <a name="optional-arguments-when-creating-a-schedule"></a>Zamanlama oluştururken isteğe bağlı bağımsız değişkenler

Daha önce ele alınan bağımsız değişkenlere ek olarak, `status` bağımsız değişkenini `"Disabled"` devre dışı bir zamanlama oluşturacak şekilde ayarlayabilirsiniz. Son olarak, `continue_on_step_failure` işlem hattının varsayılan hata davranışını geçersiz kılacak bir Boole değeri geçirmenize olanak sağlar.

## <a name="view-your-scheduled-pipelines"></a>Zamanlanan işlem hatlarınızı görüntüleyin

Web tarayıcınızda Azure Machine Learning ' a gidin. Gezinti bölmesinin **uç noktalar** bölümünde **ardışık düzen uç noktaları**' nı seçin. Bu sizi çalışma alanında yayınlanan işlem hatları listesine götürür.

:::image type="content" source="./media/how-to-trigger-published-pipeline/scheduled-pipelines.png" alt-text="AML ardışık düzenleri sayfası":::

Bu sayfada, çalışma alanındaki tüm işlem hatları hakkında özet bilgileri görebilirsiniz: adlar, açıklamalar, durum ve benzeri. İşlem hattınızı tıklatarak detaya gidin. Sonuç sayfasında, işlem hatınızla ilgili daha fazla ayrıntı vardır ve tek tek çalıştırmalar üzerinde ayrıntıya gidebilirsiniz.

## <a name="deactivate-the-pipeline"></a>İşlem hattını devre dışı bırakma

`Pipeline`Yayımlanmış ancak zamanlanmamış bir ' a sahipseniz, şu ile devre dışı bırakabilirsiniz:

```python
pipeline = PublishedPipeline.get(ws, id=pipeline_id)
pipeline.disable()
```

İşlem hattı zamanlanırsa, önce zamanlamayı iptal etmeniz gerekir. Zamanlamadan veya çalıştırarak zamanlamanın tanımlayıcısını alın:

```python
ss = Schedule.list(ws)
for s in ss:
    print(s)
```

`schedule_id`Devre dışı bırakmak istediğiniz bir kez daha çalıştırın:

```python
def stop_by_schedule_id(ws, schedule_id):
    s = next(s for s in Schedule.list(ws) if s.id == schedule_id)
    s.disable()
    return s

stop_by_schedule_id(ws, schedule_id)
```

Daha sonra yeniden çalıştırırsanız `Schedule.list(ws)` boş bir liste almanız gerekir.

## <a name="use-azure-logic-apps-for-complex-triggers"></a>Karmaşık Tetikleyiciler için Azure Logic Apps kullanma 

[Azure mantıksal uygulaması](../logic-apps/logic-apps-overview.md)kullanılarak daha karmaşık tetikleyici kuralları veya davranışları oluşturulabilir.

Bir Machine Learning işlem hattını tetiklemek üzere bir Azure mantıksal uygulaması kullanmak için, yayımlanan bir Machine Learning işlem hattı için REST uç noktasına ihtiyacınız olacaktır. İşlem [hattınızı oluşturun ve yayımlayın](./how-to-create-machine-learning-pipelines.md). Ardından, `PublishedPipeline` ardışık düzen kimliğini kullanarak VERILERINIZIN REST uç noktasını bulun:

```python
# You can find the pipeline ID in Azure Machine Learning studio

published_pipeline = PublishedPipeline.get(ws, id="<pipeline-id-here>")
published_pipeline.endpoint 
```

## <a name="create-a-logic-app"></a>Mantıksal Uygulama oluşturma

Şimdi bir [Azure Logic App](../logic-apps/logic-apps-overview.md) örneği oluşturun. İsterseniz, [bir tümleştirme hizmeti ortamı (ISE) kullanın](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) ve mantıksal uygulamanız tarafından kullanılmak üzere [müşteri tarafından yönetilen bir anahtar ayarlayın](../logic-apps/customer-managed-keys-integration-service-environment.md) .

Mantıksal uygulamanız sağlandıktan sonra, işlem hattınızda bir tetikleyiciyi yapılandırmak için aşağıdaki adımları kullanın:

1. Uygulamaya Azure Machine Learning Çalışma Alanı erişimi sağlamak için [sistem tarafından atanan bir yönetilen kimlik oluşturun](../logic-apps/create-managed-service-identity.md) .

1. Logic App Designer görünümü ' ne gidin ve boş mantıksal uygulama şablonunu seçin. 
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="media/how-to-trigger-published-pipeline/blank-template.png" alt-text="Boş şablon":::

1. Tasarımcıda **BLOB**' u arayın. **Bir blob eklendiğinde veya değiştirildiğinde (yalnızca Özellikler)** tetikleyicisi ' ni seçin ve bu tetikleyiciyi mantıksal uygulamanıza ekleyin.
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="media/how-to-trigger-published-pipeline/add-trigger.png" alt-text="Tetikleyici ekleme":::

1. Blob ekleme veya değişiklik için izlemek istediğiniz BLOB depolama hesabı için bağlantı bilgilerini girin. İzlenecek kapsayıcıyı seçin. 
 
    Sizin için çalışan güncelleştirmeleri yoklamaya yönelik **aralığı** ve **sıklığı** seçin.  

    > [!NOTE]
    > Bu tetikleyici seçili kapsayıcıyı izler, ancak alt klasörleri izlemez.

1. Yeni veya değiştirilmiş bir blob algılandığında çalışacak bir HTTP eylemi ekleyin. **+ Yeni adım**' ı seçin ve ardından http eylemini arayın ve seçin.

  > [!div class="mx-imgBorder"]
  > :::image type="content" source="media/how-to-trigger-published-pipeline/search-http.png" alt-text="HTTP eylemi ara":::

  Eyleminizi yapılandırmak için aşağıdaki ayarları kullanın:

  | Ayar | Değer | 
  |---|---|
  | HTTP eylemi | POST |
  | URI |bir [Önkoşul](#prerequisites) olarak bulduğunuz yayınlanan işlem hattının bitiş noktası |
  | Kimlik doğrulaması modu | Yönetilen Kimlik |

1. Sahip olduğunuz herhangi bir [DataPath Pipelineparametrelerinin](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-showcasing-datapath-and-pipelineparameter.ipynb) değerini ayarlamak için zamanlamanızı ayarlayın:

    ```json
    {
      "DataPathAssignments": {
        "input_datapath": {
          "DataStoreName": "<datastore-name>",
          "RelativePath": "@{triggerBody()?['Name']}" 
        }
      },
      "ExperimentName": "MyRestPipeline",
      "ParameterAssignments": {
        "input_string": "sample_string3"
      },
      "RunSource": "SDK"
    }
    ```

    `DataStoreName`Çalışma alanınıza bir [Önkoşul](#prerequisites)olarak eklediğiniz öğesini kullanın.
     
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="media/how-to-trigger-published-pipeline/http-settings.png" alt-text="HTTP ayarları":::

1. **Kaydet** ' i seçin ve zamanlamanız artık hazırdır.

> [!IMPORTANT]
> İşlem hattınızı erişimi yönetmek için Azure rol tabanlı erişim denetimi (Azure RBAC) kullanıyorsanız, işlem [hattı senaryonuz (eğitim veya Puanlama) için izinleri ayarlayın](how-to-assign-roles.md#common-scenarios).

## <a name="call-machine-learning-pipelines-from-azure-data-factory-pipelines"></a>Azure Data Factory işlem hatlarından makine öğrenimi işlem hatlarını çağırma

Azure Data Factory işlem hattında, Machine Learning işlem *hattı yürütme* etkinliği bir Azure Machine Learning işlem hattı çalıştırır. Bu etkinliği *Machine Learning* kategorisinin Data Factory yazma sayfasında bulabilirsiniz:

:::image type="content" source="media/how-to-trigger-published-pipeline/azure-data-factory-pipeline-activity.png" alt-text="Azure Data Factory yazma ortamında ML işlem hattı etkinliğini gösteren ekran görüntüsü":::

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, bir işlem hattını iki farklı şekilde zamanlamak için Python için Azure Machine Learning SDK 'sını kullandınız. Bir zamanlama geçen saat zamanına göre yinelenir. Diğer zamanlama, belirtilen bir dosya `Datastore` veya bu depodaki bir dizin içinde değiştirilirse çalışır. İşlem hattını ve bireysel çalıştırmaları incelemek için portalın nasıl kullanılacağını gördünüz. İşlem hattının çalışmayı durdurması için zamanlamayı nasıl devre dışı bırakakullanacağınızı öğrendiniz. Son olarak, bir işlem hattının tetiklenmesi için bir Azure mantıksal uygulaması oluşturdunuz. 

Daha fazla bilgi için bkz.

> [!div class="nextstepaction"]
> [Batch Puanlama için Azure Machine Learning işlem hatları kullanma](tutorial-pipeline-batch-scoring-classification.md)

* İşlem [hatları](concept-ml-pipelines.md) hakkında daha fazla bilgi
* [Jupyıter ile Azure Machine Learning araştırma](samples-notebooks.md) hakkında daha fazla bilgi edinin