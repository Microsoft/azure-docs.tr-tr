---
title: Azure IoT Hub için ortak ağ erişimini yönetme
description: IoT Hub için genel ağ erişimini devre dışı bırakma ve etkinleştirme ile ilgili belgeler
author: jlian
ms.author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/22/2021
ms.openlocfilehash: fbbdaeb796dfa23906c8010a54af14eff6df0b97
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105026649"
---
# <a name="managing-public-network-access-for-your-iot-hub"></a>IoT Hub 'ınız için ortak ağ erişimini yönetme

Erişimi [sanal ağınızdaki IoT Hub 'ınız için yalnızca özel uç nokta](virtual-network-support.md)ile kısıtlamak için genel ağ erişimini devre dışı bırakın. Bunu yapmak için Azure portal veya `publicNetworkAccess` API kullanın. 

## <a name="turn-off-public-network-access-using-azure-portal"></a>Azure portal kullanarak genel ağ erişimini kapatma

1. [Azure Portal](https://portal.azure.com) ziyaret edin
2. IoT Hub'ınıza gidin.
3. Sol taraftaki menüden **ağ** ' ı seçin.
4. "Genel ağa erişime Izin ver" altında **devre dışı** seçeneğini belirleyin.
5. **Kaydet**’i seçin.

:::image type="content" source="media/iot-hub-publicnetworkaccess/turn-off-public-network-access.png" alt-text="Ortak ağ erişiminin kapatılacağı Azure portal gösteren resim" lightbox="media/iot-hub-publicnetworkaccess/turn-off-public-network-access.png":::

Ortak ağ erişimini açmak için **tüm ağlar**' ı seçtikten sonra **kaydedin**.

## <a name="accessing-the-iot-hub-after-disabling-public-network-access"></a>Ortak ağ erişimini devre dışı bıraktıktan sonra IoT Hub erişme

Genel ağ erişimi devre dışı bırakıldıktan sonra, IoT Hub yalnızca [Azure özel bağlantısı kullanılarak VNET özel uç noktası](virtual-network-support.md)aracılığıyla erişilebilir. Bu kısıtlama, IoT Hub hizmetine yönelik API çağrıları kimlik bilgilerinizle doğrudan tarayıcınız kullanılarak yapıldığından, Azure portal 'e erişimi içerir.

## <a name="iot-hub-endpoint-ip-address-and-ports-after-disabling-public-network-access"></a>Genel ağ erişimini devre dışı bıraktıktan sonra uç nokta, IP adresi ve bağlantı noktaları IoT Hub

IoT Hub, çok kiracılı bir hizmet olarak platform (PaaS) olduğundan, farklı müşteriler aynı işlem, ağ ve depolama donanım kaynakları havuzunu paylaşır. IoT Hub ana bilgisayar adları, internet üzerinden genel olarak yönlendirilebilir bir IP adresi olan genel bir uç noktaya eşlenir. Farklı müşteriler bu IoT Hub genel uç noktasını paylaşır ve geniş alan ağlarda ve şirket içi ağlarda bulunan IoT cihazları buna erişebilir. 

Genel ağ erişiminin devre dışı bırakılması, belirli bir IoT Hub kaynağında yalıtımını sağlamak için zorlanır. Hizmetin genel yolu kullanarak diğer müşteri kaynakları için etkin kalmasını sağlamak üzere, genel uç noktası çözümlenemeye devam eder, IP adresleri bulunabilir ve bağlantı noktaları açık kalır. Microsoft, kiracılar arasında tamamen yalıtımı sağlamak için birden çok güvenlik katmanını tümleştirirken, bu sorun için bir neden değildir. Daha fazla bilgi için bkz. [Azure genel bulutundaki yalıtım](../security/fundamentals/isolation-choices.md#tenant-level-isolation).

## <a name="ip-filter"></a>IP Filtresi 

Ortak ağ erişimi devre dışıysa, tüm [IP filtresi](iot-hub-ip-filtering.md) kuralları yok sayılır. Bunun nedeni, genel İnternet 'ten gelen tüm IP 'Lerin engellenmesidir. IP filtresini kullanmak için **SEÇILI IP aralıkları** seçeneğini kullanın.

## <a name="bug-fix-with-built-in-event-hub-compatible-endpoint"></a>Yerleşik Olay Hub 'ı ile uyumlu uç nokta ile hata onarımı

IoT Hub genel ağ erişimi devre dışı bırakıldığında [yerleşik Olay Hub 'ı ile uyumlu uç noktanın](iot-hub-devguide-messages-read-builtin.md) genel İnternet üzerinden erişilebilir olmaya devam ettiği IoT Hub bir hata vardır. Daha fazla bilgi edinmek ve bu hata hakkında bizimle iletişim kurmak için bkz. [IoT Hub için genel ağ erişimini devre dışı bırakma yerleşik Olay Hub 'ı uç noktasına erişimi devre dışı bırakır](https://azure.microsoft.com/updates/iot-hub-public-network-access-bug-fix)
