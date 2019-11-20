---
title: Yönetilen örnek bağlama uygulaması
description: Bu makalede, uygulamanızı Azure SQL veritabanı yönetilen örneği 'ne nasıl bağlayabileceğinizi ele alınmaktadır.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab, vanto
ms.date: 11/09/2018
ms.openlocfilehash: 9f592c345b7cfcf5f21d816fde1fae6b8e6b98c7
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73823395"
---
# <a name="connect-your-application-to-azure-sql-database-managed-instance"></a>Uygulamanızı Azure SQL veritabanı yönetilen örneğine bağlama

Bugün, uygulamanızı nasıl ve nerede barındırmanıza karar verirken birden çok seçeneğiniz vardır.

Azure App Service Ortamı, sanal makine, sanal makine ölçek kümesi gibi Azure App Service veya Azure sanal ağ (VNet) tümleşik seçeneklerinden birini kullanarak, uygulamayı bulutta barındırabilirsiniz. Ayrıca karma bulut yaklaşımı alabilir ve uygulamalarınızı şirket içinde tutabilirsiniz.

Hangi seçimi yaptıysanız, onu yönetilen bir örneğe bağlayabilirsiniz.  

![yüksek kullanılabilirlik](./media/sql-database-managed-instance/application-deployment-topologies.png)

## <a name="connect-an-application-inside-the-same-vnet"></a>Aynı sanal ağ içinde bir uygulamayı bağlama

Bu senaryo en basit olanıdır. VNet içindeki sanal makineler, farklı alt ağların içinde olsalar dahi birbirlerine doğrudan bağlanabilir. Diğer bir deyişle, uygulamayı bir Azure uygulama ortamı veya sanal makine içinde bağlamanız gerektiği anlamına gelir.  

## <a name="connect-an-application-inside-a-different-vnet"></a>Farklı bir VNet içindeki bir uygulamayı bağlama

Yönetilen örnek kendi VNet 'inde özel IP adresine sahip olduğundan bu senaryo biraz daha karmaşıktır. Bağlanmak için bir uygulamanın, yönetilen örneğin dağıtıldığı VNet 'e erişmesi gerekir. Bu nedenle, önce uygulama ile yönetilen örnek VNet arasında bir bağlantı oluşturmanız gerekir. Bu senaryonun çalışması için VNET 'lerin aynı abonelikte olması gerekmez.

Sanal ağları bağlamak için iki seçenek vardır:

- [Azure sanal ağ eşlemesi](../virtual-network/virtual-network-peering-overview.md)
- VNet-VNet VPN Gateway ([Azure Portal](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md), [POWERSHELL](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md), [Azure CLI](../vpn-gateway/vpn-gateway-howto-vnet-vnet-cli.md))

Eşleme Microsoft omurga ağını kullandığından eşleme seçeneği tercih edilir, çünkü bağlantı açısından, eşlenmiş VNet 'teki sanal makineler ile aynı VNet 'te gecikme fark yoktur. VNet eşlemesi, aynı bölgedeki ağlarla sınırlıdır.  

> [!IMPORTANT]
> Yönetilen örnek için VNet eşleme senaryosu, [Genel sanal ağ eşlemesi kısıtlamalarından](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints)dolayı aynı bölgedeki ağlarla sınırlıdır. Daha fazla bilgi için bkz. [Azure sanal ağlar sık sorulan sorular](https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) makalesinin ilgili bölümü. 

## <a name="connect-an-on-premises-application"></a>Şirket içi bir uygulamayı bağlama

Yönetilen örneğe yalnızca özel bir IP adresi üzerinden erişilebilir. Şirket içinden erişmek için, uygulama ile yönetilen örnek VNet arasında siteden siteye bağlantı oluşturmanız gerekir.

Şirket içinde Azure VNet 'e nasıl bağlanıbir iki seçenek vardır:

- Siteden siteye VPN bağlantısı ([Azure Portal](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md), [POWERSHELL](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md), [Azure CLI](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md))
- [ExpressRoute](../expressroute/expressroute-introduction.md) bağlantısı  

Şirket içinden Azure bağlantısı kurmayı başarıyla yaptıysanız ve yönetilen örnekle bağlantı kuramıyoruz, güvenlik duvarınızın SQL bağlantı noktası 1433 ' de giden bağlantı bağlantısı ve yeniden yönlendirme için 11000-11999 bağlantı noktası aralığı olduğunu denetleyin.

