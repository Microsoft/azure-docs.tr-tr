---
title: PostgreSQL için Azure veritabanı 'nda güvenlik duvarı kuralları-tek sunucu
description: Bu makalede PostgreSQL için Azure veritabanı-tek sunucu güvenlik duvarı kuralları açıklanmaktadır.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/25/2019
ms.openlocfilehash: 6b4896d78bcc6e9fc1f5d9cd47e60e3df7eba325
ms.sourcegitcommit: c4700ac4ddbb0ecc2f10a6119a4631b13c6f946a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/27/2019
ms.locfileid: "72965275"
---
# <a name="firewall-rules-in-azure-database-for-postgresql---single-server"></a>PostgreSQL için Azure veritabanı 'nda güvenlik duvarı kuralları-tek sunucu
PostgreSQL için Azure veritabanı sunucu güvenlik duvarı, hangi bilgisayarların izin olduğunu belirtene kadar veritabanı sunucunuza tüm erişimi engeller. Güvenlik Duvarı, her isteğin kaynak IP adresini temel alarak sunucuya erişim izni verir.
Güvenlik duvarınızı yapılandırmak için kabul edilebilir IP adreslerinin aralıklarını belirten güvenlik duvarı kuralları oluşturun. Sunucu düzeyinde güvenlik duvarı kuralları oluşturabilirsiniz.

**Güvenlik duvarı kuralları:** Bu kurallar, istemcilerin tüm PostgreSQL sunucusu için Azure veritabanı 'na, diğer bir deyişle, aynı mantıksal sunucu içindeki tüm veritabanlarına erişmesini sağlar. Sunucu düzeyinde güvenlik duvarı kuralları Azure portal veya Azure CLı komutları kullanılarak yapılandırılabilir. Sunucu düzeyinde güvenlik duvarı kuralları oluşturmak için, abonelik sahibi veya abonelik katılımcısı olmanız gerekir.

## <a name="firewall-overview"></a>Güvenlik duvarına genel bakış
PostgreSQL için Azure veritabanı sunucunuza yönelik tüm veritabanı erişimi, güvenlik duvarı tarafından varsayılan olarak engellenir. Sunucunuzu başka bir bilgisayardan kullanmaya başlamak için, sunucunuza erişimi etkinleştirmek üzere bir veya daha fazla sunucu düzeyi güvenlik duvarı kuralı belirtmeniz gerekir. Internet 'ten izin verilecek IP adresi aralıklarını belirtmek için güvenlik duvarı kurallarını kullanın. Azure portal web sitesinin kendisi için erişim, güvenlik duvarı kurallarından etkilenmez.
Internet 'ten ve Azure 'dan gelen bağlantı girişimleri, aşağıdaki diyagramda gösterildiği gibi ilk olarak, PostgreSQL veritabanınıza ulaşabilmesi için önce güvenlik duvarından geçmesi gerekir:

![Güvenlik duvarının nasıl çalıştığına ilişkin örnek akış](media/concepts-firewall-rules/1-firewall-concept.png)

## <a name="connecting-from-the-internet"></a>İnternet'ten bağlanma
Sunucu düzeyinde güvenlik duvarı kuralları, PostgreSQL için Azure veritabanı sunucusu üzerindeki tüm veritabanları için geçerlidir. İstek IP adresi sunucu düzeyinde güvenlik duvarı kurallarında belirtilen aralıklardan biri içindeyse, bağlantı izni verilir.
İsteğin IP adresi sunucu düzeyinde güvenlik duvarı kurallarında belirtilen aralıklar dahilinde değilse, bağlantı isteği başarısız olur.
Örneğin, uygulamanız PostgreSQL için JDBC sürücüsüyle bağlanıyorsa, güvenlik duvarı bağlantıyı engellediği zaman bu hatayla karşılaşabilirsiniz.
> Java. util. eşzamanlı. ExecutionException: Java. lang. RuntimeException: org. PostgreSQL. util. PSQLException: ÖNEMLI: Ana bilgisayar "123.45.67.890", Kullanıcı "AdminUser", veritabanı "PostgreSQL", SSL için bir PG\_HBA. conf girdisi

## <a name="connecting-from-azure"></a>Azure'dan bağlanma
Azure 'daki uygulamaların PostgreSQL için Azure veritabanı sunucusuna bağlanmasına izin vermek için, Azure bağlantılarının etkinleştirilmesi gerekir. Örneğin, bir Azure Web Apps uygulamasını veya bir Azure VM 'de çalışan bir uygulamayı barındırmak ya da bir Azure Data Factory veri yönetimi ağ geçidinden bağlanmak için. Bu bağlantıları etkinleştirmek için kaynakların, güvenlik duvarı kuralı için aynı sanal ağ (VNet) veya kaynak grubunda olması gerekmez. Azure’dan bir uygulama, veritabanı sunucunuza bağlanmayı denediğinizde güvenlik duvarı Azure bağlantılarına izin verildiğini doğrular. Bu tür bağlantıları etkinleştirmek için birkaç yöntem vardır. Başlangıç ve bitiş adresi 0.0.0.0’a eşit olan bir güvenlik duvarı ayarı, bu bağlantılara izin verildiğini gösterir. Alternatif olarak, **bağlantı güvenliği** bölmesinden portalda **Azure hizmetlerine erişime izin ver** seçeneğini ayarlayabilir ve **Kaydet** **' e basın** . Bağlantı girişimine izin verilmiyorsa, istek PostgreSQL için Azure veritabanı sunucusuna ulaşmaz.

