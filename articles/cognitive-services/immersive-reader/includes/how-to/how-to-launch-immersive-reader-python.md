---
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: include
ms.date: 03/04/2021
ms.author: erhopf
ms.openlocfilehash: f88ca39a378e997bb72300188166192e3383f6f1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102620186"
---
## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/cognitive-services)
* Azure Active Directory kimlik doğrulaması için yapılandırılmış bir tam ekran okuyucu kaynağı. Kurulumunu yapmak için [Bu yönergeleri](../../how-to-create-immersive-reader.md) izleyin.  Ortam özellikleri yapılandırılırken burada oluşturulan bazı değerler gerekir. Daha sonra başvurmak üzere oturumunuzun çıkışını bir metin dosyasına kaydedin.
* [Git](https://git-scm.com/).
* [Modern Okuyucu SDK 'sı](https://github.com/microsoft/immersive-reader-sdk).
* [Python](https://www.python.org/downloads/) ve [PIP](https://docs.python.org/3/installing/index.html). Python 3,4 ' den başlayarak, PIP Python ikili yükleyicilerine göre varsayılan olarak dahil edilir.
* [Flask](https://flask.palletsprojects.com/en/1.0.x/).
* [Jınja](http://jinja.pocoo.org/docs/2.10/).
* [virtualalenv](https://virtualenv.pypa.io/en/latest/) ve [virtualalenvwrapper-Win for Windows](https://pypi.org/project/virtualenvwrapper-win/) veya [VIRTUALENVWRAPPER for OSX](https://virtualenvwrapper.readthedocs.io/en/latest/).
* [İstekler modülü](https://pypi.org/project/requests/2.7.0/).
* [Visual Studio Code](https://code.visualstudio.com/)gıbı bir IDE.

## <a name="configure-authentication-credentials"></a>Kimlik doğrulama kimlik bilgilerini yapılandırma

**. Env** adlı yeni bir dosya oluşturun ve içine aşağıdaki adları ve değerleri yapıştırın. Tam ekran okuyucuyu oluştururken verilen değerleri sağlayın.

```text
TENANT_ID={YOUR_TENANT_ID}
CLIENT_ID={YOUR_CLIENT_ID}
CLIENT_SECRET={YOUR_CLIENT_SECRET}
SUBDOMAIN={YOUR_SUBDOMAIN}
```

Ortak yapılmaması gereken gizli dizileri içerdiğinden, bu dosyayı kaynak denetimine teslim etmeyin.

Örnek, [OAuth](https://oauth.net/2/)gibi bir kimlik doğrulaması biçiminin arkasındaki **getimmersivereadertoken** API uç noktasının güvenliğini sağlayın. Kimlik doğrulaması, yetkisiz kullanıcıların, derinlikli okuyucu hizmetinize ve faturalandırmaya yönelik belirteçleri kullanmasını engeller. Bu iş, Bu öğreticinin kapsamı dışındadır.

## <a name="create-a-python-web-app-on-windows"></a>Windows 'da Python web uygulaması oluşturma

Windows 'da kullanarak bir Python web uygulaması oluşturun `flask` .

[Git](https://git-scm.com/)'i yükler.

Git yüklendikten sonra, bir komut istemi açın ve tam ekran okuyucu SDK Git deposunu bilgisayarınızdaki bir klasöre kopyalayın.

```cmd
git clone https://github.com/microsoft/immersive-reader-sdk.git
```

[Python](https://www.python.org/downloads/)'ı yükler.

**Yola Python Ekle** onay kutusunu seçin.

![Python Windows yüklemesi iletişim kutusu 1. adım](../../media/pythoninstallone.jpg)

Onay kutularını seçerek **Isteğe bağlı özellikler** ekleyin ve ardından **İleri**' yi seçin.

![Python Windows yüklemesi iletişim kutusu 2. adım](../../media/pythoninstalltwo.jpg)

**Özel yükleme**' yi seçin ve yükleme yolunu kök klasörünüz olarak ayarlayın (örneğin,) `C:\Python37-32\` . Ardından **Yükle**’yi seçin.

![Python Windows yüklemesi iletişim kutusu 3. adım](../../media/pythoninstallthree.jpg)

Python yüklemesi tamamlandıktan sonra, bir komut istemi açın ve `cd` Python betikleri klasörüne gitmek için öğesini kullanın.

```cmd
cd C:\Python37-32\Scripts
```

Flask 'yi yükler.

```cmd
pip install flask
```

Jinja2 'i yükler. Python için tam özellikli bir şablon altyapısıdır.

```cmd
pip install jinja2
```

Virtualenv 'yi yükler. Bu araç yalıtılmış Python ortamları oluşturur.

```cmd
pip install virtualenv
```

Virtualalenvwrapper-WIN 'yi yükler. Virtualalenvwrapper arkasındaki fikir virtualalenv kullanımını kolaylaştırmaya yönelik.

```cmd
pip install virtualenvwrapper-win
```

İstekler modülünü yükler. İstekler, Python 'da yazılmış bir apache2 lisanslı HTTP kitaplığı.

```cmd
pip install requests
```

Python-dotenv modülünü yükler. Bu modül,. env dosyasından anahtar-değer çiftini okur ve ortam değişkenine ekler.

```cmd
pip install python-dotenv
```

Sanal bir ortam oluşturun.

```cmd
mkvirtualenv advanced-python
```

`cd`Örnek proje kök klasörüne gitmek için öğesini kullanın.

```cmd
cd C:\immersive-reader-sdk\js\samples\advanced-python
```

Örnek projeyi ortamla bağlayın. Bu eylem, yeni oluşturulan sanal ortamı örnek proje kök klasörüne eşler.

```cmd
setprojectdir .
```

Sanal ortamı etkinleştirin.

```cmd
activate
```

Proje artık etkin olmalıdır ve komut isteminde gibi bir şey görürsünüz `(advanced-python) C:\immersive-reader-sdk\js\samples\advanced-python>` .

Ortamı devre dışı bırakın.

```cmd
deactivate
```

`(advanced-python)`Ortam devre dışı bırakıldığı için ön ek gitmelidir.

Ortamı yeniden etkinleştirmek için `workon advanced-python` örnek proje kök klasöründen çalıştırın.

```cmd
workon advanced-python
```

### <a name="start-the-immersive-reader-with-sample-content"></a>Örnek içerikle modern okuyucu başlatma

Ortam etkin olduğunda, örnek proje kök klasöründen girerek örnek projeyi çalıştırın `flask run` .

```cmd
flask run
```

Tarayıcınızı açın ve adresine gidin http://localhost:5000 .

## <a name="create-a-python-web-app-on-osx"></a>OSX üzerinde bir Python web uygulaması oluşturma

OSX üzerinde kullanarak bir Python web uygulaması oluşturun `flask` .

[Git](https://git-scm.com/)'i yükler.

Git yüklendikten sonra, Terminal 'yi açın ve modern Okuyucu SDK Git deposunu bilgisayarınızdaki bir klasöre kopyalayın.

```bash
git clone https://github.com/microsoft/immersive-reader-sdk.git
```

[Python](https://www.python.org/downloads/)'ı yükler.

Python kök klasörü, örneğin,, `Python37-32` artık uygulamalar klasöründe olmalıdır.

Python yüklemesi tamamlandıktan sonra, ' yi açın ve `cd` Python betikleri klasörüne gitmek için öğesini kullanın.

```bash
cd immersive-reader-sdk/js/samples/advanced-python
```

Pip yükleyin.

```bash
curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py
```

İzin sorunlarından kaçınmak için şu anda oturum açmış olan kullanıcıya yönelik PIP 'yi yüklemek için aşağıdaki kodu çalıştırın.

```bash
python get-pip.py --user
```

```bash
sudo nano /etc/paths
```

- İstendiğinde parolanızı girin.
- PIP yüklemenizin yolunu PATH değişkenine ekleyin.
- Dosyanın sonuna gidin ve listenin son öğesi olarak eklemek istediğiniz yolu girin, örneğin `PATH=$PATH:/usr/local/bin` .
- Çıkmak için **CTRL + X** ' i seçin.
- Değiştirilen arabelleği kaydetmek için **Y** girin.
- İşte bu kadar! Bunu test etmek için yeni bir Terminal penceresinde girin `echo $PATH` .

Flask 'yi yükler.

```bash
pip install flask --user
```

Jinja2 'i yükler. Python için tam özellikli bir şablon altyapısıdır.

```bash
pip install Jinja2 --user
```

Virtualenv 'yi yükler. Bu araç yalıtılmış Python ortamları oluşturur.

```bash
pip install virtualenv --user
```

Virtualalenvwrapper 'ı yükler. Virtualalenvwrapper arkasındaki fikir virtualalenv kullanımını kolaylaştırmaya yönelik.

```bash
pip install virtualenvwrapper --user
```

İstekler modülünü yükler. İstekler, Python 'da yazılmış bir apache2 lisanslı HTTP kitaplığı.

```bash
pip install requests --user
```

Python-dotenv modülünü yükler. Bu modül,. env dosyasından anahtar-değer çiftini okur ve ortam değişkenine ekler.

```bash
pip install python-dotenv --user
```

Sanal ortamlarınızı tutmak istediğiniz klasörü seçin ve şu komutu çalıştırın:

```bash
mkdir ~/.virtualenvs
```

`cd`Tam ekran okuyucu SDK 'Sı Python örnek uygulama klasörüne gitmek için kullanın.

```bash
cd immersive-reader-sdk/js/samples/advanced-python
```

Sanal bir ortam oluşturun.

```bash
mkvirtualenv -p /usr/local/bin/python3 advanced-python
```

Örnek projeyi ortamla bağlayın. Bu eylem, yeni oluşturulan sanal ortamı örnek proje kök klasörüne eşler.

```bash
setprojectdir .
```

Sanal ortamı etkinleştirin.

```bash
activate
```

Proje artık etkin olmalıdır ve komut isteminde gibi bir şey görürsünüz `(advanced-python) /immersive-reader-sdk/js/samples/advanced-python>` .

Ortamı devre dışı bırakın.

```bash
deactivate
```

`(advanced-python)`Ortam devre dışı bırakıldığı için ön ek gitmelidir.

Ortamı yeniden etkinleştirmek için `workon advanced-python` örnek proje kök klasöründen çalıştırın.

```bash
workon advanced-python
```

## <a name="start-the-immersive-reader-with-sample-content"></a>Örnek içerikle modern okuyucu başlatma

Ortam etkin olduğunda, örnek proje kök klasöründen girerek örnek projeyi çalıştırın `flask run` .

```bash
flask run
```

Tarayıcınızı açın ve adresine gidin http://localhost:5000 .

## <a name="next-steps"></a>Sonraki adımlar

* [Modern Okuyucu SDK 'sını](https://github.com/microsoft/immersive-reader-sdk) ve [tam ekran okuyucu SDK başvurusunu](../../reference.md)bulun.
* [GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/)'daki kod örneklerini görüntüleyin.
