---
title: IoT çözüm hızlandırıcılarına giriş-Azure | Microsoft Docs
description: Azure IoT çözüm hızlandırıcıları hakkında bilgi edinin. IoT çözüm hızlandırıcıları, IoT çözümlerini dağıtmak için kullanılan tam kapsamlı, uçtan uca ve dağıtıma hazır sistemlerdir.
author: dominicbetts
ms.author: dobett
ms.date: 03/09/2019
ms.topic: overview
ms.custom: mvc
ms.service: iot-accelerators
services: iot-accelerators
manager: timlt
ms.openlocfilehash: c966051ed5699d408fe83f1e9c862ca78b3282c4
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714551"
---
# <a name="what-are-azure-iot-solution-accelerators"></a>Azure IoT çözüm hızlandırıcıları nedir?

Bulut tabanlı bir IoT çözümü genellikle cihaz bağlantısı, veri işleme ve analiz ve sunum işlemlerini yönetmek için özel kod ve bulut hizmetleri kullanır.

IoT çözüm hızlandırıcıları sık kullanılan IoT senaryolarını uygulayan tam kapsamlı ve dağıtıma hazır IoT çözümleridir. Senaryolar bağlı fabrika ve cihaz simülasyonundan oluşur. Bir çözüm hızlandırıcısını dağıttığınızda gerekli tüm bulut tabanlı hizmetler ve gerekli uygulama kodları dağıtılır.

Çözüm hızlandırıcıları, kendi IoT çözümlerinizi oluşturmak için başlangıç noktalarıdır. Tüm çözüm hızlandırıcılarının kaynak kodu açık kaynaktır ve GitHub üzerinde mevcuttur. Çözüm hızlandırıcılarını indirmeniz ve gereksinimlerinize göre özelleştirmeniz önerilir.

Sıfırdan özel bir IoT çözümü derlemeden önce çözüm hızlandırıcılarını eğitim aracı olarak kullanabilirsiniz. Çözüm hızlandırıcıları, bulut tabanlı IoT çözümleri için uygulayabileceğiniz kanıtlanmış yöntemler sunar.

Her çözüm hızlandırıcısındaki uygulama kodunda çözüm hızlandırıcısını yönetmenizi sağlayan bir web uygulaması bulunur.

> [!NOTE]
> Uzaktan izleme ve tahmine dayalı bakım çözümleri [Azure IoT Çözüm Hızlandırıcıları](https://www.azureiotsolutions.com/Accelerators) sitesinden kaldırılmıştır. Daha fazla bilgi edinmek için bkz. [Azure IoT Çözüm Hızlandırıcıları nelerdir? (önceki sürüm)](/previous-versions/azure/iot-accelerators/about-iot-accelerators).

## <a name="supported-iot-scenarios"></a>Desteklenen IoT senaryoları

Şu anda, dağıtım için kullanabileceğiniz iki çözüm Hızlandırıcısı vardır:

### <a name="connected-factory"></a>Bağlı Fabrika

[OPC Birleşik mimari](https://opcfoundation.org/about/opc-technologies/opc-ua/) arabirimiyle endüstriyel varlıklardan telemetri toplamak ve bunları denetlemek Için [bağlı fabrika çözümü hızlandırıcıyı](iot-accelerators-connected-factory-features.md) kullanın. Endüstriyel varlıklar arasında bir fabrikanın üretim hattındaki montaj ve test istasyonları olabilir.

Bağlı fabrika panonuzu kullanarak endüstriyel cihazlarınızı izleyebilir ve yönetebilirsiniz:

:::image type="content" source="./media/about-iot-accelerators/cf-dashboard-inline.png" alt-text="Bağlı fabrika çözümü panosunu gösteren ekran görüntüsü." lightbox="./media/about-iot-accelerators/cf-dashboard-expanded.png":::

### <a name="device-simulation"></a>Cihaz Benzetimi

Gerçekçi telemetri üreten sanal cihazları çalıştırmak için [cihaz benzetimi çözüm hızlandırıcıyı](iot-accelerators-device-simulation-overview.md) kullanın. Bu çözüm hızlandırıcısını kullanarak diğer çözüm hızlandırıcılarının davranışını veya kendi IoT çözümlerinizi test edebilirsiniz.

Cihaz benzetimi web uygulamasını kullanarak benzetimlerinizi yapılandırabilir ve çalıştırabilirsiniz:

:::image type="content" source="./media/about-iot-accelerators/ds-dashboard-inline.png" alt-text="Cihaz benzetimi çözüm panosunu gösteren ekran görüntüsü." lightbox="./media/about-iot-accelerators/ds-dashboard-expanded.png":::

## <a name="design-principles"></a>Tasarım ilkeleri

Tüm çözüm hızlandırıcıları aynı tasarım ilkelerini ve hedeflerini takip etmektedir. Bu bileşenler şu şekilde tasarlanmıştır:

* **Ölçeklenebilir**: Milyonlarca cihaz bağlamanıza ve yönetmenize izin verir.
* **Genişletilebilir**: Çözümleri ihtiyaçlarınıza göre özelleştirmenizi sağlar.
* **Anlaşılır**: Çözümlerin nasıl çalıştığını ve nasıl uygulandığını kavramanızı sağlar.
* **Modüler**: Hizmetleri alternatifleriyle değiştirmeniz izin verir.
* **Güvenli**: Azure güvenliğini yerleşik bağlantı ve cihaz güvenliği özellikleriyle birleştirir.

## <a name="architectures-and-languages"></a>Mimariler ve diller

Özgün çözüm hızlandırıcıları .NET ile model-görünüm-denetleyici (MVC) mimarisi kullanılarak yazılmıştır. Microsoft, çözüm hızlandırıcıları yeni bir mikro hizmet mimarisiyle güncelleştirmektedir. Aşağıdaki tabloda, çözüm hızlandırıcılarının geçerli durumu ve GitHub deposu bağlantıları gösterilmektedir:

| Çözüm hızlandırıcısı   | Mimari  | Diller     |
| ---------------------- | ------------- | ------------- |
| Bağlı Fabrika      | MVC           | [.NET](https://github.com/Azure/azure-iot-connected-factory)          |
| Cihaz Benzetimi      | Mikro hizmetler | [.NET](https://github.com/Azure/azure-iot-pcs-device-simulation)          |

Mikro hizmet mimarisi hakkında daha fazla bilgi edinmek için bkz. [Azure IoT başvuru mimarisine Giriş](/azure/architecture/reference-architectures/iot/).

## <a name="deployment-options"></a>Dağıtım seçenekleri

Çözüm hızlandırıcılarını [Microsoft Azure IoT Çözüm Hızlandırıcıları](https://www.azureiotsolutions.com/Accelerators#) sitesinden veya komut satırını kullanarak dağıtabilirsiniz.

Çözüm Hızlandırıcısı çalıştırmanın maliyeti, [temel alınan Azure hizmetlerini çalıştırmanın Birleşik maliyetlidir](https://azure.microsoft.com/pricing). Kullanılan Azure hizmetlerinin ayrıntılarını dağıtım seçeneklerinizi belirlerken görebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

IoT çözüm hızlandırıcılardan birini denemek için hızlı başlangıç ' [a bağlı bir fabrika çözümü deneyin](quickstart-connected-factory-deploy.md).
