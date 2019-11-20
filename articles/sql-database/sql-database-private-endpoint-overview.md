---
title: Özel Bağlantı
description: Özel uç nokta özelliğine genel bakış
author: rohitnayakmsft
ms.author: rohitna
titleSuffix: Azure SQL Database and SQL Data Warehouse
ms.service: sql-database
ms.topic: overview
ms.reviewer: vanto
ms.date: 09/17/2019
ms.openlocfilehash: fcb89cbcadb5e101ab2b4bfd18d0b7b91c63c92a
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73821301"
---
# <a name="private-link-for-azure-sql-database-and-data-warehouse-preview"></a>Azure SQL veritabanı ve veri ambarı için özel bağlantı (Önizleme)

Özel bağlantı, Azure 'daki çeşitli PaaS hizmetlerine **özel bir uç nokta**aracılığıyla bağlanmanızı sağlar. Özel bağlantı işlevselliğini destekleyen PaaS hizmetlerinin listesi için [özel bağlantı belgeleri](../private-link/index.yml) sayfasına gidin. Özel uç nokta, belirli bir [sanal](../virtual-network/virtual-networks-overview.md) ağ ve alt ağ içindeki özel bir IP adresidir. 

> [!IMPORTANT]
> Bu makale Azure SQL Server ve Azure SQL Server 'da oluşturulan SQL veritabanı ve SQL veri ambarı veritabanları için geçerlidir. Kolaylık açısından, hem SQL Veritabanı hem de SQL Veri Ambarı için SQL Veritabanı terimi kullanılmaktadır. Bu makale, Azure SQL veritabanı 'nda **yönetilen bir örnek** dağıtımı *için geçerlidir.*

## <a name="data-exfiltration-prevention"></a>Veri kaybı önleme

Azure SQL veritabanı 'nda veri ayıklama, veritabanı yöneticisi gibi yetkili bir kullanıcının verileri bir sistemden ayıklamasına ve kuruluşun dışında başka bir konuma veya sisteme taşımasına yönelik bir veritabanıdır. Örneğin, Kullanıcı, verileri üçüncü tarafa ait bir depolama hesabına taşıtır.

Bir SQL veritabanına bağlanan bir Azure VM içinde SQL Server Management Studio (SSMS) çalıştıran bir kullanıcı içeren bir senaryo düşünün. Bu SQL veritabanı Batı ABD veri merkezinde. Aşağıdaki örnekte, ağ erişim denetimleri kullanılarak SQL veritabanı 'ndaki genel uç noktalarla erişimin nasıl sınırlandıralınacağını gösterilmektedir.

1. Azure hizmetlerinin **kapalı**çalışmasına izin ver ayarını yaparak, genel uç nokta aracılığıyla SQL veritabanı 'Na tüm Azure hizmet trafiğini devre dışı bırakın. Sunucu ve veritabanı düzeyinde güvenlik duvarı kurallarında IP adresine izin verilmediğinden emin olun. Daha fazla bilgi için bkz. [Azure SQL veritabanı ve veri ambarı ağ erişim denetimleri](sql-database-networkaccess-overview.md).
1. Yalnızca VM 'nin özel IP adresini kullanarak SQL veritabanı 'na giden trafiğe izin verin. Daha fazla bilgi için [hizmet uç noktası](sql-database-vnet-service-endpoint-rule-overview.md) ve [VNET güvenlik duvarı kuralları](sql-database-firewall-configure.md)makalesine bakın.
1. Azure VM 'de, [ağ güvenlik grupları (NSG 'ler)](../virtual-network/manage-network-security-group.md) ve hizmet etiketleri kullanarak giden bağlantı kapsamını aşağıda gösterildiği gibi daraltın
    - Hizmet etiketi = SQL için trafiğe izin veren bir NSG kuralı belirtin. WestUs-yalnızca Batı ABD SQL veritabanı 'na bağlantıya izin veriliyor
    - Hizmet etiketi = SQL-tüm bölgelerde SQL veritabanı bağlantılarını reddetmek için bir NSG kuralı ( **daha yüksek önceliğe**sahip) belirtin

