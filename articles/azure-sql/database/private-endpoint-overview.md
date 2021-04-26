---
title: Azure Özel Bağlantı
description: Özel uç nokta özelliğine genel bakış.
author: rohitnayakmsft
ms.author: rohitna
titleSuffix: Azure SQL Database and Azure Synapse Analytics
ms.service: sql-database
ms.topic: overview
ms.custom: sqldbrb=1, fasttrack-edit
ms.reviewer: vanto
ms.date: 03/09/2020
ms.openlocfilehash: 0ce8e45f301ac0fd84f7d5d27d81b63103768f89
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105567721"
---
# <a name="azure-private-link-for-azure-sql-database-and-azure-synapse-analytics"></a>Azure SQL veritabanı ve Azure SYNAPSE Analytics için Azure özel bağlantısı
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

Özel bağlantı, Azure 'daki çeşitli PaaS hizmetlerine **özel bir uç nokta** aracılığıyla bağlanmanızı sağlar. Özel bağlantı işlevselliğini destekleyen PaaS hizmetlerinin bir listesi için [özel bağlantı belgeleri](../../private-link/index.yml) sayfasına gidin. Özel uç nokta, belirli bir [sanal](../../virtual-network/virtual-networks-overview.md) ağ ve alt ağ içindeki özel bir IP adresidir.

> [!IMPORTANT]
> Bu makale hem Azure SQL veritabanı hem de Azure SYNAPSE Analytics için geçerlidir. Basitlik için, ' Database ' terimi, Azure SQL veritabanı ve Azure SYNAPSE Analytics 'te her iki veritabanına başvurur. Benzer şekilde, ' Server ' öğesine yapılan tüm başvurular, Azure SQL veritabanı ve Azure SYNAPSE Analytics 'i barındıran [MANTıKSAL SQL Server](logical-servers.md) 'a başvurmaktadır. Bu *Makale,* **Azure SQL yönetilen örneği** için geçerlidir.

## <a name="how-to-set-up-private-link-for-azure-sql-database"></a>Azure SQL veritabanı için özel bağlantı ayarlama 

### <a name="creation-process"></a>Oluşturma Işlemi
Özel uç noktalar Azure portal, PowerShell veya Azure CLı kullanılarak oluşturulabilir:
- [Portal](../../private-link/create-private-endpoint-portal.md)
- [PowerShell](../../private-link/create-private-endpoint-powershell.md)
- [CLI](../../private-link/create-private-endpoint-cli.md)

### <a name="approval-process"></a>Onay işlemi
Ağ Yöneticisi özel uç noktayı (PE) oluşturduktan sonra, SQL Yöneticisi özel uç nokta bağlantısını (PEC) SQL veritabanı 'na yönetebilir.

1. Aşağıdaki ekran görüntüsünde gösterilen adımlara göre Azure portal sunucu kaynağına gidin

    - (1) sol bölmedeki özel uç nokta bağlantılarını seçin
    - (2) tüm özel uç nokta bağlantılarının (lar) bir listesini gösterir
    - (3) karşılık gelen özel uç nokta (PE) oluşturulan ![ tüm nesnelerin ekran görüntüsü][3]

1. Listeden tek bir PEC seçin.
![Ekran görüntüsü seçili PEC][6]

1. SQL Yöneticisi bir PEC 'i onaylamayı veya reddetmeyi seçebilir ve isteğe bağlı olarak kısa bir metin yanıtı ekleyebilir.
![PEC onayının ekran görüntüsü][4]

1. Onay veya reddetme sonrasında, liste, yanıt metniyle birlikte uygun durumu yansıtır.
![Onay sonrasında tüm her türlü bir ekran görüntüsü][5]

## <a name="on-premises-connectivity-over-private-peering"></a>Özel eşleme üzerinde şirket içi bağlantısı

Müşteriler şirket içi makinelerden ortak uç noktaya bağlandıklarında, IP adreslerinin [sunucu düzeyinde bir güvenlik duvarı kuralı](firewall-create-server-level-portal-quickstart.md)kullanılarak IP tabanlı güvenlik duvarına eklenmesi gerekir. Bu model geliştirme veya test iş yükleri için bireysel makinelere erişim sağlamak için iyi bir şekilde çalıştığından, bir üretim ortamında yönetilmesi zordur.

