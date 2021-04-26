---
title: Yaygın hatalarda sorun giderme-MySQL için Azure veritabanı
description: MySQL için Azure veritabanı hizmetine yönelik yeni kullanıcıların karşılaştığı yaygın geçiş hatalarıyla ilgili sorunları nasıl giderebileceğinizi öğrenin
author: savjani
ms.service: mysql
ms.author: pariks
ms.custom: mvc
ms.topic: overview
ms.date: 8/20/2020
ms.openlocfilehash: 3bfcfee0f5dab2d978eb1856bdc915c270d43ed6
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105109806"
---
# <a name="commonly-encountered-errors-during-or-post-migration-to-azure-database-for-mysql-service"></a>MySQL için Azure veritabanı hizmetine geçiş sırasında sıkça karşılaşılan hatalarla karşılaşıldı

[!INCLUDE[applies-to-single-flexible-server](includes/applies-to-single-flexible-server.md)]

MySQL için Azure veritabanı, MySQL 'in topluluk sürümü tarafından desteklenen tam olarak yönetilen bir hizmettir. Yönetilen bir hizmet ortamındaki MySQL deneyimi, kendi ortamınızda MySQL 'i çalıştırmanın farklı olabilir. Bu makalede, kullanıcıların MySQL için Azure veritabanı hizmetine geçiş yaparken veya geliştirme sırasında karşılaşabilecekleri yaygın hatalardan bazılarını görürsünüz.

## <a name="common-connection-errors"></a>Ortak bağlantı hataları

#### <a name="error-1184-08s01-aborted-connection-22-to-db-db-name-user-user-host-hostip-init_connect-command-failed"></a>HATA 1184 (08S01): bağlantı 22 ' den DB 'ye durduruldu: ' DB-adı ' kullanıcısı: ' Kullanıcı ' Konağı: ' hostIP ' (init_connect komutu başarısız oldu)
Yukarıdaki hata, başarılı oturum açma işleminden sonra, ancak oturum oluşturulduğunda herhangi bir komut yürütülmeden önce oluşur. Yukarıdaki ileti, oturum başlatmanın başarısız olmasına neden olan init_connect Server parametresinin yanlış bir değerini ayarlamış olduğunu gösterir.

Oturum düzeyinde desteklenmeyen require_secure_transport gibi bazı sunucu parametreleri vardır ve bu nedenle init_connect kullanarak bu parametrelerin değerlerini değiştirmeye çalışmak, aşağıda gösterildiği gibi MySQL sunucusuna bağlanılırken 1184 hatasına neden olabilir

MySQL> veritabanlarını göster; HATA 2006 (HY000): MySQL sunucusunun bağlantısı yok. Yeniden bağlanmaya çalışılıyor... Bağlantı kimliği: 64897 geçerli veritabanı: * * * yok * * * hata 1184 (08S01): bağlantı 22-veritabanı: ' DB-adı ' kullanıcısı: ' Kullanıcı ' Konağı: ' hostIP ' (init_connect komutu başarısız oldu)

**Çözüm** : Azure Portal içindeki sunucu parametreleri sekmesinde init_connect değeri sıfırlamanız ve yalnızca init_connect parametresini kullanarak desteklenen sunucu parametreleri ayarlamanız gerekir. 


## <a name="errors-due-to-lack-of-super-privilege-and-dba-role"></a>Süper ayrıcalık olmaması ve DBA rolü nedeniyle hatalar oluştu

Süper ayrıcalık ve DBA rolü hizmette desteklenmez. Sonuç olarak, aşağıda listelenen bazı yaygın hatalarla karşılaşabilirsiniz:

#### <a name="error-1419-you-do-not-have-the-super-privilege-and-binary-logging-is-enabled-you-might-want-to-use-the-less-safe-log_bin_trust_function_creators-variable"></a>HATA 1419: süper ayrıcalığa sahip değilsiniz ve ikili günlüğe kaydetme etkin değil (daha az güvenli log_bin_trust_function_creators değişkenini *kullanmak isteyebilirsiniz)*

Bir işlev oluşturulurken yukarıdaki hata oluşabilir, aşağıdaki gibi tetiklenebilir veya bir şema içeri aktarılarak. OLUŞTURMA IşLEVI veya oluşturma TETIKLEYICISI gibi DDL deyimleri ikili günlüğe yazılır, bu nedenle ikincil çoğaltma bunları yürütebilir. Çoğaltma SQL iş parçacığının ayrıcalıkları yükseltmek için yararlanılabilen tam ayrıcalıkları vardır. İkili günlük etkinleştirilmiş sunucular için bu olma tehlikesi 'a karşı koruma sağlamak için, MySQL Engine 'in, gereken olağan oluşturma yordamına ek olarak, saklı işlev oluşturucularının süper ayrıcalığa sahip olması gerekir. 

