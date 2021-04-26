---
title: Machine Learning ve veri bilimi araçları
titleSuffix: Azure Data Science Virtual Machine
description: Veri Bilimi Sanal Makinesi önceden yüklenmiş makine öğrenimi araçları ve çerçeveleri hakkında bilgi edinin.
keywords: veri bilimi araçları, veri bilimi sanal makinesi, veri bilimi için araçlar, linux veri bilimi
services: machine-learning
ms.service: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: d78b106464bfbd3c86b9899a5c3543eed5dc0924
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104888966"
---
# <a name="machine-learning-and-data-science-tools-on-azure-data-science-virtual-machines"></a>Azure veri bilimi sanal makinelerinde makine öğrenimi ve veri bilimi araçları
Azure veri bilimi sanal makineleri (DSVMs), Python, R ve Julia gibi popüler dillerde makine öğrenimi için zengin bir araç ve kitaplık kümesine sahiptir.

DSVMs üzerindeki makine öğrenimi araçlarından ve kitaplıklarından bazıları aşağıda verilmiştir.

## <a name="azure-machine-learning-sdk-for-python"></a>Python için Azure Machine Learning SDK'sı

[Python için Azure MACHINE LEARNING SDK](../overview-what-is-azure-ml.md)'nın tam başvurusuna bakın.

| Kategori | Değer |
| ------------- | ------------- |
| Nedir o?   |   Azure Machine Learning, makine öğrenimi modelleri geliştirip dağıtmak için kullanabileceğiniz bir bulut hizmetidir. Bunları, Python SDK 'sını kullanarak oluştururken, eğerek, ölçeklendirerek ve yönetirken modellerinizi izleyebilirsiniz. Modelleri kapsayıcı olarak dağıtın ve bulutta, şirket içinde veya Azure IoT Edge üzerinde çalıştırın.   |
| Desteklenen sürümler     | Windows (Conda Environment: AzureML), Linux (Conda ortamı: py36)    |
| Tipik kullanımlar      | Genel makine öğrenimi platformu      |
| Nasıl yapılandırılır veya yüklenir?      |  GPU desteğiyle yüklendi   |
| Nasıl kullanılır veya çalıştırılır      | Bir Python SDK ve Azure CLı olarak. `AzureML`Windows sürümünde *veya* Linux Edition 'da Conda ortamına etkinleştirin `py36` .      |
| Örneklere bağlantı      | Örnek jupi Not defterleri, `AzureML` Not defterleri altındaki dizine eklenir.  |
| İlgili araçlar      | Visual Studio Code, Jupyıter   |

## <a name="h2o"></a>H2O

| Kategori | Değer |
| ------------- | ------------- |
| Nedir o?   | Bellek içi, dağıtılmış, hızlı ve ölçeklenebilir makine öğrenimini destekleyen açık kaynaklı bir AI platformu.  |
| Desteklenen sürümler      | Linux   |
| Tipik kullanımlar      | Genel amaçlı dağıtılmış, ölçeklenebilir makine öğrenimi   |
| Nasıl yapılandırılır veya yüklenir?      | H2O yüklendi `/dsvm/tools/h2o` .      |
| Nasıl kullanılır veya çalıştırılır      | X2Go kullanarak VM 'ye bağlanın. Yeni bir Terminal başlatın ve çalıştırın `java -jar /dsvm/tools/h2o/current/h2o.jar` . Sonra bir Web tarayıcısı başlatın ve ' e bağlanın `http://localhost:54321` .      |
| Örneklere bağlantı      | Örnek, dizin altında Jupo 'da bulunan VM 'de kullanılabilir `h2o` .      |
| İlgili araçlar      | Apache Spark, MXNet, XGBoost, parlak su, derin su    |

Dsvms üzerinde, `scikit-learn` dsvms Için Anaconda Python dağıtımının parçası olan popüler paket gibi diğer makine öğrenimi kitaplıkları vardır. Python, R ve Julia 'da bulunan paketlerin listesini denetlemek için ilgili paket yöneticilerini çalıştırın.

## <a name="lightgbm"></a>LightGBM

