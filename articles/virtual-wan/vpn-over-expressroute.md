---
title: Azure sanal WAN 'da ExpressRoute özel eşlemesi üzerinden siteden siteye VPN bağlantısı oluşturma | Microsoft Docs
description: Bu öğreticide, ExpressRoute özel eşlemesi üzerinden siteden siteye VPN bağlantısı oluşturmak için Azure sanal WAN ' ı nasıl kullanacağınızı öğrenin.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: article
ms.date: 10/11/2019
ms.author: cherylmc
Customer intent: I want to connect my on-premises networks to my VNets using S2S VPN connection over my ExpressRoute private peering using Azure Virtual WAN.
ms.openlocfilehash: 6272d6fe6f8c35c06a8121e10be2dd5a2e5512a8
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73515037"
---
# <a name="create-a-site-to-site-vpn-connection-over-expressroute-private-peering-using-azure-virtual-wan"></a>Azure sanal WAN kullanarak ExpressRoute özel eşlemesi üzerinden siteden siteye VPN bağlantısı oluşturma

Bu makalede, bir ExpressRoute bağlantı hattının özel eşlemesi üzerinden şirket içi ağınızdan Azure 'a bir IPSec/ıKE VPN bağlantısı kurmak için sanal WAN 'ın nasıl kullanılacağı gösterilmektedir. Bu, şirket içi ağlar ve Azure sanal ağları arasında ExpressRoute üzerinden, genel Internet üzerinden geçmeden veya genel IP adreslerini kullanarak şifrelenmiş bir aktarım sağlayabilir.

## <a name="topology-and-routing"></a>Topoloji ve yönlendirme

Aşağıdaki diyagramda ExpressRoute özel eşleme bağlantısı üzerinden VPN örneği gösterilmektedir:

![ExpressRoute üzerinden VPN](./media/vpn-over-expressroute/vwan-vpn-over-er.png)

Diyagramda, ExpressRoute özel eşlemesi üzerinden Azure hub VPN Gateway 'e bağlı şirket içi ağ içindeki bir ağ gösterilmektedir. Bağlantı kurma basittir:

1. ExpressRoute devresi ve özel eşleme ile ExpressRoute bağlantısı kurma
2. Bu belgede açıklandığı gibi VPN bağlantısını oluşturun

Bu yapılandırmanın önemli bir yönü, hem ExpressRoute hem de VPN yolları üzerinde şirket içi ağlar ve Azure arasında yönlendirbir yoldur.

### <a name="traffic-from-on-premises-networks-to-azure"></a>Şirket içi ağlardan Azure 'a trafik

Şirket içi ağlardan Azure 'a giden trafik için Azure önekleri (sanal hub ve hub 'a bağlı olan tüm bağlı bileşen sanal ağları dahil), hem ExpressRoute özel eşleme BGP hem de VPN BGP aracılığıyla tanıtılacaktır. Bu, şirket içi ağlardan Azure 'a yönelik iki ağ yolunun (yollar) oluşmasına neden olur; IPsec korumalı yolundan biri ve doğrudan ExpressRoute üzerinden IPSec koruması **olmadan** bir tane. Şifrelemenin iletişime uygulandığından emin olmak için, diyagramdaki VPN bağlantılı ağ için, şirket içi VPN ağ geçidi üzerinden Azure yollarının doğrudan ExpressRoute yolunda tercih edildiğini doğrulayın.

### <a name="traffic-from-azure-to-on-premises-networks"></a>Azure 'dan şirket içi ağlara giden trafik

Aynı gereksinim, Azure 'dan şirket içi ağlara giden trafiğe de yöneliktir. IPSec yolunun doğrudan ExpressRoute yolunda (IPSec olmadan) tercih edildiğini sağlamak için iki seçeneğiniz vardır:

- VPN bağlantılı ağ için VPN BGP oturumunda daha özel ön ekler duyurur. ExpressRoute özel eşlemesi üzerinden "VPN bağlantılı ağı" ve ardından VPN BGP oturumunda daha belirgin aralıklar dahil olmak üzere daha büyük bir Aralık tanıtabilirsiniz. Örneğin, ExpressRoute üzerinden 10.0.0.0/16, VPN üzerinden 10.0.1.0/24 duyurusunu yapın.

