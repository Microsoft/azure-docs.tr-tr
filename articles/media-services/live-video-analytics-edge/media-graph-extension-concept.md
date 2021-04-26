---
title: Medya grafiği uzantısı nedir? Azure
description: IoT Edge üzerinde canlı video analizi, bir grafik uzantısı düğümü aracılığıyla medya grafiği işleme yeteneklerini genişletmenizi sağlar.
ms.topic: overview
ms.date: 09/14/2020
ms.openlocfilehash: 4a7aea7cc60a67603d8a0376cf84228072659d6c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105557624"
---
# <a name="media-graph-extension"></a>Medya grafiği uzantısı

IoT Edge üzerinde canlı video analizi, bir grafik uzantısı düğümü aracılığıyla medya grafiği işleme yeteneklerini genişletmenizi sağlar. Analytics uzantı eklentisi, geleneksel görüntü işleme tekniklerinden veya bilgisayar Vision AI modellerinden kullanılabilir. Grafik uzantıları, bir ortam grafiğine uzantı işlemcisi düğümü eklenerek etkinleştirilir. Uzantı işlemcisi düğümü, video çerçevelerini yapılandırılmış uç noktaya geçirir ve uzantınızın arabirimi olarak görev yapar. Bağlantı, yerel veya uzak bir uç noktaya yapılabilir ve gerekirse, kimlik doğrulama ve TLS şifrelemesi ile güvenliği sağlanmış olabilir. Ayrıca, grafik uzantısı işlemci düğümü, video çerçevelerinin özel uzantınızı göndermeden önce isteğe bağlı ölçeklendirilmesine ve kodlamasına olanak sağlar. 

Canlı video analizi, iki tür medya grafiği uzantısı işlemciyi destekler:

* [HTTP uzantısı işlemcisi](media-graph-concept.md#http-extension-processor)
* [gRPC uzantı işlemcisi](media-graph-concept.md#grpc-extension-processor)

Grafik uzantısı düğümü, Analytics uzantı eklentisinin sonuçları JSON biçiminde döndürmesini bekler. İdeal olarak sonuçlar, [çıkarım meta veri şeması nesne modelini](./inference-metadata-schema.md)izlemelidir.

## <a name="http-extension-processor"></a>HTTP uzantısı işlemcisi

HTTP uzantısı işlemcisi, performans ve/veya en iyi kaynak kullanımının birincil sorun olmadığı [http protokolünü](./http-extension-protocol.md)kullanarak genişletilebilirlik senaryolarına izin verir. Bir HTTP REST uç noktası aracılığıyla kendi AI 'nizi bir medya grafiğinde kullanıma sunabilirsiniz. 

Şu durumlarda HTTP uzantı işlemcisi düğümünü kullan:

* Var olan HTTP ınlam sistemleri ile daha iyi birlikte çalışabilirlik istiyorsunuz.
* Düşük performanslı veri aktarımı kabul edilebilir.
* Canlı video analizi için basit bir istek-yanıt arabirimi kullanmak istiyorsunuz.

## <a name="grpc-extension-processor"></a>gRPC uzantı işlemcisi

gRPC uzantısı işlemcisi, gRPC tabanlı, yüksek performanslı [yapılandırılmış Protokolü](./grpc-extension-protocol.md)kullanan genişletilebilirlik senaryolarına izin vermez. Performans ve/veya en iyi kaynak kullanımının öncelik olduğu senaryolar için idealdir. GRPC uzantısı işlemcisi, yapılandırılmış veri tanımlarının tam avantajını almanızı sağlar. gRPC, aşağıdakileri kullanarak yüksek içerik aktarımı performansı sunar:

* [yerleşik paylaşılan bellek](https://en.wikipedia.org/wiki/Shared_memory) veya 
* içerik, gRPC iletilerinin gövdesine doğrudan katıştırılıyor. 

GRPC uzantı işlemcisi, medya özelliklerinin gönderilmesi için, çıkarım iletileri değiş tokuşu ile birlikte kullanılabilir.
Bu nedenle, şu durumlarda bir gRPC uzantı işlemcisi düğümü kullanın:

* Yapılandırılmış bir sözleşme (örneğin, istekler ve yanıtlar için yapılandırılmış iletiler) kullanmak ister
* İletişim için temeldeki ileti değişim biçimi olarak protokol arabellekleri ([Protobellek](https://developers.google.com/protocol-buffers)) kullanmak ister.
* Gelen istekleri ayrıştırmak ve doğru uygulama işlevlerini çağırmak için özel bir istek işleyicisine ihtiyaç duyan geleneksel istek-yanıt modeli yerine tek bir akış oturumunda bir gRPC sunucusuyla iletişim kurmak istiyorsunuz. 
* Canlı video analizi ve modülünüzün arasında düşük gecikme süresi ve yüksek aktarım hızı iletişimi ister.

## <a name="use-your-inferencing-model-with-live-video-analytics"></a>Canlı video analizi ile ınsıya sınırlama modelinizi kullanın

Medya grafiği uzantıları, tercih ettiğiniz çıkarım modellerini, ONNX, TensorFlow, PyTorch veya kendi Docker kapsayıcısındaki diğer kişiler gibi kullanılabilir bir çıkarma çalışma zamanı üzerinde çalıştırmanızı sağlar. En iyi performansı elde etmek için, Inse sınırlaması özel uzantısının canlı video analizi Edge modülü ile birlikte dağıtılması gerekir ve daha sonra, grafik topolojinize dahil edilen HTTP uzantısı işlemcisi veya gRPC uzantı işlemcisi aracılığıyla çağrılacaktır. Ayrıca, özel uzantıza yapılan çağrıların sıklığı isteğe bağlı olarak, medya uzantısı işlemcisine yukarı akış bir [hareket algılayıcısı işlemcisi](media-graph-concept.md#motion-detection-processor) eklenerek kısıtlanabilir.

Aşağıdaki diyagramda üst düzey veri akışı gösterilmektedir:

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/media-graph-extension/analyze-live-video-with-AI-inference-service.svg" alt-text="AI çıkarımı hizmeti":::

## <a name="samples"></a>Örnekler

Daha önceden oluşturulmuş uzantı hizmeti olan canlı video analizlerini, [http uzantısı işlemcisi](./use-your-model-quickstart.md?pivots=programming-language-csharp) Ile veya [GRPC uzantı işlemcisi](./analyze-live-video-use-your-grpc-model-quickstart.md?pivots=programming-language-csharp) ile yüksek kare ücretlerine sahip olan düşük kare ücretlerine sahip olan hızlı başlangıçlardan biriyle çalışmaya başlayın

Gelişmiş kullanıcılar için, canlı video analizi için [Jupyter Not defteri](https://github.com/Azure/live-video-analytics/blob/master/utilities/video-analysis/notebooks/readme.md) örneklerinden bazılarını kullanıma alabilirsiniz. Bu not defterleri size **medya grafik uzantıları** için adım adım yönergeler sağlar:

* Uzantı hizmetinin Docker kapsayıcı görüntüsü oluşturma
* Uzantı hizmetini bir kapsayıcı olarak ve canlı video analizi kapsayıcısı ile birlikte dağıtma
* Bir uzantı istemcisiyle canlı bir video analizi medya grafiğini kullanma ve uzantıyı uzantı uç noktasına (HTTP/gRPC) işaret etme