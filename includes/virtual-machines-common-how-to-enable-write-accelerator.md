---
title: include dosyası
description: include dosyası
services: virtual-machines
author: msraiye
ms.service: virtual-machines
ms.topic: include
ms.date: 05/23/2019
ms.author: raiye
ms.custom: include file
ms.openlocfilehash: c8e0bb50e14467d2950d97da660fc8e6fa176b99
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74008920"
---
Yazma Hızlandırıcısı, yalnızca Azure yönetilen diskleri olan Premium depolamada bulunan, a serisi sanal makineler (VM) için bir disk özelliğidir. Ad olduğu gibi, işlevselliğin amacı, Azure Premium depolamada yazma işlemleri için g/ç gecikmesini geliştirmedir. Yazma Hızlandırıcısı, günlük dosyası güncelleştirmelerinin modern veritabanları için yüksek performanslı bir şekilde diskte kalıcı hale getirilmesi için gerekli olduğu idealdir.

Yazma Hızlandırıcısı genel buluttaki d serisi VM 'Ler için genel kullanıma sunulmuştur.

## <a name="planning-for-using-write-accelerator"></a>Yazma Hızlandırıcısı kullanımı için planlama yapma

Yazma Hızlandırıcısı, işlem günlüğünü içeren birimler veya bir DBMS 'nin günlüklerini yinelemek için kullanılmalıdır. Özellik günlük disklerinde kullanılmak üzere iyileştirildiğinden, bir DBMS veri birimlerinde Yazma Hızlandırıcısı kullanılması önerilmez.

