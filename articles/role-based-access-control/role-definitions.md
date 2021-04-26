---
title: Azure rol tanımlarını anlama-Azure RBAC
description: Azure kaynakları için ayrıntılı erişim yönetimi için Azure rol tabanlı erişim denetimi 'nde (Azure RBAC) Azure rol tanımları hakkında bilgi edinin.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: conceptual
ms.workload: identity
ms.date: 03/22/2021
ms.author: rolyon
ms.custom: ''
ms.openlocfilehash: 5b2ec3289d187997763ee0d9280a777d4fa1f396
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104801766"
---
# <a name="understand-azure-role-definitions"></a>Azure rol tanımlarını anlama

Bir Azure rolünün nasıl çalıştığını anlamaya çalışıyorsanız veya kendi [Azure özel rolünüzü](custom-roles.md)oluşturuyorsanız, rollerinin nasıl tanımlandığını anlamanız yararlı olur. Bu makalede, rol tanımlarının ayrıntıları açıklanmakta ve bazı örnekler sağlanmaktadır.

## <a name="role-definition"></a>Rol tanımı

*Rol tanımı*, izinlerden oluşan bir koleksiyondur. Bazen yalnızca bir *rol* olarak adlandırılır. Rol tanımı; okuma, yazma ve silme gibi gerçekleştirilebilecek işlemleri listeler. Ayrıca, temel alınan verilerle ilgili olarak izin verilen işlemler veya işlemlerden çıkarılan işlemleri de listeleyebilir.

Aşağıda, Azure PowerShell kullanılarak görüntülenmediğinde rol tanımındaki özelliklerin bir örneği gösterilmektedir:

```
Name
Id
IsCustom
Description
Actions []
NotActions []
DataActions []
NotDataActions []
AssignableScopes []
```

Aşağıda, Azure portal, Azure CLı veya REST API kullanılarak görüntülenmediğinde rol tanımındaki özelliklerin bir örneği gösterilmektedir:

```
roleName
name
type
description
actions []
notActions []
dataActions []
notDataActions []
assignableScopes []
```

Aşağıdaki tabloda rol özelliklerinin anlamı açıklanmaktadır.

| Özellik | Açıklama |
| --- | --- |
| `Name`</br>`roleName` | Rolün görünen adı. |
| `Id`</br>`name` | Rolün benzersiz KIMLIĞI. Yerleşik roller, bulutlar genelinde aynı rol KIMLIĞINE sahiptir. |
| `IsCustom`</br>`roleType` | Bunun özel bir rol olup olmadığını gösterir. `true`Özel roller için veya olarak ayarlayın `CustomRole` . `false`Yerleşik roller için veya olarak ayarlayın `BuiltInRole` . |
| `Description`</br>`description` | Rolün açıklaması. |
| `Actions`</br>`actions` | Rolün gerçekleştirilmesine izin verdiği yönetim işlemlerini belirten dizeler dizisi. |
| `NotActions`</br>`notActions` | İzin verilen ' dan dışlanan yönetim işlemlerini belirten dizeler dizisi `Actions` . |
| `DataActions`</br>`dataActions` | Rolün bu nesne içindeki verilerinize gerçekleştirilmesine izin verdiği veri işlemlerini belirten dizeler dizisi. |
| `NotDataActions`</br>`notDataActions` | İzin verilen ' dan dışlanan veri işlemlerini belirten dizeler dizisi `DataActions` . |
| `AssignableScopes`</br>`assignableScopes` | Rolün atama için kullanılabilir olduğu kapsamları belirten dizeler dizisi. |

### <a name="operations-format"></a>İşlemler biçimi

İşlemler, aşağıdaki biçime sahip dizeler ile belirtilir:

- `{Company}.{ProviderName}/{resourceType}/{action}`

Bir `{action}` işlem dizesinin bölümü, bir kaynak türü üzerinde gerçekleştirebileceğiniz işlem türünü belirtir. Örneğin, içinde aşağıdaki alt dizeleri görürsünüz `{action}` :

