---
title: Veead ile verilerinizi Azure 'a yedekleyin
titleSuffix: Azure Storage
description: Göz önünde bulundurulması gereken faktörlere ve Azure 'un depolama hedefi ve kurtarma konumu olarak, Veead yedeklemesi ve kurtarması
author: karauten
ms.author: karauten
ms.date: 03/15/2021
ms.topic: conceptual
ms.service: storage
ms.subservice: partner
ms.openlocfilehash: 0b8bc0defd3314fcff691a049323201732644ff3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104589914"
---
# <a name="backup-to-azure-with-veeam"></a>Veead ile Azure 'a yedekleme

Bu makale, bir Veead altyapısını Azure Blob depolama ile tümleştirmenize yardımcı olur. Önkoşullar, önemli noktalar, uygulama ve operasyonel kılavuz içerir. Bu makalede, birincil sitenizde normal işlemi önleyen bir olağanüstü durum oluşursa, site dışı bir yedekleme hedefi ve bir kurtarma sitesi olarak Azure kullanımı ele alınmaktadır.

> [!NOTE]
> Veead, daha düşük bir kurtarma süresi hedefi (RTO) çözümü, Veead çoğaltması de sunmaktadır. Bu çözüm, bir Azure üretim ortamında olağanüstü bir durum oluşması durumunda daha hızlı kurtarmanıza yardımcı olabilecek bir bekleme sanal makinesine sahip olmanızı sağlar. Veead Ayrıca Azure ve Office 365 kaynaklarını yedeklemek için adanmış araçlara sahiptir. Bu yetenekler, bu belgenin kapsamı dışındadır.

## <a name="reference-architecture"></a>Başvuru mimarisi

Aşağıdaki diyagramda, şirket içi Azure 'a ve Azure 'da dağıtımlara yönelik bir başvuru mimarisi sunulmaktadır.

![Azure Reference mimari diyagramına veead.](../media/veeam-architecture.png)

Mevcut Veead dağıtımınız, bir Azure depolama hesabı veya birden çok hesabı bir bulut yedekleme deposu olarak ekleyerek Azure ile kolayca tümleştirilebilir. Veeab Ayrıca Azure 'daki şirket içi yedeklemeleri kurtarmanızı sağlayarak Azure 'da isteğe bağlı kurtarma sitesi sağlar.

## <a name="veeam-interoperability-matrix"></a>Veead birlikte çalışabilirlik matrisi

| İş Yükü | GPv2 ve BLOB depolama | Cool katman desteği | Arşiv katmanı desteği | Data Box aile desteği |
|-----------------------|--------------------|--------------------|-------------------------|-------------------------|
| Şirket içi VM 'Ler/veriler | v10a | v10a | Yok | 10A<sup>*</sup> |
| Azure VM’leri | v10a | v10a | Yok | 10A<sup>*</sup> |
| Azure Blob | v10a | v10a | Yok | 10A<sup>*</sup> |
| Azure Dosyaları | v10a | v10a | Yok | 10A<sup>*</sup> |

<sup>*</sup>Veeab yedekleme ve çoğaltma desteği yalnızca Azure Data Box için REST API. Bu nedenle, Azure Data Box Disk desteklenmez.

## <a name="before-you-begin"></a>Başlamadan önce

Küçük bir ön planlama, Azure 'u site dışı yedekleme hedefi ve kurtarma sitesi olarak kullanmanıza yardımcı olur.

### <a name="get-started-with-azure"></a>Azure'ı kullanmaya başlayın

