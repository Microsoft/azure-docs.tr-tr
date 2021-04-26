---
title: Taban çizgisi ve özel denetimler
description: IoT temeli için Azure Defender kavramı hakkında bilgi edinin.
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: 1b8b9d62918e40262da6b3df48d0fece842e050f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104779367"
---
# <a name="azure-defender-for-iot-baseline-and-custom-checks"></a>IoT için Azure Defender temel ve özel denetimler

Bu makalede IoT taban çizgisi için Defender açıklanmakta ve temel özel denetimlerin tüm ilişkili özellikleri özetlenmektedir.

## <a name="baseline"></a>Taban çizgisi

Taban çizgisi her cihaz için standart davranış oluşturur ve beklenen Norms 'den olağan dışı davranış veya sapma oluşturmayı kolaylaştırır.

## <a name="baseline-custom-checks"></a>Taban çizgisi özel denetimleri

Taban çizgisi özel denetimleri, cihazın **modül kimliği ikizi** kullanarak her bir cihaz temeli için özel bir denetim listesi kurar.

## <a name="setting-baseline-properties"></a>Taban çizgisi özelliklerini ayarlama

1. IoT Hub değiştirmek istediğiniz cihazı bulun ve seçin.

1. Cihaza tıklayın ve sonra **azureiotsecurity** modülüne tıklayın.

1. **Modül kimliği ikizi**' na tıklayın.

1. Ana **hat özel denetim** dosyasını cihaza yükleyin.

1. Defender-IoT-mikro-Agent öğesine temel özellikler ekleyin ve **Kaydet**' e tıklayın.

### <a name="baseline-custom-check-file-example"></a>Temel özel denetim dosyası örneği

Temel özel denetimleri yapılandırmak için:

   ```json
    "desired": {
      "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration": {
        "baselineCustomChecksEnabled": {
          "value" : true
        },
        "baselineCustomChecksFilePath": {
          "value" : "/home/user/full_path.xml"
        },
        "baselineCustomChecksFileHash": {
          "value" : "#hashexample!"
        }
      }
    },
   ```

## <a name="baseline-custom-check-properties"></a>Taban çizgisi özel denetim özellikleri

| Name| Durum | Geçerli değerler| Varsayılan değerler| Description |
|------|-----|------|-----|-----|
|baselineCustomChecksEnabled|Gerekli: true |Geçerli değerler: **Boolean** |Varsayılan değer: **false** |Yüksek öncelikli iletiler gönderilmeden önce en uzun zaman aralığı.|
|baselineCustomChecksFilePath |Gerekli: true|Geçerli değerler: **String**, **null** |Varsayılan değer: **null** |Taban çizgisi XML yapılandırmasının tam yolu|
|baselineCustomChecksFileHash |Gerekli: true|Geçerli değerler: **String**, **null** |Varsayılan değer: **null** |`sha256sum` XML yapılandırma dosyası. Ek bilgi için [SHA256sum başvurusunu](https://linux.die.net/man/1/sha256sum) kullanın. |

Ek temel örnekleri gözden geçirmek için bkz. [özel taban çizgisi örneği-1](https://ascforiot.blob.core.windows.net/public/custom_baseline_example_hyperv_ubuntu1804.xml) ve [özel taban çizgisi örneği-2](https://ascforiot.blob.core.windows.net/public/oms_audits.xml).

## <a name="next-steps"></a>Sonraki adımlar

- [Ham güvenlik verilerinize](how-to-security-data-access.md) erişin
- [Cihazı araştırma](how-to-investigate-device.md)
- [Güvenlik önerilerini](concept-recommendations.md) anlayın ve araştırın
- [Güvenlik uyarılarını](concept-security-alerts.md) anlama ve araştırma
