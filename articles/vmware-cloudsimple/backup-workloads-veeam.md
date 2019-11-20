---
title: Cloudead kullanarak özel bulutta iş yükü sanal makinelerini CloudSimple ile Azure VMware çözümü
description: Veeam B & R 9,5 kullanarak Azure tabanlı CloudSimple özel bulutu 'nda çalışan sanal makinelerinizi nasıl yedekleyebileceğinizi açıklar.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 3414cc54e5023bdeebb2d5536c1408f981e68f19
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73891393"
---
# <a name="back-up-workload-vms-on-cloudsimple-private-cloud-using-veeam-br"></a>Jeam B & R kullanarak CloudSimple özel bulutunda iş yükü VM 'lerini yedekleme

Bu kılavuzda, Veeam B & R 9,5 kullanarak Azure tabanlı CloudSimple özel bulutu 'nda çalışan sanal makinelerinizi nasıl yedekleyebileceğiniz açıklanmaktadır.

## <a name="about-the-veeam-back-up-and-recovery-solution"></a>Veead yedekleme ve kurtarma çözümü hakkında

Veeab çözümü aşağıdaki bileşenleri içerir.

**Yedekleme sunucusu**

Yedekleme sunucusu, Veead için Denetim Merkezi görevi gören bir Windows Server (VM) ve şu işlevleri gerçekleştirir: 

* Yedekleme, çoğaltma, kurtarma doğrulama ve geri yükleme görevlerini düzenler
* İş zamanlama ve kaynak ayırmayı denetler
* Yedekleme altyapısı bileşenlerini ayarlamanıza ve yönetmenize ve yedekleme altyapısının genel ayarlarını belirtmenize olanak tanır

**Proxy sunucular**

Yedekleme sunucusu ve yedekleme altyapısının diğer bileşenleri arasında proxy sunucular yüklenir. Aşağıdaki işlevleri yönetir:

* Üretim depolamadan VM verilerini alma
* Sıkıştırma
* Yinelenenleri kaldırma
* Şifreleme
* Yedekleme deposuna veri iletimi

**Yedekleme deposu**

Yedekleme deposu, Veead 'nin çoğaltılan VM 'Ler için yedekleme dosyalarını, VM kopyalarını ve meta verileri sakladığı depolama konumudur.  Depo, yerel diskler (veya bağlı NFS/SMB) veya bir donanım depolama yinelenenleri kaldırma gereci olan bir Windows veya Linux sunucusu olabilir.

### <a name="veeam-deployment-scenarios"></a>Veead dağıtım senaryoları
Bir yedekleme deposu ve uzun süreli yedekleme ve arşivleme için bir depolama hedefi sağlamak üzere Azure 'dan yararlanabilirsiniz. Özel buluttaki ve Azure 'daki yedekleme deposundaki VM 'Ler arasındaki tüm yedekleme ağ trafiği, yüksek bant genişliğine sahip düşük gecikme süreli bir bağlantı üzerinden hareket eder. Bölgeler arasında çoğaltma trafiği, kullanıcılar için bant genişliği maliyetlerini düşürür.

**Temel dağıtım**

Yedeklenme için 30 TB 'den az olan ortamlarda CloudSimple aşağıdaki yapılandırmayı önerir:

* Veeab yedekleme sunucusu ve proxy sunucusu özel buluttaki aynı VM 'ye yüklendi.
* Azure 'da yedekleme işleri için hedef olarak yapılandırılmış Linux tabanlı birincil yedekleme deposu.
* `azcopy`, verileri birincil yedekleme deposundan başka bir bölgeye çoğaltılan bir Azure Blob kapsayıcısına kopyalamak için kullanılır.

![Temel dağıtım senaryoları](media/veeam-basicdeployment.png)

**Gelişmiş dağıtım**

Yedeklenme için 30 TB 'tan fazla olan ortamlarda CloudSimple aşağıdaki yapılandırmayı önerir:

