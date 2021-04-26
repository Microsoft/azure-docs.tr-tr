---
title: 'Öğretici: kendi verilerinizi kullanma'
titleSuffix: Azure Machine Learning
description: Azure Machine Learning Başlarken serisinin 4. bölümü, kendi verilerinizi uzaktan eğitim çalıştırmasında nasıl kullanacağınızı gösterir.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aminsaied
ms.author: amsaied
ms.reviewer: sgilley
ms.date: 02/11/2021
ms.custom: tracking-python, contperf-fy21q3
ms.openlocfilehash: 503d1d1220cd4704a6e70d0b7e575a70275e5e4d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105936844"
---
# <a name="tutorial-use-your-own-data-part-4-of-4"></a>Öğretici: kendi verilerinizi kullanın (4. bölüm)

Bu öğreticide, Azure Machine Learning ' de makine öğrenimi modellerini eğitmek için kendi verilerinizi karşıya yükleme ve kullanma işlemlerinin nasıl yapılacağı gösterilir.

Bu öğretici, Azure 'da Azure Machine Learning ve iş tabanlı makine öğrenimi görevlerinin temellerini öğrendiğiniz *dört bölümden oluşan bir öğretici serisinin 4. parçasıdır* . Bu öğretici, [1. Bölüm: ayarlama](tutorial-1st-experiment-sdk-setup-local.md), [Bölüm 2: "Merhaba Dünya!"](tutorial-1st-experiment-hello-world.md)ve [3. kısım: bir modeli eğitme](tutorial-1st-experiment-sdk-train.md)bölümünde tamamladığınız işi oluşturur.

