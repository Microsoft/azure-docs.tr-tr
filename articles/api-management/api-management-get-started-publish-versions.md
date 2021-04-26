---
title: Öğretici-Azure API Management kullanarak API 'nizin sürümlerini yayımlama
description: API Management 'de birden çok API sürümünün nasıl yayımlanacağını öğrenmek için Bu öğreticinin adımlarını izleyin.
author: vladvino
ms.service: api-management
ms.custom: mvc, devx-track-azurecli
ms.topic: tutorial
ms.date: 02/10/2021
ms.author: apimpm
ms.openlocfilehash: 5930979258372e4269c4d453ffbc5e0d46258088
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107483623"
---
# <a name="tutorial-publish-multiple-versions-of-your-api"></a>Öğretici: API 'nizin birden fazla sürümünü yayımlama 

API 'nize yönelik tüm çağıranların tam olarak aynı sürümü kullanması pratik olduğu durumlar vardır. Çağıranlar sonraki bir sürüme yükseltmek istiyorlarsa, anlaşılması kolay bir yaklaşım ister. Bu öğreticide gösterildiği gibi, Azure API Management 'de birden çok *Sürüm* sağlanması mümkündür. 

Arka plan için bkz. [sürümler & düzeltmeleri](https://azure.microsoft.com/blog/versions-revisions/).

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Yeni bir sürümü mevcut bir API’ye ekleme
> * Sürüm şeması seçme
> * Sürümü bir ürüne ekleme
> * Sürümü görüntülemek için geliştirici portalına göz atma

:::image type="content" source="media/api-management-getstarted-publish-versions/azure-portal.png" alt-text="Azure portal gösterilen sürüm":::

## <a name="prerequisites"></a>Önkoşullar

+ [Azure API Management terminolojisini](api-management-terminology.md) öğrenin.
+ Şu hızlı başlangıcı tamamlayın: [Azure API Management örneği oluşturma](get-started-create-service-instance.md).
+ Ayrıca, şu öğreticiyi tamamlayın: [İlk API'nizi içeri aktarma ve yayımlama](import-and-publish.md).

## <a name="add-a-new-version"></a>Yeni bir sürüm ekleme

1. [Azure portal](https://portal.azure.com)API Management örneğinize gidin.
1. **API’ler** seçeneğini belirleyin.
1. API listesinden **Tanıtım Konferans API’sini** seçin. 
1. **Tanıtım Konferansı API 'sinin** yanındaki bağlam menüsünü (**...**) seçin.
1. **Sürüm Ekle**' yi seçin.

:::image type="content" source="media/api-management-getstarted-publish-versions/add-version-menu.png" alt-text="API bağlam menüsü-sürüm Ekle":::


> [!TIP]
> Sürümler, yeni bir API oluşturduğunuzda da etkinleştirilebilir. **API Ekle** ekranında, **Bu API 'yi, sürüm?**' i seçin.

## <a name="choose-a-versioning-scheme"></a>Sürüm oluşturma düzeni seçme

Azure API Management, bir *sürüm oluşturma düzeni* seçerek ÇAĞıRANLARıN API sürümünü nasıl belirtmesini istediğinizi seçersiniz: **yol, üst bilgi** veya **sorgu dizesi**. Aşağıdaki örnekte, *Dizin* oluşturma düzeni olarak kullanılır.

Aşağıdaki tablodaki değerleri girin. Ardından, sürümünüzü oluşturmak için **Oluştur** ' u seçin.

:::image type="content" source="media/api-management-getstarted-publish-versions/add-version.png" alt-text="Sürüm penceresi Ekle":::



|Ayar   |Değer  |Açıklama  |
|---------|---------|---------|
|**Ad**     |  *Tanıtım-konferans-API-v1*       |  API Management örneğiniz benzersiz adı.<br/><br/>Bir sürüm aslında bir API 'nin [düzeltmesini](api-management-get-started-revise-api.md)temel alarak yenı bir API 'de olduğundan, bu ayar yeni API 'nin adıdır.   |
|**Sürüm oluşturma düzeni**     |  **Yol**       |  Arayanlara API sürümünü belirtme şekli.     |
|**Sürüm tanımlayıcı**     |  *v1*       |  Sürümün şemaya özgü göstergesi. **Yol** IÇIN, API URL yolu için sonek. <br/><br/> **Üst bilgi** veya **sorgu dizesi** seçilirse, ek bir değer girin: üst bilgi veya sorgu dizesi parametresinin adı.<br/><br/> Kullanım örneği görüntülenir.        |
|**Ürünler**     |  **Sınırsız**       |  İsteğe bağlı olarak, API sürümünün ilişkilendirildiği bir veya daha fazla ürün. API’yi yayımlamak için bir ürünle ilişkilendirmeniz gerekir. Ayrıca sürümü daha sonra [bir ürüne ekleyebilirsiniz](#add-the-version-to-a-product) .      |

Sürüm oluşturulduktan sonra API listesinde **demo Conference API** altında görüntülenir. Şimdi iki API görürsünüz: **özgün** ve **v1**.

![Azure portalında bir API altında listelenen sürümler](media/api-management-getstarted-publish-versions/version-list.png)

Şimdi, **v1** 'yi **orijinalden** ayrı bir API olarak düzenleyebilir ve yapılandırabilirsiniz. Bir sürümde yapılan değişiklikler diğer bir sürümü etkilemez.

> [!Note]
> Sürümü sürümü olmayan bir API 'ye sürüm eklerseniz, bir **özgün** de otomatik olarak oluşturulur. Bu sürüm varsayılan URL 'de yanıt verir. Orijinal sürüm oluşturmak, mevcut çağıranların sürüm ekleme işlemi tarafından kesilmemesini sağlar. Başlangıçta etkinleştirilmiş sürümlerle yeni bir API oluşturursanız, özgün bir oluşturulmaz.

## <a name="add-the-version-to-a-product"></a>Sürümü bir ürüne ekleme

Çağıranların yeni sürümü görmesi için sürümün *ürüne* eklenmesi gerekir. Sürümü henüz bir ürüne eklemediğiniz takdirde, uygulamayı dilediğiniz zaman bir ürüne ekleyebilirsiniz.

Örneğin, sürümü **sınırsız** ürüne eklemek için:
1. Azure portal API Management örneğinize gidin.
1. **Ürünleri**  >  **sınırsız**  >  **API**  >  **+ Ekle**' yi seçin.
1. **Tanıtım Konferansı API 'si**, sürüm **v1**' yi seçin.
1. **Seç**’e tıklayın.

:::image type="content" source="media/api-management-getstarted-publish-versions/08-add-multiple-versions-03-add-version-product.png" alt-text="Ürüne sürüm Ekle":::

## <a name="use-version-sets"></a>Sürüm kümelerini kullanma

Birden çok sürüm oluşturduğunuzda Azure portal, tek bir mantıksal API için bir sürüm kümesini temsil eden bir *sürüm kümesi* oluşturur. Birden çok sürümü olan bir API 'nin adını seçin. Azure portal, **Sürüm kümesini** görüntüler. Bir sanal küme **adını** ve **açıklamasını** özelleştirebilirsiniz.

Azure CLı kullanarak doğrudan sürüm kümeleriyle etkileşim kurabilirsiniz:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Tüm sürüm kümelerinizi görmek için [az APIM API versionset List](/cli/azure/apim/api/versionset#az_apim_api_versionset_list) komutunu çalıştırın:

```azurecli
az apim api versionset list --resource-group apim-hello-word-resource-group \
    --service-name apim-hello-world --output table
```

Azure portal sizin için bir sürüm kümesi oluşturduğunda, listenin **ad** sütununda görünen alfasayısal bir ad atar. Diğer Azure CLı komutlarında bu adı kullanın.

Bir sürüm kümesi hakkındaki ayrıntıları görmek için [az APIM API versionset Show](/cli/azure/apim/api/versionset#az_apim_api_versionset_show) komutunu çalıştırın:

```azurecli
az apim api versionset show --resource-group apim-hello-word-resource-group \
    --service-name apim-hello-world --version-set-id 00000000000000000000000
```

Sürüm kümeleri hakkında daha fazla bilgi için bkz. [Azure API Management sürümler](api-management-versions.md#how-versions-are-represented).

## <a name="browse-the-developer-portal-to-see-the-version"></a>Sürümü görüntülemek için geliştirici portalına göz atma

[Geliştirici Portalını](api-management-howto-developer-portal-customize.md)DENEDIYSENIZ, API sürümlerini burada görebilirsiniz.

1. Üstteki menüden **Geliştirici Portalı**’nı seçin.
2. **API'ler** ve ardından **Tanıtım Konferans API’si** öğesini seçin.
3. API adının yanında birden fazla sürümü olan bir açılan menü görmeniz gerekir.
4. **v1**’i seçin.
5. Listedeki ilk işlemin **İstek URL'sine** dikkat edin. Bu, API URL’si yolunun **v1** içerdiğini gösterir.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
> * Yeni bir sürümü mevcut bir API’ye ekleme
> * Sürüm şeması seçme 
> * Sürümü bir ürüne ekleme
> * Sürümü görüntülemek için geliştirici portalına göz atma

Sonraki öğreticiye ilerleyin:

> [!div class="nextstepaction"]
> [Geliştirici portalı sayfalarının stilini özelleştirme](api-management-howto-developer-portal-customize.md)
