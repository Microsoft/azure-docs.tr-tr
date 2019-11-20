---
title: 'PowerShell betiği: Azure veri paylaşma daveti oluşturma | Microsoft Docs'
description: Bu PowerShell betiği bir veri paylaşma daveti gönderir.
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: 9fd8d6428e94007002d524d9ade99f6b368b8201
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70307244"
---
# <a name="use-powershell-to-monitor-the-usage-of-a-sent-data-share"></a>Gönderilen veri paylaşımının kullanımını izlemek için PowerShell 'i kullanma

Bu PowerShell betiği bir veri paylaşma daveti oluşturur.

## <a name="sample-script"></a>Örnek betik


```powershell
# Set variables with your own values
$resourceGroupName = "<Resource group name>"
$dataShareAccountName = "<Data share account name>"
$dataShareName = "<Data share name>"
$targetEmail = "<Target email>"

# Send a data share invitation
New-AzDataShareInvitation -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -ShareName $dataShareName -Name $dataShareName -TargetEmail $targetEmail

```


## <a name="script-explanation"></a>Betik açıklaması

Bu betik şu komutları kullanır: 

| Komut | Notlar |
|---|---|
| [New-Azdatasharedavetiyesi](/powershell/module/az.datashare/new-azdatashareinvitation?view=azps-2.6.0) | Veri paylaşma daveti oluşturun. |
|||

## <a name="next-steps"></a>Sonraki adımlar

Azure PowerShell hakkında daha fazla bilgi için bkz. [Azure PowerShell belgeleri](https://docs.microsoft.com/powershell/).

Ek Azure veri paylaşımının PowerShell betiği örnekleri, [Azure veri paylaşımında PowerShell örnekleri](../../samples-powershell.md)bölümünde bulunabilir.
