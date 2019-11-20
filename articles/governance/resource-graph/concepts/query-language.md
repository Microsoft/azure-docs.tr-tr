---
title: Sorgu dilini anlayın
description: Kaynak grafik tablolarını ve kullanılabilir kusto veri türlerini, işleçlerini ve Azure Kaynak Graf ile kullanılabilir işlevleri açıklar.
ms.date: 10/21/2019
ms.topic: conceptual
ms.openlocfilehash: baef46f4ba6f899c2c0a1392f87006223d75a4e1
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73959044"
---
# <a name="understanding-the-azure-resource-graph-query-language"></a>Azure Kaynak Grafiği sorgu dilini anlama

Azure Kaynak grafiğinin sorgu dili, bir dizi işleci ve işlevi destekler. Her iş ve [kusto sorgu diline (KQL)](/azure/kusto/query/index)göre çalışır. Kaynak Graph tarafından kullanılan sorgu dili hakkında bilgi edinmek için, [KQL öğreticisi](/azure/kusto/query/tutorial)ile başlayın.

Bu makalede kaynak Graph tarafından desteklenen dil bileşenleri ele alınmaktadır:

- [Kaynak grafik tabloları](#resource-graph-tables)
- [Desteklenen KQL dil öğeleri](#supported-kql-language-elements)
- [Kaçış karakterleri](#escape-characters)

## <a name="resource-graph-tables"></a>Kaynak grafik tabloları

Kaynak Grafiği Kaynak Yöneticisi kaynak türlerini ve bunların özelliklerini depolayan veriler için birkaç tablo sağlar. Bu tablolar, ilgili kaynak türlerinden özellikleri almak için `join` veya `union` işleçleriyle birlikte kullanılabilir. Kaynak grafiğinde kullanılabilen tabloların listesi aşağıda verilmiştir:

|Kaynak grafik tabloları |Açıklama |
|---|---|
|Kaynaklar |Sorguda tanımlanmazsa varsayılan tablo. Çoğu Kaynak Yöneticisi kaynak türü ve özelliği burada bulunur. |
|ResourceContainers |Abonelik (Önizleme--`Microsoft.Resources/subscriptions`) ve kaynak grubu (`Microsoft.Resources/subscriptions/resourcegroups`) kaynak türleri ve verileri içerir. |
|AlertsManagementResources |`Microsoft.AlertsManagement`_ilgili_ kaynakları içerir. |
|SecurityResources |`Microsoft.Security`_ilgili_ kaynakları içerir. |

> [!NOTE]
> _Kaynaklar_ varsayılan tablodur. _Kaynak_ tablosu sorgulanırken, `join` veya `union` kullanılmadığı sürece tablo adının sağlanması gerekmez. Ancak önerilen uygulama, her zaman sorguya ilk tabloyu dahil etmek için kullanılır.

Her tabloda hangi kaynak türlerinin kullanılabildiğini öğrenmek için portalda kaynak grafiği gezginini kullanın. Alternatif olarak, kaynak türlerinin bir listesini almak için `<tableName> | distinct type` gibi bir sorgu kullanın, bu kaynak grafik tablosu ortamınızda mevcut olan

Aşağıdaki sorguda basit bir `join`gösterilmektedir. Sorgu sonucu sütunları bir araya ve birleştirilmiş tablodaki tüm yinelenen sütun adlarını, bu örnekteki _Resourcecontainer_ 'leri **1**' de eklenmiş şekilde harmanlar. _Resourcecontainers_ tablosunda hem abonelikler hem de kaynak grupları için türler olduğundan, _kaynak tablosundan kaynağa_ katılması için her iki tür de kullanılabilir.

```kusto
Resources
| join ResourceContainers on subscriptionId
| limit 1
```

Aşağıdaki sorgu `join`daha karmaşık bir kullanımını gösterir. Sorgu, birleştirilmiş tabloyu abonelik kaynakları ile sınırlandırır ve `project` yalnızca özgün alan _SubscriptionID_ ve _ad_ alanı, _SubName_olarak yeniden adlandırılır. Alan yeniden adlandırma `join`, _kaynak_içinde zaten mevcut olduğundan, _name1_ olarak eklenmesini önler. Özgün tablo `where` ile filtrelenmiştir ve aşağıdaki `project` her iki tablodan sütun içerir. Sorgu sonucu, türünü, anahtar kasasının adını ve içindeki aboneliğin adını gösteren tek bir Anahtar Kasası.

```kusto
Resources
| where type == 'microsoft.keyvault/vaults'
| join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId
| project type, name, SubName
| limit 1
```

> [!NOTE]
> `join` sonuçları `join` `project`ile sınırlandırırken, yukarıdaki örnekteki SubscriptionID, yukarıdaki örnekteki _abonelik_ `project`dahil olmalıdır.

## <a name="supported-kql-language-elements"></a>Desteklenen KQL dil öğeleri

Kaynak Grafiği tüm KQL [veri türlerini](/azure/kusto/query/scalar-data-types/), [skaler işlevleri](/azure/kusto/query/scalarfunctions), [skaler işleçleri](/azure/kusto/query/binoperators)ve [toplama işlevlerini](/azure/kusto/query/any-aggfunction)destekler. Belirli [tablolu işleçler](/azure/kusto/query/queries) , bazılarının farklı davranışları olan kaynak Graph tarafından desteklenir.

### <a name="supported-tabulartop-level-operators"></a>Desteklenen tablolu/en üst düzey işleçler

Aşağıda belirli örneklere sahip kaynak Graph tarafından desteklenen KQL tablolu işleçlerinin listesi verilmiştir:

|KQL |Kaynak Grafiği örnek sorgusu |Notlar |
|---|---|---|
|[count](/azure/kusto/query/countoperator) |[Ana kasaları say](../samples/starter.md#count-keyvaults) | |
|[ayrı](/azure/kusto/query/distinctoperator) |[Belirli bir diğer ad için farklı değerleri göster](../samples/starter.md#distinct-alias-values) | |
|[genişletmeyi](/azure/kusto/query/extendoperator) |[Sanal makineleri işletim sistemi türüne göre sayma](../samples/starter.md#count-os) | |
|[join](/azure/kusto/query/joinoperator) |[Abonelik adı olan Anahtar Kasası](../samples/advanced.md#join) |Birleşim türleri desteklenir: [ınnerunique](/azure/kusto/query/joinoperator#default-join-flavor), [Inner](/azure/kusto/query/joinoperator#inner-join), [soltouter](/azure/kusto/query/joinoperator#left-outer-join). Tek bir sorgudaki 3 `join` limiti. Yayın katılımı gibi özel JOIN stratejilerine izin verilmez. Tek bir tablo içinde veya _kaynaklar_ Ile _resourcecontainers_ tabloları arasında kullanılabilir. |
|[sınırlı](/azure/kusto/query/limitoperator) |[Tüm genel IP adreslerini listele](../samples/starter.md#list-publicip) |`take` eş anlamlı |
|[MV-Genişlet](/azure/kusto/query/mvexpandoperator) |[Belirli yazma konumlarına sahip Cosmos DB listeleyin](../samples/advanced.md#mvexpand-cosmosdb) |_RowLimit_ maksimum 400 |
|[siparişi](/azure/kusto/query/orderoperator) |[Ada göre sıralanmış kaynakları listeleme](../samples/starter.md#list-resources) |`sort` eş anlamlı |
|[Proje](/azure/kusto/query/projectoperator) |[Ada göre sıralanmış kaynakları listeleme](../samples/starter.md#list-resources) | |
|[Proje-dışarıda](/azure/kusto/query/projectawayoperator) |[Sütunları sonuçlardan kaldır](../samples/advanced.md#remove-column) | |
|[düzenine](/azure/kusto/query/sortoperator) |[Ada göre sıralanmış kaynakları listeleme](../samples/starter.md#list-resources) |`order` eş anlamlı |
|[ölçütü](/azure/kusto/query/summarizeoperator) |[Azure kaynaklarını sayma](../samples/starter.md#count-resources) |Yalnızca Basitleştirilmiş ilk sayfa |
|[take](/azure/kusto/query/takeoperator) |[Tüm genel IP adreslerini listele](../samples/starter.md#list-publicip) |`limit` eş anlamlı |
|[Sayfanın Üstü](/azure/kusto/query/topoperator) |[Ada ve işletim sistemi türüne göre ilk beş sanal makineyi gösterme](../samples/starter.md#show-sorted) | |
|[union](/azure/kusto/query/unionoperator) |[İki sorgudan alınan sonuçları tek bir sonuç halinde birleştirin](../samples/advanced.md#unionresults) |Tek tablo izin verildi: _T_ `| union` \[`kind=` `inner`\|`outer`\] \[`withsource=`_ColumnName_\] _tablo_. Tek bir sorgudaki 3 `union` Let limiti. `union` bacak tablolarının benzer çözümüne izin verilmez. Tek bir tablo içinde veya _kaynaklar_ Ile _resourcecontainers_ tabloları arasında kullanılabilir. |
|[olmadığı](/azure/kusto/query/whereoperator) |[Depolama içeren kaynakları göster](../samples/starter.md#show-storage) | |

## <a name="escape-characters"></a>Kaçış karakterleri

`.` veya `$`dahil olanlar gibi bazı özellik adları, sorguda sarmalanmış veya kaçışlanmış ya da özellik adının yanlış yorumlanması ve beklenen sonuçları sağlamamalıdır.

- `.`-özellik adını şu şekilde kaydırın: `['propertyname.withaperiod']`
  
  OData özelliğini sarmalayan örnek sorgu _. tür_:

  ```kusto
  where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.['odata.type']
  ```

- `$`, özellik adındaki karakteri kaçış. Kullanılan kaçış karakteri, Shell kaynak grafiğine göre çalıştırılır.

  - **Bash** - `\`

    Bash içindeki özelliğin _\$türünü_ iptal eden örnek sorgu:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.\$type
    ```

  - **cmd** -`$` karakterini kaçış.

  - **PowerShell** - ``` ` ```

    PowerShell 'de _\$Özellik türünü_ iptal eden örnek sorgu:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.`$type
    ```

## <a name="next-steps"></a>Sonraki adımlar

- Bkz. [Başlangıç sorgularında](../samples/starter.md)kullanılan dil.
- Gelişmiş [sorgularda](../samples/advanced.md)gelişmiş kullanımlar bölümüne bakın.
- [Kaynakları araştırma](explore-resources.md)hakkında daha fazla bilgi edinin.