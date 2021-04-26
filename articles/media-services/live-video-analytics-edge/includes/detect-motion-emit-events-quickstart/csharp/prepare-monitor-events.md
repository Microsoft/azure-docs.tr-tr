---
ms.openlocfilehash: 882ba60e16f770651a1d9fe3b02b61be2b4c34c8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99532290"
---
IoT Edge modüldeki canlı video analizlerini kullanarak gelen canlı video akışındaki hareketleri algılayabilir ve olayları IoT Hub gönderebilirsiniz. Bu olayları görmek için şu adımları izleyin:

1. Visual Studio Code Gezgin bölmesini açın ve sol alt köşedeki Azure IoT Hub arayın.
1. **Cihazlar** düğümünü genişletin.
1. **LVA-örnek-cihaz öğesine** sağ tıklayın ve **Izlemeyi Başlat yerleşik olay uç noktası**' nı seçin.

    ![Yerleşik bir olay uç noktasını izlemeye başlama](../../../media/quickstarts/start-monitoring-iothub-events.png)

> [!NOTE]
> IoT Hub için yerleşik uç nokta bilgisi sağlamanız istenebilir. Bu bilgileri almak için Azure portal ' de IoT Hub gidin ve sol gezinti bölmesindeki **yerleşik uç noktalar** seçeneğini bulun. Buraya tıklayın ve **Olay Hub** 'ı ile uyumlu uç nokta bölümünde **Olay Hub 'ı ile uyumlu uç noktası** bölümüne bakın. Kutusunda metni kopyalayın ve kullanın. Uç nokta şuna benzer şekilde görünecektir:  
    ```
    Endpoint=sb://iothub-ns-xxx.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX;EntityPath=<IoT Hub name>
    ```
