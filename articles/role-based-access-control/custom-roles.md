---
title: Azure özel rolleri-Azure RBAC
description: Azure kaynakları için ayrıntılı erişim yönetimi için Azure rol tabanlı erişim denetimi (Azure RBAC) ile Azure özel rolleri oluşturmayı öğrenin.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: conceptual
ms.workload: identity
ms.date: 12/15/2020
ms.author: rolyon
ms.openlocfilehash: 9779c2a269902d856d1639ce78028d0e658656bb
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107479840"
---
# <a name="azure-custom-roles"></a>Özel Azure rolleri

> [!IMPORTANT]
> ' Ye bir yönetim grubu eklemek `AssignableScopes` Şu anda önizlemededir.
> Önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir.
> Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[Azure yerleşik rolleri](built-in-roles.md) , kuruluşunuzun belirli ihtiyaçlarını karşılamıyorsa, kendi özel rollerinizi de oluşturabilirsiniz. Yerleşik rollerde olduğu gibi, yönetim grubundaki kullanıcılar, gruplar ve hizmet sorumlularına (yalnızca Önizleme), abonelik ve kaynak grubu kapsamları gibi özel roller de atayabilirsiniz.

Özel Roller aynı Azure AD dizinine güvenen abonelikler arasında paylaşılabilir. Dizin başına **5.000** özel rol sınırı vardır. (Azure Almanya ve Azure Çin 21Vianet için sınır 2.000 özel rollerdir.) Özel roller Azure portal, Azure PowerShell, Azure CLı veya REST API kullanılarak oluşturulabilir.

## <a name="steps-to-create-a-custom-role"></a>Özel rol oluşturma adımları

Özel bir rol oluşturmak için temel adımlar aşağıda verilmiştir.

