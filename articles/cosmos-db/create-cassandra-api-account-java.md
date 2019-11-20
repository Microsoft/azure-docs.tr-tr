---
title: 'Öğretici: Java uygulaması kullanarak Cassandra API hesabı oluşturma-Azure Cosmos DB'
description: Bu öğreticide, bir Cassandra API hesabının nasıl oluşturulacağı, bir veritabanının nasıl ekleneceği (keyspace olarak da bilinir) ve bir Java uygulaması kullanılarak bu hesaba bir tablo nasıl ekleneceği gösterilmektedir.
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: tutorial
ms.date: 12/06/2018
ms.custom: seodec18
Customer intent: As a developer, I want to build a Java application to access and manage Azure Cosmos DB resources so that customers can store key/value data and utilize the global distribution, elastic scaling, multi-master, and other capabilities offered by Azure Cosmos DB.
ms.openlocfilehash: d2d4d568f53c426b063f3285cc8d3d510c3db440
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/26/2019
ms.locfileid: "70034609"
---
# <a name="tutorial-create-a-cassandra-api-account-in-azure-cosmos-db-by-using-a-java-application-to-store-keyvalue-data"></a>Öğretici: Anahtar/değer verilerini depolamak için bir Java uygulaması kullanarak Azure Cosmos DB Cassandra API hesabı oluşturma

Bir geliştirici olarak, anahtar/değer çiftleri kullanan uygulamalarınız olabilir. Anahtar/değer verilerini depolamak için Azure Cosmos DB Cassandra API hesabı kullanabilirsiniz. Bu öğreticide, bir Java uygulamasını kullanarak Azure Cosmos DB bir Cassandra API hesabı oluşturma, bir veritabanı ekleme (keyspace olarak da adlandırılır) ve bir tablo ekleme açıklanır. Java uygulaması, Kullanıcı KIMLIĞI, Kullanıcı adı ve Kullanıcı şehri gibi ayrıntıları içeren bir kullanıcı veritabanı oluşturmak için [Java sürücüsünü](https://github.com/datastax/java-driver) kullanır.  

Bu öğretici aşağıdaki görevleri kapsar:

> [!div class="checklist"]
> * Cassandra veritabanı hesabı oluşturma
> * Hesabın bağlantı dizesini alma
> * Maven projesi ve bağımlılıkları oluşturma
> * Veritabanı ve tablo ekleme
> * Uygulamayı çalıştırma

## <a name="prerequisites"></a>Önkoşullar 

* Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) oluşturun. 

