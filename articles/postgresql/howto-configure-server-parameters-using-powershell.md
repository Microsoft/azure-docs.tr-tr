---
title: Sunucu parametrelerini Yapılandırma-Azure PowerShell-PostgreSQL için Azure veritabanı
description: Bu makalede, PowerShell kullanarak PostgreSQL için Azure veritabanı 'nda hizmet parametrelerinin nasıl yapılandırılacağı açıklanır.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.devlang: azurepowershell
ms.topic: how-to
ms.date: 06/08/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6e9bf55aa46e2206928a5f93285a4001dc731f64
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105604303"
---
# <a name="customize-azure-database-for-postgresql-server-parameters-using-powershell"></a>PowerShell kullanarak PostgreSQL için Azure veritabanı sunucu parametrelerini özelleştirme

PowerShell kullanarak bir PostgreSQL için Azure veritabanı sunucusu için yapılandırma parametrelerini listeleyebilir, gösterebilir ve güncelleştirebilirsiniz. Altyapı yapılandırmalarının bir alt kümesi sunucu düzeyinde sunulur ve değiştirilebilir.

## <a name="prerequisites"></a>Önkoşullar

Bu nasıl yapılır kılavuzunu tamamlayabilmeniz için şunlar gerekir:

- Yerel olarak yüklenen veya tarayıcıda [Azure Cloud Shell](https://shell.azure.com/) [az PowerShell modülü](/powershell/azure/install-az-ps)
- [PostgreSQL Için Azure veritabanı sunucusu](quickstart-create-postgresql-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Az. PostgreSql PowerShell modülü önizlemedeyken, aşağıdaki komutu kullanarak az PowerShell modülünden ayrı olarak yüklemelisiniz: `Install-Module -Name Az.PostgreSql -AllowPrerelease` .
> Az. PostgreSql PowerShell modülü genel kullanıma sunulduğunda, bu, gelecekteki az PowerShell modülü sürümlerinin bir parçası haline gelir ve Azure Cloud Shell içinden yerel olarak kullanılabilir.

PowerShell 'i yerel olarak kullanmayı seçerseniz, [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) cmdlet 'Ini kullanarak Azure hesabınıza bağlanın.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="list-server-configuration-parameters-for-azure-database-for-postgresql-server"></a>PostgreSQL için Azure veritabanı sunucusu için sunucu yapılandırma parametrelerini listeleyin

Bir sunucudaki tüm değiştirilebilir parametreleri ve değerlerini listelemek için `Get-AzPostgreSqlConfiguration` cmdlet 'ini çalıştırın.

Aşağıdaki örnekte, **myresourcegroup** kaynak grubundaki **demosunucum** sunucusu için sunucu yapılandırma parametreleri listelenmektedir.

```azurepowershell-interactive
Get-AzPostgreSqlConfiguration -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

Listelenen parametrelerin her birinin tanımı için, [ortam değişkenlerinin](https://www.postgresql.org/docs/12/libpq-envars.html)PostgreSQL başvurusu bölümüne bakın.

## <a name="show-server-configuration-parameter-details"></a>Sunucu yapılandırma parametresi ayrıntılarını göster

Bir sunucu için belirli bir yapılandırma parametresi ayrıntılarını göstermek için `Get-AzPostgreSqlConfiguration` cmdlet 'ini çalıştırın ve **ad** parametresini belirtin.

Bu örnekte, **myresourcegroup** kaynak grubu altındaki **demosunucum** sunucusu için **yavaş \_ sorgu \_ günlüğü** sunucu yapılandırma parametresinin ayrıntıları gösterilmektedir.

```azurepowershell-interactive
Get-AzPostgreSqlConfiguration -Name slow_query_log -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

## <a name="modify-a-server-configuration-parameter-value"></a>Sunucu yapılandırma parametre değerini değiştirme

Ayrıca, PostgreSQL sunucu altyapısının temel yapılandırma değerini güncelleştiren belirli bir sunucu yapılandırma parametresinin değerini de değiştirebilirsiniz. Yapılandırmayı güncelleştirmek için `Update-AzPostgreSqlConfiguration` cmdlet 'ini kullanın.

Myresourcegroup kaynak grubu altındaki **demosunucum** sunucusunun **yavaş \_ sorgu \_ günlüğü** sunucu yapılandırma parametresini güncelleştirmek için .

```azurepowershell-interactive
Update-AzPostgreSqlConfiguration -Name slow_query_log -ResourceGroupName myresourcegroup -ServerName mydemoserver -Value On
```

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [PowerShell kullanarak PostgreSQL Için Azure veritabanı sunucusu 'nda depolamayı otomatik olarak büyütün](howto-auto-grow-storage-powershell.md).