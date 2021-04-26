---
title: 'ExpressRoute şifrelemesini yapılandırma: Azure sanal WAN için ExpressRoute üzerinden IPSec'
description: Azure sanal WAN kullanarak ExpressRoute özel eşlemesi üzerinden siteden siteye VPN bağlantısı oluşturma hakkında bilgi edinin.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: b8dde3ed76587e2343edaec8626287853ec6ef9b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96487516"
---
# <a name="expressroute-encryption-ipsec-over-expressroute-for-virtual-wan"></a>ExpressRoute şifrelemesi: sanal WAN için ExpressRoute üzerinden IPSec

Bu makalede, Azure sanal WAN kullanarak Azure ExpressRoute bağlantı hattının özel eşlemesi aracılığıyla şirket içi ağınızdan Azure 'a bir IPSec/ıKE VPN bağlantısı kurma konusu gösterilmektedir. Bu teknik, genel İnternet üzerinden veya genel IP adreslerini kullanarak ExpressRoute üzerinden şirket içi ağlar ve Azure sanal ağları arasında şifrelenmiş bir aktarım sağlayabilir.

## <a name="topology-and-routing"></a>Topoloji ve yönlendirme

Aşağıdaki diyagramda ExpressRoute özel eşlemesi üzerinden VPN bağlantısı örneği gösterilmektedir:

:::image type="content" source="./media/vpn-over-expressroute/vwan-vpn-over-er.png" alt-text="ExpressRoute üzerinden VPN":::

Diyagramda, ExpressRoute özel eşlemesi üzerinden Azure hub VPN Gateway 'e bağlı şirket içi ağ içindeki bir ağ gösterilmektedir. Bağlantı kurma basittir:

1. ExpressRoute devresi ve özel eşleme ile ExpressRoute bağlantısı kurun.
2. Bu makalede açıklandığı gibi VPN bağlantısı kurun.

Bu yapılandırmanın önemli bir yönü, hem ExpressRoute hem de VPN yolları üzerinde şirket içi ağlar ve Azure arasında yönlendirbir yoldur.

### <a name="traffic-from-on-premises-networks-to-azure"></a>Şirket içi ağlardan Azure 'a trafik

Şirket içi ağlardan Azure 'a giden trafik için, Azure önekleri (sanal hub ve hub 'a bağlı olan tüm bağlı bileşen sanal ağları dahil), hem ExpressRoute özel eşleme BGP hem de VPN BGP aracılığıyla tanıtılabilir. Bu, şirket içi ağlardan Azure 'a yönelik iki ağ yolu (yollar) ile sonuçlanır:

- IPsec korumalı yoldan biri
- IPSec koruması *olmadan* doğrudan ExpressRoute üzerinden bir tane 

İletişime şifreleme uygulamak için, diyagramdaki VPN bağlantılı ağ için, şirket içi VPN ağ geçidi üzerinden Azure yollarının doğrudan ExpressRoute yolu üzerinden tercih edildiğini doğrulayın.

### <a name="traffic-from-azure-to-on-premises-networks"></a>Azure 'dan şirket içi ağlara giden trafik

Aynı gereksinim, Azure 'dan şirket içi ağlara giden trafiğe de yöneliktir. IPSec yolunun doğrudan ExpressRoute yolu (IPSec olmadan) üzerinden tercih edildiğini sağlamak için iki seçeneğiniz vardır:

- VPN bağlantılı ağ için VPN BGP oturumunda daha özel ön ekler duyurur. ExpressRoute özel eşlemesi üzerinden VPN bağlantılı ağı kapsayan daha büyük bir Aralık verebilir ve ardından VPN BGP oturumunda daha belirgin aralıklar sağlayabilirsiniz. Örneğin, ExpressRoute üzerinden 10.0.0.0/16, VPN üzerinden 10.0.1.0/24 duyurusunu yapın.

