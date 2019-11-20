---
title: Azure blok zinciri çalışma ekranı yapılandırma başvurusu
description: Azure blok zinciri çalışma ekranı önizleme uygulama yapılandırmasına genel bakış.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 09/05/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: brendal
manager: femila
ms.openlocfilehash: 1c737106b47b95fcc6d1abdadc81398a3bc9256d
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/10/2019
ms.locfileid: "70845105"
---
# <a name="azure-blockchain-workbench-configuration-reference"></a>Azure blok zinciri çalışma ekranı yapılandırma başvurusu

Azure blok zinciri çalışma ekranı uygulamaları, yapılandırma meta verileri ve akıllı sözleşme kodu tarafından tanımlanan çok taraflı iş akışlarıdır. Yapılandırma meta verileri, blok zinciri uygulamasının üst düzey iş akışlarını ve etkileşim modelini tanımlar. Akıllı sözleşmeler, blok zinciri uygulamasının iş mantığını tanımlar. Çalışma ekranı, blok zinciri uygulaması kullanıcı deneyimleri oluşturmak için yapılandırma ve akıllı sözleşme kodunu kullanır.

Yapılandırma meta verileri her blok zinciri uygulaması için aşağıdaki bilgileri belirtir:

* Blok zinciri uygulamasının adı ve açıklaması
* Blok zinciri uygulamasına davranabilir veya katılabilen kullanıcılar için benzersiz roller
* Bir veya daha fazla iş akışı. Her iş akışı, iş mantığının akışını denetlemek için bir durum makinesi işlevi görür. İş akışları birbirleriyle bağımsızdır veya birbirleriyle etkileşim kurabilir.

Tanımlı her iş akışı şunları belirtir:

* İş akışının adı ve açıklaması
* İş akışının durumları.  Her durum iş mantığının denetim akışındaki bir aşamadır. 
* Sonraki duruma geçiş eylemleri
* Her eylemi başlatmalarına izin verilen kullanıcı rolleri
* Kod dosyalarında iş mantığını temsil eden akıllı sözleşmeler

## <a name="application"></a>Uygulama

Bir blok zinciri uygulaması, yapılandırma meta verilerini, iş akışlarını ve uygulama içinde davranabilir veya katılabilen kullanıcı rollerini içerir.

