---
title: Azure Site Recovery kullanarak Hyper-V olağanüstü durum kurtarmayı ayarlama
description: Site Recovery kullanarak şirket içi Hyper-V VM 'lerinin (VMM olmadan) Azure 'a olağanüstü durum kurtarmayı ayarlamayı öğrenin.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/12/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 741d4718b5e6140f4ddd2bb22e1a2ec830763176
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74029941"
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-to-azure"></a>Şirket içi Hyper-V sanal makineleri için Azure’da olağanüstü durum kurtarma ayarlama

[Azure Site Recovery](site-recovery-overview.md) hizmeti, şirket içi makinelerin ve Azure sanal makinelerinin (VM) çoğaltma, yük devretme ve yeniden çalışma işlemlerini yönetip düzenleyerek olağanüstü durum kurtarma stratejinize katkıda bulunur.

Bu, serideki üçüncü öğreticidir. Şirket içi Hyper-V VM 'lerinin Azure 'a olağanüstü durum kurtarmayı nasıl ayarlayagösterdiğini gösterir. Bu öğretici, Microsoft System Center Virtual Machine Manager (VMM) tarafından yönetilmeyen Hyper-V VM 'lerini uygular.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Çoğaltma kaynağınızı ve hedefinizi seçme.
> * Şirket içi Site Recovery bileşenleri ve hedef çoğaltma ortamı dahil olmak üzere kaynak çoğaltma ortamını ayarlayın.
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
6. İçinde, **Hyper-V konaklarınızı yönetmek Için System Center VMM kullanıyorsunuz musunuz?** , **Hayır**' ı seçin.
7. **Tamam**’ı seçin.

    ![Çoğaltma hedefi](./media/hyper-v-azure-tutorial/replication-goal.png)

## <a name="confirm-deployment-planning"></a>Dağıtım planlamasını onaylama

1. **Dağıtım planlaması**' nda, büyük bir dağıtım planlıyorsanız, Hyper-V ' y i dağıtım planlayıcısı sayfadaki bağlantıdan indirin. Hyper-V dağıtım planlaması hakkında [daha fazla bilgi edinin](hyper-v-deployment-planner-overview.md) .
2. Bu öğretici için Dağıtım Planlayıcısı gerekli değildir. **Dağıtım planlamasını tamamladınız mı?** , **daha sonra yapacağım**' i seçin ve ardından **Tamam**' ı seçin.

    ![Dağıtım planlaması](./media/hyper-v-azure-tutorial/deployment-planning.png)

## <a name="set-up-the-source-environment"></a>Kaynak ortamı ayarlama

Kaynak ortamı ayarlamak için bir Hyper-V sitesi oluşturup bu siteye, çoğaltmak istediğiniz VM 'Leri içeren Hyper-V konaklarına eklersiniz. Ardından, Azure Site Recovery sağlayıcısı ve Azure kurtarma hizmetleri aracısını her bir konağa indirip yüklersiniz ve Hyper-V sitesini kasaya kaydedersiniz.

1. **Altyapıyı hazırla**altında **kaynak**' ı seçin.
2. **Kaynağı hazırla**bölümünde **+ Hyper-V sitesi**' ni seçin.
3. **Hyper-V sitesi oluştur**' da, site adını belirtin. **Contosohypervsite**kullanıyorsunuz.

    ![Hyper-V sitesi](./media/hyper-v-azure-tutorial/hyperv-site.png)

4. Site oluşturulduktan sonra **kaynak hazırla** > **1. Adım: Hyper-V sitesini seçin**, oluşturduğunuz siteyi seçin.
5. **+ Hyper-V sunucusu**' nu seçin.

    ![Hyper-V sunucusu](./media/hyper-v-azure-tutorial/hyperv-server.png)

6. Microsoft Azure Site Recovery sağlayıcısı için yükleyiciyi indirin.
7. Kasa kayıt anahtarını indirin. Sağlayıcıyı yüklemek için bu anahtara ihtiyacınız vardır. Anahtar, oluşturulduktan sonra beş gün boyunca geçerlidir.

    ![Sağlayıcıyı ve kayıt anahtarını indirin](./media/hyper-v-azure-tutorial/download.png)
    

### <a name="install-the-provider"></a>Sağlayıcıyı yükleyin

Hyper-V sitesine eklemek istediğiniz her Hyper-V konağına indirilen kurulum dosyasını (AzureSiteRecoveryProvider. exe) yükleme. Kurulum, Azure Site Recovery sağlayıcısı ve kurtarma hizmetleri aracısını her Hyper-V konağına yüklenir.

1. Kurulum dosyasını çalıştırın.
2. Azure Site Kurtarma Sağlayıcısı Kurulum sihirbazındaki **Microsoft Update** bölümünde, Sağlayıcı güncelleştirmelerini denetlemek için Microsoft Update’i kullanmayı kabul edin.
3. **Yükleme**bölümünde, sağlayıcı ve aracı için varsayılan yükleme konumunu kabul edin ve **yükleme**' yi seçin.
4. Yükleme sonrasında, Microsoft Azure Site Recovery Kayıt Sihirbazı > **kasa ayarları**' nda, **Araştır**' ı seçin ve **anahtar dosyası**' nda indirdiğiniz kasa anahtarı dosyasını seçin.
5. Azure Site Recovery aboneliğini, kasa adını (**ContosoVMVault**) ve Hyper-V sunucusunun ait olduğu Hyper-V sitesini (**ContosoHyperVSite**) belirtin.
6. **Proxy Ayarları** bölümünde **Proxy sunucusu olmadan doğrudan Azure Site Recovery hizmetine bağlan** seçeneğini belirleyin.
7. **Kayıt**' de, sunucu kasada kaydedildikten sonra **son**' u seçin.