- VPN ve ExpressRoute için kopuk ön ekleri tanıtma. VPN bağlantılı ağ aralıkları diğer ExpressRoute bağlı ağından ayrılarıysa, ön ekleri VPN ve ExpressRoute BGP oturumlarındaki tanıtabilirsiniz. Örneğin, ExpressRoute üzerinden 10.0.0.0/24, VPN üzerinden 10.0.1.0/24 duyurusunu yapın.

Bu örneklerde, Azure, VPN koruması olmadan doğrudan ExpressRoute üzerinden değil, VPN bağlantısı üzerinden 10.0.1.0/24 ' e trafik gönderir.

> [!WARNING]
> **Aynı** önekleri hem ExpressRoute hem de VPN bağlantıları üzerinden tanıyorsanız Azure, **doğrudan VPN koruması olmadan ExpressRoute yolunu kullanır**.
>

## <a name="before-you-begin"></a>Başlamadan önce

[!INCLUDE [Before you begin](../../includes/virtual-wan-tutorial-vwan-before-include.md)]

## <a name="openvwan"></a>1. ağ geçitleri ile sanal bir WAN ve hub oluşturma

Devam etmeden önce aşağıdaki Azure kaynakları ve ilgili şirket içi yapılandırmaların yerinde olması gerekir:

1. Azure sanal WAN
2. [ExpressRoute ağ geçidine](virtual-wan-expressroute-portal.md) ve [VPN ağ geçidine](virtual-wan-site-to-site-portal.md) sahip sanal bir WAN hub 'ı

ExpressRoute ilişkilendirmesine sahip bir Azure sanal WAN ve hub oluşturma adımları için Azure sanal [WAN kullanarak ExpressRoute Ilişkilendirmesi oluşturma](virtual-wan-expressroute-portal.md) ve VPN oluşturma adımları için [Azure sanal WAN kullanarak siteden siteye bağlantı oluşturma](virtual-wan-site-to-site-portal.md) bölümüne bakın. sanal WAN 'da ağ geçidi.

## <a name="site"></a>2. Şirket içi ağ için bir site oluşturun

Site kaynağı, sanal WAN için ExpressRoute VPN siteleri ile aynıdır. Önemli şey, şirket içi VPN cihazının IP adresinin artık özel IP adresi veya 1. adımda oluşturulan ExpressRoute özel eşlemesi aracılığıyla erişilebilen şirket içi ağda genel bir IP adresi olabileceğini ifade edebilir.

> [!NOTE]
> Şirket içi VPN cihazı IP adresi, Azure ExpressRoute özel eşlemesi aracılığıyla sanal WAN hub 'ına tanıtılan adres öneklerinin bir parçası OLMALıDıR.
>

1. Tarayıcınızda Azure portal gidin. Oluşturduğunuz WAN'a tıklayın. WAN sayfasında, **bağlantı**altında **VPN siteleri ' ne tıklayarak VPN** siteleri sayfasını açın.

2. **VPN siteleri** sayfasında **+Site oluştur**’a tıklayın.

3. **Site oluştur** sayfasında aşağıdaki alanları doldurun:

   * **Abonelik**: Aboneliği doğrulayın.
   * **Kaynak grubu** -kullanmak istediğiniz kaynak grubunu seçin veya oluşturun.
   * **Bölge** -VPN site kaynağı için Azure bölgesi.
   * **Ad** -şirket içi sitenize başvurmak istediğiniz addır.
   * **Cihaz satıcısı** -ŞIRKET içi VPN cihazının satıcısı.
   * **Sınır Ağ Geçidi Protokolü** -şirket ıçı ağınız BGP kullanıyorsa "etkinleştir" seçeneğini belirleyin.
   * **Özel adres alanı**: Şirket içi sitenizde yer alan IP adres alanıdır. Bu adres alanına giden trafik, VPN ağ geçidi üzerinden şirket içi ağa yönlendirilir.
   * **Hub 'lar** -bu VPN sitesini bağlamak için bir veya daha fazla hub seçin. Seçili hub 'larda VPN ağ geçitleri zaten oluşturulmuş olmalıdır.