```sql
CREATE FUNCTION f1(i INT)
RETURNS INT
DETERMINISTIC
READS SQL DATA
BEGIN
  RETURN i;
END;
```

**Çözüm**: hatayı çözümlemek için, Portal 'daki [sunucu parametreleri](howto-server-parameters.md) dikey penceresinden log_bin_trust_function_creators 1 olarak ayarlayın, DDL deyimlerini yürütün veya istenen nesneleri oluşturmak için şemayı içeri aktarın. Gelecekte hata oluşmasını önlemek için sunucunuz için log_bin_trust_function_creators tutmaya devam edebilirsiniz. MySQL [Community belgelerinde](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html#sysvar_log_bin_trust_function_creators) vurgulanan güvenlik riski, hiçbir tehditlere açık olmadığından, MySQL IÇIN Azure DB hizmetinde en az olduğu için log_bin_trust_function_creators ayarlama önerimiz.

#### <a name="error-1227-42000-at-line-101-access-denied-you-need-at-least-one-of-the-super-privileges-for-this-operation-operation-failed-with-exitcode-1"></a>HATA 1227 (42000), satır 101: erişim engellendi; Bu işlem için süper ayrıcalıklar (en az biri) gereklidir. İşlem, ExitCode 1 ile başarısız oldu

Yukarıdaki hata, bir döküm dosyası içeri aktarılırken veya [definers](https://dev.mysql.com/doc/refman/5.7/en/create-procedure.html)içeren yordam oluşturulurken oluşabilir. 

**Çözüm**: Bu hatayı çözmek için Yönetici Kullanıcı, aşağıdaki örneklerde olduğu gıbı, Grant komutunu çalıştırarak yordam oluşturma veya yürütme ayrıcalıkları verebilir:

```sql
GRANT CREATE ROUTINE ON mydb.* TO 'someuser'@'somehost';
GRANT EXECUTE ON PROCEDURE mydb.myproc TO 'someuser'@'somehost';
```
Alternatif olarak, definers 'ı aşağıda gösterildiği gibi içeri aktarma işlemini çalıştıran yönetici kullanıcının adıyla değiştirebilirsiniz.

```sql
DELIMITER;;
/*!50003 CREATE*/ /*!50017 DEFINER=`root`@`127.0.0.1`*/ /*!50003
DELIMITER;;

/* Modified to */

DELIMITER ;;
/*!50003 CREATE*/ /*!50017 DEFINER=`AdminUserName`@`ServerName`*/ /*!50003
DELIMITER ;
```
#### <a name="error-1227-42000-at-line-295-access-denied-you-need-at-least-one-of-the-super-or-set_user_id-privileges-for-this-operation"></a>HATA 1227 (42000), satır 295: erişim engellendi; Bu işlem için süper veya SET_USER_ID ayrıcalıkları (en az biri) gerekir

Döküm dosyası almanın veya bir betik çalıştırmanın parçası olarak DEFINER deyimleriyle oluşturma görünümü yürütülürken yukarıdaki hata ortaya çıkabilir. MySQL için Azure veritabanı, herhangi bir kullanıcının süper ayrıcalıklarına veya SET_USER_ID ayrıcalığına izin vermez. 

**Çözüm**: 
* Mümkünse oluşturma görünümü yürütmek için definer kullanıcısını kullanın. Farklı tanımlı farklı izinleri olan çok sayıda görünüm olduğundan, bu mümkün olmayabilir.  VEYA
* Döküm dosyasını düzenleyin veya görünüm betiği oluşturun ve DEFINER = ifadesini döküm dosyasından kaldırın veya 
* Döküm dosyasını düzenleyin veya görünüm betiği oluşturun ve definer değerlerini Kullanıcı ile içeri aktarma veya yürütme işlemini gerçekleştiren yönetici izinleriyle değiştirin.

> [!Tip] 
> DEFINER = ifadesini değiştirmek için bir döküm dosyasını veya SQL betiğini değiştirmek üzere SED veya Perl kullanın

#### <a name="error-1227-42000-at-line-18-access-denied-you-need-at-least-one-of-the-super-privileges-for-this-operation"></a>18. satırdaki hata 1227 (42000): erişim engellendi; Bu işlem için süper ayrıcalıklar (en az biri) gerekiyor

Bu hata, GTıD özellikli MySQL sunucusundan bir döküm dosyasını MySQL sunucusu için hedef Azure veritabanı sunucusuna aktarmaya çalışmak üzere kullanıyorsanız ortaya çıkabilir. Mysqldump, SET @ @SESSION.sql_log_bin = 0 ifadesini, GTIDs 'nin kullanıldığı bir sunucudan bir döküm dosyasına ekler. Bu, döküm dosyası yeniden yüklenirken ikili günlüğü devre dışı bırakır.

**Çözüm**: Bu hatayı içeri aktarırken, mysqldump dosyanızdaki aşağıdaki satırları kaldırın veya not alın ve başarılı olduğundan emin olmak için içeri aktarmayı yeniden çalıştırın. 

AYARLA @MYSQLDUMP_TEMP_LOG_BIN = @ @SESSION.SQL_LOG_BIN ; AYARLA @ @SESSION.SQL_LOG_BIN = 0; AYARLA @ @GLOBAL.GTID_PURGED = ' '; AYARLA @ @SESSION.SQL_LOG_BIN = @MYSQLDUMP_TEMP_LOG_BIN ;

## <a name="common-connection-errors-for-server-admin-login"></a>Sunucu Yöneticisi oturum açma için ortak bağlantı hataları

MySQL için Azure veritabanı sunucusu oluşturulduğunda, sunucu oluşturma sırasında Son Kullanıcı tarafından Sunucu Yöneticisi oturum açma bilgileri sağlanır. Sunucu Yöneticisi oturum açma, yeni veritabanları oluşturmanıza, yeni kullanıcılar eklemenize ve izin vermenize olanak tanır. Sunucu Yöneticisi oturum açma silinirse, izinleri iptal edilir veya parolası değiştirilirse, bağlantı sırasında uygulamanızdaki bağlantı hatalarını görmeyi başlatabilirsiniz. Yaygın hatalardan bazıları aşağıda verilmiştir

#### <a name="error-1045-28000-access-denied-for-user-usernameip-address-using-password-yes"></a>HATA 1045 (28000): Kullanıcı ' Kullanıcı adı ' @ ' IP adresi ' için erişim engellendi (parola kullanılarak: Evet)

Şu durumlarda yukarıdaki hata oluşur:

* Kullanıcı adı yok
* Kullanıcı Kullanıcı adı silindi
* parolası değiştirilir veya sıfırlanır.

**Çözüm**: 
* "Kullanıcı adı" nın sunucuda geçerli bir kullanıcı olarak var olduğunu veya yanlışlıkla silindiğini doğrulayın. MySQL için Azure veritabanı kullanıcısına oturum açarak aşağıdaki sorguyu yürütebilirsiniz:
  ```sql
  select user from mysql.user;
  ```
* Yukarıdaki sorgunun kendisini yürütmek için MySQL 'de oturum açabiliyorsanız [Azure Portal kullanarak yönetici parolasını sıfırlamanızı](howto-create-manage-server-portal.md)öneririz. Azure portal parola sıfırlama seçeneği, kullanıcıyı yeniden oluşturmaya, parolayı sıfırlamasına ve yönetici izinlerini geri yüklemenize yardımcı olur. Bu, Sunucu Yöneticisi 'ni kullanarak oturum açıp daha fazla işlem gerçekleştirmenize olanak tanır.

## <a name="next-steps"></a>Sonraki adımlar
Aradığınız yanıtı bulamazsanız aşağıdakileri göz önünde bulundurun:

- Sorunuzu [Microsoft Q&soru sayfası](/answers/topics/azure-database-mysql.html) veya [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-database-mysql)olarak gönderin.
- MySQL için Azure veritabanı ekibine bir e-posta gönderin [ @Ask ](mailto:AskAzureDBforMySQL@service.microsoft.com). Bu e-posta adresi bir teknik destek diğer adı değil.
- Azure desteği 'ne başvurun, [Azure Portal bir bilet dosyası](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)yapın. Hesabınızla ilgili bir sorun gidermek için Azure portalda bir [destek isteği](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) oluşturun.
- Görüş bildirmek veya yeni özellikler istemek için [UserVoice](https://feedback.azure.com/forums/597982-azure-database-for-mysql) aracılığıyla bir giriş oluşturun.
