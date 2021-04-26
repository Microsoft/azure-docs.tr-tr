---
title: Ağ Izleyicisi uzantısını en son sürüme güncelleştirin
description: Azure ağ Izleyicisi uzantısını en son sürüme güncelleştirmeyi öğrenin.
services: virtual-machines
documentationcenter: ''
author: damendo
manager: balar
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.subservice: extensions
ms.collection: windows
ms.topic: article
ms.workload: infrastructure-services
ms.date: 09/23/2020
ms.author: damendo
ms.openlocfilehash: 42efe2927b4d711f7fa66a96ebd25f1a62bf654a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102563617"
---
# <a name="update-the-network-watcher-extension-to-the-latest-version"></a>Ağ Izleyicisi uzantısını en son sürüme güncelleştirin

## <a name="overview"></a>Genel Bakış

[Azure Ağ İzleyicisi](../../network-watcher/network-watcher-monitoring-overview.md) , Azure ağlarını izleyen bir ağ performansı izleme, tanılama ve analiz hizmetidir. Ağ Izleyicisi Aracısı sanal makinesi (VM) uzantısı, ağ trafiğini talep üzerine yakalama ve Azure VM 'lerinde diğer gelişmiş işlevleri kullanma gereksinimidir. Ağ Izleyicisi uzantısı, bağlantı Izleyicisi, bağlantı Izleyicisi (Önizleme), bağlantı sorunlarını giderme ve paket yakalama gibi özellikler tarafından kullanılır.

## <a name="prerequisites"></a>Önkoşullar

Bu makalede, ağ Izleyicisi uzantısının sanal makinenizde yüklü olduğu varsayılır.

## <a name="latest-version"></a>En son sürüm

Ağ Izleyicisi uzantısının en son sürümü şu anda `1.4.1693.1` .

## <a name="update-your-extension-using-a-powershell-script"></a>Bir PowerShell betiği kullanarak uzantınızı güncelleştirme
Aynı anda birden çok VM 'yi güncelleştirmesi gereken büyük dağıtımları olan müşteriler. Select VM 'Leri el ile güncelleştirmek için lütfen sonraki bölüme bakın 

```powershell
<#
    .SYNOPSIS
    This script will scan all VMs in the provided subscription and upgrade any out of date AzureNetworkWatcherExtensions

    .DESCRIPTION
    This script should be no-op if AzureNetworkWatcherExtensions are up to date
    Requires Azure PowerShell 4.2 or higher to be installed (e.g. Install-Module AzureRM).

    .EXAMPLE
    .\UpdateVMAgentsInSub.ps1 -SubID F4BC4873-5DAB-491E-B713-1358EF4992F2 -NoUpdate

#>
[CmdletBinding()]
param(
    [Parameter(Mandatory=$true)]
    [string] $SubID,
    [Parameter(Mandatory=$false)]
    [Switch] $NoUpdate = $false,
    [Parameter(Mandatory=$false)]
    [string] $MinVersion = "1.4.1654.1"
)


function NeedsUpdate($version)
{
    if ($version -eq $MinVersion)
    {
        return $false
    }

    $lessThan = $true;
    $versionParts = $version -split '\.';
    $minVersionParts = $MinVersion -split '\.';
    for ($i = 0; $i -lt $versionParts.Length; $i++)
    {
        if ([int]$versionParts[$i] -gt [int]$minVersionParts[$i])
        {
            $lessThan = $false;
            break;
        }
    }

    return $lessThan
}

Write-Host "Scanning all VMs in the subscription: $($SubID)"
Select-AzSubscription -SubscriptionId $SubID;
$vms = Get-AzVM;
$foundVMs = $false;
Write-Host "Starting VM search, this may take a while"

foreach ($vmName in $vms)
{
    # Get Detailed VM info
    $vm = Get-AzVM -ResourceGroupName $vmName.ResourceGroupName -Name $vmName.name -Status;
    $isWindows = $vm.OsVersion -match "Windows";
    foreach ($extension in $vm.Extensions)
    {
        if ($extension.Name -eq "AzureNetworkWatcherExtension")
        {
            if (NeedsUpdate($extension.TypeHandlerVersion))
            {
                $foundVMs = $true;
                if (-not ($NoUpdate))
                {
                    Write-Host "Found VM that needs to be updated: subscriptions/$($SubID)/resourceGroups/$($vm.ResourceGroupName)/providers/Microsoft.Compute/virtualMachines/$($vm.Name) -> Updating " -NoNewline
                    Remove-AzVMExtension -ResourceGroupName $vm.ResourceGroupName -VMName $vm.Name -Name "AzureNetworkWatcherExtension" -Force
                    Write-Host "... " -NoNewline
                    $type = if ($isWindows) { "NetworkWatcherAgentWindows" } else { "NetworkWatcherAgentLinux" };
                    Set-AzVMExtension -ResourceGroupName $vm.ResourceGroupName -Location $vmName.Location -VMName $vm.Name -Name "AzureNetworkWatcherExtension" -Publisher "Microsoft.Azure.NetworkWatcher" -Type $type -typeHandlerVersion "1.4"
                    Write-Host "Done"
                }
                else
                {
                    Write-Host "Found $(if ($isWindows) {"Windows"} else {"Linux"}) VM that needs to be updated: subscriptions/$($SubID)/resourceGroups/$($vm.ResourceGroupName)/providers/Microsoft.Compute/virtualMachines/$($vm.Name)"
                }
            }
        }
    }
}

if ($foundVMs)
{
    Write-Host "Finished $(if ($NoUpdate) {"searching"} else {"updating"}) out of date AzureNetworkWatcherExtension on VMs"
}
else
{
    Write-Host "All AzureNetworkWatcherExtensions up to date"
}

```

