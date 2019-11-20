---
title: 'Python betiğini Yürüt: modül başvurusu'
titleSuffix: Azure Machine Learning
description: Python kodunu çalıştırmak için Azure Machine Learning Python betiğini yürütme modülünü nasıl kullanacağınızı öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: 1ba10bf682d900a45f345f2ebe2707ba1275e94e
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73497857"
---
# <a name="execute-python-script-module"></a>Python betik modülünü Yürüt

Bu makalede Azure Machine Learning tasarımcısında modül (Önizleme) açıklanmaktadır.

Python kodunu çalıştırmak için bu modülü kullanın. Python 'un mimari ve tasarım ilkeleri hakkında daha fazla bilgi için [aşağıdaki makaleye](https://docs.microsoft.com/azure/machine-learning/machine-learning-execute-python-scripts)bakın.

Python ile Şu anda mevcut modüller tarafından desteklenmeyen görevleri gerçekleştirebilirsiniz:

+ `matplotlib` kullanarak verileri görselleştirme
+ Çalışma alanınızdaki veri kümelerini ve modelleri numaralandırmak için Python kitaplıklarını kullanma
+ [Verileri Içeri aktarma](./import-data.md) modülü tarafından desteklenmeyen kaynaklardaki verileri okuma, yükleme ve düzenleme
+ Kendi derin öğrenme kodunuzu çalıştırın 


Azure Machine Learning, Python 'un, veri işleme için birçok yaygın yardımcı programını içeren Anaconda dağıtımını kullanır. Anaconda sürümünü otomatik olarak güncelleştireceğiz. Geçerli sürüm:
 -  Python 3,6 için Anaconda 4.5 + dağıtımı 

Önceden yüklenmiş paketler şunlardır:
-  asn1crypto = = 0.24.0
- attrs = = 19.1.0
- Azure-ortak = = 1.1.18
- Azure-Storage-blob = = 1.5.0
- Azure-Storage-ortak = = 1.4.0
- certifi = = 2019.3.9
- cffi = = 1.12.2
- chardet = = 3.0.4
- şifreleme = = 2.6.1
- disden = = 1.4.0
- IDNA = = 2.8
- jsonschema = = 3.0.1
- lightgbm = = 2.2.3
- more-ıtertools = = 6.0.0
- sayısal tuş takımı = = 1.16.2
- Pandas = = 0.24.2
- Pillow = = 6.0.0
- PIP = = 19.0.3
- pyarrow = = 0.12.1
- pycparser = = 2.19
- pycryptodomex = = 3.7.3
- pyrsistent = = 0.14.11
- Python-dateutil = = 2.8.0
- pytz = = 2018.9
- istekler = = 2.21.0
- scikit-öğren = = 0.20.3
- SciPy = = 1.2.1
- setuptools = = 40.8.0
- altı = = 1.12.0
- Torch = = 1.0.1. post2
- torchvision = = 0.2.2. post3
- urllib3 = = 1.24.1
- tekerlek = = 0.33.1 

 Önceden yüklenmiş listede olmayan diğer paketleri yüklemek için, örneğin *scikit-misc*, aşağıdaki kodu betiğe ekleyin: 

 ```python
import os
os.system(f"pip install scikit-misc")
```

## <a name="how-to-use"></a>Nasıl kullanılır

**Execute Python betik** modülü, başlangıç noktası olarak kullanabileceğiniz örnek Python kodunu içerir. Python betik modülünü **Yürüt** ' ü yapılandırmak Için, **Python betiği** metin kutusunda yürütülecek bir giriş ve Python kodu kümesi sağlarsınız.

1. İşlem hattınızla **Python betiği yürütme** modülünü ekleyin.

2. Giriş için kullanmak istediğiniz tasarımcıdan **dataSet1** herhangi bir veri kümesini ekleyin ve bağlayın. Bu veri kümesine Python betiğinizdeki **DataFrame1**olarak başvurun.

    Veri kümesinin kullanımı isteğe bağlıdır, Python kullanarak veri oluşturmak istiyorsanız veya verileri doğrudan modüle aktarmak için Python kodu kullanın.

    Bu modül, **DataSet2**üzerindeki ikinci veri kümesinin eklenmesini destekler. Python betiğinizdeki ikinci veri kümesine DataFrame2 olarak başvurun.

    Azure Machine Learning depolanan veri kümeleri, bu modülle yüklendiğinde otomatik olarak **Pandas** Data. Frames 'e dönüştürülür.

    ![Python giriş eşlemesini Yürüt](media/module/python-module.png)

4. Yeni Python paketleri veya kodu eklemek için, bu özel kaynakları içeren daraltılmış dosyayı **betik paketi**'ne ekleyin. **Betiğe** yapılan giriş, çalışma alanınıza zaten yüklenmiş bir sıkıştırılmış dosya olmalıdır. 

    Karşıya yüklenen sıkıştırılmış arşivde bulunan herhangi bir dosya, işlem hattı yürütmesi sırasında kullanılabilir. Arşiv bir dizin yapısı içeriyorsa, yapı korunur, ancak **src** adlı bir dizini yola eklemek zorundasınız.

5. **Python betiği** metin kutusuna geçerli Python betiği yazın veya yapıştırın.

    **Python betiği** metin kutusu, açıklamalarda bazı yönergeler ve veri erişimi ve çıktısı için örnek kod ile önceden doldurulur. Bu kodu düzenlemeniz veya değiştirmeniz gerekir. Girintileme ve büyük küçük harf ile ilgili Python kurallarını izlediğinizden emin olun.

    + Betik, Bu modülün giriş noktası olarak `azureml_main` adlı bir işlev içermelidir.
    + Giriş noktası işlevi, en fazla iki giriş bağımsız değişkeni içerebilir: `Param<dataframe1>` ve `Param<dataframe2>`
    + Üçüncü giriş bağlantı noktasına bağlı daraltılmış dosyalar, `.\Script Bundle`, Python `sys.path`de eklenen dizininde sıkıştırıldı ve dizinde depolanmaktadır. 

    Bu nedenle, ZIP dosyanız `mymodule.py`içeriyorsa `import mymodule`kullanarak içeri aktarın.

    + İki veri kümesi, `pandas.DataFrame`türünde bir dizi olması gereken tasarımcıya döndürülür. Python kodunuzda başka çıktılar oluşturabilir ve bunları doğrudan Azure depolama 'ya yazabilirsiniz.

6. İşlem hattını çalıştırın veya modülü seçip yalnızca Python betiğini çalıştırmak için **Seçileni Çalıştır** ' a tıklayın.

    Tüm veriler ve kodlar bir sanal makineye yüklenir ve belirtilen Python ortamı kullanılarak çalıştırılır.

## <a name="results"></a>Sonuçlar

Katıştırılmış Python kodu tarafından gerçekleştirilen hesaplamaların sonuçlarının bir Pandas olarak sağlanması gerekir. Sonuçları, işlem hattındaki diğer modüllerle kullanabilmeniz için Azure Machine Learning veri kümesi biçimine otomatik olarak dönüştürülecek olan DataFrame.

Modül iki veri kümesi döndürür:  
  
+ Python betikteki ilk döndürülen Pandas dataframe tarafından tanımlanan **sonuçlar veri kümesi 1**

+ **Sonuç veri kümesi 2**, Python betiğinde ikinci döndürülen Pandas dataframe tarafından tanımlanır


## <a name="next-steps"></a>Sonraki adımlar

Azure Machine Learning için [kullanılabilen modül kümesine](module-reference.md) bakın. 