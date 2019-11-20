---
title: Özel ilke tanımı oluşturma
description: Azure kaynaklarınız için özel iş kurallarını zorlamak üzere Azure Ilkesi için özel bir ilke tanımı oluşturun.
ms.date: 04/23/2019
ms.topic: tutorial
ms.openlocfilehash: 97a85eb28cd0dbb2586623fda442d87a5790db2a
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/16/2019
ms.locfileid: "74128791"
---
# <a name="tutorial-create-a-custom-policy-definition"></a>Öğretici: özel bir ilke tanımı oluşturma

Özel bir ilke tanımı, müşterilerin Azure kullanımı için kendi kurallarını tanımlamasına olanak tanır. Bu kurallar genellikle zorlanır:

- Güvenlik uygulamaları
- Maliyet yönetimi
- Kuruluşa özgü kurallar (adlandırma veya konumlar gibi)

Özel bir ilke oluşturmak için iş sürücüsü ne olursa olsun, adımlar yeni özel ilkeyi tanımlamak için aynıdır.

Özel bir ilke oluşturmadan önce, gereksinimlerinize uyan bir ilkenin zaten var olup olmadığını görmek için [ilke örneklerine](../samples/index.md) bakın.

Özel bir ilke oluşturmaya yönelik yaklaşım şu adımları izler:

> [!div class="checklist"]
> - İş gereksinimlerinizi belirlemek
> - Her gereksinimi bir Azure Kaynak özelliği ile eşleyin
> - Özelliği bir diğer ada eşleyin
> - Hangi etkiyi kullanacağınızı belirleme
> - İlke tanımını oluşturma

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.

## <a name="identify-requirements"></a>Gereksinimleri tanımla

İlke tanımını oluşturmadan önce, ilkenin amacını anlamak önemlidir. Bu öğreticide, ilgili adımları göstermek için hedef olarak ortak bir kurumsal güvenlik gereksinimi kullanacağız:

- Her depolama hesabının HTTPS için etkinleştirilmesi gerekir
- Her depolama hesabının HTTP için devre dışı bırakılması gerekir

Gereksinimleriniz hem "yapılacak" hem de "değil" kaynak durumlarını açıkça tanımlamalıdır.

