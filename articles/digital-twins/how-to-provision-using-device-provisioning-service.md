---
title: Cihaz sağlama hizmeti 'ni kullanarak cihazları otomatik olarak yönetme
titleSuffix: Azure Digital Twins
description: Cihaz sağlama hizmeti 'ni (DPS) kullanarak Azure dijital TWINS 'te IoT cihazlarını sağlamak ve devre dışı bırakmak için otomatik bir işlem ayarlama bölümüne bakın.
author: baanders
ms.author: baanders
ms.date: 3/21/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 51b5714f9009cbe48aa49c6a04a1434cec12396e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790698"
---
# <a name="auto-manage-devices-in-azure-digital-twins-using-device-provisioning-service-dps"></a>Cihaz sağlama hizmeti 'ni (DPS) kullanarak Azure dijital TWINS 'de cihazları otomatik olarak yönetme

Bu makalede, Azure Digital TWINS 'i [cihaz sağlama hizmeti (DPS)](../iot-dps/about-iot-dps.md)ile tümleştirmeyi öğreneceksiniz.

Bu makalede açıklanan çözüm, cihaz sağlama hizmeti 'ni kullanarak Azure dijital TWINS 'de IoT Hub cihazları **_sağlama_** ve **_devre dışı bırakma_** sürecini otomatikleştirmenize imkan tanır. 