| Kategori | Değer |
| ------------- | ------------- |
| Nedir o?   | Karar ağacı algoritmalarına dayanan hızlı, dağıtılmış, yüksek performanslı gradyan-arttırma (GBDT, GBRT, GBM veya MART) çerçevesi. Bu, sıralama, sınıflandırma ve diğer birçok makine öğrenimi görevi için kullanılır.    |
| Desteklenen sürümler      | Windows, Linux    |
| Tipik kullanımlar      | Genel amaçlı gradyan arttırma çerçevesi      |
| Nasıl yapılandırılır veya yüklenir?      | Windows 'ta, LightGBM bir Python paketi olarak yüklenir. Linux 'ta, komut satırı çalıştırılabilir dosyası, `/opt/LightGBM/lightgbm` R paketi yüklenir ve Python paketleri yüklenir.     |
| Örneklere bağlantı      | [LightGBM Kılavuzu](https://github.com/Microsoft/LightGBM/tree/master/examples/python-guide)   |
| İlgili araçlar      | MXNet, XgBoost  |

## <a name="rattle"></a>Rattle
| Kategori | Değer |
| ------------- | ------------- |
| Nedir o?   |   R kullanarak veri madenciliği için grafik kullanıcı arabirimi.   |
| Desteklenen sürümler     | Windows, Linux     |
| Tipik kullanımlar      | R için genel UI veri araştırma aracı    |
| Nasıl kullanılır veya çalıştırılır      | Bir kullanıcı arabirimi aracı olarak. Windows üzerinde, bir komut istemi başlatın, R çalıştırın ve ardından R içinde komutunu çalıştırın `rattle()` . Linux 'ta X2Go ile bağlanın, bir Terminal başlatın, R çalıştırın ve ardından R içinde çalıştırın `rattle()` . |
| Örneklere bağlantı      | [Rattle](https://togaware.com/onepager/) |
| İlgili araçlar      |LightGBM, WEKA, XGBoost   |

## <a name="vowpal-wabbit"></a>Vowpal Wabbit
| Kategori | Değer |
| ------------- | ------------- |
| Nedir o?   |   Hızlı, açık kaynaklı, çekirdek olmayan bir öğrenme sistemi kitaplığı    |
| Desteklenen sürümler     | Windows, Linux     |
| Tipik kullanımlar      | Genel makine-öğrenme kitaplığı      |
| Nasıl yapılandırılır veya yüklenir?      |  Windows: msi yükleyicisi<br/>Linux: apt-get |
| Nasıl kullanılır veya çalıştırılır      | Yol üzerine bir komut satırı aracı ( `C:\Program Files\VowpalWabbit\vw.exe` Windows üzerinde, `/usr/bin/vw` Linux üzerinde)    |
| Örneklere bağlantı      | [VowPal Wabbit örnekleri](https://github.com/JohnLangford/vowpal_wabbit/wiki/Examples) |
| İlgili araçlar      |LightGBM, MXNet, XGBoost   |


## <a name="weka"></a>Weka
| Kategori | Değer |
| ------------- | ------------- |
| Nedir o?   |  Veri araştırma görevleri için makine öğrenimi algoritmalarının koleksiyonu. Algoritmalar doğrudan bir veri kümesine uygulanabilir ya da kendi Java kodınızdan çağrılabilir. WEKA, veri ön işleme, sınıflandırma, gerileme, kümeleme, ilişkilendirme kuralları ve görselleştirme için araçlar içerir. |
| Desteklenen sürümler     | Windows, Linux     |
| Tipik kullanımlar      | Genel makine öğrenme aracı     |
| Nasıl kullanılır veya çalıştırılır      | Windows 'ta, **Başlat** menüsünde WEKA araması yapın. Linux 'ta X2Go ile oturum açın ve ardından **uygulamalar**  >  **geliştirme**  >  **WEKA**' ya gidin. |
| Örneklere bağlantı      | [WEKA örnekleri](https://www.cs.waikato.ac.nz/ml/weka/documentation.html) |
| İlgili araçlar      |LightGBM, Rattle, XGBoost   |

## <a name="xgboost"></a>XGBoost 
| Kategori | Değer |
| ------------- | ------------- |
| Nedir o?   |   Python, R, Java, Scala, C++ ve daha fazlası için hızlı, taşınabilir ve dağıtılmış gradyan-arttırma (GBDT, GBRT veya GBM) kitaplığı. Tek bir makine üzerinde ve Apache Hadoop ve Spark üzerinde çalışır.    |
| Desteklenen sürümler     | Windows, Linux     |
| Tipik kullanımlar      | Genel makine-öğrenme kitaplığı      |
| Nasıl yapılandırılır veya yüklenir?      |  GPU desteğiyle yüklendi   |
| Nasıl kullanılır veya çalıştırılır      | Python kitaplığı (2,7 ve 3.6 +), R paketi ve yol üzerine komut satırı aracı ( `C:\dsvm\tools\xgboost\bin\xgboost.exe` Windows ve `/dsvm/tools/xgboost/xgboost` Linux için) olarak    |
| Örneklere bağlantılar      | Örnekler, sanal makinede, `/dsvm/tools/xgboost/demo` Linux ve `C:\dsvm\tools\xgboost\demo` Windows üzerinde bulunur.   |
| İlgili araçlar      | LightGBM, MXNet   |

## <a name="apache-drill"></a>Apache Drill
| Kategori | Değer |
| ------------- | ------------- |
| Nedir o?   | Büyük verilerde açık kaynaklı SQL sorgu altyapısı    |
| Desteklenen DSVM sürümleri      | Windows 2019, Linux  |
| DSVM 'de nasıl yapılandırılır ve yüklenir?      |  `/dsvm/tools/drill*`Yalnızca katıştırılmış modda yüklenir   |
| Tipik kullanımlar      |  Ayıklama, dönüştürme, yükleme (ETL) gerekmeden yerinde veri araştırması için. CSV, JSON, ilişkisel tablolar ve Hadoop gibi farklı veri kaynaklarını ve biçimleri sorgulayın.     |
| Kullanma ve çalıştırma      | Masaüstü kısayolu  <br/> [10 dakika içinde detaya gitmeyi kullanmaya başlayın](https://drill.apache.org/docs/drill-in-10-minutes/)  |
| DSVM 'deki ilgili araçlar      |   Rattle, WEKA, SQL Server Management Studio      |