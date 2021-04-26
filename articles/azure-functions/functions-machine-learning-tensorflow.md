---
title: Azure 'da Machine Learning için Python ve TensorFlow kullanma
description: Bir görüntüyü içeriğine göre sınıflandırmak için bir makine öğrenimi modeliyle Python, TensorFlow ve Azure Işlevleri kullanın.
author: anthonychu
ms.topic: tutorial
ms.date: 01/15/2020
ms.author: antchu
ms.custom: mvc, devx-track-python, devx-track-azurepowershell
ms.openlocfilehash: ca9ce27583168dfee1a597fce559afad38a3a8c7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95994613"
---
# <a name="tutorial-apply-machine-learning-models-in-azure-functions-with-python-and-tensorflow"></a>Öğretici: Python ve TensorFlow ile Azure Işlevleri 'nde makine öğrenimi modellerini uygulama

Bu makalede, bir görüntüyü içeriğine göre sınıflandırmak için Python, TensorFlow ve Azure Işlevlerini bir makine öğrenimi modeliyle nasıl kullanacağınızı öğreneceksiniz. Tüm işleri yerel olarak yaptığınız ve bulutta hiçbir Azure kaynağı olmadığınızdan, bu öğreticiyi tamamlamaya yönelik bir maliyet yoktur.

> [!div class="checklist"]
> * Python 'da Azure Işlevleri geliştirmek için yerel bir ortam başlatın.
> * Özel bir TensorFlow makine öğrenimi modelini bir işlev uygulamasına aktarın.
> * Bir köpek veya kedi içeren bir görüntüyü sınıflandırmak için sunucusuz bir HTTP API 'SI oluşturun.
> * Web uygulamasından API’yi kullanma.

## <a name="prerequisites"></a>Önkoşullar 

- Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Python 3.7.4](https://www.python.org/downloads/release/python-374/). (Python 3.7.4 ve Python 3.6. x, Azure Işlevleri ile doğrulanır; Python 3,8 ve sonraki sürümler henüz desteklenmiyor.)
- [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools)
- [Visual Studio Code](https://code.visualstudio.com/) gibi bir kod Düzenleyicisi

### <a name="prerequisite-check"></a>Önkoşul denetimi

1. Bir Terminal veya komut penceresinde, `func --version` Azure Functions Core Tools sürüm 2.7.1846 veya üzeri olduğunu denetlemek için komutunu çalıştırın.
1. `python --version` `py --version` Python sürüm raporlarınızı 3.7. x olarak denetlemek için (Linux/MacOS) veya (Windows) öğesini çalıştırın.

## <a name="clone-the-tutorial-repository"></a>Öğretici deposunu Kopyala

1. Bir Terminal veya komut penceresinde, git kullanarak aşağıdaki depoyu kopyalayın:

    ```
    git clone https://github.com/Azure-Samples/functions-python-tensorflow-tutorial.git
    ```

1. Klasöre gidin ve içeriğini inceleyin.

    ```
    cd functions-python-tensorflow-tutorial
    ```

    - *Başlangıç* , öğreticinin çalışma klasörüdür.
    - *End* , başvurağınız için nihai sonuç ve tam uygulama.
    - *kaynaklar* makine öğrenimi modelini ve yardımcı kitaplıklarını içerir.
    - *ön uç* , işlev uygulamasını çağıran bir Web sitesidir.
    
## <a name="create-and-activate-a-python-virtual-environment"></a>Python sanal ortamı oluşturma ve etkinleştirme

*Başlangıç* klasörüne gidin ve adlı bir sanal ortam oluşturmak ve etkinleştirmek için aşağıdaki komutları çalıştırın `.venv` . Azure Işlevleri tarafından desteklenen Python 3,7 ' i kullandığınızdan emin olun.


# <a name="bash"></a>[Bash](#tab/bash)

```bash
cd start
```

```bash
python -m venv .venv
```

```bash
source .venv/bin/activate
```

Python, venv paketini Linux dağıtımına yüklememediyse aşağıdaki komutu çalıştırın:

```bash
sudo apt-get install python3-venv
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
cd start
```

```powershell
py -3.7 -m venv .venv
```

```powershell
.venv\scripts\activate
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
cd start
```

```cmd
py -3.7 -m venv .venv
```

```cmd
.venv\scripts\activate
```

---

Bu etkinleştirilmiş sanal ortamda sonraki tüm komutları çalıştırırsınız. (Sanal ortamdan çıkmak için, öğesini çalıştırın `deactivate` .)


## <a name="create-a-local-functions-project"></a>Yerel işlevler projesi oluşturma

Azure Işlevlerinde bir işlev projesi, her birinin belirli bir tetikleyiciye yanıt verdiği bir veya daha fazla bağımsız işlev için bir kapsayıcıdır. Projedeki tüm işlevler aynı yerel ve barındırma yapılandırmalarına sahiptir. Bu bölümde, `classify` http uç noktası sağlayan adlı tek bir ortak işlevi içeren bir işlev projesi oluşturacaksınız. Daha sonraki bir bölüme daha özel kod eklersiniz.

1. *Başlangıç* klasöründe, bir Python işlev uygulamasını başlatmak için Azure Functions Core Tools kullanın:

    ```
    func init --worker-runtime python
    ```

    Başlatma işleminden sonra *Başlangıç* klasörü, [local.settings.js](functions-run-local.md#local-settings-file) ve [host.js](functions-host-json.md)adlı yapılandırma dosyaları dahil olmak üzere proje için çeşitli dosyalar içerir. *local.settings.json* , Azure 'dan indirilen gizli dizileri içerebildiğinden, dosya, *. gitignore* dosyasında varsayılan olarak kaynak denetiminden çıkarılır.

    > [!TIP]
    > Bir işlev projesi belirli bir çalışma zamanına bağlı olduğundan, projedeki tüm işlevlerin aynı dille yazılması gerekir.

1. Aşağıdaki komutu kullanarak projenize bir işlev ekleyin; burada `--name` bağımsız değişken işlevinizin benzersiz adıdır ve `--template` bağımsız değişken işlevin tetikleyicisini belirtir. `func new` Projenin seçtiği dile uygun bir kod dosyası ve *üzerindefunction.js* adlı bir yapılandırma dosyası içeren işlev adıyla eşleşen bir alt klasör oluşturun.

    ```
    func new --name classify --template "HTTP trigger"
    ```

    Bu komut, işlevin adıyla eşleşen bir klasör oluşturur, *sınıflandır*. Bu klasörde iki dosya vardır: işlev kodunu içeren *\_ \_ init \_ \_ .* ger ve işlevin tetikleyicisini ve giriş ve çıkış bağlamalarını açıklayan *function.js*. Bu dosyaların içeriğiyle ilgili ayrıntılar için bkz. Python hızlı başlangıçta [Dosya Içeriğini İnceleme](./create-first-function-cli-python.md#optional-examine-the-file-contents) .


## <a name="run-the-function-locally"></a>İşlevi yerel olarak çalıştırma

1. *Başlangıç* klasöründeki yerel Azure işlevleri çalışma zamanı konağını başlatarak işlevi başlatın:

    ```
    func start
    ```
    
1. `classify`Uç noktanın çıktıda göründüğünü gördüğünüzde URL 'ye gidin ```http://localhost:7071/api/classify?name=Azure``` . "Merhaba Azure!" iletisi çıktıda görünmelidir.

1.  - Konağı durdurmak için CTRL **C** 'yi kullanın.


## <a name="import-the-tensorflow-model-and-add-helper-code"></a>TensorFlow modelini içeri aktarma ve yardımcı kodu ekleme

`classify`Bir görüntüyü içeriğine göre sınıflandırmak için işlevi değiştirmek üzere, Azure özel görüntü işleme hizmeti ile eğitilen ve verilen önceden oluşturulmuş bir TensorFlow modeli kullanın. Daha önce Klonladığınız örneğin *Resources* klasöründe yer alan model, bir köpek veya kedi içerip içermediğini temel alarak bir görüntüyü sınıflandırır. Daha sonra projenize bazı yardımcı kod ve bağımlılıklar eklersiniz.

Özel Görüntü İşleme Hizmeti ücretsiz katmanını kullanarak kendi modelinizi derlemek için, [örnek proje deposundaki](https://github.com/Azure-Samples/functions-python-tensorflow-tutorial/blob/master/train-custom-vision-model.md)yönergeleri izleyin.

> [!TIP]
> TensorFlow modelinizi işlev uygulamasından bağımsız olarak barındırmak istiyorsanız, bunun yerine, bunu Linux işlev uygulamanıza modelinizi içeren bir dosya paylaşımından bağlayabilirsiniz. Daha fazla bilgi edinmek için bkz. [Azure CLI kullanarak bir Python işlev uygulamasına bir dosya paylaşma bağlama](./scripts/functions-cli-mount-files-storage-linux.md).

1. *Başlangıç* klasöründe, model dosyalarını *sınıflandır* klasörüne kopyalamak için aşağıdaki komutu çalıştırın. Komutuna dahil ettiğinizden emin olun `\*` . 

    # <a name="bash"></a>[Bash](#tab/bash)
    
    ```bash
    cp ../resources/model/* classify
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    copy ..\resources\model\* classify
    ```
    
    # <a name="cmd"></a>[Cmd](#tab/cmd)
    
    ```cmd
    copy ..\resources\model\* classify
    ```
    
    ---
    
1. *Sınıflandır* klasörünün *model. PB* ve *labels.txt* adlı dosyaları içerdiğini doğrulayın. Aksi takdirde, komutu *Başlangıç* klasöründe çalıştırmanızdan emin olun.

1. *Başlangıç* klasöründe, yardımcı kod içeren bir dosyayı *sınıflandır* klasörüne kopyalamak için aşağıdaki komutu çalıştırın:

    # <a name="bash"></a>[Bash](#tab/bash)
    
    ```bash
    cp ../resources/predict.py classify
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    copy ..\resources\predict.py classify
    ```
    
    # <a name="cmd"></a>[Cmd](#tab/cmd)
    
    ```cmd
    copy ..\resources\predict.py classify
    ```
    
    ---

1. *Sınıflandır* klasörünün artık *Predict.py* adlı bir dosya içerdiğini doğrulayın.

1. Bir metin düzenleyicisinde *Başlat/requirements.txt* açın ve yardımcı kodu için gereken aşağıdaki bağımlılıkları ekleyin:

    ```txt
    tensorflow==1.14
    Pillow
    requests
    ```
    
1. *requirements.txt* kaydedin.

1. *Başlangıç* klasöründe aşağıdaki komutu çalıştırarak bağımlılıkları yükler. Yükleme işlemi birkaç dakika sürebilir. bu sırada, sonraki bölümde işlevini değiştirmeye devam edebilirsiniz.

    ```
    pip install --no-cache-dir -r requirements.txt
    ```
    
    Windows 'da, "bir EnvironmentError nedeniyle paketler yüklenemedi: [errno 2] böyle bir dosya veya dizin yok:" arkasından *sharded_mutable_dense_hashtable. CPython-37. PYC* gibi bir dosyaya uzun bir yol adı gelmelidir. Genellikle, bu hata, klasör yolunun derinliği çok uzun hale geldiği için oluşur. Bu durumda, `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\FileSystem@LongPathsEnabled` `1` uzun yolları etkinleştirmek için kayıt defteri anahtarını olarak ayarlayın. Alternatif olarak, Python yorumlayıcının nereye yüklendiğini kontrol edin. Bu konumda uzun bir yol varsa, daha kısa bir yola sahip bir klasöre yeniden yüklemeyi deneyin.

> [!TIP]
> İlk tahminini yapmak için *Predict.py* üzerine çağrılırken, adlı bir işlev, bir Işlev `_initialize` olan TensorFlow modelini diskten yükler ve genel değişkenlerde önbelleğe alır. Bu önbelleğe alma, sonraki tahminleri hızlandırır. Genel değişkenleri kullanma hakkında daha fazla bilgi için, [Azure Işlevleri Python Geliştirici Kılavuzu](functions-reference-python.md#global-variables)' na bakın.

## <a name="update-the-function-to-run-predictions"></a>Tahminleri çalıştırmak için işlevi güncelleştirin

1. *Sınıflandırma/ \_ \_ init \_ \_ .* düzenleyene BIR metin düzenleyicisinde açın ve `import` Standart JSON kitaplığını ve *tahmin* eden yardımcıları içeri aktarmak için mevcut deyimlerden sonra aşağıdaki satırları ekleyin:

    :::code language="python" source="~/functions-python-tensorflow-tutorial/end/classify/__init__.py" range="1-6" highlight="5-6":::

1. İşlevin tüm içeriğini `main` aşağıdaki kodla değiştirin:

    :::code language="python" source="~/functions-python-tensorflow-tutorial/end/classify/__init__.py" range="8-19":::

    Bu işlev, adlı bir sorgu dizesi parametresinde bir görüntü URL 'SI alır `img` . Daha sonra `predict_image_from_url` , TensorFlow modelini kullanarak görüntüyü indirmek ve sınıflandırmak için yardımcı kitaplıktan çağrı yapılır. İşlev daha sonra sonuçlarıyla bir HTTP yanıtı döndürür. 

    > [!IMPORTANT]
    > Bu HTTP uç noktası, başka bir etki alanında barındırılan bir Web sayfası tarafından çağrıldığından, yanıt, `Access-Control-Allow-Origin` tarayıcının çıkış noktaları arası kaynak paylaşımı (CORS) gereksinimlerini karşılamak için bir üst bilgi içerir.
    >
    > Bir üretim uygulamasında, `*` ek güvenlik için Web sayfasının belirli bir kaynağına geçiş yapın.

1. Yaptığınız değişiklikleri kaydedin, sonra bağımlılıkların yükleme tamamlandığını varsayarak, yerel işlev konağını ile yeniden başlatın `func start` . Konağı, sanal ortamın etkinleştirildiği *Başlangıç* klasöründe çalıştırdığınızdan emin olun. Aksi takdirde konak başlar, ancak işlevi çağırırken hata görürsünüz.

    ```
    func start
    ```

1. Bir tarayıcıda, bir Cat görüntüsünün URL 'siyle işlevi çağırmak için aşağıdaki URL 'YI açın ve döndürülen JSON 'ın görüntüyü bir kedi olarak sınıflandırdığından emin olun.

    ```
    http://localhost:7071/api/classify?img=https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/cat1.png
    ```
    
1. Bir sonraki adımda kullandığınız için Konağı çalışır durumda tutun. 

### <a name="run-the-local-web-app-front-end-to-test-the-function"></a>İşlevi test etmek için yerel Web uygulaması ön ucu 'nı çalıştırın

İşlev uç noktasının başka bir Web uygulamasından çağrılması test etmek için deponun *ön uç* klasöründe basit bir uygulama vardır.

1. Yeni bir Terminal veya komut istemi açın ve sanal ortamı etkinleştirin (daha önce [bir Python sanal ortamı oluşturma ve etkinleştirme](#create-and-activate-a-python-virtual-environment)altında açıklandığı gibi).

1. Deponun *ön uç* klasörüne gidin.

1. Python ile HTTP sunucusu başlatma:

    # <a name="bash"></a>[Bash](#tab/bash)

    ```bash 
    python -m http.server
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```powershell
    py -m http.server
    ```

    # <a name="cmd"></a>[Cmd](#tab/cmd)

    ```cmd
    py -m http.server
    ```

1. Bir tarayıcıda öğesine gidin ve `localhost:8000` ardından metin kutusuna aşağıdaki fotoğraf URL 'lerinden birini girin veya herkese açık olarak erişilebilen herhangi bir görüntünün URL 'sini kullanın.

    - `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/cat1.png`
    - `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/cat2.png`
    - `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/dog1.png`
    - `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/dog2.png`
    
1. Görüntüyü sınıflandırmak için işlev uç noktasını çağırmak üzere **Gönder** ' i seçin.

    ![Tamamlanmış projenin ekran görüntüsü](media/functions-machine-learning-tensorflow/functions-machine-learning-tensorflow-screenshot.png)

    Görüntü URL 'sini gönderdiğinizde tarayıcıda bir hata bildirirse, işlev uygulamasını çalıştırmakta olduğunuz terminali kontrol edin. "Modül bulunamadı" PIL ' "gibi bir hata görürseniz, önce daha önce oluşturduğunuz sanal ortamı etkinleştirmeden önce, işlev uygulamasını *Başlangıç* klasöründe başlatmış olabilirsiniz. Hala hata görüyorsanız, `pip install -r requirements.txt` sanal ortam etkinleştirildikten sonra yeniden çalıştırın ve hata olup olmadığına bakın.

> [!NOTE]
> Model her zaman görüntünün içeriğini bir kedi veya bir köpek olarak sınıflandırdığından, resmin her ikisi de içerip içermediğini belirtir. Örneğin, genellikle kedi olarak sınıflandırır ve kyaların görüntüleri, ancak elekülebilirlik, carrots ya da uçak gibi sınıflandırmacılar görüntüleri.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu öğreticinin tamamı makinenizde yerel olarak çalıştığından, temizleyebilen hiçbir Azure kaynağı veya hizmeti yoktur.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, bir TensorFlow modeli kullanarak görüntüleri sınıflandırmak için Azure Işlevleri ile bir HTTP API uç noktası oluşturmayı ve özelleştirmeyi öğrendiniz. Ayrıca, API 'nin bir Web uygulamasından nasıl çağrılacağını öğrenirsiniz. Azure Işlevleri tarafından sunulan sunucusuz işlem modelinde çalışırken her türlü karmaşıklığın API 'Lerini oluşturmak için bu öğreticideki teknikleri kullanabilirsiniz.

> [!div class="nextstepaction"]
> [Azure CLı kılavuzunu kullanarak işlevi Azure Işlevleri 'ne dağıtma](./functions-run-local.md#publish)

Ayrıca bkz:

- [Visual Studio Code kullanarak Işlevi Azure 'A dağıtın](https://code.visualstudio.com/docs/python/tutorial-azure-functions).
- [Azure Işlevleri Python Geliştirici Kılavuzu](./functions-reference-python.md)
- [Azure CLı kullanarak bir Python işlev uygulamasına bir dosya paylaşma bağlama](./scripts/functions-cli-mount-files-storage-linux.md)