## <a name="update-your-extension-manually"></a>Uzantınızı el ile güncelleştirin

Uzantınızı güncelleştirmek için uzantı sürümünüzü bilmeniz gerekir.

### <a name="check-your-extension-version"></a>Uzantı sürümünüzü denetleyin

Azure portal, Azure CLı veya PowerShell 'i kullanarak uzantı sürümünüzü kontrol edebilirsiniz.

#### <a name="usetheazureportal"></a>Azure portal kullanın

1. Azure portal sanal makinenizin **Uzantılar** bölmesine gidin.
1. Ayrıntılar bölmesini görmek için **Azurenetworkizleyici** uzantısını seçin.
1. **Sürüm alanındaki sürüm** numarasını bulun.  

#### <a name="use-the-azure-cli"></a>Azure CLI kullanma

Bir Azure CLı isteminde aşağıdaki komutu çalıştırın:

```azurecli
az vm get-instance-view --resource-group  "SampleRG" --name "Sample-VM"
```
Çıktıda **"AzureNetworkWatcherExtension"** konumunu bulun ve çıktıda *"typehandlerversion"* alanından sürüm numarasını belirleyin.  Lütfen unutmayın: uzantı hakkındaki bilgiler JSON çıktısında birden çok kez görünür. Lütfen "Uzantılar" bloğunun altına bakın ve uzantının tam sürüm numarasını görmeniz gerekir. 

Aşağıdaki gibi bir şey görmeniz gerekir: ![ Azure CLI ekran görüntüsü](./media/network-watcher/azure-cli-screenshot.png)

#### <a name="usepowershell"></a>PowerShell 'i kullanma

Bir PowerShell isteminden aşağıdaki komutları çalıştırın:

```powershell
Get-AzVM -ResourceGroupName "SampleRG" -Name "Sample-VM" -Status
```
Çıktıda Azure ağ Izleyicisi uzantısını bulun ve çıktıda *"Typehandlerversion"* alanından sürüm numarasını belirleyin.   

Aşağıdakine benzer bir şey görmeniz gerekir: ![ PowerShell ekran görüntüsü](./media/network-watcher/powershell-screenshot.png)

### <a name="update-your-extension"></a>Uzantınızı güncelleştirme

Sürümünüz yukarıda belirtilen en son sürümün altındaysa, aşağıdaki seçeneklerden birini kullanarak uzantınızı güncelleştirin.

#### <a name="option-1-use-powershell"></a>Seçenek 1: PowerShell kullanma

Aşağıdaki komutları çalıştırın:

