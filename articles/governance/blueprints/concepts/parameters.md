---
title: Dinamik şemaları oluşturmak için parametreleri kullanma
description: Statik ve dinamik parametreler hakkında bilgi edinin ve bunların güvenli ve dinamik planlar oluşturmak için nasıl kullanılacağını öğrenin.
ms.date: 03/12/2019
ms.topic: conceptual
ms.openlocfilehash: 31fff8fddd8e1969ef0d33047ae070f6302c9c83
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/16/2019
ms.locfileid: "74128925"
---
# <a name="creating-dynamic-blueprints-through-parameters"></a>Parametreler aracılığıyla dinamik planlar oluşturma

Çeşitli yapıtlar (örneğin, kaynak grupları, Kaynak Yöneticisi şablonları, ilkeler veya rol atamaları) ile tam olarak tanımlanmış bir şema, Azure 'da nesnelerin hızlı bir şekilde oluşturulmasını ve tutarlı olarak oluşturulmasını sağlar. Bu yeniden kullanılabilir Tasarım desenlerinin ve kapsayıcılarının esnek kullanımını etkinleştirmek için, Azure şemaları parametreleri destekler. Parametresi, şeması tarafından dağıtılan yapıtların özelliklerini değiştirmek için hem tanım hem de atama sırasında esneklik oluşturur.

Kaynak grubu yapıtı basit bir örnektir. Bir kaynak grubu oluşturulduğunda, sağlanması gereken iki zorunlu değer vardır: ad ve konum. Blueprint verilerinize bir kaynak grubu eklerken, parametreler yoksa, Blueprint 'in her kullanımı için bu adı ve konumu tanımlarsınız. Bu yineleme, aynı kaynak grubunda yapılar oluşturmak için şema 'in her kullanımına neden olur. Bu kaynak grubundaki kaynaklar çoğaltılır ve bir çakışmaya neden olur.

> [!NOTE]
> Aynı ada sahip bir kaynak grubu dahil olmak üzere iki farklı şema için bir sorun değildir.
> Şemayı içeren bir kaynak grubu zaten mevcutsa, şema bu kaynak grubunda ilgili yapıtları oluşturmaya devam eder. Bu, bir abonelikte aynı ada ve kaynak türüne sahip iki kaynak bulunmadığından bir çakışmaya neden olabilir.

Bu sorunun çözümü parametrelerdir. Planlar, bir aboneliğe atama sırasında yapıtın her özelliğinin değerini tanımlamanızı sağlar. Parametresi, bir kaynak grubu ve diğer kaynakları çakışma olmadan tek bir abonelik içinde oluşturan bir şema yeniden kullanılmasını mümkün kılar.

## <a name="blueprint-parameters"></a>Şema parametreleri

