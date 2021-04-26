---
title: Azure PowerShell ile bir Windows sanal makinesinde SQL Server oluşturun | Microsoft Docs
description: Bu öğreticide, Azure PowerShell SQL Server 2017 çalıştıran bir Windows sanal makinesi oluşturmak için nasıl kullanılacağı gösterilmektedir.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: deployment
ms.topic: quickstart
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 12/21/2018
ms.author: mathoma
ms.reviewer: jroth
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 350df1aede076120fd14dc46da351835c894c5b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97356846"
---
# <a name="quickstart-create-sql-server-on-a-windows-virtual-machine-with-azure-powershell"></a>Hızlı başlangıç: Azure PowerShell ile Windows sanal makinesinde SQL Server oluşturma

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Bu hızlı başlangıçta Azure PowerShell ile SQL Server sanal makine (VM) oluşturma adımları sağlanır.

> [!TIP]
> - Bu hızlı başlangıç, hızlı bir şekilde bir SQL VM sağlama ve VM’ye bağlanma yolu sağlar. SQL VM’leri oluşturmaya yönelik diğer Azure PowerShell seçenekleri hakkında daha fazla bilgi için bkz. [Azure PowerShell ile SQL Server VM'leri için sağlama kılavuzu](create-sql-vm-powershell.md).
> - SQL Server sanal makineleri hakkında sorularınız olursa [Sık Sorulan Sorular](frequently-asked-questions-faq.md) bölümüne bakın.

## <a name="get-an-azure-subscription"></a><a id="subscription"></a> Azure aboneliği edinme

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.


## <a name="get-azure-powershell"></a><a id="powershell"></a> Azure PowerShell edinme

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

## <a name="configure-powershell"></a>PowerShell yapılandırma

1. PowerShell 'i açın ve **Connect-AzAccount** komutunu çalıştırarak Azure hesabınıza erişim sağlayın.

   ```powershell
   Connect-AzAccount
   ```

1. Oturum açma penceresini gördüğünüzde, kimlik bilgilerinizi girin. Azure portala giriş yapmak için aynı e-posta adresini ve parolayı kullanın.

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

1. Benzersiz kaynak grubu adına sahip bir değişken tanımlayın. Hızlı başlangıç adımlarını basitleştirmek için, kalan komutlar bu adı diğer kaynak adları için temel olarak kullanır.

   ```powershell
   $ResourceGroupName = "sqlvm1"
   ```

1. Tüm VM kaynakları için hedef Azure bölgesinin konumunu tanımlayın.

   ```powershell
   $Location = "East US"
   ```

1. Kaynak grubunu oluşturun.

   ```powershell
   New-AzResourceGroup -Name $ResourceGroupName -Location $Location
   ```

## <a name="configure-network-settings"></a>Ağ ayarlarını yapılandırma

1. Bir sanal ağ, alt ağ ve genel IP adresi oluşturun. Bu kaynaklar, sanal makineye ağ bağlantısı sağlamak ve sanal makineyi İnternet'e bağlamak için kullanılır.

   ``` PowerShell
   $SubnetName = $ResourceGroupName + "subnet"
   $VnetName = $ResourceGroupName + "vnet"
   $PipName = $ResourceGroupName + $(Get-Random)

   # Create a subnet configuration
   $SubnetConfig = New-AzVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix 192.168.1.0/24

   # Create a virtual network
   $Vnet = New-AzVirtualNetwork -ResourceGroupName $ResourceGroupName -Location $Location `
      -Name $VnetName -AddressPrefix 192.168.0.0/16 -Subnet $SubnetConfig

   # Create a public IP address and specify a DNS name
   $Pip = New-AzPublicIpAddress -ResourceGroupName $ResourceGroupName -Location $Location `
      -AllocationMethod Static -IdleTimeoutInMinutes 4 -Name $PipName
   ```

1. Ağ güvenlik grubu oluşturma. Uzak masaüstü (RDP) ve SQL Server bağlantılarına izin vermek için kuralları yapılandırın.

   ```powershell
   # Rule to allow remote desktop (RDP)
   $NsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name "RDPRule" -Protocol Tcp `
      -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 3389 -Access Allow

   #Rule to allow SQL Server connections on port 1433
   $NsgRuleSQL = New-AzNetworkSecurityRuleConfig -Name "MSSQLRule"  -Protocol Tcp `
      -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 1433 -Access Allow

   # Create the network security group
   $NsgName = $ResourceGroupName + "nsg"
   $Nsg = New-AzNetworkSecurityGroup -ResourceGroupName $ResourceGroupName `
      -Location $Location -Name $NsgName `
      -SecurityRules $NsgRuleRDP,$NsgRuleSQL
   ```

1. Ağ arabirimini oluşturun.

   ```powershell
   $InterfaceName = $ResourceGroupName + "int"
   $Interface = New-AzNetworkInterface -Name $InterfaceName `
      -ResourceGroupName $ResourceGroupName -Location $Location `
      -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $Pip.Id `
      -NetworkSecurityGroupId $Nsg.Id
   ```

## <a name="create-the-sql-vm"></a>SQL VM'sini oluşturma

1. VM 'de oturum açmak için kimlik bilgilerinizi tanımlayın. Kullanıcı adı "azureadmin" dır. Komutunu çalıştırmadan önce değişiklik yaptığınızdan emin olun \<password> .

   ``` PowerShell
   # Define a credential object
   $SecurePassword = ConvertTo-SecureString '<password>' `
      -AsPlainText -Force
   $Cred = New-Object System.Management.Automation.PSCredential ("azureadmin", $securePassword)
   ```

1. Sanal makine yapılandırma nesnesi ve ardından VM oluşturun. Aşağıdaki komut, Windows Server 2016 üzerinde bir SQL Server 2017 Developer Edition VM oluşturur.

   ```powershell
   # Create a virtual machine configuration
   $VMName = $ResourceGroupName + "VM"
   $VMConfig = New-AzVMConfig -VMName $VMName -VMSize Standard_DS13_V2 |
      Set-AzVMOperatingSystem -Windows -ComputerName $VMName -Credential $Cred -ProvisionVMAgent -EnableAutoUpdate |
      Set-AzVMSourceImage -PublisherName "MicrosoftSQLServer" -Offer "SQL2017-WS2016" -Skus "SQLDEV" -Version "latest" |
      Add-AzVMNetworkInterface -Id $Interface.Id
   
   # Create the VM
   New-AzVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VMConfig
   ```

   > [!TIP]
   > VM'nin oluşturulması birkaç dakika sürer.

## <a name="register-with-sql-vm-rp"></a>SQL VM RP ile kaydolma 

Portal tümleştirmesi ve SQL VM özelliklerini almak için [SQL IaaS Aracısı uzantısına](sql-agent-extension-manually-register-single-vm.md)kaydolmanız gerekir.

Tam işlevselliği almak için, uzantıya tam modda kaydolmanız gerekir. Ancak bunu yapmak SQL Server hizmetini yeniden başlatır, bu nedenle önerilen yaklaşım basit modda kaydolmalıdır ve bakım penceresi sırasında tam olarak yükseltilir. 

İlk olarak, SQL Server VM basit modda kaydedin: 

```powershell-interactive
# Get the existing compute VM
$vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
# Register SQL VM with 'Lightweight' SQL IaaS agent
New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
  -LicenseType PAYG -SqlManagementType LightWeight
```

Ardından, bir bakım penceresi sırasında tam moda yükseltin: 

```powershell-interactive
# Get the existing Compute VM
$vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
      
# Register with SQL IaaS Agent extension in full mode
Update-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full
```



## <a name="remote-desktop-into-the-vm"></a>VM'ye uzak masaüstü bağlantısı kurma

1. Yeni VM 'nin genel IP adresini almak için aşağıdaki komutu kullanın.

   ```powershell
   Get-AzPublicIpAddress -ResourceGroupName $ResourceGroupName | Select IpAddress
   ```

1. Yeni VM 'ye Uzak Masaüstü oturumu başlatmak için döndürülen IP adresini **mstsc** 'ye bir komut satırı parametresi olarak geçirin.

   ```
   mstsc /v:<publicIpAddress>
   ```

1. Kimlik bilgileri istendiğinde farklı bir hesabın kimlik bilgilerini girme seçeneğini belirleyin. Önceki ters eğik çizgiyle (örneğin,) Kullanıcı adını `\azureadmin` ve daha önce bu hızlı başlangıçta ayarladığınız parolayı girin.

## <a name="connect-to-sql-server"></a>SQL Server’a bağlanma

1. Uzak Masaüstü oturumunda oturum açtıktan sonra Başlat menüsünden **SQL Server Management Studio 2017** ' u başlatın.

1. **Sunucuya Bağlan** iletişim kutusunda, Varsayılanları koruyun. Sunucu adı, VM'nin adıdır. Kimlik doğrulaması, **Windows Kimlik Doğrulaması** olarak ayarlanmıştır. **Bağlan**’ı seçin.

Artık SQL Server yerel olarak bağlı olursunuz. Uzaktan bağlanmak istiyorsanız Azure portal bağlantıyı veya el ile [yapılandırmanız](ways-to-connect-to-sql.md) gerekir.

## <a name="clean-up-resources"></a>Kaynakları temizleme

VM 'nin sürekli olarak çalışmasına gerek yoksa, kullanımda olmadığında durdurarak gereksiz ücretlerden kaçınabilirsiniz. Aşağıdaki komut VM'yi durdurur ancak özelliği daha sonra kullanılmak üzere muhafaza eder.

```powershell
Stop-AzVM -Name $VMName -ResourceGroupName $ResourceGroupName
```

Ayrıca, **Remove-AzResourceGroup** komutuyla sanal makineyle ilişkili tüm kaynakları kalıcı olarak silebilirsiniz. Bunun yapılması sanal makineyi de kalıcı olarak siler, bu nedenle bu komutu dikkatli kullanın.

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, Azure PowerShell kullanarak bir SQL Server 2017 sanal makinesi oluşturdunuz. Verilerinizi yeni SQL Server’a geçirme hakkında daha fazla bilgi edinmek için, aşağıdaki makaleye bakın.

> [!div class="nextstepaction"]
> [Veritabanını SQL VM'ye geçirme](migrate-to-vm-from-sql-server.md)
