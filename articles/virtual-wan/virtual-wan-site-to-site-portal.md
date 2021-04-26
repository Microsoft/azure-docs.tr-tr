---
title: 'Öğretici: siteden siteye bağlantılar oluşturmak için Azure sanal WAN kullanma'
description: Bu öğreticide Azure Sanal WAN kullanarak Azure'a siteden siteye bağlantı oluşturmayı öğreneceksiniz.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 03/05/2021
ms.author: cherylmc
ms.openlocfilehash: 365952beca96b91d312eab209c5332fca2d4842b
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106061885"
---
# <a name="tutorial-create-a-site-to-site-connection-using-azure-virtual-wan"></a>Öğretici: Azure Sanal WAN kullanarak siteden siteye bağlantı oluşturma

Bu öğreticide Sanal WAN kullanarak Azure'daki kaynaklarınıza bir IPsec/IKE (IKEv1 ve IKEv2) VPN bağlantısı üzerinden bağlanmayı öğreneceksiniz. Bu bağlantı türü için, şirket içinde yer alan ve kendisine atanmış dışarıya yönelik bir genel IP adresi atanmış olan bir VPN cihazı gerekir. Sanal WAN hakkında daha fazla bilgi için bkz. [Sanal WAN'a Genel Bakış](virtual-wan-about.md).

Bu öğreticide şunların nasıl yapıldığını öğrenirsiniz:

> [!div class="checklist"]
> * Sanal WAN oluşturma
> * Hub oluşturma
> * Site oluşturma
> * Bir siteyi hub 'a bağlama
> * VPN sitesini bir hub 'a bağlama
> * Bir sanal ağı bir hub'a bağlama
> * Yapılandırma dosyasını indir
> * VPN ağ geçidinizi yapılandırma

> [!NOTE]
> Birden fazla siteniz varsa bu yapılandırmayı oluşturmak için [Sanal WAN iş ortağı](https://aka.ms/virtualwan) kullanmanız gerekir. Ancak ağ bağlantıları ve kendi VPN cihazınızı yapılandırma konularında deneyimliyseniz bu yapılandırmayı kendiniz oluşturabilirsiniz.
>

![Sanal WAN diyagramı](./media/virtual-wan-about/virtualwan.png)

## <a name="prerequisites"></a>Önkoşullar

Yapılandırmanıza başlamadan önce aşağıdaki ölçütleri karşıladığınızı doğrulayın:

[!INCLUDE [Before you begin](../../includes/virtual-wan-before-include.md)]

## <a name="create-a-virtual-wan"></a><a name="openvwan"></a>Sanal WAN oluşturma

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-create-vwan-include.md)]

## <a name="create-a-hub"></a><a name="hub"></a>Hub oluşturma

Hub, siteden siteye, ExpressRoute veya Noktadan siteye işlevselliği için ağ geçitleri içerebilen bir sanal ağ. Hub oluşturulduktan sonra site eklemeseniz bile hub için ücretlendirilirsiniz. Sanal hub 'da siteden siteye VPN ağ geçidinin oluşturulması 30 dakika sürer.

[!INCLUDE [Create a hub](../../includes/virtual-wan-tutorial-s2s-hub-include.md)]

## <a name="create-a-site"></a><a name="site"></a>Site oluşturma

Bu bölümde, sitesi oluşturursunuz. Siteler fiziksel konumlarınıza karşılık gelir. İhtiyaç duyduğunuz kadar çok site oluşturun. Örneğin İstanbul'da, Ankara'da ve İzmir'de birer şubeniz varsa üç ayrı site oluşturmanız gerekir. Bu siteler şirket içi VPN cihazı uç noktalarını içerir. Sanal bir WAN 'da, sanal hub başına en fazla 1000 site oluşturabilirsiniz. Birden çok hub olsaydıysanız, bu hub 'lardan her biri için 1000 oluşturabilirsiniz. Sanal WAN iş ortağı CPE cihazınız varsa, Azure 'a Otomasyon hakkında bilgi edinmek için bunlarla görüşün. Genellikle otomasyon, büyük ölçekli şube bilgilerini Azure 'a aktarmak ve CPE 'dan Azure sanal WAN VPN Gateway 'e bağlantı kurmak için basit bir tıklama deneyimi sağlar. Daha fazla bilgi için bkz. [Azure 'DAN CPE iş ortaklarına Otomasyon Kılavuzu](virtual-wan-configure-automation-providers.md).

[!INCLUDE [Create a site](../../includes/virtual-wan-tutorial-s2s-site-include.md)]

## <a name="connect-the-vpn-site-to-the-hub"></a><a name="connectsites"></a>VPN sitesini hub 'a bağlama

Bu adımda, VPN sitenizi hub 'a bağlayaöğreneceksiniz.

