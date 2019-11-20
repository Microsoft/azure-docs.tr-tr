---
title: Büyük veri kümeleriyle çalışma
description: Azure Kaynak Grafında çalışırken büyük veri kümelerinin nasıl alınacağını ve kontrol olduğunu anlayın.
ms.date: 10/18/2019
ms.topic: conceptual
ms.openlocfilehash: a7258534b867c57f89eeb432b579e87070b4fc1d
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73959009"
---
# <a name="working-with-large-azure-resource-data-sets"></a>Büyük Azure Kaynak veri kümeleriyle çalışma

Azure Kaynak Grafiği, Azure ortamınızdaki kaynaklarla çalışmak ve bunlarla ilgili bilgi almak için tasarlanmıştır. Kaynak Grafiği, binlerce kaydı sorgularken bile bu verilerin hızlı bir şekilde alınmasını sağlar. Kaynak grafiğinde bu büyük veri kümeleriyle çalışmak için çeşitli seçenekler bulunur.

Yüksek bir sıklıkta sorgularla çalışmaya ilişkin yönergeler için bkz. [Kısıtlanmış istekler Için rehberlik](./guidance-for-throttled-requests.md).

## <a name="data-set-result-size"></a>Veri kümesi sonuç boyutu

Varsayılan olarak, kaynak Graph tüm sorgular yalnızca **100** kayıt döndürüyor şekilde kısıtlar. Bu denetim, büyük veri kümelerine yol açacak istemeden yapılan sorgulardan hem kullanıcıyı hem de hizmeti korur. Bu olay çoğu zaman bir müşteri, kaynakları belirli ihtiyaçlarına uygun şekilde bulmak ve filtrelemek için sorguları deneydiğinde oluşur. Bu denetim, sonuçları sınırlamak için [üst](/azure/kusto/query/topoperator) veya Azure Veri Gezgini Dil işleçlerini [sınırlandırtan](/azure/kusto/query/limitoperator) farklıdır.

> [!NOTE]
> **İlk**kullanıldığında, sonuçları `asc` veya `desc`en az bir sütuna göre sıralamak önerilir. Sıralama yapmadan döndürülen sonuçlar rastgele ve tekrarlanabilir değildir.

Varsayılan sınır, kaynak Graph ile etkileşimde bulunmak için tüm yöntemler aracılığıyla geçersiz kılınabilir. Aşağıdaki örneklerde veri kümesi boyut sınırının _200_olarak nasıl değiştirileceği gösterilmektedir:

```azurecli-interactive
az graph query -q "Resources | project name | order by name asc" --first 200 --output table
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project name | order by name asc" -First 200
```

[REST API](/rest/api/azureresourcegraph/resourcegraph(2018-09-01-preview)/resources/resources), Denetim **$top** ve **queryrequestoptions**'ın bir parçasıdır.

_En kısıtlayıcı_ olan denetim kazanacaktır. Örneğin, sorgunuz **top** veya **limit** işleçlerini kullanıyorsa ve **ilk**olarak daha fazla kayıt ile sonuçlanacaksa, döndürülen en fazla kayıt sayısı **birincisine**eşittir. Benzer şekilde, **top** veya **limit** **birinciden**küçükse, döndürülen kayıt kümesi **top** veya **limit**tarafından yapılandırılan daha küçük bir değer olacaktır.

**Önce** Şu anda izin verilen en fazla _5000_değeri vardır.

## <a name="skipping-records"></a>Kayıtlar atlanıyor

Büyük veri kümeleriyle çalışma için sonraki seçenek, **atlama** denetimidir. Bu denetim, sorgunuzun sonuçları döndürmeden önce tanımlanan kayıt sayısını atlamasını veya atlamasını sağlar. **Skip** , sonuçları, sonuç kümesinin ortasında bir yerde bir yere alacağınız anlamlı bir şekilde sıralayan sorgular için yararlıdır. Gereken sonuçlar döndürülen veri kümesinin sonunda ise, farklı bir sıralama yapılandırması kullanmak ve bunun yerine veri kümesinin en üstünden sonuçları almak daha etkilidir.

> [!NOTE]
> **Atla**kullanırken, sonuçları `asc` veya `desc`en az bir sütuna göre sıralamak önerilir. Sıralama yapmadan döndürülen sonuçlar rastgele ve tekrarlanabilir değildir.

Aşağıdaki örneklerde, bir sorgunun neden olacağı ilk _10_ kaydın nasıl atlanacağını, bunun yerine 11. kayıt ile döndürülen sonuç kümesini başlatmak gösterilmektedir:

```azurecli-interactive
az graph query -q "Resources | project name | order by name asc" --skip 10 --output table
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project name | order by name asc" -Skip 10
```

[REST API](/rest/api/azureresourcegraph/resourcegraph(2018-09-01-preview)/resources/resources), Denetim **$Skip** ve **queryrequestoptions**'ın bir parçasıdır.

## <a name="paging-results"></a>Disk belleği sonuçları

