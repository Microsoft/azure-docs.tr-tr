---
title: Redsıs için Azure önbelleği için ASP.NET çıkış önbelleği sağlayıcısı
description: Redsıs için Azure önbelleğini kullanarak ASP.NET sayfa çıkışını önbelleğe alma hakkında bilgi edinin
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 04/22/2018
ms.author: yegu
ms.openlocfilehash: 5d7099779f330bc0a92f0c8f305ac534ab385119
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74122468"
---
# <a name="aspnet-output-cache-provider-for-azure-cache-for-redis"></a>Redsıs için Azure önbelleği için ASP.NET çıkış önbelleği sağlayıcısı

Redsıs çıkış önbelleği sağlayıcısı, çıkış önbelleği verileri için işlem dışı bir depolama mekanizmasıdır. Bu veriler özellikle tam HTTP yanıtları (sayfa çıktısı önbelleği) içindir. Sağlayıcı, ASP.NET 4 ' te tanıtılan yeni çıktı önbelleği sağlayıcısı genişletilebilirlik noktasına takılır.

Redsıs çıkış önbelleği sağlayıcısını kullanmak için, önce önbelleğinizi yapılandırın ve ardından ASP.NET uygulamanızı Redsıs çıkış önbelleği sağlayıcısı NuGet paketini kullanarak yapılandırın. Bu konu, uygulamanızın Redsıs çıkış önbelleği sağlayıcısını kullanmak üzere yapılandırılmasına ilişkin yönergeler sağlar. Redsıs örneği için Azure önbelleği oluşturma ve yapılandırma hakkında daha fazla bilgi için bkz. [önbellek oluşturma](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache).

## <a name="store-aspnet-page-output-in-the-cache"></a>ASP.NET sayfa çıkışını önbellekte depola

Visual Studio 'da Redsıs oturum durumu NuGet paketi için Azure önbelleğini kullanarak bir istemci uygulamasını yapılandırmak için, **Araçlar** menüsünden **NuGet Paket Yöneticisi**, **Paket Yöneticisi konsolu** ' na tıklayın.

`Package Manager Console` penceresinden aşağıdaki komutu çalıştırın.

```
Install-Package Microsoft.Web.RedisOutputCacheProvider
```

