---
title: Azure Site Recovery VMware vCenter sunucularını yönetme
description: Bu makalede, Azure Site Recovery ile Azure 'a VMware VM 'lerinin olağanüstü durum kurtarması için VMware vCenter 'ın nasıl ekleneceği ve yönetileceği açıklanmaktadır.
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: ramamill
ms.openlocfilehash: 8f339103f67f37d10999ef43fa57a6eb27b60f37
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74083970"
---
# <a name="manage-vmware-vcenter-server"></a>VMware vCenter Server 'ı yönetme

Bu makalede, bir VMware vCenter üzerinde gerçekleştirilebilecek çeşitli Site Recovery işlemleri açıklanmaktadır. Başlamadan önce [önkoşulları](vmware-physical-azure-support-matrix.md#replicated-machines) doğrulayın.


## <a name="set-up-an-account-for-automatic-discovery"></a>Otomatik bulma için bir hesap ayarlayın

Site Recovery, işlem sunucusunun sanal makineleri otomatik olarak bulması ve sanal makinelerin yük devretme ve yeniden çalışma için VMware 'e erişmesi gerekir. Erişim için aşağıdaki gibi bir hesap oluşturun:

1. Yapılandırma sunucusu makinesinde oturum açın.
2. Masaüstü kısayolunu kullanarak Cspsconfigtool. exe ' yi başlatın.
3. **Hesabı Yönet** sekmesinde **Hesap Ekle** ' ye tıklayın.

   ![Hesap Ekle](./media/vmware-azure-manage-vcenter/addaccount.png)
1. Hesap ayrıntılarını girip **Tamam** ' a tıklayarak ekleyin.  Hesap, aşağıdaki tabloda özetlenen ayrıcalıklara sahip olmalıdır. 

Hesap bilgilerinin Site Recovery hizmetiyle eşitlenmesi yaklaşık 15 dakika sürer.

### <a name="account-permissions"></a>Hesap izinleri

|**Görev** | **Hesap** | **İzinler** | **Ayrıntılar**|
|--- | --- | --- | ---|
|**Otomatik keşif/geçiş (yeniden çalışma olmadan)** | En az bir salt okunurdur Kullanıcı gerekir | Veri Merkezi nesnesi –> Alt Nesneye Yay, role=Read-only | Kullanıcı veri merkezi düzeyinde atandı ve bu veri merkezindeki tüm nesnelere erişimi var.<br/><br/> Erişimi kısıtlamak için, **alt nesnelere yay** nesnesine göre **erişim** rolü (vSphere Konakları, veri depoları, sanal makineler ve ağlar) atayın.|
|**Çoğaltma/yük devretme** | En az bir salt okunurdur Kullanıcı gerekir| Veri Merkezi nesnesi –> Alt Nesneye Yay, role=Read-only | Kullanıcı veri merkezi düzeyinde atandı ve bu veri merkezindeki tüm nesnelere erişimi var.<br/><br/> Erişimi kısıtlamak için, alt nesnelere (vSphere Konakları, datamağazalar, sanal makineler ve ağlar), **alt nesneye yay** ile **hiçbir erişim** rolü atayın.<br/><br/> Geçiş amaçları için kullanışlıdır, ancak tam çoğaltma, yük devretme, yeniden çalışma.|
|**Çoğaltma/yük devretme/yeniden çalışma** | Gerekli izinlerle bir rol (AzureSiteRecoveryRole) oluşturmanızı ve ardından rolü bir VMware kullanıcısına veya grubuna atamayı öneririz | Veri merkezi nesnesi – > alt nesneye yay, rol = AzureSiteRecoveryRole<br/><br/> Veri deposu -> Alan ayırma, veri deposuna göz atma, düşük düzeyli dosya işlemleri, dosyayı kaldırma, sanal makine dosyalarını güncelleştirme<br/><br/> Ağ -> Ağ ataması<br/><br/> Kaynak -> VM’yi kaynak havuzuna atama, kapalı VM’yi geçirme, açık VM’yi geçirme<br/><br/> Görevler -> Görev oluşturma, görevi güncelleştirme<br/><br/> Sanal makine -> Yapılandırma<br/><br/> Sanal makine -> Etkileşim -> soruyu yanıtlama, cihaz bağlantısı, CD ortamını yapılandırma, disket ortamını yapılandırma, kapatma, açma, VMware araçlarını yükleme<br/><br/> Sanal makine -> Envanter -> Oluşturma, kaydetme, kaydı kaldırma<br/><br/> Sanal makine -> Sağlama -> Sanal makine indirmeye izin verme, Sanal makine dosyalarını karşıya yüklemeye izin verme<br/><br/> Sanal makine -> Anlık görüntüler -> Anlık görüntüleri kaldırma | Kullanıcı veri merkezi düzeyinde atandı ve bu veri merkezindeki tüm nesnelere erişimi var.<br/><br/> Erişimi kısıtlamak için, **alt nesnelere yay** nesnesine göre **erişim** rolü (vSphere Konakları, veri depoları, sanal makineler ve ağlar) atayın.|


## <a name="add-vmware-server-to-the-vault"></a>VMware sunucusunu kasaya ekleme

1. Azure portal, > **altyapınızı Site Recovery altyapıyı** > **yapılandırma mühürlerinizi**açın ve yapılandırma sunucusunu açın.
2. **Ayrıntılar** sayfasında **+ vCenter**' a tıklayın.

[!INCLUDE [site-recovery-add-vcenter](../../includes/site-recovery-add-vcenter.md)]

## <a name="modify-credentials"></a>Kimlik bilgilerini değiştir

VCenter sunucusuna veya ESXi konağına bağlanmak için kullanılan kimlik bilgilerini aşağıdaki gibi değiştirin:

1. Yapılandırma sunucusunda oturum açın ve masaüstündeki Cspsconfigtool. exe ' yi başlatın.
2. **Hesabı Yönet** sekmesinde **Hesap Ekle** ' ye tıklayın.

   ![Hesap Ekle](./media/vmware-azure-manage-vcenter/addaccount.png)
3. Yeni hesap ayrıntılarını girip **Tamam** ' a tıklayarak ekleyin. Hesabın [yukarıda](#account-permissions)listelenen ayrıcalıklara sahip olması gerekir.
4. Azure portal, altyapı > **yapılandırma**ve yapılandırma sunucusunu açmak için kasa > **Site Recovery** açın.
5. **Ayrıntılar** sayfasında, **sunucuyu Yenile**' ye tıklayın.
6. Sunucuyu Yenile işi tamamlandıktan sonra, vCenter **Özet** sayfasını açmak için vCenter Server seçin.
7. **VCenter Server/vSphere konak hesabı** alanında yeni eklenen hesabı seçin ve **Kaydet**' e tıklayın.

   ![değiştirme hesabı](./media/vmware-azure-manage-vcenter/modify-vcente-creds.png)

## <a name="delete-a-vcenter-server"></a>VCenter sunucusunu silme

1. Azure portal, > **altyapınızı Site Recovery altyapıyı** > **yapılandırma mühürlerinizi**açın ve yapılandırma sunucusunu açın.
2. **Ayrıntılar** sayfasında vCenter Server ' ı seçin.
3. **Sil** düğmesine tıklayın.

   ![Hesabı Sil](./media/vmware-azure-manage-vcenter/delete-vcenter.png)

## <a name="modify-the-vcenter-ip-address-and-port"></a>VCenter IP adresini ve bağlantı noktasını değiştirme

1. Azure Portal’da oturum açın.
2. **Kurtarma Hizmetleri kasası** > **Site Recovery altyapı** > **yapılandırma sunucularına**gidin.
3. VCenter 'ın atandığı yapılandırma sunucusuna tıklayın.
4. **VCenter Servers** bölümünde, değiştirmek istediğiniz vCenter 'a tıklayın.
5. VCenter Summary sayfasında, ilgili alanlarda vCenter 'un IP adresini ve bağlantı noktasını güncelleştirin ve ardından değişikliklerinizi kaydedin.

   ![add_ip_new_vcenter](media/vmware-azure-manage-vcenter/add-ip.png)

6. Değişikliklerin etkili olması için 15 dakika bekleyin veya [yapılandırma sunucusunu yenileyin](vmware-azure-manage-configuration-server.md#refresh-configuration-server).

## <a name="migrate-all-protected-virtual-machines-to-a-new-vcenter"></a>Tüm korumalı sanal makineleri yeni bir vCenter 'a geçir

Tüm sanal makineleri yeni vCenter 'a geçirmek için başka bir vCenter hesabı eklemeyin. Bu, yinelenen girişlere yol açabilir. Yalnızca yeni vCenter 'un IP adresini güncelleştirin:

1. Azure Portal’da oturum açın.
2. **Kurtarma Hizmetleri kasası** > **Site Recovery altyapı** > **yapılandırma sunucularına**gidin.
3. Eski vCenter 'ın atandığı yapılandırma sunucusuna tıklayın.
4. **VCenter Servers** bölümünde, geçiş yapmak istediğiniz vCenter 'a tıklayın.
5. VCenter Summary sayfasında, **vCenter Server/vSphere konak adı veya IP adresi**alanındaki yeni vCenter 'un IP adresini güncelleştirin. Yaptığınız değişiklikleri kaydedin.

IP adresi güncelleştirildikten hemen sonra, Site Recovery bileşenleri yeni vCenter 'dan sanal makinelerin bulma bilgilerini almaya başlar. Bu işlem devam eden çoğaltma etkinliklerini etkilemez.

## <a name="migrate-few-protected-virtual-machines-to-a-new-vcenter"></a>Birkaç korumalı sanal makineyi yeni bir vCenter 'a geçirme

> [!NOTE]
> Bu bölüm yalnızca, korunan sanal makinelerinizin birkaçını yeni bir vCenter 'a geçirdiğinizde geçerlidir. Yeni bir vCenter 'dan yeni bir sanal makine kümesini korumak istiyorsanız, [yapılandırma sunucusuna yeni vCenter ayrıntıları ekleyin](#add-vmware-server-to-the-vault) ve **[korumayı etkinleştir](vmware-azure-tutorial.md#enable-replication)** ' i kullanmaya başlayın.

Birkaç sanal makineyi yeni bir vCenter 'a taşımak için:

1. [Yeni vCenter ayrıntılarını yapılandırma sunucusuna ekleyin](#add-vmware-server-to-the-vault).
2. Geçirmeyi planladığınız [sanal makinelerin çoğaltmasını devre dışı bırakın](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) .
3. Seçili sanal makinelerin yeni vCenter 'a geçişini doldurun.
4. Şimdi, [korumayı etkinleştirdiğinizde yeni vCenter](vmware-azure-tutorial.md#enable-replication)' ı seçerek geçirilen sanal makineleri koruyun.

> [!TIP]
> Geçirilmekte olan sanal makinelerin sayısı, eski vCenter 'da saklanan sanal makine sayısı sayısından **yüksekse** , burada verilen yönergeleri kullanarak yeni vCenter 'un IP adresini güncelleştirin. Eski vCenter 'da tutulan bazı sanal makineler için [çoğaltmayı devre dışı bırakın](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure); [yapılandırma sunucusuna yeni vCenter ayrıntıları ekleyin](#add-vmware-server-to-the-vault)ve **[korumayı etkinleştir](vmware-azure-tutorial.md#enable-replication)** ' i başlatın.

## <a name="frequently-asked-questions"></a>Sık sorulan sorular

1. Korunan sanal makineler bir ESXi konağından diğerine taşınırsa çoğaltmayı etkiler mi?

    Hayır, bu işlem devam eden çoğaltmayı etkilemez. Ancak, [ana hedef sunucuyu yeterli ayrıcalıklarla dağıttığınızdan emin olun](vmware-azure-reprotect.md#deploy-a-separate-master-target-server)

2. VCenter ve diğer Site Recovery bileşenleri arasındaki iletişimde kullanılan bağlantı noktası numaraları nelerdir?

    Varsayılan bağlantı noktası 443 ' dir. Yapılandırma sunucusu, vCenter/vSphere ana bilgisayar bilgilerine bu bağlantı noktası üzerinden erişir. Bu bilgileri güncelleştirmek istiyorsanız [buraya](#modify-the-vcenter-ip-address-and-port)tıklayın.
