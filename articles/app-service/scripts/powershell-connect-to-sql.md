---
title: Azure PowerShell betik örneği-bir uygulamayı SQL veritabanına bağlama | Microsoft Docs
description: Azure PowerShell betik örneği-bir App Service uygulamasını SQL veritabanına bağlama
services: app-service\web
documentationcenter: ''
author: syntaxc4
manager: erikre
editor: ''
tags: azure-service-management
ms.assetid: 055440a9-fff1-49b2-b964-9c95b364e533
ms.service: app-service
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 03/20/2017
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: 799fb67b20c45653ec741a92ec7a75aff2f8c509
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70087824"
---
# <a name="connect-an-app-service-app-to-a-sql-database"></a>App Service uygulamasını SQL veritabanına bağlama

Bu senaryoda, Azure SQL veritabanı ve App Service uygulaması oluşturmayı öğreneceksiniz. Daha sonra uygulama ayarlarını kullanarak SQL veritabanını uygulamaya bağlayacaksınız.

Gerekirse, [Azure PowerShell kılavuzunda](/powershell/azure/overview) bulunan yönergeleri kullanarak Azure PowerShell’i yükleyin ve ardından Azure ile bağlantı oluşturmak için `Connect-AzAccount` komutunu çalıştırın.

## <a name="sample-script"></a>Örnek betik

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/connect-to-sql/connect-to-sql.ps1?highlight=13 "Connect an app to a SQL database")]

## <a name="clean-up-deployment"></a>Dağıtımı temizleme 

Betik örneği çalıştırıldıktan sonra, kaynak grubunu, App Service uygulamayı ve tüm ilgili kaynakları kaldırmak için aşağıdaki komut kullanılabilir.

```powershell
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>Betik açıklaması

Bu betik aşağıdaki komutları kullanır. Tablodaki her komut, komuta özgü belgelere yönlendirir.

| Komut | Notlar |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Tüm kaynakların depolandığı bir kaynak grubu oluşturur. |
| [New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) | App Service planı oluşturur. |
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | App Service uygulaması oluşturur. |
| [New-AzSQLServer](/powershell/module/az.sql/new-azsqlserver) | SQL Veritabanı sunucusu oluşturur. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | SQL Veritabanı sunucusu için bir güvenlik duvarı kuralı oluşturur. |
| [New-AzSQLDatabase](/powershell/module/az.sql/new-azsqldatabase) | Bir veritabanı veya elastik bir veritabanı oluşturur. |
| [Set-AzWebApp](/powershell/module/az.websites/set-azwebapp) | App Service uygulamasının yapılandırmasını değiştirir. |

## <a name="next-steps"></a>Sonraki adımlar

Azure PowerShell modülü hakkında daha fazla bilgi için bkz. [Azure PowerShell belgeleri](/powershell/azure/overview).

Azure App Service için ek Azure PowerShell örnekleri [Azure PowerShell örneklerde](../samples-powershell.md)bulunabilir.
