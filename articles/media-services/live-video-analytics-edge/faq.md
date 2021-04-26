---
title: IoT Edge SSS-Azure 'da canlı video analizi
description: Bu makalede, IoT Edge üzerinde canlı video analizi hakkında sıkça sorulan sorular yanıtlanmaktadır.
ms.topic: conceptual
ms.date: 12/01/2020
ms.openlocfilehash: 2e5ec6e3a303bb8d655e666a820cfe67943b4eb6
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106275966"
---
# <a name="live-video-analytics-on-iot-edge-faq"></a>IoT Edge SSS üzerinde canlı video analizi

Bu makalede, Azure IoT Edge üzerinde canlı video analizi hakkında sıkça sorulan sorular yanıtlanmaktadır.

## <a name="general"></a>Genel

**Grafik topolojisi tanımında hangi sistem değişkenlerini kullanabilirim?**

| Değişken   |  Açıklama  | 
| --- | --- | 
| [System. DateTime](/dotnet/framework/data/adonet/sql/linq/system-datetime-methods) | Genellikle aşağıdaki biçimde bir tarih ve saat olarak ifade edilen UTC zamanında bir anlık temsil eder:<br>*yyyyMMddTHHmmssZ* | 
| System. ııisedatetime | Aşağıdaki biçimde, ıSO8601 dosya uyumlu bir biçimde milisaniye olan bir Eşgüdümlü Evrensel Saat (UTC) Tarih-saat örneğini temsil eder:<br>*yyyyMMddTHHmmss. fffZ* | 
| System. Graphtopologyıname   | Bir medya grafiği topolojisini temsil eder ve bir grafiğin şema öğesini barındırır. | 
| System. Graphınstancename |    Bir medya grafiği örneğini temsil eder, parametre değerlerini barındırır ve topolojiye başvurur. | 

## <a name="configuration-and-deployment"></a>Yapılandırma ve dağıtım

**Medya Edge modülünü bir Windows 10 cihazına dağıtabilir miyim?**