4. Ileri ' ye tıklayın: VPN bağlantısı ayarları için **bağlantılar >** :

   * **Bağlantı adı** -bu bağlantıya başvurmak istediğiniz ad.
   * **Sağlayıcı adı** -bu sitenin Internet hizmet sağlayıcısının adı. ExpressRoute şirket içi ağ durumunda ExpressRoute hizmet sağlayıcısının adı.
   * **Hız** -Internet hizmet bağlantısının hızı veya ExpressRoute bağlantı hattı.
   * **IP adresi** -şirket içi SITENIZDE bulunan VPN CIHAZıNıN genel IP adresi. Ya da ExpressRoute 'un Şirket içi olması durumunda, ExpressRoute aracılığıyla VPN cihazının özel IP adresi.

   BGP etkinse, Azure 'da bu site için oluşturulan tüm bağlantılara uygulanır. Sanal WAN üzerinde BGP 'yi yapılandırmak, Azure VPN ağ geçidinde BGP 'yi yapılandırmaya eşdeğerdir. Şirket içi BGP eşi adresiniz, cihaza VPN 'nizin IP adresi veya VPN sitesinin VNet adres alanı ile *aynı olmamalıdır.* BGP eşi IP 'niz için VPN cihazında farklı bir IP adresi kullanın. Bu aygıttaki geri döngü arabirimine atanmış bir adres olabilir. Ancak, bir APIPA (169,254) *olamaz* . *x*. *x*) adresi. Bu adresi, konumu temsil eden ilgili Yerel Ağ Geçidi’nde belirtin. BGP önkoşulları için bkz. [Azure VPN Gateway Ile BGP hakkında](../vpn-gateway/vpn-gateway-bgp-overview.md).

5. Ileri ' ye tıklayın ve ayar değerlerini denetlemek ve VPN sitesini oluşturmak için **> + Oluştur** ' a tıklayın. Bağlanacak **hub 'ları** seçtiyseniz, şirket içi ağ ve Merkez VPN ağ geçidi arasında bağlantı kurulacaktır.

## <a name="hub"></a>3. ExpressRoute kullanmak için VPN bağlantısı ayarını güncelleştirin

VPN sitesini oluşturduktan ve hub 'a bağlandıktan sonra, bağlantıyı ExpressRoute özel eşlemesini kullanacak şekilde yapılandırmak için aşağıdaki adımları kullanın:

1. Sanal WAN kaynağı sayfasına dönün ve hub kaynağına tıklayın. Ya da VPN sitesinden bağlı hub 'a gidin.

2. **Bağlantı**altında **VPN (siteden siteye)** seçeneğine tıklayın

3. "..." Seçeneğine tıklayın. ExpressRoute üzerinden VPN sitesinde "**VPN bağlantısını bu hub 'A Düzenle**" seçeneğini belirleyin.

4. "**Azure özel IP adresini kullan**" üzerinde "Evet" seçeneğini belirleyin. Bu ayar, hub VPN ağ geçidini, genel IP adresleri yerine, bu bağlantı için ağ geçidinde bulunan hub adres aralığı içinde özel IP adresleri kullanacak şekilde yapılandırır. Bu, şirket içi ağdan gelen trafiğin, bu VPN bağlantısı için genel Internet kullanmak yerine ExpressRoute özel eşleme yollarından geçeceğini güvence altına alır. Aşağıdaki ekran görüntüsünde ayar penceresi gösterilmektedir.

   ![VPN bağlantısı ayarı](./media/vpn-over-expressroute/vpn-link-configuration.png)
   
5. **Kaydet** düğmesine tıklayın.

Merkez VPN ağ geçidi kaydedildikten sonra, ExpressRoute üzerinden şirket içi VPN cihazıyla IPSec/ıKE bağlantıları kurmak için VPN ağ geçidinde özel IP adreslerini kullanır.

## <a name="associate"></a>4. hub VPN Gateway özel IP adreslerini al

Hub VPN ağ geçidinin özel IP adreslerini almak için VPN cihaz yapılandırmasını indirin. Bunlar şirket içi VPN cihazını yapılandırmak için gereklidir.

1. Hub 'ınızın sayfasında, **bağlantı** altında **VPN (siteden siteye)** seçeneğine tıklayın.

2. Genel Bakış sayfasının en üstünde **VPN yapılandırmasını indir**' e tıklayın. Azure, ' Microsoft-Network-[location] ' kaynak grubunda, konumun WAN konumunun bulunduğu bir depolama hesabı oluşturur. Yapılandırmayı VPN cihazlarınıza uyguladıktan sonra bu depolama hesabını silebilirsiniz.

3. Dosya oluşturulduktan sonra bağlantıya tıklayarak indirebilirsiniz.

4. Yapılandırmayı VPN cihazınıza uygulayın.

### <a name="understanding-the-vpn-device-configuration-file"></a>VPN cihazı yapılandırma dosyasını anlama

