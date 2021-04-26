---
title: 'Hızlı başlangıç: iç yük dengeleyici oluşturma-Azure portal'
titleSuffix: Azure Load Balancer
description: Bu hızlı başlangıçta, Azure portal kullanarak iç yük dengeleyicinin nasıl oluşturulacağı gösterilmektedir.
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/30/2020
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: 9f987cd99007f66540fd826a6cd14c6427ea0893
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106056394"
---
# <a name="quickstart-create-an-internal-load-balancer-to-load-balance-vms-using-the-azure-portal"></a>Hızlı başlangıç: Azure portal kullanarak VM 'Lerin yükünü dengelemek için iç yük dengeleyici oluşturma

İç yük dengeleyici ve üç sanal makine oluşturmak için Azure portal kullanarak Azure Load Balancer kullanmaya başlayın.

## <a name="prerequisites"></a>Önkoşullar

- Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-azure"></a>Azure'da oturum açma

[https://portal.azure.com](https://portal.azure.com) adresinden Azure portalında oturum açın.

---

# <a name="standard-sku"></a>[**Standart SKU**](#tab/option-1-create-internal-load-balancer-standard)

>[!NOTE]
>Standart SKU yük dengeleyici, üretim iş yükleri için önerilir.  SKU 'lar hakkında daha fazla bilgi için bkz. **[Azure Load Balancer SKU 'lar](skus.md)**.

Bu bölümde, sanal makinelerin yükünü dengeleyen bir yük dengeleyici oluşturacaksınız. 

Bir iç yük dengeleyici oluşturduğunuzda, yük dengeleyici için ağ olarak bir sanal ağ yapılandırılır. 

Aşağıdaki diyagramda bu hızlı başlangıçta oluşturulan kaynaklar gösterilmektedir:

:::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/resources-diagram-internal.png" alt-text="Hızlı başlangıç için oluşturulan Standart yük dengeleyici kaynakları." border="false":::

Sanal ağdaki özel bir IP adresi, yük dengeleyici için ön uç (varsayılan olarak **Loadbalancerön uç** olarak adlandırılır) olarak yapılandırılır. 

Ön uç IP adresi **statik** veya **dinamik** olabilir.

## <a name="create-the-virtual-network"></a>Sanal ağı oluşturma

Bu bölümde, bir sanal ağ ve alt ağ oluşturacaksınız.

1. Ekranın sol üst kısmındaki **Kaynak oluştur > Ağ > Sanal ağ** seçeneğini belirleyin veya arama kutusuna **Sanal ağ** yazarak arama yapın.

2. **Sanal ağ oluştur**' da, **temel** bilgiler sekmesinde bu bilgileri girin veya seçin:

    | **Ayar**          | **Değer**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Proje ayrıntıları**  |                                                                 |
    | Abonelik     | Azure aboneliğinizi seçin                                  |
    | Kaynak Grubu   | **Createıntlbqs-RG** seçin |
    | **Örnek ayrıntıları** |                                                                 |
    | Name             | **Myvnet** girin                                    |
    | Region           | Seç **(Avrupa) Batı Avrupa** |

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

## <a name="create-load-balancer"></a>Yük dengeleyici oluşturma

1. **Kaynak oluştur**’u seçin. 
2. Arama kutusuna **yük dengeleyici** girin. Arama sonuçlarında **Yük dengeleyiciyi** seçin.
3. **Yük dengeleyici** sayfasında **Oluştur**' u seçin.
4. **Yük dengeleyici oluştur** sayfasında girin veya aşağıdaki bilgileri seçin: 

    | Ayar                 | Değer                                              |
    | ---                     | ---                                                |
    | Abonelik               | Aboneliğinizi seçin.    |    
    | Kaynak grubu         | Önceki adımda oluşturulan **Createıntlbqs-RG** öğesini seçin.|
    | Name                   | **Myloadbalancer** girin                                   |
    | Region         | **(Avrupa) Batı Avrupa** seçin.                                        |
    | Tür          | **Dahili**' ı seçin.                                        |
    | SKU           | **Standart** seçin |
    | Sanal ağ | Önceki adımda oluşturulan **Myvnet** öğesini seçin. |
    | Alt ağ  | Önceki adımda oluşturulan **Mybackendsubnet** öğesini seçin. |
    | IP adresi ataması | **Dinamik**' i seçin. |
    | Kullanılabilirlik alanı | **Bölge yedekli** seçeneğini belirleyin |

