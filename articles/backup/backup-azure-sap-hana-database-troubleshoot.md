---
title: SAP HANA veritabanlarının yedekleme hatalarını giderme
description: SAP HANA veritabanlarını yedeklemek için Azure Backup kullandığınızda oluşabilecek yaygın hataların nasıl giderileceği açıklanmaktadır.
ms.topic: troubleshooting
ms.date: 11/7/2019
ms.openlocfilehash: cdf4c26aa32d65ec63ec84d85e454adaaf2ece8d
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/15/2021
ms.locfileid: "107517241"
---
# <a name="troubleshoot-backup-of-sap-hana-databases-on-azure"></a>Azure 'da SAP HANA veritabanlarının yedeklenmesi sorunlarını giderme

Bu makale, Azure sanal makinelerinde SAP HANA veritabanlarının yedeklenmesi için sorun giderme bilgileri sağlar. Şu anda desteklediğimiz SAP HANA yedekleme senaryoları hakkında daha fazla bilgi için bkz. [senaryo desteği](sap-hana-backup-support-matrix.md#scenario-support).

## <a name="prerequisites-and-permissions"></a>Önkoşullar ve Izinler

Yedeklemeleri yapılandırmadan önce [önkoşullara](tutorial-backup-sap-hana-db.md#prerequisites) ve [ön kayıt betiğinin bölümler yaptığını](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does) inceleyin.

## <a name="common-user-errors"></a>Ortak Kullanıcı hataları

### <a name="usererrorhanainternalrolenotpresent"></a>UserErrorHANAInternalRoleNotPresent

| **Hata Iletisi**      | <span style="font-weight:normal">Azure Backup, yedeklemeyi gerçekleştirmek için gerekli rol ayrıcalıklarına sahip değil</span>    |
| ---------------------- | ------------------------------------------------------------ |
| **Olası nedenler**    | Rolün üzerine yazılmış olabilir.                          |
| **Önerilen eylem** | Sorunu çözmek için betiği **bul** bölmesinden çalıştırın veya [buradan](https://aka.ms/scriptforpermsonhana)indirin. Alternatif olarak, ' SAP_INTERNAL_HANA_SUPPORT ' rolünü Iş yükü yedekleme kullanıcısına (AZUREWLBACKUPHANAUSER) ekleyin. |

### <a name="usererrorinopeninghanaodbcconnection"></a>UserErrorInOpeningHanaOdbcConnection

| Hata İletisi      | <span style="font-weight:normal">HANA sistemine bağlanılamadı</span>                        |
| ------------------ | ------------------------------------------------------------ |
| **Olası nedenler**    | SAP HANA örneği çalışmıyor olabilir.<br/>HANA veritabanıyla etkileşimde bulunmak için gereken Azure Backup izinleri ayarlanmamış. |
| **Önerilen eylem** | SAP HANA veritabanının çalışır olup olmadığını denetleyin. Veritabanı çalışır duruma kullanıyorsa, tüm gerekli izinlerin ayarlanmış olup olmadığını denetleyin. İzinlerden herhangi biri eksikse eksik izinleri eklemek için [preregistration betiğini](https://aka.ms/scriptforpermsonhana) çalıştırın. |

### <a name="usererrorhanainstancenameinvalid"></a>Usererrorhanaınstancenamegeçersiz

| Hata İletisi      | <span style="font-weight:normal">Belirtilen SAP HANA örneği geçersiz ya da bulunamıyor</span>  |
| ------------------ | ------------------------------------------------------------ |
| **Olası nedenler**    | Tek bir Azure VM üzerindeki birden çok SAP HANA örneği yedeklenebilir. |
| **Önerilen eylem** | Yedeklemek istediğiniz SAP HANA örneğinde [preregistration betiğini](https://aka.ms/scriptforpermsonhana) çalıştırın. Sorun devam ederse, Microsoft destek 'e başvurun. |

### <a name="usererrorhanaunsupportedoperation"></a>UserErrorHanaUnsupportedOperation

| Hata İletisi      | <span style="font-weight:normal">Belirtilen SAP HANA işlemi desteklenmiyor</span>              |
| ------------------ | ------------------------------------------------------------ |
| **Olası nedenler**    | SAP HANA için Azure Backup, SAP HANA yerel istemcilerde gerçekleştirilen artımlı yedeklemeyi ve eylemleri desteklemez (Studio/kokpit/DBA kokpiti) |
| **Önerilen eylem** | Daha fazla bilgi için [buraya](./sap-hana-backup-support-matrix.md#scenario-support)bakın. |

### <a name="usererrorhanalsnvalidationfailure"></a>UserErrorHANALSNValidationFailure

| Hata İletisi      | <span style="font-weight:normal">Yedekleme günlük zinciri bozuk</span>                                    |
| ------------------ | ------------------------------------------------------------ |
| **Olası nedenler**    | Günlük yedekleme hedefi, backint 'ten dosya sistemine güncelleştirilmiş olabilir veya backınt yürütülebilir dosyası değiştirilmiş olabilir |
| **Önerilen eylem** | Sorunu çözmek için bir tam yedekleme tetikleyin                   |

### <a name="usererrorsdctomdcupgradedetected"></a>Usererrorsdctomdcupgradealgılandı

| Hata İletisi      | <span style="font-weight:normal">SDC 'den MDC 'ye yükseltme algılandı</span>                                   |
| ------------------ | ------------------------------------------------------------ |
| **Olası nedenler**    | SAP HANA örneği SDC 'den MDC 'ye yükseltildi. Güncelleştirme sonrasında yedeklemeler başarısız olur. |
| **Önerilen eylem** | Sorunu çözmek için [SDC Ile MDC yükseltmesine](#sdc-to-mdc-upgrade-with-a-change-in-sid) listelenen adımları izleyin |

### <a name="usererrorinvalidbackintconfiguration"></a>UserErrorInvalidBackintConfiguration

| Hata İletisi      | <span style="font-weight:normal">Geçersiz backınt yapılandırması algılandı</span>                       |
| ------------------ | ------------------------------------------------------------ |
| **Olası nedenler**    | Yedekleme parametreleri Azure Backup için yanlış belirtilmiş |
| **Önerilen eylem** | Aşağıdaki (backınt) parametrelerinin ayarlanmış olup olmadığını denetleyin:<br/>\* [catalog_backup_using_backint: true]<br/>\* [enable_accumulated_catalog_backup: false]<br/>\* [parallel_data_backup_backint_channels: 1]<br/>\* [log_backup_timeout_s: 900)]<br/>\* [backint_response_timeout: 7200]<br/>KONAKTA backınt tabanlı parametreler varsa, bunları kaldırın. Parametreler, ana bilgisayar düzeyinde mevcut değilse ancak bir veritabanı düzeyinde el ile değiştirilmişse, bunları daha önce açıklandığı gibi uygun değerlere dönüştürmeniz gerekir. Ya da, [korumayı durdurun ve Azure Portal yedekleme verilerini koruyun](./sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) ve sonra **yedeklemeyi devam ettirir**' ı seçin. |

### <a name="usererrorincompatiblesrctargetsystemsforrestore"></a>Usererrorıncompatiblesrctargetsystemsforrestore

|Hata İletisi  |Geri yükleme için kaynak ve hedef sistemler uyumsuz  |
|---------|---------|
|Olası nedenler   | Geri yükleme için seçilen kaynak ve hedef sistemler uyumsuz        |
|Önerilen eylem   |   Geri yükleme senaryolarınızın aşağıdaki olası uyumsuz geri yüklemeler listesinde olmadığından emin olun: <br><br>   **Durum 1:** Restore sırasında SYSTEMDB yeniden adlandırılamaz.  <br><br> **Durum 2:** Source-SDC ve Target-MDC: kaynak veritabanı hedef üzerinde SYSTEMDB veya kiracı DB olarak geri yüklenemez. <br><br> **Durum 3:** Source-MDC ve Target-SDC: kaynak veritabanı (SYSTEMDB veya kiracı DB) hedefe geri yüklenemez. <br><br>  Daha fazla bilgi için bkz. [SAP support Başlatma Çubuğu](https://launchpad.support.sap.com)içindeki Not **1642148** . |

## <a name="restore-checks"></a>Geri yükleme denetimleri

### <a name="single-container-database-sdc-restore"></a>Tek kapsayıcı veritabanı (SDC) geri yükleme

HANA için tek bir kapsayıcı veritabanını (SDC) başka bir SDC makinesine geri yüklerken girişlerden yararlanın. Veritabanı adı, köşeli ayraçlar içinde "SDC" eklenmiş şekilde, küçük harfle verilmelidir. HANA örneği büyük harf olarak görüntülenecektir.

"H21" SDC HANA örneğinin yedeklenmekte olduğunu varsayalım. Yedekleme öğeleri sayfasında, yedekleme öğesi adı **"H21 (SDC)"** olarak gösterilir. Bu veritabanını başka bir hedef SDC 'ye geri yüklemeye çalışırsanız, H11 deyin ve aşağıdaki girişlerin sağlanması gerekir.

![Geri yüklenen SDC veritabanı adı](media/backup-azure-sap-hana-database/hana-sdc-restore.png)

Aşağıdaki noktalara dikkat edin:

- Varsayılan olarak, geri yüklenen veritabanı adı yedekleme öğesi adı ile doldurulur. Bu durumda, H21 (SDC).
- H11 olarak Target seçildiğinde geri yüklenen veritabanı adı otomatik olarak değişmez. **H11 (SDC) olarak düzenlenmelidir**. SDC ile ilgili olarak, geri yüklenen veritabanı adı, küçük harflerle birlikte hedef örnek KIMLIĞI ve köşeli ayraçlar içine eklenen ' SDC ' olur.
- SDC 'nin yalnızca tek bir veritabanı olduğundan, var olan veritabanı verilerinin kurtarma noktası verileriyle geçersiz kılınmasına izin vermek için onay kutusunu da seçmeniz gerekir.
- Linux, büyük/küçük harfe duyarlıdır. Bu nedenle, durumu korumak için dikkatli olun.

### <a name="multiple-container-database-mdc-restore"></a>Birden çok kapsayıcı veritabanı (MDC) geri yükleme

HANA için birden çok kapsayıcı veritabanında standart yapılandırma SISTEM DB + 1 veya daha fazla kiracı DBs 'dir. SAP HANA örneğinin tamamının geri yüklenmesi hem SYSTEMDB hem de kiracı veritabanlarını geri yüklemek anlamına gelir. İlk olarak SYSTEMDB 'yi geri yükler ve ardından kiracı DB 'ye devam eder. Sistem DB temelde, seçili hedefteki sistem bilgilerini geçersiz kılmak anlamına gelir. Bu geri yükleme, hedef örnekteki BackInt ile ilgili bilgileri de geçersiz kılar. Bu nedenle, sistem DB bir hedef örneğe geri yüklendikten sonra, önceden kayıt betiğini yeniden çalıştırın. Yalnızca sonraki kiracı DB geri yüklemeleri başarılı olur.

## <a name="back-up-a-replicated-vm"></a>Çoğaltılan bir VM 'yi yedekleme

### <a name="scenario-1"></a>1\. Senaryo

Özgün VM Azure Site Recovery veya Azure VM yedeklemesi kullanılarak çoğaltıldı. Yeni VM, eski VM 'nin benzetimini yapmak için oluşturulmuştur. Diğer bir deyişle, ayarlar tamamen aynıdır. (Bunun nedeni, özgün VM 'nin silindiği ve geri yüklemenin VM yedeklemesinden veya Azure Site Recovery) yapılmıştı.

Bu senaryo iki olası durumu içerebilir. Her iki durumda da çoğaltılan VM 'yi nasıl yedekleyeceğinizi öğrenin:

1. Oluşturulan yeni VM aynı ada sahiptir ve silinen VM ile aynı kaynak grubunda ve abonelikte bulunur.

    - Uzantı VM 'de zaten var, ancak hiçbir hizmetten görülemez
    - Kayıt öncesi betiği çalıştırma
    - Azure Portal aynı makinenin uzantısını yeniden kaydedin (**yedekleme**  ->  **Görünümü ayrıntıları** -> ilgili Azure VM 'yi seçin-> yeniden kaydedin)
    - Zaten var olan yedeklenmiş veritabanlarının (silinen VM 'den) yedeklenme sonrasında başarıyla başlatılması gerekir

2. Oluşturulan yeni VM şunlardan birini içerir:

    - Silinen VM 'den farklı bir ad
    - Silinen VM ile aynı ad, ancak farklı bir kaynak grubunda veya abonelikte (silinen VM ile karşılaştırıldığında)

    Bu durumda, aşağıdaki adımları uygulayın:

    - Uzantı VM 'de zaten var, ancak hiçbir hizmetten görülemez
    - Kayıt öncesi betiği çalıştırma
    - Yeni veritabanlarını bulur ve koruyorduğunuzda, portalda yinelenen etkin veritabanları görmeye başlayabilirsiniz. Bunu önlemek için, eski veritabanları için [verileri koruyun ' i korumayı durdurun](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) . Ardından kalan adımlara devam edin.
    - Yedeklemeyi etkinleştirmek için veritabanlarını bulma
    - Bu veritabanlarında yedeklemeleri etkinleştir
    - Zaten var olan yedeklenmiş veritabanları (silinen VM 'den) kasada depolanmaya devam eder (yedeklemeler ilkeye göre tutulmaktadır)

### <a name="scenario-2"></a>2\. Senaryo

Özgün VM Azure Site Recovery veya Azure VM yedeklemesi kullanılarak çoğaltıldı. Yeni VM, bir şablon olarak kullanılacak içerikten oluşturulmuştur. Bu, yeni bir SID 'ye sahip yeni bir VM 'dir.

Yeni VM 'de yedeklemeleri etkinleştirmek için şu adımları izleyin:

- Uzantı VM üzerinde zaten var, ancak hizmetlerden hiçbirinde görünmüyor
- Kayıt öncesi betiği çalıştırın. Yeni VM 'nin SID 'sine bağlı olarak iki senaryo oluşabilir:
  - Özgün VM ve yeni VM aynı SID 'ye sahip. Ön kayıt betiği başarıyla çalışacaktır.
  - Özgün VM ve yeni VM 'de farklı SID 'Ler vardır. Ön kayıt betiği başarısız olur. Bu senaryoda yardım almak için desteğe başvurun.
- Yedeklemek istediğiniz veritabanlarını bulun
- Bu veritabanlarında yedeklemeleri etkinleştir

## <a name="sdc-version-upgrade-or-mdc-version-upgrade-on-the-same-vm"></a>SDC sürüm yükseltmesi veya aynı VM 'de MDC sürümü yükseltme

İşletim sistemine yükseltmeler, SDC sürümü değişikliği veya bir SID değişikliğine neden olmayan MDC sürümü değişikliği aşağıdaki gibi işlenebilir:

- Yeni işletim sistemi sürümü, SDC veya MDC sürümünün şu anda [Azure Backup tarafından desteklendiğinden](sap-hana-backup-support-matrix.md#scenario-support) emin olun
- Veritabanı için [verileri tutma ile korumayı durdur](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database)
- Yükseltme veya güncelleştirme gerçekleştirme
- Ön kayıt betiğini yeniden çalıştırın. Genellikle, yükseltme işlemi [gerekli rolleri](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does)kaldırabilir. Ön kayıt betiğini çalıştırmak, tüm gerekli rolleri doğrulamaya yardımcı olur.
- Veritabanı için korumayı yeniden deneyin

## <a name="sdc-to-mdc-upgrade-with-no-change-in-sid"></a>SID 'de değişiklik yapmadan SDC 'den MDC 'ye yükseltme

SDC 'den MDC 'ye, SID değişikliğine neden olmayan yükseltmeler şu şekilde işlenebilir:

- Yeni MDC sürümünün şu anda [Azure Backup tarafından desteklendiğinden](sap-hana-backup-support-matrix.md#scenario-support) emin olun
- Eski SDC veritabanı için [verileri koruyun ile korumayı durdur](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database)
- Yükseltmeyi gerçekleştirin. Tamamlandıktan sonra, HANA sistemi artık bir sistem DB ve kiracı DBs ile MDC 'dir
- [Ön kayıt betiğini](https://aka.ms/scriptforpermsonhana) yeniden çalıştır
- Azure Portal aynı makinenin uzantısını yeniden kaydedin (**yedekleme**  ->  **Görünümü ayrıntıları** -> ilgili Azure VM 'yi seçin-> yeniden kaydedin)
- Aynı VM için veritabanlarını **yeniden keşfet** ' i seçin. Bu eylem, adım 3 ' teki yeni DBs 'Leri, SDC değil, SYSTEMDB ve kiracı DB olarak göstermelidir
- Eski SDC veritabanı kasada olmaya devam edecektir ve eski yedeklenen verilerin ilkeye göre bekletilmesi gerekir
- Bu veritabanları için yedeklemeyi yapılandırma

## <a name="sdc-to-mdc-upgrade-with-a-change-in-sid"></a>SID 'de değişiklik ile SDC 'den MDC 'ye yükseltme

SDC 'den MDC 'ye yükseltme, SID değişikliğine neden olacak şekilde aşağıdaki gibi işlenebilir:

- Yeni MDC sürümünün şu anda [Azure Backup tarafından desteklendiğinden](sap-hana-backup-support-matrix.md#scenario-support) emin olun
- Eski SDC veritabanı için **verileri koruyun ile korumayı durdur**
- Yükseltmeyi gerçekleştirin. Tamamlandıktan sonra, HANA sistemi artık bir sistem DB ve kiracı DBs ile MDC 'dir
- [Ön kayıt betiğini](https://aka.ms/scriptforpermsonhana) doğru ayrıntılarla yeniden çalıştırın (yeni SID ve MDC). SID 'deki bir değişiklik nedeniyle, betiği başarıyla çalıştırmaya yönelik sorunlar yaşayabilirsiniz. Sorun yaşıyorsanız Azure Backup desteğe başvurun.
- Azure Portal aynı makinenin uzantısını yeniden kaydedin (**yedekleme**  ->  **Görünümü ayrıntıları** -> ilgili Azure VM 'yi seçin-> yeniden kaydedin)
- Aynı VM için veritabanlarını **yeniden keşfet** ' i seçin. Bu eylem, adım 3 ' teki yeni DBs 'Leri, SDC değil, SYSTEMDB ve kiracı DB olarak göstermelidir
- Eski SDC veritabanı kasada olmaya devam edecektir ve eski yedeklenen verilerin ilkeye göre korunması gerekir
- Bu veritabanları için yedeklemeyi yapılandırma

## <a name="re-registration-failures"></a>Yeniden kayıt sorunları

Yeniden kaydetme işlemini tetiklemeniz için aşağıdaki belirtilerden bir veya daha fazlasını denetleyin:

- Tüm işlemler (yedekleme, geri yükleme ve yapılandırma gibi) VM 'de şu hata kodlarından biriyle başarısız oluyor: **Workloadextensionnoterişilebilen, Usererrorworkloadextensionnotyüklü, Workloadextensionnotsun, WorkloadExtensionDidntDequeueMsg**.
- Yedekleme öğesi için **yedekleme durum** alanı **erişilebilir değil** olarak gösteriyorsa, aynı durum oluşmasına neden olabilecek diğer tüm nedenleri inceleyin:

  - VM 'de yedeklemeyle ilgili işlemler gerçekleştirme izninin bulunmaması
  - VM kapatılıyor, bu nedenle yedeklemeler gerçekleşmiyor
  - Ağ sorunları

Bu belirtiler aşağıdaki nedenlerden biri veya birkaçı olabilir:

- Bir uzantı silinmiş veya portaldan kaldırılmış.
- VM, yerinde disk geri yüklemesi sırasında geri yüklendi.
- VM, genişletilmiş bir süre için kapatıldı, bu nedenle uzantı yapılandırması süresi dolmadı.
- VM silindi ve silinen VM ile aynı kaynak grubunda ve aynı ada sahip başka bir VM oluşturuldu.

Yukarıdaki senaryolarda, VM 'de bir yeniden kaydetme işlemi tetiklemeniz önerilir.

## <a name="next-steps"></a>Sonraki adımlar

- Azure VM 'lerinde SAP HANA veritabanlarının yedeklenmesi hakkında [sık sorulan soruları](./sap-hana-faq-backup-azure-vm.yml) gözden geçirin.
