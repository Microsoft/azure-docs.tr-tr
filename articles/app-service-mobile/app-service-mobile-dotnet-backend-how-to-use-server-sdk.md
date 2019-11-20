---
title: Mobile Apps için .NET arka uç sunucu SDK 'Sı ile çalışma | Microsoft Docs
description: Azure App Service Mobile Apps için .NET arka uç sunucu SDK ile nasıl çalışacağınızı öğrenin.
keywords: App Service, Azure App Service, mobil uygulama, mobil hizmet, ölçek, ölçeklenebilir, uygulama dağıtımı, Azure uygulama dağıtımı
services: app-service\mobile
documentationcenter: ''
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 0620554f-9590-40a8-9f47-61c48c21076b
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: 5372385bd4de0f5592a7fff3e4a78fbb5e1648dc
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388787"
---
# <a name="work-with-the-net-backend-server-sdk-for-azure-mobile-apps"></a>Azure Mobile Apps için .NET arka uç sunucu SDK’sı ile çalışma
[!INCLUDE [app-service-mobile-selector-server-sdk](../../includes/app-service-mobile-selector-server-sdk.md)]

> [!NOTE]
> Visual Studio App Center mobil uygulama dağıtımında merkezi konumdaki uçtan uca ve tümleşik hizmetleri destekler. Geliştiriciler Sürekli Tümleştirme ve Teslim işlem hattını ayarlamak için **Oluşturma**, **Test** ve **Dağıtım** hizmetlerini kullanabilir. Uygulama dağıtıldıktan sonra, geliştiriciler **Analiz** ve **Tanılama** hizmetlerini kullanarak uygulamanın durumunu ve kullanımını izleyebilir, **Gönderme** hizmetini kullanarak kullanıcılarla etkileşim kurabilir. Geliştiriciler ayrıca kullanıcıların kimliğini doğrulamak için **Kimlik Doğrulaması**'ndan ve uygulama verilerini bulutta kalıcı hale getirmek ve eşitlemek için **Veri** hizmetinden yararlanabilir.
>
> Mobil uygulamanızda bulut hizmetlerini tümleştirmek istiyorsanız bugün [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) kaydolun.

Bu konu başlığında, .NET arka uç sunucu SDK 'sının anahtar Azure App Service Mobile Apps senaryolarında nasıl kullanılacağı gösterilmektedir. Azure Mobile Apps SDK, ASP.NET uygulamanızdan mobil istemcilerle çalışmanıza yardımcı olur.

> [!TIP]
> [Azure Mobile Apps için .NET Server SDK][2] , GitHub 'da açık kaynaktır. Depo, tüm sunucu SDK birimi test paketi ve bazı örnek projeler dahil olmak üzere tüm kaynak kodlarını içerir.
>
>

## <a name="reference-documentation"></a>Başvuru belgeleri
Sunucu SDK 'Sı için başvuru belgeleri şurada bulunur: [Azure Mobile Apps .net başvurusu][1].

## <a name="create-app"></a>Nasıl yapılır: .NET mobil uygulaması arka ucu oluşturma
Yeni bir proje başlatıyorsanız, [Azure portalda] ya da Visual Studio kullanarak bir App Service uygulaması oluşturabilirsiniz. App Service uygulamayı yerel olarak çalıştırabilir veya projeyi bulut tabanlı App Service mobil uygulamanızda yayımlayabilirsiniz.

Mevcut bir projeye mobil özellikleri ekliyorsanız, [SDK 'Yı indirme ve başlatma](#install-sdk) bölümüne bakın.

### <a name="create-a-net-backend-using-the-azure-portal"></a>Azure portal kullanarak bir .NET arka ucu oluşturun
App Service mobil arka uç oluşturmak için [hızlı başlangıç öğreticisini][3] izleyin veya şu adımları izleyin:

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service-classic](../../includes/app-service-mobile-dotnet-backend-create-new-service-classic.md)]

*Başlarken* dikey penceresine geri döndüğünüzde **tablo API 'Si oluştur**altında **arka uç diliniz**olarak **C#** öğesini seçin. **İndir**' e tıklayın, sıkıştırılmış proje dosyalarını yerel bilgisayarınıza ayıklayın ve Visual Studio 'da çözümü açın.

### <a name="create-a-net-backend-using-visual-studio-2017"></a>Visual Studio 2017 kullanarak .NET arka ucu oluşturma

Visual Studio 'dan Azure Mobile Apps projesi yayımlamak için Visual Studio Yükleyicisi aracılığıyla Azure iş yükünü yükler. SDK 'Yı yükledikten sonra, aşağıdaki adımları kullanarak bir ASP.NET uygulaması oluşturun:

1. **Yeni proje** iletişim kutusunu açın (dosyadan **@no__t-** 2**Yeni** > **proje...** ).
2. **Görsel C#**  ' i genişletin ve **Web**' i seçin.
3. **ASP.NET Web uygulaması (.NET Framework)** seçeneğini belirleyin.
4. Proje adını girin. Daha sonra, **Tamam**'a tıklayın.
5. Şablonlar listesinden **Azure mobil uygulaması** ' nı seçin.
6. Çözümü oluşturmak için **Tamam** ' ı tıklatın.
7. **Çözüm Gezgini** projeye sağ tıklayın ve **Yayımla...** öğesini seçin ve ardından yayımlama hedefi olarak **App Service** seçin.
8. Kimlik doğrulaması yapmak için istemleri izleyin ve yayımlanacak yeni veya mevcut bir Azure App Service seçin.

