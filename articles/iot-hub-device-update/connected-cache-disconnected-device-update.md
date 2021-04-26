---
title: Microsoft bağlı önbelleği kullanarak bağlantısı kesilen cihaz güncelleştirmesi desteğini anlayın | Microsoft Docs
titleSuffix: Device Update for Azure IoT Hub
description: Microsoft bağlı önbelleği kullanarak bağlantısı kesilen cihaz güncelleştirmesi desteğini anlama
author: andyriv
ms.author: andyriv
ms.date: 2/16/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: e216d42ff1f279d87e657126514fcfb50960f806
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107811913"
---
# <a name="understand-support-for-disconnected-device-updates"></a>Bağlantısı kesilen cihaz güncelleştirmeleri desteğini anlama

Saydam bir ağ geçidi senaryosunda, bir veya daha fazla cihaz, iletilerini Azure IoT Hub bağlantısını tutan tek bir ağ geçidi cihazından geçirebilir. Bu durumlarda, alt cihazların internet bağlantısı olmayabilir veya internet 'ten içerik indirmesine izin verilmiyor olabilir. Microsoft bağlı önbellek önizleme IoT Edge modülü, Azure IoT Hub müşterilerine yönelik olarak, Linux işletim sistemi tabanlı cihazların ve IoT Edge ağ geçidinin (aşağı akış IoT cihazları) görüntü tabanlı ve paket tabanlı güncelleştirmelerinin yanı sıra Azure IoT Hub müşterilerine yönelik cihaz güncelleştirmesi için bant genişliği tasarrufu sağlar.

## <a name="how-does-microsoft-connected-cache-preview-for-device-update-for-azure-iot-hub-work"></a>Azure IoT Hub yönelik cihaz güncelleştirmesi için Microsoft bağlı önbellek önizlemesi nasıl çalışır?

Microsoft bağlı önbellek önizlemesi, Azure IoT Hub içeriğine yönelik cihaz güncelleştirmesi için yayımlanan içeriğe yönelik akıllı ve saydam bir önbellektir ve paket depoları gibi diğer kaynaklardan gelen içerikleri de önbelleğe almak için özelleştirilebilir. Microsoft bağlı önbelleği, teslim Iyileştirme istemcisi tarafından istenen tam dosya aralıklarına yönelik istemci istekleri tarafından çarpımış ve önceden çekirdek hale getirilmemiş bir soğuk önbellektir. Aşağıdaki diyagramda ve adım adım açıklamasında Microsoft bağlı önbelleğin Azure IoT Hub altyapısı için cihaz güncelleştirmesi içinde nasıl çalıştığı açıklanmaktadır.

>[!Note]
>Bu akışı tanımlarken, IoT Edge ağ geçidinin internet bağlantısı olduğu varsayılır. Aşağı akış IoT Edge ağ geçidi (Iç Içe geçmiş kenar) senaryosunda, "Content Delivery Network" (CDN) üst IoT Edge ağ geçidinde barındırılan MCC olarak düşünülebilir.

  :::image type="content" source="media/connected-cache-overview/disconnected-device-update.png" alt-text="Bağlantısı kesilen cihaz güncelleştirmesi" lightbox="media/connected-cache-overview/disconnected-device-update.png":::

1. Microsoft bağlı önbellek, şirket içi sunucuya IoT Edge modülü olarak dağıtılır.
2. Azure IoT Hub istemcileri için cihaz güncelleştirmesi, IoT yaprak cihazları için cihaz bağlantı dizesinin GatewayHostName özniteliği **veya** IoT Edge alt cihazlarda yapılandırma. toml parent_hostname ayarlanmış olan bir Microsoft bağlı önbelleğinden içerik indirmek üzere yapılandırılmıştır.
3. Her iki durumda da Azure IoT Hub istemcileri için cihaz güncelleştirmesi, Azure IoT Hub hizmeti için cihaz güncelleştirmesinden içerik güncelleştirme indirme komutlarını alır ve CDN yerine Microsoft bağlı önbelleğine güncelleştirme içeriği ister. Varsayılan olarak Microsoft bağlı önbelleği, http bağlantı noktası 80 ' ü dinlemek için yapılandırılmıştır ve dağıtım Iyileştirme istemcisi, bağlantı noktası 80 ' de içerik isteğini Bu bağlantı noktasında dinlemek üzere yapılandırılması gerekir.  Şu anda yalnızca http Protokolü destekleniyor.
4. Microsoft bağlı önbellek sunucusu CDN 'den içerik indirir, yerel önbelleğinde diskte depolanan ve içeriği Azure IoT Hub Client için cihaz güncelleştirmesine dağıtır.
   
>[!Note]
>Paket tabanlı güncelleştirmeler kullanılırken, Microsoft bağlı önbellek sunucusu yönetici tarafından gerekli paket ana bilgisayar adına sahip olacak şekilde yapılandırılır.

5. Aynı güncelleştirme içeriğine yönelik Azure IoT Hub istemcileri için diğer cihaz güncelleştirmesinden sonraki istekler artık önbellekten geliyor ve Microsoft bağlı önbelleği aynı içerik için CDN 'e istek yapmayacak.

### <a name="supporting-industrial-iot-iiot-with-parentchild-hosting-scenarios"></a>Üst/alt barındırma senaryolarıyla endüstriyel IoT (IIoT) destekleme

Bir aşağı akış veya alt IoT Edge ağ geçidi Microsoft bağlı önbellek sunucusunu barındırdığında, Microsoft bağlı önbellek sunucusunu barındıran üst IoT Edge ağ geçidinden güncelleştirme içeriği isteyecek şekilde yapılandırılır. Bu, internet erişimi olan bir Microsoft bağlı önbellek sunucusunu barındıran üst IoT Edge ağ geçidine ulaşmadan önce gereken sayıda düzey için gereklidir. İnternet 'e bağlı sunucudan içerik CDN 'den istenir ve içeriğin ilk olarak içeriği isteyen alt IoT Edge ağ geçidine geri gönderilir. İçerik her düzeyde diskte depolanır.

## <a name="access-to-the-microsoft-connected-cache-preview-for-device-update-for-azure-iot-hub"></a>Azure IoT Hub cihaz güncelleştirmesi için Microsoft bağlı önbellek önizlemesine erişim

Microsoft bağlı önbellek IoT Edge modülü, Azure IoT Hub cihaz güncelleştirmesi 'ni kullanarak çözümler dağıtan müşteriler için önizleme olarak yayımlanmıştır. Önizlemeye erişim davetine göre yapılır. IoT Hut için cihaz güncelleştirmesi için Microsoft bağlı önbellek önizlemesine [erişim isteyin](https://aka.ms/MCCForDeviceUpdateForIoT) ve modüle erişim istiyorsanız istenen bilgileri sağlayın.
