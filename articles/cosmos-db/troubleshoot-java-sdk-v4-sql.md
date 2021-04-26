---
title: Java SDK v4 Azure Cosmos DB tanılama ve sorun giderme
description: Java SDK v4 'daki Azure Cosmos DB sorunları tanımlamak, tanılamak ve sorunlarını gidermek için istemci tarafı günlüğe kaydetme ve diğer üçüncü taraf araçları gibi özellikleri kullanın.
author: anfeldma-ms
ms.service: cosmos-db
ms.date: 06/11/2020
ms.author: anfeldma
ms.devlang: java
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.custom: devx-track-java
ms.openlocfilehash: cba8b97adb40ca2c277268188ff6ad541c7e9676
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100596467"
---
# <a name="troubleshoot-issues-when-you-use-azure-cosmos-db-java-sdk-v4-with-sql-api-accounts"></a>SQL API hesaplarıyla Java SDK 'Sı v4 Azure Cosmos DB kullandığınızda karşılaşılan sorunları giderme
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [Java SDK’sı v4](troubleshoot-java-sdk-v4-sql.md)
> * [Zaman uyumsuz Java SDK v2](troubleshoot-java-async-sdk.md)
> * [.NET](troubleshoot-dot-net-sdk.md)
> 

> [!IMPORTANT]
> Bu makalede yalnızca Azure Cosmos DB Java SDK v4 için sorun giderme ele alınmaktadır. Daha fazla bilgi için lütfen Azure Cosmos DB Java SDK v4 [sürüm notları](sql-api-sdk-java-v4.md), [Maven deposu](https://mvnrepository.com/artifact/com.azure/azure-cosmos)ve [performans ipuçlarına](performance-tips-java-sdk-v4-sql.md) bakın. Şu anda v4 'den daha eski bir sürüm kullanıyorsanız, v4 'ye yükseltme konusunda yardım için [Azure Cosmos DB Java SDK 'sı v4](migrate-java-v4-sdk.md) Kılavuzu ' na bakın.
>

Bu makalede, Azure Cosmos DB SQL API hesaplarıyla Java SDK 'Sı v4 Azure Cosmos DB kullandığınızda yaygın sorunlar, geçici çözümler, Tanılama adımları ve araçlar ele alınmaktadır.
Azure Cosmos DB Java SDK v4, Azure Cosmos DB SQL API 'sine erişmek için istemci tarafı mantıksal temsili sağlar. Bu makalede, sorunla karşılaştığınızda size yardımcı olacak araçlar ve yaklaşımlar açıklanır.

Bu listeyle başla:

* Bu makaledeki [yaygın sorunlar ve geçici çözümler] bölümüne göz atın.
* [GitHub 'da açık kaynak](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-cosmos)olan Azure Cosmos DB merkezi depoda Java SDK 'sına bakın. Etkin olarak izlenen bir [sorunlar bölümü](https://github.com/Azure/azure-sdk-for-java/issues) vardır. Geçici bir çözüm ile benzer bir sorun olup olmadığını denetleyin. Yardımcı bir ipucu, sorunları *Cosmos: v4-item* etiketine göre filtrelemenize yardımcı olur.
* Java SDK 'Sı v4 Azure Cosmos DB için [performans ipuçlarını](performance-tips-java-sdk-v4-sql.md) gözden geçirin ve önerilen uygulamaları izleyin.
* Çözüm bulamazsanız, bu makalenin geri kalanını okuyun. Sonra bir [GitHub sorunu verin](https://github.com/Azure/azure-sdk-for-java/issues). GitHub sorununuz için etiket ekleme seçeneği varsa, *Cosmos: v4-item* etiketi ekleyin.

### <a name="retry-logic"></a>Yeniden deneme mantığı <a id="retry-logics"></a>
Tüm GÇ hatalarında Cosmos DB SDK'sı, SDK'da yeniden deneme uygulanabiliyorsa başarısız işlemi yeniden dener. Herhangi bir hata için yeniden deneme olması iyi bir uygulamadır, ancak özellikle işleme/yeniden deneme yazma hataları olmalıdır. Yeniden deneme mantığı sürekli iyileştirildiğinden, en son SDK 'nın kullanılması önerilir.

1. Okuma ve sorgulama GÇ arızaları, son kullanıcıya sunulmadan SDK tarafından yeniden denenir.
2. Yazma (oluşturma, upsert, Replace, Delete) "Not" ıdempotent, bu nedenle SDK başarısız yazma işlemlerini her zaman yeniden denemeyebilir. Kullanıcının uygulama mantığının hatayı işlemesi ve yeniden denemesi gerekir.
3. [SDK kullanılabilirliği ile Ilgili sorun giderme](troubleshoot-sdk-availability.md) , çok bölgeli Cosmos DB hesaplar için yeniden denemeleri açıklar.

## <a name="common-issues-and-workarounds"></a><a name="common-issues-workarounds"></a>Yaygın sorunlar ve geçici çözümler

### <a name="network-issues-netty-read-timeout-failure-low-throughput-high-latency"></a>Ağ sorunları, netty okuma zaman aşımı hatası, düşük aktarım hızı, yüksek gecikme süresi

#### <a name="general-suggestions"></a>Genel öneriler
En iyi performans için:
* Uygulamanın Azure Cosmos DB hesabınızla aynı bölgede çalıştığından emin olun. 
* Uygulamanın çalıştığı konaktaki CPU kullanımını denetleyin. CPU kullanımı yüzde 50 veya daha fazlaysa, uygulamanızı daha yüksek bir yapılandırmaya sahip bir konakta çalıştırın. Ya da yükü daha fazla makineye dağıtabilirsiniz.
    * Uygulamanızı Azure Kubernetes hizmetinde çalıştırıyorsanız, [CPU kullanımını izlemek Için Azure İzleyicisi 'ni kullanabilirsiniz](../azure-monitor/containers/container-insights-analyze.md).

#### <a name="connection-throttling"></a>Bağlantı azaltma
[Bir konak makinesindeki bağlantı sınırının] veya [Azure SNAT (Pat) bağlantı noktası tükenmesi]nedeniyle bağlantı azaltma gerçekleşecektir.

##### <a name="connection-limit-on-a-host-machine"></a><a name="connection-limit-on-host"></a>Bir konak makinesinde bağlantı sınırı
Red hat gibi bazı Linux sistemleri, Toplam açık dosya sayısı için üst sınıra sahiptir. Linux 'daki yuvalar dosya olarak uygulanır, bu nedenle bu sayı toplam bağlantı sayısını da sınırlar.
Aşağıdaki komutu çalıştırın.

```bash
ulimit -a
```
"Nofile" olarak tanımlanan en fazla izin verilen açık dosya sayısı, bağlantı havuzu boyutunuzun en az çift katı olması gerekir. Daha fazla bilgi için bkz. Java SDK v4 [Performans ipuçları](performance-tips-java-sdk-v4-sql.md)Azure Cosmos DB.

##### <a name="azure-snat-pat-port-exhaustion"></a><a name="snat"></a>Azure SNAT (PAT) bağlantı noktası tükenmesi

Uygulamanız Azure sanal makinelerinde genel IP adresi olmadan dağıtılmışsa, varsayılan olarak [Azure SNAT bağlantı noktaları](../load-balancer/load-balancer-outbound-connections.md#preallocatedports) , sanal makinenizin dışındaki herhangi bir uç noktaya bağlantı kurar. VM 'den Azure Cosmos DB uç noktasına izin verilen bağlantı sayısı [Azure SNAT yapılandırması](../load-balancer/load-balancer-outbound-connections.md#preallocatedports)ile sınırlıdır.

 Azure SNAT bağlantı noktaları yalnızca sanal makinenizin özel bir IP adresi varsa ve VM 'den bir işlem ortak IP adresine bağlanmaya çalıştığında kullanılır. Azure SNAT sınırlamasından kaçınmak için iki geçici çözüm vardır:

* Azure Cosmos DB hizmeti uç noktanızı Azure sanal makineler sanal ağınızın alt ağına ekleyin. Daha fazla bilgi için bkz. [Azure sanal ağ hizmeti uç noktaları](../virtual-network/virtual-network-service-endpoints-overview.md). 

    Hizmet uç noktası etkinleştirildiğinde, istekler artık genel bir IP Azure Cosmos DB ' e gönderilmez. Bunun yerine, sanal ağ ve alt ağ kimliği gönderilir. Yalnızca genel IP 'Lere izin veriliyorsa bu değişiklik güvenlik duvarı düşceye neden olabilir. Bir güvenlik duvarı kullanıyorsanız, hizmet uç noktasını etkinleştirdiğinizde, [sanal ağ ACL 'lerini](/previous-versions/azure/virtual-network/virtual-networks-acl)kullanarak güvenlik duvarına bir alt ağ ekleyin.
* Azure sanal makinenize genel IP atayın.

##### <a name="cant-reach-the-service---firewall"></a><a name="cant-connect"></a>Hizmete ulaşılamıyor-güvenlik duvarı
``ConnectTimeoutException`` SDK 'nın hizmete ulaşamayacağını gösterir.
Doğrudan modunu kullanırken aşağıdakine benzer bir hata alabilirsiniz:
```
GoneException{error=null, resourceAddress='https://cdb-ms-prod-westus-fd4.documents.azure.com:14940/apps/e41242a5-2d71-5acb-2e00-5e5f744b12de/services/d8aa21a5-340b-21d4-b1a2-4a5333e7ed8a/partitions/ed028254-b613-4c2a-bf3c-14bd5eb64500/replicas/131298754052060051p//', statusCode=410, message=Message: The requested resource is no longer available at the server., getCauseInfo=[class: class io.netty.channel.ConnectTimeoutException, message: connection timed out: cdb-ms-prod-westus-fd4.documents.azure.com/101.13.12.5:14940]
```

Uygulama makinenizde çalışan bir güvenlik duvarınız varsa, doğrudan mod tarafından kullanılan 10.000 numaralı bağlantı noktası aralığını 20.000 ' i açın.
Ayrıca, [bir konak makinesindeki bağlantı sınırını](#connection-limit-on-host)izleyin.

#### <a name="http-proxy"></a>HTTP proxy 'si

Bir HTTP proxy kullanıyorsanız, SDK 'da yapılandırılan bağlantı sayısını destekleyediğinden emin olun `ConnectionPolicy` .
Aksi halde bağlantı sorunlarıyla karşılaşın.

#### <a name="invalid-coding-pattern-blocking-netty-io-thread"></a>Geçersiz kodlama stili: Netty GÇ iş parçacığını engelleme

SDK, Azure Cosmos DB iletişim kurmak için [Netty](https://netty.io/) GÇ kitaplığını kullanır. SDK 'nın zaman uyumsuz bir API 'SI vardır ve blok olmayan GÇ API 'Lerini kullanır. SDK 'nın GÇ işi, GÇ ağ parçacıklarında gerçekleştirilir. GÇ Netty iş parçacıklarının sayısı, uygulama makinesinin CPU çekirdekleri sayısıyla aynı olacak şekilde yapılandırılmıştır. 

Netty GÇ iş parçacıklarının yalnızca engelleyici olmayan Netty GÇ işleri için kullanılması amaçlanmıştır. SDK, netty GÇ iş parçacıklarından birindeki API çağırma sonucunu uygulamanın koduna döndürür. Uygulama, netty iş parçacığında sonuçları aldıktan sonra uzun süreli bir işlem gerçekleştiriyorsa, SDK 'nın iç GÇ işlerini gerçekleştirmek için yeterli GÇ iş parçacığı olmayabilir. Bu tür uygulamalar, düşük aktarım hızı, yüksek gecikme süresi ve hatalara yol açabilir `io.netty.handler.timeout.ReadTimeoutException` . Geçici çözüm, işlemin zaman aldığını bildiğiniz zaman iş parçacığını geçeceğdir.

Örneğin, bir kapsayıcıya öğeler ekleyen aşağıdaki kod parçacığına göz atın (veritabanını ve kapsayıcıyı ayarlama hakkında rehberlik için [buraya](create-sql-api-java.md) bakın.) Netty iş parçacığında birkaç milisaniyeye sahip olan uzun süreli işler gerçekleştirebilirsiniz. Bu durumda, sonunda GÇ işini işlemek için bir Netty GÇ iş parçacığı bulunmayan bir duruma ulaşabilirsiniz. Sonuç olarak, bir ReadTimeoutException hatası alırsınız.

### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-readtimeout"></a>Java SDK v4 (Maven com. Azure:: Azure-Cosmos) zaman uyumsuz API

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=TroubleshootNeedsSchedulerAsync)]

Geçici çözüm, zaman alan iş parçacığını değiştirmek için kullanılır. Uygulamanız için Scheduler 'ın tek bir örneğini tanımlayın.

### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-scheduler"></a>Java SDK v4 (Maven com. Azure:: Azure-Cosmos) zaman uyumsuz API

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=TroubleshootCustomSchedulerAsync)]

Zaman alan, örneğin, yoğun bir iş veya GÇ 'yi engelleme gibi iş yapmanız gerekebilir. Bu durumda, API 'yi kullanarak iş parçacığını tarafından sağlanmış bir çalışana geçirin `customScheduler` `.publishOn(customScheduler)` .

### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-apply-custom-scheduler"></a>Java SDK v4 (Maven com. Azure:: Azure-Cosmos) zaman uyumsuz API

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=TroubleshootPublishOnSchedulerAsync)]

Kullanarak `publishOn(customScheduler)` , netty GÇ iş parçacığını serbest bırakın ve özel Zamanlayıcı tarafından sağlanmış olan özel iş parçacığına geçiş yapın. Bu değişiklik sorunu çözer. Artık bir hata almazsınız `io.netty.handler.timeout.ReadTimeoutException` .

### <a name="request-rate-too-large"></a>İstek hızı çok büyük
Bu hata, sunucu tarafı hatasıdır. Sağlanan aktarım hızını kullandınız olduğunu gösterir. Daha sonra yeniden deneyin. Bu hatayla sık sık karşılaşırsanız, koleksiyon aktarım hızı ' nda bir artış düşünün.

* **GetRetryAfterInMilliseconds aralıklarında geri alma Uygula**

    Performans testi sırasında, küçük bir istek hızı kısıtlanana kadar yükü artırmanız gerekir. Kısıtlanmamışsa, istemci uygulamasının sunucu tarafından belirtilen yeniden deneme aralığı için geri kapatması gerekir. Geri alma işleminin en düşük olması, yeniden denemeler arasında bekleyen minimum süreyi harcamanızı sağlar.

### <a name="failure-connecting-to-azure-cosmos-db-emulator"></a>Azure Cosmos DB öykünücüye bağlanılırken hata oluştu

Azure Cosmos DB öykünücü HTTPS sertifikası otomatik olarak imzalanır. SDK 'nın öykünücü ile çalışması için öykünücü sertifikasını bir Java TrustStore 'a aktarın. Daha fazla bilgi için bkz. [Azure Cosmos DB öykünücü sertifikalarını dışarı aktarma](local-emulator-export-ssl-certificates.md).

### <a name="dependency-conflict-issues"></a>Bağımlılık çakışma sorunları

Azure Cosmos DB Java SDK 'Sı bir dizi bağımlılığı çeker; Genellikle, proje bağımlılığı ağacınıza Azure Cosmos DB Java SDK 'sının bağlı olduğu daha eski bir sürümü varsa, bu, uygulamanızı çalıştırdığınızda beklenmeyen hataların üretilmesine neden olabilir. Uygulamanızın neden beklenmedik bir şekilde özel durum oluşturduğundan hata ayıklaması yapıyorsanız, bağımlılık ağacınızdaki bir veya daha fazla Azure Cosmos DB Java SDK bağımlılıklarından yanlışlıkla daha eski bir sürümde çekmediğini iki kez kontrol etmeniz iyi bir fikirdir.

Böyle bir sorun için geçici çözüm, proje bağımlılıklarınızın eski sürümde ne olduğunu belirlemektir ve bu eski sürümdeki geçişli bağımlılığı hariç tutabilir ve Java SDK 'nın daha yeni sürümü uygulamasına izin Azure Cosmos DB verir.

Proje bağımlılıklarınızın hangisinin, Java SDK 'sının Azure Cosmos DB daha eski bir sürümünde olduğunu belirlemek için, proje pom.xml dosyanızda aşağıdaki komutu çalıştırın:
```bash
mvn dependency:tree
```
Daha fazla bilgi için bkz. [Maven bağımlılık ağacı Kılavuzu](https://maven.apache.org/plugins/maven-dependency-plugin/examples/resolving-conflicts-using-the-dependency-tree.html).

Projenizin hangi bağımlılığının daha eski bir sürüme bağlı olduğunu öğrendikten sonra, aşağıdaki örnekte yer alan bu LIB 'in bağımlılığını değiştirebilir ve geçişli bağımlılığı dışarıda bırakabilirsiniz (Bu, *reaktör-çekirdeğin* eski bağımlılığı olduğunu varsaymaktadır):

```xml
<dependency>
  <groupId>${groupid-of-lib-which-brings-in-reactor}</groupId>
  <artifactId>${artifactId-of-lib-which-brings-in-reactor}</artifactId>
  <version>${version-of-lib-which-brings-in-reactor}</version>
  <exclusions>
    <exclusion>
      <groupId>io.projectreactor</groupId>
      <artifactId>reactor-core</artifactId>
    </exclusion>
  </exclusions>
</dependency>
```

Daha fazla bilgi için bkz. [geçişli bağımlılığı hariç tutma Kılavuzu](https://maven.apache.org/guides/introduction/introduction-to-optional-and-excludes-dependencies.html).


## <a name="enable-client-sdk-logging"></a><a name="enable-client-sice-logging"></a>İstemci SDK günlük kaydını etkinleştir

Azure Cosmos DB Java SDK v4, Log4J ve logback gibi popüler günlük çerçeveleri içinde oturum açmayı destekleyen oturum açma façlata olarak dolayısıyla slf4j kullanır.

Örneğin, Log4J 'yi günlüğe kaydetme çerçevesi olarak kullanmak istiyorsanız, Java sınıfizinizdeki aşağıdaki-BS 'leri ekleyin.

```xml
<dependency>
  <groupId>org.slf4j</groupId>
  <artifactId>slf4j-log4j12</artifactId>
  <version>${slf4j.version}</version>
</dependency>
<dependency>
  <groupId>log4j</groupId>
  <artifactId>log4j</artifactId>
  <version>${log4j.version}</version>
</dependency>
```

Ayrıca bir Log4J yapılandırması da ekleyin.
```
# this is a sample log4j configuration

# Set root logger level to INFO and its only appender to A1.
log4j.rootLogger=INFO, A1

log4j.category.com.azure.cosmos=INFO
#log4j.category.io.netty=OFF
#log4j.category.io.projectreactor=OFF
# A1 is set to be a ConsoleAppender.
log4j.appender.A1=org.apache.log4j.ConsoleAppender

# A1 uses PatternLayout.
log4j.appender.A1.layout=org.apache.log4j.PatternLayout
log4j.appender.A1.layout.ConversionPattern=%d %5X{pid} [%t] %-5p %c - %m%n
```

Daha fazla bilgi için bkz. [sfl4j Logging el kitabı](https://www.slf4j.org/manual.html).

## <a name="os-network-statistics"></a><a name="netstats"></a>İşletim sistemi ağ istatistikleri
Ve gibi durumlarda birçok bağlantı olduğunu öğrenmek için netstat komutunu çalıştırın `ESTABLISHED` `CLOSE_WAIT` .

Linux 'ta, aşağıdaki komutu çalıştırabilirsiniz.
```bash
netstat -nap
```

Windows 'ta aynı komutu farklı bağımsız değişken bayraklarıyla çalıştırabilirsiniz:
```bash
netstat -abn
```

Sonucu yalnızca Azure Cosmos DB uç noktasına bağlantılarla filtreleyin.

Durumdaki Azure Cosmos DB uç noktasına bağlantı sayısı, `ESTABLISHED` yapılandırılmış bağlantı havuzu boyutundan büyük olamaz.

Azure Cosmos DB uç noktasına birçok bağlantı `CLOSE_WAIT` durumunda olabilir. 1.000 ' den fazla olabilir. Yüksek bir sayı, bağlantıların hızlı bir şekilde kurulacağını ve yırtılmış olduğunu gösterir. Bu durum potansiyel olarak soruna neden olur. Daha fazla bilgi için bkz. [genel sorunlar ve geçici çözümler] bölümü.

 <!--Anchors-->
[Yaygın sorunlar ve geçici çözümler]: #common-issues-workarounds
[Enable client SDK logging]: #enable-client-sice-logging
[Bir konak makinesinde bağlantı sınırı]: #connection-limit-on-host
[Azure SNAT (PAT) bağlantı noktası tükenmesi]: #snat
