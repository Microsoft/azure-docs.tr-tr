---
title: Eğitim & çıkarım işlem hedeflerini ayarlama
titleSuffix: Azure Machine Learning
description: Makine öğrenimi eğitimi ve çıkarımı için kullanmak üzere çalışma alanınıza işlem kaynakları (işlem hedefleri) ekleme
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 10/02/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperf-fy21q1
ms.openlocfilehash: a3a70ac5d5603cad98c199cbd8e3b98bb095d131
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106167677"
---
# <a name="set-up-compute-targets-for-model-training-and-deployment"></a>Model eğitimi ve dağıtımı için işlem hedefleri ayarlama

Azure Machine Learning çalışma alanınıza Azure işlem kaynakları eklemeyi öğrenin.  Daha sonra bu kaynakları, Machine Learning görevlerinde eğitim ve çıkarım [işlem hedefleri](concept-compute-target.md) olarak kullanabilirsiniz.

Bu makalede, çalışma alanınızı bu işlem kaynaklarını kullanacak şekilde ayarlamayı öğrenin:

* Yerel bilgisayarınız
* Uzak sanal makineler
* Azure HDInsight
* Azure Batch
* Azure Databricks
* Azure Data Lake Analytics
* Azure Container Örneği

Azure Machine Learning tarafından yönetilen işlem hedeflerini kullanmak için bkz.:


* [Azure Machine Learning işlem örneği](how-to-create-manage-compute-instance.md)
* [Azure Machine Learning işlem kümesi](how-to-create-attach-compute-cluster.md)
* [Azure Kubernetes hizmet kümesi](how-to-create-attach-kubernetes.md)

## <a name="prerequisites"></a>Önkoşullar

* Azure Machine Learning çalışma alanı. Daha fazla bilgi için bkz. [Azure Machine Learning çalışma alanı oluşturma](how-to-manage-workspace.md).

* [Machine Learning hizmeti Için Azure CLI uzantısı](reference-azure-machine-learning-cli.md), [Azure Machine Learning Python SDK](/python/api/overview/azure/ml/intro)veya [Azure Machine Learning Visual Studio Code uzantısı](tutorial-setup-vscode-extension.md).

## <a name="limitations"></a>Sınırlamalar

* Çalışma alanınızdan **aynı işlem için birden çok, eşzamanlı ek oluşturmayın** . Örneğin, bir Azure Kubernetes hizmet kümesini iki farklı ad kullanarak bir çalışma alanına ekleme. Her yeni ek önceki mevcut ekleri keser.

    Bir işlem hedefini yeniden iliştirmek isterseniz (örneğin, TLS veya diğer küme yapılandırma ayarını değiştirmek için), önce mevcut eki kaldırmalısınız.

## <a name="whats-a-compute-target"></a>İşlem hedefi nedir?

