---
title: Azure portal ile API Uygulamasını bir API olarak içeri aktarma | Microsoft Docs
description: Bu öğreticide, API Management’ı (APIM) kullanarak API Uygulamasını bir API olarak içeri aktarma gösterilir.
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
ms.openlocfilehash: bf4039b661ac7e0ee9630b86b9e15d1388991431
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74108494"
---
# <a name="import-an-api-app-as-an-api"></a>API Uygulamasını bir API olarak içeri aktarma

Bu makalede API Uygulamasının bir API olarak nasıl içeri aktarılacağı gösterilir. Makale, APIM API’sinin nasıl test edileceğini de göstermektedir.

Bu makalede şunları öğreneceksiniz:

> [!div class="checklist"]
> * API Uygulamasını bir API olarak içeri aktarma
> * Azure portalında API’yi test etme
> * Geliştirici portalında API’yi test etme

## <a name="prerequisites"></a>Önkoşullar

+ Şu hızlı başlangıcı tamamlayın: [Azure API Management örneği oluşturma](get-started-create-service-instance.md)
+ Aboneliğinizde bir API Uygulaması olduğundan emin olun. Daha fazla bilgi için bkz. [App Service belgeleri](https://docs.microsoft.com/azure/app-service/)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-api"> </a>Arka uç API’sini içeri aktarma ve yayımlama

1. **API YÖNETİMİ** bölümünden **API’ler** öğesini seçin.
2. **Yeni API ekleyin** listesinden **API Uygulaması**’nı seçin.

    ![API uygulaması](./media/import-api-app-as-api/api-app.png)
3. Aboneliğinizdeki API Uygulamalarının listesini görmek için **Gözat**’a basın.
4. Uygulamayı seçin. APIM, seçili uygulamayla ilişkili swagger’ı bulur, getirir ve içeri aktarır. 

    APIM’in swagger bulmaması durumunda, API’yi bir “geçiş” API’si olarak sunar. 
5. API URL'si soneki ekleyin. Son ek, bu APIM örneğinde özel olarak bu API’yi tanımlayan bir addır. Son ekin bu APIM örneğinde benzersiz olması gerekir.
6. API’yi bir ürünle ilişkilendirerek yayımlayın. Bu durumda, "*Sınırsız*" ürünü kullanılır.  API’nin yayımlanmasını ve geliştiricilerin kullanımına sunulmasını istiyorsanız API’yi bir ürüne ekleyin. Bunu API oluşturması sırasında yapabilir ya da daha sonra ayarlayabilirsiniz.

    Ürünler bir veya daha fazla API arasındaki ilişkilendirmelerdir. Bir dizi API ekleyebilir ve geliştirici portalı aracılığıyla geliştiricilere sunabilirsiniz. Geliştiricilerin bir API’ye erişebilmesi için önce ürüne abone olması gerekir. Abone olduklarında, ilgili üründeki tüm API’ler için geçerli olan bir abonelik anahtarı edinirler. APIM örneğini siz oluşturduysanız zaten bir yöneticisinizdir ve varsayılan olarak tüm ürünlere abone olmuşsunuz demektir.

    Varsayılan olarak, her bir API Management örneği iki örnek ürün ile birlikte gelir:

    * **Başlangıç**
    * **Sınırsız**   
7. **Oluştur**'u seçin.

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