Evet. Daha fazla bilgi için bkz. [Windows 10 ' da Linux kapsayıcıları](/virtualization/windowscontainers/deploy-containers/linux-containers).

## <a name="capture-from-ip-camera-and-rtsp-settings"></a>IP kamerası ve RTSP ayarlarından yakala

**Video akışı göndermek için cihazımda belirli bir SDK kullanmam gerekiyor mu?**

Hayır, IoT Edge canlı video analizi, video akışı için, çoğu IP kamerada desteklenen RTSP (gerçek zamanlı akış protokolü) kullanarak medya yakalamayı destekler.

**Real-Time mesajlaşma Protokolü (RTMP) veya Kesintisiz Akış Protokolü (Media Services canlı bir olay gibi) kullanarak IoT Edge medya canlı video analizlerine gönderebilir miyim?**

Hayır, canlı video analizi yalnızca IP kameralarından video yakalamak için RTSP 'yi destekler. TCP/HTTP üzerinden RTSP akışını destekleyen tüm kamera çalışmalıdır. 

**Bir grafik örneğindeki RTSP kaynak URL 'sini sıfırlayabilir veya güncelleştirebilir miyim?**

Evet, grafik örneği *etkin* değil durumunda.  

**Test ve geliştirme sırasında kullanılabilecek bir RTSP simülatörü var mı?**

Evet, bir [RTSP simülatörü](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) uç modülü hızlı başlangıçlarda, öğrenme sürecini desteklemek için kullanılabilir. Bu modül en iyi çaba olarak sağlanır ve her zaman kullanılabilir olmayabilir. Simülatörü birkaç saatten uzun bir *süre kullanmamanız önerilir* . Üretim dağıtımını planlayabilmeniz için önce gerçek RTSP kaynağınıza yönelik teste yatırım yapmanız gerekir.

**Uçta IP kameraları için ONVIF bulmayı destekliyor musunuz?**

Hayır, uçta cihazların açık ağ video arabirimi Forumu (ONVıF) bulma işlemini desteklemiyoruz.

## <a name="streaming-and-playback"></a>Akış ve kayıttan yürütme

**Azure Media Services, HLS veya DASH gibi akış teknolojilerini kullanarak kenarından kaydedilmiş varlıkları kayıttan yürütebilir miyim?**

Evet. Kayıtlı varlıkları Azure Media Services başka bir varlık gibi akışla aktarabilirsiniz. İçeriği akışa almak için, oluşturulmuş bir akış uç noktası ve çalışır durumda olmalıdır. Standart akış Konumlandırıcı oluşturma işleminin kullanılması, herhangi bir özellikli oynatıcı çerçevesine akış için bir Apple HTTP Canlı Akışı (HLS) veya HTTP üzerinden dinamik Uyarlamalı akışa (MPEG-DASH olarak da bilinir) yönelik erişim sağlayacak. HLS veya DASH bildirimleri oluşturma ve yayımlama hakkında daha fazla bilgi için bkz. [dinamik paketleme](../latest/encode-dynamic-packaging-concept.md).

**Arşivlenmiş bir varlık üzerinde Media Services standart içerik koruması ve DRM özelliklerini kullanabilir miyim?**

Evet. Tüm standart dinamik şifreleme içerik koruması ve dijital hak yönetimi (DRM) özellikleri, bir medya grafiğinden kaydedilen varlıklarda kullanılabilir.

**Kayıtlı varlıkların içeriğini görüntülemek için hangi oyuncuları kullanabilirim?**

Uyumlu HLS sürüm 3 veya sürüm 4 ' ü destekleyen tüm standart oyuncular desteklenir. Ayrıca, uyumlu MPEG-DASH kayıttan yürütme yeteneğine sahip olan her oyuncu de desteklenir.

Test için önerilen oyuncular şunlardır:

* [Azure Media Player](../latest/player-use-azure-media-player-how-to.md)
* [HLS.js](https://hls-js.netlify.app/demo/)
* [Video.js](https://videojs.com/)
* [Dash.js](https://github.com/Dash-Industry-Forum/dash.js/wiki)
* [Shaka oynatıcı](https://github.com/google/shaka-player)
* [Exoçalar](https://github.com/google/ExoPlayer)
* [Apple Native HTTP Canlı Akışı](https://developer.apple.com/streaming/)
* Edge, Chrome veya Safari yerleşik HTML5 video oynatıcı
* HLS veya DASH oynatmayı destekleyen ticari oyuncular

**Medya grafiği varlığı akışı için sınırlamalar nelerdir?**

Bir medya grafiğinden canlı veya kayıtlı bir varlık akışı, medya & eğlence için isteğe bağlı ve canlı akış için desteklenen Media Services yüksek ölçekli altyapıyı ve akış uç noktasını kullanır, en üst (OTT) ve yayın müşterileri. Bu, senaryonuza bağlı olarak Azure Content Delivery Network, Verizon veya Akamai ' yi hızla ve kolayca etkinleştirebilmeniz anlamına gelir.

Apple HLS veya MPEG-DASH kullanarak içerik teslim edebilirsiniz.

## <a name="design-your-ai-model"></a>AI modelinizi tasarlama 

**Bir Docker kapsayıcısında Sarmalanan birden çok AI modeli var. Bunları canlı video analiziyle nasıl kullanmalıyım?** 

Çözümler, canlı video analiziyle iletişim kurmak için ınlebilme sunucusu tarafından kullanılan iletişim protokolüne bağlı olarak farklılık gösterir. Aşağıdaki bölümlerde her protokolün nasıl çalıştığı açıklanır.

*Http protokolünü kullanın*:

* Tek kapsayıcı (tek lvaExtension):  

   İkinci olarak, farklı AI modelleri için tek bir bağlantı noktası, ancak farklı uç noktalar kullanabilirsiniz. Örneğin, bir Python örneği için `route` , burada gösterildiği gibi model başına farklı s kullanabilirsiniz: 

   ```
   @app.route('/score/face_detection', methods=['POST']) 
   … 
   Your code specific to face detection model… 

   @app.route('/score/vehicle_detection', methods=['POST']) 
   … 
   Your code specific to vehicle detection model 
   … 
   ```

   Ardından, canlı video analizi dağıtımınızda, grafikler örneğini oluştururken, her bir örnek için çıkarım sunucu URL 'sini burada gösterildiği gibi ayarlayın: 

   1. örnek: çıkarım sunucu URL 'SI =`http://lvaExtension:44000/score/face_detection`<br/>
   2. örnek: çıkarım sunucu URL 'SI =`http://lvaExtension:44000/score/vehicle_detection`  
   
    > [!NOTE]
    > Alternatif olarak, farklı bağlantı noktalarında AI modellerinizi açığa çıkarmak ve grafiklerin örneğini oluşturmak için bunları çağırabilirsiniz.  

* Birden çok kapsayıcı: 

   Her kapsayıcı farklı bir adla dağıtılır. Daha önce, canlı video analizi belge kümesinde *Lvaextension* adlı bir uzantının nasıl dağıtılacağını gösterdik. Artık, her biri aynı HTTP arabirimine sahip olan iki farklı kapsayıcı geliştirebilirsiniz, bu da aynı uç noktaya sahip oldukları anlamına gelir `/score` . Bu iki kapsayıcıyı farklı adlarla dağıtın ve her ikisinin de *farklı bağlantı noktalarında* dinleme yapıldığından emin olun. 

   Örneğin, adlı bir kapsayıcı `lvaExtension1` bağlantı noktasını dinler `44000` ve adlı ikinci kapsayıcı `lvaExtension2` bağlantı noktasını dinler `44001` . 

   Canlı video analizi topolojinizde, aşağıda gösterildiği gibi farklı çıkarım URL 'Leri ile iki grafik örnekleyebilirsiniz: 

   İlk örnek: çıkarım sunucu URL 'SI = `http://lvaExtension1:44001/score`    
   İkinci örnek: çıkarım sunucu URL 'SI = `http://lvaExtension2:44001/score`
   
*GRPC protokolünü kullanın*: 

* Canlı video analizi modülü 1,0 ile, genel amaçlı bir uzak yordam çağrısı (gRPC) protokolü kullandığınızda bunu yapmanın tek yolu, gRPC sunucusunun farklı bağlantı noktaları aracılığıyla farklı AI modelleri kullanıma sunamadır. [Bu kod örneğinde](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/grpcExtensionOpenVINO/2.0/topology.json), tek bir bağlantı noktası olan 44000, tüm Yolo modellerini gösterir. Teorik olarak, bağlantı noktası 44000 ve diğerleri bağlantı noktası 45000 ' de bazı modelleri kullanıma sunmak için Yolo gRPC sunucusu yeniden yazılabilir. 

* Canlı video analizi modülü 2,0 ile gRPC uzantısı düğümüne yeni bir özellik eklenmiştir. Bu özellik olan **Extensionconfiguration**, GRPC sözleşmesinin bir parçası olarak kullanılabilen isteğe bağlı bir dizedir. Tek bir çıkarım sunucusunda paketlenmiş birden çok AI modeli olduğunda, her AI modeli için bir düğüm kullanıma almanız gerekmez. Bunun yerine, uzantı sağlayıcısı olarak bir grafik örneği için, **Extensionconfiguration** özelliğini kullanarak farklı AI modellerinin nasıl seçkullanılabileceğini tanımlayabilir. Yürütme sırasında, canlı video analizi bu dizeyi, istenen AI modelini çağırmak için onu kullanan ınstrıng sunucusuna geçirir. 

**Bir AI modeli etrafında gRPC sunucusu oluşturuyor ve birden çok kamera veya grafik örneği tarafından kullanımını destekleyebilmem istiyorum. My Server 'ı nasıl dermalıyım?** 

 İlk olarak, sunucunuzun aynı anda birden fazla isteği işleyebileceğinden veya paralel iş parçacıklarında çalışırken emin olun. 

Örneğin, aşağıdaki [canlı video analizi gRPC örneğinde](https://github.com/Azure/live-video-analytics/blob/master/utilities/video-analysis/notebooks/Yolo/yolov3/yolov3-grpc-icpu-onnx/lvaextension/server/server.py)varsayılan sayıda paralel kanal ayarlanır: 

```
server = grpc.server(futures.ThreadPoolExecutor(max_workers=3)) 
```

Önceki gRPC sunucusu örneklemesinde, sunucu her kamera için tek seferde veya grafik topolojisi örneği başına yalnızca üç kanalı açabilir. Sunucuya üçten fazla örnek bağlamayı denemeyin. Üçten fazla kanal açmaya çalışırsanız, var olan bir kanal düşene kadar istekler beklemede olur.  

Önceki gRPC sunucu uygulamasının Python örneklerimizde kullanılması. Bir geliştirici olarak, kendi sunucunuzu uygulayabilir veya önceki varsayılan uygulamayı kullanarak, video akışları için kullanılacak kamera sayısına göre ayarlayabilirsiniz. 

Birden çok kamera ayarlamak ve kullanmak için, her biri aynı veya farklı bir çıkarım sunucusuna (örneğin, önceki paragrafta bahsedilen sunucu) işaret eden birden çok Graf topolojisi örneği oluşturabilirsiniz. 

**Ters sınırlama kararı vermeden önce yukarı akış içinden birden çok kare alabilmesini istiyorum. Bunu nasıl etkinleştirebilirim?** 

Geçerli [varsayılan örneklerimiz](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis) *durum bilgisi olmayan* bir modda çalışıyor. Bunlar, önceki çağrıların durumunu veya çağıran hatta onları tutmazlar. Bu, birden çok topoloji örneğinin aynı çıkarım sunucusunu çağırabileceği anlamına gelir, ancak sunucu, çağıran başına ya da durum çağrısını ayırt edemez. 

*Http protokolünü kullanın*:

Durumu, her çağıranı veya Graf topolojisi örneğini tutmak için, arayan için benzersiz olan HTTP sorgu parametresini kullanarak ını sınırlama sunucusunu çağırır. Örneğin, her örnek için çıkarım sunucusu URL adresleri burada gösterilmiştir:  

Birinci topoloji örneği = `http://lvaExtension:44000/score?id=1`<br/>
2. topoloji örneği = `http://lvaExtension:44000/score?id=2`

… 

Sunucu tarafında, puan rotası kimin çağıran olduğunu bilir. KIMLIK = 1 ise, bu, çağıran veya grafik topolojisi örneği için durumu ayrı olarak tutabilir. Ardından, alınan video çerçevelerini bir arabellekte tutabilirsiniz. Örneğin, bir dizi veya bir tarih saat anahtarına sahip bir sözlük kullanın ve değer çerçevedir. Daha sonra, *x* kare sayısı alındıktan sonra işleyecek sunucuyu (çıkarım) tanımlayabilirsiniz. 

*GRPC protokolünü kullanın*: 

GRPC uzantısıyla her oturum tek bir kamera akışına yöneliktir, bu nedenle bir KIMLIK sağlanması gerekmez. Şimdi, extensionConfiguration özelliği ile video çerçevelerini bir arabellekte saklayabilir ve *x* kare sayısı alındıktan sonra işlenecek sunucuyu (çıkarım) tanımlayabilirsiniz. 

**Belirli bir kapsayıcıdaki tüm ProcessMediaStreams aynı AI modelini mi çalıştırsın?** 

Hayır. Bir grafik örneğindeki son kullanıcıdan gelen çağrıları Başlat veya Durdur bir oturum oluşturur veya belki de bir kamera bağlantısını kesme veya yeniden bağlanma. Hedef, kameranın video akışı olması halinde tek bir oturumu kalıcı hale maktır. 

* İşlenmek üzere video Gönderen iki kamera iki oturum oluşturur. 
* İki gRPC uzantısı düğümü olan bir grafiğe giden bir kamera iki oturum oluşturur. 

Her oturum, canlı video analizi ve gRPC sunucusu arasında tam çift yönlü bir bağlantıdır ve her bir oturumun farklı bir modeli veya işlem hattı olabilir. 

> [!NOTE]
> Kameranın bağlantısının kesilmesi veya yeniden bağlanmasına yönelik olarak, kamera tolerans sınırlarının ötesinde bir dönemde çevrimdışı olduğunda, canlı video analizi gRPC sunucusuyla yeni bir oturum açar. Sunucunun bu oturumlardaki durumu izlemesini sağlamak için gerekli değildir. 

Canlı video analizi, bir grafik örneğindeki tek bir kamera için birden çok gRPC uzantısı desteği de sağlar. Bu gRPC uzantılarını, hem paralel olarak hem de her ikisinin bir birleşimi olarak Birleşik bir şekilde yürütmek için kullanabilirsiniz. 

> [!NOTE]
> Paralel olarak birden çok uzantı çalıştırmak, donanım kaynaklarınızı etkiler. Hesaplama gereksinimlerinize uyan donanımları seçerken bunu göz önünde bulundurun. 

**Eşzamanlı ProcessMediaStreams sayısı üst sınırı nedir?** 

Canlı video analizi bu numaraya hiçbir sınır uygulanmaz.  

**Inverimin, CPU veya GPU mı yoksa başka bir donanım hızlandırıcımı kullanması gerektiğine nasıl karar veririm?** 

Kararınız, Geliştirilmiş AI modelinin karmaşıklığına ve CPU ve donanım hızlandırıcılarını nasıl kullanmak istediğinize bağlıdır. AI modelini geliştirirken, modelin kullanması gereken kaynakları ve hangi eylemlerin gerçekleştirileceğini belirtebilirsiniz. 

**Nasıl yaparım?, sınırlama kutuları ile görüntü işleme sonrası mi?** 

Bugün, yalnızca çıkarım iletileri olarak sınırlama kutusu koordinatları sunuyoruz. Bu iletileri kullanan ve video çerçevelerinde sınırlayıcı kutuları kaplayan özel bir MJPEG streammer oluşturabilirsiniz.  

## <a name="grpc-compatibility"></a>gRPC uyumluluğu 

**Medya akışı tanımlayıcısı için zorunlu alanların ne olduğunu nasıl anlarım?** 

Değer sağlamayan herhangi bir alana [, gRPC tarafından belirtilen şekilde varsayılan bir değer](https://developers.google.com/protocol-buffers/docs/proto3#default)verilir.  

Canlı video analizi, protokol arabelleği dilinin *proto3* sürümünü kullanır. Canlı video analizi sözleşmeleri tarafından kullanılan tüm protokol arabelleği verileri [protokol arabelleği dosyalarında](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc)kullanılabilir. 

**En son protokol arabelleği dosyalarını kullandığımı nasıl sağlayabilirsiniz?** 

En son protokol arabelleği dosyalarını, [sözleşme dosyaları sitesinde](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc)edinebilirsiniz. Sözleşme dosyalarını her güncelleştirtiğimiz zaman, bu konumda yer alırlar. Protokol dosyalarını güncelleştirmeye yönelik bir plan yoktur, bu nedenle sürümü bilmek için dosyaların en üstündeki paket adını arayın. Şunu okumalı: 

```
microsoft.azure.media.live_video_analytics.extensibility.grpc.v1 
```

Bu dosyalardaki tüm güncelleştirmeler, adın sonundaki "v-value" değerini artırır. 

> [!NOTE]
> Canlı video analizi dilin proto3 sürümünü kullandığından, alanlar isteğe bağlıdır ve sürüm geri ve ileri uyumlu olur. 

**Live video Analytics ile kullanmak için hangi gRPC özellikleri kullanılabilir? Hangi özellikler zorunludur ve isteğe bağlıdır?** 

Protokol arabellekleri (Protobellek) sözleşmesinin yerine getirilmesi durumunda herhangi bir sunucu tarafı gRPC özelliğini kullanabilirsiniz. 

## <a name="monitoring-and-metrics"></a>İzleme ve ölçümler

**Azure Event Grid kullanarak kenar üzerinde medya grafiğini izleyebilir miyim?**

Evet. Prometheus ölçümlerini kullanabilir ve bunları olay kılavuzunuzda yayımlayabilirsiniz. 

**Bulutta veya kenarda medya grafiklerimin sistem durumunu, ölçümlerini ve performansını görüntülemek için Azure Izleyicisini kullanabilir miyim?**

Evet, bu yaklaşımı destekliyoruz. Daha fazla bilgi için bkz. [Azure Izleyici ölçümlerine genel bakış](../../azure-monitor/essentials/data-platform-metrics.md).

**Media Services IoT Edge modülünü izlemeyi kolaylaştırmak için herhangi bir araç var mı?**

Visual Studio Code, LVAEdge modül uç noktalarını kolayca izleyebilmeniz için Azure IoT araçları uzantısını destekler. Bu aracı, IoT Hub 'ın yerleşik uç noktasını "olaylar" için izlemeye hızla başlamak ve uç cihazdan buluta yönlendirilen çıkarım iletilerini görüntülemek için kullanabilirsiniz. 

Ayrıca, bu uzantıyı kullanarak, LVAEdge modülünün modül ikizi 'yi düzenleyerek medya grafik ayarlarını değiştirebilirsiniz.

Daha fazla bilgi için bkz. [izleme ve günlüğe kaydetme](monitoring-logging.md) makalesi.

## <a name="billing-and-availability"></a>Faturalandırma ve kullanılabilirlik

**IoT Edge için canlı video analizi nasıl faturalandırılır?**

Faturalandırma ayrıntıları için bkz. [Media Services fiyatlandırması](https://azure.microsoft.com/pricing/details/media-services/).

## <a name="next-steps"></a>Sonraki adımlar

[Hızlı başlangıç: IoT Edge canlı video analiziyle çalışmaya başlama](get-started-detect-motion-emit-events-quickstart.md)
