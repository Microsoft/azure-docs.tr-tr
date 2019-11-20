---
title: 'Öğretici: Azure Cosmos DB ve Event Hubs Java işlevlerini kullanma'
description: Bu öğreticide, Java 'da yazılmış bir işlevi kullanarak Azure Cosmos DB güncelleştirme yapmak için Event Hubs olaylarının nasıl kullanılacağı gösterilmektedir.
author: KarlErickson
manager: barbkess
ms.service: azure-functions
ms.devlang: java
ms.topic: tutorial
ms.date: 11/04/2019
ms.author: karler
ms.openlocfilehash: 172dda0a79620431d41a5be7e872b6b1507a6c81
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73608711"
---
# <a name="tutorial-create-a-function-in-java-with-an-event-hub-trigger-and-an-azure-cosmos-db-output-binding"></a>Öğretici: bir olay hub 'ı tetikleyicisi ve bir Azure Cosmos DB çıktı bağlaması ile Java 'da işlev oluşturma

Bu öğreticide, sürekli sıcaklık ve basınç verilerini analiz eden bir Java işlevi oluşturmak için Azure Işlevlerinin nasıl kullanılacağı gösterilmektedir. Algılayıcı okumaları temsil eden olay hub 'ı olayları işlevi tetikler. İşlevi, olay verilerini işler ve sonra durum girdilerini bir Azure Cosmos DB ekler.

Bu öğreticide şunları yapmanız gerekir:

> [!div class="checklist"]
> * Azure CLı kullanarak Azure kaynakları oluşturun ve yapılandırın.
> * Bu kaynaklarla etkileşime geçen Java işlevlerini oluşturun ve test edin.
> * İşlevlerinizi Azure 'a dağıtın ve Application Insights ile izleyin.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Ön koşullar

Bu öğreticiyi tamamlayabilmeniz için aşağıdakilerin yüklü olması gerekir:

* [Java geliştirici seti](https://aka.ms/azure-jdks), sürüm 8
* [Apache Maven](https://maven.apache.org), sürüm 3,0 veya üzeri
* Kullanmayı tercih ederseniz [Azure clı](/cli/azure/install-azure-cli) Cloud Shell
* [Azure Functions Core Tools](https://www.npmjs.com/package/azure-functions-core-tools) sürüm 2.6.666 veya üzeri

> [!IMPORTANT]
> Bu öğreticiyi tamamlayabilmeniz için `JAVA_HOME` ortam değişkeni JDK 'nin Install konumuna ayarlanmalıdır.

Bu öğreticide doğrudan kodu kullanmayı tercih ediyorsanız, bkz. [Java-Functions-eventhub-cosmosdb](https://github.com/Azure-Samples/java-functions-eventhub-cosmosdb) örnek deposu.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-azure-resources"></a>Azure kaynakları oluşturma

Bu öğreticide, şu kaynaklara ihtiyacınız olacaktır:

* Diğer kaynakları içeren bir kaynak grubu
* Bir Event Hubs ad alanı, Olay Hub 'ı ve yetkilendirme kuralı
* Bir Cosmos DB hesabı, veritabanı ve koleksiyonu
* Bir işlev uygulaması ve barındırmak için bir depolama hesabı

Aşağıdaki bölümlerde, Azure CLı kullanarak bu kaynakları nasıl oluşturacağınız gösterilmektedir.

### <a name="log-in-to-azure"></a>Azure'da oturum açma

Cloud Shell kullanmıyorsanız, hesabınıza erişmek için Azure CLı 'yi yerel olarak kullanmanız gerekir. Tarayıcı tabanlı oturum açma deneyimini başlatmak için bash isteminde `az login` komutunu kullanın. Birden fazla Azure aboneliğine erişiminiz varsa, varsayılan değer olarak `az account set --subscription` ve ardından abonelik KIMLIĞI ' ni ayarlayın.

### <a name="set-environment-variables"></a>Ortam değişkenlerini belirleme

Sonra, oluşturacağınız kaynakların adları ve konumu için bazı ortam değişkenleri oluşturun. `<value>` yer tutucuları seçtiğiniz değerlerle değiştirerek aşağıdaki komutları kullanın. Değerler, [Azure kaynakları için adlandırma kurallarına ve kısıtlamalarına](/azure/architecture/best-practices/resource-naming)uymalıdır. `LOCATION` değişkeni için, `az functionapp list-consumption-locations` komutu tarafından oluşturulan değerlerden birini kullanın.

```azurecli-interactive
RESOURCE_GROUP=<value>
EVENT_HUB_NAMESPACE=<value>
EVENT_HUB_NAME=<value>
EVENT_HUB_AUTHORIZATION_RULE=<value>
COSMOS_DB_ACCOUNT=<value>
STORAGE_ACCOUNT=<value>
FUNCTION_APP=<value>
LOCATION=<value>
```

Bu öğreticinin geri kalanı bu değişkenleri kullanır. Bu değişkenlerin yalnızca geçerli Azure CLı veya Cloud Shell oturumunuz süresince kalıcı olduğunu unutmayın. Farklı bir yerel Terminal penceresi kullanıyorsanız veya Cloud Shell oturumunuz zaman aşımına uğrarsa bu komutları yeniden çalıştırmanız gerekir.

### <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

Azure, hesabınızdaki tüm ilgili kaynakları toplamak için kaynak gruplarını kullanır. Bu şekilde, bunları bir birim olarak görüntüleyebilir ve bunlarla işiniz bittiğinde tek bir komutla silebilirsiniz.

Bir kaynak grubu oluşturmak için aşağıdaki komutu kullanın:

```azurecli-interactive
az group create \
    --name $RESOURCE_GROUP \
    --location $LOCATION
```

### <a name="create-an-event-hub"></a>Olay hub’ı oluşturma

Ardından, aşağıdaki komutları kullanarak bir Azure Event Hubs ad alanı, Olay Hub 'ı ve yetkilendirme kuralı oluşturun:

```azurecli-interactive
az eventhubs namespace create \
    --resource-group $RESOURCE_GROUP \
    --name $EVENT_HUB_NAMESPACE
az eventhubs eventhub create \
    --resource-group $RESOURCE_GROUP \
    --name $EVENT_HUB_NAME \
    --namespace-name $EVENT_HUB_NAMESPACE \
    --message-retention 1
az eventhubs eventhub authorization-rule create \
    --resource-group $RESOURCE_GROUP \
    --name $EVENT_HUB_AUTHORIZATION_RULE \
    --eventhub-name $EVENT_HUB_NAME \
    --namespace-name $EVENT_HUB_NAMESPACE \
    --rights Listen Send
```

Event Hubs ad alanı gerçek olay hub 'ını ve yetkilendirme kuralını içerir. Yetkilendirme kuralı, işlevlerinizin hub 'a ileti göndermesini ve ilgili olayları dinlemesini sağlar. Bir işlev telemetri verilerini temsil eden iletiler gönderir. Başka bir işlev olayları dinler, olay verilerini analiz eder ve sonuçları Azure Cosmos DB depolar.

### <a name="create-an-azure-cosmos-db"></a>Bir Azure Cosmos DB oluşturun

Ardından, aşağıdaki komutları kullanarak bir Azure Cosmos DB hesabı, veritabanı ve koleksiyonu oluşturun:

```azurecli-interactive
az cosmosdb create \
    --resource-group $RESOURCE_GROUP \
    --name $COSMOS_DB_ACCOUNT
az cosmosdb database create \
    --resource-group-name $RESOURCE_GROUP \
    --name $COSMOS_DB_ACCOUNT \
    --db-name TelemetryDb
az cosmosdb collection create \
    --resource-group-name $RESOURCE_GROUP \
    --name $COSMOS_DB_ACCOUNT \
    --collection-name TelemetryInfo \
    --db-name TelemetryDb \
    --partition-key-path '/temperatureStatus'
```

`partition-key-path` değeri, her öğenin `temperatureStatus` değerine göre verilerinizi bölümlendirir. Bölüm anahtarı, verilerinizi bağımsız olarak erişebileceği farklı alt kümelere ayırarak performansı artırmaya Cosmos DB olanak sağlar.

### <a name="create-a-storage-account-and-function-app"></a>Depolama hesabı ve işlev uygulaması oluşturma

Ardından, Azure Işlevleri için gerekli olan bir Azure depolama hesabı oluşturun ve ardından işlev uygulamasını oluşturun. Aşağıdaki komutları kullanın:

```azurecli-interactive
az storage account create \
    --resource-group $RESOURCE_GROUP \
    --name $STORAGE_ACCOUNT \
    --sku Standard_LRS
az functionapp create \
    --resource-group $RESOURCE_GROUP \
    --name $FUNCTION_APP \
    --storage-account $STORAGE_ACCOUNT \
    --consumption-plan-location $LOCATION \
    --runtime java
```

`az functionapp create` komutu işlev uygulamanızı oluşturduğunda aynı ada sahip bir Application Insights kaynağı da oluşturur. İşlev uygulaması, Application Insights bağlayan `APPINSIGHTS_INSTRUMENTATIONKEY` adlı bir ayarla otomatik olarak yapılandırılır. Bu öğreticinin ilerleyen kısımlarında açıklandığı gibi, işlevlerinizi Azure 'a dağıttıktan sonra uygulama telemetrisini görüntüleyebilirsiniz.

## <a name="configure-your-function-app"></a>İşlev uygulamanızı yapılandırma

İşlev uygulamanızın doğru şekilde çalışması için diğer kaynaklara erişmesi gerekir. Aşağıdaki bölümlerde, işlev uygulamanızı yerel makinenizde çalışacak şekilde nasıl yapılandırabileceğiniz gösterilmektedir.

### <a name="retrieve-resource-connection-strings"></a>Kaynak bağlantı dizelerini alma

Depolama, Olay Hub 'ı ve Cosmos DB bağlantı dizelerini almak ve bunları ortam değişkenlerine kaydetmek için aşağıdaki komutları kullanın:

```azurecli-interactive
AZURE_WEB_JOBS_STORAGE=$( \
    az storage account show-connection-string \
        --name $STORAGE_ACCOUNT \
        --query connectionString \
        --output tsv)
echo $AZURE_WEB_JOBS_STORAGE
EVENT_HUB_CONNECTION_STRING=$( \
    az eventhubs eventhub authorization-rule keys list \
        --resource-group $RESOURCE_GROUP \
        --name $EVENT_HUB_AUTHORIZATION_RULE \
        --eventhub-name $EVENT_HUB_NAME \
        --namespace-name $EVENT_HUB_NAMESPACE \
        --query primaryConnectionString \
        --output tsv)
echo $EVENT_HUB_CONNECTION_STRING
COSMOS_DB_CONNECTION_STRING=$( \
    az cosmosdb keys list \
        --resource-group $RESOURCE_GROUP \
        --name $COSMOS_DB_ACCOUNT \
        --type connection-strings \
        --query connectionStrings[0].connectionString \
        --output tsv)
echo $COSMOS_DB_CONNECTION_STRING
```

Bu değişkenler, Azure CLı komutlarından alınan değerlere ayarlanır. Her komut, döndürülen JSON yükünün bağlantı dizesini ayıklamak için bir JMESPath sorgusu kullanır. Bağlantı dizeleri de `echo` kullanılarak görüntülenir, böylece başarıyla alındığını doğrulayabilirsiniz.

### <a name="update-your-function-app-settings"></a>İşlev uygulaması ayarlarınızı güncelleştirme

Sonra, bağlantı dizesi değerlerini Azure Işlevleri hesabınızdaki uygulama ayarlarına aktarmak için aşağıdaki komutu kullanın:

```azurecli-interactive
az functionapp config appsettings set \
    --resource-group $RESOURCE_GROUP \
    --name $FUNCTION_APP \
    --settings \
        AzureWebJobsStorage=$AZURE_WEB_JOBS_STORAGE \
        EventHubConnectionString=$EVENT_HUB_CONNECTION_STRING \
        CosmosDBConnectionString=$COSMOS_DB_CONNECTION_STRING
```

Azure kaynaklarınız artık oluşturulmuştur ve birlikte düzgün çalışacak şekilde yapılandırılmıştır.

## <a name="create-and-test-your-functions"></a>İşlevlerinizi oluşturma ve test etme

Ardından, yerel makinenizde bir proje oluşturacak, Java kodunu ekleyecek ve test edersiniz. Azure Işlevleri ve Azure Functions Core Tools Maven eklentisi ile çalışan komutları kullanacaksınız. İşlevleriniz yerel olarak çalışır, ancak oluşturduğunuz bulut tabanlı kaynakları kullanacaktır. Yerel olarak çalışan işlevleri aldıktan sonra, Maven kullanarak bunları buluta dağıtabilir ve veri ve analiz birikmesini izleyebilirsiniz.

Kaynaklarınızın oluşturulması için Cloud Shell kullandıysanız, Azure 'a yerel olarak Bağlanmayacağız. Bu durumda, tarayıcı tabanlı oturum açma işlemini başlatmak için `az login` komutunu kullanın. Gerekirse, varsayılan aboneliği `az account set --subscription` ve ardından abonelik KIMLIĞI ile ayarlayın. Son olarak, yerel makinenizde bazı ortam değişkenlerini yeniden oluşturmak için aşağıdaki komutları çalıştırın. `<value>` yer tutucuları, daha önce kullandığınız değerlerle değiştirin.

```bash
RESOURCE_GROUP=<value>
FUNCTION_APP=<value>
```

### <a name="create-a-local-functions-project"></a>Yerel işlevler projesi oluşturma

Bir işlevler projesi oluşturmak ve gerekli bağımlılıkları eklemek için aşağıdaki Maven komutunu kullanın.

```bash
mvn archetype:generate --batch-mode \
    -DarchetypeGroupId=com.microsoft.azure \
    -DarchetypeArtifactId=azure-functions-archetype \
    -DappName=$FUNCTION_APP \
    -DresourceGroup=$RESOURCE_GROUP \
    -DgroupId=com.example \
    -DartifactId=telemetry-functions
```

Bu komut `telemetry-functions` bir klasör içinde birkaç dosya oluşturur:

* Maven ile kullanım için bir `pom.xml` dosyası
* Yerel test için uygulama ayarlarını tutacak bir `local.settings.json` dosyası
* Veri çözümleme işlevinizdeki Cosmos DB çıkış bağlaması için gerekli olan Azure Işlevleri uzantısı paketini sağlayan bir `host.json` dosyası
* Varsayılan işlev uygulamasını içeren bir `Function.java` dosyası
* Bu öğreticiye gereken birkaç test dosyası

Derleme hatalarını önlemek için, test dosyalarını silmeniz gerekir. Yeni proje klasörüne gitmek ve test klasörünü silmek için aşağıdaki komutları çalıştırın:

```bash
cd telemetry-functions
rm -r src/test
```

### <a name="retrieve-your-function-app-settings-for-local-use"></a>Yerel kullanım için işlev uygulaması ayarlarınızı alma

Yerel test için, işlev projenizin Bu öğreticide daha önce Azure 'daki işlev uygulamanıza eklediğiniz bağlantı dizeleri gerekir. Bulutta depolanan tüm işlev uygulaması ayarlarını alan ve `local.settings.json` dosyanıza ekleyen aşağıdaki Azure Functions Core Tools komutunu kullanın:

```bash
func azure functionapp fetch-app-settings $FUNCTION_APP
```

### <a name="add-java-code"></a>Java kodu ekle

Sonra, `Function.java` dosyasını açın ve içeriğini aşağıdaki kodla değiştirin.

```java
package com.example;

import com.example.TelemetryItem.status;
import com.microsoft.azure.functions.annotation.Cardinality;
import com.microsoft.azure.functions.annotation.CosmosDBOutput;
import com.microsoft.azure.functions.annotation.EventHubOutput;
import com.microsoft.azure.functions.annotation.EventHubTrigger;
import com.microsoft.azure.functions.annotation.FunctionName;
import com.microsoft.azure.functions.annotation.TimerTrigger;
import com.microsoft.azure.functions.ExecutionContext;
import com.microsoft.azure.functions.OutputBinding;

public class Function {

    @FunctionName("generateSensorData")
    @EventHubOutput(
        name = "event",
        eventHubName = "", // blank because the value is included in the connection string
        connection = "EventHubConnectionString")
    public TelemetryItem generateSensorData(
        @TimerTrigger(
            name = "timerInfo",
            schedule = "*/10 * * * * *") // every 10 seconds
            String timerInfo,
        final ExecutionContext context) {

        context.getLogger().info("Java Timer trigger function executed at: "
            + java.time.LocalDateTime.now());
        double temperature = Math.random() * 100;
        double pressure = Math.random() * 50;
        return new TelemetryItem(temperature, pressure);
    }

    @FunctionName("processSensorData")
    public void processSensorData(
        @EventHubTrigger(
            name = "msg",
            eventHubName = "", // blank because the value is included in the connection string
            cardinality = Cardinality.ONE,
            connection = "EventHubConnectionString")
            TelemetryItem item,
        @CosmosDBOutput(
            name = "databaseOutput",
            databaseName = "TelemetryDb",
            collectionName = "TelemetryInfo",
            connectionStringSetting = "CosmosDBConnectionString")
            OutputBinding<TelemetryItem> document,
        final ExecutionContext context) {

        context.getLogger().info("Event hub message received: " + item.toString());

        if (item.getPressure() > 30) {
            item.setNormalPressure(false);
        } else {
            item.setNormalPressure(true);
        }

        if (item.getTemperature() < 40) {
            item.setTemperatureStatus(status.COOL);
        } else if (item.getTemperature() > 90) {
            item.setTemperatureStatus(status.HOT);
        } else {
            item.setTemperatureStatus(status.WARM);
        }

        document.setValue(item);
    }
}
```

Görebileceğiniz gibi, bu dosya `generateSensorData` ve `processSensorData`iki işlev içerir. `generateSensorData` işlevi, Olay Hub 'ına sıcaklık ve basınç okumaları gönderen bir algılayıcının benzetimini yapar. Bir Zamanlayıcı tetikleyicisi, işlevi 10 saniyede bir çalıştırır ve bir olay hub 'ı çıkış bağlaması, dönüş değerini Olay Hub 'ına gönderir.

Olay Hub 'ı iletiyi aldığında bir olay oluşturur. `processSensorData` işlevi olayı aldığında çalışır. Daha sonra olay verilerini işler ve sonuçları Azure Cosmos DB göndermek için bir Azure Cosmos DB çıkış bağlaması kullanır.

Bu işlevler tarafından kullanılan veriler, uygulamanız gereken `TelemetryItem`adlı bir sınıf kullanılarak saklanır. `Function.java` ile aynı konumda `TelemetryItem.java` adlı yeni bir dosya oluşturun ve aşağıdaki kodu ekleyin:

```java
package com.example;

public class TelemetryItem {

    private String id;
    private double temperature;
    private double pressure;
    private boolean isNormalPressure;
    private status temperatureStatus;
    static enum status {
        COOL,
        WARM,
        HOT
    }

    public TelemetryItem(double temperature, double pressure) {
        this.temperature = temperature;
        this.pressure = pressure;
    }

    public String getId() {
        return id;
    }

    public double getTemperature() {
        return temperature;
    }

    public double getPressure() {
        return pressure;
    }

    @Override
    public String toString() {
        return "TelemetryItem={id=" + id + ",temperature="
            + temperature + ",pressure=" + pressure + "}";
    }

    public boolean isNormalPressure() {
        return isNormalPressure;
    }

    public void setNormalPressure(boolean isNormal) {
        this.isNormalPressure = isNormal;
    }

    public status getTemperatureStatus() {
        return temperatureStatus;
    }

    public void setTemperatureStatus(status temperatureStatus) {
        this.temperatureStatus = temperatureStatus;
    }
}
```

### <a name="run-locally"></a>Yerel olarak çalıştırma

Artık işlevleri yerel olarak oluşturup çalıştırabilir ve Azure Cosmos DB verileri görebilirsiniz.

İşlevleri derlemek ve çalıştırmak için aşağıdaki Maven komutlarını kullanın:

```bash
mvn clean package
mvn azure-functions:run
```

Bazı derleme ve başlangıç iletilerinden sonra, işlevlerin her çalıştırılışında aşağıdaki örneğe benzer bir çıktı görürsünüz:

```output
[10/22/19 4:01:30 AM] Executing 'Functions.generateSensorData' (Reason='Timer fired at 2019-10-21T21:01:30.0016769-07:00', Id=c1927c7f-4f70-4a78-83eb-bc077d838410)
[10/22/19 4:01:30 AM] Java Timer trigger function executed at: 2019-10-21T21:01:30.015
[10/22/19 4:01:30 AM] Function "generateSensorData" (Id: c1927c7f-4f70-4a78-83eb-bc077d838410) invoked by Java Worker
[10/22/19 4:01:30 AM] Executed 'Functions.generateSensorData' (Succeeded, Id=c1927c7f-4f70-4a78-83eb-bc077d838410)
[10/22/19 4:01:30 AM] Executing 'Functions.processSensorData' (Reason='', Id=f4c3b4d7-9576-45d0-9c6e-85646bb52122)
[10/22/19 4:01:30 AM] Event hub message received: TelemetryItem={id=null,temperature=32.728691307527015,pressure=10.122563042388165}
[10/22/19 4:01:30 AM] Function "processSensorData" (Id: f4c3b4d7-9576-45d0-9c6e-85646bb52122) invoked by Java Worker
[10/22/19 4:01:38 AM] Executed 'Functions.processSensorData' (Succeeded, Id=1cf0382b-0c98-4cc8-9240-ee2a2f71800d)
```

Daha sonra [Azure Portal](https://portal.azure.com) gidebilir ve Azure Cosmos DB hesabınıza gidebilirsiniz. **Veri Gezgini**seçin, **TelemetryInfo**' ı genişletin ve ardından verileri geldiğinde görüntülemek için **öğeleri** seçin.

![Cosmos DB Veri Gezgini](media/functions-event-hub-cosmos-db/data-explorer.png)

## <a name="deploy-to-azure-and-view-app-telemetry"></a>Azure 'a dağıtma ve uygulama telemetrisini görüntüleme

Son olarak, uygulamanızı Azure 'a dağıtabilir ve yerel olarak olduğu şekilde çalışmaya devam ettiğini doğrulayabilirsiniz.

Aşağıdaki komutu kullanarak projenizi Azure 'a dağıtın:

```bash
mvn azure-functions:deploy
```

İşlevleriniz artık Azure 'da çalışır ve Azure Cosmos DB verileri birikmeye devam eder. Dağıtılmış işlev uygulamanızı Azure portal görüntüleyebilir ve aşağıdaki ekran görüntülerinde gösterildiği gibi bağlı Application Insights kaynağı aracılığıyla uygulama telemetrisini görüntüleyebilirsiniz:

**Canlı Ölçüm Akışı:**

![Application Insights Canlı Ölçüm Akışı](media/functions-event-hub-cosmos-db/application-insights-live-metrics-stream.png)

**Performans:**

![Application Insights performans dikey penceresi](media/functions-event-hub-cosmos-db/application-insights-performance.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu öğreticide oluşturduğunuz Azure kaynaklarıyla işiniz bittiğinde, aşağıdaki komutu kullanarak bunları silebilirsiniz:

```azurecli-interactive
az group delete --name $RESOURCE_GROUP
```

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, Olay Hub 'ı olaylarını işleyen ve bir Cosmos DB güncelleştiren bir Azure Işlevi oluşturmayı öğrendiniz. Daha fazla bilgi için bkz. [Azure Işlevleri Java geliştirici kılavuzu](/azure/azure-functions/functions-reference-java). Kullanılan ek açıklamalar hakkında daha fazla bilgi için bkz. [com. Microsoft. Azure. Functions. Annotation](/java/api/com.microsoft.azure.functions.annotation) başvurusu.

Bu öğretici, bağlantı dizeleri gibi gizli dizileri depolamak için ortam değişkenlerini ve uygulama ayarlarını kullandı. Bu gizli dizileri Azure Key Vault depolama hakkında bilgi için bkz. [App Service ve Azure işlevleri için Key Vault başvurularını kullanma](/azure/app-service/app-service-key-vault-references).

Sonra, otomatik dağıtım için Azure Pipelines CI/CD 'yi nasıl kullanacağınızı öğrenin:

> [!div class="nextstepaction"]
> [Java oluşturma ve Azure 'a dağıtma Işlevleri](/azure/devops/pipelines/ecosystems/java-function)
