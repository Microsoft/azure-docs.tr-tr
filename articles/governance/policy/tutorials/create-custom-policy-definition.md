---
title: 'Öğretici: özel bir ilke tanımı oluşturma'
description: Bu öğreticide, Azure kaynaklarınız için özel iş kurallarını zorlamak üzere Azure Ilkesi için özel bir ilke tanımı oluşturursunuz.
ms.date: 03/31/2021
ms.topic: tutorial
ms.openlocfilehash: c6c4fbe05b6df8a6f8521a0551fe276c893bb6be
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106092814"
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

## <a name="prerequisites"></a>Ön koşullar

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

- VS Code için Azure İlkesi uzantısı
- Azure Resource Manager şablonları (ARM şablonları)
  - Mevcut kaynağı dışarı aktar
  - Oluşturma deneyimi
  - Hızlı başlangıç şablonları (GitHub)
  - Şablon başvuru belgeleri
- Azure Resource Manager

### <a name="view-resources-in-vs-code-extension"></a>VS Code uzantılı kaynakları görüntüle

[Vs Code uzantısı](../how-to/extension-for-vscode.md#search-for-and-view-resources) , ortamınızdaki kaynaklara gözatıp her kaynak üzerinde Kaynak Yöneticisi özelliklerini görmenizi sağlamak için kullanılabilir.

### <a name="arm-templates"></a>ARM şablonları

Yönetmek istediğiniz özelliği içeren bir [ARM](../../../azure-resource-manager/templates/template-tutorial-use-template-reference.md) 'ye bakmak için birkaç yol vardır.

#### <a name="existing-resource-in-the-portal"></a>Portalda mevcut kaynak

Özellikleri bulmanın en kolay yolu, aynı türdeki mevcut bir kaynağa bakmanız. Zorlamak istediğiniz ayarla önceden yapılandırılmış kaynaklar, karşılaştırılacak değeri de sağlar.
Söz konusu kaynak için Azure portal **şablonu dışarı aktar** sayfasına ( **Ayarlar** altında) bakın.

> [!WARNING]
> Azure portal tarafından dışarıya alınan ARM şablonu, `deployment` bir [Deployifnotexists](../concepts/effects.md#deployifnotexists) Ilke tanımındaki bir ARM şablonunun özelliğine doğrudan takılıdır.

:::image type="content" source="../media/create-custom-policy-definition/export-template.png" alt-text="Azure portal içindeki mevcut bir kaynaktaki şablonu dışarı aktar sayfasının ekran görüntüsü." border="false":::

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

**Özellikler** altında, **supportsHttpsTrafficOnly** adlı bir değer **false** olarak ayarlanır. Bu özellik, aradığınız özellik gibi görünüyor. Ayrıca, kaynağın **türü** **Microsoft. Storage/storageaccounts** olur. Tür, ilkeyi yalnızca bu türdeki kaynaklarla sınırlandırmamızı sağlar.

#### <a name="create-a-resource-in-the-portal"></a>Portalda kaynak oluşturma

Portal aracılığıyla başka bir yöntem de kaynak oluşturma deneyimidir. Portal aracılığıyla bir depolama hesabı oluştururken, **Gelişmiş** sekmesinde **güvenlik aktarımı gerekli** değildir. Bu özellik _devre dışı_ ve _etkin_ seçeneklere sahiptir. Bilgi simgesi, bu seçeneği doğrulayan ek metnin büyük olasılıkla istediğiniz özelliği olduğunu onaylar. Ancak, Portal bu ekrandaki özellik adını bize söylemez.

**Gözden geçir + oluştur** sekmelerinde, **Otomasyon Için bir şablon indirmek** için sayfanın alt kısmında bir bağlantı bulunur. Bağlantıyı seçmek, yapılandırdığımız kaynağı oluşturan şablonu açar. Bu durumda, iki temel bilgi parçası görüyoruz:

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

GitHub 'daki [Azure hızlı başlangıç şablonlarında](https://github.com/Azure/azure-quickstart-templates) , farklı kaynaklar için oluşturulmuş yüzlerce ARM şablonu vardır. Bu şablonlar, Aradığınız kaynak özelliğini bulmanın harika bir yolu olabilir. Bazı özellikler aradığınız şey gibi görünebilir, ancak başka bir şeyi de kontrol edebilirsiniz.

#### <a name="resource-reference-docs"></a>Kaynak başvuru belgeleri

**SupportsHttpsTrafficOnly** 'ın doğru olduğunu doğrulamak için depolama sağlayıcısı üzerindeki [depolama HESABı kaynağı](/azure/templates/microsoft.storage/2018-07-01/storageaccounts) için ARM şablonu başvurusunu kontrol edin. Properties nesnesi geçerli parametrelerin bir listesini içerir. [Storageaccountpropertiescreateparameters-Object](/azure/templates/microsoft.storage/2018-07-01/storageaccounts#storageaccountpropertiescreateparameters-object) bağlantısına seçilirse, kabul edilebilir özelliklerden oluşan bir tablo gösterilir. **supportsHttpsTrafficOnly** mevcuttur ve açıklama, iş gereksinimlerini karşılayacak şekilde aradığımızda eşleşir.

### <a name="azure-resource-explorer"></a>Azure Resource Manager

Azure kaynaklarınızı keşfetmeye yönelik başka bir yol da [Azure Kaynak Gezgini](https://resources.azure.com) (Önizleme). Bu araç, aboneliğinizin bağlamını kullanır, bu nedenle web sitesinde Azure kimlik bilgilerinizle kimlik doğrulaması yapmanız gerekir. Kimliği doğrulandıktan sonra, sağlayıcılara, aboneliklere, kaynak gruplarına ve kaynaklara göre gezinebilirsiniz.

Bir depolama hesabı kaynağı bulun ve özelliklerine bakın. Burada **supportsHttpsTrafficOnly** özelliği de görüyoruz. **Belge** sekmesini seçtiğinizde, özellik açıklamasının daha önce başvuru belgelerinde bulduğumuz ile eşleştiğini görüyoruz.

## <a name="find-the-property-alias"></a>Özellik diğer adını bul

Kaynak özelliği tanımlandık, ancak bu özelliği bir [diğer adla](../concepts/definition-structure.md#aliases)eşleştirmemiz gerekiyor.

Bir Azure kaynağı için diğer adları belirlemenin birkaç yolu vardır. Bu öğretici için her birine bakacağız:

- VS Code için Azure İlkesi uzantısı
- Azure CLI’si
- Azure PowerShell

### <a name="get-aliases-in-vs-code-extension"></a>VS Code uzantılı diğer adları al

VS Code uzantısının Azure Ilke uzantısı, kaynaklarınıza gözatmayı ve [diğer adları bulmayı](../how-to/extension-for-vscode.md#discover-aliases-for-resource-properties)kolaylaştırır.

> [!NOTE]
> VS Code uzantısı yalnızca Kaynak Yöneticisi modu özelliklerini gösterir ve herhangi bir [kaynak sağlayıcısı modu](../concepts/definition-structure.md#mode) özelliği görüntülemez.

### <a name="azure-cli"></a>Azure CLI’si

Azure CLı 'da, `az provider` komut grubu kaynak diğer adlarını aramak için kullanılır. Daha önce Azure kaynağı hakkında aldık ayrıntıları temel alarak **Microsoft. Storage** ad alanını filtreleyeceğiz.

```azurecli-interactive
# Login first with az login if not using Cloud Shell

# Get Azure Policy aliases for type Microsoft.Storage
az provider show --namespace Microsoft.Storage --expand "resourceTypes/aliases" --query "resourceTypes[].aliases[].name"
```

Sonuçlarda, **supportsHttpsTrafficOnly** adlı depolama hesapları tarafından desteklenen bir diğer ad görüyoruz. Bu diğer adın varlığı, iş gereksinimlerinizi zorlayabilmemiz için ilkeyi yazabildiğimiz anlamına gelir!

### <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell, cmdlet, `Get-AzPolicyAlias` kaynak diğer adlarını aramak için kullanılır. Daha önce Azure kaynağı hakkında aldık ayrıntıları temel alarak **Microsoft. Storage** ad alanını filtreleyeceğiz.

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Use Get-AzPolicyAlias to list aliases for Microsoft.Storage
(Get-AzPolicyAlias -NamespaceMatch 'Microsoft.Storage').Aliases
```

Azure CLı gibi sonuçlar, **supportsHttpsTrafficOnly** adlı depolama hesapları tarafından desteklenen bir diğer ad gösterir.

## <a name="determine-the-effect-to-use"></a>Kullanılacak etkiyi belirleme

Uyumlu olmayan kaynaklarınız için ne olacağına karar vermek, ilk yerde değerlendirileceğine karar verirken neredeyse önemli bir şeydir. Uyumlu olmayan bir kaynağa yönelik olası her yanıta bir [efekt](../concepts/effects.md)denir. Bu efekt, uyumlu olmayan kaynağın günlüğe kaydedilmesini, engellenip engellenmediğini, verilerin eklenmiş olduğunu veya kaynağın uyumlu duruma geri yerleştirilmesi için kendisiyle ilişkili bir dağıtıma sahip olup olmadığını denetler.

Bizim örneğimizde, reddetme, Azure ortamımızda uyumlu olmayan kaynaklar oluşturulmasını istemediğimiz için, istiyoruz. Denetim, ilke efektinin, bir ilkenin ne kadar etkili olduğunu belirlemek için ne kadar etkili olduğunu belirlemek için iyi bir seçenektir. Atama başına etkiyi değiştirmenin bir yolu, etkiyi parametreleştirmek olacaktır. Ayrıntıları hakkında daha fazla bilgi için aşağıdaki [parametrelere](#parameters) bakın.

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

### <a name="metadata"></a>Meta veri

İlk üç bileşen ilke meta verilersidir. Bu bileşenlerin, için kural oluşturduğumuz öğrendiğimiz için değerleri sağlaması kolaydır. [Mod](../concepts/definition-structure.md#mode) öncelikli olarak Etiketler ve kaynak konumu ile ilgilidir. Etiketleri destekleyen kaynaklarla değerlendirmeyi sınırlandırmaya gerek olmadığı için, **mod** için _Tüm_ değeri kullanacağız.

```json
"displayName": "Deny storage accounts not using only HTTPS",
"description": "Deny storage accounts not using only HTTPS. Checks the supportsHttpsTrafficOnly property on StorageAccounts.",
"mode": "all",
```

### <a name="parameters"></a>Parametreler

Değerlendirmeyi değiştirmek için bir parametre kullanmadığımızda, sorun giderme için **etkiyi** değiştirmeye izin vermek üzere bir parametre kullanmak istiyoruz. **Bir bir** bir bir bir bir bir bir bir bir bir bir bir  parametre tanımlayacağız  Bu iki seçenek iş gereksinimlerimizle eşleşir. Tamamlanan parametreler bloğu Şu örneğe benzer şekilde görünür:

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

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu öğreticideki kaynaklarla çalışmayı bitirdiğinizde, yukarıda oluşturulan atamalardan veya tanımlardan birini silmek için aşağıdaki adımları kullanın:

1. Azure Ilkesi sayfasının sol tarafında **yazma** ' nın altında **tanımlar** (veya atamayı silmeye çalışıyorsanız **atamalar** ) ' ı seçin.

1. Kaldırmak istediğiniz yeni girişim veya tanımını (ya da atamayı) arayın.

1. Satıra sağ tıklayın ya da tanımın (veya atamanın) sonundaki üç noktayı seçip **Tanımı sil** (veya **Atamayı sil**) öğesini seçin.

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
