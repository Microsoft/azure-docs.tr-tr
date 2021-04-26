---
title: Yalnızca giden yük dengeleyici yapılandırması
titleSuffix: Azure Load Balancer
description: Bu makalede, giden NAT ile iç yük dengeleyici oluşturma hakkında bilgi edinin
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/07/2020
ms.author: allensu
ms.openlocfilehash: a96e6b567c04f2e0561a0a754e854a0e2b1ad6b0
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106076267"
---
# <a name="outbound-only-load-balancer-configuration"></a>Yalnızca giden yük dengeleyici yapılandırması

İç ve dış standart yük dengeleyiciler bileşimini kullanarak iç yük dengeleyicinin arkasındaki VM 'Ler için giden bağlantı oluşturun. 

Bu yapılandırma, iç yük dengeleyici senaryosu için giden NAT sağlar ve arka uç havuzunuzun "yalnızca çıkış" kurulumunu üretir.


<p align="center">
  <img src="./media/egress-only/load-balancer-egress-only.png" alt="Figure depicts a egress only load balancer configuration." width="500" title="Sanal ağ NAT">
</p>


<!-- 
:::image type="content" source="./media/egress-only/load-balancer-egress-only.png" alt-text="Figure depicts a egress only load balancer configuration" border="true":::
-->


*Şekil: yalnızca çıkış yük dengeleyici yapılandırması*

Gereken adımlar şunlardır:

1. Bir savunma ana bilgisayarıyla bir sanal ağ oluşturun.
2. Yalnızca özel bir IP ile sanal makine oluşturun.
3. Hem iç hem de genel standart yük dengeleyiciler oluşturun.
4. Her iki yük dengeleyiciler için arka uç havuzları ekleyin ve VM 'yi her havuza yerleştirin.
5. Savunma ana bilgisayarı ile sanal makinenize bağlanın ve:
    1. Giden bağlantıyı test etme, 
    2. Ortak yük dengeleyicide giden bir kural yapılandırın.
    3. Giden bağlantıyı yeniden test edin.

## <a name="create-virtual-network-and-virtual-machine"></a>Sanal ağ ve sanal makine oluştur

İki alt ağa sahip bir sanal ağ oluşturun:

* Birincil alt ağ
* Savunma alt ağı

Yeni Sanal ağda bir sanal makine oluşturun.

### <a name="create-the-virtual-network"></a>Sanal ağı oluşturma

