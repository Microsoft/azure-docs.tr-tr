---
title: Pekiştirmeye dayalı öğrenme modeli (Önizleme) eğitimi yapın ve dağıtın.
titleSuffix: Azure Machine Learning
description: Azure Machine Learning pekiştirmeye dayalı Learning (Önizleme) kullanarak bir RL aracısını Pong oynamak üzere eğitme hakkında bilgi edinin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: peterlu
author: peterclu
ms.date: 05/05/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperf-fy21q2
ms.openlocfilehash: 4c03016d003978b3c56361595bec7c559205574b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102520889"
---
# <a name="reinforcement-learning-preview-with-azure-machine-learning"></a>Azure Machine Learning ile pekiştirmeye dayalı öğrenme (Önizleme)



> [!NOTE]
> Azure Machine Learning pekiştirmeye dayalı Learning Şu anda bir önizleme özelliğidir. Şu anda yalnızca Ray ve RLlib çerçeveleri destekleniyor.

Bu makalede, video oyunu oynamak için pekiştirmeye dayalı Learning (RL) aracısını eğitme hakkında bilgi edineceksiniz. Dağıtılmış RL 'nin karmaşıklığını yönetmek için Azure Machine Learning birlikte açık kaynaklı Python Kitaplığı [Ray Rlrllib](https://ray.readthedocs.io/en/master/rllib.html) kullanın.

Bu makalede şunları öğreneceksiniz:
> [!div class="checklist"]
> * Deneme ayarlama
> * Baş ve çalışan düğümlerini tanımlama
> * Bir RL tahmin aracı oluşturma
> * Çalıştırmayı başlatmak için bir deneme gönder
> * Sonuçları görüntüleme

