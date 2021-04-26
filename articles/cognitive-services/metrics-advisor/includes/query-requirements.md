---
title: Sorgu gereksinimleri
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: include
ms.date: 09/30/2020
ms.author: mbullwin
ms.openlocfilehash: 777e3261bd6f842861ed6622b78589eedaaf65d7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "92043228"
---
Sorgu içinde, `@StartTime` tek bir zaman damgasına yönelik ölçüm verilerini almak için parametresini kullanın. Ölçüm Danışmanı, `yyyy-MM-ddTHH:mm:ss` sorguyu çalıştırırken parametre bir biçim dizesiyle değiştirilir.

> [!IMPORTANT]
> Sorgu her bir zaman damgasında her bir boyut birleşimi için en çok bir kayıt döndürmelidir. Ve sorgu tarafından döndürülen tüm kayıtlar aynı zaman damgalarına sahip olmalıdır. Ölçüm Danışmanı, verilerinizi almak için bu sorguyu her zaman damgası için çalıştırır. Daha fazla bilgi ve örnek için [sorgular hakkında SSS bölümüne](../faq.md#how-do-i-write-a-valid-query-for-ingesting-my-data) bakın. 