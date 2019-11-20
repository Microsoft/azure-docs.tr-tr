---
title: Azure IoT Central nedir? | Microsoft Docs
description: Azure IoT Central, IoT çözümlerinin oluşturulmasını kolaylaştıran bir IoT uygulama platformudur. Bu makalede Azure IoT Central’ın özelliklerine genel bir bakış sunulmaktadır.
author: dominicbetts
ms.author: dobett
ms.date: 08/26/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: timlt
ms.openlocfilehash: 30e7b4c39c24f4271c53f7a9f8940e4fb3c2e298
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74048667"
---
<!---
Purpose of an Overview article: 
1. To give a TECHNICAL overview of a service/product: What is it? Why should I use it? It's a "learn" topic that describes key benefits and our competitive advantage. It's not a "do" topic.
2. To help audiences who are new to service but who may be familiar with related concepts. 
3. To compare the service to another service/product that has some similar functionality, ex. SQL Database / SQL Data Warehouse, if appropriate. This info can be in a short list or table. 
-->

# <a name="what-is-azure-iot-central"></a>Azure IoT Central nedir?

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

Azure IoT Central, kurumsal düzeyde IoT çözümlerini geliştirme, yönetme ve sürdürme ile ilişkili yükü ve maliyeti azaltan bir IoT uygulama platformudur. Azure IoT Central ile oluşturmayı seçtiğinizde, yalnızca karmaşık ve sürekli gelişen bir IoT altyapısını korumak ve güncelleştirmek yerine, işletmenizin IoT verileriyle dönüştürülmesi için zaman, para ve enerji tasarrufu sağlayabilirsiniz.

Kullanımı kolay arabirim, cihaz koşullarını izlemeyi, kuralları oluşturmayı ve milyonlarca cihazı ve bunların yaşam döngüsü boyunca verilerini yönetmeyi kolaylaştırır. Ayrıca, IoT Intelligence 'ı iş kolu uygulamalarına genişleterek cihaz öngörülerine göre hareket etmenizi sağlar.

Bu makalede, Azure IoT Central için özetlenmektedir:

- Bir projeyle ilişkili tipik kişilikler.
- Uygulamanızı oluşturma.
- Cihazlarınızı uygulamanıza bağlama
- Uygulamanızı yönetme.

## <a name="personas"></a>Kişilikler

Azure IoT Central belgeleri, Azure IoT Central uygulamasıyla etkileşime geçen dört kişilik anlamına gelir:

- _Oluşturucu_, uygulamaya bağlanan cihaz türlerini tanımlamaktan ve uygulamayı operatör için özelleştirmekten sorumludur.
- _Operatör_, uygulamaya bağlı cihazları yönetir.
- _Yönetici_ , uygulama içindeki [Kullanıcı rollerini ve izinleri](howto-administer.md) yönetme gibi görevlerden sorumludur.
- _Cihaz geliştiricisi_, uygulamanıza bağlı bir cihaz üzerinde çalışan kodu oluşturur.

## <a name="create-your-azure-iot-central-application"></a>Azure IoT Central uygulamanızı oluşturma

Oluşturucu olarak Azure IoT Central’ı, kuruluşunuz için özel, bulutta barındırılan bir IoT çözümü oluşturmak için kullanırsınız. Özel bir IoT çözümü genellikle aşağıdakilerden oluşur:

- Cihazlarınızdan telemetri alan ve bu cihazları yönetmenizi sağlayan bulut tabanlı bir uygulama.
- Bulut tabanlı uygulamanıza bağlı özel kod çalıştıran birden fazla cihaz.

Yeni bir Azure IoT Central uygulamasını hızlıca dağıtabilir ve sonra tarayıcınızda özel gereksinimlerinize göre özelleştirebilirsiniz. Bir Oluşturucu olarak, uygulamanıza bağlanan cihazlar için bir _cihaz şablonu_ oluşturmak üzere Web tabanlı araçları kullanın. Cihaz şablonu, şöyle bir cihaz türünün özelliklerini ve davranışını tanımlayan şema ' dir:

- Gönderdiği telemetri.
- Bir operatörün değiştirebileceği iş özellikleri.
- Bir cihaz tarafından ayarlanan ve uygulamada salt okunur özellikte olan cihaz özellikleri.
- Uygulamanın yanıt verdiği eşikler.
- Cihazın davranışını belirleyen ayarlar.

