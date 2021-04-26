---
title: Güvenlik duvarı kuralları-MariaDB için Azure veritabanı
description: MariaDB sunucusu için Azure veritabanı 'na bağlantıları etkinleştirmek üzere güvenlik duvarı kurallarını kullanma hakkında bilgi edinin.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.date: 7/17/2020
ms.openlocfilehash: 452ef4787812dbdf88eb541cf5a164f2888dddcc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98662568"
---
# <a name="azure-database-for-mariadb-server-firewall-rules"></a>MariaDB sunucusu için Azure veritabanı güvenlik duvarı kuralları
Güvenlik duvarları, hangi bilgisayarların izne sahip olduğunu belirtene kadar veritabanı sunucunuza tüm erişimi engeller. Güvenlik Duvarı, her isteğin kaynak IP adresini temel alarak sunucuya erişim izni verir.

Bir güvenlik duvarını yapılandırmak için, kabul edilebilir IP adresleri aralıklarını belirten güvenlik duvarı kuralları oluşturun. Sunucu düzeyinde güvenlik duvarı kuralları oluşturabilirsiniz.

**Güvenlik duvarı kuralları:** Bu kurallar istemcilerin tüm MariaDB sunucusu, diğer bir deyişle aynı mantıksal sunucu içindeki tüm veritabanları için Azure veritabanı 'na erişmesini sağlar. Sunucu düzeyinde güvenlik duvarı kuralları Azure portal veya Azure CLı komutları kullanılarak yapılandırılabilir. Sunucu düzeyinde güvenlik duvarı kuralları oluşturmak için, abonelik sahibi veya abonelik katılımcısı olmanız gerekir.

## <a name="firewall-overview"></a>Güvenlik duvarına genel bakış
MariaDB sunucusu için Azure veritabanınıza yönelik tüm veritabanı erişimi, varsayılan olarak güvenlik duvarı tarafından engelleniyor. Sunucunuzu başka bir bilgisayardan kullanmaya başlamak için, sunucunuza erişimi etkinleştirmek üzere bir veya daha fazla sunucu düzeyi güvenlik duvarı kuralı belirtmeniz gerekir. Internet 'ten izin verilecek IP adresi aralıklarını belirtmek için güvenlik duvarı kurallarını kullanın. Azure portal web sitesinin kendisi için erişim, güvenlik duvarı kurallarından etkilenmez.

Internet 'ten ve Azure 'dan gelen bağlantı girişimleri, aşağıdaki diyagramda gösterildiği gibi, MariaDB veritabanı için Azure veritabanınıza ulaşabilmesi için önce güvenlik duvarından geçmelidir:

![Güvenlik duvarının nasıl çalıştığına ilişkin örnek akış](./media/concepts-firewall-rules/1-firewall-concept.png)

## <a name="connecting-from-the-internet"></a>İnternet'ten bağlanma
Sunucu düzeyinde güvenlik duvarı kuralları, MariaDB sunucusu için Azure veritabanı 'ndaki tüm veritabanları için geçerlidir.

İsteğin IP adresi sunucu düzeyinde güvenlik duvarı kurallarında belirtilen aralıklardan biri içindeyse, bağlantı verilir.

İsteğin IP adresi, veritabanı düzeyinde veya sunucu düzeyinde güvenlik duvarı kurallarında belirtilen aralıkların dışındaysa bağlantı isteği başarısız olur.

