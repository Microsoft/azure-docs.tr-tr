---
title: "Öğretici: İç yük dengeleyici - Azure portal'ı oluşturma"
titlesuffix: Azure Load Balancer
description: Bu öğretici, Azure portalını kullanarak iç temel yük dengeleyici oluşturma işlemini göstermektedir.
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
Customer intent: As an IT administrator, I want to create a load balancer that load balances incoming internal traffic to virtual machines within a specific zone in a region.
ms.service: load-balancer
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/27/2019
ms.author: allensu
ms.custom: seodec18
ms.openlocfilehash: 09a73b5d97e46797dd880f64383fdf03fd591f3d
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68273488"
---
# <a name="tutorial-balance-internal-traffic-load-with-a-basic-load-balancer-in-the-azure-portal"></a>Öğretici: Azure portalında bir temel yük dengeleyici ile iç trafik Yük Dengeleme

Yük Dengeleme, sanal makineye (VM) gelen istekleri yayarak daha yüksek düzeyde kullanılabilirlik ve ölçek sağlar. Basic load balancer oluşturma ve VM'ler arasında iç trafiği dengelemek için Azure portalını kullanabilirsiniz. Bu öğreticide oluşturma ve iç yük dengeleyici, arka uç sunucularının ve ağ kaynakları temel fiyatlandırma katmanında yapılandırma gösterilmektedir.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun. 

Tercih ederseniz, bu adımları kullanarak yapabileceğiniz [Azure CLI](load-balancer-get-started-ilb-arm-cli.md) veya [Azure PowerShell](load-balancer-get-started-ilb-arm-ps.md) portalı yerine.

Bu öğretici adımları yapmak için oturum açın Azure Portal'daki [ https://portal.azure.com ](https://portal.azure.com).

## <a name="create-a-vnet-back-end-servers-and-a-test-vm"></a>Bir sanal ağ, arka uç sunucularının ve bir test sanal makinesi oluşturma

İlk olarak bir sanal ağın (VNet) oluşturun. Sanal ağda Yük Dengeleyiciyi test etmek için kullanılacak temel yük dengeleyici ve üçüncü bir VM arka uç havuzu için kullanılacak iki VM oluşturun. 

### <a name="create-a-virtual-network"></a>Sanal ağ oluşturma

1. Portalda sol tarafta seçin **kaynak Oluştur** > **ağ** > **sanal ağ**.
   
1. İçinde **sanal ağ oluştur** bölmesinde yazın veya bu değerleri seçin:
   
   - **Ad**: Tür *MyVNet*.
   - **ResourceGroup**: Seçin **Yeni Oluştur**, enter *MyResourceGroupLB*seçip **Tamam**. 
   - **Alt ağ** > **adı**: Tür *MyBackendSubnet*.
   
1. **Oluştur**’u seçin.

   ![Sanal ağ oluşturma](./media/tutorial-load-balancer-basic-internal-portal/2-load-balancer-virtual-network.png)

### <a name="create-virtual-machines"></a>Sanal makineler oluşturma

1. Portalda sol tarafta seçin **kaynak Oluştur** > **işlem** > **Windows Server 2016 Datacenter**. 
   
1. İçinde **sanal makine oluşturma**yazın veya aşağıdaki değerleri seçin **Temelleri** sekmesinde:
   - **Abonelik** > **kaynak grubu**: Açılır listesine tıklayıp **MyResourceGroupLB**.
   - **Örnek ayrıntıları** > **sanal makine adı**: Tür *MyVM1*.
   - **Örnek ayrıntıları** > **kullanılabilirlik seçeneklerini**: 
     1. Açılır listesine tıklayıp **kullanılabilirlik kümesi**. 
     2. Seçin **Yeni Oluştur**, türü *MyAvailabilitySet*seçip **Tamam**.
   
1. Seçin **ağ** sekmesinde veya seçin **sonraki: Diskleri**, ardından **sonraki: Ağ**. 
   
   Aşağıdaki seçili olduğundan emin olun:
   - **Sanal ağ**: **MyVNet**
   - **Alt ağ**: **MyBackendSubnet**
   
   Altında **ağ güvenlik grubu**:
   1. **Gelişmiş**'i seçin. 
   1. Açılan menü **yapılandırma ağ güvenlik grubu** seçip **hiçbiri**. 
   
