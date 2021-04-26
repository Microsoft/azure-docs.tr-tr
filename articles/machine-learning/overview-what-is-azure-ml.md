---
title: Azure Machine Learning nedir?
description: Azure Machine Learning, veri bilimcilerinin ve MLops 'lerin bulut ölçeğinde ML uygulamalarını modelleyerek ve dağıtmaları için tümleşik bir veri bilimi çözümüdür.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
ms.author: larryfr
author: BlackMist
ms.date: 04/08/2021
ms.custom: devx-track-python
adobe-target: true
ms.openlocfilehash: cd395f0ff29eed1e09f5bdac882482b201052c90
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107814846"
---
# <a name="what-is-azure-machine-learning"></a>Azure Machine Learning nedir?

Bu makalede, ML modellerini eğmek, dağıtmak, otomatikleştirmek, yönetmek ve izlemek için kullanabileceğiniz bulut tabanlı bir ortam olan Azure Machine Learning hakkında bilgi edineceksiniz. 

Azure Machine Learning, klasik ml 'den derin öğrenme, denetimli ve denetimli öğrenime kadar her türlü makine öğrenimi için kullanılabilir. Bu SDK ile Python veya R kodu yazmayı veya [Studio](#build-ml-models-in-the-studio)'da kod içermeyen/düşük kod seçenekleriyle çalışmayı tercih etmeksizin, bir Azure Machine Learning çalışma alanı makine öğrenimi ve derin öğrenme modelleri oluşturabilir, eğitebilir ve izleyebilirsiniz. 

Yerel makinenizde eğitime başlayın ve ardından buluta ölçeklendirin. 

Hizmet Ayrıca, bilinen derin öğrenme ve pekiştirmeye dayalı açık kaynaklı araçlar ile PyTorch, TensorFlow, scikit-öğren ve Ray. 

