---
title: Azure VM 'de SQL Server sağlamak için Azure PowerShell kullanma kılavuzu
description: SQL Server sanal makine galeri görüntüleri ile Azure VM oluşturmak için adımlar ve PowerShell komutları sağlar.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-resource-manager
ms.assetid: 98d50dd8-48ad-444f-9031-5378d8270d7b
ms.service: virtual-machines-sql
ms.subservice: deployment
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/21/2018
ms.author: mathoma
ms.reviewer: jroth
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: a3f51a07b274320d1cd9f12b33703d8ec7f21f49
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97359668"
---
# <a name="how-to-use-azure-powershell-to-provision-sql-server-on-azure-virtual-machines"></a>Azure sanal makinelerinde SQL Server sağlamak için Azure PowerShell kullanma

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Bu kılavuzda, Azure sanal makinelerinde (VM) SQL Server sağlamak için PowerShell kullanma seçenekleri ele alınmaktadır. Varsayılan değerleri temel alan kolaylaştırılmış Azure PowerShell bir örnek için bkz. [SQL VM Azure PowerShell hızlı başlangıç](sql-vm-create-powershell-quickstart.md).

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

## <a name="configure-your-subscription"></a>Aboneliğinizi yapılandırma

1. PowerShell 'i açın ve **Connect-AzAccount** komutunu çalıştırarak Azure hesabınıza erişim sağlayın.

   ```powershell
   Connect-AzAccount
   ```

1. İstendiğinde kimlik bilgilerinizi girin. Azure portala giriş yapmak için aynı e-posta adresini ve parolayı kullanın.

## <a name="define-image-variables"></a>Görüntü değişkenlerini tanımlama

Değerleri yeniden kullanmak ve betik oluşturmayı basitleştirmek için, bir dizi değişken tanımlayarak başlayın. Parametre değerlerini istediğiniz gibi değiştirin, ancak girilen değerleri değiştirirken ad uzunlukları ve özel karakterlerle ilgili adlandırma kısıtlamalarının farkında olun.

### <a name="location-and-resource-group"></a>Konum ve kaynak grubu

Diğer VM kaynaklarını oluşturmak istediğiniz veri bölgesini ve kaynak grubunu tanımlayın.

İstediğiniz gibi değiştirin ve bu değişkenleri başlatmak için bu cmdlet 'leri çalıştırın.

```powershell
$Location = "SouthCentralUS"
$ResourceGroupName = "sqlvm2"
```

### <a name="storage-properties"></a>Depolama özellikleri

Sanal makine tarafından kullanılacak depolama hesabını ve depolama türünü tanımlayın.

