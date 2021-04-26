---
title: Azure SQL veritabanı ve Azure SYNAPSE Analytics için Azure SQL denetimi
description: Veritabanı olaylarını bir denetim günlüğüne izlemek için Azure SQL veritabanı denetimini kullanın.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 03/17/2021
ms.custom: azure-synapse, sqldbrb=1
ms.openlocfilehash: bc7ac6b97d10e5941e46b8be3e12baff32bded4a
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107483070"
---
# <a name="auditing-for-azure-sql-database-and-azure-synapse-analytics"></a>Azure SQL veritabanı ve Azure SYNAPSE Analytics için denetim
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

[Azure SQL veritabanı](sql-database-paas-overview.md) ve [Azure SYNAPSE Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) için denetim, veritabanı olaylarını izler ve bunları Azure Depolama hesabınızdaki bir denetim günlüğüne, Log Analytics çalışma alanına veya Event Hubs yazar.

Denetim şunları da sağlar:

- Mevzuatla uyumluluk, veritabanı etkinliğini anlama ve işletme sorunlarını veya şüpheli güvenlik ihlallerini işaret edebilecek farklılıklar ve anormal durumlar hakkında içgörü sahip olmanıza yardımcı olur.

- Uyumluluğu garanti etmese bile uyumluluk standartlarına uymayı sağlar ve kolaylaştırır. Standartlarla uyumluluğu destekleyen Azure programları hakkında daha fazla bilgi edinmek için, en güncel Azure SQL uyumluluk sertifikalarının listesini de bulabileceğiniz [Azure Güven Merkezi](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942)'ne bakın.

> [!NOTE]
> Azure SQL yönetilen örnek denetimi hakkında bilgi için, aşağıdaki makaleye bakın, [SQL yönetilen örnek denetimi ile çalışmaya](../managed-instance/auditing-configure.md)başlayın.

## <a name="overview"></a><a id="overview"></a>Genel Bakış

SQL veritabanı denetimini kullanarak şunları yapabilirsiniz:

- Seçili olayların denetim izlerini **koruyun** . Denetlenecek veritabanı eylemi kategorilerini tanımlayabilirsiniz.
- Veritabanı etkinliğini **raporla** . Etkinlik ve olay raporlamaya hızlı bir başlangıç yapmak için önceden yapılandırılmış raporları ve bir panoyu kullanabilirsiniz.
- Raporları **analiz edin** . Şüpheli olayları, alışılmışın dışındaki etkinlikleri ve eğilimleri bulabilirsiniz.

> [!IMPORTANT]
> Azure SQL veritabanı ve Azure SYNAPSE için denetim, kullanılabilirlik ve performans için iyileştirilmiştir. Çok yüksek etkinlik veya yüksek ağ yükü, Azure SQL veritabanı ve Azure SYNAPSE, işlemlerin devam etmesini sağlar ve bazı denetlenen olayları kaydedemeyebilir.

### <a name="auditing-limitations"></a>Denetim sınırlamaları

- **Premium Depolama** Şu anda **desteklenmiyor**.
- **Azure Data Lake Storage 2. depolama hesabı** için **hiyerarşik ad alanı** Şu anda **desteklenmiyor**.
- Duraklatılmış bir **Azure SYNAPSE** üzerinde denetim etkinleştirilmesi desteklenmez. Denetimi etkinleştirmek için Azure Synapse'i sürdürün.
- **Azure SYNAPSE SQL havuzları** için Denetim **yalnızca** varsayılan denetim eylemi gruplarını destekler.


#### <a name="define-server-level-vs-database-level-auditing-policy"></a><a id="server-vs-database-level"></a>Sunucu düzeyinde ve veritabanı düzeyinde denetim ilkesini tanımlayın

Bir denetim ilkesi, belirli bir veritabanı için veya Azure 'da varsayılan [sunucu](logical-servers.md) ilkesi olarak TANıMLANABILIR (SQL veritabanını veya Azure SYNAPSE 'i barındırır):

- Sunucu ilkesi, sunucuda var olan ve yeni oluşturulan tüm veritabanları için geçerlidir.

- *Sunucu denetimi etkinse*, *her zaman veritabanına uygulanır*. Veritabanı denetim ayarlarından bağımsız olarak veritabanını denetlenecektir.