### <a name="create-a-net-backend-using-visual-studio-2015"></a>Visual Studio 2015 kullanarak .NET arka ucu oluşturma

Visual Studio 'da bir Azure Mobile Apps projesi oluşturmak için [.net Için Azure SDK 'sını][4] (sürüm 2.9.0 veya üzeri) yükler. SDK 'Yı yükledikten sonra, aşağıdaki adımları kullanarak bir ASP.NET uygulaması oluşturun:

1. **Yeni proje** iletişim kutusunu açın (dosyadan **@no__t-** 2**Yeni** > **proje...** ).
2. **Şablonlar** > **görsel C#** ' i genişletin ve **Web**' i seçin.
3. **ASP.NET Web Uygulaması**'nı seçin.
4. Proje adını girin. Daha sonra, **Tamam**'a tıklayın.
5. *ASP.NET 4.5.2 şablonları*altında **Azure mobil uygulaması**' nı seçin. Bulutta bulunan **Konağı** denetleyerek bu projeyi yayımlayacağınız bulutta bir mobil arka uç oluşturun.
6. **Tamam**’a tıklayın.

## <a name="install-sdk"></a>Nasıl yapılır: SDK 'Yı Indirme ve başlatma
SDK [NuGet.org]' de kullanılabilir. Bu paket, SDK 'Yı kullanmaya başlamak için gereken temel işlevselliği içerir. SDK 'yı başlatmak için **HttpConfiguration** nesnesi üzerinde eylemler gerçekleştirmeniz gerekir.

### <a name="install-the-sdk"></a>SDK yükle
SDK 'yı yüklemek için, Visual Studio 'da sunucu projesine sağ tıklayın, **NuGet Paketlerini Yönet**' i seçin, [Microsoft. Azure. Mobile. Server] paketini arayın ve ardından **Install**' a tıklayın.

### <a name="server-project-setup"></a>Sunucu projesini Başlat
.NET arka uç sunucu projesi, OWıN başlangıç sınıfı ekleyerek diğer ASP.NET projelerine benzer şekilde başlatılır. NuGet paketine `Microsoft.Owin.Host.SystemWeb` ' a başvurultığınızdan emin olun. Visual Studio 'ya bu sınıfı eklemek için, sunucu projenize sağ tıklayın ve  >
**Yeni öğe** **Ekle**' yi ve ardından **Web** > **genel** > **owın başlangıç sınıfını**seçin.  Aşağıdaki öznitelikle bir sınıf oluşturulur:

    [assembly: OwinStartup(typeof(YourServiceName.YourStartupClassName))]

OWıN başlangıç sınıfınızın `Configuration()` yönteminde Azure Mobile Apps ortamını yapılandırmak için bir **HttpConfiguration** nesnesi kullanın.
Aşağıdaki örnek, eklenen hiçbir özellik olmadan sunucu projesini başlatır:

    // in OWIN startup class
    public void Configuration(IAppBuilder app)
    {
        HttpConfiguration config = new HttpConfiguration();

        new MobileAppConfiguration()
            // no added features
            .ApplyTo(config);

        app.UseWebApi(config);
    }

Bireysel özellikleri etkinleştirmek için, **ApplyTo**çağrılmadan önce **mobileappconfiguration** nesnesinde genişletme yöntemlerini çağırmanız gerekir. Örneğin, aşağıdaki kod, başlatma sırasında `[MobileAppController]` özniteliğine sahip tüm API denetleyicilerine varsayılan yolları ekler:

    new MobileAppConfiguration()
        .MapApiControllers()
        .ApplyTo(config);

Azure portal sunucu hızlı başlangıcı, **UseDefaultConfiguration () öğesini**çağırır. Bu, aşağıdaki kuruluma eşdeğerdir:

        new MobileAppConfiguration()
            .AddMobileAppHomeController()             // from the Home package
            .MapApiControllers()
            .AddTables(                               // from the Tables package
                new MobileAppTableConfiguration()
                    .MapTableControllers()
                    .AddEntityFramework()             // from the Entity package
                )
            .AddPushNotifications()                   // from the Notifications package
            .MapLegacyCrossDomainController()         // from the CrossDomain package
            .ApplyTo(config);

Kullanılan uzantı yöntemleri şunlardır:

* `AddMobileAppHomeController()` varsayılan Azure Mobile Apps giriş sayfasını sağlar.
* `MapApiControllers()`, `[MobileAppController]` özniteliğiyle donatılmış olan WebAPI denetleyicileri için özel API özellikleri sağlar.
* `AddTables()`, tablo denetleyicilerine `/tables` uç noktalarının eşleşmesini sağlar.
* `AddTablesWithEntityFramework()`, Entity Framework tabanlı denetleyicileri kullanarak `/tables` uç noktalarının eşlenmesinin kısa bir tarafındır.
* `AddPushNotifications()`, Notification Hubs cihazları kaydetmek için basit bir yöntem sağlar.
* `MapLegacyCrossDomainController()` yerel geliştirme için standart CORS üst bilgilerini sağlar.

