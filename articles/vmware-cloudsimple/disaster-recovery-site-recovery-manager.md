---
title: CloudSimple tarafından Azure VMware çözümü-özel bulutu VMware Site Recovery Manager kullanarak olağanüstü durum kurtarma sitesi olarak ayarlama
description: CloudSimple özel bulutunuzu şirket içi VMware iş yükleri için olağanüstü durum kurtarma sitesi olarak ayarlamayı açıklar
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 151058f23bed674883da57e0b728dc1df4b698d9
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/10/2019
ms.locfileid: "70846123"
---
# <a name="set-up-private-cloud-as-a-disaster-recovery-target-with-vmware-site-recovery-manager"></a>VMware Site Recovery Manager ile özel bulutu olağanüstü durum kurtarma hedefi olarak ayarlama

CloudSimple özel bulutunuzu, şirket içi VMware iş yükleri için olağanüstü durum kurtarma (DR) sitesi olarak kullanabilirsiniz.

DR çözümü, vSphere çoğaltmasını, VMware Site Recovery Manager 'ı (SRM) temel alır. Özel bulutunuzu, şirket içi kurtarma siteniz tarafından korunan bir birincil site olarak etkinleştirmek için benzer bir yaklaşım izlenebilir.

CloudSimple çözümü:

* Özellikle DR için bir veri merkezi ayarlama gereksinimini ortadan kaldırır.
* , Dünya çapındaki coğrafi esnekliği için CloudSimple 'ın dağıtıldığı Azure konumlarından yararlanmanızı sağlar.
* , DR oluşturmaya yönelik dağıtım maliyetlerini ve toplam sahiplik maliyetini azaltmaya yönelik bir seçenek sunar.

CloudSimple çözümü şunları yapmanızı gerektirir:

* Özel bulutunuzda vSphere çoğaltmasını ve SRM 'yi yükler, yapılandırın ve yönetin.
* Özel bulut korumalı site olduğunda, SRM için kendi lisanslarınızı sağlayın. Kurtarma sitesi olarak kullanıldığında CloudSimple sitesi için ek bir lisans gerekmez.

Bu çözümle, vSphere çoğaltma ve SRM üzerinde tam denetime sahip olursunuz. Tanıdık UI, API ve CLı arabirimleri, mevcut betiklerinizin ve araçlarınızın kullanımını sağlar.

![Site Recovery Manager dağıtımı](media/srm-deployment.png)

Özel bulut ve şirket içi ortamlarınızla uyumlu vRA ve SRM sürümlerini kullanabilirsiniz. Bu kılavuzdaki örneklerde vRA 6,5 ve SRM 6,5 kullanılır. Bu sürümler, CloudSimple tarafından desteklenen vSphere 6,5 ile uyumludur.

## <a name="deploy-the-solution"></a>Çözümü dağıtma

Aşağıdaki bölümlerde özel bulutunuzda SRM kullanarak bir DR çözümünün nasıl dağıtılacağı açıklanır.

