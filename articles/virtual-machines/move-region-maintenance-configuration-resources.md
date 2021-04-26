---
title: Bakım yapılandırmasıyla ilişkili kaynakları başka bir bölgeye taşıma
description: Bir VM bakım yapılandırmasıyla ilişkili kaynakları başka bir Azure bölgesine taşımayı öğrenin
author: shants123
ms.service: virtual-machines
ms.topic: how-to
ms.date: 03/04/2020
ms.author: shants
ms.openlocfilehash: 4427071edf237d82e8a99d44678d77d23e180fff
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107865252"
---
# <a name="move-resources-in-a-maintenance-control-configuration-to-another-region"></a>Bakım denetim yapılandırmasındaki kaynakları başka bir bölgeye taşıma

Bir bakım denetimi yapılandırmasıyla ilişkili kaynakları farklı bir Azure bölgesine taşımak için bu makaleyi izleyin. Bir yapılandırmayı bir kaç nedenden dolayı taşımak isteyebilirsiniz. Örneğin, yeni bir bölgeden yararlanmak için, belirli bir bölgedeki özellikleri veya hizmetleri dağıtmak, iç ilke ve idare gereksinimlerini karşılamak veya kapasite planlamasına yanıt vermek için.

[Bakım denetimi](maintenance-control.md), özelleştirilmiş bakım yapılandırmalarında, platform güncelleştirmelerinin sanal makinelere ve Azure adanmış konaklara nasıl uygulandığını denetlemenize olanak tanır. Bakım denetimini bölgeler arasında taşımak için birkaç senaryo vardır:

- Bir bakım yapılandırmasıyla ilişkili kaynakları taşımak, ancak yapılandırmanın kendisi için değil, bu makaleye uyun.
- Bakım denetimi yapılandırmanızı taşımak, ancak yapılandırmayla ilişkili kaynakları değil, [Bu yönergeleri](move-region-maintenance-configuration.md)izleyin.
- Bakım yapılandırmasını ve bununla ilişkili kaynakları taşımak için önce [Bu yönergeleri](move-region-maintenance-configuration.md)uygulayın. Ardından, bu makaledeki yönergeleri izleyin.

## <a name="prerequisites"></a>Önkoşullar

Bir bakım denetimi yapılandırmasıyla ilişkili kaynakları taşımaya başlamadan önce:

- Başlamadan önce, taşıdığınız kaynakların yeni bölgede mevcut olduğundan emin olun.
- Taşımak istediğiniz Azure VM 'Leri ve Azure adanmış Konakları ile ilişkili bakım denetimi yapılandırmalarının doğrulanması. Her kaynağı ayrı ayrı denetleyin. Şu anda birden fazla kaynağa yönelik yapılandırmaların alınması mümkün değildir.
- Bir kaynak için yapılandırma alırken:
    - Azure ayrılmış ana bilgisayar KIMLIĞI değil, hesap için abonelik KIMLIĞINI kullandığınızdan emin olun.
    - CLı:--output tablo parametresi yalnızca okunabilirlik için kullanılır ve silinebilir veya değiştirilebilir.
    - PowerShell: Format-Table name parametresi yalnızca okunabilirlik için kullanılır ve silinebilir veya değiştirilebilir.
    - PowerShell kullanıyorsanız, ilişkili yapılandırmalara sahip olmayan bir kaynak için yapılandırma listedenemeye çalıştığınızda bir hata alırsınız. Hata şuna benzer: "Işlem şu durumla başarısız oldu: ' Not Found '. Ayrıntılar: 404 Istemci hatası: URL için bulunamadı.

    
## <a name="prepare-to-move"></a>Taşımaya hazırlanma

1. Başlamadan önce bu değişkenleri tanımlayın. Her biri için bir örnek sağladık.

    **Değişken** | **Ayrıntılar** | **Örnek**
    --- | ---
    $subId | Bakım yapılandırmasını içeren aboneliğin KIMLIĞI | "bizim-abonelik KIMLIĞI"
    $rsrcGroupName | Kaynak grubu adı (Azure VM) | "VMResourceGroup"
    $vmName | VM kaynağı adı |  MyVM
    $adhRsrcGroupName |  Kaynak grubu (ayrılmış konaklar) | "HostResourceGroup"
    $adh | Adanmış konak adı | "myHost"
    $adhParentName | Üst kaynak adı | HostGroup
    
2. PowerShell [Get-Azconfigurationatama](/powershell/module/az.maintenance/get-azconfigurationassignment) komutunu kullanarak bakım yapılandırmasını almak için:

    - Azure ayrılmış konakları için şunu çalıştırın:
        ```
        Get-AzConfigurationAssignment -ResourceGroupName $adhRsrcGroupName -ResourceName $adh -ResourceType hosts -ProviderName Microsoft.Compute -ResourceParentName $adhParentName -ResourceParentType hostGroups | Format-Table Name
        ```

    - Azure VM 'Leri için şunu çalıştırın:

        ```
        Get-AzConfigurationAssignment -ResourceGroupName $rgName -ResourceName $vmName -ProviderName Microsoft.Compute -ResourceType virtualMachines | Format-Table Name
        ```
3. CLı [az Maintenance atama](/cli/azure/maintenance/assignment) komutunu kullanarak bakım yapılandırmasını almak için:

    - Azure adanmış konaklar için:

        ```
        az maintenance assignment list --subscription $subId --resource-group $adhRsrcGroupName --resource-name $adh --resource-type hosts --provider-name Microsoft.Compute --resource-parent-name $adhParentName --resource-parent-type hostGroups --query "[].{HostResourceGroup:resourceGroup,ConfigName:name}" --output table
        ```

    - Azure VM 'Leri için:

        ```
        az maintenance assignment list --subscription $subId --provider-name Microsoft.Compute --resource-group $rsrcGroupName --resource-name $vmName --resource-type virtualMachines --query "[].{HostResourceGroup:resourceGroup, ConfigName:name}" --output table
        ```


## <a name="move"></a>Taşı 

1. Azure VM 'lerini yeni bölgeye taşımak için [Bu yönergeleri izleyin](../site-recovery/azure-to-azure-tutorial-migrate.md?toc=/azure/virtual-machines/windows/toc.json&bc=/azure/virtual-machines/windows/breadcrumb/toc.json) .
2. Kaynaklar taşındıktan sonra, bakım yapılandırmalarının taşınmadığınıza bağlı olarak, bakım yapılandırmasını yeni bölgedeki kaynaklara uygun şekilde yeniden uygulayın. [PowerShell](../virtual-machines/maintenance-control-powershell.md) veya [CLI](../virtual-machines/maintenance-control-cli.md)kullanarak bir kaynağa bakım yapılandırması uygulayabilirsiniz.


## <a name="verify-the-move"></a>Taşımayı doğrulama

Yeni bölgedeki kaynakları doğrulayın ve yeni bölgedeki kaynaklar için ilişkili konfigürasyonları doğrulayın. 

## <a name="clean-up-source-resources"></a>Kaynak kaynaklarını Temizleme

Taşıma işleminden sonra kaynak bölgedeki taşınan kaynakları silmeyi göz önünde bulundurun.


## <a name="next-steps"></a>Sonraki adımlar

Bakım yapılandırması ' nı taşımanız gerekiyorsa [Bu yönergeleri](move-region-maintenance-configuration.md) izleyin. 