3. Geri kalan ayarlar için varsayılan değerleri kabul edin ve ardından **gözden geçir + oluştur**' u seçin.

4. **Gözden geçir + oluştur** sekmesinde **Oluştur**' u seçin.   
    
    :::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/create-standard-internal-load-balancer.png" alt-text="Standart bir iç yük dengeleyici oluşturun." border="true":::
 
## <a name="create-load-balancer-resources"></a>Yük dengeleyici kaynakları oluşturma

Bu bölümde şunları yapılandırırsınız:

* Arka uç adres havuzu için yük dengeleyici ayarları.
* Bir sistem durumu araştırması.
* Yük dengeleyici kuralı.

### <a name="create-a-backend-pool"></a>Arka uç havuzu oluşturma

Arka uç adres havuzu, yük dengeleyiciye bağlı sanal (NIC) IP adreslerini içerir. 

Yük Dengeleme internet trafiği için sanal makineleri dahil etmek üzere **Mybackendpool** arka uç adres havuzunu oluşturun.

1. Sol taraftaki menüden **tüm hizmetler** ' i seçin, **tüm kaynaklar**' ı seçin ve ardından kaynaklar listesinden **myloadbalancer** ' ı seçin.

2. **Ayarlar** altında **arka uç havuzları**' nı seçin ve ardından **Ekle**' yi seçin.

3. **Arka uç Havuzu Ekle** sayfasında, ad için, arka uç havuzunuzun adı olarak **mybackendpool** yazın ve ardından **Ekle**' yi seçin.

### <a name="create-a-health-probe"></a>Durum araştırması oluşturma

Yük dengeleyici, uygulamanızın durumunu bir sistem durumu araştırmasıyla izler. 

Sistem durumu araştırması, sistem durumu denetimlerine yönelik yanıtlarını temel alarak yük dengeleyiciden VM 'Ler ekler veya kaldırır. 

Sanal makinelerin durumunu izlemek için **myHealthProbe** adlı bir durum araştırması oluşturun.

1. Sol taraftaki menüden **tüm hizmetler** ' i seçin, **tüm kaynaklar**' ı seçin ve ardından kaynaklar listesinden **myloadbalancer** ' ı seçin.

2. **Ayarlar** altında **sistem durumu araştırmaları**' nı ve ardından **Ekle**' yi seçin.
    
    | Ayar | Değer |
    | ------- | ----- |
    | Ad | **Myhealtharaştırması** girin. |
    | Protokol | **Http**'yi seçin. |
    | Bağlantı noktası | **80** girin.|
    | Aralık | Yoklama denemeleri arasındaki saniye cinsinden **Aralık** sayısı için **15** girin. |
    | İyi durumda olmayan durum eşiği | Bir VM sağlıksız kabul edilmeden önce gerçekleşmesi gereken **sağlıksız eşik** veya arka arkaya araştırma hatası sayısı için **2** ' yi seçin.|
    | | |

3. Rest varsayılanlarını bırakın ve **Tamam**' ı seçin.

### <a name="create-a-load-balancer-rule"></a>Yük dengeleyici kuralı oluşturma

Trafiğin VM’lere dağıtımını tanımlamak için bir yük dengeleyici kuralı kullanılır. Trafiği almak için gelen trafik için ön uç IP yapılandırmasını ve arka uç IP havuzunu tanımlarsınız. Kaynak ve hedef bağlantı noktası kuralda tanımlanmıştır. 

Bu bölümde, bir yük dengeleyici kuralı oluşturacaksınız:

* Adlandırılmış **Myhttprule**.
* **Loadbalancerön uç** adında.
* **80 numaralı bağlantı noktasını** dinliyor.
* Yük dengeli trafiği, **80 numaralı bağlantı noktasında** **Mybackendpool** adlı arka uca yönlendirir.