Bu makale, Azure Machine Learning Not defteri [GitHub deposunda](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/reinforcement-learning/README.md)bulunan [Rllib Pong örneğini](https://aka.ms/azureml-rl-pong) temel alır.

## <a name="prerequisites"></a>Önkoşullar

Bu kodu bu ortamlardan birinde çalıştırın. En hızlı başlangıç deneyimi için Azure Machine Learning işlem örneği denemeniz önerilir. Pekiştirmeye dayalı örnek not defterlerini bir Azure Machine Learning işlem örneğinde hızlıca kopyalayabilir ve çalıştırabilirsiniz.

 - Azure Machine Learning işlem örneği

     - Eğitim bölümünde örnek not defterlerini nasıl klonleyeceğinizi öğrenin [: Kurulum ortamı ve çalışma alanı](tutorial-1st-experiment-sdk-setup.md).
         - **Öğretici** yerine, **kullanım için nasıl yapılır-azureml** klasörünü kopyalayın
     - `/how-to-use-azureml/reinforcement-learning/setup/devenv_setup.ipynb`Dağıtılmış pekiştirmeye dayalı öğrenimi için kullanılan ağ bağlantı noktalarını açmak üzere konumunda bulunan sanal ağ kurulum Not defterini çalıştırın.
     - Örnek Not defterini çalıştırma `/how-to-use-azureml/reinforcement-learning/atari-on-distributed-compute/pong_rllib.ipynb`
 
 - Kendi Jupyter Notebook sunucunuz

    - [Azure Machine Learning SDK 'sını](/python/api/overview/azure/ml/install)yükler.
    - [Azure MACHINE LEARNING rl SDK 'sını](/python/api/azureml-contrib-reinforcementlearning/)yükler:`pip install --upgrade azureml-contrib-reinforcementlearning`
    - Bir [çalışma alanı yapılandırma dosyası](how-to-configure-environment.md#workspace)oluşturun.
    - Dağıtılmış pekiştirmeye dayalı öğrenimi için kullanılan ağ bağlantı noktalarını açmak için sanal ağı çalıştırın.


## <a name="how-to-train-a-pong-playing-agent"></a>Pong-çalıyor aracısını eğitme

Pekiştirmeye dayalı Learning (RL), bir makine öğrenimine, bunu yaparak öğrenerek bir yaklaşıktır. Diğer makine öğrenimi teknikleri, çok büyük bir şekilde giriş verileri alarak ve içindeki desenleri bularak öğrenirken, RL, kararları etkin bir şekilde oluşturmak ve sonuçları öğrenmek için **eğitim aracılarını** kullanır.

Eğitim aracılarınız, **sanal bir ortamda** Pong oynamasını öğrendiğinde. Eğitim aracıları, her oyunun çerçevesini artırma, azaltma veya yerinde kalma gibi bir karardır. Bir karar vermek için oyunun durumuna (ekranın RGB görüntüsü) bakar.

RL, kararlarının başarılı olup olmadığını aracıya bildirmek için **yeniden** kullanır. Bu örnekte, aracı bir noktaya göre puanlandığında bir noktayı işaret eden bir nokta ve negatif bir ödül aldığında bir pozitif ödül alır. Eğitim Aracısı birçok yinelemeden sonra, beklenen gelecekteki yeniden temellerinin toplamını en iyi duruma getirir. Bu iyileştirmesi RL 'de gerçekleştirmek için **derin sinir Networks** (DNN) kullanılması yaygındır. 

Eğitim, aracı bir eğitim dönemi içinde 18 ' in Ortalama bir yeniden puanına ulaştığında sona erer. Bu, aracının rakibinize, en az 21 ' e varan bir ortalama, en az 18 puntodan oluşan bir ortalama işaret ettiği anlamına gelir.

Bir DNN benzetimi ve yeniden eğitimi aracılığıyla yineleme süreci, oldukça pahalıdır ve çok fazla veri gerektirir. RL işlerinin performansını artırmanın bir yolu, birden çok eğitim aracısının aynı anda hareket edebilmesi ve öğrenilmesine olanak sağlayacak şekilde **işi paralelleştirmenize** yol açabilir. Ancak, dağıtılmış bir RL ortamının yönetilmesi karmaşık bir yetersiz duruma neden olabilir.

Azure Machine Learning, RL iş yüklerinizi ölçeklendirmek için bu karmaşıklıkları yönetme çerçevesini sağlar.

## <a name="set-up-the-environment"></a>Ortamı ayarlama

Yerel RL ortamını şu şekilde ayarlayın:
1. Gerekli Python paketleri yükleniyor
1. Çalışma alanınız başlatılıyor
1. Deneme oluşturma
1. Yapılandırılmış bir sanal ağ belirtme.

### <a name="import-libraries"></a>Kitaplıkları içeri aktarma

Bu örneğin geri kalanını çalıştırmak için gerekli Python paketlerini içeri aktarın.

```python
# Azure ML Core imports
import azureml.core
from azureml.core import Workspace
from azureml.core import Experiment
from azureml.core.compute import AmlCompute
from azureml.core.compute import ComputeTarget
from azureml.core.runconfig import EnvironmentDefinition
from azureml.widgets import RunDetails
from azureml.tensorboard import Tensorboard

# Azure ML Reinforcement Learning imports
from azureml.contrib.train.rl import ReinforcementLearningEstimator, Ray
from azureml.contrib.train.rl import WorkerConfiguration
```

### <a name="initialize-a-workspace"></a>Çalışma alanını başlatma

[](concept-workspace.md) `config.json` [Önkoşullar bölümünde](#prerequisites)oluşturulan dosyadan bir çalışma alanı nesnesi başlatın. Bu kodu bir Azure Machine Learning Işlem örneğinde yürütüyorsunuz, yapılandırma dosyası sizin için zaten oluşturulmuştur.

```Python
ws = Workspace.from_config()
```

### <a name="create-a-reinforcement-learning-experiment"></a>Pekiştirmeye dayalı Learning denemesi oluşturun

Pekiştirmeye dayalı Learning çalıştırmanızı izlemek için bir [deneme](/python/api/azureml-core/azureml.core.experiment.experiment) oluşturun. Azure Machine Learning, denemeleri, çalışma günlüklerini, geçmişi, çıkışları ve daha fazlasını düzenlemek için ilgili denemelerin mantıksal koleksiyonlarıdır.

```python
experiment_name='rllib-pong-multi-node'

exp = Experiment(workspace=ws, name=experiment_name)
```

### <a name="specify-a-virtual-network"></a>Bir sanal ağ belirtin

Birden çok işlem hedefi kullanan RL işleri için, çalışan düğümlerinin ve baş düğümlerin birbirleriyle iletişim kurmasına izin veren açık bağlantı noktaları içeren bir sanal ağ belirtmeniz gerekir.

Sanal ağ herhangi bir kaynak grubunda olabilir, ancak çalışma alanınız ile aynı bölgede olmalıdır. Sanal ağınızı ayarlama hakkında daha fazla bilgi için, Önkoşullar bölümünde çalışma alanı kurulum Not defteri ' ne bakın. Burada, kaynak grubunuzda sanal ağın adını belirtirsiniz.

```python
vnet = 'your_vnet'
```

## <a name="define-head-and-worker-compute-targets"></a>Baş ve çalışan işlem hedeflerini tanımlama

Bu örnek, Ray baş ve çalışanlar düğümleri için ayrı işlem hedefleri kullanır. Bu ayarlar, iş yükünüze bağlı olarak işlem kaynaklarınızı yukarı ve aşağı ölçeklendirmenizi sağlar. Gereksinimlerinize göre düğümlerin sayısını ve her bir düğümün boyutunu ayarlayın.

### <a name="head-computing-target"></a>Baş bilgi işlem hedefi

Derin öğrenme performansını geliştirmek için GPU ile donatılmış bir baş küme kullanabilirsiniz. Baş düğüm, aracının kararlar almak için kullandığı sinir ağını tratlar. Baş düğüm Ayrıca sinir ağını eğiteetmek için çalışan düğümlerinden veri noktaları toplar.

Baş işlem tek bir [ `STANDARD_NC6` sanal makine](../virtual-machines/nc-series.md) (VM) kullanır. Üzerinde iş dağıtmak için 6 sanal CPU vardır.


```python
from azureml.core.compute import AmlCompute, ComputeTarget

# choose a name for the Ray head cluster
head_compute_name = 'head-gpu'
head_compute_min_nodes = 0
head_compute_max_nodes = 2

# This example uses GPU VM. For using CPU VM, set SKU to STANDARD_D2_V2
head_vm_size = 'STANDARD_NC6'

if head_compute_name in ws.compute_targets:
    head_compute_target = ws.compute_targets[head_compute_name]
    if head_compute_target and type(head_compute_target) is AmlCompute:
        print(f'found head compute target. just use it {head_compute_name}')
else:
    print('creating a new head compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size = head_vm_size,
                                                                min_nodes = head_compute_min_nodes, 
                                                                max_nodes = head_compute_max_nodes,
                                                                vnet_resourcegroup_name = ws.resource_group,
                                                                vnet_name = vnet_name,
                                                                subnet_name = 'default')

    # create the cluster
    head_compute_target = ComputeTarget.create(ws, head_compute_name, provisioning_config)
    
    # can poll for a minimum number of nodes and for a specific timeout. 
    # if no min node count is provided it will use the scale settings for the cluster
    head_compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
    
     # For a more detailed view of current AmlCompute status, use get_status()
    print(head_compute_target.get_status().serialize())
```

[!INCLUDE [low-pri-note](../../includes/machine-learning-low-pri-vm.md)]

### <a name="worker-computing-cluster"></a>Çalışan bilgi işlem kümesi

Bu örnek, çalışan işlem hedefi için dört [ `STANDARD_D2_V2` VM](../virtual-machines/nc-series.md) kullanır. Her çalışan düğümünde toplam 8 kullanılabilir CPU için 2 kullanılabilir CPU bulunur.

Derin öğrenme gerçekleştirmediği için GPU 'Lar çalışan düğümleri için gerekli değildir. Çalışanlar oyun benzetimleri çalıştırır ve veri toplar.

```python
# choose a name for your Ray worker cluster
worker_compute_name = 'worker-cpu'
worker_compute_min_nodes = 0 
worker_compute_max_nodes = 4

# This example uses CPU VM. For using GPU VM, set SKU to STANDARD_NC6
worker_vm_size = 'STANDARD_D2_V2'

# Create the compute target if it hasn't been created already
if worker_compute_name in ws.compute_targets:
    worker_compute_target = ws.compute_targets[worker_compute_name]
    if worker_compute_target and type(worker_compute_target) is AmlCompute:
        print(f'found worker compute target. just use it {worker_compute_name}')
else:
    print('creating a new worker compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size = worker_vm_size,
                                                                min_nodes = worker_compute_min_nodes, 
                                                                max_nodes = worker_compute_max_nodes,
                                                                vnet_resourcegroup_name = ws.resource_group,
                                                                vnet_name = vnet_name,
                                                                subnet_name = 'default')

    # create the cluster
    worker_compute_target = ComputeTarget.create(ws, worker_compute_name, provisioning_config)
    
    # can poll for a minimum number of nodes and for a specific timeout. 
    # if no min node count is provided it will use the scale settings for the cluster
    worker_compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
    
     # For a more detailed view of current AmlCompute status, use get_status()
    print(worker_compute_target.get_status().serialize())
```

## <a name="create-a-reinforcement-learning-estimator"></a>Pekiştirmeye dayalı Learning tahmin aracı oluşturma
Azure Machine Learning bir eğitim işi göndermek için [Reforcementlearningestimator](/python/api/azureml-contrib-reinforcementlearning/azureml.contrib.train.rl.reinforcementlearningestimator) ' i kullanın.

Azure Machine Learning, çalışan yapılandırma bilgilerini kapsüllemek için tahmin aracı sınıflarını kullanır. Bu, bir komut dosyası yürütmenin nasıl yapılandırılacağını belirtmenizi sağlar. 

### <a name="define-a-worker-configuration"></a>Çalışan yapılandırması tanımlama

WorkerConfiguration nesnesi, giriş betiğini çalıştıran çalışan kümenin nasıl başlatılacağını Azure Machine Learning söyler.

```python
# Pip packages we will use for both head and worker
pip_packages=["ray[rllib]==0.8.3"] # Latest version of Ray has fixes for isses related to object transfers

# Specify the Ray worker configuration
worker_conf = WorkerConfiguration(
    
    # Azure ML compute cluster to run Ray workers
    compute_target=worker_compute_target, 
    
    # Number of worker nodes
    node_count=4,
    
    # GPU
    use_gpu=False, 
    
    # PIP packages to use
    pip_packages=pip_packages
)
```

### <a name="define-script-parameters"></a>Betik parametrelerini tanımla

Giriş betiği, `pong_rllib.py` eğitim işinin nasıl yürütüleceğini tanımlayan parametrelerin bir listesini kabul eder. Bu parametreleri bir kapsülleme katmanı olarak tahmin aracı aracılığıyla geçirmek, betik parametrelerinin değiştirilmesini ve yapılandırmaların birbirinden bağımsız olarak çalıştırılmasını kolaylaştırır.

Doğru belirtme, `num_workers` paralelleştirme çabalarınızın en iyi şekilde yararlanmasına neden olur. Çalışan sayısını kullanılabilir CPU sayısıyla aynı olarak ayarlayın. Bu örnekte, aşağıdaki hesaplamayı kullanabilirsiniz:

Baş düğüm 6 vCPU içeren bir [Standard_NC6](../virtual-machines/nc-series.md) . Çalışan kümesi, toplam 8 CPU için 2 CPU 'ya sahip 4 [Standard_D2_V2 VM](../cloud-services/cloud-services-sizes-specs.md#dv2-series) 'dir. Ancak, baş düğüm rolüne ayrılmanız gerektiğinden, çalışan sayısı 1 CPU 'YU çıkarmalısınız.

6 CPU + 8 CPU-1 baş CPU = 13 eşzamanlı çalışan. Azure Machine Learning, işlem kaynaklarını ayırt etmek için baş ve çalışan kümelerini kullanır. Ancak, Ray baş ve çalışanları ayırt etmez ve tüm CPU 'Lar çalışan iş parçacıkları olarak kullanılabilir.


```python
training_algorithm = "IMPALA"
rl_environment = "PongNoFrameskip-v4"

# Training script parameters
script_params = {
    
    # Training algorithm, IMPALA in this case
    "--run": training_algorithm,
    
    # Environment, Pong in this case
    "--env": rl_environment,
    
    # Add additional single quotes at the both ends of string values as we have spaces in the 
    # string parameters, outermost quotes are not passed to scripts as they are not actually part of string
    # Number of GPUs
    # Number of ray workers
    "--config": '\'{"num_gpus": 1, "num_workers": 13}\'',
    
    # Target episode reward mean to stop the training
    # Total training time in seconds
    "--stop": '\'{"episode_reward_mean": 18, "time_total_s": 3600}\'',
}
```

### <a name="define-the-reinforcement-learning-estimator"></a>Pekiştirmeye dayalı Learning tahmin aracı 'ı tanımlama

Estimator oluşturmak için parametre listesini ve çalışan yapılandırma nesnesini kullanın.

```python
# RL estimator
rl_estimator = ReinforcementLearningEstimator(
    
    # Location of source files
    source_directory='files',
    
    # Python script file
    entry_script="pong_rllib.py",
    
    # Parameters to pass to the script file
    # Defined above.
    script_params=script_params,
    
    # The Azure ML compute target set up for Ray head nodes
    compute_target=head_compute_target,
    
    # Pip packages
    pip_packages=pip_packages,
    
    # GPU usage
    use_gpu=True,
    
    # RL framework. Currently must be Ray.
    rl_framework=Ray(),
    
    # Ray worker configuration defined above.
    worker_configuration=worker_conf,
    
    # How long to wait for whole cluster to start
    cluster_coordination_timeout_seconds=3600,
    
    # Maximum time for the whole Ray job to run
    # This will cut off the run after an hour
    max_run_duration_seconds=3600,
    
    # Allow the docker container Ray runs in to make full use
    # of the shared memory available from the host OS.
    shm_size=24*1024*1024*1024
)
```

### <a name="entry-script"></a>Giriş betiği

[Giriş betiği](https://aka.ms/azure-rl-pong-script) , `pong_rllib.py` [openaı Gym ortamını](https://github.com/openai/gym/) kullanarak bir sinir ağı eğititir `PongNoFrameSkip-v4` . Openaı Gmakları, klasik atari oyunlarında pekiştirmeye dayalı öğrenme algoritmalarını test etmek için standartlaştırılmış arabirimler sağlar.

Bu örnek, [Impala](https://arxiv.org/abs/1802.01561) (önem ağırlıklı Actor-Learner mimarisi) olarak bilinen bir eğitim algoritması kullanır. IMPALA, her bireysel öğrenme aktörinin hız veya kararlılığı etkilemeden birçok işlem düğümü genelinde ölçeklendirilmesini sağlar.

[Işın ayarlama](https://ray.readthedocs.io/en/latest/tune.html) Impala çalışan görevlerini düzenler.

```python
import ray
import ray.tune as tune
from ray.rllib import train

import os
import sys

from azureml.core import Run
from utils import callbacks

DEFAULT_RAY_ADDRESS = 'localhost:6379'

if __name__ == "__main__":

    # Parse arguments
    train_parser = train.create_parser()

    args = train_parser.parse_args()
    print("Algorithm config:", args.config)

    if args.ray_address is None:
        args.ray_address = DEFAULT_RAY_ADDRESS

    ray.init(address=args.ray_address)

    tune.run(run_or_experiment=args.run,
             config={
                 "env": args.env,
                 "num_gpus": args.config["num_gpus"],
                 "num_workers": args.config["num_workers"],
                 "callbacks": {"on_train_result": callbacks.on_train_result},
                 "sample_batch_size": 50,
                 "train_batch_size": 1000,
                 "num_sgd_iter": 2,
                 "num_data_loader_buffers": 2,
                 "model": {
                    "dim": 42
                 },
             },
             stop=args.stop,
             local_dir='./logs')
```

### <a name="logging-callback-function"></a>Günlüğe kaydetme geri çağırma işlevi


Giriş betiği, ölçümleri Azure Machine Learning çalışma alanınıza kaydetmek için [özel bir RLlib geri çağırma işlevi](https://docs.ray.io/en/latest/rllib-training.html#callbacks-and-custom-metrics) tanımlamak üzere bir yardımcı program işlevi kullanır. Bu ölçümleri [izleme ve sonuçları görüntüleme](#monitor-and-view-results) bölümünde görüntülemeyi öğrenin.

```python
'''RLlib callbacks module:
    Common callback methods to be passed to RLlib trainer.
'''
from azureml.core import Run

def on_train_result(info):
    '''Callback on train result to record metrics returned by trainer.
    '''
    run = Run.get_context()
    run.log(
        name='episode_reward_mean',
        value=info["result"]["episode_reward_mean"])
    run.log(
        name='episodes_total',
        value=info["result"]["episodes_total"])
```

## <a name="submit-a-run"></a>Bir çalıştırma gönder

[Çalıştır](/python/api/azureml-core/azureml.core.run%28class%29) işlemi devam eden veya tamamlanmış işlerin çalıştırma geçmişini işler. 

```python
run = exp.submit(config=rl_estimator)
```
> [!NOTE]
> Çalıştırmanın tamamlanması 30 ila 45 dakika sürebilir.

## <a name="monitor-and-view-results"></a>Sonuçları izleme ve görüntüleme

Çalışmalarınızın durumunu gerçek zamanlı olarak görmek için Azure Machine Learning jupi pencere öğesini kullanın. Pencere öğesi iki alt çalışma gösterir: biri baş ve diğeri çalışanlar için. 

```python
from azureml.widgets import RunDetails

RunDetails(run).show()
run.wait_for_completion()
```

1. Pencere öğesinin yüklenmesini bekleyin.
1. Çalıştırmalar listesinde baş çalıştırmayı seçin.

Studio 'da ek çalışma bilgileri için **Azure Machine Learning Studio 'da çalıştırmayı görmek üzere buraya tıklayın ' ı** seçin. Çalıştırma devam ederken veya tamamlandıktan sonra bu bilgilere erişebilirsiniz.

![Çalışma ayrıntıları pencere öğesinin nasıl çalıştığını gösteren çizgi grafiği](./media/how-to-use-reinforcement-learning/pong-run-details-widget.png)

**Episode_reward_mean** çizimde, eğitim dönemi başına puanlanmış noktaların ortalama sayısı gösterilir. Eğitim aracısının başlangıçta kötü bir şekilde gerçekleştirildiğini ve tek bir noktaya sahip olmadan eşleşmelerini kaybetmesini (-21 reward_mean) görebilirsiniz. Eğitim Aracısı 100 yinelemeden, bilgisayar rakibinizi ortalama 18 noktaya ayırarak öğrendi.

Alt çalıştırmanın günlüklerine gözatıp, driver_log.txt dosyasına kaydedilmiş değerlendirme sonuçlarını görebilirsiniz. Bu ölçümler çalışma sayfasında kullanılabilir hale gelmeden birkaç dakika beklemeniz gerekebilir.

Kısa çalışmalarda, bir pekiştirmeye dayalı öğrenme aracısının bir bilgisayar opppcomponent 'a karşı çok iyi oynamasını sağlamak üzere birden çok işlem kaynağını yapılandırmayı öğrendiniz.

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, bir ıMPALA Learning Aracısı kullanarak bir pekiştirmeye dayalı öğrenme aracısını eğitme hakkında bilgi edindiniz. Ek örnekleri görmek için [Azure Machine Learning pekiştirmeye dayalı Learning GitHub deposuna](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/reinforcement-learning/README.md)gidin.