Özel bağlantıyla, müşteriler [ExpressRoute](../../expressroute/expressroute-introduction.md), özel eşleme veya VPN tüneli kullanarak özel uç noktaya şirket içi erişimi etkinleştirebilir. Müşteriler daha sonra genel uç nokta aracılığıyla tüm erişimi devre dışı bırakabilir ve IP adreslerine izin vermek için IP tabanlı güvenlik duvarını kullanmaz.

## <a name="use-cases-of-private-link-for-azure-sql-database"></a>Azure SQL veritabanı için özel bağlantı durumlarını kullanma 

İstemciler aynı sanal ağdaki özel uç noktaya, aynı bölgedeki eşlenmiş sanal ağa veya bölgeler arasında sanal ağdan sanal ağ bağlantısı aracılığıyla bağlanabilir. Ayrıca, istemciler ExpressRoute, özel eşleme veya VPN tüneli kullanarak şirket içinden bağlanabilir. Aşağıda, yaygın kullanım durumlarını gösteren basitleştirilmiş bir diyagram bulunur.

 ![Bağlantı seçenekleri diyagramı][1]

Ayrıca, sanal ağda doğrudan çalıştırmayan ancak onunla tümleştirilmiş olan hizmetler (örneğin, App Service Web Apps veya Işlevler) veritabanına özel bağlantı elde edebilir. Bu özel kullanım durumu hakkında daha fazla bilgi için bkz. [Azure SQL veritabanı mimarisine özel bağlantı Ile Web uygulaması](/azure/architecture/example-scenario/private-web-app/private-web-app) .

## <a name="test-connectivity-to-sql-database-from-an-azure-vm-in-same-virtual-network"></a>Aynı sanal ağdaki bir Azure VM 'den SQL veritabanı 'na bağlantıyı test etme

Bu senaryo için, Windows Server 2016 çalıştıran bir Azure sanal makinesi (VM) oluşturduğunuzu varsayın. 

