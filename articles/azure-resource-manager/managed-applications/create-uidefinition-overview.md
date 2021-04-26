---
title: Portal bölmesi için dosya CreateUiDefinition.js
description: Azure portal için Kullanıcı arabirimi tanımlarının nasıl oluşturulacağını açıklar. Azure yönetilen uygulamaları tanımlarken kullanılır.
author: tfitzmac
ms.topic: conceptual
ms.date: 03/26/2021
ms.author: tomfitz
ms.openlocfilehash: 586237c6dd909312780163cf316220d2f3fddd8c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105641657"
---
# <a name="createuidefinitionjson-for-azure-managed-applications-create-experience"></a>Azure tarafından yönetilen uygulamanın oluşturma deneyimi için CreateUiDefinition.json

Bu belgede **createUiDefinition.js** dosyadaki temel kavramlar tanıtılmaktadır. Azure portal, yönetilen bir uygulama oluştururken Kullanıcı arabirimini tanımlamak için bu dosyayı kullanır.

Şablon aşağıdaki gibidir

```json
{
    "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
    "handler": "Microsoft.Azure.CreateUIDef",
    "version": "0.1.2-preview",
    "parameters": {
        "config": {
            "isWizard": false,
            "basics": { }
        },
        "basics": [ ],
        "steps": [ ],
        "outputs": { },
        "resourceTypes": [ ]
    }
}
```

`CreateUiDefinition`Her zaman üç özellik içerir:

* Iy
* sürüm
* parameters

İşleyici her zaman `Microsoft.Azure.CreateUIDef` ve desteklenen en son sürüm olmalıdır `0.1.2-preview` .

Parameters özelliğinin şeması, belirtilen işleyicinin ve sürümün birleşimine bağlıdır. Yönetilen uygulamalar için desteklenen özellikler,, ve ' dir `config` `basics` `steps` `outputs` . `config`Yalnızca adımın varsayılan davranışını geçersiz kılmanız gerektiğinde kullanırsınız `basics` . Temel bilgiler ve adımlar özellikleri, Azure portal görüntülenecek metin kutuları ve açılan [öğeler](create-uidefinition-elements.md) içerir. Çıktılar özelliği, belirtilen öğelerin çıkış değerlerini Azure Resource Manager şablonunun parametreleriyle eşlemek için kullanılır.

Dahil edilmesi `$schema` önerilir, ancak isteğe bağlıdır. Belirtilmişse, değeri `version` URI içindeki sürümle eşleşmelidir `$schema` .

Createuıdefinition 'nizi oluşturmak için bir JSON Düzenleyicisi kullanabilir, ardından bunu önizlemek için [Createuıdefinition korumalı](https://portal.azure.com/?feature.customPortal=false&#blade/Microsoft_Azure_CreateUIDef/SandboxBlade) alanında test edebilirsiniz. Korumalı alan hakkında daha fazla bilgi için bkz. [Azure yönetilen uygulamalar için Portal arabiriminizi test](test-createuidefinition.md)etme.

## <a name="config"></a>Config

`config`Özelliği isteğe bağlıdır. Temel bilgiler adımının varsayılan davranışını geçersiz kılmak ya da arabiriminizi adım adım sihirbaz olarak ayarlamak için kullanın. Kullanılıyorsa `config` , dosyanın bölümündeki **createUiDefinition.js** ilk özelliktir `parameters` . Aşağıdaki örnek, kullanılabilir özellikleri gösterir.

```json
"config": {
    "isWizard": false,
    "basics": {
        "description": "Customized description with **markdown**, see [more](https://www.microsoft.com).",
        "subscription": {
            "constraints": {
                "validations": [
                    {
                        "isValid": "[not(contains(subscription().displayName, 'Test'))]",
                        "message": "Can't use test subscription."
                    },
                    {
                        "permission": "Microsoft.Compute/virtualmachines/write",
                        "message": "Must have write permission for the virtual machine."
                    },
                    {
                        "permission": "Microsoft.Compute/virtualMachines/extensions/write",
                        "message": "Must have write permission for the extension."
                    }
                ]
            },
            "resourceProviders": [
                "Microsoft.Compute"
            ]
        },
        "resourceGroup": {
            "constraints": {
                "validations": [
                    {
                        "isValid": "[not(contains(resourceGroup().name, 'test'))]",
                        "message": "Resource group name can't contain 'test'."
                    }
                ]
            },
            "allowExisting": true
        },
        "location": {
            "label": "Custom label for location",
            "toolTip": "provide a useful tooltip",
            "resourceTypes": [
                "Microsoft.Compute/virtualMachines"
            ],
            "allowedValues": [
                "eastus",
                "westus2"
            ],
            "visible": true
        }
    }
},
```

