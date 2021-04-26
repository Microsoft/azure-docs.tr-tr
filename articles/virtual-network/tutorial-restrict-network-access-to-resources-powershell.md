---
title: PaaS kaynaklarına ağ erişimini kısıtlama-Azure PowerShell
description: Bu makalede, Azure depolama ve Azure SQL veritabanı gibi Azure kaynaklarına ağ erişimini, Azure PowerShell kullanan sanal ağ hizmeti uç noktaları ile sınırlama ve kısıtlama hakkında bilgi edineceksiniz.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/14/2018
ms.author: kumud
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: f7930bce55ebcc2714bdf43a88ac6cb77069cf4f
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106057227"
---
# <a name="restrict-network-access-to-paas-resources-with-virtual-network-service-endpoints-using-powershell"></a>PowerShell kullanarak sanal ağ hizmet uç noktaları ile PaaS kaynaklarına ağ erişimini kısıtlama

Sanal ağ hizmet uç noktaları bazı Azure hizmet uç noktalarına ağ erişimini bir sanal ağ alt ağı ile sınırlamanıza olanak tanır. Ayrıca, kaynaklara internet erişimini de kaldırabilirsiniz. Hizmet uç noktaları, sanal ağınızdan desteklenen Azure hizmetlerine doğrudan bağlantı sağlar, böylece Azure hizmetlerine erişmek için sanal ağınızın özel adres alanını kullanabilirsiniz. Hizmet uç noktaları aracılığıyla Azure kaynaklarına gönderilen trafik her zaman Microsoft Azure omurga ağı üzerinde kalır. Bu makalede şunları öğreneceksiniz:

* Alt ağ ile sanal ağ oluşturma
* Alt ağ ekleme ve hizmet uç noktasını etkinleştirme
* Azure kaynağı oluşturma ve yalnızca bir alt ağdan ağ erişimine izin verme
* Her alt ağa bir sanal makine (VM) dağıtma
* Bir alt ağdan kaynağa erişimi onaylama
* Bir alt ağdan ve internetten kaynağa erişimin reddedildiğini onaylama

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

PowerShell 'i yerel olarak yükleyip kullanmayı tercih ederseniz, bu makale Azure PowerShell modülü sürümü 1.0.0 veya üstünü gerektirir. Yüklü sürümü bulmak için `Get-Module -ListAvailable Az` komutunu çalıştırın. Yükseltmeniz gerekirse, bkz. [Azure PowerShell modülünü yükleme](/powershell/azure/install-az-ps). PowerShell'i yerel olarak çalıştırıyorsanız Azure bağlantısı oluşturmak için `Connect-AzAccount` komutunu da çalıştırmanız gerekir.

## <a name="create-a-virtual-network"></a>Sanal ağ oluşturma

Bir sanal ağ oluşturmadan önce, sanal ağ ve bu makalede oluşturulan tüm diğer kaynaklar için bir kaynak grubu oluşturmanız gerekir. [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)ile bir kaynak grubu oluşturun. Aşağıdaki örnek, *Myresourcegroup* adlı bir kaynak grubu oluşturur: 

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

[New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork)ile bir sanal ağ oluşturun. Aşağıdaki örnek, *10.0.0.0/16* adres ön ekine sahip *myVirtualNetwork* adlı bir sanal ağ oluşturur.

```azurepowershell-interactive
$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

[New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig)ile bir alt ağ yapılandırması oluşturun. Aşağıdaki örnek, *genel* adlı bir alt ağ için bir alt ağ yapılandırması oluşturur:

```azurepowershell-interactive
$subnetConfigPublic = Add-AzVirtualNetworkSubnetConfig `
  -Name Public `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork
```

Alt ağ yapılandırmasını [set-AzVirtualNetwork](/powershell/module/az.network/Set-azVirtualNetwork)ile sanal ağa yazarak sanal ağda alt ağ oluşturun:

```azurepowershell-interactive
$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="enable-a-service-endpoint"></a>Hizmet uç noktasını girin

