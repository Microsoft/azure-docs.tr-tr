---
title: Denetim ile çalışmaya başlama
description: Veritabanı olaylarını bir denetim günlüğüne izlemek için Azure SQL veritabanı denetimini kullanın.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: barmichal
ms.author: mibar
ms.reviewer: vanto
ms.date: 08/22/2019
ms.openlocfilehash: 65d092b36ed0e339a77bb423f24079caae38ab84
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73821982"
---
# <a name="get-started-with-sql-database-auditing"></a>SQL veritabanı denetimini kullanmaya başlayın

Azure [SQL veritabanı](sql-database-technical-overview.md) ve [SQL veri ambarı](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) için denetim, veritabanı olaylarını izler ve bunları Azure Depolama hesabınızdaki bir denetim günlüğüne yazar, Log Analytics çalışma alanı veya Event Hubs. Ayrıca Denetim:

- Yönetmelikli uyumluluğu korumanıza, veritabanı etkinliklerini anlamanıza ve işle ilgili endişeleri veya şüpheli güvenlik ihlallerini gösterebilen tutarsızlıklar ve bozukluklar elde etmenize yardımcı olur.

- Uyumluluğu garanti etmez ancak uyumluluk standartlarına uygunluğunu sağlar ve kolaylaştırır. Standartlar uyumluluğunu destekleyen Azure programları hakkında daha fazla bilgi için, SQL veritabanı uyumluluk sertifikalarının en güncel listesini bulabileceğiniz [Azure Güven Merkezi](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) bakın.


> [!NOTE] 
> Bu konu başlığı, Azure SQL sunucusunun yanı sıra Azure SQL sunucusu üzerinde oluşturulmuş olan SQL Veritabanı ve SQL Veri Ambarı veritabanları için de geçerlidir. Kolaylık açısından, hem SQL Veritabanı hem de SQL Veri Ambarı için SQL Veritabanı terimi kullanılmaktadır.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]


## <a id="subheading-1"></a>Azure SQL veritabanı denetimine genel bakış

SQL veritabanı denetimini kullanarak şunları yapabilirsiniz:

- Seçili olayların denetim izlerini **koruyun** . Denetlenecek veritabanı eylemi kategorilerini tanımlayabilirsiniz.
- Veritabanı etkinliğini **raporla** . Etkinlik ve olay raporlamasını hızlı bir şekilde kullanmaya başlamak için önceden yapılandırılmış raporları ve panoları kullanabilirsiniz.
- Raporları **analiz edin** . Şüpheli olayları, olağan dışı etkinlikleri ve eğilimleri bulabilirsiniz.

> [!IMPORTANT]
> Azure aboneliğinizdeki Azure Blob depolama alanındaki **BLOB 'Ları eklemek** için denetim günlükleri yazılır.
>
> - Tüm depolama türleri (v1, v2, blob) desteklenir.
> - Tüm depolama çoğaltması yapılandırması desteklenir.
> - **Premium Depolama** Şu anda **desteklenmiyor**.
> - **VNET 'Teki depolama** Şu anda **desteklenmiyor**.
> - **Bir güvenlik duvarının arkasındaki depolama** Şu anda **desteklenmiyor**.
> - **Azure Data Lake Storage 2. depolama hesabı** için **hiyerarşik ad alanı** Şu anda **desteklenmiyor**.

## <a id="subheading-8"></a>Sunucu düzeyinde ve veritabanı düzeyinde denetim ilkesini tanımlayın

Belirli bir veritabanı veya varsayılan sunucu ilkesi olarak bir denetim ilkesi tanımlanabilir:

- Sunucu ilkesi, sunucuda var olan ve yeni oluşturulan tüm veritabanları için geçerlidir.

- *Sunucu blobu denetimi etkinse*, *her zaman veritabanına uygulanır*. Veritabanı denetim ayarlarından bağımsız olarak veritabanını denetlenecektir.

