---
title: Azure geçişi sunucu geçişi ile ExpressRoute üzerinden veri çoğaltma
description: Azure ExpressRoute 'ı Azure geçişi sunucu geçişi ile çoğaltma için kullanma
author: DeSeelam
ms.author: deseelam
ms.manager: bsiva
ms.topic: how-to
ms.date: 02/22/2021
ms.openlocfilehash: 9aa9a42422f3c114490d1dbb28a146b6e76ca8cd
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105558627"
---
# <a name="replicate-data-over-expressroute-with-azure-migrate-server-migration"></a>Azure geçişi ile ExpressRoute üzerinden veri çoğaltma: sunucu geçişi

Bu makalede, sunucuları Azure 'a geçirirken ExpressRoute bağlantı hattı üzerinden verileri çoğaltmak için [Azure geçişi](./migrate-services-overview.md#azure-migrate-server-migration-tool) 'nin nasıl yapılandırılacağını öğreneceksiniz.

## <a name="understand-azure-expressroute-circuits"></a>Azure ExpressRoute devreleri anlama
ExpressRoute (ER) devresi, şirket içi altyapınızı bir bağlantı sağlayıcısı üzerinden Microsoft 'a bağlar. ExpressRoute devreleri, özel eşleme, Microsoft eşleme veya her ikisini de kullanacak şekilde yapılandırılabilir. ExpressRoute ile kullanılabilen çeşitli eşleme seçenekleri hakkında daha fazla bilgi edinmek için [ExpressRoute devreleri ve eşleme](../expressroute/expressroute-circuit-peerings.md#peeringcompare) makalesindeki makaleyi gözden geçirin.

Azure geçişi 'nin sunucu geçiş aracı, şirket içi sunucuları ve sunucuları diğer bulutlardan Azure sanal makinelerine geçirmenize yardımcı olur. Araç, Azure aboneliğinizdeki yönetilen disklere geçirilecek olan sunuculardan veri çoğaltmak için devam eden bir çoğaltma akışı ayarlayarak çalışır. Sunucuları geçirmeye hazırsanız, sunucuları geçirmek için Azure 'daki çoğaltılan veriler kullanılır.

Şirket içi sunucularınızdan çoğaltılan veriler, internet üzerinden (güvenli şifreli bir bağlantı kullanılarak) veya bir ExpressRoute bağlantısı üzerinden Azure aboneliğinize gönderilmek üzere yapılandırılabilir. Geçirilecek çok sayıda sunucunuz olduğunda, çoğaltma için ExpressRoute kullanılması, ExpressRoute bağlantı hattınızı kullanan sağlanan bant genişliğini kullanarak sunucuları daha verimli bir şekilde geçirmenize yardımcı olabilir.

Bu makalede,
> [!div class="checklist"]
>
> * Özel eşleme ile bir ExpressRoute bağlantı hattı kullanarak verileri çoğaltma.
> * Microsoft eşlemesiyle bir ExpressRoute bağlantı hattı kullanarak verileri çoğaltma.

## <a name="replicate-data-using-an-expressroute-circuit-with-private-peering"></a>Özel eşleme ile ExpressRoute bağlantı hattını kullanarak verileri çoğaltma

