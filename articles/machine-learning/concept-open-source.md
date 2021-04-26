---
title: Açık kaynak makine öğrenimi tümleştirmesi
titleSuffix: Azure Machine Learning
description: Azure Machine Learning ' de uçtan uca makine öğrenimi çözümlerini eğerek, dağıtmaya ve yönetmeye yönelik açık kaynaklı Python makine öğrenimi çerçevelerini nasıl kullanacağınızı öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: luisquintanilla
ms.author: luquinta
ms.date: 01/14/2020
ms.openlocfilehash: 983e037376be48f497118b06cce8b23c430b1501
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "98223083"
---
# <a name="open-source-integration-with-azure-machine-learning-projects"></a>Azure Machine Learning projelerle açık kaynaklı tümleştirme

Açık kaynaklı Python makine öğrenimi kitaplıklarını ve platformlarını kullanarak Azure Machine Learning ' de uçtan uca makine öğrenimi sürecini eğitebilirsiniz, dağıtabilir ve yönetebilirsiniz.  Azure Machine Learning içindeki mevcut modellerinizi ve betikleri kullanmak için jupi Not defterleri ve Visual Studio Code gibi geliştirme araçlarını kullanın.  

Bu makalede, bu açık kaynaklı kitaplıklar ve platformlar hakkında daha fazla bilgi edinin.

## <a name="train-open-source-machine-learning-models"></a>Açık kaynaklı makine öğrenimi modellerini eğitme

Machine Learning eğitim süreci, bir görevi gerçekleştirmek veya bir sorunu çözmek için verilerinize yönelik algoritmaların uygulamasını içerir. Soruna bağlı olarak, göreve ve verilerinize en iyi şekilde uyum sağlayan farklı algoritmalar seçebilirsiniz. Machine Learning 'in farklı dalları hakkında daha fazla bilgi için bkz. [derin öğrenme vs makine öğrenimi makalesi](./concept-deep-learning-vs-machine-learning.md) ve [makine öğrenimi algoritması bilgi sayfası](algorithm-cheat-sheet.md).

### <a name="preserve-data-privacy-using-differential-privacy"></a>Değişiklik gizliliğini kullanarak veri gizliliğini koruma

Bir makine öğrenimi modelini eğitebilmeniz için, verilerin olması gerekir. Bazen bu veriler duyarlıdır ve verilerin güvenli ve özel olduğundan emin olmak önemlidir. Değişiklik gizliliği, bir veri kümesindeki bilgilerin gizliliğini koruma tekniğidir. Daha fazla bilgi edinmek için [veri gizliliğini koruma](concept-differential-privacy.md)başlıklı makaleye bakın. 

