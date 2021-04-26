---
title: IoT Edge anomali algılayıcısı çalıştırma
titleSuffix: Azure Cognitive Services
description: Anomali algılayıcı modülünü IoT Edge için dağıtın.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 12/03/2020
ms.author: mbullwin
ms.openlocfilehash: b4153b07b153a9ee0b16dc032ab5e7810e236d7d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "98936268"
---
# <a name="deploy-an-anomaly-detector-module-to-iot-edge"></a>IoT Edge için anomali algılayıcı modülünü dağıtın

Bilişsel Hizmetler [anomali algılayıcısı](../anomaly-detector-container-howto.md) modülünü bir IoT Edge cihazına dağıtmayı öğrenin. IoT Edge ' ye dağıtıldıktan sonra modül, kapsayıcı örnekleri olarak diğer modüllerle birlikte IoT Edge birlikte çalışır. Standart bir Docker kapsayıcı ortamında çalışan anomali algılayıcı kapsayıcısı örneğiyle tam olarak aynı API 'Leri sunar. 

## <a name="prerequisites"></a>Önkoşullar

* Bir Azure aboneliği kullanın. Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free) oluşturun.
* [Azure CLI](/cli/azure/install-azure-cli)'yı yükler.
* [IoT Hub](../../../iot-hub/iot-hub-create-through-portal.md) ve [IoT Edge](../../../iot-edge/quickstart-linux.md) bir cihaz.

[!INCLUDE [Create a Cognitive Services Anomaly Detector resource](../includes/create-anomaly-detector-resource.md)]

## <a name="deploy-the-anomaly-detection-module-to-the-edge"></a>Anomali algılama modülünü kenara dağıtın

1. Azure portal, aramaya **IoT Edge üzerinde anomali algılayıcısı** girin ve Azure Marketi sonucunu açın.
2. Bu işlem sizi [IoT Edge modülü için Azure Portal hedef cihazlarına](https://portal.azure.com/#create/azure-cognitive-service.edge-anomaly-detector)götürür. Şu gerekli bilgileri belirtin.

    1. Aboneliğinizi seçin.

    1. IoT Hub seçin.

    1. **Cihaz bul** ve IoT Edge cihaz bul ' u seçin.

3. **Oluştur** düğmesini seçin.

4. **Anoydetectoronıotedge** modülünü seçin.

    :::image type="content" source="../media/deploy-anomaly-detection-on-iot-edge/iot-edge-modules.png" alt-text="' In Seçilecek öğe olduğunu göstermek için Anoydetectoroniotedge bağlantısı ile IoT Edge modüller Kullanıcı arabiriminin görüntüsü kırmızı bir kutu ile vurgulanır.":::

5. **Ortam Değişkenleri**’ne gidin ve aşağıdaki bilgileri sağlayın.

    1.  Eula için **accept** değerini koruyun.

    1. **Billing** alanını Bilişsel Hizmetler uç noktanızla doldurun.

    1. **ApiKey** alanını Bilişsel Hizmetler API anahtarınızla doldurun.

    :::image type="content" source="../media/deploy-anomaly-detection-on-iot-edge/environment-variables.png" alt-text="Uç nokta ve API anahtarı için değerlerin doldurulması gereken alanların etrafında kırmızı kutular içeren ortam değişkenleri":::

6. **Güncelleştir**’i seçin

7. **İleri ' yi seçin:** rotayı tanımlamak için yollar. Tüm modüllerden gelen tüm iletilerin Azure IoT Hub’a gönderilmesini tanımlarsınız.

8. **Sonraki: Gözden geçirme ve oluşturma**’yı seçin. IoT Edge cihazınıza dağıtılacak olan tüm modülleri tanımlayan JSON dosyasının önizlemesini görebilirsiniz.
    
9. Modül dağıtımını başlatmak için **Oluştur**’u seçin.

10. Modül dağıtımını tamamladıktan sonra IoT hub'ınızın IoT Edge sayfasına dönersiniz. Ayrıntılarını görmek için IoT Edge cihazları listesinden cihazınızı seçin.

11. Ekranı aşağı kaydırın ve listelenen modüllere bakın. Yeni modülünüzün çalışma zamanı durumunun çalışıp çalışmadığını denetleyin. 

IoT Edge cihazınızın çalışma zamanı durumunda sorun gidermek için [sorun giderme kılavuzuna](../../../iot-edge/troubleshoot.md) başvurun

## <a name="test-anomaly-detector-on-an-iot-edge-device"></a>IoT Edge cihazda anomali algılayıcısı test etme

Azure Bilişsel Hizmetler kapsayıcısının çalıştırıldığı Azure IoT Edge cihazına bir HTTP çağrısı yaparsınız. Kapsayıcı, REST tabanlı uç nokta API 'Leri sağlar. `http://<your-edge-device-ipaddress>:5000`Modül API 'leri için Konağı kullanın.

Sınır cihazınız 5000 numaralı bağlantı noktasında gelen iletişime zaten izin vermediği takdirde yeni bir **gelen bağlantı noktası kuralı** oluşturmanız gerekecektir. 

Bir Azure VM için bu, **sanal makine**  >  **ayarları**\  >  **ağ**  >  **bağlantı noktası kuralı**  >  **gelen bağlantı noktası kuralı ekle** bölümünde ayarlanabilir.

Modülün çalıştığını doğrulamak için birkaç yol vardır. *Dış IP* adresini ve söz konusu Edge cihazının açık bağlantı noktasını bulun ve en sevdiğiniz web tarayıcınızı açın. Kapsayıcının çalıştığını doğrulamak için aşağıdaki çeşitli istek URL 'Lerini kullanın. Aşağıda listelenen örnek URL 'Ler aşağıda verilmiştir `http://<your-edge-device-ipaddress:5000` ancak belirli Kapsayıcınız farklılık gösterebilir. Edge cihazınızın *dış IP* adresini kullanmanız gerektiğini aklınızda bulundurun.

| İstek URL’si | Amaç |
|:-------------|:---------|
| `http://<your-edge-device-ipaddress>:5000/` | Kapsayıcı bir giriş sayfası sağlar. |
| `http://<your-edge-device-ipaddress>:5000/status` | GET ile de istenirse, bu, kapsayıcıyı başlatmak için kullanılan api anahtarının bir uç nokta sorgusuna neden olmadan geçerli olup olmadığını doğrular. Bu istek, Kubernetes [limize ve hazırlık araştırmaları](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/)için kullanılabilir. |
| `http://<your-edge-device-ipaddress>:5000/swagger` | Kapsayıcı uç noktalar için tüm belgeleri ve bir de **Deneyin** özelliği sağlar. Bu özellikle, ayarlarınızı bir Web tabanlı HTML biçiminde girebilir ve herhangi bir kod yazmak zorunda kalmadan sorguyu oluşturabilirsiniz. Sorgu çağrıldıktan sonra, gereken HTTP üst bilgilerini ve gövde biçimini göstermek için örnek bir KıVRıMLı komut sağlanır. |

![Kapsayıcının ana sayfası](../../../../includes/media/cognitive-services-containers-api-documentation/container-webpage.png)

## <a name="next-steps"></a>Sonraki adımlar

* Kapsayıcı görüntüsünü çekmek ve kapsayıcıyı çalıştırmak için [kapsayıcıları yüklemeyi ve çalıştırmayı](../anomaly-detector-container-configuration.md) gözden geçirin
* Yapılandırma ayarları için [kapsayıcıları](../anomaly-detector-container-configuration.md) yapılandırmayı gözden geçir
* [Anomali algılayıcı API hizmeti hakkında daha fazla bilgi edinin](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)
