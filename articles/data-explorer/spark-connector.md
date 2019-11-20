---
title: Azure Veri Gezgini ve Spark kümeleri arasında veri taşımak için Azure Veri Gezgini bağlayıcısını Apache Spark kullanın.
description: Bu konu başlığı altında, verileri Azure Veri Gezgini ve Apache Spark kümeleri arasında nasıl taşıyacağınız gösterilmektedir.
author: orspod
ms.author: orspodek
ms.reviewer: michazag
ms.service: data-explorer
ms.topic: conceptual
ms.date: 4/29/2019
ms.openlocfilehash: 6a95cbad161906bd12a608880ac694d6bdf1ed27
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/05/2019
ms.locfileid: "70383061"
---
# <a name="azure-data-explorer-connector-for-apache-spark-preview"></a>Apache Spark için Azure Veri Gezgini Bağlayıcısı (Önizleme)

[Apache Spark](https://spark.apache.org/) , büyük ölçekli veri işleme için Birleşik bir analiz altyapısıdır. Azure Veri Gezgini, büyük hacimde veri üzerinde gerçek zamanlı analizler için hızlı ve tam olarak yönetilen bir veri analizi hizmetidir. 

Spark için Azure Veri Gezgini Bağlayıcısı, her iki özelliği de kullanmak üzere verileri Azure Veri Gezgini ve Spark kümeleri arasında taşımak için veri kaynağını ve veri havuzunu uygular. Azure Veri Gezgini ve Apache Spark kullanarak, makine öğrenimi (ML), ayıklama-dönüştürme-yükleme (ETL) ve Log Analytics gibi veri odaklı senaryoları hedefleyen hızlı ve ölçeklenebilir uygulamalar oluşturabilirsiniz. Azure Veri Gezgini yazma işlemi, Batch ve akış modunda yapılabilir.
Azure Veri Gezgini okuma, sütun ayıklama ve koşul push'yi destekler, bu da Azure Veri Gezgini verileri filtreleyerek aktarılan verilerin hacmini azaltır.

Azure Veri Gezgini Spark Bağlayıcısı, herhangi bir Spark kümesinde çalışabilen [Açık kaynaklı bir projem](https://github.com/Azure/azure-kusto-spark) . Azure Veri Gezgini Spark Bağlayıcısı, Azure Veri Gezgini, standart Spark kaynak ve yazma, okuma ve writeStream gibi havuz işlemleri için geçerli bir veri deposu sağlar. 

> [!NOTE]
> Aşağıdaki örneklerden bazıları [Azure Databricks](https://docs.azuredatabricks.net/) Spark kümesine başvurmakla birlikte, Azure Veri Gezgini Spark Bağlayıcısı Databricks veya diğer Spark dağıtımında doğrudan bağımlılıklar almaz.

## <a name="prerequisites"></a>Önkoşullar

* [Azure Veri Gezgini kümesi ve veritabanı oluşturma](/azure/data-explorer/create-cluster-database-portal) 
* Spark kümesi oluşturma
* Aşağıdaki [kusto Java SDK](/azure/kusto/api/java/kusto-java-client-library) kitaplıkları dahil olmak üzere [bağımlılıklarda](https://github.com/Azure/azure-kusto-spark#dependencies) listelenen Azure Veri Gezgini bağlayıcı kitaplığı ve kitaplıklarını yükler:
    * [Kusto veri Istemcisi](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-data)
    * [Kusto ınest Istemcisi](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-ingest)
* [Spark 2,4, Scala 2,11](https://github.com/Azure/azure-kusto-spark/releases) için önceden oluşturulmuş kitaplıklar

## <a name="how-to-build-the-spark-connector"></a>Spark bağlayıcısını oluşturma

Spark Bağlayıcısı, aşağıda açıklandığı gibi [kaynaklardan](https://github.com/Azure/azure-kusto-spark) oluşturulabilir.

> [!NOTE]
> Bu adım isteğe bağlıdır. Önceden oluşturulmuş kitaplıkları kullanıyorsanız [Spark kümesi kurulumuna](#spark-cluster-setup)gidin.

### <a name="build-prerequisites"></a>Derleme önkoşulları

* Java 1,8 SDK yüklü
* [Maven 3. x](https://maven.apache.org/download.cgi) yüklendi
* Apache Spark sürüm 2.4.0 veya üzeri

> [!TIP]
> 2.3. x sürümleri de desteklenir, ancak Pod. xml bağımlılıklarında bazı değişiklikler gerektirebilir.

Maven proje tanımlarını kullanan Scala/Java uygulamaları için, uygulamanızı aşağıdaki yapıtla bağlayın (en son sürüm farklılık gösterebilir):

```Maven
   <dependency>
     <groupId>com.microsoft.azure</groupId>
     <artifactId>spark-kusto-connector</artifactId>
     <version>1.0.0-Beta-02</version>
   </dependency>
```

### <a name="build-commands"></a>Derleme komutları

Jar oluşturmak ve tüm testleri çalıştırmak için:

```
mvn clean package
```

Jar oluşturmak için tüm testleri çalıştırın ve yerel Maven deponuza jar 'i yüklemek için:

```
mvn clean install
```

Daha fazla bilgi için bkz. [bağlayıcı kullanımı](https://github.com/Azure/azure-kusto-spark#usage).

## <a name="spark-cluster-setup"></a>Spark kümesi kurulumu

> [!NOTE]
> Aşağıdaki adımları gerçekleştirirken en son Azure Veri Gezgini Spark Bağlayıcısı sürümünün kullanılması önerilir:

1. Spark 2,4 ve Scala 2,11 kullanarak Azure Databricks kümesine bağlı olarak aşağıdaki Spark kümesi ayarlarını yapın: 

    ![Databricks kümesi ayarları](media/spark-connector/databricks-cluster.png)

1. Azure Veri Gezgini bağlayıcı kitaplığı 'nı içeri aktarma:

    ![Azure Veri Gezgini kitaplığı 'nı içeri aktarma](media/spark-connector/db-create-library.png)

1. Ek bağımlılıklar ekleyin (Maven 'den kullanılmışsa gerekli değildir):

    ![Bağımlılık Ekle](media/spark-connector/db-dependencies.png)

    > [!TIP]
    > Her Spark sürümü için doğru Java yayın sürümü [burada](https://github.com/Azure/azure-kusto-spark#dependencies)bulunur.

1. Tüm gerekli kitaplıkların yüklü olduğunu doğrulayın:

    ![Kitaplıkların yüklü olduğunu doğrulama](media/spark-connector/db-libraries-view.png)

## <a name="authentication"></a>Authentication

Azure Veri Gezgini Spark Bağlayıcısı, Azure [ad uygulaması](#azure-ad-application-authentication), [Azure AD erişim belirteci](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#direct-authentication-with-access-token), [cihaz kimlik doğrulaması](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#device-authentication) (üretim dışı senaryolar için) veya [Azure anahtarı kullanarak Azure Active Directory (Azure AD) ile kimlik doğrulaması yapmanıza olanak sağlar Kasa](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#key-vault). Kullanıcının Azure-keykasa paketini yüklemesi ve Key Vault kaynağına erişmek için uygulama kimlik bilgilerini sağlaması gerekir.

### <a name="azure-ad-application-authentication"></a>Azure AD uygulama kimlik doğrulaması

En basit ve ortak kimlik doğrulama yöntemi. Bu yöntem, Azure Veri Gezgini Spark bağlayıcı kullanımı için önerilir.

|Özellikler  |Açıklama  |
|---------|---------|
|**KUSTO_AAD_CLIENT_ID**     |   Azure AD uygulaması (istemci) tanımlayıcısı.      |
|**KUSTO_AAD_AUTHORITY_ID**     |  Azure AD kimlik doğrulama yetkilisi. Azure AD dizini (kiracı) KIMLIĞI.        |
|**KUSTO_AAD_CLIENT_PASSWORD**    |    İstemci için Azure AD uygulama anahtarı.     |

### <a name="azure-data-explorer-privileges"></a>Azure Veri Gezgini ayrıcalıkları

Azure Veri Gezgini kümesinde aşağıdaki ayrıcalıkların verilmesi gerekir:

* Okuma (veri kaynağı) için Azure AD uygulaması, hedef veritabanında *Görüntüleyici* ayrıcalıklarına veya hedef tablodaki *yönetici* ayrıcalıklarına sahip olmalıdır.
* Yazmak için (veri havuzu), Azure AD uygulaması hedef veritabanında *alma ayrıcalıklarına sahip olmalıdır.* Ayrıca, yeni tablolar oluşturmak için hedef veritabanında *Kullanıcı* ayrıcalıklarına sahip olmalıdır. Hedef tablo zaten varsa, hedef tablodaki *yönetici* ayrıcalıkları yapılandırılabilir.
 
Azure Veri Gezgini sorumlusu rolleri hakkında daha fazla bilgi için bkz. [rol tabanlı yetkilendirme](/azure/kusto/management/access-control/role-based-authorization). Güvenlik rollerini yönetmek için bkz. [güvenlik rolleri yönetimi](/azure/kusto/management/security-roles).

## <a name="spark-sink-writing-to-azure-data-explorer"></a>Spark havuzu: Azure Veri Gezgini yazma

1. Havuz parametrelerini ayarla:

     ```scala
    val KustoSparkTestAppId = dbutils.secrets.get(scope = "KustoDemos", key = "KustoSparkTestAppId")
    val KustoSparkTestAppKey = dbutils.secrets.get(scope = "KustoDemos", key = "KustoSparkTestAppKey")
 
    val appId = KustoSparkTestAppId
    val appKey = KustoSparkTestAppKey
    val authorityId = "72f988bf-86f1-41af-91ab-2d7cd011db47" // Optional - defaults to microsoft.com
    val cluster = "Sparktest.eastus2"
    val database = "TestDb"
    val table = "StringAndIntTable"
    ```

1. Spark veri çerçevesini Azure Veri Gezgini kümesine Batch olarak yazın:

    ```scala
    import com.microsoft.kusto.spark.datasink.KustoSinkOptions
    val conf = Map(
            KustoSinkOptions.KUSTO_CLUSTER -> cluster,
            KustoSinkOptions.KUSTO_TABLE -> table,
            KustoSinkOptions.KUSTO_DATABASE -> database,
            KustoSinkOptions.KUSTO_AAD_CLIENT_ID -> appId,
            KustoSinkOptions.KUSTO_AAD_CLIENT_PASSWORD -> appKey,
            KustoSinkOptions.KUSTO_AAD_AUTHORITY_ID -> authorityId)
    
    df.write
      .format("com.microsoft.kusto.spark.datasource")
      .options(conf)
      .save()
      
    ```
    
   Ya da Basitleştirilmiş sözdizimini kullanın:
   
    ```scala
         import com.microsoft.kusto.spark.datasink.SparkIngestionProperties
         import com.microsoft.kusto.spark.sql.extension.SparkExtension._
         
         val sparkIngestionProperties = Some(new SparkIngestionProperties()) // Optional, use None if not needed
         df.write.kusto(cluster, database, table, conf, sparkIngestionProperties)
    ```
   
1. Akış verilerini yaz:

    ```scala    
    import org.apache.spark.sql.streaming.Trigger
    import java.util.concurrent.TimeUnit
    import java.util.concurrent.TimeUnit
    import org.apache.spark.sql.streaming.Trigger

    // Set up a checkpoint and disable codeGen. Set up a checkpoint and disable codeGen as a workaround for an known issue 
    spark.conf.set("spark.sql.streaming.checkpointLocation", "/FileStore/temp/checkpoint")
    spark.conf.set("spark.sql.codegen.wholeStage","false") // Use in case a NullPointerException is thrown inside codegen iterator
    
    // Write to a Kusto table from a streaming source
    val kustoQ = df
          .writeStream
          .format("com.microsoft.kusto.spark.datasink.KustoSinkProvider")
          .options(conf) 
          .option(KustoSinkOptions.KUSTO_WRITE_ENABLE_ASYNC, "true") // Optional, better for streaming, harder to handle errors
          .trigger(Trigger.ProcessingTime(TimeUnit.SECONDS.toMillis(10))) // Sync this with the ingestionBatching policy of the database
          .start()
    
    ```

## <a name="spark-source-reading-from-azure-data-explorer"></a>Spark kaynağı: Azure Veri Gezgini okunuyor

1. Küçük miktarlarda veri okurken veri sorgusunu tanımlayın:

    ```scala
    import com.microsoft.kusto.spark.datasource.KustoSourceOptions
    import org.apache.spark.SparkConf
    import org.apache.spark.sql._
    import com.microsoft.azure.kusto.data.ClientRequestProperties

    val query = s"$table | where (ColB % 1000 == 0) | distinct ColA"
    val conf: Map[String, String] = Map(
          KustoSourceOptions.KUSTO_AAD_CLIENT_ID -> appId,
          KustoSourceOptions.KUSTO_AAD_CLIENT_PASSWORD -> appKey
        )

    val df = spark.read.format("com.microsoft.kusto.spark.datasource").
      options(conf).
      option(KustoSourceOptions.KUSTO_QUERY, query).
      option(KustoSourceOptions.KUSTO_DATABASE, database).
      option(KustoSourceOptions.KUSTO_CLUSTER, cluster).
      load()

    // Simplified syntax flavor
    import com.microsoft.kusto.spark.sql.extension.SparkExtension._
    
    val cpr: Option[ClientRequestProperties] = None // Optional
    val df2 = spark.read.kusto(cluster, database, query, conf, cpr)
    display(df2)
    ```

1. Büyük miktarlarda veriyi okurken geçici blob depolaması sağlanmalıdır. Depolama kapsayıcısı SAS anahtarı veya depolama hesabı adı, hesap anahtarı ve kapsayıcı adı sağlayın. Bu adım yalnızca Spark bağlayıcısının geçerli önizleme sürümü için gereklidir.

    ```scala
    // Use either container/account-key/account name, or container SaS
    val container = dbutils.secrets.get(scope = "KustoDemos", key = "blobContainer")
    val storageAccountKey = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageAccountKey")
    val storageAccountName = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageAccountName")
    // val storageSas = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageSasUrl")
    ```

    Yukarıdaki örnekte, bağlayıcı arabirimini kullanarak Key Vault eriştik. Alternatif olarak, Databricks gizli dizilerini kullanmanın daha basit bir yöntemini kullanırız.

1. Azure Veri Gezgini okuyun:

    ```scala
     val conf3 = Map(
          KustoSourceOptions.KUSTO_AAD_CLIENT_ID -> appId,
          KustoSourceOptions.KUSTO_AAD_CLIENT_PASSWORD -> appKey
          KustoSourceOptions.KUSTO_BLOB_STORAGE_SAS_URL -> storageSas)
    val df2 = spark.read.kusto(cluster, database, "ReallyBigTable", conf3)
    
    val dfFiltered = df2
      .where(df2.col("ColA").startsWith("row-2"))
      .filter("ColB > 12")
      .filter("ColB <= 21")
      .select("ColA")
    
    display(dfFiltered)
    ```
