---
title: PostgreSQL için Azure veritabanı 'nda Kullanıcı oluşturma-tek sunucu
description: Bu makalede, bir PostgreSQL için Azure veritabanı-tek sunucu ile etkileşim kurmak üzere nasıl yeni kullanıcı hesapları oluşturabileceğiniz açıklanır.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2019
ms.openlocfilehash: 91ba485347aeb19ce9b173bd4cec944a655a56dc
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/23/2019
ms.locfileid: "71203504"
---
# <a name="create-users-in-azure-database-for-postgresql---single-server"></a>PostgreSQL için Azure veritabanı 'nda Kullanıcı oluşturma-tek sunucu
Bu makalede, bir PostgreSQL için Azure veritabanı sunucusu içindeki kullanıcıları nasıl oluşturabileceğiniz açıklanmaktadır. 

Azure abonelik kullanıcıları ve bunların ayrıcalıklarını oluşturma ve yönetme hakkında bilgi edinmek istiyorsanız, [Azure rol tabanlı erişim denetimi (RBAC) makalesini](../role-based-access-control/built-in-roles.md) ziyaret edebilir veya [rolleri özelleştirmeyi](../role-based-access-control/custom-roles.md)inceleyebilirsiniz.

## <a name="the-server-admin-account"></a>Sunucu yöneticisi hesabı
PostgreSQL için Azure veritabanınızı ilk oluşturduğunuzda, bir sunucu yöneticisi Kullanıcı adı ve parolası sağladınız. Daha fazla bilgi için, adım adım yaklaşımı görmek üzere [hızlı](quickstart-create-server-database-portal.md) başlangıcı izleyebilirsiniz. Sunucu Yöneticisi Kullanıcı adı özel bir ad olduğundan, seçilen Sunucu Yöneticisi Kullanıcı adını Azure portal bulabilirsiniz.

PostgreSQL için Azure veritabanı sunucusu, tanımlanan 3 varsayılan rolle oluşturulur. Şu komutu çalıştırarak bu rolleri görebilirsiniz:`SELECT rolname FROM pg_roles;`
- azure_pg_admin
- azure_superuser
- Sunucu Yöneticisi kullanıcısı

Sunucu Yöneticisi kullanıcısı, azure_pg_admin rolünün bir üyesidir. Ancak, sunucu yöneticisi hesabı azure_superuser rolünün bir parçası değildir. Bu hizmet yönetilen bir PaaS hizmeti olduğundan, yalnızca Microsoft süper kullanıcı rolünün bir parçasıdır. 