Cihaz şablonlarınızı ve uygulamanızı Azure IoT Central’ın sizin için oluşturduğu sanal verilerle hemen test edebilirsiniz.

Oluşturucu olarak, Azure IoT Central uygulamanızın kullanıcı arabirimini uygulamanızın günlük kullanımından sorumlu operatörler için de özelleştirebilirsiniz. Bir oluşturucu aşağıdaki özelleştirmeleri yapabilir:

- Bir cihaz şablonundaki özellik ve ayarların düzenini tanımlama.
- Operatörlerin içgörüleri keşfetmesine ve sorunları daha hızlı çözümlemesine yardımcı olacak özel panoları yapılandırma.
- Bağlı cihazlarınızdan zaman serisi verilerini keşfetmek için özel analizler yapılandırma.

## <a name="connect-your-devices"></a>Cihazlarınızı bağlama

Oluşturucunun uygulamaya bağlanabilen cihaz türlerini tanımlamasından sonra, cihaz geliştiricisi cihazlar üzerinde çalıştırılacak kodu oluşturur. Cihaz geliştiricisi olarak, cihaz kodunuzu oluşturmak için Microsoft'un açık kaynak [Azure IoT SDK’larını](https://github.com/Azure/azure-iot-sdks) kullanırsınız. Bu SDK’lar, Azure IoT Central uygulamanıza cihazlarınızı bağlama gereksinimlerinizi karşılamak üzere geniş dil, platform ve protokol desteği sunar. SDK 'lar aşağıdaki cihaz yeteneklerini uygulamanıza yardımcı olur:

- Güvenli bir bağlantı oluşturma.
- Telemetri gönderme.
- Durum bildirme.
- Yapılandırma güncelleştirmeleri alma.

Daha fazla bilgi için, [Azure IoT SDK’larını kullanmanın avantajları ve kullanmamanız durumunda kaçınılması gereken tuzaklar](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/) başlıklı blog gönderisine bakın.

## <a name="manage-your-application"></a>Uygulamanızı yönetme

Azure IoT Central uygulamaları tamamen Microsoft tarafından barındırılır, böylece uygulamalarınızı yönetmenin idari ek yükü azalır.

Bir operatör olarak, Azure IoT Central uygulamanızı kullanarak Azure IoT Central çözümünüzdeki cihazları yönetebilirsiniz. İşleçler gibi görevler şunlardır:

- Uygulamaya bağlı cihazları izleme.
- Cihazlarla ilgili sorunları giderme ve düzeltme.
- Yeni cihazlar hazırlama.

Bir Oluşturucu olarak, bağlı cihazlardan veri akışı üzerinde çalışan özel kurallar ve eylemler tanımlayabilirsiniz. Operatör ise uygulama içindeki görevleri denetlemek ve otomatik hale getirmek için cihaz düzeyinde bu kuralları etkinleştirebilir ya da devre dışı bırakabilir.

Yöneticiler [, Kullanıcı rolleri ve izinleriyle](howto-administer.md)uygulamanıza erişimi yönetir.

## <a name="next-steps"></a>Sonraki adımlar

Azure IoT Central’a genel bir bakış elde ettiğinize göre, aşağıdaki önerilen adımlara geçebilirsiniz:

- [Azure IoT Central ile Azure IoT çözüm hızlandırıcıları](overview-iot-options.md) arasındaki farklılıkları anlayın.
- [Azure IoT Central Kullanıcı Arabirimi](overview-iot-central-tour.md)’ni tanıma.
- [Azure IoT Central uygulaması oluşturmaya](quick-deploy-iot-central.md) bağlama.
- Aşağıdaki işlemlerin nasıl yapılacağını gösteren bir öğretici dizisini takip etme:
  - [Oluşturucu olarak, bir cihaz şablonu oluşturma](tutorial-define-device-type.md)
  - [Oluşturucu olarak, çözümünüzü otomatikleştirmek için kurallar ekleme](tutorial-configure-rules.md)
  - [Oluşturucu olarak, uygulamanızı operatörler için özelleştirme](tutorial-customize-operator.md)
  - [Operatör olarak, cihazlarınızı izleme](tutorial-monitor-devices.md)
  - [Operatör olarak, çözümünüze gerçek bir cihaz ekleme](tutorial-add-device.md)
  - [Cihaz geliştiricisi olarak, cihazlarınız için kod oluşturma](tutorial-add-device.md#prepare-the-client-code)
