---
title: Redsıs için Premium bir Azure önbelleği için Redsıs Kümelemesi yapılandırma
description: Redsıs örnekleri için Premium katman Azure önbelleğiniz için Redsıs Kümelemesi oluşturmayı ve yönetmeyi öğrenin
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 06/13/2018
ms.author: yegu
ms.openlocfilehash: 1f0c97d6c0854254026e194ffd5030976fc506b2
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74122167"
---
# <a name="how-to-configure-redis-clustering-for-a-premium-azure-cache-for-redis"></a>Redsıs için Premium bir Azure önbelleği için Redsıs Kümelemesi yapılandırma
Redin için Azure önbelleğinde, kümeleme, kalıcılık ve sanal ağ desteği gibi Premium katman özellikleri de dahil olmak üzere, önbellek boyutu ve özellikleri seçimine esneklik sağlayan farklı önbellek teklifleri vardır. Bu makalede, Redsıs örneği için Premium Azure önbelleğinde kümelemenin nasıl yapılandırılacağı açıklanır.

Diğer Premium önbellek özellikleri hakkında daha fazla bilgi için bkz. [Redsıs Premium katmanı Için Azure önbelleğine giriş](cache-premium-tier-intro.md).

## <a name="what-is-redis-cluster"></a>Redsıs kümesi nedir?
Redsıs için Azure Cache, redsıs ['de uygulanan](https://redis.io/topics/cluster-tutorial)redin kümesi sunar. Redsıs kümesi ile aşağıdaki avantajları elde edersiniz: 

* Veri kümenizi birden çok düğüm arasında otomatik olarak bölme özelliği. 
* Düğümlerin bir alt kümesi hatalara sahip olduğunda veya kümenin geri kalanıyla iletişim kuramadıkça işlemlere devam etme yeteneği. 
* Daha fazla verimlilik: parçalar sayısını artırdıkça üretilen Iş miktarı artar. 
* Daha fazla bellek boyutu: parça sayısını artırdıkça doğrusal miktarı artırır.  

Kümeleme, kümelenmiş bir önbellek için kullanılabilen bağlantı sayısını artırmaz. Premium önbellekler ile boyut, aktarım hızı ve bant genişliği hakkında daha fazla bilgi için bkz. [redin sunumu Için Azure önbelleği ve boyutu ne kullanmalıyım?](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)

Azure 'da reddo kümesi, her parçanın Redsıs hizmeti için Azure Cache tarafından yönetilen çoğaltma ile birincil/çoğaltma çifti olduğu bir birincil/çoğaltma modeli olarak sunulur. 

## <a name="clustering"></a>Kümeleme
Kümeleme, önbellek oluşturma sırasında **redsıs dikey penceresinde yeni Azure önbelleğinde** etkinleştirilir. 

[!INCLUDE [redis-cache-create](../../includes/redis-cache-premium-create.md)]

Kümeleme, **Redsıs kümesi** dikey penceresinde yapılandırılır.

![Kümeleme][redis-cache-clustering]

Kümede en fazla 10 parça olabilir. **Etkin** ' e tıklayın ve kaydırıcıyı kaydırın veya parça **sayısı** için 1 ile 10 arasında bir sayı yazın ve **Tamam**' a tıklayın.

Her parça Azure tarafından yönetilen birincil/çoğaltma önbelleği çiftidir ve önbelleğin toplam boyutu, parça sayısı fiyatlandırma katmanında seçilen önbellek boyutuyla çarpılarak hesaplanır. 

![Kümeleme][redis-cache-clustering-selected]

Önbellek oluşturulduktan sonra bu sunucuya bağlanır ve bunu kümelenmemiş bir önbellekte olduğu gibi kullanırsınız ve Redit verileri önbellek parçaları genelinde dağıtır. Tanılama [etkinleştirilmişse](cache-how-to-monitor.md#enable-cache-diagnostics), ölçümler her parça için ayrı olarak yakalanır ve reddo dikey penceresinde Azure önbelleğinde [görüntülenebilir](cache-how-to-monitor.md) . 

> [!NOTE]
> 
> Kümeleme yapılandırıldığında istemci uygulamanızda gereken bazı küçük farklılıklar vardır. Daha fazla bilgi için bkz. [kümelendirmeyi kullanmak için istemci Uygulamam üzerinde herhangi bir değişiklik yapmam gerekir mi?](#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)
> 
> 

StackExchange. Redo istemcisiyle kümeleme ile çalışma hakkında örnek kod için [Merhaba Dünya](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) örneğinin [Clustering.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/Clustering.cs) bölümüne bakın.

<a name="cluster-size"></a>

## <a name="change-the-cluster-size-on-a-running-premium-cache"></a>Çalışan bir Premium önbellekte küme boyutunu değiştirme
Kümeleme etkin olan çalışan bir Premium önbellekteki küme boyutunu değiştirmek için, **Kaynak menüsünden** **Redsıs küme boyutu** ' na tıklayın.

> [!NOTE]
> Redsıs Premium katmanı için Azure önbelleği genel kullanıma sunulurken, Redsıs küme boyutu özelliği şu anda önizlemededir.
> 
> 

![Redsıs kümesi boyutu][redis-cache-redis-cluster-size]

Küme boyutunu değiştirmek için kaydırıcıyı kullanın veya parça **sayısı** metin kutusunda 1 ile 10 arasında bir sayı yazın ve kaydetmek için **Tamam** ' a tıklayın.

Küme boyutunun artırılması, en yüksek aktarım hızını ve önbellek boyutunu artırır. Küme boyutunun artırılması en yüksek değeri artırmaz. istemciler için kullanılabilir bağlantılar.

> [!NOTE]
> Bir kümeyi ölçeklendirmek, pahalı bir komut olan [MIGRATE](https://redis.io/commands/migrate) komutunu çalıştırır, bu nedenle en düşük düzeyde etki için bu işlemi yoğun olmayan saatlerde çalıştırmayı göz önünde bulundurun. Geçiş işlemi sırasında sunucu yükünde bir ani artış görürsünüz. Bir kümenin ölçeklendirilmesi uzun süredir çalışan bir işlemdir ve geçen süre, anahtar sayısına ve bu anahtarlarla ilişkili değerlerin boyutuna bağlıdır.
> 
> 

## <a name="clustering-faq"></a>Kümeleme hakkında SSS
Aşağıdaki liste, Redsıs Kümelemesi için Azure önbelleği hakkında sık sorulan soruların yanıtlarını içerir.

* [Kümeleme kullanmak için istemci uygulamamda herhangi bir değişiklik yapmam gerekiyor mu?](#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)
* [Anahtarlar bir kümede nasıl dağıtılır?](#how-are-keys-distributed-in-a-cluster)
* [Oluşturabileceğiniz en büyük önbellek boyutu nedir?](#what-is-the-largest-cache-size-i-can-create)
* [Tüm redin istemcileri kümelemeyi destekliyor mu?](#do-all-redis-clients-support-clustering)
* [Kümeleme etkinleştirildiğinde Nasıl yaparım? önbellekme bağlansın mı?](#how-do-i-connect-to-my-cache-when-clustering-is-enabled)
* [Önbelleğim bireysel parçalara doğrudan bağlanabilir miyim?](#can-i-directly-connect-to-the-individual-shards-of-my-cache)
* [Daha önce oluşturulmuş bir önbellek için kümeleme yapılandırabilir miyim?](#can-i-configure-clustering-for-a-previously-created-cache)
* [Temel veya standart önbellek için kümeleme yapılandırabilir miyim?](#can-i-configure-clustering-for-a-basic-or-standard-cache)
* [Redin ASP.NET oturum durumu ve çıktı önbelleği sağlayıcılarıyla kümeleme kullanabilir miyim?](#can-i-use-clustering-with-the-redis-aspnet-session-state-and-output-caching-providers)
* [StackExchange. reddo ve kümeleme kullanırken ne yapmam gerekir?](#i-am-getting-move-exceptions-when-using-stackexchangeredis-and-clustering-what-should-i-do)

### <a name="do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering"></a>Kümeleme kullanmak için istemci uygulamamda herhangi bir değişiklik yapmam gerekiyor mu?
* Kümeleme etkinleştirildiğinde yalnızca veritabanı 0 kullanılabilir. İstemci uygulamanız birden çok veritabanı kullanıyorsa ve 0 dışında bir veritabanını okumaya veya yazmaya çalışırsa, aşağıdaki özel durum oluşturulur. `Unhandled Exception: StackExchange.Redis.RedisConnectionException: ProtocolFailure on GET --->` `StackExchange.Redis.RedisCommandException: Multiple databases are not supported on this server; cannot switch to database: 6`
  
  Daha fazla bilgi için bkz. [Redsıs küme belirtimi uygulanmış alt küme](https://redis.io/topics/cluster-spec#implemented-subset).
* [StackExchange. Redo](https://www.nuget.org/packages/StackExchange.Redis/)kullanıyorsanız, 1.0.481 veya üstünü kullanmanız gerekir. Küme etkin olmayan bir önbelleğe bağlanırken kullandığınız [uç noktaları, bağlantı noktalarını ve anahtarları](cache-configure.md#properties) kullanarak önbelleğe bağlanırsınız. Tek fark, tüm okuma ve yazma işlemlerini veritabanı 0 ' a uygulamak olmalıdır.
  
  * Diğer istemcilerin farklı gereksinimleri olabilir. Bkz. [Tüm Redmi istemcileri kümeleme desteği mi?](#do-all-redis-clients-support-clustering)
* Uygulamanız tek bir komutta toplu olarak birden çok anahtar işlem kullanıyorsa, tüm anahtarların aynı parça içinde bulunması gerekir. Aynı parça içindeki anahtarları bulmak için bkz. [anahtarlar bir kümede nasıl dağıtılır?](#how-are-keys-distributed-in-a-cluster)
* Redsıs ASP.NET oturum durumu sağlayıcısını kullanıyorsanız, 2.0.1 veya üstünü kullanmanız gerekir. Bkz [. redin ASP.NET oturum durumu ve çıktı önbelleği sağlayıcılarıyla kümeleme kullanabilir miyim?](#can-i-use-clustering-with-the-redis-aspnet-session-state-and-output-caching-providers)

### <a name="how-are-keys-distributed-in-a-cluster"></a>Anahtarlar bir kümede nasıl dağıtılır?
Redsıs [anahtarları dağıtım modeli](https://redis.io/topics/cluster-spec#keys-distribution-model) belgeleri: anahtar alanı 16384 yuvalara bölünür. Her anahtar karma hale getirilir ve kümenin düğümlerine dağıtılmış olan bu yuvalardan birine atanır. Karma Etiketler kullanılarak aynı parça içinde birden fazla anahtarın bulunduğundan emin olmak için anahtarın hangi kısmının karma hale getirilmiş olduğunu yapılandırabilirsiniz.

* Karma etiketine sahip anahtarlar-anahtarın herhangi bir bölümü `{` ve `}`içindeyse, anahtarın karma yuvasını belirleme amacıyla yalnızca anahtarın bir bölümü karma hale getirilir. Örneğin, aşağıdaki 3 anahtar aynı parça içinde bulunur: `{key}1`, `{key}2`ve `{key}3` yalnızca adının `key` bölümü karma hale getirilmiş. Anahtarların karma etiket özelliklerinin tamamı listesi için bkz. [anahtarlar Karma etiketleri](https://redis.io/topics/cluster-spec#keys-hash-tags).
* Karma etiketi olmayan anahtarlar-karma için tüm anahtar adı kullanılır. Bu, önbelleğin parçaları genelinde istatistiksel olarak eşit bir dağıtıma neden olur.

En iyi performans ve verimlilik için anahtarları eşit olarak dağıtmanız önerilir. Bir karma etiketi ile anahtar kullanıyorsanız, anahtarların eşit şekilde dağıtılmasını sağlamak uygulamanın sorumluluğundadır.

Daha fazla bilgi için bkz. [anahtarlar dağıtım modeli](https://redis.io/topics/cluster-spec#keys-distribution-model), [redthe Cluster veri](https://redis.io/topics/cluster-tutorial#redis-cluster-data-sharding)parçaları ve [anahtarlar Karma etiketleri](https://redis.io/topics/cluster-spec#keys-hash-tags).

Kümeleme ile çalışma ve StackExchange. reddo istemcisiyle aynı parça içindeki anahtarları bulma hakkında örnek kod için, [Merhaba Dünya](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) örneğinin [Clustering.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/Clustering.cs) bölümüne bakın.

### <a name="what-is-the-largest-cache-size-i-can-create"></a>Oluşturabileceğiniz en büyük önbellek boyutu nedir?
En büyük Premium önbellek boyutu 120 GB 'dir. En fazla 10 adet parçalama, en fazla 1,2 TB GB boyutunda olabilir. Daha büyük bir boyuta ihtiyacınız varsa [daha fazla istek](mailto:wapteams@microsoft.com?subject=Redis%20Cache%20quota%20increase)yapabilirsiniz. Daha fazla bilgi için bkz. [Redsıs fiyatlandırması Için Azure önbelleği](https://azure.microsoft.com/pricing/details/cache/).

### <a name="do-all-redis-clients-support-clustering"></a>Tüm redin istemcileri kümelemeyi destekliyor mu?
Tüm istemciler Redsıs kümelemesini desteklemez! Kümelemeyi destekleyen bir kitaplık ve sürüm kullandığınızı doğrulamak için lütfen kullandığınız kitaplığın belgelerini denetleyin. StackExchange. Redo, daha yeni sürümlerinde kümelemeyi destekleyen bir kitaplıktır. Diğer istemcilerle ilgili daha fazla bilgi için, [redsıs kümesi öğreticisinin](https://redis.io/topics/cluster-tutorial) [küme ile yürütülmesi](https://redis.io/topics/cluster-tutorial#playing-with-the-cluster) bölümüne bakın. 

Reddo kümeleme protokolü, her bir istemcinin her parçaya doğrudan kümeleme modunda bağlanmasını gerektirir ve ayrıca ' TAŞıNMıŞ ' veya ' çapraz YUVALAR ' gibi yeni hata yanıtlarını tanımlar. Küme modu önbelleği ile kümeleme desteği olmayan bir istemciyi kullanma girişimi, çok sayıda [taşınan yeniden yönlendirme özel durumu](https://redis.io/topics/cluster-spec#moved-redirection)oluşmasına neden olabilir veya çapraz yuva çoklu anahtar istekleri yapıyorsanız uygulamanızı kesmeniz yeterlidir.

> [!NOTE]
> İstemci olarak StackExchange. Redsıs kullanıyorsanız, kümelemenin doğru bir şekilde çalışması için [StackExchange. redsıs](https://www.nuget.org/packages/StackExchange.Redis/) 1.0.481 veya üzeri sürümünün en son sürümünü kullandığınızdan emin olun. Taşıma özel durumlarıyla ilgili herhangi bir sorununuz varsa daha fazla bilgi için bkz. [özel durumları taşıma](#move-exceptions) .
> 
> 

### <a name="how-do-i-connect-to-my-cache-when-clustering-is-enabled"></a>Kümeleme etkinleştirildiğinde Nasıl yaparım? önbellekme bağlansın mı?
Kümelemeye sahip olmayan bir önbelleğe bağlanırken kullandığınız [uç noktaları](cache-configure.md#properties), [bağlantı noktalarını](cache-configure.md#properties)ve [anahtarları](cache-configure.md#access-keys) kullanarak önbelleğinize bağlanabilirsiniz. Redsıs, arka uçta kümelendirmeyi yönetir, böylece istemciden yönetmeniz gerekmez.

### <a name="can-i-directly-connect-to-the-individual-shards-of-my-cache"></a>Önbelleğim bireysel parçalara doğrudan bağlanabilir miyim?
Kümeleme protokolü, istemcinin doğru parça bağlantıları yapmasını gerektirir. Bu nedenle, istemcinin bunu sizin için doğru yapması gerekir. Bu şekilde, her parça, toplu olarak bir önbellek örneği olarak bilinen birincil/çoğaltma önbelleği çiftinin oluşur. Bu önbellek örneklerine, GitHub 'da Redsıs deposunun [kararsız](https://redis.io/download) dalında redsıs-CLI yardımcı programını kullanarak bağlanabilirsiniz. Bu sürüm `-c` anahtarla başlatıldığında temel desteği uygular. Daha fazla bilgi için bkz. [redsıs kümesi öğreticisinde](https://redis.io/topics/cluster-tutorial) [https://redis.io](https://redis.io) [kümeyle birlikte yürütme](https://redis.io/topics/cluster-tutorial#playing-with-the-cluster) .

SSL olmayan için aşağıdaki komutları kullanın.

    Redis-cli.exe –h <<cachename>> -p 13000 (to connect to instance 0)
    Redis-cli.exe –h <<cachename>> -p 13001 (to connect to instance 1)
    Redis-cli.exe –h <<cachename>> -p 13002 (to connect to instance 2)
    ...
    Redis-cli.exe –h <<cachename>> -p 1300N (to connect to instance N)

SSL için `1300N` `1500N`ile değiştirin.

### <a name="can-i-configure-clustering-for-a-previously-created-cache"></a>Daha önce oluşturulmuş bir önbellek için kümeleme yapılandırabilir miyim?
Evet. İlk olarak, yoksa ölçeklendirmeye göre önbelleğinizin Premium olduğundan emin olun. Daha sonra, clsuter etkinleştirme seçeneği dahil olmak üzere küme yapılandırma seçeneklerini görebilmeniz gerekir. Önbellek oluşturulduktan sonra veya kümelemeyi ilk kez etkinleştirdikten sonra küme boyutunu değiştirebilirsiniz.

   >[!IMPORTANT]
   >Kümelemeyi etkinleştirme işlemini geri alamazsınız. Ve kümeleme etkin olan bir önbellek ve yalnızca bir parça, kümeleme *olmadan* aynı boyuttaki bir önbellekten *farklı* davranır.

### <a name="can-i-configure-clustering-for-a-basic-or-standard-cache"></a>Temel veya standart önbellek için kümeleme yapılandırabilir miyim?
Kümeleme yalnızca Premium önbellekler için kullanılabilir.

### <a name="can-i-use-clustering-with-the-redis-aspnet-session-state-and-output-caching-providers"></a>Redin ASP.NET oturum durumu ve çıktı önbelleği sağlayıcılarıyla kümeleme kullanabilir miyim?
* **Redsıs çıkış önbelleği sağlayıcısı** -hiçbir değişiklik gerekmiyor.
* **Redsıs oturum durumu sağlayıcısı** -kümelendirmeyi kullanmak Için [Redissessionstateprovider](https://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider) 2.0.1 veya üstünü kullanmanız ya da bir özel durum oluşturulması gerekir. Bu bir son değişiklik; daha fazla bilgi için bkz. [v 2.0.0 kıran değişiklik ayrıntıları](https://github.com/Azure/aspnet-redis-providers/wiki/v2.0.0-Breaking-Change-Details).

<a name="move-exceptions"></a>

### <a name="i-am-getting-move-exceptions-when-using-stackexchangeredis-and-clustering-what-should-i-do"></a>StackExchange. reddo ve kümeleme kullanırken ne yapmam gerekir?
Kümeleme kullanırken StackExchange. Redsıs kullanıyorsanız ve `MOVE` özel durumlar alıyorsanız, [StackExchange. redsıs 1.1.603](https://www.nuget.org/packages/StackExchange.Redis/) veya üstünü kullandığınızdan emin olun. .NET uygulamalarınızı StackExchange. Redsıs kullanacak şekilde yapılandırma yönergeleri için bkz. [önbellek Istemcilerini yapılandırma](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).

## <a name="next-steps"></a>Sonraki adımlar
Daha fazla Premium önbellek özelliği kullanmayı öğrenin.

* [Redsıs Premium katmanı için Azure önbelleğine giriş](cache-premium-tier-intro.md)

<!-- IMAGES -->

[redis-cache-clustering]: ./media/cache-how-to-premium-clustering/redis-cache-clustering.png

[redis-cache-clustering-selected]: ./media/cache-how-to-premium-clustering/redis-cache-clustering-selected.png

[redis-cache-redis-cluster-size]: ./media/cache-how-to-premium-clustering/redis-cache-redis-cluster-size.png







