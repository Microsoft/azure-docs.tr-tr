---
title: Uzun ses API 'SI-konuşma hizmeti
titleSuffix: Azure Cognitive Services
description: Uzun ses API 'sinin zaman uyumsuz senkron metin okuma için nasıl tasarlandığına öğrenin.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 08/11/2020
ms.author: trbye
ms.openlocfilehash: 65c0d80394317c2b2bfbf621d3cc2ad0c2e3448a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102618415"
---
# <a name="long-audio-api"></a>Uzun ses API 'SI

Uzun ses API 'SI, zaman uyumsuz birleştirme için tasarlanan uzun biçimli metin (örneğin, ses kitapları, haber makaleleri ve belgeler) için tasarlanmıştır. Bu API gerçek zamanlı olarak birleştirilmiş ses döndürmez, bunun yerine beklentiler, yanıtları yoklayacaksınız ve çıkışta hizmetten kullanılabilir hale getirildiklerinden çıktıyı tüketebilir. Konuşma SDK 'Sı tarafından kullanılan metin okuma API 'sinden farklı olarak, uzun ses API 'SI 10 dakikadan daha uzun bir yığın oluşturabilir, böylece bir toplu iş içindeki ses kitapları gibi uzun ses içeriği oluşturmak için yayımcılar ve ses içeriği platformları için ideal hale getirebilirsiniz.

Uzun ses API 'sinin ek avantajları:

* Hizmet tarafından döndürülen sentezlenmiş konuşma en iyi sinir sesleri kullanır.
* Sesleri hiçbiri gerçek zamanlı toplu iş modunda birleştirdiğinden ses uç noktası dağıtmanız gerekmez.

> [!NOTE]
> Uzun ses API 'SI artık [genel sinir seslerini](./language-support.md#neural-voices) ve [özel sinir seslerini](./how-to-custom-voice.md#custom-neural-voices)desteklemektedir.

## <a name="workflow"></a>İş akışı

Genellikle, uzun ses API 'SI kullanılırken, birleştirme için bir metin dosyası ya da dosya gönderir, durum için yoklama yapılır, ardından durum başarılı olursa ses çıkışını indirebilirsiniz.

Bu diyagram, iş akışına üst düzey bir genel bakış sağlar.

![Uzun ses API iş akışı diyagramı](media/long-audio-api/long-audio-api-workflow.png)

## <a name="prepare-content-for-synthesis"></a>Birleştirme için içerik hazırlama

Metin dosyanızı hazırlarken şunları yaptığınızdan emin olun:

* Düz metin (. txt) ya da SSML metni (. txt)
* , [Bayt sıra Işaretiyle UTF-8 olarak kodlanır (BOM)](https://www.w3.org/International/questions/qa-utf8-bom.en#bom)
* ZIP değil tek bir dosyadır
* SSML metni için düz metin veya 400 [faturalanabilir karakter](./text-to-speech.md#pricing-note) için 400 karakterden daha uzun ve 10.000 ' den az paragraf içeriyor
  * Düz metin için, her paragraf **Enter/Return** -View [düz metin girişi örneğine](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/en-US.txt) vurarak ayrılır
  * SSML metninde her SSML parçası bir paragraf olarak değerlendirilir. SSML parçaları farklı paragraflar ile ayrılmalıdır- [SSML metin girişi örneğini](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/SSMLTextInputSample.txt) görüntüleyin

## <a name="sample-code"></a>Örnek kod
Bu sayfanın geri kalanı Python 'a odaklanacaktır, ancak şu programlama dilleri için GitHub 'da uzun ses API 'SI için örnek kod mevcuttur:

* [Örnek kod: Python](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/Python)
* [Örnek kod: C #](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/CSharp)
* [Örnek kod: Java](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/)

## <a name="python-example"></a>Python örneği

Bu bölüm, uzun ses API 'sinin temel kullanımını gösteren Python örnekleri içerir. Favori IDE ortamınızda veya düzenleyicide yeni bir Python projesi oluşturun. Sonra bu kod parçacığını adlı bir dosyaya kopyalayın `long_audio_synthesis_client.py` .

```python
import json
import ntpath
import requests
```

Bu kitaplıklar, HTTP isteğini oluşturmak için kullanılır ve metin okuma uzun ses senku REST API.

### <a name="get-a-list-of-supported-voices"></a>Desteklenen seslerin listesini al

Desteklenen seslerin listesini almak için öğesine bir GET isteği gönderin `https://<endpoint>/api/texttospeech/v3.0/longaudiosynthesis/voices` .


Bu kod, kullanabileceğiniz belirli bir bölgeye/uç noktaya ait seslerin tam listesini almanızı sağlar.
```python
def get_voices():
    region = '<region>'
    key = '<your_key>'
    url = 'https://{}.customvoice.api.speech.microsoft.com/api/texttospeech/v3.0/longaudiosynthesis/voices'.format(region)
    header = {
        'Ocp-Apim-Subscription-Key': key
    }

    response = requests.get(url, headers=header)
    print(response.text)

get_voices()
```

Aşağıdaki değerleri değiştirin:

* `<your_key>`Konuşma hizmeti abonelik anahtarınızla değiştirin. Bu bilgiler, [Azure Portal](https://aka.ms/azureportal)kaynağınızın **genel bakış** sekmesinde bulunabilir.
* `<region>`Konuşma kaynağınızın oluşturulduğu bölgeyle değiştirin (örneğin: `eastus` veya `westus` ). Bu bilgiler, [Azure Portal](https://aka.ms/azureportal)kaynağınızın **genel bakış** sekmesinde bulunabilir.

Aşağıdakine benzer bir çıktı görürsünüz:

```console
{
  "values": [
    {
      "locale": "en-US",
      "voiceName": "en-US-AriaNeural",
      "description": "",
      "gender": "Female",
      "createdDateTime": "2020-05-21T05:57:39.123Z",
      "properties": {
        "publicAvailable": true
      }
    },
    {
      "id": "8fafd8cd-5f95-4a27-a0ce-59260f873141"
      "locale": "en-US",
      "voiceName": "my custom neural voice",
      "description": "",
      "gender": "Male",
      "createdDateTime": "2020-05-21T05:25:40.243Z",
      "properties": {
        "publicAvailable": false
      }
    }
  ]
}
```

**Properties. publicAvailable** değeri **true** ise, Voice ortak bir sinir sestir. Aksi takdirde, özel bir sinir sestir.

### <a name="convert-text-to-speech"></a>Metni konuşmaya Dönüştür

Bir giriş metin dosyasını düz metin veya SSML metninde hazırlayın ve ardından aşağıdaki kodu ekleyin `long_audio_synthesis_client.py` :

> [!NOTE]
> `concatenateResult` isteğe bağlı bir parametredir. Bu parametre ayarlanmamışsa, her paragraf için ses çıkışları oluşturulacaktır. Ayrıca, parametresini ayarlayarak sesos 'yi 1 çıkışa ekleyebilirsiniz. 
> `outputFormat` Ayrıca isteğe bağlıdır. Varsayılan olarak, Ses çıktısı Riff-16khz-16bit-mono-PCM olarak ayarlanır. Desteklenen ses çıkış biçimleri hakkında daha fazla bilgi için bkz. [ses çıkış biçimleri](#audio-output-formats).

```python
def submit_synthesis():
    region = '<region>'
    key = '<your_key>'
    input_file_path = '<input_file_path>'
    locale = '<locale>'
    url = 'https://{}.customvoice.api.speech.microsoft.com/api/texttospeech/v3.0/longaudiosynthesis'.format(region)
    header = {
        'Ocp-Apim-Subscription-Key': key
    }

    voice_identities = [
        {
            'voicename': '<voice_name>'
        }
    ]

    payload = {
        'displayname': 'long audio synthesis sample',
        'description': 'sample description',
        'locale': locale,
        'voices': json.dumps(voice_identities),
        'outputformat': 'riff-16khz-16bit-mono-pcm',
        'concatenateresult': True,
    }

    filename = ntpath.basename(input_file_path)
    files = {
        'script': (filename, open(input_file_path, 'rb'), 'text/plain')
    }

    response = requests.post(url, payload, headers=header, files=files)
    print('response.status_code: %d' % response.status_code)
    print(response.headers['Location'])

submit_synthesis()
```

Aşağıdaki değerleri değiştirin:

* `<your_key>`Konuşma hizmeti abonelik anahtarınızla değiştirin. Bu bilgiler, [Azure Portal](https://aka.ms/azureportal)kaynağınızın **genel bakış** sekmesinde bulunabilir.
* `<region>`Konuşma kaynağınızın oluşturulduğu bölgeyle değiştirin (örneğin: `eastus` veya `westus` ). Bu bilgiler, [Azure Portal](https://aka.ms/azureportal)kaynağınızın **genel bakış** sekmesinde bulunabilir.
* `<input_file_path>`Metin okuma için hazırladığınız metin dosyasının yoluyla değiştirin.
* `<locale>`İstenen çıkış yerel ayarıyla değiştirin. Daha fazla bilgi için bkz. [dil desteği](language-support.md#neural-voices).

Önceki uç noktaya yapılan çağrılarınızın döndürdüğü sesden birini kullanın `/voices` .

* Genel sinir Voice kullanıyorsanız, `<voice_name>` istenen çıkış sesiyle değiştirin.
* Özel bir sinir sesi kullanmak için, `voice_identities` değişkeni aşağıdaki ile değiştirin ve `<voice_id>` `id` özel sinir sesinizin ile değiştirin.
```Python
voice_identities = [
    {
        'id': '<voice_id>'
    }
]
```

Aşağıdakine benzer bir çıktı görürsünüz:

```console
response.status_code: 202
https://<endpoint>/api/texttospeech/v3.0/longaudiosynthesis/<guid>
```

> [!NOTE]
> 1 ' den fazla giriş dosyası varsa, birden fazla istek göndermeniz gerekir. Farkında olması gereken bazı sınırlamalar vardır.
> * İstemcinin her bir Azure abonelik hesabı için saniyede en fazla **5** istek göndermesine izin verilir. Sınırlama aştıysa, istemci 429 hata kodu (çok fazla istek) alır. Lütfen saniye başına istek miktarını azaltın.
> * Sunucunun her bir Azure abonelik hesabı için en fazla **120** istek çalıştırmasına ve sıraya alma yapmasına izin verilir. Sınırlama aşıldıysa, sunucu 429 hata kodu (çok fazla istek) döndürür. Lütfen bekleyin ve bazı istekler tamamlanana kadar yeni istek gönderilmesini önleyin.

Çıkışın URL 'SI, istek durumunu almak için kullanılabilir.

### <a name="get-information-of-a-submitted-request"></a>Gönderilen istek hakkında bilgi alın

Gönderilen bir senmu isteğinin durumunu almak için, önceki adımda döndürülen URL 'ye bir GET isteği gönderin.
```Python

def get_synthesis():
    url = '<url>'
    key = '<your_key>'
    header = {
        'Ocp-Apim-Subscription-Key': key
    }
    response = requests.get(url, headers=header)
    print(response.text)

get_synthesis()
```
Çıktı şöyle olacaktır:
```console
response.status_code: 200
{
  "models": [
    {
      "voiceName": "en-US-AriaNeural"
    }
  ],
  "properties": {
    "outputFormat": "riff-16khz-16bit-mono-pcm",
    "concatenateResult": false,
    "totalDuration": "PT5M57.252S",
    "billableCharacterCount": 3048
  },
  "id": "eb3d7a81-ee3e-4e9a-b725-713383e71677",
  "lastActionDateTime": "2021-01-14T11:12:27.240Z",
  "status": "Succeeded",
  "createdDateTime": "2021-01-14T11:11:02.557Z",
  "locale": "en-US",
  "displayName": "long audio synthesis sample",
  "description": "sample description"
}
```

`status`Özelliğinden, bu isteğin durumunu okuyabilirsiniz. İstek `NotStarted` durumundan başlar, sonra olarak değişir `Running` ve son olarak `Succeeded` veya olur `Failed` . Durum durumuna gelinceye kadar bu API 'yi yoklamak için bir döngü kullanabilirsiniz `Succeeded` .

### <a name="download-audio-result"></a>Ses sonucunu indir

Birleştirme isteği başarılı olduktan sonra, GET API çağırarak ses sonucunu indirebilirsiniz `/files` .

```python
def get_files():
    id = '<request_id>'
    region = '<region>'
    key = '<your_key>'
    url = 'https://{}.customvoice.api.speech.microsoft.com/api/texttospeech/v3.0/longaudiosynthesis/{}/files'.format(region, id)
    header = {
        'Ocp-Apim-Subscription-Key': key
    }

    response = requests.get(url, headers=header)
    print('response.status_code: %d' % response.status_code)
    print(response.text)

get_files()
```
`<request_id>`Sonucu indirmek istediğiniz ISTEĞIN kimliğiyle değiştirin. Bu, önceki adımın yanıtında bulunabilir.

Çıktı şöyle olacaktır:
```console
response.status_code: 200
{
  "values": [
    {
      "name": "2779f2aa-4e21-4d13-8afb-6b3104d6661a.txt",
      "kind": "LongAudioSynthesisScript",
      "properties": {
        "size": 4200
      },
      "createdDateTime": "2021-01-14T11:11:02.410Z",
      "links": {
        "contentUrl": "https://customvoice-usw.blob.core.windows.net/artifacts/input.txt?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=e05d8d56-9675-448b-820c-4318ae64c8d5"
      }
    },
    {
      "name": "voicesynthesis_waves.zip",
      "kind": "LongAudioSynthesisResult",
      "properties": {
        "size": 9290000
      },
      "createdDateTime": "2021-01-14T11:12:27.226Z",
      "links": {
        "contentUrl": "https://customvoice-usw.blob.core.windows.net/artifacts/voicesynthesis_waves.zip?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=e05d8d56-9675-448b-820c-4318ae64c8d5"
      }
    }
  ]
}
```
Çıktı 2 dosya bilgilerini içerir. Bu, `"kind": "LongAudioSynthesisScript"` giriş betiğinizin gönderildiği bir. Diğeri, `"kind": "LongAudioSynthesisResult"` Bu isteğin sonucudur.
Sonuç, giriş metninin bir kopyasıyla birlikte oluşturulan ses çıkış dosyalarını içeren zip ' dir.

Her iki dosya da özelliğindeki URL 'den indirilebilir `links.contentUrl` .

### <a name="get-all-synthesis-requests"></a>Tüm sensıs isteklerini al

Aşağıdaki kodla gönderilen tüm isteklerin listesini alabilirsiniz:

```python
def get_synthesis():
    region = '<region>'
    key = '<your_key>'
    url = 'https://{}.customvoice.api.speech.microsoft.com/api/texttospeech/v3.0/longaudiosynthesis/'.format(region)    
    header = {
        'Ocp-Apim-Subscription-Key': key
    }

    response = requests.get(url, headers=header)
    print('response.status_code: %d' % response.status_code)
    print(response.text)

get_synthesis()
```

Çıkış şöyle olacaktır:
```console
response.status_code: 200
{
  "values": [
    {
      "models": [
        {
          "id": "8fafd8cd-5f95-4a27-a0ce-59260f873141",
          "voiceName": "my custom neural voice"
        }
      ],
      "properties": {
        "outputFormat": "riff-16khz-16bit-mono-pcm",
        "concatenateResult": false,
        "totalDuration": "PT1S",
        "billableCharacterCount": 5
      },
      "id": "f9f0bb74-dfa5-423d-95e7-58a5e1479315",
      "lastActionDateTime": "2021-01-05T07:25:42.433Z",
      "status": "Succeeded",
      "createdDateTime": "2021-01-05T07:25:13.600Z",
      "locale": "en-US",
      "displayName": "Long Audio Synthesis",
      "description": "Long audio synthesis sample"
    },
    {
      "models": [
        {
          "voiceName": "en-US-AriaNeural"
        }
      ],
      "properties": {
        "outputFormat": "riff-16khz-16bit-mono-pcm",
        "concatenateResult": false,
        "totalDuration": "PT5M57.252S",
        "billableCharacterCount": 3048
      },
      "id": "eb3d7a81-ee3e-4e9a-b725-713383e71677",
      "lastActionDateTime": "2021-01-14T11:12:27.240Z",
      "status": "Succeeded",
      "createdDateTime": "2021-01-14T11:11:02.557Z",
      "locale": "en-US",
      "displayName": "long audio synthesis sample",
      "description": "sample description"
    }
  ]
}
```

`values` özelliği, sensıs isteklerinin bir listesini içerir. Liste, en fazla 100 sayfa boyutuna sahip sayfalandırılır. 100 ' den fazla istek varsa, `"@nextLink"` sayfalandırılmış listenin bir sonraki sayfasını almak için bir özellik sağlanacaktır.

```console
  "@nextLink": "https://<endpoint>/api/texttospeech/v3.0/longaudiosynthesis/?top=100&skip=100"
```

Ayrıca, URL parametresi sağlayarak sayfa boyutunu özelleştirebilir ve sayıyı atlayabilirsiniz `skip` `top` .

### <a name="remove-previous-requests"></a>Önceki istekleri kaldır

Hizmet, her bir Azure aboneliği hesabı için en fazla **20.000** istek tutar. İstek miktarınız bu sınırlamayı aşarsa, lütfen yenilerini oluşturmadan önce önceki istekleri kaldırın. Mevcut istekleri kaldırmazsanız bir hata bildirimi alırsınız.

Aşağıdaki kod, belirli bir sensıs isteğinin nasıl kaldırılacağını gösterir.
```python
def delete_synthesis():
    id = '<request_id>'
    region = '<region>'
    key = '<your_key>'
    url = 'https://{}.customvoice.api.speech.microsoft.com/api/texttospeech/v3.0/longaudiosynthesis/{}/'.format(region, id)
    header = {
        'Ocp-Apim-Subscription-Key': key
    }

    response = requests.delete(url, headers=header)
    print('response.status_code: %d' % response.status_code)
```

İstek başarıyla kaldırılırsa, yanıt durum kodu HTTP 204 (Içerik yok) olur.

```console
response.status_code: 204
```

> [!NOTE]
> Durumu veya olan istekler `NotStarted` `Running` kaldırılamaz veya silinemez.

Tamamlandı, `long_audio_synthesis_client.py` [GitHub](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Python/voiceclient.py)' da kullanılabilir.

## <a name="http-status-codes"></a>HTTP durum kodu

Aşağıdaki tabloda, REST API HTTP yanıt kodlarının ve iletilerinin ayrıntıları verilmiştir.

| API | HTTP durum kodu | Açıklama | Çözüm |
|-----|------------------|-------------|----------|
| Oluştur | 400 | Ses birleştirme bu bölgede etkin değil. | Konuşma abonelik anahtarını desteklenen bir bölge ile değiştirin. |
|        | 400 | Bu bölge için yalnızca **Standart** konuşma aboneliği geçerlidir. | Konuşma abonelik anahtarını "standart" fiyatlandırma katmanıyla değiştirin. |
|        | 400 | Azure hesabı için 20.000 istek sınırını aşıyor. Lütfen yenilerini göndermeden önce bazı istekleri kaldırın. | Sunucu, her bir Azure hesabı için en fazla 20.000 istek tutar. Yeni bir istek göndermeden önce bazı istekleri silin. |
|        | 400 | Bu model şu ses senlimte kullanılamaz: {ModelId}. | {ModelId} durumunun doğru olduğundan emin olun. |
|        | 400 | İsteğin bölgesi şu modelin bölgesiyle eşleşmiyor: {ModelId}. | {ModelId} bölgesinin, isteğin bölgesiyle eşleştiğinden emin olun. |
|        | 400 | Ses birleştirme yalnızca, bayt sırası işaretleyicisi ile UTF-8 Kodlamadaki metin dosyasını destekler. | Giriş dosyalarının, bayt sırası işaretleyicisi ile UTF-8 kodlaması içinde olduğundan emin olun. |
|        | 400 | Ses senssıs isteğinde yalnızca geçerli SSML girdilerine izin verilir. | Giriş SSML ifadelerinin doğru olduğundan emin olun. |
|        | 400 | Giriş dosyasında {voiceName} ses adı bulunamadı. | SSML ses adı girdisi model KIMLIĞIYLE hizalı değil. |
|        | 400 | Giriş dosyasındaki paragraf sayısı 10.000 ' den az olmalıdır. | Dosyadaki paragraf sayısının 10.000 ' den küçük olduğundan emin olun. |
|        | 400 | Giriş dosyası 400 karakterden uzun olmalıdır. | Giriş dosyanızın 400 karakteri aştığından emin olun. |
|        | 404 | Voice sensııd tanımında belirtilen model bulunamıyor: {ModelId}. | {ModelId} ' ın doğru olduğundan emin olun. |
|        | 429 | Etkin ses sensimi sınırını aşıyor. Lütfen bazı isteklerin bitmesini bekleyin. | Sunucunun her bir Azure hesabı için en fazla 120 istek çalıştırmasına ve sıraya alma yapmasına izin verilir. Lütfen bekleyin ve bazı istekler tamamlanana kadar yeni istek gönderilmesini önleyin. |
| Tümü       | 429 | Çok fazla istek var. | İstemcinin her bir Azure hesabı için saniyede en fazla 5 istek göndermesine izin verilir. Lütfen saniye başına istek miktarını azaltın. |
| Sil    | 400 | Ses birleştirme görevi hala kullanımda. | Yalnızca **Tamamlanan** veya **başarısız** olan istekleri silebilirsiniz. |
| GetById   | 404 | Belirtilen varlık bulunamıyor. | Sen, ID 'nin doğru olduğundan emin olun. |

## <a name="regions-and-endpoints"></a>Bölgeler ve uç noktalar

Uzun ses API 'SI, benzersiz uç noktaları olan birden çok bölgede kullanılabilir.

| Region | Uç Nokta |
|--------|----------|
| Doğu ABD | `https://eastus.customvoice.api.speech.microsoft.com` |
| Hindistan Orta | `https://centralindia.customvoice.api.speech.microsoft.com` |
| Güneydoğu Asya | `https://southeastasia.customvoice.api.speech.microsoft.com` |
| Güney Birleşik Krallık | `https://uksouth.customvoice.api.speech.microsoft.com` |
| West Europe | `https://westeurope.customvoice.api.speech.microsoft.com` |

## <a name="audio-output-formats"></a>Ses çıkış biçimleri

Esnek ses çıkışı biçimlerini destekliyoruz. ' ConcatenateResult ' parametresini ayarlayarak, paragraf başına ses çıkışları oluşturabilir veya ses çıkışlarını tek bir çıkışa birleştirebilirsiniz. Aşağıdaki ses çıkış biçimleri, uzun ses API 'SI tarafından desteklenir:

> [!NOTE]
> Varsayılan ses biçimi Riff-16khz-16bit-mono-PCM ' dir.

* Riff-8khz-16bit-mono-PCM
* Riff-16khz-16bit-mono-PCM
* Riff-24khz-16bit-mono-PCM
* Riff-48kHz-16bit-mono-PCM
* Ses-16khz-32K bit hızı-mono-MP3
* Ses-16khz-64kbit hızı-mono-MP3
* Ses-16khz-128kbit hızı-mono-MP3
* Ses-24khz-48kbit hızı-mono-MP3
* Ses-24khz-96kbit hızı-mono-MP3
* Ses-24khz-160kbit hızı-mono-MP3
