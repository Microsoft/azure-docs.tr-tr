---
title: Azure Izleyici çalışma kitabı açılan parametreleri
description: Açılan parametreleri içeren önceden oluşturulmuş ve özel parametreli çalışma kitapları ile karmaşık raporlamayı kolaylaştırın
services: azure-monitor
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.openlocfilehash: d6fb4954ff616bccd9b237aedb3001b0a8d592dc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101717205"
---
# <a name="workbook-drop-down-parameters"></a>Çalışma kitabı açılan parametreleri

Bırakma listeleri, kullanıcının bilinen kümeden bir veya daha fazla giriş değeri toplamasına izin verir (örneğin, uygulamanızın isteklerinden birini seçin). Bırakma listeleri, kullanıcılardan rastgele girişler toplamanın kolay bir yolunu sağlar. Açılan liste, özellikle etkileşimli raporlarınızda filtrelemeyi etkinleştirmek için yararlıdır. 

Açılan bir liste belirtmek için en kolay yol, parametre ayarında bir statik liste sağlamaktır. Daha ilgi çekici bir yol, listeyi bir KQL sorgusu aracılığıyla dinamik olarak kullanmaktır. Parametre ayarları aynı zamanda tek veya çoklu seçim olduğunu belirtmenize olanak tanır ve çoklu seçim ise, sonuç kümesinin nasıl biçimlendirilmesi gerektiğini (sınırlayıcı, teklif, vb.) belirtebilirsiniz.

## <a name="creating-a-static-drop-down-parameter"></a>Statik açılan parametre oluşturma

1. Düzenleme modunda boş bir çalışma kitabıyla başlayın.
2. Çalışma kitabı içindeki bağlantılardan _parametre Ekle_ ' yi seçin.
3. Mavi _parametre Ekle_ düğmesine tıklayın.
4. Açılır yeni parametre bölmesinde şunu girin:
    1. Parametre adı: `Environment`
    2. Parametre türü: `Drop down`
    3. Gerekli: `checked`
    4. İzin ver `multiple selection` : `unchecked`
    5. Veri al: `JSON`
5. JSON giriş metin bloğunda şu JSON kod parçacığını ekleyin:
    ```json
    [
        { "value":"dev", "label":"Development" },
        { "value":"ppe", "label":"Pre-production" },
        { "value":"prod", "label":"Production", "selected":true }
    ]
    ```
6. Mavi düğmesine basın `Update` .
7. Parametresini oluşturmak için araç çubuğundan ' Kaydet ' seçeneğini belirleyin.
8. Ortam parametresi, üç değere sahip bir açılan liste olacaktır.

    ![Statik bir Drown oluşturmayı gösteren resim](./media/workbooks-dropdowns/dropdown-create.png)

## <a name="creating-a-static-dropdown-with-groups-of-items"></a>Öğe gruplarıyla statik bir açılan menü oluşturma

Sorgu sonucu/JSON 'niz bir "Grup" alanı içeriyorsa, açılan menüde değer grupları görüntülenir. Yukarıdaki örneği izleyin, ancak bunun yerine aşağıdaki JSON kullanın:

```json
[
    { "value":"dev", "label":"Development", "group":"Development" },
    { "value":"dev-cloud", "label":"Development (Cloud)", "group":"Development" },
    { "value":"ppe", "label":"Pre-production", "group":"Test" },
    { "value":"ppe-test", "label":"Pre-production (Test)", "group":"Test" },
    { "value":"prod1", "label":"Prod 1", "selected":true, "group":"Production" },
    { "value":"prod2", "label":"Prod 2", "group":"Production" }
]
```

![Gruplanmış bir açılan menü örneğini gösteren resim](./media/workbooks-dropdowns/grouped-dropDown.png)


## <a name="creating-a-dynamic-drop-down-parameter"></a>Dinamik açılan parametre oluşturma
1. Düzenleme modunda boş bir çalışma kitabıyla başlayın.
2. Çalışma kitabı içindeki bağlantılardan _parametre Ekle_ ' yi seçin.
3. Mavi _parametre Ekle_ düğmesine tıklayın.
4. Açılır yeni parametre bölmesinde şunu girin:
    1. Parametre adı: `RequestName`
    2. Parametre türü: `Drop down`
    3. Gerekli: `checked`
    4. İzin ver `multiple selection` : `unchecked`
    5. Veri al: `Query`
5. JSON giriş metin bloğunda şu JSON kod parçacığını ekleyin:

    ```kusto
        requests
        | summarize by name
        | order by name asc
    ```