| Eylem alt dizesi    | Description         |
| ------------------- | ------------------- |
| `*` | Joker karakter karakteri dize ile eşleşen tüm işlemlere erişim verir. |
| `read` | Okuma işlemlerini (GET) etkinleştirilir. |
| `write` | Yazma işlemlerini (PUT veya PATCH) etkinleştirilir. |
| `action` | Sanal makineleri yeniden Başlat (POST) gibi özel işlemleri sunar. |
| `delete` | Silme işlemlerini (SILME) mümkün. |

### <a name="role-definition-example"></a>Rol tanımı örneği

İşte Azure PowerShell ve Azure CLı 'de gösterildiği gibi [katkıda](built-in-roles.md#contributor) bulunan rol tanımı. `Actions` altındaki joker karakter (`*`) işlemi, bu role atanan sorumlunun tüm eylemleri gerçekleştirebileceğini gösterir veya başka bir deyişle her şeyi yönetebilir. Bu, Azure yeni kaynak türleri ekledikçe gelecekte tanımlanacak eylemleri de içerir. `NotActions` altındaki işlemler `Actions` işlemlerinden çıkarılır. [Katkıda](built-in-roles.md#contributor) bulunan rolü söz konusu olduğunda, `NotActions` Bu rolün kaynaklara erişimi yönetme ve ayrıca Azure Blueprint atamalarını yönetme yeteneğini ortadan kaldırır.

Azure PowerShell içinde gösterildiği gibi katkıda bulunan rolü:

```json
{
  "Name": "Contributor",
  "Id": "b24988ac-6180-42a0-ab88-20f7382dd24c",
  "IsCustom": false,
  "Description": "Lets you manage everything except access to resources.",
  "Actions": [
    "*"
  ],
  "NotActions": [
    "Microsoft.Authorization/*/Delete",
    "Microsoft.Authorization/*/Write",
    "Microsoft.Authorization/elevateAccess/Action",
    "Microsoft.Blueprint/blueprintAssignments/write",
    "Microsoft.Blueprint/blueprintAssignments/delete"
  ],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
    "/"
  ]
}
```

