---
title: include dosyası
description: include dosyası
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 02/14/2020
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: cf651a1a09662e3084a8a9bdb6365b69b6ea52b5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99580144"
---
Bu bölümde [Azure Portal](https://portal.azure.com)kullanarak IoT Hub 'ı oluşturma açıklanmaktadır.

1. [Azure portalında](https://portal.azure.com) oturum açın.

1. Azure giriş sayfasından **+ kaynak oluştur** düğmesini seçin ve ardından **Market 'te ara** alanına *IoT Hub* girin.

1. Arama sonuçlarından **IoT Hub** seçin ve ardından **Oluştur**' u seçin.

1. **Temel bilgiler** sekmesinde, alanları aşağıdaki gibi doldurun:

   - **Abonelik**: hub 'ınız için kullanılacak aboneliği seçin.

   - **Kaynak grubu**: bir kaynak grubu seçin veya yeni bir tane oluşturun. Yeni bir tane oluşturmak için **Yeni oluştur** ' u seçin ve kullanmak istediğiniz adı girin. Var olan bir kaynak grubunu kullanmak için bu kaynak grubunu seçin. Daha fazla bilgi için bkz. [Azure Resource Manager kaynak gruplarını yönetme](../articles/azure-resource-manager/management/manage-resource-groups-portal.md).

   - **Bölge**: hub 'ınızın bulunmasını istediğiniz bölgeyi seçin. Size en yakın konumu seçin. [IoT Hub cihaz akışları](../articles/iot-hub/iot-hub-device-streams-overview.md)gibi bazı özellikler yalnızca belirli bölgelerde kullanılabilir. Bu sınırlı Özellikler için desteklenen bölgelerden birini seçmeniz gerekir.

   - **IoT Hub adı**: hub 'ınız için bir ad girin. Bu adın genel olarak benzersiz olması gerekir.

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

   :::image type="content" source="./media/iot-hub-include-create-hub/iot-hub-create-screen-basics.png" alt-text="Azure portal hub oluşturun.":::

1. Ileri ' yi seçin: hub 'ınızı oluşturmaya devam etmek için **ağ iletişimi** .

   IoT Hub bağlanabilme uç noktalarını seçin. Varsayılan **genel uç nokta (tüm ağlar)** ayarını seçebilir veya **genel uç nokta (seçili IP aralıkları)** veya **Özel uç nokta** seçeneğini belirleyebilirsiniz. Bu örnek için varsayılan ayarı kabul edin.

   :::image type="content" source="./media/iot-hub-include-create-hub/iot-hub-create-network-screen.png" alt-text="Bağlanabilecek uç noktaları seçin.":::

1. Hub 'ınızı oluşturmaya devam etmek için **İleri: yönetim** ' i seçin.

   :::image type="content" source="./media/iot-hub-include-create-hub/iot-hub-management.png" alt-text="Azure portal kullanarak yeni bir hub için boyut ve ölçek ayarlayın.":::

    Varsayılan ayarları burada kabul edebilirsiniz. İsterseniz, aşağıdaki alanlardan herhangi birini değiştirebilirsiniz:

    - **Fiyatlandırma ve ölçek katmanı**: seçtiğiniz katman. İstediğiniz sayıda özelliğe ve her gün çözümünüz aracılığıyla kaç tane ileti gönderdiğinize bağlı olarak çeşitli katmanlardan seçim yapabilirsiniz. Ücretsiz katman, test ve değerlendirme için tasarlanmıştır. 500 cihazların hub 'a ve günde en fazla 8.000 iletiye bağlanmasını sağlar. Her Azure aboneliği ücretsiz katmanda bir IoT Hub 'ı oluşturabilir.

      IoT Hub cihaz akışları için bir hızlı başlangıç üzerinden çalışıyorsanız, ücretsiz katmanı seçin.

    - **IoT Hub birimler**: günlük birim başına izin verilen ileti sayısı, hub 'ın fiyatlandırma katmanına bağlıdır. Örneğin, hub 'ın 700.000 ileti girişini desteklemesini istiyorsanız iki adet S1 katmanı birimi seçersiniz.
    Diğer katman seçenekleri hakkında daha fazla bilgi için, bkz. [doğru IoT Hub katmanını seçme](../articles/iot-hub/iot-hub-scaling.md).

    - **IoT Için Defender**: IoT ve cihazlarınıza ek bir tehdit koruması katmanı eklemek için bunu etkinleştirin. Bu seçenek ücretsiz katmanda hub 'lar için kullanılamaz. Bu özellik hakkında daha fazla bilgi için bkz. [IoT Için Azure Güvenlik Merkezi](/azure/asc-for-iot/).

    - **Gelişmiş ayarlar**  >  **Cihazdan buluta bölümler**: Bu özellik cihazdan buluta iletileri, iletilerin eşzamanlı okuyucu sayısıyla ilişkilendirir. Çoğu hub 'da yalnızca dört bölüm gereklidir.

1. Sonraki ekrana devam etmek için **İleri: Etiketler** ' i seçin.

    Etiketler ad/değer çiftleridir. Kaynakları kategorilere ayırarak ve faturalandırmayı birleştirmek için birden fazla kaynağa ve kaynak grubuna aynı etiketi atayabilirsiniz. Daha fazla bilgi için bkz. [Azure kaynaklarınızı düzenlemek için etiketleri kullanma](../articles/azure-resource-manager/management/tag-resources.md).

    :::image type="content" source="./media/iot-hub-include-create-hub/iot-hub-create-tags.png" alt-text="Azure portal kullanarak hub için Etiketler atayın.":::

1. **İleri ' yi seçin:** seçimlerinizi gözden geçirmek için ve Oluştur ' a tıklayın. Bu ekrana benzer bir şey görürsünüz, ancak hub 'ı oluştururken seçtiğiniz değerler vardır.

    :::image type="content" source="./media/iot-hub-include-create-hub/iot-hub-review-and-create.png" alt-text="Yeni hub oluşturma bilgilerini gözden geçirin.":::

1. Yeni hub 'ınızı oluşturmak için **Oluştur** ' u seçin. Hub 'ın oluşturulması birkaç dakika sürer.