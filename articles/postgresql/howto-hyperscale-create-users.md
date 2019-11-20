---
title: PostgreSQL için Azure veritabanı 'nda Kullanıcı oluşturma-hiper ölçek (Citus)
description: Bu makalede, PostgreSQL için Azure veritabanı-hiper ölçek (Citus) ile etkileşim kurmak üzere nasıl yeni kullanıcı hesapları oluşturabileceğiniz açıklanmaktadır.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 7187135b29f0a9a790c032330c73bcb1ae27229b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73515947"
---
# <a name="create-users-in-azure-database-for-postgresql---hyperscale-citus"></a>PostgreSQL için Azure veritabanı 'nda Kullanıcı oluşturma-hiper ölçek (Citus)

Bu makalede, Hyperscale (Citus) sunucu grubu içinde nasıl Kullanıcı oluşturabileceğiniz açıklanmaktadır. Azure abonelik kullanıcıları ve ayrıcalıkları hakkında bilgi edinmek için [Azure rol tabanlı erişim denetimi (RBAC) makalesini](../role-based-access-control/built-in-roles.md) ziyaret edin veya [rolleri nasıl özelleştireceğinizi](../role-based-access-control/custom-roles.md)inceleyin.

## <a name="the-server-admin-account"></a>Sunucu yöneticisi hesabı

Yeni oluşturulan bir hiper ölçek (Citus) sunucu grubu önceden tanımlanmış birkaç rol ile gelir:

* [Varsayılan PostgreSQL rolleri](https://www.postgresql.org/docs/current/default-roles.html)
* *azure_pg_admin*
* *postgres*
* *citus*

Sunucu Yöneticisi kullanıcısı, *citus*, *azure_pg_admin* rolünün bir üyesidir.
Ancak, *Postgres* (Süper Kullanıcı) rolünün bir parçası değildir.  Hiper ölçek yönetilen bir PaaS hizmeti olduğundan, yalnızca Microsoft süper kullanıcı rolünün bir parçasıdır.

PostgreSQL altyapısı, [PostgreSQL ürün belgelerinde](https://www.postgresql.org/docs/current/static/sql-createrole.html)açıklandığı gibi, veritabanı nesnelerine erişimi denetlemek için ayrıcalıkları kullanır.
PostgreSQL için Azure veritabanı 'nda, Sunucu Yöneticisi kullanıcısına şu ayrıcalıklar verilir: LOGIN, NOSUPERUSER, ıNHERIT, CREATEDB, CREATEROLE, NOREPLICATION

## <a name="how-to-create-additional-users"></a>Ek Kullanıcı oluşturma

*Citus* yönetici hesabının ek Kullanıcı oluşturma izni yok. Bir kullanıcı eklemek için, bunun yerine Azure portal kullanın.

1. Hiper ölçek sunucu grubunuzun **Roller** sayfasına gidin ve **+ Ekle**' ye tıklayın:

   ![Roller sayfası](media/howto-hyperscale-create-users/1-role-page.png)

2. Rol adını ve parolayı girin. **Kaydet** düğmesine tıklayın.

   ![Rol Ekle](media/howto-hyperscale-create-users/2-add-user-fields.png)

Kullanıcı, sunucu grubunun düzenleyici düğümünde oluşturulacak ve tüm çalışan düğümlerine yayılacaktır.

## <a name="how-to-delete-a-user-or-change-their-password"></a>Bir kullanıcıyı silme veya parolasını değiştirme

Hiper ölçek sunucu grubunuzun **Roller** sayfasına gidin ve bir kullanıcının yanındaki üç nokta **...** seçeneğine tıklayın. Üç nokta, kullanıcıyı silmek veya parolasını sıfırlamak için bir menü açar.

   ![Rol düzenleme](media/howto-hyperscale-create-users/edit-role.png)

*Citus* rolü ayrıcalıklı ve silinemez.

## <a name="how-to-modify-privileges-for-role"></a>Rol için ayrıcalıkları değiştirme

Yeni roller genellikle kısıtlı ayrıcalıklarla veritabanı erişimi sağlamak için kullanılır. Kullanıcı ayrıcalıklarını değiştirmek için, PgAdmin veya psql gibi bir araç kullanarak standart PostgreSQL komutlarını kullanın. (Bkz. Hyperscale (Citus) hızlı başlangıç bölümünde [psql ile bağlanma](quickstart-create-hyperscale-portal.md#connect-to-the-database-using-psql) .)

Örneğin, *DB_USER* 'in *MyTable*okumasına izin vermek için izin verin:

```sql
GRANT SELECT ON mytable TO db_user;
```

Hiper ölçek (Citus) tek tablo verme deyimlerini tüm çalışan düğümlerine uygulayarak tüm küme aracılığıyla yayar. Ancak, sistem genelinde (bir şemadaki tüm tablolar için), her bir tarih düğümünde çalıştırılması gereken Izin verir.  *Run_command_on_workers ()* yardımcı işlevini kullanın:

```sql
-- applies to the coordinator node
GRANT SELECT ON ALL TABLES IN SCHEMA public TO db_user;

-- make it apply to workers as well
SELECT run_command_on_workers(
  'GRANT SELECT ON ALL TABLES IN SCHEMA public TO db_user;'
);
```

## <a name="next-steps"></a>Sonraki adımlar

Bağlantı kurmasını sağlamak için yeni kullanıcıların makinelerinin IP adresleri için güvenlik duvarını açın: [Azure Portal kullanarak hiper ölçek (Citus) güvenlik duvarı kuralları oluşturun ve yönetin](howto-hyperscale-manage-firewall-using-portal.md).

Veritabanı kullanıcı hesabı yönetimi hakkında daha fazla bilgi için bkz. PostgreSQL ürün belgeleri:

* [Veritabanı rolleri ve ayrıcalıkları](https://www.postgresql.org/docs/current/static/user-manag.html)
* [Sözdizimi verme](https://www.postgresql.org/docs/current/static/sql-grant.html)
* [Ayrıcalıkları](https://www.postgresql.org/docs/current/static/ddl-priv.html)
