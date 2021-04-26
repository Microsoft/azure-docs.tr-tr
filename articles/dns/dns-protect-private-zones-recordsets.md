---
title: Özel DNS Bölgeleri ve kayıtları koruma-Azure DNS
description: Bu öğrenme yolunda, Microsoft Azure DNS 'de özel DNS bölgelerini ve kayıt kümelerini korumaya başlayın.
services: dns
author: asudbring
ms.service: dns
ms.topic: how-to
ms.date: 02/18/2020
ms.author: allensu
ms.openlocfilehash: a68b40852750e124749ac838c50acae2212c4732
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107785892"
---
# <a name="how-to-protect-private-dns-zones-and-records"></a>Özel DNS bölgelerini ve kayıtlarını koruma

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Özel DNS bölgeler ve kayıtlar kritik kaynaklardır. Bir DNS bölgesini veya tek bir DNS kaydını silmek, hizmet kesintisi oluşmasına neden olabilir. DNS bölgelerinin ve kayıtlarının yetkisiz veya yanlışlıkla yapılan değişikliklere karşı korunması önemlidir.

Bu makalede Azure DNS, özel DNS bölgelerinizi ve kayıtlarınızı bu değişikliklere karşı korumanıza nasıl olanak sağladığını açıklamaktadır.  [Azure rol tabanlı erişim denetimi (Azure RBAC)](../role-based-access-control/overview.md) ve [kaynak kilitleri](../azure-resource-manager/management/lock-resources.md)Azure Resource Manager tarafından sunulan iki güçlü menkul Özellik uygulayacağız.

## <a name="azure-role-based-access-control"></a>Azure rol tabanlı erişim denetimi

Azure rol tabanlı erişim denetimi (Azure RBAC), Azure kullanıcıları, grupları ve kaynakları için ayrıntılı erişim yönetimine izin vermez. Azure RBAC ile, kullanıcıların ihtiyaç duyduğu erişim düzeyine izin verebilirsiniz. Azure RBAC 'nin erişimi yönetmenize nasıl yardımcı olduğu hakkında daha fazla bilgi için bkz. [Azure rol tabanlı erişim denetimi (Azure RBAC)](../role-based-access-control/overview.md).

### <a name="the-private-dns-zone-contributor-role"></a>Özel DNS bölgesi katılımcısı rolü

Özel DNS bölgesi katılımcısı rolü, özel DNS kaynaklarını yönetmeye yönelik yerleşik bir roldür. Bir kullanıcıya veya gruba uygulanan bu rol, özel DNS kaynaklarını yönetmesine olanak sağlar.

*Myprivatedns* kaynak grubu, contoso Corporation için beş bölge içerir. Bu kaynak grubuna DNS Yöneticisi Özel DNS bölge katılımcısı izinleri verilmesi, bu DNS bölgeleri üzerinde tam denetim sağlar. Gereksiz izinler verilmesini önler. DNS Yöneticisi sanal makineleri oluşturamaz veya durdurabilir.

Azure RBAC izinlerini atamak için en kolay yol [Azure Portal](../role-based-access-control/role-assignments-portal.md)kullanmaktır.  

Kaynak grubu için **erişim denetimi 'ni (IAM)** açın, **Ekle**' yi seçin, sonra **özel DNS bölgesi katılımcısı** rolünü seçin. İzin vermek için gerekli kullanıcıları veya grupları seçin.

![Kaynak grubu düzeyi Azure portal aracılığıyla Azure RBAC](./media/dns-protect-private-zones-recordsets/rbac1.png)

İzinler, [Azure PowerShell kullanılarak da verilebilir](../role-based-access-control/role-assignments-powershell.md):

```azurepowershell-interactive
# Grant 'Private DNS Zone Contributor' permissions to all zones in a resource group

$rsg = "<resource group name>"
$usr = "<user email address>"
$rol = "Private DNS Zone Contributor"

New-AzRoleAssignment -SignInName $usr -RoleDefinitionName $rol -ResourceGroupName $rsg
```

Eşdeğer komut [Azure CLI aracılığıyla da kullanılabilir](../role-based-access-control/role-assignments-cli.md):

```azurecli-interactive
# Grant 'Private DNS Zone Contributor' permissions to all zones in a resource group

az role assignment create \
--assignee "<user email address>" \
--role "Private DNS Zone Contributor" \
--resource-group "<resource group name>"
```

### <a name="private-zone-level-azure-rbac"></a>Özel bölge düzeyi Azure RBAC

Azure RBAC kuralları bir aboneliğe, bir kaynak grubuna veya tek bir kaynağa uygulanabilir. Bu kaynak tek bir DNS bölgesi veya tek bir kayıt kümesi olabilir.

