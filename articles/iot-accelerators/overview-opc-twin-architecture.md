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
ms.openlocfilehash: b8d4424c92ff24c36650e34a5d050bdc5f0f9091
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819848"
---
# <a name="opc-twin-architecture"></a>OPC Ikizi mimarisi

Aşağıdaki diyagramlarda OPC Ikizi mimarisi gösterilmektedir.

## <a name="discover-and-activate"></a>Bul ve Etkinleştir

1. İşleci, modülde ağ taramayı sağlar veya bir bulma URL 'SI kullanarak bir kerelik bulma işlemi yapar. Bulunan uç noktalar ve uygulama bilgileri, işleme için ekleme aracısına telemetri aracılığıyla gönderilir.  OPC UA cihaz ekleme Aracısı, bulma veya tarama modundayken OPC Ikizi IoT Edge modülü tarafından gönderilen OPC UA sunucu bulma olaylarını işler. Bulma olayları, uygulama kaydı ve OPC UA cihaz kayıt defterindeki güncelleştirmeler ile sonuçlanır.

   ![OPC Ikizi nasıl çalıştığı](media/overview-opc-twin-architecture/opc-twin1.png)

1. İşleci, bulunan uç noktanın sertifikasını inceler ve erişim için kayıtlı uç nokta ikizi etkinleştirir. 

   ![OPC Ikizi nasıl çalıştığı](media/overview-opc-twin-architecture/opc-twin2.png)

## <a name="browse-and-monitor"></a>Tarama ve izleme

1. İşlem etkinleştirildikten sonra operatör, Ikizi Service REST API kullanarak sunucu bilgi modeli ' ni tarayın veya inceleyebilir, nesne değişkenlerini ve çağrı yöntemlerini okur/yazabilir.  Kullanıcı, HTTP ve JSON içinde tam olarak ifade edilen basitleştirilmiş bir OPC UA API kullanır.

   ![OPC Ikizi nasıl çalıştığı](media/overview-opc-twin-architecture/opc-twin3.png)

1. İkizi hizmet REST arabirimi, OPC yayımcısı 'nda izlenen öğeler ve abonelikler oluşturmak için de kullanılabilir. OPC yayımcısı, telemetri 'e OPC UA sunucu sistemlerinden IoT Hub gönderilmesine olanak tanır. OPC yayımcısı hakkında daha fazla bilgi için bkz. [OPC yayımcısı nedir](overview-opc-publisher.md).

   ![OPC Ikizi nasıl çalıştığı](media/overview-opc-twin-architecture/opc-twin4.png)
