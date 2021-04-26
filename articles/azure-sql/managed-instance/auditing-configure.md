---
title: SQL yönetilen örnek denetimi
description: T-SQL kullanarak Azure SQL yönetilen örnek denetimi ile çalışmaya nasıl başlaleyeceğinizi öğrenin
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
f1_keywords:
- mi.azure.sqlaudit.general.f1
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 05/26/2020
ms.openlocfilehash: ae0d9696d869b2a260de643482a9f86c34bcc824
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100575472"
---
# <a name="get-started-with-azure-sql-managed-instance-auditing"></a>Azure SQL yönetilen örnek denetimi 'ni kullanmaya başlama
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

[Azure SQL yönetilen örnek](sql-managed-instance-paas-overview.md) denetimi, veritabanı olaylarını izler ve bunları Azure Depolama hesabınızdaki bir denetim günlüğüne yazar. Denetim şunları da sağlar:

- Mevzuatla uyumluluk, veritabanı etkinliğini anlama ve işletme sorunlarını veya şüpheli güvenlik ihlallerini işaret edebilecek farklılıklar ve anormal durumlar hakkında içgörü sahip olmanıza yardımcı olur.
- Uyumluluğu garanti etmese bile uyumluluk standartlarına uymayı sağlar ve kolaylaştırır. Standartlar uyumluluğunu destekleyen Azure programları hakkında daha fazla bilgi için, en güncel uyumluluk sertifikaları listesini bulabileceğiniz [Azure Güven Merkezi](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942)bakın.

## <a name="set-up-auditing-for-your-server-to-azure-storage"></a>Azure depolama 'ya sunucunuz için Denetim kurma

Aşağıdaki bölümde, yönetilen örneğiniz üzerinde denetim yapılandırması açıklanmaktadır.