* Veead tarafından önerildiği şekilde, vSAN kümesindeki düğüm başına bir ara sunucu.
* Hızlı geri yüklemeler için beş günlük veriyi önbelleğe almak üzere özel buluttaki Windows tabanlı birincil yedekleme deposu.
* Azure 'daki Linux yedekleme deposu, daha uzun süreli saklama için yedekleme kopyalama işlerinin hedefi olarak. Bu depo, genişleme yedekleme deposu olarak yapılandırılmalıdır.
* `azcopy`, verileri birincil yedekleme deposundan başka bir bölgeye çoğaltılan bir Azure Blob kapsayıcısına kopyalamak için kullanılır.

![Temel dağıtım senaryoları](media/veeam-advanceddeployment.png)

Önceki şekilde, yedekleme proxy 'sinin, vSAN veri deposundaki iş yükü VM disklerine sık erişimli ekleme erişimi olan bir VM olduğuna dikkat edin. Veead, vSAN için Sanal Gereç yedekleme proxy 'si aktarım modunu kullanır.

## <a name="requirements-for-veeam-solution-on-cloudsimple"></a>CloudSimple 'da Veead çözümü için gereksinimler

Veeab çözümü şunları yapmanızı gerektirir:

* Kendi Veead lisanslarınızı sağlayın.
* CloudSimple özel bulutu 'nda çalışan iş yüklerini yedeklemek için Veead 'yi dağıtın ve yönetin.

Bu çözüm, Veead yedekleme aracı üzerinde tam denetim sağlar ve VM yedekleme işlerini yönetmek için yerel Veead arabirimini veya Veead vCenter eklentisini kullanma seçeneği sunar.

