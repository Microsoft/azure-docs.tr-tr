---
title: include dosyası
description: include dosyası
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 04/19/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 9151a84aa03e9b87b02278672622d458fbc5281e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99538785"
---
Azure portalı kullanarak IoT Hub oluşturmak için:

1. [Azure portalında](https://portal.azure.com) oturum açın.

1. Azure giriş sayfasında, **kaynak oluştur**' u seçin ve ardından **market 'te arama** *IoT Hub* girin.

1. Arama sonuçlarından **IoT Hub** seçin ve ardından **Oluştur**' u seçin.

1. **Temel bilgiler** sekmesinde, alanları aşağıdaki gibi doldurun:

   - **Abonelik**: hub 'ınız için kullanılacak aboneliği seçin.

   - **Kaynak grubu**: bir kaynak grubu seçin veya yeni bir tane oluşturun. Yeni bir tane oluşturmak için **Yeni oluştur** ' u seçin ve kullanmak istediğiniz adı girin. Var olan bir kaynak grubunu kullanmak için bu kaynak grubunu seçin. Daha fazla bilgi için bkz. [Azure Resource Manager kaynak gruplarını yönetme](../articles/azure-resource-manager/management/manage-resource-groups-portal.md). Bu öğretici **tutorials-iot-hub-rg** adını kullanır.

   - **Bölge**: hub 'ınızın bulunmasını istediğiniz bölgeyi seçin. Size en yakın konumu seçin. [IoT Hub cihaz akışları](../articles/iot-hub/iot-hub-device-streams-overview.md)gibi bazı özellikler yalnızca belirli bölgelerde kullanılabilir. Bu sınırlı Özellikler için desteklenen bölgelerden birini seçmeniz gerekir. Bu öğretici **Batı ABD** bölgesini kullanır.

   - **IoT Hub adı**: hub 'ınız için bir ad girin. Bu adın genel olarak benzersiz olması gerekir. Bu öğretici **öğreticiler-IoT-Hub** kullanır. Hub'ınızı oluştururken kendi benzersiz adınızı seçmeniz gerekir.

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

   :::image type="content" source="media/iot-hub-tutorials-create-free-hub/hub-definition-basics.png" alt-text="Azure portal hub oluşturma":::

1. Ileri ' yi seçin: hub 'ınızı oluşturmaya devam etmek için **ağ iletişimi** .

   IoT Hub bağlanabilme uç noktalarını seçin. Varsayılan **genel uç nokta (tüm ağlar)** ayarını seçebilir veya **genel uç nokta (seçili IP aralıkları)** veya **Özel uç nokta** seçeneğini belirleyebilirsiniz. Bu öğretici için varsayılan ayarı kabul edin.

   :::image type="content" source="media/iot-hub-tutorials-create-free-hub/hub-definition-networking.png" alt-text="Bağlanabilecek uç noktaları seçin.":::

1. Hub 'ınızı oluşturmaya devam etmek için **İleri: yönetim** ' i seçin.

   :::image type="content" source="media/iot-hub-tutorials-create-free-hub/hub-definition-management.png" alt-text="Azure portal kullanarak yeni bir hub için boyut ve ölçek ayarlayın.":::

   Varsayılan ayarları burada kabul edebilirsiniz. İsterseniz, aşağıdaki alanlardan herhangi birini değiştirebilirsiniz:

   - **Fiyatlandırma ve ölçek katmanı**: seçtiğiniz katman. Ücretsiz katmanı seçin. Ücretsiz katman, test ve değerlendirme için tasarlanmıştır. 500 cihazların hub 'a ve günde en fazla 8.000 iletiye bağlanmasını sağlar. Her Azure aboneliği ücretsiz katmanda bir IoT Hub 'ı oluşturabilir.

   - **IoT Hub birimler**: günlük birim başına izin verilen ileti sayısı, hub 'ın fiyatlandırma katmanına bağlıdır. Örneğin, hub 'ın 700.000 ileti girişini desteklemesini istiyorsanız iki adet S1 katmanı birimi seçersiniz.
   Her Azure aboneliği ücretsiz katmanda bir IoT Hub 'ı oluşturabilir. Diğer katman seçenekleri hakkında daha fazla bilgi için, bkz. [doğru IoT Hub katmanını seçme](../articles/iot-hub/iot-hub-scaling.md).

   - **IoT Için Defender**: IoT ve cihazlarınıza ek bir tehdit koruması katmanı eklemek için bunu etkinleştirin. Bu seçenek ücretsiz katmanda hub 'lar için kullanılamaz. Bu özellik hakkında daha fazla bilgi için bkz. [IoT Için Azure Güvenlik Merkezi](/azure/asc-for-iot/).

   - **Gelişmiş ayarlar**  >  **Cihazdan buluta bölümler**: Bu özellik cihazdan buluta iletileri, iletilerin eşzamanlı okuyucu sayısıyla ilişkilendirir. Çoğu hub 'da yalnızca dört bölüm gereklidir. Ücretsiz katman hub 'ı iki bölümden sınırlıdır.

1. Sonraki ekrana devam etmek için **İleri: Etiketler** ' i seçin.

   Etiketler ad/değer çiftleridir. Kaynakları kategorilere ayırarak ve faturalandırmayı birleştirmek için birden fazla kaynağa ve kaynak grubuna aynı etiketi atayabilirsiniz. Daha fazla bilgi için bkz. [Azure kaynaklarınızı düzenlemek için etiketleri kullanma](../articles/azure-resource-manager/management/tag-resources.md).

   :::image type="content" source="media/iot-hub-tutorials-create-free-hub/hub-definition-tags.png" alt-text="Azure portal kullanarak hub için Etiketler atayın.":::

1. **İleri ' yi seçin:** seçimlerinizi gözden geçirmek için ve Oluştur ' a tıklayın. Bu ekrana benzer bir şey görürsünüz, ancak hub 'ı oluştururken seçtiğiniz değerler vardır.

   :::image type="content" source="media/iot-hub-tutorials-create-free-hub/hub-definition-create.png" alt-text="Yeni hub oluşturma bilgilerini gözden geçirin.":::

1. Seçtiğiniz IoT hub adını not edin. Bu değeri öğreticinin sonraki bölümlerinde kullanacaksınız.
