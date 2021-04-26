---
title: 'Öğretici: ilk makine öğrenimi modelinizi eğitme-Python'
titleSuffix: Azure Machine Learning
description: Azure Machine Learning Başlarken serisinin 3. bölümü, makine öğrenimi modelinin nasıl eğitede olduğunu gösterir.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aminsaied
ms.author: amsaied
ms.reviewer: sgilley
ms.date: 02/11/2021
ms.custom: devx-track-python, contperf-fy21q3
ms.openlocfilehash: 7e035406ce220728c316aaa2005e0f4d6089b5e2
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105935524"
---
# <a name="tutorial-train-your-first-machine-learning-model-part-3-of-4"></a>Öğretici: ilk makine öğrenimi modelinizi eğitme (Bölüm 3/4)

Bu öğreticide, Azure Machine Learning bir makine öğrenimi modelinin nasıl eğiteyapılacağı gösterilmektedir.

Bu öğretici, Azure 'da Azure Machine Learning ve iş tabanlı makine öğrenimi görevlerinin temellerini öğrendiğiniz *dört bölümden oluşan bir öğretici serisinin 3. parçasıdır* . Bu öğretici, [1. Bölüm: ayarlama](tutorial-1st-experiment-sdk-setup-local.md) ve [Bölüm 2: "Hello World!" çalıştırmak](tutorial-1st-experiment-hello-world.md) için tamamladığınız işleri oluşturur serisi.

Bu öğreticide, bir makine öğrenimi modelini gösteren bir komut dosyası göndererek sonraki adıma geçin. Bu örnek, yerel hata ayıklama ve uzak çalıştırmalar arasında Azure Machine Learning tutarlı davranışı nasıl bir şekilde hareket eder.

Bu öğreticide şunları yaptınız:

> [!div class="checklist"]
> * Bir eğitim betiği oluşturun.
> * Azure Machine Learning ortamını tanımlamak için Conda kullanın.
> * Bir denetim betiği oluşturun.
> * Azure Machine Learning sınıflarını anlayın ( `Environment` , `Run` , `Metrics` ).
> * Eğitim betiğinizi gönder ve Çalıştır.
> * Kod çıktılarınızı bulutta görüntüleyin.
> * Azure Machine Learning için günlük ölçümleri.
> * Ölçümünüzü bulutta görüntüleyin.

## <a name="prerequisites"></a>Önkoşullar