1. Sol taraftaki menüden **tüm hizmetler** ' i seçin, **tüm kaynaklar**' ı seçin ve ardından kaynaklar listesinden **myloadbalancer** ' ı seçin.

2. **Ayarlar** bölümünde **Yük Dengeleme kuralları**' nı ve ardından **Ekle**' yi seçin.

3. Yük Dengeleme kuralını yapılandırmak için şu değerleri kullanın:
    
    | Ayar | Değer |
    | ------- | ----- |
    | Ad | **Myhttprule** girin. |
    | IP sürümü | **IPv4** seçin |
    | Ön uç IP adresi | **Loadbalancerön uç** seçin |
    | Protokol | **TCP**’yi seçin. |
    | Bağlantı noktası | **80** girin.|
    | Arka uç bağlantı noktası | **80** girin. |
    | Arka uç havuzu | **Mybackendpool** öğesini seçin.|
    | Durum yoklaması | **Myhealtharaştırması**' ni seçin. |
    | Boşta kalma zaman aşımı (dakika) | Kaydırıcıyı **15** dakikaya taşıyın. |
    | TCP sıfırlaması | **Etkin**'i seçin. |
    
4. Kalan varsayılan değerleri bırakın ve **Tamam**' ı seçin.

## <a name="create-backend-servers"></a>Arka uç sunucular oluşturma

Bu bölümde şunları yapacaksınız:

* Yük dengeleyicinin arka uç havuzu için üç sanal makine oluşturun.
* Yük dengeleyiciyi test etmek için sanal makinelere IIS yükleyin.

### <a name="create-virtual-machines"></a>Sanal makineler oluşturma

Bu bölümde üç VM (**myVM1**, **myVM2** ve **myVM3**) oluşturacaksınız.

Bu VM 'Ler, daha önce oluşturulmuş yük dengeleyicinin arka uç havuzuna eklenir.

1. Portalın sol üst kısmında **kaynak oluştur**  >  **işlem**  >  **sanal makinesi**' ni seçin. 
   
2. **Sanal makine oluştur**' da **temel bilgiler** sekmesinde değerleri yazın veya seçin:

    | Ayar | Değer                                          |
    |-----------------------|----------------------------------|
    | **Proje ayrıntıları** |  |
    | Abonelik | Azure aboneliğinizi seçin |
    | Kaynak Grubu | **Createıntlbqs-RG** seçin |
    | **Örnek ayrıntıları** |  |
    | Sanal makine adı | **MyVM1** girin |
    | Region | Seç **(Avrupa) Batı Avrupa** |
    | Kullanılabilirlik seçenekleri | **Kullanılabilirlik bölgelerini** seçin |
    | Kullanılabilirlik alanı | **1** seçin |
    | Görüntü | **Windows Server 2019 Datacenter** seçin |
    | Azure Spot örneği | **Hayır** seçin |
    | Boyut | VM boyutunu seçin veya varsayılan ayarı yapın |
    | **Yönetici hesabı** |  |
    | Kullanıcı adı | Kullanıcı adı girin |
    | Parola | Parola girin |
    | Parolayı onayla | Parolayı yeniden girin |

3. **Ağ** sekmesini seçin veya **Sonraki: diskler**' i ve sonra **İleri: ağ**' ı seçin.
  
4. Ağ sekmesinde, şunu seçin veya girin:

    | Ayar | Değer |
    |-|-|
    | **Ağ arabirimi** |  |
    | Sanal ağ | **myVNet** |
    | Alt ağ | **myBackendSubnet** |
    | Genel IP | **Hiçbirini** seçme |
    | NIC ağ güvenlik grubu | **Gelişmiş** seçin|
    | Ağ güvenlik grubunu yapılandırma | **Yeni oluştur**’u seçin. </br> **Ağ güvenlik grubu oluştur**' da, **ad** alanına **mynsg** yazın. </br> **Tamam**'ı seçin |
    | **Yük dengeleme**  |
    | Bu sanal makine, var olan bir yük dengeleme çözümünün arkasına mi yerleştirsin? | **Evet**’i seçin |
    | **Yük Dengeleme ayarları** |
    | Yük dengeleme seçenekleri | **Azure yük dengelemeyi** seçin |
    | Yük dengeleyici seçin | **Myloadbalancer** seçin  |
    | Bir arka uç havuzu seçin | **Mybackendpool** seçin |
   