Özelliği için `isValid` , true veya false olarak çözümlenen bir ifade yazın. Özelliği için `permission` , [kaynak sağlayıcısı eylemleriyle](../../role-based-access-control/resource-provider-operations.md)birini belirtin.

### <a name="wizard"></a>Ekleme

`isWizard`Özelliği, bir sonraki adıma geçmeden önce her adımın başarılı bir şekilde doğrulanmasını zorunlu kılmanıza olanak sağlar. `isWizard`Özellik belirtilmediğinde, varsayılan değer **false**'dur ve adım adım doğrulama gerekli değildir.

`isWizard`Etkinleştirildiğinde, **doğru** olarak ayarlandığında **temel kavramlar** sekmesi kullanılabilir ve diğer tüm sekmeler devre dışı bırakılır. Bir **sonraki** düğme seçildiğinde sekmenin simgesi, bir sekmenin doğrulamasının geçtiğini veya başarısız olduğunu gösterir. Sekmenin gerekli alanları tamamlanıp doğrulandıktan sonra, **İleri** düğmesi bir sonraki sekmeye gidilmesine izin verir. Tüm sekmeler doğrulamadan geçene zaman, **Gözden geçirme ve oluşturma** sayfasına gidebilir ve **Oluştur** düğmesini seçerek dağıtıma başlayabilirsiniz.

:::image type="content" source="./media/create-uidefinition-overview/tab-wizard.png" alt-text="Sekme Sihirbazı":::

### <a name="override-basics"></a>Geçersiz kılma temelleri

Temel bilgiler yapılandırması, temel kavramlar adımını özelleştirmenize olanak sağlar.

İçin `description` kaynağını açıklayan markaşağı etkin bir dize sağlayın. Çok satırlı biçim ve bağlantılar desteklenir.

`subscription`Ve `resourceGroup` öğeleri daha fazla doğrulama belirtmenize olanak tanır. Doğrulamaları belirtmenin sözdizimi, [metin kutusu](microsoft-common-textbox.md)için özel doğrulama ile aynıdır. Ayrıca `permission` , abonelik veya kaynak grubunda doğrulama belirtebilirsiniz.  

Abonelik denetimi, kaynak sağlayıcısı ad alanlarının listesini kabul eder. Örneğin, **Microsoft. COMPUTE** belirtebilirsiniz. Kullanıcı kaynak sağlayıcısını desteklemeyen bir abonelik seçtiğinde bir hata iletisi gösterir. Bu hata, kaynak sağlayıcısı bu abonelikte kayıtlı olmadığında ve kullanıcının kaynak sağlayıcısını kaydetme izni yoksa oluşur.  

Kaynak grubu denetimi için bir seçeneği vardır `allowExisting` . Ne zaman `true` , kullanıcılar zaten kaynakları olan kaynak gruplarını seçebilir. Bu bayrak en çok çözüm şablonları için geçerlidir; burada varsayılan davranış, kullanıcıların yeni veya boş bir kaynak grubu seçmesini sağlamalıdır. Çoğu senaryoda, bu özelliğin belirtilmesi gerekli değildir.  

İçin `location` , geçersiz kılmak istediğiniz konum denetimi özelliklerini belirtin. Geçersiz kılınmayan özellikler, varsayılan değerlerine ayarlanır. `resourceTypes` tam kaynak türü adlarını içeren bir dize dizisini kabul eder. Konum seçenekleri yalnızca kaynak türlerini destekleyen bölgelerle kısıtlıdır.  `allowedValues`   bir bölge dizesi dizisini kabul eder. Yalnızca bu bölgeler açılan menüde görünür.Hem hem de ayarlayabilirsiniz `allowedValues`    `resourceTypes` . Sonuç, her iki listenin kesişmesi olur. Son olarak, `visible` özellik konum açılan listesini koşullu veya tamamen devre dışı bırakmak için kullanılabilir.  

## <a name="basics"></a>Temel Bilgiler

