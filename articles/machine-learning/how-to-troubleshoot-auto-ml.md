---
title: Otomatik ML denemeleri sorunlarını giderme
titleSuffix: Azure Machine Learning
description: Otomatikleştirilmiş makine öğrenimi denemeleri sorunlarını giderme ve sorunları çözme hakkında bilgi edinin.
author: nibaccam
ms.author: nibaccam
ms.reviewer: nibaccam
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.date: 03/08/2021
ms.topic: conceptual
ms.custom: how-to, devx-track-python, automl, references_regions
ms.openlocfilehash: 2e6e4e468adec76d50242b31922e4db1c7e910ab
ms.sourcegitcommit: c3739cb161a6f39a9c3d1666ba5ee946e62a7ac3
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107210491"
---
# <a name="troubleshoot-automated-ml-experiments-in-python"></a>Python 'da otomatik ML denemeleri sorunlarını giderme

Bu kılavuzda, [Azure MACHINE LEARNING SDK](/python/api/overview/azure/ml/intro)ile otomatikleştirilmiş makine öğrenimi denemeleri içinde bilinen sorunları belirlemeyi ve çözümlemeyi öğrenin.

## <a name="version-dependencies"></a>Sürüm bağımlılıkları

**`AutoML` daha yeni paket sürümlerine yönelik bağımlılıklar uyumluluk kesmesi**. SDK sürüm 1.13.0 sonrasında modeller, önceki paketlerde sabitlenmiş eski sürümler arasında uyumsuzluk `AutoML` ve bugün sabitlenmiş yeni sürümler nedeniyle eski SDK 'larda yüklenmez.

Şöyle bir hata beklenir:

* Modül gibi hatalar bulunmadı,

  `No module named 'sklearn.decomposition._truncated_svd`

* Gibi içeri aktarma hataları

  `ImportError: cannot import name 'RollingOriginValidator'`,
* Gibi öznitelik hataları

  `AttributeError: 'SimpleImputer' object has no attribute 'add_indicator`

Çözümler `AutoML` SDK Eğitim sürümüne bağlıdır:

* `AutoML`SDK eğitim sürümünüz 1.13.0 büyükse, `pandas == 0.25.1` ve gereklidir `scikit-learn==0.22.1` .

    * Sürüm uyuşmazlığı varsa, scikit 'i yükseltin-aşağıdaki şekilde sürüme doğru sürümü öğrenmek için bilgi edinin ve/veya Pandas

      ```bash
          pip install --upgrade pandas==0.25.1
          pip install --upgrade scikit-learn==0.22.1
      ```

* `AutoML`SDK eğitim sürümünüz 1.12.0 değerinden küçük veya bu değere eşitse, `pandas == 0.23.4` ve gereklidir `sckit-learn==0.20.3` .
  * Sürüm uyuşmazlığı varsa, scikit-sürümü ve/veya Pandas sürümlerini aşağıdaki şekilde düzeltin.
  
    ```bash
      pip install --upgrade pandas==0.23.4
      pip install --upgrade scikit-learn==0.20.3
    ```

## <a name="setup"></a>Kurulum

`AutoML` sürüm 1.0.76 sürümü, yeni sürüme güncelleştirmeden önce önceki sürümün kaldırılmasını gerektirdiğinden paket değişir.

* **`ImportError: cannot import name AutoMLConfig`**

    V 1.0.76 'den v 1.0.76 veya üzeri bir SDK sürümünden yükseltme sonrasında bu hatayla karşılaşırsanız, şunu çalıştırarak hatayı çözün: `pip uninstall azureml-train automl` ve sonra `pip install azureml-train-automl` . Automl_setup. cmd betiği bunu otomatik olarak yapar.

