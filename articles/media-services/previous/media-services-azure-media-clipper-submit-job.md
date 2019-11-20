---
title: Azure Media Clipper kırpma işleri gönderme | Microsoft Docs
description: Azure Media Clipper 'dan kırpma işleri gönderme adımları
services: media-services
keywords: klip; alt klip; kodlama; medya
author: Juliako
manager: femila
ms.author: juliako
ms.date: 03/14/2019
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 04d0d2bb8939c8036ec6817c58f9ac2fbb3acd72
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73684969"
---
# <a name="submit-clipping-jobs-from-azure-media-clipper"></a>Azure Media Clipper 'dan kırpma işleri gönderme 

Azure Media Clipper, kırpma işi gönderimini işlemek için uygulanması gereken bir **Submitsubclipcallback** yöntemi gerektirir. Bu işlev, bir Web hizmetine Clipper çıkışının HTTP GÖNDERISINI uygulamak içindir. Bu Web hizmeti, kodlama işini gönderebileceğiniz yerdir. Clipper çıkışı, işlenmiş işler için Media Encoder Standard kodlama ön kümesi veya dinamik bildirim filtresi çağrıları için REST API yükü olur. Media Services hesabı kimlik bilgileri istemcisinin tarayıcısında güvenli olmadığından, bu geçişli model gereklidir.

Aşağıdaki sıralı diyagram, tarayıcı istemcisi, Web hizmetiniz ve Azure Media Services: ![Azure Media Clipper sıra diyagramı arasındaki iş akışını gösterir](media/media-services-azure-media-clipper-submit-job/media-services-azure-media-clipper-sequence-diagram.PNG)

Önceki diyagramda dört varlık şunlardır: son kullanıcının tarayıcısı, Web hizmetiniz, Clipper kaynaklarını barındıran CDN uç noktası ve Azure Media Services. Son Kullanıcı Web sayfanıza gittiğinde, sayfa, barındırma CDN uç noktasındaki Clipper JavaScript ve CSS kaynaklarını alır. Son Kullanıcı, tarayıcıdan kırpma işini veya dinamik bildirim filtresi oluşturma çağrısını yapılandırır. Son Kullanıcı işi veya filtre oluşturma çağrısını gönderdiğinde, tarayıcı iş yükünü dağıtmanız gereken bir Web hizmetine koyar. Bu Web hizmeti, Media Services hesabınızın kimlik bilgilerini kullanarak Azure Media Services için kırpma işini veya filtre oluşturma çağrısını gönderir.

Aşağıdaki kod örneğinde örnek bir **Submitsubclipcallback** yöntemi gösterilmektedir. Bu yöntemde, Media Encoder Standard Encoding önayarının HTTP GÖNDERISINI uygulayacağınızı görürsünüz. GÖNDERI başarılı olduysa (**sonuç**) **Promise** çözümlenirse, aksi takdirde hata ayrıntıları ile reddedilir.

```javascript
// Submit Subclip Callback
//
// Parameter:
// - subclip: object that represents the subclip (output contract).
//
// Returns: a Promise object that, when resolved, returns true if the operation was accept in the back-end; otherwise, returns false.
var onSubmitSubclip = function (subclip) {
    var promise = new Promise(function (resolve, reject) {
        // TODO: perform the back-end AJAX request to submit the subclip job.
        var result = true;

        if (/* everything turned out fine */ result) {
            resolve(result);
        }
        else {
            reject(Error("error details"));
        }
    });

    return promise;
};

var subclipper = new subclipper({
    selector: '#root',
    restVersion: '2.0',
    submitSubclipCallback: onSubmitSubclip,
});
```
İş gönderimi çıktısı, işlenen iş için Media Encoder Standard kodlama ön kümesi veya dinamik bildirim filtreleri için REST API yükü olur.

## <a name="submitting-encoding-job-to-create-video"></a>Video oluşturmak için kodlama işi gönderiliyor
Çerçeveye doğru bir video klibi oluşturmak için bir Media Encoder Standard Encoding işi gönderebilirsiniz. Kodlama işi, yeni bir parçalanmış MP4 dosyası olan işlenmiş videoları üretir.

İşlenmiş kırpmayla ilgili iş çıkış sözleşmesi, aşağıdaki özelliklere sahip bir JSON nesnesidir:

```json
{
  /* Required */
  "name": "My New Subclip Output Name",

  /* Required: string specifying the format of the Json file.
   */
  /* NOTE: The REST API version, currently 2.0 is supported
   */
  "restVersion": "2.0"

  /* Required: array containing all the input Ids (both "asset" or "filter" type) used in the subclipper timeline;
  it must contain at least one item. */
  /* NOTE: when "output"."type" === "job", all items must match the "inputsIds"[i]."type" === "asset" condition;
  this array can be used in the back-end to get the input assets for the subclipping job with the Media Encoder Standard (MES) processor. */
  /* NOTE: when "output"."type" === "filter", there must be a single item in the array ("inputsIds".length === 1)
  that can be used in the back-end to get the source for the dynamic manifest filter (create or update). */
  "inputsIds": [{
    /* Required */
    "id": "my-asset-id-1",

    /* Required: must be one of the following values: "asset" or "filter" */
    "type": "asset"
  }, {
    /* Required */
    "id": "my-asset-id-2",

    /* Required: must be one of the following values: "asset" or "filter" */
    "type": "asset"
  }],

  /* Required */
  "output": {

    /* Required: must be one of the following values: "job" or "filter" */
    "type": "job",

    /* Required if "type" === "job" */
    /* NOTE: This is the preset for the Media Encoder Standard (MES) processor that can be used in the back-end to submit the subclip job.
    The encoding profile ("Codecs" property) depends on the "singleBitrateMp4Profile" and "multiBitrateMp4Profile" option parameters
    specified when creating the widget instance. */
    /* REFERENCE: https://docs.microsoft.com/azure/media-services/media-services-advanced-encoding-with-mes */
    "job": {
      "Version": 1.0,
      "Codecs": [{
          "KeyFrameInterval": "00:00:02",
          "SceneChangeDetection": true,
          "H264Layers": [{
            "Profile": "Auto",
            "Level": "auto",
            "Bitrate": 6750,
            "MaxBitrate": 6750,
            "BufferWindow": "00:00:05",
            "Width": 1920,
            "Height": 1080,
            "BFrames": 3,
            "ReferenceFrames": 3,
            "AdaptiveBFrame": true,
            "Type": "H264Layer",
            "FrameRate": "0/1"
          }],
          "Type": "H264Video"
        },
        {
          "Profile": "AACLC",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128,
          "Type": "AACAudio"
        }
      ],
      "Outputs": [{
        "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",
        "Format": {
          "Type": "MP4Format"
        }
      }],
      "Sources": [{
        "AssetId": "my-asset-id-1",
        "StartTime": "0.00:01:15.000",
        "Duration": "0.00:00:25.000"
      }, {
        "AssetId": "my-asset-id-2",
        "StartTime": "0.00:20:04.000",
        "Duration": "0.00:33:07.500"
      }]
    }
  }
}
```

Kodlama işini gerçekleştirmek için, Media Encoder Standard kodlama işini ilişkili ön ayarla birlikte gönderebilirsiniz. [.NET SDK](https://docs.microsoft.com/azure/media-services/media-services-dotnet-encode-with-media-encoder-standard) veya [REST API](https://docs.microsoft.com/azure/media-services/media-services-rest-encode-asset)kullanarak kodlama işleri gönderme hakkında daha fazla bilgi için bu makaleye bakın.

## <a name="quickly-creating-video-clips-without-encoding"></a>Kodlama olmadan hızla video klipleri oluşturma
Bir kodlama işi oluşturmaya alternatif olarak, dinamik bildirim filtreleri oluşturmak için Azure Media Clipper 'ı kullanabilirsiniz. Filtreler kodlama gerektirmez ve yeni bir varlık oluşturulmadığından hızlı bir şekilde oluşturulabilir. Filtre kırpması için çıkış sözleşmesi, aşağıdaki özelliklere sahip bir JSON nesnesidir:

```json
{
  /* Required: Filter name (Alphanumeric characters and hyphens only, no whitespace) */
  "name": "FilterName",

  /* Required: string specifying the format of the Json file.
   */
  /* NOTE: This subclipper version always returns '2.0' in this field.
   */
  "restVersion": "2.0"

  /* Required: array containing all the input Ids (both "asset" or "filter" type) used in the subclipper timeline;
  it must contain at least one item. */
  /* NOTE: when "output"."type" === "job", all items must match the "inputsIds"[i]."type" === "asset" condition;
  this array can be used in the back-end to get the input assets for the subclipping job with the Media Encoder Standard (MES) processor. */
  /* NOTE: when "output"."type" === "filter", there must be a single item in the array ("inputsIds".length === 1)
  that can be used in the back-end to get the source for the dynamic manifest filter (create or update). */
  "inputsIds": [{
    /* Required */
    "id": "my-asset-id",

    /* Required: must be one of the following values: "asset" or "filter" */
    "type": "asset"
  }],

  /* Required */
  "output": {

    /* Required: must be one of the following values: "job" or "filter" */
    "type": "filter",

    /* Required if "type" === "filter" */
    /* REFERENCE: https://docs.microsoft.com/rest/api/media/operations/assetfilter */
    "filter": {
      "PresentationTimeRange": {
        "StartTimestamp": "340000000",
        "EndTimestamp": "580000000",
        "Timescale": "10000000"
      },
      "Tracks": [{
        "PropertyConditions": [{
          "Property": "Type",
          "Value": "video",
          "Operator": "Equal"
        }]
      }, {
        "PropertyConditions": [{
          "Property": "Type",
          "Value": "audio",
          "Operator": "Equal"
        }, {
          "Property": "Name",
          "Value": "audio-track-1",
          "Operator": "Equal"
        }]
      }, {
        "PropertyConditions": [{
          "Property": "Type",
          "Value": "text",
          "Operator": "Equal"
        }, {
          "Property": "Language",
          "Value": "en",
          "Operator": "Equal"
        }]
      }]
    }
  }
}
```

Dinamik bildirim filtresi oluşturmaya yönelik REST çağrısını göndermek için [REST API](https://docs.microsoft.com/azure/media-services/media-services-rest-dynamic-manifest)kullanarak ilişkili filtre yükünü iletin.
