---
title: Modüllerin cihazlarınızda mantık çalıştırma hakkında bilgi edinin-Azure IoT Edge | Microsoft Docs
description: Azure IoT Edge modüller, IoT Edge cihazlarda iş mantığını çalıştırabilmeniz için uzaktan dağıtılabilecek ve yönetilebilen mantıksal birim birimleridir.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/21/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: devx-track-csharp
ms.openlocfilehash: a9b1ffb2dbcbd6e81856277f4b672cf876cc75f1
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103492379"
---
# <a name="understand-azure-iot-edge-modules"></a>Azure IoT Edge modüllerini anlama

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Azure IoT Edge, çalışma mantığını uç üzerinde *modüller* biçiminde dağıtmanıza ve yönetmenize olanak tanır. Azure IoT Edge modüller, IoT Edge tarafından yönetilen en küçük hesaplama birimidir ve Azure Hizmetleri (Azure Stream Analytics gibi) veya kendi çözüme özgü kodunuzla bulunabilir. Modüllerin nasıl geliştirildiğini, dağıtıldığını ve yapıldığını anlamak için, bir modülün dört kavramsal öğesini göz önünde bulundurun:

* **Modül görüntüsü** , bir modülü tanımlayan yazılımı içeren bir pakettir.
* **Modül örneği** , bir IoT Edge cihazında modül görüntüsünü çalıştıran belirli bir hesaplama birimidir. Modül örneği IoT Edge çalışma zamanı tarafından başlatılır.
* **Modül kimliği** , her modül örneğiyle ilişkili IoT Hub depolanan bir bilgi parçasıdır (güvenlik kimlik bilgileri dahil).
* **Modül ikizi** , meta veriler, konfigürasyonlar ve koşullar dahil olmak üzere bir modül örneği için durum bilgilerini içeren IoT Hub depolanan bir JSON belgesidir.

## <a name="module-images-and-instances"></a>Modül görüntüleri ve örnekleri

IoT Edge modül görüntüleri, IoT Edge çalışma zamanının yönetim, güvenlik ve iletişim özelliklerinden faydalanan uygulamalar içerir. Kendi modül görüntülerinizi geliştirebilir veya Azure Stream Analytics gibi desteklenen bir Azure hizmetinden dışarı aktarabilirsiniz.
Görüntüler bulutta bulunur ve farklı çözümlerde güncelleştirilebilirler, değiştirilebilir ve dağıtılabilir. Örneğin, üretim satırı çıkışını tahmin etmek için makine öğrenimi kullanan bir modül, bir drone 'yi denetlemek için bilgisayar vizyonu kullanan bir modülden ayrı bir görüntü olarak mevcuttur.

Bir cihaza her bir modül görüntüsü dağıtıldığında ve IoT Edge çalışma zamanı tarafından başlatıldığında, Bu modülün yeni bir örneği oluşturulur. Dünyanın farklı kısımlarındaki iki cihaz aynı modül görüntüsünü kullanabilir. Ancak, modül cihazda başlatıldığında her cihazın kendi modül örneği olur.

![Diyagram-buluttaki modül resimleri, cihazlardaki modül örnekleri](./media/iot-edge-modules/image_instance.png)

Uygulamada, modüller görüntüleri bir depodaki kapsayıcı görüntüleri olarak bulunur ve modül örnekleri cihazlarda kapsayıcılardır.

<!--
As use cases for Azure IoT Edge grow, new types of module images and instances will be created. For example, resource constrained devices cannot run containers so may require module images that exist as dynamic link libraries and instances that are executables. 
-->

## <a name="module-identities"></a>Modül kimlikleri

IoT Edge çalışma zamanı tarafından yeni bir modül örneği oluşturulduğunda, buna karşılık gelen bir modül kimliği alır. Modül kimliği IoT Hub depolanır ve söz konusu modül örneği için tüm yerel ve bulut iletişimleri için adresleme ve güvenlik kapsamı olarak kullanılır.

Bir modül örneğiyle ilişkili kimlik, örneğin çalıştığı aygıtın kimliğine ve çözümünüzde Bu modüle sağladığınız ada bağlıdır. Örneğin, bir `insight` Azure Stream Analytics kullanan bir modül çağırırsanız ve bunu adlı bir cihaza dağıtırsanız `Hannover01` , IoT Edge çalışma zamanı adlı karşılık gelen bir modül kimliğini oluşturur `/devices/Hannover01/modules/insight` .

Açık olarak, bir modül görüntüsünü aynı cihaza birden çok kez dağıtmanız gerektiğinde, aynı görüntüyü farklı adlarla birden çok kez dağıtabilirsiniz.

![Diyagram-modül kimlikleri cihazlar ve cihazlar arasında benzersizdir](./media/iot-edge-modules/identity.png)

## <a name="module-twins"></a>Modül TWINS

Her modül örneğinin Ayrıca modül örneğini yapılandırmak için kullanabileceğiniz karşılık gelen bir modül ikizi vardır. Örnek ve ikizi, modül kimliği aracılığıyla birbirleriyle ilişkilendirilir.

Modül ikizi, modül bilgilerini ve yapılandırma özelliklerini depolayan bir JSON belgesidir. Bu kavram, IoT Hub [cihaz ikizi](../iot-hub/iot-hub-devguide-device-twins.md) kavramını paraleldir. İkizi modülünün yapısı bir Device ikizi ile aynıdır. Her iki tür TWINS ile etkileşim kurmak için kullanılan API 'Ler de aynıdır. İkisi arasındaki tek fark, istemci SDK 'sını örneklemek için kullanılan kimliktir.

```csharp
// Create a ModuleClient object. This ModuleClient will act on behalf of a
// module since it is created with a module’s connection string instead
// of a device connection string.
ModuleClient client = new ModuleClient.CreateFromEnvironmentAsync(settings);
await client.OpenAsync();

// Get the module twin
Twin twin = await client.GetTwinAsync();
```

## <a name="offline-capabilities"></a>Çevrimdışı özellikler

Azure IoT Edge modüller, en az bir kez IoT Hub eşitlemeden sonra sonsuza kadar çevrimdışı olarak çalışabilir. IoT Edge cihazlar, bu çevrimdışı özelliği diğer IoT cihazlarına da genişletebilir. Daha fazla bilgi için bkz. [IoT Edge cihazları, modülleri ve alt cihazları için genişletilmiş çevrimdışı özellikleri anlama](offline-capabilities.md).

## <a name="next-steps"></a>Sonraki adımlar

* [IoT Edge modülleri geliştirmeye yönelik gereksinimleri ve araçları anlayın](module-development.md)
* [Azure IoT Edge çalışma zamanını ve mimarisini anlayın](iot-edge-runtime.md)
