---
title: Azure Izleyici ile SQL Server ortamınızı iyileştirin | Microsoft Docs
description: Azure Izleyici ile, düzenli aralıklarla ortamlarınızın riskini ve durumunu değerlendirmek için SQL sistem durumu denetimi çözümünü kullanabilirsiniz.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 03/28/2019
ms.openlocfilehash: 7808ead7ec4191bdf17e3ab225aeaa909abd7d08
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72900676"
---
# <a name="optimize-your-sql-environment-with-the-sql-server-health-check-solution-in-azure-monitor"></a>Azure Izleyici 'de SQL Server sistem durumu denetimi çözümü ile SQL ortamınızı iyileştirin

![SQL sistem durumu denetimi simgesi](./media/sql-assessment/sql-assessment-symbol.png)

Düzenli aralıklarla sunucu ortamlarınızın riskini ve sistem durumunu değerlendirmek için SQL sistem durumu denetimi çözümünü kullanabilirsiniz. Bu makale, olası sorunlar için düzeltici eylemler yapabilmeniz için çözümü yüklemenize yardımcı olur.

Bu çözüm, dağıtılan sunucu altyapınıza özgü önerilerin öncelikli bir listesini sağlar. Öneriler, riski hızla anlamanıza ve düzeltici eylem yapmanıza yardımcı olan altı odak alanı genelinde kategorize edilir.

Yapılan öneriler, binlerce müşteri ziyaretinden Microsoft mühendisleri tarafından kazanılan bilgi ve deneyime dayanır. Her öneri, bir sorunun neden bir sorun ve önerilen değişikliklerin nasıl uygulanacağı hakkında rehberlik sağlar.

Kuruluşunuz için en önemli odak alanını seçebilir ve risk ücretsiz ve sağlıklı bir ortamı çalıştırmaya yönelik ilerlemenizi izleyebilirsiniz.

Çözümü ekledikten ve bir değerlendirme tamamlandıktan sonra, ortamınızdaki altyapının **SQL sistem durumu denetimi** panosunda odak alanlarının Özet bilgileri gösterilir. Aşağıdaki bölümlerde, SQL Server altyapınız için önerilen eylemleri görüntüleyip uygulayabileceğiniz **SQL sistem durumu denetimi** panosundaki bilgilerin nasıl kullanılacağı açıklanır.

![SQL sistem durumu denetimi kutucuğunun görüntüsü](./media/sql-assessment/sql-healthcheck-summary-tile.png)

![SQL sistem durumu denetimi panosunun görüntüsü](./media/sql-assessment/sql-healthcheck-dashboard-01.png)

## <a name="prerequisites"></a>Önkoşullar

* SQL durum denetimi çözümü, Microsoft Monitoring Agent (MMA) yüklü olan her bilgisayarda .NET Framework 4 ' ün desteklenen bir sürümünü gerektirir.  MMA Aracısı System Center 2016-Operations Manager ve Operations Manager 2012 R2 ve Azure Izleyici tarafından kullanılır.  
* Çözüm 2012, 2014 ve 2016 SQL Server sürümünü destekler.
* Azure portal Azure Marketi 'nden SQL sistem durumu denetimi çözümünü eklemek için bir Log Analytics çalışma alanı.  Çözümü yüklemek için Azure aboneliğinde bir yönetici veya katkıda bulunan olması gerekir.

  > [!NOTE]
  > Çözümü ekledikten sonra, danışmanlı Assessment. exe dosyası aracıları olan sunuculara eklenir. Yapılandırma verileri okuyup işlenmek üzere Bulutta Azure Izleyici 'ye gönderilir. Alınan verilere mantık uygulanır ve bulut hizmeti verileri kaydeder.
  >
  >

SQL Server sunucularınızda sistem durumu denetimini gerçekleştirmek için, aşağıdaki desteklenen yöntemlerden birini kullanarak bir aracı ve Azure Izleyici bağlantısı gerekir:

