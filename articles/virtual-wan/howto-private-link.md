---
title: Sanal WAN genelinde özel bağlantı hizmeti paylaşma
titleSuffix: Azure Virtual WAN
description: Sanal WAN 'da özel bağlantı yapılandırma adımları
services: virtual-wan
author: erjosito
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: jomore
ms.custom: fasttrack-new
ms.openlocfilehash: cc8e7314c941035207ecf809a9d85ef46bd58379
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92913764"
---
# <a name="use-private-link-in-virtual-wan"></a>Sanal WAN 'da özel bağlantı kullan

[Azure özel bağlantı](../private-link/private-link-overview.md) , [Özel uç noktaları](../private-link/private-endpoint-overview.md)ortaya çıkarmak için özel IP adresi bağlantısı kullanarak Azure hizmet olarak platform tekliflerini bağlamanıza olanak tanıyan bir teknolojidir. Azure sanal WAN ile herhangi bir sanal hub 'a bağlı sanal ağlardan birinde özel bir uç nokta dağıtabilirsiniz. Bu, aynı sanal WAN 'a bağlı başka bir sanal ağ veya dala bağlantı sağlar.

## <a name="before-you-begin"></a>Başlamadan önce

Bu makaledeki adımlarda, bir veya daha fazla hub ile sanal WAN dağıtmış olduğunuz ve sanal WAN 'a bağlı en az iki sanal ağ olduğu varsayılır.

Yeni bir sanal WAN ve yeni bir hub oluşturmak için aşağıdaki makalelerdeki adımları kullanın:

