---
title: Coğrafi olarak dağıtılmış bir çözüm uygulama
description: Azure SQL veritabanı ve istemci uygulamasındaki veritabanınızı, çoğaltılan bir veritabanına yük devretme için yapılandırmayı ve yük devretmeyi test yapmayı öğrenin.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: sqldbrb=1, devx-track-azurecli, devx-track-azurepowershell
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein
ms.date: 03/12/2019
ms.openlocfilehash: 89d285a56553f5c521d1edbc92786debd4a92e32
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101659299"
---
# <a name="tutorial-implement-a-geo-distributed-database-azure-sql-database"></a>Öğretici: coğrafi olarak dağıtılmış bir veritabanı uygulama (Azure SQL veritabanı)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

SQL veritabanı ve istemci uygulamasındaki bir veritabanını uzak bir bölgeye yük devretme için yapılandırın ve yük devretme planını test edin. Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:

> [!div class="checklist"]
>
> - [Yük devretme grubu](auto-failover-group-overview.md) oluşturma
> - SQL veritabanında bir veritabanını sorgulamak için bir Java uygulaması çalıştırma
> - Yük devretme testi

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!IMPORTANT]
> PowerShell Azure Resource Manager modülü Azure SQL veritabanı tarafından hala desteklenmektedir, ancak gelecekteki tüm geliştirmeler az. SQL modülüne yöneliktir. Bu cmdlet 'ler için bkz. [Azurerd. SQL](/powershell/module/AzureRM.Sql/). Az Module ve Azurerd modüllerinde komutların bağımsız değişkenleri önemli ölçüde aynıdır.

Öğreticiyi tamamlayabilmeniz için aşağıdaki öğeleri yüklediğinizden emin olun:

- [Azure PowerShell](/powershell/azure/)
- Azure SQL veritabanı 'nda tek bir veritabanı. Bir kullanım oluşturmak için
  - [Azure portalı](single-database-create-quickstart.md)
  - [Azure CLI](az-cli-script-samples-content-guide.md)
  - [PowerShell](powershell-script-content-guide.md)

  > [!NOTE]
  > Öğretici, *AdventureWorksLT* örnek veritabanını kullanır.

- Java ve Maven, bkz. [SQL Server kullanarak uygulama oluşturma](https://www.microsoft.com/sql-server/developer-get-started/), **Java 'yı** vurgulama ve ortamınızı seçme, ardından adımları izleyin.

> [!IMPORTANT]
> Bu öğreticideki adımları gerçekleştirdiğiniz bilgisayarın genel IP adresini kullanmak için güvenlik duvarı kuralları ayarladığınızdan emin olun. Veritabanı düzeyinde güvenlik duvarı kuralları otomatik olarak ikincil sunucuya çoğaltılır.
>
> Bilgi için bkz. [veritabanı düzeyinde güvenlik duvarı kuralı oluşturma](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database) veya bilgisayarınızın sunucu düzeyinde güvenlik duvarı kuralı IÇIN kullanılan IP adresini belirleme bkz. [sunucu düzeyinde güvenlik duvarı oluşturma](firewall-create-server-level-portal-quickstart.md).  

## <a name="create-a-failover-group"></a>Yük devretme grubu oluşturma

Azure PowerShell kullanarak, var olan bir sunucu ile başka bir bölgedeki yeni bir sunucu arasında [Yük devretme grupları](auto-failover-group-overview.md) oluşturun. Ardından örnek veritabanını yük devretme grubuna ekleyin.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

> [!IMPORTANT]
> [!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

Yük devretme grubu oluşturmak için aşağıdaki betiği çalıştırın:

```powershell
$admin = "<adminName>"
$password = "<password>"
$resourceGroup = "<resourceGroupName>"
$location = "<resourceGroupLocation>"
$server = "<serverName>"
$database = "<databaseName>"
$drLocation = "<disasterRecoveryLocation>"
$drServer = "<disasterRecoveryServerName>"
$failoverGroup = "<globallyUniqueFailoverGroupName>"

# create a backup server in the failover region
New-AzSqlServer -ResourceGroupName $resourceGroup -ServerName $drServer `
    -Location $drLocation -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
    -ArgumentList $admin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))

# create a failover group between the servers
New-AzSqlDatabaseFailoverGroup –ResourceGroupName $resourceGroup -ServerName $server `
    -PartnerServerName $drServer –FailoverGroupName $failoverGroup –FailoverPolicy Automatic -GracePeriodWithDataLossHours 2

# add the database to the failover group
Get-AzSqlDatabase -ResourceGroupName $resourceGroup -ServerName $server -DatabaseName $database | `
    Add-AzSqlDatabaseToFailoverGroup -ResourceGroupName $resourceGroup -ServerName $server -FailoverGroupName $failoverGroup
```

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

> [!IMPORTANT]
> `az login`Azure 'da oturum açmak için ' i çalıştırın.

```azurecli
$admin = "<adminName>"
$password = "<password>"
$resourceGroup = "<resourceGroupName>"
$location = "<resourceGroupLocation>"
$server = "<serverName>"
$database = "<databaseName>"
$drLocation = "<disasterRecoveryLocation>" # must be different then $location
$drServer = "<disasterRecoveryServerName>"
$failoverGroup = "<globallyUniqueFailoverGroupName>"