- VPN ve ExpressRoute için kopuk ön ekleri tanıtma. VPN bağlantılı ağ aralıkları diğer ExpressRoute bağlantılı ağlardan ayrılarıysa, ön ekleri VPN ve ExpressRoute BGP oturumlarındaki tanıtabilirsiniz. Örneğin, ExpressRoute üzerinden 10.0.0.0/24, VPN üzerinden 10.0.1.0/24 duyurusunu yapın.

Bu örneklerde, Azure, VPN koruması olmadan doğrudan ExpressRoute üzerinden değil, VPN bağlantısı üzerinden 10.0.1.0/24 ' e trafik gönderir.

> [!WARNING]
> *Aynı* önekleri hem ExpressRoute hem de VPN bağlantıları üzerinden tanıyorsanız Azure, doğrudan VPN koruması olmadan ExpressRoute yolunu kullanır.
>

## <a name="before-you-begin"></a>Başlamadan önce

[!INCLUDE [Before you begin](../../includes/virtual-wan-tutorial-vwan-before-include.md)]

## <a name="1-create-a-virtual-wan-and-hub-with-gateways"></a><a name="openvwan"></a>1. ağ geçitleri ile sanal bir WAN ve hub oluşturma

Devam etmeden önce aşağıdaki Azure kaynakları ve ilgili şirket içi yapılandırmaların yerinde olması gerekir:

- Azure sanal WAN
- [ExpressRoute ağ geçidine](virtual-wan-expressroute-portal.md) ve [VPN ağ geçidine](virtual-wan-site-to-site-portal.md) sahip sanal bir WAN hub 'ı

Azure sanal WAN ve ExpressRoute ilişkilendirmesi içeren bir hub oluşturma adımları için bkz. [Azure sanal WAN kullanarak ExpressRoute Ilişkilendirmesi oluşturma](virtual-wan-expressroute-portal.md). Sanal WAN 'da VPN Gateway oluşturma adımları için bkz. [Azure sanal WAN kullanarak siteden siteye bağlantı oluşturma](virtual-wan-site-to-site-portal.md).

## <a name="2-create-a-site-for-the-on-premises-network"></a><a name="site"></a>2. Şirket içi ağ için bir site oluşturun

Site kaynağı, sanal bir WAN için ExpressRoute VPN siteleri ile aynıdır. Şirket içi VPN cihazının IP adresi artık özel bir IP adresi veya 1. adımda oluşturulan ExpressRoute özel eşlemesi aracılığıyla erişilebilen şirket içi ağda genel bir IP adresi olabilir.

> [!NOTE]
> Şirket içi VPN cihazının IP adresi, Azure ExpressRoute özel eşlemesi aracılığıyla sanal WAN hub 'ına tanıtılan adres öneklerinin bir parçası *olmalıdır* .
>

1. Tarayıcınızda Azure portal gidin. 
1. Oluşturduğunuz hub 'ı seçin. Sanal WAN hub 'ı sayfasında, **bağlantı** altında **VPN siteleri**' ni seçin.
1. **VPN siteleri** sayfasında **+ site oluştur**' u seçin.
1. **Site oluştur** sayfasında aşağıdaki alanları doldurun:
   * **Abonelik**: aboneliği doğrulayın.
   * **Kaynak grubu**: kullanmak istediğiniz kaynak grubunu seçin veya oluşturun.
   * **Bölge**: VPN site kaynağı için Azure bölgesini girin.
   * **Ad**: şirket içi sitenize başvurmak istediğiniz adı girin.
   * **Cihaz satıcısı**: ŞIRKET içi VPN cihazının satıcısını girin.
   * **Sınır Ağ Geçidi Protokolü**: Şirket ıçı ağınız BGP kullanıyorsa "etkinleştir" i seçin.
   * **Özel adres alanı**: şirket içi SITENIZDE bulunan IP adres alanını girin. Bu adres alanına giden trafik, VPN ağ geçidi üzerinden şirket içi ağa yönlendirilir.
   * **Hub 'lar**: Bu VPN sitesini bağlamak için bir veya daha fazla hub seçin. Seçili hub 'larda VPN ağ geçitleri zaten oluşturulmuş olmalıdır.