### <a name="sdk-extensions"></a>SDK uzantıları
Aşağıdaki NuGet tabanlı uzantı paketleri, uygulamanız tarafından kullanılabilecek çeşitli mobil özellikler sağlar. **Mobileappconfiguration** nesnesini kullanarak başlatma sırasında uzantıları etkinleştirirsiniz.

* [Microsoft. Azure. Mobile. Server. QuickStart] temel Mobile Apps kurulumunu destekler. Başlatma sırasında **UseDefaultConfiguration** genişletme yöntemi çağırarak yapılandırmaya eklenir. Bu uzantı, şu uzantıları içerir: bildirimler, kimlik doğrulama, varlık, tablolar, etki alanları arası ve giriş paketleri. Bu paket, Azure portal kullanılabilir Mobile Apps hızlı başlangıç tarafından kullanılır.
* [Microsoft. Azure. Mobile. Server. Home](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Home/) , *Bu mobil uygulamanın varsayılan olarak Web sitesi kökünün çalışır ve çalışıyor sayfasını* uygular. **Addmobileapphomecontroller** uzantı yöntemini çağırarak yapılandırmaya ekleyin.
* [Microsoft. Azure. Mobile. Server. Tables](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Tables/) , verilerle çalışmaya yönelik sınıfları içerir ve veri işlem hattını ayarlar. **Addtables** uzantı yöntemini çağırarak yapılandırmaya ekleyin.
* [Microsoft. Azure. Mobile. Server. Entity](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Entity/) , Entity Framework SQL veritabanındaki verilere erişmesine olanak sağlar. **Addtableswithentityframework** genişletme yöntemini çağırarak yapılandırmaya ekleyin.
* [Microsoft. Azure. Mobile. Server. Authentication] , belirteçleri doğrulamak için kullanılan owın ara yazılımı için kimlik doğrulaması ve ayar sağlar. **Addappserviceauthentication** ve **ıappbuilder**çağırarak yapılandırmaya ekleyin. **Useappserviceauthentication** uzantı yöntemleri.
* [Microsoft. Azure. Mobile. Server. Notifications] anında iletme bildirimleri sunar ve bir anında kayıt uç noktası tanımlar. **Addpushnotifications** genişletme yöntemini çağırarak yapılandırmaya ekleyin.
* [Microsoft. Azure. Mobile. Server. CrossDomain](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.CrossDomain/) , mobil uygulamanızdan eski Web tarayıcılarına veri sunan bir denetleyici oluşturur. **MapLegacyCrossDomainController** Extension metodunu çağırarak yapılandırmaya ekleyin.
* [Microsoft. Azure. Mobile. Server. Login] , özel kimlik doğrulama senaryolarında kullanılan bir statik yöntem olan AppServiceLoginHandler. CreateToken () yöntemini sağlar.

## <a name="publish-server-project"></a>Nasıl yapılır: sunucu projesini yayımlama
Bu bölümde, .NET arka uç projenizi Visual Studio 'dan nasıl yayımlayacağınız gösterilmektedir. Ayrıca, arka uç projenizi [Git](../app-service/deploy-local-git.md) ' i veya burada bulunan diğer yöntemlerden birini kullanarak dağıtabilirsiniz.

1. Visual Studio 'da NuGet paketlerini geri yüklemek için projeyi yeniden derleyin.
2. Çözüm Gezgini, projeye sağ tıklayın, **Yayımla**' ya tıklayın. İlk kez yayımladığınızda, bir yayımlama profili tanımlamanız gerekir. Tanımlı bir profiliniz zaten varsa, bunu seçip **Yayımla**' ya tıklayabilirsiniz.
3. Bir yayımlama hedefi seçmek isteyip istemediğiniz sorulursa **Microsoft Azure App Service** >  ' e tıklayın **, ardından (** gerekirse) Azure kimlik bilgilerinizle oturum açın.
   Visual Studio, yayımlama ayarlarınızı doğrudan Azure 'dan indirir ve güvenli bir şekilde depolar.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-1.png)
4. **Aboneliğinizi**seçin, **Görünüm**' den **kaynak türü** ' nü seçin, **mobil uygulama**' yı genişletin ve mobil uygulama arka ucunuza ve ardından **Tamam**' a tıklayın.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-2.png)
5. Yayımlama profili bilgilerini doğrulayın ve **Yayımla**' ya tıklayın.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-3.png)

    Mobil uygulama arka ucunuz başarıyla yayımlandığında, başarıyı belirten bir giriş sayfası görürsünüz.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-success.png)

## <a name="define-table-controller"></a>Nasıl yapılır: tablo denetleyicisi tanımlama
Mobil istemcilere bir SQL tablosu göstermek için bir tablo denetleyicisi tanımlayın.  Bir tablo denetleyicisini yapılandırmak için üç adım gerekir:

1. Veri Aktarımı nesnesi (DTO) sınıfı oluşturun.
2. Mobil DbContext sınıfında bir tablo başvurusu yapılandırın.
3. Tablo denetleyicisi oluşturun.

Veri Aktarımı nesnesi (DTO), `EntityData` C# ' den devralan düz bir nesnedir.  Örnek:

    public class TodoItem : EntityData
    {
        public string Text { get; set; }
        public bool Complete {get; set;}
    }