[Smartnoise](https://github.com/opendifferentialprivacy/smartnoise-core-python) gibi açık kaynaklı değişiklik gizliliği araç takımları Azure Machine Learning çözümlerinde [verilerin gizliliğini korumanıza](how-to-differential-privacy.md) yardımcı olur.

### <a name="classical-machine-learning-scikit-learn"></a>Klasik makine öğrenimi: scikit-öğrenme

Bu tür sınıflandırma, kümeleme ve gerileme gibi klasik makine öğrenimi algoritmalarını içeren eğitim görevlerinde, Scikit-öğren gibi bir işlem kullanabilirsiniz. Çiçek sınıflandırma modelini eğitme hakkında bilgi edinmek için bkz. [Scikit ile eğitme makalesi hakkında bilgi](how-to-train-scikit-learn.md)edinin.

### <a name="neural-networks-pytorch-tensorflow-keras"></a>Sinir Networks: PyTorch, TensorFlow, keras

Machine Learning 'in bir alt kümesi olan sinir Networks olarak bilinen açık kaynaklı makine öğrenimi algoritmaları, Azure Machine Learning ' deki derinlemesine öğrenme modellerini eğitmek için yararlıdır.

Açık kaynaklı derin öğrenme çerçeveleri ve nasıl yapılır kılavuzlarında şunlar vardır:

 *  [Pytorch](https://github.com/pytorch/pytorch): [Aktarım öğrenimi kullanarak derin bir öğrenme görüntü sınıflandırma modeli eğitme](how-to-train-pytorch.md) 
 *  [TensorFlow](https://github.com/tensorflow/tensorflow): [TensorFlow kullanarak el ile yazılmış rakamları tanıma](how-to-train-tensorflow.md)
 *  [Keras](https://github.com/keras-team/keras): [keras kullanarak görüntüleri çözümlemek için sinir ağı oluşturma](how-to-train-keras.md)

Derin bir öğrenme modelinin sıfırdan eğitimi, genellikle büyük miktarda zaman, veri ve işlem kaynağı gerektirir. Eğitim sürecini aktarma öğrenimini kullanarak kısayol oluşturabilirsiniz. Aktarım öğrenimi, bir problemi farklı ancak ilgili bir soruna çözmeyle elde edilen bilgileri uygulayan bir tekniktir. Bu, yeniden amaçlandırın var olan bir modeli götürebilmeniz anlamına gelir. Aktarım öğrenimi hakkında daha fazla bilgi edinmek için [derin öğrenme vs Machine Learning makalesine](concept-deep-learning-vs-machine-learning.md#what-is-transfer-learning) bakın.

### <a name="reinforcement-learning-ray-rllib"></a>Pekiştirmeye dayalı Learning: Ray RLLib

Pekiştirmeye dayalı Learning, eylemleri, durumları ve depoları kullanarak modelleyen yapay zeka tekniğidir: pekiştirmeye dayalı öğrenme aracıları, ortamının geçerli durumuna göre belirtilen yeniden temellerin en üst düzeye çıkaran bir dizi önceden tanımlanmış eylem yapmayı öğreniyor. 

[Ray RLLib](https://github.com/ray-project/ray) projesinde eğitim sürecinde yüksek ölçeklenebilirlik sağlayan bir dizi özellik vardır. Yinelemeli işlem hem zaman hem de kaynak yoğunluklu olarak pekiştirmeye dayalı öğrenme aracıları bir görevi elde etmenin en iyi yolunu öğrenmeye çalışır.  Ray, TensorFlow ve PyTorch gibi derin öğrenme çerçevelerini de yerel olarak destekler.  

Azure Machine Learning ile Ray yöntemi ile birlikte kullanma hakkında bilgi edinmek için bkz. [pekiştirmeye dayalı Learning modelini eğitme](how-to-use-reinforcement-learning.md).

### <a name="monitor-model-performance-tensorboard"></a>Model performansını izleme: TensorBoard

Tek veya birden çok modeli eğitmek için, modelin beklendiği gibi gerçekleştirildiğinden emin olmak için istenen ölçümlerin görselleştirilmesi ve incelemesi gerekir. [Deneme ölçümlerini izlemek ve görselleştirmek için Azure Machine Learning ' de TensorBoard kullanabilirsiniz](./how-to-monitor-tensorboard.md)

### <a name="frameworks-for-interpretable-and-fair-models"></a>Yorumlutablo ve dengeli modeller için çerçeveler

Makine öğrenimi sistemleri, bankacılık, eğitim ve sağlık gibi farklı topluluğu alanlarında kullanılır. Bu nedenle, bu sistemlerin, istenmeyen sonuçlara engel olmak için öngörülen tahminler ve öneriler için muhasebeci olması önemlidir.

[Interkıl](https://github.com/interpretml/interpret/) ve Fairlearn gibi açık kaynaklı çerçeveler ( https://github.com/fairlearn/fairlearn) daha saydam ve eşitlenmiş makine öğrenimi modelleri oluşturmak için Azure Machine Learning ile çalışın.

Dengeli ve yorumlandı modeller oluşturma hakkında daha fazla bilgi için aşağıdaki makalelere bakın:

- [Azure Machine Learning'de model yorumlanabilirliği](how-to-machine-learning-interpretability.md)
- [Makine öğrenimi modellerini yorumlama ve açıklama](how-to-machine-learning-interpretability-aml.md)
- [Oto ml modellerini açıklayın](how-to-machine-learning-interpretability-automl.md)
- [Makine öğrenimi modellerinde eşitliği 'i azaltma](concept-fairness-ml.md)
- [Varlık modeli eşitliği Azure Machine Learning kullanma](how-to-machine-learning-fairness-aml.md)

## <a name="model-deployment"></a>Model dağıtımı

Modeller eğitildikten ve üretime hazırlandıktan sonra, nasıl dağıtılacağını seçmeniz gerekir. Azure Machine Learning çeşitli dağıtım hedefleri sağlar. Daha fazla bilgi için bkz. [nasıl yapılır ve nasıl dağıtılır makalesi](./how-to-deploy-and-where.md).

### <a name="standardize-model-formats-with-onnx"></a>ONNX ile model biçimlerini standartlaştırın

Eğitimin ardından, öğrenilen parametreler gibi modelin içeriği serileştirilir ve bir dosyaya kaydedilir. Her çerçevenin kendi serileştirme biçimi vardır. Farklı çerçeveler ve araçlarla çalışırken, modelleri Framework gereksinimlerine göre dağıtmanız gerektiği anlamına gelir. Bu işlemi standartlaştırmak için Open sinir Network Exchange (ONNX) biçimini kullanabilirsiniz. ONNX, yapay zeka modelleri için açık kaynaklı bir biçimdir. ONNX çerçeveler arasında birlikte çalışabilirliği destekler. Bu, bir modeli PyTorch gibi birçok popüler makine öğrenimi çerçevelerinden birinde eğitebileceğiniz, ONNX biçimine dönüştürebileceğiniz ve ML.NET gibi farklı bir çerçevede ONNX modelini tükettiğiniz anlamına gelir.

ONNX hakkında daha fazla bilgi ve ONNX modellerini kullanma hakkında daha fazla bilgi için aşağıdaki makalelere bakın:

- [ONNX ile ML modellerini oluşturma ve hızlandırma](concept-onnx.md)
- [.NET uygulamalarında ONNX modellerini kullanma](how-to-use-automl-onnx-model-dotnet.md)

### <a name="package-and-deploy-models-as-containers"></a>Modelleri kapsayıcı olarak paketleme ve dağıtma

Docker gibi kapsayıcı teknolojileri, modelleri Web Hizmetleri olarak dağıtmanın bir yoludur. Kapsayıcılar, tekrarlanabilir yazılım ortamları oluşturmak ve düzenlemek için bir platform ve kaynak bağımsız yolu sağlar. Bu temel teknolojilerle, makine öğrenimi modellerinizi [Kubernetes kümelerine](./how-to-deploy-azure-kubernetes-service.md?tabs=python)dağıtmak için [önceden yapılandırılmış ortamları](./how-to-use-environments.md), [önceden yapılandırılmış kapsayıcı görüntülerini](./how-to-deploy-custom-docker-image.md) veya özel olanları kullanabilirsiniz. GPU yoğun iş akışlarında, GPU 'ları kullanarak tahmine dayalı [hale getirmek](how-to-deploy-with-triton.md?tabs=python)Için NVIDIA Triton çıkarım sunucusu gibi araçları kullanabilirsiniz.

### <a name="secure-deployments-with-homomorphic-encryption"></a>Homomorphic şifrelemesiyle güvenli dağıtımlar

Dağıtımları güvenli hale getirmek, dağıtım sürecinin önemli bir parçasıdır. [Şifrelenmiş ınary hizmetleri dağıtmak](how-to-homomorphic-encryption-seal.md)için, `encrypted-inference` Açık kaynaklı Python kitaplığını kullanın. `encrypted inferencing`Paket, bir homomorphic şifreleme kitaplığı olan [Microsoft mühürleme](https://github.com/Microsoft/SEAL)tabanlı bağlamalar sağlar.

## <a name="machine-learning-operations-mlops"></a>Machine Learning Işlemler (MLOps)

Makine öğrenimi için genellikle DevOps olarak düşünüldük Machine Learning Işlemler (MLOps), daha saydam, dayanıklı ve tekrarlanabilir makine öğrenimi iş akışları oluşturmanıza olanak tanır. MLOps hakkında daha fazla bilgi edinmek için [MLOps nedir makalesine](./concept-model-management-and-deployment.md) bakın. 

Sürekli tümleştirme (CI) ve sürekli dağıtım (CD) gibi DevOps uygulamalarını kullanarak, uçtan uca makine öğrenimi yaşam döngüsünü otomatik hale getirebilir ve BT genelinde idare verileri yakalayabilirsiniz. Azure Machine Learning eğitim ve dağıtım görevlerini çalıştırmak için, [GitHub eylemlerinizde Machine LEARNING CI/CD işlem](./how-to-github-actions-machine-learning.md) hattınızı tanımlayabilirsiniz. 

Yazılım bağımlılıklarını yakalama, ölçümler, meta veriler, veri ve model sürümü oluşturma, saydam, tekrarlanabilir ve denetlenebilir işlem hatları oluşturmak için MLOps işleminin önemli bir parçasıdır. Bu görev için, Azure Machine Learning ve [Azure Databricks ' de makine öğrenimi modellerini öğreticiyi](./how-to-use-mlflow-azure-databricks.md) [kullanarak mlflow 'u kullanabilirsiniz](how-to-use-mlflow.md) . Ayrıca, [MLflow modellerini bir Azure Web hizmeti olarak dağıtabilirsiniz](how-to-deploy-mlflow-models.md). 
