---
title: Hedeflenen izleyiciler için özelliklerin aşamalı dağıtımını etkinleştir
titleSuffix: Azure App Configuration
description: Hedeflenen izleyiciler için özelliklerin aşamalı dağıtımını nasıl etkinleştireceğinizi öğrenin
ms.service: azure-app-configuration
author: AlexandraKemperMS
ms.author: alkemper
ms.topic: conceptual
ms.date: 11/20/2020
ms.openlocfilehash: c415eaeab2edd0a1b324bba4266266201cb50cbf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96929693"
---
# <a name="enable-staged-rollout-of-features-for-targeted-audiences"></a>Hedeflenen izleyiciler için özelliklerin aşamalı dağıtımını etkinleştir

Özellik bayrakları, uygulamanızdaki işlevselliği dinamik olarak etkinleştirmenizi veya devre dışı bırakabilmeniz için izin verir. Özellik filtreleri her değerlendirildiğinde bir özellik bayrağının durumunu belirlenir. `Microsoft.FeatureManagement`Kitaplık, `TargetingFilter` belirli bir Kullanıcı ve Grup listesi için veya belirtilen kullanıcı yüzdesi için özellik bayrağını sağlayan, öğesini içerir. `TargetingFilter` "yapışkan" dir. Bu, tek bir Kullanıcı bir özellik aldığında bu özelliği gelecekteki tüm isteklerde görmeye devam edebilecekleri anlamına gelir. `TargetingFilter`Bir demo sırasında belirli bir hesap için bir özelliği etkinleştirmek, farklı gruplardaki veya "halkalar" ve çok daha fazlasına sahip kullanıcılara aşamalı olarak yeni özellikler almak için kullanabilirsiniz.

Bu makalede, `TargetingFilter` Azure Uygulama yapılandırması ile kullanarak bir ASP.NET Core Web uygulamasındaki yeni bir özelliği belirtilen kullanıcılara ve gruplara nasıl dağıtacağınızı öğreneceksiniz.

## <a name="create-a-web-application-with-feature-flags-and-authentication"></a>Özellik bayrakları ve kimlik doğrulaması ile bir Web uygulaması oluşturma

Kullanıcıları ve grupları temel alan özellikleri almak için kullanıcıların oturum açmasına izin veren bir Web uygulamasına ihtiyacınız vardır.

1. Aşağıdaki komutu kullanarak yerel bir veritabanında kimlik doğrulayan bir Web uygulaması oluşturun:

   ```dotnetcli
   dotnet new mvc --auth Individual -o TestFeatureFlags
   ```

1. Derleyin ve çalıştırın, sonra sağ üst köşedeki **Kaydet** bağlantısını seçerek yeni bir kullanıcı hesabı oluşturun. E-posta adresini kullanın `test@contoso.com` . **Kayıt onayı** ekranında, **Hesabınızı onaylamak Için buraya tıklayın ' ı** seçin.

1. Yeni Web uygulamanıza Özellik bayrağı eklemek için [hızlı başlangıç: ASP.NET Core uygulamasına özellik bayrakları ekleme](./quickstart-feature-flag-aspnet-core.md) ' deki yönergeleri izleyin.

1. Uygulama yapılandırmasındaki Özellik bayrağını değiştirin. Bu eylemin, gezinti çubuğundaki **Beta** öğesinin görünürlüğünü denetlediğini doğrulayın.

## <a name="update-the-web-application-code-to-use-targetingfilter"></a>Web uygulaması kodunu TargetingFilter kullanacak şekilde güncelleştirme

Bu noktada, `Beta` tüm kullanıcılar için özelliği etkinleştirmek veya devre dışı bırakmak üzere özellik bayrağını kullanabilirsiniz. Özellik bayrağını başkaları için devre dışı bırakırken bazı kullanıcılar için etkinleştirmek üzere kodunuzu kullanmak üzere güncelleştirin `TargetingFilter` . Bu örnekte, oturum açmış kullanıcının e-posta adresini Kullanıcı KIMLIĞI olarak ve e-posta adresinin etki alanı adı kısmını grup olarak kullanacaksınız. Kullanıcıyı ve grubunu öğesine ekleyeceksiniz `TargetingContext` . `TargetingFilter`Her istek için özellik bayrağının durumunu öğrenmek için bu bağlamı kullanır.

1. Paketin en son sürümüne güncelleştirin `Microsoft.FeatureManagement.AspNetCore` .

   ```dotnetcli
   dotnet add package Microsoft.FeatureManagement.AspNetCore
   ```

1. *Testtargetingcontextaccessor. cs* dosyası ekleyin:

    ```csharp
    using Microsoft.AspNetCore.Http;
    using Microsoft.FeatureManagement.FeatureFilters;
    using System;
    using System.Collections.Generic;
    using System.Threading.Tasks;

    namespace TestFeatureFlags
    {
        public class TestTargetingContextAccessor : ITargetingContextAccessor
        {
            private const string TargetingContextLookup = "TestTargetingContextAccessor.TargetingContext";
            private readonly IHttpContextAccessor _httpContextAccessor;

            public TestTargetingContextAccessor(IHttpContextAccessor httpContextAccessor)
            {
                _httpContextAccessor = httpContextAccessor ?? throw new ArgumentNullException(nameof(httpContextAccessor));
            }

            public ValueTask<TargetingContext> GetContextAsync()
            {
                HttpContext httpContext = _httpContextAccessor.HttpContext;
                if (httpContext.Items.TryGetValue(TargetingContextLookup, out object value))
                {
                    return new ValueTask<TargetingContext>((TargetingContext)value);
                }
                List<string> groups = new List<string>();
                if (httpContext.User.Identity.Name != null)
                {
                    groups.Add(httpContext.User.Identity.Name.Split("@", StringSplitOptions.None)[1]);
                }
                TargetingContext targetingContext = new TargetingContext
                {
                    UserId = httpContext.User.Identity.Name,
                    Groups = groups
                };
                httpContext.Items[TargetingContextLookup] = targetingContext;
                return new ValueTask<TargetingContext>(targetingContext);
            }
        }
    }
    ```

