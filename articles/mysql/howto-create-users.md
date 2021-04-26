---
title: Veritabanları ve kullanıcılar oluşturma-MySQL için Azure veritabanı
description: Bu makalede, MySQL için Azure veritabanı sunucusu ile etkileşim kurmak üzere yeni kullanıcı hesaplarının nasıl oluşturulacağı açıklanır.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 01/13/2021
ms.openlocfilehash: 9a6346a2b62c81dd74cf0ebe9a85df12d3488679
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98251259"
---
# <a name="create-databases-and-users-in-azure-database-for-mysql"></a>MySQL için Azure veritabanı 'nda veritabanları ve kullanıcılar oluşturma

[!INCLUDE[applies-to-single-flexible-server](includes/applies-to-single-flexible-server.md)]

Bu makalede, MySQL için Azure veritabanı 'nda kullanıcıların nasıl oluşturulacağı açıklanır.

> [!NOTE]
> Bu makale, Microsoft 'un artık kullandığı bir terim olan _bağımlı_ dönem başvuruları içerir. Terim yazılımlardan kaldırıldığında, bu makaleden kaldıracağız.
>

MySQL Server için Azure veritabanınızı ilk oluşturduğunuzda, bir sunucu yöneticisi Kullanıcı adı ve parolası sağladınız. Daha fazla bilgi için bu [hızlı](quickstart-create-mysql-server-database-using-azure-portal.md)başlangıca bakın. Sunucu Yöneticisi Kullanıcı adınızı Azure portal belirleyebilirsiniz.

Sunucu Yöneticisi kullanıcısının şu ayrıcalıkları vardır:

   SEÇME, EKLEME, GÜNCELLEŞTIRME, SILME, OLUŞTURMA, BıRAKMA, YENIDEN YÜKLEME, IŞLEME, BAŞVURULARı, DIZIN, DEĞIŞTIRME, VERITABANLARıNı GÖSTERME, GEÇICI TABLOLAR OLUŞTURMA, TABLOLARı KILITLEME, YÜRÜTME, ÇOĞALTMA BAĞıMLı, ÇOĞALTMA ISTEMCISI, GÖRÜNÜM OLUŞTURMA, GÖRÜNÜMÜ GÖSTERME, RUTIN OLUŞTURMA, RUTIN YORDAM, KULLANıCı OLUŞTURMA, OLAY, TETIKLEYICI

MySQL için Azure veritabanı sunucusu oluşturduktan sonra, daha fazla kullanıcı oluşturmak ve bunlara yönetici erişimi vermek için ilk sunucu yöneticisi hesabını kullanabilirsiniz. Tek tek veritabanı şemalarına erişimi olan daha az ayrıcalıklı kullanıcılar oluşturmak için de sunucu yöneticisi hesabını kullanabilirsiniz.

