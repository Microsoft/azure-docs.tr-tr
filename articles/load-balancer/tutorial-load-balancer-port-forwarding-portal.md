---
title: 'Öğretici: bağlantı noktası iletmeyi Yapılandırma-Azure portal'
titleSuffix: Azure Load Balancer
description: Bu öğreticide, bir Azure sanal ağındaki VM 'lere bağlantı oluşturmak için Azure Load Balancer kullanarak bağlantı noktası iletmeyi yapılandırma gösterilmektedir.
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
Customer intent: As an IT administrator, I want to configure port forwarding in Azure Load Balancer to remotely connect to VMs in an Azure virtual network.
ms.service: load-balancer
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/26/2019
ms.author: allensu
ms.custom: seodec18
ms.openlocfilehash: 6dda01543a6a7f447adefcc6cc3cfa3ea5da5492
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74048858"
---
# <a name="tutorial-configure-port-forwarding-in-azure-load-balancer-using-the-portal"></a>Öğretici: portalı kullanarak Azure Load Balancer bağlantı noktası iletmeyi yapılandırma

Bağlantı noktası iletme, bir Azure sanal ağındaki sanal makinelere (VM) bir Azure Load Balancer genel IP adresi ve bağlantı noktası numarası kullanarak bağlanmanızı sağlar. 

Bu öğreticide, Azure Load Balancer bağlantı noktası iletmeyi ayarlarsınız. Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:

> [!div class="checklist"]
> * VM 'Ler üzerinden ağ trafiğini dengelemek için genel bir standart yük dengeleyici oluşturun. 
> * Bir ağ güvenlik grubu (NSG) kuralına sahip bir sanal ağ ve VM 'Ler oluşturun. 
> * VM 'Leri yük dengeleyici arka uç adres havuzuna ekleyin.
> * Yük dengeleyici durum araştırması ve trafik kuralları oluşturun.
> * Yük dengeleyici gelen NAT bağlantı noktası iletme kuralları oluşturun.
> * Yük Dengeleme ve bağlantı noktası iletmeyi görüntülemek için VM 'Lere IIS yükleyin ve yapılandırın.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun. 

