---
title: Uygulamanızı SQL yönetilen örneğine bağlama
titleSuffix: Azure SQL Managed Instance
description: Bu makalede, uygulamanızı Azure SQL yönetilen örneği 'ne nasıl bağlayabileceğinizi anlatmaktadır.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, vanto
ms.date: 02/25/2021
ms.openlocfilehash: ca61f2163f1a66e87317274bab789db7944a4536
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106065217"
---
# <a name="connect-your-application-to-azure-sql-managed-instance"></a>Uygulamanızı Azure SQL Yönetilen Örneği'ne bağlama
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Bugün, uygulamanızı nasıl ve nerede barındırmanıza karar verirken birden çok seçeneğiniz vardır.

Azure App Service veya Azure 'un Azure App Service Ortamı, Azure sanal makineleri ve sanal makine ölçek kümeleri gibi sanal ağ tümleşik seçeneklerinden birini kullanarak bulutta uygulamayı barındırın. Ayrıca karma bulut yaklaşımı alabilir ve uygulamalarınızı şirket içinde tutabilirsiniz.

Yaptığınız seçim ne olursa olsun, Azure SQL yönetilen örneği 'ne bağlayabilirsiniz. 

![Yüksek kullanılabilirlik](./media/connect-application-instance/application-deployment-topologies.png)

Bu makalede bir uygulamayı birçok farklı uygulama senaryosunda Azure SQL Yönetilen Örneği'ne bağlama yöntemleri açıklanmaktadır. 

## <a name="connect-inside-the-same-vnet"></a>Aynı VNet içinde Bağlan

Bir uygulamayı SQL yönetilen örneği ile aynı sanal ağ içinde bağlamak en basit senaryodur. Sanal ağ içindeki sanal makineler, farklı alt ağlarda olsalar bile birbirlerine doğrudan bağlanabilir. Diğer bir deyişle, App Service Ortamı veya bir sanal makine içinde bir uygulamayı bağlamanız gereken bağlantı dizesini uygun şekilde ayarlayabilmeniz anlamına gelir.  

## <a name="connect-inside-a-different-vnet"></a>Farklı bir VNet içinde Bağlan

SQL yönetilen örneği kendi sanal ağında özel IP adreslerine sahip olduğundan, bir uygulamayı SQL yönetilen örneğinden farklı bir sanal ağ içinde bulunduğunda bağlamak biraz daha karmaşıktır. Bağlanmak için, bir uygulamanın SQL yönetilen örneğinin dağıtıldığı sanal ağa erişmesi gerekir. Bu nedenle, uygulama ile SQL yönetilen örnek sanal ağı arasında bir bağlantı oluşturmanız gerekir. Bu senaryonun çalışması için sanal ağların aynı abonelikte olması gerekmez.

Sanal ağları bağlamak için iki seçenek vardır:

- [Azure VNet eşlemesi](../../virtual-network/virtual-network-peering-overview.md)
- VNet-VNet VPN Gateway ([Azure Portal](../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md), [POWERSHELL](../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md), [Azure CLI](../../vpn-gateway/vpn-gateway-howto-vnet-vnet-cli.md))

Eşleme, Microsoft omurga ağını kullandığından tercih edilir, bu nedenle bağlantı perspektifinden, eşlenmiş bir sanal ağdaki ve aynı sanal ağdaki sanal makineler arasındaki gecikmede fark yoktur. Sanal ağ eşlemesi, aynı bölgedeki ağlar arasında desteklenmelidir. Genel sanal ağ eşleme, aşağıdaki notta açıklanan sınırlandıranlarla de desteklenir.  