DTO, tabloyu SQL veritabanı içinde tanımlamak için kullanılır.  Veritabanı girişini oluşturmak için, kullandığınız DbContext 'e bir `DbSet<>` özelliği ekleyin.  Azure Mobile Apps için varsayılan proje şablonunda, DbContext adı `Models\MobileServiceContext.cs` ' dır:

    public class MobileServiceContext : DbContext
    {
        private const string connectionStringName = "Name=MS_TableConnectionString";

        public MobileServiceContext() : base(connectionStringName)
        {

        }

        public DbSet<TodoItem> TodoItems { get; set; }

        protected override void OnModelCreating(DbModelBuilder modelBuilder)
        {
            modelBuilder.Conventions.Add(
                new AttributeToColumnAnnotationConvention<TableColumnAttribute, string>(
                    "ServiceColumnTable", (property, attributes) => attributes.Single().ColumnType.ToString()));
        }
    }

Azure SDK 'Sı yüklüyse, artık aşağıdaki gibi bir şablon tablo denetleyicisi oluşturabilirsiniz:

1. Denetleyiciler klasörüne sağ tıklayın ve @no__t **Ekle**-1 denetleyicisi ' ni seçin **...**
2. **Azure Mobile Apps tablo denetleyicisi** seçeneğini belirleyin ve ardından **Ekle**' ye tıklayın.
3. **Denetleyici Ekle** iletişim kutusunda:
   * **Model sınıfı** açılan menüsünde Yeni DTO ' ı seçin.
   * **DbContext** açılan menüsünde, mobil hizmet DbContext sınıfını seçin.
   * Denetleyici adı sizin için oluşturulur.
4. **Ekle**'ye tıklayın.

Hızlı başlangıç sunucu projesi basit bir **TodoItemController**için bir örnek içerir.

### <a name="adjust-pagesize"></a>Nasıl yapılır: tablo disk belleği boyutunu ayarlama
Azure Mobile Apps, varsayılan olarak istek başına 50 kayıt döndürür.  Sayfalama, istemcinin kullanıcı ARABIRIMI iş parçacığını veya sunucusunu çok uzun süredir bağlamamasını sağlar ve iyi bir kullanıcı deneyimi sağlar. Tablo disk belleği boyutunu değiştirmek için, "izin verilen sorgu boyutu" sunucu tarafı ve istemci tarafı sayfa boyutu, sunucu tarafı "izin verilen sorgu boyutu" `EnableQuery` özniteliği kullanılarak ayarlanır:

    [EnableQuery(PageSize = 500)]

PageSize 'nın, istemci tarafından istenen boyuttan büyük veya daha büyük olduğundan emin olun.  İstemci sayfa boyutunu değiştirme hakkında ayrıntılı bilgi için, belirli istemciye nasıl bir şekilde bakın.

## <a name="how-to-define-a-custom-api-controller"></a>Nasıl yapılır: özel bir API denetleyicisi tanımlama
Özel API denetleyicisi, bir uç nokta açığa çıkarmak için mobil uygulamanızın arka ucuna en temel işlevselliği sağlar. [MobileAppController] özniteliğini kullanarak mobil 'e özgü bir API denetleyicisi kaydedebilirsiniz. @No__t-0 özniteliği rotayı kaydeder, Mobile Apps JSON serileştiricisini ayarlar ve [istemci sürümü denetimini](app-service-mobile-client-and-server-versioning.md)etkinleştirir.

1. Visual Studio 'da denetleyiciler klasörüne sağ tıklayın ve ardından  >  denetleyicisi **Ekle**' ye tıklayın, **Web API 2 denetleyicisi @ no__t-4Empty** ' i seçin ve **Ekle**' ye tıklayın.
2. @No__t-1 gibi bir **Denetleyici adı**sağlayın ve **Ekle**' ye tıklayın.
3. Yeni denetleyici sınıf dosyasında aşağıdaki using ifadesini ekleyin:

        using Microsoft.Azure.Mobile.Server.Config;
4. Aşağıdaki örnekte olduğu gibi, **[Mobileappcontroller]** özniteliğini API denetleyicisi sınıf tanımına uygulayın:

        [MobileAppController]
        public class CustomController : ApiController
        {
              //...
        }
5. App_Start/Startup. MobileApp. cs dosyasında, **Mapapicontrolleyiciler** uzantı yöntemine aşağıdaki örnekte olduğu gibi bir çağrı ekleyin:

        new MobileAppConfiguration()
            .MapApiControllers()
            .ApplyTo(config);

@No__t-1 yerine `UseDefaultConfiguration()` genişletme yöntemini de kullanabilirsiniz. **Mobileappcontrollerattribute** uygulanmış herhangi bir denetleyiciye istemciler tarafından erişilebilir, ancak herhangi bir mobil uygulama istemci SDK 'sını kullanan istemciler tarafından doğru şekilde tüketilmeyebilir.

## <a name="how-to-work-with-authentication"></a>Nasıl yapılır: kimlik doğrulamasıyla çalışma
Azure Mobile Apps, mobil arka ucunuzun güvenliğini sağlamak için App Service kimlik doğrulaması/yetkilendirme kullanır.  Bu bölümde, .NET arka uç sunucu projenizde aşağıdaki kimlik doğrulama ile ilgili görevlerin nasıl gerçekleştirileceği gösterilmektedir:

