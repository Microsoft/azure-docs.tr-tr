---
title: NetBackup ile bir yedekleme hedefi olarak StorSimple 8000 serisi | Microsoft Docs
description: VERITAS NetBackup ile StorSimple yedekleme hedefi yapılandırmasını açıklar.
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
ms.date: 06/15/2017
ms.author: matd
ms.openlocfilehash: 957fff73f2406e0e057a7c978dd76a6bd9c156b7
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/31/2019
ms.locfileid: "67876200"
---
# <a name="storsimple-as-a-backup-target-with-netbackup"></a>NetBackup ile bir yedekleme hedefi olarak StorSimple

## <a name="overview"></a>Genel Bakış

Azure StorSimple, Microsoft 'un sunduğu bir karma bulut depolama çözümüdür. StorSimple, şirket içi çözümün uzantısı olarak bir Azure depolama hesabı kullanarak üstel veri artışına ilişkin karmaşıklıkları giderir ve şirket içi depolama ve bulut depolaması genelinde verileri otomatik olarak katmanlama.

Bu makalede, VERITAS NetBackup ile StorSimple tümleştirmesini ve her iki çözümü tümleştirmek için en iyi uygulamaları tartıştık. Ayrıca, Veritas NetBackup 'ı StorSimple ile en iyi tümleşecek şekilde ayarlamaya yönelik öneriler de sunuyoruz. Tek tek yedekleme gereksinimlerini ve hizmet düzeyi sözleşmelerini (SLA 'Lar) karşılamak üzere VERITAS NetBackup 'ı ayarlamaya yönelik en iyi yol için VERITAS en iyi uygulamaları, yedekleme mimarları ve yöneticileri erteliyoruz.

Yapılandırma adımlarını ve temel kavramları gösterdiğimiz halde bu makalede, adım adım bir yapılandırma veya Yükleme Kılavuzu olması gerekir. Temel bileşenlerin ve altyapının çalışma sırasında olduğunu ve tanımladığımız kavramları desteklemeye hazırlandığını varsayalım.

### <a name="who-should-read-this"></a>Bunu kimler okumalı?

Bu makaledeki bilgiler, depolama, Windows Server 2012 R2, Ethernet, Cloud Services ve VERITAS NetBackup hakkında bilgi sahibi olan yedekleme yöneticileri, depolama yöneticileri ve depolama mimarları için en yararlı olacaktır.

### <a name="supported-versions"></a>Desteklenen sürümler

-   NetBackup 7.7. x ve üzeri sürümleri
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
![StorSimple katmanlama diyagramı](./media/storsimple-configure-backup-target-using-netbackup/image1.jpg)

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

| Depolama kapasitesi       | 8100          | 8600            |
|------------------------|---------------|-----------------|
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

![Birincil yedekleme hedefi mantıksal diyagramı olarak StorSimple](./media/storsimple-configure-backup-target-using-netbackup/primarybackuptargetlogicaldiagram.png)

### <a name="primary-target-backup-logical-steps"></a>Birincil hedef yedekleme mantıksal adımları

1.  Yedekleme sunucusu hedef yedekleme aracısıyla iletişim kurar ve yedekleme Aracısı verileri yedekleme sunucusuna iletir.
2.  Yedekleme sunucusu StorSimple katmanlı birimlere veri yazar.
3.  Yedekleme sunucusu, katalog veritabanını güncelleştirir ve ardından yedekleme işini sonlandırır.
4.  Anlık görüntü betiği StorSimple Snapshot Manager 'ı tetikler (Başlat veya Sil).
5.  Yedekleme sunucusu, bir bekletme ilkesine göre süresi biten yedeklemeleri siler.

### <a name="primary-target-restore-logical-steps"></a>Birincil hedef geri yükleme mantıksal adımları

1.  Yedekleme sunucusu, depolama deposundan uygun verileri geri yüklemeye başlar.
2.  Yedekleme aracısı, yedekleme sunucusundan verileri alır.
3.  Yedekleme sunucusu geri yükleme işini sonlandırır.

## <a name="storsimple-as-a-secondary-backup-target"></a>İkincil yedekleme hedefi olarak StorSimple

Bu senaryoda, StorSimple birimleri öncelikle uzun süreli saklama veya arşivleme için kullanılır.

