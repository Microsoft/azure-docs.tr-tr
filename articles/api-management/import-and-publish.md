---
title: Azure API Yönetimi’nde ilk API’nizi içeri aktarma ve yayımlama | Microsoft Docs
description: API Yönetimi ile ilk API’nizin nasıl içeri aktarılacağını ve yayımlanacağını öğrenin.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 02/24/2019
ms.author: apimpm
ms.openlocfilehash: bae762b4603b2f5f80447a16671fed4e37e62b95
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74108545"
---
# <a name="import-and-publish-your-first-api"></a>İlk API’nizi içeri aktarma ve yayımlama 

Bu öğreticide, https://conferenceapi.azurewebsites.net?format=json konumunda bulunan bir "OpenAPI belirtimi" arka uç API’sinin nasıl içeri aktarılacağı gösterilmektedir. Bu arka uç API’si, Microsoft tarafından sağlanır ve Azure’da barındırılır. 

Arka uç API’si, API Yönetimi’ne (APIM) içeri aktarıldıktan sonra APIM API’si, arka uç API’si için bir cephe olur. Arka uç API'sini içeri aktardığınızda hem kaynak API’si hem de APIM API’si aynı olur. APIM, arka uç API’sine dokunmadan ihtiyaçlarınıza göre cepheyi özelleştirmenize olanak sağlar. Daha fazla bilgi için bkz. [API’nizi dönüştürme ve koruma](transform-api.md). 

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * İlk API’nizi içeri aktarma
> * Azure portalında API’yi test etme
> * Geliştirici portalında API’yi test etme

![Yeni API](./media/api-management-get-started/created-api.png)

## <a name="prerequisites"></a>Önkoşullar

+ [Azure API Management terminolojisini](api-management-terminology.md) öğrenin.
+ Şu hızlı başlangıcı tamamlayın: [Azure API Management örneği oluşturma](get-started-create-service-instance.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-api"> </a>Arka uç API’sini içeri aktarma ve yayımlama

Bu bölümde, bir OpenAPI belirtimi arka uç API’sinin nasıl içeri aktarılacağı ve yayımlanacağı gösterilmektedir.
 
1. **API YÖNETİMİ** bölümünden **API’ler** öğesini seçin.
2. Listeden **Openapı belirtimini** seçin ve açılır pencerede **tam** ' ı tıklatın.

    ![Bir API oluşturma](./media/api-management-get-started/create-api.png)

    **Ayarlar** sekmesine giderek, oluşturma sırasında veya daha sonra API değerlerini ayarlayabilirsiniz. Bir alanın yanındaki kırmızı yıldız alanın gerekli olduğunu gösterir.

    İlk API'nizi oluşturmak için aşağıdaki tabloda yer alan değerleri kullanın.

    | Ayar                   | Değer                                              | Açıklama                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
    |---------------------------|----------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | **OpenAPI Belirtimi** | https://conferenceapi.azurewebsites.net?format=json | API’yi uygulayan hizmete başvurur. API Management istekleri bu adrese iletir.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
    | **Görünen ad**          | *Tanıtım Konferansı API’si*                              | Hizmet URL’sini girdikten sonra sekme tuşuna basarsanız APIM, json'da ne olduğuna bağlı olarak bu alanı doldurur. <br/>Bu ad, Geliştirici portalında görüntülenir.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
    | **Adı**                  | *demo-conference-api*                              | API için benzersiz bir ad sağlar. <br/>Hizmet URL’sini girdikten sonra sekme tuşuna basarsanız APIM, json'da ne olduğuna bağlı olarak bu alanı doldurur.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
    | **Açıklama**           | API için isteğe bağlı bir açıklama sağlayın.        | Hizmet URL’sini girdikten sonra sekme tuşuna basarsanız APIM, json'da ne olduğuna bağlı olarak bu alanı doldurur.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
    | **URL düzeni**            | *HTTPS*                                            | API’ye erişmek için hangi protokollerin kullanılabileceğini belirler.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
    | **API URL’si soneki**        | *conference*                                       | Sonek, API yönetimi hizmetinin temel URL’sinin sonuna eklenir. API Management API'leri soneklerine bakarak ayırt eder; dolayısıyla belirli bir yayımcıdaki her API için sonekin benzersiz olması gerekir.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
    | **Ürünler**              | *Sınırsız*                                        | Ürünler bir veya daha fazla API arasındaki ilişkilendirmelerdir. Bir Ürüne birkaç API ekleyebilir ve bunları geliştirici portalı aracılığıyla geliştiricilere sunabilirsiniz. <br/>API’yi bir ürünle ilişkilendirerek yayımlarsınız (bu örnekte, *Sınırsız*). Bu yeni API’yi bir ürüne eklemek için ürün adını yazın (**Ayarlar** sayfasından da bunu yapabilirsiniz). Bu adım, API’yi birden fazla ürüne eklemek için birçok defa yinelenebilir.<br/>API’ye erişim elde etmek için geliştiricilerin önce bir ürüne abone olması gerekir. Abone olduklarında, ilgili üründeki tüm API’ler için geçerli olan bir abonelik anahtarı edinirler. <br/> APIM örneğini siz oluşturduysanız zaten bir yöneticisinizdir ve tüm ürünlere abone olmuşsunuz demektir.<br/> Varsayılan olarak, her bir API Management örneği iki örnek ürün ile birlikte gelir: **Başlangıç** ve **Sınırsız**. |
    | **Etiketler**                  |                                                    | API 'Leri düzenlemek için Etiketler. Etiketler arama, gruplama veya filtreleme için kullanılabilir.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
    | **Bu API sürümünü mi kullanıyorsunuz?**     |                                                    | Sürüm oluşturma hakkında daha fazla bilgi için bkz. [API’nizin birden çok sürümünü yayımlama](api-management-get-started-publish-versions.md)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |

    >[!NOTE]
    > API’yi yayımlamak için bir ürünle ilişkilendirmeniz gerekir. **Ayarlar sayfasından** bunu yapabilirsiniz.

3. **Oluştur**'u seçin.

> [!TIP]
> Kendi API tanımınızı içeri aktarma konusunda sorun yaşıyorsanız [bilinen sorunlar ve kısıtlamalar listesine bakın](api-management-api-import-restrictions.md).

## <a name="test-the-new-api-in-the-azure-portal"></a>Azure portal yeni API 'YI test etme

![API haritasını test etme](./media/api-management-get-started/01-import-first-api-01.png)

İşlemler doğrudan bir API’nin işlemlerini görüntülemek ve test etmek için kullanışlı bir yol sağlayan Azure portalından çağrılabilir.

1. Önceki adımda oluşturduğunuz API’yi seçin (**API’ler** sekmesinden).
2. **Test** sekmesine basın.
3. **GetSpeakers** seçeneğine tıklayın. Sayfa, sorgu parametreleri alanlarını ve üst bilgileri görüntüler, ancak bu durumda bir alan yok. Bu API ile ilişkilendirilmiş ürünün abonelik anahtarı için, üst bilgilerden biri "Ocp-Apim-Subscription-Key" üst bilgisidir. Anahtar otomatik olarak doldurulur.
4. **Gönder**’e basın.

    Arka uç, **200 OK** ve bazı verilerle yanıt verir.

## <a name="next-steps"> </a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
> * İlk API’nizi içeri aktarma
> * Azure portalında API’yi test etme

Sonraki öğreticiye ilerleyin:

> [!div class="nextstepaction"]
> [Ürün oluşturma ve yayımlama](api-management-howto-add-products.md)
