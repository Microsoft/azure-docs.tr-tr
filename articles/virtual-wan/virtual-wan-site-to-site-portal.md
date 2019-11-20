---
title: 'Azure sanal WAN: siteden siteye bağlantılar oluşturma'
description: Bu öğreticide Azure Sanal WAN kullanarak Azure'a siteden siteye bağlantı oluşturmayı öğreneceksiniz.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 11/04/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to connect my local site to my VNets using Virtual WAN and I don't want to go through a Virtual WAN partner.
ms.openlocfilehash: 82f662dd00805cf53c0581fb0a2b3322a0207a11
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74005722"
---
# <a name="tutorial-create-a-site-to-site-connection-using-azure-virtual-wan"></a>Öğretici: Azure Sanal WAN kullanarak siteden siteye bağlantı oluşturma

Bu öğreticide Sanal WAN kullanarak Azure'daki kaynaklarınıza bir IPsec/IKE (IKEv1 ve IKEv2) VPN bağlantısı üzerinden bağlanmayı öğreneceksiniz. Bu bağlantı türü için, şirket içinde yer alan ve kendisine atanmış dışarıya yönelik bir genel IP adresi atanmış olan bir VPN cihazı gerekir. Sanal WAN hakkında daha fazla bilgi için bkz. [Sanal WAN'a Genel Bakış](virtual-wan-about.md).

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Sanal WAN oluşturma
> * Hub oluşturma
> * Site oluşturma
> * Bir siteyi hub 'a bağlama
> * VPN sitesini bir hub 'a bağlama
> * Bir sanal ağı bir hub'a bağlama
> * Yapılandırma dosyasını indir
> * Sanal WAN'ınızı görüntüleme

