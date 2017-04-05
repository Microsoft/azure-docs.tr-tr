---
title: "İlk Azure Sanal Ağınızı oluşturma | Microsoft Docs"
description: "Azure Sanal Ağı (VNet) oluşturmayı, iki sanal makineyi (VM) sanal ağa bağlamayı ve VM’lere bağlamayı öğrenebilirsiniz."
services: virtual-network
documentationcenter: 
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/27/2016
ms.author: jdial
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: c731099cb91512f3bf0ecc2ffa5258788c90cd1b
ms.lasthandoff: 03/31/2017


---

# <a name="create-your-first-virtual-network"></a>İlk sanal ağınızı oluşturma

İki alt ağa sahip bir sanal ağ (VNet) oluşturmayı, iki sanal makine (VM) oluşturmayı ve aşağıdaki resimde gösterildiği gibi her bir VM’yi alt ağların birine bağlamayı öğrenin:

![Sanal ağ diyagramı](./media/virtual-network-get-started-vnet-subnet/vnet-diagram.png)

Azure sanal ağ (VNet) buluttaki kendi ağınızın bir gösterimidir. Azure ağ ayarlarınızı kontrol edebilir ve DHCP adres bloklarını, DNS ayarlarını, güvenlik ilkelerini ve yönlendirmeyi tanımlayabilirsiniz. Sanal ağ kavramları hakkında daha fazla bilgi için, [Sanal Ağa genel bakış](virtual-networks-overview.md) makalesini okuyun. Resimde gösterilen kaynakları oluşturmak için aşağıdaki adımları tamamlayın:

1. [İki alt ağı olan bir sanal ağ oluşturma](#create-vnet)
2. [Her biri bir ağ arabirimine (NIC) sahip iki VM oluşturma](#create-vms) ve her NIC ile bir ağ güvenlik grubu (NSG) ilişkilendirme
3. [VM’lerden gelen ve giden bağlantı](#connect-to-from-vms)
4. [Tüm kaynakları silme](#delete-resources). Bu uygulamada oluşturulan kaynakların bazıları için, hazırlanırlarken ücret uygulanır. Ücretleri en aza indirmek için, uygulamayı tamamladıktan sonra, oluşturduğunuz kaynakları silmek için bu bölümdeki adımları tamamlamayı unutmayın.

Bu makaledeki adımları tamamladıktan sonra sanal ağı nasıl kullanabileceğinize dair temel bir anlayışa sahip olacaksınız. Sonraki adımlar, sanal ağları daha ayrıntılı bir düzeyde nasıl kullanacağınızla ilgili daha fazla bilgi edinmeniz için verilmiştir.

## <a name="create-vnet"></a>İki alt ağa sahip bir sanal ağ oluşturma

İki alt ağa sahip bir sanal ağ oluşturmak için, aşağıdaki adımları tamamlayın. Farklı alt ağlar genellikle alt ağlar arasındaki trafik akışını denetlemek için kullanılır.

1. [Azure Portal](<https://portal.azure.com>)’da oturum açın. Henüz bir hesabınız yoksa, [bir aylık ücretsiz denemeye](https://azure.microsoft.com/free) kaydolabilirsiniz. 
2. Portalın **Favoriler** bölmesinde, **Yeni** öğesine tıklayın.
3. **Yeni** dikey penceresinde, **Ağ** öğesine tıklayın. Aşağıdaki resimde gösterildiği şekilde, **Ağ** dikey penceresinde **Sanal ağ** öğesine tıklayın:

    ![Sanal ağ diyagramı](./media/virtual-network-get-started-vnet-subnet/virtual-network.png)

4.  **Sanal ağ** dikey penceresinde, *Resource Manager*’ı dağıtım modeli olarak seçilmiş halde bırakın ve **Oluştur**’a tıklayın.
5.  Ekrana gelen **Sanal ağ oluşturma dikey penceresinde**, aşağıdaki değerleri girin, ardından **Oluştur**’a tıklayın:

    |**Ayar**|**Değer**|**Ayrıntılar**|
    |---|---|---|
    |**Ad**|*MyVNet*|Ad, kaynak grubu içinde benzersiz olmalıdır.|
    |**Adres alanı**|*10.0.0.0/16*|CIDR gösteriminde istediğiniz herhangi bir adres alanını belirtebilirsiniz.|
    |**Alt ağ adı**|*Ön uç*|Alt ağ adı sanal ağ içinde benzersiz olmalıdır.|
    |**Alt ağ adres aralığı**|*10.0.0.0/24*| Belirttiğiniz aralığın, sanal ağ için tanımladığınız adres alanı içinde olması gerekir.|
    |**Abonelik**|*[Aboneliğiniz]*|Sanal ağın oluşturulacağı bir abonelik seçin. Bir sanal ağ, tek bir abonelik içinde bulunabilir.|
    |**Kaynak grubu**|**Yeni oluştur:** *MyRG*|Bir kaynak grubu oluşturun. Kaynak grubu adı, seçili abonelik içinde benzersiz olmalıdır. Kaynak grupları hakkında daha fazla bilgi için, [Resource Manager](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-groups)’a genel bakış makalesini okuyun.|
    |**Konum**|*Batı ABD*| Genellikle, fiziksel konumunuza en yakın konum seçilir.|

    Sanal ağı oluşturmak birkaç saniye sürer. Oluşturulduktan sonra, Azure portalı panosunu görürsünüz.

6. Oluşturulan sanal ağ ile, Azure portalı **Sık Kullanılanlar** bölmesinde, **Tüm kaynaklar**’a tıklayın. **Tüm kaynaklar** dikey penceresinde **MyVNet** sanal ağına tıklayın. Seçili abonelikte zaten çeşitli kaynaklar varsa, sanal ağa kolaylıkla erişmek için **Ada göre filtrele...** kutusuna *MyVNet* girebilirsiniz.
7. **MyVNet** dikey penceresi açılır ve aşağıdaki resimde gösterildiği gibi sanal ağ hakkındaki bilgileri görüntüler:

    ![Sanal ağ diyagramı](./media/virtual-network-get-started-vnet-subnet/myvnet.png)

8. Önceki resimde gösterildiği gibi, **Alt ağlar** öğesine tıklayarak sanal ağ içindeki alt ağların bir listesini görüntüleyin. Mevcut olan tek alt ağ, 5. adımda oluşturduğunuz alt ağ olan **Ön uç**’tur.
9. MyVNet - Alt ağlar dikey penceresinde, aşağıdaki bilgilerle bir alt ağ oluşturmak için **+ Alt ağ** öğesine tıklayın ve **Tamam**’a tıklayarak alt ağı oluşturun:

    |**Ayar**|**Değer**|**Ayrıntılar**|
    |---|---|---|
    |**Ad**|*Arka uç*|Ad, sanal ağ içinde benzersiz olmalıdır.|
    |**Adres aralığı**|*10.0.1.0/24*|Belirttiğiniz aralığın, sanal ağ için tanımladığınız adres alanı içinde olması gerekir.|
    |**Ağ güvenlik grubu** ve **Yol tablosu**|*Hiçbiri* (varsayılan)|Ağ güvenlik grupları (NSG’ler) b makalenin sonraki bölümlerinde ele alınmıştır. Kullanıcı tanımlı yollar hakkında daha fazla bilgi için, [Kullanıcı tanımlı yollar](virtual-networks-udr-overview.md) makalesini okuyun.|

10. Yeni alt ağ sanal ağa eklendikten sonra, **MyVNet – Alt ağlar** dikey penceresini kapatabilirsiniz, ardından **Tüm kaynakları** dikey penceresini kapatabilirsiniz.

## <a name="create-vms"></a>Sanal makineler oluşturma

Sanal ağ ve alt ağlar oluşturulduktan sonra, VM’leri oluşturabilirsiniz. Bu alıştırmada, her iki VM de Windows Server işletim sistemini çalıştırır, ancak çeşitli Linux dağıtımları da dahil olmak üzere Azure tarafından herhangi bir işletim sistemini çalıştırabilirler.

### <a name="create-web-server-vm"></a>Web sunucusu VM’sini oluşturma

Web sunucusu VM’sini oluşturmak için, aşağıdaki adımları tamamlayın:

1. Azure portalı sık kullanılanlar bölmesinde, **Yeni**, **İşlem** öğelerine, ardından **Windows Server 2016 Datacenter**’a tıklayın.
2. **Windows Server 2016 Datacenter** dikey penceresinde, **Oluştur**’a tıklayın.
3. Ekrana gelen **Temeller** dikey penceresinde, aşağıdaki değerleri girin veya seçin ve **Tamam**’a tıklayın:

    |**Ayar**| **Değer**|**Ayrıntılar**|
    |---|---|---|
    |**Ad**|*MyWebServer*|Bu VM, İnternet kaynaklarının bağlı olduğu bir web sunucusu işlevi görür.|
    |**VM disk türü**|*SSD*|
    |**Kullanıcı adı**|*Tercih ettiğiniz*|
    |**Parola ve Parolayı onayla**|*Tercih ettiğiniz*|
    | **Abonelik**|*<Your subscription>*|Abonelik, bu makalenin [İki alt ağa sahip bir sanal ağ oluşturma](#create-vnet) bölümünde 5. adımda seçtiğiniz abonelikle aynı olmalıdır. Bir VM bağladığınız sanal ağ, VM ile aynı abonelikte olmalıdır.|
    |**Kaynak grubu**|**Var olanı kullan:** *MyRG* öğesini seçin|Sanal ağ ile aynı kaynak grubu kullanılsa da, kaynakların aynı kaynak grubunda olması gerekmez.|
    |**Konum**|*Batı ABD*|Konum, bu makalenin [İki alt ağa sahip bir sanal ağ oluşturma](#create-vnet) bölümünde 5. adımda belirttiğiniz konumla aynı olmalıdır. VM’ler ve bağlandıkları sanal ağlar aynı konumda olmalıdır.|

4. **Boyut seç** dikey penceresinde, *DS1_V2 Standart* öğesine tıklayın, ardından **Seç**'e tıklayın. Azure tarafından desteklenen tüm Windows VM boyutlarının bir listesi için, [Windows VM boyutları](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) makalesini okuyun.
5. **Ayarlar** dikey penceresinde, aşağıdaki değerleri girin veya seçin ve **Tamam**’a tıklayın:

    |**Ayar**|**Değer**|**Ayrıntılar**|
    |---|---|---|
    |**Depolama: Yönetilen diskler kullan**|*Evet*||
    |**Sanal ağ**| *MyVNet* öğesini seçin|Oluşturmakta olduğunuz VM ile aynı konumda olan herhangi bir sanal ağı seçebilirsiniz. Sanal ağlar ve alt ağlar hakkında daha fazla bilgi için, [Sanal ağ](virtual-networks-overview.md) makalesini okuyun.|
    |**Alt ağ**|*Ön uç*’u seçin|Sanal ağ içinde mevcut herhangi bir alt ağı seçebilirsiniz.|
    |**Genel IP adresi**|Varsayılanı kabul edin|Bir genel IP adresi, sanal ağa İnternet’ten bağlanmanızı sağlar. Genel IP adresleri hakkında daha fazla bilgi edinmek için, [IP adresleri](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) makalesini okuyun.|
    |**Ağ güvenlik grubu (güvenlik duvarı)**|Varsayılanı kabul edin|Portalın oluşturduğu **(Yeni) MyWebServer nsg** varsayılan NSG’ye tıklayarak ayarlarını görüntüleyin. Açılan **Ağ güvenlik grubu oluştur** dikey penceresinde, herhangi bir kaynak IP adresinden TCP/3389 (RDP) trafiğine izin veren bir gelen kuralı olduğunu göreceksiniz.|
    |**Diğer tüm değerler**|Varsayılanları kabul edin|Kalan ayarlar hakkında daha fazla bilgi için, [VM’ler hakkında](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) makalesini okuyun.|

    Ağ güvenlik grupları (NSG’ler), VM için gelen ve giden akış ağ trafiği türüne dair gelen/giden kuralları oluşturmanıza olanak sağlar. Varsayılan olarak, VM’ye tüm gelen trafik reddedilir. Bir üretim web sunucusu için, TCP/80 (HTTP) ve TCP/443 (HTTPS) için ilave gelen kuralları ekleyebilirsiniz. Varsayılan olarak, giden trafik için kural yoktur, tüm giden trafiğe izin verilir. Trafiği ilkelerinize göre denetlemek için kurallar ekleyebilir/kaldırabilirsiniz. NSG’ler hakkında daha fazla bilgi almak için [Ağ güvenlik grupları](virtual-networks-nsg.md) makalesini okuyun.

6.  **Özet** dikey penceresinde, ayarları gözden geçirin ve VM’yi oluşturmak için **Tamam**’a tıklayın. VM oluşturulurken, portal panosunda bir durum kutucuğu görüntülenir. Oluşturulması birkaç dakika sürebilir. Tamamlanmasını beklemeniz gerekmez. VM oluşturulurken sonraki adıma devam edebilirsiniz.

### <a name="create-database-server-vm"></a>Veritabanı sunucusu VM’sini oluşturma

Veritabanı sunucusu VM’sini oluşturmak için, aşağıdaki adımları tamamlayın:

1.  Sık kullanılanlar bölmesinde, **Yeni**, **İşlem** öğelerine, ardından **Windows Server 2016 Datacenter**’a tıklayın.
2.  **Windows Server 2016 Datacenter** dikey penceresinde, **Oluştur**’a tıklayın.
3.  **Temeller dikey penceresinde**, aşağıdaki değerleri girin veya seçin, ardından **Tamam**’a tıklayın:

    |**Ayar**|**Değer**|**Ayrıntılar**|
    |---|---|---|
    |**Ad**|*MyDBServer*|Bu VM, İnternet’in bağlanamadığı ancak web sunucusunun bağlandığı bir veritabanı sunucusu işlevi görür.|
    |**VM disk türü**|*SSD*||
    |**Kullanıcı adı**|Tercih ettiğiniz||
    |**Parola ve Parolayı onayla**|Tercih ettiğiniz||
    |**Abonelik**|<Your subscription>|Abonelik, bu makalenin [İki alt ağa sahip bir sanal ağ oluşturma](#create-vnet) bölümünde 5. adımda seçtiğiniz abonelikle aynı olmalıdır.|
    |**Kaynak grubu**|**Var olanı kullan:** *MyRG* öğesini seçin|Sanal ağ ile aynı kaynak grubu kullanılsa da, kaynakların aynı kaynak grubunda olması gerekmez.|
    |**Konum**|*Batı ABD*|Konum, bu makalenin [İki alt ağa sahip bir sanal ağ oluşturma](#create-vnet) bölümünde 5. adımda belirttiğiniz konumla aynı olmalıdır.|

4.  **Boyut seç** dikey penceresinde, *DS1_V2 Standart* öğesine tıklayın, ardından **Seç**'e tıklayın.
5.  **Ayarlar** dikey penceresinde, aşağıdaki değerleri girin veya seçin ve **Tamam**’a tıklayın:

    |**Ayar**|**Değer**|**Ayrıntılar**|
    |----|----|---|
    |**Depolama: Yönetilen diskler kullan**|*Evet*||
    |**Sanal ağ**|*MyVNet* öğesini seçin|Oluşturmakta olduğunuz VM ile aynı konumda olan herhangi bir sanal ağı seçebilirsiniz.|
    |**Alt ağ**|**Alt ağ** kutusuna tıklayarak, ardından **Bir alt ağ seçin** dikey penceresinden **Arka uç** öğesini seçerek *Arka uç* ’u seçin|Sanal ağ içinde mevcut herhangi bir alt ağı seçebilirsiniz.|
    |**Genel IP adresi**|Hiçbiri – Varsayılan adrese tıklayın, ardından **Genel IP adresi seçin** dikey penceresinden **Hiçbiri** öğesini seçin|Bir genel IP adresi olmadan, VM’ye yalnızca, aynı sanal ağa bağlı başka bir VM’den bağlanabilirsiniz. Ona doğrudan İnternet’ten bağlanamazsınız.|
    |**Ağ güvenlik grubu (güvenlik duvarı)**|Varsayılanı kabul edin| MyWebServer VM’si için oluşturulan varsayılan NSG gibi, bu NSG’nin de aynı varsayılan gelen kuralı vardır. Bir veritabanı sunucusu için TCP/1433 (MS SQL) için ek bir gelen kuralı ekleyebilirsiniz. Varsayılan olarak, giden trafik için kural yoktur, tüm giden trafiğe izin verilir. Trafiği ilkelerinize göre denetlemek için kurallar ekleyebilir/kaldırabilirsiniz.|
    |**Diğer tüm değerler**|Varsayılanları kabul edin||

6.  **Özet** dikey penceresinde, ayarları gözden geçirin ve VM’yi oluşturmak için **Tamam**’a tıklayın. VM oluşturulurken, portal panosunda bir durum kutucuğu görüntülenir. Oluşturulması birkaç dakika sürebilir. Tamamlanmasını beklemeniz gerekmez. VM oluşturulurken sonraki adıma devam edebilirsiniz.

## <a name="review"></a>Kaynakları gözden geçirme

Bir sanal ağ ve iki VM oluştursanız da Azure portalı, MyRG kaynak grubunda sizin için çeşitli ek kaynaklar oluşturmuştur. Aşağıdaki adımları tamamlayarak MyRG kaynak grubunun içeriğini gözden geçirin:

1. **Sık Kullanılanlar** bölmesinde, **Daha fazla hizmet** öğesine tıklayın.
2. **Daha fazla hizmet** bölmesinde, içinde *Filtre* kelimesi olan kutuya *Kaynak grupları* yazın. Filtrelenen listede gördüğünüzde **Kaynak grupları**’na tıklayın.
3. **Kaynak grupları** bölmesinde, *MyRG* kaynak grubuna tıklayın. Aboneliğinizde birçok kaynak grubu varsa, MyRG kaynak grubuna hızlıca erişmek için, *Ada göre filtrele...* yazısını içeren kutuya *MyRG* yazabilirsiniz.
4.  **MyRG** dikey penceresinde, aşağıdaki resimde gösterildiği gibi, kaynak grubunun 12 kaynak içerdiğini göreceksiniz:

    ![Kaynak grubu içeriği](./media/virtual-network-get-started-vnet-subnet/resource-group-contents.png)

VM’ler, diskler ve depolama hesapları hakkında daha fazla bilgi için, [Sanal makine](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [Disk](../storage/storage-about-disks-and-vhds-windows.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ve [Depolama hesabı](../storage/storage-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) genel bakış makalelerini okuyun. Portalın sizin için oluşturduğu iki varsayılan NSG’yi görebilirsiniz. Ayrıca, portalın iki ağ arabirimi (NIC) kaynağı oluşturduğunu da görebilirsiniz. Bir NIC, bir VM’nin sanal ağ üzerindeki diğer kaynaklara bağlanmasını sağlar. NIC’ler hakkında daha fazla bilgi için, [NIC](virtual-network-network-interface.md) makalesini okuyun. Portal bir Genel IP adresi kaynağı da oluşturmuştur. Genel IP adresleri, genel bir IP adresi kaynağı için bir ayardır. Genel IP adresleri hakkında daha fazla bilgi edinmek için, [IP adresleri](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) makalesini okuyun.

## <a name="connect-to-from-vms"></a>VM’lere bağlanma

Sanal ağınız ve iki VM’niz oluşturulduğunda, aşağıdaki bölümlerde yer alan adımları tamamlayarak VM’lere bağlanabilirsiniz:

### <a name="connect-from-internet"></a>İnternetten web sunucusu VM‘sine bağlanma

Web sunucusu VM’sine İnternet’ten bağlanmak için, aşağıdaki adımları tamamlayın:

1. Bu makalenin [Kaynakları gözden geçirme](#review) bölümündeki adımları tamamlayarak portalda MyRG kaynak grubunu açın.
2. **MyRG** dikey penceresinde, **MyWebServer** VM’sine tıklayın.
3. **MyWebServer** dikey penceresinde, aşağıdaki resimde gösterildiği gibi **Bağlan** öğesine tıklayın:

    ![Web sunucusu VM’sine bağlanma](./media/virtual-network-get-started-vnet-subnet/webserver.png)

4. Tarayıcınızın *MyWebServer.rdp* dosyasını indirmesine izin verin, ardından açın.
5. Uzak bağlantı yayımcısının doğrulanamadığını bildiren bir iletişim kutusu alırsanız, **Bağlan**’a tıklayın.
6. Kimlik bilgilerinizi girerken, bu makalenin [Web sunucusu VM’si oluşturma](#create-web-server-vm) bölümünde 3. adımda belirttiğiniz kullanıcı adı ve parolayla oturum açtığınızdan emin olun. Ekrana gelen **Windows Güvenliği** kutusunda doğru kimlik bilgileri listelenmiyorsa, **Daha fazla seçenek** öğesine, ardından **Farklı bir hesap kullan**’a tıklamanız gerekebilir, böylece doğru kullanıcı adını ve parolasını belirtebilirsiniz). VM’ye bağlanmak için **Tamam**’a tıklayın.
7. Uzak bilgisayar kimliğinin doğrulanamadığı bilgisini veren bir **Uzak Masaüstü Bağlantısı** kutusu gelirse, **Evet** öğesine tıklayın.
8. Artık İnternet’ten MyWebServer VM’sine bağlanmış oldunuz. Sonraki bölümde yer alan adımları tamamlamak için, uzak masaüstü bağlantısını açık bırakın.

Uzak bağlantı, bu makalede [İki alt ağa sahip bir sanal ağ oluşturma](#create-vnet) bölümünde 5. adımda portalın oluşturduğu genel IP adresi kaynağına atanmış genel IP adresidir. **MyWebServer nsg** NSG içinde oluşturulan varsayılan kural herhangi bir kaynak IP adresinden VM’ye TCP/3389 (RDP) gelene izin verdiğinden bağlantıya izin verilir. VM’ye diğer herhangi bir bağlantı noktasından bağlanmayı denerseniz, NSG’ye farklı gelen kuralları ekleyerek ilave bağlantı noktalarına izin vermediğiniz müddetçe bağlantı başarısız olur.

>[!NOTE]
>NSG'ye ilave gelen kuralları eklerseniz, Windows güvenlik duvarında aynı bağlantı noktalarının açık olduğundan emin olun, aksi halde bağlantı başarısız olur.
>

### <a name="connect-to-internet"></a>Web sunucusu VM’sinden İnternet’e bağlanma

Web sunucusu VM’sinden giden İnternet’e bağlanmak için, aşağıdaki adımları tamamlayın:

1. MyWebServerVM’ye açık bir uzak bağlantınız yoksa, bu makalenin [İnternetten web sunucusu VM’sine bağlanma](#connect-from-internet) bölümündeki adımları tamamlayarak VM’ye bir uzak bağlantı oluşturun.
2. Windows masaüstünden, Internet Explorer'ı açın. **Internet Explorer 11 Kurulumu** iletişim kutusunda, **Önerilen ayarları kullanma** öğesine, ardından **Tamam**’a tıklayın. Bir üretim sunucusu için önerilen ayarların kabul edilmesi önerilir.
3. Internet Explorer adres çubuğunda, [bing.com](http:www.bing.com) girin. Bir Internet Explorer iletişim kutusu gelirse, tıklatın **Ekle**’ye, ardından **Güvenilen siteler** iletişim kutusunda **Ekle**’ye tıklayın ve **Kapat**’a tıklayın. Bu işlemi diğer herhangi bir Internet Explorer iletişim kutusu için yineleyin.
4. Bing arama sayfasında, *whatsmyipaddress* yazın, ardından büyüteç düğmesine tıklayın. Bing, VM’yi oluşturduğunuzda portal tarafından oluşturulan genel IP adresi kaynağına atanmış genel IP adresini getirir. **MyWebServer-ip** kaynağı için ayarları incelerseniz, aşağıdaki resimde gösterildiği gibi, genel IP adresi kaynağına atanmış IP adresinin aynısını görürsünüz. Ancak VM’nize atanan IP adresi farklıdır.

    ![Web sunucusu VM’sine bağlanma](./media/virtual-network-get-started-vnet-subnet/webserver-pip.png)

5.  Sonraki bölümde yer alan adımları tamamlamak için, uzak masaüstü bağlantısını açık bırakın.

VM’den tüm giden bağlantılara varsayılan olarak izin verildiğinden, İnternet’e VM’den bağlanabilirsiniz. NIC’ye uygulanan NSG’ye, NIC’nin bağlı olduğu alt ağa veya her ikisine ekleme kuralları ilave ederek giden bağlantıyı sınırlandırabilirsiniz.

VM, portal kullanılarak durdurulmuş (serbest bırakıldığında) duruma konursa, genel IP adresi değişebilir. Genel IP adresinin hiçbir zaman değişmemesi gerekiyorsa, IP adresi için dinamik ayırma yöntemi yerine (varsayılan değerdir) statik ayırma yöntemini kullanabilirsiniz. Ayırma yöntemleri arasındaki farklar hakkında daha fazla bilgi için, [IP adresi türleri ve ayırma yöntemleri](virtual-network-ip-addresses-overview-arm.md) makalesini okuyun.

### <a name="webserver-to-dbserver"></a>Web sunucusu VM’sinden veritabanı sunucusu VM’sine bağlanma

Web sunucusu VM’sinden veritabanı sunucusu VM’sine bağlanmak için, aşağıdaki adımları tamamlayın:

1. MyWebServer VM’sine açık bir uzak bağlantınız yoksa, bu makalenin [İnternetten web sunucusu VM’sine bağlanma](#connect-from-internet) bölümündeki adımları tamamlayarak VM’sine bir uzak bağlantı oluşturun.
2. Windows masaüstünde sol alt köşedeki Başlat düğmesine tıklayın, sonra *uzak masaüstü* yazmaya başlayın. Başlat menüsü listesinde **Uzak Masaüstü Bağlantısı** görüntülendiğinde, buna tıklayın.
3. **Uzak Masaüstü Bağlantısı** iletişim kutusunda, bilgisayar adı için *MyDBServer* girin ve **Bağlan**’a tıklayın.
4. Bu makalede [Veritabanı sunucusu VM’si oluşturma](#create-database-server-vm) bölümünde 3. adımda girdiğiniz kullanıcı adını ve parolaları girin, ardından **Tamam**’a tıklayın.
5. Uzak bilgisayar kimliğinin doğrulanamadığı bilgisini veren bir iletişim kutusu gelirse, **Evet**’e tıklayın.
6. Sonraki bölümde yer alan adımları tamamlamak için, her iki sunucuya uzak masaüstü bağlantısını açık bırakın.

Veritabanı sunucusu VM’sine bağlantıyı aşağıdaki nedenlerle web sunucusu VM’sinden yapabilirsiniz:

- TCP/3389 gelen bağlantılarının bu makalede [Veritabanı sunucusu VM’si oluşturma](#create-database-server-vm) bölümünde 5. adımda oluşturulmuş varsayılan NSG’de herhangi bir kaynak IP için etkinleştirilmesi nedeniyle.
- Bağlantıyı, veritabanı sunucusu sanal makinesiyle aynı sanal ağa bağlı olan web sunucusu sanal makinesinden başlatmanız nedeniyle. Kendisine atanmış bir genel IP adresi olmayan bir VM’ye bağlanmak için, sanal makine farklı bir alt ağa bağlı olsa dahi, aynı sanal ağa bağlı başka bir VM’den bağlantı yapmanız gerekir.
- VM’ler farklı alt ağlara bağlı olsa da, Azure alt ağlar arasında bağlantı sağlayacak varsayılan yollar oluşturur. Ancak kendi yollarınızı oluşturarak varsayılan yolları geçersiz kılabilirsiniz. Azure’da yollar hakkında daha fazla bilgi için, [Kullanıcı tanımlı yollar](virtual-networks-udr-overview.md) makalesini okuyun.

Bu makalede [Web sunucusu VM’sine İnternet’ten bağlanma](#connect-from-internet) bölümünde yaptığınız gibi İnternet’ten veritabanı sunucusu VM’sine uzak bağlantı başlatmayı denerseniz, **Bağlan** seçeneğinin devre dışı olduğunu göreceksiniz. VM’ye atanmış bir genel IP adresi olmadığından, yani ona İnternet’ten gelen bağlantı yapmak mümkün olmadığından Bağlan seçeneği devre dışıdır.

### <a name="connect-to-the-internet-from-the-database-server-vm"></a>Veritabanı sunucusu VM’sinden İnternet’e bağlanma

Aşağıdaki adımları tamamlayarak veritabanı sunucusu VM’sinden giden İnternet’e bağlanın:

1. MyWebServer VM’sinden MyDBServer VM’sine açılmış bir uzak bağlantınız yoksa, bu makalenin [Web sunucusu VM’sinden veritabanı sunucusu VM’sine bağlanma](#webserver-to-dbserver) bölümündeki adımları tamamlayın.
2. 0MyDBServer VM’sindeki Windows masaüstünde, Internet Explorer'ı açın ve bu makalede [Web sunucusu VM’sinden İnternet’e bağlanma](#connect-to-internet) bölümünde 2. ve 3. adımlarda yaptığınız gibi iletişim kutularına yanıt verin.
3. Adres çubuğunda, [bing.com](http:www.bing.com) yazın.
4. Ekrana gelen Internet Explorer iletişim kutusunda **Ekle**’ye tıklayın, ardından **Güvenilen siteler** iletişim kutusunda **Ekle**’ye ve **Kapat**’a tıklayın. Ekrana gelen ilave iletişim kutularında bu adımları tamamlayın.
5. Bing arama sayfasında, *whatsmyipaddress* yazın, ardından büyüteç düğmesine tıklayın. Bing, Azure altyapısı tarafından VM’ye atanmış durumda olan genel IP adresini getirir. 6. MyWebServer VM’sinden MyDBServer VM’sine uzak masaüstünü kapatın, ardından MyWebServer VM’sine uzak bağlantıyı kapatın.

MyDBServer VM’sine bir genel IP adresi kaynağı atanmamış olsa dahi tüm giden trafiğe varsayılan olarak izin verildiği için, İnternet’e giden bağlantıya izin verilir. VM’ye bir genel IP adresi kaynağı atanmış olsun olmasın, varsayılan olarak tüm VM’lerin İnternet’e giden bağlantı yapmasına izin verilir. Ancak, bir genel IP adresi kaynağı atanmış MyWebServer VM’si için yapabileceğiniz gibi İnternet’ten genel IP adresine bağlanmanız mümkün olmaz.

## <a name="delete-resources"></a>Tüm kaynakları silme

Bu makalede oluşturulan tüm kaynakları silmek için, aşağıdaki adımları tamamlayın:

1. Bu makalede oluşturulan MyRG kaynak grubunu görüntülemek için, bu makalede [Kaynakları gözden geçirme](#review) bölümündeki 1-3. adımları tamamlayın. Bir kez daha, kaynak grubundaki kaynakları gözden geçirin. MyRG kaynak grubunu önceki adımlara göre oluşturduysanız, 4. adımdaki resimde gösterilen 12 kaynağı göreceksiniz.
2. MyRG dikey penceresinde, **Sil** düğmesine tıklayın.
3. Portal, silmek istediğinizi onaylamak için kaynak grubunun adını yazmanızı gerektirir. Bu makalede [Kaynakları gözden geçirme](#review) bölümündeki 4. adımda gösterilen kaynaklardan başka kaynaklar görürseniz, **İptal** öğesine tıklayın. Yalnızca, bu makale kapsamında oluşturulan 12 kaynağı görürseniz, kaynak grubu adı için *MyRG* yazın, ardından **Sil** öğesine tıklayın. Bir kaynak grubunun silinmesiyle, kaynak grubu içerisindeki tüm kaynaklar silinir, bu nedenle, silmeden önce kaynak grubunun içeriğini onaylamayı hiçbir zaman unutmayın. Portal, kaynak grubu içinde yer alan tüm kaynakları siler ve sonra kaynak grubunu siler. Bu işlem birkaç dakika sürer.

## <a name="next-steps"></a>Sonraki adımlar

Bu alıştırmada, bir sanal ağ ve iki VM oluşturdunuz. VM oluşturma esnasında bazı özel ayarlar belirttiniz ve çeşitli varsayılan ayarları kabul ettiniz. Tüm kullanılabilir ayarları anladığınızdan emin olmak için, üretim sanal ağlarınız ve VM’lerini dağıtmadan önce aşağıdaki makaleleri okumanızı öneririz:

- [Sanal ağlar](virtual-networks-overview.md)
- [Genel IP adresleri](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses)
- [Ağ arabirimleri](virtual-network-network-interface.md)
- [Ağ güvenlik grupları](virtual-networks-nsg.md)
- [Sanal makineler](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

