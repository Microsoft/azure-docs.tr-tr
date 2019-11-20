---
title: include dosyası
description: include dosyası
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 03/05/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 3a5747912a18b8406cabd03c9823e3f6fe6898a6
ms.sourcegitcommit: ec2b75b1fc667c4e893686dbd8e119e7c757333a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72808843"
---
<!-- description of message routing used in the Azure CLI, PowerShell, and RM routing articles.-->

Simülasyon cihazı tarafından iletiye eklenen özellikler temelinde iletileri farklı kaynaklara yönlendireceksiniz. Özel yönlendirmesi olmayan iletiler varsayılan uç noktaya gönderilir (iletiler/olaylar). Sonraki öğreticide, IoT Hub iletileri gönderir ve bunların farklı hedeflere yönlendirildiğini görürsünüz.

|Değer |Sonuç|
|------|------|
|level="storage" |Azure Depolama'ya yazın.|
|level="critical" |Service Bus kuyruğuna yazın. Mantıksal Uygulama iletiyi kuyruktan alır ve Office 365 kullanarak iletiyi e-postayla gönderir.|
|default |Power BI'ı kullanarak bu verileri görüntüleyin.|

İlk adım, verilerin yönlendirileceği uç noktayı kurmak olacaktır. İkinci adım, bu uç noktayı kullanan ileti yolunu ayarlamaya yönelik olur. Yönlendirmeyi ayarladıktan sonra, portalda uç noktaları ve ileti yollarını görüntüleyebilirsiniz.