1. [VMware ürün sürümlerinin uyumlu olduğunu doğrulama](#verify-that-vmware-product-versions-are-compatible)
2. [DR ortamınızın boyutunu tahmin etme](#estimate-the-size-of-your-dr-environment)
3. [Ortamınız için özel bir bulut oluşturma](#create-a-private-cloud-for-your-environment)
4. [SRM çözümü için özel bulut ağını ayarlama](#set-up-private-cloud-networking-for-the-srm-solution)
5. [Şirket içi ağınız ile özel bulut arasında siteden siteye VPN bağlantısı kurun ve gerekli bağlantı noktalarını açın](#set-up-a-site-to-site-vpn-connection-between-your-on-premises-network-and-the-private-cloud-and-open-required-ports)
6. [Özel bulutunuzda altyapı hizmetlerini ayarlama](#set-up-infrastructure-services-in-your-private-cloud)
7. [Şirket içi ortamınıza vSphere çoğaltma gereci yüklemesi](#install-vsphere-replication-appliance-in-your-on-premises-environment)
8. [Özel bulut ortamınıza vSphere çoğaltma gereci yüklemesi](#install-vsphere-replication-appliance-in-your-private-cloud-environment)
9. [Şirket içi ortamınıza SRM sunucusu 'nı yüklemeyin](#install-srm-server-in-your-on-premises-environment)
10. [Özel bulutunuzda SRM sunucusunu yükler](#install-srm-server-in-your-private-cloud)

### <a name="verify-that-vmware-product-versions-are-compatible"></a>VMware ürün sürümlerinin uyumlu olduğunu doğrulama

Bu kılavuzdaki konfigürasyonlar aşağıdaki uyumluluk gereksinimlerine tabidir:

* Aynı SRM sürümünün özel bulutunuzda ve şirket içi ortamınızda dağıtılması gerekir.
* Aynı vSphere çoğaltma sürümünün özel bulutunuzda ve şirket içi ortamınızda dağıtılması gerekir.
* Özel bulutunuzda ve şirket içi ortamınızda bulunan Platform Hizmetleri denetleyicisi 'nin (PSC) sürümleri uyumlu olmalıdır.
* Özel bulutunuzda ve şirket içi ortamınızda vCenter 'ın sürümleri uyumlu olmalıdır.
* SRM ve vSphere çoğaltmasının sürümleri birbirleriyle ve PSC ve vCenter sürümleriyle uyumlu olmalıdır.

İlgili VMware belgelerinin ve uyumluluk bilgilerinin bağlantıları için [VMware Site Recovery Manager](https://docs.vmware.com/en/Site-Recovery-Manager/index.html) belgeleri ' ne gidin.

Özel bulutunuzda vCenter ve PSC sürümlerini bulmak için CloudSimple Portal ' ı açın. **Kaynaklar**' a gidin, özel bulutunuzu seçin ve **vSphere yönetim ağı** sekmesine tıklayın.

![Özel bulutta vCenter & PSC sürümleri](media/srm-resources.png)

### <a name="estimate-the-size-of-your-dr-environment"></a>DR ortamınızın boyutunu tahmin etme

1. Tanımlı şirket içi yapılandırmanızın desteklenen sınırlar dahilinde olduğunu doğrulayın. SRM 6,5 için sınırlar, VMware Bilgi Bankası makalesinde [Site Recovery Manager 6,5 için işlemsel sınırlara](https://kb.vmware.com/s/article/2147110)göre belgelenmiştir.
2. İş yükü boyutunuzu ve RPO gereksinimlerinizi karşılayacak yeterli ağ bant genişliğine sahip olduğunuzdan emin olun. [VSphere çoğaltması için bant genişliği gereksinimlerinin hesaplanmasındaki](https://docs.vmware.com/en/vSphere-Replication/6.5/com.vmware.vsphere.replication-admin.doc/GUID-4A34D0C9-8CC1-46C4-96FF-3BF7583D3C4F.html) VMware Bilgi Bankası makalesi bant genişliği sınırları hakkında rehberlik sağlar.
3. Şirket içi ortamınızı korumak için, DR sitenizde gerekli olan kaynakları tahmin etmek üzere CloudSimple sizer aracını kullanın.

### <a name="create-a-private-cloud-for-your-environment"></a>Ortamınız için özel bir bulut oluşturma

[Özel bulut oluşturma](create-private-cloud.md)' daki yönergeleri ve boyutlandırma önerilerini Izleyerek cloudsimple portalından özel bir bulut oluşturun.

### <a name="set-up-private-cloud-networking-for-the-srm-solution"></a>SRM çözümü için özel bulut ağını ayarlama

SRM çözümü için özel bulut ağı ayarlamak üzere CloudSimple portalına erişin.

SRM çözümü ağı için bir VLAN oluşturun ve bir alt ağ CıDR atayın. Yönergeler için bkz. [VLAN/alt ağlar oluşturma ve yönetme](create-vlan-subnet.md).

### <a name="set-up-a-site-to-site-vpn-connection-between-your-on-premises-network-and-the-private-cloud-and-open-required-ports"></a>Şirket içi ağınız ile özel bulut arasında siteden siteye VPN bağlantısı kurun ve gerekli bağlantı noktalarını açın

Şirket içi ağınız ile özel bulutunuz arasında siteden siteye bağlantı kurmak için. Yönergeler için bkz. [CloudSimple özel bulutunuzun VPN bağlantısını yapılandırma](set-up-vpn.md).

### <a name="set-up-infrastructure-services-in-your-private-cloud"></a>Özel bulutunuzda altyapı hizmetlerini ayarlama

İş yüklerinizi ve araçlarınızı yönetmeyi kolaylaştırmak için özel buluttaki altyapı hizmetlerini yapılandırın.

Aşağıdakilerden birini yapmak istiyorsanız, [Azure AD 'Yi CloudSimple özel bulutu 'Nda vCenter için kimlik sağlayıcısı olarak kullanma](azure-ad.md) bölümünde anlatıldığı şekilde bir dış kimlik sağlayıcısı ekleyebilirsiniz:

* Şirket içi Active Directory (AD) özel bulutunuzda kullanıcıları belirler.
* Tüm kullanıcılar için özel bulutunuzda bir AD ayarlayın.
* Azure AD 'yi kullanın.

Özel buluttaki iş yükleriniz için IP adresi araması, IP adresi yönetimi ve ad çözümleme hizmetleri sağlamak istiyorsanız, [CloudSimple özel BULUTUNUZDA DNS ve DHCP uygulamalarını ve iş yüklerini ayarlama](dns-dhcp-setup.md)bölümünde açıklandığı gıbı bir DHCP ve DNS sunucusu ayarlayın.

*. Cloudsimple.io etki alanı, yönetim VM 'Leri ve özel buluttaki konaklar tarafından kullanılır. Bu etki alanına yönelik istekleri çözümlemek için DNS sunucusunda DNS iletmeyi, [koşullu Iletici oluşturma](on-premises-dns-setup.md#create-a-conditional-forwarder)bölümünde açıklandığı gibi yapılandırın.

### <a name="install-vsphere-replication-appliance-in-your-on-premises-environment"></a>Şirket içi ortamınıza vSphere çoğaltma gereci yüklemesi

VMware belgelerini izleyerek şirket içi ortamınıza vSphere çoğaltma gereci (vRA) yüklemesini yapın. Yükleme bu üst düzey adımlardan oluşur:

1. Şirket içi ortamınızı vRA yüklemesi için hazırlayın.

2. Vmware.com adresinden VR ISO 'daki OVF 'yi kullanarak şirket içi ortamınızda vRA 'yi dağıtın. VRA 6,5 için, [Bu VMware blogu](https://blogs.vmware.com/virtualblocks/2017/01/20/vr-65-ovf-choices) ilgili bilgileri içerir.

3. Şirket içi vRA 'yi, şirket içi sitede vCenter çoklu oturum açma ile kaydedin. VSphere çoğaltma 6,5 hakkında ayrıntılı yönergeler için bkz. VMware Document [VMware vSphere çoğaltma 6,5 yükleme ve yapılandırma](https://docs.vmware.com/en/vSphere-Replication/6.5/vsphere-replication-65-install.pdf).

## <a name="install-vsphere-replication-appliance-in-your-private-cloud-environment"></a>Özel bulut ortamınıza vSphere çoğaltma gereci yüklemesi

Başlamadan önce, aşağıdakilere sahip olduğunuzu doğrulayın:

* Şirket içi ortamınızdaki alt ağlardan IP 'ye, özel bulutunuzun yönetim alt ağına ulaşılabilirlik
* IP şirket içi vSphere ortamınızdaki çoğaltma alt ağından, özel bulutunuzun SRM çözüm alt ağına ulaşılabilirlik

Yönergeler için bkz. [CloudSimple özel bulutunuzun VPN bağlantısını yapılandırma](set-up-vpn.md). Adımlar, şirket içi yüklemede olanlarla benzerdir.

CloudSimple, vRA ve SRM yüklemesi sırasında IP adresleri yerine FQDN 'lerin kullanılmasını önerir. Özel bulutunuzda vCenter ve PSC 'nin FQDN 'sini bulmak için CloudSimple Portal ' ı açın. **Kaynaklar**' a gidin, özel bulutunuzu seçin ve **vSphere yönetim ağı** sekmesine tıklayın.

![Özel bulutta vCenter/PSC FQDN 'SI bulma](media/srm-resources.png)

CloudSimple, varsayılan ' cloudowner ' kullanıcısını kullanarak vRA ve SRM yüklememeyi gerektirir, bunun yerine yeni bir kullanıcı oluşturur. Bu işlem, özel bulut vCenter ortamınız için yüksek çalışma süresi ve kullanılabilirlik sağlanmasına yardımcı olmak üzere yapılır. Ancak, özel bulut vCenter 'daki varsayılan cloudowner kullanıcısı yönetici ayrıcalıklarına sahip yeni bir kullanıcı oluşturmak için yeterli ayrıcalıklara sahip değil.

VRA ve SRM yüklemeden önce, cloudowner kullanıcısının vCenter ayrıcalıklarını ilerletin ve sonra vCenter SSO etki alanında yönetici ayrıcalıklarına sahip bir kullanıcı oluşturmanız gerekir. Varsayılan özel bulut kullanıcısı ve izin modeli hakkında daha fazla bilgi için bkz. [özel bulut izin modelini öğrenme](learn-private-cloud-permissions.md).

Yükleme bu üst düzey adımlardan oluşur:

1. [Ayrıcalıkları ilerletin](escalate-private-cloud-privileges.md).
2. VSphere çoğaltma ve SRM yüklemesi için özel bulutunuzda bir kullanıcı oluşturun. [VCenter Kullanıcı arabiriminde aşağıda açıklanmıştır: VRA & SRM yüklemesi](#vcenter-ui-create-a-user-in-private-cloud-for-vra-and-srm-installation)için özel bulutta bir kullanıcı oluşturun.
3. Özel bulut ortamınızı vRA yüklemesi için hazırlayın.
4. Vmware.com adresinden VR ISO 'daki OVF 'yi kullanarak özel bulutunuzda vRA 'yi dağıtın. VRA 6,5 için, [Bu VMware bloguna](https://blogs.vmware.com/virtualblocks/2017/01/20/vr-65-ovf-choices) ilgili bilgiler vardır.
5. VRA için güvenlik duvarı kurallarını yapılandırın. Cloudsimple Portal ['da aşağıda açıklanmıştır: VRA](#cloudsimple-portal-configure-firewall-rules-for-vra)için güvenlik duvarı kurallarını yapılandırın.
6. Özel bulut sitesinde özel bulut vRA 'yi vCenter çoklu oturum açma ile kaydedin.
7. İki gereç arasında vSphere çoğaltma bağlantılarını yapılandırma. Güvenlik duvarları genelinde gerekli bağlantı noktalarının açıldığından emin olun. VSphere çoğaltma 6,5 için açık olması gereken bağlantı noktası numaraları listesi için [Bu VMware Bilgi Bankası makalesine](https://kb.vmware.com/s/article/2087769) bakın.

VSphere çoğaltma 6,5 için ayrıntılı yükleme yönergeleri için bkz. VMware Document [VMware vSphere replication 6,5 yükleme ve yapılandırma](https://docs.vmware.com/en/vSphere-Replication/6.5/vsphere-replication-65-install.pdf).

#### <a name="vcenter-ui-create-a-user-in-private-cloud-for-vra-and-srm-installation"></a>vCenter Kullanıcı arabirimi: VRA ve SRM yüklemesi için özel bulutta Kullanıcı oluşturma

CloudSimple portalından ayrıcalıkların ilerleme işleminden sonra cloudowner Kullanıcı kimlik bilgilerini kullanarak vCenter 'da oturum açın.

VCenter 'da yeni bir Kullanıcı `srm-soln-admin`oluşturun ve vCenter 'daki Yöneticiler grubuna ekleyin.
Cloudowner kullanıcısı olarak vCenter oturumunu kapatın ve *SRM-soln-yönetici* kullanıcısı olarak oturum açın.

#### <a name="cloudsimple-portal-configure-firewall-rules-for-vra"></a>CloudSimple Portalı: VRA için güvenlik duvarı kurallarını yapılandırma

Aralarında iletişim sağlamak için bağlantı noktalarını açmak üzere [güvenlik duvarı tablolarını ve kuralları ayarlama](firewall.md) bölümünde açıklandığı gibi güvenlik duvarı kurallarını yapılandırın:

* SRM çözümü ağı ve vCenter ve ESXi yönetim ağında bulunan vRA.
* iki sitede vRA gereçleri.

VSphere çoğaltma 6,5 için açık olması gereken bağlantı noktası numaraları listesi için bu [VMware Bilgi Bankası makalesine](https://kb.vmware.com/s/article/2087769) bakın.

### <a name="install-srm-server-in-your-on-premises-environment"></a>Şirket içi ortamınıza SRM sunucusu 'nı yüklemeyin

Başlamadan önce aşağıdakileri doğrulayın:

* vSphere çoğaltma gereci şirket içi ve özel bulut ortamlarınıza yüklenir.
* Her iki sitedeki vSphere çoğaltma araçları birbirlerine bağlanır.
* Ön koşullar ve en iyi uygulamalar hakkında VMware bilgilerini gözden geçirdiniz. SRM 6,5 için, VMware belge [Önkoşulları ve SRM 6,5 Için En Iyi uygulamalar](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-BB0C03E4-72BE-4C74-96C3-97AC6911B6B8.html)bölümüne başvurabilirsiniz.

Bu [VMware belgesinde](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-F474543A-88C5-4030-BB86-F7CC51DADE22.html)açıklandığı gibi, dağıtım modelinde "platform hizmetleri denetleyicisi başına bir vCenter örneği Ile Iki site topolojisi" olan bir SRM sunucu yüklemesi gerçekleştirmek için VMware belgelerini izleyin. SRM 6,5 yükleme yönergeleri [Site Recovery Manager 'ı yükleyen](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html)VMware belgesinde bulunabilir.

### <a name="install-srm-server-in-your-private-cloud"></a>Özel bulutunuzda SRM sunucusunu yükler

Başlamadan önce aşağıdakileri doğrulayın:

* vSphere çoğaltma gereci şirket içi ve özel bulut ortamlarınıza yüklenir.
* Her iki sitedeki vSphere çoğaltma araçları birbirlerine bağlanır.
* Ön koşullar ve en iyi uygulamalar hakkında VMware bilgilerini gözden geçirdiniz. SRM 6,5 için [Site Recovery Manager 6,5 sunucu yüklemesi Için önkoşulları ve En Iyi yöntemlere](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-BB0C03E4-72BE-4C74-96C3-97AC6911B6B8.html)başvurabilirsiniz.

Aşağıdaki adımlarda özel bulut SRM yüklemesi açıklanır.

1. [vCenter Kullanıcı arabirimi: SRM 'yi yüklemeli](#vcenter-ui-install-srm)
2. [CloudSimple Portalı: SRM için güvenlik duvarı kurallarını yapılandırma](#cloudsimple-portal-configure-firewall-rules-for-srm)
3. [vCenter Kullanıcı arabirimi: SRM yapılandırma](#vcenter-ui-configure-srm)
4. [CloudSimple Portal: ön yükseltme ayrıcalıkları](#cloudsimple-portal-de-escalate-privileges)

#### <a name="vcenter-ui-install-srm"></a>vCenter Kullanıcı arabirimi: SRM 'yi yüklemeli

SRM-soln-yönetici kimlik bilgilerini kullanarak vCenter 'a oturum açtıktan sonra, bu VMware 'de açıklandığı şekilde dağıtım modelinde ' her platform hizmeti denetleyicisi için bir vCenter örneğine sahip Iki site topolojisi [ Belge](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-F474543A-88C5-4030-BB86-F7CC51DADE22.html). SRM 6,5 yükleme yönergeleri [Site Recovery Manager 'ı yükleyen](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html)VMware belgesinde bulunabilir.

#### <a name="cloudsimple-portal-configure-firewall-rules-for-srm"></a>CloudSimple Portalı: SRM için güvenlik duvarı kurallarını yapılandırma

Güvenlik duvarı kurallarını, aralarında iletişime izin vermek için [güvenlik duvarı tabloları ve kuralları ayarlama](firewall.md) bölümünde açıklandığı şekilde yapılandırın:

Özel bulutta SRM sunucusu ve vCenter/PSC.
Her iki sitedeki SRM sunucuları

VSphere çoğaltma 6,5 için açık olması gereken bağlantı noktası numaraları listesi için [Bu VMware Bilgi Bankası makalesine](https://kb.vmware.com/s/article/2087769) bakın.

#### <a name="vcenter-ui-configure-srm"></a>vCenter Kullanıcı arabirimi: SRM yapılandırma

SRM özel buluta yüklendikten sonra, VMware Site Recovery Manager yükleme ve yapılandırma kılavuzunun bölümlerinde açıklandığı gibi aşağıdaki görevleri gerçekleştirin. SRM 6,5 için, yönergeler [Site Recovery Manager 'ı yükleyen](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html)VMware belgesinde kullanılabilir.

1. Korunan ve kurtarma sitelerindeki Site Recovery Manager sunucusu örneklerini bağlayın.
2. Uzak Site Recovery Manager sunucu örneğine Istemci bağlantısı kurun.
3. Site Recovery Manager lisans anahtarını yükler.

#### <a name="cloudsimple-portal-de-escalate-privileges"></a>CloudSimple Portalı: Ön yükseltme ayrıcalıkları

Ayrıcalıkları devre dışı bırakmak için bkz. [yükseltme ayrıcalıkları](escalate-private-cloud-privileges.md#de-escalate-privileges).

## <a name="ongoing-management-of-your-srm-solution"></a>SRM çözümünüzün devam eden yönetimi

Özel bulut ortamınızda vSphere çoğaltma ve SRM yazılımı üzerinde tam denetime sahip olursunuz ve gerekli yazılım yaşam döngüsü yönetimini gerçekleştirmesi beklenir. VSphere çoğaltmasını veya SRM 'i güncelleştirmeden veya yükseltmeden önce yazılımın yeni bir sürümünün özel bulut vCenter ve PSC ile uyumlu olduğundan emin olun.

> [!NOTE]
> CloudSimple Şu anda yönetilen bir DR hizmeti sunma seçeneklerini araştırmakta. 

## <a name="multiple-replication-configuration"></a>Birden çok çoğaltma yapılandırması

 [Dizi tabanlı çoğaltma ve vSphere çoğaltma teknolojileri aynı anda SRM ile birlikte kullanılabilir](https://blogs.vmware.com/virtualblocks/2017/06/22/srm-array-based-replication-vs-vsphere-replication) . Ancak, bunların ayrı bir VM kümesine uygulanmaları gerekir (belirli bir VM, dizi tabanlı çoğaltma veya vSphere çoğaltması tarafından korunabilir, ancak her ikisi birden değil). Ayrıca, CloudSimple sitesi birden çok korumalı site için bir kurtarma sitesi olarak yapılandırılabilir. Birden çok site yapılandırması hakkında bilgi için bkz. [SRM çok siteli seçenekler](https://blogs.vmware.com/virtualblocks/2016/07/28/srm-multisite/) .

## <a name="references"></a>Referanslar

* [VMware Site Recovery Manager belgeleri](https://docs.vmware.com/en/Site-Recovery-Manager/index.html)
* [Site Recovery Manager 6,5 için işlemsel sınırlar](https://kb.vmware.com/s/article/2147110)
* [VSphere çoğaltması için bant genişliği gereksinimleri hesaplanıyor](https://kb.vmware.com/s/article/2037268)
* [VSphere çoğaltma 6,5 ' i dağıttığınızda OVF seçimleri](https://blogs.vmware.com/virtualblocks/2017/01/20/vr-65-ovf-choices/)
* [VMware vSphere çoğaltma 6,5 yükleme ve yapılandırma](https://docs.vmware.com/en/vSphere-Replication/6.5/vsphere-replication-65-install.pdf)
* [SRM 6,5 için Önkoşullar ve En Iyi uygulamalar](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-BB0C03E4-72BE-4C74-96C3-97AC6911B6B8.html)
* [Platform Hizmetleri denetleyicisi başına bir vCenter Server örneği ile Iki siteli topolojide Site Recovery Manager](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-F474543A-88C5-4030-BB86-F7CC51DADE22.html)
* [VMware Site Recovery Manager 6,5 yükleme ve yapılandırma kılavuzu](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html)
* [Dizi tabanlı çoğaltma ve vSphere çoğaltma ile SRM üzerinde VMware blogu](https://blogs.vmware.com/virtualblocks/2017/06/22/srm-array-based-replication-vs-vsphere-replication)
* [SRM çoklu site seçeneklerinde VMware blogu](https://blogs.vmware.com/virtualblocks/2016/07/28/srm-multisite)
* [VSphere çoğaltması 5,8. x, 6. x ve 8 için açık olması gereken bağlantı noktası numaraları](https://kb.vmware.com/s/article/2147112)
