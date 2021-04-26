---
title: include dosyası
description: include dosyası
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 07/05/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: a2f5264db1f95bcea524a87a61735cf730af23ba
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105645397"
---
## <a name="enable-event-grid-resource-provider"></a>Event Grid kaynak sağlayıcısını etkinleştirme

Azure aboneliğinizde daha önce Event Grid kullanmadıysanız Event Grid kaynak sağlayıcısına kaydolmanız gerekebilir. Şu komutu çalıştırın:

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.EventGrid
```

Kayıt işleminin tamamlanması biraz sürebilir. Durumu denetlemek için şunu çalıştırın:

```azurepowershell-interactive
Get-AzResourceProvider -ProviderNamespace Microsoft.EventGrid
```

`RegistrationStatus``Registered` olduğu zaman devam edebilirsiniz.