> [!NOTE]
> Süper ayrıcalık ve DBA rolü desteklenmez. Hizmette Nelerin desteklenmediğini anlamak için sınırlamalar makalesindeki [ayrıcalıkları](concepts-limits.md#privileges--data-manipulation-support) gözden geçirin.
>
> Ve gibi parola `validate_password` eklentileri `caching_sha2_password` hizmeti tarafından desteklenmez.

## <a name="to-create-a-database-with-a-non-admin-user-in-azure-database-for-mysql"></a>MySQL için Azure veritabanı 'nda yönetici olmayan bir kullanıcıyla veritabanı oluşturmak için

1. Bağlantı bilgilerini ve yönetici kullanıcı adını alın.
   Veritabanı sunucusuna bağlanmak için tam sunucu adı ve yönetici oturum açma kimlik bilgileri gerekir. Sunucu adını ve oturum açma bilgilerini sunucuya **genel bakış** sayfasında veya Azure Portal **Özellikler** sayfasında kolayca bulabilirsiniz.

2. Veritabanı sunucunuza bağlanmak için yönetici hesabı ve parolasını kullanın. MySQL çalışma ekranı, mysql.exe veya HeidiSQL gibi tercih ettiğiniz istemci aracını kullanın.

   Nasıl bağlanadığınızdan emin değilseniz, bkz. [tek sunucu için verileri bağlama ve sorgulama](./connect-workbench.md) veya [esnek sunucu için verileri bağlama ve sorgulama](./flexible-server/connect-workbench.md).

3. Aşağıdaki SQL kodunu düzenleyin ve çalıştırın. Yer tutucu değerini `db_user` amaçlanan Yeni Kullanıcı adınızla değiştirin. Yer tutucu değerini `testdb` veritabanı adınızla değiştirin.

   Bu SQL kodu TestDB adlı yeni bir veritabanı oluşturur. Daha sonra MySQL hizmetinde yeni bir kullanıcı oluşturur ve bu kullanıcıya yeni veritabanı şeması (TestDB.) için tüm ayrıcalıklara izin verir \* .

   ```sql
   CREATE DATABASE testdb;

   CREATE USER 'db_user'@'%' IDENTIFIED BY 'StrongPassword!';

   GRANT ALL PRIVILEGES ON testdb . * TO 'db_user'@'%';

   FLUSH PRIVILEGES;
   ```

4. Veritabanına izin verildiğini doğrulayın:

   ```sql
   USE testdb;

   SHOW GRANTS FOR 'db_user'@'%';
   ```

5. Sunucuda, belirlenen veritabanını belirterek ve Yeni Kullanıcı adı ve parolayı kullanarak oturum açın. Bu örnekte MySQL komut satırı gösterilmektedir. Bu komutu kullandığınızda Kullanıcı parolasını girmeniz istenir. Kendi sunucu adı, veritabanı adı ve Kullanıcı adınızı kullanın.

   ### <a name="single-server"></a>[Tek sunucu](#tab/single-server)

   ```azurecli-interactive
   mysql --host mydemoserver.mysql.database.azure.com --database testdb --user db_user@mydemoserver -p
   ```

   ### <a name="flexible-server"></a>[Esnek sunucu](#tab/flexible-server)

   ```azurecli-interactive
   mysql --host mydemoserver.mysql.database.azure.com --database testdb --user db_user -p
   ```
 ---

## <a name="to-create-more-admin-users-in-azure-database-for-mysql"></a>MySQL için Azure veritabanı 'nda daha fazla yönetici kullanıcı oluşturmak için

1. Bağlantı bilgilerini ve yönetici kullanıcı adını alın.
   Veritabanı sunucusuna bağlanmak için tam sunucu adı ve yönetici oturum açma kimlik bilgileri gerekir. Sunucu adını ve oturum açma bilgilerini sunucuya **genel bakış** sayfasında veya Azure Portal **Özellikler** sayfasında kolayca bulabilirsiniz.

2. Veritabanı sunucunuza bağlanmak için yönetici hesabı ve parolasını kullanın. MySQL çalışma ekranı, mysql.exe veya HeidiSQL gibi tercih ettiğiniz istemci aracını kullanın.

   Nasıl bağlanadığınızdan emin değilseniz, bkz. [bağlanmak ve veri sorgulamak Için MySQL çalışma ekranı kullanma](./connect-workbench.md).

3. Aşağıdaki SQL kodunu düzenleyin ve çalıştırın. Yer tutucu değerini `new_master_user` Yeni Kullanıcı adınızla değiştirin. Bu söz dizimi, kullanıcıya tüm veritabanı şemaları (*.*) üzerinde listelenen ayrıcalıkları verir ( `new_master_user` Bu örnekte).

   ```sql
   CREATE USER 'new_master_user'@'%' IDENTIFIED BY 'StrongPassword!';

   GRANT SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, RELOAD, PROCESS, REFERENCES, INDEX, ALTER, SHOW DATABASES, CREATE TEMPORARY TABLES, LOCK TABLES, EXECUTE, REPLICATION SLAVE, REPLICATION CLIENT, CREATE VIEW, SHOW VIEW, CREATE ROUTINE, ALTER ROUTINE, CREATE USER, EVENT, TRIGGER ON *.* TO 'new_master_user'@'%' WITH GRANT OPTION;

   FLUSH PRIVILEGES;
   ```

4. Onayları doğrulayın:

   ```sql
   USE sys;

   SHOW GRANTS FOR 'new_master_user'@'%';
   ```

## <a name="azure_superuser"></a>azure_superuser

MySQL için Azure veritabanı sunucuları, "azure_superuser" adlı bir kullanıcıyla oluşturulur. Bu, Microsoft tarafından, izleme, yedeklemeler ve diğer normal bakım işlemlerini gerçekleştirmek üzere sunucuyu yönetmek için oluşturulan bir sistem hesabıdır. Çağrı tabanlı mühendisler Ayrıca, sertifika kimlik doğrulamasıyla bir olay sırasında sunucuya erişmek için bu hesabı kullanabilir ve tam zamanında (JıT) süreçler kullanarak erişim istemesi gerekir.

## <a name="next-steps"></a>Sonraki adımlar

Bağlantı kurmasını sağlamak için yeni kullanıcıların makinelerinin IP adresleri için güvenlik duvarını açın:

* [Tek bir sunucuda güvenlik duvarı kuralları oluşturma ve yönetme](howto-manage-firewall-using-portal.md)
* [Esnek sunucuda güvenlik duvarı kuralları oluşturma ve yönetme](flexible-server/how-to-connect-tls-ssl.md)

Kullanıcı hesabı yönetimi hakkında daha fazla bilgi için, [Kullanıcı hesabı yönetimi](https://dev.mysql.com/doc/refman/5.7/en/access-control.html), [sözdizimi verme](https://dev.mysql.com/doc/refman/5.7/en/grant.html)ve [ayrıcalıklar](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html)için MySQL ürün belgelerine bakın.
