---
title: içerme dosyası
description: içerme dosyası
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 09/30/2019
ms.custom: include file
ms.openlocfilehash: 12d4278171d43fdaf8613a1c91bdbffc269adc56
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/04/2019
ms.locfileid: "71949029"
---
@No__t-0 (veya **nesne tanımlayıcı türü**), bir role verilen kimliğin türünü ifade eder. @No__t-0 ve `UserDefinedFunctionId` türlerinden ayrı olarak, nesne tanımlayıcı türleri Azure Active Directory nesnelerinin özelliklerine karşılık gelir.

Aşağıdaki tabloda, Azure dijital TWINS 'de desteklenen nesne tanımlayıcı türleri yer almaktadır:

| Tür | Açıklama |
| --- | --- |
| UserID | Bir kullanıcıya bir rol atar. |
| DeviceID | Cihaza bir rol atar. |
| DomainName | Bir etki alanı adına bir rol atar. Belirtilen etki alanı adına sahip her Kullanıcı ilgili rolün erişim haklarına sahiptir. |
| Değerine | Bir kiracıya bir rol atar. Belirtilen Azure AD kiracı KIMLIĞINE ait olan her Kullanıcı ilgili rolün erişim haklarına sahiptir. |
| Serviceprincipalıd | Hizmet sorumlusu nesne KIMLIĞINE bir rol atar. |
| Userdefinedfunctionıd | Kullanıcı tanımlı bir işleve (UDF) bir rol atar. |