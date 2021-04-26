---
title: "Hızlı başlangıç: Javas 'de arama dizini oluşturma"
titleSuffix: Azure Cognitive Search
description: Bu Java hızlı başlangıçta, Java için Azure Bilişsel Arama istemci kitaplığı 'nı kullanarak dizin oluşturmayı, verileri yüklemeyi ve sorguları çalıştırmayı öğrenin.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.devlang: java
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 03/04/2021
ms.custom: devx-track-java
ms.openlocfilehash: f52aaa0636c453c0def7cb97c203f17f0b11a221
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104955304"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-in-java"></a>Hızlı başlangıç: Java 'da Azure Bilişsel Arama dizini oluşturma
> [!div class="op_single_selector"]
> * [Java](search-get-started-java.md)
> * [JavaScript](search-get-started-javascript.md)
> * [C#](search-get-started-dotnet.md)
> * [Portal](search-get-started-portal.md)
> * [PowerShell](search-get-started-powershell.md)
> * [Python](search-get-started-python.md)
> * [REST](search-get-started-rest.md)

[IntelliJ](https://www.jetbrains.com/idea/), [Java 11 SDK](/java/azure/jdk/)ve [Azure bilişsel arama REST API](/rest/api/searchservice/)kullanarak bir arama dizini oluşturan, yükleyen ve sorgulayan bir Java konsol uygulaması oluşturun. Bu makalede, uygulama oluşturmaya yönelik adım adım yönergeler sağlanmaktadır. Alternatif olarak, [tüm uygulamayı indirebilir ve çalıştırabilirsiniz](https://github.com/Azure-Samples/azure-search-java-samples).

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

Bu hızlı başlangıcı derlemek ve test etmek için aşağıdaki yazılım ve Hizmetleri kullandık:

+ [IntelliJ IDEA](https://www.jetbrains.com/idea/)

+ [Java 11 SDK](/java/azure/jdk/)

+ Geçerli aboneliğinizde [bir Azure bilişsel arama hizmeti oluşturun](search-create-service-portal.md) veya [var olan bir hizmeti bulun](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) . Bu hızlı başlangıç için ücretsiz bir hizmet kullanabilirsiniz.

<a name="get-service-info"></a>

## <a name="get-a-key-and-url"></a>Anahtar ve URL al

Hizmete yapılan çağrılar, her istekte bir URL uç noktası ve erişim anahtarı gerektirir. Her ikisiyle de bir arama hizmeti oluşturulur. bu nedenle, aboneliğinize Azure Bilişsel Arama eklediyseniz, gerekli bilgileri almak için aşağıdaki adımları izleyin:

1. [Azure Portal oturum açın](https://portal.azure.com/)ve arama hizmetine **genel bakış** sayfasında URL 'yi alın. Örnek uç nokta `https://mydemo.search.windows.net` şeklinde görünebilir.

1. **Ayarlar**  >  **anahtarlar**' da, hizmette tam haklar için bir yönetici anahtarı alın. Üzerinde bir tane almanız gereken iş sürekliliği için iki adet değiştirilebilir yönetici anahtarı vardır. Nesneleri eklemek, değiştirmek ve silmek için isteklerde birincil veya ikincil anahtarı kullanabilirsiniz.

   :::image type="content" source="media/search-get-started-rest/get-url-key.png" alt-text="Hizmet adı ve yönetici ve sorgu anahtarlarını alın" border="false":::

Hizmetinize gönderilen her istek bir API anahtarı gerektirir. İstek başına geçerli bir anahtara sahip olmak, isteği gönderen uygulama ve bunu işleyen hizmet arasında güven oluşturur.

## <a name="set-up-your-environment"></a>Ortamınızı ayarlama

IntelliJ FIKRINI açıp yeni bir proje ayarlayarak başlayın.

### <a name="create-the-project"></a>Proje oluşturma

1. IntelliJ FIKRINI açın ve **Yeni proje oluştur**' u seçin.
1. **Maven**' ı seçin.
1. **Proje SDK** 'sı listesinde, Java 11 SDK ' yı seçin.

    :::image type="content" source="media/search-get-started-java/java-quickstart-create-new-maven-project.png" alt-text="Maven projesi oluşturma" border="false":::

1. **GroupID** ve **ArtifactId** için girin `AzureSearchQuickstart` .
1. Projeyi açmak için kalan Varsayılanları kabul edin.

### <a name="specify-maven-dependencies"></a>Maven bağımlılıklarını belirt

1. **Dosya**  >  **ayarları**' nı seçin.
1. **Ayarlar** penceresinde **Yapı, yürütme, dağıtım**  >  **derleme araçları**  >  **Maven**  >  **içeri aktarma**' yı seçin.
1. **Maven projelerini otomatik olarak Içeri aktar** onay kutusunu seçin ve **Tamam** ' a tıklayarak pencereyi kapatın. Maven eklentileri ve diğer bağımlılıklar artık sonraki adımda pom.xml dosyasını güncelleştirdiğinizde otomatik olarak eşitlenir.

    :::image type="content" source="media/search-get-started-java/java-quickstart-settings-import-maven-auto.png" alt-text="IntelliJ ayarlarındaki Maven içeri aktarma seçenekleri" border="false":::

1. pom.xml dosyasını açın ve içeriğini aşağıdaki Maven yapılandırma ayrıntıları ile değiştirin. Bunlar, [Exec Maven eklentisine](https://www.mojohaus.org/exec-maven-plugin/) ve bir [JSON arabirimi API](https://javadoc.io/doc/org.glassfish/javax.json/1.0.2) 'sine yönelik başvuruları içerir

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <project xmlns="http://maven.apache.org/POM/4.0.0"
             xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
             xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
        <modelVersion>4.0.0</modelVersion>
        <groupId>AzureSearchQuickstart</groupId>
        <artifactId>AzureSearchQuickstart</artifactId>
        <packaging>jar</packaging>
        <version>1.0-SNAPSHOT</version>
        <properties>
            <jackson.version>2.12.1</jackson.version>
            <auto-value.version>1.6.2</auto-value.version>
            <junit.version>5.4.2</junit.version>
            <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        </properties>
        <name>azuresearch-console</name>
        <url>http://maven.apache.org</url>
        <dependencies>
            <!-- https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-core -->
            <dependency>
                <groupId>com.fasterxml.jackson.core</groupId>
                <artifactId>jackson-core</artifactId>
                <version>${jackson.version}</version>
            </dependency>
            <dependency>
                <groupId>com.fasterxml.jackson.core</groupId>
                <artifactId>jackson-databind</artifactId>
                <version>${jackson.version}</version>
            </dependency>
            <dependency>
                <groupId>com.fasterxml.jackson.datatype</groupId>
                <artifactId>jackson-datatype-jdk8</artifactId>
                <version>${jackson.version}</version>
            </dependency>
            <dependency>
                <groupId>com.google.auto.value</groupId>
                <artifactId>auto-value-annotations</artifactId>
                <version>${auto-value.version}</version>
            </dependency>
            <dependency>
                <groupId>com.google.auto.value</groupId>
                <artifactId>auto-value</artifactId>
                <version>${auto-value.version}</version>
                <scope>provided</scope>
            </dependency>
            <dependency>
                <groupId>com.azure</groupId>
                <artifactId>azure-search-documents</artifactId>
                <version>11.1.3</version>
            </dependency>
        </dependencies>
    
        <build>
            <plugins>
                <!--put generated source files to generated-sources-->
                <plugin>
                    <groupId>org.apache.maven.plugins</groupId>
                    <artifactId>maven-compiler-plugin</artifactId>
                    <version>3.8.0</version>
                    <configuration>
                        <source>11</source>
                        <target>11</target>
                    </configuration>
                </plugin>
                <!-- For JUnit -->
                <plugin>
                    <groupId>org.apache.maven.plugins</groupId>
                    <artifactId>maven-surefire-plugin</artifactId>
                    <version>2.22.1</version>
                </plugin>
                <!-- Add exec plugin to run demo program -->
                <plugin>
                    <groupId>org.codehaus.mojo</groupId>
                    <artifactId>exec-maven-plugin</artifactId>
                    <version>1.6.0</version>
                    <executions>
                        <execution>
                            <goals>
                                <goal>exec</goal>
                            </goals>
                        </execution>
                    </executions>
                    <configuration>
                        <mainClass>com.microsoft.azure.search.samples.demo.App</mainClass>
                        <cleanupDaemonThreads>false</cleanupDaemonThreads>
                    </configuration>
                </plugin>
            </plugins>
        </build>
    </project>
    ```

<!-- STOPPED HERE -- SENT EMAIL TO TONG XU ASKING FOR INFO -->
### <a name="set-up-the-project-structure"></a>Proje yapısını ayarlama

1. **Dosya**  >  **projesi yapısı ' nı** seçin.
1. **Modüller**' i seçin ve kaynak ağacı ' nı genişleterek klasörün içeriklerine erişin `src`  >   `main` .
1. Klasöründe,,,,,,,,,, `src`  >   `main`  >  `java` için klasörler ekleyin `com` `microsoft` `azure` `search` `samples` `demo` . Bunu yapmak için `java` klasörü seçin, alt + Ekle tuşlarına basın ve ardından klasör adını girin.
1. `src`  >   `main`  > `resources` Klasöründe, `app` ve `service` klasörlerini ekleyin.

    İşiniz bittiğinde, proje ağacının aşağıdaki resim gibi görünmesi gerekir.

    :::image type="content" source="media/search-get-started-java/java-quickstart-basic-code-tree.png" alt-text="Proje dizini yapısı" border="false":::

1. Pencereyi kapatmak için **Tamam**’a tıklayın.

### <a name="add-azure-cognitive-search-service-information"></a>Azure Bilişsel Arama hizmet bilgilerini ekleme

1. **Proje** penceresinde, klasöre erişmek için kaynak ağacını genişletin `src`  >   `main`  > `resources`  >  `app` ve bir `config.properties` dosya ekleyin. Bunu yapmak için `app` klasörü seçin, alt + Ekle ' ye basın, **Dosya**' yı seçin ve ardından dosya adını girin.

1. Aşağıdaki ayarları yeni dosyaya kopyalayın ve `<YOUR-SEARCH-SERVICE-NAME>` `<YOUR-ADMIN-KEY>` `<YOUR-QUERY-KEY>` hizmet adınızla ve anahtarlarınız ile değiştirin. Hizmet uç noktanız ise `https://mydemo.search.windows.net` , hizmet adı olacaktır `"mydemo"` .

    ```java
        SearchServiceName=<YOUR-SEARCH-SERVICE-NAME>
        SearchServiceAdminKey=<YOUR-ADMIN-KEY>
        SearchServiceQueryKey=<YOUR-QUERY-KEY>
        IndexName=hotels-quickstart
        ApiVersion=2020-06-30
    ```

### <a name="add-the-main-method"></a>Main metodunu ekleyin

1. `src`  >   `main`  >  `java`  >  `app` Klasöründe bir `App` sınıf ekleyin. Bunu yapmak için `app` klasörü seçin, alt + Ekle ' ye basın, **Java sınıfı**' nı seçin ve ardından sınıf adını girin.
1. Sınıfını açın `App` ve içeriği aşağıdaki kodla değiştirin. Bu kod yöntemini içerir `main` . 

    Açıklamalı olmayan kod, arama hizmeti parametrelerini okur ve arama hizmeti istemcisinin bir örneğini oluşturmak için bunları kullanır. Arama hizmeti istemci kodu bir sonraki bölüme eklenecektir.

    Bu sınıftaki açıklamalı kod, bu hızlı başlangıç bölümünün sonraki bölümlerinde açıklama kaldırılacak.

    ```java
    package main.java.app;
    
    import main.java.service.SearchServiceClient;
    import java.io.IOException;
    import java.util.Properties;
    
    public class App {
    
        private static Properties loadPropertiesFromResource(String resourcePath) throws IOException {
            var inputStream = App.class.getResourceAsStream(resourcePath);
            var configProperties = new Properties();
            configProperties.load(inputStream);
            return configProperties;
        }
    
        public static void main(String[] args) {
            try {
                var config = loadPropertiesFromResource("/app/config.properties");
                var client = new SearchServiceClient(
                        config.getProperty("SearchServiceName"),
                        config.getProperty("SearchServiceAdminKey"),
                        config.getProperty("SearchServiceQueryKey"),
                        config.getProperty("ApiVersion"),
                        config.getProperty("IndexName")
                );
    
    
    //Uncomment the next 3 lines in the 1 - Create Index section of the quickstart
    //            if(client.indexExists()){ client.deleteIndex();}
    //            client.createIndex("/service/index.json");
    //            Thread.sleep(1000L); // wait a second to create the index
    
    //Uncomment the next 2 lines in the 2 - Load Documents section of the quickstart
    //            client.uploadDocuments("/service/hotels.json");
    //            Thread.sleep(2000L); // wait 2 seconds for data to upload
    
    //Uncomment the following 5 search queries in the 3 - Search an index section of the quickstart
    //            // Query 1
    //            client.logMessage("\n*QUERY 1****************************************************************");
    //            client.logMessage("Search for: Atlanta'");
    //            client.logMessage("Return: All fields'");
    //            client.searchPlus("Atlanta");
    //
    //            // Query 2
    //            client.logMessage("\n*QUERY 2****************************************************************");
    //            client.logMessage("Search for: Atlanta");
    //            client.logMessage("Return: HotelName, Tags, Address");
    //            SearchServiceClient.SearchOptions options2 = client.createSearchOptions();
    //            options2.select = "HotelName,Tags,Address";
    //            client.searchPlus("Atlanta", options2);
    //
    //            //Query 3
    //            client.logMessage("\n*QUERY 3****************************************************************");
    //            client.logMessage("Search for: wifi & restaurant");
    //            client.logMessage("Return: HotelName, Description, Tags");
    //            SearchServiceClient.SearchOptions options3 = client.createSearchOptions();
    //            options3.select = "HotelName,Description,Tags";
    //            client.searchPlus("wifi,restaurant", options3);
    //
    //            // Query 4 -filtered query
    //            client.logMessage("\n*QUERY 4****************************************************************");
    //            client.logMessage("Search for: all");
    //            client.logMessage("Filter: Ratings greater than 4");
    //            client.logMessage("Return: HotelName, Rating");
    //            SearchServiceClient.SearchOptions options4 = client.createSearchOptions();
    //            options4.filter="Rating%20gt%204";
    //            options4.select = "HotelName,Rating";
    //            client.searchPlus("*",options4);
    //
    //            // Query 5 - top 2 results, ordered by
    //            client.logMessage("\n*QUERY 5****************************************************************");
    //            client.logMessage("Search for: boutique");
    //            client.logMessage("Get: Top 2 results");
    //            client.logMessage("Order by: Rating in descending order");
    //            client.logMessage("Return: HotelId, HotelName, Category, Rating");
    //            SearchServiceClient.SearchOptions options5 = client.createSearchOptions();
    //            options5.top=2;
    //            options5.orderby = "Rating%20desc";
    //            options5.select = "HotelId,HotelName,Category,Rating";
    //            client.searchPlus("boutique", options5);
    
            } catch (Exception e) {
                System.err.println("Exception:" + e.getMessage());
                e.printStackTrace();
            }
        }
    }
    ```

### <a name="add-the-http-operations"></a>HTTP işlemlerini ekleme

1. `src`  >   `main`  >  `java`  >  `service` Klasöründe bir `SearchServiceClient` sınıf ekleyin. Bunu yapmak için `service` klasörü seçin, alt + Ekle ' ye basın, **Java sınıfı**' nı seçin ve ardından sınıf adını girin.
1. Sınıfını açın `SearchServiceClient` ve içeriğini aşağıdaki kodla değiştirin. Bu kod, Azure Bilişsel Arama REST API kullanmak için gereken HTTP işlemlerini sağlar. Dizin oluşturmak, belge yüklemek ve dizini sorgulamak için ek yöntemler sonraki bir bölüme eklenecektir.

    ```java
    package main.java.service;

    import javax.json.Json;
    import javax.net.ssl.HttpsURLConnection;
    import java.io.IOException;
    import java.io.StringReader;
    import java.net.HttpURLConnection;
    import java.net.URI;
    import java.net.http.HttpClient;
    import java.net.http.HttpRequest;
    import java.net.http.HttpResponse;
    import java.nio.charset.StandardCharsets;
    import java.util.Formatter;
    import java.util.function.Consumer;
    
        /* This class is responsible for implementing HTTP operations for creating the index, uploading documents and searching the data*/
        public class SearchServiceClient {
            private final String _adminKey;
            private final String _queryKey;
            private final String _apiVersion;
            private final String _serviceName;
            private final String _indexName;
            private final static HttpClient client = HttpClient.newHttpClient();
    
        public SearchServiceClient(String serviceName, String adminKey, String queryKey, String apiVersion, String indexName) {
            this._serviceName = serviceName;
            this._adminKey = adminKey;
            this._queryKey = queryKey;
            this._apiVersion = apiVersion;
            this._indexName = indexName;
        }

        private static HttpResponse<String> sendRequest(HttpRequest request) throws IOException, InterruptedException {
            logMessage(String.format("%s: %s", request.method(), request.uri()));
            return client.send(request, HttpResponse.BodyHandlers.ofString());
        }

        private static URI buildURI(Consumer<Formatter> fmtFn)
                {
                    Formatter strFormatter = new Formatter();
                    fmtFn.accept(strFormatter);
                    String url = strFormatter.out().toString();
                    strFormatter.close();
                    return URI.create(url);
        }
    
        public static void logMessage(String message) {
            System.out.println(message);
        }
    
        public static boolean isSuccessResponse(HttpResponse<String> response) {
            try {
                int responseCode = response.statusCode();
    
                logMessage("\n Response code = " + responseCode);
    
                if (responseCode == HttpURLConnection.HTTP_OK || responseCode == HttpURLConnection.HTTP_ACCEPTED
                        || responseCode == HttpURLConnection.HTTP_NO_CONTENT || responseCode == HttpsURLConnection.HTTP_CREATED) {
                    return true;
                }
    
                // We got an error
                var msg = response.body();
                if (msg != null) {
                    logMessage(String.format("\n MESSAGE: %s", msg));
                }
    
            } catch (Exception e) {
                e.printStackTrace();
            }
    
            return false;
        }
    
        public static HttpRequest httpRequest(URI uri, String key, String method, String contents) {
            contents = contents == null ? "" : contents;
            var builder = HttpRequest.newBuilder();
            builder.uri(uri);
            builder.setHeader("content-type", "application/json");
            builder.setHeader("api-key", key);
    
            switch (method) {
                case "GET":
                    builder = builder.GET();
                    break;
                case "HEAD":
                    builder = builder.GET();
                    break;
                case "DELETE":
                    builder = builder.DELETE();
                    break;
                case "PUT":
                    builder = builder.PUT(HttpRequest.BodyPublishers.ofString(contents));
                    break;
                case "POST":
                    builder = builder.POST(HttpRequest.BodyPublishers.ofString(contents));
                    break;
                default:
                    throw new IllegalArgumentException(String.format("Can't create request for method '%s'", method));
            }
            return builder.build();
        }
    }
    
    ```

### <a name="build-the-project"></a>Projeyi derleme

1. Projenizin aşağıdaki yapıya sahip olduğunu doğrulayın.

    :::image type="content" source="media/search-get-started-java/java-quickstart-basic-code-tree-plus-classes.png" alt-text="Proje dizin yapısı Plus sınıfları" border="false":::

1. **Maven** araç penceresini açın ve şu Maven hedefini yürütün: `verify exec:java` 
 :::image type="content" source="media/search-get-started-java/java-quickstart-execute-maven-goal.png" alt-text="Maven hedefini yürütme: exec: Java 'yı doğrula" border="false":::

İşlem tamamlandığında, derleme başarılı iletisini ve ardından sıfır (0) çıkış kodunu arayın.

## <a name="1---create-index"></a>1-Dizin oluşturma

Oteller Dizin tanımı basit alanlar ve bir karmaşık alan içerir. Basit bir alana örnek olarak "HotelName" veya "Description" verilebilir. "Adres" alanı, "sokak adresi" ve "şehir" gibi alt alanlar içerdiğinden karmaşık bir alandır. Bu hızlı başlangıçta, Dizin tanımı JSON kullanılarak belirtilir.

1. **Proje** penceresinde, klasöre erişmek için kaynak ağacını genişletin `src`  >   `main`  > `resources`  >  `service` ve bir `index.json` dosya ekleyin. Bunu yapmak için `app` klasörü seçin, alt + Ekle ' ye basın, **Dosya**' yı seçin ve ardından dosya adını girin.

1. Dosyasını açın `index.json` ve aşağıdaki dizin tanımını ekleyin.

    ```json
    {
      "name": "hotels-quickstart",
      "fields": [
        {
          "name": "HotelId",
          "type": "Edm.String",
          "key": true,
          "filterable": true
        },
        {
          "name": "HotelName",
          "type": "Edm.String",
          "searchable": true,
          "filterable": false,
          "sortable": true,
          "facetable": false
        },
        {
          "name": "Description",
          "type": "Edm.String",
          "searchable": true,
          "filterable": false,
          "sortable": false,
          "facetable": false,
          "analyzer": "en.lucene"
        },
        {
          "name": "Description_fr",
          "type": "Edm.String",
          "searchable": true,
          "filterable": false,
          "sortable": false,
          "facetable": false,
          "analyzer": "fr.lucene"
        },
        {
          "name": "Category",
          "type": "Edm.String",
          "searchable": true,
          "filterable": true,
          "sortable": true,
          "facetable": true
        },
        {
          "name": "Tags",
          "type": "Collection(Edm.String)",
          "searchable": true,
          "filterable": true,
          "sortable": false,
          "facetable": true
        },
        {
          "name": "ParkingIncluded",
          "type": "Edm.Boolean",
          "filterable": true,
          "sortable": true,
          "facetable": true
        },
        {
          "name": "LastRenovationDate",
          "type": "Edm.DateTimeOffset",
          "filterable": true,
          "sortable": true,
          "facetable": true
        },
        {
          "name": "Rating",
          "type": "Edm.Double",
          "filterable": true,
          "sortable": true,
          "facetable": true
        },
        {
          "name": "Address",
          "type": "Edm.ComplexType",
          "fields": [
            {
              "name": "StreetAddress",
              "type": "Edm.String",
              "filterable": false,
              "sortable": false,
              "facetable": false,
              "searchable": true
            },
            {
              "name": "City",
              "type": "Edm.String",
              "searchable": true,
              "filterable": true,
              "sortable": true,
              "facetable": true
            },
            {
              "name": "StateProvince",
              "type": "Edm.String",
              "searchable": true,
              "filterable": true,
              "sortable": true,
              "facetable": true
            },
            {
              "name": "PostalCode",
              "type": "Edm.String",
              "searchable": true,
              "filterable": true,
              "sortable": true,
              "facetable": true
            },
            {
              "name": "Country",
              "type": "Edm.String",
              "searchable": true,
              "filterable": true,
              "sortable": true,
              "facetable": true
            }
          ]
        }
      ]
    }
    ```

    Dizin adı "oteller-hızlı başlangıç" olacaktır. Dizin alanlarındaki öznitelikler, dizine alınmış verilerin bir uygulamada nasıl arandığını tespit edebilir. Örneğin, `IsSearchable` özniteliğin tam metin aramasına dahil olması gereken her alana atanması gerekir. Öznitelikler hakkında daha fazla bilgi için bkz. [Dizin oluşturma (REST)](/rest/api/searchservice/create-index).
    
    `Description`Bu dizindeki alan, `analyzer` varsayılan Lucene dil çözümleyicisini geçersiz kılmak için isteğe bağlı özelliği kullanır. `Description_fr`Alan, Fransızca metin depoladığı Için Fransızca Lucene Çözümleyicisi 'ni kullanıyor `fr.lucene` . , `Description` İsteğe bağlı Microsoft dil Çözümleyicisi en. Lucene ' i kullanıyor. Çözümleyiciler hakkında daha fazla bilgi edinmek için bkz. [Azure bilişsel arama 'de metin işleme Için çözümleyiciler](search-analyzers.md).

1. Aşağıdaki kodu `SearchServiceClient` sınıfına ekleyin. Bu yöntemler, bir dizin oluşturup silen ve bir dizinin mevcut olup olmadığını belirten Azure Bilişsel Arama REST hizmeti URL 'Lerini oluşturur. Yöntemler ayrıca HTTP isteğini de yapar.

    ```java
    public boolean indexExists() throws IOException, InterruptedException {
        logMessage("\n Checking if index exists...");
        var uri = buildURI(strFormatter -> strFormatter.format(
                "https://%s.search.windows.net/indexes/%s/docs?api-version=%s&search=*",
                _serviceName,_indexName,_apiVersion));
        var request = httpRequest(uri, _adminKey, "HEAD", "");
        var response = sendRequest(request);
        return isSuccessResponse(response);
    }
    
    public boolean deleteIndex() throws IOException, InterruptedException {
        logMessage("\n Deleting index...");
        var uri = buildURI(strFormatter -> strFormatter.format(
                "https://%s.search.windows.net/indexes/%s?api-version=%s",
                _serviceName,_indexName,_apiVersion));
        var request = httpRequest(uri, _adminKey, "DELETE", "*");
        var response = sendRequest(request);
        return isSuccessResponse(response);
    }
    
    
    public boolean createIndex(String indexDefinitionFile) throws IOException, InterruptedException {
        logMessage("\n Creating index...");
        //Build the search service URL
        var uri = buildURI(strFormatter -> strFormatter.format(
                "https://%s.search.windows.net/indexes/%s?api-version=%s",
                _serviceName,_indexName,_apiVersion));
        //Read in index definition file
        var inputStream = SearchServiceClient.class.getResourceAsStream(indexDefinitionFile);
        var indexDef = new String(inputStream.readAllBytes(), StandardCharsets.UTF_8);
        //Send HTTP PUT request to create the index in the search service
        var request = httpRequest(uri, _adminKey, "PUT", indexDef);
        var response = sendRequest(request);
        return isSuccessResponse(response);
    }
    ```

1. Sınıfında aşağıdaki kodun açıklamasını kaldırın `App` . Bu kod, varsa "oteller-hızlı başlangıç" dizinini siler ve "index.json" dosyasındaki Dizin tanımına göre yeni bir dizin oluşturur. 

    Dizin oluşturma isteğinden sonra tek saniyelik bir duraklatma eklenir. Bu duraklatma, belgeleri karşıya yüklemeden önce dizinin oluşturulmasını sağlar.

    ```java
        if (client.indexExists()) { client.deleteIndex();}
          client.createIndex("/service/index.json");
          Thread.sleep(1000L); // wait a second to create the index
    ```

1. **Maven** araç penceresini açın ve bu Maven hedefini yürütün:`verify exec:java`

    Kod çalışırken bir "Dizin oluşturma" iletisi ve ardından bir 201 yanıt kodu bulun. Bu yanıt kodu, dizinin oluşturulduğunu onaylar. Çalıştırma, derleme başarılı iletisi ve sıfır (0) çıkış kodu ile bitmelidir.
    
## <a name="2---load-documents"></a>2-belge yükleme

1. **Proje** penceresinde, klasöre erişmek için kaynak ağacını genişletin `src`  >   `main`  > `resources`  >  `service` ve bir `hotels.json` dosya ekleyin. Bunu yapmak için `app` klasörü seçin, alt + Ekle ' ye basın,  **Dosya**' yı seçin ve ardından dosya adını girin.
1. Aşağıdaki otel belgelerini dosyaya ekleyin.

    ```json
    {
      "value": [
        {
          "@search.action": "upload",
          "HotelId": "1",
          "HotelName": "Secret Point Motel",
          "Description": "The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Time's Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.",
          "Description_fr": "L'hôtel est idéalement situé sur la principale artère commerciale de la ville en plein cœur de New York. A quelques minutes se trouve la place du temps et le centre historique de la ville, ainsi que d'autres lieux d'intérêt qui font de New York l'une des villes les plus attractives et cosmopolites de l'Amérique.",
          "Category": "Boutique",
          "Tags": [ "pool", "air conditioning", "concierge" ],
          "ParkingIncluded": "false",
          "LastRenovationDate": "1970-01-18T00:00:00Z",
          "Rating": 3.60,
          "Address": {
            "StreetAddress": "677 5th Ave",
            "City": "New York",
            "StateProvince": "NY",
            "PostalCode": "10022",
            "Country": "USA"
          }
        },
        {
          "@search.action": "upload",
          "HotelId": "2",
          "HotelName": "Twin Dome Motel",
          "Description": "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
          "Description_fr": "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
          "Category": "Boutique",
          "Tags": [ "pool", "free wifi", "concierge" ],
          "ParkingIncluded": "false",
          "LastRenovationDate": "1979-02-18T00:00:00Z",
          "Rating": 3.60,
          "Address": {
            "StreetAddress": "140 University Town Center Dr",
            "City": "Sarasota",
            "StateProvince": "FL",
            "PostalCode": "34243",
            "Country": "USA"
          }
        },
        {
          "@search.action": "upload",
          "HotelId": "3",
          "HotelName": "Triple Landscape Hotel",
          "Description": "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel’s restaurant services.",
          "Description_fr": "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
          "Category": "Resort and Spa",
          "Tags": [ "air conditioning", "bar", "continental breakfast" ],
          "ParkingIncluded": "true",
          "LastRenovationDate": "2015-09-20T00:00:00Z",
          "Rating": 4.80,
          "Address": {
            "StreetAddress": "3393 Peachtree Rd",
            "City": "Atlanta",
            "StateProvince": "GA",
            "PostalCode": "30326",
            "Country": "USA"
          }
        },
        {
          "@search.action": "upload",
          "HotelId": "4",
          "HotelName": "Sublime Cliff Hotel",
          "Description": "Sublime Cliff Hotel is located in the heart of the historic center of Sublime in an extremely vibrant and lively area within short walking distance to the sites and landmarks of the city and is surrounded by the extraordinary beauty of churches, buildings, shops and monuments. Sublime Cliff is part of a lovingly restored 1800 palace.",
          "Description_fr": "Le sublime Cliff Hotel est situé au coeur du centre historique de sublime dans un quartier extrêmement animé et vivant, à courte distance de marche des sites et monuments de la ville et est entouré par l'extraordinaire beauté des églises, des bâtiments, des commerces et Monuments. Sublime Cliff fait partie d'un Palace 1800 restauré avec amour.",
          "Category": "Boutique",
          "Tags": [ "concierge", "view", "24-hour front desk service" ],
          "ParkingIncluded": "true",
          "LastRenovationDate": "1960-02-06T00:00:00Z",
          "Rating": 4.60,
          "Address": {
            "StreetAddress": "7400 San Pedro Ave",
            "City": "San Antonio",
            "StateProvince": "TX",
            "PostalCode": "78216",
            "Country": "USA"
          }
        }
      ]
    }
    ```

1. Sınıfına aşağıdaki kodu ekleyin `SearchServiceClient` . Bu kod, otel belgelerini dizine yüklemek için REST hizmeti URL 'sini oluşturur ve ardından HTTP POST isteğini yapar.

    ```java
    public boolean uploadDocuments(String documentsFile) throws IOException, InterruptedException {
        logMessage("\n Uploading documents...");
        //Build the search service URL
        var endpoint = buildURI(strFormatter -> strFormatter.format(
                "https://%s.search.windows.net/indexes/%s/docs/index?api-version=%s",
                _serviceName,_indexName,_apiVersion));
        //Read in the data to index
        var inputStream = SearchServiceClient.class.getResourceAsStream(documentsFile);
        var documents = new String(inputStream.readAllBytes(), StandardCharsets.UTF_8);
        //Send HTTP POST request to upload and index the data
        var request = httpRequest(endpoint, _adminKey, "POST", documents);
        var response = sendRequest(request);
        return isSuccessResponse(response);
    }
    ```

1. Sınıfında aşağıdaki kodun açıklamasını kaldırın `App` . Bu kod, "hotels.json" içindeki belgeleri dizine yükler.

    ```java
    client.uploadDocuments("/service/hotels.json");
    Thread.sleep(2000L); // wait 2 seconds for data to upload
    ```

    Dizin sorgulanmadan önce belge yükleme işleminin tamamlanmasını sağlamak için karşıya yükleme isteğinden iki saniyelik bir duraklama eklenir.

1. **Maven** araç penceresini açın ve bu Maven hedefini yürütün:`verify exec:java`

    Önceki adımda bir "oteller-QuickStart" dizini oluşturduğunuz için, kod şimdi onu silecek ve otel belgelerini yüklemeden önce yeniden oluşturacak.

    Kod çalışırken, bir "belge karşıya yükleme" iletisini ve ardından bir 200 yanıt kodu olup olmadığına bakın. Bu yanıt kodu, belgelerin dizine yüklendiğini onaylar. Çalıştırma, derleme başarılı iletisi ve sıfır (0) çıkış kodu ile bitmelidir.

## <a name="3---search-an-index"></a>3 - Dizin arama

Oteller belgelerini yüklemişseniz, otel verilerine erişmek için arama sorguları oluşturabilirsiniz.

1. Aşağıdaki kodu `SearchServiceClient` sınıfına ekleyin. Bu kod, dizinli verileri aramak ve arama sonuçlarını yazdıran Azure Bilişsel Arama REST hizmeti URL 'Lerini oluşturur.

    `SearchOptions`Sınıfı ve `createSearchOptions` yöntemi, kullanılabilir Azure bilişsel arama REST API sorgu seçeneklerinin bir alt kümesini belirtmenizi sağlar. REST API sorgu seçenekleri hakkında daha fazla bilgi için bkz. [arama belgeleri (Azure Bilişsel Arama REST API)](/rest/api/searchservice/search-documents).

    `SearchPlus`Yöntemi arama sorgu URL 'sini oluşturur, arama isteğini yapar ve sonuçları konsola yazdırır. 

    ```java
    public SearchOptions createSearchOptions() { return new SearchOptions();}

    //Defines available search parameters that can be set
    public static class SearchOptions {

        public String select = "";
        public String filter = "";
        public int top = 0;
        public String orderby= "";
    }

    //Concatenates search parameters to append to the search request
    private String createOptionsString(SearchOptions options)
    {
        String optionsString = "";
        if (options != null) {
            if (options.select != "")
                optionsString = optionsString + "&$select=" + options.select;
            if (options.filter != "")
                optionsString = optionsString + "&$filter=" + options.filter;
            if (options.top != 0)
                optionsString = optionsString + "&$top=" + options.top;
            if (options.orderby != "")
                optionsString = optionsString + "&$orderby=" +options.orderby;
        }
        return optionsString;
    }
    
    public void searchPlus(String queryString)
    {
        searchPlus( queryString, null);
    }
    
    public void searchPlus(String queryString, SearchOptions options) {
    
        try {
            String optionsString = createOptionsString(options);
            var uri = buildURI(strFormatter -> strFormatter.format(
                    "https://%s.search.windows.net/indexes/%s/docs?api-version=%s&search=%s%s",
                    _serviceName, _indexName, _apiVersion, queryString, optionsString));
            var request = httpRequest(uri, _queryKey, "GET", null);
            var response = sendRequest(request);
            var jsonReader = Json.createReader(new StringReader(response.body()));
            var jsonArray = jsonReader.readObject().getJsonArray("value");
            var resultsCount = jsonArray.size();
            logMessage("Results:\nCount: " + resultsCount);
            for (int i = 0; i <= resultsCount - 1; i++) {
                logMessage(jsonArray.get(i).toString());
            }
    
            jsonReader.close();
    
        }
        catch (Exception e) {
            e.printStackTrace();
        }
    
    }
    ```

1. `App`Sınıfında, aşağıdaki kodun açıklamasını kaldırın. Bu kod, döndürülecek arama metni, sorgu parametreleri ve veri alanları dahil olmak üzere beş farklı sorgu ayarlar. 

    ```java
    // Query 1
    client.logMessage("\n*QUERY 1****************************************************************");
    client.logMessage("Search for: Atlanta");
    client.logMessage("Return: All fields'");
    client.searchPlus("Atlanta");

    // Query 2
    client.logMessage("\n*QUERY 2****************************************************************");
    client.logMessage("Search for: Atlanta");
    client.logMessage("Return: HotelName, Tags, Address");
    SearchServiceClient.SearchOptions options2 = client.createSearchOptions();
    options2.select = "HotelName,Tags,Address";
    client.searchPlus("Atlanta", options2);

    //Query 3
    client.logMessage("\n*QUERY 3****************************************************************");
    client.logMessage("Search for: wifi & restaurant");
    client.logMessage("Return: HotelName, Description, Tags");
    SearchServiceClient.SearchOptions options3 = client.createSearchOptions();
    options3.select = "HotelName,Description,Tags";
    client.searchPlus("wifi,restaurant", options3);

    // Query 4 -filtered query
    client.logMessage("\n*QUERY 4****************************************************************");
    client.logMessage("Search for: all");
    client.logMessage("Filter: Ratings greater than 4");
    client.logMessage("Return: HotelName, Rating");
    SearchServiceClient.SearchOptions options4 = client.createSearchOptions();
    options4.filter="Rating%20gt%204";
    options4.select = "HotelName,Rating";
    client.searchPlus("*",options4);

    // Query 5 - top 2 results, ordered by
    client.logMessage("\n*QUERY 5****************************************************************");
    client.logMessage("Search for: boutique");
    client.logMessage("Get: Top 2 results");
    client.logMessage("Order by: Rating in descending order");
    client.logMessage("Return: HotelId, HotelName, Category, Rating");
    SearchServiceClient.SearchOptions options5 = client.createSearchOptions();
    options5.top=2;
    options5.orderby = "Rating%20desc";
    options5.select = "HotelId,HotelName,Category,Rating";
    client.searchPlus("boutique", options5);
    ```



    [Bir sorgudaki terimlerle eşleşen iki yol](search-query-overview.md#types-of-queries)vardır: tam metin araması ve filtreler. Tam metin arama sorgusu, dizininizdeki alanlarda bir veya daha fazla terimi arar `IsSearchable` . Filtre, bir dizindeki alanlar üzerinde değerlendirilen bir Boolean ifadedir `IsFilterable` . Tam metin arama ve filtreleri birlikte veya ayrı olarak kullanabilirsiniz.

1. **Maven** araç penceresini açın ve bu Maven hedefini yürütün:`verify exec:java`

    Her bir sorgunun özetini ve sonuçlarını arayın. Çalıştırma, derleme başarılı iletisi ve sıfır (0) çıkış kodu ile tamamlanmalıdır.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bir projenin sonunda kendi aboneliğinizde çalışırken, artık ihtiyaç duyulmadığınızda kaynakları kaldırmak iyi bir fikirdir. Çalışır durumda bırakılan kaynaklar maliyetlerin artmasına neden olabilir. Kaynakları teker teker silebilir veya tüm kaynak grubunu silerek kaynak kümesinin tamamını kaldırabilirsiniz.

Sol gezinti bölmesindeki **tüm kaynaklar** veya **kaynak grupları** bağlantısını kullanarak portalda kaynakları bulabilir ve yönetebilirsiniz.

Ücretsiz bir hizmet kullanıyorsanız, üç Dizin, Dizin Oluşturucu ve veri kaynağı ile sınırlı olduğunu unutmayın. Sınırın altında kalmak için portalda ayrı ayrı öğeleri silebilirsiniz. 

## <a name="next-steps"></a>Sonraki adımlar

Bu Java hızlı başlangıçta, dizin oluşturmak, belgeler ile yüklemek ve sorguları çalıştırmak için bir dizi görev üzerinden çalıştık. Temel kavramları rahat deneyimliyseniz, bekleyen Dizin Oluşturucu işlemlerini listeleyen aşağıdaki makaleyi öneririz.

> [!div class="nextstepaction"]
> [Dizin Oluşturucu işlemleri](/rest/api/searchservice/indexer-operations)