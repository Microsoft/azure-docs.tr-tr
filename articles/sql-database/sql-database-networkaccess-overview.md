---
title: Ağ erişim denetimleri
description: Erişimi yönetmek ve tek veya havuza alınmış bir veritabanını yapılandırmak için Azure SQL veritabanı ve veri ambarı için ağ erişim denetimlerine genel bakış.
services: sql-database
ms.service: sql-database
ms.subservice: security
titleSuffix: Azure SQL Database and SQL Data Warehouse
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto
ms.date: 08/05/2019
ms.openlocfilehash: 16de1d9fcf86459b6bcadd9d8c372e436aad0915
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73802932"
---
# <a name="azure-sql-database-and-data-warehouse-network-access-controls"></a>Azure SQL veritabanı ve veri ambarı ağ erişim denetimleri

> [!NOTE]
> Bu makale Azure SQL Server ve Azure SQL Server 'da oluşturulan SQL veritabanı ve SQL veri ambarı veritabanları için geçerlidir. Kolaylık açısından, hem SQL Veritabanı hem de SQL Veri Ambarı için SQL Veritabanı terimi kullanılmaktadır.

> [!IMPORTANT]
> Bu *Makale,* **Azure SQL veritabanı yönetilen örneği**için geçerlidir. ağ yapılandırması hakkında daha fazla bilgi için bkz. [yönetilen örneğe bağlanma](sql-database-managed-instance-connect-app.md) .

[Azure Portal](sql-database-single-database-get-started.md)yeni bir Azure SQL Server oluşturduğunuzda sonuç, *yourservername.Database.Windows.net*biçimindeki genel bir uç noktadır. Tasarıma göre, genel uç noktaya tüm erişim reddedilir. Daha sonra, genel uç nokta aracılığıyla SQl veritabanı erişimine seçmeli olarak izin vermek için aşağıdaki ağ erişim denetimlerini kullanabilirsiniz
- Azure hizmetlerine izin ver:-açık olarak ayarlandığında, Azure sınırının içindeki diğer kaynaklar, örneğin bir Azure sanal makinesi SQL veritabanına erişebilir

- IP güvenlik duvarı kuralları:-Bu özelliği, örneğin şirket içi makinelerden gelen belirli bir IP adresinden gelen bağlantılara açıkça izin vermek için kullanın.

- Sanal ağ güvenlik duvarı kuralları:-Azure sınırları içindeki belirli bir sanal ağdan gelen trafiğe izin vermek için bu özelliği kullanın

> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Data-Exposed--SQL-Database-Connectivity-Explained/player?WT.mc_id=dataexposed-c9-niner]

## <a name="allow-azure-services"></a>Azure hizmetlerine izin ver 
[Azure Portal](sql-database-single-database-get-started.md)yeni bir Azure SQL Server oluşturma sırasında, bu ayar işaretsiz bırakılır.

 ![Yeni sunucu oluşturma ekranının ekran görüntüsü][1]

Bu ayarı, Azure SQL Server aşağıdaki gibi oluşturulduktan sonra güvenlik duvarı bölmesi aracılığıyla da değiştirebilirsiniz.
  
 ![Sunucu güvenlik duvarını yönetme ekran görüntüsü][2]

Azure SQL Server olarak ayarlandığında **, Azure sınırının** içindeki tüm kaynaklardan gelen iletişimlere izin verir, bu da aboneliğinizin bir parçası olmayabilir veya olmayabilir.

Çoğu durumda, **Açık** ayarı, çoğu müşterinin izin verilenden daha fazla izne sahiptir. Bu ayarı **kapalı** olarak ayarlamak ve daha kısıtlayıcı IP güvenlik duvarı kuralları ya da sanal ağ güvenlik duvarı kuralları ile değiştirmek isteyebilir. Bunun yapılması aşağıdaki özellikleri etkiler:

### <a name="import-export-service"></a>İçeri aktarma hizmeti al

