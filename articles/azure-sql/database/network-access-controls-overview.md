---
title: Ağ erişim denetimleri
titleSuffix: Azure SQL Database & Azure Synapse Analytics
description: Azure SQL veritabanı ve Azure SYNAPSE Analytics için ağ erişimini yönetme ve denetleme konusuna genel bakış.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sqldbrb=3
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto
ms.date: 03/09/2020
ms.openlocfilehash: 90bc57af3aaf0d11cd354bfe7163014f836a72e8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96460011"
---
# <a name="azure-sql-database-and-azure-synapse-analytics-network-access-controls"></a>Azure SQL veritabanı ve Azure SYNAPSE Analytics ağ erişim denetimleri

Azure SQL veritabanı ve Azure SYNAPSE Analytics için [Azure Portal](single-database-create-quickstart.md) BIR mantıksal SQL Server oluşturduğunuzda sonuç, *yourservername.Database.Windows.net* biçiminde genel bir uç noktasıdır.

Ortak uç nokta aracılığıyla bir veritabanına erişime izin vermek için aşağıdaki ağ erişim denetimlerini kullanabilirsiniz:

- Azure hizmetlerine izin ver: açık olarak ayarlandığında Azure sınırının içindeki diğer kaynaklar, örneğin bir Azure sanal makinesi SQL veritabanına erişebilir
- IP güvenlik duvarı kuralları: belirli bir IP adresinden (örneğin, şirket içi makinelerden) bağlantılara açıkça izin vermek için bu özelliği kullanın

Ayrıca, [sanal ağlardan](../../virtual-network/virtual-networks-overview.md) veritabanına özel erişime şu yollarla izin verebilirsiniz:

- Sanal ağ güvenlik duvarı kuralları: Azure sınırları içindeki belirli bir sanal ağdan gelen trafiğe izin vermek için bu özelliği kullanın
- Özel bağlantı: belirli bir sanal ağ içindeki [MANTıKSAL SQL Server](logical-servers.md) için özel bir uç nokta oluşturmak için bu özelliği kullanın

> [!IMPORTANT]
> Bu *Makale,* **SQL yönetilen örneği** için geçerlidir. Ağ yapılandırması hakkında daha fazla bilgi için bkz. [Azure SQL yönetilen örneğine bağlanma](../managed-instance/connect-application-instance.md) .

Bu erişim denetimlerine ilişkin üst düzey bir açıklama ve ne yapacaklarınız için aşağıdaki videoya bakın:

> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Data-Exposed--SQL-Database-Connectivity-Explained/player?WT.mc_id=dataexposed-c9-niner]

## <a name="allow-azure-services"></a>Azure hizmetlerine izin ver

Yeni bir mantıksal SQL Server oluşturma sırasında [Azure Portal](single-database-create-quickstart.md)varsayılan olarak, bu ayar **kapalı** olarak ayarlanır. Bu ayar, genel hizmet uç noktası kullanılarak bağlantıya izin verildiğinde görüntülenir.

Bu ayarı, mantıksal SQL Server aşağıdaki gibi oluşturulduktan sonra güvenlik duvarı bölmesi aracılığıyla da değiştirebilirsiniz.
  
![Sunucu güvenlik duvarını yönetme ekran görüntüsü][2]

**Açık** olarak ayarlandığında sunucunuz Azure sınırının içindeki tüm kaynaklardan gelen iletişimlere izin verir, bu da aboneliğinizin bir parçası olmayabilir veya olmayabilir.

Çoğu durumda, **Açık** ayarı, çoğu müşterinin izin verilenden daha fazla izne sahiptir. Bu ayarı **kapalı** olarak ayarlamak ve daha kısıtlayıcı IP güvenlik duvarı kuralları veya sanal ağ güvenlik duvarı kuralları ile değiştirmek isteyebilirsiniz. 

Ancak bunun yapılması, Azure 'da sanal ağınızın bir parçası olmayan ve bu nedenle veritabanına bir Azure IP adresi aracılığıyla bağlanan sanal makinelerde çalışan aşağıdaki özellikleri etkiler:

### <a name="import-export-service"></a>İçeri aktarma hizmeti al