Azure Machine Learning, modelinizi toplu olarak [__işlem hedefleri__](concept-azure-machine-learning-architecture.md#compute-targets)olarak adlandırılan çeşitli kaynaklarda veya ortamlarda eğitebilirsiniz. İşlem hedefi bir yerel makine veya Azure Machine Learning Işlem, Azure HDInsight veya uzak bir sanal makine gibi bir bulut kaynağı olabilir.  Ayrıca ["modellerinizi dağıtma"](how-to-deploy-and-where.md)bölümünde açıklandığı gibi model dağıtımı için işlem hedeflerini de kullanırsınız.


## <a name="local-computer"></a><a id="local"></a>Yerel bilgisayar

**Eğitim** için yerel bilgisayarınızı kullandığınızda bir işlem hedefi oluşturmanız gerekmez.  Yalnızca yerel makinenizden [eğitim çalıştırmasını göndermeniz](how-to-set-up-training-targets.md) yeterlidir.

Yerel bilgisayarınızı **çıkarım** Için kullandığınızda Docker 'ın yüklü olması gerekir. Dağıtımı gerçekleştirmek için, Web hizmetinin kullanacağı bağlantı noktasını tanımlamak için [LocalWebservice.deploy_configuration ()](/python/api/azureml-core/azureml.core.webservice.local.localwebservice#deploy-configuration-port-none-) kullanın. Ardından, [Azure Machine Learning modelleri dağıtma](how-to-deploy-and-where.md)bölümünde açıklandığı gibi normal dağıtım sürecini kullanın.

## <a name="remote-virtual-machines"></a><a id="vm"></a>Uzak sanal makineler

Azure Machine Learning Ayrıca bir Azure sanal makinesini eklemeyi destekler. VM bir Azure Veri Bilimi Sanal Makinesi (DSVM) olmalıdır. VM, tam yaşam döngüsü makine öğrenimi geliştirmesi için seçkin bir araç ve çerçeve seçeneği sunar. DSVM 'nin Azure Machine Learning ile nasıl kullanılacağı hakkında daha fazla bilgi için bkz. [bir geliştirme ortamı yapılandırma](./how-to-configure-environment.md#dsvm).

> [!TIP]
> Uzak bir VM yerine [Azure Machine Learning işlem örneğini](concept-compute-instance.md)kullanmanızı öneririz. Azure Machine Learning özgü, tam olarak yönetilen, bulut tabanlı bir işlem çözümüdür. Daha fazla bilgi için bkz. [Azure Machine Learning işlem örneği oluşturma ve yönetme](how-to-create-manage-compute-instance.md).

1. **Oluşturma**: Azure Machine Learning sizin IÇIN uzak VM oluşturamaz. Bunun yerine, VM 'yi oluşturmanız ve ardından Azure Machine Learning çalışma alanınıza bağlamanız gerekir. DSVM oluşturma hakkında daha fazla bilgi için bkz. [Linux için veri bilimi sanal makinesi sağlama (Ubuntu)](./data-science-virtual-machine/dsvm-ubuntu-intro.md).

    > [!WARNING]
    > Azure Machine Learning yalnızca **Ubuntu** çalıştıran sanal makineleri destekler. Bir VM oluşturduğunuzda veya var olan bir VM 'yi seçtiğinizde Ubuntu kullanan bir VM seçmeniz gerekir.
    > 
    > Azure Machine Learning Ayrıca, sanal makinenin __Genel BIR IP adresine__ sahip olmasını gerektirir.

1. **Ekle**: var olan bir sanal makineyi bir işlem hedefi olarak eklemek için, sanal makine IÇIN kaynak kimliği, Kullanıcı adı ve parola sağlamanız gerekir. VM 'nin kaynak KIMLIĞI, aşağıdaki dize biçimi kullanılarak abonelik KIMLIĞI, kaynak grubu adı ve VM adı kullanılarak oluşturulabilir: `/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Compute/virtualMachines/<vm_name>`

 
   ```python
   from azureml.core.compute import RemoteCompute, ComputeTarget

   # Create the compute config 
   compute_target_name = "attach-dsvm"
   
   attach_config = RemoteCompute.attach_configuration(resource_id='<resource_id>',
                                                   ssh_port=22,
                                                   username='<username>',
                                                   password="<password>")

   # Attach the compute
   compute = ComputeTarget.attach(ws, compute_target_name, attach_config)

   compute.wait_for_completion(show_output=True)
   ```

   Veya [Azure Machine Learning Studio 'yu kullanarak](how-to-create-attach-compute-studio.md#attached-compute)dsvm 'yi çalışma alanınıza ekleyebilirsiniz.

    > [!WARNING]
    > Çalışma alanınızdan aynı DSVM 'ye birden çok, eşzamanlı ek oluşturmayın. Her yeni ek önceki mevcut ekleri keser.

1. **Yapılandır**: dsvm işlem hedefi için bir çalıştırma yapılandırması oluşturun. Docker ve Conda, DSVM 'de Eğitim ortamı oluşturmak ve yapılandırmak için kullanılır.

   ```python
   from azureml.core import ScriptRunConfig
   from azureml.core.environment import Environment
   from azureml.core.conda_dependencies import CondaDependencies
   
   # Create environment
   myenv = Environment(name="myenv")
   
   # Specify the conda dependencies
   myenv.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'])
   
   # If no base image is explicitly specified the default CPU image "azureml.core.runconfig.DEFAULT_CPU_IMAGE" will be used
   # To use GPU in DSVM, you should specify the default GPU base Docker image or another GPU-enabled image:
   # myenv.docker.enabled = True
   # myenv.docker.base_image = azureml.core.runconfig.DEFAULT_GPU_IMAGE
   
   # Configure the run configuration with the Linux DSVM as the compute target and the environment defined above
   src = ScriptRunConfig(source_directory=".", script="train.py", compute_target=compute, environment=myenv) 
   ```

> [!TIP]
> Bir VM 'yi çalışma alanınızdan __kaldırmak__ (ayırmak) Istiyorsanız [remotecompute. detach ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.remotecompute#detach--) yöntemini kullanın.
>
> Azure Machine Learning VM 'yi sizin için silmez. VM 'yi, Azure VM için Azure portal, CLı veya SDK 'yı kullanarak el ile silmeniz gerekir.

## <a name="azure-hdinsight"></a><a id="hdinsight"></a>Azure HDInsight 

Azure HDInsight, büyük veri analizi için popüler bir platformdur. Platform, modelinizi eğitebilmeniz için kullanılabilecek Apache Spark sağlar.

1. **Oluşturma**: Azure Machine Learning, sizin için HDInsight kümesi oluşturamaz. Bunun yerine, kümeyi oluşturmanız ve ardından Azure Machine Learning çalışma alanınıza bağlamanız gerekir. Daha fazla bilgi için bkz. [HDInsight 'Ta Spark kümesi oluşturma](../hdinsight/spark/apache-spark-jupyter-spark-sql.md). 

    > [!WARNING]
    > Azure Machine Learning, HDInsight kümesinin __Genel BIR IP adresine__ sahip olmasını gerektirir.

    Kümeyi oluşturduğunuzda bir SSH Kullanıcı adı ve parolası belirtmeniz gerekir. HDInsight 'ı bir işlem hedefi olarak kullanmak için ihtiyacınız olduğu için bu değerleri de göz önünde ayırın.
    
    Küme oluşturulduktan sonra, ana bilgisayar adı \<clustername> -SSH.azurehdinsight.NET ile bağlanın, burada \<clustername> küme için verdiğiniz addır. 

1. **İliştirme**: bir HDInsight kümesini işlem hedefi olarak eklemek için HDInsight kümesi IÇIN kaynak kimliği, Kullanıcı adı ve parola sağlamalısınız. HDInsight kümesinin kaynak KIMLIĞI, aşağıdaki dize biçimi kullanılarak abonelik KIMLIĞI, kaynak grubu adı ve HDInsight kümesi adı kullanılarak oluşturulabilir: `/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.HDInsight/clusters/<cluster_name>`

    ```python
   from azureml.core.compute import ComputeTarget, HDInsightCompute
   from azureml.exceptions import ComputeTargetException

   try:
    # if you want to connect using SSH key instead of username/password you can provide parameters private_key_file and private_key_passphrase

    attach_config = HDInsightCompute.attach_configuration(resource_id='<resource_id>',
                                                          ssh_port=22, 
                                                          username='<ssh-username>', 
                                                          password='<ssh-pwd>')
    hdi_compute = ComputeTarget.attach(workspace=ws, 
                                       name='myhdi', 
                                       attach_configuration=attach_config)

   except ComputeTargetException as e:
    print("Caught = {}".format(e.message))

   hdi_compute.wait_for_completion(show_output=True)
   ```

   İsterseniz de [Azure Machine Learning Studio kullanarak](how-to-create-attach-compute-studio.md#attached-compute)HDInsight kümesini çalışma alanınıza ekleyebilirsiniz.

    > [!WARNING]
    > Çalışma alanınızdan aynı HDInsight 'a birden çok, eşzamanlı ek oluşturmayın. Her yeni ek önceki mevcut ekleri keser.

1. **Yapılandır**: HDI işlem hedefi için bir çalıştırma yapılandırması oluşturun. 

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/hdi.py?name=run_hdi)]

> [!TIP]
> Çalışma alanından bir HDInsight kümesini __kaldırmak__ Istiyorsanız, [HDInsightCompute. detach ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.hdinsight.hdinsightcompute#detach--) yöntemini kullanın.
>
> Azure Machine Learning, HDInsight kümesini sizin için silmez. Azure HDInsight için Azure portal, CLı veya SDK 'Yı kullanarak el ile silmeniz gerekir.

## <a name="azure-batch"></a><a id="azbatch"></a>Azure Batch 

Azure Batch, büyük ölçekli paralel ve yüksek performanslı bilgi işlem (HPC) uygulamalarını bulutta verimli bir şekilde çalıştırmak için kullanılır. AzureBatchStep bir makine havuzuna Azure Batch iş göndermek için bir Azure Machine Learning işlem hattında kullanılabilir.

İşlem hedefi olarak Azure Batch iliştirmek için Azure Machine Learning SDK 'sını kullanmanız ve aşağıdaki bilgileri sağlamanız gerekir:

-    **Azure Batch işlem adı**: çalışma alanı içinde işlem için kullanılacak kolay bir ad
-    **Azure Batch hesap adı**: Azure Batch hesabının adı
-    **Kaynak grubu**: Azure Batch hesabını içeren kaynak grubu.

Aşağıdaki kod, Azure Batch işlem hedefi olarak nasıl ekleneceğini göstermektedir:

```python
from azureml.core.compute import ComputeTarget, BatchCompute
from azureml.exceptions import ComputeTargetException

# Name to associate with new compute in workspace
batch_compute_name = 'mybatchcompute'

# Batch account details needed to attach as compute to workspace
batch_account_name = "<batch_account_name>"  # Name of the Batch account
# Name of the resource group which contains this account
batch_resource_group = "<batch_resource_group>"

try:
    # check if the compute is already attached
    batch_compute = BatchCompute(ws, batch_compute_name)
except ComputeTargetException:
    print('Attaching Batch compute...')
    provisioning_config = BatchCompute.attach_configuration(
        resource_group=batch_resource_group, account_name=batch_account_name)
    batch_compute = ComputeTarget.attach(
        ws, batch_compute_name, provisioning_config)
    batch_compute.wait_for_completion()
    print("Provisioning state:{}".format(batch_compute.provisioning_state))
    print("Provisioning errors:{}".format(batch_compute.provisioning_errors))

print("Using Batch compute:{}".format(batch_compute.cluster_resource_id))
```

> [!WARNING]
> Çalışma alanınızdan aynı Azure Batch birden çok, eşzamanlı ek oluşturmayın. Her yeni ek önceki mevcut ekleri keser.

### <a name="azure-databricks"></a><a id="databricks"></a>Azure Databricks

Azure Databricks, Azure bulutu 'nda Apache Spark tabanlı bir ortamdır. Bir Azure Machine Learning işlem hattı ile işlem hedefi olarak kullanılabilir.

> [! ÖNEMLI} Azure Machine Learning Azure Databricks işlem hedefi oluşturamaz. Bunun yerine, bir Azure Databricks çalışma alanı oluşturmanız ve sonra bunu Azure Machine Learning workspacee bağlamanız gerekir. Bir çalışma alanı kaynağı oluşturmak için Azure Databricks belge [üzerinde bir Spark Işi çalıştırma](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal) ' ya bakın.

Azure Databricks bir işlem hedefi olarak eklemek için aşağıdaki bilgileri sağlayın:

* __Databricks işlem adı__: Bu işlem kaynağına atamak istediğiniz ad.
* __Databricks çalışma alanı adı__: Azure Databricks çalışma alanının adı.
* __Databricks erişim belirteci__: Azure Databricks için kimlik doğrulaması yapmak için kullanılan erişim belirteci. Erişim belirteci oluşturmak için, bkz. [kimlik doğrulama](/azure/databricks/dev-tools/api/latest/authentication) belgesi.

Aşağıdaki kod, Azure Machine Learning SDK ile bir işlem hedefi olarak Azure Databricks nasıl ekleneceğini gösterir (__Databricks çalışma alanı, AML çalışma alanınızın aynı abonelikte bulunması gerekir__):

```python
import os
from azureml.core.compute import ComputeTarget, DatabricksCompute
from azureml.exceptions import ComputeTargetException

databricks_compute_name = os.environ.get(
    "AML_DATABRICKS_COMPUTE_NAME", "<databricks_compute_name>")
databricks_workspace_name = os.environ.get(
    "AML_DATABRICKS_WORKSPACE", "<databricks_workspace_name>")
databricks_resource_group = os.environ.get(
    "AML_DATABRICKS_RESOURCE_GROUP", "<databricks_resource_group>")
databricks_access_token = os.environ.get(
    "AML_DATABRICKS_ACCESS_TOKEN", "<databricks_access_token>")

try:
    databricks_compute = ComputeTarget(
        workspace=ws, name=databricks_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('databricks_compute_name {}'.format(databricks_compute_name))
    print('databricks_workspace_name {}'.format(databricks_workspace_name))
    print('databricks_access_token {}'.format(databricks_access_token))

    # Create attach config
    attach_config = DatabricksCompute.attach_configuration(resource_group=databricks_resource_group,
                                                           workspace_name=databricks_workspace_name,
                                                           access_token=databricks_access_token)
    databricks_compute = ComputeTarget.attach(
        ws,
        databricks_compute_name,
        attach_config
    )

    databricks_compute.wait_for_completion(True)
```

Daha ayrıntılı bir örnek için GitHub 'daki [örnek bir not defteri](https://aka.ms/pl-databricks) bölümüne bakın.

> [!WARNING]
> Çalışma alanınızdan aynı Azure Databricks birden çok, eşzamanlı ek oluşturmayın. Her yeni ek önceki mevcut ekleri keser.

### <a name="azure-data-lake-analytics"></a><a id="adla"></a>Azure Data Lake Analytics

Azure Data Lake Analytics, Azure bulutundaki büyük bir veri analizi platformudur. Bir Azure Machine Learning işlem hattı ile işlem hedefi olarak kullanılabilir.

Kullanmadan önce bir Azure Data Lake Analytics hesabı oluşturun. Bu kaynağı oluşturmak için [Azure Data Lake Analytics kullanmaya başlama](../data-lake-analytics/data-lake-analytics-get-started-portal.md) belgesine bakın.

İşlem hedefi olarak Data Lake Analytics iliştirmek için Azure Machine Learning SDK 'sını kullanmanız ve aşağıdaki bilgileri sağlamanız gerekir:

* __İşlem adı__: Bu işlem kaynağına atamak istediğiniz ad.
* __Kaynak grubu__: Data Lake Analytics hesabını içeren kaynak grubu.
* __Hesap adı__: Data Lake Analytics hesap adı.

Aşağıdaki kod, Data Lake Analytics işlem hedefi olarak nasıl ekleneceğini göstermektedir:

```python
import os
from azureml.core.compute import ComputeTarget, AdlaCompute
from azureml.exceptions import ComputeTargetException


adla_compute_name = os.environ.get(
    "AML_ADLA_COMPUTE_NAME", "<adla_compute_name>")
adla_resource_group = os.environ.get(
    "AML_ADLA_RESOURCE_GROUP", "<adla_resource_group>")
adla_account_name = os.environ.get(
    "AML_ADLA_ACCOUNT_NAME", "<adla_account_name>")

try:
    adla_compute = ComputeTarget(workspace=ws, name=adla_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('adla_compute_name {}'.format(adla_compute_name))
    print('adla_resource_id {}'.format(adla_resource_group))
    print('adla_account_name {}'.format(adla_account_name))
    # create attach config
    attach_config = AdlaCompute.attach_configuration(resource_group=adla_resource_group,
                                                     account_name=adla_account_name)
    # Attach ADLA
    adla_compute = ComputeTarget.attach(
        ws,
        adla_compute_name,
        attach_config
    )

    adla_compute.wait_for_completion(True)
```

Daha ayrıntılı bir örnek için GitHub 'daki [örnek bir not defteri](https://aka.ms/pl-adla) bölümüne bakın.

> [!WARNING]
> Çalışma alanınızdan aynı ADLA birden çok, eşzamanlı ek oluşturmayın. Her yeni ek önceki mevcut ekleri keser.

> [!TIP]
> Azure Machine Learning işlem hatları yalnızca Data Lake Analytics hesabının varsayılan veri deposunda depolanan verilerle çalışabilir. Üzerinde çalışmanız gereken veriler varsayılan olmayan bir depoda varsa, [`DataTransferStep`](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep) verileri eğitimden önce kopyalamak için kullanabilirsiniz.

## <a name="azure-container-instance"></a><a id="aci"></a>Azure Container örneği

Azure Container Instances (acı), bir modeli dağıtırken dinamik olarak oluşturulur. Çalışma alanınıza başka hiçbir şekilde bir acı oluşturamaz veya ekleyemezsiniz. Daha fazla bilgi için bkz. [Azure Container Instances model dağıtma](how-to-deploy-azure-container-instance.md).

## <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

Azure Kubernetes hizmeti (AKS) Azure Machine Learning ile kullanıldığında çeşitli yapılandırma seçeneklerine izin verir. Daha fazla bilgi için bkz. [Azure Kubernetes hizmeti oluşturma ve iliştirme](how-to-create-attach-kubernetes.md).

## <a name="notebook-examples"></a>Not defteri örnekleri

Çeşitli işlem hedeflerine yönelik eğitim örnekleri için bu not defterlerine bakın:
* [Nasıl yapılır kullanımı-azureml/eğitim](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [Öğreticiler/img-Classification-part1-Training. ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/image-classification-mnist-data/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Sonraki adımlar

* [Bir eğitim çalıştırması yapılandırmak ve göndermek](how-to-set-up-training-targets.md)için işlem kaynağını kullanın.
* [Öğretici: bir modeli eğitme](tutorial-train-models-with-aml.md) bir modeli eğmek için yönetilen bir işlem hedefi kullanır.
* Daha iyi modeller oluşturmak için [hiper parametreleri verimli](how-to-tune-hyperparameters.md) bir şekilde ayarlamayı öğrenin.
* Eğitilen bir modelden sonra [modellerin nasıl ve nereye dağıtılacağını](how-to-deploy-and-where.md)öğrenin.
* [Azure sanal ağları ile Azure Machine Learning kullanma](./how-to-network-security-overview.md)