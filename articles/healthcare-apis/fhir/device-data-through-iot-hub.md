---
title: 'Öğretici: Azure IoT Hub aracılığıyla cihaz verileri alma'
description: Bu öğreticide, FHıR için Azure IoT Bağlayıcısı aracılığıyla IoT Hub 'den Azure API 'ye cihaz veri yönlendirmeyi nasıl etkinleştireceğinizi öğreneceksiniz.
services: healthcare-apis
author: ms-puneet-nagpal
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: tutorial
ms.date: 11/13/2020
ms.author: punagpal
ms.openlocfilehash: 77d54ef0c9bef40af47c2fc48b9d4b5d6315b119
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107780404"
---
# <a name="tutorial-receive-device-data-through-azure-iot-hub"></a>Öğretici: Azure IoT Hub aracılığıyla cihaz verileri alma

Hızlı sağlık ile birlikte çalışabilirlik kaynakları için Azure IoT Bağlayıcısı (FHıR&#174;) *, tıp 'leri Internet 'ten, FHAR için Azure API 'sine veri alma olanağı sağlar. Azure portal hızlı başlangıçta Azure [IoT bağlayıcısını dağıtma (Önizleme)](iot-fhir-portal-quickstart.md) , IoT Central Azure tarafından yönetilen bir cihaz örneği olduğunu ve fhır Için Azure IoT Bağlayıcısı 'na [telemetri göndermeyi](iot-fhir-portal-quickstart.md#connect-your-devices-to-iot) gösterdi. FHıR için Azure IoT Bağlayıcısı, Azure IoT Hub aracılığıyla sağlanan ve yönetilen cihazlarla da çalışabilir. Bu öğreticide, Azure IoT Hub cihaz verilerini, FHıR için Azure IoT bağlayıcısına bağlama ve yönlendirme yordamı sağlanmaktadır.

## <a name="prerequisites"></a>Önkoşullar

- Etkin bir Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Fhır için en az bir Azure IoT Bağlayıcısı içeren FHıR kaynağı için Azure API- [Azure Portal kullanarak fhir Için Azure IoT bağlayıcısını dağıtma (Önizleme)](iot-fhir-portal-quickstart.md)
- Azure IoT Hub kaynak gerçek veya sanal cihazla bağlandı- [Azure Portal kullanarak IoT Hub 'ı oluşturma](../../iot-hub/quickstart-send-telemetry-dotnet.md)

> [!TIP]
> Azure IoT Hub sanal cihaz uygulaması kullanıyorsanız, tercih ettiğiniz uygulamayı farklı desteklenen diller ve sistemler arasında seçebilirsiniz.

## <a name="get-connection-string-for-azure-iot-connector-for-fhir-preview"></a>FHıR için Azure IoT Bağlayıcısı bağlantı dizesini al (Önizleme)

Azure IoT Hub, FHıR için Azure IoT bağlayıcınıza güvenli bir şekilde bağlanmak için bir bağlantı dizesi gerektirir. [Bir bağlantı dizesi oluşturma](iot-fhir-portal-quickstart.md#generate-a-connection-string)bölümünde açıklandığı gibi fhır Için Azure IoT Bağlayıcınız için yeni bir bağlantı dizesi oluşturun. Sonraki adımda kullanılmak üzere bu bağlantı dizesini koru.

FHıR için Azure IoT Bağlayıcısı, cihaz iletilerini almak için kullanılan bir Azure Event hub örneğini kullanır. Yukarıda oluşturulan bağlantı dizesi temelde bu temel olay hub 'ına yönelik bağlantı dizesidir.

## <a name="connect-azure-iot-hub-with-the-azure-iot-connector-for-fhir-preview"></a>FHıR için Azure IoT Bağlayıcısı (Önizleme) ile Azure IoT Hub bağlama

Azure IoT Hub, cihaz verilerini Olay Hub 'ı, depolama hesabı ve Service Bus gibi çeşitli Azure hizmetlerine gönderme yeteneği sağlayan [ileti yönlendirme](../../iot-hub/iot-hub-devguide-messages-d2c.md) adlı bir özelliği destekler. FHıR için Azure IoT Bağlayıcısı bu özellikten yararlanarak Azure IoT Hub 'tan Olay Hub 'ı uç noktasına bağlanıp cihaz verileri gönderebilir.

> [!NOTE] 
> Şu anda, FHıR 'nin Olay Hub 'ı için Azure IoT Bağlayıcısı 'nın müşteri aboneliğinde barındırılmadığı için, PowerShell veya CLı komutunu yalnızca [ileti yönlendirme oluşturmak](../../iot-hub/tutorial-routing.md) için kullanabilirsiniz. bu nedenle, Azure Portal aracılığıyla görülemez. Ancak, ileti yönlendirme nesneleri PowerShell veya CLı kullanılarak eklendikten sonra, Azure portal görünür ve buradan yönetilebilir.

İleti yönlendirmeyi ayarlama iki adımdan oluşur.

### <a name="add-an-endpoint"></a>Bir uç nokta ekleme
Bu adım IoT Hub verileri yönlendiren bir uç nokta tanımlar. Bu uç noktayı, tercihlerinize göre [Add-AzIotHubRoutingEndpoint](/powershell/module/az.iothub/Add-AzIotHubRoutingEndpoint) PowerShell komutunu ya da [az IoT Hub Routing-ENDPOINT Create](/cli/azure/iot/hub/routing-endpoint#az_iot_hub_routing_endpoint_create) CLI komutunu kullanarak oluşturun.

Aşağıda, bir uç nokta oluşturmak için komutuyla birlikte kullanılacak parametrelerin listesi verilmiştir:

|PowerShell parametresi|CLı parametresi|Description|
|---|---|---|
|ResourceGroupName|resource-group|IoT Hub kaynağınızın kaynak grubu adı.|
|Name|Hub-adı|IoT Hub kaynağınızın adı.|
|Uçnoktaadı|uç nokta adı|Oluşturulan uç noktaya atamak istediğiniz bir ad kullanın.|
|EndpointType|uç nokta türü|IoT Hub bağlantı kurmak için gereken uç nokta türü. PowerShell için "EventHub" ve CLı için "eventhub" değerlerini kullanın.|
|EndpointResourceGroup|uç nokta-kaynak grubu|FHıR 'nin FHıR kaynağı için Azure API 'SI için Azure IoT bağlayıcınızın kaynak grubu adı. Bu değeri, FHıR için Azure API 'nin Genel Bakış sayfasından edinebilirsiniz.|
|Endpointsubscriptionıd|uç nokta-abonelik kimliği|FHıR 'nin FHıR kaynağı için Azure API 'SI için Azure IoT bağlayıcınızın abonelik kimliği. Bu değeri, FHıR için Azure API 'nin Genel Bakış sayfasından edinebilirsiniz.|
|Dizisi|bağlantı dizesi|FHıR için Azure IoT bağlayıcınıza bağlantı dizesi. Önceki adımda edindiğiniz değeri kullanın.|

### <a name="add-a-message-route"></a>İleti yolu ekleme
Bu adım, yukarıda oluşturulan uç noktayı kullanarak bir ileti yolunu tanımlar. Tercihinize göre [Add-AzIotHubRoute](/powershell/module/az.iothub/Add-AzIoTHubRoute) PowerShell komutunu veya [az IoT Hub Route Create](/cli/azure/iot/hub/route#az_iot_hub_route_create) CLI komutunu kullanarak bir yol oluşturun.

İleti yolu eklemek için komutuyla birlikte kullanılacak parametrelerin listesi aşağıdadır:

|PowerShell parametresi|CLı parametresi|Description|
|---|---|---|
|ResourceGroupName|g|IoT Hub kaynağınızın kaynak grubu adı.|
|Name|Hub-adı|IoT Hub kaynağınızın adı.|
|Uçnoktaadı|uç nokta adı|Yukarıda oluşturduğunuz bitiş noktasının adı.|
|Routetablename|yol adı|Oluşturulan ileti yoluna atamak istediğiniz ad.|
|Kaynak|Kaynak türü|Uç noktaya gönderilen veri türü. PowerShell için "DeviceMessages" değeri ve CLı için "devicemessages" değerlerini kullanın.|

## <a name="send-device-message-to-iot-hub"></a>IoT Hub cihaz iletisi gönder

Aşağıda gösterilen örnek kalp oranı iletisini Azure IoT Hub göndermek için cihazınızı (gerçek veya sanal) kullanın. Bu ileti fhır için Azure IoT Bağlayıcısı 'na yönlendirilir. burada ileti bir FHıR gözlem kaynağına dönüştürülür ve FHıR için Azure API 'sinde depolanır.

```json
{
  "HeartRate": 80,
  "RespiratoryRate": 12,
  "HeartRateVariability": 64,
  "BodyTemperature": 99.08839032397609,
  "BloodPressure": {
    "Systolic": 23,
    "Diastolic": 34
  },
  "Activity": "walking"
}
```
> [!IMPORTANT]
> FHıR için Azure IoT Bağlayıcınız ile yapılandırılan [eşleme şablonlarına](iot-mapping-templates.md) uyan cihaz iletisini gönderdiğinizden emin olun.

## <a name="view-device-data-in-azure-api-for-fhir"></a>FHıR için Azure API 'de cihaz verilerini görüntüleme

Postman kullanarak FHıR için Azure API 'sindeki fhır için Azure IoT Bağlayıcısı tarafından oluşturulan FHıR izleme kaynakları ' nı görüntüleyebilirsiniz. [Fhır Için Azure API 'sine erişmek üzere Postman](access-fhir-postman-tutorial.md) 'nizi ayarlayın ve `GET` `https://your-fhir-server-url/Observation?code=http://loinc.org|8867-4` Yukarıdaki örnek iletide sinyal oranı değeri gönderilen gözehar kaynaklarını görüntülemek için bir istek yapın.

> [!TIP]
> Kullanıcının FHıR veri düzlemi için Azure API 'sine uygun erişimi olduğundan emin olun. Gerekli veri düzlemi rollerini atamak için [Azure rol tabanlı erişim denetimi 'ni (Azure RBAC)](configure-azure-rbac.md) kullanın.


## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıç kılavuzunda, Azure IoT Hub 'yi, FHıR için Azure IoT Bağlayıcısı 'na yönlendirmek üzere ayarlarsınız. FHıR için Azure IoT Bağlayıcısı hakkında daha fazla bilgi edinmek için aşağıdaki adımları aşağıdan seçin:

Azure IoT Bağlayıcısı içindeki FHIR için farklı veri akışı aşamalarını anlayın.

>[!div class="nextstepaction"]
>[FHıR veri akışı için Azure IoT Bağlayıcısı](iot-data-flow.md)

Cihaz ve FHıR eşleme şablonlarını kullanarak IoT bağlayıcısını nasıl yapılandıracağınızı öğrenin.

>[!div class="nextstepaction"]
>[FHıR eşleme şablonları için Azure IoT Bağlayıcısı](iot-mapping-templates.md)

* Azure portal, FHıR için Azure IoT Bağlayıcısı, IoT Bağlayıcısı (Önizleme) olarak adlandırılır. FHıR, HL7 'in tescilli ticari markasıdır ve HL7 izniyle kullanılır.