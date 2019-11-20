---
title: "Öğretici: Bir bölge içinde--Azure portalında dengeleyici VM'ler yük"
titlesuffix: Azure Load Balancer
description: Bu öğretici, Azure portalı kullanarak bir kullanılabilirlik alanı içindeki sanal makinelerin yükünü dengelemek üzere bölge ön ucu ile Standard Load Balancer oluşturma işlemini gösterir
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
Customer intent: As an IT administrator, I want to create a load balancer that load balances incoming internet traffic to virtual machines within a specific zone in a region.
ms.service: load-balancer
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/27/2019
ms.author: allensu
ms.custom: seodec18
ms.openlocfilehash: 0ec9fae1ce4ef976d5f50e1d8d8412354706c5f8
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68273395"
---
# <a name="tutorial-load-balance-vms-within-an-availability-zone-with-standard-load-balancer-by-using-the-azure-portal"></a>Öğretici: Azure portalını kullanarak bir Standard Load Balancer ile kullanılabilirlik alanı içinde Yük Dengeleme sanal makineleri

Bu öğreticide Azure portal kullanılarak genel IP standart adresine sahip bir bölge ön ucu ile genel bir [Azure Standard Load Balancer](https://aka.ms/azureloadbalancerstandard) oluşturulur. Bu senaryoda, veri yolunuzu ve kaynaklarınızı belirli bir bölge ile hizalamak amacıyla ön uç ve arka uç örnekleriniz için belirli bir bölge belirtirsiniz. Aşağıdaki işlevleri gerçekleştirmeyi öğrenirsiniz:

> [!div class="checklist"]
> * Bir bölge ön ucu ile Standard Load Balancer örneği oluşturma.
> * Gelen trafik kurallarını tanımlamak için ağ güvenlik grupları oluşturma.
> * Bölgesel sanal makineler (VM) oluşturma ve yük dengeleyiciye ekleme.
> * Yük dengeleyici durum araştırması oluşturma.
> * Yük dengeleyici trafik kuralları oluşturma.
> * Temel bir Internet Information Services (IIS) sitesi oluşturma.
> * Çalışan yük dengeleyiciyi görüntüleme.

Standard Load Balancer ile kullanılabilirlik alanlarını kullanma hakkında daha fazla bilgi için [Standard Load Balancer ve Kullanılabilirlik Alanları](load-balancer-standard-availability-zones.md).

İsterseniz bu öğreticiyi tamamlamak için [Azure CLI](load-balancer-standard-public-zonal-cli.md) kullanabilirsiniz.

## <a name="sign-in-to-azure"></a>Azure'da oturum açma

[https://portal.azure.com](https://portal.azure.com) adresinden Azure portalında oturum açın.

## <a name="create-a-public-standard-load-balancer-instance"></a>Genel bir Standard Load Balancer örneği oluşturma

Standard Load Balancer yalnızca standart genel IP adresini destekler. Yük dengeleyiciyi oluştururken yeni bir genel IP oluşturduğunuzda, bir Standart SKU sürümü halinde otomatik olarak yapılandırılır. Ayrıca otomatik olarak bölgesel olarak yedeklidir.

1. Ekranın sol üst kısmında **Kaynak oluştur** > **Ağ** > **Yük Dengeleyici** seçeneğini belirleyin.
2. İçinde **Temelleri** sekmesinde **yük dengeleyici Oluştur** sayfasında, girin veya aşağıdaki bilgileri seçin, geri kalan ayarlar için varsayılan değerleri kabul edin ve ardından **gözden geçir +Oluştur**:

    | Ayar                 | Value                                              |
    | ---                     | ---                                                |
    | Subscription               | Aboneliğinizi seçin.    |    
    | Resource group         | Seçin **Yeni Oluştur** ve türü *MyResourceGroupZLB* metin kutusuna.|
    | Ad                   | *myLoadBalancer*                                   |
    | Bölge         | **Batı Avrupa**'yı seçin.                                        |
    | Type          | Seçin **genel**.                                        |
    | SKU           | Seçin **standart**.                          |
    | Genel IP adresi | **Yeni oluştur**’u seçin. |
    | Genel IP adresi adı              | Tür *Mypublicıp* metin kutusuna.   |
    |Kullanılabilirlik alanı| **1**'i seçin.    |
3. İçinde **gözden geçir + Oluştur** sekmesinde **Oluştur**.   

   ## <a name="create-backend-servers"></a>Arka uç sunucular oluşturma

Bu bölümde, bir sanal ağ oluşturursunuz. Ayrıca, yük dengeleyicinizin arka uç havuzuna eklenecek bölge için aynı bölgede (bölge 1) iki sanal makine oluşturursunuz. Ardından alanlar arası yedekli yük dengeleyicinin test edilmesine yardımcı olması için sanal makinelere IIS yükleyeceksiniz. Bir VM başarısız olursa aynı bölgedeki VM için sistem durumu araştırması başarısız olur. Trafik aynı bölgedeki diğer VM’ler tarafından sunulmaya devam eder.

### <a name="create-a-virtual-network"></a>Sanal ağ oluşturma
1. Ekranın sol üst kısmında **Kaynak oluştur** > **Ağ** > **Sanal ağ** seçeneğini belirleyin.  Sanal ağ için şu değerleri girin:
    - Sanal ağın adı olarak **myVNet**.
    - Mevcut kaynak grubunun adı olarak **myResourceGroupZLB**.
    - Alt ağ adı olarak **myBackendSubnet**.
2. Sanal ağı oluşturmak için **Oluştur**’u seçin.

    ![Sanal ağ oluşturma](./media/tutorial-load-balancer-standard-zonal-portal/create-virtual-network.png)

## <a name="create-a-network-security-group"></a>Ağ güvenlik grubu oluşturma

1. Ekranın sol üst kısmında **Kaynak oluştur** seçeneğini belirleyin. Arama kutusuna **Ağ Güvenlik Grubu** yazın. Ağ güvenlik grubu sayfasında **Oluştur**’u seçin.
2. **Ağ güvenlik grubu oluşturma** sayfasında şu değerleri girin:
   - Ağ güvenlik grubunun adı olarak **myNetworkSecurityGroup**.
   - Mevcut kaynak grubunun adı olarak **myResourceGroupLBAZ**.
   
     ![Ağ güvenlik grubu oluşturma](./media/tutorial-load-balancer-standard-zonal-portal/create-network-security-group.png)

### <a name="create-nsg-rules"></a>NSG kuralları oluşturma

Bu bölümde, Azure portalı ile HTTP ve Microsoft Uzak Masaüstü Protokolü (RDP) kullanarak gelen bağlantılara izin vermek için NSG kuralları oluşturacaksınız.

1. Azure Portal'da sol taraftaki menüden **Tüm kaynaklar**’ı seçin. Ardından **myNetworkSecurityGroup** değerini arayın ve seçin. **myResourceGroupZLB** kaynak grubunda bulunur.
2. **Ayarlar** altında **Gelen güvenlik kuralları**’nı seçin. Ardından **Ekle**'yi seçin.
3. 80 numaralı bağlantı noktasını kullanan gelen HTTP bağlantılarına izin vermek için **myHTTPRule** adlı gelen güvenlik kuralı için şu değerleri girin:
    - **Kaynak** için **Hizmet Etiketi**.
    - **Kaynak hizmet etiketi** için **İnternet**.
    - **Hedef bağlantı noktası aralıkları** için **80**.
    - **Protokol** için **TCP**.
    - **Eylem** için **İzin Ver**.
    - **Öncelik** için **100**.
    - **Ad** için **myHTTPRule**.
    - **Açıklama** için **HTTP’ye İzin Ver**.
4. **Tamam**’ı seçin.
 
   ![NSG kuralları oluşturma](./media/load-balancer-standard-public-availability-zones-portal/8-load-balancer-nsg-rules.png)

5. **myRDPRule** adlı başka bir kural oluşturmak için 2 ile 4 arasındaki adımları yineleyin. Bu kural, aşağıdaki değerlerle, 3389 numaralı bağlantı noktasını kullanan bir gelen RDP bağlantına izin verir:
    - **Kaynak** için **Hizmet Etiketi**.
    - **Kaynak hizmet etiketi** için **İnternet**.
    - **Hedef bağlantı noktası aralıkları** için **3389**.
    - **Protokol** için **TCP**.
    - **Eylem** için **İzin Ver**.
    - **Öncelik** için **200**.
    - **Ad** için **myRDPRule**.
    - **Açıklama** için **RDP’ye İzin Ver**.

      ![RDP kuralı oluşturma](./media/tutorial-load-balancer-standard-zonal-portal/create-rdp-rule.png)

### <a name="create-virtual-machines"></a>Sanal makineler oluşturma

1. Ekranın sol üst kısmında **Kaynak oluştur** > **İşlem** > **Windows Server 2016 Datacenter** seçeneğini belirleyin. Sanal makine için şu değerleri girin:
    - Sanal makinenin adı için **myVM1**.        
    - Yönetici kullanıcı adı için **azureuser**.    
    - **Kaynak grubu** için **myResourceGroupLB**. **Mevcut olanı kullan**’ı seçin ve **myResourceGroup** seçeneğini belirleyin.
2. **Tamam**’ı seçin.
3. Sanal makinenin boyutu için **DS1_V2** seçeneğini belirleyin. **Seç**’i seçin.
4. Sanal makine ayarları için şu değerleri girin:
    - VM’yi yerleştirdiğiniz Kullanılabilirlik alanı olarak **bölge 1**.
    -  **myVNet**. Sanal makine olarak bu değerin seçildiğinden emin olun.
    - Oluşturduğunuz standart genel IP adresi olarak **myVM1PIP**. **Yeni oluştur**’u seçin. Ardından ad türü olarak **myVM1PIP** değerini seçin. **Bölge** olarak **1**‘i seçin. IP adresi SKU’su varsayılan olarak standarttır.
    - **myBackendSubnet**. Alt ağ olarak seçili olduğundan emin olun.
    - Zaten mevcut olan ağ güvenlik grubunun (güvenlik duvarı) adı olarak **myNetworkSecurityGroup**.
5. Önyükleme tanılamalarını devre dışı bırakmak için **Devre Dışı** seçeneğini belirleyin.
6. **Tamam**’ı seçin. Özet sayfasında ayarları gözden geçirin. Ardından **Oluştur**’u seçin.
7. Bölge 1’de **myVM2** adlı ikinci bir VM oluşturmak için 1-6 arası adımları tekrarlayın. **myVnet**’i sanal ağ yapın. **myVM2PIP**’yi standart genel IP adresi yapın. **myBackendSubnet**’i alt ağ yapın. **myNetworkSecurityGroup**’u ağ güvenlik grubu yapın.

    ![Sanal makineler oluşturma](./media/tutorial-load-balancer-standard-zonal-portal/create-virtual-machine.png) 

### <a name="install-iis-on-vms"></a>VM’lere IIS yükleme

1. Sol taraftaki menüden **Tüm kaynaklar**’ı seçin. Kaynaklar listesinden **myVM1**’i seçin. **myResourceGroupZLB** kaynak grubunda bulunur.
2. VM’ye ulaşırken RDP kullanmak için **Genel Bakış** sayfasında **Bağlan**’ı seçin.
3. VM oluştururken yapılandırdığınız kullanıcı adı ve parolayla VM’de oturum açın. Sanal makineyi oluştururken girdiğiniz kimlik bilgilerini belirtmek için **Diğer seçenekler**’i seçmeniz gerekebilir. Ardından **farklı bir hesap kullan**’ı seçin. Sonrasında **Tamam**’ı seçin. Oturum açma işlemi sırasında bir sertifika uyarısı alabilirsiniz. Bağlantıya devam etmek için **Evet**’i seçin.
4. Sunucu masaüstünde **Windows Yönetimsel Araçları** > **Windows PowerShell** bölümüne gidin.
6. **PowerShell** penceresinde IIS sunucusunu yüklemek için aşağıdaki komutları çalıştırın. Bu komutlar ayrıca varsayılan iisstart.htm dosyasını kaldırır ve ardından VM’nin adını gösteren yeni bir iisstart.htm dosyasını ekler:

   ```azurepowershell-interactive
    # install IIS server role
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    # remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    # Add a new htm file that displays server name
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from" + $env:computername)
   ```
7. **myVM1** ile RDP oturumunu kapatın.
8. **myVM2**’ye IIS yüklemek için 1 ile 7 arasındaki adımları tekrarlayın.

## <a name="create-load-balancer-resources"></a>Yük dengeleyici kaynakları oluşturma

Bu bölümde, arka uç adres havuzu ve durum araştırması için yük dengeleyici ayarlarını yapılandıracaksınız. Ayrıca yük dengeleyici ve ağ adresi çevirisi kuralları da belirtirsiniz.


### <a name="create-a-backend-address-pool"></a>Arka uç adres havuzu oluşturma

Trafiği sanal makinelere dağıtmak için bir arka uç adres havuzu, yük dengeleyiciye bağlı sanal ağ arabirimi kartlarının IP adreslerini içerir. **VM1** ve **VM2** öğesini eklemek için **myBackendPool** adlı arka uç adres havuzunu oluşturun.

1. Sol taraftaki menüden **Tüm kaynaklar**’ı seçin. Ardından kaynaklar listesinden **myLoadBalancer**’ı seçin.
2. **Ayarlar**’ın altında **Arka Uç Havuzları**’nı seçin. Ardından **Ekle**'yi seçin.
3. **Arka uç havuzu ekle** sayfasında aşağıdakileri yapın:
    - Ad için, arka uç havuzunuzun adı olarak **myBackEndPool** yazın.
    - **Sanal ağ** için açılır menüde **myVNet**’i seçin. 
    - **Sanal makine** ve **IP adresi** için **myVM1**, **myVM2** ve karşılık gelen genel IP adreslerini ekleyin.
4. **Add (Ekle)** seçeneğini belirleyin.
5. Yük dengeleyici arka uç havuzu ayarınızın hem **myVM1** hem de **myVM2** sanal makinelerini görüntülediğinden emin olun.
 
    ![Arka uç havuzu oluşturma](./media/tutorial-load-balancer-standard-zonal-portal/create-backend-pool.png) 

### <a name="create-a-health-probe"></a>Durum araştırması oluşturma

Yük dengeleyicinin uygulamanızın durumunu izlemesine izin vermek için sistem durumu araştırması kullanın. Durum yoklaması, durum denetimlerine verdikleri yanıtlara göre VM’leri dinamik olarak yük dengeleyici rotasyonuna ekler ve kaldırır. Sanal makinelerin durumunu izlemek için **myHealthProbe** durum araştırması oluşturun.

1. Sol taraftaki menüden **Tüm kaynaklar**’ı seçin. Ardından kaynaklar listesinden **myLoadBalancer**’ı seçin.
2. **Ayarlar** bölümünde **Sistem durumu araştırmaları**’nı seçin. Ardından **Ekle**'yi seçin.
3. Durum araştırması oluşturmak için şu değerleri kullanın:
    - Durum araştırmasının adı olarak **myHealthProbe**.
    - Protokol türü için **HTTP**.
    - Bağlantı noktası numarası için **80**.
    - Araştırma denemeleri arasındaki saniye cinsinden **Aralık** için **15**.
    - Bir sanal makinenin sağlıksız olduğu kanısına varılmadan önce gerçekleşmesi gereken ardışık araştırma hatası veya **Sağlıksız eşik** sayısı için **2**.
4. **Tamam**’ı seçin.

   ![Durum araştırması ekleme](./media/load-balancer-standard-public-availability-zones-portal/4-load-balancer-probes.png)

### <a name="create-a-load-balancer-rule"></a>Yük dengeleyici kuralı oluşturma

Yük dengeleyici kuralı, trafiğin sanal makinelere nasıl dağıtıldığını belirler. Gerekli kaynak ve hedef bağlantı noktalarının yanı sıra gelen trafik için ön uç IP yapılandırması ve trafiği almak için arka uç IP havuzu tanımlamanız gerekir. **LoadBalancerFrontEnd** ön ucunda 80 numaralı bağlantı noktasını dinlemek için **myLoadBalancerRuleWeb** adlı bir yük dengeleyici kuralı oluşturun. Kural, 80 numaralı bağlantı noktasını kullanarak **myBackEndPool** arka uç adres havuzuna yük dengeli ağ trafiği göndermek için de geçerlidir. 

1. Sol taraftaki menüden **Tüm kaynaklar**’ı seçin. Ardından kaynaklar listesinden **myLoadBalancer**’ı seçin.
2. **Ayarlar** bölümünde **Yük dengeleme kuralları**’nı seçin. Ardından **Ekle**'yi seçin.
3. Yük dengeleme kuralını yapılandırmak için şu değerleri kullanın:
    - Yük dengeleme kuralının adı için **myHTTPRule**.
    - Protokol türü için **TCP**.
    - Bağlantı noktası numarası için **80**.
    - Arka uç bağlantı noktası için **80**.
    - Arka uç havuzunun adı için **myBackendPool**.
    - Durum araştırmasının adı olarak **myHealthProbe**.
4. **Tamam**’ı seçin.
    
    ![Yük dengeleme kuralı ekleme](./media/tutorial-load-balancer-standard-zonal-portal/load-balancing-rule.png)

## <a name="test-the-load-balancer"></a>Yük dengeleyiciyi test etme
1. **Genel Bakış** ekranında yük dengeleyici için genel IP adresini bulun. **Tüm kaynaklar**’ı seçin. Ardından **myPublicIP**’yi seçin. 

2. Genel IP adresini kopyalayın. Ardından tarayıcınızın adres çubuğuna yapıştırın. Web sunucusu sayfasının adını içeren varsayılan sayfa, tarayıcıda görüntülenir.

      ![IIS web sunucusu](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)
3. Yük dengeleyici çalışırken görmek için görüntülenen VM’yi durmaya zorlayın. Tarayıcıda görüntülenen diğer sunucu adını görmek için tarayıcıyı yenileyin.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli olmadığında kaynak grubunu, yük dengeleyiciyi ve tüm ilgili kaynakları silin. Yük dengeleyiciyi içeren kaynak grubunu seçin. Ardından **Sil**’i seçin.

## <a name="next-steps"></a>Sonraki adımlar

- [Standart Yük Dengeleyici](load-balancer-standard-overview.md) hakkında daha fazla bilgi edinin.
- [Farklı kullanılabilirlik bölgelerindeki VM’lerin yükünü dengeleme](tutorial-load-balancer-standard-public-zone-redundant-portal.md).
