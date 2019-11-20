---
title: Azure Site Recovery ile Azure VM 'lerini başka bir bölgeye taşıma
description: Azure IaaS VM 'lerini bir Azure bölgesinden diğerine taşımak için Azure Site Recovery kullanın.
services: site-recovery
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: 00fc836f098f3c03afc9adebe2450f00750eb5ff
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73954097"
---
# <a name="move-azure-vms-to-another-region"></a>Azure VM’lerini başka bir bölgeye taşıma

Mevcut Azure IaaS sanal makinelerinizi (VM) bir bölgeden diğerine taşımak istediğiniz çeşitli senaryolar vardır. Örneğin, mevcut sanal makinelerinizin güvenilirliğini ve kullanılabilirliğini artırmak, yönetilebilirlik düzeyini artırmak veya idare nedenleriyle taşımak isteyebilirsiniz. Daha fazla bilgi için bkz. [Azure VM taşımaya genel bakış](azure-to-azure-move-overview.md). 

İş sürekliliği ve olağanüstü durum kurtarma (BCDR) için şirket içi makinelerin ve Azure VM 'lerinin olağanüstü durum kurtarma işlemlerini yönetmek ve düzenlemek için [Azure Site Recovery](site-recovery-overview.md) hizmetini kullanabilirsiniz. Azure VM 'lerinin ikincil bir bölgeye taşınmasını yönetmek için Site Recovery de kullanabilirsiniz.

Bu öğreticide şunları yapacaksınız:

> [!div class="checklist"]
> 
> * Taşıma için önkoşulları doğrulama
> * Kaynak VM 'Leri ve hedef bölgeyi hazırlama
> * Verileri kopyalama ve çoğaltmayı etkinleştirme
> * Yapılandırmayı test edin ve taşıma işlemini gerçekleştirin
> * Kaynak bölgedeki kaynakları Sil
> 
> [!NOTE]
> Bu öğreticide, Azure sanal makinelerini bir bölgeden diğerine nasıl taşıyacağınız gösterilmektedir. Bir kullanılabilirlik kümesindeki VM 'Leri farklı bir bölgedeki bölge sabitlenmiş VM 'lere taşıyarak kullanılabilirliği iyileştirmenize gerek varsa bkz. [Azure sanal makinelerini kullanılabilirlik alanları öğreticisine taşıma](move-azure-vms-avset-azone.md).

## <a name="prerequisites"></a>Önkoşullar

- Azure VM 'lerinin, taşımak istediğiniz Azure bölgesinde olduğundan emin olun.
- [Kaynak bölgesi hedef bölgesi bileşiminin desteklendiğinden](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support)emin olun ve hedef bölge hakkında bilinçli bir karar alın.
- [Senaryo mimarisini ve bileşenlerini ](azure-to-azure-architecture.md) anladığınızdan emin olun.
- [Destek sınırlamaları ve gereksinimleri](azure-to-azure-support-matrix.md) konusunu inceleyin.
- Hesap izinlerini doğrulayın. Ücretsiz Azure hesabınızı oluşturduysanız aboneliğinizin yöneticisi olursunuz. Abonelik yöneticisi değilseniz, ihtiyaç duyduğunuz izinleri atamak için yöneticiyle birlikte çalışın. Bir VM için çoğaltmayı etkinleştirmek ve temel olarak Azure Site Recovery kullanarak verileri kopyalamak için, şunları yapmanız gerekir:

    - Azure kaynaklarında sanal makine oluşturma izinleri. Sanal makine katılımcısı yerleşik rolü bu izinlere sahiptir ve şunları içerir:
    - Seçilen kaynak grubunda sanal makine oluşturma izni
    - Seçilen sanal ağda sanal makine oluşturma izni
    - Seçilen depolama hesabına yazma izni
    
    - Azure Site Recovery işlemlerini yönetme izinleri. Site Recovery katkıda bulunan rolü, bir kurtarma hizmetleri kasasındaki Site Recovery işlemlerini yönetmek için gereken tüm izinlere sahiptir.

- En son kök sertifikaların, taşımak istediğiniz Azure sanal makinelerinde bulunduğundan emin olun. En son kök sertifikalar VM 'de değilse, güvenlik kısıtlamaları hedef bölgeye veri kopyalamayı engeller.