> [!Tip]
> **Ücretsiz deneme!**  Azure aboneliğiniz yoksa başlamadan önce ücretsiz bir hesap oluşturun. [Azure Machine Learning ücretsiz veya ücretli sürümünü](https://aka.ms/AMLFree) bugün deneyin. Azure hizmetlerinde harcayabileceğiniz krediler alırsınız. Krediler bittikten sonra hesabı tutabilir ve [ücretsiz Azure hizmetlerini](https://azure.microsoft.com/free/) kullanabilirsiniz. Açıkça ayarlarınızı değiştirip ücretlendirme istemediğiniz sürece kredi kartınız asla ücretlendirilmez.


## <a name="what-is-machine-learning"></a>Makine öğrenmesi nedir?

Makine öğrenimi; bilgisayarların var olan verileri kullanarak gelecekteki davranışları, sonuçları ve eğilimleri öngörmelerini sağlayan bir veri bilimi tekniğidir. Makine öğrenimini kullanarak bilgisayarlar açıkça programlamadan öğreniyor.

Makine öğreniminin öngörüleri veya tahminleri, uygulama ve cihazları daha akıllı hale getirir. Örneğin, çevrimiçi alışveriş yaparken makine öğrenimi, satın aldığınız ürüne göre istediğiniz diğer ürünleri önermenize yardımcı olur. Ya da kredi kartınızdan çekim yapıldığında makine öğrenmesi işlemi bir işlem veritabanıyla karşılaştırır ve dolandırıcılığın tespit edilmesine yardımcı olur. Elektrikli süpürge robotunuz bir odayı temizlediğinde ise, makine öğrenimi robotunuzun işin tamamlanıp tamamlanmadığına karar vermesine yardımcı olur.

## <a name="machine-learning-tools-to-fit-each-task"></a>Her göreve uyacak makine öğrenimi araçları 

Azure Machine Learning, aşağıdakiler dahil olmak üzere makine öğrenimi iş akışları için gereken tüm araç geliştiricileri ve veri bilimcilerini sağlar:
+ [Azure Machine Learning Tasarımcısı](tutorial-designer-automobile-price-train-score.md): Sürükle-n-Bırak modülleri denemeleri oluşturup bir düşük kod ortamında işlem hatlarını dağıtın.

+ Jupyter Not defterleri: makinenizde öğrendiğimiz <a href="/python/api/overview/azure/ml/intro" target="_blank">Python örnekleri Için SDK</a> 'ümüzden yararlanmak üzere [örnek Not defterlerimizi](https://github.com/Azure/MachineLearningNotebooks) kullanın veya kendi not defterlerinizi oluşturun. 

+ Kendi kodunuzu yazmak için <a href="https://azure.github.io/azureml-sdk-for-r/reference/index.html" target="_blank">r Için SDK 'yı</a> kullandığınız r betikleri veya Not defterleri veya tasarımcıda r modüllerini kullanın.

+ [Birçok model Çözüm Hızlandırıcısı](https://aka.ms/many-models) (önizleme) Azure Machine Learning oluşturur ve yüzlerce veya hatta binlerce makine öğrenimi modelini eğitmenize, çalıştırmanıza ve yönetmenize olanak sağlar.

+ [Visual Studio Code (Önizleme) Için Machine Learning uzantısı](how-to-set-up-vs-code-remote.md) , Machine Learning projelerinizi oluşturmak ve yönetmek için size tam özellikli bir geliştirme ortamı sağlar.

+ [Machine LEARNING CLI](reference-azure-machine-learning-cli.md) , komut satırından Azure Machine Learning kaynaklarıyla yönetmek için komutlar sağlayan BIR Azure CLI uzantısıdır.

+ PyTorch, TensorFlow ve scikit gibi [Açık kaynaklı çerçevelerle tümleştirme](concept-open-source.md) , uçtan uca makine öğrenimi sürecini eğitmek, dağıtmak ve yönetmek için çok daha fazla bilgi edinin.

+ [Pekiştirmeye dayalı](how-to-use-reinforcement-learning.md) with Ray Rlrllib

Hatta, [ölçümleri izlemek Için Mlflow, modelleri](how-to-use-mlflow.md) veya Kubeflow kullanarak [uçtan uca iş akışı işlem hatları](https://www.kubeflow.org/docs/azure/)oluşturabilirsiniz.

## <a name="build-ml-models-in-python-or-r"></a>Python veya R 'de ML modelleri derleme

<a href="/python/api/overview/azure/ml/intro" target="_blank">Python SDK</a> veya <a href="https://azure.github.io/azureml-sdk-for-r/reference/index.html" target="_blank">R SDK</a>Azure Machine Learning kullanarak yerel makinenizde eğitim başlatın. Daha sonra, buluta ölçeklendirebilirsiniz. 

Azure Machine Learning Işlem ve [Azure Databricks](/azure/databricks/scenarios/what-is-azure-databricks)gibi birçok kullanılabilir [işlem hedefi](how-to-create-attach-compute-studio.md)ile ve [Gelişmiş hyperparameter ayarlama hizmetleriyle](how-to-tune-hyperparameters.md), bulutun gücünü kullanarak daha iyi modeller oluşturabilirsiniz.

SDK kullanarak [model eğitimi ve ayarlamayı otomatik hale](tutorial-auto-train-models.md) getirebilirsiniz.

## <a name="build-ml-models-in-the-studio"></a>Studio 'da ML modelleri derleme

[Azure Machine Learning Studio](https://studio.azureml.net) , model eğitimi, dağıtımı ve varlık yönetimi için düşük kod ve kod içermeyen seçenekler için Azure Machine Learning bir Web portalıdır. Studio, sorunsuz bir deneyim için Azure Machine Learning SDK ile tümleşir. Daha fazla bilgi için bkz. [Azure Machine Learning Studio nedir?](overview-what-is-machine-learning-studio.md).

+ **Azure Machine Learning tasarımcısı**

  [Tasarımcı](concept-designer.md) kullanarak makine öğrenimi modellerini herhangi bir kod yazmadan eğitme ve dağıtma. Başlamak için [Tasarımcı öğreticisini](tutorial-designer-automobile-price-train-score.md) deneyin. 

  ![Azure Machine Learning Tasarımcısı 'nın sürükle ve bırak arabiriminin animasyonlu gif 'i](media/concept-designer/designer-drag-and-drop.gif)

+ **Denemeleri izleme**

  Studio 'da [veri bilimi denemeleri nasıl izleneceğini ve görselleştirileyeceğinizi](how-to-track-monitor-analyze-runs.md) öğrenin. 

    :::image type="content" source="media/how-to-track-monitor-analyze-runs/run-history.png" alt-text="Azure Machine Learning Studio 'da çalıştırma ayrıntıları":::


+ **Ve çok daha fazlası...**

  [Ml.Azure.com](https://studio.azureml.net)adresinde Azure Machine Learning Studio adresini ziyaret edin.


## <a name="mlops-deploy--lifecycle-management"></a>MLOps: & yaşam döngüsü yönetimini dağıtma
Doğru modele sahip olduğunuzda, bir Web hizmetinde, IoT cihazında veya Power BI aracılığıyla kolayca kullanabilirsiniz. Daha fazla bilgi için, [nasıl dağıtılacağı ve nerede yapılacağı](how-to-deploy-and-where.md)hakkındaki makaleye bakın.

Daha sonra Python, [Azure Machine Learning Studio](https://ml.azure.com)veya [Machine Learning CLI](reference-azure-machine-learning-cli.md) [için Azure Machine Learning SDK 'sını](/python/api/overview/azure/ml/)kullanarak dağıtılan modellerinizi yönetebilirsiniz.

Bu modeller tüketilebilir ve [gerçek zamanlı](how-to-consume-web-service.md) veya [zaman uyumsuz](./tutorial-pipeline-batch-scoring-classification.md) olarak büyük miktarlarda veri alabilir.

Gelişmiş [makine öğrenimi ardışık düzenleri](concept-ml-pipelines.md)sayesinde, dağıtım aracılığıyla veri hazırlama, model eğitimi ve değerlendirmede her bir adımla işbirliği yapabilirsiniz. İşlem hatları şunları yapmanıza olanak sağlar:

* Bulutta uçtan uca makine öğrenimi işlemini otomatikleştirin
* Bileşenleri yeniden kullan ve yalnızca gerektiğinde adımları yeniden çalıştır
* Her adımda farklı işlem kaynakları kullanın
* Toplu Puanlama görevlerini Çalıştır

Machine Learning iş akışınızı otomatik hale getirmek için betikleri kullanmak istiyorsanız, [Machine LEARNING CLI](reference-azure-machine-learning-cli.md) , bir eğitim gönderme veya model dağıtma gibi genel görevleri gerçekleştiren komut satırı araçları sağlar.

Azure Machine Learning kullanmaya başlamak için bkz. [sonraki adımlar](#next-steps).

## <a name="integration-with-other-services"></a>Diğer hizmetlerle tümleştirme

Azure Machine Learning Azure platformunda diğer hizmetlerle çalışır ve ayrıca git ve MLFlow gibi açık kaynak araçlarla tümleştirilir.

+ __Azure Kubernetes hizmeti__, __Azure Container Instances__, __Azure Databricks__, __Azure Data Lake Analytics__ ve __Azure HDInsight__ gibi işlem hedefleri. İşlem hedefleri hakkında daha fazla bilgi için bkz. [işlem hedefleri nelerdir?](concept-compute-target.md).
+ __Azure Event Grid__. Daha fazla bilgi için bkz. [Azure Machine Learning olayları](./how-to-use-event-grid.md)kullanma.
+ __Azure izleyici__. Daha fazla bilgi için bkz. [izleme Azure Machine Learning](monitor-azure-machine-learning.md).
+ __Azure depolama hesapları__, __Azure Data Lake Storage__, __Azure SQL veritabanı__, __PostgreSQL Için Azure veritabanı__ ve __Azure açık veri kümeleri__ gibi veri depoları. Daha fazla bilgi için bkz. Azure [Storage hizmetlerindeki verilere erişme](how-to-access-data.md) ve [Azure açık veri kümeleri Ile veri kümeleri oluşturma](how-to-create-register-datasets.md).
+ __Azure sanal ağları__. Daha fazla bilgi için bkz. [sanal ağ yalıtımı ve gizliliğe genel bakış](how-to-network-security-overview.md).
+ __Azure Pipelines__. Daha fazla bilgi için bkz. [makine öğrenimi modellerini eğitme ve dağıtma](/azure/devops/pipelines/targets/azure-machine-learning).
+ __Git deposu günlükleri__. Daha fazla bilgi için bkz. [Git tümleştirmesi](concept-train-model-git-integration.md).
+ __Mlflow__. Daha fazla bilgi için bkz. ölçümleri izlemek ve [mlflow modellerini bir Web hizmeti olarak dağıtmak](how-to-deploy-mlflow-models.md) [için mlflow](how-to-use-mlflow.md) 
+ __Kubeflow__. Daha fazla bilgi için bkz. [uçtan uca iş akışı işlem hatları oluşturma](https://www.kubeflow.org/docs/azure/).

### <a name="secure-communications"></a>Güvenli iletişim

Azure depolama hesabınız, işlem hedefleri ve diğer kaynaklarınız, modelleri eğitmek ve çıkarım gerçekleştirmek için bir sanal ağ içinde güvenli bir şekilde kullanılabilir. Daha fazla bilgi için bkz. [sanal ağ yalıtımı ve gizliliğe genel bakış](how-to-network-security-overview.md).

## <a name="next-steps"></a>Sonraki adımlar

- Tercih ettiğiniz yöntemle ilk denemenizi oluşturun:
- + [Kendi geliştirme ortamınızı kullanmaya başlayın](tutorial-1st-experiment-sdk-setup-local.md)
  + [ML modellerini eğitmek & dağıtmak için bir işlem örneğinde Jupyıter not defterlerini kullanma](tutorial-1st-experiment-sdk-setup.md)
  + [ML modellerini eğitmek & dağıtmak için otomatik makine öğrenimi kullanma](tutorial-first-experiment-automated-ml.md) 
  + [Visual Studio Code kaynaklarını yönetme](how-to-manage-resources-vscode.md)
  + [Görüntü sınıflandırma modelini eğitmek ve dağıtmak için Visual Studio Code kullanma](tutorial-train-deploy-image-classification-model-vscode.md)
  + [& dağıtmayı eğitme tasarımcı & bırakma yeteneklerini kullanın](tutorial-designer-automobile-price-train-score.md) 
  + [Makine öğrenimi CLı kullanarak bir modeli eğitme ve dağıtma](tutorial-train-deploy-model-cli.md)

- Makine öğrenmesi senaryolarınızı derlemek, iyileştirmek ve yönetmek için [makine öğrenmesi işlem hatları](concept-ml-pipelines.md) hakkında bilgi edinin.

- Derinlemesine [Azure Machine Learning mimarisi ve kavramlar](concept-azure-machine-learning-architecture.md) makalesini okuyun.