Bu kurulumun sonunda, Azure VM yalnızca Batı ABD bölgesindeki SQL veritabanlarına bağlanabilir. Ancak, bağlantı tek bir SQL veritabanıyla sınırlı değildir. VM, aboneliğin parçası olmayan veritabanları da dahil olmak üzere Batı ABD bölgesindeki SQL veritabanlarına yine de bağlanabilir. Yukarıdaki senaryodaki veri taşalım kapsamını belirli bir bölgeye azalttık, ancak bunu tamamen ortadan kaldırdık.

Özel bağlantıyla, müşteriler artık özel uç noktaya erişimi kısıtlamak için NSG 'ler gibi ağ erişim denetimleri ayarlayabilir. Tek tek Azure PaaS kaynakları, belirli özel uç noktalara eşlenir. Kötü amaçlı bir Insider, eşlenen PaaS kaynağına (örneğin bir SQL veritabanı) ve başka bir kaynağa erişebilir. 

## <a name="on-premises-connectivity-over-private-peering"></a>Özel eşleme üzerinden şirket içi bağlantı

Müşteriler şirket içi makinelerden ortak uç noktaya bağlandıklarında, IP adreslerinin [sunucu düzeyinde bir güvenlik duvarı kuralı](sql-database-server-level-firewall-rule.md)kullanılarak IP tabanlı güvenlik duvarına eklenmesi gerekir. Bu model geliştirme veya test iş yükleri için bireysel makinelere erişim sağlamak için iyi bir şekilde çalıştığından, bir üretim ortamında yönetilmesi zordur.

Özel bağlantıyla, müşteriler [ExpressRoute](../expressroute/expressroute-introduction.md), özel eşleme veya VPN tüneli kullanarak özel uç noktaya şirket içi erişimi etkinleştirebilir. Müşteriler daha sonra genel uç nokta aracılığıyla tüm erişimi devre dışı bırakabilir ve IP adreslerine izin vermek için IP tabanlı güvenlik duvarını kullanmaz.

Özel bağlantıyla, müşteriler Express Route (ER) özel eşleme veya VPN tüneli kullanarak özel uç noktaya şirket içi erişimi etkinleştirebilir. Daha sonra, genel uç nokta aracılığıyla tüm erişimi devre dışı bırakabilir ve IP tabanlı güvenlik duvarını kullanmaz.

## <a name="how-to-set-up-private-link-for-azure-sql-database"></a>Azure SQL veritabanı için özel bağlantı ayarlama 

### <a name="creation-process"></a>Oluşturma Işlemi
Özel uç noktalar Portal, PowerShell veya Azure CLı kullanılarak oluşturulabilir:
- [Portal](../private-link/create-private-endpoint-portal.md)
- [PowerShell](../private-link/create-private-endpoint-powershell.md)
- [CLI](../private-link/create-private-endpoint-cli.md)

### <a name="approval-process"></a>Onay Işlemi
Ağ Yöneticisi özel uç noktayı (PE) oluşturduktan sonra, SQL Yöneticisi özel uç nokta bağlantısını (PEC) SQL veritabanı 'na yönetebilir.

1. Azure portal SQL Server kaynağına gidin.

    - (1) sol bölmedeki özel uç nokta bağlantılarını seçin
    - (2) tüm özel uç nokta bağlantılarının (lar) bir listesini gösterir
    - (3) karşılık gelen özel uç nokta (PE), tüm ![][3] ekran görüntüsü oluşturdu

1. Listeden tek bir PEC seçin.
![ekran görüntüsü seçili PEC][6]

