---
title: 'Python betiğini Yürüt: modül başvurusu'
titleSuffix: Azure Machine Learning
description: Python kodunu çalıştırmak için Azure Machine Learning tasarımcısında Python betiği yürütme modülünü nasıl kullanacağınızı öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.custom: devx-track-python
author: likebupt
ms.author: keli19
ms.date: 01/02/2021
ms.openlocfilehash: 6003ca9156d8553604d7ebbf94c5c3373d077f0f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102500708"
---
# <a name="execute-python-script-module"></a>Python betik modülünü Yürüt

Bu makalede Azure Machine Learning tasarımcısında Python betiği yürütme modülü açıklanmaktadır.

Python kodunu çalıştırmak için bu modülü kullanın. Python 'un mimari ve tasarım ilkeleri hakkında daha fazla bilgi için bkz. [Azure Machine Learning tasarımcısında Python kodunu çalıştırma](../how-to-designer-python.md).

Python ile, var olan modüllerin desteklemediği görevleri gerçekleştirebilirsiniz, örneğin:

+ Kullanarak verileri görselleştirme `matplotlib` .
+ Çalışma alanınızdaki veri kümelerini ve modelleri listelemek için Python kitaplıklarını kullanma.
+ [Veri alma](./import-data.md) modülünün desteklemediği kaynaklardaki verileri okuma, yükleme ve düzenleme.
+ Kendi derin öğrenme kodunuzu çalıştırın. 

## <a name="supported-python-packages"></a>Desteklenen Python paketleri

Azure Machine Learning, Python 'un, veri işleme için birçok yaygın yardımcı programını içeren Anaconda dağıtımını kullanır. Anaconda sürümünü otomatik olarak güncelleştireceğiz. Geçerli sürüm:
 -  Python 3,6 için Anaconda 4.5 + dağıtımı 

