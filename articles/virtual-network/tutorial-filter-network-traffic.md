---
title: Ağ trafiğini filtreleme-öğretici-Azure portalı
titlesuffix: Azure Virtual Network
description: Bu öğreticide, Azure portal kullanarak bir ağ güvenlik grubu ile ağ trafiğini bir alt ağa nasıl filtreleyeceğinizi öğreneceksiniz.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
tags: azure-resource-manager
Customer intent: I want to filter network traffic to virtual machines that perform similar functions, such as web servers.
ms.service: virtual-network
ms.devlang: ''
ms.topic: tutorial
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 12/13/2018
ms.author: kumud
ms.openlocfilehash: a700cc83c6bd34f5f726b9cc1c97bd7e1476963b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73519284"
---
# <a name="tutorial-filter-network-traffic-with-a-network-security-group-using-the-azure-portal"></a>Öğretici: Azure portal kullanarak ağ trafiğini ağ güvenlik grubuyla filtreleme

Bir sanal ağ alt ağına gelen ve sanal ağ alt ağından giden ağ trafiğini, bir ağ güvenlik grubu ile filtreleyebilirsiniz. Ağ güvenlik grupları, ağ trafiğini IP adresi, bağlantı noktası ve protokole göre filtreleyen güvenlik kuralları içerir. Güvenlik kuralları bir alt ağda dağıtılmış kaynaklara uygulanır. Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Ağ güvenlik grubu ve güvenlik kuralları oluşturma
> * Bir sanal ağ oluşturma ve ağ güvenlik grubunu alt ağ ile ilişkilendirme
> * Sanal makineleri (VM) bir alt ağa dağıtma
> * Trafik filtrelerini test etme

Tercih ederseniz, bu öğreticiyi [Azure CLI](tutorial-filter-network-traffic-cli.md) veya [PowerShell](tutorial-filter-network-traffic-powershell.md) kullanarak tamamlayabilirsiniz.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="sign-in-to-azure"></a>Azure'da oturum açma

https://portal.azure.com adresinden Azure portalında oturum açın.

## <a name="create-a-virtual-network"></a>Sanal ağ oluşturma

1. Azure portal menüsünde veya **giriş** sayfasında, **kaynak oluştur**' u seçin. 
2. **Ağ**’ı ve sonra **Sanal ağ**’ı seçin.
3. Aşağıdaki bilgileri girin veya seçin, kalan ayarlar için varsayılan değerleri kabul edin ve sonra **Oluştur**’u seçin:

    | Ayar                 | Değer                                              |
    | ---                     | ---                                                |
    | Ad                    | myVirtualNetwork                                   |
    | Adres alanı           | 10.0.0.0/16                                        |
    | Abonelik            | Aboneliğinizi seçin.                          |
    | Kaynak grubu          | **Yeni oluştur**’u seçin ve *myResourceGroup* değerini girin. |
    | Konum                | **Doğu ABD**’yi seçin.                                |
    | Alt Ağ - Ad            | mySubnet                                           |
    | Alt Ağ - Adres aralığı  | 10.0.0.0/24                                        |

## <a name="create-application-security-groups"></a>Uygulama güvenlik grupları oluşturma

Uygulama güvenlik grubu, web sunucuları gibi benzer işlevlere sahip sunucuları birlikte gruplandırmanızı sağlar.

1. Azure portal menüsünde veya **giriş** sayfasında, **kaynak oluştur**' u seçin. 
2. **Market içinde ara** kutusuna *Uygulama güvenlik grubu* girin. Arama sonuçlarında **Uygulama güvenlik grubu** gösterildiğinde bunu seçin, **Her şey**'in altında yeniden **Uygulama güvenlik grubu**'nu seçin ve sonra da **Oluştur**'u seçin.
3. Aşağıdaki bilgileri girin veya seçin ve sonra **Oluştur**’u seçin:

    | Ayar        | Değer                                                         |
    | ---            | ---                                                           |
    | Ad           | myAsgWebServers                                               |
    | Abonelik   | Aboneliğinizi seçin.                                     |
    | Kaynak grubu | **Var olanı kullan**’ı seçin ve sonra **myResourceGroup** öğesini seçin. |
    | Konum       | Doğu ABD                                                       |

