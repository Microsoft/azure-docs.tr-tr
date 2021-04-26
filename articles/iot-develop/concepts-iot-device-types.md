---
title: Azure IoT cihaz türlerine genel bakış
description: Azure IoT tarafından desteklenen farklı cihaz türlerini ve kullanılabilir araçları öğrenin.
author: ryanwinter
ms.author: rywinter
ms.service: iot-develop
ms.topic: conceptual
ms.date: 01/11/2021
ms.openlocfilehash: aa99594fe3de98635e37d15beebf015f15dc4f64
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100654564"
---
# <a name="overview-of-azure-iot-device-types"></a>Azure IoT cihaz türlerine genel bakış
IoT cihazları, çok sayıda donanım platformunda bulunur. Masaüstü bilgisayarda bulunan en son x86 CPU 'Lara kadar her şey için küçük 8 bit MCUs vardır. Birçok değişken, IoT cihazı için hangi donanımın tercih ettiğiniz ve bu makalede bazı önemli farklılıkların özetlenmektedir kararı veren karardır.

## <a name="key-hardware-differentiators"></a>Ana donanım farklılayıcısı
Donanımınızı seçerken bazı önemli etmenler maliyet, güç tüketimi, ağ ve kullanılabilir girişler ve çıktılar.

* **Maliyet:** Daha küçük ucuz cihazları, genellikle son ürünü toplu olarak üretirken kullanılır. Ancak, bu denge, cihazın geliştirilmesi, yüksek oranda kısıtlanmış cihaz için daha pahalı olabilir. Geliştirme maliyeti, üretilen tüm cihazlara yayılabilecek ve bu sayede birim başına geliştirme maliyeti düşüktür.

* **Güç:** Cihaz pille çalışıyorsa ve güç kılavuzuna bağlı olmayan bir cihaz ne kadar güç harcar önem taşır. MCUs genellikle daha düşük güç senaryoları için tasarlanmıştır ve pil ömrünü uzatmak için daha iyi bir seçenek olabilir.

* **Ağ erişimi:** Bir cihazı bir bulut hizmetine bağlamak için birçok yol vardır. Ethernet, Wi-Fi ve hücresel ve kullanılabilir seçeneklerden bazıları. Seçtiğiniz bağlantı türü, cihazın dağıtıldığı yere ve nasıl kullanıldığına bağlı olarak değişir. Örneğin, hücresel yüksek kapsam verilen etkileyici bir seçenek olabilir, ancak yüksek trafik cihazları için pahalı olabilir. Hardkablolu Ethernet, ucuz veri maliyetlerini sağlar, ancak daha az taşınabilir olmasını sağlar.

* **Giriş ve çıkışlar:** Cihazda bulunan girişler ve çıkışlar, cihazların işletim yeteneklerini doğrudan etkiler. Mikro denetleyici genellikle doğrudan yongasında yerleşik olarak bulunan çok sayıda g/ç işlevi olur ve doğrudan bağlanmak için geniş bir algılayıcı seçeneği sağlar.

## <a name="microcontrollers-vs-microprocessors"></a>Mikro denetleyiciler vs mikro Işlemciler
IoT cihazları, mikro denetleyiciler (MCUs) ve mikro işlemciler (MPUs) için iki geniş kategoride ayrılabilir.

**MCUs** , MPUs 'in çalışması için daha pahalı ve daha basittir. Bir MCU, yonga içinde bellek, arabirimler ve g/ç gibi birçok işlevi içerir. Bir MPU, bu işlevselliği destekleyici yongaların bileşenlerinden çeker. Bir MCU genellikle gerçek zamanlı bir işletim sistemi (RTOS) kullanır veya çıplak (OS) çalıştırması ve dış olaylara gerçek zamanlı yanıt ve son derece belirleyici bir işlem sağlar.

**MPUs** , genellikle belirleyici olmayan gerçek zamanlı bir yanıt sağlayan Windows, Linux veya MacOSX gibi genel amaçlı bir işletim sistemini çalıştırır. Genellikle bir görevin ne zaman tamamlanabileceğini garanti etmez. 

:::image type="content" border="false" source="media/concepts-iot-device-types/mcu-vs-mpu.png" alt-text="MCU vs MPU":::

Aşağıda, bir MCU ve MPU tabanlı sistem arasındaki bazı tanımlama farklarını gösteren bir tablo verilmiştir:

||Mikro denetleyici (MCU)|Mikro işlemci (MPU)|
|-|-|-|
|**CPU**| Less | Daha fazla |
|**KOÇ**| Less | Daha fazla |
|**In**| Less | Daha fazla |
|**İşletim sistemi**| Hayır veya RTOS | Genel Amaçlı |
|**Geliştirme güçlüğü**| Kaybet |  Kolay |
|**Güç tüketimi**| Daha az | Daha fazla |
|**Maliyet**| Daha az | Daha fazla |
|**Olmayan**| Yes | Hayır-özel durum ile|
|**Cihaz boyutu**| Artı | Boyutta |