- Python sanal ortamlarını yönetmek ve paketleri yüklemek için [Anaconda](https://www.anaconda.com/download/) veya [miniconda](https://www.anaconda.com/download/) .
- Serinin [part1](tutorial-1st-experiment-sdk-setup-local.md) ve [Bölüm 2](tutorial-1st-experiment-hello-world.md) ' nin tamamlanması.

## <a name="create-training-scripts"></a>Eğitim betikleri oluşturma

İlk olarak, sinir ağ mimarisini bir dosyada tanımlarsınız `model.py` . Tüm eğitim kodunuz `src` , dahil olmak üzere alt dizine gider `model.py` .

Aşağıdaki kod, PyTorch kaynağından [Bu giriş örneğinde](https://pytorch.org/tutorials/beginner/blitz/cifar10_tutorial.html) alınır. Azure Machine Learning kavramların yalnızca PyTorch değil, herhangi bir makine öğrenimi kodu için uygulanacağını unutmayın.

:::code language="python" source="~/MachineLearningNotebooks/tutorials/get-started-day1/IDE-users/src/model.py":::

Daha sonra eğitim betiğini tanımlarsınız. Bu betik, PyTorch API 'lerini kullanarak CIFAR10 veri kümesini indirir `torchvision.dataset` , içinde tanımlanan ağı ayarlar `model.py` ve standart SGD ve çapraz entropi kaybını kullanarak iki dönemler için bunu yapar.

`train.py`Alt dizinde bir betik oluşturun `src` :

:::code language="python" source="~/MachineLearningNotebooks/tutorials/get-started-day1/IDE-users/src/train.py":::

Artık şu dizin yapısına sahipsiniz:

:::image type="content" source="media/tutorial-1st-experiment-sdk-train/directory-structure.png" alt-text="Dizin yapısı src alt dizininde train.py gösterir":::


> [!div class="nextstepaction"]
> [Bir sorunla karşılaşdığım](https://www.research.net/r/7CTJQQN?issue=create-scripts) [eğitim betiklerini oluşturdum](?success=create-scripts#environment)

## <a name="create-a-new-python-environment"></a><a name="environment"></a> Yeni bir Python ortamı oluştur

Gizli dizinde adlı bir dosya oluşturun `pytorch-env.yml` `.azureml` :

:::code language="yml" source="~/MachineLearningNotebooks/tutorials/get-started-day1/IDE-users/environments/pytorch-env.yml":::

Bu ortam, modelinize ve eğitim betiğinizin gerektirdiği tüm bağımlılıklara sahiptir. Python için Azure Machine Learning SDK 'nın bağımlılığı olmadığına dikkat edin.

> [!div class="nextstepaction"]
> [Bir sorunla karşılaşdığım](https://www.research.net/r/7CTJQQN?issue=create-env-file) [ortam dosyasını oluşturdum](?success=create-env-file#test-local)

## <a name="test-locally"></a><a name="test-local"></a> Yerel olarak test etme

Bir Terminal veya Anaconda Istem penceresinde, komut dosyanızı yeni ortamda yerel olarak test etmek için aşağıdaki kodu kullanın.  

```bash
conda deactivate                                # If you are still using the tutorial environment, exit it
conda env create -f .azureml/pytorch-env.yml    # create the new Conda environment
conda activate pytorch-env                      # activate new Conda environment
python src/train.py                             # train model
```

Bu betiği çalıştırdıktan sonra adlı bir dizine indirilen verileri görürsünüz `tutorial/data` .

> [!div class="nextstepaction"]
> [Yerel olarak](?success=test-local#create-local) [bir sorunla karşılaşdığım](https://www.research.net/r/7CTJQQN?issue=test-local) kodu çalıştırdım

## <a name="create-the-control-script"></a><a name="create-local"></a> Denetim betiğini oluşturma

Aşağıdaki denetim betiği ve "Hello World!" göndermek için kullandığınız biri arasındaki fark , ortamı ayarlamak için birkaç ek satır eklemenizi sağlar.

Adlı dizinde yeni bir Python dosyası oluşturun `tutorial` `04-run-pytorch.py` :

```python
# 04-run-pytorch.py
from azureml.core import Workspace
from azureml.core import Experiment
from azureml.core import Environment
from azureml.core import ScriptRunConfig

if __name__ == "__main__":
    ws = Workspace.from_config()
    experiment = Experiment(workspace=ws, name='day1-experiment-train')
    config = ScriptRunConfig(source_directory='./src',
                             script='train.py',
                             compute_target='cpu-cluster')

    # set up pytorch environment
    env = Environment.from_conda_specification(
        name='pytorch-env',
        file_path='./.azureml/pytorch-env.yml'
    )
    config.run_config.environment = env

    run = experiment.submit(config)

    aml_url = run.get_portal_url()
    print(aml_url)
```    
    
### <a name="understand-the-code-changes"></a>Kod değişikliklerini anlayın

:::row:::
   :::column span="":::
      `env = ...`
   :::column-end:::
   :::column span="2":::
      Azure Machine Learning, denemeleri çalıştırmaya yönelik tekrarlanabilir, sürümlü bir Python ortamını temsil eden bir [ortam](/python/api/azureml-core/azureml.core.environment.environment) kavramı sağlar. Yerel bir Conda veya PIP ortamından ortam oluşturmak kolaydır.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `config.run_config.environment = env`
   :::column-end:::
   :::column span="2":::
      Ortamı [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig)'e ekler.
   :::column-end:::
:::row-end:::

> [!div class="nextstepaction"]
> [Bir sorunla karşılaşdığım](https://www.research.net/r/7CTJQQN?issue=control-script) [Denetim betiğini](?success=control-script#submit) oluşturdum


## <a name="submit-the-run-to-azure-machine-learning"></a><a name="submit"></a> Çalıştırmayı Azure Machine Learning gönder

Python için Azure Machine Learning SDK 'Sı yüklü olan *öğretici* ortamına geri dönün. Eğitim kodu bilgisayarınızda çalışmadığından, PyTorch ' ın yüklü olması gerekmez.  Ancak, `azureml-sdk` *öğretici* ortamında olan öğesine ihtiyacınız vardır.

```bash
conda deactivate
conda activate tutorial
python 04-run-pytorch.py
```

>[!NOTE] 
> Bu betiği ilk kez çalıştırdığınızda Azure Machine Learning, PyTorch ortamınızdan yeni bir Docker görüntüsü oluşturacak. Tüm çalıştırmanın tamamlanması 5 ila 10 dakika sürebilir. 
>
> Docker Derleme günlüklerini Azure Machine Learning Studio 'da görebilirsiniz. Studio bağlantısını izleyin, **çıktılar + Günlükler** sekmesini seçin ve ardından öğesini seçin `20_image_build_log.txt` .
>
> Bu görüntü daha hızlı çalışmasını sağlamak için gelecekteki çalışmalarda yeniden kullanılacaktır.

Görüntünüz oluşturulduktan sonra `70_driver_log.txt` eğitim betiğiniz çıktıyı görmek için seçin.

```txt
Downloading https://www.cs.toronto.edu/~kriz/cifar-10-python.tar.gz to ./data/cifar-10-python.tar.gz
...
Files already downloaded and verified
epoch=1, batch= 2000: loss 2.19
epoch=1, batch= 4000: loss 1.82
epoch=1, batch= 6000: loss 1.66
epoch=1, batch= 8000: loss 1.58
epoch=1, batch=10000: loss 1.52
epoch=1, batch=12000: loss 1.47
epoch=2, batch= 2000: loss 1.39
epoch=2, batch= 4000: loss 1.38
epoch=2, batch= 6000: loss 1.37
epoch=2, batch= 8000: loss 1.33
epoch=2, batch=10000: loss 1.31
epoch=2, batch=12000: loss 1.27
Finished Training
```

> [!WARNING]
> Bir hata görürseniz `Your total snapshot size exceeds the limit` , `data` Dizin ' `source_directory` de kullanılan değerde bulunur `ScriptRunConfig` .
>
> `data`Dışına Taşı `src` .

Ortamlar, ile bir çalışma alanına kaydedilebilir `env.register(ws)` . Daha sonra kolayca paylaşılabilir, yeniden kullanılabilir ve sürümlenebilir. Ortamlar, önceki sonuçları yeniden oluşturulmasını ve ekibinizle işbirliği yapmayı kolaylaştırır.

Azure Machine Learning Ayrıca, seçkin ortamların bir koleksiyonunu tutar. Bu ortamlar ortak makine öğrenimi senaryolarını kapsar ve önbelleğe alınmış Docker görüntüleri tarafından desteklenir. Önbelleğe alınmış Docker görüntüleri, ilk uzak çalıştırmayı daha hızlı hale getirir.

Kısacası, kayıtlı ortamların kullanılması zamandan tasarruf edebilir! Daha fazla bilgi için [ortamları nasıl kullanacağınızı](./how-to-use-environments.md) okuyun.

> [!div class="nextstepaction"]
> [Bir sorunla karşılaşdığım](https://www.research.net/r/7CTJQQN?issue=test-w-environment) [çalışmayı gönderdim](?success=test-w-environment#log)

## <a name="log-training-metrics"></a><a name="log"></a> Günlük eğitimi ölçümleri

Artık Azure Machine Learning bir model eğitimine sahip olduğunuza göre bazı performans ölçümlerini izlemeye başlayın.

Geçerli eğitim betiği, ölçümleri terminalden yazdırır. Azure Machine Learning, ölçümleri daha fazla işlevle günlüğe kaydetmek için bir mekanizma sağlar. Birkaç satır kod ekleyerek, Studio 'daki ölçümleri görselleştirme ve birden çok çalıştırma arasında ölçümleri karşılaştırma imkanına sahip olursunuz.

### <a name="modify-trainpy-to-include-logging"></a>`train.py`Günlüğe kaydetmeyi dahil etmek için değiştirin

`train.py`Betiğinizi iki satırlık kod satırı içerecek şekilde değiştirin:

:::code language="python" source="~/MachineLearningNotebooks/tutorials/get-started-day1/code/pytorch-cifar10-train-with-logging/train.py":::


#### <a name="understand-the-additional-two-lines-of-code"></a>Kodun ek iki satırını anlayın

' De `train.py` , yöntemini kullanarak, çalışma nesnesine eğitim betiğinin _içinden_ erişirsiniz `Run.get_context()` ve ölçümleri günlüğe kaydetmek için kullanabilirsiniz:

```python
# in train.py
run = Run.get_context()

...

run.log('loss', loss)
```

Azure Machine Learning ölçümler şunlardır:

- Deneme ve çalıştırma tarafından düzenlenmiştir, bu nedenle ölçümleri izlemek ve karşılaştırmak kolaydır.
- , Studio 'da eğitim performansını görselleştirebilmeniz için bir kullanıcı arabirimi ile donatılmış.
- Ölçeklendirilmesi için tasarlanan, yüzlerce denemeleri çalıştırırken bu avantajları de koruyabilirsiniz.

> [!div class="nextstepaction"]
> [Bir sorunla karşılaştım](https://www.research.net/r/7CTJQQN?issue=modify-train) [train.py değiştirdim](?success=modify-train#log)

### <a name="update-the-conda-environment-file"></a>Conda ortam dosyasını güncelleştirme

`train.py`Komut dosyası yeni bir bağımlılık almış `azureml.core` . `pytorch-env.yml`Bu değişikliği yansıtacak şekilde güncelleştirin:

:::code language="python" source="~/MachineLearningNotebooks/tutorials/get-started-day1/configuration/pytorch-aml-env.yml":::

> [!div class="nextstepaction"]
> [Bir sorunla karşılaşdığım](https://www.research.net/r/7CTJQQN?issue=update-environment) [ortam dosyasını güncelleştirdim](?success=update-environment#submit-again)

### <a name="submit-the-run-to-azure-machine-learning"></a><a name="submit-again"></a> Çalıştırmayı Azure Machine Learning gönder
Bu betiği bir kez daha gönder:

```bash
python 04-run-pytorch.py
```

Bu kez, Studio 'yu ziyaret ettiğinizde, artık model eğitimi kaybından canlı güncelleştirmeleri görebileceğiniz **ölçümler** sekmesine gidin!

:::image type="content" source="media/tutorial-1st-experiment-sdk-train/logging-metrics.png" alt-text="Ölçümler sekmesinde eğitim kaybı grafiği.":::

> [!div class="nextstepaction"]
> [](?success=resubmit-with-logging#next-steps) [Bir sorunla Karşılaşdığım](https://www.research.net/r/7CTJQQN?issue=resubmit-with-logging) çalışmayı yeniden aldım

## <a name="next-steps"></a>Sonraki adımlar

Bu oturumda, temel bir "Hello World!" üzerinden yükselttiniz belirli bir Python ortamının çalışmasını gerektiren daha gerçekçi bir eğitim betiğinin betiği. Azure Machine Learning ortamları ile buluta yerel bir Conda ortamını nasıl alacağınızı gördünüz. Son olarak, Azure Machine Learning ölçümleri günlüğe kaydetmek için birkaç satır kod satırını gördünüz.

[Bir PIP requirements.txt](/python/api/azureml-core/azureml.core.environment.environment#from-pip-requirements-name--file-path-) dosyasından veya [var olan bir yerel conda ortamından](/python/api/azureml-core/azureml.core.environment.environment#from-existing-conda-environment-name--conda-environment-name-)Azure Machine Learning ortamlar oluşturmanın başka yolları da vardır.

Bir sonraki oturumda, CIFAR10 veri kümesini Azure 'a yükleyerek Azure Machine Learning verilerle nasıl çalışacaksınız görürsünüz.

> [!div class="nextstepaction"]
> [Öğretici: kendi verilerinizi getirin](tutorial-1st-experiment-bring-data.md)

>[!NOTE] 
> Öğretici serisini burada bitirebilmeniz ve bir sonraki adımda ilerlemeniz istiyorsanız [kaynaklarınızı temizlemeyi](tutorial-1st-experiment-bring-data.md#clean-up-resources)unutmayın.
