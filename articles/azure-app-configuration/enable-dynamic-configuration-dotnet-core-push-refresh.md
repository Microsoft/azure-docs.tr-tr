---
title: 'Öğretici: .NET Core uygulamasında anında yenileme kullanarak dinamik yapılandırma kullanma'
titleSuffix: Azure App Configuration
description: Bu öğreticide, anında yenileme kullanarak .NET Core uygulamaları için yapılandırma verilerini dinamik olarak güncelleştirme hakkında bilgi edineceksiniz.
services: azure-app-configuration
documentationcenter: ''
author: GrantMeStrength
manager: zhenlan
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 07/25/2020
ms.author: jken
ms.openlocfilehash: f330e69d37ac36e2a374e81fbffc6235c86feedd
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105625730"
---
# <a name="tutorial-use-dynamic-configuration-using-push-refresh-in-a-net-core-app"></a>Öğretici: .NET Core uygulamasında anında yenileme kullanarak dinamik yapılandırma kullanma

Uygulama yapılandırması .NET Core istemci kitaplığı, bir uygulamanın yeniden başlatılmasına neden olmadan isteğe bağlı yapılandırmayı güncelleştirmeyi destekler. Bir uygulama, aşağıdaki iki yaklaşımdan birini veya her ikisini kullanarak uygulama yapılandırmasındaki değişiklikleri algılamak üzere yapılandırılabilir.

1. Yoklama modeli: Bu, yapılandırmadaki değişiklikleri algılamak için yoklama kullanan varsayılan davranıştır. Bir ayarın önbelleğe alınmış değeri süre dolduktan sonra, bir sonraki çağrısı `TryRefreshAsync` veya `RefreshAsync` sunucuya bir istek gönderir ve yapılandırmanın değişip değişmediğini kontrol edin ve gerekirse güncelleştirilmiş yapılandırmayı çeker.

1. Gönderim modeli: Bu, yapılandırmadaki değişiklikleri algılamak için [uygulama yapılandırma olaylarını](./concept-app-configuration-event.md) kullanır. Uygulama yapılandırması, anahtar değer değişikliği olaylarını Azure Event Grid gönderecek şekilde ayarlandıktan sonra, uygulama, yapılandırmanın güncelleştirilmesini sağlamak için gereken toplam istek sayısını iyileştirmek üzere bu olayları kullanabilir. Uygulamalar, doğrudan Event Grid veya bir Web kancası, Azure işlevi veya bir Service Bus konu başlığı gibi [desteklenen olay işleyicilerinden](../event-grid/event-handlers.md) birini ya da bunlara abone olmayı tercih edebilir.

Uygulamalar, doğrudan Event Grid veya bir Web Kancası aracılığıyla ya da olayları Azure Service Bus ileterek bu olaylara abone olmayı seçebilirler. Azure Service Bus SDK, bir HTTP uç noktası olmayan veya değişiklik için olay kılavuzunu sürekli olarak yoklamayı istemeyen uygulamalar için bu işlemi basitleştiren bir ileti işleyicisini kaydetmek için bir API sağlar.

Bu öğreticide, anında yenileme kullanarak kodunuzda dinamik yapılandırma güncelleştirmelerini nasıl uygulayabileceğinizi gösterilmektedir. Hızlı başlangıçlarda tanıtılan uygulamada oluşturulur. Devam etmeden önce, önce [uygulama yapılandırması ile bir .NET Core uygulaması oluşturun](./quickstart-dotnet-core-app.md) .

