---
title: 'MLOps: ML model yönetimi'
titleSuffix: Azure Machine Learning
description: 'Azure Machine Learning (MLOps) ile model yönetimi hakkında bilgi edinin. Modellerini sürekli olarak geliştirmek için modellerinizi dağıtın, yönetin ve izleyin. '
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: jpe316
ms.author: jordane
ms.date: 11/04/2019
ms.custom: seodec18
ms.openlocfilehash: c77e36b5fbeb1ecacd42352c8c52cfd4b617d0e6
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74123611"
---
# <a name="mlops-model-management-deployment-and-monitoring-with-azure-machine-learning"></a>MLOps: model yönetimi, dağıtım ve Azure Machine Learning ile izleme

Bu makalede, modellerinizin yaşam döngüsünü yönetmek için Azure Machine Learning kullanma hakkında bilgi edinin. Azure Machine Learning, Machine Learning çözümlerinizin kalitesini ve tutarlılığını artıran bir Machine Learning Işlemleri (MLOps) yaklaşımı kullanır. 

Azure Machine Learning aşağıdaki MLOps yeteneklerini sağlar:

- **ML projelerini her yerden dağıtın**
- **Çalışma ve ml ile ilgili sorunlar IÇIN ml uygulamalarını izleyin** -eğitim ve çıkarım arasındaki model girişlerini karşılaştırın, modele özgü ölçümleri bulun ve ml altyapınızda izleme ve Uyarılar sağlayın.
- Modellerden kimin yayımladığına, neden değişikliklerin yapılmakta olduğuna ve modellerin üretimde ne zaman dağıtıldığını veya üretimde kullanıldığına dahil olmak üzere **ml yaşam döngüsünün uçtan uca denetim izi oluşturmak için gereken verileri yakalayın**.
- **Azure Machine Learning ve Azure DevOps ile uçtan uca ml yaşam döngüsünü otomatikleştirerek** , sıklıkla güncelleştirme, yeni modelleri test etme ve diğer uygulama ve hizmetlerinizle bırlıkte yeni ml modellerini sürekli olarak alma.

MLOps 'nin arkasındaki kavramlar ve bunların Azure Machine Learning nasıl uygulandığı hakkında daha fazla bilgi almak için aşağıdaki videoyu izleyin.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2X1GX]

## <a name="deploy-ml-projects-from-anywhere"></a>ML projelerini her yerden dağıtın

### <a name="turn-your-training-process-into-a-reproducible-pipeline"></a>Eğitim işleminizi tekrarlanabilir bir işlem hattına dönüştürün
Model eğitimi sürecinizdeki tüm adımları bir araya toplamak için Azure Machine Learning ' den ML işlem hatlarını kullanın, veri hazırlama ve özellik ayıklama 'den hiper parametre ayarlama ' ya kadar model değerlendirmesi yapın.

Daha fazla bilgi için bkz. [ml işlem hatları](concept-ml-pipelines.md).

### <a name="register-and-track-ml-models"></a>ML modellerini kaydetme ve izleme

Model kaydı, modellerinizi Azure bulutu 'nda çalışma alanınızda depolamanıza ve yapılandırmanıza olanak tanır. Model kayıt defteri, eğitilen modellerinizi düzenlemenizi ve izlemenizi kolaylaştırır.

> [!TIP]
> Kayıtlı bir model, modelinizi oluşturan bir veya daha fazla dosya için mantıksal bir kapsayıcıdır. Örneğin, birden çok dosyada depolanan bir modeliniz varsa, bunları Azure Machine Learning çalışma alanınızda tek bir model olarak kaydedebilirsiniz. Kayıttan sonra, kayıtlı modeli indirebilir veya dağıtabilir ve kayıtlı tüm dosyaları alabilirsiniz.
 
Kayıtlı modeller ad ve sürüm ile tanımlanır. Mevcut bir aynı ada sahip bir model her kaydettirdiğinizde, kayıt defteri sürüm artırır. Ek meta veri etiketleri aramak modellerinde kullanılabilir kayıt sırasında de sağlayabilirsiniz. Azure Machine Learning, Python 3.5.2 veya üzeri kullanılarak yüklenebilecek tüm modelleri destekler.

> [!TIP]
> Azure Machine Learning dışında eğitilen modelleri de kaydedebilirsiniz.

