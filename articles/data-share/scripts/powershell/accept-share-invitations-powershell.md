---
title: 'PowerShell betiği: Azure veri paylaşımından daveti kabul etme | Microsoft Docs'
description: Bu PowerShell betiği, var olan bir veri paylaşımından davetleri kabul eder.
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: 246e3550650dfd458b4aeecda3b5b7733e49f017
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70307340"
---
# <a name="use-powershell-to-accept-a-data-share-invitation"></a>PowerShell kullanarak veri paylaşma davetini kabul etme

Bu PowerShell betiği bir tüketiciye gönderilen davetleri kabul eder.

## <a name="sample-script"></a>Örnek betik
```powershell
#List invitations sent to a consumer
Get-AzDataShareInvitation

# Set variables with your own values
$resourceGroupName = "<Resource group name>"
$dataShareAccountName = "<Data share account name>"
$dataShareName = "<Data share name>"
$invitationId = "<Invitation id>"

#Accept a specific invitation by creating a share subscription
New-AzDataShareSubscription -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -Name $dataShareName -InvitationId $invitationId

```

## <a name="script-explanation"></a>Betik açıklaması

Bu betik şu komutları kullanır: 

| Komut | Notlar |
|---|---|
| [Get-Azdatasharedavetini](/powershell/module/az.datashare/get-azdatashareinvitation?view=azps-2.6.0) | Gönderilen veri paylaşma davetlerini alın ve listeleyin. |
| [New-AzDataShareSubscription](/powershell/module/az.datashare/get-azdatasharesubscription?view=azps-2.6.0) | Veri paylaşma aboneliği oluşturun. |
|||

## <a name="next-steps"></a>Sonraki adımlar

Azure PowerShell hakkında daha fazla bilgi için bkz. [Azure PowerShell belgeleri](https://docs.microsoft.com/powershell/).

Ek Azure veri paylaşımının PowerShell betiği örnekleri, [Azure veri paylaşımında PowerShell örnekleri](../../samples-powershell.md)bölümünde bulunabilir.

