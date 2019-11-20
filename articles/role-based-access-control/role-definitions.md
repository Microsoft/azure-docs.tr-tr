---
title: Azure kaynakları için RBAC 'de rol tanımlarını anlama | Microsoft Docs
description: Azure kaynaklarının ayrıntılı erişim yönetimi için rol tabanlı erişim denetimi (RBAC) içindeki rol tanımları hakkında bilgi edinin.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/11/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: ''
ms.openlocfilehash: 1cd5325be7def4bc631d994f8811734e6c3cf545
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/14/2019
ms.locfileid: "70996430"
---
# <a name="understand-role-definitions-for-azure-resources"></a>Azure kaynakları için rol tanımlarını anlama

Bir rolün nasıl çalıştığını anlamaya çalışıyorsanız veya [Azure kaynakları için kendi özel rolünüzü](custom-roles.md)oluşturuyorsanız, rollerinin nasıl tanımlandığını anlamanız yararlı olur. Bu makalede, rol tanımlarının ayrıntıları açıklanmakta ve bazı örnekler sağlanmaktadır.

## <a name="role-definition-structure"></a>Rol tanımı yapısı

*Rol tanımı*, izinlerden oluşan bir koleksiyondur. Bazen yalnızca *rol* olarak da adlandırılır. Rol tanımı; okuma, yazma ve silme gibi gerçekleştirilebilecek işlemleri listeler. Ayrıca, gerçekleştirilemediği işlemleri veya temel alınan verilerle ilişkili işlemleri de listeleyebilir. Rol tanımı aşağıdaki yapıya sahiptir:

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

İşlemler, aşağıdaki biçime sahip dizeler ile belirtilir:

- `{Company}.{ProviderName}/{resourceType}/{action}`

Bir `{action}` işlem dizesinin bölümü, bir kaynak türü üzerinde gerçekleştirebileceğiniz işlem türünü belirtir. Örneğin, içinde `{action}`aşağıdaki alt dizeleri görürsünüz:

| Eylem alt dizesi    | Açıklama         |
| ------------------- | ------------------- |
| `*` | Joker karakter karakteri dize ile eşleşen tüm işlemlere erişim verir. |
| `read` | Okuma işlemlerini (GET) etkinleştirilir. |
| `write` | Yazma işlemlerini (PUT veya PATCH) etkinleştirilir. |
| `action` | Sanal makineleri yeniden Başlat (POST) gibi özel işlemleri sunar. |
| `delete` | Silme işlemlerini (SILME) mümkün. |