- Denetim ilkesi veritabanı düzeyinde bir Log Analytics çalışma alanına veya bir olay hub hedefine tanımlandığında, aşağıdaki işlemler kaynak veritabanı düzeyinde denetim ilkesini tutamaz:
    - [Veritabanı kopyalama](database-copy.md)
    - [Belirli bir noktaya geri yükleme](recovery-using-backups.md)
    - [Coğrafi çoğaltma](active-geo-replication-overview.md) (ikincil veritabanı, veritabanı düzeyinde denetime sahip olmaz)

- Veritabanında etkinleştirmenin yanı sıra sunucu üzerinde denetimi etkinleştirmek, sunucu denetiminin ayarlarından *hiçbirini geçersiz kılmaz veya değiştirmez.* Her iki denetim de yan yana bulunur. Diğer bir deyişle, veritabanı paralel olarak iki kez denetlenir; sunucu ilkesi tarafından bir kez ve veritabanı ilkesi tarafından bir kez.

   > [!NOTE]
   > Her iki sunucu denetimi ve veritabanı blobu denetimini birlikte etkinleştirmemeye özen gösterin:
    >
    > - Belirli bir veritabanı için farklı bir *depolama hesabı*, *bekletme dönemi* veya *Log Analytics çalışma alanı* kullanmak istiyorsunuz.
    > - Sunucu üzerindeki veritabanlarının geri kalanından farklı olan belirli bir veritabanı için olay türlerini veya kategorilerini denetlemek istiyorsunuz. Örneğin, yalnızca belirli bir veritabanı için denetlenmesi gereken tablo eklemeleri olabilir.
   >
   > Aksi takdirde, yalnızca sunucu düzeyinde denetlemeyi etkinleştirmenizi ve veritabanı düzeyinde denetimi tüm veritabanları için devre dışı bırakmayı öneririz.

#### <a name="remarks"></a>Açıklamalar

