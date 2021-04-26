---
title: 'Öğretici: bağlantı noktası iletmeyi Yapılandırma-Azure portal'
titleSuffix: Azure Load Balancer
description: Bu öğreticide, bir Azure sanal ağındaki VM 'lere bağlantı oluşturmak için Azure Load Balancer kullanarak bağlantı noktası iletmeyi yapılandırma gösterilmektedir.
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
ms.service: load-balancer
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/26/2019
ms.author: allensu
ms.custom: seodec18
ms.openlocfilehash: 27851a44dbe3085610fadc8b9bdaf400b4d2876c
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106056071"
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

Bu öğreticideki tüm adımlar için Azure portal oturum açın [https://portal.azure.com](https://portal.azure.com) .

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği.

## <a name="create-a-standard-load-balancer"></a>Standart yük dengeleyici oluşturma

İlk olarak, VM 'Ler üzerinden trafik yükünü dengeleyebilir ortak bir standart yük dengeleyici oluşturun. Standart yük dengeleyici yalnızca standart bir genel IP adresini destekler. Standart yük dengeleyici oluşturduğunuzda, yük dengeleyici ön ucu olarak yapılandırılan ve varsayılan olarak **Loadbalancerön uç** olarak yapılandırılmış yeni bir standart genel IP adresi de oluşturursunuz. 

1. Ekranın sol üst kısmında **kaynak**  >  **ağ**  >  **Load Balancer** oluştur ' a tıklayın.
2. **Yük dengeleyici oluştur** sayfasının **temel bilgiler** sekmesinde aşağıdaki bilgileri girin veya seçin, kalan ayarlar için varsayılan değerleri kabul edin ve ardından **gözden geçir + oluştur**' u seçin:

    | Ayar                 | Değer                                              |
    | ---                     | ---                                                |
    | Abonelik               | Aboneliğinizi seçin.    |    
    | Kaynak grubu         | **Yeni oluştur** ' u seçin ve metin kutusuna *Myresourcegrouplb* yazın.|
    | Name                   | *myLoadBalancer*                                   |
    | Region         | **Batı Avrupa**'yı seçin.                                        |
    | Tür          | **Genel**’i seçin.                                        |
    | SKU           | **Standart**' ı seçin.                          |
    | Genel IP adresi | **Yeni oluştur**’u seçin. |
    | Genel IP adresi adı              | Metin kutusuna *Mypublicıp* yazın.   |
    |Kullanılabilirlik alanı| **Bölge yedekli** seçeneğini belirleyin.    |
     
    >[!NOTE]
     >Load Balancer ve tüm kaynaklarınızı, Kullanılabilirlik Alanları destekleyen bir konumda oluşturduğunuzdan emin olun. Daha fazla bilgi için bkz. [kullanılabilirlik alanları destekleyen bölgeler](../availability-zones/az-region.md). 

3. **Gözden geçir + oluştur** sekmesinde **Oluştur**' a tıklayın.  
  
## <a name="create-and-configure-back-end-servers"></a>Arka uç sunucuları oluşturma ve yapılandırma

İki sanal makineyle bir sanal ağ oluşturun ve VM 'Leri yük dengeleyicinizin arka uç havuzuna ekleyin. 

## <a name="virtual-network-and-parameters"></a>Sanal ağ ve parametreler

Bu bölümde, adımlarda aşağıdaki parametreleri aşağıdaki bilgilerle değiştirmeniz gerekir:

| Parametre                   | Değer                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroupLB (mevcut kaynak grubunu Seç) |
| **\<virtual-network-name>** | myVNet          |
| **\<region-name>**          | West Europe      |
| **\<IPv4-address-space>**   | 10.3.0.0 \ 16          |
| **\<subnet-name>**          | myBackendSubnet        |
| **\<subnet-address-range>** | 10.3.0.0 \ 24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

### <a name="create-vms-and-add-them-to-the-load-balancer-back-end-pool"></a>VM oluşturma ve bunları yük dengeleyici arka uç havuzuna ekleme

1. Portalın sol üst tarafında, **kaynak oluştur**  >  **işlem**  >  **Windows Server 2016 Datacenter**' u seçin. 
   
1. **Sanal makine oluştur**' da, **temel bilgiler** sekmesinde aşağıdaki değerleri yazın veya seçin:
   - **Abonelik**  >  **Kaynak grubu**: açılır ve **Myresourcegrouplb**' ı seçin.
   - **Sanal makine adı**: *MyVM1* yazın.
   - **Bölge**: **Batı Avrupa** seçin. 
   - **Kullanıcı adı**: *azureuser* yazın.
   - **Parola**: *Azure1234567* yazın. 
     Parolayı **Onayla** alanına parolayı yeniden yazın.
   
1. **Ağ** sekmesini seçin veya **Sonraki: diskler**' i ve sonra **İleri: ağ**' ı seçin. 
   
   Aşağıdakilerin seçili olduğundan emin olun:
   - **Sanal ağ**: **myvnet**
   - **Alt ağ**: **mybackendsubnet**
   
1. **Ortak IP** altında, **Yeni oluştur**' u seçin, **genel IP adresi oluştur** sayfasında **Standart** ' ı seçin ve ardından **Tamam**' ı seçin. 
   
1. **Ağ güvenlik grubu** altında, yeni bir ağ güvenlik grubu (NSG) oluşturmak için **Gelişmiş** ' i seçerek bir güvenlik duvarı türü oluşturun. 
   1. **Ağ güvenlik grubunu yapılandır** alanında **Yeni oluştur**' u seçin. 
   1. *Mynetworksecuritygroup* yazın ve **Tamam**' ı seçin. 
   
   >[!NOTE]
   >Varsayılan olarak NSG 'nin bağlantı noktası 3389, Uzak Masaüstü (RDP) bağlantı noktasını açmak için bir gelen kuralı zaten olduğunu gözlemleyin.
   
1. VM 'yi, oluşturduğunuz bir yük dengeleyici arka uç havuzuna ekleyin:
   
   1. **Yük Dengeleme** altında  >  **Bu sanal makineyi var olan bir yük dengeleme çözümünün arkasına yerleştirin**, **Evet**' i seçin. 
   1. **Yük dengeleme seçenekleri** için, açılır ve **Azure yük dengeleyici**' ni seçin. 
   1. **Yük dengeleyici seçin** için, açılır ve **myloadbalancer**' ı seçin. 
   1. **Bir arka uç havuzu seçin** altında **Yeni oluştur**' u seçin, sonra *mybackendpool* yazın ve **Oluştur**' u seçin. 
   
   ![Sanal ağ oluşturma](./media/tutorial-load-balancer-port-forwarding-portal/create-vm-networking.png)
   
1. **Yönetim** sekmesini seçin veya **İleri**  >  **Yönetim**' i seçin. **İzleme** altında, **önyükleme tanılamayı** **kapalı** olarak ayarlayın.
   
1. **Gözden geçir ve oluştur**’u seçin.
   
1. Ayarları gözden geçirin ve doğrulamanın başarılı olması durumunda **Oluştur**' u seçin. 

1. *MyVM2* adlı ikinci bir sanal makine oluşturmak için adımları izleyin, diğer tüm ayarlar MyVM1 ile aynıdır. 
   
   **Ağ güvenlik grubu** Için, **Gelişmiş**' i seçtikten sonra, açılan listeden daha önce oluşturduğunuz **mynetworksecuritygroup** ' u seçin. 
   
   **Bir arka uç havuzu seçin** altında **mybackendpool** ' ın seçili olduğundan emin olun. 

### <a name="create-an-nsg-rule-for-the-vms"></a>VM 'Ler için bir NSG kuralı oluşturma

VM 'Ler için gelen internet (HTTP) bağlantılarına izin veren bir ağ güvenlik grubu (NSG) kuralı oluşturun.

>[!NOTE]
>Varsayılan olarak, NSG bağlantı noktası 3389, Uzak Masaüstü (RDP) bağlantı noktasını açan bir kuralla zaten sahiptir.

1. Soldaki menüden **Tüm kaynaklar**’ı seçin. Kaynak listesinden **Myresourcegrouplb** kaynak grubundaki **Mynetworksecuritygroup** ' u seçin.
   
1. **Ayarlar** bölümünde **Gelen güvenlik kuralları**’nı ve sonra **Ekle**’yi seçin.
   
1. **Gelen güvenlik kuralı ekle** iletişim kutusunda, aşağıdakileri yazın veya seçin:
   
   - **Kaynak**: **hizmet etiketi** seçin.  
   - **Kaynak hizmet etiketi**: **Internet**' i seçin. 
   - **Hedef bağlantı noktası aralıkları**: *80* yazın.
   - **Protokol**: **TCP**' yi seçin. 
   - **Eylem**: **izin ver**' i seçin.  
   - **Öncelik**: *100* yazın. 
   - **Ad**: *Myhttprule* yazın. 
   - **Açıklama**: tür *http 'ye izin ver*. 
   
1. **Add (Ekle)** seçeneğini belirleyin. 
   
   ![NSG kuralı oluşturma](./media/tutorial-load-balancer-port-forwarding-portal/8-load-balancer-nsg-rules.png)
   
## <a name="create-load-balancer-resources"></a>Yük dengeleyici kaynakları oluşturma

Bu bölümde, yük dengeleyici arka uç havuzunu inceleyeceksiniz, yük dengeleyici durum araştırmasını ve trafik kurallarını yapılandırırsınız.

### <a name="view-the-back-end-address-pool"></a>Arka uç adres havuzunu görüntüleme

Trafiği VM 'lere dağıtmak için, yük dengeleyici yük dengeleyiciye bağlı sanal ağ arabirimlerinin (NIC 'ler) IP adreslerini içeren bir arka uç adres havuzu kullanır. 

