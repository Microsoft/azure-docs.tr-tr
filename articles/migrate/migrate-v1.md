---
title: Azure geçişi 'nin önceki sürümüyle çalışma | Microsoft Docs
description: Azure geçişi 'nin eski sürümüyle çalışma için bir Özet sağlar
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: overview
ms.date: 07/11/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 2c63d63e57a23963f17b6773f244973b051b57eb
ms.sourcegitcommit: a7a9d7f366adab2cfca13c8d9cbcf5b40d57e63a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/20/2019
ms.locfileid: "71162467"
---
# <a name="work-with-the-previous-version-of-azure-migrate"></a>Azure geçişi 'nin önceki sürümüyle çalışma

Bu makalede, Azure geçişi 'nin önceki sürümüyle çalışma hakkında bilgiler sağlanmaktadır.


Azure geçişi hizmetinin iki sürümü vardır:

- **Geçerli sürüm**: Azure geçişi projelerini oluşturmak, şirket içi makineleri yönetmek ve değerlendirmeleri ve geçişleri düzenlemek için bu sürümü kullanın. Bu sürümdeki yenilikler hakkında [daha fazla bilgi edinin](whats-new.md) .
- **Önceki sürüm**: Azure geçişi 'nin önceki sürümünü kullanıyorsanız (yalnızca şirket içi VMware VM 'lerinin değerlendirmesi desteklenir), artık geçerli sürümü kullanmanız gerekir. Yine de önceki sürümde oluşturulan Azure geçiş projelerini kullanmanız gerekiyorsa, bu sizin yapabileceğiniz ve yapameyeceğiniz bir şeydir:
    - Artık geçiş projeleri oluşturamazsınız.
    - Yeni bulmalar gerçekleştirmemenizi öneririz.
    - Mevcut projelere erişmeye devam edebilirsiniz.
    - Değerlendirmeleri çalıştırmaya devam edebilirsiniz.
    

## <a name="upgrade-between-versions"></a>Sürümler arasında yükseltme

Önceki sürümdeki proje veya bileşenleri yeni sürüme yükseltemezsiniz. [Yeni bir Azure geçişi projesi oluşturmanız](how-to-add-tool-first-time.md)ve buna değerlendirme ve geçiş araçları eklemeniz gerekir.

## <a name="find-projects-from-previous-version"></a>Önceki sürümden projeler bulun

Önceki sürümden projeleri şu şekilde bulun:

1. **Tüm hizmetleri**> Azure Portal **Azure geçişi**' ni arayıp seçin. 
2. Azure geçişi panosunda, eski Azure geçiş projelerine erişim için bir bildirim ve bir bağlantı vardır.
3. V1 projelerini açmak için bağlantıya tıklayın.


## <a name="create-an-assessment"></a>Değerlendirme oluşturma

Portalda VM 'Ler bulunduktan sonra bunları gruplandırabilir ve değerlendirme oluşturursunuz.

- Portalda VM 'Ler bulunduktan hemen sonra şirket içi değerlendirmeler olarak hemen oluşturabilirsiniz.
- Performans tabanlı değerlendirmeler için, güvenilir boyut önerileri almak üzere performans tabanlı bir değerlendirme oluşturmadan önce en az bir gün beklemeniz önerilir.

Aşağıdaki gibi bir değerlendirme oluşturun:

1. Projenin **Genel Bakış** sayfasında **+Değerlendirme oluştur**’a tıklayın.
2. Değerlendirme özelliklerini gözden geçirmek için **Tümünü görüntüle**’ye tıklayın.
3. Grubu oluşturun ve bir grup adı belirtin.
4. Gruba eklemek istediğiniz makineleri seçin.
5. Grubu ve değerlendirmeyi oluşturmak için **Değerlendirme Oluştur**’a tıklayın.
6. Değerlendirme oluşturulduktan sonra **Genel Bakış** > **Pano** bölümünde görüntüleyebilirsiniz.
7. Excel dosyası olarak indirmek için **Değerlendirmeyi dışarı aktar**’a tıklayın.

Var olan bir değerlendirmeyi en son performans verileriyle güncelleştirmek isterseniz, değerlendirmeyi güncelleştirmek için **Yeniden Hesapla** komutunu kullanabilirsiniz.

