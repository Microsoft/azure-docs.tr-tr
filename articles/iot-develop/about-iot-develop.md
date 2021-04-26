---
title: Azure IoT cihaz ve uygulama geliştirmeye giriş
description: Azure IoT kullanarak katıştırılmış cihaz geliştirme ve cihaz özellikli bulut uygulamaları oluşturma hakkında bilgi edinin.
author: ryanwinter
ms.author: rywinter
ms.service: iot-develop
ms.topic: overview
ms.date: 01/11/2021
ms.openlocfilehash: dd4e53eebe6593db457798f009d3d05ddcbd77b8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100655030"
---
# <a name="what-is-azure-iot-device-and-application-development"></a>Azure IoT cihaz ve uygulama geliştirme nedir?

Azure IoT, IoT cihazlarınızı bağlayan, izleyen ve denetleyen bir yönetilen ve platform hizmetleri koleksiyonudur. Azure IoT, geliştiricilere kapsamlı bir seçenek kümesi sunar. Seçenekleriniz cihaz platformları, bulut Hizmetleri, SDK 'lar ve cihaz özellikli bulut uygulamaları oluşturmaya yönelik araçlar içerir.

Bu makalede, Azure IoT ile çalışmaya başlayan geliştiriciler için çeşitli önemli noktalar açıklanır. Bu kavramlar sizi IoT cihaz geliştiricisi olarak, Azure IoT seçeneklerinizde ve başlamadan nasıl başlayabileceğiniz bir şekilde yönelecektir. Bu makalede özellikle bu kavramlar genel bakışlar:
- [Cihaz geliştirme rollerini anlama](#device-development-roles)
- [Donanımınızı seçme](#choosing-your-hardware)
- [SDK seçme](#choosing-an-sdk)
- [Bağlantı seçeneklerini belirleme](#selecting-connection-options)

## <a name="device-development-roles"></a>Cihaz geliştirme rolleri
Bu makalede, cihaz geliştiricileri arasında gözlemleyebileceğiniz iki ortak rol ele alınmaktadır. Burada kullanılan bir rol, ilgili geliştirme görevlerinin koleksiyonudur. Üzerinde çalışmakta olduğunuz geliştirme rolü türünü anlamak yararlı olur. Rolünüz, yaptığınız birçok geliştirme seçimi etkiler.

* **Cihaz uygulaması geliştirme:** Modern geliştirme uygulamalarıyla hizalanır, daha yüksek sıralı dillerin birçoğunu hedefler ve Windows veya Linux gibi genel amaçlı bir işletim sisteminde yürütülür.

* **Katıştırılmış cihaz geliştirme:** Kaynak kısıtlı cihazları geliştirme hedefleme açıklanmaktadır. Birim maliyetlerini, güç tüketimini veya cihaz boyutunu azaltmak için genellikle kaynak kısıtlanmış bir cihaz kullanılır. Bu cihazlar üzerinde yürüttikleri donanım platformunda doğrudan denetime sahiptir.

### <a name="device-application-development"></a>Cihaz uygulaması geliştirme
Cihaz uygulaması geliştiricileri, var olan cihazları buluta bağlanmak ve IoT çözümleriyle bütünleştirmek için uyarlardır. Bu cihazlar, C# veya Python gibi daha yüksek sıralı dilleri destekleyebilir ve genellikle Windows veya Linux gibi sağlam bir genel amaçlı işletim sistemini destekler. Ortak hedef cihazlar PC 'Leri, kapsayıcıları, Raspberry PSIS ve mobil cihazları içerir. 

Ölçekteki kısıtlı cihazları geliştirmek yerine, bu geliştiriciler bulut çözümü için gereken belirli bir IoT senaryosunu etkinleştirmeye odaklanmaktadır. Bu geliştiricilerden bazıları bulut çözümü için kısıtlanmış cihazlarda da çalışacaktır. Kısıtlanmış cihazlarla çalışan geliştiriciler için aşağıdaki [eklenmiş cihaz geliştirme](#embedded-device-development) yolu bölümüne bakın.

> [!TIP]
> Başlamak için [Kısıtlanmış cihaz SDK](about-iot-sdks.md#unconstrained-device-sdks) 'larına bakın.

### <a name="embedded-device-development"></a>Katıştırılmış cihaz geliştirme
Katıştırılmış Geliştirme sınırlı bellek ve işleme sahip kısıtlı cihazları hedefler. Kısıtlanmış cihazlar, geleneksel bir geliştirme platformuna kıyasla neler elde edilebileceklerini kısıtlar.

Katıştırılmış cihazlar genellikle gerçek zamanlı bir işletim sistemi (RTOS) veya hiç işletim sistemi kullanmaz. Katıştırılmış cihazlar, genel amaçlı bir işletim sisteminin olmamasından dolayı donanım üzerinde tam denetime sahiptir. Bu olgu, gömülü cihazları gerçek zamanlı sistemler için iyi bir seçenek yapar.

Geçerli eklenmiş SDK 'lar **C** dilini hedefleyin. Katıştırılmış SDK 'lar, herhangi bir işletim sistemi veya Azure RTOS desteği sağlar. Bunlar, ekli hedefler göz önünde bulundurularak tasarlanırlar. Tasarım konuları, en az bir parmak izi ve bellek ayırma tasarımı gereksinimini içerir.

Cihazınız genel amaçlı bir işletim sistemi çalıştırabiliyor ise, [cihaz uygulaması geliştirme](#device-application-development) yolunu takip etmenizi öneririz. Daha zengin bir geliştirme seçenekleri kümesi sağlar.

> [!TIP]
> Başlamak için [Kısıtlanmış cihaz SDK](about-iot-sdks.md#constrained-device-sdks) 'larına bakın.

## <a name="choosing-your-hardware"></a>Donanımınızı seçme
Azure IoT cihazları, IoT çözümünün temel yapı taşlarıdır ve ortamlarına göre gözlemlemeden ve etkileşimde bulunmaktan sorumludur. Birçok farklı IoT cihazı türü vardır ve var olan cihaz türlerini ve bunların geliştirme sürecinizi nasıl etkileyebileceğini anlamak yararlı olur.

Bu makalede ele alınan cihaz türleri arasındaki fark hakkında daha fazla bilgi için [IoT cihaz türleri hakkında](concepts-iot-device-types.md)bölümünü okuyun.

## <a name="choosing-an-sdk"></a>SDK seçme
Bir Azure IoT cihaz geliştiricisi olarak, cihaz özellikli bulut uygulamaları oluşturmanıza yardımcı olmak için çeşitli cihaz SDK 'ları ve Azure hizmeti SDK 'Ları vardır. SDK 'lar geliştirme çabalarınızı kolaylaştırır ve cihazların bağlanmasına ve yönetilmesine ilişkin karmaşıklığın çoğunu basitleştirir. 

[Cihaz geliştirme rolleri](#device-development-roles) bölümünde gösterildiği gibi, cihaz geliştirmesi için üç tür IoT SDK 'sı vardır:
- Katıştırılmış cihaz SDK 'Ları (kısıtlı cihazlar için)
- Cihaz SDK 'Ları (mevcut Cihazları IoT uygulamalarına bağlamak için daha yüksek sıra dillerini kullanmak üzere)
- Hizmet SDK 'Ları (cihazları hizmetlere bağlayan Azure IoT çözümleri oluşturmak için)

Azure IoT cihazı veya hizmet SDK 'Sı seçme hakkında daha fazla bilgi edinmek için bkz. [Azure IoT cihaz SDK 'Larına genel bakış](about-iot-sdks.md).

## <a name="selecting-connection-options"></a>Bağlantı seçeneklerini belirleme
Geliştirme sürecinde önemli bir adım, cihazlarınızı bağlamak ve yönetmek için kullanacağınız seçenek kümesini seçmektir. Dikkate alınması gereken iki önemli nokta vardır:
- Cihazlarınızı barındırmak için bir IoT uygulaması platformu seçme. Azure IoT için, IoT Hub veya IoT Central seçme anlamına gelir.
- Cihazları bağlanmanıza, yönetmenize ve izlemenize yardımcı olacak geliştirici araçları 'nı seçme.

Uygulama platformu ve araçlar seçme hakkında daha fazla bilgi edinmek için bkz. [genel bakış: Azure IoT cihaz geliştiricileri Için bağlantı seçenekleri](concepts-overview-connection-options.md).

## <a name="next-steps"></a>Sonraki adımlar
Geliştirme rolünüzle en uygun olan aşağıdaki hızlı başlangıç serilerinden birini seçin. Bu makalelerde, cihazları barındırmak, bir SDK kullanarak, bir cihazı bağlamak ve telemetri göndermek için bir Azure IoT uygulaması oluşturma temelleri gösterilmektedir.  
- Cihaz uygulaması geliştirme için:  [hızlı başlangıç: bir cihazdan Azure 'a telemetri gönderme IoT Central](quickstart-send-telemetry-python.md)
- Katıştırılmış cihaz geliştirmesi için: [Azure IoT Embedded cihaz geliştirmeye Başlarken](quickstart-device-development.md)
