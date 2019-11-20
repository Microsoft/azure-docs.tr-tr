---
title: Windows VM Uzantısı hatalarında sorun giderme
description: Azure Windows VM Uzantısı hatalarında sorun giderme hakkında bilgi edinin
services: virtual-machines-windows
documentationcenter: ''
author: kundanap
manager: gwallace
editor: ''
tags: top-support-issue,azure-resource-manager
ms.assetid: 878ab9b6-c3e6-40be-82d4-d77fecd5030f
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/29/2016
ms.author: kundanap
ms.openlocfilehash: bc99a9c9e9ff985730ec97dbacd1d7c1de06a45e
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74073647"
---
# <a name="troubleshooting-azure-windows-vm-extension-failures"></a>Azure Windows VM Uzantısı hatalarında sorun giderme
[!INCLUDE [virtual-machines-common-extensions-troubleshoot](../../../includes/virtual-machines-common-extensions-troubleshoot.md)]

## <a name="viewing-extension-status"></a>Uzantı durumunu görüntüleme
Azure Resource Manager Şablonlar Azure PowerShell yürütülebilir. Şablon yürütüldüğünde, uzantı durumu Azure Kaynak Gezgini veya komut satırı araçlarından görüntülenebilir.

Örnek aşağıda verilmiştir:

Azure PowerShell:

      Get-AzVM -ResourceGroupName $RGName -Name $vmName -Status

Örnek çıktı aşağıdaki gibidir:

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
    }
  ]

## <a name="troubleshooting-extension-failures"></a>Uzantı hatalarında sorun giderme
### <a name="rerun-the-extension-on-the-vm"></a>Uzantıyı VM 'de yeniden çalıştırın
Özel Betik uzantısı kullanarak VM 'de komut dosyaları çalıştırıyorsanız, bazen VM 'nin başarıyla oluşturulduğu ancak betiğin başarısız olduğu bir hata ile çalışabilir. Bu koşullar altında, bu hatayı kurtarmak için önerilen yol, uzantıyı kaldırmak ve şablonu yeniden çalıştıramaktır.
Note: gelecekte, uzantıyı kaldırma gereksinimini ortadan kaldırmak için bu işlev geliştirilir.

#### <a name="remove-the-extension-from-azure-powershell"></a>Uzantıyı Azure PowerShell kaldır
    Remove-AzVMExtension -ResourceGroupName $RGName -VMName $vmName -Name "myCustomScriptExtension"

Uzantı kaldırıldıktan sonra, şablon, betikleri sanal makinede çalıştırmak için yeniden çalıştırılabilir.

