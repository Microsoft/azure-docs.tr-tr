---
title: Güvenlik duvarı kuralları-PostgreSQL için Azure veritabanı-tek sunucu
description: Bu makalede, PostgreSQL için Azure veritabanı 'na bağlanmak üzere Güvenlik Duvarı kurallarının nasıl kullanılacağı açıklanır-tek sunucu.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 07/17/2020
ms.openlocfilehash: ba353cf41cf3876a681f8f18d4121401260ff4ff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98877179"
---
# <a name="firewall-rules-in-azure-database-for-postgresql---single-server"></a>PostgreSQL için Azure veritabanı 'nda güvenlik duvarı kuralları-tek sunucu
PostgreSQL için Azure veritabanı sunucusu, hangi IP konaklarının erişebileceğini belirtene kadar veritabanı sunucunuza tüm erişimi engellemeyi varsayılan olarak güvenlidir. Güvenlik Duvarı, her isteğin kaynak IP adresini temel alarak sunucuya erişim izni verir.
Güvenlik duvarınızı yapılandırmak için kabul edilebilir IP adreslerinin aralıklarını belirten güvenlik duvarı kuralları oluşturun. Sunucu düzeyinde güvenlik duvarı kuralları oluşturabilirsiniz.

**Güvenlik duvarı kuralları:** Bu kurallar, istemcilerin tüm PostgreSQL sunucusu için Azure veritabanı 'na, diğer bir deyişle, aynı mantıksal sunucu içindeki tüm veritabanlarına erişmesini sağlar. Sunucu düzeyinde güvenlik duvarı kuralları Azure portal veya Azure CLı komutları kullanılarak yapılandırılabilir. Sunucu düzeyinde güvenlik duvarı kuralları oluşturmak için, abonelik sahibi veya abonelik katılımcısı olmanız gerekir.

## <a name="firewall-overview"></a>Güvenlik duvarına genel bakış
PostgreSQL için Azure veritabanı sunucusuna tüm erişim, varsayılan olarak güvenlik duvarı tarafından engellenir. Sunucunuza başka bir bilgisayardan/istemciden veya uygulamadan erişmek için, sunucunuza erişimi etkinleştirmek üzere bir veya daha fazla sunucu düzeyi güvenlik duvarı kuralı belirtmeniz gerekir. İzin verilen genel IP adresi aralıklarını belirtmek için güvenlik duvarı kurallarını kullanın. Azure portal web sitesinin kendisi için erişim, güvenlik duvarı kurallarından etkilenmez.
Internet 'ten ve Azure 'dan gelen bağlantı girişimleri, aşağıdaki diyagramda gösterildiği gibi ilk olarak, PostgreSQL veritabanınıza ulaşabilmesi için önce güvenlik duvarından geçmesi gerekir:

:::image type="content" source="media/concepts-firewall-rules/1-firewall-concept.png" alt-text="Güvenlik duvarının nasıl çalıştığına ilişkin örnek akış":::

## <a name="connecting-from-the-internet"></a>İnternet'ten bağlanma
Sunucu düzeyinde güvenlik duvarı kuralları, PostgreSQL için Azure veritabanı sunucusu üzerindeki tüm veritabanları için geçerlidir. İsteğin kaynak IP adresi sunucu düzeyinde güvenlik duvarı kurallarında belirtilen aralıklardan biri içindeyse, bu bağlantı reddedilir, aksi takdirde reddedilir. Örneğin, uygulamanız PostgreSQL için JDBC sürücüsüyle bağlanıyorsa, güvenlik duvarı bağlantıyı engellediği zaman bu hatayla karşılaşabilirsiniz.
> java.util.concurrent.ExecutionException: Java. lang. RuntimeException: org. PostgreSQL. util. PSQLException: ÖNEMLI: \_ ana bilgisayar "123.45.67.890", Kullanıcı "AdminUser", veritabanı "PostgreSQL", SSL için pg HBA. conf girişi yok

