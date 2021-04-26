---
title: LUSıS için Docker Kapsayıcıları yükleyip çalıştırın
titleSuffix: Azure Cognitive Services
description: Eğitilen veya yayınlanan uygulamanızı yüklemek için LUSıS kapsayıcısını kullanın ve şirket içi tahminlere erişim elde edin.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18, cog-serv-seo-aug-2020
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 03/02/2021
ms.author: aahi
keywords: Şirket içi, Docker, kapsayıcı
ms.openlocfilehash: e157e976186f03aa984877435c42b996ce476740
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102040201"
---
# <a name="install-and-run-docker-containers-for-luis"></a>LUSıS için Docker Kapsayıcıları yükleyip çalıştırın

[!INCLUDE [container image location note](../containers/includes/image-location-note.md)]

Kapsayıcılar, Lu, kendi ortamınızda kullanmanıza olanak sağlar. Kapsayıcılar, belirli güvenlik ve veri idare gereksinimleri için çok kullanışlıdır. Bu makalede, bir LUSıS kapsayıcısını indirme, yükleme ve çalıştırma hakkında bilgi edineceksiniz.

Language Understanding (LUSıS) kapsayıcısı, eğitilen veya yayımlanmış Language Understanding modelinizi yükler. Bir [lusıs uygulaması](https://www.luis.ai)olarak Docker kapsayıcısı, kapsayıcının API uç noktalarından alınan sorgu tahminlere erişim sağlar. Sorgudan sorgu günlüklerini toplayabilir ve uygulamanın tahmin doğruluğunu artırmak için bunları Language Understanding uygulamasına geri yükleyebilirsiniz.

Aşağıdaki videoda Bu kapsayıcının kullanımı gösterilmektedir.

[![Bilişsel hizmetler için kapsayıcı gösterimi](./media/luis-container-how-to/luis-containers-demo-video-still.png)](https://aka.ms/luis-container-demo)

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/cognitive-services/) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

Lusıs kapsayıcısını çalıştırmak için aşağıdaki önkoşullara göz önünde bulunur:

|Gerekli|Amaç|
|--|--|
|Docker altyapısı| Bir [ana bilgisayarda](#the-host-computer)Docker altyapısının yüklü olması gerekir. Docker, [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) ve [Linux](https://docs.docker.com/engine/installation/#supported-platforms) üzerinde Docker ortamını yapılandıran paketler sağlar. Docker ve kapsayıcı temel bilgileri ile ilgili giriş yapmak için [Docker’a genel bakış](https://docs.docker.com/engine/docker-overview/) bölümüne bakın.<br><br> Kapsayıcıların Azure 'a bağlanıp faturalandırma verilerini göndermesini sağlamak için Docker yapılandırılmalıdır. <br><br> **Windows 'da** Docker 'ın de Linux kapsayıcılarını destekleyecek şekilde yapılandırılması gerekir.<br><br>|
|Docker ile benzerlik | Kayıt defterleri, depolar, kapsayıcılar ve kapsayıcı görüntüleri gibi Docker kavramlarının yanı sıra temel komutlar hakkında bilgi sahibi olmanız gerekir `docker` .|
|Azure `Cognitive Services` Resource ve lusıs [paketlenmiş uygulama](luis-how-to-start-new-app.md) dosyası |Kapsayıcısını kullanabilmeniz için şunları yapmanız gerekir:<br><br>* Bilişsel _Hizmetler_ Azure kaynağı ve ilgili faturalandırma anahtarı faturalandırma uç noktası URI 'si. Her iki değer de kaynak için genel bakış ve anahtarlar sayfalarında bulunur ve kapsayıcıyı başlatmak için gereklidir. <br>* İlişkili uygulama KIMLIĞIYLE kapsayıcıya bağlı giriş olarak paketlenmiş eğitilen veya yayımlanmış bir uygulama. Paketlenmiş dosyayı LUıS portalından veya yazma API 'Lerinden alabilirsiniz. [Yazma API 'lerinden](#authoring-apis-for-package-file)LUIS paketlenmiş uygulama alıyorsanız, _yazma anahtarınıza_ de ihtiyacınız olacaktır.<br><br>Bu gereksinimler komut satırı bağımsız değişkenlerini aşağıdaki değişkenlere iletmek için kullanılır:<br><br>**{AUTHORING_KEY}**: Bu anahtar, paketteki lusıs hizmetinden paketlenmiş uygulamayı almak ve sorgu günlüklerini buluta geri yüklemek için kullanılır. Biçim `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` .<br><br>**{APP_ID}**: Bu kimlik, uygulamayı seçmek için kullanılır. Biçim `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` .<br><br>**{API_KEY}**: Bu anahtar kapsayıcıyı başlatmak için kullanılır. Uç nokta anahtarını iki yerde bulabilirsiniz. Birincisi, bilişsel _Hizmetler_ kaynağının anahtar listesi içindeki Azure Portal. Uç nokta anahtarı, anahtarlar ve uç nokta ayarları sayfasındaki LUO portalında da kullanılabilir. Başlangıç anahtarını kullanmayın.<br><br>**{ENDPOINT_URI}**: Genel Bakış sayfasında belirtilen bitiş noktası.<br><br>[Yazma anahtarı ve uç nokta anahtarının](luis-limits.md#key-limits) farklı amaçları vardır. Bunları birbirlerinin yerine kullanmayın. |

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="authoring-apis-for-package-file"></a>Paket dosyası için yazma API 'Leri

Paketlenmiş uygulamalar için yazma API 'Leri:

* [Yayımlanmış paket API 'SI](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagepublishedapplicationasgzip)
* [Yayımlanmamış, yalnızca eğitilen paket API 'SI](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagetrainedapplicationasgzip)

### <a name="the-host-computer"></a>Ana bilgisayar

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Kapsayıcı gereksinimleri ve önerileri

Aşağıdaki tabloda kapsayıcı ana bilgisayarı için en düşük ve önerilen değerler listelenmiştir. Gereksinimleriniz, trafik hacmine bağlı olarak değişebilir.

|Kapsayıcı| Minimum | Önerilen | TPS<br>(En düşük, en yüksek)|
|-----------|---------|-------------|--|
|LUIS|1 çekirdek, 2 GB bellek|1 çekirdek, 4 GB bellek|20, 40|

* Her çekirdek en az 2,6 gigahertz (GHz) veya daha hızlı olmalıdır.
* TPS-saniye başına işlem

Çekirdek ve bellek, `--cpus` `--memory` komutunun bir parçası olarak kullanılan ve ayarlarına karşılık gelir `docker run` .

## <a name="get-the-container-image-with-docker-pull"></a>Kapsayıcı görüntüsünü al `docker pull`

[`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/)Depodan bir kapsayıcı görüntüsü indirmek için komutunu kullanın `mcr.microsoft.com/azure-cognitive-services/language/luis` :

```
docker pull mcr.microsoft.com/azure-cognitive-services/language/luis:latest
```

Yukarıdaki komutta kullanılan gibi kullanılabilir etiketlerin tam bir açıklaması için `latest` bkz. Docker Hub üzerinde [Luis](https://go.microsoft.com/fwlink/?linkid=2043204) .

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="how-to-use-the-container"></a>Kapsayıcıyı kullanma

Kapsayıcı [ana bilgisayardan](#the-host-computer)olduktan sonra, kapsayıcında çalışmak için aşağıdaki işlemi kullanın.

![Language Understanding (LUSıS) kapsayıcısını kullanma işlemi](./media/luis-container-how-to/luis-flow-with-containers-diagram.jpg)

1. LUıS portalından veya LUıS API 'Lerinden kapsayıcı için [paket dışarı aktarma](#export-packaged-app-from-luis) .
1. Paket dosyasını, [ana bilgisayarda](#the-host-computer)gerekli **giriş** dizinine taşıyın. LUSıS paket dosyasını yeniden adlandırmayın, değiştirmeyin, üzerine yazmaz veya sıkıştırmasını açın.
1. [Kapsayıcıyı](#run-the-container-with-docker-run), gerekli _giriş bağlama_ ve faturalandırma ayarlarıyla çalıştırın. Komuta [](luis-container-configuration.md#example-docker-run-commands) daha fazla örnek `docker run` kullanılabilir.
1. [Kapsayıcının tahmin uç noktası sorgulanıyor](#query-the-containers-prediction-endpoint).
1. Kapsayıcı ile işiniz bittiğinde, Halu portalındaki çıkış bağlamasından [uç nokta günlüklerini içeri aktarın](#import-the-endpoint-logs-for-active-learning) ve kapsayıcıyı [durdurun](#stop-the-container) .
1. Uygulamayı geliştirmek için, **Gözden geçirme uç noktası sıralayıcısı** sayfasında Luo portalının [etkin öğrenimini](luis-how-to-review-endpoint-utterances.md) kullanın.

Kapsayıcıda çalışan uygulama değiştirilemez. Kapsayıcıda uygulamayı değiştirmek için [, LUIS portalını kullanarak](https://www.luis.ai) LUIS hizmetinde uygulamayı DEĞIŞTIRMENIZ veya LUIS [yazma API 'lerini](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f)kullanmanız gerekir. Daha sonra eğitme ve/veya Yayımla, sonra yeni bir paket indirip kapsayıcıyı yeniden çalıştırın.

Kapsayıcının içindeki LUO uygulaması, LUSıS hizmetine geri verilemez. Yalnızca sorgu günlükleri karşıya yüklenebilir.

## <a name="export-packaged-app-from-luis"></a>Paketlenmiş uygulamayı LUSıS 'den dışarı aktarma

LUSıS kapsayıcısı, Kullanıcı sorusunun tahmin sorgularını yanıtlamak için eğitilen veya yayınlanmış bir LUO uygulaması gerektirir. LUıN uygulamasını almak için, eğitilen veya yayımlanmış paket API 'sini kullanın.

Varsayılan konum, `input` komutu çalıştırdığınız konuma göre alt dizindir `docker run` .

Paket dosyasını bir dizine yerleştirin ve Docker kapsayıcısını çalıştırdığınızda giriş bağlama olarak bu dizine başvurun.

### <a name="package-types"></a>Paket türleri

Giriş bağlama dizini, uygulamanın **Üretim**, **hazırlama** ve **Sürümlenmiş** modellerini eşzamanlı olarak içerebilir. Tüm paketler bağlanır.

|Paket türü|Sorgu uç noktası API 'SI|Sorgu kullanılabilirliği|Paket dosya adı biçimi|
|--|--|--|--|
|Sürümü|AL, POSTALA|Yalnızca kapsayıcı|`{APP_ID}_v{APP_VERSION}.gz`|
|Hazırlama|AL, POSTALA|Azure ve kapsayıcı|`{APP_ID}_STAGING.gz`|
|Üretim|AL, POSTALA|Azure ve kapsayıcı|`{APP_ID}_PRODUCTION.gz`|

> [!IMPORTANT]
> LUSıS paket dosyalarını yeniden adlandırmayın, değiştirmeyin, üzerine yazmaz veya sıkıştırmasını açın.

### <a name="packaging-prerequisites"></a>Paketleme önkoşulları

Bir LUSıS uygulamasını paketlemeden önce aşağıdakilere sahip olmanız gerekir:

|Paketleme gereksinimleri|Ayrıntılar|
|--|--|
|Azure bilişsel _Hizmetler_ kaynak örneği|Desteklenen bölgeler şunlardır<br><br>Batı ABD ( `westus` )<br>Batı Avrupa ( `westeurope` )<br>Avustralya Doğu ( `australiaeast` )|
|Eğitilen veya yayınlanan LUO uygulaması|, [Desteklenmeyen bağımlılıklar][unsupported-dependencies]yok. |
|[Ana bilgisayarın](#the-host-computer)dosya sistemine erişim |Ana bilgisayar bir [giriş bağlamaya](luis-container-configuration.md#mount-settings)izin vermelidir.|

### <a name="export-app-package-from-luis-portal"></a>Uygulama paketini LUSıS portalından dışarı aktarma

Lua [portalı](https://www.luis.ai) , eğitilen veya yayınlanan uygulamanın paketini dışarı aktarma olanağını sağlar.

### <a name="export-published-apps-package-from-luis-portal"></a>Yayınlanan uygulamanın paketini LUSıS portalından dışarı aktar

Yayınlanan uygulamanın paketine, **uygulamalar** listesi sayfasından ulaşılabilir.

1. LUI [portalında](https://www.luis.ai)oturum açın.
1. Listedeki uygulama adının solundaki onay kutusunu seçin.
1. Listenin üzerindeki bağlam araç çubuğundan **dışarı aktarma** öğesini seçin.
1. **Kapsayıcı (GZIP) Için dışarı aktar**' ı seçin.
1. **Üretim yuvası** veya **hazırlama yuvası** ortamını seçin.
1. Paket tarayıcıdan indirilir.

![Uygulama sayfasının dışarı aktarma menüsünden, kapsayıcı için yayımlanmış paketi dışarı aktarma](./media/luis-container-how-to/export-published-package-for-container.png)

### <a name="export-versioned-apps-package-from-luis-portal"></a>Sürümü tutulan uygulamanın paketini LUSıS portalından dışarı aktar

Sürümlenmiş uygulamanın paketine **sürümler** listesi sayfasından ulaşılabilir.

1. LUI [portalında](https://www.luis.ai)oturum açın.
1. Listeden uygulamaya tıklayın.
1. Uygulamanın gezinti çubuğunda **Yönet** ' i seçin.
1. Sol gezinti çubuğundaki **sürümler** ' i seçin.
1. Listedeki sürüm adının solundaki onay kutusunu seçin.
1. Listenin üzerindeki bağlam araç çubuğundan **dışarı aktarma** öğesini seçin.
1. **Kapsayıcı (GZIP) Için dışarı aktar**' ı seçin.
1. Paket tarayıcıdan indirilir.

![Sürümler sayfasının dışa aktarma menüsünde, kapsayıcı için eğitilen paketi dışarı aktarma](./media/luis-container-how-to/export-trained-package-for-container.png)

### <a name="export-published-apps-package-from-api"></a>Yayınlanan uygulamanın paketini API 'den dışarı aktar

Zaten [yayımlamış](luis-how-to-publish-app.md)olduğunuz bir Luo uygulamasını paketlemek için aşağıdaki REST API yöntemini kullanın. HTTP belirtiminin altındaki tabloyu kullanarak API çağrısındaki yer tutucular için kendi uygun değerlerinizi değiştirme.

```http
GET /luis/api/v2.0/package/{APP_ID}/slot/{SLOT_NAME}/gzip HTTP/1.1
Host: {AZURE_REGION}.api.cognitive.microsoft.com
Ocp-Apim-Subscription-Key: {AUTHORING_KEY}
```

| Yer tutucu | Değer |
|-------------|-------|
| **{APP_ID}** | Yayınlanan LUSıS uygulamasının uygulama KIMLIĞI. |
| **{SLOT_NAME}** | Yayınlanan LUSıS uygulamasının ortamı. Aşağıdaki değerlerden birini kullanın:<br/>`PRODUCTION`<br/>`STAGING` |
| **{AUTHORING_KEY}** | Yayımlanan LUO uygulaması için Luo hesabının yazma anahtarı.<br/>Yazma anahtarınızı, Lua portalındaki **Kullanıcı ayarları** sayfasından edinebilirsiniz. |
| **{AZURE_REGION}** | Uygun Azure bölgesi:<br/><br/>`westus` -Batı ABD<br/>`westeurope` -Batı Avrupa<br/>`australiaeast` -Avustralya Doğu |

Yayınlanan paketi indirmek için [buradaki API belgelerine][download-published-package]başvurun. Başarılı bir şekilde indirildiyse, yanıt bir LUSıS paket dosyasıdır. Dosyayı kapsayıcının giriş bağlaması için belirtilen depolama konumuna kaydedin.

### <a name="export-versioned-apps-package-from-api"></a>Sürümlü uygulamanın paketini API 'den dışarı aktar

Zaten [eğitilen](luis-how-to-train.md)bir Luo uygulamasını paketlemek için aşağıdaki REST API yöntemini kullanın. HTTP belirtiminin altındaki tabloyu kullanarak API çağrısındaki yer tutucular için kendi uygun değerlerinizi değiştirme.

```http
GET /luis/api/v2.0/package/{APP_ID}/versions/{APP_VERSION}/gzip HTTP/1.1
Host: {AZURE_REGION}.api.cognitive.microsoft.com
Ocp-Apim-Subscription-Key: {AUTHORING_KEY}
```

| Yer tutucu | Değer |
|-------------|-------|
| **{APP_ID}** | Eğitilen LUO uygulamasının uygulama KIMLIĞI. |
| **{APP_VERSION}** | Eğitilen LUIN uygulamasının uygulama sürümü. |
| **{AUTHORING_KEY}** | Yayımlanan LUO uygulaması için Luo hesabının yazma anahtarı.<br/>Yazma anahtarınızı, Lua portalındaki **Kullanıcı ayarları** sayfasından edinebilirsiniz. |
| **{AZURE_REGION}** | Uygun Azure bölgesi:<br/><br/>`westus` -Batı ABD<br/>`westeurope` -Batı Avrupa<br/>`australiaeast` -Avustralya Doğu |

Sürümlenmiş paketi indirmek için [buradaki API belgelerine][download-versioned-package]başvurun. Başarılı bir şekilde indirildiyse, yanıt bir LUSıS paket dosyasıdır. Dosyayı kapsayıcının giriş bağlaması için belirtilen depolama konumuna kaydedin.

## <a name="run-the-container-with-docker-run"></a>Kapsayıcıyı ile çalıştırma `docker run`

Kapsayıcıyı çalıştırmak için [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) komutunu kullanın. Ve değerlerini alma hakkında ayrıntılar için [gerekli parametreleri toplama](#gathering-required-parameters) bölümüne bakın `{ENDPOINT_URI}` `{API_KEY}` .

Komut [örnekleri](luis-container-configuration.md#example-docker-run-commands) `docker run` mevcuttur.

```console
docker run --rm -it -p 5000:5000 ^
--memory 4g ^
--cpus 2 ^
--mount type=bind,src=c:\input,target=/input ^
--mount type=bind,src=c:\output\,target=/output ^
mcr.microsoft.com/azure-cognitive-services/language/luis ^
Eula=accept ^
Billing={ENDPOINT_URI} ^
ApiKey={API_KEY}
```

* Bu örnek, `C:` Windows 'da herhangi bir izin çakışmasını önlemek için dizini sürücüden kullanır. Giriş dizini olarak belirli bir dizin kullanmanız gerekiyorsa, Docker hizmeti iznini vermeniz gerekebilir.
* Docker Kapsayıcıları hakkında bilginiz yoksa bağımsız değişkenlerin sırasını değiştirmeyin.
* Farklı bir işletim sistemi kullanıyorsanız, sistem için doğru konsol/Terminal, bağlama için klasör söz dizimi ve satır devamlılık karakteri kullanın. Bu örneklerde bir Windows konsolunun satır devamlılık karakteriyle birlikte varsayılmaktadır `^` . Kapsayıcı bir Linux işletim sistemi olduğundan, hedef bağlama bir Linux stili klasör söz dizimini kullanır.

Şu komut:

* LUSıS kapsayıcı görüntüsünden bir kapsayıcı çalıştırır
* Kapsayıcı konağında bulunan *c:\ınput* konumundaki giriş BAĞLAMASıNDAN Luo uygulamasını yükler
* İki CPU çekirdeği ve 4 gigabayt (GB) bellek ayırır
* TCP bağlantı noktası 5000 ' i kullanıma sunar ve kapsayıcı için bir sözde TTY ayırır
* Kapsayıcı ana bilgisayarında bulunan *c:\Output* konumundaki çıkış bağlamasındaki KAPSAYıCıYı ve Lua günlüklerini kaydeder
* Kapsayıcıyı çıktıktan sonra otomatik olarak kaldırır. Kapsayıcı görüntüsü hala ana bilgisayarda kullanılabilir.

Komuta [](luis-container-configuration.md#example-docker-run-commands) daha fazla örnek `docker run` kullanılabilir.

> [!IMPORTANT]
> `Eula` `Billing` `ApiKey` Kapsayıcıyı çalıştırmak için, ve seçenekleri belirtilmelidir; Aksi takdirde kapsayıcı başlatılmaz.  Daha fazla bilgi için bkz. [faturalandırma](#billing).
> ApiKey değeri, LUO portalındaki **Azure kaynakları** sayfasının **anahtarıdır** ve Azure `Cognitive Services` kaynak anahtarları sayfasında da kullanılabilir.

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

## <a name="endpoint-apis-supported-by-the-container"></a>Kapsayıcı tarafından desteklenen uç nokta API 'Leri

Kapsayıcı ile birlikte API 'nin v2 ve [v3](luis-migration-api-v3.md) sürümleri mevcuttur.

## <a name="query-the-containers-prediction-endpoint"></a>Kapsayıcının tahmin uç noktasını sorgulama

Kapsayıcı REST tabanlı sorgu tahmin uç noktası API’lerini sağlar. Yayımlanan (hazırlama veya üretim) uygulamalarının uç noktaları, sürümlü uygulamalar için uç noktalardan _farklı_ bir rotaya sahiptir.

Kapsayıcı API’leri için `http://localhost:5000` konağını kullanın.

# <a name="v3-prediction-endpoint"></a>[V3 tahmin uç noktası](#tab/v3)

|Paket türü|HTTP fiili|Yol|Sorgu parametreleri|
|--|--|--|--|
|Yayımlanmış|AL, POSTALA|`/luis/v3.0/apps/{appId}/slots/{slotName}/predict?`|`query={query}`<br>[`&verbose`]<br>[`&log`]<br>[`&show-all-intents`]|
|Sürümü|AL, POSTALA|`/luis/v3.0/apps/{appId}/versions/{versionId}/predict?`|`query={query}`<br>[`&verbose`]<br>[`&log`]<br>[`&show-all-intents`]|

Sorgu parametreleri, sorgu yanıtında nasıl ve neyin döndürüleceğini yapılandırır:

|Sorgu parametresi|Tür|Amaç|
|--|--|--|
|`query`|string|Kullanıcının utterliği.|
|`verbose`|boolean|Tahmin edilen modeller için tüm meta verilerin döndürülüp döndürülmeyeceğini belirten bir Boole değeri. Varsayılan değer false’tur.|
|`log`|boolean|Daha sonra [etkin öğrenme](luis-how-to-review-endpoint-utterances.md)için kullanılabilen sorguları günlüğe kaydeder. Varsayılan değer false’tur.|
|`show-all-intents`|boolean|Yalnızca tüm amaçlar veya en üst Puanlama hedefinin döndürülüp döndürülmeyeceğini belirten bir Boole değeri. Varsayılan değer false’tur.|

# <a name="v2-prediction-endpoint"></a>[V2 tahmin uç noktası](#tab/v2)

|Paket türü|HTTP fiili|Yol|Sorgu parametreleri|
|--|--|--|--|
|Yayımlanmış|[Al](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78), [Postala](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee79)|`/luis/v2.0/apps/{appId}?`|`q={q}`<br>`&staging`<br>[`&timezoneOffset`]<br>[`&verbose`]<br>[`&log`]<br>|
|Sürümü|AL, POSTALA|`/luis/v2.0/apps/{appId}/versions/{versionId}?`|`q={q}`<br>[`&timezoneOffset`]<br>[`&verbose`]<br>[`&log`]|

Sorgu parametreleri, sorgu yanıtında nasıl ve neyin döndürüleceğini yapılandırır:

|Sorgu parametresi|Tür|Amaç|
|--|--|--|
|`q`|string|Kullanıcının utterliği.|
|`timezoneOffset`|sayı|Timezonekayması, önceden oluşturulmuş varlık datetimeV2 tarafından kullanılan [saat dilimini değiştirmenize](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity) izin verir.|
|`verbose`|boolean|Doğru olarak ayarlandığında tüm hedefleri ve puanlarını döndürür. Varsayılan değer, yalnızca en üst amacı döndüren false ' dır.|
|`staging`|boolean|True olarak ayarlanırsa, hazırlama ortamı sonuçlarından sorgu döndürür. |
|`log`|boolean|Daha sonra [etkin öğrenme](luis-how-to-review-endpoint-utterances.md)için kullanılabilen sorguları günlüğe kaydeder. Varsayılan değer doğru değeridir.|

***

### <a name="query-the-luis-app"></a>LUSıS uygulamasını sorgulama

Yayımlanmış bir uygulama için kapsayıcıyı sorgulamak üzere örnek bir KıVRıMLı komutu:

# <a name="v3-prediction-endpoint"></a>[V3 tahmin uç noktası](#tab/v3)

Bir yuvada bir modeli sorgulamak için aşağıdaki API 'yi kullanın:

```bash
curl -G \
-d verbose=false \
-d log=true \
--data-urlencode "query=turn the lights on" \
"http://localhost:5000/luis/v3.0/apps/{APP_ID}/slots/production/predict"
```

**Hazırlama** ortamına sorgu yapmak için, `production` rotada şunu ile değiştirin `staging` :

`http://localhost:5000/luis/v3.0/apps/{APP_ID}/slots/staging/predict`

Sürümlü bir modeli sorgulamak için aşağıdaki API 'yi kullanın:

```bash
curl -G \
-d verbose=false \
-d log=false \
--data-urlencode "query=turn the lights on" \
"http://localhost:5000/luis/v3.0/apps/{APP_ID}/versions/{APP_VERSION}/predict"
```

# <a name="v2-prediction-endpoint"></a>[V2 tahmin uç noktası](#tab/v2)

Bir yuvada bir modeli sorgulamak için aşağıdaki API 'yi kullanın:

```bash
curl -X GET \
"http://localhost:5000/luis/v2.0/apps/{APP_ID}?q=turn%20on%20the%20lights&staging=false&timezoneOffset=0&verbose=false&log=true" \
-H "accept: application/json"
```
**Hazırlama** ortamına sorgu yapmak için, **hazırlama** sorgu dizesi parametre değerini doğru olarak değiştirin:

`staging=true`

Sürümlü bir modeli sorgulamak için aşağıdaki API 'yi kullanın:

```bash
curl -X GET \
"http://localhost:5000/luis/v2.0/apps/{APP_ID}/versions/{APP_VERSION}?q=turn%20on%20the%20lights&timezoneOffset=0&verbose=false&log=true" \
-H "accept: application/json"
```
Sürüm adı en fazla 10 karakterden oluşur ve yalnızca bir URL 'de izin verilen karakterleri içerir.

***

## <a name="import-the-endpoint-logs-for-active-learning"></a>Etkin öğrenme için uç nokta günlüklerini içeri aktarma

LUSıS kapsayıcısı için bir çıkış bağlaması belirtilmişse, uygulama sorgusu günlük dosyaları çıkış dizinine kaydedilir; burada `{INSTANCE_ID}` KAPSAYıCı kimliği olur. Uygulama sorgu günlüğü, LUO kapsayıcısına gönderilen her bir tahmin sorgusunun sorgusunu, yanıtını ve zaman damgalarını içerir.

Aşağıdaki konum kapsayıcının günlük dosyaları için iç içe dizin yapısını gösterir.
```
/output/luis/{INSTANCE_ID}/
```

LUU portalından, uygulamanızı seçin ve ardından bu günlükleri karşıya yüklemek için **uç nokta günlüklerini Içeri aktar** ' ı seçin.

![Etkin öğrenme için kapsayıcının günlük dosyalarını içeri aktar](./media/luis-container-how-to/upload-endpoint-log-files.png)

Günlük karşıya yüklendikten sonra, LUı portalındaki uç nokta utslerini [gözden geçirin](./luis-concept-review-endpoint-utterances.md) .

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Kapsayıcıyı durdurma

Kapsayıcıyı kapatmak için kapsayıcının çalıştığı komut satırı ortamında **CTRL + C** tuşlarına basın.

## <a name="troubleshooting"></a>Sorun giderme

Kapsayıcıyı bir çıkış [bağlaması](luis-container-configuration.md#mount-settings) ve günlüğü etkin olarak çalıştırırsanız kapsayıcı, kapsayıcıyı başlatırken veya çalıştırırken oluşan sorunları gidermek için yararlı olan günlük dosyaları oluşturur.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Faturalandırma

LUSıS kapsayıcısı, Azure hesabınızdaki bilişsel _Hizmetler_ kaynağını kullanarak faturalandırma bilgilerini Azure 'a gönderir.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Bu seçenekler hakkında daha fazla bilgi için bkz. [kapsayıcıları yapılandırma](luis-container-configuration.md).

## <a name="summary"></a>Özet

Bu makalede, Language Understanding (LUSıS) kapsayıcıları indirmek, yüklemek ve çalıştırmak için kavramları ve iş akışını öğrendiniz. Özet:

* Language Understanding (LUSıS), Docker için uç nokta sorgu tahminleri sağlayan bir Linux kapsayıcısı sağlar.
* Kapsayıcı görüntüleri Microsoft Container Registry (MCR) ile indirilir.
* Kapsayıcı görüntüleri Docker 'da çalışır.
* Kapsayıcının ana bilgisayar URI 'sini belirterek kapsayıcı uç noktalarını sorgulamak için REST API kullanabilirsiniz.
* Bir kapsayıcıyı örnekledikten sonra faturalandırma bilgilerini belirtmeniz gerekir.

> [!IMPORTANT]
> Bilişsel hizmetler kapsayıcıları, ölçüm için Azure 'a bağlı kalmadan çalıştırılmak üzere lisanslanmaz. Müşterilerin, ödeme bilgilerini her zaman ölçüm hizmetiyle iletişimine olanak tanımak için kapsayıcıların etkinleştirilmesi gerekir. Bilişsel hizmetler kapsayıcıları, müşteri verilerini (örneğin, çözümlenmekte olan resim veya metin) Microsoft 'a göndermez.

## <a name="next-steps"></a>Sonraki adımlar

* Yapılandırma ayarları için [kapsayıcıları yapılandırma](luis-container-configuration.md) konusunu gözden geçirin.
* Bilinen yetenek kısıtlamaları için [lusıs kapsayıcısı kısıtlamalarına](luis-container-limitations.md) bakın.
* LUSıS işlevleriyle ilgili sorunları çözmek için [sorun giderme](troubleshooting.md) bölümüne bakın.
* Daha fazla bilişsel [Hizmetler kapsayıcısı](../cognitive-services-container-support.md) kullanın

<!-- Links - external -->
[download-published-package]: https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagepublishedapplicationasgzip
[download-versioned-package]: https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagetrainedapplicationasgzip

[unsupported-dependencies]: luis-container-limitations.md#unsupported-dependencies-for-latest-container