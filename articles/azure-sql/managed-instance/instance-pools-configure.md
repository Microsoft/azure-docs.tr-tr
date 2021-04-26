---
title: SQL yönetilen örneğini bir örnek havuzuna dağıtma
titleSuffix: Azure SQL Managed Instance
description: Bu makalede, Azure SQL yönetilen örnek havuzlarının (Önizleme) nasıl oluşturulacağı ve yönetileceği açıklanmaktadır.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: bonova
ms.author: bonova
ms.reviewer: sstein
ms.date: 09/05/2019
ms.openlocfilehash: 96c24c05f5e0dbb671a294b10ee51be411ca25ff
ms.sourcegitcommit: c6a2d9a44a5a2c13abddab932d16c295a7207d6a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107285300"
---
# <a name="deploy-azure-sql-managed-instance-to-an-instance-pool"></a>Azure SQL yönetilen örneğini bir örnek havuzuna dağıtma
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Bu makalede, [örnek havuzu](instance-pools-overview.md) oluşturma ve Azure SQL yönetilen örneği dağıtma hakkında ayrıntılı bilgi verilmektedir. 

## <a name="instance-pool-operations"></a>Örnek havuzu işlemleri

Aşağıdaki tabloda, Azure portal ve PowerShell 'de örnek havuzlarla ilgili kullanılabilir işlemler ve bunların kullanılabilirlikleri gösterilmektedir.

|Komut|Azure portal|PowerShell|
|:---|:---|:---|
|Örnek havuzu oluşturma|Hayır|Yes|
|Örnek havuzunu güncelleştirme (sınırlı sayıda özellik)|Hayır |Yes |
|Örnek havuzu kullanımını ve özelliklerini denetleme|Hayır|Yes |
|Örnek havuzu silme|Hayır|Yes|
|Örnek havuzu içinde yönetilen bir örnek oluşturma|Hayır|Yes|
|Yönetilen örnek için kaynak kullanımını güncelleştirme|Yes |Yes|
|Yönetilen örnek için kullanımı ve özellikleri denetleyin|Yes|Yes|
|Yönetilen bir örneği havuzdan silme|Yes|Yes|
|Havuzda bir örnek içinde veritabanı oluşturma|Yes|Yes|
|SQL yönetilen örneğinden bir veritabanını silme|Yes|Yes|

Kullanılabilir [PowerShell komutları](/powershell/module/az.sql/):

|Cmdlet |Açıklama |
|:---|:---|
|[New-Azsqlınstancepool](/powershell/module/az.sql/new-azsqlinstancepool/) | SQL yönetilen örnek havuzu oluşturur. |
|[Get-Azsqlınstancepool](/powershell/module/az.sql/get-azsqlinstancepool/) | Örnek havuz hakkında bilgi döndürür. |
|[Set-Azsqlınstancepool](/powershell/module/az.sql/set-azsqlinstancepool/) | SQL yönetilen örneğindeki örnek havuzun özelliklerini ayarlar. |
|[Remove-Azsqlınstancepool](/powershell/module/az.sql/remove-azsqlinstancepool/) | SQL yönetilen örneğindeki bir örnek havuzunu kaldırır. |
|[Get-Azsqlınstancepoolusage](/powershell/module/az.sql/get-azsqlinstancepoolusage/) | SQL yönetilen örnek havuzu kullanımı hakkında bilgi döndürür. |


