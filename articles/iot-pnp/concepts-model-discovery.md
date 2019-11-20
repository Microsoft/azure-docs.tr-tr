---
title: IoT Tak ve Kullan önizleme modeli bulmayı uygulama | Microsoft Docs
description: Çözüm geliştiricisi olarak, çözümünüzde IoT Tak ve Kullan modeli bulmayı nasıl uygulayabileceğinizi öğrenin.
author: ChrisGMsft
ms.author: chrisgre
ms.date: 07/17/2019
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: c37446fd5a0cdc986044405a9aa3da32462d9c04
ms.sourcegitcommit: 8e1fb03a9c3ad0fc3fd4d6c111598aa74e0b9bd4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70114267"
---
# <a name="implement-iot-plug-and-play-preview-model-discovery-in-an-iot-solution"></a>IoT çözümünde IoT Tak ve Kullan önizleme modeli bulmayı uygulama

Bu makalede, bir IoT çözümünde IoT Tak ve Kullan önizleme modeli bulmayı nasıl uygulayabileceğinizi anlatan bir çözüm geliştiricisi olarak açıklanmaktadır.  IoT Tak ve Kullan modeli bulma, IoT Tak ve Kullan cihazlarının desteklenen yetenek modellerini ve arabirimlerini nasıl tanımladığına ve bir IoT çözümünün bu yetenek modellerini ve arabirimlerini nasıl alacağını belirler.

IoT çözümünün iki geniş kategorisi vardır: bilinen bir IoT Tak ve Kullan cihazları kümesiyle çalışan ve tüm IoT Tak ve Kullan cihazlarıyla çalışan model temelli çözümler.

Bu kavram makalesinde, model bulmanın her iki çözüm türünde nasıl uygulanacağı açıklanır.

## <a name="model-discovery"></a>Model keşfi

IoT Tak ve Kullan cihazı, IoT Hub 'ınıza ilk kez bağlanırsa, bir model bilgileri telemetri iletisi gönderir. Bu ileti, cihazın uyguladığı arabirimlerin kimliklerini içerir. Çözümünüzün cihazla çalışması için, bu kimlikleri çözmesi ve her arabirim için tanımları alması gerekir.

Bir IoT Tak ve Kullan cihazının bir hub 'a bağlanmak üzere cihaz sağlama hizmeti 'ni (DPS) kullandığında aldığı adımlar aşağıda verilmiştir:

1. Cihaz açıldığında, DPS için genel bitiş noktasına bağlanır ve izin verilen yöntemlerden birini kullanarak kimlik doğrular.
1. Daha sonra DPS, cihazın kimliğini doğrular ve cihazı hangi IoT Hub 'ına atayacağını söyleyen kurala bakar. DPS daha sonra cihazı bu hub 'a kaydeder.
1. DPS cihaza bir IoT Hub bağlantı dizesi döndürür.
1. Cihaz daha sonra IoT Hub bir bulma telemetri iletisi gönderir. Bulma telemetri iletisi, cihazın uyguladığı arabirimlerin kimliklerini içerir.
1. IoT Tak ve Kullan cihazı artık IoT Hub 'ınızı kullanan bir çözümle çalışmaya hazırdır.

Cihaz doğrudan IoT Hub 'ınıza bağlanıyorsa, cihaz kodunda gömülü bir bağlantı dizesi kullanarak bağlanır. Cihaz daha sonra IoT Hub bir bulma telemetri iletisi gönderir.

Model bilgileri telemetri iletisi hakkında daha fazla bilgi için bkz. [ModelInformation](concepts-common-interfaces.md) arabirimi.

### <a name="purpose-built-iot-solutions"></a>Amaç tarafından oluşturulan IoT çözümleri

Amaç oluşturulmuş bir IoT çözümü, bilinen bir IoT Tak ve Kullan cihaz yeteneği modelleri ve arabirimleri kümesiyle birlikte çalışarak.

Çözümünüze bir süre önce bağlanacak cihazlar için yetenek modeli ve arabirimler olacaktır. Çözümünüzü hazırlamak için aşağıdaki adımları kullanın:

1. Azure 'da arabirim JSON dosyalarını, çözümünüzün okuyabildiği bir konuma depolayın.
1. IoT çözümünüze, beklenen IoT Tak ve Kullan yetenek modellerine ve arabirimine göre mantık yazın.
1. Çözümünüzün kullandığı IoT Hub 'ından gelen bildirimlere abone olun.

Yeni bir cihaz bağlantısı için bildirim aldığınızda, şu adımları izleyin:

1. Cihaz tarafından uygulanan yetenek modeli ve arabirimlerin kimliklerini almak için bulma telemetri iletisini okuyun.
1. Yetenek modelinin KIMLIĞINI, önceden depoladığınız yetenek modellerinin kimliklerine göre karşılaştırın.
1. Artık ne tür bir cihaz bağlandığını öğrenmiş olursunuz. Kullanıcıların cihazla uygun şekilde etkileşime geçmesini sağlamak için, daha önce yazdığınız mantığı kullanın.

### <a name="model-driven-solutions"></a>Model temelli çözümler

Model temelli bir IoT çözümü, tüm IoT Tak ve Kullan cihazlarla çalışabilir. Model temelli bir IoT çözümünün oluşturulması daha karmaşıktır, ancak çözümünüz gelecekte eklenen tüm cihazlarla birlikte çalışmıdır.

Model temelli bir IoT çözümü oluşturmak için IoT Tak ve Kullan arabirimi temel elemanlarına karşı mantık oluşturmanız gerekir: telemetri, Özellikler ve komutlar. IoT çözümünüzün mantığı birden çok telemetri, özellik ve komut özelliklerini birleştirerek bir cihazı temsil eder.

Çözümünüz Ayrıca kullandığı IoT Hub 'ından gelen bildirimlere abone olmalıdır.

Çözümünüz yeni bir cihaz bağlantısı için bir bildirim aldığında, şu adımları izleyin:

1. Cihaz tarafından uygulanan yetenek modeli ve arabirimlerin kimliklerini almak için bulma telemetri iletisini okuyun.
1. Her KIMLIK için, cihazın yeteneklerini bulmak için tam JSON dosyasını okuyun.
1. Çözümünüz tarafından daha önce alınan JSON dosyalarını depolamak için oluşturduğunuz önbellekte her arabirimin mevcut olup olmadığını denetleyin.
1. Daha sonra, bu KIMLIĞE sahip bir arabirimin ortak model deposunda mevcut olup olmadığını denetleyin. Daha fazla bilgi için bkz. [ortak model deposu](howto-manage-models.md).
1. Arabirim ortak model deposunda yoksa, çözümü çözümünüz tarafından bilinen tüm şirket modeli depolarında aramaya çalışın. Bir şirket modeli deposuna erişmek için bir bağlantı dizesine ihtiyacınız vardır. Daha fazla bilgi için bkz. [Şirket modeli deposu](howto-manage-models.md).
1. Tüm arabirimleri ortak model deposunda veya bir şirket modeli deposunda bulamıyorsanız, cihazın arabirim tanımını sağlayıp sağlayabileceğinizi kontrol edebilirsiniz. Bir cihaz, arabirim dosyalarını komutla alma hakkında bilgi yayımlamak için standart [ModelDefinition](concepts-common-interfaces.md) arabirimini uygulayabilir.
1. Cihaz tarafından uygulanan her arabirim için JSON dosyaları buldıysanız, cihazın yeteneklerini sıralayabilirsiniz. Kullanıcıların cihazla etkileşime geçmesini sağlamak için daha önce yazdığınız mantığı kullanın.
1. İstediğiniz zaman, cihazın yetenek modeli KIMLIĞINI ve arabirim kimliklerini almak için dijital TWINS API 'sini çağırabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Artık bir IoT çözümünü model bulma hakkında öğrendiğinize göre, çözümünüzün diğer özelliklerinden yararlanmak için [Azure IoT Platformu](overview-iot-plug-and-play.md) hakkında daha fazla bilgi edinin.