**Azure hizmetlerine erişime Izin ver** **devre dışı** bırakıldığında içeri aktarma hizmeti çalışmıyor. Bununla birlikte, [bir Azure VM 'den sqlpackage.exe el ile çalıştırarak veya](./database-import-export-azure-services-off.md) DACFX API kullanarak doğrudan kodunuzda dışarı aktarmayı gerçekleştirerek soruna geçici bir çözüm bulabilirsiniz.

### <a name="data-sync"></a>Data Sync

Veri eşitleme özelliğini **Azure hizmetlerine erişime Izin ver** özelliği **kapalı** olarak kullanmak için, **hub** VERITABANıNı barındıran bölgenin **SQL hizmeti etiketinden** [IP adresleri eklemek](firewall-create-server-level-portal-quickstart.md) üzere ayrı ayrı güvenlik duvarı kuralı girdileri oluşturmanız gerekir.
Bu sunucu düzeyi güvenlik duvarı kurallarını hem **hub** 'ı hem de **üye** veritabanlarını (farklı bölgelerde olabilir) barındıran sunuculara ekleyin

Batı ABD bölgenin SQL hizmeti etiketine karşılık gelen IP adreslerini oluşturmak için aşağıdaki PowerShell betiğini kullanın

```powershell
PS C:\>  $serviceTags = Get-AzNetworkServiceTag -Location eastus2
PS C:\>  $sql = $serviceTags.Values | Where-Object { $_.Name -eq "Sql.WestUS" }
PS C:\> $sql.Properties.AddressPrefixes.Count
70
PS C:\> $sql.Properties.AddressPrefixes
13.86.216.0/25
13.86.216.128/26
13.86.216.192/27
13.86.217.0/25
13.86.217.128/26
13.86.217.192/27
```

> [!TIP]
> Get-AzNetworkServiceTag, konum parametresini belirtirken SQL Service etiketinin genel aralığını döndürür. Eşitleme grubunuz tarafından kullanılan Merkez veritabanını barındıran bölgeye filtre uyguladığınızdan emin olun

PowerShell betiğinin çıktısının sınıfsız Inter-Domain yönlendirme (CıDR) gösteriminde olduğunu unutmayın. Bunun gibi [Get-IPrangeStartEnd.ps1](https://gallery.technet.microsoft.com/scriptcenter/Start-and-End-IP-addresses-bcccc3a9) kullanarak bir başlangıç ve bitiş IP adresi biçimine dönüştürülmesi gerekir:

```powershell
PS C:\> Get-IPrangeStartEnd -ip 52.229.17.93 -cidr 26
start        end
-----        ---
52.229.17.64 52.229.17.127
```

CıDR 'ten gelen tüm IP adreslerini başlangıç ve bitiş IP adresi biçimine dönüştürmek için bu ek PowerShell betiğini kullanabilirsiniz.

```powershell
PS C:\>foreach( $i in $sql.Properties.AddressPrefixes) {$ip,$cidr= $i.split('/') ; Get-IPrangeStartEnd -ip $ip -cidr $cidr;}
start          end
-----          ---
13.86.216.0    13.86.216.127
13.86.216.128  13.86.216.191
13.86.216.192  13.86.216.223
```

Artık bunları farklı güvenlik duvarı kuralları olarak ekleyebilir ve sonra **Azure hizmetlerinin sunucuya erişmesine Izin ver**  seçeneğini ayarlayabilirsiniz.

## <a name="ip-firewall-rules"></a>IP güvenlik duvarı kuralları

IP tabanlı güvenlik duvarı, Azure 'daki mantıksal SQL Server 'ın, istemci makinelerin [IP adreslerini açıkça eklemeene](firewall-create-server-level-portal-quickstart.md) kadar sunucunuza tüm erişimleri önleyen bir özelliğidir.

## <a name="virtual-network-firewall-rules"></a>Sanal ağ güvenlik duvarı kuralları

Sunucu güvenlik duvarı, IP kurallarına ek olarak *sanal ağ kurallarını* tanımlamanızı sağlar.  
Daha fazla bilgi edinmek için bkz. [Azure SQL veritabanı Için sanal ağ hizmet uç noktaları ve kuralları](vnet-service-endpoint-rule-overview.md) . bu videoyu izleyin:

> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Data-Exposed--Demo--Vnet-Firewall-Rules-for-SQL-Database/player?WT.mc_id=dataexposed-c9-niner]

### <a name="azure-networking-terminology"></a>Azure ağ terminolojisi

Sanal ağ güvenlik duvarı kurallarını araştırırken aşağıdaki Azure ağ koşullarına dikkat edin

**Sanal ağ:** Azure aboneliğinizle ilişkili sanal ağlarınız olabilir

**Alt ağ:** Bir sanal ağ, **alt ağlar** içerir. Sahip olduğunuz tüm Azure sanal makineleri (VM 'Ler) alt ağlara atanır. Bir alt ağ birden çok VM veya başka işlem düğümü içerebilir. Ağınızı erişime izin verecek şekilde yapılandırmadığınız müddetçe, sanal ağınızın dışındaki işlem düğümleri sanal ağınıza erişemez.

**Sanal ağ hizmeti uç noktası:** [Sanal ağ hizmeti uç noktası](../../virtual-network/virtual-network-service-endpoints-overview.md) , özellik değerleri bir veya daha fazla resmi Azure hizmet türü adı içeren bir alt ağıdır. Bu makalede, SQL veritabanı adlı Azure hizmetine başvuran **Microsoft. SQL** tür adı ile ilgileniyoruz.

**Sanal ağ kuralı:** Sunucunuz için bir sanal ağ kuralı, sunucunuzun erişim denetim listesinde (ACL) listelenen bir alt ağıdır. SQL veritabanında veritabanınızın ACL 'sinde olması için, alt ağ **Microsoft. SQL** tür adını içermelidir. Bir sanal ağ kuralı, sunucunuza alt ağdaki her düğümden gelen iletişimleri kabul etmesini söyler.

## <a name="ip-vs-virtual-network-firewall-rules"></a>IP-sanal ağ güvenlik duvarı kuralları

Azure SQL veritabanı güvenlik duvarı, iletişimlerin SQL veritabanı 'na kabul edileceği IP adresi aralıklarını belirtmenize olanak tanır. Bu yaklaşım, Azure özel ağının dışında olan kararlı IP adresleri için çok uygundur. Ancak, Azure özel ağı içindeki sanal makineler (VM) *dinamik* IP adresleriyle yapılandırılır. Dinamik IP adresleri, VM 'niz yeniden başlatıldığında değişebilir ve IP tabanlı güvenlik duvarı kuralını geçersiz kılar. Bir güvenlik duvarı kuralında, bir üretim ortamında dinamik bir IP adresi belirtmek de bu şekilde yapılır.

SANAL ağınız için bir *statik* IP adresi elde ederek bu sınırlamaya geçici bir çözüm bulabilirsiniz. Ayrıntılar için bkz. [Azure Portal kullanarak statik genel IP adresi olan bir sanal makine oluşturma](../../virtual-network/virtual-network-deploy-static-pip-arm-portal.md). Ancak, statik IP yaklaşımının yönetilmesi zor olabilir ve ölçekteki işlem sırasında maliyetli olur.

Sanal ağ kuralları, VM 'lerinizi içeren belirli bir alt ağdan erişimi kurmak ve yönetmek için daha kolay bir alternatiftir.

> [!NOTE]
> Henüz bir alt ağda SQL veritabanınız olamaz. Sunucunuz sanal ağınızdaki bir alt ağda bulunan bir düğümse, sanal ağ içindeki tüm düğümler SQL veritabanınıza iletişim kurabilir. Bu durumda, VM 'niz herhangi bir sanal ağ kuralına veya IP kuralına gerek duymadan SQL veritabanı ile iletişim kurabilir.

## <a name="private-link"></a>Özel Bağlantı

Özel bağlantı, **özel bir uç nokta** aracılığıyla bir sunucuya bağlanmanızı sağlar. Özel uç nokta, belirli bir [sanal ağ](../../virtual-network/virtual-networks-overview.md) ve alt ağ içindeki özel bir IP adresidir.

## <a name="next-steps"></a>Sonraki adımlar

- Sunucu düzeyinde bir IP güvenlik duvarı kuralı oluşturmaya yönelik hızlı başlangıç için bkz. [SQL veritabanı 'nda veritabanı oluşturma](single-database-create-quickstart.md).

- Sunucu düzeyinde bir sanal ağ güvenlik duvarı kuralı oluşturmaya yönelik hızlı başlangıç için bkz. [Azure SQL veritabanı Için sanal ağ hizmet uç noktaları ve kuralları](vnet-service-endpoint-rule-overview.md).

- Açık kaynaklı veya üçüncü taraf uygulamalardan SQL veritabanındaki bir veritabanına bağlanma konusunda yardım için bkz. [SQL veritabanı Için istemci hızlı başlangıç kodu örnekleri](/previous-versions/azure/ee336282(v=azure.100)).

- Açmanız gerekebilecek ek bağlantı noktaları hakkında daha fazla bilgi için bkz **. SQL veritabanı:** [ADO.NET 4,5 ve SQL veritabanı için 1433 ' den sonraki bağlantı noktalarının](adonet-v12-develop-direct-route-ports.md) iç içe geçmiş bölümü

- Azure SQL veritabanı bağlantısına genel bakış için bkz. [Azure SQL bağlantı mimarisi](connectivity-architecture.md)

- Azure SQL veritabanı güvenliğine genel bakış için bkz. [veritabanınızın güvenliğini sağlama](security-overview.md)

<!--Image references-->
[1]: media/quickstart-create-single-database/new-server2.png
[2]: media/quickstart-create-single-database/manage-server-firewall.png
