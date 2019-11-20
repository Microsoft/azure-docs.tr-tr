---
title: Azure PowerShell Betik Örneği - Windows üzerinde AzCopy kullanarak blobları depolama hesapları arasında geçirme | Microsoft Docs
description: AzCopy kullanarak, bir Azure Depolama Hesabının Blob içeriklerini diğerine kopyalar.
services: storage
documentationcenter: na
author: normesta
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: sample
ms.date: 02/01/2018
ms.author: normesta
ms.openlocfilehash: 0af945177289760ca0d2a97133a300d9978ce702
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/25/2019
ms.locfileid: "71260635"
---
# <a name="migrate-blobs-across-storage-accounts-using-azcopy-on-windows"></a>Windows üzerinde AzCopy kullanarak blobları depolama hesapları arasında geçirme

Bu örnek kullanıcı tarafından sağlanan bir kaynak depolama hesabından tüm blob nesnelerini kullanıcı tarafından sağlanan bir hedef depolama hesabına geçirir. 

Bu bir depolama hesabındaki tüm kapsayıcıları listeleyen `Get-AzStorageContainer` komutu kullanılarak gerçekleştirilir. Örnek daha sonra AzCopy komutları vererek kaynak depolama hesabından her bir kapsayıcıyı hedef depolama hesabına taşır. Herhangi bir hata oluşursa, örnek $retryTimes kez yeniden dener (varsayılan değer 3’tür ve `-RetryTimes` parametresiyle değiştirilebilir). Her yeniden denemede hatayla karşılaşılırsa, kullanıcı `-LastSuccessContainerName` parametresini kullanarak örneğe başarılı bir şekilde kopyalanan son kapsayıcıyı sağlayarak betiği yeniden çalıştırabilir. Örnek daha sonra bu noktadan kapsayıcıları kopyalamaya devam eder.

Bu örnek Azure PowerShell depolama modülü **0,7** veya sonraki bir sürümü gerektirir. `Get-Module -ListAvailable Az.storage` kullanarak yüklü olan sürümü denetleyebilirsiniz. Yüklemeniz veya yükseltmeniz gerekirse, bkz. [Azure PowerShell Modülü yükleme](/powershell/azure/install-Az-ps). 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

Bu örnek ayrıca en son [Windows üzerinde AzCopy](https://aka.ms/downloadazcopy) sürümünü gerektirir. Varsayılan yükleme dizini: `C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\`

Bu örnek bir kaynak depolama hesabının adı ve anahtarını, bir hedef depolama hesabının adı ve anahtarını ve AzCopy.exe dosyasının tam dosya yolunu (varsayılan dizinde yüklü değilse) alır.

Aşağıda bu örnek için giriş örnekleri verilmiştir:

AzCopy varsayılan dizinde yüklüyse:
```powershell
srcStorageAccountName: ExampleSourceStorageAccountName
srcStorageAccountKey: ExampleSourceStorageAccountKey
DestStorageAccountName: ExampleTargetStorageAccountName
DestStorageAccountKey: ExampleTargetStorageAccountKey
```

AzCopy varsayılan dizinde yüklü değilse:

```Powershell
srcStorageAccountName: ExampleSourceStorageAccountName
srcStorageAccountKey: ExampleSourceStorageAccountKey
DestStorageAccountName: ExampleTargetStorageAccountName
DestStorageAccountKey: ExampleTargetStorageAccountKey
AzCopyPath: C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\AzCopy.exe
```

Bir hatayla karşılaşılırsa ve örneğin belirli bir kapsayıcıdan yeniden çalıştırılması gerekirse: 

`.\copyScript.ps1 -LastSuccessContainerName myContainerName`

## <a name="sample-script"></a>Örnek betik

[!code-powershell[main](../../../powershell_scripts/storage/migrate-blobs-between-accounts/migrate-blobs-between-accounts.ps1 "Migrate blobs between storage accounts.")]

## <a name="script-explanation"></a>Betik açıklaması

Betik bir depolama hesabından diğerine verileri kopyalamak için aşağıdaki komutları kullanır. Tablodaki her öğe, komuta özgü belgelere yönlendirir.

| Komut | Notlar |
|---|---|
| [Get-AzStorageContainer](/powershell/module/az.storage/Get-AzStorageContainer) | Bu depolama hesabıyla ilişkili kapsayıcıları döndürür. |
| [New-AzStorageContext](/powershell/module/az.storage/New-AzStorageContext) | Azure Depolama bağlamı oluşturur. |

## <a name="next-steps"></a>Sonraki adımlar

Azure PowerShell modülü hakkında daha fazla bilgi için bkz. [Azure PowerShell belgeleri](/powershell/azure/overview).

Ek depolama PowerShell betiği örnekleri, [Azure Blob depolama için Azure PowerShell örneklerinde](../blobs/storage-samples-blobs-powershell.md) bulunabilir.
