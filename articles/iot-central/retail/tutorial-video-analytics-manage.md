---
title: Öğretici-Azure IoT Central video analizi-nesne ve hareket algılama uygulama şablonunu kullanarak videoyu Izleyin
description: Bu öğreticide, kameraları yönetmek ve videoyu izlemek için video analizi-nesne ve hareket algılama uygulama şablonundaki panoların nasıl kullanılacağı gösterilmektedir.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.author: nandab
author: KishorIoT
ms.date: 07/31/2020
ms.openlocfilehash: fbfef094cd062e437f2a28369162de96631ef41b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99832615"
---
# <a name="tutorial-monitor-and-manage-a-video-analytics---object-and-motion-detection-application"></a>Öğretici: bir video analizini Izleme ve yönetme-nesne ve hareket algılama uygulaması

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:
> [!div class="checklist"]
> * IoT Central uygulamanıza nesne ve hareket algılama kameraları ekleyin.
> * Video akışlarınızı yönetin ve ilginç olaylar algılandığında yürütün.

## <a name="prerequisites"></a>Önkoşullar

Başlamadan önce şunları tamamlamalısınız:

* [Azure 'da canlı bir video analizi uygulaması oluşturun IoT Central](./tutorial-video-analytics-create-app-yolo-v3.md) veya [Azure IoT Central (openvino &trade; ) öğreticilerinde bir video analizi oluşturun](tutorial-video-analytics-create-app-openvino.md) .
* Önceki bir sürümü, [canlı video analizi (LINUX VM) için bir IoT Edge örneği oluşturun](tutorial-video-analytics-iot-edge-vm.md) veya [canlı video analizi (Intel nuc) öğreticileri için bir IoT Edge örneği oluşturun](tutorial-video-analytics-iot-edge-nuc.md) .

Video görüntüleyici uygulamasını çalıştırmak için yerel makinenizde [Docker](https://www.docker.com/products/docker-desktop) yüklü olmalıdır.

## <a name="add-an-object-detection-camera"></a>Nesne algılama Kamerası ekleme

IoT Central uygulamanızda, daha önce oluşturduğunuz **LVA Gateway 001** cihazına gidin. Ardından **Komutlar** sekmesini seçin.

Aşağıdaki tablodaki değerleri, **Kamera Isteği Ekle** komutunun parametre değerleri olarak kullanın. Tabloda gösterilen değerler, Azure VM 'deki sanal kamerayı kullandığınızı varsayıyoruz, gerçek bir kamera kullanıyorsanız, değerleri uygun şekilde ayarlayın:

| Alan| Açıklama| Örnek değer|
|---------|---------|---------|
| Kamera KIMLIĞI      | Sağlama için cihaz KIMLIĞI | Kamera-003 |
| Kamera adı    | Kolay ad           | Nesne algılama Kamerası |
| RTSP URL 'Si       | Akışın adresi   | RTSP://10.0.0.4:554/media/camera-300s.mkv|
| RTSP Kullanıcı adı  |                         | kullanıcı    |
| RTSP parolası  |                         | password    |
| Algılama türü | Açılan menü                | Nesne Algılama       |

Kamera cihazını eklemek için **Çalıştır** ' ı seçin:

:::image type="content" source="media/tutorial-video-analytics-manage/add-camera.png" alt-text="Kamera Ekle":::

> [!NOTE]
> **LVA Edge nesne algılayıcısı** cihaz şablonu uygulamada zaten var.

## <a name="add-a-motion-detection-camera-optional"></a>Hareket algılama Kamerası ekleme (isteğe bağlı)

IoT Edge ağ geçidi cihazınıza bağlı iki kamera varsa, uygulamaya bir hareket algılama Kamerası eklemek için önceki adımları tekrarlayın. **Kamera kimliği**, **Kamera adı** ve **RTSP URL** parametreleri için farklı değerler kullanın.

## <a name="view-the-downstream-devices"></a>Aşağı akış cihazlarını görüntüleme

Az önce eklediğiniz kamera cihazlarını görmek için **LVA Gateway 001** cihazının **aşağı akış cihazları** sekmesini seçin:

:::image type="content" source="media/tutorial-video-analytics-manage/inspect-downstream.png" alt-text="Bilgiyi":::

Kamera aygıtları, uygulamanın **cihazlar** sayfasındaki listede de görüntülenir.

## <a name="configure-and-manage-the-camera"></a>Kamerayı yapılandırma ve yönetme

**Camera-003** cihazına gidin ve **Yönet** sekmesini seçin.

Varsayılan değerleri kullanın veya cihaz özelliklerini özelleştirmeniz gerekiyorsa değiştirin:

**Kamera ayarları**

| Özellik | Açıklama | Önerilen Değer |
|-|-|-|
| Video kayıttan yürütme Konağı | Azure Media Player Görüntüleyicisi için ana bilgisayar | http://localhost:8094 |

**LVA Işlemleri ve tanılama**

| Özellik | Açıklama | Önerilen Değer |
|-|-|-|
| Otomatik Başlatma | LVA Gateway yeniden başlatıldığında nesne algılamayı Başlat | İşaretli |
| Hata ayıklama telemetrisi | Olay Izlemeleri | İsteğe Bağlı |
|Çıkarım zaman aşımı (sn)| Bu ınstaların durdurulduğunu belirlemede kullanılan zaman miktarı | 20 |

**AI nesne algılaması**

| Özellik | Açıklama | Önerilen Değer |
|-|-|-|
| Maksimum çıkarım yakalama süresi (sn) | En fazla yakalama süresi | 15 |
| Algılama sınıfları | Algılama etiketleriyle virgülle ayrılmış dizeler. Daha fazla bilgi için [desteklenen etiketlerin listesine](https://github.com/Azure/live-video-analytics/blob/master/utilities/video-analysis/yolov3-onnx/tags.txt) bakın | kamyon, Bus |
| Güvenilirlik eşiği | Nesne algılamanın geçerli olup olmadığını belirleme yüzdesi nitelendirme | 70 |
| Çıkarım çerçevesi örnek hızı (fps) | [Buraya Açıklama] | 2 |

**Kaydet**’i seçin.

Birkaç saniye sonra, her ayar için **kabul edilen** onay iletisini görürsünüz:

:::image type="content" source="media/tutorial-video-analytics-manage/object-detection.png" alt-text="Nesne Algıla":::

## <a name="start-lva-processing"></a>LVA işlemesini Başlat

**Camera-003** cihazına gidin ve **Komutlar** sekmesini seçin.

**Start LVA işleme** komutunu çalıştırın.

Komut tamamlandığında, hata olmadığından emin olmak için komut geçmişini görüntüleyin:

:::image type="content" source="media/tutorial-video-analytics-manage/start-processing.png" alt-text="LVA işleme komutunu Başlat":::

## <a name="monitor-the-cameras"></a>Kameraları izleme

**Camera-003** cihazına gidin ve **Pano** sekmesini seçin:

:::image type="content" source="media/tutorial-video-analytics-manage/camera-dashboard.png" alt-text="Kamera panosu":::

**Algılama sayısı** kutucuğu, tek saniyelik bir algılama aralığı sırasında seçili algılama sınıfları nesnelerinin her biri için Ortalama algılama sayısını gösterir.

**Algılama sınıfları** pasta grafiğinde her bir sınıf türü için algılamaların yüzdesi gösterilmektedir.

**Çıkarım olayı videosu** , algılamaları içeren Azure Media Services varlıkların bağlantılarının bir listesidir. Bağlantı, aşağıdaki bölümde açıklanan konak oynatıcıyı kullanır.

## <a name="start-the-streaming-endpoint"></a>Akış uç noktasını başlatma

İstemci Media Player uygulamanızın kaydedilmiş videoyu akışını sağlamak için Media Services uç noktasını başlatın:

* Azure portal, **LVA-RG** kaynak grubuna gidin.
* **Akış uç noktası** kaynağını açın.
* **Akış uç noktası ayrıntıları** sayfasında **Başlat**' ı seçin. Endpoint başladığında faturalandırma 'nin başlatıldığını belirten bir uyarı görürsünüz.

## <a name="view-stored-video"></a>Depolanan videoyu görüntüle

Kameraları izlerken ve şüpheli görüntüleri yeniden davranan günler daha fazla. Otomatik olay etiketleme ve saklanan videonun çıkarılan algılamayla doğrudan bağlantıları sayesinde, güvenlik işleçleri bir listede İlgilendiğiniz olayları bulabilir ve ardından videoyu görüntülemek için bağlantıyı izleyebilir.

Azure Media Services hesabınızda depolanan videoyu görüntülemek için [amp video oynatıcı](https://github.com/Azure/live-video-analytics/tree/master/utilities/amp-viewer) ' yı kullanabilirsiniz.

IoT Central uygulama, video akışını oluşturabileceğiniz yerden Azure Media Services depolar. Azure Media Services depolanan videoyu oynatmak için bir video oynatıcı gerekir.

Yerel makinenizde **Docker** 'ın çalıştığından emin olun.

Bir komut istemi açın ve video oynatıcıyı yerel makinenizde bir Docker kapsayıcısında çalıştırmak için aşağıdaki komutu kullanın. Komutta yer tutucuları, *scratchpad.txt* dosyasında daha önce bir nota yaptığınız değerlerle değiştirin. `amsAadClientId` `amsAadSecret` `amsAadTenantId` `amsSubscriptionId` `amsAccountName` Media Services hesabınız için hizmet sorumlusu oluştururken,,, ve ' yi bir yere görürsünüz:

```bash
docker run -it --rm -e amsAadClientId="<FROM_AZURE_PORTAL>" -e amsAadSecret="<FROM_AZURE_PORTAL>" -e amsAadTenantId="<FROM_AZURE_PORTAL>" -e amsArmAadAudience="https://management.core.windows.net" -e amsArmEndpoint="https://management.azure.com" -e amsAadEndpoint="https://login.microsoftonline.com" -e amsSubscriptionId="<FROM_AZURE_PORTAL>" -e amsResourceGroup="<FROM_AZURE_PORTAL>" -e amsAccountName="<FROM_AZURE_PORTAL>" -p 8094:8094 mcr.microsoft.com/lva-utilities/amp-viewer:1.0-amd64
```

|Docker parametresi | AMS API erişimi (JSON)|
|-|-|
|Amsaadclientıd| Aadclientıd|
|amsAadSecret| AadSecret |
|Amsaadtenantıd| Aadtenantıd |
|Amssubscriptionıd| SubscriptionId|
|amsResourceGroup| adlı yönetilen örnek, |
|amsAccountName| AccountName|

**Camera-003** cihazına gidin ve **Pano** sekmesini seçin. Ardından, **çıkarım olayı video** kutucuğunda yakalanan nesne algılama köprülerinin birine tıklayın. Video, yerel video oynatıcı tarafından görüntülenen bir sayfada görünür:

:::image type="content" source="media/tutorial-video-analytics-manage/video-snippet.png" alt-text="Video kod parçacığı":::

## <a name="change-the-simulated-devices-in-application-dashboards"></a>Uygulama panolarındaki sanal cihazları değiştirme

Uygulama panoları başlangıçta IoT Central sanal cihazlardan oluşturulan telemetri ve özelliklerle doldurulur. Kutucukları gerçek kameralardan veya Live555 benzeticisinden telemetri olarak yapılandırmak için aşağıdaki adımları izleyin:

1. **(Örnek) gerçek kamera İzleyicisi** uygulama panosuna gidin.
1. **Düzenle**'yi seçin.
1. **Dekont** kutucuğunu seçin ve silin.
1. Pano başlığını *gerçek kamera Izleyicisine* değiştirin.
1. **Çıkarım sayısı** kutucuğunda Yapılandır simgesini seçin.
1. **Grafiği Yapılandır** bölümünde, **LVA Edge nesne algılayıcısı** cihaz grubundaki bir veya daha fazla gerçek kameraları seçin.
1. `AI Inference Interface/Inference Count`Telemetri alanını seçin.
1. **Güncelleştir**’i seçin.

1. Aşağıdaki kutucuklar için adımları yineleyin:
    1. **Algılama** pasta grafiği `AI Inference Interface/Inference/entity/tag/value` telemetri türünü kullanır.
    1. **Çıkarım** , `AI Inference Interface/Inference/entity/tag/value` bilinen son değeri kullanır.
    1. **Güven%** `AI Inference Interface/Inference/entity/tag/confidence` bilinen son değeri kullanır.
    1. **Anlık görüntü** , `AI Inference Interface/Inference Image` görüntü olarak gösterilen kullanımlar.
    1. **Çıkarım olayı videosu** `AI Inference Interface/Inference Event Video` bağlantı olarak gösterilir.
1. **Kaydet**’i seçin.

**Gerçek kamera İzleyicisi** panosu artık gerçek kamera cihazınızdan değerleri gösterir:

:::image type="content" source="media/tutorial-video-analytics-manage/update-real-cameras.png" alt-text="Gerçek kameralar uygulama panosu":::

## <a name="pause-processing"></a>İşlemeyi Duraklat

Uygulamada canlı video analizi işlemini duraklatabilirsiniz:

1. Uygulamanızda nesne algılama kamerası için **Komutlar** sayfasına gidin. Ardından **Durdur LVA işleme** komutunu çalıştırın.

1. Media Services hesabınız için akış uç noktasını durdurun:
    * Azure portal, **LVA-RG** kaynak grubuna gidin.
    * **Akış uç noktası** kaynağına tıklayın.
    * **Akış uç noktası ayrıntıları** sayfasında **Durdur**' u seçin.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Uygulamayı tamamladıysanız, oluşturduğunuz tüm kaynakları aşağıdaki şekilde kaldırabilirsiniz:

1. IoT Central uygulamasında, **Yönetim** bölümünde **uygulamanızın** sayfasına gidin. Ardından **Sil**’i seçin.
1. Azure portal, **LVA-RG** kaynak grubunu silin.
1. Yerel makinenizde **amp-Viewer** Docker kapsayıcısını durdurun.

## <a name="next-steps"></a>Sonraki adımlar

Artık IoT Central uygulamasına kamera eklemeyi ve bunları nesne ve hareket algılama için yapılandırmayı öğrendiniz.

IoT Edge modülleri için kaynak kodu özelleştirmeyi öğrenmek için:

> [!div class="nextstepaction"]
> [Canlı video analizi ağ geçidi modüllerini değiştirme ve derleme](./tutorial-video-analytics-build-module.md)