1. Mavi düğmesine basın `Run Query` .
2. Parametresini oluşturmak için araç çubuğundan ' Kaydet ' seçeneğini belirleyin.
3. RequestName parametresi, uygulamadaki tüm isteklerin adlarını açılan bir liste olacaktır.

    ![Dinamik açılan bir liste oluşturmayı gösteren resim](./media/workbooks-dropdowns/dropdown-dynamic.png)

## <a name="referencing-drop-down-parameter"></a>Açılan parametreye başvuruluyor

### <a name="in-kql"></a>KQL 'de
1. Çalışma kitabına bir sorgu denetimi ekleyin ve bir Application Insights kaynağı seçin.
2. KQL düzenleyicisinde, bu kod parçacığını girin

    ```kusto
        requests
        | where name == '{RequestName}'
        | summarize Requests = count() by bin(timestamp, 1h)

    ```
3. Bu, sorgu değerlendirmesi süresini şu şekilde genişletir:

    ```kusto
        requests
        | where name == 'GET Home/Index'
        | summarize Requests = count() by bin(timestamp, 1h)
    ```

4. Sonuçları görmek için sorguyu çalıştırın. İsteğe bağlı olarak, bunu bir grafik olarak işleyebilirsiniz.

    ![KQL 'de Başvurulmuş bir açılan liste gösteren resim](./media/workbooks-dropdowns/dropdown-reference.png)


## <a name="parameter-value-label-selection-and-group"></a>Parametre değeri, etiket, seçim ve Grup
Yukarıdaki dinamik açılan parametresinde kullanılan sorgu yalnızca açılan listede faithtam işlenen değerlerin bir listesini döndürür. Ancak farklı bir görünen ad isterseniz veya bunlardan biri seçilmelidir. Açılan parametreler, değer, etiket, seçim ve grup sütunları aracılığıyla buna izin verir.

Aşağıdaki örnek, görünen adları emoji ile stillendirilmiş, seçili olan ve işlem adlarına göre gruplandırılan Application Insights bağımlılıkların bir listesinin nasıl alınacağını gösterir.

```kusto
dependencies
| summarize by operation_Name, name
| where name !contains ('.')
| order by name asc
| serialize Rank = row_number()
| project value = name, label = strcat('🌐 ', name), selected = iff(Rank == 1, true, false), group = operation_Name
```

![Değer, etiket, seçim ve grup seçeneklerini kullanarak bir açılan parametreyi gösteren resim](./media/workbooks-dropdowns/dropdown-more-options.png)


## <a name="drop-down-parameter-options"></a>Açılan parametre seçenekleri
| Parametre | Açıklama | Örnek |
| ------------- |:-------------|:-------------|
| `{DependencyName}` | Seçili değer | Fabrikamaccount al |
| `{DependencyName:label}` | Seçili etiket | fabrikamaccount al 🌐 |
| `{DependencyName:value}` | Seçili değer | Fabrikamaccount al |

## <a name="multiple-selection"></a>Çoklu seçim
Örnek olarak, bir parametreyi doğrudan açılan kutuda yalnızca bir değer seçmek üzere ayarlamaya yönelik örnekler. Açılan parametreler de desteklenir `multiple selection` -Bu seçenek, seçeneğini denetlemek kadar basittir `Allow multiple selection` . 

Kullanıcı ayrıca, ve ayarları aracılığıyla sonuç kümesinin biçimini belirtme seçeneğine sahiptir `delimiter` `quote with` . Varsayılan değer Şu biçimdeki değerleri bir koleksiyon olarak döndürür: ' a ', ' b ', ' c '. Ayrıca, seçimlerin sayısını sınırlama seçeneği de vardır.

Parametreye başvuran KQL 'in, sonucun biçimiyle çalışacak şekilde değiştirilmesi gerekir. Bunu etkinleştirmenin en yaygın yolu işleç aracılığıyla yapılır `in` .

```kusto
dependencies
| where name in ({DependencyName})
| summarize Requests = count() by bin(timestamp, 1h), name
```

İşte, çalışma sırasında çoklu seçim açılan bir örnek aşağıda verilmiştir:

![Çoklu seçim açılan bir parametre gösteren resim](./media/workbooks-dropdowns/dropdown-multiselect.png)

## <a name="next-steps"></a>Sonraki adımlar

* Çok sayıda zengin görselleştirmeler seçeneği hakkında daha fazla [bilgi edinmeye başlayın](./workbooks-overview.md#visualizations) .
* Çalışma kitabı kaynaklarınıza erişimi [denetleme](./workbooks-access-control.md) ve paylaşma.