---
ms.openlocfilehash: 870b5b54c4a5afb39b43063bd00d3cb73e8ee0f9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "88682238"
---
Medya grafiğini çalıştırdığınızda, hareket algılayıcısı işlemci düğümündeki sonuçlar, IoT Hub havuz düğümünden IoT Hub 'ına geçer. Visual Studio Code **Çıkış** penceresinde gördüğünüz iletiler bir `body` bölümü ve bir `applicationProperties` bölümü içerir. Daha fazla bilgi için bkz. [IoT Hub Iletileri oluşturma ve okuma](../../../../../iot-hub/iot-hub-devguide-messages-construct.md).

Aşağıdaki iletilerde, canlı video analizi modülü, uygulama özelliklerini ve gövdenin içeriğini tanımlar.

### <a name="mediasessionestablished-event"></a>Mediasessionkurulduğu olayı

Bir medya grafiği oluşturulduğunda, RTSP kaynak düğümü, rtspsim-live555 kapsayıcısında çalışan RTSP sunucusuna bağlanmaya çalışır. Bağlantı başarılı olursa, aşağıdaki olay yazdırılır.

```
[IoTHubMonitor] [05:37:21 AM] Message received from [lva-sample-device/lvaEdge]:
{  
"body": {
"sdp": "SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 xxx.xxx.xxx.xxx\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets={SPS}\r\na=control:track1\r\n"  
},  
"applicationProperties": {  
    "dataVersion": "1.0",  
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",  
    "subject": "/graphInstances/Sample-Graph-1/sources/rtspSource",  
    "eventType": "Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished",  
    "eventTime": "2020-05-21T05:37:21.398Z",
    }  
}
```

Önceki çıktıda: 

* İleti bir tanılama olayıdır `MediaSessionEstablished` . RTSP kaynak düğümünün (konu) RTSP simülatörü ile bağlantı kurmadığını ve (benzetimli) canlı akışını almaya başladığını gösterir.
* `applicationProperties`' De, `subject` iletinin oluşturulduğu grafik topolojisinde bulunan düğüme başvurur. Bu durumda, ileti RTSP kaynak düğümünden gelmektedir.
* `applicationProperties`' De, `eventType` Bu olayın bir tanılama olayı olduğunu gösterir.
* `eventTime`Değer, olayın gerçekleştiği zaman değeridir.
* `body`Bölüm, tanılama olayı hakkındaki verileri içerir. Bu durumda, veriler [oturum açıklaması Protokolü (SDP)](https://en.wikipedia.org/wiki/Session_Description_Protocol) ayrıntılarını içerir.

### <a name="recordingstarted-event"></a>RecordingStarted olayı

Hareket algılandığında, sinyal kapısı işlemci düğümü etkinleştirilir ve medya grafiğindeki dosya havuzu düğümü bir MP4 dosyası yazmaya başlar. Dosya havuzu düğümü işlemsel bir olay gönderir. , `type` `motion` Hareket algılama işlemcisinden kaynaklanan bir sonuç olduğunu belirtmek için olarak ayarlanır. `eventTime`Değer, hareketin GERÇEKLEŞTIĞI UTC zamanının değeridir. Bu işlem hakkında daha fazla bilgi için bu hızlı başlangıçta [genel bakış](#overview) bölümüne bakın.

Bu iletinin bir örneği aşağıda verilmiştir:

```
[IoTHubMonitor] [05:37:27 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "filePath",
    "outputLocation": "/var/media/sampleFilesFromEVR-filesinkOutput-20200521T053726Z.mp4"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",  
    "subject": "/graphInstances/Sample-Graph-1/sinks/fileSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStarted",
    "eventTime": "2020-05-21T05:37:27.713Z",
    "dataVersion": "1.0"
  }
}
```

Önceki iletide: 

* `applicationProperties`' De, `subject` iletinin oluşturulduğu medya grafiğindeki düğüme başvurur. Bu durumda, ileti dosya havuzu düğümünden gelmektedir.
* `applicationProperties`' De, `eventType` Bu olayın işlevsel olduğunu gösterir.
* `eventTime`Değer, olayın gerçekleştiği zaman değeridir. Bu süre, `MediaSessionEstablished` video akışa başladıktan sonra ve sonra 5-6 saniye arasındadır. Bu süre, Araba Park lotuna [taşımaya başladığında](#review-the-sample-video) 5-6 saniyelik bir işaretine karşılık gelir.
* `body`Bölüm işletimsel olayla ilgili verileri içerir. Bu durumda, veriler `outputType` ve içerir `outputLocation` .
* `outputType`Değişkeni, bu bilgilerin dosya yolu hakkında olduğunu gösterir.
* `outputLocation`Değer, sınır MODÜLÜNDEKI MP4 dosyasının konumudur.

### <a name="recordingstopped-and-recordingavailable-events"></a>Recordingdurduruldu ve RecordingAvailable olayları

[Graph topolojisinde](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/evr-motion-files/topology.json)sinyal kapısı işlemci düğümünün özelliklerini incelerseniz etkinleştirme sürelerinin 5 saniyeye ayarlandığını görürsünüz. Bu nedenle, olay alındıktan sonra 5 saniye boyunca şunları `RecordingStarted` alırsınız:

* `RecordingStopped`Kaydın durdurulduğunu belirten bir olay.
* `RecordingAvailable`MP4 dosyasının artık görüntülenmek üzere kullanılabileceğini gösteren bir olay.

İki olay, genellikle birbirleriyle Saniyeler içinde yayılır.
