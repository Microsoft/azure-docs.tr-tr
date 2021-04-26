---
title: Özgün sürümden farklılıklar
titleSuffix: Azure Digital Twins
description: Yeni Azure dijital TWINS sürümünde nelerin değiştiğini anlayın
author: baanders
ms.author: baanders
ms.date: 1/28/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 2bfd36b79ad800c14a68948aa8488e842d3f4def
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107481183"
---
# <a name="what-is-the-new-azure-digital-twins-how-is-it-different-from-the-original-version-2018"></a>Yeni Azure dijital TWINS nedir? Özgün sürümden (2018) farklı mi?

Azure Digital TWINS 'in ilk genel önizlemesi Ekim 2018 ' de yayımlanmıştır. Bu orijinal sürümden temel kavramlar geçerli hizmete taşınırken, hizmet ve uygulama ayrıntılarının birçoğu hizmeti daha esnek ve erişilebilir hale getirmek için değiştirilmiştir. Bu değişiklikler müşteri geri bildirimleri tarafından görüşmiş.

> [!IMPORTANT]
> Yeni hizmetin genişletilmiş özellikleri 'nin açık olduğu, özgün Azure dijital TWINS hizmeti kullanımdan kaldırılmıştır. 2021 Ocak itibariyle API 'Ler ve ilişkili veriler artık kullanılamıyor.

İlk genel önizleme sırasında Azure Digital TWINS 'in ilk sürümünü kullandıysanız, geçerli hizmetle nasıl çalışacağınızı öğrenmek ve özelliklerinden yararlanmak için bu makaledeki bilgileri ve en iyi uygulamaları kullanın.

## <a name="differences-by-topic"></a>Konuya göre farklılıklar

Aşağıdaki grafik, hizmetin orijinal sürümü ve geçerli hizmet arasında değişen kavramların yan yana görünümünü sağlar.

| Konu | Özgün sürümde | Geçerli sürümde |
| --- | --- | --- | --- |
| **Modelleme**<br>*Daha esnek* | Özgün sürüm akıllı boşluklar etrafında tasarlanmıştı, bu nedenle binalar için yerleşik bir sözlük ile birlikte geldi. | Geçerli Azure dijital TWINS etki alanı belirsiz. Çözümünüz için kendi özel sözlüğünüzü ve özel modellerinizi tanımlayabilir ve daha esnek yollarla daha fazla ortam temsil edebilirsiniz.<br><br>Kavramlar hakkında daha fazla bilgi edinin [*: özel modeller*](concepts-models.md). |
| **Topoloji**<br>*Daha esnek*| Özgün sürüm, akıllı alanlara uyarlanmış bir ağaç veri yapısını destekliyordu. Dijital TWINS Hiyerarşik ilişkilerle bağlandı. | Geçerli sürümle birlikte dijital ikgörüler, sizin istediğiniz şekilde düzenlenmiş, rastgele grafik Topolojilerine bağlanabilir. Bu, gerçek dünyanın karmaşık ilişkilerini ifade etmek için size daha fazla esneklik sağlar.<br><br>Kavramlar hakkında daha fazla bilgi edinin [*: dijital TWINS ve ikizi grafiği*](concepts-twins-graph.md). |
| **İşlem**<br>*Daha zengin, daha esnek* | Özgün sürümde, olay ve telemetri işleme mantığı JavaScript Kullanıcı tanımlı işlevlerde (UDF 'ler) tanımlanmıştır. UDF 'ler ile hata ayıklama sınırlandı. | Geçerli yayında açık bir işlem modeli var: [Azure işlevleri](../azure-functions/functions-overview.md)gibi dış işlem kaynaklarını ekleyerek özel mantık sağlarsınız. Bu, tercih ettiğiniz bir programlama dilini kullanmanıza, kısıtlama olmadan özel kod kitaplıklarına erişebilmenize ve dış hizmetin sahip olabileceği geliştirme ve hata ayıklama kaynaklarından faydalanabilir.<br><br>[*Nasıl yapılır: verileri işlemek için bir Azure Işlevi ayarlama hakkında*](how-to-create-azure-function.md)daha fazla bilgi edinin. |
| **IoT Hub ile cihaz yönetimi**<br>*Daha erişilebilir* | Özgün sürüme, Azure dijital TWINS hizmeti için dahili bir [IoT Hub](../iot-hub/about-iot-hub.md) örneği ile yönetilen cihazlar. Bu tümleşik hub, geliştiriciler için tam olarak erişilebilir değildi. | Geçerli sürümde, bağımsız olarak oluşturulan bir IoT Hub örneği (zaten yönettiği tüm cihazlarla birlikte) ekleyerek kendi IoT Hub 'ınızı "kendi" getirin. Bu, IoT Hub özelliklerine tam erişim sağlar ve sizi cihaz yönetiminin denetimine geçirir.<br><br>[*IoT Hub 'Dan nasıl yapılır: alma telemetrisi*](how-to-ingest-iot-hub-data.md)hakkında daha fazla bilgi edinin. |
| **Güvenlik**<br>*Daha fazla standart* | Özgün sürümde, örneğinizin erişimini yönetmek için kullanabileceğiniz önceden tanımlanmış roller vardı. | Geçerli yayın, diğer Azure hizmetlerinin kullandığı aynı [Azure rol tabanlı erişim denetimi (Azure RBAC)](../role-based-access-control/overview.md) arka uç hizmetiyle tümleştirilir. Bu, çözümünüzde IoT Hub, Azure Işlevleri, Event Grid gibi diğer Azure hizmetleri arasında kimlik doğrulaması yapmayı daha kolay hale getirir.<br>RBAC ile önceden tanımlanmış rolleri kullanmaya devam edebilir veya özel roller oluşturabilir ve yapılandırabilirsiniz.<br><br>Kavramlar hakkında daha fazla bilgi edinin [*: Azure dijital TWINS çözümleri Için güvenlik*](concepts-security.md). |
| **Ölçeklenebilirlik**<br>*İlerisi* | Orijinal sürümde cihazlar, iletiler, grafikler ve ölçek birimleri için ölçek sınırlamaları vardı. Abonelik başına yalnızca bir Azure dijital TWINS örneği desteklenir.  | Geçerli yayın, geliştirilmiş ölçeklenebilirlik ile yeni bir mimariye dayanır ve daha fazla işlem gücü içerir. Ayrıca, her abonelik için bölge başına 10 örnek destekler.<br><br>Geçerli sürümdeki limitlerin ayrıntıları için bkz. [*Azure Digital TWINS hizmet limitleri*](reference-service-limits.md) . |

## <a name="service-limits"></a>Hizmet sınırlamaları

Azure dijital TWINS sınırları listesi için bkz. [*Azure Digital TWINS hizmet limitleri*](reference-service-limits.md).

## <a name="next-steps"></a>Sonraki adımlar

* Hızlı başlangıç: [*hızlı başlangıç: örnek bir senaryoyu araştırmak*](quickstart-azure-digital-twins-explorer.md)için geçerli sürümle çalışmaya göz atın.

* Ya da kavramlarla ilgili temel kavramlar hakkında okumaya başlayabilirsiniz [*: özel modeller*](concepts-models.md).