1. Ileri ' yi seçin: VPN bağlantısı ayarları için **bağlantılar >** :
   * **Bağlantı adı**: bu bağlantıya başvurmak istediğiniz ad.
   * **Sağlayıcı adı**: Bu sitenin Internet hizmet sağlayıcısının adı. ExpressRoute şirket içi ağ için, ExpressRoute hizmet sağlayıcısının adıdır.
   * **Hız**: Internet hizmet bağlantısı veya ExpressRoute bağlantı hattının hızı.
   * **IP adresi**: şirket içi SITENIZDE bulunan VPN CIHAZıNıN genel IP adresi. Ya da şirket içi ExpressRoute için, ExpressRoute aracılığıyla VPN cihazının özel IP adresidir.

   BGP etkinse, Azure 'da bu site için oluşturulan tüm bağlantılara uygulanır. Sanal WAN üzerinde BGP 'yi yapılandırmak, Azure VPN ağ geçidinde BGP 'yi yapılandırmaya eşdeğerdir. 
   
   Şirket içi BGP eş adresiniz, cihazdaki VPN 'nizin IP adresi veya VPN sitesinin sanal ağ adresi alanı ile *aynı olmamalıdır.* BGP eşi IP 'niz için VPN cihazında farklı bir IP adresi kullanın. Bu aygıttaki geri döngü arabirimine atanmış bir adres olabilir. Ancak, bir APIPA (169,254 *) olamaz.* *x*. *x*) adresi. Bu adresi, konumu temsil eden ilgili VPN sitesinde belirtin. BGP önkoşulları için bkz. [Azure VPN Gateway Ile BGP hakkında](../vpn-gateway/vpn-gateway-bgp-overview.md).

1. Ileri ' yi seçin: ayar değerlerini denetlemek ve VPN sitesini oluşturmak için **>+ oluştur** ' u seçin. Bağlanacak **hub 'ları** seçtiyseniz, şirket içi ağ ve Merkez VPN ağ geçidi arasında bağlantı kurulacaktır.

## <a name="3-update-the-vpn-connection-setting-to-use-expressroute"></a><a name="hub"></a>3. ExpressRoute kullanmak için VPN bağlantısı ayarını güncelleştirin

VPN sitesini oluşturup hub 'a bağlandıktan sonra, bağlantıyı ExpressRoute özel eşlemesini kullanacak şekilde yapılandırmak için aşağıdaki adımları kullanın:

1. Sanal WAN kaynak sayfasına dönün ve hub kaynağını seçin. Ya da VPN sitesinden bağlı hub 'a gidin.

   :::image type="content" source="./media/vpn-over-expressroute/hub-selection.png" alt-text="Bir hub seçin":::
1. **Bağlantı** altında **VPN (siteden siteye)** seçeneğini belirleyin.

   :::image type="content" source="./media/vpn-over-expressroute/vpn-select.png" alt-text="VPN seçin (siteden siteye)":::
1. ExpressRoute üzerinden VPN sitesinde üç nokta (**...**) simgesini SEÇIN ve **VPN bağlantısını bu hub 'a Düzenle**' yi seçin.

   :::image type="content" source="./media/vpn-over-expressroute/config-menu.png" alt-text="Yapılandırma menüsünü girin":::
1. **Azure özel IP adresini kullan** için **Evet**' i seçin. Bu ayar, hub VPN ağ geçidini, genel IP adresleri yerine, bu bağlantı için ağ geçidinde bulunan hub adres aralığı içinde özel IP adresleri kullanacak şekilde yapılandırır. Bu, şirket içi ağdan gelen trafiğin bu VPN bağlantısı için genel internet kullanmak yerine ExpressRoute özel eşleme yollarından geçeceğini güvence altına alır. Aşağıdaki ekran görüntüsünde Bu ayar gösterilmektedir:

   :::image type="content" source="./media/vpn-over-expressroute/vpn-link-configuration.png" alt-text="VPN bağlantısı için özel IP adresi kullanma ayarı" border="false":::
1. **Kaydet**’i seçin.

