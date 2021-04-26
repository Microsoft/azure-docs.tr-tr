---
title: Ortam başına yapılandırma kullan
titleSuffix: Azure App Configuration
description: Ortam başına yapılandırma değerlerini sağlamak için etiketleri kullanın.
ms.service: azure-app-configuration
author: AlexandraKemperMS
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 3/12/2020
ms.author: alkemper
ms.openlocfilehash: 84286df063994f3def15079cb9b190550d5bd977
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96929625"
---
# <a name="use-labels-to-enable-configurations-for-different-environments"></a>Farklı ortamlara yönelik yapılandırmaların etkinleştirilmesi için etiketleri kullanma

Birçok uygulamanın farklı ortamlar için farklı konfigürasyonlar kullanması gerekir. Bir uygulamanın, arka uç veritabanı için kullanılacak bağlantı dizesini tanımlayan bir yapılandırma değeri olduğunu varsayalım. Uygulama geliştiricileri üretimde kullanılan farklı bir veritabanını kullanır. Uygulamanın kullandığı veritabanı bağlantı dizesi, uygulama geliştirmeden üretime taşındıkça değişiklik sağlamalıdır.

Azure Uygulama yapılandırmasında, aynı anahtar için farklı değerler tanımlamak üzere *etiketleri* kullanabilirsiniz. Örneğin, geliştirme ve üretim için farklı değerlerle tek bir anahtar tanımlayabilirsiniz. Uygulama yapılandırmasına bağlanırken hangi etiketin yükleneceğini belirtebilirsiniz.

Bu işlevi göstermek için hızlı başlangıçta oluşturulan Web uygulamasını değiştireceksiniz [: Azure Uygulama yapılandırmasıyla bir ASP.NET Core uygulaması oluşturma](./quickstart-aspnet-core-app.md) , geliştirme ve üretime yönelik farklı yapılandırma ayarlarını kullanmak için. Devam etmeden önce hızlı başlangıcı doldurun.

## <a name="specify-a-label-when-adding-a-configuration-value"></a>Yapılandırma değeri eklenirken bir etiket belirtin

Azure portal, **yapılandırma Gezgini** ' ne gidin ve hızlı başlangıçta oluşturduğunuz *TestApp: Settings: fontcolor* anahtarını bulun. Bağlam menüsünü seçin ve sonra **Değer Ekle**' yi seçin.

> [!div class="mx-imgBorder"]
> ![Değer menü öğesi Ekle](media/labels-add-value.png)

**Değer Ekle** ekranında, **kırmızı** ve bir **geliştirme** **etiketi** **değeri** girin. **İçerik türünü** boş bırak. **Uygula**’yı seçin.

## <a name="load-configuration-values-with-a-specified-label"></a>Yapılandırma değerlerini belirtilen etiketle yükle

Azure Uygulama yapılandırması, varsayılan olarak yalnızca etiketi olmayan yapılandırma değerlerini yükler. Yapılandırma değerleriniz için Etiketler tanımladıysanız, uygulama yapılandırmasına bağlanırken kullanılacak etiketleri belirtmek isteyeceksiniz.

Önceki bölümde, geliştirme ortamı için farklı bir yapılandırma değeri oluşturdunuz. `HostingEnvironment.EnvironmentName`Bu değişkeni, uygulamanın Şu anda hangi ortamda çalıştığını dinamik olarak belirleyecek şekilde kullanırsınız. Daha fazla bilgi için bkz. [ASP.NET Core birden çok ortam kullanma](/aspnet/core/fundamentals/environments).

Yapılandırma değerlerini, ortam adını yöntemine geçirerek geçerli ortama karşılık gelen etiketle yükleyin `Select` :

```csharp
        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(options =>
                    options
                        .Connect(Environment.GetEnvironmentVariable("AppConfigConnectionString"))
                        // Load configuration values with no label
                        .Select(KeyFilter.Any, LabelFilter.Null)
                        // Override with any configuration values specific to current hosting env
                        .Select(KeyFilter.Any, hostingContext.HostingEnvironment.EnvironmentName)
                );
            })
            .UseStartup<Startup>());
```

> [!IMPORTANT]
> Yukarıdaki kod parçacığı, uygulama yapılandırma bağlantı dizesini adlı bir ortam değişkeninden yükler `AppConfigConnectionString` . Bu ortam değişkeninin doğru ayarlandığından emin olun.

`Select`Yöntemi iki kez çağrılır. İlk kez, hiçbir etiketi olmayan yapılandırma değerlerini yükler. Ardından, yapılandırma değerlerini geçerli ortama karşılık gelen etiketle yükler. Bu ortama özgü değerler etiketi olmayan karşılık gelen değerleri geçersiz kılar. Her anahtar için ortama özgü değerler tanımlamanız gerekmez. Bir anahtar, geçerli ortama karşılık gelen etikete sahip bir değere sahip değilse, etiketi olmayan değeri kullanır.

## <a name="test-in-different-environments"></a>Farklı ortamlarda test

`launchSettings.json`Dosyayı dizin altında açın `Properties` . `config`Altında girişi bulun `profiles` . Bölümünde, `environmentVariables` `ASPNETCORE_ENVIRONMENT` değişkenini olarak ayarlayın `Production` .

Yeni değerler ayarlandığında uygulamanızı derleyin ve çalıştırın.

```dotnetcli
dotnet build
dotnet run
```

Gitmek için bir Web tarayıcısı kullanın `http://localhost:5000` . Yazı tipi renginin siyah olduğunu fark edeceksiniz.

![Üretim yapılandırması ile çalışan Web uygulaması](media/labels-website-prod.png)

`launchSettings.json`Değişkenini olarak ayarlamak için güncelleştirin `ASPNETCORE_ENVIRONMENT` `Development` . `dotnet run` komutunu yeniden çalıştırın. 

Yazı tipi renginin artık kırmızı olduğunu fark edeceksiniz. Bunun nedeni, uygulamanın artık `TestApp:Settings:FontColor` etiketine sahip olan değerini kullanmadır `Development` . Diğer tüm yapılandırma değerleri, üretim değerleriyle aynı kalır.

![Geliştirme yapılandırmasıyla çalışan Web uygulaması](media/labels-website-dev.png)

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [ASP.NET Core yapılandırma](/aspnet/core/fundamentals/configuration/)
