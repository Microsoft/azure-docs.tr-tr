---
title: Örnek havuzları nasıl yapılır Kılavuzu (Önizleme)
description: Bu makalede, Azure SQL veritabanı örneği havuzlarının (Önizleme) nasıl oluşturulacağı ve yönetileceği açıklanmaktadır.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: sstein, carlrab
ms.date: 09/05/2019
ms.openlocfilehash: 13c58ddf5f51e5b63d2dbe425b3ec795e21dabb8
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73810344"
---
# <a name="azure-sql-database-instance-pools-preview-how-to-guide"></a>Azure SQL veritabanı örnek havuzları (Önizleme) nasıl yapılır Kılavuzu

Bu makalede, [örnek havuzlarının](sql-database-instance-pools.md)nasıl oluşturulacağı ve yönetileceği hakkında ayrıntılı bilgi verilmektedir.

## <a name="instance-pool-operations"></a>Örnek havuzu işlemleri

Aşağıdaki tabloda, Azure portal ve PowerShell 'de örnek havuzlarla ilgili kullanılabilir işlemler ve bunların kullanılabilirlikleri gösterilmektedir.

|Komut|Azure portal|PowerShell|
|:---|:---|:---|
|Örnek havuzu oluşturma|Hayır|Evet|
|Örnek havuzunu güncelleştir (sınırlı sayıda özellik)|Hayır |Evet |
|Örnek havuzunun kullanımını ve özelliklerini denetleme|Hayır|Evet |
|Örnek havuzunu Sil|Hayır|Evet|
|Örnek havuzu içinde yönetilen örnek oluştur|Hayır|Evet|
|Yönetilen örnek kaynak kullanımını Güncelleştir|Evet |Evet|
|Yönetilen örnek kullanımını ve özelliklerini denetleme|Evet|Evet|
|Yönetilen örneği havuzdan Sil|Evet|Evet|
|Yönetilen örnekte havuza yerleştirilmiş bir veritabanı oluşturma|Evet|Evet|
|Yönetilen örnekten bir veritabanını silme|Evet|Evet|