**Temel kavramlar** adımı Azure Portal dosyayı ayrıştırdığında oluşturulan ilk adımdır. Varsayılan olarak, temel bilgiler, kullanıcıların dağıtım için abonelik, kaynak grubu ve konum seçmesini sağlar.

:::image type="content" source="./media/create-uidefinition-overview/basics.png" alt-text="Temel bilgiler varsayılan":::

Bu bölüme daha fazla öğe ekleyebilirsiniz. Mümkün olduğunda, küme veya yönetici kimlik bilgileri gibi dağıtım genelindeki parametreleri sorgulayan öğeleri ekleyin.

Aşağıdaki örnek, varsayılan öğelere eklenen bir metin kutusunu gösterir.

```json
"basics": [
    {
        "name": "textBox1",
        "type": "Microsoft.Common.TextBox",
        "label": "Textbox on basics",
        "defaultValue": "my text value",
        "toolTip": "",
        "visible": true
    }
]
```

## <a name="steps"></a>Adımlar

Steps özelliği, temel esadan sonra görüntülenecek sıfır veya daha fazla adım içerir. Her adım bir veya daha fazla öğe içerir. Dağıtılan uygulamanın rol veya katmanına göre adımları eklemeyi düşünün. Örneğin, birincil düğüm girişleri için bir adım ve bir kümedeki çalışan düğümlerine yönelik bir adım ekleyin.

```json
"steps": [
    {
        "name": "demoConfig",
        "label": "Configuration settings",
        "elements": [
          ui-elements-needed-to-create-the-instance
        ]
    }
]
```

## <a name="outputs"></a>Çıkışlar

Azure portal, `outputs` `basics` ve `steps` Azure Resource Manager dağıtım şablonunun parametreleriyle öğeleri eşlemek için özelliğini kullanır. Bu sözlüğün anahtarları, şablon parametrelerinin adlarıdır ve değerler başvurulan öğelerden çıkış nesnelerinin özellikleridir.

Yönetilen uygulama kaynak adını ayarlamak için, çıktılar özelliğinde adlı bir değer eklemelisiniz `applicationResourceName` . Bu değeri ayarlamazsanız, uygulama ad için bir GUID atar. Kullanıcı arabirimine, kullanıcıdan bir ad isteyen bir metin kutusu ekleyebilirsiniz.

```json
"outputs": {
    "vmName": "[steps('appSettings').vmName]",
    "trialOrProduction": "[steps('appSettings').trialOrProd]",
    "userName": "[steps('vmCredentials').adminUsername]",
    "pwd": "[steps('vmCredentials').vmPwd.password]",
    "applicationResourceName": "[steps('appSettings').vmName]"
}
```

## <a name="resource-types"></a>Kaynak türleri

Kullanılabilir konumları yalnızca dağıtılacak kaynak türlerini destekleyen konumlara göre filtrelemek için kaynak türlerinin bir dizisini sağlayın. Birden çok kaynak türü sağlarsanız, yalnızca tüm kaynak türlerini destekleyen konumlar döndürülür. Bu özellik isteğe bağlıdır.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
    "handler": "Microsoft.Azure.CreateUIDef",
    "version": "0.1.2-preview",
    "parameters": {
        "resourceTypes": ["Microsoft.Compute/disks"],
        "basics": [
          ...
```  

## <a name="functions"></a>İşlevler

Createuıdefinition, öğelerin girdileri ve çıkışları ve conditionals gibi özelliklerle çalışmak için [işlevler](create-uidefinition-functions.md) sağlar. Bu işlevler, Azure Resource Manager şablon işlevleri için hem söz dizimi hem de işlevselliğe benzer.

## <a name="next-steps"></a>Sonraki adımlar

createUiDefinition.jsdosyanın kendisinde basit bir şema vardır. Gerçek derinliği, desteklenen tüm öğe ve işlevlerden gelir. Bu öğeler, daha ayrıntılı olarak açıklanmıştır:

- [Elements](create-uidefinition-elements.md)
- [İşlevler](create-uidefinition-functions.md)

Createuıdefinition için geçerli bir JSON şeması şurada bulunabilir: `https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json` .

Örnek bir kullanıcı arabirimi dosyası için bkz. [createUiDefinition.json](https://github.com/Azure/azure-managedapp-samples/blob/master/Managed%20Application%20Sample%20Packages/201-managed-app-using-existing-vnet/createUiDefinition.json).
