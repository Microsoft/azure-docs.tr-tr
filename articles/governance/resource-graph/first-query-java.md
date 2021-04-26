---
title: 'Hızlı başlangıç: ilk Java sorgunuz'
description: Bu hızlı başlangıçta, Java için kaynak grafiği Maven paketlerini etkinleştirmek ve ilk sorgunuzu çalıştırmak için adımları izleyin.
ms.date: 03/30/2021
ms.topic: quickstart
ms.custom: devx-track-java
ms.openlocfilehash: 97c04cb8b8180034bdc5109446c79deb56e457c9
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106223987"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-java"></a>Hızlı başlangıç: Java kullanarak ilk kaynak grafik sorgunuzu çalıştırma

Azure Kaynak Grafiği 'ni kullanmanın ilk adımı, Java için gereken Maven paketlerinin yüklü olduğunu denetlerdir. Bu hızlı başlangıç, Java yüklemenize Maven paketleri ekleme sürecinde size yol gösterir.

Bu işlemin sonunda, Maven paketlerini Java yüklemenize eklemiş ve ilk kaynak Graf sorgunuzu çalıştıracaksınız.

## <a name="prerequisites"></a>Önkoşullar

- Azure aboneliği. Azure aboneliğiniz yoksa başlamadan önce [ücretsiz](https://azure.microsoft.com/free/) bir hesap oluşturun.

- En son Azure CLı 'nın yüklü olup olmadığını denetleyin (en azından **2.21.0**). Henüz yüklenmemişse bkz. [Azure CLI 'Yı yükleme](/cli/azure/install-azure-cli).

  > [!NOTE]
  > Java için Azure SDK 'nın aşağıdaki örneklerde **CLI tabanlı kimlik doğrulaması** kullanmasını sağlamak IÇIN Azure CLI gereklidir. Diğer seçenekler hakkında daha fazla bilgi için bkz. [Java Için Azure kimlik istemci kitaplığı](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity).

- [Java geliştirici seti](/azure/developer/java/fundamentals/java-jdk-long-term-support), sürümü
  8.

