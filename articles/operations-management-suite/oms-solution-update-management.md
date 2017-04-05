---
title: "OMS’de Güncelleştirme Yönetimi çözümü | Microsoft Belgeleri"
description: "Bu makale, Windows ve Linux bilgisayarlarınızın güncelleştirmelerini yönetmek için bu çözümün nasıl kullanılacağına yönelik bilgiler verir."
services: operations-management-suite
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: e33ce6f9-d9b0-4a03-b94e-8ddedcc595d2
ms.service: operations-management-suite
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/06/2017
ms.author: magoedte
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 18aa17f6af7fe492f3875e1af7cb06b613f171af
ms.lasthandoff: 04/03/2017


---
# <a name="update-management-solution-in-oms"></a>OMS’de Güncelleştirme Yönetimi çözümü
OMS’de Güncelleştirme Yönetimi çözümü, Windows ve Linux bilgisayarlarınıza yönelik güncelleştirmeleri yönetmenize olanak tanır.  Tüm aracı bilgisayarlardaki kullanılabilir güncelleştirmelerin durumunu hızlıca değerlendirebilir ve sunucular için gerekli güncelleştirmeleri yükleme işlemini başlatabilirsiniz. 

## <a name="prerequisites"></a>Ön koşullar
* Çözüm yalnızca Windows Server 2008 ve üzeri sürümlere göre güncelleştirme değerlendirmeleri; Windows Server 2012 ve üzeri sürümlere göre güncelleştirme dağıtımları gerçekleştirmeyi destekler.  Sunucu Çekirdeği ve Nano Sunucu yükleme seçenekleri desteklenmez.
* Windows istemci işletim sistemleri desteklenmez.  
* Windows aracıları Windows Server Update Services (WSUS) sunucusuyla iletişim kuracak veya Microsoft Update’e erişecek şekilde yapılandırılmış olmalıdır.  
  
  > [!NOTE]
  > Windows aracısı System Center Configuration Manager tarafından eşzamanlı olarak yönetilemez.  
  > 
  > 