5. **Gözden geçir ve oluştur**’u seçin. 
  
6. Ayarları gözden geçirin ve ardından **Oluştur**' u seçin.

7. Aşağıdaki değerleri ve **myVM1** ile aynı diğer tüm ayarları Içeren iki VM oluşturmak için 1 ile 8 arasındaki adımları uygulayın:

    | Ayar | VM 2 | VM 3 |
    | ------- | ----- | ---- |
    | Name |  **myVM2** | **myVM3** |
    | Kullanılabilirlik alanı | **2** | **3** |
    | Ağ güvenlik grubu | Mevcut **Mynsg** 'yi seçin| Mevcut **Mynsg** 'yi seçin |


# <a name="basic-sku"></a>[**Temel SKU**](#tab/option-1-create-internal-load-balancer-basic)

>[!NOTE]
>Standart SKU yük dengeleyici, üretim iş yükleri için önerilir.  SKU 'lar hakkında daha fazla bilgi için bkz. **[Azure Load Balancer SKU 'lar](skus.md)**.

Bu bölümde, sanal makinelerin yükünü dengeleyen bir yük dengeleyici oluşturacaksınız. 

Bir iç yük dengeleyici oluşturduğunuzda, yük dengeleyici için ağ olarak bir sanal ağ yapılandırılır. 

Aşağıdaki diyagramda bu hızlı başlangıçta oluşturulan kaynaklar gösterilmektedir:

:::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/resources-diagram-internal-basic.png" alt-text="Hızlı başlangıçta oluşturulan temel yük dengeleyici kaynakları." border="false":::

Sanal ağdaki özel bir IP adresi, yük dengeleyici için ön uç (varsayılan olarak **Loadbalancerön uç** olarak adlandırılır) olarak yapılandırılır. 

Ön uç IP adresi **statik** veya **dinamik** olabilir.

## <a name="create-the-virtual-network"></a>Sanal ağı oluşturma

Bu bölümde, bir sanal ağ ve alt ağ oluşturacaksınız.

1. Ekranın sol üst kısmındaki **Kaynak oluştur > Ağ > Sanal ağ** seçeneğini belirleyin veya arama kutusuna **Sanal ağ** yazarak arama yapın.

2. **Sanal ağ oluştur**' da, **temel** bilgiler sekmesinde bu bilgileri girin veya seçin:

    | **Ayar**          | **Değer**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Proje ayrıntıları**  |                                                                 |
    | Abonelik     | Azure aboneliğinizi seçin                                  |
    | Kaynak Grubu   | **Createıntlbqs-RG** seçin |
    | **Örnek ayrıntıları** |                                                                 |
    | Name             | **Myvnet** girin                                    |
    | Region           | Seç **(Avrupa) Batı Avrupa** |

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

## <a name="create-load-balancer"></a>Yük dengeleyici oluşturma

1. Ekranın sol üst kısmında, **kaynak oluştur**  >  **ağ**  >  **Load Balancer**' ı seçin.

2. **Yük dengeleyici oluştur** sayfasının **temel bilgiler** sekmesinde, aşağıdaki bilgileri girin veya seçin: 

    | Ayar                 | Değer                                              |
    | ---                     | ---                                                |
    | Abonelik               | Aboneliğinizi seçin.    |    
    | Kaynak grubu         | Önceki adımda oluşturulan **Createıntlbqs-RG** öğesini seçin.|
    | Name                   | **Myloadbalancer** girin                                   |
    | Region         | **(Avrupa) Batı Avrupa** seçin.                                        |
    | Tür          | **Dahili**' ı seçin.                                        |
    | SKU           | **Temel** seçin |
    | Sanal ağ | Önceki adımda oluşturulan **Myvnet** öğesini seçin. |
    | Alt ağ  | Önceki adımda oluşturulan **Mybackendsubnet** öğesini seçin. |
    | IP adresi ataması | **Dinamik**' i seçin. |

