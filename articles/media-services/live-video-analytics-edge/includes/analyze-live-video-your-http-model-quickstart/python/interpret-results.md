---
ms.openlocfilehash: 373b7b8dbf2ccc516edbbb3b87e284d88dfaf0c2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99508499"
---
Medya grafiğini çalıştırdığınızda, HTTP uzantısı işlemci düğümünün sonuçları IoT Hub 'ına IoT Hub havuz düğümünden geçer. **Çıktı** penceresinde gördüğünüz iletiler bir `body` bölümü ve bir `applicationProperties` bölümü içerir. Daha fazla bilgi için bkz. [IoT Hub Iletileri oluşturma ve okuma](../../../../../iot-hub/iot-hub-devguide-messages-construct.md).

Aşağıdaki iletilerde, canlı video analizi modülü, uygulama özelliklerini ve gövdenin içeriğini tanımlar. 

### <a name="mediasessionestablished-event"></a>Mediasessionkurulduğu olayı

Bir medya grafiği oluşturulduğunda, RTSP kaynak düğümü, rtspsim-live555 kapsayıcısında çalışan RTSP sunucusuna bağlanmaya çalışır. Bağlantı başarılı olursa, aşağıdaki olay yazdırılır. Olay türü `Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished` .

```
[IoTHubMonitor] [9:42:18 AM] Message received from [lvaedgesample/lvaEdge]:
{
  "body": {
    "sdp&quot;: &quot;SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 nnn.nn.0.6\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets=Z00AKeKQCgC3YC3AQEBpB4kRUA==,aO48gA==\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "dataVersion": "1.0",
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",
    "subject": "/graphInstances/GRAPHINSTANCENAMEHERE/sources/rtspSource",
    "eventType": "Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-04-09T16:42:18.1280000Z"
  }
}
```

Bu iletide, bu ayrıntılara dikkat edin:

* İleti bir tanılama olayıdır. `MediaSessionEstablished` RTSP kaynak düğümünün (konu) RTSP simülatörü ile bağlandığını ve (benzetimli) canlı akışını almaya başladığını gösterir.
* `applicationProperties`' De, `subject` iletinin medya grafiğindeki RTSP kaynak düğümünden oluşturulduğunu gösterir.
* `applicationProperties`' De, `eventType` Bu olayın bir tanılama olayı olduğunu gösterir.
* , `eventTime` Olayın gerçekleştiği saati gösterir.
* `body`Tanılama olayı hakkındaki verileri içerir. Bu durumda, veriler [oturum açıklaması Protokolü (SDP)](https://en.wikipedia.org/wiki/Session_Description_Protocol) ayrıntılarını içerir.

### <a name="inference-event"></a>Çıkarım olayı

HTTP uzantısı işlemci düğümü, yolov3 modülünden çıkarım sonuçlarını alır. Ardından sonuçları, çıkarım olayları olarak IoT Hub havuz düğümü aracılığıyla yayar. 

Bu olaylarda, türü, `entity` bir otomobil ya da kamyon gibi bir varlık olduğunu belirtmek için olarak ayarlanır. `eventTime`Değer, nesnenin ALGıLANDıĞı UTC zamandır. 

Aşağıdaki örnekte, farklı düzeylerde güvenle aynı video çerçevesinde iki araba algılandı.

```
[IoTHubMonitor] [11:37:17 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "inferences": [
      {
        "entity": {
          "box": {
            "h": 0.0344108157687717,
            "l": 0.5756940841674805,
            "t": 0.5929375966389974,
            "w": 0.04484643936157227
          },
          "tag": {
            "confidence": 0.8714089393615723,
            "value&quot;: &quot;car"
          }
        },
        "type&quot;: &quot;entity"
      },
      {
        "entity": {
          "box": {
            "h": 0.03960910373263889,
            "l": 0.2750667095184326,
            "t": 0.6102327558729383,
            "w": 0.031027007102966308
          },
          "tag": {
            "confidence": 0.7042660713195801,
            "value&quot;: &quot;car"
          }
        },
        "type&quot;: &quot;entity"
      }
    ]
  }
}
```

İletilerde aşağıdaki ayrıntılara dikkat edin:

* `eventTime`Değer, olayın gerçekleştiği zaman değeridir.
* `body`Bölüm, analiz olayı hakkındaki verileri içerir. Bu durumda, olay bir çıkarım olayıdır, bu nedenle gövde verileri içerir `inferences` .
* Bölümü, olduğunu `inferences` gösterir `type` `entity` . Bu bölüm, varlıkla ilgili ek verileri içerir.
