---
title: Veead ile bir yedekleme hedefi olarak StorSimple 8000 serisi | Microsoft Docs
description: Veead ile StorSimple yedekleme hedefi yapılandırmasını açıklar.
services: storsimple
documentationcenter: ''
author: harshakirank
manager: matd
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/06/2016
ms.author: matd
ms.openlocfilehash: 3ebf464fed1480e7452f246f04f3906faf0dd219
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/31/2019
ms.locfileid: "67875309"
---
# <a name="storsimple-as-a-backup-target-with-veeam"></a>Veead ile yedekleme hedefi olarak StorSimple

## <a name="overview"></a>Genel Bakış

Azure StorSimple, Microsoft 'un sunduğu bir karma bulut depolama çözümüdür. StorSimple, şirket içi çözümün uzantısı olarak bir Azure depolama hesabı kullanarak üstel veri artışına ilişkin karmaşıklıkları giderir ve şirket içi depolama ve bulut depolama genelindeki verileri otomatik olarak katmanlama.

Bu makalede, Veeam ile StorSimple tümleştirmesini ve her iki çözümü tümleştirmek için en iyi uygulamaları tartıştık. Ayrıca, StorSimple ile en iyi tümleşecek şekilde Veead 'yi ayarlama önerileri de sunuyoruz. Tek tek yedekleme gereksinimlerini ve hizmet düzeyi sözleşmelerini (SLA 'Lar) karşılamak üzere Veead 'yi ayarlamak için en iyi yol için Veeak en iyi uygulamaları, yedekleme mimarları ve yöneticileri erteliyoruz.

Yapılandırma adımlarını ve temel kavramları gösterdiğimiz halde bu makalede, adım adım bir yapılandırma veya Yükleme Kılavuzu olması gerekir. Temel bileşenlerin ve altyapının çalışma sırasında olduğunu ve tanımladığımız kavramları desteklemeye hazırlandığını varsayalım.

### <a name="who-should-read-this"></a>Bunu kimler okumalı?

Bu makaledeki bilgiler, depolama, Windows Server 2012 R2, Ethernet, bulut hizmetleri ve Veead hakkında bilgi sahibi olan yedekleme yöneticileri, depolama yöneticileri ve depolama mimarları için en yararlı olacaktır.

### <a name="supported-versions"></a>Desteklenen sürümler