4. 3\. adımı yeniden tamamlayın ve aşağıdaki değerleri belirtin:

    | Ayar        | Değer                                                         |
    | ---            | ---                                                           |
    | Ad           | myAsgMgmtServers                                              |
    | Abonelik   | Aboneliğinizi seçin.                                     |
    | Kaynak grubu | **Var olanı kullan**’ı seçin ve sonra **myResourceGroup** öğesini seçin. |
    | Konum       | Doğu ABD                                                       |

## <a name="create-a-network-security-group"></a>Ağ güvenlik grubu oluşturma

1. Azure portal menüsünde veya **giriş** sayfasında, **kaynak oluştur**' u seçin. 
2. **Ağ**'ı ve sonra **Ağ güvenlik grubu**’nu seçin.
3. Aşağıdaki bilgileri girin veya seçin ve sonra **Oluştur**’u seçin:

    |Ayar|Değer|
    |---|---|
    |Ad|myNsg|
    |Abonelik| Aboneliğinizi seçin.|
    |Kaynak grubu | **Mevcut olanı kullan**’ı seçin ve *myResourceGroup* seçeneğini belirleyin.|
    |Konum|Doğu ABD|

## <a name="associate-network-security-group-to-subnet"></a>Ağ güvenlik grubunu alt ağ ile ilişkilendirme

