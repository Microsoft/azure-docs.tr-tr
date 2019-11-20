---
title: Reda için Azure önbelleği ile bir Web uygulaması oluşturmaya yönelik öğretici, önbellek oluşturma deseninin kullanımı
description: Önbelleğe alma modelini kullanan Reda için Azure önbelleği ile bir Web uygulaması oluşturmayı öğrenin
author: yegu-ms
ms.service: cache
ms.topic: tutorial
ms.custom: mvc
ms.date: 03/30/2018
ms.author: yegu
ms.openlocfilehash: dbd8250da46e640aaa9403430ae0fbfaa9b181c8
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74121519"
---
# <a name="tutorial-create-a-cache-aside-leaderboard-on-aspnet"></a>Öğretici: ASP.NET üzerinde edilgen önbellekli puan tablosu oluşturma

Bu öğreticide, [redde için Azure önbelleği için ASP.net hızlı](cache-web-app-howto.md)başlangıcı ' nda oluşturulan *contosoteamstats* ASP.NET Web uygulamasını, redsıs için Azure Cache ile birlikte [önbellek modelini](https://docs.microsoft.com/azure/architecture/patterns/cache-aside) kullanan bir puan tablosu içerecek şekilde güncelleştecaksınız. Örnek uygulama bir veritabanındaki ekip istatistiklerinin listesini görüntüler ve performansı artırmak için önbellekten veri depolamak ve almak için Azure önbelleği 'ni kullanmak için farklı yollar gösterir. Öğreticiyi tamamladığınızda, bir veritabanını okuyan ve yazan, Redsıs için Azure önbelleğiyle en iyi duruma getirilmiş ve Azure 'da barındırılan, çalışan bir Web uygulamasına sahip olursunuz.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Redsıs için Azure önbelleği 'ni kullanarak verileri depolayarak ve alarak veri üretimini geliştirme ve veritabanı yükünü azaltma.
> * En iyi beş takımı almak için bir Redis sıralanmış kümesi kullanma.
> * Resource Manager şablonunu kullanarak uygulama için Azure kaynakları sağlama.
> * Visual Studio kullanarak uygulamayı yayımlama.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlamak için aşağıdaki ön koşullara sahip olmanız gerekir:

* Bu öğretici [, redin Için Azure önbelleği için ASP.net hızlı](cache-web-app-howto.md)başlangıcı ' nda kaldığınız yerden devam eder. Henüz yapmadıysanız önce hızlı başlangıcı izleyin.
* Aşağıdaki iş yükleriyle [Visual Studio 2019](https://www.visualstudio.com/downloads/) ' i yükledikten sonra:
    * ASP.NET ve web geliştirme
    * Azure Geliştirme
    * [SQL Server 2017 Express edition](https://www.microsoft.com/sql-server/sql-server-editions-express) veya SQL Server Express LocalDB ile .NET masaüstü geliştirmesi.

## <a name="add-a-leaderboard-to-the-project"></a>Projeye puan tablosu ekleme

Öğreticinin bu bölümünde, kurgusal takımlar listesi için galibiyet, mağlubiyet ve berabere kalma istatistiklerini bildiren bir puan tablosu ile *ContosoTeamStats* projesini yapılandırırsınız.

### <a name="add-the-entity-framework-to-the-project"></a>Projeye Entity Framework ekleme

1. Visual Studio 'da [redsıs Için Azure önbelleği için ASP.net hızlı başlangıç](cache-web-app-howto.md)bölümünde oluşturduğunuz *contosoteamstats* çözümünü açın.
2. **Araçlar > NuGet Paket Yöneticisi > Paket Yöneticisi Konsolu**’na tıklayın.
3. EntityFramework’ü yüklemek için **Paket Yöneticisi Konsolu** penceresinden aşağıdaki komutu çalıştırın:

    ```powershell
    Install-Package EntityFramework
    ```

Bu paket hakkında daha fazla bilgi için [EntityFramework](https://www.nuget.org/packages/EntityFramework/) NuGet sayfasına bakın.

### <a name="add-the-team-model"></a>Takım modeli ekleme

1. **Çözüm Gezgini**’nde **Modeller**’e sağ tıklayın ve **Ekle**, **Sınıf**’ı seçin.

1. Sınıf adı için `Team` girin ve **Ekle**’ye tıklayın.

    ![Model sınıfı ekleme](./media/cache-web-app-cache-aside-leaderboard/cache-model-add-class-dialog.png)

1. `using`Team.cs*dosyasının üst kısmındaki* deyimlerini aşağıdaki `using` deyimleriyle değiştirin:

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Data.Entity;
    using System.Data.Entity.SqlServer;
    ```

1. `Team` sınıfının tanımını, bazı diğer Entity Framework yardımcı sınıflarının yanı sıra güncelleştirilmiş `Team` sınıf tanımını içeren aşağıdaki kod parçacığı ile değiştirin. Bu öğreticide, Entity Framework ile code first yaklaşımı kullanılmaktadır. Bu yaklaşım, Entity Framework’ün kodunuzdan veritabanını oluşturmasını sağlar. Bu öğreticide kullanılan Entity Framework için ilk kod yaklaşımı hakkında daha fazla bilgi için, bkz. [Yeni bir veritabanına ilk kod](/ef/ef6/modeling/code-first/workflows/new-database).

    ```csharp
    public class Team
    {
        public int ID { get; set; }
        public string Name { get; set; }
        public int Wins { get; set; }
        public int Losses { get; set; }
        public int Ties { get; set; }

        static public void PlayGames(IEnumerable<Team> teams)
        {
            // Simple random generation of statistics.
            Random r = new Random();

            foreach (var t in teams)
            {
                t.Wins = r.Next(33);
                t.Losses = r.Next(33);
                t.Ties = r.Next(0, 5);
            }
        }
    }

    public class TeamContext : DbContext
    {
        public TeamContext()
            : base("TeamContext")
        {
        }

        public DbSet<Team> Teams { get; set; }
    }

    public class TeamInitializer : CreateDatabaseIfNotExists<TeamContext>
    {
        protected override void Seed(TeamContext context)
        {
            var teams = new List<Team>
            {
                new Team{Name="Adventure Works Cycles"},
                new Team{Name="Alpine Ski House"},
                new Team{Name="Blue Yonder Airlines"},
                new Team{Name="Coho Vineyard"},
                new Team{Name="Contoso, Ltd."},
                new Team{Name="Fabrikam, Inc."},
                new Team{Name="Lucerne Publishing"},
                new Team{Name="Northwind Traders"},
                new Team{Name="Consolidated Messenger"},
                new Team{Name="Fourth Coffee"},
                new Team{Name="Graphic Design Institute"},
                new Team{Name="Nod Publishers"}
            };

            Team.PlayGames(teams);

            teams.ForEach(t => context.Teams.Add(t));
            context.SaveChanges();
        }
    }

    public class TeamConfiguration : DbConfiguration
    {
        public TeamConfiguration()
        {
            SetExecutionStrategy("System.Data.SqlClient", () => new SqlAzureExecutionStrategy());
        }
    }
    ```

1. **Çözüm Gezgini**’nde, **Web.config**’i açmak için çift tıklayın.

    ![Web.config](./media/cache-web-app-cache-aside-leaderboard/cache-web-config.png)

1. Aşağıdaki `connectionStrings` bölümünü `configuration` bölümüne ekleyin. Bağlantı dizesinin adı, Entity Framework veritabanı bağlamı sınıfının adı olan `TeamContext` ile eşleşmelidir.

    Bu bağlantı dizesi [önkoşulları](#prerequisites) karşıladığınızı ve Visual Studio 2019 ile yüklenen *.net masaüstü geliştirme* iş yükünün bir parçası olan LocalDB SQL Server Express yüklediğinizi varsayar.

    ```xml
    <connectionStrings>
        <add name="TeamContext" connectionString="Data Source=(LocalDB)\MSSQLLocalDB;AttachDbFilename=|DataDirectory|\Teams.mdf;Integrated Security=True" providerName="System.Data.SqlClient" />
    </connectionStrings>
    ```

    Aşağıdaki örnek, `connectionStrings` bölümündeki `configSections` bölümünü izleyen yeni `configuration` bölümünü gösterir:

    ```xml
    <configuration>
        <configSections>
        <section name="entityFramework" type="System.Data.Entity.Internal.ConfigFile.EntityFrameworkSection, EntityFramework, Version=6.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" requirePermission="false" />
        </configSections>
        <connectionStrings>
        <add name="TeamContext" connectionString="Data Source=(LocalDB)\MSSQLLocalDB;AttachDbFilename=|DataDirectory|\Teams.mdf;Integrated Security=True"     providerName="System.Data.SqlClient" />
        </connectionStrings>
        ...
    ```

### <a name="add-the-teamscontroller-and-views"></a>TeamsController ve görünümleri ekleme

1. Visual Studio’da projeyi derleyin. 

1. **Çözüm Gezgini**'nde **Denetleyiciler** klasörüne sağ tıklayın ve **Ekle**, **Denetleyici**'yi seçin.

1. **Görünümlere sahip MVC 5 Denetleyici, Entity Framework kullanarak** öğesini seçin ve **Ekle**’ye tıklayın. **Ekle**’ye tıkladıktan sonra herhangi bir hata alırsanız, önce projeyi oluşturduğunuzdan emin olun.

    ![Denetleyici sınıfı ekleme](./media/cache-web-app-cache-aside-leaderboard/cache-add-controller-class.png)

1. **Model sınıfı** açılır listesinden **Ekip (ContosoTeamStats.Models)** öğesini seçin. **Veri bağlamı** açılır listesinden **TeamContext (ContosoTeamStats.Models)** öğesini seçin. `TeamsController`Denetleyici**adı metin kutusuna** yazın (otomatik olarak doldurulmamışsa). Denetleyici sınıfını oluşturmak ve varsayılan görünümleri eklemek için **Ekle**’ye tıklayın.

    ![Denetleyici yapılandırma](./media/cache-web-app-cache-aside-leaderboard/cache-configure-controller.png)

1. **Çözüm Gezgini**’nde, **Global.asax** öğesini genişletin ve **Global.asax.cs**’yi açmak için çift tıklayın.

    ![Global.asax.cs](./media/cache-web-app-cache-aside-leaderboard/cache-global-asax.png)

1. Aşağıdaki iki `using` deyimini dosyanın üst tarafındaki diğer `using` deyimlerinin altına ekleyin:

    ```csharp
    using System.Data.Entity;
    using ContosoTeamStats.Models;
    ```

1. `Application_Start` yönteminin sonuna aşağıdaki kod satırını ekleyin:

    ```csharp
    Database.SetInitializer<TeamContext>(new TeamInitializer());
    ```

1. **Çözüm Gezgini**’nde, `App_Start` öğesini genişletin ve `RouteConfig.cs` öğesine çift tıklayın.

    ![RouteConfig.cs](./media/cache-web-app-cache-aside-leaderboard/cache-RouteConfig-cs.png)

1. `RegisterRoutes` yönteminde, `controller = "Home"` rotasındaki `Default` öğesini, aşağıdaki örnekte gösterildiği gibi `controller = "Teams"` ile değiştirin:

    ```csharp
    routes.MapRoute(
        name: "Default",
        url: "{controller}/{action}/{id}",
        defaults: new { controller = "Teams", action = "Index", id = UrlParameter.Optional }
    );
    ```

### <a name="configure-the-layout-view"></a>Düzen görünümünü yapılandırma

1. **Çözüm Gezgini**’nde, **Görünümler** klasörünü ve ardından **Paylaşılan** klasörünü genişletin ve **_Layout.cshtml** öğesine çift tıklayın. 

    ![_Layout.cshtml](./media/cache-web-app-cache-aside-leaderboard/cache-layout-cshtml.png)

1. `title` öğesinin içeriğini değiştirin ve aşağıdaki örnekte gösterildiği gibi `My ASP.NET Application` öğesini `Contoso Team Stats` ile değiştirin:

    ```html
    <title>@ViewBag.Title - Contoso Team Stats</title>
    ```

1. `body` bölümünde, *Redsıs testi Için Azure Cache*bağlantısının hemen altındaki *contoso Team stats* için aşağıdaki yeni `Html.ActionLink` ifadesini ekleyin.

    ```csharp
    @Html.ActionLink("Contoso Team Stats", "Index", "Teams", new { area = "" }, new { @class = "navbar-brand" })`
    ```

    ![Kod değişiklikleri](./media/cache-web-app-cache-aside-leaderboard/cache-layout-cshtml-code.png)

1. Uygulamayı derleyip çalıştırmak için **Ctrl+F5**'e basın. Uygulamasının bu sürümü, sonuçları doğrudan veritabanından okur. **Yeni Oluştur**, **Düzenle**, **Ayrıntılar** ve **Sil** eylemlerinin **Görünümlere sahip MVC 5 Denetleyici, Entity Framework kullanarak** iskelesi tarafından otomatik olarak uygulamaya eklendiğini unutmayın. Öğreticinin sonraki bölümünde, veri erişimini iyileştirmek ve uygulamaya ek özellikler sağlamak için Redsıs için Azure önbelleği ekleyeceksiniz.

    ![Başlangıç uygulaması](./media/cache-web-app-cache-aside-leaderboard/cache-starter-application.png)

## <a name="configure-the-app-for-azure-cache-for-redis"></a>Redsıs için uygulamayı Azure önbelleği için yapılandırma

Öğreticinin bu bölümünde, [StackExchange. redsıs](https://github.com/StackExchange/StackExchange.Redis) Önbellek istemcisini kullanarak bir redin örneği Için Azure önbelleğinden contoso ekip istatistiklerini depolamak ve almak üzere örnek uygulamayı yapılandırırsınız.

### <a name="add-a-cache-connection-to-the-teams-controller"></a>Teams Controller’a önbellek bağlantısı ekleme

Hızlı başlangıçta *StackExchange.Redis* istemci kitaplığı paketini zaten yüklediniz. Ayrıca yayımlanan App Service ile ve yerel olarak kullanılmak üzere *CacheConnection* uygulama ayarını da yapılandırdınız. *TeamsController*’da bu aynı istemci kitaplığını ve *CacheConnection* bilgilerini kullanın.

1. **Çözüm Gezgini**’nde, **Denetleyiciler** klasörünü genişletin ve **TeamsController.cs** öğesini açmak için çift tıklayın.

    ![Ekip denetleyicisi](./media/cache-web-app-cache-aside-leaderboard/cache-teamscontroller.png)

1. `using`TeamsController.cs**deyimlerini kullanarak aşağıdaki iki** deyimini ekleyin:

    ```csharp
    using System.Configuration;
    using StackExchange.Redis;
    ```

1. Aşağıdaki iki özelliği `TeamsController` sınıfına ekleyin:

    ```csharp
    // Redis Connection string info
    private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
    {
        string cacheConnection = ConfigurationManager.AppSettings["CacheConnection"].ToString();
        return ConnectionMultiplexer.Connect(cacheConnection);
    });

    public static ConnectionMultiplexer Connection
    {
        get
        {
            return lazyConnection.Value;
        }
    }
    ```

### <a name="update-the-teamscontroller-to-read-from-the-cache-or-the-database"></a>Önbellekten veya veritabanından okumak için TeamsController’ı güncelleştirme

Bu örnekte, ekip istatistikleri veritabanı veya önbellekten alınabilir. Ekip istatistikleri seri hale getirilmiş bir `List<Team>` ve ayrıca, Redis veri türleri kullanılarak sıralanmış bir küme olarak veritabanında depolanır. Bir sıralanmış kümeden öğeleri alırken, belirli öğeler için bazı, tümü veya sorgu alabilirsiniz. Bu örnekte, galibiyet sayısına göre sıralanan en iyi 5 takım için sıralanmış kümeyi sorgulayacaksınız.

Redsıs için Azure önbelleğini kullanmak amacıyla, takım istatistiklerini önbellekte birden çok biçimde depolamak gerekli değildir. Bu öğretici, verileri önbelleğe almak için kullanabileceğiniz farklı yol ve farklı veri türlerinin bazılarını göstermek için birden çok biçim kullanır.

1. Aşağıdaki `using` deyimlerini `TeamsController.cs` dosyasının üst tarafındaki diğer `using` deyimleri ile değiştirin:

    ```csharp
    using System.Diagnostics;
    using Newtonsoft.Json;
    ```

1. Geçerli `public ActionResult Index()` yöntemi uygulamasını aşağıdaki uygulama ile değiştirin:

    ```csharp
    // GET: Teams
    public ActionResult Index(string actionType, string resultType)
    {
        List<Team> teams = null;

        switch(actionType)
        {
            case "playGames": // Play a new season of games.
                PlayGames();
                break;

            case "clearCache": // Clear the results from the cache.
                ClearCachedTeams();
                break;

            case "rebuildDB": // Rebuild the database with sample data.
                RebuildDB();
                break;
        }

        // Measure the time it takes to retrieve the results.
        Stopwatch sw = Stopwatch.StartNew();

        switch(resultType)
        {
            case "teamsSortedSet": // Retrieve teams from sorted set.
                teams = GetFromSortedSet();
                break;

            case "teamsSortedSetTop5": // Retrieve the top 5 teams from the sorted set.
                teams = GetFromSortedSetTop5();
                break;

            case "teamsList": // Retrieve teams from the cached List<Team>.
                teams = GetFromList();
                break;

            case "fromDB": // Retrieve results from the database.
            default:
                teams = GetFromDB();
                break;
        }

        sw.Stop();
        double ms = sw.ElapsedTicks / (Stopwatch.Frequency / (1000.0));

        // Add the elapsed time of the operation to the ViewBag.msg.
        ViewBag.msg += " MS: " + ms.ToString();

        return View(teams);
    }
    ```

1. Önceki kod parçacığında eklenen switch deyiminden `TeamsController`, `playGames` ve `clearCache` eylem türlerini uygulamak için aşağıdaki üç yöntemi `rebuildDB` sınıfına ekleyin.

    `PlayGames` yöntemi, oyun sezonunu taklit ederek ekip istatistiklerini güncelleştirir, sonuçları veritabanına kaydeder ve artık güncel olmayan verileri veritabanından temizler.

    ```csharp
    void PlayGames()
    {
        ViewBag.msg += "Updating team statistics. ";
        // Play a "season" of games.
        var teams = from t in db.Teams
                    select t;

        Team.PlayGames(teams);

        db.SaveChanges();

        // Clear any cached results
        ClearCachedTeams();
    }
    ```

    `RebuildDB` yöntemi, varsayılan ekip kümesine sahip veritabanını yeniden başlatır, bunlar için istatistikler oluşturur ve artık güncel olmayan verileri veritabanından temizler.

    ```csharp
    void RebuildDB()
    {
        ViewBag.msg += "Rebuilding DB. ";
        // Delete and re-initialize the database with sample data.
        db.Database.Delete();
        db.Database.Initialize(true);

        // Clear any cached results
        ClearCachedTeams();
    }
    ```

    `ClearCachedTeams` yöntemi önbelleğe alınan tüm ekip istatistiklerini önbellekten kaldırır.

    ```csharp
    void ClearCachedTeams()
    {
        IDatabase cache = Connection.GetDatabase();
        cache.KeyDelete("teamsList");
        cache.KeyDelete("teamsSortedSet");
        ViewBag.msg += "Team data removed from cache. ";
    }
    ```

1. Önbellek ve veritabanından ekip istatistiklerini almanın çeşitli yollarını uygulamak için aşağıdaki dört yöntemi `TeamsController` sınıfına ekleyin. Bu yöntemlerin her biri, daha sonra görünüm tarafından görüntülenen bir `List<Team>` döndürür.

    `GetFromDB` yöntemi veritabanından ekip istatistiklerini okur.
    ```csharp
    List<Team> GetFromDB()
    {
        ViewBag.msg += "Results read from DB. ";
        var results = from t in db.Teams
            orderby t.Wins descending
            select t;

        return results.ToList<Team>();
    }
    ```

    `GetFromList` yöntemi önbellekteki ekip istatistiklerini seri hale getirilmiş bir `List<Team>` olarak okur. Önbellekte istatistikler mevcut değilse, bir önbellek isabetsizliği oluşur. Önbellek isabetsizliği için, veritabanından takım istatistikleri okunur ve sonraki istek için önbellekte depolanır. Bu örnekte, önbelleğe veya önbellekten .NET nesnelerini seri hale getirmek için JSON.NET seri hale getirme kullanılmaktadır. Daha fazla bilgi için bkz. [redsıs Için Azure önbelleğinde .NET nesneleriyle çalışma](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache).

    ```csharp
    List<Team> GetFromList()
    {
        List<Team> teams = null;

        IDatabase cache = Connection.GetDatabase();
        string serializedTeams = cache.StringGet("teamsList");
        if (!String.IsNullOrEmpty(serializedTeams))
        {
            teams = JsonConvert.DeserializeObject<List<Team>>(serializedTeams);

            ViewBag.msg += "List read from cache. ";
        }
        else
        {
            ViewBag.msg += "Teams list cache miss. ";
            // Get from database and store in cache
            teams = GetFromDB();

            ViewBag.msg += "Storing results to cache. ";
            cache.StringSet("teamsList", JsonConvert.SerializeObject(teams));
        }
        return teams;
    }
    ```

    `GetFromSortedSet` yöntemi önbelleğe alınan bir sıralanmış kümeden ekip istatistiklerini okur. Önbellek isabetsizliği varsa, ekip istatistikleri veritabanından okunur ve ardından bir sıralanmış küme olarak önbellekte depolanır.

    ```csharp
    List<Team> GetFromSortedSet()
    {
        List<Team> teams = null;
        IDatabase cache = Connection.GetDatabase();
        // If the key teamsSortedSet is not present, this method returns a 0 length collection.
        var teamsSortedSet = cache.SortedSetRangeByRankWithScores("teamsSortedSet", order: Order.Descending);
        if (teamsSortedSet.Count() > 0)
        {
            ViewBag.msg += "Reading sorted set from cache. ";
            teams = new List<Team>();
            foreach (var t in teamsSortedSet)
            {
                Team tt = JsonConvert.DeserializeObject<Team>(t.Element);
                teams.Add(tt);
            }
        }
        else
        {
            ViewBag.msg += "Teams sorted set cache miss. ";

            // Read from DB
            teams = GetFromDB();

            ViewBag.msg += "Storing results to cache. ";
            foreach (var t in teams)
            {
                Console.WriteLine("Adding to sorted set: {0} - {1}", t.Name, t.Wins);
                cache.SortedSetAdd("teamsSortedSet", JsonConvert.SerializeObject(t), t.Wins);
            }
        }
        return teams;
    }
    ```

    `GetFromSortedSetTop5` yöntemi, önbelleğe alınan sıralanmış kümeden en iyi 5 takımı okur. Bu, `teamsSortedSet` anahtarının varlığı için önbelleği denetleyerek başlar. Bu anahtar yoksa, ekip istatistikleri okumak ve bunları önbellekte depolamak için `GetFromSortedSet` yöntemi çağrılır. Daha sonra önbelleğe alınan sıralanmış küme, döndürülen en iyi beş takım için sorgulanır.

    ```csharp
    List<Team> GetFromSortedSetTop5()
    {
        List<Team> teams = null;
        IDatabase cache = Connection.GetDatabase();

        // If the key teamsSortedSet is not present, this method returns a 0 length collection.
        var teamsSortedSet = cache.SortedSetRangeByRankWithScores("teamsSortedSet", stop: 4, order: Order.Descending);
        if(teamsSortedSet.Count() == 0)
        {
            // Load the entire sorted set into the cache.
            GetFromSortedSet();

            // Retrieve the top 5 teams.
            teamsSortedSet = cache.SortedSetRangeByRankWithScores("teamsSortedSet", stop: 4, order: Order.Descending);
        }

        ViewBag.msg += "Retrieving top 5 teams from cache. ";
        // Get the top 5 teams from the sorted set
        teams = new List<Team>();
        foreach (var team in teamsSortedSet)
        {
            teams.Add(JsonConvert.DeserializeObject<Team>(team.Element));
        }
        return teams;
    }
    ```

### <a name="update-the-create-edit-and-delete-methods-to-work-with-the-cache"></a>Önbellek ile çalışacak şekilde Oluştur, Düzenle ve Sil yöntemlerini güncelleştirme

Bu örneğin bir parçası olarak oluşturulan iskele kurma kodu ekip ekleme, düzenleme ve silme yöntemlerini içerir. Bir ekip her eklendiğinde, düzenlendiğinde veya kaldırıldığında önbellekteki veriler güncel olmayan hale gelir. Bu bölümde, önbelleğin yenilenmesi için önbelleğe alınan takımları temizlemek üzere bu üç yöntemi değiştireceksiniz.

1. `Create(Team team)` sınıfındaki `TeamsController` yöntemine göz atın. Aşağıdaki örnekte gösterildiği gibi `ClearCachedTeams` yöntemine bir çağrı ekleyin:

    ```csharp
    // POST: Teams/Create
    // To protect from overposting attacks, please enable the specific properties you want to bind to, for 
    // more details see https://go.microsoft.com/fwlink/?LinkId=317598.
    [HttpPost]
    [ValidateAntiForgeryToken]
    public ActionResult Create([Bind(Include = "ID,Name,Wins,Losses,Ties")] Team team)
    {
        if (ModelState.IsValid)
        {
            db.Teams.Add(team);
            db.SaveChanges();
            // When a team is added, the cache is out of date.
            // Clear the cached teams.
            ClearCachedTeams();
            return RedirectToAction("Index");
        }

        return View(team);
    }
    ```

2. `Edit(Team team)` sınıfındaki `TeamsController` yöntemine göz atın. Aşağıdaki örnekte gösterildiği gibi `ClearCachedTeams` yöntemine bir çağrı ekleyin:

    ```csharp
    // POST: Teams/Edit/5
    // To protect from overposting attacks, please enable the specific properties you want to bind to, for 
    // more details see https://go.microsoft.com/fwlink/?LinkId=317598.
    [HttpPost]
    [ValidateAntiForgeryToken]
    public ActionResult Edit([Bind(Include = "ID,Name,Wins,Losses,Ties")] Team team)
    {
        if (ModelState.IsValid)
        {
            db.Entry(team).State = EntityState.Modified;
            db.SaveChanges();
            // When a team is edited, the cache is out of date.
            // Clear the cached teams.
            ClearCachedTeams();
            return RedirectToAction("Index");
        }
        return View(team);
    }
    ```

3. `DeleteConfirmed(int id)` sınıfındaki `TeamsController` yöntemine göz atın. Aşağıdaki örnekte gösterildiği gibi `ClearCachedTeams` yöntemine bir çağrı ekleyin:

    ```csharp
    // POST: Teams/Delete/5
    [HttpPost, ActionName("Delete")]
    [ValidateAntiForgeryToken]
    public ActionResult DeleteConfirmed(int id)
    {
        Team team = db.Teams.Find(id);
        db.Teams.Remove(team);
        db.SaveChanges();
        // When a team is deleted, the cache is out of date.
        // Clear the cached teams.
        ClearCachedTeams();
        return RedirectToAction("Index");
    }
    ```

### <a name="add-caching-methods-to-the-teams-index-view"></a>Takımlar Dizini görünümüne önbelleğe alma yöntemleri ekleme

1. **Çözüm Gezgini**’nde, **Görünümler** klasörünü ve ardından **Ekipler** klasörünü genişletin ve **Index.cshtml** öğesine çift tıklayın.

    ![Index.cshtml](./media/cache-web-app-cache-aside-leaderboard/cache-views-teams-index-cshtml.png)

1. Dosyanın en üstüne yakın bir yerde, aşağıdaki paragraf öğesini arayın:

    ![Eylem tablosu](./media/cache-web-app-cache-aside-leaderboard/cache-teams-index-table.png)

    Bu bağlantı yeni bir takım oluşturur. Paragraf öğesini aşağıdaki tablo ile değiştirin. Bu tabloda yeni bir ekip oluşturmak, yeni bir oyun sezonu oynama, önbelleği temizleme, önbellekten ekipleri çeşitli biçimlerde alma, veritabanından ekipleri alma ve yeni örnek veriler ile veritabanını yeniden oluşturma eylemlerinin bağlantılarını içermektedir.

    ```html
    <table class="table">
        <tr>
            <td>
                @Html.ActionLink("Create New", "Create")
            </td>
            <td>
                @Html.ActionLink("Play Season", "Index", new { actionType = "playGames" })
            </td>
            <td>
                @Html.ActionLink("Clear Cache", "Index", new { actionType = "clearCache" })
            </td>
            <td>
                @Html.ActionLink("List from Cache", "Index", new { resultType = "teamsList" })
            </td>
            <td>
                @Html.ActionLink("Sorted Set from Cache", "Index", new { resultType = "teamsSortedSet" })
            </td>
            <td>
                @Html.ActionLink("Top 5 Teams from Cache", "Index", new { resultType = "teamsSortedSetTop5" })
            </td>
            <td>
                @Html.ActionLink("Load from DB", "Index", new { resultType = "fromDB" })
            </td>
            <td>
                @Html.ActionLink("Rebuild DB", "Index", new { actionType = "rebuildDB" })
            </td>
        </tr>
    </table>
    ```

1. **Index.cshtml** dosyasının aşağısına kaydırın ve dosyada bulunan son tablodaki son satır olması için aşağıdaki `tr` öğesini ekleyin:

    ```html
    <tr><td colspan="5">@ViewBag.Msg</td></tr>
    ```
    Bu satırda, geçerli işlem hakkında durum raporu içeren `ViewBag.Msg` değerini gösterir. Önceki adımdan herhangi bir eylem bağlantısına tıkladığınızda `ViewBag.Msg` değeri ayarlanır.

    ![Durum iletisi](./media/cache-web-app-cache-aside-leaderboard/cache-status-message.png)

1. Projeyi derlemek için **F6**’ya basın.

## <a name="run-the-app-locally"></a>Uygulamayı yerel olarak çalıştırma

Takımları desteklemek için eklenmiş olan işlevselliği doğrulamak üzere makinenizde yerel olarak uygulamayı çalıştırın.

Bu testte uygulama ve veritabanının her ikisi de yerel olarak çalışmaktadır. Ancak, redde Azure önbelleği Azure 'da uzaktan barındırılır. Bu nedenle önbellek büyük olasılıkla veritabanı performansının biraz gerisinde kalır. En iyi performansı elde etmek için, istemci uygulaması ve Redsıs örneği için Azure önbelleği aynı konumda olmalıdır. Sonraki bölümde, önbellek kullanımından elde edilen yüksek performansı görmek için tüm kaynakları Azure’a dağıtacaksınız.

Uygulamayı yerel olarak çalıştırmak için:

1. Uygulamayı çalıştırmak için **Ctrl+F5**'e basın.

    ![Yerel olarak çalıştırılan uygulama](./media/cache-web-app-cache-aside-leaderboard/cache-local-application.png)

1. Görünüme eklenen yeni yöntemlerin her birini test edin. Bu testlerde önbellek uzak olduğundan veritabanı, önbellek performansının üzerine çıkmalıdır.

## <a name="publish-and-run-in-azure"></a>Azure’da yayımlama ve çalıştırma

### <a name="provision-a-sql-azure-database-for-the-app"></a>Uygulama için bir SQL Azure veritabanı sağlama

Bu bölümde, Azure’da barındırılan, kullanılacak uygulama için yeni bir SQL Azure veritabanı sağlayacaksınız.

1. [Azure portalında](https://portal.azure.com/), Azure portalının sol üst köşesindeki **Kaynak oluştur**’a tıklayın.

1. **Yeni** sayfasında **Veritabanları** > **SQL Veritabanı** seçeneklerine tıklayın.

1. Yeni SQL Veritabanı için aşağıdaki ayarları kullanın:

   | Ayar       | Önerilen değer | Açıklama |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Veritabanı adı** | *ContosoTeamsDatabase* | Geçerli veritabanı adları için bkz. [Veritabanı Tanımlayıcıları](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers). |
   | **Abonelik** | *Aboneliğiniz*  | Önbelleği oluşturmak ve App Service’i barındırmak için kullandığınız aynı aboneliği seçin. |
   | **Kaynak grubu**  | *TestResourceGroup* | **Var olanı kullan**’a tıklayın ve önbelleğinizi ve App Service’i yerleştirdiğiniz aynı kaynak grubunu kullanın. |
   | **Kaynak seçme** | **Boş veritabanı** | Boş bir veritabanıyla başlayın. |

1. **Sunucu** bölümünde, **Gerekli ayarları yapılandır** > **Yeni sunucu oluştur** seçeneklerine tıklayın ve aşağıdaki bilgileri girip **Seç** düğmesine tıklayın:

   | Ayar       | Önerilen değer | Açıklama |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Sunucu adı** | Genel olarak benzersiz bir ad | Geçerli sunucu adları için bkz. [Adlandırma kuralları ve kısıtlamalar](/azure/architecture/best-practices/resource-naming). |
   | **Sunucu yöneticisi oturum açma bilgileri** | Geçerli bir ad | Geçerli oturum açma adları için bkz. [Veritabanı Tanımlayıcıları](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers). |
   | **Parola** | Geçerli bir parola | Parolanızda en az 8 karakter bulunmalı ve parolanız şu üç kategoriden karakterler içermelidir: büyük harf karakterler, küçük harf karakterler, sayılar ve alfasayısal olmayan karakterler. |
   | **Konum** | *Doğu ABD* | Önbelleği ve App Service’i oluşturduğunuz aynı bölgeyi seçin. |

1. **Panoya sabitle**’ye tıklayın ve sonra **Oluştur**’a tıklayarak yeni veritabanını ve sunucuyu oluşturun.

1. Yeni veritabanı oluşturulduktan sonra **Veritabanı bağlantı dizelerini göster**’e tıklayın ve **ADO.NET** bağlantı dizesini kopyalayın.

    ![Bağlantı dizelerini göster](./media/cache-web-app-cache-aside-leaderboard/cache-show-connection-strings.png)

1. Azure portalında App Service’inize gidip **Uygulama Ayarları**’na tıklayın ve sonra Bağlantı dizeleri bölümünden **Yeni bağlantı dizesi ekle**’ye tıklayın.

1. Entity Framework veritabanı bağlam sınıfıyla eşleşecek şekilde *TeamContext* adlı yeni bir bağlantı dizesi ekleyin. Yeni veritabanınız için bağlantı dizesini değer olarak yapıştırın. Bağlantı dizesinde aşağıdaki yer tutucuları değiştirdiğinizden emin olup **Kaydet**’e tıklayın:

    | Yer tutucu | Önerilen değer |
    | --- | --- |
    | *{your_username}* | Yeni oluşturduğunuz veritabanı sunucusu için **sunucu yöneticisi oturum açma bilgilerini** kullanın. |
    | *{your_password}* | Yeni oluşturduğunuz veritabanı sunucusu için parolayı kullanın. |

    Uygulama Ayarı olarak kullanıcı adını ve parolayı eklediğinizde kullanıcı adınız ve parolanız kodunuza dahil edilmez. Bu yaklaşım, bu kimlik bilgilerinin korunmasına yardımcı olur.

### <a name="publish-the-application-updates-to-azure"></a>Uygulama güncelleştirmelerini Azure’da yayımlama

Öğreticinin bu adımında, uygulamayı bulutta çalıştırmak üzere uygulama güncelleştirmelerini Azure’da yayımlayacaksınız.

1. Visual Studio’da **ContosoTeamStats** öğesine sağ tıklayın ve **Yayımla**’yı seçin.

    ![Yayımlama](./media/cache-web-app-cache-aside-leaderboard/cache-publish-app.png)

2. **Yayımla**’ya tıklayarak, hızlı başlangıçta oluşturduğunuz aynı yayımlama profilini kullanın.

3. Yayımlama tamamlandıktan sonra Visual Studio, uygulamayı varsayılan web tarayıcınızda başlatır.

    ![Önbellek eklendi](./media/cache-web-app-cache-aside-leaderboard/cache-added-to-application.png)

    Aşağıdaki tablo örnek uygulamadaki her eylem bağlantısını açıklar:

    | Eylem | Açıklama |
    | --- | --- |
    | Yeni Oluştur |Yeni bir Ekip oluşturun. |
    | Sezonu Oynat |Oyun sezonunu oynatın, ekip istatistiklerini güncelleştirin ve veritabanından tüm güncel olmayan ekip verilerini temizleyin. |
    | Önbelleği Temizle |Önbellekten ekip istatistiklerini temizleyin. |
    | Önbellekten Liste |Önbellekten ekip istatistiklerini alın. Önbellek isabetsizliği varsa, veritabanından istatistikleri yükleyin ve bir sonraki seferde önbelleğe kaydedin. |
    | Önbellekten Sıralanmış Küme |Bir sıralanmış küme kullanarak önbellekten en iyi istatistiklerini alın. Önbellek isabetsizliği varsa, veritabanından istatistikleri yükleyin ve bir sıralanmış küme kullanarak önbelleğe kaydedin. |
    | Önbellekteki En İyi 5 Ekip |Bir sıralanmış küme kullanarak önbellekten en iyi 5 ekibi alın. Önbellek isabetsizliği varsa, veritabanından istatistikleri yükleyin ve bir sıralanmış küme kullanarak önbelleğe kaydedin. |
    | DB’den yükleme |Veritabanından ekip istatistiklerini alın. |
    | DB Yeniden Oluşturma |Veritabanını yeniden oluşturun ve örnek ekip verileri ile yeniden yükleyin. |
    | Düzenle / Ayrıntılar / Sil |Bir ekibi düzenleyin, ekibin ayrıntılarını görüntüleyin, ekibi silin. |

Eylemlerden bazılarına tıklayın ve farklı kaynaklardan veri alma denemeleri yapın. Veritabanı ve önbellekten veri almanın çeşitli yollarını tamamlamak için gereken süre arasındaki farklılıklara dikkat edin.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Örnek öğretici uygulamasıyla işiniz bittiğinde, maliyet ve kaynakları korumak için kullanılan Azure kaynaklarını silebilirsiniz. Tüm kaynaklarınız, aynı kaynak grubunda bulunmalıdır; kaynak grubunu silerek bunları birlikte tek bir işlemde silebilirsiniz. Bu konudaki yönergelerde *TestResources* adlı bir kaynak grubu kullanılmıştır.

> [!IMPORTANT]
> Bir kaynak grubunu silme işlemi geri alınamaz ve kaynak grubunun ve içindeki tüm kaynaklar kalıcı olarak silinir. Yanlış kaynak grubunu veya kaynakları yanlışlıkla silmediğinizden emin olun. Bu örneği, tutmak istediğiniz kaynakları içeren mevcut bir kaynak grubunda barındırmak için kaynaklar oluşturduysanız, her kaynağı kendi ilgili dikey penceresinden tek tek silebilirsiniz.
>

1. [Azure portalında](https://portal.azure.com) oturum açın ve **Kaynak grupları**’na tıklayın.
2. **Öğeleri filtrele...** metin kutusuna kaynak grubunuzun adını yazın.
3. Kaynak grubunuzun sağındaki **...** öğesine tıklayın ve **Kaynak grubunu sil** seçeneğine tıklayın.

    ![Sil](./media/cache-web-app-cache-aside-leaderboard/cache-delete-resource-group.png)

4. Kaynak grubunun silinmesini onaylamanız istenir. Onaylamak için kaynak grubunuzun adını yazın ve **Sil**’e tıklayın.

    Birkaç dakika sonra kaynak grubu ve içerdiği kaynakların tümü silinir.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Redsıs için Azure önbelleğini ölçeklendirme](./cache-how-to-scale.md)