Var olan bir Veeab kullanıcısı varsa, Veead Çözüm bileşenlerinde bulunan bölümü atlayabilir ve doğrudan [Veead dağıtım senaryolarına](#veeam-deployment-scenarios)devam edebilirsiniz.

## <a name="install-and-configure-veeam-backups-in-your-cloudsimple-private-cloud"></a>CloudSimple özel bulutunuzda Veead yedeklemelerini yükleyip yapılandırın

Aşağıdaki bölümlerde, CloudSimple özel bulutunuz için bir Veead yedekleme çözümünün nasıl yükleneceği ve yapılandırılacağı açıklanır.

Dağıtım işlemi aşağıdaki adımlardan oluşur:

1. [vCenter Kullanıcı arabirimi: özel bulutunuzda altyapı hizmetleri ayarlama](#vcenter-ui-set-up-infrastructure-services-in-your-private-cloud)
2. [CloudSimple Portalı: Veead için özel bulut ağını ayarlama](#cloudsimple-private-cloud-set-up-private-cloud-networking-for-veeam)
3. [CloudSimple Portalı: Ilerleme ayrıcalıkları](#cloudsimple-private-cloud-escalate-privileges-for-cloudowner)
4. [Azure portal: Sanal ağınızı özel buluta bağlayın](#azure-portal-connect-your-virtual-network-to-the-private-cloud)
5. [Azure portal: Azure 'da bir yedekleme deposu oluşturun](#azure-portal-connect-your-virtual-network-to-the-private-cloud)
6. [Azure portal: uzun süreli veri saklama için Azure Blob depolamayı yapılandırma](#configure-azure-blob-storage-for-long-term-data-retention)
7. [Özel bulutun vCenter Kullanıcı arabirimi: Install Veeam B & R](#vcenter-console-of-private-cloud-install-veeam-br)
8. [Veeamconsole: Veead Backup & kurtarma yazılımını yapılandırma](#veeam-console-install-veeam-backup-and-recovery-software)
9. [CloudSimple Portal: Veead erişimi ayarlama ve bu ayrıcalıkları serbest bırakma](#cloudsimple-portal-set-up-veeam-access-and-de-escalate-privileges)

### <a name="before-you-begin"></a>Başlamadan önce

Veead dağıtımına başlamadan önce aşağıdakiler gereklidir:

* Size ait bir Azure aboneliği
* Önceden oluşturulmuş bir Azure Kaynak grubu
* Aboneliğinizdeki bir Azure sanal ağı
* Bir Azure depolama hesabı
* CloudSimple Portalı kullanılarak oluşturulan [özel bir bulut](create-private-cloud.md) .  

Uygulama aşamasında aşağıdaki öğeler gereklidir:

* Windows için VMware şablonları Veead (Windows Server 2012 R2-64 bit görüntü gibi)
* Yedekleme ağı için tanımlanan bir kullanılabilir VLAN
* Yedekleme ağına atanacak alt ağın CıDR 'i
* Veeaz 9,5 U3 yüklenebilen medya (ISO) özel bulutun vSAN veri deposuna yüklendi

### <a name="vcenter-ui-set-up-infrastructure-services-in-your-private-cloud"></a>vCenter Kullanıcı arabirimi: özel bulutunuzda altyapı hizmetleri ayarlama

İş yüklerinizi ve araçlarınızı yönetmeyi kolaylaştırmak için özel buluttaki altyapı hizmetlerini yapılandırın.

* Aşağıdakilerden biri varsa [Active Directory kullanmak Için vCenter Identity kaynaklarını ayarlama](set-vcenter-identity.md) bölümünde açıklandığı gibi bir dış kimlik sağlayıcısı ekleyebilirsiniz:

  * Şirket içi Active Directory (AD) kullanıcılarını özel bulutunuzda tanımlamak istiyorsunuz.
  * Tüm kullanıcılar için özel bulutunuzda bir AD ayarlamak istiyorsunuz.
  * Azure AD 'yi kullanmak istiyorsunuz.
* Özel buluttaki iş yükleriniz için IP adresi araması, IP adresi yönetimi ve ad çözümleme hizmetleri sağlamak istiyorsanız, [CloudSimple özel BULUTUNUZDA DNS ve DHCP uygulamalarını ve iş yüklerini ayarlama](dns-dhcp-setup.md)bölümünde açıklandığı gıbı bir DHCP ve DNS sunucusu ayarlayın.

### <a name="cloudsimple-private-cloud-set-up-private-cloud-networking-for-veeam"></a>CloudSimple özel bulutu: Veead için özel bulut ağını ayarlama

Veead çözümü için özel bulut ağı ayarlamak üzere CloudSimple portalına erişin.

Yedekleme ağı için bir VLAN oluşturun ve bir alt ağ CıDR atayın. Yönergeler için bkz. [VLAN/alt ağlar oluşturma ve yönetme](create-vlan-subnet.md).

Veead tarafından kullanılan bağlantı noktalarında ağ trafiğine izin vermek için yönetim alt ağı ve yedekleme ağı arasında güvenlik duvarı kuralları oluşturun. Bkz. Veeab konusunda [kullanılan bağlantı noktaları](https://helpcenter.veeam.com/docs/backup/vsphere/used_ports.html?ver=95). Güvenlik duvarı kuralı oluşturma yönergeleri için bkz. [güvenlik duvarı tablolarını ve kurallarını ayarlama](firewall.md).

Aşağıdaki tabloda bir bağlantı noktası listesi verilmiştir.

| simg | Açıklama | simg | Açıklama |
| ------------ | ------------- | ------------ | ------------- |
| Backup Sunucusu  | vCenter  | HTTPS/TCP  | 443 |
| Backup Sunucusu <br> *Veead yedeklemesini dağıtmak için gerekli & çoğaltma bileşenleri* | Yedekleme proxy 'Si  | TCP/UDP  | 135, 137-139 ve 445 |
    | Backup Sunucusu   | DNS  | PROTOKOLLERINDEN  | 53  | 
    | Backup Sunucusu   | Veeab güncelleştirme bildirimi sunucusu  | TCP  | 80  | 
    | Backup Sunucusu   | Veeab lisans güncelleştirme sunucusu  | TCP  | 443  | 
    | Yedekleme proxy 'Si   | vCenter |   |   | 
    | Yedekleme proxy 'Si  | Linux yedekleme deposu   | TCP  | 22  | 
    | Yedekleme proxy 'Si  | Windows yedekleme deposu  | TCP  | 49152-65535   | 
    | Yedekleme deposu  | Yedekleme proxy 'Si  | TCP  | 2500-5000  | 
    | Kaynak yedekleme deposu<br> *Yedekleme Kopyalama işleri için kullanılır*  | Hedef yedekleme deposu  | TCP  | 2500-5000  | 

[Güvenlik duvarı tablolarını ve kurallarını ayarlama](firewall.md)bölümünde açıklandığı gibi iş yükü alt ağı ve yedekleme ağı arasında güvenlik duvarı kuralları oluşturun.  Uygulamayla uyumlu yedekleme ve geri yükleme için, belirli uygulamaları barındıran iş yükü VM 'lerinde [ek bağlantı noktaları](https://helpcenter.veeam.com/docs/backup/vsphere/used_ports.html?ver=95) açılmalıdır.

Varsayılan olarak, CloudSimple, 1Gbps ExpressRoute bağlantısı sağlar. Daha büyük ortam boyutları için daha yüksek bir bant genişliği bağlantısı gerekebilir. Daha yüksek bant genişliği bağlantıları hakkında daha fazla bilgi için Azure desteğine başvurun.

Kuruluma devam etmek için yetkilendirme anahtarı ve eş devre URI 'sine ve Azure aboneliğinize erişmeniz gerekir.  Bu bilgiler, CloudSimple portalındaki sanal ağ bağlantısı sayfasında bulunur. Yönergeler için bkz. [Azure sanal ağı Için CloudSimple bağlantısı için eşleme bilgileri alma](virtual-network-connection.md). Bilgileri alırken sorun yaşarsanız [desteğe başvurun](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

### <a name="cloudsimple-private-cloud-escalate-privileges-for-cloudowner"></a>CloudSimple özel bulutu: cloudowner için Ilerletin ayrıcalıkları

Varsayılan ' cloudowner ' kullanıcısı, VEEAD 'yi yüklemek için özel bulut vCenter 'da yeterli ayrıcalıklara sahip değil, bu nedenle kullanıcının vCenter ayrıcalıkları ilerletilmiş olmalıdır. Daha fazla bilgi için bkz. [ayrıcalıkları Yükselt](escalate-private-cloud-privileges.md).

### <a name="azure-portal-connect-your-virtual-network-to-the-private-cloud"></a>Azure portal: Sanal ağınızı özel buluta bağlayın

[ExpressRoute kullanarak Azure sanal ağ bağlantısı](azure-expressroute-connection.md)'ndaki yönergeleri izleyerek sanal ağınızı özel buluta bağlayın.

### <a name="azure-portal-create-a-backup-repository-vm"></a>Azure portal: yedekleme deposu VM 'si oluşturma

1. (2 vCPU ve 8 GB bellek) ile standart bir D2 v3 VM oluşturun.
2. CentOS 7,4 tabanlı görüntüsünü seçin.
3. VM için bir ağ güvenlik grubu (NSG) yapılandırın. VM 'nin genel IP adresi olmadığından ve genel İnternet 'ten ulaşılamadiğini doğrulayın.
4. Yeni VM için bir Kullanıcı adı ve parola tabanlı kullanıcı hesabı oluşturun. Yönergeler için, bkz. [Azure Portal Linux sanal makinesi oluşturma](../virtual-machines/linux/quick-create-portal.md).
5. 1x512 GiB standart HDD oluşturun ve depo VM 'sine bağlayın.  Yönergeler için bkz. [Azure Portal bir WINDOWS sanal makinesine yönetilen veri diski iliştirme](../virtual-machines/windows/attach-managed-disk-portal.md).
6. [Yönetilen diskte BIR XFS birimi oluşturun](https://www.digitalocean.com/docs/volumes/how-to/). Daha önce belirtilen kimlik bilgilerini kullanarak VM 'de oturum açın. Bir mantıksal birim oluşturmak için aşağıdaki betiği yürütün, diski buna ekleyin, bir XFS FileSystem [bölümü](https://www.digitalocean.com/docs/volumes/how-to/partition/) oluşturun ve bölümü/backup1 yolu altına [bağlayın](https://www.digitalocean.com/docs/volumes/how-to/mount/) .

    Örnek komut dosyası:

    ```
    sudo pvcreate /dev/sdc
    sudo vgcreate backup1 /dev/sdc
    sudo lvcreate -n backup1 -l 100%FREE backup1
    sudo mkdir -p /backup1
    sudo chown veeamadmin /backup1
    sudo chmod 775 /backup1
    sudo mkfs.xfs -d su=64k -d sw=1 -f /dev/mapper/backup1-backup1
    sudo mount -t xfs /dev/mapper/backup1-backup1 /backup1
    ```

7. /Backup1 ' i özel bulutta çalışan Veead yedekleme sunucusuna NFS bağlama noktası olarak kullanıma sunun. Yönergeler için, [CentOS 6 ' da NFS bağlama ayarlama](https://www.digitalocean.com/community/tutorials/how-to-set-up-an-nfs-mount-on-centos-6)başlıklı dijital okyanus makalesine bakın. Bu NFS paylaşımının adını, Veead yedekleme sunucusunda Yedekleme deposunu yapılandırırken kullanın.

8. Yedekleme deposu sanal makinesi için NSG 'de filtreleme kurallarını, VM 'ye ve VM 'ye giden tüm ağ trafiğine açıkça izin verecek şekilde yapılandırın.

> [!NOTE]
> Veeab Backup & çoğaltma, Linux yedekleme depolarıyla iletişim kurmak için SSH protokolünü kullanır ve Linux depolarında SCP yardımcı programını gerektirir. SSH arka plan programının düzgün yapılandırıldığını ve bu SCP 'nin Linux ana bilgisayarında kullanılabilir olduğunu doğrulayın.

### <a name="configure-azure-blob-storage-for-long-term-data-retention"></a>Uzun süreli veri saklama için Azure Blob depolamayı yapılandırma

1. Microsoft videosunda [Azure Storage Ile çalışmaya başlama](https://azure.microsoft.com/resources/videos/get-started-with-azure-storage)bölümünde açıklandığı gibi standart türde ve bir blob kapsayıcısının genel amaçlı depolama hesabı (GPv2) oluşturun.
2. [Kapsayıcı oluşturma](https://docs.microsoft.com/rest/api/storageservices/create-container) başvurusunda açıklandığı gibi bir Azure depolama kapsayıcısı oluşturun.
2. Microsoft 'tan Linux için `azcopy` komut satırı yardımcı programını indirin. CentOS 7,5 ' de bash kabuğu 'nda aşağıdaki komutları kullanabilirsiniz.

    ```
    wget -O azcopy.tar.gz https://aka.ms/downloadazcopylinux64
    tar -xf azcopy.tar.gz
    sudo ./install.sh
    sudo yum -y install libunwind.x86_64
    sudo yum -y install icu
    ```

3. Yedekleme dosyalarını blob kapsayıcısından ve öğesinden kopyalamak için `azcopy` komutunu kullanın.  Ayrıntılı komutlar için bkz. [Linux üzerinde AzCopy ile veri aktarma](../storage/common/storage-use-azcopy-linux.md) .

### <a name="vcenter-console-of-private-cloud-install-veeam-br"></a>Özel bulutun vCenter konsolu: Install Veeam B & R

Özel bulutunuzun vCenter 'a erişerek bir Veeam hizmet hesabı oluşturun, Veeam B & R 9,5 ' yi yükleyip hizmet hesabını kullanarak Veeam 'yi yapılandırın.

1. ' Veeam Backup role ' adlı yeni bir rol oluşturun ve gerekli izinleri Veeam tarafından önerilen şekilde atayın. Ayrıntılar için bkz. Veeab konusu [gerekli izinler](https://helpcenter.veeam.com/docs/backup/vsphere/required_permissions.html?ver=95).
2. VCenter 'da yeni bir ' Veeam Kullanıcı grubu ' grubu oluşturun ve bunu ' Veeam yedekleme rolü ' olarak atayın.
3. Yeni bir ' Veeamservice hesabı ' kullanıcısı oluşturun ve ' Veeae kullanıcı grubuna ekleyin.

    ![Veead hizmet hesabı oluşturma](media/veeam-vcenter01.png)

4. VCenter 'da yedek ağ VLAN 'ıNı kullanarak dağıtılmış bir bağlantı noktası grubu oluşturun. Ayrıntılar için, [vSphere Web Istemcisinde dağıtılmış bir bağlantı noktası grubu oluşturan](https://www.youtube.com/watch?v=wpCd5ZbPOpA)VMware videosunu görüntüleyin.
5. Veead yedekleme ve proxy sunucuları için VM 'Leri, [veead sistem gereksinimlerine](https://helpcenter.veeam.com/docs/backup/vsphere/system_requirements.html?ver=95)göre vCenter 'da oluşturun. Windows 2012 R2 veya Linux 'u kullanabilirsiniz. Daha fazla bilgi için bkz. [Linux yedek depoları kullanma gereksinimleri](https://www.veeam.com/kb2216).
6. Veead yedekleme sunucusu VM 'sinde bir CDROM cihazı olarak yüklenebilir Veead ISO takın.
7. Windows 2012 R2 makinesinde bir RDP oturumu kullanarak (Veeam yüklemesi için hedef), bir Windows 2012 R2 sanal makinesine [Veeam B & R 9.5 U3 'yi yükleme](https://helpcenter.veeam.com/docs/backup/vsphere/install_vbr.html?ver=95) .
8. Veeab yedekleme sunucusu VM 'sinin iç IP adresini bulun ve IP adresini DHCP sunucusunda statik olacak şekilde yapılandırın. Bunu yapmak için gereken tam adımlar DHCP sunucusuna bağlıdır. Örnek olarak, <a href="https://www.netgate.com/docs/pfsense/dhcp/dhcp-server.html" target="_blank">STATIK DHCP eşlemeleri</a> NETGATE MAKALESINDE bir DHCP sunucusunun bir PFSense yönlendiricisi kullanılarak nasıl yapılandırılacağı açıklanmaktadır.

### <a name="veeam-console-install-veeam-backup-and-recovery-software"></a>Veead konsolu: Veead yedekleme ve kurtarma yazılımlarını yükler

Veead konsolunu kullanarak Veead yedekleme ve kurtarma yazılımlarını yapılandırın. Ayrıntılar için bkz. [Veead Backup & çoğaltma v9-yükleme ve dağıtım](https://www.youtube.com/watch?v=b4BqC_WXARk).

1. VMware vSphere yönetilen sunucu ortamı olarak ekleyin. İstendiğinde, özel bulutun vCenter konsolunun başlangıcında oluşturduğunuz Veeam hizmet hesabının kimlik bilgilerini sağlayın [: Veeam B & R 'Yi yükler](#vcenter-console-of-private-cloud-install-veeam-br).

    * Yükleme denetimi ve varsayılan Gelişmiş ayarlar için varsayılan ayarları kullanın.
    * Bağlama sunucusunun konumunu yedekleme sunucusu olacak şekilde ayarlayın.
    * Veead sunucusu için yapılandırma yedekleme konumunu uzak depoya değiştirin.

2. Azure 'da Linux sunucusunu yedekleme deposu olarak ekleyin.

    * Yükleme denetimi ve Gelişmiş ayarlar için varsayılan ayarları kullanın. 
    * Bağlama sunucusunun konumunu yedekleme sunucusu olacak şekilde ayarlayın.
    * Veead sunucusu için yapılandırma yedekleme konumunu uzak depoya değiştirin.

3. **Ana > yapılandırma yedekleme ayarlarını**kullanarak yapılandırma yedeklemesi şifrelemesini etkinleştirin.

4. VMware ortamı için proxy sunucusu olarak bir Windows Server VM 'si ekleyin. Bir ara sunucu için ' trafik kuralları ' ' nı kullanarak, yedekleme verilerini kablo üzerinden şifreleyin.

5. Yedekleme işlerini yapılandırın.
    * Yedekleme işlerini yapılandırmak için, [yedekleme Işi oluşturma](https://www.youtube.com/watch?v=YHxcUFEss4M)bölümündeki yönergeleri izleyin.
    * **Gelişmiş ayarlar altında depolama >** yedekleme dosyalarının şifrelenmesini etkinleştirin.

6. Yedekleme Kopyalama işlerini yapılandırın.

    * Yedek kopya işlerini yapılandırmak için, video [Yedekleme Kopyalama oluşturma Işi oluşturma](https://www.youtube.com/watch?v=LvEHV0_WDWI&t=2s)içindeki yönergeleri izleyin.
    * **Gelişmiş ayarlar altında depolama >** yedekleme dosyalarının şifrelenmesini etkinleştirin.

### <a name="cloudsimple-portal-set-up-veeam-access-and-de-escalate-privileges"></a>CloudSimple Portal: Veead erişimi ayarlama ve bu ayrıcalıkları serbest bırakma
Veead yedekleme ve kurtarma sunucusu için genel bir IP adresi oluşturun. Yönergeler için bkz. [genel IP adresleri ayırma](public-ips.md).

Veeab yedekleme sunucusunun, TCP bağlantı noktası 80 ' de güncelleştirmeleri/düzeltme eklerini indirmek üzere bir giden bağlantı oluşturmasına izin vermek için kullanarak bir güvenlik duvarı kuralı oluşturun. Yönergeler için bkz. [güvenlik duvarı tablolarını ve kurallarını ayarlama](firewall.md).

Ayrıcalıkları devre dışı bırakmak için bkz. [yükseltme ayrıcalıkları](escalate-private-cloud-privileges.md#de-escalate-privileges).

## <a name="references"></a>Başvurular

### <a name="cloudsimple-references"></a>CloudSimple başvuruları

* [Özel Bulut oluşturma](create-private-cloud.md)
* [VLAN/alt ağlar oluşturma ve yönetme](create-vlan-subnet.md)
* [vCenter Identity kaynakları](set-vcenter-identity.md)
* [İş yükü DNS ve DHCP kurulumu](dns-dhcp-setup.md)
* [Ayrıcalıkları ilerlet](escalate-privileges.md)
* [Güvenlik Duvarı tablolarını ve kurallarını ayarlama](firewall.md)
* [Özel bulut izinleri](learn-private-cloud-permissions.md)
* [Genel IP adreslerini ayır](public-ips.md)

### <a name="veeam-references"></a>Veeay başvuruları

* [Kullanılan bağlantı noktaları](https://helpcenter.veeam.com/docs/backup/vsphere/used_ports.html?ver=95)
* [Gerekli Izinler](https://helpcenter.veeam.com/docs/backup/vsphere/required_permissions.html?ver=95)
* [Sistem gereksinimleri](https://helpcenter.veeam.com/docs/backup/vsphere/system_requirements.html?ver=95)
* [Veead yedekleme & çoğaltma yükleniyor](https://helpcenter.veeam.com/docs/backup/vsphere/install_vbr.html?ver=95)
* [Linux için birden çok işletim sistemi FLR ve depo desteği için gerekli modüller ve izinler](https://www.veeam.com/kb2216)
* [Veead Backup & çoğaltma v9-yükleme ve dağıtım-video](https://www.youtube.com/watch?v=b4BqC_WXARk)
* [Veeab v9 yedekleme Işi oluşturma-video](https://www.youtube.com/watch?v=YHxcUFEss4M)
* [Veead v9 bir yedekleme kopyalama Işi oluşturma-video](https://www.youtube.com/watch?v=LvEHV0_WDWI&t=2s)

### <a name="azure-references"></a>Azure başvuruları

* [Azure portal kullanarak ExpressRoute için sanal ağ geçidi yapılandırma](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)
* [VNet 'i devre dışı bir aboneliğe bağlama](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#connect-a-vnet-to-a-circuit---different-subscription)
* [Azure portal bir Linux sanal makinesi oluşturun](../virtual-machines/linux/quick-create-portal.md)
* [Azure portal bir Windows sanal makinesine yönetilen veri diski iliştirme](../virtual-machines/windows/attach-managed-disk-portal.md)
* [Azure depolama ile çalışmaya başlama-video](https://azure.microsoft.com/resources/videos/get-started-with-azure-storage)
* [Kapsayıcı oluştur](https://docs.microsoft.com/rest/api/storageservices/create-container)
* [Linux üzerinde AzCopy ile veri aktarma](../storage/common/storage-use-azcopy-linux.md)

### <a name="vmware-references"></a>VMware başvuruları

* [VSphere Web Istemcisinde dağıtılmış bir bağlantı noktası grubu oluşturma-video](https://www.youtube.com/watch?v=wpCd5ZbPOpA)

### <a name="other-references"></a>Diğer başvurular

* [Yönetilen disk-RedHat üzerinde bir XFS birimi oluşturun](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/storage_administration_guide/ch-xfs)
* [CentOS 7 ' de bir NFS bağlama ayarlama-HowToForge](https://www.howtoforge.com/nfs-server-and-client-on-centos-7)
* [DHCP sunucusu-NETGATE yapılandırma](https://www.netgate.com/docs/pfsense/dhcp/dhcp-server.html)
