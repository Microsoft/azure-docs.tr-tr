---
title: "SSIS tümleştirme çalışma zamanı 'nda paket yürütmeye sorun giderme "
description: Bu makale, SSIS tümleştirme çalışma zamanı 'nda SSIS paketi yürütme sorunlarını giderme kılavuzu sağlar
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/15/2019
author: wenjiefu
ms.author: wenjiefu
ms.reviewer: sawinark
manager: craigg
ms.openlocfilehash: de90614d9d24b3f57cb170e9a590de859f689331
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73683992"
---
# <a name="troubleshoot-package-execution-in-the-ssis-integration-runtime"></a>SSIS tümleştirme çalışma zamanı 'nda paket yürütmeye sorun giderme

Bu makale, SSIS tümleştirme çalışma zamanı 'nda SQL Server Integration Services (SSIS) paketlerini yürütürken bulabileceğiniz en yaygın hataları içerir. Hataları çözmeye yönelik olası nedenleri ve eylemleri açıklar.

## <a name="where-to-find-logs-for-troubleshooting"></a>Sorun giderme için günlüklerin bulunacağı yer

SSIS paketi yürütme etkinliğinin çıkışını denetlemek için Azure Data Factory portalını kullanın. Çıktı, yürütme sonucunu, hata iletilerini ve işlem KIMLIĞINI içerir. Ayrıntılar için bkz. işlem hattını [izleme](how-to-invoke-ssis-package-ssis-activity.md#monitor-the-pipeline).

Yürütmenin ayrıntı günlüklerini denetlemek için SSIS kataloğunu (SSıSDB) kullanın. Ayrıntılar için bkz. [çalışan paketleri ve diğer Işlemleri izleme](https://docs.microsoft.com/sql/integration-services/performance/monitor-running-packages-and-other-operations?view=sql-server-2017).

## <a name="common-errors-causes-and-solutions"></a>Yaygın hatalar, nedenleri ve çözümleri

### <a name="error-message-connection-timeout-expired-or-the-service-has-encountered-an-error-processing-your-request-please-try-again"></a>Hata iletisi: "bağlantı zaman aşımı süresi doldu" veya "hizmet isteğinizi işlerken bir hatayla karşılaştı. Lütfen yeniden deneyin. "

Olası nedenler ve önerilen eylemler şunlardır:
* Veri kaynağı veya hedef aşırı yüklendi. Veri kaynağınızdaki veya hedefteki yükü denetleyin ve yeterli kapasiteye sahip olup olmadığını görün. Örneğin, Azure SQL veritabanı 'nı kullandıysanız, veritabanının zaman aşımına uğrar olması durumunda ölçeklendirmeyi değerlendirin.
* SSIS tümleştirme çalışma zamanı ile veri kaynağı veya hedef arasındaki ağ, özellikle bağlantı çapraz bölgedeyse ve şirket içi ile Azure arasında kararsız hale geldi. Aşağıdaki adımları izleyerek, SSIS paketine yeniden deneme modelini uygulayın:
  * SSIS paketlerinizin, yan etkileri olmadan hata durumunda yeniden çalıştığından emin olun (örneğin, veri kaybı veya veri çoğaltma).
  * **Genel** SEKMESINDE, **SSIS paketi yürütme** etkinliğinin **yeniden denenme** ve **yeniden deneme aralığını** yapılandırın. ![Genel sekmesindeki özellikleri ayarlayın](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)
  * Bir ADO.NET ve OLE DB kaynak veya hedef bileşeni için SSIS paketi veya SSIS etkinliğinde bağlantı Yöneticisi 'nde **ConnectRetryCount** ve **ConnectRetryInterval** ayarını yapın.

### <a name="error-message-ado-net-source-has-failed-to-acquire-the-connection--with-a-network-related-or-instance-specific-error-occurred-while-establishing-a-connection-to-sql-server-the-server-was-not-found-or-was-not-accessible"></a>Hata iletisi: "ADO ağ kaynağı bağlantıyı alamadı '... '" "SQL Server bağlantı kurulurken ağla ilgili veya örneğe özgü bir hata oluştu. Sunucu bulunamadı veya erişilebilir durumda değil. "

Bu sorun genellikle veri kaynağına veya hedefe, SSIS tümleştirme çalışma zamanından erişilemediği anlamına gelir. Nedenler farklılık gösterebilir. Şu eylemleri deneyin:
* Veri kaynağını veya hedef adını/IP 'yi doğru geçirdiğinizden emin olun.
* Güvenlik duvarının doğru ayarlandığından emin olun.
* Veri kaynağınız veya hedefi şirket içi ise sanal ağınızın düzgün şekilde yapılandırıldığından emin olun:
  * Aynı sanal ağ içinde bir Azure VM sağlayarak sorunun sanal ağ yapılandırmasından olup olmadığını doğrulayabilirsiniz. Ardından, veri kaynağına veya hedefine Azure VM 'den erişip erişemeyeceğini kontrol edin.
  * Bir [Azure-SSIS tümleştirme çalışma zamanına bir sanal ağa katılarak](join-azure-ssis-integration-runtime-virtual-network.md)bir SSIS tümleştirme çalışma zamanı ile sanal ağ kullanma hakkında daha fazla bilgi edinebilirsiniz.

### <a name="error-message-ado-net-source-has-failed-to-acquire-the-connection--with-could-not-create-a-managed-connection-manager"></a>Hata iletisi: "ADO ağ kaynağı bağlantıyı alamadı '... '" "yönetilen bağlantı Yöneticisi oluşturulamadı."

Olası nedeni, pakette kullanılan ADO.NET sağlayıcısının SSIS tümleştirme çalışma zamanına yüklenmesidir. Sağlayıcıyı özel bir kurulum kullanarak yükleyebilirsiniz. [Azure-SSIS tümleştirme çalışma zamanı için kurulumu Özelleştir](how-to-configure-azure-ssis-ir-custom-setup.md)bölümünde özel kurulumla ilgili daha fazla ayrıntı bulabilirsiniz.

### <a name="error-message-the-connection--is-not-found"></a>Hata iletisi: "bağlantı '... ' bulunamadı "

Daha eski SQL Server Management Studio (SSMS) sürümlerindeki bilinen bir sorun bu hataya neden olabilir. Paket, dağıtımı yapmak için SQL Server Management Studio’nun kullanıldığı makinede yüklü olmayan özel bir bileşen (örneğin, SQL Server Integration Services Azure Özellik Paketi veya iş ortağı bileşenleri) içeriyorsa SQL Server Management Studio, bileşeni kaldırır ve hataya neden olur. [SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)'yi sorunun düzeltildiği en son sürüme yükseltin.

### <a name="error-messagessis-executor-exit-code--1073741819"></a>Hata iletisi: "SSIS yürütücü çıkış kodu:-1073741819."

* Olası neden ve önerilen eylem:
  * Birden çok iş parçacığında paralel olarak birden fazla Excel kaynağı veya hedefi çalıştırıldığında, bu hata Excel kaynağı ve hedefi kısıtlamasından dolayı olabilir. Excel bileşenlerinizi sırayla yürütmek üzere değiştirerek veya bunları farklı paketlere ayırarak ve ExecuteOutOfProcess özelliği true olarak ayarlanmış şekilde "paket görevini Yürüt" aracılığıyla tetikleyerek bu sınırlamaya geçici çözüm verebilirsiniz.

### <a name="error-message-there-is-not-enough-space-on-the-disk"></a>Hata iletisi: "diskte yeterli alan yok"

Bu hata, yerel diskin SSIS tümleştirme çalışma zamanı düğümünde kullanıldığı anlamına gelir. Paketinizin veya özel kurulumun çok miktarda disk alanı kullanıp kullanmadığını denetleyin:
* Disk paketiniz tarafından tüketilmişse, paket yürütme tamamlandıktan sonra serbest bırakılır.
* Disk özel kurulumlarınız tarafından tüketilmişse, SSIS tümleştirme çalışma zamanını durdurmanız, komut dosyanızı değiştirmeniz ve tümleştirme çalışma zamanını yeniden başlatmanız gerekir. Özel kurulum için belirttiğiniz tüm Azure Blob kapsayıcısı SSIS tümleştirme çalışma zamanı düğümüne kopyalanır, bu nedenle o kapsayıcı altında gereksiz içerik olup olmadığını kontrol edin.

### <a name="error-message-failed-to-retrieve-resource-from-master-microsoftsqlserverintegrationservicesscalescaleoutcontractcommonmasterresponsefailedexception-code300004-descriptionload-file--failed"></a>Hata iletisi: "ana sunucudan kaynak alınamadı. Microsoft. SqlServer. IntegrationServices. Scale. ScaleoutContract. Common. MasterResponseFailedException: Code: 300004. Açıklama: "* * *" dosyasını yükleme başarısız. "

* Olası neden ve önerilen eylem:
  * SSIS etkinliği dosya sisteminden (paket dosyası veya proje dosyası) paketi yürütüp, bu hata oluşur çünkü proje, paket veya yapılandırma dosyasına SSIS etkinliğinde verdiğiniz paket erişim kimlik bilgileri ile erişilebilir değilse
    * Azure dosyası kullanıyorsanız:
      * Dosya yolu \\\\\<depolama hesabı adı\>. file.core.windows.net\\\<dosya paylaşma yolu ile başlamalıdır\>
      * Etki alanı "Azure" olmalıdır
      * Kullanıcı adının \<depolama hesabı adı olması gerekir\>
      * Parola \<depolama erişim anahtarı olmalıdır\>
    * Şirket içi dosya kullanıyorsanız, Azure-SSIS tümleştirme çalışma zamanının şirket içi dosya paylaşımınıza erişebilmesi için lütfen VNet, paket erişimi kimlik bilgileri ve izninin doğru yapılandırılıp yapılandırılmadığını denetleyin.

### <a name="error-message-the-file-name--specified-in-the-connection-was-not-valid"></a>Hata iletisi: "dosya adı '... ' bağlantıda belirtilen geçerli değildi "

* Olası neden ve önerilen eylem:
  * Geçersiz bir dosya adı belirtildi
  * Bağlantı yöneticinizin kısa süre yerine FQDN 'yi (tam etki alanı adı) kullandığınızdan emin olun

### <a name="error-message-cannot-open-file-"></a>Hata iletisi: "dosya açılamıyor '... '"

Bu hata, paket yürütmesi SSIS tümleştirme çalışma zamanı 'nda yerel diskte bir dosya bulamadığında oluşur. Şu eylemleri deneyin:
* SSIS tümleştirme çalışma zamanı 'nda yürütülen pakette mutlak yolu kullanmayın. Geçerli yürütme çalışma dizinini (.) veya Temp klasörünü (% TEMP%) kullanın yerine.
* Bazı dosyaları SSIS tümleştirme çalışma zamanı düğümlerinde kalıcı hale getirmeniz gerekiyorsa, dosyaları [kurulumu Özelleştir](how-to-configure-azure-ssis-ir-custom-setup.md)bölümünde açıklandığı gibi hazırlayın. Çalışma dizinindeki tüm dosyalar yürütme tamamlandıktan sonra temizlenir.
* Dosyayı SSIS tümleştirme çalışma zamanı düğümünde depolamak yerine Azure dosyaları 'nı kullanın. Ayrıntılar için bkz. [Azure dosya paylaşımlarını kullanma](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-files-file-shares?view=sql-server-2017#use-azure-file-shares).

### <a name="error-message-the-database-ssisdb-has-reached-its-size-quota"></a>Hata iletisi: "' SSSıSDB ' veritabanı boyut kotasına ulaştı"

Bunun olası bir nedeni, Azure SQL veritabanında oluşturulan SSISDB veritabanının veya siz SSIS tümleştirme çalışma zamanını oluştururken yönetilen bir örneğin kotasına ulaşması olabilir. Şu eylemleri deneyin:
* Veritabanınızın DTU değerini artırın. [Azure SQL Veritabanı sunucusu için SQL Veritabanı kaynağı sınırları](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-logical-server) bölümünde ayrıntıları bulabilirsiniz.
* Paketinizin çok sayıda günlük oluşturup oluşturmayacağını denetleyin. Bu durumda, bu günlükleri temizlemek için elastik bir iş yapılandırabilirsiniz. Ayrıntılar için bkz. [Azure Elastik Veritabanı işleri ile SSISDB günlüklerini temizleme](how-to-clean-up-ssisdb-logs-with-elastic-jobs.md).

### <a name="error-message-the-request-limit-for-the-database-is--and-has-been-reached"></a>Hata iletisi: "veritabanı için istek sınırı... ve bu sınıra ulaşıldı. "

SSIS tümleştirme çalışma zamanı 'nda çok sayıda paket paralel çalışıyorsa, SSıSDB 'nin istek sınırına ulaştığından bu hata ortaya çıkabilir. Bu sorunu çözmek için SSıSDB DTC 'YI artırmayı düşünün. [Azure SQL Veritabanı sunucusu için SQL Veritabanı kaynağı sınırları](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-logical-server) bölümünde ayrıntıları bulabilirsiniz.

### <a name="error-message-ssis-operation-failed-with-unexpected-operation-status-"></a>Hata iletisi: "SSIS Işlemi beklenmeyen işlem durumuyla başarısız oldu:..."

Hata genellikle geçici bir sorundan kaynaklanır, bu nedenle paket yürütmeyi yeniden çalıştırmayı deneyin. Aşağıdaki adımları izleyerek, SSIS paketine yeniden deneme modelini uygulayın:

* SSIS paketlerinizin, yan etkileri olmadan hata durumunda yeniden çalıştığından emin olun (örneğin, veri kaybı veya veri çoğaltma).
* **Genel** SEKMESINDE, **SSIS paketi yürütme** etkinliğinin **yeniden denenme** ve **yeniden deneme aralığını** yapılandırın. ![Genel sekmesindeki özellikleri ayarlayın](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)
* Bir ADO.NET ve OLE DB kaynak veya hedef bileşeni için SSIS paketi veya SSIS etkinliğinde bağlantı Yöneticisi 'nde **ConnectRetryCount** ve **ConnectRetryInterval** ayarını yapın.

### <a name="error-message-there-is-no-active-worker"></a>Hata iletisi: "etkin çalışan yok."

Bu hata genellikle SSIS tümleştirme çalışma zamanının sağlıksız bir duruma sahip olduğu anlamına gelir. Durum ve ayrıntılı hatalar için Azure portal denetleyin. Daha fazla bilgi için bkz. [Azure-SSIS tümleştirme çalışma zamanı](https://docs.microsoft.com/azure/data-factory/monitor-integration-runtime#azure-ssis-integration-runtime).

### <a name="error-message-your-integration-runtime-cannot-be-upgraded-and-will-eventually-stop-working-since-we-cannot-access-the-azure-blob-container-you-provided-for-custom-setup"></a>Hata iletisi: "özel kurulum için verdiğiniz Azure Blob kapsayıcısına erişemedik, tümleştirme çalışma zamanı yükseltilemiyor ve sonunda çalışmayı durduracak."

Bu hata, SSIS tümleştirme çalışma zamanı özel kurulum için yapılandırılmış depolamaya erişemediğinde oluşur. Belirttiğiniz paylaşılan erişim imzası (SAS) URI 'sinin geçerli olup olmadığını ve süresinin dolmadığını denetleyin.

### <a name="error-message-microsoft-ole-db-provider-for-analysis-services-hresult-0x80004005-description-com-error-com-error-mscorlib-exception-has-been-thrown-by-the-target-of-an-invocation"></a>Hata iletisi: "Microsoft OLE DB sağlayıcısı Analysis Services. ' HRESULT: 0x80004005 Description: ' COM hatası: COM hatası: mscorlib; Bir çağrının hedefi tarafından özel durum oluşturuldu "

Olası bir neden, Azure Multi-Factor Authentication etkinleştirilen kullanıcı adının veya parolanın Azure Analysis Services kimlik doğrulaması için yapılandırılmış olması olabilir. Bu kimlik doğrulaması, SSIS tümleştirme çalışma zamanı 'nda desteklenmez. Azure Analysis Services kimlik doğrulaması için bir hizmet sorumlusu kullanmayı deneyin:

1. Hizmet sorumlusu [Ile Otomasyon](https://docs.microsoft.com/azure/analysis-services/analysis-services-service-principal)bölümünde açıklandığı gibi bir hizmet sorumlusu hazırlayın.
2. Bağlantı Yöneticisi 'nde **belirli bir Kullanıcı adı ve parola kullan**: **AppID** 'yi parola olarak Kullanıcı adı ve **ClientSecret** olarak ayarlayın.

### <a name="error-message-adonet-source-has-failed-to-acquire-the-connection-guid-with-the-following-error-message-login-failed-for-user-nt-authorityanonymous-logon-when-using-a-managed-identity"></a>Hata iletisi: "ADONET Source şu hata iletisiyle {GUID} bağlantısını alamadı: yönetilen bir kimlik kullanırken Kullanıcı ' NT AUTHORıTY\ANONYMOUS LOGON ' için oturum açma başarısız oldu

Bağlantı Yöneticisi 'nin kimlik doğrulama yöntemini, *Connectusingmanagedıdentity* parametresi **true**olduğunda **Active Directory parola kimlik doğrulaması** olarak yapılandırmadığınızdan emin olun. Bunun yerine **SQL kimlik doğrulaması** olarak yapılandırabilirsiniz, bu, *Connectusingmanagedıdentity* ayarlandıysa yok sayılır.

### <a name="error-message-0xc020801f-at--odata-source--cannot-acquire-a-managed-connection-from-the-run-time-connection-manager"></a>Hata iletisi: "0Xcc801f,..., OData kaynağı [...]: çalışma zamanı bağlantı Yöneticisi 'nden yönetilen bağlantı alınamıyor"

Olası bir neden, Aktarım Katmanı Güvenliği 'nin (TLS), OData kaynağınız için gereken SSIS tümleştirme çalışma zamanı ' nda etkinleştirilmesidir. SSIS tümleştirme çalışma zamanı ' nda TLS 'yi Özelleştir ' i kullanarak TLS 'yi etkinleştirebilirsiniz. ' De daha fazla ayrıntı, [SSIS 'Den Project Online OData 'e bağlanamamalıdır](https://docs.microsoft.com/office365/troubleshoot/cant-connect-project-online-odata-from-ssis) ve [Azure-SSIS tümleştirme çalışma zamanı için kurulumu özelleştirebilir](how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="error-message-request-staging-task-with-operation-guid--fail-since-error-failed-to-dispatch-staging-operation-with-error-message-microsoftsqlserverintegrationservicesaisagentcoreaisagentexception-failed-to-load-data-proxy"></a>Hata iletisi: "işlem GUID 'si ile hazırlama görevi ıste... hata nedeniyle başarısız oldu: hazırlama işlemi şu hata iletisiyle gönderilemedi: Microsoft. SqlServer. IntegrationServices. AisAgentCore. AisAgentException: veri proxy 'si yüklenemedi. "

Azure-SSIS tümleştirme çalışma zamanının kendi kendine barındırılan tümleştirme çalışma zamanı ile yapılandırıldığından emin olun. [ADF 'de Azure-SSIS IR Için otomatik olarak BARıNDıRıLAN IR 'yi proxy olarak yapılandırma bölümünde](self-hosted-integration-runtime-proxy-ssis.md)daha ayrıntılı bilgi bulabilirsiniz.

### <a name="error-message-staging-task-status-failed-staging-task-error-errorcode-2010-errormessage-the-self-hosted-integration-runtime--is-offline"></a>Hata iletisi: "hazırlama görevi durumu: başarısız. Hazırlama görevi hatası: ErrorCode: 2010, ErrorMessage: şirket içinde barındırılan Integration Runtime... Çevrimdışı "

Şirket içinde barındırılan tümleştirme çalışma zamanının yüklü ve başlatılmış olduğundan emin olun. [Şirket içinde barındırılan tümleştirme çalışma zamanı oluşturma ve yapılandırma](create-self-hosted-integration-runtime.md) hakkında daha ayrıntılı bilgi bulabilirsiniz

### <a name="error-message-staging-task-error-errorcode-2906-errormessage-package-execution-failed-output-operationerrormessages-error-the-requested-ole-db-provider--is-not-registered-if-the-64-bit-driver-is-not-installed-run-the-package-in-32-bit-mode"></a>Hata iletisi: "hazırlama görevi hatası: ErrorCode: 2906, ErrorMessage: paket yürütülemedi., çıkış: {" OperationErrorMessages ":" hata: istenen OLE DB sağlayıcı... kayıtlı değil. 64 bitlik sürücü yüklü değilse, paketi 32 bit modda çalıştırın... "

Paketinizdeki OLE DB bağlayıcılarınız tarafından kullanılan karşılık gelen sağlayıcının şirket içinde barındırılan tümleştirme çalışma zamanı makinesine doğru bir şekilde yüklendiğinden emin olun. [ADF 'de Azure-SSIS IR Için otomatik olarak BARıNDıRıLAN IR 'yi proxy olarak yapılandırma bölümünde](self-hosted-integration-runtime-proxy-ssis.md#prepare-self-hosted-ir) daha ayrıntılı bilgi bulabilirsiniz

### <a name="error-message-staging-task-error-errorcode-2906-errormessage-package-execution-failed-output-operationerrormessages-error-systemiofileloadexception-could-not-load-file-or-assembly-microsoftwindowsazurestorage-version-cultureneutral-publickeytoken31bf3856ad364e35-or-one-of-its-dependencies-the-located-assemblys-manifest-definition-does-not-match-the-assembly-reference"></a>Hata iletisi: "hazırlama görevi hatası: ErrorCode: 2906, ErrorMessage: paket yürütülemedi., çıkış: {" OperationErrorMessages ":" hata: System. ıO. FileLoadException: dosya veya derleme ' Microsoft. WindowsAzure. Storage, Version =... Culture = neutral, PublicKeyToken = 31bf3856ad364e35 ' veya bağımlılıklarından biri. Konumlandırılan derlemenin bildirim tanımı bütünleştirilmiş kod başvurusuyla eşleşmiyor. ' ..."

Bunun olası nedenlerinden biri, şirket içinde barındırılan tümleştirme çalışma zamanının düzgün şekilde yüklenmediğini veya yükseltilmesidir. En son şirket içinde barındırılan tümleştirme çalışma zamanını indirip yeniden yüklemeyi önerin. [Şirket içinde barındırılan tümleştirme çalışma zamanı oluşturma ve yapılandırma](create-self-hosted-integration-runtime.md#installation-best-practices) hakkında daha ayrıntılı bilgi bulabilirsiniz

### <a name="error-message-a-connection-is-required-when-requesting-metadata-if-you-are-working-offline-uncheck-work-offline-on-the-ssis-menu-to-enable-the-connection"></a>Hata iletisi: "meta veri istenirken bir bağlantı gereklidir. Çevrimdışı çalışıyorsanız, bağlantıyı etkinleştirmek için SSIS menüsünde Çevrimdışı çalış seçeneğinin işaretini kaldırın "

* Olası neden ve önerilen eylem:
  * Aynı zamanda bir uyarı mesajı varsa "Bileşen, yürütme günlüğünde" ConnectByProxy değeri ile bağlantı Yöneticisi 'ni kullanmayı desteklemez "olarak, bu, bir bağlantı yöneticisinin" ConnectByProxy "henüz desteklenmeyen bir bileşende kullanıldığı anlamına gelir. Desteklenen bileşenler, [ADF 'de Azure-SSIS IR Için otomatik olarak BARıNDıRıLAN IR 'yi proxy olarak yapılandırma bölümünde](self-hosted-integration-runtime-proxy-ssis.md#enable-ssis-packages-to-connect-by-proxy) bulunabilir
  * Yürütme günlüğü [SSMS raporunda](https://docs.microsoft.com/sql/integration-services/performance/monitor-running-packages-and-other-operations?view=sql-server-2017#reports) veya SSIS paketi yürütme etkinliğinde belirttiğiniz günlük klasöründe bulunabilir.
  * vNet Ayrıca, şirket içi verilere alternatif olarak erişmek için de kullanılabilir. [Azure-SSIS tümleştirme çalışma zamanına bir sanal ağa ekleme](join-azure-ssis-integration-runtime-virtual-network.md) konusunda daha ayrıntılı bilgi bulabilirsiniz

### <a name="error-message-staging-task-status-failed-staging-task-error-errorcode-2906-errormessage-package-execution-failed-output-operationerrormessages-ssis-executor-exit-code--1n-loglocation-ssistelemetryexecutionlog-effectiveintegrationruntime--executionduration--durationinqueue--integrationruntimequeue--"></a>Hata iletisi: "hazırlama görevi durumu: başarısız. Hazırlama görevi hatası: ErrorCode: 2906, ErrorMessage: paket yürütülemedi., çıkış: {"OperationErrorMessages": "SSIS yürütücüsü çıkış kodu:-1. \ n", "LogLocation": "...\\SSISTelemetry\\ExecutionLog\\...", " efektte ıntegrationruntime ":"... "," executionDuration ":...," durationInQueue ": {" ıntegrationruntimequeue ":...}}"

Visual C++ Runtime 'ın şirket içinde barındırılan tümleştirme çalışma zamanı makinesinde yüklü olduğundan emin olun. [ADF 'de Azure-SSIS IR Için otomatik olarak BARıNDıRıLAN IR 'yi proxy olarak yapılandırma bölümünde](self-hosted-integration-runtime-proxy-ssis.md#prepare-self-hosted-ir) daha ayrıntılı bilgi bulabilirsiniz

### <a name="multiple-package-executions-are-triggered-unexpectedly"></a>Birden çok paket yürütmesi beklenmedik şekilde tetikleniyor

* Olası neden ve önerilen eylem:
  * ADF saklı yordam etkinliği veya arama etkinliği, SSIS paketi yürütmesini tetiklemek için kullanılır. T-SQL komutu geçici bir sorunla karşılaşabilir ve çoklu paket yürütmelerinin oluşmasına neden olacak şekilde yeniden çalıştırma tetikleyebilir.
  * Kullanıcı yeniden deneme sayısını etkinlikte bir şekilde ayarlamadığınız takdirde paket yürütmenin yeniden çalıştırılmamasını sağlayan Executessıspackage etkinliğini kullanın. Ayrıntı, [https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity) adresinde bulunabilir
  * Bir yürütmenin zaten tetiklenip tetiklenmeyeceğini denetleyerek, yeniden çalıştırmak için t-SQL komutunu daraltın

### <a name="package-execution-takes-too-long"></a>Paket yürütmesi çok uzun sürüyor

Olası nedenler ve önerilen eylemler şunlardır:

* SSIS tümleştirme çalışma zamanı üzerinde çok fazla paket yürütmesi zamanlandı. Tüm bu yürütmeler, kendi kapamaları için bir kuyrukta beklemeye alınır.
  * Bu formülü kullanarak en yüksek değeri belirle:

    IR başına en fazla paralel yürütme sayısı = düğüm sayısı * düğüm başına en fazla paralel yürütme
  * Düğüm sayısı ve düğüm başına en fazla paralel yürütme ayarlama hakkında bilgi edinmek için bkz. [Azure Data Factory bir Azure-SSIS tümleştirme çalışma zamanı oluşturma](create-azure-ssis-integration-runtime.md).
* SSIS tümleştirme çalışma zamanı durdurulur veya sağlıksız durumda olur. SSIS tümleştirme çalışma zamanı durumunu ve hatalarını denetleme hakkında bilgi edinmek için bkz. [Azure-SSIS tümleştirme çalışma zamanı](monitor-integration-runtime.md#azure-ssis-integration-runtime).

Ayrıca **Genel sekmesinde bir** zaman aşımı ayarlamanızı öneririz: ![](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)Genel sekmesindeki özellikleri ayarlayın.

### <a name="poor-performance-in-package-execution"></a>Paket yürütmede kötü performans

Şu eylemleri deneyin:

* SSIS tümleştirme çalışma zamanının veri kaynağı ve hedefle aynı bölgede olduğundan emin olun.

* Yürütme sırasında her bir bileşen için Duration bilgilerini toplamak üzere paket yürütmenin günlüğe kaydetme düzeyini **performans** olarak ayarlayın. Ayrıntılar için bkz. [Integration Services (SSIS) günlüğü](https://docs.microsoft.com/sql/integration-services/performance/integration-services-ssis-logging).

* Azure portal IR düğüm performansını kontrol edin:
  * SSIS tümleştirme çalışma zamanının nasıl izleneceği hakkında bilgi için bkz. [Azure-SSIS tümleştirme çalışma zamanı](monitor-integration-runtime.md#azure-ssis-integration-runtime).
  * Azure portal veri fabrikasının ölçümlerini görüntüleyerek SSIS tümleştirme çalışma zamanı için CPU/bellek geçmişini bulabilirsiniz.
    SSIS tümleştirme çalışma zamanının ![Izleme ölçümleri](media/ssis-integration-runtime-ssis-activity-faq/monitor-metrics-ssis-integration-runtime.png)
