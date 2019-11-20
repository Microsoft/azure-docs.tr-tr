---
title: Coğrafi olarak dağıtılmış bir çözüm uygulama
description: Azure SQL veritabanınızı ve uygulamanızı, çoğaltılan bir veritabanına yük devretme için yapılandırmayı ve yük devretmeyi test yapmayı öğrenin.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 03/12/2019
ms.openlocfilehash: 51380d312c778380602c64cac766b050511cf994
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73810935"
---
# <a name="tutorial-implement-a-geo-distributed-database"></a>Öğretici: coğrafi olarak dağıtılmış bir veritabanı uygulama

Bir Azure SQL veritabanı ve uygulamasını uzak bir bölgeye yük devretme için yapılandırın ve yük devretme planını test edin. Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:

> [!div class="checklist"]
> - [Yük devretme grubu](sql-database-auto-failover-group.md) oluşturma
> - Bir Java uygulamasını çalıştırarak bir Azure SQL veritabanını sorgulama
> - Yük devretme testi

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.

## <a name="prerequisites"></a>Ön koşullar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager modülü Azure SQL veritabanı tarafından hala desteklenmektedir, ancak gelecekteki tüm geliştirmeler az. SQL modülüne yöneliktir. Bu cmdlet 'ler için bkz. [Azurerd. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Az Module ve Azurerd modüllerinde komutların bağımsız değişkenleri önemli ölçüde aynıdır.

Öğreticiyi tamamlayabilmeniz için aşağıdaki öğeleri yüklediğinizden emin olun:

- [Azure PowerShell](/powershell/azureps-cmdlets-docs)
- Azure SQL veritabanı 'nda tek bir veritabanı. Bir kullanım oluşturmak için
  - [Portal](sql-database-single-database-get-started.md)
  - [CLI](sql-database-cli-samples.md)
  - [PowerShell](sql-database-powershell-samples.md)

  > [!NOTE]
  > Öğretici, *AdventureWorksLT* örnek veritabanını kullanır.

- Java ve Maven, bkz. [SQL Server kullanarak uygulama oluşturma](https://www.microsoft.com/sql-server/developer-get-started/), **Java 'yı** vurgulama ve ortamınızı seçme, ardından adımları izleyin.

> [!IMPORTANT]
> Bu öğreticideki adımları gerçekleştirdiğiniz bilgisayarın genel IP adresini kullanmak için güvenlik duvarı kuralları ayarladığınızdan emin olun. Veritabanı düzeyinde güvenlik duvarı kuralları otomatik olarak ikincil sunucuya çoğaltılır.
>
> Bilgi için bkz. [veritabanı düzeyinde güvenlik duvarı kuralı oluşturma](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database) veya bilgisayarınızın sunucu düzeyinde güvenlik duvarı kuralı IÇIN kullanılan IP adresini belirleme bkz. [sunucu düzeyinde güvenlik duvarı oluşturma](sql-database-server-level-firewall-rule.md).  

## <a name="create-a-failover-group"></a>Yük devretme grubu oluşturma

Azure PowerShell kullanarak, var olan bir Azure SQL Server ve başka bir bölgedeki yeni bir Azure SQL sunucusu arasında [Yük devretme grupları](sql-database-auto-failover-group.md) oluşturun. Ardından örnek veritabanını yük devretme grubuna ekleyin.

> [!IMPORTANT]
> [!INCLUDE [sample-powershell-install](../../includes/sample-powershell-install-no-ssh.md)]

Yük devretme grubu oluşturmak için aşağıdaki betiği çalıştırın:

   ```powershell
    # Set variables for your server and database
    $adminlogin = "<your admin>"
    $password = "<your password>"
    $myresourcegroupname = "<your resource group name>"
    $mylocation = "<your resource group location>"
    $myservername = "<your existing server name>"
    $mydatabasename = "<your database name>"
    $mydrlocation = "<your disaster recovery location>"
    $mydrservername = "<your disaster recovery server name>"
    $myfailovergroupname = "<your globally unique failover group name>"

    # Create a backup server in the failover region
    New-AzSqlServer -ResourceGroupName $myresourcegroupname `
       -ServerName $mydrservername `
       -Location $mydrlocation `
       -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
          -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))

    # Create a failover group between the servers
    New-AzSqlDatabaseFailoverGroup `
       –ResourceGroupName $myresourcegroupname `
       -ServerName $myservername `
       -PartnerServerName $mydrservername  `
       –FailoverGroupName $myfailovergroupname `
       –FailoverPolicy Automatic `
       -GracePeriodWithDataLossHours 2

    # Add the database to the failover group
    Get-AzSqlDatabase `
       -ResourceGroupName $myresourcegroupname `
       -ServerName $myservername `
       -DatabaseName $mydatabasename | `
     Add-AzSqlDatabaseToFailoverGroup `
       -ResourceGroupName $myresourcegroupname `
       -ServerName $myservername `
       -FailoverGroupName $myfailovergroupname
   ```