- [Apache Maven](https://maven.apache.org/), sürüm 3,6 veya üzeri.

## <a name="create-the-resource-graph-project"></a>Kaynak Grafiği projesi oluşturma

Java 'yı Azure Kaynak Grafiği 'ni sorgulamak üzere etkinleştirmek için Maven ile yeni bir uygulama oluşturup yapılandırın ve gerekli Maven paketlerini yükler.

1. [Maven arşiv ETYPE](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html)Ile "argquery" adlı yeni bir Java uygulaması başlatın:

   ```cmd
   mvn -B archetype:generate -DarchetypeGroupId="org.apache.maven.archetypes" -DgroupId="com.Fabrikam" -DartifactId="argQuery"
   ```

1. Dizinleri yeni proje klasörüyle değiştirin ve en `argQuery` `pom.xml` sevdiğiniz düzenleyicide açın. `<dependency>`Mevcut düğümün altına aşağıdaki düğümleri ekleyin `<dependencies>` :

   ```xml
    <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-identity</artifactId>
        <version>1.2.4</version>
    </dependency>
    <dependency>
        <groupId>com.azure.resourcemanager</groupId>
        <artifactId>azure-resourcemanager-resourcegraph</artifactId>
        <version>1.0.0-beta.1</version>
    </dependency>
   ```

1. `pom.xml`Dosyasında, `<properties>` `<project>` kaynak ve hedef sürümleri güncelleştirmek için temel düğümün altına aşağıdaki düğümü ekleyin:

   ```xml
   <properties>
        <maven.compiler.source>1.8</maven.compiler.source>
        <maven.compiler.target>1.8</maven.compiler.target>
   </properties>
   ```

1. `pom.xml`Dosyasında, `<build>` `<project>` projenin çalıştırılacağı hedefi ve ana sınıfı yapılandırmak için temel düğümün altına aşağıdaki düğümü ekleyin.

   ```xml
   <build>
       <plugins>
           <plugin>
               <groupId>org.codehaus.mojo</groupId>
               <artifactId>exec-maven-plugin</artifactId>
               <version>1.2.1</version>
               <executions>
                   <execution>
                       <goals>
                           <goal>java</goal>
                       </goals>
                   </execution>
               </executions>
               <configuration>
                   <mainClass>com.Fabrikam.App</mainClass>
               </configuration>
           </plugin>
       </plugins>
   </build>
   ```

1. `App.java`İçindeki varsayılanı `\argQuery\src\main\java\com\Fabrikam` aşağıdaki kodla değiştirin ve güncelleştirilmiş dosyayı kaydedin:

   ```java
   package com.Fabrikam;
   
   import java.util.Arrays;
   import java.util.List;
   import com.azure.core.management.AzureEnvironment;
   import com.azure.core.management.profile.AzureProfile;
   import com.azure.identity.DefaultAzureCredentialBuilder;
   import com.azure.resourcemanager.resourcegraph.ResourceGraphManager;
   import com.azure.resourcemanager.resourcegraph.models.QueryRequest;
   import com.azure.resourcemanager.resourcegraph.models.QueryRequestOptions;
   import com.azure.resourcemanager.resourcegraph.models.QueryResponse;
   import com.azure.resourcemanager.resourcegraph.models.ResultFormat;
   
   public class App
   {
       public static void main( String[] args )
       {
           List<String> listSubscriptionIds = Arrays.asList(args[0]);
           String strQuery = args[1];
   
           ResourceGraphManager manager = ResourceGraphManager.authenticate(new DefaultAzureCredentialBuilder().build(), new AzureProfile(AzureEnvironment.AZURE));
   
           QueryRequest queryRequest = new QueryRequest()
               .withSubscriptions(listSubscriptionIds)
               .withQuery(strQuery);
           
           QueryResponse response = manager.resourceProviders().resources(queryRequest);
   
           System.out.println("Records: " + response.totalRecords());
           System.out.println("Data:\n" + response.data());
       }
   }
   ```

1. `argQuery`Konsol uygulamasını oluşturun:

   ```bash
   mvn package
   ```

## <a name="run-your-first-resource-graph-query"></a>İlk Kaynak Grafiği sorgunuzu çalıştırma

Tümleşik Java konsol uygulaması ile basit bir kaynak grafiği sorgusu denemenize zaman atalım. Sorgu, ilk beş Azure kaynağını her bir kaynağın **adı** ve **kaynak türü** ile döndürür.

Her çağrıda `argQuery` , kendi değerlerinizle değiştirmeniz gereken kullanılan değişkenler vardır:

- `{subscriptionId}` - Abonelik kimliğinizle değiştirin
- `{query}` -Azure Kaynak Graph Sorgunuzla değiştirin

1. Azure CLı kullanarak kimlik doğrulaması yapın `az login` .

1. Dizinleri `argQuery` Önceki komutla oluşturduğunuz proje klasörüyle değiştirin `mvn -B archetype:generate` .

1. Maven kullanarak ilk Azure Kaynak Grafiği sorgunuzu çalıştırarak konsol uygulamasını derleyin ve bağımsız değişkenleri geçirin. `exec.args`Özelliği, bağımsız değişkenleri boşluklarla tanımlar. Sorguyu tek bir bağımsız değişken olarak tanımlamak için, bunu tek tırnak () ile sardık `'` .

   ```bash
   mvn compile exec:java -Dexec.args "{subscriptionId} 'Resources | project name, type | limit 5'"
   ```

   > [!NOTE]
   > Bu sorgu örneği gibi bir sıralama değiştiricisi sağlamadığından `order by` , bu sorguyu birden çok kez çalıştırmak, istek başına farklı bir kaynak kümesi sunmamasından kaynaklanıyor olabilir.

1. Bağımsız değişkenini olarak değiştirin `argQuery.exe` ve sorguyu `order by` **Name** özelliği olarak değiştirin:

   ```bash
   mvn compile exec:java -Dexec.args "{subscriptionId} 'Resources | project name, type | limit 5 | order by name asc'"
   ```

   > [!NOTE]
   > İlk sorguda olduğu gibi, bu sorguyu birden çok kez çalıştırmak, muhtemelen istek başına farklı bir kaynak kümesi sunacaktır. Sorgu komutlarının düzeni önemlidir. Bu örnekte `order by`, `limit`’den sonra gelmektedir. Bu komut sırası ilk olarak sorgu sonuçlarını kısıtlar ve sonra sıralar.

1. Son parametreyi olarak değiştirin `argQuery.exe` ve sorguyu ilk olarak `order by` **ad** özelliğine, sonra da `limit` en üstteki beş sonuca değiştirin:

   ```bash
   mvn compile exec:java -Dexec.args "{subscriptionId} 'Resources | project name, type | order by name asc | limit 5'"
   ```

Son sorgu birkaç kez çalıştırıldığında, ortamınızdaki hiçbir şeyin değişmediği kabul edildiğinde döndürülen sonuçlar tutarlı ve **ad** özelliğine göre sıralanır, ancak yine de en üstteki beş sonuçla sınırlıdır.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Java konsol uygulamasını ve yüklü paketleri kaldırmak istiyorsanız proje klasörünü silerek bunu yapabilirsiniz `argQuery` .

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, gerekli kaynak grafik paketleriyle bir Java konsol uygulaması oluşturdunuz ve ilk sorgunuzu çalıştırdık. Kaynak grafik dili hakkında daha fazla bilgi edinmek için sorgu dili ayrıntıları sayfasına ilerleyin.

> [!div class="nextstepaction"]
> [Sorgu dili hakkında daha fazla bilgi alın](./concepts/query-language.md)