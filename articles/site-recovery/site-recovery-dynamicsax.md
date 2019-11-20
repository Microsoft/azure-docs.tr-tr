---
title: Azure Site Recovery ile çok katmanlı Dynamics AX dağıtımı için olağanüstü durum kurtarma | Microsoft Docs
description: Bu makalede, Dynamics AX için Azure Site Recovery olağanüstü durum kurtarmanın nasıl ayarlanacağı açıklanır
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: asgang
ms.openlocfilehash: 5b8aaff3a3418177f92c3b54fb3bb3e99f93810e
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73620747"
---
# <a name="set-up-disaster-recovery-for-a-multitier-dynamics-ax-application"></a>Çok katmanlı Dynamics AX uygulaması için olağanüstü durum kurtarmayı ayarlama   




 Dynamics AX, kuruluşlar tarafından konumlar arasında işlem standartlaştırmak, kaynakları yönetmek ve uyumluluğu basitleştirmek için kullanılan en popüler ERP çözümlerinin biridir. Uygulama bir kuruluş için kritik olduğundan, bir olağanüstü durum durumunda uygulamanın en kısa sürede çalışıyor ve çalışır durumda olması gerekir.

Günümüzde Dynamics AX, kullanıma hazır bir olağanüstü durum kurtarma özelliği sağlamıyor. Dynamics AX, Windows uygulama nesne sunucusu, Azure Active Directory, Azure SQL veritabanı, SharePoint Server ve Raporlama Hizmetleri gibi birçok sunucu bileşeninden oluşur. Bu bileşenlerin her birinin olağanüstü durum kurtarmayı yönetmek için yalnızca pahalı değildir, ayrıca hataya açıktır.

Bu makalede, [Azure Site Recovery](site-recovery-overview.md)kullanarak Dynamics AX uygulamanız için nasıl olağanüstü durum kurtarma çözümü oluşturabileceğiniz açıklanmaktadır. Ayrıca tek tıklamayla kurtarma planı, desteklenen konfigürasyonlar ve Önkoşullar kullanılarak planlı/planlanmamış test yük devretmeleri de ele alınmaktadır.



## <a name="prerequisites"></a>Ön koşullar

Site Recovery kullanarak Dynamics AX uygulaması için olağanüstü durum kurtarmayı uygulamak aşağıdaki önkoşulları gerektirir:

• Şirket içi Dynamics AX dağıtımı kurun.

• Azure aboneliğinde bir Site Recovery Kasası oluşturun.

• Azure kurtarma siteniz ise VM 'lerde Azure sanal makine hazır olma durumu değerlendirmesi aracını çalıştırın. Azure sanal makineler ve Site Recovery hizmetleriyle uyumlu olmaları gerekir.

## <a name="site-recovery-support"></a>Site Recovery desteği

Bu makaleyi oluşturmak amacıyla, Windows Server 2012 R2 Enterprise üzerinde Dynamics AX 2012 R3 ile VMware sanal makinelerini kullandık. Site Recovery çoğaltması uygulamanın belirsiz olduğu için, burada aşağıdaki senaryolar için de sunulan önerilerin beklendiğini kabul ediyoruz.

### <a name="source-and-target"></a>Kaynak ve hedef

**Senaryo** | **İkincil bir siteye** | **Azure’a**
--- | --- | ---
**Hyper-V** | Evet | Evet
**VMware** | Evet | Evet
**Fiziksel sunucu** | Evet | Evet

## <a name="enable-disaster-recovery-of-the-dynamics-ax-application-by-using-site-recovery"></a>Site Recovery kullanarak Dynamics AX uygulamasının olağanüstü durum kurtarma özelliğini etkinleştirme
### <a name="protect-your-dynamics-ax-application"></a>Dynamics AX uygulamanızı koruyun
Tüm uygulama çoğaltmasını ve kurtarmayı etkinleştirmek için, Dynamics AX 'in her bir bileşeni korunmalıdır.

### <a name="1-set-up-active-directory-and-dns-replication"></a>1. Active Directory ve DNS çoğaltmasını ayarlama

Dynamics AX uygulamasının çalışması için olağanüstü durum kurtarma sitesinde Active Directory gereklidir. Müşterinin Şirket içi ortamının karmaşıklığına göre aşağıdaki iki seçeneği öneririz.

**Seçenek 1**

Müşterinin, tüm şirket içi site için az sayıda uygulama ve tek bir etki alanı denetleyicisi vardır ve tüm siteyi birlikte devretmek için plan yapın. Etki alanı denetleyicisi makinesini ikincil bir siteye çoğaltmak için Site Recovery çoğaltma kullanmanızı öneririz (hem siteden siteye ve siteden Azure senaryolarına yönelik senaryolar için geçerlidir).

