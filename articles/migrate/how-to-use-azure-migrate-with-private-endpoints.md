---
title: Azure geçişi 'ni özel uç noktalarla kullanma
description: Özel bağlantıyı kullanarak keşif, değerlendirme ve geçiş yapmak için Azure geçişi özel bağlantı desteğini kullanın.
author: deseelam
ms.author: deseelam
ms.manager: bsiva
ms.topic: how-to
ms.date: 04/07/2020
ms.openlocfilehash: 82811c731c158d970d7ec2c2350a0cba106f6a67
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107835497"
---
# <a name="using-azure-migrate-with-private-endpoints"></a>Azure geçişi 'ni özel uç noktalarla kullanma  

Bu makalede, Azure [özel bağlantısı](https://docs.microsoft.com/azure/private-link/private-endpoint-overview)kullanarak özel bir ağ üzerinden sunucu bulma, değerlendirme ve geçirme Için Azure geçişi 'nin nasıl kullanılacağı açıklanır. 

Azure geçişi 'ni kullanarak Azure geçişi hizmetini bir ExpressRoute özel eşlemesi veya siteden siteye VPN bağlantısı üzerinden Azure geçiş hizmetine özel olarak ve güvenli bir şekilde bağlamak için Azure geçişi [: bulma ve değerlendirme](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-discovery-and-assessment-tool) ve [Azure geçişi: sunucu geçiş](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-server-migration-tool) araçları 'nı kullanabilirsiniz. 

Özel uç nokta bağlantı yöntemi, Azure geçişi hizmetine ve diğer Azure kaynaklarına genel ağlarla geçiş yapılmadan erişmek için bir kuruluş gereksinimi olduğunda önerilir. Ayrıca, daha iyi bant genişliği veya gecikme süresi gereksinimleri için mevcut ExpressRoute özel eşleme devrelerinizi kullanmak üzere özel bağlantı desteğini de kullanabilirsiniz. 

## <a name="support-requirements"></a>Destek gereksinimleri 

### <a name="required-permissions"></a>Gerekli izinler

**Katılımcı + abonelik üzerinde Kullanıcı erişimi Yöneticisi** veya **sahip** izinleri. 

### <a name="supported-scenarios-and-tools"></a>Desteklenen senaryolar ve araçlar

**Dağıtım** | **Ayrıntılar** | **Araçlar** 
--- | --- | ---
**Bulma ve değerlendirme** | [VMware vSphere](https://docs.microsoft.com/azure/migrate/tutorial-discover-vmware) veya [Microsoft Hyper-V](https://docs.microsoft.com/azure/migrate/tutorial-discover-hyper-v)gibi herhangi bir platform-hiper yönetici platformunda çalışan sunucularınızın, [AWS](https://docs.microsoft.com/azure/migrate/tutorial-discover-aws) veya [GCP](https://docs.microsoft.com/azure/migrate/tutorial-discover-gcp)gibi genel bulutlarda veya hatta [çıplak sunuculardan](https://docs.microsoft.com/azure/migrate/tutorial-discover-physical)aracısız, ölçek yerinde bulma ve değerlendirme gerçekleştirin. | Azure geçişi: bulma ve değerlendirme  <br/> 
**Yazılım envanteri** | VMware VM 'lerinde çalışan uygulamaları, rolleri ve özellikleri bulun. | Azure geçişi: bulma ve değerlendirme  
**Bağımlılık görselleştirmesi** | Sunucular arasında bağımlılıkları tanımlamak ve anlamak için bağımlılık Analizi özelliğini kullanın. <br/> [Aracısız bağımlılık görselleştirmesi](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies-agentless) Azure geçişi özel bağlantı desteğiyle yerel olarak desteklenir. <br/>[Aracı tabanlı bağımlılık görselleştirmesi](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) Internet bağlantısı gerektirir. aracı tabanlı bağımlılık görselleştirmesi için özel uç noktaları [nasıl kullanacağınızı öğrenin](https://docs.microsoft.com/azure/azure-monitor/logs/private-link-security) . | Azure geçişi: bulma ve değerlendirme |
**Geçiş** | [Aracısız Hyper-v geçişleri](https://docs.microsoft.com/azure/migrate/tutorial-migrate-hyper-v) gerçekleştirin veya [VMware VM](./tutorial-migrate-vmware-agent.md)'Lerinizi, [Hyper-V VM](./tutorial-migrate-physical-virtual-machines.md)'lerini, [fiziksel sunucuları](./tutorial-migrate-physical-virtual-machines.md), [AWS üzerinde](./tutorial-migrate-aws-virtual-machines.md)çalışan VM 'leri, [GCP](https://docs.microsoft.com/azure/migrate/tutorial-migrate-gcp-virtual-machines)üzerinde çalışan VM 'leri ya da farklı bir sanallaştırma sağlayıcısında çalışan VM 'leri geçirmek için aracı tabanlı yaklaşımı kullanın. | Azure Geçişi: Sunucu Geçişi
 
>[!Note]
>
> [Aracısız VMware geçişleri](https://docs.microsoft.com/azure/migrate/tutorial-migrate-vmware) , ExperessRoute Microsoft eşlemesi aracılığıyla Internet erişimi veya bağlantısı gerektirir. <br/> ExpressRoute özel eşlemesi veya siteden siteye (S2S) VPN bağlantısı üzerinden çoğaltmalar gerçekleştirmek için özel uç noktaları [nasıl kullanacağınızı öğrenin](https://docs.microsoft.com/azure/migrate/replicate-using-expressroute) .  <br/><br/> 
   
#### <a name="other-integrated-tools"></a>Diğer tümleşik araçlar

Genel ağ erişimi devre dışıysa bazı geçiş araçları, Azure geçişi projesine kullanım verilerini karşıya yükleyemeyebilir. Azure geçişi projesi, tüm ağlardan gelen trafiğin diğer Microsoft veya harici [bağımsız yazılım satıcısı (ISV)](https://docs.microsoft.com/azure/migrate/migrate-services-overview#isv-integration) tekliflerinden veri almasına izin verecek şekilde yapılandırılmalıdır. 


Azure geçişi projesi için genel ağ erişimini etkinleştirmek üzere Azure portal Azure geçiş **Özellikleri sayfasına** gidin, **Hayır**' ı seçin ve **Kaydet**' i seçin.

![Ağ erişim modunun nasıl değiştirileceğini gösteren diyagram.](./media/how-to-use-azure-migrate-with-private-endpoints/migration-project-properties.png)

### <a name="other-considerations"></a>Diğer önemli noktalar   

**Dikkat edilmesi gerekenler** | **Ayrıntılar**
--- | --- 
**Fiyatlandırma** | Fiyatlandırma bilgileri için bkz. [Azure Blob fiyatlandırması](https://azure.microsoft.com/pricing/details/storage/page-blobs/) ve [Azure özel bağlantı fiyatlandırması](https://azure.microsoft.com/pricing/details/private-link/).  
**Sanal ağ gereksinimleri** | ExpressRoute/VPN Gateway uç noktası, seçilen sanal ağda veya bu ağa bağlı bir sanal ağda bulunmalıdır. Sanal ağda ~ 15 IP adresi gerekebilir.  

## <a name="create-a-project-with-private-endpoint-connectivity"></a>Özel uç nokta bağlantısına sahip bir proje oluşturma

Yeni bir Azure geçişi projesi ayarlamak için bu [makaleyi](https://docs.microsoft.com/azure/migrate/create-manage-projects#create-a-project-for-the-first-time) kullanın. 

> [!Note]
> Mevcut Azure geçişi projeleri için bağlantı yöntemini özel uç nokta bağlantısı olarak değiştiremezsiniz.

**Gelişmiş** yapılandırma bölümünde, Azure geçişi projeniz için özel bir uç nokta oluşturmak üzere aşağıdaki ayrıntıları sağlayın.
- **Bağlantı yönteminde** **Özel uç nokta**' ı seçin. 
- **Genel uç nokta erişimini devre dışı bırak**' da, varsayılan ayarı **Hayır** olarak tutun. Genel ağ erişimi devre dışıysa bazı geçiş araçları, Azure geçişi projesine kullanım verilerini karşıya yükleyemeyebilir. [Daha fazla bilgi edinin.](#other-integrated-tools)
- **Sanal ağ aboneliği**' nde, Özel uç nokta sanal ağı için aboneliği seçin. 
- **Sanal ağ**' da, Özel uç nokta için sanal ağı seçin. Azure geçişi projesine bağlanması gereken Azure geçiş gereci ve diğer yazılım bileşenleri bu ağda veya bağlı bir sanal ağda olmalıdır.
- **Alt ağ**' da, Özel uç nokta için alt ağı seçin. 

**Oluştur**’u seçin. Azure Geçişi projesinin dağıtılması için birkaç dakika bekleyin. Proje oluşturma işlemi sürerken bu sayfayı kapatmayın.

![Proje oluşturma](./media/how-to-use-azure-migrate-with-private-endpoints/create-project.png)

    
Bu, bir geçiş projesi oluşturur ve kendisine özel bir uç nokta ekler. 

## <a name="discover-and-assess-servers-for-migration-using-azure-private-link"></a>Azure özel bağlantısını kullanarak sunucuları geçiş için bulma ve değerlendirme 

### <a name="set-up-the-azure-migrate-appliance"></a>Azure geçişi gereç 'yi ayarlama 

1. Makinelerde **bulunan makineler**  >  **sanallaştırılmış mı?**, sunucu türünü seçin.
2. **Azure geçişi proje anahtarı oluştur**' da Azure geçişi gereci için bir ad sağlayın. 
3. Gerekli Azure kaynaklarını oluşturmak için **anahtar oluştur** ' u seçin. 

    > [!Important]
    > Kaynak oluşturma sırasında makineleri keşfet sayfasını kapatmayın.  
    - Bu adımda, Azure geçişi bir Anahtar Kasası, depolama hesabı, kurtarma hizmetleri Kasası (yalnızca aracısız VMware geçişleri için) ve her kaynağa özel bir uç nokta ekler. Özel uç noktalar, proje oluşturma sırasında seçilen sanal ağda oluşturulur.  
    - Özel uç noktalar oluşturulduktan sonra, Azure geçişi kaynakları için DNS CNAME kaynak kayıtları, ' Privatelink ' önekine sahip bir alt etki alanındaki diğer ada güncelleştirilir. Azure geçişi, varsayılan olarak her kaynak türü için ' Privatelink ' alt etki alanına karşılık gelen özel bir DNS bölgesi oluşturur ve ilişkili özel uç noktalar için DNS A kayıtları ekler. Bu, Azure geçiş gerecinin ve kaynak ağda bulunan diğer yazılım bileşenlerinin özel IP adreslerinde Azure geçiş kaynak uç noktalarına ulaşmasını sağlar.  
    - Azure geçişi Ayrıca, geçiş projesi için [yönetilen bir kimlik](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) sağlar ve depolama hesabına güvenli bir şekilde erişmek için yönetilen kimliğe izinler verir.  

4. Anahtar başarıyla oluşturulduktan sonra, Gereç yapılandırmak ve kaydetmek için anahtar ayrıntılarını kopyalayın.   

#### <a name="download-the-appliance-installer-file"></a>Gereç yükleyicisi dosyasını indirin  

Azure geçişi: bulma ve değerlendirme basit bir Azure geçişi gereci kullanır. Gereç sunucu bulma işlemini gerçekleştirir ve Azure geçişi 'ne sunucu yapılandırma ve performans meta verileri gönderir.

Gereci ayarlamak için, portaldan yükleyici betiğini içeren daraltılmış dosyayı indirin. Sıkıştırılmış dosyayı, gereci barındıracak olan sunucuya kopyalayın. ZIP dosyasını indirdikten sonra, dosya güvenliğini doğrulayın ve gereci dağıtmak için yükleyici betiğini çalıştırın. 

Aşağıda, her senaryonun karma değerleri ile ilgili karşıdan yükleme bağlantıları verilmiştir:

Senaryo | İndirme bağlantısı | Karma değeri
--- | --- | ---
Hyper-V | [AzureMigrateInstaller-HyperV-Public-PrivateLink.zip](https://go.microsoft.com/fwlink/?linkid=2160557) | 17EFA01E3A7683F1CE2A08E3A9197A27D8BD2CC03C3AB5C6E00E4261A822BDB3
Fiziksel | [AzureMigrateInstaller-Physical-Public-PrivateLink.zip](https://go.microsoft.com/fwlink/?linkid=2160558) | 01028F92C2095452F2DDCB89986CDC1F177AAC58E150A5B219A69CF1B7DA3BE0
VMware | [AzureMigrateInstaller-VMware-public-PrivateLink.zip](https://go.microsoft.com/fwlink/?linkid=2160648) | 66D3217AEC1DE51D84EC608B22BDDAV605EC9C4FAFX06FC691/B 985886627C224
VMware genişleme | [AzureMigrateInstaller-VMware-Public-Scaleout-PrivateLink.zip](https://go.microsoft.com/fwlink/?linkid=2160811) | 42C1E8D5CF428E35E5B98E4E7465DD08439F0FD5C319340CE3E3ADC3DC1717A6

#### <a name="verify-security"></a>Güvenliği doğrulama

Dağıtmadan önce daraltılmış dosyanın güvenli olduğunu denetleyin.

1. Dosyayı indirdiğiniz sunucuda bir yönetici komut penceresi açın.
2. Daraltılmış dosyanın karmasını oluşturmak için aşağıdaki komutu çalıştırın

    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Genel bulut için örnek kullanım: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-VMware-public-PrivateLink.zip SHA256 ```

3.  Yukarıdaki tablodaki karma değerleri karşılaştırarak gerecin en son sürümünü doğrulayın.

Sunucunun seçilen senaryo (VMware/Hyper-V/fiziksel veya diğer) için [donanım gereksinimlerini](https://docs.microsoft.com/azure/migrate/migrate-appliance) karşıladığından ve gerekli Azure URL 'lerine ( [genel](./migrate-appliance.md#public-cloud-urls-for-private-link-connectivity) ve [kamu](./migrate-appliance.md#government-cloud-urls-for-private-link-connectivity) bulutlarına) bağlanabildiğinden emin olun.


#### <a name="run-the-script"></a>Betiği çalıştırın

1. Sıkıştırılmış dosyayı, Gereç barındıracak sunucuda bir klasöre ayıklayın. 
2. Makinede, yönetici (yükseltilmiş) ayrıcalıklarla PowerShell 'i başlatın.
3. PowerShell dizinini, indirilen sıkıştırılmış dosyadan ayıklanan içerikleri içeren klasör olarak değiştirin.
4. Komut dosyası **AzureMigrateInstaller.ps1** aşağıdaki gibi çalıştırın:

    ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller-VMware-public-PrivateLink> .\AzureMigrateInstaller.ps1```
   
5. Betik başarıyla çalıştıktan sonra gereci yapılandırabilmeniz için gereç yapılandırma yöneticisini başlatır. Herhangi bir sorunla karşılaşırsanız, C:\ProgramData\Microsoft Azure\Logs\ AzureMigrateScenarioInstaller_<em>timestamp</em>. log konumundaki betik günlüklerini gözden geçirin.

### <a name="configure-the-appliance-and-start-continuous-discovery"></a>Gereci yapılandırma ve sürekli bulmayı başlatma

Gereç sunucusuna bağlanabilecek herhangi bir makinede bir tarayıcı açın ve gereç Yapılandırma Yöneticisi 'nin URL 'sini açın: `https://appliance name or IP address: 44368` . Alternatif olarak, Configuration Manager kısayolunu seçerek, Gereç sunucu masaüstündeki Configuration Manager ' ı açabilirsiniz.

#### <a name="set-up-prerequisites"></a>Önkoşulları ayarlama

1. Üçüncü taraf bilgilerini okuyun ve **Lisans koşullarını** kabul edin.    
 
2. Configuration Manager > **önkoşulları ayarlama** bölümünde şunları yapın:
   - **Bağlantı**: gereç gereken URL 'lere erişimi denetler. Sunucu bir proxy kullanıyorsa:
     - Proxy  adresini `http://ProxyIPAddress` veya `http://ProxyFQDN` dinleme bağlantı noktasını belirtmek için proxy ayarla ' yı seçin.
     - Proxy için kimlik doğrulaması gerekiyorsa kimlik bilgilerini gerekin. Yalnızca HTTP proxy’si desteklenir.
     - İsterseniz, proxy sunucusunu atlayabilmeniz gereken URL 'Ler/IP adreslerinin listesini ekleyebilirsiniz. ExpressRoute özel eşlemesi kullanıyorsanız, bu [URL 'leri](https://docs.microsoft.com/azure/migrate/replicate-using-expressroute#configure-proxy-bypass-rules-on-the-azure-migrate-appliance-for-vmware-agentless-migrations)attığınızdan emin olun.
     - Proxy sunucusu ayrıntılarını güncelleştirdiyseniz veya proxy 'yi atlamak için URL/IP adresleri eklediyseniz yapılandırmayı kaydetmek için **Kaydet** ' i seçmeniz gerekir.
     
        > [!Note]
        > Bağlantı denetimi sırasında aka.ms/* bağlantısı ile ilgili bir hata alıyorsanız ve gerecin bu URL 'ye internet üzerinden erişmesini istemiyorsanız [**buradaki**](https://docs.microsoft.com/azure/migrate/migrate-appliance#turn-off-auto-update)adımları izleyerek gereç üzerindeki otomatik güncelleştirme hizmetini devre dışı bırakmanız gerekir. Otomatik güncelleştirme devre dışı bırakıldıktan sonra, aka.ms/* URL bağlantı denetimi atlanır. 

   - **Zaman eşitleme**: bulmanın düzgün çalışması için gereç üzerindeki zaman internet ile eşitlenmiş olmalıdır.
   - **Güncelleştirmeleri yükleme**: gereç en son güncelleştirmelerin yüklü olmasını sağlar. Denetim tamamlandıktan sonra, Gereç sunucusunda çalışan hizmetlerin durumunu ve sürümlerini görmek için **gereç hizmetlerini görüntüle** ' yi seçebilirsiniz.
        > [!Note]
        > Otomatik güncelleştirme hizmetini gereç üzerinde devre dışı bırakmayı seçtiyseniz [**buradaki**](https://docs.microsoft.com/azure/migrate/migrate-appliance#manually-update-an-older-version)adımları izleyerek hizmetlerin en son sürümlerini almak için gereç hizmetlerini el ile güncelleştirebilirsiniz.
   - **VDDK yükleme**: (_yalnızca VMware gereci için gereklidir)_ gereç VMware vSphere sanal disk geliştirme seti 'nin (VDDK) yüklü olduğunu denetler. Yüklü değilse, VMware 'den VDDK 6,7 ' i indirin ve indirilen ZIP içeriğini **yükleme yönergelerindeki** belirtilen konuma ayıklayın.

#### <a name="register-the-appliance-and-start-continuous-discovery"></a>Gereci kaydetme ve sürekli bulmayı başlatma

Önkoşul denetimi tamamlandıktan sonra, gereci kaydetmek ve ilgili senaryolar için sürekli bulmayı başlatmak üzere şu adımları izleyin: [VMware VM](https://docs.microsoft.com/azure/migrate/tutorial-discover-vmware#register-the-appliance-with-azure-migrate)'leri, [Hyper-V VM 'Leri](https://docs.microsoft.com/azure/migrate/tutorial-discover-hyper-v#register-the-appliance-with-azure-migrate), [fiziksel sunucular](https://docs.microsoft.com/azure/migrate/tutorial-discover-physical#register-the-appliance-with-azure-migrate), [AWS VM 'leri](https://docs.microsoft.com/azure/migrate/tutorial-discover-aws#register-the-appliance-with-azure-migrate), [GCP VM](https://docs.microsoft.com/azure/migrate/tutorial-discover-gcp#register-the-appliance-with-azure-migrate)'leri.


>[!Note]
> Gereç kaydı sırasında veya bulmayı başlatırken DNS çözümleme sorunları alıyorsanız, portalda **anahtar oluştur** adımı sırasında oluşturulan Azure geçiş kaynaklarını Azure geçiş gereci barındıran şirket içi sunucudan ulaşılabilir olduğundan emin olun. [Ağ bağlantısını doğrulama hakkında daha fazla bilgi edinin](#troubleshoot-network-connectivity).

### <a name="assess-your-servers-for-migration-to-azure"></a>Sunucularınızı Azure 'a geçiş için değerlendirin
Bulma işlemi tamamlandıktan sonra Azure geçişi: bulma ve değerlendirme aracı 'nı kullanarak sunucularınızı ([VMware VM](https://docs.microsoft.com/azure/migrate/tutorial-assess-vmware-azure-vm)'leri, [Hyper-V VM 'leri](https://docs.microsoft.com/azure/migrate/tutorial-assess-hyper-v), [fiziksel sunucular](https://docs.microsoft.com/azure/migrate/tutorial-assess-vmware-azure-vm), [AWS VM](https://docs.microsoft.com/azure/migrate/tutorial-assess-aws)'Leri, [GCP VM](https://docs.microsoft.com/azure/migrate/tutorial-assess-gcp)'LERI) Azure VM 'lerine veya Azure VMware çözümüne (AVS) geçiş için değerlendirin. 

Ayrıca, içeri aktarılan bir virgülle ayrılmış değerler (CSV) dosyası kullanarak [Şirket içi makinelerinizi](https://docs.microsoft.com/azure/migrate/tutorial-discover-import#prepare-the-csv) Azure geçişi: bulma ve değerlendirme aracı ile de değerlendirebilirsiniz.   

## <a name="migrate-servers-to-azure-using-azure-private-link"></a>Azure özel bağlantısını kullanarak sunucuları Azure 'a geçirme

Aşağıdaki bölümlerde, ExpressRoute özel eşlemesi veya VPN bağlantıları kullanarak geçişler için [Özel uç noktalarla](https://docs.microsoft.com/azure/private-link/private-endpoint-overview) Azure geçişi kullanmak için gereken adımlar açıklanır.  

Bu makalede, [VMware VM](./tutorial-migrate-vmware-agent.md)'lerinizi, [Hyper-V VM](./tutorial-migrate-physical-virtual-machines.md)'lerini, [fiziksel sunucuları](./tutorial-migrate-physical-virtual-machines.md), [AWS üzerinde](./tutorial-migrate-aws-virtual-machines.md)çalışan VM 'leri, [GCP üzerinde çalışan](https://docs.microsoft.com/azure/migrate/tutorial-migrate-gcp-virtual-machines)VM 'leri ya da Azure özel uç noktaları kullanarak farklı bir sanallaştırma sağlayıcısında çalışan VM 'leri geçirmeye yönelik bir kavram kanıtı dağıtım yolu gösterilmektedir. Özel bağlantı kullanarak [aracısız Hyper-V geçişleri](https://docs.microsoft.com/azure/migrate/tutorial-migrate-hyper-v) gerçekleştirmek için benzer bir yaklaşım kullanabilirsiniz.

>[!Note]
>[Aracısız VMware geçişleri](https://docs.microsoft.com/azure/migrate/tutorial-assess-physical) , ExperessRoute Microsoft eşlemesi aracılığıyla Internet erişimi veya bağlantısı gerektirir. 

### <a name="set-up-a-replication-appliance-for-migration"></a>Geçiş için bir çoğaltma gereci ayarlama 

Aşağıdaki diyagramda, Azure geçişi: sunucu geçiş aracı kullanılarak özel uç noktalarla aracı tabanlı çoğaltma iş akışı gösterilmektedir.  

![Çoğaltma mimarisi](./media/how-to-use-azure-migrate-with-private-endpoints/replication-architecture.png)

Araç, sunucularınızı Azure 'a çoğaltmak için bir çoğaltma gereci kullanır. [Çoğaltma gereci için bir makine hazırlamak ve ayarlamak](https://docs.microsoft.com/azure/migrate/tutorial-migrate-physical-virtual-machines#prepare-a-machine-for-the-replication-appliance) için bu makaleyi kullanın.

Çoğaltma gereci ayarladıktan sonra, geçiş için gerekli kaynakları oluşturmak üzere aşağıdaki yönergeleri kullanın. 

1. Makinelerde **bulunan makineler**  >  **sanallaştırılmış mi?**, **sanallaştırılmamış/diğer**' i seçin.
2. **Hedef bölge**' de, makineleri geçirmek istediğiniz Azure bölgesini seçin ve onaylayın.
3. Gerekli Azure kaynaklarını oluşturmak için **kaynak oluştur** ' u seçin. Kaynak oluşturma sırasında sayfayı kapatmayın.   
    - Bu, arka planda bir kurtarma hizmetleri Kasası oluşturur ve kasa için yönetilen bir kimlik sunar. Kurtarma Hizmetleri Kasası, sunucuların çoğaltma bilgilerini içeren ve çoğaltma işlemlerini tetiklemek için kullanılan bir varlıktır.  
    - Azure geçişi projesinde özel uç nokta bağlantısı varsa, kurtarma hizmetleri Kasası için özel bir uç nokta oluşturulur. Bu, kurtarma hizmetleri kasasıyla bağlantılı her mikro hizmet için bir tane olmak üzere özel uç noktaya beş tam özel ad (FQDN) ekler.   
    - Bu düzende, beş etki alanı adı biçimlendirilir: <br/> _{Kasa-kimliği}-ASR-pod01-{Type}-. {Target-coğrafi-kod}_. privatelink.siterecovery.windowsazure.com  
    - Varsayılan olarak, Azure geçişi otomatik olarak özel bir DNS bölgesi oluşturur ve kurtarma hizmetleri Kasası mikro hizmetleri için DNS A kayıtları ekler. Özel DNS bölgesi daha sonra özel uç nokta sanal ağına bağlanır. Bu, şirket içi çoğaltma gerecinin tam etki alanı adlarını özel IP adreslerine çözümlemesine izin verir.

4. Çoğaltma gerecini kaydetmeden önce, kasasının özel bağlantı FQDN 'Lerinin çoğaltma gerecini barındıran makineden erişilebilir olduğundan emin olun. [Ağ bağlantısını doğrulama hakkında daha fazla bilgi edinin.](#troubleshoot-network-connectivity) 

5. Bağlantıyı doğruladıktan sonra, Gereç kurulumunu ve anahtar dosyasını indirin, yükleme işlemini çalıştırın ve gereci Azure geçişi 'ne kaydedin. [Buradaki ayrıntılı adımları](https://docs.microsoft.com/azure/migrate/tutorial-migrate-physical-virtual-machines#set-up-the-replication-appliance)gözden geçirin. Çoğaltma gereç 'yı ayarladıktan sonra, taşımak istediğiniz makinelere [Mobility hizmetini yüklemek](https://docs.microsoft.com/azure/migrate/tutorial-migrate-physical-virtual-machines#install-the-mobility-service) için bu yönergeleri izleyin. 

### <a name="replicate-servers-to-azure-using-azure-private-link"></a>Azure özel bağlantısını kullanarak sunucuları Azure 'a çoğaltma 

Şimdi, çoğaltma için sunucuları seçmek üzere [Bu adımları](https://docs.microsoft.com/azure/migrate/tutorial-migrate-physical-virtual-machines#replicate-machines) izleyin.  

  >  **Hedef ayarlarını** Çoğalt  >  **önbellek/çoğaltma depolama hesabı**' nda, özel bir bağlantı üzerinden çoğaltılacak bir depolama hesabı seçmek için açılan listesini kullanın.  

Azure geçişi projenizin özel uç nokta bağlantısı varsa, Azure geçişi tarafından istenen depolama hesabına erişmek için  [Kurtarma Hizmetleri Kasası tarafından yönetilen kimliğe izinler vermeniz](#grant-access-permissions-to-the-recovery-services-vault) gerekir.   

Ayrıca, özel bir bağlantı üzerinden çoğaltmaları etkinleştirmek için, [depolama hesabı için özel bir uç nokta oluşturun.](#create-a-private-endpoint-for-the-storage-account-optional)

#### <a name="grant-access-permissions-to-the-recovery-services-vault"></a>Kurtarma Hizmetleri kasasına erişim izinleri verme

Kurtarma Hizmetleri Kasası yönetilen kimliği, önbellek/çoğaltma depolama hesabına kimliği doğrulanmış erişim için izinler gerektirir. 

Azure geçişi tarafından oluşturulan Kurtarma Hizmetleri kasasını belirlemek ve gerekli izinleri vermek için aşağıdaki kılavuzu kullanın. 

**_Kurtarma Hizmetleri kasasını ve yönetilen kimlik nesnesi KIMLIĞINI tanımlama_**

Kurtarma Hizmetleri kasasının ayrıntılarını Azure geçişi: sunucu geçiş **özellikleri** sayfasında bulabilirsiniz. 

1. **Azure geçişi hub 'ına** gidin, Azure geçişi: sunucu geçiş kutucuğunda **genel bakış** ' ı seçin.

    ![Azure geçişi merkezindeki genel bakış sayfası](./media/how-to-use-azure-migrate-with-private-endpoints/hub-overview.png)

2. Sol bölmede **Özellikler**' i seçin. Kurtarma Hizmetleri Kasası adı ve yönetilen kimlik KIMLIĞI ' ni unutmayın. Kasa, **bağlantı türü** olarak ve _diğer_ **çoğaltma türü** olarak _Özel uç noktaya_ sahip olacaktır. Kasaya erişim sağlarken bu bilgilere ihtiyacınız olacaktır.
      
    ![Azure geçişi: sunucu geçiş özellikleri sayfası](./media/how-to-use-azure-migrate-with-private-endpoints/vault-info.png)

**_Depolama hesabına erişmek için gerekli izinleri verin_**

 Kasanın yönetilen kimliğine çoğaltma için gereken depolama hesabında aşağıdaki rol izinlerinin verilmesi gerekir.  Bu durumda, depolama hesabını önceden oluşturmanız gerekir.

>[!Note]
> Özel bağlantı kullanarak Hyper-V VM 'lerini Azure 'a geçirmek için hem çoğaltma depolama hesabı hem de önbellek depolama hesabına erişim vermeniz gerekir. 

Rol izinleri, depolama hesabının türüne bağlı olarak farklılık gösterir.

- Kaynak Yöneticisi tabanlı depolama hesapları (Standart tür):
  - [Katkıda bulunan](../role-based-access-control/built-in-roles.md#contributor) _ve_
  - [Depolama Blob Verileri Katkıda Bulunanı](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)
- Kaynak Yöneticisi tabanlı depolama hesapları (Premium türü):
  - [Katkıda bulunan](../role-based-access-control/built-in-roles.md#contributor) _ve_
  - [Depolama Blob Verileri Sahibi](../role-based-access-control/built-in-roles.md#storage-blob-data-owner)

1. Çoğaltma için seçilen çoğaltma/önbellek depolama hesabına gidin. Sol bölmedeki **erişim denetimi (IAM)** seçeneğini belirleyin. 

1. **Rol ataması Ekle** bölümünde **Ekle**' yi seçin.

   ![Rol ataması ekleyin](./media/how-to-use-azure-migrate-with-private-endpoints/storage-role-assignment.png)


1. **Rol ataması Ekle** sayfasında, **rol** alanında yukarıda belirtilen izinler listesinden uygun rolü seçin. Daha önce belirtilen kasasının adını girin ve **Kaydet**' i seçin.

    ![Rol tabanlı erişim sağlama](./media/how-to-use-azure-migrate-with-private-endpoints/storage-role-assignment-select-role.png)

4. Bu izinlere ek olarak, Microsoft Trusted Services erişimine de izin vermeniz gerekir. Ağ erişiminizi seçili ağlarla sınırlandırdıysanız, **ağ sekmesindeki** **özel durumlar** bölümünde **Güvenilen Microsoft hizmetlerinin bu depolama hesabına erişmesine izin ver** ' i seçin. 

![Depolama hesabı için güvenilen Microsoft hizmetlerine izin ver](./media/how-to-use-azure-migrate-with-private-endpoints/exceptions.png)


### <a name="create-a-private-endpoint-for-the-storage-account-optional"></a>Depolama hesabı için özel bir uç nokta oluştur (isteğe bağlı)

Özel eşleme ile ExpressRoute kullanarak çoğaltmak için, önbellek/çoğaltma depolama hesapları için [özel bir uç nokta oluşturun](https://docs.microsoft.com/azure/private-link/tutorial-private-endpoint-storage-portal#create-storage-account-with-a-private-endpoint) (hedef alt kaynak: **_BLOB_**). 

>[!Note]
>
> - Yalnızca bir Genel Amaçlı v2 (GPv2) depolama hesabında özel uç noktalar oluşturabilirsiniz. Fiyatlandırma bilgileri için bkz. [Azure sayfa Blobları fiyatlandırması](https://azure.microsoft.com/pricing/details/storage/page-blobs/) ve [Azure özel bağlantı fiyatlandırması](https://azure.microsoft.com/pricing/details/private-link/)

Depolama hesabının özel uç noktası, Azure geçişi proje özel uç noktası veya bu ağa bağlı başka bir sanal ağ ile aynı sanal ağda oluşturulmalıdır. 

**Evet** ' i seçin ve özel bir DNS bölgesi ile tümleştirin. Özel DNS bölgesi, sanal ağdan gelen bağlantıları özel bir bağlantı üzerinden depolama hesabına yönlendirmenize yardımcı olur. **Evet** seçildiğinde DNS bölgesi otomatik olarak sanal ağa bağlanır ve oluşturulan yeni IP 'lerin ve tam etki alanı adlarının ÇÖZÜMLENMESI için DNS kayıtları eklenir. [Özel DNS bölgeleri](https://docs.microsoft.com/azure/dns/private-dns-overview) hakkında daha fazla bilgi edinin.

Özel uç noktayı oluşturan kullanıcı aynı zamanda depolama hesabının sahibiyseniz, Özel uç nokta otomatik olarak onaylanır. Aksi takdirde, depolama hesabının sahibi kullanım için özel uç noktasını onaylamalıdır. İstenen özel uç nokta bağlantısını onaylamak veya reddetmek için, depolama hesabı sayfasında **ağ** altında **Özel uç nokta bağlantılarına** gidin.

Devam etmeden önce özel uç nokta bağlantı durumunun durumunu gözden geçirin. 

![Özel uç nokta onay durumu](./media/how-to-use-azure-migrate-with-private-endpoints/private-endpoint-connection-state.png)

Özel uç noktasını oluşturduktan sonra,   >  özel bir bağlantı üzerinden çoğaltma için depolama hesabını seçmek üzere **hedef ayarlarını** Çoğalt  >  **önbellek depolama hesabını** Çoğalt açılan listesini kullanın.  

Şirket içi çoğaltma gerecinin, kendi özel uç noktasındaki depolama hesabına ağ bağlantısı olduğundan emin olun. [Ağ bağlantısını doğrulama hakkında daha fazla bilgi edinin.](#troubleshoot-network-connectivity)

>[!Note]
>
> - Azure 'a Hyper-V VM geçişleri için, çoğaltma depolama hesabı _Premium_ tür ise, önbellek depolama hesabı için _Standart_ türde başka bir depolama hesabı seçmeniz gerekir. Bu durumda, hem çoğaltma hem de önbellek depolama hesabı için özel uç noktalar oluşturmanız gerekir.  

Ardından, [çoğaltmayı gözden geçirmek ve yeniden başlatmak](https://docs.microsoft.com/azure/migrate/tutorial-migrate-physical-virtual-machines#replicate-machines) ve [geçişleri gerçekleştirmek](https://docs.microsoft.com/azure/migrate/tutorial-migrate-physical-virtual-machines#run-a-test-migration)için bu yönergeleri izleyin.  

## <a name="troubleshoot-network-connectivity"></a>Ağ bağlantısı sorunlarını giderme 

### <a name="validate-private-endpoints-configuration"></a>Özel uç nokta yapılandırmasını doğrula 

Özel uç noktanın onaylanmış bir durumda olduğundan emin olun.  

1. Azure geçişi: bulma ve değerlendirme ve sunucu geçiş özellikleri sayfasına gidin.
2. Özellikler sayfası, Azure geçişi tarafından otomatik olarak oluşturulan özel uç noktaların ve özel bağlantı FQDN 'lerinin listesini içerir.  

3. Tanılamak istediğiniz özel uç noktayı seçin.  
    1. Bağlantı durumunun onaylandığını doğrulayın.
    2. Bağlantı bekleme durumundaysa, onay almanız gerekir.
    3. Ayrıca, Özel uç nokta kaynağına gidebilir ve sanal ağın projeyi geçir özel uç noktası sanal ağıyla eşleşip eşleşmediğini gözden geçirebilirsiniz. 

    ![Özel uç nokta bağlantısını görüntüle](./media/how-to-use-azure-migrate-with-private-endpoints/private-endpoint-connection.png)

### <a name="verify-dns-resolution"></a>DNS çözümlemesini doğrulama 

Şirket içi gereç (veya çoğaltma sağlayıcısı), tam özel bağlantı etki alanı adlarını (FQDN) kullanarak Azure geçiş kaynaklarına erişir. Özel uç noktaların özel IP adresini kaynak ortamdan çözümlemek için ek DNS ayarları gerekebilir. Herhangi bir ağ bağlantısı sorununu gidermeye yardımcı olabilecek DNS yapılandırma senaryolarını anlamak için [Bu makaleyi kullanın](https://docs.microsoft.com/azure/private-link/private-endpoint-dns#on-premises-workloads-using-a-dns-forwarder) .  

Özel bağlantı bağlantısını doğrulamak için, geçiş gereci barındıran şirket içi sunucudan Azure geçişi kaynak uç noktaları (özel bağlantı kaynak FQDN 'leri) için bir DNS çözümlemesi yapın ve özel bir IP adresine çözümlendiğinden emin olun. Özel uç nokta ayrıntıları ve özel bağlantı kaynağı FQDN 'leri bilgileri bulma ve değerlendirme ve sunucu geçiş özellikleri sayfalarında kullanılabilir. Listeyi görüntülemek için **DNS ayarlarını indir** ' i seçin.   

 ![Azure geçişi: bulma ve değerlendirme özellikleri](./media/how-to-use-azure-migrate-with-private-endpoints/server-assessment-properties.png)

 ![Azure geçişi: sunucu geçiş özellikleri](./media/how-to-use-azure-migrate-with-private-endpoints/azure-migrate-server-migration-properties.png)

Depolama hesabı özel bağlantı FQDN 'sinin DNS çözümlemesi için bir tanım örneği.  

- _Nslookup<Storage-Account-name>_. blob.Core.Windows.net girin.  <Storage-Account-Name> değerini Azure geçişi için kullanılan depolama hesabının adıyla değiştirin.  

    Şöyle bir ileti alacaksınız:  

   ![DNS çözümleme örneği](./media/how-to-use-azure-migrate-with-private-endpoints/dns-resolution-example.png)

- Depolama hesabı için 10.1.0.5 özel IP adresi döndürülür. Bu adres özel uç nokta sanal ağ alt ağına aittir.   

Benzer bir yaklaşım kullanarak diğer Azure geçişi yapıtları için DNS çözümlemesini doğrulayabilirsiniz.   

DNS çözünürlüğü yanlışsa, şu adımları izleyin:  

- Özel bir DNS kullanıyorsanız, özel DNS ayarlarınızı gözden geçirin ve DNS yapılandırmasının doğru olduğunu doğrulayın. Rehberlik için bkz. [Özel uç noktaya genel bakış: DNS yapılandırması](https://docs.microsoft.com/azure/private-link/private-endpoint-overview#dns-configuration).
- Azure tarafından sağlanan DNS sunucuları kullanıyorsanız, daha fazla sorun giderme için aşağıdaki bölüme bakın.  

> [!Tip]
> Şirket içi gerecinizdeki DNS ana bilgisayarları dosyasını, FQDN 'Ler ve bunlarla ilişkili özel IP adresleriyle düzenleyerek, kaynak ortam DNS kayıtlarınızı el ile güncelleştirebilirsiniz. Bu seçenek yalnızca test için önerilir. <br/>  


### <a name="validate-the-private-dns-zone"></a>Özel DNS bölgeyi doğrulama   
DNS çözümlemesi, önceki bölümde açıklandığı gibi çalışmıyorsa Özel DNS bölgenize bir sorun olabilir.  

#### <a name="confirm-that-the-required-private-dns-zone-resource-exists"></a>Gerekli Özel DNS bölgesi kaynağının var olduğunu onaylayın  
Azure geçişi, varsayılan olarak her kaynak türü için ' Privatelink ' alt etki alanına karşılık gelen özel bir DNS bölgesi de oluşturur. Özel DNS bölgesi, Özel uç nokta kaynak grubuyla aynı Azure Kaynak grubunda oluşturulacaktır. Azure Kaynak grubu, aşağıdaki biçimde özel DNS bölge kaynakları içermelidir:
- Anahtar Kasası için privatelink.vaultcore.azure.net 
- depolama hesabı için privatelink.blob.core.windows.net
- Kurtarma Hizmetleri Kasası için privatelink.siterecovery.windowsazure.com (Hyper-V ve aracı tabanlı çoğaltmalar için)
- privatelink.prod.migration.windowsazure.com-proje, değerlendirme projesi ve bulma sitesini geçirme.   

Özel DNS bölgesi, Azure geçişi tarafından otomatik olarak oluşturulur (Kullanıcı tarafından seçilen önbellek/çoğaltma depolama hesabı hariç). Özel uç nokta sayfasına giderek ve DNS yapılandırması ' nı seçerek bağlantılı özel DNS bölgesini bulabilirsiniz. Özel DNS tümleştirmesi bölümünün altında özel DNS bölgesini görmeniz gerekir. 

![DNS yapılandırması ekran görüntüsü](./media/how-to-use-azure-migrate-with-private-endpoints/dns-configuration.png)  

DNS bölgesi yoksa (aşağıda gösterildiği gibi), [Yeni bir özel DNS bölge kaynağı oluşturun.](https://docs.microsoft.com/azure/dns/private-dns-getstarted-portal)  

![Özel DNS bölgesi oluşturma](./media/how-to-use-azure-migrate-with-private-endpoints/create-dns-zone.png) 

#### <a name="confirm-that-the-private-dns-zone-is-linked-to-the-virtual-network"></a>Özel DNS bölgesinin sanal ağa bağlı olduğunu onaylayın  
Özel DNS bölgesi, kaynak uç noktasının özel IP adresini çözümlemek için DNS sorgusunun özel uç noktasını içeren sanal ağa bağlanmalıdır. Özel DNS bölgesi doğru sanal ağa bağlı değilse, bu sanal ağdan gelen herhangi bir DNS çözümlemesi özel DNS bölgesini yoksayar.   

Azure portal özel DNS bölgesi kaynağına gidin ve sol menüden sanal ağ bağlantılarını seçin. Bağlı sanal ağları görmeniz gerekir.

![Sanal ağ bağlantılarını görüntüle](./media/how-to-use-azure-migrate-with-private-endpoints/virtual-network-links.png) 

Bu, her biri aboneliğinizdeki bir sanal ağın adı ile birlikte bağlantıların bir listesini gösterir. Özel uç nokta kaynağını içeren sanal ağın burada listelenmesi gerekir. Aksi takdirde, özel DNS bölgesini bir sanal ağa bağlamak için [Bu makaleyi izleyin](https://docs.microsoft.com/azure/dns/private-dns-getstarted-portal#link-the-virtual-network) .    

Özel DNS bölgesi sanal ağa bağlandıktan sonra, sanal ağdan kaynaklanan DNS istekleri özel DNS bölgesindeki DNS kayıtlarını arayacaktır. Bu, Özel uç noktanın oluşturulduğu sanal ağ için doğru adres çözümlemesi için gereklidir.   

#### <a name="confirm-that-the-private-dns-zone-contains-the-right-a-records"></a>Özel DNS bölgesinin doğru bir kayıt içerdiğini onaylayın 

Sorun gidermek istediğiniz özel DNS bölgesine gidin. Genel Bakış sayfası, özel DNS bölgesi için tüm DNS kayıtlarını gösterir. Kaynak için bir DNS A kaydı bulunduğunu doğrulayın. Bir kaydın değeri (IP adresi), kaynakların özel IP adresi olmalıdır. Yanlış IP adresine sahip bir kayıt bulursanız, yanlış IP adresini kaldırmalı ve yeni bir tane eklemeniz gerekir. Bir kaydın tamamını kaldırmanız ve yeni bir tane eklemeniz ve şirket içi kaynak gereci üzerinde bir DNS temizlemesi yapmanız önerilir.   

Özel DNS bölgesindeki depolama hesabı DNS A kaydı için bir tanım örneği:

![DNS kayıtları](./media/how-to-use-azure-migrate-with-private-endpoints/dns-a-records.png)   

Kurtarma Hizmetleri Kasası mikro hizmetleri DNS, özel DNS bölgesindeki kayıtlar için bir tanım örneği: 

![Kurtarma Hizmetleri Kasası için DNS kayıtları](./media/how-to-use-azure-migrate-with-private-endpoints/rsv-a-records.png)   

>[!Note]
> Bir kaydı kaldırdığınızda veya değiştirirken, TTL (yaşam süresi) değeri henüz kullanım süresi dolmayabilir, bu durumda makine eski IP adresine çözümlenmeye devam edebilir.  

#### <a name="other-things-that-may-affect-private-link-connectivity"></a>Özel bağlantı bağlantısını etkileyebilecek diğer şeyler  

Bu, gelişmiş veya karmaşık senaryolarda bulunan öğelerin ayrıntılı olmayan bir listesidir: 

- Güvenlik Duvarı ayarları, sanal ağa bağlı Azure Güvenlik duvarı ya da gereç makinesinde dağıtım yapılan özel bir güvenlik duvarı çözümüdür.  
- Hangi DNS sunucularının kullanıldığını ve trafiğin nasıl yönlendirildiğini etkileyebilecek ağ eşleme.  
- Özel ağ geçidi (NAT) çözümleri, DNS sorgularından gelen trafik dahil trafiğin nasıl yönlendirildiğini etkileyebilir.

Daha fazla bilgi için, [Özel uç nokta bağlantı sorunları için sorun giderme kılavuzunu](https://docs.microsoft.com/azure/private-link/troubleshoot-private-endpoint-connectivity) gözden geçirin.  

## <a name="next-steps"></a>Sonraki adımlar 
- [Geçiş Işlemini tamamlayıp](https://docs.microsoft.com/azure/migrate/tutorial-migrate-physical-virtual-machines#complete-the-migration) [geçiş sonrası en iyi yöntemleri](https://docs.microsoft.com/azure/migrate/tutorial-migrate-physical-virtual-machines#post-migration-best-practices)gözden geçirin.