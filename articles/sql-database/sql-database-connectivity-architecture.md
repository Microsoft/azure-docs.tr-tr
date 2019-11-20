---
title: Bağlantı mimarisi
description: Bu belgede, Azure içinden veya Azure dışından veritabanı bağlantıları için Azure SQL bağlantı mimarisi açıklanmaktadır.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: fasttrack-edit
titleSuffix: Azure SQL Database and SQL Data Warehouse
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: carlrab, vanto
ms.date: 07/02/2019
ms.openlocfilehash: 2140216a27d9c903495da4f7b43f6fdfda62591e
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73826903"
---
# <a name="azure-sql-connectivity-architecture"></a>Azure SQL bağlantı mimarisi

Bu makalede, Azure SQL veritabanı ve SQL veri ambarı bağlantı mimarisinin yanı sıra farklı bileşenlerin Azure SQL örneğinize trafiği nasıl yönlendirdiği açıklanmaktadır. Bu bağlantı bileşenleri, Azure içinden ve Azure dışından bağlanan istemcilerle bağlantı kurarak Azure SQL veritabanı veya SQL veri ambarı 'na ağ trafiğini yönlendirmek için işlev sağlar. Bu makalede ayrıca bağlantının nasıl gerçekleştiği ve varsayılan bağlantı ayarlarını değiştirmekle ilgili önemli noktalar yer almaktadır.

## <a name="connectivity-architecture"></a>Bağlantı mimarisi

Aşağıdaki diyagramda Azure SQL veritabanı bağlantı mimarisine yönelik yüksek düzeyde bir genel bakış sunulmaktadır.

![mimariye genel bakış](./media/sql-database-connectivity-architecture/connectivity-overview.png)

Aşağıdaki adımlarda, bir bağlantının Azure SQL veritabanına nasıl kurulduğu açıklanır:

- İstemciler, genel bir IP adresine sahip olan ağ geçidine bağlanır ve 1433 numaralı bağlantı noktasını dinler.
- Ağ Geçidi, etkin bağlantı ilkesine bağlı olarak, trafiği doğru veritabanı kümesine yönlendirir veya proxy 'ye yönlendirir.
- Veritabanı kümesi trafiğinin içinde uygun Azure SQL veritabanına iletilir.

## <a name="connection-policy"></a>Bağlantı ilkesi

Azure SQL veritabanı, SQL veritabanı sunucusunun bağlantı ilkesi ayarı için aşağıdaki üç seçeneği destekler:

- **Yeniden yönlendir (önerilir):** İstemciler, veritabanını barındıran düğüme doğrudan bağlantı kurar. Bağlantıyı etkinleştirmek için istemciler, ağ güvenlik gruplarını (NSG), bağlantı 1433 noktası 11000-11999 ' de yalnızca Azure SQL veritabanı ağ geçidi IP adreslerini değil, bağlantı noktaları için [hizmet etiketleriyle](../virtual-network/security-overview.md#service-tags) birlikte giden güvenlik duvarı kurallarına izin vermelidir. Paketler doğrudan veritabanına gittiğinden gecikme süresi ve aktarım hızı performansı artırılmıştır.
- **Proxy:** Bu modda, tüm bağlantılar Azure SQL veritabanı ağ geçitleri aracılığıyla proxy olarak kullanılır. Bağlantıyı etkinleştirmek için, istemcinin yalnızca Azure SQL veritabanı ağ geçidi IP adreslerine (genellikle bölge başına iki IP adresi) izin veren giden güvenlik duvarı kurallarına sahip olması gerekir. Bu modun seçilmesi, iş yükünün doğasına bağlı olarak daha yüksek gecikme süresine ve düşük aktarım hızına yol açabilir. En düşük gecikme süresi ve en yüksek aktarım hızı için `Proxy` bağlantı ilkesi üzerinde `Redirect` bağlantı ilkesini kesinlikle öneririz.
- **Varsayılan:** Bu, bağlantı ilkesini açıkça `Proxy` veya `Redirect`olarak değiştirmediğiniz takdirde, oluşturulduktan sonra tüm sunucularda etkin olan bağlantı ilkesidir. Etkin ilke, bağlantıların Azure (`Redirect`) veya Azure dışında (`Proxy`) kaynaklanıp kaynaklanmadığını bağlıdır.

## <a name="connectivity-from-within-azure"></a>Azure içinden bağlantı

Azure içinden bağlanıyorsanız bağlantılarınız, varsayılan olarak `Redirect` bağlantı ilkesine sahiptir. `Redirect` ilkesi, TCP oturumu Azure SQL veritabanı 'na kurulduktan sonra, istemci oturumu doğru veritabanı kümesine Azure SQL veritabanı ağ geçidindeki hedef sanal IP 'ye yapılan bir değişikliğe yeniden yönlendirilir. içi. Bundan sonra, Azure SQL veritabanı ağ geçidini atlayarak sonraki tüm paketler doğrudan kümeye akar. Aşağıdaki diyagramda bu trafik akışı gösterilmektedir.

