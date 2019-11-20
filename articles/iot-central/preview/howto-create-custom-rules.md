---
title: Azure IoT Central özel kurallar ve bildirimler ile genişletin | Microsoft Docs
description: Çözüm geliştiricisi olarak, bir cihaz telemetri göndermeyi durdurduğu zaman e-posta bildirimleri göndermek için bir IoT Central uygulaması yapılandırın. Bu çözüm Azure Stream Analytics, Azure Işlevleri ve SendGrid kullanır.
author: dominicbetts
ms.author: dobett
ms.date: 11/01/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: 56ff01af6466e90ff4b69cd37c1638265c59b873
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73895871"
---
# <a name="extend-azure-iot-central-with-custom-rules-using-stream-analytics-azure-functions-and-sendgrid-preview-features"></a>Stream Analytics, Azure Işlevleri ve SendGrid kullanarak Azure IoT Central 'yi özel kurallarla genişletme (Önizleme özellikleri)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Bu nasıl yapılır kılavuzunda, çözüm geliştiricisi olarak, IoT Central uygulamanızı özel kurallarla ve bildirimlerle genişletmenin nasıl genişletileceği gösterilmektedir. Örnek, bir cihaz telemetri göndermeyi durdurduğu zaman bir işlece bildirim göndermeyi gösterir. Çözüm, bir cihazın telemetri göndermeyi durdurduğunu algılamak için bir [Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/) sorgusu kullanır. Stream Analytics işi, [SendGrid](https://sendgrid.com/docs/for-developers/partners/microsoft-azure/)kullanarak bildirim e-postaları göndermek Için [Azure işlevleri](https://docs.microsoft.com/azure/azure-functions/) 'ni kullanır.

Bu nasıl yapılır Kılavuzu, yerleşik kurallar ve eylemlerle daha önce neler yapabileceğini IoT Central nasıl genişletebileceğinizi gösterir.

Bu nasıl yapılır kılavuzunda şunları yapmayı öğreneceksiniz:

* *Sürekli veri dışa aktarma*kullanarak bir IoT Central uygulamasından Telemetriyi akışla.
* Bir cihazın veri göndermeyi durdurduğunu algılayan bir Stream Analytics sorgusu oluşturun.
* Azure Işlevleri ve SendGrid hizmetlerini kullanarak e-posta bildirimi gönderin.

## <a name="prerequisites"></a>Ön koşullar

Bu nasıl yapılır kılavuzundaki adımları tamamlayabilmeniz için etkin bir Azure aboneliğine ihtiyacınız vardır.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

### <a name="iot-central-application"></a>IoT Central uygulaması