1. SQL Yöneticisi bir PEC 'i onaylamayı veya reddetmeyi seçebilir ve isteğe bağlı olarak kısa bir metin yanıtı ekleyebilir.
PEC onay][4] ekran görüntüsünü ![

1. Onay veya reddetme sonrasında, liste, yanıt metniyle birlikte uygun durumu yansıtır.
onay sonrasında tüm ö ![ekran görüntüsünü][5]

## <a name="use-cases-of-private-link-for-azure-sql-database"></a>Azure SQL veritabanı için özel bağlantı durumlarını kullanma 

İstemciler aynı VNet 'ten özel uç noktaya, aynı bölgedeki eşlenmiş VNet 'e veya bölgeler arasında VNet-VNet bağlantısı aracılığıyla bağlanabilir. Ayrıca, istemciler ExpressRoute, özel eşleme veya VPN tüneli kullanarak şirket içinden bağlanabilir. Aşağıda, yaygın kullanım durumlarını gösteren basitleştirilmiş bir diyagram bulunur.

 ![Bağlantı seçenekleri diyagramı][1]

## <a name="test-connectivity-to-sql-database-from-an-azure-vm-in-same-virtual-network-vnet"></a>Aynı sanal ağdaki (VNet) bir Azure VM 'den SQL veritabanı ile bağlantı sınama

Bu senaryo için, Windows Server 2016 çalıştıran bir Azure sanal makinesi (VM) oluşturduğunuzu varsayın. 

1. [Bir uzak masaüstü (RDP) oturumu başlatın ve sanal makineye bağlanın](../virtual-machines/windows/connect-logon.md#connect-to-the-virtual-machine). 
1. Daha sonra, aşağıdaki araçları kullanarak VM 'nin özel uç nokta aracılığıyla SQL veritabanı 'na bağlanmasını sağlamak için bazı temel bağlantı denetimlerini yapabilirsiniz:
    1. Sun
    1. Psping
    1. Nmap
    1. SQL Server Management Studio (SSMS)

### <a name="check-connectivity-using-telnet"></a>Telnet kullanarak bağlantıyı denetleme

[Telnet Client](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754293%28v%3dws.10%29) , bağlantıyı test etmek için kullanılabilen bir Windows özelliğidir. Windows işletim sisteminin sürümüne bağlı olarak, bu özelliği açıkça etkinleştirmeniz gerekebilir. 

Telnet 'i yükledikten sonra bir komut Istemi penceresi açın. Telnet komutunu çalıştırın ve SQL veritabanının IP adresini ve özel uç noktasını belirtin.

```
>telnet 10.1.1.5 1433
```

Telnet başarıyla bağlanıp, aşağıdaki görüntüde olduğu gibi komut penceresinde boş bir ekran görürsünüz:

 ![Telnet diyagramı][2]

### <a name="check-connectivity-using-psping"></a>Psping kullanarak bağlantıyı denetle

Özel uç nokta bağlantısının (PEC) bağlantı noktası 1433 ' deki bağlantıları dinlediğini denetlemek için aşağıdaki gibi bir [Psping](/sysinternals/downloads/psping) kullanılabilir.

SQL veritabanı sunucunuz ve bağlantı noktası 1433 için FQDN 'yi sağlayarak aşağıdaki gibi psping 'yi çalıştırın:

```
>psping.exe mysqldbsrvr.database.windows.net:1433

PsPing v2.10 - PsPing - ping, latency, bandwidth measurement utility
Copyright (C) 2012-2016 Mark Russinovich
Sysinternals - www.sysinternals.com

TCP connect to 10.6.1.4:1433:
5 iterations (warmup 1) ping test:
Connecting to 10.6.1.4:1433 (warmup): from 10.6.0.4:49953: 2.83ms
Connecting to 10.6.1.4:1433: from 10.6.0.4:49954: 1.26ms
Connecting to 10.6.1.4:1433: from 10.6.0.4:49955: 1.98ms
Connecting to 10.6.1.4:1433: from 10.6.0.4:49956: 1.43ms
Connecting to 10.6.1.4:1433: from 10.6.0.4:49958: 2.28ms
```

Çıktı, PEC ile ilişkili özel IP adresine ping atabildiğini gösterir.

### <a name="check-connectivity-using-nmap"></a>Nmap kullanarak bağlantıyı denetle

Nmap (ağ Eşleyici), ağ bulma ve güvenlik denetimi için kullanılan ücretsiz ve açık kaynaklı bir araçtır. Daha fazla bilgi ve indirme bağlantısı için https://nmap.orgziyaret edin. Özel uç noktanın 1433 numaralı bağlantı noktasında bağlantıları dinlediğinden emin olmak için bu aracı kullanabilirsiniz.

Özel uç noktayı barındıran alt ağın adres aralığını sağlayarak Nmap 'i aşağıdaki şekilde çalıştırın.

```
>nmap -n -sP 10.1.1.0/24
...
...
Nmap scan report for 10.1.1.5
Host is up (0.00s latency).
Nmap done: 256 IP addresses (1 host up) scanned in 207.00 seconds
```

Sonuç bir IP adresinin yukarı olduğunu gösterir; Özel uç nokta için IP adresine karşılık gelen.


### <a name="check-connectivity-using-sql-server-management-studio-ssms"></a>SQL Server Management Studio (SSMS) kullanarak bağlantıyı denetleme

Son adım [SQL veritabanına bağlanmak Için SSMS](sql-database-connect-query-ssms.md)'yi kullanmaktır. SSMS kullanarak SQL veritabanına bağlandıktan sonra, aşağıdaki sorguyu çalıştırarak Azure VM 'nin özel IP adresinden bağlandığınızı doğrulayın:

````
select client_net_address from sys.dm_exec_connections 
where session_id=@@SPID
````
> [!NOTE]
> Önizlemede, Özel uç nokta bağlantıları yalnızca [bağlantı ilkesi](sql-database-connectivity-architecture.md#connection-policy) olarak **proxy** 'yi destekler


## <a name="connecting-from-an-azure-vm-in-peered-virtual-network-vnet"></a>Eşlenen sanal ağdaki (VNet) bir Azure VM 'den bağlanma 

Eşlenen VNet 'teki bir Azure VM 'den SQL veritabanı bağlantısı kurmak için [VNET eşlemesini](../virtual-network/tutorial-connect-virtual-networks-powershell.md) yapılandırın.

## <a name="connecting-from-an-azure-vm-in-vnet-to-vnet-environment"></a>VNet-VNet ortamında bir Azure VM 'sinden bağlantı kurma

Farklı bir bölgedeki veya abonelikteki bir Azure VM 'den bir SQL veritabanı bağlantısı kurmak için [VNET-VNET VPN Gateway bağlantısını](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) yapılandırın.

## <a name="connecting-from-an-on-premises-environment-over-vpn"></a>VPN üzerinden şirket içi bir ortamdan bağlanma

Şirket içi bir ortamdan SQL veritabanına bağlantı kurmak için seçeneklerden birini seçin ve uygulayın:
- [Noktadan siteye bağlantı](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)
- [Siteden siteye VPN bağlantısı](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
- [ExpressRoute devresi](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)


## <a name="connecting-from-an-azure-sql-data-warehouse-to-azure-storage-using-polybase"></a>PolyBase kullanarak Azure SQL veri ambarından Azure depolama 'ya bağlanma

PolyBase, Azure depolama hesaplarından Azure SQL veri ambarı 'na veri yüklemek için yaygın olarak kullanılır. Verileri yüklediğiniz Azure depolama hesabı, erişimi yalnızca özel uç noktalar, hizmet uç noktaları veya IP tabanlı güvenlik duvarları aracılığıyla yalnızca bir VNet-alt ağ kümesine sınırlayıp, PolyBase 'den hesaba olan bağlantı kesilir. VNet ile güvenli hale getirilmiş Azure depolama 'ya bağlanan Azure SQL veri ambarı ile hem PolyBase içeri ve dışarı aktarma senaryolarını etkinleştirmek için [burada](sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage)verilen adımları izleyin. 



## <a name="next-steps"></a>Sonraki adımlar

- Azure SQL veritabanı güvenliğine genel bakış için bkz. [veritabanınızın güvenliğini sağlama](sql-database-security-overview.md)
- Azure SQL veritabanı bağlantısına genel bakış için bkz. [Azure SQL bağlantı mimarisi](sql-database-connectivity-architecture.md)

<!--Image references-->
[1]: ./media/sql-database-get-started-portal/pe-connect-overview.png
[2]: ./media/sql-database-get-started-portal/telnet-result.png
[3]: ./media/sql-database-get-started-portal/pec-list-before.png
[4]: ./media/sql-database-get-started-portal/pec-approve.png
[5]: ./media/sql-database-get-started-portal/pec-list-after.png
[6]: ./media/sql-database-get-started-portal/pec-select.png