Hizmet uç noktalarını yalnızca hizmet uç noktalarını destekleyen hizmetler için etkinleştirebilirsiniz. [Get-AzVirtualNetworkAvailableEndpointService](/powershell/module/az.network/get-azvirtualnetworkavailableendpointservice)Ile bir Azure konumunda bulunan hizmet uç noktası etkin hizmetlerini görüntüleyin. Aşağıdaki örnek, *eastus* bölgesinde kullanılabilir olan hizmet uç noktası etkin hizmetleri listesini döndürür. Daha fazla Azure hizmeti uç noktası etkinleştirildiğinden, döndürülen hizmetlerin listesi zaman içinde büyüyecektir.

```azurepowershell-interactive
Get-AzVirtualNetworkAvailableEndpointService -Location eastus | Select Name
```

Sanal ağda ek bir alt ağ oluşturun. Bu örnekte, *özel* adlı bir alt ağ, *Microsoft. Storage* için bir hizmet uç noktası ile oluşturulur: 

```azurepowershell-interactive
$subnetConfigPrivate = Add-AzVirtualNetworkSubnetConfig `
  -Name Private `
  -AddressPrefix 10.0.1.0/24 `
  -VirtualNetwork $virtualNetwork `
  -ServiceEndpoint Microsoft.Storage

$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="restrict-network-access-for-a-subnet"></a>Bir kaynak için ağ erişimini kısıtlama

[New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig)ile ağ güvenlik grubu güvenlik kuralları oluşturun. Aşağıdaki kural, Azure depolama hizmetine atanan genel IP adreslerine giden erişime izin verir: 

```azurepowershell-interactive
$rule1 = New-AzNetworkSecurityRuleConfig `
  -Name Allow-Storage-All `
  -Access Allow `
  -DestinationAddressPrefix Storage `
  -DestinationPortRange * `
  -Direction Outbound `
  -Priority 100 `
  -Protocol * `
  -SourceAddressPrefix VirtualNetwork `
  -SourcePortRange *
```

Aşağıdaki kural tüm genel IP adreslerine erişimi reddeder. Önceki kural, Azure depolama 'nın genel IP adreslerine erişim sağlayan daha yüksek öncelikli bir nedenle bu kuralı geçersiz kılar.

```azurepowershell-interactive
$rule2 = New-AzNetworkSecurityRuleConfig `
  -Name Deny-Internet-All `
  -Access Deny `
  -DestinationAddressPrefix Internet `
  -DestinationPortRange * `
  -Direction Outbound `
  -Priority 110 `
  -Protocol * `
  -SourceAddressPrefix VirtualNetwork `
  -SourcePortRange *
```

Aşağıdaki kural, alt ağa gelen Uzak Masaüstü Protokolü (RDP) trafiğinin her yerden çalışmasına izin verir. Daha sonraki bir adımda bir kaynağa ağ erişimini doğrulayabilmeniz için, Uzak Masaüstü bağlantılarına alt ağ ile izin verilir.

```azurepowershell-interactive
$rule3 = New-AzNetworkSecurityRuleConfig `
  -Name Allow-RDP-All `
  -Access Allow `
  -DestinationAddressPrefix VirtualNetwork `
  -DestinationPortRange 3389 `
  -Direction Inbound `
  -Priority 120 `
  -Protocol * `
  -SourceAddressPrefix * `
  -SourcePortRange *
```

[New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup)ile bir ağ güvenlik grubu oluşturun. Aşağıdaki örnek, *Mynsgprivate* adlı bir ağ güvenlik grubu oluşturur.

```azurepowershell-interactive
$nsg = New-AzNetworkSecurityGroup `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myNsgPrivate `
  -SecurityRules $rule1,$rule2,$rule3
```

Ağ güvenlik grubunu [set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) ile *özel* alt ağ ile ilişkilendirin ve sonra alt ağ yapılandırmasını sanal ağa yazın. Aşağıdaki örnek, *Mynsgprivate* ağ güvenlik grubunu *özel* alt ağla ilişkilendirir:

```azurepowershell-interactive
Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $VirtualNetwork `
  -Name Private `
  -AddressPrefix 10.0.1.0/24 `
  -ServiceEndpoint Microsoft.Storage `
  -NetworkSecurityGroup $nsg

$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="restrict-network-access-to-a-resource"></a>Bir kaynağa ağ erişimini kısıtlama

Hizmet uç noktaları için etkinleştirilmiş Azure hizmetleri aracılığıyla oluşturulan kaynaklara ağ erişimini kısıtlamak için gereken adımlar, hizmetler arasında farklılık gösterir. Bir hizmete yönelik belirli adımlar için ilgili hizmetin belgelerine bakın. Bu makalenin geri kalanında bir Azure depolama hesabı için ağ erişimini bir örnek olarak kısıtlama adımları yer alır.

### <a name="create-a-storage-account"></a>Depolama hesabı oluşturma

[New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount)Ile bir Azure depolama hesabı oluşturun. `<replace-with-your-unique-storage-account-name>`Yalnızca rakamlar ve küçük harfler kullanarak, 3-24 karakter uzunluğunda tüm Azure konumlarında benzersiz olan bir adla değiştirin.

```azurepowershell-interactive
$storageAcctName = '<replace-with-your-unique-storage-account-name>'

New-AzStorageAccount `
  -Location EastUS `
  -Name $storageAcctName `
  -ResourceGroupName myResourceGroup `
  -SkuName Standard_LRS `
  -Kind StorageV2
```

Depolama hesabı oluşturulduktan sonra, [Get-AzStorageAccountKey](/powershell/module/az.storage/get-azstorageaccountkey)ile depolama hesabı anahtarını bir değişkene alın:

```azurepowershell-interactive
$storageAcctKey = (Get-AzStorageAccountKey `
  -ResourceGroupName myResourceGroup `
  -AccountName $storageAcctName).Value[0]
```

Anahtar, sonraki bir adımda dosya paylaşma oluşturmak için kullanılır. `$storageAcctKey`Dosya paylaşma 'yı BIR VM 'deki sürücüyle eşlediğinizde daha sonra da el ile girmeniz gerekeceğinden, değeri girin ve aklınızda bir adım girin.

### <a name="create-a-file-share-in-the-storage-account"></a>Depolama hesabında dosya paylaşımı oluşturma

[New-AzStorageContext](/powershell/module/az.storage/new-AzStoragecontext)ile depolama hesabınız ve anahtarınız için bir bağlam oluşturun. Bağlam, depolama hesabı adını ve hesap anahtarını kapsüller:

```azurepowershell-interactive
$storageContext = New-AzStorageContext $storageAcctName $storageAcctKey
```

[New-Azstoragesshare](/powershell/module/az.storage/new-azstorageshare)ile bir dosya paylaşımı oluşturun:

$share = New-AzStorageShare My-Share-Context $storageContext

### <a name="deny-all-network-access-to-a-storage-account"></a>Bir depolama hesabına tüm ağ erişimini reddet

Varsayılan olarak, depolama hesapları herhangi bir ağdaki istemcilerden gelen ağ bağlantılarını kabul eder. Seçilen ağlara erişimi sınırlandırmak için varsayılan eylemi [Update-AzStorageAccountNetworkRuleSet](/powershell/module/az.storage/update-azstorageaccountnetworkruleset)ile *Reddet* olarak değiştirin. Ağ erişimi reddedildiğinde depolama hesabı hiçbir ağdan erişilebilir olmaz.

```azurepowershell-interactive
Update-AzStorageAccountNetworkRuleSet  `
  -ResourceGroupName "myresourcegroup" `
  -Name $storageAcctName `
  -DefaultAction Deny
```

### <a name="enable-network-access-from-a-subnet"></a>Bir alt ağdan ağ erişimini etkinleştirme

[Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) ile oluşturulan sanal ağı alın ve sonra özel alt ağ nesnesini [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig)ile bir değişkene alın:

```azurepowershell-interactive
$privateSubnet = Get-AzVirtualNetwork `
  -ResourceGroupName "myResourceGroup" `
  -Name "myVirtualNetwork" `
  | Get-AzVirtualNetworkSubnetConfig `
  -Name "Private"
```

[Add-AzStorageAccountNetworkRule](/powershell/module/az.network/add-aznetworksecurityruleconfig)ile *özel* alt ağdan depolama hesabına ağ erişimine izin verin.

```azurepowershell-interactive
Add-AzStorageAccountNetworkRule `
  -ResourceGroupName "myresourcegroup" `
  -Name $storageAcctName `
  -VirtualNetworkResourceId $privateSubnet.Id
```

