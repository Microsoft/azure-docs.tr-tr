---
title: 'ML Studio (klasik): Python betiklerini yürütme-Azure'
description: Machine Learning Studio (klasik) denemeleri ve Web hizmetlerinde Python kodu kullanmak için Python betiği yürütme modülünü nasıl kullanacağınızı öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: devx-track-python, previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/12/2019
ms.openlocfilehash: 374589212b230e6b3ce0abcadbad8aa3eb7271fc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "100519992"
---
# <a name="execute-python-machine-learning-scripts-in-azure-machine-learning-studio-classic"></a>Azure Machine Learning Studio (klasik) üzerinde Python Machine Learning betikleri yürütme

**Uygulama hedefi:** ![ İçin geçerlidir. ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (klasik) ![ için geçerlidir.](../../../includes/media/aml-applies-to-skus/no.png)[ Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  


Python birçok veri bilimcilerinin araç çındaki değerli bir araçtır. Veri araştırması, özellik ayıklama, model eğitimi ve doğrulama ve dağıtım dahil olmak üzere tipik makine öğrenimi iş akışlarının her aşamasında kullanılır.

Bu makalede, Azure Machine Learning Studio (klasik) denemeleri ve Web hizmetlerinde Python kodu kullanmak için Python betik modülünü yürütme işlemini nasıl kullanabileceğiniz açıklanır.

## <a name="using-the-execute-python-script-module"></a>Python betiği yürütme modülünü kullanma

Studio 'da (klasik) Python 'a yönelik birincil arabirim, [Python betik modülünü Çalıştır][execute-python-script] ' dır. En fazla üç giriş kabul eder ve [R betiği Yürüt][execute-r-script] modülüne benzer şekilde en fazla iki çıkış üretir. Python kodu, adlı özel olarak adlandırılmış bir giriş noktası işlevi aracılığıyla parametre kutusuna girilir `azureml_main` .

![Python betik modülünü Yürüt](./media/execute-python-scripts/execute-machine-learning-python-scripts-module.png)

![Modül parametre kutusunda örnek Python kodu](./media/execute-python-scripts/embedded-machine-learning-python-script.png)

### <a name="input-parameters"></a>Giriş parametreleri

Python modülüne yönelik girişler Pandas veri çerçeveleri olarak sunulur. `azureml_main`İşlevi, parametre olarak en fazla iki isteğe bağlı Pandas veri çerçevesini kabul eder.

Giriş bağlantı noktaları ve işlev parametreleri arasındaki eşleme konumsal:

- İlk bağlı giriş bağlantı noktası, işlevin ilk parametresine eşlenir.
- İkinci giriş (bağlıysa) işlevin ikinci parametresine eşlenir.
- Üçüncü giriş, [ek Python modüllerini içeri aktarmak](#import-modules)için kullanılır.

Giriş bağlantı noktalarının işlevin parametreleriyle nasıl eşlendiğine ilişkin daha ayrıntılı bir semantik anlamı `azureml_main` aşağıda verilmiştir.

![Giriş bağlantı noktası yapılandırmalarının ve sonuç Python imzasının tablosu](./media/execute-python-scripts/python-script-inputs-mapped-to-parameters.png)

### <a name="output-return-values"></a>Çıkış dönüş değerleri

`azureml_main`İşlevin demet, liste veya sayısal tuş takımı dizisi gibi bir Python [dizisinde](https://docs.python.org/2/c-api/sequence.html) paketlenmiş tek bir Pandas dataframe döndürmesi gerekir. Bu dizinin ilk öğesi, modülün ilk çıkış bağlantı noktasına döndürülür. Modülün ikinci çıkış bağlantı noktası [görselleştirmeler](#visualizations) için kullanılır ve bir dönüş değeri gerektirmez. Bu düzen aşağıda gösterilmiştir.

![Giriş bağlantı noktalarını parametrelere eşleme ve değeri çıkış bağlantı noktasına döndürme](./media/execute-python-scripts/map-of-python-script-inputs-outputs.png)

## <a name="translation-of-input-and-output-data-types"></a>Giriş ve çıkış veri türlerinin çevirisi

Studio veri kümeleri, Panda Dataframe ile aynı değildir. Sonuç olarak, Studio 'da (klasik) giriş veri kümeleri Pandas DataFrame 'e dönüştürülür ve çıkış veri çerçeveleri, Studio (klasik) veri kümelerine geri dönüştürülür. Bu dönüştürme işlemi sırasında aşağıdaki Çeviriler de gerçekleştirilir:

 **Python veri türü** | **Studio çeviri yordamı** |
| --- | --- |
| Dizeler ve sayı değerleri| Şöyle çevrilmiş |
| Pandas ' NA ' | ' Eksik değer ' olarak çevrilmiş |
| Dizin vektörleri | Desteklenen |
| Dize olmayan sütun adları | `str`Sütun adlarında çağır |
| Yinelenen sütun adları | Sayısal sonek ekle: (1), (2), (3), vb.

**Python işlevindeki tüm giriş verisi çerçeveleri her zaman 0 ' dan fazla satır sayısına 1 64 bitlik bir sayısal dizine sahiptir*

## <a name="importing-existing-python-script-modules"></a><a id="import-modules"></a>Mevcut Python betik modüllerini içeri aktarma

Python 'u yürütmek için kullanılan arka uç, yaygın [olarak kullanılan](https://www.anaconda.com/distribution/)bir bilimsel Python dağıtımına dayanır. Bu, veri merkezli iş yüklerinde kullanılan en yaygın Python paketlerinin 200 ' e yakın bir sürümüne sahiptir. Studio (klasik) Şu anda dış kitaplıkları yüklemek ve yönetmek için PIP veya Conda gibi paket yönetim sistemlerinin kullanılmasını desteklememektedir.  Ek kitaplıklar ekleme gereksinimini fark ederseniz, kılavuz olarak aşağıdaki senaryoyu kullanın.

Yaygın kullanım örneği, mevcut Python betikleri Studio (klasik) denemeleri içine dahil değildir. [Execute Python betik][execute-python-script] modülü, üçüncü giriş bağlantı noktasında Python modüllerini içeren bir ZIP dosyasını kabul eder. Dosya, çalışma zamanında yürütme çerçevesi tarafından sıkıştırıldı ve içerik Python yorumlayıcı kitaplık yoluna eklenir. `azureml_main`Giriş noktası işlevi daha sonra bu modülleri doğrudan içeri aktarabilir. 

Örnek olarak, basit bir "Hello, World" işlevi içeren Hello.py dosyasını göz önünde bulundurun.

![Hello.py dosyasında Kullanıcı tanımlı işlev](./media/execute-python-scripts/figure4.png)

Sonra, Hello.py içeren bir dosya Hello.zip oluşturacağız:

![Kullanıcı tanımlı Python kodunu içeren zip dosyası](./media/execute-python-scripts/figure5.png)

Zip dosyasını bir veri kümesi olarak Studio 'ya (klasik) yükleyin. Daha sonra, aşağıdaki görüntüde gösterildiği gibi, Hello.zip dosyasında Python kodunu kullanarak Python kodu **yürütme komut dosyası** modülünün üçüncü giriş bağlantı noktasına ekleyerek bir deneme oluşturun ve çalıştırın.

![Bir Python betik modülüne giriş olarak Hello.zip örnek deneme](./media/execute-python-scripts/figure6a.png)

![Zip dosyası olarak karşıya yüklenen Kullanıcı tanımlı Python kodu](./media/execute-python-scripts/figure6b.png)

Modül çıktısı, ZIP dosyasının paketlenmemiş olduğunu ve işlevin çalıştırılmış olduğunu gösterir `print_hello` .

![Kullanıcı tanımlı işlevi gösteren modül çıkışı](./media/execute-python-scripts/figure7.png)

## <a name="accessing-azure-storage-blobs"></a>Azure depolama Bloblarına erişme

Aşağıdaki adımları kullanarak, bir Azure Blob depolama hesabında depolanan verilere erişebilirsiniz:

1. [Python Için Azure Blob depolama paketini](https://azuremlpackagesupport.blob.core.windows.net/python/azure.zip) yerel olarak indirin.
1. ZIP dosyasını veri kümesi olarak Studio (klasik) çalışma alanınıza yükleyin.
1. BlobService nesneniz ile oluşturma `protocol='http'`

```
from azure.storage.blob import BlockBlobService

# Create the BlockBlockService that is used to call the Blob service for the storage account
block_blob_service = BlockBlobService(account_name='account_name', account_key='account_key', protocol='http')
```

1. Depolama **yapılandırma** ayarınız Sekmesinizdeki **güvenli aktarımı** devre dışı bırakma

![Azure portal gereken güvenli aktarımı devre dışı bırakma](./media/execute-python-scripts/disable-secure-transfer-required.png)

## <a name="operationalizing-python-scripts"></a>Python betikleri kullanıma aldırma

Bir Puanlama denemesinde kullanılan herhangi bir [yürütme Python betik][execute-python-script] modülü, Web hizmeti olarak yayımlandığında çağrılır. Örneğin, aşağıdaki görüntüde tek bir Python ifadesini değerlendirmek için kodu içeren bir Puanlama denemesi gösterilmektedir.

![Web hizmeti için Studio çalışma alanı](./media/execute-python-scripts/figure3a.png)

![Python Pandas ifadesi](./media/execute-python-scripts/python-script-with-python-pandas.png)

Bu deneyden oluşturulan bir Web hizmeti aşağıdaki eylemleri gerçekleştirebilir:

1. Bir Python ifadesini girdi olarak alma (dize olarak)
1. Python ifadesini Python yorumlayıcıya gönder
1. Hem ifadeyi hem de değerlendirilen sonucu içeren bir tablo döndürür.

## <a name="working-with-visualizations"></a><a id="visualizations"></a>Görselleştirmelerle çalışma

MatplotLib kullanılarak oluşturulan çizimler [Python betiği yürütme][execute-python-script]tarafından döndürülebilir. Ancak, çizimler R kullanırken olduğu gibi görüntülere otomatik olarak yeniden yönlendirilmez. Bu nedenle, kullanıcının tüm çizimleri PNG dosyalarına açıkça kaydetmesi gerekir.

MatplotLib adresinden görüntü oluşturmak için aşağıdaki adımları uygulamanız gerekir:

1. Arka ucunu varsayılan QT tabanlı işleyiciden "AGG" olarak değiştirin.
1. Yeni bir şekil nesnesi oluşturun.
1. Ekseni alın ve içindeki tüm çizimleri oluşturun.
1. Şekli bir PNG dosyasına kaydedin.

Bu işlem, Pandas 'teki scatter_matrix işlevi kullanılarak dağılım çizim matrisi oluşturan aşağıdaki görüntülerde gösterilmiştir.

![MatplotLib rakamların görüntülere kaydedileceği kod](./media/execute-python-scripts/figure-v1-8.png)

![Şekilleri görüntülemek için Python betik modülünü yürütme sayfasında Görselleştir ' e tıklayın](./media/execute-python-scripts/figure-v2-9a.png)

![Python kodu kullanarak örnek bir deneme için çizimleri görselleştirme](./media/execute-python-scripts/figure-v2-9b.png)

Farklı görüntülere kaydederek birden çok şekil döndürmek mümkündür. Studio (klasik) çalışma zamanı tüm görüntüleri seçer ve görselleştirmeleri için birleştirir.

## <a name="advanced-examples"></a>Gelişmiş örnekler

Studio 'da yüklü Anaconda ortamı (klasik), sayısal tuş y, SciPy ve Scikits-öğren gibi ortak paketleri içerir. Bu paketler, makine öğrenimi ardışık düzeninde veri işleme için etkili bir şekilde kullanılabilir.

Örneğin, aşağıdaki deneme ve komut dosyası, bir veri kümesi için özellik önem puanlarını hesaplamak üzere Scikits-Learn için ensebölümlü öğrenenler kullanımını gösterir. Puanlar, denetimli özellik seçimini başka bir modele gönderilmeden önce gerçekleştirmek için kullanılabilir.

Önem puanlarını hesaplamak ve özellikleri puanlarını temel alarak sıralamak için kullanılan Python işlevi aşağıda verilmiştir:

![Özellikleri puanlara göre derecelendirmek için işlev](./media/execute-python-scripts/figure8.png)

Aşağıdaki deneme daha sonra, Azure Machine Learning Studio (klasik) içindeki "Pima Hint diabetes" veri kümesindeki özelliklerin önem puanlarını hesaplar ve döndürür:

![Python kullanarak Pima Hint diabetes veri kümesindeki özellikleri derecelendirmek için denemeler yapın](./media/execute-python-scripts/figure9a.png)

![Python betiği yürütme modülünün çıktısının görselleştirmesi](./media/execute-python-scripts/figure9b.png)

## <a name="limitations"></a>Sınırlamalar

[Execute Python betiği][execute-python-script] modülü şu anda aşağıdaki sınırlamalara sahiptir:

### <a name="sandboxed-execution"></a>Korumalı yürütme

Python çalışma zamanı şu anda korumalı değildir ve ağa veya yerel dosya sistemine kalıcı bir biçimde erişime izin vermez. Yerel olarak kaydedilen tüm dosyalar, modül bittikten sonra yalıtılmıştır ve silinir. Python kodu, üzerinde çalıştığı makinedeki birçok dizine erişemez, özel durum geçerli dizin ve alt dizinlerindedir.

### <a name="lack-of-sophisticated-development-and-debugging-support"></a>Gelişmiş geliştirme ve hata ayıklama desteğinin olmaması

Python modülü şu anda IntelliSense ve hata ayıklama gibi IDE özelliklerini desteklemiyor. Ayrıca, modül çalışma zamanında başarısız olursa, tam Python yığın izlemesi kullanılabilir. Ancak modülün çıktı günlüğünde görüntülenmelidir. Şu anda, IPython gibi bir ortamda Python betikleri geliştirmenize ve hata ayıklamanızı ve ardından kodu modüle içeri aktarmanızı öneririz.

### <a name="single-data-frame-output"></a>Tek veri çerçevesi çıkışı

Python giriş noktasının yalnızca çıkış olarak tek bir veri çerçevesi döndürmesine izin verilir. Şu anda doğrudan Stüdyo (klasik) çalışma zamanına eğitilen modeller gibi rastgele Python nesneleri döndürmek mümkün değildir. Aynı sınırlamaya sahip olan [R betiği Yürüt][execute-r-script]gibi, nesnelerin bir bayt dizisine pickve sonra da bir veri çerçevesinin içinde döndürülmesi olasıdır.

### <a name="inability-to-customize-python-installation"></a>Python yüklemesini özelleştirememe

Şu anda, özel Python modülleri eklemenin tek yolu daha önce açıklanan ZIP dosya mekanizmasına sahiptir. Bu küçük modüller için uygun olmakla birlikte, büyük modüller (özellikle yerel dll 'Ler içeren modüller) veya çok sayıda modülle çok daha fazla olabilir.

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla bilgi için bkz. [Python Geliştirici Merkezi](https://azure.microsoft.com/develop/python/).

<!-- Module References -->
[execute-python-script]: /azure/machine-learning/studio-module-reference/execute-python-script
[execute-r-script]: /azure/machine-learning/studio-module-reference/execute-r-script