- Windows VM’ler için, güvenilir kök sertifikaların tamamı makinede mevcut olacak şekilde sanal makineye en son Windows güncelleştirmelerinin tümünü yükleyin. Bağlantısı kesilmiş bir ortamda, kuruluşunuz için standart Windows Update ve sertifika güncelleştirme işlemlerini uygulayın.
    
- Linux sanal makineleri için, VM 'deki en son güvenilen kök sertifikaları ve sertifika iptal listesini almak için Linux dağıtıcısının sunduğu yönergeleri izleyin.
- Taşımak istediğiniz VM 'Ler için ağ bağlantısını denetlemek üzere bir kimlik doğrulama proxy 'si kullanmadığınız emin olun.

- Taşımaya çalıştığınız sanal makinenin internet erişimi yoksa veya giden erişimi denetlemek için bir güvenlik duvarı ara sunucusu kullanıyorsa, [gereksinimleri kontrol](azure-to-azure-tutorial-enable-replication.md#set-up-outbound-network-connectivity-for-vms)edin.

- Kaynak ağ düzeni ve şu anda kullanmakta olduğunuz tüm kaynakları belirler. Bu, yük dengeleyiciler, ağ güvenlik grupları (NSG 'Ler) ve genel IP 'Leri içerir ancak bunlarla sınırlı değildir.

- Azure aboneliğinizin, olağanüstü durum kurtarma için kullanılan hedef bölgede VM 'Ler oluşturmanıza izin verdiğini doğrulayın. Gerekli kotayı sağlamak için desteğe başvurun.

- Aboneliğinizin, kaynak VM 'larınızla eşleşen boyutlarda VM 'Leri desteklemek için yeterli kaynağa sahip olduğundan emin olun. Verileri hedefe kopyalamak için Site Recovery kullanıyorsanız, Site Recovery hedef VM için aynı boyutu veya mümkün olan en yakın boyutu seçer.

- Kaynak ağ düzeninde tanımlanan her bileşen için bir hedef kaynak oluşturduğunuzdan emin olun. Bu adım, sanal makinelerinizin kaynak bölgede sahip olduğunuz hedef bölgedeki tüm işlevsellik ve özelliklere sahip olduğundan emin olmak için önemlidir.

     > [!NOTE] 
     > Azure Site Recovery, kaynak VM için çoğaltmayı etkinleştirdiğinizde otomatik olarak bir sanal ağ bulur ve oluşturur. Ayrıca, çoğaltmayı etkinleştirmek için bir ağı önceden oluşturup Kullanıcı akışındaki sanal makineye atayabilirsiniz. Daha sonra bahsedildiği gibi, hedef bölgede diğer kaynakları el ile oluşturmanız gerekir.

    Kaynak VM yapılandırmasına bağlı olarak sizin için uygun olan en sık kullanılan ağ kaynaklarını oluşturmak için aşağıdaki belgelere bakın:
    - [Ağ güvenlik grupları](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)
    - [Yük dengeleyiciler](https://docs.microsoft.com/azure/load-balancer)
    -  [Genel IP](../virtual-network/virtual-network-public-ip-address.md)
    - Diğer ağ bileşenleri için [ağ belgelerine](https://docs.microsoft.com/azure/#pivot=products&panel=network)bakın.



## <a name="prepare"></a>Hazırlama
Aşağıdaki adımlarda, bir çözüm olarak Azure Site Recovery kullanarak sanal makinenin taşıma için nasıl hazırlanacağı gösterilmektedir. 

### <a name="create-the-vault-in-any-region-except-the-source-region"></a>Kaynak bölgesi dışında herhangi bir bölgede kasa oluşturma

1. [Azure Portal](https://portal.azure.com) > **Kurtarma Hizmetleri**’nde oturum açın.
1. **Yedekleme ve Site Recovery** > **Yönetim Araçları** > **kaynak oluştur** ' u seçin.
1. **Ad** bölümünde **ContosoVMVault** kolay adını belirtin. Birden fazla aboneliğiniz varsa uygun olanı seçin.
1. **ContosoRG**kaynak grubunu oluşturun.
1. Bir Azure bölgesi belirtin. Desteklenen bölgeleri denetlemek için [Azure Site Recovery fiyatlandırma ayrıntılarında](https://azure.microsoft.com/pricing/details/site-recovery/)coğrafi kullanılabilirlik bölümüne bakın.
1. **Kurtarma Hizmetleri kasalarında** **genel bakış** > **Contosovmkasa > ** **+ Çoğalt**' ı seçin.
1. **Kaynak** bölümünde **Azure** seçeneğini belirleyin.
1. **Kaynak konumu**’nda, VM’lerinizin çalışmakta olduğu kaynak Azure bölgesini seçin.
1. Kaynak Yöneticisi dağıtım modelini seçin. Ardından **kaynak aboneliği** ve **kaynak kaynak grubunu**seçin.
1. Ayarları kaydetmek için **Tamam ' ı** seçin.

### <a name="enable-replication-for-azure-vms-and-start-copying-the-data"></a>Azure VM 'Leri için çoğaltmayı etkinleştirme ve verileri kopyalamaya başlama

Site Recovery, abonelikle ve kaynak grubuyla ilişkili VM 'lerin listesini alır.

1. Sonraki adımda, taşımak istediğiniz VM 'yi seçin ve ardından **Tamam**' ı seçin.
1. **Ayarlar**' da, **olağanüstü durum kurtarma**' yı seçin.
1. **Olağanüstü durumdan kurtarma yapılandırma** > **Hedef bölge** bölümünde, çoğaltma yapacağınız hedef bölgeyi seçin.
1. Bu öğretici için diğer varsayılan ayarları kabul edin.
1. **Çoğaltmayı etkinleştir** seçeneğini belirleyin. Bu adım VM için çoğaltmayı etkinleştirmek üzere bir iş başlatır.

    ![Çoğaltmayı etkinleştirme](media/tutorial-migrate-azure-to-azure/settings.png)

## <a name="move"></a>Taşı

Aşağıdaki adımlarda, hedef bölgeye taşımanın nasıl gerçekleştirileceği gösterilmektedir.

1. Kasaya gidin. **Yinelenen öğeleri** > **Ayarlar** ' da, VM ' yi seçin ve ardından **Yük devretme**' yı seçin.
2. **Yük devretme** bölümünde **En geç** seçeneğini belirleyin.
3. **Yük devretmeyi başlatmadan önce makineyi kapatın** seçeneğini belirleyin. Site Recovery, yük devretmeyi tetiklemeden önce kaynak sanal makineyi kapatmaya çalışır. Kapatma işlemi başarısız olsa bile yük devretme devam eder. Yük devretme işlemini **İşler** sayfasında takip edebilirsiniz.
4. İş bittikten sonra, sanal makinenin hedef Azure bölgesinde beklenen şekilde göründüğünden emin olun.


## <a name="discard"></a>Mı 

Taşınan VM 'yi denetlediyseniz ve yük devretme noktası olarak değiştirilmesi veya önceki bir noktaya geri dönmek istiyorsanız, **çoğaltılan öğelerde**VM 'yi sağ seçin > **kurtarma noktasını değiştirin**. Bu adım size, farklı bir kurtarma noktası ve bunun için yük devretme belirtme seçeneği sağlar. 


## <a name="commit"></a>İşleme 

Taşınan sanal makineyi denetledikten ve değişikliği kaydetmeye hazırladıktan sonra, **çoğaltılan öğelerde**, VM > **Kaydet**' i sağ seçin. Bu adım, hedef bölgeye taşıma işlemini tamamlar. Tamamlama işi bitene kadar bekleyin.

## <a name="clean-up"></a>Temizleme

Aşağıdaki adımlar, kaynak bölgenin ve taşıma için kullanılan ilgili kaynakların nasıl temizleyene kılavuzluk eder.

Taşıma için kullanılan tüm kaynaklar için:

- VM 'ye gidin. **Çoğaltmayı devre dışı bırak**seçeneğini belirleyin. Bu adım, VM 'nin verileri kopyalama işlemini sonlandırır.

   > [!IMPORTANT]
   > Azure Site Recovery çoğaltma için ücretlendirilmeden kaçınmak için bu adımın yerine getirmeniz önemlidir.

Kaynak kaynaklarından herhangi birini yeniden kullanmak için herhangi bir planınız yoksa, şu ek adımları uygulayın:

1. [Ön](#prerequisites)koşullarda tanımladığınız kaynak bölgedeki tüm ilgili ağ kaynaklarını silin.
1. Kaynak bölgedeki karşılık gelen depolama hesabını silin.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, bir Azure VM 'yi farklı bir Azure bölgesine taşıdınız. Artık taşıdığınız VM için olağanüstü durum kurtarmayı yapılandırabilirsiniz.

> [!div class="nextstepaction"]
> [Geçişten sonra olağanüstü durum kurtarmayı ayarlama](azure-to-azure-quickstart.md)

