---
title: Sanal ağ eşlemesi ile sanal ağları bağlama - öğretici - Azure portalı | Microsoft Docs
description: Bu öğreticide, Azure portalını kullanarak sanal ağ eşlemesi ile sanal ağların nasıl bağlanacağını öğreneceksiniz.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
Customer intent: I want to connect two virtual networks so that virtual machines in one virtual network can communicate with virtual machines in the other virtual network.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 08/16/2018
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: b32f3762f2546a4d4956bf38c914173657e9d3da
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499894"
---
# <a name="tutorial-connect-virtual-networks-with-virtual-network-peering-using-the-azure-portal"></a>Öğretici: Azure portalını kullanarak sanal ağ eşlemesi ile sanal ağları bağlama

Sanal ağ eşlemesi ile sanal ağları birbirine bağlayabilirsiniz. Bu sanal ağlar aynı bölgede veya farklı bölgelerde (Genel Sanal Ağ Eşleme olarak da bilinir) olabilir. Sanal ağlar eşlendikten sonra, kaynaklar aynı sanal ağ üzerindeymiş gibi, aynı gecikme süresi ve bant genişliği ile her iki sanal ağdaki kaynaklar birbiriyle iletişim kurabilir. Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * İki sanal ağ oluşturma
> * Sanal ağ eşlemesi iki sanal ağı bağlama
> * Her sanal ağa sanal makine (VM) dağıtma
> * Sanal makineler arasında iletişim

Tercih ederseniz, [Azure CLI](tutorial-connect-virtual-networks-cli.md) veya [Azure PowerShell](tutorial-connect-virtual-networks-powershell.md) kullanarak bu öğreticiyi tamamlayabilirsiniz.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="log-in-to-azure"></a>Azure'da oturum açma 

https://portal.azure.com adresinden Azure portalında oturum açın.

## <a name="create-virtual-networks"></a>Sanal ağlar oluşturma

1. Azure portal menüsünde veya **giriş** sayfasında, **kaynak oluştur**' u seçin.
2. **Ağ**’ı ve sonra **Sanal ağ**’ı seçin.
3. **Temel bilgiler** sayfasında, aşağıdaki bilgileri girin veya seçin, kalan ayarlar için varsayılan değerleri kabul edin:

    |Ayar|Değer|
    |---|---|
    |Abonelik| Aboneliğinizi seçin.|
    |Kaynak grubu| **Yeni oluştur**’u seçin ve *myResourceGroup* değerini girin.|
    |Bölge| **Doğu ABD**’yi seçin.|
    |Ad|myVirtualNetwork1|

4. **IP adresleri** sayfasında **Adres alanı** alanı için 10.0.0.0/16 girin. Aşağıdaki **alt ağ ekle** düğmesine tıklayın ve alt ağ **adı** Için Subnet1 ve IP **adresi aralığı**için 10.0.0.0/24 girin.
   
5. Aşağıdaki değişikliklerle birlikte 1.-3. adımları tekrar tamamlayın:

    |Ayar|Değer|
    |---|---|
    |Ad|myVirtualNetwork2|
    |Adres alanı|10.1.0.0/16|
    |Kaynak grubu| **Mevcut olanı kullan**’ı seçin ve **myResourceGroup** seçeneğini belirleyin.|
    |Alt Ağ Adresi aralığı|10.1.0.0/24|

## <a name="peer-virtual-networks"></a>Sanal ağları eşleme

1. Azure portalının üst kısmındaki Arama kutusuna *MyVirtualNetwork1* yazmaya başlayın. Arama sonuçlarında **myVirtualNetwork1** görüntülendiğinde bunu seçin.
2. Ayarlar **altında, eşlemeler**'i seçin ve ardından aşağıdaki resimde gösterildiği gibi **Ekle**' yi seçin:

    ![Eşleme oluşturma](./media/tutorial-connect-virtual-networks-portal/create-peering.png)