1. Portalın üst kısmındaki *Kaynak, hizmet ve belgeleri arayın* kutusuna *myNsg* yazmaya başlayın. Arama sonuçlarında **myNsg** görüntülendiğinde bunu seçin.
2. **AYARLAR**'ın altında **Alt Ağlar**’ı ve sonra aşağıdaki resimde gösterildiği gibi **+ İlişkilendir**’i seçin:

    ![NSG'yi alt ağ ile ilişkilendirme](./media/tutorial-filter-network-traffic/associate-nsg-subnet.png)

3. **Alt ağı ilişkilendir**'in altında **Sanal ağ**’ı seçin ve sonra da **myVirtualNetwork** öğesini seçin. **Alt ağ**'ı seçin, **mySubnet** öğesini ve sonra da **Tamam**'ı seçin.

## <a name="create-security-rules"></a>Güvenlik kuralları oluşturma

1. Aşağıdaki resimde gösterildiği gibi, **AYARLAR**'ın altında **Gelen güvenlik kuralları**’nı ve sonra da **+ Ekle**’yi seçin:

    ![Gelen güvenlik kuralı ekleme](./media/tutorial-filter-network-traffic/add-inbound-rule.png)

2. 80 ve 443 numaralı bağlantı noktalarına **myAsgWebServers** uygulama güvenlik grubu için izin veren bir güvenlik kuralı oluşturun. **Gelen güvenlik kuralı ekle**'nin altında, aşağıdaki değerleri girin veya seçin, kalan varsayılan değerleri kabul edin ve ardından **Ekle**'yi seçin:

    | Ayar                 | Değer                                                                                                           |
    | ---------               | ---------                                                                                                       |
    | Hedef             | **Uygulama güvenlik grubu**'nu seçin ve sonra da **Uygulama güvenlik grubu** olarak **myAsgWebServers** öğesini seçin.  |
    | Hedef bağlantı noktası aralıkları | 80, 443 girin                                                                                                    |
    | Protokol                | TCP seçin                                                                                                      |
    | Ad                    | Allow-Web-All                                                                                                   |

3. Aşağıdaki değerleri kullanarak 2. adımı yeniden tamamlayın:

    | Ayar                 | Değer                                                                                                           |
    | ---------               | ---------                                                                                                       |
    | Hedef             | **Uygulama güvenlik grubu**'nu seçin ve sonra da **Uygulama güvenlik grubu** olarak **myAsgMgmtServers** öğesini seçin. |
    | Hedef bağlantı noktası aralıkları | 3389 girin                                                                                                      |
    | Protokol                | TCP seçin                                                                                                      |
    | Öncelik                | 110 girin                                                                                                       |
    | Ad                    | İzin Ver-RDP-Tümü                                                                                                   |

    Bu öğreticide, RDP (3389 numaralı bağlantı noktası) *myAsgMgmtServers* uygulama güvenlik grubuna atanmış olan VM için İnternet'te kullanıma sunulur. Üretim ortamlarında 3389 numaralı bağlantı noktasını İnternette kullanıma sunmak yerine VPN veya özel ağ bağlantısı kullanarak yönetmek istediğiniz Azure kaynaklarına bağlamanız önerilir.

1 ile 3 arası adımları tamamladıktan sonra, oluşturduğunuz kuralları gözden geçirin. Listeniz aşağıdaki resimde gösterilen listeye benzer olmalıdır:

![Güvenlik kuralları](./media/tutorial-filter-network-traffic/security-rules.png)

## <a name="create-virtual-machines"></a>Sanal makineler oluşturma

Sanal ağ üzerinde iki sanal makine oluşturun.

### <a name="create-the-first-vm"></a>Birinci sanal makineyi oluşturma

1. Azure portal menüsünde veya **giriş** sayfasında, **kaynak oluştur**' u seçin. 
2. **İşlem**'i seçin ve sonra da **Windows Server 2016 Datacenter**'ı seçin.
3. Aşağıdaki bilgileri girin veya seçin, kalan ayarlar için varsayılan değerleri kabul edin:

    |Ayar|Değer|
    |---|---|
    |Abonelik| Aboneliğinizi seçin.|
    |Kaynak grubu| **Mevcut olanı kullan**’ı seçin ve **myResourceGroup** seçeneğini belirleyin.|
    |Ad|myVmWeb|
    |Konum| **Doğu ABD**’yi seçin.|
    |Kullanıcı adı| Seçtiğiniz bir kullanıcı adını girin.|
    |Parola| Seçtiğiniz bir parolayı girin. Parola en az 12 karakter uzunluğunda olmalı ve [tanımlanmış karmaşıklık gereksinimlerini](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm) karşılamalıdır.|

   

4. Sanal makine için bir boyut seçin ve **Seç** seçeneğini belirleyin.
5. **Ağ**altında, aşağıdaki değerleri seçin ve kalan Varsayılanları kabul edin:

    |Ayar|Değer|
    |---|---|
    |Sanal ağ |**MyVirtualNetwork**öğesini seçin.|
    |NIC ağ güvenlik grubu |**Gelişmiş**'i seçin.|
    |Ortak gelen bağlantı noktaları|**Hiçbiri**' ni seçin. |

6. Sol köşedeki **gözden geçir + oluştur** seçeneğini BELIRLEYIN, VM dağıtımını başlatmak için **Oluştur** ' u seçin.

### <a name="create-the-second-vm"></a>İkinci sanal makineyi oluşturma

1 ile 6 arası adımları yeniden tamamlayın, ama 3. adımda VM'yi *myVmMgmt* olarak adlandırın. Sanal makinenin dağıtılması birkaç dakika sürer. VM dağıtılana kadar sonraki adıma geçmeyin.

## <a name="associate-network-interfaces-to-an-asg"></a>Ağ arabirimlerini ASG ile ilişkilendirme

Portal VM'leri oluştururken, her VM için bir ağ arabirimi oluşturur ve ağ arabirimini VM'ye bağlar. Her VM'nin ağ arabirimini daha önce oluşturduğunuz uygulama güvenlik gruplarından birine ekleyin:

1. Portalın üst kısmındaki *Kaynak, hizmet ve belgeleri arayın* kutusuna *myVmWeb* yazmaya başlayın. Arama sonuçlarında **myVmWeb** VM'si göründüğünde bunu seçin.
2. **AYARLAR**'ın altında **Ağ İletişimi**’ni seçin.  Aşağıdaki resimde gösterildiği gibi **Uygulama güvenlik gruplarını yapılandır**'ı seçin, **Uygulama güvenlik grupları** için **myAsgWebServers** değerini seçin ve sonra da **Kaydet**'i seçin:

    ![ASG ile ilişkilendirme](./media/tutorial-filter-network-traffic/associate-to-asg.png)

3. 1\. ve 2. adımları yeniden tamamlayın; **myVmMgmt** VM'si için arama yapın ve **myAsgMgmtServers** ASG'sini seçin.

## <a name="test-traffic-filters"></a>Trafik filtrelerini test etme

1. *myVmMgmt* VM'sine bağlanın. Portalın üst kısmındaki arama kutusuna *myVmMgmt* yazın. Arama sonuçlarında **myVmMgmt** görüntülendiğinde bunu seçin. **Bağlan** düğmesini seçin.
2. **RDP dosyasını indir**'i seçin.
3. İndirilen rdp dosyasını açın ve **Bağlan**'ı seçin. Sanal makine oluştururken belirttiğiniz kullanıcı adını ve parolayı girin. Sanal makineyi oluştururken girdiğiniz kimlik bilgilerini belirtmek için **Diğer seçenekler**’i ve sonra **Farklı bir hesap kullan** seçeneğini belirlemeniz gerekebilir.
4. **Tamam**’ı seçin.
5. Oturum açma işlemi sırasında bir sertifika uyarısı alabilirsiniz. Uyarı alırsanız, bağlantıya devam etmek için **Evet**’i veya **Bağlan**’ı seçin.

    *myVmMgmt* sanal makinesine bağlı ağ arabiriminin içinde bulunduğu *myAsgMgmtServers* uygulama güvenlik grubuna 3389 numaralı bağlantı noktasının internetten gelmesine izin verildiği için bağlantı başarılı olur.

6. PowerShell oturumunda aşağıdaki komutu girerek *myVmMgmt* VM'sinden *myVmWeb* VM'sine bağlanın:

    ``` 
    mstsc /v:myVmWeb
    ```

    myVmMgmt VM'sinden myVmWeb VM'sine bağlanabilirsiniz çünkü aynı sanal ağ içindeki VM'ler varsayılan olarak herhangi bir bağlantı noktası üzerinden birbiriyle iletişim kurabilir. Öte yandan *myAsgWebServers* güvenlik kuralı internetten 3389 numaralı gelen bağlantı noktasına izin vermediği için *myVmWeb* sanal makinesi ile bir uzak masaüstü bağlantısı oluşturamazsınız ve İnternet'ten tüm kaynaklara gelen trafik varsayılan olarak reddedilir.

7. *myVmWeb* VM'sinde Microsoft IIS'yi yüklemek için, *myVmWeb* VM'sinde PowerShell oturumundan aşağıdaki komutu girin:

    ```powershell
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    ```

8. IIS yüklemesi tamamlandıktan sonra *myVmWeb* sanal makinesinin bağlantısını kesmeniz durumunda *myVmMgmt* VM uzak masaüstü bağlantısında kalırsınız.
9. *myVmMgmt* sanal makinesiyle bağlantıyı kesin.
10. Bilgisayarınızdan Azure portalının üst kısmındaki *Kaynak, hizmet ve belgeleri arayın* kutusuna *myVmWeb* yazmaya başlayın. Arama sonuçlarında **myVmWeb** görüntülendiğinde bunu seçin. VM'nizin **Genel IP adresini** not alın. Aşağıdaki resimde adres olarak 137.135.84.74 gösterilir ama sizin adresiniz farklıdır:

    ![Genel IP adresi](./media/tutorial-filter-network-traffic/public-ip-address.png)
  
11. *myVmWeb* web sunucusuna İnternet'ten erişebildiğinizi onaylamak için bilgisayarınızda bir İnternet tarayıcısı açın ve `http://<public-ip-address-from-previous-step>` sayfasına göz atın. *myVmWeb* sanal makinesine bağlı ağ arabiriminin içinde bulunduğu *myAsgWebServers* uygulama güvenlik grubuna 80 numaralı bağlantı noktasının internetten gelmesine izin verildiği için IIS hoş geldiniz ekranını görürsünüz.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli olmadığında kaynak grubunu ve içerdiği tüm kaynakları silin:

1. Portalın üst kısmındaki *Ara* kutusuna **myResourceGroup** değerini girin. Arama sonuçlarında **myResourceGroup** seçeneğini gördüğünüzde bunu seçin.
2. **Kaynak grubunu sil**'i seçin.
3. *KAYNAK GRUBU ADINI YAZIN:* için **myResourceGroup** girin ve **Sil**’i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide bir ağ güvenlik grubu oluşturdunuz ve bir sanal ağ alt ağı ile ilişkilendirdiniz. Ağ güvenlik grupları hakkında daha fazla bilgi edinmek bkz. [Ağ güvenlik grubuna genel bakış](security-overview.md) ve [Ağ güvenlik grubunu yönetme](manage-network-security-group.md).

Azure, varsayılan olarak trafiği alt ağlar arasında yönlendirir. Bunun yerine, alt ağlar arasındaki trafiği, örneğin, güvenlik duvarı olarak görev yapan bir VM aracılığıyla yönlendirmeyi seçebilirsiniz. Yönlendirme tablosu oluşturma hakkında bilgi edinmek için sonraki öğreticiye geçin.

> [!div class="nextstepaction"]
> [Yönlendirme tablosu oluşturma](./tutorial-create-route-table-portal.md)
