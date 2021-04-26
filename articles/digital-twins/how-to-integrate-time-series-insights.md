---
title: Azure Time Series Insights ile tümleştirme
titleSuffix: Azure Digital Twins
description: Bkz. Azure dijital TWINS 'ten olay yollarını ayarlama Azure Time Series Insights.
author: alexkarcher-msft
ms.author: alkarche
ms.date: 4/7/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: d89ee4c8e66ba4dda004fbd27e15b96ab13c642b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783782"
---
# <a name="integrate-azure-digital-twins-with-azure-time-series-insights"></a>Azure dijital TWINS 'i Azure Time Series Insights ile tümleştirme

Bu makalede, Azure dijital TWINS 'i [Azure Time Series Insights (TSI)](../time-series-insights/overview-what-is-tsi.md)ile tümleştirmeyi öğreneceksiniz.

Bu makalede açıklanan çözüm, IoT çözümünüz hakkında geçmiş verileri toplayıp analiz etmenize olanak sağlayacak. Azure Digital Twins, Time Series Insights'a veri akışı yapmak için ideal bir hizmettir ve bilgilerinizi Time Series Insights'a göndermeden önce birden çok veri akışı arasında bağıntı kurup standart hale getirmenizi sağlar.

## <a name="prerequisites"></a>Önkoşullar

