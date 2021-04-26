---
title: Güvenlik duvarı kurallarını yönetme-Azure portal-PostgreSQL için Azure veritabanı-esnek sunucu
description: PostgreSQL için Azure veritabanı için güvenlik duvarı kuralları oluşturma ve yönetme-Azure portal kullanarak esnek sunucu
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 36f282571dc0f3b8f6b9298c23042c955bc59058
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107225759"
---
# <a name="create-and-manage-firewall-rules-for-azure-database-for-postgresql---flexible-server-using-the-azure-portal"></a>PostgreSQL için Azure veritabanı için güvenlik duvarı kuralları oluşturma ve yönetme-Azure portal kullanarak esnek sunucu

> [!IMPORTANT]
> PostgreSQL için Azure veritabanı-esnek sunucu şu anda genel önizlemededir.

PostgreSQL için Azure veritabanı-esnek sunucu, esnek sunucunuza bağlanmak için iki tür karşılıklı kullanım dışı ağ bağlantısı yöntemini destekler. Bu iki seçenek şunlardır:

* Genel erişim (izin verilen IP adresleri)
* Özel erişim (Sanal Ağ Tümleştirmesi)

Bu makalede, Azure portal kullanarak **genel erişim (izin VERILEN IP adresleri)** Ile PostgreSQL sunucusu oluşturmaya odaklanacağız ve esnek sunucu oluşturulduktan sonra güvenlik duvarı kurallarını yönetmeye ilişkin bir genel bakış sağlanır. *Ortak erişim (izin VERILEN IP adresleri)* Ile, PostgreSQL sunucusuna yapılan bağlantılar yalnızca ızın verilen IP adresleriyle kısıtlıdır. Güvenlik duvarı kurallarında istemci IP adreslerine izin verilmesi gerekir. Daha fazla bilgi edinmek için bkz. [genel erişim (izin VERILEN IP adresleri)](./concepts-networking.md#public-access-allowed-ip-addresses). Güvenlik duvarı kuralları sunucu oluşturma sırasında tanımlanabilir (önerilir), ancak daha sonra da eklenebilir. Bu makalede, genel erişimi (izin verilen IP adresleri) kullanarak güvenlik duvarı kuralları oluşturma ve yönetme konusunda genel bir bakış sağlıyoruz.

## <a name="create-a-firewall-rule-when-creating-a-server"></a>Sunucu oluştururken bir güvenlik duvarı kuralı oluşturma

1. Portalın sol üst köşesinde bulunan **kaynak oluştur** (+) seçeneğini belirleyin.
2. **Veritabanları**  >  **PostgreSQL için Azure veritabanı**' nı seçin. Hizmeti bulmak için arama kutusuna **PostgreSQL** ' i de girebilirsiniz.
3. Dağıtım seçeneği olarak **esnek sunucu** ' yı seçin.
4. **Temel bilgiler** formunu doldurun.
5. Sunucunuza nasıl bağlanmak istediğinizi yapılandırmak için **ağ** sekmesine gidin.
6. **Bağlantı yönteminde**, *genel erişim (izin verilen IP adresleri)* öğesini seçin. **Güvenlik duvarı kuralları** oluşturmak Için, güvenlik duvarı kuralı adını ve tek IP adresini veya bir adres aralığını belirtin. Kuralı tek bir IP adresiyle sınırlandırmak istiyorsanız, başlangıç IP adresi ve bitiş IP adresi alanına aynı adresi yazın. Güvenlik duvarının açılması, yöneticilerin, kullanıcıların ve uygulamaların geçerli kimlik bilgilerine sahip oldukları PostgreSQL sunucusundaki herhangi bir veritabanına erişmesine olanak sağlar.
   > [!Note]
   > PostgreSQL için Azure veritabanı-esnek sunucu, sunucu düzeyinde bir güvenlik duvarı oluşturur. Bu güvenlik duvarı, belirli IP adresleri için güvenlik duvarını açmak üzere bir kural oluşturmadığınız sürece, dış uygulama ve araçların sunucuya ve sunucu üzerindeki herhangi bir veritabanına bağlanmasını engeller.
7. Esnek sunucu yapılandırmanızı gözden geçirmek için **gözden geçir + oluştur** ' u seçin.
8.  Sunucuyu sağlamak için **Oluştur**’u seçin. Sağlama birkaç dakika sürebilir.

## <a name="create-a-firewall-rule-after-server-is-created"></a>Sunucu oluşturulduktan sonra bir güvenlik duvarı kuralı oluşturma

1. [Azure Portal](https://portal.azure.com/), güvenlik duvarı kuralları eklemek Istediğiniz PostgreSQL Için Azure veritabanı-esnek sunucu ' yı seçin.
2. Esnek sunucu sayfasında, **Ayarlar** başlığı altında, esnek sunucu ağ sayfasını açmak Için **ağ iletişimi** ' ne tıklayın.

   <!--![Azure portal - click Connection Security](./media/howto-manage-firewall-portal/1-connection-security.png)-->

3. Güvenlik duvarı kurallarında **geçerli ISTEMCI IP adresi ekle** ' ye tıklayın. Bu otomatik olarak, Azure sistemi tarafından algılanan şekilde bilgisayarınızın genel IP adresi ile bir güvenlik duvarı kuralı oluşturur.

   <!--![Azure portal - click Add My IP](./media/howto-manage-firewall-portal/2-add-my-ip.png)-->

4. Yapılandırmayı kaydetmeden önce IP adresinizi doğrulayın. Bazı durumlarda Azure portal tarafından gözlenen IP adresi, internet ve Azure sunucularına erişirken kullanılan IP adresinden farklıdır. Bu nedenle, kural işlevini beklendiği gibi yapmak için başlangıç IP adresini ve bitiş IP adresini değiştirmeniz gerekebilir.

   Kendi IP adresinizi denetlemek için bir arama altyapısı veya başka bir çevrimiçi araç kullanabilirsiniz. Örneğin, "IP nedir?" ifadesini aratın.

   <!--![Bing search for What is my IP](./media/howto-manage-firewall-portal/3-what-is-my-ip.png)-->

5. Ek adres aralıkları ekleyin. PostgreSQL için Azure veritabanı-esnek sunucu güvenlik duvarı kurallarında tek bir IP adresi veya bir adres aralığı belirtebilirsiniz. Kuralı tek bir IP adresiyle sınırlandırmak istiyorsanız, başlangıç IP adresi ve bitiş IP adresi alanına aynı adresi yazın. Güvenlik duvarının açılması, yöneticilerin, kullanıcıların ve uygulamaların geçerli kimlik bilgilerine sahip oldukları PostgreSQL sunucusundaki herhangi bir veritabanına erişmesine olanak sağlar.

   <!--![Azure portal - firewall rules](./media/howto-manage-firewall-portal/4-specify-addresses.png)-->

6. Bu güvenlik duvarı kuralını kaydetmek için araç çubuğunda **Kaydet** ' e tıklayın. Güvenlik duvarı kuralları güncelleştirmesi başarılı oldu onay için bekleyin.

   <!--![Azure portal - click Save](./media/howto-manage-firewall-portal/5-save-firewall-rule.png)-->

## <a name="connecting-from-azure"></a>Azure'dan bağlanma

Esnek sunucunuza bağlanmak için Azure 'da dağıtılan kaynakları veya uygulamaları etkinleştirmek isteyebilirsiniz. Bu, bir Azure VM üzerinde çalışan Azure App Service, bir Azure Data Factory veri yönetimi ağ geçidi ve daha birçok konuda barındırılan Web uygulamalarını içerir. 

Azure 'daki bir uygulama sunucunuza bağlanmaya çalıştığında, güvenlik duvarı Azure bağlantılarına izin verildiğini doğrular. Bu ayarı, **ağ** sekmesinden portalda **Bu sunucuya Azure hizmetlerinden ve kaynaklarından ortak erişime izin ver** seçeneğini belirleyerek etkinleştirebilir ve **Kaydet**' e basın.

Bu bağlantıları etkinleştirmek için kaynakların, güvenlik duvarı kuralı için aynı sanal ağ (VNet) veya kaynak grubunda olması gerekmez. Bağlantı girişimine izin verilmiyorsa, istek PostgreSQL için Azure veritabanı-esnek sunucu ' ya ulaşmaz.

> [!IMPORTANT]
> Bu seçenek, diğer müşterilerin aboneliklerinden gelen bağlantılar dahil Azure’dan tüm bağlantılara izin verecek şekilde güvenlik duvarınızı yapılandırır. Bu seçeneği belirlerken, oturum açma ve kullanıcı izinlerinizin erişimi yalnızca yetkili kullanıcılarla sınırladığından emin olun.
>
> Esnek sunucuya güvenli bir şekilde erişmek için **özel erişimin (VNET tümleştirmesi)** seçilmesini öneririz.
>
## <a name="manage-existing-firewall-rules-through-the-azure-portal"></a>Azure portal aracılığıyla mevcut güvenlik duvarı kurallarını yönetme

Güvenlik duvarı kurallarını yönetmek için aşağıdaki adımları tekrarlayın.

- Geçerli bilgisayarı eklemek için, güvenlik duvarı kurallarında + **geçerli ISTEMCI IP adresi ekle** ' ye tıklayın. Değişiklikleri kaydetmek için **Kaydet**’e tıklayın.
- Daha fazla IP adresi eklemek için Kural Adı, Başlangıç IP Adresi ve Bitiş IP Adresi’ni yazın. Değişiklikleri kaydetmek için **Kaydet**’e tıklayın.
- Mevcut bir kuralı değiştirmek için kuraldaki alanlardan dilediğinize tıklayıp değiştirin. Değişiklikleri kaydetmek için **Kaydet**’e tıklayın.
- Mevcut bir kuralı silmek için, üç nokta (...] simgesine tıklayın ve **Sil** ' e tıklayarak kuralı kaldırın. Değişiklikleri kaydetmek için **Kaydet**’e tıklayın.

## <a name="next-steps"></a>Sonraki adımlar
- [PostgreSQL Için Azure veritabanı 'Nda ağ](./concepts-networking.md) hakkında daha fazla bilgi edinin-esnek sunucu
- [PostgreSQL Için Azure veritabanı-esnek sunucu güvenlik duvarı kuralları](./concepts-networking.md#public-access-allowed-ip-addresses) hakkında daha fazla bilgi edinin
- [Azure CLI kullanarak PostgreSQL Için Azure veritabanı güvenlik duvarı kuralları oluşturun ve yönetin](./how-to-manage-firewall-cli.md).