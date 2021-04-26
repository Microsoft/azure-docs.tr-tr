---
title: Girişim tanımı yapısının ayrıntıları
description: Kuruluşunuzdaki Azure kaynaklarına dağıtım için ilke tanımlarını gruplamak üzere ilke girişim tanımlarının nasıl kullanıldığını açıklar.
ms.date: 03/16/2021
ms.topic: conceptual
ms.openlocfilehash: edd3f25dd528d1a718c9287c9f30988b87fb73e2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104587228"
---
# <a name="azure-policy-initiative-definition-structure"></a>Azure Ilke girişimi tanım yapısı

Girişimler, tek bir öğe olarak bir grupla birlikte çalışırken atamaları ve yönetimi basitleştirmek için çeşitli ilgili ilke tanımlarını gruplandırmaya olanak sağlar. Örneğin, ilgili etiketleme ilkesi tanımlarını tek bir girişimde gruplandırabilirsiniz. Her ilkeyi ayrı ayrı atamak yerine girişim uygularsınız.

Bir ilke girişim tanımı oluşturmak için JSON kullanırsınız. İlke girişim tanımı için öğeleri içerir:

- görünen ad
- açıklama
- meta veriler
- parameters
- ilke tanımları
- ilke grupları (Bu özellik, [mevzuat uyumluluk (Önizleme) özelliğinin](./regulatory-compliance.md)bir parçasıdır)

Aşağıdaki örnek, iki etiket işlemek için bir girişim oluşturmayı gösterir: `costCenter` ve `productName` . Varsayılan etiket değerini uygulamak için iki yerleşik ilke kullanır.

```json
{
    "properties": {
        "displayName": "Billing Tags Policy",
        "policyType": "Custom",
        "description": "Specify cost Center tag and product name tag",
        "metadata": {
            "version": "1.0.0",
            "category": "Tags"
        },
        "parameters": {
            "costCenterValue": {
                "type": "String",
                "metadata": {
                    "description": "required value for Cost Center tag"
                },
                "defaultValue": "DefaultCostCenter"
            },
            "productNameValue": {
                "type": "String",
                "metadata": {
                    "description": "required value for product Name tag"
                },
                "defaultValue": "DefaultProduct"
            }
        },
        "policyDefinitions": [{
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
                "parameters": {
                    "tagName": {
                        "value": "costCenter"
                    },
                    "tagValue": {
                        "value": "[parameters('costCenterValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
                "parameters": {
                    "tagName": {
                        "value": "costCenter"
                    },
                    "tagValue": {
                        "value": "[parameters('costCenterValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
                "parameters": {
                    "tagName": {
                        "value": "productName"
                    },
                    "tagValue": {
                        "value": "[parameters('productNameValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
                "parameters": {
                    "tagName": {
                        "value": "productName"
                    },
                    "tagValue": {
                        "value": "[parameters('productNameValue')]"
                    }
                }
            }
        ]
    }
}
```

Azure Ilkesi yerleşik bileşenleri ve desenleri [Azure ilke örnekleri](../samples/index.md)' nde bulunur.

## <a name="metadata"></a>Meta veri

İsteğe bağlı `metadata` özelliği, ilke girişim tanımıyla ilgili bilgileri depolar.
Müşteriler, ' de kuruluşları için yararlı olan özellikleri ve değerleri tanımlayabilir `metadata` . Ancak, Azure Ilkesi tarafından ve yerleşik olarak kullanılan bazı _ortak_ özellikler vardır.

### <a name="common-metadata-properties"></a>Ortak meta veri özellikleri

- `version` (dize): bir ilke girişim tanımının içerik sürümü hakkındaki ayrıntıları Izler.
- `category` (dize): ilke tanımının Azure portal hangi kategori altında görüntülendiğini belirler.

  > [!NOTE]
  > [Mevzuat uyumluluk](./regulatory-compliance.md) girişimi Için, `category` **mevzuat uyumluluğu** olmalıdır.

- `preview` (Boolean): ilke girişim tanımı _Önizleme_ Ise, true veya false bayrağı.
- `deprecated` (Boolean): ilke girişim tanımı _kullanım dışı_ olarak işaretlenmişse true veya false bayrağı.

