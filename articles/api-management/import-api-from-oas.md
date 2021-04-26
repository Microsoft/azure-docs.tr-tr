---
title: Azure portalını kullanarak OpenAPI belirtimini içeri aktarma | Microsoft Docs
description: API Management ile bir Openapı belirtimini içeri aktarmayı öğrenin ve ardından API 'yi Azure ve geliştirici portalları ' nda test edin.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 04/20/2020
ms.author: apimpm
ms.openlocfilehash: 05295efec3d7651c3a77dd5ad6e2c72b57a6c5a3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95994732"
---
# <a name="import-an-openapi-specification"></a>OpenAPI belirtimini içeri aktarma

Bu makalede, https://conferenceapi.azurewebsites.net?format=json konumunda bulunan bir "OpenAPI belirtimi" arka uç API’sinin nasıl içeri aktarılacağı gösterilmektedir. Bu arka uç API’si, Microsoft tarafından sağlanır ve Azure’da barındırılır. Makale, APIM API’sinin nasıl test edileceğini de göstermektedir.

Bu makalede şunları öğreneceksiniz:

> [!div class="checklist"]
> * "OpenAPI belirtimi" arka uç API’sini içeri aktarma
> * Azure portalında API’yi test etme
> * Geliştirici portalında API’yi test etme

## <a name="prerequisites"></a>Önkoşullar

Şu hızlı başlangıcı doldurun: [Azure API Management örneği oluşturma](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="import-and-publish-a-back-end-api"></a><a name="create-api"> </a>Arka uç API'sini içeri aktarma ve yayımlama

1. Azure portal API Management hizmetinize gidin ve menüden **API 'leri** seçin.
2. **Yeni API ekle** listesinden **OpenAPI belirtimi**’ni seçin.

    ![Openapı belirtimi](./media/import-api-from-oas/oas-api.png)
3. API ayarlarını girin. Oluşturma sırasında değerleri ayarlayabilir veya daha sonra **Ayarlar** sekmesine giderek yapılandırabilirsiniz. Ayarlar, [Ilk API 'Nizi Içeri aktarma ve yayımlama](import-and-publish.md#import-and-publish-a-backend-api) öğreticisinde açıklanmıştır.
4. **Oluştur**’u seçin.

> [!NOTE]
> API içeri aktarma sınırlamaları, [başka bir makalede](api-management-api-import-restrictions.md)belgelenmiştir.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Yayımlanan API’yi dönüştürme ve koruma](transform-api.md)
