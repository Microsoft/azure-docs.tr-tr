---
title: Azure portalını kullanarak SOAP API’sini içeri aktarma ve REST’e dönüştürme | Microsoft Docs
description: API Management ile SOAP API’sini içeri aktarmayı ve REST’e dönüştürmeyi öğrenin.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 11/22/2017
ms.author: apimpm
ms.openlocfilehash: a2bec6401ae3ac53f46e92e9be38abf57a92163d
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74108109"
---
# <a name="import-a-soap-api-and-convert-to-rest"></a>SOAP API'sini içeri aktarma ve REST’e dönüştürme

Bu makalede, bir SOAP API'sinin nasıl içeri aktarılıp REST’e dönüştürüleceği gösterilmektedir. Makale, APIM API’sinin nasıl test edileceğini de göstermektedir.

Bu makalede şunları öğreneceksiniz:

> [!div class="checklist"]
> * SOAP API'sini içeri aktarma ve REST’e dönüştürme
> * Azure portalında API’yi test etme
> * Geliştirici portalında API’yi test etme

## <a name="prerequisites"></a>Önkoşullar

Şu hızlı başlangıcı tamamlayın: [Azure API Management örneği oluşturma](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-api"> </a>Arka uç API’sini içeri aktarma ve yayımlama

1. **API YÖNETİMİ** bölümünden **API’ler** öğesini seçin.
2. **Yeni API ekleyin** listesinden **WSDL**’yi seçin.

    ![SOAP API’si](./media/restify-soap-api/wsdl-api.png)
3. **WSDL belirtimi**’nde, SOAP API’nizin bulunduğu URL'yi girin.
4. **SOAP’den REST’e** radyo düğmesine tıklayın. Bu seçenek tıklandığında APIM, XML ve JSON arasında bir otomatik dönüştürme yapmaya çalışır. Bu durumda tüketiciler API’yi, JSON döndüren bir RESTful API’si olarak çağırmalıdır. APIM her isteği bir SOAP çağrısına dönüştürür.

    ![SOAP'den REST'e](./media/restify-soap-api/soap-to-rest.png)

5. Sekme tuşuna basın.

    Şu alanlar, SOAP API’sinde bulunan bilgilerle doldurulur: Görünen ad, Ad, Açıklama.
6. API URL'si soneki ekleyin. Son ek, bu APIM örneğinde özel olarak bu API’yi tanımlayan bir addır. Son ekin bu APIM örneğinde benzersiz olması gerekir.
9. API’yi bir ürünle ilişkilendirerek yayımlayın. Bu durumda, "*Sınırsız*" ürünü kullanılır.  API’nin yayımlanmasını ve geliştiricilerin kullanımına sunulmasını istiyorsanız API’yi bir ürüne ekleyin. Bunu API oluşturması sırasında yapabilir ya da daha sonra ayarlayabilirsiniz.

    Ürünler bir veya daha fazla API arasındaki ilişkilendirmelerdir. Bir dizi API ekleyebilir ve geliştirici portalı aracılığıyla geliştiricilere sunabilirsiniz. Geliştiricilerin bir API’ye erişebilmesi için önce ürüne abone olması gerekir. Abone olduklarında, ilgili üründeki tüm API’ler için geçerli olan bir abonelik anahtarı edinirler. APIM örneğini siz oluşturduysanız zaten bir yöneticisinizdir ve varsayılan olarak tüm ürünlere abone olmuşsunuz demektir.

    Varsayılan olarak, her bir API Management örneği iki örnek ürün ile birlikte gelir:

    * **Başlangıç**
    * **Sınırsız**   
10. **Oluştur**'u seçin.

## <a name="test-the-new-api-in-the-azure-portal"></a>Azure portal yeni API 'YI test etme

İşlemler doğrudan bir API’nin işlemlerini görüntülemek ve test etmek için kullanışlı bir yol sağlayan Azure portalından çağrılabilir.  

1. Önceki adımda oluşturduğunuz API’yi seçin.
2. **Test** sekmesine basın.
3. Bir işlem seçin.

    Sayfa, sorgu parametrelerinin ve üst bilgilerin alanlarını görüntüler. Bu API ile ilişkilendirilmiş ürünün abonelik anahtarı için, üst bilgilerden biri "Ocp-Apim-Subscription-Key" üst bilgisidir. APIM örneğini siz oluşturduysanız zaten bir yöneticisinizdir ve anahtar otomatik olarak doldurulur. 
1. **Gönder**’e basın.

    Arka uç, **200 OK** ve bazı verilerle yanıt verir.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Yayımlanan API’yi dönüştürme ve koruma](transform-api.md)