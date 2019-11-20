---
title: "Öğretici: kullanılabilirlik alanları genelinde VM 'Leri yük dengelemesi-Azure portal"
titlesuffix: Azure Load Balancer
description: Bu öğretici, Azure portalı kullanarak kullanılabilirlik alanları arasındaki sanal makinelerin yükünü dengelemek üzere alanlar arası yedekli ön uç ile Standart Load Balancer oluşturma işlemini gösterir
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
Customer intent: As an IT administrator, I want to create a load balancer that load balances incoming internet traffic to virtual machines across availability zones in a region, so that the customers can still access the web service if a datacenter is unavailable.
ms.service: load-balancer
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/27/2019
ms.author: allensu
ms.custom: seodec18
ms.openlocfilehash: 2079ec95b582ed724583112f7af4865af66eef9d
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74048863"
---
# <a name="tutorial-load-balance-vms-across-availability-zones-with-a-standard-load-balancer-using-the-azure-portal"></a>Öğretici: Azure portalını kullanarak Standart Yük Dengeleyici ile kullanılabilirlik alanları arasındaki sanal makinelerde yük dengeleme

Yük dengeleme, gelen istekleri birden çok sanal makineye dağıtarak yüksek düzeyde kullanılabilirlik sunar. Bu öğreticide, kullanılabilirlik alanları arasında VM 'Lerin yükünü dengeleyen bir genel Standart Load Balancer oluşturma adımları sağlanır. Bu, uygulamalarınızı beklenmeyen hatalardan veya tüm veri merkezinin kaybedilmesinden korumaya yardımcı olur. Bölgesel olarak yedeklilik sayesinde bir veya daha fazla kullanılabilirlik alanı başarısız olurken bölgedeki bir alan sağlıklı kaldıkça veri yolu etkin olmaya devam eder. Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:

> [!div class="checklist"]
> * Standart Yük Dengeleyici oluşturma
> * Gelen trafik kurallarını tanımlamak için ağ güvenlik grupları oluşturma
> * Kullanılabilirlik alanları arasında yedekli VM'ler oluşturma ve bir yük dengeleyiciye ekleme
> * Yük dengeleyici durum yoklaması oluşturma
> * Yük dengeleyici trafik kuralları oluşturma
> * Temel IIS sitesi oluşturma
> * Çalışan yük dengeleyiciyi görüntüleme

Standart Yük Dengeleyici ile Kullanılabilirlik alanlarını kullanma hakkında daha fazla bilgi için [Standart Yük Dengeleyici ve Kullanılabilirlik Alanları](load-balancer-standard-availability-zones.md).

Tercih ederseniz, [Azure CLI](load-balancer-standard-public-zone-redundant-cli.md) kullanarak bu öğreticiyi tamamlayabilirsiniz.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun. 

## <a name="sign-in-to-azure"></a>Azure'da oturum açma

