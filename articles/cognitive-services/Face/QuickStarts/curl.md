---
title: 'Hızlı Başlangıç: Azure REST API ve kıvrımlı bir görüntüdeki yüzeyleri algılama'
titleSuffix: Azure Cognitive Services
description: Bu hızlı başlangıçta, görüntüdeki yüzeyleri algılamak için Azure yüz REST API kıvrımlı olarak kullanacaksınız.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 09/06/2019
ms.author: pafarley
ms.openlocfilehash: da929744922f8653bc293b68dbbadb9347a447e9
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/10/2019
ms.locfileid: "70859144"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-face-rest-api-and-curl"></a>Hızlı Başlangıç: Yüz REST API ve kıvrımlı kullanarak görüntüdeki yüzeyleri algılama

Bu hızlı başlangıçta, bir görüntüdeki insan yüzlerini saptamak için Azure yüz REST API 'yi kıvrımlı olarak kullanacaksınız.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun. 

## <a name="prerequisites"></a>Önkoşullar

- Yüz tanıma API'si abonelik anahtarı. Ücretsiz deneme aboneliği anahtarından alabilirsiniz [Bilişsel Hizmetler'i deneyin](https://azure.microsoft.com/try/cognitive-services/?api=face-api). Veya yönergeleri [Bilişsel Hizmetler hesabı oluşturma](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) yüz tanıma API'si hizmete abone ve anahtarınızı alın.

## <a name="write-the-command"></a>Komutu yazın
 
Yüz Tanıma API'si çağırmak ve bir görüntüden yüz öznitelik verileri almak için aşağıdaki gibi bir komut kullanacaksınız. İlk olarak, kodu bir metin düzenleyicisine&mdash;kopyalayın, çalıştırmadan önce komutun belirli kısımlarında değişiklik yapmanız gerekir.

```shell
curl -H "Ocp-Apim-Subscription-Key: <Subscription Key>" "https://<My Endpoint String>.com/face/v1.0/detect?returnFaceId=true&returnFaceLandmarks=false&returnFaceAttributes=age,gender,headPose,smile,facialHair,glasses,emotion,hair,makeup,occlusion,accessories,blur,exposure,noise" -H "Content-Type: application/json" --data-ascii "{\"url\":\"https://upload.wikimedia.org/wikipedia/commons/c/c3/RH_Louise_Lillian_Gish.jpg\"}"
```

### <a name="subscription-key"></a>Abonelik anahtarı
Geçerli `<Subscription Key>` yüz abonelik anahtarınızla değiştirin.

### <a name="face-endpoint-url"></a>Yüz uç noktası URL 'SI

URL `https://<My Endpoint String>.com/face/v1.0/detect` , sorgulanacak Azure yüz uç noktasını belirtir. Bu URL 'nin ilk kısmını, abonelik anahtarınıza karşılık gelen uç noktayla eşleşecek şekilde değiştirmeniz gerekebilir.

[!INCLUDE [subdomains-note](../../../../includes/cognitive-services-custom-subdomains-note.md)]

### <a name="url-query-string"></a>URL sorgu dizesi

Yüz uç nokta URL 'sinin sorgu dizesi alınacak yüz özniteliklerini belirtir. Bu dizeyi, amaçlanan kullanım yönteminize bağlı olarak değiştirmek isteyebilirsiniz.

```
?returnFaceId=true&returnFaceLandmarks=false&returnFaceAttributes=age,gender,headPose,smile,facialHair,glasses,emotion,hair,makeup,occlusion,accessories,blur,exposure,noise
```

### <a name="image-source-url"></a>Görüntü kaynağı URL 'SI
Kaynak URL 'SI, giriş olarak kullanılacak resmi belirtir. Bunu, çözümlemek istediğiniz herhangi bir görüntüyü işaret ederek değiştirebilirsiniz.

```
https://upload.wikimedia.org/wikipedia/commons/c/c3/RH_Louise_Lillian_Gish.jpg
``` 

## <a name="run-the-command"></a>Yeni bir “kurtarma VM’si” oluşturmak ve sorunlu VM’nin işletim sistemi diskini kurtarma VM’sine veri diski olarak takmak için

Değişikliklerinizi yaptıktan sonra, bir komut istemi açın ve yeni komutu girin. Konsol penceresinde JSON verileri olarak görünen yüz bilgilerini görmeniz gerekir. Örneğin:

```json
[
  {
    "faceId": "49d55c17-e018-4a42-ba7b-8cbbdfae7c6f",
    "faceRectangle": {
      "top": 131,
      "left": 177,
      "width": 162,
      "height": 162
    },
    "faceAttributes": {
      "smile": 0,
      "headPose": {
        "pitch": 0,
        "roll": 0.1,
        "yaw": -32.9
      },
      "gender": "female",
      "age": 22.9,
      "facialHair": {
        "moustache": 0,
        "beard": 0,
        "sideburns": 0
      },
      "glasses": "NoGlasses",
      "emotion": {
        "anger": 0,
        "contempt": 0,
        "disgust": 0,
        "fear": 0,
        "happiness": 0,
        "neutral": 0.986,
        "sadness": 0.009,
        "surprise": 0.005
      },
      "blur": {
        "blurLevel": "low",
        "value": 0.06
      },
      "exposure": {
        "exposureLevel": "goodExposure",
        "value": 0.67
      },
      "noise": {
        "noiseLevel": "low",
        "value": 0
      },
      "makeup": {
        "eyeMakeup": true,
        "lipMakeup": true
      },
      "accessories": [],
      "occlusion": {
        "foreheadOccluded": false,
        "eyeOccluded": false,
        "mouthOccluded": false
      },
      "hair": {
        "bald": 0,
        "invisible": false,
        "hairColor": [
          {
            "color": "brown",
            "confidence": 1
          },
          {
            "color": "black",
            "confidence": 0.87
          },
          {
            "color": "other",
            "confidence": 0.51
          },
          {
            "color": "blond",
            "confidence": 0.08
          },
          {
            "color": "red",
            "confidence": 0.08
          },
          {
            "color": "gray",
            "confidence": 0.02
          }
        ]
      }
    }
  }
]
```

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, bir görüntüdeki yüzeyleri tespit etmek ve özniteliklerini döndürmek için Azure Yüz Tanıma API'si çağıran bir kıvrımlı komut yazdınız. Daha fazla bilgi edinmek için Yüz Tanıma API'si başvuru belgelerini inceleyin.

> [!div class="nextstepaction"]
> [Yüz Tanıma API’si](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
