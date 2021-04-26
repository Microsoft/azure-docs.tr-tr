---
title: Güvenlik duvarı kurallarını yönetme-Azure portal-MariaDB için Azure veritabanı
description: Azure portal kullanarak MariaDB için Azure veritabanı güvenlik duvarı kuralları oluşturma ve yönetme
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 3/18/2020
ms.openlocfilehash: 895c8e51e16cb0b82ebf3edcf954dabe62b08ff4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98665064"
---
# <a name="create-and-manage-azure-database-for-mariadb-firewall-rules-by-using-the-azure-portal"></a>Azure portal kullanarak MariaDB için Azure veritabanı güvenlik duvarı kuralları oluşturun ve yönetin
Sunucu düzeyinde güvenlik duvarı kuralları, belirli bir IP adresinden veya bir IP adresi aralığından MariaDB sunucusu için Azure veritabanı 'na erişimi yönetmek için kullanılabilir.

Sanal ağ (VNet) kuralları, sunucunuza erişimi güvenli hale getirmek için de kullanılabilir. [Azure Portal kullanarak sanal ağ hizmet uç noktaları ve kuralları oluşturma ve yönetme](howto-manage-vnet-portal.md)hakkında daha fazla bilgi edinin.

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Azure portalında sunucu düzeyinde bir güvenlik duvarı kuralı oluşturma

1. MariaDB sunucusu sayfasında, ayarlar başlığı altında **bağlantı güvenliği** ' ne tıklayarak MariaDB Için Azure veritabanı bağlantı güvenliği sayfasını açın.

   ![Azure portal-bağlantı güvenliği ' ne tıklayın](./media/howto-manage-firewall-portal/1-connection-security.png)

2. Araç çubuğunda **IP Ekle** ' ye tıklayın. Bu otomatik olarak, Azure sistemi tarafından algılanan şekilde bilgisayarınızın genel IP adresi ile bir güvenlik duvarı kuralı oluşturur.

   ![Azure portal-IP Ekle 'ye tıklayın](./media/howto-manage-firewall-portal/2-add-my-ip.png)

3. Yapılandırmayı kaydetmeden önce IP adresinizi doğrulayın. Bazı durumlarda Azure portal tarafından gözlenen IP adresi, internet ve Azure sunucularına erişirken kullanılan IP adresinden farklıdır. Bu nedenle, kural işlevini beklendiği gibi yapmak için başlangıç IP 'sini ve bitiş IP 'sini değiştirmeniz gerekebilir.

   Kendi IP adresinizi denetlemek için bir arama altyapısı veya başka bir çevrimiçi araç kullanın. Örneğin, "IP adresim nedir?" ifadesini aratın.

4. Ek adres aralıkları ekleyin. MariaDB için Azure veritabanı güvenlik duvarı kurallarında tek bir IP adresi veya bir adres aralığı belirtebilirsiniz. Kuralı tek bir IP adresiyle sınırlandırmak istiyorsanız, başlangıç IP 'si ve bitiş IP alanlarına aynı adresi yazın. Güvenlik duvarının açılması, yöneticilerin, kullanıcıların ve uygulamanın, geçerli kimlik bilgilerine sahip oldukları MariaDB sunucusundaki herhangi bir veritabanına erişmesine olanak sağlar.

   ![Azure portal-güvenlik duvarı kuralları](./media/howto-manage-firewall-portal/4-specify-addresses.png)

5. Bu sunucu düzeyinde güvenlik duvarı kuralını kaydetmek için araç çubuğunda **Kaydet** ' e tıklayın. Güvenlik duvarı kurallarına güncelleştirme işleminin başarılı olduğunu onaylayın.

   ![Azure portal-kaydet 'e tıklayın](./media/howto-manage-firewall-portal/5-save-firewall-rule.png)

## <a name="connecting-from-azure"></a>Azure'dan bağlanma
Azure 'daki uygulamaların, MariaDB sunucusu için Azure veritabanınıza bağlanmasına izin vermek için Azure bağlantılarının etkinleştirilmesi gerekir. Örneğin, bir Azure Web Apps uygulamasını veya bir Azure VM 'de çalışan bir uygulamayı barındırmak ya da bir Azure Data Factory veri yönetimi ağ geçidinden bağlanmak için. Bu bağlantıları etkinleştirmek için kaynakların, güvenlik duvarı kuralı için aynı sanal ağ (VNet) veya kaynak grubunda olması gerekmez. Azure’dan bir uygulama, veritabanı sunucunuza bağlanmayı denediğinizde güvenlik duvarı Azure bağlantılarına izin verildiğini doğrular. Bu tür bağlantıları etkinleştirmek için birkaç yöntem vardır. Başlangıç ve bitiş adresi 0.0.0.0’a eşit olan bir güvenlik duvarı ayarı, bu bağlantılara izin verildiğini gösterir. Alternatif olarak, **bağlantı güvenliği** bölmesinden portalda **Azure hizmetlerine erişime izin ver** seçeneğini ayarlayabilir ve **Kaydet** **' e tıklayabilirsiniz** . Bağlantı girişimine izin verilmiyorsa, istek, MariaDB sunucusu için Azure veritabanına ulaşmaz.

> [!IMPORTANT]
> Bu seçenek, diğer müşterilerin aboneliklerinden gelen bağlantılar dahil Azure’dan tüm bağlantılara izin verecek şekilde güvenlik duvarınızı yapılandırır. Bu seçeneği belirlerken, oturum açma ve kullanıcı izinlerinizin erişimi yalnızca yetkili kullanıcılarla sınırladığından emin olun.
> 

## <a name="manage-existing-firewall-rules-in-the-azure-portal"></a>Azure portal mevcut güvenlik duvarı kurallarını yönetme
Güvenlik duvarı kurallarını yönetmek için adımları yineleyin.
* Geçerli bilgisayarı eklemek için **+ IP 'Mi Ekle**'ye tıklayın. Değişiklikleri kaydetmek için **Kaydet**’e tıklayın.
* Ek IP adresleri eklemek için **kural adı**, **Başlangıç IP**'si ve **bitiş IP 'si** yazın. Değişiklikleri kaydetmek için **Kaydet**’e tıklayın.
* Mevcut bir kuralı değiştirmek için kuraldaki alanlardan herhangi birine tıklayın ve ardından değiştirin. Değişiklikleri kaydetmek için **Kaydet**’e tıklayın.
* Mevcut bir kuralı silmek için, üç nokta işaretine [...] ve sonra **Sil**' e tıklayın. Değişiklikleri kaydetmek için **Kaydet**’e tıklayın.

## <a name="next-steps"></a>Sonraki adımlar
 - Benzer şekilde, [Azure CLI kullanarak MariaDB güvenlik duvarı kuralları Için Azure veritabanı oluşturmak ve yönetmek](howto-manage-firewall-cli.md)üzere komut dosyası oluşturabilirsiniz.
 - [Azure Portal kullanarak sanal ağ hizmet uç noktaları ve kuralları oluşturup yöneterek](howto-manage-vnet-portal.md)sunucunuza daha güvenli bir şekilde erişin.