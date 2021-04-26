---
title: Azure IoT Hub Önizleme modunu açma
description: IoT Hub için Önizleme modunu açmayı, neden istediğinizi ve bazı uyarıları nasıl kullanacağınızı öğrenin
services: iot-hub
author: jlian
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 11/24/2020
ms.author: jlian
ms.openlocfilehash: 864870c4392b12477c321c86afd9da848120490c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96621714"
---
# <a name="turn-on-preview-mode-for-iot-hub-to-try-select-new-features"></a>Yeni özellikleri seçmeyi denemek için IoT Hub Önizleme modunu açın

<!-- 
- We are working hard to bring you new features
- Some of these features require a brand new iot hub with preview mode on
- some features may not work at all or have unexpected behavior
- "Normal preview features" do NOT require preview mode 
- Support opt-in at creation time only
- Customer cannot opt back out post creation
- If customer wants to evaluate, they must use new hub dedicated for the preview
- Banners, documentations and all materials indicate preview quality: no GA guarantee at all
-->

Yeni özellikler aşağıdakiler de dahil olmak üzere IoT Hub için genel önizlemededir:

- MQTT 5
- ECC sunucu sertifikası
- TLS parçası uzunluğu anlaşması
- HTTPS/WebSocket için x509 CA zinciri desteği

Bu özellikler IoT Hub Protokolü ve kimlik doğrulama katmanlarında geliştirmelerdir, bu nedenle yalnızca **Yeni** IoT Hub 'ı için geçerlidir. Henüz mevcut IoT Hub 'ları *için kullanılamaz.* Bu özellikleri önizlemek için, IoT Hub 'ın önizleme modu açık durumdayken oluşturulması gerekir.

## <a name="turn-on-preview-mode-for-a-new-iot-hub"></a>Yeni bir IoT Hub 'ı için Önizleme modunu aç

1. [Azure portalında](https://portal.azure.com) oturum açın.

1. Azure giriş sayfasından **+ kaynak oluştur** düğmesini seçin ve ardından **Market 'te ara** alanına *IoT Hub* girin.

1. Arama sonuçlarından **IoT Hub** seçin ve ardından **Oluştur**' u seçin.

1. **Temel bilgiler** sekmesinde, alanları normalde **bölge** dışında [yaptığınız gibi](iot-hub-create-through-portal.md) doldurun. Şu bölgelerden birini seçin:
    
    - Central US
    - West Europe
    - Güneydoğu Asya

1. **Yönetim** sekmesini seçin ve ardından **Gelişmiş ayarlar** bölümünü genişletin.

1. **Önizleme modu**' nun yanında **Açık**' ı seçin. Uyarı metnini dikkatle gözden geçirin.

    :::image type="content" source="media/iot-hub-preview-mode/turn-on-preview-mode-at-create.png" alt-text="Yeni bir IoT Hub 'ı oluştururken önizleme modu seçeneğinin nerede seçileceğini gösteren resim":::

1. **İleri**' yi seçin ve **ardından Oluştur ' a tıklayın**.

Bir kez oluşturduktan sonra, önizleme modundaki bir IoT Hub her zaman bu başlığı gösterir ve bu IoT Hub 'ını yalnızca önizleme amacıyla kullanmayı bilmenize olanak tanır: 

:::image type="content" source="media/iot-hub-preview-mode/banner.png" alt-text="Önizleme modu IoT Hub için başlık gösteren resim":::

## <a name="using-an-iot-hub-in-preview-mode"></a>Önizleme modunda IoT Hub 'ı kullanma

Üretim için önizleme modunda IoT Hub *'ı kullanmayın.* Önizleme modu *yalnızca* bu sayfanın en üstünde listelenen seçme özelliklerinin önizlemesini amaçlıdır. IoT Hub önizleme modundaki bazı diğer sınırlamalar

- IP filtresi, özel bağlantı, yönetilen kimlik, cihaz akışları ve yük devretme gibi bazı mevcut IoT Hub Özellikler beklenmedik şekilde çalışabilir veya hiç çalışmayabilir.
- Önizleme modundaki bir IoT Hub 'ı değiştirilemez veya normal bir IoT Hub 'ına yükseltilemez.
- Üretime yönelik olmayan normal [IoT Hub SLA 'sını](https://azure.microsoft.com/support/legal/sla/iot-hub/v1_2/) garanti edemeyiz.

> [!TIP]
> [Cihaz akışları](iot-hub-device-streams-overview.md) ve [Dağıtılmış izleme](iot-hub-distributed-tracing.md)için önizleme modu gerekli değildir. Bu eski Önizleme özelliklerini kullanmak için, belgelerini normal olarak takip edin. 

## <a name="next-steps"></a>Sonraki adımlar

- MQTT 5 desteğini önizlemek için bkz. [IoT Hub MQTT 5 desteğine genel bakış (Önizleme)](iot-hub-mqtt-5.md)
- ECC sunucu sertifikasını önizlemek için bkz. [Eliptik Eğri Şifreleme (ECC) sunucusu TLS sertifikası (Önizleme)](iot-hub-tls-support.md#elliptic-curve-cryptography-ecc-server-tls-certificate-preview)
- TLS parçası boyutu anlaşmasını önizlemek için bkz. [en fazla TLS parça uzunluğu anlaşması (Önizleme)](iot-hub-tls-support.md#tls-maximum-fragment-length-negotiation-preview)