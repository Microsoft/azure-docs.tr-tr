---
title: Öğretici-Azure API Management bir ürün oluşturma ve yayımlama
description: Bu öğreticide, Azure API Management bir ürün oluşturup yayımlayabilirsiniz. Yayımlandıktan sonra, geliştiriciler ürünün API 'Lerini kullanmaya başlayabilir.
author: mikebudzynski
ms.service: api-management
ms.topic: tutorial
ms.date: 02/09/2021
ms.author: apimpm
ms.openlocfilehash: d0420b92fc94e0a1a9c8a4057f419a57a9909223
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100545165"
---
# <a name="tutorial-create-and-publish-a-product"></a>Öğretici: ürün oluşturma ve yayımlama  

Azure API Management, bir [*ürün*](api-management-terminology.md#term-definitions) bir veya daha fazla API ve kullanım koşulları ve kullanım koşulları içerir. Ürün yayımlandığında, geliştiriciler ürüne abone olabilir ve ürünün API'lerini kullanmaya başlayabilir.  

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Ürün oluşturma ve yayımlama
> * Ürüne bir API ekleme

:::image type="content" source="media/api-management-howto-add-products/added-product.png" alt-text="Portalda API Management ürünleri":::


## <a name="prerequisites"></a>Önkoşullar

+ [Azure API Management terminolojisini](api-management-terminology.md) öğrenin.
+ Şu hızlı başlangıcı tamamlayın: [Azure API Management örneği oluşturma](get-started-create-service-instance.md).
+ Ayrıca, şu öğreticiyi tamamlayın: [İlk API'nizi içeri aktarma ve yayımlama](import-and-publish.md).

## <a name="create-and-publish-a-product"></a>Ürün oluşturma ve yayımlama

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Azure portal oturum açın ve API Management örneğinize gidin.
1. Sol gezinti bölmesinde, **Ürünler**  >  **+ Ekle**' yi seçin.
1.  Ürün **Ekle** penceresinde, ürününüzü oluşturmak için aşağıdaki tabloda açıklanan değerleri girin.

    :::image type="content" source="media/api-management-howto-add-products/02-create-publish-product-01.png" alt-text="Portala ürün ekleme":::

    | Ad                     | Açıklama                                                                                                                                                                                                                                                                                                             |
    |--------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Görünen ad             | [Geliştirici Portalında](api-management-howto-developer-portal.md)görünmesini istediğiniz ad.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
    | Description              | Ürün hakkında, amacı, erişimi sağladığı API 'Ler ve diğer ayrıntılar gibi bilgiler sağlayın.                                                                                                                                               |
    | Durum                    | Ürünü yayımlamak istiyorsanız **yayımlandı** ' i seçin. Bir üründeki API'lerin çağrılabilmesi için önce ürünün yayımlanması gerekir. Varsayılan olarak, yeni ürünler yayımdan kaldırılır ve yalnızca  **Yöneticiler** grubu tarafından görülebilir.                                                                                      |
    | Abonelik gerektirir    | Ürünü kullanmak için bir kullanıcının abone olması gerekiyorsa seçin.                                                                                                                                                                                                                                   |
    | Onay gerekiyor        | Yöneticinin bu ürüne yönelik abonelik denemelerini gözden geçirmesini ve kabul etmesini ya da reddetmesini istiyorsanız seçin. Seçilmezse, abonelik girişimleri otomatik olarak onaylanır.                                                                                                                         |
    | Abonelik sayısı limiti | İsteğe bağlı olarak birden çok eşzamanlı aboneliğin sayısını sınırlayın.                                                                                                                                                                                                                                |
    | Yasal koşullar              | Abonelerin ürünü kullanmak için kabul etmek zorunda olduğu ürün kullanım koşullarını ekleyebilirsiniz.                                                                                                                                                                                                             |
    | API'ler                     | Bir veya daha fazla API seçin. Ayrıca, ürünü oluşturduktan sonra API 'Leri ekleyebilirsiniz. Daha fazla bilgi için, bu makalede daha sonra [bir ürüne API ekleme](#add-apis-to-a-product) bölümüne bakın. |

3. Yeni ürünü oluşturmak için **Oluştur** ' u seçin.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLı kullanmaya başlamak için:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Bir ürün oluşturmak için [az APIM Product Create](/cli/azure/apim/product#az_apim_product_create) komutunu çalıştırın:

```azurecli
az apim product create --resource-group apim-hello-word-resource-group \
    --product-name "Contoso product" --product-id contoso-product \
    --service-name apim-hello-world --subscription-required true \
    --state published --description "This is a test."
```

Ürününüz için çeşitli değerler belirtebilirsiniz:

   | Parametre | Açıklama |
   |-----------|-------------|
   | `--product-name` | [Geliştirici Portalında](api-management-howto-developer-portal.md)görünmesini istediğiniz ad. |
   | `--description`  | Ürün hakkında, amacı, erişimi sağladığı API 'Ler ve diğer ayrıntılar gibi bilgiler sağlayın. |
   | `--state`        | Ürünü yayımlamak istiyorsanız **yayımlandı** ' i seçin. Bir üründeki API'lerin çağrılabilmesi için önce ürünün yayımlanması gerekir. Varsayılan olarak, yeni ürünler yayımdan kaldırılır ve yalnızca  **Yöneticiler** grubu tarafından görülebilir. |
   | `--subscription-required` | Ürünü kullanmak için bir kullanıcının abone olması gerekiyorsa seçin. |
   | `--approval-required` | Yöneticinin bu ürüne yönelik abonelik denemelerini gözden geçirmesini ve kabul etmesini ya da reddetmesini istiyorsanız seçin. Seçilmezse, abonelik girişimleri otomatik olarak onaylanır. |
   | `--subscriptions-limit` | İsteğe bağlı olarak birden çok eşzamanlı aboneliğin sayısını sınırlayın.|
   | `--legal-terms`         | Abonelerin ürünü kullanmak için kabul etmek zorunda olduğu ürün kullanım koşullarını ekleyebilirsiniz. |

Geçerli ürünlerinizi görmek için [az APIM Product List](/cli/azure/apim/product#az_apim_product_list) komutunu kullanın:

```azurecli
az apim product list --resource-group apim-hello-word-resource-group \
    --service-name apim-hello-world --output table
```

[Az APIM Product Delete](/cli/azure/apim/product#az_apim_product_delete) komutunu kullanarak bir ürünü silebilirsiniz:

```azurecli
az apim product delete --product-id contoso-product \
    --resource-group apim-hello-word-resource-group \
    --service-name apim-hello-world --delete-subscriptions true
```

---

### <a name="add-more-configurations"></a>Daha fazla yapılandırma ekleme

Ürünü kaydettikten sonra yapılandırmaya devam edin. API Management Örneğinizde, **Ürünler** penceresinden ürünü seçin. Ekle veya güncelleştir:

|Öğe   |Açıklama  |
|---------|---------|
|Ayarlar     |    Ürün meta verileri ve durumu     |
|API'ler     |  Ürünle ilişkili API 'Ler       |
|[İlkeler](api-management-howto-policies.md)     |  Ürün API 'Lerine uygulanan ilkeler      |
|Erişim denetimi     |  Geliştiriciler veya Konuklar için ürün görünürlüğü       |
|[Abonelikler](api-management-subscriptions.md)    |    Ürün aboneleri     |

## <a name="add-apis-to-a-product"></a>Bir ürüne API ekleme

Ürünler bir veya daha fazla API arasındaki ilişkilendirmelerdir. Bir dizi API ekleyebilir ve geliştirici portalı aracılığıyla geliştiricilere sunabilirsiniz. Ürün oluşturma sırasında, bir veya daha fazla var olan API ekleyebilirsiniz. Ayrıca, ürün **ayarları** sayfasından veya bir API oluştururken, daha sonra da ürüne API 'ler ekleyebilirsiniz.

Geliştiricilerin bir API’ye erişebilmesi için önce ürüne abone olması gerekir. Abone olduklarında, ilgili üründeki tüm API’ler için geçerli olan bir abonelik anahtarı edinirler. APIM örneğini siz oluşturduysanız zaten bir yöneticisinizdir ve varsayılan olarak tüm ürünlere abone olmuşsunuz demektir.

### <a name="add-an-api-to-an-existing-product"></a>Mevcut bir ürüne API ekleme

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. API Management örneğinizin sol gezinti bölmesinde **Ürünler**' i seçin.
1. Bir ürün seçin ve ardından **API 'ler**' i seçin.
1. **+ Ekle**'yi seçin.
1. Bir veya daha fazla API seçin ve ardından öğesini **seçin**.

:::image type="content" source="media/api-management-howto-add-products/02-create-publish-product-02.png" alt-text="Mevcut ürüne API ekleme":::

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Yönetilen API 'lerinizi görmek için [az APIM API List](/cli/azure/apim/api#az_apim_api_list) komutunu kullanın:

   ```azurecli
   az apim api list --resource-group apim-hello-word-resource-group \
       --service-name apim-hello-world --output table
   ```

1. Ürününüzü bir API eklemek için [az APIM Product API Add](/cli/azure/apim/product/api#az_apim_product_api_add) komutunu çalıştırın:

   ```azurecli
   az apim product api add --resource-group apim-hello-word-resource-group \
       --api-id demo-conference-api --product-id contoso-product \
       --service-name apim-hello-world
   ```

1. [Az APIM Product API List](/cli/azure/apim/product/api#az_apim_product_api_list) komutunu kullanarak eklemeyi doğrulayın:

   ```azurecli
   az apim product api list --resource-group apim-hello-word-resource-group \
       --product-id contoso-product --service-name apim-hello-world --output table
   ```

[Az APIM Product API Delete](/cli/azure/apim/product/api#az_apim_product_api_delete) komutunu kullanarak bir üründen bir API 'yi kaldırabilirsiniz:

```azurecli
az apim product api delete --resource-group apim-hello-word-resource-group \
    --api-id demo-conference-api --product-id contoso-product \
    --service-name apim-hello-world
```

---

> [!TIP]
> Bir kullanıcının aboneliğini, bir [REST API](/rest/api/apimanagement/2019-12-01/subscription/createorupdate) veya PowerShell komutu aracılığıyla özel abonelik anahtarlarıyla bir ürüne oluşturabilir veya güncelleyebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
> * Ürün oluşturma ve yayımlama
> * Ürüne bir API ekleme

Sonraki öğreticiye ilerleyin:

> [!div class="nextstepaction"]
> [Boş bir API ve sahte API yanıtları oluşturma](mock-api-responses.md)