Cihaz yapılandırma dosyasında şirket içi VPN cihazınızı yapılandırırken kullanacağınız ayarlar bulunur. Bu dosyayı görüntülediğinizde aşağıdaki bilgilere dikkat edin:

* **vpnSiteConfiguration -** Bu bölümde sanal WAN'a bağlanan bir site olarak ayarlanmış cihazın ayrıntıları yer alır. Dal cihazının adını ve genel IP adresini içerir.
* **Vpnsiteconnections-** Bu bölüm aşağıdaki ayarlarla ilgili bilgiler sağlar:

    * Sanal hub sanal ağının **adres alanı**<br/>Örnek:
           ```
           "AddressSpace":"10.51.230.0/24"
           ```
    * Hub'a bağlı sanal ağların **adres alanı**<br>Örnek:
           ```
           "ConnectedSubnets":["10.51.231.0/24"]
            ```
    * vpngateway sanal hub'ının **IP adresleri**. Vpngateway 'in her bağlantısı etkin-etkin yapılandırmada iki tünelde bulunduğundan, bu dosyada her iki IP adresinin de listelendiğini görürsünüz. Bu örnekte, her site için "Örnek0" ve "Instance1" görüntülenir ve genel IP adresleri yerine özel IP adresleridir.<br>Örnek:
           ``` 
           "Instance0":"10.51.230.4"
           "Instance1":"10.51.230.5"
           ```
    * BGP, önceden paylaşılan anahtar vb. gibi **Vpngateway bağlantısı yapılandırma ayrıntıları** . PSK, sizin için otomatik olarak oluşturulan önceden paylaşılmış anahtardır. Dilediğiniz zaman genel bakış sayfasındaki bağlantıyı düzenleyerek özel bir PSK ekleyebilirsiniz.
  
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

* VPN cihazları sayfasındaki yönergeler Sanal WAN için yazılmamıştır ancak yapılandırma dosyasındaki Sanal WAN değerlerini kullanarak VPN cihazınızı el ile yapılandırabilirsiniz. 
* Farklı yapılandırmaya sahip olduğundan VPN Gateway'e özgü indirilebilir cihaz yapılandırma betikleri Sanal WAN ile çalışmaz.
* Yeni bir Sanal WAN hem IKEv1 hem de IKEv2’yi destekleyebilir.
* Sanal WAN yalnızca rota temelli cihazları ve cihaz yönergelerini kullanabilir.

## <a name="viewwan"></a>5. sanal WAN 'nizi görüntüleyin

1. Sanal WAN'a gidin.

2. Genel bakış sayfasında haritadaki her bir nokta bir hub'ı temsil eder. Hub sistem durumu özetini görüntülemek için noktalardan birinin üzerine gidin.

3. Hub'lar ve bağlantılar bölümünde herhangi bir hub'ın durumunu, sitesini, bölgesini, VPN bağlantısı durumunu ve gelen/giden baytları görüntüleyebilirsiniz.

## <a name="viewhealth"></a>6. kaynak sistem durumunu görüntüleyin

1. WAN'ınıza gidin.

2. WAN sayfanızın **Destek ve sorun giderme** bölümünde **Sistem durumu**'na tıklayın ve kaynağınızı görüntüleyin.

## <a name="connectmon"></a>7. bir bağlantıyı izleme

Bir Azure sanal makinesi ile uzak site arasındaki iletişimi izlemek için bir bağlantı oluşturun. Bağlantı izleyici oluşturma hakkında bilgi almak için bkz. [Ağ iletişimini izleme](~/articles/network-watcher/connection-monitor.md). Kaynak alan Azure'daki sanal makinenin IP adresi, hedef IP ise Sitenin IP adresidir.

## <a name="cleanup"></a>8. Kaynakları Temizleme

Bu kaynaklara artık ihtiyacınız kalmadığında, [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) komutunu kullanarak kaynak grubunu ve içerdiği tüm kaynakları kaldırabilirsiniz. "myResourceGroup" yerine kaynak grubunuzun adını yazın ve aşağıdaki PowerShell komutunu çalıştırın:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Sonraki adımlar

Bu makale, sanal WAN kullanarak ExpressRoute özel eşlemesi üzerinden bir VPN bağlantısı oluşturmanıza yardımcı olur. Sanal WAN ve diğer ilgili özellikler hakkında daha fazla bilgi edinmek için bkz. [sanal WAN genel bakış](virtual-wan-about.md) sayfası.