* **automl_setup başarısız oluyor**

  * Windows üzerinde automl_setup bir Anaconda Isteminden çalıştırın. [Miniconda 'Yı yükler](https://docs.conda.io/en/latest/miniconda.html).

  * Conda 64-bit sürüm 4.4.10 veya üstünün yüklü olduğundan emin olun. Komutuyla biti kontrol edebilirsiniz `conda info` . `platform` `win-64` Windows veya Mac için olmalıdır `osx-64` . Sürümü denetlemek için komutunu kullanın `conda -V` . Önceki bir sürümü yüklüyse, şu komutu kullanarak güncelleştirebilirsiniz: `conda update conda` . 32 bit 'i çalıştırarak denetlemek için 

  * Conda 'nın yüklü olduğundan emin olun. 

  * 'Un `gcc: error trying to exec 'cc1plus'`

    1. Hatayla karşılaşırsanız `gcc: error trying to exec 'cc1plus': execvp: No such file or directory` , Linux dağıtım için GCC derleme araçlarını yükledikten sonra. Örneğin, Ubuntu 'da komutunu kullanın `sudo apt-get install build-essential` .

    1. Yeni bir Conda ortamı oluşturmak için automl_setup ilk parametre olarak yeni bir ad geçirin. Kullanarak mevcut Conda ortamlarını görüntüleyin `conda env list` ve ile kaldırın `conda env remove -n <environmentname>` .

* **automl_setup_linux. sh başarısız**: automl_setup_linus. sh şu hatayla başarısız Ubuntu Linux olur: `unable to execute 'gcc': No such file or directory`

  1. 53 ve 80 giden bağlantı noktalarının etkinleştirildiğinden emin olun. Bir Azure sanal makinesinde, VM 'yi seçip **ağ iletişimi**' ne tıklayarak bunu Azure Portal yapabilirsiniz.
  1. Şu komutu çalıştırın: `sudo apt-get update`
  1. Şu komutu çalıştırın: `sudo apt-get install build-essential --fix-missing`
  1. `automl_setup_linux.sh`Yeniden çalıştır

* **Configuration. ipynb başarısız olur**:

  * Yerel Conda için, önce başarıyla çalıştığından emin olun `automl_setup` .
  * Subscription_id doğru olduğundan emin olun. Tüm hizmet ve abonelikler ' i seçerek Azure portal subscription_id bulun. "<" ve ">" karakterlerinin subscription_id değere dahil edilmemelidir. Örneğin, `subscription_id = "12345678-90ab-1234-5678-1234567890abcd"` geçerli biçimi vardır.
  * Aboneliğe katkıda bulunan veya sahip erişiminin olduğundan emin olun.
  * Bölgenin desteklenen bölgelerden biri olup olmadığını denetleyin: `eastus2` , `eastus` ,, `westcentralus` `southeastasia` , `westeurope` , `australiaeast` , `westus2` , `southcentralus` .
  * Azure portal kullanarak bölgeye erişim sağlayın.
  
* **Workspace.from_config başarısız olur**:

  Çağrı başarısız olursa `ws = Workspace.from_config()` :

  1. Configuration. ipynb Not defterinin başarıyla çalıştığından emin olun.
  1. Not defteri, çalıştığı klasör altında olmayan bir klasörden çalıştırıldıysa `configuration.ipynb` , aml_config klasörü ve dosyanın içerdiği config.jsdosyayı yeni klasöre kopyalayın. Workspace.from_config, Not defteri klasörü veya onun üst klasörü için config.jsokur.
  1. Yeni bir abonelik, kaynak grubu, çalışma alanı veya bölge kullanılıyorsa, `configuration.ipynb` Not defterini yeniden çalıştırdığınızdan emin olun. config.jsdoğrudan üzerinde değiştirmek, yalnızca belirtilen abonelik altındaki belirtilen kaynak grubunda çalışma alanı zaten mevcutsa çalışır.
  1. Bölgeyi değiştirmek istiyorsanız, çalışma alanını, kaynak grubunu veya aboneliği değiştirin. `Workspace.create` , belirtilen bölge farklı olsa da, zaten varsa, bir çalışma alanı oluşturmaz veya güncelleştirmeyecektir.

## <a name="tensorflow"></a>TensorFlow

SDK 'nın sürüm 1.5.0 itibariyle otomatik makine öğrenimi, TensorFlow modellerini varsayılan olarak yüklemez. TensorFlow 'u yüklemek ve otomatik ML denemeleri ile kullanmak için aracılığıyla uygulamasını kullanın `tensorflow==1.12.0` `CondaDependencies` .

```python
  from azureml.core.runconfig import RunConfiguration
  from azureml.core.conda_dependencies import CondaDependencies
  run_config = RunConfiguration()
  run_config.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['tensorflow==1.12.0'])
```

## <a name="numpy-failures"></a>Sayısal tuş takımı hatalarının

* **`import numpy` Windows 'da başarısız oldu**: bazı Windows ortamlarında, en son Python sürümü 3.6.8 ile bir sayısal tuş takımı yükleme hatası görüntülenir. Bu sorunu görürseniz Python sürüm 3.6.7 ile deneyin.

* **`import numpy` başarısız oldu**: otomatik ml Conda ortamındaki TensorFlow sürümünü denetleyin. Desteklenen sürümler < 1,13 ' dir. Sürüm >= 1,13 ise, TensorFlow ortamdan kaldırın.

TensorFlow sürümünü ve kaldırma işlemini aşağıdaki şekilde kontrol edebilirsiniz:

  1. Bir komut kabuğu başlatın, otomatik ml paketlerinin yüklendiği Conda ortamını etkinleştirin.
  1. `pip freeze` `tensorflow` Bulunursa, listelenen sürüm < 1,13 olmalıdır.
  1. Listelenen sürüm desteklenen bir sürüm değilse, `pip uninstall tensorflow` komut kabuğu 'nda, onay için y girin.

## `jwt.exceptions.DecodeError`

Tam hata iletisi: `jwt.exceptions.DecodeError: It is required that you pass in a value for the "algorithms" argument when calling decode()` .

SDK sürümleri için <= 1.17.0, yükleme desteklenmeyen bir PyJWT sürümü ile sonuçlanabilir. Otomatik ml Conda ortamındaki PyJWT sürümünün desteklenen bir sürüm olduğundan emin olun. Bu, PyJWT sürümü < 2.0.0.

PyJWT sürümünü aşağıdaki şekilde kontrol edebilirsiniz:

1. Bir komut kabuğu başlatın ve otomatik ML paketlerinin yüklendiği Conda ortamını etkinleştirin.

1. `pip freeze` `PyJWT` Bulunursa, listelenen sürümün < 2.0.0 olması gerektiğini belirtin

Listelenen sürüm desteklenen bir sürüm değilse:

1. En son oto ml SDK sürümüne yükseltmeyi göz önünde bulundurun: `pip install -U azureml-sdk[automl]`

1. Bu önemli değilse, ortamdan PyJWT ' yi kaldırın ve doğru sürümü aşağıdaki şekilde yükleme:

    1. `pip uninstall PyJWT` yazın ve `y` onay için girin.
    1. Uygulamasını kullanarak `pip install 'PyJWT<2.0.0'` .
  
## <a name="databricks"></a>Databricks
[Databricks ile OTOMATIKLEŞTIRILMIŞ ml denemesini yapılandırma](how-to-configure-databricks-automl-environment.md#troubleshooting)konusuna bakın.

## <a name="forecasting-r2-score-is-always-zero"></a>Tahmin R2 puanı her zaman sıfırdır

 Bu sorun, belirtilen eğitim verilerinde son veri noktaları için aynı değeri içeren zaman serisi varsa ortaya çıkar `n_cv_splits`  +  `forecasting_horizon` .

Zaman seriniz içinde bu kalıp bekleniyorsa, birincil ölçümünüzün **normalleştirilmiş kök ortalama kare hatasına** geçiş yapabilirsiniz.

## <a name="failed-deployment"></a>Başarısız dağıtım

 SDK 'nın <= 1.18.0 sürümleri için, dağıtım için oluşturulan temel görüntü şu hatayla başarısız olabilir: `ImportError: cannot import name cached_property from werkzeug` .

  Aşağıdaki adımlar soruna geçici bir çözüm olarak çalışabilir:

  1. Model paketini indirin
  1. Paketi unzip
  1. Daraltılmış varlıkları kullanarak dağıtma

## <a name="azure-functions-application"></a>Azure Işlevleri uygulaması
  
  Otomatik ML Şu anda Azure Işlevleri uygulamalarını desteklemez. 

## <a name="sample-notebook-failures"></a>Örnek Not defteri sorunları

 Örnek bir not defteri, özelliğin, yöntemin veya kitaplığın bulunmadığı bir hata ile başarısız olursa:

* Jupyter Notebook doğru çekirdeğin seçildiğinden emin olun. Çekirdek, Not Defteri sayfasının sağ üst kısmında görüntülenir. Varsayılan değer *azure_automl*. Çekirdek, Not defterinin bir parçası olarak kaydedilir. Yeni bir Conda ortamına geçerseniz, not defterinde yeni çekirdeği seçmeniz gerekir.

  * Azure Notebooks, Python 3,6 olmalıdır.
  * Yerel Conda ortamları için, automl_setup ' de belirttiğiniz Conda ortam adı olmalıdır.

* Not defterinin kullandığınız SDK sürümü için olduğundan emin olmak için
  * Jupyter Notebook bir hücrede yürüterek SDK sürümünü denetleyin `azureml.core.VERSION` .
  * Aşağıdaki adımları izleyerek, örnek Not defterlerinin önceki sürümünü GitHub 'dan indirebilirsiniz:
    1. Düğmeyi seçin `Branch`
    1. `Tags`Sekmeye git
    1. Sürümü seçin

## <a name="next-steps"></a>Sonraki adımlar

+ [Otomatik makine öğrenimi ile regresyon modelini eğitme](tutorial-auto-train-models.md) veya [uzak bir kaynakta otomatik makine öğrenimi kullanarak nasıl eğitme](how-to-auto-train-remote.md)yapılacağı hakkında daha fazla bilgi edinin.

+ [Bir modelin nasıl ve nereye dağıtılacağı](how-to-deploy-and-where.md)hakkında daha fazla bilgi edinin.
