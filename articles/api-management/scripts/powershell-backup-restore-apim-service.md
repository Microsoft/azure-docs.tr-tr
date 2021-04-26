---
title: Azure PowerShell Betik Örneği - Hizmet yedekleme ve geri yükleme | Microsoft Docs
description: API Management hizmet örneğini yedekleme ve geri yükleme hakkında bilgi edinin. Örnek bir betik görüntüleyin ve kullanılabilir ek kaynakları görüntüleyin.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.topic: sample
ms.date: 11/16/2017
ms.author: apimpm
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: e879c4df24e7913aac2aac09078bbf7f817ffcea
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107812615"
---
# <a name="backup-and-restore-service"></a>Hizmet yedekleme ve geri yükleme

Bu makaledeki örnek betik, API Management hizmet örneğinin nasıl yedeklenve geri yükleneceğini gösterir. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

PowerShell 'i yerel olarak yükleyip kullanmayı tercih ederseniz bu öğretici, Azure PowerShell modülü 1,0 veya sonraki bir sürümü gerektirir. Sürümü bulmak için `Get-Module -ListAvailable Az` komutunu çalıştırın. Yükseltmeniz gerekirse, bkz. [Azure PowerShell modülünü yükleme](/powershell/azure/install-Az-ps). PowerShell'i yerel olarak çalıştırıyorsanız Azure bağlantısı oluşturmak için `Connect-AzAccount` komutunu da çalıştırmanız gerekir.

## <a name="sample-script"></a>Örnek betik

[!code-powershell[main](../../../powershell_scripts/api-management/backup-restore-apim-service/backup_restore_apim_service.ps1 "Backup and restore the APIM service instance")]

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli değilse, [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) komutunu kullanarak kaynak grubunu ve tüm ilgili kaynakları kaldırabilirsiniz.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Sonraki adımlar

Azure PowerShell modülü hakkında daha fazla bilgi için bkz. [Azure PowerShell belgeleri](/powershell/azure/).

Azure API Management için ek Azure PowerShell örneklerini [PowerShell örnekleri](../powershell-samples.md) bölümünde bulabilirsiniz.
