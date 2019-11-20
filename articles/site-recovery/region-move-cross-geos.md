---
title: Azure VM 'lerini kamu ve kamu bölgeleri arasında Azure Site Recovery ile taşıma
description: Azure sanal makinelerini Azure Kamu ve kamu bölgeleri arasında taşımak için Azure Site Recovery kullanın.
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: 2a749e9345fec0e91751641cd15805d7f7d62d95
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73961404"
---
# <a name="move-azure-vms-between-azure-government-and-public-regions"></a>Azure Kamu ve ortak bölgeler arasında Azure VM 'lerini taşıma 

Mevcut sanal makinelerinizin kullanılabilirliğini artırmak, yönetilebilirlik düzeyini artırmak veya idare nedenleriyle, [burada](azure-to-azure-move-overview.md)açıklandığı gibi, IaaS sanal makinelerinizi Azure Kamu ve kamu bölgeleri arasında taşımak isteyebilirsiniz.

İş sürekliliği ve olağanüstü durum kurtarma (BCDR) amacıyla Şirket içi makinelerin ve Azure VM 'lerinin olağanüstü durum kurtarma işlemlerini yönetmek ve düzenlemek için [Azure Site Recovery](site-recovery-overview.md) hizmetini kullanmanın yanı sıra, Azure sanal makinelerini ikincil bir bölgeye taşımayı yönetmek için Site Recovery de kullanabilirsiniz.       

Bu öğreticide, Azure sanal makinelerini Azure Kamu ve ortak bölgeler arasında Azure Site Recovery kullanarak nasıl taşıyacağınız gösterilmektedir. Aynı coğrafi küme içinde olmayan bölge çiftleri arasında VM 'Leri taşımak için de aynı genişletilebilir. Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Önkoşulları doğrulama
> * Kaynak VM 'Leri hazırlama
> * Hedef bölgeyi hazırlama
> * Hedef bölgeye veri kopyalama
> * Yapılandırmayı test etme
> * Taşımayı gerçekleştir
> * Kaynak bölgedeki kaynakları at

> [!IMPORTANT]
> Bu öğreticide, Azure sanal makinelerini Azure Kamu ve ortak bölgeler arasında veya Azure VM 'Leri için normal olağanüstü durum kurtarma çözümü tarafından desteklenmeyen bölgeler çiftleri arasında nasıl taşıyacağınız gösterilmektedir. Bu durumda, kaynak ve hedef bölge çiftlerinizin [desteklenmiş](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support)olması için lütfen taşıma için bu [belgeye](azure-to-azure-tutorial-migrate.md) başvurun. Gereksiniminiz, bir kullanılabilirlik kümesindeki VM 'Leri farklı bir bölgedeki bölge sabitlenmiş VM 'lere taşıyarak kullanılabilirliği geliştirmek istiyorsanız [buradaki](move-azure-VMs-AVset-Azone.md)öğreticiye bakın.

> [!IMPORTANT]
> Çiftler, bir DR senaryosu için kritik olan veri gecikmesini aklınızda tutarak, desteklenmeyen bölge çiftleri arasında DR 'yi yapılandırmak için bu yöntemin kullanılması önerilmez.

## <a name="verify-prerequisites"></a>Önkoşulları doğrulama

> [!NOTE]
> Bu senaryonun [mimarisini ve bileşenlerini](physical-azure-architecture.md) anladığınızdan emin olun. Bu mimari, **VM 'leri fiziksel sunucu olarak düşünerek**Azure VM 'leri taşımak için kullanılacaktır.