Aşağıdaki şekilde, ilk yedeklemelerin ve geri yüklemeler için yüksek performanslı bir birimin hedeflemesini gösteren bir mimari gösterilmektedir. Bu yedeklemeler, küme zamanlamasında bir StorSimple katmanlı birime kopyalanır ve arşivlenir.

En yüksek performanslı haciminizi, bekletme ilkesi kapasitenizi ve performans gereksinimlerinizi işleyebilecek şekilde boyutlandırmak önemlidir.

![İkincil yedekleme hedefi mantıksal diyagramı olarak StorSimple](./media/storsimple-configure-backup-target-using-netbackup/secondarybackuptargetlogicaldiagram.png)

### <a name="secondary-target-backup-logical-steps"></a>İkincil hedef yedekleme mantıksal adımları

1.  Yedekleme sunucusu hedef yedekleme aracısıyla iletişim kurar ve yedekleme Aracısı verileri yedekleme sunucusuna iletir.
2.  Yedekleme sunucusu, verileri yüksek performanslı depolamaya yazar.
3.  Yedekleme sunucusu, katalog veritabanını güncelleştirir ve ardından yedekleme işini sonlandırır.
4.  Yedekleme sunucusu yedeklemeleri bir bekletme ilkesine göre StorSimple 'a kopyalar.
5.  Anlık görüntü betiği StorSimple Snapshot Manager 'ı tetikler (Başlat veya Sil).
6.  Yedekleme sunucusu, süresi biten yedeklemeleri bir bekletme ilkesine göre siler.

### <a name="secondary-target-restore-logical-steps"></a>İkincil hedef geri yükleme mantıksal adımları

1.  Yedekleme sunucusu, depolama deposundan uygun verileri geri yüklemeye başlar.
2.  Yedekleme aracısı, yedekleme sunucusundan verileri alır.
3.  Yedekleme sunucusu geri yükleme işini sonlandırır.

## <a name="deploy-the-solution"></a>Çözümü dağıtma

Bu çözümü dağıtmak için üç adım gerekir:
1. Ağ altyapısını hazırlayın.
2. StorSimple cihazınızı bir yedekleme hedefi olarak dağıtın.
3. VERITAS NetBackup 'ı dağıtın.

Her adım aşağıdaki bölümlerde ayrıntılı olarak ele alınmıştır.

### <a name="set-up-the-network"></a>Ağı ayarlama

StorSimple, Azure bulutu ile tümleştirilmiş bir çözüm olduğundan, StorSimple, Azure bulutuna etkin ve çalışır bir bağlantı gerektirir. Bu bağlantı, bulut anlık görüntüleri, veri yönetimi ve meta veri aktarımı gibi işlemler için kullanılır ve daha eski, daha az erişilen verileri Azure bulut depolama alanına göre katmanlanır.

Çözümün en iyi şekilde gerçekleştirilebilmesi için, bu ağ en iyi yöntemlerini izlemenizi öneririz:

-   StorSimple katmanlamayı Azure 'a bağlayan bağlantı, bant genişliği gereksinimlerinize uymalıdır. Bunu başarmak için, RPO ve kurtarma süresi hedefi (RTO) SLA 'larınızı eşleştirmek üzere altyapı anahtarlarınızda uygun hizmet kalitesi (QoS) düzeyini uygulayın.

-   Maksimum Azure Blob depolama erişim gecikmeleri 80 MS 'de olmalıdır.

### <a name="deploy-storsimple"></a>StorSimple dağıtma

Adım adım StorSimple dağıtım kılavuzu için bkz. Şirket [Içi StorSimple cihazınızı dağıtma](storsimple-deployment-walkthrough-u2.md).

### <a name="deploy-netbackup"></a>NetBackup dağıt