# create a backup server in the failover region
az sql server create --admin-password $password --admin-user $admin `
    --name $drServer --resource-group $resourceGroup --location $drLocation

# create a failover group between the servers
az sql failover-group create --name $failoverGroup --partner-server $drServer `
    --resource-group $resourceGroup --server $server --add-db $database `
    --failover-policy Automatic --grace-period 2
```

* * *

Coğrafi çoğaltma ayarları Azure Portal Ayrıca, veritabanınızı seçip **Ayarlar**  >  **coğrafi çoğaltma** ayarları ' nı seçerek de değiştirebilirsiniz.

![Coğrafi çoğaltma ayarları](./media/geo-distributed-application-configure-tutorial/geo-replication.png)

## <a name="run-the-sample-project"></a>Örnek projeyi çalıştırma

1. Konsolunda, aşağıdaki komutla bir Maven projesi oluşturun:

   ```bash
   mvn archetype:generate "-DgroupId=com.sqldbsamples" "-DartifactId=SqlDbSample" "-DarchetypeArtifactId=maven-archetype-quickstart" "-Dversion=1.0.0"
   ```

1. **Y** yazın ve **ENTER** tuşuna basın.

1. Dizinleri yeni proje ile değiştirin.

   ```bash
   cd SqlDbSample
   ```

1. En sevdiğiniz düzenleyiciyi kullanarak proje klasörünüzdeki *pom.xml* dosyasını açın.

1. Aşağıdaki bölümü ekleyerek SQL Server bağımlılığı için Microsoft JDBC sürücüsü ekleyin `dependency` . Bağımlılığın daha büyük bölüm içinde yapıştırılabilmesi gerekir `dependencies` .

   ```xml
   <dependency>
     <groupId>com.microsoft.sqlserver</groupId>
     <artifactId>mssql-jdbc</artifactId>
    <version>6.1.0.jre8</version>
   </dependency>
   ```

1. Bölümün sonrasına bölüm ekleyerek Java sürümünü belirtin `properties` `dependencies` :

   ```xml
   <properties>
     <maven.compiler.source>1.8</maven.compiler.source>
     <maven.compiler.target>1.8</maven.compiler.target>
   </properties>
   ```

1. Bölümünden sonraki bölümü ekleyerek bildirim dosyalarını destekleme `build` `properties` :

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

1. *Pom.xml* dosyasını kaydedin ve kapatın.

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
                   (insertData((highWaterMark + i)) ? "successful" : "failed"));
                TimeUnit.SECONDS.sleep(1);
                System.out.print(", read from secondary " +
                   (selectData((highWaterMark + i)) ? "successful" : "failed") + "\n");
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

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Aşağıdaki komutla, test sırasında olağanüstü durum kurtarma sunucusunun rolünü kontrol edebilirsiniz:

```powershell
(Get-AzSqlDatabaseFailoverGroup -FailoverGroupName $failoverGroup `
    -ResourceGroupName $resourceGroup -ServerName $drServer).ReplicationRole
```

Yük devretmeyi test etmek için:

1. Yük devretme grubunun el ile yük devretmesini başlatın:

   ```powershell
   Switch-AzSqlDatabaseFailoverGroup -ResourceGroupName $resourceGroup `
    -ServerName $drServer -FailoverGroupName $failoverGroup
   ```

1. Yük devretme grubunu birincil sunucuya geri çevir:

   ```powershell
   Switch-AzSqlDatabaseFailoverGroup -ResourceGroupName $resourceGroup `
    -ServerName $server -FailoverGroupName $failoverGroup
   ```

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

Aşağıdaki komutla, test sırasında olağanüstü durum kurtarma sunucusunun rolünü kontrol edebilirsiniz:

```azurecli
az sql failover-group show --name $failoverGroup --resource-group $resourceGroup --server $drServer
```

Yük devretmeyi test etmek için:

1. Yük devretme grubunun el ile yük devretmesini başlatın:

   ```azurecli
   az sql failover-group set-primary --name $failoverGroup --resource-group $resourceGroup --server $drServer
   ```

1. Yük devretme grubunu birincil sunucuya geri çevir:

   ```azurecli
   az sql failover-group set-primary --name $failoverGroup --resource-group $resourceGroup --server $server
   ```

* * *

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, Azure SQL veritabanı 'nda bir veritabanını ve bir uzak bölgeye yük devretme ve yük devretme planını test eden bir uygulamayı yapılandırdınız. Şunları öğrendiniz:

> [!div class="checklist"]
>
> - Coğrafi çoğaltma yük devretme grubu oluşturma
> - SQL veritabanında bir veritabanını sorgulamak için bir Java uygulaması çalıştırma
> - Yük devretme testi

Bir yük devretme grubuna Azure SQL yönetilen örneği örneği ekleme hakkında sonraki öğreticiye ilerleyin:

> [!div class="nextstepaction"]
> [Bir yük devretme grubuna Azure SQL yönetilen örneği örneği ekleme](../managed-instance/failover-group-add-instance-tutorial.md)
