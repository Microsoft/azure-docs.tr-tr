---
title: Sunucu parametrelerini Yapılandırma-Azure PowerShell-MariaDB için Azure veritabanı
description: Bu makalede, PowerShell kullanarak MariaDB için Azure veritabanı 'nda hizmet parametrelerinin nasıl yapılandırılacağı açıklanır.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.devlang: azurepowershell
ms.topic: how-to
ms.date: 10/1/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 8ace6306bec4c79cbce0a1572360db1acd2cea97
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98662466"
---
# <a name="configure-server-parameters-in-azure-database-for-mariadb-using-powershell"></a>PowerShell kullanarak MariaDB için Azure veritabanı 'nda sunucu parametrelerini yapılandırma

PowerShell kullanarak bir MariaDB sunucusu için Azure veritabanı için yapılandırma parametrelerini listeleyebilir, gösterebilir ve güncelleştirebilirsiniz. Altyapı yapılandırmalarının bir alt kümesi sunucu düzeyinde sunulur ve değiştirilebilir.

>[!Note]
> Sunucu parametreleri sunucu düzeyinde genel olarak güncelleştirilebilir; [Azure CLI](./howto-configure-server-parameters-cli.md), [PowerShell](./howto-configure-server-parameters-using-powershell.md) veya [Azure Portal](./howto-server-parameters.md) kullanın.

## <a name="prerequisites"></a>Önkoşullar

Bu nasıl yapılır kılavuzunu tamamlayabilmeniz için şunlar gerekir:

- Yerel olarak yüklenen veya tarayıcıda [Azure Cloud Shell](https://shell.azure.com/) [az PowerShell modülü](/powershell/azure/install-az-ps)
- [MariaDB sunucusu Için Azure veritabanı](quickstart-create-mariadb-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Az. MariaDb PowerShell modülü önizlemedeyken, aşağıdaki komutu kullanarak az PowerShell modülünden ayrı olarak yüklemelisiniz: `Install-Module -Name Az.MariaDb -AllowPrerelease` .
> Az. MariaDb PowerShell modülü genel kullanıma sunulduğunda, bu, gelecekteki az PowerShell modülü sürümlerinin bir parçası haline gelir ve Azure Cloud Shell içinden yerel olarak kullanılabilir.

PowerShell 'i yerel olarak kullanmayı seçerseniz, [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) cmdlet 'Ini kullanarak Azure hesabınıza bağlanın.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="list-server-configuration-parameters-for-azure-database-for-mariadb-server"></a>MariaDB sunucusu için Azure veritabanı için sunucu yapılandırma parametrelerini listeleyin

Bir sunucudaki tüm değiştirilebilir parametreleri ve değerlerini listelemek için `Get-AzMariaDbConfiguration` cmdlet 'ini çalıştırın.

Aşağıdaki örnekte, **myresourcegroup** kaynak grubundaki **demosunucum** sunucusu için sunucu yapılandırma parametreleri listelenmektedir.

```azurepowershell-interactive
Get-AzMariaDbConfiguration -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

Listelenen parametrelerin her birinin tanımı için [sunucu sistem değişkenlerinde](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html)MariaDB başvurusu bölümüne bakın.

## <a name="show-server-configuration-parameter-details"></a>Sunucu yapılandırma parametresi ayrıntılarını göster

Bir sunucu için belirli bir yapılandırma parametresi ayrıntılarını göstermek için `Get-AzMariaDbConfiguration` cmdlet 'ini çalıştırın ve **ad** parametresini belirtin.

Bu örnekte, **myresourcegroup** kaynak grubu altındaki **demosunucum** sunucusu için **yavaş \_ sorgu \_ günlüğü** sunucu yapılandırma parametresinin ayrıntıları gösterilmektedir.

```azurepowershell-interactive
Get-AzMariaDbConfiguration -Name slow_query_log -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

## <a name="modify-a-server-configuration-parameter-value"></a>Sunucu yapılandırma parametre değerini değiştirme

Ayrıca, MariaDB sunucu altyapısının temel yapılandırma değerini güncelleştiren belirli bir sunucu yapılandırma parametresinin değerini de değiştirebilirsiniz. Yapılandırmayı güncelleştirmek için `Update-AzMariaDbConfiguration` cmdlet 'ini kullanın.

Myresourcegroup kaynak grubu altındaki **demosunucum** sunucusunun **yavaş \_ sorgu \_ günlüğü** sunucu yapılandırma parametresini güncelleştirmek için .

```azurepowershell-interactive
Update-AzMariaDbConfiguration -Name slow_query_log -ResourceGroupName myresourcegroup -ServerName mydemoserver -Value On
```

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [PowerShell kullanarak MariaDB sunucusu Için Azure veritabanı 'nda depolamayı otomatik olarak büyütün](howto-auto-grow-storage-powershell.md).