JSON biçiminde [katılımcı](built-in-roles.md#contributor) rolü tanımı aşağıda verilmiştir. `*` Altında`Actions` joker karakter () işlemi, bu role atanan sorumlunun tüm işlemleri gerçekleştirebileceğini veya diğer bir deyişle, her şeyi yönetebileceğini belirtir. Bu, Azure yeni kaynak türleri eklediği için gelecekte tanımlanan eylemleri içerir. Altındaki `NotActions` İşlemler`Actions`çıkarılır. [Katkıda](built-in-roles.md#contributor) bulunan rolü söz konusu olduğunda, `NotActions` bu rolün kaynaklara erişimi yönetme ve ayrıca kaynaklara erişim atama yeteneğini ortadan kaldırır.

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
    "Microsoft.Authorization/elevateAccess/Action"
  ],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
    "/"
  ]
}
```

## <a name="management-and-data-operations"></a>Yönetim ve veri işlemleri

Yönetim işlemleri için rol tabanlı erişim denetimi, `Actions` bir rol tanımının ve `NotActions` özelliklerinde belirtilir. Azure 'daki yönetim işlemlerine ilişkin bazı örnekler aşağıda verilmiştir:

- Bir depolama hesabına erişimi yönetme
- Blob kapsayıcısı oluşturma, güncelleştirme veya silme
- Bir kaynak grubunu ve tüm kaynaklarını Sil

Yönetim erişimi, kapsayıcı kimlik doğrulama yönteminin "Azure AD Kullanıcı hesabı" olarak ayarlandığı ve "erişim anahtarı" olmadığı için verilerinize devralınmaz. Bu ayrım, joker karakter (`*`) içeren rollerin verilerinize sınırsız erişmesini önler. Örneğin, bir kullanıcı abonelik üzerinde bir [okuyucu](built-in-roles.md#reader) rolüne sahipse, depolama hesabını görüntüleyebilirler, ancak varsayılan olarak temel alınan verileri görüntüleyemez.

Daha önce, rol tabanlı erişim denetimi veri işlemleri için kullanılmadı. Kaynak sağlayıcıları arasında değişen veri işlemlerine yönelik yetkilendirme. Yönetim işlemleri için kullanılan rol tabanlı erişim denetimi yetkilendirme modeli, veri işlemlerine genişletildi.

Veri işlemlerini desteklemek için, rol tanımı yapısına yeni veri özellikleri eklenmiştir. Veri işlemleri `DataActions` ve `NotDataActions` özelliklerinde belirtilir. Bu veri özellikleri eklenerek, yönetim ve veriler arasındaki ayrım korunur. Bu, joker karakter (`*`) içeren geçerli rol atamalarının, aniden verilere erişmesine engel olur. `DataActions` Ve`NotDataActions`içinde belirtilenebilir bazı veri işlemleri şunlardır:

- Bir kapsayıcıdaki Blobların listesini okuma
- Kapsayıcıda bir Depolama Blobu yazma
- Kuyruktaki bir iletiyi silme

Hem`Actions`hemde [özelliklerinde işlemler içeren Depolama Blobu veri okuyucusu rol tanımı](built-in-roles.md#storage-blob-data-reader) aşağıdaverilmiştir.`DataActions` Bu rol, blob kapsayıcısını ve ayrıca temel alınan blob verilerini okumanızı sağlar.

```json
{
  "Name": "Storage Blob Data Reader",
  "Id": "2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
  "IsCustom": false,
  "Description": "Allows for read access to Azure Storage blob containers and data",
  "Actions": [
    "Microsoft.Storage/storageAccounts/blobServices/containers/read"
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

`DataActions` Ve`NotDataActions` özelliklerine yalnızca veri işlemleri eklenebilir. Kaynak sağlayıcıları, `isDataAction` özelliğini olarak `true`ayarlayarak hangi işlemlerin veri işlemleri olduğunu belirler. Olduğu işlemlerin `isDataAction` `true`bir listesini görmek için bkz. [kaynak sağlayıcısı işlemleri](resource-provider-operations.md). Veri işlemlerine sahip olmayan roller, rol tanımında ve `DataActions` `NotDataActions` özelliklerine sahip olmak için gerekli değildir.

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
&nbsp;&nbsp;&nbsp;&nbsp;Veri eylemleri<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write`

Ayla bir abonelik kapsamında bir joker`*`karakter () eylemi olduğundan, izinleri tüm yönetim eylemlerini gerçekleştirmeye olanak tanımak için devralınır. Gamze kapsayıcıları okuyabilir, yazabilir ve silebilir. Ancak, Çiğdem ek adımlar uygulamadan veri işlemleri gerçekleştiremez. Örneğin, varsayılan olarak, Gamze bir kapsayıcı içindeki Blobları okuyamaz. Blobları okumak için, Çiğdem 'in depolama erişim anahtarlarını alması ve bloblara erişmek için onları kullanması gerekir.

Bob 'un izinleri, `Actions` [Depolama Blobu veri katılımcısı](built-in-roles.md#storage-blob-data-contributor) rolünde `DataActions` yalnızca ve belirtilen ile kısıtlıdır. Bob, rol temelinde hem yönetim hem de veri işlemlerini gerçekleştirebilir. Örneğin Bob, belirtilen depolama hesabındaki kapsayıcıları okuyabilir, yazabilir ve silebilir ve ayrıca Blobları okuyabilir, yazabilir ve silebilir.

Depolama için yönetim ve veri düzlemi güvenliği hakkında daha fazla bilgi için bkz. [Azure depolama Güvenlik Kılavuzu](../storage/common/storage-security-guide.md).

### <a name="what-tools-support-using-rbac-for-data-operations"></a>Veri işlemleri için RBAC kullanılarak hangi araçlar desteklenir?

Veri işlemlerini görüntülemek ve bunlarla çalışmak için, araçların veya SDK 'ların doğru sürümlerine sahip olmanız gerekir:

| Tool  | Version  |
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

`Actions` İzin, rolün gerçekleştirilmesine izin verdiği yönetim işlemlerini belirler. Azure Kaynak sağlayıcılarının güvenli kılınabilir işlemlerini tanımlayan bir işlem dizeleri koleksiyonudur. Burada, ' de `Actions`kullanılabilen yönetim işlemlerine ilişkin bazı örnekler verilmiştir.

| İşlem dizesi    | Açıklama         |
| ------------------- | ------------------- |
| `*/read` | Tüm Azure Kaynak sağlayıcılarının tüm kaynak türleri için okuma işlemlerine erişim izni verir.|
| `Microsoft.Compute/*` | Microsoft. COMPUTE kaynak sağlayıcısındaki tüm kaynak türleri için tüm işlemlere erişim verir.|
| `Microsoft.Network/*/read` | Microsoft. Network kaynak sağlayıcısındaki tüm kaynak türleri için okuma işlemlerine erişim izni verir.|
| `Microsoft.Compute/virtualMachines/*` | Sanal makinelerin tüm işlemlerine ve alt kaynak türlerine erişim verir.|
| `microsoft.web/sites/restart/Action` | Bir Web uygulamasını yeniden başlatma erişimine izin verir.|

## <a name="notactions"></a>NotActions

İzin izin verilen `Actions`' dan dışlanan yönetim işlemlerini belirler. `NotActions` İzin vermek istediğiniz işlem kümesi kısıtlanmış işlemleri dışlayarak daha kolay tanımlanmazsa, iznikullanın.`NotActions` Bir rol tarafından verilen erişim (geçerli izinler) işlemleri `NotActions` `Actions` işlemlerden çıkararak hesaplanır.

> [!NOTE]
> Bir kullanıcıya ' de `NotActions`bir işlemi dışlayan bir rol atanırsa ve aynı işleme erişim izni veren ikinci bir rol atanırsa, kullanıcının bu işlemi gerçekleştirmesine izin verilir. `NotActions`bir reddetme kuralı değil, belirli işlemler dışlanmanız gerektiğinde, izin verilen bir işlem kümesi oluşturmanın kolay bir yoludur.
>

## <a name="dataactions"></a>DataActions

`DataActions` İzin, rolün bu nesne içindeki verilerinize yapılmasına izin verdiği veri işlemlerini belirler. Örneğin, bir kullanıcının depolama hesabına yönelik blob veri okuma erişimi varsa, bu depolama hesabındaki Blobları okuyabilirler. İşte kullanılabilecek `DataActions`veri işlemlerine ilişkin bazı örnekler aşağıda verilmiştir.

| İşlem dizesi    | Açıklama         |
| ------------------- | ------------------- |
| `Microsoft.Storage/storageAccounts/ blobServices/containers/blobs/read` | Blob veya Blobların listesini döndürür. |
| `Microsoft.Storage/storageAccounts/ blobServices/containers/blobs/write` | Blob yazma sonucunu döndürür. |
| `Microsoft.Storage/storageAccounts/ queueServices/queues/messages/read` | Bir ileti döndürür. |
| `Microsoft.Storage/storageAccounts/ queueServices/queues/messages/*` | Bir ileti veya bir ileti yazmanın ya da silmenin sonucunu döndürür. |

## <a name="notdataactions"></a>NotDataActions

İzin izin verilen `DataActions`' dan dışlanan veri işlemlerini belirler. `NotDataActions` Bir rol tarafından verilen erişim (geçerli izinler) işlemleri `NotDataActions` `DataActions` işlemlerden çıkararak hesaplanır. Her kaynak sağlayıcı, veri işlemlerini karşılamak için ilgili API kümesini sağlar.

> [!NOTE]
> Bir kullanıcıya ' de `NotDataActions`bir veri işlemini dışlayan bir rol atanırsa ve aynı veri işlemine erişim veren ikinci bir rol atanırsa, kullanıcının bu veri işlemini gerçekleştirmesine izin verilir. `NotDataActions`, bir reddetme kuralı değil, belirli veri işlemlerinin dışlanması gerektiğinde bir izin verilen veri işlemleri kümesi oluşturmanın kolay bir yoludur.
>

## <a name="assignablescopes"></a>Astifblescopes

`AssignableScopes` Özelliği, bu rol tanımının kullanılabildiği kapsamları (Yönetim grupları, abonelikler, kaynak grupları veya kaynaklar) belirtir. Rolü yalnızca gereken yönetim gruplarında, aboneliklerde veya kaynak gruplarında atama için kullanılabilir hale getirebilirsiniz. En az bir yönetim grubu, abonelik, kaynak grubu veya kaynak KIMLIĞI kullanmanız gerekir.

Yerleşik roller `AssignableScopes` kök kapsamına (`"/"`) ayarlı. Kök kapsamı, rolün tüm kapsamlardaki atamaya uygun olduğunu gösterir. Geçerli atanabilir kapsamların örnekleri şunlardır:

| Rol atama için kullanılabilir | Örnek |
|----------|---------|
| Bir abonelik | `"/subscriptions/{subscriptionId1}"` |
| İki abonelik | `"/subscriptions/{subscriptionId1}", "/subscriptions/{subscriptionId2}"` |
| Ağ kaynak grubu | `"/subscriptions/{subscriptionId1}/resourceGroups/Network"` |
| Bir yönetim grubu | `"/providers/Microsoft.Management/managementGroups/{groupId1}"` |
| Yönetim grubu ve abonelik | `"/providers/Microsoft.Management/managementGroups/{groupId1}", /subscriptions/{subscriptionId1}",` |
| Tüm kapsamlar (yalnızca yerleşik roller için geçerlidir) | `"/"` |

Özel roller hakkında `AssignableScopes` daha fazla bilgi için bkz. [Azure kaynakları için özel roller](custom-roles.md).

## <a name="next-steps"></a>Sonraki adımlar

* [Azure kaynakları için yerleşik roller](built-in-roles.md)
* [Azure kaynakları için özel roller](custom-roles.md)
* [Azure Resource Manager kaynak sağlayıcısı işlemleri](resource-provider-operations.md)
