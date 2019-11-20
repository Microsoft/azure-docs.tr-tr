---
title: "Öğretici: metni çevirmek, eklemek ve analiz etmek için bir Flask uygulaması oluşturun-Translator Metin Çevirisi API'si"
titleSuffix: Azure Cognitive Services
description: Bu öğreticide, metin çevirmek, yaklaşımı çözümlemek ve çevrilmiş metni konuşmaya dönüştürmek için Flask tabanlı bir Web uygulaması oluşturacaksınız.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: tutorial
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: 7b9f1cfeb6ebcbc693135d83ad167092e16f478d
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73647324"
---
# <a name="tutorial-build-a-flask-app-with-azure-cognitive-services"></a>Öğretici: Azure bilişsel hizmetler ile bir Flask uygulaması derleme

Bu öğreticide, Azure bilişsel hizmetler 'i kullanarak metin çevirmek, yaklaşımı çözümlemek ve çevrilmiş metni konuşmaya dönüştürmek için bir Flask Web uygulaması oluşturacaksınız. Sistemimiz, uygulamamızı etkinleştiren Python kodu ve Flask rotaları üzerinde yer alır; ancak, uygulamayı birlikte çeken HTML ve JavaScript ile size yardımcı olacaktır. Herhangi bir sorunla karşılaşırsanız aşağıdaki geri bildirim düğmesini kullanarak bize bilgi verin.

Bu öğreticinin şu şekilde ele alınmaktadır:

> [!div class="checklist"]
> * Azure abonelik anahtarları 'nı al
> * Geliştirme ortamınızı ayarlama ve bağımlılıkları kurma
> * Flask uygulaması oluşturma
> * Metni çevirmek için Translator Metin Çevirisi API'si kullanma
> * Giriş metninin ve çevirilerin olumlu/olumsuz yaklaşımını çözümlemek için Metin Analizi kullanın
> * Çevrilmiş metni sentezleştirilmiş konuşmaya dönüştürmek için konuşma hizmetlerini kullanma
> * Flask uygulamanızı yerel olarak çalıştırma

> [!TIP]
> Daha sonra atlamak ve tüm kodu tek seferde görmek isterseniz, tüm örnek, derleme yönergeleriyle birlikte [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Flask-App-Tutorial)'da bulunabilir.

## <a name="what-is-flask"></a>Flask nedir?

Flask, Web uygulamaları oluşturmak için bir mikro çerçevedir. Bu, Flask 'nın Size bir Web uygulaması oluşturmanıza izin veren araçları, kitaplıkları ve teknolojileri sağladığı anlamına gelir. Bu Web uygulaması bazı Web sayfaları, blog, wiki veya Web tabanlı bir takvim uygulaması ya da ticari web sitesi olarak da kullanılabilir.

Bu öğreticinin ne kadar ayrıntılı olmasını istediğiniz, birkaç faydalı bağlantı verilmiştir:

* [Flask belgeleri](http://flask.pocoo.org/)
* [Flask, Dummies için bir başlangıç kılavuzu](https://codeburst.io/flask-for-dummies-a-beginners-guide-to-flask-part-uno-53aec6afc5b1)

## <a name="prerequisites"></a>Ön koşullar

Bu öğretici için ihtiyacınız olan yazılım ve abonelik anahtarlarını gözden geçirelim.

* [Python 3.5.2 veya üzeri](https://www.python.org/downloads/)
* [Git araçları](https://git-scm.com/downloads)
* [Visual Studio Code](https://code.visualstudio.com/) veya [atom](https://atom.io/) gibi bir IDE veya metin düzenleyici  
* [Chrome](https://www.google.com/chrome/browser/) veya [Firefox](https://www.mozilla.org/firefox)
* Bir **Translator metin çevirisi** abonelik anahtarı (bölge seçmeniz gerekmediğini unutmayın.)
* **Batı ABD** bölgesinde **metin analizi** abonelik anahtarı.
* **Batı ABD** bölgesindeki bir **konuşma Hizmetleri** abonelik anahtarı.

## <a name="create-an-account-and-subscribe-to-resources"></a>Hesap oluşturma ve kaynaklara abone olma

Daha önce belirtildiği gibi, bu öğretici için üç abonelik anahtarına ihtiyacınız olacak. Bu, için Azure hesabınızda bir kaynak oluşturmanız gerektiği anlamına gelir:
* Translator Metni
* Metin Analizi
* Konuşma Hizmetleri

Kaynak oluşturmak için adım adım yönergeler için Azure portal bilişsel [Hizmetler hesabı oluşturma '](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) yı kullanın.

> [!IMPORTANT]
> Bu öğreticide, lütfen kaynaklarınızı Batı ABD bölgesinde oluşturun. Farklı bir bölge kullanıyorsanız, Python dosyalarınızın her birinde temel URL 'YI ayarlamanız gerekir.

## <a name="set-up-your-dev-environment"></a>Geliştirme ortamınızı kurma

Flask Web uygulamanızı oluşturmadan önce, projeniz için bir çalışma dizini oluşturmanız ve birkaç Python paketi yüklemeniz gerekir.

### <a name="create-a-working-directory"></a>Çalışma dizini oluşturma

1. Komut satırı (Windows) veya Terminal (macOS/Linux) öğesini açın. Ardından, projeniz için bir çalışma dizini ve alt dizinler oluşturun:  

   ```
   mkdir -p flask-cog-services/static/scripts && mkdir flask-cog-services/templates
   ```
2. Projenizin çalışma dizinine geçin:  

   ```
   cd flask-cog-services
   ```

### <a name="create-and-activate-your-virtual-environment-with-virtualenv"></a>`virtualenv` ile sanal ortamınızı oluşturma ve etkinleştirme

`virtualenv`kullanarak Flask uygulamamız için bir sanal ortam oluşturalım. Sanal ortamın kullanılması, üzerinde çalışmak için temiz bir ortam olmasını sağlar.

1. Çalışma dizininizde, sanal ortam oluşturmak için şu komutu çalıştırın: **MacOS/Linux:**
   ```
   virtualenv venv --python=python3
   ```
   Sanal ortamın Python 3 ' ü kullanması gerektiğini açıkça bildirdik. Bu, birden çok Python yüklemesi olan kullanıcıların doğru sürümü kullanmasını sağlar.

   **Windows CMD/Windows bash:**
   ```
   virtualenv venv
   ```
   Şeyleri basit tutmak için sanal ortamınızı venv olarak adlandırıyoruz.

2. Sanal ortamınızı etkinleştirme komutları, platforma/kabuğa bağlı olarak değişir:   

   | Platform | Shell | Komut |
   |----------|-------|---------|
   | macOS/Linux | Bash/ZSH | `source venv/bin/activate` |
   | Windows | Bash | `source venv/Scripts/activate` |
   | | Komut Satırı | `venv\Scripts\activate.bat` |
   | | PowerShell | `venv\Scripts\Activate.ps1` |

   Bu komutu çalıştırdıktan sonra komut satırlarınızın veya Terminal oturumunuzun `venv`önceden başında olması gerekir.

3. Oturumu dilediğiniz zaman komut satırına veya terminale yazarak devre dışı bırakabilirsiniz: `deactivate`.

> [!NOTE]
> Python 'da sanal ortamlar oluşturmaya ve yönetmeye yönelik kapsamlı belgeler bulunur, bkz. [virtualalenv](https://virtualenv.pypa.io/en/latest/).

### <a name="install-requests"></a>İstekleri yükler

İstekler HTTP 1,1 istekleri göndermek için kullanılan popüler bir modüldür. URL 'nize Sorgu dizelerini el ile eklemeniz veya GÖNDERI verilerinizi biçimlendirmek gerekmez.

1. İstekleri yüklemek için şunu çalıştırın:

   ```
   pip install requests
   ```

> [!NOTE]
> İstekler hakkında daha fazla bilgi edinmek istiyorsanız bkz. [istekler: insanlar IÇIN http](http://docs.python-requests.org/en/master/).

### <a name="install-and-configure-flask"></a>Flask 'yi yükleyip yapılandırma

Ardından Flask 'yi yüklememiz gerekir. Flask web uygulamamız için yönlendirmeyi işler ve son kullanıcıdan abonelik anahtarlarımızı gizleyen sunucudan sunucuya çağrılar yapmamızı sağlar.

1. Flask 'yi yüklemek için şunu çalıştırın:
   ```
   pip install Flask
   ```
   Flask 'nin yüklendiğinden emin olalım. Çalıştırın:
   ```
   flask --version
   ```
   Sürüm terminal 'ye yazdırılmalıdır. Diğer her şey bir sorun olduğunu gösterir.

2. Flask uygulamasını çalıştırmak için Flask komutunu ya da Python 'un-a anahtarını Flask ile birlikte kullanabilirsiniz. Bunu yapmadan önce, `FLASK_APP` ortam değişkenini dışarı aktararak terminalinize hangi uygulamayı çalışacağınızı bildirmeniz gerekir:

   **MacOS/Linux**:
   ```
   export FLASK_APP=app.py
   ```

   **Windows**:
   ```
   set FLASK_APP=app.py
   ```

## <a name="create-your-flask-app"></a>Flask uygulamanızı oluşturma

Bu bölümde, kullanıcılar uygulamanızın köküne geldiğinde bir HTML dosyası döndüren bir barekemikler Flask uygulaması oluşturacaksınız. Kodu çekmeye çalışırken çok fazla zaman harcamayın, bu dosyayı daha sonra güncelleştirmek için geri döneceğiz.

### <a name="what-is-a-flask-route"></a>Flask rotası nedir?

"[Rotalar](http://flask.pocoo.org/docs/1.0/api/#flask.Flask.route)" hakkında konuşmak için bir dakikanızı atalım. Yönlendirme, bir URL 'YI belirli bir işleve bağlamak için kullanılır. Flask, işlevleri belirli URL 'lere kaydetmek için yol dekoratlarını kullanır. Örneğin, bir Kullanıcı Web uygulamamızın köküne (`/`) gittiğinde `index.html` işlenir.  

```python
@app.route('/')
def index():
    return render_template('index.html')
```

Bu girişe göz atmak için bir örneğe bakalım.

```python
@app.route('/about')
def about():
    return render_template('about.html')
```

Bu kod, bir Kullanıcı `about.html` dosyasının işlendiği `http://your-web-app.com/about` ' a gittiğinde sağlar.

Bu örnekler, bir kullanıcı için HTML sayfalarının nasıl işleneceğini gösterir, ancak bir düğmeye basıldığında API 'Leri çağırmak için yollar kullanılabilir veya giriş sayfasından uzaklaşmak zorunda kalmadan istediğiniz sayıda eylem yapabilirsiniz. Çeviri, yaklaşım ve konuşma senkiyle ilgili yollar oluştururken bunu eylemde görürsünüz.

### <a name="get-started"></a>Başlarken

1. Projeyi IDE 'de açın, sonra çalışma dizininizin kökünde `app.py` adlı bir dosya oluşturun. Sonra, bu kodu `app.py` kopyalayın ve kaydedin:

   ```python
   from flask import Flask, render_template, url_for, jsonify, request

   app = Flask(__name__)
   app.config['JSON_AS_ASCII'] = False

   @app.route('/')
   def index():
       return render_template('index.html')
   ```

   Bu kod bloğu, bir Kullanıcı Web uygulamanızın köküne her gittiğinde `index.html` görüntülemesini söyler (`/`).

2. Daha sonra, Web uygulamam için ön ucu oluşturalım. `templates` dizininde `index.html` adlı bir dosya oluşturun. Sonra bu kodu `templates/index.html`kopyalayın.

   ```html
   <!doctype html>
   <html lang="en">
     <head>
       <!-- Required metadata tags -->
       <meta charset="utf-8">
       <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
       <meta name="description" content="Translate and analyze text with Azure Cognitive Services.">
       <!-- Bootstrap CSS -->
       <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/css/bootstrap.min.css" integrity="sha384-Gn5384xqQ1aoWXA+058RXPxPg6fy4IWvTNh0E263XmFcJlSAwiGgFAW/dAiS6JXm" crossorigin="anonymous">
       <title>Translate and analyze text with Azure Cognitive Services</title>
     </head>
     <body>
       <div class="container">
         <h1>Translate, synthesize, and analyze text with Azure</h1>
         <p>This simple web app uses Azure for text translation, text-to-speech conversion, and sentiment analysis of input text and translations. Learn more about <a href="https://docs.microsoft.com/azure/cognitive-services/">Azure Cognitive Services</a>.
        </p>
        <!-- HTML provided in the following sections goes here. -->

        <!-- End -->
       </div>

       <!-- Required Javascript for this tutorial -->
       <script src="https://code.jquery.com/jquery-3.2.1.slim.min.js" integrity="sha384-KJ3o2DKtIkvYIK3UENzmM7KCkRr/rE9/Qpg6aAZGJwFDMVNA/GpGFF93hXpG5KkN" crossorigin="anonymous"></script>
       <script src="https://ajax.googleapis.com/ajax/libs/jquery/3.3.1/jquery.min.js"></script>
       <script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.12.9/umd/popper.min.js" integrity="sha384-ApNbgh9B+Y1QKtv3Rn7W3mgPxhU9K/ScQsAP7hUibX39j7fakFPskvXusvfa0b4Q" crossorigin="anonymous"></script>
       <script src="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/js/bootstrap.min.js" integrity="sha384-JZR6Spejh4U02d8jOt6vLEHfe/JQGiRRSQQxSfFWpi1MquVdAyjUar5+76PVCmYl" crossorigin="anonymous"></script>
       <script type = "text/javascript" src ="static/scripts/main.js"></script>
     </body>
   </html>
   ```

3. Flask uygulamasını test edelim. Terminalden şunu çalıştırın:

   ```
   flask run
   ```

4. Bir tarayıcı açın ve belirtilen URL 'ye gidin. Tek sayfalı uygulamanızı görmeniz gerekir. Uygulamayı sonlandırmak için **Ctrl + c** tuşlarına basın.

## <a name="translate-text"></a>Metin çevirme

Basit bir Flask uygulamasının nasıl çalıştığına ilişkin bir fikir sahibi olduğunuza göre şunları yapabilirsiniz:

* Translator Metin Çevirisi API'si çağırmak için bir Python yazın ve bir yanıt döndürün
* Python kodunuzu çağırmak için bir Flask yolu oluşturma
* HTML 'yi metin girişi ve çevirisi, bir dil Seçicisi ve çevir düğmesi için bir alanla güncelleştirme
* Kullanıcıların HTML 'den Flask uygulamanız ile etkileşime geçmesini sağlayan JavaScript yazma

### <a name="call-the-translator-text-api"></a>Translator Metin Çevirisi API'si çağırın

Yapmanız gereken ilk şey, Translator Metin Çevirisi API'si çağırmak için bir işlev yazmaktır. Bu işlev iki bağımsız değişken alır: `text_input` ve `language_output`. Bu işlev, bir Kullanıcı uygulamanızdaki çevir düğmesine her bastığında çağrılır. HTML 'deki metin alanı `text_input`olarak gönderilir ve HTML 'deki dil seçimi değeri `language_output`olarak gönderilir.

1. Çalışma dizininizin kökünde `translate.py` adlı bir dosya oluşturarak başlayalım.
2. Sonra, bu kodu `translate.py`ekleyin. Bu işlev iki bağımsız değişken alır: `text_input` ve `language_output`.
   ```python
   import os, requests, uuid, json

   # Don't forget to replace with your Cog Services subscription key!
   # If you prefer to use environment variables, see Extra Credit for more info.
   subscription_key = 'YOUR_TRANSLATOR_TEXT_SUBSCRIPTION_KEY'
   
   # Don't forget to replace with your Cog Services location!
   # Our Flask route will supply two arguments: text_input and language_output.
   # When the translate text button is pressed in our Flask app, the Ajax request
   # will grab these values from our web app, and use them in the request.
   # See main.js for Ajax calls.
   def get_translation(text_input, language_output):
       base_url = 'https://api.cognitive.microsofttranslator.com'
       path = '/translate?api-version=3.0'
       params = '&to=' + language_output
       constructed_url = base_url + path + params

       headers = {
           'Ocp-Apim-Subscription-Key': subscription_key,
           'Ocp-Apim-Subscription-Region': 'location',
           'Content-type': 'application/json',
           'X-ClientTraceId': str(uuid.uuid4())
       }

       # You can pass more than one object in body.
       body = [{
           'text' : text_input
       }]
       response = requests.post(constructed_url, headers=headers, json=body)
       return response.json()
   ```
3. Translator Metin Çevirisi abonelik anahtarınızı ekleyin ve kaydedin.

### <a name="add-a-route-to-apppy"></a>`app.py` bir yol ekleyin

Daha sonra, `translate.py`çağıran Flask uygulamanızda bir yol oluşturmanız gerekir. Bu yol, Kullanıcı uygulamanızdaki çevir düğmesine her bastığında çağrılır.

Bu uygulama için rota `POST` isteklerini kabul edecek. Bunun nedeni, işlevin metnin çevirisini ve çeviri için bir çıkış dilini beklemesidir.

Flask her isteği ayrıştırmanıza ve yönetmenize yardımcı olacak yardımcı işlevler sağlar. Belirtilen kodda, `get_json()` `POST` isteğinden JSON olarak verileri döndürür. `data['text']` ve `data['to']`kullanarak, metin ve çıkış dili değerleri `translate.py`bulunan `get_translation()` işlevine geçirilir. Bu verileri Web uygulamanızda görüntülemesi gerekeceğinden, son adım yanıtı JSON olarak döndürmemelidir.

Aşağıdaki bölümlerde, yaklaşım Analizi ve konuşma birleştirme için yollar oluştururken bu işlemi tekrarlamalısınız.

1. `app.py` açın ve `app.py` en üstünde içeri aktarma ifadesini bulun ve aşağıdaki satırı ekleyin:

   ```python
   import translate
   ```
   Şimdi Flask uygulamamız `translate.py`aracılığıyla kullanılabilir yöntemi kullanabilir.

2. Bu kodu `app.py` sonuna kopyalayın ve kaydedin:

   ```python
   @app.route('/translate-text', methods=['POST'])
   def translate_text():
       data = request.get_json()
       text_input = data['text']
       translation_output = data['to']
       response = translate.get_translation(text_input, translation_output)
       return jsonify(response)
   ```

### <a name="update-indexhtml"></a>Güncelleştirme `index.html`

Artık metni çevirecek bir işleve sahip olduğunuza ve bunu çağırmak için Flask uygulamanızda bir yola sahip olduğunuza göre, bir sonraki adım uygulamanız için HTML oluşturmaya başlamadır. Aşağıdaki HTML birkaç şeyi yapar:

* Kullanıcıların çevrilecek metni girbilecekleri bir metin alanı sağlar.
* Bir dil Seçicisi içerir.
* Çeviri sırasında döndürülen algılanan dili ve güvenirlik puanlarını işlemek için HTML öğeleri içerir.
* Çeviri çıkışının görüntülendiği salt okunurdur bir metin alanı sağlar.
* Bu dosyaya daha sonra öğreticide ekleyeceğiniz yaklaşım analizine ve konuşma senssıs koduna yönelik yer tutucuları içerir.

`index.html`güncelleştirelim.

1. `index.html` açın ve şu kod açıklamalarını bulun:
   ```html
   <!-- HTML provided in the following sections goes here. -->

   <!-- End -->
   ```

2. Kod açıklamalarını bu HTML bloğu ile değiştirin:
   ```html
   <div class="row">
     <div class="col">
       <form>
         <!-- Enter text to translate. -->
         <div class="form-group">
           <label for="text-to-translate"><strong>Enter the text you'd like to translate:</strong></label>
           <textarea class="form-control" id="text-to-translate" rows="5"></textarea>
         </div>
         <!-- Select output language. -->
         <div class="form-group">
           <label for="select-language"><strong>Translate to:</strong></label>
           <select class="form-control" id="select-language">
             <option value="ar">Arabic</option>
             <option value="ca">Catalan</option>
             <option value="zh-Hans">Chinese (Simplified)</option>
             <option value="zh-Hant">Chinese (Traditional)</option>
             <option value="hr">Croatian</option>
             <option value="en">English</option>
             <option value="fr">French</option>
             <option value="de">German</option>
             <option value="el">Greek</option>
             <option value="he">Hebrew</option>
             <option value="hi">Hindi</option>
             <option value="it">Italian</option>
             <option value="ja">Japanese</option>
             <option value="ko">Korean</option>
             <option value="pt">Portuguese</option>
             <option value="ru">Russian</option>
             <option value="es">Spanish</option>
             <option value="th">Thai</option>
             <option value="tr">Turkish</option>
             <option value="vi">Vietnamese</option>
           </select>
         </div>
         <button type="submit" class="btn btn-primary mb-2" id="translate">Translate text</button></br>
         <div id="detected-language" style="display: none">
           <strong>Detected language:</strong> <span id="detected-language-result"></span><br />
           <strong>Detection confidence:</strong> <span id="confidence"></span><br /><br />
         </div>

         <!-- Start sentiment code-->

         <!-- End sentiment code -->

       </form>
     </div>
     <div class="col">
       <!-- Translated text returned by the Translate API is rendered here. -->
       <form>
         <div class="form-group" id="translator-text-response">
           <label for="translation-result"><strong>Translated text:</strong></label>
           <textarea readonly class="form-control" id="translation-result" rows="5"></textarea>
         </div>

         <!-- Start voice font selection code -->

         <!-- End voice font selection code -->

       </form>

       <!-- Add Speech Synthesis button and audio element -->

       <!-- End Speech Synthesis button -->

     </div>
   </div>
   ```

Sonraki adım, bazı JavaScript yazmak için kullanılır. Bu, HTML ve Flask rotası arasındaki köprüdir.

### <a name="create-mainjs"></a>`main.js` oluştur  

`main.js` dosyası, HTML ve Flask rotası arasındaki köprüdir. Uygulamanız, içerik işlemek için jQuery, Ajax ve XMLHttpRequest özelliklerinin bir birleşimini kullanır ve Flask yollarınız için `POST` istekleri yapar.

Aşağıdaki kodda, Flask yönlendirmenize bir istek oluşturmak için HTML 'deki içerik kullanılır. Özellikle, metin alanının ve dil seçicinin içeriği değişkenlere atanır ve sonra `translate-text`istek içinde geçirilir.

Kod daha sonra yanıt boyunca yinelenir ve HTML 'yi çeviri, algılanan dil ve Güvenirlik puanı ile güncelleştirir.

1. IDE 'nizden `static/scripts` dizininde `main.js` adlı bir dosya oluşturun.
2. Bu kodu `static/scripts/main.js`kopyalayın:
   ```javascript
   //Initiate jQuery on load.
   $(function() {
     //Translate text with flask route
     $("#translate").on("click", function(e) {
       e.preventDefault();
       var translateVal = document.getElementById("text-to-translate").value;
       var languageVal = document.getElementById("select-language").value;
       var translateRequest = { 'text': translateVal, 'to': languageVal }

       if (translateVal !== "") {
         $.ajax({
           url: '/translate-text',
           method: 'POST',
           headers: {
               'Content-Type':'application/json'
           },
           dataType: 'json',
           data: JSON.stringify(translateRequest),
           success: function(data) {
             for (var i = 0; i < data.length; i++) {
               document.getElementById("translation-result").textContent = data[i].translations[0].text;
               document.getElementById("detected-language-result").textContent = data[i].detectedLanguage.language;
               if (document.getElementById("detected-language-result").textContent !== ""){
                 document.getElementById("detected-language").style.display = "block";
               }
               document.getElementById("confidence").textContent = data[i].detectedLanguage.score;
             }
           }
         });
       };
     });
     // In the following sections, you'll add code for sentiment analysis and
     // speech synthesis here.
   })
   ```

### <a name="test-translation"></a>Test çevirisi

Uygulamada çeviri testi yapın.

```
flask run
```

Belirtilen sunucu adresine gidin. Giriş alanına metin yazın, bir dil seçin ve çevir ' e basın. Çeviri almalısınız. İşe yaramazsa, abonelik anahtarınızı eklediğinizden emin olun.

> [!TIP]
> Yaptığınız değişiklikler görüntülenmiyorsa veya uygulama, istediğiniz şekilde çalışmazsa, Önbelleğinizi temizlemeyi veya bir özel/geçersiz bir pencere açmayı deneyin.

Uygulamayı sonlandırmak için **Ctrl + c** tuşlarına basın ve ardından sonraki bölüme gidin.

## <a name="analyze-sentiment"></a>Yaklaşımı analiz etme

[Metin Analizi API'si](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) , yaklaşım analizi gerçekleştirmek, metinden anahtar ifadeleri ayıklamak veya kaynak dilini algılamak için kullanılabilir. Bu uygulamada, girilen metnin pozitif, nötr veya negatif olduğunu anlamak için yaklaşım analizini kullanacağız. API, 0 ile 1 arasında bir sayısal puan döndürür. Puanın 1’e yakın olması yaklaşımın olumlu olduğunu, 0’a yakın olması ise olumsuz olduğunu gösterir.

Bu bölümde birkaç şey yapacağız:

* Yaklaşım analizini gerçekleştirmek ve bir yanıt döndürmek için Metin Analizi API'si çağırmak üzere bir Python yazın
* Python kodunuzu çağırmak için bir Flask yolu oluşturma
* HTML 'yi, yaklaşım puanları için bir alanla ve analiz yapmak için bir düğmeye güncelleştirin
* Kullanıcıların HTML 'den Flask uygulamanız ile etkileşime geçmesini sağlayan JavaScript yazma

### <a name="call-the-text-analytics-api"></a>Metin Analizi API’sini çağırma

Metin Analizi API'si çağırmak için bir işlev yazalım. Bu işlev dört bağımsız değişken alır: `input_text`, `input_language`, `output_text`ve `output_language`. Bu işlev, bir Kullanıcı uygulamanızdaki yaklaşım analizini Çalıştır düğmesine her bastığında çağrılır. Kullanıcı tarafından metin alanından ve dil seçicinden, algılanan dilin ve çeviri çıktısının her istekle birlikte sağlandığı veriler. Yanıt nesnesi, kaynak ve çeviri için yaklaşım puanlarını içerir. Aşağıdaki bölümlerde, yanıtı ayrıştırmak ve uygulamanızda kullanmak için bazı JavaScript yazılacak. Şimdilik Metin Analizi API'si çağrısına odaklanalım.

1. Çalışma dizininizin kökünde `sentiment.py` adlı bir dosya oluşturalım.
2. Sonra, bu kodu `sentiment.py`ekleyin.
   ```python
   import os, requests, uuid, json

   # Don't forget to replace with your Cog Services subscription key!
   subscription_key = 'YOUR_TEXT_ANALYTICS_SUBSCRIPTION_KEY'

   # Our Flask route will supply four arguments: input_text, input_language,
   # output_text, output_language.
   # When the run sentiment analysis button is pressed in our Flask app,
   # the Ajax request will grab these values from our web app, and use them
   # in the request. See main.js for Ajax calls.

   def get_sentiment(input_text, input_language, output_text, output_language):
       base_url = 'https://westus.api.cognitive.microsoft.com/text/analytics'
       path = '/v2.0/sentiment'
       constructed_url = base_url + path

       headers = {
           'Ocp-Apim-Subscription-Key': subscription_key,
           'Content-type': 'application/json',
           'X-ClientTraceId': str(uuid.uuid4())
       }

       # You can pass more than one object in body.
       body = {
           'documents': [
               {
                   'language': input_language,
                   'id': '1',
                   'text': input_text
               },
               {
                   'language': output_language,
                   'id': '2',
                   'text': output_text
               }
           ]
       }
       response = requests.post(constructed_url, headers=headers, json=body)
       return response.json()
   ```
3. Metin Analizi abonelik anahtarınızı ekleyin ve kaydedin.

### <a name="add-a-route-to-apppy"></a>`app.py` bir yol ekleyin

Flask uygulamanızda `sentiment.py`çağıran bir yol oluşturalım. Bu yol, bir Kullanıcı uygulamanızdaki yaklaşım analizini Çalıştır düğmesine her bastığında çağrılır. Çeviri rotası gibi, bu yol, işlevin bağımsız değişkenleri beklediği için `POST` isteklerini kabul eder.

1. `app.py` açın ve `app.py` en üstündeki içeri aktarma ifadesini bulun ve güncelleştirin:

   ```python
   import translate, sentiment
   ```
   Şimdi Flask uygulamamız `sentiment.py`aracılığıyla kullanılabilir yöntemi kullanabilir.

2. Bu kodu `app.py` sonuna kopyalayın ve kaydedin:
   ```python
   @app.route('/sentiment-analysis', methods=['POST'])
   def sentiment_analysis():
       data = request.get_json()
       input_text = data['inputText']
       input_lang = data['inputLanguage']
       output_text = data['outputText']
       output_lang =  data['outputLanguage']
       response = sentiment.get_sentiment(input_text, input_lang, output_text, output_lang)
       return jsonify(response)
   ```

### <a name="update-indexhtml"></a>Güncelleştirme `index.html`

Yaklaşım analizini çalıştırmaya yönelik bir işleviniz ve bunu çağırmak için Flask uygulamanızda bir yol olduğuna göre, sonraki adım uygulamanız için HTML yazmaya başlamadır. Aşağıdaki HTML birkaç şeyi yapar:

* Yaklaşım analizini çalıştırmak için uygulamanıza düğme ekler
* Yaklaşım puanlamasını açıklayan bir öğe ekler
* Yaklaşım puanlarını göstermek için bir öğe ekler

1. `index.html` açın ve şu kod açıklamalarını bulun:
   ```html
   <!-- Start sentiment code-->

   <!-- End sentiment code -->
   ```

2. Kod açıklamalarını bu HTML bloğu ile değiştirin:
   ```html
   <button type="submit" class="btn btn-primary mb-2" id="sentiment-analysis">Run sentiment analysis</button></br>
   <div id="sentiment" style="display: none">
      <p>Sentiment scores are provided on a 1 point scale. The closer the sentiment score is to 1, indicates positive sentiment. The closer it is to 0, indicates negative sentiment.</p>
      <strong>Sentiment score for input:</strong> <span id="input-sentiment"></span><br />
      <strong>Sentiment score for translation:</strong> <span id="translation-sentiment"></span>
   </div>
   ```

### <a name="update-mainjs"></a>Güncelleştirme `main.js`

Aşağıdaki kodda, Flask yönlendirmenize bir istek oluşturmak için HTML 'deki içerik kullanılır. Özellikle, metin alanı ve dil seçicisinin içeriği değişkenlere atanır ve sonra istek içinde `sentiment-analysis` yoluna geçirilir.

Kod daha sonra yanıt boyunca yinelenir ve HTML 'yi yaklaşım puanlarına göre güncelleştirir.

1. IDE 'nizden `static` dizininde `main.js` adlı bir dosya oluşturun.

2. Bu kodu `static/scripts/main.js`kopyalayın:
   ```javascript
   //Run sentinment analysis on input and translation.
   $("#sentiment-analysis").on("click", function(e) {
     e.preventDefault();
     var inputText = document.getElementById("text-to-translate").value;
     var inputLanguage = document.getElementById("detected-language-result").innerHTML;
     var outputText = document.getElementById("translation-result").value;
     var outputLanguage = document.getElementById("select-language").value;

     var sentimentRequest = { "inputText": inputText, "inputLanguage": inputLanguage, "outputText": outputText,  "outputLanguage": outputLanguage };

     if (inputText !== "") {
       $.ajax({
         url: "/sentiment-analysis",
         method: "POST",
         headers: {
             "Content-Type":"application/json"
         },
         dataType: "json",
         data: JSON.stringify(sentimentRequest),
         success: function(data) {
           for (var i = 0; i < data.documents.length; i++) {
             if (typeof data.documents[i] !== "undefined"){
               if (data.documents[i].id === "1") {
                 document.getElementById("input-sentiment").textContent = data.documents[i].score;
               }
               if (data.documents[i].id === "2") {
                 document.getElementById("translation-sentiment").textContent = data.documents[i].score;
               }
             }
           }
           for (var i = 0; i < data.errors.length; i++) {
             if (typeof data.errors[i] !== "undefined"){
               if (data.errors[i].id === "1") {
                 document.getElementById("input-sentiment").textContent = data.errors[i].message;
               }
               if (data.errors[i].id === "2") {
                 document.getElementById("translation-sentiment").textContent = data.errors[i].message;
               }
             }
           }
           if (document.getElementById("input-sentiment").textContent !== '' && document.getElementById("translation-sentiment").textContent !== ""){
             document.getElementById("sentiment").style.display = "block";
           }
         }
       });
     }
   });
   // In the next section, you'll add code for speech synthesis here.
   ```

### <a name="test-sentiment-analysis"></a>Test yaklaşımı Analizi

Uygulamanın deneme analizini inceleyelim.

```
flask run
```

Belirtilen sunucu adresine gidin. Giriş alanına metin yazın, bir dil seçin ve çevir ' e basın. Çeviri almalısınız. Sonra, yaklaşım analizini Çalıştır düğmesine basın. İki puan görmeniz gerekir. İşe yaramazsa, abonelik anahtarınızı eklediğinizden emin olun.

> [!TIP]
> Yaptığınız değişiklikler görüntülenmiyorsa veya uygulama, istediğiniz şekilde çalışmazsa, Önbelleğinizi temizlemeyi veya bir özel/geçersiz bir pencere açmayı deneyin.

Uygulamayı sonlandırmak için **Ctrl + c** tuşlarına basın ve ardından sonraki bölüme gidin.

## <a name="convert-text-to-speech"></a>Metin okumayı dönüştürme

[Metin okuma API 'si](https://docs.microsoft.com/azure/cognitive-services/speech-service/text-to-speech) , uygulamanızın metni doğal insan benzeri sentezleştirilmiş konuşmaya dönüştürmesini sağlar. Hizmet standart, sinir ve özel sesleri destekler. Örnek uygulamamız, tam bir liste için kullanılabilir seslerin her birini kullanır, bkz. [desteklenen diller](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#text-to-speech).

Bu bölümde birkaç şey yapacağız:

* Metin okuma API 'siyle metinden konuşmaya dönüştürmek için bazı Python yazma
* Python kodunuzu çağırmak için bir Flask yolu oluşturma
* Metin okumayı ve ses kayıttan yürütme için bir öğeyi dönüştürmek üzere HTML 'yi bir düğmeyle güncelleştirme
* Kullanıcıların Flask uygulamasıyla etkileşime geçmesini sağlayan JavaScript yazma

### <a name="call-the-text-to-speech-api"></a>Metinden konuşmaya API 'sini çağırma

Metinden konuşmaya dönüştürmek için bir işlev yazalım. Bu işlev iki bağımsız değişken alır: `input_text` ve `voice_font`. Bu işlev, bir Kullanıcı uygulamanızdaki konuşmayı konuşmaya Dönüştür düğmesine her bastığında çağrılır. `input_text`, metin çevirme çağrısı tarafından döndürülen çeviri çıktısıdır `voice_font`, HTML 'deki ses yazı tipi seçicisinin değeridir.

1. Çalışma dizininizin kökünde `synthesize.py` adlı bir dosya oluşturalım.

2. Sonra, bu kodu `synthesize.py`ekleyin.
   ```Python
   import os, requests, time
   from xml.etree import ElementTree

   class TextToSpeech(object):
       def __init__(self, input_text, voice_font):
           subscription_key = 'YOUR_SPEECH_SERVICES_SUBSCRIPTION_KEY'
           self.subscription_key = subscription_key
           self.input_text = input_text
           self.voice_font = voice_font
           self.timestr = time.strftime('%Y%m%d-%H%M')
           self.access_token = None

       # This function performs the token exchange.
       def get_token(self):
           fetch_token_url = 'https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken'
           headers = {
               'Ocp-Apim-Subscription-Key': self.subscription_key
           }
           response = requests.post(fetch_token_url, headers=headers)
           self.access_token = str(response.text)

       # This function calls the TTS endpoint with the access token.
       def save_audio(self):
           base_url = 'https://westus.tts.speech.microsoft.com/'
           path = 'cognitiveservices/v1'
           constructed_url = base_url + path
           headers = {
               'Authorization': 'Bearer ' + self.access_token,
               'Content-Type': 'application/ssml+xml',
               'X-Microsoft-OutputFormat': 'riff-24khz-16bit-mono-pcm',
               'User-Agent': 'YOUR_RESOURCE_NAME',
           }
           # Build the SSML request with ElementTree
           xml_body = ElementTree.Element('speak', version='1.0')
           xml_body.set('{http://www.w3.org/XML/1998/namespace}lang', 'en-us')
           voice = ElementTree.SubElement(xml_body, 'voice')
           voice.set('{http://www.w3.org/XML/1998/namespace}lang', 'en-US')
           voice.set('name', 'Microsoft Server Speech Text to Speech Voice {}'.format(self.voice_font))
           voice.text = self.input_text
           # The body must be encoded as UTF-8 to handle non-ascii characters.
           body = ElementTree.tostring(xml_body, encoding="utf-8")

           #Send the request
           response = requests.post(constructed_url, headers=headers, data=body)

           # Write the response as a wav file for playback. The file is located
           # in the same directory where this sample is run.
           return response.content
   ```
3. Konuşma Hizmetleri abonelik anahtarınızı ekleyin ve kaydedin.

### <a name="add-a-route-to-apppy"></a>`app.py` bir yol ekleyin

Flask uygulamanızda `synthesize.py`çağıran bir yol oluşturalım. Bu yol, bir Kullanıcı uygulamanızdaki konuşmayı konuşmaya Dönüştür düğmesine bastığında her seferinde çağrılır. Çeviri ve yaklaşım analizine yönelik yollar gibi, bu yol, işlevin iki bağımsız değişken beklediği bu yana `POST` isteklerini kabul eder: birleştirme metni ve kayıttan yürütme için ses yazı tipi.

1. `app.py` açın ve `app.py` en üstündeki içeri aktarma ifadesini bulun ve güncelleştirin:

   ```python
   import translate, sentiment, synthesize
   ```
   Şimdi Flask uygulamamız `synthesize.py`aracılığıyla kullanılabilir yöntemi kullanabilir.

2. Bu kodu `app.py` sonuna kopyalayın ve kaydedin:

   ```Python
   @app.route('/text-to-speech', methods=['POST'])
   def text_to_speech():
       data = request.get_json()
       text_input = data['text']
       voice_font = data['voice']
       tts = synthesize.TextToSpeech(text_input, voice_font)
       tts.get_token()
       audio_response = tts.save_audio()
       return audio_response
   ```

### <a name="update-indexhtml"></a>Güncelleştirme `index.html`

Metinden konuşmaya dönüştürmek için bir işleviniz olduğuna ve bunu çağırmak için Flask uygulamanızda bir yola sahip olduğunuza göre, sonraki adım uygulamanız için HTML yazmaya başlamadır. Aşağıdaki HTML birkaç şeyi yapar:

* Bir ses seçimi açılır listesini sağlar
* Metni konuşmaya dönüştürmek için düğme ekler
* Sentezlenmiş konuşmayı çalmak için kullanılan bir ses öğesi ekler

1. `index.html` açın ve şu kod açıklamalarını bulun:
   ```html
   <!-- Start voice font selection code -->

   <!-- End voice font selection code -->
   ```

2. Kod açıklamalarını bu HTML bloğu ile değiştirin:
   ```html
   <div class="form-group">
     <label for="select-voice"><strong>Select voice font:</strong></label>
     <select class="form-control" id="select-voice">
       <option value="(ar-SA, Naayf)">Arabic | Male | Naayf</option>
       <option value="(ca-ES, HerenaRUS)">Catalan | Female | HerenaRUS</option>
       <option value="(zh-CN, HuihuiRUS)">Chinese (Mainland) | Female | HuihuiRUS</option>
       <option value="(zh-CN, Kangkang, Apollo)">Chinese (Mainland) | Male | Kangkang, Apollo</option>
       <option value="(zh-HK, Tracy, Apollo)">Chinese (Hong Kong)| Female | Tracy, Apollo</option>
       <option value="(zh-HK, Danny, Apollo)">Chinese (Hong Kong) | Male | Danny, Apollo</option>
       <option value="(zh-TW, Yating, Apollo)">Chinese (Taiwan)| Female | Yaiting, Apollo</option>
       <option value="(zh-TW, Zhiwei, Apollo)">Chinese (Taiwan) | Male | Zhiwei, Apollo</option>
       <option value="(hr-HR, Matej)">Croatian | Male | Matej</option>
       <option value="(en-US, Jessa24kRUS)">English (US) | Female | Jessa24kRUS</option>
       <option value="(en-US, Guy24kRUS)">English (US) | Male | Guy24kRUS</option>
       <option value="(en-IE, Sean)">English (IE) | Male | Sean</option>
       <option value="(fr-FR, Julie, Apollo)">French | Female | Julie, Apollo</option>
       <option value="(fr-FR, HortenseRUS)">French | Female | Julie, HortenseRUS</option>
       <option value="(fr-FR, Paul, Apollo)">French | Male | Paul, Apollo</option>
       <option value="(de-DE, Hedda)">German | Female | Hedda</option>
       <option value="(de-DE, HeddaRUS)">German | Female | HeddaRUS</option>
       <option value="(de-DE, Stefan, Apollo)">German | Male | Apollo</option>
       <option value="(el-GR, Stefanos)">Greek | Male | Stefanos</option>
       <option value="(he-IL, Asaf)">Hebrew (Isreal) | Male | Asaf</option>
       <option value="(hi-IN, Kalpana, Apollo)">Hindi | Female | Kalpana, Apollo</option>
       <option value="(hi-IN, Hemant)">Hindi | Male | Hemant</option>
       <option value="(it-IT, LuciaRUS)">Italian | Female | LuciaRUS</option>
       <option value="(it-IT, Cosimo, Apollo)">Italian | Male | Cosimo, Apollo</option>
       <option value="(ja-JP, Ichiro, Apollo)">Japanese | Male | Ichiro</option>
       <option value="(ja-JP, HarukaRUS)">Japanese | Female | HarukaRUS</option>
       <option value="(ko-KR, HeamiRUS)">Korean | Female | Haemi</option>
       <option value="(pt-BR, HeloisaRUS)">Portuguese (Brazil) | Female | HeloisaRUS</option>
       <option value="(pt-BR, Daniel, Apollo)">Portuguese (Brazil) | Male | Daniel, Apollo</option>
       <option value="(pt-PT, HeliaRUS)">Portuguese (Portugal) | Female | HeliaRUS</option>
       <option value="(ru-RU, Irina, Apollo)">Russian | Female | Irina, Apollo</option>
       <option value="(ru-RU, Pavel, Apollo)">Russian | Male | Pavel, Apollo</option>
       <option value="(ru-RU, EkaterinaRUS)">Russian | Female | EkaterinaRUS</option>
       <option value="(es-ES, Laura, Apollo)">Spanish | Female | Laura, Apollo</option>
       <option value="(es-ES, HelenaRUS)">Spanish | Female | HelenaRUS</option>
       <option value="(es-ES, Pablo, Apollo)">Spanish | Male | Pablo, Apollo</option>
       <option value="(th-TH, Pattara)">Thai | Male | Pattara</option>
       <option value="(tr-TR, SedaRUS)">Turkish | Female | SedaRUS</option>
       <option value="(vi-VN, An)">Vietnamese | Male | An</option>
     </select>
   </div>
   ```

3. Sonra, şu kod açıklamalarını bulun:
   ```html
   <!-- Add Speech Synthesis button and audio element -->

   <!-- End Speech Synthesis button -->
   ```

4. Kod açıklamalarını bu HTML bloğu ile değiştirin:

```html
<button type="submit" class="btn btn-primary mb-2" id="text-to-speech">Convert text-to-speech</button>
<div id="audio-playback">
  <audio id="audio" controls>
    <source id="audio-source" type="audio/mpeg" />
  </audio>
</div>
```

5. Çalışmanızı kaydettiğinizden emin olun.

### <a name="update-mainjs"></a>Güncelleştirme `main.js`

Aşağıdaki kodda, Flask yönlendirmenize bir istek oluşturmak için HTML 'deki içerik kullanılır. Özellikle, çeviri ve ses yazı tipi değişkenlere atanır ve sonra istek içinde `text-to-speech` yoluna geçirilir.

Kod daha sonra yanıt boyunca yinelenir ve HTML 'yi yaklaşım puanlarına göre güncelleştirir.

1. IDE 'nizden `static` dizininde `main.js` adlı bir dosya oluşturun.
2. Bu kodu `static/scripts/main.js`kopyalayın:
   ```javascript
   // Convert text-to-speech
   $("#text-to-speech").on("click", function(e) {
     e.preventDefault();
     var ttsInput = document.getElementById("translation-result").value;
     var ttsVoice = document.getElementById("select-voice").value;
     var ttsRequest = { 'text': ttsInput, 'voice': ttsVoice }

     var xhr = new XMLHttpRequest();
     xhr.open("post", "/text-to-speech", true);
     xhr.setRequestHeader("Content-Type", "application/json");
     xhr.responseType = "blob";
     xhr.onload = function(evt){
       if (xhr.status === 200) {
         audioBlob = new Blob([xhr.response], {type: "audio/mpeg"});
         audioURL = URL.createObjectURL(audioBlob);
         if (audioURL.length > 5){
           var audio = document.getElementById("audio");
           var source = document.getElementById("audio-source");
           source.src = audioURL;
           audio.load();
           audio.play();
         }else{
           console.log("An error occurred getting and playing the audio.")
         }
       }
     }
     xhr.send(JSON.stringify(ttsRequest));
   });
   // Code for automatic language selection goes here.
   ```
3. Neredeyse bitti. Yapacağınız son şey, çeviri için seçilen dile göre otomatik olarak bir ses yazı tipi seçmek üzere `main.js` kod eklemektir. Bu kod bloğunu `main.js`ekleyin:
   ```javascript
   // Automatic voice font selection based on translation output.
   $('select[id="select-language"]').change(function(e) {
     if ($(this).val() == "ar"){
       document.getElementById("select-voice").value = "(ar-SA, Naayf)";
     }
     if ($(this).val() == "ca"){
       document.getElementById("select-voice").value = "(ca-ES, HerenaRUS)";
     }
     if ($(this).val() == "zh-Hans"){
       document.getElementById("select-voice").value = "(zh-HK, Tracy, Apollo)";
     }
     if ($(this).val() == "zh-Hant"){
       document.getElementById("select-voice").value = "(zh-HK, Tracy, Apollo)";
     }
     if ($(this).val() == "hr"){
       document.getElementById("select-voice").value = "(hr-HR, Matej)";
     }
     if ($(this).val() == "en"){
       document.getElementById("select-voice").value = "(en-US, Jessa24kRUS)";
     }
     if ($(this).val() == "fr"){
       document.getElementById("select-voice").value = "(fr-FR, HortenseRUS)";
     }
     if ($(this).val() == "de"){
       document.getElementById("select-voice").value = "(de-DE, HeddaRUS)";
     }
     if ($(this).val() == "el"){
       document.getElementById("select-voice").value = "(el-GR, Stefanos)";
     }
     if ($(this).val() == "he"){
       document.getElementById("select-voice").value = "(he-IL, Asaf)";
     }
     if ($(this).val() == "hi"){
       document.getElementById("select-voice").value = "(hi-IN, Kalpana, Apollo)";
     }
     if ($(this).val() == "it"){
       document.getElementById("select-voice").value = "(it-IT, LuciaRUS)";
     }
     if ($(this).val() == "ja"){
       document.getElementById("select-voice").value = "(ja-JP, HarukaRUS)";
     }
     if ($(this).val() == "ko"){
       document.getElementById("select-voice").value = "(ko-KR, HeamiRUS)";
     }
     if ($(this).val() == "pt"){
       document.getElementById("select-voice").value = "(pt-BR, HeloisaRUS)";
     }
     if ($(this).val() == "ru"){
       document.getElementById("select-voice").value = "(ru-RU, EkaterinaRUS)";
     }
     if ($(this).val() == "es"){
       document.getElementById("select-voice").value = "(es-ES, HelenaRUS)";
     }
     if ($(this).val() == "th"){
       document.getElementById("select-voice").value = "(th-TH, Pattara)";
     }
     if ($(this).val() == "tr"){
       document.getElementById("select-voice").value = "(tr-TR, SedaRUS)";
     }
     if ($(this).val() == "vi"){
       document.getElementById("select-voice").value = "(vi-VN, An)";
     }
   });
   ```

### <a name="test-your-app"></a>Uygulamanızı test etme

Diyelim çünkü konuşmayı test edin.

```
flask run
```

Belirtilen sunucu adresine gidin. Giriş alanına metin yazın, bir dil seçin ve çevir ' e basın. Çeviri almalısınız. Sonra bir ses seçin ve sonra metni konuşmaya Dönüştür düğmesine basın. çeviri, sentezlenmiş konuşma olarak oynatılabilir. İşe yaramazsa, abonelik anahtarınızı eklediğinizden emin olun.

> [!TIP]
> Yaptığınız değişiklikler görüntülenmiyorsa veya uygulama, istediğiniz şekilde çalışmazsa, Önbelleğinizi temizlemeyi veya bir özel/geçersiz bir pencere açmayı deneyin.

Bu, çevirileri gerçekleştiren, yaklaşımı çözümleyen ve sentezlenmiş konuşmayı karşılayan bir çalışan uygulamanız vardır. Uygulamayı sonlandırmak için **Ctrl + c** tuşlarına basın. Diğer Azure bilişsel [Hizmetler](https://docs.microsoft.com/azure/cognitive-services/)'i kullanıma aldığınızdan emin olun.

## <a name="get-the-source-code"></a>Kaynak kodunu al

Bu proje için kaynak kodu [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Flask-App-Tutorial)' da kullanılabilir.

## <a name="next-steps"></a>Sonraki adımlar

* [Translator Metin Çevirisi API’si başvurusu](https://docs.microsoft.com/azure/cognitive-services/Translator/reference/v3-0-reference)
* [Metin Analizi API'si başvurusu](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7)
* [Metin Okuma API başvurusu](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-text-to-speech)