## <a name="connect-an-application-on-the-developers-box"></a>Geliştirici kutusunda uygulama bağlama

Yönetilen örneğe yalnızca özel bir IP adresi üzerinden erişilebilir. böylece Geliştirici kutudan erişebilmek için öncelikle geliştirici kutusu ile yönetilen örnek VNet arasında bir bağlantı oluşturmanız gerekir. Bunu yapmak için yerel Azure sertifika kimlik doğrulaması kullanarak bir sanal ağa Noktadan siteye bağlantı yapılandırın. Daha fazla bilgi için bkz. [Şirket içi bilgisayardan Azure SQL veritabanı yönetilen örneğine bağlanmak için Noktadan siteye bağlantı yapılandırma](sql-database-managed-instance-configure-p2s.md).

## <a name="connect-from-on-premises-with-vnet-peering"></a>VNet eşlemesi ile Şirket içinden bağlanma

Müşteriler tarafından uygulanan başka bir senaryo da VPN ağ geçidinin ayrı bir sanal ağa ve barındırma yönetilen örneğinden bir aboneliğe yüklendiği yerdir. İki sanal ağ daha sonra eşlenmez. Aşağıdaki örnek mimari diyagramı, bunun nasıl uygulankullanılabileceğini gösterir.

![VNet eşlemesi](./media/sql-database-managed-instance-connect-app/vnet-peering.png)

Temel altyapıyı ayarladıktan sonra, VPN Gateway yönetilen örneği barındıran sanal ağdaki IP adreslerini görebilmesi için bazı ayarları değiştirmeniz gerekir. Bunu yapmak için, **eşleme ayarları**altında aşağıdaki belirli değişiklikleri yapın.

1. VPN ağ geçidini barındıran VNet 'de, eşlemeler **' e gidin**, ardından yönetilen örnek eşlenmiş VNet bağlantısı ' na gidin ve **ağ geçidi aktarımına izin ver**' e tıklayın.
2. Yönetilen örneği barındıran VNet **'de, eşlemeler ' e gidin**, sonra eşlenen vnet bağlantısına VPN Gateway ve ardından **uzak ağ geçitlerini kullan**' a tıklayın.

## <a name="connect-an-azure-app-service-hosted-application"></a>Azure App Service barındırılan bir uygulamayı bağlama

Yönetilen örneğe yalnızca bir özel IP adresi aracılığıyla erişilebilir. bu nedenle, Azure App Service adresinden erişebilmek için öncelikle uygulamayla yönetilen örnek VNet arasında bir bağlantı oluşturmanız gerekir. Bkz. [uygulamanızı Azure sanal ağıyla tümleştirme](../app-service/web-sites-integrate-with-vnet.md).  

