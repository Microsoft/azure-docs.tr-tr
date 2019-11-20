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
ms.openlocfilehash: c868aa0f1c2449ccf163523c9ded25a31d1d84c4
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73820103"
---
# <a name="what-is-connected-factory-iot-solution-accelerator"></a>Bağlı fabrika IoT Çözüm Hızlandırıcısı nedir?

Bağlı fabrika, Microsoft 'un Azure endüstriyel IoT başvuru mimarisinin, açık kaynaklı çözümde olduğu gibi paketlenmiş bir uygulamasıdır. Bunu ticari bir ürün için başlangıç noktası olarak kullanabilirsiniz. Azure [IoT çözüm hızlandırıcılarına](https://www.azureiotsolutions.com/#solutions/types/CF)bağlı fabrika çözümünün önceden oluşturulmuş bir sürümünü Azure aboneliğinize dağıtabilirsiniz.

![Bağlı fabrika çözümü panosu](./media/iot-accelerators-connected-factory-features/dashboard.png)

Bağlı fabrika çözümü hızlandırıcı [kodu GitHub ' da kullanılabilir](https://github.com/Azure/azure-iot-connected-factory).

Bağlı fabrika aşağıdaki özellikleri içerir:

## <a name="industrial-device-interoperability"></a>Endüstriyel cihaz birlikte çalışabilirliği

- Bir OPC UA arabirimiyle endüstriyel varlıklara bağlanın.
- Bu cihazlardan canlı telemetri görmek için sanal üretim hatlarını (Docker kapsayıcılarında OPC UA sunucuları çalıştıran) kullanın.
- Bir bulut panosundan OPC UA sunucularının OPC UA bilgi modeline gözatamazsınız.

## <a name="remote-management"></a>Uzaktan yönetim

- OPC UA varlıklarınızı bulut panosundan yapılandırın (yöntem çağırma, veri okuma ve yazma).
- OPC UA varlıklarınızdan toplanan telemetri varlıklarını bir bulut panosundan yayımlayın ve yayından kaldırın.

## <a name="cloud-dashboard"></a>Bulut panosu

- Telemetri önizlemelerini doğrudan bir bulut panosunda görüntüleyin.
- Time Series Insights Gezgini panosunu kullanarak Telemetri verilerindeki eğilimleri görün ve bağıntılar oluşturun.
- Bkz. bulut panosundan hesaplanan genel ekipman verimliliği (OEE) ve ana performans göstergeleri (KPI).
- Endüstriyel varlık hiyerarşilerini bir ağaç topolojisinde ve etkileşimli bir haritada görüntüleyin.
- Bir bulut panosundan uyarıları görüntüleyin, kabul edin ve kapatın.

## <a name="azure-time-series-insights"></a>Azure Zaman Serisi Görüşleri

- [Azure Time Series Insights](../time-series-insights/time-series-insights-overview.md) , büyük miktarlarda zaman serisi verilerinin depolanması, görselleştirilmesi ve sorgulanması için oluşturulmuştur. Bağlı fabrika bu hizmetten yararlanır.
- Bağlı fabrika bu hizmetle tümleşerek cihaz verilerinizin derin ve gerçek zamanlı analizini gerçekleştirmenize olanak tanır.

## <a name="rules-and-alerts"></a>Kurallar ve uyarılar

[Uyarılar için eşik tabanlı kurallar yapılandırın](iot-accelerators-connected-factory-configure.md).

## <a name="end-to-end-security"></a>Uçtan uca güvenlik

- Rol Tabanlı Erişim Denetimi’ni (RBAC) kullanarak kullanıcılar için güvenlik izinlerini yapılandırın.
- Uçtan uca şifreleme OPC UA kimlik doğrulaması (X. 509.440 sertifikaları kullanılarak) ve güvenlik belirteçleri kullanılarak uygulanır.

## <a name="customizability"></a>Özelleştirme

- Çözümü belirli iş gereksinimlerini karşılayacak şekilde özelleştirin.
- Tam çözüm kaynağı-GitHub 'da kullanılabilir kod. [Önceden yapılandırılmış bağlı fabrika çözüm](https://github.com/Azure/azure-iot-connected-factory) deposuna bakın.

## <a name="next-steps"></a>Sonraki adımlar

Bağlı fabrika çözümü Hızlandırıcısı hakkında daha fazla bilgi edinmek için bkz. hızlı başlangıç, [endüstriyel IoT cihazlarımı yönetmek için bulut tabanlı bir çözüm deneyin](quickstart-connected-factory-deploy.md).
