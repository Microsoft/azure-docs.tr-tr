---
title: 'Hızlı başlangıç: Azure PowerShell bir yönetim grubu oluşturma'
description: Bu hızlı başlangıçta, kaynaklarınızı bir kaynak hiyerarşisinde düzenlemek üzere bir yönetim grubu oluşturmak için Azure PowerShell kullanırsınız.
ms.date: 02/05/2021
ms.topic: quickstart
ms.custom:
- devx-track-azurepowershell
- mode-api
ms.openlocfilehash: 0291bb2bfb439ad09531066f6bad4e20a3f4c6bd
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107535974"
---
# <a name="quickstart-create-a-management-group-with-azure-powershell"></a>Hızlı başlangıç: Azure PowerShell bir yönetim grubu oluşturma

Yönetim grupları, birden çok abonelik üzerinde erişimi, ilkeyi ve uyumluluğu yönetmenize yardımcı olan kapsayıcılardır. [Azure ilkesi](../policy/overview.md) ve [Azure rol tabanlı erişim denetimleriyle](../../role-based-access-control/overview.md)kullanılabilecek etkili ve verimli bir hiyerarşi oluşturmak için bu kapsayıcıları oluşturun. Yönetim grupları hakkında daha fazla bilgi için bkz. [Azure Yönetim gruplarıyla kaynaklarınızı düzenleme](overview.md).

Dizinde oluşturulan ilk yönetim grubunun tamamlanması 15 dakika sürebilir. Dizininiz için Azure 'da yönetim grupları hizmetini ayarlamak için ilk kez çalışan süreçler vardır. İşlem tamamlandığında bir bildirim alırsınız. Daha fazla bilgi için bkz. [yönetim gruplarının ilk kurulumu](./overview.md#initial-setup-of-management-groups).

## <a name="prerequisites"></a>Önkoşullar

- Azure aboneliğiniz yoksa başlamadan önce [ücretsiz](https://azure.microsoft.com/free/) bir hesap oluşturun.

- Başlamadan önce, Azure PowerShell en son sürümünün yüklü olduğundan emin olun. Ayrıntılı bilgi için bkz. [Azure PowerShell modülünü Install](/powershell/azure/install-az-ps) .

- Kiracıdaki tüm Azure AD kullanıcıları, [hiyerarşi koruması](./how-to/protect-resource-hierarchy.md#setting---require-authorization) etkinleştirilmemişse, bu kullanıcıya atanan yönetim grubu yazma izni olmadan bir yönetim grubu oluşturabilir. Bu yeni yönetim grubu kök yönetim grubunun veya [Varsayılan yönetim grubunun](./how-to/protect-resource-hierarchy.md#setting---default-management-group) bir alt öğesi haline gelir ve oluşturucuya "Owner" rol ataması verilir. Yönetim grubu hizmeti, bu becerisine, rol atamalarının kök düzeyinde gerekmemesi için izin verir. Kök yönetim grubuna, bir kullanıcı oluşturulduğunda erişemez. Yönetim grupları 'nı kullanmaya başlamak üzere Azure AD Genel yöneticilerini bulmanın önüne geçmek için, kök düzeyinde ilk yönetim gruplarının oluşturulmasına izin veririz.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

### <a name="create-in-azure-powershell"></a>Azure PowerShell oluşturma

PowerShell için, New [-AzManagementGroup](/powershell/module/az.resources/new-azmanagementgroup) cmdlet 'ini kullanarak yeni bir yönetim grubu oluşturun. Bu örnekte, **GroupName** yönetim grubu _contoso_' dır.

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

## <a name="clean-up-resources"></a>Kaynakları temizleme

Yukarıda oluşturulan yönetim grubunu kaldırmak için [Remove-AzManagementGroup](/powershell/module/az.resources/remove-azmanagementgroup) cmdlet 'ini kullanın:

```azurepowershell-interactive
Remove-AzManagementGroup -GroupName 'Contoso'
```

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, kaynak hiyerarşinizi düzenlemek için bir yönetim grubu oluşturdunuz. Yönetim grubu, abonelikleri veya diğer yönetim gruplarını içerebilir.

Yönetim grupları ve kaynak hiyerarşinizi yönetme hakkında daha fazla bilgi edinmek için şu çalışmaya devam edin:

> [!div class="nextstepaction"]
> [Kaynakları yönetim gruplarıyla yönetme](./manage.md)