Yük dengeleyici arka uç havuzunuzu oluşturdunuz ve VM 'Leri oluştururken buna VM 'Ler eklediniz. Ayrıca, arka uç havuzları oluşturabilir ve yük dengeleyici **arka uç havuzları** sayfasından VM 'ler ekleyebilir veya kaldırabilirsiniz. 

1. Sol taraftaki menüden **tüm kaynaklar** ' ı seçin ve ardından kaynak listesinden **myloadbalancer** ' yi seçin.
   
1. **Ayarlar**’ın altında **Arka Uç Havuzları**’nı seçin.
   
1. **Arka uç havuzları** sayfasında, **Mybackendpool** ' u genişletin ve hem **VM1** hem de **VM2** öğelerinin listelendiğinden emin olun.

1. **Mybackendpool** öğesini seçin. 
   
   **Mybackendpool** sayfasında, **sanal makıne** ve **IP adresi** altında, havuza kullanılabilir VM 'leri kaldırabilir veya ekleyebilirsiniz.

**Arka uç havuzları** sayfasında **Ekle** ' ye tıklayarak yeni arka uç havuzları oluşturabilirsiniz.

### <a name="create-a-health-probe"></a>Durum araştırması oluşturma

Yük dengeleyicinin VM durumunu izlemesine izin vermek için, bir sistem durumu araştırması kullanırsınız. Durum yoklaması, durum denetimlerine verdikleri yanıtlara göre VM’leri dinamik olarak yük dengeleyici rotasyonuna ekler ve kaldırır. 

