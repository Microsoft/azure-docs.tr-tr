---
title: Azure PowerShell betik örneği-uygulama yedeklemesini başka bir aboneliğe geri yükleme | Microsoft Docs
description: Azure PowerShell betik örneği-bir Web uygulamasını başka bir abonelikteki yedekten geri yükleme
services: app-service\web
documentationcenter: ''
author: msangapu
manager: jpconnoc
editor: ''
tags: azure-service-management
ms.assetid: a2a27d94-d378-4c17-a6a9-ae1e69dc4a72
ms.service: app-service-web
ms.workload: web
ms.topic: sample
ms.date: 11/21/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 5f293f290bd0c8b6c5546d37a43dde64a5af4f82
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70098414"
---
# <a name="restore-a-web-app-from-a-backup-in-another-subscription-using-powershell"></a>PowerShell kullanarak bir Web uygulamasını başka bir abonelikteki yedekten geri yükleme

Bu örnek betik, daha önce tamamlanmış bir yedeği var olan bir Web uygulamasından alır ve onu başka bir abonelikteki Web uygulamasına geri yükler. 

Gerekirse, [Azure PowerShell kılavuzunda](/powershell/azure/overview) bulunan yönergeleri kullanarak Azure PowerShell’i yükleyin ve ardından Azure ile bağlantı oluşturmak için `Connect-AzAccount` komutunu çalıştırın. 

## <a name="sample-script"></a>Örnek betik

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/backup-restore-diff-sub/backup-restore-diff-sub.ps1?highlight=1-6 "Restore a web app from a backup in another subscription")]

## <a name="clean-up-deployment"></a>Dağıtımı temizleme 

Artık Web uygulamasına ihtiyacınız yoksa, kaynak grubunu, Web uygulamasını ve tüm ilgili kaynakları kaldırmak için aşağıdaki komutu kullanın.

```powershell
Remove-AzResourceGroup -Name $resourceGroupName -Force
```

## <a name="script-explanation"></a>Betik açıklaması

Bu betik aşağıdaki komutları kullanır. Tablodaki her komut, komuta özgü belgelere yönlendirir.

| Komut | Notlar |
|---|---|
| [Add-AzAccount](/powershell/module/az.accounts/connect-azaccount) | Azure Resource Manager cmdlet istekleri için kullanılmak üzere kimliği doğrulanmış bir hesap ekler.  |
| [Get-AzWebAppBackupList](/powershell/module/az.websites/get-azwebappbackuplist) | Bir web uygulamasının yedekleme listesini alır. |
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | Bir Web uygulaması oluşturur |
| [Restore-AzWebAppBackup](/powershell/module/az.websites/restore-azwebappbackup) | Bir Web uygulamasını daha önce tamamlanmış bir yedekten geri yükler. |

## <a name="next-steps"></a>Sonraki adımlar

Azure PowerShell modülü hakkında daha fazla bilgi için bkz. [Azure PowerShell belgeleri](/powershell/azure/overview).

Azure App Service Web Apps için ek Azure PowerShell örneklerini [Azure PowerShell örnekleri](../samples-powershell.md) bölümünde bulabilirsiniz.