| Alan | Description | Gerekli |
|-------|-------------|:--------:|
| ApplicationName | Benzersiz uygulama adı. İlgili akıllı sözleşmenin geçerli sözleşme sınıfı için aynı **ApplicationName** kullanması gerekir.  | Evet |
| DisplayName | Uygulamanın kolay görünen adı. | Evet |
| Description | Uygulamanın açıklaması. | Hayır |
| applicationRoles | [ApplicationRoles](#application-roles)koleksiyonu. Uygulama içinde davranabilir veya katılabilen kullanıcı rolleri.  | Evet |
| Workflows | [Iş akışlarının](#workflows)toplanması. Her iş akışı, iş mantığının akışını denetlemek için bir durum makinesi işlevi görür. | Evet |

Bir örnek için bkz. [yapılandırma dosyası örneği](#configuration-file-example).

## <a name="workflows"></a>Workflows

Bir uygulamanın iş mantığı, bir eylem gerçekleşmeden bir durum makinesi olarak modellenebilir ve iş mantığının akışının bir durumdan diğerine taşınmasına neden olur. Bir iş akışı, bu tür durumlar ve eylemlerin bir koleksiyonudur. Her iş akışı, kod dosyalarındaki iş mantığını temsil eden bir veya daha fazla akıllı sözleşmeden oluşur. Yürütülebilir bir sözleşme, bir iş akışının örneğidir.

| Alan | Description | Gerekli | En fazla uzunluk |
|-------|-------------|:--------:|-----------:|
| Name | Benzersiz iş akışı adı. İlgili akıllı sözleşmenin ilgili sözleşme sınıfı için aynı **adı** kullanması gerekir. | Evet | 50 |
| DisplayName | İş akışının kolay görünen adı. | Evet | 255 |
| Description | İş akışının açıklaması. | Hayır | 255 |
| Initiators | [ApplicationRoles](#application-roles)koleksiyonu. İş akışında sözleşme oluşturma yetkisine sahip kullanıcılara atanan roller. | Evet | |
| StartState | İş akışının ilk durumunun adı. | Evet | |
| Properties | [Tanımlayıcılar](#identifiers)koleksiyonu. Bir kullanıcı deneyimi aracında, zincir dışı veya görselleştirilmemiş verileri temsil eder. | Evet | |
| Constructor | İş akışının bir örneğini oluşturmak için giriş parametrelerini tanımlar. | Evet | |
| Functions | İş akışında yürütülebilecek [işlevlerin](#functions) bir koleksiyonu. | Evet | |
| States | İş akışı [durumları](#states)koleksiyonu. | Evet | |

Bir örnek için bkz. [yapılandırma dosyası örneği](#configuration-file-example).

## <a name="type"></a>Type

Desteklenen veri türleri.

| Type | Description |
|-------|-------------|
| address  | *Sözleşmeler* veya *Kullanıcılar*gibi blok zinciri adres türü. |
| array    | Tamsayı, bool, para veya saat türünde tek düzeyli dizi. Diziler statik veya dinamik olabilir. Dizide bulunan öğelerin veri türünü belirtmek için **ElementType** kullanın. Bkz. [örnek yapılandırma](#example-configuration-of-type-array). |
| bool     | Boole veri türü. |
| contract | Sözleşme türü adresi. |
| enum     | Adlandırılmış değerler kümesi numaralandırılıyor. Sabit listesi türünü kullanırken, EnumValues listesini de belirtirsiniz. Her değer 255 karakterle sınırlıdır. Geçerli değer karakterleri, büyük ve küçük harfler (A-Z, A-z) ve sayılar (0-9) içerir. Bkz. [Solidity 'de örnek yapılandırma ve kullanım](#example-configuration-of-type-enum). |
| int      | Tamsayı veri türü. |
| money    | Para veri türü. |
| state    | İş akışı durumu. |
| dize  | Dize veri türü. maksimum 4000 karakter. Bkz. [örnek yapılandırma](#example-configuration-of-type-string). |
| user     | Kullanıcı türü adresi. |
| time     | Saat veri türü. |
|`[ Application Role Name ]`| Uygulama rolünde belirtilen ad. Kullanıcıları bu rol türünde olacak şekilde sınırlandırır. |

### <a name="example-configuration-of-type-array"></a>Dizi türünde örnek yapılandırma

```json
{
  "Name": "Quotes",
  "Description": "Market quotes",
  "DisplayName": "Quotes",
  "Type": {
    "Name": "array",
    "ElementType": {
        "Name": "int"
    }
  }
}
```

#### <a name="using-a-property-of-type-array"></a>Dizi türünde bir özelliği kullanma

Yapılandırmada tür dizisi olarak bir özellik tanımlarsanız, Solidity içinde dizi türünün public özelliğini döndürmek için bir açık get işlevi eklemeniz gerekir. Örneğin:

```
function GetQuotes() public constant returns (int[]) {
     return Quotes;
}
```

### <a name="example-configuration-of-type-string"></a>Tür dizesinin örnek yapılandırması

``` json
{
  "Name": "description",
  "Description": "Descriptive text",
  "DisplayName": "Description",
  "Type": {
    "Name": "string"
  }
}
```

### <a name="example-configuration-of-type-enum"></a>Enum türünde örnek yapılandırma

``` json
{
  "Name": "PropertyType",
  "DisplayName": "Property Type",
  "Description": "The type of the property",
  "Type": {
    "Name": "enum",
    "EnumValues": ["House", "Townhouse", "Condo", "Land"]
  }
}
```

#### <a name="using-enumeration-type-in-solidity"></a>Solidity içinde numaralandırma türü kullanma

Yapılandırmada bir numaralandırma tanımlandıktan sonra, Solidity içinde numaralandırma türlerini kullanabilirsiniz. Örneğin, PropertyTypeEnum adlı bir sabit listesi tanımlayabilirsiniz.

```
enum PropertyTypeEnum {House, Townhouse, Condo, Land} PropertyTypeEnum public PropertyType; 
```

Dizelerin listesi, yapılandırma ve akıllı sözleşme arasında geçerli ve tutarlı bildirimler ile blok zinciri çalışma ekranı arasında eşleşmesi gerekir.

Atama örneği:

```
PropertyType = PropertyTypeEnum.Townhouse;
```

İşlev parametresi örneği: 

``` 
function AssetTransfer(string description, uint256 price, PropertyTypeEnum propertyType) public
{
    InstanceOwner = msg.sender;
    AskingPrice = price;
    Description = description;
    PropertyType = propertyType;
    State = StateType.Active;
    ContractCreated();
}

```

## <a name="constructor"></a>Constructor

Bir iş akışı örneği için giriş parametrelerini tanımlar.

| Alan | Description | Gerekli |
|-------|-------------|:--------:|
| Parametreler | Akıllı sözleşme başlatmak için gereken [tanımlayıcıların](#identifiers) toplanması. | Evet |

### <a name="constructor-example"></a>Oluşturucu örneği

``` json
{
  "Parameters": [
    {
      "Name": "description",
      "Description": "The description of this asset",
      "DisplayName": "Description",
      "Type": {
        "Name": "string"
      }
    },
    {
      "Name": "price",
      "Description": "The price of this asset",
      "DisplayName": "Price",
      "Type": {
        "Name": "money"
      }
    }
  ]
}
```

## <a name="functions"></a>Functions

İş akışında yürütülebilecek işlevleri tanımlar.

| Alan | Description | Gerekli | En fazla uzunluk |
|-------|-------------|:--------:|-----------:|
| Name | İşlevin benzersiz adı. İlgili akıllı sözleşmenin, uygulanabilir işlev için aynı **adı** kullanması gerekir. | Evet | 50 |
| DisplayName | İşlevin kolay görünen adı. | Evet | 255 |
| Description | İşlevin açıklaması | Hayır | 255 |
| Parametreler | İşlevin parametrelerine karşılık gelen [tanımlayıcıların](#identifiers) koleksiyonu. | Evet | |

### <a name="functions-example"></a>İşlevler örneği

``` json
"Functions": [
  {
    "Name": "Modify",
    "DisplayName": "Modify",
    "Description": "Modify the description/price attributes of this asset transfer instance",
    "Parameters": [
      {
        "Name": "description",
        "Description": "The new description of the asset",
        "DisplayName": "Description",
        "Type": {
          "Name": "string"
        }
      },
      {
        "Name": "price",
        "Description": "The new price of the asset",
        "DisplayName": "Price",
        "Type": {
          "Name": "money"
        }
      }
    ]
  },
  {
    "Name": "Terminate",
    "DisplayName": "Terminate",
    "Description": "Used to cancel this particular instance of asset transfer",
    "Parameters": []
  }
]

```

## <a name="states"></a>States

Bir iş akışı içindeki benzersiz durumlar koleksiyonu. Her durum iş mantığının Denetim akışında bir adım yakalar. 

| Alan | Description | Gerekli | En fazla uzunluk |
|-------|-------------|:--------:|-----------:|
| Name | Durumun benzersiz adı. İlgili akıllı sözleşmenin geçerli durum için aynı **adı** kullanması gerekir. | Evet | 50 |
| DisplayName | Durumun kolay görünen adı. | Evet | 255 |
| Description | Durumun açıklaması. | Hayır | 255 |
| Tamamlanmatamam | İş mantığı denetim akışındaki ilerlemeyi göstermek için blok zinciri çalışma ekranı Kullanıcı arabiriminde görünen bir tamsayı değeri. | Evet | |
| Stil | Durumun başarı veya başarısızlık durumunu temsil edip etmediğini belirten görsel ipucu. İki geçerli değer vardır: `Success` veya. `Failure` | Evet | |
| Lere | Geçerli durumdan sonraki durum kümesine kullanılabilir [geçişlerin](#transitions) toplanması. | Hayır | |

### <a name="states-example"></a>Durumlar örneği

``` json
"States": [
    {
      "Name": "Active",
      "DisplayName": "Active",
      "Description": "The initial state of the asset transfer workflow",
      "PercentComplete": 20,
      "Style": "Success",
      "Transitions": [
        {
          "AllowedRoles": [],
          "AllowedInstanceRoles": [ "InstanceOwner" ],
          "Description": "Cancels this instance of asset transfer",
          "Function": "Terminate",
          "NextStates": [ "Terminated" ],
          "DisplayName": "Terminate Offer"
        },
        {
          "AllowedRoles": [ "Buyer" ],
          "AllowedInstanceRoles": [],
          "Description": "Make an offer for this asset",
          "Function": "MakeOffer",
          "NextStates": [ "OfferPlaced" ],
          "DisplayName": "Make Offer"
        },
        {
          "AllowedRoles": [],
          "AllowedInstanceRoles": [ "InstanceOwner" ],
          "Description": "Modify attributes of this asset transfer instance",
          "Function": "Modify",
          "NextStates": [ "Active" ],
          "DisplayName": "Modify"
        }
      ]
    },
    {
      "Name": "Accepted",
      "DisplayName": "Accepted",
      "Description": "Asset transfer process is complete",
      "PercentComplete": 100,
      "Style": "Success",
      "Transitions": []
    },
    {
      "Name": "Terminated",
      "DisplayName": "Terminated",
      "Description": "Asset transfer has been canceled",
      "PercentComplete": 100,
      "Style": "Failure",
      "Transitions": []
    }
  ]
```

## <a name="transitions"></a>Lere

Sonraki duruma yönelik kullanılabilir eylemler. Bir veya daha fazla kullanıcı rolü her durumda bir eylem gerçekleştirebilir, burada bir eylem iş akışındaki bir durumu başka bir duruma geçirebilir. 

| Alan | Description | Gerekli |
|-------|-------------|:--------:|
| AllowedRoles | Geçişi başlatmalarına izin verilen uygulama rollerinin listesi. Belirtilen rolün tüm kullanıcıları eylemi gerçekleştirebilir. | Hayır |
| Allowedınstanceroles | Geçişi başlatmalarına izin verilen akıllı sözleşmeye katılan veya belirtilen kullanıcı rollerinin listesi. Örnek rolleri iş akışlarının içindeki **özelliklerde** tanımlanmıştır. Allowedınstanceroles, akıllı bir sözleşmenin bir örneğine katılan bir kullanıcıyı temsil eder. Allowedınstanceroles, bir eylemi bir sözleşme örneğinde Kullanıcı rolüne göre kısıtlama olanağı sağlar.  Örneğin, rolü AllowedRoles ' de belirtmeniz durumunda, rolü (InstanceOwner) oluşturan kullanıcının rol türü (sahip) içindeki tüm kullanıcılar yerine sonlanmasına izin vermek isteyebilirsiniz. | Hayır |
| DisplayName | Geçişin kolay görünen adı. | Evet |
| Description | Geçişin açıklaması. | Hayır |
| İşlev | Geçişi başlatacak işlevin adı. | Evet |
| NextStates | Başarılı bir geçişten sonra olası bir sonraki durumlardan oluşan bir koleksiyon. | Evet |

### <a name="transitions-example"></a>Geçişler örneği

``` json
"Transitions": [
  {
    "AllowedRoles": [],
    "AllowedInstanceRoles": [ "InstanceOwner" ],
    "Description": "Cancels this instance of asset transfer",
    "Function": "Terminate",
    "NextStates": [ "Terminated" ],
    "DisplayName": "Terminate Offer"
  },
  {
    "AllowedRoles": [ "Buyer" ],
    "AllowedInstanceRoles": [],
    "Description": "Make an offer for this asset",
    "Function": "MakeOffer",
    "NextStates": [ "OfferPlaced" ],
    "DisplayName": "Make Offer"
  },
  {
    "AllowedRoles": [],
    "AllowedInstanceRoles": [ "InstanceOwner" ],
    "Description": "Modify attributes of this asset transfer instance",
    "Function": "Modify",
    "NextStates": [ "Active" ],
    "DisplayName": "Modify"
  }
]

```

## <a name="application-roles"></a>Uygulama rolleri

Uygulama rolleri, uygulama içinde çalışmak veya katılmak isteyen kullanıcılara atanabilecek bir rol kümesi tanımlar. Uygulama rolleri, eylemleri kısıtlamak ve blok zinciri uygulaması ile ilgili iş akışları arasında katılım sağlamak için kullanılabilir. 

| Alan | Description | Gerekli | En fazla uzunluk |
|-------|-------------|:--------:|-----------:|
| Name | Uygulama rolünün benzersiz adı. İlgili akıllı sözleşmenin ilgili rol için aynı **adı** kullanması gerekir. Temel tür adları ayrılmıştır. Aynı ada sahip bir uygulama rolünü [türle](#type) birlikte adı| Evet | 50 |
| Description | Uygulama rolünün açıklaması. | Hayır | 255 |

### <a name="application-roles-example"></a>Uygulama rolleri örneği

``` json
"ApplicationRoles": [
  {
    "Name": "Appraiser",
    "Description": "User that signs off on the asset price"
  },
  {
    "Name": "Buyer",
    "Description": "User that places an offer on an asset"
  }
]
```
## <a name="identifiers"></a>Tanımlayıcılar

Tanımlayıcılar, iş akışı özelliklerini, oluşturucuyu ve işlev parametrelerini anlatmak için kullanılan bir bilgi koleksiyonunu temsil eder. 

| Alan | Description | Gerekli | En fazla uzunluk |
|-------|-------------|:--------:|-----------:|
| Name | Özelliğin veya parametrenin benzersiz adı. İlgili akıllı sözleşmenin ilgili özellik veya parametre için aynı **adı** kullanması gerekir. | Evet | 50 |
| DisplayName | Özellik veya parametre için kolay görünen ad. | Evet | 255 |
| Description | Özelliğin veya parametrenin açıklaması. | Hayır | 255 |

### <a name="identifiers-example"></a>Tanımlayıcılar örneği

``` json
"Properties": [
  {
    "Name": "State",
    "DisplayName": "State",
    "Description": "Holds the state of the contract",
    "Type": {
      "Name": "state"
    }
  },
  {
    "Name": "Description",
    "DisplayName": "Description",
    "Description": "Describes the asset being sold",
    "Type": {
      "Name": "string"
    }
  }
]
```

## <a name="configuration-file-example"></a>Yapılandırma dosyası örneği

Varlık aktarımı, bir Inspector ve Appraiser gerektiren yüksek değerli varlıklar satın alıp satmaya yönelik bir akıllı sözleşme senaryosudur. Satıcılar bir varlık aktarımı akıllı sözleşmesi oluşturarak varlıklarını listeleyebilir. Alıcılar akıllı sözleşmede bir eylem gerçekleştirerek teklifleri gerçekleştirebilir ve diğer taraflar varlığı incelemek veya uygulamak için işlem gerçekleştirebilir. Varlık hem incelenen hem de apptetiklenir olarak işaretlendikten sonra, sözleşme tamamlanana kadar önce satıcı satışı yeniden onaylanır. İşlemdeki her bir noktada, tüm katılımcılar, güncelleştirildiği sırada sözleşmenin durumuna göre görünürlüğe sahiptir. 

Kod dosyaları dahil daha fazla bilgi için bkz. [Azure blok zinciri için varlık aktarım örneği çalışma ekranı](https://github.com/Azure-Samples/blockchain/tree/master/blockchain-workbench/application-and-smart-contract-samples/asset-transfer)

Aşağıdaki yapılandırma dosyası varlık aktarım örneği içindir:

``` json
{
  "ApplicationName": "AssetTransfer",
  "DisplayName": "Asset Transfer",
  "Description": "Allows transfer of assets between a buyer and a seller, with appraisal/inspection functionality",
  "ApplicationRoles": [
    {
      "Name": "Appraiser",
      "Description": "User that signs off on the asset price"
    },
    {
      "Name": "Buyer",
      "Description": "User that places an offer on an asset"
    },
    {
      "Name": "Inspector",
      "Description": "User that inspects the asset and signs off on inspection"
    },
    {
      "Name": "Owner",
      "Description": "User that signs off on the asset price"
    }
  ],
  "Workflows": [
    {
      "Name": "AssetTransfer",
      "DisplayName": "Asset Transfer",
      "Description": "Handles the business logic for the asset transfer scenario",
      "Initiators": [ "Owner" ],
      "StartState":  "Active",
      "Properties": [
        {
          "Name": "State",
          "DisplayName": "State",
          "Description": "Holds the state of the contract",
          "Type": {
            "Name": "state"
          }
        },
        {
          "Name": "Description",
          "DisplayName": "Description",
          "Description": "Describes the asset being sold",
          "Type": {
            "Name": "string"
          }
        },
        {
          "Name": "AskingPrice",
          "DisplayName": "Asking Price",
          "Description": "The asking price for the asset",
          "Type": {
            "Name": "money"
          }
        },
        {
          "Name": "OfferPrice",
          "DisplayName": "Offer Price",
          "Description": "The price being offered for the asset",
          "Type": {
            "Name": "money"
          }
        },
        {
          "Name": "InstanceAppraiser",
          "DisplayName": "Instance Appraiser",
          "Description": "The user that appraises the asset",
          "Type": {
            "Name": "Appraiser"
          }
        },
        {
          "Name": "InstanceBuyer",
          "DisplayName": "Instance Buyer",
          "Description": "The user that places an offer for this asset",
          "Type": {
            "Name": "Buyer"
          }
        },
        {
          "Name": "InstanceInspector",
          "DisplayName": "Instance Inspector",
          "Description": "The user that inspects this asset",
          "Type": {
            "Name": "Inspector"
          }
        },
        {
          "Name": "InstanceOwner",
          "DisplayName": "Instance Owner",
          "Description": "The seller of this particular asset",
          "Type": {
            "Name": "Owner"
          }
        }
      ],
      "Constructor": {
        "Parameters": [
          {
            "Name": "description",
            "Description": "The description of this asset",
            "DisplayName": "Description",
            "Type": {
              "Name": "string"
            }
          },
          {
            "Name": "price",
            "Description": "The price of this asset",
            "DisplayName": "Price",
            "Type": {
              "Name": "money"
            }
          }
        ]
      },
      "Functions": [
        {
          "Name": "Modify",
          "DisplayName": "Modify",
          "Description": "Modify the description/price attributes of this asset transfer instance",
          "Parameters": [
            {
              "Name": "description",
              "Description": "The new description of the asset",
              "DisplayName": "Description",
              "Type": {
                "Name": "string"
              }
            },
            {
              "Name": "price",
              "Description": "The new price of the asset",
              "DisplayName": "Price",
              "Type": {
                "Name": "money"
              }
            }
          ]
        },
        {
          "Name": "Terminate",
          "DisplayName": "Terminate",
          "Description": "Used to cancel this particular instance of asset transfer",
          "Parameters": []
        },
        {
          "Name": "MakeOffer",
          "DisplayName": "Make Offer",
          "Description": "Place an offer for this asset",
          "Parameters": [
            {
              "Name": "inspector",
              "Description": "Specify a user to inspect this asset",
              "DisplayName": "Inspector",
              "Type": {
                "Name": "Inspector"
              }
            },
            {
              "Name": "appraiser",
              "Description": "Specify a user to appraise this asset",
              "DisplayName": "Appraiser",
              "Type": {
                "Name": "Appraiser"
              }
            },
            {
              "Name": "offerPrice",
              "Description": "Specify your offer price for this asset",
              "DisplayName": "Offer Price",
              "Type": {
                "Name": "money"
              }
            }
          ]
        },
        {
          "Name": "Reject",
          "DisplayName": "Reject",
          "Description": "Reject the user's offer",
          "Parameters": []
        },
        {
          "Name": "AcceptOffer",
          "DisplayName": "Accept Offer",
          "Description": "Accept the user's offer",
          "Parameters": []
        },
        {
          "Name": "RescindOffer",
          "DisplayName": "Rescind Offer",
          "Description": "Rescind your placed offer",
          "Parameters": []
        },
        {
          "Name": "ModifyOffer",
          "DisplayName": "Modify Offer",
          "Description": "Modify the price of your placed offer",
          "Parameters": [
            {
              "Name": "offerPrice",
              "DisplayName": "Price",
              "Type": {
                "Name": "money"
              }
            }
          ]
        },
        {
          "Name": "Accept",
          "DisplayName": "Accept",
          "Description": "Accept the inspection/appraisal results",
          "Parameters": []
        },
        {
          "Name": "MarkInspected",
          "DisplayName": "Mark Inspected",
          "Description": "Mark the asset as inspected",
          "Parameters": []
        },
        {
          "Name": "MarkAppraised",
          "DisplayName": "Mark Appraised",
          "Description": "Mark the asset as appraised",
          "Parameters": []
        }
      ],
      "States": [
        {
          "Name": "Active",
          "DisplayName": "Active",
          "Description": "The initial state of the asset transfer workflow",
          "PercentComplete": 20,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Cancels this instance of asset transfer",
              "Function": "Terminate",
              "NextStates": [ "Terminated" ],
              "DisplayName": "Terminate Offer"
            },
            {
              "AllowedRoles": [ "Buyer" ],
              "AllowedInstanceRoles": [],
              "Description": "Make an offer for this asset",
              "Function": "MakeOffer",
              "NextStates": [ "OfferPlaced" ],
              "DisplayName": "Make Offer"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Modify attributes of this asset transfer instance",
              "Function": "Modify",
              "NextStates": [ "Active" ],
              "DisplayName": "Modify"
            }
          ]
        },
        {
          "Name": "OfferPlaced",
          "DisplayName": "Offer Placed",
          "Description": "Offer has been placed for the asset",
          "PercentComplete": 30,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Accept the proposed offer for the asset",
              "Function": "AcceptOffer",
              "NextStates": [ "PendingInspection" ],
              "DisplayName": "Accept Offer"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Reject the proposed offer for the asset",
              "Function": "Reject",
              "NextStates": [ "Active" ],
              "DisplayName": "Reject"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Cancel this instance of asset transfer",
              "Function": "Terminate",
              "NextStates": [ "Terminated" ],
              "DisplayName": "Terminate"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Rescind the offer you previously placed for this asset",
              "Function": "RescindOffer",
              "NextStates": [ "Active" ],
              "DisplayName": "Rescind Offer"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Modify the price that you specified for your offer",
              "Function": "ModifyOffer",
              "NextStates": [ "OfferPlaced" ],
              "DisplayName": "Modify Offer"
            }
          ]
        },
        {
          "Name": "PendingInspection",
          "DisplayName": "Pending Inspection",
          "Description": "Asset is pending inspection",
          "PercentComplete": 40,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Reject the offer",
              "Function": "Reject",
              "NextStates": [ "Active" ],
              "DisplayName": "Reject"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Cancel the offer",
              "Function": "Terminate",
              "NextStates": [ "Terminated" ],
              "DisplayName": "Terminate"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Rescind the offer you placed for this asset",
              "Function": "RescindOffer",
              "NextStates": [ "Active" ],
              "DisplayName": "Rescind Offer"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceInspector" ],
              "Description": "Mark this asset as inspected",
              "Function": "MarkInspected",
              "NextStates": [ "Inspected" ],
              "DisplayName": "Mark Inspected"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceAppraiser" ],
              "Description": "Mark this asset as appraised",
              "Function": "MarkAppraised",
              "NextStates": [ "Appraised" ],
              "DisplayName": "Mark Appraised"
            }
          ]
        },
        {
          "Name": "Inspected",
          "DisplayName": "Inspected",
          "PercentComplete": 45,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Reject the offer",
              "Function": "Reject",
              "NextStates": [ "Active" ],
              "DisplayName": "Reject"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Cancel the offer",
              "Function": "Terminate",
              "NextStates": [ "Terminated" ],
              "DisplayName": "Terminate"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Rescind the offer you placed for this asset",
              "Function": "RescindOffer",
              "NextStates": [ "Active" ],
              "DisplayName": "Rescind Offer"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceAppraiser" ],
              "Description": "Mark this asset as appraised",
              "Function": "MarkAppraised",
              "NextStates": [ "NotionalAcceptance" ],
              "DisplayName": "Mark Appraised"
            }
          ]
        },
        {
          "Name": "Appraised",
          "DisplayName": "Appraised",
          "Description": "Asset has been appraised, now awaiting inspection",
          "PercentComplete": 45,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Reject the offer",
              "Function": "Reject",
              "NextStates": [ "Active" ],
              "DisplayName": "Reject"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Cancel the offer",
              "Function": "Terminate",
              "NextStates": [ "Terminated" ],
              "DisplayName": "Terminate"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Rescind the offer you placed for this asset",
              "Function": "RescindOffer",
              "NextStates": [ "Active" ],
              "DisplayName": "Rescind Offer"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceInspector" ],
              "Description": "Mark the asset as inspected",
              "Function": "MarkInspected",
              "NextStates": [ "NotionalAcceptance" ],
              "DisplayName": "Mark Inspected"
            }
          ]
        },
        {
          "Name": "NotionalAcceptance",
          "DisplayName": "Notional Acceptance",
          "Description": "Asset has been inspected and appraised, awaiting final sign-off from buyer and seller",
          "PercentComplete": 50,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Sign-off on inspection and appraisal",
              "Function": "Accept",
              "NextStates": [ "SellerAccepted" ],
              "DisplayName": "SellerAccept"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Reject the proposed offer for the asset",
              "Function": "Reject",
              "NextStates": [ "Active" ],
              "DisplayName": "Reject"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Cancel this instance of asset transfer",
              "Function": "Terminate",
              "NextStates": [ "Terminated" ],
              "DisplayName": "Terminate"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Sign-off on inspection and appraisal",
              "Function": "Accept",
              "NextStates": [ "BuyerAccepted" ],
              "DisplayName": "BuyerAccept"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Rescind the offer you placed for this asset",
              "Function": "RescindOffer",
              "NextStates": [ "Active" ],
              "DisplayName": "Rescind Offer"
            }
          ]
        },
        {
          "Name": "BuyerAccepted",
          "DisplayName": "Buyer Accepted",
          "Description": "Buyer has signed-off on inspection and appraisal",
          "PercentComplete": 75,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Sign-off on inspection and appraisal",
              "Function": "Accept",
              "NextStates": [ "SellerAccepted" ],
              "DisplayName": "Accept"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Reject the proposed offer for the asset",
              "Function": "Reject",
              "NextStates": [ "Active" ],
              "DisplayName": "Reject"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Cancel this instance of asset transfer",
              "Function": "Terminate",
              "NextStates": [ "Terminated" ],
              "DisplayName": "Terminate"
            }
          ]
        },
        {
          "Name": "SellerAccepted",
          "DisplayName": "Seller Accepted",
          "Description": "Seller has signed-off on inspection and appraisal",
          "PercentComplete": 75,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Sign-off on inspection and appraisal",
              "Function": "Accept",
              "NextStates": [ "Accepted" ],
              "DisplayName": "Accept"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Rescind the offer you placed for this asset",
              "Function": "RescindOffer",
              "NextStates": [ "Active" ],
              "DisplayName": "Rescind Offer"
            }
          ]
        },
        {
          "Name": "Accepted",
          "DisplayName": "Accepted",
          "Description": "Asset transfer process is complete",
          "PercentComplete": 100,
          "Style": "Success",
          "Transitions": []
        },
        {
          "Name": "Terminated",
          "DisplayName": "Terminated",
          "Description": "Asset transfer has been canceled",
          "PercentComplete": 100,
          "Style": "Failure",
          "Transitions": []
        }
      ]
    }
  ]
}
```
## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Azure Blockchain Workbench REST API'si başvurusu](https://docs.microsoft.com/rest/api/azure-blockchain-workbench)