Microsoft, Azure kullanmaya başlamanızı sağlamak için izlenecek bir çerçeve sunar. [Bulut benimseme çerçevesi](/azure/architecture/cloud-adoption/) (CAF), bir üretim sınıfı bulutu benimseme planlaması için kurumsal dijital dönüştürmeye ve kapsamlı kılavuza yönelik ayrıntılı bir yaklaşımdır. CAF, hızlı ve güvenli bir şekilde çalışmaya başlamanıza yardımcı olacak adım adım bir [Azure Kurulum Kılavuzu](/azure/cloud-adoption-framework/ready/azure-setup-guide/) içerir. [Azure Portal](https://portal.azure.com/?feature.quickstart=true#blade/Microsoft_Azure_Resources/QuickstartCenterBlade)etkileşimli bir sürüm bulabilirsiniz. Örnek mimarilerin, uygulama dağıtmaya yönelik belirli en iyi uygulamaları ve Azure uzmanlığına yola koymak için ücretsiz eğitim kaynaklarını bulacaksınız.

### <a name="consider-the-network-between-your-location-and-azure"></a>Konumunuz ile Azure arasında ağı değerlendirin

Bulut kaynaklarını üretim, test ve geliştirme çalıştırmak için ya da bir yedekleme hedefi ve kurtarma sitesi olarak kullanıp kullanmayacağınızı, ilk yedekleme dengeli dağıtımı ve devam eden gündelik aktarımlar için bant genişliği ihtiyaçlarınızı anlamak önemlidir.

Azure Data Box, ilk yedekleme temelinizi daha fazla bant genişliği gerekmeden Azure 'a aktarmaya yönelik bir yol sağlar. Taban çizgisi aktarımının tahmin edebileceğinden daha uzun sürme tahmini olması durumunda bu faydalıdır. İlk yedeklemenizi aktarmak için gereken süreyi tahmin etmek üzere bir depolama hesabı oluşturduğunuzda veri aktarımı tahmin aracı 'ı kullanabilirsiniz.

![Portalda Azure depolama veri aktarımı tahmin Aracı ' i gösterir.](../media/az-storage-transfer.png)

, Üretim uygulamalarını etkilemeden gerekli aktarım penceresinde (yedekleme penceresi) günlük veri aktarımlarını desteklemek için yeterli ağ kapasitesi gerekeceğini unutmayın. Bu bölümde, ağ ihtiyaçlarınızı değerlendirmek için kullanılabilen araçlar ve teknikler özetlenmektedir.

#### <a name="determine-how-much-bandwidth-youll-need"></a>Ne kadar bant genişliğine ihtiyacınız olacağını belirleme

Azure 'a ilk temel aktarım için değişiklik oranını ve toplam yedekleme kümesi boyutunu tespit etmek üzere birden çok değerlendirme seçeneği kullanılabilir. Değerlendirme ve raporlama araçlarının bazı örnekleri aşağıda verilmiştir:

- [MiTrend](https://mitrend.com/)
- [Apt,](https://www.veritas.com/insights/aptare-it-analytics)
- [Veri Voss](https://www.datavoss.com/)

#### <a name="determine-unutilized-internet-bandwidth"></a>Unutilized internet bant genişliğini belirleme

Günde bir gün içinde (veya *yer* alan) bant genişliğinin ne kadar uygun olduğunu bilmeniz önemlidir. Bu, için hedeflerinizi karşılayıp karşılamadığını değerlendirmenize yardımcı olur:

- çevrimdışı dengeli dağıtım için Azure Data Box kullanmadığınız ilk karşıya yükleme zamanı
- daha önce tanımlanan değişiklik hızına ve yedekleme pencerenizi göre günlük yedeklemeler Tamamlanıyor

Azure 'a yedeklemelerinizin tükettiği bant genişliği yer odasını belirlemek için aşağıdaki yöntemleri kullanın.

- Mevcut bir Azure ExpressRoute müşterisiyseniz, [devre kullanımınızı](../../../../../expressroute/expressroute-monitoring-metrics-alerts.md#circuits-metrics) Azure Portal görüntüleyin.
- ISS 'nize başvurun. Bu kişiler, mevcut günlük ve aylık kullanımlarınızı gösteren raporlar paylaşamazlar.
- Ağ trafiğinizi yönlendirici/anahtar düzeyinde izleyerek kullanımı ölçebilir çeşitli araçlar vardır. Bu modüller şunlardır:

  - [Solarwinds bant genişliği çözümleyici paketi](https://www.solarwinds.com/network-bandwidth-analyzer-pack?CMP=ORG-BLG-DNS)
  - [Paessler PRTG](https://www.paessler.com/bandwidth_monitoring)
  - [Cisco Ağ Yardımcısı](https://www.cisco.com/c/en/us/products/cloud-systems-management/network-assistant/index.html)
  - [WhatsUp Gold](https://www.whatsupgold.com/network-traffic-monitoring)

### <a name="choose-the-right-storage-options"></a>Doğru depolama seçeneklerini belirleyin

Azure 'U bir yedekleme hedefi olarak kullandığınızda [Azure Blob depolama alanını](../../../../blobs/storage-blobs-introduction.md)kullanacaksınız. BLOB depolama, Microsoft 'un nesne depolama çözümüdür. BLOB depolama, herhangi bir veri modeli veya tanımına bağlı olmayan çok büyük miktarlarda yapılandırılmamış verileri depolamak için iyileştirilmiştir. Ayrıca, Azure Storage dayanıklı, yüksek oranda kullanılabilir, güvenli ve ölçeklenebilir. İş yükünüz için doğru depolamayı, iç SLA 'larınızı karşılamak için [esneklik düzeyi](../../../../common/storage-redundancy.md) sağlamak üzere seçebilirsiniz. BLOB depolama, kullanım başına ödeme hizmetidir. Depolanan veri miktarı, bu verilere erişim ve seyrek erişimli ve arşiv katmanları söz konusu olduğunda, gereken en düşük saklama süresi için [aylık olarak ücretlendirilirsiniz](../../../../blobs/storage-blob-storage-tiers.md#pricing-and-billing) . Yedekleme verileri için geçerli olan dayanıklılık ve katmanlama seçenekleri aşağıdaki tablolarda özetlenmektedir.

**BLOB depolama dayanıklılığı seçenekleri:**

|  |Yerel olarak yedekli  |Bölge yedekli  |Coğrafi olarak yedekli  |Coğrafi bölge-yedekli  |
|---------|---------|---------|---------|---------|
|**Geçerli kopya sayısı**     | 3         | 3         | 6         | 6 |
|**kullanılabilirlik bölgesi sayısı**     | 1         | 3         | 2         | 4 |
|**bölge** sayısı     | 1         | 1         | 2         | 2 |
|**İkincil bölgeye el ile yük devretme**     | Yok         | Yok         | Evet         | Yes |

**BLOB depolama katmanları:**

|  | Etkin katman   |Cool katmanı   | Arşiv katmanı |
| ----------- | ----------- | -----------  | -----------  |
| **Kullanılabilirlik** | %99,9         | %99         | Çevrimdışı      |
| **Kullanım ücretleri** | Daha yüksek depolama maliyetleri, daha düşük erişim ve işlem maliyetleri | Daha düşük depolama maliyetleri, daha yüksek erişim ve işlem maliyetleri | En düşük depolama maliyetleri, en yüksek erişim ve işlem maliyetleri |
| **Gerekli en düşük veri saklama** | NA | 30 gün | 180 gün |
| **Gecikme süresi (ilk bayta kalan süre)** | Mayacak | Mayacak | Saat |

#### <a name="sample-backup-to-azure-cost-model"></a>Azure maliyet modeline örnek yedekleme

Kullanım başına ödeme sayesinde, buluta yeni eklenen müşterilere karşı geçiş yapabilirsiniz. Yalnızca kullanılan kapasite için ödeme yaparken, Azure Express Route Direct yerel veya Express Route 'a yönelik işlemler (okuma ve yazma) ve [Çıkış](https://azure.microsoft.com/pricing/details/bandwidth/) işlemleri için de ödeme yaparsınız. Ayrıca, Azure [Express Route Direct yerel veya Express Route sınırsız veri planı](https://azure.microsoft.com/pricing/details/expressroute/) dahil değildir. [Azure Fiyatlandırma Hesaplayıcı](https://azure.microsoft.com/pricing/calculator/) ' nı kullanarak "ne tür" analizlerini gerçekleştirebilirsiniz. Analizi, liste fiyatlandırması veya [Azure Depolama ayrılmış kapasitesi fiyatlandırmasına](../../../../../cost-management-billing/reservations/save-compute-costs-reservations.md)dayandırın %38 tasarruf elde edebilir. Azure 'a yedekleme için aylık maliyet maliyetini modelleyerek örnek bir fiyatlandırma alıştırması aşağıda verilmiştir. Bu yalnızca bir örnektir. *Ücretleriniz, burada yakalanmayan etkinliklerden dolayı farklılık gösterebilir.*

|Maliyet faktörü  |Aylık maliyet  |
|---------|---------|
|Seyrek Erişimli depolamada 100 TB yedekleme verisi     |$1556,48         |
|2 TB gün başına yazılan yeni veri x 30 gün     |işlemlerde $72          |
|Aylık tahmini toplam     |$1628,48         |
|---------|---------|
|Ortak internet üzerinden şirket içi olarak 5 TB 'lık bir kez geri yükleme   | $527,26         |

> [!Note]
> Bu tahmin, Azure fiyatlandırma hesaplayıcıda Doğu ABD Kullandıkça Öde fiyatlandırması kullanılarak oluşturulmuştur ve WAN aktarımları için 256 KB 'lık yığın boyutu varsayılanını temel alır. Bu örnek, gereksinimlerinize uygun olmayabilir.

## <a name="implementation-guidance"></a>Uygulama Kılavuzu

Bu bölümde, şirket içi Veead dağıtımına Azure Storage ekleme hakkında kısa bir kılavuz sunulmaktadır. Ayrıntılı yönergeler ve planlama konuları için bkz. [Veead Cloud Connect Backup Kılavuzu](https://helpcenter.veeam.com/docs/backup/cloud/cloud_backup.html?ver=100).

1. Azure portal açın ve **depolama hesapları** için arama yapın. Varsayılan hizmet simgesine de tıklayabilirsiniz.

      ![Azure portal bir depolama hesabı eklemeyi gösterir.](../media/azure-portal.png)

      ![Azure portal arama kutusuna depolamayı yazdığınız yeri gösterir.](../media/locate-storage-account.png)

2. Hesap eklemek için **Oluştur** ' u seçin. Bir kaynak grubu seçin veya oluşturun, benzersiz bir ad sağlayın, bölgeyi seçin, **Standart** performans ' ı seçin, her zaman hesap türünü **depolama v2** olarak bırakın, SLA 'larınızı karşılayan çoğaltma düzeyini ve yedekleme yazılımınızın uygulanacağı varsayılan katmanı seçin. Azure depolama hesabı, tek bir hesap içinde sık erişimli, seyrek erişimli ve arşiv katmanları sağlar ve Veeab ilkeleri, verilerinizin yaşam döngüsünü etkin bir şekilde yönetmek için birden fazla katman kullanmanıza olanak sağlar.

    ![Portalda depolama hesabı ayarlarını gösterir](../media/account-create-1.png)

3. Varsayılan ağ seçeneklerini şimdilik tutun ve **veri korumasına** geçin. Burada, yanlışlıkla silinen bir yedekleme dosyasını tanımlanan saklama süresi içinde kurtarmanıza ve yanlışlıkla veya kötü amaçlı silmeye karşı koruma sağlamasına olanak sağlayan geçici silme özelliğini etkinleştirebilirsiniz.

    ![Portalda veri koruma ayarlarını gösterir.](../media/account-create-2.png)

4. Daha sonra, Azure kullanım örneklerine yedekleme için **Gelişmiş** ekranın varsayılan ayarlarını öneririz.

    ![Portalda Gelişmiş ayarlar sekmesini gösterir.](../media/account-create-3.png)

5. Etiketleme kullanırsanız ve hesabınızı oluşturduğunuzda kuruluş için Etiketler ekleyin.

6. Hesabı Veead ortamınıza ekleyebilmeniz için hemen gerekli olan iki hızlı adım vardır. Azure portal oluşturduğunuz hesaba gidin ve **BLOB hizmeti** menüsü altında **kapsayıcılar** ' ı seçin. Kapsayıcı ekleyin ve anlamlı bir ad seçin. Ardından **Ayarlar** altındaki **erişim tuşları** öğesine gidin ve **depolama hesabı adını** ve iki erişim tuşlarından birini kopyalayın. Sonraki adımlarda kapsayıcı adı, hesap adı ve erişim anahtarına ihtiyaç duyarsınız.

    ![Portalda kapsayıcı oluşturmayı gösterir.](../media/container-b.png)

    ![Portalda erişim anahtarı ayarlarını gösterir.](../media/access-key.png)

    > [!Note]
    > Veeab yedekleme ve çoğaltma, Azure 'a bağlanmak için ek seçenekler sunar. Bu makalenin kullanım durumu için, bir yedekleme hedefi olarak Microsoft Azure Blob depolamayı kullanarak, yukarıdaki yöntemi kullanmak önerilen en iyi uygulamadır.

7. *(Isteğe bağlı)* Dağıtımınıza daha fazla güvenlik katmanı ekleyebilirsiniz.

     1. Depolama hesabınızda kimlerin değişiklik yapacağına izin vermek için rol tabanlı erişimi yapılandırın. Daha fazla bilgi için bkz. [yönetim işlemleri Için yerleşik roller](../../../../common/authorization-resource-provider.md#built-in-roles-for-management-operations).

     1. Şirket ağınızın dışından erişim girişimlerini engellemek için, hesap erişimini [depolama Güvenlik Duvarı ayarlarıyla](../../../../common/storage-network-security.md) belirli ağ kesimleriyle kısıtlayın.

        ![Portalda depolama güvenlik duvarı ayarlarını gösterir.](../media/storage-firewall.png)

     1. Depolama hesabının yanlışlıkla silinmesini engellemek için hesapta bir [silme kilidi](../../../../../azure-resource-manager/management/lock-resources.md) ayarlayın.

        ![Kaynak kilidi](../media/resource-lock.png)

     1. Ek [güvenlik en iyi yöntemlerini](../../../../../storage/blobs/security-recommendations.md)yapılandırın.

8. Veahar yedekleme ve Çoğaltma Yönetimi konsolunda, **Yedekleme altyapısı** ' na gidin > genel bakış bölmesine sağ tıklayın ve Yapılandırma Sihirbazı 'nı açmak Için **yedekleme deposu Ekle** ' yi seçin. İletişim kutusunda, **nesne depolama**  ->  **Microsoft Azure Blob depolama**  ->  **Azure Blob depolama**' yı seçin.

    ![Veead deposu sihirbazında nesne depolamayı seçmeyi gösterir.](../media/veeam-repo-a.png)

    ![Veead deposu sihirbazında Microsoft Azure Blob depolamayı seçmeyi gösterir.](../media/veeam-repo-b.png)

    ![Veead deposu sihirbazında Azure Blob depolamayı seçmeyi gösterir.](../media/veeam-repo-c.png)

9. Ardından, yeni BLOB depolama deponuzdan bir ad ve açıklama belirtin.

    ![Veead deposu sihirbazında depo için bir ad yazmayı gösterir.](../media/veeam-repo-d.png)

10. Sonraki adımda, Azure depolama hesabınıza erişmek için kimlik bilgilerini ekleyin. Bulut kimlik bilgileri yöneticisinde **Microsoft Azure depolama hesabı** ' nı seçin ve depolama hesabı adınızı ve erişim anahtarınızı girin. Bölge seçicisinde **Azure Global** ' i ve varsa herhangi bir ağ geçidi sunucusunu seçin.

    ![Veead deposu sihirbazında bir hesap belirtmeyi gösterir.](../media/veeam-repo-e.png)

    > [!Note]
    > Veead Gateway sunucusu kullanmayı tercih ederseniz, tüm genişleme depo kapsamlarının doğrudan internet erişimine sahip olduğundan emin olun.

11. Kapsayıcı kaydı ' nda, Azure depolama kapsayıcınızı seçin ve yedeklemelerinizi depolamak için bir klasör seçin veya oluşturun. Ayrıca, bu önerilir, Veead tarafından kullanılacak genel depolama kapasitesi üzerinde bir geçici sınır tanımlayabilirsiniz. Özet bölümünde görünen bilgileri gözden geçirin ve yapılandırma aracını doldurun. Artık yedekleme işi yapılandırmanızda yeni depoyu seçebilirsiniz.

    ![Veead deposu sihirbazında bir kapsayıcı belirtmeyi gösterir.](../media/veeam-repo-f.png)

    ![Veead deposu sihirbazında bir klasör oluşturmayı gösterir.](../media/veeam-repo-g.png)

## <a name="operational-guidance"></a>İşletimsel kılavuz

### <a name="azure-alerts-and-performance-monitoring"></a>Azure uyarıları ve performans izleme

Yedeklemelerinizi depolamak için kullandığınız herhangi bir depolama hedefi ile yaptığınız gibi, hem Azure kaynaklarınızın hem de aynı olanlarınızın onlardan faydalanma yeteneğini izlemeniz önerilir. Azure Izleyici ve Veeam 'nin izleme özellikleri (Veeam yönetim konsolunun **işler** düğümündeki **Istatistikler** sekmesi veya Veeam ONE Reporter gibi daha gelişmiş seçenekler), ortamınızı sağlıklı tutmanıza yardımcı olur.

#### <a name="azure-portal"></a>Azure portalı

Azure, [Azure izleyici](../../../../../azure-monitor/essentials/monitor-azure-resource.md)biçiminde güçlü bir izleme çözümü sağlar. Azure Izleyici 'yi Azure depolama kapasitesini, işlemleri, kullanılabilirliği, kimlik doğrulamasını ve daha fazlasını izlemek için [yapılandırabilirsiniz](../../../../blobs/monitor-blob-storage.md) . İzlenen ölçümlerin tam başvurusu [burada](../../../../blobs/monitor-blob-storage-reference.md)bulunabilir. İzleme için birkaç faydalı ölçüm, BlobCapacity ' dir-en yüksek [depolama hesabı kapasite sınırının](../../../../common/scalability-targets-standard-account.md)altında kalmakta ve Azure depolama hesabınızda okunan veri miktarını Izlemek için çıkış yapın ve Azure Storage ve mediaagent 'a giden ve giden isteklerin gidiş dönüş süresini Izlemek için SuccessE2ELatency.

Azure depolama hizmeti durumunu izlemek ve istediğiniz zaman [Azure durum panosunu](https://status.azure.com/status) görüntülemek için [günlük uyarıları da oluşturabilirsiniz](../../../../../service-health/alerts-activity-log-service-notifications-portal.md) .

#### <a name="veeam-reporting"></a>Veead raporlama

- [Veead bir raporlamayı yapılandırma](https://helpcenter.veeam.com/docs/one/reporter/configure_reporter.html?ver=100)
- [Veeab yedekleme ve çoğaltma alarmları](https://helpcenter.veeam.com/docs/one/monitor/backup_alarms.html?ver=100)

### <a name="how-to-open-support-cases"></a>Destek çalışmalarını açma

Azure çözümüne yedeklemeniz için yardıma ihtiyacınız olduğunda, hem Veead hem de Azure ile bir servis talebi açmanız gerekir. Bu, destek kuruluşlarımızın gerektiğinde işbirliği yapmasına yardımcı olur.

#### <a name="to-open-a-case-with-veeam"></a>Veead ile bir servis talebi açmak için

[Veeab müşteri destek sitesinde](https://www.veeam.com/support.html)oturum açın ve bir servis talebi açın.

Veead tarafından sunulan destek seçeneklerini anlamak için, [Veeae müşteri desteği ilkesine](https://www.veeam.com/veeam_software_support_policy_ds.pdf)bakın.

Ayrıca, bir servis talebi açmak için çağırabilirsiniz: [dünya genelindeki destek numaraları](https://www.veeam.com/contacts.html?ad=in-text-link#support-numbers)

#### <a name="to-open-a-case-with-azure"></a>Azure ile bir servis talebi açmak için

[Azure Portal](https://portal.azure.com) en üstteki arama çubuğunda **destek** için arama yapın. **Yardım ve destek**  ->  **Yeni destek isteği ' ni** seçin.

> [!NOTE]
> Bir servis talebi açtığınızda, Azure depolama veya Azure ağı ile ilgili yardıma ihtiyacınız olduğundan emin olun. Azure Backup belirtmeyin. Azure Backup bir Azure hizmeti adıdır ve servis talebi yanlış yönlendirilir.

### <a name="links-to-relevant-veeam-documentation"></a>İlgili Veead belgelerinin bağlantıları

Daha ayrıntılı bilgi için aşağıdaki Veeab belgelerine bakın:

- [Veeab Kullanıcı Kılavuzu](https://helpcenter.veeam.com/docs/backup/hyperv/overview.html?ver=100)
- [Veead mimari Kılavuzu](https://helpcenter.veeam.com/docs/backup/vsphere/backup_architecture.html?ver=100)

### <a name="marketplace-offerings"></a>Market teklifleri

Azure üzerinde çalışan iş yüklerinizi korumak için bildiğiniz ve güvendiğiniz Veeam çözümünü kullanmaya devam edebilirsiniz. Veead, çözümlerini Azure 'a dağıtmayı ve Azure sanal makinelerini ve diğer birçok Azure hizmetini korumanıza yardımcı oldu.

- [Azure Marketi aracılığıyla Veead yedekleme & çoğaltmasını dağıtma](https://azuremarketplace.microsoft.com/marketplace/apps/veeam.veeam-backup-replication?tab=overview)
- [Veeam'nin Azure yedekleme ve kurtarma web sitesi](https://www.veeam.com/backup-azure.html?ad=menu-products)

## <a name="next-steps"></a>Sonraki adımlar

Özelleştirilmiş kullanım senaryoları hakkında bilgi için Veeab Web sitesinde aşağıdaki kaynaklara bakın:

- [Veead videoları](https://www.veeam.com/how-to-videos.html?ad=menu-resources)
- [Veeab teknik belgeleri](https://www.veeam.com/documentation-guides-datasheets.html?ad=menu-resources)
- [Veeab Bilgi Bankası ve SSS](https://www.veeam.com/knowledge-base.html?ad=menu-resources)