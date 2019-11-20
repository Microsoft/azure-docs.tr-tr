---
title: Machine Learning işlem hattı YAML
titleSuffix: Azure Machine Learning
description: Bir YAML dosyası kullanarak makine öğrenimi ardışık düzeni tanımlama hakkında bilgi edinin. YAML ardışık düzen tanımları, Azure CLı için Machine Learning Uzantısı ile birlikte kullanılır.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: sanpil
author: sanpil
ms.date: 11/11/2019
ms.openlocfilehash: 474a184b24ca3318a33adb89b25640939a814474
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73929505"
---
# <a name="define-machine-learning-pipelines-in-yaml"></a>YAML 'de makine öğrenimi işlem hatlarını tanımlama

[YAML](https://yaml.org/)'de makine öğrenimi ardışık düzenleri tanımlama hakkında bilgi edinin. Azure CLı için Machine Learning uzantısını kullanırken, işlem hattı ile ilgili komutların birçoğu, işlem hattını tanımlayan bir YAML dosyası bekler.

Aşağıdaki tabloda, YAML 'de bir işlem hattı tanımlarken ne olduğu ve şu anda desteklenmeyen listelenmektedir:

| Adım türü | Destekleniyor mu? |
| ----- | :-----: |
| PythonScriptStep | Yes |
| AdlaStep | Yes |
| AzureBatchStep | Yes |
| DatabricksStep | Yes |
| DataTransferStep | Yes |
| Oto Mlstep | Hayır |
| HyperDriveStep | Hayır |
| ModuleStep | Hayır |
| MPIStep | Hayır |
| EstimatorStep | Hayır |

## <a name="pipeline-definition"></a>İşlem hattı tanımı

İşlem hattı tanımı, işlem [hatları](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline.pipeline?view=azure-ml-py) sınıfına karşılık gelen aşağıdaki anahtarları kullanır:

| YAML anahtarı | Açıklama |
| ----- | ----- |
| `name` | İşlem hattının açıklaması. |
| `parameters` | İşlem hattının parametreleri. |
| `data_reference` | Verilerin bir çalıştırmada nasıl ve nerede kullanılabilir yapılacağını tanımlar. |
| `default_compute` | İşlem hattının tüm adımlarının çalıştırıldığı varsayılan işlem hedefi. |
| `steps` | İşlem hattında kullanılan adımlar. |

## <a name="parameters"></a>Parametreler

`parameters` bölümü, [Pipelineparameter](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelineparameter?view=azure-ml-py) sınıfına karşılık gelen aşağıdaki anahtarları kullanır:

| YAML anahtarı | Açıklama |
| ---- | ---- |
| `type` | Parametrenin değer türü. Geçerli türler `string`, `int`, `float`, `bool`veya `datapath`. |
| `default` | Varsayılan değer. |

Her parametre olarak adlandırılır. Örneğin, aşağıdaki YAML kod parçacığı `NumIterationsParameter`, `DataPathParameter`ve `NodeCountParameter`adlı üç parametreyi tanımlar:

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        NumIterationsParameter:
            type: int
            default: 40
        DataPathParameter:
            type: datapath
            default:
                datastore: workspaceblobstore
                path_on_datastore: sample2.txt
        NodeCountParameter:
            type: int
            default: 4
```

## <a name="data-reference"></a>Veri başvurusu

`data_references` bölümü, [DataReference](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py)öğesine karşılık gelen aşağıdaki anahtarları kullanır:

| YAML anahtarı | Açıklama |
| ----- | ----- |
| `datastore` | Başvurulacak veri deposu. |
| `path_on_datastore` | Veri başvurusu için yedekleme depolama alanındaki göreli yol. |

Her veri başvurusu bir anahtarda yer alır. Örneğin, aşağıdaki YAML kod parçacığı `employee_data`adlı anahtarda depolanan bir veri başvurusunu tanımlar:

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        employee_data:
            datastore: adftestadla
            path_on_datastore: "adla_sample/sample_input.csv"
```

## <a name="steps"></a>Adımlar

Adımlar, ortamda çalıştırılacak dosyalarla birlikte bir hesaplama ortamı tanımlar. Bir adımın türünü tanımlamak için `type` anahtarını kullanın:

| Adım türü | Açıklama |
| ----- | ----- |
| `AdlaStep` | Azure Data Lake Analytics ile bir U-SQL betiği çalıştırır. [Adlastep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.adlastep?view=azure-ml-py) sınıfına karşılık gelir. |
| `AzureBatchStep` | İşleri Azure Batch kullanarak çalıştırır. [AzureBatchStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.azurebatchstep?view=azure-ml-py) sınıfına karşılık gelir. |
| `DatabricsStep` | Bir Databricks Not defteri, Python betiği veya JAR ekler. [Databricksstep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricksstep?view=azure-ml-py) sınıfına karşılık gelir. |
| `DataTransferStep` | Verileri depolama seçenekleri arasında aktarır. [Datatransferstep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?view=azure-ml-py) sınıfına karşılık gelir. |
| `PythonScriptStep` | Bir Python betiği çalıştırır. [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py) sınıfına karşılık gelir. |

### <a name="adla-step"></a>ADLA adımı

| YAML anahtarı | Açıklama |
| ----- | ----- |
| `script_name` | U-SQL betiğinin adı (`source_directory`göreli). |
| `compute_target` | Bu adım için kullanılacak işlem hedefi Azure Data Lake. |
| `parameters` | İşlem hattının [parametreleri](#parameters) . |
| `inputs` | Girişler [ınputportbinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?view=azure-ml-py), [DataReference](#data-reference), [portdatareference](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?view=azure-ml-py), [pipelinedata](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py), [DataSet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py), [DataSetDefinition](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py)veya [pipelinedataset](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py)olabilir. |
| `outputs` | Çıkışlar, [Pipelinedata](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) ya da [outputportbinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py)olabilir. |
| `source_directory` | Betiği, derlemeleri vb. içeren dizin. |
| `priority` | Geçerli iş için kullanılacak öncelik değeri. |
| `params` | Ad-değer çiftleri sözlüğü. |
| `degree_of_parallelism` | Bu iş için kullanılacak paralellik derecesi. |
| `runtime_version` | Data Lake Analytics altyapısının çalışma zamanı sürümü. |
| `allow_reuse` | Aynı ayarlarla yeniden çalıştırıldığında adımın önceki sonuçları yeniden kullanıp kullanmayacağını belirler. |

Aşağıdaki örnek bir ADLA adım tanımı içerir:

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        employee_data:
            datastore: adftestadla
            path_on_datastore: "adla_sample/sample_input.csv"
    default_compute: adlacomp
    steps:
        Step1:
            runconfig: "D:\\Yaml\\default_runconfig.yml"
            parameters:
                NUM_ITERATIONS_2:
                    source: PipelineParam1
                NUM_ITERATIONS_1: 7
            type: "AdlaStep"
            name: "MyAdlaStep"
            script_name: "sample_script.usql"
            source_directory: "D:\\scripts\\Adla"
            inputs:
                employee_data:
                    source: employee_data
            outputs:
                OutputData:
                    destination: Output4
                    datastore: adftestadla
                    bind_mode: mount
```

### <a name="azure-batch-step"></a>Azure Batch adımı

| YAML anahtarı | Açıklama |
| ----- | ----- |
| `compute_target` | Bu adım için kullanılacak işlem hedefi Azure Batch. |
| `inputs` | Girişler [ınputportbinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?view=azure-ml-py), [DataReference](#data-reference), [portdatareference](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?view=azure-ml-py), [pipelinedata](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py), [DataSet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py), [DataSetDefinition](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py)veya [pipelinedataset](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py)olabilir. |
| `outputs` | Çıkışlar, [Pipelinedata](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) ya da [outputportbinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py)olabilir. |
| `source_directory` | Modül ikililerini içeren dizin, çalıştırılabilir, derlemeler vb. |
| `executable` | Bu işin bir parçası olarak çalıştırılacak komutun/yürütülebilir dosyanın adı. |
| `create_pool` | İşi çalıştırmadan önce havuzun oluşturulup oluşturulmayacağını belirten Boole bayrağı. |
| `delete_batch_job_after_finish` | Işlem tamamlandıktan sonra Batch hesabından işin silinip silineceğini belirten Boole bayrağı. |
| `delete_batch_pool_after_finish` | İş bittikten sonra havuzun silinip silineceğini belirten Boole bayrağı. |
| `is_positive_exit_code_failure` | Görev pozitif bir kodla çıkış yaptığında işin başarısız olup olmadığını belirten Boole bayrağı. |
| `vm_image_urn` | `create_pool` `True`ve VM `VirtualMachineConfiguration`kullanır. |
| `pool_id` | İşin çalıştırılacağı havuzun KIMLIĞI. |
| `allow_reuse` | Aynı ayarlarla yeniden çalıştırıldığında adımın önceki sonuçları yeniden kullanıp kullanmayacağını belirler. |

Aşağıdaki örnek bir Azure Batch adım tanımı içerir:

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        input:
            datastore: workspaceblobstore
            path_on_datastore: "input.txt"
    default_compute: testbatch
    steps:
        Step1:
            runconfig: "D:\\Yaml\\default_runconfig.yml"
            parameters:
                NUM_ITERATIONS_2:
                    source: PipelineParam1
                NUM_ITERATIONS_1: 7
            type: "AzureBatchStep"
            name: "MyAzureBatchStep"
            pool_id: "MyPoolName"
            create_pool: true
            executable: "azurebatch.cmd"
            source_directory: "D:\\scripts\\AureBatch"
            allow_reuse: false
            inputs:
                input:
                    source: input
            outputs:
                output:
                    destination: output
                    datastore: workspaceblobstore
```

### <a name="databricks-step"></a>Databricks adımı

| YAML anahtarı | Açıklama |
| ----- | ----- |
| `compute_target` | Bu adım için kullanılacak işlem hedefi Azure Databricks. |
| `inputs` | Girişler [ınputportbinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?view=azure-ml-py), [DataReference](#data-reference), [portdatareference](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?view=azure-ml-py), [pipelinedata](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py), [DataSet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py), [DataSetDefinition](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py)veya [pipelinedataset](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py)olabilir. |
| `outputs` | Çıkışlar, [Pipelinedata](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) ya da [outputportbinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py)olabilir. |
| `run_name` | Bu çalıştırma için Databricks içindeki ad. |
| `source_directory` | Betiği ve diğer dosyaları içeren dizin. |
| `num_workers` | Databricks kümesi çalıştıran statik çalışan sayısı. |
| `runconfig` | `.runconfig` bir dosyanın yolu. Bu dosya [RunConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py) sınıfının YAML gösterimidir. Bu dosyanın yapısı hakkında daha fazla bilgi için bkz. [runconfigschema. JSON](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json). |
| `allow_reuse` | Aynı ayarlarla yeniden çalıştırıldığında adımın önceki sonuçları yeniden kullanıp kullanmayacağını belirler. |

Aşağıdaki örnek bir Databricks adımını içerir:

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        adls_test_data:
            datastore: adftestadla
            path_on_datastore: "testdata"
        blob_test_data:
            datastore: workspaceblobstore
            path_on_datastore: "dbtest"
    default_compute: mydatabricks
    steps:
        Step1:
            runconfig: "D:\\Yaml\\default_runconfig.yml"
            parameters:
                NUM_ITERATIONS_2:
                    source: PipelineParam1
                NUM_ITERATIONS_1: 7
            type: "DatabricksStep"
            name: "MyDatabrickStep"
            run_name: "DatabricksRun"
            python_script_name: "train-db-local.py"
            source_directory: "D:\\scripts\\Databricks"
            num_workers: 1
            allow_reuse: true
            inputs:
                blob_test_data:
                    source: blob_test_data
            outputs:
                OutputData:
                    destination: Output4
                    datastore: workspaceblobstore
                    bind_mode: mount
```

### <a name="data-transfer-step"></a>Veri aktarımı adımı

| YAML anahtarı | Açıklama |
| ----- | ----- |
| `compute_target` | Bu adım için kullanılacak işlem hedefi Azure Data Factory. |
| `source_data_reference` | Veri aktarımı işlemlerinin kaynağı olarak hizmet veren giriş bağlantısı. Desteklenen değerler [ınputportbinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?view=azure-ml-py), [DataReference](#data-reference), [portdatareference](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?view=azure-ml-py), [pipelinedata](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py), [DataSet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py), [DataSetDefinition](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py)veya [pipelinedataset](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py). |
| `destination_data_reference` | Veri aktarımı işlemlerinin hedefi olarak hizmet veren giriş bağlantısı. Desteklenen değerler, [Pipelinedata](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) ve [outputportbinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py)' dir. |
| `allow_reuse` | Aynı ayarlarla yeniden çalıştırıldığında adımın önceki sonuçları yeniden kullanıp kullanmayacağını belirler. |

Aşağıdaki örnek bir veri aktarımı adımını içerir:

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        adls_test_data:
            datastore: adftestadla
            path_on_datastore: "testdata"
        blob_test_data:
            datastore: workspaceblobstore
            path_on_datastore: "testdata"
    default_compute: adftest
    steps:
        Step1:
            runconfig: "D:\\Yaml\\default_runconfig.yml"
            parameters:
                NUM_ITERATIONS_2:
                    source: PipelineParam1
                NUM_ITERATIONS_1: 7
            type: "DataTransferStep"
            name: "MyDataTransferStep"
            adla_compute_name: adftest
            source_data_reference:
                adls_test_data:
                    source: adls_test_data
            destination_data_reference:
                blob_test_data:
                    source: blob_test_data
```

### <a name="python-script-step"></a>Python betik adımı

| YAML anahtarı | Açıklama |
| ----- | ----- |
| `compute_target` | Bu adım için kullanılacak işlem hedefi. İşlem hedefi bir Azure Machine Learning Işlem, sanal makine (Veri Bilimi VM'si gibi) veya HDInsight olabilir. |
| `inputs` | Girişler [ınputportbinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?view=azure-ml-py), [DataReference](#data-reference), [portdatareference](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?view=azure-ml-py), [pipelinedata](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py), [DataSet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py), [DataSetDefinition](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py)veya [pipelinedataset](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py)olabilir. |
| `outputs` | Çıkışlar, [Pipelinedata](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) ya da [outputportbinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py)olabilir. |
| `script_name` | Python betiğinin adı (`source_directory`göreli). |
| `source_directory` | Betiği içeren dizin, Conda ortamı, vb. |
| `runconfig` | `.runconfig` bir dosyanın yolu. Bu dosya [RunConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py) sınıfının YAML gösterimidir. Bu dosyanın yapısı hakkında daha fazla bilgi için bkz. [runconfig. JSON](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json). |
| `allow_reuse` | Aynı ayarlarla yeniden çalıştırıldığında adımın önceki sonuçları yeniden kullanıp kullanmayacağını belirler. |

Aşağıdaki örnek bir Python betik adımını içerir:

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        DataReference1:
            datastore: workspaceblobstore
            path_on_datastore: testfolder/sample.txt
    default_compute: cpu-cluster
    steps:
        Step1:
            runconfig: "D:\\Yaml\\default_runconfig.yml"
            parameters:
                NUM_ITERATIONS_2:
                    source: PipelineParam1
                NUM_ITERATIONS_1: 7
            type: "PythonScriptStep"
            name: "MyPythonScriptStep"
            script_name: "train.py"
            allow_reuse: True
            source_directory: "D:\\scripts\\PythonScript"
            inputs:
                InputData:
                    source: DataReference1
            outputs:
                OutputData:
                    destination: Output4
                    datastore: workspaceblobstore
                    bind_mode: mount
```

## <a name="schedules"></a>Zamanlamalar

Bir işlem hattı için zamanlamayı tanımlarken, veri deposu tarafından tetiklenen veya bir zaman aralığına göre yinelenen bir değer olabilir. Aşağıda, bir zamanlamayı tanımlamak için kullanılan anahtarlar verilmiştir:

| YAML anahtarı | Açıklama |
| ----- | ----- |
| `description` | Zamanlamanın açıklaması. |
| `recurrence` | Zamanlama yinelense, yinelenme ayarlarını içerir. |
| `pipeline_parameters` | İşlem hattının gerektirdiği tüm parametreler. |
| `wait_for_provisioning` | Zamanlamanın tamamlanmasını beklemek isteyip istemediğinizi belirtir. |
| `wait_timeout` | Zaman aşımından önce beklenecek saniye sayısı. |
| `datastore_name` | Değiştirilen/eklenen Blobları izlemek için veri deposu. |
| `polling_interval` | Değiştirme/ekleme Blobları için yoklama arasında dakika cinsinden süre. Varsayılan değer: 5 dakika. Yalnızca veri deposu zamanlamaları için desteklenir. |
| `data_path_parameter_name` | Değiştirilen blob yoluyla ayarlanacak veri yolu ardışık düzen parametresinin adı. Yalnızca veri deposu zamanlamaları için desteklenir. |
| `continue_on_step_failure` | Bir adım başarısız olursa, gönderilen ardışık düzen eylemsizlik içindeki diğer adımların yürütülmesine devam edilip edilmeyeceğini belirtir. Sağlanmışsa, işlem hattının `continue_on_step_failure` ayarını geçersiz kılar.
| `path_on_datastore` | İsteğe bağlı. Değiştirilen/eklenen Blobları izlemek için veri deposundaki yol. Yol, veri deposu için kapsayıcının altındadır, bu nedenle zamanlama izleyicilerinin gerçek yolu kapsayıcı/`path_on_datastore`. Hiçbiri yoksa, veri deposu kapsayıcısı izlenir. `path_on_datastore` bir alt klasöründe yapılan ekler/değişiklikler izlenmez. Yalnızca veri deposu zamanlamaları için desteklenir. |

Aşağıdaki örnek, bir veri deposu tarafından tetiklenen bir zamanlamanın tanımını içerir:

```yaml
Schedule: 
      description: "Test create with datastore" 
      recurrence: ~ 
      pipeline_parameters: {} 
      wait_for_provisioning: True 
      wait_timeout: 3600 
      datastore_name: "workspaceblobstore" 
      polling_interval: 5 
      data_path_parameter_name: "input_data" 
      continue_on_step_failure: None 
      path_on_datastore: "file/path" 
```

**Yinelenen bir zamanlama**tanımlarken, `recurrence`altında aşağıdaki anahtarları kullanın:

| YAML anahtarı | Açıklama |
| ----- | ----- |
| `frequency` | Zamanlamanın yinelenme sıklığı. Geçerli değerler `"Minute"`, `"Hour"`, `"Day"`, `"Week"`veya `"Month"`. |
| `interval` | Zamanlamanın ne sıklıkta tetiklendiği. Tamsayı değeri, zamanlama yeniden etkinleştirilinceye kadar beklenecek zaman birimi sayısıdır. |
| `start_time` | Zamanlamanın başlangıç saati. Değerin dize biçimi `YYYY-MM-DDThh:mm:ss`. Başlangıç saati sağlanmazsa, ilk iş yükü anında çalıştırılır ve gelecekteki iş yükleri zamanlamaya göre çalıştırılır. Başlangıç zamanı geçmişte ise, ilk iş yükü bir sonraki hesaplanan çalışma zamanına göre çalıştırılır. |
| `time_zone` | Başlangıç saatinin saat dilimi. Saat dilimi sağlanmazsa UTC kullanılır. |
| `hours` | `frequency` `"Day"` veya `"Week"`, işlem hattının çalışması gereken günün saatleri olarak, virgülle ayırarak 0 ile 23 arasında bir veya daha fazla tamsayı belirtebilirsiniz. Yalnızca `time_of_day` veya `hours` ve `minutes` kullanılabilir. |
| `minutes` | `frequency` `"Day"` veya `"Week"`, işlem hattının çalışması gereken saatin dakikalarıyla virgülle ayırarak 0 ile 59 arasında bir veya daha fazla tamsayı belirtebilirsiniz. Yalnızca `time_of_day` veya `hours` ve `minutes` kullanılabilir. |
| `time_of_day` | `frequency` `"Day"` veya `"Week"`, zamanlamanın çalıştırılacağı günün bir saatini belirtebilirsiniz. Değerin dize biçimi `hh:mm`. Yalnızca `time_of_day` veya `hours` ve `minutes` kullanılabilir. |
| `week_days` | `frequency` `"Week"`, zamanlamanın çalışması gerektiği zaman virgülle ayırarak bir veya daha fazla gün belirtebilirsiniz. Geçerli değerler `"Monday"`, `"Tuesday"`, `"Wednesday"`, `"Thursday"`, `"Friday"`, `"Saturday"`ve `"Sunday"`. |

Aşağıdaki örnek yinelenen bir zamanlamanın tanımını içerir:

```yaml
Schedule: 
    description: "Test create with recurrence" 
    recurrence: 
        frequency: Week # Can be "Minute", "Hour", "Day", "Week", or "Month". 
        interval: 1 # how often fires 
        start_time: 2019-06-07T10:50:00 
        time_zone: UTC 
        hours: 
        - 1 
        minutes: 
        - 0 
        time_of_day: null 
        week_days: 
        - Friday 
    pipeline_parameters: 
        'a': 1 
    wait_for_provisioning: True 
    wait_timeout: 3600 
    datastore_name: ~ 
    polling_interval: ~ 
    data_path_parameter_name: ~ 
    continue_on_step_failure: None 
    path_on_datastore: ~ 
```

## <a name="next-steps"></a>Sonraki adımlar

[Azure Machine Learning IÇIN CLI uzantısını kullanmayı](reference-azure-machine-learning-cli.md)öğrenin.