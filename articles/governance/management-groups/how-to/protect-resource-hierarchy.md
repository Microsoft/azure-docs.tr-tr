---
title: Kaynak hiyerarşinizi koruma-Azure Idare
description: Kaynak hiyerarşinizi, varsayılan yönetim grubunu ayarlamayı içeren hiyerarşi ayarlarıyla nasıl koruyacağınızı öğrenin.
ms.date: 04/09/2021
ms.topic: conceptual
ms.openlocfilehash: 11c20ccf5aff74d810533cd56e0a7b116f2dc64b
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107303653"
---
# <a name="how-to-protect-your-resource-hierarchy"></a>Kaynak hiyerarşinizi koruma

Kaynaklarınız, kaynak gruplarınız, abonelikleriniz, yönetim gruplarınız ve kiracınız, kaynak hiyerarşinizi topluca yapar. Azure özel rolleri veya Azure Ilke ilkesi atamaları gibi kök yönetim grubundaki ayarlar, kaynak hiyerarşinizdeki her kaynağı etkileyebilir. Kaynak hiyerarşisinin tüm kaynakları olumsuz yönde etkileyebilecek değişikliklerden korunması önemlidir.

Yönetim gruplarında artık, kiracı yöneticisinin bu davranışları denetlemesini sağlayan hiyerarşi ayarları vardır. Bu makale, kullanılabilir hiyerarşi ayarlarının her birini ve bunların nasıl ayarlanacağını ele alır.

## <a name="azure-rbac-permissions-for-hierarchy-settings"></a>Hiyerarşi ayarları için Azure RBAC izinleri

Hiyerarşi ayarlarından herhangi birini yapılandırmak kök yönetim grubunda aşağıdaki iki kaynak sağlayıcısı işlemini gerektirir:

- `Microsoft.Management/managementgroups/settings/write`
- `Microsoft.Management/managementgroups/settings/read`

Bu işlemler yalnızca bir kullanıcının hiyerarşi ayarlarını okumasına ve güncelleştirmesine izin verir. İşlemler, hiyerarşideki yönetim grubu hiyerarşisine veya kaynaklarına başka bir erişim sağlamaz. Bu işlemlerin her ikisi de Azure yerleşik rol **hiyerarşisi ayarları Yöneticisi**' nde bulunur.

## <a name="setting---default-management-group"></a>Ayarlama-varsayılan Yönetim grubu

Varsayılan olarak, kiracı içine eklenen yeni bir abonelik kök yönetim grubunun bir üyesi olarak eklenir. İlke atamaları, Azure rol tabanlı erişim denetimi (Azure RBAC) ve diğer idare yapıları kök yönetim grubuna atanırsa, bu yeni abonelikleri hemen etkiler. Bu nedenle, birçok kuruluş bu yapıları, bu yapıları atamak için istenen yer olsa bile kök yönetim grubuna uygulamaz. Diğer durumlarda, yeni abonelikler için daha kısıtlayıcı bir denetim kümesi istenir, ancak tüm aboneliklere atanmamalıdır. Bu ayar hem kullanım durumlarını destekler.

Yeni abonelikler için varsayılan yönetim grubunun tanımlanmasına izin vererek, kuruluş genelinde idare yapıları kök yönetim grubuna uygulanabilir ve ilke atamaları veya Azure rolü atamaları olan ayrı bir yönetim grubu, yeni bir aboneliğe daha uygun olabilir.

### <a name="set-default-management-group-in-portal"></a>Portalda varsayılan yönetim grubunu ayarla

Bu ayarı Azure portal yapılandırmak için aşağıdaki adımları izleyin:

1. ' Yönetim grupları ' ' nı aramak ve seçmek için arama çubuğunu kullanın.

1. Kök yönetim grubunda, yönetim grubunun adının yanındaki **Ayrıntılar** ' ı seçin.

1. **Ayarlar** altında **Hiyerarşi ayarları**' nı seçin.

1. **Varsayılan yönetim grubunu değiştir** düğmesini seçin.

   > [!NOTE]
   > **Varsayılan yönetim grubunu değiştir** düğmesi devre dışıysa, görüntülenen yönetim grubu kök yönetim grubu değildir veya güvenlik sorumlunuz hiyerarşi ayarlarını değiştirmek için gerekli izinlere sahip değildir.

1. Hiyerarşinizden bir yönetim grubu seçin ve **Seç** düğmesini kullanın.

### <a name="set-default-management-group-with-rest-api"></a>Varsayılan yönetim grubunu REST API ayarla

Bu ayarı REST API yapılandırmak için [Hiyerarşi ayarları](/rest/api/managementgroups/hierarchysettings) uç noktası çağrılır. Bunu yapmak için aşağıdaki REST API URI ve gövde biçimini kullanın. `{rootMgID}`Kök yönetim GRUBUNUZUN kimliğiyle ve `{defaultGroupID}` varsayılan Yönetim grubu olacak YÖNETIM grubu kimliğiyle değiştirin:

- REST API URI'si

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{rootMgID}/settings/default?api-version=2020-02-01
  ```

- İstek Gövdesi

  ```json
  {
      "properties": {
          "defaultManagementGroup": "/providers/Microsoft.Management/managementGroups/{defaultGroupID}"
      }
  }
  ```

Varsayılan yönetim grubunu kök yönetim grubuna geri ayarlamak için aynı uç noktayı kullanın ve **Defaultmanagementgroup** değerini değerine ayarlayın `/providers/Microsoft.Management/managementGroups/{rootMgID}` .

## <a name="setting---require-authorization"></a>Ayar-yetkilendirme gerektir

Herhangi bir Kullanıcı, varsayılan olarak bir kiracı içinde yeni yönetim grupları oluşturabilir. Bir kiracının yöneticileri, yönetim grubu hiyerarşisinde tutarlılık ve uyumluluk sağlamak için yalnızca belirli kullanıcılara bu izinleri sağlamak isteyebilir. Etkinleştirilirse, bir Kullanıcı, `Microsoft.Management/managementGroups/write` kök yönetim grubu üzerinde yeni alt yönetim grupları oluşturmak için bu işlemi gerektirir.

### <a name="set-require-authorization-in-portal"></a>Portalda yetkilendirme gerektir ayarı

Bu ayarı Azure portal yapılandırmak için aşağıdaki adımları izleyin:

1. ' Yönetim grupları ' ' nı aramak ve seçmek için arama çubuğunu kullanın.

1. Kök yönetim grubunda, yönetim grubunun adının yanındaki **Ayrıntılar** ' ı seçin.

1. **Ayarlar** altında **Hiyerarşi ayarları**' nı seçin.

1. **Yeni yönetim grupları oluşturmak için Izinleri iste ' ye** geçiş yapın. seçeneğini açık.

   > [!NOTE]
   > **Yeni yönetim grupları oluşturmak için Izinleri iste.** geçiş devre dışı bırakıldı, görüntülenen yönetim grubu kök yönetim grubu değil ya da güvenlik sorumlunuz hiyerarşi ayarlarını değiştirmek için gerekli izinlere sahip değil.

### <a name="set-require-authorization-with-rest-api"></a>REST API yetkilendirme gerektir ayarla

Bu ayarı REST API yapılandırmak için [Hiyerarşi ayarları](/rest/api/managementgroups/hierarchysettings) uç noktası çağrılır. Bunu yapmak için aşağıdaki REST API URI ve gövde biçimini kullanın. Bu değer bir _Boole_ değeridir, bu nedenle değer için **true** veya **false** değerini sağlayın. **Doğru** değeri, bu yönetim grubu hiyerarşinizi koruma yöntemini sunar:

- REST API URI'si

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{rootMgID}/settings/default?api-version=2020-02-01
  ```

- İstek Gövdesi

  ```json
  {
      "properties": {
          "requireAuthorizationForGroupCreation": true
      }
  }
  ```

Ayarı yeniden kapatmak için aynı uç noktayı kullanın ve **Requireauthorizationforgroupoluşturmayı** **false** değerine ayarlayın.

## <a name="powershell-sample"></a>PowerShell örneği

PowerShell 'in varsayılan yönetim grubunu ayarlamak için ' az ' komutu yoktur veya yetkilendirme gerektir seçeneğini belirleyin, ancak geçici bir çözüm olarak aşağıdaki PowerShell örneğiyle REST API yararlanabilirsiniz:

```powershell
$root_management_group_id = "Enter the ID of root management group"
$default_management_group_id = "Enter the ID of default management group (or use the same ID of the root management group)"

$body = '{
     "properties": {
          "defaultManagementGroup": "/providers/Microsoft.Management/managementGroups/' + $default_management_group_id + '",
          "requireAuthorizationForGroupCreation": true
     }
}'

$token = (Get-AzAccessToken).Token
$headers = @{"Authorization"= "Bearer $token"; "Content-Type"= "application/json"}
$uri = "https://management.azure.com/providers/Microsoft.Management/managementGroups/$root_management_group_id/settings/default?api-version=2020-02-01"

Invoke-RestMethod -Method PUT -Uri $uri -Headers $headers -Body $body
```

## <a name="next-steps"></a>Sonraki adımlar

Yönetim grupları hakkında daha fazla bilgi almak için bkz.:

- [Azure kaynaklarını düzenlemek için yönetim grupları oluşturma](../create-management-group-portal.md)
- [Yönetim gruplarınızı değiştirme, silme veya yönetme](../manage.md)
