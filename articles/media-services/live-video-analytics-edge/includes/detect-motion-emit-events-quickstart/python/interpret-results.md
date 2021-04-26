---
ms.openlocfilehash: 8bcef40dad9c67e9e2c6d6c4a051045999487027
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99531838"
---
Medya grafiğini çalıştırdığınızda, hareket algılayıcısı işlemci düğümündeki sonuçlar, IoT Hub havuz düğümünden IoT Hub 'ına geçer. Visual Studio Code **Çıkış** penceresinde gördüğünüz iletiler bir `body` bölümü ve bir `applicationProperties` bölümü içerir. Daha fazla bilgi için bkz. [IoT Hub Iletileri oluşturma ve okuma](../../../../../iot-hub/iot-hub-devguide-messages-construct.md).

Aşağıdaki iletilerde, canlı video analizi modülü, uygulama özelliklerini ve gövdenin içeriğini tanımlar.

### <a name="mediasessionestablished-event"></a>Mediasessionkurulduğu olayı

Bir medya grafiği oluşturulduğunda, RTSP kaynak düğümü, rtspsim-live555 kapsayıcısında çalışan RTSP sunucusuna bağlanmaya çalışır. Bağlantı başarılı olursa, aşağıdaki olay yazdırılır.

```
[IoTHubMonitor] [9:42:18 AM] Message received from [lvaedgesample/lvaEdge]:  
{  
"body": {
"sdp&quot;: &quot;SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 xxx.xxx.xxx.xxx\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets={SPS}\r\na=control:track1\r\n"  
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

Önceki çıktıda: 

* İleti bir tanılama olayıdır `MediaSessionEstablished` . RTSP kaynak düğümünün (konu) RTSP simülatörü ile bağlandığını ve (benzetimli) canlı akışını almaya başladığını gösterir.
* ' De, içinde `applicationProperties` `subject` iletinin oluşturulduğu grafik topolojisinde bulunan düğüme başvurur. Bu durumda, ileti RTSP kaynak düğümünden gelmektedir.
* `applicationProperties`' De, `eventType` Bu olayın bir tanılama olayı olduğunu gösterir.
* `eventTime`Değer, olayın gerçekleştiği zamanı gösterir.
* `body`Bölüm, tanılama olayı hakkındaki verileri içerir. Bu durumda, veriler [oturum açıklaması Protokolü (SDP)](https://en.wikipedia.org/wiki/Session_Description_Protocol) ayrıntılarını içerir.

### <a name="motiondetection-event"></a>MotionDetection olayı

Hareket algılandığında IoT Edge modüldeki canlı video analizi bir çıkarım olayı gönderir. , `type` `motion` Hareket algılama işlemcisinden kaynaklanan bir sonuç olduğunu belirtmek için olarak ayarlanır. `eventTime`Değer, hareketin ne zaman (UTC) meydana oluştuğunu söyler. 

Bu iletinin bir örneği aşağıda verilmiştir:

```
  {  
  "body": {  
    "timestamp": 142843967343090,
    "inferences": [  
      {  
        "type": "motion",  
        "motion": {  
          "box": {  
            "l": 0.573222,  
            "t": 0.492537,  
            "w": 0.141667,  
            "h": 0.074074  
          }  
        }  
      }  
    ]  
  } 
}  
```

Bu örnekte: 

* `eventTime`Değer, olayın gerçekleştiği zaman değeridir.
* `body`Değer, analiz olayı hakkında veri değeridir. Bu durumda, olay bir çıkarım olayıdır, bu nedenle gövde `timestamp` ve veri içerir `inferences` .
* Veri, olduğunu `inferences` gösterir `type` `motion` . Bu olay hakkında ek verilere sahiptir `motion` .
* `box`Bölüm, hareketli nesnenin çevresindeki bir sınırlayıcı kutunun koordinatlarını içerir. Değerler, videonun piksel cinsinden genişliği ve yüksekliğiyle normalleştirilir. Örneğin, Genişlik 1920, yükseklik ise 1080 ' dir.

    ```
    l - distance from left of image
    t - distance from top of image
    w - width of bounding box
    h - height of bounding box
    ```
