---
title: Şablon yapısı ve sözdizimi
description: Bildirim temelli JSON sözdizimi kullanan Azure Resource Manager şablonlarının yapısını ve özelliklerini açıklar.
ms.topic: conceptual
ms.date: 11/12/2019
ms.openlocfilehash: ed3f75862e33f87001bc0a7720a98adb64dde5c3
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74149765"
---
# <a name="understand-the-structure-and-syntax-of-azure-resource-manager-templates"></a>Azure Resource Manager şablonlarının yapısını ve sözdizimini anlayın

Bu makalede bir Azure Resource Manager şablonunun yapısı açıklanır. Bir şablonun farklı bölümlerini ve bu bölümlerde kullanılabilen özellikleri gösterir.

Bu makale, Kaynak Yöneticisi şablonları hakkında bazı benzerlik sahibi olan kullanıcılara yöneliktir. Şablonun yapısı hakkında ayrıntılı bilgi sağlar. Şablon oluşturma sürecinde size kılavuzluk eden adım adım bir öğretici için bkz. [öğretici: ilk Azure Resource Manager şablonunuzu oluşturma ve dağıtma](template-tutorial-create-first-template.md).

## <a name="template-format"></a>Şablon biçimi

En basit yapısında, bir şablon aşağıdaki öğelere sahiptir:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "",
  "apiProfile": "",
  "parameters": {  },
  "variables": {  },
  "functions": [  ],
  "resources": [  ],
  "outputs": {  }
}
```

| Öğe adı | Gerekli | Açıklama |
|:--- |:--- |:--- |
| $schema |Yes |Şablon dilinin sürümünü açıklayan JSON Şema dosyasının konumu.<br><br> Kaynak grubu dağıtımları için şunu kullanın: `https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#`<br><br>Abonelik dağıtımları için şunu kullanın: `https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#` |
| contentVersion |Yes |Şablonun sürümü (1.0.0.0 gibi). Bu öğe için herhangi bir değer sağlayabilirsiniz. Şablonunuzda önemli değişiklikleri belgelemek için bu değeri kullanın. Şablonu kullanarak kaynakları dağıttığınızda, bu değer doğru şablonun kullanıldığından emin olmak için kullanılabilir. |
| apiProfile |Hayır | Kaynak türleri için API sürümleri koleksiyonu görevi gören bir API sürümü. Şablondaki her kaynak için API sürümlerini belirtmek zorunda kalmamak için bu değeri kullanın. Bir API profili sürümü belirttiğinizde ve kaynak türü için bir API sürümü belirtmezseniz Kaynak Yöneticisi, profilde tanımlanan bu kaynak türü için API sürümünü kullanır.<br><br>API profili özelliği, bir şablonu Azure Stack ve küresel Azure gibi farklı ortamlara dağıtmada özellikle faydalıdır. Şablonunuzun her iki ortamda da desteklenen sürümleri otomatik olarak kullandığından emin olmak için API profili sürümünü kullanın. Geçerli API profili sürümlerinin ve profilde tanımlanan kaynak API sürümlerinin bir listesi için bkz. [API profili](https://github.com/Azure/azure-rest-api-specs/tree/master/profile).<br><br>Daha fazla bilgi için bkz. [API profillerini kullanarak sürümleri izleme](templates-cloud-consistency.md#track-versions-using-api-profiles). |
| [parametreler](#parameters) |Hayır |Kaynak dağıtımını özelleştirmek için dağıtım yürütüldüğünde belirtilen değerler. |
| [variables](#variables) |Hayır |Şablon dil ifadelerini basitleştirmek için şablonda JSON parçaları olarak kullanılan değerler. |
| [functions](#functions) |Hayır |Şablon içinde kullanılabilen Kullanıcı tanımlı işlevler. |
| [resources](#resources) |Yes |Bir kaynak grubunda veya abonelikte dağıtılan veya güncellenen kaynak türleri. |
| [outputs](#outputs) |Hayır |Dağıtımdan sonra döndürülen değerler. |

Her öğenin ayarlayabileceğiniz özellikleri vardır. Bu makalede, şablonun bölümleri daha ayrıntılı olarak açıklanmaktadır.

## <a name="parameters"></a>Parametreler

Şablonun parametreler bölümünde, kaynakları dağıttığınızda hangi değerleri gir, istediğinizi belirtirsiniz. Bir şablonda 256 parametre ile sınırlı olursunuz. Birden çok özellik içeren nesneleri kullanarak parametre sayısını azaltabilirsiniz.

Bir parametre için kullanılabilen özellikler şunlardır:

```json
"parameters": {
  "<parameter-name>" : {
    "type" : "<type-of-parameter-value>",
    "defaultValue": "<default-value-of-parameter>",
    "allowedValues": [ "<array-of-allowed-values>" ],
    "minValue": <minimum-value-for-int>,
    "maxValue": <maximum-value-for-int>,
    "minLength": <minimum-length-for-string-or-array>,
    "maxLength": <maximum-length-for-string-or-array-parameters>,
    "metadata": {
      "description": "<description-of-the parameter>"
    }
  }
}
```

| Öğe adı | Gerekli | Açıklama |
|:--- |:--- |:--- |
| parametre-adı |Yes |Parametrenin adı. Geçerli bir JavaScript tanımlayıcı olmalıdır. |
| type |Yes |Parametre değerinin türü. İzin verilen türler ve değerler **dize**, **SecureString**, **int**, **bool**, **nesne**, **secureobject**ve **dizidir**. Bkz. [veri türleri](#data-types). |
| defaultValue |Hayır |Parametresi için değer sağlanmazsa, parametre için varsayılan değer. |
| allowedValues |Hayır |Doğru değerin sağlandığından emin olmak için parametresi için izin verilen değerler dizisi. |
| minValue |Hayır |İnt tür parametrelerinin minimum değeri, bu değer dahil değildir. |
| maxValue |Hayır |İnt tür parametrelerinin en büyük değeri, bu değer dahil değildir. |
| minLength |Hayır |Dize, güvenli dize ve dizi türü parametrelerinin minimum uzunluğu, bu değer dahil değildir. |
| maxLength |Hayır |Dize, güvenli dize ve dizi türü parametrelerinin uzunluk üst sınırı, bu değer dahil değildir. |
| açıklama |Hayır |Portalda kullanıcılara görüntülenen parametrenin açıklaması. Daha fazla bilgi için bkz. [şablonlarda açıklamalar](#comments). |

Parametrelerin nasıl kullanılacağına ilişkin örnekler için, bkz. [Azure Resource Manager şablonlarındaki parametreler](template-parameters.md).

### <a name="data-types"></a>Veri türleri

Satır içi parametre olarak geçirilen tamsayılar için, değer aralığı, dağıtım için kullandığınız SDK veya komut satırı aracıyla sınırlı olabilir. Örneğin, bir şablonu dağıtmak için PowerShell kullanılırken, tamsayı türleri-2147483648 ile 2147483647 arasında değişebilir. Bu sınırlamayı önlemek için bir [parametre dosyasında](resource-manager-parameter-files.md)büyük tamsayı değerlerini belirtin. Kaynak türleri, tamsayı özellikleri için kendi sınırlarını uygular.

Şablonunuzda Boole ve tamsayı değerlerini belirtirken değeri tırnak işaretleriyle çevreleyin. Dize değerlerini çift tırnak işaretleriyle Başlat ve bitir.

Nesneler sol küme ayracı ile başlayıp sağ parantez ile biter. Diziler sol köşeli ayraç ile başlar ve sağ köşeli ayraç ile biter.

Güvenli dizeler ve güvenli nesneler, kaynak dağıtımdan sonra okunamaz.

Veri türlerini biçimlendirme örnekleri için bkz. [parametre türü biçimleri](resource-manager-parameter-files.md#parameter-type-formats).

## <a name="variables"></a>Değişkenler

Değişkenler bölümünde, şablonunuzun tamamında kullanılabilecek değerler oluşturursunuz. Değişken tanımlamanız gerekmez, ancak genellikle karmaşık ifadeleri azaltarak şablonunuzu basitleştirir.

Aşağıdaki örnek, bir değişken tanımlamaya yönelik kullanılabilir seçenekleri gösterir:

```json
"variables": {
  "<variable-name>": "<variable-value>",
  "<variable-name>": {
    <variable-complex-type-value>
  },
  "<variable-object-name>": {
    "copy": [
      {
        "name": "<name-of-array-property>",
        "count": <number-of-iterations>,
        "input": <object-or-value-to-repeat>
      }
    ]
  },
  "copy": [
    {
      "name": "<variable-array-name>",
      "count": <number-of-iterations>,
      "input": <object-or-value-to-repeat>
    }
  ]
}
```

Bir değişken için birkaç değer oluşturmak üzere `copy` kullanma hakkında daha fazla bilgi için bkz. [değişken yineleme](resource-group-create-multiple.md#variable-iteration).

Değişkenlerin nasıl kullanılacağına ilişkin örnekler için bkz. [Azure Resource Manager şablonundaki değişkenler](template-variables.md).

## <a name="functions"></a>İşlevler

Şablonunuz içinde kendi işlevlerinizi oluşturabilirsiniz. Bu işlevler, şablonunuzda kullanıma sunulmuştur. Genellikle, şablonunuzun tamamında yinelemek istemediğiniz karmaşık ifadeleri tanımlarsınız. Şablonlarda desteklenen ifadelerden ve [işlevlerden](resource-group-template-functions.md) Kullanıcı tanımlı işlevler oluşturursunuz.

Bir Kullanıcı işlevi tanımlarken bazı kısıtlamalar vardır:

* İşlev değişkenlere erişemez.
* İşlevi yalnızca işlevinde tanımlanan parametreleri kullanabilir. Kullanıcı tanımlı bir işlev içinde [Parameters işlevini](resource-group-template-functions-deployment.md#parameters) kullandığınızda, bu işlevin parametreleriyle sınırlandırılırsınız.
* İşlev, Kullanıcı tanımlı diğer işlevleri çağıramaz.
* İşlev, [başvuru işlevini](resource-group-template-functions-resource.md#reference)kullanamaz.
* İşlevin parametreleri varsayılan değerlere sahip olamaz.

```json
"functions": [
  {
    "namespace": "<namespace-for-functions>",
    "members": {
      "<function-name>": {
        "parameters": [
          {
            "name": "<parameter-name>",
            "type": "<type-of-parameter-value>"
          }
        ],
        "output": {
          "type": "<type-of-output-value>",
          "value": "<function-return-value>"
        }
      }
    }
  }
],
```

| Öğe adı | Gerekli | Açıklama |
|:--- |:--- |:--- |
| ad alanı |Yes |Özel işlevler için ad alanı. Şablon işlevleriyle adlandırma çakışmalarını önlemek için kullanın. |
| işlev adı |Yes |Özel işlevin adı. İşlevi çağrılırken, işlev adını ad alanıyla birleştirin. Örneğin, contoso ad alanında uniqueName adlı bir işlevi çağırmak için `"[contoso.uniqueName()]"`kullanın. |
| parametre-adı |Hayır |Özel işlev içinde kullanılacak parametrenin adı. |
| parametre-değer |Hayır |Parametre değerinin türü. İzin verilen türler ve değerler **dize**, **SecureString**, **int**, **bool**, **nesne**, **secureobject**ve **dizidir**. |
| çıkış türü |Yes |Çıkış değeri türü. Çıkış değerleri işlev giriş parametreleriyle aynı türleri destekler. |
| çıkış değeri |Yes |İşlev tarafından değerlendirilen ve döndürülen şablon dili ifadesi. |

Özel işlevlerin nasıl kullanılacağına ilişkin örnekler için [Azure Resource Manager şablondaki Kullanıcı tanımlı işlevler](template-user-defined-functions.md)bölümüne bakın.

## <a name="resources"></a>Kaynaklar

Kaynaklar bölümünde, dağıtılan veya güncellenen kaynakları tanımlarsınız.

Kaynakları aşağıdaki yapıyla tanımlarsınız:

```json
"resources": [
  {
      "condition": "<true-to-deploy-this-resource>",
      "apiVersion": "<api-version-of-resource>",
      "type": "<resource-provider-namespace/resource-type-name>",
      "name": "<name-of-the-resource>",
      "location": "<location-of-resource>",
      "tags": {
          "<tag-name1>": "<tag-value1>",
          "<tag-name2>": "<tag-value2>"
      },
      "comments": "<your-reference-notes>",
      "copy": {
          "name": "<name-of-copy-loop>",
          "count": <number-of-iterations>,
          "mode": "<serial-or-parallel>",
          "batchSize": <number-to-deploy-serially>
      },
      "dependsOn": [
          "<array-of-related-resource-names>"
      ],
      "properties": {
          "<settings-for-the-resource>",
          "copy": [
              {
                  "name": ,
                  "count": ,
                  "input": {}
              }
          ]
      },
      "sku": {
          "name": "<sku-name>",
          "tier": "<sku-tier>",
          "size": "<sku-size>",
          "family": "<sku-family>",
          "capacity": <sku-capacity>
      },
      "kind": "<type-of-resource>",
      "plan": {
          "name": "<plan-name>",
          "promotionCode": "<plan-promotion-code>",
          "publisher": "<plan-publisher>",
          "product": "<plan-product>",
          "version": "<plan-version>"
      },
      "resources": [
          "<array-of-child-resources>"
      ]
  }
]
```

| Öğe adı | Gerekli | Açıklama |
|:--- |:--- |:--- |
| condition | Hayır | Bu dağıtım sırasında kaynağın sağlanıp sağlanmayacağını belirten Boole değeri. `true`, kaynak dağıtım sırasında oluşturulur. `false`, bu dağıtım için kaynak atlanır. [Koşula](conditional-resource-deployment.md)bakın. |
| apiVersion |Yes |Kaynağı oluşturmak için kullanılacak REST API sürümü. Kullanılabilir değerleri anlamak için bkz. [şablon başvurusu](/azure/templates/). |
| type |Yes |Kaynağın türü. Bu değer, kaynak sağlayıcının ve kaynak türünün ( **Microsoft. Storage/storageAccounts**gibi) ad alanının bir birleşimidir. Kullanılabilir değerleri anlamak için bkz. [şablon başvurusu](/azure/templates/). Bir alt kaynak için, türün biçimi üst kaynak içinde iç içe veya üst kaynak dışında tanımlanmış olmasına bağlıdır. Bkz. [alt kaynaklar için ad ve tür ayarlama](child-resource-name-type.md). |
| ad |Yes |Kaynağın adı. Ad, RFC3986 içinde tanımlanan URI bileşen kısıtlamalarına uymalıdır. Kaynak adını dış taraflar üzerinde kullanıma sunan Azure Hizmetleri, başka bir kimliği sızma girişimi olmadığından emin olmak için adı doğrular. Bir alt kaynak için, adın biçimi üst kaynak içinde iç içe veya üst kaynak dışında tanımlanmış olmasına bağlıdır. Bkz. [alt kaynaklar için ad ve tür ayarlama](child-resource-name-type.md). |
| location |Değişir |Belirtilen kaynağın desteklenen coğrafi konumları. Kullanılabilir konumlardan herhangi birini seçebilirsiniz, ancak genellikle kullanıcılarınıza yakın olan bir seçim yapmak mantıklı olur. Genellikle, aynı bölgedeki birbirleriyle etkileşim kuran kaynakları yerleştirmek de anlamlı hale gelir. Çoğu kaynak türü bir konum gerektirir, ancak bazı türler (örneğin, rol ataması) bir konum gerektirmez. Bkz. [kaynak konumunu ayarlama](resource-location.md). |
| etiketler |Hayır |Kaynakla ilişkili Etiketler. Aboneliğiniz genelinde kaynakları mantıksal olarak düzenlemek için etiketleri uygulayın. |
| comments |Hayır |Şablonunuzda kaynakları belgelemek için notlarınız. Daha fazla bilgi için bkz. [şablonlarda açıklamalar](resource-group-authoring-templates.md#comments). |
| copy |Hayır |Birden fazla örnek gerekliyse, oluşturulacak kaynak sayısı. Varsayılan mod paraleldir. Tüm veya kaynakların aynı anda dağıtılmasını istemiyorsanız seri modunu belirtin. Daha fazla bilgi için bkz. [Azure Resource Manager çeşitli kaynak örnekleri oluşturma](resource-group-create-multiple.md). |
| dependsOn |Hayır |Bu kaynak dağıtılmadan önce dağıtılması gereken kaynaklar. Kaynak Yöneticisi, kaynaklar arasındaki bağımlılıkları değerlendirir ve doğru sırayla dağıtır. Kaynaklar birbirine bağımlı olmadığında, paralel olarak dağıtılır. Değer, kaynak adlarının veya kaynak benzersiz tanımlayıcılarının virgülle ayrılmış bir listesi olabilir. Yalnızca bu şablonda dağıtılan kaynakları listeleyin. Bu şablonda tanımlı olmayan kaynaklar zaten var olmalıdır. Dağıtımınızı yavaşlatabilir ve dairesel bağımlılıklar oluşturduklarında gereksiz bağımlılıkları eklemekten kaçının. Bağımlılıkları ayarlama hakkında yönergeler için bkz. [Azure Resource Manager şablonlarda bağımlılıkları tanımlama](resource-group-define-dependencies.md). |
| properties |Hayır |Kaynağa özgü yapılandırma ayarları. Özelliklerin değerleri, kaynağı oluşturmak için REST API işlem (PUT yöntemi) için istek gövdesinde sağladığınız değerlerle aynıdır. Ayrıca, bir özelliğin birkaç örneğini oluşturmak için bir kopya dizisi belirtebilirsiniz. Kullanılabilir değerleri anlamak için bkz. [şablon başvurusu](/azure/templates/). |
| isteyin | Hayır | Bazı kaynaklar, dağıtılacak SKU 'YU tanımlayan değerlere izin verir. Örneğin, bir depolama hesabı için artıklık türünü belirtebilirsiniz. |
| kind | Hayır | Bazı kaynaklar, dağıttığınız kaynak türünü tanımlayan bir değere izin verir. Örneğin, oluşturulacak Cosmos DB türünü belirtebilirsiniz. |
| plan | Hayır | Bazı kaynaklar, dağıtılacak planı tanımlayan değerlere izin verir. Örneğin, bir sanal makine için Market görüntüsünü belirtebilirsiniz. |
| resources |Hayır |Tanımlanmakta olan kaynağa bağlı olan alt kaynaklar. Yalnızca üst kaynağın şemasına izin verilen kaynak türlerini sağlayın. Üst kaynağın bağımlılığı örtük değildir. Bu bağımlılığı açıkça tanımlamanız gerekir. Bkz. [alt kaynaklar için ad ve tür ayarlama](child-resource-name-type.md). |

## <a name="outputs"></a>Çıkışlar

Çıkış bölümünde dağıtımdan döndürülen değerlerini belirtin. Genellikle, dağıtılan kaynaklardan değerleri döndürürler.

Aşağıdaki örnek, bir çıkış tanımı yapısını gösterir:

```json
"outputs": {
  "<output-name>" : {
    "condition": "<boolean-value-whether-to-output-value>",
    "type" : "<type-of-output-value>",
    "value": "<output-value-expression>"
  }
}
```

| Öğe adı | Gerekli | Açıklama |
|:--- |:--- |:--- |
| çıkış adı |Yes |Çıkış değeri adı. Geçerli bir JavaScript tanımlayıcı olmalıdır. |
| condition |Hayır | Bu çıkış değerinin döndürülüp döndürülmeyeceğini belirten Boolean değeri. `true`, değer dağıtımın çıktısına dahil edilir. `false`, bu dağıtım için çıkış değeri atlanır. Belirtilmediğinde, varsayılan değer `true`. |
| type |Yes |Çıkış değeri türü. Çıkış değerleri şablon giriş parametreleri aynı türlerini destekler. Çıktı türü için **SecureString** belirtirseniz, değer dağıtım geçmişinde gösterilmez ve başka bir şablondan alınamaz. Birden fazla şablonda gizli bir değer kullanmak için, gizli anahtarı bir Key Vault depolayın ve parametre dosyasındaki gizli dizi başvurusu yapın. Daha fazla bilgi için bkz. [dağıtım sırasında güvenli parametre değeri geçirmek için Azure Key Vault kullanma](resource-manager-keyvault-parameter.md). |
| değer |Yes |Değerlendirilen ve çıkış değeri döndürülen şablon dili ifadesi. |

Çıkışları kullanma örnekleri için bkz. [Azure Resource Manager şablonundaki çıktılar](template-outputs.md).

<a id="comments" />

## <a name="comments-and-metadata"></a>Açıklamalar ve meta veriler

Şablonunuza açıklama ve meta veri eklemek için birkaç seçeneğiniz vardır.

### <a name="comments"></a>Yorumlar

Satır içi açıklamalar için `//` ya da `/* ... */` kullanabilirsiniz, ancak bu söz dizimi tüm araçlarla birlikte çalışmaz. Satır içi açıklamalarla şablonlarda çalışmak için Portal Şablon düzenleyicisini kullanamazsınız. Bu açıklama stilini eklerseniz, kullandığınız araçların satır içi JSON açıklamalarını desteklemesini sağlayın.

> [!NOTE]
> Azure CLı kullanarak açıklamaları olan şablonları dağıtmak için `--handle-extended-json-format` anahtarını kullanmanız gerekir.

```json
{
  "type": "Microsoft.Compute/virtualMachines",
  "name": "[variables('vmName')]", // to customize name, change it in variables
  "location": "[parameters('location')]", //defaults to resource group location
  "apiVersion": "2018-10-01",
  "dependsOn": [ /* storage account and network interface must be deployed first */
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
  ],
```

Visual Studio Code, [Azure Resource Manager araçları uzantısı](./resource-manager-tools-vs-code.md#install-resource-manager-tools-extension) Kaynak Yöneticisi şablonu otomatik olarak algılayabilir ve dil modunu uygun şekilde değiştirebilir. VS Code sağ alt köşesinde **Azure Resource Manager şablon** görürseniz, satır içi açıklamaları kullanabilirsiniz. Satır içi açıklamalar artık geçersiz olarak işaretlenmemiştir.

![Şablon modunu Visual Studio Code Azure Resource Manager](./media/resource-group-authoring-templates/resource-manager-template-editor-mode.png)

### <a name="metadata"></a>Meta Veriler

Şablonunuzda neredeyse her yerde `metadata` bir nesne ekleyebilirsiniz. Kaynak Yöneticisi nesneyi yoksayar, ancak JSON düzenleyiciniz özelliğin geçerli olmadığını uyarabilir. Nesnesinde, ihtiyacınız olan özellikleri tanımlayın.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "metadata": {
    "comments": "This template was developed for demonstration purposes.",
    "author": "Example Name"
  },
```

**Parametreler**için `description` özelliği olan bir `metadata` nesnesi ekleyin.

```json
"parameters": {
  "adminUsername": {
    "type": "string",
    "metadata": {
      "description": "User name for the Virtual Machine."
    }
  },
```

Şablonu Portal üzerinden dağıttığınızda, açıklamada sağladığınız metin, bu parametre için bir ipucu olarak otomatik olarak kullanılır.

![Parametre ipucunu göster](./media/resource-group-authoring-templates/show-parameter-tip.png)

**Kaynaklar**için bir `comments` öğesi veya meta veri nesnesi ekleyin. Aşağıdaki örnek hem bir Comments öğesini hem de bir meta veri nesnesini gösterir.

```json
"resources": [
  {
    "comments": "Storage account used to store VM disks",
    "apiVersion": "2018-07-01",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[concat('storage', uniqueString(resourceGroup().id))]",
    "location": "[parameters('location')]",
    "metadata": {
      "comments": "These tags are needed for policy compliance."
    },
    "tags": {
      "Dept": "[parameters('deptName')]",
      "Environment": "[parameters('environment')]"
    },
    "sku": {
      "name": "Standard_LRS"
    },
    "kind": "Storage",
    "properties": {}
  }
]
```

**Çıktılar**için, çıkış değerine bir meta veri nesnesi ekleyin.

```json
"outputs": {
  "hostname": {
    "type": "string",
    "value": "[reference(variables('publicIPAddressName')).dnsSettings.fqdn]",
    "metadata": {
      "comments": "Return the fully qualified domain name"
    }
  },
```

Kullanıcı tanımlı işlevlere meta veri nesnesi ekleyemezsiniz.

## <a name="multi-line-strings"></a>Çok satırlı dizeler

Bir dizeyi birden çok satıra kesebilirsiniz. Örneğin, Location özelliği ve aşağıdaki JSON örneğinde bulunan açıklamalardan biri.

```json
{
  "type": "Microsoft.Compute/virtualMachines",
  "name": "[variables('vmName')]", // to customize name, change it in variables
  "location": "[
    parameters('location')
    ]", //defaults to resource group location
  "apiVersion": "2018-10-01",
  /*
    storage account and network interface
    must be deployed first
  */
  "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
  ],
```

Azure CLı kullanarak çok satırlı dizeler içeren şablonlar dağıtmak için `--handle-extended-json-format` anahtarını kullanmanız gerekir.

## <a name="next-steps"></a>Sonraki adımlar

* Farklı türlerde çözümler için tam şablonları görüntülemek üzere bkz. [Azure Hızlı Başlangıç Şablonları](https://azure.microsoft.com/documentation/templates/).
* Kullanabileceğiniz gelen içinde şablon işlevleri hakkında daha fazla ayrıntı için bkz: [Azure Resource Manager şablonu işlevleri](resource-group-template-functions.md).
* Dağıtım sırasında birkaç şablonu birleştirmek için bkz. [Azure Resource Manager ile bağlantılı şablonları kullanma](resource-group-linked-templates.md).
* Şablon oluşturma hakkında öneriler için bkz. [Azure Resource Manager şablonu en iyi yöntemleri](template-best-practices.md).
* Tüm Azure ortamlarında kullanabileceğiniz Kaynak Yöneticisi şablonları oluşturmaya yönelik öneriler ve Azure Stack için bkz. [bulut tutarlılığı için Azure Resource Manager şablonları geliştirme](templates-cloud-consistency.md).
