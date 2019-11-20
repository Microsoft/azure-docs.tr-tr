---
title: Application Gateway için Web uygulaması güvenlik duvarı (WAF) ilkeleri oluşturma
description: Application Gateway için Web uygulaması güvenlik duvarı ilkeleri oluşturmayı öğrenin.
services: web-application-firewall
ms.topic: conceptual
author: vhorne
ms.service: web-application-firewall
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: 3f7d213aed82d1cb94bb96b9e212d3b255851afd
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74171213"
---
# <a name="create-web-application-firewall-policies-for-application-gateway"></a>Application Gateway için Web uygulaması güvenlik duvarı ilkeleri oluşturma

Bir WAF ilkesinin dinleyiciyle ilişkilendirilmesi, tek bir WAF arkasındaki birden fazla sitenin farklı ilkelerle korunmasını sağlar. Örneğin, WAF 'nizin arkasındaki beş site varsa, diğer dört uygulamadan bağımsız olarak bir siteye yönelik dışlamaları, özel kuralları ve yönetilen RuleSets 'ler özelleştirmek için beş ayrı WAF ilkesine (her dinleyici için bir tane) sahip olabilirsiniz. Tek bir ilkenin tüm sitelere uygulanmasını istiyorsanız, ilkeyi tek tek dinleyiciler yerine, genel olarak uygulanmasını sağlamak için yalnızca Application Gateway ile ilişkilendirebilirsiniz. İlkeler, yol tabanlı bir yönlendirme kuralına da uygulanabilir. 

   > [!NOTE]
   > Site başına WAF Ilkesi Orta Güney ABD ve Kuzey Avrupa kullanılabilir. Portalda bunlara erişmek için lütfen herkes için etkin olana kadar [Bu bağlantıyı](https://aka.ms/AppgwwafWithAllFeatureFlags) kullanın.  

İstediğiniz kadar ilke yapabilirsiniz. Bir ilke oluşturduktan sonra, etkin olması için bir Application Gateway ilişkilendirilmesi gerekir, ancak uygulama ağ geçitlerinin ve dinleyicilerinin herhangi bir birleşimiyle ilişkilendirilebilir. 

Application Gateway bir ilke uygulanmışsa ve sonra bu Application Gateway üzerindeki bir dinleyiciye farklı bir ilke uygularsanız, dinleyicinin ilkesi geçerli olur, ancak yalnızca atandığı dinleyicilerden yararlanabilir. Application Gateway ilkesi, kendisine atanmış belirli bir ilke olmayan tüm diğer dinleyiciler için de geçerlidir. 

   > [!NOTE]
   > Site başına ve URI başına WAF Ilkeleri genel önizlemede. Bu, bu özelliğin Microsoft 'un ek kullanım koşulları 'na tabi olduğu anlamına gelir. Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Tüm yeni Web uygulaması güvenlik duvarı WAF ayarları (özel kurallar, yönetilen kural kümesi yapılandırması, dışlamaları vb.) bir WAF Ilkesi içinde canlı. Var olan bir WAF 'niz varsa, bu ayarlar WAF yapılandırması 'nda hala mevcut olabilir. Yeni WAF Ilkesine geçme adımları için, bu makalenin ilerleyen kısımlarında [WAF config DOSYANıZı WAF Ilkesine geçirme](#migrate) bölümüne bakın. 

## <a name="create-a-policy"></a>İlke oluşturma

İlk olarak, Azure portal kullanarak, yönetilen varsayılan kural kümesi (DRS) ile temel bir WAF ilkesi oluşturun.

1. Portalın sol üst kısmında **kaynak oluştur**' u seçin. **WAF**araması yapın, **Web uygulaması güvenlik duvarı**' nı seçin ve **Oluştur**' u seçin.
2. **BIR WAF Ilkesi oluştur** sayfasında, **temel** bilgiler sekmesinde, aşağıdaki bilgileri girin veya seçin, kalan ayarlar için varsayılan değerleri kabul edin ve ardından **gözden geçir + oluştur**' u seçin:

   |Ayar  |Değer  |
   |---------|---------|
   |İlke     |Bölgesel WAF (Application Gateway)|
   |Abonelik     |Abonelik adınızı seçin|
   |Kaynak grubu     |Kaynak grubunuzu seçin|
   |İlke adı     |WAF ilkeniz için benzersiz bir ad yazın.|
3. **İlişkilendirme** sekmesinde, aşağıdaki ayarlardan birini girin ve ardından **Ekle**' yi seçin:

   |Ayar  |Değer  |
   |---------|---------|
   |Application Gateway ilişkilendir     |Application Gateway profili adınızı seçin.|
   |Dinleyicileri ilişkilendir     |Application Gateway dinleyicinizin adını seçip **Ekle**' yi seçin.|

   > [!NOTE]
   > Zaten bir ilkeye sahip olan Application Gateway (veya dinleyiciye) ilke atarsanız, özgün ilkenin üzerine yazılır ve yeni ilke konur.
4. **Gözden geçir + oluştur**' u seçin ve **Oluştur**' u seçin.

   ![WAF ilkesi temelleri](../media/create-waf-policy-ag/waf-policy-basics.png)

## <a name="configure-waf-rules-optional"></a>WAF kurallarını yapılandırma (isteğe bağlı)

Bir WAF ilkesi oluşturduğunuzda, varsayılan olarak *algılama* modundadır. Algılama modunda WAF hiçbir isteği engellemez. Bunun yerine, eşleşen WAF kuralları WAF günlüklerine kaydedilir. WAF 'yi eylemde görmek için mod ayarlarını *önleme*olarak değiştirebilirsiniz. Önleme modunda, seçtiğiniz sp_configure kural kümesinde tanımlanan eşleşen kurallar engellenir ve/veya WAF günlüklerinde günlüğe kaydedilir.

## <a name="managed-rules"></a>Yönetilen kurallar

Azure tarafından yönetilen OWASP kuralları varsayılan olarak etkinleştirilmiştir. Bir kural grubundaki tek bir kuralı devre dışı bırakmak için bu kural grubundaki kuralları genişletin, kural numarasının önündeki onay kutusunu seçin ve yukarıdaki sekmede **devre dışı bırak** ' ı seçin.

[Yönetilen kuralları ![](../media/create-waf-policy-ag/managed-rules.png)](../media/create-waf-policy-ag/managed-rules-lrg.png#lightbox)

## <a name="custom-rules"></a>Özel kurallar

Özel kural oluşturmak için özel **kurallar** sekmesinin altında **özel kural ekle** ' yi seçin. Bu, özel kural yapılandırma sayfasını açar. Aşağıdaki ekran görüntüsünde, sorgu dizesinde *blok metni varsa*bir isteği engellemek için yapılandırılmış örnek bir özel kural gösterilmektedir.

[Özel kuralı Düzenle](../media/create-waf-policy-ag/edit-custom-rule.png) ![](../media/create-waf-policy-ag/edit-custom-rule-lrg.png#lightbox)

## <a name="migrate"></a>WAF yapılandırmasını bir WAF Ilkesine geçirme

Zaten bir WAF varsa, portalda bazı değişiklikler fark etmiş olabilirsiniz. İlk olarak, WAF 'niz üzerinde etkinleştirdiğiniz Ilke türünü belirlemeniz gerekir. Üç olası durum vardır:

- WAF Ilkesi yok
- Yalnızca özel kurallar Ilkesi
- WAF Ilkesi

Portalda, WAF 'nizin hangi duruma olduğunu söyleyebilirsiniz. WAF ayarları görünür durumdaysa ve Application Gateway görünümünde değiştirilebiliyorsa, WAF 'niz durum 1 ' dir.

[![WAF yapılandırma](../media/create-waf-policy-ag/waf-configure.png)](../media/create-waf-policy-ag/waf-configure-lrg.png#lightbox)

**Web uygulaması güvenlik duvarı 'nı** seçerseniz ve ilgili bir ilke gösteriyorsa WAF, durum 2 veya durum 3 ' dir. İlkeye gidildikten sonra, **yalnızca** özel kurallar ve Ilişkili uygulama ağ geçitleri gösteriyorsa, bu yalnızca özel bir kural ilkesidir.

![WAF özel kuralları](../media/create-waf-policy-ag/waf-custom-rules.png)

Ayrıca Ilke ayarlarını ve yönetilen kuralları gösteriyorsa, tam bir Web uygulaması güvenlik duvarı ilkesidir. 

![WAF ilke ayarları](../media/create-waf-policy-ag/waf-policy-settings.png)

## <a name="migrate-to-waf-policy"></a>WAF Ilkesine geçir

Yalnızca WAF Ilkenize özel bir Kuraldıysanız, yeni WAF Ilkesine geçiş yapmak isteyebilirsiniz. Daha sonra, güvenlik duvarı ilkesi WAF ilke ayarlarını, yönetilen kural kümelerini, dışlamaları ve devre dışı kural gruplarını destekleyecektir. Temelde, daha önce Application Gateway içinde yapılan tüm WAF yapılandırmalarının artık WAF Ilkesi aracılığıyla yapılır. 

Özel kuralda yapılan düzenlemeler yalnızca WAF ilkesi devre dışı bırakıldı. Kuralları devre dışı bırakma, dışlamaları ekleme vb. gibi WAF ayarlarını düzenlemek için, yeni bir üst düzey güvenlik duvarı ilkesi kaynağına geçiş yapmanız gerekir.

Bunu yapmak için bir *Web uygulaması güvenlik duvarı ilkesi* oluşturun ve bunu tercih ettiğiniz Application Gateway ve dinleyiclarıyla ilişkilendirin. Bu yeni Ilke, geçerli WAF yapılandırması ile tam olarak aynı **olmalıdır** , yani her özel kural, dışlama, devre dışı kuralı vb., oluşturmakta olduğunuz yeni ilkeye kopyalanmalıdır. Application Gateway ilişkili bir Ilkeniz olduktan sonra, WAF kurallarınızdaki ve ayarlarınızda değişiklik yapmaya devam edebilirsiniz. Bunu, Azure PowerShell ile de yapabilirsiniz. Daha fazla bilgi için bkz. [BIR WAF ilkesini mevcut bir Application Gateway ilişkilendirme](associate-waf-policy-existing-gateway.md).

İsteğe bağlı olarak, bir WAF ilkesine geçiş yapmak için bir geçiş betiği kullanabilirsiniz. Daha fazla bilgi için bkz. [Azure PowerShell kullanarak Web uygulaması güvenlik duvarı Ilkelerini geçirme](migrate-policy.md).

## <a name="next-steps"></a>Sonraki adımlar

[Web uygulaması güvenlik duvarı 'nın ilgili kural grupları ve kuralları](application-gateway-crs-rulegroups-rules.md)hakkında daha fazla bilgi edinin.
