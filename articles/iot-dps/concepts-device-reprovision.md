---
title: Azure IoT Hub cihaz sağlama hizmeti-cihaz kavramları
description: Azure IoT Hub cihaz sağlama hizmeti (DPS) için cihaz yeniden sağlama kavramlarını açıklar
author: wesmc7777
ms.author: wesmc
ms.date: 04/16/2021
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: fbc83ec62c10fae00e371cd9ad95cf2860495fad
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107575777"
---
# <a name="iot-hub-device-reprovisioning-concepts"></a>Cihaz yeniden sağlama kavramlarını IoT Hub

IoT çözümünün yaşam döngüsü boyunca Cihazları IoT Hub 'ları arasında taşımak yaygın bir çözümdür. Bu taşımanın nedenleri aşağıdaki senaryolara dahil olabilir:

* **Coğrafi konum/tsıklık**: bir cihaz konumlar arasında taşındığında, cihazın daha yakın bir IoT Hub 'ına geçirilmesi halinde ağ gecikmesi geliştirilmiştir.

* **Çoklu kiracı**: bir cihaz aynı IoT çözümünde kullanılabilir ve yeni bir müşteriye veya müşteri sitesine yeniden atanabilir. Bu yeni müşteri farklı bir IoT Hub 'ı kullanılarak hizmet verebilir.

* **Çözüm değişikliği**: bir cihaz yeni veya güncelleştirilmiş bir IoT çözümüne taşınabilir. Bu yeniden atama, cihazın diğer arka uç bileşenlerine bağlı yeni bir IoT Hub 'ı ile iletişim kurmasını gerektirebilir.

* **Karantina**: bir çözüm değişikliğine benzer. Hatalı çalışan, tehlikeye girmiş veya güncel olmayan bir cihaz yalnızca güncelleştirme ve uyumluluk için geri alma olabilecek bir IoT Hub 'ına yeniden atanabilir. Cihaz düzgün şekilde çalıştıktan sonra ana hub 'ına geri geçirilir.

Cihaz sağlama hizmeti içindeki yeniden sağlama desteği bu ihtiyaçları ele alınmaktadır. Cihazlar, cihazın kayıt girişinde yapılandırılan yeniden sağlama ilkesine bağlı olarak yeni IoT Hub 'larına otomatik olarak atanabilir.

## <a name="device-state-data"></a>Cihaz durumu verileri

Cihaz durumu verileri [cihaz ikizi](../iot-hub/iot-hub-devguide-device-twins.md) ve cihaz yetilerinden oluşur. Bu veriler cihaz sağlama Hizmeti örneğinde ve bir cihazın atandığı IoT Hub 'ında depolanır.

![Sağlama, cihaz sağlama hizmeti ile nasıl çalıştığını gösteren diyagram.](./media/concepts-device-reprovisioning/dps-provisioning.png)

Bir cihaz başlangıçta bir cihaz sağlama hizmeti örneğiyle sağlandığında, aşağıdaki adımlar gerçekleştirilir:

1. Cihaz, cihaz sağlama hizmeti örneğine bir sağlama isteği gönderir. Hizmet örneği, bir kayıt girişine göre cihaz kimliğini doğrular ve cihaz durumu verilerinin başlangıç yapılandırmasını oluşturur. Hizmet örneği, cihazı kayıt yapılandırmasına bağlı olarak bir IoT Hub 'ına atar ve bu IoT Hub atamasını cihaza döndürür.

2. Sağlama hizmeti örneği, atanan IoT Hub 'ına ilk cihaz durumu verilerinin bir kopyasını verir. Cihaz, atanan IoT Hub 'ına bağlanır ve işlemleri başlatır.

Zamanla, IoT Hub 'daki cihaz durumu verileri [cihaz işlemleri](../iot-hub/iot-hub-devguide-device-twins.md#device-operations) ve [arka uç işlemleri](../iot-hub/iot-hub-devguide-device-twins.md#back-end-operations)tarafından güncelleştirilemeyebilir. Cihaz sağlama Hizmeti örneğinde depolanan ilk cihaz durumu bilgileri dokunulmadan kalır. Bu dokunulmamış cihaz durumu verileri ilk yapılandırmadır.