- Veritabanı veya veri ambarında blob denetimini etkinleştirmek, sunucuda etkinleştirilmesinin yanı sıra sunucu blob denetimi ayarlarından *hiçbirini geçersiz kılmaz veya değiştirmez.* Her iki denetim de yan yana bulunur. Diğer bir deyişle, veritabanı paralel olarak iki kez denetlenir; sunucu ilkesi tarafından bir kez ve veritabanı ilkesi tarafından bir kez.

   > [!NOTE]
   > Her iki sunucu blob denetimi ve veritabanı blobu denetimini birlikte etkinleştirmemeye özen gösterin:
    > - Belirli bir veritabanı için farklı bir *depolama hesabı* veya *Bekletme dönemi* kullanmak istiyorsunuz.
    > - Sunucu üzerindeki veritabanlarının geri kalanından farklı olan belirli bir veritabanı için olay türlerini veya kategorilerini denetlemek istiyorsunuz. Örneğin, yalnızca belirli bir veritabanı için denetlenmesi gereken tablo eklemeleri olabilir.
   >
   > Aksi takdirde, yalnızca sunucu düzeyinde blob denetimini etkinleştirmenizi ve veritabanı düzeyinde denetimi tüm veritabanları için devre dışı bırakmayı öneririz.

## <a id="subheading-2"></a>Veritabanınız için Denetim kurma

Aşağıdaki bölümde Azure portal kullanılarak denetim yapılandırması açıklanmaktadır.

