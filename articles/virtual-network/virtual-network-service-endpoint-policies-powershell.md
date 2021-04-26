---
title: Azure depolama 'ya veri alımını kısıtla-Azure PowerShell
description: Bu makalede, Azure PowerShell kullanarak sanal ağ hizmeti uç noktası ilkeleriyle sanal ağ verilerini nasıl sınırlandırmayı ve kısıtlayacağınızı öğreneceksiniz.
services: virtual-network
documentationcenter: virtual-network
author: RDhillon
manager: narayan
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: rdhillon
ms.custom: ''
ms.openlocfilehash: 3ff0ee79413640c09132b8399a3b0c07dd1783cc
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106063719"
---
# <a name="manage-data-exfiltration-to-azure-storage-accounts-with-virtual-network-service-endpoint-policies-using-azure-powershell"></a>Azure PowerShell kullanarak sanal ağ hizmeti uç noktası ilkeleri ile Azure depolama hesaplarına veri alımını yönetme

Sanal ağ hizmeti uç noktası ilkeleri, Azure depolama hesaplarında hizmet uç noktaları üzerinden bir sanal ağ içinden erişim denetimi uygulamanıza olanak tanır. Bu, iş yüklerinizi güvenli hale getirmenin, hangi depolama hesaplarına izin verileceğini ve veri ayıklanma izin verileceğini yönetme anahtarıdır.
Bu makalede şunları öğreneceksiniz:

* Sanal ağ oluşturun.
* Azure depolama için bir alt ağ ekleyin ve hizmet uç noktasını etkinleştirin.
* İki Azure depolama hesabı oluşturun ve yukarıda oluşturulan alt ağdan ağ erişimine izin verin.
* Yalnızca depolama hesaplarından birine erişime izin vermek için bir hizmet uç noktası ilkesi oluşturun.
* Alt ağa bir sanal makine (VM) dağıtın.
* Alt ağdan izin verilen depolama hesabına erişimi onaylayın.
* Alt ağdan izin verilmeyen depolama hesabına erişimin reddedildiğini doğrulayın.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

PowerShell 'i yerel olarak yükleyip kullanmayı tercih ederseniz, bu makale Azure PowerShell modülü sürümü 1.0.0 veya üstünü gerektirir. Yüklü sürümü bulmak için `Get-Module -ListAvailable Az` komutunu çalıştırın. Yükseltmeniz gerekirse, bkz. [Azure PowerShell modülünü yükleme](/powershell/azure/install-az-ps). PowerShell'i yerel olarak çalıştırıyorsanız Azure bağlantısı oluşturmak için `Connect-AzAccount` komutunu da çalıştırmanız gerekir.

## <a name="create-a-virtual-network"></a>Sanal ağ oluşturma

Bir sanal ağ oluşturmadan önce, sanal ağ ve bu makalede oluşturulan tüm diğer kaynaklar için bir kaynak grubu oluşturmanız gerekir. [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)ile bir kaynak grubu oluşturun. Aşağıdaki örnek, *Myresourcegroup* adlı bir kaynak grubu oluşturur: 

```azurepowershell-interactive
New-AzResourceGroup `
  -ResourceGroupName myResourceGroup `
  -Location EastUS
```

[New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork)ile bir sanal ağ oluşturun. Aşağıdaki örnek, *10.0.0.0/16* adres ön ekine sahip *myVirtualNetwork* adlı bir sanal ağ oluşturur.

```azurepowershell-interactive
$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

## <a name="enable-a-service-endpoint"></a>Hizmet uç noktasını girin

Sanal ağda bir alt ağ oluşturun. Bu örnekte, *özel* adlı bir alt ağ, *Microsoft. Storage* için bir hizmet uç noktası ile oluşturulur: 

```azurepowershell-interactive
$subnetConfigPrivate = Add-AzVirtualNetworkSubnetConfig `
  -Name Private `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork `
  -ServiceEndpoint Microsoft.Storage

$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="restrict-network-access-for-the-subnet"></a>Alt ağ için ağ erişimini kısıtlama

[New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig)ile ağ güvenlik grubu güvenlik kuralları oluşturun. Aşağıdaki kural, Azure depolama hizmetine atanan genel IP adreslerine giden erişime izin verir: 

```azurepowershell-interactive
$rule1 = New-AzNetworkSecurityRuleConfig `
  -Name Allow-Storage-All `
  -Access Allow `
  -DestinationAddressPrefix Storage `
  -DestinationPortRange * `
  -Direction Outbound `
  -Priority 100 -Protocol * `
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
  -Priority 110 -Protocol * `
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
  -AddressPrefix 10.0.0.0/24 `
  -ServiceEndpoint Microsoft.Storage `
  -NetworkSecurityGroup $nsg