Değişikliklerinizi kaydettikten sonra, Merkez VPN ağ geçidi, ExpressRoute üzerinden şirket içi VPN cihazıyla IPSec/ıKE bağlantıları kurmak için VPN ağ geçidinde özel IP adreslerini kullanır.

## <a name="4-get-the-private-ip-addresses-for-the-hub-vpn-gateway"></a><a name="associate"></a>4. hub VPN Gateway için özel IP adreslerini alın

Hub VPN ağ geçidinin özel IP adreslerini almak için VPN cihaz yapılandırmasını indirin. Şirket içi VPN cihazını yapılandırmak için bu adreslere ihtiyacınız vardır.

1. Hub 'ınızın sayfasında, **bağlantı** altında **VPN (siteden siteye)** seçeneğini belirleyin.
1. **Genel bakış** sayfasının en üstünde **VPN yapılandırmasını indir**' i seçin. 

   Azure, "Microsoft-Network-[location]" kaynak grubunda, *KONUMUN* WAN konumunun bulunduğu bir depolama hesabı oluşturur. Yapılandırmayı VPN cihazlarınıza uyguladıktan sonra, bu depolama hesabını silebilirsiniz.
1. Dosya oluşturulduktan sonra, indirmek için bağlantıyı seçin.
1. Yapılandırmayı VPN cihazınıza uygulayın.

### <a name="vpn-device-configuration-file"></a>VPN cihazı yapılandırma dosyası

Cihaz yapılandırma dosyası, şirket içi VPN cihazınızı yapılandırırken kullanılacak ayarları içerir. Bu dosyayı görüntülediğinizde aşağıdaki bilgilere dikkat edin:

* **Vpnsiteconfiguration**: Bu bölüm, sanal WAN 'a bağlanan bir site olarak ayarlanan cihaz ayrıntılarını gösterir. Bu, şube cihazının adını ve genel IP adresini içerir.
* **Vpnsiteconnections**: Bu bölüm aşağıdaki ayarlarla ilgili bilgiler sağlar:

    * Sanal hub 'ın sanal ağının adres alanı.<br/>Örnek:
           ```
           "AddressSpace":"10.51.230.0/24"
           ```
    * Hub 'a bağlı sanal ağların adres alanı.<br>Örnek:
           ```
           "ConnectedSubnets":["10.51.231.0/24"]
            ```
    * Sanal hub 'ın VPN ağ geçidinin IP adresleri. Her VPN Ağ Geçidi bağlantısı etkin-etkin yapılandırmada iki tünelde bulunduğundan, bu dosyada her iki IP adresinin de listelendiğini görürsünüz. Bu örnekte, `Instance0` ve `Instance1` her site için ve genel IP adresleri yerıne özel IP adresleri görürsünüz.<br>Örnek:
           ``` 
           "Instance0":"10.51.230.4"
           "Instance1":"10.51.230.5"
           ```
    * VPN Ağ Geçidi bağlantısı için BGP ve önceden paylaşılan anahtar gibi yapılandırma ayrıntıları. Önceden paylaşılan anahtar sizin için otomatik olarak oluşturulur. Bağlantıyı, özel bir önceden paylaşılan anahtar için **genel bakış** sayfasında her zaman düzenleyebilirsiniz.
  
### <a name="example-device-configuration-file"></a>Örnek cihaz yapılandırma dosyası

