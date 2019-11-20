---
title: Azure sanal WAN iş ortakları | Microsoft Docs
description: Bu makale, iş ortaklarının Azure sanal WAN Otomasyonu 'nu ayarlama konusunda yardımcı olur.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 10/07/2019
ms.author: cherylmc
ms.openlocfilehash: 2f847d8db983303d46b465f4f80bff65eeff632f
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72168488"
---
# <a name="virtual-wan-partners"></a>Sanal WAN iş ortakları

Bu makale, Azure sanal WAN için bir şube cihazını (bir müşteri şirket içi VPN cihazı veya SDWAN CPE) bağlamak ve yapılandırmak üzere otomasyon ortamının nasıl ayarlanacağını anlamanıza yardımcı olur. IPSec/Ikev2 veya IPSec/IKEv1 üzerinden VPN bağlantısı barındırabilecek dal cihazları sağlayan bir sağlayıcısıysanız, bu makale sizin için sizin içindir.

Bir dal aygıtı (bir müşteri şirket içi VPN cihazı veya SDWAN CPE) genellikle sağlanması için bir denetleyici/cihaz panosu kullanır. SD-WAN çözümü yöneticileri, bir cihazı ağa takılmadan önce önceden sağlamak için genellikle bir yönetim konsolu kullanabilirler. Bu VPN özellikli cihaz, bir denetleyiciden denetim düzlemi mantığını alır. VPN cihazı veya SD-WAN denetleyicisi, Azure sanal WAN bağlantısını otomatik hale getirmek için Azure API 'Leri kullanabilir. Bu tür bir bağlantı, şirket içi cihazın kendisine atanmış bir genel IP adresi olmasını gerektirir.

## <a name ="before"></a>Otomatikleştirmeye başlamadan önce

