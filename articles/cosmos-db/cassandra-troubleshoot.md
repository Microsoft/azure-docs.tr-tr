---
title: Azure Cosmos DB genel hatalarda sorun giderme Cassandra API
description: Bu makalede, Azure Cosmos DB Cassandra API genel sorunlar ve bunların nasıl giderileceği açıklanmaktadır.
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 03/02/2021
ms.author: thvankra
ms.openlocfilehash: e81ab2539527c9d5c5cf2c6bff77fd19887103cd
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105967362"
---
# <a name="troubleshoot-common-issues-in-the-azure-cosmos-db-cassandra-api"></a>Azure Cosmos DB genel sorunları giderme Cassandra API

[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

[Azure Cosmos DB](./introduction.md) Cassandra API, açık kaynaklı Apache Cassandra veritabanı için [kablo protokol desteği](cassandra-support.md) sağlayan bir uyumluluk katmanıdır.

Bu makalede, Azure Cosmos DB Cassandra API kullanan uygulamalar için yaygın hatalar ve çözümler açıklanmaktadır. Hata listelenmiyorsa ve [Cassandra 'da desteklenen bir işlem](cassandra-support.md)yürüttüğünüzde hata yaşarsanız, ancak yerel Apache Cassandra kullanılırken hata mevcut değilse, [bir Azure destek isteği oluşturun](../azure-portal/supportability/how-to-create-azure-support-request.md).

>[!NOTE]
>Tam olarak yönetilen bir bulutta yerel hizmet olarak, Azure Cosmos DB Cassandra API [kullanılabilirlik, aktarım hızı ve tutarlılık hakkında garanti](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_3/) sağlar. Cassandra API ayrıca sıfır bakım platformu işlemlerini ve sıfır kapalı kalma süresi düzeltme ekini kolaylaştırır.
>
>Bu garantiler Apache Cassandra 'nin önceki uygulamalarında mümkün değildir, bu nedenle Cassandra API arka uç işlemlerinin birçoğu Apache Cassandra 'dan farklıdır. Yaygın hatalardan kaçınmaya yardımcı olmak için belirli ayarları ve yaklaşımları öneririz.

## <a name="nonodeavailableexception"></a>NoNodeAvailableException

Bu hata, çok sayıda olası neden ve iç özel durum içeren üst düzey bir sarmalayıcı özel durumdur ve bunların birçoğu istemci ile ilgili olabilir.

Yaygın nedenler ve çözümler:

- **Azure LoadBalancers Için boşta kalma zaman aşımı**: Bu sorun, olarak da bildirim alabilir `ClosedConnectionException` . Bu sorunu çözmek için, sürücüden canlı tut ayarını ayarlayın (bkz. [Java sürücüsü için etkin tutmayı etkinleştirme](#enable-keep-alive-for-the-java-driver)) ve işletim Sisteminizdeki etkin tutma ayarlarını artırma veya [Azure Load Balancer boşta kalma zaman aşımını ayarlama](../load-balancer/load-balancer-tcp-idle-timeout.md?tabs=tcp-reset-idle-portal).

- **İstemci uygulama kaynak tükenmesi**: istemci makinelerin isteği tamamlayacak yeterli kaynağı olduğundan emin olun.

## <a name="cant-connect-to-a-host"></a>Konağa bağlanılamıyor

Şu hatayı görebilirsiniz: "hiçbir konağa bağlanılamıyor, 600000 milisaniye içinde yeniden deneme zamanlanıyor."

Bu hata, istemci tarafındaki kaynak ağ adresi çevirisi (SNAT) tükenmesi nedeniyle oluşmuş olabilir. Bu sorunu kesmek için, [giden bağlantılar Için SNAT](../load-balancer/load-balancer-outbound-connections.md) konumundaki adımları izleyin.

Hata ayrıca, Azure Yük dengeleyicinin varsayılan olarak dört dakikalık boş zaman aşımı olduğu bir boşta kalma zaman aşımı sorunu olabilir. Bkz. [yük dengeleyici boşta zaman aşımı](../load-balancer/load-balancer-tcp-idle-timeout.md?tabs=tcp-reset-idle-portal). [Java sürücüsü için canlı tutmayı etkinleştirin](#enable-keep-alive-for-the-java-driver) ve `keepAlive` işletim sisteminde zaman aralığını dört dakikadan kısa bir süre olarak ayarlayın.

## <a name="overloadedexception-java"></a>OverloadedException (Java)

Tüketilen istek birimlerinin toplam sayısı, anahtar alanı veya tablo üzerinde sağladığınız istek birimi sayısından daha yüksek olduğu için istekler kısıtlanıyor.

Azure Portal bir anahtar alanı veya tabloya atanan aktarım hızını ölçeklendirin (bkz. [Esnek Azure Cosmos DB Cassandra API hesabını ölçeklendirme](manage-scale-cassandra.md)) veya yeniden deneme ilkesi uygulama.

Java için bkz. [v3. x sürücüsü](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample) ve [v4. x sürücüsü](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample-v4)için yeniden deneme örnekleri. Ayrıca bkz. [Java Için Azure Cosmos Cassandra uzantıları](https://github.com/Azure/azure-cosmos-cassandra-extensions).

### <a name="overloadedexception-despite-sufficient-throughput"></a>Yeterli aktarım hızına karşın OverloadedException

İstek hacmi veya tüketilen istek birimi maliyeti için yeterli üretilen iş olsa bile sistem, istekleri daraltma gibi görünüyor. Olası iki nedeni vardır:

- **Şema düzeyi işlemleri**: Cassandra API şema düzeyindeki işlemler (Create Table, alter table, Drop Table) için sistem üretilen iş bütçesini uygular. Bu bütçe, bir üretim sistemindeki şema işlemleri için yeterince olmalıdır. Ancak, çok sayıda şema düzeyi işlem yaptıysanız, bu sınırı aşabilirsiniz.

  Bütçe kullanıcı denetimli olmadığından, çalıştırdığınız şema işlemlerinin sayısını azaltmayı göz önünde bulundurun. Bu eylem sorunu çözmezse veya iş yükünüz için uygun değilse, [bir Azure destek isteği oluşturun](../azure-portal/supportability/how-to-create-azure-support-request.md).

- **Veri eğriltme**: Cassandra API aktarım hızı sağlandığında fiziksel bölümler arasında eşit olarak bölünür ve her fiziksel bölümün üst sınırı vardır. Belirli bir bölümden eklenen veya sorgulanan yüksek miktarda veriniz varsa, bu tablo için büyük miktarda genel üretilen iş (istek birimleri) temin etseniz bile bu hız sınırlı olabilir.

  Veri modelinizi gözden geçirin ve etkin bölümlere neden olabilecek çok fazla eğme olmadığından emin olun.

## <a name="intermittent-connectivity-errors-java"></a>Aralıklı bağlantı hataları (Java)

Bağlantı beklenmedik bir şekilde bırakılır veya zaman aşımına uğrar.

Java için Apache Cassandra sürücüleri iki yerel yeniden bağlantı ilkesi sağlar: `ExponentialReconnectionPolicy` ve `ConstantReconnectionPolicy` . Varsayılan değer: `ExponentialReconnectionPolicy`. Ancak, Azure Cosmos DB Cassandra API için `ConstantReconnectionPolicy` iki saniyelik bir gecikmeyle önerilir.

Java [4. x sürücüsüne yönelik belgelere](https://docs.datastax.com/en/developer/java-driver/4.9/manual/core/reconnection/), [Java 3. x sürücüsüne yönelik belgelere](https://docs.datastax.com/en/developer/java-driver/3.7/manual/reconnection/)veya [Java sürücü örnekleri için reconnectionpolicy yapılandırmasına](#configure-reconnectionpolicy-for-the-java-driver) bakın.

## <a name="error-with-load-balancing-policy"></a>Yük Dengeleme ilkesiyle ilgili hata

Java DataStax sürücüsünün v3. x bölümünde şuna benzer kodla bir yük dengeleme ilkesi uygulamış olabilirsiniz:

```java
cluster = Cluster.builder()
        .addContactPoint(cassandraHost)
        .withPort(cassandraPort)
        .withCredentials(cassandraUsername, cassandraPassword)
        .withPoolingOptions(new PoolingOptions() .setConnectionsPerHost(HostDistance.LOCAL, 1, 2)
                .setMaxRequestsPerConnection(HostDistance.LOCAL, 32000).setMaxQueueSize(Integer.MAX_VALUE))
        .withSSL(sslOptions)
        .withLoadBalancingPolicy(DCAwareRoundRobinPolicy.builder().withLocalDc("West US").build())
        .withQueryOptions(new QueryOptions().setConsistencyLevel(ConsistencyLevel.LOCAL_QUORUM))
        .withSocketOptions(getSocketOptions())
        .build();
```

İçin değeri, `withLocalDc()` iletişim noktası veri merkezi ile eşleşmezse aralıklı bir hatayla karşılaşabilirsiniz: `com.datastax.driver.core.exceptions.NoHostAvailableException: All host(s) tried for query failed (no host was tried)` .

[CosmosLoadBalancingPolicy](https://github.com/Azure/azure-cosmos-cassandra-extensions/blob/master/package/src/main/java/com/microsoft/azure/cosmos/cassandra/CosmosLoadBalancingPolicy.java)uygulayın. Çalışmasını sağlamak için, aşağıdaki kodu kullanarak DataStax ' i yükseltmeniz gerekebilir:

```java
LoadBalancingPolicy loadBalancingPolicy = new CosmosLoadBalancingPolicy.Builder().withWriteDC("West US").withReadDC("West US").build();
```

## <a name="the-count-fails-on-a-large-table"></a>Büyük bir tabloda sayı başarısız oluyor

`select count(*) from table`Çok sayıda satır için çalıştırdığınız veya benzer olduğunda, sunucu zaman aşımına uğrar.

Yerel bir CSQLSH istemcisi kullanıyorsanız, `--connect-timeout` veya `--request-timeout` ayarlarını değiştirin. Bkz. [csqlsh: CQL kabuğu](https://cassandra.apache.org/doc/latest/tools/cqlsh.html).

Sayım hala zaman aşımına uğrarsa, Azure portal ölçüm sekmesine giderek, ölçümü seçerek `document count` ve ardından veritabanı ya da koleksiyon için bir filtre ekleyerek Azure Cosmos DB arka uç telemetrisinden bir kayıt sayısı alabilirsiniz (Azure Cosmos DB tablosunun analog ' u). Daha sonra, zaman içinde kayıt sayısının sayımını yapmak istediğiniz zaman içinde ortaya çıkan grafiğin üzerine gelin.

:::image type="content" source="./media/cassandra-troubleshoot/metrics.png" alt-text="Ölçüm görünümü":::

## <a name="configure-reconnectionpolicy-for-the-java-driver"></a>Java sürücüsü için ReconnectionPolicy yapılandırma

### <a name="version-3x"></a>Sürüm 3. x

Java sürücüsünün 3. x sürümü için, bir küme nesnesi oluştururken yeniden bağlanma ilkesini yapılandırın:

```java
import com.datastax.driver.core.policies.ConstantReconnectionPolicy;

Cluster.builder()
  .withReconnectionPolicy(new ConstantReconnectionPolicy(2000))
  .build();
```

### <a name="version-4x"></a>Sürüm 4. x

Java sürücüsünün 4. x sürümü için, dosyadaki ayarları geçersiz kılarak yeniden bağlanma ilkesini yapılandırın `reference.conf` :

```xml
datastax-java-driver {
  advanced {
    reconnection-policy{
      # The driver provides two implementations out of the box: ExponentialReconnectionPolicy and
      # ConstantReconnectionPolicy. We recommend ConstantReconnectionPolicy for Cassandra API, with 
      # base-delay of 2 seconds.
      class = ConstantReconnectionPolicy
      base-delay = 2 second
    }
}
```

## <a name="enable-keep-alive-for-the-java-driver"></a>Java sürücüsü için canlı tutmayı etkinleştir

### <a name="version-3x"></a>Sürüm 3. x

Java sürücüsünün 3. x. x için, bir küme nesnesi oluştururken canlı tut seçeneğini ayarlayın ve ardından etkin tut özelliğinin [işletim sisteminde etkinleştirildiğinden](https://knowledgebase.progress.com/articles/Article/configure-OS-TCP-KEEPALIVE-000080089)emin olun:

```java
import java.net.SocketOptions;
    
SocketOptions options = new SocketOptions();
options.setKeepAlive(true);
cluster = Cluster.builder().addContactPoints(contactPoints).withPort(port)
  .withCredentials(cassandraUsername, cassandraPassword)
  .withSocketOptions(options)
  .build();
```

### <a name="version-4x"></a>Sürüm 4. x

Java sürücüsünün 4. x sürümü için, ' deki ayarları geçersiz kılarak canlı tut ' u ayarlayın `reference.conf` ve ardından etkin tut ' un [işletim sisteminde etkinleştirildiğinden](https://knowledgebase.progress.com/articles/Article/configure-OS-TCP-KEEPALIVE-000080089)emin olun:

```xml
datastax-java-driver {
  advanced {
    socket{
      keep-alive = true
    }
}
```

## <a name="next-steps"></a>Sonraki adımlar

- Azure Cosmos DB Cassandra API [desteklenen özellikler](cassandra-support.md) hakkında bilgi edinin.
- [Yerel Apache Cassandra 'dan Azure Cosmos DB Cassandra API geçiş](cassandra-migrate-cosmos-db-databricks.md)yapmayı öğrenin.