## <a name="create-virtual-machines"></a>Sanal makineler oluşturma

Bir depolama hesabına ağ erişimini test etmek için her alt ağa bir VM dağıtın.

### <a name="create-the-first-virtual-machine"></a>İlk sanal makineyi oluşturma

[New-AzVM](/powershell/module/az.compute/new-azvm)ile *ortak* alt ağda bir sanal makine oluşturun. Sonraki komutu çalıştırırken kimlik bilgileri istenir. Girdiğiniz değerler, sanal makinenin kullanıcı adı ve parolası olarak yapılandırılır. `-AsJob` seçeneği, sonraki adıma devam edebilmeniz için arka planda sanal makineyi oluşturur.

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Location "East US" `
    -VirtualNetworkName "myVirtualNetwork" `
    -SubnetName "Public" `
    -Name "myVmPublic" `
    -AsJob
```

Aşağıdaki örnek çıktıya benzer bir çıktı döndürülür:

```powershell
Id     Name            PSJobTypeName   State         HasMoreData     Location             Command                  
--     ----            -------------   -----         -----------     --------             -------                  
1      Long Running... AzureLongRun... Running       True            localhost            New-AzVM     
```

### <a name="create-the-second-virtual-machine"></a>İkinci sanal makineyi oluşturma

*Özel* alt ağda bir sanal makine oluşturun:

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Location "East US" `
    -VirtualNetworkName "myVirtualNetwork" `
    -SubnetName "Private" `
    -Name "myVmPrivate"
```

Azure 'un VM 'yi oluşturması birkaç dakika sürer. Azure VM 'yi oluşturmayı bitirene ve çıktıyı PowerShell 'e döndürünceye kadar sonraki adıma devam etmez.

## <a name="confirm-access-to-storage-account"></a>Depolama hesabına erişimi onaylama

Bir sanal makinenin genel IP adresini döndürmek için [Get-Azpublicıpaddress](/powershell/module/az.network/get-azpublicipaddress) komutunu kullanın. Aşağıdaki örnek *myVmPrivate* VM 'nın genel IP adresini döndürür:

```azurepowershell-interactive
Get-AzPublicIpAddress `
  -Name myVmPrivate `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Aşağıdaki komuttaki `<publicIpAddress>` öğesini, önceki komuttan döndürülen genel IP adresiyle değiştirin ve sonra aşağıdaki komutu girin:

```powershell
mstsc /v:<publicIpAddress>
```

Uzak Masaüstü Protokolü (.rdp) dosyası oluşturulur ve bilgisayarınıza indirilir. İndirilen rdp dosyasını açın. İstendiğinde **Bağlan**’ı seçin. Sanal makine oluştururken belirttiğiniz kullanıcı adını ve parolayı girin. Sanal makineyi oluştururken girdiğiniz kimlik bilgilerini belirtmek için **Diğer seçenekler**’i ve sonra **Farklı bir hesap kullan** seçeneğini belirlemeniz gerekebilir. **Tamam**’ı seçin. Oturum açma işlemi sırasında bir sertifika uyarısı alabilirsiniz. Uyarıyı alırsanız, bağlantıya devam etmek için **Evet** ' i veya **devam et**' i seçin.

