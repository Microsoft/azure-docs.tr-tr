---
title: 'Öğretici: IP tabanlı arka uç Azure portal ortak yük dengeleyici oluşturma'
titleSuffix: Azure Load Balancer
description: Bu öğreticide, IP tabanlı arka uç havuzu ile genel yük dengeleyici oluşturmayı öğrenin.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: tutorial
ms.date: 3/31/2021
ms.custom: template-tutorial
ms.openlocfilehash: f8174d46d1674e0cf81e36e89f6fb6180091a9b9
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106123598"
---
# <a name="tutorial-create-a-public-load-balancer-with-an-ip-based-backend-using-the-azure-portal"></a>Öğretici: Azure portal kullanarak IP tabanlı arka uçta ortak yük dengeleyici oluşturma

Bu öğreticide, IP tabanlı arka uç havuzu ile genel yük dengeleyici oluşturmayı öğreneceksiniz. 

Azure Load Balancer geleneksel dağıtımı, sanal makinelerin ağ arabirimini kullanır. IP tabanlı arka uç ile sanal makineler, IP adresi tarafından arka uca eklenir.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Sanal ağ oluşturma
> * Giden bağlantı için bir NAT ağ geçidi oluşturma
> * Azure Load Balancer oluşturma
> * IP tabanlı arka uç havuzu oluşturma
> * İki sanal makine oluşturma
> * Yük dengeleyiciyi test etme
## <a name="prerequisites"></a>Önkoşullar

- Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-virtual-network"></a>Sanal ağ oluşturma

Bu bölümde, yük dengeleyici, NAT ağ geçidi ve sanal makineler için bir sanal ağ oluşturacaksınız.

