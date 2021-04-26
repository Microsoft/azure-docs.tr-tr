---
ms.openlocfilehash: 94044e95e83742487a0d4d650814a5324f07011a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101751074"
---
Dağıtım bildirimi, bir sınır cihazına hangi modüllerin dağıtıldığını tanımlar. Ayrıca, bu modüllerle ilgili yapılandırma ayarlarını tanımlar. 

Şablon dosyasından bildirim oluşturmak ve ardından Edge cihazına dağıtmak için bu adımları izleyin.

1. Visual Studio Code’u açın.
1. **Azure ıOT hub** bölmesinin yanındaki IoT Hub bağlantı dizesini ayarlamak için **diğer eylemler** simgesini seçin. Dizeyi *src/buluttan cihazdan-Console-App/appsettings.js* dosyasından kopyalayabilirsiniz. 

    ![IOT bağlantı dizesi ayarla](../../../media/quickstarts/set-iotconnection-string.png)

> [!NOTE]
> IoT Hub için yerleşik uç nokta bilgisi sağlamanız istenebilir. Bu bilgileri almak için Azure portal ' de IoT Hub gidin ve sol gezinti bölmesindeki **yerleşik uç noktalar** seçeneğini bulun. Buraya tıklayın ve **Olay Hub** 'ı ile uyumlu uç nokta bölümünde **Olay Hub 'ı ile uyumlu uç noktası** bölümüne bakın. Kutusunda metni kopyalayın ve kullanın. Uç nokta şuna benzer şekilde görünecektir:  
    ```
    Endpoint=sb://iothub-ns-xxx.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX;EntityPath=<IoT Hub name>
    ```

1. **Src/Edge/deployment.template.jsüzerinde** sağ tıklayın ve **IoT Edge dağıtım bildirimi oluştur**' u seçin.

    ![IoT Edge dağıtım bildirimini oluşturma](../../../media/quickstarts/generate-iot-edge-deployment-manifest.png)

    Bu eylem, *src/Edge/config* klasöründe *deployment.amd64.js* adlı bir bildirim dosyası oluşturmalı.
1. **Src/Edge/config/deployment.amd64.jsüzerinde** sağ tıklayın, **tek cihaz için dağıtım oluştur**' u seçin ve ardından Edge cihazınızın adını seçin.

    ![Tek bir cihaz için dağıtım oluşturma](../../../media/quickstarts/create-deployment-single-device.png)

1. IoT Hub bir cihaz seçmeniz istendiğinde, açılan menüden **LVA-Sample-Device** ' ı seçin.
1. 30 saniye sonra, pencerenin sol alt köşesinde Azure IoT Hub ' yi yenileyin. Edge cihazında artık aşağıdaki dağıtılan modüller gösterilmektedir:

    * IoT Edge (modül adı) üzerinde canlı video analizi `lvaEdge`
    * Real-Time streaming Protocol (RTSP) Simülatörü (modül adı `rtspsim` )

RTSP simülatör modülü, [canlı video analizi kaynakları kurulum betiğini](https://github.com/Azure/live-video-analytics/tree/master/edge/setup)çalıştırdığınızda Edge cihazınıza kopyalanmış bir video dosyası kullanarak canlı bir video akışının benzetimini yapar. 

> [!NOTE]
> Kurulum betiğimizden temin yerine kendi Edge cihazınızı kullanıyorsanız, bu hızlı başlangıç için kullanılan örnek video dosyasını çekmek ve depolamak için uç cihazınıza gidin ve **yönetici haklarıyla** aşağıdaki komutları çalıştırın:  

```
mkdir /home/lvaedgeuser/samples      
mkdir /home/lvaedgeuser/samples/input    
curl https://lvamedia.blob.core.windows.net/public/camera-300s.mkv > /home/lvaedgeuser/samples/input/camera-300s.mkv  
chown -R lvalvaedgeuser:localusergroup /home/lvaedgeuser/samples/  
```
Bu aşamada modüller dağıtılır ancak hiçbir medya grafiği etkin değildir.
