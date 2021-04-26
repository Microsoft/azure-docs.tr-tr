---
title: OPC Ikizi mimarisi-Azure | Microsoft Docs
description: Bu makalede OPC Ikizi mimarisine genel bakış sunulmaktadır. Sunucu bulma, etkinleştirme, göz atma ve izleme hakkında bilgi açıklar.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 602d2f77564709294d28b797ff3f03f12b3b32d8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105646986"
---
# <a name="opc-twin-architecture"></a>OPC Ikizi mimarisi

> [!IMPORTANT]
> Bu makaleyi güncelleştirdiğimiz sürece, en güncel içerik için bkz. [Azure endüstriyel IoT](https://azure.github.io/Industrial-IoT/) .

Aşağıdaki diyagramlarda OPC Ikizi mimarisi gösterilmektedir.

## <a name="discover-and-activate"></a>Bul ve Etkinleştir

1. İşleci, modülde ağ taramayı sağlar veya bir bulma URL 'SI kullanarak bir kerelik bulma işlemi yapar. Bulunan uç noktalar ve uygulama bilgileri, işleme için ekleme aracısına telemetri aracılığıyla gönderilir.  OPC UA cihaz ekleme Aracısı, bulma veya tarama modundayken OPC Ikizi IoT Edge modülü tarafından gönderilen OPC UA sunucu bulma olaylarını işler. Bulma olayları, uygulama kaydı ve OPC UA cihaz kayıt defterindeki güncelleştirmeler ile sonuçlanır.

   ![Bulma veya tarama modundaki OPC Ikizi IoT Edge modülü ile OPC Ikizi mimarisini gösteren diyagram.](media/overview-opc-twin-architecture/opc-twin1.png)

1. İşleci, bulunan uç noktanın sertifikasını inceler ve erişim için kayıtlı uç nokta ikizi etkinleştirir. 

   ![IoT Edge "Ikizi Identity" olan OPC Ikizi mimarisini gösteren diyagram.](media/overview-opc-twin-architecture/opc-twin2.png)

## <a name="browse-and-monitor"></a>Tarama ve izleme

1. İşlem etkinleştirildikten sonra operatör, Ikizi Service REST API kullanarak sunucu bilgi modeli ' ni tarayın veya inceleyebilir, nesne değişkenlerini ve çağrı yöntemlerini okur/yazabilir.  Kullanıcı, HTTP ve JSON içinde tam olarak ifade edilen basitleştirilmiş bir OPC UA API kullanır.

   ![Sunucu bilgi modelini göz atmak ve incelemek için OPC Ikizi mimari kurulumunu gösteren diyagram.](media/overview-opc-twin-architecture/opc-twin3.png)

1. İkizi hizmet REST arabirimi, OPC yayımcısı 'nda izlenen öğeler ve abonelikler oluşturmak için de kullanılabilir. OPC yayımcısı, telemetri 'e OPC UA sunucu sistemlerinden IoT Hub gönderilmesine olanak tanır. OPC yayımcısı hakkında daha fazla bilgi için bkz. [OPC yayımcısı nedir](overview-opc-publisher.md).

   ![OPC Ikizi nasıl çalıştığı](media/overview-opc-twin-architecture/opc-twin4.png)
