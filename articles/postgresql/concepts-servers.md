---
title: PostgreSQL için Azure veritabanı 'nda sunucu kavramları-tek sunucu
description: Bu makalede PostgreSQL için Azure veritabanı 'nın yapılandırılmasına ve yönetilmesine yönelik konular ve tek sunucu için yönergeler sağlanmaktadır.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: bd7c24489e8ac7b26cb703923ddf9cbb741cfd68
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72029975"
---
# <a name="azure-database-for-postgresql---single-server"></a>PostgreSQL için Azure veritabanı-tek sunucu
Bu makalede PostgreSQL için Azure veritabanı-tek sunucu ile çalışmaya yönelik konular ve yönergeler sağlanmaktadır.

## <a name="what-is-an-azure-database-for-postgresql-server"></a>PostgreSQL için Azure veritabanı sunucusu nedir?
PostgreSQL için Azure veritabanı-tek sunuculu dağıtım seçeneği, birden çok veritabanı için merkezi bir yönetim noktasıdır. Bu, şirket içi dünyada bildiğiniz PostgreSQL sunucu yapısıdır. Özellikle PostgreSQL hizmeti yönetiliyor, performans garantisi sağlar, sunucu düzeyinde erişim ve özellikler sunar.

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

PostgreSQL için Azure veritabanı sunucusunda bir veya daha fazla veritabanı oluşturabilirsiniz. Tüm kaynakları kullanmak için sunucu başına tek bir veritabanı oluşturmayı veya kaynakları paylaşmak için birden çok veritabanı oluşturmayı seçebilirsiniz. Fiyatlandırma Katmanı, sanal çekirdek ve depolama (GB) yapılandırmasına bağlı olarak, fiyatlandırma sunucu başına yapılandırılır. Daha fazla bilgi için bkz. [fiyatlandırma katmanları](./concepts-pricing-tiers.md).

## <a name="how-do-i-connect-and-authenticate-to-an-azure-database-for-postgresql-server"></a>Nasıl yaparım? PostgreSQL sunucusu için Azure veritabanı 'na bağlanıp kimlik doğrulaması yapılsın mı?
Aşağıdaki öğeler veritabanınıza güvenli erişimin sağlanmasına yardımcı olur:

|||
|:--|:--|
| **Kimlik doğrulama ve yetkilendirme** | PostgreSQL için Azure veritabanı sunucusu yerel PostgreSQL kimlik doğrulamasını destekler. Sunucu Yöneticisi oturum açma bilgileri ile sunucuya bağlanabilir ve kimlik doğrulaması yapabilirsiniz. |
| **Protokol** | Hizmet, PostgreSQL tarafından kullanılan ileti tabanlı bir protokolü destekler. |
| **TCP/ıP** | Protokol TCP/IP üzerinden ve UNIX-etki alanı Yuvaları üzerinden desteklenir. |
| **Güvenlik duvarı** | Verilerinizin korunmasına yardımcı olmak için, hangi bilgisayarların izne sahip olduğunu belirtene kadar, bir güvenlik duvarı kuralı sunucunuza ve veritabanlarına tüm erişimi engeller. Bkz. [PostgreSQL Için Azure veritabanı sunucu güvenlik duvarı kuralları](concepts-firewall-rules.md). |

## <a name="managing-your-server"></a>Sunucunuzu yönetme
[Azure Portal](https://portal.azure.com) veya [Azure CLI](/cli/azure/postgres)kullanarak PostgreSQL için Azure veritabanı sunucularını yönetebilirsiniz.

Sunucu oluştururken, yönetici kullanıcı için kimlik bilgilerini ayarlarsınız. Yönetici Kullanıcı, sunucuda sahip olduğunuz en yüksek ayrıcalıklı kullanıcı. Azure_pg_admin rolüne aittir. Bu rolün tam süper kullanıcı izinleri yok. 

PostgreSQL süper kullanıcı özniteliği, yönetilen hizmete ait olan azure_superuser öğesine atanır. Bu role erişiminiz yok.

PostgreSQL için Azure veritabanı sunucusu varsayılan veritabanlarına sahiptir: 
- **Postgres** -sunucunuz oluşturulduktan sonra bağlanabilir bir varsayılan veritabanıdır.
- **azure_maintenance** -bu veritabanı, yönetilen hizmet sağlayan işlemleri kullanıcı eylemlerine ayırmak için kullanılır. Bu veritabanına erişiminiz yok.
- **azure_sys** -sorgu deposu için bir veritabanı. Bu veritabanı, sorgu deposu kapalıyken veri toplamaz; Bu, varsayılan ayardır. Daha fazla bilgi için bkz. [sorgu deposuna genel bakış](concepts-query-store.md).


## <a name="server-parameters"></a>Sunucu parametreleri
PostgreSQL sunucu parametreleri sunucunun yapılandırmasını belirlenir. PostgreSQL için Azure veritabanı 'nda, parametre listesi Azure portal veya Azure CLı kullanılarak görüntülenebilir ve düzenlenebilir. 

Postgres için yönetilen bir hizmet olarak, PostgreSQL için Azure veritabanı 'nda yapılandırılabilir parametreler, yerel bir Postgres örneğindeki parametrelerin bir alt kümesidir (Postgres parametreleri hakkında daha fazla bilgi Için bkz. [PostgreSQL belgeleri](https://www.postgresql.org/docs/9.6/static/runtime-config.html)). PostgreSQL için Azure veritabanı sunucusu, oluşturma işlemi sırasında her bir parametre için varsayılan değerlerle etkinleştirilir. Değişikliklerin etkili olması için sunucu yeniden başlatması veya Süper Kullanıcı erişimi gerektiren bazı parametreler Kullanıcı tarafından yapılandırılamaz.


## <a name="next-steps"></a>Sonraki adımlar
- Hizmete genel bakış için bkz. [PostgreSQL Için Azure veritabanı genel bakış](overview.md).
- **Hizmet katmanınıza**dayalı belirli kaynak kotaları ve sınırlamalar hakkında bilgi için bkz. [hizmet katmanları](concepts-pricing-tiers.md).
- Hizmete bağlanma hakkında daha fazla bilgi için bkz. [PostgreSQL Için Azure veritabanı bağlantı kitaplıkları](concepts-connection-libraries.md).
- [Azure Portal](howto-configure-server-parameters-using-portal.md) veya [Azure CLI](howto-configure-server-parameters-using-cli.md)aracılığıyla sunucu parametrelerini görüntüleyin ve düzenleyin.
