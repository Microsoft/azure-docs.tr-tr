---
title: Geçici hataları işleyin ve MySQL için Azure veritabanı 'na verimli bir şekilde bağlanın | Microsoft Docs
description: Geçici bağlantı hatalarını işleme ve MySQL için Azure veritabanı 'na verimli bir şekilde bağlanma hakkında bilgi edinin.
keywords: MySQL bağlantısı, bağlantı dizesi, bağlantı sorunları, geçici hata, bağlantı hatası, etkin bağlanma
author: jan-eng
ms.author: janeng
ms.service: mysql
ms.topic: conceptual
ms.date: 11/09/2018
ms.openlocfilehash: 4bc5281c891a9d4cd27a48aa365e6cfcec16ad82
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/18/2019
ms.locfileid: "72598265"
---
# <a name="handle-transient-errors-and-connect-efficiently-to-azure-database-for-mysql"></a>MySQL için Azure veritabanı 'na geçici hataları işleyin ve verimli bir şekilde bağlanın

Bu makalede, geçici hataların nasıl işleneceğini ve MySQL için Azure veritabanı 'na verimli bir şekilde nasıl bağlanabileceğinizi açıklamaktadır.

## <a name="transient-errors"></a>Geçici hatalar

Geçici arıza olarak da bilinen geçici bir hata, kendisini çözecek bir hatadır. Genellikle bu hatalar, bırakılmakta olan veritabanı sunucusuna bağlantı olarak bildirimde yer vardır. Ayrıca sunucuya yeni bağlantılar açılamaz. Geçici hatalar, örneğin, donanım veya ağ hatası oluştuğunda meydana gelebilir. Başka bir nedenden dolayı PaaS hizmetinin kullanıma alınmış yeni bir sürümü olabilir. Bu olayların çoğu, sistem tarafından 60 saniyeden daha az bir süre içinde otomatik olarak azalır. Bulutta uygulama tasarlamaya ve geliştirmeye yönelik en iyi yöntem geçici hataların beklenmektedir. Her zaman herhangi bir bileşende ve bu durumları işlemek için uygun mantığın yerine gelebileceğini varsayın.

## <a name="handling-transient-errors"></a>Geçici hataları işleme

Geçici hatalar, yeniden deneme mantığı kullanılarak işlenmelidir. Göz önünde bulundurulması gereken durumlar:

* Bir bağlantıyı açmaya çalıştığınızda bir hata oluşur
* Sunucu tarafında boştaki bir bağlantı bırakılır. Bir komut verilmenize çalıştığınızda yürütülemiyor
* Şu anda bir komutun yürütüldüğü etkin bir bağlantı bırakılır.

İlk ve ikinci durum, işlemek için oldukça ileri bir işlemdir. Bağlantıyı tekrar açmayı deneyin. Başarılı olduğunuzda, geçici hata sistem tarafından azaltılmıştır. MySQL için Azure veritabanınızı yeniden kullanabilirsiniz. Bağlantıyı yeniden denemeden önce beklemeleri önerilir. İlk yeniden denemeler başarısız olursa geri dönün. Bu şekilde, sistem hata durumu aşmak için kullanılabilen tüm kaynakları kullanabilir. İzlenecek iyi bir örüntü şunlardır:

* İlk yeniden denemeden önce 5 saniye bekleyin.
* Aşağıdaki her yeniden denemede, bekleme süresini 60 saniyeye kadar artırın.
* Uygulamanızın işlemi başarısız olduğu noktada en fazla yeniden deneme sayısını ayarlayın.

Etkin bir işlem başarısız olursa, kurtarmanın doğru şekilde işlenmesi daha zordur. İki durum vardır: işlem, doğası halinde salt okunurdur, bağlantıyı yeniden açmak ve işlemi yeniden denemek güvenlidir. Ancak işlem veritabanına yazıyorsanız, işlemin geri alınması veya geçici hata yapılmadan önce başarılı olup olmadığını belirlemelisiniz. Bu durumda, yalnızca veritabanı sunucusundan kayıt onayını almamış olabilirsiniz.

