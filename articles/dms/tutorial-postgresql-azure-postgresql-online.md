---
title: "Öğretici: PostgreSQL için Azure veritabanı 'na PostgreSQL için çevrimiçi geçiş gerçekleştirmek üzere Azure veritabanı geçiş hizmeti 'ni kullanma | Microsoft Docs"
description: Azure veritabanı geçiş hizmeti 'ni kullanarak şirket içi PostgreSQL 'ten PostgreSQL için Azure veritabanı 'na çevrimiçi geçiş gerçekleştirmeyi öğrenin.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 10/28/2019
ms.openlocfilehash: 1b4eebafadcdbebfc89ce7265f4d4f77f4f5ac8c
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73043241"
---
# <a name="tutorial-migrate-postgresql-to-azure-database-for-postgresql-online-using-dms"></a>Öğretici: DMS kullanarak PostgreSQL’i çevrimiçi ortamda PostgreSQL için Azure Veritabanına geçirme

Azure veritabanı geçiş hizmeti 'ni kullanarak veritabanlarını şirket içi bir PostgreSQL örneğinden [PostgreSQL Için Azure veritabanı](https://docs.microsoft.com/azure/postgresql/) 'na en az kapalı kalma süresiyle geçirebilirsiniz. Diğer bir deyişle, geçişe uygulamanın en az kapalı kalma süresi ile ulaşılabilecek. Bu öğreticide, Azure veritabanı geçiş hizmeti 'ndeki çevrimiçi geçiş etkinliğini kullanarak PostgreSQL 9,6 'nin şirket içi bir örneğinden **DVD Kiralama** örneği veritabanını PostgreSQL Için Azure veritabanı 'na geçireceğiniz.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:
> [!div class="checklist"]
>
> * Örnek şemayı pg_dump yardımcı programını kullanarak geçirin.
> * Azure Veritabanı Geçiş Hizmeti örneği oluşturma.
> * Azure Veritabanı Geçiş Hizmeti'ni kullanarak geçiş projesi oluşturma.
> * Geçişi çalıştırma.
> * Geçişi izleme.

> [!NOTE]
> Çevrimiçi bir geçiş gerçekleştirmek için Azure veritabanı geçiş hizmeti 'nin kullanılması, Premium fiyatlandırma katmanını temel alan bir örnek oluşturulmasını gerektirir.

> [!IMPORTANT]
> En iyi geçiş deneyimi için, Microsoft, hedef veritabanıyla aynı Azure bölgesinde Azure veritabanı geçiş hizmeti örneği oluşturulmasını önerir. Verileri bölgeler veya coğrafyalar arasında taşımak, geçiş sürecini yavaşlatabilir ve hatalara neden olabilir.

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlamak için aşağıdakileri yapmanız gerekir:

* [PostgreSQL Community edition](https://www.postgresql.org/download/) 9,5, 9,6 veya 10 ' u indirin ve yükleyin. Kaynak PostgreSQL sunucusu sürümü 9.5.11, 9.6.7, 10 veya üzeri olmalıdır. Daha fazla bilgi için bkz. [PostgreSQL veritabanı sürümlerini destekleyen](https://docs.microsoft.com/azure/postgresql/concepts-supported-versions)makale.

    Ayrıca, şirket içi PostgreSQL sürümünün, PostgreSQL için Azure Veritabanı sürümü ile eşleşmesi gerekir. Örneğin, PostgreSQL 9.5.11.5 yalnızca PostgreSQL için Azure Veritabanı 9.5.11 sürümüne geçirilebilir ve 9.6.7 sürümüne geçirilemez.

* [PostgreSQL için Azure Veritabanı’nda örnek oluşturma](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal).  
* [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) veya VPN kullanarak şirket içi kaynak sunucularınıza siteden siteye bağlantı sağlayan Azure Resource Manager dağıtım modelini kullanarak Azure veritabanı geçiş hizmeti Için bir Azure sanal ağı (VNet) oluşturun [ ](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). VNet oluşturma hakkında daha fazla bilgi için, [sanal ağ belgelerine](https://docs.microsoft.com/azure/virtual-network/)ve özellikle de adım adım ayrıntılara sahip olan hızlı başlangıç makalelerine bakın.

    > [!NOTE]
    > VNet kurulumu sırasında, ExpressRoute 'u Microsoft 'a ağ eşlemesi ile kullanıyorsanız, aşağıdaki hizmet [uç noktalarını](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) hizmetin sağlanacağı alt ağa ekleyin:
    > * Hedef veritabanı uç noktası (örneğin, SQL uç noktası, Cosmos DB uç noktası vb.)
    > * Depolama uç noktası
    > * Service Bus uç noktası
    >
    > Azure veritabanı geçiş hizmeti internet bağlantısı olmadığından bu yapılandırma gereklidir.

* VNet ağ güvenlik grubu (NSG) kurallarınızın Azure veritabanı geçiş hizmeti 'ne yönelik aşağıdaki gelen iletişim bağlantı noktalarını engellemediğinden emin olun: 443, 53, 9354, 445, 12000. Azure VNet NSG trafik filtrelemesi hakkında daha fazla bilgi için ağ [güvenlik grupları ile ağ trafiğini filtreleme](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm)makalesine bakın.
* [Windows Güvenlik Duvarınızı veritabanı altyapısı erişimi](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access) için yapılandırın.
* Azure veritabanı geçiş hizmeti 'nin kaynak PostgreSQL sunucusuna erişmesine izin vermek için Windows Güvenlik duvarınızı açın, varsayılan olarak TCP bağlantı noktası 5432 ' dir.
* Kaynak veritabanlarınızın önünde bir güvenlik duvarı cihazı kullanıyorsanız, Azure Veritabanı Geçiş Hizmeti'nin geçiş amacıyla kaynak veritabanlarına erişmesi için güvenlik duvarı kuralları eklemeniz gerekebilir.
* Azure veritabanı geçiş hizmeti 'nin hedef veritabanlarına erişmesine izin vermek için PostgreSQL için Azure veritabanı için sunucu düzeyinde bir [güvenlik duvarı kuralı](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) oluşturun. Azure veritabanı geçiş hizmeti için kullanılan VNet 'in alt ağ aralığını belirtin.
* CLI’yi çağırmak için iki yöntem vardır:

  * Azure portal sağ üst köşesinde Cloud Shell düğmesini seçin:

       ![Azure portaldaki Cloud Shell düğmesi](media/tutorial-postgresql-to-azure-postgresql-online/cloud-shell-button.png)

  * CLI’yi yerel olarak yükleyip çalıştırın. CLI 2.0, Azure kaynaklarını yönetmeye yönelik komut satırı aracıdır.

       CLI’yi indirmek için [Azure CLI 2.0’ı yükleme](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) makalesindeki yönergeleri izleyin. Makalede ayrıca CLI 2.0'ı destekleyen platformlar listelenir.

       Linux için Windows Alt Sistemi’ni (WSL) ayarlamak istiyorsanız [Windows 10 Yükleme Kılavuzu](https://docs.microsoft.com/windows/wsl/install-win10) içindeki yönergeleri izleyin

* postgresql.config dosyasında mantıksal çoğaltmayı etkinleştirin ve aşağıdaki parametreleri ayarlayın:

  * wal_level = **logical**
  * max_replication_slots = [yuva sayısı], **beş yuva** ayarı önerilir
  * max_wal_senders =[eşzamanlı görev sayısı] - max_wal_senders parametresi, çalışabilecek eşzamanlı görevlerin sayısını ayarlar; önerilen ayar **10 görevdir**

## <a name="migrate-the-sample-schema"></a>Örnek şemayı geçirme

Tablo şemaları, dizinler ve saklı yordamlar gibi tüm veritabanı nesnelerini tamamlamak için kaynak veritabanındaki şemayı ayıklamamız ve veritabanına uygulamamız gerekir.

1. Bir veritabanına yönelik şema döküm dosyası oluşturmak için pg_dump -s komutunu kullanın. 

    ```
    pg_dump -o -h hostname -U db_username -d db_name -s > your_schema.sql
    ```

    Örneğin, dvdrental veritabanındaki bir şema dosyasının dökümünü çıkarmak için:
    ```
    pg_dump -o -h localhost -U postgres -d dvdrental -s  > dvdrentalSchema.sql
    ```

    pg_dump yardımcı programını kullanma hakkında daha fazla bilgi için [pg-dump](https://www.postgresql.org/docs/9.6/static/app-pgdump.html#PG-DUMP-EXAMPLES) öğreticisindeki örneklere bakın.

2. Hedef ortamınız olan PostgreSQL için Azure Veritabanı içinde boş bir veritabanı oluşturun.

    Bağlantı kurma ve veritabanı oluşturma hakkında ayrıntılı bilgi için [Azure portalda PostgreSQL için Azure Veritabanı sunucusu oluşturma](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal) makalesine bakın.

3. Şema döküm dosyasını geri yükleyerek, şemayı oluşturduğunuz hedef veritabanına aktarın.

    ```
    psql -h hostname -U db_username -d db_name < your_schema.sql 
    ```

    Örnek:

    ```
    psql -h mypgserver-20170401.postgres.database.azure.com  -U postgres -d dvdrental < dvdrentalSchema.sql
    ```

4. Şemanızda yabancı anahtarlar varsa geçişe ilişkin ilk yük ve sürekli eşitleme başarısız olur. Hedefte (PostgreSQL için Azure Veritabanı) bırakma yabancı anahtar betiği ve ekleme yabancı anahtar betiğini ayıklamak için PgAdmin veya psql içinde aşağıdaki betiği yürütün.
  
    ```
    SELECT Queries.tablename
           ,concat('alter table ', Queries.tablename, ' ', STRING_AGG(concat('DROP CONSTRAINT ', Queries.foreignkey), ',')) as DropQuery
                ,concat('alter table ', Queries.tablename, ' ',
                                                STRING_AGG(concat('ADD CONSTRAINT ', Queries.foreignkey, ' FOREIGN KEY (', column_name, ')', 'REFERENCES ', foreign_table_name, '(', foreign_column_name, ')' ), ',')) as AddQuery
        FROM
        (SELECT
        tc.table_schema,
        tc.constraint_name as foreignkey,
        tc.table_name as tableName,
        kcu.column_name,
        ccu.table_schema AS foreign_table_schema,
        ccu.table_name AS foreign_table_name,
        ccu.column_name AS foreign_column_name
    FROM
        information_schema.table_constraints AS tc
        JOIN information_schema.key_column_usage AS kcu
          ON tc.constraint_name = kcu.constraint_name
          AND tc.table_schema = kcu.table_schema
        JOIN information_schema.constraint_column_usage AS ccu
          ON ccu.constraint_name = tc.constraint_name
          AND ccu.table_schema = tc.table_schema
    WHERE constraint_type = 'FOREIGN KEY') Queries
      GROUP BY Queries.tablename;
    ```

    Sorgu sonucunda bırakma yabancı anahtarını (ikinci sütun) çalıştırın.

5. Verilerdeki tetikleyiciler (ekleme veya güncelleştirme tetikleyicileri) kaynaktan çoğaltılan veriler ile hedef arasında veri bütünlüğü sağlar. Geçiş sırasında **Hedefteki** tüm tablolardaki Tetikleyicileri devre dışı bırakmanız ve ardından geçiş tamamlandıktan sonra Tetikleyicileri yeniden etkinleştirmeniz önerilir.

    Hedef veritabanında tetikleyicileri devre dışı bırakmak için şu komutu kullanın:

    ```
    select concat ('alter table ', event_object_table, ' disable trigger ', trigger_name)
    from information_schema.triggers;
    ```

6. Herhangi bir tabloda sabit listesi veri türü varsa, onu hedef tablodaki ' karakter değişen ' veri türüne geçici olarak güncelleştirmeniz önerilir. Veri çoğaltma tamamlandıktan sonra veri türünü yeniden ENUM yapın.

## <a name="provisioning-an-instance-of-dms-using-the-cli"></a>CLI kullanarak DMS örneği sağlama

1. Dms eşitleme uzantısını yükleyin:
   * Aşağıdaki komutu çalıştırarak Azure'da oturum açın:
       ```
       az login
       ```

   * Sorulduğunda bir web tarayıcısı açın ve cihazınızın kimliğini doğrulamak için bir kod girin. Listelenen yönergeleri uygulayın.
   * Dms uzantısını ekleyin:
       * Kullanılabilir uzantıları listelemek için aşağıdaki komutu çalıştırın:

           ```
           az extension list-available –otable
           ```

       * Uzantıyı yüklemek için aşağıdaki komutu çalıştırın:

           ```
           az extension add –n dms-preview
           ```

   * Dms uzantısının doğru yüklendiğini onaylamak için aşağıdaki komutu çalıştırın:

       ```
       az extension list -otable
       ```
       Aşağıdaki çıktıyı görmeniz gerekir:

       ```
       ExtensionType    Name
       ---------------  ------
       whl              dms
       ```

   * Dilediğiniz zaman aşağıdaki komutu çalıştırarak desteklenen tüm komutları görüntüleyin:

       ```
       az dms -h
       ```

   * Birden çok Azure aboneliğiniz varsa, DMS hizmetinin bir örneğini sağlamak için kullanmak istediğiniz aboneliği ayarlamak üzere aşağıdaki komutu çalıştırın.

        ```
       az account set -s 97181df2-909d-420b-ab93-1bff15acb6b7
        ```

2. Aşağıdaki komutu çalıştırarak bir DMS örneğini sağlayın:

   ```
   az dms create -l [location] -n <newServiceName> -g <yourResourceGroupName> --sku-name BusinessCritical_4vCores --subnet/subscriptions/{vnet subscription id}/resourceGroups/{vnet resource group}/providers/Microsoft.Network/virtualNetworks/{vnet name}/subnets/{subnet name} –tags tagName1=tagValue1 tagWithNoValue
   ```

   Örneğin, aşağıdaki komut şurada bir hizmet oluşturur:

   * Konum: Doğu ABD2
   * Abonelik: 97181df2-909d-420b-ab93-1bff15acb6b7
   * Kaynak Grubu Adı: PostgresDemo
   * DMS Hizmet Adı: PostgresCLI

   ```
   az dms create -l eastus2 -g PostgresDemo -n PostgresCLI --subnet /subscriptions/97181df2-909d-420b-ab93-1bff15acb6b7/resourceGroups/ERNetwork/providers/Microsoft.Network/virtualNetworks/AzureDMS-CORP-USC-VNET-5044/subnets/Subnet-1 --sku-name BusinessCritical_4vCores
   ```

   DMS hizmeti örneğini oluşturmak yaklaşık 10-12 dakika sürer.

3. Postgres pg_hba.conf dosyasına eklemek üzere DMS aracısının IP adresini tanımlamak için aşağıdaki komutu çalıştırın:

    ```
    az network nic list -g <ResourceGroupName>--query '[].ipConfigurations | [].privateIpAddress'
    ```

    Örnek:

    ```
    az network nic list -g PostgresDemo --query '[].ipConfigurations | [].privateIpAddress'
    ```

    Aşağıdaki adrese benzer bir sonuç almalısınız: 

    ```
    [
      "172.16.136.18"
    ]
    ```

4. DMS aracısının IP adresini Postgres pg_hba.conf dosyasına ekleyin.

    * DMS içinde sağlamayı bitirdikten sonra DMS IP adresini not alın.
    * IP adresini aşağıdaki girişe benzer şekilde kaynaktaki pg_hba.conf dosyasına ekleyin:

        ```
        host    all     all     172.16.136.18/10    md5
        host    replication     postgres    172.16.136.18/10    md5
        ```

5. Ardından, aşağıdaki komutu çalıştırarak bir PostgreSQL geçiş projesi oluşturun:
    
    ```
    az dms project create -l <location> -g <ResourceGroupName> --service-name <yourServiceName> --source-platform PostgreSQL --target-platform AzureDbforPostgreSQL -n <newProjectName>
    ```

    Örneğin, aşağıdaki komut bu parametreleri kullanarak bir proje oluşturur:

   * Konum: Orta Batı ABD
   * Kaynak Grubu Adı: PostgresDemo
   * Hizmet Adı: PostgresCLI
   * Proje adı: PGMigration
   * Kaynak platform: PostgreSQL
   * Hedef platform: AzureDbForPostgreSql

     ```
     az dms project create -l westcentralus -n PGMigration -g PostgresDemo --service-name PostgresCLI --source-platform PostgreSQL --target-platform AzureDbForPostgreSql
     ```

6. Aşağıdaki adımları kullanarak bir PostgreSQL geçiş görevi oluşturun.

    Bu adım, bağlantı kurmak için kaynak IP, UserID ve parola, hedef IP, UserID, parola ve görev türünü kullanmayı içerir.

   * Seçeneklerin tam listesini görmek için komutu çalıştırın:

       ```
       az dms project task create -h
       ```

       Hem kaynak hem de hedef bağlantı için giriş parametresi, nesne listesini içeren bir json dosyasına başvurur.

       PostgreSQL bağlantıları için bağlantı JSON nesnesinin biçimi.
        
       ```
       {
                   "userName": "user name",    // if this is missing or null, you will be prompted
                   "password": null,           // if this is missing or null (highly recommended) you will
               be prompted
                   "serverName": "server name",
                   "databaseName": "database name", // if this is missing, it will default to the 'postgres'
               server
                   "port": 5432                // if this is missing, it will default to 5432
               }
       ```

   * JSON nesnelerini listeleyen bir veritabanı seçeneği JSON dosyası da vardır. PostgreSQL için veri tabanı seçeneği JSON nesnesinin biçimi aşağıda gösterilmiştir:

       ```
       [
           {
               "name": "source database",
               "target_database_name": "target database",
           },
           ...n
       ]
       ```

   * Not Defteri ile bir json dosyası oluşturun, aşağıdaki komutları kopyalayın ve dosyanın içine yapıştırın, ardından dosyayı C:\DMS\source.json yoluna kaydedin.

        ```
       {
                   "userName": "postgres",    
                   "password": null,           
               be prompted
                   "serverName": "13.51.14.222",
                   "databaseName": "dvdrental", 
                   "port": 5432                
               }
        ```

   * Target.json adlı başka bir dosya oluşturun ve C:\DMS\target.json olarak kaydedin. Aşağıdaki komutları ekleyin:

       ```
       {
               "userName": " dms@builddemotarget",    
               "password": null,           
               "serverName": " builddemotarget.postgres.database.azure.com",
               "databaseName": "inventory", 
               "port": 5432                
           }
       ```

   * Geçirilecek veritabanı olarak stok listeleyen bir veritabanı seçeneği json dosyası oluşturun:

       ``` 
       [
           {
               "name": "dvdrental",
               "target_database_name": "dvdrental",
           }
       ]
       ```

   * Kaynak, hedef ve DB seçeneği json dosyalarını alan aşağıdaki komutu çalıştırın.

       ``` 
       az dms project task create -g PostgresDemo --project-name PGMigration --source-platform postgresql --target-platform azuredbforpostgresql --source-connection-json c:\DMS\source.json --database-options-json C:\DMS\option.json --service-name PostgresCLI --target-connection-json c:\DMS\target.json –task-type OnlineMigration -n runnowtask    
       ```

     Bu noktada, bir geçiş görevini başarıyla gönderdiniz.

7. Görevin ilerleme durumunu göstermek için aşağıdaki komutu çalıştırın:

   ```
   az dms project task show --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name Runnowtask
   ```

   VEYA

    ```
   az dms project task show --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name Runnowtask --expand output
    ```

8. Genişletme çıktısından migrationState sorgusu da yapabilirsiniz:

    ```
    az dms project task show --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name Runnowtask --expand output --query 'properties.output[].migrationState | [0]' "READY_TO_COMPLETE"
    ```

## <a name="understanding-migration-task-status"></a>Geçiş görevinin durumunu anlama

Çıktı dosyasında geçişin ilerleme durumunu gösteren birkaç parametre vardır. Örneğin, aşağıdaki çıktı dosyasına bakın:

    ```
    "output": [                                 Database Level
          {
            "appliedChanges": 0,        //Total incremental sync applied after full load
            "cdcDeleteCounter": 0       // Total delete operation  applied after full load
            "cdcInsertCounter": 0,      // Total insert operation applied after full load
            "cdcUpdateCounter": 0,      // Total update operation applied after full load
            "databaseName": "inventory",
            "endedOn": null,
            "fullLoadCompletedTables": 2,   //Number of tables completed full load
            "fullLoadErroredTables": 0, //Number of tables that contain migration error
            "fullLoadLoadingTables": 0, //Number of tables that are in loading status
            "fullLoadQueuedTables": 0,  //Number of tables that are in queued status
            "id": "db|inventory",
            "incomingChanges": 0,       //Number of changes after full load
            "initializationCompleted": true,
            "latency": 0,
            "migrationState": "READY_TO_COMPLETE",  //Status of migration task. READY_TO_COMPLETE means the database is ready for cutover
            "resultType": "DatabaseLevelOutput",
            "startedOn": "2018-07-05T23:36:02.27839+00:00"
          },
          {
            "databaseCount": 1,
            "endedOn": null,
            "id": "dd27aa3a-ed71-4bff-ab34-77db4261101c",
            "resultType": "MigrationLevelOutput",
            "sourceServer": "138.91.123.10",
            "sourceVersion": "PostgreSQL",
            "startedOn": "2018-07-05T23:36:02.27839+00:00",
            "state": "PENDING",
            "targetServer": "builddemotarget.postgres.database.azure.com",
            "targetVersion": "Azure Database for PostgreSQL"
          },
          {                                     Table 1
            "cdcDeleteCounter": 0,
            "cdcInsertCounter": 0,
            "cdcUpdateCounter": 0,
            "dataErrorsCount": 0,
            "databaseName": "inventory",
            "fullLoadEndedOn": "2018-07-05T23:36:20.740701+00:00",  //Full load completed time
            "fullLoadEstFinishTime": "1970-01-01T00:00:00+00:00",
            "fullLoadStartedOn": "2018-07-05T23:36:15.864552+00:00",    //Full load started time
            "fullLoadTotalRows": 10,                    //Number of rows loaded in full load
            "fullLoadTotalVolumeBytes": 7056,               //Volume in Bytes in full load
            "id": "or|inventory|public|actor",          
            "lastModifiedTime": "2018-07-05T23:36:16.880174+00:00",
            "resultType": "TableLevelOutput",
            "state": "COMPLETED",                   //State of migration for this table
            "tableName": "public.catalog",              //Table name
            "totalChangesApplied": 0                //Total sync changes that applied after full load
          },
          {                                     Table 2
            "cdcDeleteCounter": 0,
            "cdcInsertCounter": 50,
            "cdcUpdateCounter": 0,
            "dataErrorsCount": 0,
            "databaseName": "inventory",
            "fullLoadEndedOn": "2018-07-05T23:36:23.963138+00:00",
            "fullLoadEstFinishTime": "1970-01-01T00:00:00+00:00",
            "fullLoadStartedOn": "2018-07-05T23:36:19.302013+00:00",
            "fullLoadTotalRows": 112,
            "fullLoadTotalVolumeBytes": 46592,
            "id": "or|inventory|public|address",
            "lastModifiedTime": "2018-07-05T23:36:20.308646+00:00",
            "resultType": "TableLevelOutput",
            "state": "COMPLETED",
            "tableName": "public.orders",
            "totalChangesApplied": 0
          }
        ],                          DMS migration task state
        "state": "Running", //Migration task state – Running means it is still listening to any changes that might come in                  
        "taskType": null
      },
      "resourceGroup": "PostgresDemo",
      "type": "Microsoft.DataMigration/services/projects/tasks"
    ```

## <a name="cutover-migration-task"></a>Tam geçiş görevi

Tam yükleme tamamlandığında veritabanı tam geçiş için hazırdır. Kaynak sunucunun gelen yeni işlemlerle ne kadar meşgul olduğuna bağlı olarak, tam yükleme tamamlandıktan sonra DMS görevi hala değişiklikleri uyguluyor olabilir.

Tüm verilerin yakalandığından emin olmak için kaynak ve hedef veritabanları arasındaki satır sayılarını doğrulayın. Örneğin, aşağıdaki komutu kullanabilirsiniz:

```
"migrationState": "READY_TO_COMPLETE", //Status of migration task. READY_TO_COMPLETE means database is ready for cutover
 "incomingChanges": 0,  //continue to check for a period of 5-10 minutes to make sure no new incoming changes that need to be applied to the target server
   "fullLoadTotalRows": 10, //full load for table 1
    "cdcDeleteCounter": 0,  //delete, insert and update counter on incremental sync after full load
    "cdcInsertCounter": 50,
    "cdcUpdateCounter": 0,
     "fullLoadTotalRows": 112,  //full load for table 2
```

1. Aşağıdaki komutu kullanarak tam veritabanı geçiş görevini gerçekleştirin:

    ```
    az dms project task cutover -h
    ```

    Örnek:

    ```
    az dms project task cutover --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name Runnowtask  --database-name Inventory
    ```

2. Tam geçişin ilerleme durumunu izlemek için aşağıdaki komutu çalıştırın:

    ```
    az dms project task show --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name Runnowtask
    ```

## <a name="service-project-task-cleanup"></a>Hizmet, proje, görev temizleme

Herhangi bir DMS görevini, projesini veya hizmetini iptal etmeniz ya da silmeniz gerekirse iptal işlemini aşağıdaki sırayla gerçekleştirin:

* Çalışan tüm görevleri iptal edin
* Görevi silin
* Projeyi silin
* DMS hizmetini silin

1. Çalışan bir görevi iptal etmek için aşağıdaki komutu kullanın:

    ```
    az dms project task cancel --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name Runnowtask
     ```

2. Çalışan bir görevi silmek için aşağıdaki komutu kullanın:
    ```
    az dms project task delete --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name Runnowtask
    ```

3. Çalışan bir projeyi iptal etmek için aşağıdaki komutu kullanın:
     ```
    az dms project task cancel -n runnowtask --project-name PGMigration -g PostgresDemo --service-name PostgresCLI
     ```

4. Çalışan bir projeyi silmek için aşağıdaki komutu kullanın:
    ```
    az dms project task delete -n runnowtask --project-name PGMigration -g PostgresDemo --service-name PostgresCLI
    ```

5. DMS hizmetini silmek için aşağıdaki komutu kullanın:

     ```
    az dms delete -g ProgresDemo -n PostgresCLI
     ```

## <a name="next-steps"></a>Sonraki adımlar

* PostgreSQL için Azure Veritabanı'na yönelik çevrimiçi geçiş gerçekleştirirken karşılaşılan bilinen sorunlar ve sınırlamalar hakkında bilgi için [PostgreSQL için Azure Veritabanı geçiş işlemleri ile ilgili bilinen sorunlar ve geçici çözümler](known-issues-azure-postgresql-online.md) başlıklı makaleye bakın.
* Azure Veritabanı Geçiş Hizmeti hakkında bilgi için [What is the Azure Database Migration Service? (Azure Veritabanı Geçiş Hizmeti nedir?)](https://docs.microsoft.com/azure/dms/dms-overview) başlıklı makaleye bakın.
* PostgreSQL için Azure veritabanı hakkında daha fazla bilgi için [PostgreSQL Için Azure veritabanı nedir?](https://docs.microsoft.com/azure/postgresql/overview)makalesine bakın.