```powershell
#Linux command
Set-AzVMExtension -ResourceGroupName "myResourceGroup1" -Location "WestUS" -VMName "myVM1" -Name "AzureNetworkWatcherExtension" -Publisher "Microsoft.Azure.NetworkWatcher" -Type "NetworkWatcherAgentLinux"

#Windows command
Set-AzVMExtension -ResourceGroupName "myResourceGroup1" -Location "WestUS" -VMName "myVM1" -Name "NetworkWatcherAgentWindows" -Publisher "Microsoft.Azure.NetworkWatcher" -Type "NetworkWatcherAgentWindows" -ForceRerun "True"

```

Bu işe yaramazsa. Aşağıdaki adımları kullanarak uzantıyı kaldırın ve yeniden yüklemeyi deneyin. Bu, en son sürümü otomatik olarak ekler.

Uzantı kaldırılıyor 

```powershell
#Same command for Linux and Windows
Remove-AzVMExtension -ResourceGroupName "SampleRG" -VMName "Sample-VM" -Name "AzureNetworkWatcherExtension"
``` 

Uzantıyı yeniden yükleme

```powershell
#Linux command
Set-AzVMExtension -ResourceGroupName "SampleRG" -Location "centralus" -VMName "Sample-VM" -Name "AzureNetworkWatcherExtension" -Publisher "Microsoft.Azure.NetworkWatcher" -Type "NetworkWatcherAgentLinux" -typeHandlerVersion "1.4"

#Windows command
Set-AzVMExtension -ResourceGroupName "SampleRG" -Location "centralus" -VMName "Sample-VM" -Name "AzureNetworkWatcherExtension" -Publisher "Microsoft.Azure.NetworkWatcher" -Type "NetworkWatcherAgentWindows" -typeHandlerVersion "1.4"
```

#### <a name="option-2-use-the-azure-cli"></a>2. seçenek: Azure CLı 'yi kullanma

Yükseltmeye zorlayın.

```azurecli
#Linux command
az vm extension set --resource-group "myResourceGroup1" --vm-name "myVM1" --name "NetworkWatcherAgentLinux" --publisher "Microsoft.Azure.NetworkWatcher" --force-update

#Windows command
az vm extension set --resource-group "myResourceGroup1" --vm-name "myVM1" --name "NetworkWatcherAgentWindows" --publisher "Microsoft.Azure.NetworkWatcher" --force-update
```

Bu işe yaramazsa, uzantıyı yeniden kaldırın ve en son sürümü otomatik olarak eklemek için aşağıdaki adımları izleyin.

Uzantıyı kaldırın.

```azurecli
#Same for Linux and Windows
az vm extension delete --resource-group "myResourceGroup1" --vm-name "myVM1" -n "AzureNetworkWatcherExtension"

```

Uzantıyı yeniden yükler.

```azurecli
#Linux command
az vm extension set --resource-group "DALANDEMO" --vm-name "Linux-01" --name "NetworkWatcherAgentLinux" --publisher "Microsoft.Azure.NetworkWatcher"

#Windows command
az vm extension set --resource-group "DALANDEMO" --vm-name "Linux-01" --name "NetworkWatcherAgentWindows" --publisher "Microsoft.Azure.NetworkWatcher"

```

#### <a name="option-3-reboot-your-vms"></a>Seçenek 3: VM 'lerinizi yeniden başlatın

Ağ Izleyicisi uzantısı için otomatik yükseltmeyi doğru olarak ayarlarsanız, VM yüklemenizi en son uzantıya yeniden başlatın.

## <a name="support"></a>Destek

Bu makalenin herhangi bir noktasında daha fazla yardıma ihtiyacınız varsa [Linux](./network-watcher-linux.md) veya [Windows](./network-watcher-windows.md)için ağ izleyicisi uzantısı belgelerine bakın. Ayrıca [MSDN Azure ve Stack Overflow forumlarında](https://azure.microsoft.com/support/forums/)Azure uzmanlarıyla da iletişime geçin. Alternatif olarak, bir Azure destek olayı dosyası. [Azure destek sitesine](https://azure.microsoft.com/support/options/)gidin ve **Destek Al**' ı seçin. Azure desteğini kullanma hakkında daha fazla bilgi için, [Microsoft Azure support SSS](https://azure.microsoft.com/support/faq/)makalesini okuyun.
