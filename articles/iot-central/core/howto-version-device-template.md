---
title: Azure IoT Central uygulamaları için cihaz şablonu sürümü oluşturma | Microsoft Docs
description: Yeni sürümler oluşturarak ve canlı bağlı cihazlarınızı etkilemeden cihaz şablonlarınızda yineleme yapın
author: sandeeppujar
ms.author: sandeepu
ms.date: 07/08/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: feaa8abcb6635573b3680b77befa5ccb462ec73a
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73930117"
---
# <a name="create-a-new-device-template-version"></a>Yeni bir cihaz şablonu sürümü oluştur

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

Azure IoT Central IoT uygulamalarının hızla geliştirilmesini sağlar. Ölçümleri, ayarları veya özellikleri ekleyerek, düzenleyerek veya silerek cihaz şablonu tasarımlarınız üzerinde hızlıca yineleme yapabilirsiniz. Bu değişikliklerden bazıları şu anda bağlı olan cihazlar için zorlayıcıdır. Azure IoT Central, bu son değişiklikleri tanımlar ve bu güncelleştirmeleri cihazlara güvenle dağıtmak için bir yol sağlar.

Bir cihaz şablonunun oluşturduğunuz sürüm numarası vardır. Varsayılan olarak, sürüm numarası 1.0.0 ' dir. Bir cihaz şablonunu düzenlerseniz ve bu değişiklik canlı bağlı cihazları etkileyebileceğinden, Azure IoT Central yeni bir cihaz şablonu sürümü oluşturmanızı ister.

> [!NOTE]
> Cihaz şablonu oluşturma hakkında daha fazla bilgi edinmek için bkz. [cihaz şablonu ayarlama](howto-set-up-template.md)

## <a name="changes-that-prompt-a-version-change"></a>Sürüm değişikliğini isteyen değişiklikler

Cihaz şablonunuzun ayarlarına veya özelliklerine ilişkin genel değişiklikler için bir sürüm değişikliği yapmanız istenir.

> [!NOTE]
> Cihaz şablonunda yapılan değişiklikler, cihaz veya en fazla bir cihaz bağlı olmadığında yeni bir sürüm oluşturulmasını istemez.

Aşağıdaki listede, yeni bir sürüm gerektirebilecek Kullanıcı eylemleri açıklanmaktadır:

* Özellikler (gerekli)
    * Gerekli bir özelliği ekleme veya silme
    * Bir özelliğin alan adını, cihazlarınız tarafından ileti göndermek için kullanılan alan adını değiştirme.
*  Özellikler (Isteğe bağlı)
    * İsteğe bağlı bir özelliği silme
    * Bir özelliğin alan adını, cihazlarınız tarafından ileti göndermek için kullanılan alan adını değiştirme.
    * İsteğe bağlı bir özelliği gerekli bir özelliğe değiştirme
*  Ayarlar
    * Ayar ekleme veya silme
    * Bir ayarın alan adını değiştirme, cihazlarınız tarafından ileti göndermek ve almak için kullanılan alan adı.

## <a name="what-happens-on-version-change"></a>Sürüm değişikliğine ne olur?

Sürüm değişikliği olduğunda kurallara ve cihaz panolarına ne olur?

Cihaz şablonunun önceki sürümündeki **kurallar** değişmeden çalışmaya devam eder. Kurallar yeni cihaz şablonu sürümüne otomatik olarak geçirilmez. Yeni şablon sürümünde her zamanki gibi kurallar oluşturabilirsiniz. Daha fazla bilgi için bkz. [telemetri oluşturma kuralı ve Azure IoT Central uygulama](howto-create-telemetry-rules.md) nasıl yapılır makalesindeki bildirimleri ayarlama.

**Cihaz panoları** çeşitli kutucuk türleri içerebilir. Bazı kutucuklar ayarlar ve özellikler içerebilir. Bir kutucukta kullanılan bir özellik veya ayar kaldırıldığında, kutucuk tamamen veya kısmen bozulur. Kutucuğa gidebilir ve kutucuğu kaldırarak ya da kutucuğun içeriğini güncelleştirerek sorunu çözebilirsiniz.

## <a name="migrate-a-device-across-device-template-versions"></a>Cihazı cihaz şablonu sürümleri arasında geçirme

Cihaz şablonunun birden çok sürümünü oluşturabilirsiniz. Zaman içinde, bu cihaz şablonlarını kullanan birden fazla bağlı cihazınız olacaktır. Cihazları, cihaz şablonunuzun bir sürümünden başka bir sürümüne geçirebilirsiniz. Aşağıdaki adımlarda bir cihazın nasıl geçirileceği açıklanır:

1. **Device Explorer** sayfasına gidin.
1. Başka bir sürüme geçirmeniz gereken cihazı seçin.
1. **Cihazı geçir**' i seçin.
1. Cihazı geçirmek istediğiniz sürüm numarasını seçin ve **geçir**' i seçin.

![Cihaz geçirme](media/howto-version-device-template/pick-version.png)

## <a name="next-steps"></a>Sonraki adımlar

Artık Azure IoT Central uygulamanızda cihaz şablonu sürümlerini kullanmayı öğrendiğinize göre, önerilen sonraki adım aşağıda verilmiştir:

> [!div class="nextstepaction"]
> [Telemetri kuralları oluşturma](howto-create-telemetry-rules.md)