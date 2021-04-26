---
title: Site Recovery ile fiziksel sunucular için yük devretme ve yeniden çalışma ayarlama
description: Fiziksel sunucuların yükünü Azure 'a devretmek ve Azure Site Recovery ile olağanüstü durum kurtarma için şirket içi siteye geri dönme hakkında bilgi edinin
services: site-recovery
ms.service: site-recovery
ms.topic: article
ms.date: 12/17/2019
ms.openlocfilehash: 68427d264b6d2eea1a38e3b8bc5273409221c126
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106579912"
---
# <a name="fail-over-and-fail-back-physical-servers-replicated-to-azure"></a>Yük devretme ve geri dönme fiziksel sunucuları Azure 'a çoğaltma

Bu öğreticide, [Azure Site Recovery](site-recovery-overview.md)ile Azure 'a çoğaltılan şirket içi fiziksel sunucuların yük devretme işlemi açıklanmaktadır. Yük devretdikten sonra, kullanılabilir olduğunda Azure 'dan şirket içi sitenize geri dönebilirsiniz.

## <a name="before-you-start"></a>Başlamadan önce

- Olağanüstü durum kurtarma 'daki yük devretme süreci hakkında [bilgi edinin](failover-failback-overview.md) .
- Birden fazla makinenin yükünü devretmek istiyorsanız, bir kurtarma planında makineleri nasıl [toplayacağınızı öğrenin](recovery-plan-overview.md) .
- Tam yük devretme yapmadan önce, her şeyin beklendiği gibi çalıştığından emin olmak için bir [olağanüstü durum kurtarma detayına](site-recovery-test-failover-to-azure.md) sahip olun.
- Yük devretmeden sonra Azure VM 'lerine bağlanmaya hazırlanmak için [Bu yönergeleri](site-recovery-failover.md#prepare-to-connect-after-failover) izleyin.



## <a name="run-a-failover"></a>Yük devretme çalıştırma

### <a name="verify-server-properties"></a>Sunucu özelliklerini doğrulama

Sunucu özelliklerini doğrulayın ve Azure VM 'Leri için [Azure gereksinimleriyle](vmware-physical-azure-support-matrix.md#replicated-machines) uyumlu olduğundan emin olun.

1. **Korunan öğeler**' de **çoğaltılan öğeler**' e tıklayın ve makineyi seçin.
2. **Çoğaltılan öğe** bölmesinde makine bilgilerinin, sistem durumunun ve en son kullanılabilir kurtarma noktalarının bir özeti vardır. Daha fazla ayrıntı görüntülemek için **Özellikler**’e tıklayın.
3. **İşlem ve Ağ** bölümünde Azure adını, kaynak grubunu, hedef boyutunu, [kullanılabilirlik kümesini](../virtual-machines/windows/tutorial-availability-sets.md) ve yönetilen disk ayarlarını değiştirebilirsiniz
4. Ağ ayalarını, yük devretmeden sonra Azure VM’nin yerleştirileceği ağ/alt ağ ve VM’ye atanacak IP adresi dahil olmak üzere görüntüleyebilir ve değiştirebilirsiniz.
5. **Diskler**' de makine işletim sistemi ve veri diskleri hakkında bilgi görebilirsiniz.

### <a name="fail-over-to-azure"></a>Azure'a yük devretme

1. **Ayarlar**  >  **çoğaltılan öğeler** ' de **Yük devretme**> makine ' ye tıklayın.
2. **Yük devretme**’de yük devretmenin yapılacağı bir **Kurtarma Noktası** seçin. Aşağıdaki seçeneklerden birini kullanabilirsiniz:
   - **Varsayılan**: Bu seçenekte öncelikle Site Recovery’ye gönderilen tüm veriler işlenir. Yük devretmeden sonra oluşturulan Azure VM, yük devretme tetiklendiğinde Site Recovery’ye çoğaltılan tüm verilere sahip olduğundan en düşük RPO (Kurtarma Noktası Hedefi) sağlanır.
   - **En son işlenen**: Bu seçenek makineyi Site Recovery tarafından işlenen en son kurtarma noktasına devreder. İşlenmemiş verileri işlemek için zaman harcanmadığından bu seçenekte düşük bir RTO (Kurtarma Süresi Hedefi) sağlanır.
   - **En son uygulamayla tutarlı**: Bu seçenek, makineyi Site Recovery tarafından işlenen en son uygulamayla tutarlı kurtarma noktasına devreder.
   - **Özel**: Bir kurtarma noktası belirtin.

3. Yük devretmeyi tetiklemeden önce Site Recovery kaynak makineyi kapatmayı denemek istiyorsanız, **yük devretmeye başlamadan önce makineyi Kapat ' ı** seçin. Kapatma işlemi başarısız olsa bile yük devretme devam eder. **İşler** sayfasında yük devretme ilerlemesini izleyebilirsiniz.
4. Azure VM’ye bağlanmaya hazırsanız, yük devretmeden sonra VM’yi doğrulamak için bağlanın.
5. Doğruladıktan sonra yük devretmeyi **Yürütün**. Bu, tüm kullanılabilir kurtarma noktalarını siler.

