---
title: 'Öğretici: arka uç havuzunda birden fazla kullanılabilirlik kümesiyle yük dengeleyici oluşturma-Azure portal'
titleSuffix: Azure Load Balancer
description: Bu öğreticide, arka uç havuzunda birden fazla kullanılabilirlik kümesiyle bir Azure Load Balancer dağıtın.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: nat
ms.topic: tutorial
ms.date: 04/16/2021
ms.custom: template-tutorial
ms.openlocfilehash: 21ff43217a7b2bd874a384f3b07a48d5223a1be2
ms.sourcegitcommit: 089c2bd1ac4861f43c4b89396d3d056a6eef4913
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107602371"
---
# <a name="tutorial-create-a-load-balancer-with-more-than-one-availability-set-in-the-backend-pool-using-the-azure-portal"></a>Öğretici: Azure portal kullanarak arka uç havuzunda birden fazla kullanılabilirlik kümesiyle bir yük dengeleyici oluşturma

Yüksek kullanılabilirlik dağıtımının bir parçası olarak, sanal makineler genellikle birden çok kullanılabilirlik kümesi halinde gruplandırılır. 

Load Balancer, arka uç havuzundaki sanal makinelerle birden fazla kullanılabilirlik kümesini destekler.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Sanal ağ oluşturma
> * Giden bağlantı için bir NAT ağ geçidi oluşturma
> * Standart SKU Azure Load Balancer oluşturma
> * Dört sanal makine ve iki kullanılabilirlik kümesi oluşturma
> * Kullanılabilirlik kümelerine sanal makineleri yük dengeleyicinin arka uç havuzuna ekleyin
> * Yük dengeleyiciyi test etme

## <a name="prerequisites"></a>Önkoşullar

- Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-virtual-network"></a>Sanal ağ oluşturma

Bu bölümde, yük dengeleyici ve öğreticide kullanılan diğer kaynaklar için bir sanal ağ oluşturacaksınız.