## <a name="connecting-from-azure"></a>Azure'dan bağlanma
Herhangi bir uygulamanın veya hizmetin giden IP adresini bulmanız ve bu IP adreslerine veya aralıklarına açıkça erişim izni vermeniz önerilir. Örneğin, bir Azure App Service giden IP adresini bulabilir veya bir sanal makineye ya da başka bir kaynağa bağlı bir genel IP kullanabilirsiniz (sanal makinenin hizmet uç noktaları üzerinden özel IP 'si ile bağlanma hakkında bilgi için aşağıya bakın). 

Azure hizmetiniz için sabit bir giden IP adresi yoksa, tüm Azure veri merkezi IP adreslerinden gelen bağlantıları etkinleştirmeyi deneyebilirsiniz. Bu ayar, **bağlantı güvenliği** bölmesinden **Açık '** a ve **Kaydet**' e basarak **Azure hizmetlerine erişime izin ver** seçeneği ayarlanarak Azure Portal etkinleştirilebilir. Azure CLı 'dan, başlangıç ve bitiş adreslerini 0.0.0.0 değerine eşit bir güvenlik duvarı kuralı ayarı eşdeğerdir. Bağlantı girişimine izin verilmiyorsa, istek, MariaDB sunucusu için Azure veritabanına ulaşmaz.

> [!IMPORTANT]
> **Azure hizmetlerine erişime Izin ver** seçeneği, güvenlik duvarını diğer müşterilerin aboneliklerinden gelen bağlantılar da dahil olmak üzere Azure 'daki tüm bağlantılara izin verecek şekilde yapılandırır. Bu seçeneği belirlerken, oturum açma ve kullanıcı izinlerinizin erişimi yalnızca yetkili kullanıcılarla sınırladığından emin olun.
> 

![Portalda Azure hizmetlerine erişime Izin ver yapılandırma](./media/concepts-firewall-rules/allow-azure-services.png)

### <a name="connecting-from-a-vnet"></a>VNet 'ten bağlanma
VNet 'ten MariaDB sunucusu için Azure veritabanı 'na güvenli bir şekilde bağlanmak için [sanal ağ hizmet uç noktalarını](./concepts-data-access-security-vnet.md)kullanmayı göz önünde bulundurun. 

## <a name="programmatically-managing-firewall-rules"></a>Güvenlik duvarı kurallarını programlı bir şekilde yönetme
Azure portal ek olarak, güvenlik duvarı kuralları Azure CLı kullanılarak programlı bir şekilde yönetilebilir. 

Ayrıca bkz. [Azure CLI kullanarak MariaDB Için Azure veritabanı güvenlik duvarı kuralları oluşturma ve yönetme](./howto-manage-firewall-cli.md).

## <a name="troubleshooting-firewall-issues"></a>Güvenlik Duvarı sorunlarını giderme
MariaDB Server hizmeti için Microsoft Azure veritabanına erişim beklendiği gibi davranmıyorsa aşağıdaki noktaları göz önünde bulundurun:

* **İzin verilenler listesindeki değişiklikler henüz uygulanmadı:** MariaDB sunucusu güvenlik duvarı yapılandırmasının etkili olması için Azure veritabanında yapılan değişiklikler için beş dakikalık bir gecikme olabilir.

* **Oturum açma yetkisi yok veya yanlış parola kullanıldı:** Bir oturum açma, MariaDB sunucusu için Azure veritabanı 'na izin içermiyorsa veya kullanılan parola yanlışsa, MariaDB sunucusu için Azure veritabanı bağlantısı reddedilir. Bir güvenlik duvarı ayarının oluşturulması yalnızca istemcilere sunucunuzla bağlantı kurmayı deneme fırsatı sunar; her istemci gerekli güvenlik kimlik bilgilerini belirtmek zorundadır.

* **Dınamık IP adresi:** Dinamik IP adresleme ile Internet bağlantınız varsa ve güvenlik duvarıyla ilgili sorun yaşıyorsanız, aşağıdaki çözümlerden birini deneyebilirsiniz:

   * Internet servis sağlayıcınıza (ISS), MariaDB sunucusu için Azure veritabanına erişen istemci bilgisayarlarınıza atanan IP adresi aralığı için sorun ve IP adresi aralığını bir güvenlik duvarı kuralı olarak ekleyin.

   * İstemci bilgisayarlarınız için bunun yerine statik IP adresi alın ve IP adreslerini güvenlik duvarı kuralları olarak ekleyin.

* **Sunucunun IP 'si genel gibi görünür:** MariaDB sunucusu için Azure veritabanı 'na bağlantılar, genel olarak erişilebilen bir Azure ağ geçidiyle yönlendirilir. Bununla birlikte gerçek sunucu IP'si güvenlik duvarı tarafından korunur. Daha fazla bilgi için [bağlantı mimarisi makalesi](concepts-connectivity-architecture.md) adresini ziyaret edin. 

* **Azure kaynağından izin VERILEN IP bağlantısı yapılamıyor:** **Microsoft. SQL** hizmet uç noktasının, bağlandığınız alt ağ için etkin olup olmadığını denetleyin. **Microsoft. SQL** etkinse, bu alt ağda yalnızca [VNET hizmet uç noktası kuralları](concepts-data-access-security-vnet.md) kullanmak istediğinizi belirtir.

   Örneğin, **Microsoft. SQL** özellikli ancak karşılık gelen VNET kuralına sahip bir alt ağda Azure VM 'sinden bağlanıyorsanız aşağıdaki hatayı görebilirsiniz:  `FATAL: Client from Azure Virtual Networks is not allowed to access the server`

* **Güvenlik duvarı kuralı IPv6 biçimi için kullanılamaz:** Güvenlik duvarı kuralları IPv4 biçiminde olmalıdır. Güvenlik duvarı kurallarını IPv6 biçiminde belirtirseniz, doğrulama hatası gösterilir.

## <a name="next-steps"></a>Sonraki adımlar
- [Azure portal kullanarak MariaDB için Azure veritabanı güvenlik duvarı kuralları oluşturma ve yönetme](./howto-manage-firewall-portal.md)
- [Azure CLı kullanarak MariaDB için Azure veritabanı güvenlik duvarı kuralları oluşturma ve yönetme](./howto-manage-firewall-cli.md)
- [MariaDB için Azure veritabanı 'nda VNet hizmet uç noktaları](./concepts-data-access-security-vnet.md)