$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="restrict-network-access-to-azure-storage-accounts"></a>Azure depolama hesaplarına ağ erişimini kısıtlama

Hizmet uç noktaları için etkinleştirilmiş Azure hizmetleri aracılığıyla oluşturulan kaynaklara ağ erişimini kısıtlamak için gereken adımlar, hizmetler arasında farklılık gösterir. Bir hizmete yönelik belirli adımlar için ilgili hizmetin belgelerine bakın. Bu makalenin geri kalanında bir Azure depolama hesabı için ağ erişimini bir örnek olarak kısıtlama adımları yer alır.

### <a name="create-two-storage-accounts"></a>İki depolama hesabı oluşturun

[New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount)Ile bir Azure depolama hesabı oluşturun.

```azurepowershell-interactive
$storageAcctName1 = 'allowedaccount'

New-AzStorageAccount `
  -Location EastUS `
  -Name $storageAcctName1 `
  -ResourceGroupName myResourceGroup `
  -SkuName Standard_LRS `
  -Kind StorageV2
```

Depolama hesabı oluşturulduktan sonra, [Get-AzStorageAccountKey](/powershell/module/az.storage/get-azstorageaccountkey)ile depolama hesabı anahtarını bir değişkene alın:

```azurepowershell-interactive
$storageAcctKey1 = (Get-AzStorageAccountKey -ResourceGroupName myResourceGroup -AccountName $storageAcctName1).Value[0]
```

Anahtar, sonraki bir adımda dosya paylaşma oluşturmak için kullanılır. `$storageAcctKey`Dosya paylaşma 'yı BIR VM 'deki sürücüyle eşlediğinizde daha sonra da el ile girmeniz gerekeceğinden, değeri girin ve aklınızda bir adım girin.

Şimdi ikinci bir depolama hesabı oluşturmak için yukarıdaki adımları tekrarlayın.

```azurepowershell-interactive
$storageAcctName2 = 'notallowedaccount'

New-AzStorageAccount `
  -Location EastUS `
  -Name $storageAcctName2 `
  -ResourceGroupName myResourceGroup `
  -SkuName Standard_LRS `
  -Kind StorageV2
```

Ayrıca, daha sonra bir dosya paylaşımının oluşturulması için bu hesaptan depolama hesabı anahtarını alın.

```azurepowershell-interactive
$storageAcctKey2 = (Get-AzStorageAccountKey -ResourceGroupName myResourceGroup -AccountName $storageAcctName2).Value[0]
```

### <a name="create-a-file-share-in-each-of-the-storage-account"></a>Depolama hesabında her birinde bir dosya paylaşma oluşturun

[New-AzStorageContext](/powershell/module/az.storage/new-AzStoragecontext)ile depolama hesabınız ve anahtarınız için bir bağlam oluşturun. Bağlam, depolama hesabı adını ve hesap anahtarını kapsüller:

```azurepowershell-interactive
$storageContext1 = New-AzStorageContext $storageAcctName1 $storageAcctKey1

$storageContext2 = New-AzStorageContext $storageAcctName2 $storageAcctKey2
```

[New-Azstoragesshare](/powershell/module/az.storage/new-azstorageshare)ile bir dosya paylaşımı oluşturun:

```azurepowershell-interactive
$share1 = New-AzStorageShare my-file-share -Context $storageContext1

$share2 = New-AzStorageShare my-file-share -Context $storageContext2
```

### <a name="deny-all-network-access-to-a-storage-accounts"></a>Depolama hesaplarına tüm ağ erişimini reddet