REST API aracılığıyla parametreler, şema üzerinde oluşturulabilir. Bu parametreler, desteklenen yapıtların her birinde parametrelerden farklıdır. Şema üzerinde bir parametre oluşturulduğunda bu şema, bu şema içindeki yapıtlar tarafından kullanılabilir. Kaynak grubunun adlandırılmasıyla ilgili önek bir örnek olabilir. Yapıt, "çoğunlukla dinamik" bir parametre oluşturmak için şema parametresini kullanabilir. Parametresi atama sırasında da tanımlanabilir, bu model adlandırma kurallarına uygun olabilecek bir tutarlılık sağlar. Adımlar için bkz. [statik parametreleri ayarlama-şema level parametresi](#blueprint-level-parameter).

### <a name="using-securestring-and-secureobject-parameters"></a>SecureString ve secureObject parametrelerini kullanma

Kaynak Yöneticisi Şablon _yapıtı_ **SecureString** ve **secureobject** türlerindeki parametreleri desteklese de Azure şemaları, her birinin bir Azure Key Vault bağlanmasını gerektirir.
Bu güvenlik önlemi, gizli dizileri depolar ve güvenli desenlerin çalışmasını teşvik eder. Azure şemaları, Kaynak Yöneticisi şablonu _yapıtında_güvenli parametrelerin dahil edilmesini algılayan bu güvenlik ölçüsünü destekler. Hizmet daha sonra, algılanan güvenli parametre başına aşağıdaki Key Vault özellikleri için atama sırasında uyarır:

- Key Vault kaynak KIMLIĞI
- Key Vault gizli dizi adı
- Key Vault gizli sürümü

Şema ataması **sistem tarafından atanan yönetilen kimlik**kullanıyorsa, başvurulan Key Vault şema tanımının atandığı abonelikte aynı _olması gerekir_ .

Şema ataması **Kullanıcı tarafından atanan yönetilen kimlik**kullanıyorsa, _başvurulan Key Vault merkezi bir abonelikte bulunabilir._ Yönetilen kimliğe, şema atamasından önce Key Vault uygun haklara sahip olmalıdır.

> [!IMPORTANT]
> Her iki durumda da, Key Vault **erişim ilkeleri** sayfasında yapılandırılmış **şablon dağıtımı Için Azure Resource Manager erişimi etkinleştir** olmalıdır. Bu özelliğin nasıl etkinleştirileceği hakkında yönergeler için bkz. [Key Vault-şablon dağıtımını etkinleştirme](../../../managed-applications/key-vault-access.md#enable-template-deployment).

Azure Key Vault hakkında daha fazla bilgi için bkz. [Key Vault genel bakış](../../../key-vault/key-vault-overview.md).

## <a name="parameter-types"></a>Parametre türleri

### <a name="static-parameters"></a>Statik parametreler

Şema 'in tanımında tanımlanan bir parametre değerine **statik parametre**denir, çünkü şema 'in her kullanımı yapıyı bu statik değeri kullanarak dağıtacaktır. Kaynak grubu örneğinde, kaynak grubunun adı için anlamlı olmasa da konum için anlamlı olabilir. Ardından, şema 'in her ataması, kaynak grubunu, atama sırasında her ne olursa olsun, aynı konumda oluşturur. Bu esneklik, atama sırasında nelerin değiştirilebileceklerini ve gerekli olarak tanımladıklarınızı seçmenize olanak sağlar.

#### <a name="setting-static-parameters-in-the-portal"></a>Portalda statik parametreleri ayarlama

1. Sol bölmedeki **tüm hizmetler** ' i seçin. **Şemaları**arayın ve seçin.

1. Soldaki sayfadan **Blueprint tanımlarını** seçin.

1. Mevcut bir şema öğesine tıklayın ve ardından şemayı **Düzenle** ' ye tıklayın veya **+ şema oluştur** ' a tıklayın ve **temel** bilgiler sekmesindeki bilgileri doldurun.

1. **İleri: yapıtlar** ' e tıklayın veya **yapılar** sekmesine tıklayın.

1. Parametre seçeneklerine sahip şema öğesine eklenen yapılar, **Parameters** sütununda **doldurulmuş X/Y parametrelerini** görüntüler. Yapıt parametrelerini düzenlemek için yapıt satırına tıklayın.

   ![Şema tanımında şema parametreleri](../media/parameters/parameter-column.png)

1. **Yapıtı Düzenle** sayfası, tıklanan yapıya uygun değer seçeneklerini görüntüler. Yapıtın üzerindeki her parametrenin bir başlığı, bir değer kutusu ve bir onay kutusu vardır. Bir **statik parametre**yapmak için kutuyu işaretsiz olarak ayarlayın. Aşağıdaki örnekte, yalnızca _konum_ işaretlenmemiştir ve _kaynak grubu adı_ denetlendiğinden bir **statik parametredir** .

   ![Şema yapıtı üzerinde şema statik parametrelerini Yazdır](../media/parameters/static-parameter.png)

#### <a name="setting-static-parameters-from-rest-api"></a>REST API statik parametreleri ayarlama

Her bir REST API URI'sinde kendi değerlerinizle değiştirmeniz gereken değişkenler bulunur:

- `{YourMG}` - Yönetim grubunuzun adıyla değiştirin
- `{subscriptionId}` - Abonelik kimliğinizle değiştirin

##### <a name="blueprint-level-parameter"></a>Blueprint düzey parametresi

REST API aracılığıyla bir şema oluştururken, [şema parametreleri](#blueprint-parameters)oluşturmak mümkündür. Bunu yapmak için aşağıdaki REST API URI ve gövde biçimini kullanın:

- REST API URI'si

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint?api-version=2018-11-01-preview
  ```

- İstek Gövdesi

  ```json
  {
      "properties": {
          "description": "This blueprint has blueprint level parameters.",
          "targetScope": "subscription",
          "parameters": {
              "owners": {
                  "type": "array",
                  "metadata": {
                      "description": "List of AAD object IDs that is assigned Owner role at the resource group"
                  }
              }
          },
          "resourceGroups": {
              "storageRG": {
                  "description": "Contains the resource template deployment and a role assignment."
              }
          }
      }
  }
  ```

Şema level parametresi oluşturulduktan sonra, bu şema için eklenen yapıtlar üzerinde kullanılabilir.
Aşağıdaki REST API örnek, şema üzerinde bir rol atama yapıtı oluşturur ve şema Level parametresini kullanır.

- REST API URI'si

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/roleOwner?api-version=2018-11-01-preview
  ```

- İstek Gövdesi

  ```json
  {
      "kind": "roleAssignment",
      "properties": {
          "resourceGroup": "storageRG",
          "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
          "principalIds": "[parameters('owners')]"
      }
  }
  ```

Bu örnekte, **Prenalids** özelliği `[parameters('owners')]`bir değeri kullanarak **sahipler** şeması düzeyi parametresini kullanır. Bir şema üzerinde bir parametre ayarlamak bir şema düzeyi parametresini kullanarak bir **statik parametre**örneği olmaya devam eder. Şema level parametresi, şema atama sırasında ayarlanamaz ve her atamada aynı değer olacaktır.

##### <a name="artifact-level-parameter"></a>Yapıt düzeyi parametresi

Bir yapıtın üzerinde **statik parametreler** oluşturmak benzerdir, ancak `parameters()` işlevini kullanmak yerine düz bir değer alır. Aşağıdaki örnek iki statik parametre, **TagName** ve **tagvalue**oluşturur. Her bir üzerinde değer doğrudan sağlanır ve bir işlev çağrısı kullanmaz.

- REST API URI'si

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/policyStorageTags?api-version=2018-11-01-preview
  ```

- İstek Gövdesi

  ```json
  {
      "kind": "policyAssignment",
      "properties": {
          "description": "Apply storage tag and the parameter also used by the template to resource groups",
          "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/49c88fc8-6fd1-46fd-a676-f12d1d3a4c71",
          "parameters": {
              "tagName": {
                  "value": "StorageType"
              },
              "tagValue": {
                  "value": "Premium_LRS"
              }
          }
      }
  }
  ```

### <a name="dynamic-parameters"></a>Dinamik parametreler

Statik parametrenin tersi, **dinamik bir parametredir**. Bu parametre Blueprint üzerinde tanımlanmamıştır, ancak bunun yerine Blueprint 'in her ataması sırasında tanımlanmıştır. Kaynak grubu örneğinde, **dinamik bir parametre** kullanımı, kaynak grubu adı için anlamlı hale gelir. Blueprint 'in her ataması için farklı bir ad sağlar. Şema işlevlerinin bir listesi için, bkz. şema [işlevleri](../reference/blueprint-functions.md) başvurusu.

#### <a name="setting-dynamic-parameters-in-the-portal"></a>Portalda dinamik parametreleri ayarlama

1. Sol bölmedeki **tüm hizmetler** ' i seçin. **Şemaları**arayın ve seçin.

1. Soldaki sayfadan **Blueprint tanımlarını** seçin.

1. Atamak istediğiniz şema öğesine sağ tıklayın. Şema **ata veya atamak istediğiniz şema '** i seçin, ardından şema **ata** düğmesine tıklayın.

1. **Şeması ata** sayfasında, **yapıt parametreleri** bölümünü bulun. En az bir **dinamik parametresi** olan her yapıt yapıt ve yapılandırma seçeneklerini görüntüler. Blueprint 'i atamadan önce parametrelere gerekli değerler sağlayın. Aşağıdaki örnekte _ad_ , şema atamasını tamamlayacak şekilde tanımlanması gereken **dinamik bir parametredir** .

   ![Şema ataması sırasında şema dinamik parametresi](../media/parameters/dynamic-parameter.png)

#### <a name="setting-dynamic-parameters-from-rest-api"></a>REST API dinamik parametreleri ayarlama

Atama sırasında **dinamik parametrelerin** ayarlanması, doğrudan değer girilerek yapılır. [Parametreler ()](../reference/blueprint-functions.md#parameters)gibi bir işlev kullanmak yerine, belirtilen değer uygun bir dizedir. Bir kaynak grubunun yapıtları, "şablon adı", **ad**ve **konum** özellikleriyle tanımlanır. Dahil edilen yapıt için diğer tüm parametreler, **\<adı\>** ve **değer** anahtarı çifti **parametreleri** altında tanımlanmıştır. Şema, atama sırasında sağlanmayan dinamik bir parametre için yapılandırılmışsa, atama başarısız olur.

- REST API URI'si

  ```http
  PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Blueprint/blueprintAssignments/assignMyBlueprint?api-version=2018-11-01-preview
  ```

- İstek Gövdesi

  ```json
  {
      "properties": {
          "blueprintId": "/providers/Microsoft.Management/managementGroups/{YourMG}  /providers/Microsoft.Blueprint/blueprints/MyBlueprint",
          "resourceGroups": {
              "storageRG": {
                  "name": "StorageAccount",
                  "location": "eastus2"
              }
          },
          "parameters": {
              "storageAccountType": {
                  "value": "Standard_GRS"
              },
              "tagName": {
                  "value": "CostCenter"
              },
              "tagValue": {
                  "value": "ContosoIT"
              },
              "contributors": {
                  "value": [
                      "7be2f100-3af5-4c15-bcb7-27ee43784a1f",
                      "38833b56-194d-420b-90ce-cff578296714"
                  ]
                },
              "owners": {
                  "value": [
                      "44254d2b-a0c7-405f-959c-f829ee31c2e7",
                      "316deb5f-7187-4512-9dd4-21e7798b0ef9"
                  ]
              }
          }
      },
      "identity": {
          "type": "systemAssigned"
      },
      "location": "westus"
  }
  ```

## <a name="next-steps"></a>Sonraki adımlar

- [Şema işlevlerinin](../reference/blueprint-functions.md)listesine bakın.
- [Şema yaşam döngüsü](lifecycle.md) hakkında bilgi edinin.
- [Şema sıralama düzenini](sequencing-order.md) özelleştirmeyi öğrenin.
- [Şema kaynak kilitleme](resource-locking.md) özelliğini kullanmayı öğrenin.
- [Mevcut atamaları güncelleştirmeyi](../how-to/update-existing-assignments.md) öğrenin.
- [Genel sorun giderme](../troubleshoot/general.md) adımlarıyla şema atama sorunlarını giderin.