3. Aşağıdaki bilgileri girin veya seçin, kalan ayarlar için varsayılan değerleri kabul edin ve sonra **Tamam**’ı seçin.

    |Ayar|Değer|
    |---|---|
    |MyVirtualNetwork1 'ten uzak sanal ağa eşleme adı|myVirtualNetwork1-myVirtualNetwork2-sayfa ilk yüklendiğinde, burada "uzak sanal ağ" ifadesini görürsünüz. Uzak sanal ağı seçtikten sonra, "uzak sanal ağ" ifadesi uzak sanal ağın adıyla birlikte değişir.|
    |Abonelik| Aboneliğinizi seçin.|
    |Sanal ağ|myVirtualNetwork2- *myVirtualNetwork2* sanal ağını seçmek için **sanal ağ**' ı seçin ve ardından **myVirtualNetwork2 (myresourcegroup)** seçeneğini belirleyin. Aynı bölgede veya farklı bir bölgede bulunan bir sanal ağı seçebilirsiniz.|
    |MyVirtualNetwork2 ile myVirtualNetwork1 arasında eşleme adı|myVirtualNetwork2-myVirtualNetwork1|

    ![Eşleme ayarları](./media/tutorial-connect-virtual-networks-portal/peering-settings-bidirectional.png)

    Aşağıdaki resimde gösterildiği gibi, **eşleme durumu** *bağlı*olur:

    ![Eşleme durumu](./media/tutorial-connect-virtual-networks-portal/peering-status-connected.png)

    Durumu görmüyorsanız tarayıcınızı yenileyin.

## <a name="create-virtual-machines"></a>Sanal makineler oluşturma

Sonraki bir adımda aralarında iletişim kurabilmeniz için her sanal ağ üzerinde bir sanal makine oluşturun.

### <a name="create-the-first-vm"></a>Birinci sanal makineyi oluşturma

1. Azure portal menüsünde veya **giriş** sayfasında, **kaynak oluştur**' u seçin.
2. **İşlem**'i seçin ve sonra da **Windows Server 2016 Datacenter**'ı seçin. Farklı bir işletim sistemi seçebilirsiniz, ancak geri kalan adımlarda, **Windows Server 2016 Datacenter** seçeneğini belirlediğiniz varsayılır. 
3. **Temel Bilgiler** için aşağıdaki bilgileri girin veya seçin, kalan ayarlar için varsayılan değerleri kabul edin ve sonra **Oluştur**’u seçin:

    |Ayar|Değer|
    |---|---|
    |Kaynak grubu| **Mevcut olanı kullan**’ı seçin ve **myResourceGroup** seçeneğini belirleyin.|
    |Ad|myVm1|
    |Konum| **Doğu ABD**’yi seçin.|
    |Kullanıcı adı| Seçtiğiniz bir kullanıcı adını girin.|
    |Parola| Seçtiğiniz bir parolayı girin. Parola en az 12 karakter uzunluğunda olmalı ve [tanımlanmış karmaşıklık gereksinimlerini](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm) karşılamalıdır.|
   
4. **Boyut** seçeneği IÇIN bir VM boyutu seçin.
5. **Ağ**altında için aşağıdaki değerleri seçin:

    |Ayar|Değer|
    |---|---|
    |Sanal ağ| myVirtualNetwork1-zaten seçili değilse, **sanal ağ** ' ı seçin ve ardından **myVirtualNetwork1**' yi seçin.|
    |Alt ağ| Subnet1-zaten seçili değilse **alt ağ** ' ı seçin ve ardından **Subnet1**' yi seçin.|
    

    ![Sanal makine ayarları](./media/tutorial-connect-virtual-networks-portal/virtual-machine-settings.png)
   
6. **Ağ**' ı seçin. **Ortak gelen bağlantı noktaları** için **Seçili bağlantı noktalarına izin ver** seçeneğini belirleyin. Bunun altındaki **gelen bağlantı noktaları seçin** seçeneği için **RDP** 'yi seçin. 

7. VM dağıtımını başlatmak için sol köşedeki sağ alt köşedeki **gözden geçir + oluştur** düğmesini seçin.

### <a name="create-the-second-vm"></a>İkinci sanal makineyi oluşturma

Aşağıdaki değişikliklerle birlikte 1.-6. adımları tekrar tamamlayın:

|Ayar|Değer|
|---|---|
|Ad | myVm2|
|Sanal ağ | myVirtualNetwork2|

