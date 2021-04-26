---
title: Defender-IoT-mikro-aracı Azure RTOS API 'SI
description: Azure RTOS için Defender-IoT-Micro-Agent için başvuru API 'SI.
ms.topic: reference
ms.date: 09/07/2020
ms.author: mlottner
ms.openlocfilehash: e7000a7e6d8ba332432f1ececa12bd9543e9e4a7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104779401"
---
# <a name="defender-iot-micro-agent-for-azure-rtos-api-preview"></a>Defender-IoT-mikro-aracı Azure RTOS API (Önizleme)

Bu API yalnızca Azure RTOS için Defender-IoT-mikro-Agent ile kullanılmaya yöneliktir. Ek kaynaklar için bkz. [Azure RTOS GitHub kaynağı Için Defender-IoT-Micro-Agent](https://github.com/azure-rtos/azure-iot-preview/releases). 

## <a name="enable-defender-iot-micro-agent-for-azure-rtos"></a>Azure RTOS için Defender-IoT-Micro-Agent ' ı etkinleştirme

**nx_azure_iot_security_module_enable**

### <a name="prototype"></a>Prototype

```c
UINT nx_azure_iot_security_module_enable(NX_AZURE_IOT *nx_azure_iot_ptr);
```

### <a name="description"></a>Description

Bu yordam, Azure IoT Defender-IoT-mikro-Agent alt sistemini sunar. Bir iç durum makinesi güvenlik olaylarının toplanmasını yönetir ve bunları Azure IoT Hub gönderir. Veri toplamayı yönetmek için yalnızca bir NX_AZURE_IOT_SECURITY_MODULE örneği gerekir ve gereklidir.

### <a name="parameters"></a>Parametreler

| Ad | Açıklama |
|---------|---------|
| nx_azure_iot_ptr [in]    | Bir işaretçisi `NX_AZURE_IOT` .  |

### <a name="return-values"></a>Dönüş değerleri

|Dönüş değerleri  |Description |
|---------|---------|
|NX_AZURE_IOT_SUCCESS|   Azure IoT güvenlik modülü başarıyla etkinleştirildi.     |
|NX_AZURE_IOT_FAILURE   |  Azure IoT güvenlik modülü bir iç hata nedeniyle etkinleştirilemedi.    |
|NX_AZURE_IOT_INVALID_PARAMETER   |  Güvenlik modülü geçerli bir #NX_AZURE_IOT örneği gerektirir.      |

### <a name="allowed-from"></a>İzin verilen

İş Parçacıkları

## <a name="disable-azure-iot-defender-iot-micro-agent"></a>Azure IoT Defender-IoT-mikro-Agent ' ı devre dışı bırakma

**nx_azure_iot_security_module_disable**


### <a name="prototype"></a>Prototype

```c
UINT nx_azure_iot_security_module_disable(NX_AZURE_IOT *nx_azure_iot_ptr);
```

### <a name="description"></a>Description

Bu yordam, Azure IoT Defender-IoT-mikro-Agent alt sistemini devre dışı bırakır.

### <a name="parameters"></a>Parametreler

| Ad | Açıklama |
|---------|---------|
| nx_azure_iot_ptr [in]    | Bir işaretçisi `NX_AZURE_IOT` . NULL ise singleton örneği devre dışıdır. |

### <a name="return-values"></a>Dönüş değerleri

|Dönüş değerleri  |Description |
|---------|---------|
|NX_AZURE_IOT_SUCCESS     |   Azure IoT güvenlik modülü başarıyla devre dışı bırakıldığında başarılı olur.      |
|NX_AZURE_IOT_INVALID_PARAMETER   |  Azure IoT Hub örneği, Singleton bileşik örneğinden farklı.       |
|NX_AZURE_IOT_FAILURE    |  Azure IoT güvenlik modülü bir iç hata nedeniyle devre dışı bırakılamadı.       |

### <a name="allowed-from"></a>İzin verilen

İş Parçacıkları


## <a name="next-steps"></a>Sonraki adımlar

Azure RTOS Defender-IoT-mikro-Agent ile çalışmaya başlama hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:

- IoT RTOS Defender-IoT-Micro-Agent [genel bakış](iot-security-azure-rtos.md)Için Defender 'ı inceleyin.
