---
title: Tahmini ile ml modellerini eğitme
titleSuffix: Azure Machine Learning
description: Azure Machine Learning Estimator sınıfını kullanarak geleneksel makine öğrenimi ve derin öğrenme modellerine yönelik tek düğümlü ve dağıtılmış eğitime nasıl gerçekleştirileceğini öğrenin
ms.author: maxluk
author: maxluk
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: sgilley
ms.date: 11/08/2019
ms.custom: seodec18
ms.openlocfilehash: 9bb22a564f52dfcdb3fbec6d842e452ca416059f
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73961709"
---
# <a name="train-models-with-azure-machine-learning-using-estimator"></a>Tahmin aracı kullanarak modelleri Azure Machine Learning eğitme
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Azure Machine Learning, bir [RunConfiguration nesnesi](how-to-set-up-training-targets.md#whats-a-run-configuration) ve bir [ScriptRunConfig nesnesi](how-to-set-up-training-targets.md#submit)kullanarak eğitim betiğinizi [çeşitli işlem hedeflerine](how-to-set-up-training-targets.md#compute-targets-for-training)kolayca gönderebilirsiniz. Bu model size çok fazla esneklik ve en yüksek denetim sağlar.

Derin öğrenme modeli eğitimini kolaylaştırmak için, Azure Machine Learning Python SDK 'sı, kullanıcıların kolayca çalıştırma yapılandırması oluşturmasına olanak tanıyan, daha yüksek düzeyde bir soyutlama ve tahmin aracı sınıfı sağlar. Seçtiğiniz herhangi bir öğrenme çerçevesini (scikit-öğren gibi) kullanarak eğitim betiğini, sizin yerel makineniz, Azure 'da tek bir VM veya Azure 'da bir GPU kümesi olsun, seçtiğiniz herhangi bir bilgi işlem hedefinde göndermek için genel bir [Estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py) oluşturabilir ve kullanabilirsiniz. Pytorch, TensorFlow ve Chainer görevleri için Azure Machine Learning Ayrıca bu çerçeveleri kullanmayı basitleştirmek için, ilgili [pytorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py), [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)ve [Chainer](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) tahmini 'ı da sağlar.

## <a name="train-with-an-estimator"></a>Bir tahmin ile eğitme

Oluşturduktan sonra [çalışma](concept-workspace.md) ve ayarlayın, [geliştirme ortamı](how-to-configure-environment.md), Azure Machine Learning modeli, aşağıdaki adımları içerir:  
1. [Uzaktan işlem hedefi](how-to-set-up-training-targets.md) oluşturma (yerel bilgisayarı işlem hedefi olarak da kullanabilirsiniz)
2. [Eğitim verilerinizi](how-to-access-data.md) veri deposuna yükleme (isteğe bağlı)
3. Oluşturma, [eğitim betiği](tutorial-train-models-with-aml.md#create-a-training-script)
4. Oluşturma bir `Estimator` nesnesi
5. Tahmin aracı 'ı çalışma alanı altındaki bir deneme nesnesine gönderme

Bu makalede, adım 4-5 üzerinde odaklanır. Adım 1-3 başvurmak için [model Öğreticisi eğitme](tutorial-train-models-with-aml.md) örneği.

### <a name="single-node-training"></a>Tek düğümlü eğitim

Kullanım bir `Estimator` bir scikit için uzak işlem azure'da çalışan bir tek düğümlü eğitim-model öğrenin. Oluşturmuş olmanız, [hedef işlem](how-to-set-up-training-targets.md#amlcompute) nesne `compute_target` ve [veri deposu](how-to-access-data.md) nesne `ds`.

```Python
from azureml.train.estimator import Estimator

script_params = {
    # to mount files referenced by mnist dataset
    '--data-folder': ds.as_named_input('mnist').as_mount(),
    '--regularization': 0.8
}

sk_est = Estimator(source_directory='./my-sklearn-proj',
                   script_params=script_params,
                   compute_target=compute_target,
                   entry_script='train.py',
                   conda_packages=['scikit-learn'])
```

Bu kod parçacığı için aşağıdaki parametreleri belirtir `Estimator` Oluşturucusu.

Parametre | Açıklama
--|--
`source_directory`| Eğitim işine yönelik gerekli kodunuzun tamamını içeren yerel dizin. Bu klasör yerel makinenizden uzak işlem 'a kopyalanmış olur.
`script_params`| Eğitim betiğe geçirilecek komut satırı bağımsız değişkenlerini belirten sözlük, `<command-line argument, value>` çiftleri biçiminde `entry_script`. `script_params`bir ayrıntılı bayrak belirtmek için `<command-line argument, "">`kullanın.
`compute_target`| Eğitim betiğinizin çalışacağı uzaktan işlem hedefi, bu durumda bir Azure Machine Learning Işlem ([Amlcompute](how-to-set-up-training-targets.md#amlcompute)) kümesi olur. (AmlCompute kümesi yaygın olarak kullanılan hedef olsa da, Azure VM 'Leri veya hatta yerel bilgisayar gibi diğer bilgi işlem hedefi türlerini de seçebilirsiniz.)
`entry_script`| FilePath (göreli `source_directory`) eğitim betiğin uzak işlem üzerinde çalıştırılacak. Bu dosya ve bağımlı olduğu tüm ek dosyalar bu klasörde bulunmalıdır.
`conda_packages`| Eğitim betiğinizi gerekli conda aracılığıyla yüklenecek Python paketleri listesi.  

Oluşturucunun, gerekli tüm PIP paketleri için kullandığınız `pip_packages` adlı başka bir parametresi vardır.

Oluşturduğunuza göre `Estimator` nesne, bir çağrı ile uzak işlem üzerinde çalıştırılacak eğitim işini gönderme `submit` işlevini, [deneme](concept-azure-machine-learning-architecture.md#experiments) nesne `experiment`. 

```Python
run = experiment.submit(sk_est)
print(run.get_portal_url())
```

> [!IMPORTANT]
> **Özel klasörler** iki klasör *çıkarır* ve *günlükleri*, Azure Machine Learning tarafından özel olarak değerlendirilmesi alırsınız. Klasörleri için dosyaları yazdığınızda eğitim sırasında *çıkarır* ve *günlükleri* kök dizine göre olan (`./outputs` ve `./logs`sırasıyla), dosyaları otomatik olarak ayarlanır çalıştırmanız tamamlandıktan sonra onlara yönelik erişimi olması için çalıştırma geçmişi karşıya yükleyin.
>
> Oluşturmak için (örneğin, model dosyaları, kontrol noktaları, veri dosyalarını veya çizilen görüntüsü) eğitim sırasında yapıtları için bu yazma `./outputs` klasör.
>
> Benzer şekilde, eğitim çalıştırınızdan `./logs` klasöre herhangi bir günlük yazabilirsiniz. Azure Machine Learning'ın kullanmaya [TensorBoard tümleştirme](https://aka.ms/aml-notebook-tb) bu klasöre TensorBoard günlüklerinizi aldığınızdan emin olun. Çalıştırma sürerken TensorBoard başlatın ve bu günlüklerin akışını mümkün olacaktır.  Daha sonra ayrıca günlükleri, önceki çalıştırmaları birini geri yüklenmesi mümkün olacaktır.
>
> Örneğin, yazılan bir dosyayı indirmek için *çıkarır* klasör yerel makinenize sonra uzak eğitim çalıştırın: `run.download_file(name='outputs/my_output_file', output_file_path='my_destination_path')`

### <a name="distributed-training-and-custom-docker-images"></a>Dağıtılmış eğitimi ve özel Docker görüntüleri

Sizin gerçekleştirdiğiniz ile iki ek eğitim senaryosu vardır `Estimator`:
* Özel Docker görüntüsü kullanma
* Çok düğümlü bir küme üzerinde dağıtılmış eğitimi

Aşağıdaki kod, bir keras modeli için dağıtılmış eğitimin nasıl alınacağını gösterir. Ayrıca, varsayılan Azure Machine Learning görüntülerini kullanmak yerine, Docker Hub 'dan eğitim için `continuumio/miniconda` özel bir Docker görüntüsü belirtir.

Oluşturmuş olmanız, [hedef işlem](how-to-set-up-training-targets.md#amlcompute) nesne `compute_target`. Tahmin aracı gibi oluşturun:

```Python
from azureml.train.estimator import Estimator
from azureml.core.runconfig import MpiConfiguration

estimator = Estimator(source_directory='./my-keras-proj',
                      compute_target=compute_target,
                      entry_script='train.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_training=MpiConfiguration(),        
                      conda_packages=['tensorflow', 'keras'],
                      custom_docker_image='continuumio/miniconda')
```

Yukarıdaki kod için aşağıdaki yeni parametreleri sunan `Estimator` Oluşturucusu:

Parametre | Açıklama | Varsayılan
--|--|--
`custom_docker_image`| Kullanmak istediğiniz resmin adı. Yalnızca genel docker depolardaki (Bu durum Docker hub'daki) görüntüleri sağlar. Özel bir Docker deposundan bir görüntü kullanmak için, onun yerine oluşturucunun `environment_definition` parametresini kullanın. [Örneğe bakın](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/how-to-use-estimator/how-to-use-estimator.ipynb). | `None`
`node_count`| Eğitim işine yönelik kullanmak için düğüm sayısı. | `1`
`process_count_per_node`| Her bir düğümde çalıştırılacak işlemleri (veya "çalışanları") sayısı. Bu durumda, kullandığınız `2` GPU'ları her düğümde kullanılabilir.| `1`
`distributed_training`| MPı arka ucunu kullanarak dağıtılmış eğitime başlatmaya yönelik [Mpiconation](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py) nesnesi.  | `None`


Son olarak, eğitim işini gönder:
```Python
run = experiment.submit(estimator)
print(run.get_portal_url())
```

## <a name="github-tracking-and-integration"></a>GitHub izleme ve Tümleştirme

Kaynak dizinin yerel bir git deposu olduğu bir eğitim çalıştırması başlattığınızda, depo hakkındaki bilgiler çalıştırma geçmişinde depolanır. Daha fazla bilgi için bkz. [Azure Machine Learning Için git tümleştirmesi](concept-train-model-git-integration.md).

## <a name="examples"></a>Örnekler
Bir tahmin aracı deseninin temellerini gösteren bir not defteri için bkz.:
* [Nasıl yapılır kullanımı-azureml/eğitim-ayrıntılı-öğrenme/nasıl yapılır-kullan-Estimator](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/how-to-use-estimator/how-to-use-estimator.ipynb)

Estimator kullanarak bir scikit-model öğrenerek bir not defteri için, bkz.:
* [öğreticiler/img-sınıflandırma-bölüm 1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/img-classification-part1-training.ipynb)

Derinlemesine öğrenme çerçevesine özgü estimators kullanarak eğitim modellerindeki Not defterleri için bkz.:

* [nasıl kullanılır-azureml/ml-çerçeveleri](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Sonraki adımlar

* [İzleme ölçümlerini eğitim sırasında çalıştırın](how-to-track-experiments.md)
* [PyTorch modellerini eğitin](how-to-train-pytorch.md)
* [TensorFlow modellerini eğitin](how-to-train-tensorflow.md)
* [Hiperparametreleri ayarlama](how-to-tune-hyperparameters.md)
* [Eğitilen model dağıtma](how-to-deploy-and-where.md)
