---
title: Azure Databricks ML denemeleri için MLflow Izleme
titleSuffix: Azure Machine Learning
description: Azure Databricks ml denemeleri 'deki ölçümleri ve yapıtları günlüğe kaydetmek için Azure Machine Learning ile MLflow ayarlayın.
services: machine-learning
author: nibaccam
ms.author: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.reviewer: nibaccam
ms.date: 09/22/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: a894a46ce7c78b65dde80c52f9516fcbe4d27bcb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102521008"
---
# <a name="track-azure-databricks-ml-experiments-with-mlflow-and-azure-machine-learning-preview"></a>MLflow ve Azure Machine Learning ile Azure Databricks ML denemeleri izleme (Önizleme)

Bu makalede, Azure Databricks (ADB) denemeleri, MLflow ve Azure Machine Learning bağlantı kurmak için mlflow [izlemesi](https://mlflow.org/docs/latest/quickstart.html#using-the-tracking-api)olarak bilinen MLFLOW izleme URI 'sini ve günlük API 'yi nasıl etkinleştireceğinizi öğrenin.

[Mlflow](https://www.mlflow.org) , Machine Learning denemeleri 'in yaşam döngüsünü yönetmeye yönelik açık kaynaklı bir kitaplıktır. MLFlow Izlemesi, öğreticinizi günlüğe kaydeden ve izleyen bir MLflow bileşenidir. [Azure Databricks ve MLflow](/azure/databricks/applications/mlflow/)hakkında daha fazla bilgi edinin. 

Daha fazla MLflow ve Azure Machine Learning işlevselliği tümleştirmeleri için bkz. [MLflow ve Azure Machine Learning deneme çalıştırmalarını izleyin](how-to-use-mlflow.md) .

>[!NOTE]
> Açık kaynak kitaplığı olarak MLflow sık sık değişir. Bu nedenle, Azure Machine Learning ve MLflow tümleştirmesiyle sunulan işlevlerin Microsoft tarafından tam olarak desteklenmeden bir önizleme olarak değerlendirilmesi gerekir.

> [!TIP]
> Bu belgedeki bilgiler öncelikli olarak, model eğitimi sürecini izlemek isteyen veri bilimcileri ve geliştiricileri içindir. Kotalar, tamamlanan eğitim çalıştırmaları veya tamamlanmış model dağıtımları gibi Azure Machine Learning kaynak kullanımını ve olayları izlemek isteyen bir yöneticiyseniz, bkz. [izleme Azure Machine Learning](monitor-azure-machine-learning.md).

## <a name="prerequisites"></a>Önkoşullar

* `azureml-mlflow` paketini yükleyin. 
    * Bu paket `azureml-core` , çalışma alanınıza erişmek Için MLflow bağlantısını sağlayan [Azure Machine Learning Python SDK 'sını](/python/api/overview/azure/ml/install)otomatik olarak getirir.
* Bir [Azure Databricks çalışma alanı ve kümesi](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal).
* [Azure Machine Learning çalışma alanı oluşturun](how-to-manage-workspace.md).
    * [MLflow işlemlerinizi çalışma alanınız ile gerçekleştirmek için hangi erişim izinlerine ihtiyacınız](how-to-assign-roles.md#mlflow-operations)olduğunu görün.

## <a name="track-azure-databricks-runs"></a>Azure Databricks çalıştırmalarını izleyin

Azure Machine Learning ile MLflow Izleme, günlüğe kaydedilen ölçümleri ve yapıtları Azure Databricks çalıştırmalarınızın her ikisine de depolamanızı sağlar: 

* Azure Databricks çalışma alanı.
* Azure Machine Learning çalışma alanı

Azure Databricks çalışma alanınızı ve kümenizi oluşturduktan sonra, 

1. Kümenizin gerekli işlevlere ve sınıflara erişebildiğinden emin olmak için PyPi 'den *azureml-mlflow* kitaplığını yükler.

1. Deneme not defterinizi ayarlayın.

1. Azure Databricks çalışma alanınızı ve Azure Machine Learning çalışma alanınızı bağlayın.

Bu adımlar için ek ayrıntılar aşağıdaki bölümlerde verilmiştir, bu sayede MLflow denemeleri Azure Databricks ile başarıyla çalıştırabilirsiniz. 

## <a name="install-libraries"></a>Kitaplıkları yükler

Kümenizi kümenize yüklemek için, **Kitaplıklar** sekmesine gidin ve **Yeni ' yi** aç ' ı seçin.

 ![Azure databricks ile mlflow](./media/how-to-use-mlflow-azure-databricks/azure-databricks-cluster-libraries.png)

**Paket** alanına, azureml-mlflow yazın ve ardından Install ' ı seçin. Bu adımı, denemeniz için kümenize diğer ek paketleri yüklemek için gereken şekilde tekrarlayın.

 ![Azure DB install mlflow kitaplığı](./media/how-to-use-mlflow-azure-databricks/install-libraries.png)

## <a name="set-up-your-notebook"></a>Not defterinizi ayarlama 

ADB kümeniz kurulduktan sonra, 
1. Sol gezinti bölmesindeki **çalışma alanları** ' nı seçin. 
1. Çalışma alanları açılan menüsünü genişletin ve **Içeri aktar** ' ı seçin.
1. ADB çalışma alanınızı içeri aktarmak için, deneme Not defterinize sürükleyip bırakın veya bulun.
1. **İçeri aktar**'ı seçin. Deneme Not defteriniz otomatik olarak açılır.
1. Sol üstteki Not defteri başlığı altında, deneme Not defterinize eklemek istediğiniz kümeyi seçin. 

## <a name="connect-your-azure-databricks-and-azure-machine-learning-workspaces"></a>Azure Databricks ve Azure Machine Learning çalışma alanlarınızı bağlama

ADB çalışma alanınızı Azure Machine Learning çalışma alanınıza bağlamak, deneme verilerinizi Azure Machine Learning çalışma alanında izlemenize olanak sağlar.

ADB çalışma alanınızı yeni veya mevcut bir Azure Machine Learning çalışma alanına bağlamak için 
1. [Azure Portal](https://ms.portal.azure.com)oturum açın.
1. ADB çalışma alanınızın **genel bakış** sayfasına gidin.
1. Sağ alt köşedeki **Azure Machine Learning çalışma alanını bağla** düğmesini seçin. 

 ![Azure DB ve Azure Machine Learning çalışma alanlarını bağlama](./media/how-to-use-mlflow-azure-databricks/link-workspaces.png)

## <a name="mlflow-tracking-in-your-workspaces"></a>Çalışma Alanlarınızdaki MLflow Izleme

Çalışma alanınızı örnekledikten sonra MLflow Izleme otomatik olarak aşağıdaki yerlerde izlenecek şekilde ayarlanır:

* Bağlı Azure Machine Learning çalışma alanı.
* Özgün ADB çalışma alanınız. 

Yönetilen Azure Machine Learning izleme hizmetinde tüm denemeleri Land.

Aşağıdaki kod, bağlantı Azure Machine Learning çalışma alanınızı almak için deneme Not defterinizde olmalıdır. 

Bu kod, 

*  Azure Machine Learning çalışma alanınızı oluşturmak için Azure aboneliğinizin ayrıntılarını alır. 

* Mevcut bir kaynak grubunuz ve Azure Machine Learning çalışma alanınız olduğunu varsayar, aksi takdirde [bunları oluşturabilirsiniz](how-to-manage-workspace.md). 

* Deneme adını ayarlar. Bu, `user_name` `user_name` Azure Databricks çalışma alanıyla ilişkili ile tutarlıdır.

```python
import mlflow
import mlflow.azureml
import azureml.mlflow
import azureml.core

from azureml.core import Workspace

subscription_id = 'subscription_id'

# Azure Machine Learning resource group NOT the managed resource group
resource_group = 'resource_group_name' 

#Azure Machine Learning workspace name, NOT Azure Databricks workspace
workspace_name = 'workspace_name'  

# Instantiate Azure Machine Learning workspace
ws = Workspace.get(name=workspace_name,
                   subscription_id=subscription_id,
                   resource_group=resource_group)

#Set MLflow experiment. 
experimentName = "/Users/{user_name}/{experiment_folder}/{experiment_name}" 
mlflow.set_experiment(experimentName) 

```

### <a name="set-mlflow-tracking-to-only-track-in-your-azure-machine-learning-workspace"></a>MLflow Izlemeyi yalnızca Azure Machine Learning çalışma alanınızda izlemek üzere ayarla

İzlenen denemeleri 'yi merkezi bir konumda yönetmeyi tercih ediyorsanız, MLflow izlemesini **yalnızca** Azure Machine Learning çalışma alanınızda izlemek üzere ayarlayabilirsiniz. 

Betiğe aşağıdaki kodu ekleyin:

```python
uri = ws.get_mlflow_tracking_uri()
mlflow.set_tracking_uri(uri)
```

Eğitim betiğinizdeki `mlflow` MLflow günlüğü API 'lerini kullanmak için içeri aktarın ve çalıştırma ölçümlerinizi günlüğe kaydetmeye başlayın. Aşağıdaki örnek, dönem kayıp ölçümünü günlüğe kaydeder. 

```python
import mlflow 
mlflow.log_metric('epoch_loss', loss.item()) 
```

## <a name="register-models-with-mlflow"></a>MLflow ile modelleri kaydetme

Modeliniz eğitilirken, modellerinizi günlüğe kaydedebilir ve yöntemi ile arka uç izleme sunucusuna kaydedebilirsiniz `mlflow.<model_flavor>.log_model()` . `<model_flavor>`, modeliyle ilişkili çatıya başvurur. [Model türleri 'nin desteklendiğini öğrenin](https://mlflow.org/docs/latest/models.html#model-api). 

Arka uç izleme sunucusu, varsayılan olarak Azure Databricks çalışma alanıdır; [MLflow izlemesini yalnızca Azure Machine Learning çalışma alanınızda izlemek üzere ayarlamayı](#set-mlflow-tracking-to-only-track-in-your-azure-machine-learning-workspace)seçmediğiniz takdirde, arka uç izleme sunucusu Azure Machine Learning çalışma alanıdır.   

* **Ada sahip kayıtlı bir model yoksa**, yöntemi yeni bir model kaydeder, sürüm 1 oluşturur ve bir Modelversion mlflow nesnesi döndürüyor. 

* **Ada sahip kayıtlı bir model zaten varsa**, yöntemi yeni bir model sürümü oluşturur ve sürüm nesnesini döndürür. 

```python
mlflow.spark.log_model(model, artifact_path = "model", 
                       registered_model_name = 'model_name')  

mlflow.sklearn.log_model(model, artifact_path = "model", 
                         registered_model_name = 'model_name') 
```

## <a name="create-endpoints-for-mlflow-models"></a>MLflow modelleri için uç noktalar oluşturma

ML modelleriniz için bir uç nokta oluşturmaya hazırsanız. Dağıtım yapabilirsiniz, 

* Etkileşimli Puanlama için bir Azure Machine Learning Request-Response Web hizmeti. Bu dağıtım, Azure Machine Learning model yönetimi ve veri Drın algılama yeteneklerini üretim modellerinize uygulayıp uygulamanıza olanak tanır. 

* Azure Databricks çalışma alanında Python işlevleri veya Pandas UDF 'ler olarak akış veya toplu işlem hatları olarak kullanılabilen MLFlow model nesneleri.

### <a name="deploy-models-to-azure-machine-learning-endpoints"></a>Modelleri Azure Machine Learning uç noktalarına dağıtma 
Azure Machine Learning çalışma alanınıza bir model dağıtmak için [mlflow. azureml. deploy](https://www.mlflow.org/docs/latest/python_api/mlflow.azureml.html#mlflow.azureml.deploy) API 'sinden yararlanabilirsiniz. Modeli, [MLflow ile kaydet](#register-models-with-mlflow) bölümünde açıklandığı gibi Azure Databricks çalışma alanına kaydettirdiğiniz takdirde, `model_name` modelin Azure Machine Learning çalışma alanına kaydedileceği parametreyi belirtin. 

Azure Databricks çalıştırmaları aşağıdaki uç noktalara dağıtılabilir. 
* [Azure Container örneği](how-to-deploy-mlflow-models.md#deploy-to-azure-container-instance-aci)
* [Azure Kubernetes Service](how-to-deploy-mlflow-models.md#deploy-to-azure-kubernetes-service-aks)

### <a name="deploy-models-to-adb-endpoints-for-batch-scoring"></a>Toplu Puanlama için ADB uç noktalarına modeller dağıtın 

Toplu Puanlama için Azure Databricks kümeleri seçebilirsiniz. MLFlow modeli yüklenir ve yeni verileri öğrenmek için Spark Pandas UDF olarak kullanılır. 

```python
from pyspark.sql.types import ArrayType, FloatType 

model_uri = "runs:/"+last_run_id+ {model_path} 

#Create a Spark UDF for the MLFlow model 

pyfunc_udf = mlflow.pyfunc.spark_udf(spark, model_uri) 

#Load Scoring Data into Spark Dataframe 

scoreDf = spark.table({table_name}).where({required_conditions}) 


#Make Prediction 

preds = (scoreDf 

           .withColumn('target_column_name', pyfunc_udf('Input_column1', 'Input_column2', ' Input_column3', …)) 

        ) 

display(preds) 
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Çalışma alanınızda günlüğe kaydedilen ölçümleri ve yapıtları kullanmayı planlamıyorsanız, bunları tek tek silme özelliği şu anda kullanılamaz. Bunun yerine, depolama hesabı ve çalışma alanını içeren kaynak grubunu silip herhangi bir ücret ödemeniz gerekmez:

1. Azure portalının en sol tarafındaki **Kaynak gruplarını** seçin.

   ![Azure portal Sil](./media/how-to-use-mlflow-azure-databricks/delete-resources.png)

1. Listeden oluşturduğunuz kaynak grubunu seçin.

1. **Kaynak grubunu sil**'i seçin.

1. Kaynak grubu adını girin. Ardından **Sil**’i seçin.

## <a name="example-notebooks"></a>Örnek not defterleri

[Azure Machine Learning Not defterlerindeki Mlflow](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/track-and-monitor-experiments/using-mlflow) , bu makalede sunulan kavramları gösterir ve genişletir.

## <a name="next-steps"></a>Sonraki adımlar
* [MLflow modellerini bir Azure Web hizmeti olarak dağıtın](how-to-deploy-mlflow-models.md). 
* [Modellerinizi yönetin](concept-model-management-and-deployment.md).
* [MLflow ve Azure Machine Learning ile deneme çalıştırmalarını izleyin](how-to-use-mlflow.md). 
* [Azure Databricks ve MLflow](/azure/databricks/applications/mlflow/)hakkında daha fazla bilgi edinin.