PowerShell 'i kullanmak için [PowerShell Core 'un en son sürümünü yükleyip](/powershell/scripting/install/installing-powershell#powershell) [Azure PowerShell modülünü yüklemek](/powershell/azure/install-az-ps)için yönergeleri izleyin.

Hem havuzların hem de tek örneklerin içindeki örneklerle ilgili işlemler için, standart [yönetilen örnek komutlarını](api-references-create-manage-instance.md#powershell-create-and-configure-managed-instances)kullanın, ancak bir havuzdaki bir örnek için bu komutlar kullanılırken *örnek havuzu adı* özelliği doldurulmalıdır.

## <a name="deployment-process"></a>Dağıtım süreci

Yönetilen bir örneği bir örnek havuzuna dağıtmak için, ilk olarak, sürenin [boş bir alt ağda oluşturulan tek bir örneği](sql-managed-instance-paas-overview.md#management-operations)dağıtmasıyla aynı olduğu zaman uzun süreli bir işlem olan örnek havuzunu dağıtmanız gerekir. Bundan sonra, tipik olarak beş dakikalık bir işlem yapan görece hızlı bir işlem olan bir yönetilen örneği havuza dağıtabilirsiniz. Örnek havuzu parametresi, bu işlemin bir parçası olarak açıkça belirtilmelidir.

Genel önizlemede, her iki eylem yalnızca PowerShell ve Azure Resource Manager şablonları kullanılarak desteklenir. Azure portal deneyimi şu anda kullanılamıyor.

Yönetilen bir örnek bir havuza dağıtıldıktan sonra, fiyatlandırma katmanı sayfasında özelliklerini değiştirmek için *Azure Portal kullanabilirsiniz.*

## <a name="create-a-virtual-network-with-a-subnet"></a>Alt ağ ile sanal ağ oluşturma 

Aynı sanal ağın içine birden çok örnek havuzu yerleştirmek için aşağıdaki makalelere bakın:

- [Azure SQL yönetilen örneği Için VNET alt ağ boyutunu belirleme](vnet-subnet-determine-size.md).
- [Azure portal şablonunu](virtual-network-subnet-create-arm-template.md) kullanarak yeni bir sanal ağ ve alt ağ oluşturun veya [var olan bir sanal ağı hazırlama](vnet-existing-add-subnet.md)yönergelerini izleyin.
 

## <a name="create-an-instance-pool"></a>Örnek havuzu oluşturma 

Önceki adımları tamamladıktan sonra bir örnek havuzu oluşturmaya hazırlanın.

Aşağıdaki kısıtlamalar örnek havuzlar için geçerlidir:

- Yalnızca Genel Amaçlı ve 5. nesil genel önizlemede kullanılabilir.
- Havuz adı yalnızca küçük harf, sayı ve kısa çizgi içerebilir ve kısa çizgi ile başlayamaz.
- Azure Hibrit Avantajı kullanmak istiyorsanız, örnek havuzu düzeyinde uygulanır. Havuz oluşturma sırasında lisans türünü ayarlayabilir veya oluşturulduktan sonra dilediğiniz zaman güncelleştirebilirsiniz.

> [!IMPORTANT]
> Örnek havuzun dağıtımı, yaklaşık 4,5 saat süren uzun süredir çalışan bir işlemdir.

Ağ parametrelerini almak için:

```powershell
$virtualNetwork = Get-AzVirtualNetwork -Name "miPoolVirtualNetwork" -ResourceGroupName "myResourceGroup"
$subnet = Get-AzVirtualNetworkSubnetConfig -Name "miPoolSubnet" -VirtualNetwork $virtualNetwork
```

Örnek havuzu oluşturmak için:

```powershell
$instancePool = New-AzSqlInstancePool `
  -ResourceGroupName "myResourceGroup" `
  -Name "mi-pool-name" `
  -SubnetId $subnet.Id `
  -LicenseType "LicenseIncluded" `
  -VCore 8 `
  -Edition "GeneralPurpose" `
  -ComputeGeneration "Gen5" `
  -Location "westeurope"
```

> [!IMPORTANT]
> Örnek havuzu dağıtmak uzun süredir çalışan bir işlem olduğundan, bu makalede aşağıdaki adımlardan herhangi birini çalıştırmadan önce tamamlanana kadar beklemeniz gerekir.

## <a name="create-a-managed-instance"></a>Yönetilen örnek oluşturma

Örnek havuzunun başarılı dağıtımından sonra, içinde yönetilen bir örnek oluşturma zamanı.

Yönetilen bir örnek oluşturmak için aşağıdaki komutu yürütün:

```powershell
$instanceOne = $instancePool | New-AzSqlInstance -Name "mi-one-name" -VCore 2 -StorageSizeInGB 256
```

Bir havuzun içindeki bir örneği dağıtmak birkaç dakika sürer. İlk örnek oluşturulduktan sonra, ek örnekler oluşturulabilir:

```powershell
$instanceTwo = $instancePool | New-AzSqlInstance -Name "mi-two-name" -VCore 4 -StorageSizeInGB 512
```

## <a name="create-a-database"></a>Veritabanı oluşturma 

Bir havuz içindeki yönetilen bir örnekte veritabanları oluşturup yönetmek için tek örnek komutlarını kullanın.

Yönetilen bir örnek içinde veritabanı oluşturmak için:

```powershell
$poolinstancedb = New-AzSqlInstanceDatabase -Name "mipooldb1" -InstanceName "poolmi-001" -ResourceGroupName "myResourceGroup"
```


## <a name="get-pool-usage"></a>Havuz kullanımını al 
 
Bir havuzun içindeki örneklerin listesini almak için:

```powershell
$instancePool | Get-AzSqlInstance
```


Havuz kaynağı kullanımını almak için:

```powershell
$instancePool | Get-AzSqlInstancePoolUsage
```


İçindeki havuza ve örneklere ilişkin ayrıntılı kullanım hakkında genel bakış almak için:

```powershell
$instancePool | Get-AzSqlInstancePoolUsage –ExpandChildren
```

Bir örnekteki veritabanlarını listelemek için:

```powershell
$databases = Get-AzSqlInstanceDatabase -InstanceName "pool-mi-001" -ResourceGroupName "resource-group-name"
```


> [!NOTE]
> Örnek havuzu başına veritabanı sayısı ve havuz içinde dağıtılan yönetilen örnek için sınırları denetlemek için [örnek havuzu kaynak sınırları](instance-pools-overview.md#resource-limitations) bölümünü ziyaret edin.


## <a name="scale"></a>Ölçek 


Yönetilen bir örneği veritabanlarıyla doldurduktan sonra, depolama veya performansla ilgili örnek sınırlarına ulaşırsınız. Bu durumda, havuz kullanımı aşılmışsa, örneğinizi ölçeklendirebilirsiniz.
Bir havuz içinde yönetilen bir örneğin ölçeklendirilmesi, birkaç dakika süren bir işlemdir. Ölçeklendirme için önkoşul, örnek havuzu düzeyindeki sanal çekirdekler ve depolama alanı ile kullanılabilir.

Sanal çekirdek sayısını ve depolama boyutunu güncelleştirmek için:

```powershell
$instanceOne | Set-AzSqlInstance -VCore 8 -StorageSizeInGB 512 -InstancePoolName "mi-pool-name"
```


Yalnızca depolama boyutunu güncelleştirmek için:

```powershell
$instance | Set-AzSqlInstance -StorageSizeInGB 1024 -InstancePoolName "mi-pool-name"
```

## <a name="connect"></a>Bağlanma 

Bir havuzdaki yönetilen bir örneğe bağlanmak için aşağıdaki iki adım gereklidir:

1. [Örnek için genel uç noktasını etkinleştirin](#enable-the-public-endpoint).
2. [Ağ güvenlik grubuna (NSG) bir gelen kuralı ekleyin](#add-an-inbound-rule-to-the-network-security-group).

Her iki adım tamamlandıktan sonra, örnek oluşturma sırasında sağlanmış olan genel bir uç nokta adresini, bağlantı noktasını ve kimlik bilgilerini kullanarak örneğe bağlanabilirsiniz. 

### <a name="enable-the-public-endpoint"></a>Genel uç noktayı etkinleştirme

Bir örnek için genel bitiş noktasının etkinleştirilmesi Azure portal aracılığıyla veya aşağıdaki PowerShell komutu kullanılarak yapılabilir:


```powershell
$instanceOne | Set-AzSqlInstance -InstancePoolName "pool-mi-001" -PublicDataEndpointEnabled $true
```

Bu parametre, örnek oluşturma sırasında da ayarlanabilir.

### <a name="add-an-inbound-rule-to-the-network-security-group"></a>Ağ güvenlik grubuna bir gelen kuralı ekleme 

Bu adım Azure portal veya PowerShell komutları kullanılarak yapılabilir ve alt ağ yönetilen örnek için hazırlandıktan sonra herhangi bir zamanda yapılabilir.

Ayrıntılar için bkz. [ağ güvenlik grubunda genel uç nokta trafiğine Izin verme](public-endpoint-configure.md#allow-public-endpoint-traffic-on-the-network-security-group).


## <a name="move-an-existing-single-instance-to-a-pool"></a>Mevcut bir örneği havuza taşıma
 
Örnekleri bir havuzun içine ve dışına taşımak, genel önizleme sınırlamalarından biridir. Geçici bir çözüm, havuzun dışındaki bir örnekten gelen veritabanlarının zaman içinde, zaten havuzda olan bir örneğe geri yüklenmesini kullanır. 

Her iki örnek de aynı abonelikte ve bölgede olmalıdır. Bölgeler arası ve çapraz abonelik geri yüklemesi şu anda desteklenmiyor.

Bu işlem bir süre kapalı kalma süresine sahip olur.

Mevcut veritabanlarını taşımak için:

1. Geçiş yaptığınız yönetilen örnekteki iş yüklerini duraklatın.
2. Sistem veritabanları oluşturmak ve bunları örnek havuzunun içindeki örnekte yürütmek için komut dosyaları oluşturun.
3. Her bir veritabanının tek örneğinden havuzdaki örneğe bir noktadan noktaya geri yükleme yapın.

    ```powershell
    $resourceGroupName = "my resource group name"
    $managedInstanceName = "my managed instance name"
    $databaseName = "my source database name"
    $pointInTime = "2019-08-21T08:51:39.3882806Z"
    $targetDatabase = "name of the new database that will be created"
    $targetResourceGroupName = "resource group of instance pool"
    $targetInstanceName = "pool instance name"
       
    Restore-AzSqlInstanceDatabase -FromPointInTimeBackup `
      -ResourceGroupName $resourceGroupName `
      -InstanceName $managedInstanceName `
      -Name $databaseName `
      -PointInTime $pointInTime `
      -TargetInstanceDatabaseName $targetDatabase `
      -TargetResourceGroupName $targetResourceGroupName `
      -TargetInstanceName $targetInstanceName
    ```

4. Uygulamanızı yeni örneğe getirin ve iş yüklerini sürdürülemez.

Birden çok veritabanı varsa, her veritabanı için işlemi yineleyin.


## <a name="next-steps"></a>Sonraki adımlar

- Özellikler ve karşılaştırma listesi için bkz. [SQL ortak özellikleri](../database/features-comparison.md).
- VNet yapılandırması hakkında daha fazla bilgi için bkz. [SQL yönetilen örnek VNET yapılandırması](connectivity-architecture-overview.md).
- Yönetilen bir örnek oluşturan ve bir veritabanını bir yedekleme dosyasından geri yükleyen hızlı başlangıç için bkz. [yönetilen örnek oluşturma](instance-create-quickstart.md).
- Geçiş için Azure veritabanı geçiş hizmeti 'ni kullanma hakkında bir öğretici için bkz. [veritabanı geçiş hizmeti kullanılarak SQL yönetilen örnek geçişi](../../dms/tutorial-sql-server-to-managed-instance.md).
- Yerleşik sorun giderme zekası ile SQL yönetilen örnek veritabanı performansının gelişmiş izlenmesi için bkz. [Azure SQL Analytics kullanarak Azure SQL yönetilen örneğini izleme](../../azure-monitor/insights/azure-sql.md).
- Fiyatlandırma bilgileri için bkz. [SQL yönetilen örnek fiyatlandırması](https://azure.microsoft.com/pricing/details/sql-database/managed/).
