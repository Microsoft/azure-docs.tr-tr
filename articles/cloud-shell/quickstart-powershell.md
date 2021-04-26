---
title: Azure Cloud Shell hızlı başlangıç-PowerShell
description: Azure Cloud Shell ile tarayıcınızda PowerShell 'i kullanmayı öğrenin.
author: maertendmsft
ms.author: damaerte
tags: azure-resource-manager
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 10/18/2018
ms.openlocfilehash: 7ff58c4e463b4ad47680b9140403e9ae5e22b057
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105045289"
---
# <a name="quickstart-for-powershell-in-azure-cloud-shell"></a>Azure Cloud Shell 'de PowerShell için hızlı başlangıç

Bu belge [Azure portal](https://portal.azure.com/)Cloud Shell ' de PowerShell 'in nasıl kullanılacağını açıklamaktadır.

> [!NOTE]
> Azure Cloud Shell hızlı başlangıç bir [Bash](quickstart.md) de mevcuttur.

## <a name="start-cloud-shell"></a>Cloud Shell'i Başlatma

1. Azure portal üst gezinti çubuğundan **Cloud Shell** düğmesine tıklayın

   ![Azure portal Azure Cloud Shell başlatmayı gösteren ekran görüntüsü.](media/quickstart-powershell/shell-icon.png)

2. Açılır listeden PowerShell ortamını seçin ve Azure Drive 'da olursunuz `(Azure:)`

   ![Azure Cloud Shell için PowerShell ortamının nasıl seçileceğini gösteren ekran görüntüsü.](media/quickstart-powershell/environment-ps.png)

## <a name="run-powershell-commands"></a>PowerShell komutlarını çalıştır

Cloud Shell normal PowerShell komutlarını çalıştırın, örneğin:

```azurepowershell-interactive
PS Azure:\> Get-Date

# Expected Output
Friday, July 27, 2018 7:08:48 AM

PS Azure:\> Get-AzVM -Status

# Expected Output
ResourceGroupName       Name       Location                VmSize   OsType     ProvisioningState  PowerState
-----------------       ----       --------                ------   ------     -----------------  ----------
MyResourceGroup2        Demo        westus         Standard_DS1_v2  Windows    Succeeded           running
MyResourceGroup         MyVM1       eastus            Standard_DS1  Windows    Succeeded           running
MyResourceGroup         MyVM2       eastus   Standard_DS2_v2_Promo  Windows    Succeeded           deallocated
```

### <a name="interact-with-virtual-machines"></a>Sanal makinelerle etkileşim kurma

Tüm sanal makinelerinizi geçerli aboneliğin altında Dizin aracılığıyla bulabilirsiniz `VirtualMachines` .

```azurepowershell-interactive
PS Azure:\MySubscriptionName\VirtualMachines> dir

    Directory: Azure:\MySubscriptionName\VirtualMachines


Name       ResourceGroupName  Location  VmSize          OsType              NIC ProvisioningState  PowerState
----       -----------------  --------  ------          ------              --- -----------------  ----------
TestVm1    MyResourceGroup1   westus    Standard_DS2_v2 Windows       my2008r213         Succeeded     stopped
TestVm2    MyResourceGroup1   westus    Standard_DS1_v2 Windows          jpstest         Succeeded deallocated
TestVm10   MyResourceGroup2   eastus    Standard_DS1_v2 Windows           mytest         Succeeded     running
```

#### <a name="invoke-powershell-script-across-remote-vms"></a>Uzak VM 'lerde PowerShell betiğini çağırma

 > [!WARNING]
 > Lütfen [Azure VM 'lerinin uzaktan yönetimine Ilişkin sorun giderme](troubleshooting.md#troubleshooting-remote-management-of-azure-vms)bölümüne bakın.

  Bir VM 'nin olduğu varsayılırsa, MyVM1, `Invoke-AzVMCommand` uzak makinede bir PowerShell betik bloğu çağırmak için kullanalım.

  ```azurepowershell-interactive
  Enable-AzVMPSRemoting -Name MyVM1 -ResourceGroupname MyResourceGroup
  Invoke-AzVMCommand -Name MyVM1 -ResourceGroupName MyResourceGroup -Scriptblock {Get-ComputerInfo} -Credential (Get-Credential)
  ```

  Ayrıca, önce VirtualMachines dizinine gidebilir ve `Invoke-AzVMCommand` aşağıdaki gibi çalıştırabilirsiniz.

  ```azurepowershell-interactive
  PS Azure:\> cd MySubscriptionName\ResourceGroups\MyResourceGroup\Microsoft.Compute\virtualMachines
  PS Azure:\MySubscriptionName\ResourceGroups\MyResourceGroup\Microsoft.Compute\virtualMachines> Get-Item MyVM1 | Invoke-AzVMCommand -Scriptblock {Get-ComputerInfo} -Credential (Get-Credential)

  # You will see output similar to the following:

  PSComputerName                                          : 65.52.28.207
  RunspaceId                                              : 2c2b60da-f9b9-4f42-a282-93316cb06fe1
  WindowsBuildLabEx                                       : 14393.1066.amd64fre.rs1_release_sec.170327-1835
  WindowsCurrentVersion                                   : 6.3
  WindowsEditionId                                        : ServerDatacenter
  WindowsInstallationType                                 : Server
  WindowsInstallDateFromRegistry                          : 5/18/2017 11:26:08 PM
  WindowsProductId                                        : 00376-40000-00000-AA947
  WindowsProductName                                      : Windows Server 2016 Datacenter
  WindowsRegisteredOrganization                           :
   ...
  ```

#### <a name="interactively-log-on-to-a-remote-vm"></a>Uzak bir sanal makinede etkileşimli olarak oturum açma

`Enter-AzVM`Azure 'da çalışan bir sanal makinede etkileşimli olarak oturum açmak için ' i kullanabilirsiniz.

  ```azurepowershell-interactive
  PS Azure:\> Enter-AzVM -Name MyVM1 -ResourceGroupName MyResourceGroup -Credential (Get-Credential)
  ```

Ayrıca, `VirtualMachines` önce dizine gidebilir ve `Enter-AzVM` aşağıdaki gibi çalıştırabilirsiniz

  ```azurepowershell-interactive
 PS Azure:\MySubscriptionName\ResourceGroups\MyResourceGroup\Microsoft.Compute\virtualMachines> Get-Item MyVM1 | Enter-AzVM -Credential (Get-Credential)
 ```

### <a name="discover-webapps"></a>WebApps bulma

`WebApps`Dizine girerek Web Apps kaynaklarınızda kolayca gezinebilirsiniz

```azurepowershell-interactive
PS Azure:\MySubscriptionName> dir .\WebApps\

    Directory: Azure:\MySubscriptionName\WebApps

Name            State    ResourceGroup      EnabledHostNames                  Location
----            -----    -------------      ----------------                  --------
mywebapp1       Stopped  MyResourceGroup1   {mywebapp1.azurewebsites.net...   West US
mywebapp2       Running  MyResourceGroup2   {mywebapp2.azurewebsites.net...   West Europe
mywebapp3       Running  MyResourceGroup3   {mywebapp3.azurewebsites.net...   South Central US

# You can use Azure cmdlets to Start/Stop your web apps
PS Azure:\MySubscriptionName\WebApps> Start-AzWebApp -Name mywebapp1 -ResourceGroupName MyResourceGroup1

Name           State    ResourceGroup        EnabledHostNames                   Location
----           -----    -------------        ----------------                   --------
mywebapp1      Running  MyResourceGroup1     {mywebapp1.azurewebsites.net ...   West US

# Refresh the current state with -Force
PS Azure:\MySubscriptionName\WebApps> dir -Force

    Directory: Azure:\MySubscriptionName\WebApps

Name            State    ResourceGroup      EnabledHostNames                  Location
----            -----    -------------      ----------------                  --------
mywebapp1       Running  MyResourceGroup1   {mywebapp1.azurewebsites.net...   West US
mywebapp2       Running  MyResourceGroup2   {mywebapp2.azurewebsites.net...   West Europe
mywebapp3       Running  MyResourceGroup3   {mywebapp3.azurewebsites.net...   South Central US
```

## <a name="ssh"></a>SSH

SSH kullanarak sunucularda veya VM 'Lerde kimlik doğrulaması yapmak için, Cloud Shell içinde ortak özel anahtar çiftini oluşturun ve ortak anahtarı uzak makinede (gibi) yayımlayın `authorized_keys` `/home/user/.ssh/authorized_keys` .

> [!NOTE]
> Kullanarak SSH özel ortak anahtarları oluşturabilir `ssh-keygen` ve bunları `$env:USERPROFILE\.ssh` Cloud Shell ' de yayımlayabilirsiniz.

### <a name="using-ssh"></a>SSH kullanma

Azure PowerShell cmdlet 'lerini kullanarak yeni bir VM yapılandırması oluşturmak için [buradaki](../virtual-machines/linux/quick-create-powershell.md) yönergeleri izleyin.
`New-AzVM`Dağıtımı devre dışı bırakmak için ' a çağrılmadan önce, VM YAPıLANDıRMASıNA SSH ortak anahtarı ekleyin.
Yeni oluşturulan VM, konumdaki ortak anahtarı içerir `~\.ssh\authorized_keys` ve böylece VM 'ye kimlik bilgisi IÇERMEYEN SSH oturumu etkinleştirir.

```azurepowershell-interactive
# Create VM config object - $vmConfig using instructions on linked page above

# Generate SSH keys in Cloud Shell
ssh-keygen -t rsa -b 2048 -f $HOME\.ssh\id_rsa 

# Ensure VM config is updated with SSH keys
$sshPublicKey = Get-Content "$HOME\.ssh\id_rsa.pub"
Add-AzVMSshPublicKey -VM $vmConfig -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"

# Create a virtual machine
New-AzVM -ResourceGroupName <yourResourceGroup> -Location <vmLocation> -VM $vmConfig

# SSH to the VM
ssh azureuser@MyVM.Domain.Com
```

## <a name="list-available-commands"></a>Kullanılabilir komutları listeleyin

`Azure`Sürücü altında, `Get-AzCommand` Içeriğe özgü Azure komutları almak için yazın.

Alternatif olarak, `Get-Command *az* -Module Az.*` kullanılabilir Azure komutlarını bulmak için her zaman kullanabilirsiniz.

## <a name="install-custom-modules"></a>Özel modülleri yükler

`Install-Module` [PowerShell Galerisi][gallery]modül yüklemek için çalıştırabilirsiniz.

## <a name="get-help"></a>Get-Help

`Get-Help`Azure Cloud Shell ' de PowerShell hakkında bilgi almak için yazın.

```azurepowershell-interactive
Get-Help
```

Belirli bir komut için yine de `Get-Help` bir cmdlet 'i takip edebilirsiniz.

```azurepowershell-interactive
Get-Help Get-AzVM
```

## <a name="use-azure-files-to-store-your-data"></a>Verilerinizi depolamak için Azure dosyalarını kullanma

Bir komut dosyası oluşturabilir, söyleyebilir `helloworld.ps1` ve `clouddrive` Shell oturumlarında kullanmak için bu dosyayı kaydedebilirsiniz.

```azurepowershell-interactive
cd $HOME\clouddrive
# Create a new file in clouddrive directory
New-Item helloworld.ps1
# Open the new file for editing
code .\helloworld.ps1
# Add the content, such as 'Hello World!'
.\helloworld.ps1
Hello World!
```

Cloud Shell ' de PowerShell 'i bir sonraki sefer kullandığınızda, `helloworld.ps1` Dosya `$HOME\clouddrive` Azure dosya paylaşımınızı bağlayan dizinin altında bulunur.

## <a name="use-custom-profile"></a>Özel profil kullan

PowerShell ortamınızı, PowerShell profilleri ( `profile.ps1` veya) oluşturarak özelleştirebilirsiniz `Microsoft.PowerShell_profile.ps1` .
`$profile.CurrentUserAllHosts` `$profile.CurrentUserAllHosts` Cloud Shell oturumunda her PowerShell 'e yüklenebilmeleri için (veya) altına kaydedin.

Profil oluşturma [hakkında bilgi][profile]için bkz. profiller.

## <a name="use-git"></a>Git kullan

Cloud Shell Git deposunu kopyalamak için bir [kişisel erişim belirteci][githubtoken] oluşturmanız ve bunu Kullanıcı adı olarak kullanmanız gerekir. Belirtecinizi aldıktan sonra depoyu şu şekilde kopyalayın:

```azurepowershell-interactive
  git clone https://<your-access-token>@github.com/username/repo.git
```

## <a name="exit-the-shell"></a>Kabuktan çıkış yapma

`exit`Oturumu sonlandırmak için yazın.

[bashqs]:quickstart.md
[gallery]:https://www.powershellgallery.com/
[customex]:https://docs.microsoft.com/azure/virtual-machines/windows/extensions-customscript
[profile]: /powershell/module/microsoft.powershell.core/about/about_profiles
[azmount]: ../storage/files/storage-how-to-use-files-windows.md
[githubtoken]: https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/
