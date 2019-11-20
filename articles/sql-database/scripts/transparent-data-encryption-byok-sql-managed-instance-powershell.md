---
title: "PowerShell: BYOK TDE-Azure SQL veritabanı yönetilen örneği 'ni etkinleştir "
description: Azure SQL yönetilen örneğini, PowerShell kullanarak bekleyen şifreleme için BYOK Saydam Veri Şifrelemesi (TDE) kullanmaya başlamak üzere nasıl yapılandıracağınızı öğrenin.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: vanto, carlrab
ms.date: 11/05/2019
ms.openlocfilehash: ddffda5229c9c0d33c563e3ae7b4a884f0f92dff
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73691399"
---
# <a name="manage-transparent-data-encryption-in-a-managed-instance-using-your-own-key-from-azure-key-vault"></a>Yönetilen bir örnekteki Saydam Veri Şifrelemesi, Azure Key Vault kendi anahtarınızı kullanarak yönetin

Bu PowerShell betiği örneği, Azure Key Vault bir anahtar kullanarak Azure SQL yönetilen örneği için müşteri tarafından yönetilen anahtarla Saydam Veri Şifrelemesi (TDE) yapılandırır. Bu, genellikle TDE Kendi Anahtarını Getir senaryosu olarak adlandırılır. Müşteri tarafından yönetilen anahtarla TDE hakkında daha fazla bilgi edinmek için bkz. [tde kendi anahtarını getir Azure SQL 'e](../transparent-data-encryption-byok-azure-sql.md).

## <a name="prerequisites"></a>Ön koşullar

- Mevcut bir yönetilen örnek. Bkz. [Azure SQL veritabanı yönetilen örneği oluşturmak Için PowerShell kullanma](sql-database-create-configure-managed-instance-powershell.md).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

PowerShell 'in yerel olarak veya Azure Cloud Shell kullanılarak kullanılması AZ PowerShell 2.3.2 veya sonraki bir sürümü gerektirir. Yükseltmeniz gerekiyorsa, bkz. [Azure PowerShell modülünü Install](/powershell/azure/install-az-ps)veya geçerli kullanıcının modülünü yüklemek için aşağıdaki örnek betiği çalıştırın:

`Install-Module -Name Az -AllowClobber -Scope CurrentUser`

PowerShell'i yerel olarak çalıştırıyorsanız Azure bağlantısı oluşturmak için `Connect-AzAccount` komutunu da çalıştırmanız gerekir.

## <a name="sample-scripts"></a>Örnek komut dosyaları

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/transparent-data-encryption/setup-tde-byok-sqlmi.ps1 "Set up BYOK TDE for SQL Managed Instance")]

## <a name="next-steps"></a>Sonraki adımlar

Azure PowerShell hakkında daha fazla bilgi için bkz. [Azure PowerShell belgeleri](/powershell/azure/overview).

Ek SQL Veritabanı PowerShell betiği örnekleri [Azure SQL Veritabanı PowerShell betikleri](../sql-database-powershell-samples.md) içinde bulunabilir.