**Seçenek 2**

Müşterinin çok sayıda uygulaması vardır ve bir Active Directory ormanı çalıştırıyor ve aynı anda birkaç uygulamanın yük devredebildiğini planlıyor. Olağanüstü durum kurtarma sitesinde (ikincil bir site veya Azure 'da) ek bir etki alanı denetleyicisi ayarlamanızı öneririz.

 Daha fazla bilgi için bkz. bir [etki alanı denetleyicisini olağanüstü durum kurtarma sitesinde kullanılabilir hale getirme](site-recovery-active-directory.md). Bu belgenin geri kalanında, bir etki alanı denetleyicisinin olağanüstü durum kurtarma sitesinde kullanılabilir olduğunu varsaytık.

### <a name="2-set-up-sql-server-replication"></a>2. SQL Server çoğaltma ayarla
SQL katmanını korumaya yönelik önerilen seçenek hakkında teknik yönergeler için bkz. [SQL Server ve Azure Site Recovery ile uygulamaları çoğaltma](site-recovery-sql.md).

### <a name="3-enable-protection-for-the-dynamics-ax-client-and-application-object-server-vms"></a>3. Dynamics AX İstemcisi ve uygulama nesne sunucusu VM 'Leri için korumayı etkinleştirme
Sanal makinelerin [Hyper-V](site-recovery-hyper-v-site-to-azure.md) veya [VMware](site-recovery-vmware-to-azure.md)üzerinde dağıtılmış olup olmadığına bağlı olarak ilgili Site Recovery yapılandırmasını gerçekleştirin.

> [!TIP]
> Kilitlenme tutarlılığı sıklığını 15 dakika olarak yapılandırmanızı öneririz.
>

Aşağıdaki anlık görüntüde, bir VMware siteden Azure koruma senaryosunda Dynamics-Component VM 'lerinin koruma durumu gösterilmektedir.

![Korumalı öğeler](./media/site-recovery-dynamics-ax/protecteditems.png)

### <a name="4-configure-networking"></a>4. ağı yapılandırma
**VM işlem ve ağ ayarlarını yapılandırma**

Dynamics AX istemci ve uygulama nesne sunucusu VM 'Leri için Site Recovery ağ ayarlarını yapılandırarak VM ağlarının yük devretme sonrasında doğru olağanüstü durum kurtarma ağına eklenmesini sağlayın. Bu katmanlara yönelik olağanüstü durum kurtarma ağının SQL katmanına yönlendirilebilir olduğundan emin olun.

Aşağıdaki anlık görüntüde gösterildiği gibi, ağ ayarlarını yapılandırmak için çoğaltılan öğelerde sanal makineyi seçebilirsiniz:

* Uygulama nesne sunucusu sunucuları için doğru kullanılabilirlik kümesini seçin.

* Statik IP kullanıyorsanız, VM 'nin **hedef IP** metin kutusunda kullanmasını istediğiniz IP 'yi belirtin.

    ![Ağ ayarları](./media/site-recovery-dynamics-ax/vmpropertiesaos1.png)


### <a name="5-create-a-recovery-plan"></a>5. bir kurtarma planı oluşturun

Yük devretme işlemini otomatikleştirmek için Site Recovery ' de bir kurtarma planı oluşturabilirsiniz. Kurtarma planına bir uygulama katmanı ve bir Web katmanı ekleyin. Ön ucun uygulama katmanından önce kapanması için bunları farklı gruplar halinde sıralayın.

1. Aboneliğinizde Site Recovery kasasını seçin ve **kurtarma planları** kutucuğunu seçin.

2. **+ Kurtarma planı**' nı seçin ve bir ad belirtin.

3. **Kaynağı** ve **hedefi**seçin. Hedef Azure veya ikincil bir site olabilir. Azure ' u seçerseniz, dağıtım modelini belirtmeniz gerekir.

    ![Kurtarma planı oluşturma](./media/site-recovery-dynamics-ax/recoveryplancreation1.png)

4. Kurtarma planı için uygulama nesne sunucusunu ve istemci VM 'lerini seçin ve ✓ seçin.

    ![Öğeleri seç](./media/site-recovery-dynamics-ax/selectvms.png)

    Kurtarma planı örneği:

    ![Kurtarma planı ayrıntıları](./media/site-recovery-dynamics-ax/recoveryplan.png)

Aşağıdaki adımları ekleyerek Dynamics AX uygulaması için kurtarma planını özelleştirebilirsiniz. Önceki anlık görüntü, tüm adımları ekledikten sonra kurtarma planının tamamını gösterir.


* **SQL Server yük devretme adımları**: SQL Server 'a özgü kurtarma adımları hakkında daha fazla bilgi için, bkz. [SQL Server ve Azure Site Recovery ile çoğaltma uygulamaları](site-recovery-sql.md).

* **Yük devretme grubu 1**: uygulama nesne sunucusu VM 'lerinin yükünü devreder.
Seçilen kurtarma noktasının veritabanının PIT için mümkün olduğunca yakın olduğundan emin olun, ancak bunun önüne geçin.

* **Betik**: yük dengeleyici ekleyin (yalnızca E-A).
Uygulama nesne sunucusu VM grubu, kendisine yük dengeleyici eklemek üzere olduktan sonra bir betik (Azure Otomasyonu aracılığıyla) ekleyin. Bu görevi yapmak için bir komut dosyası kullanabilirsiniz. Daha fazla bilgi için bkz. [çok katmanlı uygulama olağanüstü durum kurtarma için yük dengeleyici ekleme](https://azure.microsoft.com/blog/cloud-migration-and-disaster-recovery-of-load-balanced-multi-tier-applications-using-azure-site-recovery/).

* **Yük devretme grubu 2**: Dynamics AX istemci VM 'lerinin yükünü devreder. Kurtarma planının bir parçası olarak Web katmanı VM 'lerini devreder.


### <a name="perform-a-test-failover"></a>Yük devretme testi gerçekleştirme

Yük devretme testi sırasında Active Directory özgü daha fazla bilgi için, Active Directory "olağanüstü durum kurtarma çözümü" yardımcı kılavuzuna bakın.

Yük devretme testi sırasında SQL Server 'a özgü daha fazla bilgi için bkz. [SQL Server ve Azure Site Recovery ile uygulamaları çoğaltma](site-recovery-sql.md).

1. Azure portal gidin ve Site Recovery kasanızı seçin.

2. Dynamics AX için oluşturulan kurtarma planını seçin.

3. **Yük Devretme Testi**'ni seçin.

4. Yük devretme testi işlemini başlatmak için sanal ağı seçin.

5. İkincil ortam açıldıktan sonra, doğrulamaları gerçekleştirebilirsiniz.

6. Doğrulamalar tamamlandıktan sonra, **doğrulamaları Tamam** ' ı seçin ve yük devretme testi ortamı temizlenir.

Yük devretme testi gerçekleştirme hakkında daha fazla bilgi için bkz. [Azure 'a yük devretmeyi test etme Site Recovery](site-recovery-test-failover-to-azure.md).

### <a name="perform-a-failover"></a>Yük devretme gerçekleştirme

1. Azure portal gidin ve Site Recovery kasanızı seçin.

2. Dynamics AX için oluşturulan kurtarma planını seçin.

3. **Yük devretmeyi**seçin ve **Yük devretme**' yı seçin.

4. Hedef ağı seçin ve yük devretme işlemini başlatmak için **✓** ' ı seçin.

Yük devretme yapma hakkında daha fazla bilgi için bkz. [Site Recovery 'de yük devretme](site-recovery-failover.md).

### <a name="perform-a-failback"></a>Yeniden çalışma gerçekleştirme

Yeniden çalışma sırasında SQL Server özgü konular için bkz. [uygulamaları SQL Server ve Azure Site Recovery çoğaltma](site-recovery-sql.md).

1. Azure portal gidin ve Site Recovery kasanızı seçin.

2. Dynamics AX için oluşturulan kurtarma planını seçin.

3. **Yük devretmeyi**seçin ve **Yük devretme**' yı seçin.

4. **Yönü Değiştir**' i seçin.

5. Uygun seçenekleri seçin: veri eşitleme ve VM oluşturma.

6. Yeniden çalışma işlemini başlatmak için **✓** öğesini seçin.


Yeniden çalışma yapma hakkında daha fazla bilgi için bkz. [Azure 'dan şirket içine yeniden çalışma VMware VM 'leri](site-recovery-failback-azure-to-vmware.md).

## <a name="summary"></a>Özet
Site Recovery kullanarak, Dynamics AX uygulamanız için tamamen bir otomatik olağanüstü durum kurtarma planı oluşturabilirsiniz. Kesinti durumunda, yük devretmeyi her yerden Saniyeler içinde başlatabilir ve birkaç dakika içinde uygulamayı çalışır duruma getirebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar
Site Recovery ile kurumsal iş yüklerini koruma hakkında daha fazla bilgi için bkz. [hangi iş yüklerini koruyabilirim?](site-recovery-workload.md).