Azure SQL veritabanı Içeri aktarma verme hizmeti, Azure 'daki VM 'lerde çalışır. Bu VM 'Ler VNet 'iniz içinde değildir ve bu nedenle veritabanınıza bağlanırken bir Azure IP 'si alın. **Azure hizmetlerinin sunucuya erişmesine Izin ver** kaldırıldığında, bu VM 'ler veritabanlarınıza erişemez.
DACFx API kullanarak doğrudan kodunuzda BACPAC içeri aktarma veya dışarı aktarma işlemini çalıştırarak soruna geçici bir çözüm bulabilirsiniz.

### <a name="sql-database-query-editor"></a>SQL veritabanı sorgu Düzenleyicisi

Azure SQL veritabanı sorgu Düzenleyicisi, Azure 'daki sanal makinelere dağıtılır. Bu VM 'Ler VNet 'iniz içinde değil. Bu nedenle VM 'Ler, veritabanınıza bağlanırken bir Azure IP 'si alır. **Azure hizmetlerinin sunucuya erişmesine Izin ver**kaldırıldığında, bu VM 'ler veritabanlarınıza erişemez.

### <a name="table-auditing"></a>Tablo denetimi

Mevcut olduğunda, SQL veritabanınızda denetimi etkinleştirmenin iki yolu vardır. Azure SQL Server hizmet uç noktalarını etkinleştirdikten sonra tablo denetimi başarısız olur. Burada risk azaltma, blob denetimine geçmadır.

### <a name="impact-on-data-sync"></a>Veri eşitlemeye etkisi

Azure SQL veritabanı, Azure IP 'Leri kullanarak veritabanlarınıza bağlanan veri eşitleme özelliğine sahiptir. Hizmet uç noktalarını kullanırken, Azure hizmetlerinin SQL veritabanı sunucunuza sunucu erişimine erişmesine **Izin ver** ' i devre dışı bırakır ve veri eşitleme özelliğini bozacaksınız.

## <a name="ip-firewall-rules"></a>IP güvenlik duvarı kuralları
IP tabanlı güvenlik duvarı, istemci makinelerin [IP adreslerini açıkça eklemeene](sql-database-server-level-firewall-rule.md) kadar veritabanı sunucunuza tüm erişimi önleyen bir Azure SQL Server özelliğidir.


## <a name="virtual-network-firewall-rules"></a>Sanal ağ güvenlik duvarı kuralları

Azure SQL Server güvenlik duvarı, IP kurallarına ek olarak *sanal ağ kurallarını*tanımlamanızı sağlar.  
Daha fazla bilgi edinmek için bkz. [Azure SQL veritabanı Için sanal ağ hizmet uç noktaları ve kuralları](sql-database-vnet-service-endpoint-rule-overview.md) . bu videoyu izleyin:

> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Data-Exposed--Demo--Vnet-Firewall-Rules-for-SQL-Database/player?WT.mc_id=dataexposed-c9-niner]

 ### <a name="azure-networking-terminology"></a>Azure ağ terminolojisi  
Sanal ağ güvenlik duvarı kurallarını araştırırken aşağıdaki Azure ağ koşullarına dikkat edin

**Sanal ağ:** Azure aboneliğinizle ilişkili sanal ağlarınız olabilir 