[https://portal.azure.com](https://portal.azure.com) adresinden Azure portalında oturum açın.

## <a name="create-a-standard-load-balancer"></a>Standart Yük Dengeleyici oluşturma

Standart Yük Dengeleyici yalnızca Standart Genel IP adresini destekler. Yük dengeleyiciyi oluştururken yeni bir genel IP oluşturduğunuzda, bir Standart SKU sürümü halinde otomatik olarak yapılandırılır ve ayrıca otomatik olarak alanlar arası yedeklidir.

1. Ekranın sol üst kısmında **Kaynak oluştur** > **Ağ** > **Yük Dengeleyici** seçeneklerine tıklayın.
2. **Yük dengeleyici oluştur** sayfasının **temel bilgiler** sekmesinde aşağıdaki bilgileri girin veya seçin, kalan ayarlar için varsayılan değerleri kabul edin ve ardından **gözden geçir + oluştur**' u seçin:

    | Ayar                 | Değer                                              |
    | ---                     | ---                                                |
    | Abonelik               | Aboneliğinizi seçin.    |    
    | Kaynak grubu         | **Yeni oluştur** ' u seçin ve metin kutusuna *MyResourceGroupLBAZ* yazın.|
    | Ad                   | *myLoadBalancer*                                   |
    | Bölge         | **Batı Avrupa**'yı seçin.                                        |
    | Tür          | **Ortak**seçeneğini belirleyin.                                        |
    | SKU           | **Standart**' ı seçin.                          |
    | Genel IP adresi | **Yeni oluştur**’u seçin. |
    | Genel IP adresi adı              | Metin kutusuna *Mypublicıp* yazın.   |
    |Kullanılabilirlik alanı| **Bölge yedekli**seçeneğini belirleyin.    |
   

## <a name="create-backend-servers"></a>Arka uç sunucular oluşturma

Bu bölümde bir sanal ağ, bölgenin farklı alanlarında sanal makineler oluşturacak ve sonra alanlar arası yedekli yük dengeleyiciyi test etmeye yardımcı olmak üzere sanal makinelere IIS yükleyeceksiniz. Bu nedenle, bir alan başarısız olursa aynı alandaki VM’nin sistem durumu yoklaması başarısız olur ve trafik diğer alanlardaki VM’ler tarafından sunulmaya devam eder.

### <a name="create-a-virtual-network"></a>Sanal ağ oluşturma
Arka uç sunucularınızı dağıtmak için bir sanal ağ oluşturun.

1. Ekranın sol üst kısmında **Kaynak oluştur** > **Ağ** > **Sanal ağ**'e tıklayın ve sanal ağ için şu değerleri girin:
    - Sanal ağın adı için *myVnet*.
    - *myResourceGroupLBAZ* - Mevcut kaynak grubunun adı
    - Alt ağ adı için *myBackendSubnet*.
2. Sanal ağı oluşturmak için **Oluştur**’a tıklayın.

    ![Sanal ağ oluşturma](./media/load-balancer-standard-public-availability-zones-portal/2-load-balancer-virtual-network.png)

## <a name="create-a-network-security-group"></a>Ağ güvenlik grubu oluşturma

Sanal ağınıza gelen bağlantıları tanımlamak için ağ güvenlik grubu oluşturun.

1. Ekranın sol üst kısmında **Kaynak oluştur**’a tıklayın, arama kutusuna *Ağ Güvenlik Grubu* yazın ve ağ güvenlik grubu sayfasında **Oluştur**’a tıklayın.
2. Ağ güvenlik grubu oluşturun sayfasında şu değerleri girin:
    - *myNetworkSecurityGroup*  - Ağ güvenlik grubunun adı.
    - *myResourceGroupLBAZ* - Mevcut kaynak grubunun adı.
   
![Sanal ağ oluşturma](./media/load-balancer-standard-public-availability-zones-portal/create-nsg.png)

### <a name="create-network-security-group-rules"></a>Ağ güvenlik grubu kuralları oluşturma

Bu bölümde, Azure portalı ile HTTP ve RDP kullanarak gelen bağlantılara izin vermek için ağ güvenlik grubu kuralları oluşturacaksınız.

1. Azure portalında, sol menüden **Tüm kaynaklar**’a tıklayın ve **myResourceGroupLBAZ** kaynak grubunda bulunan **myNetworkSecurityGroup** öğesini bulup tıklayın.
2. **Ayarlar**’ın altında **Gelen güvenlik kuralları**’na ve sonra **Ekle**’ye tıklayın.
3. 80 numaralı bağlantı noktasını kullanarak gelen HTTP bağlantılarına izin vermek için *myHTTPRule* adlı gelen güvenlik kuralı için şu değerleri girin:
    - *Kaynak* için **Hizmet Etiketi**.
    - *Kaynak hizmet etiketi* için **İnternet**
    - *Hedef bağlantı noktası aralıkları* için **80**
    - *Protokol* için **TCP**
    - *Eylem* için **İzin Ver**
    - *Öncelik* için **100**
    - *myHTTPRule* - Yük dengeleyici kuralının adı.
    - *HTTP’ye izin ver* - Yük dengeleyici kuralının açıklaması.
4. **OK (Tamam)** düğmesine tıklayın.
 
   ![Sanal ağ oluşturma](./media/load-balancer-standard-public-availability-zones-portal/8-load-balancer-nsg-rules.png)
5. 2 - 4 arası adımları yineleyerek, aşağıdaki değerlerle 3389 numaralı bağlantı noktasını kullanarak gelen RDP bağlantısına izin vermek için *myRDPRule* adlı başka bir kural oluşturun:
    - *Kaynak* için **Hizmet Etiketi**.
    - *Kaynak hizmet etiketi* için **İnternet**
    - *Hedef bağlantı noktası aralıkları* için **3389**
    - *Protokol* için **TCP**
    - *Eylem* için **İzin Ver**
    - *Öncelik* için **200**
    - Ad için *myRDPRule*
    - Açıklama için *RDP’ye İzin Ver*

### <a name="create-virtual-machines"></a>Sanal makineler oluşturma

Yük dengeleyicinin arka uç sunucuları olarak hareket edebilen bölge için farklı alanlarda (bölge 1, bölge 2 ve bölge 3) sanal makineler oluşturun.

1. Ekranın sol üst kısmında **Kaynak oluştur** > **İşlem** > **Windows Server 2016 Datacenter**'a tıklayın ve sanal makine için şu değerleri girin:
    - Sanal makinenin adı için *myVM1*.        
    - Yönetici kullanıcı adı için *azureuser*.    
    - *myResourceGroupLBAZ* - **Kaynak grubu** için **Var olanı kullan**’ı seçin ve sonra *myResourceGroupLBAZ* seçeneğini belirleyin.
2. **OK (Tamam)** düğmesine tıklayın.
3. Sanal makinenin boyutu için **DS1_V2** seçeneğini belirleyin ve **Seç**’e tıklayın.
4. Sanal makine ayarları için şu değerleri girin:
    - *bölge 1* - VM’yi yerleştirdiğiniz bölge.
    -  Sanal makine olarak *myVNet*'in seçildiğinden emin olun.
    - Alt ağ olarak *myBackendSubnet* öğesinin seçildiğinden emin olun.
    - *myNetworkSecurityGroup* - Ağ güvenlik grubunun (güvenlik duvarı) adı.
5. Önyükleme tanılamalarını devre dışı bırakmak için **Devre Dışı** seçeneğine tıklayın.
6. **Tamam**’a tıklayın, özet sayfasındaki ayarları gözden geçirin ve sonra **Oluştur**’a tıklayın.
  
   ![Sanal makine oluşturma](./media/load-balancer-standard-public-availability-zones-portal/create-vm-standard-ip.png)

7. Adım 1-6’yı kullanarak, sanal ağ *myVnet*, alt ağ *myBackendSubnet* ve **myNetworkSecurityGroup* ağ güvenlik grubu olacak şekilde Bölge 2’de *VM2* adlı ikinci bir VM, Bölge 3’te üçüncü bir VM oluşturun.

### <a name="install-iis-on-vms"></a>VM’lere IIS yükleme

1. Sol menüden **Tüm kaynaklar**’a tıklayın ve kaynak listesinden, **myResourceGroupLBAZ** kaynak grubunda bulunan *myVM1* öğesine tıklayın.
2. Sanal makineye yönelik RDP için **Genel Bakış** sayfasında **Bağlan**’a tıklatın.
3. *azureuser* kullanıcı adıyla sanal makinede oturum açın.
4. Sunucu masaüstünde **Windows Yönetimsel Araçları**>**Windows PowerShell** bölümüne gidin.
5. PowerShell Penceresinde aşağıdaki komutları çalıştırarak IIS sunucusunu yükleyin, varsayılan iisstart.htm dosyasını kaldırın ve ardından VM’nin adını gösteren yeni bir iisstart.htm dosyasını ekleyin:
   ```azurepowershell-interactive
    
    # install IIS server role
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    # Add a new htm file that displays server name
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from" + $env:computername)
   ```
6. *myVM1* ile RDP oturumunu kapatın.
7. IIS’yi ve *myVM2* ve *myVM3*’teki güncelleştirilmiş iisstart.htm dosyasını yüklemek için 1 ile 6 arasındaki adımları tekrarlayın.

## <a name="create-load-balancer-resources"></a>Yük dengeleyici kaynakları oluşturma

Bu bölümde, arka uç adres havuzu ve durum araştırması için yük dengeleyici ayarlarını yapılandıracak ve yük dengeleyici ve NAT kurallarını belirteceksiniz.


### <a name="create-a-backend-address-pool"></a>Arka uç adres havuzu oluşturma

Trafiği VM’lere dağıtmak için, bir arka uç adres havuzunda yük dengeleyiciye bağlı sanal NIC’lerin IP adresleri barındırılır. *VM1*, *VM2* ve *VM3* öğesini içerecek şekilde *myBackendPool* arka uç havuzunu oluşturun.

1. Sol menüden **Tüm kaynaklar**’a tıklayın ve sonra kaynak listesinden **myLoadBalancer** seçeneğine tıklayın.
2. **Ayarlar** bölümünde **Arka uç havuzları**’na ve sonra **Ekle**’ye tıklayın.
3. **Arka uç havuzu ekle** sayfasında aşağıdakileri yapın:
    - Ad için, arka uç havuzunuzun adı olarak *myBackEndPool* yazın.
    - **Sanal ağ** için açılır menüde **myVNet** öğesine tıklayın
    - **Sanal makine** için açılır menüde **myVM1** öğesine tıklayın.
    - **IP adresi** için açılır menüde myVM1’in IP adresine tıklayın.
4. Yük dengeleyicinin arka uç havuzuna eklenecek her bir sanal makineyi (**myVM2** ve *myVM3*) eklemek için *Yeni arka uç kaynağı ekle*’ye tıklayın.
5. **Ekle**'ye tıklayın.

    ![Arka uç adres havuzuna ekleme -](./media/load-balancer-standard-public-availability-zones-portal/add-backend-pool.png)

3. Yük dengeleyici arka uç havuzu ayarınızın üç VM’yi de gösterdiğinden emin olun: **myVM1**, **myVM2** ve **myVM3**.

### <a name="create-a-health-probe"></a>Durum araştırması oluşturma

Yük dengeleyicinin uygulamanızın durumunu izlemesine izin vermek için durum araştırması kullanabilirsiniz. Durum yoklaması, durum denetimlerine verdikleri yanıtlara göre VM’leri dinamik olarak yük dengeleyici rotasyonuna ekler ve kaldırır. Sanal makinelerin durumunu izlemek için *myHealthProbe* durum araştırması oluşturun.

1. Sol menüden **Tüm kaynaklar**’a tıklayın ve sonra kaynak listesinden **myLoadBalancer** seçeneğine tıklayın.
2. **Ayarlar** bölümünde **Durum araştırmaları**’na ve sonra **Ekle**’ye tıklayın.
3. Durum araştırması oluşturmak için şu değerleri kullanın:
    - Durum araştırmasının adı için *myHealthProbe*.
    - Protokol türü için **HTTP**.
    - Bağlantı noktası numarası için *80*.
    - Araştırma denemeleri arasındaki saniye cinsinden *Aralık* için **15**.
    - Bir sanal makinenin sağlıksız olduğu kanısına varılmadan önce gerçekleşmesi gereken ardışık araştırma hatası veya *Sağlıksız eşik* sayısı için **2**.
4. **OK (Tamam)** düğmesine tıklayın.

   ![Araştırma ekleme](./media/load-balancer-standard-public-availability-zones-portal/4-load-balancer-probes.png)

### <a name="create-a-load-balancer-rule"></a>Yük dengeleyici kuralı oluşturma

Trafiğin VM’lere dağıtımını tanımlamak için bir yük dengeleyici kuralı kullanılır. Gerekli kaynak ve hedef bağlantı noktalarının yanı sıra gelen trafik için ön uç IP yapılandırması ve trafiği almak için arka uç IP havuzu tanımlamanız gerekir. *FrontendLoadBalancer* ön uç havuzunda 80 numaralı bağlantı noktasını dinlemek ve yine 80 numaralı bağlantı noktasını kullanarak *myBackEndPool* arka uç adres havuzuna yük dengelemesi yapılmış ağ trafiğini göndermek için *myLoadBalancerRuleWeb* yük dengeleyici kuralını oluşturun. 

1. Sol menüden **Tüm kaynaklar**’a tıklayın ve sonra kaynak listesinden **myLoadBalancer** seçeneğine tıklayın.
2. **Ayarlar** bölümünde **Yük dengeleme kuralları**’na ve sonra **Ekle**’ye tıklayın.
3. Yük dengeleme kuralını yapılandırmak için şu değerleri kullanın:
    - Yük dengeleme kuralının adı için *myHTTPRule*.
    - Protokol türü için **TCP**.
    - Bağlantı noktası numarası için *80*.
    - Arka uç bağlantı noktası için *80*.
    - Arka uç havuzunun adı için *myBackendPool*.
    - Durum araştırmasının adı için *myHealthProbe*.
4. **OK (Tamam)** düğmesine tıklayın.
    
    ![Yük dengeleme kuralı ekleme](./media/load-balancer-standard-public-availability-zones-portal/load-balancing-rule.png)

## <a name="test-the-load-balancer"></a>Yük dengeleyiciyi test etme
1. **Genel Bakış** ekranında Yük Dengeleyici için genel IP adresini bulun. **Tüm kaynaklar**’a ve sonra **myPublicIP** seçeneğine tıklayın.

2. Genel IP adresini kopyalayıp tarayıcınızın adres çubuğuna yapıştırın. IIS Web sunucusunun varsayılan sayfası, tarayıcıda görüntülenir.

      ![IIS Web sunucusu](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

Yük dengeleyicinin trafiği bölgeye dağıtılmış VM’ler arasında dağıttığını görmek için web tarayıcınızı yenilemeye zorlayabilirsiniz.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli olmadığında kaynak grubunu, yük dengeleyiciyi ve tüm ilgili kaynakları silin. Bunu yapmak için, yük dengeleyiciyi içeren kaynak grubunu seçin ve **Sil**’e tıklayın.

## <a name="next-steps"></a>Sonraki adımlar

[Standart Yük Dengeleyici](load-balancer-standard-overview.md) hakkında daha fazla bilgi edinin.
