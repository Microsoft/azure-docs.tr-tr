---
title: Azure geçişi 'nde VMware değerlendirmesi desteği
description: Azure geçişi bulma ve değerlendirme ile VMware ortamında çalışan sunucular için değerlendirme desteği hakkında bilgi edinin
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 03/17/2021
ms.openlocfilehash: 4d51fc13e3587c21a7340b35db10d3cf36ab74b5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105557556"
---
# <a name="support-matrix-for-vmware-assessment"></a>VMware değerlendirmesi için destek matrisi 

Bu makalede, Azure geçişi [: bulma ve değerlendirme](migrate-services-overview.md#azure-migrate-discovery-and-assessment-tool) Aracı ' nı kullanarak, VMware ortamında çalışan sunucuları bulma ve değerlendirme sırasında önkoşulları ve destek gereksinimleri özetlenmektedir. Sunucuları değerlendirmek için, projeye Azure geçişi: bulma ve değerlendirme aracını ekleyen bir proje oluşturursunuz. Araç eklendikten sonra Azure geçişi gereci dağıtırsınız. Gereç, şirket içi sunucuları sürekli olarak bulur ve Azure 'a yapılandırma ve performans meta verilerini gönderir. Bulma işlemi tamamlandıktan sonra keşfedilen sunucuları gruplar halinde toplar ve bir grup için değerlendirme çalıştırırsınız.

VMware sunucularını Azure 'a geçirmek istiyorsanız, [geçiş desteği matrisini](migrate-support-matrix-vmware-migration.md)gözden geçirin.



## <a name="limitations"></a>Sınırlamalar

**Gereksinim** | **Ayrıntılar**
--- | ---
**Proje limitleri** | Bir Azure aboneliğinde birden çok proje oluşturabilirsiniz.<br/><br/> Tek bir [projede](migrate-support-matrix.md#project)VMware ortamından en fazla 50.000 sunucuyu bulabilir ve değerlendirebilirsiniz. Bir proje, fiziksel sunucuları ve Hyper-V ortamından, değerlendirme sınırlarına kadar olan sunucuları da içerebilir.
**Bulma** | Azure geçişi gereci bir vCenter Server en fazla 10.000 sunucu bulabilir.
**Değerlendirme** | Tek bir gruba en fazla 35.000 sunucu ekleyebilirsiniz.<br/><br/> Tek bir değerlendirmede en fazla 35.000 sunucu değerlendirebilirsiniz.

Değerlendirmeler hakkında [daha fazla bilgi edinin](concepts-assessment-calculation.md) .


## <a name="vmware-requirements"></a>VMware gereksinimleri

**VMware** | **Ayrıntılar**
--- | ---
**vCenter Server** | Keşfetmek ve değerlendirmek istediğiniz sunucular vCenter Server sürüm 5,5, 6,0, 6,5, 6,7 veya 7,0 tarafından yönetilmelidir.<br/><br/> Uygulama için ESXi ana bilgisayar ayrıntıları sağlayarak sunucu keşfi Şu anda desteklenmiyor.
**İzinler** | Azure geçişi: bulma ve değerlendirme için vCenter Server salt okunurdur ve değerlendirme için bir salt okuma hesabı gerekir.<br/><br/> Yazılım envanteri ve aracısız bağımlılık Analizi bulmayı gerçekleştirmek istiyorsanız, hesabın **sanal makineler**  >  **Konuk işlemleri** için etkinleştirilmiş ayrıcalıkları olması gerekir.

## <a name="server-requirements"></a>Sunucu gereksinimleri
**VMware** | **Ayrıntılar**
--- | ---
**Desteklenen işletim sistemi** | Geçiş için tüm Windows ve Linux işletim sistemleri değerlendirilenebilir.
**Depolama** | SCSI, IDE ve SATA tabanlı denetleyicilere bağlı diskler desteklenir.


## <a name="azure-migrate-appliance-requirements"></a>Azure Geçişi aleti gereksinimleri

Azure geçişi, bulma ve değerlendirme için [Azure geçişi](migrate-appliance.md) gereci kullanır. Gereci bir OVA şablonu kullanarak VMware ortamınızda bir sunucu olarak dağıtabilir, vCenter Server içeri aktarılabilir veya bir [PowerShell betiği](deploy-appliance-script.md)kullanabilirsiniz.

- VMware için [gereç gereksinimleri](migrate-appliance.md#appliance---vmware) hakkında bilgi edinin.
- Azure Kamu 'da, [betiği kullanarak](deploy-appliance-script-government.md)gereci dağıtmanız gerekir.
- Gereçlerin [ortak](migrate-appliance.md#public-cloud-urls) ve [kamu](migrate-appliance.md#government-cloud-urls) bulutlarında erişmesi gereken URL 'leri gözden geçirin.


## <a name="port-access-requirements"></a>Bağlantı noktası erişim gereksinimleri

**Cihaz** | **Bağlantı**
--- | ---
**Elektrikli** | TCP bağlantı noktası 3389 üzerindeki gelen bağlantılar, gereci Uzak Masaüstü bağlantılarına izin vermek için.<br/><br/> 44368 numaralı bağlantı noktası üzerinden gereç yönetimi uygulamasına uzaktan erişim için gelen bağlantılar: ```https://<appliance-ip-or-name>:44368``` <br/><br/>Azure geçişi 'ne bulma ve performans meta verileri göndermek için 443 (HTTPS) bağlantı noktası üzerinden giden bağlantılar.
**vCenter server** | TCP bağlantı noktası 443 ' deki gelen bağlantılar, gerecin değerlendirmeler için yapılandırma ve performans meta verilerini toplamasına izin verir. <br/><br/> Gereç, bağlantı noktası 443 ' de varsayılan olarak vCenter 'a bağlanır. VCenter sunucusu farklı bir bağlantı noktasını dinliyorsa, bulmayı ayarlarken bağlantı noktasını değiştirebilirsiniz.
**ESXi Konakları** | [Yazılım envanterini bulmayı](how-to-discover-applications.md)veya [aracısız bağımlılık analizini](concepts-dependency-visualization.md#agentless-analysis)yapmak istiyorsanız, Gereç, sunuculardaki yazılım envanterini ve bağımlılıklarını bulmak için TCP bağlantı noktası 443 üzerindeki ESXi konaklarına bağlanır.

## <a name="application-discovery-requirements"></a>Uygulama bulma gereksinimleri

Sunucuları bulmanın yanı sıra Azure geçişi: bulma ve değerlendirme, sunucularda çalışan yazılım envanterini bulabilir. Uygulama bulma, şirket içi iş yükleriniz için uyarlanmış bir geçiş yolu tanımlamanızı ve planlamanızı sağlar.

**Destek** | **Ayrıntılar**
--- | ---
**Desteklenen sunucular** | Şu anda yalnızca VMware ortamınızdaki sunucular için destekleniyor. Her bir Azure geçişi gerecinden en fazla 10000 sunucuda uygulama bulma işlemini gerçekleştirebilirsiniz.
**İşletim sistemleri** | Tüm Windows ve Linux sürümlerini çalıştıran sunucular desteklenir.
**VM gereksinimleri** | Yazılım envanteri bulmayı gerçekleştirmek için, VMware araçlarının sunucularda yüklü ve çalışıyor olması gerekir. <br/><br/> VMware araçları sürümü 10.2.0 'dan daha sonra olmalıdır.<br/><br/> Windows sunucularında PowerShell sürüm 2,0 veya üzeri yüklü olmalıdır.
**Bulma** | Sunucularda uygulama bulma işlemi, sunucularda yüklü olan VMware araçları kullanılarak vCenter Server gerçekleştirilir. Gereç, vSphere API 'Lerini kullanarak vCenter Server yazılım envanteriyle ilgili bilgileri toplar. Uygulama bulma aracısız. Sunucuda aracı yüklü değil ve gereç doğrudan sunuculara bağlanmıyor. WMI ve SSH, sırasıyla Windows ve Linux sunucularında etkinleştirilmiş ve kullanılabilir olmalıdır.
**vCenter Server Kullanıcı hesabı** | Değerlendirme için kullanılan vCenter Server salt okuma hesabı,   >  uygulama keşfi için sunucularla etkileşime geçmek üzere sanal makineler **Konuk işlemleri** için etkinleştirilmiş ayrıcalıklara ihtiyaç duyuyor.
**Sunucu erişimi** | Uygulama bulma için gereç Yapılandırma Yöneticisi 'nde birden çok etki alanı ve etki alanı olmayan (Windows/Linux) kimlik bilgilerini ekleyebilirsiniz.<br/><br/> Windows Server için bir Konuk Kullanıcı hesabına ve tüm Linux sunucuları için normal/normal kullanıcı hesabına (sudo erişimi olmayan) ihtiyacınız vardır.
**Bağlantı noktası erişimi** | Azure geçişi gereci, uygulama bulma işlemini gerçekleştirmek istediğiniz sunucuları çalıştıran ESXi konaklarında TCP bağlantı noktası 443 ' e bağlanabilmelidir. VCenter Server, yazılım envanterinin ayrıntılarını içeren dosyayı indirmek için bir ESXi ana bilgisayar bağlantısı döndürür.

## <a name="requirements-for-discovery-of-sql-server-instances-and-databases"></a>SQL Server örnekleri ve veritabanlarını bulma gereksinimleri

[Uygulama bulma](how-to-discover-applications.md) SQL Server örnekleri tanımlar. Bu bilgileri kullanarak, Gereç, Windows kimlik doğrulaması veya gereç üzerinde sunulan SQL Server kimlik doğrulama kimlik bilgileri aracılığıyla ilgili SQL Server örneklerine bağlanmaya çalışır. Bağlantı kurulduktan sonra, Gereç SQL Server örneklerinin ve veritabanlarının yapılandırma ve performans verilerini toplar. SQL Server yapılandırma verileri her 24 saatte bir güncelleştirilir ve performans verileri her 30 saniyede yakalanır.

**Destek** | **Ayrıntılar**
--- | ---
**Desteklenen sunucular** | Şu anda yalnızca VMware ortamınızdaki SQL Server 'Lar için desteklenmektedir. En az 300 SQL Server örneği veya 6000 SQL veritabanını bulabilirsiniz.
**Windows sunucuları** | Windows Server 2008 ve üzeri desteklenir.
**Linux sunucuları** | Şu anda desteklenmiyor.
**Kimlik doğrulama mekanizması** | Hem Windows hem de SQL Server kimlik doğrulaması desteklenir. Gereç Yapılandırma Yöneticisi 'nde her iki kimlik doğrulaması türünün kimlik bilgilerini sağlayabilirsiniz.
**SQL Server erişimi** | Azure geçişi, sysadmin sunucu rolünün üyesi olan bir Windows Kullanıcı hesabı gerektirir.
**SQL Server sürümleri** | SQL Server 2008 ve üzeri desteklenir.
**SQL Server sürümleri** | Enterprise, Standard, Developer ve Express sürümleri desteklenir.
**Desteklenen SQL yapılandırması** | Şu anda yalnızca tek başına SQL Server örnekleri ve ilgili veritabanlarının bulunması desteklenmektedir.<br/> Yük devretme kümesi ve Always on kullanılabilirlik gruplarının tanımlanması desteklenmez.
**Desteklenen SQL Hizmetleri** | Yalnızca SQL Server veritabanı altyapısı desteklenir. <br/> SQL Server Reporting Services (SSRS), SQL Server Integration Services (SSIS) ve SQL Server Analysis Services (SSAS) keşfi desteklenmez.

> [!Note]
> Azure geçişi, Azure geçişi gereci ve kaynak SQL Server örnekleri (şifreleme bağlantısı özelliği TRUE olarak ayarlanır) arasındaki iletişimi şifreler. Bu bağlantılar [**TrustServerCertificate**](/dotnet/api/system.data.sqlclient.sqlconnectionstringbuilder.trustservercertificate) ile ŞIFRELENIR (true olarak ayarlanır); Aktarım katmanı, kanalı şifrelemek ve güveni doğrulamak üzere sertifika zincirini atlamak için SSL kullanır. Gereç sunucusunun, [**sertifikanın kök yetkilisine güvenecek**](/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine)şekilde ayarlanması gerekir.<br/>
Sunucu başlatıldığında sunucuda bir sertifika sağlanmadıysa SQL Server, oturum açma paketlerini şifrelemek için kullanılan kendinden imzalı bir sertifika oluşturur. [**Daha fazla bilgi edinin**](/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine).

## <a name="dependency-analysis-requirements-agentless"></a>Bağımlılık Analizi gereksinimleri (aracısız)

[Bağımlılık Analizi](concepts-dependency-visualization.md) , değerlendirmek ve Azure 'a geçirmek istediğiniz şirket içi sunucular arasındaki bağımlılıkları belirlemenize yardımcı olur. Tablo aracısız bağımlılık analizini ayarlamaya yönelik gereksinimleri özetler.

**Destek** | **Ayrıntılar**
--- | --- 
**Desteklenen sunucular** | Şu anda yalnızca VMware ortamınızdaki sunucular için destekleniyor.
**Windows sunucuları** | Windows Server 2016<br/> Windows Server 2012 R2<br/> Windows Server 2012<br/> Windows Server 2008 R2 (64 bit).<br/>Microsoft Windows Server 2008 (32-bit).
**Linux sunucuları** | Red Hat Enterprise Linux 7, 6, 5<br/> Ubuntu Linux 14,04, 16,04<br/> Desek6, 8<br/> Oracle Linux 6, 7<br/> CentOS 5, 6, 7.<br/> SUSE Linux Enterprise Server 11 ve üzeri.
**Sunucu gereksinimleri** | VMware araçları (10.2.0 ' den sonraki sürümler), çözümlemek istediğiniz sunucularda yüklü ve çalışır olmalıdır.<br/><br/> Sunucularda PowerShell sürüm 2,0 veya üzeri yüklü olmalıdır.
**Bulma yöntemi** |  Sunucular arasındaki bağımlılık bilgileri, sunucuda yüklü olan VMware araçları kullanılarak vCenter Server toplanır. Gereç, vSphere API 'Lerini kullanarak vCenter Server bilgileri toplar. Sunucuda aracı yüklü değil ve gereç doğrudan sunucularla bağlantı yapmaz. WMI ve SSH, sırasıyla Windows ve Linux sunucularında etkinleştirilmiş ve kullanılabilir olmalıdır.
**vCenter hesabı** | Değerlendirme için Azure geçişi tarafından kullanılan salt tanımlı hesabın, **sanal makineler > Konuk işlemleri** için etkinleştirilmiş ayrıcalıkları vardır.
**Windows Server izinleri** |  Sunucularda yönetim izinlerine sahip bir kullanıcı hesabı (yerel veya etki alanı).
**Linux hesabı** | Kök kullanıcı hesabı veya/bin/netstat ve/bin/ls dosyalarında şu izinlere sahip bir hesap: CAP_DAC_READ_SEARCH ve CAP_SYS_PTRACE.<br/><br/> Aşağıdaki komutları kullanarak bu özellikleri ayarlayın: <br/><br/> sudo setcap CAP_DAC_READ_SEARCH, CAP_SYS_PTRACE = EP/bin/ls<br/><br/> sudo setcap CAP_DAC_READ_SEARCH, CAP_SYS_PTRACE = EP/bin/netstat
**Bağlantı noktası erişimi** | Azure geçişi gereci, bağımlılıklarını öğrenmek istediğiniz sunucuları çalıştıran ESXi konaklarında TCP bağlantı noktası 443 ' e bağlanabilmelidir. VCenter Server, bağımlılık verilerini içeren dosyayı indirmek için bir ESXi ana bilgisayar bağlantısı döndürür.


## <a name="dependency-analysis-requirements-agent-based"></a>Bağımlılık Analizi gereksinimleri (aracı tabanlı)

[Bağımlılık Analizi](concepts-dependency-visualization.md) , değerlendirmek ve Azure 'a geçirmek istediğiniz şirket içi sunucular arasındaki bağımlılıkları belirlemenize yardımcı olur. Tablo, aracı tabanlı bağımlılık analizini ayarlamaya yönelik gereksinimleri özetler.

**Gereksinim** | **Ayrıntılar** 
--- | --- 
**Dağıtımdan önce** | Projeye Azure geçişi: bulma ve değerlendirme aracı eklenmiş bir projeniz olması gerekir.<br/><br/>  Şirket içi sunucularınızı bulmaya yönelik bir Azure geçiş gereci ayarladıktan sonra bağımlılık görselleştirmesini dağıtırsınız.<br/><br/> İlk kez bir proje oluşturmayı [öğrenin](create-manage-projects.md) .<br/> Mevcut bir projeye bulma ve değerlendirme aracı ekleme [hakkında bilgi edinin](how-to-assess.md) .<br/> [Hyper-V](how-to-set-up-appliance-hyper-v.md), [VMware](how-to-set-up-appliance-vmware.md)veya fiziksel sunucu değerlendirmesi için Azure geçişi gerecini ayarlamayı öğrenin.
**Desteklenen sunucular** | Şirket içi ortamınızdaki tüm sunucular için desteklenir.
**Log Analytics** | Azure geçişi, bağımlılık görselleştirmesi için [Azure izleyici günlüklerinde](../azure-monitor/log-query/log-query-overview.md) [hizmet eşlemesi](../azure-monitor/insights/service-map.md) çözümünü kullanır.<br/><br/> Yeni veya mevcut bir Log Analytics çalışma alanını projeyle ilişkilendirirsiniz. Bir projenin çalışma alanı eklendikten sonra değiştirilemez. <br/><br/> Çalışma alanı, projeyle aynı abonelikte olmalıdır.<br/><br/> Çalışma alanı Doğu ABD, Güneydoğu Asya veya Batı Avrupa bölgelerinde bulunmalıdır. Diğer bölgelerdeki çalışma alanları bir projeyle ilişkilendirilemez.<br/><br/> Çalışma alanının [hizmet eşlemesi desteklendiği](../azure-monitor/insights/vminsights-configure-workspace.md#supported-regions)bir bölgede olması gerekir.<br/><br/> Log Analytics, Azure geçişi ile ilişkili çalışma alanı, proje anahtarı ve proje adı ile etiketlenir.
**Gerekli aracılar** | Çözümlemek istediğiniz her sunucuda, aşağıdaki aracıları yükleyebilirsiniz:<br/><br/> [Microsoft Monitoring Agent (MMA)](../azure-monitor/platform/agent-windows.md).<br/> [Bağımlılık Aracısı](../azure-monitor/platform/agents-overview.md#dependency-agent).<br/><br/> Şirket içi sunucular internet 'e bağlı değilse, bunlara Log Analytics ağ geçidi indirip yüklemeniz gerekir.<br/><br/> [Bağımlılık aracısını](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) yükleme ve [MMA](how-to-create-group-machine-dependencies.md#install-the-mma)hakkında daha fazla bilgi edinin.
**Log Analytics çalışma alanı** | Çalışma alanı, projeyle aynı abonelikte olmalıdır.<br/><br/> Azure geçişi Doğu ABD, Güneydoğu Asya ve Batı Avrupa bölgelerinde bulunan çalışma alanlarını destekler.<br/><br/>  Çalışma alanının [hizmet eşlemesi desteklendiği](../azure-monitor/insights/vminsights-configure-workspace.md#supported-regions)bir bölgede olması gerekir.<br/><br/> Bir projenin çalışma alanı eklendikten sonra değiştirilemez.
**Maliyet** | Hizmet Eşlemesi çözümü ilk 180 gün boyunca ücret almaz (Log Analytics çalışma alanını projeyle ilişkilendirdiğinizden itibaren)/<br/><br/> 180 günden sonra standart Log Analytics ücretleri uygulanır.<br/><br/> İlişkili Log Analytics çalışma alanında Hizmet Eşlemesi dışında herhangi bir çözümün kullanılması Log Analytics için [Standart ücretler](https://azure.microsoft.com/pricing/details/log-analytics/) doğurur.<br/><br/> Proje silindiğinde, çalışma alanı onunla birlikte silinmez. Projeyi sildikten sonra Hizmet Eşlemesi kullanımı ücretsizdir ve her düğüm, Log Analytics çalışma alanının ücretli katmanına göre ücretlendirilir/<br/><br/>Azure genel kullanım (GA-28 Şubat 2018) geçirmeden önce oluşturduğunuz projeleriniz varsa, ek Hizmet Eşlemesi ücretleri tahakkuk etmeyebilirsiniz. Yalnızca 180 günden sonra ödemeyi sağlamak için, GA 'nin mevcut çalışma alanları Ücretlendirilebilir olmaya devam ettiğinden yeni bir proje oluşturmanızı öneririz.
**Yönetim** | Aracıları çalışma alanına kaydettiğinizde, proje tarafından sunulan KIMLIĞI ve anahtarı kullanırsınız.<br/><br/> Log Analytics çalışma alanını Azure geçişi dışında kullanabilirsiniz.<br/><br/> İlişkili projeyi silerseniz, çalışma alanı otomatik olarak silinmez. [El Ile silin](../azure-monitor/platform/manage-access.md).<br/><br/> Projeyi silmediğiniz takdirde, Azure geçişi tarafından oluşturulan çalışma alanını silmeyin. Bunu yaparsanız, bağımlılık görselleştirme işlevselliği beklendiği gibi çalışmaz.
**İnternet bağlantısı** | Sunucular internet 'e bağlı değilse, bunlara Log Analytics ağ geçidini yüklemeniz gerekir.
**Azure Devlet Kurumları** | Aracı tabanlı bağımlılık Analizi desteklenmez.


## <a name="next-steps"></a>Sonraki adımlar

- Değerlendirme oluşturmak için en iyi uygulamaları [gözden geçirin](best-practices-assessment.md) .
- [VMware değerlendirmesi Için hazırlanma](./tutorial-discover-vmware.md) .