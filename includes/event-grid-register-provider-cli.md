---
title: include dosyası
description: include dosyası
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 08/17/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: cd778da5fd53cb8744a9f267384fcc8e11941582
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105645451"
---
## <a name="enable-the-event-grid-resource-provider"></a>Event Grid Kaynak sağlayıcısını etkinleştirin

Daha önce Azure aboneliğinizde Event Grid kullanmadıysanız, Event Grid Kaynak sağlayıcısını kaydetmeniz gerekebilir. Sağlayıcıyı kaydetmek için şu komutu çalıştırın:

```azurecli-interactive
az provider register --namespace Microsoft.EventGrid
```

Kaydın tamamlanması biraz zaman alabilir. Durumu denetlemek için şunu çalıştırın:

```azurecli-interactive
az provider show --namespace Microsoft.EventGrid --query "registrationState"
```

`registrationState``Registered` olduğu zaman devam edebilirsiniz.