Etkin bir dağıtımda kullanılmakta olan kayıtlı bir modeli silemezsiniz.
Daha fazla bilgi için, [modelleri dağıtma](how-to-deploy-and-where.md#registermodel)' nın model kaydetme bölümüne bakın.

### <a name="package-and-debug-models"></a>Paket ve hata ayıklama modelleri

Bir modeli üretime dağıtmadan önce, bir Docker görüntüsüne paketlenir. Çoğu durumda, dağıtım sırasında görüntü oluşturma arka planda otomatik olarak gerçekleşir. Gelişmiş senaryolar için görüntüyü el ile belirtebilirsiniz.

Dağıtım ile ilgili sorunlar yaşıyorsanız, sorun giderme ve hata ayıklama için yerel geliştirme ortamınızda dağıtım yapabilirsiniz.

Daha fazla bilgi için bkz. [modelleri dağıtma](how-to-deploy-and-where.md#registermodel) ve [dağıtım sorunlarını giderme](how-to-troubleshoot-deployment.md).

### <a name="validate-and-profile-models"></a>Model ve profil modellerini doğrulama

Azure Machine Learning, modelinizi dağıttığınızda kullanılacak ideal CPU ve bellek ayarlarını tespit etmek için profil oluşturmayı kullanabilir. Model doğrulama, profil oluşturma işlemi için sağladığınız verileri kullanarak bu işlemin bir parçası olarak gerçekleşir.

### <a name="convert-and-optimize-models"></a>Modelleri dönüştürme ve iyileştirme

Modelinizi [Open sinir Network Exchange](https://onnx.ai) (onnx) olarak dönüştürmek, performansı iyileştirebilir. ONNX 'e dönüştürmek, ortalama bir 2x performans artışı sağlayabilir.

Azure Machine Learning ile ONNX hakkında daha fazla bilgi için bkz. [ml modellerini oluşturma ve hızlandırma](concept-onnx.md) makalesi.

### <a name="use-models"></a>Modelleri kullanma

Eğitilen makine öğrenimi modelleri, bulutta Web Hizmetleri olarak veya geliştirme ortamınızda yerel olarak dağıtılabilir. Modelleri, Azure IoT Edge cihazlara de dağıtabilirsiniz. Dağıtımlar, Inor sınırlaması için CPU, GPU veya alan programlanabilir geçit dizileri (FPGA) kullanabilir. Ayrıca, Power BI modelleri de kullanabilirsiniz.

Bir modeli Web hizmeti veya IoT Edge cihaz olarak kullanırken, aşağıdaki öğeleri sağlarsınız:

* Hizmete/cihaza gönderilen verileri almak için kullanılan model (ler).
* Bir giriş betiği. Bu betik istekleri kabul eder, verileri öğrenmek için modeli kullanır ve bir yanıt döndürür.
* Model (ler) ve giriş betiği için gereken bağımlılıkları açıklayan bir Conda ortam dosyası.
* Model ve giriş betiği için gereken metin, veri vb. gibi diğer varlıklar.

Bu varlıklar bir Docker görüntüsüne paketlenir ve bir Web hizmeti veya IoT Edge modülü olarak dağıtılır.

İsteğe bağlı olarak, dağıtımı daha fazla ayarlamak için aşağıdaki parametreleri kullanabilirsiniz:

* GPU 'YU etkinleştir: Docker görüntüsünde GPU desteğini etkinleştirmek için kullanılır. Görüntü, Azure Container Instances, Azure Kubernetes hizmeti, Azure Machine Learning Işlem veya Azure sanal makineleri gibi Microsoft Azure hizmetlerinde kullanılmalıdır.
* Ek Docker dosya adımları: Docker görüntüsünü oluştururken çalıştırmak için ek Docker adımları içeren bir dosya.
* Temel görüntü: temel görüntü olarak kullanılacak özel bir görüntü. Özel bir görüntü kullanmıyorsanız, temel görüntü Azure Machine Learning tarafından sağlanır.

Ayrıca, hedef dağıtım platformunun yapılandırmasını da sağlarsınız. Örneğin, sanal makine aile türü, kullanılabilir bellek ve Azure Kubernetes hizmetine dağıtım yaparken çekirdek sayısı.

Görüntü oluşturulduğunda, Azure Machine Learning için gereken bileşenler de eklenir. Örneğin, Web hizmetini çalıştırmak ve IoT Edge etkileşimde bulunmak için gereken varlıklar.

> [!NOTE]
> Docker görüntüsünde kullanılan Web sunucusunu veya IoT Edge bileşenlerini değiştiremez veya değiştiremezsiniz. Azure Machine Learning, Microsoft tarafından sınanmış ve desteklenen bir Web sunucusu yapılandırması ve IoT Edge bileşenleri kullanır.

#### <a name="web-service"></a>Web hizmeti

Aşağıdaki işlem hedefleri ile **Web hizmetlerindeki** modellerinizi kullanabilirsiniz:

* Azure Container Örneği
* Azure Kubernetes Service
* Yerel geliştirme ortamı

Modeli bir Web hizmeti olarak dağıtmak için aşağıdaki öğeleri sağlamanız gerekir:

* Modellerin modeli veya modelini işleme.
* Modeli kullanmak için gereken bağımlılıklar. Örneğin, istekleri kabul eden ve modeli çağıran bir betik, Conda bağımlılıkları, vb.
* Modelin nasıl ve nereye dağıtılacağını açıklayan dağıtım yapılandırması.

Daha fazla bilgi için bkz. [modelleri dağıtma](how-to-deploy-and-where.md).

#### <a name="iot-edge-devices"></a>IoT Edge cihazlar

IoT cihazlarıyla modelleri **Azure IoT Edge modüller**aracılığıyla kullanabilirsiniz. IoT Edge modüller, cihazda çıkarım veya model Puanlama sağlayan bir donanım cihazına dağıtılır.

Daha fazla bilgi için bkz. [modelleri dağıtma](how-to-deploy-and-where.md).

### <a name="analytics"></a>Analiz

Microsoft Power BI, veri analizi için makine öğrenimi modellerinin kullanılmasını destekler. Daha fazla bilgi için bkz. [Power BI Azure Machine Learning Tümleştirme (Önizleme)](https://docs.microsoft.com/power-bi/service-machine-learning-integration).


## <a name="monitor-for-operational--ml-issues"></a>İşlemsel & ML sorunlarını izleme

İzleme, modelinize hangi verilerin gönderildiğini ve döndürdüğü tahminleri anlamanıza olanak sağlar.

Bu bilgiler, modelinizin nasıl kullanıldığını anlamanıza yardımcı olur. Toplanan giriş verileri, modelin gelecek sürümlerini eğitmek için de yararlı olabilir.

Daha fazla bilgi için bkz. [model veri toplamayı etkinleştirme](how-to-enable-data-collection.md).


## <a name="audit-trail-of-the-ml-lifecycle"></a>ML yaşam döngüsünün denetim izi

Azure ML, tüm ML varlıklarınızın uçtan uca denetim izini izleme olanağı sunar. Daha ayrıntılı şekilde belirtmek gerekirse:

- Azure ML, kodunuzun geldiği depoyu/dalı/yürütmeyi belirten bilgileri izlemek için [Git ile tümleşir](how-to-set-up-training-targets.md#gitintegration) .
- [Azure ML veri kümeleri](how-to-create-register-datasets.md) , verileri izlemenize ve sürümetmenize yardımcı olur.
- Azure ML çalışma geçmişi, bir modeli eğitmek için kullanılan kodun, verilerin ve işlemin bir anlık görüntüsünü depolar.
- Azure ML modeli kayıt defteri, modelinizle ilişkili tüm meta verileri yakalar (Bu, dağıtımları, dağıtımı sağlıklı ise, dağıtıldığı yerde).

## <a name="automate-the-ml-lifecycle"></a>ML yaşam döngüsünü otomatikleştirin 

Bir modeli gösteren bir sürekli tümleştirme işlemi oluşturmak için GitHub ve Azure Pipelines kullanabilirsiniz. Tipik bir senaryoda, bir veri Bilimconu bir proje için git deposunda değişiklik denetlediğinde, Azure işlem hattı bir eğitim çalıştırması başlatır. Daha sonra, eğitilen modelin performans özelliklerini görmek için çalıştırmanın sonuçları incelenebilir. Ayrıca, modeli bir Web hizmeti olarak dağıtan bir işlem hattı da oluşturabilirsiniz.

[Azure Machine Learning uzantısı](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml) , Azure Pipelines çalışmayı kolaylaştırır. Azure Pipelines için aşağıdaki geliştirmeleri sağlar:

* Hizmet bağlantısı tanımlarken çalışma alanı seçimini izin vermez.
* Eğitim ardışık düzeninde oluşturulan eğitilen modeller tarafından tetiklenecek yayın işlem hatlarını sağlar.

Azure Machine Learning ile Azure Pipelines kullanma hakkında daha fazla bilgi için, bkz. Azure Pipelines article ve [Azure Machine Learning MLOps](https://aka.ms/mlops) deposu [Ile ml modellerinin sürekli tümleştirilmesi ve dağıtımı](/azure/devops/pipelines/targets/azure-machine-learning) .

## <a name="next-steps"></a>Sonraki adımlar

Aşağıdaki kaynakları okuyarak ve inceleyerek daha fazla bilgi edinin:

+ Azure Machine Learning ile [modellerin dağıtılacağı konum &](how-to-deploy-and-where.md)

+ [Öğretici: ACI 'de görüntü sınıflandırma modeli dağıtın](tutorial-deploy-models-with-aml.md).

+ [Uçtan uca MLOps örnekleri deposu](https://github.com/microsoft/MLOps)

+ [Azure Pipelines ile ML modellerinin CI/CD 'si](/azure/devops/pipelines/targets/azure-machine-learning)

+ [Dağıtılan bir modeli](how-to-consume-web-service.md) kullanan istemciler oluşturma

+ [Uygun ölçekte makine öğrenimi](/architecture/data-guide/big-data/machine-learning-at-scale)

+ [Azure AI başvuru mimarileri & en iyi yöntemler temsilcisi](https://github.com/microsoft/AI)