Bu öğreticideki adımları uygulamak için herhangi bir kod düzenleyicisi kullanabilirsiniz. [Visual Studio Code](https://code.visualstudio.com/) , Windows, MacOS ve Linux platformlarında kullanılabilen harika bir seçenektir.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Yapılandırma değişikliği olaylarını uygulama yapılandırmasından Service Bus bir konuya göndermek için bir abonelik ayarlama
> * Uygulama yapılandırmasındaki değişikliklere yanıt olarak yapılandırmasını güncelleştirmek için .NET Core uygulamanızı ayarlayın.
> * Uygulamanızdaki en son yapılandırmayı tükettin.

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi yapmak için [.NET Core SDK](https://dotnet.microsoft.com/download)' yi yüklemelisiniz.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-azure-service-bus-topic-and-subscription"></a>Azure Service Bus konu ve abonelik ayarlama

Bu öğretici, değişiklikler için uygulama yapılandırmasını sürekli olarak yoklamayı istemeyen uygulamalar için yapılandırma değişikliklerinin algılanmasını basitleştirmek amacıyla Event Grid için Service Bus tümleştirmesini kullanır. Azure Service Bus SDK 'sı, uygulama yapılandırmasında değişiklikler algılandığında yapılandırmayı güncelleştirmek için kullanılabilecek bir ileti işleyicisini kaydetmek için bir API sağlar. Hızlı Başlangıç bölümündeki adımları uygulayın: bir hizmet veri yolu ad alanı, konu ve abonelik oluşturmak için [Service Bus konu ve abonelik oluşturmak için Azure Portal kullanın](../service-bus-messaging/service-bus-quickstart-topics-subscriptions-portal.md) .

Kaynaklar oluşturulduktan sonra, aşağıdaki ortam değişkenlerini ekleyin. Bunlar, uygulama kodundaki yapılandırma değişiklikleri için bir olay işleyicisini kaydetmek üzere kullanılacaktır.

| Anahtar | Değer |
|---|---|
| ServiceBusConnectionString | Service Bus ad alanı için bağlantı dizesi |
| ServiceBusTopic | Service Bus konusunun adı |
| ServiceBusSubscription | Service Bus aboneliğinin adı |

## <a name="set-up-event-subscription"></a>Olay aboneliğini ayarlama

1. Azure portal uygulama yapılandırma kaynağını açın ve bölmesinde açık ' a tıklayın `+ Event Subscription` `Events` .

    ![Uygulama yapılandırma olayları](./media/events-pane.png)

1. Ve için bir ad girin `Event Subscription` `System Topic` .

    ![Olay aboneliği oluştur](./media/create-event-subscription.png)

1. `Endpoint Type`As ' ı seçin `Service Bus Topic` , Service Bus konusunu seçip Açık ' a tıklayın `Confirm Selection` .

    ![Olay aboneliği Service Bus uç noktası](./media/event-subscription-servicebus-endpoint.png)

1. `Create`Olay aboneliği oluşturmak için açık ' a tıklayın.

1. `Event Subscriptions` `Events` Aboneliğin başarıyla oluşturulduğunu doğrulayan bölmede öğesine tıklayın.

    ![Uygulama yapılandırma olay abonelikleri](./media/event-subscription-view.png)

> [!NOTE]
> Yapılandırma değişiklikleri için abone olurken, uygulamanıza gönderilen olay sayısını azaltmak için bir veya daha fazla filtre kullanılabilir. Bunlar, [Event Grid abonelik filtreleri](../event-grid/event-filtering.md) veya [Service Bus abonelik filtreleri](../service-bus-messaging/topic-filters.md)olarak yapılandırılabilir. Örneğin, bir abonelik filtresi yalnızca belirli bir dizeyle başlayan bir anahtardaki değişikliklere yönelik olaylara abone olmak için kullanılabilir.

## <a name="register-event-handler-to-reload-data-from-app-configuration"></a>Uygulama yapılandırmasından verileri yeniden yüklemek için olay işleyicisini Kaydet

*Program. cs* dosyasını açın ve aşağıdaki kodla dosyayı güncelleştirin.

```csharp
using Microsoft.Azure.ServiceBus;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Configuration.AzureAppConfiguration;
using System;
using System.Diagnostics;
using System.Text;
using System.Text.Json;
using System.Threading.Tasks;

namespace TestConsole
{
    class Program
    {
        private const string AppConfigurationConnectionStringEnvVarName = "AppConfigurationConnectionString"; // e.g. Endpoint=https://{store_name}.azconfig.io;Id={id};Secret={secret}
        private const string ServiceBusConnectionStringEnvVarName = "ServiceBusConnectionString"; // e.g. Endpoint=sb://{service_bus_name}.servicebus.windows.net/;SharedAccessKeyName={key_name};SharedAccessKey={key}
        private const string ServiceBusTopicEnvVarName = "ServiceBusTopic";
        private const string ServiceBusSubscriptionEnvVarName = "ServiceBusSubscription";

        private static IConfigurationRefresher _refresher = null;

        static async Task Main(string[] args)
        {
            string appConfigurationConnectionString = Environment.GetEnvironmentVariable(AppConfigurationConnectionStringEnvVarName);

            IConfiguration configuration = new ConfigurationBuilder()
                .AddAzureAppConfiguration(options =>
                {
                    options.Connect(appConfigurationConnectionString);
                    options.ConfigureRefresh(refresh =>
                        refresh
                            .Register("TestApp:Settings:Message")
                            .SetCacheExpiration(TimeSpan.FromDays(30))  // Important: Reduce poll frequency
                    );

                    _refresher = options.GetRefresher();
                }).Build();

            RegisterRefreshEventHandler();
            var message = configuration["TestApp:Settings:Message"];
            Console.WriteLine($"Initial value: {configuration["TestApp:Settings:Message"]}");

            while (true)
            {
                await _refresher.TryRefreshAsync();

                if (configuration["TestApp:Settings:Message"] != message)
                {
                    Console.WriteLine($"New value: {configuration["TestApp:Settings:Message"]}");
                    message = configuration["TestApp:Settings:Message"];
                }
                
                await Task.Delay(TimeSpan.FromSeconds(1));
            }
        }

        private static void RegisterRefreshEventHandler()
        {
            string serviceBusConnectionString = Environment.GetEnvironmentVariable(ServiceBusConnectionStringEnvVarName);
            string serviceBusTopic = Environment.GetEnvironmentVariable(ServiceBusTopicEnvVarName);
            string serviceBusSubscription = Environment.GetEnvironmentVariable(ServiceBusSubscriptionEnvVarName);
            SubscriptionClient serviceBusClient = new SubscriptionClient(serviceBusConnectionString, serviceBusTopic, serviceBusSubscription);

            serviceBusClient.RegisterMessageHandler(
                handler: (message, cancellationToken) =>
               {
                   string messageText = Encoding.UTF8.GetString(message.Body);
                   JsonElement messageData = JsonDocument.Parse(messageText).RootElement.GetProperty("data");
                   string key = messageData.GetProperty("key").GetString();
                   Console.WriteLine($"Event received for Key = {key}");

                   _refresher.SetDirty();
                   return Task.CompletedTask;
               },
                exceptionReceivedHandler: (exceptionargs) =>
                {
                    Console.WriteLine($"{exceptionargs.Exception}");
                    return Task.CompletedTask;
                });
        }
    }
}
```

[SetDirty](/dotnet/api/microsoft.extensions.configuration.azureappconfiguration.iconfigurationrefresher.setdirty) yöntemi, yenileme için kaydedilmiş anahtar değerleri için, kirli olarak kaydedilen önbelleğe alınan değeri ayarlamak için kullanılır. Bu, sonraki çağrının `RefreshAsync` `TryRefreshAsync` önbelleğe alınmış değerleri uygulama yapılandırmasıyla yeniden doğrulamasını ve gerekirse bunları güncelleştirmesini sağlar.

Aynı anda birden çok örneğin yenilenmesi durumunda, olası azaltmayı azaltmak için, önbelleğe alınan değer kirli olarak işaretlenmeden önce rastgele bir gecikme eklenir. Önbelleğe alınan değer kirli olarak işaretlenmeden önce varsayılan en fazla gecikme 30 saniyedir, ancak yöntemine isteğe bağlı bir parametre geçirerek geçersiz kılınabilir `TimeSpan` `SetDirty` .

> [!NOTE]
> Anında yenilemeyi kullanırken uygulama yapılandırmasına yönelik isteklerin sayısını azaltmak için, `SetCacheExpiration(TimeSpan cacheExpiration)` uygun bir parametre değeriyle çağırmak önemlidir `cacheExpiration` . Bu, çekme yenileme için önbellek sona erme süresini denetler ve olay aboneliğinde veya Service Bus abonelikle ilgili bir sorun olması durumunda bir güvenlik ağı olarak kullanılabilir. Önerilen değer `TimeSpan.FromDays(30)` .

## <a name="build-and-run-the-app-locally"></a>Uygulamayı yerel olarak derleyin ve çalıştırın

1. **Appconfigurationconnectionstring** adlı bir ortam değişkeni ayarlayın ve bunu uygulama yapılandırma deponuzu için erişim anahtarı olarak ayarlayın. Windows komut istemi 'ni kullanırsanız, aşağıdaki komutu çalıştırın ve değişikliğin etkili olması için komut istemi ' ni yeniden başlatın:

    ```console
     setx AppConfigurationConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Windows PowerShell kullanıyorsanız şu komutu çalıştırın:

    ```powershell
     $Env:AppConfigurationConnectionString = "connection-string-of-your-app-configuration-store"
    ```

    MacOS veya Linux kullanıyorsanız şu komutu çalıştırın:

    ```console
     export AppConfigurationConnectionString='connection-string-of-your-app-configuration-store'
    ```

1. Konsol uygulamasını derlemek için aşağıdaki komutu çalıştırın:

    ```console
     dotnet build
    ```

1. Oluşturma başarıyla tamamlandıktan sonra, uygulamayı yerel olarak çalıştırmak için aşağıdaki komutu çalıştırın:

    ```console
     dotnet run
    ```

    ![Güncelleştirmeden önce yenileme çalıştırmasını gönder](./media/dotnet-core-app-pushrefresh-initial.png)

1. [Azure portalında](https://portal.azure.com) oturum açın. **Tüm kaynaklar**' ı seçin ve hızlı başlangıçta oluşturduğunuz uygulama yapılandırma deposu örneğini seçin.

1. **Yapılandırma Gezgini**' ni seçin ve aşağıdaki anahtarların değerlerini güncelleştirin:

    | Anahtar | Değer |
    |---|---|
    | TestApp: ayarlar: Ileti | Azure Uygulama yapılandırmasından alınan veriler-güncelleştirildi |

1. Etkinliğin işlenmesine ve güncelleştirilmesine izin vermek için 30 saniye bekleyin.

    ![Güncelleştirildikten sonra çekme yenileme çalıştırması](./media/dotnet-core-app-pushrefresh-final.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, uygulama yapılandırmasından yapılandırma ayarlarını dinamik olarak yenilemek için .NET Core uygulamanızı etkinleştirdiniz. Azure yönetilen kimliğin uygulama yapılandırmasına erişimi kolaylaştırmak için nasıl kullanılacağını öğrenmek için bir sonraki öğreticiye geçin.

> [!div class="nextstepaction"]
> [Yönetilen kimlik tümleştirmesi](./howto-integrate-azure-managed-service-identity.md)