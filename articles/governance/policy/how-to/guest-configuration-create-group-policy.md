---
title: Windows için grup ilkesi taban çizgisinden Konuk yapılandırma ilkesi tanımları oluşturma
description: Windows Server 2019 güvenlik taban çizgisinden grup ilkesi bir ilke tanımına nasıl dönüştüreceğiniz hakkında bilgi edinin.
ms.date: 03/31/2021
ms.topic: how-to
ms.openlocfilehash: a49c8044914c8c23b4f99cad7838652eb94c4b92
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106096589"
---
# <a name="how-to-create-guest-configuration-policy-definitions-from-group-policy-baseline-for-windows"></a>Windows için grup ilkesi taban çizgisinden Konuk yapılandırma ilkesi tanımları oluşturma

Özel ilke tanımları oluşturmadan önce, [Azure Ilke Konuk yapılandırması](../concepts/guest-configuration.md)' na kavramsal genel bakış bilgilerini okumak iyi bir fikirdir. Linux için özel konuk yapılandırma ilkesi tanımları oluşturma hakkında bilgi edinmek için bkz. [Linux Için Konuk yapılandırma ilkeleri oluşturma](./guest-configuration-create-linux.md). Windows için özel konuk yapılandırma ilkesi tanımları oluşturma hakkında bilgi edinmek için bkz. [Windows Için Konuk yapılandırma ilkeleri oluşturma](./guest-configuration-create.md).

Windows’u denetlerken, Konuk Yapılandırması yapılandırma dosyasını oluşturmak için [Desired State Configuration](/powershell/scripting/dsc/overview/overview) (DSC) kaynak modülünü kullanır. DSC yapılandırması makinenin olması gereken durumu tanımlar. Yapılandırmanın değerlendirmesi **uyumlu** değilse, *Auditınotexists* ilke efekti tetiklenir.
[Azure Ilke Konuk yapılandırması](../concepts/guest-configuration.md) yalnızca makineler içindeki ayarları denetler.

