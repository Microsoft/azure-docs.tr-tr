---
author: baanders
description: Azure dijital TWINS ile çapraz kiracı sınırlamasını açıklayan dosyayı dahil edin
ms.service: digital-twins
ms.topic: include
ms.date: 4/13/2021
ms.author: baanders
ms.openlocfilehash: 16684f8c5947f8b6a09a9a785968dabf3e644c18
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589386"
---
Sonuç olarak, Azure Digital TWINS API 'Lerine yönelik istekler, Azure Digital TWINS örneğinin bulunduğu aynı kiracının parçası olan bir kullanıcı veya hizmet sorumlusu gerektirir. Azure dijital TWINS uç noktalarının kötü taramasını engellemek için, kaynak kiracının dışından erişim belirteçleri olan istekler bir "404 Sub-Domain bulunamadı" hata iletisi döndürür. Bu hata, kullanıcıya veya hizmet sorumlusuna Azure [AD B2B](../articles/active-directory/external-identities/what-is-b2b.md) işbirliği aracılığıyla bir Azure dijital TWINS veri sahibi veya Azure Digital TWINS veri okuyucusu [rolü](../articles/digital-twins/concepts-security.md) verilse *bile* döndürülür. 