1. [Azure Portal](https://portal.azure.com) gidin.
2. Denetim günlüklerinin depolandığı bir Azure depolama **kapsayıcısı** oluşturun.

   1. Denetim günlüklerinizi depolamak istediğiniz Azure depolama hesabına gidin.

      > [!IMPORTANT]
      > - Bölgeler arası okuma/yazma işlemlerini önlemek için yönetilen örnekle aynı bölgedeki bir depolama hesabını kullanın. 
      > - Depolama hesabınız bir sanal ağın veya güvenlik duvarının arkasındaysa, lütfen bkz. [bir sanal ağ üzerinden erişim verme](../../storage/common/storage-network-security.md#grant-access-from-a-virtual-network).
      > - Saklama süresini 0 ' dan (sınırsız saklama) başka herhangi bir değere değiştirirseniz, lütfen bekletme 'nin yalnızca bekletme değeri değiştirildikten sonra yazılan günlüklere uygulanacağını unutmayın (bekletme etkin olduktan sonra bile, bekletme olarak sınırsız olarak ayarlandığında zaman içinde yazılır).

   1. Depolama hesabında **Genel Bakış ' a** gidin ve **Bloblar**' a tıklayın.

      ![Azure Blob pencere öğesi](./media/auditing-configure/1_blobs_widget.png)

   1. Yeni bir kapsayıcı oluşturmak için üst menüsünde **+ kapsayıcı** ' ya tıklayın.

      ![Blob kapsayıcısı Oluştur simgesi](./media/auditing-configure/2_create_container_button.png)

   1. Bir kapsayıcı **adı** girin, **ortak erişim düzeyini** **özel** olarak ayarlayın ve ardından **Tamam**' a tıklayın.

      ![Blob kapsayıcı yapılandırması oluştur](./media/auditing-configure/3_create_container_config.png)

    > [!IMPORTANT]
    > Sunucu veya veritabanı düzeyinde denetim olayları için bir sabit günlük deposu yapılandırmak isteyen müşteriler, [Azure depolama tarafından sunulan yönergeleri](../../storage/blobs/storage-blob-immutability-policies-manage.md#enabling-allow-protected-append-blobs-writes)izlemelidir. (Lütfen sabit blob depolamayı yapılandırırken **ek eklemesine Izin ver** ' i seçtiğinizden emin olun.)
  
3. Denetim günlükleri için kapsayıcıyı oluşturduktan sonra, denetim günlüklerinin hedefi olarak yapılandırmak için iki yol vardır: [T-SQL kullanma](#blobtsql) veya [SQL Server Management Studio (SSMS) Kullanıcı arabirimini kullanma](#blobssms):

   - <a id="blobtsql"></a>**T-SQL kullanarak denetim günlükleri için blob depolamayı yapılandırma:**

     1. Kapsayıcılar listesinde, yeni oluşturulan kapsayıcıya ve ardından **kapsayıcı özellikleri**' ne tıklayın.

        ![Blob kapsayıcısı Özellikler düğmesi](./media/auditing-configure/4_container_properties_button.png)

     1. Kopyalama simgesine tıklayarak ve daha sonra kullanmak üzere URL 'YI (örneğin, Not defteri 'nde) kaydederek kapsayıcı URL 'sini kopyalayın. Kapsayıcı URL biçimi şu olmalıdır `https://<StorageName>.blob.core.windows.net/<ContainerName>`

        ![Blob kapsayıcı kopyalama URL 'SI](./media/auditing-configure/5_container_copy_name.png)

     1. Depolama hesabına yönetilen örnek denetimi erişim hakları vermek için bir Azure Storage **SAS belirteci** oluşturun:

        - Önceki adımda kapsayıcıyı oluşturduğunuz Azure depolama hesabına gidin.

        - **Depolama ayarları** menüsünde **paylaşılan erişim imzası** ' na tıklayın.

          ![Depolama ayarları menüsünde paylaşılan erişim imzası simgesi](./media/auditing-configure/6_storage_settings_menu.png)

        - SAS 'yi aşağıdaki gibi yapılandırın:

          - **Izin verilen hizmetler**: blob

          - **Başlangıç tarihi**: saat dilimiyle ilgili sorunlardan kaçınmak için, dün tarihini kullanın

          - **Bitiş tarihi**: Bu SAS belirtecinin süresinin dolacağı tarihi seçin

            > [!NOTE]
            > Denetim hatalarından kaçınmak için süre dolduktan sonra belirteci yenileyin.

          - **SAS Oluştur**’a tıklayın.

            ![SAS yapılandırması](./media/auditing-configure/7_sas_configure.png)

        - SAS belirteci en altta görünür. Kopyala simgesine tıklayarak belirteci kopyalayın ve daha sonra kullanmak üzere (örneğin, Not defteri 'nde) kaydedin.

          ![SAS belirtecini Kopyala](./media/auditing-configure/8_sas_copy.png)

          > [!IMPORTANT]
          > Soru işareti ("?") karakterini belirtecin başından kaldır.

     1. Yönetilen örneğiniz SQL Server Management Studio veya desteklenen başka bir araç aracılığıyla bağlanın.

     1. Önceki adımlarda oluşturduğunuz kapsayıcı URL 'sini ve SAS belirtecini kullanarak **Yeni bir kimlik bilgisi oluşturmak** Için aşağıdaki T-SQL ifadesini yürütün:

        ```SQL
        CREATE CREDENTIAL [<container_url>]
        WITH IDENTITY='SHARED ACCESS SIGNATURE',
        SECRET = '<SAS KEY>'
        GO
        ```

     1. Yeni bir sunucu denetimi oluşturmak için aşağıdaki T-SQL ifadesini yürütün (kendi denetim adınızı seçin ve önceki adımlarda oluşturduğunuz kapsayıcı URL 'sini kullanın). Belirtilmemişse, `RETENTION_DAYS` varsayılan değer 0 ' dır (sınırsız saklama):

        ```SQL
        CREATE SERVER AUDIT [<your_audit_name>]
        TO URL ( PATH ='<container_url>' , RETENTION_DAYS =  integer )
        GO
        ```

     1. [Sunucu denetim belirtimini veya veritabanı denetim belirtimini oluşturarak](#createspec)devam edin.

   - <a id="blobssms"></a>**SQL Server Management Studio 18 ' i kullanarak denetim günlükleri için blob depolamayı yapılandırma:**

     1. SQL Server Management Studio Kullanıcı arabirimini kullanarak yönetilen örneğe bağlanın.

     1. Nesne Gezgini kök dekontunu genişletin.

     1. **Güvenlik** düğümünü genişletin, **denetimler** düğümüne sağ tıklayın ve **Yeni denetim**' e tıklayın:

        ![Güvenlik ve denetim düğümünü Genişlet](./media/auditing-configure/10_mi_SSMS_new_audit.png)

     1. **Denetim hedefi** 'Nde **URL 'nin** seçildiğinden emin olun ve **gözatmaya** tıklayın:

        ![Azure Storage 'a gözatmaya](./media/auditing-configure/11_mi_SSMS_audit_browse.png)

     1. Seçim Azure hesabınızda oturum açın:

        ![Azure'da oturum açma](./media/auditing-configure/12_mi_SSMS_sign_in_to_azure.png)

     1. Açılır listeden bir abonelik, depolama hesabı ve BLOB kapsayıcısı seçin ya da **Oluştur**' a tıklayarak kendi kapsayıcınızı oluşturun. İşiniz bittiğinde **Tamam**' a tıklayın:

        ![Azure aboneliği, depolama hesabı ve BLOB kapsayıcısı seçin](./media/auditing-configure/13_mi_SSMS_select_subscription_account_container.png)

     1. **Denetim oluştur** Iletişim kutusunda **Tamam** ' a tıklayın.
     
     1. <a id="createspec"></a>Blob kapsayıcısını denetim günlükleri için hedef olarak yapılandırdıktan sonra, SQL Server için yaptığınız şekilde sunucu denetim belirtimini veya veritabanı denetim belirtimini oluşturun ve etkinleştirin:

   - [Sunucu denetim belirtimi oluşturma T-SQL Kılavuzu](/sql/t-sql/statements/create-server-audit-specification-transact-sql)
   - [Veritabanı denetim belirtimi oluşturma T-SQL Kılavuzu](/sql/t-sql/statements/create-database-audit-specification-transact-sql)

5. Adım 3 ' te oluşturduğunuz sunucu denetimini etkinleştirin:

    ```SQL
    ALTER SERVER AUDIT [<your_audit_name>]
    WITH (STATE=ON);
    GO
    ```

Ek bilgi için:

- [SQL Server Azure SQL yönetilen örneği ve veritabanı arasındaki farkları denetleme](#auditing-differences-between-databases-in-azure-sql-managed-instance-and-databases-in-sql-server)
- [SUNUCU DENETIMI OLUŞTUR](/sql/t-sql/statements/create-server-audit-transact-sql)
- [SUNUCU DENETIMINI DEĞIŞTIR](/sql/t-sql/statements/alter-server-audit-transact-sql)

## <a name="set-up-auditing-for-your-server-to-event-hubs-or-azure-monitor-logs"></a>Sunucunuza Event Hubs veya Azure Izleyici günlüklerine yönelik denetim kurma

Yönetilen bir örnekten gelen denetim günlükleri Azure Event Hubs veya Azure Izleyici günlüklerine gönderilebilir. Bu bölümde, bunun nasıl yapılandırılacağı açıklanmaktadır:

1. Yönetilen örneğe [Azure Portal](https://portal.azure.com/) gidin.

2. **Tanılama ayarları**' na tıklayın.

3. **Tanılamayı aç**' a tıklayın. Tanılama zaten etkinse + bunun yerine **Tanılama ayarı Ekle** görünür.

4. Günlükler listesinde **SQLSecurityAuditEvents** öğesini seçin.

5. Denetim olayları için bir hedef seçin: Event Hubs, Azure Izleyici günlükleri veya her ikisi. Her bir hedef için yapılandırma gereken parametreleri (ör. Log Analytics çalışma alanı).

6. **Kaydet**’e tıklayın.

    ![Tanılama ayarlarını yapılandırma](./media/auditing-configure/9_mi_configure_diagnostics.png)

7. **SQL Server Management Studio (SSMS)** veya desteklenen başka bir istemciyi kullanarak yönetilen örneğe bağlanın.

8. Sunucu denetimi oluşturmak için aşağıdaki T-SQL ifadesini yürütün:

    ```SQL
    CREATE SERVER AUDIT [<your_audit_name>] TO EXTERNAL_MONITOR;
    GO
    ```

9. SQL Server için yaptığınız gibi sunucu denetim belirtimini veya veritabanı denetim belirtimini oluşturun ve etkinleştirin:

   - [Sunucu denetim belirtimi oluşturma T-SQL Kılavuzu](/sql/t-sql/statements/create-server-audit-specification-transact-sql)
   - [Veritabanı denetim belirtimi oluşturma T-SQL Kılavuzu](/sql/t-sql/statements/create-database-audit-specification-transact-sql)

10. 8. adımda oluşturulan sunucu denetimini etkinleştirin:

    ```SQL
    ALTER SERVER AUDIT [<your_audit_name>]
    WITH (STATE=ON);
    GO
    ```

## <a name="consume-audit-logs"></a>Denetim günlüklerini kullanma

### <a name="consume-logs-stored-in-azure-storage"></a>Azure depolama 'da depolanan günlükleri kullanma

Blob denetim günlüklerini görüntülemek için kullanabileceğiniz çeşitli yöntemler vardır.

- `sys.fn_get_audit_file`Denetim günlüğü verilerini tablosal biçiminde döndürmek için sistem işlevini (T-SQL) kullanın. Bu işlevi kullanma hakkında daha fazla bilgi için [sys.fn_get_audit_file belgelerine](/sql/relational-databases/system-functions/sys-fn-get-audit-file-transact-sql)bakın.

- [Azure Depolama Gezgini](https://azure.microsoft.com/features/storage-explorer/)gibi bir araç kullanarak denetim günlüklerini inceleyebilirsiniz. Azure depolama 'da denetim günlükleri, Denetim günlüklerini depolamak için tanımlanan bir kapsayıcı içinde blob dosyaları koleksiyonu olarak kaydedilir. Depolama klasörü, adlandırma kuralları ve günlük biçiminin hiyerarşisi hakkında daha fazla ayrıntı için bkz. [BLOB denetim günlüğü biçim başvurusu](../database/audit-log-format.md).

- Denetim günlüğü tüketim yöntemlerinin tam listesi için [Azure SQL veritabanı denetimini kullanmaya başlama](../../azure-sql/database/auditing-overview.md)bölümüne bakın.

### <a name="consume-logs-stored-in-event-hubs"></a>Event Hubs depolanan günlükleri kullanma

Event Hubs denetim günlükleri verilerini kullanmak için, olayları tüketmek ve bunları bir hedefe yazmak üzere bir akış ayarlamanız gerekir. Daha fazla bilgi için bkz. Azure Event Hubs belgeleri.

### <a name="consume-and-analyze-logs-stored-in-azure-monitor-logs"></a>Azure Izleyici günlüklerinde depolanan günlükleri tüketme ve çözümleme

Denetim günlükleri Azure Izleyici günlüklerine yazılmışsa, denetim verilerinde gelişmiş aramaları çalıştırabileceğiniz Log Analytics çalışma alanında kullanılabilir. Başlangıç noktası olarak Log Analytics çalışma alanına gidin. **Genel** bölümünde **Günlükler** ' e tıklayın ve örneğin: `search "SQLSecurityAuditEvents"` Denetim günlüklerini görüntülemek için basit bir sorgu girin.  

Azure Izleyici günlükleri, tüm iş yüklerinizde ve sunucularınızda milyonlarca kaydı kolayca çözümlemek için tümleşik arama ve özel panolar kullanarak gerçek zamanlı operasyonel içgörüler sağlar. Azure Izleyici günlükleri arama dili ve komutları hakkında daha fazla yararlı bilgi için bkz. [Azure izleyici günlükleri arama başvurusu](../../azure-monitor/logs/log-query-overview.md).

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="auditing-differences-between-databases-in-azure-sql-managed-instance-and-databases-in-sql-server"></a>Azure SQL yönetilen örneği ve SQL Server veritabanlarında veritabanları arasındaki farkları denetleme

Azure SQL yönetilen örneği ve SQL Server veritabanlarında veritabanlarında denetim arasındaki temel farklılıklar şunlardır:

- Azure SQL yönetilen örneği ile denetim, sunucu düzeyinde çalışarak `.xel` günlük dosyalarını Azure Blob depolama alanında depolar.
- SQL Server, denetim sunucu düzeyinde çalışmaktadır, ancak olayları dosyalar sistem/Windows olay günlüklerine depolar.

Yönetilen örneklerde XEvent denetimi Azure Blob depolama hedeflerini destekler. Dosya ve Windows günlükleri **desteklenmez**.

`CREATE AUDIT`Azure Blob depolamaya denetim için söz diziminde önemli farklılıklar şunlardır:

- Yeni bir sözdizimi `TO URL` sağlanır ve dosyaların yerleştirildiği Azure Blob depolama KAPSAYıCıSıNıN URL 'sini belirtmenize olanak sağlar `.xel` .
- `TO EXTERNAL MONITOR`Event Hubs ve Azure izleyici günlük hedeflerini etkinleştirmek için yeni bir sözdizimi sağlanır.
- `TO FILE`Azure SQL yönetilen örneği Windows dosya paylaşımlarına erişemediği için sözdizimi **desteklenmiyor** .
- Kapalı seçeneği **desteklenmez**.
- `queue_delay` /0 **desteklenmez**.

## <a name="next-steps"></a>Sonraki adımlar

- Denetim günlüğü tüketim yöntemlerinin tam listesi için [Azure SQL veritabanı denetimini kullanmaya başlama](../../azure-sql/database/auditing-overview.md)bölümüne bakın.
- Standartlar uyumluluğunu destekleyen Azure programları hakkında daha fazla bilgi için, en güncel uyumluluk sertifikaları listesini bulabileceğiniz [Azure Güven Merkezi](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942)bakın.

<!--Image references-->
