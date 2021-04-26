---
title: Okuma çoğaltmalarını yönetme-Azure PowerShell-MariaDB için Azure veritabanı
description: PowerShell kullanarak MariaDB için Azure veritabanı 'nda okuma çoğaltmalarını ayarlamayı ve yönetmeyi öğrenin.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 6/10/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 713f99f67706caded0694d88ece220373920e555
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98664996"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mariadb-using-powershell"></a>PowerShell kullanarak MariaDB için Azure veritabanı 'nda okuma çoğaltmaları oluşturma ve yönetme

Bu makalede, PowerShell kullanarak MariaDB hizmeti için Azure veritabanı 'nda okuma çoğaltmaları oluşturmayı ve yönetmeyi öğreneceksiniz. Okuma çoğaltmaları hakkında daha fazla bilgi edinmek için bkz. [genel bakış](concepts-read-replicas.md).

## <a name="azure-powershell"></a>Azure PowerShell

PowerShell kullanarak okuma çoğaltmaları oluşturabilir ve yönetebilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

Bu nasıl yapılır kılavuzunu tamamlayabilmeniz için şunlar gerekir:

- Yerel olarak yüklenen veya tarayıcıda [Azure Cloud Shell](https://shell.azure.com/) [az PowerShell modülü](/powershell/azure/install-az-ps)
- [MariaDB sunucusu Için Azure veritabanı](quickstart-create-mariadb-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Az. MariaDb PowerShell modülü önizlemedeyken, aşağıdaki komutu kullanarak az PowerShell modülünden ayrı olarak yüklemelisiniz: `Install-Module -Name Az.MariaDb -AllowPrerelease` .
> Az. MariaDb PowerShell modülü genel kullanıma sunulduğunda, bu, gelecekteki az PowerShell modülü sürümlerinin bir parçası haline gelir ve Azure Cloud Shell içinden yerel olarak kullanılabilir.

PowerShell 'i yerel olarak kullanmayı seçerseniz, [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) cmdlet 'Ini kullanarak Azure hesabınıza bağlanın.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Çoğaltma oku özelliği yalnızca Genel Amaçlı veya bellek için Iyileştirilmiş fiyatlandırma katmanlarında bulunan MariaDB sunucuları için Azure veritabanı 'nda kullanılabilir. Kaynak sunucunun bu fiyatlandırma katmanlarından birinde olduğundan emin olun.

### <a name="create-a-read-replica"></a>Okuma amaçlı çoğaltma oluşturma

> [!IMPORTANT]
> Mevcut çoğaltmaları olmayan bir kaynak için çoğaltma oluşturduğunuzda, kaynak ilk olarak çoğaltma için hazırlamak üzere yeniden başlatılır. Bunu dikkate alın ve yoğun bir süre boyunca bu işlemleri gerçekleştirin.

Aşağıdaki komut kullanılarak bir okuma çoğaltması sunucusu oluşturulabilir:

```azurepowershell-interactive
Get-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  New-AzMariaDbServerReplica -Name mydemoreplicaserver -ResourceGroupName myresourcegroup
```

`New-AzMariaDbServerReplica`Komut aşağıdaki parametreleri gerektirir:

| Ayar | Örnek değer | Description  |
| --- | --- | --- |
| ResourceGroupName |  myresourcegroup |  Çoğaltma sunucusunun oluşturulduğu kaynak grubu.  |
| Name | mydemoreplicaserver | Oluşturulan yeni çoğaltma sunucusunun adı. |

Çapraz bölge okuma çoğaltması oluşturmak için **konum** parametresini kullanın. Aşağıdaki örnekte **Batı ABD** bölgesinde bir çoğaltma oluşturulur.

```azurepowershell-interactive
Get-AzMariaDbServer -Name mrdemoserver -ResourceGroupName myresourcegroup |
  New-AzMariaDServerReplica -Name mydemoreplicaserver -ResourceGroupName myresourcegroup -Location westus
```

İçinde bir çoğaltma oluşturabileceğiniz bölgeler hakkında daha fazla bilgi edinmek için [çoğaltma kavramlarını oku makalesini](concepts-read-replicas.md)ziyaret edin.

Varsayılan olarak, **SKU** parametresi belirtilmediği sürece, okuma çoğaltmaları kaynakla aynı sunucu yapılandırmasıyla oluşturulur.

> [!NOTE]
> Çoğaltmanın ana öğe ile devam edebileceğinden emin olmak için çoğaltma sunucusunun yapılandırmasının kaynaktan eşit veya daha büyük bir değer tutulması önerilir.

### <a name="list-replicas-for-a-source-server"></a>Kaynak sunucu için çoğaltmaları listeleme

Belirli bir kaynak sunucu için tüm çoğaltmaları görüntülemek için aşağıdaki komutu çalıştırın:

```azurepowershell-interactive
Get-AzMariaDReplica -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

`Get-AzMariaDReplica`Komut aşağıdaki parametreleri gerektirir:

| Ayar | Örnek değer | Description  |
| --- | --- | --- |
| ResourceGroupName |  myresourcegroup |  Çoğaltma sunucusunun oluşturulacağı kaynak grubu.  |
| aboneliğinde ve | mydemoserver | Kaynak sunucunun adı veya KIMLIĞI. |

### <a name="delete-a-replica-server"></a>Çoğaltma sunucusunu silme

Okuma çoğaltması sunucusunu silme, `Remove-AzMariaDbServer` cmdlet 'i çalıştırılarak yapılabilir.

```azurepowershell-interactive
Remove-AzMariaDbServer -Name mydemoreplicaserver -ResourceGroupName myresourcegroup
```

### <a name="delete-a-source-server"></a>Kaynak sunucuyu silme

> [!IMPORTANT]
> Bir kaynak sunucu durdurulduğunda, tüm çoğaltma sunucularına çoğaltma durdurulur ve kaynak sunucu silinir. Çoğaltma sunucuları artık hem okuma hem de yazma işlemlerini destekleyen tek başına sunucular haline gelir.

Bir kaynak sunucuyu silmek için `Remove-AzMariaDbServer` cmdlet 'ini çalıştırabilirsiniz.

```azurepowershell-interactive
Remove-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup
```

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [PowerShell kullanarak MariaDB sunucusu için Azure veritabanını yeniden başlatma](howto-restart-server-powershell.md)