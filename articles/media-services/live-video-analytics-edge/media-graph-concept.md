---
title: Medya grafiği kavramı-Azure
description: Medya grafiği, medyanın nerede yakalanabileceğini, nasıl işleneceğini ve sonuçların nereye teslim edileceğini tanımlamanızı sağlar. Bu makale, medya grafiği kavramının ayrıntılı bir açıklamasını vermektedir.
ms.topic: conceptual
ms.date: 05/01/2020
ms.openlocfilehash: ad23acbbbdd0c15e92e471ee22a229470a8a3a75
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105557675"
---
# <a name="media-graph"></a>Medya grafiği

## <a name="suggested-pre-reading"></a>Önerilen önceden okuma

* [IoT Edge genel bakış üzerinde canlı video analizi](overview.md)
* [IoT Edge terminolojisinde canlı video analizi](terminology.md)

## <a name="overview"></a>Genel Bakış

Medya grafiği, medyanın nerede yakalanabileceğini, nasıl işleneceğini ve sonuçların nereye teslim edileceğini tanımlamanızı sağlar. Bunu, bileşenleri veya düğümleri istenen şekilde bağlayarak gerçekleştirirsiniz. Aşağıdaki diyagramda bir medya grafiğinin grafik temsili verilmiştir.  

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/media-graph/media-graph.svg" alt-text="Medya grafiği":::

IoT Edge üzerindeki canlı video analizi, farklı kaynak, işlemci ve havuz türlerini destekler.

* **Kaynak düğümleri** medya grafiğine medya yakalamaya olanak sağlar. Bu bağlamdaki medya, kavramsal olarak bir ses akışı, video akışı, veri akışı veya ses, video ve/veya veri içeren bir akış, tek bir akışta birlikte birleştirilir.
* **İşlemci düğümleri** medya grafiğinde Medya işlemeyi etkinleştirir.
* **Havuz düğümleri** , işleme sonuçlarının medya grafiğinin dışındaki hizmetlere ve uygulamalara teslim edilmesini sağlar.

## <a name="media-graph-topologies-and-instances"></a>Medya grafiği topolojileri ve örnekleri 

IoT Edge üzerindeki canlı video analizi, medya grafiklerini "Graf topolojisi" ve "Graf örneği" olmak üzere iki kavram aracılığıyla yönetmenizi sağlar. Graph topolojisi, parametreler içeren bir grafiğin şeması, değerler için yer tutucu olarak tanımlamanızı sağlar. Topoloji, medya grafiğinde kullanılan düğümleri ve bunların medya grafiğinde nasıl bağlandığını tanımlar. Örneğin, akışı bir kameradan kaydetmek isterseniz, video alan bir kaynak düğümü ve videoyu yazan bir havuz düğümünü içeren bir grafiğe ihtiyacınız vardır.

Topolojideki parametrelerin değerleri, topolojiye başvuran grafik örnekleri oluşturduğunuzda belirtilir. Bu, aynı topolojiye başvuruda bulunan ancak topolojide belirtilen parametrelere ait farklı değerlerle birden çok örnek oluşturmanıza olanak sağlar. Yukarıdaki örnekte, kameranın IP adresini ve kaydedilen videonun adını temsil etmek için kullanılan parametreleri kullanabilirsiniz. Bir binadaki her kamera için tek bir örnek olan, her biri belirli IP adresine ve belirli bir ada sahip olan çok sayıda grafik örneği oluşturabilirsiniz.

## <a name="media-graph-states"></a>Medya grafiği durumları  

Grafik topolojileri ve grafik örneklerinin yaşam döngüsü aşağıdaki durum diyagramında gösterilmiştir.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/media-graph/graph-topology-lifecycle.svg" alt-text="Graph topolojisi ve grafik örneği yaşam döngüsü":::

