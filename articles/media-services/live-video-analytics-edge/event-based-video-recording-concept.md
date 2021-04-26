---
title: Olay tabanlı video kaydı-Azure
description: Olay tabanlı video kaydı (EVR), bir olay tarafından tetiklenen videoyu kaydetme sürecini ifade eder. Söz konusu olay, video sinyalinin işlenmesi (örneğin, hareket üzerinde algılama) veya bağımsız bir kaynaktan (örneğin, bir kapı açılaması) olabilir.  Olay tabanlı video kaydıyla ilgili birkaç kullanım durumu bu makalede açıklanmıştır.
ms.topic: conceptual
ms.date: 05/27/2020
ms.openlocfilehash: 6a5f4873b2cfef8d9a6594916d82cd30a3bc1cc2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97401615"
---
# <a name="event-based-video-recording"></a>Olay tabanlı video kaydı  
 
## <a name="suggested-pre-reading"></a>Önerilen önceden okuma  

* [Sürekli video kaydı](continuous-video-recording-concept.md)
* [Kaydedilen içeriğin kayıttan yürütülmesi](video-playback-concept.md)
* [Medya grafiği kavramı](media-graph-concept.md)

## <a name="overview"></a>Genel Bakış 

Olay tabanlı video kaydı (EVR), bir olay tarafından tetiklenen videoyu kaydetme sürecini ifade eder. Söz konusu olay, video sinyalinin işlenmesi (örneğin, hareket üzerinde algılama) veya bağımsız bir kaynaktan (örneğin, bir kapı açılaması) olabilir. 