* [Nasıl yapılır: sunucu projesine kimlik doğrulaması ekleme](#add-auth)
* [Nasıl yapılır: uygulamanız için özel kimlik doğrulaması kullanma](#custom-auth)
* [Nasıl yapılır: kimliği doğrulanmış kullanıcı bilgilerini alma](#user-info)
* [Nasıl yapılır: yetkili kullanıcılar için veri erişimini kısıtlama](#authorize)

### <a name="add-auth"></a>Nasıl yapılır: sunucu projesine kimlik doğrulaması ekleme
**Mobileappconfiguration** nesnesini genişleterek ve owın ara yazılımını yapılandırarak sunucu projenize kimlik doğrulama ekleyebilirsiniz. [Microsoft. Azure. Mobile. Server. QuickStart] paketini yüklediğinizde ve **UseDefaultConfiguration** genişletme yöntemini çağırdığınızda, adım 3 ' e atlayabilirsiniz.

1. Visual Studio 'da [Microsoft. Azure. Mobile. Server. Authentication] paketini yükledikten sonra.
2. Startup.cs proje dosyasında, **yapılandırma** yönteminin başına aşağıdaki kod satırını ekleyin:

        app.UseAppServiceAuthentication(config);

    Bu OWIN ara yazılım bileşeni, ilişkili App Service ağ geçidi tarafından verilen belirteçleri doğrular.
3. Kimlik doğrulaması gerektiren herhangi bir denetleyiciye veya yönteme `[Authorize]` özniteliğini ekleyin.

Mobile Apps arka ucunuza istemcilerin kimliğini doğrulama hakkında bilgi edinmek için bkz. [uygulamanıza kimlik doğrulaması ekleme](app-service-mobile-ios-get-started-users.md).

### <a name="custom-auth"></a>Nasıl yapılır: uygulamanız için özel kimlik doğrulaması kullanma
> [!IMPORTANT]
> Özel kimlik doğrulamasını etkinleştirmek için, önce Azure portal App Service bir sağlayıcı seçmeden App Service kimlik doğrulamasını etkinleştirmeniz gerekir. Bu, WEBSITE_AUTH_SIGNING_KEY ortam değişkenini barındırılırken etkinleştirir.
> 
> 
> App Service kimlik doğrulaması/yetkilendirme sağlayıcılarından birini kullanmak istemiyorsanız, kendi oturum açma sisteminizi uygulayabilirsiniz. Kimlik doğrulama belirteci oluşturmaya yardımcı olmak için [Microsoft. Azure. Mobile. Server. Login] paketini yükler.  Kullanıcı kimlik bilgilerini doğrulamak için kendi kodunuzu girin. Örneğin, bir veritabanında sallanan ve karma hale getirilmiş parolalara karşı kontrol edebilirsiniz. Aşağıdaki örnekte, `isValidAssertion()` yöntemi (başka bir yerde tanımlanır) Bu denetimlerden sorumludur.

Özel kimlik doğrulaması, bir ApiController oluşturularak ve `register` ve `login` eylemleri ortaya çıkaran şekilde gösterilir. İstemci, kullanıcıdan bilgileri toplamak için özel bir kullanıcı arabirimi kullanmalıdır.  Daha sonra bilgiler, standart bir HTTP POST çağrısıyla API 'ye gönderilir. Sunucu onay doğrulamasını doğrulandıktan sonra `AppServiceLoginHandler.CreateToken()` yöntemi kullanılarak bir belirteç verilir.  ApiController `[MobileAppController]` özniteliğini **kullanmamalıdır** .

Örnek `login` eylem:

        public IHttpActionResult Post([FromBody] JObject assertion)
        {
            if (isValidAssertion(assertion)) // user-defined function, checks against a database
            {
                JwtSecurityToken token = AppServiceLoginHandler.CreateToken(new Claim[] { new Claim(JwtRegisteredClaimNames.Sub, assertion["username"]) },
                    mySigningKey,
                    myAppURL,
                    myAppURL,
                    TimeSpan.FromHours(24) );
                return Ok(new LoginResult()
                {
                    AuthenticationToken = token.RawData,
                    User = new LoginResultUser() { UserId = userName.ToString() }
                });
            }
            else // user assertion was not valid
            {
                return this.Request.CreateUnauthorizedResponse();
            }
        }

Önceki örnekte, LoginResult ve LoginResultUser, gerekli özellikleri açığa çıkarıseri hale getirilebilir nesnelerdir. İstemci, oturum açma yanıtlarının formun JSON nesneleri olarak döndürülmesini bekliyor:

        {
            "authenticationToken": "<token>",
            "user": {
                "userId": "<userId>"
            }
        }

@No__t-0 yöntemi bir *hedef kitle* ve *veren* parametresi içerir. Bu parametrelerin her ikisi de, HTTPS şeması kullanılarak uygulama kökinizin URL 'sine ayarlanır. Benzer şekilde, *SecretKey* değerini uygulamanızın İmzalama anahtarının değeri olacak şekilde ayarlamanız gerekir. İmzalama anahtarını, anahtarları krem etmek ve kullanıcıların kimliğine bürünmek için kullanılabilecek şekilde dağıtmayın. App Service 'de barındırılırken imzalama anahtarını, *@ no__t-1AUTH @ no__t-2SIGNING @ no__t-3KEY* ortam değişkenine başvurarak elde edebilirsiniz. Yerel bir hata ayıklama bağlamında gerekliyse, anahtarı almak ve uygulamayı bir uygulama ayarı olarak depolamak için [kimlik doğrulamasında yerel hata ayıklama](#local-debug) bölümündeki yönergeleri izleyin.

Verilen belirteç diğer talepleri ve bir süre sonu tarihini de içerebilir.  En düşük düzeyde, verilen belirtecin bir konu (**Sub**) talebi içermesi gerekir.

Kimlik doğrulama yolunu aşırı yükleyerek standart istemci `loginAsync()` yöntemini destekleyebilirsiniz.  İstemci oturum açmak için `client.loginAsync('custom');` ' ı çağırırsa, yönlendirmenize `/.auth/login/custom` olmalıdır.  @No__t-0 kullanarak özel kimlik doğrulama denetleyicisinin yolunu ayarlayabilirsiniz:

    config.Routes.MapHttpRoute("custom", ".auth/login/custom", new { controller = "CustomAuth" });

> [!TIP]
> @No__t-0 yaklaşımını kullanmak, kimlik doğrulama belirtecinin, hizmete yapılan her sonraki çağrıya eklenmesini sağlar.
>
>

### <a name="user-info"></a>Nasıl yapılır: kimliği doğrulanmış kullanıcı bilgilerini alma
App Service tarafından bir kullanıcının kimliği doğrulandığında, .NET arka uç kodunuzda atanan kullanıcı KIMLIĞINE ve diğer bilgilere erişebilirsiniz. Kullanıcı bilgileri, arka uçta yetkilendirme kararları oluşturmak için kullanılabilir. Aşağıdaki kod bir istekle ilişkili kullanıcı KIMLIĞINI edinir:

    // Get the SID of the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;

SID, sağlayıcıya özgü Kullanıcı KIMLIĞINDEN türetilir ve belirli bir Kullanıcı ve oturum açma sağlayıcısı için statiktir.  SID, geçersiz kimlik doğrulama belirteçleri için null.

App Service Ayrıca, oturum açma sağlayıcınızdan belirli talepler istemeniz de sağlar. Her kimlik sağlayıcısı, kimlik sağlayıcısı SDK 'sını kullanarak daha fazla bilgi sağlayabilir.  Örneğin, arkadaşlar bilgileri için Facebook Graph API kullanabilirsiniz.  Azure portal sağlayıcı dikey penceresinde istenen talepleri belirtebilirsiniz. Bazı talepler kimlik sağlayıcısı ile ek yapılandırma gerektirir.

Aşağıdaki kod, Facebook Graph API karşı istek yapmak için gereken erişim belirtecini içeren oturum açma kimlik bilgilerini almak için **Getappserviceıdentityasync** genişletme yöntemini çağırır:

    // Get the credentials for the logged-in user.
    var credentials =
        await this.User
        .GetAppServiceIdentityAsync<FacebookCredentials>(this.Request);

    if (credentials.Provider == "Facebook")
    {
        // Create a query string with the Facebook access token.
        var fbRequestUrl = "https://graph.facebook.com/me/feed?access_token="
            + credentials.AccessToken;

        // Create an HttpClient request.
        var client = new System.Net.Http.HttpClient();

        // Request the current user info from Facebook.
        var resp = await client.GetAsync(fbRequestUrl);
        resp.EnsureSuccessStatusCode();

        // Do something here with the Facebook user information.
        var fbInfo = await resp.Content.ReadAsStringAsync();
    }

**Getappserviceıdentityasync** genişletme metodunu sağlamak için `System.Security.Principal` için bir using açıklaması ekleyin.

### <a name="authorize"></a>Nasıl yapılır: yetkili kullanıcılar için veri erişimini kısıtlama
Önceki bölümde, kimliği doğrulanmış bir kullanıcının kullanıcı KIMLIĞINI nasıl alacağınızı gösterdik. Bu değere göre verilere ve diğer kaynaklara erişimi kısıtlayabilirsiniz. Örneğin, tablolara bir UserID sütunu eklemek ve sorgu sonuçlarının Kullanıcı KIMLIĞINE göre filtrelenmesi, döndürülen verileri yalnızca yetkili kullanıcılara sınırlamak için basit bir yoldur. Aşağıdaki kod yalnızca SID, TodoItem tablosundaki UserID sütunundaki değerle eşleştiğinde veri satırlarını döndürür:

    // Get the SID of the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;

    // Only return data rows that belong to the current user.
    return Query().Where(t => t.UserId == sid);

@No__t-0 yöntemi, filtrelemeyi işlemek için LINQ tarafından işlenebilen bir `IQueryable` döndürür.

## <a name="how-to-add-push-notifications-to-a-server-project"></a>Nasıl yapılır: sunucu projesine anında iletme bildirimleri ekleme
**Mobileappconfiguration** nesnesini genişleterek ve bir Notification Hubs istemcisi oluşturarak sunucu projenize anında iletme bildirimleri ekleyin.

1. Visual Studio 'da sunucu projesine sağ tıklayın ve **NuGet Paketlerini Yönet**' e tıklayın, `Microsoft.Azure.Mobile.Server.Notifications` ' i arayın ve ardından **Install**' a tıklayın.
2. Notification Hubs istemci kitaplığını içeren `Microsoft.Azure.NotificationHubs` paketini yüklemek için bu adımı tekrarlayın.
3. App_Start/Startup. MobileApp. cs dosyasında, başlatma sırasında **Addpushnotifications ()** uzantı yöntemine bir çağrı ekleyin:

        new MobileAppConfiguration()
            // other features...
            .AddPushNotifications()
            .ApplyTo(config);
4. Notification Hubs istemcisi oluşturan aşağıdaki kodu ekleyin:

        // Get the settings for the server project.
        HttpConfiguration config = this.Configuration;
        MobileAppSettingsDictionary settings =
            config.GetMobileAppSettingsProvider().GetMobileAppSettings();

        // Get the Notification Hubs credentials for the Mobile App.
        string notificationHubName = settings.NotificationHubName;
        string notificationHubConnection = settings
            .Connections[MobileAppSettingsKeys.NotificationHubConnectionString].ConnectionString;

        // Create a new Notification Hub client.
        NotificationHubClient hub = NotificationHubClient
            .CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

Artık Notification Hubs istemcisini, kayıtlı cihazlara anında iletme bildirimleri göndermek için kullanabilirsiniz. Daha fazla bilgi için bkz. [uygulamanıza anında iletme bildirimleri ekleme](app-service-mobile-ios-get-started-push.md). Notification Hubs hakkında daha fazla bilgi edinmek için bkz. [Notification Hubs genel bakış](../notification-hubs/notification-hubs-push-notification-overview.md).

## <a name="tags"></a>Nasıl yapılır: etiketleri kullanarak hedeflenen gönderimi etkinleştirme
Notification Hubs, etiketleri kullanarak belirli kayıtlara hedeflenmiş bildirimler göndermenizi sağlar. Birkaç etiket otomatik olarak oluşturulur:

* Yükleme KIMLIĞI belirli bir cihazı tanımlar.
* Kimliği doğrulanmış SID 'yi temel alan Kullanıcı kimliği belirli bir kullanıcıyı tanımlar.

Yükleme KIMLIĞINE **MobileServiceClient**üzerinde **ınstalyüklemekimliği** özelliğinden erişilebilir.  Aşağıdaki örnek, Notification Hubs ' deki belirli bir yüklemeye etiket eklemek için bir yükleme KIMLIĞININ nasıl kullanılacağını gösterir:

    hub.PatchInstallation("my-installation-id", new[]
    {
        new PartialUpdateOperation
        {
            Operation = UpdateOperationType.Add,
            Path = "/tags",
            Value = "{my-tag}"
        }
    });

Anında iletme bildirimi kaydı sırasında istemci tarafından sağlanan Etiketler, yükleme oluşturulurken arka uç tarafından yoksayılır. Bir istemcinin yüklemeye Etiketler eklemesini sağlamak için, önceki kalıbı kullanarak Etiketler ekleyen özel bir API oluşturmanız gerekir.

Bir örnek için App Service Mobile Apps tamamlanan hızlı başlangıç örneğinde [istemci tarafından eklenmiş anında iletme bildirimi etiketleri][5] bölümüne bakın.

## <a name="push-user"></a>Nasıl yapılır: kimliği doğrulanmış bir kullanıcıya anında iletme bildirimleri gönderme
Kimliği doğrulanmış bir Kullanıcı anında iletme bildirimlerini kaydettiğinde, kayda otomatik olarak bir kullanıcı KIMLIĞI etiketi eklenir. Bu etiketi kullanarak, bu kişi tarafından kaydedilen tüm cihazlara anında iletme bildirimleri gönderebilirsiniz. Aşağıdaki kod, isteği yapan kullanıcının SID 'sini alır ve bu kişiye ait her cihaz kaydına bir şablon anında iletme bildirimi gönderir:

    // Get the current user SID and create a tag for the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;
    string userTag = "_UserId:" + sid;

    // Build a dictionary for the template with the item message text.
    var notification = new Dictionary<string, string> { { "message", item.Text } };

    // Send a template notification to the user ID.
    await hub.SendTemplateNotificationAsync(notification, userTag);

Kimliği doğrulanmış bir istemciden anında iletme bildirimleri için kaydolurken, kayıt denenmeden önce kimlik doğrulamanın tamam olduğundan emin olun. Daha fazla bilgi için bkz. .NET arka ucu için App Service Mobile Apps tamamlanan hızlı başlangıç örneğinde [kullanıcılara gönderim][6] .

## <a name="how-to-debug-and-troubleshoot-the-net-server-sdk"></a>Nasıl yapılır: .NET Server SDK hata ayıklama ve sorunlarını giderme
Azure App Service, ASP.NET uygulamaları için çeşitli hata ayıklama ve sorun giderme teknikleri sağlar:

* [Azure App Service izleme](../app-service/web-sites-monitor.md)
* [Azure App Service tanılama günlüğünü etkinleştirme](../app-service/troubleshoot-diagnostic-logs.md)
* [Visual Studio 'da Azure App Service sorunlarını giderme](../app-service/troubleshoot-dotnet-visual-studio.md)

### <a name="logging"></a>Günlüğe kaydetme
Standart ASP.NET Trace yazma kullanarak App Service tanılama günlüklerine yazabilirsiniz. Günlüklere yazmadan önce, mobil uygulama arka ucunuzdaki tanılamayı etkinleştirmeniz gerekir.

Tanılamayı etkinleştirmek ve günlüklere yazmak için:

1. [Uygulama günlüğünü etkinleştirme (Windows)](../app-service/troubleshoot-diagnostic-logs.md#enable-application-logging-windows)bölümündeki adımları izleyin.
2. Aşağıdaki using ifadesini kod dosyanıza ekleyin:

        using System.Web.Http.Tracing;
3. .NET arka ucunu tanılama günlüklerine yazmak için aşağıdaki gibi bir izleme yazıcısı oluşturun:

        ITraceWriter traceWriter = this.Configuration.Services.GetTraceWriter();
        traceWriter.Info("Hello, World");
4. Sunucu projenizi yeniden yayımlayın ve kod yolunu günlüğe kaydetme ile yürütmek için mobil uygulama arka ucuna erişin.
5. [Erişim günlüğü dosyalarında](../app-service/troubleshoot-diagnostic-logs.md#access-log-files)açıklandığı gibi günlükleri indirip değerlendirin.

### <a name="local-debug"></a>Kimlik doğrulamasıyla yerel hata ayıklama
Değişiklikleri buluta yayımlamadan önce test etmek için uygulamanızı yerel olarak çalıştırabilirsiniz. Azure Mobile Apps arka uçlarının çoğunda, Visual Studio 'da *F5* tuşuna basın. Ancak, kimlik doğrulaması kullanılırken bazı ek hususlar vardır.

App Service kimlik doğrulaması/yetkilendirme yapılandırılmış bir bulut tabanlı mobil uygulamanıza sahip olmanız gerekir ve istemciniz, alternatif oturum açma Konağı olarak belirtilen bulut uç noktasına sahip olmalıdır. Gereken belirli adımlar için istemci platformunuzun belgelerine bakın.

Mobil arka ucunuzun [Microsoft. Azure. Mobile. Server. Authentication] 'ın yüklü olduğundan emin olun. Ardından, uygulamanızın OWıN başlangıç sınıfında `MobileAppConfiguration` `HttpConfiguration` ' e uygulandıktan sonra aşağıdakini ekleyin:

        app.UseAppServiceAuthentication(new AppServiceAuthenticationOptions()
        {
            SigningKey = ConfigurationManager.AppSettings["authSigningKey"],
            ValidAudiences = new[] { ConfigurationManager.AppSettings["authAudience"] },
            ValidIssuers = new[] { ConfigurationManager.AppSettings["authIssuer"] },
            TokenHandler = config.GetAppServiceTokenHandler()
        });

Yukarıdaki örnekte, HTTPS şemasını kullanarak Web. config dosyanızdaki *Authaudience* ve *authıssuer* uygulama ayarlarını uygulama köklerinizin URL 'si olacak şekilde yapılandırmanız gerekir. Benzer şekilde, *Authsigningkey* ' i uygulamanızın İmzalama anahtarının değeri olacak şekilde ayarlamanız gerekir.
İmzalama anahtarını almak için:

1. [Azure portalda] içinde uygulamanıza gidin
2. **Araçlar**, **kudu**, **Git**öğesine tıklayın.
3. Kudu yönetim sitesinde **ortam**' a tıklayın.
4. *@ No__t-1AUTH @ no__t-2SIGNING @ no__t-3KEY Web sitesi*için değeri bulur.

Yerel uygulama yapılandırma uygulamanızda *Authsigningkey* parametresi için imzalama anahtarını kullanın.  Mobil arka ucunuz, yerel olarak çalıştırıldığında belirteçleri doğrulamak için, istemcinin bulut tabanlı uç noktadan belirtecini alacağı anlamına gelir.

[1]: https://msdn.microsoft.com/library/azure/dn961176.aspx
[2]: https://github.com/Azure/azure-mobile-apps-net-server
[3]: app-service-mobile-ios-get-started.md
[4]: https://azure.microsoft.com/downloads/
[5]: https://github.com/Azure-Samples/app-service-mobile-dotnet-backend-quickstart/blob/master/README.md#client-added-push-notification-tags
[6]: https://github.com/Azure-Samples/app-service-mobile-dotnet-backend-quickstart/blob/master/README.md#push-to-users
[Azure portalda]: https://portal.azure.com
[NuGet.org]: https://www.nuget.org/
[Microsoft. Azure. Mobile. Server]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/
[Microsoft. Azure. Mobile. Server. QuickStart]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Quickstart/
[Microsoft. Azure. Mobile. Server. Authentication]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Authentication/
[Microsoft. Azure. Mobile. Server. Login]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Login/
[Microsoft. Azure. Mobile. Server. Notifications]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Notifications/
[MapHttpAttributeRoutes]: https://msdn.microsoft.com/library/dn479134(v=vs.118).aspx
