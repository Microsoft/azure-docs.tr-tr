---
title: 'Öğretici: İnternet trafiğini sanal makinelere Yük Dengeleme-Azure portal'
titlesuffix: Azure Load Balancer
description: Bu öğreticide, Azure portalını kullanarak Standard Load Balancer'ın nasıl oluşturulacağı ve yönetileceği gösterilir.
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
Customer intent: I want to create and Standard Load Balancer so that I can load balance internet traffic to VMs and add and remove VMs from the load-balanced set.
ms.service: load-balancer
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/11/2019
ms.author: allensu
ms.custom: seodec18
ms.openlocfilehash: a0887a66b6cb886419f86d93973b991151141e0a
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71327214"
---
# <a name="tutorial-load-balance-internet-traffic-to-vms-using-the-azure-portal"></a>Öğretici: Azure portal kullanarak internet trafiğini VM 'lere Yük Dengeleme

Yük dengeleme, gelen istekleri birden fazla sanal makineye yayarak daha yüksek bir kullanılabilirlik ve ölçek düzeyi sağlar. Bu öğreticide, sanal makinelere internet trafiği dağıtan ve yüksek kullanılabilirlik sağlayan Azure Standart Load Balancer farklı bileşenleri hakkında bilgi edinirsiniz. Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:


> [!div class="checklist"]
> * Azure Load Balancer oluşturma
> * Load Balancer kaynakları oluşturma
> * Sanal makineler oluşturma ve IIS sunucusunu yükleme
> * Load Balancer eylem içinde görüntüle
> * Load Balancer VM 'Leri ekleme ve kaldırma

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun. 

## <a name="sign-in-to-the-azure-portal"></a>Azure portalında oturum açın