Örneğin, *Myprivatedns* kaynak grubu *Private.contoso.com* bölgesini ve bir alt bölge *Customers.Private.contoso.com* içerir. CNAME kayıtları her müşteri hesabı için oluşturulur. CNAME kayıtlarını yönetmek için kullanılan yönetici hesabına, *Customers.Private.contoso.com* bölgesinde kayıt oluşturmak için izinler atanır. Hesap yalnızca *Customers.Private.contoso.com* yönetebilir.

Bölge düzeyinde Azure RBAC izinleri Azure portal aracılığıyla verilebilir.  Bölge için **erişim denetimi (IAM)** açın, **Ekle**' yi seçin, sonra **özel DNS bölgesi katılımcısı** rolünü seçin. İzin vermek için gerekli kullanıcıları veya grupları seçin.

![Azure portal aracılığıyla DNS bölge düzeyi Azure RBAC](./media/dns-protect-private-zones-recordsets/rbac2.png)

İzinler, [Azure PowerShell kullanılarak da verilebilir](../role-based-access-control/role-assignments-powershell.md):

```azurepowershell-interactive
# Grant 'Private DNS Zone Contributor' permissions to a specific zone

$rsg = "<resource group name>"
$usr = "<user email address>"
$zon = "<zone name>"
$rol = "Private DNS Zone Contributor"
$rsc = "Microsoft.Network/privateDnsZones"

New-AzRoleAssignment -SignInName $usr -RoleDefinitionName $rol -ResourceGroupName $rsg -ResourceName $zon -ResourceType $rsc
```

Eşdeğer komut [Azure CLI aracılığıyla da kullanılabilir](../role-based-access-control/role-assignments-cli.md):

```azurecli-interactive
# Grant 'Private DNS Zone Contributor' permissions to a specific zone

az role assignment create \
--assignee <user email address> \
--role "Private DNS Zone Contributor" \
--scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/privateDnsZones/<zone name>/"
```

### <a name="record-set-level-azure-rbac"></a>Kayıt kümesi düzeyi Azure RBAC

İzinler, kayıt kümesi düzeyinde uygulanır.  Kullanıcıya ihtiyaç duydukları girişlere denetim verilir ve başka bir değişiklik yapamaz.

Kayıt kümesi düzeyi Azure RBAC izinleri, kayıt kümesi sayfasındaki **Access Control (IAM)** düğmesini kullanarak Azure Portal aracılığıyla yapılandırılabilir:

![Ekran görüntüsünde Access Control (M A) düğmesi gösterilir.](./media/dns-protect-private-zones-recordsets/rbac3.png)

![Ekran görüntüsü rol ataması ekle seçili Access Control gösterir.](./media/dns-protect-private-zones-recordsets/rbac4.png)

Kayıt kümesi düzeyi Azure RBAC izinleri, [Azure PowerShell kullanılarak da verilebilir](../role-based-access-control/role-assignments-powershell.md):

```azurepowershell-interactive
# Grant permissions to a specific record set

$usr = "<user email address>"
$rol = "Private DNS Zone Contributor"
$sco = 
"/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/privateDnsZones/<zone name>/<record type>/<record name>"

New-AzRoleAssignment -SignInName $usr -RoleDefinitionName $rol -Scope $sco
```

Eşdeğer komut [Azure CLI aracılığıyla da kullanılabilir](../role-based-access-control/role-assignments-cli.md):

```azurecli-interactive
# Grant permissions to a specific record set

az role assignment create \
--assignee "<user email address>" \
--role "Private DNS Zone Contributor" \
--scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/privateDnsZones/<zone name>/<record type>/<record name>"
```

### <a name="custom-roles"></a>Özel roller

Yerleşik Özel DNS bölgesi katılımcısı rolü bir DNS kaynağı üzerinde tam denetim imkanı sunar. Daha ayrıntılı denetim sağlamak için kendi özel Azure rollerinizi oluşturmak mümkündür.

CNAMEs 'leri yönetmek için kullanılan hesaba yalnızca CNAME kayıtlarını yönetme izni verilir. Hesap, diğer türlerin kayıtlarını değiştiremiyor. Hesap, bölge silme gibi bölge düzeyindeki işlemleri yapamıyor.

Aşağıdaki örnekte yalnızca CNAME kayıtlarını yönetmek için özel bir rol tanımı gösterilmektedir:

```json
{
    "Name": "Private DNS CNAME Contributor",
    "Id": "",
    "IsCustom": true,
    "Description": "Can manage DNS CNAME records only.",
    "Actions": [
        "Microsoft.Network/privateDnsZones/CNAME/*",
        "Microsoft.Network/privateDNSZones/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
    ],
    "NotActions": [
    ],
    "AssignableScopes": [
        "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ]
}
```

