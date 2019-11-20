---
title: Azure API Management ilkelerinde adlandırılmış değerleri kullanma
description: Azure API Management ilkelerinde adlandırılmış değerleri nasıl kullanacağınızı öğrenin.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/05/2019
ms.author: apimpm
ms.openlocfilehash: d11239aa49a53a90a38f2b5336d36cea6c97e9df
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73824170"
---
# <a name="how-to-use-named-values-in-azure-api-management-policies"></a>Azure API Management ilkelerinde adlandırılmış değerleri kullanma

API Management ilkeleri, sistemin yapılandırma aracılığıyla API 'nin davranışını değiştirmesine Azure portal olanak tanıyan güçlü bir yetenektir. İlkeler, bir API isteği veya yanıtı üzerinde sırayla yürütülen deyimlerin bir koleksiyonudur. İlke deyimleri, sabit metin değerleri, ilke ifadeleri ve adlandırılmış değerler kullanılarak oluşturulabilir.

Her bir API Management hizmet örneği, hizmet örneği için genel olan adlandırılmış değerler olarak adlandırılan anahtar/değer çiftlerinin bir özellikler koleksiyonuna sahiptir. Koleksiyondaki öğe sayısı üzerinde hiçbir uygulanan sınır yoktur. Adlandırılmış değerler, tüm API yapılandırması ve ilkeleri genelinde sabit dize değerlerini yönetmek için kullanılabilir. Her bir adlandırılmış değer aşağıdaki özniteliklere sahip olabilir:

| Öznitelik      | Tür            | Açıklama                                                                                                                         |
| -------------- | --------------- | ----------------------------------------------------------------------------------------------------------------------------------- |
| `Display name` | string          | İlkelerdeki adlandırılmış değere başvurmak için kullanılır. Bir ile 256 karakter arasında bir dize. Yalnızca harfler, rakamlar, nokta ve tireye izin verilir. |
| `Value`        | string          | Gerçek değer. Boş olmamalı veya yalnızca boşluklardan oluşmalıdır. En fazla 4096 karakter uzunluğunda.                                     |
| `Secret`       | boole         | Değerin gizli olup olmadığını ve şifrelenmesinin gerekip gerekmediğini belirler.                                                            |
| `Tags`         | dize dizisi | Adlandırılmış değer listesini filtrelemek için kullanılır. En fazla 32 etiket.                                                                                    |

![Adlandırılmış değerler](./media/api-management-howto-properties/named-values.png)

Adlandırılmış değerler, sabit dizeler ve [ilke ifadeleri](/azure/api-management/api-management-policy-expressions)içerebilir. Örneğin, `Expression` değeri geçerli tarih ve saati içeren bir dize döndüren bir ilke deyimidir. Adlandırılmış değer `Credential` gizli olarak işaretlenir, bu nedenle değeri varsayılan olarak görüntülenmez.

| Ad       | Değer                      | Gizli dizi | Etiketler          |
| ---------- | -------------------------- | ------ | ------------- |
| Değer      | 42                         | False  | önemli sayılar |
| Kimlik Bilgisi | ••••••••••••••••••••••     | True   | güvenlik      |
| İfadeler | @ (DateTime. Now. ToString ()) | False  |               |

## <a name="to-add-and-edit-a-named-value"></a>Adlandırılmış bir değer eklemek ve düzenlemek için

![Adlandırılmış bir değer ekleyin](./media/api-management-howto-properties/add-property.png)

1. **API YÖNETİMİ** bölümünden **API’ler** öğesini seçin.
2. **Adlandırılmış değerleri**seçin.
3. **+ Ekle**tuşuna basın.

    Ad ve değer gerekli değerlerdir. Değer bir gizli dizi ise, *Bu gizli bir* onay kutusu olup olmadığını kontrol edin. Adlandırılmış değerlerinizi organize etmenize yardımcı olmak için bir veya daha fazla isteğe bağlı etiket girin ve Kaydet ' e tıklayın.

4. **Oluştur**'a tıklayın.

Adlandırılmış değer oluşturulduktan sonra, üzerine tıklayarak düzenleyebilirsiniz. Adlandırılmış değer adını değiştirirseniz, bu adlandırılmış değere başvuruda bulunan tüm ilkeler, yeni adı kullanacak şekilde otomatik olarak güncelleştirilir.

REST API kullanarak adlandırılmış bir değeri düzenleme hakkında daha fazla bilgi için, bkz. [adlandırılmış bir değeri REST API kullanarak düzenleme](/rest/api/apimanagement/2019-01-01/property?patch).

## <a name="to-delete-a-named-value"></a>Adlandırılmış bir değeri silmek için

Adlandırılmış bir değeri silmek için, silinecek adlandırılmış değerin yanındaki **Sil** ' e tıklayın.

> [!IMPORTANT]
> Adlandırılmış değere herhangi bir ilke tarafından başvuruluyorsa, adlandırılmış değeri onu kullanan tüm ilkelerden kaldırana kadar başarıyla silemezsiniz.

