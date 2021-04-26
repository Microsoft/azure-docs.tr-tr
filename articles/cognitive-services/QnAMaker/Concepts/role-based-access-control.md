---
title: Başkalarıyla işbirliği yapma-Soru-Cevap Oluşturma
description: Azure rol tabanlı erişim denetimi kullanarak diğer yazarlarla ve düzenleyicilerle işbirliği yapmayı öğrenin.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 05/15/2020
ms.openlocfilehash: 512ae470d3c9068b9d284095a18013fc84dbf36e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103232554"
---
# <a name="collaborate-with-other-authors-and-editors"></a>Diğer yazarlarla ve düzenleyicilerle işbirliği yapın

Soru-Cevap Oluşturma kaynağına yerleştirilmiş Azure rol tabanlı erişim denetimi (Azure RBAC) kullanarak diğer yazarlarla ve düzenleyicilerle işbirliği yapın.

## <a name="access-is-provided-on-the-qna-maker-resource"></a>Soru-Cevap Oluşturma kaynağında erişim sağlanır

Tüm izinler Soru-Cevap Oluşturma kaynağına yerleştirilmiş izinlerle denetlenir. Bu izinler, okuma, yazma, yayımlama ve tam erişim için hizalanır. Soru-Cevap Oluşturma kaynak için [RBAC erişimini güncelleştirerek](../how-to/manage-qna-maker-app.md) birden çok kullanıcı arasında işbirliğine izin verebilirsiniz.

Bu Azure RBAC özelliği şunları içerir:
* Azure Active Directory (AAD), sahipler ve katkıda bulunanlar için anahtar tabanlı kimlik doğrulama ile %100 geriye dönük olarak uyumludur. Müşteriler, isteklerinde anahtar tabanlı kimlik doğrulaması veya Azure RBAC tabanlı kimlik doğrulaması kullanabilir.
* Denetim, Bilgi Bankası düzeyinde değil kaynak düzeyinde olduğundan, kaynak içindeki tüm bilgi bankalarına yazar ve düzenleyiciler hızlıca ekleyin.

> [!NOTE]
> Kaynak için özel bir alt etki alanı eklediğinizden emin olun. [Özel alt etki alanı](../../cognitive-services-custom-subdomains.md) varsayılan olarak bulunmalı, ancak yoksa, lütfen ekleyin

## <a name="access-is-provided-by-a-defined-role"></a>Erişim, tanımlı bir rol tarafından sağlanır

[!INCLUDE [Azure RBAC permissions table](../includes/role-based-access-control.md)]

## <a name="authentication-flow"></a>Kimlik doğrulaması akışı

Aşağıdaki diyagramda, Soru-Cevap Oluşturma portalında oturum açmak ve yazma API 'Lerini kullanmak için yazarın perspektifinden akış gösterilmektedir.

> [!div class="mx-imgBorder"]
> ![Aşağıdaki diyagramda, Soru-Cevap Oluşturma portalında oturum açmak ve yazma API 'Lerini kullanmak için yazarın perspektifinden akış gösterilmektedir.](../media/qnamaker-how-to-collaborate-knowledge-base/rbac-flow-from-portal-to-service.png)

|Adımlar|Açıklama|
|--|--|
|1|Portal Soru-Cevap Oluşturma kaynak için belirteç alır.|
|2|Portal anahtarlar yerine belirteci geçirerek uygun Soru-Cevap Oluşturma yazma API 'sini (APıM) çağırır.|
|3|Soru-Cevap Oluşturma API'si belirteci doğrular.|
|4 |Soru-Cevap Oluşturma API'si QnAMaker hizmetini çağırır.|

[Yazma API 'lerini](../index.yml)çağırmak istiyorsanız, kimlik doğrulamasının nasıl ayarlanacağı hakkında daha fazla bilgi edinin.

## <a name="authenticate-by-qna-maker-portal"></a>Soru-Cevap Oluşturma portalına göre kimlik doğrulaması

Soru-Cevap Oluşturma portalını kullanarak yazar ve işbirliği yaparsanız, ortak çalışan için kaynağa uygun rolü ekledikten sonra, Soru-Cevap Oluşturma portalı tüm erişim izinlerini yönetir.

## <a name="authenticate-by-qna-maker-apis-and-sdks"></a>Soru-Cevap Oluşturma API 'Ler ve SDK 'Lar tarafından kimlik doğrulama

API 'Leri kullanarak, REST veya SDK 'Lar aracılığıyla yazar ve işbirliği yaparsanız, kimlik doğrulamasını yönetmek için [bir hizmet sorumlusu oluşturmanız](../../authentication.md#assign-a-role-to-a-service-principal) gerekir.

## <a name="next-step"></a>Sonraki adım

* Diller ve istemci uygulamaları için Bilgi Bankası tasarlama
