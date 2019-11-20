---
title: Yönetilen örnek bağlantı türleri
description: Yönetilen örnek bağlantı türleri hakkında bilgi edinin
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: vanto
ms.date: 10/07/2019
ms.openlocfilehash: 46223d1701b930d93de7c49c1e216a41045dda16
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819469"
---
# <a name="azure-sql-database-managed-instance-connection-types"></a>Azure SQL veritabanı yönetilen örnek bağlantı türleri

Bu makalede, istemcilerin bağlantı türüne bağlı olarak Azure SQL veritabanı yönetilen örneği 'ne nasıl bağlanacağı açıklanmaktadır. Bağlantı türlerini değiştirmeye yönelik betik örnekleri aşağıda verilmiştir ve varsayılan bağlantı ayarlarını değiştirmekle ilgili önemli noktalar verilmektedir.

## <a name="connection-types"></a>Bağlantı türleri

Azure SQL veritabanı yönetilen örneği aşağıdaki iki bağlantı türünü destekler:

- **Yeniden yönlendir (önerilir):** İstemciler, veritabanını barındıran düğüme doğrudan bağlantı kurar. Yeniden yönlendirme kullanarak bağlantı sağlamak için, 1433 ve 11000-11999 bağlantı noktalarında erişime izin vermek üzere güvenlik duvarları ve ağ güvenlik grupları (NSG) açmanız gerekir. Paketler doğrudan veritabanına gider ve bu nedenle proxy üzerinden yeniden yönlendirme kullanan gecikme süresi ve işleme performans iyileştirmeleri vardır.
- **Ara sunucu (varsayılan):** Bu modda, tüm bağlantılar proxy ağ geçidi bileşeni kullanıyor. Bağlantıyı etkinleştirmek için, yalnızca özel ağlar için bağlantı noktası 1433 ve genel bağlantı bağlantı noktası 3342 ' nin açılması gerekir. Bu modun seçilmesi, iş yükünün doğasına bağlı olarak daha yüksek gecikme süresine ve düşük aktarım hızına yol açabilir. En düşük gecikme süresi ve en yüksek aktarım hızı için proxy bağlantı İlkesi üzerinden yeniden yönlendirme bağlantı ilkesini öneririz.

## <a name="redirect-connection-type"></a>Yeniden yönlendirme bağlantı türü

Yeniden yönlendirme bağlantı türü, TCP oturumu SQL altyapısına kurulduktan sonra, istemci oturumu yük dengeleyiciden sanal küme düğümünün hedef sanal IP 'sini edinir. Sonraki paketler, ağ geçidini atlayarak doğrudan sanal küme düğümüne akar. Aşağıdaki diyagramda bu trafik akışı gösterilmektedir.

![yeniden yönlendirme. png](media/sql-database-managed-instance-connection-types/redirect.png)

> [!IMPORTANT]
> Yeniden yönlendirme bağlantı türü şu anda yalnızca özel uç nokta için çalışıyor. Bağlantı türü ayarından bağımsız olarak, genel uç nokta üzerinden gelen bağlantılar bir ara sunucu üzerinden olur.

## <a name="proxy-connection-type"></a>Proxy bağlantı türü

Ara sunucu bağlantı türü, TCP oturumunun ağ geçidi kullanılarak kurulduğu ve sonraki tüm paketlerin üzerinden akış yaptığı anlamına gelir. Aşağıdaki diyagramda bu trafik akışı gösterilmektedir.

![Proxy. png](media/sql-database-managed-instance-connection-types/proxy.png)

## <a name="script-to-change-connection-type-settings-using-powershell"></a>PowerShell kullanarak bağlantı türü ayarlarını değiştirme betiği

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Aşağıdaki PowerShell betiği, yönetilen bir örnek için yeniden yönlendirme için bağlantı türünün nasıl değiştirileceğini gösterir.

```powershell
Install-Module -Name Az
Import-Module Az.Accounts
Import-Module Az.Sql

Connect-AzAccount
# Get your SubscriptionId from the Get-AzSubscription command
Get-AzSubscription
# Use your SubscriptionId in place of {subscription-id} below
Select-AzSubscription -SubscriptionId {subscription-id}
# Replace {rg-name} with the resource group for your managed instance, and replace {mi-name} with the name of your managed instance
$mi = Get-AzSqlInstance -ResourceGroupName {rg-name} -Name {mi-name}
$mi = $mi | Set-AzSqlInstance -ProxyOverride "Redirect" -force
```

## <a name="next-steps"></a>Sonraki adımlar

- [Veritabanını yönetilen örneğe geri yükleme](sql-database-managed-instance-get-started-restore.md)
- [Yönetilen örnekte ortak bir uç noktanın nasıl yapılandırılacağını](sql-database-managed-instance-public-endpoint-configure.md) öğrenin
- [Yönetilen örnek bağlantı mimarisi](sql-database-managed-instance-connectivity-architecture.md) hakkında bilgi edinin