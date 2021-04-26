---
title: Öğretici-API Management içindeki sahte API yanıtları Azure portal | Microsoft Docs
description: Bu öğreticide, bir API üzerinde bir ilke ayarlamak için API Management kullanırsınız, böylece arka uç gerçek yanıtları göndermek için kullanılamaz.
author: vladvino
ms.service: api-management
ms.custom: mvc, devx-track-azurecli
ms.topic: tutorial
ms.date: 02/09/2021
ms.author: apimpm
ms.openlocfilehash: 6214594f825908388762ac35cc8c8722ff9135f1
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107874360"
---
# <a name="tutorial-mock-api-responses"></a>Öğretici: sahte API yanıtları

Arka uç API 'Leri, bir API Management (APıM) API 'sine içeri aktarılabilir veya el ile oluşturulup yönetilebilir. Bu öğreticideki adımlarda, APıM kullanarak boş bir API oluşturup el ile nasıl yönetebileceğinizi ve ardından bir API üzerinde bir ilke ayarlayıp bir API üzerinde bir bir yanıt döndürmesinin nasıl ayarlanacağı gösterilmektedir. Bu yöntem, arka ucun gerçek yanıtlar göndermek için kullanılamadığı durumlarda bile geliştiricilerin APIM örneğinde uygulama ve test işlemlerine devam etmesini sağlar. 

Yanıtları artırma özelliği, çeşitli senaryolarda yararlı olabilir:

+ İlk olarak API cephesi tasarlanıp arka uç uygulaması daha sonra geldiğinde. Veya arka uç paralel olarak geliştirildiğinde.
+ Arka uç geçici olarak çalışır durumda olmadığında veya ölçeklenemediğinde.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Test API’si oluşturma 
> * Test API’sine işlem ekleme
> * Sahte yanıt vermeyi etkinleştirme
> * Sahte API’yi test etme


:::image type="content" source="media/mock-api-responses/mock-api-responses01.png" alt-text="Moclenmiş API yanıtı":::

## <a name="prerequisites"></a>Önkoşullar

