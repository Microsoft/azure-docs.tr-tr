---
title: include dosyası
description: include dosyası
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 10/01/2020
ms.author: robb
ms.custom: include file
ms.openlocfilehash: 6f9405e1b402b029b628821824a1727dd825a031
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101734053"
---
| Kaynak | Varsayılan limit | Üst sınır |
| --- | --- | --- |
| Azure uygulama gönderme | eylem grubu başına 10 Azure uygulama eylemi. | Varsayılan ile aynı |
| E-posta | bir eylem grubundaki 1.000 e-posta eylemleri.<br>Bir saatte 100 ' den fazla e-posta yok.<br>Ayrıca bkz. [hız sınırlandırma bilgileri](../articles/azure-monitor/alerts/alerts-rate-limiting.md). | Varsayılan ile aynı |
| ITSM | bir eylem grubunda 10 ıSM eylemi. | Varsayılan ile aynı | 
| Mantıksal uygulama | bir eylem grubunda 10 mantıksal uygulama eylemi. | Varsayılan ile aynı |
| Runbook | bir eylem grubunda 10 runbook eylemi. | Varsayılan ile aynı |
| SMS | bir eylem grubunda 10 SMS eylemi.<br>5 dakikada bir en fazla 1 SMS ileti yok.<br>Ayrıca bkz. [hız sınırlandırma bilgileri](../articles/azure-monitor/alerts/alerts-rate-limiting.md). | Varsayılan ile aynı |
| Ses | bir eylem grubunda 10 sesli eylem.<br>5 dakikada bir en fazla 1 ses çağrısı yoktur.<br>Ayrıca bkz. [hız sınırlandırma bilgileri](../articles/azure-monitor/alerts/alerts-rate-limiting.md). | Varsayılan ile aynı |
| Web Kancası | bir eylem grubunda 10 Web kancası eylemi.  Abonelik başına en fazla Web kancası çağrısı sayısı dakika başına 1500 ' dir. Diğer sınırlar [eyleme özgü bilgiler ile](../articles/azure-monitor/alerts/action-groups.md#action-specific-information)kullanılabilir.  | Varsayılan ile aynı |