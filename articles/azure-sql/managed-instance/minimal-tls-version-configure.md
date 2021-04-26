---
title: Minimum TLS sürümü ile yönetilen örneği yapılandırma
description: Yönetilen örnek için en düşük TLS sürümünü yapılandırmayı öğrenin
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: ''
ms.topic: how-to
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: ''
ms.date: 05/25/2020
ms.openlocfilehash: 17d430946f3cba1aa4680d1eaf8979fa4338bc22
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92788409"
---
# <a name="configure-minimal-tls-version-in-azure-sql-managed-instance"></a>Azure SQL Yönetilen Örneğinde en düşük TLS sürümünü yapılandırma
Minimum [Aktarım Katmanı Güvenliği (TLS)](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server) sürümü ayarı, MÜŞTERILERIN Azure SQL yönetilen örneği tarafından kullanılan TLS sürümünü denetlemesine olanak tanır.

Şu anda TLS 1.0, 1.1 ve 1.2’yi destekliyoruz. En Düşük TLS Sürümünü ayarlamak sonraki, daha yeni TLS sürümlerinin desteklendiğinden emin olmanızı sağlar. Örneğin, örneğin, 1,1 'den büyük bir TLS sürümü seçme. yalnızca TLS 1.1 ve 1.2 içeren bağlantıların kabul edileceği ve TLS 1.0’ın reddedileceği anlamına gelir. Uygulamalarınızın bunu desteklediğini test edip onayladıktan sonra en düşük TLS sürümü olarak 1.2’yi ayarlamanızı öneririz çünkü bu sürüm önceki sürümlerde bulunan güvenlik açıklarının düzeltmelerini içerir ve TLS’nin Azure SQL Yönetilen Örneği’nde desteklenen en yüksek sürümüdür.

TLS 'nin eski sürümlerini kullanan uygulamalar için, uygulamalarınızın gereksinimlerine göre en düşük TLS sürümünü ayarlamayı öneririz. Şifrelenmemiş bir bağlantı kullanarak bağlanmak için uygulamalara bağlı olan müşteriler için, en az TLS sürümü ayarlamamız önerilir. 

Daha fazla bilgi için bkz. [SQL veritabanı bağlantısı Için TLS konuları](../database/connect-query-content-reference-guide.md#tls-considerations-for-database-connectivity).

En düşük TLS sürümünü ayarladıktan sonra, sunucunun en düşük TLS sürümünü kullanarak bir TLS sürümü kullanan istemcilerden gelen oturum açma girişimleri aşağıdaki hatayla başarısız olur:

```output
Error 47072
Login failed with invalid TLS version
```

## <a name="set-minimal-tls-version-via-powershell"></a>PowerShell ile en az TLS sürümü ayarla

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager modülü Azure SQL veritabanı tarafından hala desteklenmektedir, ancak gelecekteki tüm geliştirmeler az. SQL modülüne yöneliktir. Bu cmdlet 'ler için bkz. [Azurerd. SQL](/powershell/module/AzureRM.Sql/). Az Module ve Azurerd modüllerinde komutların bağımsız değişkenleri önemli ölçüde aynıdır. Aşağıdaki betik [Azure PowerShell modülünü](/powershell/azure/install-az-ps)gerektiriyor.

Aşağıdaki PowerShell betiği, `Get` örnek düzeyinde nasıl ve `Set` **en az TLS sürümü** özelliğinin olduğunu gösterir:

```powershell
#Get the Minimal TLS Version property
(Get-AzSqlInstance -Name sql-instance-name -ResourceGroupName resource-group).MinimalTlsVersion

# Update Minimal TLS Version Property
Set-AzSqlInstance -Name sql-instance-name -ResourceGroupName resource-group -MinimalTlsVersion "1.2"
```

## <a name="set-minimal-tls-version-via-azure-cli"></a>Azure CLı ile en az TLS sürümü ayarlama

> [!IMPORTANT]
> Bu bölümdeki tüm betikler [Azure CLI](/cli/azure/install-azure-cli)gerektirir.

### <a name="azure-cli-in-a-bash-shell"></a>Bash kabuğu 'nda Azure CLı

Aşağıdaki CLı betiği, bir bash kabuğunda **En düşük TLS sürümü** ayarının nasıl değiştirileceğini göstermektedir:

```azurecli-interactive
# Get current setting for Minimal TLS Version
az sql mi show -n sql-instance-name -g resource-group --query "minimalTlsVersion"

# Update setting for Minimal TLS Version
az sql mi update -n sql-instance-name -g resource-group --set minimalTlsVersion="1.2"
```