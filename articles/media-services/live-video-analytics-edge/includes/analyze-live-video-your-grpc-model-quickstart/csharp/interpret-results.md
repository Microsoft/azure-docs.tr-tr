---
ms.openlocfilehash: fdc7360911e37babdb830b7d67ad7224ea84c774
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92038519"
---
Medya grafiğini çalıştırdığınızda, HTTP uzantısı işlemci düğümünün sonuçları IoT Hub 'ına IoT Hub havuz düğümünden geçer. **Çıktı** penceresinde gördüğünüz iletiler bir Body bölümü ve bir applicationproperties bölümü içerir. Daha fazla bilgi için bkz. [IoT Hub Iletileri oluşturma ve okuma](../../../../../iot-hub/iot-hub-devguide-messages-construct.md).

Aşağıdaki iletilerde, canlı video analizi modülü, uygulama özelliklerini ve gövdenin içeriğini tanımlar.

### <a name="mediasessionestablished-event"></a>Mediasessionkurulduğu olayı

Bir medya grafiği oluşturulduğunda, RTSP kaynak düğümü, rtspsim-live555 kapsayıcısında çalışan RTSP sunucusuna bağlanmaya çalışır. Bağlantı başarılı olursa, aşağıdaki olay yazdırılır. Olay türü `Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished` .

```
[IoTHubMonitor] [9:42:18 AM] Message received from [lvaedgesample/lvaEdge]:
{  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 nnn.nn.0.6\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets=Z00AKeKQCgC3YC3AQEBpB4kRUA==,aO48gA==\r\na=control:track1\r\n"
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
* İçinde `applicationProperties` , konusu iletinin medya GRAFIĞINDEKI RTSP kaynak düğümünden oluşturulduğunu gösterir.
* `applicationProperties`' De, `eventType` Bu olayın bir tanılama olayı olduğunu gösterir.
* , `eventTime` Olayın gerçekleştiği saati gösterir.
* Gövde, tanılama olayı hakkındaki verileri içerir. Bu durumda, veriler [oturum açıklaması Protokolü (SDP)](https://en.wikipedia.org/wiki/Session_Description_Protocol) ayrıntılarını içerir.

### <a name="inference-event"></a>Çıkarım olayı

GRPC uzantısı işlemci düğümü, lvaExtension modülünden çıkarım sonuçlarını alır. Ardından sonuçları, çıkarım olayları olarak IoT Hub havuz düğümü aracılığıyla yayar.
Bu olaylarda, bir otomobil veya kamyonun gibi bir varlık olduğunu göstermek için tür varlık olarak ayarlanır. `eventTime`Değer, nesnenin ALGıLANDıĞı UTC zamandır.
Aşağıdaki örnekte, farklı düzeylerde güvenle aynı video çerçevesinde üç araba algılandı.

```
[IoTHubMonitor] [7:52:57 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "timestamp": 143802500954716,
    "inferences": [
      {
        "type": "entity",
        "subtype": "",
        "inferenceId": "",
        "relatedInferences": [],
        "entity": {
          "tag": {
            "value": "car",
            "confidence": 0.86707145
          },
          "attributes": [],
          "box": {
            "l": 0.7294476,
            "t": 0.567829,
            "w": 0.031738576,
            "h": 0.027762715
          }
        },
        "extensions": {},
        "valueCase": "entity"
      },
      {
        "type": "entity",
        "subtype": "",
        "inferenceId": "",
        "relatedInferences": [],
        "entity": {
          "tag": {
            "value": "car",
            "confidence": 0.8634398
          },
          "attributes": [],
          "box": {
            "l": 0.4765757,
            "t": 0.59559196,
            "w": 0.05597749,
            "h": 0.048316106
          }
        },
        "extensions": {},
        "valueCase": "entity"
      },
      {
        "type": "entity",
        "subtype": "",
        "inferenceId": "",
        "relatedInferences": [],
        "entity": {
          "tag": {
            "value": "car",
            "confidence": 0.67120105
          },
          "attributes": [],
          "box": {
            "l": 0.7247387,
            "t": 0.49816906,
            "w": 0.032748587,
            "h": 0.030686663
          }
        },
        "extensions": {},
        "valueCase": "entity"
      }
    ]
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",
    "subject": "/graphInstances/GRAPHINSTANCENAMEHERE/processors/grpcExtension",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime": "2020-08-19T02:52:57.174Z",
    "dataVersion": "1.0"
  }
}
```

İletilerde aşağıdaki ayrıntılara dikkat edin:

* İçinde `applicationProperties` , konusu, iletinin oluşturulduğu grafik topolojisinde bulunan düğüme başvurur.
* `applicationProperties`' De, EventType bu olayın bir analiz olayı olduğunu gösterir.
* `eventTime`Değer, olayın gerçekleştiği zaman değeridir.
* `body`Bölüm, analiz olayı hakkındaki verileri içerir. Bu durumda, olay bir çıkarım olayıdır, bu nedenle gövde ikinci dereceden verileri içerir.
* `inferences`Bölüm, türün varlık olduğunu gösterir. Bu bölüm, varlıkla ilgili ek verileri içerir.