> [!WARNING]
> Devam eden bir yük devretme işlemini iptal etmeyin. Yük devretme başlamadan önce makine çoğaltması durduruluyor. Yük devretmeyi iptal ederseniz, yanıt vermez, ancak makine tekrar çoğaltılmaz.
> Fiziksel sunucular için ek yük devretme işleminin tamamlanması yaklaşık sekiz ila on dakika sürebilir.

## <a name="automate-actions-during-failover"></a>Yük devretme sırasında eylemleri otomatikleştirme

Yük devretme sırasında eylemleri otomatikleştirmek isteyebilirsiniz. Bunu yapmak için kurtarma planlarında betikleri veya Azure Otomasyonu runbook 'larını kullanabilirsiniz.

- Betik ekleme dahil olmak üzere Kurtarma planlarını oluşturma ve özelleştirme hakkında [bilgi edinin](site-recovery-create-recovery-plans.md) .
- Kurtarma planlarına Azure Otomasyonu runbook 'ları eklemeyi [öğrenin](site-recovery-runbook-automation.md) .

## <a name="configure-settings-after-failover"></a>Yük devretmeden sonra ayarları Yapılandır

Yük devretmeden sonra, [Azure ayarlarını](site-recovery-failover.md#prepare-in-azure-to-connect-after-failover) çoğaltılan Azure VM 'lerine bağlanacak şekilde yapılandırmanız gerekir. Ayrıca, [iç ve genel](site-recovery-failover.md#set-up-ip-addressing) IP adresleme 'yi ayarlayın.

## <a name="prepare-for-reprotection-and-failback"></a>Yeniden koruma ve yeniden çalışma için hazırlanma

Azure 'a yük devrettikten sonra, Azure VM 'lerini şirket içi siteye çoğaltarak yeniden koruyabilirsiniz. Çoğaltma tamamlandıktan sonra, Azure 'dan şirket içi sitenize bir yük devretme çalıştırarak bunları şirket içinde geri alabilirsiniz.

1. Site Recovery kullanılarak Azure 'a çoğaltılan fiziksel sunucular yalnızca VMware VM 'Leri olarak yeniden çalışabilir. Yeniden yük devredebilmek için bir VMware altyapısına ihtiyacınız vardır. Azure 'da bir işlem sunucusu ve şirket içi ana hedef sunucusu ayarlama ve bir siteden siteye VPN veya ExpressRoute özel eşlemesini yeniden çalışma için yapılandırma dahil, yeniden koruma ve yeniden çalışma için hazırlanmak üzere [Bu makaledeki](vmware-azure-prepare-failback.md) adımları izleyin.
2. Şirket içi yapılandırma sunucusunun çalıştığından ve Azure 'a bağlı olduğundan emin olun. Azure 'a yük devretme sırasında şirket içi siteye erişilemeyebilir ve yapılandırma sunucusu kullanılamıyor olabilir veya kapatılabilir. Yeniden çalışma sırasında VM 'nin yapılandırma sunucusu veritabanında mevcut olması gerekir. Aksi takdirde, yeniden çalışma başarısız olur.
3. Şirket içi ana hedef sunucusundaki tüm anlık görüntüleri silin. Anlık görüntüler varsa yeniden koruma çalışmaz.  Yeniden koruma işi sırasında VM 'deki anlık görüntüler otomatik olarak birleştirilir.
4. Çoklu VM tutarlılığı için bir çoğaltma grubuna toplanan VM 'Leri yeniden koruyorsanız, tümünün aynı işletim sistemine (Windows veya Linux) sahip olduklarından emin olun ve dağıttığınız ana hedef sunucunun aynı işletim sistemi türüne sahip olduğundan emin olun. Bir çoğaltma grubundaki tüm VM 'Lerin aynı ana hedef sunucuyu kullanması gerekir.
5. Yeniden çalışma için [gereken bağlantı noktalarını](vmware-azure-prepare-failback.md#ports-for-reprotectionfailback) açın.
6. Yeniden çalışma öncesinde vCenter Server bağlı olduğundan emin olun. Aksi takdirde, disklerin bağlantısının kesilmesi ve sanal makineye geri eklenmesi başarısız olur.
7. Bir vCenter sunucusu, geri dönecek VM 'Leri yönetirse, gerekli izinlere sahip olduğunuzdan emin olun. Salt okuma Kullanıcı vCenter bulmayı ve sanal makineleri koruyorsa, koruma başarılı olur ve yük devretme işlemi gerçekleştirilir. Bununla birlikte, yeniden koruma sırasında, veri depoları keşfedilmeden ve yeniden koruma sırasında listelenmediği için yük devretme başarısız olur. Bu sorunu çözmek için vCenter kimlik bilgilerini [uygun bir hesap/izinlerle](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery)güncelleştirebilir ve ardından işi yeniden deneyebilirsiniz. 
8. Sanal makinelerinizi oluşturmak için bir şablon kullandıysanız, her VM 'nin diskler için kendi UUID 'sine sahip olduğundan emin olun. Aynı şablondan oluşturulduğundan, şirket içi VM UUID 'SI ana hedef sunucunun UUID 'SI ile çakışıyor, yeniden koruma başarısız olur. Farklı bir şablondan dağıtın.
9. Alternatif bir vCenter Server geri yüklüyorsanız, yeni vCenter Server ve ana hedef sunucunun bulunduğundan emin olun. Genellikle veri depoları erişilemez veya **yeniden koruma** bölümünde görünmez.
10. Geri gerçekleştiremeyecek aşağıdaki senaryoları doğrulayın:
    - ESXi 5,5 Free Edition ya da vSphere 6 hiper yönetici ücretsiz sürümü kullanıyorsanız. Farklı bir sürüme yükseltin.
    - Windows Server 2008 R2 SP1 fiziksel sunucunuz varsa.
    - Geçirilen VM 'Ler.
    - Başka bir kaynak grubuna taşınan bir VM.
    - Silinen bir çoğaltma Azure VM 'si.
    - Korunmayan bir çoğaltma Azure VM 'si (Şirket içi siteye çoğaltma).
10. Yeniden [çalışma türlerini gözden geçirin](concepts-types-of-failback.md) -özgün konum kurtarma ve alternatif konum kurtarma kullanabilirsiniz.


## <a name="reprotect-azure-vms-to-an-alternate-location"></a>Azure VM 'lerini alternatif bir konuma yeniden koruma

Bu yordam, şirket içi VM 'nin kullanılabilir olmadığını varsayar.

1. Kasa > **ayarları**  >  **çoğaltılan öğeler**' de, yük devredilecek makineye sağ tıklayın > **yeniden koruyun**.
2. **Yeniden koru** bölümünde **Azure’dan Şirket içine** seçeneğinin belirlendiğinden emin olun.
3. Şirket içi ana hedef sunucuyu ve işlem sunucusunu belirtin.
4. **Veri deposu**’nda şirket içi diskleri kurtarmak istediğiniz ana hedef veri deposunu seçin.
       - Şirket içi VM silinmişse veya yoksa ve yeni diskler oluşturmanız gerekiyorsa bu seçeneği kullanın.
       - Diskler zaten varsa, bu ayar yok sayılır, ancak bir değer belirtmeniz gerekir.
5. Ana hedef bekletme sürücüsünü seçin. Yeniden çalışma ilkesi otomatik olarak seçilir.
6. Yeniden korumaya başlamak için **Tamam**’a tıklayın. Bir iş, Azure VM 'yi şirket içi siteye çoğaltmaya başlar. **İşler** sekmesinde ilerleme durumunu izleyebilirsiniz.

> [!NOTE]
> Azure VM’sini mevcut bir şirket içi VM’ye kurtarmak istiyorsanız, şirket içi sanal makinenin veri deposunu okuma/yazma erişimiyle ana hedef sunucunun ESXi ana bilgisayarına bağlayın.


## <a name="fail-back-from-azure"></a>Azure’dan yeniden çalışma

Yük devretmeyi şu şekilde çalıştırın:

1. **Çoğaltılan Öğeler** sayfasında makineye sağ tıklayın ve **Planlanmamış Yük Devretme** seçeneğini belirleyin.
2. **Yük Devretmeyi Onayla** bölümünde yük devretme yönünün Azure’dan olduğundan emin olun.
3. yük devretme için kullanmak istediğiniz kurtarma noktasını seçin.
    - **En son** kurtarma noktasını kullanmanızı öneririz. Uygulamayla tutarlı nokta en son zaman noktası arkasında ve veri kaybına neden olur.
    - **En son** , kilitlenme ile tutarlı bir kurtarma noktasıdır.
    - Yük devretme çalıştığında Site Recovery, Azure VM’lerini kapatır ve şirket içi VM’yi başlatır. Hizmet bir süre kapalı kalacağından uygun bir zaman seçin.
4. Makineye sağ tıklayın ve **Yürüt**’e tıklayın. Bu, Azure VM’lerini kaldıran bir işi tetikler.
5. Azure VM’lerinin beklenen şekilde kapatıldığından emin olun.


## <a name="reprotect-on-premises-machines-to-azure"></a>Şirket içi makineleri Azure’da yeniden koruma

Veriler artık şirket içi sitenizde tekrar yerleştirilir, ancak Azure’a çoğaltılmaz. Tekrar Azure’a çoğaltmaya başlamak için şunları yapabilirsiniz:

1. Kasa > **Ayarlar** >**Çoğaltılan Öğeler** bölümünde yeniden çalışan VM’leri seçin ve **Yeniden Koru**’ya tıklayın.
2. Çoğaltılan verileri Azure’a göndermek için kullanılan işlem sunucusunu seçin ve **Tamam**’a tıklayın.


## <a name="next-steps"></a>Sonraki adımlar

Yeniden koruma işi tamamlandıktan sonra şirket içi VM, Azure 'a çoğaltılır. Gerektiğinde, Azure 'a [başka bir yük devretme](site-recovery-failover.md) işlemi de çalıştırabilirsiniz.
