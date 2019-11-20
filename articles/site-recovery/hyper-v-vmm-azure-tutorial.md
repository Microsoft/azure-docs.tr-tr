---
title: Azure Site Recovery kullanarak Hyper-V (VMM ile) olağanüstü durum kurtarma ayarlama
description: Site Recovery kullanarak, System Center VMM bulutlarındaki şirket içi Hyper-V VM 'lerinin olağanüstü durum kurtarma ayarlamayı öğrenin.
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 0c570702e4c3899ef2847883e6fc8649e603a787
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74039682"
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-in-vmm-clouds-to-azure"></a>VMM bulutlarındaki şirket içi Hyper-V VM 'lerinin Azure 'a olağanüstü durum kurtarma ayarlama

Bu makalede, [Azure Site Recovery](site-recovery-overview.md) hizmeti kullanılarak Azure 'a olağanüstü durum kurtarma için System Center VIRTUAL MACHINE Manager (VMM) tarafından yönetilen şirket içi Hyper-V VM 'lerinin nasıl etkinleştirileceği açıklanır. VMM kullanmıyorsanız bunun yerine [Bu öğreticiyi izleyin](hyper-v-azure-tutorial.md) .

Bu, şirket içi VMware VM 'Leri için Azure 'da olağanüstü durum kurtarmanın nasıl ayarlanacağını gösteren bir serideki üçüncü öğreticidir. Önceki öğreticide, Azure 'a olağanüstü durum kurtarma için [Şirket Içi Hyper-V ortamını hazırladık](hyper-v-prepare-on-premises-tutorial.md) .

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Çoğaltma kaynağınızı ve hedefinizi seçme.
> * Şirket içi Site Recovery bileşenleri ve hedef çoğaltma ortamı dahil olmak üzere kaynak çoğaltma ortamını ayarlayın.
> * VMM VM ağları ile Azure sanal ağları arasında eşleme yapmak için ağ eşlemesi ayarlayın.
> * Bir çoğaltma ilkesi oluşturma.
> * Sanal makine için çoğaltmayı etkinleştirme.

