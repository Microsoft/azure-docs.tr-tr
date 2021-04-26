---
title: Commkasasıyla verilerinizi Azure 'a yedekleyin
titleSuffix: Azure Storage
description: Azure 'un bir depolama hedefi ve kurtarma konumu olarak Azure kullanımı için gereken etkenlere ve izlenecek adımlara genel bir bakış sağlar. yedekleme ve kurtarma
author: karauten
ms.author: karauten
ms.date: 03/15/2021
ms.topic: conceptual
ms.service: storage
ms.subservice: partner
ms.openlocfilehash: fa60b6f002e49babc1e1f014bcb941e7953a43a8
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107484787"
---
# <a name="backup-to-azure-with-commvault"></a>Commkasasıyla Azure 'da yedekleme

Bu makale, Azure Blob depolama ile bir Commkasa altyapısını tümleştirmenize yardımcı olur. Önkoşullar, önemli noktalar, uygulama ve operasyonel kılavuz içerir. Bu makalede, birincil sitenizde normal işlemi önleyen bir olağanüstü durum oluşursa, site dışı bir yedekleme hedefi ve bir kurtarma sitesi olarak Azure kullanımı ele alınmaktadır.

> [!NOTE]
> Commkasa, daha düşük bir kurtarma süresi hedefi (RTO) çözümü, Commkasasel canlı eşitleme sağlar. Bu çözüm, bir Azure üretim ortamında olağanüstü bir durum oluşması durumunda daha hızlı kurtarmanıza yardımcı olabilecek bir bekleme sanal makinesine sahip olmanızı sağlar. Bu yetenekler, bu belgenin kapsamı dışındadır.

## <a name="reference-architecture"></a>Başvuru mimarisi

Aşağıdaki diyagramda, şirket içi Azure 'a ve Azure 'da dağıtımlara yönelik bir başvuru mimarisi sunulmaktadır.

![Azure başvuru mimarisine yönelik commkasadan](../media/commvault-diagram.png)

Mevcut bir Azure depolama hesabı veya birden çok hesap ekleyerek bulut depolama hedefi olarak Azure ile kolayca tümleştirilebilir. Ayrıca, Azure 'da şirket içi kurtarma sitesi sunarak Azure 'daki yedeklemeleri kurtarmanızı sağlar.

## <a name="commvault-interoperability-matrix"></a>Commkasası birlikte çalışabilirlik matrisi

| İş Yükü | GPv2 ve BLOB depolama | Cool katman desteği | Arşiv katmanı desteği | Data Box aile desteği |
|-----------------------|--------------------|--------------------|-------------------------|-------------------------|
| Şirket içi VM 'Ler/veriler | v 11,5 lik | v 11,5 lik | v 11.10 | v 11.10 |
| Azure VM’leri | v 11,5 lik | v 11,5 lik | v 11,5 lik | Yok |
| Azure Blob | v 6x | v 6x | v 6x | Yok |
| Azure Dosyaları | v 6x | v 6x | v 6x | Yok |

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

Ne kadar bant genişliğine ihtiyacınız olacağını öğrenmek için aşağıdaki kaynakları kullanın:

- Yedekleme yazılımınızdan raporlar.
- Commkasa, Azure 'a ilk temel aktarım için [değişiklik oranını](https://documentation.commvault.com/commvault/v11_sp19/article?p=39699.htm) ve [Toplam yedekleme kümesi boyutunu](https://documentation.commvault.com/commvault/v11_sp19/article?p=39621.htm) belirlemede Standart raporlar sağlar.
- Yazılım bağımsız değerlendirmesi ve raporlama araçlarını yedekleme:
  - [MiTrend](https://mitrend.com/)
  - [Aptları](https://www.veritas.com/insights/aptare-it-analytics)
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
|**bölge sayısı**     | 1         | 1         | 2         | 2 |
|**İkincil bölgeye el ile yük devretme**     | Yok         | Yok         | Evet         | Yes |

**BLOB depolama katmanları:**

|  | Etkin katman   |Cool katmanı   | Arşiv katmanı |
| ----------- | ----------- | -----------  | -----------  |
| **Kullanılabilirlik** | %99,9         | %99         | Çevrimdışı      |
| **Kullanım ücretleri** | Daha yüksek depolama maliyetleri, daha düşük erişim ve işlem maliyetleri | Daha düşük depolama maliyetleri, daha yüksek erişim ve işlem maliyetleri | En düşük depolama maliyetleri, en yüksek erişim ve işlem maliyetleri |
| **Gerekli en düşük veri saklama**| Yok | 30 gün | 180 gün |
| **Gecikme süresi (ilk bayta kalan süre)** | Mayacak | Mayacak | Saat |

#### <a name="sample-backup-to-azure-cost-model"></a>Azure maliyet modeline örnek yedekleme

Kullanım başına ödeme sayesinde, buluta yeni eklenen müşterilere karşı geçiş yapabilirsiniz. Yalnızca kullanılan kapasite için ödeme yaparken, Azure Express Route Direct yerel veya Express Route 'a yönelik işlemler (okuma ve yazma) ve [Çıkış](https://azure.microsoft.com/pricing/details/bandwidth/) işlemleri için de ödeme yaparsınız. Ayrıca, Azure [Express Route Direct yerel veya Express Route sınırsız veri planı](https://azure.microsoft.com/pricing/details/expressroute/) dahil değildir. [Azure Fiyatlandırma Hesaplayıcı](https://azure.microsoft.com/pricing/calculator/) ' nı kullanarak "ne tür" analizlerini gerçekleştirebilirsiniz. Analizi, liste fiyatlandırması veya [Azure Depolama ayrılmış kapasitesi fiyatlandırmasına](../../../../../cost-management-billing/reservations/save-compute-costs-reservations.md)dayandırın %38 tasarruf elde edebilir. Azure 'a yedekleme için aylık maliyet maliyetini modelleyerek örnek bir fiyatlandırma alıştırması aşağıda verilmiştir. Bu yalnızca bir örnektir. *Ücretleriniz, burada yakalanmayan etkinliklerden dolayı farklılık gösterebilir.*

|Maliyet faktörü  |Aylık maliyet  |
|---------|---------|
|Seyrek Erişimli depolamada 100 TB yedekleme verisi     |$1556,48         |
|2 TB gün başına yazılan yeni veri x 30 gün     |işlemlerde $39          |
|Aylık tahmini toplam     |$1595,48         |
|---------|---------|
|Ortak internet üzerinden şirket içi olarak 5 TB 'lık bir kez geri yükleme   | $491,26         |

> [!NOTE]
> Bu tahmin, Azure fiyatlandırma hesaplayıcısında Doğu ABD Kullandıkça Öde fiyatlandırması kullanılarak oluşturulmuştur ve günlük 32 MB 'lık alt öbek boyutunu temel alarak, her gün 65.536 PUT Isteği (yazma işlemleri) oluşturur. Bu örnek, gereksinimlerinize uygun olmayabilir.

## <a name="implementation-guidance"></a>Uygulama Kılavuzu

Bu bölümde, Azure Storage 'ın şirket içi bir iletişim dağıtımına nasıl ekleneceği hakkında kısa bir kılavuz sunulmaktadır. Ayrıntılı yönergeler ve planlama konuları için bkz. [Microsoft Azure Için Commkasa genel bulut mimarisi Kılavuzu](https://documentation.commvault.com/commvault/v11/others/pdf/public-cloud-architecture-guide-for-microsoft-azure11-19.pdf).

1. Azure portal açın ve **depolama hesapları** için arama yapın. Varsayılan **depolama hesapları** simgesine de tıklayabilirsiniz.

    ![Azure portal bir depolama hesabı eklemeyi gösterir.](../media/azure-portal.png)
  
    ![Azure portal arama kutusuna depolamayı yazdığınız yeri gösterir.](../media/locate-storage-account.png)

2. Hesap eklemek için **Oluştur** ' u seçin. Bir kaynak grubu seçin veya oluşturun, benzersiz bir ad sağlayın, bölgeyi seçin, **Standart** performans ' ı seçin, her zaman hesap türünü **depolama v2** olarak bırakın, SLA 'larınızı karşılayan çoğaltma düzeyini ve yedekleme yazılımınızın uygulanacağı varsayılan katmanı seçin. Azure depolama hesabı, tek bir hesap içinde kullanılabilir sık erişimli, seyrek erişimli ve arşiv katmanlarının yanı sıra, önemli yaşam döngüsünü verimli bir şekilde yönetmek için birden fazla katman kullanmanıza imkan sağlar.

    ![Portalda depolama hesabı ayarlarını gösterir](../media/account-create-1.png)

3. Varsayılan ağ seçeneklerini şimdilik tutun ve **veri korumasına** geçin. Burada, yanlışlıkla silinen bir yedekleme dosyasını tanımlanan saklama süresi içinde kurtarmanıza ve yanlışlıkla veya kötü amaçlı silmeye karşı koruma sağlamasına olanak sağlayan geçici silme özelliğini etkinleştirebilirsiniz.

    ![Portalda veri koruma ayarlarını gösterir.](../media/account-create-2.png)

4. Daha sonra, Azure kullanım örneklerine yedekleme için **Gelişmiş** ekranın varsayılan ayarlarını öneririz.

    ![Portalda Gelişmiş ayarlar sekmesini gösterir.](../media/account-create-3.png)

5. Etiketleme kullanırsanız ve hesabınızı oluşturduğunuzda kuruluş için Etiketler ekleyin.

6. Hesabı Commkasa ortamınıza ekleyebilmeniz için hemen gerekli olan iki hızlı adım vardır. Azure portal oluşturduğunuz hesaba gidin ve **BLOB hizmeti** menüsü altında **kapsayıcılar** ' ı seçin. Kapsayıcı ekleyin ve anlamlı bir ad seçin. Ardından **Ayarlar** altındaki **erişim tuşları** öğesine gidin ve **depolama hesabı adını** ve iki erişim tuşlarından birini kopyalayın. Sonraki adımlarda kapsayıcı adı, hesap adı ve erişim anahtarı gerekir.

    ![Portalda kapsayıcı oluşturmayı gösterir.](../media/container.png)

    ![Portalda erişim anahtarı ayarlarını gösterir.](../media/access-key.png)

7. (*Isteğe bağlı*) Dağıtımınıza ek güvenlik katmanları ekleyebilirsiniz.

    1. Depolama hesabınızda kimlerin değişiklik yapacağına izin vermek için rol tabanlı erişimi yapılandırın. Daha fazla bilgi için bkz. [yönetim işlemleri Için yerleşik roller](../../../../common/authorization-resource-provider.md#built-in-roles-for-management-operations).
    1. Şirket ağınızın dışından erişim girişimlerini engellemek için, hesap erişimini [depolama Güvenlik Duvarı ayarlarıyla](../../../../common/storage-network-security.md) belirli ağ kesimleriyle kısıtlayın.

        ![Portalda depolama güvenlik duvarı ayarlarını gösterir.](../media/storage-firewall.png)

    1. Depolama hesabının yanlışlıkla silinmesini engellemek için hesapta bir [silme kilidi](../../../../../azure-resource-manager/management/lock-resources.md) ayarlayın.

        ![Portalda silme kilidi ayarlamayı gösterir.](../media/resource-lock.png)

    1. Ek [güvenlik en iyi yöntemlerini](../../../../../storage/blobs/security-recommendations.md)yapılandırın.

1. Commkasaal komut merkezinde   ->  **güvenlik**  ->  **kimlik bilgileri yöneticisini** Yönet ' e gidin. **Bulut hesabı**, **satıcı türü** **Microsoft Azure depolama** seçin, Azure 'a veya Azure 'dan veri aktarımı yapılacak **mediaagent**'ı seçin, depolama hesabı adını ve erişim anahtarını ekleyin.

    ![Commkasası komut merkezindeki kimlik bilgilerini ekleme gösterir.](../media/commvault-credential.png)

9. Ardından,   ->  commkasa komut merkezindeki depolama **bulutu** ' na gidin. **Eklemeyi** seçin. Depolama hesabı için kolay bir ad girin ve ardından **tür** listesinden **Microsoft Azure depolama** ' yi seçin. Yedeklemeleri Azure depolama 'ya aktarmak için kullanılacak bir medya Aracısı sunucusu seçin. Oluşturduğunuz kapsayıcıyı ekleyin, Azure depolama hesabı içinde kullanılacak depolama katmanını seçin ve #8 adımında oluşturulan kimlik bilgilerini seçin. Son olarak, yinelenenleri kaldırılmış yedeklemelerin mi yoksa yinelenenleri kaldırma veritabanı için bir konumun mi aktarılmayacağını seçin.

     ![Commkasasının bulut Kullanıcı ekleme arabiriminin ekran görüntüsü. Arşiv açılan menüsünde * * arşiv * * seçilidir.](../media/commvault-add-storage.png)

10. Son olarak, yeni Azure depolama kaynağınızı,   ->  bir yedekleme hedefi olarak **planları** Yönet aracılığıyla bir commkasasındaki mevcut veya yeni bir plana ekleyin.

    ![Commkasasının Komut Merkezi Kullanıcı arabiriminin ekran görüntüsü. Sol gezinti bölmesinde, * * Yönet * *, * * planlar * * altında seçilidir.](../media/commvault-plan.png)

11. *(Isteğe bağlı)* Sunucu ve uygulamaları Azure 'a geçirmek için Azure 'u bir kurtarma sitesi veya Commkasa olarak kullanmayı planlıyorsanız, Azure 'da bir VSA proxy 'Si dağıtmak en iyi uygulamadır. Ayrıntılı yönergeleri [buradan](https://documentation.commvault.com/commvault/v11/article?p=106208.htm)bulabilirsiniz.

## <a name="operational-guidance"></a>İşletimsel kılavuz

### <a name="azure-alerts-and-performance-monitoring"></a>Azure uyarıları ve performans izleme

Yedeklemelerinizi depolamak için kullandığınız herhangi bir depolama hedefi ile yaptığınız gibi, hem Azure kaynaklarınızın hem de Commkasasının bunlardan faydalanma yeteneğini izlemeniz önerilir. Azure Izleyici ve Commkasası Komut Merkezi izlemenin bir birleşimi ortamınızı sağlıklı tutmanıza yardımcı olur.

#### <a name="azure-portal"></a>Azure portalı

Azure, [Azure izleyici](../../../../../azure-monitor/essentials/monitor-azure-resource.md)biçiminde güçlü bir izleme çözümü sağlar. Azure Izleyici 'yi Azure depolama kapasitesini, işlemleri, kullanılabilirliği, kimlik doğrulamasını ve daha fazlasını izlemek için [yapılandırabilirsiniz](../../../../blobs/monitor-blob-storage.md) . [Burada](../../../../blobs/monitor-blob-storage-reference.md)toplanan ölçümlerin tam başvurusunu bulabilirsiniz. İzleme için birkaç faydalı ölçüm, BlobCapacity ' dir-en yüksek [depolama hesabı kapasite sınırının](../../../../common/scalability-targets-standard-account.md)altında kalmakta ve Azure depolama hesabınızda okunan veri miktarını Izlemek için çıkış yapın ve Azure Storage ve mediaagent 'a giden ve giden isteklerin gidiş dönüş süresini Izlemek için SuccessE2ELatency.

Azure depolama hizmeti durumunu izlemek ve istediğiniz zaman [Azure durum panosunu](https://status.azure.com/status) görüntülemek için [günlük uyarıları da oluşturabilirsiniz](../../../../../service-health/alerts-activity-log-service-notifications-portal.md) .

#### <a name="commvault-command-center"></a>Commkasala Komut Merkezi

- [Bulut depolama havuzları için uyarı oluşturma](https://documentation.commvault.com/commvault/v11/article?p=100514_3.htm)
- Performans raporlarını görüntüleme, iş tamamlama ve temel sorun gidermeyi başlatma hakkında bilgi için bkz. [panolar](https://documentation.commvault.com/commvault/v11/article?p=95306_1.htm).

### <a name="how-to-open-support-cases"></a>Destek çalışmalarını açma

Azure çözümüne yedeklemeniz için yardıma ihtiyacınız olduğunda, hem Commkasadan hem de Azure ile bir servis talebi açmanız gerekir. Bu, destek kuruluşlarımızın gerektiğinde işbirliği yapmasına yardımcı olur.

#### <a name="to-open-a-case-with-commvault"></a>Commkasasıyla bir servis talebi açmak için

[Commkasa destek sitesinde](https://www.commvault.com/support)oturum açın ve bir servis talebi açın.

Size sunulan destek sözleşmesi seçeneklerini anlamak için bkz. [Commkasa destek seçenekleri](https://ma.commvault.com/support)

Ayrıca, bir servis talebi açmak veya e-posta yoluyla Commkasa desteğine ulaşmak için ' de çağrı yapabilirsiniz:

- Ücretsiz: + 1 877-780-3077
- [Dünya genelindeki destek numaraları](https://ma.commvault.com/Support/TelephoneSupport)
- [E-posta Commkasası desteği](mailto:support@commvault.com)

#### <a name="to-open-a-case-with-azure"></a>Azure ile bir servis talebi açmak için

[Azure Portal](https://portal.azure.com) en üstteki arama çubuğunda **destek** için arama yapın. **Yardım ve destek**  ->  **Yeni destek isteği ' ni** seçin.

> [!NOTE]
> Bir servis talebi açtığınızda, Azure depolama veya Azure ağı ile ilgili yardıma ihtiyacınız olduğundan emin olun. Azure Backup belirtmeyin. Azure Backup bir Azure hizmeti adıdır ve servis talebi yanlış yönlendirilir.

### <a name="links-to-relevant-commvault-documentation"></a>İlgili Commkasası belgelerinin bağlantıları

Daha ayrıntılı bilgi için aşağıdaki Commkasası belgelerine bakın:

- [Commkasakasası Kullanıcı Kılavuzu](https://documentation.commvault.com/commvault/v11/article?p=37684_1.htm)
- [Commkasakasası Azure mimari Kılavuzu](https://documentation.commvault.com/commvault/v11/others/pdf/public-cloud-architecture-guide-for-microsoft-azure11-19.pdf)

### <a name="marketplace-offerings"></a>Market teklifleri

Azure sanal makinelerini ve diğer birçok Azure hizmetini korumak için commkasa, çözümlerini Azure 'a dağıtmayı kolaylaştırdı. Daha fazla bilgi için aşağıdaki başvuruları inceleyin:

- [Azure Marketi aracılığıyla Commkasasını dağıtma](https://azuremarketplace.microsoft.com/marketplace/apps/commvault.commvault?tab=Overview)
- [Azure veri sayfası için commkasakasası](https://www.commvault.com/resources/microsoft-azure-cloud-platform-datasheet)
- [Commkasasının desteklenen Azure özellikleri ve hizmetleri listesi](https://documentation.commvault.com/commvault/v11/article?p=109795_1.htm)
- [Azure 'da SAP HANA korumak için Commkasasını kullanma](https://azure.microsoft.com/resources/protecting-sap-hana-in-azure/)

## <a name="next-steps"></a>Sonraki adımlar

Özelleştirilmiş kullanım senaryoları hakkında bilgi için bu ek Commkasa kaynaklarına bakın.

- [Sunucularınızı ve uygulamalarınızı Azure 'a geçirmek için Commkasasını kullanın](https://www.commvault.com/resources/demonstration-vmware-to-azure-migrations-with-commvault)
- [Azure 'da SAP 'yi Commkasala koruma](https://www.youtube.com/watch?v=4ZGGE53mGVI)
- [Office365 ile Commkasasıyla koruma](https://www.youtube.com/watch?v=dl3nvAacxZU)