- Azure aboneliğinizdeki bir Azure Blob depolama alanındaki **BLOB 'Ları eklemek** için denetim günlükleri yazılır
- Denetim günlükleri. XEL biçimindedir ve [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms)kullanılarak açılabilir.
- Sunucu veya veritabanı düzeyinde denetim olayları için bir sabit günlük deposu yapılandırmak için, [Azure depolama tarafından sunulan yönergeleri](../../storage/blobs/storage-blob-immutability-policies-manage.md#enabling-allow-protected-append-blobs-writes)izleyin. Sabit blob depolamayı yapılandırırken **ek ekler 'e Izin ver** ' i seçtiğinizden emin olun.
- Denetim günlüklerini, VNet veya güvenlik duvarının arkasındaki bir Azure depolama hesabına yazabilirsiniz. Belirli yönergeler için bkz. [VNET ve güvenlik duvarının arkasındaki depolama hesabına yönelik denetim yazma](audit-write-storage-account-behind-vnet-firewall.md).
- Günlük biçimi, depolama klasörü ve adlandırma kurallarının hiyerarşisi hakkında daha fazla bilgi için bkz. [BLOB denetim günlüğü biçim başvurusu](./audit-log-format.md).
- [Salt Okuma Çoğaltmalarında](read-scale-out.md) denetim otomatik olarak etkinleştirilir. Depolama klasörlerinin, adlandırma kurallarının ve günlük biçiminin hiyerarşisi hakkında daha fazla ayrıntı için bkz. [SQL veritabanı denetim günlüğü biçimi](audit-log-format.md).
- Azure AD kimlik doğrulaması kullanılırken, başarısız oturum açma kayıtları SQL denetim *günlüğünde görünmez.* Başarısız oturum açma denetim kayıtlarını görüntülemek için, bu olayların ayrıntılarını günlüğe kaydeden [Azure Active Directory portalını](../../active-directory/reports-monitoring/reference-sign-ins-error-codes.md)ziyaret etmeniz gerekir.
- Oturum açma işlemleri ağ geçidi tarafından veritabanının bulunduğu belirli örneğe yönlendirilir.  AAD oturum açma işlemleri söz konusu olduğunda, bu kullanıcıyla istenen veritabanında oturum açmayı denemeden önce kimlik bilgileri doğrulanır.  Başarısız olması durumunda istenen veritabanına hiçbir şekilde erişilemez ve dolayısıyla hiçbir denetim gerçekleştirilmez.  SQL oturum açmaları durumunda, kimlik bilgileri istenen verilerde doğrulanır, bu nedenle bu durumda denetlenebilir.  Her iki durumda da, veritabanına ulaştığı açıkça görülen başarılı oturum açma işlemleri denetlenir.
- Denetim ayarlarınızı yapılandırdıktan sonra, yeni tehdit algılama özelliğini açıp e-postaları güvenlik uyarılarını alacak şekilde yapılandırabilirsiniz. Tehdit algılama 'yı kullandığınızda, olası güvenlik tehditlerini gösterebilen anormal veritabanı etkinliklerinde proaktif uyarılar alırsınız. Daha fazla bilgi için bkz. [tehdit algılamayı kullanmaya başlama](threat-detection-overview.md).

## <a name="set-up-auditing-for-your-server"></a><a id="setup-auditing"></a>Sunucunuz için denetim ayarlama

Varsayılan denetim ilkesi, tüm eylemleri ve aşağıdaki eylem grubu kümesini içerir. Bu, veritabanına göre yürütülen tüm sorguları ve saklı yordamları ve başarılı ve başarısız oturum açma işlemlerini denetler:
  
- BATCH_COMPLETED_GROUP
- SUCCESSFUL_DATABASE_AUTHENTICATION_GROUP
- FAILED_DATABASE_AUTHENTICATION_GROUP
  
[Azure PowerShell kullanarak SQL veritabanı denetimini yönetme](#manage-auditing) bölümünde açıklandığı gibi, PowerShell kullanarak farklı türlerde eylemler ve eylem grupları için denetimi yapılandırabilirsiniz.

Azure SQL veritabanı ve Azure SYNAPSE Audit, bir denetim kaydındaki karakter alanları için 4000 karakter veri depolar. Denetlenebilir bir eylemden döndürülen **ifade** veya **data_sensitivity_information** değerleri 4000 'den fazla karakter içeriyorsa, ilk 4000 karakterden sonraki tüm veriler **kesilir ve denetlenmeyecektir**.
Aşağıdaki bölümde Azure portal kullanılarak denetim yapılandırması açıklanmaktadır.

  > [!NOTE]
  > - Duraklatılmış bir adanmış SQL havuzunda denetim etkinleştirilmesi mümkün değildir. Denetimi etkinleştirmek için adanmış SQL havuzunun duraklamasını kaldırın. [ADANMıŞ SQL havuzu](../..//synapse-analytics/sql/best-practices-dedicated-sql-pool.md)hakkında daha fazla bilgi edinin.
  > - Denetim, Azure portal veya PowerShell cmdlet 'i aracılığıyla bir Log Analytics çalışma alanına ya da bir olay hub hedefine yapılandırıldığında, "SQLSecurityAuditEvents" kategorisi etkin bir [Tanılama ayarı](../../azure-monitor/essentials/diagnostic-settings.md) oluşturulur.

1. [Azure Portal](https://portal.azure.com) gidin.
2. **SQL veritabanınızda** veya **SQL Server** bölmesindeki güvenlik başlığı altında bulunan **denetime** gidin.
3. Sunucu denetim ilkesi ayarlamayı tercih ediyorsanız, veritabanı denetimi sayfasında **sunucu ayarlarını görüntüle** bağlantısını seçebilirsiniz. Daha sonra sunucu denetimi ayarlarını görüntüleyebilir veya değiştirebilirsiniz. Sunucu denetim ilkeleri, bu sunucudaki tüm mevcut ve yeni oluşturulan veritabanları için geçerlidir.

    ![Veritabanı denetimi sayfasında vurgulanan sunucu ayarlarını görüntüle bağlantısını gösteren ekran görüntüsü.](./media/auditing-overview/2_auditing_get_started_server_inherit.png)

4. Veritabanı düzeyinde denetlemeyi etkinleştirmeyi tercih ediyorsanız, **denetimi** **Açık** olarak değiştirin. Sunucu denetimi etkinse, veritabanı yapılandırılmış denetim sunucu denetimi ile yan yana bulunur.

5. Denetim günlüklerinin nerede yazıldığını yapılandırmak için birden çok seçeneğiniz vardır. Günlükleri bir Azure depolama hesabına, Azure Izleyici günlüklerine göre tüketim için bir Log Analytics çalışma alanına veya Olay Hub 'ı kullanarak tüketim için Olay Hub 'ına yazabilirsiniz. Bu seçeneklerin herhangi bir birleşimini yapılandırabilirsiniz ve denetim günlükleri her birine yazılır.
  
   ![depolama seçenekleri](./media/auditing-overview/auditing-select-destination.png)

### <a name="auditing-of-microsoft-support-operations"></a><a id="auditing-of-microsoft-support-operations"></a>Microsoft Desteği işlemlerinin denetlenmesi

Azure SQL Server Microsoft Desteği işlemlerinin denetlenmesi, destek isteği sırasında sunucunuza erişmesi gerektiğinde Microsoft Destek mühendislerinin işlemlerini denetlemenize olanak tanır. Bu özelliğin kullanımı, denetim ile birlikte, iş gücünüze daha fazla saydamlık sağlar ve anomali algılama, eğilim görselleştirme ve veri kaybı önleme için izin verir.

Microsoft Desteği işlemlerinde denetim sağlamak için **Azure SQL Server** bölmeniz içindeki güvenlik başlığı altında bulunan **Denetim** ' e gidin ve **Microsoft destek işlemlerinin denetimini** **Açık** olarak değiştirin.

  > [!IMPORTANT]
  > Microsoft destek işlemlerinin denetlenmesi, depolama hesabı hedefini desteklemez. Özelliği etkinleştirmek için, bir Log Analytics çalışma alanı veya bir olay hub 'ı hedefi yapılandırılmalıdır.

![Microsoft Desteği Işlemlerinin ekran görüntüsü](./media/auditing-overview/support-operations.png)

Log Analytics çalışma alanınızdaki Microsoft Desteği işlemlerin denetim günlüklerini gözden geçirmek için aşağıdaki sorguyu kullanın:

```kusto
AzureDiagnostics
| where Category == "DevOpsOperationsAudit"
```

### <a name="audit-to-storage-destination"></a><a id="audit-storage-destination"></a>Depolama hedefine yönelik denetim

Denetim günlüklerini bir depolama hesabına yazmayı yapılandırmak için, **Denetim** bölümüne geldiğinizde **depolama** ' yı seçin. Günlüklerin kaydedileceği Azure Depolama hesabını seçin ve ardından **Gelişmiş Özellikler**' i açarak bekletme dönemini seçin. Daha sonra **Kaydet**'e tıklayın. Saklama süresinden daha eski Günlükler silinir.

- Saklama dönemi için varsayılan değer 0 ' dır (sınırsız saklama). Denetim için depolama hesabını yapılandırırken **Gelişmiş Özellikler** ' de **bekletme (gün)** kaydırıcısını taşıyarak bu değeri değiştirebilirsiniz.
  - Saklama süresini 0 ' dan (sınırsız saklama) başka herhangi bir değere değiştirirseniz, lütfen bekletme 'nin yalnızca bekletme değeri değiştirildikten sonra yazılan günlüklere uygulanacağını unutmayın (bekletme etkin olduktan sonra bile, bekletme olarak sınırsız olarak ayarlandığında zaman içinde yazılır).

  ![depolama hesabı](./media/auditing-overview/auditing_select_storage.png)

### <a name="audit-to-log-analytics-destination"></a><a id="audit-log-analytics-destination"></a>Log Analytics hedefe yönelik denetim
  
Log Analytics çalışma alanına denetim günlükleri yazmayı yapılandırmak için **Log Analytics** ' i seçin ve **Log Analytics Ayrıntılar**' ı açın. Günlüklerin yazılacağı Log Analytics çalışma alanını seçin ve ardından **Tamam**' a tıklayın. Log Analytics çalışma alanı oluşturmadıysanız, bkz. [Azure portal Log Analytics çalışma alanı oluşturma](../../azure-monitor/logs/quick-create-workspace.md).

   ![Loganalticsworkspace](./media/auditing-overview/auditing_select_oms.png)

Azure Izleyici Log Analytics çalışma alanı hakkında daha fazla bilgi için bkz. [Azure Izleyici günlükleri dağıtımınızı tasarlama](../../azure-monitor/logs/design-logs-deployment.md)
   
### <a name="audit-to-event-hub-destination"></a><a id="audit-event-hub-destination"></a>Olay Hub 'ı hedefine yönelik denetim

Bir olay hub 'ına denetim günlükleri yazmayı yapılandırmak için **Olay Hub**'ı ' nı seçin. Günlüklerin yazılacağı Olay Hub 'ını seçin ve ardından **Kaydet**' e tıklayın. Olay Hub 'ının, veritabanınız ve sunucunuz ile aynı bölgede olduğundan emin olun.

   ![Eventhub](./media/auditing-overview/auditing_select_event_hub.png)

## <a name="analyze-audit-logs-and-reports"></a><a id="subheading-3"></a>Denetim günlüklerini ve raporları çözümleme

Denetim günlüklerini Azure Izleyici günlüklerine yazmayı seçtiyseniz:

- [Azure portalını](https://portal.azure.com) kullanın. İlgili veritabanını açın. Veritabanının **Denetim** sayfasının en üstünde **Denetim günlüklerini görüntüle**' yi seçin.

    ![Denetim günlüklerini görüntüle](./media/auditing-overview/auditing-view-audit-logs.png)

- Ardından, günlükleri görüntülemenin iki yolu vardır:

    **Denetim kayıtları** sayfasının en üstündeki **Log Analytics** ' a tıkladığınızda, Log Analytics çalışma alanındaki Günlükler görünümü açılır. burada, zaman aralığını ve arama sorgusunu özelleştirebilirsiniz.

    ![Log Analytics çalışma alanında aç](./media/auditing-overview/auditing-log-analytics.png)

    **Denetim kayıtları** sayfasının en üstündeki **Görünüm panosunu** tıklatmak, güvenlik öngörülerine gidebileceğiniz, hassas verilere erişim ve daha fazlasını ekleyebileceğiniz denetim günlükleri bilgilerini gösteren bir pano açar. Bu Pano, verilerinize yönelik güvenlik öngörüleri elde etmenize yardımcı olmak için tasarlanmıştır.
    Ayrıca zaman aralığını ve arama sorgusunu özelleştirebilirsiniz.
    ![Log Analytics panosunu görüntüle](media/auditing-overview/auditing-view-dashboard.png)

    ![Log Analytics panosu](media/auditing-overview/auditing-log-analytics-dashboard.png)

    ![Log Analytics güvenlik öngörüleri](media/auditing-overview/auditing-log-analytics-dashboard-data.png)

- Alternatif olarak, Log Analytics dikey penceresinden denetim günlüklerine de erişebilirsiniz. Log Analytics çalışma alanınızı açın ve **genel** bölümünde **Günlükler**' e tıklayın. Denetim günlüklerini görüntülemek için *"SQLSecurityAuditEvents" araması* yapın gibi basit bir sorgu ile başlayabilirsiniz.
    Buradan, denetim günlüğü verilerinizde gelişmiş aramalar çalıştırmak için [Azure izleyici günlüklerini](../../azure-monitor/logs/log-query-overview.md)  de kullanabilirsiniz. Azure Izleyici günlükleri, tüm iş yüklerinizde ve sunucularınızda milyonlarca kaydı kolayca çözümlemek için tümleşik arama ve özel panolar kullanarak gerçek zamanlı operasyonel içgörüler sağlar. Azure Izleyici günlükleri arama dili ve komutları hakkında daha fazla yararlı bilgi için bkz. [Azure izleyici günlükleri arama başvurusu](../../azure-monitor/logs/log-query-overview.md).

Denetim günlüklerini Olay Hub 'ına yazmayı seçtiyseniz:

- Olay Hub 'ından denetim günlükleri verilerini kullanmak için, olayları tüketmek ve bunları bir hedefe yazmak üzere bir akış ayarlamanız gerekir. Daha fazla bilgi için bkz. [Azure Event Hubs belgeleri](../index.yml).
- Olay Hub 'ındaki denetim günlükleri [Apache avro](https://avro.apache.org/) olaylarının gövdesinde YAKALANıR ve UTF-8 KODLAMASı ile JSON biçimlendirme kullanılarak depolanır. Denetim günlüklerini okumak için [Avro Araçları](../../event-hubs/event-hubs-capture-overview.md#use-avro-tools)'nı veya bu biçimi işleyebilen benzer araçları kullanabilirsiniz.

Denetim günlüklerini bir Azure depolama hesabına yazmayı seçerseniz, günlükleri görüntülemek için kullanabileceğiniz çeşitli yöntemler vardır:

- Denetim günlükleri, kurulum sırasında seçtiğiniz hesapta toplanır. [Azure Depolama Gezgini](https://storageexplorer.com/)gibi bir araç kullanarak denetim günlüklerini inceleyebilirsiniz. Azure depolama 'da denetim günlükleri, **sqldbauditlogs** adlı bir kapsayıcı içinde blob dosyaları koleksiyonu olarak kaydedilir. Depolama klasörlerinin, adlandırma kurallarının ve günlük biçiminin hiyerarşisi hakkında daha fazla ayrıntı için bkz. [SQL veritabanı denetim günlüğü biçimi](./audit-log-format.md).

- [Azure portalını](https://portal.azure.com) kullanın.  İlgili veritabanını açın. Veritabanının **Denetim** sayfasının en üstünde **Denetim günlüklerini görüntüle**' ye tıklayın.

    ![Denetim günlüklerini görüntüle](./media/auditing-overview/auditing-view-audit-logs.png)

    Günlükleri görüntüleyebileceğiniz **denetim kayıtları** açılır.

  - **Denetim kayıtları** sayfasının en üstünde bulunan **filtre** ' ye tıklayarak belirli tarihleri görüntüleyebilirsiniz.
  - **Denetim kaynağını** değiştirerek *sunucu denetim ilkesi* ve *veritabanı denetim ilkesi* tarafından oluşturulan denetim kayıtları arasında geçiş yapabilirsiniz.

       ![Denetim kayıtlarını görüntüleme seçeneklerini gösteren ekran görüntüsü.]( ./media/auditing-overview/8_auditing_get_started_blob_audit_records.png)

- Denetim günlüğü verilerini tablosal biçiminde döndürmek için **sys.fn_get_audit_file** (T-SQL) sistem işlevini kullanın. Bu işlevi kullanma hakkında daha fazla bilgi için bkz. [sys.fn_get_audit_file](/sql/relational-databases/system-functions/sys-fn-get-audit-file-transact-sql).

- SQL Server Management Studio (SSMS 17 ' den başlayarak) **birleştirme denetim dosyalarını** kullanın:
    1. Ssms menüsünden **Dosya**  >  **açma**  >  **birleştirme denetim dosyaları**' nı seçin.

        ![Denetim dosyalarını Birleştir menü seçeneğini gösteren ekran görüntüsü.](./media/auditing-overview/9_auditing_get_started_ssms_1.png)
    2. **Denetim dosyaları Ekle** iletişim kutusu açılır. Denetim dosyalarını yerel bir diskten birleştirmeyi veya Azure Storage 'tan içeri aktarmayı seçmek için **Ekle** seçeneklerinden birini seçin. Azure depolama ayrıntılarını ve hesap anahtarınızı sağlamanız gerekir.

    3. Birleştirilecek tüm dosyalar eklendikten sonra birleştirme işlemini gerçekleştirmek için **Tamam** ' ı tıklatın.

    4. Birleştirilmiş dosya SSMS 'de açılır; burada bunu görüntüleyebilir ve analiz edebilir, ayrıca bir XEL veya CSV dosyasına ya da bir tabloya dışarı aktarabilirsiniz.

- Power BI kullanın. Power BI ' de denetim günlüğü verilerini görüntüleyebilir ve analiz edebilirsiniz. Daha fazla bilgi edinmek ve indirilebilir bir şablona erişmek için bkz. [Power BI denetim günlüğü verilerini çözümleme](https://techcommunity.microsoft.com/t5/azure-database-support-blog/sql-azure-blob-auditing-basic-power-bi-dashboard/ba-p/368895).
- Azure Storage blob kapsayıcısından Portal aracılığıyla veya [Azure Depolama Gezgini](https://storageexplorer.com/)gibi bir araç kullanarak günlük dosyalarını indirin.
  - Bir günlük dosyasını yerel olarak indirdikten sonra SSMS 'de günlükleri açmak, görüntülemek ve analiz etmek için dosyaya çift tıklayın.
  - Birden çok dosyayı aynı anda Azure Depolama Gezgini aracılığıyla da indirebilirsiniz. Bunu yapmak için belirli bir alt klasöre sağ tıklayın ve **farklı kaydet** ' i seçerek yerel bir klasöre kaydedin.

- Ek Yöntemler:

  - Birden çok dosya veya günlük dosyası içeren bir alt klasör indirdikten sonra, daha önce açıklanan SSMS birleştirme denetim dosyaları yönergeleri bölümünde açıklandığı gibi yerel olarak birleştirebilirsiniz.
  - Blob denetim günlüklerini programlı olarak görüntüle: PowerShell kullanarak [genişletilmiş olaylar dosyalarını sorgulama](https://sqlscope.wordpress.com/2014/11/15/reading-extended-event-files-using-client-side-tools-only/) .

## <a name="production-practices"></a><a id="production-practices"></a>Üretim uygulamaları


### <a name="auditing-geo-replicated-databases"></a>Coğrafi olarak çoğaltılan veritabanlarını denetleme

Coğrafi olarak çoğaltılan veritabanları ile birincil veritabanında denetimi etkinleştirdiğinizde, ikincil veritabanı aynı denetim ilkesine sahip olur. Birincil veritabanından bağımsız olarak **ikincil sunucuda** denetimi etkinleştirerek ikincil veritabanında denetim kurmak mümkündür.

- Sunucu düzeyi (**önerilir**): hem **birincil sunucuda** hem de **ikincil sunucuda** denetimi açın-birincil ve ikincil veritabanlarının her biri kendi sunucu düzeyi ilkeye göre ayrı olarak denetlenir.
- Veritabanı düzeyi: ikincil veritabanları için veritabanı düzeyinde denetim, yalnızca birincil veritabanı denetim ayarlarından yapılandırılabilir.
  - Sunucuda değil, *birincil veritabanının kendisinde* denetim etkinleştirilmiş olmalıdır.
  - Birincil veritabanında denetim etkinleştirildikten sonra, ikincil veritabanında da etkin hale gelir.

    > [!IMPORTANT]
    > Veritabanı düzeyinde denetim sayesinde, ikincil veritabanı için depolama ayarları, birincil veritabanıyla aynı olur ve bölgesel trafiğe neden olur. Yalnızca sunucu düzeyinde denetimi etkinleştirmenizi ve veritabanı düzeyinde denetimi tüm veritabanları için devre dışı bırakmayı öneririz.

### <a name="storage-key-regeneration"></a>Depolama anahtarı yeniden oluşturma

Üretimde, depolama anahtarlarınızı düzenli aralıklarla yenilemeniz olasıdır. Azure depolama 'ya denetim günlükleri yazarken, anahtarlarınızı yenilerken denetim ilkenizi yeniden kaydetmeniz gerekir. Süreç şu şekilde ilerler:

1. **Depolama** altında **Gelişmiş Özellikler** ' i açın. **Depolama erişim anahtarı** kutusunda **İkincil**' ı seçin. Sonra Denetim yapılandırma sayfasının en üstündeki **Kaydet** ' e tıklayın.

    ![İkincil depolama erişim anahtarı seçme işlemini gösteren ekran görüntüsü.](./media/auditing-overview/5_auditing_get_started_storage_key_regeneration.png)
2. Depolama yapılandırması sayfasına gidin ve birincil erişim tuşunu yeniden oluşturun.

    ![Gezinti bölmesi](./media/auditing-overview/6_auditing_get_started_regenerate_key.png)
3. Denetim yapılandırması sayfasına dönün, depolama erişim anahtarını ikincilden birinciye geçirin ve ardından **Tamam**' a tıklayın. Sonra Denetim yapılandırma sayfasının en üstündeki **Kaydet** ' e tıklayın.
4. Depolama yapılandırması sayfasına dönün ve ikincil erişim anahtarını yeniden oluşturun (bir sonraki anahtarın yenileme döngüsüne hazırlanın).

## <a name="manage-azure-sql-database-auditing"></a><a id="manage-auditing"></a>Azure SQL veritabanı denetimini yönetme

### <a name="using-azure-powershell"></a>Azure PowerShell’i kullanma

**PowerShell cmdlet 'leri (ek filtreleme IÇIN WHERE yan tümcesi desteği dahil)**:

- [Veritabanı denetim Ilkesi oluştur veya güncelleştir (set-AzSqlDatabaseAudit)](/powershell/module/az.sql/set-azsqldatabaseaudit)
- [Sunucu denetim Ilkesi oluştur veya güncelleştir (set-AzSqlServerAudit)](/powershell/module/az.sql/set-azsqlserveraudit)
- [Veritabanı denetim Ilkesini al (Get-AzSqlDatabaseAudit)](/powershell/module/az.sql/get-azsqldatabaseaudit)
- [Sunucu denetim Ilkesini al (Get-AzSqlServerAudit)](/powershell/module/az.sql/get-azsqlserveraudit)
- [Veritabanı denetim Ilkesini Kaldır (Remove-AzSqlDatabaseAudit)](/powershell/module/az.sql/remove-azsqldatabaseaudit)
- [Sunucu denetim Ilkesini Kaldır (Remove-AzSqlServerAudit)](/powershell/module/az.sql/remove-azsqlserveraudit)

Betik örneği için bkz. [PowerShell kullanarak denetim ve tehdit algılamayı yapılandırma](scripts/auditing-threat-detection-powershell-configure.md).

### <a name="using-rest-api"></a>REST API kullanma

**REST API**:

- [Veritabanı denetim Ilkesi oluştur veya güncelleştir](/rest/api/sql/database%20auditing%20settings/createorupdate)
- [Sunucu denetim Ilkesi oluştur veya güncelleştir](/rest/api/sql/server%20auditing%20settings/createorupdate)
- [Veritabanı denetim Ilkesini al](/rest/api/sql/database%20auditing%20settings/get)
- [Sunucu denetim Ilkesini al](/rest/api/sql/server%20auditing%20settings/get)

Ek filtreleme için WHERE yan tümcesi desteğiyle genişletilmiş ilke:

- [Veritabanı *genişletilmiş* Denetim İlkesi Oluştur veya güncelleştir](/rest/api/sql/database%20extended%20auditing%20settings/createorupdate)
- [Sunucu *genişletilmiş* Denetim İlkesi Oluştur veya güncelleştir](/rest/api/sql/server%20auditing%20settings/createorupdate)
- [Veritabanı *genişletilmiş* denetim ilkesini al](/rest/api/sql/database%20extended%20auditing%20settings/get)
- [Sunucu *genişletilmiş* denetim ilkesini al](/rest/api/sql/server%20auditing%20settings/get)

### <a name="using-azure-cli"></a>Azure CLI’yı kullanma

- [Sunucunun Denetim ilkesini yönetme](/cli/azure/sql/server/audit-policy)
- [Veritabanının denetim ilkesini yönetme](/cli/azure/sql/db/audit-policy)

### <a name="using-azure-resource-manager-templates"></a>Azure Resource Manager şablonlarını kullanma

[Azure Resource Manager](../../azure-resource-manager/management/overview.md) şablonları kullanarak Azure SQL veritabanı denetimini aşağıdaki örneklerde gösterildiği gibi yönetebilirsiniz:

- [Azure Blob depolama hesabına denetim günlükleri yazmak için etkin bir Azure SQL veritabanı dağıtın](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-blob-storage)
- [Denetim günlüklerini Log Analytics yazmak için etkin bir Azure SQL veritabanı dağıtın](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-oms)
- [Denetim günlüklerini Event Hubs yazmak için etkin bir Azure SQL veritabanı dağıtın](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-eventhub)

> [!NOTE]
> Bağlantılı örnekler bir dış ortak depodadır ve ' olduğu gibi ', garanti olmadan ve herhangi bir Microsoft destek programı/hizmeti kapsamında desteklenmez.
