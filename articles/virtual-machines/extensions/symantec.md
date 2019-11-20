---
title: Azure 'da Windows VM 'ye Symantec Endpoint Protection 'yi yükler
description: Symantec Endpoint Protection güvenlik uzantısını, klasik dağıtım modeliyle oluşturulan yeni veya mevcut bir Azure VM 'ye yüklemeyi ve yapılandırmayı öğrenin.
services: virtual-machines-windows
documentationcenter: ''
author: roiyz
manager: gwallace
editor: ''
tags: azure-service-management
ms.assetid: 19dcebc7-da6b-4510-907b-d64088e81fa2
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.topic: article
ms.date: 03/31/2017
ms.author: akjosh
ms.openlocfilehash: 143a697e509b96787ad3f52567f5a70fb7e5f4a4
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74072955"
---
# <a name="how-to-install-and-configure-symantec-endpoint-protection-on-a-windows-vm"></a>Bir Windows VM’de Symantec Uç Nokta Koruması yükleme ve yapılandırma
> [!IMPORTANT] 
> Azure 'da kaynak oluşturmak ve bunlarla çalışmak için iki farklı dağıtım modeli vardır: [Kaynak Yöneticisi ve klasik](../../azure-resource-manager/resource-manager-deployment-model.md). Bu makalede, klasik dağıtım modelinin kullanımı ele alınmaktadır. Microsoft, yeni dağıtımların çoğunun Resource Manager modelini kullanmasını önerir.

Bu makalede, Symantec Endpoint Protection istemcisinin Windows Server çalıştıran var olan bir sanal makineye (VM) nasıl yükleneceği ve yapılandırılacağı gösterilmektedir. Bu tam istemci, virüs ve casus yazılım koruması, güvenlik duvarı ve izinsiz giriş önleme gibi hizmetleri içerir. İstemci, VM Aracısı kullanılarak bir güvenlik uzantısı olarak yüklenir.

Symantec 'ten şirket içi bir çözüm için mevcut bir aboneliğiniz varsa, Azure sanal makinelerinizi korumak için bunu kullanabilirsiniz. Henüz bir müşteriyseniz, bir deneme aboneliği için kaydolabilirsiniz. Bu çözüm hakkında daha fazla bilgi için bkz. [Microsoft 'un Azure platformunda Symantec Endpoint Protection][Symantec]. Bu sayfada, zaten bir Symantec müşterisiyseniz lisanslama bilgilerine ve istemciyi yüklemeye yönelik yönergelere bağlantılar de bulunur.

## <a name="install-symantec-endpoint-protection-on-an-existing-vm"></a>Mevcut bir VM 'ye Symantec Endpoint Protection 'yi yükler
Başlamadan önce aşağıdakilere ihtiyacınız vardır:

* İş bilgisayarınızdaki Azure PowerShell modülü, sürüm 0.8.2 veya üzeri. Yüklediğiniz Azure PowerShell sürümünü **Get-Module Azure | biçim-tablo sürümü** komutuyla kontrol edebilirsiniz. Yönergeler ve en son sürüme bağlantı için bkz. [Azure PowerShell nasıl yüklenir ve yapılandırılır][PS]. `Add-AzureAccount`kullanarak Azure aboneliğinizde oturum açın.
* Azure sanal makinesinde çalışan VM Aracısı.

İlk olarak, VM aracısının sanal makinede zaten yüklü olduğunu doğrulayın. Bulut hizmeti adını ve sanal makine adını doldurup yönetici düzeyinde bir Azure PowerShell komut isteminde aşağıdaki komutları çalıştırın. < Ve > karakterler dahil olmak üzere tırnak içindeki her şeyi değiştirin.

> [!TIP]
> Bulut hizmeti ve sanal makine adlarını bilmiyorsanız, geçerli aboneliğinizdeki tüm sanal makinelerin adlarını listelemek için **Get-AzureVM** ' ı çalıştırın.

```powershell
$CSName = "<cloud service name>"
$VMName = "<virtual machine name>"
$vm = Get-AzureVM -ServiceName $CSName -Name $VMName
write-host $vm.VM.ProvisionGuestAgent
```

**Write-Host** komutu **true**görüntülüyorsa, VM Aracısı yüklenir. **Yanlış**görüntüleniyorsa, Azure Blog Post [VM aracısında ve uzantıları-Bölüm 2][Agent]' de bulunan yönergelere ve bir bağlantıya bakın.

VM Aracısı yüklüyse, Symantec Endpoint Protection Aracısı 'nı yüklemek için şu komutları çalıştırın.

```powershell
$Agent = Get-AzureVMAvailableExtension -Publisher Symantec -ExtensionName SymantecEndpointProtection

Set-AzureVMExtension -Publisher Symantec –Version $Agent.Version -ExtensionName SymantecEndpointProtection \
    -VM $vm | Update-AzureVM
```

Symantec güvenlik uzantısının yüklendiğini ve güncel olduğunu doğrulamak için:

1. Sanal makinede oturum açın. Yönergeler için bkz. [Windows Server çalıştıran bir sanal makinede oturum açma][Logon].
2. Windows Server 2008 R2 için, **> Symantec Endpoint Protection Başlat**' a tıklayın. Windows Server 2012 veya Windows Server 2012 R2 için, başlangıç ekranında, **Symantec**yazın ve ardından **Symantec Endpoint Protection**' ye tıklayın.
3. **Durum-Symantec Endpoint Protection** penceresinin **durum** sekmesinde, güncelleştirmeleri uygulayın veya gerekirse yeniden başlatın.

## <a name="additional-resources"></a>Ek kaynaklar
[Windows Server çalıştıran bir sanal makinede oturum açma][Logon]

[Azure VM uzantıları ve özellikleri][Ext]

<!--Link references-->
[Symantec]: https://www.symantec.com/connect/blogs/symantec-endpoint-protection-now-microsoft-azure

[Create]:../windows/classic/tutorial.md

[PS]: /powershell/azureps-cmdlets-docs

[Agent]: https://go.microsoft.com/fwlink/p/?LinkId=403947

[Logon]:../windows/classic/connect-logon.md

[Ext]: https://go.microsoft.com/fwlink/p/?linkid=390493