1. [Bir uzak masaüstü (RDP) oturumu başlatın ve sanal makineye bağlanın](../../virtual-machines/windows/connect-logon.md#connect-to-the-virtual-machine). 
1. Daha sonra, aşağıdaki araçları kullanarak VM 'nin özel uç nokta aracılığıyla SQL veritabanı 'na bağlanmasını sağlamak için bazı temel bağlantı denetimlerini yapabilirsiniz:
    1. Sun
    1. Psping
    1. Nmap
    1. SQL Server Management Studio (SSMS)

### <a name="check-connectivity-using-telnet"></a>Telnet kullanarak bağlantıyı denetleme

[Telnet Client](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754293%28v%3dws.10%29) , bağlantıyı test etmek için kullanılabilen bir Windows özelliğidir. Windows işletim sisteminin sürümüne bağlı olarak, bu özelliği açıkça etkinleştirmeniz gerekebilir. 

Telnet 'i yükledikten sonra bir komut Istemi penceresi açın. Telnet komutunu çalıştırın ve SQL veritabanı 'nda veritabanının IP adresini ve özel uç noktasını belirtin.

```
>telnet 10.1.1.5 1433
```

Telnet başarıyla bağlanıp, aşağıdaki görüntüde olduğu gibi komut penceresinde boş bir ekran görürsünüz:

 ![Telnet diyagramı][2]

### <a name="check-connectivity-using-psping"></a>Psping kullanarak bağlantıyı denetle

Özel uç nokta bağlantısının (PEC) bağlantı noktası 1433 ' deki bağlantıları dinlediğini denetlemek için aşağıdaki gibi bir [Psping](/sysinternals/downloads/psping) kullanılabilir.

Mantıksal SQL Server ve 1433 numaralı bağlantı noktası için FQDN sağlayarak aşağıdaki gibi psping 'yi çalıştırın:

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

Nmap (ağ Eşleyici), ağ bulma ve güvenlik denetimi için kullanılan ücretsiz ve açık kaynaklı bir araçtır. Daha fazla bilgi ve indirme bağlantısı için, adresini ziyaret edin https://nmap.org . Özel uç noktanın 1433 numaralı bağlantı noktasında bağlantıları dinlediğinden emin olmak için bu aracı kullanabilirsiniz.

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
> [!NOTE]
> İstemcileriniz için bağlantı dizelerinde sunucunun **tam etki alanı adını (FQDN)** kullanın ( `<server>.database.windows.net` ). Doğrudan IP adresine yapılan veya özel bağlantı FQDN 'SI () kullanılarak yapılan oturum açma girişimleri `<server>.privatelink.database.windows.net` başarısız olur. Özel uç nokta, bölgedeki SQL ağ geçidine giden trafiği yönlendirdiğinden ve oturum açma işleminin başarılı olması için doğru FQDN 'nin belirtilmesi gerektiğinden, bu davranış tasarıma göre yapılır.

[SQL veritabanına bağlanmak Için SSMS](connect-query-ssms.md)'yi kullanmak için buradaki adımları izleyin. SSMS kullanarak SQL veritabanına bağlandıktan sonra, aşağıdaki sorguyu çalıştırarak Azure VM 'nin özel IP adresinden bağlandığınızı doğrulayın:

````
select client_net_address from sys.dm_exec_connections 
where session_id=@@SPID
````

## <a name="data-exfiltration-prevention"></a>Veri sızdırmayı önleme

Azure SQL veritabanı 'nda veri ayıklama, veritabanı yöneticisi gibi yetkili bir kullanıcının verileri bir sistemden ayıklamasına ve kuruluşun dışında başka bir konuma veya sisteme taşımasına yönelik bir veritabanıdır. Örneğin, Kullanıcı, verileri üçüncü tarafa ait bir depolama hesabına taşıtır.

SQL veritabanı 'nda bir veritabanına bağlanan bir Azure sanal makinesi içinde SQL Server Management Studio (SSMS) çalıştıran bir kullanıcıya sahip bir senaryo düşünün. Bu veritabanı Batı ABD veri merkezinde. Aşağıdaki örnekte, ağ erişim denetimleri kullanılarak SQL veritabanı 'ndaki genel uç noktalarla erişimin nasıl sınırlandıralınacağını gösterilmektedir.

1. Azure hizmetlerinin **kapalı** çalışmasına izin ver ayarını yaparak, genel uç nokta aracılığıyla SQL veritabanı 'Na tüm Azure hizmet trafiğini devre dışı bırakın. Sunucu ve veritabanı düzeyinde güvenlik duvarı kurallarında IP adresine izin verilmediğinden emin olun. Daha fazla bilgi için bkz. [Azure SQL veritabanı ve Azure SYNAPSE Analytics ağ erişim denetimleri](network-access-controls-overview.md).
1. Yalnızca VM 'nin özel IP adresini kullanarak SQL veritabanı 'ndaki veritabanına giden trafiğe izin verin. Daha fazla bilgi için [hizmet uç noktası](vnet-service-endpoint-rule-overview.md) ve [sanal ağ güvenlik duvarı kuralları](firewall-configure.md)makalesine bakın.
1. Azure VM 'de, [ağ güvenlik grupları (NSG 'ler)](../../virtual-network/manage-network-security-group.md) ve hizmet etiketleri kullanarak giden bağlantı kapsamını aşağıda gösterildiği gibi daraltın
    - Hizmet etiketi = SQL için trafiğe izin veren bir NSG kuralı belirtin. WestUs-yalnızca Batı ABD SQL veritabanı 'na bağlantıya izin veriliyor
    - Hizmet etiketi = SQL-tüm bölgelerde SQL veritabanı bağlantılarını reddetmek için bir NSG kuralı ( **daha yüksek önceliğe** sahip) belirtin

Bu kurulumun sonunda, Azure VM yalnızca Batı ABD bölgesindeki SQL veritabanındaki bir veritabanına bağlanabilir. Ancak, bağlantı SQL veritabanı 'ndaki tek bir veritabanıyla sınırlı değildir. VM, aboneliğin parçası olmayan veritabanları da dahil olmak üzere Batı ABD bölgesindeki herhangi bir veritabanına hala bağlanabilir. Yukarıdaki senaryodaki veri taşalım kapsamını belirli bir bölgeye azalttık, ancak bunu tamamen ortadan kaldırdık.