Coğrafi çoğaltma ayarları Azure portal Ayrıca, veritabanınızı seçip, ardından **coğrafi çoğaltma** > **Ayarlar** ' da değiştirilebilir.

![Coğrafi çoğaltma ayarları](./media/sql-database-implement-geo-distributed-database/geo-replication.png)

## <a name="run-the-sample-project"></a>Örnek projeyi çalıştırma

1. Konsolunda, aşağıdaki komutla bir Maven projesi oluşturun:

   ```bash
   mvn archetype:generate "-DgroupId=com.sqldbsamples" "-DartifactId=SqlDbSample" "-DarchetypeArtifactId=maven-archetype-quickstart" "-Dversion=1.0.0"
   ```

1. **Y** yazın ve **ENTER**tuşuna basın.

1. Dizinleri yeni proje ile değiştirin.

   ```bash
   cd SqlDbSample
   ```

1. En sevdiğiniz düzenleyiciyi kullanarak, proje klasörünüzdeki *Pod. xml* dosyasını açın.

1. Aşağıdaki `dependency` bölümünü ekleyerek SQL Server bağımlılığı için Microsoft JDBC sürücüsü ekleyin. Bağımlılığın daha büyük `dependencies` bölümü içine yapıştırılabilmesi gerekir.

   ```xml
   <dependency>
     <groupId>com.microsoft.sqlserver</groupId>
     <artifactId>mssql-jdbc</artifactId>
    <version>6.1.0.jre8</version>
   </dependency>
   ```

1. `dependencies` bölümünden sonra `properties` bölümünü ekleyerek Java sürümünü belirtin:

   ```xml
   <properties>
     <maven.compiler.source>1.8</maven.compiler.source>
     <maven.compiler.target>1.8</maven.compiler.target>
   </properties>
   ```

1. `properties` bölümünden sonra `build` bölümünü ekleyerek bildirim dosyalarını destekleme:

   ```xml
   <build>
     <plugins>
       <plugin>
         <groupId>org.apache.maven.plugins</groupId>
         <artifactId>maven-jar-plugin</artifactId>
         <version>3.0.0</version>
         <configuration>
           <archive>
             <manifest>
               <mainClass>com.sqldbsamples.App</mainClass>
             </manifest>
           </archive>
        </configuration>
       </plugin>
     </plugins>
   </build>
   ```

1. *Pod. xml* dosyasını kaydedin ve kapatın.

1. İçinde bulunan *app. Java* dosyasını açın. \Sqldbsample\src\mainjava\com\sqldbsamples ve içeriği şu kodla değiştirin:

   ```java
   package com.sqldbsamples;

   import java.sql.Connection;
   import java.sql.Statement;
   import java.sql.PreparedStatement;
   import java.sql.ResultSet;
   import java.sql.Timestamp;
   import java.sql.DriverManager;
   import java.util.Date;
   import java.util.concurrent.TimeUnit;

   public class App {

      private static final String FAILOVER_GROUP_NAME = "<your failover group name>";  // add failover group name
  
      private static final String DB_NAME = "<your database>";  // add database name
      private static final String USER = "<your admin>";  // add database user
      private static final String PASSWORD = "<your password>";  // add database password

      private static final String READ_WRITE_URL = String.format("jdbc:" +
         "sqlserver://%s.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;" +
         "hostNameInCertificate=*.database.windows.net;loginTimeout=30;", +
         FAILOVER_GROUP_NAME, DB_NAME, USER, PASSWORD);
      private static final String READ_ONLY_URL = String.format("jdbc:" +
         "sqlserver://%s.secondary.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;" +
         "hostNameInCertificate=*.database.windows.net;loginTimeout=30;", +
         FAILOVER_GROUP_NAME, DB_NAME, USER, PASSWORD);

      public static void main(String[] args) {
         System.out.println("#######################################");
         System.out.println("## GEO DISTRIBUTED DATABASE TUTORIAL ##");
         System.out.println("#######################################");
         System.out.println("");

         int highWaterMark = getHighWaterMarkId();

         try {
            for(int i = 1; i < 1000; i++) {
                //  loop will run for about 1 hour
                System.out.print(i + ": insert on primary " +
                   (insertData((highWaterMark + i))?"successful":"failed"));
                TimeUnit.SECONDS.sleep(1);
                System.out.print(", read from secondary " +
                   (selectData((highWaterMark + i))?"successful":"failed") + "\n");
                TimeUnit.SECONDS.sleep(3);
            }
         } catch(Exception e) {
            e.printStackTrace();
      }
   }

   private static boolean insertData(int id) {
      // Insert data into the product table with a unique product name so we can find the product again
      String sql = "INSERT INTO SalesLT.Product " +
         "(Name, ProductNumber, Color, StandardCost, ListPrice, SellStartDate) VALUES (?,?,?,?,?,?);";

      try (Connection connection = DriverManager.getConnection(READ_WRITE_URL);
              PreparedStatement pstmt = connection.prepareStatement(sql)) {
         pstmt.setString(1, "BrandNewProduct" + id);
         pstmt.setInt(2, 200989 + id + 10000);
         pstmt.setString(3, "Blue");
         pstmt.setDouble(4, 75.00);
         pstmt.setDouble(5, 89.99);
         pstmt.setTimestamp(6, new Timestamp(new Date().getTime()));
         return (1 == pstmt.executeUpdate());
      } catch (Exception e) {
         return false;
      }
   }

   private static boolean selectData(int id) {
      // Query the data previously inserted into the primary database from the geo replicated database
      String sql = "SELECT Name, Color, ListPrice FROM SalesLT.Product WHERE Name = ?";

      try (Connection connection = DriverManager.getConnection(READ_ONLY_URL);
              PreparedStatement pstmt = connection.prepareStatement(sql)) {
         pstmt.setString(1, "BrandNewProduct" + id);
         try (ResultSet resultSet = pstmt.executeQuery()) {
            return resultSet.next();
         }
      } catch (Exception e) {
         return false;
      }
   }

   private static int getHighWaterMarkId() {
      // Query the high water mark id stored in the table to be able to make unique inserts
      String sql = "SELECT MAX(ProductId) FROM SalesLT.Product";
      int result = 1;
      try (Connection connection = DriverManager.getConnection(READ_WRITE_URL);
              Statement stmt = connection.createStatement();
              ResultSet resultSet = stmt.executeQuery(sql)) {
         if (resultSet.next()) {
             result =  resultSet.getInt(1);
            }
         } catch (Exception e) {
          e.printStackTrace();
         }
         return result;
      }
   }
   ```