-   Veead 9 ve sonraki sürümler
-   [StorSimple güncelleştirme 3 ve sonraki sürümleri](storsimple-overview.md#storsimple-workload-summary)


## <a name="why-storsimple-as-a-backup-target"></a>Neden bir yedekleme hedefi olarak StorSimple?

StorSimple, yedekleme hedefi için iyi bir seçimdir çünkü:

-   Yedekleme uygulamaları için, hiçbir değişiklik yapılmadan hızlı yedekleme hedefi olarak kullanılacak standart, yerel depolama alanı sağlar. Ayrıca, son yedeklemelerin hızlı bir şekilde geri yüklenmesi için StorSimple 'ı kullanabilirsiniz.
-   Bulut katmanlaması, uygun maliyetli Azure depolama alanı kullanmak için bir Azure bulut depolama hesabıyla sorunsuzca tümleşiktir.
-   Olağanüstü durum kurtarma için otomatik olarak şirket dışı depolama sağlar.


## <a name="key-concepts"></a>Önemli kavramlar

Herhangi bir depolama çözümünde olduğu gibi, çözümün depolama performansına, SLA 'Lara, değişiklik hızına ve kapasite büyüme ihtiyaçlarına yönelik dikkatli bir değerlendirme, başarılı olması için kritik öneme sahiptir. Ana fikir, bir bulut katmanı ile tanışın, erişim zamanlarınız ve işlerinizin buluta, işini yapmak için StorSimple özelliği sayesinde temel bir rol oynar.

StorSimple, iyi tanımlanmış bir çalışma verileri kümesi (sık kullanılan veriler) üzerinde çalışan uygulamalara depolama sağlamak için tasarlanmıştır. Bu modelde, çalışma verileri yerel katmanlara depolanır ve kalan çalışma dışı/soğuk/arşivlenmiş veri kümesi buluta katmanlı. Bu model aşağıdaki şekilde temsil edilir. Neredeyse düz yeşil çizgi, StorSimple cihazının yerel katmanlarında depolanan verileri temsil eder. Kırmızı çizgi, tüm katmanlar genelinde StorSimple çözümünde depolanan toplam veri miktarını temsil eder. Düz yeşil çizgi ile üstel kırmızı eğri arasındaki boşluk, bulutta depolanan toplam veri miktarını temsil eder.

**StorSimple katmanlama**
![StorSimple katmanlama diyagramı](./media/storsimple-configure-backup-target-using-veeam/image1.jpg)

Bu mimari göz önünde bulundurularak, StorSimple 'ın yedekleme hedefi olarak çalışacak şekilde uygun olduğunu fark edersiniz. StorSimple kullanarak şunları yapabilirsiniz:

-   Yerel çalışma kümesinden en sık geri yükleme işlemini gerçekleştirin.
-   Şirket dışı olağanüstü durum kurtarma ve daha eski veriler için bulutu kullanarak geri yüklemeler daha sık sık kullanılır.

## <a name="storsimple-benefits"></a>StorSimple avantajları

StorSimple, şirket içi ve bulut depolamaya sorunsuz erişimin avantajlarından yararlanarak Microsoft Azure ile sorunsuz bir şekilde tümleştirilmiş şirket içi bir çözüm sunar.

StorSimple, katı hal cihazı (SSD) ve seri ekli SCSI (SAS) depolama alanı ve Azure depolama ile şirket içi cihaz arasında otomatik katmanlama kullanır. Otomatik katmanlama, SSD ve SAS katmanlarında sık erişilen verileri yerel olarak korur. Seyrek erişilen verileri Azure depolama 'ya taşıdığından.

StorSimple şu avantajları sunar:

-   Yinelenenleri kaldırma düzeyleri elde etmek için bulutu kullanan benzersiz yinelenenleri kaldırma ve sıkıştırma algoritmaları
-   Yüksek kullanılabilirlik
-   Azure coğrafi çoğaltma kullanarak coğrafi çoğaltma
-   Azure tümleştirmesi
-   Bulutta veri şifreleme
-   İyileştirilmiş olağanüstü durum kurtarma ve uyumluluk

StorSimple, temelde iki ana dağıtım senaryosu (birincil yedekleme hedefi ve ikincil yedekleme hedefi) sunmakla birlikte, düz, blok depolama cihazından oluşan bir Işlemdir. StorSimple, tüm sıkıştırma ve yinelenenleri kaldırma işlemi yapar. Bulut ile uygulama ve dosya sistemi arasında sorunsuz bir şekilde veri gönderir ve alır.

StorSimple hakkında daha fazla bilgi için bkz [. StorSimple 8000 serisi: Karma bulut depolama çözümü](storsimple-overview.md). Ayrıca, [Teknik StorSimple 8000 serisi belirtimlerini](storsimple-technical-specifications-and-compliance.md)inceleyebilirsiniz.

> [!IMPORTANT]
> Bir StorSimple cihazının yedekleme hedefi olarak kullanılması yalnızca StorSimple 8000 güncelleştirme 3 ve sonraki sürümlerinde desteklenir.

## <a name="architecture-overview"></a>Mimariye genel bakış

Aşağıdaki tablolarda cihaz modelden mimari ilk kılavuz gösterilmektedir.

**Yerel ve bulut depolama için StorSimple kapasiteleri**

| Depolama kapasitesi | 8100 | 8600 |
|---|---|---|
| Yerel depolama kapasitesi | &lt;10 TiB\*  | &lt;20 TiB\*  |
| Bulut depolama kapasitesi | &gt;200 TiB\* | &gt;500 TiB\* |

\*Depolama boyutu, yinelenenleri kaldırma veya sıkıştırma olmadığını varsayar.

**Birincil ve ikincil yedeklemeler için StorSimple kapasiteleri**

| Yedekleme senaryosu  | Yerel depolama kapasitesi  | Bulut depolama kapasitesi  |
|---|---|---|
| Birincil yedekleme  | Kurtarma noktası hedefini (RPO) karşılamak üzere hızlı kurtarma için yerel depolama alanında depolanan son yedeklemeler | Yedekleme geçmişi (RPO) bulut kapasitesine uyar |
| İkincil yedekleme | Yedekleme verilerinin ikincil kopyası, bulut kapasitesinde depolanabilir  | Yok  |

## <a name="storsimple-as-a-primary-backup-target"></a>Birincil yedekleme hedefi olarak StorSimple

Bu senaryoda, StorSimple birimleri yedekleme uygulamasına yedeklemeler için tek depo olarak sunulur. Aşağıdaki şekilde, tüm yedeklemelerin yedeklemeler ve geri yüklemeler için StorSimple katmanlı birimler kullanıldığı bir çözüm mimarisi gösterilmektedir.

![Birincil yedekleme hedefi mantıksal diyagramı olarak StorSimple](./media/storsimple-configure-backup-target-using-veeam/primarybackuptargetlogicaldiagram.png)

### <a name="primary-target-backup-logical-steps"></a>Birincil hedef yedekleme mantıksal adımları

1.  Yedekleme sunucusu hedef yedekleme aracısıyla iletişim kurar ve yedekleme Aracısı verileri yedekleme sunucusuna iletir.
2.  Yedekleme sunucusu StorSimple katmanlı birimlere veri yazar.
3.  Yedekleme sunucusu, katalog veritabanını güncelleştirir ve ardından yedekleme işini sonlandırır.
4.  Bir Snapshot betiği StorSimple bulut anlık görüntü yöneticisi 'ni (başlangıç veya silme) tetikler.
5.  Yedekleme sunucusu, bir bekletme ilkesine göre süresi biten yedeklemeleri siler.

### <a name="primary-target-restore-logical-steps"></a>Birincil hedef geri yükleme mantıksal adımları

1.  Yedekleme sunucusu, depolama deposundan uygun verileri geri yüklemeye başlar.
2.  Yedekleme aracısı, yedekleme sunucusundan verileri alır.
3.  Yedekleme sunucusu geri yükleme işini sonlandırır.

## <a name="storsimple-as-a-secondary-backup-target"></a>İkincil yedekleme hedefi olarak StorSimple

Bu senaryoda, StorSimple birimleri öncelikle uzun süreli saklama veya arşivleme için kullanılır.

Aşağıdaki şekilde, ilk yedeklemelerin ve geri yüklemeler için yüksek performanslı bir birimin hedeflemesini gösteren bir mimari gösterilmektedir. Bu yedeklemeler, küme zamanlamasında bir StorSimple katmanlı birime kopyalanır ve arşivlenir.

Yüksek performanslı haciminizi, bekletme ilkesi kapasitenizi ve performans gereksinimlerinizi işleyebilecek şekilde boyutlandırmamak önemlidir.

![İkincil yedekleme hedefi mantıksal diyagramı olarak StorSimple](./media/storsimple-configure-backup-target-using-veeam/secondarybackuptargetlogicaldiagram.png)

### <a name="secondary-target-backup-logical-steps"></a>İkincil hedef yedekleme mantıksal adımları

1.  Yedekleme sunucusu hedef yedekleme aracısıyla iletişim kurar ve yedekleme Aracısı verileri yedekleme sunucusuna iletir.
2.  Yedekleme sunucusu, verileri yüksek performanslı depolamaya yazar.
3.  Yedekleme sunucusu, katalog veritabanını güncelleştirir ve ardından yedekleme işini sonlandırır.
4.  Yedekleme sunucusu yedeklemeleri bir bekletme ilkesine göre StorSimple 'a kopyalar.
5.  Bir Snapshot betiği StorSimple bulut anlık görüntü yöneticisi 'ni (başlangıç veya silme) tetikler.
6.  Yedekleme sunucusu, bir bekletme ilkesine göre süresi biten yedeklemeleri siler.

### <a name="secondary-target-restore-logical-steps"></a>İkincil hedef geri yükleme mantıksal adımları

1.  Yedekleme sunucusu, depolama deposundan uygun verileri geri yüklemeye başlar.
2.  Yedekleme aracısı, yedekleme sunucusundan verileri alır.
3.  Yedekleme sunucusu geri yükleme işini sonlandırır.

## <a name="deploy-the-solution"></a>Çözümü dağıtma

Çözümü dağıtmak için üç adım gerekir:

1. Ağ altyapısını hazırlayın.
2. StorSimple cihazınızı bir yedekleme hedefi olarak dağıtın.
3. Veead 'yi dağıtın.

Her adım aşağıdaki bölümlerde ayrıntılı olarak ele alınmıştır.

### <a name="set-up-the-network"></a>Ağı ayarlama

StorSimple, Azure bulutu ile tümleştirilmiş bir çözüm olduğundan, StorSimple, Azure bulutuna etkin ve çalışır bir bağlantı gerektirir. Bu bağlantı, bulut anlık görüntüleri, veri yönetimi ve meta veri aktarımı gibi işlemler için kullanılır ve daha eski, daha az erişilen verileri Azure bulut depolama alanına göre katmanlanır.

Çözümün en iyi şekilde gerçekleştirilebilmesi için, bu ağ en iyi yöntemlerini izlemenizi öneririz:

-   StorSimple katmanlamayı Azure 'a bağlayan bağlantı, bant genişliği gereksinimlerinize uymalıdır. RPO ve kurtarma süresi hedefi (RTO) SLA 'larınızı eşleştirmek için altyapı anahtarlarınızda gerekli hizmet kalitesi (QoS) düzeyini uygulayarak bunu elde edin.
-   Maksimum Azure Blob depolama erişim gecikmeleri 80 MS 'de olmalıdır.

### <a name="deploy-storsimple"></a>StorSimple dağıtma

Adım adım StorSimple dağıtım kılavuzu için bkz. Şirket [Içi StorSimple cihazınızı dağıtma](storsimple-deployment-walkthrough-u2.md).

### <a name="deploy-veeam"></a>Veead 'yi dağıt

Veeamınstallation en iyi uygulamaları için bkz. [Veeambackup & çoğaltma En Iyi uygulamaları](https://bp.veeam.expert/), [Veeaı Yardım Merkezi (teknik belgeler)](https://www.veeam.com/documentation-guides-datasheets.html)adresindeki kullanıcı kılavuzunu okuyun.

## <a name="set-up-the-solution"></a>Çözümü ayarlama

Bu bölümde bazı yapılandırma örnekleri gösterilmektedir. Aşağıdaki örneklerde ve önerilerle en temel ve temel uygulama gösterilmektedir. Bu uygulama, özel yedekleme gereksinimlerinize doğrudan uygulanmayabilir.

### <a name="set-up-storsimple"></a>StorSimple 'ı ayarlama

| StorSimple dağıtım görevleri  | Ek açıklamalar |
|---|---|
| Şirket içi StorSimple cihazınızı dağıtın. | Desteklenen sürümler: Güncelleştirme 3 ve sonraki sürümleri. |
| Yedekleme hedefini açın. | Yedekleme hedefi modunu açmak veya kapatmak ve durum almak için bu komutları kullanın. Daha fazla bilgi için bkz. [StorSimple cihazına uzaktan bağlanma](storsimple-remote-connect.md).</br> Yedekleme modunu açmak için: `Set-HCSBackupApplianceMode -enable`. </br> Yedekleme modunu devre dışı bırakmak için `Set-HCSBackupApplianceMode -disable`:. </br> Yedekleme modu ayarlarının geçerli durumunu almak için: `Get-HCSBackupApplianceMode`. |
| Yedekleme verilerini depolayan biriminiz için ortak bir birim kapsayıcısı oluşturun. Bir birim kapsayıcısındaki tüm veriler yinelenenleri kaldırılmış. | StorSimple birim kapsayıcıları yinelenenleri kaldırma etki alanlarını tanımlar.  |
| StorSimple birimleri oluşturun. | Birim boyutu bulut anlık görüntü süresi süresini etkilediğinden, boyutları beklenen kullanım için yakın olan birimler oluşturun. Bir birimin nasıl boyutlandıralınacağını öğrenmek için [bekletme ilkeleri](#retention-policies)hakkında bilgi edinin.</br> </br> StorSimple katmanlı birimleri kullanın ve **Bu birimi daha az sıklıkta erişilen arşiv verileri Için kullan** onay kutusunu seçin. </br> Yalnızca yerel olarak sabitlenmiş birimlerin kullanılması desteklenmez. |
| Tüm yedekleme hedefi birimleri için benzersiz bir StorSimple yedekleme ilkesi oluşturun. | Bir StorSimple yedekleme ilkesi, birim tutarlılığı grubunu tanımlar. |
| Anlık görüntülerin süre dolduğunda zamanlamayı devre dışı bırakın. | Anlık görüntüler, işlem sonrası bir işlem olarak tetiklenir. |

### <a name="set-up-the-host-backup-server-storage"></a>Konak yedekleme sunucusu depolamayı ayarlama

Konak yedekleme sunucusu depolamayı şu yönergelere göre ayarlayın:  

- Yayılmış birimleri kullanmayın (Windows disk yönetimi tarafından oluşturulur). Yayılmış birimler desteklenmez.
- Hacimlerinizi, 64 KB ayırma birimi boyutuyla NTFS kullanarak biçimlendirin.
- StorSimple birimlerini doğrudan Veead sunucusuna eşleyin.
    - Fiziksel sunucular için Iscsı kullanın.


## <a name="best-practices-for-storsimple-and-veeam"></a>StorSimple ve Veead için en iyi uygulamalar

Çözümünüzü aşağıdaki birkaç bölümde bulunan yönergelere göre ayarlayın.

### <a name="operating-system-best-practices"></a>İşletim sistemi en iyi uygulamaları

- NTFS dosya sistemi için Windows Server şifrelemesini ve Yinelenenleri kaldırmayı devre dışı bırakın.
- StorSimple birimlerinde Windows Server birleştirmesini devre dışı bırakın.
- StorSimple birimlerinde Windows Server dizinlemeyi devre dışı bırakın.
- Kaynak konakta (StorSimple birimlerine karşı değil) bir virüsten koruma taraması çalıştırın.
- Görev Yöneticisi 'nde varsayılan [Windows Server bakımını](https://msdn.microsoft.com/library/windows/desktop/hh848037.aspx) devre dışı bırakın. Bunu aşağıdaki yollarla yapın:
  - Windows Görev Zamanlayıcı 'de bakım yapılandırıcısını kapatın.
  - Windows Sysinternals 'dan [PsExec](https://technet.microsoft.com/sysinternals/bb897553.aspx) 'yi indirin. PsExec indirdikten sonra, Windows PowerShell 'i yönetici olarak çalıştırın ve şunu yazın:
    ```powershell
    psexec \\%computername% -s schtasks /change /tn “MicrosoftWindowsTaskSchedulerMaintenance Configurator" /disable
    ```

### <a name="storsimple-best-practices"></a>StorSimple en iyi uygulamaları

-   StorSimple cihazının [güncelleştirme 3 veya sonraki bir sürüme](storsimple-install-update-3.md)güncelleştirildiğinden emin olun.
-   Iscsı ve bulut trafiğini yalıtın. StorSimple ve Backup sunucusu arasındaki trafik için adanmış Iscsı bağlantıları kullanın.
-   StorSimple cihazınızın ayrılmış bir yedekleme hedefi olduğundan emin olun. RTO ve RPO 'larınızı etkilediği için karışık iş yükleri desteklenmez.

### <a name="veeam-best-practices"></a>Veead en iyi uygulamaları

-   Veeab veritabanı sunucuda yerel olmalıdır ve StorSimple biriminde yer almalıdır.
-   Olağanüstü durum kurtarma için, Veead veritabanını bir StorSimple biriminde yedekleyin.
-   Bu çözüm için Veeak tam ve artımlı yedeklemeleri destekliyoruz. Yapay ve değişiklik yedeklemeleri kullanmanızı öneririz.
-   Yedekleme veri dosyaları yalnızca belirli bir iş için verileri içermelidir. Örneğin, farklı işlere hiçbir medya ekleme yapılmasına izin verilmez.
-   İş doğrulamasını devre dışı bırakın. Gerekirse, doğrulama en son yedekleme işinden sonra zamanlanmalıdır. Bu işin yedekleme pencerenizi etkilediğini anlamak önemlidir.
-   Medya ön ayırmasını açın.
-   Paralel işlemenin açık olduğundan emin olun.
-   Sıkıştırmayı devre dışı bırakın.
-   Yedekleme işinde Yinelenenleri kaldırmayı kapatın.
-   İyileştirmeyi **LAN hedefine**ayarlayın.
-   **Etkin tam yedekleme oluştur** (her 2 hafta) seçeneğini etkinleştirin.
-   Yedekleme deposunda, **VM başına yedekleme dosyalarını kullanın**.
-   **İş başına birden fazla karşıya yükleme akışını** **8** ' e ayarlayın (en fazla 16 kullanılabilir). StorSimple cihazında CPU kullanımına göre bu sayıyı yukarı veya aşağı ayarlayın.

## <a name="retention-policies"></a>Elde tutma ilkeleri

En yaygın yedekleme bekletme ilkesi türlerinden biri, bir en babalar, babalar ve son (GFS) ilkesidir. Bir GFS ilkesinde, her gün artımlı bir yedekleme gerçekleştirilir ve tam yedeklemeler haftalık ve aylık yapılır. Bu ilke altı StorSimple katmanlı hacime neden olur: bir birim haftalık, aylık ve yıllık tam yedeklemeleri içerir; diğer beş birim günlük artımlı yedeklemeleri depolar.

Aşağıdaki örnekte, GFS dönüşü kullanıyoruz. Örnek aşağıdaki gibi kabul eder:

-   Yinelenenleri kaldırılmış veya sıkıştırılmış veriler kullanılır.
-   Tam yedeklemeler her biri 1 TiB.
-   Günlük artımlı yedeklemeler, her biri 500 GiB.
-   Ayda dört haftalık yedekleme tutulur.
-   Yılda iki aylık yedekleme tutulur.
-   10 yıl boyunca bir yıllık yedekleme tutulur.

Yukarıdaki varsayımlar temelinde, aylık ve yıllık tam yedeklemeler için 26-TiB StorSimple katmanlı bir birim oluşturun. Her bir artımlı günlük yedeklemelerin her biri için 5-TiB StorSimple katmanlı bir birim oluşturun.

| Yedekleme türü tutma | Boyut (TiB) | GFS çarpanı\* | Toplam Kapasite (TiB)  |
|---|---|---|---|
| Haftalık tam | 1 | 4  | 4 |
| Günlük artımlı | 0,5 | 20 (ayda eşit sayıda hafta döngüsü) | 12 (ek kota için 2) |
| Aylık tam | 1\. | 12 | 12 |
| Yıllık tam | 1\.  | 10 | 10 |
| GFS gereksinimi |   | 38 |   |
| Ek kota  | 4  |   | Toplam 42 GFS gereksinimi  |

\*GFS çarpanı, yedekleme ilkesi gereksinimlerinizi karşılamak için korumanız ve korumanız gereken kopya sayısıdır.

## <a name="set-up-veeam-storage"></a>Veead depolaması ayarlama

### <a name="to-set-up-veeam-storage"></a>Veead depolaması ayarlamak için

1.  Veead yedekleme ve çoğaltma konsolunda, **Depo araçları**' nda, **Yedekleme altyapısı**' na gidin. **Yedekleme depoları**' na sağ tıklayın ve ardından **yedek deposu Ekle**' yi seçin.

    ![Veeaı Yönetim Konsolu, yedekleme deposu sayfası](./media/storsimple-configure-backup-target-using-veeam/veeamimage1.png)

2.  **Yeni yedekleme deposu** iletişim kutusunda, depo için bir ad ve açıklama girin. **İleri**’yi seçin.

    ![Veead Yönetim Konsolu, ad ve açıklama sayfası](./media/storsimple-configure-backup-target-using-veeam/veeamimage2.png)

3.  Tür için **Microsoft Windows Server**' ı seçin. Veeab sunucusunu seçin. **İleri**’yi seçin.

    ![Veead Yönetim Konsolu, yedekleme deposu türünü seçin](./media/storsimple-configure-backup-target-using-veeam/veeamimage3.png)

4.  **Konumu**belirtmek için, birimi bulup seçin. **En fazla eşzamanlı görev sayısını sınırla:** onay kutusunu seçin ve değeri **4**olarak ayarlayın. Bu, her bir sanal makine (VM) işlenirken aynı anda yalnızca dört sanal diskin işlenmesini sağlar. **Gelişmiş** düğmesini seçin.

    ![Veeaı Yönetim Konsolu, birim seçin](./media/storsimple-configure-backup-target-using-veeam/veeamimage4.png)


5.  **Depolama uyumluluğu ayarları** iletişim kutusunda, **VM başına yedekleme dosyalarını kullan** onay kutusunu seçin.

    ![Veead Yönetim Konsolu, depolama uyumluluk ayarları](./media/storsimple-configure-backup-target-using-veeam/veeamimage5.png)

6.  **Yeni yedekleme deposu** iletişim kutusunda, **bağlama sunucusunda vPower NFS hizmetini etkinleştir (önerilir)** onay kutusunu seçin. **İleri**’yi seçin.

    ![Veeaı Yönetim Konsolu, yedekleme deposu sayfası](./media/storsimple-configure-backup-target-using-veeam/veeamimage6.png)

7.  Ayarları gözden geçirin ve ardından **İleri**' yi seçin.

    ![Veeaı Yönetim Konsolu, yedekleme deposu sayfası](./media/storsimple-configure-backup-target-using-veeam/veeamimage7.png)

    Veead sunucusuna bir depo eklenir.

## <a name="set-up-storsimple-as-a-primary-backup-target"></a>Birincil yedekleme hedefi olarak StorSimple 'ı ayarlama

> [!IMPORTANT]
> Buluta katmanlı bir yedekten veri geri yükleme bulut hızında gerçekleşir.

Aşağıdaki şekilde, tipik bir birimin bir yedekleme işine eşlenmesi gösterilmektedir. Bu durumda, tüm haftalık yedeklemeler Cumartesi tam diskine eşlenir ve artımlı yedeklemeler Pazartesi-Cuma artımlı disklere eşlenir. Tüm yedeklemeler ve geri yüklemeler StorSimple katmanlı bir birimdir.

![Birincil yedekleme hedefi yapılandırma mantıksal diyagramı](./media/storsimple-configure-backup-target-using-veeam/primarybackuptargetdiagram.png)

### <a name="storsimple-as-a-primary-backup-target-gfs-schedule-example"></a>Birincil yedekleme hedefi GFS zamanlama örneği olarak StorSimple

Dört hafta, aylık ve yıllık için bir GFS döndürme zamanlaması örneği aşağıda verilmiştir:

| Sıklık/yedekleme türü | Tam | Artımlı (gün 1-5)  |   
|---|---|---|
| Haftalık (hafta 1-4) | Cumartesi | Pazartesi-Cuma |
| Aylık  | Cumartesi  |   |
| Yıllık | Cumartesi  |   |


### <a name="assign-storsimple-volumes-to-a-veeam-backup-job"></a>Bir Veead yedekleme işine StorSimple birimleri atama

Birincil yedekleme hedefi senaryosunda, birincil Veead StorSimple haciminiz ile günlük bir iş oluşturun. İkincil bir yedekleme hedefi senaryosunda, doğrudan bağlı depolama (DAS), ağa bağlı depolama (NAS) veya yalnızca bir dizi disk (JBOD) depolama alanı kullanarak günlük bir iş oluşturun.

#### <a name="to-assign-storsimple-volumes-to-a-veeam-backup-job"></a>Bir Veead yedekleme işine StorSimple birimleri atamak için

1.  Veead yedekleme ve çoğaltma konsolunda, **yedekleme & çoğaltması**' nı seçin. **Yedekle**' ye sağ tıklayın ve ardından ortamınıza bağlı olarak **VMware** veya **Hyper-V**' yi seçin.

    ![Veeaı Yönetim Konsolu, yeni yedekleme işi](./media/storsimple-configure-backup-target-using-veeam/veeamimage8.png)

2.  **Yeni yedekleme işi** iletişim kutusunda günlük yedekleme işi için bir ad ve açıklama girin.

    ![Veeaı Yönetim Konsolu, yeni yedekleme işi sayfası](./media/storsimple-configure-backup-target-using-veeam/veeamimage9.png)

3.  Yedeklenecek bir sanal makine seçin.

    ![Veeaı Yönetim Konsolu, yeni yedekleme işi sayfası](./media/storsimple-configure-backup-target-using-veeam/veeamimage10.png)

4.  **Yedekleme proxy 'si** ve **yedekleme deposu**için istediğiniz değerleri seçin. Yerel olarak bağlı depolamada ortamınızın RPO ve RTO tanımlarına göre **diskte tutulması Için geri yükleme noktaları** için bir değer seçin. **Gelişmiş**'i seçin.

    ![Veeaı Yönetim Konsolu, yeni yedekleme işi sayfası](./media/storsimple-configure-backup-target-using-veeam/veeamimage11.png)

5. **Gelişmiş ayarlar** iletişim kutusunda, **yedekleme** sekmesinde, **artımlı**' i seçin. **Düzenli olarak yapay tam yedeklemeler oluştur** onay kutusunun temizlenmiş olduğundan emin olun. **Düzenli aralıklarla etkin tam yedeklemeler oluştur** onay kutusunu seçin. **Etkin tam yedekleme**altında, Cumartesi için **haftalık seçili günler** onay kutusunu seçin.

    ![Veeaı Yönetim Konsolu, yeni yedekleme işi Gelişmiş ayarlar sayfası](./media/storsimple-configure-backup-target-using-veeam/veeamimage12.png)

6. **Depolama** sekmesinde, **satır içi yinelenen verileri kaldırmayı etkinleştir** onay kutusunun temizlenmiş olduğundan emin olun. **Takas dosyası bloklarını hariç tut** onay kutusunu seçin ve **silinen dosya bloklarını hariç tut** onay kutusunu seçin. **Sıkıştırma düzeyini** **none**olarak ayarlayın. Dengeli performans ve yinelenenleri kaldırma için, **depolama iyileştirmesini** **LAN hedefe**ayarlayın. **Tamam**’ı seçin.

    ![Veeaı Yönetim Konsolu, yeni yedekleme işi Gelişmiş ayarlar sayfası](./media/storsimple-configure-backup-target-using-veeam/veeamimage13.png)

    Veeab yinelenenleri kaldırma ve sıkıştırma ayarları hakkında daha fazla bilgi için bkz. [veri sıkıştırma ve yinelenenleri kaldırma](https://helpcenter.veeam.com/backup/vsphere/compression_deduplication.html).

7.  **Yedekleme Işini Düzenle** iletişim kutusunda, **uygulama durumunu algılayan işlemeyi etkinleştir** onay kutusunu seçebilirsiniz (isteğe bağlı).

    ![Veeaı Yönetim Konsolu, yeni yedekleme işi Konuk işleme sayfası](./media/storsimple-configure-backup-target-using-veeam/veeamimage14.png)

8.  Zamanlamayı, her gün bir kez çalıştırılacak şekilde ayarlayın.

    ![Veeaı Yönetim Konsolu, yeni yedekleme işi zamanlama sayfası](./media/storsimple-configure-backup-target-using-veeam/veeamimage15.png)

## <a name="set-up-storsimple-as-a-secondary-backup-target"></a>İkincil yedekleme hedefi olarak StorSimple 'ı ayarlama

> [!NOTE]
> Buluta katmanlı bir yedekten veri geri yüklemeleri bulut hızlarından oluşur.

Bu modelde, geçici önbellek olarak kullanılacak bir depolama medyası (StorSimple dışında) olmalıdır. Örneğin, boşluk, giriş/çıkış (g/ç) ve bant genişliğini karşılamak için yedekli bir bağımsız diskler dizisi (RAID) birimi kullanabilirsiniz. RAID 5, 50 ve 10 kullanmanızı öneririz.

Aşağıdaki şekilde, tipik kısa süreli bekletme yerel (sunucu) birimleri ve uzun süreli bekletme Arşivi birimleri gösterilmektedir. Bu senaryoda, tüm yedeklemeler yerel (sunucu) RAID birimi üzerinde çalışır. Bu yedeklemeler düzenli aralıklarla çoğaltılır ve bir arşiv birimine arşivlenir. Kısa vadeli saklama kapasitesini ve performans gereksinimlerinizi işleyebilmesi için yerel (sunucu) RAID birimine göre boyutlandırmamak önemlidir.

![İkincil yedekleme hedefi mantıksal diyagramı olarak StorSimple](./media/storsimple-configure-backup-target-using-veeam/secondarybackuptargetdiagram.png)

### <a name="storsimple-as-a-secondary-backup-target-gfs-example"></a>İkincil yedekleme hedefi GFS olarak StorSimple örneği

Aşağıdaki tabloda, yerel ve StorSimple disklerinde çalışacak yedeklemelerin nasıl ayarlanacağı gösterilmektedir. Bireysel ve toplam kapasite gereksinimlerini içerir.

| Yedekleme türü ve bekletme | Yapılandırılan depolama alanı | Boyut (TiB) | GFS çarpanı | Toplam kapasite\* (Tib) |
|---|---|---|---|---|
| Hafta 1 (tam ve artımlı) |Yerel disk (kısa vadeli)| 1\. | 1\. | 1\. |
| StorSimple hafta 2-4 |StorSimple diski (uzun vadeli) | 1\. | 4 | 4 |
| Aylık tam |StorSimple diski (uzun vadeli) | 1\. | 12 | 12 |
| Yıllık tam |StorSimple diski (uzun vadeli) | 1 | 1\. | 1\. |
|GFS birimleri boyut gereksinimi |  |  |  | 18*|

\*Toplam kapasite, StorSimple disklerinin 17 TiB ve yerel RAID birimi 'nin 1 TiB 'leri içerir.


### <a name="gfs-example-schedule"></a>GFS örnek zamanlaması

GFS haftalık, aylık ve yıllık zamanlama

| Hafta | Tam | Artımlı gün 1 | Artımlı gün 2 | Artımlı gün 3 | Artımlı gün 4 | Artımlı gün 5 |
|---|---|---|---|---|---|---|
| Hafta 1 | Yerel RAID birimi  | Yerel RAID birimi | Yerel RAID birimi | Yerel RAID birimi | Yerel RAID birimi | Yerel RAID birimi |
| Hafta 2 | StorSimple hafta 2-4 |   |   |   |   |   |
| Hafta 3 | StorSimple hafta 2-4 |   |   |   |   |   |
| Hafta 4 | StorSimple hafta 2-4 |   |   |   |   |   |
| Aylık | StorSimple aylık |   |   |   |   |   |
| Yıllık | StorSimple yıllık  |   |   |   |   |   |

### <a name="assign-storsimple-volumes-to-a-veeam-copy-job"></a>Bir Veead kopyalama işine StorSimple birimleri atama

#### <a name="to-assign-storsimple-volumes-to-a-veeam-copy-job"></a>Bir Veeab kopyalama işine StorSimple birimleri atamak için

1.  Veead yedekleme ve çoğaltma konsolunda, **yedekleme & çoğaltması**' nı seçin. **Yedekle**' ye sağ tıklayın ve ardından ortamınıza bağlı olarak **VMware** veya **Hyper-V**' yi seçin.

    ![Veeaı Yönetim Konsolu, yeni yedekleme kopyalama işi sayfası](./media/storsimple-configure-backup-target-using-veeam/veeamimage16.png)

2.  **Yeni yedekleme kopyalama işi** iletişim kutusunda iş için bir ad ve açıklama girin.

    ![Veeaı Yönetim Konsolu, yeni yedekleme kopyalama işi sayfası](./media/storsimple-configure-backup-target-using-veeam/veeamimage17.png)

3.  İşlemek istediğiniz VM 'Leri seçin. Yedekler arasından seçim yapın ve ardından daha önce oluşturduğunuz günlük yedeklemeyi seçin.

    ![Veeaı Yönetim Konsolu, yeni yedekleme kopyalama işi sayfası](./media/storsimple-configure-backup-target-using-veeam/veeamimage18.png)

4.  Gerekirse, nesneleri yedekleme kopyalama işinden hariç tutun.

5.  Yedekleme deponuzu seçin ve **geri yükleme noktalarının saklanacak**bir değer ayarlayın. **Arşiv amaçlarıyla aşağıdaki geri yükleme noktalarını tut** onay kutusunu seçtiğinizden emin olun. Yedekleme sıklığını tanımlayın ve **Gelişmiş**' i seçin.

    ![Veeaı Yönetim Konsolu, yeni yedekleme kopyalama işi sayfası](./media/storsimple-configure-backup-target-using-veeam/veeamimage19.png)

6.  Aşağıdaki gelişmiş ayarları belirtin:

    * **Bakım** sekmesinde, depolama düzeyi bozulma koruması ' nı kapatın.

    ![Veeaı Yönetim Konsolu, yeni yedekleme kopyalama işi gelişmiş ayarları sayfası](./media/storsimple-configure-backup-target-using-veeam/veeamimage20.png)

    * **Depolama** sekmesinde, yinelenenleri kaldırma ve sıkıştırma özelliğinin kapalı olduğundan emin olun.

    ![Veeaı Yönetim Konsolu, yeni yedekleme kopyalama işi gelişmiş ayarları sayfası](./media/storsimple-configure-backup-target-using-veeam/veeamimage21.png)

7.  Veri aktarımının doğrudan olduğunu belirtin.

8.  Yedekleme kopyası pencere zamanlamasını gereksinimlerinize göre tanımlayın ve ardından Sihirbazı sona erdirin.

Daha fazla bilgi için bkz. [Yedekleme Kopyalama Işleri oluşturma](https://helpcenter.veeam.com/backup/hyperv/backup_copy_create.html).

## <a name="storsimple-cloud-snapshots"></a>StorSimple bulut anlık görüntüleri

StorSimple bulut anlık görüntüleri, StorSimple cihazınızda bulunan verileri korur. Bulut anlık görüntüsü oluşturmak, yerel yedekleme bantlarını site dışı bir tesise aktarma ile eşdeğerdir. Azure coğrafi olarak yedekli depolama kullanırsanız, bir bulut anlık görüntüsü oluşturmak, yedekleme bantlarını birden çok siteye aktarmanın eşdeğeridir. Bir olağanüstü durum sonrasında bir cihazı geri yüklemeniz gerekiyorsa, başka bir StorSimple cihazını çevrimiçi duruma getirebilir ve yük devretme yapabilirsiniz. Yük devretmeden sonra, verilere (bulut hızlarından) en son bulut anlık görüntüsünden erişebilirsiniz.

Aşağıdaki bölümde, yedekleme sonrası işlem sırasında StorSimple bulut anlık görüntülerini başlatmak ve silmek için kısa bir komut dosyasının nasıl oluşturulacağı açıklanmaktadır.

> [!NOTE]
> El ile veya program aracılığıyla oluşturulan anlık görüntüler StorSimple anlık görüntü süre sonu ilkesini takip etmez. Bu anlık görüntülerin el ile veya program aracılığıyla silinmesi gerekir.

### <a name="start-and-delete-cloud-snapshots-by-using-a-script"></a>Betik kullanarak bulut anlık görüntülerini başlatma ve silme

> [!NOTE]
> Bir StorSimple anlık görüntüsünü silmeden önce uyumluluk ve veri saklama repercuslarını dikkatle değerlendirin. Yedekleme sonrası betiğin nasıl çalıştırılacağı hakkında daha fazla bilgi için, bkz. Veead belgeleri.


### <a name="backup-lifecycle"></a>Yedekleme yaşam döngüsü

![Yedekleme yaşam döngüsü diyagramı](./media/storsimple-configure-backup-target-using-veeam/backuplifecycle.png)

### <a name="requirements"></a>Gereksinimler

-   Betiği çalıştıran sunucunun Azure bulut kaynaklarına erişimi olmalıdır.
-   Kullanıcı hesabı gerekli izinlere sahip olmalıdır.
-   İlişkili StorSimple birimlerine sahip bir StorSimple yedekleme ilkesi ayarlanmalıdır, ancak açılmamalıdır.
-   StorSimple kaynak adı, kayıt anahtarı, cihaz adı ve yedekleme ilkesi KIMLIĞI gerekir.

### <a name="to-start-or-delete-a-cloud-snapshot"></a>Bir bulut anlık görüntüsünü başlatmak veya silmek için

1. [Azure PowerShell'i yükleme](/powershell/azure/overview).
2. [Manage-CloudSnapshots. ps1](https://github.com/anoobbacker/storsimpledevicemgmttools/blob/master/Manage-CloudSnapshots.ps1) PowerShell betiğini indirip kurun.
3. Betiği çalıştıran sunucuda PowerShell 'i yönetici olarak çalıştırın. Betiğin hangi değişiklikleri yaptığını görmek için betiği `-WhatIf $true` ile çalıştırtığınızdan emin olun. Doğrulama tamamlandıktan sonra Pass `-WhatIf $false`. Aşağıdaki komutu çalıştırın:
   ```powershell
   .\Manage-CloudSnapshots.ps1 -SubscriptionId [Subscription Id] -TenantId [Tenant ID] -ResourceGroupName [Resource Group Name] -ManagerName [StorSimple Device Manager Name] -DeviceName [device name] -BackupPolicyName [backup policyname] -RetentionInDays [Retention days] -WhatIf [$true or $false]
   ```
4. Betiği yedekleme işinize eklemek için Veeae iş gelişmiş seçeneklerinizi düzenleyin.

    ![Veeaz Yedekleme Gelişmiş ayarları komut dosyaları sekmesi](./media/storsimple-configure-backup-target-using-veeam/veeamimage22.png)

StorSimple bulut anlık görüntü yedekleme ilkenizi, günlük yedekleme işinizin sonunda bir işlem sonrası betiği olarak çalıştırmanızı öneririz. RPO ve RTO 'larınızı karşılamanıza yardımcı olmak için yedekleme uygulaması ortamınızı yedekleme ve geri yükleme hakkında daha fazla bilgi için lütfen yedekleme mimarinize başvurun.

## <a name="storsimple-as-a-restore-source"></a>Geri yükleme kaynağı olarak StorSimple

Bir StorSimple cihazından geri yükleme, herhangi bir blok depolama cihazından geri yükleme gibi çalışır. Buluta katmanlı verilerin geri yüklemeleri bulut hızlarında oluşur. Yerel veriler için geri yükleme, cihazın yerel disk hızında oluşur.

Veead, Veead konsolundaki yerleşik gezgin görünümleri aracılığıyla StorSimple aracılığıyla hızlı, ayrıntılı, dosya düzeyinde kurtarma edinirsiniz. E-posta iletileri, Active Directory nesneleri ve SharePoint öğeleri yedeklemelerinden ayrı ayrı öğeleri kurtarmak için Veeam araştırmalarını kullanın. Kurtarma, şirket içi VM kesintisi olmadan yapılabilir. Ayrıca Azure SQL veritabanı ve Oracle veritabanları için zaman içinde kurtarma yapabilirsiniz. Veeaa ve StorSimple, Azure 'dan hızlı ve kolay bir şekilde öğe düzeyinde kurtarma işlemi yapar. Geri yükleme gerçekleştirme hakkında daha fazla bilgi için, bkz. Veead belgeleri:

- [Exchange Server](https://www.veeam.com/microsoft-exchange-recovery.html) için
- [Active Directory](https://www.veeam.com/microsoft-active-directory-explorer.html) için
- [SQL Server](https://www.veeam.com/microsoft-sql-server-explorer.html) için
- [SharePoint](https://www.veeam.com/microsoft-sharepoint-recovery-explorer.html) için
- [Oracle](https://www.veeam.com/oracle-backup-recovery-explorer.html) için


## <a name="storsimple-failover-and-disaster-recovery"></a>StorSimple yük devretme ve olağanüstü durum kurtarma

> [!NOTE]
> Yedekleme hedefi senaryolarında StorSimple Cloud Appliance, geri yükleme hedefi olarak desteklenmez.

Olağanüstü bir durum, çeşitli faktörlerden kaynaklanıyor olabilir. Aşağıdaki tabloda, yaygın olağanüstü durum kurtarma senaryoları listelenmektedir.

| Senaryo | Etkisi | Kurtarma | Notlar |
|---|---|---|---|
| StorSimple cihaz hatası | Yedekleme ve geri yükleme işlemleri kesintiye uğrar. | Başarısız olan cihazı değiştirin ve [StorSimple yük devretme ve olağanüstü durum kurtarma](storsimple-device-failover-disaster-recovery.md)gerçekleştirin. | Cihaz kurtarmasından sonra geri yükleme gerçekleştirmeniz gerekirse, tam veri çalışma kümeleri buluttan yeni cihaza alınır. Tüm işlemler bulut hızlardır. Dizin ve Katalog yeniden tarama işlemi, tüm yedekleme kümelerinin bulut katmanından yerel cihaz katmanına taranmasını ve çekilmesine neden olabilir ve bu da zaman alan bir işlemdir. |
| Veeab sunucusu hatası | Yedekleme ve geri yükleme işlemleri kesintiye uğrar. | Backup sunucusunu yeniden oluşturun ve veritabanı geri yükleme işlemini [Veeamhelp Center (teknik belgeler)](https://www.veeam.com/documentation-guides-datasheets.html)bölümünde açıklandığı gibi gerçekleştirin.  | Acil durum kurtarma sitesinde Veead sunucusunu yeniden oluşturmanız veya geri yüklemeniz gerekir. Veritabanını en son noktaya geri yükleyin. Geri yüklenen Veead veritabanı en son yedekleme işleriniz ile eşitlenmediğinde, dizin oluşturma ve kataloglama gereklidir. Bu dizin ve Katalog yeniden tarama işlemi, tüm yedekleme kümelerinin bulut katmanından yerel cihaz katmanına taranmasını ve çekilmesine neden olabilir. Bu, daha fazla zaman yoğunluğu sağlar. |
| Hem yedekleme sunucusu hem de StorSimple kaybına neden olan site hatası | Yedekleme ve geri yükleme işlemleri kesintiye uğrar. | Önce StorSimple 'ı geri yükleyin ve ardından Veead 'yi geri yükleyin. | Önce StorSimple 'ı geri yükleyin ve ardından Veead 'yi geri yükleyin. Cihazın kurtarmasından sonra geri yükleme gerçekleştirmeniz gerekirse, tam veri çalışma kümeleri buluttan yeni cihaza alınır. Tüm işlemler bulut hızlardır. |


## <a name="references"></a>Referanslar

Bu makale için aşağıdaki belgelere başvuruldu:

- [StorSimple çok yollu g/ç kurulumu](storsimple-configure-mpio-windows-server.md)
- [Depolama senaryoları: Ölçülü kaynak sağlama](https://msdn.microsoft.com/library/windows/hardware/dn265487.aspx)
- [GPT sürücüleri kullanma](https://msdn.microsoft.com/windows/hardware/gg463524.aspx#EHD)
- [Paylaşılan klasörler için gölge kopyaları ayarlama](https://technet.microsoft.com/library/cc771893.aspx)

## <a name="next-steps"></a>Sonraki adımlar

- [Bir yedekleme kümesinden geri yükleme](storsimple-restore-from-backup-set-u2.md)hakkında daha fazla bilgi edinin.
- [Cihaz yük devretmesini ve olağanüstü durum kurtarmayı](storsimple-device-failover-disaster-recovery.md)gerçekleştirme hakkında daha fazla bilgi edinin.