## <a name="review-an-assessment"></a>Değerlendirmeyi gözden geçirme 

Değerlendirme üç aşamaya sahiptir:

- Bir değerlendirme, makinelerin Azure 'da uyumlu olup olmadığını anlamak için uygunluğu bulunan bir analizler ile başlar.
- Boyut tahminleri.
- Aylık maliyet tahmini.

Bir makine, bir öncekini geçerse daha sonraki bir aşamaya geçer. Örneğin, bir makine uygunluk denetiminden başarısız olursa, Azure için uygun değil olarak işaretlenir ve boyutlandırma ve maliyetlendirme yapılmaz.


### <a name="review-azure-readiness"></a>Azure hazırlığını gözden geçirme

Değerlendirmedeki Azure için hazır olma görünümü, her bir sanal makinenin hazır olma durumunu gösterir.

**Hazırlığı** | **State** | **Ayrıntılar**
--- | --- | ---
Azure için hazır | Uyumluluk sorunu yok. Makine Azure 'a olduğu gibi geçirilebilir ve Azure 'da tam Azure desteğiyle önyüklenir. | Azure Geçişi, hazır olan VM’ler için Azure’da bir VM boyutu önerir.
Azure için koşullu olarak hazır | Makine Azure 'da önbaşlatılabilir, ancak tam Azure desteği olmayabilir. Örneğin, Windows Server 'ın Azure 'da desteklenmeyen eski bir sürümü olan bir makine. | Azure geçişi hazırlık sorunlarını açıklar ve düzeltme adımları sağlar.
Azure için hazır değil |  VM, Azure 'da önyüklenmez. Örneğin, bir VM 'nin 4 TB 'den fazla diski varsa, Azure üzerinde barındırılamaz. | Azure geçişi hazırlık sorunlarını açıklar ve düzeltme adımları sağlar.
Hazırlık bilinmiyor | Azure geçişi, genellikle veri kullanılamadığından Azure hazırlığını tanımlayamıyor.


#### <a name="azure-vm-properties"></a>Azure VM özellikleri
Hazır olma durumu, VM 'nin Azure 'da çalıştırılıp çalıştırılamayacağını belirlemek için bir dizi VM özelliği hesaba girer.


**Özellik** | **Ayrıntılar** | **Hazırlığı**
--- | --- | ---
**Önyükleme türü** | BIOS desteklenir. UEFı desteklenmiyor. | Önyükleme türü UEFı ise koşullu olarak hazırlanın.
**Sayısı** | Makine çekirdeği < = bir Azure VM için desteklenen en fazla çekirdek sayısı (128).<br/><br/> Performans geçmişi varsa Azure geçişi, kullanılan çekirdekleri dikkate alır.<br/>Değerlendirme ayarlarında bir rahatlık faktörü belirtilmişse, kullanılan çekirdek sayısı, rahatlık faktörüyle çarpılarak çarpılır.<br/><br/> Hiçbir performans geçmişi yoksa, Azure geçişi,, rahatlık faktörünü uygulamadan ayrılmış çekirdekleri kullanır. | Sınırlara eşit veya daha küçükse hazırlanın.
**Bellek** | Makine bellek boyutu < = bir Azure VM 'si için maksimum bellek (Azure 8 serisi Standard_M128m&nbsp;<sup>2</sup>üzerinde 3892 GB). [Daha fazla bilgi edinin](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).<br/><br/> Performans geçmişi varsa Azure geçişi, kullanılan belleği dikkate alır.<br/><br/>Bir rakip faktörü belirtilmişse, kullanılan bellek, rahatlık faktörüyle çarpılarak çarpılır.<br/><br/> Hiçbir geçmiş yoksa, rahatlık faktörünü uygulamadan ayrılan bellek kullanılır.<br/><br/> | Sınırlar içindeyse hazırlanın.
**Depolama diski** | Bir diskin ayrılan boyutu 4 TB (4096 GB) veya daha az olmalıdır.<br/><br/> Makineye bağlı disk sayısı, işletim sistemi diski dahil 65 veya daha az olmalıdır. | Sınırlar içindeyse hazırlanın.
**Ağ** | Bir makinenin kendisine bağlı 32 veya daha az NIC 'e sahip olması gerekir. | Sınırlar içindeyse hazırlanın.

