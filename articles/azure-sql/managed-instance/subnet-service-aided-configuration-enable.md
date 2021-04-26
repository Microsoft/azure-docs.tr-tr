---
title: Azure SQL yönetilen örneği için hizmet destekli alt ağ yapılandırması etkinleştiriliyor
description: Azure SQL yönetilen örneği için hizmet destekli alt ağ yapılandırması etkinleştiriliyor
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: srdan-bozovic-msft
ms.author: srbozovi
ms.date: 03/12/2020
ms.openlocfilehash: 67b398194d9094cd99fccaa85ed0df3be362ce2b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91618038"
---
# <a name="enabling-service-aided-subnet-configuration-for-azure-sql-managed-instance"></a>Azure SQL yönetilen örneği için hizmet destekli alt ağ yapılandırması etkinleştiriliyor
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Hizmet destekli alt ağ yapılandırması, yönetilen örnekleri barındıran alt ağlar için otomatik ağ yapılandırma yönetimi sağlar. Hizmet destekli alt ağ yapılandırması kullanıcısı, yönetilen örnek, SLA 'yı karşılamak için yönetim trafiğinin kesintisiz akışını güvence altına almak için bir sorumluluk sunarken, veri erişiminin (TDS trafik akışları) tam denetiminde kalır.

Otomatik olarak yapılandırılan ağ güvenlik grupları ve yol tablosu kuralları müşteri tarafından görülebilir ve _Microsoft. SQL-managedInstances_UseOnly_ _ önekiyle birlikte açıklanmalıdır.

Kaynak sağlayıcısı için [alt ağ temsilcisini](../../virtual-network/subnet-delegation-overview.md) etkinleştirdikten sonra hizmet destekli yapılandırma otomatik olarak etkinleştirilir `Microsoft.Sql/managedInstances` .

> [!IMPORTANT] 
> Alt ağ-temsili açık olduktan sonra, alt ağdan son sanal kümeyi kaldırana kadar devre dışı bırakabilirsiniz. Sanal kümeyi silme hakkında daha fazla bilgi için aşağıdaki [makaleye](virtual-cluster-delete.md#delete-a-virtual-cluster-from-the-azure-portal)bakın.

> [!NOTE] 
> Hizmet destekli alt ağ yapılandırması SLA 'yı sürdürmek için gerekli bir özelliktir. 1 Mayıs 2020 tarihinden itibaren, yönetilen örnek kaynak sağlayıcısı için temsilci olmayan alt ağlarda yönetilen örnekleri dağıtmak mümkün olmayacaktır. 1 Temmuz 2020 ' de, yönetilen örnekler içeren tüm alt ağlar otomatik olarak yönetilen örnek kaynak sağlayıcısına devredilecektir. 

## <a name="enabling-subnet-delegation-for-new-deployments"></a>Yeni dağıtımlar için alt ağ temsilcisini etkinleştirme
Yönetilen örneği boş alt ağa dağıtmak için, `Microsoft.Sql/managedInstances` aşağıdaki [makalede](../../virtual-network/manage-subnet-delegation.md)açıklandığı gibi kaynak sağlayıcısına temsilci seçmeniz gerekir. _Başvurulan makalede `Microsoft.DBforPostgreSQL/serversv2` Örneğin kaynak sağlayıcısını kullandığını lütfen unutmayın. `Microsoft.Sql/managedInstances` Bunun yerine kaynak sağlayıcısını kullanmanız gerekir._

## <a name="enabling-subnet-delegation-for-existing-deployments"></a>Mevcut dağıtımlar için alt ağ temsilcisini etkinleştirme

Mevcut yönetilen örnek dağıtımınız için alt ağ temsilciliğini etkinleştirmek üzere, yerleştirildiği sanal ağ alt ağını bulmanız gerekir. 

Bunu öğrenmek için, `Virtual network/subnet` `Overview` yönetilen örneğinizin Portal dikey penceresine bakabilirsiniz.

Alternatif olarak, bunu öğrenmek için aşağıdaki PowerShell komutlarını çalıştırabilirsiniz. **Abonelik** KIMLIĞINI abonelik Kimliğinizle değiştirin. Ayrıca, **RG-Name** öğesini yönetilen örneğinizin kaynak grubuyla değiştirin ve **mı-adını** yönetilen örneğinizin adıyla değiştirin.

```powershell
Install-Module -Name Az

Import-Module Az.Accounts
Import-Module Az.Sql

Connect-AzAccount

# Use your subscription ID in place of subscription-id below

Select-AzSubscription -SubscriptionId {subscription-id}

# Replace rg-name with the resource group for your managed instance, and replace mi-name with the name of your managed instance

$mi = Get-AzSqlInstance -ResourceGroupName {rg-name} -Name {mi-name}

$mi.SubnetId
```

Yönetilen örnek alt ağını bulduktan sonra, `Microsoft.Sql/managedInstances` aşağıdaki [makalede](../../virtual-network/manage-subnet-delegation.md)açıklandığı gibi kaynak sağlayıcısına temsilci seçmeniz gerekir. _Başvurulan makalede `Microsoft.DBforPostgreSQL/serversv2` Örneğin kaynak sağlayıcısını kullandığını lütfen unutmayın. `Microsoft.Sql/managedInstances` Bunun yerine kaynak sağlayıcısını kullanmanız gerekir._


> [!IMPORTANT]
> Hizmet destekli yapılandırmayı etkinleştirmek, alt ağda zaten olan yönetilen örnekler için bağlantıda yük devretmeye veya kesintiye neden olmaz.
