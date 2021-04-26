---
title: Güvenlik duvarı kurallarını yönetme-Azure portal-MySQL için Azure veritabanı
description: Azure portal kullanarak MySQL için Azure Güvenlik duvarı kuralları oluşturma ve yönetme
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 3/18/2020
ms.openlocfilehash: 032db324f9dd2f0d6a5dce5c4fd5c64342fed59f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94540493"
---
# <a name="create-and-manage-azure-database-for-mysql-firewall-rules-by-using-the-azure-portal"></a>Azure portalı kullanarak MySQL için Azure Veritabanı güvenli duvarı kurallarını oluşturma ve yönetme
Sunucu düzeyinde güvenlik duvarı kuralları, belirli bir IP adresinden veya bir IP adresi aralığından MySQL için Azure veritabanı sunucusuna erişimi yönetmek için kullanılabilir. 

Sanal ağ (VNet) kuralları, sunucunuza erişimi güvenli hale getirmek için de kullanılabilir. [Azure Portal kullanarak sanal ağ hizmet uç noktaları ve kuralları oluşturma ve yönetme](howto-manage-vnet-using-portal.md)hakkında daha fazla bilgi edinin.

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Azure portalında sunucu düzeyinde bir güvenlik duvarı kuralı oluşturma

1. MySQL sunucusu sayfasında, ayarlar başlığı altında **bağlantı güvenliği** ' ne tıklayarak MySQL Için Azure veritabanı bağlantı güvenliği sayfasını açın.

   :::image type="content" source="./media/howto-manage-firewall-using-portal/1-connection-security.png" alt-text="Azure portal-bağlantı güvenliği ' ne tıklayın":::

2. Araç çubuğunda **IP Ekle** ' ye tıklayın. Bu otomatik olarak, Azure sistemi tarafından algılanan şekilde bilgisayarınızın genel IP adresi ile bir güvenlik duvarı kuralı oluşturur.

   :::image type="content" source="./media/howto-manage-firewall-using-portal/2-add-my-ip.png" alt-text="Azure portal-IP Ekle 'ye tıklayın":::

3. Yapılandırmayı kaydetmeden önce IP adresinizi doğrulayın. Bazı durumlarda Azure portal tarafından gözlenen IP adresi, internet ve Azure sunucularına erişirken kullanılan IP adresinden farklıdır. Bu nedenle, kural işlevini beklendiği gibi yapmak için başlangıç IP 'sini ve bitiş IP 'sini değiştirmeniz gerekebilir.

   Kendi IP adresinizi denetlemek için bir arama altyapısı veya başka bir çevrimiçi araç kullanın. Örneğin, "IP adresim nedir?" ifadesini aratın.

4. Ek adres aralıkları ekleyin. MySQL için Azure veritabanı güvenlik duvarı kurallarında tek bir IP adresi veya bir adres aralığı belirtebilirsiniz. Kuralı tek bir IP adresiyle sınırlandırmak istiyorsanız, başlangıç IP 'si ve bitiş IP alanlarına aynı adresi yazın. Güvenlik duvarının açılması, yöneticilerin, kullanıcıların ve uygulamanın geçerli kimlik bilgilerine sahip oldukları MySQL sunucusundaki herhangi bir veritabanına erişmesine olanak sağlar.

   :::image type="content" source="./media/howto-manage-firewall-using-portal/4-specify-addresses.png" alt-text="Azure portal-güvenlik duvarı kuralları":::

5. Bu sunucu düzeyinde güvenlik duvarı kuralını kaydetmek için araç çubuğunda **Kaydet** ' e tıklayın. Güvenlik duvarı kurallarına güncelleştirme işleminin başarılı olduğunu onaylayın.

   :::image type="content" source="./media/howto-manage-firewall-using-portal/5-save-firewall-rule.png" alt-text="Azure portal-kaydet 'e tıklayın":::

## <a name="connecting-from-azure"></a>Azure'dan bağlanma
Azure 'daki uygulamaların MySQL sunucusu için Azure veritabanı 'na bağlanmasına izin vermek için Azure bağlantılarının etkinleştirilmesi gerekir. Örneğin, bir Azure Web Apps uygulamasını veya bir Azure VM 'de çalışan bir uygulamayı barındırmak ya da bir Azure Data Factory veri yönetimi ağ geçidinden bağlanmak için. Bu bağlantıları etkinleştirmek için kaynakların, güvenlik duvarı kuralı için aynı sanal ağ (VNet) veya kaynak grubunda olması gerekmez. Azure’dan bir uygulama, veritabanı sunucunuza bağlanmayı denediğinizde güvenlik duvarı Azure bağlantılarına izin verildiğini doğrular. Bu tür bağlantıları etkinleştirmek için birkaç yöntem vardır. Başlangıç ve bitiş adresi 0.0.0.0’a eşit olan bir güvenlik duvarı ayarı, bu bağlantılara izin verildiğini gösterir. Alternatif olarak, **bağlantı güvenliği** bölmesinden portalda **Azure hizmetlerine erişime izin ver** seçeneğini ayarlayabilir ve **Kaydet** **' e basın** . Bağlantı girişimine izin verilmiyorsa, istek MySQL için Azure veritabanı sunucusuna ulaşmaz.

> [!IMPORTANT]
> Bu seçenek, diğer müşterilerin aboneliklerinden gelen bağlantılar dahil Azure’dan tüm bağlantılara izin verecek şekilde güvenlik duvarınızı yapılandırır. Bu seçeneği belirlerken, oturum açma ve kullanıcı izinlerinizin erişimi yalnızca yetkili kullanıcılarla sınırladığından emin olun.
> 

## <a name="manage-existing-server-level-firewall-rules-by-using-the-azure-portal"></a>Azure portal kullanarak mevcut sunucu düzeyi güvenlik duvarı kurallarını yönetme
Güvenlik duvarı kurallarını yönetmek için adımları yineleyin.
* Geçerli bilgisayarı eklemek için **+ IP 'Mi Ekle**'ye tıklayın. Değişiklikleri kaydetmek için **Kaydet**’e tıklayın.
* Ek IP adresleri eklemek için **kural adı**, **Başlangıç IP**'si ve **bitiş IP 'si** yazın. Değişiklikleri kaydetmek için **Kaydet**’e tıklayın.
* Mevcut bir kuralı değiştirmek için kuraldaki alanlardan herhangi birine tıklayın ve ardından değiştirin. Değişiklikleri kaydetmek için **Kaydet**’e tıklayın.
* Mevcut bir kuralı silmek için, üç nokta işaretine [...] ve sonra **Sil**' e tıklayın. Değişiklikleri kaydetmek için **Kaydet**’e tıklayın.


## <a name="next-steps"></a>Sonraki adımlar
- Benzer şekilde, [Azure CLI kullanarak MySQL Için Azure Güvenlik duvarı kuralları oluşturmak ve yönetmek](howto-manage-firewall-using-cli.md)için komut dosyası oluşturabilirsiniz.
- [Azure Portal kullanarak sanal ağ hizmet uç noktaları ve kuralları oluşturup yöneterek](howto-manage-vnet-using-portal.md)sunucunuza daha güvenli bir şekilde erişin.
- MySQL için Azure veritabanı sunucusuna bağlanma konusunda yardım için bkz. [MySQL Için Azure veritabanı bağlantı kitaplıkları](./concepts-connection-libraries.md).                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 