Bunu yapmanın bir yolu, istemci üzerinde tüm yeniden denemeler için kullanılan benzersiz bir KIMLIK oluşturmak içindir. Bu benzersiz KIMLIĞI işlemin bir parçası olarak sunucuya geçirin ve benzersiz bir kısıtlamaya sahip bir sütunda saklayın. Bu şekilde işlemi güvenle yeniden deneyebilirsiniz. Önceki işlem geri alınırsa ve istemci tarafından oluşturulan benzersiz KIMLIK sistemde henüz yoksa başarılı olur. Önceki işlem başarıyla tamamlandığından, benzersiz KIMLIK daha önce depolanıyorsa yinelenen bir anahtar ihlaline işaret eder.

Programınız, üçüncü taraf ara yazılım aracılığıyla MySQL için Azure veritabanı ile iletişim kurduğunda, satıcıdan geçici hatalar için yeniden deneme mantığı içerip içermediğini öğrenin.

Yeniden deneme mantığını test ettiğinizden emin olun. Örneğin, MySQL için Azure veritabanı sunucusu için işlem kaynaklarını ölçeklendirirken veya azaltılarken kodunuzu yürütmeyi deneyin. Uygulamanız herhangi bir sorun olmadan bu işlem sırasında karşılaşılan kısa kapalı kalma süresini işlemelidir.

## <a name="connect-efficiently-to-azure-database-for-mysql"></a>MySQL için Azure veritabanı 'na verimli bir şekilde bağlanma

Veritabanı bağlantıları sınırlı bir kaynaktır; bu nedenle, MySQL için Azure veritabanı 'na erişmek üzere bağlantı havuzunun etkili bir şekilde kullanılmasını sağlamak performansı iyileştirir. Aşağıdaki bölümde, MySQL için Azure veritabanı 'na daha etkin bir şekilde erişmek üzere bağlantı havuzunun veya kalıcı bağlantıların nasıl kullanılacağı açıklanmaktadır.

## <a name="access-databases-by-using-connection-pooling-recommended"></a>Bağlantı havuzu kullanarak veritabanlarına erişin (önerilir)

Veritabanı bağlantılarını yönetmek, uygulamanın performansı üzerinde önemli bir etkiye sahip olabilir. Uygulamanızın performansını iyileştirmek için, hedefin, anahtar kodu yollarında bağlantıların oluşturulması ve zaman sayısını azaltmanız gerekir. MySQL için Azure veritabanı 'na bağlanmak üzere veritabanı bağlantı havuzunun veya kalıcı bağlantıların kullanılması önemle önerilir. Veritabanı bağlantı havuzu, veritabanı bağlantılarının oluşturulmasını, yönetilmesini ve ayrılmasını işler. Bir program bir veritabanı bağlantısı istediğinde, yeni bir bağlantı oluşturmak yerine var olan boştaki veritabanı bağlantılarının ayrılmasını önceliklendirir. Program veritabanı bağlantısını kullanmayı bitirdikten sonra, bağlantı yalnızca kapatılmak yerine daha fazla kullanım için hazırlanmak üzere kurtarılır.