1. Seçin **Yönetim** sekmesinde veya seçin **sonraki** > **Yönetim**. Altında **izleme**ayarlayın **önyükleme tanılaması** için **kapalı**.
   
1. **İncele ve oluştur**’u seçin.
   
1. Ayarları gözden geçirin ve ardından **Oluştur**. 

1. Adlı ikinci bir VM oluşturmak için adımları *MyVM2*, diğer tüm ayarlarla MyVM1 ile aynı. 

1. Üçüncü bir adlı VM yeniden oluşturma adımlarını izleyin *MyTestVM*. 

## <a name="create-a-basic-load-balancer"></a>Temel yük dengeleyici oluşturma

Portalı kullanarak bir iç temel yük dengeleyici oluşturun. Oluşturduğunuz IP adresi ve adını, load balancer'ın ön ucu olarak otomatik olarak yapılandırılır.

1. Portalın sol üst kısmında **Kaynak oluştur** > **Ağ** > **Yük Dengeleyici** seçeneğini belirleyin.
   
2. İçinde **Temelleri** sekmesinde **yük dengeleyici Oluştur** sayfasında, girin veya aşağıdaki bilgileri seçin, geri kalan ayarlar için varsayılan değerleri kabul edin ve ardından **gözden geçir +Oluştur**:

    | Ayar                 | Değer                                              |
    | ---                     | ---                                                |
    | Subscription               | Aboneliğinizi seçin.    |    
    | Resource group         | Seçin **Yeni Oluştur** ve türü *MyResourceGroupLB* metin kutusuna.|
    | Ad                   | *myLoadBalancer*                                   |
    | Bölge         | **Batı Avrupa**'yı seçin.                                        |
    | Type          | Seçin **iç**.                                        |
    | SKU           | **Temel**'i seçin.                          |
    | Sanal ağ           | Seçin *MyVNet*.                          |    
    | IP adresi ataması              | Seçin **statik**.   |
    | Özel IP adresi|Örneğin sanal ağ ve alt ağ, adres alanında olan bir adres *10.3.0.7*.  |

3. İçinde **gözden geçir + Oluştur** sekmesinde **Oluştur**. 
   

## <a name="create-basic-load-balancer-resources"></a>Temel yük dengeleyici kaynakları oluşturma

Bu bölümde, bir arka uç adres havuzu ve durum araştırması için yük dengeleyici ayarlarını yapılandırın ve yük dengeleyici kuralları belirtin.

### <a name="create-a-back-end-address-pool"></a>Arka uç adres havuzu oluşturma

Trafiği Vm'lere dağıtmak için yük dengeleyici arka uç adres havuzu kullanır. Arka uç adres havuzundaki IP adreslerini yük dengeleyiciye bağlı sanal ağ arabirimlerini (NIC'ler) içerir. 

**VM1 ve VM2 içeren bir arka uç adres havuzu oluşturmak için:**

1. Seçin **tüm kaynakları** sol menüsünü ve ardından **MyLoadBalancer** kaynak listesinde.
   
1. **Ayarlar** bölümünde **Arka uç havuzları**’nı ve sonra **Ekle**’yi seçin.
   
1. Üzerinde **bir arka uç havuzu Ekle** sayfasında yazın veya aşağıdaki değerleri seçin:
   
   - **Ad**: Tür *MyBackendPool*.
   - **İlişkili**: Açılır listesine tıklayıp **kullanılabilirlik kümesi**.
   - **Kullanılabilirlik kümesi**: Seçin **MyAvailabilitySet**.
   
1. Seçin **hedef ağ IP Yapılandırması Ekle**. 
   1. Ekleme **MyVM1** ve **MyVM2** arka uç havuzuna.
   2. Her makine ekledikten sonra açılır listesine tıklayıp, **ağ IP yapılandırması**. 
   
   >[!NOTE]
   >Eklemeyin **MyTestVM** havuza. 
   
1. **Tamam**’ı seçin.
   
   ![Arka uç adres havuzu ekleme](./media/tutorial-load-balancer-basic-internal-portal/3-load-balancer-backend-02.png)
   
1. Üzerinde **arka uç havuzları** sayfasında **MyBackendPool** ve her ikisi de emin **VM1** ve **VM2** listelenir.

### <a name="create-a-health-probe"></a>Durum araştırması oluşturma