Sorun giderme için bkz. [VNET ve uygulamalar sorunlarını giderme](../app-service/web-sites-integrate-with-vnet.md#troubleshooting). Bir bağlantı kurulamazsa [ağ yapılandırmasını eşitlemeyi](sql-database-managed-instance-sync-network-configuration.md)deneyin.

Yönetilen örneğe Azure App Service bağlanmada özel bir durum, yönetilen örnek VNet 'e eşlenmiş bir ağa Azure App Service tümleştirildiğinde oluşur. Bu durum, aşağıdaki yapılandırmanın ayarlanmasını gerektirir:

- Yönetilen örnek VNet 'in ağ geçidine sahip OLMAMASı gerekir  
- Yönetilen örnek VNet 'in uzak ağ geçitleri seçenek kümesi kullanması gerekir
- Eşlenen VNet 'in ağ geçidi aktarım seçeneği kümesine Izin ver

Bu senaryo aşağıdaki diyagramda gösterilmiştir:

![Tümleşik uygulama eşlemesi](./media/sql-database-managed-instance/integrated-app-peering.png)

>[!NOTE]
>VNet tümleştirme özelliği, bir uygulamayı ExpressRoute ağ geçidi olan bir VNet ile tümleştirmez. ExpressRoute ağ geçidi, birlikte bulunma modunda yapılandırılmış olsa bile VNet tümleştirmesi çalışmaz. Kaynaklara bir ExpressRoute bağlantısı aracılığıyla erişmeniz gerekiyorsa, VNet 'iniz üzerinde çalışan bir App Service Ortamı kullanabilirsiniz.

## <a name="troubleshooting-connectivity-issues"></a>Bağlantı sorunlarını giderme

Bağlantı sorunlarını gidermek için aşağıdakileri gözden geçirin:

- Aynı VNet içindeki ancak farklı alt ağdaki bir Azure sanal makinesinden yönetilen örneğe bağlanamıyorsanız, VM alt ağında erişimi engelliyor olabilecek bir ağ güvenlik grubu olup olmadığını kontrol edin. Ayrıca, Azure sınırının içindeki yeniden yönlendirme yoluyla bağlantı kurulması gerektiğinden, SQL bağlantı noktası 1433 ' de giden bağlantı ve bağlantı noktaları ' nın 11000-11999 aralığında açık olması gerektiğini unutmayın.
- BGP yayılması VNet ile ilişkili yol tablosu için **etkin** olarak ayarlandığından emin olun.
- P2S VPN kullanıyorsanız, giriş **/Çıkış** numaraları olup olmadığını görmek için Azure Portal yapılandırmayı kontrol edin. Sıfır olmayan sayılar, Azure 'un Şirket içi/içeri trafiği yönlendirme olduğunu gösterir.

   ![giriş/çıkış numaraları](./media/sql-database-managed-instance-connect-app/ingress-egress-numbers.png)

- İstemci makinesinin (VPN istemcisini çalıştıran), erişmeniz gereken tüm sanal ağlar için rota girişlerine sahip olup olmadığını denetleyin. Yollar `%AppData%\ Roaming\Microsoft\Network\Connections\Cm\<GUID>\routes.txt`depolanır.

   ![Route. txt](./media/sql-database-managed-instance-connect-app/route-txt.png)

   Bu görüntüde gösterildiği gibi, her VNet için iki giriş ve portalda yapılandırılan VPN uç noktası için üçüncü bir giriş vardır.

   Yolları denetlemeye yönelik başka bir yöntem de aşağıdaki komutu kullanmaktır. Çıktı, çeşitli alt ağların yollarını gösterir:

   ```cmd
   C:\ >route print -4
   ===========================================================================
   Interface List
   14...54 ee 75 67 6b 39 ......Intel(R) Ethernet Connection (3) I218-LM
   57...........................rndatavnet
   18...94 65 9c 7d e5 ce ......Intel(R) Dual Band Wireless-AC 7265
   1...........................Software Loopback Interface 1
   Adapter===========================================================================

   IPv4 Route Table
   ===========================================================================
   Active Routes:
   Network Destination        Netmask          Gateway       Interface  Metric
          0.0.0.0          0.0.0.0       10.83.72.1     10.83.74.112     35
         10.0.0.0    255.255.255.0         On-link       172.26.34.2     43
         10.4.0.0    255.255.255.0         On-link       172.26.34.2     43
   ===========================================================================
   Persistent Routes:
   None
   ```

- VNet eşlemesi kullanıyorsanız, [ağ geçidi aktarımına Izin ver ' i ayarlama ve uzak ağ geçitleri kullanma](#connect-from-on-premises-with-vnet-peering)yönergelerini izlediğinizden emin olun.

## <a name="required-versions-of-drivers-and-tools"></a>Gerekli sürücü ve araç sürümleri

Yönetilen örneğe bağlanmak istiyorsanız, araçların ve sürücülerin aşağıdaki en düşük sürümleri önerilir:

| Sürücü/araç | Sürüm |
| --- | --- |
|.NET Framework | 4.6.1 (veya .NET Core) |
|ODBC sürücüsü| v17 |
|PHP sürücüsü| 5.2.0 |
|JDBC sürücüsü| 6.4.0 |
|Node.js sürücüsü| 2.1.1 |
|OLEDB sürücüsü| 18.0.2.0 |
|SSMS| 18,0 veya [üzeri](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) |
|[SMO](https://docs.microsoft.com/sql/relational-databases/server-management-objects-smo/sql-server-management-objects-smo-programming-guide) | [150](https://www.nuget.org/packages/Microsoft.SqlServer.SqlManagementObjects) veya üzeri |

## <a name="next-steps"></a>Sonraki adımlar

- Yönetilen Örnek hakkında daha fazla bilgi için bkz. [Yönetilen Örnek nedir?](sql-database-managed-instance.md).
- Yeni bir yönetilen örnek oluşturma hakkında bir öğretici için bkz. [yönetilen örnek oluşturma](sql-database-managed-instance-get-started.md).