Varsayılan olarak, depolama hesapları herhangi bir ağdaki istemcilerden gelen ağ bağlantılarını kabul eder. Seçilen ağlara erişimi sınırlandırmak için varsayılan eylemi [Update-AzStorageAccountNetworkRuleSet](/powershell/module/az.storage/update-azstorageaccountnetworkruleset)ile *Reddet* olarak değiştirin. Ağ erişimi reddedildiğinde depolama hesabı hiçbir ağdan erişilebilir olmaz.

```azurepowershell-interactive
Update-AzStorageAccountNetworkRuleSet `
  -ResourceGroupName myresourcegroup `
  -Name $storageAcctName1 `
  -DefaultAction Deny

Update-AzStorageAccountNetworkRuleSet  `
  -ResourceGroupName myresourcegroup `
  -Name $storageAcctName2 `
  -DefaultAction Deny
```

### <a name="enable-network-access-only-from-the-vnet-subnet"></a>Yalnızca VNet alt ağından ağ erişimini etkinleştir

[Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) ile oluşturulan sanal ağı alın ve sonra özel alt ağ nesnesini [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig)ile bir değişkene alın:

```azurepowershell-interactive
$privateSubnet = Get-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Name myVirtualNetwork `
  | Get-AzVirtualNetworkSubnetConfig -Name Private
```

[Add-AzStorageAccountNetworkRule](/powershell/module/az.network/add-aznetworksecurityruleconfig)ile *özel* alt ağdan depolama hesabına ağ erişimine izin verin.

```azurepowershell-interactive
Add-AzStorageAccountNetworkRule `
  -ResourceGroupName myresourcegroup `
  -Name $storageAcctName1 `
  -VirtualNetworkResourceId $privateSubnet.Id

Add-AzStorageAccountNetworkRule `
  -ResourceGroupName myresourcegroup `
  -Name $storageAcctName2 `
  -VirtualNetworkResourceId $privateSubnet.Id
```

## <a name="apply-policy-to-allow-access-to-valid-storage-account"></a>Geçerli depolama hesabına erişime izin vermek için ilkeyi Uygula

Sanal ağ kullanıcılarının yalnızca güvenli ve izin verilen Azure depolama hesaplarına erişebildiğinizden emin olmak için, tanımda izin verilen depolama hesapları listesiyle bir hizmet uç noktası ilkesi oluşturabilirsiniz. Bu ilke daha sonra hizmet uç noktaları aracılığıyla depolamaya bağlı olan sanal ağ alt ağına uygulanır.

### <a name="create-a-service-endpoint-policy"></a>Hizmet uç noktası ilkesi oluşturma

Bu bölüm, hizmet uç noktası üzerinden erişim için izin verilen kaynaklar listesiyle ilke tanımını oluşturur

Birinci (izin verilen) depolama hesabı için kaynak KIMLIĞINI al 

```azurepowershell-interactive
$resourceId = (Get-AzStorageAccount -ResourceGroupName myresourcegroup -Name $storageAcctName1).id
```

Yukarıdaki kaynağa izin vermek için ilke tanımını oluşturun

```azurepowershell-interactive
$policyDefinition = New-AzServiceEndpointPolicyDefinition -Name mypolicydefinition `
  -Description "Service Endpoint Policy Definition" `
  -Service "Microsoft.Storage" `
  -ServiceResource $resourceId
```

Yukarıda oluşturulan ilke tanımını kullanarak hizmet uç noktası ilkesini oluşturma

```azurepowershell-interactive
$sepolicy = New-AzServiceEndpointPolicy -ResourceGroupName myresourcegroup `
  -Name mysepolicy -Location EastUS
  -ServiceEndpointPolicyDefinition $policyDefinition
```

### <a name="associate-the-service-endpoint-policy-to-the-virtual-network-subnet"></a>Hizmet uç noktası ilkesini sanal ağ alt ağıyla ilişkilendir

Hizmet uç noktası ilkesini oluşturduktan sonra, Azure depolama için hizmet uç noktası yapılandırması ile hedef alt ağla ilişkilendireceğiz.

```azurepowershell-interactive
Set-AzVirtualNetworkSubnetConfig -VirtualNetwork $VirtualNetwork `
  -Name Private `
  -AddressPrefix 10.0.0.0/24 `
  -NetworkSecurityGroup $nsg `
  -ServiceEndpoint Microsoft.Storage `
  -ServiceEndpointPolicy $sepolicy

$virtualNetwork | Set-AzVirtualNetwork
```
## <a name="validate-access-restriction-to-azure-storage-accounts"></a>Azure depolama hesaplarına erişim kısıtlamasını doğrulama