Adım adım NetBackup 7.7. x dağıtım kılavuzu için bkz. [NetBackup 7.7. x belgeleri](http://www.veritas.com/docs/000094423).

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

- Yayılmış birimleri kullanmayın (Windows disk yönetimi tarafından oluşturulan); yayılmış birimler desteklenmez.
- Hacimlerinizi, 64 KB 'lik ayırma boyutuyla NTFS kullanarak biçimlendirin.
- StorSimple birimlerini doğrudan NetBackup sunucusuna eşleyin.
    - Fiziksel sunucular için Iscsı kullanın.
    - Sanal sunucular için geçiş disklerini kullanın.


## <a name="best-practices-for-storsimple-and-netbackup"></a>StorSimple ve NetBackup için en iyi uygulamalar

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

### <a name="netbackup-best-practices"></a>NetBackup en iyi uygulamaları

-   NetBackup veritabanının sunucuda yerel olması ve StorSimple biriminde yer olmaması gerekir.
-   Olağanüstü durum kurtarma için, NetBackup veritabanını bir StorSimple biriminde yedekleyin.
-   Bu çözüm için NetBackup tam ve artımlı yedeklemeleri (NetBackup 'ta değişiklik artımlı yedeklemeleri olarak da bilinir) destekliyoruz. Yapay ve birikimli artımlı yedeklemeler kullanmanızı öneririz.
-   Yedekleme veri dosyaları yalnızca belirli bir iş için verileri içermelidir. Örneğin, farklı işlere hiçbir medya ekleme yapılmasına izin verilmez.

Bu gereksinimleri uygulamak için en son NetBackup ayarları ve en iyi uygulamalar için [www.Veritas.com](https://www.veritas.com)adresindeki NetBackup belgelerine bakın.


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
| Haftalık tam | 1\. | 4  | 4 |
| Günlük artımlı | 0,5 | 20 (ayda eşit sayıda hafta döngüsü) | 12 (ek kota için 2) |
| Aylık tam | 1\. | 12 | 12 |
| Yıllık tam | 1\.  | 10 | 10 |
| GFS gereksinimi |   | 38 |   |
| Ek kota  | 4  |   | Toplam 42 GFS gereksinimi  |

\*GFS çarpanı, yedekleme ilkesi gereksinimlerinizi karşılamak için korumanız ve korumanız gereken kopya sayısıdır.

## <a name="set-up-netbackup-storage"></a>NetBackup depolaması ayarlama

### <a name="to-set-up-netbackup-storage"></a>NetBackup depolaması ayarlamak için

1.  NetBackup yönetim konsolunda **medya ve cihaz yönetim** > **aygıtları** > **disk havuzları**' nı seçin. Disk havuzu yapılandırma sihirbazında, **Advanceddisk**depolama sunucusu türünü seçin ve ardından **İleri**' yi seçin.

    ![NetBackup Yönetim Konsolu, disk havuzu yapılandırma Sihirbazı](./media/storsimple-configure-backup-target-using-netbackup/nbimage1.png)

2.  Sunucunuzu seçin ve ardından **İleri**' yi seçin.

    ![NetBackup Yönetim Konsolu, sunucuyu seçin](./media/storsimple-configure-backup-target-using-netbackup/nbimage2.png)

3.  StorSimple biriminizi seçin.

    ![NetBackup Yönetim Konsolu, StorSimple birim diskini seçin](./media/storsimple-configure-backup-target-using-netbackup/nbimage3.png)

4.  Yedekleme hedefi için bir ad girin ve ardından **İleri** > ' yi seçerek Sihirbazı sona erdirin.

5.  Ayarları gözden geçirin ve ardından **son**' u seçin.

6.  Her birim atamasının sonunda, depolama cihazı ayarlarını [StorSimple ve NetBackup Için en iyi yöntemler için](#best-practices-for-storsimple-and-netbackup)önerilen yöntemlerle eşleşecek şekilde değiştirin.

7. StorSimple birimlerinizi atamayı tamamlayana kadar 1-6 arasındaki adımları yineleyin.

    ![NetBackup Yönetim Konsolu, disk yapılandırması](./media/storsimple-configure-backup-target-using-netbackup/nbimage5.png)

## <a name="set-up-storsimple-as-a-primary-backup-target"></a>Birincil yedekleme hedefi olarak StorSimple 'ı ayarlama

> [!NOTE]
> Buluta katmanlı bir yedekten veri geri yüklemeleri bulut hızlarından oluşur.

Aşağıdaki şekilde, tipik bir birimin bir yedekleme işine eşlenmesi gösterilmektedir. Bu durumda, tüm haftalık yedeklemeler Cumartesi tam diskine eşlenir ve artımlı yedeklemeler Pazartesi-Cuma artımlı disklere eşlenir. Tüm yedeklemeler ve geri yüklemeler StorSimple katmanlı bir birimdir.

![Birincil yedekleme hedefi yapılandırma mantıksal diyagramı](./media/storsimple-configure-backup-target-using-netbackup/primarybackuptargetdiagram.png)

### <a name="storsimple-as-a-primary-backup-target-gfs-schedule-example"></a>Birincil yedekleme hedefi GFS zamanlama örneği olarak StorSimple

Dört hafta, aylık ve yıllık için bir GFS döndürme zamanlaması örneği aşağıda verilmiştir:

| Sıklık/yedekleme türü | Tam | Artımlı (gün 1-5)  |   
|---|---|---|
| Haftalık (hafta 1-4) | Cumartesi | Pazartesi-Cuma |
| Aylık  | Cumartesi  |   |
| Yıllık | Cumartesi  |   |

## <a name="assigning-storsimple-volumes-to-a-netbackup-backup-job"></a>Bir NetBackup yedekleme işine StorSimple birimleri atama

Aşağıdaki sıra, NetBackup 'ın ve hedef konağın NetBackup Aracısı yönergelerine uygun olarak yapılandırıldığını varsayar.

### <a name="to-assign-storsimple-volumes-to-a-netbackup-backup-job"></a>Bir NetBackup yedekleme işine StorSimple birimleri atamak için

1. NetBackup yönetim konsolunda, **NetBackup Management**' ı seçin, **ilkeler**' e sağ tıklayın ve ardından **Yeni ilke**' yi seçin.

   ![NetBackup Yönetim Konsolu, yeni bir ilke oluşturun](./media/storsimple-configure-backup-target-using-netbackup/nbimage6.png)

2. **Yeni Ilke Ekle** iletişim kutusunda, ilke için bir ad girin ve ardından **Ilke Yapılandırma Sihirbazı 'nı kullan** onay kutusunu seçin. **Tamam**’ı seçin.

   ![NetBackup Yönetim Konsolu, yeni bir Ilke Ekle iletişim kutusu](./media/storsimple-configure-backup-target-using-netbackup/nbimage7.png)

3. Yedekleme Ilkesi Yapılandırma sihirbazında, istediğiniz yedekleme türünü seçin ve ardından **İleri**' yi seçin.

   ![NetBackup Yönetim Konsolu, yedekleme türü seçin](./media/storsimple-configure-backup-target-using-netbackup/nbimage8.png)

4. İlke türünü ayarlamak için **Standart**' ı seçin ve ardından **İleri**' yi seçin.

   ![NetBackup Yönetim Konsolu, ilke türü seçin](./media/storsimple-configure-backup-target-using-netbackup/nbimage9.png)

5. Ana bilgisayarınızı seçin, **istemci işletim sistemini Algıla** onay kutusunu seçin ve ardından **Ekle**' yi seçin. **İleri**’yi seçin.

   ![NetBackup Yönetim Konsolu, istemcileri yeni bir ilkede listeleme](./media/storsimple-configure-backup-target-using-netbackup/nbimage10.png)

6. Yedeklemek istediğiniz sürücüleri seçin.

   ![NetBackup Yönetim Konsolu, yeni bir ilke için yedekleme seçimleri](./media/storsimple-configure-backup-target-using-netbackup/nbimage11.png)

7. Yedekleme döndürme gereksinimlerinizi karşılayan sıklığı ve bekletme değerlerini seçin.

   ![NetBackup Yönetim Konsolu, yeni bir ilke için yedekleme sıklığı ve döndürme](./media/storsimple-configure-backup-target-using-netbackup/nbimage12.png)

8. **Sonraki** > sonrakison > **bitiş**' i seçin.  Zamanlamayı, ilke oluşturulduktan sonra değiştirebilirsiniz.

9. Yeni oluşturduğunuz ilkeyi genişletmeyi seçin ve ardından **zamanlamalar**' ı seçin.

   ![NetBackup Yönetim Konsolu, yeni bir ilke için zamanlamalar](./media/storsimple-configure-backup-target-using-netbackup/nbimage13.png)

10. **Fark-Inc**' e sağ tıklayın, **Yeni ' ye Kopyala**' yı seçin ve **Tamam**' ı seçin.

    ![NetBackup Yönetim Konsolu, zamanlamayı yeni bir ilkeye Kopyala](./media/storsimple-configure-backup-target-using-netbackup/nbimage14.png)

11. Yeni oluşturulan zamanlamaya sağ tıklayın ve ardından **Değiştir**' i seçin.

12. **Öznitelikler** sekmesinde, **Ilke depolama seçimini geçersiz kıl** onay kutusunu seçin ve ardından Pazartesi artımlı yedeklemelerin bulunduğu hacmi seçin.

    ![NetBackup Yönetim Konsolu, Zamanlamayı Değiştir](./media/storsimple-configure-backup-target-using-netbackup/nbimage15.png)

13. **Başlangıç penceresi** sekmesinde yedeklemelerinizin zaman penceresini seçin.

    ![NetBackup Yönetim Konsolu, başlangıç penceresini Değiştir](./media/storsimple-configure-backup-target-using-netbackup/nbimage16.png)

14. **Tamam**’ı seçin.

15. Her artımlı yedekleme için 10-14 adımlarını yineleyin. Oluşturduğunuz her yedekleme için uygun birimi ve zamanlamayı seçin.

16. **Fark-Inc** zamanlamasını sağ tıklatın ve silin.

17. Tam zamanlamanızı, yedekleme gereksinimlerinizi karşılayacak şekilde değiştirin.

    ![NetBackup Yönetim Konsolu, tam Zamanlamayı Değiştir](./media/storsimple-configure-backup-target-using-netbackup/nbimage17.png)

18. Başlangıç penceresini değiştirin.

    ![NetBackup Yönetim Konsolu, başlangıç penceresini değiştirme](./media/storsimple-configure-backup-target-using-netbackup/nbimage18.png)

19. Son zamanlama şöyle görünür:

    ![NetBackup Yönetim Konsolu, son zamanlama](./media/storsimple-configure-backup-target-using-netbackup/nbimage19.png)

## <a name="set-up-storsimple-as-a-secondary-backup-target"></a>İkincil yedekleme hedefi olarak StorSimple 'ı ayarlama

> [!NOTE]
>Buluta katmanlı bir yedekten veri geri yüklemeleri bulut hızlarından oluşur.

Bu modelde, geçici önbellek olarak kullanılacak bir depolama medyası (StorSimple dışında) olmalıdır. Örneğin, boşluk, giriş/çıkış (g/ç) ve bant genişliğini karşılamak için yedekli bir bağımsız diskler dizisi (RAID) birimi kullanabilirsiniz. RAID 5, 50 ve 10 kullanmanızı öneririz.

Aşağıdaki şekilde, tipik kısa süreli bekletme yerel (sunucu) birimleri ve uzun süreli bekletme Arşivi birimleri gösterilmektedir. Bu senaryoda, tüm yedeklemeler yerel (sunucu) RAID birimi üzerinde çalışır. Bu yedeklemeler düzenli aralıklarla çoğaltılır ve bir arşiv birimine arşivlenir. Kısa vadeli saklama kapasitesini ve performans gereksinimlerinizi işleyebilmesi için yerel (sunucu) RAID birimine göre boyutlandırmamak önemlidir.

### <a name="storsimple-as-a-secondary-backup-target-gfs-example"></a>İkincil yedekleme hedefi GFS olarak StorSimple örneği

![İkincil yedekleme hedefi mantıksal diyagramı olarak StorSimple](./media/storsimple-configure-backup-target-using-netbackup/secondarybackuptargetdiagram.png)

Aşağıdaki tabloda, yerel ve StorSimple disklerinde çalışacak yedeklemelerin nasıl ayarlanacağı gösterilmektedir. Bireysel ve toplam kapasite gereksinimlerini içerir.

### <a name="backup-configuration-and-capacity-requirements"></a>Yedekleme yapılandırması ve kapasite gereksinimleri

| Yedekleme türü ve bekletme | Yapılandırılan depolama alanı | Boyut (TiB) | GFS çarpanı | Toplam kapasite\* (Tib) |
|---|---|---|---|---|
| Hafta 1 (tam ve artımlı) |Yerel disk (kısa vadeli)| 1\. | 1\. | 1\. |
| StorSimple hafta 2-4 |StorSimple diski (uzun vadeli) | 1\. | 4 | 4 |
| Aylık tam |StorSimple diski (uzun vadeli) | 1\. | 12 | 12 |
| Yıllık tam |StorSimple diski (uzun vadeli) | 1\. | 1\. | 1\. |
|GFS birimleri boyut gereksinimi |  |  |  | 18*|

\*Toplam kapasite, StorSimple disklerinin 17 TiB ve yerel RAID birimi 'nin 1 TiB 'leri içerir.


### <a name="gfs-example-schedule-gfs-rotation-weekly-monthly-and-yearly-schedule"></a>GFS örnek zamanlaması: GFS haftalık, aylık ve yıllık zamanlama

| Hafta | Tam | Artımlı gün 1 | Artımlı gün 2 | Artımlı gün 3 | Artımlı gün 4 | Artımlı gün 5 |
|---|---|---|---|---|---|---|
| Hafta 1 | Yerel RAID birimi  | Yerel RAID birimi | Yerel RAID birimi | Yerel RAID birimi | Yerel RAID birimi | Yerel RAID birimi |
| Hafta 2 | StorSimple hafta 2-4 |   |   |   |   |   |
| Hafta 3 | StorSimple hafta 2-4 |   |   |   |   |   |
| Hafta 4 | StorSimple hafta 2-4 |   |   |   |   |   |
| Aylık | StorSimple aylık |   |   |   |   |   |
| Yıllık | StorSimple yıllık  |   |   |   |   |   |


## <a name="assign-storsimple-volumes-to-a-netbackup-archive-and-duplication-job"></a>Bir NetBackup arşivine ve yineleme işine StorSimple birimleri atama

NetBackup, depolama ve medya yönetimi için çok çeşitli seçenekler sağladığından, depolama yaşam döngüsü ilkesi (SLP) gereksinimlerinizi doğru şekilde değerlendirmek için VERITAS veya NetBackup mimariyle ilgili bir sorun yaşamanız önerilir.

İlk disk havuzlarını tanımladıktan sonra, toplam dört ilke için üç ek depolama yaşam döngüsü ilkesi tanımlamanız gerekir:
* Localkıdvolume
* StorSimpleWeek2-4
* StorSimpleMonthlyFulls
* StorSimpleYearlyFulls

### <a name="to-assign-storsimple-volumes-to-a-netbackup-archive-and-duplication-job"></a>Bir NetBackup arşivine ve yineleme işine StorSimple birimleri atamak için

1. NetBackup yönetim konsolunda, **depolama** > **alanı yaşam döngüsü ilkeleri** > **Yeni depolama yaşam döngüsü ilkesi**' ni seçin.

   ![NetBackup Yönetim Konsolu, yeni depolama yaşam döngüsü ilkesi](./media/storsimple-configure-backup-target-using-netbackup/nbimage20.png)

2. Anlık görüntü için bir ad girin ve ardından **Ekle**' yi seçin.

3. **Yeni işlem** iletişim kutusunda, **Özellikler** sekmesinde, **işlem**için **Yedekle**' yi seçin. **Hedef depolama**, **bekletme türü**ve **Bekletme dönemi**için istediğiniz değerleri seçin. **Tamam**’ı seçin.

   ![NetBackup Yönetim Konsolu, yeni Işlem iletişim kutusu](./media/storsimple-configure-backup-target-using-netbackup/nbimage22.png)

   Bu, ilk yedekleme işlemini ve depoyu tanımlar.

4. Önceki işlemi vurgulamak için seçin ve ardından **Ekle**' yi seçin. **Depolama Işlemini Değiştir** iletişim kutusunda, **hedef depolama**, **bekletme türü**ve **Bekletme dönemi**için istediğiniz değerleri seçin.

   ![NetBackup Yönetim Konsolu, depolama Işlemini değiştirme iletişim kutusu](./media/storsimple-configure-backup-target-using-netbackup/nbimage23.png)

5. Önceki işlemi vurgulamak için seçin ve ardından **Ekle**' yi seçin. **Yeni depolama yaşam döngüsü ilkesi** iletişim kutusunda bir yıl boyunca aylık yedeklemeler ekleyin.

   ![NetBackup Yönetim Konsolu, yeni depolama yaşam döngüsü Ilkesi iletişim kutusu](./media/storsimple-configure-backup-target-using-netbackup/nbimage24.png)

6. İhtiyacınız olan kapsamlı SLP bekletme ilkesini oluşturup 4-5 arasındaki adımları yineleyin.

   ![NetBackup Yönetim Konsolu, yeni depolama yaşam döngüsü Ilkesi iletişim kutusuna ilke Ekle](./media/storsimple-configure-backup-target-using-netbackup/nbimage25.png)

7. SLP bekletme ilkenizi tanımlamayı tamamladığınızda, **ilke**bölümünde, bir [NetBackup yedekleme işine StorSimple birimleri atama](#assigning-storsimple-volumes-to-a-netbackup-backup-job)konusunda ayrıntılı adımları izleyerek bir yedekleme ilkesi tanımlayın.

8. **Zamanlamalar**' ın altında, **Zamanlamayı Değiştir** iletişim kutusunda, **tam**öğesine sağ tıklayın ve ardından **Değiştir**' i seçin.

   ![NetBackup Yönetim Konsolu, Zamanlamayı Değiştir iletişim kutusu](./media/storsimple-configure-backup-target-using-netbackup/nbimage26.png)

9. **İlke depolama seçimini geçersiz kıl** onay kutusunu seçin ve ardından 1-6 ADıMLARıNDA oluşturduğunuz SLP bekletme ilkesini seçin.

   ![NetBackup Yönetim Konsolu, ilke depolama seçimini geçersiz kıl](./media/storsimple-configure-backup-target-using-netbackup/nbimage27.png)

10. **Tamam**' ı seçin ve ardından artımlı yedekleme zamanlaması için tekrarlayın.

    ![NetBackup Yönetim Konsolu, artımlı yedeklemeler için Zamanlamayı Değiştir iletişim kutusu](./media/storsimple-configure-backup-target-using-netbackup/nbimage28.png)


| Yedekleme türü tutma | Boyut (TiB) | GFS çarpanı\* | Toplam Kapasite (TiB)  |
|---|---|---|---|
| Haftalık tam |  1\.  |  4 | 4  |
| Günlük artımlı  | 0,5  | 20 (döngü, ay başına hafta sayısına eşittir) | 12 (ek kota için 2) |
| Aylık tam  | 1\. | 12 | 12 |
| Yıllık tam | 1\.  | 10 | 10 |
| GFS gereksinimi  |     |     | 38 |
| Ek kota  | 4  |    | Toplam 42 GFS gereksinimi |

\*GFS çarpanı, yedekleme ilkesi gereksinimlerinizi karşılamak için korumanız ve korumanız gereken kopya sayısıdır.

## <a name="storsimple-cloud-snapshots"></a>StorSimple bulut anlık görüntüleri

StorSimple bulut anlık görüntüleri, StorSimple cihazınızda bulunan verileri korur. Bulut anlık görüntüsü oluşturmak, yerel yedekleme bantlarını site dışı bir tesise aktarma ile eşdeğerdir. Azure coğrafi olarak yedekli depolama kullanırsanız, bir bulut anlık görüntüsü oluşturmak, yedekleme bantlarını birden çok siteye aktarmanın eşdeğeridir. Bir olağanüstü durum sonrasında bir cihazı geri yüklemeniz gerekiyorsa, başka bir StorSimple cihazını çevrimiçi duruma getirebilir ve yük devretme yapabilirsiniz. Yük devretmeden sonra, verilere (bulut hızlarından) en son bulut anlık görüntüsünden erişebilirsiniz.

Aşağıdaki bölümde, yedekleme sonrası işlem sırasında StorSimple bulut anlık görüntülerini başlatmak ve silmek için kısa bir komut dosyasının nasıl oluşturulacağı açıklanmaktadır.

> [!NOTE]
> El ile veya program aracılığıyla oluşturulan anlık görüntüler StorSimple anlık görüntü süre sonu ilkesini takip etmez. Bu anlık görüntülerin el ile veya program aracılığıyla silinmesi gerekir.

### <a name="start-and-delete-cloud-snapshots-by-using-a-script"></a>Betik kullanarak bulut anlık görüntülerini başlatma ve silme

> [!NOTE]
> Bir StorSimple anlık görüntüsünü silmeden önce uyumluluk ve veri saklama repercuslarını dikkatle değerlendirin. Yedekleme sonrası betiği çalıştırma hakkında daha fazla bilgi için bkz. [NetBackup belgeleri](http://www.veritas.com/docs/000094423).

### <a name="backup-lifecycle"></a>Yedekleme yaşam döngüsü

![Yedekleme yaşam döngüsü diyagramı](./media/storsimple-configure-backup-target-using-netbackup/backuplifecycle.png)

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
4. NetBackup 'taki yedekleme işinize betiği ekleyin. Bunu yapmak için, ' ön işleme ve işlem sonrası komutları NetBackup iş seçenekleriniz ' ni düzenleyin.

> [!NOTE]
> StorSimple bulut anlık görüntü yedekleme ilkenizi, günlük yedekleme işinizin sonunda bir işlem sonrası betiği olarak çalıştırmanızı öneririz. RPO ve RTO 'larınızı karşılamanıza yardımcı olmak için yedekleme uygulaması ortamınızı yedekleme ve geri yükleme hakkında daha fazla bilgi için lütfen yedekleme mimarinize başvurun.

## <a name="storsimple-as-a-restore-source"></a>Geri yükleme kaynağı olarak StorSimple

Bir StorSimple cihazından geri yükleme, herhangi bir blok depolama cihazından geri yükleme gibi çalışır. Buluta katmanlı verilerin geri yüklemeleri bulut hızlarında oluşur. Yerel veriler için geri yükleme, cihazın yerel disk hızında oluşur. Geri yükleme gerçekleştirme hakkında daha fazla bilgi için bkz. [NetBackup belgeleri](http://www.veritas.com/docs/000094423). NetBackup geri yükleme en iyi uygulamalarına uymalarını öneririz.

## <a name="storsimple-failover-and-disaster-recovery"></a>StorSimple yük devretme ve olağanüstü durum kurtarma

> [!NOTE]
> Yedekleme hedefi senaryolarında StorSimple Cloud Appliance, geri yükleme hedefi olarak desteklenmez.

Olağanüstü bir durum, çeşitli faktörlerden kaynaklanıyor olabilir. Aşağıdaki tabloda, yaygın olağanüstü durum kurtarma senaryoları listelenmektedir.

| Senaryo | Etkisi | Kurtarma | Notlar |
|---|---|---|---|
| StorSimple cihaz hatası | Yedekleme ve geri yükleme işlemleri kesintiye uğrar. | Başarısız olan cihazı değiştirin ve [StorSimple yük devretme ve olağanüstü durum kurtarma](storsimple-device-failover-disaster-recovery.md)gerçekleştirin. | Cihaz kurtarmasından sonra geri yükleme gerçekleştirmeniz gerekirse, tam veri çalışma kümeleri buluttan yeni cihaza alınır. Tüm işlemler bulut hızlardır. Dizin ve Katalog yeniden tarama işlemi, tüm yedekleme kümelerinin bulut katmanından yerel cihaz katmanına taranmasını ve çekilmesine neden olabilir ve bu da zaman alan bir işlemdir. |
| NetBackup sunucusu hatası | Yedekleme ve geri yükleme işlemleri kesintiye uğrar. | Yedekleme sunucusunu yeniden oluşturun ve veritabanı geri yükleme işlemini gerçekleştirin. | NetBackup sunucusunu olağanüstü durum kurtarma sitesinde yeniden oluşturmanız veya geri yüklemeniz gerekir. Veritabanını en son noktaya geri yükleyin. Geri yüklenen NetBackup veritabanı en son yedekleme işleriniz ile eşitlenmediğinde, dizin oluşturma ve kataloglama gereklidir. Bu dizin ve Katalog yeniden tarama işlemi, tüm yedekleme kümelerinin bulut katmanından yerel cihaz katmanına taranmasını ve çekilmesine neden olabilir. Bu, daha fazla zaman yoğunluğu sağlar. |
| Hem yedekleme sunucusu hem de StorSimple kaybına neden olan site hatası | Yedekleme ve geri yükleme işlemleri kesintiye uğrar. | Önce StorSimple 'ı geri yükleyin ve ardından NetBackup 'ı geri yükleyin. | Önce StorSimple 'ı geri yükleyin ve ardından NetBackup 'ı geri yükleyin. Cihazın kurtarmasından sonra geri yükleme gerçekleştirmeniz gerekirse, tam veri çalışma kümeleri buluttan yeni cihaza alınır. Tüm işlemler bulut hızlardır. |

## <a name="references"></a>Referanslar

Bu makale için aşağıdaki belgelere başvuruldu:

- [StorSimple çok yollu g/ç kurulumu](storsimple-configure-mpio-windows-server.md)
- [Depolama senaryoları: Ölçülü kaynak sağlama](https://msdn.microsoft.com/library/windows/hardware/dn265487.aspx)
- [GPT sürücüleri kullanma](https://msdn.microsoft.com/windows/hardware/gg463524.aspx#EHD)
- [Paylaşılan klasörler için gölge kopyaları ayarlama](https://technet.microsoft.com/library/cc771893.aspx)

## <a name="next-steps"></a>Sonraki adımlar

- [Bir yedekleme kümesinden geri yükleme](storsimple-restore-from-backup-set-u2.md)hakkında daha fazla bilgi edinin.
- [Cihaz yük devretmesini ve olağanüstü durum kurtarmayı](storsimple-device-failover-disaster-recovery.md)gerçekleştirme hakkında daha fazla bilgi edinin.