## <a name="connecting-from-azure"></a>Azure'dan bağlanma
Herhangi bir uygulamanın veya hizmetin giden IP adresini bulmanız ve bu IP adreslerine veya aralıklarına açıkça erişim izni vermeniz önerilir. Örneğin, bir Azure App Service giden IP adresini bulabilir veya bir sanal makineye ya da başka bir kaynağa bağlı bir genel IP kullanabilirsiniz (sanal makinenin hizmet uç noktaları üzerinden özel IP 'si ile bağlanma hakkında bilgi için aşağıya bakın). 

Azure hizmetiniz için sabit bir giden IP adresi yoksa, tüm Azure veri merkezi IP adreslerinden gelen bağlantıları etkinleştirmeyi deneyebilirsiniz. Bu ayar, **bağlantı güvenliği** bölmesinden **Açık '** a ve **Kaydet**' e basarak **Azure hizmetlerine erişime izin ver** seçeneği ayarlanarak Azure Portal etkinleştirilebilir. Azure CLı 'dan, başlangıç ve bitiş adreslerini 0.0.0.0 değerine eşit bir güvenlik duvarı kuralı ayarı eşdeğerdir. Bağlantı girişimi güvenlik duvarı kuralları tarafından reddedilirse, PostgreSQL için Azure veritabanı sunucusuna ulaşmaz.

> [!IMPORTANT]
> **Azure hizmetlerine erişime Izin ver** seçeneği, güvenlik duvarını diğer müşterilerin aboneliklerinden gelen bağlantılar da dahil olmak üzere Azure 'daki tüm bağlantılara izin verecek şekilde yapılandırır. Bu seçeneği belirlerken, oturum açma ve kullanıcı izinlerinizin erişimi yalnızca yetkili kullanıcılarla sınırladığından emin olun.
> 

:::image type="content" source="media/concepts-firewall-rules/allow-azure-services.png" alt-text="Portalda Azure hizmetlerine erişime Izin ver yapılandırma":::

## <a name="connecting-from-a-vnet"></a>VNet 'ten bağlanma
VNet 'ten PostgreSQL için Azure veritabanı sunucusuna güvenli bir şekilde bağlanmak için [sanal ağ hizmet uç noktalarını](./concepts-data-access-and-security-vnet.md)kullanmayı göz önünde bulundurun. 

## <a name="programmatically-managing-firewall-rules"></a>Güvenlik duvarı kurallarını programlı bir şekilde yönetme
Azure portal ek olarak, güvenlik duvarı kuralları Azure CLı kullanılarak programlı bir şekilde yönetilebilir.
Ayrıca bkz. [Azure CLI kullanarak PostgreSQL Için Azure veritabanı güvenlik duvarı kuralları oluşturma ve yönetme](howto-manage-firewall-using-cli.md)

## <a name="troubleshooting-firewall-issues"></a>Güvenlik Duvarı sorunlarını giderme
PostgreSQL için Microsoft Azure veritabanına erişim beklendiği gibi davranmıyorsa aşağıdaki noktaları göz önünde bulundurun:

* **İzin verilenler listesindeki değişiklikler henüz uygulanmadı:** PostgreSQL için Azure veritabanı sunucu güvenlik duvarı yapılandırmasının etkili olması için beş dakikalık bir gecikme olabilir.

* **Oturum açma yetkisi yok veya yanlış parola kullanıldı:** Bir oturum açma işlemi PostgreSQL için Azure veritabanı sunucusunda izinlere sahip değilse veya kullanılan parola yanlışsa, PostgreSQL için Azure veritabanı sunucusuna bağlantı reddedilir. Bir güvenlik duvarı ayarı oluşturmak, istemcilere yalnızca sunucunuza bağlanmayı denemek için bir fırsat sağlar; her istemci yine de gerekli güvenlik kimlik bilgilerini sağlamalıdır.

   Örneğin, bir JDBC istemcisi kullanarak aşağıdaki hata görünebilir.
   > java.util.concurrent.ExecutionException: Java. lang. RuntimeException: org. PostgreSQL. util. PSQLException: ÖNEMLI: Kullanıcı için parola kimlik doğrulaması başarısız oldu "YourUserName"

* **Dinamik IP adresi:** Dinamik IP adresiyle kurulmuş bir İnternet bağlantınız varsa ve güvenlik duvarını aşmakta sorun yaşıyorsanız aşağıdaki çözümlerden birini deneyebilirsiniz:

   * PostgreSQL için Azure veritabanı sunucusuna erişen istemci bilgisayarlarınıza atanan IP adresi aralığı için Internet servis sağlayıcınıza (ISS) sorun ve IP adresi aralığını bir güvenlik duvarı kuralı olarak ekleyin.

   * İstemci bilgisayarlarınız yerine statik IP adresleme alın ve statik IP adresini bir güvenlik duvarı kuralı olarak ekleyin.

* **Sunucunun IP 'si genel gibi görünür:** PostgreSQL için Azure veritabanı sunucusuna yapılan bağlantılar, genel olarak erişilebilen bir Azure ağ geçidiyle yönlendirilir. Bununla birlikte gerçek sunucu IP'si güvenlik duvarı tarafından korunur. Daha fazla bilgi için [bağlantı mimarisi makalesi](concepts-connectivity-architecture.md) adresini ziyaret edin.

* **Azure kaynağından izin VERILEN IP bağlantısı yapılamıyor:** **Microsoft. SQL** hizmet uç noktasının, bağlandığınız alt ağ için etkin olup olmadığını denetleyin. **Microsoft. SQL** etkinse, bu alt ağda yalnızca [VNET hizmet uç noktası kuralları](concepts-data-access-and-security-vnet.md) kullanmak istediğinizi belirtir.

   Örneğin, **Microsoft. SQL** özellikli ancak karşılık gelen VNET kuralına sahip bir alt ağda Azure VM 'sinden bağlanıyorsanız aşağıdaki hatayı görebilirsiniz:  `FATAL: Client from Azure Virtual Networks is not allowed to access the server`

* **Güvenlik duvarı kuralı IPv6 biçimi için kullanılamaz:** Güvenlik duvarı kuralları IPv4 biçiminde olmalıdır. Güvenlik duvarı kurallarını IPv6 biçiminde belirtirseniz, doğrulama hatası gösterilir.


## <a name="next-steps"></a>Sonraki adımlar
* [Azure portal kullanarak PostgreSQL için Azure veritabanı güvenlik duvarı kuralları oluşturma ve yönetme](howto-manage-firewall-using-portal.md)
* [Azure CLı kullanarak PostgreSQL için Azure veritabanı güvenlik duvarı kuralları oluşturma ve yönetme](howto-manage-firewall-using-cli.md)
* [PostgreSQL için Azure veritabanı 'ndaki VNet hizmet uç noktaları](./concepts-data-access-and-security-vnet.md)
