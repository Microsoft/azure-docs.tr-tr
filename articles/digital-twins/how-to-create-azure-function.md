---
title: Azure 'da verileri işlemek için bir işlev ayarlama
titleSuffix: Azure Digital Twins
description: Azure 'da dijital TWINS 'e erişebilen ve tetiklenebilecek bir işlev oluşturma bölümüne bakın.
author: baanders
ms.author: baanders
ms.date: 8/27/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 5dddaabf47a261f741b3b1cb8d3319d589c4e474
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107480765"
---
# <a name="connect-function-apps-in-azure-for-processing-data"></a>Verileri işlemek için Azure 'da işlev uygulamalarına bağlanma

Dijital TWINS, işlem kaynakları üzerinden [olay yolları](concepts-route-events.md) kullanılarak verilere göre güncelleştirilir. Örneğin, [Azure işlevleri](../azure-functions/functions-overview.md) kullanılarak yapılan bir işlev, yanıt olarak bir dijital ikizi güncelleştirebilir:
* Azure IoT Hub cihaz telemetri verileri.
* İkizi grafiğinin içindeki başka bir dijital ikizi bir özellik değişikliği veya diğer veriler.

Bu makalede, Azure 'da Azure dijital TWINS ile kullanım için nasıl bir işlev oluşturacağınız gösterilmektedir. Bir işlev oluşturmak için aşağıdaki temel adımları izlemelisiniz:

1. Visual Studio 'da bir Azure Işlevleri projesi oluşturun.
2. [Azure Event Grid](../event-grid/overview.md) tetikleyicisi olan bir işlev yazın.
3. Azure dijital TWINS 'e erişebilmeniz için işleve kimlik doğrulama kodu ekleyin.
4. İşlev uygulamasını Azure 'da yayımlayın.
5. İşlev uygulaması için [güvenliği](concepts-security.md) ayarlayın.

## <a name="prerequisite-set-up-azure-digital-twins"></a>Önkoşul: Azure dijital TWINS ayarlama

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

## <a name="create-a-function-app-in-visual-studio"></a>Visual Studio 'da işlev uygulaması oluşturma

Visual Studio 2019 ' de **Dosya**  >  **Yeni**  >  **Proje**' yi seçin. **Azure işlevleri** şablonunu arayın. **İleri**’yi seçin.

:::image type="content" source="media/how-to-create-azure-function/create-azure-function-project.png" alt-text="Yeni proje iletişim kutusunu gösteren Visual Studio ekran görüntüsü. Azure Işlevleri proje şablonu vurgulanır.":::

İşlev uygulaması için bir ad belirtip __Oluştur__' u seçin.

:::image type="content" source="media/how-to-create-azure-function/configure-new-project.png" alt-text="Visual Studio 'nun yeni bir proje yapılandırma iletişim kutusunu gösteren ekran görüntüsü. Ayarlar proje adı, konum Kaydet, yeni bir çözüm oluşturmak için seçim ve çözüm adı içerir.":::

**Event Grid tetikleyicisi** işlevi uygulama türünü seçin ve ardından __Oluştur__' u seçin.

:::image type="content" source="media/how-to-create-azure-function/event-grid-trigger-function.png" alt-text="Yeni bir Azure Işlevleri uygulaması oluşturmak için iletişim kutusunu gösteren Visual Studio ekran görüntüsü. Event Grid tetikleyici seçeneği vurgulanır.":::

İşlev uygulamanız oluşturulduktan sonra, Visual Studio proje klasörünüzdeki bir *işlev1. cs* dosyasında bir kod örneği oluşturur. Bu kısa işlev olayları günlüğe kaydetmek için kullanılır.

:::image type="content" source="media/how-to-create-azure-function/visual-studio-sample-code.png" alt-text="Visual Studio 'nun ekran görüntüsü. Yeni proje için Proje penceresi gösterilir. Örnek işlevin kodu, Işlev1 adlı bir dosyada gösterilmiştir." lightbox="media/how-to-create-azure-function/visual-studio-sample-code.png":::

## <a name="write-a-function-that-has-an-event-grid-trigger"></a>Event Grid tetikleyicisi olan bir işlev yazma

