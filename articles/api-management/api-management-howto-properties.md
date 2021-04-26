---
title: Azure API Management ilkelerinde adlandırılmış değerleri kullanma
description: Azure API Management ilkelerinde adlandırılmış değerleri nasıl kullanacağınızı öğrenin. Adlandırılmış değerler, Azure Key Vault depolanan sabit dizeler, ilke ifadeleri ve gizli dizileri içerebilir.
services: api-management
documentationcenter: ''
author: vladvino
ms.service: api-management
ms.topic: article
ms.date: 02/09/2021
ms.author: apimpm
ms.openlocfilehash: a970eb23c171522907b6066454d9ca15d85f0835
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107812303"
---
# <a name="use-named-values-in-azure-api-management-policies"></a>Azure API Management ilkelerinde adlandırılmış değerleri kullanın

[API Management ilkeleri](api-management-howto-policies.md) , yayımcının API 'nin davranışını yapılandırma yoluyla değiştirmesine olanak tanıyan güçlü bir yetenektir. İlkeler, bir API isteği veya yanıtı üzerinde sırayla yürütülen deyimlerin bir koleksiyonudur. İlke deyimleri, sabit metin değerleri, ilke ifadeleri ve adlandırılmış değerler kullanılarak oluşturulabilir.

*Adlandırılmış değerler* her bir API Management örneğindeki genel ad/değer çiftleri koleksiyonudur. Koleksiyondaki öğe sayısı üzerinde hiçbir uygulanan sınır yoktur. Adlandırılmış değerler, tüm API yapılandırmalarında ve ilkelerinde sabit dize değerlerini ve gizli dizileri yönetmek için kullanılabilir. 

:::image type="content" source="media/api-management-howto-properties/named-values.png" alt-text="Azure portal adlandırılmış değerler":::

## <a name="value-types"></a>Değer türleri

