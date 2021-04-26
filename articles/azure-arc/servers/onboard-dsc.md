---
title: Windows PowerShell DSC kullanarak bağlı makine aracısını yükler
description: Bu makalede, Windows PowerShell DSC 'yi kullanarak Azure Arc etkin sunucularını kullanarak makineleri Azure 'a bağlamayı öğreneceksiniz.
ms.date: 09/24/2020
ms.topic: conceptual
ms.openlocfilehash: c0ae9c97afe14559aa36c1b8387f07897aa4c43b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100587651"
---
# <a name="how-to-install-the-connected-machine-agent-using-windows-powershell-dsc"></a>Windows PowerShell DSC kullanarak bağlı makine aracısını yüklemek

[Windows PowerShell Istenen durum yapılandırması](/powershell/scripting/dsc/getting-started/winGettingStarted) 'nı (DSC) kullanarak bir Windows bilgisayarı için yazılım yükleme ve yapılandırmasını otomatik hale getirebilirsiniz. Bu makalede, Azure Arc etkin sunucuları bağlı makine aracısını karma Windows makinelerine yüklemek için DSC 'nin nasıl kullanılacağı açıklanır.

## <a name="requirements"></a>Gereksinimler

- Windows PowerShell sürüm 4,0 veya üzeri

- [AzureConnectedMachineDsc](https://www.powershellgallery.com/packages/AzureConnectedMachineDsc) DSC modülü

- Makineleri etkileşimli olmayan Azure Arc etkin sunucularına bağlamak için bir hizmet sorumlusu. Zaten yay özellikli sunucular için bir hizmet sorumlusu oluşturmadıysanız, [ölçeğe ekleme için bir hizmet sorumlusu oluşturma](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale) bölümünde bulunan adımları izleyin.

## <a name="install-the-connectedmachine-dsc-module"></a>ConnectedMachine DSC modülünü yükler

1. Modülü el ile yüklemek için, kaynak kodunu indirin ve proje dizininin içeriğini içine ayıklayın `$env:ProgramFiles\WindowsPowerShell\Modules folder` . Veya PowerShellGet kullanarak PowerShell galerisinden yüklemek için aşağıdaki komutu çalıştırın (PowerShell 5,0 ' de):

    ```powershell
    Find-Module -Name AzureConnectedMachineDsc -Repository PSGallery | Install-Module
    ```

2. Yüklemeyi onaylamak için aşağıdaki komutu çalıştırın ve Azure bağlı makinesi DSC kaynaklarını mevcut olarak görtığınızdan emin olun.

    ```powershell
    Get-DscResource -Module AzureConnectedMachineDsc
    ```

   Çıktıda aşağıdakine benzer bir şey görmeniz gerekir:

   ![Bağlı makine DSC modülü yükleme örneği onayı](./media/onboard-dsc/confirm-module-installation.png)

## <a name="install-the-agent-and-connect-to-azure"></a>Aracıyı yükleyip Azure 'a bağlanın

Bu modüldeki kaynaklar, Azure bağlı makine Aracısı yapılandırmasını yönetmek için tasarlanmıştır. Ayrıca, klasöründe bulunan bir PowerShell betiğiyle birlikte bulunur `AzureConnectedMachineAgent.ps1` `AzureConnectedMachineDsc\examples` . İndirme ve yüklemeyi otomatikleştirmek ve Azure Arc ile bağlantı kurmak için topluluk kaynaklarını kullanır. Bu betik, [Azure Portal makalesinden karma makineleri Azure 'A bağlama](onboard-portal.md) bölümünde açıklanan benzer adımları gerçekleştirir.

Makinenin bir ara sunucu üzerinden hizmete iletişim kurması gerekiyorsa, aracıyı yükledikten sonra [burada](manage-agent.md#update-or-remove-proxy-settings)açıklanan bir komut çalıştırmanız gerekir. Bu, proxy sunucu sistemi ortam değişkenini ayarlar `https_proxy` . Komutu el ile çalıştırmak yerine, [ComputeManagementDsc](https://www.powershellgallery.com/packages/ComputerManagementDsc) modülünü kullanarak bu adımı DSC ile gerçekleştirebilirsiniz.

>[!NOTE]
>DSC 'nin çalışmasına izin vermek için, Windows 'un bir localhost yapılandırması çalıştırırken bile PowerShell uzak komutlarını alacak şekilde yapılandırılması gerekir. Ortamınızı doğru şekilde yapılandırmak için, yalnızca `Set-WsManQuickConfig -Force` yükseltilmiş bir PowerShell terminalinde çalıştırın.
>

Yapılandırma belgeleri (MOF dosyaları), cmdlet 'i kullanılarak makineye uygulanabilir `Start-DscConfiguration` .

Aşağıda, kullanılacak PowerShell betiğine geçirdiğiniz parametreler verilmiştir.

- `TenantId`: Adanmış Azure AD örneğinizi temsil eden benzersiz tanımlayıcı (GUID).

- `SubscriptionId`: İçinde makinelere istediğiniz Azure aboneliğinizin abonelik KIMLIĞI (GUID).

- `ResourceGroup`: Bağlı makinelerinizin ait olmasını istediğiniz kaynak grubu adı.

- `Location`: [Desteklenen Azure bölgelerine](overview.md#supported-regions)bakın. Bu konum, kaynak grubunun konumuyla aynı veya farklı olabilir.

- `Tags`: Bağlı makine kaynağına uygulanması gereken etiketlerin dize dizisi.

- `Credential`: [Hizmet sorumlusu](onboard-service-principal.md)kullanarak, makineleri ölçekli olarak kaydettirmek Için kullanılan **ApplicationId** ve **Password** içeren bir PowerShell kimlik bilgisi nesnesi.

1. Bir PowerShell konsolunda, dosyayı kaydettiğiniz klasöre gidin `.ps1` .

2. MOF belgesini derlemek için aşağıdaki PowerShell komutlarını çalıştırın (DSC yapılandırmalarının derlenmesi hakkında daha fazla bilgi için bkz. [DSC yapılandırması](/powershell/scripting/dsc/configurations/configurations):

    ```powershell
    .\`AzureConnectedMachineAgent.ps1 -TenantId <TenantId GUID> -SubscriptionId <SubscriptionId GUID> -ResourceGroup '<ResourceGroupName>' -Location '<LocationName>' -Tags '<Tag>' -Credential <psCredential>
    ```

3. Bu, `localhost.mof file` adlı yeni bir klasörde oluşturulur `C:\dsc` .

Aracıyı yükledikten ve Azure Arc etkin sunucularına bağlanacak şekilde yapılandırdıktan sonra, sunucunun başarıyla bağlandığını doğrulamak için Azure portal gidin. [Azure portalında](https://aka.ms/hybridmachineportal) makinelerinizi görüntüleyin.

## <a name="adding-to-existing-configurations"></a>Mevcut yapılandırmalara ekleme

Bu kaynak, bir makine için uçtan uca yapılandırmayı temsil etmek üzere mevcut DSC yapılandırmalarına eklenebilir. Örneğin, bu kaynağı güvenli işletim sistemi ayarlarını ayarlayan bir yapılandırmaya eklemek isteyebilirsiniz.

PowerShell Galerisi bileşim [Teresource](https://www.powershellgallery.com/packages/compositeresource) modülü, yapılandırmaların birleştirilmesinin daha da basitleşmesi için örnek yapılandırmanın [bileşik kaynağını](/powershell/scripting/dsc/resources/authoringResourceComposite) oluşturmak üzere kullanılabilir.

## <a name="next-steps"></a>Sonraki adımlar

* Sorun giderme bilgileri, [bağlı makine Aracısı sorunlarını giderme kılavuzunda](troubleshoot-agent-onboard.md)bulunabilir.

* VM [Konuk yapılandırması](../../governance/policy/concepts/guest-configuration.md), makinenin beklenen Log Analytics çalışma alanına rapor olduğunu doğrulama, [VM 'lerle Azure izleyici](../../azure-monitor/vm/vminsights-enable-policy.md)ile izlemeyi etkinleştirme ve çok daha birçok şey için [Azure ilkesi](../../governance/policy/overview.md)'ni kullanarak makinenizi yönetmeyi öğrenin.

* [Log Analytics Aracısı](../../azure-monitor/agents/log-analytics-agent.md)hakkında daha fazla bilgi edinin. Makinede çalışan işletim sistemi ve iş yüklerini önceden izlemek, Otomasyon Runbook 'larını veya Güncelleştirme Yönetimi gibi çözümleri kullanarak yönetmek ya da [Azure Güvenlik Merkezi](../../security-center/security-center-introduction.md)gibi diğer Azure hizmetlerini kullanmak istediğinizde Windows ve Linux için Log Analytics Aracısı gerekir.