PostgreSQL altyapısı, [PostgreSQL ürün belgelerinde](https://www.postgresql.org/docs/current/static/sql-createrole.html)açıklandığı gibi, veritabanı nesnelerine erişimi denetlemek için ayrıcalıkları kullanır. PostgreSQL için Azure veritabanı 'nda, Sunucu Yöneticisi kullanıcısına şu ayrıcalıklar verilir: LOGIN, NOSUPERUSER, INHERIT, CREATEDB, CREATEROLE, NOREPLICATION

Sunucu Yöneticisi Kullanıcı hesabı, ek kullanıcılar oluşturmak ve bu kullanıcılara azure_pg_admin rolüne vermek için kullanılabilir. Ayrıca, sunucu yöneticisi hesabı, bireysel veritabanlarına ve şemalara erişimi olan daha az ayrıcalıklı kullanıcılar ve Roller oluşturmak için kullanılabilir.

## <a name="how-to-create-additional-admin-users-in-azure-database-for-postgresql"></a>PostgreSQL için Azure veritabanı 'nda ek yönetici kullanıcılar oluşturma
1. Bağlantı bilgilerini ve yönetici kullanıcı adını alın.
   Veritabanı sunucusuna bağlanmak için tam sunucu adı ve yönetici oturum açma kimlik bilgileri gerekir. Sunucu adını ve oturum açma bilgilerini sunucuya **genel bakış** sayfasından veya Azure Portal **Özellikler** sayfasından kolayca bulabilirsiniz. 

2. Veritabanı sunucunuza bağlanmak için yönetici hesabı ve parolasını kullanın. PgAdmin veya psql gibi tercih ettiğiniz istemci aracını kullanın.
   Nasıl bağlanacağınızdan emin değilseniz, bkz [. hızlı başlangıç](./quickstart-create-server-database-portal.md)

3. Aşağıdaki SQL kodunu düzenleyin ve çalıştırın. < New_user > yer tutucu değeri için yeni kullanıcı adınızı değiştirin ve yer tutucu parolasını kendi güçlü parolanızla değiştirin. 

   ```sql
   CREATE ROLE <new_user> WITH LOGIN NOSUPERUSER INHERIT CREATEDB CREATEROLE NOREPLICATION PASSWORD '<StrongPassword!>';
   
   GRANT azure_pg_admin TO <new_user>;
   ```

## <a name="how-to-create-database-users-in-azure-database-for-postgresql"></a>PostgreSQL için Azure veritabanı 'nda veritabanı kullanıcıları oluşturma

1. Bağlantı bilgilerini ve yönetici kullanıcı adını alın.
   Veritabanı sunucusuna bağlanmak için tam sunucu adı ve yönetici oturum açma kimlik bilgileri gerekir. Sunucu adını ve oturum açma bilgilerini sunucuya **genel bakış** sayfasından veya Azure Portal **Özellikler** sayfasından kolayca bulabilirsiniz. 

2. Veritabanı sunucunuza bağlanmak için yönetici hesabı ve parolasını kullanın. PgAdmin veya psql gibi tercih ettiğiniz istemci aracını kullanın.

3. Aşağıdaki SQL kodunu düzenleyin ve çalıştırın. Yer tutucu değerini `<db_user>` amaçlanan Yeni Kullanıcı adınızla ve yer tutucu değerini `<newdb>` kendi veritabanı adınızla değiştirin. Yer tutucu parolasını kendi güçlü parolanızla değiştirin. 

   Bu SQL kod sözdizimi, örneğin, TestDB adlı yeni bir veritabanı oluşturur. Sonra PostgreSQL hizmetinde yeni bir kullanıcı oluşturur ve bu kullanıcı için yeni veritabanına bağlanma ayrıcalıkları verir. 

   ```sql
   CREATE DATABASE <newdb>;
   
   CREATE ROLE <db_user> WITH LOGIN NOSUPERUSER INHERIT CREATEDB NOCREATEROLE NOREPLICATION PASSWORD '<StrongPassword!>';
   
   GRANT CONNECT ON DATABASE <newdb> TO <db_user>;
   ```

4. Yönetici hesabı kullanarak, veritabanındaki nesneleri güvenli hale getirmek için ek ayrıcalıklar vermeniz gerekebilir. Veritabanı rolleri ve ayrıcalıkları hakkında daha fazla bilgi için [PostgreSQL belgelerine](https://www.postgresql.org/docs/current/static/ddl-priv.html) bakın. Örneğin: 
   ```sql
   GRANT ALL PRIVILEGES ON DATABASE <newdb> TO <db_user>;
   ```

5. Yeni Kullanıcı adı ve parolasını kullanarak sunucunuzda oturum açın, belirlenen veritabanını belirtin. Bu örnekte psql komut satırı gösterilmektedir. Bu komutla Kullanıcı adı için parola istenir. Kendi sunucu adı, veritabanı adı ve Kullanıcı adınızı değiştirin.

   ```azurecli-interactive
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=db_user@mydemoserver --dbname=newdb
   ```

## <a name="next-steps"></a>Sonraki adımlar
Bağlantı kurmasını sağlamak için yeni kullanıcıların makinelerinin IP adresleri için güvenlik duvarını açın: Azure portal veya [Azure CLI](howto-manage-firewall-using-cli.md) [kullanarak PostgreSQL için Azure veritabanı güvenlik duvarı kuralları oluşturun ve yönetin](howto-manage-firewall-using-portal.md) .

Kullanıcı hesabı yönetimi hakkında daha fazla bilgi için bkz. PostgreSQL ürün belgeleri için [veritabanı rolleri ve ayrıcalıklar](https://www.postgresql.org/docs/current/static/user-manag.html), [sözdizimi verme](https://www.postgresql.org/docs/current/static/sql-grant.html)ve [ayrıcalıklar](https://www.postgresql.org/docs/current/static/ddl-priv.html).
