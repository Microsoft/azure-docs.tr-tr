---
title: Azure Backup raporlarını yapılandırma
description: Log Analytics ve Azure çalışma kitaplarını kullanarak Azure Backup raporlarını yapılandırma ve görüntüleme
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: 0f3638e7649fc02f050c575ee621ce9dc237c24f
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/15/2021
ms.locfileid: "107517275"
---
# <a name="configure-azure-backup-reports"></a>Azure Backup raporlarını yapılandırma

Yedekleme yöneticileri için ortak bir gereksinim, uzun bir süre yayılan verilere göre yedeklemeler hakkında öngörüler elde etmek içindir. Böyle bir çözüm için kullanım örnekleri şunları içerir:

- Tüketilen bulut depolamasını ayırma ve tahmin etme.
- Yedeklemeler ve geri yüklemeler denetimi.
- Farklı ayrıntı düzeyi düzeylerinde önemli eğilimleri tanımlama.

Bugün Azure Backup [Azure izleyici günlüklerini](../azure-monitor/logs/log-analytics-tutorial.md) ve [Azure çalışma kitaplarını](../azure-monitor/visualize/workbooks-overview.md)kullanan bir raporlama çözümü sağlar. Bu kaynaklar, tüm yedeklemeleriniz genelinde yedeklemeleriniz hakkında zengin Öngörüler elde etmenize yardımcı olur. Bu makalede Azure Backup raporlarının nasıl yapılandırılacağı ve görüntüleneceği açıklanır.

## <a name="supported-scenarios"></a>Desteklenen senaryolar

- Yedekleme raporları Azure VM 'leri, Azure sanal makinelerinde SQL, Azure VM 'lerinde SAP HANA, Microsoft Azure Kurtarma Hizmetleri (MARS) Aracısı, Microsoft Azure Backup sunucu (MABS) ve System Center Data Protection Manager (DPM) için desteklenir. Azure dosya paylaşma yedeklemesi için veriler, 1 Haziran 2020 ' de veya sonrasında oluşturulan kayıtlar için görüntülenir.
- Azure dosya paylaşma yedeklemesi için, korumalı örneklerdeki veriler, 1 Şubat 2021 ' den sonra oluşturulan kayıtlar için görüntülenir (eski kayıtlar için varsayılan olarak sıfırdır).
- DPM iş yükleri için yedekleme raporları, DPM sürüm 5.1.363.0 ve üzeri ve aracı sürümü 2.0.9127.0 ve üzeri için desteklenir.
- MABS iş yükleri için yedekleme raporları, MABS sürümü 13.0.415.0 ve üzeri ve aracı sürümü 2.0.9170.0 ve üzeri için desteklenir.
- Yedekleme raporları, verileri kullanıcının erişimi olan bir Log Analytics çalışma alanına gönderildiği sürece tüm yedekleme öğeleri, kasa, abonelik ve bölgelerde görüntülenebilir. Bir kasa kümesinin raporlarını görüntülemek için, yalnızca kasaların verilerini gönderdiği Log Analytics çalışma alanına okuyucu erişiminizin olması gerekir. Bireysel kasaların erişimine sahip olmanız gerekmez.
- Müşterilerinizin aboneliklerine temsilci erişimi olan bir [Azure](../lighthouse/index.yml) açık Kullanıcı kullanıyorsanız, tüm kiracılarınızdaki raporları görüntülemek için Azure açık thouse ile bu raporları kullanabilirsiniz.
- Şu anda, veriler en fazla 100 Log Analytics çalışma alanı (kiracılar arasında) üzerinde yedekleme raporlarında görüntülenebilir.
- Şu anda raporlarda görüntülenmeyen günlük yedekleme işleri verileri.

[!INCLUDE [backup-center.md](../../includes/backup-center.md)]

## <a name="get-started"></a>başlarken

Raporları kullanmaya başlamak için bu adımları izleyin.

### <a name="1-create-a-log-analytics-workspace-or-use-an-existing-one"></a>1. bir Log Analytics çalışma alanı oluşturun veya mevcut olanı kullanın

Yedekleme raporlama verilerinizi depolamak için bir veya daha fazla Log Analytics çalışma alanı ayarlayın. Bu Log Analytics çalışma alanının oluşturulabildiği konum ve abonelik, kasalarınızın bulunduğu konumdan ve aboneliğin bağımsızdır.