> [!IMPORTANT]
> [9/22/2020 tarihinde yeni oluşturulan sanal kümeler için genel sanal ağ eşlemesi duyuruldu](https://azure.microsoft.com/en-us/updates/global-virtual-network-peering-support-for-azure-sql-managed-instance-now-available/). Diğer bir deyişle, duyuru tarihinden sonra boş alt ağlarda oluşturulan SQL yönetilen örnekleri ve bu alt ağlarda oluşturulan tüm sonraki yönetilen örnekler için genel sanal ağ eşlemesi desteklenir. Diğer tüm SQL yönetilen örnekler için eşleme desteği, [Genel sanal ağ eşlemesi kısıtlamalarından](../../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints)dolayı aynı bölgedeki ağlarla sınırlıdır. Daha fazla bilgi için bkz. [Azure sanal ağlar sık sorulan sorular](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) makalesinin ilgili bölümü. 

## <a name="connect-from-on-premises"></a>Şirket içinden Bağlan 

Şirket içi uygulamanızı SQL yönetilen örneğine de bağlayabilirsiniz. SQL yönetilen örneğine yalnızca özel bir IP adresi üzerinden erişilebilir. Şirket içinden erişmek için, uygulama ile SQL yönetilen örnek sanal ağı arasında siteden siteye bağlantı oluşturmanız gerekir.

Şirket içinde bir Azure sanal ağına nasıl bağlanay, iki seçenek vardır:

- Siteden siteye VPN bağlantısı ([Azure Portal](../../vpn-gateway/tutorial-site-to-site-portal.md), [POWERSHELL](../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md), [Azure CLI](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md))
- [Azure ExpressRoute](../../expressroute/expressroute-introduction.md) bağlantısı  

Şirket içinde Azure bağlantısı kurmayı başarıyla yaptıysanız ve SQL yönetilen örneği ile bağlantı kuramıyoruz, güvenlik duvarınızın SQL bağlantı noktası 1433 ' de açık bir giden bağlantı olup olmadığını ve yeniden yönlendirme için 11000-11999 bağlantı noktası aralığını kontrol edin.

## <a name="connect-the-developer-box"></a>Geliştirici kutusunu bağlama

Geliştirici kutusunu SQL yönetilen örneğine bağlamak de mümkündür. SQL yönetilen örneğine yalnızca özel bir IP adresi üzerinden erişilebilir, bu nedenle geliştirici kutudan erişebilmek için öncelikle geliştirici kutusu ve SQL yönetilen örnek sanal ağı arasında bir bağlantı oluşturmanız gerekir. Bunu yapmak için yerel Azure sertifika kimlik doğrulamasını kullanarak bir sanal ağa Noktadan siteye bağlantı yapılandırın. Daha fazla bilgi için bkz. Şirket  [içi bir bilgisayardan Azure SQL yönetilen örneğine bağlanmak için Noktadan siteye bağlantı yapılandırma](point-to-site-p2s-configure.md).

## <a name="connect-with-vnet-peering"></a>VNet eşlemesi ile bağlanma

Müşteriler tarafından uygulanan başka bir senaryo, bir VPN ağ geçidinin ayrı bir sanal ağa ve bir SQL yönetilen örneği barındıran aboneliğe yüklendiği yerdir. İki sanal ağ daha sonra eşlenmez. Aşağıdaki örnek mimari diyagramı, bunun nasıl uygulankullanılabileceğini gösterir.

![Sanal ağ eşleme](./media/connect-application-instance/vnet-peering.png)

Temel altyapıyı ayarladıktan sonra, VPN ağ geçidinin SQL yönetilen örneği barındıran sanal ağdaki IP adreslerini görebilmesi için bazı ayarları değiştirmeniz gerekir. Bunu yapmak için, **eşleme ayarları** altında aşağıdaki belirli değişiklikleri yapın.

1. VPN ağ geçidini barındıran sanal ağda, eşlemeler **' e gidin**, SQL yönetilen örneği için eşlenen sanal ağ bağlantısına gidin ve **ağ geçidi aktarımına izin ver**' e tıklayın.
2. SQL yönetilen örneğini barındıran sanal **ağda, eşlemeler ' e gidin**, VPN ağ geçidinin eşlenen sanal ağ bağlantısına gidin ve **uzak ağ geçitlerini kullan**' a tıklayın.

## <a name="connect-azure-app-service"></a>Azure App Service Bağlan 

Ayrıca, Azure App Service tarafından barındırılan bir uygulamayı da bağlayabilirsiniz. SQL yönetilen örneği 'ne yalnızca özel bir IP adresi aracılığıyla erişilebilir, bu nedenle Azure App Service 'ten erişebilmek için öncelikle uygulamayla SQL yönetilen örnek sanal ağı arasında bir bağlantı oluşturmanız gerekir. Bkz. [uygulamanızı Azure sanal ağıyla tümleştirme](../../app-service/web-sites-integrate-with-vnet.md).  

Sorun giderme için bkz. [sanal ağlarda ve uygulamalarda sorun giderme](../../app-service/web-sites-integrate-with-vnet.md#troubleshooting). Bir bağlantı kurulamazsa [ağ yapılandırmasını eşitlemeyi](azure-app-sync-network-configuration.md)deneyin.

Azure App Service SQL yönetilen örneğine bağlama işlemi, Azure App Service bir SQL yönetilen örnek sanal ağı ile eşlenen bir ağla tümleştirmeniz durumunda oluşur. Bu durum, aşağıdaki yapılandırmanın ayarlanmasını gerektirir:

- SQL yönetilen örneği sanal ağı bir ağ geçidine sahip olmamalıdır  
- SQL yönetilen örneği sanal ağı, `Use remote gateways` seçenek kümesine sahip olmalıdır
- Eşlenen sanal ağın `Allow gateway transit` seçenek kümesi olmalıdır

Bu senaryo aşağıdaki diyagramda gösterilmiştir:

![Tümleşik uygulama eşlemesi](./media/connect-application-instance/integrated-app-peering.png)

>[!NOTE]
>Sanal ağ tümleştirme özelliği, ExpressRoute ağ geçidine sahip bir sanal ağla bir uygulamayı tümleştirmez. ExpressRoute ağ geçidi, birlikte bulunma modunda yapılandırılmış olsa bile, sanal ağ tümleştirmesi çalışmaz. Kaynaklara bir ExpressRoute bağlantısı aracılığıyla erişmeniz gerekiyorsa, sanal ağınızda çalışan App Service Ortamı kullanabilirsiniz.

## <a name="troubleshooting-connectivity-issues"></a>Bağlantı sorunlarını giderme

Bağlantı sorunlarını gidermek için aşağıdakileri gözden geçirin:

- Aynı sanal ağ içindeki bir Azure sanal makinesinden, farklı bir alt ağda SQL yönetilen örneği 'ne bağlanamıyorsanız, VM alt ağında erişimi engelliyor olabilecek bir ağ güvenlik grubu olup olmadığını kontrol edin. Ayrıca, Azure sınırının içindeki yeniden yönlendirme yoluyla bağlantı kurulması gerektiğinden, SQL bağlantı noktası 1433 ' de giden bağlantı ' yı ve 11000-11999 aralığındaki bağlantı noktalarını açın.
- BGP yayılması 'nın, sanal ağla ilişkili yol tablosu için **etkin** olarak ayarlandığından emin olun.
- P2S VPN kullanıyorsanız, giriş **/Çıkış** numaraları olup olmadığını görmek için Azure Portal yapılandırmayı kontrol edin. Sıfır olmayan sayılar, Azure 'un Şirket içi/içeri trafiği yönlendirme olduğunu gösterir.

   ![giriş/çıkış numaraları](./media/connect-application-instance/ingress-egress-numbers.png)

- İstemci makinesinin (VPN istemcisini çalıştıran) erişmeniz gereken tüm sanal ağlar için rota girişlerine sahip olup olmadığını denetleyin. Yollar içinde depolanır `%AppData%\Roaming\Microsoft\Network\Connections\Cm\<GUID>\routes.txt` .

   ![route.txt](./media/connect-application-instance/route-txt.png)

   Bu görüntüde gösterildiği gibi, her bir sanal ağ için iki giriş ve portalda yapılandırılan VPN uç noktası için üçüncü bir giriş vardır.

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

- Sanal ağ eşlemesi kullanıyorsanız, [ağ geçidi aktarımına Izin ver ' i ayarlama ve uzak ağ geçitleri kullanma](#connect-from-on-premises)yönergelerini izlediğinizden emin olun.

- Azure App Service barındırılan bir uygulamayı bağlamak için sanal ağ eşlemesi kullanıyorsanız ve SQL yönetilen örnek sanal ağının ortak bir IP adresi aralığı varsa, barındırılan uygulama ayarlarınızın, giden trafiğinizin ortak IP ağlarına yönlendirilmesine izin verdiğinden emin olun. [Bölgesel sanal ağ tümleştirmesinde](../../app-service/web-sites-integrate-with-vnet.md#regional-vnet-integration)yönergeleri izleyin.

## <a name="required-versions-of-drivers-and-tools"></a>Sürücülerin ve araçların gerekli sürümleri

SQL yönetilen örneğine bağlanmak istiyorsanız, araçların ve sürücülerin aşağıdaki en düşük sürümleri önerilir:

| Sürücü/araç | Sürüm |
| --- | --- |
|.NET Framework | 4.6.1 (veya .NET Core) |
|ODBC sürücüsü| v17 |
|PHP sürücüsü| 5.2.0 |
|JDBC sürücüsü| 6.4.0 |
|Node.js sürücüsü| 2.1.1 |
|OLEDB sürücüsü| 18.0.2.0 |
|SSMS| 18,0 veya [üzeri](/sql/ssms/download-sql-server-management-studio-ssms) |
|[SMO](/sql/relational-databases/server-management-objects-smo/sql-server-management-objects-smo-programming-guide) | [150](https://www.nuget.org/packages/Microsoft.SqlServer.SqlManagementObjects) veya üzeri |

## <a name="next-steps"></a>Sonraki adımlar

- SQL yönetilen örneği hakkında daha fazla bilgi için bkz. [SQL yönetilen örneği nedir?](sql-managed-instance-paas-overview.md).
- Yeni bir yönetilen örnek oluşturma hakkında bir öğretici için bkz. [yönetilen örnek oluşturma](instance-create-quickstart.md).