1. [Azure portalında](https://portal.azure.com) oturum açın.

2. Portalın üst kısmındaki arama kutusuna **sanal ağ**' ı girin.

3. Arama sonuçlarında **sanal ağlar**' ı seçin.

4. **+ Oluştur** seçeneğini belirleyin.

5. **Sanal ağ oluştur**' un **temel bilgiler** sekmesinde, girin veya aşağıdaki bilgileri seçin:

    | Ayar | Değer |
    | ------- | ------|
    | **Proje ayrıntıları** |   |
    | Abonelik | Aboneliğinizi seçin. |
    | Kaynak grubu | **Yeni oluştur**’u seçin. </br> **Ad** olarak **Tutorlbmultiavs-RG** girin. |
    | **Örnek ayrıntıları** |   |
    | Name | **myVNet** yazın. |
    | Region | **(ABD) Batı ABD 2**’yi seçin. |

6. Sayfanın alt kısmındaki **IP adresleri** sekmesini veya **Sonraki: IP adresleri** düğmesini seçin.

7. **IP adresleri** sekmesinde **alt ağ adı** altında **varsayılan**' ı seçin.

8. **Alt ağı Düzenle** bölmesinde **alt ağ adı** altında **mybackendsubnet** yazın.

9. **Kaydet**’i seçin.

10. Sayfanın alt kısmındaki **güvenlik** sekmesini veya **Sonraki: güvenlik** düğmesini seçin.

11. **Güvenlik** sekmesinde, **Bastionhost** ' da **Etkinleştir**' i seçin.

12. Aşağıdaki bilgileri girin veya seçin:

    | Ayar | Değer |
    | ------- | ----- |
    | Savunma adı | **Mybastionhost** girin. |
    | AzureBastionSubnet adres alanı | **10.1.1.0/27** girin. |
    | Genel IP adresi | **Yeni oluştur**’u seçin. </br> **Ad** alanına **Mybastionıp** girin. |

13. Sayfanın alt kısmındaki **gözden geçir + oluştur** sekmesini veya mavi **gözden geçir + oluştur** düğmesini seçin.

14. **Oluştur**’u seçin.

## <a name="create-nat-gateway"></a>NAT ağ geçidi oluşturma 

Bu bölümde, sanal makinelerin giden bağlantısı için bir NAT ağ geçidi oluşturacaksınız.

1. Portalın üst kısmındaki arama kutusuna **NAT Gateway** girin.

2. Arama sonuçlarında **NAT ağ geçitleri** ' ni seçin.

3. **+ Oluştur** seçeneğini belirleyin.

4. **Ağ adresi çevirisi (NAT) ağ geçidi oluştur**' un **temel bilgiler** sekmesinde aşağıdaki bilgileri girin veya seçin:

    | Ayar | Değer |
    | ------- | ----- |
    | **Proje ayrıntıları** |   |
    | Abonelik | Aboneliğinizi seçin. |
    | Kaynak grubu | **Tutorlbmultiavs-RG** öğesini seçin. |
    | **Örnek ayrıntıları** |   |
    | NAT ağ geçidi adı | **Mynatgateway** girin. |
    | Region | **(ABD) Batı ABD 2**’yi seçin. |
    | Kullanılabilirlik alanı | **Hiçbiri** seçeneğini belirtin. |
    | Boşta kalma zaman aşımı (dakika) | **15** girin. |

5. **Gıden IP** sekmesini seçin veya sayfanın altındaki **sonrakı: giden IP** düğmesini seçin.

6. **Gıden IP** SEKMESINDE **ortak IP adresleri** ' nin YANıNDA **Yeni bir genel IP adresi oluştur** ' u seçin.

7. **Ad** Içinde **Mypublicıp-NAT** girin.

8. **Tamam**’ı seçin.

9. **Alt ağ** sekmesini seçin veya sayfanın altındaki **Sonraki: alt ağ** düğmesini seçin.

10. **Sanal ağ** altındaki açılır menüden **myvnet** ' i seçin.

11. **Mybackendsubnet öğesinin** yanındaki onay kutusunu işaretleyin.

12. **Gözden geçir + oluştur** sekmesini seçin veya sayfanın altındaki mavi **gözden geçir + oluştur** düğmesini seçin.

13. **Oluştur**’u seçin.

## <a name="create-load-balancer"></a>Yük dengeleyici oluşturma

Bu bölümde, sanal makineler için bir yük dengeleyici oluşturacaksınız.

1. Portalın üst kısmındaki arama kutusuna **yük dengeleyici** girin.

2. Arama sonuçlarında **yük dengeleyiciler** ' ı seçin.

3. **+ Oluştur** seçeneğini belirleyin.

4. **Yük dengeleyici oluşturma**' nın **temel bilgiler** sekmesinde, aşağıdaki bilgileri girin veya seçin:

    | Ayar | Değer |
    | ------- | ----- |
    | **Proje ayrıntıları** |   |
    | Abonelik | Aboneliğinizi seçin. |
    | Kaynak grubu | **Tutorlbmultiavs-RG** öğesini seçin. |
    | **Örnek ayrıntıları** |   |
    | Name | **Myloadbalancer** girin. |
    | Region | **(ABD) Batı ABD 2**’yi seçin. |
    | Tür | Varsayılan olarak **genel**' i bırakın. |
    | SKU | Varsayılan değer olan **Standart**' i bırakın. |
    | Katman | Varsayılan olarak **Bölgesel**' i bırakın. |
    | **Genel IP adresi** |   |
    | Genel IP adresi | **Yeni oluştur**' un varsayılan öğesini bırakın. |
    | Genel IP adresi adı | **Mypublicıp-lb** girin. |
    | Kullanılabilirlik alanı | **Bölge yedekli** seçeneğini belirleyin. |
    | Genel IPv6 adresi ekle | Varsayılan değer olan **Hayır** olarak bırakın. |
    | Yönlendirme tercihi | Varsayılan **Microsoft ağı**' nı bırakın. |

5. **Gözden geçir + oluştur** sekmesini seçin veya sayfanın altındaki mavi **gözden geçir + oluştur** düğmesini seçin.

6. **Oluştur**’u seçin.

### <a name="configure-load-balancer-settings"></a>Yük dengeleyici ayarlarını yapılandırma

Bu bölümde, **Myloadbalancer** için bir arka uç havuzu oluşturacaksınız.

**Http** ve **80 numaralı bağlantı noktasını** izlemek için bir sistem durumu araştırması oluşturacaksınız. Sistem durumu araştırması, arka uç havuzundaki sanal makinelerin sistem durumunu izler. 

Giden SNAT devre dışı ile **80 numaralı bağlantı noktası** için bir yük dengeleme kuralı oluşturacaksınız. Daha önce oluşturduğunuz NAT ağ geçidi, sanal makinelerin giden bağlantısını işleymeyecektir.

1. Portalın üst kısmındaki arama kutusuna **yük dengeleyici** girin.

2. Arama sonuçlarında **yük dengeleyiciler** ' ı seçin.

3. **Myloadbalancer** öğesini seçin.

4. **Myloadbalancer** içinde **Ayarlar**' da **arka uç havuzları** ' nı seçin.

5. **Arka uç havuzlarında** **+ Ekle** ' yi seçin.

6. **Arka uç Havuzu Ekle**' de aşağıdaki bilgileri girin veya seçin:

    | Ayar | Değer |
    | ------- | ----- |
    | Ad | **Mybackendpool** girin. |
    | Sanal ağ | **Myvnet**' i seçin. |
    | Arka uç havuzu yapılandırması | Varsayılan **NIC**' i bırakın. |
    | IP sürümü | Varsayılan **IPv4**' ü bırakın. |

7. **Add (Ekle)** seçeneğini belirleyin.

8. **Sistem durumu araştırmalarını** seçin.

9. **+ Ekle**'yi seçin.

10. **Sistem durumu araştırması Ekle**' de, aşağıdaki bilgileri girin veya seçin:

    | Ayar | Değer |
    | ------- | ----- |
    | Ad | **Myhttparaştırması** girin. |
    | Protokol | **Http**'yi seçin. |
    | Bağlantı noktası | Varsayılan değer olan **80**' i bırakın. |
    | Yol | Varsayılan ' i bırakın **/** . |
    | Aralık | Varsayılan **5** saniye olarak bırakın. |
    | İyi durumda olmayan durum eşiği | Varsayılan **2** ardışık hatalardan ayrılın. |

11. **Add (Ekle)** seçeneğini belirleyin.

12. **Yük Dengeleme kurallarını** seçin. 

13. **+ Ekle**'yi seçin.

14. **Yük Dengeleme kuralı ekle**' de aşağıdaki bilgileri girin veya seçin:

    | Ayar | Değer |
    | ------- | ----- |
    | Ad | **Myhttprule** girin. |
    | IP sürümü | Varsayılan **IPv4**' ü bırakın. |
    | Ön uç IP adresi | **Loadbalancerön uç** öğesini seçin. |
    | Protokol | Varsayılan **TCP**' yi seçin. |
    | Bağlantı noktası | **80** girin. |
    | Arka uç bağlantı noktası | **80** girin. |
    | Arka uç havuzu | **Mybackendpool** öğesini seçin. |
    | Durum yoklaması | **Myhttparaştırması** öğesini seçin. |
    | Oturum kalıcılığı | Varsayılan değerini **yok** olarak bırakın. |
    | Boşta kalma zaman aşımı (dakika) | Kaydırıcıyı **15** olarak değiştirin. |
    | TCP sıfırlaması | **Etkin**'i seçin. |
    | Kayan IP | Varsayılan olarak **devre dışı** bırakın. |
    | Giden kaynak ağ adresi çevirisi (SNAT) | Varsayılan **(önerilen) çıkış kuralları ' nı, arka uç havuzu üyelerine internet erişimi sağlamak Için kullanın.** |

5. **Add (Ekle)** seçeneğini belirleyin.

## <a name="create-virtual-machines"></a>Sanal makineler oluşturma

Bu bölümde, Grup başına iki sanal makine ile iki kullanılabilirlik grubu oluşturacaksınız. Bu makineler, oluşturma sırasında yük dengeleyicinin arka uç havuzuna eklenecektir. 

### <a name="create-first-set-of-vms"></a>İlk VM kümesini oluştur

1. Portalın sol üst kısmında **+ kaynak oluştur** ' u seçin.

2. **Yeni**' de, **işlem**  >  **sanal makinesi**' ni seçin.

3. **Sanal makine oluşturma**' nın **temel bilgiler** sekmesinde, aşağıdaki bilgileri girin veya seçin:

    | Ayar | Değer |
    | ------- | ----- |
    | **Proje ayrıntıları** |   |
    | Abonelik | Aboneliğinizi seçin |
    | Kaynak grubu | **Tutorlbmultiavs-RG** öğesini seçin. |
    | **Örnek ayrıntıları** |   |
    | Sanal makine adı | **MyVM1** girin. |
    | Region | **(ABD) Batı ABD 2**’yi seçin. |
    | Kullanılabilirlik seçenekleri | **Kullanılabilirlik kümesi** seçin. |
    | Kullanılabilirlik kümesi | **Yeni oluştur**’u seçin. </br> Ada **myAvailabilitySet1** girin . </br> **Tamam**’ı seçin. |
    | Görüntü | **Windows Server 2019 Datacenter-Gen1** öğesini seçin. |
    | Azure Spot örneği | Varsayılan işaretini işaretsiz bırakın. |
    | Boyut | Sanal makine için bir boyut seçin. |
    | **Yönetici hesabı** |   |
    | Kullanıcı adı | Bir Kullanıcı adı girin. |
    | Parola | Bir parola girin. |
    | **Gelen bağlantı noktası kuralları** |   |
    | Genel gelen bağlantı noktaları | **Hiçbiri** seçeneğini belirtin. |

4. **Ağ** sekmesini seçin veya sayfanın altındaki **Sonraki: diskler**, **Sonraki: ağ iletişimi** düğmesini seçin.

5. **Ağ** sekmesinde, aşağıdaki bilgileri girin veya seçin:

    | Ayar | Değer |
    | ------- | ----- |
    | **Ağ arabirimi** |   |
    | Sanal ağ | **Myvnet**' i seçin. |
    | Alt ağ | **Mybackendsubnet** öğesini seçin. |
    | Genel IP | **Hiçbiri** seçeneğini belirtin. |
    | NIC ağ güvenlik grubu | **Gelişmiş**'i seçin. |
    | Ağ güvenlik grubunu yapılandırma | **Yeni oluştur**’u seçin. </br> **Ad** alanına **mynsg** yazın. </br> **Gelen kurallarında** **+ gelen kuralı ekle** ' yi seçin. </br> **Hizmet** için **http** 'yi seçin. </br> **Ad** Için **Myhttprule** girin. </br> **Add (Ekle)** seçeneğini belirleyin. </br> **Tamam**’ı seçin. | 
    | **Yük dengeleme** |   |
    | Bu sanal makine, var olan bir yük dengeleme çözümünün arkasına mi yerleştirsin? | Onay kutusunu işaretleyin. |
    | **Yük Dengeleme ayarları** |   |
    | Yük dengeleme seçenekleri | **Azure Yük dengeleyiciyi** seçin. |
    | Yük dengeleyici seçin | **Myloadbalancer** öğesini seçin. |
    | Bir arka uç havuzu seçin | **Mybackendpool** öğesini seçin. |

6. **Gözden geçir + oluştur** sekmesini seçin veya sayfanın altındaki mavi **gözden geçir + oluştur** düğmesini seçin.

7. **Oluştur**’u seçin.

8. Küme ikinci sanal makinesini oluşturmak için 1 ile yedi arasındaki adımları yineleyin. VM 'nin ayarlarını aşağıdaki bilgilerle değiştirin:

    | Ayar | Değer |
    | ------- | ----- |
    | Ad | **MyVM2** girin. |
    | Kullanılabilirlik kümesi | **MyAvailabilitySet1** öğesini seçin. |
    | Sanal Ağ | **Myvnet**' i seçin. |
    | Alt ağ | **Mybackendsubnet** öğesini seçin. |
    | Genel IP | **Hiçbiri** seçeneğini belirtin. |
    | Ağ güvenlik grubu | **Mynsg** öğesini seçin. |
    | Yük dengeleme seçenekleri | **Azure Yük dengeleyiciyi** seçin. |
    | Yük dengeleyici seçin | **Myloadbalancer** öğesini seçin. |
    | Bir arka uç havuzu seçin | **Mybackendpool** öğesini seçin. |

### <a name="create-second-set-of-vms"></a>İkinci VM kümesi oluşturma

1. Portalın sol üst kısmında **+ kaynak oluştur** ' u seçin.

2. **Yeni**' de, **işlem**  >  **sanal makinesi**' ni seçin.

3. **Sanal makine oluşturma**' nın **temel bilgiler** sekmesinde, aşağıdaki bilgileri girin veya seçin:

    | Ayar | Değer |
    | ------- | ----- |
    | **Proje ayrıntıları** |   |
    | Abonelik | Aboneliğinizi seçin |
    | Kaynak grubu | **Tutorlbmultiavs-RG** öğesini seçin. |
    | **Örnek ayrıntıları** |   |
    | Sanal makine adı | **MyVM3** girin. |
    | Region | **(ABD) Batı ABD 2**’yi seçin. |
    | Kullanılabilirlik seçenekleri | **Kullanılabilirlik kümesi** seçin. |
    | Kullanılabilirlik kümesi | **Yeni oluştur**’u seçin. </br> Ada **myAvailabilitySet2** girin . </br> **Tamam**’ı seçin. |
    | Görüntü | **Windows Server 2019 Datacenter-Gen1** öğesini seçin. |
    | Azure Spot örneği | Varsayılan işaretini işaretsiz bırakın. |
    | Boyut | Sanal makine için bir boyut seçin. |
    | **Yönetici hesabı** |   |
    | Kullanıcı adı | Bir Kullanıcı adı girin. |
    | Parola | Bir parola girin. |
    | **Gelen bağlantı noktası kuralları** |   |
    | Genel gelen bağlantı noktaları | **Hiçbiri** seçeneğini belirtin. |

4. **Ağ** sekmesini seçin veya sayfanın altındaki **Sonraki: diskler**, **Sonraki: ağ iletişimi** düğmesini seçin.

5. **Ağ** sekmesinde, aşağıdaki bilgileri girin veya seçin:

    | Ayar | Değer |
    | ------- | ----- |
    | **Ağ arabirimi** |   |
    | Sanal ağ | **Myvnet**' i seçin. |
    | Alt ağ | **Mybackendsubnet** öğesini seçin. |
    | Genel IP | **Hiçbiri** seçeneğini belirtin. |
    | NIC ağ güvenlik grubu | **Gelişmiş**'i seçin. |
    | Ağ güvenlik grubunu yapılandırma | **Mynsg** öğesini seçin. | 
    | **Yük dengeleme** |   |
    | Bu sanal makine, var olan bir yük dengeleme çözümünün arkasına mi yerleştirsin? | Onay kutusunu işaretleyin. |
    | **Yük Dengeleme ayarları** |   |
    | Yük dengeleme seçenekleri | **Azure Yük dengeleyiciyi** seçin. |
    | Yük dengeleyici seçin | **Myloadbalancer** öğesini seçin. |
    | Bir arka uç havuzu seçin | **Mybackendpool** öğesini seçin. |

6. **Gözden geçir + oluştur** sekmesini seçin veya sayfanın altındaki mavi **gözden geçir + oluştur** düğmesini seçin.

7. **Oluştur**’u seçin.

8. Küme ikinci sanal makinesini oluşturmak için 1 ile yedi arasındaki adımları yineleyin. VM 'nin ayarlarını aşağıdaki bilgilerle değiştirin:

    | Ayar | Değer |
    | ------- | ----- |
    | Ad | **MyVM4** girin. |
    | Kullanılabilirlik kümesi | **MyAvailabilitySet2** öğesini seçin. |
    | Sanal Ağ | **Myvnet**' i seçin. |
    | Ağ güvenlik grubu | **Mynsg** öğesini seçin. |
    | Yük dengeleme seçenekleri | **Azure Yük dengeleyiciyi** seçin. |
    | Yük dengeleyici seçin | **Myloadbalancer** öğesini seçin. |
    | Bir arka uç havuzu seçin | **Mybackendpool** öğesini seçin. |

## <a name="install-iis"></a>IIS yükleme

Bu bölümde, daha önce oluşturduğunuz Azure savunma konağını sanal makinelere bağlanıp IIS 'yi yüklemek için kullanacaksınız.

1. Portalın üst kısmındaki arama kutusuna **sanal makine** yazın.

2. Arama sonuçlarındaki **sanal makineler** ' i seçin.

3. **MyVM1** öğesini seçin.

4. MyVM1 **genel bakış** sayfasında Bağlan ' ı seçin   >  .

5. **Kullanım kullanımı**' nı seçin.

6. Sanal makineyi oluştururken oluşturduğunuz **Kullanıcı adını** ve **parolayı** girin.

7. **Bağlan**’ı seçin.

7. Sunucu masaüstünde **Windows Yönetim Araçları**  >  **Windows PowerShell**' e gidin.

8. PowerShell penceresinde aşağıdaki komutları çalıştırın:

    * IIS sunucusunu yükler
    * Varsayılan iisstart.htm dosyasını Kaldır
    * VM 'nin adını görüntüleyen yeni bir iisstart.htm dosyası ekleyin:

   ```powershell
    # Install IIS server role
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
    Remove-Item  C:\inetpub\wwwroot\iisstart.htm
    
    # Add a new htm file that displays server name
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
   ```
8. **MyVM1** ile savunma oturumunu kapatın.

9. **MyVM2**, **myVM3** ve **myVM4** için 1 ile sekiz arasındaki adımları yineleyin.

## <a name="test-the-load-balancer"></a>Yük dengeleyiciyi test etme

Bu bölümde, yük dengeleyicinin genel IP adresini keşfedeceksiniz. Yük dengeleyicinin işlemini test etmek için IP adresini kullanacaksınız.

1. Portalın üst kısmındaki arama kutusuna **genel IP** girin.

2. Arama sonuçlarında **genel IP adresleri** ' ni seçin.

3. **Mypublicıp-lb**' ı seçin.

4. **Mypublicıp-lb** **genel bakış** sayfasında **IP adresi** bölümünde listelenen genel IP adresini göz önünde edin:

    :::image type="content" source="./media/tutorial-multi-availability-sets-portal/find-public-ip.png" alt-text="Yük dengeleyicinin genel IP adresini bulun." border="true":::

5. Bir Web tarayıcısı açın ve adres çubuğuna genel IP adresini girin:

    :::image type="content" source="./media/tutorial-multi-availability-sets-portal/verify-load-balancer.png" alt-text="Yük dengeleyiciyi web tarayıcıyla test edin." border="true":::

6. Arka uç havuzundaki diğer sanal makinelere dengelenen trafiği görmek için tarayıcıda Yenile ' yi seçin.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu uygulamayı kullanmaya devam edemeyecekiniz, yük dengeleyiciyi ve destekleyici kaynakları aşağıdaki adımlarla silin:

1. Portalın üst kısmındaki arama kutusuna **kaynak grubu** girin.

2. Arama sonuçlarında **kaynak grupları** ' nı seçin.

3. **Tutorlbmultiavs-RG** öğesini seçin.

4. **Tutorlbmultiavs-RG** Genel Bakış sayfasında **kaynak grubunu sil**' i seçin.

5. **Kaynak grubu adını yazmak Için** **Tutorlbmultiavs-RG** girin.

6. **Sil**’i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide şunları yaptınız:

* Bir sanal ağ ve Azure savunma Konağı oluşturdunuz.
* Azure Standart Load Balancer oluşturdu.
* Küme başına iki sanal makine ile iki kullanılabilirlik kümesi oluşturuldu.
* IIS yüklendi ve yük dengeleyici test edildi.

Bir çapraz bölge Azure Load Balancer oluşturma hakkında bilgi edinmek için sonraki makaleye ilerleyin:
> [!div class="nextstepaction"]
> [Çapraz bölge yük dengeleyici oluşturma](tutorial-cross-region-portal.md)