1. *Startup. cs* dosyasında, *Microsoft. Featuremanagement. featurefilters* ad alanına bir başvuru ekleyin:

    ```csharp
    using Microsoft.FeatureManagement.FeatureFilters;
    ```

1. Hizmetine yapılan çağrıyı izleyerek, kaydolmak üzere *ConfigureServices* yöntemini güncelleştirin `TargetingFilter` `AddFeatureManagement()` :

    ```csharp
    services.AddFeatureManagement()
            .AddFeatureFilter<TargetingFilter>();
    ```

1. Önceki adımda oluşturulan ' i hizmet koleksiyonuna eklemek için *ConfigureServices* yöntemini güncelleştirin `TestTargetingContextAccessor` . *Targetingfilter* , özellik bayrağı her değerlendirildiğinde hedefleme bağlamını belirlemede onu kullanır.

    ```csharp
      services.AddSingleton<ITargetingContextAccessor, TestTargetingContextAccessor>();
    ```

Tüm *ConfigureServices* yöntemi şöyle görünür:

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(
            Configuration.GetConnectionString("DefaultConnection")));
    services.AddDefaultIdentity<IdentityUser>(options => options.SignIn.RequireConfirmedAccount = true)
            .AddEntityFrameworkStores<ApplicationDbContext>();
    services.AddControllersWithViews();
    services.AddRazorPages();

    // Add feature management, targeting filter, and ITargetingContextAccessor to service collection
    services.AddFeatureManagement().AddFeatureFilter<TargetingFilter>();
    services.AddSingleton<ITargetingContextAccessor, TestTargetingContextAccessor>();
    }
```

## <a name="update-the-feature-flag-to-use-targetingfilter"></a>Özellik bayrağını TargetingFilter kullanacak şekilde Güncelleştir

1. Azure portal, uygulama yapılandırma deponuza gidin ve **Feature Manager**' ı seçin.

1. Hızlı başlangıçta oluşturduğunuz *Beta* özelliği bayrağının bağlam menüsünü seçin. **Düzenle**'yi seçin.

    > [!div class="mx-imgBorder"]
    > ![Beta Özellik bayrağını Düzenle](./media/edit-beta-feature-flag.png)

1. **Düzenleme** ekranında, henüz seçili değilse **özellik bayrağını etkinleştir** onay kutusunu işaretleyin. Sonra **özellik filtresini kullan** onay kutusunu seçin.

1. **Hedef** radyo düğmesini seçin.

1. Aşağıdaki seçenekleri belirtin:

    - **Varsayılan yüzde**: 0
    - **Gruplar**: bir contoso.com **adı** ve  _50_ **yüzdesi** girin
    - **Kullanıcılar**: `test@contoso.com`

    Özellik filtresi ekranı şöyle görünür:

    > [!div class="mx-imgBorder"]
    > ![Koşullu Özellik bayrağı](./media/feature-flag-filter-enabled.png)

    Bu ayarlar aşağıdaki davranışa neden olacak:

    - Özellik bayrağı, `test@contoso.com` `test@contoso.com` _Kullanıcılar_ bölümünde listelendiğinden, her zaman Kullanıcı için etkindir.
    - _Contoso.com_ grubundaki diğer kullanıcıların %50 ' u için özellik bayrağı etkinleştirilir, çünkü _contoso.com_ _gruplar_ bölümünde _50_ _yüzdesi_ ile listelenir.
    - _Varsayılan yüzde_ _0_ olarak ayarlandığından, özellik diğer tüm kullanıcılar için her zaman devre dışıdır.

1. Bu ayarları kaydetmek ve **Özellik Yöneticisi** ekranına dönmek için **Uygula** ' yı seçin.

1. Özellik bayrağı için **özellik filtresi** artık *hedefleme* olarak görünür. Bu durum, *hedefleme* özelliği filtresi tarafından zorlanan ölçütlere göre, özellik bayrağının istek başına temelinde etkinleştirileceğini veya devre dışı bırakılacağını gösterir.

## <a name="targetingfilter-in-action"></a>TargetingFilter eylemi

Bu özellik bayrağının etkilerini görmek için uygulamayı derleyin ve çalıştırın. İlk olarak, *Beta* öğesi araç çubuğunda görünmez, çünkü _varsayılan yüzde_ seçeneği 0 olarak ayarlanmıştır.

`test@contoso.com`Kayıt sırasında ayarladığınız parolayı kullanarak, şimdi olarak oturum açın. *Beta* öğesi artık araç çubuğunda görünür, çünkü `test@contoso.com` hedeflenen kullanıcı olarak belirtilir.

Aşağıdaki videoda bu davranış eylemi gösterilmektedir.

> [!div class="mx-imgBorder"]
> ![TargetingFilter eylemi](./media/feature-flags-targetingfilter.gif)

`@contoso.com`Grup ayarlarının davranışını görmek için e-posta adresleriyle ek kullanıcılar oluşturabilirsiniz. Bu kullanıcıların %50 *Beta* öğesini görür. Diğer %50 *Beta* öğesini görmez.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Özellik yönetimine genel bakış](./concept-feature-management.md)