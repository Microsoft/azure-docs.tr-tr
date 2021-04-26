---
title: Azure Percept güvenliğine genel bakış
description: Azure Percept güvenliği hakkında daha fazla bilgi edinin
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: conceptual
ms.date: 03/24/2021
ms.custom: template-concept
ms.openlocfilehash: 93884fb87f87651054ffff0a04c4910de634a5eb
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105567653"
---
# <a name="azure-percept-security-overview"></a>Azure Percept güvenliğine genel bakış

Azure Percept cihazları, bir güven donanım köküyle tasarlanmıştır. Bu yerleşik güvenlik, kamera ve mikrofonlar gibi çıkarım verilerini ve gizlilik açısından duyarlı algılayıcıları korumanıza yardımcı olur ve Azure Percept Studio Hizmetleri için cihaz kimlik doğrulaması ve yetkilendirmesini sağlar.

> [!NOTE]
> Azure Percept DK yalnızca geliştirme ve test ortamlarında kullanılmak üzere lisanslanır.

## <a name="devices"></a>Cihazlar

### <a name="azure-percept-dk"></a>Azure Percept DK

Azure Percept DK, cihazı Azure cihaz sağlama hizmetleri 'ne (DPS) ek güvenlikle bağlamak için kullanılabilecek Güvenilir Platform Modülü (TPM) 2,0 sürümünü içerir. TPM, Trusted Computing Group bir sektör genelinde ISO standardıdır. Tüm TPM 2,0 belirtimi veya ISO/ıEC 11889 belirtimi hakkında daha fazla bilgi için [Trusted Computing Group Web sitesine](https://trustedcomputinggroup.org/resource/tpm-library-specification/) göz atın. DPS cihazları güvenli bir şekilde nasıl sağlayabildiği hakkında daha fazla bilgi için bkz. [Azure IoT Hub cihaz sağlama hizmeti-TPM kanıtlama](../iot-dps/concepts-tpm-attestation.md).

### <a name="azure-percept-system-on-modules-soms"></a>Azure Percept System-on-modüller (SoMs)

Azure Percept Vision System-on-Module (SoM) ve Azure Percept Audio SoM, gömülü AI sensörlerinden erişimi korumak için bir mikro denetleyici birimi (MCU) içerir. Her önyüklemede, MCU bellenimi, cihaz tanımlayıcı bileşim altyapısı (zar) mimarisini kullanarak Azure Percept Studio Hizmetleri ile AI hızlandırıcısının kimliğini doğrular ve yetkilendirir. ZAR, katmanlara önyükleme gerçekleştirerek ve her katman ve yapılandırma için benzersiz cihaz gizli dizileri (UDS) oluşturarak çalışır. Zincirdeki herhangi bir noktada farklı kod veya yapılandırma önyüklenmiyorsa, gizlilikler farklı olur. Zar [çalışma grubu belirtiminde](https://trustedcomputinggroup.org/work-groups/dice-architectures/), zar hakkında daha fazla bilgi edinebilirsiniz. Azure Percept Studio ve gerekli hizmetlere erişimi yapılandırmak için [Azure PERCEPT dk için güvenlik duvarlarını yapılandırma](concept-security-configuration.md)makalesine bakın.

Azure Percept cihazları, bellenimin güvenliğini sağlamak için güvenin donanım kökünü kullanır. Önyükleme ROM 'U, ROM ve işletim sistemi (OS) yükleyicisi arasındaki üretici yazılımının bütünlüğünü sağlar; bu da diğer yazılım bileşenlerinin bütünlüğünü sağlar ve bir güven zinciri oluşturur.

## <a name="services"></a>Hizmetler

### <a name="iot-edge"></a>IoT Edge

Azure Percept DK, Aktarım Katmanı Güvenliği (TLS) protokolü kullanan ek güvenlik ve diğer Azure hizmetleriyle Azure Percept Studio 'ya bağlanır. Azure Percept DK, Azure IoT Edge özellikli bir cihazdır. IoT Edge Runtime, bir cihazı IoT Edge cihazına veren bir programlar koleksiyonudur. Toplu olarak, IoT Edge çalışma zamanı bileşenleri, IoT Edge cihazların kenarda çalışacak kodu almasını ve sonuçları iletmelerini sağlar. Azure Percept DK, ana bilgisayar işletim sisteminden ve kenar özellikli uygulamalardan IoT Edge iş yüklerini yalıtmak için Docker kapsayıcılarını kullanır. Azure IoT Edge güvenlik çerçevesi hakkında daha fazla bilgi için [IoT Edge Güvenlik Yöneticisi](../iot-edge/iot-edge-security-manager.md)hakkında makalesini okuyun.

### <a name="device-update-for-iot-hub"></a>IoT Hub için cihaz güncelleştirmesi

IoT Hub cihaz güncelleştirmesi, Azure Percept cihazlarına yenilenebilir güvenlik sağlayan, daha güvenli, ölçeklenebilir ve güvenilir, kablosuz bir güncelleştirme sağlar. Öngörüler aracılığıyla zengin yönetim denetimleri ve güncelleştirme uyumluluğu sağlar. Azure Percept DK, üretici yazılımından işletim sistemi katmanlarına dayanıklı güncelleştirme (A/B) sağlayan önceden tümleştirilmiş bir cihaz güncelleştirme çözümü içerir.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Güvenlik Duvarı konfigürasyonları ve güvenlik önerileri hakkında daha fazla bilgi edinin](concept-security-configuration.md)

> [!div class="nextstepaction"]
> [Microsoft Online Mağazası 'ndan bir Azure Percept DK satın alma](https://go.microsoft.com/fwlink/p/?LinkId=2155270)
