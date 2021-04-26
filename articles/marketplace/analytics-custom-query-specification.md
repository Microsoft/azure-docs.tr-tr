---
title: Özel sorgu belirtimi
description: Microsoft ticari Market 'teki tekliflerinizin analiz tablolarından verileri program aracılığıyla ayıklamak için özel sorguları nasıl kullanacağınızı öğrenin.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 4be063342a6c46d73c86f2d9dff1da5395328389
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102584201"
---
# <a name="custom-query-specification"></a>Özel sorgu belirtimi

İş ortakları bu sorgu belirtimini, analiz tablolarından veri ayıklama için özel sorguları kolayca formülleştirmek için kullanabilir. Sorgular yalnızca istenen sütunları ve belirli bir ölçütle eşleşen ölçümleri seçmek için kullanılabilir. Dil belirtiminin kalmasıyla, özel bir sorgunun yazılabileceği veri kümesi tanımıdır.

## <a name="datasets"></a>Veri kümeleri

Tablo ve sütun içeren bir veritabanında bazı sorguların çalıştırıldığı şekilde, özel bir sorgu sütunları ve ölçümleri olan veri kümelerinde çalışır. Sorgu oluşturmak için kullanılabilir veri kümelerinin tam listesi, veri kümeleri API 'SI kullanılarak elde edilebilir.

JSON olarak gösterilen veri kümesine bir örnek aşağıda verilmiştir.

```json
{
     "datasetName": "ISVUsage",
     "selectableColumns": [
       "MarketplaceSubscriptionId",
       "OfferName",
            "CustomerId",
     "MonthStartDate",
     "SKU"
    ],
    "availableMetrics": [
     "NormalizedUsage", 
     "RawUsage"
        "EstimatedExtendedChargeCC"
    ],
    "availableDateRanges": [
     "LAST_MONTH",
     "LAST_3_MONTHS",
     "LAST_6_MONTHS",
     "LIFETIME"
    ]
}
```

### <a name="parts-of-a-dataset"></a>Veri kümesinin parçaları

- Veri kümesi adı, veritabanı tablosu adı gibidir. Örneğin, ISVUsage. Veri kümesinde, Marketplacesubscriptionıd gibi seçilebilir bir sütun listesi bulunur.
- Bir veri kümesinde ayrıca bir veritabanında toplama işlevleri gibi ölçümler de vardır. Örneğin NormalizedUsage.
- Verilerin verilebileceği sabit zaman yaymalar vardır.

### <a name="formulating-a-query-on-a-dataset"></a>Bir veri kümesindeki sorguyu formülleyici

Bunlar çeşitli veri türlerinin nasıl ayıklanacağını gösteren bazı örnek sorgulardır.

| Sorgu | Description |
| ------------ | ------------- |
| **Seç** Pazar Placesubscriptionıd, ISVUsage TIMESPAN **'Tan** CustomerID **LAST_MONTH** | Bu sorgu, `MarketplaceSubscriptionId` Son 1 ayda her benzersiz ve karşılık gelen her benzersiz alır `CustomerId` . |
| **Seç** Pazar Placesubscriptionıd, EstimatedExtendedChargeCC **öğesinden** ISVUsage **Order** EstimatedExtendedChargeCC **sınır** 10 | Bu sorgu, her abonelik kapsamında satılan lisansların sayısını azalan sırada en iyi 10 aboneliği alacak. |
| **Seç** CustomerID, NormalizedUsage, ISVUsage order **'dan** normalizedusage **, normalizedusage** > 100000 **sırası** normalizedusage zaman LAST_6_MONTHS **aralığı**  | Bu sorgu, 100.000 'den daha büyük bir normalleştirilmiş kullanımı olan tüm müşterilerin NormalizedUsage ve RawUsage sürümlerini alır. |
| **Seç** Marketplacesubscriptionıd, MonthStartDate, NormalizedUsage **,** ISVUsage **WHERE** CustomerID ın (' 2a31c234-1f4e-4c60-909e-76d234f93161 ', ' 80780748-3f9a-11eb-b378-0242ac130002 ') | Bu sorgu, `MarketplaceSubscriptionId` iki değere göre her ay için oluşturulan geliri ve gelir elde edecektir `CustomerId` : `2a31c234-1f4e-4c60-909e-76d234f93161` ve `80780748-3f9a-11eb-b378-0242ac130002` . |
|||

## <a name="query-specification"></a>Sorgu belirtimi

Bu bölümde sorgu tanımı ve yapısı açıklanmaktadır.

### <a name="grammar-reference"></a>Dilbilgisi başvurusu

Bu tablo sorgularda kullanılan sembolleri açıklar.

| Sembol | Anlamı |
| ------------ | ------------- |
| ? | İsteğe Bağlı |
| * | Sıfır veya daha fazla |
| + | Bir veya daha fazla |
| &#124; | Veya/bir liste |
| | |

### <a name="query-definition"></a>Sorgu tanımı

Sorgu deyimi şu yan tümceleri içeriyor: SelectClause, FromClause, WhereClause?, OrderClause?, LimitClause? ve TimeSpan?.

- **Selectclause**: ColumOrMetricName (, ColumOrMetricName) * öğesini **seçin**
    - **ColumOrMetricName**: veri kümesi içinde tanımlanan sütunlar ve ölçümler
- **FromClause**: DataSetName **kaynağından**
    - **DataSetName**: veri kümesi içinde tanımlanan veri kümesi adı
