---
title: Uzaktan Izleme çözümü Edge cihazı ekleme-Azure | Microsoft Docs
description: Bu makalede, bir IoT Edge cihazının uzaktan Izleme çözüm hızlandırıcısına nasıl ekleneceği açıklanmaktadır
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/09/2018
ms.topic: conceptual
ms.openlocfilehash: 0a42763ff47cccfa506acbbbd95d20d41eb0827f
ms.sourcegitcommit: c4700ac4ddbb0ecc2f10a6119a4631b13c6f946a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/27/2019
ms.locfileid: "72965384"
---
# <a name="add-an-iot-edge-device-to-your-remote-monitoring-solution-accelerator"></a>Uzaktan Izleme çözüm hızlandırıcısına IoT Edge cihaz ekleme

Çözüm hızlandırıcısına [IoT Edge](../iot-edge/about-iot-edge.md) bir cihaz eklemek için aşağıdaki iki adımı izleyin:

1. Edge cihazını, uzaktan Izleme çözümü Hızlandırıcısı Web Kullanıcı arabirimindeki **Device Explorer** sayfasına ekleyin.
1. IoT Edge çalışma zamanını Edge cihazınıza yükler.

## <a name="add-the-iot-edge-device"></a>IoT Edge cihazı ekleme

Uzaktan Izleme çözüm hızlandırıcısına IoT Edge bir cihaz eklemek için Web Kullanıcı arabirimindeki **Device Explorer** sayfasına gidin ve **+ yeni cihaz**' a tıklayın.

**Yeni cihaz** panelinde **IoT Edge cihaz**' ı seçin. Diğer ayarlar için varsayılan değerleri bırakabilirsiniz. Ardından **Apply** (Uygula) öğesine tıklayın:

![IoT Edge cihaz ekle](media/iot-accelerators-remote-monitoring-add-edge-device/addedgedevice.png)

### <a name="alternative-ways-to-add-an-iot-edge-device"></a>IoT Edge cihaz eklemenin alternatif yolları

Çözüm hızlandırıcısında bir IoT Edge cihazını doğrudan IoT Hub örneğiyle kaydetmek de mümkündür. Bu nasıl yapılır kılavuzlarından herhangi birini izlemeden önce çözüm hızlandırıcısında IoT Hub 'ın adını bilmeniz gerekir:

- [Azure portal yeni bir Azure IoT Edge cihazı kaydetme](../iot-edge/how-to-register-device.md#register-in-the-azure-portal)
- [Azure CLı ile yeni bir Azure IoT Edge cihaz kaydetme](../iot-edge/how-to-register-device.md#register-with-the-azure-cli)
- [Visual Studio Code 'tan yeni bir Azure IoT Edge cihazı kaydetme](../iot-edge/how-to-register-device.md#register-with-visual-studio-code)

Uzaktan Izleme çözüm hızlandırıcısında bir cihazı doğrudan IoT Hub ile kaydettiğinizde, Web Kullanıcı arabirimindeki **Device Explorer** sayfasında listelenir.

## <a name="install-the-iot-edge-runtime"></a>IoT Edge çalışma zamanını yükler

Sınır cihazınıza modül dağıtabilmeniz için önce gerçek cihaza IoT Edge çalışma zamanını yüklemelisiniz. Aşağıdaki nasıl yapılır kılavuzlarında, çalışma zamanının ortak cihaz platformları üzerine nasıl yükleneceği gösterilmektedir:

- [Linux (x64) üzerinde Azure IoT Edge çalışma zamanını yükler](../iot-edge/how-to-install-iot-edge-linux.md)
- [Linux 'ta Azure IoT Edge çalışma zamanı 'nı (ARM32v7/armhf) yükler](../iot-edge/how-to-install-iot-edge-linux-arm.md)
- [Windows kapsayıcıları ile kullanmak için Windows üzerinde Azure IoT Edge çalışma zamanı 'nı yüklemek](../iot-edge/how-to-install-iot-edge-windows-with-windows.md)
- [Linux kapsayıcılarıyla kullanmak için Windows 'a Azure IoT Edge çalışma zamanını yükler](../iot-edge/how-to-install-iot-edge-windows-with-linux.md)
- [Windows IoT Core üzerinde IoT Edge çalışma zamanını yükler](../iot-edge/how-to-install-iot-core.md)

## <a name="next-steps"></a>Sonraki adımlar

IoT Edge cihazınızı hazırladığınıza göre, bir sonraki adım buna modüller dağıtmaktır. Bkz. [IoT Edge paketini uzaktan izleme çözüm hızlandırıcısına alma](iot-accelerators-remote-monitoring-import-edge-package.md)