**Alt ağ:** Bir sanal ağ, **alt ağlar**içerir. Sahip olduğunuz tüm Azure sanal makineleri (VM 'Ler) alt ağlara atanır. Bir alt ağ birden çok VM veya başka işlem düğümü içerebilir. Ağınızı erişime izin verecek şekilde yapılandırmadığınız müddetçe, sanal ağınızın dışındaki işlem düğümleri sanal ağınıza erişemez.

**Sanal ağ hizmeti uç noktası:** [Sanal ağ hizmeti uç noktası] [VM-sanal-ağ-hizmeti-uç noktaları-genel bakış-649d], özellik değerleri bir veya daha fazla resmi Azure hizmet türü adı içeren bir alt ağ. Bu makalede, SQL veritabanı adlı Azure hizmetine başvuran **Microsoft. SQL**tür adı ile ilgileniyoruz.

**Sanal ağ kuralı:** SQL veritabanı sunucunuz için bir sanal ağ kuralı, SQL veritabanı sunucunuzun erişim denetim listesinde (ACL) listelenen bir alt ağıdır. SQL veritabanınızın ACL 'sinde olması için alt ağın **Microsoft. SQL** tür adını içermesi gerekir. Bir sanal ağ kuralı, SQL veritabanı sunucunuza alt ağdaki her düğümden gelen iletişimleri kabul etmesini söyler.


## <a name="ip-vs-virtual-network-firewall-rules"></a>IP-sanal ağ güvenlik duvarı kuralları

Azure SQL Server güvenlik duvarı, iletişimlerin SQL veritabanı 'na kabul edileceği IP adresi aralıklarını belirtmenize olanak tanır. Bu yaklaşım, Azure özel ağının dışında olan kararlı IP adresleri için çok uygundur. Ancak, Azure özel ağı içindeki sanal makineler (VM) *dinamik* IP adresleriyle yapılandırılır. Dinamik IP adresleri, VM 'niz yeniden başlatıldığında değişebilir ve IP tabanlı güvenlik duvarı kuralını geçersiz kılar. Bir güvenlik duvarı kuralında, bir üretim ortamında dinamik bir IP adresi belirtmek de bu şekilde yapılır.

SANAL ağınız için bir *statik* IP adresi elde ederek bu sınırlamaya geçici bir çözüm bulabilirsiniz. Ayrıntılar için bkz. [Azure portal kullanarak bir sanal makine için özel IP adreslerini yapılandırma] [VM-configure-Private-IP-Addresses--a-sanal-Machine-------------------321W]. Ancak, statik IP yaklaşımının yönetilmesi zor olabilir ve ölçekteki tamamlandığında maliyetli hale gelir. 

Sanal ağ kuralları, VM 'lerinizi içeren belirli bir alt ağdan erişimi kurmak ve yönetmek için daha kolay bir alternatiftir.

> [!NOTE]
> Henüz bir alt ağda SQL veritabanınız olamaz. Azure SQL veritabanı sunucunuz, sanal ağınızdaki bir alt ağda bulunan bir düğümse, sanal ağ içindeki tüm düğümler SQL veritabanınıza iletişim kurabilir. Bu durumda, VM 'niz herhangi bir sanal ağ kuralına veya IP kuralına gerek duymadan SQL veritabanı ile iletişim kurabilir.

## <a name="next-steps"></a>Sonraki adımlar

- Sunucu düzeyinde bir IP güvenlik duvarı kuralı oluşturmaya yönelik hızlı başlangıç için bkz. [Azure SQL veritabanı oluşturma](sql-database-single-database-get-started.md).

- Sunucu düzeyinde VNET güvenlik duvarı kuralı oluşturmaya yönelik hızlı başlangıç için bkz. [Azure SQL veritabanı Için sanal ağ hizmet uç noktaları ve kuralları](sql-database-vnet-service-endpoint-rule-overview.md).

- Açık kaynaklı veya üçüncü taraf uygulamalardan bir Azure SQL veritabanına bağlanma konusunda yardım için bkz. [SQL veritabanı Için istemci hızlı başlangıç kodu örnekleri](https://msdn.microsoft.com/library/azure/ee336282.aspx).

- Açmanız gerekebilecek ek bağlantı noktaları hakkında daha fazla bilgi için bkz **. SQL veritabanı:** [ADO.NET 4,5 ve SQL veritabanı için 1433 ' den sonraki bağlantı noktalarının](sql-database-develop-direct-route-ports-adonet-v12.md) iç içe geçmiş bölümü

- Azure SQL veritabanı bağlantısına genel bakış için bkz. [Azure SQL bağlantı mimarisi](sql-database-connectivity-architecture.md)

- Azure SQL veritabanı güvenliğine genel bakış için bkz. [veritabanınızın güvenliğini sağlama](sql-database-security-overview.md)

<!--Image references-->
[1]: ./media/sql-database-get-started-portal/new-server2.png
[2]: ./media/sql-database-get-started-portal/manage-server-firewall.png