[!INCLUDE [Connect VPN sites](../../includes/virtual-wan-tutorial-s2s-connect-vpn-site-include.md)]

## <a name="connect-the-vnet-to-the-hub"></a><a name="vnet"></a>VNet 'i hub 'a bağlama

[!INCLUDE [Connect](../../includes/virtual-wan-connect-vnet-hub-include.md)]

## <a name="download-vpn-configuration"></a><a name="device"></a>VPN yapılandırmasını indirme

Şirket içi VPN cihazınızı yapılandırmak için VPN cihazı yapılandırmasını kullanın.

1. Sanal WAN'ınızın sayfasında **Genel bakış**'a tıklayın.
2. **Hub->VPNSite** sayfasının en üstünde **VPN yapılandırması 'nı indir**' e tıklayın. Azure, ' Microsoft-Network-[location] ' kaynak grubunda, konumun WAN konumunun bulunduğu bir depolama hesabı oluşturur. Yapılandırmayı VPN cihazlarınıza uyguladıktan sonra bu depolama hesabını silebilirsiniz.
3. Dosya oluşturulduktan sonra bağlantıya tıklayarak indirebilirsiniz.
4. Yapılandırmayı şirket içi VPN cihazınıza uygulayın.

### <a name="about-the-vpn-device-configuration-file"></a>VPN cihazı yapılandırma dosyası hakkında

Cihaz yapılandırma dosyasında şirket içi VPN cihazınızı yapılandırırken kullanacağınız ayarlar bulunur. Bu dosyayı görüntülediğinizde aşağıdaki bilgilere dikkat edin:

* **vpnSiteConfiguration -** Bu bölümde sanal WAN'a bağlanan bir site olarak ayarlanmış cihazın ayrıntıları yer alır. Dal cihazının adını ve genel IP adresini içerir.
* **Vpnsiteconnections-** Bu bölüm aşağıdaki ayarlarla ilgili bilgiler sağlar:

    * Sanal hub 'lar VNet 'in **Adres alanı** .<br>Örnek:
 
        ```
        "AddressSpace":"10.1.0.0/24"
        ```
    * Hub 'a bağlı sanal ağların **Adres alanı** .<br>Örnek:

         ```
        "ConnectedSubnets":["10.2.0.0/16","10.3.0.0/16"]
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
                  "10.3.0.0/16"
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
* Sanal WAN, ilke tabanlı ve rota tabanlı VPN cihazlarını ve cihaz talimatlarını kullanabilir.

## <a name="configure-your-vpn-gateway"></a><a name="gateway-config"></a>VPN ağ geçidinizi yapılandırma

VPN Gateway ayarlarınızı **görüntüleme/yapılandırma**' yı seçerek istediğiniz zaman görüntüleyebilir ve yapılandırabilirsiniz.

:::image type="content" source="media/virtual-wan-site-to-site-portal/view-configuration-1.png" alt-text="' Görünüm/yapılandırma ' eylemine işaret eden bir oka sahip ' VPN (siteden siteye) ' sayfasını gösteren ekran görüntüsü." lightbox="media/virtual-wan-site-to-site-portal/view-configuration-1-expand.png":::

**VPN Gateway Düzenle** sayfasında, aşağıdaki ayarları görebilirsiniz:

* VPN Gateway genel IP adresi (Azure tarafından atanan)
* VPN Gateway özel IP adresi (Azure tarafından atanan)
* VPN Gateway varsayılan BGP IP adresi (Azure tarafından atanan)
* Özel BGP IP adresi için yapılandırma seçeneği: Bu alan, APIPA (otomatik özel IP adresleme) için ayrılmıştır. Azure, 169.254.21. * ve 169.254.22. * aralıklarında BGP IP 'sini destekler. Azure, bu aralıklardaki BGP bağlantılarını kabul eder, ancak varsayılan BGP IP 'si ile bağlantıyı çevirebilir.

   :::image type="content" source="media/virtual-wan-site-to-site-portal/view-configuration-2.png" alt-text="Yapılandırmayı görüntüle" lightbox="media/virtual-wan-site-to-site-portal/view-configuration-2-expand.png":::

## <a name="clean-up-resources"></a><a name="cleanup"></a>Kaynakları temizleme

Oluşturduğunuz kaynaklara artık ihtiyacınız kalmadığında, bunları silin. Bağımlılıklar nedeniyle sanal WAN kaynaklarından bazılarının belirli bir sırada silinmesi gerekir. Silme işleminin tamamlanması yaklaşık 30 dakika sürebilir.

[!INCLUDE [Delete resources](../../includes/virtual-wan-resource-cleanup.md)]

## <a name="next-steps"></a>Sonraki adımlar

Sonra, sanal WAN hakkında daha fazla bilgi edinmek için bkz.:

> [!div class="nextstepaction"]
> * [Sanal WAN hakkında SSS](virtual-wan-faq.md)