|Tür  |Description  |
|---------|---------|
|Düz     |  Sabit dize veya ilke ifadesi     |
|Gizli dizi     |   API Management tarafından şifrelenen sabit dize veya ilke ifadesi      |
|[Anahtar Kasası](#key-vault-secrets)     |  Azure Anahtar Kasası 'nda depolanan bir gizli dizi tanımlayıcısı.      |

Düz değerler veya gizlilikler, [ilke ifadeleri](./api-management-policy-expressions.md)içerebilir. Örneğin, ifade `@(DateTime.Now.ToString())` geçerli tarih ve saati içeren bir dize döndürür.

Adlandırılmış değer öznitelikleri hakkında daha fazla bilgi için API Management [REST API başvurusuna](/rest/api/apimanagement/2020-06-01-preview/namedvalue/createorupdate)bakın.

## <a name="key-vault-secrets"></a>Anahtar Kasası gizli dizileri

Gizli değerler, API Management (özel gizlilikler) şifreli dizeler olarak veya [Azure Key Vault](../key-vault/general/overview.md)gizli dizileri olarak depolanabilir. 

Anahtar Kasası gizliliklerin kullanılması önerilir API Management güvenliği artırmaya yardımcı olur:

* Anahtar kasalarında depolanan gizli dizileri, hizmetler arasında yeniden kullanılabilir
* Ayrıntılı [erişim ilkeleri](../key-vault/general/security-features.md#privileged-access) gizli anahtarlara uygulanabilir
* Anahtar kasasında güncellenen gizli diziler API Management otomatik olarak döndürülür. Anahtar kasasındaki güncelleştirmeden sonra, API Management bir adlandırılmış değer 4 saat içinde güncelleştirilir. Ayrıca, Azure portal kullanarak veya yönetim REST API aracılığıyla gizli dizi el ile yenileyebilirsiniz.

### <a name="prerequisites-for-key-vault-integration"></a>Anahtar Kasası tümleştirmesi için Önkoşullar

1. Bir Anahtar Kasası oluşturma adımları için bkz. [hızlı başlangıç: Azure Portal kullanarak bir Anahtar Kasası oluşturma](../key-vault/general/quick-create-portal.md).
1. API Management örneğinde sistem tarafından atanan veya Kullanıcı tarafından atanan bir [yönetilen kimliği](api-management-howto-use-managed-service-identity.md) etkinleştirin.
1. Kasadaki gizli dizileri almak ve listelemek için izinleri olan yönetilen kimliğe bir [Anahtar Kasası erişim ilkesi](../key-vault/general/assign-access-policy-portal.md) atayın. İlkeyi eklemek için:
    1. Portalda anahtar kasanıza gidin.
    1. **Ayarlar > erişim ilkeleri > + erişim Ilkesi Ekle**' yi seçin.
    1. **Gizli izinler**' i seçin, ardından **Al** ve **Listele**' yi seçin.
    1. **Asıl seçin**' de, yönetilen kimliğinizin kaynak adını seçin. Sistem tarafından atanan bir kimlik kullanıyorsanız sorumlu, API Management örneğinizin adıdır.
1. Anahtar kasasında bir gizli dizi oluşturun veya bu anahtarı içeri aktarın. Bkz. [hızlı başlangıç: Azure Key Vault Azure Portal kullanarak bir gizli dizi ayarlama ve alma](../key-vault/secrets/quick-create-portal.md).

Anahtar Kasası gizliliğini kullanmak için, [adlandırılmış bir değer ekleyin veya düzenleyin](#add-or-edit-a-named-value)ve **Anahtar Kasası** türünü belirtin. Anahtar kasasından gizli dizi seçin.

[!INCLUDE [api-management-key-vault-network](../../includes/api-management-key-vault-network.md)]

## <a name="add-or-edit-a-named-value"></a>Adlandırılmış değer ekleme veya düzenleme

### <a name="add-a-key-vault-secret"></a>Anahtar Kasası gizli anahtarı ekleme

Bkz. [Anahtar Kasası tümleştirmesi Için Önkoşullar](#prerequisites-for-key-vault-integration).

> [!CAUTION]
> API Management bir Anahtar Kasası gizli dizisi kullanırken, anahtar kasasına erişmek için kullanılan gizli anahtarı, anahtar kasasını veya yönetilen kimliği silmemeye dikkat edin.

1. [Azure portal](https://portal.azure.com)API Management örneğinize gidin.
1. **API 'ler** altında, **adlandırılmış değerler**  >  **+ Ekle**' yi seçin.
1. Bir **ad** tanımlayıcısı girin ve ilkelerde özelliğe başvurmak için kullanılan bir **görünen ad** girin.
1. **Değer türü**' nde **Anahtar Kasası**' nı seçin.
1. Bir Anahtar Kasası parolasının tanımlayıcısını (sürüm olmadan) girin veya bir anahtar kasasından gizli dizi seçmek için **Seç** ' i seçin.
    > [!IMPORTANT]
    > Kendi kendinize bir Anahtar Kasası gizli tanımlayıcısı girerseniz, sürüm bilgisi olmadığından emin olun. Aksi takdirde, anahtar kasasındaki bir güncelleştirmeden sonra gizli dizi API Management otomatik olarak döndürülmez.
1. **İstemci kimliği**' nde, sistem tarafından atanan veya Kullanıcı tarafından atanan mevcut bir yönetilen kimlik seçin. [API Management hizmetinize Yönetilen kimlikler ekleme veya değiştirme](api-management-howto-use-managed-service-identity.md)hakkında bilgi edinin.
    > [!NOTE]
    > Kimliğin, anahtar kasasından parolaları almak ve listelemek için izinleri olması gerekir. Zaten Anahtar Kasası 'na erişimi yapılandırmadıysanız, API Management kimliği, gerekli izinlerle otomatik olarak yapılandırabilmesi için sizi uyarır.
1. Adlandırılmış değerlerinizi düzenlemenize yardımcı olmak için bir veya daha fazla isteğe bağlı etiket ekleyin ve ardından **kaydedin**.
1. **Oluştur**’u seçin.

    :::image type="content" source="media/api-management-howto-properties/add-property.png" alt-text="Anahtar Kasası gizli değeri Ekle":::

### <a name="add-a-plain-or-secret-value"></a>Düz veya gizli değer ekleme

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. [Azure portal](https://portal.azure.com)API Management örneğinize gidin.
1. **API 'ler** altında, **adlandırılmış değerler**  >  **+ Ekle**' yi seçin.
1. Bir **ad** tanımlayıcısı girin ve ilkelerde özelliğe başvurmak için kullanılan bir **görünen ad** girin.
1. **Değer türü**' nde **düz** veya **gizli** öğesini seçin.
1. **Değer** alanına bir dize veya ilke ifadesi girin.
1. Adlandırılmış değerlerinizi düzenlemenize yardımcı olmak için bir veya daha fazla isteğe bağlı etiket ekleyin ve ardından **kaydedin**.
1. **Oluştur**’u seçin.

Adlandırılmış değer oluşturulduktan sonra, adı seçerek düzenleyebilirsiniz. Görünen adı değiştirirseniz, adlandırılmış değere başvuruda bulunan tüm ilkeler, yeni görünen adı kullanacak şekilde otomatik olarak güncelleştirilir.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLı kullanmaya başlamak için:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Adlandırılmış bir değer eklemek için [az APIM NV Create](/cli/azure/apim/nv#az_apim_nv_create) komutunu kullanın:

```azurecli
az apim nv create --resource-group apim-hello-word-resource-group \
    --display-name "named_value_01" --named-value-id named_value_01 \
    --secret true --service-name apim-hello-world --value test
```

Adlandırılmış bir değer oluşturduktan sonra, [az APIM NV Update](/cli/azure/apim/nv#az_apim_nv_update) komutunu kullanarak bunu güncelleştirebilirsiniz. Tüm adlandırılmış değerlerinizi görmek için [az APIM NV List](/cli/azure/apim/nv#az_apim_nv_list) komutunu çalıştırın:

```azurecli
az apim nv list --resource-group apim-hello-word-resource-group \
    --service-name apim-hello-world --output table
```

Bu örnek için oluşturduğunuz adlandırılmış değerin ayrıntılarını görmek için [az APIM NV Show](/cli/azure/apim/nv#az_apim_nv_show) komutunu çalıştırın:

```azurecli
az apim nv show --resource-group apim-hello-word-resource-group \
    --service-name apim-hello-world --named-value-id named_value_01
```

Bu örnek gizli bir değerdir. Önceki komut değeri döndürmez. Değeri görmek için [az APIM NV Show-Secret](/cli/azure/apim/nv#az_apim_nv_show_secret) komutunu çalıştırın:

```azurecli
az apim nv show-secret --resource-group apim-hello-word-resource-group \
    --service-name apim-hello-world --named-value-id named_value_01
```

Adlandırılmış bir değeri silmek için [az APIM NV Delete](/cli/azure/apim/nv#az_apim_nv_delete) komutunu kullanın:

```azurecli
az apim nv delete --resource-group apim-hello-word-resource-group \
    --service-name apim-hello-world --named-value-id named_value_01
```

---

## <a name="use-a-named-value"></a>Adlandırılmış bir değer kullan

Bu bölümdeki örnekler, aşağıdaki tabloda gösterilen adlandırılmış değerleri kullanır.

| Name               | Değer                      | Gizli dizi | 
|--------------------|----------------------------|--------|---------|
| ContosoHeader      | `TrackingId`                 | Yanlış  | 
| ContosoHeaderValue | ••••••••••••••••••••••     | Doğru   | 
| ExpressionProperty | `@(DateTime.Now.ToString())` | Yanlış  | 

İlkede adlandırılmış bir değer kullanmak için, `{{ContosoHeader}}` Aşağıdaki örnekte gösterildiği gibi, görünen adını gibi çift bir küme ayracı içine yerleştirin:

```xml
<set-header name="{{ContosoHeader}}" exists-action="override">
  <value>{{ContosoHeaderValue}}</value>
</set-header>
```

Bu örnekte, `ContosoHeader` bir ilkedeki üst bilgi adı olarak kullanılır `set-header` ve bu `ContosoHeaderValue` üstbilginin değeri olarak kullanılır. Bu ilke bir istek veya API Management ağ geçidine yanıt sırasında değerlendirildiğinde `{{ContosoHeader}}` ve `{{ContosoHeaderValue}}` ilgili değerleriyle değiştirilmiştir.

Adlandırılmış değerler, önceki örnekte gösterildiği gibi, tüm öznitelik veya öğe değerleri olarak kullanılabilir, ancak aşağıdaki örnekte gösterildiği gibi, değişmez değer metin ifadesinin bir bölümüne eklenebilir veya birleştirilebilir: 

```xml
<set-header name = "CustomHeader{{ContosoHeader}}" ...>
```

Adlandırılmış değerler, ilke ifadeleri de içerebilir. Aşağıdaki örnekte, `ExpressionProperty` ifadesi kullanılır.

```xml
<set-header name="CustomHeader" exists-action="override">
    <value>{{ExpressionProperty}}</value>
</set-header>
```

Bu ilke değerlendirildiğinde, `{{ExpressionProperty}}` değeri ile değiştirilmiştir `@(DateTime.Now.ToString())` . Değer bir ilke ifadesi olduğundan, ifade değerlendirilir ve ilke yürütmeye devam eder.

Kapsamda adlandırılmış değerler içeren bir işlem çağırarak Azure portal veya [Geliştirici Portalında](api-management-howto-developer-portal.md) bunu test edebilirsiniz. Aşağıdaki örnekte, bir işlem, `set-header` adlandırılmış değerlere sahip olan iki önceki örnek ilkesiyle birlikte çağırılır. Yanıtın, adlandırılmış değerleri olan ilkeler kullanılarak yapılandırılmış iki özel üst bilgi içerdiğine dikkat edin.

:::image type="content" source="media/api-management-howto-properties/api-management-send-results.png" alt-text="API yanıtını sına":::

Adlandırılmış değerlere sahip iki önceki örnek ilkeyi içeren bir çağrı için giden [API izlemeye](api-management-howto-api-inspector.md) bakarsanız, `set-header` adlandırılmış değerlerin eklendiği iki ilkeyi ve ilke ifadesini içeren adlandırılmış değer için ilke ifadesi değerlendirmesini görebilirsiniz.

:::image type="content" source="media/api-management-howto-properties/api-management-api-inspector-trace.png" alt-text="API denetçisi izleme":::

> [!CAUTION]
> Bir ilke Azure Key Vault bir gizli dizi başvurusu varsa, anahtar kasasındaki değer [API isteği izleme](api-management-howto-api-inspector.md)için etkinleştirilen aboneliklere erişimi olan kullanıcılar tarafından görülebilir.

Adlandırılmış değerler ilke ifadeleri içerebildiği sürece, diğer adlandırılmış değerleri içeremez. Adlandırılmış bir değer başvurusu içeren metin, gibi bir değer için kullanılırsa, `Text: {{MyProperty}}` Bu başvuru çözümlenmez ve değiştirilmez.

## <a name="delete-a-named-value"></a>Adlandırılmış bir değeri silme

Adlandırılmış bir değeri silmek için, adı seçin ve bağlam menüsünden **Sil** ' i (**...**) seçin.

> [!IMPORTANT]
> Adlandırılmış değere herhangi bir API Management ilkesi tarafından başvuruluyorsa, bunu kullanan tüm ilkelerden, adlandırılmış değeri kaldırana kadar silemezsiniz.

## <a name="next-steps"></a>Sonraki adımlar

-   İlkelerle çalışma hakkında daha fazla bilgi edinin
    -   [API Management ilkeler](api-management-howto-policies.md)
    -   [İlke başvurusu](./api-management-policies.md)
    -   [İlke ifadeleri](./api-management-policy-expressions.md)

[api-management-send-results]: ./media/api-management-howto-properties/api-management-send-results.png