1. *App. Java* dosyasını kaydedin ve kapatın.

1. Komut konsolunda aşağıdaki komutu çalıştırın:

   ```bash
   mvn package
   ```

1. El ile durduruluncaya kadar yaklaşık 1 saat boyunca çalışacak uygulamayı başlatın ve yük devretme testini çalıştırma zamanı sağlar.

   ```bash
   mvn -q -e exec:java "-Dexec.mainClass=com.sqldbsamples.App"
   ```

   ```output
   #######################################
   ## GEO DISTRIBUTED DATABASE TUTORIAL ##
   #######################################

   1. insert on primary successful, read from secondary successful
   2. insert on primary successful, read from secondary successful
   3. insert on primary successful, read from secondary successful
   ...
   ```

## <a name="test-failover"></a>Yük devretme testi

Yük devretmenin benzetimini yapmak ve uygulama sonuçlarını gözlemlemek için aşağıdaki komut dosyalarını çalıştırın. Veritabanı geçişi sırasında bazı ekleme ve seçimi nasıl başarısız olacağını fark edebilirsiniz.

Aşağıdaki komutla, test sırasında olağanüstü durum kurtarma sunucusunun rolünü de denetleyebilirsiniz:

   ```powershell
   (Get-AzSqlDatabaseFailoverGroup `
      -FailoverGroupName $myfailovergroupname `
      -ResourceGroupName $myresourcegroupname `
      -ServerName $mydrservername).ReplicationRole
   ```

Yük devretmeyi test etmek için:

1. Yük devretme grubunun el ile yük devretmesini başlatın:

   ```powershell
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $myresourcegroupname `
      -ServerName $mydrservername `
      -FailoverGroupName $myfailovergroupname
   ```

1. Yük devretme grubunu birincil sunucuya geri çevir:

   ```powershell
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $myresourcegroupname `
      -ServerName $myservername `
      -FailoverGroupName $myfailovergroupname
   ```

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, uzak bir bölgeye yük devretme için bir Azure SQL veritabanı ve uygulaması yapılandırmış ve bir yük devretme planını test edersiniz. Şunları öğrendiniz:

> [!div class="checklist"]
> - Coğrafi çoğaltma yük devretme grubu oluşturma
> - Bir Java uygulamasını çalıştırarak bir Azure SQL veritabanını sorgulama
> - Yük devretme testi

DMS kullanarak geçiş yapmak için bir sonraki öğreticiye ilerleyin.

> [!div class="nextstepaction"]
> [DMS kullanarak SQL Server Azure SQL veritabanı yönetilen örneği 'ne geçirme](../dms/tutorial-sql-server-to-managed-instance.md)