REST API kullanarak adlandırılmış bir değeri silme hakkında daha fazla bilgi için, bkz. [REST API kullanarak adlandırılan değeri silme](/rest/api/apimanagement/2019-01-01/property/delete).

## <a name="to-search-and-filter-named-values"></a>Adlandırılmış değerleri aramak ve filtrelemek için

**Adlandırılmış değerler** sekmesi, adlandırılmış değerlerinizi yönetmenize yardımcı olmak için arama ve filtreleme özelliklerini içerir. Adlandırılmış değerler listesini ada göre filtrelemek için **arama özelliği** metin kutusuna bir arama terimi girin. Tüm adlandırılmış değerleri göstermek için **arama özelliği** metin kutusunu temizleyin ve ENTER tuşuna basın.

Listeyi etikete göre filtrelemek için **etiketlere göre filtrele** metin kutusuna bir veya daha fazla etiket girin. Tüm adlandırılmış değerleri göstermek için **etiketlere göre filtrele** metin kutusunu temizleyin ve ENTER tuşuna basın.

## <a name="to-use-a-named-value"></a>Adlandırılmış bir değer kullanmak için

Bir ilkede adlandırılmış bir değer kullanmak için, aşağıdaki örnekte gösterildiği gibi, adını `{{ContosoHeader}}`gibi çift küme ayracı içine yerleştirin:

```xml
<set-header name="{{ContosoHeader}}" exists-action="override">
  <value>{{ContosoHeaderValue}}</value>
</set-header>
```

Bu örnekte `ContosoHeader`, bir `set-header` ilkesindeki bir üstbilginin adı olarak kullanılır ve bu üst bilginin değeri olarak `ContosoHeaderValue` kullanılır. Bu ilke bir istek sırasında veya API Management ağ geçidine yanıt sırasında değerlendirildiğinde, `{{ContosoHeader}}` ve `{{ContosoHeaderValue}}` ilgili değerleriyle değiştirilmiştir.

Adlandırılmış değerler, önceki örnekte gösterildiği gibi, tüm öznitelik veya öğe değerleri olarak kullanılabilir, ancak aşağıdaki örnekte gösterildiği gibi, değişmez değer metin ifadesinin bir bölümüyle de eklenebilir veya birleştirilebilir: `<set-header name = "CustomHeader{{ContosoHeader}}" ...>`

Adlandırılmış değerler, ilke ifadeleri de içerebilir. Aşağıdaki örnekte `ExpressionProperty` kullanılır.

```xml
<set-header name="CustomHeader" exists-action="override">
    <value>{{ExpressionProperty}}</value>
</set-header>
```

Bu ilke değerlendirildiğinde `{{ExpressionProperty}}`, değeri ile değiştirilmiştir: `@(DateTime.Now.ToString())`. Değer bir ilke ifadesi olduğundan, ifade değerlendirilir ve ilke yürütmeye devam eder.

Kapsam içinde adlandırılmış değerlere sahip bir ilkeye sahip bir işlem çağırarak Geliştirici Portalında bunu test edebilirsiniz. Aşağıdaki örnekte, bir işlem adlandırılan iki örnekle `set-header` adlandırılmış değerlerle birlikte çağırılır. Yanıtın, adlandırılmış değerleri olan ilkeler kullanılarak yapılandırılmış iki özel üst bilgi içerdiğini unutmayın.

![Geliştirici portalı][api-management-send-results]

Adlandırılmış değerlere sahip iki önceki örnek ilkeyi içeren bir çağrı için [API denetçisi izlemeye](api-management-howto-api-inspector.md) bakarsanız, adlandırılmış değerlerin eklendiği iki `set-header` ilkesini ve adlandırılmış değer için ilke ifadesi değerlendirmesini görebilirsiniz ilke ifadesini içeriyordu.

![API denetçisi izleme][api-management-api-inspector-trace]

Adlandırılmış değerler ilke ifadeleri içerebildiği sürece, diğer adlandırılmış değerleri içeremez. Adlandırılmış bir değer başvurusu içeren metin, `Text: {{MyProperty}}`gibi bir değer için kullanılırsa, bu başvuru çözümlenmez ve değiştirilmez.

## <a name="next-steps"></a>Sonraki adımlar

-   İlkelerle çalışma hakkında daha fazla bilgi edinin
    -   [API Management ilkeler](api-management-howto-policies.md)
    -   [İlke başvurusu](/azure/api-management/api-management-policies)
    -   [İlke ifadeleri](/azure/api-management/api-management-policy-expressions)

[api-management-send-results]: ./media/api-management-howto-properties/api-management-send-results.png
[api-management-properties-filter]: ./media/api-management-howto-properties/api-management-properties-filter.png
[api-management-api-inspector-trace]: ./media/api-management-howto-properties/api-management-api-inspector-trace.png