Actions özelliği, aşağıdaki DNS 'e özgü izinleri tanımlar:

* `Microsoft.Network/privateDnsZones/CNAME/*` CNAME kayıtları üzerinde tam denetim verir
* `Microsoft.Network/privateDNSZones/read` DNS özel bölgelerini okuma izni verir, ancak bunları değiştirmemelidir, CNAME 'in oluşturulduğu bölgeyi görmenizi sağlar.

> [!NOTE]
> Kayıt kümelerinin güncelleştirilmesini engellemek için Azure özel rolü kullanma, hala güncelleştirilebilmesine izin verirken etkili bir denetim değildir. Kayıt kümelerinin silinmesini engeller, ancak değiştirilmesini engellemez.  İzin verilen değişiklikler, kayıt kümesinden kayıtları ekleme ve kaldırma, boş bir kayıt kümesi bırakmak için tüm kayıtları kaldırma dahil. Bu, kayıt kümesini bir DNS çözünürlüğü bakış açısından silme ile aynı etkiye sahiptir.

Özel rol tanımları Şu anda Azure portal aracılığıyla tanımlanamaz. Bu rol tanımına dayalı özel bir rol, Azure PowerShell kullanılarak oluşturulabilir:

```azurepowershell-interactive
# Create new role definition based on input file

New-AzRoleDefinition -InputFile <file path>
```

Azure CLı aracılığıyla da oluşturulabilir:

```azurecli-interactive
# Create new role definition based on input file

az role create -inputfile <file path>
```

Daha sonra bu makalede açıklandığı gibi, rol yerleşik rollerle aynı şekilde atanabilir.

Özel roller oluşturma, yönetme ve atama hakkında daha fazla bilgi için bkz. [Azure özel roller](../role-based-access-control/custom-roles.md).

## <a name="resource-locks"></a>Kaynak kilitleri

Azure Resource Manager, kaynakları kilitleme özelliği olan başka tür güvenlik denetimini destekler. Kaynak kilitleri kaynağa uygulanır ve tüm kullanıcılar ve roller arasında etkilidir. Daha fazla bilgi için bkz. [Azure Resource Manager ile kaynakları kilitleme](../azure-resource-manager/management/lock-resources.md).

İki tür kaynak kilidi vardır: **Cannotdelete** ve **ReadOnly**. Bu kilit türleri bir Özel DNS bölgesine veya tek bir kayıt kümesine uygulanabilir.  Aşağıdaki bölümlerde bazı yaygın senaryolar ve kaynak kilitleri kullanılarak bunların nasıl destekleyeceği açıklanır.

### <a name="protecting-against-all-changes"></a>Tüm değişikliklere karşı koruma

Yapılan değişikliklerin oluşmasını engellemek için, bölgeye salt okunur bir kilit uygulayın. Bu kilit, yeni kayıt kümelerinin oluşturulmasını ve varolan kayıt kümelerinin değiştirilmesini veya silinmesini engeller.

Bölge düzeyi kaynak kilitleri Azure portal aracılığıyla oluşturulabilir.  DNS bölgesi sayfasında, **kilitler**' ı seçin ve **+ Ekle**' yi seçin:

![Azure portal aracılığıyla bölge düzeyi kaynak kilitleri](./media/dns-protect-private-zones-recordsets/locks1.png)

Bölge düzeyi kaynak kilitleri [Azure PowerShell](/powershell/module/az.resources/new-azresourcelock)aracılığıyla da oluşturulabilir:

```azurepowershell-interactive
# Lock a DNS zone

$lvl = "<lock level>"
$lnm = "<lock name>"
$rsc = "<zone name>"
$rty = "Microsoft.Network/privateDnsZones"
$rsg = "<resource group name>"

New-AzResourceLock -LockLevel $lvl -LockName $lnm -ResourceName $rsc -ResourceType $rty -ResourceGroupName $rsg
```

