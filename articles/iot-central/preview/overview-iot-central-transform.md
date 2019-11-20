---
title: Dönüşümüne genel bakış
description: Dönüştürme hakkında bilgi edinin
author: viv-liu
ms.author: viviali
ms.date: 10/11/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: corywink
ms.openlocfilehash: 270017e23bef4be439dbe70bf1845f75333c5311
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73895546"
---
# <a name="transform-your-iot-data-preview-features"></a>IoT verilerinizi dönüştürme (Önizleme özellikleri)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Bir uygulama platformu olarak IoT Central, IoT verilerinizi, işlem yapılabilir sonuçları sağlayan iş içgörülerine dönüştürmenizi sağlayacak birkaç temel model sağlar. IoT Central, Iş kolu uygulamaları ve özel uygulamalarla tümleştirmeler oluşturmak için IoT verilerinizi Harici sistemlere genişletmenin yollarını sağlar. Teknisyenleri bir mobil hizmet aracılığıyla bilgilendirmek için kurallar oluşturarak cihaz sistem durumu ve işlemlerini izleyebilirsiniz. Ham telemetri verilerini Azure 'da hizmetlere aktararak özel analiz ve makine öğrenimi ile belirli iş öngörüleri oluşturabilirsiniz. Cihazları izleyip denetleyen ve genel API 'Leri kullanarak IoT Central uygulamanızı yönetebilen hizmetler ve araçlar oluşturabilirsiniz. 

![IoT Central genel bakışa Dönüştür](media/overview-iot-central-transform/transform.png)

## <a name="monitor-device-health-and-operations-using-rules"></a>Kuralları kullanarak cihaz sistem durumunu ve işlemlerini izleme
Makineleriniz bağlanıp verileri gönderdikten sonra, hangi makinelerin sorun yaşadığını tanımlayabilir veya hata mesajlarını, normal olarak en az kapalı kalma süresiyle çalışacak şekilde düzeltibilecekleri şekilde belirleyebilirsiniz. Cihazların gönderdiği Telemetriyi izlemek ve bir eşik geçildiğinde veya belirli bir olay iletisi gönderildiğinde sizi uyarmak için IoT Central uygulamanızda kurallar oluşturabilirsiniz. Doğru kişilere ve sağ aşağı akış sistemlerine bildirimde bulunan kurallarınız için e-posta eylemleri ve Web kancaları gibi eylemleri yapılandırabilirsiniz. Kurallar hakkında daha fazla bilgi edinin.

## <a name="run-custom-analytics-and-processing-on-your-exported-data"></a>Özel analiz ve işleme, verdiğiniz verilerinizde çalıştırın
Ham IoT telemetrinizi işlemek ve nihai sonucu depolamak üzere özel analiz işlem hatları oluşturarak, makine verimlilik eğilimlerini belirleme veya bir fabrika katında gelecekteki enerji kullanımını tahmin etme gibi son derece özel iş öngörüleri oluşturabilirsiniz. IoT Central uygulamanızda veri dışarı aktarmaları oluşturarak telemetri, cihaz özellikleri ve yaşam döngüsünü ve cihaz şablonu değişiklik bilgilerini diğer Azure hizmetlerine aktarabilirsiniz. böylece, sizin için en değerli araçlarda verileri çözümleyebilir, saklayabilir ve görselleştirebilirsiniz. Buradan veri aktarma hakkında daha fazla bilgi edinin.

## <a name="build-custom-iot-solutions-and-integrations-with-apis"></a>API 'lerle özel IoT çözümleri ve tümleştirmeler oluşturma
Cihazları uzaktan denetleyebilen ve yeni cihazları ayarlayan ve mevcut Iş kolu uygulamalarıyla özel tümleştirmelerin yanı sıra, IoT cihazlarınızla ve işletmenize uygun verilerle etkileşimde bulunmak için mobil yardımcı uygulamalar gibi IoT çözümleri oluşturabilirsiniz. IoT Central cihaz modelleme, ekleme, yönetim ve veri erişimi için omurga olarak kullanabilirsiniz. Bu öğrenme modülündeki ortak API hakkında daha fazla bilgi edinin.