3. Geri kalan ayarlar için varsayılan değerleri kabul edin ve ardından **gözden geçir + oluştur**' u seçin.

4. **Gözden geçir + oluştur** sekmesinde **Oluştur**' u seçin.   

    :::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/create-basic-internal-load-balancer.png" alt-text="Temel bir iç yük dengeleyici oluşturun." border="true":::

## <a name="create-load-balancer-resources"></a>Yük dengeleyici kaynakları oluşturma

Bu bölümde şunları yapılandırırsınız:

* Arka uç adres havuzu için yük dengeleyici ayarları.
* Bir sistem durumu araştırması.
* Yük dengeleyici kuralı.

### <a name="create-a-backend-pool"></a>Arka uç havuzu oluşturma

Arka uç adres havuzu, yük dengeleyiciye bağlı sanal (NIC) IP adreslerini içerir. 

Yük Dengeleme internet trafiği için sanal makineleri dahil etmek üzere **Mybackendpool** arka uç adres havuzunu oluşturun.

1. Sol taraftaki menüden **tüm hizmetler** ' i seçin, **tüm kaynaklar**' ı seçin ve ardından kaynaklar listesinden **myloadbalancer** ' ı seçin.

2. **Ayarlar** altında **arka uç havuzları**' nı seçin ve ardından **Ekle**' yi seçin.

3. **Arka uç Havuzu Ekle** sayfasında girin veya seçin:
    
    | Ayar | Değer |
    | ------- | ----- |
    | Ad | **Mybackendpool** girin. |
    | Sanal ağ | **Myvnet**' i seçin. |
    | İlişkili olduğu öğe | **Sanal makineleri** seçin |

4. **Add (Ekle)** seçeneğini belirleyin.

### <a name="create-a-health-probe"></a>Durum araştırması oluşturma

Yük dengeleyici, uygulamanızın durumunu bir sistem durumu araştırmasıyla izler. 

Sistem durumu araştırması, sistem durumu denetimlerine yönelik yanıtlarını temel alarak yük dengeleyiciden VM 'Ler ekler veya kaldırır. 

Sanal makinelerin durumunu izlemek için **myHealthProbe** adlı bir durum araştırması oluşturun.

1. Sol taraftaki menüden **tüm hizmetler** ' i seçin, **tüm kaynaklar**' ı seçin ve ardından kaynaklar listesinden **myloadbalancer** ' ı seçin.