[3. Bölüm 'de, bir modeli eğitme](tutorial-1st-experiment-sdk-train.md), veriler `torchvision.datasets.CIFAR10` PYTORCH API 'sindeki yerleşik yöntem aracılığıyla indirilir. Ancak çoğu durumda, kendi verilerinizi bir uzaktan eğitim çalıştırmasında kullanmak isteyeceksiniz. Bu makalede, Azure Machine Learning içinde kendi verilerinize çalışmak için kullanabileceğiniz iş akışı gösterilmektedir.

Bu öğreticide şunları yaptınız:

> [!div class="checklist"]
> * Yerel bir dizinde veri kullanmak için bir eğitim betiği yapılandırın.
> * Eğitim betiğini yerel olarak test edin.
> * Verileri Azure 'a yükleyin.
> * Bir denetim betiği oluşturun.
> * Yeni Azure Machine Learning kavramlarını anlayın (parametreler, veri kümeleri, veri depoları geçirerek).
> * Eğitim betiğinizi gönder ve Çalıştır.
> * Kod çıktılarınızı bulutta görüntüleyin.

## <a name="prerequisites"></a>Önkoşullar

Önceki öğreticide oluşturulan pytorch ortamının veri ve güncelleştirilmiş bir sürümü gerekir.  Bu adımları tamamladığınızdan emin olun:

1. [Eğitim betiğini oluşturma](tutorial-1st-experiment-sdk-train.md#create-training-scripts)
1. [Yeni bir Python ortamı oluştur](tutorial-1st-experiment-sdk-train.md#environment)
1. [Yerel olarak test etme](tutorial-1st-experiment-sdk-train.md#test-local)
1. [Conda ortam dosyasını güncelleştirme](tutorial-1st-experiment-sdk-train.md#update-the-conda-environment-file)

## <a name="adjust-the-training-script"></a>Eğitim betiğini ayarlama

Artık eğitim betiğinizin (öğretici/src/tren. Kay) Azure Machine Learning çalışıyor ve model performansını izleyebilirsiniz. Bağımsız değişkenler sunarak eğitim betiğini parametreleştirim. Bağımsız değişkenlerin kullanılması, farklı hiper parametreleri kolayca karşılaştırmanıza imkan tanır.

Eğitim betiğimiz artık her çalıştırmada CIFAR10 veri kümesini indirmek üzere ayarlanmıştır. Aşağıdaki Python kodu bir dizinden verileri okumak için ayarlandı.

>[!NOTE] 
> `argparse`Betik kullanımı betiği kullanılır.

:::code language="python" source="~/MachineLearningNotebooks/tutorials/get-started-day1/code/pytorch-cifar10-your-data/train.py":::

### <a name="understanding-the-code-changes"></a>Kod değişikliklerini anlama

İçindeki kod `train.py` `argparse` ,, ve ayarlamak için kitaplığı kullandı `data_path` `learning_rate` `momentum` .

```python
# .... other code
parser = argparse.ArgumentParser()
parser.add_argument('--data_path', type=str, help='Path to the training data')
parser.add_argument('--learning_rate', type=float, default=0.001, help='Learning rate for SGD')
parser.add_argument('--momentum', type=float, default=0.9, help='Momentum for SGD')
args = parser.parse_args()
# ... other code
```

Ayrıca, `train.py` komut dosyası, Kullanıcı tanımlı parametreleri kullanmak üzere İyileştiriciyi güncelleştirmek için uyarlanmıştır:

```python
optimizer = optim.SGD(
    net.parameters(),
    lr=args.learning_rate,     # get learning rate from command-line argument
    momentum=args.momentum,    # get momentum from command-line argument
)
```

> [!div class="nextstepaction"]
> [Bir sorunla karşılaşdığım](https://www.research.net/r/7C6W7BQ?issue=adjust-training-script) [eğitim betiğini ayarladım](?success=adjust-training-script#test-locally)

## <a name="test-the-script-locally"></a><a name="test-locally"></a> Betiği yerel olarak test etme

Komut dosyası artık bir bağımsız değişken olarak _veri yolunu_ kabul eder. İle başlamak için, yerel olarak test edin. Öğretici dizin yapınıza adlı bir klasör ekleyin `data` . Dizin yapınız şöyle görünmelidir:

:::image type="content" source="media/tutorial-1st-experiment-bring-data/directory-structure.png" alt-text="Dizin yapısı. azureml, veri ve src alt dizinlerini gösterir":::

1. Geçerli ortamdan çıkın.

    ```bash
    conda deactivate

1. Now create and activate the new environment.  This will rebuild the pytorch-aml-env with the [updated environment file](tutorial-1st-experiment-sdk-train.md#update-the-conda-environment-file)


    ```bash
    conda env create -f .azureml/pytorch-env.yml    # create the new conda environment with updated dependencies
    ```

    ```bash
    conda activate pytorch-aml-env          # activate new conda environment
    ```

1. Son olarak, değiştirilen eğitim betiğini yerel olarak çalıştırın.

    ```bash
    python src/train.py --data_path ./data --learning_rate 0.003 --momentum 0.92
    ```

Verilere yerel bir yol geçirerek CIFAR10 veri kümesini indirmek zorunda kalmaktan kaçının. Eğitim betiğinden, _öğrenme oranı_ ve _itici güç_ ayarlama hiperparametreleri için de farklı değerlerle denemeler yapabilirsiniz.

> [!div class="nextstepaction"]
> [Bir sorunla karşılaşdığım](https://www.research.net/r/7C6W7BQ?issue=test-locally) [betiği yerel olarak test ediyorum](?success=test-locally#upload)

## <a name="upload-the-data-to-azure"></a><a name="upload"></a> Verileri Azure 'a yükleme

Bu betiği Azure Machine Learning çalıştırmak için eğitim verilerinizi Azure 'da kullanılabilir hale getirmeniz gerekir. Azure Machine Learning çalışma alanınız _varsayılan_ bir veri deposu ile donatılmıştır. Bu, eğitim verilerinizi depolayabileceğiniz bir Azure Blob depolama hesabıdır.

>[!NOTE] 
> Azure Machine Learning, verilerinizi depolayan diğer bulut tabanlı veri depolarına bağlanmanızı sağlar. Daha ayrıntılı bilgi için bkz. [veri depoları belgeleri](./concept-data.md).  

Dizininde adlı yeni bir Python denetim betiği oluşturun `05-upload-data.py` `tutorial` :

:::code language="python" source="~/MachineLearningNotebooks/tutorials/get-started-day1/IDE-users/05-upload-data.py":::

`target_path`Değer, CIFAR10 verilerinin karşıya yükleneceği veri deposundaki yolu belirtir.

>[!TIP] 
> Verileri karşıya yüklemek için Azure Machine Learning kullanırken, geçici dosyaları karşıya yüklemek için [Azure Depolama Gezgini](https://azure.microsoft.com/features/storage-explorer/) kullanabilirsiniz. Bir ETL aracına ihtiyacınız varsa, verilerinizi Azure 'a almak için [Azure Data Factory](../data-factory/introduction.md) kullanabilirsiniz.

Etkinleştirilen *Tutorial1* Conda ortamı olan pencerede, verileri karşıya yüklemek için Python dosyasını çalıştırın. (Karşıya yükleme, 60 saniyeden kısa bir sürede hızlı olmalıdır.)

```bash
python 05-upload-data.py
```

Aşağıdaki Standart çıktıyı görmeniz gerekir:

```txt
Uploading ./data\cifar-10-batches-py\data_batch_2
Uploaded ./data\cifar-10-batches-py\data_batch_2, 4 files out of an estimated total of 9
.
.
Uploading ./data\cifar-10-batches-py\data_batch_5
Uploaded ./data\cifar-10-batches-py\data_batch_5, 9 files out of an estimated total of 9
Uploaded 9 files
```

> [!div class="nextstepaction"]
> [Bir sorunla karşılaşdığım](https://www.research.net/r/7C6W7BQ?issue=upload-data) [verileri karşıya yükledim](?success=upload-data#control-script)

## <a name="create-a-control-script"></a><a name="control-script"></a> Denetim betiği oluşturma

Daha önce yaptığınız gibi, adlı yeni bir Python denetim betiği oluşturun `06-run-pytorch-data.py` :

```python
# 06-run-pytorch-data.py
from azureml.core import Workspace
from azureml.core import Experiment
from azureml.core import Environment
from azureml.core import ScriptRunConfig
from azureml.core import Dataset

if __name__ == "__main__":
    ws = Workspace.from_config()
    datastore = ws.get_default_datastore()
    dataset = Dataset.File.from_files(path=(datastore, 'datasets/cifar10'))

    experiment = Experiment(workspace=ws, name='day1-experiment-data')

    config = ScriptRunConfig(
        source_directory='./src',
        script='train.py',
        compute_target='cpu-cluster',
        arguments=[
            '--data_path', dataset.as_named_input('input').as_mount(),
            '--learning_rate', 0.003,
            '--momentum', 0.92],
    )
    # set up pytorch environment
    env = Environment.from_conda_specification(
        name='pytorch-env',
        file_path='./.azureml/pytorch-env.yml'
    )
    config.run_config.environment = env

    run = experiment.submit(config)
    aml_url = run.get_portal_url()
    print("Submitted to compute cluster. Click link below")
    print("")
    print(aml_url)
```

### <a name="understand-the-code-changes"></a>Kod değişikliklerini anlayın

Denetim betiği, [Bu serinin 3. bölümtekine](tutorial-1st-experiment-sdk-train.md)benzer ve aşağıdaki yeni satırlarla benzerdir:

:::row:::
   :::column span="":::
      `dataset = Dataset.File.from_files( ... )`
   :::column-end:::
   :::column span="2":::
      Veri [kümesi](/python/api/azureml-core/azureml.core.dataset.dataset) , Azure Blob depolama alanına yüklediğiniz verilere başvurmak için kullanılır. Veri kümeleri, verilerinizin üzerinde güvenilirlik ve güvenilirliği artırmak için tasarlanan bir Özet katmandır.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `config = ScriptRunConfig(...)`
   :::column-end:::
   :::column span="2":::
      [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig) , içine geçirilecek bağımsız değişkenlerin bir listesini içerecek şekilde değiştirilir `train.py` . `dataset.as_named_input('input').as_mount()`Bağımsız değişkeni belirtilen dizinin işlem hedefine _bağlanması_ anlamına gelir.
   :::column-end:::
:::row-end:::

> [!div class="nextstepaction"]
> [Bir sorunla karşılaşdığım](https://www.research.net/r/7C6W7BQ?issue=control-script) [Denetim betiğini](?success=control-script#submit-to-cloud) oluşturdum

## <a name="submit-the-run-to-azure-machine-learning"></a><a name="submit-to-cloud"></a> Çalıştırmayı Azure Machine Learning gönder

Şimdi yeni yapılandırmayı kullanmak için çalıştırmayı yeniden gönderin:

```bash
python 06-run-pytorch-data.py
```

Bu kod, Azure Machine Learning Studio 'daki deneye bir URL 'YI yazdırır. Bu bağlantıya giderseniz, kodunuzun çalıştığını görebileceksiniz.

> [!div class="nextstepaction"]
> [](?success=submit-to-cloud#inspect-log) [Bir sorunla Karşılaşdığım](https://www.research.net/r/7C6W7BQ?issue=submit-to-cloud) çalışmayı yeniden aldım

### <a name="inspect-the-log-file"></a><a name="inspect-log"></a> Günlük dosyasını inceleyin

Studio 'da, deneme çalıştırmasına (önceki URL çıkışını seçerek) ve ardından **çıktılar + Günlükler**' e gidin. Dosyayı seçin `70_driver_log.txt` . Aşağıdaki çıkışı görmeniz gerekir:

```txt
Processing 'input'.
Processing dataset FileDataset
{
  "source": [
    "('workspaceblobstore', 'datasets/cifar10')"
  ],
  "definition": [
    "GetDatastoreFiles"
  ],
  "registration": {
    "id": "XXXXX",
    "name": null,
    "version": null,
    "workspace": "Workspace.create(name='XXXX', subscription_id='XXXX', resource_group='X')"
  }
}
Mounting input to /tmp/tmp9kituvp3.
Mounted input to /tmp/tmp9kituvp3 as folder.
Exit __enter__ of DatasetContextManager
Entering Run History Context Manager.
Current directory:  /mnt/batch/tasks/shared/LS_root/jobs/dsvm-aml/azureml/tutorial-session-3_1600171983_763c5381/mounts/workspaceblobstore/azureml/tutorial-session-3_1600171983_763c5381
Preparing to call script [ train.py ] with arguments: ['--data_path', '$input', '--learning_rate', '0.003', '--momentum', '0.92']
After variable expansion, calling script [ train.py ] with arguments: ['--data_path', '/tmp/tmp9kituvp3', '--learning_rate', '0.003', '--momentum', '0.92']

Script type = None
===== DATA =====
DATA PATH: /tmp/tmp9kituvp3
LIST FILES IN DATA PATH...
['cifar-10-batches-py', 'cifar-10-python.tar.gz']
```

Değiştirilebileceğini

- Azure Machine Learning, blob depolamayı sizin için otomatik olarak işlem kümesine bağlamıştır.
- ``dataset.as_named_input('input').as_mount()``Denetim betikte kullanılan, bağlama noktasına çözümlenir.

> [!div class="nextstepaction"]
> [Bir sorunla karşılaşdığım](https://www.research.net/r/7C6W7BQ?issue=inspect-log) [günlük dosyasını incedum](?success=inspect-log#clean-up-resources)

## <a name="clean-up-resources"></a>Kaynakları temizleme

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

Ayrıca, kaynak grubunu koruyabilir ancak tek bir çalışma alanını silebilirsiniz. Çalışma alanı özelliklerini görüntüleyin ve **Sil**' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, kullanarak Azure 'a nasıl veri yükleneceğini gördük `Datastore` . Çalışma alanınız için bulut depolaması olarak hizmet veren veri deposu, verilerinizi korumak için kalıcı ve esnek bir yer sağlar.

Komut satırı aracılığıyla bir veri yolunu kabul etmek için eğitim betiğinizi nasıl değiştireceğiniz gördünüz. Kullanarak `Dataset` , uzak çalıştırmaya bir dizin bağlayabilmiş olabilirsiniz. 

Artık bir modelinize sahip olduğunuza göre şunları öğrenin:

* [Modelleri Azure Machine Learning ile dağıtma](how-to-deploy-and-where.md).
