---
title: 'Hızlı başlangıç: Azure CLı ile bir yönetim grubu oluşturma'
description: Bu hızlı başlangıçta, kaynaklarınızı bir kaynak hiyerarşisinde düzenlemek üzere bir yönetim grubu oluşturmak için Azure CLı 'yi kullanırsınız.
ms.date: 02/05/2021
ms.topic: quickstart
ms.custom: devx-track-azurecli
ms.openlocfilehash: fe38882bd3b025635662e228ae919a24b03dee78
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99592475"
---
# <a name="quickstart-create-a-management-group-with-the-azure-cli"></a>Hızlı başlangıç: Azure CLı ile bir yönetim grubu oluşturma

Yönetim grupları, birden çok abonelik üzerinde erişimi, ilkeyi ve uyumluluğu yönetmenize yardımcı olan kapsayıcılardır. [Azure ilkesi](../policy/overview.md) ve [Azure rol tabanlı erişim denetimleriyle](../../role-based-access-control/overview.md)kullanılabilecek etkili ve verimli bir hiyerarşi oluşturmak için bu kapsayıcıları oluşturun. Yönetim grupları hakkında daha fazla bilgi için bkz. [Azure Yönetim gruplarıyla kaynaklarınızı düzenleme](overview.md).

Dizinde oluşturulan ilk yönetim grubunun tamamlanması 15 dakika sürebilir. Dizininiz için Azure 'da yönetim grupları hizmetini ayarlamak için ilk kez çalışan süreçler vardır. İşlem tamamlandığında bir bildirim alırsınız. Daha fazla bilgi için bkz. [yönetim gruplarının ilk kurulumu](./overview.md#initial-setup-of-management-groups).

## <a name="prerequisites"></a>Önkoşullar

- Azure aboneliğiniz yoksa başlamadan önce [ücretsiz](https://azure.microsoft.com/free/) bir hesap oluşturun.

- Bu hızlı başlangıç, CLı 'yi yerel olarak yüklemek ve kullanmak için Azure CLı sürüm 2.0.76 veya üstünü çalıştırmanızı gerektirir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yüklemeniz veya yükseltmeniz gerekirse, bkz. [Azure CLI yükleme](/cli/azure/install-azure-cli).

- Kiracıdaki tüm Azure AD kullanıcıları, [hiyerarşi koruması](./how-to/protect-resource-hierarchy.md#setting---require-authorization) etkinleştirilmemişse, bu kullanıcıya atanan yönetim grubu yazma izni olmadan bir yönetim grubu oluşturabilir. Bu yeni yönetim grubu kök yönetim grubunun veya [Varsayılan yönetim grubunun](./how-to/protect-resource-hierarchy.md#setting---default-management-group) bir alt öğesi haline gelir ve oluşturucuya "Owner" rol ataması verilir. Yönetim grubu hizmeti, bu becerisine, rol atamalarının kök düzeyinde gerekmemesi için izin verir. Kök yönetim grubuna, bir kullanıcı oluşturulduğunda erişemez. Yönetim grupları 'nı kullanmaya başlamak üzere Azure AD Genel yöneticilerini bulmanın önüne geçmek için, kök düzeyinde ilk yönetim gruplarının oluşturulmasına izin veririz.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

### <a name="create-in-the-azure-cli"></a>Azure CLı 'de oluşturma

Azure CLı için, yeni bir yönetim grubu oluşturmak için [az Account Management-Group Create](/cli/azure/account/management-group#az_account_management_group_create) komutunu kullanın. Bu örnekte, yönetim grubu **adı** _contoso_' dır.

```azurecli-interactive
az account management-group create --name 'Contoso'
```

**Ad** , oluşturulmakta olan benzersiz bir tanımlayıcıdır. Bu KIMLIK, bu gruba başvurmak için diğer komutlar tarafından kullanılır ve daha sonra değiştirilemez.

Yönetim grubunun Azure portal içinde farklı bir ad göstermesini istiyorsanız, **görünen ad** parametresini ekleyin. Örneğin, contoso GroupName ve "contoso Group" görünen adı ile bir yönetim grubu oluşturmak için aşağıdaki komutu kullanın:

```azurecli-interactive
az account management-group create --name 'Contoso' --display-name 'Contoso Group'
```

Yukarıdaki örneklerde, yeni yönetim grubu kök yönetim grubu altında oluşturulur. Üst öğe olarak farklı bir yönetim grubu belirtmek için **üst** parametreyi kullanın ve üst grubun adını sağlayın.

```azurecli-interactive
az account management-group create --name 'ContosoSubGroup' --parent 'Contoso'
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Yukarıda oluşturulan yönetim grubunu kaldırmak için [az Account Management-Group Delete](/cli/azure/account/management-group#az_account_management_group_delete) komutunu kullanın:

```azurecli-interactive
az account management-group delete --name 'Contoso'
```

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, kaynak hiyerarşinizi düzenlemek için bir yönetim grubu oluşturdunuz. Yönetim grubu, abonelikleri veya diğer yönetim gruplarını içerebilir.

Yönetim grupları ve kaynak hiyerarşinizi yönetme hakkında daha fazla bilgi edinmek için şu çalışmaya devam edin:

> [!div class="nextstepaction"]
> [Kaynakları yönetim gruplarıyla yönetme](./manage.md)