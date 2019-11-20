---
title: Yönetilen örnek denetimi
description: T-SQL kullanarak Azure SQL veritabanı yönetilen örnek denetimi 'ni kullanmaya nasıl başlacağınızı öğrenin
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
f1_keywords:
- mi.azure.sqlaudit.general.f1
author: barmichal
ms.author: mibar
ms.reviewer: vanto
ms.date: 04/08/2019
ms.openlocfilehash: 41d632b145a7187dd0aeaab740cd8546a3955e7f
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819003"
---
# <a name="get-started-with-azure-sql-database-managed-instance-auditing"></a>Azure SQL veritabanı yönetilen örnek denetimi 'ni kullanmaya başlama

[Yönetilen örnek](sql-database-managed-instance.md) denetimi, veritabanı olaylarını izler ve bunları Azure Depolama hesabınızdaki bir denetim günlüğüne yazar. Ayrıca Denetim:

- Yönetmelikli uyumluluğu korumanıza, veritabanı etkinliklerini anlamanıza ve işle ilgili endişeleri veya şüpheli güvenlik ihlallerini gösterebilen tutarsızlıklar ve bozukluklar elde etmenize yardımcı olur.
- Uyumluluğu garanti etmez ancak uyumluluk standartlarına uygunluğunu sağlar ve kolaylaştırır. Standartlar uyumluluğunu destekleyen Azure programları hakkında daha fazla bilgi için, SQL veritabanı uyumluluk sertifikalarının en güncel listesini bulabileceğiniz [Azure Güven Merkezi](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) bakın.

## <a name="set-up-auditing-for-your-server-to-azure-storage"></a>Azure depolama 'ya sunucunuz için Denetim kurma

Aşağıdaki bölümde, yönetilen örneğiniz üzerinde denetim yapılandırması açıklanmaktadır.