> [!NOTE]
> Özel bir eşleme devresi üzerinde çoğaltma işlemi şu anda yalnızca [VMware sanal makinelerinin Azure 'a aracısız geçirilmesi](./tutorial-migrate-vmware.md)için desteklenmektedir. Diğer [çoğaltma yöntemlerine](./migrate-services-overview.md#azure-migrate-server-migration-tool) yönelik özel uç nokta desteği yakında kullanıma sunulacaktır.

VMware sanal makinelerini Azure 'a geçirmeye yönelik aracısız yöntemde, Azure geçişi gereci öncelikle aboneliğinizdeki bir depolama hesabına (önbellek depolama hesabı) çoğaltma verilerini yükler. Önbellek depolama hesabındaki çoğaltılan veriler daha sonra Azure geçişi hizmeti tarafından aboneliğinizdeki çoğaltma ile yönetilen disklere taşınır. Çoğaltma için özel bir eşleme devresi kullanmak üzere önbellek depolama hesabı kullanımı için özel bir uç nokta oluşturup ekleyebilirsiniz. Özel uç noktalar, sanal ağınızdan (VNet) bir veya daha fazla özel IP adresi kullanarak depolama hesabını etkin bir şekilde Azure VNet 'e getiriyor. Özel uç nokta, Azure geçişi gerecinin ExpressRoute özel eşlemesini kullanarak önbellek depolama hesabına bağlanmasına ve doğrudan özel IP adresine veri aktarmasına olanak tanır. <br/>  

![Çoğaltma işlemi](./media/replicate-using-expressroute/replication-process.png)

> [!Important]
>
> - Çoğaltma verilerine ek olarak Azure geçişi gereci, çoğaltma düzenleme da dahil olmak üzere denetim düzlemi etkinlikleri için Azure geçişi hizmeti ile iletişim kurar. Azure geçişi gereci ile Azure geçişi hizmeti arasındaki denetim düzlemi iletişimi, Azure geçişi hizmeti 'nin Genel uç noktasındaki internet üzerinden gerçekleşmeye devam eder.
> - Depolama hesabının özel uç noktasına Azure geçişi gerecinin dağıtıldığı ağdan erişilebilir olması gerekir.
> - DNS sorguları, önbellek depolama hesabının blob hizmeti uç noktası için Azure geçişi gereci, önbellek depolama hesabına bağlı özel uç noktanın özel IP adresine göre çözümlemek üzere yapılandırılmış olmalıdır.
> - Önbellek depolama hesabına, genel uç noktasında erişilebilir olması gerekir. (Azure geçişi hizmeti, verileri depolama hesabından çoğaltma tarafından yönetilen disklere taşımak için önbellek depolama hesabının genel uç noktasını kullanır.) 


### <a name="1-pre-requisites"></a>1. önkoşulların önkoşulları

Özel uç noktayı oluşturan Azure kullanıcısı, kaynak grubu ve özel uç noktanın oluşturulacağı sanal ağ üzerinde aşağıdaki izinlere sahip olmalıdır.

**Kullanım örneği** | **İzinler** 
--- | --- 
 Özel uç noktalar oluşturun ve yönetin. | Microsoft. Network/privateEndpoint/Write/Action<br/>Microsoft. Network/privateEndpoint/okuma/eylem  
|Bir VNet/alt ağa özel bir uç nokta ekleyin.<br/>Bu, Özel uç noktanın oluşturulacağı sanal ağda gereklidir.| Microsoft. Network/virtualNetworks/subnet/JOIN/ACTION Microsoft. Network/virtualNetworks/JOIN/Action
|Özel uç noktayı bir depolama hesabına bağlayın. <br/>| Microsoft. Microsoft. Storage/storageAccounts/privateEndpointConnectionApproval/Action <br/> Microsoft. Microsoft. Storage/storageAccounts/privateEndpointConnections/Read
|Bir ağ arabirimi oluşturun ve ağ güvenlik grubuna ekleyin. | Microsoft. Network/NetworkInterfaces/Read <br/> Microsoft. Network/NetworkInterfaces/alt ağlar/Write <br/> Microsoft. Network/NetworkInterfaces/alt ağlar/okuma<br/> Microsoft. Network/networkSecurityGroups/JOIN/Action (isteğe bağlı)
Özel DNS bölgeleri oluşturun ve yönetin.| Özel DNS bölge katılımcısı rolü <br/> _Veya_ <br/> Microsoft. Network/privateDnsZones/A/* <br/>  Microsoft. Network/privateDnsZones/Write Microsoft. Network/privateDnsZones/Read <br/> Microsoft. Network/privateEndpoints/privateDnsZoneGroups/Write <br/> Microsoft. Network/privateEndpoints/privateDnsZoneGroups/Read <br/> Microsoft. Network/privateDnsZones/virtualNetworkLinks/Write <br/>  Microsoft. Network/privateDnsZones/virtualNetworkLinks/Read <br/> Microsoft. Network/virtualNetworks/JOIN/Action 

### <a name="2-identify-the-cache-storage-account"></a>2. önbellek depolama hesabını tanımla 
 
Azure geçişi, bir sanal makine için çoğaltmayı (Azure portal deneyimini kullanarak) bir Azure geçişi projesi içinde ilk kez yapılandırdığınızda otomatik olarak bir önbellek depolama hesabı oluşturur. Depolama hesabı, içinde Azure geçişi projesini oluşturduğunuz aynı abonelikte ve kaynak grubunda oluşturulur.

Depolama hesabını oluşturmak ve bulmak için:

1. Projedeki bir veya daha fazla sanal makineyi çoğaltmak için Azure geçişi Azure portal deneyimini kullanın.
2. Azure geçişi projesinin kaynak grubuna gidin.
3. Depolama hesabı adındaki **"LSA"** önekini tanımlayarak önbellek depolama hesabını bulun.

![Kaynak grubu görünümü](./media/replicate-using-expressroute/storage-account-name.png)

> [!Tip]
>  
> Kaynak grubunuzda **"LSA"** ön ekine sahip birden fazla depolama hesabınız varsa, projede çoğaltılan VM 'lerden herhangi birinin çoğaltma ayarları ve hedef yapılandırma menüsüne giderek depolama hesabını doğrulayabilirsiniz. <br/> 
> ![Çoğaltma ayarlarına genel bakış](./media/replicate-using-expressroute/storage-account.png)

### <a name="3-upgrade--cache-storage-account-to-general-purpose-v2"></a>3. Genel Amaçlı v2 'ye önbellek depolama hesabı yükseltin 

Yalnızca bir Genel Amaçlı v2 (GPv2) depolama hesabında özel uç noktalar oluşturabilirsiniz. Önbellek depolama hesabı bir GPv2 Storage hesabı değilse, aşağıdaki adımları kullanarak bunu GPv2 'ye yükseltin:

1. Depolama hesabınıza gidin.
2. **Yapılandırma**'yı seçin.
3. **Hesap türü** altında, **Yükselt**' i seçin.
4. **Yükseltmeyi Onayla** altında, hesabınızın adını yazın.
5. Sayfanın alt kısmındaki **Yükselt** ' i seçin.

![Depolama Hesabını Yükselt](./media/replicate-using-expressroute/upgrade-storage-account.png)

### <a name="4-create-a-private-endpoint-for-the-storage-account"></a>4. depolama hesabı için özel bir uç nokta oluşturun

1. Depolama hesabınıza gidin, sol menüden **ağ** ' ı seçin ve **Özel uç nokta bağlantıları** sekmesini seçin.  
2. **+ Özel uç nokta**' ı seçin.

    a. **Özel uç nokta oluştur** penceresinde: **abonelik** ve **kaynak grubunu** seçin. Özel uç noktanız için bir ad girin ve depolama hesabı bölgesini seçin.  
    ![PE yapılandırma penceresi](./media/replicate-using-expressroute/storage-account-private-endpoint-creation.png)

    b. **Kaynak** sekmesinde, depolama hesabının bulunduğu **abonelik adını** belirtin. **Kaynak türü** olarak **Microsoft. Storage/storageaccounts** öğesini seçin. **Kaynak** bölümünde GPv2 türü çoğaltma depolama hesabının adını belirtin. **Hedef alt kaynak** olarak **BLOB** ' u seçin.  
    ![storageaccountpesettings](./media/replicate-using-expressroute/storage-account-private-endpoint-settings.png)

    c. **Yapılandırma** sekmesinde, depolama hesabının özel uç noktasının **sanal ağını** ve **alt ağını** seçin.  

    > [!Note]
    > Sanal ağın ExpressRoute ağ geçidi uç noktasını içermesi veya ExpressRoute ağ geçidi ile sanal ağa bağlı olması gerekir. 

    **Özel DNS tümleştirme** bölümünde **Evet** ' i SEÇIN ve özel bir DNS bölgesi ile tümleştirin. **Evet** seçildiğinde DNS bölgesi seçili sanal ağa otomatik olarak bağlanır ve yeni IP 'lerin DNS çözümlemesi IÇIN gereken DNS kayıtları ve özel uç nokta için oluşturulan tam etki alanı adları eklenir. [Özel DNS bölgeleri](../dns/private-dns-overview.md) hakkında daha fazla bilgi edinin.

    ![privatednszone](./media/replicate-using-expressroute/private-dns-zone.png)

    d. Ayrıca, Özel uç noktanız için **Etiketler** ekleyebilirsiniz.  

    e. Ayrıntıları girerken ve bir kez **oluşturma** işlemi tamamlandıktan sonra devam edin. Doğrulama tamamlandığında, Özel uç noktayı oluşturmak için **Oluştur** ' u seçin.

    > [!Note]
    > Özel uç noktayı oluşturan kullanıcı aynı zamanda depolama hesabının sahibiyseniz, Özel uç nokta otomatik olarak onaylanır. Aksi takdirde, sahibin kullanım için özel uç noktasını onaylaması gerekir. 

#### <a name="create-private-dns-zones-and-add-dns-records-manually-optional"></a>Özel DNS bölgeleri oluşturma ve DNS kayıtlarını el ile ekleme (Isteğe bağlı)

Özel uç nokta oluşturma sırasında özel bir DNS bölgesi ile tümleştirme seçeneğini seçmediyseniz, el ile özel bir DNS bölgesi oluşturmak için bu bölümdeki adımları izleyin. 

> [!Note]
> Özel bir DNS bölgesi ile tümleştirme için **Evet** ' i seçtiyseniz, bu bölümü atlayabilirsiniz. 

1. Özel bir DNS bölgesi oluşturun. 

    ![createprivatedns](./media/replicate-using-expressroute/create-private-dns.png)

    a. Yeni bir bölge oluşturmaya başlamak için **özel DNS bölgeleri** sayfasında **+ Ekle** düğmesini seçin.  
    b. **Özel DNS bölgesi oluştur** sayfasında, gerekli ayrıntıları girin. Özel DNS bölgesinin adını _Privatelink_. blob.Core.Windows.net olarak girin.  
    c. DNS bölgesini gözden geçirmek ve oluşturmak için **gözden geçir + oluştur** sekmesine geçin.   

2. Özel DNS bölgesini sanal ağınıza bağlayın.  

    Yukarıda oluşturulan özel DNS bölgesinin, Özel uç noktanın eklendiği sanal ağa bağlanması gerekir.

    a. Önceki adımda oluşturulan özel DNS bölgesine gidin ve sayfanın sol tarafındaki sanal ağ bağlantıları ' na gidin. **+ Ekle** düğmesini seçin.   
    b. Gerekli ayrıntıları girin. **Abonelik** ve **sanal ağ** alanları, Özel uç noktanızın eklendiği sanal ağın ilgili ayrıntıları ile doldurulmalıdır. Diğer alanlar ise olduğu gibi bırakılabilir.

3. Bir sonraki adım, DNS kayıtlarını DNS bölgesine eklemektir. Özel DNS bölgenize depolama hesabının tam etki alanı adı için bir giriş ekleyin.

    a. Özel DNS bölgenize gidin ve sayfanın sol tarafındaki **genel bakış** bölümüne gidin. Kayıt eklemeyi başlatmak için **+ kayıt** kümesi seçin.  

    b. **Kayıt kümesi Ekle** sayfasında, tam etki alanı adı için bir giriş ve bir tür kaydı olarak özel IP ekleyin.

> [!Important]
> Kaynak ortamdan, depolama hesabının özel uç noktasının özel IP adresini çözümlemek için ek DNS ayarları gerekebilir. Gereken DNS yapılandırmasını anlamak için [Bu makaleyi gözden geçirin](../private-link/private-endpoint-dns.md#on-premises-workloads-using-a-dns-forwarder) .

## <a name="replicate-data-using-an-expressroute-circuit-with-microsoft-peering"></a>Microsoft eşlemesiyle ExpressRoute bağlantı hattını kullanarak verileri çoğaltma

Aşağıdaki diyagramda gösterildiği gibi, çoğaltma trafiğinizi bir ExpressRoute bağlantı hattı üzerinden yönlendirmek için Microsoft eşlemesini veya var olan bir genel eşleme etki alanını (yeni ExpressRoute bağlantıları için kullanım dışı) kullanabilirsiniz.
![replicationwithmicrosofteşlemesi](./media/replicate-using-expressroute/replication-with-microsoft-peering.png)

Çoğaltma verileri Microsoft eşlenmiş devre üzerinden geçmekle birlikte, yine de Azure geçişi hizmeti ile şirket içi siteden diğer iletişimler (denetim düzlemi) için internet bağlantısına sahip olmanız gerekir. ExpressRoute üzerinden erişilebilen bazı ek URL 'Ler vardır. Bu, çoğaltma gereci/Hyper-V konağının çoğaltma işlemini yönetmek için erişmesi gerekir. Geçiş senaryosuna, [VMware aracısız geçişlerine](./migrate-appliance.md#public-cloud-urls) veya [aracı tabanlı GEÇIŞLERE](./migrate-replication-appliance.md)göre URL gereksinimlerini gözden geçirebilirsiniz.  

Şirket içi sitenizde bir ara sunucu kullanıyor ve çoğaltma trafiği için ExpressRoute 'u kullanmak istiyorsanız, şirket içi gereç üzerinde ilgili URL 'Ler için bir proxy atlama yapılandırmanız gerekir. 

### <a name="configure-proxy-bypass-rules-on-the-azure-migrate-appliance-for-vmware-agentless-migrations"></a>Azure geçiş aracılarındaki proxy atlama kurallarını yapılandırma (VMware aracısız geçişleri için)

1. Azure geçişi gerecine (Uzak Masaüstü) oturum açın.   
2. Not defteri 'ni kullanarak C:/ProgramData/MicrosoftAzure/config/appliance.jsdosyasını açın.
3. Dosyasında, "EnableProxyBypassList": "false" adlı satırı "EnableProxyBypassList": "true" olarak değiştirin. Değişiklikleri kaydedin ve gereci yeniden başlatın.
4. Yeniden başlattıktan sonra, Gereç Yapılandırma Yöneticisi 'ni açtığınızda, Web uygulaması kullanıcı arabiriminde proxy atlama seçeneğini görebilirsiniz. Aşağıdaki URL 'Leri proxy atlama listesine ekleyin.   
    - . *. vault.azure.net
    - . *. servicebus.windows.net
    - . *. discoverysrv.windowsazure.com
    - . *. migration.windowsazure.com
    - . *. hypervrecoverymanager.windowsazure.com
    - . *. blob.core.windows.net

### <a name="configure-proxy-bypass-rules-on-the-replication-appliance-for-agent-based-migrations"></a>Çoğaltma aracısında proxy atlama kurallarını yapılandırma (aracı tabanlı geçişler için)

Yapılandırma sunucusu ve Işlem sunucularındaki proxy atlama listesini yapılandırmak için aşağıdaki adımları izleyin:

1. Sistem Kullanıcı bağlamına erişmek için [PsExec aracını indirin](/sysinternals/downloads/psexec) .
2. Aşağıdaki komut satırı PsExec-s-i "%ProgramFiles%\Internet Explorer\iexplore.exe" komutunu çalıştırarak Internet Explorer 'ı sistem kullanıcı bağlamında açın
3. Proxy ayarlarını IE 'ye ekleyin.
4. Atlama listesinde, Azure depolama URL 'sini ekleyin. *. blob. Core. Windows. net.  

Yukarıdaki atlama kuralları, çoğaltma trafiğinin ExpressRoute üzerinden akabilir, ancak yönetim iletişimi Internet için proxy üzerinden gidebilirler.  

Ayrıca, Azure geçişi çoğaltma trafiğinizi internet yerine bir ExpressRoute bağlantı hattı üzerinden çapraz geçiş yapmak için aşağıdaki BGP topluluklarına yönelik yol filtresindeki yolları tanıtmanız gerekir.  

- Kaynak Azure bölgesi için bölgesel BGP topluluğu (Azure geçişi proje bölgesi)
- Hedef Azure bölgesi (geçiş için bölge) için bölgesel BGP topluluğu
- Azure Active Directory için BGP topluluğu (12076:5060)

[Yol filtreleri](../expressroute/how-to-routefilter-portal.md) ve [ExpressRoute için BGP toplulukları](../expressroute/expressroute-routing.md#bgp)listesi hakkında daha fazla bilgi edinin. 

## <a name="next-steps"></a>Sonraki adımlar

- [ExpressRoute devreleri](../expressroute/expressroute-circuit-peerings.md)hakkında daha fazla bilgi edinin.
- [ExpressRoute yönlendirme etki alanları](../expressroute/expressroute-circuit-peerings.md#peeringcompare)hakkında daha fazla bilgi edinin.
- [Özel uç noktalar](../private-link/private-endpoint-overview.md)hakkında daha fazla bilgi edinin.