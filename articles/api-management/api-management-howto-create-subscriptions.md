---
title: Azure API Management abonelik oluşturma | Microsoft Docs
description: Azure API Management abonelik oluşturmayı öğrenin.
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/14/2018
ms.author: apimpm
ms.openlocfilehash: f8b2238eb0fab9aeeb42d11b4176c0d681b5f8e5
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70073535"
---
# <a name="create-subscriptions-in-azure-api-management"></a>Azure API Management abonelik oluşturma

API 'Leri Azure API Management aracılığıyla yayımladığınızda, abonelik anahtarları kullanılarak bu API 'lere güvenli bir şekilde erişim sağlamak kolay ve yaygındır. Yayımlanan API 'Leri kullanması gereken istemci uygulamalar, bu API 'lere çağrılar yaparken HTTP isteklerinde geçerli bir abonelik anahtarı içermelidir. API 'Lere erişim için bir abonelik anahtarı almak üzere bir abonelik gerekir. Abonelikler hakkında daha fazla bilgi için bkz. [Azure API Management abonelikler](api-management-subscriptions.md).

Bu makalede Azure portal abonelik oluşturma adımları anlatılmaktadır.

## <a name="prerequisites"></a>Önkoşullar

Bu makaledeki adımları gerçekleştirmek için önkoşullar aşağıdaki gibidir:

+ [API Management bir örnek oluşturun](get-started-create-service-instance.md).
+ [API Management abonelikleri](api-management-subscriptions.md)anlayın.

## <a name="create-a-new-subscription"></a>Yeni abonelik oluştur

1. Soldaki menüden **abonelikler** ' i seçin.
2. **Abonelik Ekle**' yi seçin.
3. Aboneliğin adını belirtin ve kapsamı seçin.
4. İsteğe bağlı olarak, aboneliğin bir kullanıcıyla ilişkilendirilmesi gerekip gerekmediğini seçin.
5. **Kaydet**’i seçin.

![Esnek abonelikler](./media/api-management-subscriptions/flexible-subscription.png)

Aboneliği oluşturduktan sonra API 'Lere erişmek için iki API anahtarı sağlanır. Bir anahtar birincildir ve biri ikincildir. 

## <a name="next-steps"></a>Sonraki adımlar
API Management hakkında daha fazla bilgi alın:

+ API Management diğer [kavramları](api-management-terminology.md) öğrenin.
+ API Management hakkında [](import-and-publish.md) daha fazla bilgi edinmek için öğreticilerimizi izleyin.
+ Sık sorulan sorular için [SSS sayfamıza](api-management-faq.md) bakın.