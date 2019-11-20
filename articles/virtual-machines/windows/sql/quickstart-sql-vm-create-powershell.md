---
title: Azure PowerShell ile SQL Server Windows VM Oluşturma | Microsoft Docs
description: Bu öğreticide Azure PowerShell ile Windows SQL Server 2017 sanal makinesi oluşturma adımları gösterilmektedir.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.topic: quickstart
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 12/21/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 69781b7c5187bd9166946a96a8b47233d0f77208
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70100685"
---
# <a name="quickstart-create-a-sql-server-windows-virtual-machine-with-azure-powershell"></a>Hızlı Başlangıç: Azure PowerShell ile SQL Server Windows sanal makinesi oluşturma

Bu hızlı başlangıç, Azure PowerShell ile SQL Server sanal makinesi oluşturma adımlarında yol gösterir.

> [!TIP]
> - Bu hızlı başlangıç, hızlı bir şekilde bir SQL VM sağlama ve VM’ye bağlanma yolu sağlar. SQL VM’leri oluşturmaya yönelik diğer Azure PowerShell seçenekleri hakkında daha fazla bilgi için bkz. [Azure PowerShell ile SQL Server VM'leri için sağlama kılavuzu](virtual-machines-windows-ps-sql-create.md).
> - SQL Server sanal makineleri hakkında sorularınız olursa [Sık Sorulan Sorular](virtual-machines-windows-sql-server-iaas-faq.md) bölümüne bakın.

## <a id="subscription"></a> Azure aboneliği edinme

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.


## <a id="powershell"></a> Azure PowerShell edinme

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

## <a name="configure-powershell"></a>PowerShell yapılandırma

1. PowerShell 'i açın ve **Connect-AzAccount** komutunu çalıştırarak Azure hesabınıza erişim sağlayın.

   ```powershell
   Connect-AzAccount
   ```

1. Kimlik bilgilerinizi girmek için bir ekran görmeniz gerekir. Azure portala giriş yapmak için aynı e-posta adresini ve parolayı kullanın.

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

1. Ağ güvenlik grubu oluşturun. Uzak masaüstü (RDP) ve SQL Server bağlantılarına izin vermek için kuralları yapılandırın.

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

1. VM 'de oturum açmak için kimlik bilgilerinizi tanımlayın. Kullanıcı adı "azureadmin" dır. Komutu çalıştırmadan önce \<parolayı > değiştirdiğinizden emin olun.

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

## <a name="install-the-sql-iaas-agent"></a>SQL IaaS Aracısı 'nı yükler

Portal tümleştirmesi ve SQL VM özelliklerini kullanabilmek için [SQL Server IaaS Aracısı Uzantısı](virtual-machines-windows-sql-server-agent-extension.md)'nı yükleyin. Aracıyı yeni VM 'ye yüklemek için VM oluşturulduktan sonra aşağıdaki komutu çalıştırın.

   ```powershell
   Set-AzVMSqlServerExtension -ResourceGroupName $ResourceGroupName -VMName $VMName -name "SQLIaasExtension" -version "1.2" -Location $Location
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

1. Kimlik bilgileri istendiğinde farklı bir hesabın kimlik bilgilerini girme seçeneğini belirleyin. Önceki ters eğik çizgiyle (örneğin, `\azureadmin`) Kullanıcı adını ve daha önce bu hızlı başlangıçta ayarladığınız parolayı girin.

## <a name="connect-to-sql-server"></a>SQL Server'a bağlanma

1. Uzak Masaüstü oturumunda oturum açtıktan sonra Başlat menüsünden **SQL Server Management Studio 2017** ' u başlatın.

1. **Sunucuya Bağlan** iletişim kutusunda, Varsayılanları koruyun. Sunucu adı, VM'nin adıdır. Kimlik doğrulaması, **Windows Kimlik Doğrulaması** olarak ayarlanmıştır. **Bağlan**’ı seçin.

Artık SQL Server yerel olarak bağlı olursunuz. Uzaktan bağlanmak istiyorsanız, portaldan veya el ile [bağlantı yapılandırmanız](virtual-machines-windows-sql-connect.md) gerekir.

## <a name="clean-up-resources"></a>Kaynakları temizleme

VM 'nin sürekli olarak çalışmasına gerek yoksa, kullanımda olmadığında durdurarak gereksiz ücretlerden kaçınabilirsiniz. Aşağıdaki komut VM'yi durdurur ancak özelliği daha sonra kullanılmak üzere muhafaza eder.

```powershell
Stop-AzVM -Name $VMName -ResourceGroupName $ResourceGroupName
```

Ayrıca, **Remove-AzResourceGroup** komutuyla sanal makineyle ilişkili tüm kaynakları kalıcı olarak silebilirsiniz. Bunun yapılması sanal makineyi de kalıcı olarak siler, bu nedenle bu komutu dikkatli kullanın.

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, Azure PowerShell kullanarak bir SQL Server 2017 sanal makinesi oluşturdunuz. Verilerinizi yeni SQL Server'a geçirme hakkında daha fazla bilgi edinmek için, aşağıdaki makaleye bakın.

> [!div class="nextstepaction"]
> [Veritabanını SQL VM'ye geçirme](virtual-machines-windows-migrate-sql.md)