* [Java Development Kit 'in (JDK)](https://aka.ms/azure-jdks)en son sürümünü alın. 

* [Maven](https://maven.apache.org/) Ikili arşivini [indirin](https://maven.apache.org/download.cgi) ve [yükleyin](https://maven.apache.org/install.html) . 
  - Ubuntu’da Maven’i yüklemek için `apt-get install maven` komutunu çalıştırabilirsiniz. 

## <a name="create-a-database-account"></a>Veritabanı hesabı oluşturma 

1. [Azure Portal](https://portal.azure.com/) oturum açın. 

2. **Kaynak oluştur** > **Veritabanları** > **Azure Cosmos DB** seçeneğini belirleyin. 

3. **Yeni hesap** bölmesinde yeni Azure Cosmos hesabının ayarlarını girin. 

   |Ayar   |Önerilen değer  |Açıklama  |
   |---------|---------|---------|
   |Kimlik   |   Benzersiz bir ad girin    | Bu Azure Cosmos hesabını tanımlamak için benzersiz bir ad girin. <br/><br/>Cassandra.cosmosdb.azure.com, iletişim noktanızı oluşturmak için sağladığınız KIMLIĞE eklendiği için benzersiz ancak tanımlanabilir bir KIMLIK kullanın.         |
   |API    |  Cassandra   |  API, oluşturulacak hesap türünü belirler. <br/> **Cassandra**' ı seçin, bu makalede Cassandra sorgu DILI (CQL) sözdizimi kullanılarak sorgulanabilecek geniş sütunlu bir veritabanı oluşturacaksınız.  |
   |Subscription    |  Aboneliğiniz        |  Bu Azure Cosmos hesabı için kullanmak istediğiniz Azure aboneliğini seçin.        |
   |Kaynak Grubu   | Ad girin    |  **Yeni oluştur**' u seçin ve ardından hesabınız için yeni bir kaynak grubu adı girin. Kolaylık olması için kimliğinizle aynı adı kullanabilirsiniz.    |
   |Konum    |  Kullanıcılarınıza en yakın bölgeyi seçin    |  Azure Cosmos hesabınızın barındırılacağı coğrafi konumu seçin. Verilere en hızlı erişim sağlamak için kullanıcılarınıza en yakın konumu kullanın.    |

   ![Portalla hesap oluşturma](./media/create-cassandra-api-account-java/create-account.png)

4. **Oluştur**’u seçin. <br/>Hesabın oluşturulması birkaç dakika sürer. Kaynak oluşturulduktan sonra, portalın sağ tarafında **dağıtım başarılı** bildirimini görebilirsiniz.

## <a name="get-the-connection-details-of-your-account"></a>Hesabınızın bağlantı ayrıntılarını alma  

Azure portal bağlantı dizesi bilgilerini alın ve Java yapılandırma dosyasına kopyalayın. Bağlantı dizesi, uygulamanızın barındırılan veritabanıyla iletişim kurmasına olanak tanır. 

1. [Azure Portal](https://portal.azure.com/)Azure Cosmos hesabınıza gidin. 

2. **Bağlantı dizesi** bölmesini açın.  

3. **TEMAS NOKTASI**, **BAĞLANTI NOKTASI**, **KULLANICI ADI** ve **BİRİNCİL PAROLA** değerlerini sonraki adımlarda kullanmak üzere kopyalayın.

## <a name="create-the-project-and-the-dependencies"></a>Projeyi ve bağımlılıkları oluşturma 

Bu makalede kullandığınız Java örnek projesi GitHub ' da barındırılır. Bu belgedeki adımları çalıştırabilir veya örneği [Azure-Cosmos-DB-Cassandra-Java-alma-başlatma](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started) deposundan indirebilirsiniz. 

Dosyaları indirdikten sonra, `java-examples\src\main\resources\config.properties` dosyadaki bağlantı dizesi bilgilerini güncelleştirin ve çalıştırın.  

```java
cassandra_host=<FILLME_with_CONTACT POINT> 
cassandra_port = 10350 
cassandra_username=<FILLME_with_USERNAME> 
cassandra_password=<FILLME_with_PRIMARY PASSWORD> 
```

Sıfırdan örnek oluşturmak için aşağıdaki adımları kullanın: 

1. Terminalden veya komut isteminden, Cassandra-demo adlı yeni bir Maven projesi oluşturun. 

   ```bash
   mvn archetype:generate -DgroupId=com.azure.cosmosdb.cassandra -DartifactId=cassandra-demo -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false 
   ```
 
2. `cassandra-demo` klasörünü bulun. Metin düzenleyicisi kullanarak, oluşturulmuş olan `pom.xml` dosyasını açın. 

   [Pod. xml](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/master/java-examples/pom.xml) dosyasında gösterildiği gibi, projeniz Için gereken Cassandra bağımlılıklarını ve yapı eklentilerini ekleyin.  

3. `cassandra-demo\src\main` klasörünün altında `resources` adlı yeni bir klasör oluşturun.  Resources klasörünün altına config.properties ve log4j.properties dosyalarını ekleyin:

   - [Config. Properties](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/master/java-examples/src/main/resources/config.properties) dosyası Cassandra API hesabının bağlantı uç noktasını ve anahtar değerlerini depolar. 
   
   - [Log4J. Properties](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/master/java-examples/src/main/resources/log4j.properties) dosyası Cassandra API etkileşimde bulunmak için gereken günlük düzeyini tanımlar.  

4. `src/main/java/com/azure/cosmosdb/cassandra/` Klasöre gidin. Cassandra klasörünün içinde `utils` adlı başka bir klasör oluşturun. Yeni klasörde Cassandra API hesabına bağlanmak için gereken yardımcı program sınıfları depolanır. 

   Kümeyi oluşturmak ve Cassandra oturumlarını açıp kapatmak için [CassandraUtils](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/master/java-examples/src/main/java/com/azure/cosmosdb/cassandra/util/CassandraUtils.java) sınıfını ekleyin. Küme, Azure Cosmos DB Cassandra API hesabına bağlanır ve erişim için bir oturum döndürür. Config.properties dosyasından bağlantı dizesi bilgisini okumak için [Configurations](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/master/java-examples/src/main/java/com/azure/cosmosdb/cassandra/util/Configurations.java) sınıfını kullanın. 

5. Java örneği Kullanıcı adı, Kullanıcı KIMLIĞI ve Kullanıcı şehri gibi Kullanıcı bilgileri içeren bir veritabanı oluşturur. Main işlevindeki kullanıcı ayrıntılarına erişmek için get ve set yöntemlerini tanımlamanız gerekir.
 
   Get ve set yöntemleri ile `src/main/java/com/azure/cosmosdb/cassandra/` klasör altında bir [User. Java](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/master/java-examples/src/main/java/com/azure/cosmosdb/cassandra/User.java) sınıfı oluşturun. 

## <a name="add-a-database-and-a-table"></a>Veritabanı ve tablo ekleme  

Bu bölüm, CQL kullanarak bir veritabanının (keyspace) ve tablonun nasıl ekleneceğini açıklar.

1. `src\main\java\com\azure\cosmosdb\cassandra` klasörünün altında `repository` adlı yeni bir klasör oluşturun. 

2. `UserRepository` Java sınıfını oluşturun ve buna aşağıdaki kodu ekleyin: 

   ```java
   package com.azure.cosmosdb.cassandra.repository; 
   import java.util.List; 
   import com.datastax.driver.core.BoundStatement; 
   import com.datastax.driver.core.PreparedStatement; 
   import com.datastax.driver.core.Row; 
   import com.datastax.driver.core.Session; 
   import org.slf4j.Logger; 
   import org.slf4j.LoggerFactory; 
   
   /** 
    * Create a Cassandra session 
    */ 
   public class UserRepository { 
   
       private static final Logger LOGGER = LoggerFactory.getLogger(UserRepository.class); 
       private Session session; 
       public UserRepository(Session session) { 
           this.session = session; 
       } 
   
       /** 
       * Create keyspace uprofile in cassandra DB 
        */ 
   
       public void createKeyspace() { 
            final String query = "CREATE KEYSPACE IF NOT EXISTS uprofile WITH REPLICATION = { 'class' : 'NetworkTopologyStrategy', 'datacenter1' : 1 }"; 
           session.execute(query); 
           LOGGER.info("Created keyspace 'uprofile'"); 
       } 
   
       /** 
        * Create user table in cassandra DB 
        */ 
   
       public void createTable() { 
           final String query = "CREATE TABLE IF NOT EXISTS uprofile.user (user_id int PRIMARY KEY, user_name text, user_bcity text)"; 
           session.execute(query); 
           LOGGER.info("Created table 'user'"); 
       } 
   } 
   ```

3. `src\main\java\com\azure\cosmosdb\cassandra` klasörünü bulun ve `examples` adlı yeni bir alt klasör oluşturun.

4. `UserProfile` Java sınıfını oluşturun. Bu sınıf, daha önce tanımladığınız createKeyspace ve createTable yöntemlerini çağıran main yöntemini içerir: 

   ```java
   package com.azure.cosmosdb.cassandra.examples; 
   import java.io.IOException; 
   import com.azure.cosmosdb.cassandra.repository.UserRepository; 
   import com.azure.cosmosdb.cassandra.util.CassandraUtils; 
   import com.datastax.driver.core.PreparedStatement; 
   import com.datastax.driver.core.Session; 
   import org.slf4j.Logger; 
   import org.slf4j.LoggerFactory; 
   
   /** 
    * Example class which will demonstrate following operations on Cassandra Database on CosmosDB 
    * - Create Keyspace 
    * - Create Table 
    * - Insert Rows 
    * - Select all data from a table 
    * - Select a row from a table 
    */ 
   
   public class UserProfile { 
   
       private static final Logger LOGGER = LoggerFactory.getLogger(UserProfile.class); 
       public static void main(String[] s) throws Exception { 
           CassandraUtils utils = new CassandraUtils(); 
           Session cassandraSession = utils.getSession(); 
   
           try { 
               UserRepository repository = new UserRepository(cassandraSession); 
               //Create keyspace in cassandra database 
               repository.createKeyspace(); 
               //Create table in cassandra database 
               repository.createTable(); 
   
           } finally { 
               utils.close(); 
               LOGGER.info("Please delete your table after verifying the presence of the data in portal or from CQL"); 
           } 
       } 
   } 
   ```
 
## <a name="run-the-app"></a>Uygulamayı çalıştırma 

1. Bir komut istemi veya terminal penceresi açın. Aşağıdaki kod bloğunu yapıştırın. 

   Bu kod, dizini (CD) projeyi oluşturduğunuz klasör yoluyla değiştirir. Ardından hedef klasörde `cosmosdb-cassandra-examples.jar` dosyasını oluşturmak için `mvn clean install` komutunu çalıştırır. Son olarak, Java uygulamasını çalıştırır.

   ```bash
   cd cassandra-demo

   mvn clean install 

   java -cp target/cosmosdb-cassandra-examples.jar com.azure.cosmosdb.cassandra.examples.UserProfile 
   ```

   Terminal penceresinde anahtar alanı ve tablonun oluşturulduğuna yönelik bildirimler gösterilir. 
   
2. Şimdi, keyspace'in ve tablonun oluşturulduğunu onaylamak için Azure portalda **Veri Gezgini**'ni açın.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, bir Java uygulaması kullanarak Azure Cosmos DB, veritabanında ve tabloda bir Cassandra API hesabı oluşturmayı öğrendiniz. Şimdi bir sonraki makaleye geçebilirsiniz:

> [!div class="nextstepaction"]
> [Örnek verileri Cassandra API'si tablosuna yükleme](cassandra-api-load-data.md).