![mimariye genel bakış](./media/sql-database-connectivity-architecture/connectivity-azure.png)

## <a name="connectivity-from-outside-of-azure"></a>Azure dışından bağlantı

Azure dışından bağlanıyorsanız, bağlantılarınızın varsayılan olarak `Proxy` bağlantı ilkesi vardır. `Proxy` ilkesi, TCP oturumunun Azure SQL veritabanı ağ geçidi aracılığıyla ve sonraki tüm paketlerin ağ geçidiyle akış üzerinden kurulduğu anlamına gelir. Aşağıdaki diyagramda bu trafik akışı gösterilmektedir.

![mimariye genel bakış](./media/sql-database-connectivity-architecture/connectivity-onprem.png)

## <a name="azure-sql-database-gateway-ip-addresses"></a>Azure SQL Veritabanı ağ geçidi IP adresleri

Aşağıdaki tabloda bölgeye göre ağ geçitlerinin IP adresleri listelenir. Bir Azure SQL veritabanına bağlanmak için ağ trafiğinin bölge için **Tüm** ağ geçitlerinden & izin vermeniz gerekir.

Trafiğin belirli bölgelerde yeni ağ geçitlerine nasıl geçirilme ayrıntıları aşağıdaki makalede verilmiştir: [Azure SQL veritabanı trafiğini daha yeni ağ geçitlerine geçirme](sql-database-gateway-migration.md)


| Bölge Adı          | Ağ geçidi IP adresleri |
| --- | --- |
| Avustralya Orta    | 20.36.105.0 |
| Avustralya Central2   | 20.36.113.0 |
| Avustralya Doğu       | 13.75.149.87, 40.79.161.1 |
| Avustralya Güneydoğu | 191.239.192.109, 13.73.109.251 |
| Güney Brezilya         | 104.41.11.5, 191.233.200.14 |
| Orta Kanada       | 40.85.224.249      |
| Doğu Kanada          | 40.86.226.166      |
| Orta ABD           | 13.67.215.62, 52.182.137.15, 23.99.160.139, 104.208.16.96, 104.208.21.1 | 
| Çin Doğu           | 139.219.130.35     |
| Çin Doğu 2         | 40.73.82.1         |
| Çin Kuzey          | 139.219.15.17      |
| Çin Kuzey 2        | 40.73.50.0         |
| Doğu Asya            | 191.234.2.139, 52.175.33.150, 13.75.32.4 |
| Doğu ABD              | 40.121.158.30, 40.79.153.12, 191.238.6.43, 40.78.225.32 |
| Doğu ABD 2            | 40.79.84.180, 52.177.185.181, 52.167.104.0, 191.239.224.107, 104.208.150.3 | 
| Fransa Orta       | 40.79.137.0, 40.79.129.1 |
| Almanya Orta      | 51.4.144.100       |
| Almanya Kuzey Doğu   | 51.5.144.179       |
| Hindistan Orta        | 104.211.96.159     |
| Hindistan Güney          | 104.211.224.146    |
| Hindistan Batı           | 104.211.160.80     |
| Japonya Doğu           | 13.78.61.196, 40.79.184.8, 13.78.106.224, 191.237.240.43, 40.79.192.5 | 
| Japonya Batı           | 104.214.148.156, 40.74.100.192, 191.238.68.11, 40.74.97.10 | 
| Kore Orta        | 52.231.32.42       |
| Kore Güney          | 52.231.200.86      |
| Orta Kuzey ABD     | 23.96.178.199, 23.98.55.75, 52.162.104.33 |
| Kuzey Avrupa         | 40.113.93.91, 191.235.193.75, 52.138.224.1 | 
| Güney Afrika Kuzey   | 102.133.152.0      |
| Güney Afrika Batı    | 102.133.24.0       |
| Orta Güney ABD     | 13.66.62.124, 23.98.162.75, 104.214.16.32   | 
| Güneydoğu Asya      | 104.43.15.0, 23.100.117.95, 40.78.232.3   | 
| BAE Orta          | 20.37.72.64        |
| BAE Kuzey            | 65.52.248.0        |
| Birleşik Krallık Güney             | 51.140.184.11      |
| Birleşik Krallık Batı              | 51.141.8.11        |
| Batı Orta ABD      | 13.78.145.25       |
| Batı Avrupa          | 40.68.37.158, 191.237.232.75, 104.40.168.105  |
| Batı ABD              | 104.42.238.205, 23.99.34.75, 13.86.216.196   |
| Batı ABD 2            | 13.66.226.202      |
|                      |                    |

