---
title: CloudSimple hızlı başlangıç ile Azure VMware çözümü-özel bir bulut oluşturma
description: CloudSimple ile Azure VMware çözümü ile özel bir bulut oluşturma ve yapılandırma hakkında bilgi edinin
author: sharaths-cs
ms.author: dikamath
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: c32045c6d68a813eaeb6e4e5de433160c2a30634
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901452"
---
# <a name="quickstart---configure-a-private-cloud-environment"></a>Hızlı başlangıç-özel bir bulut ortamı yapılandırma

Bu makalede, CloudSimple özel bulutu oluşturmayı ve özel bulut ortamınızı ayarlamayı öğrenin.

## <a name="before-you-begin"></a>Başlamadan önce

[Ağ önkoşullarını](cloudsimple-network-checklist.md)gözden geçirin.

## <a name="sign-in-to-azure"></a>Azure'da oturum açın

[https://portal.azure.com](https://portal.azure.com) adresinden Azure portalında oturum açın.

## <a name="create-a-private-cloud"></a>Özel Bulut oluşturma

Özel bulut, ESXi Konakları, vCenter, vSAN ve NSX 'i destekleyen yalıtılmış bir VMware yığınına sahiptir.

Özel bulutlar CloudSimple portalı aracılığıyla yönetilir. Kendi yönetim etki alanında kendi vCenter Server kuruluşları vardır. Yığın adanmış düğümlerde ve yalıtılmış tam donanım düğümlerinde çalışır.

1. **Tüm Hizmetler**’i seçin.
2. **Cloudsimple Hizmetleri**için arama yapın.
3. Özel bulutunuzu oluşturmak istediğiniz CloudSimple hizmetini seçin.
4. **Özel bulut oluştur** **' a tıklayarak**cloudsimple portalı için yeni bir tarayıcı sekmesi açın.  İstenirse, Azure oturum açma kimlik bilgilerinizle oturum açın.  

    ![Azure 'dan özel bulut oluşturma](media/create-private-cloud-from-azure.png)

5. CloudSimple portalında, özel bulutunuz için bir ad sağlayın.
6. Özel bulutunuzun **konumunu** seçin.
7. Azure 'da sağladığınız verilerle tutarlı bir **düğüm türü**seçin.
8. **Düğüm sayısını**belirtin.  Özel bir bulut oluşturmak için en az üç düğüm gerekir.

    ![Özel bulut oluşturma-temel bilgi](media/create-private-cloud-basic-info.png)

9. Ileri ' ye tıklayın **: Gelişmiş Seçenekler**.
10. VSphere/vSAN alt ağları için CıDR aralığını girin. CıDR aralığının şirket içi veya diğer Azure alt ağlarınızdan (sanal ağlar) veya ağ geçidi alt ağıyla çakışmadığından emin olun.

    **CIDR aralığı seçenekleri:** /24,/23,/22 veya/21. A/24 ' e kadar olan bir CıDR aralığı, en fazla 26 düğüm destekler, a/23 CıDR aralığı 58 düğümü destekler ve/22 ve/21 CıDR aralığı 64 düğümlerini destekler (özel buluttaki en fazla düğüm sayısı).  Daha fazla bilgi edinmek için bkz. [VLAN ve alt ağlara genel bakış](cloudsimple-vlans-subnets.md).

      > [!IMPORTANT]
      > VSphere/vSAN CıDR aralığındaki IP adresleri özel bulut altyapısı tarafından kullanılmak üzere ayrılmıştır.  Bu aralıkta IP adresini herhangi bir sanal makinede kullanmayın.

11. Ileri ' ye tıklayın. **İnceleme ve oluşturma**.
12. Ayarları gözden geçirin. Herhangi bir ayarı değiştirmeniz gerekiyorsa, **önceki**' ye tıklayın.
13. **Oluştur**’a tıklayın.

Özel bulut sağlama işlemi başlar.  Özel bulutun sağlanması iki saate kadar sürebilir.

## <a name="launch-cloudsimple-portal"></a>CloudSimple portalını Başlat

Azure portal 'ten CloudSimple portalına erişebilirsiniz.  CloudSimple portalı, çoklu oturum açma (SSO) ile Azure oturum açma kimlik bilgilerinizle başlatılır.  CloudSimple portalına erişmek için **Cloudsimple hizmet Yetkilendirme** uygulamasını yetkilendirmeniz gerekir.  İzinleri verme hakkında daha fazla bilgi için bkz. [CloudSimple hizmet Yetkilendirme uygulamasına izin](access-cloudsimple-portal.md#consent-to-cloudsimple-service-authorization-application)verme.

1. **Tüm Hizmetler**’i seçin.
2. **Cloudsimple Hizmetleri**için arama yapın.
3. Özel bulutunuzu oluşturmak istediğiniz CloudSimple hizmetini seçin.
4. Genel bakışta, cloudsimple portalına **Git** ' e tıklayarak cloudsimple portalı için yeni bir tarayıcı sekmesi açın.  İstenirse, Azure oturum açma kimlik bilgilerinizle oturum açın.  

    ![CloudSimple portalını Başlat](media/launch-cloudsimple-portal.png)

## <a name="create-point-to-site-vpn"></a>Noktadan siteye VPN oluşturma

Noktadan siteye VPN bağlantısı, bilgisayarınızdan özel bulutunuzu bağlamak için en kolay yoldur. Özel buluta uzaktan bağlanıyorsanız Noktadan siteye VPN bağlantısı kullanın.  Özel bulutunuz için hızlı erişim için aşağıdaki adımları izleyin.  Şirket içi ağınızdan CloudSimple bölgesine erişim, [siteden sıteye VPN](vpn-gateway.md) veya [Azure ExpressRoute](on-premises-connection.md)kullanılarak yapılabilir.

### <a name="create-gateway"></a>Ağ Geçidi oluştur

1. CloudSimple portalını başlatın ve **ağ**' ı seçin.
2. **VPN Gateway**seçin.
3. **Yeni VPN Gateway**' ye tıklayın.

    ![VPN ağ geçidi oluşturma](media/create-vpn-gateway.png)

4. **Ağ geçidi yapılandırması**için aşağıdaki ayarları belirtin ve **İleri**' ye tıklayın.

    * Ağ Geçidi türü olarak **noktadan sıteye VPN ' yi** seçin.
    * Ağ geçidini tanımlamak için bir ad girin.
    * CloudSimple hizmetinizin dağıtıldığı Azure konumunu seçin.
    * Noktadan siteye ağ geçidi için istemci alt ağını belirtin.  Bağlandığınızda, DHCP adresleri bu alt ağdan verilecek.

5. **Bağlantı/Kullanıcı**için aşağıdaki ayarları belirtin ve **İleri**' ye tıklayın.

    * Tüm geçerli ve gelecekteki kullanıcıların bu noktadan siteye ağ geçidi aracılığıyla özel buluta erişmesine otomatik olarak izin vermek için **tüm kullanıcıları otomatik olarak ekle**' yi seçin. Bu seçeneği belirlediğinizde, Kullanıcı listesindeki tüm kullanıcılar otomatik olarak seçilir. Listedeki bireysel kullanıcıların seçimini kaldırarak otomatik seçeneği geçersiz kılabilirsiniz.
    * Yalnızca bireysel kullanıcıları seçmek için Kullanıcı listesindeki onay kutularına tıklayın.

6. VLAN/alt ağlar bölümü, ağ geçidi ve bağlantılar için yönetim ve Kullanıcı VLAN 'Ları/alt ağları belirtmenize olanak tanır.

    * **Otomatik olarak ekle** seçeneği bu ağ geçidinin genel ilkesini ayarlar. Ayarlar geçerli ağ geçidi için geçerlidir. Ayarlar **seçim** alanında geçersiz kılınabilir.
    * **Özel bulutların yönetim VLAN 'ları/alt ağlarını Ekle**' yi seçin.
    * Kullanıcı tanımlı tüm VLAN 'Ları/alt ağları eklemek için **Kullanıcı tanımlı VLAN 'lar/alt ağlar Ekle**' ye tıklayın.
    * **Seçim** ayarları **otomatik olarak ekle**altındaki genel ayarları geçersiz kılar.

7. Ayarları gözden geçirmek için **İleri** 'ye tıklayın. Herhangi bir değişiklik yapmak için düzenleme simgeleri ' ne tıklayın.
8. VPN ağ geçidini oluşturmak için **Oluştur** ' a tıklayın.

### <a name="connect-to-cloudsimple-using-point-to-site-vpn"></a>Noktadan siteye VPN kullanarak CloudSimple 'a bağlanma

VPN istemcisi, bilgisayarınızdan CloudSimple 'a bağlanmak için gereklidir.  MacOS ve OS X için Windows veya [viscosity](https://www.sparklabs.com/viscosity/download/) Için [OpenVPN istemcisini](https://openvpn.net/community-downloads/) indirin.

1. CloudSimple portalını başlatın ve **ağ**' ı seçin.
2. **VPN Gateway**seçin.
3. VPN ağ geçitleri listesinden Noktadan siteye VPN Gateway ' e tıklayın.
4. **Kullanıcıları**seçin.
5. **VPN yapılandırmadan indir**' e tıklayın.

    ![VPN yapılandırmasını indirme](media/download-p2s-vpn-configuration.png)

6. VPN istemcinizdeki yapılandırmayı içeri aktarın.

    * [Windows istemcisinde yapılandırmayı içeri aktarma](https://openvpn.net/vpn-server-resources/connecting-to-access-server-with-windows/#openvpn-open-source-openvpn-gui-program) yönergeleri
    * [MacOS veya OS X üzerinde yapılandırmayı içeri aktarma](https://www.sparklabs.com/support/kb/article/getting-started-with-viscosity-mac/#creating-your-first-connection) yönergeleri

7. CloudSimple 'a bağlanın.

## <a name="create-a-vlan-for-your-workload-vms"></a>İş yükü sanal makinelerinize yönelik bir VLAN oluşturma

Özel bir bulut oluşturduktan sonra, iş yükünüzü/uygulama VM 'lerinizi dağıtacağınız bir VLAN oluşturun.

1. CloudSimple portalında **ağ**' ı seçin.
2. **VLAN/alt ağlar**' a tıklayın.
3. **VLAN/subnet oluştur**' a tıklayın.

    ![VLAN/subnet oluştur](media/create-new-vlan-subnet.png)

4. Yeni VLAN/alt ağ için **özel bulutu** seçin.
5. Listeden bir VLAN KIMLIĞI seçin.  
6. Alt ağı tanımlamak için bir alt ağ adı girin.
7. CıDR aralığını ve maskesini belirtin.  Bu Aralık varolan alt ağlarla çakışmamalıdır.
8. **Gönder**' e tıklayın.

    ![VLAN/subnet ayrıntıları oluştur](media/create-new-vlan-subnet-details.png)

VLAN/subnet oluşturulacak.  Artık özel bulut vCenter 'unuzda dağıtılmış bir bağlantı noktası grubu oluşturmak için bu VLAN KIMLIĞINI kullanabilirsiniz.

## <a name="connect-your-environment-to-an-azure-virtual-network"></a>Ortamınızı bir Azure sanal ağına bağlama

CloudSimple, özel bulutunuz için bir ExpressRoute devresi sağlar. Azure 'daki Sanal ağınızı ExpressRoute devresine bağlayabilirsiniz. Bağlantıyı ayarlamayla ilgili tam Ayrıntılar için [ExpressRoute kullanarak Azure sanal ağ bağlantısı](https://docs.azure.cloudsimple.com/cloudsimple-azure-network-connection/)' nda bulunan adımları izleyin.

## <a name="sign-in-to-vcenter"></a>VCenter 'da oturum açın

Artık sanal makineleri ve ilkeleri ayarlamak için vCenter 'da oturum açabilirsiniz.

1. VCenter 'a erişmek için CloudSimple portalından başlatın. Giriş sayfasında, **ortak görevler**altında, **vSphere istemcisini Başlat**' a tıklayın.  Özel bulutu seçin ve ardından özel bulutta **vSphere Istemcisini Başlat** ' a tıklayın.

    ![VSphere Istemcisini Başlat](media/launch-vcenter-from-cloudsimple-portal.png)

2. VCenter 'a erişmek için tercih ettiğiniz vSphere istemcinizi seçin ve Kullanıcı adınızla parolanızla oturum açın.  Varsayılanlar şunlardır:
    * Kullanıcı adı: **CloudOwner@cloudsimple.local**
    * Parola: **CloudSimple123!**  

Sonraki yordamlardaki vCenter ekranları vSphere (HTML5) istemcisinden alınır.

## <a name="change-your-vcenter-password"></a>VCenter parolanızı değiştirme

CloudSimple, vCenter 'da ilk kez oturum açtığınızda parolanızı değiştirmenizi önerir.  
Ayarladığınız parolanın aşağıdaki gereksinimleri karşılaması gerekir:

* Maksimum ömür: parolanın her 365 günde bir değiştirilmesi gerekir
* Yeniden kullanımı kısıtla: kullanıcılar önceki beş parolanın hiçbirini yeniden kullanabilir
* Uzunluk: 8-20 karakter
* Özel karakter: en az bir özel karakter
* Alfabetik karakterler: en az bir büyük harf, A-Z ve en az bir küçük harf, a-z
* Sayılar: en az bir sayısal karakter, 0-9
* En büyük bitişik karakterler: üç

    Örnek: CC veya CCC parolanın bir parçası olarak kabul edilebilir, ancak CCCC değildir.

Gereksinimleri karşılamayan bir parola ayarlarsanız:

* vSphere Flash Istemcisini kullanıyorsanız bir hata bildiriyor
* HTML5 istemcisini kullanıyorsanız bir hata raporlamaz. İstemci değişikliği kabul etmez ve eski parola çalışmaya devam eder.

## <a name="access-nsx-manager"></a>NSX yöneticisine erişme

NSX Yöneticisi varsayılan parolayla dağıtılır. 

* Kullanıcı adı: **yönetici**
* Parola: **CloudSimple123!**

CloudSimple portalında tam etki alanı adını (FQDN) ve NSX Manager IP adresini bulabilirsiniz.

1. CloudSimple portalını başlatın ve **kaynakları**seçin.
2. Kullanmak istediğiniz özel buluta tıklayın.
3. **VSphere yönetim ağını** seçin
4. **NSX Manager** 'ıN FQDN 'SINI veya IP adresini kullanın ve bir Web tarayıcısı kullanarak bağlanın.

    ![NSX Manager FQDN bulun](media/private-cloud-nsx-manager-fqdn.png)

## <a name="create-a-port-group"></a>Bağlantı noktası grubu oluşturma

VSphere içinde dağıtılmış bir bağlantı noktası grubu oluşturmak için:

1. [VSphere Ağ Kılavuzu](https://docs.vmware.com/en/VMware-vSphere/6.5/vsphere-esxi-vcenter-server-65-networking-guide.pdf)'ndaki "dağıtılmış bağlantı noktası grubu ekleme" bölümündeki yönergeleri izleyin.
2. Dağıtılmış bağlantı noktası grubunu ayarlarken, [Iş yükü sanal makinelerinize VLAN oluşturma](#create-a-vlan-for-your-workload-vms)bölümünde oluşturulan VLAN kimliğini sağlayın.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure 'da VMware VM 'lerini kullanma](quickstart-create-vmware-virtual-machine.md)
* [Azure ExpressRoute kullanarak şirket içi ağa bağlanma](on-premises-connection.md)
* [Şirket içinden siteden siteye VPN ayarlama](vpn-gateway.md)
