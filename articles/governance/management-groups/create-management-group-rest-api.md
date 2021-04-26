---
title: 'Hızlı başlangıç: REST API bir yönetim grubu oluşturma'
description: Bu hızlı başlangıçta, kaynaklarınızı bir kaynak hiyerarşisinde düzenlemek üzere bir yönetim grubu oluşturmak için REST API kullanırsınız.
ms.date: 02/05/2021
ms.topic: quickstart
ms.openlocfilehash: 26a1c0a8025a00155bcf0498e6dfa89a8a73d983
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107259141"
---
# <a name="quickstart-create-a-management-group-with-rest-api"></a>Hızlı başlangıç: REST API bir yönetim grubu oluşturma

Yönetim grupları, birden çok abonelik üzerinde erişimi, ilkeyi ve uyumluluğu yönetmenize yardımcı olan kapsayıcılardır. [Azure ilkesi](../policy/overview.md) ve [Azure rol tabanlı erişim denetimleriyle](../../role-based-access-control/overview.md)kullanılabilecek etkili ve verimli bir hiyerarşi oluşturmak için bu kapsayıcıları oluşturun. Yönetim grupları hakkında daha fazla bilgi için bkz. [Azure Yönetim gruplarıyla kaynaklarınızı düzenleme](overview.md).

Dizinde oluşturulan ilk yönetim grubunun tamamlanması 15 dakika sürebilir. Dizininiz için Azure 'da yönetim grupları hizmetini ayarlamak için ilk kez çalışan süreçler vardır. İşlem tamamlandığında bir bildirim alırsınız. Daha fazla bilgi için bkz. [yönetim gruplarının ilk kurulumu](./overview.md#initial-setup-of-management-groups).

## <a name="prerequisites"></a>Önkoşullar

- Azure aboneliğiniz yoksa başlamadan önce [ücretsiz](https://azure.microsoft.com/free/) bir hesap oluşturun.

- Henüz yapmadıysanız, [Armclient](https://github.com/projectkudu/ARMClient)' ı yüklemeyin. Azure Resource Manager tabanlı REST API 'Lerine HTTP istekleri gönderen bir araçtır. Bunun yerine, bekleyen belgelerde "dene" özelliğini veya PowerShell 'in [Invoke-RestMethod](/powershell/module/microsoft.powershell.utility/invoke-restmethod) veya [Postman](https://www.postman.com)gibi araçları kullanabilirsiniz.

- Kiracıdaki tüm Azure AD kullanıcıları, [hiyerarşi koruması](./how-to/protect-resource-hierarchy.md#setting---require-authorization) etkinleştirilmemişse, bu kullanıcıya atanan yönetim grubu yazma izni olmadan bir yönetim grubu oluşturabilir. Bu yeni yönetim grubu kök yönetim grubunun veya [Varsayılan yönetim grubunun](./how-to/protect-resource-hierarchy.md#setting---default-management-group) bir alt öğesi haline gelir ve oluşturucuya "Owner" rol ataması verilir. Yönetim grubu hizmeti, bu becerisine, rol atamalarının kök düzeyinde gerekmemesi için izin verir. Kök yönetim grubuna, bir kullanıcı oluşturulduğunda erişemez. Yönetim grupları 'nı kullanmaya başlamak üzere Azure AD Genel yöneticilerini bulmanın önüne geçmek için, kök düzeyinde ilk yönetim gruplarının oluşturulmasına izin veririz.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

### <a name="create-in-rest-api"></a>REST API oluşturma

REST API için [Yönetim grupları-oluştur veya Güncelleştir](/rest/api/managementgroups/managementgroups/createorupdate) uç noktasını kullanarak yeni bir yönetim grubu oluşturun. Bu örnekte, yönetim grubu **GroupID** _contoso_' dır.

- REST API URI'si

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/Contoso?api-version=2020-02-01
  ```

- Istek gövdesi yok

**GroupID** , oluşturulmakta olan benzersiz bir tanımlayıcıdır. Bu KIMLIK, bu gruba başvurmak için diğer komutlar tarafından kullanılır ve daha sonra değiştirilemez.

Yönetim grubunun Azure portal içinde farklı bir ad göstermesini istiyorsanız, istek gövdesine **Properties. DisplayName** özelliğini ekleyin. Örneğin, _contoso_ **GroupID** ve _contoso grubunun_ görünen adı ile bir yönetim grubu oluşturmak için aşağıdaki uç noktayı ve istek gövdesini kullanın:

- REST API URI'si

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/Contoso?api-version=2020-02-01
  ```

- İstek Gövdesi

  ```json
  {
    "properties": {
      "displayName": "Contoso Group"
    }
  }
  ```

Yukarıdaki örneklerde, yeni yönetim grubu kök yönetim grubu altında oluşturulur. Üst öğe olarak farklı bir yönetim grubu belirtmek için **Properties.Parent.id** özelliğini kullanın.

- REST API URI'si

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/Contoso?api-version=2020-02-01
  ```

- İstek Gövdesi

  ```json
  {
    "properties": {
      "displayName": "Contoso Group",
      "parent": {
        "id": "/providers/Microsoft.Management/managementGroups/HoldingGroup"
      }
    }
  }
  ```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Yukarıda oluşturulan yönetim grubunu kaldırmak için [Yönetim grupları silme](/rest/api/managementgroups/managementgroups/delete) uç noktasını kullanın:

- REST API URI'si

  ```http
  DELETE https://management.azure.com/providers/Microsoft.Management/managementGroups/Contoso?api-version=2020-02-01
  ```

- Istek gövdesi yok

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, kaynak hiyerarşinizi düzenlemek için bir yönetim grubu oluşturdunuz. Yönetim grubu, abonelikleri veya diğer yönetim gruplarını içerebilir.

Yönetim grupları ve kaynak hiyerarşinizi yönetme hakkında daha fazla bilgi edinmek için şu çalışmaya devam edin:

> [!div class="nextstepaction"]
> [Kaynakları yönetim gruplarıyla yönetme](./manage.md)