Özel bağlantıyla, müşteriler artık özel uç noktaya erişimi kısıtlamak için NSG 'ler gibi ağ erişim denetimleri ayarlayabilir. Tek tek Azure PaaS kaynakları, belirli özel uç noktalara eşlenir. Kötü amaçlı bir Insider, eşlenen PaaS kaynağına (örneğin, SQL veritabanı 'ndaki bir veritabanı) ve başka bir kaynağa erişebilir. 

## <a name="limitations"></a>Sınırlamalar 
Özel uç nokta bağlantıları yalnızca [bağlantı ilkesi](connectivity-architecture.md#connection-policy) olarak **proxy** 'yi destekler


## <a name="connecting-from-an-azure-vm-in-peered-virtual-network"></a>Eşlenen sanal ağdaki bir Azure VM 'den bağlanma 

Eşlenmiş bir sanal ağdaki bir Azure VM 'den SQL veritabanı bağlantısı kurmak için [sanal ağ eşlemeyi](../../virtual-network/tutorial-connect-virtual-networks-powershell.md) yapılandırın.

## <a name="connecting-from-an-azure-vm-in-virtual-network-to-virtual-network-environment"></a>Sanal ağdaki bir Azure VM 'den sanal ağ ortamına bağlanma

Farklı bir bölgedeki veya abonelikteki bir Azure VM 'den SQL veritabanındaki bir veritabanına bağlantı kurmak için [sanal ağı sanal ağ VPN Gateway bağlantısına](../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) yapılandırın.

## <a name="connecting-from-an-on-premises-environment-over-vpn"></a>VPN üzerinden şirket içi bir ortamdan bağlanma

Şirket içi bir ortamdan SQL veritabanı 'ndaki veritabanına bağlantı kurmak için seçeneklerden birini seçin ve uygulayın:
- [Noktadan siteye bağlantı](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)
- [Konumlar arası VPN bağlantısı](../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
- [ExpressRoute devresi](../../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)


## <a name="connecting-from-azure-synapse-analytics-to-azure-storage-using-polybase-and-the-copy-statement"></a>PolyBase ve COPY deyimlerini kullanarak Azure SYNAPSE Analytics 'ten Azure depolama 'ya bağlanma

PolyBase ve COPY deyimleri, Azure depolama hesaplarından Azure SYNAPSE Analytics 'e veri yüklemek için yaygın olarak kullanılır. Verileri yüklediğiniz Azure depolama hesabı, erişimi yalnızca özel uç noktalar, hizmet uç noktaları veya IP tabanlı güvenlik duvarları aracılığıyla yalnızca bir sanal ağ alt ağları kümesine sınırlayıp, PolyBase bağlantısı ve COPY deyiminin hesaba göre kesintiye uğramasını sağlar. Azure SYNAPSE Analytics ile hem içeri ve dışarı aktarma senaryolarını hem de bir sanal ağla güvenli hale getirilmiş Azure depolama 'ya bağlanarak etkinleştirmek için [burada](vnet-service-endpoint-rule-overview.md#impact-of-using-virtual-network-service-endpoints-with-azure-storage)verilen adımları izleyin. 

## <a name="next-steps"></a>Sonraki adımlar

- Azure SQL veritabanı güvenliğine genel bakış için bkz. [veritabanınızın güvenliğini sağlama](security-overview.md)
- Azure SQL veritabanı bağlantısına genel bakış için bkz. [Azure SQL bağlantı mimarisi](connectivity-architecture.md)
- Web uygulamasını, sanal ağın dışında bir Web uygulamasını bir veritabanının özel uç noktasına bağlayan [Azure SQL veritabanı mimarisi senaryosuna özel bağlantı ile](/azure/architecture/example-scenario/private-web-app/private-web-app) de ilgileniyor olabilirsiniz.

<!--Image references-->
[1]: media/quickstart-create-single-database/pe-connect-overview.png
[2]: media/quickstart-create-single-database/telnet-result.png
[3]: media/quickstart-create-single-database/pec-list-before.png
[4]: media/quickstart-create-single-database/pec-approve.png
[5]: media/quickstart-create-single-database/pec-list-after.png
[6]: media/quickstart-create-single-database/pec-select.png