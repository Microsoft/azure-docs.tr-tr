---
title: 'Öğretici: makine öğrenimini oluşturmak için Azure Işlevlerinde Python ve TensorFlow kullanın | Microsoft Docs'
description: Bu öğreticide, Azure Işlevleri 'nde TensorFlow makine öğrenimi modellerinin nasıl uygulanacağı gösterilmektedir
services: functions
author: anthonychu
manager: gwallace
ms.service: azure-functions
ms.devlang: python
ms.topic: tutorial
ms.date: 07/29/2019
ms.author: antchu
ms.custom: mvc
ms.openlocfilehash: 491adb2719dc7c05a2943634e83027376c9327c3
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74082739"
---
# <a name="tutorial-apply-machine-learning-models-in-azure-functions-with-python-and-tensorflow"></a>Öğretici: Python ve TensorFlow ile Azure Işlevleri 'nde makine öğrenimi modellerini uygulama

Bu makalede, Azure Işlevlerinin, bir görüntüyü içeriğine göre sınıflandırmak için bir makine öğrenimi modeliyle Python ve TensorFlow 'u nasıl kullanabileceğinizi gösterir.

Bu öğreticide şunların nasıl yapıldığını öğrenirsiniz: 

> [!div class="checklist"]
> * Python 'da Azure Işlevleri geliştirmek için yerel bir ortam başlatma
> * Özel bir TensorFlow makine öğrenimi modelini bir işlev uygulamasına aktarma
> * Fotoğrafın bir köpek veya kedi içerip içermediğini tahmin etmek için sunucusuz bir HTTP API 'SI oluşturun
> * API 'YI bir Web uygulamasından tüketme

