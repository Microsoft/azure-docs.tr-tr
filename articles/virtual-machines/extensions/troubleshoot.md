---
title: Windows VM Uzantısı hatalarında sorun giderme
description: Azure Windows VM Uzantısı hatalarında sorun giderme hakkında bilgi edinin
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: windows
ms.date: 03/29/2016
ms.openlocfilehash: 8cc8a0b5ae0a83a152168b14a2c5577f8f7b48ab
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102564807"
---
# <a name="troubleshooting-azure-windows-vm-extension-failures"></a>Azure Windows VM Uzantısı hatalarında sorun giderme
[!INCLUDE [virtual-machines-common-extensions-troubleshoot](../../../includes/virtual-machines-common-extensions-troubleshoot.md)]

## <a name="viewing-extension-status"></a>Uzantı durumunu görüntüleme
Azure Resource Manager Şablonlar Azure PowerShell yürütülebilir. Şablon yürütüldüğünde, uzantı durumu Azure Kaynak Gezgini veya komut satırı araçlarından görüntülenebilir.

Aşağıda bir örnek verilmiştir:

Azure PowerShell:

```azurepowershell
Get-AzVM -ResourceGroupName $RGName -Name $vmName -Status
```

Örnek çıktı aşağıdaki gibidir:

```output
Extensions:  {
  "ExtensionType": "Microsoft.Compute.CustomScriptExtension",
  "Name": "myCustomScriptExtension",
  "SubStatuses": [
    {
      "Code": "ComponentStatus/StdOut/succeeded",
      "DisplayStatus": "Provisioning succeeded",
      "Level": "Info",
      "Message": "    Directory: C:\\temp\\n\\n\\nMode                LastWriteTime     Length Name
          \\n----                -------------     ------ ----                              \\n-a---          9/1/2015   2:03 AM         11
          test.txt                          \\n\\n",
                  "Time": null
      },
    {
      "Code": "ComponentStatus/StdErr/succeeded",
      "DisplayStatus": "Provisioning succeeded",
      "Level": "Info",
      "Message": "",
      "Time": null
    }
  ]
}
```

## <a name="troubleshooting-extension-failures"></a>Uzantı hatalarında sorun giderme

### <a name="rerun-the-extension-on-the-vm"></a>Uzantıyı VM 'de yeniden çalıştırın
Özel Betik uzantısı kullanarak VM 'de komut dosyaları çalıştırıyorsanız, bazen VM 'nin başarıyla oluşturulduğu ancak betiğin başarısız olduğu bir hata ile çalışabilir. Bu koşullar altında, bu hatayı kurtarmak için önerilen yol, uzantıyı kaldırmak ve şablonu yeniden çalıştıramaktır.
Note: gelecekte, uzantıyı kaldırma gereksinimini ortadan kaldırmak için bu işlev geliştirilir.

#### <a name="remove-the-extension-from-azure-powershell"></a>Uzantıyı Azure PowerShell kaldır
```azurepowershell
Remove-AzVMExtension -ResourceGroupName $RGName -VMName $vmName -Name "myCustomScriptExtension"
```

Uzantı kaldırıldıktan sonra, şablon, betikleri sanal makinede çalıştırmak için yeniden çalıştırılabilir.

### <a name="trigger-a-new-goalstate-to-the-vm"></a>VM 'ye yeni bir GoalState tetikleme
Bir uzantının çalıştırılmadığını veya eksik bir "Windows Azure CRP sertifika Oluşturucusu" (Bu sertifika, uzantının korunan ayarlarının aktarımını korumak için kullanılır) nedeniyle yürütülebileceğini fark edebilirsiniz.
Bu sertifika, sanal makinenin içinden Windows Konuk Aracısı yeniden başlatılarak otomatik olarak yeniden oluşturulur:
- Görev Yöneticisi 'Ni açın
- Ayrıntılar sekmesine gidin
- WindowsAzureGuestAgent.exe işlemini bulun
- Sağ tıklayın ve "Görevi Sonlandır" seçeneğini belirleyin. İşlem otomatik olarak yeniden başlatılacak


Ayrıca, bir "VM yeniden uygula" yürüterek sanal makineye yeni bir GoalState tetikleyebilirsiniz. VM yeniden [uygulama](/rest/api/compute/virtualmachines/reapply) , bir VM 'nin durumunu yeniden uygulamak için 2020 ' de TANıTıLAN bir API 'dir. Kısa bir VM kapalı kalma süresini kabul edebilmeniz için bunu tek seferde yapmanızı öneririz. Yeniden Uygula işlemi, VM 'nin yeniden başlatılmasına neden olmaz ve yeniden uygula 'yı çağırmanın büyük çoğunluğu sanal makineyi yeniden başlatmaz, VM modeline yönelik başka bir bekleyen güncelleştirme, yeni bir hedef durumu tetikler ve diğer değişikliğin yeniden başlatma gerektirebilecekleri çok küçük bir risk vardır. 

Azure portal:

Portalda VM ' yi seçin ve sol bölmedeki **destek + sorun giderme**' yi seçin, yeniden **Dağıt**' ı ve ardından **Yeniden Uygula ' yı seçin.**


Azure PowerShell *(RG adı ve VM adını değerlerinizle değiştirin)*:

```azurepowershell
Set-AzVM -ResourceGroupName <RG Name> -Name <VM Name> -Reapply
```

Azure CLı *(RG adı ve VM adını değerlerinizle değiştirin)*:

```azurecli
az vm reapply -g <RG Name> -n <VM Name>
```

Bir "sanal makine yeniden uygula" işlemi işe yaramazsa, Azure Yönetim Portalı sanal makinesine yeni bir boş veri diski ekleyebilir ve ardından sertifika geri eklendikten sonra bunu kaldırabilirsiniz.