İstediğiniz gibi değiştirin ve ardından bu değişkenleri başlatmak için aşağıdaki cmdlet 'i çalıştırın. Üretim iş yükleri için [Premium SSD 'ler](../../../virtual-machines/disks-types.md#premium-ssd) kullanmanızı öneririz.

```powershell
$StorageName = $ResourceGroupName + "storage"
$StorageSku = "Premium_LRS"
```

### <a name="network-properties"></a>Ağ özellikleri

Ağ tarafından sanal makinede kullanılacak özellikleri tanımlayın. 

- Ağ arabirimi
- TCP/IP ayırma yöntemi
- Sanal ağın adı
- Sanal alt ağ adı
- Sanal ağ için IP adresi aralığı
- Alt ağ için IP adresi aralığı
- Ortak etki alanı adı etiketi

İstediğiniz gibi değiştirin ve ardından bu değişkenleri başlatmak için bu cmdlet 'i çalıştırın.

```powershell
$InterfaceName = $ResourceGroupName + "ServerInterface"
$NsgName = $ResourceGroupName + "nsg"
$TCPIPAllocationMethod = "Dynamic"
$VNetName = $ResourceGroupName + "VNet"
$SubnetName = "Default"
$VNetAddressPrefix = "10.0.0.0/16"
$VNetSubnetAddressPrefix = "10.0.0.0/24"
$DomainName = $ResourceGroupName
```

### <a name="virtual-machine-properties"></a>Sanal makine özellikleri

Aşağıdaki özellikleri tanımlayın:

- Sanal makine adı
- Bilgisayar adı
- Sanal makine boyutu
- Sanal makine için işletim sistemi diski adı

İstediğiniz gibi değiştirin ve ardından bu değişkenleri başlatmak için bu cmdlet 'i çalıştırın.

```powershell
$VMName = $ResourceGroupName + "VM"
$ComputerName = $ResourceGroupName + "Server"
$VMSize = "Standard_DS13"
$OSDiskName = $VMName + "OSDisk"
```

### <a name="choose-a-sql-server-image"></a>SQL Server görüntüsü seçin

Sanal makine için kullanılacak SQL Server görüntüsünü tanımlamak için aşağıdaki değişkenleri kullanın. 

1. İlk olarak, komutu ile SQL Server görüntü tekliflerinin tümünü listeleyin `Get-AzVMImageOffer` . Bu komut, Azure portal kullanılabilen geçerli görüntüleri ve yalnızca PowerShell ile yüklenebilen daha eski görüntüleri listeler:

   ```powershell
   Get-AzVMImageOffer -Location $Location -Publisher 'MicrosoftSQLServer'
   ```

1. Bu öğreticide, Windows Server 2016 üzerinde SQL Server 2017 belirtmek için aşağıdaki değişkenleri kullanın.

   ```powershell
   $OfferName = "SQL2017-WS2016"
   $PublisherName = "MicrosoftSQLServer"
   $Version = "latest"
   ```

1. Daha sonra teklifiniz için kullanılabilir sürümleri listeleyin.

   ```powershell
   Get-AzVMImageSku -Location $Location -Publisher 'MicrosoftSQLServer' -Offer $OfferName | Select Skus
   ```

1. Bu öğretici için SQL Server 2017 Developer Edition 'ı (**SQLDev**) kullanın. Geliştirici sürümü, test ve geliştirme için serbestçe lisanslanır ve yalnızca VM 'yi çalıştırmanın maliyeti için ödeme yaparsınız.

   ```powershell
   $Sku = "SQLDEV"
   ```

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

Kaynak Yöneticisi dağıtım modeliyle, oluşturduğunuz ilk nesne kaynak grubudur. [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) cmdlet 'ini kullanarak bir Azure Kaynak grubu ve kaynakları oluşturun. Kaynak grubu adı ve konumu için daha önce oluşturduğunuz değişkenleri belirtin.

Yeni kaynak grubunuzu oluşturmak için bu cmdlet 'i çalıştırın.

```powershell
New-AzResourceGroup -Name $ResourceGroupName -Location $Location
```

## <a name="create-a-storage-account"></a>Depolama hesabı oluşturma

Sanal makine, işletim sistemi diski ve SQL Server veri ve günlük dosyaları için depolama kaynakları gerektirir. Kolaylık olması için her ikisi için tek bir disk oluşturacaksınız. SQL Server verilerinizi ve günlük dosyalarını ayrılmış disklere yerleştirmek için [Add-Azure disk](/powershell/module/servicemanagement/azure.service/add-azuredisk) cmdlet 'ini kullanarak daha sonra ek diskler ekleyebilirsiniz. Yeni kaynak grubunuzda standart bir depolama hesabı oluşturmak için [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) cmdlet 'ini kullanın. Depolama hesabı adı, depolama SKU adı ve konum için daha önce oluşturduğunuz değişkenleri belirtin.

Yeni depolama hesabınızı oluşturmak için bu cmdlet 'i çalıştırın.

```powershell
$StorageAccount = New-AzStorageAccount -ResourceGroupName $ResourceGroupName `
   -Name $StorageName -SkuName $StorageSku `
   -Kind "Storage" -Location $Location
```

> [!TIP]
> Depolama hesabı oluşturmak birkaç dakika sürebilir.

## <a name="create-network-resources"></a>Ağ kaynakları oluşturma

Sanal makine, ağ bağlantısı için birkaç ağ kaynağı gerektirir.

* Her sanal makine için bir sanal ağ gerekir.
* Bir sanal ağda en az bir alt ağ tanımlanmış olmalıdır.
* Bir ağ arabirimi, genel veya özel bir IP adresiyle tanımlanmalıdır.

### <a name="create-a-virtual-network-subnet-configuration"></a>Sanal ağ alt ağı yapılandırması oluşturma

Sanal ağınız için bir alt ağ yapılandırması oluşturarak başlayın. Bu öğretici için, [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) cmdlet 'ini kullanarak bir varsayılan alt ağ oluşturun. Alt ağ adı ve adres ön eki için daha önce oluşturduğunuz değişkenleri belirtin.

> [!NOTE]
> Bu cmdlet 'i kullanarak sanal ağ alt ağı yapılandırmasının ek özelliklerini tanımlayabilirsiniz, ancak Bu öğreticinin kapsamı dışındadır.

Sanal alt ağ yapılandırmanızı oluşturmak için bu cmdlet 'i çalıştırın.

```powershell
$SubnetConfig = New-AzVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $VNetSubnetAddressPrefix
```

### <a name="create-a-virtual-network"></a>Sanal ağ oluşturma

Sonra, [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) cmdlet 'ini kullanarak yeni kaynak grubunuzda Sanal ağınızı oluşturun. Ad, konum ve adres ön eki için daha önce oluşturduğunuz değişkenleri belirtin. Önceki adımda tanımladığınız alt ağ yapılandırmasını kullanın.

Sanal ağınızı oluşturmak için bu cmdlet 'i çalıştırın.

```powershell
$VNet = New-AzVirtualNetwork -Name $VNetName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -AddressPrefix $VNetAddressPrefix -Subnet $SubnetConfig
```

### <a name="create-the-public-ip-address"></a>Genel IP adresini oluşturma

Sanal ağınız tanımlandığına göre, sanal makine bağlantısı için bir IP adresi yapılandırmanız gerekir. Bu öğreticide, Internet bağlantısını desteklemek için dinamik IP adresleme 'yi kullanarak genel bir IP adresi oluşturun. Yeni kaynak grubunuzda genel IP adresini oluşturmak için [New-Azpublicıpaddress](/powershell/module/az.network/new-azpublicipaddress) cmdlet 'ini kullanın. Ad, konum, ayırma yöntemi ve DNS etki alanı adı etiketi için daha önce oluşturduğunuz değişkenleri belirtin.

> [!NOTE]
> Bu cmdlet 'i kullanarak genel IP adresinin ek özelliklerini tanımlayabilirsiniz, ancak bu ilk öğreticinin kapsamı dışındadır. Ayrıca, Bu öğreticinin kapsamı dışında bir özel adres veya bir adresi de oluşturabilirsiniz.

Genel IP adresinizi oluşturmak için bu cmdlet 'i çalıştırın.

```powershell
$PublicIp = New-AzPublicIpAddress -Name $InterfaceName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -AllocationMethod $TCPIPAllocationMethod -DomainNameLabel $DomainName
```

### <a name="create-the-network-security-group"></a>Ağ güvenlik grubunu oluşturma

VM 'yi ve SQL Server trafiği güvenli hale getirmek için bir ağ güvenlik grubu oluşturun.

1. İlk olarak, RDP bağlantılarına izin vermek için Uzak Masaüstü (RDP) için bir ağ güvenlik grubu kuralı oluşturun.

   ```powershell
   $NsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name "RDPRule" -Protocol Tcp `
      -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 3389 -Access Allow
   ```
1. TCP bağlantı noktası 1433 ' de trafiğe izin veren bir ağ güvenlik grubu kuralı yapılandırın. Bunun yapılması Internet üzerinden SQL Server bağlantılar sağlar.

   ```powershell
   $NsgRuleSQL = New-AzNetworkSecurityRuleConfig -Name "MSSQLRule"  -Protocol Tcp `
      -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 1433 -Access Allow
   ```

1. Ağ güvenlik grubunu oluşturun.

   ```powershell
   $Nsg = New-AzNetworkSecurityGroup -ResourceGroupName $ResourceGroupName `
      -Location $Location -Name $NsgName `
      -SecurityRules $NsgRuleRDP,$NsgRuleSQL
   ```

### <a name="create-the-network-interface"></a>Ağ arabirimini oluşturma

Artık sanal makineniz için ağ arabirimini oluşturmaya hazırsınız. Yeni kaynak grubunuzda ağ arabirimini oluşturmak için [New-Aznetworkınterface](/powershell/module/az.network/new-aznetworkinterface) cmdlet 'ini kullanın. Daha önce tanımlanan adı, konumu, alt ağı ve genel IP adresini belirtin.

Ağ arabiriminizi oluşturmak için bu cmdlet 'i çalıştırın.

```powershell
$Interface = New-AzNetworkInterface -Name $InterfaceName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PublicIp.Id `
   -NetworkSecurityGroupId $Nsg.Id
```

## <a name="configure-a-vm-object"></a>VM nesnesi yapılandırma

Depolama ve ağ kaynakları tanımlandığına göre, sanal makine için işlem kaynaklarını tanımlamaya hazır olursunuz.

- Sanal makine boyutunu ve çeşitli işletim sistemi özelliklerini belirtin.
- Daha önce oluşturduğunuz ağ arabirimini belirtin.
- Blob depolamayı tanımlayın.
- İşletim sistemi diskini belirtin.

### <a name="create-the-vm-object"></a>VM nesnesi oluşturma

Sanal makine boyutunu belirterek başlayın. Bu öğretici için bir DS13 belirtin. Yapılandırılabilir bir sanal makine nesnesi oluşturmak için [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig) cmdlet 'ini kullanın. Ad ve boyut için daha önce oluşturduğunuz değişkenleri belirtin.

Sanal makine nesnesini oluşturmak için bu cmdlet 'i çalıştırın.

```powershell
$VirtualMachine = New-AzVMConfig -VMName $VMName -VMSize $VMSize
```

### <a name="create-a-credential-object-to-hold-the-name-and-password-for-the-local-administrator-credentials"></a>Yerel yönetici kimlik bilgileri için adı ve parolayı tutacak bir kimlik bilgisi nesnesi oluşturun

Sanal makine için işletim sistemi özelliklerini ayarlamadan önce, yerel yönetici hesabının kimlik bilgilerini güvenli bir dize olarak sağlamanız gerekir. Bunu gerçekleştirmek için [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential) cmdlet 'ini kullanın.

Aşağıdaki cmdlet 'i çalıştırın. PowerShell kimlik bilgileri istek penceresine VM 'nin yerel yönetici adını ve parolasını yazmanız gerekir.

```powershell
$Credential = Get-Credential -Message "Type the name and password of the local administrator account."
```

### <a name="set-the-operating-system-properties-for-the-virtual-machine"></a>Sanal makine için işletim sistemi özelliklerini ayarla

Artık, sanal makinenin işletim sistemi özelliklerini [set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem) cmdlet 'i ile ayarlamaya hazırsınız.

- İşletim sisteminin türünü Windows olarak ayarlayın.
- [Sanal makine aracısının](../../../virtual-machines/extensions/agent-windows.md) yüklenmesini gerektir.
- Cmdlet 'in otomatik güncelleştirmeyi etkinleştirmesine izin vermez.
- Sanal makine adı, bilgisayar adı ve kimlik bilgisi için daha önce oluşturduğunuz değişkenleri belirtin.

Sanal makinenizin işletim sistemi özelliklerini ayarlamak için bu cmdlet 'i çalıştırın.

```powershell
$VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine `
   -Windows -ComputerName $ComputerName -Credential $Credential `
   -ProvisionVMAgent -EnableAutoUpdate
```

### <a name="add-the-network-interface-to-the-virtual-machine"></a>Ağ arabirimini sanal makineye Ekle

Ardından, daha önce tanımladığınız değişkeni kullanarak ağ arabirimini eklemek için [Add-Azvmnetworkınterface](/powershell/module/az.compute/add-azvmnetworkinterface) cmdlet 'ini kullanın.

Sanal makinenizin ağ arabirimini ayarlamak için bu cmdlet 'i çalıştırın.

```powershell
$VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $Interface.Id
```

### <a name="set-the-blob-storage-location-for-the-disk-to-be-used-by-the-virtual-machine"></a>Sanal makine tarafından kullanılacak diskin BLOB depolama konumunu ayarla

Daha sonra, VM 'nin diskinin BLOB depolama konumunu daha önce tanımladığınız değişkenlerle ayarlayın.

BLOB depolama konumunu ayarlamak için bu cmdlet 'i çalıştırın.

```powershell
$OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName + ".vhd"
```

### <a name="set-the-operating-system-disk-properties-for-the-virtual-machine"></a>Sanal makine için işletim sistemi diski özelliklerini ayarla

Sonra, [set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk) cmdlet 'ini kullanarak sanal makine için işletim sistemi disk özelliklerini ayarlayın. 

- Sanal makinenin işletim sisteminin bir görüntüden gelmesini belirtin.
- Önbelleğe almayı salt okunurdur (SQL Server aynı diske yüklenmekte olduğundan) ayarlayın.
- VM adı ve işletim sistemi diski için daha önce oluşturduğunuz değişkenleri belirtin.

Sanal makineniz için işletim sistemi diski özelliklerini ayarlamak üzere bu cmdlet 'i çalıştırın.

```powershell
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name `
   $OSDiskName -VhdUri $OSDiskUri -Caching ReadOnly -CreateOption FromImage
```

### <a name="specify-the-platform-image-for-the-virtual-machine"></a>Sanal makine için platform görüntüsünü belirtin

Son yapılandırma adımı, sanal makineniz için platform görüntüsünü belirtmektir. Bu öğretici için en son SQL Server 2016 CTP görüntüsünü kullanın. Bu görüntüyü daha önce tanımladığınız değişkenlerle birlikte kullanmak için [set-Azvmsourceımage](/powershell/module/az.compute/set-azvmsourceimage) cmdlet 'ini kullanın.

Sanal makinenizin platform görüntüsünü belirtmek için bu cmdlet 'i çalıştırın.

```powershell
$VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine `
   -PublisherName $PublisherName -Offer $OfferName `
   -Skus $Sku -Version $Version
```

## <a name="create-the-sql-vm"></a>SQL VM'sini oluşturma

Yapılandırma adımlarını tamamladığınıza göre, sanal makineyi oluşturmaya hazırsınız demektir. Tanımladığınız değişkenleri kullanarak sanal makineyi oluşturmak için [New-AzVM](/powershell/module/az.compute/new-azvm) cmdlet 'ini kullanın.

> [!TIP]
> Sanal makine oluşturulması birkaç dakika sürebilir.

Sanal makinenizi oluşturmak için bu cmdlet 'i çalıştırın.

```powershell
New-AzVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VirtualMachine
```

Sanal makine oluşturulur.

> [!NOTE]
> Önyükleme tanılamaları hakkında bir hata alırsanız, bunu yoksayabilirsiniz. Sanal makinenin diski için belirtilen depolama hesabı bir Premium depolama hesabı olduğundan, önyükleme tanılaması için standart bir depolama hesabı oluşturulur.

## <a name="install-the-sql-iaas-agent"></a>SQL Iaas Aracısı'nı yükleme

SQL Server sanal makineler, [SQL Server IaaS Aracısı uzantısı](sql-server-iaas-agent-extension-automate-management.md)ile otomatikleştirilmiş yönetim özelliklerini destekler. SQL Server uzantıya kaydetmek için, sanal makine oluşturulduktan sonra [New-AzSqlVM](/powershell/module/az.sqlvirtualmachine/new-azsqlvm) komutunu çalıştırın. SQL Server VM için lisans türünü belirtin, [Azure hibrit avantajı](https://azure.microsoft.com/pricing/hybrid-benefit/)aracılığıyla Kullandıkça Öde veya kendi lisansını getir seçeneklerinden birini belirleyin. Lisanslama hakkında daha fazla bilgi için bkz. [lisans modeli](licensing-model-azure-hybrid-benefit-ahb-change.md). 


   ```powershell
   New-AzSqlVM -ResourceGroupName $ResourceGroupName -Name $VMName -Location $Location -LicenseType <PAYG/AHUB> 
   ```

Uzantıya kaydolmak için üç yol vardır: 
- [Bir abonelikteki tüm geçerli ve gelecekteki VM 'Ler için otomatik olarak](sql-agent-extension-automatic-registration-all-vms.md)
- [Tek bir VM için el ile](sql-agent-extension-manually-register-single-vm.md)
- [Toplu olarak birden çok VM için el ile](sql-agent-extension-manually-register-vms-bulk.md)


## <a name="stop-or-remove-a-vm"></a>VM 'yi durdurma veya kaldırma

VM 'nin sürekli olarak çalışmasına gerek yoksa, kullanımda olmadığında durdurarak gereksiz ücretlerden kaçınabilirsiniz. Aşağıdaki komut VM'yi durdurur ancak özelliği daha sonra kullanılmak üzere muhafaza eder.

```powershell
Stop-AzVM -Name $VMName -ResourceGroupName $ResourceGroupName
```

Ayrıca, **Remove-AzResourceGroup** komutuyla sanal makineyle ilişkili tüm kaynakları kalıcı olarak silebilirsiniz. Bunun yapılması sanal makineyi de kalıcı olarak siler, bu nedenle bu komutu dikkatli kullanın.

## <a name="example-script"></a>Örnek betik

Aşağıdaki betik, Bu öğreticinin tüm PowerShell betiğini içerir. **Connect-AzAccount** ve **Select-azsubscription** komutlarıyla birlikte kullanmak üzere Azure aboneliğini ayarlamış olduğunuzu varsayar.

```powershell
# Variables

## Global
$Location = "SouthCentralUS"
$ResourceGroupName = "sqlvm2"

## Storage
$StorageName = $ResourceGroupName + "storage"
$StorageSku = "Premium_LRS"

## Network
$InterfaceName = $ResourceGroupName + "ServerInterface"
$NsgName = $ResourceGroupName + "nsg"
$VNetName = $ResourceGroupName + "VNet"
$SubnetName = "Default"
$VNetAddressPrefix = "10.0.0.0/16"
$VNetSubnetAddressPrefix = "10.0.0.0/24"
$TCPIPAllocationMethod = "Dynamic"
$DomainName = $ResourceGroupName

##Compute
$VMName = $ResourceGroupName + "VM"
$ComputerName = $ResourceGroupName + "Server"
$VMSize = "Standard_DS13"
$OSDiskName = $VMName + "OSDisk"

##Image
$PublisherName = "MicrosoftSQLServer"
$OfferName = "SQL2017-WS2016"
$Sku = "SQLDEV"
$Version = "latest"

# Resource Group
New-AzResourceGroup -Name $ResourceGroupName -Location $Location

# Storage
$StorageAccount = New-AzStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageName -SkuName $StorageSku -Kind "Storage" -Location $Location

# Network
$SubnetConfig = New-AzVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $VNetSubnetAddressPrefix
$VNet = New-AzVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName -Location $Location -AddressPrefix $VNetAddressPrefix -Subnet $SubnetConfig
$PublicIp = New-AzPublicIpAddress -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -AllocationMethod $TCPIPAllocationMethod -DomainNameLabel $DomainName
$NsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name "RDPRule" -Protocol Tcp -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 3389 -Access Allow
$NsgRuleSQL = New-AzNetworkSecurityRuleConfig -Name "MSSQLRule"  -Protocol Tcp -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 1433 -Access Allow
$Nsg = New-AzNetworkSecurityGroup -ResourceGroupName $ResourceGroupName -Location $Location -Name $NsgName -SecurityRules $NsgRuleRDP,$NsgRuleSQL
$Interface = New-AzNetworkInterface -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PublicIp.Id -NetworkSecurityGroupId $Nsg.Id

# Compute
$VirtualMachine = New-AzVMConfig -VMName $VMName -VMSize $VMSize
$Credential = Get-Credential -Message "Type the name and password of the local administrator account."
$VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate #-TimeZone = $TimeZone
$VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $Interface.Id
$OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName + ".vhd"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name $OSDiskName -VhdUri $OSDiskUri -Caching ReadOnly -CreateOption FromImage

# Image
$VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine -PublisherName $PublisherName -Offer $OfferName -Skus $Sku -Version $Version

# Create the VM in Azure
New-AzVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VirtualMachine

# Add the SQL IaaS Extension, and choose the license type
New-AzSqlVM -ResourceGroupName $ResourceGroupName -Name $VMName -Location $Location -LicenseType <PAYG/AHUB> 
```

## <a name="next-steps"></a>Sonraki adımlar

Sanal makine oluşturulduktan sonra şunları yapabilirsiniz:

- RDP kullanarak sanal makineye bağlanma
- VM 'niz için portalda SQL Server ayarlarını yapılandırın, örneğin:
   - [Depolama ayarları](storage-configuration.md) 
   - [Otomatikleştirilmiş yönetim görevleri](sql-server-iaas-agent-extension-automate-management.md)
- [Bağlantı yapılandırma](ways-to-connect-to-sql.md)
- İstemcileri ve uygulamaları yeni SQL Server örneğine bağlama