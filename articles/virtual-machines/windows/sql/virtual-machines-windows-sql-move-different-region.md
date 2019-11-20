---
title: Sanal makineyi başka bir bölgeye taşı (Azure Site Recovery)
description: SQL Server sanal makinenizi Azure 'da bir bölgeden diğerine nasıl geçirebileceğinizi öğrenin.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 07/30/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 3b84119cdcc1bb7e8603de64e3d23c69dac70cc3
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74022302"
---
# <a name="move-sql-server-vm-to-another-region-within-azure-with-azure-site-recovery-services"></a>SQL Server VM Azure Site Recovery hizmetleriyle Azure 'daki başka bir bölgeye taşıma

Bu makalede, SQL Server sanal makinenizi (VM) Azure 'da bir bölgeden diğerine geçirmek için Azure Site Recovery kullanma öğretilir. 

SQL Server VM farklı bir bölgeye taşımak şunları gerektirir:
1. [**Hazırlanıyor**](#prepare-to-move): hem kaynak SQL Server VM hem de hedef bölgenizin taşıma için yeterli hazır olduğunu onaylayın. 
1. [**Yapılandırma**](#configure-azure-site-recovery-vault): SQL Server VM taşımak, Azure Site Recovery kasasında çoğaltılan bir nesne olmasını gerektirir. SQL Server VM Azure Site Recovery kasasına eklemeniz gerekir. 
1. [**Test**](#test-move-process): SQL Server VM geçişi, kaynak bölgeden çoğaltılan hedef bölgeye yük devretmesini gerektirir. Taşıma işleminin başarılı olduğundan emin olmak için öncelikle SQL Server VM hedef bölgeye başarıyla yük devredebileceğinizi test etmeniz gerekir. Bu, tüm sorunları ortaya çıkaran ve gerçek taşıma gerçekleştirirken bunları önlemenize yardımcı olur. 
1. [**Taşıma**](#move-the-sql-server-vm): test yük devretmesi başarılı olduktan sonra, SQL Server VM geçirmek için güvenli olduğunuzu öğrendikten sonra sanal makinenin hedef bölgeye taşınmasını sağlayabilirsiniz. 
1. [**Temizleniyor**](#clean-up-source-resources): Faturalandırma ücretlerinden kaçınmak için, kasadan SQL Server VM ve kaynak grubunda kalan gereksiz kaynakları kaldırın. 

## <a name="verify-prerequisites"></a>Önkoşulları doğrulama 

- Kaynak bölgeinizden hedef bölgenize geçiş [desteklendiğinden](../../../site-recovery/azure-to-azure-support-matrix.md#region-support)emin olun.  
- [Senaryo mimarisini ve bileşenlerini](../../../site-recovery/azure-to-azure-architecture.md) , ayrıca [destek sınırlamalarını ve gereksinimlerini](../../../site-recovery/azure-to-azure-support-matrix.md)gözden geçirin. 
- Hesap izinlerini doğrulayın. Ücretsiz Azure hesabınızı oluşturduysanız aboneliğinizin yöneticisi olursunuz. Abonelik yöneticisi değilseniz, ihtiyaç duyduğunuz izinleri atamak için yöneticiyle birlikte çalışın. Bir VM için çoğaltmayı etkinleştirmek ve Azure Site Recovery kullanarak verileri kopyalamak için şunları yapmanız gerekir: 
    - VM oluşturma izinleri. *Sanal makine katılımcısı* yerleşik rolü bu izinlere sahiptir ve şunları içerir: 
        - Seçili kaynak grubunda VM oluşturma izinleri. 
        - Seçilen sanal ağda sanal makıne oluşturma izinleri. 
        - Seçilen depolama hesabına yazma izinleri. 
      - Azure Site Recovery işlemlerini yönetme izinleri. *Site Recovery katkıda* bulunan rolü, bir kurtarma hizmetleri kasasındaki Site Recovery işlemlerini yönetmek için gereken tüm izinlere sahiptir.  

## <a name="prepare-to-move"></a>Taşımaya hazırlanma
Taşıma için hem kaynak SQL Server VM hem de hedef bölgeyi hazırlayın. 

### <a name="prepare-the-source-sql-server-vm"></a>Kaynak SQL Server VM hazırlama

- En son kök sertifikaların, taşımak istediğiniz SQL Server VM olduğundan emin olun. En son kök sertifikalar orada yoksa, güvenlik kısıtlamaları hedef bölgeye veri kopyalamayı engeller. 
- Windows VM 'Leri için, tüm güvenilen kök sertifikaların makinede olması için, tüm en son Windows güncelleştirmelerini VM 'ye yükler. Bağlantısı kesilmiş bir ortamda, kuruluşunuz için standart Windows UPdate ve sertifika güncelleştirme işlemini izleyin. 
- Linux sanal makineleri için, VM 'deki en son güvenilen kök sertifikaları ve sertifika iptal listesini almak için Linux dağıtıcısının sunduğu yönergeleri izleyin. 
- Taşımak istediğiniz VM 'Ler için ağ bağlantısını denetlemek üzere bir kimlik doğrulama proxy 'si kullanmadığınız emin olun. 
- Taşımaya çalıştığınız sanal makinenin internet erişimi yoksa veya giden erişimi denetlemek için bir güvenlik duvarı ara sunucusu kullanıyorsa, gereksinimleri kontrol edin. 
- Kaynak ağ düzeni ve şu anda kullanmakta olduğunuz tüm kaynakları belirler. Bu, yük dengeleyiciler, ağ güvenlik grupları (NSG 'Ler) ve genel IP 'Leri içerir ancak bunlarla sınırlı değildir. 

### <a name="prepare-the-target-region"></a>Hedef bölgeyi hazırlama

- Azure aboneliğinizin, olağanüstü durum kurtarma için kullanılan hedef bölgede VM 'Ler oluşturmanıza izin verdiğini doğrulayın. Gerekli kotayı sağlamak için desteğe başvurun. 
- Aboneliğinizin, kaynak VM 'larınızla eşleşen boyutlu VM 'Leri desteklemek için yeterli kaynağa sahip olduğundan emin olun. Verileri hedefe kopyalamak için Site Recovery kullanıyorsanız, Site Recovery hedef VM için aynı boyutu veya mümkün olan en yakın boyutu seçer. 
- Kaynak ağ düzeninde tanımlanan her bileşen için bir hedef kaynak oluşturduğunuzdan emin olun. Bu adım, sanal makinelerinizin kaynak bölgede sahip olduğunuz hedef bölgedeki tüm işlevsellik ve özelliklere sahip olduğundan emin olmak için önemlidir. 
    - Azure Site Recovery, kaynak VM için çoğaltmayı etkinleştirdiğinizde otomatik olarak bir sanal ağ bulur ve oluşturur. Ayrıca, bir ağı önceden oluşturup çoğaltmayı etkinleştirmek için Kullanıcı akışındaki sanal makineye atayabilirsiniz. Hedef bölgede diğer kaynakları el ile oluşturmanız gerekir.
- Kaynak VM yapılandırmasına bağlı olarak sizin için uygun olan en sık kullanılan ağ kaynaklarını oluşturmak için aşağıdaki belgelere bakın: 
    - [Ağ güvenlik grupları](../../../virtual-network/tutorial-filter-network-traffic.md) 
    - [Yük Dengeleyici](../../../load-balancer/tutorial-load-balancer-basic-internal-portal.md)
    - [Genel IP adresi](../../../virtual-network/virtual-network-public-ip-address.md)
    - Tüm ek ağ bileşenleri için [ağ belgelerine](../../../virtual-network/virtual-networks-overview.md)bakın.
- Hedef bölgede son taşıma işlemini gerçekleştirmeden önce yapılandırmayı test etmek istiyorsanız, hedef bölgede el ile bir üretim dışı ağ oluşturun. Üretim ağıyla en az girişim sağladığından bu adımı öneririz. 

## <a name="configure-azure-site-recovery-vault"></a>Azure Site Recovery kasasını yapılandırma

Aşağıdaki adımlarda, verileri hedef bölgeye kopyalamak için Azure Site Recovery nasıl kullanılacağı gösterilmektedir. Kurtarma Hizmetleri kasasını kaynak bölgesi dışında herhangi bir bölgede oluşturun. 

1. [Azure portalında](https://portal.azure.com) oturum açın. 
1. Gezinti bölmesinin sol üst köşesinden **bir kaynak oluşturmayı** seçin. 
1. **Yönetim araçları &** seçin ve ardından **Yedekle ve Site Recovery '** ı seçin. 
1. **Temel bilgiler** sekmesinde, **proje ayrıntıları**' nın altında, hedef bölgede yeni bir kaynak grubu oluşturun veya hedef bölgede var olan bir kaynak grubunu seçin. 
1. **Örnek ayrıntıları**' nın altında, kasanızın adını belirtin ve ardından açılan listeden hedef **bölgenizi** seçin. 
1. Kurtarma Hizmetleri kasanızı oluşturmak için **gözden geçir + oluştur** ' u seçin. 
1. Gezinti bölmesinin sol üst köşesindeki **tüm hizmetler** ' i seçin ve arama kutusuna `recovery services`yazın. 
1. I **Kurtarma Hizmetleri kasaları** ' nın yanındaki yıldızı seçerek hızlı gezinti çubuğunuza ekleyin. 
1. **Kurtarma Hizmetleri kasaları** ' nı seçin ve ardından oluşturduğunuz kurtarma hizmetleri kasasını seçin. 
1. **Genel bakış** bölmesinde **Çoğalt**' ı seçin. 

   ![Çoğaltmayı yapılandırma](media/virtual-machines-windows-sql-move-to-new-region/configure-replication.png)

1. **Kaynak** ' ı seçin ve kaynak olarak **Azure** ' u seçin. Kaynak sanal makinelerinize ilişkin konum gibi diğer açılan alanlar için uygun değerleri seçin. Yalnızca **kaynak konum** bölgesinde bulunan kaynak grupları **kaynak kaynak grubu** alanında görünür olur. 
1. **Sanal makineler** ' i seçin ve ardından geçirmek istediğiniz sanal makineleri seçin. VM seçiminizi kaydetmek için **Tamam ' ı** seçin. 
1. **Ayarlar**' ı seçin ve ardından açılır listeden **hedef konumunuzu** seçin. Bu, daha önce hazırladığınız kaynak grubu olmalıdır. 
1. Çoğaltmayı özelleştirdikten sonra, yeni konumdaki kaynakları oluşturmak için **hedef kaynakları oluştur** ' u seçin. 
1. Kaynak oluşturma işlemi tamamlandıktan sonra, SQL Server VM kaynaktan hedef bölgeye çoğaltmasını başlatmak için **çoğaltmayı etkinleştir** ' i seçin.
1. Kurtarma kasanıza giderek, **çoğaltılan öğeler** ' i seçerek ve SQL Server VM **durumunu** görüntüleyerek çoğaltmanın durumunu kontrol edebilirsiniz. **Korunan** durum çoğaltmanın tamamlandığını gösterir. 

   ![Çoğaltma durumunu doğrulama](media/virtual-machines-windows-sql-move-to-new-region/check-replication-status.png)

## <a name="test-move-process"></a>Test taşıma işlemi
Aşağıdaki adımlarda, taşıma işlemini test etmek için Azure Site Recovery nasıl kullanılacağı gösterilmektedir. 

1. [Azure Portal](https://portal.azure.com) **Kurtarma Hizmetleri kasanıza** gidin ve **çoğaltılan öğeler**' i seçin. 
1. Taşımak istediğiniz SQL Server VM seçin, **çoğaltma durumunun** **sağlıklı** olarak göründüğünü doğrulayın ve ardından **Yük devretmeyi sına**' yı seçin. 

   ![VM 'niz için yük devretme testi](media/virtual-machines-windows-sql-move-to-new-region/test-failover-of-replicated-vm.png)

1. **Yük devretme testi** sayfasında, yük devretme Için kullanılacak **en son uygulamayla tutarlı** kurtarma noktasını seçin. Bu, SQL Server veri tutarlılığını garanti eden tek bir anlık görüntü türüdür. 
1. **Azure sanal ağ** altında sanal ağı seçin ve ardından yük devretmeyi sınamak için **Tamam** ' ı seçin. 
   
   >[!IMPORTANT]
   > Yük devretme testi için ayrı bir Azure VM ağı kullanmanızı öneririz. Çoğaltmayı etkinleştirdiğinizde ayarlanan üretim ağını kullanmayın ve sanal makinelerinizi son olarak taşımak istiyorsunuz. 

1. İlerlemeyi izlemek için kasanıza gidin, **izleme**altında **Site Recovery işler** ' i seçin ve ardından devam eden **Test yük devretmesi** işini seçin.

   ![Yük devretme testi ilerlemesini izleme](media/virtual-machines-windows-sql-move-to-new-region/monitor-failover-test-job.png)

1. Test tamamlandıktan sonra portalda **sanal makineler** ' e gidin ve yeni oluşturulan sanal makineyi gözden geçirin. SQL Server VM çalıştığından ve uygun ağa bağlı olduğundan emin olun. 
1. Yük devretme testi kaynakları temizlenene kadar **Yük devretme** seçeneği gri olacağı için testin bir parçası olarak oluşturulan VM 'yi silin. Kasaya geri dönün, **çoğaltılan öğeler**' i seçin, SQL Server VM seçin ve sonra **Test yük devretmesini temizle**' yi seçin. **Notlar** bölümündeki sınamalarla ilişkili tüm gözlemlerinizi kaydedin ve kaydedin ve test et ' in yanındaki onay kutusunu seçin **. Yük devretme testi sanal makinelerini silin**. Testten sonra kaynakları temizlemek için **Tamam ' ı** seçin. 

   ![Yük devretme testinin ardından öğeleri Temizle](media/virtual-machines-windows-sql-move-to-new-region/cleanup-test-items.png)

## <a name="move-the-sql-server-vm"></a>SQL Server VM taşıyın 
Aşağıdaki adımlarda SQL Server VM kaynak bölgeinizden hedef bölgenize nasıl taşıyacağınız gösterilmektedir. 

1. **Kurtarma Hizmetleri** kasasına gidin, **çoğaltılan öğeler**' i seçin, VM ' yi seçin ve ardından **Yük devretme**' yı seçin. 

   ![Yük devretmeyi Başlat](media/virtual-machines-windows-sql-move-to-new-region/initiate-failover.png)

1. **Kurtarma noktası**altında **uygulamayla tutarlı en son** kurtarma noktasını seçin. 
1. **Yük devretmeye başlamadan önce makineyi kapat**' ın yanındaki onay kutusunu işaretleyin. Site Recovery yük devretmeyi tetiklemeden önce kaynak VM 'yi kapatmayı deneyecek. Kapatma başarısız olsa bile yük devretme devam eder. 
1. Yük devretmeyi başlatmak için **Tamam ' ı** seçin.
1. Önceki bölümde yük devretme testini izlerken görüntülenen aynı **Site Recovery işleri** sayfasından yük devretme sürecini izleyebilirsiniz. 
1. İş tamamlandıktan sonra, SQL Server VM hedef bölgede beklenen şekilde göründüğünden emin olun. 
1. Kasaya geri dönün, **çoğaltılan öğeler**' i seçin, SQL Server VM seçin ve **Kaydet** ' i seçerek hedef bölgeye taşıma işlemini tamamlayın. Tamamlama işi bitene kadar bekleyin. 
1. Kaynak sağlayıcısıyla ilişkili Azure portal ve özelliklerde **SQL sanal makine** yönetilebilirliği sağlamak IÇIN SQL Server VM SQL VM kaynak sağlayıcısı ile kaydedin. Daha fazla bilgi için bkz. [SQL VM kaynak sağlayıcısı ile SQL Server VM kaydetme](virtual-machines-windows-sql-register-with-rp.md). 

  > [!WARNING]
  > SQL Server veri tutarlılığı yalnızca uygulamayla tutarlı anlık görüntülerle garanti edilir. Kilitlenme kurtarma anlık görüntüsü SQL Server veri tutarlılığını garanti edemediğinden, **en son işlenen** anlık görüntü SQL Server yük devretme için kullanılamaz. 

## <a name="clean-up-source-resources"></a>Kaynak kaynaklarını Temizleme
Faturalandırma ücretlerinden kaçınmak için SQL Server VM kasadan kaldırın ve gereksiz ilişkili kaynakları silin. 

1. **Site Recovery** kasasına geri gidin, **çoğaltılan öğeler**' i seçin ve SQL Server VM seçin. 
1. **Çoğaltmayı devre dışı bırak**seçeneğini belirleyin. Korumayı devre dışı bırakma nedeninizi seçin ve ardından çoğaltmayı devre dışı bırakmak için **Tamam** ' ı seçin. 

   >[!IMPORTANT]
   > Azure Site Recovery çoğaltma için ücretlendirilmeden kaçınmak için bu adımın yerine getirmeniz önemlidir. 

1. Kaynak bölgedeki herhangi bir kaynağı yeniden kullanmayı planlarsanız, tüm ilgili ağ kaynaklarını ve karşılık gelen depolama hesaplarını silin. 


## <a name="next-steps"></a>Sonraki adımlar

Daha fazla bilgi için aşağıdaki makalelere bakın: 

* [Windows VM 'de SQL Server genel bakış](virtual-machines-windows-sql-server-iaas-overview.md)
* [Windows VM 'de SQL Server SSS](virtual-machines-windows-sql-server-iaas-faq.md)
* [Windows VM fiyatlandırma kılavuzunda SQL Server](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Windows VM sürüm notlarında SQL Server](virtual-machines-windows-sql-server-iaas-release-notes.md)


