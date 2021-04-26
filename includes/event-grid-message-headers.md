---
title: include dosyası
description: include dosyası
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 11/19/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: c37e2c28eb6aee9edf4bdd97066ce5f15e7447c1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96005648"
---
## <a name="message-headers"></a>İleti üstbilgileri
İleti üstbilgilerinde aldığınız özellikler şunlardır:

| Özellik adı | Description |
| ------------- | ----------- | 
| AEG-abonelik-adı | Olay aboneliğinin adı. |
| AEG-Delivery-Count | Olay için yapılan deneme sayısı. |
| AEG-Event-Type | <p>Etkinliğin türü.</p><p>Aşağıdaki değerlerden biri olabilir:</p><ul><li>SubscriptionValidation</li><li>Bildirim</li><li>Subscriptionsilinmeye</li></ul> | 
| AEG-Metadata-Version | <p>Etkinliğin meta veri sürümü.<p> **Event Grid olay şeması** için, bu özellik meta veri sürümünü ve **bulut olay şeması** için, **Belirtim sürümünü** temsil eder. </p>|
| AEG-veri sürümü | <p>Etkinliğin veri sürümü.</p><p>**Event Grid olay şeması** için, bu özellik veri sürümünü ve **bulut olay şeması** için, uygulanmaz.</p> |
| AEG-çıkış-olay kimliği | Event Grid olayının KIMLIĞI. |


