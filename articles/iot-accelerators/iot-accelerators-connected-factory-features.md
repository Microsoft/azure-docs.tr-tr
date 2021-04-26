---
title: Bağlı fabrika çözümü özellikleri-Azure | Microsoft Docs
description: Bu makalede, önceden yapılandırılmış bağlı fabrika çözümünün bulut panosu, kuralları ve uyarıları gibi özelliklerine genel bir bakış açıklanmaktadır.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: dobett
ms.openlocfilehash: e492ebf70f69c985691852a1c1f2351d1e09578e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105646458"
---
# <a name="what-is-connected-factory-iot-solution-accelerator"></a>Bağlı fabrika IoT Çözüm Hızlandırıcısı nedir?

> [!IMPORTANT]
> Bu makaleyi güncelleştirdiğimiz sürece, en güncel içerik için bkz. [Azure endüstriyel IoT](https://azure.github.io/Industrial-IoT/) .

Bağlı fabrika, Microsoft 'un Azure endüstriyel IoT başvuru mimarisinin, açık kaynaklı çözümde olduğu gibi paketlenmiş bir uygulamasıdır. Bunu ticari bir ürün için başlangıç noktası olarak kullanabilirsiniz. Azure [IoT çözüm hızlandırıcılarına](https://www.azureiotsolutions.com/#solutions/types/CF)bağlı fabrika çözümünün önceden oluşturulmuş bir sürümünü Azure aboneliğinize dağıtabilirsiniz.

![Bağlı fabrika çözümü panosu](./media/iot-accelerators-connected-factory-features/dashboard.png)

Bağlı fabrika çözümü hızlandırıcı [kodu GitHub ' da kullanılabilir](https://github.com/Azure/azure-iot-connected-factory).

Bağlı fabrika aşağıdaki özellikleri içerir:

## <a name="industrial-device-interoperability"></a>Endüstriyel cihaz birlikte çalışabilirliği

- OPC UA arabirimi ile endüstriyel varlıklara bağlanın.
- Bu cihazlardan canlı telemetri görmek için sanal üretim hatlarını (Docker kapsayıcılarında OPC UA sunucuları çalıştıran) kullanın.
- Bir bulut panosundan OPC UA sunucularının OPC UA bilgi modeline gözatamazsınız.

## <a name="remote-management"></a>Uzaktan yönetim

- OPC UA varlıklarınızı bulut panosundan yapılandırın (çağrı yöntemleri, okuma ve yazma verileri).
- Bir bulut panosundan OPC UA varlıklarınızdan telemetri verilerini yayımlayın ve yayımdan kaldırın.

## <a name="cloud-dashboard"></a>Bulut panosu

- Telemetri önizlemelerini doğrudan bir bulut panosunda görüntüleyin.
- Time Series Insights gezgin panosunu kullanarak telemetri verilerindeki eğilimleri görüntüleyin ve bağıntılar oluşturun.
- Bkz. bulut panosundan hesaplanan genel ekipman verimliliği (OEE) ve ana performans göstergeleri (KPI).
- Endüstriyel varlık hiyerarşilerini bir ağaç topolojisinde ve etkileşimli bir haritada görüntüleyin.
- Bir bulut panosundan uyarıları görüntüleyin, kabul edin ve kapatın.

## <a name="azure-time-series-insights"></a>Azure Time Series Insights

- [Azure Time Series Insights](../time-series-insights/time-series-insights-overview.md) , büyük miktarlarda zaman serisi verilerinin depolanması, görselleştirilmesi ve sorgulanması için oluşturulmuştur. Bağlı fabrika bu hizmetten yararlanır.
- Bağlı fabrika bu hizmetle tümleşerek cihaz verilerinizin derin ve gerçek zamanlı analizini gerçekleştirmenize olanak tanır.

## <a name="rules-and-alerts"></a>Kurallar ve uyarılar

[Uyarılar için eşik tabanlı kurallar yapılandırın](iot-accelerators-connected-factory-configure.md).

## <a name="end-to-end-security"></a>Uçtan uca güvenlik

- Rol tabanlı erişim denetimi (RBAC) kullanan kullanıcılar için güvenlik izinlerini yapılandırma.
- Uçtan uca şifreleme OPC UA kimlik doğrulaması (X. 509.440 sertifikaları kullanılarak) ve güvenlik belirteçleri kullanılarak uygulanır.

## <a name="customizability"></a>Özelleştirme

- Çözümü belirli iş gereksinimlerini karşılayacak şekilde özelleştirin.
- Tam çözüm kaynağı-GitHub 'da kullanılabilir kod. [Önceden yapılandırılmış bağlı fabrika çözüm](https://github.com/Azure/azure-iot-connected-factory) deposuna bakın.

## <a name="next-steps"></a>Sonraki adımlar

Bağlı fabrika çözümü Hızlandırıcısı hakkında daha fazla bilgi edinmek için bkz. hızlı başlangıç, [endüstriyel IoT cihazlarımı yönetmek için bulut tabanlı bir çözüm deneyin](quickstart-connected-factory-deploy.md).
