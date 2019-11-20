---
title: Kaynakları düzenlemek için yönetim grupları oluşturma-Azure Idare
description: Portal, Azure PowerShell ve Azure CLı kullanarak birden çok kaynağı yönetmek için Azure Yönetim grupları oluşturmayı öğrenin.
ms.date: 04/05/2019
ms.topic: conceptual
ms.openlocfilehash: 335dd8f7f3a9ec20c2b7740e4ec97454489027f6
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73960210"
---
# <a name="create-management-groups-for-resource-organization-and-management"></a>Kaynak organizasyonu ve yönetimi için yönetim grupları oluşturma

Yönetim grupları, birden çok abonelik üzerinde erişimi, ilkeyi ve uyumluluğu yönetmenize yardımcı olan kapsayıcılardır. [Azure ilkesi](../policy/overview.md) ve [Azure rol tabanlı erişim denetimleriyle](../../role-based-access-control/overview.md)kullanılabilecek etkili ve verimli bir hiyerarşi oluşturmak için bu kapsayıcıları oluşturun. Yönetim grupları hakkında daha fazla bilgi için bkz. [Azure Yönetim gruplarıyla kaynaklarınızı düzenleme](overview.md).

Dizinde oluşturulan ilk yönetim grubunun tamamlanması 15 dakika sürebilir. Dizininiz için Azure 'da yönetim grupları hizmetini ayarlamak için ilk kez çalışan süreçler vardır. İşlem tamamlandığında bir bildirim alırsınız.

## <a name="create-a-management-group"></a>Yönetim grubu oluşturma

Portal, PowerShell veya Azure CLı kullanarak yönetim grubu oluşturabilirsiniz. Şu anda yönetim grupları oluşturmak için Kaynak Yöneticisi şablonları kullanamazsınız.

### <a name="create-in-portal"></a>Portalda oluştur

1. [Azure portalında](https://portal.azure.com) oturum açın.

1. **Tüm hizmetler** > **Yönetim + idare**' ı seçin.

1. **Maliyet yönetimi + faturalandırma** seçin

1. Maliyet yönetimi + faturalandırma-yönetim grupları sayfasında **Yönetim grupları** ' yi seçin.

1. **+ Yönetim grubu Ekle**' yi seçin.

   ![Yönetim gruplarıyla çalışma sayfası](./media/main.png)

1. Yönetim grubu KIMLIĞI alanını girin.

   - **Yönetim grubu kimliği** , bu yönetim grubundaki komutları göndermek için kullanılan dizin benzersiz tanımlayıcısıdır. Bu tanımlayıcı, bu grubu tanımlamak için Azure sistem genelinde kullanıldığından oluşturulduktan sonra düzenlenebilir değildir. [Kök yönetim grubu](overview.md#root-management-group-for-each-directory) , Azure Active Directory kimliği olan bir kimlikle otomatik olarak oluşturulur. Diğer tüm yönetim grupları için benzersiz bir KIMLIK atayın.
   - Görünen ad alanı Azure portal içinde görüntülenen addır. Ayrı bir görünen ad, yönetim grubu oluşturulurken isteğe bağlı bir alandır ve herhangi bir zamanda değiştirilebilir.  

   ![Yeni yönetim grubu oluşturmak için seçenekler bölmesi](./media/create_context_menu.png)  

1. **Kaydet**’i seçin.

### <a name="create-in-powershell"></a>PowerShell 'de oluştur

PowerShell için, New [-AzManagementGroup](/powershell/module/az.resources/new-azmanagementgroup) cmdlet 'ini kullanarak yeni bir yönetim grubu oluşturun.

```azurepowershell-interactive
New-AzManagementGroup -GroupName 'Contoso'
```

**GroupName** , oluşturulmakta olan benzersiz bir tanımlayıcıdır. Bu KIMLIK, bu gruba başvurmak için diğer komutlar tarafından kullanılır ve daha sonra değiştirilemez.

Yönetim grubunun Azure portal içinde farklı bir ad göstermesini istiyorsanız **DisplayName** parametresini ekleyin. Örneğin, contoso GroupName ve "contoso Group" görünen adı ile bir yönetim grubu oluşturmak için aşağıdaki cmdlet 'i kullanın:

```azurepowershell-interactive
New-AzManagementGroup -GroupName 'Contoso' -DisplayName 'Contoso Group'
```

Yukarıdaki örneklerde, yeni yönetim grubu kök yönetim grubu altında oluşturulur. Üst öğe olarak farklı bir yönetim grubu belirtmek için **parentID** parametresini kullanın.

```azurepowershell-interactive
$parentGroup = Get-AzManagementGroup -GroupName Contoso
New-AzManagementGroup -GroupName 'ContosoSubGroup' -ParentId $parentGroup.id
```

### <a name="create-in-azure-cli"></a>Azure CLı 'de oluşturma

Azure CLı için, yeni bir yönetim grubu oluşturmak için [az Account Management-Group Create](/cli/azure/account/management-group?view=azure-cli-latest#az-account-management-group-create) komutunu kullanın.

```azurecli-interactive
az account management-group create --name Contoso
```

**Ad** , oluşturulmakta olan benzersiz bir tanımlayıcıdır. Bu KIMLIK, bu gruba başvurmak için diğer komutlar tarafından kullanılır ve daha sonra değiştirilemez.

Yönetim grubunun Azure portal içinde farklı bir ad göstermesini istiyorsanız, **görünen ad** parametresini ekleyin. Örneğin, contoso GroupName ve "contoso Group" görünen adı ile bir yönetim grubu oluşturmak için aşağıdaki komutu kullanın:

```azurecli-interactive
az account management-group create --name Contoso --display-name 'Contoso Group'
```

Yukarıdaki örneklerde, yeni yönetim grubu kök yönetim grubu altında oluşturulur. Üst öğe olarak farklı bir yönetim grubu belirtmek için **üst** parametreyi kullanın ve üst grubun adını sağlayın.

```azurecli-interactive
az account management-group create --name ContosoSubGroup --parent Contoso
```

## <a name="next-steps"></a>Sonraki adımlar

Yönetim grupları hakkında daha fazla bilgi almak için bkz.:

- [Azure kaynaklarını düzenlemek için yönetim grupları oluşturma](create.md)
- [Yönetim gruplarınızı değiştirme, silme veya yönetme](manage.md)
- [Azure PowerShell Kaynak Modülünde yönetim gruplarını gözden geçirme](/powershell/module/az.resources#resources)
- [REST API'de yönetim gruplarını gözden geçirme](/rest/api/resources/managementgroups)
- [Azure CLI'de yönetim gruplarını gözden geçirme](/cli/azure/account/management-group)