2. **Ayarlar** altında **sistem durumu araştırmaları**' nı ve ardından **Ekle**' yi seçin.
    
    | Ayar | Değer |
    | ------- | ----- |
    | Ad | **Myhealtharaştırması** girin. |
    | Protokol | **Http**'yi seçin. |
    | Bağlantı noktası | **80** girin.|
    | Yol | Girmesini **/** |
    | Aralık | Yoklama denemeleri arasındaki saniye cinsinden **Aralık** sayısı için **15** girin. |
    | İyi durumda olmayan durum eşiği | Bir VM sağlıksız kabul edilmeden önce gerçekleşmesi gereken **sağlıksız eşik** veya arka arkaya araştırma hatası sayısı için **2** ' yi seçin.|

3. **Tamam**’ı seçin.

### <a name="create-a-load-balancer-rule"></a>Yük dengeleyici kuralı oluşturma

Trafiğin VM’lere dağıtımını tanımlamak için bir yük dengeleyici kuralı kullanılır. Trafiği almak için gelen trafik için ön uç IP yapılandırmasını ve arka uç IP havuzunu tanımlarsınız. Kaynak ve hedef bağlantı noktası kuralda tanımlanmıştır. 

Bu bölümde, bir yük dengeleyici kuralı oluşturacaksınız:

* Adlandırılmış **Myhttprule**.
* **Loadbalancerön uç** adında.
* **80 numaralı bağlantı noktasını** dinliyor.
* Yük dengeli trafiği, **80 numaralı bağlantı noktasında** **Mybackendpool** adlı arka uca yönlendirir.

1. Sol taraftaki menüden **tüm hizmetler** ' i seçin, **tüm kaynaklar**' ı seçin ve ardından kaynaklar listesinden **myloadbalancer** ' ı seçin.

2. **Ayarlar** bölümünde **Yük Dengeleme kuralları**' nı ve ardından **Ekle**' yi seçin.

3. Yük Dengeleme kuralını yapılandırmak için şu değerleri kullanın:
    
    | Ayar | Değer |
    | ------- | ----- |
    | Ad | **Myhttprule** girin. |
    | IP sürümü | **IPv4** seçin |
    | Ön uç IP adresi | **Loadbalancerön uç** seçin |
    | Protokol | **TCP**’yi seçin. |
    | Bağlantı noktası | **80** girin.|
    | Arka uç bağlantı noktası | **80** girin. |
    | Arka uç havuzu | **Mybackendpool** öğesini seçin.|
    | Durum yoklaması | **Myhealtharaştırması**' ni seçin. |
    | Boşta kalma zaman aşımı (dakika) | Kaydırıcıyı **15** dakikaya taşıyın. |
 
4. Kalan varsayılan değerleri bırakın ve **Tamam**' ı seçin.

## <a name="create-backend-servers"></a>Arka uç sunucular oluşturma

Bu bölümde şunları yapacaksınız:

* Yük dengeleyicinin arka uç havuzu için üç sanal makine oluşturun.
* Sanal makineler için bir kullanılabilirlik kümesi oluşturun.
* Yük dengeleyiciyi test etmek için sanal makinelere IIS yükleyin.

### <a name="create-virtual-machines"></a>Sanal makineler oluşturma

Bu bölümde üç VM oluşturacaksınız (**myVM1**, **myVM2**, **myVM3**).

İki VM, **myAvailabilitySet** adlı bir kullanılabilirlik kümesine eklenecektir.

Bu VM 'Ler, daha önce oluşturulmuş yük dengeleyicinin arka uç havuzuna eklenir.

1. Portalın sol üst kısmında **kaynak oluştur**  >  **işlem**  >  **sanal makinesi**' ni seçin. 
   
2. **Sanal makine oluştur**' da **temel bilgiler** sekmesinde değerleri yazın veya seçin:

    | Ayar | Değer                                          |
    |-----------------------|----------------------------------|
    | **Proje ayrıntıları** |  |
    | Abonelik | Azure aboneliğinizi seçin |
    | Kaynak Grubu | **Createıntlbqs-RG** seçin |
    | **Örnek ayrıntıları** |  |
    | Sanal makine adı | **MyVM1** girin |
    | Region | **Batı Avrupa** seçin |
    | Kullanılabilirlik seçenekleri | **Kullanılabilirlik kümesi**’ni seçin |
    | Kullanılabilirlik kümesi | **Yeni oluştur**’u seçin. </br> Ada **myAvailabilitySet** girin . </br> **Tamam**'ı seçin |
    | Görüntü | **Windows Server 2019 Datacenter** |
    | Azure Spot örneği | **Hayır** seçin |
    | Boyut | VM boyutunu seçin veya varsayılan ayarı yapın |
    | **Yönetici hesabı** |  |
    | Kullanıcı adı | Kullanıcı adı girin |
    | Parola | Parola girin |
    | Parolayı onayla | Parolayı yeniden girin |

3. **Ağ** sekmesini seçin veya **Sonraki: diskler**' i ve sonra **İleri: ağ**' ı seçin.
  
4. Ağ sekmesinde, şunu seçin veya girin:

    | Ayar | Değer |
    |-|-|
    | **Ağ arabirimi** |  |
    | Sanal ağ | **Myvnet** seçin |
    | Alt ağ | **Mybackendsubnet** seçin |
    | Genel IP | **Hiçbirini** seçme |
    | NIC ağ güvenlik grubu | **Gelişmiş** seçin|
    | Ağ güvenlik grubunu yapılandırma | **Yeni oluştur**’u seçin. </br> **Ağ güvenlik grubu oluştur**' da, **ad** alanına **mynsg** yazın. </br> **Tamam**'ı seçin |
    | **Yük dengeleme**  |
    | Bu sanal makine, var olan bir yük dengeleme çözümünün arkasına mi yerleştirsin? | **Hayır** seçin |

5. **Gözden geçir ve oluştur**’u seçin. 
  
6. Ayarları gözden geçirin ve ardından **Oluştur**' u seçin.

7. Aşağıdaki değerleri ve **myVM1** ile aynı diğer tüm ayarları Içeren iki VM oluşturmak için 1 ile 8 arasındaki adımları uygulayın:

    | Ayar | VM 2 | VM 3 |
    | ------- | ----- | ---- |
    | Name |  **myVM2** | **myVM3** |
    | Kullanılabilirlik kümesi | **MyAvailabilitySet** seçin | **MyAvailabilitySet** seçin |
    | Ağ güvenlik grubu | Mevcut **Mynsg** 'yi seçin | Mevcut **Mynsg** 'yi seçin |

### <a name="add-virtual-machines-to-the-backend-pool"></a>Arka uç havuzuna sanal makineler ekleme

Önceki adımlarda oluşturulan VM 'Lerin **Myloadbalancer** arka uç havuzuna eklenmesi gerekir.

1. Sol taraftaki menüden **tüm hizmetler** ' i seçin, **tüm kaynaklar**' ı seçin ve ardından kaynaklar listesinden **myloadbalancer** ' ı seçin.

2. **Ayarlar** altında **arka uç havuzları**' nı seçin ve ardından **mybackendpool**' u seçin.

3. Ilişkili **sanal makineleri** seçin .

4. **Sanal makineler** bölümünde **+ Ekle**' yi seçin.

5. **MyVM1**, **myVM2** ve **myVM3**' nin yanındaki kutuları seçin.

6. **Add (Ekle)** seçeneğini belirleyin.

7. **Kaydet**’i seçin.
---

## <a name="create-test-virtual-machine"></a>Test sanal makinesi oluştur

Bu bölümde, **Mytestvm** adlı bir sanal makine oluşturacaksınız.  Bu VM, yük dengeleyici yapılandırmasını test etmek için kullanılacaktır.

1. Portalın sol üst kısmında **kaynak oluştur**  >  **işlem**  >  **sanal makinesi**' ni seçin. 
   
2. **Sanal makine oluştur**' da **temel bilgiler** sekmesinde değerleri yazın veya seçin:

    | Ayar | Değer                                          |
    |-----------------------|----------------------------------|
    | **Proje ayrıntıları** |  |
    | Abonelik | Azure aboneliğinizi seçin |
    | Kaynak Grubu | **Createıntlbqs-RG** seçin |
    | **Örnek ayrıntıları** |  |
    | Sanal makine adı | **Mytestvm** girin |
    | Region | **Batı Avrupa** seçin |
    | Kullanılabilirlik seçenekleri | **Altyapı yedekliliği gerekli değil** ' i seçin |
    | Görüntü | **Windows Server 2019 Datacenter** seçin |
    | Azure Spot örneği | **Hayır** seçin |
    | Boyut | VM boyutunu seçin veya varsayılan ayarı yapın |
    | **Yönetici hesabı** |  |
    | Kullanıcı adı | Kullanıcı adı girin |
    | Parola | Parola girin |
    | Parolayı onayla | Parolayı yeniden girin |

3. **Ağ** sekmesini seçin veya **Sonraki: diskler**' i ve sonra **İleri: ağ**' ı seçin.
  
4. Ağ sekmesinde, şunu seçin veya girin:

    | Ayar | Değer |
    |-|-|
    | **Ağ arabirimi** |  |
    | Sanal ağ | **myVNet** |
    | Alt ağ | **myBackendSubnet** |
    | Genel IP | **Hiçbiri** seçeneğini belirtin. |
    | NIC ağ güvenlik grubu | **Gelişmiş** seçin|
    | Ağ güvenlik grubunu yapılandırma | Önceki adımda oluşturulan **Mynsg** öğesini seçin.|
       
5. **Gözden geçir ve oluştur**’u seçin. 
  
6. Ayarları gözden geçirin ve ardından **Oluştur**' u seçin.

## <a name="install-iis"></a>IIS yükleme

1. Sol taraftaki menüden **tüm hizmetler** ' i seçin, **tüm kaynaklar**' ı seçin ve ardından kaynaklar listesinden **createıntlbqs-RG** kaynak grubunda bulunan **myVM1** ' yi seçin.

2. **Genel bakış** sayfasında **Bağlan** **' ı ve** sonra da ' yi seçin.

4. VM oluşturma sırasında girilen kullanıcı adını ve parolayı girin.

5. **Bağlan**’ı seçin.

6. Sunucu masaüstünde **Windows Yönetim Araçları**  >  **Windows PowerShell**' e gidin.

7. PowerShell penceresinde aşağıdaki komutları çalıştırın:

    * IIS sunucusunu yükler
    * Varsayılan iisstart.htm dosyasını Kaldır
    * VM 'nin adını görüntüleyen yeni bir iisstart.htm dosyası ekleyin:

   ```powershell
    
    # install IIS server role
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    # Add a new htm file that displays server name
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
   ```
8. **MyVM1** ile savunma oturumunu kapatın.

9. IIS’yi ve **myVM2** ve **myVM3**’teki güncelleştirilmiş iisstart.htm dosyasını yüklemek için 1 ile 6 arasındaki adımları tekrarlayın.


## <a name="test-the-load-balancer"></a>Yük dengeleyiciyi test etme

1. **Genel bakış** ekranında yük DENGELEYICININ özel IP adresini bulun. Sol taraftaki menüden **tüm hizmetler** ' i seçin, **tüm kaynaklar**' ı seçin ve ardından **myloadbalancer**' ı seçin.

2. **Myloadbalancer** **genel** görünümünde, **özel IP adresi** ' nin yanındaki bir yere göz atın veya adresi kopyalayın.

3. Sol taraftaki menüden **tüm hizmetler** ' i seçin, **tüm kaynaklar**' ı seçin ve ardından kaynaklar listesinden **createıntlbqs-RG** kaynak grubunda bulunan **mytestvm** ' yi seçin.

4. **Genel bakış** sayfasında **Bağlan** **' ı ve** sonra da ' yi seçin.

6. VM oluşturma sırasında girilen kullanıcı adını ve parolayı girin.

7. **Mytestvm**'de **Internet Explorer** 'ı açın.

8. Önceki adımdan, tarayıcının adres çubuğuna IP adresini girin. IIS Web sunucusunun varsayılan sayfası, tarayıcıda görüntülenir.

    :::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/load-balancer-test.png" alt-text="Ekran görüntüsü, beklenen şekilde varsayılan sayfayı görüntüleyen bir tarayıcı penceresi gösterir." border="true":::
   
Yük dengeleyiciyi her iki VM arasında trafiği dağıtmalarını görmek için, her bir sanal makinenin IIS Web sunucusunun varsayılan sayfasını özelleştirebilir ve sonra Web tarayıcınızı istemci makinesinden yenileyebilirsiniz.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli olmadığında kaynak grubunu, yük dengeleyiciyi ve tüm ilgili kaynakları silin. Bunu yapmak için, kaynakları içeren **Createıntlbqs-RG** kaynak grubunu seçin ve **Sil**' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta:

* Bir Azure Standard veya temel iç yük dengeleyici oluşturuldu
* Yük dengeleyicisine 3 VM eklenmiş.
* Yük dengeleyici trafik kuralını, sistem durumu araştırmasını ve sonra yük dengeleyiciyi test edin. 

Azure Load Balancer hakkında daha fazla bilgi edinmek için devam edin:
> [!div class="nextstepaction"]
> [Azure Load Balancer nedir?](load-balancer-overview.md)