+ [Azure API Management terminolojisini](api-management-terminology.md) öğrenin.
+ [Azure API Management'ta ilke kavramını](api-management-howto-policies.md) anlayın.
+ Şu hızlı başlangıcı tamamlayın: [Azure API Management örneği oluşturma](get-started-create-service-instance.md).

## <a name="create-a-test-api"></a>Test API’si oluşturma 

Bu bölümdeki adımlar arka uç olmadan boş bir API oluşturmayı gösterir. 


1. Azure portal oturum açın ve API Management örneğinize gidin.
1. **API 'leri** seçin  >  **+ API**  >  **boş API 'si** ekleyin.
1. **Boş BIR API oluştur** penceresinde **tam**' ı seçin.
1. **Görünen ad** için *Test API 'si* girin.
1. **Ürün** için **sınırsız** ' yı seçin.
1. **Ağ geçitlerinde** **yönetilen** ' ın seçili olduğundan emin olun.
1. **Oluştur**’u seçin.

    :::image type="content" source="media/mock-api-responses/03-mock-api-responses-01-create-test-api.png" alt-text="Boş API oluşturma":::

## <a name="add-an-operation-to-the-test-api"></a>Test API’sine işlem ekleme

Bir API, bir veya daha fazla işlem sunar. Bu bölümde, oluşturduğunuz boş API 'ye bir işlem ekleyin. Bu bölümdeki adımları tamamladıktan sonra işlem çağrılırsa bir hata oluşur. [Yanıt verme sahte işlem](#enable-response-mocking) bölümünde adımları tamamladıktan sonra herhangi bir hata almaz.

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Önceki adımda oluşturduğunuz API’yi seçin.
1. **+ İşlem Ekle**’yi seçin.
1. **Ön uç** penceresinde, aşağıdaki değerleri girin.

     | Ayar             | Değer                             | Açıklama                                                                                                                                                                                   |
    |---------------------|-----------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | **Görünen ad**    | *Test çağrısı*                       | [Geliştirici Portalında](api-management-howto-developer-portal.md)görüntülenen ad.                                                                                                                                       |
    | **URL** (HTTP fiili) | GET                               | Önceden tanımlanmış HTTP fiillerinden birini seçin.                                                                                                                                         |
    | **URL**             | */test*                           | API için bir URL yolu.                                                                                                                                                                       |
    | **Açıklama**     |                                   |  Bu API kullanan geliştiricilere Geliştirici Portalında belge sağlamak için kullanılan işlemin isteğe bağlı açıklaması.                                                    |
    
1. URL, görünen ad ve açıklama alanları altında bulunan **yanıtlar** sekmesini seçin. Yanıt durum kodları, içerik türleri, örnekler ve şemalar tanımlamak için bu sekmeye ayarları girin.
1. **+ Yanıt Ekle**' yi seçin ve listeden **200 Tamam** ' ı seçin.
1. Sağ taraftaki **Gösterimler** başlığının altında **+ Gösterim ekle**’yi seçin.
1. Arama kutusuna *Application/JSON* girin ve **Application/JSON** içerik türünü seçin.
1. **Örnek** metin kutusuna `{ "sampleField" : "test" }` girin.
1. **Kaydet**’i seçin.

:::image type="content" source="media/mock-api-responses/03-mock-api-responses-02-add-operation.png" alt-text="API işlemi ekleme" border="false":::

Bu örnek için gerekli olmamasına rağmen, bir API işleminin ek ayarları diğer sekmelerde yapılandırılabilir; örneğin:


|Tab      |Description  |
|---------|---------|
|**Sorgu**     |  Sorgu parametreleri ekleyin. Bir ad ve açıklama sağlamanın yanı sıra, bir sorgu parametresine atanmış değerler sağlayabilirsiniz. Varsayılan olarak işaretlenebilecek değerlerde biri (isteğe bağlı).        |
|**İstek**     |  İstek içerik türleri, örnekler ve şemalar tanımlayın.       |

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLı kullanmaya başlamak için:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Test API 'nize bir işlem eklemek için [az APIM API Operation Create](/cli/azure/apim/api/operation#az_apim_api_operation_create) komutunu çalıştırın:

```azurecli
az apim api operation create --resource-group apim-hello-word-resource-group \
    --display-name "Test call" --api-id test-api --method GET \
    --url-template /test --service-name apim-hello-world 
```

Bir API 'nin tüm işlemlerinizi görmek için [az APIM API Operation List](/cli/azure/apim/api/operation#az_apim_api_operation_list) komutunu çalıştırın:

```azurecli
az apim api operation list --resource-group apim-hello-word-resource-group \
    --api-id test-api --service-name apim-hello-world --output table
```

Bir işlemi kaldırmak için [az APIM API Operation Delete](/cli/azure/apim/api/operation#az_apim_api_operation_delete) komutunu kullanın. Önceki komuttan işlem KIMLIĞINI alın.

```azurecli
az apim api operation delete --resource-group apim-hello-word-resource-group \
    --api-id test-api --operation-id 00000000000000000000000000000000 \
    --service-name apim-hello-world
```

Bu işlemi bu makalenin geri kalanında kullanmak üzere saklayın.

---

## <a name="enable-response-mocking"></a>Sahte yanıt vermeyi etkinleştirme

1. [Test API 'Si oluşturma](#create-a-test-api)bölümünde oluşturduğunuz API 'yi seçin.
1. Eklediğiniz test işlemini seçin.
1. Sağdaki pencerede, **Tasarım** sekmesinin seçili olduğundan emin olun.
1. **Gelen işlem** penceresinde **+ ilke Ekle**' yi seçin.

    :::image type="content" source="media/mock-api-responses/03-mock-api-responses-03-enable-mocking.png" alt-text="İşlem İlkesi Ekle" border="false":::

1. Galeriden **sahte yanıtlar**  ' ı seçin.

    :::image type="content" source="media/mock-api-responses/mock-responses-policy-tile.png" alt-text="Sahte yanıtlar ilke kutucuğu" border="false":::

1. **API Management yanıtı** metin kutusuna **200 OK, application/json** yazın. Bu seçim, API’nizin önceki bölümde tanımladığınız yanıt örneğini döndürmesi gerektiğini gösterir.

    :::image type="content" source="media/mock-api-responses/mock-api-responses-set-mocking.png" alt-text="Sahte işlem yanıtını ayarla":::

1. **Kaydet**’i seçin.

    > [!TIP]
    > API 'niz için metin **taşıtmasını etkin** olan sarı bir çubuk, API Management 'den döndürülen yanıtların [sahte işlem ilkesi](api-management-advanced-policies.md#mock-response) tarafından tıklandığını ve arka uç tarafından üretilmediğini belirtir.

## <a name="test-the-mocked-api"></a>Sahte API’yi test etme

1. [Test API 'Si oluşturma](#create-a-test-api)bölümünde oluşturduğunuz API 'yi seçin.
1. **Test** sekmesini seçin.
1. **Test çağrısı** API’sinin seçili olduğundan emin olun. Bir test çağrısı yapmak için **Gönder**’i seçin.

   :::image type="content" source="media/mock-api-responses/03-mock-api-responses-04-test-mocking.png" alt-text="Sahte API’yi test etme":::

1. **HTTP yanıtı**, öğreticinin ilk bölümde örnek olarak sağlanan JSON’u görüntüler.

    :::image type="content" source="media/mock-api-responses/mock-api-responses-test-response.png" alt-text="Sahte HTTP yanıtı":::

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
> * Test API’si oluşturma
> * Test API’sine işlem ekleme
> * Sahte yanıt vermeyi etkinleştirme
> * Sahte API’yi test etme

Sonraki öğreticiye ilerleyin:

> [!div class="nextstepaction"]
> [Yayımlanan API’yi dönüştürme ve koruma](transform-api.md)
