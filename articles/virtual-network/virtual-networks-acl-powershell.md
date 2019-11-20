---
title: Azure uç noktası erişim denetim listelerini yönetme | PowerShell | Klasik | Microsoft Docs
description: PowerShell ile ACL 'Leri yönetmeyi öğrenin
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
ms.assetid: c84e40af-f351-4572-b3f0-d572d46bafe7
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: genli
ms.openlocfilehash: 4fe8349863c16a15886f9f1d33056f0bbfec31f2
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71056765"
---
# <a name="manage-endpoint-access-control-lists-using-powershell-in-the-classic-deployment-model"></a>Klasik dağıtım modelinde PowerShell kullanarak uç nokta erişim denetim listelerini yönetme
Azure PowerShell veya Yönetim Portalı kullanarak uç noktalar için ağ Access Control listeleri (ACL) oluşturabilir ve yönetebilirsiniz. Bu konu başlığında, PowerShell kullanarak tamamlayabilmeniz için ACL ortak görevlerinin yordamlarını bulacaksınız. Azure PowerShell cmdlet 'leri listesi için bkz. [Azure Yönetim cmdlet 'leri](https://go.microsoft.com/fwlink/?LinkId=317721). ACL 'Ler hakkında daha fazla bilgi için bkz. [ağ Access Control listesi (ACL) nedir?](virtual-networks-acl.md). Yönetim Portalı kullanarak ACL 'Leri yönetmek istiyorsanız, bkz. [sanal makineye uç noktaları ayarlama](../virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="manage-network-acls-by-using-azure-powershell"></a>Azure PowerShell kullanarak ağ ACL 'Lerini yönetme
Ağ Access Control listelerini (ACL 'Ler) oluşturmak, kaldırmak ve yapılandırmak için Azure PowerShell cmdlet 'lerini kullanabilirsiniz. PowerShell kullanarak bir ACL 'yi yapılandırma yöntemlerinden bazılarına birkaç örnek ekledik.

ACL PowerShell cmdlet 'lerinin tüm listesini almak için aşağıdakilerden birini kullanabilirsiniz:

    Get-Help *AzureACL*
    Get-Command -Noun AzureACLConfig

### <a name="create-a-network-acl-with-rules-that-permit-access-from-a-remote-subnet"></a>Uzak alt ağdan erişime izin veren kurallarla ağ ACL 'SI oluşturma
Aşağıdaki örnekte, kuralları içeren yeni bir ACL oluşturma yöntemi gösterilmektedir. Bu ACL daha sonra bir sanal makine uç noktasına uygulanır. Aşağıdaki örnekteki ACL kuralları uzak bir alt ağdan erişime izin verir. Uzak alt ağ için izin verme kurallarına sahip yeni bir ağ ACL 'SI oluşturmak için bir Azure PowerShell ıSE açın. Aşağıdaki betiği kopyalayıp yapıştırın, betiği kendi değerlerinizle yapılandırıp betiği çalıştırın.

1. Yeni ağ ACL nesnesi oluşturun.
   
        $acl1 = New-AzureAclConfig
2. Uzak alt ağdan erişime izin veren bir kural ayarlayın. Aşağıdaki örnekte, sanal makine uç noktasına *10.0.0.0/8* erişimine izin vermek için kural *100* (200 ve üzeri kurala göre önceliğe sahiptir) ayarı ayarlanır. Değerleri kendi yapılandırma gereksinimlerinize göre değiştirin. "SharePoint ACL config" adı, bu kuralı çağırmak istediğiniz kolay adla değiştirilmelidir.
   
        Set-AzureAclConfig –AddRule –ACL $acl1 –Order 100 `
            –Action permit –RemoteSubnet "10.0.0.0/8" `
            –Description "SharePoint ACL config"
3. Ek kurallar için, değerleri kendi yapılandırma gereksinimlerinize göre değiştirerek cmdlet 'ini tekrarlayın. Kural numarası sırasını kuralların uygulanmasını istediğiniz sırayı yansıtacak şekilde değiştirdiğinizden emin olun. Alt kural numarası, daha yüksek sayıdan önceliklidir.
   
        Set-AzureAclConfig –AddRule –ACL $acl1 –Order 200 `
            –Action permit –RemoteSubnet "157.0.0.0/8" `
            –Description "web frontend ACL config"
4. Ardından, yeni bir uç nokta oluşturabilir (Ekle) veya ACL 'yi mevcut bir uç nokta (küme) olarak ayarlayabilirsiniz. Bu örnekte, "Web" adlı yeni bir sanal makine uç noktası ekleyecek ve sanal makine uç noktasını ACL ayarlarıyla güncelleştireceğiz.
   
        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Add-AzureEndpoint –Name "web" –Protocol tcp –Localport 80 - PublicPort 80 –ACL $acl1 `
        | Update-AzureVM
5. Sonra, cmdlet 'leri birleştirin ve betiği çalıştırın. Bu örnekte, birleştirilmiş cmdlet 'ler şöyle görünür:
   
        $acl1 = New-AzureAclConfig
        Set-AzureAclConfig –AddRule –ACL $acl1 –Order 100 `
            –Action permit –RemoteSubnet "10.0.0.0/8" `
            –Description "SharePoint ACL config"
        Set-AzureAclConfig –AddRule –ACL $acl1 –Order 200 `
            –Action permit –RemoteSubnet "157.0.0.0/8" `
            –Description "web frontend ACL config"
        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        |Add-AzureEndpoint –Name "web" –Protocol tcp –Localport 80 - PublicPort 80 –ACL $acl1 `
        |Update-AzureVM

### <a name="remove-a-network-acl-rule-that-permits-access-from-a-remote-subnet"></a>Uzak alt ağdan erişime izin veren bir ağ ACL kuralını kaldırma
Aşağıdaki örnekte, bir ağ ACL kuralını kaldırmanın bir yolu gösterilmektedir.  Uzak alt ağ için izin verme kurallarına sahip bir ağ ACL kuralını kaldırmak için Azure PowerShell ıSE 'yi açın. Aşağıdaki betiği kopyalayıp yapıştırın, betiği kendi değerlerinizle yapılandırıp betiği çalıştırın.

1. İlk adım, sanal makine uç noktası için ağ ACL nesnesi almaya yöneliktir. Daha sonra ACL kuralını kaldıracaksınız. Bu durumda, kuralı kural KIMLIĞIYLE kaldırdık. Bu, yalnızca ACL 'den 0 olan kural KIMLIĞINI kaldırır. ACL nesnesini sanal makine uç noktasından kaldırmaz.
   
        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Get-AzureAclConfig –EndpointName "web" `
        | Set-AzureAclConfig –RemoveRule –ID 0 –ACL $acl1
2. Sonra, ağ ACL nesnesini sanal makine uç noktasına uygulamanız ve sanal makineyi güncelleştirmeniz gerekir.
   
        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Set-AzureEndpoint –ACL $acl1 –Name "web" `
        | Update-AzureVM

### <a name="remove-a-network-acl-from-a-virtual-machine-endpoint"></a>Bir sanal makine uç noktasından ağ ACL 'sini kaldırma
Belirli senaryolarda bir ağ ACL nesnesini bir sanal makine uç noktasından kaldırmak isteyebilirsiniz. Bunu yapmak için bir Azure PowerShell ıSE açın. Aşağıdaki betiği kopyalayıp yapıştırın, betiği kendi değerlerinizle yapılandırıp betiği çalıştırın.

        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Remove-AzureAclConfig –EndpointName "web" `
        | Update-AzureVM

## <a name="next-steps"></a>Sonraki adımlar
[Ağ Access Control listesi (ACL) nedir?](virtual-networks-acl.md)

