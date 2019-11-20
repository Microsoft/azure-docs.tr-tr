---
title: Cache ASP.NET oturum durumu sağlayıcısı
description: Redsıs için Azure önbelleğini kullanarak ASP.NET oturum durumunu nasıl depolayacağınızı öğrenin
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 05/01/2017
ms.author: yegu
ms.openlocfilehash: cfcad48060a3cf33da80c09c3900ce4322b947da
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74122830"
---
# <a name="aspnet-session-state-provider-for-azure-cache-for-redis"></a>Redsıs için Azure önbelleği ASP.NET oturum durumu sağlayıcısı

Redin için Azure Cache, oturum durumunu bir SQL Server veritabanı yerine Redto için Azure önbelleği ile bellekte depolamak için kullanabileceğiniz bir oturum durumu sağlayıcısı sağlar. Önbelleğe alma oturum durumu sağlayıcısını kullanmak için, önce önbelleğinizi yapılandırın ve ardından ASP.NET uygulamanızı, Redsıs oturum durumu NuGet paketi için Azure önbelleğini kullanarak önbellek için yapılandırın.

Bir Kullanıcı oturumu için bir durum biçiminin depolanmasını önlemek için genellikle gerçek dünyada bir bulut uygulamasında pratik değildir ancak bazı yaklaşımlar performansı ve ölçeklenebilirliği diğerlerinden daha fazla etkiler. Durumu depolamanız gerekirse, en iyi çözüm durum miktarını küçük tutmak ve tanımlama bilgilerinde depolamak olur. Bu uygun değilse, bir sonraki en iyi çözüm dağıtılmış, bellek içi önbellek için bir sağlayıcıyla ASP.NET oturum durumunu kullanmaktır. Performans ve ölçeklenebilirlik açısından en kötü çözüm, veritabanı tarafından desteklenen bir oturum durumu sağlayıcısı kullanmaktır. Bu konu, ASP.NET oturum durumu sağlayıcısını Redsıs için Azure önbelleği kullanma hakkında rehberlik sağlar. Diğer oturum durumu seçenekleri hakkında daha fazla bilgi için bkz. [ASP.NET oturum durumu seçenekleri](#aspnet-session-state-options).

## <a name="store-aspnet-session-state-in-the-cache"></a>Önbellekte ASP.NET oturumu durumu depolama

Visual Studio 'da Redsıs oturum durumu NuGet paketi için Azure önbelleğini kullanarak bir istemci uygulamasını yapılandırmak için, **Araçlar** menüsünden **NuGet Paket Yöneticisi**, **Paket Yöneticisi konsolu** ' na tıklayın.

`Package Manager Console` penceresinden aşağıdaki komutu çalıştırın.
    

```powershell
Install-Package Microsoft.Web.RedisSessionStateProvider
```

> [!IMPORTANT]
> Premium katmanda kümeleme özelliğini kullanıyorsanız, [Redissessionstateprovider](https://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider) 2.0.1 veya üstünü kullanmanız ya da bir özel durum oluşturulması gerekir. 2\.0.1 veya üzeri bir sürüme geçilmesi, bir son değişiklik değildir; daha fazla bilgi için bkz. [v 2.0.0 kıran değişiklik ayrıntıları](https://github.com/Azure/aspnet-redis-providers/wiki/v2.0.0-Breaking-Change-Details). Bu makalede güncelleştirme sırasında, bu paketin geçerli sürümü 2.2.3.
> 
> 

Redsıs oturum durumu sağlayıcısı NuGet paketinin StackExchange. Redsıs. StrongName paketine bağımlılığı vardır. StackExchange. redin. StrongName paketi projenizde yoksa, yüklenir.

>[!NOTE]
>Güçlü adlandırılmış StackExchange. Redsıs. StrongName paketine ek olarak, StackExchange. Red, tanımlayıcı olmayan adlı sürüm de vardır. Projeniz tanımlayıcı olmayan, StackExchange. Redsıs sürümünü kullanıyorsa, bunu kaldırmanız gerekir, aksi takdirde projenizde adlandırma çakışmalarını alırsınız. Bu paketler hakkında daha fazla bilgi için bkz. [.net önbellek Istemcilerini yapılandırma](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).
>
>

NuGet paketi, gerekli derleme başvurularını indirir ve ekler ve aşağıdaki bölümü Web. config dosyanıza ekler. Bu bölüm, ASP.NET uygulamanızın Redsıs oturum durumu sağlayıcısı için Azure önbelleğini kullanması için gereken yapılandırmayı içerir.

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
  <providers>
    <!-- Either use 'connectionString' OR 'settingsClassName' and 'settingsMethodName' OR use 'host','port','accessKey','ssl','connectionTimeoutInMilliseconds' and 'operationTimeoutInMilliseconds'. -->
    <!-- 'throwOnError','retryTimeoutInMilliseconds','databaseId' and 'applicationName' can be used with both options. -->
    <!--
      <add name="MySessionStateStore" 
        host = "127.0.0.1" [String]
        port = "" [number]
        accessKey = "" [String]
        ssl = "false" [true|false]
        throwOnError = "true" [true|false]
        retryTimeoutInMilliseconds = "5000" [number]
        databaseId = "0" [number]
        applicationName = "" [String]
        connectionTimeoutInMilliseconds = "5000" [number]
        operationTimeoutInMilliseconds = "1000" [number]
        connectionString = "<Valid StackExchange.Redis connection string>" [String]
        settingsClassName = "<Assembly qualified class name that contains settings method specified below. Which basically return 'connectionString' value>" [String]
        settingsMethodName = "<Settings method should be defined in settingsClass. It should be public, static, does not take any parameters and should have a return type of 'String', which is basically 'connectionString' value.>" [String]
        loggingClassName = "<Assembly qualified class name that contains logging method specified below>" [String]
        loggingMethodName = "<Logging method should be defined in loggingClass. It should be public, static, does not take any parameters and should have a return type of System.IO.TextWriter.>" [String]
        redisSerializerType = "<Assembly qualified class name that implements Microsoft.Web.Redis.ISerializer>" [String]
      />
    -->
    <add name="MySessionStateStore" type="Microsoft.Web.Redis.RedisSessionStateProvider"
         host=""
         accessKey=""
         ssl="true" />
  </providers>
</sessionState>
```

Açıklamalı bölüm, her bir özniteliğe yönelik özniteliklerin ve örnek ayarların bir örneğini sağlar.

Öznitelikleri, Microsoft Azure portal önbellek dikey pencerenize ait değerlerle yapılandırın ve diğer değerleri istediğiniz gibi yapılandırın. Önbellek özelliklerinizi erişme hakkında yönergeler için bkz. [redsıs ayarları Için Azure önbelleğini yapılandırma](cache-configure.md#configure-azure-cache-for-redis-settings).

* **ana bilgisayar** – önbellek uç noktanızı belirtin.
* **bağlantı noktası** : SSL ayarlarına bağlı olarak SSL olmayan bağlantı NOKTANıZıN veya SSL bağlantı noktanızın kullanın.
* **AccessKey** : önbelleğiniz için birincil ya da ikincil anahtar kullanın.
* **SSL** – önbellek/istemci iletişimlerini SSL ile güvenli hale getirmek istiyorsanız doğru; Aksi halde yanlış. Doğru bağlantı noktasını belirttiğinizden emin olun.
  * SSL olmayan bağlantı noktasın yeni önbellekler için varsayılan olarak devre dışı bırakılmıştır. Bu ayar için SSL bağlantı noktasını kullanmak üzere true değerini belirtin. SSL olmayan bağlantı noktasını etkinleştirme hakkında daha fazla bilgi için, [önbellek yapılandırma](cache-configure.md) konusunun [erişim bağlantı noktaları](cache-configure.md#access-ports) bölümüne bakın.
* **throwOnError** : bir hata oluşursa bir özel durumun oluşturulması için doğru veya işlemin sessizce başarısız olmasını istiyorsanız false. Statik Microsoft. Web. Redsıs. RedisSessionStateProvider. LastException özelliğini denetleyerek bir hata olup olmadığını kontrol edebilirsiniz. Varsayılan değer true 'dur.
* **Retrytimeoutınmilliseconds** – başarısız olan işlemler, milisaniye cinsinden belirtilen aralık sırasında yeniden denenir. İlk yeniden deneme 20 milisaniyelik sonra gerçekleşir ve yeniden denemeler, Retrytimeoutınmilliseconds aralığı sona erene kadar her saniye oluşur. Bu aralıktan hemen sonra işlem bir son kez yeniden denenir. İşlem yine başarısız olursa, throwOnError ayarına bağlı olarak, özel durum çağırana geri getirilir. Varsayılan değer 0 ' dır, bu da yeniden deneme anlamına gelir.
* **DatabaseID** : önbellek çıkış verileri için kullanılacak veritabanını belirtir. Belirtilmemişse, varsayılan 0 değeri kullanılır.
* **ApplicationName** – anahtarlar redsıs 'de `{<Application Name>_<Session ID>}_Data`olarak depolanır. Bu adlandırma şeması, birden fazla uygulamanın aynı Redsıs örneğini paylaşmasına olanak sağlar. Bu parametre isteğe bağlıdır ve bunu sağlamazsanız varsayılan bir değer kullanılır.
* **Connectiontimeoutınmilliseconds** – Bu ayar, StackExchange. redsıs Istemcisinde ConnectTimeout ayarını geçersiz kılmanızı sağlar. Belirtilmezse, varsayılan connectTimeout ayarı 5000 kullanılır. Daha fazla bilgi için bkz. [StackExchange. redsıs yapılandırma modeli](https://go.microsoft.com/fwlink/?LinkId=398705).
* **Operationtimeoutınmilliseconds** – Bu ayar, StackExchange. redsıs Istemcisinde synctimeout ayarını geçersiz kılmanızı sağlar. Belirtilmemişse, varsayılan syncTimeout ayarı 1000 kullanılır. Daha fazla bilgi için bkz. [StackExchange. redsıs yapılandırma modeli](https://go.microsoft.com/fwlink/?LinkId=398705).
* **Redisserializertype** -Bu ayar redsıs 'e gönderilen oturum içeriğinin özel serileştirmesini belirtmenize olanak tanır. Belirtilen tür `Microsoft.Web.Redis.ISerializer` gerçekleştirmelidir ve ortak parametresiz Oluşturucu bildirmelidir. Varsayılan olarak `System.Runtime.Serialization.Formatters.Binary.BinaryFormatter` kullanılır.

Bu özellikler hakkında daha fazla bilgi için bkz. [ASP.net for the The redsıs for](https://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx)The The The The The The The The The The The The

Web. config dosyanızın standart InProc oturum durumu sağlayıcısı bölümüne açıklama koymayı unutmayın.

```xml
<!-- <sessionState mode="InProc"
     customProvider="DefaultSessionProvider">
     <providers>
        <add name="DefaultSessionProvider"
              type="System.Web.Providers.DefaultSessionStateProvider,
                    System.Web.Providers, Version=1.0.0.0, Culture=neutral,
                    PublicKeyToken=31bf3856ad364e35"
              connectionStringName="DefaultConnection" />
      </providers>
</sessionState> -->
```

Bu adımlar gerçekleştirildikten sonra, uygulamanız Redsıs oturum durumu sağlayıcısı için Azure önbelleğini kullanacak şekilde yapılandırılır. Uygulamanızda oturum durumu kullandığınızda, Redsıs örneği için bir Azure önbelleğinde depolanır.

> [!IMPORTANT]
> Önbellekte depolanan verilerin, varsayılan bellek içi ASP.NET oturum durumu sağlayıcısında depolanabilecek verilerin aksine seri hale getirilebilir olması gerekir. Redin için oturum durumu sağlayıcısı kullanıldığında, oturum durumunda saklanan veri türlerinin seri hale getirilebilir olduğundan emin olun.
> 
> 

## <a name="aspnet-session-state-options"></a>ASP.NET oturum durumu seçenekleri

* Bellek içi oturum durumu sağlayıcısı-bu sağlayıcı oturum durumunu bellekte depolar. Bu sağlayıcıyı kullanmanın avantajı basit ve hızlıdır. Ancak, dağıtılmadığından bu yana bellek sağlayıcısında kullanıyorsanız Web Apps ölçeklendiremezsiniz.
* SQL Server oturum durumu sağlayıcısı-bu sağlayıcı oturum durumunu SQL Server 'da depolar. Oturum durumunu kalıcı depolamada depolamak istiyorsanız bu sağlayıcıyı kullanın. Web uygulamanızı ölçeklendirebilirsiniz, ancak SQL Server 'ın oturum için kullanılması, Web uygulamanızda bir performans etkisine sahiptir. Bu sağlayıcıyı, performansı artırmaya yardımcı olmak için [bellek ıçı OLTP yapılandırması](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2017/11/28/asp-net-session-state-with-sql-server-in-memory-oltp/) ile de kullanabilirsiniz.
* Redsıs oturum durumu sağlayıcısı için Azure önbelleği gibi bellek Içi oturum durumu sağlayıcısına dağıtılmış-bu sağlayıcı, her iki dünyanın da en iyi şekilde yararlanmanızı sağlar. Web uygulamanız basit, hızlı ve ölçeklenebilir bir oturum durumu sağlayıcısına sahip olabilir. Bu sağlayıcı oturum durumunu bir önbellekte depoladığından, geçici ağ arızalarına benzer şekilde, uygulamanızın bir dağıtılmış bellek önbelleğinde iletişim kurulurken ilişkili tüm özellikleri dikkate almanız gerekir. Önbellek kullanımı ile ilgili en iyi uygulamalar için bkz. Microsoft desenlerinden [önbelleğe alma kılavuzu](../best-practices-caching.md) & [Azure bulut uygulaması tasarımı ve uygulama kılavuzu](https://github.com/mspnp/azure-guidance).

Oturum durumu ve diğer en iyi uygulamalar hakkında daha fazla bilgi için bkz. [Web geliştirme En Iyi uygulamaları (Azure Ile gerçek bulut uygulamaları oluşturma)](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/web-development-best-practices).

## <a name="next-steps"></a>Sonraki adımlar

[Redu Için Azure önbelleği için ASP.net çıktı önbelleği sağlayıcısına](cache-aspnet-output-cache-provider.md)göz atın.
