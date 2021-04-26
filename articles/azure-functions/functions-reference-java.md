---
title: Azure Işlevleri için Java geliştirici başvurusu
description: Java ile işlevleri geliştirmeyi anlayın.
ms.topic: conceptual
ms.date: 09/14/2018
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 1ffbd760ae75605d75652b29d379420d6946aa8f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96326463"
---
# <a name="azure-functions-java-developer-guide"></a>Azure Işlevleri Java geliştirici kılavuzu

Bu kılavuz, Java kullanarak Azure Işlevleri geliştirmeye yardımcı olacak ayrıntılı bilgiler içerir.

Bir Java geliştiricisi olarak, Azure Işlevleri 'ne yeni başladıysanız, lütfen önce aşağıdaki makalelerden birini okuyun:

| Kullanmaya başlama | Kavramlar| 
| -- | -- |  
| <ul><li>[Visual Studio Code kullanarak Java işlevi](./create-first-function-vs-code-java.md)</li><li>[Terminal/komut istemiyle Java/Maven işlevi](./create-first-function-cli-java.md)</li><li>[Gradle kullanarak Java işlevi](functions-create-first-java-gradle.md)</li><li>[Çakışan Küreler kullanan Java işlevi](functions-create-maven-eclipse.md)</li><li>[IntelliJ FIKRINI kullanarak Java işlevi](functions-create-maven-intellij.md)</li></ul> | <ul><li>[Geliştirici kılavuzu](functions-reference.md)</li><li>[Barındırma seçenekleri](functions-scale.md)</li><li>[Performans &nbsp; konuları](functions-best-practices.md)</li></ul> |

## <a name="java-function-basics"></a>Java işlevi temelleri

Java işlevi, `public` ek açıklamayla donatılmış bir yöntemdir `@FunctionName` . Bu yöntem, bir Java işlevinin girişini tanımlar ve belirli bir pakette benzersiz olmalıdır. Pakette birden fazla ortak yöntemi olan birden fazla sınıf bulunabilir `@FunctionName` . Azure 'daki bir işlev uygulamasına tek bir paket dağıtılır. Azure 'da çalışırken, işlev uygulaması bireysel Java işlevleriniz için dağıtım, yürütme ve yönetim bağlamını sağlar.

## <a name="programming-model"></a>Programlama modeli 

[Tetikleyiciler ve bağlamaların](functions-triggers-bindings.md) kavramları Azure işlevleri için temel bir araçlardır. Tetikleyiciler, kodunuzun yürütülmesini başlatır. Bağlamalar, özel veri erişim kodu yazmak zorunda kalmadan bir işleve veri geçirmek ve veri döndürmek için bir yol sağlar.

## <a name="create-java-functions"></a>Java işlevleri oluşturma

Java işlevlerinin oluşturulmasını kolaylaştırmak için, belirli bir işlev tetikleyicisine sahip projeler oluşturmanıza yardımcı olması için önceden tanımlanmış Java şablonlarını kullanan Maven tabanlı araç ve arşiv türleri vardır.    

### <a name="maven-based-tooling"></a>Maven tabanlı araç

Aşağıdaki geliştirici ortamlarında, Java işlev projeleri oluşturmanıza olanak sağlayan Azure Işlevleri araçları vardır: 

