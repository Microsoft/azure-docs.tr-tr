---
title: Machine Learning ve veri bilimi araçları
description: Veri Bilimi Sanal Makinesi önceden yüklenmiş makine öğrenimi araçları ve çerçeveleri hakkında bilgi edinin.
keywords: veri bilimi araçları, veri bilimi sanal makinesi, veri bilimi için araçlar, linux veri bilimi
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: conceptual
ms.date: 10/11/2019
ms.openlocfilehash: 8eae1501973d24dc468056229e8a762910f2f181
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72330751"
---
# <a name="machine-learning-and-data-science-tools-on-azure-data-science-virtual-machines"></a>Azure veri bilimi sanal makinelerinde makine öğrenimi ve veri bilimi araçları
Azure veri bilimi sanal makineleri (DSVMs), Python, R ve Julia gibi popüler dillerde makine öğrenimi için zengin bir araç ve kitaplık kümesine sahiptir.

DSVMs üzerindeki makine öğrenimi araçlarından ve kitaplıklarından bazıları aşağıda verilmiştir.

## <a name="azure-machine-learning-sdk-for-python"></a>Python için SDK Azure Machine Learning

[Python için Azure MACHINE LEARNING SDK](https://docs.microsoft.com/azure/machine-learning/service/overview-what-is-azure-ml)'nın tam başvurusuna bakın.

|    |           |
| ------------- | ------------- |
| Nedir?   |   Azure Machine Learning, makine öğrenimi modelleri geliştirip dağıtmak için kullanabileceğiniz bir bulut hizmetidir. Bunları, Python SDK 'sını kullanarak oluştururken, eğerek, ölçeklendirerek ve yönetirken modellerinizi izleyebilirsiniz. Modelleri kapsayıcı olarak dağıtın ve bulutta, şirket içinde veya Azure IoT Edge üzerinde çalıştırın.   |
| Desteklenen sürümler     | Windows (Conda Environment: AzureML), Linux (Conda ortamı: py36)    |
| Tipik kullanımlar      | Genel makine öğrenimi platformu      |
| Nasıl yapılandırılır veya yüklenir?      |  GPU desteğiyle yüklendi   |
| Nasıl kullanılır veya çalıştırılır      | Bir Python SDK ve Azure CLı olarak. Windows sürümünde *veya* Linux edition 'da `py36` ' @no__t Conda ortamına etkinleştirin.      |
| Örneklere bağlantı      | Örnek jupi Not defterleri, Not defterleri altındaki `AzureML` dizinine dahildir.  |
| İlgili araçlar      | Visual Studio Code, Jupyıter   |

## <a name="h2o"></a>H2O

|    |           |
| ------------- | ------------- |
| Nedir?   | Bellek içi, dağıtılmış, hızlı ve ölçeklenebilir makine öğrenimini destekleyen açık kaynaklı bir AI platformu.  |
| Desteklenen sürümler      | Linux   |
| Tipik kullanımlar      | Genel amaçlı dağıtılmış, ölçeklenebilir makine öğrenimi   |
| Nasıl yapılandırılır veya yüklenir?      | H2O `/dsvm/tools/h2o` ' a yüklenir.      |
| Nasıl kullanılır veya çalıştırılır      | X2Go kullanarak VM 'ye bağlanın. Yeni bir Terminal başlatın ve `java -jar /dsvm/tools/h2o/current/h2o.jar` ' ı çalıştırın. Sonra bir Web tarayıcısı başlatın ve `http://localhost:54321` ' a bağlanın.      |
| Örneklere bağlantı      | Örnek, `h2o` dizini altında jupi içindeki VM 'de kullanılabilir.      |
| İlgili araçlar      | Apache Spark, MXNet, XGBoost, parlak su, derin su    |

Dsvms üzerinde, DSVMs için Anaconda Python dağıtımının parçası olan popüler `scikit-learn` paketi gibi diğer makine öğrenimi kitaplıkları vardır. Python, R ve Julia 'da bulunan paketlerin listesini denetlemek için ilgili paket yöneticilerini çalıştırın.

## <a name="lightgbm"></a>LightGBM

|    |           |
| ------------- | ------------- |
| Nedir?   | Karar ağacı algoritmalarına dayanan hızlı, dağıtılmış, yüksek performanslı gradyan-arttırma (GBDT, GBRT, GBM veya MART) çerçevesi. Bu, sıralama, sınıflandırma ve diğer birçok makine öğrenimi görevi için kullanılır.    |
| Desteklenen sürümler      | Windows, Linux    |
| Tipik kullanımlar      | Genel amaçlı gradyan arttırma çerçevesi      |
| Nasıl yapılandırılır veya yüklenir?      | Windows 'ta, LightGBM bir Python paketi olarak yüklenir. Linux 'ta, komut satırı çalıştırılabilir dosyası `/opt/LightGBM/lightgbm` ' dır, R paketi yüklenir ve Python paketleri yüklenir.     |
| Örneklere bağlantı      | [LightGBM Kılavuzu](https://github.com/Microsoft/LightGBM/tree/master/examples/python-guide)   |
| İlgili araçlar      | MXNet, XgBoost  |

## <a name="rattle"></a>Rattle
|    |           |
| ------------- | ------------- |
| Nedir?   |   R kullanarak veri madenciliği için grafik kullanıcı arabirimi.   |
| Desteklenen sürümler     | Windows, Linux     |
| Tipik kullanımlar      | R için genel UI veri araştırma aracı    |
| Nasıl kullanılır veya çalıştırılır      | Bir kullanıcı arabirimi aracı olarak. Windows üzerinde, bir komut istemi başlatın, R çalıştırın ve ardından R içinde `rattle()` ' ı çalıştırın. Linux 'ta X2Go ile bağlanın, bir Terminal başlatın, R çalıştırın ve ardından R içinde `rattle()` ' ı çalıştırın. |
| Örneklere bağlantı      | [Rattle](https://togaware.com/onepager/) |
| İlgili araçlar      |LightGBM, WEKA, XGBoost   |

## <a name="vowpal-wabbit"></a>Vowpal Wabbit
|    |           |
| ------------- | ------------- |
| Nedir?   |   Hızlı, açık kaynaklı, çekirdek olmayan bir öğrenme sistemi kitaplığı    |
| Desteklenen sürümler     | Windows, Linux     |
| Tipik kullanımlar      | Genel makine-öğrenme kitaplığı      |
| Nasıl yapılandırılır veya yüklenir?      |  Windows: msi yükleyicisi<br/>Linux: apt-get |
| Nasıl kullanılır veya çalıştırılır      | Yol üzerine bir komut satırı aracı (Windows üzerinde `C:\Program Files\VowpalWabbit\vw.exe`, Linux üzerinde `/usr/bin/vw`)    |
| Örneklere bağlantı      | [VowPal Wabbit örnekleri](https://github.com/JohnLangford/vowpal_wabbit/wiki/Examples) |
| İlgili araçlar      |LightGBM, MXNet, XGBoost   |


## <a name="weka"></a>WEKA
|    |           |
| ------------- | ------------- |
| Nedir?   |  Veri araştırma görevleri için makine öğrenimi algoritmalarının koleksiyonu. Algoritmalar doğrudan bir veri kümesine uygulanabilir ya da kendi Java kodınızdan çağrılabilir. WEKA, veri ön işleme, sınıflandırma, gerileme, kümeleme, ilişkilendirme kuralları ve görselleştirme için araçlar içerir. |
| Desteklenen sürümler     | Windows, Linux     |
| Tipik kullanımlar      | Genel makine öğrenme aracı     |
| Nasıl kullanılır veya çalıştırılır      | Windows 'ta, **Başlat** menüsünde WEKA araması yapın. Linux 'ta X2Go ile oturum açın ve **uygulamalar** > **geliştirme** > **WEKA ' a**gidin. |
| Örneklere bağlantı      | [WEKA örnekleri](https://www.cs.waikato.ac.nz/ml/weka/documentation.html) |
| İlgili araçlar      |LightGBM, Rattle, XGBoost   |

## <a name="xgboost"></a>XGBoost 
|    |           |
| ------------- | ------------- |
| Nedir?   |   Python, R, Java, Scala C++ve daha fazlası için hızlı, taşınabilir ve dağıtılmış gradyan-arttırma (gbdt, GBRT veya GBM) kitaplığı. Tek bir makine üzerinde ve Apache Hadoop ve Spark üzerinde çalışır.    |
| Desteklenen sürümler     | Windows, Linux     |
| Tipik kullanımlar      | Genel makine-öğrenme kitaplığı      |
| Nasıl yapılandırılır veya yüklenir?      |  GPU desteğiyle yüklendi   |
| Nasıl kullanılır veya çalıştırılır      | Python kitaplığı (2,7 ve 3,5), R paketi ve yol üzerine komut satırı aracı (Windows için `C:\dsvm\tools\xgboost\bin\xgboost.exe` ve Linux için `/dsvm/tools/xgboost/xgboost`)    |
| Örneklere bağlantılar      | Örnekler, Linux üzerinde `/dsvm/tools/xgboost/demo` ve Windows üzerinde `C:\dsvm\tools\xgboost\demo` ' de bulunur.   |
| İlgili araçlar      | LightGBM, MXNet   |