1. [Azure Portal](https://portal.azure.com) gidin.
2. SQL veritabanı/sunucu bölmeniz içindeki güvenlik başlığı altında bulunan **Denetim** ' e gidin.

    <a id="auditing-screenshot"></a>![gezinti bölmesi][1]

3. Sunucu denetim ilkesi ayarlamayı tercih ediyorsanız, veritabanı denetimi sayfasında **sunucu ayarlarını görüntüle** bağlantısını seçebilirsiniz. Daha sonra sunucu denetimi ayarlarını görüntüleyebilir veya değiştirebilirsiniz. Sunucu denetim ilkeleri, bu sunucudaki tüm mevcut ve yeni oluşturulan veritabanları için geçerlidir.

    ![Gezinti bölmesi][2]

4. Veritabanı düzeyinde denetlemeyi etkinleştirmeyi tercih ediyorsanız, **denetimi** **Açık**olarak değiştirin.

    Sunucu denetimi etkinse, veritabanı yapılandırılmış denetim sunucu denetimi ile yan yana bulunur.

    ![Gezinti bölmesi][3]

5. **Yeni** -artık denetim günlüklerinin yazılacağı yeri yapılandırmak için birden çok seçeneğiniz vardır. Günlükleri bir Azure depolama hesabına, Azure Izleyici günlüklerine göre tüketim için bir Log Analytics çalışma alanına veya Olay Hub 'ı kullanarak tüketim için Olay Hub 'ına yazabilirsiniz. Bu seçeneklerin herhangi bir birleşimini yapılandırabilirsiniz ve denetim günlükleri her birine yazılır.

   > [!WARNING]
   > Log Analytics için denetimin etkinleştirilmesi, alma ücretlerine göre maliyet doğurur. Lütfen bu [seçeneği](https://azure.microsoft.com/pricing/details/monitor/)kullanarak ilişkili maliyetten haberdar olun veya denetim günlüklerini bir Azure depolama hesabında depolamayı düşünün.

    ![depolama seçenekleri](./media/sql-database-auditing-get-started/auditing-select-destination.png)

6. Bir depolama hesabına denetim günlükleri yazmayı yapılandırmak için **depolama** ve açık **depolama ayrıntıları**' nı seçin. Günlüklerin kaydedileceği Azure Depolama hesabını seçin ve ardından bekletme dönemini seçin. Eski Günlükler silinir. Daha sonra, **Tamam**'a tıklayın.

   > [!IMPORTANT]
   > - Saklama dönemi için varsayılan değer 0 ' dır (sınırsız saklama). Bu değeri, depolama hesabını denetim için yapılandırırken **depolama ayarları** ' nda **bekletme (gün)** kaydırıcısını taşıyarak değiştirebilirsiniz.
   > - Saklama süresini 0 ' dan (sınırsız saklama) başka herhangi bir değere değiştirirseniz, lütfen bekletmenin yalnızca bekletme değeri değiştirildikten sonra yazılan günlüklere uygulanacağını unutmayın (bekletme olarak ayarlandığında, bekletme olarak ayarlanan Günlükler, bu süre sonunda bile) bekletme etkin)

    ![depolama hesabı](./media/sql-database-auditing-get-started/auditing_select_storage.png)

7. Log Analytics çalışma alanına denetim günlükleri yazmayı yapılandırmak için **Log Analytics (Önizleme)** öğesini seçin ve **Log Analytics ayrıntılarını**açın. Günlüklerin yazılacağı Log Analytics çalışma alanını seçin veya oluşturun ve ardından **Tamam**' a tıklayın.

    ![Log Analytics çalışma alanı](./media/sql-database-auditing-get-started/auditing_select_oms.png)

8. Bir olay hub 'ına denetim günlükleri yazmayı yapılandırmak için **Olay Hub 'ı (Önizleme)** seçin ve **Olay Hub 'ı ayrıntılarını**açın. Günlüklerin yazılacağı Olay Hub 'ını seçin ve ardından **Tamam**' a tıklayın. Olay Hub 'ının, veritabanınız ve sunucunuz ile aynı bölgede olduğundan emin olun.

    ![Olay hub'ı](./media/sql-database-auditing-get-started/auditing_select_event_hub.png)

9. **Kaydet** düğmesine tıklayın.
10. Denetlenen olayları özelleştirmek istiyorsanız bunu [PowerShell cmdlet 'leri](#subheading-7) veya [REST API](#subheading-9)aracılığıyla yapabilirsiniz.
11. Denetim ayarlarınızı yapılandırdıktan sonra, yeni tehdit algılama özelliğini açıp e-postaları güvenlik uyarılarını alacak şekilde yapılandırabilirsiniz. Tehdit algılama 'yı kullandığınızda, olası güvenlik tehditlerini gösterebilen anormal veritabanı etkinliklerinde proaktif uyarılar alırsınız. Daha fazla bilgi için bkz. [tehdit algılamayı kullanmaya başlama](sql-database-threat-detection-get-started.md).

> [!IMPORTANT]
> Duraklatılmış bir Azure SQL veri ambarında denetim etkinleştirilmesi mümkün değildir. Bunu etkinleştirmek için veri ambarının duraklamasını kaldırın.

> [!WARNING]
> Üzerinde Azure SQL veri ambarı olan bir sunucuda denetimin etkinleştirilmesi **, veri ambarının yeniden sürdürülmesiyle ve** faturalandırma ücretlerine tabi olabilecek yeniden duraklatılmasıyla sonuçlanır.

## <a id="subheading-3"></a>Denetim günlüklerini ve raporları çözümleme

Denetim günlüklerini Azure Izleyici günlüklerine yazmayı seçtiyseniz:

- [Azure Portal](https://portal.azure.com)kullanın.  İlgili veritabanını açın. Veritabanının **Denetim** sayfasının en üstünde **Denetim günlüklerini görüntüle**' ye tıklayın.

    ![Denetim günlüklerini görüntüle](./media/sql-database-auditing-get-started/auditing-view-audit-logs.png)

- Ardından, günlükleri görüntülemenin iki yolu vardır:
    
    **Denetim kayıtları** sayfasının en üstündeki **Log Analytics** ' a tıkladığınızda, Log Analytics çalışma alanındaki Günlükler görünümü açılır. burada, zaman aralığını ve arama sorgusunu özelleştirebilirsiniz.
    
    ![Log Analytics çalışma alanında aç](./media/sql-database-auditing-get-started/auditing-log-analytics.png)

    **Denetim kayıtları** sayfasının en üstündeki **Görünüm panosunu** tıklatmak, güvenlik öngörülerine gidebileceğiniz, hassas verilere erişim ve daha fazlasını ekleyebileceğiniz denetim günlükleri bilgilerini gösteren bir pano açar. Bu Pano, verilerinize yönelik güvenlik öngörüleri elde etmenize yardımcı olmak için tasarlanmıştır.
    Ayrıca zaman aralığını ve arama sorgusunu özelleştirebilirsiniz. 
    ![Log Analytics panosunu görüntüle](media/sql-database-auditing-get-started/auditing-view-dashboard.png)

    ![Log Analytics panosu](media/sql-database-auditing-get-started/auditing-log-analytics-dashboard.png)

    ![Log Analytics güvenlik öngörüleri](media/sql-database-auditing-get-started/auditing-log-analytics-dashboard-data.png)
 

- Alternatif olarak, Log Analytics dikey penceresinden denetim günlüklerine de erişebilirsiniz. Log Analytics çalışma alanınızı açın ve **genel** bölümünde **Günlükler**' e tıklayın. Denetim günlüklerini görüntülemek için *"SQLSecurityAuditEvents" araması* yapın gibi basit bir sorgu ile başlayabilirsiniz.
    Buradan, denetim günlüğü verilerinizde gelişmiş aramalar çalıştırmak için [Azure izleyici günlüklerini](../log-analytics/log-analytics-log-search.md) de kullanabilirsiniz. Azure Izleyici günlükleri, tüm iş yüklerinizde ve sunucularınızda milyonlarca kaydı kolayca çözümlemek için tümleşik arama ve özel panolar kullanarak gerçek zamanlı operasyonel içgörüler sağlar. Azure Izleyici günlükleri arama dili ve komutları hakkında daha fazla yararlı bilgi için bkz. [Azure izleyici günlükleri arama başvurusu](../log-analytics/log-analytics-log-search.md).

Denetim günlüklerini Olay Hub 'ına yazmayı seçtiyseniz:

- Olay Hub 'ından denetim günlükleri verilerini kullanmak için, olayları tüketmek ve bunları bir hedefe yazmak üzere bir akış ayarlamanız gerekir. Daha fazla bilgi için bkz. [Azure Event Hubs belgeleri](https://docs.microsoft.com/azure/event-hubs/).
- Olay Hub 'ındaki denetim günlükleri [Apache avro](https://avro.apache.org/) olaylarının gövdesinde YAKALANıR ve UTF-8 KODLAMASı ile JSON biçimlendirme kullanılarak depolanır. Denetim günlüklerini okumak için, bu biçimi işleyen [avro araçları](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview#use-avro-tools) veya benzer araçları kullanabilirsiniz.

Denetim günlüklerini bir Azure depolama hesabına yazmayı seçerseniz, günlükleri görüntülemek için kullanabileceğiniz çeşitli yöntemler vardır:

- Denetim günlükleri, kurulum sırasında seçtiğiniz hesapta toplanır. [Azure Depolama Gezgini](https://storageexplorer.com/)gibi bir araç kullanarak denetim günlüklerini inceleyebilirsiniz. Azure depolama 'da denetim günlükleri, **sqldbauditlogs**adlı bir kapsayıcı içinde blob dosyaları koleksiyonu olarak kaydedilir. Depolama klasörünün, adlandırma kurallarının ve günlük biçiminin hiyerarşisi hakkında daha fazla ayrıntı için bkz. [SQL veritabanı denetim günlüğü biçimi](https://go.microsoft.com/fwlink/?linkid=829599).

- [Azure Portal](https://portal.azure.com)kullanın.  İlgili veritabanını açın. Veritabanının **Denetim** sayfasının en üstünde **Denetim günlüklerini görüntüle**' ye tıklayın.

    ![Gezinti bölmesi][7]

    Günlükleri görüntüleyebileceğiniz **denetim kayıtları** açılır.

  - **Denetim kayıtları** sayfasının en üstünde bulunan **filtre** ' ye tıklayarak belirli tarihleri görüntüleyebilirsiniz.
  - **Denetim kaynağını**değiştirerek *sunucu denetim ilkesi* ve *veritabanı denetim ilkesi* tarafından oluşturulan denetim kayıtları arasında geçiş yapabilirsiniz.
  - Yalnızca SQL **ınjections onay kutusu için denetim kayıtlarını göster** ' i denetleyerek, yalnızca SQL ekleme ile ilgili denetim kayıtlarını görüntüleyebilirsiniz.

       ![Gezinti bölmesi][8]

- Denetim günlüğü verilerini tablosal biçiminde döndürmek için **sys. fn_get_audit_file** (T-SQL) sistem işlevini kullanın. Bu işlevi kullanma hakkında daha fazla bilgi için bkz. [sys. fn_get_audit_file](https://docs.microsoft.com/sql/relational-databases/system-functions/sys-fn-get-audit-file-transact-sql).

- SQL Server Management Studio (SSMS 17 ' den başlayarak) **birleştirme denetim dosyalarını** kullanın:
    1. SSMS menüsünden **dosya** >  > **birleştirme denetim dosyalarını** **Aç** ' ı seçin.

        ![Gezinti bölmesi][9]
    2. **Denetim dosyaları Ekle** iletişim kutusu açılır. Denetim dosyalarını yerel bir diskten birleştirmeyi veya Azure Storage 'tan içeri aktarmayı seçmek için **Ekle** seçeneklerinden birini seçin. Azure depolama ayrıntılarını ve hesap anahtarınızı sağlamanız gerekir.

    3. Birleştirilecek tüm dosyalar eklendikten sonra birleştirme işlemini gerçekleştirmek için **Tamam** ' ı tıklatın.

    4. Birleştirilmiş dosya SSMS 'de açılır; burada bunu görüntüleyebilir ve analiz edebilir, ayrıca bir XEL veya CSV dosyasına ya da bir tabloya dışarı aktarabilirsiniz.

- Power BI kullanın. Power BI ' de denetim günlüğü verilerini görüntüleyebilir ve analiz edebilirsiniz. Daha fazla bilgi edinmek ve indirilebilir bir şablona erişmek için bkz. [Power BI denetim günlüğü verilerini çözümleme](https://blogs.msdn.microsoft.com/azuresqldbsupport/20../../sql-azure-blob-auditing-basic-power-bi-dashboard/).
- Azure Storage blob kapsayıcısından Portal aracılığıyla veya [Azure Depolama Gezgini](https://storageexplorer.com/)gibi bir araç kullanarak günlük dosyalarını indirin.
  - Bir günlük dosyasını yerel olarak indirdikten sonra SSMS 'de günlükleri açmak, görüntülemek ve analiz etmek için dosyaya çift tıklayın.
  - Birden çok dosyayı aynı anda Azure Depolama Gezgini aracılığıyla da indirebilirsiniz. Bunu yapmak için belirli bir alt klasöre sağ tıklayın ve **farklı kaydet** ' i seçerek yerel bir klasöre kaydedin.

- Ek Yöntemler:

  - Birden çok dosya veya günlük dosyası içeren bir alt klasör indirdikten sonra, daha önce açıklanan SSMS birleştirme denetim dosyaları yönergeleri bölümünde açıklandığı gibi yerel olarak birleştirebilirsiniz.
  - Blob denetim günlüklerini programlı olarak görüntüle:

    - PowerShell kullanarak [genişletilmiş olaylar dosyalarını sorgulayın](https://sqlscope.wordpress.com/20../../reading-extended-event-files-using-client-side-tools-only/) .

## <a id="subheading-5"></a>Üretim uygulamaları

<!--The description in this section refers to preceding screen captures.-->

### <a id="subheading-6">Coğrafi olarak çoğaltılan veritabanlarını denetleme</a>

Coğrafi olarak çoğaltılan veritabanları ile birincil veritabanında denetimi etkinleştirdiğinizde, ikincil veritabanı aynı denetim ilkesine sahip olur. Birincil veritabanından bağımsız olarak **ikincil sunucuda**denetimi etkinleştirerek ikincil veritabanında denetim kurmak mümkündür.

- Sunucu düzeyi (**önerilir**): hem **birincil sunucuda** hem de **ikincil sunucuda** denetimi açın-birincil ve ikincil veritabanlarının her biri kendi sunucu düzeyi ilkeye göre ayrı olarak denetlenir.
- Veritabanı düzeyi: ikincil veritabanları için veritabanı düzeyinde denetim, yalnızca birincil veritabanı denetim ayarlarından yapılandırılabilir.
  - Sunucuda değil, *birincil veritabanının kendisinde*denetim etkinleştirilmiş olmalıdır.
  - Birincil veritabanında denetim etkinleştirildikten sonra, ikincil veritabanında da etkin hale gelir.

    >[!IMPORTANT]
    >Veritabanı düzeyinde denetim sayesinde, ikincil veritabanı için depolama ayarları, birincil veritabanıyla aynı olur ve bölgesel trafiğe neden olur. Yalnızca sunucu düzeyinde denetimi etkinleştirmenizi ve veritabanı düzeyinde denetimi tüm veritabanları için devre dışı bırakmayı öneririz.

### <a id="subheading-6">Depolama anahtarı yeniden oluşturma</a>

Üretimde, depolama anahtarlarınızı düzenli aralıklarla yenilemeniz olasıdır. Azure depolama 'ya denetim günlükleri yazarken, anahtarlarınızı yenilerken denetim ilkenizi yeniden kaydetmeniz gerekir. İşlem aşağıdaki gibidir:

1. **Depolama ayrıntılarını**açın. **Depolama erişim anahtarı** kutusunda **İkincil**' ı seçin ve **Tamam**' a tıklayın. Sonra Denetim yapılandırma sayfasının en üstündeki **Kaydet** ' e tıklayın.

    ![Gezinti bölmesi][5]
2. Depolama yapılandırması sayfasına gidin ve birincil erişim tuşunu yeniden oluşturun.

    ![Gezinti bölmesi][6]
3. Denetim yapılandırması sayfasına dönün, depolama erişim anahtarını ikincilden birinciye geçirin ve ardından **Tamam**' a tıklayın. Sonra Denetim yapılandırma sayfasının en üstündeki **Kaydet** ' e tıklayın.
4. Depolama yapılandırması sayfasına dönün ve ikincil erişim anahtarını yeniden oluşturun (bir sonraki anahtarın yenileme döngüsüne hazırlanın).

## <a name="additional-information"></a>Ek Bilgi

- Günlük biçimi, depolama klasörü ve adlandırma kurallarının hiyerarşisi hakkında daha fazla bilgi için bkz. [BLOB denetim günlüğü biçim başvurusu](https://go.microsoft.com/fwlink/?linkid=829599).

    > [!IMPORTANT]
    > Azure SQL veritabanı denetimi bir denetim kaydındaki karakter alanları için 4000 karakter veri depolar. Denetlenebilir bir eylemden döndürülen **ifade** veya **data_sensitivity_information** değerleri 4000 'den fazla karakter içeriyorsa, ilk 4000 karakterden sonraki tüm veriler **kesilir ve denetlenmeyecektir**.

- Azure aboneliğinizdeki bir Azure Blob depolama alanındaki **BLOB 'Ları eklemek** için denetim günlükleri yazılır:
  - **Premium Depolama** Şu anda ekleme Blobları tarafından **desteklenmiyor** .
  - **VNET 'Teki depolama** Şu anda **desteklenmiyor**.

- Varsayılan denetim ilkesi, tüm eylemleri ve aşağıdaki eylem grubu kümesini içerir. Bu, veritabanına göre yürütülen tüm sorguları ve saklı yordamları ve başarılı ve başarısız oturum açma işlemlerini denetler:

    BATCH_COMPLETED_GROUP<br>
    SUCCESSFUL_DATABASE_AUTHENTICATION_GROUP<br>
    FAILED_DATABASE_AUTHENTICATION_GROUP

    [Azure PowerShell kullanarak SQL veritabanı denetimini yönetme](#subheading-7) bölümünde açıklandığı gibi, PowerShell kullanarak farklı türlerde eylemler ve eylem grupları için denetimi yapılandırabilirsiniz.

- AAD kimlik doğrulaması kullanılırken, başarısız oturum açma kayıtları SQL denetim *günlüğünde görünmez.* Başarısız oturum açma denetim kayıtlarını görüntülemek için, bu olayların ayrıntılarını günlüğe kaydeden [Azure Active Directory portalını]( ../active-directory/reports-monitoring/reference-sign-ins-error-codes.md)ziyaret etmeniz gerekir.


## <a id="subheading-7"></a>Azure PowerShell kullanarak SQL veritabanı denetimini yönetme

**PowerShell cmdlet 'leri (ek filtreleme IÇIN WHERE yan tümcesi desteği dahil)** :

- [Veritabanı denetim Ilkesi oluştur veya güncelleştir (set-AzSqlDatabaseAudit)](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabaseaudit)
- [Sunucu denetim Ilkesi oluştur veya güncelleştir (set-AzSqlServerAudit)](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlserveraudit)
- [Veritabanı denetim Ilkesini al (Get-AzSqlDatabaseAudit)](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaseaudit)
- [Sunucu denetim Ilkesini al (Get-AzSqlServerAudit)](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlserveraudit)
- [Veritabanı denetim Ilkesini Kaldır (Remove-AzSqlDatabaseAudit)](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabaseaudit)
- [Sunucu denetim Ilkesini Kaldır (Remove-AzSqlServerAudit)](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlserveraudit)

Betik örneği için bkz. [PowerShell kullanarak denetim ve tehdit algılamayı yapılandırma](scripts/sql-database-auditing-and-threat-detection-powershell.md).

## <a id="subheading-9"></a>REST API kullanarak SQL veritabanı denetimini yönetme

**REST API**:

- [Veritabanı denetim Ilkesi oluştur veya güncelleştir](https://docs.microsoft.com/rest/api/sql/database%20auditing%20settings/createorupdate)
- [Sunucu denetim Ilkesi oluştur veya güncelleştir](https://docs.microsoft.com/rest/api/sql/server%20auditing%20settings/createorupdate)
- [Veritabanı denetim Ilkesini al](https://docs.microsoft.com/rest/api/sql/database%20auditing%20settings/get)
- [Sunucu denetim Ilkesini al](https://docs.microsoft.com/rest/api/sql/server%20auditing%20settings/get)

Ek filtreleme için WHERE yan tümcesi desteğiyle genişletilmiş ilke:

- [Veritabanı *genişletilmiş* Denetim İlkesi Oluştur veya güncelleştir](https://docs.microsoft.com/rest/api/sql/database%20extended%20auditing%20settings/createorupdate)
- [Sunucu *genişletilmiş* Denetim İlkesi Oluştur veya güncelleştir](https://docs.microsoft.com/rest/api/sql/server%20auditing%20settings/createorupdate)
- [Veritabanı *genişletilmiş* denetim ilkesini al](https://docs.microsoft.com/rest/api/sql/database%20extended%20auditing%20settings/get)
- [Sunucu *genişletilmiş* denetim ilkesini al](https://docs.microsoft.com/rest/api/sql/server%20auditing%20settings/get)

## <a id="subheading-10"></a>Azure Resource Manager şablonları kullanarak SQL veritabanı denetimini yönetme

[Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) şablonları kullanarak Azure SQL veritabanı denetimini aşağıdaki örneklerde gösterildiği gibi yönetebilirsiniz:

- [Denetim günlüklerini Azure Blob depolama hesabına yazmak için etkinleştirilen bir Azure SQL Server dağıtma](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-blob-storage)
- [Denetim günlüklerini Log Analytics yazmak için etkin bir Azure SQL Server dağıtın](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-oms)
- [Denetim günlüklerini Event Hubs yazmak için etkin bir Azure SQL Server dağıtın](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-eventhub)

> [!NOTE]
> Bağlantılı örnekler bir dış ortak depodadır ve ' olduğu gibi ', garanti olmadan ve herhangi bir Microsoft destek programı/hizmeti kapsamında desteklenmez.

<!--Anchors-->
[Azure SQL Database Auditing overview]: #subheading-1
[Set up auditing for your database]: #subheading-2
[Analyze audit logs and reports]: #subheading-3
[Practices for usage in production]: #subheading-5
[Storage Key Regeneration]: #subheading-6
[Manage SQL database auditing using Azure PowerShell]: #subheading-7
[Blob/Table differences in Server auditing policy inheritance]: (#subheading-8)
[Manage SQL database auditing using REST API]: #subheading-9
[Manage SQL database auditing using ARM templates]: #subheading-10

<!--Image references-->
[1]: ./media/sql-database-auditing-get-started/1_auditing_get_started_settings.png
[2]: ./media/sql-database-auditing-get-started/2_auditing_get_started_server_inherit.png
[3]: ./media/sql-database-auditing-get-started/3_auditing_get_started_turn_on.png
[4]: ./media/sql-database-auditing-get-started/4_auditing_get_started_storage_details.png
[5]: ./media/sql-database-auditing-get-started/5_auditing_get_started_storage_key_regeneration.png
[6]: ./media/sql-database-auditing-get-started/6_auditing_get_started_regenerate_key.png
[7]: ./media/sql-database-auditing-get-started/7_auditing_get_started_blob_view_audit_logs.png
[8]: ./media/sql-database-auditing-get-started/8_auditing_get_started_blob_audit_records.png
[9]: ./media/sql-database-auditing-get-started/9_auditing_get_started_ssms_1.png
[10]: ./media/sql-database-auditing-get-started/10_auditing_get_started_ssms_2.png