Redsıs çıkış önbelleği sağlayıcısı NuGet paketinin StackExchange. Redsıs. StrongName paketine bağımlılığı vardır. StackExchange. redin. StrongName paketi projenizde yoksa, yüklenir. Redsıs çıkış önbelleği sağlayıcısı NuGet paketi hakkında daha fazla bilgi için [Redisoutputcacheprovider](https://www.nuget.org/packages/Microsoft.Web.RedisOutputCacheProvider/) NuGet sayfasına bakın.

>[!NOTE]
>Güçlü adlandırılmış StackExchange. Redsıs. StrongName paketine ek olarak, StackExchange. Red, tanımlayıcı olmayan adlı sürüm de vardır. Projeniz tanımlayıcı olmayan, StackExchange. Redsıs sürümünü kullanıyorsa, kaldırmanız gerekir; Aksi takdirde, projenizde adlandırma çakışmalarıyla karşılaşmanız gerekir. Bu paketler hakkında daha fazla bilgi için bkz. [.net önbellek Istemcilerini yapılandırma](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).

NuGet paketi, gerekli derleme başvurularını indirir ve ekler ve aşağıdaki bölümü Web. config dosyanıza ekler. Bu bölüm, ASP.NET uygulamanızın Redsıs çıkış önbelleği sağlayıcısını kullanması için gereken yapılandırmayı içerir.

```xml
<caching>
  <outputCache defaultProvider="MyRedisOutputCache">
    <providers>
      <add name="MyRedisOutputCache" type="Microsoft.Web.Redis.RedisOutputCacheProvider"
           host=""
           accessKey=""
           ssl="true" />
    </providers>
  </outputCache>
</caching>
```

Öznitelikleri, Microsoft Azure portal önbellek dikey pencerenize ait değerlerle yapılandırın ve diğer değerleri istediğiniz gibi yapılandırın. Önbellek özelliklerinizi erişme hakkında yönergeler için bkz. [redsıs ayarları Için Azure önbelleğini yapılandırma](cache-configure.md#configure-azure-cache-for-redis-settings).

| Öznitelik | Tür | Varsayılan | Açıklama |
| --------- | ---- | ------- | ----------- |
| *konağının* | string | e | Redsıs sunucu IP adresi veya ana bilgisayar adı |
| *bağ* | pozitif tamsayı | 6379 (SSL olmayan)<br/>6380 (SSL) | Redsıs sunucu bağlantı noktası |
| *accessKey* | string | "" | Redsıs yetkilendirmesi etkinken redsıs sunucu parolası. Değer varsayılan olarak boş dizedir. Bu, oturum durumu sağlayıcısının Redsıs sunucusuna bağlanırken herhangi bir parolayı kullanmayacağı anlamına gelir. **Redsıs sunucunuz Azure Redis Cache gibi genel olarak erişilebilen bir ağda ise, güvenliği artırmak için Redsıs yetkilendirmesini etkinleştirdiğinizden emin olun ve güvenli bir parola sağlayın.** |
| *SSL* | boole | **yanlýþ** | SSL aracılığıyla Redsıs sunucusuna bağlanıp bağlanmayacağı. Redsıs, kutudan çıkış dışı SSL 'yi desteklemediğinden bu değer varsayılan olarak **false 'tur** . **SSL 'yi destekleyen Azure Redis Cache kullanıyorsanız, güvenliği artırmak için bunu true olarak ayarladığınızdan emin olun.**<br/><br/>SSL olmayan bağlantı noktasın yeni önbellekler için varsayılan olarak devre dışı bırakılmıştır. Bu ayar için SSL bağlantı noktasını kullanmak üzere **true değerini** belirtin. SSL olmayan bağlantı noktasını etkinleştirme hakkında daha fazla bilgi için, [önbellek yapılandırma](cache-configure.md) konusunun [erişim bağlantı noktaları](cache-configure.md#access-ports) bölümüne bakın. |
| *Databaseıdnumber* | pozitif tamsayı | 0 | *Bu öznitelik yalnızca, Web. config veya AppSettings aracılığıyla belirtilebilir.*<br/><br/>Kullanılacak redne veritabanını belirtin. |
| *Connectiontimeoutınmilliseconds* | pozitif tamsayı | StackExchange. Redsıs tarafından sağlanır | StackExchange. Redsıs. Connectionçoğullayıcı oluşturulurken *ConnectTimeout* ayarlamak için kullanılır. |
| *Operationtimeoutınmilliseconds* | pozitif tamsayı | StackExchange. Redsıs tarafından sağlanır | StackExchange. Redsıs. Connectionçoğullayıcı oluştururken *Synctimeout* ayarlamak için kullanılır. |
| *ConnectionString* (geçerli StackExchange. redsıs bağlantı dizesi) | string | *yok* | AppSettings veya Web. config için bir parametre başvurusu ya da geçerli bir StackExchange. Redsıs bağlantı dizesi. Bu öznitelik *ana bilgisayar*, *bağlantı noktası*, *AccessKey*, *SSL*ve diğer StackExchange. redsıs öznitelikleri için değerler sağlayabilir. *ConnectionString*'e daha yakından bakmak Için, [öznitelik notları](#attribute-notes) bölümünde [ConnectionString ayarlama](#setting-connectionstring) bölümüne bakın. |
| *settingsClassName*<br/>*settingsMethodName* | string<br/>string | *yok* | *Bu öznitelikler yalnızca, Web. config veya AppSettings aracılığıyla belirtilebilir.*<br/><br/>Bir bağlantı dizesi sağlamak için bu öznitelikleri kullanın. *Settingsclassname* , *settingsmethodname*tarafından belirtilen metodu içeren derleme nitelikli sınıf adı olmalıdır.<br/><br/>*Settingsmethodname* tarafından belirtilen yöntem public, static ve void (herhangi bir parametre almaz) olmalıdır ve bu da **dize**dönüş türü ile. Bu yöntem, gerçek bağlantı dizesini döndürür. |
| *loggingClassName*<br/>*loggingMethodName* | string<br/>string | *yok* | *Bu öznitelikler yalnızca, Web. config veya AppSettings aracılığıyla belirtilebilir.*<br/><br/>StackExchange. Redof günlükleriyle birlikte oturum durumu/çıkış önbelleğinden Günlükler sağlayarak uygulamanızda hata ayıklamak için bu öznitelikleri kullanın. *Loggingclassname* , *loggingmethodname*tarafından belirtilen yöntemi içeren bir derleme nitelikli sınıf adı olmalıdır.<br/><br/>*Loggingmethodname* tarafından belirtilen yöntem, **System. IO. TextWriter**dönüş türü ile genel, statik ve void (herhangi bir parametre almaz) olmalıdır. |
| *applicationName* | string | Geçerli işlemin modül adı veya "/" | *Yalnızca SessionStateProvider*<br/>*Bu öznitelik yalnızca, Web. config veya AppSettings aracılığıyla belirtilebilir.*<br/><br/>Redsıs önbelleğinde kullanılacak uygulama adı ön eki. Müşteri, farklı amaçlar için aynı redo önbelleğini kullanabilir. Oturum anahtarlarının çakışmadığından emin olmak için uygulama adının önüne eklenebilir. |
| *throwOnError* | boole | true | *Yalnızca SessionStateProvider*<br/>*Bu öznitelik yalnızca, Web. config veya AppSettings aracılığıyla belirtilebilir.*<br/><br/>Bir hata oluştuğunda özel durum oluşturulup oluşturulmayacağını belirtir.<br/><br/>*ThrowOnError*hakkında daha fazla bilgi Için, [öznitelik notları](#attribute-notes) bölümünde [ *throwOnError* 'teki notlar](#notes-on-throwonerror) bölümüne bakın. |*Microsoft. Web. redsıs. RedisSessionStateProvider. LastException*>. |
| *Retrytimeoutınmilliseconds* | pozitif tamsayı | 5000 | *Yalnızca SessionStateProvider*<br/>*Bu öznitelik yalnızca, Web. config veya AppSettings aracılığıyla belirtilebilir.*<br/><br/>Bir işlem başarısız olduğunda yeniden denenme süresini. Bu değer *Operationtimeoutınmilliseconds*değerinden küçükse, sağlayıcı yeniden denenmeyecektir.<br/><br/>*Retrytimeoutınmilliseconds*hakkında daha fazla bilgi için bkz. [öznitelik notları](#attribute-notes) bölümünde [ *retrytimeoutınmilliseconds* hakkında notlar](#notes-on-retrytimeoutinmilliseconds) . |
| *redisSerializerType* | string | *yok* | Microsoft. Web. Reddir uygulayan bir sınıfın derleme nitelikli tür adını belirtir. ISerializer ve değerleri seri hale getirmek ve seri durumdan çıkarmak için özel mantığı içerir. Daha fazla bilgi için, [öznitelik notları](#attribute-notes) bölümünde [ *Redisserializertype* hakkında](#about-redisserializertype) bölümüne bakın. |
|

## <a name="attribute-notes"></a>Öznitelik notları

### <a name="setting-connectionstring"></a>*ConnectionString* ayarlanıyor

*ConnectionString* 'in değeri, appSettings içinde bu tür bir dize varsa, appSettings 'ten gerçek bağlantı dizesini getirmek için anahtar olarak kullanılır. AppSettings içinde bulunmazsa, *ConnectionString* değeri Web. config **ConnectionString** bölümünden gerçek bağlantı dizesini getirmek için anahtar olarak kullanılır. Bağlantı dizesi AppSettings veya Web. config **ConnectionString** bölümünde yoksa, StackExchange. redsıs. connectionçoğullayıcı oluşturulurken bağlantı dizesi olarak *ConnectionString* öğesinin sabit değeri kullanılacaktır.

Aşağıdaki örneklerde, *ConnectionString* 'in nasıl kullanıldığı gösterilmektedir.

#### <a name="example-1"></a>Örnek 1

```xml
<connectionStrings>
    <add name="MyRedisConnectionString" connectionString="mycache.redis.cache.windows.net:6380,password=actual access key,ssl=True,abortConnect=False" />
</connectionStrings>
```

`web.config`' de, yukarıdaki anahtarı gerçek değer yerine parametre değeri olarak kullanın.

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
    <providers>
        <add type = "Microsoft.Web.Redis.RedisSessionStateProvide"
             name = "MySessionStateStore"
             connectionString = "MyRedisConnectionString"/>
    </providers>
</sessionState>
```

#### <a name="example-2"></a>Örnek 2

```xml
<appSettings>
    <add key="MyRedisConnectionString" value="mycache.redis.cache.windows.net:6380,password=actual access key,ssl=True,abortConnect=False" />
</appSettings>
```

`web.config`' de, yukarıdaki anahtarı gerçek değer yerine parametre değeri olarak kullanın.

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
    <providers>
        <add type = "Microsoft.Web.Redis.RedisSessionStateProvide"
             name = "MySessionStateStore"
             connectionString = "MyRedisConnectionString"/>
    </providers>
</sessionState>
```

#### <a name="example-3"></a>Örnek 3

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
    <providers>
        <add type = "Microsoft.Web.Redis.RedisSessionStateProvide"
             name = "MySessionStateStore"
             connectionString = "mycache.redis.cache.windows.net:6380,password=actual access key,ssl=True,abortConnect=False"/>
    </providers>
</sessionState>
```

### <a name="notes-on-throwonerror"></a>*ThrowOnError* hakkındaki notlar

Şu anda bir oturum işlemi sırasında bir hata oluşursa, oturum durumu sağlayıcısı bir özel durum oluşturur. Bu, uygulamayı kapatır.

Bu davranış, mevcut ASP.NET oturum durumu sağlayıcısı kullanıcılarının beklentilerini destekleyen bir şekilde değiştirilmiştir, Ayrıca isterseniz özel durumlar üzerinde işlem yapma özelliği de sağlar. Varsayılan davranış yine bir hata oluştuğunda, diğer ASP.NET oturum durumu sağlayıcılarıyla tutarlı bir özel durum oluşturur; mevcut kod, önceki ile aynı şekilde çalışmalıdır.

*ThrowOnError* **değerini false**olarak ayarlarsanız bir hata oluştuğunda özel durum oluşturmak yerine sessizce başarısız olur. Bir hata olup olmadığını görmek için, özel durumun ne olduğunu bulun, *Microsoft. Web. redsıs. RedisSessionStateProvider. LastException*statik özelliğini denetleyin.

### <a name="notes-on-retrytimeoutinmilliseconds"></a>*Retrytimeoutınmilliseconds* üzerine notlar

Bu, bazı oturum işleminin ağ hatası gibi şeyler nedeniyle hata durumunda yeniden denenmesi için bazı yeniden deneme mantığı sağlar ve aynı zamanda yeniden deneme zaman aşımını denetlemenize veya tamamen yeniden denenmenize izin verir.

*Retrytimeoutınmilliseconds* öğesini bir sayıya ayarlarsanız, örneğin 2000, bir oturum işlemi başarısız olduğunda, bir hata olarak davranmadan önce 2000 milisaniyeye yeniden dener. Bu nedenle, oturum durumu sağlayıcısı 'nın bu yeniden deneme mantığını uygulaması için zaman aşımını yapılandırmanız yeterlidir. İlk yeniden deneme 20 milisaniyelik sonra gerçekleşecektir, bu da çoğu durumda bir ağ hatası oluştuğunda yeterlidir. Bundan sonra, zaman aşımına uğrayana kadar her saniye yeniden dener. Zaman aşımına uğradıktan hemen sonra, zaman aşımını (en fazla) bir saniye kesmeyeceği için bir kez daha yeniden dener.

Yeniden deneme gerektiğini düşünmezseniz (örneğin, Redsıs sunucusunu uygulamanızla aynı makinede çalıştırıyorsanız) veya yeniden deneme mantığını kendiniz işlemek istiyorsanız, *Retrytimeoutınmilliseconds* öğesini 0 olarak ayarlayın.

### <a name="about-redisserializertype"></a>*Redisserializertype* hakkında

Varsayılan olarak, redin değerlerini depolamak için serileştirme, **BinaryFormatter** sınıfı tarafından belirtilen ikili biçimde yapılır. **Microsoft. Web. Red. ISerializer** uygulayan bir sınıfın derleme nitelikli tür adını belirtmek Için *redisserializertype* kullanın ve değerleri seri hale getirmek ve seri durumdan çıkarmak için özel mantık vardır. Örneğin, JSON.NET kullanan bir JSON serileştirici sınıfı aşağıda verilmiştir:

```cs
namespace MyCompany.Redis
{
    public class JsonSerializer : ISerializer
    {
        private static JsonSerializerSettings _settings = new JsonSerializerSettings() { TypeNameHandling = TypeNameHandling.All };

        public byte[] Serialize(object data)
        {
            return Encoding.UTF8.GetBytes(JsonConvert.SerializeObject(data, _settings));
        }

        public object Deserialize(byte[] data)
        {
            if (data == null)
            {
                return null;
            }
            return JsonConvert.DeserializeObject(Encoding.UTF8.GetString(data), _settings);
        }
    }
}
```

Bu sınıfın **Mycompanydll**adlı bir derlemede tanımlandığını varsayarsak, parametresini kullanmak Için *Redisserializertype* parametresini ayarlayabilirsiniz:

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
    <providers>
        <add type = "Microsoft.Web.Redis.RedisSessionStateProvider"
             name = "MySessionStateStore"
             redisSerializerType = "MyCompany.Redis.JsonSerializer,MyCompanyDll"
             ... />
    </providers>
</sessionState>
```

## <a name="output-cache-directive"></a>Çıktı önbelleği yönergesi

Çıktıyı önbelleğe almak istediğiniz her sayfaya bir OutputCache yönergesi ekleyin.

```xml
<%@ OutputCache Duration="60" VaryByParam="*" %>
```

Önceki örnekte, önbelleğe alınan sayfa verileri 60 saniye boyunca önbellekte kalır ve sayfanın farklı bir sürümü her bir parametre birleşimi için önbelleğe alınır. OutputCache yönergesi hakkında daha fazla bilgi için bkz. [@OutputCache](https://go.microsoft.com/fwlink/?linkid=320837).

Bu adımlar gerçekleştirildikten sonra uygulamanız Redsıs çıkış önbelleği sağlayıcısını kullanacak şekilde yapılandırılır.

## <a name="next-steps"></a>Sonraki adımlar

[Redsıs Için Azure önbelleği için ASP.NET oturum durumu sağlayıcısına](cache-aspnet-session-state-provider.md)göz atın.
