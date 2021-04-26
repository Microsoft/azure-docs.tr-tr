---
Başlık: canlı döküm: Azure Media Services açıklaması: Azure Media Services canlı döküm hakkında bilgi edinin.  
Hizmetler: Media-Services belgetationcenter: ' ' Yazar: ınridatmicrosoft Manager: femıla Düzenleyicisi: ' ' MS. Service: Media-Services MS. Workload: Media ms.tgt_pltfrm: na MS. devlang: ne MS. Topic: nasıl yapılır MS. Date: 08/31/2020 MS. Author: inhenkel

---

# <a name="live-transcription-preview"></a>Canlı döküm (Önizleme)

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure Medya hizmeti, farklı protokollerde video, ses ve metin sağlar. MPEG-DASH veya HLS/CMAF kullanarak canlı akışınızı yayımladığınızda ve video ve sesle birlikte hizmetimiz, IMSC 1.1 uyumlu TTML 'de el ile metin sağlar. Teslim, MPEG-4 Bölüm 30 (ISO/ıEC 14496-30) parçalarında paketlenmiştir. HLS/TS aracılığıyla teslim kullanılıyorsa, metin öbekli VTT olarak dağıtılır.

Canlı döküm açık olduğunda ek ücretler uygulanır. Lütfen [Media Services fiyatlandırma sayfasının](https://azure.microsoft.com/pricing/details/media-services/)canlı video bölümündeki fiyatlandırma bilgilerini gözden geçirin.

Bu makalede, Azure Media Services ile canlı bir olay akışı yapılırken canlı döküm 'nin nasıl etkinleştirileceği açıklanır. Devam etmeden önce Media Services v3 REST API 'lerinin kullanımına alışkın olduğunuzdan emin olun (Ayrıntılar için [Bu öğreticiye](stream-files-tutorial-with-rest.md) bakın). Ayrıca, [canlı akış](stream-live-streaming-concept.md) kavramı hakkında bilgi sahibi olmanız gerekir. [Media Services öğreticisiyle canlı akışı](stream-live-tutorial-with-api.md) doldurmanız önerilir.

## <a name="live-transcription-preview-regions-and-languages"></a>Canlı döküm önizleme bölgeleri ve dilleri

Canlı döküm aşağıdaki bölgelerde kullanılabilir:

- Güneydoğu Asya
- West Europe
- Kuzey Avrupa
- Doğu ABD
- Central US
- Orta Güney ABD
- Batı ABD 2
- Güney Brezilya

Bu, kullanılabilir dillerin, API 'deki dil kodunu kullanan bir listesidir.

| Dil | Dil kodu |
| -------- | ------------- |
| Katalanca  | ca-ES |
| Danca (Danimarka) | da-DK |
| Almanca (Almanya) | de-DE |
| İngilizce (Avustralya) | En-AU |
| İngilizce (Kanada) | en-CA |
| İngilizce (İngiltere) | en-GB |
| İngilizce (Hindistan) | En-ın |
| İngilizce (Yeni Zelanda) | En-NZ |
| İngilizce (ABD) | en-US |
| İspanyolca (İspanya) | es-ES |
| İspanyolca (Meksika) | es-MX |
| Fince (Finlandiya) | fi-FI |
| Fransızca (Kanada) | fr-CA |
| Fransızca (Fransa) | fr-FR |
| İtalyanca (İtalya) | it-IT |
| Felemenkçe (Hollanda) | nl-NL |
| Portekizce (Brezilya) | pt-BR |
| Portekizce (Portekiz) | pt-PT |
| İsveççe (İsviçre) | sv-SE |

## <a name="create-the-live-event-with-live-transcription"></a>Canlı döküm ile canlı etkinlik oluşturma

Döküm açıkken canlı bir olay oluşturmak için, PUT işlemini 2019-05-01-Preview API sürümüyle birlikte gönderin, örneğin:

```
PUT https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/liveEvents/:liveEventName?api-version=2019-05-01-preview&autoStart=true 
```

İşlem aşağıdaki gövdeye sahiptir (burada, bir geçiş canlı olayı, alma protokolü olarak RTMP ile oluşturulur). Döküm özelliğinin eklenmesini aklınızda edin.

```
{
  "properties": {
    "description": "Demonstrate how to enable live transcriptions",
    "input": {
      "streamingProtocol": "RTMP",
      "accessControl": {
        "ip": {
          "allow": [
            {
              "name": "Allow All",
              "address": "0.0.0.0",
              "subnetPrefixLength": 0
            }
          ]
        }
      }
    },
    "preview": {
      "accessControl": {
        "ip": {
          "allow": [
            {
              "name": "Allow All",
              "address": "0.0.0.0",
              "subnetPrefixLength": 0
            }
          ]
        }
      }
    },
    "encoding": {
      "encodingType": "None"
    },
    "transcriptions": [
      {
        "language": "en-US"
      }
    ],
    "useStaticHostname": false,
    "streamOptions": [
      "Default"
    ]
  },
  "location": "West US 2"
}
```

## <a name="start-or-stop-transcription-after-the-live-event-has-started"></a>Canlı etkinlik başladıktan sonra yeniden başlatma veya durdurma

Canlı olay çalışır durumda olduğunda canlı dökümü başlatabilir ve durdurabilirsiniz. Canlı olayları başlatma ve durdurma hakkında daha fazla bilgi için [Media Services v3 API 'leri Ile geliştirme](media-services-apis-overview.md#long-running-operations)konusundaki uzun süre çalışan işlemler bölümünü okuyun.

Canlı güncelleştirmeleri açmak veya döküm dilini güncelleştirmek için canlı olayı bir "döküm" özelliği içerecek şekilde yama yapın. Canlı dökümleri devre dışı bırakmak için, "Transcriptions" özelliğini canlı olay nesnesinden kaldırın.  

> [!NOTE]
> Canlı olay sırasında **bir defadan fazla** açma veya kapatma işlemi, desteklenen bir senaryo değildir.

Bu, canlı dökümlerini açmak için örnek çağrıdır.

DÜZELTMESI ```https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/liveEvents/:liveEventName?api-version=2019-05-01-preview```

```
{
  "properties": {
    "description": "Demonstrate how to enable live transcriptions", 
    "input": {
      "streamingProtocol": "RTMP",
      "accessControl": {
        "ip": {
          "allow": [
            {
              "name": "Allow All",
              "address": "0.0.0.0",
              "subnetPrefixLength": 0
            }
          ]
        }
      }
    },
    "preview": {
      "accessControl": {
        "ip": {
          "allow": [
            {
              "name": "Allow All",
              "address": "0.0.0.0",
              "subnetPrefixLength": 0
            }
          ]
        }
      }
    },
    "encoding": {
      "encodingType": "None"
    },
    "transcriptions": [
      {
        "language": "en-US"
      }
    ],
    "useStaticHostname": false,
    "streamOptions": [
      "Default"
    ]
  },
  "location": "West US 2"
}
```

## <a name="transcription-delivery-and-playback"></a>Dağıtım ve kayıttan yürütme

Hizmetimizin, farklı protokollerde video, ses ve metin sunmak için dinamik paketleme nasıl kullandığını gösteren [dinamik paketlemeye genel bakış](encode-dynamic-packaging-concept.md#to-prepare-your-source-files-for-delivery) makalesini gözden geçirin. MPEG-DASH veya HLS/CMAF kullanarak canlı akışınızı yayımladığınızda ve video ve sesle birlikte hizmetimiz, IMSC 1.1 uyumlu TTML 'de el ile metin sağlar. Bu teslim, MPEG-4 Bölüm 30 (ISO/ıEC 14496-30) parçalarında paketlenmiştir. HLS/TS aracılığıyla teslim kullanılıyorsa, metin öbekli VTT olarak dağıtılır. Akışı oynatmak için [Azure Media Player](player-use-azure-media-player-how-to.md) gibi bir Web oynatıcı kullanabilirsiniz.  

> [!NOTE]
> Azure Media Player kullanıyorsanız, sürüm 2.3.3 veya üstünü kullanın.

## <a name="known-issues"></a>Bilinen sorunlar

Önizleme için, canlı dökümle ilgili bilinen sorunlar aşağıda verilmiştir:

- Uygulamaların [Media Services v3 Openapı belirtiminde](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/preview/2019-05-01-preview/streamingservice.json)açıklanan önizleme API 'lerini kullanması gerekir.
- Dijital hak yönetimi (DRM) koruması metin parçası için uygulanmıyor, yalnızca AES zarf şifrelemesi mümkündür.

## <a name="next-steps"></a>Sonraki adımlar

* [Media Services genel bakış](media-services-overview.md)