Log Analytics çalışma alanı ayarlamak için, bkz. [Azure portal Log Analytics çalışma alanı oluşturma](../azure-monitor/logs/quick-create-workspace.md).

Varsayılan olarak, bir Log Analytics çalışma alanındaki veriler 30 gün boyunca tutulur. Daha uzun bir süre için verileri görmek üzere Log Analytics çalışma alanının saklama süresini değiştirin. Saklama süresini değiştirmek için bkz. [Azure izleyici günlükleri ile kullanımı ve maliyetleri yönetme](../azure-monitor/logs/manage-cost-storage.md).

### <a name="2-configure-diagnostics-settings-for-your-vaults"></a>2. kasalarınız için tanılama ayarlarını yapılandırın

Kurtarma Hizmetleri kasaları gibi kaynaklar Azure Resource Manager, zamanlanmış işlemler ve Kullanıcı tarafından tetiklenen işlemler hakkındaki bilgileri Tanılama verileri olarak kaydeder.

Kurtarma Hizmetleri kasaınızın İzleme bölümünde **Tanılama ayarları** ' nı seçin ve kurtarma hizmetleri kasasının tanılama verilerinin hedefini belirtin. Tanılama olaylarını kullanma hakkında daha fazla bilgi için bkz. [Kurtarma Hizmetleri kasaları için tanılama ayarlarını kullanma](./backup-azure-diagnostic-events.md).

![Tanılama ayarları bölmesi](./media/backup-azure-configure-backup-reports/resource-specific-blade.png)

Azure Backup Ayrıca, belirli bir kapsamdaki tüm kasaların tanılama ayarlarının yapılandırılmasını otomatikleştiren yerleşik bir Azure Ilke tanımı sağlar. Bu ilkeyi nasıl kullanacağınızı öğrenmek için bkz. [kasa tanılama ayarlarını ölçeklendirerek yapılandırma](./azure-policy-configure-diagnostics.md).