[Graph topolojisi oluşturmaya](direct-methods.md#graphtopologyset)başlayabilirsiniz. Ardından, Bu topolojiyle işlemek istediğiniz her canlı video akışı için [bir grafik örneği oluşturursunuz](direct-methods.md#graphinstanceset). 

Grafik örneği `Inactive` (boş) durumunda olacaktır.

Canlı video akışını grafik örneğine göndermeye hazırsanız, uygulamayı [etkinleştirirsiniz](direct-methods.md#graphinstanceactivate) . Grafik örneği, geçişli bir duruma göre kısa bir süre boyunca gider `Activating` ve başarılı olursa bir `Active` duruma geçer. `Active`Bu durumda, medya işlenir (grafik örneği giriş verilerini alırsa).

> [!NOTE]
>  Grafik örneği, üzerinden veri akışı olmadan etkin olabilir (örneğin, kamera çevrimdışı olur).
> Grafik örneği etkin durumundayken Azure aboneliğiniz faturalandırılır.

İşlemek için diğer canlı video akışlarınız varsa, aynı topoloji için diğer grafik örneklerini oluşturma ve etkinleştirme sürecini tekrarlayabilirsiniz.

Canlı video akışını işlemeyi tamamladığınızda, grafik örneğini [devre dışı](direct-methods.md#graphinstancedeactivate) bırakabilirsiniz. Grafik örneği, geçişli bir durumdan daha kısa sürer `Deactivating` , sahip olduğu tüm verileri temizler ve sonra duruma geri döner `Inactive` .

Yalnızca durumundayken bir grafik örneğini [silebilirsiniz](direct-methods.md#graphinstancedelete) `Inactive` .

Belirli bir grafik topolojisine başvuran tüm grafik örnekleri silinmişse, [grafik topolojisini silebilirsiniz](direct-methods.md#graphtopologydelete).


## <a name="sources-processors-and-sinks"></a>Kaynaklar, işlemciler ve havuzlar  

IoT Edge canlı video analizi, bir medya grafiğinde aşağıdaki düğüm türlerini destekler:

### <a name="sources"></a>Kaynaklar 

#### <a name="rtsp-source"></a>RTSP kaynağı 

Bir RTSP kaynak düğümü, bir [RTSP](https://tools.ietf.org/html/rfc2326) sunucusundan medya almanıza olanak sağlar. İzleme ve IP tabanlı kameralar, verileri telefon ve video kameralar gibi diğer cihaz türlerinden farklı olarak RTSP (gerçek zamanlı akış-protokol) adlı bir protokolde iletir. Bu protokol, bir sunucu (kamera) ile istemci arasındaki medya oturumlarını oluşturmak ve denetlemek için kullanılır. Bir medya grafiğindeki RTSP kaynak düğümü istemci olarak davranır ve bir RTSP sunucusuyla oturum oluşturabilir. Çoğu [IP kamerada](https://en.wikipedia.org/wiki/IP_camera) gibi birçok cihazın YERLEŞIK bir RTSP sunucusu vardır. [ONVIF](https://www.onvif.org/) MANTARIHLERIN RTSP [, S & T](https://www.onvif.org/wp-content/uploads/2019/12/ONVIF_Profile_Feature_overview_v2-3.pdf) uyumlu cihazların tanımına göre desteklenir. RTSP kaynak düğümü, kimliği doğrulanmış bir bağlantıyı etkinleştirmek için kimlik bilgileriyle birlikte bir RTSP URL 'SI belirtmenizi gerektirir.

#### <a name="iot-hub-message-source"></a>IoT Hub ileti kaynağı 

Diğer [IoT Edge modüller](../../iot-fundamentals/iot-glossary.md#iot-edge)gibi, IoT Edge modüldeki canlı video analizi, [IoT Edge hub](../../iot-fundamentals/iot-glossary.md#iot-edge-hub)aracılığıyla iletiler alabilir. Bu iletiler diğer modüllerden veya sınır cihazında ya da buluttan çalışan uygulamalardan gönderilebilir. Bu tür iletiler, modüldeki [adlandırılmış bir girişe](../../iot-edge/module-composition.md#sink) dağıtılır (yönlendirilir). IoT Hub ileti kaynağı düğümü, bu tür iletilerin medya grafiğine ulaşmasını sağlar. Bu iletiler veya sinyaller, genellikle sinyal kapılarını etkinleştirmek için medya grafiğinde dahili olarak kullanılabilir (aşağıdaki [sinyal kapıları](#signal-gate-processor) bölümüne bakın). 

Örneğin, bir kapı açıldığında ileti üreten bir IoT Edge modüle sahip olabilirsiniz. Bu modülden gelen ileti, daha sonra bir medya grafiğinin IoT Hub 'ı ileti kaynağına yönlendirilebileceği IoT Edge hub 'a yönlendirilebilir. Medya grafiğinde, IoT Hub ileti kaynağı olayı bir sinyal kapısı işlemcisine geçirebilir, bu da daha sonra videonun bir RTSP kaynağından dosyaya kaydedilmesini etkinleştirebilir. 

### <a name="processors"></a>İşlemciler  

#### <a name="motion-detection-processor"></a>Hareket algılama işlemcisi 

Hareket algılama işlemcisi düğümü, canlı video 'daki hareketi algılamanıza olanak sağlar. Gelen video çerçevelerini inceler ve videoda hareket olup olmadığını belirler. Hareket algılanırsa, video çerçevelerini aşağı akış bileşenine geçirir ve bir olay yayar. Hareket algılama işlemcisi düğümü (diğer düğümlerle birlikte), algılanan bir işlem olduğunda gelen videonun kaydını tetikleyebilmek için kullanılabilir.

#### <a name="frame-rate-filter-processor"></a>Çerçeve hızı filtre işlemcisi  

Çerçeve hızı filtresi işlemci düğümü, gelen video akışından belirtilen bir hızda çerçeveler örneklemenizi sağlar. Bu, daha fazla işleme için aşağı akış bileşenlerine (HTTP uzantısı işlemci düğümü gibi) gönderilen çerçevelerin sayısını azaltmanıza olanak sağlar.
>[!WARNING]
> Bu işlemci, IoT Edge modülündeki canlı video analizinin en son sürümünde **kullanımdan kaldırılmıştır** . Çerçeve hızı yönetimi artık grafik uzantısı işlemcilerinin içinde destekleniyor.

#### <a name="http-extension-processor"></a>HTTP uzantısı işlemcisi

HTTP uzantısı işlemci düğümü, kendi IoT Edge modülünüzü bir medya grafiğine bağlamanıza olanak sağlar. Bu düğüm, giriş olarak kodu çözülmüş video çerçevelerini alır ve bu çerçeveleri modülünüzün açığa çıkarılan bir HTTP REST uç noktasına geçirir. Bu düğüm, gerekirse REST uç noktasıyla kimlik doğrulaması yapabilir. Ayrıca, düğümün REST uç noktasına geçmeden önce video çerçevelerini ölçeklendirmeye ve kodlamaya yönelik yerleşik bir görüntü biçimlendirici vardır. Scaler, görüntü en boy oranının korunmasıyla, doldurulmuş veya esneme yönelik seçeneklere sahiptir. Görüntü Kodlayıcısı JPEG, PNG veya BMP biçimlerini destekler. [İşlemci hakkında](media-graph-extension-concept.md#http-extension-processor)daha fazla bilgi edinin.

#### <a name="grpc-extension-processor"></a>gRPC uzantı işlemcisi

GRPC uzantısı işlemci düğümü, kodu çözülmüş video çerçevelerini giriş olarak alır ve bu çerçeveleri modülünüzün açığa çıkarılan bir [GRPC](terminology.md#grpc) uç noktasına geçirir. Düğüm, [paylaşılan bellek](https://en.wikipedia.org/wiki/Shared_memory) kullanarak verilerin aktarımını veya doğrudan GRPC iletilerinin gövdesine içerik katıştırmayı destekler. Ayrıca, düğüm, gRPC uç noktasına geçmeden önce video çerçevelerini ölçeklendirmeye ve kodlamaya yönelik yerleşik bir görüntü biçimlendirici içerir. Scaler, görüntü en boy oranının korunmasıyla, doldurulmuş veya esneme yönelik seçeneklere sahiptir. Görüntü Kodlayıcısı JPEG, PNG veya BMP biçimlerini destekler. [İşlemci hakkında](media-graph-extension-concept.md#grpc-extension-processor)daha fazla bilgi edinin.

#### <a name="signal-gate-processor"></a>Sinyal kapısı işlemcisi  

Sinyal kapısı işlemci düğümü, medyayı bir düğümden diğerine koşullu olarak iletmenizi sağlar. Ayrıca, medya ve olay eşitlemesine izin veren bir arabellek görevi görür. Tipik bir kullanım durumu, RTSP kaynak düğümü ve varlık havuz düğümü arasında bir sinyal kapısı işlemci düğümü eklemek ve ağ geçidini tetiklemek için bir hareket algılayıcısı işlemci düğümünün çıkışını kullanmaktır. Böyle bir medya grafiğinde, yalnızca hareket algılandığında video kaydı yapabilirsiniz.

### <a name="sinks"></a>Yapma  

#### <a name="asset-sink"></a>Varlık havuzu  

Bir varlık havuzu düğümü, bir Azure Media Services varlığına medya (video ve/veya ses) verileri yazmanızı sağlar. Bir medya grafiğinde yalnızca bir varlık havuzu düğümü olabilir. Varlıklar hakkında daha fazla bilgi için [varlık](terminology.md#asset) bölümüne ve medya kaydetme ve kayıttan yürütme içindeki rolüyle ilgili daha fazla bilgi için bkz.. Ayrıca, bu düğümün özelliklerinin nasıl kullanıldığı hakkında ayrıntılı bilgi edinmek için [sürekli video kaydetme](continuous-video-recording-concept.md) makalesini de görebilirsiniz.

#### <a name="file-sink"></a>Dosya havuzu  

Dosya havuzu düğümü, IoT Edge cihazının yerel dosya sistemindeki bir konuma medya (video ve/veya ses) verileri yazmanızı sağlar. Bir medya grafiğinde yalnızca bir dosya havuzu düğümü olabilir ve sinyal kapısı işlemci düğümünün bir aşağı akış olması gerekir. Bu, çıkış dosyalarının süresini sinyal kapısı işlemci düğümü özelliklerinde belirtilen değerlerle sınırlandırır. Edge cihazınızın disk alanı tükendiğinden emin olmak için, IoT Edge modülündeki canlı video analizinin verileri depolamak için kullanabileceği en büyük boyutu da ayarlayabilirsiniz.  
> [!NOTE]
Dosya havuzu tam alırsa, IoT Edge modüldeki canlı video analizi en eski verileri silmeye başlar ve yeni bir dosyayla değiştirir.
#### <a name="iot-hub-message-sink"></a>IoT Hub ileti havuzu  

Bir IoT Hub ileti havuzu düğümü, olayları IoT Edge hub 'a yayımlamanıza olanak sağlar. IoT Edge Hub daha sonra verileri uç cihazdaki diğer modüllere veya uygulamalara yönlendirebilir veya bulutta IoT Hub (dağıtım bildiriminde belirtilen yollar başına). IoT Hub ileti havuzu düğümü, bir hareket algılama işlemcisi düğümü veya bir dış çıkarım hizmetinden bir HTTP uzantısı işlemci düğümü aracılığıyla olayları kabul edebilir.

## <a name="rules-on-the-use-of-nodes"></a>Düğümlerin kullanımıyla ilgili kurallar

Farklı düğümlerin bir medya grafiğinde nasıl kullanılabileceği hakkında ek kurallar için bkz. [grafik topolojileriyle ilgili sınırlamalar](quotas-limitations.md#limitations-on-graph-topologies-at-preview) .

## <a name="scenarios"></a>Senaryolar

Yukarıda tanımlanan kaynak, işlemci ve havuz birleşimini kullanarak, canlı video analiziyle ilgili çeşitli senaryolar için medya grafikleri oluşturabilirsiniz. Örnek senaryolar şunlardır:

* [Sürekli video kaydı](continuous-video-recording-concept.md)
* [Olay tabanlı video kaydı](event-based-video-recording-concept.md)
* [Video kaydı olmadan Canlı Video Analizi](analyze-live-video-concept.md)

## <a name="next-steps"></a>Sonraki adımlar

Canlı video akışında hareket algılamayı nasıl çalıştıracağınızı görmek için bkz. [hızlı başlangıç: kendi modelinizle canlı video analizi çalıştırma](use-your-model-quickstart.md).
