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
ms.openlocfilehash: 72261989b7cee9d2251eb18b36431ec807b0e874
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72791608"
---
# <a name="quickstart-for-powershell-in-azure-cloud-shell"></a>Azure Cloud Shell 'de PowerShell için hızlı başlangıç

Bu belge [Azure portal](https://portal.azure.com/)Cloud Shell ' de PowerShell 'in nasıl kullanılacağını açıklamaktadır.

> [!NOTE]
> Azure Cloud Shell hızlı başlangıç bir [Bash](quickstart.md) de mevcuttur.

## <a name="start-cloud-shell"></a>Cloud Shell Başlat

1. Azure portal üst gezinti çubuğundan **Cloud Shell** düğmesine tıklayın

   ![](media/quickstart-powershell/shell-icon.png)

2. Açılır listeden PowerShell ortamını seçin ve Azure sürücüsünde olursunuz `(Azure:)`

   ![](media/quickstart-powershell/environment-ps.png)

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

## <a name="navigate-azure-resources"></a>Azure kaynaklarında gezin

 1. `Azure` sürücüdeki tüm aboneliklerinizi listeleyin

    ```azurepowershell-interactive
    PS Azure:\> dir
    ```

 2. tercih ettiğiniz aboneliğe `cd`

    ```azurepowershell-interactive
    PS Azure:\> cd MySubscriptionName
    PS Azure:\MySubscriptionName>
    ```

 3. Geçerli abonelik kapsamındaki tüm Azure kaynaklarınızı görüntüleyin

    Azure kaynaklarınızın birden çok görünümünü listelemek için `dir` yazın.

    ```azurepowershell-interactive
    PS Azure:\MySubscriptionName> dir

        Directory: azure:\MySubscriptionName

    Mode Name
    ---- ----
    +    AllResources
    +    ResourceGroups
    +    StorageAccounts
    +    VirtualMachines
    +    WebApps
    ```

### <a name="allresources-view"></a>AllResources görünümü

Azure kaynaklarınızı görüntülemek için `AllResources` dizin altına `dir` yazın.

```azurepowershell-interactive
PS Azure:\MySubscriptionName> dir AllResources
```

### <a name="explore-resource-groups"></a>Kaynak gruplarını keşfet

 `ResourceGroups` dizinine ve belirli bir kaynak grubunun içine giderek sanal makineleri bulabilirsiniz.

```azurepowershell-interactive
PS Azure:\MySubscriptionName> cd ResourceGroups\MyResourceGroup1\Microsoft.Compute\virtualMachines

PS Azure:\MySubscriptionName\ResourceGroups\MyResourceGroup1\Microsoft.Compute\virtualMachines> dir


    Directory: Azure:\MySubscriptionName\ResourceGroups\MyResourceGroup1\Microsoft.Compute\virtualMachines


VMName    Location   ProvisioningState VMSize          OS            SKU             OSVersion AdminUserName  NetworkInterfaceName
------    --------   ----------------- ------          --            ---             --------- -------------  --------------------
TestVm1   westus     Succeeded         Standard_DS2_v2 WindowsServer 2016-Datacenter Latest    AdminUser      demo371
TestVm2   westus     Succeeded         Standard_DS1_v2 WindowsServer 2016-Datacenter Latest    AdminUser      demo271
```

> [!NOTE]
> `dir`yazdığınızda ikinci zaman Cloud Shell öğeleri çok daha hızlı bir şekilde görüntüleyebileceğini fark edebilirsiniz.
> Bunun nedeni, daha iyi bir kullanıcı deneyimi için alt öğelerin bellekte önbelleğe alınmasıdır.
Ancak, yeni verileri almak için `dir -Force` her zaman kullanabilirsiniz.

### <a name="navigate-storage-resources"></a>Depolama kaynaklarında gezin

`StorageAccounts` dizinine girerek tüm depolama kaynaklarınızda kolayca gezinebilirsiniz

```azurepowershell-interactive
PS Azure:\MySubscriptionName\StorageAccounts\MyStorageAccountName\Files> dir

    Directory: Azure:\MySubscriptionNameStorageAccounts\MyStorageAccountName\Files

Name          ConnectionString
----          ----------------
MyFileShare1  \\MyStorageAccountName.file.core.windows.net\MyFileShare1;AccountName=MyStorageAccountName AccountKey=<key>
MyFileShare2  \\MyStorageAccountName.file.core.windows.net\MyFileShare2;AccountName=MyStorageAccountName AccountKey=<key>
MyFileShare3  \\MyStorageAccountName.file.core.windows.net\MyFileShare3;AccountName=MyStorageAccountName AccountKey=<key>
```

Bağlantı dizesiyle, Azure dosya paylaşımının bağlanması için aşağıdaki komutu kullanabilirsiniz.

```azurepowershell-interactive
net use <DesiredDriveLetter>: \\<MyStorageAccountName>.file.core.windows.net\<MyFileShareName> <AccountKey> /user:Azure\<MyStorageAccountName>
```

Ayrıntılar için bkz. [Azure dosya paylaşma bağlama ve Windows 'da paylaşıma erişme][azmount].

Ayrıca, Azure dosyaları paylaşımının altındaki dizinlere aşağıdaki gibi gidebilirsiniz:

```azurepowershell-interactive
PS Azure:\MySubscriptionName\StorageAccounts\MyStorageAccountName\Files> cd .\MyFileShare1\
PS Azure:\MySubscriptionName\StorageAccounts\MyStorageAccountName\Files\MyFileShare1> dir

Mode  Name
----  ----
+     TestFolder
.     hello.ps1
```

### <a name="interact-with-virtual-machines"></a>Sanal makinelerle etkileşim kurma

Tüm sanal makinelerinizi geçerli aboneliğin altında `VirtualMachines` dizin aracılığıyla bulabilirsiniz.

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

  Bir sanal makineniz olduğu varsayılarak, MyVM1, uzak makinede bir PowerShell betik bloğu çağırmak için `Invoke-AzVMCommand` kullanalım.

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

Azure 'da çalışan bir sanal makinede etkileşimli olarak oturum açmak için `Enter-AzVM` kullanabilirsiniz.

  ```azurepowershell-interactive
  PS Azure:\> Enter-AzVM -Name MyVM1 -ResourceGroupName MyResourceGroup -Credential (Get-Credential)
  ```

`VirtualMachines` önce, ilk olarak dizinine gidebilir ve `Enter-AzVM` aşağıdaki şekilde çalıştırabilirsiniz

  ```azurepowershell-interactive
 PS Azure:\MySubscriptionName\ResourceGroups\MyResourceGroup\Microsoft.Compute\virtualMachines> Get-Item MyVM1 | Enter-AzVM -Credential (Get-Credential)
 ```

### <a name="discover-webapps"></a>WebApps bulma

`WebApps` dizinine girerek Web Apps kaynaklarınızda kolayca gezinebilirsiniz

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

SSH kullanarak sunucularda veya VM 'Lerde kimlik doğrulaması yapmak için Cloud Shell içinde ortak özel anahtar çiftini oluşturun ve ortak anahtarı, uzak makinede `/home/user/.ssh/authorized_keys`gibi `authorized_keys` yayımlayın.

> [!NOTE]
> `ssh-keygen` kullanarak SSH özel-ortak anahtarları oluşturabilir ve bunları Cloud Shell `$env:USERPROFILE\.ssh` yayımlayabilirsiniz.

### <a name="using-ssh"></a>SSH kullanma

Azure PowerShell cmdlet 'lerini kullanarak yeni bir VM yapılandırması oluşturmak için [buradaki](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-powershell) yönergeleri izleyin.
Dağıtımı kapatmak için `New-AzVM` çağrılmadan önce, VM yapılandırmasına SSH ortak anahtarı ekleyin.
Yeni oluşturulan VM, `~\.ssh\authorized_keys` konumdaki ortak anahtarı içerir ve böylece VM 'ye kimlik bilgisi içermeyen SSH oturumu etkinleştirir.

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

`Azure` sürücü altında, içeriğe özgü Azure komutlarını almak için `Get-AzCommand` yazın.

Alternatif olarak, kullanılabilir Azure komutlarını bulmak için `Get-Command *az* -Module Az.*` her zaman kullanabilirsiniz.

## <a name="install-custom-modules"></a>Özel modülleri yükler

[PowerShell Galerisi][gallery]modülleri yüklemek için `Install-Module` çalıştırabilirsiniz.

## <a name="get-help"></a>Yardım alın

Azure Cloud Shell PowerShell hakkında bilgi almak için `Get-Help` yazın.

```azurepowershell-interactive
Get-Help
```

Belirli bir komut için yine de bir cmdlet 'i `Get-Help` yapabilirsiniz.

```azurepowershell-interactive
Get-Help Get-AzVM
```

## <a name="use-azure-files-to-store-your-data"></a>Verilerinizi depolamak için Azure dosyalarını kullanma

Bir komut dosyası oluşturabilir, `helloworld.ps1`söyleyebilirsiniz ve bunu kabuk oturumlarında kullanmak için `clouddrive` kaydedebilirsiniz.

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

Cloud Shell içinde PowerShell kullandığınızda `helloworld.ps1` dosyası, Azure dosya paylaşımınızı bağlayan `$HOME\clouddrive` dizininde bulunur.

## <a name="use-custom-profile"></a>Özel profil kullan

PowerShell ortamınızı, PowerShell `profile.ps1` profilleri (veya `Microsoft.PowerShell_profile.ps1`) oluşturarak özelleştirebilirsiniz.
`$profile.CurrentUserAllHosts` (veya `$profile.CurrentUserAllHosts`) altına kaydederek Cloud Shell oturumunda her PowerShell 'e yüklenebilmesini sağlayın.

Profil oluşturma [hakkında bilgi][profile]için bkz. profiller.

## <a name="use-git"></a>Git kullan

Cloud Shell Git deposunu kopyalamak için bir [kişisel erişim belirteci][githubtoken] oluşturmanız ve bunu Kullanıcı adı olarak kullanmanız gerekir. Belirtecinizi aldıktan sonra depoyu şu şekilde kopyalayın:

```azurepowershell-interactive
  git clone https://<your-access-token>@github.com/username/repo.git
```

## <a name="exit-the-shell"></a>Kabuktan çıkış yapma

Oturumu sonlandırmak için `exit` yazın.

[bashqs]:quickstart.md
[gallery]:https://www.powershellgallery.com/
[customex]:https://docs.microsoft.com/azure/virtual-machines/windows/extensions-customscript
[profile]: https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.core/about/about_profiles
[azmount]: https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows
[githubtoken]: https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/
