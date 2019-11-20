---
title: Azure portalını kullanarak OpenAPI belirtimini içeri aktarma | Microsoft Docs
description: API Management ile OpenAPI belirtimini içeri aktarmayı öğrenin.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 07/12/2019
ms.author: apimpm
ms.openlocfilehash: 2b5bcd0d3bba914b81e305c88a512645c1a1c258
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74108516"
---
# <a name="import-an-openapi-specification"></a>OpenAPI belirtimini içeri aktarma

Bu makalede, https://conferenceapi.azurewebsites.net?format=json konumunda bulunan bir "OpenAPI belirtimi" arka uç API’sinin nasıl içeri aktarılacağı gösterilmektedir. Bu arka uç API’si, Microsoft tarafından sağlanır ve Azure’da barındırılır. Makale, APIM API’sinin nasıl test edileceğini de göstermektedir.

> [!IMPORTANT]
> OpenAPI içeri aktarma ile ilgili önemli bilgiler ve ipuçları için bu [belgeye](https://blogs.msdn.microsoft.com/apimanagement/2018/04/11/important-changes-to-openapi-import-and-export/) bakın.

Bu makalede şunları öğreneceksiniz:

> [!div class="checklist"]
> * "OpenAPI belirtimi" arka uç API’sini içeri aktarma
> * Azure portalında API’yi test etme
> * Geliştirici portalında API’yi test etme

## <a name="prerequisites"></a>Önkoşullar

Şu hızlı başlangıcı tamamlayın: [Azure API Management örneği oluşturma](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-api"> </a>Arka uç API’sini içeri aktarma ve yayımlama

1. **API YÖNETİMİ** bölümünden **API’ler** öğesini seçin.
2. **Yeni API ekle** listesinden **OpenAPI belirtimi**’ni seçin.

    ![Openapı belirtimi](./media/import-api-from-oas/oas-api.png)
3. Uygun ayarları girin. Oluşturma sırasında tüm API değerlerini ayarlayabilirsiniz. Alternatif olarak, **Ayarlar** sekmesine giderek bunların bazılarını daha sonra ayarlayabilirsiniz. <br/> **Sekme** tuşuna basarsanız, alanların bazıları (veya tümü), belirtilen arka uç hizmetindeki bilgilerle doldurulur.

    ![Bir API oluşturma](./media/api-management-get-started/create-api.png)

    |Ayar|Değer|Açıklama|
    |---|---|---|
    |**OpenAPI Belirtimi**|https://conferenceapi.azurewebsites.net?format=json|API’yi uygulayan hizmete başvurur. API Management istekleri bu adrese iletir.|
    |**Görünen ad**|*Tanıtım Konferansı API’si*|Hizmet URL’sini girdikten sonra sekme tuşuna basarsanız APIM, json'da ne olduğuna bağlı olarak bu alanı doldurur. <br/>Bu ad, Geliştirici portalında görüntülenir.|
    |**Adı**|*demo-conference-api*|API için benzersiz bir ad sağlar. <br/>Hizmet URL’sini girdikten sonra sekme tuşuna basarsanız APIM, json'da ne olduğuna bağlı olarak bu alanı doldurur.|
    |**Açıklama**|API için isteğe bağlı bir açıklama sağlayın.|Hizmet URL’sini girdikten sonra sekme tuşuna basarsanız APIM, json'da ne olduğuna bağlı olarak bu alanı doldurur.|
    |**API URL’si soneki**|*conference*|Sonek, API yönetimi hizmetinin temel URL’sinin sonuna eklenir. API Management API'leri soneklerine bakarak ayırt eder; dolayısıyla belirli bir yayımcıdaki her API için sonekin benzersiz olması gerekir.|
    |**URL düzeni**|*HTTPS*|API’ye erişmek için hangi protokollerin kullanılabileceğini belirler. |
    |**Ürünler**|*Sınırsız*| API’yi bir ürünle ilişkilendirerek yayımlayın. İsteğe bağlı olarak bu yeni API’yi bir ürüne ekleyin, ürün adını yazın. Bu adım, API’yi birden fazla ürüne eklemek için birçok defa yinelenebilir.<br/>Ürünler bir veya daha fazla API arasındaki ilişkilendirmelerdir. Bir dizi API ekleyebilir ve geliştirici portalı aracılığıyla geliştiricilere sunabilirsiniz. Geliştiricilerin bir API’ye erişebilmesi için önce ürüne abone olması gerekir. Abone olduklarında, ilgili üründeki tüm API’ler için geçerli olan bir abonelik anahtarı edinirler. APIM örneğini siz oluşturduysanız zaten bir yöneticisinizdir ve varsayılan olarak tüm ürünlere abone olmuşsunuz demektir.<br/> Varsayılan olarak, her bir API Management örneği iki örnek ürün ile birlikte gelir: **Başlangıç** ve **Sınırsız**. |

4. **Oluştur**'u seçin.

> [!NOTE]
> API içeri aktarma sınırlamaları, [başka bir makalede](api-management-api-import-restrictions.md)belgelenmiştir.

## <a name="test-the-new-api-in-the-azure-portal"></a>Azure portal yeni API 'YI test etme

![API haritasını test etme](./media/api-management-get-started/01-import-first-api-01.png)

İşlemler doğrudan bir API’nin işlemlerini görüntülemek ve test etmek için kullanışlı bir yol sağlayan Azure portalından çağrılabilir.

1. Önceki adımda oluşturduğunuz API’yi seçin (**API’ler** sekmesinden).
2. **Test** sekmesine basın.
3. **GetSpeakers** seçeneğine tıklayın. Sayfa, sorgu parametreleri alanlarını ve üst bilgileri görüntüler, ancak bu durumda bir alan yok. Bu API ile ilişkilendirilmiş ürünün abonelik anahtarı için, üst bilgilerden biri "Ocp-Apim-Subscription-Key" üst bilgisidir. Anahtar otomatik olarak doldurulur.
4. **Gönder**’e basın.

    Arka uç, **200 OK** ve bazı verilerle yanıt verir.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Yayımlanan API’yi dönüştürme ve koruma](transform-api.md)
