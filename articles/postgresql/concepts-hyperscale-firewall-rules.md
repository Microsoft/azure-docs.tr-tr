---
title: PostgreSQL için Azure veritabanı 'nda güvenlik duvarı kuralları-hiper ölçek (Citus)
description: Bu makalede PostgreSQL için Azure veritabanı-hiper ölçek (Citus) güvenlik duvarı kuralları açıklanmaktadır.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 9/12/2019
ms.openlocfilehash: 567fb27ed942a24ab7d031d791e18fa487956fad
ms.sourcegitcommit: a6718e2b0251b50f1228b1e13a42bb65e7bf7ee2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/25/2019
ms.locfileid: "71273734"
---
# <a name="firewall-rules-in-azure-database-for-postgresql---hyperscale-citus"></a>PostgreSQL için Azure veritabanı 'nda güvenlik duvarı kuralları-hiper ölçek (Citus)
PostgreSQL için Azure veritabanı sunucu güvenlik duvarı, hangi bilgisayarların izne sahip olduğunu belirtene kadar Hyperscale (Citus) düzenleyici düğümüne tüm erişimi engeller. Güvenlik Duvarı, her isteğin kaynak IP adresini temel alarak sunucuya erişim izni verir.
Güvenlik duvarınızı yapılandırmak için kabul edilebilir IP adreslerinin aralıklarını belirten güvenlik duvarı kuralları oluşturun. Sunucu düzeyinde güvenlik duvarı kuralları oluşturabilirsiniz.

**Güvenlik duvarı kuralları:** Bu kurallar, istemcilerin Hyperscale (Citus) düzenleyici düğümüne, diğer bir deyişle aynı mantıksal sunucu içindeki tüm veritabanlarına erişmesini sağlar. Sunucu düzeyinde güvenlik duvarı kuralları Azure portal kullanılarak yapılandırılabilir. Sunucu düzeyinde güvenlik duvarı kuralları oluşturmak için, abonelik sahibi veya abonelik katılımcısı olmanız gerekir.

## <a name="firewall-overview"></a>Güvenlik duvarına genel bakış
Koordinatör düğümünüz için tüm veritabanı erişimi, güvenlik duvarı tarafından varsayılan olarak engellenir. Sunucunuzu başka bir bilgisayardan kullanmaya başlamak için, sunucunuza erişimi etkinleştirmek üzere bir veya daha fazla sunucu düzeyi güvenlik duvarı kuralı belirtmeniz gerekir. Internet 'ten izin verilecek IP adresi aralıklarını belirtmek için güvenlik duvarı kurallarını kullanın. Azure portal web sitesinin kendisi için erişim, güvenlik duvarı kurallarından etkilenmez.
Internet 'ten ve Azure 'dan gelen bağlantı girişimleri, aşağıdaki diyagramda gösterildiği gibi ilk olarak, PostgreSQL veritabanınıza ulaşabilmesi için önce güvenlik duvarından geçmesi gerekir:

![Güvenlik duvarının nasıl çalıştığına ilişkin örnek akış](media/concepts-hyperscale-firewall-rules/1-firewall-concept.png)

## <a name="connecting-from-the-internet-and-from-azure"></a>Internet 'ten ve Azure 'dan bağlanma

Hiper ölçek (Citus) sunucu grubu güvenlik duvarı, grubun düzenleyici düğümüne kimlerin bağlanabildiğini denetler. Güvenlik Duvarı, yapılandırılabilir kurallar listesine danışarak erişimi belirler. Her kural, içinde izin verilen bir IP adresidir veya bir adres aralığıdır.

Güvenlik Duvarı bağlantıları engellediğinde, uygulama hatalarına neden olabilir. Örneğin PostgreSQL JDBC sürücüsünü kullanarak, aşağıdaki gibi bir hata oluşturur:

> Java. util. eşzamanlı. ExecutionException: Java. lang. RuntimeException: org. PostgreSQL. util. PSQLException: Önemli: Ana bilgisayar\_"123.45.67.890", Kullanıcı "citus", veritabanı "citus", SSL için pg HBA. conf girişi yok

Kuralların nasıl tanımlandığını öğrenmek için bkz. [güvenlik duvarı kuralları oluşturma ve yönetme](howto-hyperscale-manage-firewall-using-portal.md) .

## <a name="troubleshooting-the-database-server-firewall"></a>Veritabanı sunucusu güvenlik duvarında sorun giderme
PostgreSQL için Microsoft Azure veritabanına erişim-hiper ölçek (Citus) hizmeti, beklendiği gibi davranmıyorsa, şu noktaları göz önünde bulundurun:

* **İzin verilenler listesindeki değişiklikler henüz uygulanmadı:** Hiper ölçek (Citus) güvenlik duvarı yapılandırmasındaki değişikliklerin etkili olabilmesi için beş dakikalık bir gecikme olabilir.

* **Kullanıcı yetkilendirilmemiş veya yanlış bir parola kullanıldı:** Bir kullanıcının sunucuda izinleri yoksa veya kullanılan parola yanlışsa sunucu bağlantısı reddedilir. Bir güvenlik duvarı ayarı oluşturmak, istemcilere yalnızca sunucunuza bağlanmayı denemek için bir fırsat sağlar; her istemci yine de gerekli güvenlik kimlik bilgilerini sağlamalıdır.

Örneğin, bir JDBC istemcisi kullanarak aşağıdaki hata görünebilir.
> Java. util. eşzamanlı. ExecutionException: Java. lang. RuntimeException: org. PostgreSQL. util. PSQLException: ÖNEMLI: "YourUserName" kullanıcısı için parola kimlik doğrulaması başarısız oldu

* **Dinamik IP adresi:** Dinamik IP adresleme ile Internet bağlantınız varsa ve güvenlik duvarından geçiş konusunda sorun yaşıyorsanız, aşağıdaki çözümlerden birini deneyebilirsiniz:

* Hiper ölçek (Citus) düzenleyici düğümüne erişen istemci bilgisayarlarınıza atanan IP adresi aralığı için Internet servis sağlayıcınıza (ISS) sorun ve IP adresi aralığını bir güvenlik duvarı kuralı olarak ekleyin.

* İstemci bilgisayarlarınız yerine statik IP adresleme alın ve statik IP adresini bir güvenlik duvarı kuralı olarak ekleyin.

## <a name="next-steps"></a>Sonraki adımlar
Sunucu düzeyinde ve veritabanı düzeyinde güvenlik duvarı kuralları oluşturma makaleleri için bkz.:
* [Oluşturma ve Azure veritabanı PostgreSQL güvenlik duvarı kuralları için Azure Portalı'nı kullanarak yönetme](howto-hyperscale-manage-firewall-using-portal.md)