* [Sanal WAN oluşturma](virtual-wan-site-to-site-portal.md#openvwan)
* [Hub oluşturma](virtual-wan-site-to-site-portal.md#hub)
* [Bir sanal ağı bir hub'a bağlama](virtual-wan-site-to-site-portal.md#hub)

## <a name="create-a-private-link-endpoint"></a><a name="endpoint"></a>Özel bağlantı uç noktası oluşturma

Birçok farklı hizmet için özel bir bağlantı uç noktası oluşturabilirsiniz. Bu örnekte, Azure SQL veritabanı 'nı kullanacağız. [Hızlı başlangıç: Azure Portal kullanarak](../private-link/create-private-endpoint-portal.md)BIR Azure SQL veritabanı için özel uç nokta oluşturma hakkında daha fazla bilgi edinebilirsiniz. Aşağıdaki görüntüde, Azure SQL veritabanı 'nın ağ yapılandırması gösterilmektedir:

:::image type="content" source="./media/howto-private-link/create-private-link.png" alt-text="özel bağlantı oluştur" lightbox="./media/howto-private-link/create-private-link.png":::

Azure SQL veritabanını oluşturduktan sonra özel uç nokta IP adresini özel uç noktalarınıza göz atarak doğrulayabilirsiniz:

:::image type="content" source="./media/howto-private-link/endpoints.png" alt-text="özel uç noktalar" lightbox="./media/howto-private-link/endpoints.png":::

Oluşturduğumuz özel uç noktasına tıkladığınızda, özel IP adresinin yanı sıra tam etki alanı adını (FQDN) görmeniz gerekir. Özel uç noktanın dağıtıldığı VNet aralığında bir IP adresi olduğunu unutmayın (10.1.3.0/24):

:::image type="content" source="./media/howto-private-link/sql-endpoint.png" alt-text="SQL uç noktası" lightbox="./media/howto-private-link/sql-endpoint.png":::

## <a name="verify-connectivity-from-the-same-vnet"></a><a name="connectivity"></a>Aynı VNet 'ten gelen bağlantıyı doğrulama

Bu örnekte, MS SQL araçları yüklü olan bir Ubuntu sanal makinesinden Azure SQL veritabanı ile bağlantıyı doğrulayacağız. İlk adım, DNS çözümünün çalıştığı ve Azure SQL veritabanı tam etki alanı adının özel bir uç noktanın dağıtıldığı sanal ağda (10.1.3.0/24) özel bir IP adresine çözümlendiğini doğrulamadır:

```bash
$ nslookup wantest.database.windows.net
Server:         127.0.0.53
Address:        127.0.0.53#53

Non-authoritative answer:
wantest.database.windows.net    canonical name = wantest.privatelink.database.windows.net.
Name:   wantest.privatelink.database.windows.net
Address: 10.1.3.228
```

Önceki çıktıda görebileceğiniz gibi, FQDN, özel `wantest.database.windows.net` `wantest.privatelink.database.windows.net` uç nokta ile oluşturulan özel DNS BÖLGESININ özel IP adresine çözümlendiğine yönelik olarak eşlenir `10.1.3.228` . Özel DNS bölgesine bakmak, özel IP adresine eşlenmiş özel uç nokta için bir kayıt olduğunu doğrulayacaktır:

:::image type="content" source="./media/howto-private-link/dns-zone.png" alt-text="DNS bölgesi" lightbox="./media/howto-private-link/dns-zone.png":::

Doğru DNS çözümlemesini doğruladıktan sonra veritabanına bağlanmayı deneyebiliriz:

```bash
$ query="SELECT CONVERT(char(15), CONNECTIONPROPERTY('client_net_address'));"
$ sqlcmd -S wantest.database.windows.net -U $username -P $password -Q "$query"

10.1.3.75
```

Gördüğünüz gibi, SQL Server 'ın istemciden gördüğü kaynak IP adresini bize veren özel bir SQL sorgusu kullanıyoruz. Bu durumda, sunucu istemciyi kendi özel IP 'si () ile görür; bu da `10.1.3.75` trafiğin VNET 'ten doğrudan özel uç noktaya gider anlamına gelir.

`username` `password` Bu kılavuzdaki örneklerin çalışmasını sağlamak için değişkenleri ve Azure SQL veritabanında tanımlanan kimlik bilgileriyle eşleşecek şekilde ayarlamanız gerektiğini unutmayın.

## <a name="connect-from-a-different-vnet"></a><a name="vnet"></a>Farklı bir VNet 'ten Bağlan

Azure sanal WAN 'daki bir VNet 'in özel uç noktayla bağlantısı olduğuna göre, sanal WAN 'a bağlı olan diğer sanal ağların ve dalların hepsi de buna erişebilir. Azure sanal WAN tarafından desteklenen herhangi bir modelden bağlantı sağlamanız gerekir (örneğin, herhangi bir [senaryo](scenario-any-to-any.md) veya [paylaşılan hizmetler sanal ağı](scenario-shared-services-vnet.md)gibi), iki örneği adlandırın.

VNet veya dal arasında özel uç noktanın dağıtıldığı VNet arasında bağlantı kurulduktan sonra, DNS çözümlemesini yapılandırmanız gerekir:

* VNet 'ten özel uç noktaya bağlantı varsa, Azure SQL veritabanı ile oluşturulan aynı özel bölgeyi kullanabilirsiniz.
* Bir daldan özel uç noktaya (siteden siteye VPN, Noktadan siteye VPN veya ExpressRoute) bağlanılıyorsa, şirket içi DNS çözümlemesi kullanmanız gerekir.

Bu örnekte, farklı bir VNet 'ten bağlanacağız. bu nedenle, iş yüklerinin Azure SQL veritabanı tam etki alanı adını özel IP adresine çözümleyebilmesi için, ilk olarak özel DNS bölgesini yeni VNet 'e bağlayacağız. Bu, özel DNS bölgesinin yeni VNet 'e bağlanması sırasında yapılır:

:::image type="content" source="./media/howto-private-link/dns-link.png" alt-text="DNS bağlantısı" lightbox="./media/howto-private-link/dns-link.png":::

Artık bağlı VNet 'teki tüm sanal makineler, Azure SQL veritabanı FQDN 'sini özel bağlantının özel IP adresine doğru şekilde çözmelidir:

```bash
$ nslookup wantest.database.windows.net
Server:         127.0.0.53
Address:        127.0.0.53#53

Non-authoritative answer:
wantest.database.windows.net    canonical name = wantest.privatelink.database.windows.net.
Name:   wantest.privatelink.database.windows.net
Address: 10.1.3.228
```

Bu sanal ağın (10.1.1.0/24), Özel uç noktanın yapılandırıldığı orijinal VNet 'e bağlantısının olduğunu (10.1.3.0/24) sağlamak için, VNet 'teki herhangi bir sanal makinede etkin yol tablosunu doğrulayabilirsiniz:

:::image type="content" source="./media/howto-private-link/effective-routes.png" alt-text="geçerli rotalar" lightbox="./media/howto-private-link/effective-routes.png":::

Gördüğünüz gibi, Azure sanal WAN 'daki sanal ağ geçitleri tarafından eklenen VNet 10.1.3.0/24 ' e işaret eden bir yol vardır. Artık son olarak veritabanına bağlantıyı test edebilirsiniz:

```bash
$ query="SELECT CONVERT(char(15), CONNECTIONPROPERTY('client_net_address'));"
$ sqlcmd -S wantest.database.windows.net -U $username -P $password -Q "$query"

10.1.1.75
```

Bu örnekte, bir sanal WAN 'a bağlı sanal ağlardan birinde özel bir uç nokta oluşturmanın, sanal WAN 'daki VNET 'lerin ve dalların geri kalanı ile bağlantı sağladığını gördük.

## <a name="next-steps"></a>Sonraki adımlar

Sanal WAN hakkında daha fazla bilgi için bkz. [SSS](virtual-wan-faq.md).
