---
title: Azure Otomasyonu’nda Azure AD kullanarak Azure’a kimlik doğrulaması yapma
description: Bu makalede Azure AD 'nin Azure 'da kimlik doğrulaması için sağlayıcı olarak Azure Otomasyonu 'nda nasıl kullanılacağı açıklanır.
services: automation
ms.date: 03/30/2020
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 34033589a297b1a3a2abb97d346f1da478f950e6
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830295"
---
# <a name="use-azure-ad-to-authenticate-to-azure"></a>Azure’da kimlik doğrulaması yapmak için Azure AD’yi kullanma

[Azure Active Directory (ad)](../active-directory/fundamentals/active-directory-whatis.md) hizmeti, Kullanıcı yönetimi, etki alanı yönetimi ve çoklu oturum açma yapılandırması gibi çeşitli yönetim görevlerini mümkün bir şekilde sunar. Bu makalede, Azure 'da kimlik doğrulaması için sağlayıcı olarak Azure Otomasyonu 'nda Azure AD 'nin nasıl kullanılacağı açıklanır. 

## <a name="install-azure-ad-modules"></a>Azure AD modüllerini yükler

Azure AD 'yi aşağıdaki PowerShell modülleri aracılığıyla etkinleştirebilirsiniz:

* Graph için Azure Active Directory PowerShell (Azurerd ve az modüller). Azure Otomasyonu Azurerd modülü ve son yükseltmesi olan az Module ile birlikte gönderilir. İşlevsellik, Azure AD Kullanıcı (OrgID) kimlik bilgileri tabanlı kimlik doğrulaması kullanılarak Azure 'a etkileşimli olmayan kimlik doğrulaması içerir. Bkz. [Azure AD 2.0.2.76](https://www.powershellgallery.com/packages/AzureAD/2.0.2.76).

* Windows PowerShell için Microsoft Azure Active Directory (MSOnline modülü). Bu modül, Microsoft 365 dahil olmak üzere Microsoft Online ile etkileşim imkanı sunar.

>[!NOTE]
>PowerShell Core, MSOnline modülünü desteklemez. Modül cmdlet 'lerini kullanmak için Windows PowerShell 'den çalıştırmanız gerekir. MSOnline modülü yerine Graph modülleri için daha yeni Azure Active Directory PowerShell 'i kullanmanız önerilir. 

### <a name="preinstallation"></a>Önkurulum

Azure AD modüllerini bilgisayarınıza yüklemeden önce:

* Azurerd/az modülünün önceki sürümlerini ve MSOnline modülünü kaldırın. 

* Yeni PowerShell modüllerinin doğru çalışmasını sağlamak için Microsoft Online Services Sign-In yardımcısını kaldırın.  

### <a name="install-the-azurerm-and-az-modules"></a>Azurerd ve az modüllerini yükler

>[!NOTE]
>Bu modüllerle çalışmak için, Windows 'un 64 bit sürümüyle birlikte PowerShell sürüm 5,1 veya sonraki bir sürümünü kullanmanız gerekir. 

1. Windows Management Framework (WMF) 5,1 ' ü yükler. Bkz. [WMF 5,1 'Yi yükleyip yapılandırma](/powershell/scripting/wmf/setup/install-configure).

2. [PowerShellGet Ile Windows üzerinde Azure PowerShell Install](/powershell/azure/azurerm/install-azurerm-ps)' daki yönergeleri kullanarak Azurerd ve/veya az ' yı yükledikten sonra.

### <a name="install-the-msonline-module"></a>MSOnline modülünü yükler

>[!NOTE]
>MSOnline modülünü yüklemek için yönetici rolünün bir üyesi olmanız gerekir. Bkz. [yönetici rolleri hakkında](/microsoft-365/admin/add-users/about-admin-roles).

1. Bilgisayarınızda Microsoft .NET Framework 3.5. x özelliğinin etkinleştirildiğinden emin olun. Bilgisayarınızda daha yeni bir sürüm yüklü olabilir, ancak .NET Framework eski sürümleriyle geriye dönük uyumluluk etkinleştirilebilir veya devre dışı bırakılabilir. 