1. [Azure portalında](https://portal.azure.com) oturum açın.

1. Ekranın sol üst kısmındaki **Kaynak oluştur > Ağ > Sanal ağ** seçeneğini belirleyin veya arama kutusuna **Sanal ağ** yazarak arama yapın.

2. **Oluştur**’u seçin. 

3. **Sanal ağ oluştur**' da, **temel** bilgiler sekmesinde bu bilgileri girin veya seçin:

    | **Ayar**          | **Değer**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Proje ayrıntıları**  |                                                                 |
    | Abonelik     | Azure aboneliğinizi seçin                                  |
    | Kaynak Grubu   | **Tutorpublbıp-RG** seçin |
    | **Örnek ayrıntıları** |                                                                 |
    | Name             | **Myvnet** girin                                    |
    | Region           | **(US) Doğu ABD** seçin |

4. **IP adresleri** sekmesini seçin veya sayfanın altındaki **Sonraki: IP adresleri** düğmesini seçin.

5. **IP adresleri** sekmesinde şu bilgileri girin:

    | Ayar            | Değer                      |
    |--------------------|----------------------------|
    | IPv4 adres alanı | **10.1.0.0/16** girin |

6. **Alt ağ adı** altında, **varsayılan** sözcük ' ı seçin.

7. **Alt ağı Düzenle**' de şu bilgileri girin:

    | Ayar            | Değer                      |
    |--------------------|----------------------------|
    | Alt ağ adı | **Mybackendsubnet** girin |
    | Alt ağ adres aralığı | **10.1.0.0/24** girin |

8. **Kaydet**’i seçin.

9. **Güvenlik** sekmesini seçin.

10. **Bastionhost** altında **Etkinleştir**' i seçin. Bu bilgileri girin:

    | Ayar            | Değer                      |
    |--------------------|----------------------------|
    | Savunma adı | **Mybastionhost** girin |
    | AzureBastionSubnet adres alanı | **10.1.1.0/27** girin |
    | Genel IP Adresi | **Yeni oluştur**’u seçin. </br> **Ad** Için **Mybastionıp** girin. </br> **Tamam**’ı seçin. |


11. **Gözden geçir + oluştur** sekmesini seçin ya da **gözden geçir + oluştur** düğmesini seçin.

12. **Oluştur**’u seçin.
## <a name="create-nat-gateway"></a>NAT ağ geçidi oluşturma

Bu bölümde, bir NAT ağ geçidi oluşturacak ve daha önce oluşturduğunuz sanal ağdaki alt ağa atayacaksınız.

1. Ekranın sol üst kısmında, **bir kaynak oluştur > ağ > NAT ağ geçidi** ' ni veya arama kutusunda **NAT ağ geçidini** ara ' yı seçin.

2. **Oluştur**’u seçin. 

3. **Ağ adresi çevirisi (NAT) ağ geçidi oluştur**' da, **temel** bilgiler sekmesinde bu bilgileri girin veya seçin:

    | **Ayar**          | **Değer**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Proje ayrıntıları**  |                                                                 |
    | Abonelik     | Azure aboneliğinizi seçin.                                  |
    | Kaynak Grubu   | **Yeni oluştur** ' u seçin ve metin kutusuna **Tutorpublbıp-RG** girin. </br> **Tamam**’ı seçin. |
    | **Örnek ayrıntıları** |                                                                 |
    | Name             | **Mynatgateway** girin                                    |
    | Region           | **(US) Doğu ABD** seçin  |
    | Kullanılabilirlik Alanı | **Hiçbiri** seçeneğini belirtin. |
    | Boşta kalma zaman aşımı (dakika) | **10** girin. |

4. **Gıden IP** sekmesini seçin veya sayfanın altındaki **sonrakı: giden IP** düğmesini seçin.

5. **Gıden IP** sekmesinde aşağıdaki bilgileri girin veya seçin:

    | **Ayar** | **Değer** |
    | ----------- | --------- |
    | Genel IP adresleri | **Yeni bir genel IP adresi oluştur**' u seçin. </br> **Ad** alanına **Mypublicıp-NAT** girin. </br> **Tamam**’ı seçin. |

6. **Alt ağ** sekmesini seçin veya sayfanın altındaki **Sonraki: alt ağ** düğmesini seçin.

7. **Alt ağ** sekmesinde, **sanal ağ** çekme kutusunda **myvnet** ' i seçin.

8. **Mybackendsubnet öğesinin** yanındaki kutuyu işaretleyin.

9. **Gözden geçir + oluştur** sekmesini seçin veya sayfanın altındaki mavi **gözden geçir + oluştur** düğmesini seçin.

10. **Oluştur**’u seçin.
## <a name="create-load-balancer"></a>Yük dengeleyici oluşturma

Bu bölümde, standart bir Azure Load Balancer oluşturacaksınız. 

1. [Azure portalında](https://portal.azure.com) oturum açın.
2. **Kaynak oluştur**’u seçin. 
3. Arama kutusuna **yük dengeleyici** girin. Arama sonuçlarında **Yük dengeleyiciyi** seçin.
4. **Yük dengeleyici** sayfasında **Oluştur**' u seçin.
5. **Yük dengeleyici oluştur** sayfasında girin veya aşağıdaki bilgileri seçin: 

    | Ayar                 | Değer                                              |
    | ---                     | ---                                                |
    | **Proje ayrıntıları** |   |
    | Abonelik               | Aboneliğinizi seçin.    |    
    | Kaynak grubu         | **Tutorpublbıp-RG** öğesini seçin.|
    | **Örnek ayrıntıları** |   |
    | Name                   | **Myloadbalancer** girin                                   |
    | Region         | **Doğu ABD (ABD)** seçin.                                        |
    | Tür          | **Genel**’i seçin.                                        |
    | SKU           | Varsayılan **Standart** bırakın. |
    | Katman          | Varsayılan **Bölgesel**' i bırakın. |
    | **Genel IP adresi** |   |
    | Genel IP adresi | **Yeni oluştur**’u seçin. </br> Kullanmak istediğiniz mevcut bir genel IP varsa, **Varolanı kullan**' ı seçin. |
    | Genel IP adresi adı | Metin kutusuna **Mypublicıp-lb** yazın.|
    | Kullanılabilirlik alanı | Esnek yük dengeleyici oluşturmak için **bölge yedekli** ' ı seçin. Bir ZGen yük dengeleyici oluşturmak için, 1, 2 veya 3 ' ten belirli bir bölge seçin |
    | Genel IPv6 adresi ekle | **Hayır**'ı seçin. </br> IPv6 adresleri ve yük dengeleyici hakkında daha fazla bilgi için bkz. [Azure sanal ağ Için IPv6 nedir?](../virtual-network/ipv6-overview.md)  |
    | Yönlendirme tercihi | Varsayılan **Microsoft ağı**' nı bırakın. </br> Yönlendirme tercihi hakkında daha fazla bilgi için bkz. [yönlendirme tercihi (Önizleme) nedir?](../virtual-network/routing-preference-overview.md). |

6. Geri kalan ayarlar için varsayılan değerleri kabul edin ve ardından **gözden geçir + oluştur**' u seçin.

7. **Gözden geçir + oluştur** sekmesinde **Oluştur**' u seçin.

## <a name="create-load-balancer-resources"></a>Yük dengeleyici kaynakları oluşturma

Bu bölümde şunları yapılandırırsınız:

* Arka uç adres havuzu için yük dengeleyici ayarları.
* Bir sistem durumu araştırması.
* Yük dengeleyici kuralı.

### <a name="create-a-backend-pool"></a>Arka uç havuzu oluşturma

Arka uç adres havuzu, yük dengeleyiciye bağlı sanal (NIC) IP adreslerini içerir. 

Yük Dengeleme internet trafiği için sanal makineleri dahil etmek üzere **Mybackendpool** arka uç adres havuzunu oluşturun.

1. Sol taraftaki menüden **tüm hizmetler** ' i seçin, **tüm kaynaklar**' ı seçin ve ardından kaynaklar listesinden **myloadbalancer** ' ı seçin.

2. **Ayarlar** altında, **arka uç havuzları**' nı seçin ve **+ Ekle**' yi seçin.

3. **Arka uç Havuzu Ekle** sayfasında, aşağıdaki bilgileri girin veya seçin:

    | Ayar | Değer |
    | ------- | ----- |
    | Ad | **Mybackendpool** girin. |
    | Sanal ağ | **Myvnet**' i seçin. |
    | Arka uç havuzu yapılandırması | **IP adresi** seçin. |
    | IP sürümü | **IPv4** seçin. |

4. **Add (Ekle)** seçeneğini belirleyin.

### <a name="create-a-health-probe"></a>Durum araştırması oluşturma

Yük dengeleyici, uygulamanızın durumunu bir sistem durumu araştırmasıyla izler. 

Sistem durumu araştırması, sistem durumu denetimlerine yönelik yanıtlarını temel alarak yük dengeleyiciden VM 'Ler ekler veya kaldırır. 

Sanal makinelerin durumunu izlemek için **myHealthProbe** adlı bir durum araştırması oluşturun.

1. Sol taraftaki menüden **tüm hizmetler** ' i seçin, **tüm kaynaklar**' ı seçin ve ardından kaynaklar listesinden **myloadbalancer** ' ı seçin.

2. **Ayarlar** altında **sistem durumu araştırmaları**' nı seçin ve **+ Ekle**' yi seçin.
    
    | Ayar | Değer |
    | ------- | ----- |
    | Ad | **Myhealtharaştırması** girin. |
    | Protokol | **TCP**’yi seçin. |
    | Bağlantı noktası | **80** girin.|
    | Aralık | Yoklama denemeleri arasındaki saniye cinsinden **Aralık** sayısı için **15** girin. |
    | İyi durumda olmayan durum eşiği | **2** öğesini seçin. |
   
3. Rest varsayılanlarını bırakın ve **Ekle**' yi seçin.

### <a name="create-a-load-balancer-rule"></a>Yük dengeleyici kuralı oluşturma

Trafiğin VM’lere dağıtımını tanımlamak için bir yük dengeleyici kuralı kullanılır. Trafiği almak için gelen trafik için ön uç IP yapılandırmasını ve arka uç IP havuzunu tanımlarsınız. Kaynak ve hedef bağlantı noktası kuralda tanımlanmıştır. 

Bu bölümde, bir yük dengeleyici kuralı oluşturacaksınız:

* Adlandırılmış **Myhttprule**.
* **Loadbalancerön uç** adında.
* **80 numaralı bağlantı noktasını** dinliyor.
* Yük dengeli trafiği, **80 numaralı bağlantı noktasında** **Mybackendpool** adlı arka uca yönlendirir.

1. Sol taraftaki menüden **tüm hizmetler** ' i seçin, **tüm kaynaklar**' ı seçin ve ardından kaynaklar listesinden **myloadbalancer** ' ı seçin.

2. **Ayarlar** bölümünde **Yük Dengeleme kuralları**' nı seçin ve **+ Ekle**' yi seçin.

3. Yük dengeleyici kuralı için aşağıdaki bilgileri girin veya seçin:
    
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
    | Oturum kalıcılığı | Varsayılan değerini **yok** olarak bırakın. |
    | Boşta kalma zaman aşımı (dakika) | **15** dakika girin. |
    | TCP sıfırlaması | **Etkin**'i seçin. |
    | Kayan IP | **Devre dışı** girişini seçin. |
    | Giden kaynak ağ adresi çevirisi (SNAT) | **Arka uç havuzu üyelerine internet erişimi sağlamak için giden kuralları kullanın (önerilir)** seçeneğini belirleyin. |

4. Varsayılan değerleri bırakın ve ardından **Ekle**' yi seçin.

## <a name="create-virtual-machines"></a>Sanal makineler oluşturma

Bu bölümde iki farklı bölgede (**bölge 1** ve **bölge 2**) iki VM (**myVM1** ve **myVM2**) oluşturacaksınız.

Bu VM 'Ler, daha önce oluşturulmuş yük dengeleyicinin arka uç havuzuna eklenir.

1. Portalın sol üst kısmında **kaynak oluştur**  >  **işlem**  >  **sanal makinesi**' ni seçin. 
   
2. **Sanal makine oluştur** bölümünde **temel bilgiler** sekmesinde değerleri girin veya seçin:

    | Ayar | Değer                                          |
    |-----------------------|----------------------------------|
    | **Proje ayrıntıları** |  |
    | Abonelik | Azure aboneliğinizi seçin |
    | Kaynak Grubu | **Tutorpublbıp-RG** seçin |
    | **Örnek ayrıntıları** |  |
    | Sanal makine adı | **MyVM1** girin |
    | Region | **(US) Doğu ABD** seçin |
    | Kullanılabilirlik seçenekleri | **Kullanılabilirlik bölgelerini** seçin |
    | Kullanılabilirlik alanı | **1** seçin |
    | Görüntü | **Windows Server 2019 Datacenter** seçin |
    | Azure Spot örneği | Varsayılanı bırak |
    | Boyut | VM boyutunu seçin veya varsayılan ayarı yapın |
    | **Yönetici hesabı** |  |
    | Kullanıcı adı | Kullanıcı adı girin |
    | Parola | Parola girin |
    | Parolayı onayla | Parolayı yeniden girin |
    | **Gelen bağlantı noktası kuralları** |  |
    | Genel gelen bağlantı noktaları | **Hiçbirini** seçme |

3. **Ağ** sekmesini seçin veya **Sonraki: diskler**' i ve sonra **İleri: ağ**' ı seçin.
  
4. Ağ sekmesinde, şunu seçin veya girin:

    | Ayar | Değer |
    |-|-|
    | **Ağ arabirimi** |  |
    | Sanal ağ | **myVNet** |
    | Alt ağ | **myBackendSubnet** |
    | Genel IP | **Hiçbiri** seçeneğini belirtin. |
    | NIC ağ güvenlik grubu | **Gelişmiş** seçin|
    | Ağ güvenlik grubunu yapılandırma | **Yeni oluştur**’u seçin. </br> **Ağ güvenlik grubu oluştur**' da, **ad** alanına **mynsg** yazın. </br> **Gelen kuralları** içinde **+ gelen kuralı ekle**' yi seçin. </br> **Hizmet** altında **http**' yi seçin. </br> **Öncelik** alanına **100** yazın. </br> **Ad** alanına **Myhttprule** yazın </br> **Ekle**’yi seçin </br> **Tamam**'ı seçin |
    | **Yük dengeleme**  |
    | Bu sanal makine, var olan bir yük dengeleme çözümünün arkasına mi yerleştirsin? | Onay kutusunu işaretleyin.|
    | **Yük Dengeleme ayarları** |
    | Yük dengeleme seçenekleri | **Azure Yük dengeleyiciyi** seçin |
    | Yük dengeleyici seçin | **Myloadbalancer** seçin  |
    | Bir arka uç havuzu seçin | **Mybackendpool** seçin |
   
5. **Gözden geçir ve oluştur**’u seçin. 
  
6. Ayarları gözden geçirin ve ardından **Oluştur**' u seçin.

7. Aşağıdaki değerleri ve **myVM1** ile aynı diğer tüm ayarları IÇEREN bir VM oluşturmak için 1 ile 6 arasındaki adımları uygulayın:

    | Ayar | VM 2 |
    | ------- | ----- |
    | Name |  **myVM2** |
    | Kullanılabilirlik alanı | **2** |
    | Ağ güvenlik grubu | Mevcut **Mynsg** 'yi seçin| 

## <a name="install-iis"></a>IIS yükleme

1. Sol taraftaki menüden **tüm hizmetler** ' i seçin, **tüm kaynaklar**' ı seçin ve ardından kaynaklar listesinden, **tutorpublbıp-RG** kaynak grubunda bulunan **myVM1** ' yi seçin.

2. **Genel bakış** sayfasında **Bağlan** **' ı ve** sonra da ' yi seçin.

3. **Kullanıma** alma düğmesini seçin.

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
    Remove-Item C:\inetpub\wwwroot\iisstart.htm
    
    # Add a new htm file that displays server name
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
   ```
8. **MyVM1** ile savunma oturumunu kapatın.

9. **MyVM2**' ye IIS ve güncelleştirilmiş iisstart.htm dosyasını yüklemek için 1 ile 7 arasındaki adımları yineleyin.

## <a name="test-the-load-balancer"></a>Yük dengeleyiciyi test etme

1. **Genel Bakış** ekranında yük dengeleyici için genel IP adresini bulun. Sol taraftaki menüden **tüm hizmetler** ' i seçin, **tüm kaynaklar**' ı seçin ve ardından **mypublicıp-lb**' ı seçin.

2. Genel IP adresini kopyalayıp tarayıcınızın adres çubuğuna yapıştırın. IIS Web sunucusunun varsayılan sayfası, tarayıcıda görüntülenir.

   ![IIS Web sunucusu](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

Yük dengeleyiciyi trafiği myVM2 'e dağıtmalarını görmek için Web tarayıcınızı istemci makineden yenilemeyi zorlayın.
## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu uygulamayı kullanmaya devam edemeyecekiz, sanal ağ, sanal makine ve NAT ağ geçidini aşağıdaki adımlarla silin:

1. Sol taraftaki menüden **kaynak grupları**' nı seçin.

2. **Tutorpublbıp-RG** kaynak grubunu seçin.

3. **Kaynak grubunu sil**'i seçin.

4. **Tutorpublbıp-RG** girin ve **Sil**' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide şunları yapabilirsiniz:

* Sanal ağ oluşturuldu
* Bir NAT ağ geçidi oluşturuldu
* IP tabanlı arka uç havuzu ile yük dengeleyici oluşturuldu
* Yük dengeleyici test edildi

Çapraz bölge yük dengeleyici oluşturma hakkında bilgi edinmek için sonraki makaleye ilerleyin:
> [!div class="nextstepaction"]
> [Azure portal kullanarak bir çapraz bölge Azure Load Balancer oluşturma](tutorial-cross-region-portal.md)