Kullanılabilir [PowerShell komutları](https://docs.microsoft.com/powershell/module/az.sql/)

|Cmdlet |Açıklama |
|:---|:---|
|[New-Azsqlınstancepool](/powershell/module/az.sql/new-azsqlinstancepool/) | Azure SQL veritabanı örnek havuzu oluşturur. |
|[Get-Azsqlınstancepool](/powershell/module/az.sql/get-azsqlinstancepool/) | Azure SQL örnek havuzu hakkında bilgi döndürür. |
|[Set-Azsqlınstancepool](/powershell/module/az.sql/set-azsqlinstancepool/) | Bir Azure SQL veritabanı örneği havuzunun özelliklerini ayarlar. |
|[Remove-Azsqlınstancepool](/powershell/module/az.sql/remove-azsqlinstancepool/) | Bir Azure SQL veritabanı örneği havuzunu kaldırır. |
|[Get-Azsqlınstancepoolusage](/powershell/module/az.sql/get-azsqlinstancepoolusage/) | Azure SQL örnek havuzu kullanımıyla ilgili bilgileri döndürür. |


PowerShell 'i kullanmak için [PowerShell Core 'un en son sürümünü yükleyip](https://docs.microsoft.com/powershell/scripting/install/installing-powershell#powershell-core) [Azure PowerShell modülünü yüklemek](https://docs.microsoft.com/powershell/azure/install-az-ps)için yönergeleri izleyin.

Hem havuzların hem de tek örneklerin içindeki örneklerle ilgili işlemler için, standart [yönetilen örnek komutlarını](sql-database-managed-instance-create-manage.md#powershell-create-and-manage-managed-instances)kullanın, ancak bir havuzdaki bir örnek için bu komutlar kullanılırken *örnek havuzu adı* özelliği doldurulmalıdır.

## <a name="how-to-deploy-managed-instances-into-pools"></a>Yönetilen örnekleri havuzlara dağıtma

Bir havuza örnek dağıtma işlemi aşağıdaki iki adımdan oluşur:

1. Tek kapalı örnek havuzu dağıtımı. Bu, sürenin [boş bir alt ağda oluşturulan tek bir örneği](sql-database-managed-instance.md#managed-instance-management-operations)dağıtmasıyla aynı olduğu uzun süren bir işlemdir.

2. Örnek havuzunda yinelenen örnek dağıtımı. Örnek havuzu parametresi, bu işlemin bir parçası olarak açıkça belirtilmelidir. Bu, genellikle 5 dakikaya kadar süren görece hızlı bir işlemdir.

Genel önizlemede, her iki adım yalnızca PowerShell ve Kaynak Yöneticisi şablonları kullanılarak desteklenir. Azure portal deneyimi şu anda kullanılamıyor.

Yönetilen bir örnek bir havuza dağıtıldıktan sonra, fiyatlandırma katmanı sayfasında özelliklerini değiştirmek için *Azure Portal kullanabilirsiniz.*


## <a name="create-an-instance-pool"></a>Örnek havuzu oluşturma

Örnek havuzu oluşturmak için:

1. [Alt ağ içeren bir sanal ağ oluşturun](#create-a-virtual-network-with-a-subnet).
2. [Örnek havuzu oluşturun](#create-an-instance-pool).


### <a name="create-a-virtual-network-with-a-subnet"></a>Alt ağ ile sanal ağ oluşturma 

Aynı sanal ağın içine birden çok örnek havuzu yerleştirmek için aşağıdaki makalelere bakın:

- [Azure SQL veritabanı yönetilen örneği Için VNET alt ağ boyutunu belirleme](sql-database-managed-instance-determine-size-vnet-subnet.md).
- [Azure portal şablonunu](sql-database-managed-instance-create-vnet-subnet.md) kullanarak yeni bir sanal ağ ve alt ağ oluşturun veya [var olan bir sanal ağı hazırlama](sql-database-managed-instance-configure-vnet-subnet.md)yönergelerini izleyin.
 


### <a name="create-an-instance-pool"></a>Örnek havuzu oluşturma 

Önceki adımları tamamladıktan sonra bir örnek havuzu oluşturmaya hazırlanın.

Aşağıdaki kısıtlamalar örnek havuzlar için geçerlidir:

- Yalnızca Genel Amaçlı ve 5. nesil genel önizlemede kullanılabilir.
- Havuz adı yalnızca küçük harf, rakam ve kısa çizgi içerebilir ve kısa çizgi ile başlayamaz.
- Alt ağ KIMLIĞINI almak için `Get-AzVirtualNetworkSubnetConfig -Name "miPoolSubnet" -VirtualNetwork $virtualNetwork`kullanın.
- AHB 'yi (Azure Hibrit Avantajı) kullanmak istiyorsanız, örnek havuzu düzeyinde uygulanır. Havuz oluşturma sırasında lisans türünü ayarlayabilir veya oluşturulduktan sonra dilediğiniz zaman güncelleştirebilirsiniz.

> [!IMPORTANT]
> Örnek havuzun dağıtımı, yaklaşık 4,5 saat süren uzun süredir çalışan bir işlemdir.

Örnek havuzu oluşturmak için:

```powershell
$instancePool = New-AzSqlInstancePool `
  -ResourceGroupName "myResourceGroup" `
  -Name "mi-pool-name" `
  -SubnetId "/subscriptions/subscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/miPoolVirtualNetwork/subnets/miPoolSubnet" `
  -LicenseType "LicenseIncluded" `
  -VCore 80 `
  -Edition "GeneralPurpose" `
  -ComputeGeneration "Gen5" `
  -Location "westeurope"
```

> [!IMPORTANT]
> Örnek havuzu dağıtmak uzun süredir çalışan bir işlem olduğundan, bu makalede aşağıdaki adımlardan herhangi birini çalıştırmadan önce tamamlanana kadar beklemeniz gerekir.

## <a name="create-a-managed-instance-inside-the-pool"></a>Havuz içinde yönetilen bir örnek oluşturma 

Örnek havuzunun başarılı dağıtımından sonra, bunun içinde bir örnek oluşturma zamanı vardır.

Yönetilen bir örnek oluşturmak için aşağıdaki komutu yürütün:

```powershell
$instanceOne = $instancePool | New-AzSqlInstance -Name "mi-pool-name" -VCore 2 -StorageSizeInGB 256
```

Bir havuzun içindeki bir örneği dağıtmak birkaç dakika sürer. İlk örnek oluşturulduktan sonra, ek örnekler oluşturulabilir:

```powershell
$instanceTwo = $instancePool | New-AzSqlInstance -Name "mi-pool-name" -VCore 4 -StorageSizeInGB 512
```

## <a name="create-a-database-inside-an-instance"></a>Örnek içinde veritabanı oluşturma 

Bir havuz içindeki yönetilen bir örnekte veritabanları oluşturup yönetmek için tek örnek komutlarını kullanın.

Yönetilen bir örnek içinde veritabanı oluşturmak için:

```powershell
$poolinstancedb = New-AzSqlInstanceDatabase -Name "mipooldb1" -InstanceName "poolmi-001" -ResourceGroupName "myResourceGroup"
```


## <a name="get-instance-pool-usage"></a>Örnek havuzu kullanımını al 
 
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
> Havuz başına 100 veritabanı sınırı vardır (örnek başına değil).


## <a name="scale-a-managed-instance-inside-a-pool"></a>Bir havuz içinde yönetilen örneği ölçeklendirme 


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



## <a name="connect-to-a-managed-instance-inside-a-pool"></a>Bir havuz içinde yönetilen bir örneğe bağlanma

Bir havuzdaki yönetilen bir örneğe bağlanmak için aşağıdaki iki adım gereklidir:

1. [Örnek için genel uç noktasını etkinleştirin](#enable-the-public-endpoint-for-the-instance).
2. [Ağ güvenlik grubuna (NSG) bir gelen kuralı ekleyin](#add-an-inbound-rule-to-the-network-security-group).

Her iki adım tamamlandıktan sonra, örnek oluşturma sırasında sağlanmış olan genel bir uç nokta adresini, bağlantı noktasını ve kimlik bilgilerini kullanarak örneğe bağlanabilirsiniz. 

### <a name="enable-the-public-endpoint-for-the-instance"></a>Örnek için genel uç noktayı etkinleştirme

Bir örnek için genel bitiş noktasının etkinleştirilmesi Azure portal aracılığıyla veya aşağıdaki PowerShell komutu kullanılarak yapılabilir:


```powershell
$instanceOne | Set-AzSqlInstance -InstancePoolName "pool-mi-001" -PublicDataEndpointEnabled $true
```

Bu parametre, örnek oluşturma sırasında da ayarlanabilir.

### <a name="add-an-inbound-rule-to-the-network-security-group"></a>Ağ güvenlik grubuna bir gelen kuralı ekleme 

Bu adım Azure portal veya PowerShell komutları kullanılarak yapılabilir ve alt ağ yönetilen örnek için hazırlandıktan sonra herhangi bir zamanda yapılabilir.

Ayrıntılar için bkz. [ağ güvenlik grubunda genel uç nokta trafiğine Izin verme](sql-database-managed-instance-public-endpoint-configure.md#allow-public-endpoint-traffic-on-the-network-security-group).


## <a name="move-an-existing-single-instance-inside-an-instance-pool"></a>Bir örnek havuzunun içindeki mevcut bir örneği taşıyın 
 
Örnekleri bir havuzun içine ve dışına taşımak, genel önizleme sınırlamalarından biridir. Kullanılabilecek bir geçici çözüm, havuzun dışındaki bir örnekten bir havuzda bulunan bir örnekten veritabanlarının zaman içindeki bir noktaya geri yüklemesini temel alır. 

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

4. Uygulamanızı yeni örneğe getirin ve iş yüklerinin yeniden sürdürülemez.

Birden çok veritabanı varsa, her veritabanı için işlemi yineleyin.


## <a name="next-steps"></a>Sonraki adımlar

- Özellikler ve karşılaştırma listesi için bkz. [SQL ortak özellikleri](sql-database-features.md).
- VNet yapılandırması hakkında daha fazla bilgi için bkz. [yönetilen örnek VNET yapılandırması](sql-database-managed-instance-connectivity-architecture.md).
- Yönetilen bir örnek oluşturan ve bir veritabanını bir yedekleme dosyasından geri yükleyen hızlı başlangıç için bkz. [yönetilen örnek oluşturma](sql-database-managed-instance-get-started.md).
- Geçiş için Azure veritabanı geçiş hizmeti 'ni (DMS) kullanan bir öğretici için bkz. [DMS kullanarak yönetilen örnek geçişi](../dms/tutorial-sql-server-to-managed-instance.md).
- Yerleşik sorun giderme zekası ile yönetilen örnek veritabanı performansının gelişmiş izlenmesi için bkz. [Azure SQL Analytics kullanarak Azure SQL veritabanını izleme](../azure-monitor/insights/azure-sql.md).
- Fiyatlandırma bilgileri için bkz. [SQL veritabanı yönetilen örnek fiyatlandırması](https://azure.microsoft.com/pricing/details/sql-database/managed/).