Kaynağın beklenen durumunu tanımladığımızda, uyumlu olmayan kaynaklarla yapıldığımız şeyleri henüz tanımlamadık. Azure Ilkesi, bir dizi [efekti](../concepts/effects.md)destekler. Bu öğreticide, iş kurallarıyla uyumlu olmadıkları takdirde kaynak oluşturulmasını önleyecek şekilde iş gereksinimini tanımlayacağız. Bu hedefi karşılamak için [reddetme](../concepts/effects.md#deny) efektini kullanacağız. Ayrıca, belirli atamalar için ilkeyi askıya alma seçeneğini de istiyoruz. Bu nedenle, [devre dışı](../concepts/effects.md#disabled) efekti kullanacağız ve ilke tanımında efekti bir [parametre](../concepts/definition-structure.md#parameters) haline getirir.

## <a name="determine-resource-properties"></a>Kaynak özelliklerini belirleme

Azure Ilkesiyle denetlenecek Azure kaynağı, iş gereksinimine bağlı olarak bir depolama hesabıdır. Ancak, ilke tanımında kullanılacak özellikleri bilmiyorum. Azure Ilkesi kaynağın JSON gösterimine göre değerlendirilir, bu nedenle söz konusu kaynakta kullanılabilen özellikleri anladık.

Bir Azure kaynağı için özellikleri belirlemenin birçok yolu vardır. Bu öğretici için her birine bakacağız:

- Resource Manager şablonları
  - Mevcut kaynağı dışarı aktar
  - Oluşturma deneyimi
  - Hızlı başlangıç şablonları (GitHub)
  - Şablon başvuru belgeleri
- Azure Resource Manager

### <a name="resource-manager-templates"></a>Resource Manager şablonları

Yönetmek istediğiniz özelliği içeren [Kaynak Yöneticisi şablona](../../../azure-resource-manager/resource-manager-tutorial-create-encrypted-storage-accounts.md) bakmak için birkaç yol vardır.

#### <a name="existing-resource-in-the-portal"></a>Portalda mevcut kaynak

Özellikleri bulmanın en kolay yolu, aynı türdeki mevcut bir kaynağa bakmanız. Zorlamak istediğiniz ayarla önceden yapılandırılmış kaynaklar, karşılaştırılacak değeri de sağlar.
Söz konusu kaynak için Azure portal **şablonu dışarı aktar** sayfasına ( **Ayarlar**altında) bakın.

![Mevcut kaynak üzerinde şablon dışarı aktarma sayfası](../media/create-custom-policy-definition/export-template.png)

Bunu bir depolama hesabı için yapmak, bu örneğe benzer bir şablonu ortaya çıkarır:

```json
...
"resources": [{
    "comments": "Generalized from resource: '/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount'.",
    "type": "Microsoft.Storage/storageAccounts",
    "sku": {
        "name": "Standard_LRS",
        "tier": "Standard"
    },
    "kind": "Storage",
    "name": "[parameters('storageAccounts_mystorageaccount_name')]",
    "apiVersion": "2018-07-01",
    "location": "westus",
    "tags": {
        "ms-resource-usage": "azure-cloud-shell"
    },
    "scale": null,
    "properties": {
        "networkAcls": {
            "bypass": "AzureServices",
            "virtualNetworkRules": [],
            "ipRules": [],
            "defaultAction": "Allow"
        },
        "supportsHttpsTrafficOnly": false,
        "encryption": {
            "services": {
                "file": {
                    "enabled": true
                },
                "blob": {
                    "enabled": true
                }
            },
            "keySource": "Microsoft.Storage"
        }
    },
    "dependsOn": []
}]
...
```

**Özellikler** altında, **supportsHttpsTrafficOnly** adlı bir değer **false**olarak ayarlanır. Bu özellik, aradığınız özellik gibi görünüyor. Ayrıca, kaynağın **türü** **Microsoft. Storage/storageaccounts**olur. Tür, ilkeyi yalnızca bu türdeki kaynaklarla sınırlandırmamızı sağlar.

#### <a name="create-a-resource-in-the-portal"></a>Portalda kaynak oluşturma

Portal aracılığıyla başka bir yöntem de kaynak oluşturma deneyimidir. Portal aracılığıyla bir depolama hesabı oluştururken, **Gelişmiş** sekmesinde **güvenlik aktarımı gerekli**değildir. Bu özellik _devre dışı_ ve _etkin_ seçeneklere sahiptir. Bilgi simgesi, bu seçeneği doğrulayan ek metnin büyük olasılıkla istediğiniz özelliği olduğunu onaylar. Ancak, Portal bu ekrandaki özellik adını bize söylemez.

**Gözden geçir + oluştur** sekmelerinde, **Otomasyon Için bir şablon indirmek**için sayfanın alt kısmında bir bağlantı bulunur. Bağlantıyı seçmek, yapılandırdığımız kaynağı oluşturan şablonu açar. Bu durumda, iki temel bilgi parçası görüyoruz:

```json
...
"supportsHttpsTrafficOnly": {
    "type": "bool"
}
...
"properties": {
    "accessTier": "[parameters('accessTier')]",
    "supportsHttpsTrafficOnly": "[parameters('supportsHttpsTrafficOnly')]"
}
...
```

Bu bilgiler bize Özellik türünü söyler ve ayrıca **supportsHttpsTrafficOnly** , ardığımız özelliktir.

#### <a name="quickstart-templates-on-github"></a>GitHub 'daki hızlı başlangıç şablonları

GitHub 'daki [Azure hızlı başlangıç şablonlarında](https://github.com/Azure/azure-quickstart-templates) , farklı kaynaklar için oluşturulmuş yüzlerce kaynak yöneticisi şablonu vardır. Bu şablonlar, Aradığınız kaynak özelliğini bulmanın harika bir yolu olabilir. Bazı özellikler aradığınız şey gibi görünebilir, ancak başka bir şeyi de kontrol edebilirsiniz.

#### <a name="resource-reference-docs"></a>Kaynak başvuru belgeleri

**SupportsHttpsTrafficOnly** 'ın doğru olduğunu doğrulamak için depolama sağlayıcısı üzerindeki [depolama hesabı kaynağı](/azure/templates/microsoft.storage/2018-07-01/storageaccounts) için Kaynak Yöneticisi şablonu başvurusunu kontrol edin.
Properties nesnesi geçerli parametrelerin bir listesini içerir. [Storageaccountpropertiescreateparameters-Object](/azure/templates/microsoft.storage/2018-07-01/storageaccounts#storageaccountpropertiescreateparameters-object) bağlantısına seçilirse, kabul edilebilir özelliklerden oluşan bir tablo gösterilir. **supportsHttpsTrafficOnly** mevcuttur ve açıklama, iş gereksinimlerini karşılayacak şekilde aradığımızda eşleşir.

### <a name="azure-resource-explorer"></a>Azure Resource Manager

Azure kaynaklarınızı keşfetmeye yönelik başka bir yol da [Azure Kaynak Gezgini](https://resources.azure.com) (Önizleme). Bu araç, aboneliğinizin bağlamını kullanır, bu nedenle web sitesinde Azure kimlik bilgilerinizle kimlik doğrulaması yapmanız gerekir. Kimliği doğrulandıktan sonra, sağlayıcılara, aboneliklere, kaynak gruplarına ve kaynaklara göre gezinebilirsiniz.

Bir depolama hesabı kaynağı bulun ve özelliklerine bakın. Burada **supportsHttpsTrafficOnly** özelliği de görüyoruz. **Belge** sekmesini seçtiğinizde, özellik açıklamasının daha önce başvuru belgelerinde bulduğumuz ile eşleştiğini görüyoruz.

## <a name="find-the-property-alias"></a>Özellik diğer adını bul

Kaynak özelliği tanımlandık, ancak bu özelliği bir [diğer adla](../concepts/definition-structure.md#aliases)eşleştirmemiz gerekiyor.

Bir Azure kaynağı için diğer adları belirlemenin birkaç yolu vardır. Bu öğretici için her birine bakacağız:

- Azure CLI
- Azure PowerShell
- Azure Kaynak Grafiği

### <a name="azure-cli"></a>Azure CLI

Azure CLı 'de, `az provider` komut grubu kaynak diğer adlarını aramak için kullanılır. Daha önce Azure kaynağı hakkında aldık ayrıntıları temel alarak **Microsoft. Storage** ad alanını filtreleyeceğiz.

```azurecli-interactive
# Login first with az login if not using Cloud Shell

# Get Azure Policy aliases for type Microsoft.Storage
az provider show --namespace Microsoft.Storage --expand "resourceTypes/aliases" --query "resourceTypes[].aliases[].name"
```

Sonuçlarda, **supportsHttpsTrafficOnly**adlı depolama hesapları tarafından desteklenen bir diğer ad görüyoruz. Bu diğer adın varlığı, iş gereksinimlerinizi zorlayabilmemiz için ilkeyi yazabildiğimiz anlamına gelir!

### <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell, `Get-AzPolicyAlias` cmdlet 'i kaynak diğer adlarını aramak için kullanılır. Daha önce Azure kaynağı hakkında aldık ayrıntıları temel alarak **Microsoft. Storage** ad alanını filtreleyeceğiz.

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Use Get-AzPolicyAlias to list aliases for Microsoft.Storage
(Get-AzPolicyAlias -NamespaceMatch 'Microsoft.Storage').Aliases
```

Azure CLı gibi sonuçlar, **supportsHttpsTrafficOnly**adlı depolama hesapları tarafından desteklenen bir diğer ad gösterir.

### <a name="azure-resource-graph"></a>Azure Kaynak Grafiği

[Azure Kaynak Grafiği](../../resource-graph/overview.md) , önizlemede yeni bir hizmettir. Azure kaynaklarının özelliklerini bulmak için başka bir yöntem sağlar. Kaynak grafiğine sahip tek bir depolama hesabına bakmak için örnek bir sorgu aşağıda verilmiştir:

```kusto
where type=~'microsoft.storage/storageaccounts'
| limit 1
```

```azurecli-interactive
az graph query -q "where type=~'microsoft.storage/storageaccounts' | limit 1"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type=~'microsoft.storage/storageaccounts' | limit 1"
```

Sonuçlar, Kaynak Yöneticisi şablonlarda görtiğimiz ve Azure Kaynak Gezgini aracılığıyla benzer şekilde görünür. Bununla birlikte, Azure Kaynak Grafiği sonuçları, diğer _adlar_ dizisinin _yansıtılasında_ [diğer ad](../concepts/definition-structure.md#aliases) ayrıntıları da içerebilir:

```kusto
where type=~'microsoft.storage/storageaccounts'
| limit 1
| project aliases
```

```azurecli-interactive
az graph query -q "where type=~'microsoft.storage/storageaccounts' | limit 1 | project aliases"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type=~'microsoft.storage/storageaccounts' | limit 1 | project aliases"
```

Diğer adlar için bir depolama hesabından örnek çıktı aşağıda verilmiştir:

```json
"aliases": {
    "Microsoft.Storage/storageAccounts/accessTier": null,
    "Microsoft.Storage/storageAccounts/accountType": "Standard_LRS",
    "Microsoft.Storage/storageAccounts/enableBlobEncryption": true,
    "Microsoft.Storage/storageAccounts/enableFileEncryption": true,
    "Microsoft.Storage/storageAccounts/encryption": {
        "keySource": "Microsoft.Storage",
        "services": {
            "blob": {
                "enabled": true,
                "lastEnabledTime": "2018-06-04T17:59:14.4970000Z"
            },
            "file": {
                "enabled": true,
                "lastEnabledTime": "2018-06-04T17:59:14.4970000Z"
            }
        }
    },
    "Microsoft.Storage/storageAccounts/encryption.keySource": "Microsoft.Storage",
    "Microsoft.Storage/storageAccounts/encryption.keyvaultproperties.keyname": null,
    "Microsoft.Storage/storageAccounts/encryption.keyvaultproperties.keyvaulturi": null,
    "Microsoft.Storage/storageAccounts/encryption.keyvaultproperties.keyversion": null,
    "Microsoft.Storage/storageAccounts/encryption.services": {
        "blob": {
            "enabled": true,
            "lastEnabledTime": "2018-06-04T17:59:14.4970000Z"
        },
        "file": {
            "enabled": true,
            "lastEnabledTime": "2018-06-04T17:59:14.4970000Z"
        }
    },
    "Microsoft.Storage/storageAccounts/encryption.services.blob": {
        "enabled": true,
        "lastEnabledTime": "2018-06-04T17:59:14.4970000Z"
    },
    "Microsoft.Storage/storageAccounts/encryption.services.blob.enabled": true,
    "Microsoft.Storage/storageAccounts/encryption.services.file": {
        "enabled": true,
        "lastEnabledTime": "2018-06-04T17:59:14.4970000Z"
    },
    "Microsoft.Storage/storageAccounts/encryption.services.file.enabled": true,
    "Microsoft.Storage/storageAccounts/networkAcls": {
        "bypass": "AzureServices",
        "defaultAction": "Allow",
        "ipRules": [],
        "virtualNetworkRules": []
    },
    "Microsoft.Storage/storageAccounts/networkAcls.bypass": "AzureServices",
    "Microsoft.Storage/storageAccounts/networkAcls.defaultAction": "Allow",
    "Microsoft.Storage/storageAccounts/networkAcls.ipRules": [],
    "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]": [],
    "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].action": [],
    "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].value": [],
    "Microsoft.Storage/storageAccounts/networkAcls.virtualNetworkRules": [],
    "Microsoft.Storage/storageAccounts/networkAcls.virtualNetworkRules[*]": [],
    "Microsoft.Storage/storageAccounts/networkAcls.virtualNetworkRules[*].action": [],
    "Microsoft.Storage/storageAccounts/networkAcls.virtualNetworkRules[*].id": [],
    "Microsoft.Storage/storageAccounts/networkAcls.virtualNetworkRules[*].state": [],
    "Microsoft.Storage/storageAccounts/primaryEndpoints": {
        "blob": "https://mystorageaccount.blob.core.windows.net/",
        "file": "https://mystorageaccount.file.core.windows.net/",
        "queue": "https://mystorageaccount.queue.core.windows.net/",
        "table": "https://mystorageaccount.table.core.windows.net/"
    },
    "Microsoft.Storage/storageAccounts/primaryEndpoints.blob": "https://mystorageaccount.blob.core.windows.net/",
    "Microsoft.Storage/storageAccounts/primaryEndpoints.file": "https://mystorageaccount.file.core.windows.net/",
    "Microsoft.Storage/storageAccounts/primaryEndpoints.queue": "https://mystorageaccount.queue.core.windows.net/",
    "Microsoft.Storage/storageAccounts/primaryEndpoints.table": "https://mystorageaccount.table.core.windows.net/",
    "Microsoft.Storage/storageAccounts/primaryEndpoints.web": null,
    "Microsoft.Storage/storageAccounts/primaryLocation": "eastus2",
    "Microsoft.Storage/storageAccounts/provisioningState": "Succeeded",
    "Microsoft.Storage/storageAccounts/sku.name": "Standard_LRS",
    "Microsoft.Storage/storageAccounts/sku.tier": "Standard",
    "Microsoft.Storage/storageAccounts/statusOfPrimary": "available",
    "Microsoft.Storage/storageAccounts/supportsHttpsTrafficOnly": false
}
```

Azure Kaynak Grafiği (Önizleme) [Cloud Shell](https://shell.azure.com)aracılığıyla kullanılabilir, bu sayede kaynaklarınızın özelliklerini keşfetmeye yönelik hızlı ve kolay bir yol haline gelir.

## <a name="determine-the-effect-to-use"></a>Kullanılacak etkiyi belirleme

Uyumlu olmayan kaynaklarınız için ne olacağına karar vermek, ilk yerde değerlendirileceğine karar verirken neredeyse önemli bir şeydir. Uyumlu olmayan bir kaynağa yönelik olası her yanıta bir [efekt](../concepts/effects.md)denir.
Bu efekt, uyumlu olmayan kaynağın günlüğe kaydedilmesini, engellenip engellenmediğini, verilerin eklenmiş olduğunu veya kaynağın uyumlu duruma geri yerleştirilmesi için kendisiyle ilişkili bir dağıtıma sahip olup olmadığını denetler.

Bizim örneğimizde, Azure ortamımızda uyumsuz kaynaklar oluşturulmasını istemediğiniz için reddetme yaptığımız bir etkiye sahip olur. Denetim, ilke efektinin, bir ilkenin ne kadar etkili olduğunu belirlemek için ne kadar etkili olduğunu belirlemek için iyi bir seçenektir. Atama başına etkiyi değiştirmenin bir yolu, etkiyi parametreleştirmek olacaktır. Ayrıntıları hakkında daha fazla bilgi için aşağıdaki [parametrelere](#parameters) bakın.

## <a name="compose-the-definition"></a>Tanımı oluştur

Artık, yönetmeyi planladığımız özellikler için özellik ayrıntıları ve diğer ad vardır. Ardından, ilke kuralının kendisini oluşturacağız. İlke dilini henüz bilmiyorsanız, ilke tanımını nasıl yapılandıracağınıza yönelik başvuru [ilke tanımı yapısı](../concepts/definition-structure.md) . Aşağıda, ilke tanımının neye benzediklerine ait boş bir şablon verilmiştir:

```json
{
    "properties": {
        "displayName": "<displayName>",
        "description": "<description>",
        "mode": "<mode>",
        "parameters": {
                <parameters>
        },
        "policyRule": {
            "if": {
                <rule>
            },
            "then": {
                "effect": "<effect>"
            }
        }
    }
}
```

### <a name="metadata"></a>Meta Veriler

İlk üç bileşen ilke meta verilersidir. Bu bileşenlerin, için kural oluşturduğumuz öğrendiğimiz için değerleri sağlaması kolaydır. [Mod](../concepts/definition-structure.md#mode) öncelikli olarak Etiketler ve kaynak konumu ile ilgilidir. Etiketleri destekleyen kaynaklarla değerlendirmeyi sınırlandırmaya gerek olmadığı için, **mod**için _Tüm_ değeri kullanacağız.

```json
"displayName": "Deny storage accounts not using only HTTPS",
"description": "Deny storage accounts not using only HTTPS. Checks the supportsHttpsTrafficOnly property on StorageAccounts.",
"mode": "all",
```

### <a name="parameters"></a>Parametreler

Değerlendirmeyi değiştirmek için bir parametre kullanmadığımızda, sorun giderme için **etkiyi** değiştirmeye izin vermek üzere bir parametre kullanmak istiyoruz. **Bir bir** bir bir bir bir bir bir bir bir bir bir bir parametre tanımlayacağız Bu iki seçenek iş gereksinimlerimizle eşleşir. Tamamlanan parametreler bloğu Şu örneğe benzer şekilde görünür:

```json
"parameters": {
    "effectType": {
        "type": "string",
        "defaultValue": "Deny",
        "allowedValues": [
            "Deny",
            "Disabled"
        ],
        "metadata": {
            "displayName": "Effect",
            "description": "Enable or disable the execution of the policy"
        }
    }
},
```

### <a name="policy-rule"></a>İlke kuralı

[İlke kuralını](../concepts/definition-structure.md#policy-rule) oluşturma işlemi, özel ilke tanımımızı oluşturmanın son adımıdır. Test etmek için iki deyim belirledik:

- Depolama hesabı **türü** **Microsoft. Storage/storageaccounts**
- **SupportsHttpsTrafficOnly** depolama hesabı **doğru** değil

Bu deyimlerin her ikisinin de doğru olması gerektiğinden, **allof** [Logical işlecini](../concepts/definition-structure.md#logical-operators)kullanacağız. Bir statik bildirim yapmak yerine, etkikiz **parametresini etkiye** geçireceğiz. Tamamlanan kuralımız Şu örneğe benzer şekilde görünür:

```json
"if": {
    "allOf": [
        {
            "field": "type",
            "equals": "Microsoft.Storage/storageAccounts"
        },
        {
            "field": "Microsoft.Storage/storageAccounts/supportsHttpsTrafficOnly",
            "notEquals": "true"
        }
    ]
},
"then": {
    "effect": "[parameters('effectType')]"
}
```

### <a name="completed-definition"></a>Tamamlandı tanımı

İlkenin üç bölümü tanımlanmış olarak, tamamlanma tanımımız şu şekildedir:

```json
{
    "properties": {
        "displayName": "Deny storage accounts not using only HTTPS",
        "description": "Deny storage accounts not using only HTTPS. Checks the supportsHttpsTrafficOnly property on StorageAccounts.",
        "mode": "all",
        "parameters": {
            "effectType": {
                "type": "string",
                "defaultValue": "Deny",
                "allowedValues": [
                    "Deny",
                    "Disabled"
                ],
                "metadata": {
                    "displayName": "Effect",
                    "description": "Enable or disable the execution of the policy"
                }
            }
        },
        "policyRule": {
            "if": {
                "allOf": [
                    {
                        "field": "type",
                        "equals": "Microsoft.Storage/storageAccounts"
                    },
                    {
                        "field": "Microsoft.Storage/storageAccounts/supportsHttpsTrafficOnly",
                        "notEquals": "true"
                    }
                ]
            },
            "then": {
                "effect": "[parameters('effectType')]"
            }
        }
    }
}
```

Tamamlanan tanım yeni bir ilke oluşturmak için kullanılabilir. Portal ve her SDK (Azure CLı, Azure PowerShell ve REST API) tanımı farklı yollarla kabul eder, bu nedenle doğru kullanımı doğrulamak için her birine yönelik komutları gözden geçirin. Daha sonra, depolama hesaplarınızın güvenliğini yönetmek için parametreli etkiyi kullanarak uygun kaynaklara göre atayın.

## <a name="review"></a>Gözden geçirme

Bu öğreticide, aşağıdaki görevleri başarıyla gerçekleştirdiniz:

> [!div class="checklist"]
> - İş gereksinimlerinizi tanımladı
> - Her gereksinimi bir Azure Kaynak özelliği ile eşlendi
> - Özelliği bir diğer ada eşlendi
> - Kullanım efektinin belirlenmesi
> - İlke tanımı oluşturulmuş

## <a name="next-steps"></a>Sonraki adımlar

Ardından, ilke oluşturmak ve atamak için özel ilke tanımınızı kullanın:

> [!div class="nextstepaction"]
> [İlke tanımı oluşturma ve atama](../how-to/programmatically-create.md#create-and-assign-a-policy-definition)