Bir CCTV kameradan, bir [RTSP kaynak](media-graph-concept.md#rtsp-source) düğümü, bir [varlık havuz](media-graph-concept.md#asset-sink) düğümü ve aşağıdaki kullanım durumlarında özetlenen diğer düğümleri içeren bir medya grafı kullanarak bir e-posta (bir olay tarafından tetiklenir) Media Services bir kıymete yazdırabilirsiniz. Her bir olaya karşılık gelen videonun kendi varlığı içinde olması için, [varlık havuzu](media-graph-concept.md#asset-sink) düğümünü her bir olay gerçekleştiğinde yeni varlıklar oluşturacak şekilde yapılandırabilirsiniz. Ayrıca, tüm olaylar için videoyu tutmak üzere bir varlık kullanmayı da seçebilirsiniz. 

Varlık havuzu düğümüne alternatif olarak, bir [Dosya havuzu](media-graph-concept.md#file-sink) düğümünü kullanarak, uç cihazdaki yerel dosya sisteminde belirli bir konuma video (ilgi bir olayla ilişkili olan) kısa kod parçacıklarını yakalayabilirsiniz. 

Olay tabanlı video kaydıyla ilgili birkaç kullanım durumu bu makalede açıklanmıştır.

### <a name="video-recording-based-on-motion-detection"></a>Hareket algılamayı temel alan video kaydı  

Bu kullanım durumunda video kliplerini yalnızca videoda bir hareket olduğunda kaydedebilir ve böyle bir video klip oluşturulduğunda uyarı alabilirsiniz. Bu, kritik altyapının korumasını içeren ticari güvenlik senaryolarıyla ilgili olabilir. Beklenmedik hareket algılandığında uyarılar oluşturup video kaydederek insan işlecinin verimliliğini geliştirebilirsiniz, çünkü eylem yalnızca uyarı oluşturulduğunda gereklidir.

Aşağıdaki diyagramda, bu kullanım örneğini ele alan bir medya grafiğinin grafik temsili gösterilmektedir. Bu tür bir medya grafiğinin grafik topolojisinin JSON temsili [burada](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/evr-motion-assets/topology.json)bulunabilir.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-based-video-recording/motion-detection.svg" alt-text="Hareket algılamayı temel alan video kaydı":::

Diyagramda, RTSP kaynak düğümü, kameradan canlı video akışını yakalar ve bunu bir [hareket algılama işlemcisi](media-graph-concept.md#motion-detection-processor) düğümüne gönderir. Canlı videoda hareket algılandıktan sonra, hareket algılama işlemcisi düğümü, [sinyal kapısı işlemci](media-graph-concept.md#signal-gate-processor) düğümüne ve IoT Hub ileti havuzu düğümüne gidecek bir olay oluşturur. İkinci düğüm, uyarıları tetiklemek için diğer hedeflere yönlendirilebileceği IoT Edge hub 'ına olayları gönderir. 

Hareket algılayıcısı düğümünden bir olay, sinyal kapısı işlemci düğümünü tetikler ve RTSP kaynak düğümünden gelen canlı video akışının varlık havuzu düğümüne akmasını sağlar. Sonraki bu hareket algılama olaylarının yokluğu, ağ geçidi yapılandırılan bir süre sonra kapatılacak. Bu, kaydedilen videonun süresini belirler.

### <a name="video-recording-based-on-events-from-other-sources"></a>Diğer kaynaklardaki olaylara dayalı video kaydı  

Bu kullanım durumunda, başka bir IoT sensörden gelen sinyaller video kaydını tetiklemek için kullanılabilir. Aşağıdaki diyagramda, bu kullanım örneğini ele alan bir medya grafiğinin grafik temsili gösterilmektedir. Bu tür bir medya grafiğinin grafik topolojisinin JSON temsili [burada](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/evr-hubMessage-files/topology.json)bulunabilir.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-based-video-recording/other-sources.svg" alt-text="Diğer kaynaklardaki olaylara dayalı video kaydı":::

Diyagramda, dış algılayıcı olayları IoT Edge hub 'ına gönderir. Daha sonra olaylar [IoT Hub ileti kaynağı](media-graph-concept.md#iot-hub-message-source) düğümü aracılığıyla sinyal kapısı işlemci düğümüne yönlendirilir. Sinyal kapısı işlemci düğümünün davranışı, önceki kullanım örneği ile aynıdır. Bu işlem açılır ve canlı video akışının, dış olay tarafından tetiklendiğinde RTSP kaynak düğümünden dosya havuzu düğümüne (veya varlık havuz düğümü) üzerinden akmasını sağlar. 

Bir dosya havuzu düğümü kullanıyorsanız, video Edge cihazında yerel dosya sistemine kaydedilir. Aksi takdirde, bir varlık havuzu düğümü kullanıyorsanız, video bir varlığa kaydedilir.

### <a name="video-recording-based-on-an-external-inferencing-module"></a>Harici bir ının sınırlaması modülünü temel alan video kaydı 

Bu kullanım durumunda, bir dış mantıksal sistemden bir sinyal temelinde video klipleri kaydedebilirsiniz. Bu tür bir kullanım örneğine örnek olarak bir video klibini yalnızca trafiğin video akışında highbir biçimde algılandığında bir örnek kayıt olabilir. Aşağıdaki diyagramda, bu kullanım örneğini ele alan bir medya grafiğinin grafik temsili gösterilmektedir. Bu tür bir medya grafiğinin grafik topolojisinin JSON temsili [burada](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/evr-hubMessage-assets/topology.json)bulunabilir.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-based-video-recording/external-inferencing-module.svg" alt-text="Harici bir ının sınırlaması modülünü temel alan video kaydı":::

Diyagramda, RTSP kaynak düğümü, kameradan canlı video akışını yakalar ve iki dala sunar: biri bir [sinyal kapısı işlemci](media-graph-concept.md#signal-gate-processor) düğümüne sahiptir ve diğeri dış mantık modülüne veri göndermek Için bir [http uzantısı](media-graph-concept.md) düğümü kullanır. HTTP uzantısı düğümü, medya grafiğinin, bekleyen bir dış çıkarım hizmetine görüntü çerçeveleri (JPEG, BMP veya PNG biçiminde) göndermesini sağlar. Bu sinyal yolu, genellikle yalnızca düşük kare oranlarını destekleyebilir (<5fps). Dış Inse sınırlaması modülüne giden videonun kare oranını düşürmek için HTTP uzantısı işlemci düğümünü kullanabilirsiniz.

Dış çıkarım hizmetinin sonuçları HTTP uzantısı düğümü tarafından alınır ve IoT Hub ileti havuzu düğümü aracılığıyla IoT Edge hub 'ına aktarıldıklarında dış mantık modülü tarafından daha fazla işlenebilir. Çıkarım hizmeti, örneğin, bir veri yolu veya kamyonu gibi belirli bir araç arayabilir. Logic Module ilgilendiğiniz nesneyi algıladığında, IoT Edge hub 'ı aracılığıyla grafikteki IoT Hub ileti kaynağı düğümüne bir olay göndererek sinyal kapısı işlemci düğümünü tetikleyebilirsiniz. Sinyal kapısındaki çıkış, bir dosya havuzu düğümüne veya bir varlık havuzu düğümüne gitmek için gösterilir. Önceki durumda, video Edge cihazında yerel dosya sistemine kaydedilir. İkinci durumda, video bir varlığa kaydedilir.

Bu örnek için bir geliştirme, HTTP uzantısı işlemci düğümünden önce bir hareket algılayıcısı işlemcisi kullanmaktır. Bu, üst bir yol üzerinde hiçbir yol olmadığında uzun süreler olması gibi, çıkarım hizmetindeki yükü azaltır. 

## <a name="next-steps"></a>Sonraki adımlar

[Öğretici: olay tabanlı video kaydı](event-based-video-recording-tutorial.md)