> [!NOTE]
> Birden fazla siteniz varsa bu yapılandırmayı oluşturmak için [Sanal WAN iş ortağı](https://aka.ms/virtualwan) kullanmanız gerekir. Ancak ağ bağlantıları ve kendi VPN cihazınızı yapılandırma konularında deneyimliyseniz bu yapılandırmayı kendiniz oluşturabilirsiniz.
>

![Sanal WAN diyagramı](./media/virtual-wan-about/virtualwan.png)

## <a name="before-you-begin"></a>Başlamadan önce

Yapılandırmanıza başlamadan önce aşağıdaki ölçütleri karşıladığınızı doğrulayın:

* Bağlanmak istediğiniz bir sanal ağınız var. Şirket içi ağlarınızın alt ağlarının hiçbirinin, bağlanmak istediğiniz sanal ağlarla çakışmadığından emin olun. Azure portal bir sanal ağ oluşturmak için [hızlı](../virtual-network/quick-create-portal.md)başlangıca bakın.

* Sanal ağınızda sanal ağ geçidi yok. Sanal ağınızda bir ağ geçidi (VPN veya ExpressRoute) varsa, tüm ağ geçitlerini kaldırmanız gerekir. Bu yapılandırma, sanal ağın bunun yerine sanal WAN hub ağ geçidine bağlanmasını gerektirir.

* Hub bölgenizden bir IP adresi aralığı edinin. Hub, sanal WAN tarafından oluşturulan ve kullanılan bir sanal ağ. Hub için belirttiğiniz adres aralığı, bağlandığınız mevcut sanal ağlarınızla çakışamaz. Ayrıca bağlandığınız şirket içi adres aralıklarıyla da çakışamaz. Şirket içi ağ yapılandırmanızda bulunan IP adresi aralıklarını tanımıyorsanız, sizin için bu ayrıntıları sağlayabilecek biriyle koordine edebilirsiniz.

* Azure aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="openvwan"></a>Sanal WAN oluşturma

Bir tarayıcıdan Azure portal gidin ve Azure hesabınızla oturum açın.

1. Sanal WAN sayfasına gidin. Portalda **+Kaynak oluştur**’a tıklayın. Arama kutusuna **sanal WAN** yazın ve ENTER ' u seçin.
2. Sonuçlardan **sanal WAN** ' ı seçin. Sanal WAN sayfasında, **Oluştur** ' a tıklayarak WAN sayfası oluştur sayfasını açın.
3. **WAN oluştur** sayfasında, **temel bilgiler** sekmesinde aşağıdaki alanları girin:

   ![Sanal WAN](./media/virtual-wan-site-to-site-portal/vwan.png)

   * **Abonelik**: Kullanmak istediğiniz aboneliği seçin.
   * **Kaynak grubu** -yeni oluştur veya var olanı kullan.
   * **Kaynak grubu konumu** -açılan listeden bir kaynak konumu seçin. WAN, global bir kaynaktır ve belirli bir bölgeyle sınırlı değildir. Ancak oluşturduğunuz WAN kaynağını daha kolay yönetmek ve bulmak için bir bölge seçmeniz gerekir.
   * **Ad** -WAN 'nizi çağırmak istediğiniz adı yazın.
   * **Şunu yazın:** Temel veya standart. Temel bir WAN oluşturursanız, yalnızca temel bir hub oluşturabilirsiniz. Temel hub 'lar yalnızca VPN siteden siteye bağlantı özelliğine sahiptir.
4. Alanları doldurmayı tamamladıktan sonra, **gözden geçir + oluştur**' u seçin.
5. Doğrulama başarılı olduktan sonra, sanal WAN oluşturmak için **Oluştur** ' u seçin.

## <a name="hub"></a>Merkez oluşturma

Hub, siteden siteye, ExpressRoute veya Noktadan siteye işlevselliği için ağ geçitleri içerebilen bir sanal ağ. Hub oluşturulduktan sonra site eklemeseniz bile hub için ücretlendirilirsiniz. Sanal hub 'da siteden siteye VPN ağ geçidinin oluşturulması 30 dakika sürer.

[!INCLUDE [Create a hub](../../includes/virtual-wan-tutorial-s2s-hub-include.md)]

## <a name="site"></a>Site oluşturma

Artık fiziksel konumlarınıza karşılık gelen siteleri oluşturmaya hazırsınız. Fiziksel konumlarınıza karşılık gelecek sayıda site oluşturabilirsiniz. Örneğin İstanbul'da, Ankara'da ve İzmir'de birer şubeniz varsa üç ayrı site oluşturmanız gerekir. Bu siteler şirket içi VPN cihazı uç noktalarını içerir. Sanal bir WAN 'da, sanal hub başına en fazla 1000 site oluşturabilirsiniz. Birden çok hub olsaydıysanız, bu hub 'lardan her biri için 1000 oluşturabilirsiniz. Sanal WAN iş ortağı (bağlantı ekleme) CPE cihazı varsa, Azure 'a Otomasyon hakkında bilgi edinmek için bunlarla görüşün. Genellikle otomasyon, büyük ölçekli şube bilgilerini Azure 'a aktarmak ve CPE 'dan Azure sanal WAN VPN Gateway 'e bağlantı kurmak için basit tıklama deneyimi (burada Azure 'dan CPE iş ortaklarına olan otomasyon kılavuzunun bir bağlantısı) sağlar.

[!INCLUDE [Create a site](../../includes/virtual-wan-tutorial-s2s-site-include.md)]

## <a name="connectsites"></a>VPN sitesini hub 'a bağlama

Bu adımda, VPN sitenizi hub 'a bağlayaöğreneceksiniz.

[!INCLUDE [Connect VPN sites](../../includes/virtual-wan-tutorial-s2s-connect-vpn-site-include.md)]

## <a name="vnet"></a>VNet 'i hub 'a bağlama

Bu adımda, hub 'ınız ile VNet arasında bağlantı oluşturursunuz. Bu adımları bağlanmak istediğiniz tüm sanal ağlar için tekrarlayın.

1. Sanal WAN'ınızın sayfasında **Sanal ağ bağlantıları**'na tıklayın.
2. Sanal ağ bağlantısı sayfasında **+Bağlantı ekle**'ye tıklayın.
3. **Bağlantı ekle** sayfasında aşağıdaki alanları doldurun:

    * **Bağlantı adı**: Bağlantınıza bir ad verin.
    * **Hub'lar**: Bu bağlantıyla ilişkilendirmek istediğiniz hub'ı seçin.
    * **Abonelik**: Aboneliği doğrulayın.
    * **Sanal ağ**: Bu hub'a bağlamak istediğiniz sanal ağı seçin. Sanal ağda önceden var olan bir sanal ağ geçidi bulunamaz.