Sanal makinelerin oluşturulması birkaç dakika sürebilir. Her iki sanal makine de oluşturulmadan kalan adımlara devam etmeyin.

## <a name="communicate-between-vms"></a>Sanal makineler arasında iletişim

1. Portalın üst kısmındaki *Arama* kutusuna *myVm1* yazmaya başlayın. Arama sonuçlarında **myVm1** görüntülendiğinde bunu seçin.
2. Aşağıdaki resimde gösterildiği gibi, *Bağlan*’ı seçerek **myVm1** sanal makinesine uzak masaüstü bağlantısı oluşturun:

    ![Sanal makineye bağlanma](./media/tutorial-connect-virtual-networks-portal/connect-to-virtual-machine.png)  

3. Sanal makineye bağlanmak için indirilen RDP dosyasını açın. İstendiğinde **Bağlan**’ı seçin.
4. Sanal makineyi oluştururken belirttiğiniz kullanıcı adını ve parolayı girin (sanal makineyi oluştururken girdiğiniz kimlik bilgilerini belirtmek için **Diğer seçenekler**’i ve sonra **Farklı bir hesap kullan**’ı seçmeniz gerekebilir), ardından **Tamam**’ı seçin.
5. Oturum açma işlemi sırasında bir sertifika uyarısı alabilirsiniz. Bağlantıya devam etmek için **Evet**’i seçin.
6. Sonraki bir adımda, *myVm1* sanal makinesinden *myVm2* sanal makinesiyle iletişim kurmak için ping kullanılır. Ping, varsayılan olarak Windows Güvenlik Duvarı üzerinden reddedilen İnternet Denetim İletisi Protokolü’nü (ICMP) kullanır. *myVm1* sanal makinesinde, Windows güvenlik duvarı üzerinden ICMP’yi etkinleştirin; böylece sonraki bir adımda PowerShell kullanarak *myVm2* sanal makinesinden bu sanal makineye ping komutu gönderebilirsiniz:

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```
    
    Bu öğreticide sanal makineler arasında iletişim kurmak için ping kullanılsa da, üretim dağıtımları için Windows Güvenlik Duvarı üzerinden ICMP’ye izin verilmesi önerilmez.

7. *myVm2* sanal makinesine bağlanmak için, *myVm1* sanal makinesinde bir komut isteminden aşağıdaki komutu girin:

    ```
    mstsc /v:10.1.0.4
    ```
    
8. *myVm1* sanal makinesinde ping’i etkinleştirdiğinizden şimdi IP adresine göre ping komutu gönderebilirsiniz:

    ```
    ping 10.0.0.4
    ```
    
9. Hem *myVm1* hem de *myVm2* sanal makinesine yönelik RDP oturumlarınızın bağlantısını kesin.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli olmadığında kaynak grubunu ve içerdiği tüm kaynakları silin: 

1. Portalın üst kısmındaki *Ara* kutusuna **myResourceGroup** değerini girin. Arama sonuçlarında **myResourceGroup** seçeneğini gördüğünüzde bunu seçin.
2. **Kaynak grubunu sil**'i seçin.
3. *KAYNAK GRUBU ADINI YAZIN:* için **myResourceGroup** girin ve **Sil**’i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, sanal ağ eşlemesi ile aynı Azure bölgesindeki iki ağın nasıl bağlanacağını öğrendiniz. Farklı [desteklenen bölgelerde](virtual-network-manage-peering.md#cross-region) ve [farklı Azure aboneliklerinde](create-peering-different-subscriptions.md#portal) sanal ağları eşleyebilir ve eşleme ile [hub ve bağlı bileşen ağ tasarımları](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering) oluşturabilirsiniz. Sanal ağ eşlemesi hakkında daha fazla bilgi için bkz. [Sanal ağ eşlemesine genel bakış](virtual-network-peering-overview.md) ve [Sanal ağ eşlemelerini yönetme](virtual-network-manage-peering.md).

Kendi bilgisayarınızı VPN üzerinden bir sanal ağa bağlamak ve bir sanal ağdaki veya eşlenmiş sanal ağlardaki kaynaklarla etkileşim kurmak için bkz. [Bilgisayarınızı sanal ağa bağlama](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