* Cihazınızın IPSec IKEv1/Ikev2 desteklediğini doğrulayın. Bkz. [varsayılan ilkeler](#default).
* Azure sanal WAN bağlantısını otomatikleştirmek için kullandığınız [REST API 'leri](#additional) görüntüleyin.
* Azure sanal WAN 'ın Portal deneyimini test edin.
* Sonra, bağlantı adımlarının hangi bölümünü otomatikleştirmek istediğinizi belirleyin. En azından, otomatikleştirilmesi önerilir:

  * Access Control
  * Şube cihaz bilgilerini Azure sanal WAN 'a yükleme
  * Azure yapılandırmasını indirme ve şube cihazından Azure sanal WAN 'a bağlantı ayarlama

### <a name ="additional"></a>Ek bilgi

* Sanal hub oluşturmayı otomatikleştirmek için [REST API](https://docs.microsoft.com/rest/api/virtualwan/virtualhubs)
* Sanal WAN için Azure VPN ağ geçidini otomatik hale getirmek için [REST API](https://docs.microsoft.com/rest/api/virtualwan/vpngateways)
* Bir VPNSite 'yi bir Azure VPN hub 'ına bağlamak için [REST API](https://docs.microsoft.com/rest/api/virtualwan/vpnconnections)
* [Varsayılan IPSec ilkeleri](#default)

## <a name ="ae"></a>Müşteri deneyimi

Beklenen müşteri deneyimini Azure sanal WAN ile birlikte anlayın.

  1. Genellikle, bir sanal WAN kullanıcısı bir sanal WAN kaynağı oluşturarak bu işlemi başlatır.
  2. Kullanıcı, şube bilgilerini Azure sanal WAN 'a yazmak için şirket içi sistem (Şube denetleyiciniz veya VPN cihaz sağlama yazılımınız) için bir hizmet sorumlusu tabanlı kaynak grubu erişimi ayarlar.
  3. Kullanıcı, şu anda Kullanıcı ARABIRIMINDE oturum açıp hizmet sorumlusu kimlik bilgilerini ayarlama kararı verebilir. Bu işlem tamamlandıktan sonra denetleyicinizin, sağlayabileceğiniz Otomasyon ile dal bilgilerini karşıya yükleyebilmeleri gerekir. Azure tarafında bu değerin el ile eşdeğeri ' site oluştur ' ' tur.
  4. Site (Şube aygıtı) bilgileri Azure 'da kullanılabilir olduğunda, kullanıcı siteyi bir hub 'a bağlayacaktır. Bir sanal hub, Microsoft tarafından yönetilen bir sanal ağ. Hub, şirket içi ağınızdan (vpnsite) bağlantıyı etkinleştirmek için çeşitli hizmet uç noktaları içerir. Hub, ağınızın bir bölgedeki çekirdekdir. Bu işlem sırasında, Azure bölgesi başına yalnızca bir hub olabilir ve içindeki VPN uç noktası (vpngateway) oluşturulur. VPN Gateway, bant genişliği ve bağlantı ihtiyaçlarına göre uygun şekilde boyutlardaki ölçeklenebilir bir ağ geçidindir. Sanal hub ve vpngateway oluşturma 'yı dal cihaz denetleyicisi panoınızdan otomatik hale getirmeyi seçebilirsiniz.
  5. Sanal hub siteyle ilişkilendirildiğinde, kullanıcının el ile indirilmesi için bir yapılandırma dosyası oluşturulur. Bu, otomasyonunun içinde geldiği ve kullanıcının sorunsuz bir şekilde karşılaşmasına neden olur. Şube cihazını el ile indirip yapılandırmak zorunda kalmadan, Otomasyonu ayarlayabilir ve Kullanıcı ARABIRIMINIZDEKI en az tıklama deneyimi sağlayabiliyor, böylece paylaşılan anahtar uyumsuzluğu, IPSec parametresi gibi tipik bağlantı sorunları hafifletmesini uyumsuzluk, yapılandırma dosyası okunabilirlik vb.
  6. Çözümünüzde bu adımın sonunda, kullanıcının şube cihazı ile sanal hub arasında sorunsuz bir siteden siteye bağlantısı olacaktır. Ayrıca, diğer hub 'larda ek bağlantılar da ayarlayabilirsiniz. Her bağlantı etkin-etkin bir tüneldir. Müşteriniz, tünele ilgili bağlantıların her biri için farklı bir ISS kullanmayı tercih edebilir.
  7. CPE yönetim arabiriminde sorun giderme ve izleme özellikleri sağlamayı düşünün. Tipik senaryolar, "bir CPE sorunu nedeniyle Azure kaynaklarına erişemeyebilirsiniz", "CPE tarafında IPSec parametrelerini göster" vb. arasında yer alır.

## <a name ="understand"></a>Otomasyon ayrıntıları

###  <a name="access"></a>Erişim denetimi

Müşteriler, cihaz Kullanıcı arabirimindeki sanal WAN için uygun erişim denetimini ayarlayabilmelidir. Bu, bir Azure hizmet sorumlusu kullanılarak önerilir. Hizmet sorumlusu tabanlı erişim, dal bilgilerini karşıya yüklemek için cihaz denetleyicisine uygun kimlik doğrulaması sağlar. Daha fazla bilgi için bkz. [hizmet sorumlusu oluşturma](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application). Bu işlev, Azure sanal WAN teklifi dışında olduğundan, Azure 'da ilgili ayrıntıların cihaz yönetimi panosuna nasıl alınacağından önce Azure 'da erişim ayarlamak için geçen tipik adımların aşağıda listelenmektedir

* Şirket içi cihaz denetleyiciniz için bir Azure Active Directory uygulaması oluşturun.
* Uygulama KIMLIĞINI ve kimlik doğrulama anahtarını al
* Kiracı KIMLIĞINI al
* Uygulamayı "katkıda bulunan" rolüne ata

###  <a name="branch"></a>Şube cihaz bilgilerini karşıya yükle

Şube (Şirket içi site) bilgilerini Azure 'a yüklemek için Kullanıcı deneyimini tasarlamanız gerekir. Sanal WAN 'da site bilgilerini oluşturmak için, VPNSite için [REST API 'lerini](https://docs.microsoft.com/rest/api/virtualwan/vpnsites) kullanabilirsiniz. Tüm dal SDWAN/VPN cihazlarını verebilir veya uygun şekilde cihaz özelleştirmeleri seçebilirsiniz.

### <a name="device"></a>Cihaz yapılandırması indirme ve bağlantı

Bu adım, Azure yapılandırmasını indirmeyi ve şube cihazından Azure sanal WAN 'a bağlantı kurmayı içerir. Bu adımda, sağlayıcı kullanmayan bir müşteri Azure yapılandırmasını el ile indirip şirket içi SDWAN/VPN cihazına uygular. Sağlayıcı olarak, bu adımı otomatikleştirmelisiniz. Ek bilgi için [REST API 'lerini](https://docs.microsoft.com/rest/api/virtualwan/vpnsitesconfiguration/download) indirin ' i görüntüleyin. Cihaz denetleyicisi, Azure yapılandırmasını indirmek için ' GetVpnConfiguration ' REST API çağırabilir.

**Yapılandırma notları**

  * Azure VNET 'ler sanal hub 'a bağlıysa, bu kişiler Connectedalt ağları olarak görünürler.
  * VPN bağlantısı, rota tabanlı yapılandırmayı kullanır ve hem IKEv1 hem de IKEv2 protokollerini destekler.

## <a name="devicefile"></a>Cihaz yapılandırma dosyası

Cihaz yapılandırma dosyası, şirket içi VPN cihazınızı yapılandırırken kullanılacak ayarları içerir. Bu dosyayı görüntülediğinizde, aşağıdaki bilgilere dikkat edin:

* **Vpnsiteconfiguration-** Bu bölüm, sanal WAN 'a bağlanan bir site olarak ayarlanan cihaz ayrıntılarını gösterir. Bu, şube cihazının adını ve genel IP adresini içerir.
* **Vpnsiteconnections-** Bu bölüm aşağıdakiler hakkında bilgi sağlar:

    * Sanal hub 'lar VNet 'in **Adres alanı** .<br>Örnek:
 
        ```
        "AddressSpace":"10.1.0.0/24"
        ```
    * Hub 'a bağlı sanal ağların **Adres alanı** .<br>Örnek:

         ```
        "ConnectedSubnets":["10.2.0.0/16","10.30.0.0/16"]
         ```
    * Sanal hub vpngateway 'in **IP adresleri** . Vpngateway 'in, etkin-etkin yapılandırmada 2 tünelden oluşan her bağlantısı olduğundan, bu dosyada her iki IP adresini de listelenmiş olarak görürsünüz. Bu örnekte, her site için "Örnek0" ve "Instance1" görüntülenir.<br>Örnek:

        ``` 
        "Instance0":"104.45.18.186"
        "Instance1":"104.45.13.195"
        ```
    * BGP, önceden paylaşılan anahtar vb. gibi **Vpngateway bağlantısı yapılandırma ayrıntıları** . PSK, sizin için otomatik olarak oluşturulan önceden paylaşılmış anahtardır. Bağlantıyı, özel bir PSK için genel bakış sayfasında her zaman düzenleyebilirsiniz.
  
**Örnek cihaz yapılandırma dosyası**

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

## <a name="default"></a>Bağlantı ayrıntıları

Şirket içi SDWAN/VPN cihazınız veya SD-WAN yapılandırmanızın Azure IPSec/ıKE ilkesinde belirttiğiniz aşağıdaki algoritmaların ve parametrelerin eşleşmesi veya içermesi gerekir.

* IKE şifreleme algoritması
* IKE bütünlük algoritması
* DH grubu
* IPSec şifreleme algoritması
* IPSec bütünlük algoritması
* PFS Grubu

### <a name="default"></a>IPSec bağlantısı için varsayılan ilkeler

[!INCLUDE [IPsec Default](../../includes/virtual-wan-ipsec-include.md)]

### <a name="custom"></a>IPSec bağlantısı için özel ilkeler

[!INCLUDE [IPsec Custom](../../includes/virtual-wan-ipsec-custom-include.md)]

## <a name="next-steps"></a>Sonraki adımlar

Sanal WAN hakkında daha fazla bilgi için bkz. [Azure sanal WAN](virtual-wan-about.md) ve [Azure sanal WAN hakkında SSS](virtual-wan-faq.md).

Ek bilgi için lütfen <azurevirtualwan@microsoft.com> adresine bir e-posta gönderin. Şirket adınızı konu satırına "[]" içine ekleyin.