1. [Azure Portal](https://portal.azure.com) gidin.
1. Denetim günlüklerinin depolandığı bir Azure depolama **kapsayıcısı** oluşturun.

   1. Denetim günlüklerinizi depolamak istediğiniz Azure depolama 'ya gidin.

      > [!IMPORTANT]
      > Bölgeler arası okuma/yazma işlemlerini önlemek için yönetilen örnekle aynı bölgedeki bir depolama hesabını kullanın.

   1. Depolama hesabında **Genel Bakış ' a** gidin ve **Bloblar**' a tıklayın.

      ![Azure Blob pencere öğesi](./media/sql-managed-instance-auditing/1_blobs_widget.png)

   1. Yeni bir kapsayıcı oluşturmak için üst menüsünde **+ kapsayıcı** ' ya tıklayın.

      ![Blob kapsayıcısı Oluştur simgesi](./media/sql-managed-instance-auditing/2_create_container_button.png)

   1. Bir kapsayıcı **adı**girin, ortak erişim düzeyini **özel**olarak ayarlayın ve ardından **Tamam**' a tıklayın.

      ![Blob kapsayıcı yapılandırması oluştur](./media/sql-managed-instance-auditing/3_create_container_config.png)

1. Denetim günlükleri için kapsayıcıyı oluşturduktan sonra, denetim günlüklerinin hedefi olarak yapılandırmak için iki yol vardır: [T-SQL kullanma](#blobtsql) veya [SQL Server Management Studio (SSMS) Kullanıcı arabirimini kullanma](#blobssms):

   - <a id="blobtsql"></a>T-SQL kullanarak denetim günlükleri için blog depolamayı yapılandırma:

     1. Kapsayıcılar listesinde, yeni oluşturulan kapsayıcıya ve ardından **kapsayıcı özellikleri**' ne tıklayın.

        ![Blob kapsayıcısı Özellikler düğmesi](./media/sql-managed-instance-auditing/4_container_properties_button.png)

     1. Kopyalama simgesine tıklayarak ve daha sonra kullanmak üzere URL 'YI (örneğin, Not defteri 'nde) kaydederek kapsayıcı URL 'sini kopyalayın. Kapsayıcı URL biçimi `https://<StorageName>.blob.core.windows.net/<ContainerName>` olmalıdır

        ![Blob kapsayıcı kopyalama URL 'SI](./media/sql-managed-instance-auditing/5_container_copy_name.png)

     1. Depolama hesabına yönetilen örnek denetimi erişim hakları vermek için bir Azure Storage **SAS belirteci** oluşturun:

        - Önceki adımda kapsayıcıyı oluşturduğunuz Azure depolama hesabına gidin.

        - Depolama ayarları menüsünde **paylaşılan erişim imzası** ' na tıklayın.

          ![Depolama ayarları menüsünde paylaşılan erişim imzası simgesi](./media/sql-managed-instance-auditing/6_storage_settings_menu.png)

        - SAS 'yi aşağıdaki gibi yapılandırın:

          - **Izin verilen hizmetler**: blob

          - **Başlangıç tarihi**: saat dilimiyle ilgili sorunlardan kaçınmak için, dün tarihinin kullanılması önerilir

          - **Bitiş tarihi**: Bu SAS belirtecinin süresinin dolacağı tarihi seçin

            > [!NOTE]
            > Denetim hatalarından kaçınmak için süre dolduktan sonra belirteci yenileyin.

          - **SAS Oluştur**’a tıklayın.
            
            ![SAS yapılandırması](./media/sql-managed-instance-auditing/7_sas_configure.png)

        - SAS oluştur 'a tıkladıktan sonra SAS belirteci en altta görünür. Kopyala simgesine tıklayarak belirteci kopyalayın ve daha sonra kullanmak üzere (örneğin, Not defteri 'nde) kaydedin.

          ![SAS belirtecini Kopyala](./media/sql-managed-instance-auditing/8_sas_copy.png)

          > [!IMPORTANT]
          > Soru işareti ("?") karakterini belirtecin başından kaldır.

     1. Yönetilen örneğinize SQL Server Management Studio (SSMS) veya desteklenen başka bir aracı aracılığıyla bağlanın.

     1. Önceki adımlarda oluşturduğunuz kapsayıcı URL 'sini ve SAS belirtecini kullanarak **Yeni bir kimlik bilgisi oluşturmak** Için aşağıdaki T-SQL ifadesini yürütün:

        ```SQL
        CREATE CREDENTIAL [<container_url>]
        WITH IDENTITY='SHARED ACCESS SIGNATURE',
        SECRET = '<SAS KEY>'
        GO
        ```

     1. Yeni bir sunucu denetimi oluşturmak için aşağıdaki T-SQL ifadesini yürütün (kendi denetim adınızı seçin, önceki adımlarda oluşturduğunuz kapsayıcı URL 'sini kullanın). Belirtilmemişse, varsayılan `RETENTION_DAYS` 0 ' dır (sınırsız saklama):

        ```SQL
        CREATE SERVER AUDIT [<your_audit_name>]
        TO URL ( PATH ='<container_url>' [, RETENTION_DAYS =  integer ])
        GO
        ```

        1. [Sunucu denetim belirtimini veya veritabanı denetim belirtimini oluşturarak](#createspec) devam edin

   - <a id="blobssms"></a>SQL Server Management Studio (SSMS) 18 (Önizleme) kullanarak denetim günlükleri için blob depolamayı yapılandırma:

     1. SQL Server Management Studio (SSMS) Kullanıcı arabirimini kullanarak yönetilen örneğe bağlanın.

     1. Nesne Gezgini kök notuna genişletin.

     1. **Güvenlik** düğümünü genişletin, **denetimler** düğümüne sağ tıklayın ve "yeni denetim" e tıklayın:

        ![Güvenlik ve denetim düğümünü Genişlet](./media/sql-managed-instance-auditing/10_mi_SSMS_new_audit.png)

     1. **Denetim hedefi** 'NDE "URL" nin seçildiğinden emin olun ve **gözatmaya**tıklayın:

        ![Azure Storage 'a gözatmaya](./media/sql-managed-instance-auditing/11_mi_SSMS_audit_browse.png)

     1. Seçim Azure hesabınızda oturum açın:

        ![Azure'da oturum açma](./media/sql-managed-instance-auditing/12_mi_SSMS_sign_in_to_azure.png)

     1. Açılır listeden bir abonelik, depolama hesabı ve BLOB kapsayıcısı seçin ya da **Oluştur**' a tıklayarak kendi kapsayıcınızı oluşturun. İşiniz bittiğinde **Tamam**' a tıklayın:

        ![Azure aboneliği, depolama hesabı ve BLOB kapsayıcısı seçin](./media/sql-managed-instance-auditing/13_mi_SSMS_select_subscription_account_container.png)

     1. "Denetim oluştur" iletişim kutusunda **Tamam** ' a tıklayın.

1. <a id="createspec"></a>Blob kapsayıcısını denetim günlükleri için hedef olarak yapılandırdıktan sonra, SQL Server için yaptığınız gibi bir sunucu denetim belirtimi veya veritabanı denetim belirtimi oluşturun:

   - [Sunucu denetim belirtimi oluşturma T-SQL Kılavuzu](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-specification-transact-sql)
   - [Veritabanı denetim belirtimi oluşturma T-SQL Kılavuzu](https://docs.microsoft.com/sql/t-sql/statements/create-database-audit-specification-transact-sql)

1. Adım 6 ' da oluşturduğunuz sunucu denetimini etkinleştirin:

    ```SQL
    ALTER SERVER AUDIT [<your_audit_name>]
    WITH (STATE=ON);
    GO
    ```

Ek bilgi için:

- [Azure SQL veritabanı ve SQL Server içindeki veritabanlarında tek veritabanları, elastik havuzlar ve yönetilen örnekler arasındaki farkları denetleme](#auditing-differences-between-databases-in-azure-sql-database-and-databases-in-sql-server)
- [SUNUCU DENETIMI OLUŞTUR](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-transact-sql)
- [SUNUCU DENETIMINI DEĞIŞTIR](https://docs.microsoft.com/sql/t-sql/statements/alter-server-audit-transact-sql)

## <a name="set-up-auditing-for-your-server-to-event-hub-or-azure-monitor-logs"></a>Sunucunuza Olay Hub 'ı veya Azure Izleyici günlüklerine yönelik denetim kurma

Yönetilen bir örnekten gelen denetim günlükleri, hatta hub 'Lara veya Azure Izleyici günlüklerine gönderilebilir. Bu bölümde, bunun nasıl yapılandırılacağı açıklanmaktadır:

1. [Azure portalında](https://portal.azure.com/) yönetilen örneğe gidin.

2. **Tanılama ayarları**' na tıklayın.

3. **Tanılamayı aç**' a tıklayın. Tanılama zaten etkinse, bunun yerine *+ Add Diagnostic ayarı* gösterilir.

4. Günlükler listesinde **SQLSecurityAuditEvents** öğesini seçin.

5. Denetim olayları için bir hedef seçin-Olay Hub 'ı, Azure Izleyici günlükleri veya her ikisi. Her bir hedef için yapılandırma gereken parametreleri (ör. Log Analytics çalışma alanı).

6. **Kaydet** düğmesine tıklayın.

    ![Tanılama ayarlarını yapılandırma](./media/sql-managed-instance-auditing/9_mi_configure_diagnostics.png)

7. **SQL Server Management Studio (SSMS)** veya desteklenen başka bir istemciyi kullanarak yönetilen örneğe bağlanın.

8. Sunucu denetimi oluşturmak için aşağıdaki T-SQL ifadesini yürütün:

    ```SQL
    CREATE SERVER AUDIT [<your_audit_name>] TO EXTERNAL_MONITOR;
    GO
    ```

9. SQL Server için yaptığınız gibi bir sunucu denetim belirtimi veya veritabanı denetim belirtimi oluşturun:

   - [Sunucu denetim belirtimi oluşturma T-SQL Kılavuzu](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-specification-transact-sql)
   - [Veritabanı denetim belirtimi oluşturma T-SQL Kılavuzu](https://docs.microsoft.com/sql/t-sql/statements/create-database-audit-specification-transact-sql)

10. 8\. adımda oluşturulan sunucu denetimini etkinleştirin:
 
    ```SQL
    ALTER SERVER AUDIT [<your_audit_name>] WITH (STATE=ON);
    GO
    ```

## <a name="consume-audit-logs"></a>Denetim günlüklerini kullanma

### <a name="consume-logs-stored-in-azure-storage"></a>Azure depolama 'da depolanan günlükleri kullanma

Blob denetim günlüklerini görüntülemek için kullanabileceğiniz çeşitli yöntemler vardır.

- Denetim günlüğü verilerini tablosal biçiminde döndürmek için `sys.fn_get_audit_file` (T-SQL) sistem işlevini kullanın. Bu işlevi kullanma hakkında daha fazla bilgi için [sys. fn_get_audit_file belgelerine](https://docs.microsoft.com/sql/relational-databases/system-functions/sys-fn-get-audit-file-transact-sql)bakın.

- [Azure Depolama Gezgini](https://azure.microsoft.com/features/storage-explorer/)gibi bir araç kullanarak denetim günlüklerini inceleyebilirsiniz. Azure depolama 'da denetim günlükleri, Denetim günlüklerini depolamak için tanımlanan bir kapsayıcı içinde blob dosyaları koleksiyonu olarak kaydedilir. Depolama klasörü, adlandırma kuralları ve günlük biçiminin hiyerarşisi hakkında daha fazla ayrıntı için bkz. [BLOB denetim günlüğü biçim başvurusu](https://go.microsoft.com/fwlink/?linkid=829599).

- Denetim günlüğü tüketim yöntemlerinin tam listesi için, [SQL veritabanı denetimini kullanmaya başlama](sql-database-auditing.md)bölümüne bakın.

### <a name="consume-logs-stored-in-event-hub"></a>Olay Hub 'ında depolanan günlükleri tüketme

Olay Hub 'ından denetim günlükleri verilerini kullanmak için, olayları tüketmek ve bunları bir hedefe yazmak üzere bir akış ayarlamanız gerekir. Daha fazla bilgi için bkz. Azure Event Hubs belgeleri.

### <a name="consume-and-analyze-logs-stored-in-azure-monitor-logs"></a>Azure Izleyici günlüklerinde depolanan günlükleri tüketme ve çözümleme

Denetim günlükleri Azure Izleyici günlüklerine yazılmışsa, denetim verilerinde gelişmiş aramaları çalıştırabileceğiniz Log Analytics çalışma alanında kullanılabilir. Başlangıç noktası olarak Log Analytics çalışma alanına gidin ve *genel* bölümünde *Günlükler* ' e tıklayın ve denetim günlüklerini görüntülemek için: `search "SQLSecurityAuditEvents"` gibi basit bir sorgu girin.  

Azure Izleyici günlükleri, tüm iş yüklerinizde ve sunucularınızda milyonlarca kaydı kolayca çözümlemek için tümleşik arama ve özel panolar kullanarak gerçek zamanlı operasyonel içgörüler sağlar. Azure Izleyici günlükleri arama dili ve komutları hakkında daha fazla yararlı bilgi için bkz. [Azure izleyici günlükleri arama başvurusu](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview).

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="auditing-differences-between-databases-in-azure-sql-database-and-databases-in-sql-server"></a>Azure SQL veritabanı ve SQL Server veritabanlarında veritabanları arasındaki farkları denetleme

Azure SQL veritabanı ve SQL Server veritabanlarında bulunan veritabanlarında denetim arasındaki temel farklılıklar şunlardır:

- Azure SQL veritabanı 'ndaki yönetilen örnek dağıtım seçeneği ile, denetim sunucu düzeyinde çalışarak, Azure Blob depolamada `.xel` günlük dosyalarını depolar.
- Şirket içi/sanal makineler SQL Server, denetim sunucu düzeyinde çalışabilir, ancak olayları dosyalar sistem/Windows olay günlüklerine depolar.

Yönetilen örnekteki XEvent denetimi Azure Blob depolama hedeflerini destekler. Dosya ve Windows günlükleri **desteklenmez**.

Azure Blob depolamaya denetim için `CREATE AUDIT` sözdiziminde önemli farklılıklar şunlardır:

- Yeni bir sözdizimi `TO URL` sağlanır ve `.xel` dosyalarının yerleştirildiği Azure Blob depolama kapsayıcısının URL 'sini belirtmenize olanak sağlar.
- Eşit hub ve Azure Izleyici günlükleri hedeflerini etkinleştirmek için yeni bir sözdizimi `TO EXTERNAL MONITOR` sağlanır.
- SQL veritabanı Windows dosya paylaşımlarına erişemediğinden `TO FILE` sözdizimi **desteklenmez** .
- Kapalı seçeneği **desteklenmez**.
- 0 `queue_delay` **desteklenmez**.

## <a name="next-steps"></a>Sonraki adımlar

- Denetim günlüğü tüketim yöntemlerinin tam listesi için, [SQL veritabanı denetimini kullanmaya başlama](sql-database-auditing.md)bölümüne bakın.
- Standartlar uyumluluğunu destekleyen Azure programları hakkında daha fazla bilgi için, SQL veritabanı uyumluluk sertifikalarının en güncel listesini bulabileceğiniz [Azure Güven Merkezi](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) bakın.

<!--Image references-->