![Cihaz sağlama hizmeti ile sağlama](./media/concepts-device-reprovisioning/dps-provisioning-2.png)

Senaryoya bağlı olarak, bir cihaz IoT Hub 'ları arasında taşınırsa, önceki IoT Hub 'ında güncelleştirilmiş cihaz durumunun yeni IoT Hub 'ına geçirilmesi de gerekebilir. Bu geçiş, cihaz sağlama hizmetindeki yeniden sağlama ilkeleri tarafından desteklenir.

## <a name="reprovisioning-policies"></a>İlkeleri yeniden sağlama

Senaryoya bağlı olarak, bir cihaz genellikle yeniden başlatma sırasında bir sağlama hizmeti örneğine bir istek gönderir. Ayrıca, isteğe bağlı olarak sağlamayı el ile tetiklemek için bir yöntemi destekler. Kayıt girişinde yeniden sağlama ilkesi, cihaz sağlama hizmeti örneğinin bu sağlama isteklerini nasıl işlediğini belirler. İlke Ayrıca cihaz durumu verilerinin yeniden sağlama sırasında geçirilmesi gerekip gerekmediğini de belirler. Aynı ilkeler, bireysel kayıtlar ve kayıt grupları için kullanılabilir:

* **Verileri yeniden sağlayın ve geçirin**: Bu ilke, yeni kayıt girişleri için varsayılandır. Bu ilke, kayıt girdisiyle ilişkili cihazlar yeni bir istek (1) gönderdiğinde işlem gerçekleştirir. Kayıt girişi yapılandırmasına bağlı olarak, cihaz başka bir IoT Hub 'ına yeniden atanabilir. Cihaz IoT Hub 'larını değiştirirse, ilk IoT Hub 'ına sahip cihaz kaydı kaldırılır. Bu ilk IoT Hub 'ından güncelleştirilmiş cihaz durumu bilgileri, yeni IoT Hub 'ına (2) geçirilecek. Geçiş sırasında cihazın durumu **atama** olarak bildirilir.

    ![Kayıt girişiyle ilişkilendirilen cihazlar yeni bir istek gönderdiğinde bir ilkenin işlem yapması gerektiğini gösteren diyagram.](./media/concepts-device-reprovisioning/dps-reprovisioning-migrate.png)

* **İlk yapılandırmaya yeniden sağlama ve sıfırlama**: Bu ilke, kayıt girdisiyle ilişkili cihazlar yeni sağlama isteği (1) gönderdiğinde işlem gerçekleştirir. Kayıt girişi yapılandırmasına bağlı olarak, cihaz başka bir IoT Hub 'ına yeniden atanabilir. Cihaz IoT Hub 'larını değiştirirse, ilk IoT Hub 'ına sahip cihaz kaydı kaldırılır. Cihaz sağlandıysa sağlama hizmeti örneğinin aldığı ilk yapılandırma verileri yeni IoT Hub 'ına (2) sağlanır. Geçiş sırasında cihazın durumu **atama** olarak bildirilir.

    Bu ilke genellikle IoT Hub 'larını değiştirmeden fabrika sıfırlaması için kullanılır.

    ![Kayıt girişiyle ilişkilendirilen cihazlar yeni bir sağlama isteği gönderdiğinde bir ilkenin nasıl işlem yapılacağını gösteren diyagram.](./media/concepts-device-reprovisioning/dps-reprovisioning-reset.png)

* **Hiçbir şekilde yeniden sağlama**: cihaz, farklı bir hub 'a hiçbir şekilde yeniden atanmaz. Bu ilke geriye dönük uyumluluğu yönetmek için sağlanır.