[Azure IoT Central uygulama Yöneticisi](https://aka.ms/iotcentral) Web sitesinde aşağıdaki ayarlarla bir IoT Central uygulaması oluşturun:

| Ayar | Değer |
| ------- | ----- |
| Ödeme planı | Kullandıkça Öde |
| Uygulama şablonu | Mağaza içi analiz – koşul izleme |
| Uygulama adı | Varsayılanı kabul edin veya kendi adınızı seçin |
| URL'si | Varsayılanı kabul edin veya kendi benzersiz URL ön ekini seçin |
| Dizin | Azure Active Directory kiracınız |
| Azure aboneliği | Azure aboneliğiniz |
| Bölge | En yakın bölgeniz |

Bu makaledeki örnekler ve ekran görüntüleri **Birleşik Devletler** bölgesini kullanır. Size yakın bir konum seçin ve tüm kaynaklarınızı aynı bölgede oluşturduğunuzdan emin olun.

Bu uygulama şablonu telemetri gönderen iki sanal termostat cihaz içerir.

### <a name="resource-group"></a>Kaynak grubu

Oluşturduğunuz diğer kaynakları içerecek şekilde, **Detectstoppeddevices** adlı [bir kaynak grubu oluşturmak için Azure Portal](https://portal.azure.com/#create/Microsoft.ResourceGroup) kullanın. Azure kaynaklarınızı IoT Central uygulamanızla aynı konumda oluşturun.

### <a name="event-hubs-namespace"></a>Event Hubs ad alanı

Aşağıdaki ayarlarla [bir Event Hubs ad alanı oluşturmak için Azure Portal](https://portal.azure.com/#create/Microsoft.EventHub) kullanın:

| Ayar | Değer |
| ------- | ----- |
| Ad    | Ad alanı adınızı seçin |
| Fiyatlandırma katmanı | Temel |
| Abonelik | Aboneliğiniz |
| Kaynak grubu | DetectStoppedDevices |
| Konum | Doğu ABD |
| İşleme Birimleri | 1 |

### <a name="stream-analytics-job"></a>Stream Analytics işi

Aşağıdaki ayarlarla [bir Stream Analytics işi oluşturmak için Azure Portal](https://portal.azure.com/#create/Microsoft.StreamAnalyticsJob) kullanın:

| Ayar | Değer |
| ------- | ----- |
| Ad    | İş adınızı seçin |
| Abonelik | Aboneliğiniz |
| Kaynak grubu | DetectStoppedDevices |
| Konum | Doğu ABD |
| Barındırma ortamı | Bulut |
| Akış birimleri | 3 |

### <a name="function-app"></a>İşlev uygulaması

Aşağıdaki ayarlara sahip [bir işlev uygulaması oluşturmak için Azure Portal](https://portal.azure.com/#create/Microsoft.FunctionApp) kullanın:

| Ayar | Değer |
| ------- | ----- |
| Uygulama adı    | İşlev uygulamanızın adını seçin |
| Abonelik | Aboneliğiniz |
| Kaynak grubu | DetectStoppedDevices |
| İşletim Sistemi | Windows |
| Barındırma planı | Tüketim Planı |
| Konum | Doğu ABD |
| Çalışma zamanı yığını | .NET |
| Depolama | Yeni oluştur |

### <a name="sendgrid-account"></a>SendGrid hesabı

Aşağıdaki ayarlarla [bir SendGrid hesabı oluşturmak için Azure Portal](https://portal.azure.com/#create/Sendgrid.sendgrid) kullanın:

| Ayar | Değer |
| ------- | ----- |
| Ad    | SendGrid hesabınızın adını seçin |
| Parola | Parola oluştur |
| Abonelik | Aboneliğiniz |
| Kaynak grubu | DetectStoppedDevices |
| Fiyatlandırma katmanı | F1 Ücretsiz |
| İletişim bilgileri | Gerekli bilgileri doldurun |

Gerekli tüm kaynakları oluşturduktan sonra, **Detectstoppeddevices** kaynak grubunuz aşağıdaki ekran görüntüsüne benzer şekilde görünür:

![Durdurulan cihazların kaynak grubunu Algıla](media/howto-create-custom-rules/resource-group.png)

## <a name="create-an-event-hub"></a>Olay hub’ı oluşturma

Bir IoT Central uygulamasını bir olay hub 'ına sürekli olarak telemetri dışarı aktarmak için yapılandırabilirsiniz. Bu bölümde, IoT Central uygulamanızdan telemetri almak için bir olay hub 'ı oluşturursunuz. Olay Hub 'ı işleme için Stream Analytics işinize telemetri sunar.

1. Azure portal, Event Hubs ad alanına gidin ve **+ Event hub ' ı**seçin.
1. Olay Hub 'ınızı **centralexport**olarak adlandırın ve **Oluştur**' u seçin.

Event Hubs ad alanınız aşağıdaki ekran görüntüsüne benzer şekilde görünür:

![Event Hubs ad alanı](media/howto-create-custom-rules/event-hubs-namespace.png)

## <a name="get-sendgrid-api-key"></a>SendGrid API anahtarı al

İşlev uygulamanız, e-posta iletileri göndermek için bir SendGrid API anahtarına ihtiyaç duyuyor. SendGrid API anahtarı oluşturmak için:

1. Azure portal SendGrid hesabınıza gidin. Ardından, SendGrid hesabınıza erişmek için **Yönet** ' i seçin.
1. SendGrid hesabınızda **Ayarlar**' ı ve ardından **API anahtarları**' nı seçin. **API anahtarı oluştur**' a tıklayın:

    ![SendGrid API anahtarı oluşturma](media/howto-create-custom-rules/sendgrid-api-keys.png)

1. **API anahtarı oluştur** sayfasında, **tam erişim** izinleriyle **AzureFunctionAccess** adlı bir anahtar oluşturun.
1. API anahtarını, işlev uygulamanızı yapılandırırken ihtiyacınız olduğunu bir yere getirin.

## <a name="define-the-function"></a>İşlevi tanımlayın

Bu çözüm, Stream Analytics işi durdurulmuş bir cihaz algıladığında e-posta bildirimi göndermek için bir Azure Işlevleri uygulaması kullanır. İşlev uygulamanızı oluşturmak için:

1. Azure portal, **Detectstoppeddevices** kaynak grubundaki **App Service** örneğine gidin.
1. Yeni bir işlev oluşturmak için **+** seçin.
1. **BIR GELIŞTIRME ortamı seçin** sayfasında **Portal '** ı seçin ve ardından **devam**' ı seçin.
1. **Işlev oluştur** sayfasında, **Web kancası + API** ' yi seçin ve ardından **Oluştur**' u seçin.

Portal **HttpTrigger1**adlı varsayılan bir işlev oluşturur:

![Varsayılan HTTP tetikleyici işlevi](media/howto-create-custom-rules/default-function.png)

### <a name="configure-function-bindings"></a>İşlev bağlamalarını yapılandırma

SendGrid ile e-posta göndermek için, işlevinizin bağlamalarını aşağıdaki gibi yapılandırmanız gerekir:

1. **Tümleştirme**' i seçin, çıkış **http ($Return)** öğesini seçin ve ardından **Sil**' i seçin.
1. **+ Yeni çıkış**' ı seçin, sonra **SendGrid**' i ve ardından **Seç**' i seçin. SendGrid uzantısını yüklemek için **yüklemeyi** seçin.
1. Yükleme tamamlandığında, **işlev dönüş değeri kullan**' ı seçin. E-posta bildirimleri almak için geçerli bir **Adres** ekleyin.  E-posta gönderici olarak kullanılacak geçerli bir **Kimden adresi** ekleyin.
1. **SendGrid API anahtarı uygulama ayarının**yanındaki **Yeni** ' yi seçin. Anahtar olarak **Sendgridapikey** ve daha önce değer olarak not ettiğiniz SENDGRID API anahtarını girin. Ardından **Oluştur**’u seçin.
1. İşleviniz için SendGrid bağlamalarını kaydetmek için **Kaydet** ' i seçin.

Tümleştirme ayarları aşağıdaki ekran görüntüsüne benzer şekilde görünür:

![İşlev uygulaması tümleştirmeleri](media/howto-create-custom-rules/function-integrate.png)

### <a name="add-the-function-code"></a>İşlev kodunu ekleyin

İşlevinizi uygulamak için, gelen HTTP C# isteğini ayrıştırmak ve e-postaları şu şekilde göndermek üzere kodu ekleyin:

1. İşlev uygulamanızda **HttpTrigger1** işlevini seçin ve C# kodu şu kodla değiştirin:

    ```csharp
    #r "Newtonsoft.Json"
    #r "..\bin\SendGrid.dll"

    using System;
    using SendGrid.Helpers.Mail;
    using Microsoft.Azure.WebJobs.Host;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;

    public static SendGridMessage Run(HttpRequest req, ILogger log)
    {
        string requestBody = new StreamReader(req.Body).ReadToEnd();
        log.LogInformation(requestBody);
        var notifications = JsonConvert.DeserializeObject<IList<Notification>>(requestBody);

        SendGridMessage message = new SendGridMessage();
        message.Subject = "Contoso device notification";

        var content = "The following device(s) have stopped sending telemetry:<br/><br/><table><tr><th>Device ID</th><th>Time</th></tr>";
        foreach(var notification in notifications) {
            log.LogInformation($"No message received - Device: {notification.deviceid}, Time: {notification.time}");
            content += $"<tr><td>{notification.deviceid}</td><td>{notification.time}</td></tr>";
        }
        content += "</table>";
        message.AddContent("text/html", content);

        return message;
    }

    public class Notification
    {
        public string deviceid { get; set; }
        public string time { get; set; }
    }
    ```

    Yeni kodu kaydetene kadar bir hata iletisi görebilirsiniz.

1. İşlevi kaydetmek için **Kaydet** ' i seçin.

### <a name="test-the-function-works"></a>İşlevin çalışması test et

İşlevi portalda test etmek için önce kod düzenleyicisinin alt kısmındaki **Günlükler** ' i seçin. Ardından kod düzenleyicisinin sağ tarafındaki **Test** ' i seçin. **İstek gövdesi**olarak aşağıdaki JSON 'ı kullanın:

```json
[{"deviceid":"test-device-1","time":"2019-05-02T14:23:39.527Z"},{"deviceid":"test-device-2","time":"2019-05-02T14:23:50.717Z"},{"deviceid":"test-device-3","time":"2019-05-02T14:24:28.919Z"}]
```

İşlev günlüğü iletileri **Günlükler** panelinde görüntülenir:

![İşlev günlüğü çıkışı](media/howto-create-custom-rules/function-app-logs.png)

Birkaç dakika sonra **, e-posta adresi** aşağıdaki içeriğe sahip bir e-posta alır:

```txt
The following device(s) have stopped sending telemetry:

Device ID   Time
test-device-1   2019-05-02T14:23:39.527Z
test-device-2   2019-05-02T14:23:50.717Z
test-device-3   2019-05-02T14:24:28.919Z
```

## <a name="add-stream-analytics-query"></a>Stream Analytics sorgusu Ekle

Bu çözüm, bir cihazın 120 saniyeden uzun bir telemetri göndermeyi durdurduğunu algılamak için bir Stream Analytics sorgusu kullanır. Sorgu, giriş olarak olay hub 'ından Telemetriyi kullanır. İş, sorgu sonuçlarını işlev uygulamasına gönderir. Bu bölümde Stream Analytics işini yapılandırırsınız:

1. Azure portal, Stream Analytics işiniz ' ne gidin, **işler topolojisi** altında **girdileri**seçin, **+ akış girişi Ekle**' yi seçin ve **Olay Hub**'ı ' nı seçin.
1. Daha önce oluşturduğunuz Olay Hub 'ını kullanarak girişi yapılandırmak için aşağıdaki tablodaki bilgileri kullanın ve ardından **Kaydet**' i seçin:

    | Ayar | Değer |
    | ------- | ----- |
    | Giriş diğer adı | merkezileştirme telemetrisi |
    | Abonelik | Aboneliğiniz |
    | Olay hub'ı ad alanı | Olay Hub 'ı ad alanınız |
    | Olay Hub'ı adı | Var olan- **centralexport** kullanma |

1. **İşler topolojisi**altında **çıktılar**' i seçin, **+ Ekle**' yi seçin ve ardından **Azure işlevi**' ni seçin.
1. Çıktıyı yapılandırmak için aşağıdaki tablodaki bilgileri kullanın ve ardından **Kaydet**' i seçin:

    | Ayar | Değer |
    | ------- | ----- |
    | Çıktı diğer adı | EmailNotification |
    | Abonelik | Aboneliğiniz |
    | İşlev uygulaması | İşlev uygulamanız |
    | İşlev  | HttpTrigger1 |

1. **İşler topolojisi**altında **sorgula** ' yı seçin ve mevcut sorguyu aşağıdaki SQL ile değiştirin:

    ```sql
    with
    LeftSide as
    (
        SELECT
        -- Get the device ID from the message metadata and create a column
        GetMetadataPropertyValue([centraltelemetry], '[EventHub].[IoTConnectionDeviceId]') as deviceid1, 
        EventEnqueuedUtcTime AS time1
        FROM
        -- Use the event enqueued time for time-based operations
        [centraltelemetry] TIMESTAMP BY EventEnqueuedUtcTime
    ),
    RightSide as
    (
        SELECT
        -- Get the device ID from the message metadata and create a column
        GetMetadataPropertyValue([centraltelemetry], '[EventHub].[IoTConnectionDeviceId]') as deviceid2, 
        EventEnqueuedUtcTime AS time2
        FROM
        -- Use the event enqueued time for time-based operations
        [centraltelemetry] TIMESTAMP BY EventEnqueuedUtcTime
    )

    SELECT
        LeftSide.deviceid1 as deviceid,
        LeftSide.time1 as time
    INTO
        [emailnotification]
    FROM
        LeftSide
        LEFT OUTER JOIN
        RightSide 
        ON
        LeftSide.deviceid1=RightSide.deviceid2 AND DATEDIFF(second,LeftSide,RightSide) BETWEEN 1 AND 120
        where
        -- Find records where a device didn't send a message 120 seconds
        RightSide.deviceid2 is NULL
    ```

1. **Kaydet**’i seçin.
1. Stream Analytics işi başlatmak için **genel bakış**' ı ve ardından **Başlat** **' ı ve**ardından **Başlat**' ı seçin:

    ![Stream Analytics](media/howto-create-custom-rules/stream-analytics.png)

## <a name="configure-export-in-iot-central"></a>IoT Central dışarı aktarmayı yapılandırma

[Azure IoT Central uygulama Yöneticisi](https://aka.ms/iotcentral) Web sitesinde, contoso şablonundan oluşturduğunuz IoT Central uygulamasına gidin. Bu bölümde, uygulamayı sanal cihazınızdan, Olay Hub 'ınıza Telemetriyi akışa almak üzere yapılandırırsınız. Dışarı aktarmayı yapılandırmak için:

1. **Veri dışa aktarma** sayfasına gidin, **+ Yeni**' yi ve ardından **Azure Event Hubs**' yi seçin.
1. Dışarı aktarmayı yapılandırmak için aşağıdaki ayarları kullanın ve **Kaydet**' i seçin:

    | Ayar | Değer |
    | ------- | ----- |
    | Görünen Ad | Event Hubs dışarı aktar |
    | Etkin | Açık |
    | Event Hubs ad alanı | Event Hubs ad alanı adınız |
    | Olay hub'ı | centralexport |
    | Ölçümler | Açık |
    | Cihazlar | Kapalı |
    | Cihaz şablonları | Kapalı |

![Sürekli veri dışa aktarma yapılandırması](media/howto-create-custom-rules/cde-configuration.png)

Devam etmeden önce dışa aktarma durumunun **çalışmaya** bitmesini bekleyin.

## <a name="test"></a>Test etme

Çözümü test etmek için, IoT Central 'den sanal olarak durdurulan cihazlara sürekli veri vermeyi devre dışı bırakabilirsiniz:

1. IoT Central uygulamanızda, **veri dışa aktarma** sayfasına gidin ve dışarı **Aktar Event Hubs** dışarı aktarma yapılandırması ' nı seçin.
1. **Etkin** **olarak ayarlayın** ve **Kaydet**' i seçin.
1. En az iki dakika sonra, e **-posta adresi** aşağıdaki örnek içerik gibi görünen bir veya daha fazla e-posta alır:

    ```txt
    The following device(s) have stopped sending telemetry:

    Device ID         Time
    Thermostat-Zone1  2019-11-01T12:45:14.686Z
    ```

## <a name="tidy-up"></a>Tidy yukarı

Bu nasıl yapılır ve gereksiz maliyetlere engel olmak için, Azure portal **Detectstoppeddevices** kaynak grubunu silin.

Uygulama içindeki **Yönetim** sayfasından IoT Central uygulamasını silebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Bu nasıl yapılır kılavuzunda şunları öğrenirsiniz:

* *Sürekli veri dışa aktarma*kullanarak bir IoT Central uygulamasından Telemetriyi akışla.
* Bir cihazın veri göndermeyi durdurduğunu algılayan bir Stream Analytics sorgusu oluşturun.
* Azure Işlevleri ve SendGrid hizmetlerini kullanarak e-posta bildirimi gönderin.

Artık özel kurallar ve bildirimler oluşturmayı bildiğinize göre, önerilen sonraki adım [Azure IoT Central özel analizler ile nasıl uzatıleyeceğinizi](howto-create-custom-analytics.md)öğrenmelidir.
