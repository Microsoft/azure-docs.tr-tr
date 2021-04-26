---
author: baanders
description: Azure dijital TWINS ile bir belirteç çözümünü çapraz kiracı sınırına açıklayan dosyayı ekleyin
ms.service: digital-twins
ms.topic: include
ms.date: 4/13/2021
ms.author: baanders
ms.openlocfilehash: 5bc7e8af356ebe5968fe8a74783edc215788e5b4
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589367"
---
Bunu yapmanın bir yolu, `<home-tenant-ID>` Azure dijital TWINS örneğini Içeren Azure AD kiracının kimliği olan AŞAĞıDAKI CLI komutuna sahiptir:

```azurecli-interactive
az account get-access-token --tenant <home-tenant-ID> --resource https://digitaltwins.azure.net
```

Bu işlemi tamamladıktan sonra kimlik, Azure *https://digitaltwins.azure.net* dijital TWINS örneğine eşleşen bir KIRACı kimliği talebi olan Azure AD kaynağı için verilen bir belirteç alır. Bu belirtecin API isteklerinde veya `Azure.Identity` kodunuzla kullanılması, Federal kimliğin Azure dijital TWINS kaynağına erişmesine izin verebilir.