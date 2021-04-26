---
title: Java 4,0 SDK kullanarak Azure Cosmos DB Cassandra API ile Java uygulaması
description: Bu hızlı başlangıçta, Azure portal ve Java 4,0 SDK ile profil uygulaması oluşturmak için Azure Cosmos DB Cassandra API nasıl kullanılacağı gösterilmektedir.
ms.service: cosmos-db
author: TheovanKraay
ms.author: thvankra
ms.subservice: cosmosdb-cassandra
ms.devlang: java
ms.topic: quickstart
ms.date: 05/18/2020
ms.custom: seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: ee30af3f07c8b350393822f01833feec6e3b52e2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93099819"
---
# <a name="quickstart-build-a-java-app-to-manage-azure-cosmos-db-cassandra-api-data-v4-driver"></a>Hızlı başlangıç: Azure Cosmos DB Cassandra API verilerini (v4 sürücüsü) yönetmek için bir Java uygulaması oluşturma
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-cassandra-dotnet.md)
> * [.NET Core](create-cassandra-dotnet-core.md)
> * [Java v3](create-cassandra-java.md)
> * [Java v4](create-cassandra-java-v4.md)
> * [Node.js](create-cassandra-nodejs.md)
> * [Python](create-cassandra-python.md)
> 

Bu hızlı başlangıçta, bir Azure Cosmos DB Cassandra API hesabı oluşturur ve Java için [v4. x Apache Cassandra sürücülerini](https://github.com/datastax/java-driver/tree/4.x) kullanarak Cassandra veritabanı ve kapsayıcısı oluşturmak için GitHub 'dan kopyalanmış bir Cassandra Java uygulaması kullanırsınız. Azure Cosmos DB, genel dağıtım ve yatay ölçeklendirme özellikleri ile belge, tablo, anahtar değer ve grafik veritabanlarını hızlıca oluşturmanıza ve sorgulamanızı sağlayan çok modelli bir veritabanı hizmetidir.

## <a name="prerequisites"></a>Önkoşullar

- Etkin aboneliği olan bir Azure hesabı. [Ücretsiz bir tane oluşturun](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Veya Azure aboneliği olmadan [ücretsiz Azure Cosmos DB deneyin](https://azure.microsoft.com/try/cosmosdb/) .
- [Java Development Kit (JDK) 8](https://www.azul.com/downloads/azure-only/zulu/?&version=java-8-lts&architecture=x86-64-bit&package=jdk). `JAVA_HOME`Ortam değişkeninizi JDK 'nin yüklü olduğu klasöre işaret edin.
- [Maven ikili Arşivi](https://maven.apache.org/download.cgi). Ubuntu 'da, `apt-get install maven` Maven 'yi yüklemek için komutunu çalıştırın.
- [Git](https://www.git-scm.com/downloads). Ubuntu 'da git 'i `sudo apt-get install git` yüklemek için komutunu çalıştırın.

## <a name="create-a-database-account"></a>Veritabanı hesabı oluşturma

Bir belge veritabanı oluşturmadan önce Azure Cosmos DB ile bir Cassandra hesabı oluşturmanız gerekir.

[!INCLUDE [cosmos-db-create-dbaccount-cassandra](../../includes/cosmos-db-create-dbaccount-cassandra.md)]

## <a name="clone-the-sample-application"></a>Örnek uygulamayı kopyalama

Şimdi kod ile çalışmaya geçelim. GitHub’dan bir Cassandra uygulaması kopyalayalım, bağlantı dizesini ayarlayalım ve uygulamayı çalıştıralım. Verilerle program aracılığıyla çalışmanın ne kadar kolay olduğunu göreceksiniz. 

1. Bir komut istemi açın. `git-samples` adlı yeni bir klasör oluşturun. Ardından, komut istemini kapatın.

    ```bash
    md "C:\git-samples"
    ```

2. Git Bash gibi bir Git terminal penceresi açın ve örnek uygulamayı yüklemek üzere yeni bir klasör olarak değiştirmek için `cd` komutunu kullanın.

    ```bash
    cd "C:\git-samples"
    ```

3. Örnek depoyu kopyalamak için aşağıdaki komutu çalıştırın. Bu komut bilgisayarınızda örnek uygulamanın bir kopyasını oluşturur.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started-v4.git
    ```

## <a name="review-the-code"></a>Kodu gözden geçirin

Bu adım isteğe bağlıdır. Kodun veritabanı kaynaklarını nasıl oluşturduğunu öğrenmek istiyorsanız aşağıdaki kod parçacıklarını gözden geçirebilirsiniz. Aksi durumda, [Bağlantı dizenizi güncelleştirme](#update-your-connection-string) bölümüne atlayabilirsiniz. Bu kod parçacıklarının tümü *src/Main/Java/com/Azure/cosmosdb/Cassandra/Util/CassandraUtils. Java* dosyasından alınmıştır.  

* `CqlSession`Azure Cosmos DB Cassandra API bağlanır ve erişim için bir oturum döndürür ( `Cluster` v3 sürücüsünden nesne artık kullanılmıyor). Cassandra Konağı, bağlantı noktası, Kullanıcı adı ve parola, Azure portal bağlantı dizesi sayfası kullanılarak ayarlanır.

    ```java
        this.session = CqlSession.builder().withSslContext(sc)
                .addContactPoint(new InetSocketAddress(cassandraHost, cassandraPort)).withLocalDatacenter(region)
                .withAuthCredentials(cassandraUsername, cassandraPassword).build();
    ```


Aşağıdaki kod parçacıkları *src/Main/Java/com/Azure/cosmosdb/Cassandra/depo/UserRepository. Java* dosyasından alınır.

* Önceki bir çalıştırınızdan zaten varsa, anahtar alanını bırakın.

    ```java
    public void dropKeyspace() {
        String query = "DROP KEYSPACE IF EXISTS "+keyspace+"";
        session.execute(query);
        LOGGER.info("dropped keyspace '"+keyspace+"'");
    } 
    ```
* Yeni bir anahtar alanı oluşturulur.

    ```java
    public void createKeyspace() {
        String query = "CREATE KEYSPACE "+keyspace+" WITH REPLICATION = { 'class' : 'NetworkTopologyStrategy', 'datacenter1' : 1 }";
        session.execute(query);
        LOGGER.info("Created keyspace '"+keyspace+"'");
    }
    ```

* Yeni bir tablo oluşturulur.

   ```java
    public void createTable() {
        String query = "CREATE TABLE "+keyspace+"."+table+" (user_id int PRIMARY KEY, user_name text, user_bcity text)";
        session.execute(query);
        LOGGER.info("Created table '"+table+"'");
    }
   ```

* Kullanıcı varlıkları hazırlanan bir ifade nesnesi kullanılarak eklenir.

    ```java
    public String prepareInsertStatement() {
        final String insertStatement = "INSERT INTO  "+keyspace+"."+table+" (user_id, user_name , user_bcity) VALUES (?,?,?)";
        return insertStatement;
    }

    public void insertUser(String preparedStatement, int id, String name, String city) {
        PreparedStatement prepared = session.prepare(preparedStatement);
        BoundStatement bound = prepared.bind(id, city, name).setIdempotent(true);
        session.execute(bound);
    }
    ```

* Tüm Kullanıcı bilgilerini almak için sorgulayın.

    ```java
    public void selectAllUsers() {
        final String query = "SELECT * FROM "+keyspace+"."+table+"";
        List<Row> rows = session.execute(query).all();

        for (Row row : rows) {
            LOGGER.info("Obtained row: {} | {} | {} ", row.getInt("user_id"), row.getString("user_name"), row.getString("user_bcity"));
        }
    }
    ```

 * Tek bir Kullanıcı bilgilerini almak için sorgulayın.

    ```java
    public void selectUser(int id) {
        final String query = "SELECT * FROM "+keyspace+"."+table+" where user_id = 3";
        Row row = session.execute(query).one();

        LOGGER.info("Obtained row: {} | {} | {} ", row.getInt("user_id"), row.getString("user_name"), row.getString("user_bcity"));
    }
    ```

## <a name="update-your-connection-string"></a>Bağlantı dizenizi güncelleştirme

Bu adımda Azure portalına dönerek bağlantı dizesi bilgilerinizi kopyalayıp uygulamaya ekleyin. Bağlantı dizesi ayrıntıları, uygulamanızın barındırılan veritabanıyla iletişim kurmasına olanak tanır.

1. [Azure portal](https://portal.azure.com/)Azure Cosmos DB hesabınızda **bağlantı dizesi**' ni seçin. 

    :::image type="content" source="./media/create-cassandra-java/copy-username-connection-string-azure-portal.png" alt-text="Azure portalı, Bağlantı Dizesi sayfasından kullanıcı adını görüntüleme ve kopyalama":::

2. :::image type="icon" source="./media/create-cassandra-java/copy-button-azure-portal.png":::ILETIŞIM noktası değerini kopyalamak için ekranın sağ tarafındaki düğmeyi kullanın.

3. *C:\git-samples\azure-cosmosdb-Cassandra-Java-getting-started\java-examples\src\main\resources* klasöründeki *config. Properties* dosyasını açın. 

3. Portaldan CONTACT POINT değerini 2. satırdaki `<Cassandra endpoint host>` üzerine yapıştırın.

    *Config. Properties 2.* satırı şuna benzer görünmelidir 

    `cassandra_host=cosmos-db-quickstart.cassandra.cosmosdb.azure.com`

3. Portala geri dönün ve Kullanıcı adı değerini kopyalayın. Portaldan USERNAME değerini 4. satırdaki `<cassandra endpoint username>` üzerine yapıştırın.

    *Config. Properties 3.* satırı şuna benzer görünmelidir 

    `cassandra_username=cosmos-db-quickstart`

4. Portala geri dönün ve parola değerini kopyalayın. Portaldan PASSWORD değerini 5. satırdaki `<cassandra endpoint password>` üzerine yapıştırın.

    *Config. Properties* 'in 5. satırı şuna benzer görünmelidir 

    `cassandra_password=2Ggkr662ifxz2Mg...==`

5. 6. satırda, belirli bir TLS/SSL sertifikası kullanmak istiyorsanız, bunu `<SSL key store file location>` TLS/SSL sertifikasının konumuyla değiştirin. Bir değer sağlanmamışsa, <JAVA_HOME>/jre/lib/security/cacerts konumuna yüklenen JDK sertifikası kullanılır. 

6. 5. satırı belirli bir TLS/SSL sertifikası kullanacak şekilde değiştirdiyseniz, bu sertifikanın parolasını kullanmak için 7. satırı güncelleştirin. 

7. Kişi noktası için varsayılan bölgeyi (örn.) eklemeniz gerektiğini unutmayın `West US` . Örneğin,

    `region=West US`

    Bunun nedeni, v. 4x sürücüsünün yalnızca bir yerel DC 'nin iletişim noktasıyla eşlenme olanağı sağlar. Varsayılan dışında bir bölge eklemek istiyorsanız (Cosmos DB hesabı ilk oluşturulduğunda verilen bölgedir), kişi noktası eklerken bölgesel son ek kullanmanız gerekir, `host-westus.cassandra.cosmos.azure.com` örn.

8. *Config. Properties* dosyasını kaydedin.

## <a name="run-the-java-app"></a>Java uygulamasını çalıştırma

1. Git terminal penceresinde, `azure-cosmosdb-cassandra-java-getting-started-v4` klasörüne `cd`.

    ```git
    cd "C:\git-samples\azure-cosmosdb-cassandra-java-getting-started-v4"
    ```

2. Git terminal penceresinde, `cosmosdb-cassandra-examples.jar` dosyasını oluşturmak için aşağıdaki komutları kullanın.

    ```git
    mvn clean install
    ```

3. Git terminal penceresinde, Java uygulamasını başlatmak için aşağıdaki komutu çalıştırın.

    ```git
    java -cp target/cosmosdb-cassandra-examples.jar com.azure.cosmosdb.cassandra.examples.UserProfile
    ```

    Terminal penceresinde anahtar alanı ve tablonun oluşturulduğuna yönelik bildirimler gösterilir. Daha sonra tablodaki tüm kullanıcıları seçip döndürür ve çıktıyı gösterir, ardından kimliğe göre bir satır seçip değeri gösterir.  

    Programın yürütülmesini durdurmak için CTRL + C tuşlarına basın ve konsol penceresini kapatın.

4. Azure portalında bu yeni verileri sorgulamak, değiştirmek ve birlikte çalışmak için **Veri Gezgini**'ni açın. 

    :::image type="content" source="./media/create-cassandra-java/view-data-explorer-java-app.png" alt-text="Veri Gezgini Azure Cosmos DB verileri görüntüleme":::

## <a name="review-slas-in-the-azure-portal"></a>Azure portalında SLA'ları gözden geçirme

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Kaynakları temizleme

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, Cassandra API sahip bir Azure Cosmos DB hesabı oluşturmayı ve Cassandra veritabanı ve kapsayıcısı oluşturan Cassandra Java uygulamasını çalıştırmayı öğrendiniz. Artık Azure Cosmos DB hesabınıza daha fazla veri aktarabilirsiniz. 

> [!div class="nextstepaction"]
> [Cassandra verilerini Azure Cosmos DB’ye aktarma](cassandra-import-data.md)