- Tüm bileşenler için [destek gereksinimlerini](vmware-physical-secondary-support-matrix.md) gözden geçirin.
- Çoğaltmak istediğiniz sunucuların [Azure VM gereksinimleriyle](vmware-physical-secondary-support-matrix.md#replicated-vm-support)uyumlu olduğundan emin olun.
- Çoğaltmak istediğiniz her sunucuya Mobility hizmetinin otomatik olarak yüklenmesine yönelik bir hesap hazırlayın.

- Azure 'da hedef bölgeye yük devretmek için kaynak bölgeye geri dönme işlemini doğrudan gerçekleştirebileceğinizi unutmayın. Çoğaltmayı yeniden hedefe tekrar ayarlamanız gerekir.

### <a name="verify-azure-account-permissions"></a>Azure hesabı izinlerini doğrulama

Azure hesabınızın VM 'lerin Azure 'a çoğaltılması için izinlere sahip olduğundan emin olun.

- Makineleri Azure 'a çoğaltmak için gereken [izinleri](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) gözden geçirin.
- [Rol tabanlı erişim](../role-based-access-control/role-assignments-portal.md) izinlerini doğrulayın ve değiştirin. 

### <a name="set-up-an-azure-network"></a>Azure ağı ayarlama

Hedef [Azure ağını](../virtual-network/quick-create-portal.md)ayarlayın.

- Yük devretmeden sonra oluşturulan Azure VM 'Leri bu ağa yerleştirilir.
- Ağın, kurtarma hizmetleri kasasıyla aynı bölgede olması gerekir


### <a name="set-up-an-azure-storage-account"></a>Azure depolama hesabı ayarlama

Bir [Azure depolama hesabı](../storage/common/storage-quickstart-create-account.md)ayarlayın.

- Site Recovery, şirket içi makineleri Azure Storage 'a çoğaltır. Yük devretme gerçekleştikten sonra Azure VM 'Leri depolamadan oluşturulur.
- Depolama hesabının, Kurtarma Hizmetleri kasasıyla aynı bölgede olması gerekir.


## <a name="prepare-the-source-vms"></a>Kaynak VM 'Leri hazırlama

### <a name="prepare-an-account-for-mobility-service-installation"></a>Bir hesabı Mobility hizmeti yüklemesi için hazırlama

Mobility hizmeti, çoğaltmak istediğiniz her bir sunucuda yüklü olmalıdır. Site Recovery, sunucu için çoğaltmayı etkinleştirdiğinizde bu hizmeti otomatik olarak yüklüyor. Otomatik olarak yüklemek için Site Recovery sunucuya erişmek üzere kullanacağı bir hesap hazırlamanız gerekir.

- Bir etki alanı veya yerel hesap kullanabilirsiniz
- Windows VM 'Ler için, bir etki alanı hesabı kullanmıyorsanız yerel makinede uzak kullanıcı erişim denetimini devre dışı bırakın. Bunu yapmak için, Register **HKEY_LOCAL_MACHINE \Software\microsoft\windows\currentversion\ilkelersystem**altında, 1 değeriyle **LocalAccountTokenFilterPolicy**DWORD girişini ekleyin.
- Bir CLı 'dan ayarı devre dışı bırakmak üzere kayıt defteri girdisini eklemek için şunu yazın: ``REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1.``
- Linux için hesap, kaynak Linux sunucusunda kök olmalıdır.


## <a name="prepare-the-target-region"></a>Hedef bölgeyi hazırlama

1. Azure aboneliğinizin, olağanüstü durum kurtarma için kullanılan hedef bölgede VM’ler oluşturmanıza izin verdiğinden emin olun. Gerekli kotayı sağlamak için desteğe başvurun.

2. Aboneliğinizin, kaynak VM’lerinize uygun boyutlardaki VM’leri desteklemek için yeterli kaynakları içerdiğinden emin olun. verileri hedefe kopyalamak için Site Recovery kullanıyorsanız, hedef VM için aynı boyutu veya mümkün olan en yakın boyutu seçer.

3. Kaynak ağ düzeninde tanımlanan her bileşen için bir hedef kaynak oluşturduğunuzdan emin olun. Bu, hedef bölgeye daha fazla işlem sonrası, sanal makinelerinizin kaynakta sahip olduğunuz tüm işlevsellik ve özelliklere sahip olduğundan emin olmak önemlidir.

    > [!NOTE]
    > Azure Site Recovery, kaynak VM için çoğaltmayı etkinleştirdiğinizde bir sanal ağı otomatik olarak bulur ve oluşturur ya da çoğaltmayı etkinleştirmek için Kullanıcı akışındaki bir ağı önceden oluşturup VM 'ye atayabilirsiniz. Ancak, aşağıda belirtildiği gibi diğer kaynaklar için, bunları hedef bölgede el ile oluşturmanız gerekir.

     Kaynak VM yapılandırmasına bağlı olarak, sizin için uygun olan en sık kullanılan ağ kaynaklarını oluşturmak için lütfen aşağıdaki belgelere bakın.

    - [Ağ güvenlik grupları](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)
    - [Yük dengeleyiciler](https://docs.microsoft.com/azure/load-balancer)
    - [Genel IP](../virtual-network/virtual-network-public-ip-address.md)
    
    Diğer ağ bileşenleri için ağ [belgelerine bakın.](https://docs.microsoft.com/azure/#pivot=products&panel=network) 

4. Hedef bölgede son kesmeyi gerçekleştirmeden önce yapılandırmayı test etmek isterseniz, hedef bölgede el ile [bir üretim dışı ağ oluşturun](https://docs.microsoft.com/azure/virtual-network/quick-create-portal) . Bu işlem, üretimde en az girişim oluşturur ve önerilir.

## <a name="copy-data-to-the-target-region"></a>Hedef bölgeye veri kopyalama
Aşağıdaki adımlar, verileri hedef bölgeye kopyalamak için Azure Site Recovery nasıl kullanacağınızı yönlendirecektir.

### <a name="create-the-vault-in-any-region-except-the-source-region"></a>Kaynak bölgesi dışında herhangi bir bölgede kasayı oluşturun.

1. [Azure Portal](https://portal.azure.com) > **Kurtarma Hizmetleri**’nde oturum açın.
2. **Yedekleme ve Site Recovery** > **Yönetim Araçları** > **kaynak oluştur ' a** tıklayın.
3. **Ad** bölümünde **ContosoVMVault** kolay adını belirtin. Birden çok tane varsa. abonelik, uygun olanı seçin.
4. Bir **ContosoRG** kaynak grubu oluşturun.
5. Bir Azure bölgesi belirtin. Desteklenen bölgeleri kontrol etmek için [Azure Site Recovery Fiyatlandırma Ayrıntıları](https://azure.microsoft.com/pricing/details/site-recovery/) bölümündeki coğrafi kullanılabilirlik kısmına bakın.
6. Kurtarma Hizmetleri kasalarında **genel bakış** > **ConsotoVMVault** >  **+ Çoğalt** ' a tıklayın.
7. Azure > **sanallaştırılmamış/başka**bir **şekilde** seçin.

### <a name="set-up-the-configuration-server-to-discover-vms"></a>VM 'Leri bulacak yapılandırma sunucusunu ayarlayın.


Yapılandırma sunucusunu ayarlayın, kasaya kaydedin ve VM 'Leri bulun.

1. Altyapıyı > **kaynağı** **hazırlamak** > **Site Recovery** ' ye tıklayın.
2. Bir yapılandırma sunucunuz yoksa **+ yapılandırma sunucusu**' na tıklayın.
3. **Sunucu Ekle**' de, **sunucu türü**' nde **Configuration Server** ' ın göründüğünden emin olun.
4. Site Recovery Birleşik kurulum yükleme dosyasını indirin.
5. Kasa kayıt anahtarını indirin. Birleşik kurulumu çalıştırdığınızda buna ihtiyacınız vardır. Anahtar, oluşturulduktan sonra beş gün boyunca geçerlidir.

   ![Kaynağı ayarlama](./media/physical-azure-disaster-recovery/source-environment.png)


### <a name="register-the-configuration-server-in-the-vault"></a>Yapılandırma sunucusunu kasaya kaydetme

Başlamadan önce aşağıdakileri yapın: 

#### <a name="verify-time-accuracy"></a>Zaman doğruluğunu doğrulama
Yapılandırma sunucusu makinesinde, sistem saatinin bir [saat sunucusuyla](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service)eşitlendiğinden emin olun. Eşleşmelidir. Ön veya arka planda 15 dakika ise, kurulum başarısız olabilir.

#### <a name="verify-connectivity"></a>Bağlantıyı doğrulama
Makinenin ortamınıza bağlı olarak bu URL 'Lere erişebildiğinizden emin olun: 

[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]  

IP adresi tabanlı güvenlik duvarı kuralları, HTTPS (443) bağlantı noktası üzerinden yukarıda listelenen tüm Azure URL 'Leriyle iletişime izin verir. IP aralıklarını basitleştirmek ve sınırlamak için, URL filtrelemesinin yapılması önerilir.

- **Ticari IP 'ler** - [Azure veri merkezi IP aralıklarına](https://www.microsoft.com/download/confirmation.aspx?id=41653)ve https (443) bağlantı noktasına izin verin. Aboneliğinizin Azure bölgesi için IP adresi aralıklarının AAD, yedekleme, çoğaltma ve depolama URL 'Lerini desteklemesini sağlar.  
- **Kamu IP 'leri** - [Azure Kamu veri merkezi IP aralıklarına](https://www.microsoft.com/en-us/download/details.aspx?id=57063)ve tüm Usgov bölgeleri (Virginia, Texas, Arizlona ve ıOWA) ait https (443) bağlantı noktasının AAD, yedekleme, çoğaltma ve depolama URL 'lerini desteklemesini sağlar.  

#### <a name="run-setup"></a>Kurulumu çalıştırma
Yapılandırma sunucusunu yüklemek için Birleşik kurulumu yerel yönetici olarak çalıştırın. İşlem sunucusu ve ana hedef sunucu, yapılandırma sunucusunda varsayılan olarak da yüklenir.

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

Kayıt tamamlandıktan sonra, yapılandırma sunucusu kasadaki **ayarlar** > **sunucular** sayfasında görüntülenir.

### <a name="configure-target-settings-for-replication"></a>Çoğaltma için hedef ayarlarını yapılandırma

Hedef kaynaklarını seçin ve doğrulayın.

1. **Altyapıyı hazırlama** > **Hedef** seçeneklerine tıklayıp kullanmak istediğiniz Azure aboneliğini seçin.
2. Hedef dağıtım modelini belirtin.
3. Site Recovery, bir veya birden çok uyumlu Azure depolama hesabınızın ve ağınızın olup olmadığını denetler.

   ![Hedef](./media/physical-azure-disaster-recovery/network-storage.png)


### <a name="create-a-replication-policy"></a>Çoğaltma ilkesi oluşturma

1. Yeni bir çoğaltma ilkesi oluşturmak için **Site Recovery altyapısı** > **Çoğaltma İlkeleri** >  **+Çoğaltma İlkesi**’ne tıklayın.
2. **Çoğaltma ilkesi oluştur** bölümünde bir ilke adı belirtin.
3. **RPO eşiği** bölümünde kurtarma noktası hedefi (RPO) sınırını belirtin. Bu değer, veri kurtarma noktalarının ne sıklıkta oluşturulacağını belirtir. Devamlı çoğaltma bu sınırı aşarsa bir uyarı oluşturulur.
4. **Kurtarma noktası bekletme** bölümünde, her kurtarma noktası için bekletme süresinin ne kadar olacağını (saat) belirtin. Çoğaltılan VM’ler bir aralıktaki herhangi bir noktaya kurtarılabilir. Premium depolama alanına çoğaltılan makineler için 24 saate, standart depolama için de 72 saate kadar bekletme desteklenir.
5. Uygulamayla **tutarlı anlık görüntü sıklığı**' nda, uygulamayla tutarlı anlık görüntüler içeren kurtarma noktalarının ne sıklıkta oluşturulacağını (dakika cinsinden) belirtin. İlkeyi oluşturmak için **Tamam**’a tıklayın.

    ![Çoğaltma ilkesi](./media/physical-azure-disaster-recovery/replication-policy.png)


İlke, yapılandırma sunucusu ile otomatik olarak ilişkilendirilir. Eşleşen bir ilke, varsayılan olarak yeniden çalışma için otomatik oluşturulur. Örneğin, çoğaltma ilkesi **rep-Policy** ise, bir yeniden çalışma ilkesi olan **rep-Policy-yeniden çalışma** oluşturulur. Bu ilke Azure’dan bir yeniden çalışma başlatılana kadar kullanılmaz.

### <a name="enable-replication"></a>Çoğaltmayı etkinleştirme

- Site Recovery, çoğaltma etkinleştirildiğinde Mobility hizmetini yükleyecek.
- Bir sunucu için çoğaltmayı etkinleştirdiğinizde, değişikliklerin etkili olması 15 dakika veya daha uzun sürebilir ve portalda görüntülenir.

1. **Uygulama çoğaltma** > **Kaynak** seçeneğine tıklayın.
2. **Kaynak** bölümünde yapılandırma sunucusunu seçin.
3. **Makine türü**' nde **fiziksel makineler**' i seçin.
4. İşlem sunucusunu (yapılandırma sunucusu) seçin. Daha sonra, **Tamam**'a tıklayın.
5. **Hedef**bölümünde, yük devretmeden sonra Azure VM 'leri oluşturmak istediğiniz aboneliği ve kaynak grubunu seçin. Azure 'da kullanmak istediğiniz dağıtım modelini (klasik veya kaynak yönetimi) seçin.
6. Verileri çoğaltmak için kullanmak istediğiniz Azure depolama hesabını seçin. 
7. Yük devretme sonrasında oluşturulan Azure VM'lerinin bağlanacağı Azure ağını ve alt ağını seçin.
8. Koruma için seçtiğiniz tüm makinelere ağ ayarını uygulamak için **Seçili makineler için şimdi yapılandır**’ı seçin. Makineler için Azure ağını ayrı ayrı seçmek için **Daha sonra yapılandır**'ı seçin. 
9. **Fiziksel makinelerde**ve **+ fiziksel makine**' ye tıklayın. Adı ve IP adresini belirtin. Çoğaltmak istediğiniz makinenin işletim sistemini seçin. Sunucuların bulunması ve listelenmesi birkaç dakika sürer. 

   > [!WARNING]
   > Taşımak istediğiniz Azure sanal makinesinin IP adresini girmeniz gerekir

10. Özellikler ** > ** Özellikler **yapılandırma**' da, Mobility hizmetini makineye otomatik olarak yüklemek için işlem sunucusu tarafından kullanılacak hesabı seçin.
11. **Çoğaltma ayarları** > **Çoğaltma ayarlarını yapılandırma** bölümünde doğru çoğaltma ilkesinin seçilip seçilmediğini doğrulayın. 
12. **Çoğaltmayı Etkinleştir**’e tıklayın. **Ayarlar**İşler > Site Recovery İşleri >  bölümünden **Korumayı Etkinleştir** işinin ilerleme durumunu izleyebilirsiniz. **Korumayı Sonlandır** işi çalıştırıldıktan sonra makine yük devretme için hazırdır.


Eklediğiniz sunucuları izlemek için, **yapılandırma sunucularındaki** son keşfedilen zamanı, en **son iletişim** > kontrol edebilirsiniz. Zamanlanan bulma süresini beklemeden makineler eklemek için yapılandırma sunucusunu vurgulayın (tıklamayın) ve **Yenile**' ye tıklayın.

## <a name="test-the-configuration"></a>Yapılandırmayı test etme


1. Kasaya gidin, **ayarlar** > **çoğaltılan öğeler**' de hedef bölgeye taşımak istediğiniz sanal makineye tıklayın, **+ Test yük devretmesi** simgesine tıklayın.
2. **Yük Devretme Testi** bölümünde, yük devretmede kullanılması için bir kurtarma noktası seçin:

   - **En son işlenen**: VM’nin yükünü, Site Recovery hizmeti tarafından işlenen en son kurtarma noktasına devreder. Zaman damgası gösterilir. Bu seçenekle veri işlemeye zaman harcanmadığından düşük RTO sağlanılır (Kurtarma Süresi Hedefi)
   - **Uygulamayla tutarlı olan son**: Bu seçenek, tüm VM’lerin yükünü uygulamayla tutarlı olan en son kurtarma noktasına devreder. Zaman damgası gösterilir.
   - **Özel**: Herhangi bir kurtarma noktası seçin.

3. Yapılandırmayı sınamak için Azure VM 'lerini taşımak istediğiniz hedef Azure sanal ağını seçin. 

   > [!IMPORTANT]
   > Yük devretme testi için ayrı bir Azure VM ağı kullanmanızı öneririz. bu sayede, sanal makinelerinizi, çoğaltmayı etkinleştirdiğinizde ayarlanmış olan VM 'lerinizi taşımak istediğiniz üretim ağına göre değil.

4. Taşımayı test etmeye başlamak için **Tamam**' a tıklayın. İlerleme durumunu izlemek için, VM’ye tıklayarak özelliklerini açın. Ya da kasa adı > **Ayarlar**İşler > Site Recovery işleri >  bölümünde **Yük Devretme Testi** işine tıklayabilirsiniz.
5. Yük devretme bittikten sonra, çoğaltma Azure VM, Azure portalı > **Sanal Makineler** bölümünde görünür. VM’nin çalıştığından, uygun şekilde boyutlandırıldığından ve uygun ağa bağlı olduğundan emin olun.
6. Taşımayı test etme işleminin bir parçası olarak oluşturulan VM 'yi silmek istiyorsanız, çoğaltılan öğede **Yük devretme testini temizle** ' ye tıklayın. **Notlar**' da, testle ilişkili tüm gözlemlerinizi kaydedin ve kaydedin.

## <a name="perform-the-move-to-the-target-region-and-confirm"></a>Hedef bölgeye taşıma işlemini gerçekleştirin ve onaylayın.

1. Kasaya gidin, **ayarlar** > **çoğaltılan öğeler**' de sanal makineye tıklayın ve ardından **Yük devretme**' ye tıklayın.
2. **Yük devretme** bölümünde **En geç** seçeneğini belirleyin. 
3. **Yük devretmeyi başlatmadan önce makineyi kapatın** seçeneğini belirleyin. Site Recovery, yük devretmeyi tetiklemeden önce kaynak sanal makineyi kapatmaya çalışır. Kapatma işlemi başarısız olsa bile yük devretme devam eder. Yük devretme işlemini **İşler** sayfasında takip edebilirsiniz. 
4. İş tamamlandıktan sonra, sanal makinenin hedef Azure bölgesinde beklenen şekilde göründüğünden emin olun.
5. **Çoğaltılan öğeler** bölümünde VM’ye sağ tıklayıp **Yürüt**’e tıklayın. Bu, hedef bölgeye taşıma işlemini tamamlar. Tamamlama işi tamamlanana kadar bekleyin.

## <a name="discard-the-resource-in-the-source-region"></a>Kaynak bölgedeki kaynağı at 

- VM 'ye gidin.  **Çoğaltmayı devre dışı bırak**' a tıklayın.  Bu, VM için verileri kopyalama işlemini sonlandırır.  

   > [!IMPORTANT]
   > ASR çoğaltması için ücretlendirilmemek üzere bu adımın yerine alınması önemlidir.

Kaynak kaynaklarından herhangi birini yeniden kullanmak için herhangi bir planınız yoksa, lütfen bir sonraki adım kümesiyle devam edin.

1. [Kaynak VM 'Leri hazırlama](#prepare-the-source-vms) bölümünde 4. adım kapsamında listelenen kaynak bölgedeki tüm ilgili ağ kaynaklarını silmeye devam edin 
2. Kaynak bölgedeki karşılık gelen depolama hesabını silin.



## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide bir Azure VM 'yi farklı bir Azure bölgesine taşıdınız. Artık taşınan VM için olağanüstü durum kurtarmayı yapılandırabilirsiniz.

> [!div class="nextstepaction"]
> [Geçişten sonra olağanüstü durum kurtarmayı ayarlama](azure-to-azure-quickstart.md)
