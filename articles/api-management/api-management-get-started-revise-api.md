---
title: Öğretici-önemli olmayan API değişikliklerini güvenli hale getirmek için API Management düzeltmeler kullanın
titleSuffix: Azure API Management
description: API Management’ta revizyonları kullanarak hataya neden olmayan değişiklikler yapmayı öğrenmek için bu öğreticideki adımları uygulayın.
services: api-management
documentationcenter: ''
author: vladvino
ms.service: api-management
ms.custom: mvc, devx-track-azurecli
ms.topic: tutorial
ms.date: 02/09/2021
ms.author: apimpm
ms.openlocfilehash: 6ab3f2e4e2164611820813a5fdb04e2b27df1ff2
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107877039"
---
# <a name="tutorial-use-revisions-to-make-non-breaking-api-changes-safely"></a>Öğretici: son olmayan API değişikliklerini güvenli hale getirmek için düzeltmeleri kullanma
API’niz kullanıma hazır olduğunda ve geliştiriciler tarafından kullanılmaya başladığında genellikle bu API’de değişiklikler yapmak ve aynı zamanda API’nizi çağıranları kesintiye uğratmamak istersiniz. Yaptığınız değişiklikleri geliştiricilere bildirmeniz de yararlıdır. 

Azure API Management 'de, değişiklikleri güvenli şekilde modelleyebilir ve test edebilmeniz için *düzeltilmeyen* API değişiklikleri yapmak üzere düzeltmeleri kullanın. Hazırsa, bir düzeltmeyi güncel hale getirebilirsiniz ve geçerli API 'nizi değiştirebilirsiniz. 

