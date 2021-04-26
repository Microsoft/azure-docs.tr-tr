---
title: Güvenlik duvarı kurallarını yönetme-Azure portal-MySQL için Azure veritabanı-esnek sunucu
description: Azure portal kullanarak MySQL için Azure veritabanı için güvenlik duvarı kuralları oluşturma ve yönetme-esnek sunucu
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: b26ce91b005fc7bd4d5b89ccf5306dc03a040b0f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105106760"
---
# <a name="create-and-manage-firewall-rules-for-azure-database-for-mysql---flexible-server-using-the-azure-portal"></a>Azure portal kullanarak MySQL için Azure veritabanı için güvenlik duvarı kuralları oluşturma ve yönetme-esnek sunucu

> [!IMPORTANT]
> MySQL için Azure veritabanı esnek sunucu şu anda genel önizlemededir.

MySQL için Azure Veritabanı Esnek Sunucusu, esnek sunucunuza bağlanmak için birbirini dışlayan iki tür ağ bağlantısı yöntemini destekler. Bu iki seçenek şunlardır:

1. Genel erişim (izin verilen IP adresleri)
2. Özel erişim (Sanal Ağ Tümleştirmesi)

Bu makalede, Azure portal kullanarak **ortak erişim (izin VERILEN IP adresleri)** ile MySQL sunucusu oluşturmaya odaklanacağız ve esnek sunucu oluşturulduktan sonra güvenlik duvarı kurallarını yönetmeye ilişkin bir genel bakış sağlanır. *Ortak erişim (izin VERILEN IP adresleri)* ile MySQL sunucusu bağlantıları yalnızca ızın verilen IP adresleriyle kısıtlıdır. Güvenlik duvarı kurallarında istemci IP adreslerine izin verilmesi gerekir. Daha fazla bilgi edinmek için bkz. [genel erişim (izin VERILEN IP adresleri)](./concepts-networking.md#public-access-allowed-ip-addresses). Güvenlik duvarı kuralları sunucu oluşturma sırasında tanımlanabilir (önerilir), ancak daha sonra da eklenebilir. Bu makalede, genel erişimi (izin verilen IP adresleri) kullanarak güvenlik duvarı kuralları oluşturma ve yönetme konusunda genel bir bakış sağlıyoruz.

## <a name="create-a-firewall-rule-when-creating-a-server"></a>Sunucu oluştururken bir güvenlik duvarı kuralı oluşturma

1. Portalın sol üst köşesinde bulunan **kaynak oluştur** (+) seçeneğini belirleyin.
2. **Veritabanları**  >  **MySQL için Azure veritabanı**' nı seçin. Ayrıca, arama kutusuna **MySQL** girerek hizmeti bulabilirsiniz.
3. Dağıtım seçeneği olarak **esnek sunucu** ' yı seçin.
4. **Temel bilgiler** formunu doldurun.
5. Sunucunuza nasıl bağlanmak istediğinizi yapılandırmak için **ağ** sekmesine gidin.
6. **Bağlantı yönteminde**, *genel erişim (izin verilen IP adresleri)* öğesini seçin. **Güvenlik duvarı kuralları** oluşturmak Için, güvenlik duvarı kuralı adını ve tek IP adresini veya bir adres aralığını belirtin. Kuralı tek bir IP adresiyle sınırlandırmak istiyorsanız, başlangıç IP adresi ve bitiş IP adresi alanına aynı adresi yazın. Güvenlik duvarının açılması, yöneticilerin, kullanıcıların ve uygulamaların geçerli kimlik bilgilerine sahip oldukları MySQL sunucusundaki herhangi bir veritabanına erişmesine olanak sağlar.
   > [!Note]
   > MySQL için Azure veritabanı esnek sunucu, sunucu düzeyinde bir güvenlik duvarı oluşturur. Bu güvenlik duvarı, belirli IP adresleri için güvenlik duvarını açmak üzere bir kural oluşturmadığınız sürece, dış uygulama ve araçların sunucuya ve sunucu üzerindeki herhangi bir veritabanına bağlanmasını engeller.

7. Esnek sunucu yapılandırmanızı gözden geçirmek için **gözden geçir + oluştur** ' u seçin.
8.  Sunucuyu sağlamak için **Oluştur**’u seçin. Sağlama birkaç dakika sürebilir.

## <a name="create-a-firewall-rule-after-server-is-created"></a>Sunucu oluşturulduktan sonra bir güvenlik duvarı kuralı oluşturma

1. [Azure Portal](https://portal.azure.com/), güvenlik duvarı kuralları eklemek istediğiniz MySQL Için Azure veritabanı esnek sunucusu ' nu seçin.
2. Esnek sunucu sayfasında, **Ayarlar** başlığı altında, esnek sunucu ağ sayfasını açmak Için **ağ iletişimi** ' ne tıklayın.

   <!--:::image type="content" source="./media/howto-manage-firewall-portal/1-connection-security.png" alt-text="Azure portal - click Connection Security":::-->

3. Güvenlik duvarı kurallarında **geçerli ISTEMCI IP adresi ekle** ' ye tıklayın. Bu otomatik olarak, Azure sistemi tarafından algılanan şekilde bilgisayarınızın genel IP adresi ile bir güvenlik duvarı kuralı oluşturur.

   <!--:::image type="content" source="./media/howto-manage-firewall-portal/2-add-my-ip.png" alt-text="Azure portal - click Add My IP":::-->

4. Yapılandırmayı kaydetmeden önce IP adresinizi doğrulayın. Bazı durumlarda Azure portal tarafından gözlenen IP adresi, internet ve Azure sunucularına erişirken kullanılan IP adresinden farklıdır. Bu nedenle, kural işlevini beklendiği gibi yapmak için başlangıç IP adresini ve bitiş IP adresini değiştirmeniz gerekebilir.

   Kendi IP adresinizi denetlemek için bir arama altyapısı veya başka bir çevrimiçi araç kullanabilirsiniz. Örneğin, "IP nedir?" ifadesini aratın.

   <!--:::image type="content" source="./media/howto-manage-firewall-portal/3-what-is-my-ip.png" alt-text="Bing search for What is my IP":::-->

5. Ek adres aralıkları ekleyin. MySQL için Azure veritabanı esnek sunucusu güvenlik duvarı kurallarında tek bir IP adresi veya bir adres aralığı belirtebilirsiniz. Kuralı tek bir IP adresiyle sınırlandırmak istiyorsanız, başlangıç IP adresi ve bitiş IP adresi alanına aynı adresi yazın. Güvenlik duvarının açılması, yöneticilerin, kullanıcıların ve uygulamaların geçerli kimlik bilgilerine sahip oldukları MySQL sunucusundaki herhangi bir veritabanına erişmesine olanak sağlar.

   <!--:::image type="content" source="./media/howto-manage-firewall-portal/4-specify-addresses.png" alt-text="Azure portal - firewall rules":::-->

6. Bu güvenlik duvarı kuralını kaydetmek için araç çubuğunda **Kaydet** ' e tıklayın. Güvenlik duvarı kuralları güncelleştirmesi başarılı oldu onay için bekleyin.

   <!--:::image type="content" source="./media/howto-manage-firewall-portal/5-save-firewall-rule.png" alt-text="Azure portal - click Save":::-->

## <a name="connect-from-azure"></a>Azure 'dan Bağlan

Esnek sunucunuza bağlanmak için Azure 'da dağıtılan kaynakları veya uygulamaları etkinleştirmek isteyebilirsiniz. Bu, bir Azure VM üzerinde çalışan Azure App Service, bir Azure Data Factory veri yönetimi ağ geçidi ve daha birçok konuda barındırılan Web uygulamalarını içerir.

Azure 'daki bir uygulama sunucunuza bağlanmaya çalıştığında, güvenlik duvarı Azure bağlantılarına izin verildiğini doğrular. Bu ayarı, **ağ** sekmesinden portalda **Bu sunucuya Azure hizmetlerinden ve kaynaklarından ortak erişime izin ver** seçeneğini belirleyerek etkinleştirebilir ve **Kaydet**' e basın.

Bu bağlantıları etkinleştirmek için kaynakların, güvenlik duvarı kuralı için aynı sanal ağ (VNet) veya kaynak grubunda olması gerekmez. Bağlantı girişimine izin verilmiyorsa, istek MySQL için Azure veritabanı esnek sunucusuna ulaşmaz.

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
- [MySQL Için Azure veritabanı esnek sunucusu 'Nda ağ](./concepts-networking.md) hakkında daha fazla bilgi edinin
- [MySQL Için Azure veritabanı esnek sunucu güvenlik duvarı kuralları](./concepts-networking.md#public-access-allowed-ip-addresses) hakkında daha fazla bilgi
- [Azure CLI kullanarak MySQL Için Azure Güvenlik duvarı kuralları oluşturun ve yönetin](./how-to-manage-firewall-cli.md).