[https://portal.azure.com](https://portal.azure.com) adresinden Azure portalında oturum açın.

## <a name="create-a-standard-load-balancer"></a>Standart Yük Dengeleyici oluşturma

Bu bölümde, sanal makinelerin yük dengelemeye yardımcı olan bir Standart Load Balancer oluşturursunuz. Standart Yük Dengeleyici yalnızca Standart Genel IP adresini destekler. Standard Load Balancer oluşturduğunuzda, Standard Load Balancer için ön uç (varsayılan olarak *LoadBalancerFrontend* adını alır) olarak yapılandırılmış yeni bir Standart Genel IP adresi de oluşturmanız gerekir. 

1. Ekranın sol üst kısmında **Kaynak oluştur** > **Ağ** > **Yük Dengeleyici**'ye tıklayın.
2. **Yük dengeleyici oluştur** sayfasının **temel bilgiler** sekmesinde aşağıdaki bilgileri girin veya seçin, kalan ayarlar için varsayılan değerleri kabul edin ve ardından **gözden geçir + oluştur**' u seçin:

    | Ayar                 | Value                                              |
    | ---                     | ---                                                |
    | Subscription               | Aboneliğinizi seçin.    |    
    | Resource group         | **Yeni oluştur** ' u seçin ve metin kutusuna *Myresourcegroupslb* yazın.|
    | Name                   | *myLoadBalancer*                                   |
    | Bölge         | **Batı Avrupa**'yı seçin.                                        |
    | Type          | **Ortak**seçeneğini belirleyin.                                        |
    | SKU           | **Standart**' ı seçin.                          |
    | Ortak IP adresi | **Yeni oluştur**’u seçin. |
    | Ortak IP adresi adı              | Metin kutusuna *Mypublicıp* yazın.   |
    |Kullanılabilirlik bölgesi| **Bölge yedekli**seçeneğini belirleyin.    |

3. **Gözden geçir + oluştur** sekmesinde **Oluştur**' a tıklayın.

   ![Standart Yük Dengeleyici oluşturma](./media/quickstart-load-balancer-standard-public-portal/create-standard-load-balancer.png)

## <a name="create-load-balancer-resources"></a>Load Balancer kaynakları oluşturma

Bu bölümde, bir arka uç adres havuzu ve bir sistem durumu araştırması için Load Balancer ayarlarını yapılandırır ve bir dengeleyici kuralı belirlersiniz.

### <a name="create-a-backend-address-pool"></a>Arka uç adres havuzu oluşturma

Trafiği VM 'lere dağıtmak için bir arka uç adres havuzu, Load Balancer bağlı sanal (NIC) IP adreslerini içerir. Yük Dengeleme internet trafiği için sanal makineleri dahil etmek üzere *Mybackendpool* arka uç adres havuzunu oluşturun.

1. Sol taraftaki menüden **tüm hizmetler** ' i seçin, **tüm kaynaklar**' ı seçin ve ardından kaynaklar listesinden **myloadbalancer** ' ye tıklayın.
2. **Ayarlar** bölümünde **Arka uç havuzları**’na ve sonra **Ekle**’ye tıklayın.
3. **Arka uç Havuzu Ekle** sayfasında, ad için, arka uç havuzunuzun adı olarak *mybackendpool*yazın ve ardından **Ekle**' yi seçin.

### <a name="create-a-health-probe"></a>Durum araştırması oluşturma

Load Balancer uygulamanızın durumunu izlemesine izin vermek için, bir sistem durumu araştırması kullanırsınız. Sistem durumu araştırması, sistem durumu denetimlerine olan yanıtına göre Load Balancer dönüşten VM 'Leri dinamik olarak ekler veya kaldırır. Sanal makinelerin durumunu izlemek için *myHealthProbe* durum araştırması oluşturun.

1. Sol taraftaki menüden **tüm hizmetler** ' i seçin, **tüm kaynaklar**' ı seçin ve ardından kaynaklar listesinden **myloadbalancer** ' ye tıklayın.
2. **Ayarlar** bölümünde **Durum araştırmaları**’na ve sonra **Ekle**’ye tıklayın.
3. Durum araştırması oluşturmak için şu değerleri kullanın:
     
    | Ayar | Value |
    | ------- | ----- |
    | Name | *Myhealtharaştırması*girin. |
    | Protocol | **Http**'yi seçin. |
    | Port | *80*girin.|
    | Interval | Yoklama denemeleri arasındaki saniye cinsinden **Aralık** sayısı için *15* girin. |
    | İyi durumda olmayan eşik | Bir VM sağlıksız kabul edilmeden önce gerçekleşmesi gereken **sağlıksız eşik** veya arka arkaya araştırma hatası sayısı için *2* ' yi seçin.|
    
4. **Tamam**’ı seçin.

### <a name="create-a-load-balancer-rule"></a>Yük Dengeleyici kuralı oluşturma

Trafiğin sanal makinelere dağıtımını tanımlamak için bir Yük Dengeleyici kuralı kullanılır. Gerekli kaynak ve hedef bağlantı noktalarının yanı sıra gelen trafik için ön uç IP yapılandırması ve trafiği almak için arka uç IP havuzu tanımlamanız gerekir. Ön uç *Frontendloadbalancer* 80 numaralı bağlantı noktasını dinlemek ve yük dengeli ağ trafiğini *mybackendpool* bağlantı noktası 80 ' i kullanarak arka uç adres havuzuna göndermek Için *myloadbalancerruleweb* Load Balancer kuralını oluşturun.

1. Sol taraftaki menüden **tüm hizmetler** ' i seçin, **tüm kaynaklar**' ı seçin ve ardından kaynaklar listesinden **myloadbalancer** ' ye tıklayın.
2. **Ayarlar** bölümünde **Yük dengeleme kuralları**’na ve sonra **Ekle**’ye tıklayın.
3. Yük Dengeleme kuralını yapılandırmak için şu değerleri kullanın:

    | Ayar | Value |
    | ------- | ----- |
    | Name | *Myhttprule*girin. |
    | Protocol | **TCP**' yi seçin. |
    | Port | *80*girin.|
    | Arka uç bağlantı noktası | *80*girin. |
    | Arka uç havuzu | *Mybackendpool*öğesini seçin.|
    | Durum araştırması | *Myhealtharaştırması*' ni seçin. |
    
4. Diğer varsayılan ayarları olduğu gibi bırakın ve **Tamam**’ı seçin.

## <a name="create-backend-servers"></a>Arka uç sunucular oluşturma

Bu bölümde, bir sanal ağ oluşturur, Load Balancer arka uç havuzu için üç sanal makine oluşturur ve sonra Load Balancer test etmenize yardımcı olması için sanal makinelere IIS yüklersiniz.

### <a name="create-a-virtual-network"></a>Sanal ağ oluşturma

1. Ekranın sol üst kısmında **kaynak** > oluştur**ağ** > **sanal ağ**' ı seçin.
2. **Sanal ağ oluştur**' da bu bilgileri girin veya seçin:

    | Ayar | Value |
    | ------- | ----- |
    | Name | *myVNet* yazın. |
    | Adres alanı | *10.1.0.0/16*girin. |
    | Subscription | Aboneliğinizi seçin.|
    | Resource group | Mevcut kaynak- *Myresourcegroupslb*öğesini seçin. |
    | Location | **Batı Avrupa**'yı seçin.|
    | Alt ağ adı | *myBackendSubnet* yazın. |
    | Alt Ağ - Adres aralığı | *10.1.0.0/24*girin. |
    
3. Varsayılan değerleri bırakın ve **Oluştur**' u seçin.

### <a name="create-virtual-machines"></a>Sanal makineler oluşturma

Standart Load Balancer, yalnızca arka uç havuzunda standart IP adreslerine sahip VM 'Leri destekler. Bu bölümde, standart bir genel IP adresi olan üç farklı bölgede (*bölge 1*, *bölge 2*ve *Bölge 3*) Standard 'ın arka uç havuzuna eklenen üç VM (*myVM1*, *myVM2*ve *myVM3*) oluşturacaksınız Daha önce oluşturulan Load Balancer.

1. Portalda sol tarafta seçin **kaynak Oluştur** > **işlem** > **Windows Server 2016 Datacenter**. 
   
1. İçinde **sanal makine oluşturma**yazın veya aşağıdaki değerleri seçin **Temelleri** sekmesinde:
   - Abonelik > **kaynak grubu**: **Myresourcegroupslb**öğesini seçin.
   - **Örnek ayrıntıları** > **sanal makine adı**: *MyVM1*yazın.
   - **Örnek ayrıntıları** > **bölgesi** > **Batı Avrupa**seçin.
   - **Örnek ayrıntıları** > **kullanılabilirlik seçenekleri** > **kullanılabilirlik alanları**' nı seçin. 
   - **Örnek ayrıntıları** > **kullanılabilirlik bölgesi** > **1**' i seçin.
  
1. **Ağ** sekmesini seçin veya ileri ' yi **seçin: Diskler**ve sonra **ileri: Ağ**iletişimi. 
   
   - Aşağıdaki seçili olduğundan emin olun:
       - **Sanal ağ**: **myvnet**
       - **Alt ağ**: **mybackendsubnet**
       - **Genel ıp** > **Yeni oluştur**' u seçin ve **genel IP adresi oluştur** penceresinde **SKU**için **Standart**' ı seçin ve **kullanılabilirlik bölgesi**için **bölge yedekli** ' ı seçin.
      
   - Yeni bir ağ güvenlik grubu (NSG), güvenlik duvarı, türü altında oluşturulacak **ağ güvenlik grubu**seçin **Gelişmiş**. 
       1. İçinde **yapılandırma ağ güvenlik grubu** alanın, Seç **Yeni Oluştur**. 
       1. *Mynetworksecuritygroup*yazın ve **Tamam**' ı seçin.

   - VM 'yi Load Balancer arka uç havuzunun bir parçası yapmak için aşağıdaki adımları izleyin:
        - **Yük dengelemesinde**, **Bu sanal makineyi var olan bir yük dengeleme çözümünün arkasına koymak**için **Evet**' i seçin.
        - **Yük Dengeleme ayarları**' nda, **Yük dengeleme seçenekleri**için **Azure yük dengeleyici**' ni seçin.
        - **Yük dengeleyici seçin**için *myloadbalancer*. 
1. Seçin **Yönetim** sekmesinde veya seçin **sonraki** > **Yönetim**. Altında **izleme**ayarlayın **önyükleme tanılaması** için **kapalı**. 
1. **İncele ve oluştur**’u seçin.   
1. Ayarları gözden geçirin ve ardından **Oluştur**.
1. Sırasıyla **kullanılabilirlik alanı** **2** ve **3** *' te*standart SKU genel IP *adresi ve diğer* tüm ayarlar *myVM1*ile aynı olan iki ek VM oluşturmak için adımları izleyin.  

### <a name="create-network-security-group-rule"></a>Ağ güvenlik grubu kuralı oluşturma

Bu bölümde, HTTP kullanarak gelen bağlantılara izin veren bir ağ güvenlik grubu kuralı oluşturacaksınız.

1. Sol taraftaki menüden **tüm hizmetler** ' i seçin, **tüm kaynaklar**' ı seçin ve ardından kaynaklar listesinden **myresourcegroupslb** kaynak grubunda bulunan **mynetworksecuritygroup** ' a tıklayın.
2. **Ayarlar**’ın altında **Gelen güvenlik kuralları**’na ve sonra **Ekle**’ye tıklayın.
3. 80 numaralı bağlantı noktasını kullanarak gelen HTTP bağlantılarına izin vermek için *myHTTPRule* adlı gelen güvenlik kuralı için şu değerleri girin:
    - **Kaynak** için *Hizmet Etiketi*.
    - **Kaynak hizmet etiketi** için *İnternet*
    - **Hedef bağlantı noktası aralıkları** için *80*
    - **Protokol** için *TCP*
    - **Eylem** için *İzin Ver*
    - **Öncelik** için *100*
    - Ad için *myHTTPRule*
    - Açıklama için *HTTP’ye İzin Ver*
4. **Add (Ekle)** seçeneğini belirleyin.

### <a name="install-iis-on-vms"></a>VM’lere IIS yükleme

1. Sol taraftaki menüden **tüm hizmetler** ' i seçin, **tüm kaynaklar**' ı seçin ve ardından kaynaklar listesinden *Myresourcegroupslb* kaynak grubunda bulunan **myVM1** ' a tıklayın.
2. Sanal makineye yönelik RDP için **Genel Bakış** sayfasında **Bağlan**’a tıklayın.
3. **Sanal makineye bağlanın** açılır penceresinde **RDP Dosyasını İndir**'i seçin ve indirilen RDP dosyasını açın.
4. **Uzak Masaüstü Bağlantısı** penceresinde **Bağlan**'a tıklayın.
5. VM oluşturma işlemleri sırasında belirlediğiniz kimlik bilgilerini kullanarak VM'de oturum açın. *myVM1* adlı sanal makinede uzak masaüstü oturumu başlatılır.
6. Sunucu masaüstünde **Windows Yönetimsel Araçları**>**Windows PowerShell** bölümüne gidin.
7. PowerShell Penceresinde aşağıdaki komutları çalıştırarak IIS sunucusunu yükleyin, varsayılan iisstart.htm dosyasını kaldırın ve ardından VM’nin adını gösteren yeni bir iisstart.htm dosyasını ekleyin:

   ```azurepowershell-interactive
    
    # install IIS server role
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    # Add a new htm file that displays server name
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
   ```
6. *myVM1* ile RDP oturumunu kapatın.
7. IIS’yi ve *myVM2* ve *myVM3*’teki güncelleştirilmiş iisstart.htm dosyasını yüklemek için 1 ile 6 arasındaki adımları tekrarlayın.

## <a name="test-the-load-balancer"></a>Load Balancer test etme
1. **Genel Bakış** ekranında Yük Dengeleyici için genel IP adresini bulun. Sol taraftaki menüden **tüm hizmetler** ' i seçin, **tüm kaynaklar**' ı seçin ve **mypublicıp**' ye tıklayın.

2. Genel IP adresini kopyalayıp tarayıcınızın adres çubuğuna yapıştırın. IIS Web sunucusunun varsayılan sayfası, tarayıcıda görüntülenir.

      ![IIS Web sunucusu](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

Uygulamanızı çalıştıran üç sanal makineye trafik dağıtmayı Load Balancer görmek için Web tarayıcınızı yenilemeyi zorlayabilirsiniz.

## <a name="remove-or-add-vms-from-the-backend-pool"></a>Arka uç havuzunda VM'leri kaldırma veya ekleme
Uygulamanızı çalıştıran VM’lerde işletim sistemi güncelleştirmelerini yükleme gibi bakım işlemleri gerçekleştirmeniz gerekebilir. Uygulamanıza gelen trafiği fazla trafiği yönetmek için ek VM’lere ihtiyaç duyabilirsiniz. Bu bölümde, Load Balancer bir VM 'yi (*myVM1*) nasıl kaldırabilmeniz veya ekleneceği gösterilmektedir.

### <a name="remove-vm-from-a-backend-pool"></a>VM 'yi arka uç havuzundan kaldırma
Arka uç havuzundan *myVM1* kaldırmak için aşağıdaki adımları izleyin:

1. Sol taraftaki menüden **tüm hizmetler** ' i seçin, **tüm kaynaklar**' ı seçin ve ardından kaynaklar listesinden **myloadbalancer** ' ye tıklayın.
2. **Ayarlar**'ın altında **Arka uç havuzları**'na tıklayın, ardından arka uç havuzunun listesinde **myBackendPool**'a tıklayın.
3. **Mybackendpool** sayfasında, *VM1* öğesini kaldırmak için *myVM1*' yi görüntüleyen satırın sonundaki Sil simgesini seçin ve ardından **Kaydet**' e tıklayın.

Artık *myVM1* arka uç adres havuzunda yer almadığından, *myVM1* üzerinde yazılım güncelleştirmelerini yükleme gibi tüm bakım görevlerini gerçekleştirebilirsiniz. *VM1*yokluğunda, yük artık *myVM2* ve *myVM3*üzerinden dengelenir. 

### <a name="add-vm-to-a-backend-pool"></a>Bir arka uç havuzuna VM ekleme
Arka uç havuzuna *myVM1* geri eklemek için aşağıdaki adımları izleyin:

1. Sol taraftaki menüden **tüm hizmetler** ' i seçin, **tüm kaynaklar**' ı seçin ve ardından kaynaklar listesinden **myVM1** ' ı seçin.
2. **VM1** sayfasında, **Ayarlar**altında **ağ**' ı seçin.
3. **Ağ** sayfasında, **Yük Dengeleme** sekmesini seçin ve ardından **Yük Dengeleme Ekle**' yi seçin.
4. **Yük Dengeleme ekleme** sayfasında, şunları yapın:
   1. **Yük dengeleme seçenekleri**için **Azure yük dengeleyici**' yi seçin.
   2. **Yük dengeleyici Seç**Için *myloadbalancer*' ı seçin.
   3. **Bir arka uç havuzu seçin**Için *mybackendpool*' u seçin. 

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli olmadığında kaynak grubunu, Load Balancer ve tüm ilgili kaynakları silin. Bunu yapmak için, Load Balancer içeren *Myçözümlenmiş Ucegroupslb* kaynak grubunu seçin ve **Sil**' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, bir Standart Load Balancer oluşturdunuz, bu sanal makineye bağlı VM 'Ler, Load Balancer trafik kuralı, sistem durumu araştırması ve ardından Load Balancer test edilmiştir. Ayrıca, yük dengeli kümeden bir VM kaldırdınız ve VM'yi arka uç adres havuzuna geri eklediniz. Azure Load Balancer hakkında daha fazla bilgi almak için Azure Load Balancer öğreticisine devam edin.

> [!div class="nextstepaction"]
> [Azure Load Balancer öğreticileri](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