Tüm liste için bkz. [önceden yüklenmiş Python paketleri](#preinstalled-python-packages)bölümü.

Önceden yüklenmiş listede olmayan paketleri yüklemek için (örneğin, *scikit-misc*), betiğe aşağıdaki kodu ekleyin: 

```python
import os
os.system(f"pip install scikit-misc")
```

Daha iyi performans için, özellikle çıkarım için paketleri yüklemek üzere aşağıdaki kodu kullanın:
```python
import importlib.util
package_name = 'scikit-misc'
spec = importlib.util.find_spec(package_name)
if spec is None:
    import os
    os.system(f"pip install scikit-misc")
```

> [!NOTE]
> İşlem hatlarınız, önceden yüklenmiş listesinde olmayan paketlere ihtiyacı olan birden çok yürütme Python betik modülü içeriyorsa, paketleri her modüle yükler.

> [!WARNING]
> Excute Python betik modülü, Java, PyODBC ve vb. gibi "apt-get" gibi komutla ek yerel kitaplıklara bağlı paketlerin yüklenmesini desteklemez. Bunun nedeni, Bu modülün Python ile önceden yüklenmiş ve yönetici olmayan izinlerle basit bir ortamda yürütüldüğünden oluşur.  

## <a name="access-to-current-workspace-and-registered-datasets"></a>Geçerli çalışma alanına ve kayıtlı veri kümelerine erişim

Çalışma alanınızdaki [kayıtlı veri kümelerine](../how-to-create-register-datasets.md) erişmek için aşağıdaki örnek koda başvurabilirsiniz:

```Python
def azureml_main(dataframe1 = None, dataframe2 = None):

    # Execution logic goes here
    print(f'Input pandas.DataFrame #1: {dataframe1}')
    from azureml.core import Run
    run = Run.get_context(allow_offline=True)
    #access to current workspace
    ws = run.experiment.workspace

    #access to registered dataset of current workspace
    from azureml.core import Dataset
    dataset = Dataset.get_by_name(ws, name='test-register-tabular-in-designer')
    dataframe1 = dataset.to_pandas_dataframe()
     
    # If a zip file is connected to the third input port,
    # it is unzipped under "./Script Bundle". This directory is added
    # to sys.path. Therefore, if your zip file contains a Python file
    # mymodule.py you can import it using:
    # import mymodule

    # Return value must be of a sequence of pandas.DataFrame
    # E.g.
    #   -  Single return value: return dataframe1,
    #   -  Two return values: return dataframe1, dataframe2
    return dataframe1,
```

## <a name="upload-files"></a>Dosyaları karşıya yükleme
Execute Python betik modülü, [Azure Machine Learning Python SDK](/python/api/azureml-core/azureml.core.run%28class%29#upload-file-name--path-or-stream-)kullanarak dosyaları karşıya yüklemeyi destekler.

Aşağıdaki örnek, Python betik modülündeki bir görüntü dosyasının nasıl karşıya yükleneceğini göstermektedir:

```Python

# The script MUST contain a function named azureml_main,
# which is the entry point for this module.

# Imports up here can be used to
import pandas as pd

# The entry point function must have two input arguments:
#   Param<dataframe1>: a pandas.DataFrame
#   Param<dataframe2>: a pandas.DataFrame
def azureml_main(dataframe1 = None, dataframe2 = None):

    # Execution logic goes here
    print(f'Input pandas.DataFrame #1: {dataframe1}')

    from matplotlib import pyplot as plt
    plt.plot([1, 2, 3, 4])
    plt.ylabel('some numbers')
    img_file = "line.png"
    plt.savefig(img_file)

    from azureml.core import Run
    run = Run.get_context(allow_offline=True)
    run.upload_file(f"graphics/{img_file}", img_file)

    # Return value must be of a sequence of pandas.DataFrame
    # For example:
    #   -  Single return value: return dataframe1,
    #   -  Two return values: return dataframe1, dataframe2
    return dataframe1,
}
```

İşlem hattı çalıştırması tamamlandıktan sonra, modülün sağ panelinde görüntünün önizlemesini yapabilirsiniz.

> [!div class="mx-imgBorder"]
> ![Karşıya yüklenen görüntünün önizlemesi](media/module/upload-image-in-python-script.png)

## <a name="how-to-configure-execute-python-script"></a>Python betiğini yürütme betiği nasıl yapılandırılır

Execute Python betik modülü, başlangıç noktası olarak kullanabileceğiniz örnek Python kodunu içerir. Python betik modülünü Yürüt ' ü yapılandırmak için, **Python betiği** metin kutusunda çalıştırılacak bir giriş kümesi ve Python kodu sağlayın.

1. İşlem hattınızla **Python betiği yürütme** modülünü ekleyin.

2. Giriş için kullanmak istediğiniz tasarımcıdan **dataSet1** herhangi bir veri kümesini ekleyin ve bağlayın. Bu veri kümesine Python betiğinizdeki **DataFrame1** olarak başvurun.

    Veri kümesinin kullanımı isteğe bağlıdır. Python kullanarak veri oluşturmak istiyorsanız veya verileri doğrudan modüle aktarmak için Python kodu ' nu kullanın.

    Bu modül, **DataSet2** üzerindeki ikinci bir veri kümesinin eklenmesini destekler. Python betiğinizdeki ikinci veri kümesine **DataFrame2** olarak başvurun.

    Azure Machine Learning depolanan veri kümeleri, bu modülle yüklendiğinde otomatik olarak Pandas veri çerçevelerine dönüştürülür.

    ![Python giriş eşlemesini Yürüt](media/module/python-module.png)

4. Yeni Python paketleri veya kodu eklemek için, bu özel kaynakları içeren daraltılmış dosyayı **betik paketi** bağlantı noktasına bağlayın. Ya da betiğinizin 16 KB 'den büyük olması durumunda, CommandLine gibi hataların *16597 karakter sınırını aşması* Için **betik paketi** bağlantı noktasını kullanın. 

    
    1. Betiği ve diğer özel kaynakları bir ZIP dosyasına paketleyin.
    1. Zip dosyasını bir **dosya veri kümesi** olarak Studio 'ya yükleyin. 
    1. Veri kümesi modülünü tasarımcı yazma sayfasındaki sol modül bölmesinde bulunan *veri kümeleri* listesinden sürükleyin. 
    1. Veri kümesi modülünü **Python betik modülünün yürütme** betik **paketi** bağlantı noktasına bağlayın.
    
    Karşıya yüklenen sıkıştırılmış arşivde bulunan herhangi bir dosya, işlem hattı yürütmesi sırasında kullanılabilir. Arşiv bir dizin yapısı içeriyorsa, yapı korunur.
 
    > [!WARNING]
    > Uygulama yerleşik hizmetler için ayrılmış bir **sözcük olduğundan,** **uygulamayı** klasörün veya betiğinizin adı **olarak kullanmayın.** Ancak gibi diğer ad alanlarını da kullanabilirsiniz `app123` .
   
    Aşağıda, bir Python betik dosyası ve bir txt dosyası içeren bir betik paketi örneği verilmiştir:
      
    > [!div class="mx-imgBorder"]
    > ![Betik paketi örneği](media/module/python-script-bundle.png)  

    Aşağıdakilerin içeriği aşağıdadır `my_script.py` :

    ```python
    def my_func(dataframe1):
    return dataframe1
    ```
    Aşağıda, komut dosyası grubundaki dosyaların kullanımını gösteren örnek kod verilmiştir:    

    ```python
    import pandas as pd
    from my_script import my_func
 
    def azureml_main(dataframe1 = None, dataframe2 = None):
 
        # Execution logic goes here
        print(f'Input pandas.DataFrame #1: {dataframe1}')
 
        # Test the custom defined python function
        dataframe1 = my_func(dataframe1)
 
        # Test to read custom uploaded files by relative path
        with open('./Script Bundle/my_sample.txt', 'r') as text_file:
            sample = text_file.read()
    
        return dataframe1, pd.DataFrame(columns=["Sample"], data=[[sample]])
    ```

5. **Python betiği** metin kutusuna geçerli Python betiği yazın veya yapıştırın.

    > [!NOTE]
    >  Betiğinizi yazarken dikkatli olun. Bildirilmemiş değişkenleri veya içeri aktarılmayan modülleri veya işlevleri kullanma gibi sözdizimi hatası olmadığından emin olun. Önceden yüklenmiş modül listesine daha fazla dikkat ödeyin. Listelenmemiş modülleri içeri aktarmak için, betiğinizdeki ilgili paketleri şu şekilde yüklemelisiniz:
    >  ``` Python
    > import os
    > os.system(f"pip install scikit-misc")
    > ```
    
    **Python betiği** metin kutusu, açıklamalarda bazı yönergelerden ve veri erişimi ve çıkış için örnek kodda önceden doldurulur. Bu kodu düzenlemeniz veya değiştirmeniz gerekir. Girintileme ve büyük küçük harf için Python kurallarını izleyin:

    + Betik, `azureml_main` Bu modül için giriş noktası olarak adlandırılan bir işlev içermelidir.
    + Giriş noktası işlevinin, `Param<dataframe1>` `Param<dataframe2>` Bu bağımsız değişkenler betiğinizde kullanılmasa bile iki giriş bağımsız değişkeni olması gerekir.
    + Üçüncü giriş bağlantı noktasına bağlı daraltılmış dosyalar, `.\Script Bundle` aynı zamanda Python 'a eklenen dizininde sıkıştırıldı ve dizinde depolanmaktadır `sys.path` . 

    . Zip dosyanız içeriyorsa `mymodule.py` , öğesini kullanarak alın `import mymodule` .

    İki veri kümesi tasarımcıya döndürülebilir ve bu tür bir dizi olmalıdır `pandas.DataFrame` . Python kodunuzda başka çıktılar oluşturabilir ve bunları doğrudan Azure depolama 'ya yazabilirsiniz.

    > [!WARNING]
    > **Python betik modülünü yürütme** Içindeki bir veritabanına veya diğer dış depolama alanına **bağlanmanız önerilmez.** [Veri alma modülünü](./import-data.md) ve [dışarı aktarma modülünü](./export-data.md) kullanabilirsiniz     

6. İşlem hattını gönderme.

    Modül tamamlanırsa, beklendiğinde çıktıyı denetleyin.

    Modül başarısız olursa, bazı sorun giderme işlemleri yapmanız gerekir. Modülünü seçin ve sağ bölmedeki **çıktılar + Günlükler** ' i açın. **70_driver_log.txt** açın ve **azureml_main** arama yapın, bu durumda hataya neden olan satırı bulabilirsiniz. Örneğin, "dosya"/tmp/tmp01_ID/user_script. Kopyala ", satır 17, azureml_main",, Python betiğinizin 17. satırında hata oluştuğunu gösterir.

## <a name="results"></a>Sonuçlar

Katıştırılmış Python kodu tarafından yapılan hesaplamaların sonuçları `pandas.DataFrame` , Azure Machine Learning veri kümesi biçimine otomatik olarak dönüştürülecek olarak sağlanmalıdır. Daha sonra sonuçları işlem hattındaki diğer modüllerle birlikte kullanabilirsiniz.

Modül iki veri kümesi döndürür:  
  
+ Bir Python betiğinde ilk döndürülen Pandas veri çerçevesi tarafından tanımlanan **sonuçlar veri kümesi 1**.

+ **Sonuç veri kümesi 2**, bir Python betiğinin ikinci döndürülen Pandas veri çerçevesi tarafından tanımlanır.

## <a name="preinstalled-python-packages"></a>Önceden yüklenmiş Python paketleri
Önceden yüklenmiş paketler şunlardır:
-    adal = = 1.2.2
-    ApplicationInsights = = 0.11.9
-    attrs = = 19.3.0
-    Azure-ortak = = 1.1.25
-    Azure-Core = = 1.3.0
-    Azure-graphrbac = = 0.61.1
-    Azure-Identity = = 1.3.0
-    Azure-MGMT-Authorization = = 0.60.0
-    Azure-MGMT-containerregistry = = 2.8.0
-    Azure-MGMT-keykasası = = 2.2.0
-    Azure-MGMT-Resource = = 8.0.1
-    Azure-MGMT-depolama = = 8.0.0
-    Azure-Storage-blob = = 1.5.0
-    Azure-Storage-ortak = = 1.4.2
-    azureml-çekirdek = = 1.1.5.5
-    azureml-dataprep-Native = = 14.1.0
-    azureml-dataprep = = 1.3.5
-    azureml-varsayılanlar = = 1.1.5.1
-    azureml-tasarımcı-klasik-modüller = = 0.0.118
-    azureml-tasarımcı-çekirdek = = 0.0.31
-    azureml-tasarımcı-iç = = 0.0.18
-    azureml-model-Yönetim-SDK = = 1.0.1 B6. post1
-    azureml-işlem hattı-çekirdek = = 1.1.5
-    azureml-telemetri = = 1.1.5.3
-    geribağlantı noktaları. TempFile = = 1.0
-    backports. WeakRef = = 1.0. post1
-    boto3 = = 1.12.29
-    botocore = = 1.15.29
-    cachetools = = 4.0.0
-    certifi = = 2019.11.28
-    cffi = = 1.12.3
-    chardet = = 3.0.4
-    = = 7.1.1 öğesine tıklayın
-    cloudpickle = = 1.3.0
-    configparser = = 3.7.4
-    contextlib2 = = 0.6.0. post1
-    şifreleme = = 2.8
-    ok = = 0.10.0
-    Dill = = 0.3.1.1
-    disden = = 1.4.0
-    Docker = = 4.2.0
-    docutils = = 0.15.2
-    dotnetcore2 = = 2.1.13
-    Flask = = 1.0.3
-    fusekopyala = = 3.0.1
-    gensim = = 3.8.1
-    Google-api-Core = = 1.16.0
-    Google-auth = = 1.12.0
-    Google-Cloud-Core = = 1.3.0
-    Google-Cloud-Storage = = 1.26.0
-    Google-sürdürülebilir-Media = = 0.5.0
-    googleapis-ortak-prototips = = 1.51.0
-    gunic, = = 19.9.0
-    IDNA = = 2.9
-    imdengeli-öğren = = 0.4.3
-    iztarihi = = 0.6.0
-    ısdangerou = = 1.1.0
-    jeepney = = 0.4.3
-    jinja2 = = 2.11.1
-    jmespath = = 0.9.5
-    joblib = = 0.14.0
-    JSON-Logging-Kopyala = = 0.2
-    jsonpickle = = 1.3
-    jsonschema = = 3.0.1
-    kiwıolver = = 1.1.0
-    Liac-arff = = 2.4.0
-    lightgbm = = 2.2.3
-    markupsafe = = 1.1.1
-    Matplotlib = = 3.1.3
-    more-ıtertools = = 6.0.0
-    msal-Extensions = = 0.1.3
-    msal = = 1.1.0
-    msrest = = 0.6.11
-    msrestazure = = 0.6.3
-    NDG-httpsclient = = 0.5.1
-    nimbusml = = 1.6.1
-    sayısal tuş takımı = = 1.18.2
-    oauthlib = = 3.1.0
-    Pandas = = 0.25.3
-    pathSpec = = 0.7.0
-    PIP = = 20.0.2
-    portalocker = = 1.6.0
-    protoarabellek = = 3.11.3
-    pyarrow = = 0.16.0
-    pyasn1-modüller = = 0.2.8
-    pyasn1 = = 0.4.8
-    pycparser = = 2.20
-    pycryptodomex = = 3.7.3
-    pyjwt = = 1.7.1
-    pyopenssl = = 19.1.0
-    pyparsing = = 2.4.6
-    pyrsistent = = 0.16.0
-    Python-dateutil = = 2.8.1
-    pytz = = 2019.3
-    istekler-oauthlib = = 1.3.0
-    istekler = = 2.23.0
-    RSA = = 4.0
-    ruamel. YAML = = 0.15.89
-    s3transfer = = 0.3.3
-    scikit-öğren = = 0.22.2
-    SciPy = = 1.4.1
-    secretstorage = = 3.1.2
-    setuptools = = 46.1.1. post20200323
-    altı = = 1.14.0
-    Smart-Open = = 1.10.0
-    urllib3 = = 1.25.8
-    WebSocket-istemci = = 0.57.0
-    Werkzeug = = 0.16.1
-    tekerlek = = 0.34.2

## <a name="next-steps"></a>Sonraki adımlar

Azure Machine Learning için [kullanılabilen modül kümesine](module-reference.md) bakın.