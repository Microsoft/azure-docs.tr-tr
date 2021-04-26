---
title: Azure portalı kullanarak SOAP API’sini içeri aktarma | Microsoft Docs
description: Bir SOAP API 'sinin standart XML gösterimini içeri aktarmayı ve ardından API 'yi Azure ve geliştirici portalları 'nda test etme hakkında bilgi edinin.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 04/22/2020
ms.author: apimpm
ms.openlocfilehash: 81ded79ee72fb7c2d89898595602cb3e6d7ae5e2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96011073"
---
# <a name="import-soap-api"></a>SOAP API’sini içeri aktarma

Bu makalede, bir SOAP API’sinin standart bir XML gösteriminin nasıl içeri aktarılacağı gösterilir. Makalede ayrıca API Management API 'sinin nasıl test yapılacağı gösterilmektedir.

Bu makalede şunları öğreneceksiniz:

> [!div class="checklist"]
> * SOAP API’sini içeri aktarma
> * Azure portalında API’yi test etme
> * Geliştirici portalında API’yi test etme

## <a name="prerequisites"></a>Önkoşullar

Şu hızlı başlangıcı doldurun: [Azure API Management örneği oluşturma](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="import-and-publish-a-back-end-api"></a><a name="create-api"> </a>Arka uç API'sini içeri aktarma ve yayımlama

1. Azure portal API Management hizmetinize gidin ve menüden **API 'leri** seçin.
2. **Yeni API ekleyin** listesinden **WSDL**’yi seçin.

    ![SOAP API’si](./media/import-soap-api/wsdl-api.png)
3. **WSDL belirtimi**’nde, SOAP API’nizin bulunduğu URL'yi girin.
4. **SOAP geçişi** radyo düğmesi, varsayılan olarak seçilir. Bu seçimle API, SOAP olarak sunulur. Tüketicinin SOAP kurallarını kullanması gerekir. API’yi REST’e dönüştürmek istiyorsanız, [SOAP API'yi içeri aktarma ve REST’e dönüştürme](restify-soap-api.md) konusundaki adımları izleyin.

    ![Ekran görüntüsü bir W S D L belirtimi girebileceğiniz W S D L 'tan oluştur iletişim kutusunu gösterir.](./media/import-soap-api/pass-through.png)
5. Sekme tuşuna basın.

    Şu alanlar, SOAP API’sinde bulunan bilgilerle doldurulur: Görünen ad, Ad, Açıklama.
6. API URL'si soneki ekleyin. Sonek, bu belirli API’yi bu API Management örneğinde tanımlayan bir addır. Bu API Management örneğinde benzersiz olmalıdır.
7. API’yi bir ürünle ilişkilendirerek yayımlayın. Bu durumda, "*sınırsız*" ürün kullanılır.  API’nin yayımlanmasını ve geliştiricilerin kullanımına sunulmasını istiyorsanız API’yi bir ürüne ekleyin. Bunu API oluşturması sırasında yapabilir ya da daha sonra ayarlayabilirsiniz.

    Ürünler bir veya daha fazla API arasındaki ilişkilendirmelerdir. Bir dizi API ekleyebilir ve geliştirici portalı aracılığıyla geliştiricilere sunabilirsiniz. Geliştiricilerin bir API’ye erişebilmesi için önce ürüne abone olması gerekir. Abone olduklarında, ilgili üründeki tüm API’ler için geçerli olan bir abonelik anahtarı edinirler. API Management örneğini oluşturduysanız, zaten bir yöneticiyseniz, varsayılan olarak her ürüne abone olursunuz.

    Varsayılan olarak, her bir API Management örneği iki örnek ürün ile birlikte gelir:

    * **Başlangıç**
    * **Sınırsız**   
8. Diğer API ayarlarını girin. Oluşturma sırasında değerleri ayarlayabilir veya daha sonra **Ayarlar** sekmesine giderek yapılandırabilirsiniz. Ayarlar, [Ilk API 'Nizi Içeri aktarma ve yayımlama](import-and-publish.md#import-and-publish-a-backend-api) öğreticisinde açıklanmıştır.
9. **Oluştur**’u seçin.

### <a name="test-the-new-api-in-the-administrative-portal"></a>Yönetim portalında yeni API 'YI test etme

İşlemler doğrudan bir API’nin işlemlerini görüntülemek ve test etmek için kullanışlı bir yol sağlayan yönetim portalından çağrılabilir.  

1. Önceki adımda oluşturduğunuz API’yi seçin.
2. **Test** sekmesine basın.
3. Bir işlem seçin.

    Sayfa, sorgu parametrelerinin ve üst bilgilerin alanlarını görüntüler. Bu API ile ilişkilendirilmiş ürünün abonelik anahtarı için, üst bilgilerden biri "Ocp-Apim-Subscription-Key" üst bilgisidir. API Management örneğini siz oluşturduysanız zaten bir yöneticisinizdir ve anahtar otomatik olarak doldurulur. 
1. **Gönder**’e basın.

    Arka uç, **200 OK** ve bazı verilerle yanıt verir.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Yayımlanan API’yi dönüştürme ve koruma](transform-api.md)