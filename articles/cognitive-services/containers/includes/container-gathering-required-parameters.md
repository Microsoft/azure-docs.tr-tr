---
title: Gerekli parametreler toplanıyor
services: cognitive-services
author: IEvangelist
manager: nitinme
description: Tüm bilişsel hizmetler kapsayıcıları için parametreler
ms.service: cognitive-services
ms.topic: include
ms.date: 10/15/2019
ms.author: dapine
ms.openlocfilehash: bf4e8f06642a4a764794a75e2b49f15958796c61
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73465912"
---
## <a name="gathering-required-parameters"></a>Gerekli parametreler toplanıyor

Gerekli olan tüm bilişsel hizmetler için üç birincil parametre vardır. Son Kullanıcı Lisans Sözleşmesi 'nin (EULA) `accept`bir değerle mevcut olması gerekir. Ayrıca, hem bir uç nokta URL 'SI hem de API anahtarı gereklidir.

### <a name="endpoint-uri-endpoint_uri"></a>Uç nokta URI `{ENDPOINT_URI}`

**Uç nokta** URI değeri, karşılık gelen bilişsel hizmet kaynağının Azure Portal *genel bakış* sayfasında bulunur. *Genel bakış* sayfasına gidin, uç noktanın üzerine gelin ve bir `Copy to clipboard` <span class="docon docon-edit-copy x-hidden-focus"></span> simgesi görüntülenir. Gerektiğinde kopyalayın ve kullanın.

![Uç nokta URI 'sini daha sonra kullanılmak üzere toplayın](../media/overview-endpoint-uri.png)

### <a name="keys-api_key"></a>Anahtarlar `{API_KEY}`

Bu anahtar, kapsayıcıyı başlatmak için kullanılır ve ilgili bilişsel hizmet kaynağının Azure portal tuşları sayfasında kullanılabilir. *Anahtarlar* sayfasına gidin ve `Copy to clipboard` <span class="docon docon-edit-copy x-hidden-focus"></span> simgesine tıklayın.

![Daha sonra kullanmak üzere iki anahtardan birini al](../media/keys-copy-api-key.png)

> [!IMPORTANT]
> Bu abonelik anahtarları bilişsel hizmet API 'nize erişmek için kullanılır. Anahtarlarınızı paylaşmayın. Azure Key Vault kullanarak güvenli bir şekilde depolayın. Ayrıca, bu anahtarların düzenli olarak yeniden oluşturulması önerilir. API çağrısı yapmak için yalnızca bir anahtar gereklidir. İlk anahtarı yeniden oluştururken, hizmete devam eden erişim için ikinci anahtarı kullanabilirsiniz.