Bu öğreticideki tüm adımlar için [https://portal.azure.com](https://portal.azure.com)Azure Portal oturum açın.

## <a name="create-a-standard-load-balancer"></a>Standart yük dengeleyici oluşturma

İlk olarak, VM 'Ler üzerinden trafik yükünü dengeleyebilir ortak bir standart yük dengeleyici oluşturun. Standart yük dengeleyici yalnızca standart bir genel IP adresini destekler. Standart yük dengeleyici oluşturduğunuzda, yük dengeleyici ön ucu olarak yapılandırılan ve varsayılan olarak **Loadbalancerön uç** olarak yapılandırılmış yeni bir standart genel IP adresi de oluşturursunuz. 

1. Ekranın sol üst kısmında **Kaynak oluştur** > **Ağ** > **Yük Dengeleyici** seçeneklerine tıklayın.
2. **Yük dengeleyici oluştur** sayfasının **temel bilgiler** sekmesinde aşağıdaki bilgileri girin veya seçin, kalan ayarlar için varsayılan değerleri kabul edin ve ardından **gözden geçir + oluştur**' u seçin:

    | Ayar                 | Değer                                              |
    | ---                     | ---                                                |
    | Abonelik               | Aboneliğinizi seçin.    |    
    | Kaynak grubu         | **Yeni oluştur** ' u seçin ve metin kutusuna *Myresourcegrouplb* yazın.|
    | Ad                   | *myLoadBalancer*                                   |
    | Bölge         | **Batı Avrupa**'yı seçin.                                        |
    | Tür          | **Ortak**seçeneğini belirleyin.                                        |
    | SKU           | **Standart**' ı seçin.                          |
    | Genel IP adresi | **Yeni oluştur**’u seçin. |
    | Genel IP adresi adı              | Metin kutusuna *Mypublicıp* yazın.   |
    |Kullanılabilirlik alanı| **Bölge yedekli**seçeneğini belirleyin.    |
     
    >[!NOTE]
     >Load Balancer ve tüm kaynaklarınızı, Kullanılabilirlik Alanları destekleyen bir konumda oluşturduğunuzdan emin olun. Daha fazla bilgi için bkz. [kullanılabilirlik alanları destekleyen bölgeler](../availability-zones/az-overview.md#services-support-by-region). 

3. **Gözden geçir + oluştur** sekmesinde **Oluştur**' a tıklayın.  
  
## <a name="create-and-configure-back-end-servers"></a>Arka uç sunucuları oluşturma ve yapılandırma

İki sanal makineyle bir sanal ağ oluşturun ve VM 'Leri yük dengeleyicinizin arka uç havuzuna ekleyin. 

### <a name="create-a-virtual-network"></a>Sanal ağ oluşturma

1. Portalda sol tarafta seçin **kaynak Oluştur** > **ağ** > **sanal ağ**.
   
1. İçinde **sanal ağ oluştur** bölmesinde yazın veya bu değerleri seçin:
   
   - **Adı**: türü *MyVNet*.
   - **ResourceGroup**: açılan **var olanı Seç** seçip **MyResourceGroupLB**. 
   - **Alt ağ** > **adı**: türü *MyBackendSubnet*.
   
1. **Oluştur**'u seçin.

   ![Sanal ağ oluşturma](./media/tutorial-load-balancer-port-forwarding-portal/2-load-balancer-virtual-network.png)

### <a name="create-vms-and-add-them-to-the-load-balancer-back-end-pool"></a>VM oluşturma ve bunları yük dengeleyici arka uç havuzuna ekleme

1. Portalda sol tarafta seçin **kaynak Oluştur** > **işlem** > **Windows Server 2016 Datacenter**. 
   
1. İçinde **sanal makine oluşturma**yazın veya aşağıdaki değerleri seçin **Temelleri** sekmesinde:
   - **Abonelik** > **kaynak grubu**: seçin ve aşağı açılan **MyResourceGroupLB**.
   - **Sanal makine adı**: *MyVM1*yazın.
   - **Bölge**: **Batı Avrupa**seçin. 
   - **Kullanıcı adı**: *azureuser*yazın.
   - **Parola**: *Azure1234567*yazın. 
     Parolayı **Onayla** alanına parolayı yeniden yazın.
   
1. Seçin **ağ** sekmesinde veya seçin **sonraki: diskleri**, ardından **sonraki: ağ**. 
   
   Aşağıdaki seçili olduğundan emin olun:
   - **Sanal ağ**: **MyVNet**
   - **Alt ağ**: **MyBackendSubnet**
   
1. **Ortak IP**altında, **Yeni oluştur**' u seçin, **genel IP adresi oluştur** sayfasında **Standart** ' ı seçin ve ardından **Tamam**' ı seçin. 
   
1. **Ağ güvenlik grubu**altında, yeni bir ağ güvenlik grubu (NSG) oluşturmak için **Gelişmiş** ' i seçerek bir güvenlik duvarı türü oluşturun. 
   1. İçinde **yapılandırma ağ güvenlik grubu** alanın, Seç **Yeni Oluştur**. 
   1. Tür *Vm2*seçip **Tamam**. 
   
   >[!NOTE]
   >Varsayılan olarak NSG 'nin bağlantı noktası 3389, Uzak Masaüstü (RDP) bağlantı noktasını açmak için bir gelen kuralı zaten olduğunu gözlemleyin.
   
1. VM 'yi, oluşturduğunuz bir yük dengeleyici arka uç havuzuna ekleyin:
   
   1. **Yük Dengeleme** altında **Bu sanal makineyi var olan bir yük dengeleme çözümünün arkasına koyun** > , **Evet**' i seçin. 
   1. **Yük dengeleme seçenekleri**için, açılır ve **Azure yük dengeleyici**' ni seçin. 
   1. **Yük dengeleyici seçin**için, açılır ve **myloadbalancer**' ı seçin. 
   1. **Bir arka uç havuzu seçin**altında **Yeni oluştur**' u seçin, sonra *mybackendpool*yazın ve **Oluştur**' u seçin. 
   
   ![Sanal ağ oluşturma](./media/tutorial-load-balancer-port-forwarding-portal/create-vm-networking.png)
   
1. Seçin **Yönetim** sekmesinde veya seçin **sonraki** > **Yönetim**. Altında **izleme**ayarlayın **önyükleme tanılaması** için **kapalı**.
   
1. **İncele ve oluştur**’u seçin.
   
1. Ayarları gözden geçirin ve doğrulamanın başarılı olması durumunda **Oluştur**' u seçin. 

1. Adlı ikinci bir VM oluşturmak için adımları *MyVM2*, diğer tüm ayarlarla MyVM1 ile aynı. 
   
   **Ağ güvenlik grubu**Için, **Gelişmiş**' i seçtikten sonra, açılan listeden daha önce oluşturduğunuz **mynetworksecuritygroup** ' u seçin. 
   
   **Bir arka uç havuzu seçin**altında **mybackendpool** ' ın seçili olduğundan emin olun. 

### <a name="create-an-nsg-rule-for-the-vms"></a>VM 'Ler için bir NSG kuralı oluşturma

VM 'Ler için gelen internet (HTTP) bağlantılarına izin veren bir ağ güvenlik grubu (NSG) kuralı oluşturun.

>[!NOTE]
>Varsayılan olarak, NSG bağlantı noktası 3389, Uzak Masaüstü (RDP) bağlantı noktasını açan bir kuralla zaten sahiptir.

1. Soldaki menüden **Tüm kaynaklar**’ı seçin. Kaynak listesinden **Vm2** içinde **MyResourceGroupLB** kaynak grubu.
   
1. **Ayarlar** bölümünde **Gelen güvenlik kuralları**’nı ve sonra **Ekle**’yi seçin.
   
1. **Gelen güvenlik kuralı ekle** iletişim kutusunda, aşağıdakileri yazın veya seçin:
   
   - **Kaynak**: seçin **hizmet etiketi**.  
   - **Kaynak hizmet etiketi**: seçin **Internet**. 
   - **Hedef bağlantı noktası aralıkları**: türü *80*.
   - **Protokol**: seçin **TCP**. 
   - **Eylem**: seçin **izin**.  
   - **Öncelik**: türü *100*. 
   - **Adı**: türü *MyHTTPRule*. 
   - **Açıklama**: türü *HTTP'ye izin ver*. 
   
1. **Add (Ekle)** seçeneğini belirleyin. 
   
   ![NSG kuralı oluşturma](./media/tutorial-load-balancer-port-forwarding-portal/8-load-balancer-nsg-rules.png)
   
## <a name="create-load-balancer-resources"></a>Yük dengeleyici kaynakları oluşturma

Bu bölümde, yük dengeleyici arka uç havuzunu inceleyeceksiniz, yük dengeleyici durum araştırmasını ve trafik kurallarını yapılandırırsınız.

### <a name="view-the-back-end-address-pool"></a>Arka uç adres havuzunu görüntüleme

Trafiği VM 'lere dağıtmak için, yük dengeleyici yük dengeleyiciye bağlı sanal ağ arabirimlerinin (NIC 'ler) IP adreslerini içeren bir arka uç adres havuzu kullanır. 

Yük dengeleyici arka uç havuzunuzu oluşturdunuz ve VM 'Leri oluştururken buna VM 'Ler eklediniz. Ayrıca, arka uç havuzları oluşturabilir ve yük dengeleyici **arka uç havuzları** sayfasından VM 'ler ekleyebilir veya kaldırabilirsiniz. 

1. Seçin **tüm kaynakları** sol menüsünü ve ardından **MyLoadBalancer** kaynak listesinde.
   
1. **Ayarlar**’ın altında **Arka Uç Havuzları**’nı seçin.
   
1. Üzerinde **arka uç havuzları** sayfasında **MyBackendPool** ve her ikisi de emin **VM1** ve **VM2** listelenir.

1. **Mybackendpool**öğesini seçin. 
   
   **Mybackendpool** sayfasında, **sanal makıne** ve **IP adresi**altında, havuza kullanılabilir VM 'leri kaldırabilir veya ekleyebilirsiniz.

**Arka uç havuzları** sayfasında **Ekle** ' ye tıklayarak yeni arka uç havuzları oluşturabilirsiniz.

### <a name="create-a-health-probe"></a>Durum araştırması oluşturma

VM durumunu izlemek için yük dengeleyicisine izin vermek için durum araştırması kullanabilirsiniz. Durum yoklaması, durum denetimlerine verdikleri yanıtlara göre VM’leri dinamik olarak yük dengeleyici rotasyonuna ekler ve kaldırır. 

1. Seçin **tüm kaynakları** sol menüsünü ve ardından **MyLoadBalancer** kaynak listesinde.
   
1. **Ayarlar** bölümünde **Durum araştırmaları**’nı ve sonra **Ekle**’yi seçin.
   
1. **Durum araştırması Ekle** sayfasında, aşağıdaki değerleri yazın veya seçin:
   
   - **Adı**: türü *MyHealthProbe*.
   - **Protokol**: seçin ve aşağı açılan **HTTP**. 
   - **Bağlantı noktası**: türü *80*. 
   - **Yol**: kabul */* için varsayılan bir URI. Diğer bir URI ile bu değeri değiştirebilirsiniz. 
   - **Aralığı**: türü *15*. Araştırma denemeleri arasındaki saniye sayısını aralığıdır.
   - **Sağlıksız durum eşiği**: türü *2*. Bu değer bir VM kötü olarak kabul edilmeden önce gerçekleşmesi ardışık araştırma hatası sayısıdır.
   
1. **Tamam**’ı seçin.
   
   ![Bir araştırma eklemek](./media/tutorial-load-balancer-port-forwarding-portal/4-load-balancer-probes.png)

### <a name="create-a-load-balancer-rule"></a>Yük dengeleyici kuralı oluşturma

Yük dengeleyici kuralı, trafiğin sanal makinelere nasıl dağıtıldığını belirler. Ön uç IP yapılandırmasını gelen trafiğe, trafik ve gerekli kaynak ve hedef bağlantı noktalarını almak için arka uç IP havuzu için kuralı tanımlar. 

Adlı yük dengeleyici kuralı **MyLoadBalancerRule** ön 80 numaralı bağlantı noktasını dinler **LoadBalancerFrontEnd**. Kural ağ trafiği arka uç adres havuzuna gönderen **MyBackendPool**, bağlantı noktası 80'de. 

1. Seçin **tüm kaynakları** sol menüsünü ve ardından **MyLoadBalancer** kaynak listesinde.
   
1. **Ayarlar** bölümünde **Yük dengeleme kuralları**’nı ve sonra **Ekle**’yi seçin.
   
1. Üzerinde **Yük Dengeleme Kuralı Ekle** sayfasında yazın veya aşağıdaki değerleri seçin:
   
   - **Adı**: türü *MyLoadBalancerRule*.
   - **Protokol**: seçin **TCP**.
   - **Bağlantı noktası**: türü *80*.
   - **Arka uç bağlantı noktası**: türü *80*.
   - **Arka uç havuzu**: seçin **MyBackendPool**.
   - **Durum araştırması**: seçin **MyHealthProbe**. 
   
1. **Tamam**’ı seçin.
   
   ![Yük Dengeleyici Kuralı Ekle](./media/tutorial-load-balancer-port-forwarding-portal/5-load-balancing-rules.png)

## <a name="create-an-inbound-nat-port-forwarding-rule"></a>Gelen NAT bağlantı noktası iletme kuralı oluşturma

Ön uç IP adresinin belirli bir bağlantı noktasından gelen trafiği arka uç VM 'sinin belirli bir bağlantı noktasına iletmek için bir yük dengeleyici gelen ağ adresi çevirisi (NAT) kuralı oluşturun.

1. Sol taraftaki menüden **tüm kaynaklar** ' ı seçin ve ardından kaynak listesinden **myloadbalancer** ' yi seçin.
   
1. **Ayarlar**altında **gelen NAT kuralları**' nı seçin ve ardından **Ekle**' yi seçin. 
   
1. **Gelen NAT kuralı ekle** sayfasında, aşağıdaki değerleri yazın veya seçin:
   
   - **Ad**: *MyNATRuleVM1*yazın.
   - **Bağlantı noktası**: *4221*yazın.
   - **Hedef sanal makine**: açılan listeden **MyVM1** öğesini seçin.
   - **Ağ IP yapılandırması**: açılan listeden **ipconfig1** öğesini seçin.
   - **Bağlantı noktası eşleme**: **özel**' i seçin.
   - **Hedef bağlantı noktası**: *3389*yazın.
   
1. **Tamam**’ı seçin.
   
1. **Bağlantı noktası**: *4222* ve **hedef sanal makine**: **MyVM2**kullanılarak *MyNATRuleVM2*adlı bir gelen NAT kuralı eklemek için adımları yineleyin.

## <a name="test-the-load-balancer"></a>Yük dengeleyiciyi test etme

Bu bölümde, arka uç sunucularına Internet Information Services (IIS) yükleyecek ve varsayılan Web sayfasını makine adını gösterecek şekilde özelleştireceksiniz. Daha sonra yük dengeleyiciyi test etmek için yük dengeleyicinin genel IP adresini kullanacaksınız. 

Her arka uç VM, yük dengeleyicinin istekler iki VM arasında dağıtmasını görebilmeniz için varsayılan IIS web sayfasına, farklı bir sürümü işlevi görür.

### <a name="connect-to-the-vms-with-rdp"></a>Vm'lere RDP ile bağlanma

Uzak Masaüstü (RDP) ile her sanal makineye bağlanın. 

1. Portalında **tüm kaynakları** sol menüsünde. Kaynak listesinden her bir sanal Makineye **MyResourceGroupLB** kaynak grubu.
   
1. Üzerinde **genel bakış** sayfasında **Connect**ve ardından **indirme RDP dosyası**. 
   
1. RDP dosyası indirilir ve seçin açık **Connect**.
   
1. Windows Güvenlik ekranında seçin **daha fazla seçenek** ardından **farklı bir hesap kullan**. 
   
   Kullanıcı adı *azureuser* ve Password *Azure1234567*yazın ve **Tamam**' ı seçin.
   
1. Yanıt **Evet** herhangi bir sertifika istemi. 
   
   VM masaüstüne yeni bir pencerede açılır. 

### <a name="install-iis-and-replace-the-default-iis-web-page"></a>IIS 'yi yükleyip varsayılan IIS Web sayfasını değiştirme 

IIS yüklemek ve varsayılan IIS Web sayfasını VM 'nin adını görüntüleyen bir sayfayla değiştirmek için PowerShell 'i kullanın.

1. MyVM1 ve MyVM2 başlatma **Windows PowerShell** gelen **Başlat** menüsü. 

2. IIS yüklemek ve varsayılan IIS web sayfasına değiştirmek için aşağıdaki komutları çalıştırın:
   
   ```powershell-interactive
    # Install IIS
      Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom htm file that displays server name
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
    
   ```
   
1. RDP bağlantıları MyVM1 ve MyVM2 seçerek kapatmak **Bağlantıyı Kes**. VM 'Leri kapatmayın.

### <a name="test-load-balancing"></a>Yük dengelemeyi test etme

1. Portalda, **Myloadbalancer** **genel bakış** sayfasında genel IP **adresi**altındaki genel IP adresini kopyalayın. Seçin ve adresi üzerine geldiğinizde **kopyalama** simgesini kopyalayın. Bu örnekte, **40.67.218.235**' dir. 
   
1. Yük dengeleyicinin genel IP adresini (*40.67.218.235*) Internet tarayıcınızın adres çubuğuna yapıştırın veya yazın. 
   
   Özelleştirilmiş bir IIS web sunucusunun varsayılan sayfası, tarayıcıda görüntülenir. Ya da iletiyi okur **Hello World MyVM1 gelen**, veya **Hello World MyVM2 gelen**.
   
   ![Yeni IIS varsayılan sayfası](./media/tutorial-load-balancer-port-forwarding-portal/9-load-balancer-test.png) 
   
1. Yük dengeleyicinin trafiği VM'ye dağıtmasını görmek için tarayıcıyı yenileyin. Bazen **MyVM1** sayfası görüntülenirse ve diğer zamanlarda **MyVM2** sayfası görüntülenirse, gibi yük dengeleyici yapılan istekler her arka uç VM'si dağıtır.
   
   >[!NOTE]
   >Tarayıcı önbelleğinizi temizlemeniz veya denemeler arasında yeni bir tarayıcı penceresi açmanız gerekebilir.

## <a name="test-port-forwarding"></a>Bağlantı noktası iletmeyi test etme

Bağlantı noktası iletme ile, yük dengeleyicinin IP adresini ve NAT kuralında tanımlanmış ön uç bağlantı noktası değerini kullanarak bir arka uç VM 'ye uzak masaüstü 'nü kullanabilirsiniz. 

1. Portalda, **Myloadbalancer** **genel bakış** sayfasında genel IP adresini kopyalayın. Seçin ve adresi üzerine geldiğinizde **kopyalama** simgesini kopyalayın. Bu örnekte, **40.67.218.235**' dir. 
   
1. Bir komut istemi açın ve yük dengeleyicinin genel IP adresini ve VM 'nin NAT kuralında tanımladığınız ön uç bağlantı noktasını kullanarak MyVM2 ile bir Uzak Masaüstü oturumu oluşturmak için aşağıdaki komutu kullanın. 
   
   ```
   mstsc /v:40.67.218.235:4222
   ```
  
1. İndirilen RDP dosyasını açın ve **Bağlan**' ı seçin.
   
1. Windows Güvenlik ekranında seçin **daha fazla seçenek** ardından **farklı bir hesap kullan**. 
   
   Kullanıcı adı *azureuser* ve Password *Azure1234567*yazın ve **Tamam**' ı seçin.
   
1. Yanıt **Evet** herhangi bir sertifika istemi. 
   
   MyVM2 masaüstü yeni bir pencerede açılır. 

**MyNATRuleVM2** gelen NAT kuralı, trafiği yük dengeleyicinin ön uç bağlantı noktası 4222 ' den MyVM2's bağlantı noktası 3389 ' e (RDP bağlantı noktası) yönlendirdiği IÇIN, RDP bağlantısı başarılı olur.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık ihtiyacınız kalmadığında Yük Dengeleyiciyi ve tüm ilgili kaynakları silmek için açık **MyResourceGroupLB** kaynak grubu ve select **kaynak grubunu Sil**.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, standart bir genel yük dengeleyici oluşturdunuz. Oluşturduğunuz ve ağ kaynaklarına, arka uç sunucuları, bir durum araştırması ve yük dengeleyici kuralları yapılandırılmış. IIS 'yi arka uç VM 'lerine yüklediniz ve yük dengeleyiciyi test etmek için yük dengeleyicinin genel IP adresini kullandınız. Yük dengeleyicideki belirtilen bir bağlantı noktasından, arka uç VM 'deki bir bağlantı noktasına bağlantı noktası iletmeyi ayarlayın ve test edersiniz. 

Azure Load Balancer hakkında daha fazla bilgi edinmek için daha fazla yük dengeleyici öğreticilerine geçin.

> [!div class="nextstepaction"]
> [Azure Load Balancer öğreticileri](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