Eşdeğer komut [Azure CLI aracılığıyla da kullanılabilir](/cli/azure/lock#az_lock_create):

```azurecli-interactive
# Lock a DNS zone

az lock create \
--lock-type "<lock level>" \
--name "<lock name>" \
--resource-name "<zone name>" \
--namespace "Microsoft.Network" \
--resource-type "privateDnsZones" \
--resource-group "<resource group name>"
```
### <a name="protecting-individual-records"></a>Bireysel kayıtları koruma

Varolan bir DNS kaydının değişikliğe karşı yapılmasını engellemek için kayıt kümesine salt okunur bir kilit uygulayın.

> [!NOTE]
> Kayıt kümesine CanNotDelete kilidi uygulamak, etkili bir denetim değildir. Kayıt kümesinin silinmesini engeller, ancak değiştirilmesini engellemez.  İzin verilen değişiklikler, kayıt kümesinden kayıtları ekleme ve kaldırma, boş bir kayıt kümesi bırakmak için tüm kayıtları kaldırma dahil. Bu, kayıt kümesini bir DNS çözünürlüğü bakış açısından silme ile aynı etkiye sahiptir.

Kayıt kümesi düzeyi kaynak kilitleri, şu anda yalnızca Azure PowerShell kullanılarak yapılandırılabilir.  Azure portal veya Azure CLı 'de desteklenmez.

Azure PowerShell

```azurepowershell-interactive
# Lock a DNS record set

$lvl = "<lock level>"
$lnm = "<lock name>"
$rnm = "<zone name>/<record set name>"
$rty = "Microsoft.Network/privateDnsZones"
$rsg = "<resource group name>"

New-AzResourceLock -LockLevel $lvl -LockName $lnm -ResourceName $rnm -ResourceType $rty -ResourceGroupName $rsg
```
### <a name="protecting-against-zone-deletion"></a>Bölge silmeye karşı koruma

Azure DNS bir bölge silindiğinde, bölgedeki tüm kayıt kümeleri silinir.  Bu işlem geri alınamaz. Yanlışlıkla kritik bir bölgeyi silmenin önemli bir iş etkisi olması olası olur.  Yanlışlıkla bölge silmeye karşı korunması önemlidir.

Bir bölgeye CanNotDelete kilidi uygulamak bölgenin silinmesini engeller. Kilitler alt kaynaklar tarafından devralınır. Kilit, bölgedeki tüm kayıt kümelerinin silinmesini engeller. Yukarıdaki notta açıklandığı gibi, kayıtların mevcut kayıt kümelerinden hala kaldırılabileceği için bu, verimsiz değildir.

Alternatif olarak, bölgenin SOA kayıt kümesi gibi bir kayıt kümesine bir CanNotDelete kilidi uygulayın. Bölge, kayıt kümelerini silmeksizin silinmez. Bu kilit bölge silmeye karşı koruma sağlarken bölge içindeki kayıt kümelerinin de serbestçe değiştirilmesini sağlar. Bölgeyi silmeye yönelik bir girişim yapılırsa Azure Resource Manager Bu kaldırma işlemini algılar. Kaldırma işlemi SOA kayıt kümesini de siler, Azure Resource Manager SOA kilitli olduğu için çağrıyı engeller.  Hiçbir kayıt kümesi silinmez.

Aşağıdaki PowerShell komutu, belirtilen bölgenin SOA kaydında bir CanNotDelete kilidi oluşturur:

```azurepowershell-interactive
# Protect against zone delete with CanNotDelete lock on the record set

$lvl = "CanNotDelete"
$lnm = "<lock name>"
$rnm = "<zone name>/@"
$rty = "Microsoft.Network/privateDnsZones/SOA"
$rsg = "<resource group name>"

New-AzResourceLock -LockLevel $lvl -LockName $lnm -ResourceName $rnm -ResourceType $rty -ResourceGroupName $rsg
```
Yanlışlıkla bölge silmeyi engellemek için başka bir seçenek de özel bir rol kullanmaktır. Bu rol, bölgelerinizi yönetmek için kullanılan hesapların bölge silme izinlerine sahip olmamasını sağlar. 

Bir bölgeyi silmeniz gerektiğinde, iki adımlı bir silmeyi zorunlu kılabilirsiniz:

 - İlk olarak, bölge silme izinleri verin
 - İkinci olarak, bölgeyi silmek için izin verin.

Özel rol, bu hesaplar tarafından erişilen tüm bölgeler için geçerlidir. Abonelik sahibi gibi bölge silme izinlerine sahip hesaplar yine de yanlışlıkla bir bölgeyi silebilir.

Aynı anda, DNS bölgesi korumasına yönelik derinlemesine savunma yaklaşımı olarak hem yaklaşımların kaynak kilitleri hem de özel roller kullanmak mümkündür.

## <a name="next-steps"></a>Sonraki adımlar

* Azure RBAC ile çalışma hakkında daha fazla bilgi için bkz. [Azure rol tabanlı erişim denetimi (Azure RBAC)](../role-based-access-control/overview.md).
* Kaynak kilitleri ile çalışma hakkında daha fazla bilgi için bkz. [Azure Resource Manager ile kaynakları kilitleme](../azure-resource-manager/management/lock-resources.md).