4. Sanal ağ bağlantısını oluşturmak için **Tamam** ' ı tıklatın.

## <a name="device"></a>VPN yapılandırmasını indir

Şirket içi VPN cihazınızı yapılandırmak için VPN cihazı yapılandırmasını kullanın.

1. Sanal WAN'ınızın sayfasında **Genel bakış**'a tıklayın.
2. **Hub-> VPNSite** sayfasının en üstünde **VPN yapılandırması 'nı indir**' e tıklayın. Azure, ' Microsoft-Network-[location] ' kaynak grubunda, konumun WAN konumunun bulunduğu bir depolama hesabı oluşturur. Yapılandırmayı VPN cihazlarınıza uyguladıktan sonra bu depolama hesabını silebilirsiniz.
3. Dosya oluşturulduktan sonra bağlantıya tıklayarak indirebilirsiniz.
4. Yapılandırmayı şirket içi VPN cihazınıza uygulayın.

### <a name="understanding-the-vpn-device-configuration-file"></a>VPN cihazı yapılandırma dosyasını anlama

Cihaz yapılandırma dosyasında şirket içi VPN cihazınızı yapılandırırken kullanacağınız ayarlar bulunur. Bu dosyayı görüntülediğinizde aşağıdaki bilgilere dikkat edin:

* **vpnSiteConfiguration -** Bu bölümde sanal WAN'a bağlanan bir site olarak ayarlanmış cihazın ayrıntıları yer alır. Dal cihazının adını ve genel IP adresini içerir.
* **Vpnsiteconnections-** Bu bölüm aşağıdaki ayarlarla ilgili bilgiler sağlar:

    * Sanal hub sanal ağının **adres alanı**<br>Örnek:
 
        ```
        "AddressSpace":"10.1.0.0/24"
        ```
    * Hub'a bağlı sanal ağların **adres alanı**<br>Örnek:

         ```
        "ConnectedSubnets":["10.2.0.0/16","10.30.0.0/16"]
         ```
    * vpngateway sanal hub'ının **IP adresleri**. Vpngateway 'in her bağlantısı etkin-etkin yapılandırmada iki tünelde bulunduğundan, bu dosyada her iki IP adresinin de listelendiğini görürsünüz. Bu örnekte her site için "Instance0" ve "Instance1" örneklerini göreceksiniz.<br>Örnek:

        ``` 
        "Instance0":"104.45.18.186"
        "Instance1":"104.45.13.195"
        ```
    * BGP, önceden paylaşılan anahtar vb. gibi **Vpngateway bağlantısı yapılandırma ayrıntıları** . PSK, sizin için otomatik olarak oluşturulan önceden paylaşılmış anahtardır. Dilediğiniz zaman genel bakış sayfasındaki bağlantıyı düzenleyerek özel bir PSK ekleyebilirsiniz.
  
