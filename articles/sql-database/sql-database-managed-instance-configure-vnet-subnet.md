---
title: Yönetilen örnek için var olan bir sanal ağı yapılandırma
description: Bu makalede, Azure SQL veritabanı yönetilen örneğini dağıtabileceğiniz mevcut bir sanal ağın ve alt ağın nasıl yapılandırılacağı açıklanır.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 01/15/2019
ms.openlocfilehash: 6dfc0a59ab4150173196fae82d90eca4880d5364
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73818889"
---
# <a name="configure-an-existing-virtual-network-for-azure-sql-database-managed-instance"></a>Azure SQL veritabanı yönetilen örneği için mevcut bir sanal ağı yapılandırma

Azure SQL veritabanı yönetilen örneği, bir Azure [sanal ağı](../virtual-network/virtual-networks-overview.md) içinde dağıtılmalıdır ve alt ağ yalnızca yönetilen örnekler için ayrılmış olmalıdır. [Yönetilen örnek sanal ağ gereksinimlerine](sql-database-managed-instance-connectivity-architecture.md#network-requirements)göre yapılandırıldıysa, var olan sanal ağı ve alt ağı kullanabilirsiniz.

Aşağıdaki durumlardan biri sizin için geçerliyse, bu makalede açıklanan betiği kullanarak ağınızı doğrulayabilir ve değiştirebilirsiniz:

- Hala yapılandırılmamış yeni bir alt ağa sahipsiniz.
- Alt ağın [gereksinimlere](sql-database-managed-instance-connectivity-architecture.md#network-requirements)göre hizalandığından emin değilsiniz.
- Değişiklikleri yaptıktan sonra alt ağın [ağ gereksinimleriyle](sql-database-managed-instance-connectivity-architecture.md#network-requirements) uyumlu olup olmadığını kontrol etmek istiyorsunuz.

> [!Note]
> Yönetilen bir örneği yalnızca Azure Resource Manager dağıtım modeliyle oluşturulan sanal ağlarda oluşturabilirsiniz. Klasik dağıtım modeli aracılığıyla oluşturulan Azure sanal ağları desteklenmez. [Yönetilen örnekler için alt ağın boyutunu belirleme](sql-database-managed-instance-determine-size-vnet-subnet.md) makalesindeki yönergeleri izleyerek alt ağ boyutunu hesaplayın. İçindeki kaynakları dağıttıktan sonra alt ağı yeniden boyutlandıramazsınız.
>
> Yönetilen bir örnek oluşturulduktan sonra, yönetilen örneği veya VNet 'i başka bir kaynak grubuna veya aboneliğe taşımak desteklenmez.

## <a name="validate-and-modify-an-existing-virtual-network"></a>Var olan bir sanal ağı doğrulama ve değiştirme

Var olan bir alt ağ içinde yönetilen bir örnek oluşturmak istiyorsanız, alt ağı hazırlamak için aşağıdaki PowerShell betiğini öneririz:

```powershell
$scriptUrlBase = 'https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/manage/azure-sql-db-managed-instance/prepare-subnet'

$parameters = @{
    subscriptionId = '<subscriptionId>'
    resourceGroupName = '<resourceGroupName>'
    virtualNetworkName = '<virtualNetworkName>'
    subnetName = '<subnetName>'
    }

Invoke-Command -ScriptBlock ([Scriptblock]::Create((iwr ($scriptUrlBase+'/prepareSubnet.ps1?t='+ [DateTime]::Now.Ticks)).Content)) -ArgumentList $parameters
```

Betik, alt ağı üç adımda hazırlar:

1. Doğrula: yönetilen örnek ağ gereksinimleri için seçili sanal ağı ve alt ağı doğrular.
2. Onayla: kullanıcıya, yönetilen örnek dağıtımı için alt ağı hazırlamak üzere yapılması gereken bir değişiklik kümesi gösterilir. Ayrıca onay ister.
3. Hazırlama: sanal ağı ve alt ağı düzgün şekilde yapılandırır.

## <a name="next-steps"></a>Sonraki adımlar

- Genel bakış için bkz. [yönetilen örnek nedir?](sql-database-managed-instance.md).
- Bir sanal ağ oluşturmayı, yönetilen bir örnek oluşturmayı ve bir veritabanını bir veritabanı yedeklemesinden geri yüklemeyi gösteren bir öğretici için bkz. [Azure SQL veritabanı yönetilen örneği oluşturma](sql-database-managed-instance-get-started.md).
- DNS sorunları için bkz. [Özel BIR DNS yapılandırma](sql-database-managed-instance-custom-dns.md).
