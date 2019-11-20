---
title: System Center Operations Manager ortamınızı Azure Log Analytics iyileştirin | Microsoft Docs
description: Düzenli aralıklarla ortamlarınızın riskini ve durumunu değerlendirmek için System Center Operations Manager Sistem Durumu Denetimi çözümünü kullanabilirsiniz.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 06/25/2018
ms.openlocfilehash: 33aa246e21b54aebaa902304ff92d4b74bfaac4b
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72898777"
---
# <a name="optimize-your-environment-with-the-system-center-operations-manager-health-check-preview-solution"></a>System Center Operations Manager Sistem Durumu Denetimi (Önizleme) çözümü ile ortamınızı iyileştirin

![System Center Operations Manager Sistem Durumu Denetimi simgesi](./media/scom-assessment/scom-assessment-symbol.png)

Düzenli bir aralıkta System Center Operations Manager yönetim grubunuzun riskini ve sistem durumunu değerlendirmek için System Center Operations Manager Sistem Durumu Denetimi çözümünü kullanabilirsiniz. Bu makale, olası sorunlar için düzeltici eylemler yapabilmeniz için çözümü yüklemenize, yapılandırmanıza ve kullanmanıza yardımcı olur.

Bu çözüm, dağıtılan sunucu altyapınıza özgü önerilerin öncelikli bir listesini sağlar. Öneriler, riski hızla anlamanıza ve düzeltici eylem yapmanıza yardımcı olan dört odak alanına göre kategorize edilir.

Yapılan öneriler, binlerce müşteri ziyaretinden Microsoft mühendisleri tarafından kazanılan bilgi ve deneyime dayanır. Her öneri, bir sorunun neden bir sorun ve önerilen değişikliklerin nasıl uygulanacağı hakkında rehberlik sağlar.

Kuruluşunuz için en önemli odak alanını seçebilir ve risk ücretsiz ve sağlıklı bir ortamı çalıştırmaya yönelik ilerlemenizi izleyebilirsiniz.

Çözümü ekledikten ve bir değerlendirme gerçekleştirildikten sonra, altyapı için **System Center Operations Manager sistem durumu denetimi** panosunda odak alanlarının Özet bilgileri gösterilir. Aşağıdaki bölümlerde, Operations Manager ortamınız için önerilen eylemleri görüntüleyip uygulayabileceğiniz **System Center Operations Manager sistem durumu denetimi** panosundaki bilgilerin nasıl kullanılacağı açıklanır.

![System Center Operations Manager çözüm kutucuğu](./media/scom-assessment/log-analytics-scom-healthcheck-tile.png)

![System Center Operations Manager Sistem Durumu Denetimi panosu](./media/scom-assessment/log-analytics-scom-healthcheck-dashboard-01.png)

## <a name="installing-and-configuring-the-solution"></a>Çözümü yükleme ve yapılandırma

Çözüm, Microsoft System Center 2012 Operations Manager hizmet paketi 1, Microsoft System Center 2012 R2 Operations Manager, Microsoft System Center 2016 Operations Manager, Microsoft System Center 2016 Operations Manager ve Microsoft System ile birlikte kullanılabilir Merkez Operations Manager 1807

Çözümü yüklemek ve yapılandırmak için aşağıdaki bilgileri kullanın.

- Log Analytics 'de sistem durumu denetimi çözümünü kullanabilmeniz için çözümün yüklü olması gerekir. Çözümü [Azure Marketi](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.SCOMAssessmentOMS?tab=Overview)'nden yüklersiniz.

- Çalışma alanına çözüm eklendikten sonra, panodaki **System Center Operations Manager sistem durumu denetimi** kutucuğunda ek bir yapılandırma gerekli iletisi görüntülenir. Kutucuğa tıklayın ve sayfada bahsedilen yapılandırma adımlarını izleyin

  ![Pano kutucuğunu System Center Operations Manager](./media/scom-assessment/scom-configrequired-tile.png)

> [!NOTE]
> System Center Operations Manager yapılandırması, Log Analytics çözümün yapılandırma sayfasında belirtilen adımları izleyerek bir komut dosyası kullanılarak yapılabilir.

 Operations Manager Işlemler konsolu aracılığıyla değerlendirmeyi yapılandırmak için aşağıdaki adımları sırayla gerçekleştirin:
1. [Farklı Çalıştır hesabını System Center Operations Manager Sistem Durumu Denetimi ayarla](#operations-manager-run-as-accounts-for-log-analytics)  
2. System Center Operations Manager Sistem Durumu Denetimi kuralını yapılandırma

## <a name="system-center-operations-manager-health-check-data-collection-details"></a>Veri toplama ayrıntılarını System Center Operations Manager Sistem Durumu Denetimi

System Center Operations Manager Sistem Durumu Denetimi çözümü, verileri aşağıdaki kaynaklardan toplar:

* Kayıt Defteri
* Windows Yönetim Araçları (WMI)
* Olay günlüğü
* Dosya verileri
* Doğrudan PowerShell ve SQL sorguları kullanarak, belirttiğiniz bir yönetim sunucusundan Operations Manager.  

Veriler yönetim sunucusunda toplanır ve her yedi günde bir Log Analytics iletilir.  

## <a name="operations-manager-run-as-accounts-for-log-analytics"></a>Log Analytics için Operations Manager farklı çalıştır hesapları

Log Analytics, değer ekleme hizmetleri sağlamak için iş yükleri için yönetim paketlerinde yapılar. Her iş yükü, yönetim paketlerini bir etki alanı kullanıcı hesabı gibi farklı bir güvenlik bağlamında çalıştırmak için iş yüküne özgü ayrıcalıklar gerektirir. Ayrıcalıklı kimlik bilgileriyle Operations Manager farklı çalıştır hesabı yapılandırın. Daha fazla bilgi için, Operations Manager belgelerinde [Farklı Çalıştır hesabı oluşturma](https://technet.microsoft.com/library/hh321655(v=sc.12).aspx) konusuna bakın.

System Center Operations Manager Sistem Durumu Denetimi için Operations Manager farklı çalıştır hesabını ayarlamak üzere aşağıdaki bilgileri kullanın.

### <a name="set-the-run-as-account"></a>Farklı Çalıştır hesabını ayarla

Devam etmeden önce farklı çalıştır hesabının aşağıdaki gereksinimleri karşılaması gerekir:

* Tüm Operations Manager rol yönetim sunucusunu destekleyen tüm sunucularda yerel Yöneticiler grubunun üyesi olan bir etki alanı kullanıcı hesabı, işletimsel, veri ambarı ve ACS veritabanı, raporlama, Web Konsolu ve ağ geçidi sunucusu SQL Server.
* Değerlendirilmekte olan yönetim grubu için Operation Manager yönetici rolü
* Hesapta SQL sysadmin hakları yoksa, Operations Manager veritabanlarının birini veya tümünü barındıran her bir SQL Server örneğindeki hesaba ayrıntılı izinler vermek için [betiği](#sql-script-to-grant-granular-permissions-to-the-run-as-account) yürütün.

1. Operations Manager konsolunda, **Yönetim** Gezinti düğmesini seçin.
2. **Farklı Çalıştır Yapılandırması**altında **hesaplar**' a tıklayın.
3. **Farklı Çalıştır hesabı oluşturma** Sihirbazı 'nda **giriş** sayfasında **İleri**' ye tıklayın.
4. **Genel Özellikler** sayfasında, **Farklı Çalıştır hesap türü:** listesinde **Windows** ' u seçin.
5. **Görünen ad** metin kutusuna bir görünen ad yazın ve isteğe bağlı olarak **Açıklama** kutusuna bir açıklama yazın ve ardından **İleri**' ye tıklayın.
6. **Dağıtım güvenliği** sayfasında **daha güvenli**' i seçin.
7. **Oluştur**’a tıklayın.  

Farklı Çalıştır hesabı oluşturuldığına göre, yönetim grubundaki yönetim sunucularını hedeflemek ve iş akışlarının kimlik bilgileri kullanılarak çalışması için önceden tanımlanmış bir Farklı Çalıştır profiliyle ilişkilendirilmesi gerekir.  

1. **Farklı Çalıştır Yapılandırması**, **hesaplar**, sonuçlar bölmesinde, daha önce oluşturduğunuz hesaba çift tıklayın.
2. **Dağıtım** sekmesinde, **Seçili bilgisayarlar** kutusu için **Ekle** ' ye tıklayın ve hesabı dağıtılacak yönetim sunucusunu ekleyin.  Değişikliklerinizi kaydetmek için iki kez **Tamam** ' a tıklayın.
3. **Farklı Çalıştır Yapılandırması**altında **profiller**' e tıklayın.
4. *SCOM değerlendirme profilini*arayın.
5. Profil adı şöyle olmalıdır: *Microsoft System Center Operations Manager sistem durumu denetimi farklı çalıştır profili*.
6. Sağ tıklayın ve özelliklerini güncelleştirin ve daha önce oluşturduğunuz en son oluşturulan farklı çalıştır hesabını ekleyin.

### <a name="sql-script-to-grant-granular-permissions-to-the-run-as-account"></a>Farklı Çalıştır hesabına ayrıntılı izinler vermek için SQL betiği

İşlem, veri ambarı ve ACS veritabanını barındıran Operations Manager tarafından kullanılan SQL Server örneğindeki Farklı Çalıştır hesabına gerekli izinleri vermek için aşağıdaki SQL betiğini yürütün.

```
-- Replace <UserName> with the actual user name being used as Run As Account.
USE master

-- Create login for the user, comment this line if login is already created.
CREATE LOGIN [UserName] FROM WINDOWS


--GRANT permissions to user.
GRANT VIEW SERVER STATE TO [UserName]
GRANT VIEW ANY DEFINITION TO [UserName]
GRANT VIEW ANY DATABASE TO [UserName]

-- Add database user for all the databases on SQL Server Instance, this is required for connecting to individual databases.
-- NOTE: This command must be run anytime new databases are added to SQL Server instances.
EXEC sp_msforeachdb N'USE [?]; CREATE USER [UserName] FOR LOGIN [UserName];'

Use msdb
GRANT SELECT To [UserName]
Go

--Give SELECT permission on all Operations Manager related Databases

--Replace the Operations Manager database name with the one in your environment
Use [OperationsManager];
GRANT SELECT To [UserName]
GO

--Replace the Operations Manager DatawareHouse database name with the one in your environment
Use [OperationsManagerDW];
GRANT SELECT To [UserName]
GO

--Replace the Operations Manager Audit Collection database name with the one in your environment
Use [OperationsManagerAC];
GRANT SELECT To [UserName]
GO

--Give db_owner on [OperationsManager] DB
--Replace the Operations Manager database name with the one in your environment
USE [OperationsManager]
GO
ALTER ROLE [db_owner] ADD MEMBER [UserName]

```

### <a name="configure-the-health-check-rule"></a>Sistem durumu denetimi kuralını yapılandırma

System Center Operations Manager Sistem Durumu Denetimi çözümünün yönetim paketi, *Microsoft System Center Operations Manager Run Health denetim kuralı*adlı bir kural içerir. Bu kural, sistem durumu denetimini çalıştırmaktan sorumludur. Kuralı etkinleştirmek ve sıklığı yapılandırmak için aşağıdaki yordamları kullanın.

Varsayılan olarak, Microsoft System Center Operations Manager çalışan sistem durumu denetim kuralı devre dışıdır. Sistem durumu denetimini çalıştırmak için, kuralı bir yönetim sunucusu üzerinde etkinleştirmeniz gerekir. Aşağıdaki adımları kullanın.

#### <a name="enable-the-rule-for-a-specific-management-server"></a>Belirli bir yönetim sunucusu için kuralı etkinleştir

1. Operations Manager Işletim konsolunun **yazma** çalışma alanında, **kurallar** bölmesindeki *Microsoft System Center Operations Manager sistem durumu denetimi kuralını Çalıştır* kuralını arayın.
2. Arama sonuçlarında, metin türünü içeren bir tane seçin *: yönetim sunucusu*.
3. Kurala sağ tıklayın ve sonra şu **sınıfın belirli bir nesnesi için** > **geçersiz kılmalar** ' a tıklayın: yönetim sunucusu.
4.  Kullanılabilir yönetim sunucuları listesinde, kuralın çalıştırılacağı yönetim sunucusunu seçin.  Bu, farklı çalıştır hesabını ile ilişkilendirmek için daha önce yapılandırdığınız yönetim sunucusu olmalıdır.
5.  **Etkin** parametre değeri için geçersiz kılma değerini **true** olarak değiştirdiğinizden emin olun.<br><br> ![override parametresi](./media/scom-assessment/rule.png)

    Bu pencerede hala, sonraki yordamı kullanarak çalıştırma sıklığını yapılandırın.

#### <a name="configure-the-run-frequency"></a>Çalıştırma sıklığını yapılandırma

Değerlendirme, varsayılan olarak her 10.080 dakikada bir (veya yedi gün) çalışacak şekilde yapılandırılmıştır. Değeri en az 1440 dakikalık bir değere (veya bir gün) geçersiz kılabilirsiniz. Değer, ardışık değerlendirme çalıştırmaları arasında gereken en kısa zaman boşluğunu temsil eder. Aralığı geçersiz kılmak için aşağıdaki adımları kullanın.

1. Operations Manager konsolunun **yazma** çalışma alanında, **kurallar** bölümündeki *Microsoft System Center Operations Manager sistem durumu denetimi kuralını Çalıştır* kuralını arayın.
2. Arama sonuçlarında, metin türünü içeren bir tane seçin *: yönetim sunucusu*.
3. Kurala sağ tıklayın ve sonra **sınıfın tüm nesneleri için** > **kuralı geçersiz kıl** ' a tıklayın: yönetim sunucusu.
4. **Aralık** parametre değerini istediğiniz Aralık değeri olarak değiştirin. Aşağıdaki örnekte, değer 1440 dakika (bir gün) olarak ayarlanır.<br><br> ![Interval parametresi](./media/scom-assessment/interval.png)<br>  

    Değer 1440 dakikadan daha kısa bir süre içinde ayarlandıysa, kural bir gün aralığında çalışır. Bu örnekte, kural Aralık değerini yoksayar ve bir günde bir sıklıkta çalışır.


## <a name="understanding-how-recommendations-are-prioritized"></a>Önerilerin nasıl önceliklendirildiğini anlama

Yapılan her öneriye, önerinin göreli önemini tanımlayan bir ağırlık değeri verilir. Yalnızca en önemli 10 öneri gösterilmektedir.

### <a name="how-weights-are-calculated"></a>Ağırlıklar nasıl hesaplanır

Ağırlıklı değerler üç ana etkene göre toplam değerlerdir:

- Bir sorunun tanımlanabileceği *olasılık* sorunlara neden olur. Daha yüksek bir olasılık, öneriye ilişkin daha büyük bir genel puanı elde eden bir olasılıktır.
- Soruna neden olursa sorunun kuruluşunuza *etkisi* . Daha yüksek bir etkisi, önerinin daha büyük bir genel puanı ile aynıdır.
- Öneriyi uygulamak için gereken *çaba* . Daha yüksek bir çaba, önerinin daha küçük bir genel puanı ile aynıdır.

Her öneri ağırlığı, her bir odak alanı için kullanılabilen toplam puanların yüzdesi olarak ifade edilir. Örneğin, kullanılabilirlik ve Iş sürekliliği odağının bir önerisi %5 puanına sahipse, bu öneriyi uygulamak genel kullanım ve Iş sürekliliği puanınızı %5 oranında artırır.

### <a name="focus-areas"></a>Odak alanı

**Kullanılabilirlik ve Iş sürekliliği** -bu odak alanı hizmet kullanılabilirliği, altyapınızın esnekliği ve iş koruma önerilerini gösterir.

**Performans ve ölçeklenebilirlik** -bu odak alanı, kuruluşunuzun BT altyapısının BÜYÜMESI, BT ortamınızın geçerli performans gereksinimlerini karşıladığından emin olmak ve değişen altyapı ihtiyaçlarına yanıt verebilmeleri için öneriler gösterir.

**Yükseltme, geçiş ve dağıtım** -bu odak alanı, mevcut altyapınıza SQL Server yükseltmenize, geçirmenize ve dağıtmanıza yardımcı olan önerileri gösterir.

**İşlemler ve izleme** -bu odak alanı, BT işlemlerinizi kolaylaştırmaya, önleyici bakım uygulamanıza ve performansı en üst düzeye çıkarmaya yardımcı olmaya yönelik öneriler gösterir.

### <a name="should-you-aim-to-score-100-in-every-focus-area"></a>Her odak alanında %100 puan elde etmeniz gerekir mi?

Olmayabilir. Öneriler, binlerce müşteri ziyaretinde Microsoft mühendisleri tarafından kazanılan bilgi ve deneyimlere dayalıdır. Ancak, iki sunucu altyapısı aynı değildir ve belirli öneriler sizinle daha fazla veya daha az olabilir. Örneğin, sanal makineleriniz Internet 'e açık değilse bazı güvenlik önerileri daha az ilgili olabilir. Bazı kullanılabilirlik önerileri düşük öncelikli geçici veri toplama ve raporlama sağlayan hizmetlerle daha az ilgili olabilir. Yetişkin iş açısından önemli olan sorunlar, başlangıç için daha az önemli olabilir. Önceliklerinizin hangi odak alanlarından olduğunu belirlemek ve sonra puanlarınızın zaman içinde nasıl değişdiklerini görmek isteyebilirsiniz.

Her öneri, neden önemli olduğuna ilişkin bir kılavuz içerir. BT hizmetlerinizin doğası ve kuruluşunuzun iş ihtiyaçlarını göz önünde bulundurarak, öneriyi uygulamak için uygun olup olmadığını değerlendirmek için bu kılavuzu kullanın.

## <a name="use-health-check-focus-area-recommendations"></a>Sistem durumu denetimi odak alanı önerilerini kullan

Log Analytics bir sistem durumu denetimi çözümünü kullanabilmeniz için çözümün yüklü olması gerekir. Çözümleri yükleme hakkında daha fazla bilgi için bkz. [yönetim çözümü yükleme](../../azure-monitor/insights/solutions.md). Yüklendikten sonra, Azure portal çalışma alanınızın **genel bakış** sayfasında System Center Operations Manager sistem durumu denetimi kutucuğunu kullanarak önerilerin özetini görüntüleyebilirsiniz.

Altyapınız için özetlenen uyumluluk değerlendirmelerini görüntüleyin ve sonra öneriler ' e gidin.

### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>Bir odak alanı önerilerini görüntülemek ve düzeltici eylemi gerçekleştirmek için
1. [https://portal.azure.com](https://portal.azure.com) adresinden Azure portalında oturum açın.
2. Azure portalının sol alt köşesinde bulunan **Diğer hizmetler**'e tıklayın. Kaynak listesinde **Log Analytics** yazın. Yazmaya başladığınızda liste, girişinize göre filtrelenir. **Log Analytics**’i seçin.
3. Log Analytics abonelikler bölmesinde bir çalışma alanı seçin ve ardından **çalışma alanı Özet** menü öğesine tıklayın.  
4. **Genel bakış** sayfasında **System Center Operations Manager sistem durumu denetimi** kutucuğuna tıklayın.
5. **System Center Operations Manager sistem durumu denetimi** sayfasında, odak alanı Dikey penceresinde bulunan Özet bilgilerini gözden geçirin ve ardından bu odak alanı önerilerini görüntülemek için bir tane tıklatın.
6. Odak alanı sayfalarında, ortamınız için yapılan öncelikli önerileri görüntüleyebilirsiniz. Önerinin neden yapıldığına ilişkin ayrıntıları görüntülemek için **etkilenen nesneler** altında bir öneriye tıklayın.<br><br> odak alanını ![](./media/scom-assessment/log-analytics-scom-healthcheck-dashboard-02.png)<br>
7. **Önerilen eylemlerde**önerilen düzeltici eylemler gerçekleştirebilirsiniz. Öğe giderildiği zaman, daha sonra değerlendirmeler önerilen eylemlerin alındığını ve uyumluluk puanınız artar. Düzeltilen öğeler **geçirilmiş nesneler**olarak görünür.

## <a name="ignore-recommendations"></a>Önerileri yoksay

Yok saymak istediğiniz önerileriniz varsa, önerilerin değerlendirme sonuçlarında görünmesini engellemek için Log Analytics tarafından kullanılan bir metin dosyası oluşturabilirsiniz.

### <a name="to-identify-recommendations-that-you-want-to-ignore"></a>Yoksaymak istediğiniz önerileri belirlemek için
1. Seçtiğiniz çalışma alanınızın Log Analytics çalışma alanı sayfasındaki Azure portal **günlük araması** menü öğesine tıklayın.
2. Ortamınızdaki bilgisayarlarda başarısız olan önerileri listelemek için aşağıdaki sorguyu kullanın.

    ```
    Type=SCOMAssessmentRecommendationRecommendationResult=Failed | select Computer, RecommendationId, Recommendation | sort Computer
    ```

    >[!NOTE]
    > Çalışma alanınız [yeni Log Analytics sorgu diline](../../azure-monitor/log-query/log-query-overview.md)yükseltilmişse yukarıdaki sorgu aşağıdaki gibi değişir.
    >
    > `SCOMAssessmentRecommendationRecommendation | where RecommendationResult == "Failed" | sort by Computer asc | project Computer, RecommendationId, Recommendation`

    Günlük arama sorgusunu gösteren bir ekran görüntüsü aşağıda verilmiştir:<br><br> ![günlük araması](./media/scom-assessment/scom-log-search.png)<br>

3. Yoksaymak istediğiniz önerileri seçin. Sonraki yordamda RecommendationId için değerleri kullanacaksınız.

### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>Bir ıgnorereyorum geçişleri. txt metin dosyası oluşturmak ve kullanmak için

1. Ignorereyorumgeçişleri. txt adlı bir dosya oluşturun.
2. Log Analytics ayrı bir satırda yoksaymasını istediğiniz her öneri için her bir RecommendationId yapıştırın veya yazın, sonra dosyayı kaydedip kapatın.
3. Log Analytics önerileri yoksaymasını istediğiniz her bilgisayarda dosyayı aşağıdaki klasöre yerleştirin.
4. Operations Manager Management Server- *systemdrive*: \Program Files\Microsoft System Center 2012 R2\Operations Manager\Server.

### <a name="to-verify-that-recommendations-are-ignored"></a>Önerilerin yoksayıldığını doğrulamak için

1. Zamanlanan bir sonraki değerlendirme çalıştıktan sonra, varsayılan olarak yedi günde bir, belirtilen öneriler yoksayıldı olarak işaretlenir ve sistem durumu denetimi panosunda görünmez.
2. Tüm yoksayılan önerileri listelemek için aşağıdaki günlük arama sorgularını kullanabilirsiniz.

    ```
    Type=SCOMAssessmentRecommendationRecommendationResult=Ignored | select  Computer, RecommendationId, Recommendation | sort  Computer
    ```

    >[!NOTE]
    > Çalışma alanınız [yeni Log Analytics sorgu diline](../../azure-monitor/log-query/log-query-overview.md)yükseltilmişse yukarıdaki sorgu aşağıdaki gibi değişir.
    >
    > `SCOMAssessmentRecommendationRecommendation | where RecommendationResult == "Ignore" | sort by Computer asc | project Computer, RecommendationId, Recommendation`

3. Daha sonra yoksayılan önerilere bakmak istediğinize karar verirseniz, tüm ıgnorereyorum. txt dosyalarını kaldırın veya RecommendationIDs kaldırabilir.

## <a name="system-center-operations-manager-health-check-solution-faq"></a>System Center Operations Manager Sistem Durumu Denetimi çözümü SSS

*Log Analytics çalışma alanım 'a durum denetimi çözümünü ekledim. Ancak önerileri görmüyorum. Neden olmasın?* Çözümü ekledikten sonra, aşağıdaki adımları kullanın Log Analytics panosundaki önerileri görüntüleyin.  

- [Farklı Çalıştır hesabını System Center Operations Manager Sistem Durumu Denetimi ayarla](#operations-manager-run-as-accounts-for-log-analytics)  
- [System Center Operations Manager Sistem Durumu Denetimi kuralını yapılandırma](#configure-the-health-check-rule)


*Çekin ne sıklıkta çalışacağını yapılandırmak için bir yol var mı?* Evet. Bkz. [çalışma sıklığını yapılandırma](#configure-the-run-frequency).

*System Center Operations Manager Sistem Durumu Denetimi çözümünü ekledikten sonra başka bir sunucu bulunursa, kontrol edilecek mi?* Evet, bulduktan sonra varsayılan olarak yedi günde bir işaretlenir.

*Veri toplamayı yapan işlemin adı nedir?* Danışmanorassessment. exe

*Danışmanorassessment. exe işlemi nerede çalıştırılır?* Danışmanorassessment. exe, sistem durumu denetimi kuralının etkinleştirildiği yönetim sunucusunun HealthService işlemi altında çalışır. Bu işlemi kullanarak, tüm ortamınızı bulma işlemi uzak veri koleksiyonu aracılığıyla gerçekleştirilir.

*Veri toplama için ne kadar sürer?* Sunucudaki veri toplama yaklaşık bir saat sürer. Birçok Operations Manager örneği veya veritabanına sahip ortamlarda daha uzun sürebilir.

*Değerlendirmenin aralığını 1440 dakikadan daha kısa bir süre olarak ayarlarsanız ne yapmalıyım?* Değerlendirme, günde en fazla bir kez çalışacak şekilde önceden yapılandırılmıştır. Aralık değerini 1440 dakikadan daha kısa bir değere geçersiz kılarsınız değerlendirme, Aralık değeri olarak 1440 dakika kullanır.

*Önkoşul hatalarından haberdar olup olmadığını nasıl anlarsınız?* Sistem durumu denetimi çalıştırılmışsa ve sonuçları görmüyorsanız, denetim önkoşullarının bazıları başarısız olabilir. Başarısız önkoşulları görmek için `Operation Solution=SCOMAssessment` ve günlük aramasında `SCOMAssessmentRecommendation FocusArea=Prerequisites` sorguları çalıştırabilirsiniz.

*Önkoşul hatalarında `Failed to connect to the SQL Instance (….).` bir ileti var. Sorun nedir?* Veri toplayan işlem, yönetim sunucusundaki HealthService işlemi altında çalışan Danışmanorassessment. exe. Sistem durumu denetiminin bir parçası olarak, işlem Operations Manager veritabanının bulunduğu SQL Server bağlanmaya çalışır. Bu hata, güvenlik duvarı kuralları SQL Server örneğine bağlantıyı engellediğinizde ortaya çıkabilir.

*Ne tür veriler toplanır?* Aşağıdaki veri türleri toplanır:-WMI veri-kayıt defteri verileri-EventLog veri-Windows PowerShell, SQL sorguları ve dosya bilgileri toplayıcısı aracılığıyla verileri Operations Manager.

*Neden farklı çalıştır hesabı yapılandırmam gerekir?* Operations Manager, çeşitli SQL sorguları çalıştırılır. Bunların çalışması için gerekli izinlere sahip bir farklı çalıştır hesabı kullanmanız gerekir. Ayrıca, WMI sorgulamak için yerel yönetici kimlik bilgileri gereklidir.

*Neden yalnızca ilk 10 öneriyi görüntülersin?* Size ayrıntılı, yoğun bir görev listesi vermek yerine öncelikle öncelikli önerileri adreslemeye odaklanmanız önerilir. Bunları adresledikten sonra ek öneriler kullanılabilir hale gelir. Ayrıntılı listeyi görmeyi tercih ediyorsanız, günlük aramasını kullanarak tüm önerileri görüntüleyebilirsiniz.

*Öneriyi yoksaymak için bir yol var mı?* Evet, bkz. [yoksayma önerileri](#ignore-recommendations).


## <a name="next-steps"></a>Sonraki adımlar

- Ayrıntılı System Center Operations Manager Sistem Durumu Denetimi verilerini ve önerilerini çözümlemeyi öğrenmek için [günlüklere bakın](../../azure-monitor/log-query/log-query-overview.md) .
