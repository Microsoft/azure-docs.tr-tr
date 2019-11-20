---
title: Mimari IoT bağlı lojistik | Microsoft Docs
description: IoT Central için IoT bağlı lojistik uygulama şablonu mimarisi
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: overview
ms.date: 10/20/2019
ms.openlocfilehash: 797488632f04af9a170f05590154e50310570b28
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73890715"
---
# <a name="architecture-of-iot-central-connected-logistics-application-template"></a>IoT Central bağlı lojistik uygulama şablonu mimarisi

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

İş ortakları & Müşteri, uçtan uca **bağlı lojistik çözümlerini**geliştirmek için aşağıdaki kılavuzun & uygulama şablonundan yararlanabilir.

> [!div class="mx-imgBorder"]
> ![bağlı lojistik panosu](./media/concept-connected-logistics-architecture/connected-logistics-architecture.png)

1. Bir ağ geçidi cihazına telemetri verileri gönderen IoT etiketleri kümesi
2. IoT Central telemetri ve toplu Öngörüler gönderen ağ geçidi cihazları
3. Veriler, işleme için istenen Azure hizmetine yönlendirilir
4. ASA veya Azure Işlevleri gibi Azure Hizmetleri, veri akışlarını yeniden biçimlendirmek ve istenen depolama hesaplarına göndermek için yararlanılabilir olabilir 
5. Çeşitli iş iş akışları, son kullanıcı iş uygulamaları tarafından desteklenmektedir

## <a name="details"></a>Ayrıntılar
Aşağıdaki bölümde, IoT etiketlerinden & ağ geçitlerinden kavramsal mimari telemetri alma 'nın her bir bölümü özetlenmektedir

## <a name="iot-tags"></a>IoT etiketleri
IoT etiketleri; sıcaklık, nem, darbe, eğim & ışığı gibi fiziksel, çevresel ve çevresel algılayıcı özellikleri sağlar. IoT etiketleri genellikle Zigbee (802.15.4) aracılığıyla ağ geçidi cihazına bağlanır. Etiketler ucuz sensörlerdir; Bu nedenle, ters lojistik ile güçlüklere engel olmak için tipik bir lojistik yolculuğunun sonunda atılabilir.

## <a name="gateway"></a>Ağ geçidi
Ağ geçitleri Ayrıca çevresel algılama özelliklerine sahip IoT etiketleri olarak davranabilir. Ağ Geçidi, hücresel, Wi-Fi kanalları aracılığıyla yukarı akış Azure IoT bulut bağlantısına (MQTT) izin verebilir.  Bluetooth, NFC ve 802.15.4 kablosuz algılayıcı ağı (WSN) modları, IoT etiketleriyle aşağı akış iletişimi için kullanılır. Ağ geçitleri uçtan uca güvenli bulut bağlantısı, IoT etiketi eşleştirmesi, algılayıcı veri toplama, veri saklama ve uyarı eşiklerini yapılandırma yeteneği sağlar.

## <a name="device-management-with-iot-central"></a>IoT Central ile cihaz yönetimi 
Azure IoT Central, IoT cihaz bağlantısı, yapılandırma ve yönetimini kolaylaştıran bir çözüm geliştirme platformudur. Platform IoT cihaz yönetimi, işlemler ve ilgili geliştirmelerin yükünü ve maliyetlerini önemli ölçüde azaltır. Müşteriler & iş ortakları, bir uçtan uca kurumsal çözüm oluşturabilir ve bu da lojistik 'de dijital bir geri bildirim döngüsü elde edebilir.

## <a name="business-insights--actions-via-data-egress"></a>Veri çıkışı aracılığıyla iş öngörüleri & eylemleri 
IoT Central platform sürekli veri dışa aktarma (CDE) ve API 'Ler aracılığıyla zengin genişletilebilirlik seçenekleri sağlar. Telemetri veri işleme veya ham telemetri temelinde iş öngörüleri genellikle tercih edilen iş kolu uygulamasına aktarılabilir. Bu, daha zengin Öngörüler & makine öğrenimi modellerini derlemek, eğitme ve dağıtmak için Web kancası, hizmet veri yolu, Olay Hub 'ı veya blob depolama aracılığıyla elde edilebilir.

## <a name="next-steps"></a>Sonraki adımlar
* [Bağlı lojistik çözüm şablonunu](./tutorial-iot-central-connected-logistics-pnp.md) dağıtmayı öğrenin
* [IoT Central perakende şablonları](./overview-iot-central-retail-pnp.md) hakkında daha fazla bilgi edinin
* IoT Central hakkında daha fazla bilgi edinin [IoT Central genel bakış](../preview/overview-iot-central.md)
