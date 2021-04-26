---
author: MikeRayMSFT
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 11/25/2018
ms.author: mikeray
ms.openlocfilehash: 27da28073b01c6bc43868172d6c989d9518d8f53
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95554062"
---
## <a name="start-your-powershell-session"></a>PowerShell oturumunuzu başlatma
 

[**Connect-az Account**](/powershell/module/Az.Accounts/Connect-AzAccount) cmdlet 'ini çalıştırın ve kimlik bilgilerinizi girmeniz için bir oturum açma ekranı görüntülenir. Azure portala giriş yapmak için kullandığınız kimlik bilgilerinin aynısını kullanın.

```powershell
Connect-AzAccount
```

Birden çok aboneliğiniz varsa, PowerShell oturumunuzun kullanması gereken aboneliği seçmek için [**set-AzContext**](/powershell/module/az.accounts/set-azcontext) cmdlet 'ini kullanın. Geçerli PowerShell oturumunun hangi abonelikte kullandığını görmek için [**Get-AzContext**](/powershell/module/az.accounts/get-azcontext)komutunu çalıştırın. Tüm aboneliklerinizi görmek için [**Get-AzSubscription**](/powershell/module/az.accounts/get-azsubscription)komutunu çalıştırın.

```powershell
Set-AzContext -SubscriptionId '4cac86b0-1e56-bbbb-aaaa-000000000000'
```