> [!NOTE]
> Azure Ilke hizmeti `version` , `preview` `deprecated` bir yerleşik ilke tanımına veya girişim ve duruma yapılan değişiklik düzeyini iletmek için, ve özelliklerini kullanır. Biçimi `version` : `{Major}.{Minor}.{Patch}` . _Kullanım dışı_ veya _Önizleme_ gibi belirli durumlar, `version` özelliğe veya başka bir özellikte **Boole** olarak eklenir. Azure Ilke sürümlerinin yerleşik yolu hakkında daha fazla bilgi için bkz. [yerleşik sürüm oluşturma](https://github.com/Azure/azure-policy/blob/master/built-in-policies/README.md).

## <a name="parameters"></a>Parametreler

Parametreler, ilke tanımlarının sayısını azaltarak ilke yönetiminizi basitleştirmeye yardımcı olur. Parametreleri,,,, biçiminde alanlar gibi düşünün `name` `address` `city` `state` . Bu parametreler her zaman aynı kalır, ancak değerleri formu dolduran kişiye göre değişir.
Parametreler, ilke girişimlerini oluştururken de aynı şekilde çalışır. Bir ilke girişim tanımına parametreler ekleyerek, dahil edilen ilkelerde bu parametreyi yeniden kullanabilirsiniz.

> [!NOTE]
> Bir girişim atandıktan sonra, giriş düzeyi parametreleri değiştirilemez. Bu nedenle, parametresini tanımlarken bir **DefaultValue** ayarlaması önerilir.

### <a name="parameter-properties"></a>Parametre özellikleri

Bir parametre, ilke girişim tanımında kullanılan aşağıdaki özelliklere sahiptir:

- `name`: Parametresinin adı. `parameters`İlke kuralı içindeki dağıtım işlevi tarafından kullanılır. Daha fazla bilgi için bkz. [parametre değeri kullanma](#passing-a-parameter-value-to-a-policy-definition).
- `type`: Parametrenin **dize**, **dizi**, **nesne**, **Boole**, **tamsayı**, **float** veya **TarihSaat** olduğunu belirler.
- `metadata`: Kullanıcı dostu bilgileri göstermek için öncelikle Azure portal tarafından kullanılan alt özellikleri tanımlar:
  - `description`: Parametresinin hangi amaçla kullanıldığına ilişkin açıklama. , Kabul edilebilir değer örnekleri sağlamak için kullanılabilir.
  - `displayName`: Parametre için portalda gösterilen kolay ad.
  - `strongType`: (İsteğe bağlı) Portal üzerinden ilke tanımı atanırken kullanılır. Bağlama duyarlı bir liste sağlar. Daha fazla bilgi için bkz. [Strongtype](#strongtype).
- `defaultValue`: (İsteğe bağlı) değer verilmezse bir atamadaki parametresinin değerini ayarlar.
- `allowedValues`: (İsteğe bağlı), atama sırasında parametrenin kabul ettiği bir değer dizisi sağlar.

Örnek olarak, dahil edilen çeşitli ilke tanımlarında kaynakların konumlarını sınırlamak için bir ilke girişim tanımı tanımlayabilirsiniz. Bu ilke girişim tanımı için bir parametre **Allowedlocations** olabilir. Daha sonra parametresi dahil edilen her ilke tanımı için kullanılabilir ve ilke girişiminin atanması sırasında tanımlanır.

```json
"parameters": {
    "init_allowedLocations": {
        "type": "array",
        "metadata": {
            "description": "The list of allowed locations for resources.",
            "displayName": "Allowed locations",
            "strongType": "location"
        },
        "defaultValue": [ "westus2" ],
        "allowedValues": [
            "eastus2",
            "westus2",
            "westus"
        ]
    }
}
```

### <a name="passing-a-parameter-value-to-a-policy-definition"></a>Bir ilke tanımına parametre değeri geçirme

Girişim tanımının [PolicyDefinitions](#policy-definitions) dizisinde dahil edilen ilke tanımlarına hangi girişim parametrelerini geçireceğiniz bildirirsiniz. Parametre adı aynı olsa da, girişimlerin ilke tanımlarındaki farklı adları kullanmak kod okunabilirliğini basitleştirir.

Örneğin, daha önce tanımlanan **init_allowedLocations** girişim parametresi, dahil edilen birkaç ilke tanımına ve bunlara **sql_locations** ve **vm_locations** parametrelere geçirilebilir:

```json
"policyDefinitions": [
    {
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/0ec8fc28-d5b7-4603-8fec-39044f00a92b",
        "policyDefinitionReferenceId": "allowedLocationsSQL",
        "parameters": {
            "sql_locations": {
                "value": "[parameters('init_allowedLocations')]"
            }
        }
    },
    {
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/aa09bd0f-aa5f-4343-b6ab-a33a6a6304f3",
        "policyDefinitionReferenceId": "allowedLocationsVMs",
        "parameters": {
            "vm_locations": {
                "value": "[parameters('init_allowedLocations')]"
            }
        }
    }
]
```

Bu örnek, [parametre özelliklerinde](#parameter-properties)gösterilen **init_allowedLocations** parametresine başvurur.

### <a name="strongtype"></a>strongType

Özelliği içinde `metadata` , Azure Portal içinde çoklu seçim listesi sağlamak Için **strongtype** kullanabilirsiniz. **strongtype** desteklenen bir _kaynak türü_ veya izin verilen bir değer olabilir. Bir _kaynak türünün_ **strongtype** için geçerli olup olmadığını anlamak Için [Get-azresourceprovider](/powershell/module/az.resources/get-azresourceprovider)' ı kullanın.

**Get-AzResourceProvider** tarafından döndürülen bazı kaynak türleri desteklenir. Bu kaynak türleri şunlardır:

- `Microsoft.RecoveryServices/vaults/backupPolicies`

**Strongtype** için kaynak olmayan tür için izin verilen değerler şunlardır:

- `location`
- `resourceTypes`
- `storageSkus`
- `vmSKUs`
- `existingResourceGroups`

## <a name="policy-definitions"></a>İlke tanımları

`policyDefinitions`Girişim tanımının bölümü, girişimi içinde mevcut ilke tanımlarının dahil olduğu bir _dizidir_ . Bir [ilke tanımına parametre değeri geçirilerek](#passing-a-parameter-value-to-a-policy-definition)belirtildiği gibi, bu özellik [girişim parametrelerinin](#parameters) ilke tanımına geçirildiği yerdir.

### <a name="policy-definition-properties"></a>İlke tanımı özellikleri

Bir ilke tanımını temsil eden her _dizi_ öğesi aşağıdaki özelliklere sahiptir:

- `policyDefinitionId` (dize): dahil edilecek özel veya yerleşik ilke tanımının KIMLIĞI.
- `policyDefinitionReferenceId` (dize): dahil edilen ilke tanımı için kısa bir ad.
- `parameters`: (İsteğe bağlı) Bu ilke tanımındaki bir özellik olarak, dahil edilen ilke tanımına bir girişim parametresi geçirmek için ad/değer çiftleri. Daha fazla bilgi için bkz. [Parametreler](#parameters).
- `groupNames` (dizeler dizisi): (Isteğe bağlı) ilke tanımının üyesi olduğu grup. Daha fazla bilgi için bkz. [ilke grupları](#policy-definition-groups).

`policyDefinitions`Her biri aynı girişim parametresini geçen iki adet dahil ilke tanımına sahip olan bir örneği aşağıda verilmiştir:

```json
"policyDefinitions": [
    {
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/0ec8fc28-d5b7-4603-8fec-39044f00a92b",
        "policyDefinitionReferenceId": "allowedLocationsSQL",
        "parameters": {
            "sql_locations": {
                "value": "[parameters('init_allowedLocations')]"
            }
        }
    },
    {
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/aa09bd0f-aa5f-4343-b6ab-a33a6a6304f3",
        "policyDefinitionReferenceId": "allowedLocationsVMs",
        "parameters": {
            "vm_locations": {
                "value": "[parameters('init_allowedLocations')]"
            }
        }
    }
]
```

## <a name="policy-definition-groups"></a>İlke Tanım grupları

Bir girişim tanımındaki ilke tanımları gruplandırılabilir ve kategorilere ayrılır. Azure Ilkesinin [mevzuat uyumluluğu](./regulatory-compliance.md) (Önizleme) **özelliği, bu** özelliği tanımlar ve **Uyumluluk etki alanlarına** tanımları gruplamak için kullanır. Bu bilgiler, `policyDefinitionGroups` _dizi_ özelliğinde tanımlanmıştır. Ek gruplandırma ayrıntıları, Microsoft tarafından oluşturulan **Policymetadata** nesnesinde bulunabilir. Bilgi için bkz. [meta veri nesneleri](#metadata-objects).

### <a name="policy-definition-groups-parameters"></a>İlke Tanım grupları parametreleri

İçindeki her _dizi_ öğesi `policyDefinitionGroups` aşağıdaki özelliklerden her ikisine de sahip olmalıdır:

- `name` (dize) \[ gerekli \] : **grubun** kısa adı. Mevzuat uyumluluğu, **Denetim**. Bu özelliğin değeri içinde tarafından kullanılır `groupNames` `policyDefinitions` .
- `category` (dize): grubun ait olduğu hiyerarşi. Yasal uyumluluk bölümünde, denetimin **Uyumluluk etki alanı** .
- `displayName` (dize): **Grup** veya **denetimin** kolay adı. Portal tarafından kullanılır.
- `description` (dize): **grubun** veya **denetimin** neleri kapsadığından bir açıklama.
- `additionalMetadataId`(dize): **Denetim** ve **Uyumluluk etki alanı** hakkında ek ayrıntılara sahip [policymetadata](#metadata-objects) nesnesinin konumu.

  > [!NOTE]
  > Müşteriler var olan bir [Policymetadata](#metadata-objects) nesnesine işaret edebilir. Ancak, bu nesneler _salt okunurdur_ ve yalnızca Microsoft tarafından oluşturulur.

`policyDefinitionGroups`NIST yerleşik girişim tanımındaki özelliğin bir örneği şöyle görünür:

```json
"policyDefinitionGroups": [
    {
        "name": "NIST_SP_800-53_R4_AC-1",
        "additionalMetadataId": "/providers/Microsoft.PolicyInsights/policyMetadata/NIST_SP_800-53_R4_AC-1"
    }
]
```

### <a name="metadata-objects"></a>Meta veri nesneleri

Microsoft tarafından oluşturulan mevzuat uyumluluğu, her denetim hakkında ek bilgiler de vardır.
Bu bilgiler şunlardır:

- Yasal bir uyumluluk girişimi üzerindeki bir **denetime** genel bakış Azure Portal gösterilir.
- REST API yoluyla kullanılabilir. `Microsoft.PolicyInsights`Kaynak sağlayıcısına ve [policymetadata işlem grubuna](/rest/api/policy/policymetadata/getresource)bakın.
- Azure CLı aracılığıyla kullanılabilir. [Az Policy Metadata](/cli/azure/policy/metadata) komutuna bakın.

> [!IMPORTANT]
> Mevzuata uygunluk için meta veri nesneleri _salt okunurdur_ ve müşteriler tarafından oluşturulamaz.

Bir ilke gruplandırması için meta veriler, düğümde aşağıdaki bilgilere sahiptir `properties` :

- `metadataId`: Gruplandırmanın ilişkili olduğu **DENETIM kimliği** .
- `category`(gerekli): **denetimin** ait olduğu **Uyumluluk etki alanı** .
- `title` (gerekli): **DENETIM kimliğinin** kolay adı.
- `owner` (gerekli): Azure 'da denetim sorumluluğunu kim olduğunu tanımlar: _Müşteri_, _Microsoft_, _paylaşılan_.
- `description`: Denetimle ilgili ek bilgiler.
- `requirements`: Denetim uygulamasının sorumluluğuyla ilgili ayrıntılar.
- `additionalContentUrl`: Denetim hakkında daha fazla bilgi için bir bağlantı. Bu özellik genellikle uyumluluk standardında bu denetimi içeren belge bölümünün bir bağlantıdır.

**Policymetadata** nesnesine bir örnek aşağıda verilmiştir. Bu örnek meta veriler _NıST SP 800-53 R4 AC-1_ denetimine aittir.

```json
{
  "properties": {
    "metadataId": "NIST SP 800-53 R4 AC-1",
    "category": "Access Control",
    "title": "Access Control Policy and Procedures",
    "owner": "Shared",
    "description": "**The organization:**    \na. Develops, documents, and disseminates to [Assignment: organization-defined personnel or roles]:  \n1. An access control policy that addresses purpose, scope, roles, responsibilities, management commitment, coordination among organizational entities, and compliance; and  \n2. Procedures to facilitate the implementation of the access control policy and associated access controls; and  \n  
\nb. Reviews and updates the current:  \n1. Access control policy [Assignment: organization-defined frequency]; and  \n2. Access control procedures [Assignment: organization-defined frequency].",
    "requirements": "**a.**  The customer is responsible for developing, documenting, and disseminating access control policies and procedures. The customer access control policies and procedures address access to all customer-deployed resources and customer system access (e.g., access to customer-deployed virtual machines, access to customer-built applications).  \n**b.**  The customer is responsible for reviewing and updating access control policies and procedures in accordance with FedRAMP requirements.",
    "additionalContentUrl": "https://nvd.nist.gov/800-53/Rev4/control/AC-1"
  },
  "id": "/providers/Microsoft.PolicyInsights/policyMetadata/NIST_SP_800-53_R4_AC-1",
  "name": "NIST_SP_800-53_R4_AC-1",
  "type": "Microsoft.PolicyInsights/policyMetadata"
}
```

## <a name="next-steps"></a>Sonraki adımlar

- [Tanım yapısına](./definition-structure.md) bakın
- [Azure ilke örneklerindeki](../samples/index.md)örnekleri gözden geçirin.
- [İlkenin etkilerini anlama](effects.md) konusunu gözden geçirin.
- [Program aracılığıyla ilkelerin nasıl oluşturulduğunu](../how-to/programmatically-create.md)anlayın.
- [Uyumluluk verilerini nasıl alabileceğinizi](../how-to/get-compliance-data.md)öğrenin.
- [Uyumlu olmayan kaynakları nasıl düzelteceğinizi](../how-to/remediate-resources.md)öğrenin.
- [Kaynakları Azure Yönetim gruplarıyla düzenleme](../../management-groups/overview.md)ile yönetim grubunun ne olduğunu inceleyin.