## <a name="change-azure-sql-database-connection-policy"></a>Azure SQL veritabanı bağlantı ilkesini değiştirme

Azure SQL veritabanı sunucusu için Azure SQL veritabanı bağlantı ilkesini değiştirmek için, [Conn-Policy](https://docs.microsoft.com/cli/azure/sql/server/conn-policy) komutunu kullanın.

- Bağlantı ilkeniz `Proxy`olarak ayarlandıysa, tüm ağ paketleri Azure SQL veritabanı ağ geçidi aracılığıyla akar. Bu ayar için, yalnızca Azure SQL veritabanı ağ geçidi IP 'ye giden erişime izin vermeniz gerekir. `Proxy` ayarının kullanılması `Redirect`ayarından daha fazla gecikme süresine sahiptir.
- Bağlantı ilkeniz `Redirect`ayaralıyorsa, tüm ağ paketleri doğrudan veritabanı kümesine akar. Bu ayar için, birden çok IP 'ye giden bir erişime izin vermeniz gerekir.

## <a name="script-to-change-connection-settings-via-powershell"></a>PowerShell aracılığıyla bağlantı ayarlarını değiştirme betiği

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager modülü Azure SQL veritabanı tarafından hala desteklenmektedir, ancak gelecekteki tüm geliştirmeler az. SQL modülüne yöneliktir. Bu cmdlet 'ler için bkz. [Azurerd. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Az Module ve Azurerd modüllerinde komutların bağımsız değişkenleri önemli ölçüde aynıdır. Aşağıdaki betik [Azure PowerShell modülünü](/powershell/azure/install-az-ps)gerektiriyor.

Aşağıdaki PowerShell betiği, bağlantı ilkesinin nasıl değiştirileceğini gösterir.

```powershell
# Get SQL Server ID
$sqlserverid=(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).ResourceId

# Set URI
$id="$sqlserverid/connectionPolicies/Default"

# Get current connection policy
(Get-AzResource -ResourceId $id).Properties.connectionType

# Update connection policy
Set-AzResource -ResourceId $id -Properties @{"connectionType" = "Proxy"} -f
```

## <a name="script-to-change-connection-settings-via-azure-cli"></a>Azure CLı aracılığıyla bağlantı ayarlarını değiştirme betiği

> [!IMPORTANT]
> Bu betik için [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)gereklidir.

### <a name="azure-cli-in-a-bash-shell"></a>Bash kabuğu 'nda Azure CLı

> [!IMPORTANT]
> Bu betik için [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)gereklidir.

Aşağıdaki CLı betiği, bir bash kabuğu 'nda bağlantı ilkesinin nasıl değiştirileceğini gösterir.

```azurecli-interactive
# Get SQL Server ID
sqlserverid=$(az sql server show -n sql-server-name -g sql-server-group --query 'id' -o tsv)

# Set URI
ids="$sqlserverid/connectionPolicies/Default"

# Get current connection policy
az resource show --ids $ids

# Update connection policy
az resource update --ids $ids --set properties.connectionType=Proxy
```

### <a name="azure-cli-from-a-windows-command-prompt"></a>Windows komut isteminden Azure CLı

> [!IMPORTANT]
> Bu betik için [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)gereklidir.

Aşağıdaki CLı betiği, bir Windows komut isteminden (Azure CLı yüklü) bağlantı ilkesinin nasıl değiştirileceğini gösterir.

```azurecli
# Get SQL Server ID and set URI
FOR /F "tokens=*" %g IN ('az sql server show --resource-group myResourceGroup-571418053 --name server-538465606 --query "id" -o tsv') do (SET sqlserverid=%g/connectionPolicies/Default)

# Get current connection policy
az resource show --ids %sqlserverid%

# Update connection policy
az resource update --ids %sqlserverid% --set properties.connectionType=Proxy
```

## <a name="next-steps"></a>Sonraki adımlar

- Azure SQL veritabanı sunucusu için Azure SQL veritabanı bağlantı ilkesini değiştirme hakkında daha fazla bilgi için bkz. [Conn-Policy](https://docs.microsoft.com/cli/azure/sql/server/conn-policy).
- ADO.NET 4,5 veya sonraki bir sürümünü kullanan istemciler için Azure SQL veritabanı bağlantı davranışı hakkında bilgi için bkz. [1433 sonrasındaki bağlantı noktaları ADO.NET 4,5](sql-database-develop-direct-route-ports-adonet-v12.md).
- Genel uygulama geliştirmeye genel bakış bilgileri için bkz. [SQL veritabanı uygulaması geliştirmeye genel bakış](sql-database-develop-overview.md).