### <a name="deploy-the-virtual-machine"></a>Sanal makineyi dağıtma

Bir depolama hesabına ağ erişimini test etmek için alt ağda bir VM dağıtın.

[New-AzVM](/powershell/module/az.compute/new-azvm)ile *özel* alt ağda bir sanal makine oluşturun. Sonraki komutu çalıştırırken kimlik bilgileri istenir. Girdiğiniz değerler, sanal makinenin kullanıcı adı ve parolası olarak yapılandırılır. `-AsJob` seçeneği, sonraki adıma devam edebilmeniz için arka planda sanal makineyi oluşturur.

```azurepowershell-interactive
New-AzVm -ResourceGroupName myresourcegroup `
  -Location "East US" `
  -VirtualNetworkName myVirtualNetwork `
  -SubnetName Private `
  -Name "myVMPrivate" -AsJob
```

Aşağıdaki örnek çıktıya benzer bir çıktı döndürülür:

```powershell
Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
--     ----            -------------   -----         -----------     --------             -------
1      Long Running... AzureLongRun... Running       True            localhost            New-AzVM
```

### <a name="confirm-access-to-the-allowed-storage-account"></a>*İzin verilen* depolama hesabına erişimi onaylayın

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

*MyVmPrivate* VM 'de, PowerShell kullanarak Azure dosya paylaşımının izin verilen depolama hesabından Z sürücüsüne eşleyin. 

```powershell
$acctKey = ConvertTo-SecureString -String $storageAcctKey1 -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential -ArgumentList ("Azure\allowedaccount"), $acctKey
New-PSDrive -Name Z -PSProvider FileSystem -Root "\\allowedaccount.file.core.windows.net\my-file-share" -Credential $credential
```

PowerShell aşağıdaki örnek çıktıya benzer bir çıktı döndürür:

```powershell
Name           Used (GB)     Free (GB) Provider      Root
----           ---------     --------- --------      ----
Z                                      FileSystem    \\allowedaccount.file.core.windows.net\my-f...
```

Z sürücüsüne başarıyla eşlenen Azure dosya paylaşımı.

*myVmPrivate* VM ile uzak masaüstü oturumunu kapatın.

### <a name="confirm-access-is-denied-to-non-allowed-storage-account"></a>*İzin verilmeyen* depolama hesabına erişimin reddedildiğini onaylayın

Aynı *myVmPrivate* VM 'de, Azure dosya paylaşımından X sürücüsünü eşleştirmeyi deneyin. 

```powershell
$acctKey = ConvertTo-SecureString -String $storageAcctKey1 -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential -ArgumentList "Azure\notallowedaccount", $acctKey
New-PSDrive -Name X -PSProvider FileSystem -Root "\\notallowedaccount.file.core.windows.net\my-file-share" -Credential $credential
```

Paylaşıma erişim reddedildi ve bir `New-PSDrive : Access is denied` hata alıyorsunuz. Erişim reddedildi çünkü depolama hesabı *notallowedaccount* , hizmet uç noktası ilkesindeki izin verilen kaynaklar listesinde değil. 

*myVmPublic* VM ile uzak masaüstü oturumunu kapatın.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli değilse, [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) komutunu kullanarak kaynak grubunu ve içerdiği tüm kaynakları kaldırabilirsiniz:

```azurepowershell-interactive 
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, Azure depolama 'ya bir Azure sanal ağ hizmeti uç noktası üzerinden bir hizmet uç noktası İlkesi uyguladınız. Azure depolama hesaplarını ve sınırlı ağ erişimini bir sanal ağ alt ağından yalnızca belirli depolama hesaplarına (ve bu nedenle reddedilir) oluşturdunuz. Hizmet uç noktası ilkeleri hakkında daha fazla bilgi için bkz. [hizmet uç noktası ilkelerine genel bakış](virtual-network-service-endpoint-policies-overview.md).
