---
title: Yapı & eğitme modelleri
titleSuffix: Azure Machine Learning
description: Modelleri Azure Machine Learning ile eğitme hakkında bilgi edinin. Farklı eğitim yöntemlerini keşfedebilir ve projeniz için doğru olanı seçin.
services: machine-learning
ms.service: machine-learning
author: Blackmist
ms.author: larryfr
ms.subservice: core
ms.topic: conceptual
ms.date: 05/13/2020
ms.custom: devx-track-python
ms.openlocfilehash: de3d9aa60322cc3e6e189f6f16c35d6f42c0cf61
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102500419"
---
# <a name="train-models-with-azure-machine-learning"></a>Modelleri Azure Machine Learning ile eğitme

Azure Machine Learning, SDK 'yı kullanarak otomatik makine öğrenimi ve görsel tasarımcı gibi düşük kod çözümlerine kadar, modellerinizi eğitmek için çeşitli yollar sağlar. Size hangi eğitim yönteminin uygun olduğunu öğrenmek için aşağıdaki listeyi kullanın:

+ [Python IÇIN sdk Azure Machine Learning](#python-sdk): Python SDK, her biri farklı yeteneklere sahip modelleri eğitmek için çeşitli yollar sağlar.

    | Eğitim yöntemi | Description |
    | ----- | ----- |
    | [Yapılandırmayı Çalıştır](#run-configuration) | **Modelleri eğitmek için tipik bir yol** , bir eğitim betiği kullanmaktır ve yapılandırma çalıştırmalıdır. Çalıştırma yapılandırması, modelinizi eğitemak için kullanılan eğitim ortamını yapılandırmak için gereken bilgileri sağlar. Çalışma yapılandırmanızda eğitim betiğinizi, işlem hedefini ve Azure ML ortamınızı belirtebilir ve bir eğitim işi çalıştırabilirsiniz. |
    | [Otomatik makine öğrenimi](#automated-machine-learning) | Otomatikleştirilmiş makine **öğrenimi, kapsamlı veri bilimi veya programlama bilgisi olmadan modelleri eğmenize** olanak tanır. Veri bilimi ve programlama arka planına sahip kişiler için, algoritma seçimini ve hiper parametre ayarlamayı otomatikleştirerek zaman ve kaynak tasarrufu yapmak için bir yol sağlar. Otomatik makine öğrenimi kullanırken çalıştırma yapılandırması tanımlama konusunda endişelenmeniz gerekmez. |
    | [Machine Learning işlem hattı](#machine-learning-pipeline) | İşlem hatları, farklı bir eğitim yöntemi değildir, ancak iş akışının bir parçası olarak eğitim içerebilen **modüler, yeniden kullanılabilir adımları kullanarak bir iş akışı tanımlamanın bir yoludur**. Makine öğrenimi ardışık düzenleri, otomatik makine öğrenimini kullanmayı destekler ve modelleri eğitmek için yapılandırmayı çalıştırır. İşlem hatları eğitimlere özel olarak odaklanmadığından, işlem hattı kullanmanın nedenleri diğer eğitim yöntemlerinden daha farklılaştırılmıştır. Genellikle, şu durumlarda bir işlem hattı kullanabilirsiniz:<br>* Uzun süre çalışan eğitim işleri veya veri hazırlama gibi **Katılımsız işlemleri zamanlamak** istiyorsunuz.<br>* Heterojen işlem kaynakları ve depolama konumları genelinde koordine edilen **birden çok adım** kullanın.<br>* Yeniden eğitim veya toplu Puanlama gibi belirli senaryolar için işlem hattını yeniden **kullanılabilir bir şablon** olarak kullanın.<br>* İş akışınız için **veri kaynaklarını, girişleri ve çıkışları** izleyin.<br>* İş akışınız, **belirli adımlarda bağımsız olarak çalışan farklı takımlar tarafından uygulanır**. Adımlar daha sonra iş akışını uygulamak için bir ardışık düzende birleştirilebilir. |

+ [R için Azure MACHINE LEARNING SDK (Önizleme)](#r-sdk-preview): r için sdk, Azure Machine Learning Python SDK 'sına bağlamak için reticute paketini kullanır. Bu, herhangi bir R ortamından Python SDK 'sında uygulanan temel nesnelere ve yöntemlere erişmenizi sağlar.

+ **Tasarımcı**: Azure Machine Learning tasarımcı, kavram kanıtı oluşturmak için veya çok az kodlama deneyimi olan kullanıcılar için makine öğrenimine kolay bir giriş noktası sağlar. Bir sürükle ve bırak Web tabanlı kullanıcı arabirimi kullanarak modelleri eğmenize olanak tanır. Python kodunu tasarımın bir parçası olarak kullanabilir veya herhangi bir kod yazmadan modelleri eğitebilirsiniz.

+ **CLI**: MACHINE Learning clı, Azure Machine Learning ortak görevlere yönelik komutlar sağlar ve genellikle **komut dosyası oluşturma ve otomatikleştirme işlemleri** için kullanılır. Örneğin, bir eğitim betiği veya işlem hattı oluşturduktan sonra, bir zamanlamaya göre çalışan bir eğitimi başlatmak veya eğitim için kullanılan veri dosyaları güncelleniyorsa, CLı 'yi kullanabilirsiniz. Eğitim modelleri için eğitim işleri gönderen komutları sağlar. Çalışma yapılandırması veya işlem hatları kullanarak işleri gönderebilir.

Bu eğitim yöntemlerinin her biri, eğitim için farklı türlerde işlem kaynakları kullanabilir. Toplu olarak, bu kaynaklara [__işlem hedefleri__](concept-azure-machine-learning-architecture.md#compute-targets)denir. İşlem hedefi bir yerel makine veya Azure Machine Learning Işlem, Azure HDInsight veya uzak bir sanal makine gibi bir bulut kaynağı olabilir.

## <a name="python-sdk"></a>Python SDK'sı

Python için Azure Machine Learning SDK, Azure Machine Learning ile makine öğrenimi iş akışları oluşturmanıza ve çalıştırmanıza olanak tanır. Etkileşimli bir Python oturumundan, Jupyıter not defterlerinden, Visual Studio Code veya başka bir IDE 'den hizmetle etkileşime geçebilirsiniz.

* [Python için Azure Machine Learning SDK nedir?](/python/api/overview/azure/ml/intro)
* [SDK’yı yükleme/güncelleştirme](/python/api/overview/azure/ml/install)
* [Azure Machine Learning için bir geliştirme ortamı yapılandırma](how-to-configure-environment.md)

### <a name="run-configuration"></a>Yapılandırmayı Çalıştır

Azure Machine Learning ile genel bir eğitim işi [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig)kullanılarak tanımlanabilir. Komut dosyası çalıştırma yapılandırması daha sonra, bir işlem hedefinde bir modeli eğitmek için eğitim betikleriyle birlikte kullanılır.

Yerel bilgisayarınız için bir çalıştırma yapılandırması ile başlayabilir ve ardından gerektiğinde bulut tabanlı bir işlem hedefi için birine geçiş yapabilirsiniz. İşlem hedefini değiştirirken yalnızca kullandığınız çalıştırma yapılandırmasını değiştirirsiniz. Bir çalıştırma Ayrıca, eğitim işiyle ilgili olarak girişler, çıktılar ve Günlükler gibi bilgileri de günlüğe kaydeder.

* [Çalışma yapılandırması nedir?](concept-azure-machine-learning-architecture.md#run-configurations)
* [Öğretici: ilk ML modelinizi eğitme](tutorial-1st-experiment-sdk-train.md)
* [Örnekler: eğitim modelleriyle Jupyter Notebook ve Python örnekleri](https://github.com/Azure/azureml-examples)
* [Nasıl yapılır: eğitim çalıştırmasını yapılandırma](how-to-set-up-training-targets.md)

### <a name="automated-machine-learning"></a>Otomatik Makine Öğrenmesi

Yinelemeleri, hiper parametre ayarlarını, fealeştirme ve diğer ayarları tanımlayın. Eğitim sırasında Azure Machine Learning, paralel olarak farklı algoritmalar ve parametreler gerçekleştirmeye çalışır. Eğitim, tanımladığınız çıkış ölçütlerine ulaştıktan sonra duraklar.

> [!TIP]
> Python SDK 'ya ek olarak, [Azure Machine Learning Studio](https://ml.azure.com)aracılığıyla otomatikleştirilmiş ml 'yi de kullanabilirsiniz.

* [Otomatik makine öğrenmesi nedir?](concept-automated-ml.md)
* [Öğretici: Otomatik makine öğrenmesiyle ilk sınıflandırma modelinizi oluşturma](tutorial-first-experiment-automated-ml.md)
* [Öğretici: Taksi ücretlerini tahmin etmek için otomatik makine öğrenmesini kullanma](tutorial-auto-train-models.md)
* [Örnekler: otomatik makine öğrenimine yönelik Jupyter Notebook örnekleri](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning)
* [Nasıl yapılır: Python 'da otomatik ML denemeleri yapılandırma](how-to-configure-auto-train.md)
* [Nasıl yapılır: zaman serisi tahmin modelini oto eğitme](how-to-auto-train-forecast.md)
* [Nasıl yapılır: Azure Machine Learning Studio ile otomatik makine öğrenimi denemeleri oluşturma, araştırma ve dağıtma](how-to-use-automated-ml-for-ml-models.md)

### <a name="machine-learning-pipeline"></a>Machine Learning işlem hattı

Machine Learning işlem hatları, daha önce bahsedilen eğitim yöntemlerini kullanabilir. İşlem hatları, bir iş akışı oluşturma hakkında daha fazla bilgi için yalnızca modellerin eğitimine kıyasla daha fazla bilgi alırlar. Bir işlem hattında, otomatik makine öğrenimi kullanarak bir modeli eğitebilirsiniz veya yapılandırma çalıştırabilirsiniz.

* [Azure Machine Learning ML işlem hatları nelerdir?](concept-ml-pipelines.md)
* [Azure Machine Learning SDK ile makine öğrenimi işlem hatları oluşturma ve çalıştırma](./how-to-create-machine-learning-pipelines.md)
* [Öğretici: Batch Puanlama için Azure Machine Learning işlem hatları kullanma](tutorial-pipeline-batch-scoring-classification.md)
* [Örnekler: makine öğrenimi ardışık düzenleri Jupyter Notebook örnekleri](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines)
* [Örnekler: otomatik makine öğrenimiyle işlem hattı](https://aka.ms/pl-automl)

### <a name="understand-what-happens-when-you-submit-a-training-job"></a>Eğitim işi gönderdiğinizde ne olacağını anlayın

Azure eğitim yaşam döngüsü aşağıdakilerden oluşur:

1. _. Amlignore_ veya _. gitignore_ içinde belirtilen dosyaları yoksayarak proje klasörünüzdeki dosyaları zipden gönderin
1. İşlem kümenizi ölçeklendirme 
1. Dockerfile 'ı işlem düğümüne derleme veya indirme 
    1. Sistem bir karma değerini hesaplar: 
        - Temel görüntü 
        - Özel Docker adımları (bkz. [özel bir Docker temel görüntüsü kullanarak model dağıtma](./how-to-deploy-custom-docker-image.md))
        - Conda tanımı YAML (bkz. [oluşturma & yazılım ortamlarını Azure Machine Learning kullanma](./how-to-use-environments.md))
    1. Sistem bu karmayı, çalışma alanının aramasında anahtar olarak kullanır Azure Container Registry (ACR)
    1. Bulunmazsa, genel ACR 'de bir eşleşme arar
    1. Bulunamadıysanız, sistem yeni bir görüntü oluşturur (önbelleğe alınır ve çalışma alanı ACR 'ye kaydedilir)
1. Daraltılmış proje dosyanızı işlem düğümündeki geçici depolamaya indirme
1. Proje dosyasının sıkıştırması kaldırılıyor
1. Yürütülen işlem düğümü `python <entry script> <arguments>`
1. Günlükler, model dosyaları ve `./outputs` çalışma alanıyla ilişkili depolama hesabına yazılan diğer dosyalar kaydediliyor
1. Geçici depolamayı kaldırma dahil olmak üzere ölçeği azaltma 

Yerel makinenizde ("Yerel çalıştırma olarak yapılandırma") eğitme seçeneğini belirlerseniz Docker kullanmanız gerekmez. İsterseniz Docker 'ı yerel olarak kullanabilirsiniz (bir örnek için ML işlem hattını [yapılandırma](./how-to-debug-pipelines.md) bölümüne bakın).

## <a name="r-sdk-preview"></a>R SDK (Önizleme)

R SDK, Azure Machine Learning R dilini kullanmanıza olanak sağlar. SDK, Azure Machine Learning Python SDK 'sına bağlamak için reticute paketini kullanır. Bu, herhangi bir R ortamından Python SDK 'sında uygulanan temel nesnelere ve yöntemlere erişmenizi sağlar.

Daha fazla bilgi için aşağıdaki makaleleri inceleyin:

* [R başvurusu için SDK Azure Machine Learning](https://azure.github.io/azureml-sdk-for-r/index.html)

## <a name="azure-machine-learning-designer"></a>Azure Machine Learning tasarımcısı

Tasarımcı, Web tarayıcınızda bir sürükle ve bırak arabirimini kullanarak modelleri eğmenize imkan tanır.

+ [Tasarımcı nedir?](concept-designer.md)
+ [Öğretici: otomobil fiyatlarını tahmin etme](tutorial-designer-automobile-price-train-score.md)

## <a name="many-models-solution-accelerator"></a>Birçok modelte Çözüm Hızlandırıcısı

[Birçok model Çözüm Hızlandırıcısı](https://aka.ms/many-models) (önizleme) Azure Machine Learning oluşturur ve yüzlerce veya hatta binlerce makine öğrenimi modelini eğitmenize, çalıştırmanıza ve yönetmenize olanak sağlar.

Örneğin, aşağıdaki senaryolarda __her örnek için__ bir model oluşturmak, geliştirilmiş sonuçlara yol açabilir:

* Her bir mağaza için satışları tahmin etme
* Yüzlerce yağ için tahmine dayalı bakım
* Bireysel kullanıcılar için bir deneyim uyarlama.

Daha fazla bilgi için GitHub 'daki [birçok modeller çözüm hızlandırıcısına](https://aka.ms/many-models) bakın.

## <a name="cli"></a>CLI

Machine Learning CLı, Azure CLı için bir uzantıdır. Azure Machine Learning ile çalışmak için platformlar arası CLı komutları sağlar. Genellikle, bir makine öğrenimi modeline eğitim gibi görevleri otomatikleştirmek için CLı 'yi kullanırsınız.

* [Azure Machine Learning için CLı uzantısını kullanın](reference-azure-machine-learning-cli.md)
* [Azure üzerinde MLOps](https://github.com/microsoft/MLOps)

## <a name="vs-code"></a>VS Code

Eğitim işlerinizi çalıştırmak ve yönetmek için VS Code uzantısını kullanabilirsiniz. Daha fazla bilgi edinmek için [vs Code kaynak yönetimi nasıl yapılır Kılavuzu](how-to-manage-resources-vscode.md#experiments) ' na bakın.

## <a name="next-steps"></a>Sonraki adımlar

[Eğitim çalıştırmasının nasıl yapılandırılacağını](how-to-set-up-training-targets.md)öğrenin.