Bir sonuç kümesini işlenmek üzere daha küçük kayıt kümelerine bölmek gerektiğinde veya bir sonuç kümesi, döndürülen en fazla _1000_ kayıt değerini aşacağından, sayfalama kullanın. [REST API](/rest/api/azureresourcegraph/resourcegraph(2018-09-01-preview)/resources/resources) **queryresponse** , bir sonuç kümesinin parçalanmış olduğunu belirten değerler sağlar: **resultkesildi** ve **$skipToken**.
**Resultkesildi** , yanıtta ek kayıtlar döndürülmediğinde tüketiciyi bildiren bir Boole değeridir. **Count** özelliği **totalRecords** özelliğinden daha az olduğunda bu durum da tanımlanabilir. **totalRecords** sorguyla eşleşen kaç kayıt olduğunu tanımlar.

**Resultkesilme** **değeri true**olduğunda **$skipToken** özelliği yanıtta ayarlanır. Bu değer, sorguyla eşleşen bir sonraki kayıt kümesini almak için aynı sorgu ve abonelik değerleriyle birlikte kullanılır.

Aşağıdaki örneklerde, ilk 3000 kaydın nasıl atlanması ve bu kayıtlar Azure CLı ile atlandıktan sonra **ilk** 1000 kayıtlarının nasıl **döndürüleceği** ve Azure PowerShell gösterilmektedir:

```azurecli-interactive
az graph query -q "Resources | project id, name | order by id asc" --first 1000 --skip 3000
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project id, name | order by id asc" -First 1000 -Skip 3000
```

> [!IMPORTANT]
> Sayfalama 'un çalışması için sorgunun **ID** alanını **projesi** gerekir. Sorguda eksik ise, yanıt **$skipToken**içermez.

Bir örnek için, REST API belgeleri içindeki [Sonraki sayfa sorgusuna](/rest/api/azureresourcegraph/resourcegraph(2018-09-01-preview)/resources/resources#next-page-query) bakın.

## <a name="formatting-results"></a>Biçimlendirme sonuçları

Kaynak Grafiği sorgusunun sonuçları iki biçimde, _tablo_ ve _objectarray_olarak sağlanır. Biçim, istek seçeneklerinin bir parçası olarak **RESULTFORMAT** parametresi ile yapılandırılır. _Tablo_ biçimi, **RESULTFORMAT**için varsayılan değerdir.

Azure CLı sonuçları JSON 'da varsayılan olarak sağlanır. Azure PowerShell sonuçları, varsayılan olarak bir **PSCustomObject** ' dir, ancak `ConvertTo-Json` cmdlet 'i kullanılarak hızlı BIR şekilde JSON 'a dönüştürülebilirler. Diğer SDK 'lar için, sorgu sonuçları _Objectarray_ biçiminin çıktısı olacak şekilde yapılandırılabilir.

### <a name="format---table"></a>Biçim-Tablo

Varsayılan biçim, _tablo_, sorgu tarafından döndürülen özelliklerin sütun tasarımını ve satır değerlerini vurgulamak için tasarlanan bir JSON biçiminde sonuçları döndürür. Bu biçim, yapılandırılmış bir tabloda veya bir elektronik tabloda, ilk olarak tanımlanan sütunları ve sonra bu sütunlara hizalanmış verileri temsil eden her bir satır için tanımlanan verilere benzer.

_Tablo_ biçimlendirmesiyle bir sorgu sonucu örneği aşağıda verilmiştir:

```json
{
    "totalRecords": 47,
    "count": 1,
    "data": {
        "columns": [{
                "name": "name",
                "type": "string"
            },
            {
                "name": "type",
                "type": "string"
            },
            {
                "name": "location",
                "type": "string"
            },
            {
                "name": "subscriptionId",
                "type": "string"
            }
        ],
        "rows": [
            [
                "veryscaryvm2-nsg",
                "microsoft.network/networksecuritygroups",
                "eastus",
                "11111111-1111-1111-1111-111111111111"
            ]
        ]
    },
    "facets": [],
    "resultTruncated": "true"
}
```

### <a name="format---objectarray"></a>Format-ObjectArray

_Objectarray_ BIÇIMI sonuçları JSON biçiminde de döndürür. Ancak, bu tasarım, sütun ve satır verilerinin dizi gruplarında eşleştiği JSON 'da ortak olan anahtar/değer çifti ilişkisine hizalanır.

_Objectarray_ biçimlendirmesiyle bir sorgu sonucu örneği aşağıda verilmiştir:

```json
{
    "totalRecords": 47,
    "count": 1,
    "data": [{
        "name": "veryscaryvm2-nsg",
        "type": "microsoft.network/networksecuritygroups",
        "location": "eastus",
        "subscriptionId": "11111111-1111-1111-1111-111111111111"
    }],
    "facets": [],
    "resultTruncated": "true"
}
```

İşte, _Objectarray_ biçimini kullanmak Için **RESULTFORMAT** ayarlamanın bazı örnekleri şunlardır:

```csharp
var requestOptions = new QueryRequestOptions( resultFormat: ResultFormat.ObjectArray);
var request = new QueryRequest(subscriptions, "Resources | limit 1", options: requestOptions);
```

```python
request_options = QueryRequestOptions(
    result_format=ResultFormat.object_array
)
request = QueryRequest(query="Resources | limit 1", subscriptions=subs_list, options=request_options)
response = client.resources(request)
```

## <a name="next-steps"></a>Sonraki adımlar

- Bkz. [Başlangıç sorgularında](../samples/starter.md)kullanılan dil.
- Gelişmiş [sorgularda](../samples/advanced.md)gelişmiş kullanımlar bölümüne bakın.
- [Kaynakları araştırma](explore-resources.md)hakkında daha fazla bilgi edinin.