> [!NOTE]
> Cihaz için yeni [Returndata](how-to-send-additional-data.md) olması durumunda, DPS, yeniden sağlama ilkesine bakılmaksızın her zaman özel ayırma Web kancasını çağırır. Yeniden sağlama ilkesi **hiçbir şekilde yeniden sağlanmayacak** şekilde ayarlandıysa, Web kancası çağrılır, ancak cihaz atanmış hub 'ını değiştirmez.

### <a name="managing-backwards-compatibility"></a>Geriye dönük uyumluluğu yönetme

2018 Eylül 'den önce IoT Hub 'larına yönelik cihaz atamalarının yapışkan bir davranışı vardı. Bir cihaz sağlama sürecinden geri döndüğünüzde, yalnızca aynı IoT Hub 'ına atanır.

Bu davranışa bağımlılık yapmış olan çözümler için, sağlama hizmeti geriye dönük uyumluluk içerir. Bu davranış aşağıdaki ölçütlere göre şu anda cihazlarda korunur:

1. Cihazlar, cihaz sağlama hizmeti 'nde yerel yeniden sağlama desteğinin kullanılabilirliğine başlamadan önce bir API sürümü ile bağlanır. Aşağıdaki API tablosuna bakın.

2. Cihazlara yönelik kayıt girişinde, bunlar üzerinde ayarlanmış bir yeniden sağlama ilkesi yoktur.

Bu uyumluluk, önceden dağıtılan cihazların ilk test sırasında mevcut olan aynı davranışı deneymesinden emin olur. Önceki davranışı korumak için, bu kayıtları yeniden sağlama ilkesini kaydetme. Yeniden sağlama ilkesi ayarlandıysa, yeniden sağlama ilkesi davranışına göre önceliklidir. Yeniden sağlama ilkesinin öncelikli olmasına izin vererek, müşteriler cihazı yeniden görüntüye gerek kalmadan cihaz davranışını güncelleştirebilir.

Aşağıdaki akış şeması, davranışın ne zaman mevcut olduğunu göstermeye yardımcı olur:

![geriye dönük uyumluluk akış grafiği](./media/concepts-device-reprovisioning/reprovisioning-compatibility-flow.png)

Aşağıdaki tabloda, cihaz sağlama hizmeti 'nde yerel yeniden sağlama desteğinin kullanılabilirliğine ait API sürümleri gösterilmektedir:

| REST API | C SDK | Python SDK'sı |  Node SDK | Java SDK | .NET SDK |
| -------- | ----- | ---------- | --------- | -------- | -------- |
| [2018-04-01 ve öncesi](/rest/api/iot-dps/createorupdateindividualenrollment/createorupdateindividualenrollment#uri-parameters) | [1.2.8 ve önceki sürümler](https://github.com/Azure/azure-iot-sdk-c/blob/master/version.txt) | [1.4.2 ve önceki sürümler](https://github.com/Azure/azure-iot-sdk-python/blob/0a549f21f7f4fc24bc036c1d2d5614e9544a9667/device/iothub_client_python/src/iothub_client_python.cpp#L53) | [1.7.3 veya önceki sürümler](https://github.com/Azure/azure-iot-sdk-node/blob/074c1ac135aebb520d401b942acfad2d58fdc07f/common/core/package.json#L3) | [1.13.0 veya önceki sürümler](https://github.com/Azure/azure-iot-sdk-java/blob/794c128000358b8ed1c4cecfbf21734dd6824de9/device/iot-device-client/pom.xml#L7) | [1.1.0 veya önceki sürümler](https://github.com/Azure/azure-iot-sdk-csharp/blob/9f7269f4f61cff3536708cf3dc412a7316ed6236/provisioning/device/src/Microsoft.Azure.Devices.Provisioning.Client.csproj#L20)

> [!NOTE]
> Bu değerler ve bağlantıların değiştirilmesi olasıdır. Bu yalnızca, sürümlerin bir müşteri tarafından belirlenebileceği ve beklenen sürümlerin ne olacağı hakkında karar vermek için bir yer tutucu girişimdir.

## <a name="next-steps"></a>Sonraki adımlar

* [Cihazları yeniden sağlama](how-to-reprovision.md)
