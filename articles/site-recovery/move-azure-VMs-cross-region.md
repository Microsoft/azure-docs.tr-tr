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
ms.openlocfilehash: 92388c54804d85d2825a25abd0c234081670e8d4
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74092177"
---
# <a name="move-azure-vms-to-another-azure-region"></a>Azure VM 'lerini başka bir Azure bölgesine taşıma

Güvenilirliği, kullanılabilirliği, yönetimi veya idare arttırmak için bir bölgeden diğerine Azure altyapı (IaaS) sanal makineleri taşımak isteyebilirsiniz. Bu öğreticide, Azure Site Recovery kullanarak sanal makineleri başka bir bölgeye nasıl taşıyacağınız gösterilmektedir. Şunları öğrenirsiniz:

> [!div class="checklist"]
> * Önkoşulları doğrulama
> * Kaynak VM 'Leri hazırlama
> * Hedef bölgeyi hazırlama
> * Hedef bölgeye veri kopyalama
> * Yapılandırmayı test etme
> * Taşımayı gerçekleştir
> * Kaynak bölgedeki kaynakları atın


> [!IMPORTANT]
> Bu makalede *,* Azure VM 'lerinin bir bölgeden diğerine nasıl taşınacağı açıklanır. Amacınız VM 'Leri kullanılabilirlik bölgelerine taşıyarak altyapınızın kullanılabilirliğini iyileştirmektir, bkz. [Azure VM 'lerini kullanılabilirlik alanları taşıma](move-azure-vms-avset-azone.md).

## <a name="prerequisites"></a>Önkoşullar

- *Geçiş yapmak*Istediğiniz kaynak Azure bölgesindeki Azure VM 'lerinin bulunduğundan emin olun.
- [Kaynak bölgesi hedef bölgesi bileşiminin desteklendiğinden](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support)emin olun ve hedef bölgeyi dikkatle seçin.
- [Senaryo mimarisini ve bileşenlerini ](azure-to-azure-architecture.md) anladığınızdan emin olun.
- [Destek sınırlamaları ve gereksinimleri](azure-to-azure-support-matrix.md) konusunu inceleyin.
- Hesap izinlerini doğrulayın. Ücretsiz Azure hesabınızı oluşturduysanız aboneliğinizin Yöneticisi *siz olursunuz* . Yönetici değilseniz, ihtiyacınız olan izinleri almak için yönetici ile çalışın:
  -  Bir VM için çoğaltmayı etkinleştirmek ve Site Recovery kullanarak verileri hedefe kopyalamak için, Azure kaynaklarınızda bir VM oluşturma izniniz olmalıdır. Sanal Makine Katılımcısı yerleşik rolü bu izinlere sahiptir. İzinlerle şunları yapabilirsiniz:
        - Seçilen kaynak grubunda sanal makine oluşturma.
        - Seçilen sanal ağda sanal makine oluşturma.
        - Seçilen depolama hesabına yazma.

  - Ayrıca Site Recovery işlemlerini yönetmek için izinlerinizin olması gerekir. Site Recovery katkıda bulunan rolü, bir Azure kurtarma hizmetleri kasasındaki Site Recovery işlemlerini yönetmek için gereken tüm izinlere sahiptir.

## <a name="prepare-the-source-vms"></a>Kaynak VM 'Leri hazırlama

1. Taşımayı planladığınız Azure VM 'lerinin en son kök sertifikalara sahip olup olmadığını denetleyin. Aksi takdirde, güvenlik kısıtlamaları nedeniyle hedef bölgeye veri kopyalamayı etkinleştiremezsiniz.

    - Windows VM 'lerinde, tüm güvenilen kök sertifikaların makinede olması için en son Windows güncelleştirmelerini yükler. Bağlantısı kesilmiş bir ortamda, kuruluşunuzun standart Windows Update ve sertifika güncelleştirme süreçlerini izleyin.
    - Linux sanal makineleri için, en son güvenilen kök sertifikaları ve sertifika iptal listesini almak için Linux dağıtıcısından gelen yönergeleri izleyin.
2. Taşımayı planladığınız VM 'Ler için ağ bağlantısını denetlemek üzere bir kimlik doğrulama proxy 'si kullanmadığınız emin olun.
3. Taşımak istediğiniz bir sanal makinenin internet erişimi yoksa ve giden erişimi denetlemek için bir güvenlik duvarı ara sunucusu kullanıyorsa, [gereksinimleri](azure-to-azure-tutorial-enable-replication.md#set-up-outbound-network-connectivity-for-vms)kontrol edin.
4. Yük dengeleyiciler, ağ güvenlik grupları ve doğrulama için genel IP adresleri dahil olmak üzere kaynak ağ düzeni ve şu anda kullanmakta olduğunuz tüm kaynakları belgeleyin.

## <a name="prepare-the-target-region"></a>Hedef bölgeyi hazırlama

1. Azure aboneliğinizde, olağanüstü durum kurtarma için kullanılan hedef bölgede VM 'Ler oluşturabileceğiniz doğrulayın. Gerekirse gerekli kotayı etkinleştirmek için desteğe başvurun.

2. Aboneliğinizin kaynak sanal makinelerinizi desteklemek için yeterli kaynağa sahip olduğundan emin olun. Verileri hedefe kopyalamak için Site Recovery kullanıyorsanız, hedef VM 'Ler için aynı boyut veya en yakın kullanılabilir boyutu seçer.

3. Kaynak ağ düzeninde tanımladığınız her bileşen için bir hedef kaynak oluşturduğunuzdan emin olun. Bu, sanal makinelerinizin kaynak bölgede sahip oldukları hedef bölgedeki tüm işlevsellik ve özelliklere sahip olmasını sağlar.

   Azure Site Recovery, kaynak VM için çoğaltmayı etkinleştirdiğinizde otomatik olarak bir sanal ağ ve depolama hesabı bulur ve oluşturur. Ayrıca, bu kaynakları önceden oluşturabilir ve etkinleştirme-çoğaltma adımının bir parçası olarak VM 'ye atayabilirsiniz. Ancak hedef bölgede diğer kaynakları el ile oluşturmanız gerekir. Kaynak VM yapılandırmanızı temel alarak en yaygın olarak kullanılan ağ kaynaklarını oluşturmak için aşağıdaki belgelere bakın:

   - [Ağ güvenlik grupları](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)
   - [Yük dengeleyiciler](https://docs.microsoft.com/azure/load-balancer)
   - [Genel IP](../virtual-network/virtual-network-public-ip-address.md)
    
   Diğer tüm ağ bileşenleri için bkz. [Azure ağ belgeleri](https://docs.microsoft.com/azure/#pivot=products&panel=network). 

4. Taşıma işlemini gerçekleştirmeden önce yapılandırmayı test etmek için, hedef bölgede el ile [Üretim dışı bir ağ oluşturun](https://docs.microsoft.com/azure/virtual-network/quick-create-portal) . Kurulumu test etmek, üretim ortamıyla en az girişim oluşturur ve bunu yapmanızı öneririz.
    
## <a name="copy-data-to-the-target-region"></a>Hedef bölgeye veri kopyalama
Aşağıdaki adımlar, verileri hedef bölgeye kopyalamak için Azure Site Recovery kullanır.

### <a name="create-the-vault-in-any-region-except-the-source"></a>Kaynak dışında herhangi bir bölgede kasa oluşturma

1. [Azure Portal](https://portal.azure.com) > **Kurtarma Hizmetleri**’nde oturum açın.
2. **Yedekleme ve Site Recovery** > **Yönetim Araçları** > **kaynak oluştur** ' u seçin.
3. **Ad**Için **contosovmkasası**kolay adını belirtin. Birden fazla aboneliğiniz varsa uygun olanı seçin.
4. Bir **ContosoRG** kaynak grubu oluşturun.
5. Bir Azure bölgesi belirtin. Desteklenen bölgeleri denetlemek için [Azure Site Recovery fiyatlandırma ayrıntıları](https://azure.microsoft.com/pricing/details/site-recovery/)bölümüne bakın.
6. Kurtarma Hizmetleri kasaları için **genel bakış** > **ConsotoVMVault** >  **+ Çoğalt**' ı seçin.
7. **Kaynak**için **Azure**' u seçin.
8. **Kaynak konumu**için sanal makinelerinizin çalışmakta olduğu kaynak Azure bölgesini seçin.
9. Azure Resource Manager dağıtım modelini seçin. Ardından, **kaynak aboneliği** ve **kaynak kaynak grubunu**seçin.
10. Ayarları kaydetmek için **Tamam ' ı** seçin.

### <a name="enable-replication-for-azure-vms-and-start-copying-the-data"></a>Azure VM 'Leri için çoğaltmayı etkinleştirme ve verileri kopyalamaya başlama

Site Recovery, abonelikle ve kaynak grubuyla ilişkili VM 'lerin listesini alır.

1. Taşımak istediğiniz VM 'yi seçin ve ardından **Tamam**' ı seçin.
2. **Ayarlar**Için **olağanüstü durum kurtarma**' yı seçin.
3. **Olağanüstü durum kurtarma** > **hedef bölge**yapılandırma için, çoğaltmakta olduğunuz hedef bölgeyi seçin.
4. Varsayılan hedef kaynaklarını veya önceden oluşturduğunuz kaynakları kullanmayı seçin.
5. İşi başlatmak için **çoğaltmayı etkinleştir** ' i seçin.

   ![Çoğaltmayı etkinleştirme](media/tutorial-migrate-azure-to-azure/settings.png)

 

## <a name="test-the-configuration"></a>Yapılandırmayı test etme


1. Kasaya gidin. **Çoğaltılan öğeleri** **Ayarlar** > , hedef bölgeye taşımak istediğiniz sanal makineyi seçin. Ardından **Yük devretmeyi test**' i seçin.
2. Yük **devretme testi**bölümünde, yük devretme için kullanılacak bir kurtarma noktası seçin:

   - **En son işlenen**: VM’nin yükünü, Site Recovery hizmeti tarafından işlenen en son kurtarma noktasına devreder. Zaman damgası gösterilir. Verileri işlemek için zaman harcanmadan Bu seçenek, düşük bir kurtarma süresi hedefi (RTO) sağlar.
   - **En son uygulamayla tutarlı**: tüm VM 'lere, uygulamayla tutarlı en son kurtarma noktasına devreder. Zaman damgası gösterilir.
   - **Özel**: Herhangi bir kurtarma noktası seçin.

3. Yapılandırmayı sınamak için Azure VM 'lerini taşımak istediğiniz hedef Azure sanal ağını seçin.

   > [!IMPORTANT]
   > Yük devretme testi için, hedef bölgedeki üretim ağını değil, ayrı bir Azure VM ağı kullanmanızı öneririz.

4. Taşımayı test etmeye başlamak için **Tamam**' ı seçin. İlerlemeyi izlemek için VM 'yi seçerek **özelliklerini açın.** Ya da kasada **Yük devretme testi** işini seçin. Ardından, **Site Recovery işler** > **Ayarlar** > **işler** ' i seçin.
5. Yük devretme bittikten sonra, çoğaltma Azure VM, Azure portalı > **Sanal Makineler** bölümünde görünür. VM’nin çalıştığından, uygun şekilde boyutlandırıldığından ve uygun ağa bağlı olduğundan emin olun.
6. Test için oluşturduğunuz VM 'yi silmek için çoğaltılan öğedeki **Yük devretme testini temizle** ' yi seçin. **Notlardan**, test ile ilgili tüm gözlemlerinizi kaydedin ve kaydedin.

## <a name="perform-the-move-and-confirm"></a>Taşımayı gerçekleştirme ve onaylama

1. **Ayarlar** > **çoğaltılan öğeler**' de kasaya gidin, sanal makineyi seçin ve ardından **Yük devretme**' yı seçin.
1. **Yük devretme**Için **en son**' u seçin. 
2. **Yük devretmeyi başlatmadan önce makineyi kapatın** seçeneğini belirleyin. Site Recovery yük devretmeyi tetiklemeden önce kaynak VM 'yi kapatmaya çalışır. Ancak, yük devretme, kapatılma başarısız olsa bile devam eder. Yük devretme işlemini **İşler** sayfasında takip edebilirsiniz.
3. İş bittiğinde, sanal makinenin hedef Azure bölgesinde beklenen şekilde göründüğünden emin olun.
4. **Çoğaltılan öğeler**' de sanal makineye sağ tıklayın ve **Yürüt**' ü seçin. Bu, taşımayı sonlandırır. Tamamlama işi bitene kadar bekleyin.

## <a name="discard-the-resources-from-the-source-region"></a>Kaynak bölgedeki kaynakları atın

- VM 'ye gidin ve **çoğaltmayı devre dışı bırak**' ı seçin. Bu, VM için verileri kopyalama işlemini sonlandırır.

  > [!IMPORTANT]
  > Taşıma işleminden sonra Site Recovery çoğaltma için ücretlendirmeden kaçınmak için bu adımı doldurun.

Kaynak kaynaklarından herhangi birini kullanmayı planlamıyorsanız, aşağıdaki adımları izleyin:

1. [Kaynak bölgede, kaynak VM 'Leri hazırlama](#prepare-the-source-vms)adım 4 ' te listelenen tüm ilgili ağ kaynaklarını silin.
2. Kaynak bölgedeki karşılık gelen depolama hesabını silin.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, Azure VM 'Leri farklı bir Azure bölgesine nasıl taşıyabileceğinizi öğrendiniz. Artık bu VM 'Ler için olağanüstü durum kurtarmayı yapılandırabilirsiniz.

> [!div class="nextstepaction"]
> [Geçişten sonra olağanüstü durum kurtarmayı ayarlama](azure-to-azure-quickstart.md)