İşlev uygulamanıza bir SDK ekleyerek bir işlev yazabilirsiniz. İşlev uygulaması, [.net Için Azure dijital TWINS SDK 'sını (C#)](/dotnet/api/overview/azure/digitaltwins/client)kullanarak Azure Digital TWINS ile etkileşime girer. 

SDK 'yı kullanmak için, aşağıdaki paketleri projenize eklemeniz gerekir. Visual Studio NuGet Paket Yöneticisi 'ni kullanarak paketleri yükler. Veya `dotnet` bir komut satırı aracında kullanarak paketleri ekleyin.

* [Azure. DigitalTwins. Core](https://www.nuget.org/packages/Azure.DigitalTwins.Core/)
* [Azure. Identity](https://www.nuget.org/packages/Azure.Identity/)
* [System .net. http](https://www.nuget.org/packages/System.Net.Http/)
* [Azure. Core](https://www.nuget.org/packages/Azure.Core/)

Ardından, Visual Studio Çözüm Gezgini ' de, örnek kodunuzu içeren _işlev1. cs_ dosyasını açın. `using`Paketler için aşağıdaki deyimlerini ekleyin.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="Function_dependencies":::

## <a name="add-authentication-code-to-the-function"></a>İşleve kimlik doğrulama kodu ekleyin

Şimdi sınıf düzeyi değişkenler bildirin ve işlevin Azure dijital TWINS 'e erişmesine izin verecek kimlik doğrulama kodu ekleyin. _İşlev1. cs_ dosyasındaki işlevinizdeki değişkenleri ve kodu ekleyin.

* **Bir ortam değişkeni olarak Azure Digital TWINS hizmet URL 'sini okumak için kod.** Hizmet URL 'sini, işlevinde sabit kodlamak yerine bir ortam değişkeninden okumak iyi bir uygulamadır. Bu ortam değişkeninin değerini [daha sonra bu makalede](#set-up-security-access-for-the-function-app)ayarlayacaksınız. Ortam değişkenleri hakkında daha fazla bilgi için bkz. [işlev uygulamanızı yönetme](../azure-functions/functions-how-to-use-azure-function-app-settings.md?tabs=portal).

    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="ADT_service_URL":::

* **HttpClient örneğini tutacak statik bir değişken.** HttpClient, oluşturmak için görece pahalıdır, bu nedenle her işlev çağrısı için oluşturmamak istiyoruz.

    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="HTTP_client":::

* **Yönetilen kimlik kimlik bilgileri.**
    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="ManagedIdentityCredential":::

* **Yerel değişken _DigitalTwinsClient_.** Azure Digital TWINS istemci örneğinizi tutmak için işlevinizin içindeki değişkeni ekleyin. Bu değişkeni sınıfınız içinde *statik yapmayın.*
    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="DigitalTwinsClient":::

* **_AdtInstanceUrl_ için null denetimi.** Null denetimini ekleyin ve ardından özel durumları yakalamak için try/catch bloğunda işlev mantığınızı sarın.

Bu değişikliklerden sonra, işlev kodunuz aşağıdaki örneğe benzer şekilde görünür.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs":::

Uygulamanız yazıldığına göre Azure 'da yayımlayabilirsiniz.

## <a name="publish-the-function-app-to-azure"></a>İşlev uygulamasını Azure'da yayımlama

[!INCLUDE [digital-twins-publish-azure-function.md](../../includes/digital-twins-publish-azure-function.md)]

### <a name="verify-the-publication-of-your-function"></a>İşlevinizin yayımlanmasını doğrulama

1. [Azure Portal](https://portal.azure.com/)kimlik bilgilerinizi kullanarak oturum açın.
2. Pencerenin üst kısmındaki arama kutusunda, işlev uygulamanızın adını arayın ve ardından seçin.

    :::image type="content" source="media/how-to-create-azure-function/search-function-app.png" alt-text="Azure portal gösteren ekran görüntüsü. Arama alanına, uygulamanın adını girin." lightbox="media/how-to-create-azure-function/search-function-app.png":::

3. Açılan **işlev uygulaması** sayfasında, sol taraftaki menüde **işlevler**' i seçin. İşleviniz başarıyla yayımlanıyorsa, adı listede görüntülenir.

    > [!Note] 
    > Birkaç dakika beklemeniz ya da işleviniz yayımlanmış işlevler listesinde görüntülenmeden önce sayfayı birkaç kez yenilemeniz gerekebilir.

    :::image type="content" source="media/how-to-create-azure-function/view-published-functions.png" alt-text="Azure portal yayınlanan işlevleri görüntüleyin." lightbox="media/how-to-create-azure-function/view-published-functions.png":::

Azure dijital TWINS 'e erişmek için, işlev uygulamanızın Azure dijital TWINS örneğinize erişme izinlerine sahip sistem tarafından yönetilen bir kimlik olması gerekir. Bunu daha sonra ayarlayacaksınız.

## <a name="set-up-security-access-for-the-function-app"></a>İşlev uygulaması için güvenlik erişimini ayarlama

Azure CLı veya Azure portal kullanarak işlev uygulaması için güvenlik erişimi ayarlayabilirsiniz. Tercih ettiğiniz seçeneğe yönelik adımları izleyin.

# <a name="cli"></a>[CLI](#tab/cli)

Bu komutları [Azure Cloud Shell](https://shell.azure.com) veya [yerel bir Azure CLI yüklemesinde](/cli/azure/install-azure-cli)çalıştırın.
Azure dijital TWINS örneğiniz için **Azure dijital TWINS veri sahibi** rolünü sağlamak üzere, uygulamanın sistem tarafından yönetilen kimliğini kullanabilirsiniz. Rol, veri düzlemi etkinliklerini gerçekleştirmek için örnekte işlev uygulaması iznini verir. Ardından, bir ortam değişkeni ayarlayarak örneğin URL 'sini işlevinizin erişimine açık hale getirin.

### <a name="assign-an-access-role"></a>Erişim rolü atama

[!INCLUDE [digital-twins-permissions-required.md](../../includes/digital-twins-permissions-required.md)]

Önceki örneklerde bulunan işlev iskelet 'i, kendisine bir taşıyıcı belirtecinin geçirilmesini gerektirir. Taşıyıcı belirteci geçirilmemişse, işlev uygulaması Azure dijital TWINS ile kimlik doğrulaması yapamaz. 

Taşıyıcı belirtecinin geçirildiğinden emin olmak için, işlev uygulamasının Azure dijital TWINS 'e erişebilmesi için [Yönetilen kimlikler](../active-directory/managed-identities-azure-resources/overview.md) izinleri ayarlayın. Bu izinleri her bir işlev uygulaması için yalnızca bir kez ayarlarsınız.


1. İşlev için sistem tarafından yönetilen kimliğin ayrıntılarını görmek için aşağıdaki komutu kullanın. Çıktıda alanı bir yere göz atın `principalId` .

    ```azurecli-interactive 
    az functionapp identity show -g <your-resource-group> -n <your-App-Service-(function-app)-name> 
    ```

    >[!NOTE]
    > Sonuç, kimlik ayrıntılarını göstermek yerine boşsa, bu komutu kullanarak işlev için yeni bir sistem tarafından yönetilen kimlik oluşturun:
    > 
    >```azurecli-interactive    
    >az functionapp identity assign -g <your-resource-group> -n <your-App-Service-(function-app)-name>  
    >```
    >
    > Çıktı, bir `principalId` sonraki adım için gereken değer de dahil olmak üzere kimliğin ayrıntılarını görüntüler. 

1. `principalId`İşlev uygulamasının kimliğini Azure dijital TWINS örneğinizin _Azure Digital TWINS veri sahibi_ rolüne atamak için aşağıdaki komutta bulunan değeri kullanın.

    ```azurecli-interactive 
    az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<principal-ID>" --role "Azure Digital Twins Data Owner"
    ```

### <a name="configure-application-settings"></a>Uygulama ayarlarını yapılandırma

Bir ortam değişkenini ayarlayarak, işlevinizin URL 'sini işlevinizin erişimine açık hale getirin. Ortam değişkenleri hakkında daha fazla bilgi için bkz. [işlev uygulamanızı yönetme](../azure-functions/functions-how-to-use-azure-function-app-settings.md?tabs=portal). 

> [!TIP]
> Azure dijital TWINS örneğinin URL 'SI, örneğinizin ana bilgisayar adının başlangıcına *https://* eklenerek yapılır. Ana bilgisayar adını, örneğinizin tüm özellikleriyle birlikte görmek için öğesini çalıştırın `az dt show --dt-name <your-Azure-Digital-Twins-instance>` .

```azurecli-interactive 
az functionapp config appsettings set -g <your-resource-group> -n <your-App-Service-(function-app)-name> --settings "ADT_SERVICE_URL=https://<your-Azure-Digital-Twins-instance-host-name>"
```

# <a name="azure-portal"></a>[Azure portalı](#tab/portal)

[Azure Portal](https://portal.azure.com/)aşağıdaki adımları izleyin.

### <a name="assign-an-access-role"></a>Erişim rolü atama

[!INCLUDE [digital-twins-permissions-required.md](../../includes/digital-twins-permissions-required.md)]

Sistem tarafından atanan yönetilen kimlik, Azure kaynaklarının kimlik bilgilerini kodda depolamadan, bulut hizmetlerinde (örneğin, Azure Key Vault) kimliğini doğrulamasına olanak sağlar. Sistem tarafından atanan yönetilen kimliği etkinleştirdikten sonra, tüm gerekli izinler Azure rol tabanlı erişim denetimi aracılığıyla verilebilir. 

Bu tür yönetilen kimliğin yaşam döngüsü, bu kaynağın yaşam döngüsüne bağlıdır. Ayrıca, her kaynak yalnızca bir sistem tarafından atanmış yönetilen kimliğe sahip olabilir.

1. [Azure Portal](https://portal.azure.com/), arama kutusuna adını yazarak işlev uygulamanızı arayın. Sonuçlardan uygulamanızı seçin. 

    :::image type="content" source="media/how-to-create-azure-function/portal-search-for-function-app.png" alt-text="Azure portal ekran görüntüsü. İşlev uygulamasının adı Portal arama çubuğudur ve arama sonucu vurgulanır.":::

1. İşlev uygulaması sayfasında, soldaki menüden __kimlik__ ' i seçerek işlevin yönetilen kimliğiyle birlikte çalışabilirsiniz. __Sistem atandı__ sayfasında, __durumun__ **Açık** olarak ayarlandığını doğrulayın. Yoksa, şimdi ayarlayın ve değişikliği **kaydedin** .

    :::image type="content" source="media/how-to-create-azure-function/verify-system-managed-identity.png" alt-text="Azure portal ekran görüntüsü. İşlev uygulamasının kimlik sayfasında, durum seçeneği açık olarak ayarlanır." lightbox="media/how-to-create-azure-function/verify-system-managed-identity.png":::

1. __Azure rol atamaları__' nı seçin.

    :::image type="content" source="media/how-to-create-azure-function/add-role-assignment-1.png" alt-text="Azure portal ekran görüntüsü. Azure Işlevinin kimlik sayfasında, Izinler altında, düğme Azure rolü atamaları vurgulanır." lightbox="media/how-to-create-azure-function/add-role-assignment-1.png":::

    __+ Rol ataması Ekle (Önizleme)__ seçeneğini belirleyin.

    :::image type="content" source="media/how-to-create-azure-function/add-role-assignment-2.png" alt-text="Azure portal ekran görüntüsü. Azure rol atamaları sayfasında, rol ataması Ekle (Önizleme) düğmesi vurgulanır." lightbox="media/how-to-create-azure-function/add-role-assignment-2.png":::

1. __Rol ataması (Önizleme) Ekle__ sayfasında, aşağıdaki değerleri seçin:

    * **Kapsam**: _kaynak grubu_
    * **Abonelik**: Azure aboneliğinizi seçin.
    * **Kaynak grubu**: kaynak grubunuzu seçin.
    * **Rol**: _Azure dijital TWINS veri sahibi_

    __Kaydet__' i seçerek ayrıntıları kaydedin.

    :::image type="content" source="media/how-to-create-azure-function/add-role-assignment-3.png" alt-text="Yeni bir rol atamasının nasıl ekleneceğini gösteren Azure portal ekran görüntüsü. İletişim kutusu kapsam, abonelik, kaynak grubu ve rol alanlarını gösterir.":::

### <a name="configure-application-settings"></a>Uygulama ayarlarını yapılandırma

Azure dijital TWINS örneğinizin URL 'sini işlevinizin erişimine açık hale getirmek için bir ortam değişkeni ayarlayabilirsiniz. Uygulama ayarları, Azure dijital TWINS örneğine erişime izin vermek için ortam değişkenleri olarak sunulur. Ortam değişkenleri hakkında daha fazla bilgi için bkz. [işlev uygulamanızı yönetme](../azure-functions/functions-how-to-use-azure-function-app-settings.md?tabs=portal). 

Bir ortam değişkenini örneğinizin URL 'siyle birlikte ayarlamak için, önce örneğinizin ana bilgisayar adını bulun: 

1. [Azure Portal](https://portal.azure.com)örneğinizi arayın. 
1. Soldaki menüde __genel bakış__' ı seçin. 
1. __Ana bilgisayar adı__ değerini kopyalayın.

    :::image type="content" source="media/how-to-create-azure-function/instance-host-name.png" alt-text="Azure portal ekran görüntüsü. Örneğin Genel Bakış sayfasında ana bilgisayar adı değeri vurgulanır.":::

Artık bir uygulama ayarı oluşturabilirsiniz:

1. Portal arama çubuğunda, işlev uygulamanızı arayın ve sonra sonuçlardan seçim yapın.

    :::image type="content" source="media/how-to-create-azure-function/portal-search-for-function-app.png" alt-text="Azure portal ekran görüntüsü. İşlev uygulamasının adı, Portal arama çubuğunda arandı. Arama sonucu vurgulanır.":::

1. Sol tarafta __yapılandırma__' yı seçin. Ardından __uygulama ayarları__ sekmesinde __+ Yeni uygulama ayarı__' nı seçin.

    :::image type="content" source="media/how-to-create-azure-function/application-setting.png" alt-text="Azure portal ekran görüntüsü. İşlev uygulamasının yapılandırma sekmesinde, yeni bir uygulama ayarı oluşturmak için düğme vurgulanır.":::

1. Açılan pencerede, bir uygulama ayarı oluşturmak için kopyaladığınız ana bilgisayar adı değerini kullanın.
    * **Ad**: ADT_SERVICE_URL
    * **Değer**: https://{-Azure-dijital-TWINS-ana bilgisayar-adı}
    
    Bir uygulama ayarı oluşturmak için __Tamam ' ı__ seçin.
    
    :::image type="content" source="media/how-to-create-azure-function/add-application-setting.png" alt-text="Azure portal ekran görüntüsü. Uygulama ayarı Ekle/Düzenle sayfasında, ad ve değer alanları doldurulur. O K düğmesi vurgulanır.":::

1. Ayarı oluşturduktan sonra, __uygulama ayarları__ sekmesinde görünmelidir. **ADT_SERVICE_URL** listede göründüğünü doğrulayın. Ardından __Kaydet__' i seçerek yeni uygulama ayarını kaydedin.

    :::image type="content" source="media/how-to-create-azure-function/application-setting-save-details.png" alt-text="Azure portal ekran görüntüsü. Uygulama ayarları sekmesinde yeni bir D T SERVICE U R L ayarı vurgulanır. Kaydet düğmesi de vurgulanır.":::

1. Uygulama ayarlarındaki değişiklikler uygulamanın yeniden başlatılmasını gerektirir, bu nedenle istendiğinde uygulamanızı yeniden başlatmak için __devam__ ' ı seçin.

    :::image type="content" source="media/how-to-create-azure-function/save-application-setting.png" alt-text="Azure portal ekran görüntüsü. Bir notun uygulama ayarlarındaki tüm değişikliklerin uygulamanızı yeniden başlatmasıyla ilgili bir durum söz konusu olur. Devam düğmesi vurgulanır.":::

---

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, Azure 'da Azure Digital TWINS ile kullanım için bir işlev uygulaması ayarlarsınız. Daha sonra, [Azure dijital TWINS 'e IoT Hub verileri](how-to-ingest-iot-hub-data.md)almak için temel işlevinizde nasıl derleme yapılacağını inceleyin.