2. [Microsoft Online Services oturum açma Yardımcısı](https://www.microsoft.com/Download/details.aspx?id=28177)'nın 64 bitlik sürümünü yükler.

3. Yükseltilmiş bir Windows PowerShell komut istemi oluşturmak için Windows PowerShell 'i yönetici olarak çalıştırın.

4. Azure Active Directory [MSOnline 1,0](http://www.powershellgallery.com/packages/MSOnline/1.0)' dan dağıtın.

5. NuGet sağlayıcısını yüklemek isteyip istemediğiniz sorulursa, Y yazın ve ENTER tuşuna basın.

6. Bu modülü [Psgallery](https://www.powershellgallery.com/)'den yüklemek isteyip istemediğiniz sorulursa, Y YAZıN ve ENTER tuşuna basın.

### <a name="install-support-for-pscredential"></a>PSCredential için destek yüklemesi

Azure Otomasyonu, bir kimlik bilgisi varlığını temsil etmek için [PSCredential](/dotnet/api/system.management.automation.pscredential) sınıfını kullanır. Betikleriniz `PSCredential` cmdlet 'ini kullanarak nesneleri alır `Get-AutomationPSCredential` . Daha fazla bilgi için bkz. [Azure Automation 'Da kimlik bilgisi varlıkları](shared-resources/credentials.md).

## <a name="assign-a-subscription-administrator"></a>Abonelik yöneticisi atama

Azure aboneliği için bir yönetici atamanız gerekir. Bu kişi, abonelik kapsamı için sahip rolüne sahiptir. Bkz. [Azure Otomasyonu 'Nda rol tabanlı erişim denetimi](automation-role-based-access-control.md). 

## <a name="change-the-azure-ad-users-password"></a>Azure AD kullanıcısının parolasını değiştirme

Azure AD kullanıcısının parolasını değiştirmek için:

1. Azure oturumunu kapatma.

2. Yöneticinin, tam Kullanıcı adı (etki alanı dahil) ve geçici bir parola kullanarak Azure 'da yeni oluşturulan Azure AD kullanıcısı olarak oturum açmasını sağlayabilirsiniz. 

3. İstendiğinde, yöneticiden parolayı değiştirmesini isteyin.

## <a name="configure-azure-automation-to-manage-the-azure-subscription"></a>Azure aboneliğini yönetmek için Azure Otomasyonu 'nu yapılandırma

Azure Otomasyonu 'nun Azure AD ile iletişim kurması için Azure AD 'ye Azure bağlantısıyla ilişkili kimlik bilgilerini almalısınız. Bu kimlik bilgilerine örnek olarak kiracı KIMLIĞI, abonelik KIMLIĞI ve beğen verilebilir. Azure ile Azure AD arasındaki bağlantı hakkında daha fazla bilgi için bkz. [kuruluşunuzu Azure Active Directory bağlama](/azure/devops/organizations/accounts/connect-organization-to-azure-ad).

## <a name="create-a-credential-asset"></a>Kimlik bilgisi varlığı oluşturma

Azure AD 'nin Azure kimlik bilgilerini kullanılabilir hale, runbook 'lar ve gereken durum yapılandırması (DSC) betiklerinin bunlara erişebilmesi için Azure AD kimlik bilgilerini güvenli bir şekilde depolamak üzere bir Azure Otomasyonu kimlik bilgisi varlığı oluşturma zamanı. Bunu, Azure portal ya da PowerShell cmdlet 'lerini kullanarak yapabilirsiniz.

### <a name="create-the-credential-asset-in-azure-portal"></a>Azure portal 'da kimlik bilgisi varlığı oluşturma

Kimlik bilgisi varlığını oluşturmak için Azure portal kullanabilirsiniz. Bu işlemi, **paylaşılan kaynaklar** altındaki **kimlik bilgilerini** kullanarak Otomasyon hesabınızdan yapın. Bkz. [Azure Automation 'Da kimlik bilgisi varlıkları](shared-resources/credentials.md).

### <a name="create-the-credential-asset-with-windows-powershell"></a>Windows PowerShell ile kimlik bilgisi varlığı oluşturma

Windows PowerShell 'de yeni bir kimlik bilgisi varlığı hazırlamak için, komut dosyası önce `PSCredential` Atanan Kullanıcı adı ve parolasını kullanarak bir nesne oluşturur. Betik daha sonra bu nesneyi [New-AzureAutomationCredential](/powershell/module/servicemanagement/azure.service/new-azureautomationcredential) cmdlet 'i çağrısıyla varlık oluşturmak için kullanır. Alternatif olarak, komut dosyası [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential) cmdlet 'ini çağırabilir ve kullanıcıdan bir ad ve parola yazıp yazmayabilirler. Bkz. [Azure Automation 'Da kimlik bilgisi varlıkları](shared-resources/credentials.md). 

## <a name="manage-azure-resources-from-an-azure-automation-runbook"></a>Azure Otomasyonu runbook 'tan Azure kaynaklarını yönetme

Azure kaynaklarını Azure Otomasyonu runbook 'lardan, kimlik bilgisi varlığını kullanarak yönetebilirsiniz. Aşağıda, bir Azure aboneliğindeki sanal makineleri durdurmak ve başlatmak için kullanılacak kimlik bilgisi varlığını toplayan örnek bir PowerShell runbook 'u verilmiştir. Bu runbook ilk `Get-AutomationPSCredential` olarak Azure 'da kimlik doğrulaması yapmak için kullanılacak kimlik bilgilerini almak üzere kullanır. Ardından, kimlik bilgisini kullanarak Azure 'a bağlanmak için [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) cmdlet 'ini çağırır. Betiği, birlikte çalışmak üzere bir abonelik seçmek için [Select-azuyeniden göndermeyi Scription](/powershell/module/servicemanagement/azure.service/select-azuresubscription) cmdlet 'ini kullanır. 

```azurepowershell
Workflow Stop-Start-AzureVM 
{ 
    Param 
    (    
        [Parameter(Mandatory=$true)][ValidateNotNullOrEmpty()] 
        [String] 
        $AzureSubscriptionId, 
        [Parameter(Mandatory=$true)][ValidateNotNullOrEmpty()] 
        [String] 
        $AzureVMList="All", 
        [Parameter(Mandatory=$true)][ValidateSet("Start","Stop")] 
        [String] 
        $Action 
    ) 
     
    $credential = Get-AutomationPSCredential -Name 'AzureCredential' 
    Connect-AzAccount -Credential $credential 
    Select-AzureSubscription -SubscriptionId $AzureSubscriptionId 
 
    if($AzureVMList -ne "All") 
    { 
        $AzureVMs = $AzureVMList.Split(",") 
        [System.Collections.ArrayList]$AzureVMsToHandle = $AzureVMs 
    } 
    else 
    { 
        $AzureVMs = (Get-AzVM).Name 
        [System.Collections.ArrayList]$AzureVMsToHandle = $AzureVMs 
 
    } 
 
    foreach($AzureVM in $AzureVMsToHandle) 
    { 
        if(!(Get-AzVM | ? {$_.Name -eq $AzureVM})) 
        { 
            throw " AzureVM : [$AzureVM] - Does not exist! - Check your inputs " 
        } 
    } 
 
    if($Action -eq "Stop") 
    { 
        Write-Output "Stopping VMs"; 
        foreach -parallel ($AzureVM in $AzureVMsToHandle) 
        { 
            Get-AzVM | ? {$_.Name -eq $AzureVM} | Stop-AzVM -Force 
        } 
    } 
    else 
    { 
        Write-Output "Starting VMs"; 
        foreach -parallel ($AzureVM in $AzureVMsToHandle) 
        { 
            Get-AzVM | ? {$_.Name -eq $AzureVM} | Start-AzVM 
        } 
    } 
}
```  

## <a name="next-steps"></a>Sonraki adımlar

* Kimlik bilgileri kullanımı hakkında ayrıntılı bilgi için bkz. [Azure Otomasyonu 'nda kimlik bilgilerini yönetme](shared-resources/credentials.md).
* Modüller hakkında daha fazla bilgi için bkz. [Azure Automation 'da modülleri yönetme](shared-resources/modules.md).
* Bir runbook 'u başlatmanız gerekiyorsa bkz. [Azure Otomasyonu 'nda runbook başlatma](start-runbooks.md).
* PowerShell ayrıntıları için bkz. [PowerShell belgeleri](/powershell/scripting/overview).