VM durumunu izlemek için yük dengeleyicisine izin vermek için durum araştırması kullanabilirsiniz. Durum yoklaması, durum denetimlerine verdikleri yanıtlara göre VM’leri dinamik olarak yük dengeleyici rotasyonuna ekler ve kaldırır. 

**Sanal makinelerin durumunu izlemek için bir durum araştırması oluşturmak için:**

1. Seçin **tüm kaynakları** sol menüsünü ve ardından **MyLoadBalancer** kaynak listesinde.
   
1. **Ayarlar** bölümünde **Durum araştırmaları**’nı ve sonra **Ekle**’yi seçin.
   
1. Üzerinde **bir durum araştırması Ekle** sayfasında yazın veya aşağıdaki değerleri seçin:
   
   - **Ad**: Tür *MyHealthProbe*.
   - **Protokol**: Açılır listesine tıklayıp **HTTP**. 
   - **Bağlantı noktası**: Tür *80*. 
   - **Yol**: Kabul */* için varsayılan bir URI. Diğer bir URI ile bu değeri değiştirebilirsiniz. 
   - **Aralığı**: Tür *15*. Araştırma denemeleri arasındaki saniye sayısını aralığıdır.
   - **Sağlıksız durum eşiği**: Tür *2*. Bu değer bir VM kötü olarak kabul edilmeden önce gerçekleşmesi ardışık araştırma hatası sayısıdır.
   
1. **Tamam**’ı seçin.
   
   ![Bir araştırma eklemek](./media/tutorial-load-balancer-basic-internal-portal/4-load-balancer-probes.png)

### <a name="create-a-load-balancer-rule"></a>Yük dengeleyici kuralı oluşturma

Yük dengeleyici kuralı, trafiğin sanal makinelere nasıl dağıtıldığını belirler. Ön uç IP yapılandırmasını gelen trafiğe, trafik ve gerekli kaynak ve hedef bağlantı noktalarını almak için arka uç IP havuzu için kuralı tanımlar. 

Adlı yük dengeleyici kuralı **MyLoadBalancerRule** ön 80 numaralı bağlantı noktasını dinler **LoadBalancerFrontEnd**. Kural ağ trafiği arka uç adres havuzuna gönderen **MyBackendPool**, bağlantı noktası 80'de. 

**Yük Dengeleyici kuralı oluşturmak için:**

1. Seçin **tüm kaynakları** sol menüsünü ve ardından **MyLoadBalancer** kaynak listesinde.
   
1. **Ayarlar** bölümünde **Yük dengeleme kuralları**’nı ve sonra **Ekle**’yi seçin.
   
1. Üzerinde **Yük Dengeleme Kuralı Ekle** sayfasında, yazın veya aşağıdaki değerleri seçin, zaten mevcut değilse:
   
   - **Ad**: Tür *MyLoadBalancerRule*.
   - **Ön uç IP adresi:** Tür *LoadBalancerFrontEnd* yoksa.
   - **Protokol**: Seçin **TCP**.
   - **Bağlantı noktası**: Tür *80*.
   - **Arka uç bağlantı noktası**: Tür *80*.
   - **Arka uç havuzu**: Seçin **MyBackendPool**.
   - **Durum araştırması**: Seçin **MyHealthProbe**. 
   
1. **Tamam**’ı seçin.
   
   ![Yük Dengeleyici Kuralı Ekle](./media/tutorial-load-balancer-basic-internal-portal/5-load-balancing-rules.png)

## <a name="test-the-load-balancer"></a>Yük dengeleyiciyi test etme

Internet Information Services (IIS) arka uç sunucularına yükleyin ve ardından MyTestVM özel IP adresini kullanarak Yük Dengeleyiciyi test etmek için kullanın. Her arka uç VM, yük dengeleyicinin istekler iki VM arasında dağıtmasını görebilmeniz için varsayılan IIS web sayfasına, farklı bir sürümü işlevi görür.

Portalında, üzerinde **genel bakış** sayfasındaki **MyLoadBalancer**, IP adresini bulmak **özel IP adresi**. Seçin ve adresi üzerine geldiğinizde **kopyalama** simgesini kopyalayın. Bu örnekte olduğu **10.3.0.7**. 

### <a name="connect-to-the-vms-with-rdp"></a>Vm'lere RDP ile bağlanma

İlk olarak, tüm üç VM ile Uzak Masaüstü (RDP) bağlanın. 

>[!NOTE]
>Vm'leri varsayılan olarak, zaten **RDP** (Uzak Masaüstü) bağlantı noktasını açın Uzak Masaüstü erişime izin vermek için. 

**Sanal makinelerde Uzak Masaüstü (RDP):**

1. Portalında **tüm kaynakları** sol menüsünde. Kaynak listesinden her bir sanal Makineye **MyResourceGroupLB** kaynak grubu.
   
1. Üzerinde **genel bakış** sayfasında **Connect**ve ardından **indirme RDP dosyası**. 
   
1. RDP dosyası indirilir ve seçin açık **Connect**.
   
1. Windows Güvenlik ekranında seçin **daha fazla seçenek** ardından **farklı bir hesap kullan**. 
   
   Kullanıcı adı ve parola girin ve ardından **Tamam**.
   
1. Yanıt **Evet** herhangi bir sertifika istemi. 
   
   VM masaüstüne yeni bir pencerede açılır. 

### <a name="install-iis-and-replace-the-default-iis-page-on-the-back-end-vms"></a>IIS yüklemek ve arka uç sanal makinelerin varsayılan IIS sayfasında değiştirin

Her arka uç sunucusunda, IIS yüklemek ve varsayılan IIS web sayfasına içeren özelleştirilmiş bir sayfa değiştirmek için PowerShell kullanın.

>[!NOTE]
>Ayrıca **Ekle roller ve Özellikler Sihirbazı** içinde **Sunucu Yöneticisi'ni** IIS yüklemek için. 

**IIS yüklemek ve varsayılan web sayfasını PowerShell ile güncelleştirmek için:**

1. MyVM1 ve MyVM2 başlatma **Windows PowerShell** gelen **Başlat** menüsü. 

2. IIS yüklemek ve varsayılan IIS web sayfasına değiştirmek için aşağıdaki komutları çalıştırın:
   
   ```powershell-interactive
    # Install IIS
      Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom htm file
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
    ```
1. RDP bağlantıları MyVM1 ve MyVM2 seçerek kapatmak **Bağlantıyı Kes**. Vm'lerini kapatmayın.

### <a name="test-the-load-balancer"></a>Yük dengeleyiciyi test etme

1. MyTestVM üzerinde açın **Internet Explorer**ve yanıt **Tamam** herhangi bir yapılandırma ister. 
   
1. Load balancer'ın özel IP adresini yazın veya yapıştırın (*10.3.0.7*) tarayıcının adres çubuğuna. 
   
   Özelleştirilmiş bir IIS web sunucusunun varsayılan sayfası, tarayıcıda görüntülenir. Ya da iletiyi okur **Hello World MyVM1 gelen**, veya **Hello World MyVM2 gelen**.
   
1. Yük dengeleyicinin trafiği VM'ye dağıtmasını görmek için tarayıcıyı yenileyin. Denemeler arasındaki tarayıcı önbelleğini temizlemeniz gerekebilir.

   Bazen **MyVM1** sayfası görüntülenirse ve diğer zamanlarda **MyVM2** sayfası görüntülenirse, gibi yük dengeleyici yapılan istekler her arka uç VM'si dağıtır. 

   ![Yeni IIS varsayılan sayfası](./media/tutorial-load-balancer-basic-internal-portal/9-load-balancer-test.png) 
   
## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık ihtiyacınız kalmadığında Yük Dengeleyiciyi ve tüm ilgili kaynakları silmek için açık **MyResourceGroupLB** kaynak grubu ve select **kaynak grubunu Sil**.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, temel katmanı iç yük dengeleyici oluşturdunuz. Oluşturduğunuz ve ağ kaynaklarına, arka uç sunucuları, bir durum araştırması ve yük dengeleyici kuralları yapılandırılmış. Arka uç sanal makinelere IIS yüklemesini ve bir test sanal makinesi yük dengeleyici, tarayıcıda test etmek için kullanılan. 

Ardından, sanal makinelerin kullanılabilirlik alanları genelinde Yük Dengelemesi konusunda bilgi edinin.

> [!div class="nextstepaction"]
> [Kullanılabilirlik bölgelerindeki VM’lerde yük dengeleme](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