1. Sunucu zaten System Center 2016-Operations Manager veya Operations Manager 2012 R2 tarafından izlenmediği takdirde, [Microsoft Monitoring Agent (MMA) ' i (MMA)](../../azure-monitor/platform/agent-windows.md) ' i (
2. System Center 2016-Operations Manager veya Operations Manager 2012 R2 ile izleniyorsa ve yönetim grubu Azure Izleyici ile tümleştirilmediğinde, sunucu, veri toplamak ve hizmete iletmek ve yine de devam etmek için Log Analytics ile birden çok bilgisayarlı olabilir. Operations Manager tarafından izleniyor.  
3. Aksi takdirde, Operations Manager yönetim grubunuz hizmetle tümleşikse, çözümü etkinleştirmek üzere [Aracı tarafından yönetilen bilgisayarlar ekleme](../../azure-monitor/platform/om-agents.md#connecting-operations-manager-to-azure-monitor) altındaki adımları izleyerek, hizmet tarafından veri toplamaya yönelik etki alanı denetleyicilerini eklemeniz gerekir. alanında.  

SQL Server, Operations Manager bir yönetim grubuna rapor veren, verileri toplayan, atanan yönetim sunucusuna ileten ve daha sonra doğrudan bir yönetim sunucusundan Azure Izleyici 'ye gönderilen aracı.  Veriler Operations Manager veritabanlarına yazılmaz.  

SQL Server Operations Manager tarafından izleniyorsa, bir Operations Manager farklı çalıştır hesabı yapılandırmanız gerekir. Daha fazla bilgi için aşağıdaki [Azure izleyici Operations Manager farklı çalıştır hesaplarını](#operations-manager-run-as-accounts-for-log-analytics) inceleyin.

## <a name="sql-health-check-data-collection-details"></a>SQL sistem durumu denetimi veri toplama ayrıntıları
SQL sistem durumu denetimi, etkinleştirdiğiniz aracıyı kullanarak aşağıdaki kaynaklardan veri toplar:

* Windows Yönetim Araçları (WMI)
* Kayıt Defteri
* Performans sayaçları
* SQL Server dinamik yönetim görünümü sonuçları

Veriler SQL Server toplanır ve her yedi günde bir Log Analytics iletilir.

## <a name="operations-manager-run-as-accounts-for-log-analytics"></a>Log Analytics için Operations Manager farklı çalıştır hesapları
Log Analytics, verileri toplamak ve Log Analytics hizmetine göndermek için Operations Manager Aracısı ve yönetim grubunu kullanır. Log Analytics, değer ekleme hizmetleri sağlamak için iş yükleri için yönetim paketlerinden sonra oluşturulur. Her iş yükü, yönetim paketlerini bir etki alanı kullanıcı hesabı gibi farklı bir güvenlik bağlamında çalıştırmak için iş yüküne özgü ayrıcalıklar gerektirir. Operations Manager farklı çalıştır hesabını yapılandırarak kimlik bilgileri sağlamanız gerekir.

SQL sistem durumu denetimi için Operations Manager farklı çalıştır hesabını ayarlamak üzere aşağıdaki bilgileri kullanın.

### <a name="set-the-run-as-account-for-sql-health-check"></a>SQL sistem durumu denetimi için farklı çalıştır hesabı ayarlama
 Zaten SQL Server yönetim paketini kullanıyorsanız, bu farklı çalıştır yapılandırmasını kullanmanız gerekir.

#### <a name="to-configure-the-sql-run-as-account-in-the-operations-console"></a>SQL farklı çalıştır hesabını Işletim konsolunda yapılandırmak için
> [!NOTE]
> Varsayılan olarak, yönetim paketindeki iş akışları yerel sistem hesabının güvenlik bağlamında çalışır. Doğrudan Operations Manager bir yönetim grubuna raporlama yerine hizmete doğrudan bağlı Microsoft Monitoring Agent kullanıyorsanız, aşağıdaki 1-5 adımları atlayın ve T-SQL veya PowerShell örneğini çalıştırın ve bu şekilde NT AUTHORITY\SYSTEM Kullanıcı adı.
>
>

1. Operations Manager, Işletim konsolunu açın ve ardından **Yönetim**' e tıklayın.
2. **Farklı Çalıştır Yapılandırması**altında, **profiller**' e tıklayın ve **Farklı Çalıştır profili SQL değerlendirmesi**açın.
3. **Farklı Çalıştır hesapları** sayfasında **Ekle**' ye tıklayın.
4. SQL Server için gereken kimlik bilgilerini içeren bir Windows farklı çalıştır hesabı seçin veya bir tane oluşturmak için **Yeni** ' ye tıklayın.

   > [!NOTE]
   > Farklı Çalıştır hesabı türü Windows olmalıdır. Farklı Çalıştır hesabı, SQL Server örnekleri barındıran tüm Windows sunucularında yerel Yöneticiler grubunun da parçası olmalıdır.
   >
   >
5. **Kaydet** düğmesine tıklayın.
6. Farklı çalıştır hesabının sistem durumu denetimini gerçekleştirmesi için gereken en düşük izinleri vermek üzere her bir SQL Server örneğinde aşağıdaki T-SQL örneğini değiştirin ve yürütün. Ancak, bir farklı çalıştır hesabı zaten SQL Server örneklerine sysadmin sunucu rolünün bir parçasıysa bunu yapmanız gerekmez.

```
    ---
    -- Replace <UserName> with the actual user name being used as Run As Account.
    USE master

    -- Create login for the user, comment this line if login is already created.
    CREATE LOGIN [<UserName>] FROM WINDOWS

    -- Grant permissions to user.
    GRANT VIEW SERVER STATE TO [<UserName>]
    GRANT VIEW ANY DEFINITION TO [<UserName>]
    GRANT VIEW ANY DATABASE TO [<UserName>]

    -- Add database user for all the databases on SQL Server Instance, this is required for connecting to individual databases.
    -- NOTE: This command must be run anytime new databases are added to SQL Server instances.
    EXEC sp_msforeachdb N'USE [?]; CREATE USER [<UserName>] FOR LOGIN [<UserName>];'

```

#### <a name="to-configure-the-sql-run-as-account-using-windows-powershell"></a>Windows PowerShell 'i kullanarak SQL farklı çalıştır hesabını yapılandırmak için
Bir PowerShell penceresi açın ve bilgilerinizi kullanarak güncelleştirdikten sonra aşağıdaki betiği çalıştırın:

```
    import-module OperationsManager
    New-SCOMManagementGroupConnection "<your management group name>"

    $profile = Get-SCOMRunAsProfile -DisplayName "SQL Assessment Run As Profile"
    $account = Get-SCOMrunAsAccount | Where-Object {$_.Name -eq "<your run as account name>"}
    Set-SCOMRunAsProfile -Action "Add" -Profile $Profile -Account $Account
```

## <a name="understanding-how-recommendations-are-prioritized"></a>Önerilerin nasıl önceliklendirildiğini anlama
Yapılan her öneriye, önerinin göreli önemini tanımlayan bir ağırlık değeri verilir. Yalnızca en önemli on öneri gösterilmektedir.

### <a name="how-weights-are-calculated"></a>Ağırlıklar nasıl hesaplanır
Ağırlıklı değerler üç ana etkene göre toplam değerlerdir:

* Bir sorunun tanımlanabileceği *olasılık* sorunlara neden olur. Daha yüksek bir olasılık, öneriye ilişkin daha büyük bir genel puanı elde eden bir olasılıktır.
* Soruna neden olursa sorunun kuruluşunuza *etkisi* . Daha yüksek bir etkisi, önerinin daha büyük bir genel puanı ile aynıdır.
* Öneriyi uygulamak için gereken *çaba* . Daha yüksek bir çaba, önerinin daha küçük bir genel puanı ile aynıdır.

Her öneri ağırlığı, her bir odak alanı için kullanılabilen toplam puanların yüzdesi olarak ifade edilir. Örneğin, güvenlik ve uyumluluk odağıyla ilgili bir önerinin %5 puanı varsa, bu öneriyi uygulamak genel güvenlik ve uyumluluk puanınızı %5 oranında artırır.

### <a name="focus-areas"></a>Odak alanı
**Güvenlik ve uyumluluk** -bu odak alanı, olası güvenlik tehditleri ve ihlal, kurumsal ilkeler ve teknik, yasal ve yasal uyumluluk gereksinimlerine yönelik öneriler gösterir.

**Kullanılabilirlik ve Iş sürekliliği** -bu odak alanı hizmet kullanılabilirliği, altyapınızın esnekliği ve iş koruma önerilerini gösterir.

**Performans ve ölçeklenebilirlik** -bu odak alanı, kuruluşunuzun BT altyapısının BÜYÜMESI, BT ortamınızın geçerli performans gereksinimlerini karşıladığından emin olmak ve değişen altyapı ihtiyaçlarına yanıt verebilmeleri için öneriler gösterir.

**Yükseltme, geçiş ve dağıtım** -bu odak alanı, mevcut altyapınıza SQL Server yükseltmenize, geçirmenize ve dağıtmanıza yardımcı olacak öneriler gösterir.

**İşlemler ve izleme** -bu odak alanı, BT işlemlerinizi kolaylaştırmaya, önleyici bakım uygulamanıza ve performansı en üst düzeye çıkarmaya yardımcı olmaya yönelik öneriler gösterir.

**Değişiklik ve yapılandırma yönetimi** -bu odak alanı, günlük işlemleri korumaya yardımcı olma önerilerini gösterir, değişikliklerin altyapınızı olumsuz şekilde etkilememesini, değişiklik denetimi yordamlarını oluşturmayı ve sistemi izleyip denetlemesini sağlayın konfigürasyonları.

### <a name="should-you-aim-to-score-100-in-every-focus-area"></a>Her odak alanında %100 puan elde etmeniz gerekir mi?
Olmayabilir. Öneriler, binlerce müşteri ziyaretinde Microsoft mühendisleri tarafından kazanılan bilgi ve deneyimlere dayalıdır. Ancak, iki sunucu altyapısı aynı değildir ve belirli öneriler sizinle daha fazla veya daha az olabilir. Örneğin, sanal makineleriniz Internet 'e açık değilse bazı güvenlik önerileri daha az ilgili olabilir. Bazı kullanılabilirlik önerileri düşük öncelikli geçici veri toplama ve raporlama sağlayan hizmetlerle daha az ilgili olabilir. Yetişkin iş açısından önemli olan sorunlar, başlangıç için daha az önemli olabilir. Önceliklerinizin hangi odak alanlarından olduğunu belirlemek ve sonra puanlarınızın zaman içinde nasıl değişdiklerini görmek isteyebilirsiniz.

Her öneri, neden önemli olduğuna ilişkin bir kılavuz içerir. BT hizmetlerinizin doğası ve kuruluşunuzun iş ihtiyaçları göz önüne alındığında, öneriyi uygulamanız için uygun olup olmadığını değerlendirmek için bu kılavuzu kullanmanız gerekir.

## <a name="use-health-check-focus-area-recommendations"></a>Sistem durumu denetimi odak alanı önerilerini kullan
Bir değerlendirme çözümünü Azure Izleyici 'de kullanabilmeniz için önce çözümün yüklü olması gerekir.  Yüklendikten sonra, Azure portal Azure Izleyici için **genel bakış** sayfasında SQL sistem durumu denetimi kutucuğunu kullanarak önerilerin özetini görüntüleyebilirsiniz.

Altyapınız için özetlenen uyumluluk değerlendirmelerini görüntüleyin ve sonra öneriler ' e gidin.

### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>Bir odak alanı önerilerini görüntülemek ve düzeltici eylemi gerçekleştirmek için
1. [https://portal.azure.com](https://portal.azure.com) adresinden Azure portalında oturum açın.
2. Azure portalının sol alt köşesinde bulunan **Diğer hizmetler**'e tıklayın. Kaynak listesinde **İzleyici** yazın. Yazmaya başladığınızda liste, girişinize göre filtrelenir. **İzleyici**'yi seçin.
3. Menünün **Öngörüler** bölümünde **diğer**' i seçin.  
4. **Genel bakış** sayfasında **SQL sistem durumu denetim** kutucuğuna tıklayın.
5. **Sistem durumu denetimi** sayfasında, odak alanı Dikey penceresinde bulunan Özet bilgilerini gözden geçirin ve ardından bu odak alanı önerilerini görüntülemek için bir tane tıklatın.
6. Odak alanı sayfalarında, ortamınız için yapılan öncelikli önerileri görüntüleyebilirsiniz. Önerinin neden yapıldığına ilişkin ayrıntıları görüntülemek için **etkilenen nesneler** altında bir öneriye tıklayın.<br><br> SQL sistem durumu denetimi önerilerinin ![görüntüsü](./media/sql-assessment/sql-healthcheck-dashboard-02.png)<br>
7. **Önerilen eylemlerde**önerilen düzeltici eylemler gerçekleştirebilirsiniz. Öğe giderildiği zaman, daha sonra değerlendirmeler önerilen eylemlerin alındığını ve uyumluluk puanınız artar. Düzeltilen öğeler **geçirilmiş nesneler**olarak görünür.

## <a name="ignore-recommendations"></a>Önerileri yoksay
Yok saymak istediğiniz önerileriniz varsa, Azure Izleyici 'nin değerlendirme sonuçlarınızda görünmesini engellemek için kullanacağı bir metin dosyası oluşturabilirsiniz.

### <a name="to-identify-recommendations-that-you-will-ignore"></a>Yok sayılacak önerileri belirlemek için
1. Azure Izleyici menüsünde **Günlükler**' e tıklayın.
2. Ortamınızdaki bilgisayarlarda başarısız olan önerileri listelemek için aşağıdaki sorguyu kullanın.

    ```
    SQLAssessmentRecommendation | where RecommendationResult == "Failed" | sort by Computer asc | project Computer, RecommendationId, Recommendation
    ```
    Günlük sorgusunun gösterildiği ekran görüntüsü aşağıda verilmiştir:<br><br> ![başarısız öneriler](./media/sql-assessment/sql-assess-failed-recommendations.png)<br>

3. Yoksaymak istediğiniz önerileri seçin. Sonraki yordamda RecommendationId için değerleri kullanacaksınız.

### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>Bir ıgnorereyorum geçişleri. txt metin dosyası oluşturmak ve kullanmak için
1. Ignorereyorumgeçişleri. txt adlı bir dosya oluşturun.
2. Azure Izleyici 'nin ayrı bir satırda yok saymasını istediğiniz her öneri için her bir RecommendationId yapıştırın veya yazın, sonra dosyayı kaydedip kapatın.
3. Azure Izleyici 'nin önerileri yoksaymasını istediğiniz her bilgisayarda dosyayı aşağıdaki klasöre yerleştirin.
   * Microsoft Monitoring Agent olan bilgisayarlarda (doğrudan veya Operations Manager üzerinden bağlı)- *systemdrive*: \Program Files\Microsoft Monitoring Tors t\agent
   * Operations Manager Management Server- *systemdrive*: \Program Files\Microsoft System Center 2012 R2\Operations manager\server
   * Operations Manager 2016 yönetim sunucusu- *systemdrive*: \Program Files\Microsoft System Center 2016 \ Operations manager\server

### <a name="to-verify-that-recommendations-are-ignored"></a>Önerilerin yoksayıldığını doğrulamak için
1. Zamanlanan bir sonraki değerlendirme çalıştıktan sonra, varsayılan olarak 7 günde bir, belirtilen öneriler yoksayıldı olarak işaretlenir ve değerlendirme panosu 'nda görünmez.
2. Tüm yoksayılan önerileri listelemek için aşağıdaki günlük arama sorgularını kullanabilirsiniz.

    ```
    SQLAssessmentRecommendation | where RecommendationResult == "Ignored" | sort by Computer asc | project Computer, RecommendationId, Recommendation
    ```
3. Daha sonra yoksayılan önerilere bakmak istediğinize karar verirseniz, tüm ıgnorereyorum. txt dosyalarını kaldırın veya RecommendationIDs kaldırabilir.

## <a name="sql-health-check-solution-faq"></a>SQL sistem durumu denetimi çözümü SSS
*Bir sistem durumu denetimi ne sıklıkta çalıştırılır?*

* Onay her yedi günde bir çalıştırılır.

*Çekin ne sıklıkta çalışacağını yapılandırmak için bir yol var mı?*

* Şimdilik hayır.

*SQL sistem durumu denetimi çözümünü ekledikten sonra başka bir sunucu bulunursa, kontrol edilecek mi?*

* Evet, bu, her yedi günde bir üzerinde denetlenir.

*Sunucu kullanımdan çıkarılmışsa, sistem durumu denetiminden kaldırılır?*

* Bir sunucu 3 hafta boyunca veri göndermezse, kaldırılır.

*Veri toplamayı yapan işlemin adı nedir?*

* Danışmanorassessment. exe

*Verilerin toplanması ne kadar sürer?*

* Sunucu üzerindeki gerçek veri koleksiyonu yaklaşık 1 saat sürer. Çok sayıda SQL örneğine veya veritabanına sahip sunucularda daha uzun sürebilir.

*Ne tür veriler toplanır?*

* Aşağıdaki veri türleri toplanır:
  * 'Ya
  * Kayıt Defteri
  * Performans sayaçları
  * SQL dinamik yönetim görünümleri (DMV).

*Verilerin toplanması sırasında yapılandırmak için bir yol var mı?*

* Şimdilik hayır.

*Neden farklı çalıştır hesabı yapılandırmam gerekir?*

* SQL Server için, az sayıda SQL sorgusu çalıştırılır. Bunların çalıştırılabilmesi için SQL 'de sunucu durumunu görüntüle izinlerine sahip bir farklı çalıştır hesabı kullanılmalıdır.  Ayrıca, WMI sorgulamak için yerel yönetici kimlik bilgileri gereklidir.

*Neden yalnızca ilk 10 öneriyi görüntülersin?*

* Size ayrıntılı bir görev listesi vermek yerine öncelikle öncelikli önerileri gidermeye odaklanmanız önerilir. Bunları adresledikten sonra ek öneriler kullanılabilir hale gelir. Ayrıntılı listeyi görmeyi tercih ediyorsanız, Log Analytics günlük aramasını kullanarak tüm önerileri görüntüleyebilirsiniz.

*Öneriyi yoksaymak için bir yol var mı?*

* Evet, bkz. Yukarıdaki [önerileri yoksay](#ignore-recommendations) bölümü.

## <a name="next-steps"></a>Sonraki adımlar
* Ayrıntılı SQL sistem durumu denetimi verilerini ve önerilerini çözümlemeyi öğrenmek için [sorguları günlüğe kaydedin](../log-query/log-query-overview.md) .