_Sağlama_ ve _devre dışı bırakma_ aşamaları hakkında daha fazla bilgi Için ve tüm kurumsal IoT projelerinde ortak olan genel cihaz yönetim aşamaları kümesini daha iyi anlamak için, IoT Hub cihaz yönetimi belgelerinin [ *cihaz yaşam döngüsü* bölümüne](../iot-hub/iot-hub-device-management-overview.md#device-lifecycle) bakın.

## <a name="prerequisites"></a>Önkoşullar

Sağlamayı ayarlamadan önce aşağıdakileri ayarlamanız gerekir:
* bir **Azure dijital TWINS örneği**. Azure dijital TWINS örneği oluşturmak için [*nasıl yapılır: örnek ve kimlik doğrulama ayarlama*](how-to-set-up-instance-portal.md) bölümündeki yönergeleri izleyin. Örnek **_ana bilgisayar adını_** Azure Portal toplayın ([yönergeler](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values)).
* **IoT Hub 'ı**. Yönergeler için, bu [IoT Hub hızlı başlangıç](../iot-hub/quickstart-send-telemetry-cli.md)konusunun *IoT Hub oluşturma* bölümüne bakın.
* IoT Hub verilerine göre dijital ikizi bilgilerini güncelleştiren bir [**Azure işlevi**](../azure-functions/functions-overview.md) . Bu Azure işlevini oluşturmak için [*nasıl yapılır: IoT Hub verilerini*](how-to-ingest-iot-hub-data.md) alma bölümündeki yönergeleri izleyin. Bu makalede kullanmak için işlev **_adını_** toplayın.

Bu örnek ayrıca cihaz sağlama hizmetini kullanarak sağlamayı içeren bir **cihaz simülatörü** kullanır. Cihaz simülatörü şurada bulunur: [Azure dijital TWINS ve IoT Hub tümleştirme örneği](/samples/azure-samples/digital-twins-iothub-integration/adt-iothub-provision-sample/). Örnek bağlantısına gidip başlık altındaki **kodu görüntüle** düğmesini seçerek makinenizde örnek projeyi alın. Bu işlem sizi örnek için GitHub deposuna götürür ve bu, bir olarak indirebilirsiniz *. ZIP* dosyası **kod** düğmesini seçip zip ' i **indirin**. 

:::image type="content" source="media/how-to-provision-using-device-provisioning-service/download-repo-zip.png" alt-text="GitHub 'da dijital-TWINS-ıothub-Integration deposunun ekran görüntüsü. Kod düğmesi seçilidir ve posta Indirme düğmesinin vurgulandığı küçük bir iletişim kutusu oluşturulur." lightbox="media/how-to-provision-using-device-provisioning-service/download-repo-zip.png":::

İndirilen klasörü sıkıştırmayı açın.

Makinenizde yüklü [**Node.js**](https://nodejs.org/download) gerekir. Cihaz simülatörü **Node.js**, sürüm 10.0. x veya üzerini temel alır.

## <a name="solution-architecture"></a>Çözüm mimarisi

Aşağıdaki görüntüde, cihaz sağlama hizmeti ile Azure dijital TWINS kullanılarak bu çözümün mimarisi gösterilmektedir. Hem cihaz sağlama hem de devre dışı bırakma akışını gösterir.

:::image type="content" source="media/how-to-provision-using-device-provisioning-service/flows.png" alt-text="Uçtan uca bir senaryoda cihaz ve çeşitli Azure Hizmetleri diyagramı. Veri akışı, bir termostat cihaz ve DPS arasında geri ve ileri akar. Veriler aynı zamanda DPS 'den IoT Hub 'ye ve Azure dijital TWINS 'e ' ayırma ' etiketli bir Azure işlevi aracılığıyla akar. El ile ' cihaz silme ' eyleminden alınan veriler, IoT Hub > Event Hubs Azure Işlevleri > Azure dijital TWINS > aracılığıyla akar." lightbox="media/how-to-provision-using-device-provisioning-service/flows.png":::

Bu makale iki bölüme ayrılmıştır:
* [*Cihaz sağlama hizmeti 'ni kullanarak cihazı otomatik sağlama*](#auto-provision-device-using-device-provisioning-service)
* [*IoT Hub yaşam döngüsü olaylarını kullanarak cihazı otomatik olarak devre dışı bırakma*](#auto-retire-device-using-iot-hub-lifecycle-events)

Mimarideki her adımın daha derin açıklamaları için, makalenin ilerleyen bölümlerinde tek tek bölümlerine bakın.

## <a name="auto-provision-device-using-device-provisioning-service"></a>Cihaz sağlama hizmeti 'ni kullanarak cihazı otomatik sağlama

Bu bölümde, cihazları aşağıdaki yoldan otomatik sağlamak için Azure dijital TWINS 'e cihaz sağlama hizmeti iliştirirsiniz. Bu, [daha önce](#solution-architecture)gösterilen tam mimarinin bir alıntısıdır.

:::image type="content" source="media/how-to-provision-using-device-provisioning-service/provision.png" alt-text="Sağlama akışı diyagramı--akış bölümlerinin sayısı etiketleyen çözüm mimarisi diyagramının bir alıntısı. Veriler, bir termostat cihazı ve DPS (cihaz > DPS için 1 ve DPS > cihaz için 5) arasında ileri ve geri akar. Veriler aynı zamanda DPS 'den IoT Hub (4) ve Azure Digital TWINS 'e (3) ' ayırma ' (2) etiketli bir Azure işlevi aracılığıyla akar." lightbox="media/how-to-provision-using-device-provisioning-service/provision.png":::

İşlem akışının açıklaması aşağıda verilmiştir:
1. Cihaz, kimliğini kanıtlamak için bilgi tanımlamayı sağlayan DPS uç noktası ile iletişim kurar.
2. DPS kayıt listesine karşı kayıt KIMLIĞI ve anahtarı doğrulayarak cihaz kimliğini doğrular ve ayırmayı yapmak için bir [Azure işlevi](../azure-functions/functions-overview.md) çağırır.
3. Azure işlevi, cihaz için Azure dijital TWINS 'te yeni bir [ikizi](concepts-twins-graph.md) oluşturur. İkizi, cihazın **kayıt kimliğiyle** aynı ada sahip olacaktır.
4. DPS, cihazı bir IoT Hub 'ına kaydeder ve cihazın istenen ikizi durumunu doldurur.
5. IoT Hub 'ı cihaza cihaz KIMLIĞI bilgilerini ve IoT Hub bağlantı bilgilerini döndürür. Cihaz artık IoT Hub 'ına bağlanabilir.

Aşağıdaki bölümler, bu otomatik sağlama cihaz akışını ayarlama adımlarında size yol gösterir.

### <a name="create-a-device-provisioning-service"></a>Cihaz sağlama hizmeti oluşturma

Cihaz sağlama hizmeti kullanılarak yeni bir cihaz sağlandığında, bu cihaz için yeni bir ikizi, kayıt KIMLIĞIYLE aynı ada sahip Azure dijital TWINS 'de oluşturulabilir.

IoT cihazları sağlamak için kullanılacak bir cihaz sağlama hizmeti örneği oluşturun. Aşağıdaki Azure CLı yönergelerini kullanabilir veya Azure portal kullanabilirsiniz: [*hızlı başlangıç: IoT Hub cihaz sağlama hizmeti 'ni Azure Portal ayarlama*](../iot-dps/quick-setup-auto-provision.md).

Aşağıdaki Azure CLı komutu bir cihaz sağlama hizmeti oluşturacaktır. Bir cihaz sağlama hizmeti adı, kaynak grubu ve bölge belirtmeniz gerekir. Cihaz sağlama hizmetini destekleyen bölgeleri görmek için [*bölgeye göre kullanılabilir Azure ürünlerini*](https://azure.microsoft.com/global-infrastructure/services/?products=iot-hub)ziyaret edin.
Bu komut, [makinenizde yüklü](/cli/azure/install-azure-cli)Azure CLI 'niz varsa [Cloud Shell](https://shell.azure.com)veya yerel olarak çalıştırılabilir.

```azurecli-interactive
az iot dps create --name <Device Provisioning Service name> --resource-group <resource group name> --location <region>
```

### <a name="add-a-function-to-use-with-device-provisioning-service"></a>Cihaz sağlama hizmeti ile kullanmak için bir işlev ekleme

[Önkoşullar](#prerequisites) bölümünde oluşturduğunuz işlev uygulaması projenizin Içinde, cihaz sağlama hizmeti ile birlikte kullanmak için yeni bir işlev oluşturacaksınız. Bu işlev, cihaz sağlama hizmeti tarafından, yeni bir cihaz sağlamak için [özel bir ayırma ilkesinde](../iot-dps/how-to-use-custom-allocation-policies.md) kullanılacaktır.

' İ makinenizde Visual Studio 'da uygulama projesini açarak başlatın ve aşağıdaki adımları izleyin.

#### <a name="step-1-add-a-new-function"></a>1. Adım: yeni bir işlev ekleme 

Visual Studio 'daki işlev uygulaması projesine *http-Trigger* türünde yeni bir işlev ekleyin.

:::image type="content" source="media/how-to-provision-using-device-provisioning-service/add-http-trigger-function-visual-studio.png" alt-text="İşlev uygulaması projenizde http tetikleyicisi türünde Azure işlevi eklemek için Visual Studio görünümünün ekran görüntüsü." lightbox="media/how-to-provision-using-device-provisioning-service/add-http-trigger-function-visual-studio.png":::

#### <a name="step-2-fill-in-function-code"></a>2. Adım: işlev kodunu doldur

Projeye yeni bir NuGet paketi ekleyin: [Microsoft. Azure. Devices. sağlama. Service](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/). Projenizde kullanılan paketler projenin bir parçası değilse, projenize de daha fazla paket eklemeniz gerekebilir.

Yeni oluşturulan işlev kodu dosyasında aşağıdaki kodu yapıştırın, işlevi *Dpsadtallocationfunc. cs* olarak yeniden adlandırın ve dosyayı kaydedin.

:::code language="csharp" source="~/digital-twins-docs-samples-dps/functions/DpsAdtAllocationFunc.cs":::

#### <a name="step-3-publish-the-function-app-to-azure"></a>3. Adım: işlev uygulamasını Azure 'da yayımlama

Azure 'daki işlev uygulamasına *Dpsadtallocationfunc. cs* işleviyle birlikte projeyi yayımlayın.

[!INCLUDE [digital-twins-publish-and-configure-function-app.md](../../includes/digital-twins-publish-and-configure-function-app.md)]

### <a name="create-device-provisioning-enrollment"></a>Cihaz sağlama kaydı oluşturma

Daha sonra, **özel bir ayırma işlevi** kullanarak cihaz sağlama hizmeti 'nde bir kayıt oluşturmanız gerekir. Cihaz sağlama hizmeti belgelerindeki özel ayırma ilkeleri makalesinin [*kayıt oluştur*](../iot-dps/how-to-use-custom-allocation-policies.md#create-the-enrollment) bölümünde bunu yapmak için yönergeleri izleyin.

Bu akıştan ilerleyecekseniz, kaydı az önce oluşturduğunuz işleve bağlamak için aşağıdaki seçenekleri seçtiğinizden emin olun.

* **Cihazlara cihazları nasıl atamak Istediğinizi seçin**: özel (Azure işlevini kullanın).
* **Bu grubun atanabileceği IoT Hub 'Larını seçin:** IoT Hub 'ınızın adını seçin veya *Yeni bir IoT Hub 'ı bağla* düğmesini seçin ve açılan listeden IoT Hub 'ınızı seçin.

Sonra, işlev uygulamanızı kayıt grubuna bağlamak için *Yeni bir Işlev Seç* düğmesini seçin. Ardından, aşağıdaki değerleri girin:

* **Abonelik**: Azure aboneliğiniz otomatik olarak doldurulur. Doğru abonelik olduğundan emin olun.
* **İşlev uygulaması**: işlev uygulamanızın adını seçin.
* **İşlev**: DpsAdtAllocationFunc öğesini seçin.

Ayrıntılarınızı kaydedin.                  

:::image type="content" source="media/how-to-provision-using-device-provisioning-service/link-enrollment-group-to-iot-hub-and-function-app.png" alt-text="Özel ' i (Azure Işlevi kullan) seçmek için gümrük kayıt grubu ayrıntıları penceresinin ekran görüntüsü ve bölümlerdeki IoT Hub 'ınızın adı, cihazları hub 'lara nasıl atamak istediğinizi seçin ve bu grubun atanabileceği IoT Hub 'larını seçin. Ayrıca, açılır listeden aboneliğiniz, işlev uygulaması ' nı seçin ve DpsAdtAllocationFunc ' yi seçtiğinizden emin olun." lightbox="media/how-to-provision-using-device-provisioning-service/link-enrollment-group-to-iot-hub-and-function-app.png":::

Kayıt oluşturulduktan sonra, bu makalenin cihaz simülatörünü yapılandırmak için kayıt için **birincil anahtar** daha sonra kullanılacaktır.

### <a name="set-up-the-device-simulator"></a>Cihaz simülatörünü ayarlama

Bu örnek, cihaz sağlama hizmeti kullanılarak sağlamayı içeren bir cihaz simülatörü kullanır. Cihaz simülatörü, [Önkoşullar](#prerequisites) bölümünde Indirdiğiniz [Azure dijital twıns ve IoT Hub tümleştirme örneğinde](/samples/azure-samples/digital-twins-iothub-integration/adt-iothub-provision-sample/) bulunur.

#### <a name="upload-the-model"></a>Modeli karşıya yükleyin

Cihaz Benzetici, şu KIMLIĞE sahip modeli kullanan bir termostat-Type aygıtıdır: `dtmi:contosocom:DigitalTwins:Thermostat;1` . Bu tür bir cihaz için bir ikizi oluşturabilmeniz için bu modeli Azure Digital TWINS 'e yüklemeniz gerekir.

[!INCLUDE [digital-twins-thermostat-model-upload.md](../../includes/digital-twins-thermostat-model-upload.md)]

Modeller hakkında daha fazla bilgi için bkz. [*nasıl yapılır: modelleri yönetme*](how-to-manage-model.md#upload-models).

#### <a name="configure-and-run-the-simulator"></a>Simülatörü yapılandırma ve çalıştırma

Komut pencerenizde, indirilen örnek *Azure dijital TWINS* ' e gidin ve daha önce sıkıştırmadan önce ve ardından *cihaz simülatör* dizinine geri dönerek IoT Hub tümleştirme yapın. Ardından, aşağıdaki komutu kullanarak projenin bağımlılıklarını yüklersiniz:

```cmd
npm install
```

Ardından, cihaz simülatör dizininizde. env. Template dosyasını. env adlı yeni bir dosyaya kopyalayın ve ayarları doldurmanız için aşağıdaki değerleri toplayın:

* PROVISIONING_IDSCOPE: Bu değeri almak Için, [Azure Portal](https://portal.azure.com/)cihaz sağlama hizmetine gidin, ardından menü seçeneklerinde *genel bakış* ' ı seçin ve alan *kimliği kapsamını* bulun.

    :::image type="content" source="media/how-to-provision-using-device-provisioning-service/id-scope.png" alt-text="KIMLIK kapsamı değerini kopyalamak için cihaz sağlama genel bakış sayfasının Azure portal görünümünün ekran görüntüsü." lightbox="media/how-to-provision-using-device-provisioning-service/id-scope.png":::

* PROVISIONING_REGISTRATION_ID: cihazınız için bir kayıt KIMLIĞI seçebilirsiniz.
* ADT_MODEL_ID: `dtmi:contosocom:DigitalTwins:Thermostat;1`
* PROVISIONING_SYMMETRIC_KEY: Bu, daha önce ayarladığınız kayıt için birincil anahtardır. Bu değeri yeniden almak için Azure portal cihaz sağlama hizmetine gidin, kayıtları *Yönet*' i seçin, ardından daha önce oluşturduğunuz kayıt grubunu seçin ve *birincil anahtarı* kopyalayın.

    :::image type="content" source="media/how-to-provision-using-device-provisioning-service/sas-primary-key.png" alt-text="Cihaz sağlama hizmeti 'nin Azure portal görünümünün ekran görüntüsü SAS birincil anahtar değerini kopyalamak için kayıt listesi sayfasını yönetir." lightbox="media/how-to-provision-using-device-provisioning-service/sas-primary-key.png":::

Şimdi,. env dosya ayarlarını güncelleştirmek için yukarıdaki değerleri kullanın.

```cmd
PROVISIONING_HOST = "global.azure-devices-provisioning.net"
PROVISIONING_IDSCOPE = "<Device Provisioning Service Scope ID>"
PROVISIONING_REGISTRATION_ID = "<Device Registration ID>"
ADT_MODEL_ID = "dtmi:contosocom:DigitalTwins:Thermostat;1"
PROVISIONING_SYMMETRIC_KEY = "<Device Provisioning Service enrollment primary SAS key>"
```

Dosyayı kaydedin ve kapatın.

### <a name="start-running-the-device-simulator"></a>Cihaz simülatörünü çalıştırmaya başla

Hala, komut pencerenizde *cihaz simülatör* dizininde, aşağıdaki komutu kullanarak cihaz simülatörünü başlatın:

```cmd
node .\adt_custom_register.js
```

Kayıtlı ve IoT Hub bağlı olduğunu ve sonra ileti gönderilmeye başladığınızı görmeniz gerekir.
:::image type="content" source="media/how-to-provision-using-device-provisioning-service/output.png" alt-text="Cihaz kaydını ve iletileri göndermeyi gösteren Komut penceresi ekran görüntüsü" lightbox="media/how-to-provision-using-device-provisioning-service/output.png":::

### <a name="validate"></a>Doğrulama

Bu makalede ayarladığınız akışın bir sonucu olarak, cihaz Azure dijital TWINS 'e otomatik olarak kaydedilir. Oluşturduğunuz Azure dijital TWINS örneğindeki cihazın ikizi bulmak için aşağıdaki [Azure Digital TWINS CLI](how-to-use-cli.md) komutunu kullanın.

```azurecli-interactive
az dt twin show -n <Digital Twins instance name> --twin-id "<Device Registration ID>"
```

Azure dijital TWINS örneğinde bulunan cihazın ikizi görmeniz gerekir.
:::image type="content" source="media/how-to-provision-using-device-provisioning-service/show-provisioned-twin.png" alt-text="Yeni oluşturulan ikizi gösteren Komut penceresi ekran görüntüsü." lightbox="media/how-to-provision-using-device-provisioning-service/show-provisioned-twin.png":::

## <a name="auto-retire-device-using-iot-hub-lifecycle-events"></a>IoT Hub yaşam döngüsü olaylarını kullanarak cihazı otomatik olarak devre dışı bırakma

Bu bölümde, aşağıdaki yoldan cihazları otomatik olarak devre dışı bırakmak için Azure dijital TWINS 'e IoT Hub yaşam döngüsü olayları iliştirirsiniz. Bu, [daha önce](#solution-architecture)gösterilen tam mimarinin bir alıntısıdır.

:::image type="content" source="media/how-to-provision-using-device-provisioning-service/retire.png" alt-text="Cihazın devre dışı bırakılması akışının diyagramı--bir çözüm mimarisi diyagramı, akışın sayı etiketleyen bölümü. Termostat cihazı, diyagramdaki Azure hizmetleriyle bağlantı olmadan gösterilir. El ile ' cihaz silme ' eyleminden alınan veriler IoT Hub (1) > Event Hubs (2) > Azure Işlevleri > Azure dijital TWINS (3)." lightbox="media/how-to-provision-using-device-provisioning-service/retire.png":::

İşlem akışının açıklaması aşağıda verilmiştir:
1. Bir dış veya el ile işlem, IoT Hub bir cihazın silinmesini tetikler.
2. IoT Hub cihazı siler ve bir [Olay Hub 'ına](../event-hubs/event-hubs-about.md)yönlendirilecek bir [cihaz yaşam döngüsü](../iot-hub/iot-hub-device-management-overview.md#device-lifecycle) olayı oluşturur.
3. Azure işlevi, Azure dijital TWINS 'de cihazın ikizi siler.

Aşağıdaki bölümlerde, bu otomatik devre dışı bırakma cihaz akışını ayarlama adımları gösterilmektedir.

### <a name="create-an-event-hub"></a>Olay hub’ı oluşturma

Ardından, IoT Hub yaşam döngüsü olaylarını almak için bir Azure [Olay Hub 'ı](../event-hubs/event-hubs-about.md) oluşturacaksınız. 

[*Olay Hub 'ı oluşturma*](../event-hubs/event-hubs-create.md) hızlı başlangıç bölümünde açıklanan adımları izleyin. Olay Hub 'ınızı *lifecycleevents* olarak adlandırın. Sonraki bölümlerde IoT Hub Route 'u ve bir Azure işlevini ayarlarken bu olay hub 'ı adını kullanacaksınız.

Aşağıdaki ekran görüntüsünde, Olay Hub 'ının oluşturulması gösterilmektedir.
:::image type="content" source="media/how-to-provision-using-device-provisioning-service/create-event-hub-lifecycle-events.png" alt-text="Lifecycleevents adlı bir olay hub 'ı oluşturmak için Azure portal penceresinin ekran görüntüsü." lightbox="media/how-to-provision-using-device-provisioning-service/create-event-hub-lifecycle-events.png":::

#### <a name="create-sas-policy-for-your-event-hub"></a>Olay Hub 'ınız için SAS ilkesi oluşturma

Daha sonra, Olay Hub 'ını işlev uygulamanızla yapılandırmak için [paylaşılan erişim imzası (SAS) ilkesi](../event-hubs/authorize-access-shared-access-signature.md) oluşturmanız gerekir.
Bunu yapmak için
1. Azure portal yeni oluşturduğunuz Olay Hub 'ına gidin ve soldaki menü seçeneklerinde **paylaşılan erişim ilkeleri** ' ni seçin.
2. **Add (Ekle)** seçeneğini belirleyin. Açılan *SAS Ilkesi Ekle* penceresinde, tercih ettiğiniz bir ilke adı girin ve *dinle* onay kutusunu seçin.
3. **Oluştur**’u seçin.
    
:::image type="content" source="media/how-to-provision-using-device-provisioning-service/add-event-hub-sas-policy.png" alt-text="Bir olay hub 'ı SAS ilkesi eklemek Azure portal ekran görüntüsü." lightbox="media/how-to-provision-using-device-provisioning-service/add-event-hub-sas-policy.png":::

#### <a name="configure-event-hub-with-function-app"></a>İşlev uygulamasıyla Olay Hub 'ını yapılandırma

Ardından, [Önkoşullar](#prerequisites) bölümünde ayarladığınız Azure işlev uygulamasını yeni Olay Hub 'ınızla çalışacak şekilde yapılandırın. Bunu, Olay Hub 'ının bağlantı dizesiyle işlev uygulaması içinde bir ortam değişkeni ayarlayarak gerçekleştirirsiniz.

1. Yeni oluşturduğunuz ilkeyi açın ve **bağlantı dizesi-birincil anahtar** değerini kopyalayın.

    :::image type="content" source="media/how-to-provision-using-device-provisioning-service/event-hub-sas-policy-connection-string.png" alt-text="Bağlantı dizesini kopyalamak için Azure portal ekran görüntüsü-birincil anahtar." lightbox="media/how-to-provision-using-device-provisioning-service/event-hub-sas-policy-connection-string.png":::

2. Bağlantı dizesini, aşağıdaki Azure CLı komutuyla işlev uygulama ayarlarına bir değişken olarak ekleyin. Bu komut, [makinenizde yüklü](/cli/azure/install-azure-cli)Azure CLI 'niz varsa [Cloud Shell](https://shell.azure.com)veya yerel olarak çalıştırılabilir.

    ```azurecli-interactive
    az functionapp config appsettings set --settings "EVENTHUB_CONNECTIONSTRING=<Event Hubs SAS connection string Listen>" -g <resource group> -n <your App Service (function app) name>
    ```

### <a name="add-a-function-to-retire-with-iot-hub-lifecycle-events"></a>IoT Hub yaşam döngüsü olaylarıyla devre dışı bırakmak için bir işlev ekleme

[Önkoşullar](#prerequisites) bölümünde oluşturduğunuz işlev uygulaması projenizin içinde, IoT Hub yaşam döngüsü olaylarını kullanarak var olan bir cihazı devre dışı bırakmak için yeni bir işlev oluşturacaksınız.

Yaşam döngüsü olayları hakkında daha fazla bilgi için bkz. [*telemetri dışı olayları IoT Hub*](../iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events). Azure işlevleri ile Event Hubs kullanma hakkında daha fazla bilgi için bkz. Azure [*için azure Event Hubs tetikleyicisi işlevleri*](../azure-functions/functions-bindings-event-hubs-trigger.md).

' İ makinenizde Visual Studio 'da uygulama projesini açarak başlatın ve aşağıdaki adımları izleyin.

#### <a name="step-1-add-a-new-function"></a>1. Adım: yeni bir işlev ekleme
     
Visual Studio 'daki işlev uygulaması projesine *Event hub tetikleyicisi* türünde yeni bir işlev ekleyin.

:::image type="content" source="media/how-to-provision-using-device-provisioning-service/create-event-hub-trigger-function.png" alt-text="İşlev uygulaması projenizde Olay Hub 'ı tetikleyicisi türünde bir Azure işlevi eklemek için Visual Studio penceresinin ekran görüntüsü." lightbox="media/how-to-provision-using-device-provisioning-service/create-event-hub-trigger-function.png":::

#### <a name="step-2-fill-in-function-code"></a>2. Adım: işlev kodunu doldur

Yeni oluşturulan işlev kodu dosyasında, aşağıdaki kodu yapıştırın, işlevi olarak yeniden adlandırın `DeleteDeviceInTwinFunc.cs` ve dosyayı kaydedin.

:::code language="csharp" source="~/digital-twins-docs-samples-dps/functions/DeleteDeviceInTwinFunc.cs":::

#### <a name="step-3-publish-the-function-app-to-azure"></a>3. Adım: işlev uygulamasını Azure 'da yayımlama

Projeyi *Deletedeviceintwinfunc. cs* işleviyle Azure 'daki işlev uygulamasına yayımlayın.

[!INCLUDE [digital-twins-publish-and-configure-function-app.md](../../includes/digital-twins-publish-and-configure-function-app.md)]

### <a name="create-an-iot-hub-route-for-lifecycle-events"></a>Yaşam döngüsü olayları için IoT Hub yolu oluşturma

Artık cihaz yaşam döngüsü olaylarını yönlendirmek için bir IoT Hub yolu ayarlayacaksınız. Bu durumda, tarafından tanımlanan cihaz silme olaylarını özellikle dinleyecaksınız `if (opType == "deleteDeviceIdentity")` . Bu, bir cihazın ve dijital ikizi 'in kullanımdan kaldırılması sonuçlandırılıyor ve dijital ikizi öğesinin silinmesini tetikler.

İlk olarak, IoT Hub 'ınızda bir olay hub 'ı uç noktası oluşturmanız gerekir. Daha sonra, bu olay hub 'ı uç noktasına yaşam döngüsü olayları göndermek için IoT Hub 'a bir yol ekleyeceksiniz.
Bir olay hub 'ı uç noktası oluşturmak için aşağıdaki adımları izleyin:

1. [Azure Portal](https://portal.azure.com/), [Önkoşullar](#prerequisites) bölümünde oluşturduğunuz IoT Hub 'ına gidin ve sol taraftaki menü seçeneklerinde **ileti yönlendirmesi** ' ni seçin.
2. **Özel uç noktalar** sekmesini seçin.
3. **+ Ekle** ' yi seçin ve olay **hub** 'larını seçin bir olay hub 'ı türü uç noktası ekleyin.

    :::image type="content" source="media/how-to-provision-using-device-provisioning-service/event-hub-custom-endpoint.png" alt-text="Bir olay hub 'ı özel uç noktası eklemek için Visual Studio penceresinin ekran görüntüsü." lightbox="media/how-to-provision-using-device-provisioning-service/event-hub-custom-endpoint.png":::

4. Pencerede açılan *bir olay hub 'ı ekleme noktası ekleyin* , aşağıdaki değerleri seçin:
    * **Uç nokta adı**: bir uç nokta adı seçin.
    * **Olay Hub 'ı ad alanı**: açılan listeden olay hub 'ı ad alanınızı seçin.
    * **Olay Hub 'ı örneği**: önceki adımda oluşturduğunuz Olay Hub 'ı adını seçin.
5. **Oluştur**’u seçin. Sonraki adımda bir yol eklemek için bu pencereyi açık tutun.

    :::image type="content" source="media/how-to-provision-using-device-provisioning-service/add-event-hub-endpoint.png" alt-text="Bir olay hub 'ı uç noktası eklemek için Visual Studio penceresinin ekran görüntüsü." lightbox="media/how-to-provision-using-device-provisioning-service/add-event-hub-endpoint.png":::

Daha sonra, yukarıdaki adımda oluşturduğunuz bitiş noktasına, silme olaylarını gönderen bir yönlendirme sorgusuyla bağlanan bir yol ekleyeceksiniz. Yol oluşturmak için aşağıdaki adımları izleyin:

1. *Yollar* sekmesine gidin ve bir rota eklemek için **Ekle** ' yi seçin.

    :::image type="content" source="media/how-to-provision-using-device-provisioning-service/add-message-route.png" alt-text="Olayları göndermek için bir yol eklemek üzere Visual Studio penceresinin ekran görüntüsü." lightbox="media/how-to-provision-using-device-provisioning-service/add-message-route.png":::

2. Açılan *yol Ekle* sayfasında, aşağıdaki değerleri seçin:

   * **Ad**: yönlendirmenize bir ad seçin. 
   * **Uç noktası**: açılan listeden daha önce oluşturduğunuz Olay Hub 'larını seçin.
   * **Veri kaynağı**: *cihaz yaşam döngüsü olaylarını* seçin.
   * **Yönlendirme sorgusu**: girin `opType='deleteDeviceIdentity'` . Bu sorgu cihaz yaşam döngüsü olaylarını yalnızca silme olaylarını gönderecek şekilde sınırlandırır.

3. **Kaydet**’i seçin.

    :::image type="content" source="media/how-to-provision-using-device-provisioning-service/lifecycle-route.png" alt-text="Yaşam döngüsü olaylarını göndermek için bir yol eklemek üzere Azure portal penceresinin ekran görüntüsü." lightbox="media/how-to-provision-using-device-provisioning-service/lifecycle-route.png":::

Bu akıştan doldurduktan sonra, her şey cihazları devre dışı bırakmak için uçtan uca ayarlanır.

### <a name="validate"></a>Doğrulama

Kullanımdan kaldırma işleminin tetiklenmesi için cihazı IoT Hub el ile silmeniz gerekir.

Bunu bir [Azure CLI komutuyla](/cli/azure/iot/hub/module-identity#az_iot_hub_module_identity_delete) veya Azure Portal yapabilirsiniz. Azure portal cihazı silmek için aşağıdaki adımları izleyin:

1. IoT Hub 'ınıza gidin ve sol taraftaki menü seçeneklerinde **IoT cihazları** ' nı seçin. 
2. [Bu makalenin ilk yarısında](#auto-provision-device-using-device-provisioning-service)seçtiğiniz CIHAZ kayıt kimliğine sahip bir cihaz görürsünüz. Alternatif olarak, Azure dijital TWINS 'te bir ikizi olduğu sürece silinecek başka bir cihaz seçebilirsiniz. böylece, cihaz silindikten sonra ikizi 'in otomatik olarak silindiğini doğrulayabilirsiniz.
3. Cihazı seçin ve **Sil**' i seçin.

:::image type="content" source="media/how-to-provision-using-device-provisioning-service/delete-device-twin.png" alt-text="IoT cihazlarından cihaz ikizi silmek için Azure portal ekran görüntüsü." lightbox="media/how-to-provision-using-device-provisioning-service/delete-device-twin.png":::

Azure dijital TWINS 'te değişikliklerin gösterilmesi birkaç dakika sürebilir.

Azure Digital TWINS örneğindeki cihazın ikizi silindiğini doğrulamak için aşağıdaki [Azure Digital TWINS CLI](how-to-use-cli.md) komutunu kullanın.

```azurecli-interactive
az dt twin show -n <Digital Twins instance name> --twin-id "<Device Registration ID>"
```

Cihazın ikizi artık Azure dijital TWINS örneğinde bulunamadığını görmeniz gerekir.

:::image type="content" source="media/how-to-provision-using-device-provisioning-service/show-retired-twin.png" alt-text="İkizi gösteren Komut penceresi ekran görüntüsü." lightbox="media/how-to-provision-using-device-provisioning-service/show-retired-twin.png":::

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu makalede oluşturulan kaynaklara artık ihtiyacınız yoksa, bunları silmek için aşağıdaki adımları izleyin.

Azure Cloud Shell veya yerel Azure CLı kullanarak, [az Group Delete](/cli/azure/group#az_group_delete) komutuyla bir kaynak grubundaki tüm Azure kaynaklarını silebilirsiniz. Bu, kaynak grubunu kaldırır; Azure dijital TWINS örneği; IoT Hub ve Hub cihaz kaydı; olay Kılavuzu konusu ve ilişkili abonelikler; depolama gibi ilişkili kaynaklar da dahil olmak üzere, Olay Hub 'ları ve her ikisi de Azure Işlevleri uygulaması.

> [!IMPORTANT]
> Silinen kaynak grupları geri alınamaz. Kaynak grubu ve içindeki tüm kaynaklar kalıcı olarak silinir. Yanlış kaynak grubunu veya kaynakları yanlışlıkla silmediğinizden emin olun. 

```azurecli-interactive
az group delete --name <your-resource-group>
```

Ardından, yerel makinenizden indirdiğiniz proje örnek klasörünü silin.

## <a name="next-steps"></a>Sonraki adımlar

Cihazlar için oluşturulan dijital TWINS, Azure dijital TWINS 'de düz bir hiyerarşi olarak depolanır, ancak model bilgileri ve kuruluş için çok düzeyli bir hiyerarşiyle zenginleştirilebilir. Bu kavram hakkında daha fazla bilgi edinmek için şunu okuyun:

* [*Kavramlar: dijital TWINS ve ikizi grafiği*](concepts-twins-graph.md)

Azure işlevleri ile HTTP istekleri kullanma hakkında daha fazla bilgi için bkz.:

* [*Azure Işlevleri için Azure http istek tetikleyicisi*](../azure-functions/functions-bindings-http-webhook-trigger.md)

Azure dijital TWINS 'te zaten depolanmış olan model ve grafik verilerini kullanarak bu bilgileri otomatik olarak sağlamak için özel mantık yazabilirsiniz. TWINS grafiğindeki bilgileri yönetme, yükseltme ve alma hakkında daha fazla bilgi edinmek için aşağıdakilere bakın:

* [*Nasıl yapılır: dijital ikizi yönetme*](how-to-manage-twin.md)
* [*Nasıl yapılır: ikizi grafiğini sorgulama*](how-to-query-graph.md)