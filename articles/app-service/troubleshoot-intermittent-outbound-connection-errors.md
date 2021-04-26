---
title: Azure App Service zaman aralıklı giden bağlantı hatalarıyla ilgili sorunları giderme
description: Azure App Service 'da aralıklı bağlantı hatalarını ve ilgili performans sorunlarını giderme
author: v-miegge
manager: barbkess
ms.topic: troubleshooting
ms.date: 11/19/2020
ms.author: ramakoni
ms.custom: security-recommendations,fasttrack-edit
ms.openlocfilehash: 2b4719561ad94d54267410d0af28db6ee8d82b00
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104799114"
---
# <a name="troubleshooting-intermittent-outbound-connection-errors-in-azure-app-service"></a>Azure App Service zaman aralıklı giden bağlantı hatalarıyla ilgili sorunları giderme

Bu makale, [Azure App Service](./overview.md)aralıklı bağlantı hatalarını ve ilgili performans sorunlarını gidermenize yardımcı olur. Bu konu, kaynak adresi ağ çevirisi (SNAT) bağlantı noktalarının tükenmesi ve sorun giderme yöntemleri hakkında daha fazla bilgi sağlayacaktır. Bu makalenin herhangi bir noktasında daha fazla yardıma ihtiyacınız varsa, [MSDN Azure ve Stack Overflow forumlarında](https://azure.microsoft.com/support/forums/)Azure uzmanlarıyla iletişim kurun. Alternatif olarak, bir Azure destek olayı dosyası. [Azure destek sitesine](https://azure.microsoft.com/support/options/) gidin ve **Destek Al**' ı seçin.

## <a name="symptoms"></a>Belirtiler

Azure App Service üzerinde barındırılan uygulamalar ve Işlevler aşağıdaki belirtilerden birini veya daha fazlasını gösterebilir:

* Bir hizmet planındaki örneklerin tümünde veya bazılarında yavaş yanıt süreleri.
* Aralıklı 5 xx veya **Hatalı ağ geçidi** hataları
* Zaman aşımı hata iletileri
* Dış uç noktalara bağlanılamadı (SQLDB, Service Fabric, diğer uygulama hizmetleri vb. gibi)

## <a name="cause"></a>Nedeni

Aralıklı bağlantı sorunlarının önemli nedeni, yeni giden bağlantılar oluştururken bir sınıra ulaşmanıza neden olur. Vurabileceğinizi şunlar olabilir:

* TCP bağlantıları: yapılabilecek giden bağlantı sayısı için bir sınır vardır. Giden bağlantı sınırı, kullanılan çalışan boyutuyla ilişkilendirilir.
* SNAT bağlantı noktaları: [Azure 'Daki giden bağlantılar](../load-balancer/load-balancer-outbound-connections.md) , SNAT bağlantı noktası kısıtlamalarını ve giden bağlantıları nasıl etkilediğini açıklar. Azure, genel IP adresleriyle iletişim kurmak için kaynak ağ adresi çevirisi (SNAT) ve yük dengeleyicileri (müşterilere gösterilmez) kullanır. Azure App Service üzerindeki her örneğe başlangıçta önceden ayrılmış **128** SNAT bağlantı noktası numarası verilir. SNAT bağlantı noktası sınırı, bağlantıların aynı adrese ve bağlantı noktası birleşimine açılmasını etkiler. Uygulamanız adres ve bağlantı noktası birleşimlerinin karışımına bağlantı oluşturursa, SNAT bağlantı noktalarınızı kullanamazsınız. SNAT bağlantı noktaları, aynı adrese ve bağlantı noktası birleşimine yinelenen çağrılar olduğunda kullanılır. Bağlantı noktası yayımlandıktan sonra, bağlantı noktası gerektiğinde yeniden kullanılabilir. Azure ağ yükü dengeleyici geri kazanır, yalnızca 4 dakika bekledikten sonra, kapalı bağlantılardan gelen SNAT bağlantı noktasıdır.

Uygulamalar veya işlevler hızlı bir şekilde yeni bir bağlantı açtığında, 128 bağlantı noktasının önceden ayrılmış kotasını hızlıca tüketebilirler. Daha sonra, yeni bir SNAT bağlantı noktası kullanılabilir hale gelene kadar, ek SNAT bağlantı noktalarını dinamik olarak ayırarak veya geri kazanılan bir SNAT bağlantı noktasının yeniden kullanılmasını engellenecektir. Uygulamanızda SNAT bağlantı noktaları biterse, zaman aralıklı giden bağlantı sorunlarıyla karşılaşacaktır. 

## <a name="avoiding-the-problem"></a>Sorunu önleme

SNAT bağlantı noktası sınırlamalarından kaçınmanıza izin veren birkaç çözüm vardır. Bunlara aşağıdakiler dahildir:

* bağlantı havuzları: bağlantılarınızı havuzlayarak aynı adrese ve bağlantı noktasına yapılan çağrılar için yeni ağ bağlantıları açmaktan kaçınabilirsiniz.
* hizmet uç noktaları: hizmet uç noktaları ile güvenli hizmetler için bir SNAT bağlantı noktası kısıtlaması yok.
* Özel uç noktalar: özel uç noktalarla güvenliği sağlanmış hizmetler için bir SNAT bağlantı noktası kısıtlaması yoktur.
* NAT ağ geçidi: bir NAT ağ geçidiyle, üzerinden trafik gönderen kaynaklar tarafından kullanılabilen 64K giden SNAT bağlantı noktalarıdır.

SNAT bağlantı noktası sorununun önlenmeleri, yeni bağlantıların kaldı aynı konak ve bağlantı noktasına oluşturulmasını önleme anlamına gelir. Bağlantı havuzları, bu sorunu çözmenin daha belirgin yöntemlerinden biridir.

Hedef, hizmet uç noktalarını destekleyen bir Azure hizmeti ise, [Bölgesel VNET tümleştirmesi](./web-sites-integrate-with-vnet.md) ve hizmet uç noktalarını veya özel uç noktaları kullanarak SNAT bağlantı noktası tükenmesi sorunlarından kaçınabilirsiniz. Bölgesel VNet tümleştirmesi kullandığınızda ve hizmet uç noktalarını tümleştirme alt ağına yerleştirdiğinizde, bu hizmetlere giden trafik giden trafiği giden SNAT bağlantı noktası kısıtlamalarına sahip olmaz. Benzer şekilde, bölgesel VNet tümleştirmesi ve özel uç noktalar kullanırsanız, o hedefe giden bir SNAT bağlantı noktası sorununuz olmayacaktır. 

Hedef, Azure dışında bir dış uç noktayken, bir NAT ağ geçidi kullanmak, 64K giden SNAT bağlantı noktalarından oluşur. Ayrıca, anybody ile paylaşmazsınız özel bir giden adresi de sağlar. 

Mümkünse, bağlantı havuzlarını kullanmak ve tüm durumu önlemek için kodunuzu geliştirebilirsiniz. Bu durumu azaltmak için kodu yeterince hızlı değiştirmek her zaman mümkün değildir. Kodunuzun zaman içinde değiştirebir durumda olduğu durumlarda, diğer çözümlerden yararlanın. Sorunun en iyi çözümü, tüm çözümlerin olabildiğince en iyi şekilde birleştirilmesine neden olur. Hizmet uç noktalarını ve özel uç noktaları Azure hizmetleri için ve NAT ağ geçidini geri kalanı ile kullanmayı deneyin. 

SNAT bağlantı noktası tükenmesi için genel stratejiler, **Azure belgelerinin giden bağlantılarının** [sorun çözme bölümünde](../load-balancer/load-balancer-outbound-connections.md) ele alınmıştır. Bu stratejilerin, Azure App Service 'te barındırılan uygulamalar ve işlevler için geçerlidir.

### <a name="modify-the-application-to-use-connection-pooling"></a>Uygulamayı bağlantı havuzunu kullanacak şekilde değiştirme

* Havuz HTTP bağlantıları için, [HttpClientFactory Ile havuz http bağlantıları](/aspnet/core/performance/performance-best-practices#pool-http-connections-with-httpclientfactory)' nı gözden geçirin.
* SQL Server bağlantı havuzu oluşturma hakkında daha fazla bilgi için [SQL Server bağlantı havuzunu inceleyin (ADO.net)](/dotnet/framework/data/adonet/sql-server-connection-pooling).
* Entity Framework uygulamalarıyla havuzlamayı uygulamak için [DbContext havuzunu](/ef/core/what-is-new/ef-core-2.0#dbcontext-pooling)gözden geçirin.

Farklı çözüm yığınına göre bağlantı havuzu uygulama bağlantılarının bir koleksiyonu aşağıda verilmiştir.

#### <a name="node"></a>Düğüm

Varsayılan olarak, NodeJS bağlantıları etkin tutulmaz. Aşağıda, bunların nasıl uygulanacağını gösteren örnekler içeren bağlantı havuzlaması için popüler veritabanları ve paketler verilmiştir.

* [MySQL](https://github.com/mysqljs/mysql#pooling-connections)
* [MongoDB](https://blog.mlab.com/2017/05/mongodb-connection-pooling-for-express-applications/)
* [PostgreSQL](https://node-postgres.com/features/pooling)
* [SQL Server](https://github.com/tediousjs/node-mssql#connection-pools)

HTTP etkin tut

* [Aracısız KeepAlive](https://www.npmjs.com/package/agentkeepalive)
* [Node.js v 13.9.0 belgeleri](https://nodejs.org/api/http.html)

#### <a name="java"></a>Java

Aşağıda, JDBC bağlantı havuzu için kullanılan ve bunların nasıl uygulanacağı hakkında örnekler içeren popüler kitaplıklar verilmiştir: JDBC bağlantı havuzu.

* [Tomcat 8](https://tomcat.apache.org/tomcat-8.0-doc/jdbc-pool.html)
* [C3p0](https://github.com/swaldman/c3p0)
* [Hikarıcp](https://github.com/brettwooldridge/HikariCP)
* [Apache DBCP](https://commons.apache.org/proper/commons-dbcp/)

HTTP bağlantı havuzu

* [Apache bağlantı yönetimi](https://hc.apache.org/httpcomponents-client-5.0.x/)
* [Sınıf PoolingHttpClientConnectionManager](https://hc.apache.org/httpcomponents-client-5.0.x/)

#### <a name="php"></a>PHP

PHP bağlantı havuzunu desteklememesine rağmen, arka uç sunucunuza kalıcı veritabanı bağlantıları kullanmayı deneyebilirsiniz.
 
* MySQL sunucusu

   * Daha yeni sürümler için [mysqli bağlantıları](https://www.php.net/manual/mysqli.quickstart.connections.php)
   * PHP 'nin eski sürümleri için [mysql_pconnect](https://www.php.net/manual/function.mysql-pconnect.php)

* Diğer veri kaynakları

   * [PHP bağlantı yönetimi](https://www.php.net/manual/en/pdo.connections.php)

### <a name="modify-the-application-to-reuse-connections"></a>Bağlantıyı yeniden kullanmak için uygulamayı değiştirme

*  Azure işlevlerinde bağlantıları yönetme hakkında ek işaretçiler ve örnekler için, [Azure işlevlerinde bağlantıları yönetme](../azure-functions/manage-connections.md)konusunu gözden geçirin.

### <a name="modify-the-application-to-use-less-aggressive-retry-logic"></a>Uygulamayı daha az agresif yeniden deneme mantığını kullanacak şekilde değiştirin

* Ek rehberlik ve örnekler için [yeniden deneme modelini](/azure/architecture/patterns/retry)inceleyin.

### <a name="use-keepalives-to-reset-the-outbound-idle-timeout"></a>Giden boşta kalma zaman aşımını sıfırlamak için keepcanlı tutmayı kullanın

* Node.js uygulamalar için keepcanlı uygulamayı uygulamak için, [düğüm uygulamamın aşırı giden çağrılar yapmasını](./app-service-web-nodejs-best-practices-and-troubleshoot-guide.md#my-node-application-is-making-excessive-outbound-calls)inceleyin.

### <a name="additional-guidance-specific-to-app-service"></a>App Service özgü ek rehberlik:

* Bir [Yük testi](/azure/devops/test/load-test/app-service-web-app-performance-test) , gerçek dünya verilerinin kararlı bir besleme hızında benzetimini yapmanız gerekir. Uygulamaları ve işlevleri gerçek dünya stres altında test etmek, SNAT bağlantı noktası tükenmesi sorunlarını önceden tanımlayabilir ve çözümleyebilir.
* Arka uç hizmetlerinin yanıtları hızlıca döndürebildiğini doğrulayın. Azure SQL veritabanı ile ilgili sorun giderme performansı sorunlarını [gidermek için akıllı içgörüler Azure SQL veritabanı performans sorunlarını giderme](../azure-sql/database/intelligent-insights-troubleshoot-performance.md#recommended-troubleshooting-flow)makalesini inceleyin.
* App Service planını daha fazla örneğe ölçeklendirin. Ölçeklendirme hakkında daha fazla bilgi için bkz. [Azure App Service bir uygulamayı ölçeklendirme](./manage-scale-up.md). Bir App Service planındaki her çalışan örneğine bir dizi SNAT bağlantı noktası ayrılır. Kullanımınızı daha fazla örneğe yaydıysanız, benzersiz bir uzak uç nokta başına önerilen 100 giden bağlantı sınırının altında her örnek için SNAT bağlantı noktası kullanımını alabilirsiniz.
* Tek bir giden IP adresi tahsis ettiğiniz ve bağlantı ve SNAT bağlantı noktalarının sınırları çok daha yüksek olduğu [App Service ortamı (Ao)](./environment/using-an-ase.md)' a geçmeyi göz önünde bulundurun. Bir Ao 'da, örnek başına SNAT bağlantı noktası sayısı, [Azure yük dengeleyici ön tahsisi tablosuna](../load-balancer/load-balancer-outbound-connections.md#snatporttable) dayalıdır. örneğin, 1-50 çalışan örneklerine sahıp bir Aken, örnek başına 1024 önceden ayrılmış bağlantı noktasına sahip olsa da, 51-100 çalışan örneklerine sahıp bir Ao, örnek başına 512 önceden ayrılan bağlantı noktalarına sahip olur.

Sınırlar, çalışanlarınızın boyutuna göre ayarlandığından, giden TCP sınırlarının çözülmesini daha kolay hale getirir. Sınır, [VM 'Ler arası sayısal sınırlara göre sınırları görebilir-TCP bağlantıları](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#cross-vm-numerical-limits)

|Sınır adı|Description|Küçük (a1)|Orta (a2)|Büyük (a3)|Yalıtılmış katman (Ao)|
|---|---|---|---|---|---|
|Bağlantılar|Tüm VM genelinde bağlantı sayısı|1920|3968|8064|16.000|

Giden TCP limitlerini önlemek için çalışanlarınızın boyutunu artırabilir veya yatay olarak ölçeklendirebilirsiniz.

## <a name="troubleshooting"></a>Sorun giderme

İki tür giden bağlantı sınırını bilmenin yanı sıra uygulamanızın ne yaptığını bilmek, sorun gidermeyi daha kolay hale getirir. Uygulamanızın aynı depolama hesabına birçok çağrı yapıyorsa, bir SNAT sınırının şüpheli olduğunu bilirsiniz. Uygulamanız tüm Internet üzerinden uç noktalara çok sayıda çağrı oluşturursa, VM sınırına ulaştığından şüphelenileolursunuz.

Uygulamanın nedenini hızlı bir şekilde tespit etmek için yeterince bilginiz yoksa, bu belirleme konusunda yardımcı olmak için App Service bazı araçlar ve teknikler mevcuttur.

### <a name="find-snat-port-allocation-information"></a>SNAT bağlantı noktası ayırma bilgilerini bul

SNAT bağlantı noktası ayırma bilgilerini bulmak için [App Service tanılamayı](./overview-diagnostics.md) kullanabilir ve bir App SERVICE sitesinin SNAT bağlantı noktası ayırma ölçüsünü gözlemleyebilirsiniz. SNAT bağlantı noktası ayırma bilgilerini bulmak için aşağıdaki adımları izleyin:

1. App Service tanılama 'ya erişmek için, App Service Web uygulamanıza veya [Azure portal](https://portal.azure.com/)App Service ortamı gidin. Sol gezinti bölmesinde **Tanıla ve sorunları çöz**' ü seçin.
2. Kullanılabilirlik ve performans kategorisi seçin
3. Kategori altındaki kullanılabilir kutucuklar listesinden SNAT bağlantı noktası Tükenme Kutucuğu ' nı seçin. Uygulama, bu işlemi 128 altına saklayacağız.
İhtiyacınız varsa, yine de bir destek bileti açabilirsiniz ve Destek Mühendisi sizin için arka uca olan ölçüyü alır.

SNAT bağlantı noktası kullanımı bir ölçüm olarak kullanılamadığından, SNAT bağlantı noktası kullanımı temel alınarak otomatik ölçeklendirme yapılamaz veya SNAT bağlantı noktası ayırma ölçüsüne göre otomatik ölçeklendirmeyi yapılandırabilirsiniz.

### <a name="tcp-connections-and-snat-ports"></a>TCP bağlantıları ve SNAT bağlantı noktaları

TCP bağlantıları ve SNAT bağlantı noktaları doğrudan ilgili değildir. TCP bağlantıları kullanım algılayıcısı, herhangi bir App Service sitesinin sorunlarını Tanıla ve çöz dikey penceresine dahildir. Bulmak için "TCP bağlantıları" ifadesini arayın.

* SNAT bağlantı noktaları yalnızca dış ağ akışları için kullanılır, ancak toplam TCP bağlantısı da yerel geri döngü bağlantıları içerir.
* Bir SNAT bağlantı noktası, akışlar her iki protokol, IP adresi veya bağlantı noktasında farklıysa farklı akışlar tarafından paylaşılabilir. TCP bağlantıları ölçümü, her TCP bağlantısını sayar.
* TCP bağlantı sınırı çalışan örneği düzeyinde gerçekleşir. Azure ağ giden Yük Dengeleme, SNAT bağlantı noktası sınırlaması için TCP bağlantıları ölçümünü kullanmaz.
* TCP bağlantı sınırları, [korumalı alan çapraz VM 'de, TCP bağlantılarında](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#cross-vm-numerical-limits) açıklanmaktadır

|Sınır adı|Description|Küçük (a1)|Orta (a2)|Büyük (a3)|Yalıtılmış katman (Ao)|
|---|---|---|---|---|---|
|Bağlantılar|Tüm VM genelinde bağlantı sayısı|1920|3968|8064|16.000|

### <a name="webjobs-and-database-connections"></a>Web Işleri ve veritabanı bağlantıları
 
SNAT bağlantı noktaları tükenirse, WebJobs 'lar SQL veritabanı 'na bağlanamıyorsa, her bir Web uygulaması işlemi tarafından kaç bağlantı açıldığını göstermek için ölçüm yoktur. Sorunlu WebJob 'u bulmak için, birkaç Web Işini başka bir App Service planına taşıyarak durumun gelişip artmediğini veya bir sorun planlardan birinde kalırsa. Sorunlu WebJob 'u bulana kadar işlemi tekrarlayın.

### <a name="using-snat-ports-sooner"></a>SNAT bağlantı noktalarını daha erken kullanma

Tüm SNAT bağlantı noktaları aşağıdaki koşullara göre yayımlanacaksa ve davranış tasarıma göre olduğu için, tüm Azure ayarlarını, kullanılan SNAT bağlantı noktalarını daha erken yayımlanacak şekilde değiştiremezsiniz.
 
* Sunucu veya istemci FINACK gönderirse, [SNAT bağlantı noktası](../load-balancer/load-balancer-outbound-connections.md) 240 saniye sonra serbest bırakılır.
* Bir RST görülüyorsa, SNAT bağlantı noktası 15 saniye sonra serbest bırakılır.
* Boşta kalma zaman aşımına ulaşılmışsa, bağlantı noktası serbest bırakılır.
 
## <a name="additional-information"></a>Ek bilgiler

* [App Service ile SNAT](https://4lowtherabbit.github.io/blogs/2019/10/SNAT/)
* [Azure App Service 'de yavaş uygulama performansı sorunlarını giderme](./troubleshoot-performance-degradation.md)