Daha iyi bir çizim için, bu makale örnek olarak JAVA kullanan [bir örnek kod parçası](./sample-scripts-java-connection-pooling.md) sağlar. Daha fazla bilgi için bkz. [Apache Common DBCP](http://commons.apache.org/proper/commons-dbcp/).

> [!NOTE]
> Sunucu, kaynakları boşaltmak için bir süre boşta durumunda olan bir bağlantıyı kapatmak üzere bir zaman aşımı mekanizması yapılandırır. Bunları kullanırken kalıcı bağlantıların verimliliğini sağlamak için doğrulama sistemini ayarladığınızdan emin olun. Daha fazla bilgi için bkz. [kalıcı bağlantıların verimliliğini sağlamak için istemci tarafında doğrulama sistemlerini yapılandırma](concepts-connectivity.md#configure-verification-mechanisms-in-clients-to-confirm-the-effectiveness-of-persistent-connections).

## <a name="access-databases-by-using-persistent-connections-recommended"></a>Kalıcı bağlantılar kullanarak veritabanlarına erişin (önerilir)

Kalıcı bağlantı kavramı bağlantı havuzlarından benzerdir. Kısa bağlantıların kalıcı bağlantılarla değiştirilmesi kodda yalnızca küçük değişiklikler yapılmasını gerektirir, ancak çoğu tipik uygulama senaryosunda performansı iyileştirmek açısından önemli bir etkiye sahiptir.

## <a name="access-databases-by-using-wait-and-retry-mechanism-with-short-connections"></a>Wait ve retry mekanizmasını kısa bağlantılarla kullanarak veritabanlarına erişin

Kaynak sınırlamalarınız varsa, veritabanlarına erişmek için veritabanı havuzunu veya kalıcı bağlantıları kullanmanızı kesinlikle öneririz. Uygulamanız, eşzamanlı bağlantı sayısı üst sınırına yaklaşımında kısa bağlantılar kullanıyorsa ve bağlantı hatalarıyla karşılaşırsanız, bekle ve yeniden deneme mekanizmasını deneyebilirsiniz. İlk denemeden sonra kısa bir bekleme süresi ile uygun bir bekleme süresi ayarlayabilirsiniz. Bundan sonra, olayları birden çok kez beklemeyi deneyebilirsiniz.

## <a name="configure-verification-mechanisms-in-clients-to-confirm-the-effectiveness-of-persistent-connections"></a>Kalıcı bağlantıların verimliliğini onaylamak için istemcilerdeki doğrulama mekanizmalarını yapılandırın

Sunucu, kaynakları boşaltmak için bir süre boşta durumunda olan bir bağlantıyı kapatmak üzere bir zaman aşımı mekanizması yapılandırır. İstemci veritabanına yeniden eriştiğinde, istemci ile sunucu arasında yeni bir bağlantı isteği oluşturmaya eşdeğerdir. Kullanım sürecinde bağlantıların verimliliğini sağlamak için istemcide bir doğrulama mekanizması yapılandırın. Aşağıdaki örnekte gösterildiği gibi, bu doğrulama mekanizmasını yapılandırmak için Tomcat JDBC bağlantı havuzunu kullanabilirsiniz.

Testonödünç Alım parametresini ayarlayarak, yeni bir istek olduğunda, bağlantı havuzu kullanılabilir boştaki bağlantıların verimliliğini otomatik olarak doğrular. Böyle bir bağlantı etkin ise, bu bağlantı havuzu doğrudan geri döndürülür. Bağlantı havuzu daha sonra yeni bir etkin bağlantı oluşturur ve bunu döndürür. Bu işlem veritabanına verimli bir şekilde erişilmesini sağlar. 

Belirli ayarlar hakkında bilgi için bkz. [JDBC bağlantı havuzu resmi giriş belgesi](https://tomcat.apache.org/tomcat-7.0-doc/jdbc-pool.html#Common_Attributes). Genellikle şu üç parametreyi ayarlamanız gerekir: Testonödünç (true olarak ayarlanır), ValidationQuery (SELECT 1 olarak ayarlanır) ve ValidationQueryTimeout (1 olarak ayarlanır). Belirli örnek kod aşağıda gösterilmiştir:

```java
public class SimpleTestOnBorrowExample {
      public static void main(String[] args) throws Exception {
          PoolProperties p = new PoolProperties();
          p.setUrl("jdbc:mysql://localhost:3306/mysql");
          p.setDriverClassName("com.mysql.jdbc.Driver");
          p.setUsername("root");
          p.setPassword("password");
            // The indication of whether objects will be validated by the idle object evictor (if any). 
            // If an object fails to validate, it will be dropped from the pool. 
            // NOTE - for a true value to have any effect, the validationQuery or validatorClassName parameter must be set to a non-null string. 
          p.setTestOnBorrow(true); 

            // The SQL query that will be used to validate connections from this pool before returning them to the caller.
            // If specified, this query does not have to return any data, it just can't throw a SQLException.
          p.setValidationQuery("SELECT 1");

            // The timeout in seconds before a connection validation queries fail. 
            // This works by calling java.sql.Statement.setQueryTimeout(seconds) on the statement that executes the validationQuery. 
            // The pool itself doesn't timeout the query, it is still up to the JDBC driver to enforce query timeouts. 
            // A value less than or equal to zero will disable this feature.
          p.setValidationQueryTimeout(1);
            // set other usefull pool properties.
          DataSource datasource = new DataSource();
          datasource.setPoolProperties(p);

          Connection con = null;
          try {
            con = datasource.getConnection();
            // execute your query here
          } finally {
            if (con!=null) try {con.close();}catch (Exception ignore) {}
          }
      }
  }
```

## <a name="next-steps"></a>Sonraki adımlar

* [MySQL için Azure Veritabanı bağlantı sorunlarını giderme](howto-troubleshoot-common-connection-issues.md)