> [!IMPORTANT]
> Bu seçenek, diğer müşterilerin aboneliklerinden gelen bağlantılar dahil Azure’dan tüm bağlantılara izin verecek şekilde güvenlik duvarınızı yapılandırır. Bu seçeneği belirlerken, oturum açma ve kullanıcı izinlerinizin erişimi yalnızca yetkili kullanıcılarla sınırladığından emin olun.
> 

![Portalda Azure hizmetlerine erişime Izin ver yapılandırma](media/concepts-firewall-rules/allow-azure-services.png)

### <a name="connecting-from-a-vnet"></a>VNet 'ten bağlanma
VNet 'ten PostgreSQL için Azure veritabanı sunucusuna güvenli bir şekilde bağlanmak için [sanal ağ hizmet uç noktalarını](./concepts-data-access-and-security-vnet.md)kullanmayı göz önünde bulundurun. 

## <a name="programmatically-managing-firewall-rules"></a>Güvenlik duvarı kurallarını programlı bir şekilde yönetme
Azure portal ek olarak, güvenlik duvarı kuralları Azure CLı kullanılarak programlı bir şekilde yönetilebilir.
Ayrıca bkz. [Azure CLI kullanarak PostgreSQL Için Azure veritabanı güvenlik duvarı kuralları oluşturma ve yönetme](howto-manage-firewall-using-cli.md)

## <a name="troubleshooting-firewall-issues"></a>Güvenlik Duvarı sorunlarını giderme
PostgreSQL için Microsoft Azure veritabanına erişim beklendiği gibi davranmıyorsa aşağıdaki noktaları göz önünde bulundurun:

* **İzin verilenler listesindeki değişiklikler henüz uygulanmadı:** PostgreSQL için Azure veritabanı sunucu güvenlik duvarı yapılandırmasının etkili olması için beş dakikalık bir gecikme olabilir.

* **Oturum açma yetkisi yok veya yanlış parola kullanıldı:** Bir oturum açma işlemi PostgreSQL için Azure veritabanı sunucusunda izinlere sahip değilse veya kullanılan parola yanlışsa, PostgreSQL için Azure veritabanı sunucusuna bağlantı reddedilir. Bir güvenlik duvarı ayarı oluşturmak, istemcilere yalnızca sunucunuza bağlanmayı denemek için bir fırsat sağlar; her istemci yine de gerekli güvenlik kimlik bilgilerini sağlamalıdır.

   Örneğin, bir JDBC istemcisi kullanarak aşağıdaki hata görünebilir.
   > Java. util. eşzamanlı. ExecutionException: Java. lang. RuntimeException: org. PostgreSQL. util. PSQLException: ÖNEMLI: "YourUserName" kullanıcısı için parola kimlik doğrulaması başarısız oldu

* **Dinamik IP adresi:** Dinamik IP adresiyle kurulmuş bir İnternet bağlantınız varsa ve güvenlik duvarını aşmakta sorun yaşıyorsanız aşağıdaki çözümlerden birini deneyebilirsiniz:

   * PostgreSQL için Azure veritabanı sunucusuna erişen istemci bilgisayarlarınıza atanan IP adresi aralığı için Internet servis sağlayıcınıza (ISS) sorun ve IP adresi aralığını bir güvenlik duvarı kuralı olarak ekleyin.

   * İstemci bilgisayarlarınız yerine statik IP adresleme alın ve statik IP adresini bir güvenlik duvarı kuralı olarak ekleyin.

* **Sunucunun IP 'si genel gibi görünür:** PostgreSQL için Azure veritabanı sunucusuna yapılan bağlantılar, genel olarak erişilebilen bir Azure ağ geçidiyle yönlendirilir. Bununla birlikte gerçek sunucu IP'si güvenlik duvarı tarafından korunur. Daha fazla bilgi için [bağlantı mimarisi makalesi](concepts-connectivity-architecture.md) adresini ziyaret edin. 

## <a name="next-steps"></a>Sonraki adımlar
Sunucu düzeyinde ve veritabanı düzeyinde güvenlik duvarı kuralları oluşturma makaleleri için bkz.:
* [Azure portal kullanarak PostgreSQL için Azure veritabanı güvenlik duvarı kuralları oluşturma ve yönetme](howto-manage-firewall-using-portal.md)
* [Azure CLı kullanarak PostgreSQL için Azure veritabanı güvenlik duvarı kuralları oluşturma ve yönetme](howto-manage-firewall-using-cli.md)
- [PostgreSQL için Azure veritabanı 'ndaki VNet hizmet uç noktaları](./concepts-data-access-and-security-vnet.md)