#### <a name="guest-operating-system"></a>Konuk işletim sistemi

VM özellikleriyle birlikte Azure geçişi, VM 'nin Azure 'da çalıştırıp çalıştıramamakta olup olmadığını belirlemek üzere şirket içi VM 'nin Konuk işletim sistemine de bakar.

- Azure geçişi, vCenter Server belirtilen işletim sistemini dikkate alır.
- Azure geçişi tarafından gerçekleştirilen bulma, Gereç tabanlı olduğundan, VM içinde çalışan işletim sisteminin vCenter Server ' de belirtilen ile aynı olup olmadığını doğrulamak için bir yol yoktur.

Aşağıdaki mantık kullanılır.

**İşletim Sistemi** | **Ayrıntılar** | **Hazırlığı**
--- | --- | ---
Windows Server 2016 ve tüm SPs 'ler | Azure tam destek sağlar. | Azure için hazır
Windows Server 2012 R2 ve tüm SPs 'ler | Azure tam destek sağlar. | Azure için hazır
Windows Server 2012 ve tüm SPs 'ler | Azure tam destek sağlar. | Azure için hazır
Windows Server 2008 R2 ve tüm SPs 'ler | Azure tam destek sağlar.| Azure için hazır
Windows Server 2008 (32-bit ve 64-bit) | Azure tam destek sağlar. | Azure için hazır
Windows Server 2003, 2003 R2 | Destek dışı ve Azure 'da destek için [özel bir destek sözleşmesi (CSA)](https://aka.ms/WSosstatement) gerekiyor. | Azure için koşullu olarak, Azure 'a geçiş yapmadan önce işletim sistemini yükseltmeyi düşünün.
Windows 2000, 98, 95, NT, 3,1, MS-DOS | Destek dışı. Makine Azure 'da önbaşlatılabilir, ancak Azure tarafından bir işletim sistemi desteği sağlanmaz. | Azure için koşullu olarak, Azure 'a geçiş yapmadan önce işletim sistemini yükseltmeniz önerilir.
Windows Istemcisi 7, 8 ve 10 | Azure [yalnızca Visual Studio aboneliği](https://docs.microsoft.com/azure/virtual-machines/windows/client-images) için destek sağlar. | Azure için koşullu olarak hazır
Windows 10 Pro masaüstü | Azure, [çok kiracılı barındırma haklarıyla](https://docs.microsoft.com/azure/virtual-machines/windows/windows-desktop-multitenant-hosting-deployment) destek sağlar. | Azure için koşullu olarak hazır
Windows Vista, XP Professional | Destek dışı. Makine Azure 'da önbaşlatılabilir, ancak Azure tarafından bir işletim sistemi desteği sağlanmaz. | Azure için koşullu olarak, Azure 'a geçiş yapmadan önce işletim sistemini yükseltmeniz önerilir.
Linux | Azure bu [Linux işletim sistemlerini](../virtual-machines/linux/endorsed-distros.md)doğrubir şekilde algılar. Diğer Linux işletim sistemleri Azure 'da önbaşlatılabilir, ancak Azure 'a geçiş yapmadan önce işletim sistemini onaylı bir sürüme yükseltmeniz önerilir. | Sürüm onaylanmış ise Azure için hazırlanın.<br/><br/>Sürüm henüz hazırlanmadığından koşullu olarak kabul edilebilir.
Diğer işletim sistemleri<br/><br/> Örneğin, Oracle Solaris, Apple Mac OS vb., FreeBSD vb. | Azure, bu işletim sistemlerini onaylamaz. Makine Azure 'da başlatılabilir, ancak Azure tarafından bir işletim sistemi desteği sağlanmaz. | Azure için koşullu olarak, Azure 'a geçiş yapmadan önce desteklenen bir işletim sistemi yüklenmesi önerilir.  
VCenter Server içinde **diğeri** olarak belirtilen işletim sistemi | Azure geçişi bu durumda işletim sistemini tanımlayamıyor. | Bilinmeyen hazırlık. VM içinde çalışan işletim sisteminin Azure 'da desteklendiğinden emin olun.
32 bit işletim sistemleri | Makine Azure 'da önbaşlatılabilir, ancak Azure tam destek sunmayabilir. | Azure için koşullu olarak, Azure 'a geçiş yapmadan önce makinenin işletim sistemini 32 bitlik IŞLETIM sisteminden 64 bit işletim sistemine yükseltmeyi düşünün.


### <a name="review-sizing"></a>Boyutlandırmayı gözden geçir

 Azure geçiş boyutu önerisi, değerlendirme özelliklerinde belirtilen boyutlandırma ölçütüne bağlıdır.

- Boyutlandırma performansı tabanlıysa, boyut önerisi VM 'lerin (CPU ve bellek) ve disklerin (ıOPS ve aktarım hızı) performans geçmişini dikkate alır.
- Boyutlandırma ölçütü ' Şirket içi olarak ' ise, Azure 'daki boyut önerisi, şirket içi VM 'nin boyutuna bağlıdır. Disk boyutlandırma, değerlendirme özelliklerinde belirtilen depolama türünü temel alır (varsayılan olarak Premium disklerdir). Azure geçişi, VM ve disklerin performans verilerini dikkate almaz.

### <a name="review-cost-estimates"></a>Maliyet tahminlerini gözden geçirin

Maliyet tahminleri, Azure 'da VM 'Leri çalıştırmanın toplam işlem ve depolama maliyetinin yanı sıra her makinenin ayrıntılarını gösterir.

- Maliyet tahminleri, bir VM makinesi ve disklerinin ve değerlendirme özelliklerinin boyut önerisi kullanılarak hesaplanır.
- İşlem ve depolama için tahmini aylık maliyetler gruptaki tüm VM’ler için birleştirilir.
- Maliyet tahmini, şirket içi VM 'yi Azure hizmet olarak altyapı (IaaS) VM 'Leri olarak çalıştırmaya yöneliktir. Azure geçişi, hizmet olarak platform (PaaS) veya hizmet olarak yazılım (SaaS) maliyetlerine göz önünde bulundurmaz.

### <a name="review-confidence-rating-performance-based-assessment"></a>Güvenilirlik derecelendirmesini gözden geçir (performans tabanlı değerlendirme)

Her performans tabanlı değerlendirme, güvenirlik derecelendirmesi ile ilişkilendirilir.

- Güvenirlik derecelendirmesi bir yıldızdan beş yıldız arasında değişir (en düşük ve beş yıldızlı en yüksek olan bir yıldız).
- Güvenilirlik derecelendirmesi, değerlendirmeyi hesaplamak için gereken veri noktalarının kullanılabilirliğine bağlı olarak bir değerlendirmeye atanır.
- Bir değerlendirmenin güvenilirlik derecesi, Azure Geçişi tarafından sağlanan boyut önerilerinin güvenilirliğini tahmin etmenize yardımcı olur.
- Güvenilirlik derecelendirmesi, "olduğu gibi" Şirket içi değerlendirmelere uygun değildir.

Performans tabanlı boyutlandırma için Azure geçişi için şunlar gerekir:
- CPU kullanım verileri.
- VM bellek verileri.
- SANAL makineye bağlı her disk için, disk ıOPS ve aktarım hızı verilerine ihtiyaç duyuyor.
- Bir sanal makineye bağlı her ağ bağdaştırıcısı için, Azure geçişi ağ girişine/çıktıya ihtiyaç duyuyor.
- Yukarıdakilerden herhangi biri kullanılamıyorsa, boyut önerileri (ve bu nedenle güven derecelendirmeleri) güvenilir olmayabilir.


Kullanılabilir veri noktalarının yüzdesine bağlı olarak, olası güven derecelendirmeleri tabloda özetlenmiştir.

**Veri noktalarının kullanılabilirliği** | **Güvenilirlik derecelendirmesi**
--- | ---
%0-%20 | 1 Yıldız
%21-%40 | 2 Yıldız
%41-%60 | 3 Yıldız
%61-%80 | 4 Yıldız
%81-%100 | 5 Yıldız


#### <a name="assessment-issues-affecting-confidence-ratings"></a>Güvenilirlik derecelendirmelerini etkileyen değerlendirme sorunları

Bir değerlendirme, birkaç nedenden dolayı kullanılabilir tüm veri noktalarına sahip olmayabilir:

- Değerlendirmesinin süresi boyunca ortamınızı profildiniz. Örneğin, performans süresi bir gün olarak ayarlanan değerlendirmesi oluşturursanız, bulmayı başlattıktan en az bir gün veya toplanacak tüm veri noktalarını beklemeniz gerekir.
- Bazı sanal makineler, değerlendirmenin hesaplanmakta olduğu süre boyunca kapatıldı. Sürenin bir parçası için herhangi bir VM kapatılmışsa, Azure geçişi söz konusu dönem için performans verilerini toplayamıyor.
- Değerlendirme hesaplama dönemi boyunca bazı VM 'Ler arasında oluşturulmuştur. Örneğin, geçen aya ait performans geçmişini kullanarak bir değerlendirme oluşturursanız, ancak ortamda hafta önce bir dizi sanal makine oluşturursanız, yeni VM 'lerin performans geçmişi sürenin tamamına katılmaz.

> [!NOTE]
> Herhangi bir değerlendirmenin güvenilirlik derecesi beş yıldızlı altındaysa, gerecin ortamı profili oluşturup en az bir gün bekleyin ve ardından değerlendirmeyi yeniden hesaplayın. Performans tabanlı boyutlandırma, güvenilir olmayabilir. Yeniden hesaplamak istemiyorsanız, değerlendirme özelliklerini değiştirerek şirket içi boyutlandırma olarak ' ye geçmeyi öneririz.



## <a name="create-groups-using-dependency-visualization"></a>Bağımlılık görselleştirmesini kullanarak Grup oluşturma

Grupları el ile oluşturmaya ek olarak, bağımlılık görselleştirmesini kullanarak gruplar oluşturabilirsiniz.
- Bu yöntemi genellikle, bir değerlendirme çalıştırmadan önce, makine bağımlılıklarından daha yüksek düzeyde güvenle grupları değerlendirmek istediğinizde kullanılır.
- Bağımlılık görselleştirme, Azure 'a geçişinizi etkin bir şekilde planlamanız için size yardımcı olabilir. Hiçbir şeyin geri ayrılmadığından ve Azure 'a geçiş yaparken Sürpriz kesintilerinin gerçekleşmemesini sağlamanıza yardımcı olur.
- Birlikte geçirilmesi gereken tüm bağımlı sistemleri bulabilir ve çalışan bir sistemin hala kullanıcılara hizmet verip vermediğini veya geçiş yerine kullanımdan kaldırma için bir aday olduğunu belirlemeniz gerekir.
- Azure geçişi, bağımlılık görselleştirmesini etkinleştirmek için Azure Izleyici 'de Hizmet Eşlemesi çözümünü kullanır.

> [!NOTE]
> Bağımlılık görselleştirmesi Azure Kamu 'da kullanılamaz.

Bağımlılık görselleştirmesini ayarlamak için bir Log Analytics çalışma alanını Azure geçişi projesiyle ilişkilendirirsiniz, bağımlılıklarını görselleştirmek istediğiniz makinelere aracıları yükler ve ardından bağımlılık bilgilerini kullanarak gruplar oluşturursunuz. 



### <a name="associate-a-log-analytics-workspace"></a>Log Analytics çalışma alanını ilişkilendir

Bağımlılık görselleştirmesini kullanmak için bir Log Analytics çalışma alanını bir geçiş projesiyle ilişkilendirirsiniz. Geçiş projesinin oluşturulduğu abonelikte yalnızca bir çalışma alanı oluşturabilir veya ekleyebilirsiniz.

1. Bir projeye Log Analytics çalışma alanı iliştirmek için, **genel bakış**' a, **temelleri**>, **yapılandırma gerekiyor**' a tıklayın.
2. Yeni bir çalışma alanı oluşturabilir veya var olan bir çalışma alanı ekleyebilirsiniz:
  - Yeni bir çalışma alanı oluşturmak için bir ad belirtin. Çalışma alanı, geçiş projesiyle aynı [Azure Coğrafya](https://azure.microsoft.com/global-infrastructure/geographies/) içindeki bir bölgede oluşturulur.
  - Mevcut bir çalışma alanını eklediğinizde, geçiş projesiyle aynı abonelikte bulunan tüm kullanılabilir çalışma alanlarını seçebilirsiniz. Yalnızca, [desteklenen bir hizmet eşlemesi bölgesinde](../azure-monitor/insights/vminsights-enable-overview.md#prerequisites)oluşturulan çalışma alanları listelenir. Bir çalışma alanı eklemek için çalışma alanına ' okuyucu ' erişiminizin olduğundan emin olun.

> [!NOTE]
> Bir geçiş projesiyle ilişkili çalışma alanını değiştiremezsiniz.

### <a name="download-and-install-vm-agents"></a>VM aracılarını indirme ve yükleme

Bir çalışma alanını yapılandırdıktan sonra, değerlendirmek istediğiniz her şirket içi makineye aracıları indirip yüklersiniz. Ayrıca, internet bağlantısı olmayan makineleriniz varsa [Log Analytics ağ geçidini](../azure-monitor/platform/gateway.md) indirip yüklemeniz gerekir.

1. Genel olarak,**makineleri** **Yönet** > ' **e**tıklayın ve gerekli makineyi seçin.
2. **Bağımlılıklar** sütununda, **aracıları yüklensin**' e tıklayın.
3. **Bağımlılıklar** sayfasında, değerlendirmek ISTEDIĞINIZ her VM 'ye Microsoft Monitoring Agent (MMA) ve bağımlılık aracısını indirip yükleyin.
4. Çalışma alanı kimliğini ve anahtarını kopyalayın. Bu, şirket içi makineye MMA 'yı yüklerken gereklidir.

> [!NOTE]
> Aracıların yüklenmesini otomatikleştirmek için, Azure geçişi için bir aracı dağıtım çözümü sağlayan, System Center Configuration Manager gibi bir dağıtım aracını veya bir [ıntigua](https://www.intigua.com/getting-started-intigua-for-azure-migration)gibi bir iş ortağı aracı kullanabilirsiniz.


#### <a name="install-the-mma-agent-on-a-windows-machine"></a>MMA aracısını bir Windows makinesine yükler

Aracıyı bir Windows makinesine yüklemek için:

1. İndirilen aracıya çift tıklayın.
2. **Hoş Geldiniz** sayfasında **İleri**'ye tıklayın. **Lisans Koşulları** sayfasında **Kabul Ediyorum**’a tıklayarak lisansı kabul edin.
3. **Hedef klasörde**, **sonraki**> varsayılan yükleme klasörünü tutun veya değiştirin.
4. **Aracı kurulum seçenekleri**' nde, **Azure Log Analytics** > **İleri**' yi seçin.
5. Yeni bir Log Analytics çalışma alanı eklemek için **Ekle** ' ye tıklayın. Portaldan kopyaladığınız çalışma alanı KIMLIĞINI ve anahtarını yapıştırın. **İleri**’ye tıklayın.

Aracıyı komut satırından veya System Center Configuration Manager gibi otomatikleştirilmiş bir yöntem kullanarak yükleyebilirsiniz. MMA aracısını yüklemek için bu yöntemleri kullanma hakkında [daha fazla bilgi edinin](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#install-and-configure-agent) .

#### <a name="install-the-mma-agent-on-a-linux-machine"></a>Bir Linux makinesine MMA aracısını yükler

Aracıyı bir Linux makinesine yüklemek için:

1. SCP/SFTP kullanarak uygun paketi (x86 veya x64) Linux bilgisayarınıza aktarın.
2. --İnstall bağımsız değişkenini kullanarak paketi yükler.

    ```sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>```

MMA tarafından Linux işletim sistemleri desteğinin listesi hakkında [daha fazla bilgi edinin](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems) .

### <a name="install-the-mma-agent-on-a-machine-monitored-by-operations-manager"></a>Operations Manager tarafından izlenen bir makineye MMA aracısını yükler

System Center Operations Manager 2012 R2 veya üzeriyle izlenen makineler için MMA aracısını yüklemek gerekmiyor. Hizmet Eşlemesi, gerekli bağımlılık verilerini toplamak için Operations Manager MMA ile tümleşir. [Daha fazla bilgi edinin](https://docs.microsoft.com/azure/azure-monitor/insights/service-map-scom#prerequisites). Bağımlılık aracısının yüklenmesi gerekir.

### <a name="install-the-dependency-agent"></a>Bağımlılık aracısını yükleme

1. Bağımlılık aracısını bir Windows makinesine yüklemek için kurulum dosyasına çift tıklayın ve Sihirbazı izleyin.
2. Bir Linux makinesine bağımlılık Aracısı 'nı yüklemek için, aşağıdaki komutu kullanarak kök olarak ' yi kullanın:

    ```sh InstallDependencyAgent-Linux64.bin```

- Windows ve Linux işletim sistemleri için [bağımlılık Aracısı desteği](../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems) hakkında daha fazla bilgi edinin.
- Bağımlılık aracısını yüklemek için betikleri nasıl kullanabileceğiniz hakkında [daha fazla bilgi edinin](../azure-monitor/insights/vminsights-enable-hybrid-cloud.md#installation-script-examples) .

>[!NOTE]
> Sistem önkoşulları ve bağımlılık aracısını dağıtma yöntemlerine genel bir bakış sağlamak için başvurulan VM'ler için Azure İzleyici makale, Hizmet Eşlemesi çözümü için de geçerlidir.

### <a name="create-a-group-with-dependency-mapping"></a>Bağımlılık eşleme ile bir grup oluşturma

1. Aracıları yükledikten sonra portala gidin ve**makineleri** **Yönet** > ' e tıklayın.
2. Aracıları yüklediğiniz makineyi arayın.
3. Makinenin **Bağımlılıklar** sütunu artık **Görünüm bağımlılıkları**olarak gösterilmelidir. Makinenin bağımlılıklarını görüntülemek için sütuna tıklayın.
4. Makinenin bağımlılık eşlemesi aşağıdaki ayrıntıları gösterir:
    - Makineden/makineden gelen (Istemciler) ve giden (sunucu) TCP bağlantısı
        - MMA ve bağımlılık aracısının yüklü olmadığı bağımlı makineler, bağlantı noktası numaralarına göre gruplandırılır.
        - MMA ve bağımlılık aracısının yüklü olduğu bağımlı makineler ayrı kutular olarak gösterilir.
    - İşlem makine içinde çalışır, her makine kutusunu genişleterek süreçlerini görüntüleyebilirsiniz
    - FQDN, işletim sistemi, MAC adresi dahil makine özellikleri gösterilir. Ayrıntıları görüntülemek için her bir makine kutusuna tıklayabilirsiniz.

4. Zaman aralığı etiketindeki zaman süresine tıklayarak farklı zaman sürelerine yönelik bağımlılıkları görüntüleyebilirsiniz. Aralık varsayılan olarak bir saattir. Zaman aralığını değiştirebilir veya başlangıç ve bitiş tarihlerini ve süreyi belirtebilirsiniz.

   > [!NOTE]
   >    Bir saate kadar zaman aralığı desteklenir. Daha uzun bir süre boyunca [bağımlılık verilerini sorgulamak](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) Için Azure izleyici günlüklerini kullanın.

5. Birlikte gruplandırmak istediğiniz bağımlı makineleri tanımladıktan sonra, haritada birden fazla makine seçmek için CTRL + tıklama tuşlarını kullanın ve **Grup makineleri**' ne tıklayın.
6. Bir grup adı belirtin. Bağımlı makinelerin Azure geçişi tarafından bulunduğundan emin olun.

    > [!NOTE]
    > Bağımlı bir makine Azure geçişi tarafından bulunamadıysanız gruba ekleyemezsiniz. Bu tür makineleri gruba eklemek için, bulma işlemini vCenter Server ' de doğru kapsamla yeniden çalıştırmanız ve makinenin Azure geçişi tarafından bulunduğundan emin olmanız gerekir.  

7. Bu grup için bir değerlendirme oluşturmak istiyorsanız, grup için yeni bir değerlendirme oluşturmak üzere onay kutusunu seçin.
8. Grubu kaydetmek için **Tamam** ' ı tıklatın.

Grup oluşturulduktan sonra, aracıların tüm makinelerine yüklenmesi ve tüm grubun bağımlılığını görselleştirerek grubu iyileştirmek önerilir.

## <a name="query-dependency-data-from-azure-monitor-logs"></a>Azure Izleyici günlüklerinden bağımlılık verilerini sorgulama

Hizmet Eşlemesi tarafından yakalanan bağımlılık verileri, Azure geçişi projenizle ilişkili Log Analytics çalışma alanında sorgulama için kullanılabilir. Azure Izleyici günlüklerinde Sorgulanacak Hizmet Eşlemesi veri tabloları hakkında [daha fazla bilgi edinin](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#log-analytics-records) . 

Kusto sorgularını çalıştırmak için:

1. Aracıları yükledikten sonra portala gidin ve **Genel Bakış ' a**tıklayın.
2. **Genel bakış**bölümünde, projenin **temel parçalar** bölümüne gidin ve **OMS çalışma alanının**yanında sunulan çalışma alanı adına tıklayın.
3. Log Analytics çalışma alanı sayfasında **genel** > **Günlükler**' e tıklayın.
4. Azure Izleyici günlüklerini kullanarak bağımlılık verilerini toplamak için sorgunuzu yazın. Sonraki bölümde örnek sorgular bulun.
5. Çalıştır ' a tıklayarak sorgunuzu çalıştırın. 

Kusto sorguları yazma hakkında [daha fazla bilgi edinin](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal) . 

### <a name="sample-azure-monitor-logs-queries"></a>Örnek Azure Izleyici günlükleri sorguları

Bağımlılık verilerini ayıklamak için kullanabileceğiniz örnek sorgular aşağıda verilmiştir. Tercih ettiğiniz veri noktalarını ayıklamak için sorguları değiştirebilirsiniz. Bağımlılık veri kayıtlarında alanların ayrıntılı bir listesi [burada](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#log-analytics-records)bulunabilir. Daha fazla örnek sorgu [burada](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#sample-log-searches)bulabilirsiniz.

#### <a name="summarize-inbound-connections-on-a-set-of-machines"></a>Bir makine kümesi üzerinde gelen bağlantıları özetleme

Bağlantı ölçümleri, VMConnection için tablodaki kayıtlar, bağımsız fiziksel ağ bağlantılarını temsil etmez. Birden çok fiziksel ağ bağlantısı mantıksal bir bağlantı halinde gruplandırılır. Fiziksel ağ bağlantısı verilerinin, VMConnection 'daki tek bir mantıksal kayıtta nasıl toplandığından [daha fazla bilgi edinin](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#connections) . 

```
// the machines of interest
let ips=materialize(ServiceMapComputer_CL
| summarize ips=makeset(todynamic(Ipv4Addresses_s)) by MonitoredMachine=ResourceName_s
| mvexpand ips to typeof(string));
let StartDateTime = datetime(2019-03-25T00:00:00Z);
let EndDateTime = datetime(2019-03-30T01:00:00Z); 
VMConnection
| where Direction == 'inbound' 
| where TimeGenerated > StartDateTime and TimeGenerated  < EndDateTime
| join kind=inner (ips) on $left.DestinationIp == $right.ips
| summarize sum(LinksEstablished) by Computer, Direction, SourceIp, DestinationIp, DestinationPort
```

#### <a name="summarize-volume-of-data-sent-and-received-on-inbound-connections-between-a-set-of-machines"></a>Bir makine kümesi arasındaki gelen bağlantılarda gönderilen ve alınan verilerin hacmini özetleme

```
// the machines of interest
let ips=materialize(ServiceMapComputer_CL
| summarize ips=makeset(todynamic(Ipv4Addresses_s)) by MonitoredMachine=ResourceName_s
| mvexpand ips to typeof(string));
let StartDateTime = datetime(2019-03-25T00:00:00Z);
let EndDateTime = datetime(2019-03-30T01:00:00Z); 
VMConnection
| where Direction == 'inbound' 
| where TimeGenerated > StartDateTime and TimeGenerated  < EndDateTime
| join kind=inner (ips) on $left.DestinationIp == $right.ips
| summarize sum(BytesSent), sum(BytesReceived) by Computer, Direction, SourceIp, DestinationIp, DestinationPort
```


## <a name="next-steps"></a>Sonraki adımlar
Azure geçişi 'nin en son sürümü [hakkında bilgi edinin](migrate-services-overview.md) .