Yazma Hızlandırıcısı yalnızca [Azure yönetilen disklerle](https://azure.microsoft.com/services/managed-disks/)birlikte çalışmaktadır.

> [!IMPORTANT]
> VM 'nin işletim sistemi diski için Yazma Hızlandırıcısı etkinleştirilmesi VM 'yi yeniden başlatır.
>
> Windows disk veya birim yöneticileri, Windows depolama alanları, Windows genişleme dosya sunucusu (SOFS), Linux LVM veya MDADDM ile birden çok diskin parçası olmayan mevcut bir Azure diskine Yazma Hızlandırıcısı etkinleştirmek için, Azure diskine erişen iş yükü kapatılması gerekiyor. Azure diskini kullanan veritabanı uygulamalarının kapatılması gerekır.
>
> Birden çok Azure Premium depolama diskinden oluşan ve Windows disk veya toplu Yöneticiler, Windows depolama alanları, Windows genişleme dosya sunucusu (SOFS), Linux LVM veya MDADDM kullanılarak şeritli mevcut bir birim için Yazma Hızlandırıcısı etkinleştirmek veya devre dışı bırakmak istiyorsanız, tümü birimi oluşturma disklerinin ayrı adımlarda Yazma Hızlandırıcısı için etkinleştirilmesi veya devre dışı bırakılması gerekir. **Bu tür bir yapılandırmada yazma Hızlandırıcısı etkinleştirmeden veya devre dışı bırakmadan önce, Azure VM 'yi kapatın**.

İşletim sistemi diskleri için Yazma Hızlandırıcısı etkinleştirme, SAP ile ilgili VM yapılandırmalarında gerekli değildir.

### <a name="restrictions-when-using-write-accelerator"></a>Yazma Hızlandırıcısı kullanırken kısıtlamalar

Azure diski/VHD için Yazma Hızlandırıcısı kullanırken, bu kısıtlamalar geçerlidir:

- Premium disk önbelleğinin ' none ' veya ' Read Only ' olarak ayarlanması gerekir. Diğer tüm önbelleğe alma modları desteklenmez.
- Anlık görüntü, Yazma Hızlandırıcısı etkinleştirilmiş diskler için şu anda desteklenmiyor. Yedekleme sırasında Azure Backup hizmeti, sanal makineye bağlı Yazma Hızlandırıcısı etkin diskleri otomatik olarak dışlar.
- Yalnızca küçük g/ç boyutları (< = 512 KiB) hızlandırılmış yolu alırken. Verilerin toplu olarak yüklendiği veya farklı DBMS 'nin işlem günlüğü arabelleklerinin depolama alanına kalıcı olmadan önce daha büyük bir dereceye doldurulduğu iş yükü durumlarında, diske yazılan g/ç 'nin hızlandırılmış yolu kaplamamasından faydalanıyor olun.

VM başına Yazma Hızlandırıcısı tarafından desteklenebilir Azure Premium Depolama VHD 'lerinin limitleri vardır. Geçerli sınırlar şunlardır:

| VM SKU | Yazma Hızlandırıcısı disk sayısı | VM başına Yazma Hızlandırıcısı disk ıOPS 'si |
| --- | --- | --- |
| M208ms_v2, M208s_v2| 8 | 10000 |
| M128ms, 128s | 16 | 20000 |
| M64ms, M64ls, M64s | 8 | 10000 |
| M32ms, M32ls, M32ts, M32s | 4 | 5000 |
| M16ms, M16s | 2 | 2500 |
| M8ms, M8s | 1 | 1250 |

IOPS sınırları, sanal makine başına değil, disk başına *değildir* . Tüm Yazma Hızlandırıcısı diskler VM başına aynı ıOPS sınırını paylaşır.

## <a name="enabling-write-accelerator-on-a-specific-disk"></a>Belirli bir diskte Yazma Hızlandırıcısı etkinleştirme

Sonraki birkaç bölümde Yazma Hızlandırıcısı Azure Premium Depolama VHD 'lerde nasıl etkinleştirilecektir.

### <a name="prerequisites"></a>Önkoşullar

Aşağıdaki Önkoşullar zaman içinde Yazma Hızlandırıcısı kullanımı için geçerlidir:

- Azure Yazma Hızlandırıcısı uygulamak istediğiniz disklerin Premium depolamada [Azure yönetilen diskleri](https://azure.microsoft.com/services/managed-disks/) olması gerekir.
- Bir d serisi VM kullanmanız gerekir

## <a name="enabling-azure-write-accelerator-using-azure-powershell"></a>Azure PowerShell kullanarak Azure Yazma Hızlandırıcısı etkinleştirme

5\.5.0 sürümündeki Azure Power Shell modülü, belirli Azure Premium Depolama disklerinde Yazma Hızlandırıcısı etkinleştirmek veya devre dışı bırakmak için ilgili cmdlet 'lerde yapılan değişiklikleri içerir.
Yazma Hızlandırıcısı tarafından desteklenen diskleri etkinleştirmek veya dağıtmak için, aşağıdaki güç kabuğu komutları değişmiştir ve Yazma Hızlandırıcısı bir parametreyi kabul edecek şekilde genişletilir.

Yeni bir switch parametresi, **-writeaccelerator** aşağıdaki cmdlet 'lere eklenmiştir:

- [Set-AzVMOsDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmosdisk?view=azurermps-6.0.0)
- [Add-AzVMDataDisk](https://docs.microsoft.com/powershell/module/az.compute/Add-AzVMDataDisk?view=azurermps-6.0.0)
- [Set-AzVMDataDisk](https://docs.microsoft.com/powershell/module/az.compute/Set-AzVMDataDisk?view=azurermps-6.0.0)
- [Add-AzVmssDataDisk](https://docs.microsoft.com/powershell/module/az.compute/Add-AzVmssDataDisk?view=azurermps-6.0.0)

Parametresi verme özelliği false olarak ayarlanır ve Yazma Hızlandırıcısı tarafından desteklenmeyen diskleri dağıtır.

Yeni bir switch parametresi, **-osdiskwriteaccelerator** aşağıdaki cmdlet 'lere eklenmiştir:

- [Set-AzVmssStorageProfile](https://docs.microsoft.com/powershell/module/az.compute/Set-AzVmssStorageProfile?view=azurermps-6.0.0)

Parametresi belirtilmeden, varsayılan olarak, Yazma Hızlandırıcısı kullanmayan diskler döndüren özelliği false olarak ayarlar.

Yeni bir isteğe bağlı Boole (null yapılamayan) parametresi, **-osdiskwriteaccelerator** aşağıdaki cmdlet 'lere eklenmiştir:

- [Güncelleştirme-AzVM](https://docs.microsoft.com/powershell/module/az.compute/Update-AzVM?view=azurermps-6.0.0)
- [Güncelleştirme-AzVmss](https://docs.microsoft.com/powershell/module/az.compute/Update-AzVmss?view=azurermps-6.0.0)

Diskler ile Azure Yazma Hızlandırıcısı desteğini denetlemek için $true ya da $false belirtin.

Komut örnekleri şöyle görünebilir:

```powershell
New-AzVMConfig | Set-AzVMOsDisk | Add-AzVMDataDisk -Name "datadisk1" | Add-AzVMDataDisk -Name "logdisk1" -WriteAccelerator | New-AzVM

Get-AzVM | Update-AzVM -OsDiskWriteAccelerator $true

New-AzVmssConfig | Set-AzVmssStorageProfile -OsDiskWriteAccelerator | Add-AzVmssDataDisk -Name "datadisk1" -WriteAccelerator:$false | Add-AzVmssDataDisk -Name "logdisk1" -WriteAccelerator | New-AzVmss

Get-AzVmss | Update-AzVmss -OsDiskWriteAccelerator:$false
```

Aşağıdaki bölümlerde gösterildiği gibi iki ana senaryo komut dosyası oluşturulabilir.

### <a name="adding-a-new-disk-supported-by-write-accelerator-using-powershell"></a>PowerShell kullanarak Yazma Hızlandırıcısı tarafından desteklenen yeni bir disk ekleme

Bu betiği, sanal makinenize yeni bir disk eklemek için kullanabilirsiniz. Bu komut dosyasıyla oluşturulan Disk Yazma Hızlandırıcısı kullanır.

`myVM`, `myWAVMs`, `log001`, diskin boyutunu ve belirli dağıtımınız için uygun değerlerle diskin yarım kimliğini değiştirin.

```powershell
# Specify your VM Name
$vmName="myVM"
#Specify your Resource Group
$rgName = "myWAVMs"
#data disk name
$datadiskname = "log001"
#LUN Id
$lunid=8
#size
$size=1023
#Pulls the VM info for later
$vm=Get-AzVM -ResourceGroupName $rgname -Name $vmname
#add a new VM data disk
Add-AzVMDataDisk -CreateOption empty -DiskSizeInGB $size -Name $vmname-$datadiskname -VM $vm -Caching None -WriteAccelerator:$true -lun $lunid
#Updates the VM with the disk config - does not require a reboot
Update-AzVM -ResourceGroupName $rgname -VM $vm
```

### <a name="enabling-write-accelerator-on-an-existing-azure-disk-using-powershell"></a>PowerShell kullanarak var olan bir Azure diskinde Yazma Hızlandırıcısı etkinleştirme

Bu betiği, mevcut bir diskte Yazma Hızlandırıcısı etkinleştirmek için kullanabilirsiniz. `myVM`, `myWAVMs`ve `test-log001`, belirli dağıtımınız için uygun değerlerle değiştirin. Betik, **$newStatus** değerinin ' $true ' olarak ayarlandığı mevcut bir diske yazma Hızlandırıcısı ekler. ' $False ' değerini kullanmak belirli bir diskte Yazma Hızlandırıcısı devre dışı bırakacak.

```powershell
#Specify your VM Name
$vmName="myVM"
#Specify your Resource Group
$rgName = "myWAVMs"
#data disk name
$datadiskname = "test-log001" 
#new Write Accelerator status ($true for enabled, $false for disabled) 
$newstatus = $true
#Pulls the VM info for later
$vm=Get-AzVM -ResourceGroupName $rgname -Name $vmname
#add a new VM data disk
Set-AzVMDataDisk -VM $vm -Name $datadiskname -Caching None -WriteAccelerator:$newstatus
#Updates the VM with the disk config - does not require a reboot
Update-AzVM -ResourceGroupName $rgname -VM $vm
```

> [!Note]
> Yukarıdaki betiği yürütmek, belirtilen diski ayıracaktır, diske karşı Yazma Hızlandırıcısı etkinleştirir ve sonra diski tekrar ekler

## <a name="enabling-write-accelerator-using-the-azure-portal"></a>Azure portal kullanarak Yazma Hızlandırıcısı etkinleştirme

Yazma Hızlandırıcısı, disk önbelleğe alma ayarlarınızı belirttiğiniz Portal aracılığıyla etkinleştirebilirsiniz:

![Azure portal Yazma Hızlandırıcısı](./media/virtual-machines-common-how-to-enable-write-accelerator/wa_scrnsht.png)

## <a name="enabling-write-accelerator-using-the-azure-cli"></a>Azure CLı kullanarak Yazma Hızlandırıcısı etkinleştirme

Yazma Hızlandırıcısı sağlamak için [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) kullanabilirsiniz.

Mevcut bir diskte Yazma Hızlandırıcısı etkinleştirmek için [az VM Update](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-update)kullanın, DiskName, VMName ve ResourceGroup değerlerini kendi değerlerinizle değiştirmeniz durumunda aşağıdaki örnekleri kullanabilirsiniz: `az vm update -g group1 -n vm1 -write-accelerator 1=true`

Yazma Hızlandırıcısı etkin bir disk eklemek için [az VM disk Attach](https://docs.microsoft.com/cli/azure/vm/disk?view=azure-cli-latest#az-vm-disk-attach)kullanın, kendi değerlerinizi yerine koymak için aşağıdaki örneği kullanabilirsiniz: `az vm disk attach -g group1 -vm-name vm1 -disk d1 --enable-write-accelerator`

Yazma Hızlandırıcısı devre dışı bırakmak için [az VM Update](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-update)kullanın, özellikleri false olarak ayarlar: `az vm update -g group1 -n vm1 -write-accelerator 0=false 1=false`

## <a name="enabling-write-accelerator-using-rest-apis"></a>REST API 'Leri kullanarak Yazma Hızlandırıcısı etkinleştirme

Azure REST API aracılığıyla dağıtmak için Azure armclient ' ı yüklemeniz gerekir.

### <a name="install-armclient"></a>Armclient 'ı yükler

Armclient 'ı çalıştırmak için, Chocolatey aracılığıyla yüklemeniz gerekir. Cmd. exe veya PowerShell aracılığıyla yükleyebilirsiniz. Bu komutlar için yükseltilmiş hakları kullanın ("yönetici olarak çalıştır").

Cmd. exe ' yi kullanarak şu komutu çalıştırın: `@"%SystemRoot%\System32\WindowsPowerShell\v1.0\powershell.exe" -NoProfile -InputFormat None -ExecutionPolicy Bypass -Command "iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))" && SET "PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin"`

Power Shell 'i kullanarak şu komutu çalıştırın: `Set-ExecutionPolicy Bypass -Scope Process -Force; iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))`

Artık, cmd. exe veya PowerShell 'de aşağıdaki komutu kullanarak armclient 'ı yükleyebilirsiniz `choco install armclient`

### <a name="getting-your-current-vm-configuration"></a>Geçerli VM yapılandırmanızı alma

Disk yapılandırmanızın özniteliklerini değiştirmek için, önce geçerli yapılandırmayı bir JSON dosyasında almanız gerekir. Şu komutu yürüterek geçerli yapılandırmayı edinebilirsiniz: `armclient GET /subscriptions/<<subscription-ID<</resourceGroups/<<ResourceGroup>>/providers/Microsoft.Compute/virtualMachines/<<virtualmachinename>>?api-version=2017-12-01 > <<filename.json>>`

' < < > > ' İçindeki terimleri JSON dosyasının sahip olduğu dosya adı da dahil olmak üzere verilerle değiştirin.

Çıktı şöyle görünebilir:

```JSON
{
  "properties": {
    "vmId": "2444c93e-f8bb-4a20-af2d-1658d9dbbbcb",
    "hardwareProfile": {
      "vmSize": "Standard_M64s"
    },
    "storageProfile": {
      "imageReference": {
        "publisher": "SUSE",
        "offer": "SLES-SAP",
        "sku": "12-SP3",
        "version": "latest"
      },
      "osDisk": {
        "osType": "Linux",
        "name": "mylittlesap_OsDisk_1_754a1b8bb390468e9b4c429b81cc5f5a",
        "createOption": "FromImage",
        "caching": "ReadWrite",
        "managedDisk": {
          "storageAccountType": "Premium_LRS",
          "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/mylittlesap_OsDisk_1_754a1b8bb390468e9b4c429b81cc5f5a"
        },
        "diskSizeGB": 30
      },
      "dataDisks": [
        {
          "lun": 0,
          "name": "data1",
          "createOption": "Attach",
          "caching": "None",
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data1"
          },
          "diskSizeGB": 1023
        },
        {
          "lun": 1,
          "name": "log1",
          "createOption": "Attach",
          "caching": "None",
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data2"
          },
          "diskSizeGB": 1023
        }
      ]
    },
    "osProfile": {
      "computerName": "mylittlesapVM",
      "adminUsername": "pl",
      "linuxConfiguration": {
        "disablePasswordAuthentication": false
      },
      "secrets": []
    },
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Network/networkInterfaces/mylittlesap518"
        }
      ]
    },
    "diagnosticsProfile": {
      "bootDiagnostics": {
        "enabled": true,
        "storageUri": "https://mylittlesapdiag895.blob.core.windows.net/"
      }
    },
    "provisioningState": "Succeeded"
  },
  "type": "Microsoft.Compute/virtualMachines",
  "location": "westeurope",
  "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/virtualMachines/mylittlesapVM",
  "name": "mylittlesapVM"

```

Sonra, JSON dosyasını güncelleştirin ve ' log1 ' adlı diskte Yazma Hızlandırıcısı etkinleştirin. Bu, diskin önbellek girişinden sonra bu öznitelik JSON dosyasına eklenerek gerçekleştirilebilir.

```JSON
        {
          "lun": 1,
          "name": "log1",
          "createOption": "Attach",
          "caching": "None",
          "writeAcceleratorEnabled": true,
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data2"
          },
          "diskSizeGB": 1023
        }
```

Sonra mevcut dağıtımı şu komutla güncelleştirin: `armclient PUT /subscriptions/<<subscription-ID<</resourceGroups/<<ResourceGroup>>/providers/Microsoft.Compute/virtualMachines/<<virtualmachinename>>?api-version=2017-12-01 @<<filename.json>>`

Çıktı aşağıdaki gibi görünmelidir. Yazma Hızlandırıcısı bir disk için etkin olduğunu görebilirsiniz.

```JSON
{
  "properties": {
    "vmId": "2444c93e-f8bb-4a20-af2d-1658d9dbbbcb",
    "hardwareProfile": {
      "vmSize": "Standard_M64s"
    },
    "storageProfile": {
      "imageReference": {
        "publisher": "SUSE",
        "offer": "SLES-SAP",
        "sku": "12-SP3",
        "version": "latest"
      },
      "osDisk": {
        "osType": "Linux",
        "name": "mylittlesap_OsDisk_1_754a1b8bb390468e9b4c429b81cc5f5a",
        "createOption": "FromImage",
        "caching": "ReadWrite",
        "managedDisk": {
          "storageAccountType": "Premium_LRS",
          "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/mylittlesap_OsDisk_1_754a1b8bb390468e9b4c429b81cc5f5a"
        },
        "diskSizeGB": 30
      },
      "dataDisks": [
        {
          "lun": 0,
          "name": "data1",
          "createOption": "Attach",
          "caching": "None",
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data1"
          },
          "diskSizeGB": 1023
        },
        {
          "lun": 1,
          "name": "log1",
          "createOption": "Attach",
          "caching": "None",
          "writeAcceleratorEnabled": true,
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data2"
          },
          "diskSizeGB": 1023
        }
      ]
    },
    "osProfile": {
      "computerName": "mylittlesapVM",
      "adminUsername": "pl",
      "linuxConfiguration": {
        "disablePasswordAuthentication": false
      },
      "secrets": []
    },
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Network/networkInterfaces/mylittlesap518"
        }
      ]
    },
    "diagnosticsProfile": {
      "bootDiagnostics": {
        "enabled": true,
        "storageUri": "https://mylittlesapdiag895.blob.core.windows.net/"
      }
    },
    "provisioningState": "Succeeded"
  },
  "type": "Microsoft.Compute/virtualMachines",
  "location": "westeurope",
  "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/virtualMachines/mylittlesapVM",
  "name": "mylittlesapVM"
```

Bu değişikliği yaptıktan sonra sürücü, Yazma Hızlandırıcısı tarafından desteklenmelidir.