Time Series Insights bir ilişki ayarlayabilmeniz için önce aşağıdaki kaynakları ayarlamanız gerekir:
* **IoT Hub 'ı**. Yönergeler için *IoT Hub telemetri gönderme* hızlı başlangıcı konusunun [*IoT Hub oluşturma*](../iot-hub/quickstart-send-telemetry-cli.md#create-an-iot-hub) bölümüne bakın.
* Bir **Azure dijital TWINS örneği**.
Yönergeler için bkz. [*nasıl yapılır: Azure dijital TWINS örneği ve kimlik doğrulaması ayarlama*](./how-to-set-up-instance-portal.md).
* **Azure dijital TWINS örneğindeki model ve ikizi**.
Time Series Insights ' de izlenen verileri görmek için ikizi 'un bilgilerini birkaç kez güncelleştirmeniz gerekir. Yönergeler için *nasıl yapılır: ınest IoT Hub* konusunun [*model ve ikizi ekleme*](how-to-ingest-iot-hub-data.md#add-a-model-and-twin) bölümüne bakın.

> [!TIP]
> Bu makalede, Time Series Insights görüntülenen değişen dijital ikizi değerleri kolaylık sağlaması için el ile güncelleştirilir. Ancak, bu makaleyi canlı sanal veriler ile tamamlayabilirsiniz, sanal bir cihazdan IoT telemetri olaylarına göre dijital TWINS 'i güncelleştiren bir Azure işlevi ayarlayabilirsiniz. Yönergeler için, cihaz simülatörünü çalıştırmanın son adımları ve veri akışının çalıştığını doğrulamak dahil olmak üzere, [*nasıl yapılır: alma IoT Hub verilerini*](how-to-ingest-iot-hub-data.md)izleyin.
>
> Daha sonra, cihaz simülatörünü çalıştırmaya başlamak için başka bir Ipucu arayın ve Azure işlevlerinizin el ile dijital ikizi güncelleştirme komutları göndermek yerine TWINS 'yi otomatik olarak güncelleştirmesini sağlayabilirsiniz.


## <a name="solution-architecture"></a>Çözüm mimarisi

Aşağıdaki yoldan Azure dijital TWINS 'e Time Series Insights iliştirilecektir.

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-integrate-time-series-insights/diagram-simple.png" alt-text="Uçtan uca bir senaryoda Azure hizmetlerinin diyagramı, Time Series Insights vurgulanmıştır." lightbox="media/how-to-integrate-time-series-insights/diagram-simple.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="create-event-hub-namespace"></a>Olay hub'ı ad alanı oluşturma

Olay Hub 'larını oluşturmadan önce, önce Azure Digital TWINS örneğinden olay alacak bir olay hub 'ı ad alanı oluşturacaksınız. Aşağıdaki Azure CLı yönergelerini kullanabilir veya Azure portal kullanabilirsiniz: [*hızlı başlangıç: Azure Portal kullanarak bir olay hub 'ı oluşturma*](../event-hubs/event-hubs-create.md). Hangi bölgelerin Olay Hub 'larını desteklediğini görmek için [*bölgeye göre kullanılabilir Azure ürünlerini*](https://azure.microsoft.com/global-infrastructure/services/?products=event-hubs)ziyaret edin.

```azurecli-interactive
az eventhubs namespace create --name <name-for-your-event-hubs-namespace> --resource-group <your-resource-group> -l <region>
```

> [!TIP]
> Belirten bir hata alırsanız `BadRequest: The specified service namespace is invalid.` , ad alanınız için seçtiğiniz adın bu başvuru belgesinde açıklanan adlandırma gereksinimlerini karşıladığından emin olun: [ad alanı oluştur](/rest/api/servicebus/create-namespace).

Bu olay hub 'ı ad alanını, bu makale için gereken iki olay hub 'ını tutmak üzere kullanacaksınız:

  1. **TWINS hub 'ı** -ikizi değişiklik olaylarını alacak Olay Hub 'ı
  2. **Zaman serisi hub 'ı** -Time Series Insights olayları akışa yönelik Olay Hub 'ı

Sonraki bölümlerde, bu hub 'ları Olay Hub 'ı ad alanınız içinde oluşturma ve yapılandırma işleminde size kılavuzluk edilir.

## <a name="create-twins-hub"></a>TWINS merkezi oluştur

Bu makalede oluşturacağınız ilk olay hub 'ı, **TWINS merkezdir**. Bu olay hub 'ı, Azure dijital TWINS 'ten ikizi değişiklik olayları alır.
TWINS hub 'ını ayarlamak için bu bölümde aşağıdaki adımları tamamlayacaksınız:

1. TWINS hub 'ını oluşturma
2. Hub için izinleri denetlemek üzere bir yetkilendirme kuralı oluşturma
3. Azure dijital TWINS 'de, hub 'a erişmek için yetkilendirme kuralını kullanan bir uç nokta oluşturma
4. Uç nokta ve bağlı TWINS hub 'ına ikizi Updates olayı gönderen Azure dijital TWINS 'de bir yol oluşturma
5. TWINS hub 'ı bağlantı dizesini al

Aşağıdaki CLı komutuyla **TWINS hub 'ını** oluşturun. TWINS hub 'ınız için bir ad belirtin.

```azurecli-interactive
az eventhubs eventhub create --name <name-for-your-twins-hub> --resource-group <your-resource-group> --namespace-name <your-event-hubs-namespace-from-above>
```

### <a name="create-twins-hub-authorization-rule"></a>TWINS hub 'ı yetkilendirme kuralı oluşturma

Gönderme ve alma izinleriyle bir [Yetkilendirme kuralı](/cli/azure/eventhubs/eventhub/authorization-rule#az_eventhubs_eventhub_authorization_rule_create) oluşturun. Kural için bir ad belirtin.

```azurecli-interactive
az eventhubs eventhub authorization-rule create --rights Listen Send --name <name-for-your-twins-hub-auth-rule> --resource-group <your-resource-group> --namespace-name <your-event-hubs-namespace-from-earlier> --eventhub-name <your-twins-hub-from-above>
```

### <a name="create-twins-hub-endpoint"></a>TWINS hub uç noktası oluştur

Olay Hub 'ınızı Azure dijital TWINS örneğinize bağlayan bir Azure dijital TWINS [uç noktası](concepts-route-events.md#create-an-endpoint) oluşturun. TWINS hub uç noktanız için bir ad belirtin.

```azurecli-interactive
az dt endpoint create eventhub -n <your-Azure-Digital-Twins-instance-name> --eventhub-resource-group <your-resource-group> --eventhub-namespace <your-event-hubs-namespace-from-earlier> --eventhub <your-twins-hub-name-from-above> --eventhub-policy <your-twins-hub-auth-rule-from-earlier> --endpoint-name <name-for-your-twins-hub-endpoint>
```

### <a name="create-twins-hub-event-route"></a>TWINS hub 'ı olay rotası oluştur

Azure dijital TWINS örnekleri, bir ikizi durumunun güncelleştirildiği her seferinde [ikizi Update olaylarını](how-to-interpret-event-data.md) yayabilir. Bu bölümde, daha fazla işleme için bu güncelleştirme olaylarını TWINS hub 'ına yönlendiren bir Azure dijital TWINS **olay yolu** oluşturacaksınız.

Yukarıdaki uç noktanıza ikizi güncelleştirme olayları göndermek için Azure dijital TWINS 'te bir [yol](concepts-route-events.md#create-an-event-route) oluşturun. Bu rotadaki filtre yalnızca ikizi güncelleştirme iletilerinin uç noktanıza geçirilmesine izin verir. TWINS hub 'ı olay rotası için bir ad belirtin.

```azurecli-interactive
az dt route create -n <your-Azure-Digital-Twins-instance-name> --endpoint-name <your-twins-hub-endpoint-from-above> --route-name <name-for-your-twins-hub-event-route> --filter "type = 'Microsoft.DigitalTwins.Twin.Update'"
```

### <a name="get-twins-hub-connection-string"></a>TWINS hub 'ı bağlantı dizesi al

TWINS hub 'ı için yukarıda oluşturduğunuz yetkilendirme kurallarını kullanarak [TWINS Olay Hub 'ı bağlantı dizesini](../event-hubs/event-hubs-get-connection-string.md)alın.

```azurecli-interactive
az eventhubs eventhub authorization-rule keys list --resource-group <your-resource-group> --namespace-name <your-event-hubs-namespace-from-earlier> --eventhub-name <your-twins-hub-from-above> --name <your-twins-hub-auth-rule-from-earlier>
```
Bu makalenin ilerleyen kısımlarında bulunan TWINS hub 'ı uygulamasını yapılandırmak için sonuçtan **Primaryconnectionstring** değerini bir yere göz atın.

## <a name="create-time-series-hub"></a>Zaman serisi hub 'ı oluştur

Bu makalede oluşturacağınız ikinci olay hub 'ı, **zaman serisi hub**'ıdır. Bu, Time Series Insights için Azure dijital TWINS olaylarını akışa alacak bir olay hub 'ı.
Zaman serisi hub 'ını ayarlamak için şu adımları tamamlayacaksınız:

1. Zaman serisi hub 'ını oluşturma
2. Hub için izinleri denetlemek üzere bir yetkilendirme kuralı oluşturma
3. Zaman serisi hub 'ı bağlantı dizesini al

Daha sonra, Time Series Insights örneğini oluşturduğunuzda, bu zaman serisi hub 'ını Time Series Insights örneği için olay kaynağı olarak bağlayacaksınız.

Aşağıdaki komutu kullanarak **zaman serisi hub 'ı** oluşturun. Zaman serisi hub 'ı için bir ad belirtin.

```azurecli-interactive
 az eventhubs eventhub create --name <name-for-your-time-series-hub> --resource-group <your-resource-group> --namespace-name <your-event-hub-namespace-from-earlier>
```

### <a name="create-time-series-hub-authorization-rule"></a>Zaman serisi hub yetkilendirme kuralı oluşturma

Gönderme ve alma izinleriyle bir [Yetkilendirme kuralı](/cli/azure/eventhubs/eventhub/authorization-rule#az_eventhubs_eventhub_authorization_rule_create) oluşturun. Zaman serisi hub 'ı kimlik doğrulama kuralı için bir ad belirtin.

```azurecli-interactive
az eventhubs eventhub authorization-rule create --rights Listen Send --name <name-for-your-time-series-hub-auth-rule> --resource-group <your-resource-group> --namespace-name <your-event-hub-namespace-from-earlier> --eventhub-name <your-time-series-hub-name-from-above>
```

### <a name="get-time-series-hub-connection-string"></a>Zaman serisi hub 'ı bağlantı dizesini al

Zaman serisi hub 'ı için yukarıda oluşturduğunuz yetkilendirme kurallarını kullanarak [zaman serisi hub 'ı bağlantı dizesini](../event-hubs/event-hubs-get-connection-string.md)alın:

```azurecli-interactive
az eventhubs eventhub authorization-rule keys list --resource-group <your-resource-group> --namespace-name <your-event-hub-namespace-from-earlier> --eventhub-name <your-time-series-hub-name-from-earlier> --name <your-time-series-hub-auth-rule-from-earlier>
```
Bu makalede daha sonra zaman serisi Hub uygulaması ayarını yapılandırmak için sonuçtan **Primaryconnectionstring** değerini bir yere göz atın.

Ayrıca, daha sonra Time Series Insights bir örnek oluşturmak için bunları kullanmak üzere aşağıdaki değerleri de göz önünde ayırın.
* Olay hub’ı ad alanı
* Zaman serisi hub 'ı adı
* Zaman serisi hub kimlik doğrulama kuralı

## <a name="create-a-function"></a>İşlev oluşturma

Bu bölümde, ikizi Update olaylarını kendi özgün formdan JSON yaması belgeleri olarak JSON nesneleri olarak dönüştürecek bir Azure işlevi oluşturacaksınız.

### <a name="step-1-create-function-app"></a>1. Adım: işlev uygulaması oluşturma

İlk olarak, Visual Studio 'da yeni bir işlev uygulama projesi oluşturun. Bunun nasıl yapılacağı hakkında yönergeler için bkz. *nasıl yapılır: verileri işlemek için bir Işlev ayarlama* makalesine yönelik [**Visual Studio 'Da işlev uygulaması oluşturma**](how-to-create-azure-function.md#create-a-function-app-in-visual-studio) başlıklı Bölüm.

### <a name="step-2-add-a-new-function"></a>2. Adım: yeni bir işlev ekleme

Cihaz telemetri olaylarını Time Series Insights güncelleştirmek için *ProcessDTUpdatetoTSI. cs* adlı yeni bir Azure işlevi oluşturun. İşlev türü, **Olay Hub 'ı tetikleyicisi** olacaktır.

:::image type="content" source="media/how-to-integrate-time-series-insights/create-event-hub-trigger-function.png" alt-text="Olay Hub 'ı tetikleyicisi türünde yeni bir Azure işlevi oluşturmak için Visual Studio 'nun ekran görüntüsü.":::

### <a name="step-3-fill-in-function-code"></a>3. Adım: işlev kodunu doldur

Aşağıdaki paketleri projenize ekleyin:
* [Microsoft. Azure. WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs/)
* [Microsoft. Azure. WebJobs. Extensions. EventHubs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs/)
* [Microsoft. NET. SDK. Functions](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/)

*ProcessDTUpdatetoTSI. cs* dosyasındaki kodu aşağıdaki kodla değiştirin:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/updateTSI.cs":::

İşlev kodunuzu kaydedin.

### <a name="step-4-publish-the-function-app-to-azure"></a>4. Adım: işlev uygulamasını Azure 'da yayımlama

Projeyi *ProcessDTUpdatetoTSI. cs* işleviyle Azure 'daki bir işlev uygulamasına yayımlayın.

Bunun nasıl yapılacağı hakkında yönergeler için bkz. *nasıl yapılır: verileri işlemek için bir Işlev ayarlama* makalesini [**Azure 'Da işlev uygulamasını yayımlama**](how-to-create-azure-function.md#publish-the-function-app-to-azure)

İki olay hub 'ı için uygulama ayarlarını yapılandırmak üzere daha sonra kullanmak üzere işlev uygulaması adını kaydedin.

### <a name="step-5-security-access-for-the-function-app"></a>5. Adım: işlev uygulaması için güvenlik erişimi

Sonra, işlev için **bir erişim rolü atayın** ve **uygulama ayarlarını** Azure dijital TWINS örneğinizi erişebilecek şekilde yapılandırın. Bunun nasıl yapılacağı hakkında yönergeler için bkz. *nasıl yapılır: verileri işlemek için bir Işlev ayarlama* makalesine [**yönelik güvenlik erişimini ayarlama**](how-to-create-azure-function.md#set-up-security-access-for-the-function-app) bölümüne bakın.

### <a name="step-6-configure-app-settings-for-the-two-event-hubs"></a>6. Adım: iki olay hub 'ı için uygulama ayarlarını yapılandırma

Daha sonra, işlev uygulamasının ayarlarına, bu, TWINS hub 'ına ve zaman serisi hub 'ına erişmesine imkan tanıyan ortam değişkenleri ekleyeceksiniz.

İşlev uygulamanızda, TWINS hub 'ı bağlantı dizesini içeren bir uygulama ayarı oluşturmak için daha önce kaydettiğiniz ikizlerini hub **primaryconnectionstring** değerini kullanın:

```azurecli-interactive
az functionapp config appsettings set --settings "EventHubAppSetting-Twins=<your-twins-hub-primaryConnectionString>" -g <your-resource-group> -n <your-App-Service-(function-app)-name>
```

İşlev uygulamanızda zaman serisi hub 'ı bağlantı dizesini içeren bir uygulama ayarı oluşturmak için daha önce kaydettiğiniz zaman serisi hub **Primaryconnectionstring** değerini kullanın:

```azurecli-interactive
az functionapp config appsettings set --settings "EventHubAppSetting-TSI=<your-time-series-hub-primaryConnectionString>" -g <your-resource-group> -n <your-App-Service-(function-app)-name>
```

## <a name="create-and-connect-a-time-series-insights-instance"></a>Time Series Insights örneği oluşturma ve bağlantı kurma

Bu bölümde, zaman serisi hub 'ınızdaki verileri alacak Time Series Insights örneğini ayarlayacaksınız. Bu süreç hakkında daha fazla ayrıntı için bkz. [*öğretici: Azure Time Series Insights Gen2 PAYG ortamı ayarlama*](../time-series-insights/tutorial-set-up-environment.md). Zaman serisi görüşleri ortamı oluşturmak için aşağıdaki adımları izleyin.

1. [Azure Portal](https://portal.azure.com), *Time Series Insights ortamları* arayın ve **Ekle** düğmesini seçin. Zaman serisi ortamını oluşturmak için aşağıdaki seçenekleri belirleyin.

    * **Abonelik** -aboneliğinizi seçin.
        - **Kaynak grubu** -kaynak grubunuzu seçin.
    * **Ortam adı** -zaman serisi ortamınız için bir ad belirleyin.
    * **Konum** -bir konum seçin.
    * **Katman** - **Gen2 (L1)** fiyatlandırma katmanını seçin.
    * **Özellik adı** - **$dtId** girin ( [*bir zaman serisi kimliği seçmek IÇIN en iyi yöntemler*](../time-series-insights/how-to-select-tsid.md)bölümünde kimlik değeri seçme hakkında daha fazla bilgi edinin).
    * **Depolama hesabı adı** -bir depolama hesabı adı belirtin.
    * **Yarı depolamayı etkinleştir** -bu alanı *Evet* olarak bırakın.

    Bu sayfadaki diğer özellikler için varsayılan değerleri bırakabilirsiniz. **Sonraki: olay kaynağı >** düğmesini seçin.

    :::image type="content" source="media/how-to-integrate-time-series-insights/create-time-series-insights-environment-1.png" alt-text="Time Series Insights ortamı oluşturmak için Azure portal ekran görüntüsü. Aboneliğiniz, kaynak grubunuz ve ilgili açılan kutudan konumunuzu seçin ve ortamınız için bir ad seçin." lightbox="media/how-to-integrate-time-series-insights/create-time-series-insights-environment-1.png":::
        
    :::image type="content" source="media/how-to-integrate-time-series-insights/create-time-series-insights-environment-2.png" alt-text="Time Series Insights ortamı oluşturmak için Azure portal ekran görüntüsü. Gen2 (L1) Fiyatlandırma Katmanı seçilidir ve zaman serisi KIMLIĞI Özellik adı $dtId." lightbox="media/how-to-integrate-time-series-insights/create-time-series-insights-environment-2.png":::

2. *Olay kaynağı* sekmesinde, aşağıdaki alanları seçin:

   * **Olay kaynağı oluşturulsun mu?** - *Evet*' i seçin.
   * **Kaynak türü** - *Olay Hub*'ı seçin.
   * **Ad** -olay kaynağınız için bir ad belirleyin.
   * **Abonelik** -Azure aboneliğinizi seçin.
   * **Olay Hub 'ı ad alanı** -Bu makalede daha önce oluşturduğunuz ad alanını seçin.
   * **Olay Hub 'ı adı** -Bu makalede daha önce oluşturduğunuz **zaman serisi hub** adını seçin.
   * **Olay Hub 'ı erişim ilkesi adı** -Bu makalede daha önce oluşturduğunuz *zaman serisi hub kimlik doğrulama kuralını* seçin.
   * **Olay Hub 'ı Tüketici grubu** - *Yeni* ' yi seçin ve Olay Hub 'ı tüketici grubunuz için bir ad belirtin. Ardından *Ekle*' yi seçin.
   * **Özellik adı** -bu alanı boş bırakın.
    
    Tüm ayrıntıları gözden geçirmek için **gözden geçir + oluştur** düğmesini seçin. Sonra, zaman serisi ortamını oluşturmak için **gözden geçir + oluştur** düğmesini tekrar seçin.

    :::image type="content" source="media/how-to-integrate-time-series-insights/create-tsi-environment-event-source.png" alt-text="Time Series Insights ortamı oluşturmak için Azure portal ekran görüntüsü. Yukarıdaki olay hub 'ı bilgileriyle bir olay kaynağı oluşturuyorsunuz. Ayrıca yeni bir tüketici grubu oluşturuyorsunuz." lightbox="media/how-to-integrate-time-series-insights/create-tsi-environment-event-source.png":::

## <a name="send-iot-data-to-azure-digital-twins"></a>Azure dijital TWINS 'e IoT verileri gönderme

Time Series Insights veri göndermeye başlamak için Azure dijital TWINS 'de, değişen veri değerleriyle Digital ikizi özelliklerini güncelleştirmeye başlamanız gerekir.

[Önkoşullar](#prerequisites) bölümünde örneğinize eklediğiniz *Thermostat67* ikizi üzerindeki *sıcaklık* özelliğini güncelleştirmek için aşağıdaki CLI komutunu kullanın.

```azurecli-interactive
az dt twin update -n <your-azure-digital-twins-instance-name> --twin-id thermostat67 --json-patch '{"op":"replace", "path":"/Temperature", "value": 20.5}'
```

Time Series Insights ortamında daha sonra gözlemlenebilir çeşitli veri noktaları oluşturmak için **komutu farklı sıcaklık değerleriyle en az 4 kez yineleyin**.

> [!TIP]
> Bu makaleyi dijital ikizi değerlerini el ile güncelleştirmek yerine canlı sanal verilerle tamamladığınızda, önce sanal bir cihazdan TWINS 'yi güncelleştiren bir Azure işlevi ayarlamak için [*Önkoşullar*](#prerequisites) bölümündeki ipucunu tamamladığınızdan emin olun.
Bundan sonra cihazı, sanal verileri göndermeye ve bu veri akışı aracılığıyla dijital ikizi güncelleştirmeye başlamak için şimdi çalıştırabilirsiniz.

## <a name="visualize-your-data-in-time-series-insights"></a>Time Series Insights verilerini görselleştirme

Şimdi, verilerin çözümlenmeye hazır Time Series Insights Örneğinizde akışı yapılmalıdır. İçinde gelen verileri araştırmak için aşağıdaki adımları izleyin.

1. [Azure Portal](https://portal.azure.com), daha önce oluşturduğunuz zaman serisi ortam adınızı arayın. Sol taraftaki menü seçeneklerinde, *Time Series Insights Explorer URL 'sini* görmek Için *genel bakış* ' ı seçin. Time Series Insights ortamında yansıtılan sıcaklık değişikliklerinin görüntüleneceği URL 'YI seçin.

    :::image type="content" source="media/how-to-integrate-time-series-insights/view-environment.png" alt-text="Time Series Insights ortamınızın Genel Bakış sekmesinde Time Series Insights gezgin URL 'sini seçmek için Azure portal ekran görüntüsü." lightbox="media/how-to-integrate-time-series-insights/view-environment.png":::

2. Gezgin 'de, sol tarafta gösterilen Azure dijital TWINS örneğinde TWINS 'i görürsünüz. *Thermostat67* ikizi öğesini seçin, özellik *sıcaklığını* seçin ve **Ekle**' ye tıklayın.

    :::image type="content" source="media/how-to-integrate-time-series-insights/add-data.png" alt-text="Time Series Insights Gezgini 'nin ekran görüntüsü thermostat67 seçin, özellik sıcaklığını seçin ve Ekle ' ye tıklayın." lightbox="media/how-to-integrate-time-series-insights/add-data.png":::

3. Artık aşağıda gösterildiği gibi, termostat 'daki ilk sıcaklık ayarlarını görmeniz gerekir. 

    :::image type="content" source="media/how-to-integrate-time-series-insights/initial-data.png" alt-text="İlk sıcaklık verilerini görüntülemek için TSI Explorer 'ın ekran görüntüsü. 68 ile 85 arasındaki rastgele değerlerin bir satırdır" lightbox="media/how-to-integrate-time-series-insights/initial-data.png":::

Bir benzetimin çok daha uzun süre çalışmasına izin verirseniz görselleştirmeniz şuna benzer şekilde görünür:

:::image type="content" source="media/how-to-integrate-time-series-insights/day-data.png" alt-text="Her bir ikizi için sıcaklık verilerinin, farklı renklerin üç paralel satırı halinde grafiği bulunan TSI Explorer 'ın ekran görüntüsü." lightbox="media/how-to-integrate-time-series-insights/day-data.png":::

## <a name="next-steps"></a>Sonraki adımlar

Dijital TWINS, varsayılan olarak Time Series Insights düz bir hiyerarşi olarak saklanır, ancak model bilgileri ve kuruluş için çok düzeyli bir hiyerarşiyle zenginleştirilebilir. Bu işlem hakkında daha fazla bilgi edinmek için şunu okuyun: 

* [*Öğretici: model tanımlama ve uygulama*](../time-series-insights/tutorial-set-up-environment.md#define-and-apply-a-model) 

Azure dijital TWINS 'te zaten depolanmış olan model ve grafik verilerini kullanarak bu bilgileri otomatik olarak sağlamak için özel mantık yazabilirsiniz. TWINS grafiğindeki bilgileri yönetme, yükseltme ve alma hakkında daha fazla bilgi edinmek için aşağıdaki başvurulara bakın:

* [*Nasıl yapılır: dijital ikizi yönetme*](./how-to-manage-twin.md)
* [*Nasıl yapılır: ikizi grafiğini sorgulama*](./how-to-query-graph.md)