> [!NOTE]
> Öğreticiler, bir senaryo için en basit dağıtım yolunu gösterir. Mümkün olduğunca varsayılan seçenekleri kullanır ve tüm olası ayarları ve yolları göstermez. Ayrıntılı yönergeler için [Site Recovery belgelerinin](https://docs.microsoft.com/azure/site-recovery) **nasıl yapılır kılavuzlarındaki** makaleleri gözden geçirin.



## <a name="before-you-begin"></a>Başlamadan önce

Bu, serideki üçüncü öğreticidir. Önceki öğreticilerdeki görevleri zaten tamamlamış olduğunu varsayar:

1. [Azure’u hazırlama](tutorial-prepare-azure.md)
2. [Şirket içi Hyper-V’leri hazırlama](tutorial-prepare-on-premises-hyper-v.md)

## <a name="select-a-replication-goal"></a>Çoğaltma hedefi seçme

1. Azure portal, **Kurtarma Hizmetleri kasaları** ' na gidin ve kasayı seçin. Önceki öğreticide **Contosovmkasası** kasasını hazırladık.
2. **Başlarken**' de **Site Recovery**' yi seçin ve ardından **altyapıyı hazırla**' yı seçin.
3. **Makinelerinizin nerede bulunduğu** **koruma hedefi** > , **Şirket içi**' ı seçin.
4. **Makinelerinizi nereye çoğaltmak istiyorsunuz?** bölümünde, **Azure 'a**seçin.
5. **Makineleriniz sanallaştırılmış mı?** , **Hyper-V ile Evet '** i seçin.
6. İçinde, **Hyper-V konaklarınızı yönetmek Için System Center VMM kullanıyorsunuz musunuz?** , **Evet**' i seçin.
7.  **Tamam**’ı seçin.

    ![Çoğaltma hedefi](./media/hyper-v-vmm-azure-tutorial/replication-goal.png)

## <a name="confirm-deployment-planning"></a>Dağıtım planlamasını onaylama

1. **Dağıtım planlaması**' nda, büyük bir dağıtım planlıyorsanız, Hyper-V ' y i dağıtım planlayıcısı sayfadaki bağlantıdan indirin. Hyper-V dağıtım planlaması hakkında [daha fazla bilgi edinin](hyper-v-deployment-planner-overview.md) .
2. Bu öğretici için Dağıtım Planlayıcısı gerekli değildir. **Dağıtım planlamasını tamamladınız mı?** , **daha sonra yapacağım**' i seçin ve ardından **Tamam**' ı seçin.

## <a name="set-up-the-source-environment"></a>Kaynak ortamı ayarlama

Kaynak ortamı ayarlarken, VMM sunucusuna Azure Site Recovery sağlayıcıyı yüklersiniz ve sunucuyu kasaya kaydedersiniz. Azure kurtarma hizmetleri Aracısı 'nı her Hyper-V konağına yüklersiniz.

1. **Altyapıyı hazırlama**bölümünde **kaynak**' ı seçin.
2. **Kaynağı hazırla**bölümünde, VMM sunucusu eklemek Için **+ VMM** ' yi seçin. **Sunucu Ekle** bölümünde **Sunucu türü** olarak **System Center VMM sunucusu** girişinin olduğundan emin olun.
3. Microsoft Azure Site Recovery sağlayıcısı için yükleyiciyi indirin.
4. Kasa kayıt anahtarını indirin. Sağlayıcı kurulumunu çalıştırdığınızda bu anahtar gerekir. Anahtar, oluşturulduktan sonra beş gün boyunca geçerlidir.
5. Microsoft Azure Kurtarma Hizmetleri Aracısı için yükleyiciyi indirin.

    ![Sağlayıcıyı, kayıt anahtarını ve aracıyı indir](./media/hyper-v-vmm-azure-tutorial/download-vmm.png)

### <a name="install-the-provider-on-the-vmm-server"></a>Sağlayıcıyı VMM sunucusuna yükleme

1. Azure Site Kurtarma Sağlayıcısı Kurulum sihirbazındaki **Microsoft Update** bölümünde, Sağlayıcı güncelleştirmelerini denetlemek için Microsoft Update’i kullanmayı kabul edin.
2. **Yükleme**bölümünde, sağlayıcının varsayılan yükleme konumunu kabul edin ve **Yükleme ' yi**seçin.
3. Yükleme sonrasında, Microsoft Azure Site Recovery Kayıt Sihirbazı > **kasa ayarları**' nda, **Araştır**' ı seçin ve **anahtar dosyası**' nda indirdiğiniz kasa anahtarı dosyasını seçin.
4. Azure Site Recovery aboneliğini ve kasa adını (**Contosovmkasa**) belirtin. VMM sunucusu için kasada tanımlamak üzere bir kolay ad belirtin.
5. **Proxy Ayarları** bölümünde **Proxy sunucusu olmadan doğrudan Azure Site Recovery hizmetine bağlan** seçeneğini belirleyin.
6. Verileri şifrelemek için kullanılan sertifika için varsayılan konumu kabul edin. Yük devretmek için şifrelenen verilerin şifresi çözülür.
7. **Bulut meta verilerini Eşitle**bölümünde **bulut meta verilerini Site Recovery Portal**' a Eşitle ' yi seçin. Bu eylemin her bir sunucuda yalnızca bir kez gerçekleşmesi gerekir. Sonra **Kaydet**' i seçin.
8. Sunucu kasada kaydedildikten sonra **son**' u seçin.

Kayıt tamamlandıktan sonra, sunucudan gelen meta veriler Azure Site Recovery tarafından alınır ve VMM sunucusu **Site Recovery altyapısında**görüntülenir.

### <a name="install-the-recovery-services-agent-on-hyper-v-hosts"></a>Kurtarma Hizmetleri aracısını Hyper-V konaklarına yükler

Aracıyı, çoğaltmak istediğiniz VM 'Leri içeren her Hyper-V konağına yükleyebilirsiniz.

1. Microsoft Azure Kurtarma Hizmetleri Aracısı Kurulum Sihirbazı > **Önkoşul denetimi**' nde, **İleri**' yi seçin. Eksik Önkoşullar otomatik olarak yüklenir.
2. **Yükleme ayarları**' nda, yükleme konumunu ve önbellek konumunu kabul edin. Önbellek sürücüsü en az 5 GB depolama alanı gerektirir. 600 GB veya daha fazla boş alan içeren bir sürücü önerilir. Ardından **Yükle**’yi seçin.
3. **Yükleme**tamamlandığında, yükleme tamamlandığında, Sihirbazı **kapatmak için Kapat** ' ı seçin.

    ![Aracıyı yükler](./media/hyper-v-vmm-azure-tutorial/mars-install.png)

## <a name="set-up-the-target-environment"></a>Hedef ortamı ayarlama

1. **Altyapıyı hazırla** > **Hedef** seçeneğini belirleyin.
2. Yük devretmeden sonra Azure VM 'lerinin oluşturulacağı aboneliği ve kaynak grubunu (**ContosoRG**) seçin.
3. **Kaynak Yöneticisi** dağıtım modelini seçin.

Site Recovery, bir veya birden çok uyumlu Azure depolama hesabınızın ve ağınızın olup olmadığını denetler.

## <a name="configure-network-mapping"></a>Ağ eşlemesini yapılandırma

1. Ağ **eşleştirmeleri** > ağ **eşlemesi** > **altyapı Site Recovery** , **+ ağ eşleme** simgesini seçin.
2. **Ağ eşlemesi Ekle**' de, kaynak VMM sunucusunu seçin. Hedef olarak **Azure** ' u seçin.
3. Yük devretme işleminden sonra dağıtım modelini ve aboneliği seçin.
4. **Kaynak ağ**bölümünde, kaynak ŞIRKET içi VM ağını seçin.
5. **Hedef ağ**' da, yük devretmeden sonra oluşturulan çoğaltma Azure VM 'Lerinin bulunduğu Azure ağını seçin. Sonra **Tamam**’ı seçin.

    ![Ağ eşlemesi](./media/hyper-v-vmm-azure-tutorial/network-mapping-vmm.png)

## <a name="set-up-a-replication-policy"></a>Çoğaltma ilkesi ayarlama

1. **Altyapıyı hazırla** > **çoğaltma ayarları** >  **+ Oluştur ve ilişkilendir '** i seçin.
2. **İlke oluştur ve ilişkilendir** bölümünde bir ilke adı belirtin. **Contosoreplicationpolicy**kullanıyorsunuz.
3. Varsayılan ayarları bırakın ve **Tamam**' ı seçin.
    - **Kopyalama sıklığı** , ilk çoğaltmadan sonra Delta verilerinin her beş dakikada bir çoğaltılacağını belirtir.
    - **Kurtarma noktası bekletme** , her kurtarma noktasının iki saat boyunca bekletileceği anlamına gelir.
    - **Uygulamayla tutarlı anlık görüntü sıklığı**, uygulamayla tutarlı anlık görüntüleri içeren kurtarma noktalarının her saat oluşturulacağını belirtir.
    - **İlk çoğaltma başlangıç zamanı** , ilk çoğaltmanın hemen başlatılacağını gösterir.
    - **Azure 'da depolanan verileri şifreleme** varsayılan (**kapalı**) olarak ayarlanır ve Azure 'daki bekleyen verilerin şifrelenmediğini belirtir.
4. İlke oluşturulduktan sonra **Tamam**' ı seçin. Yeni bir ilke oluşturduğunuzda, VMM bulutu ile otomatik olarak ilişkilendirilir.

## <a name="enable-replication"></a>Çoğaltmayı etkinleştirme

1. **Uygulamayı Çoğalt**bölümünde **kaynak**' ı seçin.
2. **Kaynak**bölümünde VMM bulutu ' nı seçin. Sonra **Tamam**’ı seçin.
3. **Hedef**bölümünde, hedef (Azure), kasa aboneliğini doğrulayın ve **Kaynak Yöneticisi** modeli seçin.
4. **Contosovmsacct1910171607** Storage hesabını ve **contosoasrnet** Azure ağını seçin.
5. **Sanal makineler** > **seçin**' de, çoğaltmak istediğiniz VM 'yi seçin. Sonra **Tamam**’ı seçin.

   **İşler**Site Recovery işleri >  bölümünde **Korumayı Etkinleştir** eyleminin ilerleme durumunu izleyebilirsiniz. **Korumayı Sonlandır** işi tamamlandıktan sonra, ilk çoğaltma TAMAMLANıR ve VM yük devretme için hazırlayın.

## <a name="next-steps"></a>Sonraki adımlar
> [!div class="nextstepaction"]
> [Olağanüstü durum kurtarma tatbikatı çalıştırma](tutorial-dr-drill-azure.md)
