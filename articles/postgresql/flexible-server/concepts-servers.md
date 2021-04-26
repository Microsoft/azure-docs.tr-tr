---
title: PostgreSQL için Azure veritabanı 'nda sunucular-esnek sunucu (Önizleme)
description: Bu makalede, PostgreSQL için Azure veritabanı 'nın yapılandırılmasına ve yönetilmesine yönelik konular ve esnek sunucu hakkında önemli noktalar ve yönergeler sunulmaktadır.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 60edbcf97b43747246e84517cc65bb0dca4ff546
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105626852"
---
# <a name="servers---azure-database-for-postgresql---flexible-server"></a>Sunucular-PostgreSQL için Azure veritabanı-esnek sunucu

> [!IMPORTANT]
> PostgreSQL için Azure veritabanı-esnek sunucu önizlemededir

Bu makalede PostgreSQL için Azure veritabanı-esnek sunucu ile çalışmaya yönelik konular ve yönergeler sağlanmaktadır.

## <a name="what-is-an-azure-database-for-postgresql-server"></a>PostgreSQL için Azure Veritabanı sunucusu nedir?

PostgreSQL için Azure veritabanı-esnek sunucu dağıtımı seçeneği, birden çok veritabanı için merkezi bir yönetim noktasıdır. Bu, şirket içi dünyada bildiğiniz PostgreSQL sunucu yapısıdır. Özellikle PostgreSQL hizmeti yönetiliyor, performans garantisi sağlar, sunucu düzeyinde erişim ve özellikler sunar.

PostgreSQL için Azure veritabanı sunucusu:

- Bir Azure aboneliği içinde oluşturulur.
- , Veritabanları için üst kaynaktır.
- Veritabanları için bir ad alanı sağlar.
- Güçlü yaşam süresi semantiğinin bulunduğu bir kapsayıcıdır; bir sunucuyu silin ve kapsanan veritabanlarını siler.
- Bir bölgedeki kaynakları birlikte bulur.
- Sunucu ve veritabanı erişimi için bir bağlantı uç noktası sağlar
- Veritabanlarına uygulanan yönetim ilkeleri için kapsam sağlar: oturum açma, güvenlik duvarı, kullanıcılar, roller, konfigürasyonlar vb.
- Birden çok sürümde kullanılabilir. Daha fazla bilgi için bkz. [desteklenen PostgreSQL veritabanı sürümleri](concepts-supported-versions.md).
- Kullanıcılara göre genişletilebilir. Daha fazla bilgi için bkz. [PostgreSQL uzantıları](concepts-extensions.md).

PostgreSQL için Azure veritabanı sunucusunda bir veya daha fazla veritabanı oluşturabilirsiniz. Tüm kaynakları kullanmak için sunucu başına tek bir veritabanı oluşturmayı veya kaynakları paylaşmak için birden çok veritabanı oluşturmayı seçebilirsiniz. Fiyatlandırma Katmanı, sanal çekirdek ve depolama (GB) yapılandırmasına bağlı olarak, fiyatlandırma sunucu başına yapılandırılır. Daha fazla bilgi için bkz. [işlem ve depolama seçenekleri](concepts-compute-storage.md).

## <a name="how-do-i-connect-and-authenticate-to-the-database-server"></a>Nasıl yaparım? veritabanı sunucusuna bağlanıp kimlik doğrulaması yapılsın mı?

Aşağıdaki öğeler veritabanınıza güvenli erişimin sağlanmasına yardımcı olur:

| Güvenlik kavramı | Description |
| :-- | :-- |
| **Kimlik doğrulaması ve yetkilendirme** | PostgreSQL için Azure Veritabanı sunucusu, yerel PostgreSQL kimlik doğrulamasını destekler. Sunucunun yönetici hesabını kullanarak sunucuya bağlanabilir ve kimlik doğrulaması gerçekleştirebilirsiniz. |
| **Protokol** | Hizmet, PostgreSQL tarafından kullanılan ileti tabanlı bir protokolü destekler. |
| **TCP/IP** | Protokol TCP/IP üzerinden ve UNIX-etki alanı Yuvaları üzerinden desteklenir. |
| **Güvenlik duvarı** | Verilerinizin korunmasına yardımcı olmak için, hangi bilgisayarların izne sahip olduğunu belirtene kadar, bir güvenlik duvarı kuralı sunucunuza ve veritabanlarına tüm erişimi engeller. Bkz. [PostgreSQL Için Azure veritabanı sunucu güvenlik duvarı kuralları](how-to-manage-firewall-portal.md). |

## <a name="managing-your-server"></a>Sunucunuzu yönetme

[Azure Portal](https://portal.azure.com) veya [Azure CLI](/cli/azure/postgres)kullanarak PostgreSQL için Azure veritabanı sunucularını yönetebilirsiniz.

Sunucu oluştururken, yönetici kullanıcı için kimlik bilgilerini ayarlarsınız. Yönetici Kullanıcı, sunucuda sahip olduğunuz en yüksek ayrıcalıklı kullanıcı. Azure_pg_admin role aittir. Bu rolün tam süper kullanıcı izinleri yok. 

PostgreSQL süper kullanıcı özniteliği, yönetilen hizmete ait olan azure_superuser atanır. Bu role erişiminiz yok.

PostgreSQL için Azure veritabanı sunucusu varsayılan veritabanlarına sahiptir: 

- **Postgres** -sunucunuz oluşturulduktan sonra bağlanabilir bir varsayılan veritabanıdır.
- **azure_maintenance** -bu veritabanı, kullanıcı eylemlerinden yönetilen hizmet sağlayan işlemleri ayırmak için kullanılır. Bu veritabanına erişiminiz yok.

## <a name="server-parameters"></a>Sunucu parametreleri

PostgreSQL sunucu parametreleri sunucunun yapılandırmasını belirlenir. PostgreSQL için Azure veritabanı 'nda, parametre listesi Azure portal veya Azure CLı kullanılarak görüntülenebilir ve düzenlenebilir.

Postgres için yönetilen bir hizmet olarak, PostgreSQL için Azure veritabanı 'nda yapılandırılabilir parametreler, yerel bir Postgres örneğindeki parametrelerin bir alt kümesidir (Postgres parametreleri hakkında daha fazla bilgi Için bkz. [PostgreSQL belgeleri](https://www.postgresql.org/docs/12/static/runtime-config.html)). PostgreSQL için Azure veritabanı sunucusu, oluşturma işlemi sırasında her bir parametre için varsayılan değerlerle etkinleştirilir. Değişikliklerin etkili olması için sunucu yeniden başlatması veya Süper Kullanıcı erişimi gerektiren bazı parametreler Kullanıcı tarafından yapılandırılamaz.

## <a name="next-steps"></a>Sonraki adımlar

- Hizmete genel bakış için bkz. [PostgreSQL Için Azure veritabanı genel bakış](overview.md).
- **Yapılandırmanıza** bağlı olarak belirli kaynak kotaları ve sınırlamalar hakkında bilgi için bkz. [işlem ve depolama seçenekleri](concepts-compute-storage.md).
- [Azure Portal](howto-configure-server-parameters-using-portal.md) veya [Azure CLI](howto-configure-server-parameters-using-cli.md)aracılığıyla sunucu parametrelerini görüntüleyin ve düzenleyin.