1. Sol taraftaki menüden **tüm kaynaklar** ' ı seçin ve ardından kaynak listesinden **myloadbalancer** ' yi seçin.
   
1. **Ayarlar** bölümünde **Durum araştırmaları**’nı ve sonra **Ekle**’yi seçin.
   
1. **Durum araştırması Ekle** sayfasında, aşağıdaki değerleri yazın veya seçin:
   
   - **Ad**: *myhealtharaştırması* yazın.
   - **Protokol**: açılır ve **http**' yi seçin. 
   - **Bağlantı noktası**: *80* yazın. 
   - **Yol**: */* varsayılan URI için kabul edin. Bu değeri başka bir URI ile değiştirebilirsiniz. 
   - **Aralık**: *15* yazın. Aralık, yoklama denemeleri arasındaki saniye sayısıdır.
   - **Sağlıksız eşik**: tür *2*. Bu değer, bir VM 'nin sağlıksız olduğu kabul edilmeden önce oluşan ardışık yoklama hatalarının sayısıdır.
   
1. **Tamam**’ı seçin.
   
   ![Araştırma ekleyin](./media/tutorial-load-balancer-port-forwarding-portal/4-load-balancer-probes.png)

### <a name="create-a-load-balancer-rule"></a>Yük dengeleyici kuralı oluşturma

Yük dengeleyici kuralı, trafiğin sanal makinelere nasıl dağıtıldığını belirler. Kural, gelen trafik için ön uç IP yapılandırmasını, trafiği almak için arka uç IP havuzunu ve gerekli kaynak ve hedef bağlantı noktalarını tanımlar. 

**Myloadbalancerrule** adlı yük dengeleyici kuralı ön uç **loadbalancerön** ucunda 80 numaralı bağlantı noktasını dinler. Kural, bağlantı noktası 80 ' de, **Mybackendpool** arka uç adres havuzuna ağ trafiği gönderir. 

1. Sol taraftaki menüden **tüm kaynaklar** ' ı seçin ve ardından kaynak listesinden **myloadbalancer** ' yi seçin.
   
1. **Ayarlar** bölümünde **Yük dengeleme kuralları**’nı ve sonra **Ekle**’yi seçin.
   
1. **Yük Dengeleme kuralı ekle** sayfasında, aşağıdaki değerleri yazın veya seçin:
   
   - **Ad**: *myloadbalancerrule* yazın.
   - **Protokol**: **TCP**' yi seçin.
   - **Bağlantı noktası**: *80* yazın.
   - **Arka uç bağlantı noktası**: *80* yazın.
   - **Arka uç havuzu**: **mybackendpool**' u seçin.
   - **Durum araştırması**: **myhealtharaştırması**' ni seçin. 
   
1. **Tamam**’ı seçin.
   
   ![Yük dengeleyici kuralı ekleme](./media/tutorial-load-balancer-port-forwarding-portal/5-load-balancing-rules.png)

## <a name="create-an-inbound-nat-port-forwarding-rule"></a>Gelen NAT bağlantı noktası iletme kuralı oluşturma

Ön uç IP adresinin belirli bir bağlantı noktasından gelen trafiği arka uç VM 'sinin belirli bir bağlantı noktasına iletmek için bir yük dengeleyici gelen ağ adresi çevirisi (NAT) kuralı oluşturun.

1. Sol taraftaki menüden **tüm kaynaklar** ' ı seçin ve ardından kaynak listesinden **myloadbalancer** ' yi seçin.
   
1. **Ayarlar** altında **gelen NAT kuralları**' nı seçin ve ardından **Ekle**' yi seçin. 
   
1. **Gelen NAT kuralı ekle** sayfasında, aşağıdaki değerleri yazın veya seçin:
   
   - **Ad**: *MyNATRuleVM1* yazın.
   - **Bağlantı noktası**: *4221* yazın.
   - **Hedef sanal makine**: açılan listeden **MyVM1** öğesini seçin.
   - **Ağ IP yapılandırması**: açılan listeden **ipconfig1** öğesini seçin.
   - **Bağlantı noktası eşleme**: **özel**' i seçin.
   - **Hedef bağlantı noktası**: *3389* yazın.
   
1. **Tamam**’ı seçin.
   
1. **Bağlantı noktası**: *4222* ve **hedef sanal makine**: **MyVM2** kullanılarak *MyNATRuleVM2* adlı bir gelen NAT kuralı eklemek için adımları yineleyin.

## <a name="test-the-load-balancer"></a>Yük dengeleyiciyi test etme

Bu bölümde, arka uç sunucularına Internet Information Services (IIS) yükleyecek ve varsayılan Web sayfasını makine adını gösterecek şekilde özelleştireceksiniz. Daha sonra yük dengeleyiciyi test etmek için yük dengeleyicinin genel IP adresini kullanacaksınız. 

Her arka uç sanal makinesi varsayılan IIS Web sayfasının farklı bir sürümüne hizmet eder, böylece yük dengeleyici istekleri iki VM arasında dağıtır.

### <a name="connect-to-the-vms-with-rdp"></a>RDP ile VM 'lere bağlanma

Uzak Masaüstü (RDP) ile her sanal makineye bağlanın. 

1. Portalda, sol taraftaki menüden **tüm kaynaklar** ' ı seçin. Kaynak listesinden, **Myresourcegrouplb** kaynak grubundaki her bir VM 'yi seçin.
   
1. **Genel bakış** sayfasında **Bağlan**' ı seçin ve ardından **RDP dosyasını indir**' i seçin. 
   
1. İndirdiğiniz RDP dosyasını açın ve **Bağlan**' ı seçin.
   
1. Windows güvenliği ekranında, **diğer seçenekler** ' i seçin ve **farklı bir hesap kullanın**. 
   
   Kullanıcı adı *azureuser* ve Password *Azure1234567* yazın ve **Tamam**' ı seçin.
   
1. Herhangi bir sertifika istemine **Evet** yanıtı verin. 
   
   VM masaüstü yeni bir pencerede açılır. 

### <a name="install-iis-and-replace-the-default-iis-web-page"></a>IIS 'yi yükleyip varsayılan IIS Web sayfasını değiştirme 

IIS yüklemek ve varsayılan IIS Web sayfasını VM 'nin adını görüntüleyen bir sayfayla değiştirmek için PowerShell 'i kullanın.

1. MyVM1 ve MyVM2 üzerinde, **Başlat** menüsünden **Windows PowerShell** 'i başlatın. 

2. IIS yüklemek için aşağıdaki komutları çalıştırın ve varsayılan IIS Web sayfasını değiştirin:
   
   ```powershell-interactive
    # Install IIS
      Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom htm file that displays server name
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
    
   ```
   
1. **Bağlantıyı kes**' i seçerek RDP bağlantılarını MyVM1 ve MyVM2 ile kapatın. VM 'Leri kapatmayın.

### <a name="test-load-balancing"></a>Yük dengelemeyi test etme

1. Portalda, **Myloadbalancer** **genel bakış** sayfasında genel IP **adresi** altındaki genel IP adresini kopyalayın. Adresin üzerine gelin ve kopyalamak için **Kopyala** simgesini seçin. Bu örnekte, **40.67.218.235**' dir. 
   
1. Yük dengeleyicinin genel IP adresini (*40.67.218.235*) Internet tarayıcınızın adres çubuğuna yapıştırın veya yazın. 
   
   Özelleştirilmiş IIS Web sunucusu varsayılan sayfası tarayıcıda görüntülenir. İleti, MyVM1 ' **dan Merhaba Dünya** veya MyVM2 ' **den Merhaba Dünya** okur.
   
   ![Yeni IIS varsayılan sayfası](./media/tutorial-load-balancer-port-forwarding-portal/9-load-balancer-test.png) 
   
1. Yük dengeleyicinin trafiği VM 'Ler arasında dağıtmalarını görmek için tarayıcıyı yenileyin. Bazı durumlarda, yük dengeleyici istekleri her bir arka uç sanal makinesine dağıttığı için, **MyVM1** sayfası görünür ve **MyVM2** sayfasının diğer zamanları görüntülenir.
   
   >[!NOTE]
   >Tarayıcı önbelleğinizi temizlemeniz veya denemeler arasında yeni bir tarayıcı penceresi açmanız gerekebilir.

## <a name="test-port-forwarding"></a>Bağlantı noktası iletmeyi test etme

Bağlantı noktası iletme ile, yük dengeleyicinin IP adresini ve NAT kuralında tanımlanmış ön uç bağlantı noktası değerini kullanarak bir arka uç VM 'ye uzak masaüstü 'nü kullanabilirsiniz. 

1. Portalda, **Myloadbalancer** **genel bakış** sayfasında genel IP adresini kopyalayın. Adresin üzerine gelin ve kopyalamak için **Kopyala** simgesini seçin. Bu örnekte, **40.67.218.235**' dir. 
   
1. Bir komut istemi açın ve yük dengeleyicinin genel IP adresini ve VM 'nin NAT kuralında tanımladığınız ön uç bağlantı noktasını kullanarak MyVM2 ile bir Uzak Masaüstü oturumu oluşturmak için aşağıdaki komutu kullanın. 
   
   ```
   mstsc /v:40.67.218.235:4222
   ```
  
1. İndirilen RDP dosyasını açın ve **Bağlan**' ı seçin.
   
1. Windows güvenliği ekranında, **diğer seçenekler** ' i seçin ve **farklı bir hesap kullanın**. 
   
   Kullanıcı adı *azureuser* ve Password *Azure1234567* yazın ve **Tamam**' ı seçin.
   
1. Herhangi bir sertifika istemine **Evet** yanıtı verin. 
   
   MyVM2 masaüstü yeni bir pencerede açılır. 

**MyNATRuleVM2** gelen NAT kuralı, trafiği yük dengeleyicinin ön uç bağlantı noktası 4222 ' den MyVM2's bağlantı noktası 3389 ' e (RDP bağlantı noktası) yönlendirdiği IÇIN, RDP bağlantısı başarılı olur.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık ihtiyaç kalmadığında yük dengeleyiciyi ve tüm ilgili kaynakları silmek için **Myresourcegrouplb** kaynak grubunu açın ve **kaynak grubunu sil**' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, standart bir genel yük dengeleyici oluşturdunuz. Ağ kaynakları, arka uç sunucuları, bir sistem durumu araştırması ve yük dengeleyici için kurallar oluşturdunuz ve yapılandırdınız. IIS 'yi arka uç VM 'lerine yüklediniz ve yük dengeleyiciyi test etmek için yük dengeleyicinin genel IP adresini kullandınız. Yük dengeleyicideki belirtilen bir bağlantı noktasından, arka uç VM 'deki bir bağlantı noktasına bağlantı noktası iletmeyi ayarlayın ve test edersiniz. 

Azure Load Balancer hakkında daha fazla bilgi edinmek için daha fazla yük dengeleyici öğreticilerine geçin.

> [!div class="nextstepaction"]
> [Azure Load Balancer öğreticileri](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