```
[{
      "configurationVersion":{
        "LastUpdatedTime":"2019-10-11T05:57:35.1803187Z",
        "Version":"5b096293-edc3-42f1-8f73-68c14a7c4db3"
      },
      "vpnSiteConfiguration":{
        "Name":"VPN-over-ER-site",
        "IPAddress":"172.24.127.211",
        "LinkName":"VPN-over-ER"
      },
      "vpnSiteConnections":[{
        "hubConfiguration":{
          "AddressSpace":"10.51.230.0/24",
          "Region":"West US 2",
          "ConnectedSubnets":["10.51.231.0/24"]
        },
        "gatewayConfiguration":{
          "IpAddresses":{
            "Instance0":"10.51.230.4",
            "Instance1":"10.51.230.5"
          }
        },
        "connectionConfiguration":{
          "IsBgpEnabled":false,
          "PSK":"abc123",
          "IPsecParameters":{"SADataSizeInKilobytes":102400000,"SALifeTimeInSeconds":3600}
        }
      }]
    },
    {
      "configurationVersion":{
        "LastUpdatedTime":"2019-10-11T05:57:35.1803187Z",
        "Version":"fbdb34ea-45f8-425b-9bc2-4751c2c4fee0"
      },
      "vpnSiteConfiguration":{
        "Name":"VPN-over-INet-site",
        "IPAddress":"13.75.195.234",
        "LinkName":"VPN-over-INet"
      },
      "vpnSiteConnections":[{
        "hubConfiguration":{
          "AddressSpace":"10.51.230.0/24",
          "Region":"West US 2",
          "ConnectedSubnets":["10.51.231.0/24"]
        },
        "gatewayConfiguration":{
          "IpAddresses":{
            "Instance0":"51.143.63.104",
            "Instance1":"52.137.90.89"
          }
        },
        "connectionConfiguration":{
          "IsBgpEnabled":false,
          "PSK":"abc123",
          "IPsecParameters":{"SADataSizeInKilobytes":102400000,"SALifeTimeInSeconds":3600}
        }
      }]
}]
```

### <a name="configuring-your-vpn-device"></a>VPN cihazınızı yapılandırma

Cihazınızı yapılandırma yönergelerine ihtiyaç duyarsanız [VPN cihazı yapılandırma betikleri sayfasındaki](~/articles/vpn-gateway/vpn-gateway-about-vpn-devices.md#configscripts) yönergeleri aşağıdaki uyarılarla birlikte kullanabilirsiniz:

* VPN cihazı sayfasındaki yönergeler bir sanal WAN için yazılmaz. Ancak VPN cihazınızı el ile yapılandırmak için yapılandırma dosyasındaki sanal WAN değerlerini kullanabilirsiniz. 
* VPN ağ geçidi için kullanılan indirilebilir cihaz yapılandırma betikleri, yapılandırma farklı olduğundan sanal WAN için çalışmaz.
* Yeni bir sanal WAN, hem IKEv1 hem de IKEv2 destekleyebilir.
* Bir sanal WAN, yalnızca rota tabanlı VPN cihazlarını ve cihaz yönergelerini kullanabilir.

## <a name="5-view-your-virtual-wan"></a><a name="viewwan"></a>5. sanal WAN 'nizi görüntüleyin

1. Sanal WAN 'a gidin.
1. **Genel bakış** sayfasında, haritadaki her bir nokta bir hub 'ı temsil eder.
1. Hub **'lar ve bağlantılar** bölümünde hub, site, bölge ve VPN bağlantı durumunu görüntüleyebilirsiniz. Ayrıca, baytları de görüntüleyebilirsiniz.

## <a name="6-monitor-a-connection"></a><a name="connectmon"></a>6. bir bağlantıyı izleme

Bir Azure sanal makinesi (VM) ile uzak site arasındaki iletişimi izlemeye yönelik bir bağlantı oluşturun. Bağlantı izleyici oluşturma hakkında bilgi almak için bkz. [Ağ iletişimini izleme](~/articles/network-watcher/connection-monitor.md). Kaynak alanı Azure 'daki VM IP 'si, hedef IP ise site IP 'si olur.

## <a name="7-clean-up-resources"></a><a name="cleanup"></a>7. Kaynakları Temizleme

Bu kaynaklara artık ihtiyacınız kalmadığında, [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) komutunu kullanarak kaynak grubunu ve içerdiği tüm kaynakları kaldırabilirsiniz. Aşağıdaki PowerShell komutunu çalıştırın ve yerine `myResourceGroup` kaynak grubunuzun adını koyun:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Sonraki adımlar

Bu makale, sanal WAN kullanarak ExpressRoute özel eşlemesi üzerinden bir VPN bağlantısı oluşturmanıza yardımcı olur. Sanal WAN ve ilgili özellikler hakkında daha fazla bilgi edinmek için bkz. [sanal WAN 'a genel bakış](virtual-wan-about.md).