- **WhereClause**: **burada** FilterCondition (**ve** FilterCondition) *
    - **FilterCondition**: ColumOrMetricName işleç değeri
        - **İşleç**: = | > | < | >= | <= |! = | BEĞENDINIZ | BEĞENMIYOR | | IÇINDE NOT ıN
        - **Değer**: sayı | StringLiteral | MultiNumberList | MultiStringList 
            - **Numara**:-? [0-9] + (. [0-9] [0-9] *)?
            - **Stringliteral**: ' [a-Za-z0-9_] * '
            - **MultiNumberList**: (sayı (, sayı) *)
            - **Multistringlist**: (stringliteral (, stringliteral) *)
- **Orderclause**: **Order Condition 'a göre sıralama** (, ordercondition) *
    - **Ordercondition**: ColumOrMetricName (**ASC**  |  **DESC**) *
    - **Limitclause**: **LIMIT** [0-9] +
    - **TimeSpan**: **TimeSpan** (bugün | DÜN | LAST_7_DAYS | LAST_14_DAYS | LAST_30_DAYS | LAST_90_DAYS | LAST_180_DAYS | LAST_365_DAYS | LAST_MONTH | LAST_3_MONTHS | LAST_6_MONTHS | LAST_1_YEAR | SÜRE

### <a name="query-structure"></a>Sorgu yapısı

Rapor sorgusu birden çok bölümden oluşur:

- SELECT
- FROM
- WHERE
- SİPARİŞ VEREN
- LIMIT
- TIMESPAN

Her bölüm aşağıda açıklanmıştır.

#### <a name="select"></a>SELECT

Sorgunun bu bölümü, verilecek sütunları belirtir. Seçilebilir olan sütunlar, içinde listelenen alanlardır `selectableColumns` ve `availableMetrics` bir veri kümesinin bölümlerinde yer alabilir. Son içe aktarılmış satırlar her zaman seçili sütunlarda ayrı değerler içerecektir. Örneğin, dışarıya aktarılmış dosyada yinelenen satır olmayacaktır. Ölçümler, Seçili sütunların her farklı birleşimi için hesaplanır.

**Örnek**:
- **Seç** `OfferName` , `NormalizedUsage`

#### <a name="from"></a>FROM

Sorgunun bu bölümü, verilerin verilmesi gereken veri kümesini gösterir. Burada verilen veri kümesi adının, veri kümeleri API 'SI tarafından döndürülen geçerli bir veri kümesi adı olması gerekir.

**Örnek**:
- Kaynak `ISVUsage`
- Kaynak `ISVOrder`

#### <a name="where"></a>WHERE

Sorgunun bu bölümü, veri kümesindeki Filtre koşullarını belirtmek için kullanılır. Yalnızca bu yan tümce içinde listelenen tüm koşullara uyan satırlar, son verilen dosyada mevcut olacaktır. Filtre koşulu ve ' de listelenen sütunlardan herhangi birinde olabilir `selectableColumns` `availableMetrics` . Filtre koşulunda belirtilen değerler, yalnızca işleci veya olduğunda bir dize listesi veya dizeler listesi olabilir `IN` `NOT IN` . Değerler her zaman sabit bir dize olarak verilebilir ve bunlar yerel sütun türlerine dönüştürülecektir. Birden çok filtre koşullarının bir işlemle ayrılması gerekir `AND` .

**Örnek**:

- Marketplacesubscriptionıd = ' 868368dav-957d-4959-8992-3c12dc7e6260 '
- '% Contosso% ' **gibi** CustomerName
- CustomerID **Içinde değil** (1000, 1001, 1002)
- OrderQuantity = 100
- OrderQuantity = ' 100 '
    - Marketplacesubscriptionıd = ' 7b487ac0-CE12-b732-dcd6-91a1e4e74a50 ' ve CustomerID = ' 0f8b7fa0-eb83-A183-1225-ca153ef807aa '

#### <a name="order-by"></a>SİPARİŞ VEREN

Sorgunun bu kısmı, aktarılmış satırlar için sıralama ölçütlerini belirtir. Sıralamanın tanımlanbileceği sütunların, `selectableColumns` veri kümesinin ve öğesinden olması gerekir `availableMetrics` . Sıralama yönü belirtilmemişse, sütununda Varsayılan olarak ayarlanır `DESC` . Sıralama, ölçütü virgülle ayırarak birden çok sütunda tanımlanabilir.

**Örnek**:

- **SıRALAMA ölçütü** NormalizedUsage **ASC**, estimatedextendedücret (CC) **DESC**
- **SıRALAMA ölçütü** CustomerName **ASC**, normalizedusage

#### <a name="limit"></a>LIMIT

Sorgunun bu bölümü, verilecek satır sayısını belirtir. Belirttiğiniz sayının pozitif sıfır dışında bir tamsayı olması gerekir.

#### <a name="timespan"></a>TIMESPAN

Sorgunun bu bölümü, verilerin verilmesi gereken süreyi belirtir. Olası değerler `availableDateRanges` veri kümesi tanımındaki alanından olmalıdır.

### <a name="case-sensitivity-in-query-specification"></a>Sorgu belirtiminde büyük/küçük harf duyarlılığı

Belirtim tamamen büyük/küçük harfe duyarlıdır. Önceden tanımlanmış anahtar sözcükler, sütun adları ve değerler büyük veya küçük harf kullanılarak belirtilebilir.

## <a name="see-also"></a>Ayrıca bkz.

- [Sistem sorgularının listesi](analytics-system-queries.md)
