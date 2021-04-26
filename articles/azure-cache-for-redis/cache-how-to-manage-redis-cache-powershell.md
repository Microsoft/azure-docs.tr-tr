---
title: Redsıs için Azure önbelleğini Azure PowerShell ile yönetme
description: Azure PowerShell kullanarak redin için Azure önbelleği için yönetim görevleri gerçekleştirmeyi öğrenin.
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 07/13/2017
ms.author: yegu
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: ac1456e2dc640e1076857da78cf4145b61ea69d4
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107832815"
---
# <a name="manage-azure-cache-for-redis-with-azure-powershell"></a>Redsıs için Azure önbelleğini Azure PowerShell ile yönetme
> [!div class="op_single_selector"]
> * [PowerShell](cache-how-to-manage-redis-cache-powershell.md)
> * [Azure CLI](cache-manage-cli.md)
> 
> 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Bu konu başlığı altında, Redsıs örnekleri için Azure önbelleğinizi oluşturma, güncelleştirme ve ölçeklendirme, erişim anahtarlarını yeniden oluşturma ve önbelleğiniz hakkındaki bilgileri görüntüleme gibi genel görevlerin nasıl gerçekleştirileceği gösterilmektedir. Redsıs PowerShell cmdlet 'lerinin Azure önbelleğinin tüm listesi için bkz. [redsıs cmdlet 'leri Için Azure önbelleği](/powershell/module/az.rediscache).

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

Klasik dağıtım modeli hakkında daha fazla bilgi için bkz [. Azure Resource Manager ile klasik dağıtım: dağıtım modellerini ve kaynaklarınızın durumunu anlayın](../azure-resource-manager/management/deployment-models.md).

## <a name="prerequisites"></a>Önkoşullar
Azure PowerShell zaten yüklediyseniz, Azure PowerShell sürüm 1.0.0 veya daha yeni bir sürüme sahip olmanız gerekir. Bu komutla yüklediğiniz Azure PowerShell sürümünü Azure PowerShell komut isteminde kontrol edebilirsiniz.

```azurepowershell
    Get-Module Az | format-table version
```

İlk olarak, bu komutla Azure 'da oturum açmalısınız.

```azurepowershell
    Connect-AzAccount
```

Microsoft Azure oturum açma iletişim kutusunda Azure hesabınızın ve parolasının e-posta adresini belirtin.

Daha sonra, birden fazla Azure aboneliğiniz varsa, Azure aboneliğinizi ayarlamanız gerekir. Geçerli aboneliklerinizin listesini görmek için bu komutu çalıştırın.

```azurepowershell
    Get-AzSubscription | sort SubscriptionName | Select SubscriptionName
```

Aboneliği belirtmek için aşağıdaki komutu çalıştırın. Aşağıdaki örnekte, abonelik adı `ContosoSubscription` .

```azurepowershell
    Select-AzSubscription -SubscriptionName ContosoSubscription
```

Windows PowerShell 'i Azure Resource Manager kullanabilmeniz için aşağıdakiler gereklidir:

* Windows PowerShell, sürüm 3,0 veya 4,0. Windows PowerShell sürümünü bulmak için, şunu yazın: `$PSVersionTable` değerini `PSVersion` 3,0 veya 4,0 olarak doğrulayın. Uyumlu bir sürüm yüklemek için bkz. [Windows Management Framework 3,0](https://www.microsoft.com/download/details.aspx?id=34595).

Bu öğreticide gördüğünüz herhangi bir cmdlet hakkında ayrıntılı yardım almak için Get-Help cmdlet 'ini kullanın.

```azurepowershell
    Get-Help <cmdlet-name> -Detailed
```

Örneğin, cmdlet 'i hakkında yardım almak için `New-AzRedisCache` şunu yazın:

```azurepowershell
    Get-Help New-AzRedisCache -Detailed
```

### <a name="how-to-connect-to-other-clouds"></a>Diğer bulutlara bağlanma
Varsayılan olarak, Azure ortamı `AzureCloud` Genel Azure bulut örneğini temsil eder. Farklı bir örneğe bağlanmak için `Connect-AzAccount` komutunu, `-Environment` `EnvironmentName` istenen ortam veya ortam adına sahip veya-komut satırı anahtarı ile birlikte kullanın.

Kullanılabilir ortamların listesini görmek için `Get-AzEnvironment` cmdlet 'ini çalıştırın.

### <a name="to-connect-to-the-azure-government-cloud"></a>Azure Kamu bulutuna bağlanmak için
Azure Kamu bulutuna bağlanmak için aşağıdaki komutlardan birini kullanın.

```azurepowershell
    Connect-AzAccount -EnvironmentName AzureUSGovernment
```

veya

```azurepowershell
    Connect-AzAccount -Environment (Get-AzEnvironment -Name AzureUSGovernment)
```

Azure Kamu bulutunda bir önbellek oluşturmak için aşağıdaki konumlardan birini kullanın.

* USGov Virginia
* USGov Iowa

Azure Kamu Bulutu hakkında daha fazla bilgi için bkz. [Microsoft Azure Kamu](https://azure.microsoft.com/features/gov/) ve [Microsoft Azure Kamu Geliştirici Kılavuzu](../azure-government/documentation-government-developer-guide.md).

### <a name="to-connect-to-the-azure-china-cloud"></a>Azure Çin bulutuna bağlanmak için
Azure Çin bulutuna bağlanmak için aşağıdaki komutlardan birini kullanın.

```azurepowershell
    Connect-AzAccount -EnvironmentName AzureChinaCloud
```

veya

```azurepowershell
    Connect-AzAccount -Environment (Get-AzEnvironment -Name AzureChinaCloud)
```

Azure Çin bulutunda bir önbellek oluşturmak için aşağıdaki konumlardan birini kullanın.

* Doğu Çin
* Kuzey Çin

Azure Çin bulutu hakkında daha fazla bilgi için bkz. [Çin 'de 21Vianet tarafından işletilen Azure Için AzureChinaCloud](https://www.windowsazure.cn/).

### <a name="to-connect-to-microsoft-azure-germany"></a>Microsoft Azure Almanya bağlanmak için
Microsoft Azure Almanya bağlanmak için aşağıdaki komutlardan birini kullanın.

```azurepowershell
    Connect-AzAccount -EnvironmentName AzureGermanCloud
```

veya

```azurepowershell
    Connect-AzAccount -Environment (Get-AzEnvironment -Name AzureGermanCloud)
```

Microsoft Azure Almanya bir önbellek oluşturmak için aşağıdaki konumlardan birini kullanın.

* Orta Almanya
* Kuzeydoğu Almanya

Microsoft Azure Almanya hakkında daha fazla bilgi için bkz. [Microsoft Azure Almanya](https://azure.microsoft.com/overview/clouds/germany/).

### <a name="properties-used-for-azure-cache-for-redis-powershell"></a>Redsıs PowerShell için Azure önbelleği için kullanılan özellikler
Aşağıdaki tabloda, Azure PowerShell kullanarak Redsıs örnekleri için Azure önbelleğinizi oluştururken ve yönetirken yaygın olarak kullanılan parametrelere yönelik özellikler ve açıklamalar yer almaktadır.

| Parametre | Açıklama | Varsayılan |
| --- | --- | --- |
| Name |Önbelleğin adı | |
| Konum |Önbelleğin konumu | |
| ResourceGroupName |Önbelleğin oluşturulacağı kaynak grubu adı | |
| Boyut |Önbelleğin boyutu. Geçerli değerler şunlardır: P1, P2, P3, P4, C0, C1, C2, C3, C4, C5, C6, 250MB, 1 GB, 2,5 GB, 6GB, 13GB, 26GB, 53GB |GB |
| ShardCount |Kümelendirmeyi etkin bir Premium önbellek oluştururken oluşturulacak parça sayısı. Geçerli değerler şunlardır: 1, 2, 3, 4, 5, 6, 7, 8, 9, 10 | |
| SKU |Önbelleğin SKU 'sunu belirtir. Geçerli değerler şunlardır: temel, standart, Premium |Standart |
| RedisConfiguration |Redsıs yapılandırma ayarlarını belirtir. Her bir ayar hakkında ayrıntılar için aşağıdaki [Redisconfiguration Özellikler](#redisconfiguration-properties) tablosuna bakın. | |
| EnableNonSslPort |SSL olmayan bağlantı noktasının etkinleştirilip etkinleştirilmeyeceğini belirtir. |Yanlış |
| MaxMemoryPolicy |Bu parametre kullanım dışı bırakıldı. bunun yerine RedisConfiguration kullanın. | |
| Staticıp |Önbelleğinizi VNET 'te barındırırken, önbelleğin alt ağında benzersiz bir IP adresi belirtir. Sağlanmazsa, alt ağdan bir tane seçilir. | |
| Alt ağ |Önbelleğinizi VNET 'te barındırırken, önbelleğin dağıtılacağı alt ağın adını belirtir. | |
| VirtualNetwork |Önbelleğinizi VNET 'te barındırırken, önbelleğin dağıtılacağı sanal ağın kaynak KIMLIĞINI belirtir. | |
| Anahtar |Erişim anahtarları yenilenirken hangi erişim anahtarının yeniden oluşturulması gerektiğini belirtir. Geçerli değerler şunlardır: birincil, Ikincil | |

### <a name="redisconfiguration-properties"></a>RedisConfiguration özellikleri
| Özellik | Açıklama | Fiyatlandırma katmanları |
| --- | --- | --- |
| RDB-yedekleme-etkin |[Redsıs veri kalıcılığının](cache-how-to-premium-persistence.md) etkin olup olmadığı |Yalnızca Premium |
| RDB-depolama-bağlantı-dize |[Redsıs veri kalıcılığı](cache-how-to-premium-persistence.md) için depolama hesabına yönelik bağlantı dizesi |Yalnızca Premium |
| RDB-yedekleme sıklığı |[Redsıs veri kalıcılığı](cache-how-to-premium-persistence.md) için yedekleme sıklığı |Yalnızca Premium |
| MaxMemory-ayrılmış |Önbelleğe alınmamış süreçler için [ayrılan belleği](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) yapılandırır |Standart ve Premium |
| MaxMemory-ilke |Önbellek için [çıkarma ilkesini](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) yapılandırır |Tüm fiyatlandırma katmanları |
| bildirim-keyspace-olaylar |[Anahtar alanı bildirimlerini](cache-configure.md#keyspace-notifications-advanced-settings) yapılandırır |Standart ve Premium |
| Karma-en yüksek-ZipList-girdileri |Küçük Toplam veri türleri için [bellek iyileştirmeyi](https://redis.io/topics/memory-optimization) yapılandırır |Standart ve Premium |
| Hash-Max-ZipList-değer |Küçük Toplam veri türleri için [bellek iyileştirmeyi](https://redis.io/topics/memory-optimization) yapılandırır |Standart ve Premium |
| Set-Max-intset-Entries |Küçük Toplam veri türleri için [bellek iyileştirmeyi](https://redis.io/topics/memory-optimization) yapılandırır |Standart ve Premium |
| zset-Max-ZipList-Entries |Küçük Toplam veri türleri için [bellek iyileştirmeyi](https://redis.io/topics/memory-optimization) yapılandırır |Standart ve Premium |
| zset-Max-ZipList-değer |Küçük Toplam veri türleri için [bellek iyileştirmeyi](https://redis.io/topics/memory-optimization) yapılandırır |Standart ve Premium |
| veritabanlarında |Veritabanlarının sayısını yapılandırır. Bu özellik yalnızca önbellek oluşturma sırasında yapılandırılabilir. |Standart ve Premium |

## <a name="to-create-an-azure-cache-for-redis"></a>Redsıs için bir Azure önbelleği oluşturmak için
Redsıs örnekleri için yeni Azure önbelleği, [New-AzRedisCache](/powershell/module/az.rediscache/new-azrediscache) cmdlet 'i kullanılarak oluşturulur.

> [!IMPORTANT]
> Azure portal kullanarak bir abonelikte redin için Azure önbelleği ilk kez oluşturduğunuzda, portal `Microsoft.Cache` bu aboneliğin ad alanını kaydeder. PowerShell kullanarak bir abonelikte Redsıs için ilk Azure önbelleğini oluşturmaya çalışırsanız, önce bu ad alanını aşağıdaki komutu kullanarak kaydetmeniz gerekir; Aksi halde, ve gibi cmdlet 'leri `New-AzRedisCache` `Get-AzRedisCache` .
> 
> `Register-AzResourceProvider -ProviderNamespace "Microsoft.Cache"`
> 
> 

Kullanılabilir parametrelerin ve bunların açıklamalarının listesini görmek için `New-AzRedisCache` aşağıdaki komutu çalıştırın.

```azurepowershell
    PS C:\> Get-Help New-AzRedisCache -detailed

    NAME
        New-AzRedisCache

    SYNOPSIS
        Creates a new Azure Cache for Redis.


    SYNTAX
        New-AzRedisCache -Name <String> -ResourceGroupName <String> -Location <String> [-RedisVersion <String>]
        [-Size <String>] [-Sku <String>] [-MaxMemoryPolicy <String>] [-RedisConfiguration <Hashtable>] [-EnableNonSslPort
        <Boolean>] [-ShardCount <Integer>] [-VirtualNetwork <String>] [-Subnet <String>] [-StaticIP <String>]
        [<CommonParameters>]


    DESCRIPTION
        The New-AzRedisCache cmdlet creates a new Azure Cache for Redis.


    PARAMETERS
        -Name <String>
            Name of the Azure Cache for Redis to create.

        -ResourceGroupName <String>
            Name of resource group in which to create the Azure Cache for Redis.

        -Location <String>
            Location in which to create the Azure Cache for Redis.

        -RedisVersion <String>
            RedisVersion is deprecated and will be removed in future release.

        -Size <String>
            Size of the Azure Cache for Redis. The default value is 1GB or C1. Possible values are P1, P2, P3, P4, C0, C1, C2, C3,
            C4, C5, C6, 250MB, 1GB, 2.5GB, 6GB, 13GB, 26GB, 53GB.

        -Sku <String>
            Sku of Azure Cache for Redis. The default value is Standard. Possible values are Basic, Standard and Premium.

        -MaxMemoryPolicy <String>
            The 'MaxMemoryPolicy' setting has been deprecated. Please use 'RedisConfiguration' setting to set
            MaxMemoryPolicy. e.g. -RedisConfiguration @{"maxmemory-policy" = "allkeys-lru"}

        -RedisConfiguration <Hashtable>
            All Redis Configuration Settings. Few possible keys: rdb-backup-enabled, rdb-storage-connection-string,
            rdb-backup-frequency, maxmemory-reserved, maxmemory-policy, notify-keyspace-events, hash-max-ziplist-entries,
            hash-max-ziplist-value, set-max-intset-entries, zset-max-ziplist-entries, zset-max-ziplist-value, databases.

        -EnableNonSslPort <Boolean>
            EnableNonSslPort is used by Azure Cache for Redis. If no value is provided, the default value is false and the
            non-SSL port will be disabled. Possible values are true and false.

        -ShardCount <Integer>
            The number of shards to create on a Premium Cluster Cache.

        -VirtualNetwork <String>
            The exact ARM resource ID of the virtual network to deploy the Azure Cache for Redis in. Example format: /subscriptions/{
            subid}/resourceGroups/{resourceGroupName}/providers/Microsoft.ClassicNetwork/VirtualNetworks/{vnetName}

        -Subnet <String>
            Required when deploying an Azure Cache for Redis inside an existing Azure Virtual Network.

        -StaticIP <String>
            Required when deploying an Azure Cache for Redis inside an existing Azure Virtual Network.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).
```

Varsayılan parametrelerle bir önbellek oluşturmak için aşağıdaki komutu çalıştırın.

```azurepowershell
    New-AzRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US"
```

`ResourceGroupName`, `Name` ve `Location` gerekli parametrelerdir, ancak geri kalan değer isteğe bağlıdır ve varsayılan değerleri vardır. Önceki komutun çalıştırılması, belirtilen ad, konum ve kaynak grubu ile birlikte, SSL olmayan bağlantı noktası devre dışı bırakılmış şekilde 1 GB olan Redsıs örneği için standart SKU Azure önbelleği oluşturur.

Premium önbellek oluşturmak için P1 (6 GB-60 GB), P2 (13 GB-130 GB), P3 (26 GB-260 GB) veya P4 (53 GB-530 GB) boyutunu belirtin. Kümelemeyi etkinleştirmek için parametresini kullanarak bir parça sayısı belirtin `ShardCount` . Aşağıdaki örnek 3 parça içeren bir P1 Premium önbellek oluşturur. P1 Premium önbelleği 6 GB boyutdur ve bu yana üç parça belirttiğimiz için toplam boyut 18 GB (3 x 6 GB).

```azurepowershell
    New-AzRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -Sku Premium -Size P1 -ShardCount 3
```

Parametresinin değerlerini belirtmek için `RedisConfiguration` değerleri `{}` gibi anahtar/değer çiftleri olarak içine koyun `@{"maxmemory-policy" = "allkeys-random", "notify-keyspace-events" = "KEA"}` . Aşağıdaki örnek, `allkeys-random` MaxMemory ilkesi ve ile yapılandırılmış anahtar alanı bildirimleri içeren standart bir 1 GB önbellek oluşturur `KEA` . Daha fazla bilgi için bkz. [keyspace bildirimleri (Gelişmiş ayarlar)](cache-configure.md#keyspace-notifications-advanced-settings) ve [bellek ilkeleri](cache-configure.md#memory-policies).

```azurepowershell
    New-AzRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -RedisConfiguration @{"maxmemory-policy" = "allkeys-random", "notify-keyspace-events" = "KEA"}
```

<a name="databases"></a>

## <a name="to-configure-the-databases-setting-during-cache-creation"></a>Önbellek oluşturma sırasında veritabanları ayarını yapılandırmak için
`databases`Ayar yalnızca önbellek oluşturma sırasında yapılandırılabilir. Aşağıdaki örnek, [New-AzRedisCache](/powershell/module/az.rediscache/New-azRedisCache) cmdlet 'ini kullanarak 48 veritabanı Ile Premium P3 (26 GB) önbelleği oluşturur.

```azurepowershell
    New-AzRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -Sku Premium -Size P3 -RedisConfiguration @{"databases" = "48"}
```

Özelliği hakkında daha fazla bilgi için `databases` bkz. [redsıs sunucu yapılandırması Için varsayılan Azure önbelleği](cache-configure.md#default-redis-server-configuration). [New-AzRedisCache](/powershell/module/az.rediscache/new-azrediscache) cmdlet 'ini kullanarak önbellek oluşturma hakkında daha fazla bilgi için bkz. Redsıs Için Azure önbelleği oluşturma bölümüne bakın.

## <a name="to-update-an-azure-cache-for-redis"></a>Redsıs için bir Azure önbelleğini güncelleştirmek için
Redsıs örnekleri için Azure önbelleği, [set-AzRedisCache](/powershell/module/az.rediscache/Set-azRedisCache) cmdlet 'i kullanılarak güncelleştirilir.

Kullanılabilir parametrelerin ve bunların açıklamalarının listesini görmek için `Set-AzRedisCache` aşağıdaki komutu çalıştırın.

```azurepowershell
    PS C:\> Get-Help Set-AzRedisCache -detailed

    NAME
        Set-AzRedisCache

    SYNOPSIS
        Set Azure Cache for Redis updatable parameters.

    SYNTAX
        Set-AzRedisCache -Name <String> -ResourceGroupName <String> [-Size <String>] [-Sku <String>]
        [-MaxMemoryPolicy <String>] [-RedisConfiguration <Hashtable>] [-EnableNonSslPort <Boolean>] [-ShardCount
        <Integer>] [<CommonParameters>]

    DESCRIPTION
        The Set-AzRedisCache cmdlet sets Azure Cache for Redis parameters.

    PARAMETERS
        -Name <String>
            Name of the Azure Cache for Redis to update.

        -ResourceGroupName <String>
            Name of the resource group for the cache.

        -Size <String>
            Size of the Azure Cache for Redis. The default value is 1GB or C1. Possible values are P1, P2, P3, P4, C0, C1, C2, C3,
            C4, C5, C6, 250MB, 1GB, 2.5GB, 6GB, 13GB, 26GB, 53GB.

        -Sku <String>
            Sku of Azure Cache for Redis. The default value is Standard. Possible values are Basic, Standard and Premium.

        -MaxMemoryPolicy <String>
            The 'MaxMemoryPolicy' setting has been deprecated. Please use 'RedisConfiguration' setting to set
            MaxMemoryPolicy. e.g. -RedisConfiguration @{"maxmemory-policy" = "allkeys-lru"}

        -RedisConfiguration <Hashtable>
            All Redis Configuration Settings. Few possible keys: rdb-backup-enabled, rdb-storage-connection-string,
            rdb-backup-frequency, maxmemory-reserved, maxmemory-policy, notify-keyspace-events, hash-max-ziplist-entries,
            hash-max-ziplist-value, set-max-intset-entries, zset-max-ziplist-entries, zset-max-ziplist-value.

        -EnableNonSslPort <Boolean>
            EnableNonSslPort is used by Azure Cache for Redis. The default value is null and no change will be made to the
            currently configured value. Possible values are true and false.

        -ShardCount <Integer>
            The number of shards to create on a Premium Cluster Cache.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).
```

Cmdlet 'i,,, `Set-AzRedisCache` `Size` `Sku` `EnableNonSslPort` ve değerleri gibi özellikleri güncelleştirmek için kullanılabilir `RedisConfiguration` . 

Aşağıdaki komut, myCache adlı Red, için Azure önbelleği için MaxMemory ilkesini güncelleştirir.

```azurepowershell
    Set-AzRedisCache -ResourceGroupName "myGroup" -Name "myCache" -RedisConfiguration @{"maxmemory-policy" = "allkeys-random"}
```

<a name="scale"></a>

## <a name="to-scale-an-azure-cache-for-redis"></a>Redsıs için bir Azure önbelleğini ölçeklendirmek için
`Set-AzRedisCache``Size`,, `Sku` veya özellikleri değiştirildiğinde redsıs örneği Için bir Azure önbelleğini ölçeklendirmek üzere kullanılabilir `ShardCount` . 

> [!NOTE]
> PowerShell kullanarak önbelleğin ölçeklendirilmesi, Azure portal bir önbelleği ölçeklendirmeyle aynı sınırlara ve yönergelere tabidir. Aşağıdaki kısıtlamalara sahip farklı bir fiyatlandırma katmanına ölçeklendirebilirsiniz.
> 
> * Daha yüksek bir fiyatlandırma katmanından daha düşük bir fiyatlandırma katmanına ölçeklendiremez.
> * **Premium** önbellekten bir **Standart** veya **temel** önbelleğe ölçeklendiremez.
> * **Standart** bir önbellekten, **temel** bir önbellekten ölçeklendiremez.
> * **Temel** bir önbellekten **Standart** bir önbelleğe ölçeklendirebilirsiniz, ancak aynı anda boyutu değiştiremezsiniz. Farklı bir boyuta ihtiyacınız varsa, sonraki ölçekleme işlemini istenen boyuta getirebilirsiniz.
> * **Temel** önbellekten doğrudan **Premium** önbelleğe ölçeklendiremez. Bir ölçeklendirme işleminde **temel** ile **Standart** arasında ölçeklendirmeniz ve sonra sonraki ölçekleme işleminde **Standart** 'den **Premium** 'a ölçeklendirmeniz gerekir.
> * Daha büyük bir boyuttan **C0 (250 MB)** boyutuna kadar ölçeklendiremez.
> 
> Daha fazla bilgi için bkz. [redsıs Için Azure önbelleğini ölçeklendirme](cache-how-to-scale.md).
> 
> 

Aşağıdaki örnek, `myCache` 2,5 GB önbellek olarak adlandırılan bir önbelleğin nasıl ölçeklendirilebilen gösterilmektedir. Bu komutun hem temel hem de standart önbellek için çalışıp çalışmadığını unutmayın.

```azurepowershell
    Set-AzRedisCache -ResourceGroupName myGroup -Name myCache -Size 2.5GB
```

Bu komut verildikten sonra, önbelleğin durumu döndürülür (çağırmaya benzer `Get-AzRedisCache` ). Olduğunu unutmayın `ProvisioningState` `Scaling` .

```azurepowershell
    PS C:\> Set-AzRedisCache -Name myCache -ResourceGroupName myGroup -Size 2.5GB


    Name               : mycache
    Id                 : /subscriptions/12ad12bd-abdc-2231-a2ed-a2b8b246bbad4/resourceGroups/mygroup/providers/Mi
                         crosoft.Cache/Redis/mycache
    Location           : South Central US
    Type               : Microsoft.Cache/Redis
    HostName           : mycache.redis.cache.windows.net
    Port               : 6379
    ProvisioningState  : Scaling
    SslPort            : 6380
    RedisConfiguration : {[maxmemory-policy, volatile-lru], [maxmemory-reserved, 150], [notify-keyspace-events, KEA],
                         [maxmemory-delta, 150]...}
    EnableNonSslPort   : False
    RedisVersion       : 3.0
    Size               : 1GB
    Sku                : Standard
    ResourceGroupName  : mygroup
    PrimaryKey         : ....
    SecondaryKey       : ....
    VirtualNetwork     :
    Subnet             :
    StaticIP           :
    TenantSettings     : {}
    ShardCount         :
```

Ölçeklendirme işlemi tamamlandığında, `ProvisioningState` üzerinde yapılan değişiklikler `Succeeded` . Temel ve standart arasında değişen bir ölçeklendirme işlemi yapmanız gerekirse, önceki işlem tamamlanana kadar beklemeniz veya aşağıdakine benzer bir hata almanız gerekir.

```azurepowershell
    Set-AzRedisCache : Conflict: The resource '...' is not in a stable state, and is currently unable to accept the update request.
```

## <a name="to-get-information-about-an-azure-cache-for-redis"></a>Redsıs için bir Azure önbelleği hakkında bilgi almak için
[Get-AzRedisCache](/powershell/module/az.rediscache/get-azrediscache) cmdlet 'ini kullanarak bir önbellek hakkında bilgi alabilirsiniz.

Kullanılabilir parametrelerin ve bunların açıklamalarının listesini görmek için `Get-AzRedisCache` aşağıdaki komutu çalıştırın.

```azurepowershell
    PS C:\> Get-Help Get-AzRedisCache -detailed

    NAME
        Get-AzRedisCache

    SYNOPSIS
        Gets details about a single cache or all caches in the specified resource group or all caches in the current
        subscription.

    SYNTAX
        Get-AzRedisCache [-Name <String>] [-ResourceGroupName <String>] [<CommonParameters>]

    DESCRIPTION
        The Get-AzRedisCache cmdlet gets the details about a cache or caches depending on input parameters. If both
        ResourceGroupName and Name parameters are provided then Get-AzRedisCache will return details about the
        specific cache name provided.

        If only ResourceGroupName is provided than it will return details about all caches in the specified resource group.

        If no parameters are given than it will return details about all caches the current subscription.

    PARAMETERS
        -Name <String>
            The name of the cache. When this parameter is provided along with ResourceGroupName, Get-AzRedisCache
            returns the details for the cache.

        -ResourceGroupName <String>
            The name of the resource group that contains the cache or caches. If ResourceGroupName is provided with Name
            then Get-AzRedisCache returns the details of the cache specified by Name. If only the ResourceGroup
            parameter is provided, then details for all caches in the resource group are returned.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).
```

Geçerli abonelikteki tüm önbellekler hakkında bilgi döndürmek için `Get-AzRedisCache` herhangi bir parametre olmadan öğesini çalıştırın.

```azurepowershell
    Get-AzRedisCache
```

Belirli bir kaynak grubundaki tüm önbellekler hakkında bilgi döndürmek için `Get-AzRedisCache` parametresiyle birlikte çalıştırın `ResourceGroupName` .

```azurepowershell
    Get-AzRedisCache -ResourceGroupName myGroup
```

Belirli bir önbellek hakkında bilgi döndürmek için, `Get-AzRedisCache` `Name` önbelleğin adını içeren parametresiyle ve `ResourceGroupName` Bu önbelleği içeren kaynak grubu ile parametresini çalıştırın.

```azurepowershell
    PS C:\> Get-AzRedisCache -Name myCache -ResourceGroupName myGroup

    Name               : mycache
    Id                 : /subscriptions/12ad12bd-abdc-2231-a2ed-a2b8b246bbad4/resourceGroups/myGroup/providers/Mi
                         crosoft.Cache/Redis/mycache
    Location           : South Central US
    Type               : Microsoft.Cache/Redis
    HostName           : mycache.redis.cache.windows.net
    Port               : 6379
    ProvisioningState  : Succeeded
    SslPort            : 6380
    RedisConfiguration : {[maxmemory-policy, volatile-lru], [maxmemory-reserved, 62], [notify-keyspace-events, KEA],
                         [maxclients, 1000]...}
    EnableNonSslPort   : False
    RedisVersion       : 3.0
    Size               : 1GB
    Sku                : Standard
    ResourceGroupName  : myGroup
    VirtualNetwork     :
    Subnet             :
    StaticIP           :
    TenantSettings     : {}
    ShardCount         :
```

## <a name="to-retrieve-the-access-keys-for-an-azure-cache-for-redis"></a>Redsıs için bir Azure önbelleğinin erişim anahtarlarını almak için
Önbelleğiniz için erişim anahtarlarını almak üzere [Get-AzRedisCacheKey](/powershell/module/az.rediscache/Get-azRedisCacheKey) cmdlet 'ini kullanabilirsiniz.

Kullanılabilir parametrelerin ve bunların açıklamalarının listesini görmek için `Get-AzRedisCacheKey` aşağıdaki komutu çalıştırın.

```azurepowershell
    PS C:\> Get-Help Get-AzRedisCacheKey -detailed

    NAME
        Get-AzRedisCacheKey

    SYNOPSIS
        Gets the accesskeys for the specified Azure Cache for Redis.


    SYNTAX
        Get-AzRedisCacheKey -Name <String> -ResourceGroupName <String> [<CommonParameters>]

    DESCRIPTION
        The Get-AzRedisCacheKey cmdlet gets the access keys for the specified cache.

    PARAMETERS
        -Name <String>
            Name of the Azure Cache for Redis.

        -ResourceGroupName <String>
            Name of the resource group for the cache.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).
```

Önbelleğiniz için anahtarları almak üzere, `Get-AzRedisCacheKey` cmdlet 'ini çağırın ve önbelleğiniz adına önbelleği içeren kaynak grubunun adını geçirin.

```azurepowershell
    PS C:\> Get-AzRedisCacheKey -Name myCache -ResourceGroupName myGroup

    PrimaryKey   : b2wdt43sfetlju4hfbryfnregrd9wgIcc6IA3zAO1lY=
    SecondaryKey : ABhfB757JgjIgt785JgKH9865eifmekfnn649303JKL=
```

## <a name="to-regenerate-access-keys-for-your-azure-cache-for-redis"></a>Redsıs için Azure önbelleğiniz için erişim anahtarlarını yeniden oluşturmak için
Önbelleğiniz için erişim anahtarlarını yeniden oluşturmak üzere [New-AzRedisCacheKey](/powershell/module/az.rediscache/New-azRedisCacheKey) cmdlet 'ini kullanabilirsiniz.

Kullanılabilir parametrelerin ve bunların açıklamalarının listesini görmek için `New-AzRedisCacheKey` aşağıdaki komutu çalıştırın.

```azurepowershell
    PS C:\> Get-Help New-AzRedisCacheKey -detailed

    NAME
        New-AzRedisCacheKey

    SYNOPSIS
        Regenerates the access key of an Azure Cache for Redis.

    SYNTAX
        New-AzRedisCacheKey -Name <String> -ResourceGroupName <String> -KeyType <String> [-Force] [<CommonParameters>]

    DESCRIPTION
        The New-AzRedisCacheKey cmdlet regenerate the access key of an Azure Cache for Redis.

    PARAMETERS
        -Name <String>
            Name of the Azure Cache for Redis.

        -ResourceGroupName <String>
            Name of the resource group for the cache.

        -KeyType <String>
            Specifies whether to regenerate the primary or secondary access key. Possible values are Primary or Secondary.

        -Force
            When the Force parameter is provided, the specified access key is regenerated without any confirmation prompts.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).
```

Önbelleğinizin birincil veya ikincil anahtarını yeniden oluşturmak için `New-AzRedisCacheKey` cmdlet 'ini çağırın ve adı, kaynak grubunu geçirin ve `Primary` parametresi için ya da belirtin `Secondary` `KeyType` . Aşağıdaki örnekte, bir önbelleğin ikincil erişim anahtarı yeniden oluşturulur.

```azurepowershell
    PS C:\> New-AzRedisCacheKey -Name myCache -ResourceGroupName myGroup -KeyType Secondary

    Confirm
    Are you sure you want to regenerate Secondary key for Azure Cache for Redis 'myCache'?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y


    PrimaryKey   : b2wdt43sfetlju4hfbryfnregrd9wgIcc6IA3zAO1lY=
    SecondaryKey : c53hj3kh4jhHjPJk8l0jji785JgKH9865eifmekfnn6=
```

## <a name="to-delete-an-azure-cache-for-redis"></a>Redsıs için bir Azure önbelleğini silmek için
Redsıs için bir Azure önbelleğini silmek için [Remove-AzRedisCache](/powershell/module/az.rediscache/remove-azrediscache) cmdlet 'ini kullanın.

Kullanılabilir parametrelerin ve bunların açıklamalarının listesini görmek için `Remove-AzRedisCache` aşağıdaki komutu çalıştırın.

```azurepowershell
    PS C:\> Get-Help Remove-AzRedisCache -detailed

    NAME
        Remove-AzRedisCache

    SYNOPSIS
        Remove Azure Cache for Redis if exists.

    SYNTAX
        Remove-AzRedisCache -Name <String> -ResourceGroupName <String> [-Force] [-PassThru] [<CommonParameters>

    DESCRIPTION
        The Remove-AzRedisCache cmdlet removes an Azure Cache for Redis if it exists.

    PARAMETERS
        -Name <String>
            Name of the Azure Cache for Redis to remove.

        -ResourceGroupName <String>
            Name of the resource group of the cache to remove.

        -Force
            When the Force parameter is provided, the cache is removed without any confirmation prompts.

        -PassThru
            By default Remove-AzRedisCache removes the cache and does not return any value. If the PassThru par
            is provided then Remove-AzRedisCache returns a boolean value indicating the success of the operatio

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).
```

Aşağıdaki örnekte, adlı önbellek `myCache` kaldırılır.

```azurepowershell
    PS C:\> Remove-AzRedisCache -Name myCache -ResourceGroupName myGroup

    Confirm
    Are you sure you want to remove Azure Cache for Redis 'myCache'?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y
```


## <a name="to-import-an-azure-cache-for-redis"></a>Redsıs için bir Azure önbelleğini içeri aktarmak için
Cmdlet 'ini kullanarak Redsıs örneği için Azure önbelleğine veri aktarabilirsiniz `Import-AzRedisCache` .

> [!IMPORTANT]
> İçeri/dışarı aktarma yalnızca [Premium katman](cache-overview.md#service-tiers) önbellekler için kullanılabilir. Içeri/dışarı aktarma hakkında daha fazla bilgi için bkz. [Redu Için Azure önbelleğindeki verileri içeri ve dışarı aktarma](cache-how-to-import-export-data.md).
> 
> 

Kullanılabilir parametrelerin ve bunların açıklamalarının listesini görmek için `Import-AzRedisCache` aşağıdaki komutu çalıştırın.

```azurepowershell
    PS C:\> Get-Help Import-AzRedisCache -detailed

    NAME
        Import-AzRedisCache

    SYNOPSIS
        Import data from blobs to Azure Cache for Redis.


    SYNTAX
        Import-AzRedisCache -Name <String> -ResourceGroupName <String> -Files <String[]> [-Format <String>] [-Force]
        [-PassThru] [<CommonParameters>]


    DESCRIPTION
        The Import-AzRedisCache cmdlet imports data from the specified blobs into Azure Cache for Redis.


    PARAMETERS
        -Name <String>
            The name of the cache.

        -ResourceGroupName <String>
            The name of the resource group that contains the cache.

        -Files <String[]>
            SAS urls of blobs whose content should be imported into the cache.

        -Format <String>
            Format for the blob.  Currently "rdb" is the only supported, with other formats expected in the future.

        -Force
            When the Force parameter is provided, import will be performed without any confirmation prompts.

        -PassThru
            By default Import-AzRedisCache imports data in cache and does not return any value. If the PassThru
            parameter is provided then Import-AzRedisCache returns a boolean value indicating the success of the
            operation.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).
```


Aşağıdaki komut, SAS URI 'si tarafından belirtilen Blobun verileri Redsıs için Azure önbelleğine aktarır.

```azurepowershell
    PS C:\>Import-AzRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -Files @("https://mystorageaccount.blob.core.windows.net/mycontainername/blobname?sv=2015-04-05&sr=b&sig=caIwutG2uDa0NZ8mjdNJdgOY8%2F8mhwRuGNdICU%2B0pI4%3D&st=2016-05-27T00%3A00%3A00Z&se=2016-05-28T00%3A00%3A00Z&sp=rwd") -Force
```

## <a name="to-export-an-azure-cache-for-redis"></a>Redsıs için bir Azure önbelleğini dışarı aktarmak için
Cmdlet 'ini kullanarak Redsıs örneği için bir Azure önbelleğinden verileri dışarı aktarabilirsiniz `Export-AzRedisCache` .

> [!IMPORTANT]
> İçeri/dışarı aktarma yalnızca [Premium katman](cache-overview.md#service-tiers) önbellekler için kullanılabilir. Içeri/dışarı aktarma hakkında daha fazla bilgi için bkz. [Redu Için Azure önbelleğindeki verileri içeri ve dışarı aktarma](cache-how-to-import-export-data.md).
> 
> 

Kullanılabilir parametrelerin ve bunların açıklamalarının listesini görmek için `Export-AzRedisCache` aşağıdaki komutu çalıştırın.

```azurepowershell
    PS C:\> Get-Help Export-AzRedisCache -detailed

    NAME
        Export-AzRedisCache

    SYNOPSIS
        Exports data from Azure Cache for Redis to a specified container.


    SYNTAX
        Export-AzRedisCache -Name <String> -ResourceGroupName <String> -Prefix <String> -Container <String> [-Format
        <String>] [-PassThru] [<CommonParameters>]


    DESCRIPTION
        The Export-AzRedisCache cmdlet exports data from Azure Cache for Redis to a specified container.


    PARAMETERS
        -Name <String>
            The name of the cache.

        -ResourceGroupName <String>
            The name of the resource group that contains the cache.

        -Prefix <String>
            Prefix to use for blob names.

        -Container <String>
            SAS url of container where data should be exported.

        -Format <String>
            Format for the blob.  Currently "rdb" is the only supported, with other formats expected in the future.

        -PassThru
            By default Export-AzRedisCache does not return any value. If the PassThru parameter is provided
            then Export-AzRedisCache returns a boolean value indicating the success of the operation.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).
```


Aşağıdaki komut, Redsıs örneği için bir Azure önbelleğindeki verileri SAS URI 'si tarafından belirtilen kapsayıcıya dışarı aktarır.

```azurepowershell
    PS C:\>Export-AzRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -Prefix "blobprefix"
    -Container "https://mystorageaccount.blob.core.windows.net/mycontainer?sv=2015-04-05&sr=c&sig=HezZtBZ3DURmEGDduauE7
    pvETY4kqlPI8JCNa8ATmaw%3D&st=2016-05-27T00%3A00%3A00Z&se=2016-05-28T00%3A00%3A00Z&sp=rwdl"
```

## <a name="to-reboot-an-azure-cache-for-redis"></a>Redsıs için bir Azure önbelleğini yeniden başlatmak için
Cmdlet 'ini kullanarak Redsıs örneği için Azure önbelleğinizi yeniden başlatabilirsiniz `Reset-AzRedisCache` .

> [!IMPORTANT]
> Yeniden başlatma yalnızca [Premium katman](cache-overview.md#service-tiers) önbellekler için kullanılabilir. Önbelleğinizi yeniden başlatma hakkında daha fazla bilgi için bkz. [önbellek yönetimi-yeniden başlatma](cache-administration.md#reboot).
> 
> 

Kullanılabilir parametrelerin ve bunların açıklamalarının listesini görmek için `Reset-AzRedisCache` aşağıdaki komutu çalıştırın.

```azurepowershell
    PS C:\> Get-Help Reset-AzRedisCache -detailed

    NAME
        Reset-AzRedisCache

    SYNOPSIS
        Reboot specified node(s) of an Azure Cache for Redis instance.


    SYNTAX
        Reset-AzRedisCache -Name <String> -ResourceGroupName <String> -RebootType <String> [-ShardId <Integer>]
        [-Force] [-PassThru] [<CommonParameters>]


    DESCRIPTION
        The Reset-AzRedisCache cmdlet reboots the specified node(s) of an Azure Cache for Redis instance.


    PARAMETERS
        -Name <String>
            The name of the cache.

        -ResourceGroupName <String>
            The name of the resource group that contains the cache.

        -RebootType <String>
            Which node to reboot. Possible values are "PrimaryNode", "SecondaryNode", "AllNodes".

        -ShardId <Integer>
            Which shard to reboot when rebooting a premium cache with clustering enabled.

        -Force
            When the Force parameter is provided, reset will be performed without any confirmation prompts.

        -PassThru
            By default Reset-AzRedisCache does not return any value. If the PassThru parameter is provided
            then Reset-AzRedisCache returns a boolean value indicating the success of the operation.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).
```


Aşağıdaki komut belirtilen önbelleğin her iki düğümünü de yeniden başlatır.

```azurepowershell
    PS C:\>Reset-AzRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -RebootType "AllNodes"
    -Force
```


## <a name="next-steps"></a>Sonraki adımlar
Azure ile Windows PowerShell kullanma hakkında daha fazla bilgi edinmek için aşağıdaki kaynaklara bakın:

* [MSDN 'de Redsıs cmdlet belgeleri için Azure önbelleği](/powershell/module/az.rediscache)
* [Azure Resource Manager cmdlet 'leri](/powershell/module/): Azure Resource Manager modülündeki cmdlet 'leri kullanmayı öğrenin.
* [Azure kaynaklarınızı yönetmek Için kaynak gruplarını kullanma](../azure-resource-manager/templates/deploy-portal.md): Azure Portal kaynak grupları oluşturma ve yönetme hakkında bilgi edinin.
* [Azure blogu](https://azure.microsoft.com/blog/): Azure 'daki yeni özellikler hakkında bilgi edinin.
* [Windows PowerShell blogu](https://devblogs.microsoft.com/powershell/): Windows PowerShell 'de yeni özellikler hakkında bilgi edinin.
* ["Hey, betik Guy!" Blog](https://devblogs.microsoft.com/scripting/tag/hey-scripting-guy/): Windows PowerShell Community 'den gerçek dünya ipuçları ve püf noktaları alın.