![Tamamlanmış projenin ekran görüntüsü](media/functions-machine-learning-tensorflow/functions-machine-learning-tensorflow-screenshot.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Önkoşullar 

Python 'da Azure Işlevleri oluşturmak için birkaç araç yüklemeniz gerekir.

- [Python 3,6](https://www.python.org/downloads/release/python-360/)
- [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools)
- [Visual Studio Code](https://code.visualstudio.com/) gibi bir kod Düzenleyicisi

## <a name="clone-the-tutorial-repository"></a>Öğretici deposunu Kopyala

Başlamak için bir Terminal açın ve git kullanarak aşağıdaki depoyu kopyalayın:

```console
git clone https://github.com/Azure-Samples/functions-python-tensorflow-tutorial.git
cd functions-python-tensorflow-tutorial
```

Depo birkaç klasör içerir.

- *Başlangıç*: Bu öğretici için çalışma klasörünüzdir
- *son*: Bu, başvurağınız için nihai sonuç ve tam uygulama
- *kaynaklar*: Machine Learning modelini ve yardımcı kitaplıklarını içerir
- *ön uç*: işlev uygulamasını çağıran bir Web sitesi

## <a name="create-and-activate-a-python-virtual-environment"></a>Python sanal ortamı oluşturma ve etkinleştirme

Azure Işlevleri Python 3.6. x gerektirir. Gerekli Python sürümünü kullandığınızdan emin olmak için bir sanal ortam oluşturacaksınız.

Geçerli çalışma dizinini *Başlangıç* klasörüyle değiştirin. Ardından *. venv*adlı bir sanal ortam oluşturun ve etkinleştirin. Python yüklemenize bağlı olarak, Python 3,6 sanal ortamı oluşturmaya yönelik komutlar aşağıdaki yönergelerden farklı olabilir.

#### <a name="linux-and-macos"></a>Linux ve macOS:

```bash
cd start
python3.6 -m venv .venv
source .venv/bin/activate
```

#### <a name="windows"></a>Windows:

```powershell
cd start
py -3.6 -m venv .venv
.venv\scripts\activate
```

Terminal istemi artık sanal ortamı başarıyla etkinleştirmiş olduğunu belirten `(.venv)` ön ekine sahiptir. Sanal ortamdaki `python` gerçekten Python 3.6. x olduğunu doğrulayın.

```console
python --version
```

> [!NOTE]
> Öğreticinin geri kalanı için, komutları sanal ortamda çalıştırırsınız. Bir terminalde sanal ortamı yeniden etkinleştirmeniz gerekiyorsa, işletim sisteminiz için uygun etkinleştirme komutunu yürütün.

## <a name="create-an-azure-functions-project"></a>Azure İşlevleri projesi oluşturma

*Başlangıç* klasöründe, bir Python işlev uygulamasını başlatmak için Azure Functions Core Tools kullanın.

```console
func init --worker-runtime python
```

Bir işlev uygulaması, bir veya daha fazla Azure Işlevi içerebilir. Bir düzenleyicide *Başlangıç* klasörünü açın ve içeriğini inceleyin.

- [*Local. Settings. JSON*](functions-run-local.md#local-settings-file): yerel geliştirme için kullanılan uygulama ayarlarını içerir
- [*Host. JSON*](functions-host-json.md): Azure işlevleri Konağı ve uzantıları için ayarları içerir
- [*requirements. txt*](functions-reference-python.md#package-management): Bu uygulama Için gereken Python paketlerini içerir

## <a name="create-an-http-function"></a>HTTP işlevi oluşturma

Uygulama, giriş olarak bir görüntü URL 'SI alan ve görüntünün bir köpek veya kedi içerip içermediğini tahmin eden tek bir HTTP API uç noktası gerektirir.

Terminalde Azure Functions Core Tools, *Sınıflandırma*adlı yenı bir http işlevini kullanmak için kullanın.

```console
func new --language python --template HttpTrigger --name classify
```

İki dosya içeren *Sınıflandırma* adlı yeni bir klasör oluşturulur.

- *\_\_init\_\_. Kopyala*: Main işlevi için bir dosya
- *function. JSON*: işlevin tetikleyicisini ve giriş ve çıkış bağlamalarını açıklayan bir dosya

### <a name="run-the-function"></a>İşlevi çalıştırın

Python sanal ortamının etkinleştirildiği terminalde, işlev uygulamasını başlatın.

```console
func start
```

Bir tarayıcı açın ve aşağıdaki URL 'ye gidin. İşlev, *Merhaba Azure* 'un yürütülmesi ve döndürülmesi gerekir!

```
http://localhost:7071/api/classify?name=Azure
```

İşlev uygulamasını durdurmak için `Ctrl-C` kullanın.

## <a name="import-the-tensorflow-model"></a>TensorFlow modelini içeri aktarma

Azure Özel Görüntü İşleme Hizmeti ile eğitilen ve verilen önceden oluşturulmuş bir TensorFlow modeli kullanacaksınız.

> [!NOTE]
> Özel Görüntü İşleme Hizmeti ücretsiz katmanını kullanarak kendinizinkini derlemek isterseniz, [örnek proje deposundaki yönergeleri](https://github.com/Azure-Samples/functions-python-tensorflow-tutorial/blob/master/train-custom-vision-model.md)izleyebilirsiniz.

Model, *< REPOSITORY_ROOT >/Resources/model* klasöründe bulunan iki dosyadan oluşur: *model. PB* ve *labels. txt*. Onları *sınıflandır* işlevinin klasörüne kopyalayın.

#### <a name="linux-and-macos"></a>Linux ve macOS:

```bash
cp ../resources/model/* classify
```

#### <a name="windows"></a>Windows:

```powershell
copy ..\resources\model\* classify
```

Yukarıdaki komutuna \* eklediğinizden emin olun. *Sınıflandırın* şimdi *model. PB* ve *labels. txt*adlı dosyaları içerdiğini doğrulayın.

## <a name="add-the-helper-functions-and-dependencies"></a>Yardımcı işlevleri ve bağımlılıkları ekleme

Giriş görüntüsünü hazırlamaya ve TensorFlow kullanarak tahmin yapmaya yönelik bazı yardımcı işlevler, *Resources* klasöründeki *Predict.py* adlı bir dosyadır. Bu dosyayı *sınıflandır* işlevinin klasörüne kopyalayın.

#### <a name="linux-and-macos"></a>Linux ve macOS:

```bash
cp ../resources/predict.py classify
```

#### <a name="windows"></a>Windows:

```powershell
copy ..\resources\predict.py classify
```

*Sınıflandırın* şimdi *Predict.py*adlı bir dosya içerdiğini doğrulayın.

### <a name="install-dependencies"></a>Bağımlılıkları yükleme

Yardımcı kitaplığı, yüklenmesi gereken bazı bağımlılıklara sahiptir. Düzenleyicinizde *Start/requirements. txt* dosyasını açın ve aşağıdaki bağımlılıkları dosyaya ekleyin.

```txt
tensorflow==1.14
Pillow
requests
```

Dosyayı kaydedin.

Sanal ortamı etkinleştirilen terminalde, bağımlılıkları yüklemek için *Başlangıç* klasöründe aşağıdaki komutu çalıştırın. Bazı yükleme adımlarının tamamlanması birkaç dakika sürebilir.

```console
pip install --no-cache-dir -r requirements.txt
```

### <a name="caching-the-model-in-global-variables"></a>Modeli genel değişkenlerde önbelleğe alma

Düzenleyicide *Predict.py* ' ı açın ve dosyanın en üstündeki `_initialize` işlevine bakın. İlk kez çalıştırıldığında ve genel değişkenlere kaydedildiğinde, TensorFlow modelinin diskten yüklendiğine dikkat edin. Diskten yükleme `_initialize` işlevinin sonraki yürütmeleri sırasında atlanır. Modeli bu teknikle bellekte önbelleğe almak, daha sonraki tahminlere hızlanır.

Genel değişkenler hakkında daha fazla bilgi için [Azure Işlevleri Python Geliştirici Kılavuzu](functions-reference-python.md#global-variables)' na bakın.

## <a name="update-function-to-run-predictions"></a>Tahminleri çalıştırmak için işlevi Güncelleştir

Düzenleyicinizde *Sınıflandırma/\_\_init\_\_. Kopyala* ' yı açın. Daha önce eklediğiniz *tahmin* kitaplığını aynı klasöre aktarın. Aşağıdaki `import` deyimlerini, dosyasında zaten bulunan diğer içeri aktarmaları altına ekleyin.

```python
import json
from .predict import predict_image_from_url
```

İşlev şablonu kodunu aşağıdaki kodla değiştirin.

```python
def main(req: func.HttpRequest) -> func.HttpResponse:
    image_url = req.params.get('img')
    results = predict_image_from_url(image_url)

    headers = {
        "Content-type": "application/json",
        "Access-Control-Allow-Origin": "*"
    }
    return func.HttpResponse(json.dumps(results), headers = headers)
```

Değişikliklerinizi kaydettiğinizden emin olun.

Bu işlev, `img`adlı bir sorgu dizesi parametresinde bir görüntü URL 'SI alır. Görüntüyü indirir ve TensorFlow modelini kullanarak bir tahmin döndüren yardımcı kitaplığından `predict_image_from_url` çağırır. İşlev daha sonra sonuçlarıyla bir HTTP yanıtı döndürür.

HTTP uç noktası, başka bir etki alanında barındırılan bir Web sayfası tarafından çağrıldığından, HTTP yanıtı tarayıcının çıkış noktaları arası kaynak paylaşımı (CORS) gereksinimlerini karşılamak için bir `Access-Control-Allow-Origin` üst bilgisi içerir.

> [!NOTE]
> Bir üretim uygulamasında, ek güvenlik için `*` Web sayfasının belirli bir kaynağına değiştirin.

### <a name="run-the-function-app"></a>İşlev uygulamasını çalıştırma

Python sanal ortamının hala etkinleştirildiğinden emin olun ve aşağıdaki komutu kullanarak işlev uygulamasını başlatın.

```console
func start
```

Bir tarayıcıda, bir kedi fotoğrafı URL 'SI ile işlevinizi çağıran bu URL 'YI açın. Geçerli bir tahmin sonucu döndürüldüğünden emin olun.

```
http://localhost:7071/api/classify?img=https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/cat1.png
```

İşlev uygulamasını çalışır durumda tutun.

### <a name="run-the-web-app"></a>Web uygulamasını çalıştırma

*Ön uç* klasöründe, Işlev UYGULAMASıNDAKI HTTP API 'sini tüketen basit bir Web uygulaması vardır.

*Ayrı* bir Terminal açın ve *ön uç* klasörüne geçin. Python 3,6 ile bir HTTP sunucusu başlatın.

#### <a name="linux-and-macos"></a>Linux ve macOS:

```bash
cd <FRONT_END_FOLDER>
python3.6 -m http.server
```

#### <a name="windows"></a>Windows:

```powershell
cd <FRONT_END_FOLDER>
py -3.6  -m http.server
```

Bir tarayıcıda, terminalde görüntülenen HTTP sunucusunun URL 'sine gidin. Bir Web uygulaması görünmelidir. Metin kutusuna aşağıdaki fotoğraf URL 'Lerinden birini girin. Ayrıca, herkese açık bir kedi veya köpek fotoğrafının URL 'sini de kullanabilirsiniz.

- `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/cat1.png`
- `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/cat2.png`
- `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/dog1.png`
- `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/dog2.png`

Gönder ' e tıkladığınızda, işlev uygulaması çağrılır ve sayfada bir sonuç görüntülenir.

## <a name="clean-up-resources"></a>Kaynakları temizleme
Bu öğreticinin tamamı makinenizde yerel olarak çalışarak temizleyebilmek için hiçbir Azure kaynağı veya hizmeti yoktur.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, bir TensorFlow modeli kullanarak tahmine dayalı hale getirmek için Azure Işlevleri ile bir HTTP API 'SI oluşturup özelleştirmeyi öğrendiniz. Ayrıca, API 'nin bir Web uygulamasından nasıl çağrılacağını öğrenirsiniz.

Azure Işlevleri tarafından sunulan sunucusuz işlem modelinde çalışırken her türlü karmaşıklığın API 'Lerini oluşturmak için bu öğreticideki teknikleri kullanabilirsiniz.

İşlev uygulamasını Azure 'a dağıtmak için [Azure Functions Core Tools](./functions-run-local.md#publish) veya [Visual Studio Code](https://code.visualstudio.com/docs/python/tutorial-azure-functions)kullanın.

> [!div class="nextstepaction"]
> [Azure Işlevleri Python Geliştirici Kılavuzu](./functions-reference-python.md)