1. Azure portalında [oturum açın](https://portal.azure.com).

2. Ekranın sol üst kısmındaki **Kaynak oluştur > Ağ > Sanal ağ** seçeneğini belirleyin veya arama kutusuna **Sanal ağ** yazarak arama yapın.

2. **Sanal ağ oluştur**' da, **temel** bilgiler sekmesinde bu bilgileri girin veya seçin:

    | **Ayar**          | **Değer**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Proje ayrıntıları**  |                                                                 |
    | Abonelik     | Azure aboneliğinizi seçin                                  |
    | Kaynak Grubu   | **Yeni oluştur**’u seçin. </br> **Myresourcegrouplb** girin. </br> **Tamam**’ı seçin. |
    | **Örnek ayrıntıları** |                                                                 |
    | Name             | **Myvnet** girin                                    |
    | Region           | **Doğu ABD 2** seçin |

3. **IP adresleri** sekmesini seçin veya sayfanın altındaki **Sonraki: IP adresleri** düğmesini seçin.

4. **IP adresleri** sekmesinde şu bilgileri girin:

    | Ayar            | Değer                      |
    |--------------------|----------------------------|
    | IPv4 adres alanı | **10.1.0.0/16** girin |

5. **Alt ağ adı** altında, **varsayılan** sözcük ' ı seçin.

6. **Alt ağı Düzenle**' de şu bilgileri girin:

    | Ayar            | Değer                      |
    |--------------------|----------------------------|
    | Alt ağ adı | **Mybackendsubnet** girin |
    | Alt ağ adres aralığı | **10.1.0.0/24** girin |

7. **Kaydet**’i seçin.

8. **Güvenlik** sekmesini seçin.

9. **Bastionhost** altında **Etkinleştir**' i seçin. Bu bilgileri girin:

    | Ayar            | Değer                      |
    |--------------------|----------------------------|
    | Savunma adı | **Mybastionhost** girin |
    | AzureBastionSubnet adres alanı | **10.1.1.0/24** girin |
    | Genel IP Adresi | **Yeni oluştur**’u seçin. </br> **Ad** Için **Mybastionıp** girin. </br> **Tamam**’ı seçin. |


8. **Gözden geçir + oluştur** sekmesini seçin ya da **gözden geçir + oluştur** düğmesini seçin.

9. **Oluştur**’u seçin.

### <a name="create-a-virtual-machine"></a>Sanal makine oluşturma

1. Portalın sol üst kısmında **kaynak oluştur**  >  **işlem**  >  **sanal makinesi**' ni seçin. 
   
2. **Sanal makine oluştur**' da **temel bilgiler** sekmesinde değerleri yazın veya seçin:

    | Ayar | Değer                                          |
    |-----------------------|----------------------------------|
    | **Proje ayrıntıları** |  |
    | Abonelik | Azure aboneliğinizi seçin |
    | Kaynak Grubu | **Myresourcegrouplb** seçin |
    | **Örnek ayrıntıları** |  |
    | Sanal makine adı | **Myvm 'yi** girin |
    | Region | **Doğu ABD 2** seçin |
    | Kullanılabilirlik seçenekleri | **Altyapı yedekliliği gerekli değil** ' i seçin |
    | Görüntü | **Windows Server 2019 Datacenter** seçin |
    | Azure Spot örneği | **Hayır** seçin |
    | Boyut | VM boyutunu seçin veya varsayılan ayarı yapın |
    | **Yönetici hesabı** |  |
    | Kullanıcı adı | Kullanıcı adı girin |
    | Parola | Parola girin |
    | Parolayı onayla | Parolayı yeniden girin |
    | **Gelen bağlantı noktası kuralları** |  |
    | Genel gelen bağlantı noktaları | **Seçili bağlantı noktalarına Izin ver** öğesini seçin |
    | Gelen bağlantı noktalarını seçin | RDP 'yi seçin **(3389)** |

3. **Ağ** sekmesini seçin veya **Sonraki: diskler**' i ve sonra **İleri: ağ**' ı seçin.
  
4. Ağ sekmesinde, şunu seçin veya girin:

    | Ayar | Değer |
    |-----|------------|
    | **Ağ arabirimi** |  |
    | Sanal ağ | **myVNet** |
    | Alt ağ | **myBackendSubnet** |
    | Genel IP | **Hiçbiri** seçeneğini belirtin. |
    | NIC ağ güvenlik grubu | **Hiçbirini** seçme|
    | Bu sanal makine, var olan bir yük dengeleme çözümünün arkasına mi yerleştirsin? | **Hayır** seçin |
   
5. **Yönetim** sekmesini seçin veya **İleri**  >  **Yönetim**' i seçin.

6. **Yönetim** sekmesinde, şunu seçin veya girin:
    
    | Ayar | Değer |
    |-|-|
    | **İzleme** |  |
    | Önyükleme tanılamaları | Seçme **kapalı** |
   
7. **Gözden geçir ve oluştur**’u seçin. 
  
8. Ayarları gözden geçirin ve ardından **Oluştur**' u seçin.

## <a name="create-load-balancers-and-test-connectivity"></a>Yük dengeleyiciler ve test bağlantısı oluşturma

Oluşturmak için Azure portal kullanın:

* İç yük dengeleyici
* Genel yük dengeleyici
 
Oluşturulan VM 'nizi her birinin arka uç havuzuna ekleyin.  Daha sonra yalnızca sanal makinenizin giden bağlantısına izin vermek için bir yapılandırma ayarlayacaksınız, önce ve sonra test ediliyor.

### <a name="create-internal-load-balancer"></a>İç yük dengeleyici oluştur

1. Ekranın sol üst kısmında, **kaynak oluştur**  >  **ağ**  >  **Load Balancer**' ı seçin.

2. **Yük dengeleyici oluştur** sayfasının **temel bilgiler** sekmesinde, aşağıdaki bilgileri girin veya seçin: 

    | Ayar                 | Değer                                              |
    | ---                     | ---                                                |
    | Abonelik               | Aboneliğinizi seçin.    |    
    | Kaynak grubu         | Önceki adımda oluşturulan **Myresourcegrouplb** öğesini seçin.|
    | Name                   | **Myınternalloadbalancer** girin                                   |
    | Region         | **Doğu ABD 2**’yi seçin.                                        |
    | Tür          | **Dahili**' ı seçin.                                        |
    | SKU           | **Standart** seçin |
    | Sanal ağ | Önceki adımda oluşturulan **Myvnet** öğesini seçin. |
    | Alt ağ  | Önceki adımda oluşturulan **Mybackendsubnet** öğesini seçin. |
    | IP adresi ataması | **Dinamik**' i seçin. |

3. Geri kalan ayarlar için varsayılan değerleri kabul edin ve ardından **gözden geçir + oluştur**' u seçin.

4. **Gözden geçir + oluştur** sekmesinde **Oluştur**' u seçin.   

### <a name="create-public-load-balancer"></a>Ortak yük dengeleyici oluştur

1. Ekranın sol üst kısmında, **kaynak oluştur**  >  **ağ**  >  **Load Balancer**' ı seçin.

2. **Yük dengeleyici oluştur** sayfasının **temel bilgiler** sekmesinde, aşağıdaki bilgileri girin veya seçin: 

    | Ayar                 | Değer                                              |
    | ---                     | ---                                                |
    | Abonelik               | Aboneliğinizi seçin.    |    
    | Kaynak grubu         | **Yeni oluştur** ' u seçin ve metin kutusuna **Myresourcegrouplb** yazın.|
    | Name                   | **Mypublicloadbalancer** girin                                   |
    | Region         | **Doğu ABD 2**’yi seçin.                                        |
    | Tür          | **Genel**’i seçin.                                        |
    | SKU           | **Standart** seçin |
    | Genel IP adresi | **Yeni oluştur**’u seçin. |
    | Genel IP adresi adı | Metin kutusuna **Myfrontendıp** girin.|
    | Kullanılabilirlik alanı | **Bölge yedekli** seçeneğini belirleyin |
    | Genel IPv6 adresi ekle | **Hayır**'ı seçin. |

3. Geri kalan ayarlar için varsayılan değerleri kabul edin ve ardından **gözden geçir + oluştur**' u seçin.

4. **Gözden geçir + oluştur** sekmesinde **Oluştur**' u seçin.   
   
### <a name="create-internal-backend-address-pool"></a>İç arka uç adres havuzu oluştur

**Myınternalbackendpool** arka uç adres havuzunu oluşturun:

1. Sol taraftaki menüden **tüm hizmetler** ' i seçin, **tüm kaynaklar**' ı seçin ve ardından kaynaklar listesinden **myınternalloadbalancer** ' yi seçin.

2. **Ayarlar** altında **arka uç havuzları**' nı seçin ve ardından **Ekle**' yi seçin.

3. **Arka uç Havuzu Ekle** sayfasında, ad için, arka uç havuzunuzun adı olarak **Myınternalbackendpool** yazın.
 
4. **Sanal ağ**' da, **myvnet**' i seçin.

5. **Sanal makineler** altında **Ekle** ' yi seçin ve **myvm**' yi seçin.
 
6. **Ekle**' yi seçin.

### <a name="create-public-backend-address-pool"></a>Genel arka uç adres havuzu oluştur

**Mypublicbackendpool** arka uç adres havuzunu oluşturun:

1. Sol taraftaki menüden **tüm hizmetler** ' i seçin, **tüm kaynaklar**' ı seçin ve ardından kaynaklar listesinden **mypublicloadbalancer** ' ı seçin.

2. **Ayarlar** altında **arka uç havuzları**' nı seçin ve ardından **Ekle**' yi seçin.

3. Arka uç havuzu **Ekle** sayfasında, ad için, arka uç havuzunuzun adı olarak **Mypublicbackendpool** yazın.

4. **Sanal ağ**' da, **myvnet**' i seçin.
 
5. **Sanal makineler** altında **Ekle** ' yi seçin ve **myvm**' yi seçin.
 
6. **Ekle**' yi seçin.

### <a name="test-connectivity-before-outbound-rule"></a>Giden kuraldan önce bağlantıyı sına

1. Sol taraftaki menüden **tüm hizmetler** ' i seçin, **tüm kaynaklar**' ı seçin ve ardından kaynaklar listesinden **myresourcegrouplb** kaynak grubunda bulunan **myvm** ' yi seçin.

2. **Genel bakış** sayfasında **Bağlan** **' ı ve** sonra da ' yi seçin.

4. VM oluşturma sırasında girilen kullanıcı adını ve parolayı girin.

5. **Bağlan**’ı seçin.

6. Internet Explorer'ı açın.

7. **https://whatsmyip.org** Adres çubuğuna yazın.

8. Bağlantı başarısız olmalıdır. Varsayılan olarak, standart genel yük dengeleyici [tanımlı bir giden kuralı olmadan giden trafiğe izin](load-balancer-overview.md#securebydefault)vermez.
 
### <a name="create-a-public-load-balancer-outbound-rule"></a>Ortak yük dengeleyici giden kuralı oluşturma

1. Sol taraftaki menüden **tüm hizmetler** ' i seçin, **tüm kaynaklar**' ı seçin ve ardından kaynaklar listesinden **mypublicloadbalancer** ' ı seçin.

2. **Ayarlar** altında **giden kuralları**' nı ve ardından **Ekle**' yi seçin.

3. Giden kuralları yapılandırmak için şu değerleri kullanın:

    | Ayar | Değer |
    | ------- | ----- |
    | Ad | **Mbir Boundrule** girin. |
    | Ön uç IP adresi | **Loadbalancerön uç** öğesini seçin.|
    | Boşta kalma zaman aşımı (dakika) | Kaydırıcıyı **15 dakikaya** taşıyın.|
    | TCP sıfırlaması | **Etkin**'i seçin.|
    | Arka uç havuzu | **Mypublicbackendpool** öğesini seçin.|
    | Bağlantı noktası ayırma-> bağlantı noktası ayırma | **Varsayılan giden bağlantı noktası sayısını kullan** ' ı seçin |

4. **Add (Ekle)** seçeneğini belirleyin.

### <a name="test-connectivity-after-outbound-rule"></a>Giden kuraldan sonra Bağlantıyı Sına

1. Sol taraftaki menüden **tüm hizmetler** ' i seçin, **tüm kaynaklar**' ı seçin ve ardından kaynaklar listesinden **myresourcegrouplb** kaynak grubunda bulunan **myvm** ' yi seçin.

2. **Genel bakış** sayfasında **Bağlan** **' ı ve** sonra da ' yi seçin.

4. VM oluşturma sırasında girilen kullanıcı adını ve parolayı girin.

5. **Bağlan**’ı seçin.

6. Internet Explorer'ı açın.

7. **https://whatsmyip.org** Adres çubuğuna yazın.

8. Bağlantı başarılı olmalıdır.

9. Görüntülenecek IP adresi **Mypublicloadbalancer** ön uç IP adresi olmalıdır.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli olmadığında kaynak grubunu, yük dengeleyicileri, VM 'yi ve tüm ilgili kaynakları silin. 

Bunu yapmak için **Myresourcegrouplb** kaynak grubunu seçin ve **Sil**' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, ortak ve iç yük dengeleyiciler ile bir "yalnızca çıkış" yapılandırması oluşturdunuz.  

Bu yapılandırma, genel gelen tüm bağlantıları engellemeye devam ederken, arka uç havuzunuza gelen iç trafiğin yükünü dengelemenize olanak tanır.

- [Azure Load Balancer](load-balancer-overview.md)hakkında bilgi edinin.
- [Azure 'da giden bağlantılar](load-balancer-outbound-connections.md)hakkında bilgi edinin.
- Yük dengeleyici [SSS](load-balancer-faqs.md).
- [Azure](../bastion/bastion-overview.md) savunma hakkında bilgi edinin
