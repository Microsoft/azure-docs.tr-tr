---
title: Okuma çoğaltmalarını yönetme-Azure PowerShell-PostgreSQL için Azure veritabanı
description: PowerShell kullanarak PostgreSQL için Azure veritabanı 'nda okuma çoğaltmalarını ayarlama ve yönetme hakkında bilgi edinin.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 06/08/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b0a5547928bd7d19343c50e40ab9fcb2c335e893
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97674540"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-postgresql-using-powershell"></a>PowerShell kullanarak PostgreSQL için Azure veritabanı 'nda okuma çoğaltmaları oluşturma ve yönetme

Bu makalede, PowerShell kullanarak PostgreSQL için Azure veritabanı hizmetinde okuma çoğaltmaları oluşturmayı ve yönetmeyi öğreneceksiniz. Okuma çoğaltmaları hakkında daha fazla bilgi edinmek için bkz. [genel bakış](concepts-read-replicas.md).

## <a name="azure-powershell"></a>Azure PowerShell

PowerShell kullanarak okuma çoğaltmaları oluşturabilir ve yönetebilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

Bu nasıl yapılır kılavuzunu tamamlayabilmeniz için şunlar gerekir:

- Yerel olarak yüklenen veya tarayıcıda [Azure Cloud Shell](https://shell.azure.com/) [az PowerShell modülü](/powershell/azure/install-az-ps)
- [PostgreSQL Için Azure veritabanı sunucusu](quickstart-create-postgresql-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Az. PostgreSql PowerShell modülü önizlemedeyken, aşağıdaki komutu kullanarak az PowerShell modülünden ayrı olarak yüklemelisiniz: `Install-Module -Name Az.PostgreSql -AllowPrerelease` .
> Az. PostgreSql PowerShell modülü genel kullanıma sunulduğunda, bu, gelecekteki az PowerShell modülü sürümlerinin bir parçası haline gelir ve Azure Cloud Shell içinden yerel olarak kullanılabilir.

PowerShell 'i yerel olarak kullanmayı seçerseniz, [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) cmdlet 'Ini kullanarak Azure hesabınıza bağlanın.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Çoğaltma oku özelliği yalnızca Genel Amaçlı veya bellek için Iyileştirilmiş fiyatlandırma katmanlarında PostgreSQL için Azure veritabanı sunucuları için kullanılabilir. Birincil sunucunun bu fiyatlandırma katmanlarından birinde olduğundan emin olun.

### <a name="create-a-read-replica"></a>Okuma amaçlı çoğaltma oluşturma

Aşağıdaki komut kullanılarak bir okuma çoğaltması sunucusu oluşturulabilir:

```azurepowershell-interactive
Get-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  New-AzPostgreSqlServerReplica -Name mydemoreplicaserver -ResourceGroupName myresourcegroup
```

`New-AzPostgreSqlServerReplica`Komut aşağıdaki parametreleri gerektirir:

| Ayar | Örnek değer | Description  |
| --- | --- | --- |
| ResourceGroupName |  myresourcegroup |  Çoğaltma sunucusunun oluşturulduğu kaynak grubu.  |
| Name | mydemoreplicaserver | Oluşturulan yeni çoğaltma sunucusunun adı. |

Çapraz bölge okuma çoğaltması oluşturmak için **konum** parametresini kullanın. Aşağıdaki örnekte **Batı ABD** bölgesinde bir çoğaltma oluşturulur.

```azurepowershell-interactive
Get-AzPostgreSqlServer -Name mrdemoserver -ResourceGroupName myresourcegroup |
  New-AzPostgreSQLServerReplica -Name mydemoreplicaserver -ResourceGroupName myresourcegroup -Location westus
```

İçinde bir çoğaltma oluşturabileceğiniz bölgeler hakkında daha fazla bilgi edinmek için [çoğaltma kavramlarını oku makalesini](concepts-read-replicas.md)ziyaret edin.

Varsayılan olarak, **SKU** parametresi belirtilmediği sürece, okuma çoğaltmaları birincil ile aynı sunucu yapılandırmasıyla oluşturulur.

> [!NOTE]
> Çoğaltmanın ana öğe ile devam edebileceğinden emin olmak için çoğaltma sunucusunun yapılandırmasının Birincilden büyük veya daha büyük bir değere sahip olması önerilir.

### <a name="list-replicas-for-a-primary-server"></a>Birincil sunucu için çoğaltmaları listeleme

Belirli bir birincil sunucu için tüm çoğaltmaları görüntülemek için aşağıdaki komutu çalıştırın:

```azurepowershell-interactive
Get-AzPostgreSQLReplica -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

`Get-AzPostgreSQLReplica`Komut aşağıdaki parametreleri gerektirir:

| Ayar | Örnek değer | Description  |
| --- | --- | --- |
| ResourceGroupName |  myresourcegroup |  Çoğaltma sunucusunun oluşturulacağı kaynak grubu.  |
| aboneliğinde ve | mydemoserver | Birincil sunucunun adı veya KIMLIĞI. |

### <a name="stop-a-replica-server"></a>Çoğaltma sunucusunu durdur

Okuma çoğaltması sunucusunu durdurmak, okuma çoğaltmasını bağımsız bir sunucu olacak şekilde yükseltir. `Update-AzPostgreSqlServer`Cmdlet 'i çalıştırılarak ve ReplicationRole değeri olarak ayarlanarak yapılabilir `None` .

```azurepowershell-interactive
Update-AzPostgreSqlServer -Name mydemoreplicaserver -ResourceGroupName myresourcegroup -ReplicationRole None
```

### <a name="delete-a-replica-server"></a>Çoğaltma sunucusunu silme

Okuma çoğaltması sunucusunu silme, `Remove-AzPostgreSqlServer` cmdlet 'i çalıştırılarak yapılabilir.

```azurepowershell-interactive
Remove-AzPostgreSqlServer -Name mydemoreplicaserver -ResourceGroupName myresourcegroup
```

### <a name="delete-a-primary-server"></a>Birincil sunucuyu silme

> [!IMPORTANT]
> Birincil sunucunun silinmesi, tüm çoğaltma sunucularına çoğaltmayı durduruyor ve birincil sunucunun kendisini siler. Çoğaltma sunucuları artık hem okuma hem de yazma işlemlerini destekleyen tek başına sunucular haline gelir.

Birincil sunucuyu silmek için `Remove-AzPostgreSqlServer` cmdlet 'ini çalıştırabilirsiniz.

```azurepowershell-interactive
Remove-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup
```

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [PowerShell kullanarak PostgreSQL için Azure veritabanı sunucusunu yeniden başlatma](howto-restart-server-powershell.md)