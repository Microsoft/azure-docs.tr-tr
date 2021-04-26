---
title: Şablon yapısı ve sözdizimi
description: Bildirim temelli JSON sözdizimini kullanarak Azure Resource Manager şablonlarının yapısını ve özelliklerini açıklar (ARM şablonları).
ms.topic: conceptual
ms.date: 03/26/2021
ms.openlocfilehash: 42b893e25155bb3ebe66e0deac180698446a2c9b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105612186"
---
# <a name="understand-the-structure-and-syntax-of-arm-templates"></a>ARM şablonlarının yapısını ve söz dizimini anlama

Bu makalede bir Azure Resource Manager şablonunun yapısı açıklanmaktadır (ARM şablonu). Bir şablonun farklı bölümlerini ve bu bölümlerde kullanılabilen özellikleri gösterir.

Bu makale, ARM şablonları hakkında bazı benzerlik sahibi olan kullanıcılar için tasarlanmıştır. Şablonun yapısı hakkında ayrıntılı bilgi sağlar. Şablon oluşturma sürecinde size kılavuzluk eden adım adım bir öğretici için bkz. [öğretici: Ilk ARM şablonunuzu oluşturma ve dağıtma](template-tutorial-create-first-template.md). Microsoft Learn üzerinde bir Kılavuzlu dizi modülle ARM şablonları hakkında bilgi edinmek için bkz. [ARM şablonlarını kullanarak Azure 'da kaynakları dağıtma ve yönetme](/learn/paths/deploy-manage-resource-manager-templates/).

## <a name="template-format"></a>Şablon biçimi

En basit yapısında, bir şablon aşağıdaki öğelere sahiptir:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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
| $schema |Yes |Şablon dilinin sürümünü açıklayan JavaScript Nesne Gösterimi (JSON) şema dosyasının konumu. Kullandığınız sürüm numarası, dağıtımın kapsamına ve JSON düzenleyicinize bağlıdır.<br><br>[Azure Resource Manager araçları uzantısı ile Visual Studio Code](quickstart-create-templates-use-visual-studio-code.md)kullanıyorsanız, kaynak grubu dağıtımları için en son sürümü kullanın:<br>`https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#`<br><br>Diğer düzenleyiciler (Visual Studio dahil) Bu şemayı işleyemeyebilir. Bu düzenleyiciler için şunu kullanın:<br>`https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#`<br><br>Abonelik dağıtımları için şunu kullanın:<br>`https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#`<br><br>Yönetim grubu dağıtımları için şunu kullanın:<br>`https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#`<br><br>Kiracı dağıtımları için şunu kullanın:<br>`https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#` |
| contentVersion |Yes |Şablonun sürümü (1.0.0.0 gibi). Bu öğe için herhangi bir değer sağlayabilirsiniz. Şablonunuzda önemli değişiklikleri belgelemek için bu değeri kullanın. Şablonu kullanarak kaynakları dağıttığınızda, bu değer doğru şablonun kullanıldığından emin olmak için kullanılabilir. |
| apiProfile |No | Kaynak türleri için API sürümleri koleksiyonu görevi gören bir API sürümü. Şablondaki her kaynak için API sürümlerini belirtmek zorunda kalmamak için bu değeri kullanın. Bir API profili sürümü belirttiğinizde ve kaynak türü için bir API sürümü belirtmezseniz Kaynak Yöneticisi, profilde tanımlanan bu kaynak türü için API sürümünü kullanır.<br><br>API profili özelliği, bir şablonu Azure Stack ve küresel Azure gibi farklı ortamlara dağıtmada özellikle faydalıdır. Şablonunuzun her iki ortamda da desteklenen sürümleri otomatik olarak kullandığından emin olmak için API profili sürümünü kullanın. Geçerli API profili sürümlerinin ve profilde tanımlanan kaynak API sürümlerinin bir listesi için bkz. [API profili](https://github.com/Azure/azure-rest-api-specs/tree/master/profile).<br><br>Daha fazla bilgi için bkz. [API profillerini kullanarak sürümleri izleme](templates-cloud-consistency.md#track-versions-using-api-profiles). |
| [parametrelere](#parameters) |No |Kaynak dağıtımını özelleştirmek için dağıtım yürütüldüğünde belirtilen değerler. |
| [değişkenlerinin](#variables) |No |Şablon dil ifadelerini basitleştirmek için şablonda JSON parçaları olarak kullanılan değerler. |
| [lerdir](#functions) |No |Şablon içinde kullanılabilen Kullanıcı tanımlı işlevler. |
| [kaynakların](#resources) |Yes |Bir kaynak grubunda veya abonelikte dağıtılan veya güncellenen kaynak türleri. |
| [çıkışı](#outputs) |No |Dağıtımdan sonra döndürülen değerler. |

Her öğenin ayarlayabileceğiniz özellikleri vardır. Bu makalede, şablonun bölümleri daha ayrıntılı olarak açıklanmaktadır.

## <a name="parameters"></a>Parametreler

`parameters`Şablonun bölümünde, kaynakları dağıttığınızda hangi değerleri gir, istediğinizi belirtirsiniz. Bir şablonda 256 parametreyle sınırlı olursunuz. Birden çok özellik içeren nesneleri kullanarak parametre sayısını azaltabilirsiniz.

Bir parametre için kullanılabilir özellikler şunlardır:

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
| parametre-adı |Yes |Parametrenin adı. Geçerli bir JavaScript tanımlayıcısı olmalıdır. |
| tür |Yes |Parametre değerinin türü. İzin verilen türler ve değerler **dize**, **SecureString**, **int**, **bool**, **nesne**, **secureobject** ve **dizidir**. [ARM şablonlarındaki veri türlerine](data-types.md)bakın. |
| Değerinin |No |Parametresi için değer sağlanmazsa, parametre için varsayılan değer. |
| allowedValues |No |Doğru değerin sağlandığından emin olmak için parametresi için izin verilen değerler dizisi. |
| minValue |No |İnt tür parametrelerinin minimum değeri, bu değer dahil değildir. |
| Değerini |No |İnt tür parametrelerinin en büyük değeri, bu değer dahil değildir. |
| minLength |No |Dize, güvenli dize ve dizi türü parametrelerinin minimum uzunluğu, bu değer dahil değildir. |
| 'In |No |Dize, güvenli dize ve dizi türü parametrelerinin uzunluk üst sınırı, bu değer dahil değildir. |
| açıklama |No |Portalda kullanıcılara görüntülenen parametrenin açıklaması. Daha fazla bilgi için bkz. [şablonlarda açıklamalar](#comments). |

Parametrelerin nasıl kullanılacağına ilişkin örnekler için bkz. [ARM şablonlarındaki parametreler](template-parameters.md).

## <a name="variables"></a>Değişkenler

`variables`Bölümünde, şablonunuzun tamamında kullanılabilecek değerler oluşturursunuz. Değişken tanımlamanız gerekmez, ancak genellikle karmaşık ifadeleri azaltarak şablonunuzu basitleştirir. Her değişkenin biçimi, [veri türlerinden](data-types.md)biriyle eşleşir.

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

`copy`Bir değişken için birkaç değer oluşturmak üzere kullanma hakkında bilgi için bkz. [değişken yineleme](copy-variables.md).

Değişkenlerin nasıl kullanılacağına ilişkin örnekler için bkz. [ARM şablonundaki değişkenler](template-variables.md).

## <a name="functions"></a>İşlevler

Şablonunuz içinde kendi işlevlerinizi oluşturabilirsiniz. Bu işlevler, şablonunuzda kullanıma sunulmuştur. Genellikle, şablonunuzun tamamında yinelemek istemediğiniz karmaşık ifadeleri tanımlarsınız. Şablonlarda desteklenen ifadelerden ve [işlevlerden](template-functions.md) Kullanıcı tanımlı işlevler oluşturursunuz.

Bir Kullanıcı işlevi tanımlarken bazı kısıtlamalar vardır:

* İşlev değişkenlere erişemez.
* İşlevi yalnızca işlevinde tanımlanan parametreleri kullanabilir. Kullanıcı tanımlı bir işlev içinde [Parameters işlevini](template-functions-deployment.md#parameters) kullandığınızda, bu işlevin parametreleriyle sınırlandırılırsınız.
* İşlev, Kullanıcı tanımlı diğer işlevleri çağıramaz.
* İşlev, [başvuru işlevini](template-functions-resource.md#reference)kullanamaz.
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
| işlev adı |Yes |Özel işlevin adı. İşlevi çağrılırken, işlev adını ad alanıyla birleştirin. Örneğin, contoso ad alanında adlı bir işlevi çağırmak için `uniqueName` kullanın `"[contoso.uniqueName()]"` . |
| parametre-adı |No |Özel işlev içinde kullanılacak parametrenin adı. |
| parametre-değer |No |Parametre değerinin türü. İzin verilen türler ve değerler **dize**, **SecureString**, **int**, **bool**, **nesne**, **secureobject** ve **dizidir**. |
| çıkış türü |Yes |Çıkış değerinin türü. Çıkış değerleri işlev giriş parametreleriyle aynı türleri destekler. |
| çıkış değeri |Yes |İşlev tarafından değerlendirilen ve döndürülen şablon dili ifadesi. |

Özel işlevlerin nasıl kullanılacağına ilişkin örnekler için, bkz. [ARM şablonunda Kullanıcı tanımlı işlevler](template-user-defined-functions.md).

## <a name="resources"></a>Kaynaklar

`resources`Bölümünde, dağıtılan veya güncellenen kaynakları tanımlarsınız.

Kaynakları aşağıdaki yapıyla tanımlarsınız:

```json
"resources": [
  {
      "condition": "<true-to-deploy-this-resource>",
      "type": "<resource-provider-namespace/resource-type-name>",
      "apiVersion": "<api-version-of-resource>",
      "name": "<name-of-the-resource>",
      "comments": "<your-reference-notes>",
      "location": "<location-of-resource>",
      "dependsOn": [
          "<array-of-related-resource-names>"
      ],
      "tags": {
          "<tag-name1>": "<tag-value1>",
          "<tag-name2>": "<tag-value2>"
      },
      "sku": {
          "name": "<sku-name>",
          "tier": "<sku-tier>",
          "size": "<sku-size>",
          "family": "<sku-family>",
          "capacity": <sku-capacity>
      },
      "kind": "<type-of-resource>",
      "scope": "<target-scope-for-extension-resources>",
      "copy": {
          "name": "<name-of-copy-loop>",
          "count": <number-of-iterations>,
          "mode": "<serial-or-parallel>",
          "batchSize": <number-to-deploy-serially>
      },
      "plan": {
          "name": "<plan-name>",
          "promotionCode": "<plan-promotion-code>",
          "publisher": "<plan-publisher>",
          "product": "<plan-product>",
          "version": "<plan-version>"
      },
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
      "resources": [
          "<array-of-child-resources>"
      ]
  }
]
```

| Öğe adı | Gerekli | Açıklama |
|:--- |:--- |:--- |
| koşul | No | Bu dağıtım sırasında kaynağın sağlanıp sağlanmayacağını belirten Boole değeri. Ne zaman `true` , kaynak dağıtım sırasında oluşturulur. Ne zaman `false` , bu dağıtım için kaynak atlanır. [Koşula](conditional-resource-deployment.md)bakın. |
| tür |Yes |Kaynağın türü. Bu değer, kaynak sağlayıcının ve kaynak türünün (gibi) ad alanının bir birleşimidir `Microsoft.Storage/storageAccounts` . Kullanılabilir değerleri anlamak için bkz. [şablon başvurusu](/azure/templates/). Bir alt kaynak için, türün biçimi üst kaynak içinde iç içe veya üst kaynak dışında tanımlanmış olmasına bağlıdır. Bkz. [alt kaynaklar için ad ve tür ayarlama](child-resource-name-type.md). |
| apiVersion |Yes |Kaynağı oluşturmak için kullanılacak REST API sürümü. Yeni bir şablon oluştururken, bu değeri dağıtmakta olduğunuz kaynağın en son sürümüne ayarlayın. Şablon gerektiği gibi çalıştığı sürece aynı API sürümünü kullanmaya devam edin. Aynı API sürümünü kullanmaya devam ederek, şablonunuzun nasıl çalıştığını değiştiren yeni bir API sürümünün riskini en aza indirirsiniz. API sürümünü, yalnızca sonraki bir sürümde sunulan yeni bir özelliği kullanmak istediğinizde güncelleştirmeyi düşünün. Kullanılabilir değerleri anlamak için bkz. [şablon başvurusu](/azure/templates/). |
| name |Yes |Kaynağın adı. Ad, RFC3986 içinde tanımlanan URI bileşen kısıtlamalarına uymalıdır. Kaynak adını dış taraflar üzerinde kullanıma sunan Azure Hizmetleri, başka bir kimliği sızma girişimi olmadığından emin olmak için adı doğrular. Bir alt kaynak için, adın biçimi üst kaynak içinde iç içe veya üst kaynak dışında tanımlanmış olmasına bağlıdır. Bkz. [alt kaynaklar için ad ve tür ayarlama](child-resource-name-type.md). |
| açıklamaları |No |Şablonunuzda kaynakları belgelemek için notlarınız. Daha fazla bilgi için bkz. [şablonlarda açıklamalar](template-syntax.md#comments). |
| location |Değişir |Belirtilen kaynağın desteklenen coğrafi konumları. Kullanılabilir konumlardan herhangi birini seçebilirsiniz, ancak genellikle kullanıcılarınıza yakın olan bir seçim yapmak mantıklı olur. Genellikle, aynı bölgedeki birbirleriyle etkileşim kuran kaynakları yerleştirmek de anlamlı hale gelir. Çoğu kaynak türü bir konum gerektirir, ancak bazı türler (örneğin, rol ataması) bir konum gerektirmez. Bkz. [kaynak konumunu ayarlama](resource-location.md). |
| dependsOn |No |Bu kaynak dağıtılmadan önce dağıtılması gereken kaynaklar. Kaynak Yöneticisi, kaynaklar arasındaki bağımlılıkları değerlendirir ve doğru sırayla dağıtır. Kaynaklar birbirine bağımlı olmadığında, paralel olarak dağıtılır. Değer, kaynak adlarının veya kaynak benzersiz tanımlayıcılarının virgülle ayrılmış bir listesi olabilir. Yalnızca bu şablonda dağıtılan kaynakları listeleyin. Bu şablonda tanımlı olmayan kaynaklar zaten var olmalıdır. Dağıtımınızı yavaşlatabilir ve dairesel bağımlılıklar oluşturduklarında gereksiz bağımlılıkları eklemekten kaçının. Bağımlılıkları ayarlama hakkında yönergeler için bkz. [ARM şablonlarında kaynak dağıtma sırasını tanımlama](define-resource-dependency.md). |
| etiketler |No |Kaynakla ilişkili Etiketler. Aboneliğiniz genelinde kaynakları mantıksal olarak düzenlemek için etiketleri uygulayın. |
| isteyin | No | Bazı kaynaklar, dağıtılacak SKU 'YU tanımlayan değerlere izin verir. Örneğin, bir depolama hesabı için artıklık türünü belirtebilirsiniz. |
| denetlenmesi | No | Bazı kaynaklar, dağıttığınız kaynak türünü tanımlayan bir değere izin verir. Örneğin, oluşturulacak Cosmos DB türünü belirtebilirsiniz. |
| scope | No | Scope özelliği yalnızca [uzantı kaynak türleri](../management/extension-resource-types.md)için kullanılabilir. Dağıtım kapsamından farklı bir kapsam belirtirken bunu kullanın. [ARM şablonlarındaki uzantı kaynakları için kapsam ayarlama](scope-extension-resources.md)bölümüne bakın. |
| kopyalama |No |Birden fazla örnek gerekliyse, oluşturulacak kaynak sayısı. Varsayılan mod paraleldir. Tüm veya kaynakların aynı anda dağıtılmasını istemiyorsanız seri modunu belirtin. Daha fazla bilgi için bkz. [Azure Resource Manager çeşitli kaynak örnekleri oluşturma](copy-resources.md). |
| plan | No | Bazı kaynaklar, dağıtılacak planı tanımlayan değerlere izin verir. Örneğin, bir sanal makine için Market görüntüsünü belirtebilirsiniz. |
| properties |No |Kaynağa özgü yapılandırma ayarları. Özelliklerin değerleri, kaynağı oluşturmak için REST API işlem (PUT yöntemi) için istek gövdesinde sağladığınız değerlerle aynıdır. Ayrıca, bir özelliğin birkaç örneğini oluşturmak için bir kopya dizisi belirtebilirsiniz. Kullanılabilir değerleri anlamak için bkz. [şablon başvurusu](/azure/templates/). |
| kaynaklar |No |Tanımlanmakta olan kaynağa bağlı olan alt kaynaklar. Yalnızca üst kaynağın şemasına izin verilen kaynak türlerini sağlayın. Üst kaynağın bağımlılığı örtük değildir. Bu bağımlılığı açıkça tanımlamanız gerekir. Bkz. [alt kaynaklar için ad ve tür ayarlama](child-resource-name-type.md). |

## <a name="outputs"></a>Çıkışlar

`outputs`Bölümünde, dağıtımdan döndürülen değerleri belirtirsiniz. Genellikle, dağıtılan kaynaklardan değerleri döndürürler.

Aşağıdaki örnek, bir çıkış tanımının yapısını gösterir:

```json
"outputs": {
  "<output-name>": {
    "condition": "<boolean-value-whether-to-output-value>",
    "type": "<type-of-output-value>",
    "value": "<output-value-expression>",
    "copy": {
      "count": <number-of-iterations>,
      "input": <values-for-the-variable>
    }
  }
}
```

| Öğe adı | Gerekli | Açıklama |
|:--- |:--- |:--- |
| çıkış adı |Yes |Çıkış değerinin adı. Geçerli bir JavaScript tanımlayıcısı olmalıdır. |
| koşul |No | Bu çıkış değerinin döndürülüp döndürülmeyeceğini belirten Boolean değeri. Ne zaman `true` , bu değer dağıtımın çıktısına dahil edilir. Ne zaman `false` , bu dağıtım için çıkış değeri atlanır. Belirtilmediğinde, varsayılan değer `true` . |
| tür |Yes |Çıkış değerinin türü. Çıkış değerleri, şablon giriş parametreleriyle aynı türleri destekler. Çıktı türü için **SecureString** belirtirseniz, değer dağıtım geçmişinde gösterilmez ve başka bir şablondan alınamaz. Birden fazla şablonda gizli bir değer kullanmak için, gizli anahtarı bir Key Vault depolayın ve parametre dosyasındaki gizli dizi başvurusu yapın. Daha fazla bilgi için bkz. [dağıtım sırasında güvenli parametre değeri geçirmek için Azure Key Vault kullanma](key-vault-parameter.md). |
| değer |No |Değerlendirilen ve çıkış değeri olarak döndürülen şablon dili ifadesi. **Değer** ya da **kopya** belirtin. |
| kopyalama |No | Bir çıktı için birden fazla değer döndürmek için kullanılır. **Değer** veya **kopya** belirtin. Daha fazla bilgi için bkz. [ARM şablonlarındaki çıkış yinelemesi](copy-outputs.md). |

Çıkışları kullanma örnekleri için bkz. [ARM şablonunda çıktılar](template-outputs.md).

<a id="comments"></a>

## <a name="comments-and-metadata"></a>Açıklamalar ve meta veriler

Şablonunuza açıklama ve meta veri eklemek için birkaç seçeneğiniz vardır.

### <a name="comments"></a>Yorumlar

Satır içi açıklamalar için ya da kullanabilirsiniz, `//` `/* ... */` ancak bu söz dizimi tüm araçlarla birlikte çalışmaz. Bu açıklama stilini eklerseniz, kullandığınız araçların satır içi JSON açıklamalarını desteklemesini sağlayın.

> [!NOTE]
> Azure CLı 'yi sürüm 2.3.0 veya daha eski bir sürümle kullanarak açıklamaları ile şablonları dağıtmak için anahtarını kullanmanız gerekir `--handle-extended-json-format` .

```json
{
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2018-10-01",
  "name": "[variables('vmName')]", // to customize name, change it in variables
  "location": "[parameters('location')]", //defaults to resource group location
  "dependsOn": [ /* storage account and network interface must be deployed first */
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
  ],
```

Visual Studio Code, [Azure Resource Manager araçları uzantısı](quickstart-create-templates-use-visual-studio-code.md) bir ARM şablonunu otomatik olarak algılayabilir ve dil modunu değiştirebilir. Visual Studio Code sağ alt köşesinde **Azure Resource Manager şablon** görürseniz, satır içi açıklamaları kullanabilirsiniz. Satır içi açıklamalar artık geçersiz olarak işaretlenmemiştir.

![Şablon modunu Visual Studio Code Azure Resource Manager](./media/template-syntax/resource-manager-template-editor-mode.png)

### <a name="metadata"></a>Meta veri

`metadata`Şablonunuzda neredeyse her yerde bir nesne ekleyebilirsiniz. Kaynak Yöneticisi nesneyi yoksayar, ancak JSON düzenleyiciniz özelliğin geçerli olmadığını uyarabilir. Nesnesinde, ihtiyacınız olan özellikleri tanımlayın.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "metadata": {
    "comments": "This template was developed for demonstration purposes.",
    "author": "Example Name"
  },
```

İçin `parameters` , `metadata` özelliği olan bir nesne ekleyin `description` .

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

![Parametre ipucunu göster](./media/template-syntax/show-parameter-tip.png)

İçin `resources` bir `comments` öğe veya `metadata` nesne ekleyin. Aşağıdaki örnek hem bir öğesi hem de `comments` bir `metadata` nesnesi gösterir.

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2018-07-01",
    "name": "[concat('storage', uniqueString(resourceGroup().id))]",
    "comments": "Storage account used to store VM disks",
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

İçin `outputs` , `metadata` Çıkış değerine bir nesne ekleyin.

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

`metadata`Kullanıcı tanımlı işlevlere bir nesne ekleyemezsiniz.

## <a name="multi-line-strings"></a>Çok satırlı dizeler

Bir dizeyi birden çok satıra kesebilirsiniz. Örneğin, `location` AŞAĞıDAKI JSON örneğinde, özelliğine ve açıklamalardan birine bakın.

```json
{
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2018-10-01",
  "name": "[variables('vmName')]", // to customize name, change it in variables
  "location": "[
    parameters('location')
    ]", //defaults to resource group location
  /*
    storage account and network interface
    must be deployed first
  */
  "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
  ],
```

> [!NOTE]
> Azure CLı 'yi sürüm 2.3.0 veya daha eski bir sürümle kullanarak çok satırlı dizeler içeren şablonlar dağıtmak için anahtarını kullanmanız gerekir `--handle-extended-json-format` .

## <a name="next-steps"></a>Sonraki adımlar

* Farklı türlerde çözümler için tam şablonları görüntülemek üzere bkz. [Azure Hızlı Başlangıç Şablonları](https://azure.microsoft.com/documentation/templates/).
* Bir şablon içinden kullanabileceğiniz işlevlerle ilgili ayrıntılar için bkz. [ARM şablon işlevleri](template-functions.md).
* Dağıtım sırasında birkaç şablonu birleştirmek için bkz. [Azure kaynaklarını dağıttığınızda bağlı ve iç içe Şablonlar kullanma](linked-templates.md).
* Şablon oluşturma hakkında öneriler için bkz. [ARM şablonu en iyi uygulamaları](template-best-practices.md).
* Yaygın soruların yanıtları için bkz. [ARM şablonları hakkında sık sorulan sorular](frequently-asked-questions.md).