*myVmPrivate* VM üzerinde PowerShell kullanarak Azure dosya paylaşımını Z sürücüsüne eşleyin. İzleyen komutları çalıştırmadan önce, `<storage-account-key>` ve `<storage-account-name>` [depolama hesabı oluşturma](#create-a-storage-account)bölümünde sağladığınız veya aldığınız değerlerle değiştirin.

```powershell
$acctKey = ConvertTo-SecureString -String "<storage-account-key>" -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential -ArgumentList "Azure\<storage-account-name>", $acctKey
New-PSDrive -Name Z -PSProvider FileSystem -Root "\\<storage-account-name>.file.core.windows.net\my-file-share" -Credential $credential
```

PowerShell aşağıdaki örnek çıktıya benzer bir çıktı döndürür:

```powershell
Name           Used (GB)     Free (GB) Provider      Root
----           ---------     --------- --------      ----
Z                                      FileSystem    \\vnt.file.core.windows.net\my-f...
```

Z sürücüsüne başarıyla eşlenen Azure dosya paylaşımı.

VM 'nin herhangi bir genel IP adresine giden bağlantısı olmadığını doğrulayın:

```powershell
ping bing.com
```

*Özel* alt ağ ile ilişkili ağ güvenlik grubu Azure Depolama hizmetine atanan adreslerden başka genel IP adreslerine giden erişime izin vermediği için bir yanıt almazsınız.

*myVmPrivate* VM ile uzak masaüstü oturumunu kapatın.

## <a name="confirm-access-is-denied-to-storage-account"></a>Depolama hesabına erişimin reddedildiğini onaylama

*MyVmPublic* VM 'nın genel IP adresini alın:

```azurepowershell-interactive
Get-AzPublicIpAddress `
  -Name myVmPublic `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Aşağıdaki komuttaki `<publicIpAddress>` öğesini, önceki komuttan döndürülen genel IP adresiyle değiştirin ve sonra aşağıdaki komutu girin: 

```powershell
mstsc /v:<publicIpAddress>
```

*MyVmPublic* VM 'de, Azure dosya paylaşımından Z sürücüsüyle eşlemeyi deneyin. İzleyen komutları çalıştırmadan önce, `<storage-account-key>` ve `<storage-account-name>` [depolama hesabı oluşturma](#create-a-storage-account)bölümünde sağladığınız veya aldığınız değerlerle değiştirin.

```powershell
$acctKey = ConvertTo-SecureString -String "<storage-account-key>" -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential -ArgumentList "Azure\<storage-account-name>", $acctKey
New-PSDrive -Name Z -PSProvider FileSystem -Root "\\<storage-account-name>.file.core.windows.net\my-file-share" -Credential $credential
```

Paylaşıma erişim reddedildi ve bir `New-PSDrive : Access is denied` hata alıyorsunuz. *myVmPublic* VM *Genel* alt ağa dağıtıldığı için erişim reddedilir. *Genel* alt ağında Azure Depolama için etkinleştirilmiş bir hizmet uç noktası bulunmaz ve depolama hesabı *Genel* alt ağından değil, yalnızca *Özel* alt ağından ağ erişimine izin verir.

*myVmPublic* VM ile uzak masaüstü oturumunu kapatın.

Aşağıdaki komutla, bilgisayarınızdan depolama hesabındaki dosya paylaşımlarını görüntülemeyi deneyin:

```powershell-interactive
Get-AzStorageFile `
  -ShareName my-file-share `
  -Context $storageContext
```

Erişim reddedildi ve bir *Get-AzStorageFile alıyorsunuz: uzak sunucu bir hata döndürdü: (403) yasak. HTTP durum kodu: 403-HTTP hata Iletisi: Bu istek* , bilgisayarınız *MyVirtualNetwork* sanal ağının *özel* alt ağında olmadığından bu işlemi gerçekleştirmek için yetkilendirilmemiş.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli değilse, [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) komutunu kullanarak kaynak grubunu ve içerdiği tüm kaynakları kaldırabilirsiniz:

```azurepowershell-interactive 
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, bir sanal ağ alt ağı için bir hizmet uç noktası etkinleştirdiniz. Hizmet uç noktalarının birden fazla Azure hizmeti ile dağıtılmış kaynaklar için etkinleştirilebildiğini öğrendiniz. Bir Azure Depolama hesabı oluşturdunuz ve depolama hesabına ağ erişimini yalnızca bir sanal ağ alt ağındaki kaynaklarla sınırladınız. Hizmet uç noktaları hakkında daha fazla bilgi için bkz. [Hizmet uç noktalarına genel bakış](virtual-network-service-endpoints-overview.md) ve [Alt ağları yönetme](virtual-network-manage-subnet.md).

Hesabınızda birden fazla sanal ağ varsa, her bir sanal ağın içindeki kaynakların birbiriyle iletişim kurabilmesi iki sanal ağı birbirine bağlamak isteyebilirsiniz. Nasıl yapılacağını öğrenmek için bkz. [sanal ağları bağlama](tutorial-connect-virtual-networks-powershell.md).
