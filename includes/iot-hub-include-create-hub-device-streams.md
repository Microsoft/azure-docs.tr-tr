---
title: içerme dosyası (cihaz akışları Önizleme)
description: içerme dosyası (cihaz akışları Önizleme)
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 08/20/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 46f8e59713896cd94b96de62d982072119c32513
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/27/2019
ms.locfileid: "70050190"
---
Bu bölümde [Azure Portal](https://portal.azure.com)kullanarak IoT Hub 'ı oluşturma açıklanmaktadır.

1. [Azure Portal](https://portal.azure.com) oturum açın.

1. **Kaynak oluştur**' u seçin ve ardından Market ' i **Ara** alanına *IoT Hub* girin.

1. Arama sonuçlarından **IoT Hub** seçin ve ardından **Oluştur**' u seçin.

1. **Temel bilgiler** sekmesinde, alanları aşağıdaki gibi doldurun:

   - **Abonelik**: Hub 'ınız için kullanılacak aboneliği seçin.

   - **Kaynak grubu**: Bir kaynak grubu seçin veya yeni bir tane oluşturun. Yeni bir tane oluşturmak için **Yeni oluştur** ' u seçin ve kullanmak istediğiniz adı girin. Var olan bir kaynak grubunu kullanmak için bu kaynak grubunu seçin. Daha fazla bilgi için bkz. [Azure Resource Manager kaynak gruplarını yönetme](../articles/azure-resource-manager/manage-resource-groups-portal.md).

   - **Bölge**: Hub 'ınızın bulunmasını istediğiniz bölgeyi seçin. **Orta ABD** veya **Orta ABD euap**IoT Hub cihaz akışları önizlemesini destekleyen bir bölge seçin.

   - **IoT Hub adı**: Hub 'ınız için bir ad girin. Bu adın küresel olarak benzersiz olması gerekir. Girdiğiniz ad kullanılabilir durumdaysa yeşil bir onay işareti görünür.

   ![Azure portal IoT Hub 'ı oluşturma](./media/iot-hub-include-create-hub-device-streams/iot-hub-creation-device-streams.png)

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

1. İleri **' yi seçin: Hub 'ınızı oluşturmaya** devam etmek için boyut ve ölçek.

   ![Azure portal kullanarak yeni bir IoT Hub için boyut ve ölçek ayarlama](./media/iot-hub-include-create-hub-device-streams/iot-hub-creation-02.png)

   **Boyut ve ölçek**' te varsayılan ayarları kabul edebilir ve alt kısımdaki **gözden geçir + oluştur** ' u seçebilirsiniz. Aşağıdaki seçenekleri göz önünde bulundurun:

   - **Fiyatlandırma ve ölçek katmanı**: Seçtiğiniz katman. Standart katmanlardan birini seçin (**S1**, **S2**veya **S3**) ya da **F1: Ücretsiz katman**. Bu seçenek ayrıca, dünyanın her tarafında, bir telemetride bekleyebileceğiniz akış olmayan iş yüklerinizin ve örneğin telemetri iletilerinin boyutuna göre de yapılabilir. Örneğin, ücretsiz katman test ve değerlendirme için tasarlanmıştır. 500 cihazların IoT Hub 'ına ve günde en fazla 8.000 iletiye bağlanmasını sağlar. Her Azure aboneliği ücretsiz katmanda bir IoT Hub 'ı oluşturabilir. 

   - **IoT Hub birimi sayısı**: Günlük birim başına izin verilen ileti sayısı, hub 'ın fiyatlandırma katmanına bağlıdır. Bu seçenek, hub 'ınızda bekleyen akış olmayan iş yüküne bağlıdır. Şimdilik 1 seçeneğini belirleyebilirsiniz.

   - **Gelişmiş ayarlar** > **cihazdan buluta bölümler**: Bu özellik cihazdan buluta iletileri, iletilerin eşzamanlı okuyucu sayısıyla ilişkilendirir. Çoğu hub 'da yalnızca dört bölüm olması gerekir.

   Katman seçenekleri hakkında daha fazla bilgi için bkz. [doğru IoT Hub katmanını seçme](../articles/iot-hub/iot-hub-scaling.md).

1. Seçimlerinizi gözden geçirmek için **gözden geçir + oluştur**' u seçin. Sonuçlarınız aşağıdakine benzer olacaktır:

   ![Yeni IoT Hub 'ı oluşturma bilgileri](./media/iot-hub-include-create-hub-device-streams/iot-hub-creation-03.png)

1. Yeni IoT Hub 'ınızı oluşturmak için **Oluştur**' u seçin. İşlem birkaç dakika sürer.