### <a name="example-device-configuration-file"></a>Örnek cihaz yapılandırma dosyası

  ```
  { 
      "configurationVersion":{ 
         "LastUpdatedTime":"2018-07-03T18:29:49.8405161Z",
         "Version":"r403583d-9c82-4cb8-8570-1cbbcd9983b5"
      },
      "vpnSiteConfiguration":{ 
         "Name":"testsite1",
         "IPAddress":"73.239.3.208"
      },
      "vpnSiteConnections":[ 
         { 
            "hubConfiguration":{ 
               "AddressSpace":"10.1.0.0/24",
               "Region":"West Europe",
               "ConnectedSubnets":[ 
                  "10.2.0.0/16",
                  "10.30.0.0/16"
               ]
            },
            "gatewayConfiguration":{ 
               "IpAddresses":{ 
                  "Instance0":"104.45.18.186",
                  "Instance1":"104.45.13.195"
               }
            },
            "connectionConfiguration":{ 
               "IsBgpEnabled":false,
               "PSK":"bkOWe5dPPqkx0DfFE3tyuP7y3oYqAEbI",
               "IPsecParameters":{ 
                  "SADataSizeInKilobytes":102400000,
                  "SALifeTimeInSeconds":3600
               }
            }
         }
      ]
   },
   { 
      "configurationVersion":{ 
         "LastUpdatedTime":"2018-07-03T18:29:49.8405161Z",
         "Version":"1f33f891-e1ab-42b8-8d8c-c024d337bcac"
      },
      "vpnSiteConfiguration":{ 
         "Name":" testsite2",
         "IPAddress":"66.193.205.122"
      },
      "vpnSiteConnections":[ 
         { 
            "hubConfiguration":{ 
               "AddressSpace":"10.1.0.0/24",
               "Region":"West Europe"
            },
            "gatewayConfiguration":{ 
               "IpAddresses":{ 
                  "Instance0":"104.45.18.187",
                  "Instance1":"104.45.13.195"
               }
            },
            "connectionConfiguration":{ 
               "IsBgpEnabled":false,
               "PSK":"XzODPyAYQqFs4ai9WzrJour0qLzeg7Qg",
               "IPsecParameters":{ 
                  "SADataSizeInKilobytes":102400000,
                  "SALifeTimeInSeconds":3600
               }
            }
         }
      ]
   },
   { 
      "configurationVersion":{ 
         "LastUpdatedTime":"2018-07-03T18:29:49.8405161Z",
         "Version":"cd1e4a23-96bd-43a9-93b5-b51c2a945c7"
      },
      "vpnSiteConfiguration":{ 
         "Name":" testsite3",
         "IPAddress":"182.71.123.228"
      },
      "vpnSiteConnections":[ 
         { 
            "hubConfiguration":{ 
               "AddressSpace":"10.1.0.0/24",
               "Region":"West Europe"
            },
            "gatewayConfiguration":{ 
               "IpAddresses":{ 
                  "Instance0":"104.45.18.187",
                  "Instance1":"104.45.13.195"
               }
            },
            "connectionConfiguration":{ 
               "IsBgpEnabled":false,
               "PSK":"YLkSdSYd4wjjEThR3aIxaXaqNdxUwSo9",
               "IPsecParameters":{ 
                  "SADataSizeInKilobytes":102400000,
                  "SALifeTimeInSeconds":3600
               }
            }
         }
      ]
   }
  ```

### <a name="configuring-your-vpn-device"></a>VPN cihazınızı yapılandırma

>[!NOTE]
> Bir Sanal WAN iş ortağı çözümü ile çalışıyorsanız VPN cihazı yapılandırması otomatik olarak gerçekleşir. Cihaz denetleyicisi, Azure'dan yapılandırma dosyasını alarak Azure bağlantısını kurmak için cihaza uygular. Bu da VPN cihazını el ile yapılandırmayı bilmenize gerek olmadığı anlamına gelir.
>

Cihazınızı yapılandırma yönergelerine ihtiyaç duyarsanız [VPN cihazı yapılandırma betikleri sayfasındaki](~/articles/vpn-gateway/vpn-gateway-about-vpn-devices.md#configscripts) yönergeleri aşağıdaki uyarılarla birlikte kullanabilirsiniz:

* VPN cihazları sayfasındaki yönergeler Sanal WAN için yazılmamıştır ancak yapılandırma dosyasındaki Sanal WAN değerlerini kullanarak VPN cihazınızı el ile yapılandırabilirsiniz. 
* Farklı yapılandırmaya sahip olduğundan VPN Gateway'e özgü indirilebilir cihaz yapılandırma betikleri Sanal WAN ile çalışmaz.
* Yeni bir sanal WAN, hem IKEv1 hem de IKEv2 destekleyebilir.
* Sanal WAN yalnızca rota temelli cihazları ve cihaz yönergelerini kullanabilir.

## <a name="viewwan"></a>Sanal WAN 'nizi görüntüleme

1. Sanal WAN'a gidin.
2. **Genel bakış** sayfasında, haritadaki her bir nokta bir hub 'ı temsil eder. Hub sistem durumu özetini, bağlantı durumunu ve gelen ve giden baytları görüntülemek için herhangi bir noktanın üzerine gelin.
3. Hub 'Lar ve bağlantılar bölümünde hub durumu, VPN siteleri vb. görüntüleyebilirsiniz. Daha fazla ayrıntı için belirli bir hub adına tıklayıp VPN sitesine gidebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Sanal WAN hakkında daha fazla bilgi için [Sanal WAN'a Genel Bakış](virtual-wan-about.md) sayfasına bakın.