---
title: Azure IoT Hub cihaz sağlama hizmeti 'nde cihazları yeniden sağlama | Microsoft Docs
description: Cihaz sağlama hizmeti örneğiniz ile cihazları yeniden sağlamayı öğrenin ve bunu yapmanız gerekebilir.
author: wesmc7777
ms.author: wesmc
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 41e6274e81c91584cf5212bc7ca7b2f31582b4db
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73888990"
---
# <a name="how-to-reprovision-devices"></a>Cihazları yeniden sağlama

IoT çözümünün yaşam döngüsü boyunca Cihazları IoT Hub 'ları arasında taşımak yaygındır. Bu taşımanın nedenleri aşağıdaki senaryolara dahil olabilir:

* **Coğrafi konum**: bir cihaz konumlar arasında taşındığında, cihazın her konuma yakın bir IoT Hub 'ına geçirilmesi halinde ağ gecikmesi geliştirilmiştir.

* **Çoklu kiracı**: bir cihaz aynı IoT çözümünde kullanılabilir, ancak yeni bir müşteri veya müşteri sitesi için yeniden atandı veya kiralanır. Bu yeni müşteri farklı bir IoT Hub 'ı kullanılarak hizmet verebilir.

* **Çözüm değişikliği**: bir cihaz yeni veya güncelleştirilmiş bir IoT çözümüne taşınabilir. Bu yeniden atama, cihazın diğer arka uç bileşenlerine bağlı yeni bir IoT Hub ile iletişim kurmasını gerektirebilir. 

* **Karantina**: bir çözüm değişikliğine benzer. Hatalı çalışan, güvenliği aşılmış veya güncel olmayan bir cihaz, hepsi güncelleyebildiği ve uyumluluk için geri alabileceğiniz bir IoT Hub 'ına yeniden atanabilir. Cihaz düzgün şekilde çalıştıktan sonra ana hub 'ına geri geçirilir.

Yeniden hazırlama hakkında daha ayrıntılı bilgi için bkz. [cihaz yeniden sağlama kavramlarını IoT Hub](concepts-device-reprovision.md).


## <a name="configure-the-enrollment-allocation-policy"></a>Kayıt ayırma ilkesini yapılandırma

Ayırma ilkesi, kayıt ile İlişkili cihazların yeniden sağlandıktan sonra bir IoT Hub 'ına nasıl ayrılacağını veya atandığını belirler.

Aşağıdaki adımlar, bir cihazın kaydı için ayırma ilkesini yapılandırır:

1. [Azure Portal](https://portal.azure.com) oturum açın ve cihaz sağlama hizmeti örneğinize gidin.

2. Kayıtları **Yönet**' e tıklayın ve yeniden sağlama için yapılandırmak istediğiniz kayıt grubuna veya bireysel kaydına tıklayın. 

3. **Cihazları hub 'lara nasıl atamak Istediğinizi seçin**altında, aşağıdaki ayırma ilkelerinden birini seçin:

    * **En düşük gecikme**: Bu ilke, cihaz ve IoT Hub arasındaki en düşük gecikme süresine neden olacak şekilde cihazları bağlantılı IoT Hub atar. Bu seçenek, cihazın konuma göre en yakın IoT Hub 'ı ile iletişim kurmasını sağlar. 
    
    * **Eşit ağırlıklı dağıtım**: Bu ilke, her bağlantılı IoT Hub 'ına atanan ayırma ağırlığına bağlı olarak cihazları bağlantılı IoT Hub 'larına dağıtır. Bu ilke, bu hub 'larda ayarlanan ayırma ağırlıklarını temel alarak cihazları bir bağlı hub grubu genelinde yük dengelemenize olanak tanır. Cihazları yalnızca bir IoT Hub sağladıysanız, bu ayar önerilir. Bu varsayılan ayardır. 
    
    * **Statik yapılandırma**: Bu ilke, bir cihazın sağlanması için kayıt girişinde istenen IoT Hub listelenmesini gerektirir. Bu ilke, cihazları atamak istediğiniz tek bir özel IoT Hub 'ı atamanıza olanak tanır.

4. **Bu grubun atanabileceği IoT Hub 'Larını seçin**altında ayırma ilkenize dahil etmek Istediğiniz bağlantılı IoT Hub 'larını seçin. İsteğe bağlı olarak, **Yeni bir IoT Hub bağlantısını** kullanarak yeni bir bağlı IoT Hub ekleyin.

    **En düşük gecikme süresi** ayırma ilkesiyle, seçtiğiniz hub 'lar cihaz ataması için en yakın hub 'ı belirlemek üzere gecikme süresi değerlendirmesine dahil edilir.

    **Eşit ağırlıklı dağıtım** ayırma ilkesiyle cihaz, yapılandırılmış ayırma ağırlıklarını ve geçerli cihaz yüküne göre seçtiğiniz hub 'larda yük dengelemesi yapılır.

    **Statik yapılandırma** ayırma ilkesiyle, cihazların atanmasını istediğiniz IoT Hub 'ını seçin.

4. Yeniden sağlama ilkesini ayarlamak için **Kaydet**' e tıklayın veya sonraki bölüme geçin.

    ![Kayıt ayırma ilkesi seçin](./media/how-to-reprovision/enrollment-allocation-policy.png)



## <a name="set-the-reprovisioning-policy"></a>Yeniden sağlama ilkesini ayarlama

1. [Azure Portal](https://portal.azure.com) oturum açın ve cihaz sağlama hizmeti örneğinize gidin.

2. Kayıtları **Yönet**' e tıklayın ve yeniden sağlama için yapılandırmak istediğiniz kayıt grubuna veya bireysel kaydına tıklayın.

3. **Farklı bir IoT Hub 'ına yeniden sağlamak üzere cihaz verilerinin nasıl Işleneceğini seçin**altında, aşağıdaki yeniden sağlama ilkelerinden birini seçin:

    * **Verileri yeniden sağlayın ve geçirin**: Bu ilke, kayıt girişiyle ilişkili cihazlar yeni bir sağlama isteği gönderdiğinde işlem gerçekleştirir. Kayıt girişi yapılandırmasına bağlı olarak, cihaz başka bir IoT Hub 'ına yeniden atanabilir. Cihaz IoT Hub 'larını değiştirirse, ilk IoT Hub 'ına sahip cihaz kaydı kaldırılır. Bu ilk IoT Hub 'ından tüm cihaz durum bilgileri yeni IoT Hub 'ına geçirilir. Geçiş sırasında cihazın durumu **atama** olarak bildirilir

    * **Yeniden sağlama ve ilk yapılandırmaya sıfırlama**: Bu ilke, kayıt girdisiyle ilişkili cihazlar yeni bir sağlama isteği gönderdiğinde işlem gerçekleştirir. Kayıt girişi yapılandırmasına bağlı olarak, cihaz başka bir IoT Hub 'ına yeniden atanabilir. Cihaz IoT Hub 'larını değiştirirse, ilk IoT Hub 'ına sahip cihaz kaydı kaldırılır. Cihaz sağlandığında sağlama hizmeti örneğinin aldığı ilk yapılandırma verileri yeni IoT Hub 'ına sağlanır. Geçiş sırasında cihazın durumu **atama**olarak bildirilir.

4. Değişikliklerinizi temel alarak cihazın yeniden sağlamasını etkinleştirmek için **Kaydet** ' e tıklayın.

    ![Kayıt ayırma ilkesi seçin](./media/how-to-reprovision/reprovisioning-policy.png)



## <a name="send-a-provisioning-request-from-the-device"></a>Cihazdan sağlama isteği gönderme

Cihazların önceki bölümlerde yapılan yapılandırma değişikliklerine göre yeniden sağlanması için, bu cihazların yeniden sağlamayı istemesi gerekir. 

Bir cihazın bir sağlama isteği gönderdiği sıklık, senaryoya bağlıdır. Ancak, yeniden başlatma sırasında cihazlarınızı bir sağlama hizmeti örneğine bir sağlama isteği gönderecek şekilde programlamak ve isteğe bağlı olarak sağlamayı el ile tetiklemek için bir [yöntemi](../iot-hub/iot-hub-devguide-direct-methods.md) desteklemek önerilir. Sağlama Ayrıca [istenen bir özellik](../iot-hub/iot-hub-devguide-device-twins.md#desired-property-example)ayarlanarak tetiklenebilir. 

Bir kayıt girişinde yeniden sağlama ilkesi, cihaz sağlama hizmeti örneğinin bu sağlama isteklerini nasıl işlediğini ve yeniden sağlama sırasında cihaz durumu verilerinin geçirilmesi gerekip gerekmediğini belirler. Aynı ilkeler, bireysel kayıtlar ve kayıt grupları için kullanılabilir:

Bir önyükleme sırası sırasında bir cihazdan sağlama isteği gönderme kodu için, bkz. [sanal cihaz otomatik sağlama](quick-create-simulated-device.md).


## <a name="next-steps"></a>Sonraki adımlar

- Daha fazla yeniden sağlama hakkında daha fazla bilgi için bkz. [cihaz yeniden sağlama kavramlarını IoT Hub](concepts-device-reprovision.md) 
- Daha fazla sağlama sağlamayı öğrenmek için bkz. [daha önce otomatik olarak sağlanan cihazların sağlamasını kaldırma](how-to-unprovision-devices.md) 