> [!NOTE]
> Tanılamayı yapılandırdıktan sonra, ilk veri gönderimi işleminin tamamlanması 24 saate kadar sürebilir. Veriler Log Analytics çalışma alanına akışa başladıktan sonra raporlardaki verileri hemen göremeyebilirsiniz, çünkü geçerli kısmi güne ait veriler raporlarda gösterilmez. Daha fazla bilgi için bkz. [yedekleme raporlarında kullanılan kurallar](#conventions-used-in-backup-reports). Vakaları Log Analytics verileri gönderecek şekilde yapılandırdıktan sonra raporları iki gün sonra görüntülemeyi başlatmanız önerilir.

#### <a name="3-view-reports-in-the-azure-portal"></a>3. Azure portal raporları görüntüleme

Vaetlerinizi Log Analytics veri gönderecek şekilde yapılandırdıktan sonra, tüm kasaların bölmesine gidip **yedekleme raporları**' nı seçerek yedekleme raporlarınızı görüntüleyin.

![Kasa panosu](./media/backup-azure-configure-backup-reports/vault-dashboard.png)

Yedekleme raporu çalışma kitabını açmak için bu bağlantıyı seçin.

> [!NOTE]
>
> - Şu anda, raporun ilk yükü 1 dakikaya kadar sürebilir.
> - Kurtarma Hizmetleri Kasası yalnızca yedekleme raporları için bir giriş noktasıdır. Yedekleme raporu çalışma kitabı bir kasadaki bölmeden açıldıktan sonra tüm kasalarınızda toplanan verileri görmek için uygun Log Analytics çalışma alanları kümesini seçin.

Rapor çeşitli sekmeler içerir:

##### <a name="summary"></a>Özet

Yedeğinize ilişkin üst düzey bir genel bakış almak için bu sekmeyi kullanın. Toplam yedekleme öğesi sayısı, tüketilen toplam bulut depolaması, korunan örnek sayısı ve iş yükü türü başına iş başarısı oranı hakkında hızlı bir bakış edinebilirsiniz. Belirli bir yedekleme yapıtı türü hakkında daha ayrıntılı bilgi için ilgili sekmelere gidin.

   ![Özet sekmesi](./media/backup-azure-configure-backup-reports/summary.png)

##### <a name="backup-items"></a>Yedekleme Öğeleri

Bir yedekleme öğesi düzeyinde tüketilen Bulut depolamada bilgi ve eğilimleri görmek için bu sekmeyi kullanın. Örneğin, bir Azure VM yedeğine SQL kullanıyorsanız, yedeklenen her SQL veritabanı için kullanılan bulut depolama alanını görebilirsiniz. Ayrıca, belirli bir koruma durumunun yedekleme öğeleri için verileri görmeyi seçebilirsiniz. Örneğin, sekmenin en üstünde **koruma durdurulmuş** kutucuğun seçilmesi, yalnızca koruma durdurulmuş durumundaki yedekleme öğeleri için verileri göstermek üzere altındaki tüm pencere öğelerini filtrelemektir.

   ![Yedekleme öğeleri sekmesi](./media/backup-azure-configure-backup-reports/backup-items.png)

##### <a name="usage"></a>Kullanım

Yedeklemeleriniz için anahtar faturalandırma parametrelerini görüntülemek için bu sekmeyi kullanın. Bu sekmede gösterilen bilgiler bir faturalandırma varlığı (korumalı kapsayıcı) düzeyidir. Örneğin, bir DPM sunucusu Azure 'a yedekleniyorsa, korunan örneklerin ve DPM sunucusu için kullanılan bulut depolamanın eğilimini görüntüleyebilirsiniz. Benzer şekilde, Azure Backup ' de SQL kullanıyorsanız veya Azure Backup SAP HANA, bu sekme, bu veritabanlarının bulunduğu sanal makine düzeyinde kullanımla ilgili bilgiler sağlar.

   ![Kullanım sekmesi](./media/backup-azure-configure-backup-reports/usage.png)

> [!NOTE]
> DPM iş yükleri için, kullanıcılar, kurtarma hizmetleri Kasası **genel bakış** sekmesinde gösterilen toplu kullanım değeriyle karşılaştırıldığında, raporlarda gösterilen kullanım değerleri arasında bir hafif fark (DPM sunucusu BAŞıNA 20 MB) görebilirler. Bu fark, yedekleme için kaydedilen her DPM sunucusunun, raporlama için yapıt olarak ortaya çıkan ilişkili bir ' Metadata ' veri kaynağına sahip olması açısından hesaba göre belirlenir.

##### <a name="jobs"></a>İşler

Her gün başarısız iş sayısı ve iş hatasının en üst nedenleri gibi işlerde uzun süre çalışan eğilimleri görüntülemek için bu sekmeyi kullanın. Bu bilgileri hem bir toplama düzeyinde hem de yedekleme öğesi düzeyinde görüntüleyebilirsiniz. Seçili zaman aralığında o yedekleme öğesinde tetiklenen her bir işle ilgili ayrıntılı bilgileri görüntülemek için kılavuzda belirli bir yedekleme öğesini seçin.

   ![İşler sekmesi](./media/backup-azure-configure-backup-reports/jobs.png)

##### <a name="policies"></a>İlkeler

İlgili öğe sayısı ve belirli bir ilke altında yedeklenen öğeler tarafından tüketilen toplam bulut depolaması gibi tüm etkin ilkeleriniz hakkındaki bilgileri görüntülemek için bu sekmeyi kullanın. İlişkili yedekleme öğelerinin her biri hakkındaki bilgileri görüntülemek için belirli bir ilkeyi seçin.

   ![İlkeler sekmesi](./media/backup-azure-configure-backup-reports/policies.png)

##### <a name="optimize"></a>İyileştirme

Yedeklemelerinize yönelik potansiyel maliyet iyileştirme fırsatlarıyla ilgili görünürlük elde etmek için bu sekmeyi kullanın. En Iyileştirme sekmesinin Şu anda öngörü sağladığı senaryolar aşağıda verilmiştir:

###### <a name="inactive-resources"></a>Etkin olmayan kaynaklar

Bu görünümü kullanarak, önemli bir süre için başarılı bir yedeklemeye sahip olmayan yedekleme öğelerini belirleyebilirsiniz. Bu, yedeklenen temel makinenin artık mevcut olmadığı anlamına gelir (ve bu nedenle başarısız olan yedeklemelere yol açar) ya da bu, yedeklemelerin güvenilir bir şekilde alınmasını engelleyen bir sorun olabilir.

Etkin olmayan kaynakları görüntülemek için **en iyileştirme** sekmesine gidin ve **etkin olmayan kaynaklar** kutucuğunu seçin. Bu kutucuğu Seç seçili kapsamda mevcut olan tüm etkin olmayan kaynakların ayrıntılarını içeren bir kılavuz görüntüler. Varsayılan olarak, kılavuz son yedi gün içinde bir kurtarma noktası olmayan öğeleri gösterir. Farklı bir zaman aralığı için etkin olmayan kaynakları bulmak için sekmenin en üstünde **zaman aralığı** filtresini ayarlayabilirsiniz.

Etkin olmayan bir kaynağı tanımladıktan sonra, bu kaynak için yedekleme öğesi panosuna veya Azure Kaynak bölmesine (varsa) giderek sorunu daha fazla inceleyebilirsiniz. Senaryonuza bağlı olarak, makinenin yedeklemesini durdurmayı (artık yoksa) seçebilir ve gereksiz yedeklemeleri silebilir ya da yedeklemelerin güvenilir bir şekilde alındığından emin olmak için makinedeki sorunları giderebilirsiniz.

![Sekme ile etkin olmayan kaynakları iyileştirme](./media/backup-azure-configure-backup-reports/optimize-inactive-resources.png)

###### <a name="backup-items-with-a-large-retention-duration"></a>Saklama süresi uzun olan Yedek Öğeleri

Bu görünümü kullanarak, yedeklemeleri, kuruluşunuz tarafından gerekenden uzun bir süre boyunca korunan öğeleri tanımlayabilirsiniz.

**Ilke iyileştirmeleri** kutucuğunun ardından **bekletme iyileştirmeleri** kutucuğunun seçilmesi, günlük, haftalık, aylık veya yıllık bekletme noktası (RP) belirtilen değerden daha büyük olduğunda tüm yedekleme öğelerini içeren bir kılavuz görüntüler. Varsayılan olarak, kılavuz seçilen kapsamdaki tüm yedekleme öğelerini görüntüler. Kılavuzu daha fazla filtrelemek ve saklama süresini yedekleme depolama maliyetlerine kaydetmek üzere azaltılacak olan öğeleri belirlemek için günlük, haftalık, aylık ve yıllık RP saklama filtrelerini kullanabilirsiniz.

SQL ve SAP HANA gibi veritabanı iş yükleri için kılavuzda gösterilen bekletme dönemleri, fark yedekleme noktalarında değil, tam yedekleme noktalarının bekletme dönemlerine karşılık gelir. Aynı zamanda bekletme filtreleri için de geçerlidir.  

![En iyileştirme sekmesi-bekletme Iyileştirmeleri](./media/backup-azure-configure-backup-reports/optimize-retention.png)

###### <a name="databases-configured-for-daily-full-backup"></a>Günlük tam yedekleme için yapılandırılmış veritabanları 

Bu görünümü kullanarak günlük tam yedekleme için yapılandırılmış veritabanı iş yüklerini belirleyebilirsiniz. Genellikle, haftalık tam yedekleme ile birlikte günlük değişiklik yedeklemesi kullanılması daha düşük maliyetli bir iştir.

**Ilke iyileştirmeleri** kutucuğunun ardından **yedekleme zamanlaması iyileştirmeleri** kutucuğunun seçilmesi, günlük tam yedekleme ilkesiyle tüm veritabanlarını içeren bir kılavuz görüntüler. Belirli bir yedekleme öğesine gitmeyi ve günlük değişiklik yedeklemesini haftalık tam yedekleme kullanacak şekilde değiştirmeyi tercih edebilirsiniz.

Kılavuzun veritabanı iş yüklerini beklendiği gibi görüntüleyebilmesi için, sekmenin en üstündeki **yedekleme yönetimi türü** filtresinin Azure VM 'de **SQL** ve **Azure VM 'de SAP HANA** , seçili olması gerekir.

![Sekmeyi en iyi duruma getirme-yedekleme zamanlaması Iyileştirmeleri](./media/backup-azure-configure-backup-reports/optimize-backup-schedule.png)

###### <a name="policy-adherence"></a>İlke bağlılığı

Bu sekmeyi kullanarak tüm yedekleme örneklerinizin her gün en az bir başarılı yedeklemeye sahip olup olmadığını belirleyebilirsiniz. Haftalık yedekleme ilkesi olan öğeler için, tüm yedekleme örneklerinin haftada en az bir başarılı yedekleme olup olmadığını anlamak için bu sekmeyi kullanabilirsiniz.

İki tür ilke uygunluk görünümü mevcuttur:

* **Zaman aralığına göre Ilke uyumluluğu**: Bu görünümü kullanarak, belirli bir günde en az bir başarılı yedeklemeye sahip olduğunu ve bu gün için kaç tane başarılı bir yedekleme kalmadığını belirleyebilirsiniz. Seçili günde tetiklenen tüm yedekleme işlerinin ayrıntılarını görmek için bir satıra tıklayabilirsiniz. Zaman aralığını, son 60 gün gibi daha büyük bir değere artırdıysanız, kılavuz haftalık görünümde işlenir ve verilen hafta her gününde en az bir başarılı yedeklemeye sahip olan tüm öğelerin sayısını görüntüler. Benzer şekilde, daha büyük zaman aralıkları için aylık bir görünüm vardır.

Haftalık olarak yedeklenen öğeler söz konusu olduğunda, bu kılavuz verilen hafta içinde en az bir başarılı yedeklemeye sahip olan tüm öğeleri belirlemenize yardımcı olur. Son 120 gün gibi daha büyük bir zaman aralığı için kılavuz aylık görünümde işlenir ve verilen aydaki her hafta en az bir başarılı yedeklemeye sahip olan tüm öğelerin sayısını görüntüler. Günlük, haftalık ve aylık görünümler hakkında daha fazla ayrıntı için [yedekleme raporlarında kullanılan kurallara](#conventions-used-in-backup-reports) bakın.

![Döneme göre ilke bağlılığı](./media/backup-azure-configure-backup-reports/policy-adherence-by-time-period.png)

* **Yedekleme örneğine göre Ilke uyumluluğu**: Bu görünümü kullanarak, bir yedekleme örneği düzeyinde ilke bağlılığı ayrıntılarını kullanabilirsiniz. Yeşil bir hücre, yedekleme örneğinin verilen gün üzerinde en az bir başarılı yedekleme olduğunu gösterir. Kırmızı olan bir hücre, yedekleme örneğinin verilen günde bir başarılı yedekleme yapmamış olduğunu gösterir. Günlük, haftalık ve aylık toplamalar, zaman dönemi görünümü ile Ilke bağlılığı ile aynı davranışı izler. Seçilen zaman aralığında verilen yedekleme örneğindeki tüm yedekleme işlerini görüntülemek için herhangi bir satıra tıklayabilirsiniz.

![Yedekleme örneğine göre ilke bağlılığı](./media/backup-azure-configure-backup-reports/policy-adherence-by-backup-instance.png)

###### <a name="email-azure-backup-reports"></a>E-posta Azure Backup raporları

Yedekleme raporlarında kullanılabilen **e-posta raporu** özelliğini kullanarak, e-posta yoluyla düzenli raporlar almak için otomatikleştirilmiş görevler oluşturabilirsiniz. Bu özellik, Azure ortamınızda, sağladığınız girişlere göre seçtiğiniz Log Analytics (LA) çalışma alanlarından verileri sorgulayan bir mantıksal uygulama dağıtarak işe yarar.

Mantıksal uygulama oluşturulduktan sonra Azure Izleyici günlüklerine ve Office 365 ' e bağlantı yetkilendirmeniz gerekir. Bunu yapmak için Azure portal **Logic Apps** gidin ve oluşturduğunuz görevin adını arayın. **API bağlantıları** menü öğesini seçmek, YETKILENDIRMENIZ gereken API bağlantılarının listesini açar. [E-postaları yapılandırma ve sorunları giderme hakkında daha fazla bilgi edinin](backup-reports-email.md).

###### <a name="customize-azure-backup-reports"></a>Azure Backup raporlarını özelleştirme

Yedekleme raporları, [Azure izleyici günlüklerinde sistem işlevlerini](backup-reports-system-functions.md)kullanır. Bu işlevler, LA 'daki ham Azure Backup tablolarındaki veriler üzerinde çalışır ve basit sorgular kullanarak yedeklemeyle ilgili tüm varlıklarınızın bilgilerini kolayca almanıza yardımcı olan biçimli verileri döndürür. 

Yedekleme raporlarını temel olarak kullanarak kendi raporlama çalışma kitaplarınızı oluşturmak için, yedekleme raporları ' na gidebilir, raporun en üstünde **Düzenle** ' ye tıklayabilir ve raporlarda kullanılan sorguları görüntüleyebilir/düzenleyebilirsiniz. Özel raporların nasıl oluşturulacağı hakkında daha fazla bilgi edinmek için [Azure çalışma kitapları belgelerine](../azure-monitor/visualize/workbooks-overview.md) bakın. 

## <a name="export-to-excel"></a>Excel'e aktar

Bir tablo veya grafik gibi herhangi bir pencere öğesinin sağ üst köşesindeki aşağı ok düğmesini seçin ve bu pencere öğesinin içeriğini bir Excel sayfası olarak, var olan filtrelerin uygulanmış olduğu gibi dışarı aktarın. Bir tablonun daha fazla satırını Excel 'e aktarmak için, her bir kılavuzun en üstünde bulunan **sayfa** aşağı açılan okunu kullanarak sayfada görüntülenecek satır sayısını artırabilirsiniz.

## <a name="pin-to-dashboard"></a>Panoya sabitle

Pencere öğesini Azure portal panonuza sabitlemek için her pencere öğesinin en üstündeki sabitle düğmesini seçin. Bu özellik, ihtiyacınız olan en önemli bilgileri görüntüleyecek şekilde uyarlanmış özelleştirilmiş panolar oluşturmanıza yardımcı olur.

## <a name="cross-tenant-reports"></a>Çapraz kiracı raporları

Çoklu kiracı ortamlarında abonelikler için temsilci erişimi olan [Azure açık Thouse](../lighthouse/index.yml) kullanırsanız, varsayılan abonelik filtresini kullanabilirsiniz. Verilerini görmek istediğiniz tüm abonelikleri seçmek için Azure portal sağ üst köşesinde bulunan filtre düğmesini seçin. Bunun yapılması, Kiracılarınızın genelinde Log Analytics çalışma alanlarını seçmenizi sağlar ve çok kiracılı raporları görüntüler.

## <a name="conventions-used-in-backup-reports"></a>Yedekleme raporlarında kullanılan kurallar

- Filtreler, her sekmede soldan sağa ve yukarıdan aşağıya doğru çalışır. Diğer bir deyişle, tüm filtreler yalnızca bu filtrenin sağına veya bu filtrenin altına konumlandırılmış olan tüm pencere öğeleri için geçerlidir.
- Renkli bir kutucuk seçilmesi, kutucuğun altındaki pencere öğelerini, bu döşemenin değerine ait olan kayıtlar için filtreler. Örneğin, **yedekleme öğeleri** sekmesindeki **koruma durduruldu** kutucuğunun seçilmesi, aşağıdaki kılavuzların ve grafiklerin koruma durdurulmuş durumda yedekleme öğelerinin verilerini göstermesi için filtre uygular.
- Renksiz olmayan kutucuklar seçilebilir değildir.
- Geçerli kısmi güne ait veriler raporlarda gösterilmez. Bu nedenle, **saat aralığının** seçili değeri **son 7 gün** olduğunda, rapor son yedi gün için kayıtları gösterir. Geçerli gün dahil değildir.
- Rapor, seçilen zaman aralığında *tetiklenen* işlerin ayrıntılarını (günlük işlerden ayrı olarak) gösterir.
- **Bulut depolama** ve **korumalı örnekler** için gösterilen değerler seçili zaman aralığının *sonunda* .
- Raporlarda görüntülenen yedekleme öğeleri, seçili zaman aralığının *sonunda* bulunan öğelerdir. Seçilen zaman aralığının ortasında silinen yedekleme öğeleri gösterilmez. Aynı kural yedekleme ilkeleri için de geçerlidir.
- Seçilen zaman aralığı 30 günlük bir döneme yayılmışsa, grafikler günlük görünümde işlenir ve burada her gün için bir veri noktası bulunur. Zaman aralığı 30 günden daha uzun ve 90 günden daha az (veya buna eşit) bir döneme yayılırsa, grafikler haftalık görünümde işlenir. Daha büyük zaman aralıkları için grafikler aylık görünümde işlenir. Verilerin haftalık veya aylık olarak yönetilmesi, sorguların daha iyi performansına ve grafiklerdeki verilerin daha kolay okunmanıza yardımcı olur.
- Ilke bağlılığı ızgaraları, yukarıda açıklandığı gibi benzer bir toplama mantığını da izler. Ancak, birkaç küçük fark vardır. İlk fark, haftalık yedekleme ilkesi olan öğeler için günlük görünüm yoktur (yalnızca haftalık ve aylık görünümler kullanılabilir). Ayrıca, haftalık yedekleme ilkesi olan öğelerin kılavuzlarında, kısmi haftaların dikkate alınması için bir ' ay ' 4 haftalık dönem (28 gün) ve 30 gün olarak değerlendirilir.

## <a name="query-load-times"></a>Sorgu yükleme süreleri

Yedekleme raporundaki pencere öğeleri, kullanıcının Log Analytics çalışma alanlarında çalışan kusto sorguları tarafından desteklenir. Bu sorgular genellikle daha zengin içgörüler sağlamak için birden fazla birleşimle büyük miktarlarda veri işlemeyi içerir. Sonuç olarak, Kullanıcı büyük bir yedekleme genelinde rapor görüntülediğinde pencere öğeleri anında yüklenmeyebilir. Bu tablo, yedekleme öğelerinin sayısına ve raporun görüntülendiği zaman aralığına bağlı olarak, farklı pencere öğelerinin yüklenmeye sürebileceği kabaca bir tahmin sağlar.

| **Veri kaynağı sayısı**                         | **Zaman ufuk** | **Yaklaşık yükleme süreleri**                                              |
| --------------------------------- | ------------- | ------------------------------------------------------------ |
| ~ 5 K                       | 1 ay          | Kutucuklar: 5-10 saniye <br> Kılavuzlar: 5-10 saniye <br> Grafikler: 5-10 saniye <br> Rapor düzeyi filtreleri: 5-10 saniye|
| ~ 5 K                       | 3 ay          | Kutucuklar: 5-10 saniye <br> Kılavuzlar: 5-10 saniye <br> Grafikler: 5-10 saniye <br> Rapor düzeyi filtreleri: 5-10 saniye|
| ~ 10 K                       | 3 ay          | Kutucuklar: 15-20 saniye <br> Kılavuzlar: 15-20 saniye <br> Grafikler: 1-2 dakika <br> Rapor düzeyi filtreleri: 25-30 saniye|
| ~ 15 K                       | 1 ay          | Kutucuklar: 15-20 saniye <br> Kılavuzlar: 15-20 saniye <br> Grafikler: 50-60 saniye <br> Rapor düzeyi filtreleri: 20-25 saniye|
| ~ 15 K                       | 3 ay          | Kutucuklar: 20-30 saniye <br> Kılavuzlar: 20-30 saniye <br> Grafikler: 2-3 dakika <br> Rapor düzeyi filtreleri: 50-60 saniye |

## <a name="what-happened-to-the-power-bi-reports"></a>Power BI raporlarına ne oldu? 

- Azure Storage hesabından veri kaynağı olan raporlama için önceki Power BI şablon uygulaması kullanımdan kaldırma yolunda yer alır. Raporları görüntülemek için Log Analytics kasa Tanılama verileri göndermeye başlayabilmeniz önerilir.

- Ayrıca, bir depolama hesabına veya bir LA çalışma alanına Tanılama verileri göndermenin [v1 şeması](./backup-azure-diagnostics-mode-data-model.md#v1-schema-vs-v2-schema) , kullanımdan kaldırma yolunda da olur. Bu, v1 şemasına göre özel sorgular veya bir sorgu yazdıysanız, bu sorguları şu anda desteklenen v2 şemasını kullanmak için güncelleştirmeniz önerilir.

## <a name="next-steps"></a>Sonraki adımlar

[Azure Backup ile izleme ve raporlama hakkında daha fazla bilgi edinin](./backup-azure-monitor-alert-faq.yml)