> [!IMPORTANT]
> Konuk Yapılandırma uzantısı Azure sanal makinelerinde denetim gerçekleştirmek için gereklidir. Uzantıyı tüm Windows makineleri genelinde ölçekli olarak dağıtmak için aşağıdaki ilke tanımlarını atayın:
> - [Windows VM 'lerinde Konuk yapılandırma Ilkesini etkinleştirmek için önkoşulları dağıtın.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ecd903d-91e7-4726-83d3-a229d7f2e293)
> 
> Özel içerik paketlerinde gizli dizileri veya gizli bilgileri kullanmayın.

DSC topluluğu, aktarılmış grup ilkesi şablonlarını DSC biçimine dönüştürmek için [Baselinemanagement modülünü](https://github.com/microsoft/BaselineManagement) yayımladı. GuestConfiguration cmdlet 'i ile birlikte BaselineManagement modülü, Windows için grup ilkesi içerikten Azure Ilke Konuk yapılandırma paketi oluşturur. BaselineManagement modülünü kullanma hakkında ayrıntılı bilgi için [hızlı başlangıç: Grup ILKESI DSC 'ye dönüştürme](/powershell/scripting/dsc/quickstarts/gpo-quickstart)makalesine bakın.

Bu kılavuzda, bir grup ilkesi nesnesinden (GPO) Azure Ilke Konuk yapılandırma paketi oluşturma işlemini yürüyoruz. İzlenecek yol, Windows Server 2019 güvenlik temelinin dönüştürülmesini özetler, ancak aynı işlem diğer GPO 'Lara de uygulanabilir.  

## <a name="download-windows-server-2019-security-baseline-and-install-related-powershell-modules"></a>Windows Server 2019 güvenlik temelini indirin ve ilgili PowerShell modüllerini yükleyin

**DSC**, **guestconfiguration**, **taban çizgisi yönetimi** ve ilgili Azure modüllerini PowerShell 'e yüklemek için:

1. Bir PowerShell isteminden aşağıdaki komutu çalıştırın:

   ```azurepowershell-interactive
   # Install the BaselineManagement module, Guest Configuration DSC resource module, and relevant Azure modules from PowerShell Gallery
   Install-Module az.resources, az.policyinsights, az.storage, guestconfiguration, gpregistrypolicyparser, securitypolicydsc, auditpolicydsc, baselinemanagement -scope currentuser -Repository psgallery -AllowClobber
   ```

1. Windows Güvenlik uyumluluk araç seti ' nden bir dizin oluşturun ve Windows Server 2019 güvenlik taban çizgisini indirin.

   ```azurepowershell-interactive
   # Download the 2019 Baseline files from https://docs.microsoft.com/windows/security/threat-protection/security-compliance-toolkit-10
   New-Item -Path 'C:\git\policyfiles\downloads' -Type Directory
   Invoke-WebRequest -Uri 'https://download.microsoft.com/download/8/5/C/85C25433-A1B0-4FFA-9429-7E023E7DA8D8/Windows%2010%20Version%201909%20and%20Windows%20Server%20Version%201909%20Security%20Baseline.zip' -Out C:\git\policyfiles\downloads\Server2019Baseline.zip
   ```

1. İndirilen sunucu 2019 taban çizgisini engellemeyi kaldırın ve genişletin.

   ```azurepowershell-interactive
   Unblock-File C:\git\policyfiles\downloads\Server2019Baseline.zip
   Expand-Archive -Path C:\git\policyfiles\downloads\Server2019Baseline.zip -DestinationPath C:\git\policyfiles\downloads\
   ```

1. **MapGuidsToGpoNames.ps1** kullanarak sunucu 2019 temel içeriğini doğrulayın.

   ```azurepowershell-interactive
   # Show content details of downloaded GPOs
   C:\git\policyfiles\downloads\Scripts\Tools\MapGuidsToGpoNames.ps1 -rootdir C:\git\policyfiles\downloads\GPOs\ -Verbose
   ```

## <a name="convert-from-group-policy-to-azure-policy-guest-configuration"></a>grup ilkesi 'den Azure Ilke Konuk yapılandırmasına dönüştürme

Ardından, indirilen sunucu 2019 taban çizgisini Konuk yapılandırma ve temel yönetim modüllerini kullanarak Konuk yapılandırma paketine dönüştürüyoruz.

1. Taban çizgisi yönetim modülünü kullanarak grup ilkesi Istenen durum yapılandırmasına dönüştürün.

   ```azurepowershell-interactive
   ConvertFrom-GPO -Path 'C:\git\policyfiles\downloads\GPOs\{3657C7A2-3FF3-4C21-9439-8FDF549F1D68}\' -OutputPath 'C:\git\policyfiles\' -OutputConfigurationScript -Verbose
   ```

1. Bir ilke içerik paketi oluşturmadan önce dönüştürülen betikleri yeniden adlandırın, biçimlendirin ve çalıştırın.

   ```azurepowershell-interactive
   Rename-Item -Path C:\git\policyfiles\DSCFromGPO.ps1 -NewName C:\git\policyfiles\Server2019Baseline.ps1
   (Get-Content -Path C:\git\policyfiles\Server2019Baseline.ps1).Replace('DSCFromGPO', 'Server2019Baseline') | Set-Content -Path C:\git\policyfiles\Server2019Baseline.ps1
   (Get-Content -Path C:\git\policyfiles\Server2019Baseline.ps1).Replace('PSDesiredStateConfiguration', 'PSDscResources') | Set-Content -Path C:\git\policyfiles\Server2019Baseline.ps1
   C:\git\policyfiles\Server2019Baseline.ps1
   ```

1. Bir Azure Ilkesi Konuk yapılandırması içerik paketi oluşturun.

   ```azurepowershell-interactive
   New-GuestConfigurationPackage -Name Server2019Baseline -Configuration c:\git\policyfiles\localhost.mof -Verbose
   ```

## <a name="create-azure-policy-guest-configuration"></a>Azure Ilke Konuk yapılandırması oluştur

1. Sonraki adım, dosyayı Azure Blob depolama alanına yayımlamaktır. Komut `Publish-GuestConfigurationPackage` `Az.Storage` modülü gerektiriyor.

   ```azurepowershell-interactive
   Publish-GuestConfigurationPackage -Path ./AuditBitlocker.zip -ResourceGroupName  myResourceGroupName -StorageAccountName myStorageAccountName
   ```

1. Konuk yapılandırması özel ilke paketi oluşturulduktan ve karşıya yüklendikten sonra, Konuk yapılandırma ilkesi tanımını oluşturun. `New-GuestConfigurationPolicy`Konuk yapılandırmasını oluşturmak için cmdlet 'ini kullanın.

   ```azurepowershell-interactive
   $NewGuestConfigurationPolicySplat = @{
        ContentUri = $Uri 
        DisplayName = 'Server 2019 Configuration Baseline' 
        Description 'Validation of using a completely custom baseline configuration for Windows VMs' 
        Path = 'C:\git\policyfiles\policy'  
        Platform = Windows 
   }
   New-GuestConfigurationPolicy @NewGuestConfigurationPolicySplat
   ```
    
1. Cmdlet 'ini kullanarak ilke tanımlarını yayımlayın `Publish-GuestConfigurationPolicy` . Cmdlet 'i yalnızca tarafından oluşturulan JSON dosyalarının konumuna işaret eden **Path** parametresine sahiptir `New-GuestConfigurationPolicy` . Yayımla komutunu çalıştırmak için Azure 'da ilke tanımları oluşturma erişiminizin olması gerekir. Belirli yetkilendirme gereksinimleri, [Azure Ilkesine genel bakış](../overview.md#getting-started) sayfasında belgelenmiştir. En iyi yerleşik rol, **kaynak Ilkesi katılımcısı**' dir.

   ```azurepowershell-interactive
   Publish-GuestConfigurationPolicy -Path C:\git\policyfiles\policy\ -Verbose
   ```

## <a name="assign-guest-configuration-policy-definition"></a>Konuk yapılandırma ilkesi tanımı ata

Azure 'da oluşturulan ilkeyle, son adım girişimi atayacaktır. Bkz. girişim, [Portal](../assign-policy-portal.md), [Azure CLI](../assign-policy-azurecli.md)ve [Azure PowerShell](../assign-policy-powershell.md)nasıl atanır.

> [!IMPORTANT]
> Konuk yapılandırma ilkesi tanımlarının **her zaman** _auditınotexists_ ve _deployifnotexists_ ilkelerini birleştiren girişim kullanılarak atanması gerekir. Yalnızca _Auditınotexists_ ilkesi atanırsa, Önkoşullar dağıtılır ve ilke her zaman ' 0 ' sunucularının uyumlu olduğunu gösterir.

Bir ilke tanımını _Deployifnotexists_ efektiyle atamak ek bir erişim düzeyi gerektirir. En az ayrıcalığa izin vermek için, **kaynak Ilkesi katılımcısı**'nı genişleten özel bir rol tanımı oluşturabilirsiniz. Aşağıdaki örnek, _Microsoft. Authorization/Roleatamalar/Write_ ek Izniyle **kaynak ilkesi katılımcısı DINE** adlı bir rol oluşturur.

   ```azurepowershell-interactive
   $subscriptionid = '00000000-0000-0000-0000-000000000000'
   $role = Get-AzRoleDefinition "Resource Policy Contributor"
   $role.Id = $null
   $role.Name = "Resource Policy Contributor DINE"
   $role.Description = "Can assign Policies that require remediation."
   $role.Actions.Clear()
   $role.Actions.Add("Microsoft.Authorization/roleAssignments/write")
   $role.AssignableScopes.Clear()
   $role.AssignableScopes.Add("/subscriptions/$subscriptionid")
   New-AzRoleDefinition -Role $role
   ```

## <a name="next-steps"></a>Sonraki adımlar

- [Konuk yapılandırması](../concepts/guest-configuration.md)olan VM 'leri denetleme hakkında bilgi edinin.
- [Program aracılığıyla ilkelerin nasıl oluşturulduğunu](./programmatically-create.md)anlayın.
- [Uyumluluk verilerini nasıl alabileceğinizi](./get-compliance-data.md)öğrenin.