* Linux aracılarının bir güncelleştirme havuzuna erişimi olmalıdır.  Linux için OMS Aracısı [GitHub](https://github.com/microsoft/oms-agent-for-linux)’dan indirilebilir. 

## <a name="configuration"></a>Yapılandırma
Güncelleştirme Yönetimi çözümünü OMS çalışma alanınıza eklemek ve Linux aracılarını eklemek için aşağıdaki adımları uygulayın. Windows aracıları ek bir yapılandırma olmadan otomatik olarak eklenir.

> [!NOTE]
> Bu çözümü etkinleştirmeniz durumunda çözümde yer alan runbook'ların desteklenmesi amacıyla OMS çalışma alanınıza bağlı tüm Windows bilgisayarları otomatik olarak bir Karma Runbook Çalışanı olarak yapılandırılır.  Ancak varsa Otomasyon hesabınızda önceden tanımlı Karma Çalışanı gruplarıyla kaydedilmemiştir.  Çözüm ve Karma Runbook Çalışanı grup üyeliği için aynı hesabı kullandığınız sürece Otomasyon gruplarını desteklemek için Otomasyon hesabınızdaki bir Karma Runbook Çalışanı grubuna eklenebilir.  Bu işlev Karma Runbook Çalışanının 7.2.12024.0 sürümüne eklenmiştir.   

1. Çözüm Galerisi’ndeki [OMS çözümü ekleme](../log-analytics/log-analytics-add-solutions.md) bölümünde anlatılan şekilde Güncelleştirme Yönetimi çözümünü OMS çalışma alanına ekleyin.  
2. OMS portalında **Ayarlar** ve ardından **Bağlı Kaynaklar**’ı seçin.  **Çalışma Alanı Kimliği**’ni ve **Birincil Anahtar** ya da **İkincil Anahtar**’ı not edin.
3. Her Linux bilgisayar için aşağıdaki adımları gerçekleştirin.
   
   a.    Aşağıdaki komutları çalıştırarak Linux için OMS Aracısı’nın en son sürümünü yükleyin.  <Workspace ID> öğesini Çalışma Alanı Kimliği ile, <Key> öğesini ise Birincil Anahtar veya İkincil Anahtar ile değiştirin.
   
        cd ~
        wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID>  -s <PrimaryKey> -d opinsights.azure.com 

   b. Aracıyı kaldırmak için, [Linux için OMS Aracısı’nı kaldırma](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#uninstalling-the-oms-agent-for-linux) bölümünde açıklanan işlemi kullanın.  

## <a name="management-packs"></a>Yönetim paketleri
System Center Operations Manager yönetim grubunuz OMS çalışma alanınıza bağlıysa, bu çözümü eklediğinizde Operation Manager’a aşağıdaki yönetim paketleri yüklenir. Bu yönetim paketleri için bir yapılandırma veya bakım gerekmez. 

* Microsoft System Center Advisor Update Assessment Intelligence Pack (Microsoft.IntelligencePacks.UpdateAssessment)
* Microsoft.IntelligencePack.UpdateAssessment.Configuration (Microsoft.IntelligencePack.UpdateAssessment.Configuration)
* MP Dağıtımını güncelleştirme

Çözüm yönetim paketlerini güncelleştirme hakkında daha fazla bilgi için bkz. [Operations Manager'ı Log Analytics’e Bağlama](../log-analytics/log-analytics-om-agents.md).

## <a name="data-collection"></a>Veri toplama
### <a name="supported-agents"></a>Desteklenen aracılar
Aşağıdaki tabloda bu çözüm tarafından desteklenen bağlı kaynaklar açıklanmaktadır.

| Bağlı Kaynak | Destekleniyor | Açıklama |
| --- | --- | --- |
| Windows aracıları |Evet |Çözüm, Windows aracılarından sistem güncelleştirme bilgilerini toplar ve gerekli güncelleştirmelerin yüklemesini başlatır. |
| Linux aracıları |Evet |Çözüm, Linux aracılarından sistem güncelleştirme bilgilerini toplar. |
| Operations Manager yönetim grubu |Evet |Çözüm, bağlı bir yönetim grubundaki aracılardan sistem güncelleştirmeleri hakkında bilgi toplar.<br>Operations Manager aracısının doğrudan Log Analytics’e bağlanması gerekmez. Veriler yönetim grubundan OMS deposuna iletilir. |
| Azure depolama hesabı |Hayır |Azure Storage, sistem güncelleştirmeleri hakkında bilgi içermez. |

### <a name="collection-frequency"></a>Toplama sıklığı
Yönetilen her Windows bilgisayarı için günde iki kez tarama gerçekleştirilir.  Bir güncelleştirme yüklendiğinde bilgileri 15 dakika içinde güncelleştirilir.  

Yönetilen her Linux bilgisayarı için 3 saatte bir tarama gerçekleştirilir.  

## <a name="using-the-solution"></a>Çözümü kullanma
Güncelleştirme Yönetimi çözümünü OMS çalışma alanınıza eklediğinizde OMS panonuza **Güncelleştirme Yönetimi** kutucuğu eklenir. Bu kutucukta, ortamınızda o anda sistem güncelleştirmeleri gerektiren bilgisayar sayısının sayı ve grafik halinde bir açıklaması gösterilir.<br><br>
![Güncelleştirme Yönetimi Özet Kutucuğu](media/oms-solution-update-management/update-management-summary-tile.png)  

## <a name="viewing-update-assessments"></a>Güncelleştirme Değerlendirmelerini görüntüleme
**Güncelleştirme Yönetimi** kutucuğuna tıklayarak **Güncelleştirme Yönetimi** panosunu açın. Pano aşağıdaki tabloda gösterilen sütunları içerir. Her sütun, sütunun belirtilen kapsam ve zaman aralığına yönelik kriterleriyle eşleşen en fazla on öğe listeler. Sütunun altındaki **Tümünü gör**’e tıklayarak veya sütun başlığına tıklayarak tüm kayıtları döndüren bir günlük araması gerçekleştirebilirsiniz.

| Sütun | Açıklama |
| --- | --- |
| **Güncelleştirmelerin Eksik Olduğu Bilgisayarlar** | |
| Kritik Güncelleştirmeler veya Güvenlik Güncelleştirmeleri |Güncelleştirmeleri eksik olan ilk on bilgisayarı, eksik güncelleştirme sayısına göre sıralar. Bir bilgisayarın tüm güncelleştirme kayıtlarını döndüren günlük araması gerçekleştirmek için bilgisayar adına tıklayın. |
| 30 günden eski Kritik Güncelleştirmeler veya Güvenlik Güncelleştirmeleri |Kritik güncelleştirmeleri veya güvenlik güncelleştirmeleri eksik bilgisayar sayısını, güncelleştirmenin yayımlandığı tarihten sonra geçen süreye göre gruplandırır. Tüm eksik ve kritik güncelleştirmeleri döndüren bir günlük araması gerçekleştirmek için girişlerden birine tıklayın. |
| **Eksik Gerekli Güncelleştirmeler** | |
| Kritik Güncelleştirmeler veya Güvenlik Güncelleştirmeleri |Bilgisayarlarda eksik olan güncelleştirme sınıflarını, ilgili kategorideki güncelleştirmelerin eksik olduğu bilgisayar sayısına göre sıralar. Bir sınıflandırmanın tüm güncelleştirme kayıtlarını döndüren günlük araması gerçekleştirmek için sınıflandırmaya tıklayın. |
| **Güncelleştirme Dağıtımları** | |
| Güncelleştirme Dağıtımları |O anda zamanlanmış güncelleştirme dağıtımlarının sayısı ve zamanlanan sonraki çalışmaya kalan süre.  O anda çalışan zamanlamaları ve tamamlanmış güncelleştirmeleri görüntülemek ya da yeni bir dağıtım zamanlamak için kutucuğa tıklayın. |

<br>  
![Güncelleştirme Yönetimi Özet Panosu](./media/oms-solution-update-management/update-management-deployment-dashboard.png)<br>  
<br>
![Güncelleştirme Yönetimi Panosu Bilgisayar Görünümü](./media/oms-solution-update-management/update-management-assessment-computer-view.png)<br>  
<br>
![Güncelleştirme Yönetimi Panosu Paket Görünümü](./media/oms-solution-update-management/update-management-assessment-package-view.png)<br>  

## <a name="installing-updates"></a>Güncelleştirmeleri yükleme
Güncelleştirmeler ortamınızdaki tüm Windows bilgisayarlar için değerlendirildikten sonra bir *Güncelleştirme Dağıtımı* oluşturarak gerekli güncelleştirmeleri yükleyebilirsiniz.  Güncelleştirme Dağıtımı, bir veya daha fazla Windows bilgisayar için gerekli güncelleştirmelerin zamanlanmış yüklemesidir.  Dağıtımın tarih ve saatine ek olarak, dahil edilmesi gereken bilgisayarı veya bilgisayar grubunu belirtmeniz gerekir.  

Güncelleştirmeler Azure Automation’daki runbook'lar tarafından yüklenir.  Bu runbook’ları görüntüleyemezsiniz ve bunlar için herhangi bir yapılandırma gerekmez.  Bir Güncelleştirme Dağıtımı oluşturulduğunda, dahil edilen bilgisayarlar için belirtilen zamanda ana güncelleştirme runbook’u başlatan bir zamanlama oluşturur.  Bu ana runbook, gerekli güncelleştirmelerin yüklemesini gerçekleştiren her Windows aracısında bir alt runbook başlatır.  

Azure Market’ten edinilebilen isteğe bağlı Red Hat Enterprise Linux (RHEL) görüntülerinden oluşturulan sanal makineler için Azure’da dağıtılmış [Red Hat Update Infrastructure (RHUI)](../virtual-machines/linux/update-infrastructure-redhat.md) erişimi için kaydedilir.  Diğer tüm Linux dağıtımları, ilgili dağıtımın desteklediği yöntemler izlenerek çevrimiçi dosya deposundan güncelleştirilmelidir.  

### <a name="viewing-update-deployments"></a>Güncelleştirme dağıtımlarını görüntüleme
Var olan Güncelleştirme Dağıtımlarının listesini görüntülemek için **Güncelleştirme Dağıtımı** kutucuğuna tıklayın.  Bunlar duruma göre gruplandırılır: **Zamanlanmış**, **Çalışıyor** ve **Tamamlandı**.<br><br> ![Güncelleştirme Dağıtımları Zamanlama Sayfası](./media/oms-solution-update-management/update-updatedeployment-schedule-page.png)<br>  

Her Güncelleştirme Dağıtımı için gösterilen özellikler aşağıdaki tabloda açıklanmıştır.

| Özellik | Açıklama |
| --- | --- |
| Adı |Güncelleştirme Dağıtımının adı. |
| Zamanlama |Zamanlama türü.  *OneTime* şu anda mümkün olan tek değerdir. |
| Başlangıç Zamanı |Güncelleştirme Dağıtımının başlatılmak üzere zamanlandığı tarih ve saat. |
| Süre |Güncelleştirme Dağıtımının çalışmasına izin verilen dakika sayısı.  Bu süre içinde tüm güncelleştirmeler yüklenmezse, kalan güncelleştirmeler sonraki Güncelleştirme Dağıtımına kadar beklemelidir. |
| Sunucular |Güncelleştirme Dağıtımından etkilenen bilgisayar sayısı. |
| Durum |Güncelleştirme Dağıtımının geçerli durumu.<br><br>Olası değerler şunlardır:<br>-    Başlatılmadı<br>- Çalışıyor<br>- Tamamlandı |

Aşağıdaki tabloda gösterilen sütunları içeren ayrıntı ekranını görüntülemek için Güncelleştirme Dağıtımı’na tıklayın.  Güncelleştirme Dağıtımı henüz başlamadıysa bu sütunlar doldurulmaz.<br>

| Sütun | Açıklama |
| --- | --- |
| **Bilgisayar Sonuçları** | |
| Başarıyla Tamamlandı |Güncelleştirme Dağıtımındaki bilgisayar sayısını duruma göre listeler.  Güncelleştirme Dağıtımında bir duruma sahip tüm güncelleştirme kayıtlarını döndüren günlük araması gerçekleştirmek için ilgili duruma tıklayın. |
| Bilgisayar Yükleme Durumu |Güncelleştirme Dağıtımında rol alan bilgisayarları ve başarıyla yüklenen güncelleştirme yüzdesini listeler. Tüm eksik ve kritik güncelleştirmeleri döndüren bir günlük araması gerçekleştirmek için girişlerden birine tıklayın. |
| **Güncelleştirme Örneği Sonuçları** | |
| Örnek Yükleme Durumu |Bilgisayarlarda eksik olan güncelleştirme sınıflarını, ilgili kategorideki güncelleştirmelerin eksik olduğu bilgisayar sayısına göre sıralar. Bir bilgisayarın tüm güncelleştirme kayıtlarını döndüren günlük araması gerçekleştirmek için bilgisayara tıklayın. |

<br><br> ![Güncelleştirme Dağıtımı Sonuçlarına genel bakış](./media/oms-solution-update-management/update-la-updaterunresults-page.png)

### <a name="creating-an-update-deployment"></a>Güncelleştirme Dağıtımı oluşturma
Yeni bir Güncelleştirme Dağıtımı oluşturmak için ekranın üst kısmındaki **Ekle** düğmesine tıklayarak **Yeni Güncelleştirme Dağıtımı** sayfasını açın.  Aşağıdaki tabloda gösterilen özelliklerin değerlerini belirtmeniz gerekir.

| Özellik | Açıklama |
| --- | --- |
| Adı |Güncelleştirme dağıtımını tanımlamak için benzersiz bir ad. |
| Saat Dilimi |Başlangıç saati için kullanılacak saat dilimi. |
| Başlangıç Zamanı |Güncelleştirme dağıtımının başlatılacağı tarih ve saat. |
| Süre |Güncelleştirme Dağıtımının çalışmasına izin verilen dakika sayısı.  Bu süre içinde tüm güncelleştirmeler yüklenmezse, kalan güncelleştirmeler sonraki Güncelleştirme Dağıtımına kadar beklemelidir. |
| Bilgisayarlar |Güncelleştirme Dağıtımına dahil edilecek bilgisayarların veya bilgisayar gruplarının adları.  Açılır listeden bir veya daha fazla giriş seçin. |

<br><br> ![Yeni Güncelleştirme Dağıtımı Sayfası](./media/oms-solution-update-management/update-newupdaterun-page.png)

### <a name="time-range"></a>Zaman aralığı
Varsayılan olarak, Güncelleştirme Yönetimi çözümünde analiz edilen verilerin kapsamı son 1 gün içinde oluşturulan tüm bağlı yönetim gruplarından belirlenir. 

Verileri zaman aralığını değiştirmek için panonun üst kısmındaki **Veri temeli**’ni seçin. Son 7 gün, 1 gün veya 6 saat içinde oluşturulan veya güncelleştirilen kayıtları seçebilirsiniz. Ya da **Özel**’i seçip özel bir tarih aralığı belirtebilirsiniz.<br><br> ![Özel Zaman Aralığı Seçeneği](./media/oms-solution-update-management/update-la-time-range-scope-databasedon.png)  

## <a name="log-analytics-records"></a>Log Analytics kayıtları
Güncelleştirme Yönetimi çözümü, OMS deposunda iki tür kayıt oluşturur.

### <a name="update-records"></a>Güncelleştirme kayıtları
Her bilgisayara yüklenen veya gerekli olan her bir güncelleştirme için **Güncelleştirme** türünde bir kayıt oluşturulur. Güncelleştirme kayıtları aşağıdaki tabloda gösterilen özelliklere sahiptir.

| Özellik | Açıklama |
| --- | --- |
| Tür |*Güncelleştirme* |
| SourceSystem |Güncelleştirmenin yüklenmesini onaylayan kaynak.<br>Olası değerler şunlardır:<br>- Microsoft Update<br>-    Windows Update<br>-    SCCM<br>- Linux Sunucuları (Paket Yöneticilerinden getirilir) |
| Onaylandı |Güncelleştirme yüklemesinin onaylanıp onaylanmadığını belirtir.<br> Düzeltme eki uygulama işlemi OMS tarafından yönetilmediğinden, Linux sunucuları için bu seçenek şu anda isteğe bağlıdır. |
| Windows Sınıflandırması |Güncelleştirme sınıflandırması.<br>Olası değerler şunlardır:<br>-    Uygulamalar<br>- Kritik Güncelleştirmeler<br>- Tanım Güncelleştirmeleri<br>- Özellik Paketleri<br>- Güvenlik Güncelleştirmeleri<br>- Hizmet Paketleri<br>- Güncelleştirme Paketleri<br>- Güncelleştirmeler |
| Linux Sınıflandırması |Güncelleştirme sınıflandırması.<br>Olası değerler şunlardır:<br>- Kritik Güncelleştirmeler<br>- Güvenlik Güncelleştirmeleri<br>- Diğer Güncelleştirmeler |
| Bilgisayar |Bilgisayarın adı. |
| InstallTimeAvailable |Yükleme zamanına, aynı güncelleştirmenin yüklü olduğu diğer aracılardan ulaşılıp ulaşılamadığını belirtir. |
| InstallTimePredictionSeconds |Aynı güncelleştirmeyi yükleyen diğer aracılara göre saniye cinsinden tahmini yükleme süresi. |
| KBID |Güncelleştirmeyi açıklayan KB makalesi kimliği. |
| ManagementGroupName |SCOM aracıları için yönetim grubunun adı.  Diğer aracılar için bu değer AOI-<workspace ID> şeklindedir. |
| MSRCBulletinID |Güncelleştirmeyi açıklayan Microsoft güvenlik bülteni kimliği. |
| MSRCSeverity |Microsoft güvenlik bülteninin önem derecesi.<br>Olası değerler şunlardır:<br>- Kritik<br>- Önemli<br>- Orta |
| İsteğe bağlı |Güncelleştirmenin isteğe bağlı olup olmadığını belirtir. |
| Ürün |Güncelleştirmenin ait olduğu ürünün adı.  Makaleyi bir tarayıcıda açmak için **Görüntüle**’ye tıklayın. |
| PackageSeverity |Bu güncelleştirmede giderilen güvenlik açığının Linux distro satıcıları tarafından bildirilen önem derecesi. |
| PublishDate |Güncelleştirmenin yüklendiği tarih ve saat. |
| RebootBehavior |Güncelleştirmenin yeniden başlatmaya zorlayıp zorlamayacağını belirtir.<br>Olası değerler şunlardır:<br>- canrequestreboot<br>- neverreboots |
| RevisionNumber |Güncelleştirmenin düzeltme sayısı. |
| SourceComputerId |Bilgisayarı benzersiz olarak tanımlayan GUID. |
| TimeGenerated |Kaydın son güncelleştirildiği tarih ve saat. |
| Başlık |Güncelleştirme başlığı. |
| UpdateID |Güncelleştirmeyi benzersiz olarak tanımlayan GUID. |
| UpdateState |Güncelleştirmenin bu bilgisayarda yüklü olup olmadığını belirtir.<br>Olası değerler şunlardır:<br>- Yüklü: Güncelleştirme bu bilgisayarda yüklü.<br>- Gerekli - Güncelleştirme yüklü değil ve bu bilgisayarda gerekli. |

<br>
**Update** türünde kayıtlar döndüren bir günlük araması yaptığınızda, arama tarafından döndürülen güncelleştirmeleri özetleyen bir kutucuk kümesi görüntülemek üzere **Güncelleştirmeler** görünümünü seçebilirsiniz. **Eksik ve uygulanan güncelleştirmeler** ile **Gerekli ve isteğe bağlı güncelleştirmeler** kutucuklarındaki girişlere tıklayarak görünümün kapsamını bu güncelleştirme kümesine ayarlayabilirsiniz. Kayıtları tek tek döndürmek için **Liste** veya **Tablo** görünümünü seçin.<br> 

![Güncelleştirme Kayıt Türü ile Günlük Arama Güncelleştirme Görünümü](./media/oms-solution-update-management/update-la-view-updates.png)  

**Tablo** görünümünde, KB makalesiyle bir tarayıcı açmak üzere herhangi bir kayda ait **KBID** öğesine tıklayabilirsiniz. Bunun yapılması, ilgili güncelleştirmenin ayrıntıları hakkında hızlıca bilgi almanızı sağlar.<br> 

![Güncelleştirme Kayıt Türü Kutucukları ile Günlük Arama Tablosu](./media/oms-solution-update-management/update-la-view-table.png)

**Liste** görünümünde KBID’nin yanındaki **Görünüm** bağlantısına tıklayarak KB makalesini açabilirsiniz.<br>

![Güncelleştirme Kayıt Türü Kutucukları ile Günlük Arama Listesi](./media/oms-solution-update-management/update-la-view-list.png)

### <a name="updatesummary-records"></a>UpdateSummary kayıtları
Her Windows aracı bilgisayarı için **UpdateSummary** türünde bir kayıt oluşturulur. Bilgisayarda yapılan her güncelleştirme taramasında kayıt güncelleştirilir. **UpdateSummary** kayıtları aşağıdaki tabloda gösterilen özelliklere sahiptir.

| Özellik | Açıklama |
| --- | --- |
| Tür |UpdateSummary |
| SourceSystem |OpsManager |
| Bilgisayar |Bilgisayarın adı. |
| CriticalUpdatesMissing |Bilgisayarda eksik olan kritik güncelleştirme sayısı. |
| ManagementGroupName |SCOM aracıları için yönetim grubunun adı. Diğer aracılar için bu değer AOI-<workspace ID> şeklindedir. |
| NETRuntimeVersion |Bilgisayarda yüklü .NET çalışma zamanı sürümü. |
| OldestMissingSecurityUpdateBucket |Bu bilgisayardaki en eski eksik güvenlik güncelleştirmesinin yayımlanmasından bu yana geçen süreyi kategorilere ayıran demet.<br>Olası değerler şunlardır:<br>- Eski<br>-    180 gün önce<br>- 150 gün önce<br>-    120 gün önce<br>- 90 gün önce<br>- 60 gün önce<br>-    30 gün önce<br>-    En Son |
| OldestMissingSecurityUpdateInDays |Bu bilgisayardaki en eski eksik güvenlik güncelleştirmesinin yayımlanmasından bu yana geçen gün sayısı. |
| OsVersion |Bilgisayarda yüklü işletim sistemi sürümü. |
| OtherUpdatesMissing |Bilgisayarda eksik olan diğer güncelleştirmelerin sayısı. |
| SecurityUpdatesMissing |Bilgisayarda eksik olan güvenlik güncelleştirmelerinin sayısı. |
| SourceComputerId |Bilgisayarı benzersiz olarak tanımlayan GUID. |
| TimeGenerated |Kaydın son güncelleştirildiği tarih ve saat. |
| TotalUpdatesMissing |Bilgisayarda eksik olan toplam güncelleştirme sayısı. |
| WindowsUpdateAgentVersion |Bilgisayardaki Windows Update aracısının sürüm numarası. |
| WindowsUpdateSetting |Bilgisayarın önemli güncelleştirmeleri yükleme yöntemiyle ilgili ayar.<br>Olası değerler şunlardır:<br>- Devre dışı<br>- Yüklemeden önce bildir<br>- Zamanlanmış yükleme |
| WSUSServer |Bilgisayar, kullanacak için yapılandırılmışsa WSUS sunucusunun URL’si. |

## <a name="sample-log-searches"></a>Örnek günlük aramaları
Aşağıdaki tabloda, bu çözüm tarafından toplanan güncelleştirme kayıtlarına ilişkin örnek günlük aramaları sunulmaktadır. 

| Sorgu | Açıklama |
| --- | --- |
| Eksik güncelleştirmeleri olan tüm bilgisayarlar |Type=Update UpdateState=Needed Optional=false &#124; select Computer,Title,KBID,Classification,UpdateSeverity,PublishedDate |
| "COMPUTER01.contoso.com" bilgisayarı için eksik güncelleştirmeler (kendi bilgisayarınızın adıyla değiştirin) |Type=Update UpdateState=Needed Optional=false Computer="COMPUTER01.contoso.com" &#124; select Computer,Title,KBID,Product,UpdateSeverity,PublishedDate |
| Eksik kritik güncelleştirmeleri veya güvenlik güncelleştirmeleri olan tüm bilgisayarlar |Type=Update UpdateState=Needed Optional=false (Classification="Security Updates" OR Classification="Critical Updates") |
| Güncelleştirmelerin el ile uygulandığı makinelerde gerekli olan kritik güncelleştirmeler veya güvenlik güncelleştirmeleri |Type=Update UpdateState=Needed Optional=false (Classification="Security Updates" OR Classification="Critical Updates") Computer IN {Type=UpdateSummary WindowsUpdateSetting=Manual &#124; Distinct Computer} &#124; Distinct KBID |
| Kritik güncelleştirmeleri veya gerekli güvenlik güncelleştirmeleri eksik olan makineler için hata olayları |Type=Event EventLevelName=error Computer IN {Type=Update (Classification="Security Updates" OR Classification="Critical Updates") UpdateState=Needed Optional=false &#124; Distinct Computer} |
| Eksik güncelleştirme paketleri olan tüm bilgisayarlar |Type=Update Optional=false Classification="Update Rollups" UpdateState=Needed &#124; select Computer,Title,KBID,Classification,UpdateSeverity,PublishedDate |
| Tüm bilgisayarlardaki ayrı eksik güncelleştirmeler |Type=Update UpdateState=Needed Optional=false &#124; Distinct Title |
| WSUS bilgisayar üyeliği |Type=UpdateSummary &#124; measure count() by WSUSServer |
| Otomatik güncelleştirme yapılandırması |Type=UpdateSummary &#124; measure count() by WindowsUpdateSetting |
| Otomatik güncelleştirmenin devre dışı olduğu bilgisayarlar |Type=UpdateSummary WindowsUpdateSetting=Manual |
| Paket güncelleştirmesi mevcut olan tüm Linux makinelerinin listesi |Type=Update and OSType=Linux and UpdateState!="Not needed" &#124; measure count() by Computer |
| Kritik veya Güvenlik türünde güvenlik açığını ele alan paket güncelleştirmesine sahip tüm Linux makinelerinin listesi |Type=Update and OSType=Linux and UpdateState!="Not needed" and (Classification="Critical Updates" OR Classification="Security Updates") &#124; measure count() by Computer |
| Bir güncelleştirmenin kullanılabilir olduğu tüm paketlerin listesi |Type=Update and OSType=Linux and UpdateState!="Not needed" |
| Kritik veya Güvenlik türünde güvenlik açığını ele alan güncelleştirmeye sahip tüm paketlerin listesi |Type=Update  and OSType=Linux and UpdateState!="Not needed" and (Classification="Critical Updates" OR Classification="Security Updates") |
| Herhangi bir güncelleştirmenin mevcut olduğu tüm "Ubuntu" makinelerinin listesi |Type=Update and OSType=Linux and OSName = Ubuntu &#124; measure count() by Computer |

## <a name="next-steps"></a>Sonraki adımlar
* Ayrıntılı güncelleştirme verilerini görüntülemek için [Log Analytics](../log-analytics/log-analytics-log-searches.md)’te Günlük Aramalarını kullanın.
* Yönetilen bilgisayarlarınızın güncelleştirme uyumluluğunu gösteren [kendi panolarınızı oluşturun](../log-analytics/log-analytics-dashboards.md).
* Bilgisayardan eksik kritik güncelleştirmeler algılandığında veya bilgisayarda otomatik güncelleştirmeler devre dışı olduğunda [uyarılar oluşturun](../log-analytics/log-analytics-alerts.md).  