Azure CLı 'de gösterildiği gibi katkıda bulunan rolü:

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage everything except access to resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
  "name": "b24988ac-6180-42a0-ab88-20f7382dd24c",
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "notActions": [
        "Microsoft.Authorization/*/Delete",
        "Microsoft.Authorization/*/Write",
        "Microsoft.Authorization/elevateAccess/Action",
        "Microsoft.Blueprint/blueprintAssignments/write",
        "Microsoft.Blueprint/blueprintAssignments/delete"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="management-and-data-operations"></a>Yönetim ve veri işlemleri

Yönetim işlemleri için rol tabanlı erişim denetimi, `Actions` `NotActions` bir rol tanımının ve özelliklerinde belirtilir. Azure 'daki yönetim işlemlerine ilişkin bazı örnekler aşağıda verilmiştir:

- Bir depolama hesabına erişimi yönetme
- Blob kapsayıcısı oluşturma, güncelleştirme veya silme
- Bir kaynak grubunu ve tüm kaynaklarını Sil

Yönetim erişimi, kapsayıcı kimlik doğrulama yönteminin "Azure AD Kullanıcı hesabı" olarak ayarlandığı ve "erişim anahtarı" olmadığı için verilerinize devralınmaz. Bu ayrım, joker karakter () içeren rollerin `*` verilerinize sınırsız erişmesini önler. Örneğin, bir kullanıcı abonelik üzerinde bir [okuyucu](built-in-roles.md#reader) rolüne sahipse, depolama hesabını görüntüleyebilirler, ancak varsayılan olarak temel alınan verileri görüntüleyemez.

Daha önce, rol tabanlı erişim denetimi veri işlemleri için kullanılmadı. Kaynak sağlayıcıları arasında değişen veri işlemlerine yönelik yetkilendirme. Yönetim işlemleri için kullanılan rol tabanlı erişim denetimi yetkilendirme modeli, veri işlemlerine genişletildi.

Veri işlemlerini desteklemek için, rol tanımına yeni veri özellikleri eklenmiştir. Veri işlemleri `DataActions` ve `NotDataActions` özelliklerinde belirtilir. Bu veri özellikleri eklenerek, yönetim ve veriler arasındaki ayrım korunur. Ayrıca joker karakter (`*`) içeren geçerli rol atamalarının aniden verilere erişim almasını da önler. Burada, `DataActions` ve `NotDataActions` özelliklerinde belirtilebilecek bazı veri işlemleri verilmiştir:

- Kapsayıcıdaki blobların listesini okuma
- Kapsayıcıda depolama blobu yazma
- Kuyruktaki iletiyi silme

Hem hem de özelliklerinde işlemler içeren [Depolama Blobu veri okuyucusu](built-in-roles.md#storage-blob-data-reader) rol tanımı aşağıda verilmiştir `Actions` `DataActions` . Bu rol, blob kapsayıcısını ve ayrıca temel alınan blob verilerini okumanızı sağlar.

Azure PowerShell içinde gösterildiği gibi Depolama Blobu veri okuyucusu rolü:

```json
{
  "Name": "Storage Blob Data Reader",
  "Id": "2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
  "IsCustom": false,
  "Description": "Allows for read access to Azure Storage blob containers and data",
  "Actions": [
    "Microsoft.Storage/storageAccounts/blobServices/containers/read",
    "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
  ],
  "NotActions": [],
  "DataActions": [
    "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read"
  ],
  "NotDataActions": [],
  "AssignableScopes": [
    "/"
  ]
}
```

Azure CLı 'de gösterildiği gibi Depolama Blobu veri okuyucusu rolü:

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read access to Azure Storage blob containers and data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
  "name": "2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/read",
        "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Blob Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

`DataActions` ve `NotDataActions` özelliklerine yalnızca veri işlemleri eklenebilir. Kaynak sağlayıcıları, özelliğini olarak ayarlayarak hangi işlemlerin veri işlemleri olduğunu belirler `isDataAction` `true` . Olduğu işlemlerin bir listesini görmek için `isDataAction` `true` bkz. [kaynak sağlayıcısı işlemleri](resource-provider-operations.md). Veri işlemlerine sahip olmayan roller `DataActions` , rol tanımında ve özelliklerine sahip olmak için gerekli değildir `NotDataActions` .

Tüm yönetim işlemi API çağrıları için yetkilendirme Azure Resource Manager tarafından işlenir. Veri işlemi API çağrıları için yetkilendirme, bir kaynak sağlayıcısı veya Azure Resource Manager tarafından işlenir.

### <a name="data-operations-example"></a>Veri işlemleri örneği

Yönetim ve veri işlemlerinin nasıl çalıştığını daha iyi anlamak için, belirli bir örneği ele alalım. Çiğdem 'e abonelik kapsamında [sahip](built-in-roles.md#owner) rolü atandı. Emre, depolama [blobu veri katılımcısı](built-in-roles.md#storage-blob-data-contributor) rolüne bir depolama hesabı kapsamında atandı. Aşağıdaki diyagramda Bu örnek gösterilmektedir.

![Rol tabanlı erişim denetimi hem yönetim hem de veri işlemlerini destekleyecek şekilde genişletildi](./media/role-definitions/rbac-management-data.png)

Gamze için [sahip](built-in-roles.md#owner) rolü ve Bob Için [Depolama Blobu veri katılımcısı](built-in-roles.md#storage-blob-data-contributor) rolü aşağıdaki eylemlere sahiptir:

Sahip

&nbsp;&nbsp;&nbsp;&nbsp;Eylem<br>
&nbsp;&nbsp;&nbsp;&nbsp;`*`

Depolama Blob Verileri Katkıda Bulunanı

&nbsp;&nbsp;&nbsp;&nbsp;Eylem<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/delete`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/read`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/write`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action`<br>
&nbsp;&nbsp;&nbsp;&nbsp;Veri eylemleri<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/move/action`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write`

Ayla bir abonelik kapsamında bir joker karakter ( `*` ) eylemi olduğundan, izinleri tüm yönetim eylemlerini gerçekleştirmeye olanak tanımak için devralınır. Gamze kapsayıcıları okuyabilir, yazabilir ve silebilir. Ancak, Çiğdem ek adımlar uygulamadan veri işlemleri gerçekleştiremez. Örneğin, varsayılan olarak, Gamze bir kapsayıcı içindeki Blobları okuyamaz. Blobları okumak için, Çiğdem 'in depolama erişim anahtarlarını alması ve bloblara erişmek için onları kullanması gerekir.

Bob 'un izinleri, `Actions` `DataActions` [Depolama Blobu veri katılımcısı](built-in-roles.md#storage-blob-data-contributor) rolünde yalnızca ve belirtilen ile kısıtlıdır. Bob, rol temelinde hem yönetim hem de veri işlemlerini gerçekleştirebilir. Örneğin Bob, belirtilen depolama hesabındaki kapsayıcıları okuyabilir, yazabilir ve silebilir ve ayrıca Blobları okuyabilir, yazabilir ve silebilir.

Depolama için yönetim ve veri düzlemi güvenliği hakkında daha fazla bilgi için bkz. [Azure depolama Güvenlik Kılavuzu](../storage/blobs/security-recommendations.md).

### <a name="what-tools-support-using-azure-roles-for-data-operations"></a>Veri işlemleri için Azure rollerini kullanarak hangi araçlar desteklenir?

Veri işlemlerini görüntülemek ve bunlarla çalışmak için, araçların veya SDK 'ların doğru sürümlerine sahip olmanız gerekir:

| Araç  | Sürüm  |
|---------|---------|
| [Azure PowerShell](/powershell/azure/install-az-ps) | 1.1.0 veya üzeri |
| [Azure CLI](/cli/azure/install-azure-cli) | 2.0.30 veya üzeri |
| [.NET için Azure](/dotnet/azure/) | 2.8.0-Önizleme veya üzeri |
| [Go için Azure SDK](/azure/go/azure-sdk-go-install) | 'sının 15.0.0 veya üzeri |
| [Java için Azure](/java/azure/) | 1.9.0 veya üzeri |
| [Python için Azure](/azure/python/) | 0.40.0 veya üzeri |
| [Ruby için Azure SDK](https://rubygems.org/gems/azure_sdk) | 0.17.1 veya üzeri |

REST API veri işlemlerini görüntülemek ve kullanmak için, **API sürümü** parametresini aşağıdaki sürüme veya daha yenisine ayarlamanız gerekir:

- 2018-07-01

## <a name="actions"></a>Eylemler

`Actions`İzin, rolün gerçekleştirilmesine izin verdiği yönetim işlemlerini belirler. Azure Kaynak sağlayıcılarının güvenli kılınabilir işlemlerini tanımlayan bir işlem dizeleri koleksiyonudur. Burada, ' de kullanılabilen yönetim işlemlerine ilişkin bazı örnekler verilmiştir `Actions` .

> [!div class="mx-tableFixed"]
> | İşlem dizesi    | Description         |
> | ------------------- | ------------------- |
> | `*/read` | Tüm Azure Kaynak sağlayıcılarının tüm kaynak türleri için okuma işlemlerine erişim izni verir.|
> | `Microsoft.Compute/*` | Microsoft. COMPUTE kaynak sağlayıcısındaki tüm kaynak türleri için tüm işlemlere erişim verir.|
> | `Microsoft.Network/*/read` | Microsoft. Network kaynak sağlayıcısındaki tüm kaynak türleri için okuma işlemlerine erişim izni verir.|
> | `Microsoft.Compute/virtualMachines/*` | Sanal makinelerin tüm işlemlerine ve alt kaynak türlerine erişim verir.|
> | `microsoft.web/sites/restart/Action` | Bir Web uygulamasını yeniden başlatma erişimine izin verir.|

## <a name="notactions"></a>NotActions

`NotActions`İzin, `Actions` joker karakter () olan izin verilen ' den çıkarılan veya dışlanan yönetim işlemlerini belirtir `*` . İzin vermek istediğiniz `NotActions` işlem kümesi, `Actions` bir joker karakter () olan öğesinden çıkarılarak daha kolay tanımlanmazsa izin kullanın `*` . Bir rol tarafından verilen erişim (geçerli izinler) işlemleri işlemlerden çıkararak hesaplanır `NotActions` `Actions` .

`Actions - NotActions = Effective management permissions`

Aşağıdaki tabloda, bir [Microsoft. CostManagement](resource-provider-operations.md#microsoftcostmanagement) joker karakter işlemi için geçerli izinlerin iki örneği gösterilmektedir:

> [!div class="mx-tableFixed"]
> | Actions | NotActions | Etkin yönetim izinleri |
> | --- | --- | --- |
> | `Microsoft.CostManagement/exports/*` | *yok* | `Microsoft.CostManagement/exports/action`</br>`Microsoft.CostManagement/exports/read`</br>`Microsoft.CostManagement/exports/write`</br>`Microsoft.CostManagement/exports/delete`</br>`Microsoft.CostManagement/exports/run/action` |
> | `Microsoft.CostManagement/exports/*` | `Microsoft.CostManagement/exports/delete` | `Microsoft.CostManagement/exports/action`</br>`Microsoft.CostManagement/exports/read`</br>`Microsoft.CostManagement/exports/write`</br>`Microsoft.CostManagement/exports/run/action` |

> [!NOTE]
> Bir kullanıcıya ' de bir işlemi dışlayan bir rol atanırsa `NotActions` ve aynı işleme erişim izni veren ikinci bir rol atanırsa, kullanıcının bu işlemi gerçekleştirmesine izin verilir. `NotActions` bir reddetme kuralı değil, belirli işlemler dışlanmanız gerektiğinde, izin verilen bir işlem kümesi oluşturmanın kolay bir yoludur.
>

### <a name="differences-between-notactions-and-deny-assignments"></a>NotActions ve reddetme atamaları arasındaki farklar

`NotActions` ve reddetme atamaları aynı değildir ve farklı amaçlara hizmet eder. `NotActions` , belirli eylemleri bir joker karakter () işleminden çıkarmak için kullanışlı bir yoldur `*` .

Bir rol ataması erişimine izin veriyorsa, atamaları Reddet, kullanıcıların belirli eylemleri gerçekleştirmesini engeller. Daha fazla bilgi için bkz. [Azure reddetme atamalarını anlama](deny-assignments.md).

## <a name="dataactions"></a>Veri eylemleri

`DataActions`İzin, rolün bu nesne içindeki verilerinize yapılmasına izin verdiği veri işlemlerini belirler. Örneğin, bir kullanıcının depolama hesabına yönelik blob veri okuma erişimi varsa, bu depolama hesabındaki Blobları okuyabilirler. İşte kullanılabilecek veri işlemlerine ilişkin bazı örnekler aşağıda verilmiştir `DataActions` .

> [!div class="mx-tableFixed"]
> | İşlem dizesi    | Description         |
> | ------------------- | ------------------- |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read` | Blob veya Blobların listesini döndürür. |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write` | Blob yazma sonucunu döndürür. |
> | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/read` | Bir ileti döndürür. |
> | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/*` | Bir ileti veya bir ileti yazmanın ya da silmenin sonucunu döndürür. |

## <a name="notdataactions"></a>NotDataActions

`NotDataActions`İzin, `DataActions` joker karakter () olan izin verilen ' den çıkarılan veya dışlanan veri işlemlerini belirtir `*` . İzin vermek istediğiniz `NotDataActions` işlem kümesi, `DataActions` bir joker karakter () olan öğesinden çıkarılarak daha kolay tanımlanmazsa izin kullanın `*` . Bir rol tarafından verilen erişim (geçerli izinler) işlemleri işlemlerden çıkararak hesaplanır `NotDataActions` `DataActions` . Her kaynak sağlayıcı, veri işlemlerini karşılamak için ilgili API kümesini sağlar.

`DataActions - NotDataActions = Effective data permissions`

Aşağıdaki tabloda bir [Microsoft. Storage](resource-provider-operations.md#microsoftstorage) joker işlemi için etkin izinlerin iki örneği gösterilmektedir:

> [!div class="mx-tableFixed"]
> | Veri eylemleri | NotDataActions | Etkin veri izinleri |
> | --- | --- | --- |
> | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/*` | *yok* | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/read`</br>`Microsoft.Storage/storageAccounts/queueServices/queues/messages/write`</br>`Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete`</br>`Microsoft.Storage/storageAccounts/queueServices/queues/messages/add/action`</br>`Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action` |
> | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/*` | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete`</br> | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/read`</br>`Microsoft.Storage/storageAccounts/queueServices/queues/messages/write`</br>`Microsoft.Storage/storageAccounts/queueServices/queues/messages/add/action`</br>`Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action` |

> [!NOTE]
> Bir kullanıcıya ' de bir veri işlemini dışlayan bir rol atanırsa `NotDataActions` ve aynı veri işlemine erişim veren ikinci bir rol atanırsa, kullanıcının bu veri işlemini gerçekleştirmesine izin verilir. `NotDataActions` , bir reddetme kuralı değil, belirli veri işlemlerinin dışlanması gerektiğinde bir izin verilen veri işlemleri kümesi oluşturmanın kolay bir yoludur.
>

## <a name="assignablescopes"></a>Astifblescopes

`AssignableScopes`Özelliği, bu rol tanımının kullanılabildiği kapsamları (Yönetim grupları, abonelikler veya kaynak grupları) belirtir. Rolü yalnızca gereken yönetim gruplarında, aboneliklerde veya kaynak gruplarında atama için kullanılabilir hale getirebilirsiniz. En az bir yönetim grubu, abonelik veya kaynak grubu kullanmanız gerekir.

Yerleşik roller `AssignableScopes` kök kapsamına ( `"/"` ) ayarlı. Kök kapsamı, rolün tüm kapsamlardaki atamaya uygun olduğunu gösterir. Geçerli atanabilir kapsamların örnekleri şunlardır:

> [!div class="mx-tableFixed"]
> | Rol atama için kullanılabilir | Örnek |
> |----------|---------|
> | Bir abonelik | `"/subscriptions/{subscriptionId1}"` |
> | İki abonelik | `"/subscriptions/{subscriptionId1}", "/subscriptions/{subscriptionId2}"` |
> | Ağ kaynak grubu | `"/subscriptions/{subscriptionId1}/resourceGroups/Network"` |
> | Bir yönetim grubu | `"/providers/Microsoft.Management/managementGroups/{groupId1}"` |
> | Yönetim grubu ve abonelik | `"/providers/Microsoft.Management/managementGroups/{groupId1}", /subscriptions/{subscriptionId1}",` |
> | Tüm kapsamlar (yalnızca yerleşik roller için geçerlidir) | `"/"` |

Özel roller hakkında daha fazla bilgi için `AssignableScopes` bkz. [Azure özel rolleri](custom-roles.md).

## <a name="next-steps"></a>Sonraki adımlar

* [Yerleşik Azure rolleri](built-in-roles.md)
* [Özel Azure rolleri](custom-roles.md)
* [Azure Kaynak sağlayıcısı işlemleri](resource-provider-operations.md)