1. İhtiyaç duyduğunuz izinleri saptayın.

    Özel bir rol oluşturduğunuzda, izinlerinizi tanımlamak için kullanılabilir işlemleri bilmeniz gerekir. Genellikle, mevcut yerleşik bir rolle başlayıp gereksinimlerinize göre değiştirirsiniz. İşlemleri `Actions` `NotActions` [Rol tanımının](role-definitions.md)veya özelliklerine ekleyeceksiniz. Veri işlemlerdir, bunları `DataActions` veya `NotDataActions` özelliklerine eklersiniz.

    Daha fazla bilgi için, [ihtiyacınız olan Izinleri nasıl belirleyebir](#how-to-determine-the-permissions-you-need)sonraki bölüme bakın.

1. Özel rolü nasıl oluşturmak istediğinize karar verin.

    [Azure Portal](custom-roles-portal.md), [Azure POWERSHELL](custom-roles-powershell.md), [Azure CLI](custom-roles-cli.md)veya [REST API](custom-roles-rest.md)kullanarak özel roller oluşturabilirsiniz.

1. Özel rolü oluşturun.

    En kolay yöntem Azure portal kullanmaktır. Azure portal kullanarak özel rol oluşturma adımları için, bkz. [Azure Portal kullanarak Azure özel rolleri oluşturma veya güncelleştirme](custom-roles-portal.md).

1. Özel rolü test edin.

    Özel rolünüzü aldıktan sonra, beklendiği gibi çalıştığını doğrulamak için test etmeniz gerekir. Daha sonra ayarlamalar yapmanız gerekiyorsa, özel rolü güncelleştirebilirsiniz.

## <a name="how-to-determine-the-permissions-you-need"></a>İhtiyaç duyduğunuz izinleri belirleme

Azure 'da, özel rolünüzün potansiyel olarak içerebileceği binlerce izin vardır. Özel rolünüze eklemek istediğiniz izinleri belirlemenize yardımcı olabilecek bazı yöntemler şunlardır:

- Mevcut [yerleşik rollere](built-in-roles.md)bakın.

    Mevcut bir rolü değiştirmek veya birden çok rolde kullanılan izinleri birleştirmek isteyebilirsiniz.

- Erişim vermek istediğiniz Azure hizmetlerini listeleyin.

- [Azure hizmetleriyle eşlenen kaynak sağlayıcılarını](../azure-resource-manager/management/azure-services-resource-providers.md)belirleme.

    Azure Hizmetleri, [kaynak sağlayıcıları](../azure-resource-manager/management/overview.md)aracılığıyla işlevlerini ve izinlerini kullanıma sunar. Örneğin, Microsoft. COMPUTE kaynak sağlayıcısı, sanal makine kaynaklarını ve Microsoft. faturalandırma kaynak sağlayıcısını, abonelik ve faturalandırma kaynakları sağlar. Kaynak sağlayıcılarının bilinmesi, özel rolünüzün gereksinim duyduğunuz izinleri daraltmanıza ve belirlemenize yardımcı olabilir.

    Azure portal kullanarak özel bir rol oluşturduğunuzda, anahtar sözcükleri arayarak kaynak sağlayıcılarını da belirleyebilirsiniz. Bu arama işlevi, [Azure Portal kullanarak Azure özel rolleri oluşturma veya güncelleştirme](custom-roles-portal.md#step-4-permissions)konularında açıklanmaktadır.

    ![Kaynak sağlayıcısı ile izin bölmesi ekleme](./media/custom-roles-portal/add-permissions-provider.png)

- Dahil etmek istediğiniz izinleri bulmak için [kullanılabilir izinleri](resource-provider-operations.md) arayın.

    Azure portal kullanarak özel bir rol oluşturduğunuzda, anahtar sözcüğe göre izinler araması yapabilirsiniz. Örneğin, *sanal makine* veya *faturalandırma* izinleri için arama yapabilirsiniz. Ayrıca tüm izinleri bir CSV dosyası olarak indirebilir ve ardından bu dosyada arama yapabilirsiniz. Bu arama işlevi, [Azure Portal kullanarak Azure özel rolleri oluşturma veya güncelleştirme](custom-roles-portal.md#step-4-permissions)konularında açıklanmaktadır.

    ![İzin listesi ekle](./media/custom-roles-portal/add-permissions-list.png)

## <a name="custom-role-example"></a>Özel rol örneği

Aşağıda, bir özel rolün JSON biçiminde Azure PowerShell kullanılarak gösterildiği gibi göründüğünü gösterilmektedir. Bu özel rol, sanal makineleri izlemek ve yeniden başlatmak için kullanılabilir.

```json
{
  "Name": "Virtual Machine Operator",
  "Id": "88888888-8888-8888-8888-888888888888",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.ResourceHealth/availabilityStatuses/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Insights/diagnosticSettings/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
    "/subscriptions/{subscriptionId1}",
    "/subscriptions/{subscriptionId2}",
    "/providers/Microsoft.Management/managementGroups/{groupId1}"
  ]
}
```

Aşağıda, Azure CLı kullanılarak gösterilen özel rol gösterilmektedir.

```json
[
  {
    "assignableScopes": [
      "/subscriptions/{subscriptionId1}",
      "/subscriptions/{subscriptionId2}",
      "/providers/Microsoft.Management/managementGroups/{groupId1}"
    ],
    "description": "Can monitor and restart virtual machines.",
    "id": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleDefinitions/88888888-8888-8888-8888-888888888888",
    "name": "88888888-8888-8888-8888-888888888888",
    "permissions": [
      {
        "actions": [
          "Microsoft.Storage/*/read",
          "Microsoft.Network/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action",
          "Microsoft.Authorization/*/read",
          "Microsoft.ResourceHealth/availabilityStatuses/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Insights/diagnosticSettings/*",
          "Microsoft.Support/*"
        ],
        "dataActions": [],
        "notActions": [],
        "notDataActions": []
      }
    ],
    "roleName": "Virtual Machine Operator",
    "roleType": "CustomRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

Özel bir rol oluşturduğunuzda, bir turuncu kaynak simgesiyle Azure portal görüntülenir.

![Özel rol simgesi](./media/custom-roles/roles-custom-role-icon.png)

## <a name="custom-role-properties"></a>Özel rol özellikleri

Aşağıdaki tabloda özel rol özelliklerinin ne anlama geldiğini açıklanmaktadır.

| Özellik | Gerekli | Tür | Açıklama |
| --- | --- | --- | --- |
| `Name`</br>`roleName` | Evet | Dize | Özel rolün görünen adı. Rol tanımı bir yönetim grubu veya abonelik düzeyi kaynağı olsa da, aynı Azure AD dizinini paylaşan birden çok abonelikte rol tanımı kullanılabilir. Bu görünen ad, Azure AD dizininin kapsamında benzersiz olmalıdır. Harfler, rakamlar, boşluklar ve özel karakterler içerebilir. En fazla karakter sayısı 128 ' dir. |
| `Id`</br>`name` | Evet | Dize | Özel rolün benzersiz KIMLIĞI. Azure PowerShell ve Azure CLı için bu KIMLIK, yeni bir rol oluşturduğunuzda otomatik olarak oluşturulur. |
| `IsCustom`</br>`roleType` | Evet | Dize | Bunun özel bir rol olup olmadığını gösterir. `true`Özel roller için veya olarak ayarlayın `CustomRole` . `false`Yerleşik roller için veya olarak ayarlayın `BuiltInRole` . |
| `Description`</br>`description` | Evet | Dize | Özel rolün açıklaması. Harfler, rakamlar, boşluklar ve özel karakterler içerebilir. En fazla karakter sayısı 1024 ' dir. |
| `Actions`</br>`actions` | Yes | String [] | Rolün gerçekleştirilmesine izin verdiği yönetim işlemlerini belirten dizeler dizisi. Daha fazla bilgi için bkz. [Eylemler](role-definitions.md#actions). |
| `NotActions`</br>`notActions` | Hayır | String [] | İzin verilen ' dan dışlanan yönetim işlemlerini belirten dizeler dizisi `Actions` . Daha fazla bilgi için bkz. [NotActions](role-definitions.md#notactions). |
| `DataActions`</br>`dataActions` | Hayır | String [] | Rolün bu nesne içindeki verilerinize gerçekleştirilmesine izin verdiği veri işlemlerini belirten dizeler dizisi. İle özel bir rol oluşturursanız `DataActions` , bu rol yönetim grubu kapsamında atanamaz. Daha fazla bilgi için bkz. [Dataactions](role-definitions.md#dataactions). |
| `NotDataActions`</br>`notDataActions` | Hayır | String [] | İzin verilen ' dan dışlanan veri işlemlerini belirten dizeler dizisi `DataActions` . Daha fazla bilgi için bkz. [Notdataactions](role-definitions.md#notdataactions). |
| `AssignableScopes`</br>`assignableScopes` | Yes | String [] | Özel rolün atama için kullanılabilir olduğu kapsamları belirten dizeler dizisi. Özel bir rol içinde yalnızca bir yönetim grubu tanımlayabilirsiniz `AssignableScopes` . ' Ye bir yönetim grubu eklemek `AssignableScopes` Şu anda önizlemededir. Daha fazla bilgi için bkz. [Astifblescopes](role-definitions.md#assignablescopes). |

## <a name="wildcard-permissions"></a>Joker karakter izinleri

`Actions`, `NotActions` , `DataActions` ve `NotDataActions` `*` izinleri tanımlamak için joker karakterleri () destekler. Bir joker karakter ( `*` ) sağladığınız eylem dizesiyle eşleşen her şeye bir izni genişletir. Örneğin, Azure maliyet yönetimi ve dışarı aktarma ile ilgili tüm izinleri eklemek istediğinizi varsayalım. Bu eylem dizelerinin tümünü ekleyebilirsiniz:

```
Microsoft.CostManagement/exports/action
Microsoft.CostManagement/exports/read
Microsoft.CostManagement/exports/write
Microsoft.CostManagement/exports/delete
Microsoft.CostManagement/exports/run/action
```

Bu dizelerin tümünü eklemek yerine yalnızca bir joker karakter dizesi ekleyebilirsiniz. Örneğin, aşağıdaki joker karakter dizesi önceki beş dizeye eşdeğerdir. Bu Ayrıca, eklenebilir olabilecek, gelecekteki tüm dışarı aktarma izinlerini de içerir.

```
Microsoft.CostManagement/exports/*
```

Bir dizede birden fazla joker karakter de olabilir. Örneğin, aşağıdaki dize maliyet yönetimi için tüm sorgu izinlerini temsil eder.

```
Microsoft.CostManagement/*/query/*
```

## <a name="who-can-create-delete-update-or-view-a-custom-role"></a>Özel bir rol oluşturabilir, silebilir, güncelleştirebilir veya görüntüleyebilir

Yerleşik rollerde olduğu gibi, `AssignableScopes` özelliği rolün atama için kullanılabilir olduğu kapsamları belirtir. `AssignableScopes`Özel bir rol için özelliği ayrıca, özel rolü kimlerin oluşturerişebileceğini, silediğini, güncelleştirediğini veya görüntüleyemeyeceğini de denetler.

| Görev | İşlem | Açıklama |
| --- | --- | --- |
| Özel bir rol oluşturma/silme | `Microsoft.Authorization/ roleDefinitions/write` | Tüm özel rol üzerinde bu işleme verilen kullanıcılar, bu `AssignableScopes` kapsamlarda kullanılmak üzere özel roller oluşturabilir (veya silebilir). Örneğin, [sahipler](built-in-roles.md#owner) ve [Kullanıcı erişimi](built-in-roles.md#user-access-administrator) yönetim gruplarının, aboneliklerinin ve kaynak gruplarının yöneticileri. |
| Özel rolü güncelleştirme | `Microsoft.Authorization/ roleDefinitions/write` | Tüm özel rol üzerinde bu işleme verilen kullanıcılar, `AssignableScopes` Bu kapsamlardaki özel rolleri güncelleştirebilir. Örneğin, [sahipler](built-in-roles.md#owner) ve [Kullanıcı erişimi](built-in-roles.md#user-access-administrator) yönetim gruplarının, aboneliklerinin ve kaynak gruplarının yöneticileri. |
| Özel bir rol görüntüleme | `Microsoft.Authorization/ roleDefinitions/read` | Bu işlem bir kapsamda verilen kullanıcılar, bu kapsamda atama için kullanılabilen özel rolleri görüntüleyebilir. Tüm yerleşik roller, özel rollerin atama için kullanılabilir olmasına olanak tanır. |

## <a name="custom-role-limits"></a>Özel rol limitleri

Aşağıdaki listede özel roller için sınırlar açıklanmaktadır.

- Her bir dizin en fazla **5000** özel role sahip olabilir.
- Azure Almanya ve Azure Çin 21Vianet, her bir dizin için en fazla 2000 özel rol içerebilir.
- `AssignableScopes`Kök kapsamına ( `"/"` ) ayarlayamazsınız.
- İçinde joker karakterler ( `*` ) kullanamazsınız `AssignableScopes` . Bu joker karakter kısıtlaması, bir kullanıcının rol tanımını güncelleştirerek bir kapsama erişim elde edemiyor olmasını sağlamaya yardımcı olur.
- Özel bir rol içinde yalnızca bir yönetim grubu tanımlayabilirsiniz `AssignableScopes` . ' Ye bir yönetim grubu eklemek `AssignableScopes` Şu anda önizlemededir.
- `DataActions`Yönetim grubu kapsamında özel roller atanamaz.
- Azure Resource Manager, rol tanımının atanabilir kapsamındaki yönetim grubunun varlığını doğrulamaz.

Özel roller ve yönetim grupları hakkında daha fazla bilgi için bkz. [Azure Yönetim gruplarıyla kaynaklarınızı düzenleme](../governance/management-groups/overview.md#azure-custom-role-definition-and-assignment).

## <a name="input-and-output-formats"></a>Giriş ve çıkış biçimleri

Komut satırını kullanarak özel bir rol oluşturmak için genellikle JSON kullanarak özel rol için istediğiniz özellikleri belirtin. Kullandığınız araçlara bağlı olarak, giriş ve çıkış biçimleri biraz farklı görünecektir. Bu bölümde, araca bağlı olarak giriş ve çıkış biçimleri listelenmektedir.

### <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell kullanarak özel bir rol oluşturmak için aşağıdaki girişi sağlamanız gerekir.

```json
{
  "Name": "",
  "Description": "",
  "Actions": [],
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": []
}
```

Azure PowerShell kullanarak özel bir rolü güncelleştirmek için aşağıdaki girişi sağlamanız gerekir. `Id`Özelliğin eklendiğini unutmayın. 

```json
{
  "Name": "",
  "Id": "",
  "Description": "",
  "Actions": [],
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": []
}
```

Aşağıda, Azure PowerShell ve [ConvertTo-JSON](/powershell/module/microsoft.powershell.utility/convertto-json) komutunu kullanarak özel bir rol listelüünüzde çıktının bir örneği gösterilmektedir. 

```json
{
  "Name": "",
  "Id": "",
  "IsCustom": true,
  "Description": "",
  "Actions": [],
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": []
}
```

### <a name="azure-cli"></a>Azure CLI

Azure CLı kullanarak özel bir rol oluşturmak veya güncelleştirmek için aşağıdaki girişi sağlamanız gerekir. Azure PowerShell kullanarak özel bir rol oluştururken bu biçim aynı biçimdedir.

```json
{
  "Name": "",
  "Description": "",
  "Actions": [],
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": []
}
```

Aşağıda, Azure CLı kullanarak özel bir rol listelüünüzde çıktının bir örneği gösterilmektedir.

```json
[
  {
    "assignableScopes": [],
    "description": "",
    "id": "",
    "name": "",
    "permissions": [
      {
        "actions": [],
        "dataActions": [],
        "notActions": [],
        "notDataActions": []
      }
    ],
    "roleName": "",
    "roleType": "CustomRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

### <a name="rest-api"></a>REST API

REST API kullanarak özel bir rol oluşturmak veya güncelleştirmek için aşağıdaki girişi sağlamanız gerekir. Bu biçim, Azure portal kullanarak özel bir rol oluşturduğunuzda oluşturulan biçimdir.

```json
{
  "properties": {
    "roleName": "",
    "description": "",
    "assignableScopes": [],
    "permissions": [
      {
        "actions": [],
        "notActions": [],
        "dataActions": [],
        "notDataActions": []
      }
    ]
  }
}
```

Aşağıda, REST API kullanarak özel bir rol listelüünüzde çıktının bir örneği gösterilmektedir.

```json
{
    "properties": {
        "roleName": "",
        "type": "CustomRole",
        "description": "",
        "assignableScopes": [],
        "permissions": [
            {
                "actions": [],
                "notActions": [],
                "dataActions": [],
                "notDataActions": []
            }
        ],
        "createdOn": "",
        "updatedOn": "",
        "createdBy": "",
        "updatedBy": ""
    },
    "id": "",
    "type": "Microsoft.Authorization/roleDefinitions",
    "name": ""
}
```

## <a name="next-steps"></a>Sonraki adımlar

- [Öğretici: Azure PowerShell kullanarak bir Azure özel rolü oluşturma](tutorial-custom-role-powershell.md)
- [Öğretici: Azure CLı kullanarak Azure özel rolü oluşturma](tutorial-custom-role-cli.md)
- [Azure rol tanımlarını anlama](role-definitions.md)
- [Azure RBAC sorunlarını giderme](troubleshooting.md)