+ [Visual Studio Code](https://code.visualstudio.com/docs/java/java-azurefunctions)
+ [Eclipse](functions-create-maven-eclipse.md)
+ [IntelliJ](functions-create-maven-intellij.md)

Yukarıdaki makale bağlantılarında, tercih ettiğiniz IDE 'yi kullanarak ilk işlevlerinizi nasıl oluşturacağınız gösterilmektedir. 

### <a name="project-scaffolding"></a>Proje yapı Iskelesi

Terminalden komut satırı geliştirmeyi tercih ediyorsanız, Java tabanlı işlev projelerini dolandırmanın en kolay yolu, arşiv `Apache Maven` türleri kullanmaktır. Azure Işlevleri için Java Maven arşiv ETYPE şu _GroupID_ altında yayımlanır:_ArtifactId_: [com. Microsoft. Azure: Azure-Functions-arşiv ETYPE](https://search.maven.org/artifact/com.microsoft.azure/azure-functions-archetype/). 

Aşağıdaki komut, bu arşiv ETYPE kullanarak yeni bir Java işlev projesi oluşturur:

# <a name="bash"></a>[Bash](#tab/bash)

```bash
mvn archetype:generate \
    -DarchetypeGroupId=com.microsoft.azure \
    -DarchetypeArtifactId=azure-functions-archetype
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
mvn archetype:generate ^
    -DarchetypeGroupId=com.microsoft.azure ^
    -DarchetypeArtifactId=azure-functions-archetype
```

---

Bu arşiv ETYPE kullanmaya başlamak için bkz. [Java hızlı başlangıç](./create-first-function-cli-java.md).

## <a name="folder-structure"></a>Klasör yapısı

Bir Azure Işlevleri Java projesinin klasör yapısı aşağıda verilmiştir:

```
FunctionsProject
 | - src
 | | - main
 | | | - java
 | | | | - FunctionApp
 | | | | | - MyFirstFunction.java
 | | | | | - MySecondFunction.java
 | - target
 | | - azure-functions
 | | | - FunctionApp
 | | | | - FunctionApp.jar
 | | | | - host.json
 | | | | - MyFirstFunction
 | | | | | - function.json
 | | | | - MySecondFunction
 | | | | | - function.json
 | | | | - bin
 | | | | - lib
 | - pom.xml
```

İşlev uygulamasını yapılandırmak için, paylaşılan bir [host.js](functions-host-json.md) dosyası kullanabilirsiniz. Her işlev kendi kod dosyasına (. Java) ve bağlama yapılandırma dosyasına (function.js) sahiptir.

Bir projeye birden fazla işlev yerleştirebilirsiniz. İşlevlerinizi ayrı jliler 'e yerleştirmekten kaçının. `FunctionApp`Hedef dizindeki, Azure 'da işlev uygulamanıza dağıtılan şeydir.

## <a name="triggers-and-annotations"></a>Tetikleyiciler ve ek açıklamalar

 İşlevler, bir HTTP isteği, süreölçer veya veri güncelleştirmesi gibi bir tetikleyici tarafından çağrılır. İşlevinizin bir veya daha fazla çıkış üretmek için bu tetikleyiciyi ve diğer girişleri işlemesi gerekir.

Yöntemlerinizi giriş ve çıkışları bağlamak için [com. Microsoft. Azure. Functions. Annotation. *](/java/api/com.microsoft.azure.functions.annotation) paketinde bulunan Java ek açıklamalarını kullanın. Daha fazla bilgi için bkz. [Java başvuru belgeleri](/java/api/com.microsoft.azure.functions.annotation).

> [!IMPORTANT] 
> Azure Blob depolama, Azure kuyruk depolama veya Azure Tablo depolama tetikleyicilerini yerel olarak çalıştırmak için [local.settings.js](./functions-run-local.md#local-settings-file) Azure Storage hesabı yapılandırmanız gerekir.

Örnek:

```java
public class Function {
    public String echo(@HttpTrigger(name = "req", 
      methods = {HttpMethod.POST},  authLevel = AuthorizationLevel.ANONYMOUS) 
        String req, ExecutionContext context) {
        return String.format(req);
    }
}
```

`function.json` [Azure-Functions-Maven-Plugin](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-maven-plugin)tarafından buna karşılık gelen oluşturulan:

```json
{
  "scriptFile": "azure-functions-example.jar",
  "entryPoint": "com.example.Function.echo",
  "bindings": [
    {
      "type": "httpTrigger",
      "name": "req",
      "direction": "in",
      "authLevel": "anonymous",
      "methods": [ "post" ]
    },
    {
      "type": "http",
      "name": "$return",
      "direction": "out"
    }
  ]
}

```

## <a name="java-versions"></a>Java sürümleri

Üzerinde çalışan işlevlerin Azure 'da çalıştığı işlev uygulaması oluşturulurken kullanılan Java sürümü pom.xml dosyasında belirtilir. Maven arşiv ETYPE Şu anda bir Java 8 için pom.xml oluşturuyor ve yayımlamadan önce değiştirebilirsiniz. pom.xml Java sürümü, uygulamanızı yerel olarak geliştirmiş ve test ettiğiniz sürümle eşleşmelidir. 

### <a name="supported-versions"></a>Desteklenen sürümler

Aşağıdaki tabloda, işletim sistemine göre Işlevler çalışma zamanının her ana sürümü için desteklenen geçerli Java sürümleri gösterilmektedir:

| İşlevler sürümü | Java sürümleri (Windows) | Java sürümleri (Linux) |
| ----- | ----- | --- |
| 3.x | 11 <br/>8 | 11 <br/>8 |
| 2.x | 8 | yok |

Dağıtımınız için bir Java sürümü belirtmediğiniz takdirde, Maven arşiv ETYPE, Azure 'a dağıtım sırasında varsayılan olarak Java 8 ' dir.

### <a name="specify-the-deployment-version"></a>Dağıtım sürümünü belirtin

Parametresini kullanarak Maven arşiv ETYPE tarafından hedeflenen Java sürümünü kontrol edebilirsiniz `-DjavaVersion` . Bu parametrenin değeri ya da olabilir `8` `11` . 

Maven arşiv ETYPE, belirtilen Java sürümünü hedefleyen bir pom.xml oluşturur. pom.xml ' deki aşağıdaki öğeler, kullanılacak Java sürümünü gösterir:

| Öğe |  Java 8 değeri | Java 11 değeri | Description |
| ---- | ---- | ---- | --- |
| **`Java.version`** | 1.8 | 11 | Maven-derleyici-eklentisi tarafından kullanılan Java sürümü. |
| **`JavaVersion`** | 8 | 11 | Azure 'da işlev uygulaması tarafından barındırılan Java sürümü. |

Aşağıdaki örneklerde pom.xml dosyasının ilgili bölümlerinde Java 8 ayarları gösterilmektedir:

#### `Java.version`
:::code language="xml" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/pom.xml" range="12-19" highlight="14":::

#### `JavaVersion`
:::code language="xml" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/pom.xml" range="77-85" highlight="80":::

> [!IMPORTANT]
> Maven kullanılarak kod derleme sırasında kullanılan JDK dizinine JAVA_HOME ortam değişkeninin doğru şekilde ayarlanmış olması gerekir. JDK sürümünün en az ayar kadar yüksek olduğundan emin olun `Java.version` . 

### <a name="specify-the-deployment-os"></a>Dağıtım işletim sistemini belirtin

Maven Ayrıca, işlev uygulamanızın Azure 'da çalıştığı işletim sistemini belirtmenize imkan tanır. `os`İşletim sistemini seçmek için öğesini kullanın. 

| Öğe |  Windows | Linux | Docker |
| ---- | ---- | ---- | --- |
| **`os`** | windows | 'un | Docker |

Aşağıdaki örnekte, pom.xml dosyasının bölümündeki işletim sistemi ayarı gösterilmektedir `runtime` :

:::code language="xml" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/pom.xml" range="77-85" highlight="79":::
 
## <a name="jdk-runtime-availability-and-support"></a>JDK çalışma zamanı kullanılabilirliği ve desteği 

Java işlev uygulamalarının yerel geliştirmesi için, [Azul sistemlerinden](https://www.azul.com/downloads/azure-only/zulu/)Azure Java JDKs [Için uygun Azul Zulu kurumsal](https://assets.azul.com/files/Zulu-for-Azure-FAQ.pdf) 'ı indirin ve kullanın. Azure Işlevleri, işlev Uygulamanızı buluta dağıtırken Azul Java JDK çalışma zamanı kullanır.

JDKs ve Function uygulamalarıyla ilgili sorunlar için [Azure desteği](https://azure.microsoft.com/support/) , [nitelikli bir destek planıyla](https://azure.microsoft.com/support/plans/)sunulmaktadır.

## <a name="customize-jvm"></a>JVM 'YI özelleştirme

İşlevler, Java işlevlerinizi çalıştırmak için kullanılan Java sanal makinesini (JVM) özelleştirmenizi sağlar. [Aşağıdaki JVM seçenekleri](https://github.com/Azure/azure-functions-java-worker/blob/master/worker.config.json#L7) varsayılan olarak kullanılır:

* `-XX:+TieredCompilation`
* `-XX:TieredStopAtLevel=1`
* `-noverify` 
* `-Djava.net.preferIPv4Stack=true`
* `-jar`

Adlı bir uygulama ayarında ek bağımsız değişkenler sağlayabilirsiniz `JAVA_OPTS` . Azure portal veya Azure CLı 'de Azure 'a dağıtılan işlev uygulamanıza uygulama ayarları ekleyebilirsiniz.

> [!IMPORTANT]  
> Tüketim planında, özelleştirmenin çalışması için değeri 0 olan WEBSITE_USE_PLACEHOLDER ayarını da eklemeniz gerekir. Bu ayar, Java işlevleri için soğuk başlangıç sürelerini artırır.

### <a name="azure-portal"></a>Azure portalı

[Azure Portal](https://portal.azure.com), ayarı eklemek Için [uygulama ayarları sekmesini](functions-how-to-use-azure-function-app-settings.md#settings) kullanın `JAVA_OPTS` .

### <a name="azure-cli"></a>Azure CLI’si

Aşağıdaki örnekte olduğu gibi, ayarlamak için [az functionapp config appSettings set](/cli/azure/functionapp/config/appsettings) komutunu kullanabilirsiniz `JAVA_OPTS` :

# <a name="consumption-plan"></a>[Tüketim planı](#tab/consumption/bash)

```azurecli-interactive
az functionapp config appsettings set \
    --settings "JAVA_OPTS=-Djava.awt.headless=true" \
    "WEBSITE_USE_PLACEHOLDER=0" \
    --name <APP_NAME> --resource-group <RESOURCE_GROUP>
```

# <a name="consumption-plan"></a>[Tüketim planı](#tab/consumption/cmd)

```azurecli-interactive
az functionapp config appsettings set ^
    --settings "JAVA_OPTS=-Djava.awt.headless=true" ^
    "WEBSITE_USE_PLACEHOLDER=0" ^
    --name <APP_NAME> --resource-group <RESOURCE_GROUP>
```

# <a name="dedicated-plan--premium-plan"></a>[Adanmış plan/Premium plan](#tab/dedicated+premium/bash)

```azurecli-interactive
az functionapp config appsettings set \
    --settings "JAVA_OPTS=-Djava.awt.headless=true" \
    --name <APP_NAME> --resource-group <RESOURCE_GROUP>
```

# <a name="dedicated-plan--premium-plan"></a>[Adanmış plan/Premium plan](#tab/dedicated+premium/cmd)

```azurecli-interactive
az functionapp config appsettings set ^
    --settings "JAVA_OPTS=-Djava.awt.headless=true" ^
    --name <APP_NAME> --resource-group <RESOURCE_GROUP>
```

---

Bu örnek, gözetimsiz moda izin vermez. `<APP_NAME>`İşlev uygulamanızın adıyla ve `<RESOURCE_GROUP>` kaynak grubuyla değiştirin. 

## <a name="third-party-libraries"></a>Üçüncü taraf kitaplıkları 

Azure Işlevleri, üçüncü taraf kitaplıkların kullanımını destekler. Varsayılan olarak, proje dosyanızda belirtilen tüm bağımlılıklar, `pom.xml` hedef sırasında otomatik olarak paketlenmiştir [`mvn package`](https://github.com/Microsoft/azure-maven-plugins/blob/master/azure-functions-maven-plugin/README.md#azure-functionspackage) . Dosyada bağımlılıklar olarak belirtilmeyen kitaplıklar için `pom.xml` , bunları `lib` işlevin kök dizinindeki bir dizine yerleştirin. Dizine yerleştirilmiş bağımlılıklar, `lib` çalışma zamanında sistem sınıfı yükleyicilerine eklenir.

`com.microsoft.azure.functions:azure-functions-java-library`Bağımlılık, varsayılan olarak Sınıfyoluna sağlanır ve dizine eklenmesi gerekmez `lib` . Ayrıca, [Azure-Functions-Java-Worker](https://github.com/Azure/azure-functions-java-worker) , [burada](https://github.com/Azure/azure-functions-java-worker/wiki/Azure-Java-Functions-Worker-Dependencies) listelenen bağımlılıkları Sınıfyoluna ekler.

## <a name="data-type-support"></a>Veri türü desteği

Giriş veya çıkış bağlamalarına bağlamak için düz eski Java nesnelerini (POJOs), içinde tanımlanan türleri `azure-functions-java-library` veya dize ve tamsayı gibi temel veri türlerini kullanabilirsiniz.

### <a name="pojos"></a>POJOs

Giriş verilerini POJO 'ya dönüştürmek için [Azure-Functions-Java-Worker](https://github.com/Azure/azure-functions-java-worker) [gson](https://github.com/google/gson) kitaplığını kullanır. İşlevlere giriş olarak kullanılan POJO türleri olmalıdır `public` .

### <a name="binary-data"></a>İkili veriler

`byte[]`Üzerinde function.jsalanı olarak ayarlayarak ikili girdileri veya çıkışları öğesine bağlayın `dataType` `binary` :

```java
   @FunctionName("BlobTrigger")
    @StorageAccount("AzureWebJobsStorage")
     public void blobTrigger(
        @BlobTrigger(name = "content", path = "myblob/{fileName}", dataType = "binary") byte[] content,
        @BindingName("fileName") String fileName,
        final ExecutionContext context
    ) {
        context.getLogger().info("Java Blob trigger function processed a blob.\n Name: " + fileName + "\n Size: " + content.length + " Bytes");
    }
```

Null değerler bekleliyorsanız kullanın `Optional<T>` .

## <a name="bindings"></a>Bağlamalar

Giriş ve çıkış bağlamaları, kodunuzun içinden verilere bağlanmak için bildirim temelli bir yol sağlar. Bir işlevde birden fazla giriş ve çıkış bağlaması olabilir.

### <a name="input-binding-example"></a>Giriş bağlama örneği

```java
package com.example;

import com.microsoft.azure.functions.annotation.*;

public class Function {
    @FunctionName("echo")
    public static String echo(
        @HttpTrigger(name = "req", methods = { HttpMethod.PUT }, authLevel = AuthorizationLevel.ANONYMOUS, route = "items/{id}") String inputReq,
        @TableInput(name = "item", tableName = "items", partitionKey = "Example", rowKey = "{id}", connection = "AzureWebJobsStorage") TestInputData inputData,
        @TableOutput(name = "myOutputTable", tableName = "Person", connection = "AzureWebJobsStorage") OutputBinding<Person> testOutputData
    ) {
        testOutputData.setValue(new Person(httpbody + "Partition", httpbody + "Row", httpbody + "Name"));
        return "Hello, " + inputReq + " and " + inputData.getKey() + ".";
    }

    public static class TestInputData {
        public String getKey() { return this.RowKey; }
        private String RowKey;
    }
    public static class Person {
        public String PartitionKey;
        public String RowKey;
        public String Name;

        public Person(String p, String r, String n) {
            this.PartitionKey = p;
            this.RowKey = r;
            this.Name = n;
        }
    }
}
```

Bu işlevi bir HTTP isteğiyle çağırır. 
- HTTP istek yükü `String` , bağımsız değişken için bir olarak geçirilir `inputReq` .
- Tablo depolamadan bir giriş alınır ve `TestInputData` bağımsız değişkenine olarak geçirilir `inputData` .

Bir toplu işlem almak için,, veya ' a bağlayabilirsiniz `String[]` `POJO[]` `List<String>` `List<POJO>` .

```java
@FunctionName("ProcessIotMessages")
    public void processIotMessages(
        @EventHubTrigger(name = "message", eventHubName = "%AzureWebJobsEventHubPath%", connection = "AzureWebJobsEventHubSender", cardinality = Cardinality.MANY) List<TestEventData> messages,
        final ExecutionContext context)
    {
        context.getLogger().info("Java Event Hub trigger received messages. Batch size: " + messages.size());
    }
    
    public class TestEventData {
    public String id;
}

```

Bu işlev, yapılandırılan olay hub 'ında her yeni veri olduğunda tetiklenir. , `cardinality` Olarak ayarlandığı için `MANY` , işlevi olay hub 'ından bir ileti toplu işi alır. `EventData` Olay Hub 'ı `TestEventData` , işlev yürütmesi için öğesine dönüştürülür.

### <a name="output-binding-example"></a>Çıkış bağlama örneği

' I kullanarak, dönüş değerine bir çıkış bağlaması bağlayabilirsiniz `$return` . 

```java
package com.example;

import com.microsoft.azure.functions.annotation.*;

public class Function {
    @FunctionName("copy")
    @StorageAccount("AzureWebJobsStorage")
    @BlobOutput(name = "$return", path = "samples-output-java/{name}")
    public static String copy(@BlobTrigger(name = "blob", path = "samples-input-java/{name}") String content) {
        return content;
    }
}
```

Birden çok çıkış bağlaması varsa, bunlardan yalnızca biri için dönüş değerini kullanın.

Birden çok çıkış değeri göndermek için, `OutputBinding<T>` pakette tanımlanan öğesini kullanın `azure-functions-java-library` . 

```java
@FunctionName("QueueOutputPOJOList")
    public HttpResponseMessage QueueOutputPOJOList(@HttpTrigger(name = "req", methods = { HttpMethod.GET,
            HttpMethod.POST }, authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> request,
            @QueueOutput(name = "itemsOut", queueName = "test-output-java-pojo", connection = "AzureWebJobsStorage") OutputBinding<List<TestData>> itemsOut, 
            final ExecutionContext context) {
        context.getLogger().info("Java HTTP trigger processed a request.");
       
        String query = request.getQueryParameters().get("queueMessageId");
        String queueMessageId = request.getBody().orElse(query);
        itemsOut.setValue(new ArrayList<TestData>());
        if (queueMessageId != null) {
            TestData testData1 = new TestData();
            testData1.id = "msg1"+queueMessageId;
            TestData testData2 = new TestData();
            testData2.id = "msg2"+queueMessageId;

            itemsOut.getValue().add(testData1);
            itemsOut.getValue().add(testData2);

            return request.createResponseBuilder(HttpStatus.OK).body("Hello, " + queueMessageId).build();
        } else {
            return request.createResponseBuilder(HttpStatus.INTERNAL_SERVER_ERROR)
                    .body("Did not find expected items in CosmosDB input list").build();
        }
    }

     public static class TestData {
        public String id;
    }
```

Bu işlevi bir HttpRequest üzerinde çağırılır. Kuyruk depolamaya birden çok değer yazar.

## <a name="httprequestmessage-and-httpresponsemessage"></a>HttpRequestMessage ve HttpResponseMessage

 Bunlar ' de tanımlanmıştır `azure-functions-java-library` . HttpTrigger işlevleriyle çalışmak için yardımcı türlerdir.

| Özelleştirilmiş tür      |       Hedef        | Tipik kullanım                  |
| --------------------- | :-----------------: | ------------------------------ |
| `HttpRequestMessage<T>`  |    HTTP Tetikleyicisi     | Yöntemi, üstbilgileri veya sorguları alır |
| `HttpResponseMessage` | HTTP çıkış bağlama | 200 dışında bir durum döndürür   |

## <a name="metadata"></a>Meta veri

Birkaç tetikleyici, giriş verileriyle birlikte [tetikleyici meta verilerini](./functions-triggers-bindings.md) gönderir. Daha fazla açıklama kullanarak `@BindingName` tetikleyici meta verilerine bağlayabilirsiniz.


```Java
package com.example;

import java.util.Optional;
import com.microsoft.azure.functions.annotation.*;


public class Function {
    @FunctionName("metadata")
    public static String metadata(
        @HttpTrigger(name = "req", methods = { HttpMethod.GET, HttpMethod.POST }, authLevel = AuthorizationLevel.ANONYMOUS) Optional<String> body,
        @BindingName("name") String queryValue
    ) {
        return body.orElse(queryValue);
    }
}
```
Yukarıdaki örnekte,, `queryValue` `name` http istek URL 'sindeki sorgu dizesi parametresine bağlanır `http://{example.host}/api/metadata?name=test` . Sıra tetikleyicisi meta verilerinden nasıl bağlanılacağını gösteren başka bir örnek aşağıda verilmiştir `Id` .

```java
 @FunctionName("QueueTriggerMetadata")
    public void QueueTriggerMetadata(
        @QueueTrigger(name = "message", queueName = "test-input-java-metadata", connection = "AzureWebJobsStorage") String message,@BindingName("Id") String metadataId,
        @QueueOutput(name = "output", queueName = "test-output-java-metadata", connection = "AzureWebJobsStorage") OutputBinding<TestData> output,
        final ExecutionContext context
    ) {
        context.getLogger().info("Java Queue trigger function processed a message: " + message + " with metadaId:" + metadataId );
        TestData testData = new TestData();
        testData.id = metadataId;
        output.setValue(testData);
    }
```

> [!NOTE]
> Ek açıklamada belirtilen adın meta veri özelliğiyle eşleşmesi gerekir.

## <a name="execution-context"></a>Yürütme bağlamı

`ExecutionContext`, içinde tanımlanan, `azure-functions-java-library` işlevler çalışma zamanı ile iletişim kurmak için yardımcı yöntemleri içerir. Daha fazla bilgi için, [ExecutionContext başvuru makalesine](/java/api/com.microsoft.azure.functions.executioncontext)bakın.

### <a name="logger"></a>Medi

`getLogger` `ExecutionContext` İşlev kodundan Günlükler yazmak için ' de tanımlanan ' ı kullanın.

Örnek:

```java

import com.microsoft.azure.functions.*;
import com.microsoft.azure.functions.annotation.*;

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS) String req, ExecutionContext context) {
        if (req.isEmpty()) {
            context.getLogger().warning("Empty request body received by function " + context.getFunctionName() + " with invocation " + context.getInvocationId());
        }
        return String.format(req);
    }
}
```

## <a name="view-logs-and-trace"></a>Günlükleri görüntüleme ve izleme

Azure CLı 'yi kullanarak Java stdout ve stderr günlük kaydını ve diğer uygulama günlüğünü de yazdırabilirsiniz. 

Azure CLı kullanarak uygulama günlüğü yazmak için işlev uygulamanızı yapılandırma:

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli-interactive
az webapp log config --name functionname --resource-group myResourceGroup --application-logging true
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```azurecli-interactive
az webapp log config --name functionname --resource-group myResourceGroup --application-logging true
```

---

Azure CLı kullanarak işlev uygulamanıza ait günlük çıktısını akışa almak için yeni bir komut istemi, bash veya terminal oturumu açın ve aşağıdaki komutu girin:

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli-interactive
az webapp log tail --name webappname --resource-group myResourceGroup
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```azurecli-interactive
az webapp log tail --name webappname --resource-group myResourceGroup
```

---

[Az WebApp log tail](/cli/azure/webapp/log) komutu, seçeneğini kullanarak çıktıyı filtrelemeye yönelik seçeneklere sahiptir `--provider` . 

Azure CLı kullanarak günlük dosyalarını tek bir ZIP dosyası olarak indirmek için yeni bir komut istemi, bash veya terminal oturumu açın ve aşağıdaki komutu girin:

```azurecli-interactive
az webapp log download --resource-group resourcegroupname --name functionappname
```

Bu komutu çalıştırmadan önce Azure portal veya Azure CLı 'de dosya sistemi günlüğünü etkinleştirmiş olmanız gerekir.

## <a name="environment-variables"></a>Ortam değişkenleri

Işlevlerde, hizmet bağlantı dizeleri gibi [uygulama ayarları](functions-app-settings.md), yürütme sırasında ortam değişkenleri olarak sunulur. Bu ayarlara,, kullanarak erişebilirsiniz `System.getenv("AzureWebJobsStorage")` .

Aşağıdaki örnek, adlı anahtar ile [uygulama ayarını](functions-how-to-use-azure-function-app-settings.md#settings)alır `myAppSetting` :

```java

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS) String req, ExecutionContext context) {
        context.getLogger().info("My app setting value: "+ System.getenv("myAppSetting"));
        return String.format(req);
    }
}

```

> [!NOTE]
> En iyileştirilmiş soğuk başlangıç deneyimi için FUNCTIONS_EXTENSION_VERSION AppSetting değeri ~ 2 veya ~ 3 olmalıdır.

## <a name="next-steps"></a>Sonraki adımlar

Azure Işlevleri Java geliştirmesi hakkında daha fazla bilgi için aşağıdaki kaynaklara bakın:

* [Azure İşlevleri için en iyi uygulamalar](functions-best-practices.md)
* [Azure İşlevleri geliştirici başvurusu](functions-reference.md)
* [Azure Işlevleri Tetikleyicileri ve bağlamaları](functions-triggers-bindings.md)
* [Visual Studio Code](https://code.visualstudio.com/docs/java/java-azurefunctions), [IntelliJ](functions-create-maven-intellij.md)ve [tutulma](functions-create-maven-eclipse.md) ile yerel geliştirme ve hata ayıklama
* [Visual Studio Code kullanarak Java işlevleri ile uzaktan hata ayıklama](https://code.visualstudio.com/docs/java/java-serverless#_remote-debug-functions-running-in-the-cloud)
* [Azure Işlevleri için Maven eklentisi](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-functions-maven-plugin/README.md) 
* İşlev oluşturmayı amaç aracılığıyla kolaylaştırın `azure-functions:add` ve [ZIP dosya dağıtımı](deployment-zip-push.md)için bir hazırlama dizini hazırlayın.
