---
title: "VMM bulutlarındaki Hyper-V sanal makinelerini Azure&quot;a çoğaltma | Microsoft Belgeleri"
description: "Bu makalede, System Center VMM bulutlarında bulunan Hyper-V ana bilgisayarlarındaki Hyper-V sanal makinelerini Azure&quot;a çoğaltma işlemi açıklanır."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: 9d526a1f-0d8e-46ec-83eb-7ea762271db5
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 02/06/2017
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: ed97f2aeb19566b12342e5194ac8a01293f453bf
ms.lasthandoff: 04/03/2017


---
# <a name="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-azure"></a>VMM bulutlarındaki Hyper-V sanal makinelerini Azure'a çoğaltma
> [!div class="op_single_selector"]
> * [Azure portalı](site-recovery-vmm-to-azure.md)
> * [PowerShell - Resource Manager](site-recovery-vmm-to-azure-powershell-resource-manager.md)
> * [Klasik portal](site-recovery-vmm-to-azure-classic.md)
> * [PowerShell - Klasik](site-recovery-deploy-with-powershell.md)
>
>

Azure Site Recovery hizmeti; çoğaltma, yük devretme ve sanal makineler ile fiziksel sunucuları kurtarma işlemlerini düzenleyerek iş sürekliliği ve olağanüstü durum kurtarma (BCDR) stratejinize katkı sağlar. Makineler, Azure'a veya bir ikincil şirket içi veri merkezine çoğaltılabilir. Hızlı bir genel bakış için [Azure Site Recovery nedir?](site-recovery-overview.md) konusunu okuyun.

## <a name="overview"></a>Genel Bakış
Bu makalede, VMM özel bulutlarında bulunan Hyper-V ana bilgisayar sunucularındaki Hyper-V sanal makineleri çoğaltmak için Site Recovery'yi Azure'a dağıtma işlemi açıklanır.

Bu makale, senaryolara ilişkin önkoşulları içerir ve Site Recovery kasası ayarlamayı, kaynak VMM sunucusunda yüklü olan Azure Site Recovery Sağlayıcısı'nı edinmeyi, sunucuyu kasaya kaydetmeyi, bir Azure depolama hesabı eklemeyi, Azure Kurtarma Hizmetleri aracısını Hyper-V ana bilgisayar sunucularına yüklemeyi, tüm korunan sanal makinelere uygulanacak VMM bulutları için koruma ayarlarını yapılandırmayı ve ardından bu korumayı tüm sanal makineler için etkinleştirmeyi gösterir. Her şeyin istendiği gibi çalıştığından emin olmak için yük devretmeyi test ederek işlemi sonlandırın.

Tüm yorumlarınızı ve sorularınızı bu makalenin alt kısmında veya [Azure Kurtarma Hizmetleri Forumu](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)'nda paylaşabilirsiniz.

## <a name="architecture"></a>Mimari
![Mimari](./media/site-recovery-vmm-to-azure-classic/topology.png)

* Azure Site Recovery Sağlayıcısı, Site Recovery dağıtımı sırasında VMM'de yüklüdür ve VMM sunucusu Site Recovery kasasına kayıtlıdır. Çoğaltma düzenlemesini sağlamak için Sağlayıcı, Site Recovery ile iletişim kurar.
* Azure Kurtarma Hizmetleri aracısı, Site Recovery dağıtımı sırasında Hyper-V ana bilgisayar sunucularında yüklüdür. Bu aracı, Azure depolama alanına yönelik veri çoğaltma işlemini ele alır.

## <a name="azure-prerequisites"></a>Azure önkoşulları
İşte Azure'da ihtiyaç duyacaklarınız:

| **Önkoşul** | **Ayrıntılar** |
| --- | --- |
| **Azure hesabı** |Bir [Microsoft Azure](https://azure.microsoft.com/) hesabınızın olması gerekir. [Ücretsiz deneme sürümüyle](https://azure.microsoft.com/pricing/free-trial/) başlayabilirsiniz. Site Recovery fiyatlandırması hakkında [daha fazla bilgi edinin](https://azure.microsoft.com/pricing/details/site-recovery/). |
| **Azure depolama alanı** |Çoğaltılan verileri depolamak için bir Azure depolama hesabınızın olması gerekir. Çoğaltılan veriler Azure depolama alanında depolanır ve yük devretme durumunda Azure VM'leri çalışmaya başlar. <br/><br/>[Standart coğrafi olarak yedekli depolama hesabınızın](../storage/storage-redundancy.md#geo-redundant-storage) olması gerekir. Bu hesabın Site Recovery ile aynı bölgede ve aynı abonelikle ilişkilendirilmiş olması gerekir. Premium depolama hesaplarında çoğaltmanın şu anda desteklenmediğini ve bu hesapların kullanılmaması gerektiğini unutmayın.<br/><br/>Azure depolama alanı [hakkında bilgi edinin](../storage/storage-introduction.md). |
| **Azure ağı** |Yük devretme gerçekleştiğinde Azure VM'lerinin bağlanabileceği bir Azure sanal ağı gerekir. Azure sanal ağının Site Recovery kasasıyla aynı bölgede olması gerekir. |

## <a name="on-premises-prerequisites"></a>Şirket içi önkoşullar
İşte şirket içinde ihtiyaç duyacaklarınız:

| **Önkoşul** | **Ayrıntılar** |
| --- | --- |
| **VMM** |Bir fiziksel veya sanal bağımsız sunucu olarak veya sanal küme olarak dağıtılmış en az bir VMM sunucusu gerekir. <br/><br/>VMM sunucusunun, en yeni birikmeli güncelleştirmeleri içeren System Center 2012 R2'yi çalıştırması gerekir.<br/><br/>En az bir adet VMM sunucusunda yapılandırılan bulut gerekir.<br/><br/>Korumak istediğiniz kaynak bulutun, bir veya birden fazla VMM ana bilgisayar grubu içermesi gerekir.<br/><br/>VMM bulutlarını ayarlama hakkında daha fazla bilgi edinmek için Keith Mayer'ın blogundaki [Adım adım kılavuz: System Center 2012 SP1 VMM içeren özel bulut oluşturma](http://blogs.technet.com/b/keithmayer/archive/2013/04/18/walkthrough-creating-private-clouds-with-system-center-2012-sp1-virtual-machine-manager-build-your-private-cloud-in-a-month.aspx) |
| **Hyper-V** |VMM bulutunda bir veya birden çok Hyper-V ana bilgisayar sunucusu veya kümesine sahip olmanız gerekir. Ana bilgisayar sunucusunun bir veya birden çok VM'ye sahip olması gerekir. <br/><br/>Hyper-V sunucusunun, Hyper-V rolü içeren **Windows Server 2012 R2** veya **Microsoft Hyper-V Server 2012 R2** sürümünde ya da bunların sonraki sürümlerinde çalıştırılması ve en son güncelleştirmelerinin yüklenmiş olması gerekir.<br/><br/>Korumak istediğiniz ve VM'leri içeren herhangi bir Hyper-V sunucusunun, VMM bulutunda yer alması gerekir.<br/><br/>Hyper-V'yi bir kümede çalıştırıyorsanız statik IP adresi temelli bir kümeye sahip olmanız durumunda küme aracısının otomatik olarak oluşturulamayacağını unutmayın. Küme aracısını sizin yapılandırmanız gerekir. Aidan Finn'in blog girdisi aracılığıyla [daha fazla bilgi edinin](https://www.petri.com/use-hyper-v-replica-broker-prepare-host-clusters). |
| **Korumalı makineler** | Korumak istediğiniz VM'lerin [Azure gereksinimlerini](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements) karşılaması gerekir. |

## <a name="network-mapping-prerequisites"></a>Ağ eşlemesi önkoşulları
Azure'da sanal makineleri koruduğunuzda, ağ eşlemesi kaynak VMM sunucusundaki VM ağlarını hedef Azure ağları ile eşleyerek şu işlemlere olanak sağlar:

* Aynı ağda yük devreden tüm makineler, hangi kurtarma planında yer aldıklarına bakılmaksızın birbiriyle bağlantı kurabilir.
* Hedef Azure ağında bir ağ geçidi ayarlanırsa sanal makineler diğer şirket içi sanal makinelere bağlanabilir.
* Ağ eşlemesini yapılandırmazsanız yalnızca aynı kurtarma planında yük devreden sanal makineler, Azure'a yük devretme işleminin ardından birbirleriyle bağlantı kurabilir.

Ağ eşlemesi dağıtmak isterseniz şunlara ihtiyaç duyarsınız:

* Kaynak VMM sunucusunda korumak istediğinizde sanal makinelerin bir VM ağına bağlanması gerekir. Bu ağın, bulutla ilişkilendirilen mantıksal bir ağ ile bağlantılı olması gerekir.
* Yük devretme işleminin ardından çoğaltılan sanal makinelerin bağlanabileceği bir Azure ağı. Bu ağı yük devretme sırasında seçersiniz. Ağın Azure Site Recovery aboneliğinizle aynı bölgede olması gerekir.


VMM'de ağları hazırlayın:

   * [Mantıksal ağlar ayarlayın](https://technet.microsoft.com/library/jj721568.aspx).
   * [VM ağları ayarlayın](https://technet.microsoft.com/library/jj721575.aspx).


## <a name="step-1-create-a-site-recovery-vault"></a>1. Adım: Site Recovery kasası oluşturma
1. Kaydolmak istediğiniz VMM sunucusundan [Yönetim Portalı](https://portal.azure.com)'nda oturum açın.
2. **Veri Hizmetleri** > **Kurtarma Hizmetleri** > **Site Recovery Kasası** seçeneklerine tıklayın.
3. **Yeni Oluştur** > **Hızlı Oluştur**'a tıklayın.
4. **Ad** alanına kasayı tanımlamak için kolay bir ad girin.
5. **Bölge** içinde, kasa için coğrafi bölgeyi seçin. Desteklenen bölgeleri kontrol etmek için [Azure Site Recovery Fiyatlandırma Ayrıntıları](https://azure.microsoft.com/pricing/details/site-recovery/) bölümünde Coğrafi Kullanılabilirlik kısmına bakın.
6. **Kasa Oluştur**'a tıklayın.

    ![Yeni Kasa](./media/site-recovery-vmm-to-azure-classic/create-vault.png)

Kasanın başarıyla oluşturulduğunu doğrulamak için durum çubuğunu kontrol edin. Kasa, ana Kurtarma Hizmetleri sayfasında **Etkin** olarak listelenir.

## <a name="step-2-generate-a-vault-registration-key"></a>2. Adım: Kasa kayıt anahtarı oluşturma
Kasada bir kayıt anahtarı oluşturun. Azure Site Recovery Sağlayıcısı'nı indirdikten sonra VMM sunucusuna yükleyin, oluşturduğunuz anahtarı kasadaki VMM sunucusuna kaydolmak için kullanacaksınız.

1. **Kurtarma Hizmetleri** sayfasında, Hızlı Başlangıç sayfasını açmak için kasaya tıklayın. Ayrıca, Hızlı Başlangıç sayfasını istediğiniz zaman simgesini kullanarak da açabilirsiniz.

    ![Hızlı Başlangıç Simgesi](./media/site-recovery-vmm-to-azure-classic/qs-icon.png)
2. Açılır listede **Şirket içi VMM sitesi ile Microsoft Azure arasında** seçeneğini belirleyin.
3. **VMM Sunucularını Hazırla** alanında **Kayıt anahtarı oluştur** dosyasına tıklayın. Anahtar dosyası otomatik olarak oluşturulur ve oluşturulduktan sonra 5 gün boyunca geçerlidir. VMM sunucusundan Azure portalına erişemiyorsanız bu dosyayı sunucuya kopyalamanız gerekir.

    ![Kayıt anahtarı](./media/site-recovery-vmm-to-azure-classic/register-key.png)

## <a name="step-3-install-the-azure-site-recovery-provider"></a>3. Adım: Azure Site Recovery Sağlayıcısı'nı yükleme
1. Sağlayıcı yükleme dosyasının en son sürümünü almak için **Hızlı Başlangıç** > **VMM sunucularını hazırla** kısmından **VMM sunucularında yükleme için Microsoft Azure Site Recovery Sağlayıcısı'nı indir** seçeneğine tıklayın.
2. Bu dosyayı kaynak VMM sunucusunda çalıştırın.

   > [!NOTE]
   > VMM bir kümede dağıtılmışsa ve Sağlayıcı'yı ilk kez yüklüyorsanız yükleme işlemini etkin bir düğümde gerçekleştirin ve VMM sunucusunu kasaya kaydetmek için yüklemeyi sonlandırın. Ardından, Sağlayıcı'yı diğer düğümlere yükleyin. Sağlayıcı'yı yükseltirken, tüm düğümlerin aynı Sağlayıcı sürümünde çalışması gerektiği için Sağlayıcı'yı tüm düğümlerde yükseltmeniz gerektiğini unutmayın.
   >
   >
3. Yükleyici ön gereksinimlere yönelik bir denetim gerçekleştirir ve Sağlayıcının kurulumunu başlatmak için VMM sunucusunu durdurmak için izin ister. Kurulum bittiğinde VMM Hizmeti otomatik olarak yeniden başlatılır. Yükleme işlemini bir VMM kümesinde gerçekleştiriyorsanız Küme rolünü durdurmanız istenir.
4. **Microsoft Update** kısmında güncelleştirmeleri seçebilirsiniz. Bu ayar etkinleştirildiğinde Sağlayıcı güncelleştirmeleri, Microsoft Update ilkenize uygun olarak yüklenir.

    ![Microsoft Güncelleştirmeleri](./media/site-recovery-vmm-to-azure-classic/updates.png)
5. Sağlayıcı'nın yükleneceği konum **<SystemDrive>\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin** olarak belirlenmiştir. **Yükle**'ye tıklayın.

   ![InstallLocation](./media/site-recovery-vmm-to-azure-classic/install-location.png)
6. Sağlayıcı yüklendikten sonra sunucuyu kasaya kaydetmek için **Kaydet**'e tıklayın.

    ![InstallComplete](./media/site-recovery-vmm-to-azure-classic/install-complete.png)
7. **Kasa adı** alanında, sunucunun kayıtlı olduğu kasanın adını doğrulayın. *İleri*’ye tıklayın.

    ![Sunucu kaydı](./media/site-recovery-vmm-to-azure-classic/vaultcred.PNG)
8. **İnternet Bağlantısı** kısmında VMM sunucusunda çalışan Sağlayıcı'nın İnternet'e nasıl bağlanacağını belirleyin. Sunucuda yapılandırılan varsayılan İnternet bağlantısı ayarlarını kullanmak için **Var olan proxy ayarları ile bağlan**'a tıklayın.

    ![İnternet Ayarları](./media/site-recovery-vmm-to-azure-classic/proxydetails.PNG)

   * Özel bir ara sunucu kullanmak isterseniz bu ara sunucuyu Sağlayıcı'yı yüklemeden önce ayarlamanız gerekir. Özel ara sunucu ayarlarını yapılandırırken ara sunucu bağlantısını denetlemek için bir test çalıştırılır.
   * Özel bir ara sunucu kullanırsanız veya varsayılan ara sunucunuz kimlik doğrulama gerektirirse ara sunucu adresi ve bağlantı noktası dahil olmak üzere ara sunucu bilgilerini girmeniz gerekir.
   * VMM Sunucusu'ndan ve Hyper-V ana bilgisayarlarından aşağıdaki URL'lere erişim sağlanması gerekir:
     * *.hypervrecoverymanager.windowsazure.com
     * *.accesscontrol.windows.net
     * *.backup.windowsazure.com
     * *.blob.core.windows.net
     * *.store.core.windows.net
   * [Azure Veri Merkezi IP Aralıkları](https://www.microsoft.com/download/confirmation.aspx?id=41653)'nda tanımlanan IP adreslerine ve HTTPS (443) protokolüne izin verin. Batı ABD ve kullanmayı düşündüğünüz Azure bölgesinin IP aralıklarını güvenilir listeye almanız gerekir.
   * Özel bir ara sunucu kullanırsanız otomatik olarak belirtilen ara sunucu kimlik bilgilerini kullanan VMM RunAs hesabı (DRAProxyAccount) oluşturulacaktır. Bu hesabın kimlik doğrulamasını başarıyla gerçekleştirebilmesi için ara sunucuyu yapılandırın. VMM RunAs hesabı ayarları VMM konsolundan değiştirilebilir. Bu işlemi gerçekleştirmek için **Ayarlar** çalışma alanını açın, **Güvenlik** bölümünü genişletin, **Farklı Çalıştır Hesapları**’na tıklayın ve ardından DRAProxyAccount parolasını değiştirin. Bu ayarın etkili olabilmesi için VMM hizmetinin yeniden başlatılması gerekir.
9. **Kayıt Anahtarı** kısmında, Azure Site Recovery'den indirdiğiniz anahtarı seçin ve VMM sunucusuna kopyalayın.
10. Şifreleme ayarı yalnızca Hyper-V sanal makinelerini Azure'daki VMM bulutlarına çoğaltırken kullanılır. İkincil bir siteye çoğaltma yapıyorsanız kullanılmaz.
11. **Sunucu adı** alanında, kasadaki VMM sunucusunu tanımlamak için bir kolay ad belirtin. Küme yapılandırmasında VMM kümesi rolü adını belirtin.
12. **Bulut meta verilerini eşitle** bölümünde VMM sunucusundaki tüm bulutlar için meta verileri kasa ile eşitlemek isteyip istemediğinizi seçin. Bu eylemin her sunucuda yalnızca bir kez gerçekleştirilmesi gerekir. Tüm bulutları eşitlemek istemezseniz bu ayarı işaretlemeden bırakıp her bulutu VMM konsolundaki bulut özelliklerinde bağımsız olarak eşitleyebilirsiniz.
13. İşlemi tamamlamak için **İleri**'ye tıklayın. Kayıttan sonra Azure Site Recovery tarafından VMM sunucusundan meta veriler alınır. Sunucu, kasadaki **Sunucular** sayfasında **VMM Sunucuları** sekmesinde görünür.

    ![Son sayfa](./media/site-recovery-vmm-to-azure-classic/provider13.PNG)

Kayıttan sonra Azure Site Recovery tarafından VMM sunucusundan meta veriler alınır. Sunucu, kasadaki **Sunucular** sayfasında bulunan **VMM Sunucuları** sekmesinde görüntülenir.

### <a name="command-line-installation"></a>Komut satırı yüklemesi
Azure Site Recovery sağlayıcısı, aşağıdaki komut satırını kullanarak da yüklenebilir. Bu yöntem, sağlayıcıyı Windows Server 2012 R2 için Sunucu Çekirdeği'nde yüklerken kullanılabilir.

1. Sağlayıcı yükleme dosyasını ve kayıt anahtarını bir klasöre indirin. Örnek: C:\ASR.
2. System Center Virtual Machine Manager hizmetini durdurun
3. Yükseltilmiş bir komut isteminde, Sağlayıcı yükleyicisini şu komutları kullanarak çıkarın:

        C:\Windows\System32> CD C:\ASR
        C:\ASR> AzureSiteRecoveryProvider.exe /x:. /q
4. Sağlayıcıyı şu şekilde yükleyin:

        C:\ASR> setupdr.exe /i
5. Sağlayıcıyı şu şekilde kaydedin:

        CD C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin
        C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin\> DRConfigurator.exe /r  /Friendlyname <friendly name of the server> /Credentials <path of the credentials file> /EncryptionEnabled <full file name to save the encryption certificate>       

Parametre konumları aşağıdaki şekildedir:

* **/Credentials**: Kayıt anahtarı dosyasının bulunduğu konumu belirten zorunlu parametre.  
* **/FriendlyName**: Azure Site Recovery portalında görünen Hyper-V ana bilgisayar sunucusu adına yönelik zorunlu parametre.
* **/EncryptionEnabled**: Azure'da sanal makinelerinizi şifrelemek isteyip istemediğinizi belirtmek için isteğe bağlı parametre (bekleyen şifreleme). Dosya adının **.pfx** uzantısını içermesi gerekir.
* **/proxyAddress**: Ara sunucunun adresini belirten isteğe bağlı parametre.
* **/proxyport**: Ara sunucunun bağlantı noktasını belirten isteğe bağlı parametre.
* **/proxyUsername**: Ara sunucu kullanıcı adını belirten isteğe bağlı parametre 
* **/proxyPassword**: Ara sunucu parolasını belirten isteğe bağlı parametre   

## <a name="step-4-create-an-azure-storage-account"></a>4. Adım: Azure depolama hesabı oluşturma
1. Azure depolama hesabınız yoksa hesap oluşturmak için **Azure Storage Hesabı Ekle**'ye tıklayın.
2. Coğrafi çoğaltmanın etkinleştirilmiş olduğu bir hesap oluşturun. Bu hesabın Azure Site Recovery hizmeti ile aynı bölgede olması ve aynı abonelikle ilişkilendirilmiş olması gerekir.

    ![Depolama hesabı](./media/site-recovery-vmm-to-azure-classic/storage.png)

> [!NOTE]
> Site Recovery dağıtımında kullanılan depolama hesapları için aynı abonelik içindeki kaynak grupları arasında veya abonelik arasında [depolama hesapları geçişi](../azure-resource-manager/resource-group-move-resources.md) desteklenmez.
>
>

## <a name="step-5-install-the-azure-recovery-services-agent"></a>5. Adım: Azure Kurtarma Hizmetleri Aracısı'nı yükleme
VMM bulutundaki tüm Hyper-V ana bilgisayar sunucularına Azure Kurtarma Hizmetleri aracısını yükleyin.

1. Aracı yükleme dosyasının en son sürümünü almak için **Hızlı Başlangıç** > **Azure Kurtarma Hizmetleri Aracısı'nı indir ve ana bilgisayarlara yükle** seçeneklerine tıklayın.

    ![Kurtarma Hizmetleri Aracısı'nı Yükleme](./media/site-recovery-vmm-to-azure-classic/install-agent.png)
2. Her Hyper-V ana bilgisayar sunucusunda yükleme dosyasını çalıştırın.
3. **Önkoşul Denetimi** sayfasında **İleri**'ye tıklayın. Eksik önkoşullar otomatik olarak yüklenir.

    ![Kurtarma Hizmetleri Aracısı Önkoşulları](./media/site-recovery-vmm-to-azure-classic/agent-prereqs.png)
4. **Yükleme Ayarları** sayfasında aracıyı yüklemek istediğiniz konumu ve yedekleme meta verilerinin yükleneceği önbellek konumunu seçin. Ardından **Yükle**'ye tıklayın.
5. Yükleme bittikten sonra sihirbazı tamamlamak için **Kapat**'a tıklayın.

    ![MARS Aracısı'nı Kaydetme](./media/site-recovery-vmm-to-azure-classic/agent-register.png)

### <a name="command-line-installation"></a>Komut satırı yüklemesi
Aşağıdaki komutu kullanarak Microsoft Azure Kurtarma Hizmetleri Aracısı'nı komut satırından da yükleyebilirsiniz:

    marsagentinstaller.exe /q /nu

## <a name="step-6-configure-cloud-protection-settings"></a>6. Adım: Bulut koruma ayarlarını yapılandırma
VMM sunucusunu kaydettikten sonra bulut koruma ayarlarını yapılandırabilirsiniz. Sağlayıcı'yı yüklerken **Bulut verilerini kasayla eşitle** seçeneğini etkinleştirdiğinizden VMM sunucusundaki tüm bulutlar kasadaki <b>Korunan Öğeler</b> sekmesinde görünür.

![Yayımlanan Bulut](./media/site-recovery-vmm-to-azure-classic/clouds-list.png)

1. Hızlı Başlangıç sayfasında **VMM Bulutları için koruma ayarla**'ya tıklayın.
2. **Korunan Öğeler** sekmesinde, yapılandırmak istediğiniz buluta tıklayın ve **Yapılandırma** sekmesine gidin.
3. **Hedef** alanı için **Azure**'ı seçin.
4. **Depolama Hesabı** kısmında çoğaltma için kullandığınız Azure depolama hesabını seçin.
5. **Depolanan verileri şifrele** seçeneğini **Kapalı** olarak ayarlayın. Bu ayar, şirket içi site ile Azure arasında çoğaltma gerçekleştiği sırada verilerin şifrelenmesi gerektiğini belirtir.
6. **Kopyalama sıklığı** alanında varsayılan ayarları koruyun. Bu değer, verilerin kaynak ve hedef konumlar arasında hangi sıklıkla eşitlenmesi gerektiğini belirtir.
7. **Koruma noktalarını şu değere göre koru:** kısmında varsayılan ayarı koruyun. Varsayılan değerin sıfır olması durumunda, birincil sanal makinenin yalnızca en son kurtarma noktası çoğaltılan ana bilgisayar sunucusunda depolanır.
8. **Uygulamayla tutarlı anlık görüntü sıklığı** alanında varsayılan ayarı koruyun. Bu değer, anlık görüntülerin hangi sıklıkta oluşturulacağını belirtir. Anlık görüntüler, anlık görüntü alınırken uygulamaların tutarlı bir durumda olmalarını sağlamak için Birim Gölge Kopyası Hizmeti'ni (VSS) kullanır.  Bir değer ayarlarsanız bu değerin yapılandırdığınız ilave kurtarma noktası sayısından daha az olduğundan emin olun.
9. **Çoğaltma başlangıç zamanı** alanında verilerin Azure'a çoğaltmasının ilk olarak ne zaman başlayacağını belirtin. Hyper-V ana bilgisayarındaki saat dilimi kullanılır. İlk çoğaltmayı yoğun olmayan saatlerde planlamanızı öneririz.

    ![Bulut çoğaltma ayarları](./media/site-recovery-vmm-to-azure-classic/cloud-settings.png)

Ayarlar kaydedildikten sonra bir iş oluşturulur ve bu iş **İşler** sekmesinden izlenebilir. VMM kaynak bulutundaki tüm Hyper-V sunucular çoğaltma için yapılandırılır.

Bulut ayarları kaydedildikten sonra **Yapılandır** sekmesinden değiştirilebilir. Hedef konumu veya hedef depolama hesabını değiştirmek için bulut yapılandırmasını kaldırıp bulutu yeniden yapılandırmanız gerekir. Depolama hesabını değiştirirseniz bu değişikliğin yalnızca depolama hesabı değiştirildikten sonra koruma işleminin etkinleştirildiği sanal makineler için uygulanacağını unutmayın. Var olan sanal makineler yeni depolama hesabına taşınmaz.

## <a name="step-7-configure-network-mapping"></a>7. Adım: Ağ eşlemesini yapılandırma
Ağ eşlemesine başlamadan önce kaynak VMM sunucusundaki sanal makinelerin bir VM ağına bağlı olduğunu doğrulayın. Ayrıca, bir veya daha fazla Azure sanal ağı oluşturun. Birden çok VM ağının tek bir Azure ağına eşlenebileceğini unutmayın.

1. Hızlı Başlangıç sayfasında **Ağları eşle**'ye tıklayın.
2. **Ağlar** sekmesinde **Kaynak konumu** kısmında kaynak VMM sunucusunu seçin. **Hedef konumu** için Azure'ı seçin.
3. **Kaynak** ağlar bölümünde, VMM sunucusuyla ilişkilendirilen VM ağlarının listesi görüntülenir. **Hedef** ağlar bölümünde, abonelikle ilişkilendirilen Azure ağları görüntülenir.
4. Kaynak VM ağı seçin ve **Eşle**'ye tıklayın.
5. **Hedef Ağ Seç** sayfasında kullanmak istediğiniz hedef Azure ağını seçin.
6. Eşleme işlemini tamamlamak için onay işaretine tıklayın.

    ![Bulut çoğaltma ayarları](./media/site-recovery-vmm-to-azure-classic/map-networks.png)

Ayarlar kaydedildikten sonra eşleme işlemini izlemek için bir iş başlatılır ve bu iş, İşler sekmesinden izlenebilir. Kaynak VM ağına karşılık gelen tüm var olan çoğaltılmış sanal makineler hedef Azure ağlarına bağlanır. Kaynak VM ağına bağlanan yeni sanal makineler, çoğaltmanın ardından eşlenen Azure ağına bağlanır. Var olan bir eşlemeyi yeni bir ağ ile değiştirirseniz çoğaltılan sanal makineler yeni ayarlara göre bağlantı kurar.

Hedef ağın birden çok alt ağı varsa ve bu alt ağlardan biri kaynak sanal makinenin bulunduğu alt ağ ile aynı adı taşıyorsa çoğaltılan sanal makinenin, yük devretme işleminin ardından hedef alt ağa bağlandığını unutmayın. Eşleşen ada sahip bir hedef alt ağ yoksa sanal makine ağdaki ilk alt ağa bağlanır.

> [!NOTE]
> Site Recovery dağıtımında kullanılan ağlar için aynı abonelik içindeki kaynak grupları arasında veya abonelik arasında [ağ geçişi](../azure-resource-manager/resource-group-move-resources.md) desteklenmez.
>
>

## <a name="step-8-enable-protection-for-virtual-machines"></a>8. Adım: Sanal makinelere yönelik korumayı etkinleştirme
Sunucular, bulutlar ve ağlar doğru şekilde yapılandırıldıktan sonra buluttaki sanal makineler için korumayı etkinleştirebilirsiniz. Şunlara dikkat edin:

* Sanal makinelerin [Azure gereksinimlerini](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements) karşılaması gerekir.
* Korumayı etkinleştirmek için işletim sisteminin ve işletim sistemi diski özelliklerinin sanal makineye göre ayarlanmış olması gerekir. Sana makine şablonu kullanarak VMM'de bir sanal makine oluşturduğunuzda özelliği de ayarlayabilirsiniz. Ayrıca var olan sanal makineler için bu özellikleri, sanal makinenin **Genel** ve **Donanım Yapılandırması** sekmelerinde de ayarlayabilirsiniz. Özellikleri VMM'de ayarlamazsanız Azure Site Recovery portalından da yapılandırabilirsiniz.

    ![Sanal makine oluşturma](./media/site-recovery-vmm-to-azure-classic/enable-new.png)

    ![Sanal makine özelliklerini değiştirme](./media/site-recovery-vmm-to-azure-classic/enable-existing.png)

1. Korumayı etkinleştirmek için sanal makinelerin bulunduğu buluttaki **Virtual Machines** sekmesinde **Korumayı etkinleştir** > **Sanal makine ekle** seçeneklerine tıklayın.
2. Buluttaki sanal makineler listesinden korumak istediğinizi seçin.

    ![Sanal makine korumasını etkinleştirme](./media/site-recovery-vmm-to-azure-classic/select-vm.png)

    **İşler** sekmesinden ilk çoğaltma dahil olmak üzere **Korumayı Etkinleştir** eyleminin ilerleyişini izleyin. **Korumayı Sonlandır** işi çalıştırıldıktan sonra sanal makine yük devretmeye hazırdır. Koruma etkinleştirilip sanal makineler çoğaltıldıktan sonra bunları Azure'da görüntüleyebilirsiniz.

    ![Sanal makine koruma işi](./media/site-recovery-vmm-to-azure-classic/vm-jobs.png)

1. Sanal makine özelliklerini doğrulayın ve gerektiği şekilde değişiklik yapın.

    ![Sanal makineleri doğrulama](./media/site-recovery-vmm-to-azure-classic/vm-properties.png)
2. Sanal makine özelliklerinin **Yapılandır** sekmesinde aşağıdaki ağ özellikleri değiştirilebilir.

* **Hedef sanal makinedeki ağ bağdaştırıcı sayısı** - Ağ bağdaştırıcılarının sayısı, hedef sanal makine için sizin belirlediğiniz boyuta göre dikte edilir. Sanal makine boyutunun desteklediği bağdaştırıcı sayısı için [sanal makine boyutu özellikleri](../virtual-machines/linux/sizes.md)'ni kontrol edin. Bir sanal makine için boyutu değiştirip ayarları kaydettiğinizde, **Yapılandır** sayfasını bir sonraki açışınızda ağ bağdaştırıcısı sayısı değişir. Hedef sanal makinelerin ağ bağdaştırıcısı sayısı, kaynak sanal makinelerin minimum ağ bağdaştırıcı sayısıdır ve seçilen sanal makine boyutu tarafından desteklenen ağ bağdaştırıcıların maksimum sayısı aşağıdaki şekildedir:

  * Kaynak makinedeki ağ bağdaştırıcılarının sayısı, hedef makine boyutu için verilen ağ bağdaştırıcısı sayısına eşitse veya daha azsa hedef makine kaynakla aynı sayıda bağdaştırıcıya sahip olur.
  * Kaynak sanal makinenin bağdaştırıcı sayısı, hedef boyut için izin verilen sayıyı aşarsa maksimum hedef boyutu kullanılır.
  * Örneğin, kaynak makinenin iki bağdaştırıcısı varsa ve hedef makine boyutu dört adet bağdaştırıcıyı destekliyorsa hedef makinenin iki bağdaştırıcısı olur. Kaynak makinenin iki bağdaştırıcısı varken hedef boyut yalnızca bir bağdaştırıcıyı destekliyorsa hedef makinenin bir bağdaştırıcısı olur.     
* **Hedef sanal makine ağı** - Sanal makinenin bağlanacağı ağ, kaynak sanal makine ağının ağ eşlemesine göre belirlenir. Kaynak sanal makinenin birden fazla ağ bağdaştırıcısı varsa ve kaynak sağlar hedefte farklı ağlarla eşlendiyse hedef ağlardan birini seçmeniz gerekir.
* **Her ağ bağdaştırıcısının alt ağı** - Her ağ bağdaştırıcısı için yük devreden sanal makinenin bağlanacağı alt ağı seçebilirsiniz.
* **Hedef IP adresi** - Kaynak sanal makinenin ağ bağdaştırıcısı statik IP kullanmak üzere yapılandırıldıysa hedef sanal makine için IP adresi sağlayabilirsiniz. Bu özelliği, yük devretme işleminden sonra kaynak sanal makinenin IP adresini korumak için kullanın. IP adresi sağlanmazsa yük devretme sırasında kullanılabilir olan herhangi bir IP adresi ağ bağdaştırıcısına verilir. Hedef IP adresi belirlenmişse ancak Azure'da çalışan başka bir sanal makine tarafından kullanılıyorsa yük devretme başarısız olur.  

    ![Ağ özelliklerini değiştirme](./media/site-recovery-vmm-to-azure-classic/multi-nic.png)

> [!NOTE]
> Statik IP adresi içeren Linux sanal makineleri desteklenmez.
>
>

## <a name="test-the-deployment"></a>Dağıtımı test etme
Dağıtımınızı test etmek için tek bir sanal makine için bir yük devretme testi çalıştırabilir veya birden çok sanal makine içeren bir kurtarma planı oluşturup bu plan için yük devretme testi çalıştırabilirsiniz.  

Yük devretme testi, yük devretme işleminizi ve kurtarma mekanizmanızı yalıtılmış bir ortamda benzetimli olarak gerçekleştirir. Şunlara dikkat edin:

* Yük devretmenin ardından Azure'daki sanal makineye Uzak Masaüstü kullanarak bağlanmak isterseniz yük devretme testini çalıştırmadan önceden sanal makinenizde Uzak Masaüstü Bağlantısı'nı etkinleştirin.
* Yük devretmeden sonra Uzak Masaüstü kullanarak Azure sanal makinesine bağlamak için genel IP adresi kullanırsınız. Bu işlemi gerçekleştirmek isterseniz genel bir adres kullanarak sanal makineye bağlanmanızı engelleyecek etki alanı ilkelerine sahip olmadığınızdan emin olun.

> [!NOTE]
> Azure'a yük devrederken en iyi performansı elde etmek için sanal makineye Azure aracısını yüklediğinizden emin olun. Aracı daha hızlı önyükleme gerçekleştirilmesini sağlar ve sorun giderme konusunda yardımcı olur. [Linux aracısını](https://github.com/Azure/WALinuxAgent) veya [Windows aracısını](http://go.microsoft.com/fwlink/?LinkID=394789) indirin.
>
>

### <a name="create-a-recovery-plan"></a>Kurtarma planı oluşturma
1. **Kurtarma Planları** sekmesinde yeni bir plan ekleyin. Bir ad belirtin, **Kaynak türü** için **VMM** ve **Kaynak** için kaynak VMM sunucusu seçeneklerini belirleyin. Hedef Azure olur.

    ![Kurtarma planı oluşturma](./media/site-recovery-vmm-to-azure-classic/recovery-plan1.png)
2. **Sanal Makine Seç** sayfasında kurtarma planına eklenecek sanal makineleri seçin. Bu sanal makineler, kurtarma planının varsayılan grubu olan Grup 1'e eklenir. Tek bir kurtarma planında en fazla 100 sanal makine test edilebilir.

* Plana eklemeden önce sanal makine özelliklerini doğrulamak isterseniz sanal makinenin bulunduğu bulutun özellikler sayfasında sanal makineye tıklayın. Ayrıca, VMM konsolunda da sanal makine özelliklerini yapılandırabilirsiniz.
* Gösterilen sanal makinelerin tümü için koruma etkinleştirilmiştir. Listede koruma özelliği etkinleştirilen ve ilk çoğaltması tamamlanan sanal makinelerin yanı sıra koruma özelliği etkinleştirilmiş ancak ilk çoğaltması bekleme durumunda olan sanal makineler bulunur. Yalnızca ilk çoğaltması tamamlanan sanal makineler kurtarma planının bir parçası olarak yük devredebilir.

    ![Kurtarma planı oluşturma](./media/site-recovery-vmm-to-azure-classic/select-rp.png)

Bir kurtarma planı oluşturulduktan sonra **Kurtarma Planları** sekmesinde görünür. Ayrıca, yük devretme sırasında eylemleri otomatikleştirmek için kurtarma planına [Azure otomasyonu Runbook'larını](site-recovery-runbook-automation.md) da ekleyebilirsiniz.

### <a name="run-a-test-failover"></a>Yük devretme testi çalıştırma
Azure için yük devretme testi çalıştırmanın iki yöntemi vardır.

* **Azure ağı olmadan yük devretme testi** - Bu türdeki yük devretme testi sanal makinenin Azure'a doğru şekilde alınıp alınmadığını denetler. Yük devretmeden sonra sanal makine herhangi bir Azure ağına bağlanmaz.
* **Azure ağıyla yük devretme testi** - Bu türdeki yük devretme testi ise tüm çoğaltma ortamının istendiği şekilde alınıp alınmadığını ve yük devredilen sanal makinelerin belirlenen bir hedef ağa bağlanıp bağlanmayacağını denetler. Alt ağı ele almaya ilişkin yük devretme testi için test edilen sanal makinenin alt ağı, çoğaltılan sanal makinenin alt ağına göre belirlenir. Bu işlem, çoğaltılan sanal makinenin alt ağının kaynak sanal makinenin alt ağına bağlı olduğunda normal çoğaltmadan farklıdır.

Azure hedef ağı belirtmeden Azure için koruması etkinleştirilmiş bir sanal makine için yük devretme testi çalıştırmak isterseniz herhangi bir hazırlık yapmanız gerekmez. Hedef Azure ağıyla bir yük devretme testi çalıştırmak isterseniz Azure üretim ağınızdan yalıtılmış olan yeni bir Azure ağı oluşturmanız gerekir (Azure'da yeni bir ağ oluştururken, varsayılan davranıştır). Daha fazla ayrıntı için [yük devretme testi çalıştırma](site-recovery-failover.md) bölümüne bakın.

Ayrıca, istenildiği gibi çalışması için çoğaltılan sanal makineye yönelik altyapı ayarlamanız gerekir. Örneğin, Etki Alanı Denetleyicisi ve DNS içeren bir sanal makine Azure'da Azure Site Recovery kullanılarak çoğaltılabilir ve Yük Devretme Testi kullanılarak test ağında oluşturulabilir. Daha fazla ayrıntı için [active directory için yük devretme testi ile ilgili dikkat edilmesi gerekenler](site-recovery-active-directory.md#test-failover-considerations) bölümüne bakın

Yük devretme testi çalıştırmak için şunları yapın:

1. **Kurtarma Planları** sekmesinde planı seçin ve **Yük devretme Testi**'ne tıklayın.
2. **Yük Devretme Testi Onaylama** sayfasında **Hiçbiri** veya özel Azure ağı seçimlerini belirleyin.  Hiçbiri seçeneğini belirlerseniz yük devretme testinin çoğaltma ağı yapılandırmasını denetlemeden yalnızca Azure'a doğru şekilde çoğaltılan sanal makineyi denetleyeceğini unutmayın.

    ![Ağ yok](./media/site-recovery-vmm-to-azure-classic/test-no-network.png)
3. Bulut için veri şifreleme etkinleştirilmişse **Şifreleme Anahtarı** kısmında Sağlayıcı'nın VMM sunucusuna yüklenmesi sırasında bulut için veri şifrelemeyi etkinleştirme seçeneğini açtığınızda verilen sertifikayı seçin.
4. **İşler** sekmesinden yük devretme işleminin ilerleyişini izleyebilirsiniz. Ayrıca, sanal makine testi çoğaltmasının Azure portalında görünür olması gerekir. Şirket içi ağınızdan sanal makinelere erişimi ayarladıysanız sanal makineye yönelik Uzak Masaüstü bağlantısını başlatabilirsiniz.
5. Yük devretme işlemi **Testi tamamla** aşamasına ulaştığında işlemi sonlandırmak için **Testi Tamamla**'ya tıklayın. Yük devretme işleminin ilerleyişini durumunu izlemek için ve gerekli eylemleri gerçekleştirmek için **İş** sekmesinin ayrıntılarına göz atabilirsiniz.
6. Yük devretmeden sonra sanal makine test çoğaltmasını Azure portalında görebilirsiniz. Şirket içi ağınızdan sanal makinelere erişimi ayarladıysanız sanal makineye yönelik Uzak Masaüstü bağlantısını başlatabilirsiniz. Şunları yapın:

   1. Sanal makinelerin başarılı bir şekilde başlatıldığını doğrulayın.
   2. Yük devretmenin ardından Azure'daki sanal makineye Uzak Masaüstü kullanarak bağlanmak isterseniz yük devretme testini çalıştırmadan önceden sanal makinenizde Uzak Masaüstü Bağlantısı'nı etkinleştirin. Ayrıca, sanal makinede bir RDP uç noktası eklemeniz gerekir. Bu işlemi gerçekleştirmek için [Azure Automation Runbook](site-recovery-runbook-automation.md)'larından yararlanabilirsiniz.
   3. Yük devretme bittikten sonra Uzak Masaüstü'nü kullanarak Azure'daki sanal makineye bağlanmak için genel bir IP adresi kullanırsanız, genel bir adres kullanarak sanal makineye bağlanmanızı engelleyecek herhangi bir etki alanı ilkeniz olmadığından emin olun.
7. Test tamamlandıktan sonra şunları yapın:

   * **Yük devretme testi tamamlandı** seçeneğine tıklayın. Otomatik olarak kapatmak ve sanal makine testlerini silmek için test ortamını temizleyin.
   * Yük devretme testiyle ilişkili gözlemlerinizi kaydetmek ve saklamak için **Notlar**'a tıklayın.


## <a name="next-steps"></a>Sonraki adımlar
[Kurtarma planlarını ayarlama](site-recovery-create-recovery-plans.md) ve [yük devretme](site-recovery-failover.md) hakkında daha fazla bilgi edinin.