Hyper-V sunucusundaki meta veriler, Azure Site Recovery tarafından alınır ve **Site Recovery Altyapısı** > **Hyper-V Konakları** bölümünde sunucu görüntülenir. Bu işlemin tamamlanması 30 dakika sürebilir.

#### <a name="install-the-provider-on-a-hyper-v-core-server"></a>Sağlayıcıyı Hyper-V çekirdek sunucusuna yükler

Hyper-V çekirdek sunucusu çalıştırıyorsanız, kurulum dosyasını indirin ve şu adımları izleyin:

1. Şu komutu çalıştırarak dosyaları AzureSiteRecoveryProvider. exe ' den yerel bir dizine ayıklayın:

    `AzureSiteRecoveryProvider.exe /x:. /q`
 
2. `.\setupdr.exe /i` öğesini çalıştırın. Sonuçlar%ProgramData%\asrlogs\drasetupwizard.log günlüğüne kaydedilir.

3. Şu komutu çalıştırarak sunucuyu kaydedin:

    ```
    cd  "C:\Program Files\Microsoft Azure Site Recovery Provider\DRConfigurator.exe" /r /Friendlyname "FriendlyName of the Server" /Credentials "path to where the credential file is saved"
    ```

## <a name="set-up-the-target-environment"></a>Hedef ortamı ayarlama

Hedef kaynakları seçin ve doğrulayın:

1. **Altyapıyı hazırla** > **Hedef** seçeneğini belirleyin.
2. Yük devretmeden sonra Azure VM 'lerinin oluşturulacağı aboneliği ve **ContosoRG** kaynak grubunu seçin.
3. **Kaynak Yöneticisi** dağıtım modelini seçin.

Site Recovery, bir veya birden çok uyumlu Azure depolama hesabınızın ve ağınızın olup olmadığını denetler.

## <a name="set-up-a-replication-policy"></a>Çoğaltma ilkesi ayarlama

1. **Altyapıyı hazırla** > **çoğaltma ayarları** >  **+ Oluştur ve ilişkilendir '** i seçin.
2. **İlke oluştur ve ilişkilendir** bölümünde bir ilke adı belirtin. **Contosoreplicationpolicy**kullanıyorsunuz.
3. Bu öğretici için varsayılan ayarları bırakacağız:
    - **Kopyalama sıklığı** , Delta verilerinin (ilk çoğaltmadan sonra) ne sıklıkta çoğaltılacağını gösterir. Varsayılan sıklık her beş dakikadır.
    - **Kurtarma noktası bekletme** , kurtarma noktalarının iki saat boyunca bekletileceği anlamına gelir.
    - **Uygulamayla tutarlı anlık görüntü sıklığı**, uygulamayla tutarlı anlık görüntüleri içeren kurtarma noktalarının her saat oluşturulacağını belirtir.
    - **İlk çoğaltma başlangıç zamanı** , ilk çoğaltmanın hemen başlatılacağını gösterir.
4. İlke oluşturulduktan sonra **Tamam**' ı seçin. Yeni bir ilke oluşturduğunuzda, belirtilen Hyper-V sitesiyle otomatik olarak ilişkilendirilir. Öğreticimizde **Contosohypervsite**.

    ![Çoğaltma ilkesi](./media/hyper-v-azure-tutorial/replication-policy.png)

## <a name="enable-replication"></a>Çoğaltmayı etkinleştirme

1. **Uygulamayı Çoğalt**bölümünde **kaynak**' ı seçin.
2. **Kaynak** bölümünde **ContosoHyperVSite** sitesini seçin. Sonra **Tamam**’ı seçin.
3. **Hedef**bölümünde, hedefi (Azure), kasa aboneliğini ve **Kaynak Yöneticisi** dağıtım modelini doğrulayın.
4. Öğretici ayarları kullanıyorsanız, çoğaltılan veriler için önceki öğreticide oluşturulan **contosovmsacct1910171607** Storage hesabını seçin. Ayrıca, yük devretmeden sonra Azure VM 'lerinin bulunduğu **Contosoasrnet** ağını seçin.
5. **Sanal makineler** > **seçin**' de, çoğaltmak istediğiniz VM 'yi seçin. Sonra **Tamam**’ı seçin.

   **İşler**Site Recovery işleri >  bölümünde **Korumayı Etkinleştir** eyleminin ilerleme durumunu izleyebilirsiniz. **Korumayı Sonlandır** işi tamamlandıktan sonra, ilk çoğaltma TAMAMLANıR ve VM yük devretme için hazırlayın.

## <a name="next-steps"></a>Sonraki adımlar
> [!div class="nextstepaction"]
> [Olağanüstü durum kurtarma tatbikatı çalıştırma](tutorial-dr-drill-azure.md)
