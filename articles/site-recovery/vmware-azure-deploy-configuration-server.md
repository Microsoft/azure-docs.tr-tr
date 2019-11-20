---
title: Yapılandırma sunucusunu Azure Site Recovery dağıtma
description: Bu makalede, Azure Site Recovery ile VMware olağanüstü durum kurtarma için bir yapılandırma sunucusunun nasıl dağıtılacağı açıklanır.
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 10/15/2019
ms.author: ramamill
ms.openlocfilehash: f2e9387af3c5922ec5eb0dded3d0d1d4bcee6a01
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74084137"
---
# <a name="deploy-a-configuration-server"></a>Yapılandırma sunucusunu dağıtma

VMware VM 'Leri ve fiziksel sunucuları Azure 'a olağanüstü durum kurtarması için [Azure Site Recovery](site-recovery-overview.md) kullandığınızda şirket içi yapılandırma sunucusu dağıtabilirsiniz. Yapılandırma sunucusu, şirket içi VMware ve Azure arasındaki iletişimleri koordine eder. Ayrıca veri çoğaltmasını yönetir. Bu makalede, VMware VM 'lerini Azure 'a çoğaltırken yapılandırma sunucusunu dağıtmak için gereken adımlarda adım adım gösterilmektedir. Fiziksel sunucu çoğaltması için bir yapılandırma sunucusu ayarlamanız gerekiyorsa bkz. [fiziksel sunucuların olağanüstü durum kurtarması için yapılandırma sunucusunu Azure 'A ayarlama](physical-azure-set-up-source.md).

> [!TIP]
> Azure Site Recovery mimarisinin bir parçası olarak bir yapılandırma sunucusunun rolü hakkında bilgi edinmek için bkz. [VMware 'Den Azure 'a olağanüstü durum kurtarma mimarisi](vmware-azure-architecture.md).

## <a name="deploy-a-configuration-server-through-an-ova-template"></a>Bir OVA şablonu aracılığıyla bir yapılandırma sunucusu dağıtma

Yapılandırma sunucusu, belirli minimum donanım ve boyutlandırma gereksinimlerine sahip yüksek oranda kullanılabilir bir VMware VM 'si olarak ayarlanmalıdır. Kullanışlı ve kolay dağıtım için Site Recovery, burada listelenen tüm uygulanan gereksinimleriyle uyumlu yapılandırma sunucusunu ayarlamak için indirilebilir bir açık sanallaştırma uygulaması (OVA) şablonu sağlar.

## <a name="prerequisites"></a>Ön koşullar

Bir yapılandırma sunucusu için en düşük donanım gereksinimleri aşağıdaki bölümlerde özetlenmektedir.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

## <a name="azure-active-directory-permission-requirements"></a>Azure Active Directory izin gereksinimleri

Yapılandırma sunucusunu Azure Site Recovery Hizmetleri ile kaydetmek için Azure Active Directory (Azure AD) ' de ayarlanmış bir kullanıcıya sahip olmanız gerekir.

1. Kullanıcının bir uygulama oluşturmak için bir uygulama geliştirici rolüne sahip olması gerekir.
    - Doğrulamak için Azure portal oturum açın.</br>
    - **Azure Active Directory** > **Roller ve yöneticiler '** e gidin.</br>
    - Uygulama geliştirici rolünün kullanıcıya atandığını doğrulayın. Aksi takdirde, bu izinle bir Kullanıcı kullanın veya [izni etkinleştirmek için bir yöneticiye](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal#assign-roles)başvurun.
    
2. Uygulama geliştirici rolü atanmadığından, kullanıcıların bir kimlik oluşturması için **kullanıcıların uygulamaları kaydedebilmesi** için, **doğru** olarak ayarlanmış olduğundan emin olun. Bu izinleri etkinleştirmek için:
    - Azure Portal’da oturum açın.
    - **Azure Active Directory** > **Kullanıcı ayarları**' na gidin.
    - **Uygulama kayıtları**altında **kullanıcılar uygulamaları kaydedebilir**, **Evet**' i seçin.

      ![Azure AD_application_permission](media/vmware-azure-deploy-configuration-server/AAD_application_permission.png)

> [!NOTE]
> Active Directory Federasyon Hizmetleri (AD FS) *desteklenmez*. [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)ile yönetilen bir hesap kullanın.

## <a name="download-the-template"></a>Şablonu indirme

1. Kasada **Altyapıyı Hazırlama** > **Kaynak** seçeneğine gidin.
2. **Kaynağı hazırla** bölümünde **+Yapılandırma sunucusu**’nu seçin.
3. **Sunucu Ekle** bölümünde **Sunucu türü**’nde **VMware için yapılandırma sunucusu**’nun görüntülenip görüntülenmediğini kontrol edin.
4. Yapılandırma sunucusu için OVA şablonunu indirin.

   > [!TIP]
   >Yapılandırma sunucusu şablonunun en son sürümünü doğrudan [Microsoft Yükleme Merkezi](https://aka.ms/asrconfigurationserver)' nden de indirebilirsiniz.

> [!NOTE]
> Bir OVA şablonuyla sunulan lisans, 180 gün boyunca geçerli olan bir değerlendirme lisanslayadır. Bu dönemden sonra, bir lisans oluşturmanız gerekir.

## <a name="import-the-template-in-vmware"></a>VMware’de şablonu içeri aktarma

1. VMWare vSphere İstemcisi’ni kullanarak VMware vCenter sunucusunda veya vSphere ESXi konağında oturum açın.
2. **Dosya** menüsünde **ovf** şablonu DAĞıT ' ı seçerek **ovf şablonu dağıtma** Sihirbazı ' nı başlatın.

     ![OVF şablonu dağıtma](./media/vmware-azure-deploy-configuration-server/vcenter-wizard.png)

3. **Kaynak seçin** bölümünde, indirilen OVF’nin konumunu girin.
4. **Değerlendirme ayrıntıları** bölümünde **İleri** seçeneğini belirleyin.
5. **Ad ve klasör seçin** ve **Yapılandırma seçin** bölümünde varsayılan ayarları kabul edin.
6. **Select storage** bölümünde, en iyi performans için **Select virtual disk format** bölümünden **Thick Provision Eager Zeroed** seçeneğini belirleyin. Ölçülü kaynak sağlama seçeneğinin kullanılması yapılandırma sunucusunun performansını etkileyebilir.
7. Sihirbaz sayfalarının geri kalan kısmında varsayılan ayarları kabul edin.
8. **Tamamlanmaya hazır** olduğunda:

    * VM’yi varsayılan ayarlarla kurmak için **Dağıtımdan sonra aç** > **Son** seçeneğini belirleyin.
    * Ek bir ağ arabirimi eklemek için **dağıtımdan sonra Aç**' ı temizleyin ve ardından **son**' u seçin. Varsayılan olarak, yapılandırma sunucusu şablonu tek bir NIC ile dağıtılır. Dağıtımdan sonra daha fazla NIC ekleyebilirsiniz.

> [!IMPORTANT]
> Bellek, çekirdek ve CPU kısıtlaması gibi kaynak yapılandırmalarını değiştirmeyin veya dağıtımdan sonra yapılandırma sunucusundaki yüklü Hizmetleri veya dosyaları değiştirin veya silin. Bu tür değişiklikler, yapılandırma sunucusunun Azure hizmetleriyle kaydını ve yapılandırma sunucusunun performansını etkiler.

## <a name="add-an-additional-adapter"></a>Ek bağdaştırıcı ekleme

> [!NOTE]
> Yük devretmede kaynak makinelerin IP adreslerini tutmayı planlıyorsanız ve daha sonra yeniden şirket içinde yük devretmek istiyorsanız iki NIC gereklidir. Bir NIC, kaynak makinelere bağlanır ve diğer NIC Azure bağlantısı için kullanılır.

Yapılandırma sunucusuna ek bir NIC eklemek istiyorsanız, sunucuyu kasaya kaydetmeden önce ekleyin. Kayıt işleminden sonra ek sunucu eklenemez.

1. vSphere Client envanterinde VM’ye sağ tıklayın ve **Ayarları Düzenle**’yi seçin.
2. **Donanım** bölümünde **Ekle** > **Ethernet Bağdaştırıcısı** seçeneğini belirleyin. Sonra **İleri**’yi seçin.
3. Bir bağdaştırıcı türü ve ağ seçin.
4. VM açıldığında sanal NIC 'yi bağlamak için, **oturum açma sırasında bağlan**' ı seçin. Ardından **ileri** > **Tamam** > **son** ' u seçin.

## <a name="register-the-configuration-server-with-azure-site-recovery-services"></a>Yapılandırma sunucusunu Azure Site Recovery hizmetleriyle kaydetme

1. VMWare vSphere Client konsolundan VM’yi açın.
2. VM’de Windows Server 2016 yükleme deneyimi önyüklemesi yapılır. Lisans sözleşmesini kabul edin ve bir yönetici parolası girin.
3. Yükleme tamamlandıktan sonra VM’de yönetici olarak oturum açın.
4. İlk kez oturum açtığınızda, birkaç saniye içinde Azure Site Recovery yapılandırma aracı başlatılır.
5. Yapılandırma sunucusunu Site Recovery’ye kaydetmek için kullanılacak bir ad girin. Sonra **İleri**’yi seçin.
6. Araç, VM’nin Azure bağlanıp bağlanamadığını denetler. Bağlantı kurulduktan sonra Azure aboneliğinizde oturum açmak için **Oturum aç** seçeneğini belirleyin.</br>
    a. Kimlik bilgilerinin, yapılandırma sunucusunu kaydetmek istediğiniz kasaya erişim izni olmalıdır.</br>
    b. Seçilen Kullanıcı hesabının Azure 'da uygulama oluşturma izni olduğundan emin olun. Gerekli izinleri etkinleştirmek için, [izin gereksinimleri Azure Active Directory](#azure-active-directory-permission-requirements)bölümündeki yönergeleri izleyin.
7. Araç birkaç yapılandırma görevi gerçekleştirir ve yeniden başlatır.
8. Makinede tekrar oturum açın. Yapılandırma sunucusu Yönetim Sihirbazı birkaç saniye içinde otomatik olarak başlar.

### <a name="configure-settings"></a>Ayarları yapılandırma

1. Yapılandırma sunucusu Yönetim Sihirbazı 'nda, **Kurulum bağlantısı**' nı seçin. Açılan kutudan önce, yerleşik işlem sunucusunun kaynak makinelere Mobility hizmeti bulma ve anında yükleme için kullandığı NIC 'i seçin. Ardından yapılandırma sunucusunun Azure ile bağlantı için kullandığı NIC 'ı seçin. **Kaydet**’i seçin. Bu ayar yapılandırıldıktan sonra değiştirilemez. Yapılandırma sunucusunun IP adresini değiştirmeyin. Yapılandırma sunucusuna atanan IP 'nin bir DHCP IP 'si değil statik IP olduğundan emin olun.
2. **Kurtarma Hizmetleri kasasını Seç**sayfasında, [yapılandırma sunucusunu Azure Site Recovery hizmetleriyle kaydetme](#register-the-configuration-server-with-azure-site-recovery-services)adım 6 ' da kullanılan kimlik bilgileriyle Microsoft Azure için oturum açın.
3. Oturum açtıktan sonra Azure aboneliğinizi ve ilgili kaynak grubunu ve kasasını seçin.

    > [!NOTE]
    > Kayıttan sonra, kurtarma hizmetleri kasasını değiştirme esnekliği yoktur.
    > Bir kurtarma hizmetleri kasasının değiştirilmesi, geçerli kasadan yapılandırma sunucusu ilişkilendirmesini gerektirir ve yapılandırma sunucusu altındaki tüm korumalı sanal makinelerin çoğaltılması durdurulur. Daha fazla bilgi edinmek için bkz. [VMware VM olağanüstü durum kurtarma için yapılandırma sunucusunu yönetme](vmware-azure-manage-configuration-server.md#register-a-configuration-server-with-a-different-vault).

4. **Üçüncü taraf yazılım yüklerken**:

    |Senaryo   |İzlenecek adımlar  |
    |---------|---------|
    |MySQL 'i el ile indirebilir ve yükleyebilir miyim?     |  Evet. MySQL uygulamasını indirin, **C:\temp\asrsetup**klasörüne yerleştirip el ile yükleyin. Koşulları kabul ettikten ve **indir ve yükle**' yi seçtikten sonra portal *zaten yüklüdür*. Sonraki adıma geçebilirsiniz.       |
    |MySQL 'i çevrimiçi indirmeyi önleyebilir miyim?     |   Evet. MySQL yükleyici uygulamanızı **C:\temp\asrsetup**klasörüne yerleştirin. Koşulları kabul edin, **indir ve yükle**' yi seçin ve Portal, uygulamayı yüklemek için eklediğiniz yükleyiciyi kullanır. Yükleme bittikten sonra, sonraki adıma geçin.    |
    |Azure Site Recovery aracılığıyla MySQL indirmek ve yüklemek istiyorum.    |  Lisans sözleşmesini kabul edin ve **indir ve yükle**' yi seçin. Yükleme bittikten sonra, sonraki adıma geçin.       |

5. **Gereç yapılandırmasını Doğrula**sayfasında, devam etmeden önce Önkoşullar doğrulanır.
6. **VCenter Server/vSphere ESXi sunucusunu yapılandır**sayfasında, çoğaltma yapmak Istediğiniz VM 'Lerin bulunduğu vCenter sunucusunun veya vSphere konağının FQDN 'SINI veya IP adresini girin. Sunucunun dinleme gerçekleştirdiği bağlantı noktasını girin. Kasadaki VMware sunucusu için kullanılacak bir kolay ad girin.
7. VMware sunucusu ile bağlantı için yapılandırma sunucusu tarafından kullanılacak kimlik bilgilerini girin. Site Recovery, bu kimlik bilgilerini çoğaltma için kullanılabilen VMware VM’lerini otomatik olarak bulmak üzere kullanır. **Ekle** > **devam et**' i seçin. Buraya girilen kimlik bilgileri yerel olarak kaydedilir.
8. **Sanal makine kimlik bilgilerini Yapılandır**sayfasında, çoğaltma sırasında Mobility hizmetini otomatik olarak yüklemek için sanal makinelerin Kullanıcı adını ve parolasını girin. **Windows** makineleri için hesabın, çoğaltmak istediğiniz makinelerde yerel yönetici ayrıcalıklarına ihtiyacı vardır. **Linux**için kök hesabının ayrıntılarını sağlayın.
9. Kaydı tamamlamak için **Yapılandırmayı son haline getir** seçeneğini belirleyin.
10. Kayıt tamamlandıktan sonra, Azure portal açın ve Configuration Server ve VMware sunucusunun **Kurtarma Hizmetleri kasasında** listelendiğini doğrulayın >  > **Site Recovery altyapıyı** **yönetin** >  **Yapılandırma sunucuları**.

## <a name="upgrade-the-configuration-server"></a>Yapılandırma sunucusunu yükseltme

Yapılandırma sunucusunu en son sürüme yükseltmek için bkz. [VMware VM olağanüstü durum kurtarması için yapılandırma sunucusunu yönetme](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server). Tüm Site Recovery bileşenlerinin nasıl yükseltileceğiyle ilgili yönergeler için, bkz. [Site Recovery hizmet güncelleştirmeleri](service-updates-how-to.md).

## <a name="manage-the-configuration-server"></a>Yapılandırma sunucusunu yönetme

Devam eden çoğaltmanın kesintiye uğramasını önlemek için yapılandırma sunucusu bir kasaya kaydedildikten sonra yapılandırma sunucusunun IP adresinin değişmediğinden emin olun. Ortak yapılandırma sunucusu yönetim görevleri hakkında daha fazla bilgi edinmek için bkz. [VMware VM olağanüstü durum kurtarma için yapılandırma sunucusunu yönetme](vmware-azure-manage-configuration-server.md).

## <a name="faqs"></a>SSS

* OVF aracılığıyla dağıtılan bir yapılandırma sunucusunda Lisans ne kadar süreyle geçerlidir? Lisansı yeniden etkinleştirediğimde ne olur?

    Bir OVA şablonuyla sunulan lisans, 180 gün için geçerli bir değerlendirme lisanslayadır. Süresi dolmadan önce lisansı etkinleştirmeniz gerekir. Aksi takdirde, yapılandırma sunucusunun sıklıkla kapatılmasını sağlayabilir ve hindrance çoğaltma etkinliklerine neden olur. Daha fazla bilgi için bkz. [VMware VM olağanüstü durum kurtarma için yapılandırma sunucusunu yönetme](vmware-azure-manage-configuration-server.md#update-windows-license).

* Yapılandırma sunucusunun farklı amaçlar için yüklendiği VM 'yi kullanabilir miyim?

    Hayır. Yapılandırma sunucusunun tek amacı için VM 'yi kullanın. Olağanüstü durum kurtarma 'nın verimli yönetimi için [Önkoşullar](#prerequisites) bölümünde bahsedilen tüm belirtimleri izlediğinizden emin olun.
* Yapılandırma sunucusunda zaten kayıtlı olan kasayı yeni oluşturulan bir kasaya değiştirebilir miyim?

    Hayır. Bir kasa yapılandırma sunucusuna kaydedildikten sonra değiştirilemez.
* Aynı yapılandırma sunucusunu hem fiziksel hem de sanal makineleri korumak için kullanabilir miyim?

    Evet. Aynı yapılandırma sunucusu fiziksel ve sanal makineleri çoğaltmak için kullanılabilir. Ancak, fiziksel makine yalnızca bir VMware VM 'sine geri alınabilir.
* Yapılandırma sunucusunun amacı nedir ve nerede kullanılır?

    Yapılandırma sunucusu ve işlevleri hakkında daha fazla bilgi edinmek için bkz. [VMware 'Den Azure 'a çoğaltma mimarisi](vmware-azure-architecture.md).
* Yapılandırma sunucusunun en son sürümünü nereden bulabilirim?

    Yapılandırma sunucusunu Portal üzerinden yükseltme adımları için bkz. [yapılandırma sunucusunu yükseltme](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server). Tüm Site Recovery bileşenlerinin nasıl yükseltileceğiyle ilgili yönergeler için, bkz. [Site Recovery hizmet güncelleştirmeleri](https://aka.ms/asr_how_to_upgrade).
* Yapılandırma sunucusu için parolayı nereden indirebilirim?

    Parolayı indirmek için bkz. [VMware VM olağanüstü durum kurtarması için yapılandırma sunucusunu yönetme](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase).
* Parolayı değiştirebilir miyim?

    Hayır. Yapılandırma sunucusunun parolasını değiştirmeyin. Parola değişikliği, korunan makinelerin çoğaltılmasını keser ve kritik bir sistem durumuna yol açar.
* Kasa kayıt anahtarlarını nereden indirebilirim?

    **Kurtarma Hizmetleri Kasası**'nda **Yönetim** > **Site Recovery altyapı** > **yapılandırma sunucuları**' nı seçin. **Sunucular**' da, kasa kimlik bilgileri dosyasını indirmek için **kayıt anahtarını indir** ' i seçin.
* Var olan bir yapılandırma sunucusunu kopyalayabilir ve çoğaltma düzenlemesi için kullanabilir miyim?

    Hayır. Kopyalanmış bir yapılandırma sunucusu bileşeninin kullanılması desteklenmez. Genişleme işlem sunucusu 'nun kopyalanması de desteklenmeyen bir senaryodur. Site Recovery bileşenlerin kopyalanması devam eden çoğaltmaları etkiler.

* Yapılandırma sunucusunun IP 'sini değiştirebilir miyim?

    Hayır. Yapılandırma sunucusunun IP adresini değiştirmeyin. Yapılandırma sunucusuna atanan tüm IP 'lerin DHCP IP 'Leri için statik IP 'Ler olduğundan emin olun.
* Azure 'da bir yapılandırma sunucusu ayarlayabilir miyim?

    Bir şirket içi ortamda, v-Center ile doğrudan görüş ve veri aktarımı gecikme sürelerini en aza indirmek için bir yapılandırma sunucusu ayarlayın. Yapılandırma sunucusunun zamanlanan yedeklemelerini yeniden [çalışma amacıyla](vmware-azure-manage-configuration-server.md#failback-requirements)gerçekleştirebilirsiniz.

Yapılandırma sunucuları hakkında daha fazla bilgi için bkz. [yapılandırma sunucusu sık sorulan sorular](vmware-azure-common-questions.md#configuration-server).

## <a name="troubleshoot-deployment-issues"></a>Dağıtım sorunlarını giderme

[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]



## <a name="next-steps"></a>Sonraki adımlar

[VMware VM](vmware-azure-tutorial.md) 'lerinin olağanüstü durum kurtarma 'yi Azure 'a ayarlayın.