Arka plan için bkz. Azure API Management ile [sürümler & düzeltmeler](https://azure.microsoft.com/blog/versions-revisions/) ve [API sürümü oluşturma](https://azure.microsoft.com/blog/api-versioning-with-azure-api-management/).

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Yeni düzeltme ekleme
> * Düzeltmenizde hataya neden olmayan değişiklikler yapma
> * Düzeltmenizi geçerli hale getirme ve bir değişiklik günlüğü girdisi ekleme
> * Değişiklikleri ve değişiklik günlüğünü görmek için geliştirici portalına göz atma

:::image type="content" source="media/api-management-getstarted-revise-api/azure-portal.png" alt-text="Azure portal API düzeltmeleri":::

## <a name="prerequisites"></a>Önkoşullar

+ [Azure API Management terminolojisini](api-management-terminology.md) öğrenin.
+ Şu hızlı başlangıcı tamamlayın: [Azure API Management örneği oluşturma](get-started-create-service-instance.md).
+ Ayrıca, şu öğreticiyi tamamlayın: [İlk API'nizi içeri aktarma ve yayımlama](import-and-publish.md).

## <a name="add-a-new-revision"></a>Yeni düzeltme ekleme

1. [Azure Portal](https://portal.azure.com)oturum açın ve API Management örneğinize gidin.
1. **API’ler** seçeneğini belirleyin.
2. API listesinden (veya düzeltme eklemek istediğiniz başka bir API) **Tanıtım Konferansı API 'si** ' ni seçin.
3. **Düzeltmeler** sekmesini seçin.
4. **+ Düzeltme Ekle**' yi seçin.

   :::image type="content" source="media/api-management-getstarted-revise-api/07-add-revisions-01-add-new-revision.png" alt-text="API düzeltmesi ekleme":::

    > [!TIP]
    > Ayrıca, API 'nin bağlam menüsünde (**...**) **Düzeltme Ekle** ' yi de seçebilirsiniz.

5. Ne için kullanılacağını hatırlamaya yardımcı olmak için revizyonunuza ilişkin bir açıklama ekleyin.
6. **Oluştur**'u seçin
7. Yeni bir düzeltme oluşturulur.

    > [!NOTE]
    > Özgün API'niz **Düzeltme 1**’de kalır. Farklı bir düzeltmeyi güncel yapana kadar kullanıcılarınız bu düzeltmeyi çağırmaya devam eder.

## <a name="make-non-breaking-changes-to-your-revision"></a>Düzeltmenizde hataya neden olmayan değişiklikler yapma

1. API listesinden **Tanıtım Konferans API’sini** seçin.
1. Ekranın üst kısmında **Tasarım** sekmesini seçin.
1. **Düzeltme seçici**’nin (tasarım sekmesinin hemen üzerinde) **Düzeltme 2**’nin seçildiğini gösterdiğinden emin olun.

    > [!TIP]
    > Üzerinde çalışmak istediğiniz düzeltmeler arasında geçiş yapmak için düzeltme seçiciyi kullanın.
1. **+ İşlem Ekle**’yi seçin.
1. Yeni işleminizi **POST** olarak, işlemin Adını, Görünen Adını ve URL'sini ise **test** olarak ayarlayın.
1. Yeni işleminizi **kaydedin**.

   :::image type="content" source="media/api-management-getstarted-revise-api/07-add-revisions-02-make-changes.png" alt-text="Düzeltmeyi değiştirme":::
1. Şimdi **Düzeltme 2**' de bir değişiklik yaptınız. **Düzeltme 1**' e geri dönmek için sayfanın üst kısmındaki **Düzeltme seçiciyi** kullanın.
1. Yeni işleminizin **Düzeltme 1**’de görünmediğine dikkat edin. 

## <a name="make-your-revision-current-and-add-a-change-log-entry"></a>Düzeltmenizi geçerli hale getirme ve bir değişiklik günlüğü girdisi ekleme

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Sayfanın üst kısmındaki menüden **Düzeltmeler** sekmesini seçin.
1. **Düzeltme 2**’nin açılır menüsünü (**...**) açın.
1. **Geçerli yap**' ı seçin.
1. Bu değişiklik hakkında not göndermek istiyorsanız **Bu API Için ortak değişiklik günlüğü** ' nü seçin. Değişiklik yapmak için geliştiricilerin göreceği bir açıklama girin, örneğin: **düzeltmeleri test etme. Yeni "test" işlemi eklendi.**
1. **Düzeltme 2** artık geçerlidir.

    :::image type="content" source="media/api-management-getstarted-revise-api/revisions-menu.png" alt-text="Düzeltmeler penceresindeki düzeltme menüsü":::

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLı kullanmaya başlamak için:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Bir yayın oluşturmak ve güncelleştirmek için bu yordamı kullanın.

1. API kimliklerinizi görmek için [az APIM API List](/cli/azure/apim/api#az_apim_api_list) komutunu çalıştırın:

   ```azurecli
   az apim api list --resource-group apim-hello-word-resource-group \
       --service-name apim-hello-world --output table
   ```

   Sonraki komutta kullanılacak API KIMLIĞI `Name` değeri. API düzeltmesi `ApiRevision` sütununda.

1. Yayını oluşturmak için, bir sürüm notunda [az APIM API Release Create](/cli/azure/apim/api/release#az_apim_api_release_create) komutunu çalıştırın:

   ```azurecli
   az apim api release create --resource-group apim-hello-word-resource-group \
       --api-id demo-conference-api --api-revision 2 --service-name apim-hello-world \
       --notes 'Testing revisions. Added new "test" operation.'
   ```

   Yayın yaptığınız düzeltme, geçerli düzeltme haline gelir.

1. Sürümlerinizi görmek için [az APIM API Release List](/cli/azure/apim/api/release#az_apim_api_release_list) komutunu kullanın:

   ```azurecli
   az apim api release list --resource-group apim-hello-word-resource-group \
       --api-id echo-api --service-name apim-hello-world --output table
   ```

   Belirttiğiniz notlar changelog 'da görünür. Bunları, önceki komutun çıktısında görebilirsiniz.

1. Bir yayın oluşturduğunuzda, `--notes` parametresi isteğe bağlıdır. [Az APIM API Release Update](/cli/azure/apim/api/release#az_apim_api_release_update) komutunu kullanarak notları daha sonra ekleyebilir veya değiştirebilirsiniz:

   ```azurecli
   az apim api release update --resource-group apim-hello-word-resource-group \
       --api-id demo-conference-api --release-id 00000000000000000000000000000000 \
       --service-name apim-hello-world --notes "Revised notes."
   ```

   `Name`Yayın Kimliği sütunundaki değeri kullanın.

[Az APIM API Release Delete](/cli/azure/apim/api/release#az_apim_api_release_delete) komutunu çalıştırarak herhangi bir sürümü kaldırabilirsiniz:

```azurecli
az apim api release delete --resource-group apim-hello-word-resource-group \
    --api-id demo-conference-api --release-id 00000000000000000000000000000000 
    --service-name apim-hello-world
```

---

## <a name="browse-the-developer-portal-to-see-changes-and-change-log"></a>Değişiklikleri ve değişiklik günlüğünü görmek için geliştirici portalına göz atma

[Geliştirici Portalını](api-management-howto-developer-portal-customize.md)DENEDIYSENIZ, API değişikliklerini gözden geçirebilir ve günlüğü burada değiştirebilirsiniz.

1. Azure portalda **API’ler** öğesini seçin.
1. Üst menüden **Geliştirici Portalı** ' nı seçin.
1. Geliştirici portalında, **API 'ler**' i seçin ve **Tanıtım Konferansı API 'si**' ni seçin.
1. Yeni **test** işleminizin artık kullanılabilir olduğuna dikkat edin.
1. API adının yakınında **changelog** öğesini seçin.
1. Değişiklik günlüğü girdinizin bu listede göründüğüne dikkat edin.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
> * Yeni düzeltme ekleme
> * Düzeltmenizde hataya neden olmayan değişiklikler yapma
> * Düzeltmenizi geçerli hale getirme ve bir değişiklik günlüğü girdisi ekleme
> * Değişiklikleri ve değişiklik günlüğünü görmek için geliştirici portalına göz atma

Sonraki öğreticiye ilerleyin:

> [!div class="nextstepaction"]
> [API'nizin birden fazla sürümünü yayımlama](api-management-get-started-publish-versions.md)
