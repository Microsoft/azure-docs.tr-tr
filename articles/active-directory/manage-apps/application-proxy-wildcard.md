---
title: Azure AD Uygulama Ara Sunucusu joker karakter uygulamaları
description: Azure Active Directory uygulama proxy 'sinde joker uygulamalar kullanmayı öğrenin.
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 09/06/2018
ms.author: kenwith
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a532ae9485efa9571137130d32ba0827728e8094
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106166879"
---
# <a name="wildcard-applications-in-the-azure-active-directory-application-proxy"></a>Azure Active Directory uygulama proxy 'sinde joker uygulamalar

Azure Active Directory (Azure AD) içinde, çok sayıda şirket içi uygulamayı yapılandırmak hızlı bir şekilde yönetilebilir hale gelebilir ve bunların birçoğu aynı ayarları gerektiriyorsa yapılandırma hatalarıyla ilgili gereksiz riskler sağlar. [Azure AD uygulama ara sunucusu](application-proxy.md)ile, aynı anda birçok uygulamayı yayımlamak ve yönetmek için joker karakter uygulama yayımlamasını kullanarak bu sorunu ele alabilirsiniz. Bu, şunları yapmanıza olanak sağlayan bir çözümdür:

- Yönetim yükünüzü kolaylaştırın
- Olası yapılandırma hatalarının sayısını azaltın
- Kullanıcılarınızın daha fazla kaynağa güvenli bir şekilde erişmesini sağlama

Bu makale, ortamınızda joker karakter uygulama yayımlamayı yapılandırmak için gereken bilgileri sağlar.

## <a name="create-a-wildcard-application"></a>Joker uygulama oluşturma

Aynı yapılandırmaya sahip bir uygulama grubunuz varsa, bir joker karakter (*) uygulaması oluşturabilirsiniz. Bir joker uygulama için olası aday uygulamalar aşağıdaki ayarları paylaşan uygulamalardır:

- Bunlara erişimi olan Kullanıcı grubu
- SSO yöntemi
- Erişim Protokolü (http, https)

Hem iç hem de dış URL 'Ler aşağıdaki biçimde olan joker karakterlerle uygulamalar yayımlayabilirsiniz:

> http (s)://*.\<domain\>

Örneğin: `http(s)://*.adventure-works.com`.

İç ve dış URL 'Ler, en iyi uygulama olarak farklı etki alanlarını kullanabilir ancak aynı olmaları gerekir. Uygulamayı yayımlarken, URL 'Lerden birinin joker karakteri yoksa bir hata görürsünüz.

Joker bir uygulama oluşturmak, diğer tüm uygulamalar için kullanılabilir olan aynı [uygulama yayımlama akışını](application-proxy-add-on-premises-application.md) temel alır. Tek fark, URL 'Lere ve potansiyel olarak SSO yapılandırmasına bir joker karakter dahil etmeniz olabilir.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için, bu gereksinimleri karşıladığınızdan emin olun.

### <a name="custom-domains"></a>Özel etki alanları

[Özel etki alanları](application-proxy-configure-custom-domain.md) diğer tüm uygulamalar için isteğe bağlı olsa da, joker uygulamalar için bir önkoşuldur. Özel etki alanları oluşturmak için şunları yapmanız gerekir:

1. Azure 'da doğrulanmış bir etki alanı oluşturun.
1. PFX biçiminde, uygulama ara sunucunuza bir TLS/SSL sertifikası yükleyin.

Oluşturmayı planladığınız uygulamayla eşleşmesi için bir joker karakter sertifikası kullanmayı göz önünde bulundurmanız gerekir. 

Güvenlik nedenleriyle bu bir sabit gereksinimdir ve dış URL için özel bir etki alanı kullanmayan uygulamalar için joker karakterleri desteklemezler.

### <a name="dns-updates"></a>DNS güncelleştirmeleri

Özel etki alanları kullanırken, dış URL için CNAME kaydıyla bir DNS girişi oluşturmanız gerekir (örneğin,  `*.adventure-works.com` ) uygulama proxy uç noktasının dış URL 'sini işaret eder. Joker uygulamalar için CNAME kaydının ilgili dış URL 'yi göstermesi gerekir:

> `<yourAADTenantId>.tenant.runtime.msappproxy.net`

CNAME 'nizi doğru şekilde yapılandırdığınızdan emin olmak için, hedef uç noktalardan birinde [nslookup](/windows-server/administration/windows-commands/nslookup) ' ı kullanabilirsiniz (örneğin,) `expenses.adventure-works.com` .  Yanıtınız zaten belirtilen diğer adı ( `<yourAADTenantId>.tenant.runtime.msappproxy.net` ) içermelidir.

### <a name="using-connector-groups-assigned-to-an-app-proxy-cloud-service-region-other-than-the-default-region"></a>Varsayılan bölge dışında bir uygulama proxy 'Si bulut hizmeti bölgesine atanan bağlayıcı gruplarını kullanma
Varsayılan kiracı bölgenizin farklı bölgelerinde yüklü bağlayıcılar varsa, bu uygulamalara erişim performansını artırmak için bağlayıcı grubunuzun en iyi duruma getirildiği bölgeyi değiştirmek faydalı olabilir. Daha fazla bilgi edinmek için bkz. [en yakın uygulama proxy 'si bulut hizmetini kullanmak için bağlayıcı gruplarını iyileştirin](application-proxy-network-topology.md#optimize-connector-groups-to-use-closest-application-proxy-cloud-service-preview).
 
Joker uygulamaya atanan bağlayıcı grubu **varsayılan bölgenizde farklı bir bölge** KULLANıYORSA, CNAME kaydını, bölgesel olarak belirtilen BIR dış URL 'ye işaret edecek şekilde güncelleştirmeniz gerekir. İlgili URL 'YI öğrenmek için aşağıdaki tabloyu kullanın:

| Bağlayıcı tarafından atanan bölge | Dış URL |
| ---   | ---         |
| Asya | `<yourAADTenantId>.asia.tenant.runtime.msappproxy.net`|
| Avustralya  | `<yourAADTenantId>.aus.tenant.runtime.msappproxy.net` |
| Avrupa  | `<yourAADTenantId>.eur.tenant.runtime.msappproxy.net`|
| Kuzey Amerika  | `<yourAADTenantId>.nam.tenant.runtime.msappproxy.net` |

## <a name="considerations"></a>Dikkat edilmesi gerekenler

Joker uygulamalar için dikkate almanız gereken bazı noktalar aşağıda verilmiştir.

### <a name="accepted-formats"></a>Kabul edilen biçimler

Joker uygulamalar için **Iç URL** 'nin olarak biçimlendirilmesi gerekir `http(s)://*.<domain>` .

![İç URL için http (s)://* biçimini kullanın. \< etki alanı>](./media/application-proxy-wildcard/22.png)

Bir **dış URL** yapılandırdığınızda, aşağıdaki biçimi kullanmanız gerekir: `https://*.<custom domain>`

![Dış URL için https://* biçimini kullanın. \< Özel etki alanı>](./media/application-proxy-wildcard/21.png)

Joker karakter, birden çok joker karakter veya diğer Regex dizelerinin diğer konumları desteklenmez ve hatalara neden oluyor.

### <a name="excluding-applications-from-the-wildcard"></a>Uygulamaları joker karakterle dışlama

Joker uygulamadan bir uygulamayı dışarıda bırakabilirsiniz

- Özel durum uygulamasını düzenli uygulama olarak yayımlama
- Yalnızca DNS ayarlarınız aracılığıyla belirli uygulamalar için joker karakter etkinleştiriliyor

Bir uygulamayı normal uygulama olarak yayımlamak, bir uygulamayı bir joker karakterle dışlamak için tercih edilen yöntemdir. Özel durumlarınızın başlangıçtan itibaren uygulanmasını sağlamak için, joker uygulamalardan önce dışlanan uygulamaları yayımlamanız gerekir. En belirli uygulama her zaman öncelikli olur. yayımlanan bir uygulama, uygulamaya `budgets.finance.adventure-works.com` göre öncelikli olarak, uygulamanın `*.finance.adventure-works.com` üzerine daha önceliklidir `*.adventure-works.com` .

Ayrıca, joker karakteri yalnızca DNS yönetimanızdan belirli uygulamalar için çalışacak şekilde sınırlayabilirsiniz. En iyi uygulama olarak, bir joker karakter içeren ve yapılandırdığınız dış URL 'nin biçimiyle eşleşen bir CNAME girişi oluşturmalısınız. Ancak, bunun yerine, belirli uygulama URL 'Lerini Joker karakterlere işaret edebilirsiniz. Örneğin,, `*.adventure-works.com` işaret `hr.adventure-works.com` ve yerine `expenses.adventure-works.com` `travel.adventure-works.com individually` `000aa000-11b1-2ccc-d333-4444eee4444e.tenant.runtime.msappproxy.net` .

Bu seçeneği kullanırsanız, değer için de `AppId.domain` `00000000-1a11-22b2-c333-444d4d4dd444.adventure-works.com` aynı konuma işaret eden bir CNAME girişi gerekir. Uygulama **kimliği** , joker karakter uygulamasının uygulama özellikleri sayfasında bulunabilir:

![Uygulamanın özellik sayfasında uygulama KIMLIĞINI bulun](./media/application-proxy-wildcard/01.png)

### <a name="setting-the-homepage-url-for-the-myapps-panel"></a>Uygps paneli için giriş sayfası URL 'sini ayarlama

Joker uygulama, [Uygps panelinde](https://myapps.microsoft.com)yalnızca bir kutucuk ile temsil edilir. Bu kutucuk varsayılan olarak gizlidir. Kutucuğu göstermek ve kullanıcıların belirli bir sayfada oturum göstermesini sağlamak için:

1. [Giriş sayfası URL 'sini ayarlamaya](application-proxy-configure-custom-home-page.md)yönelik yönergeleri izleyin.
1. Uygulama özellikleri sayfasında uygulamayı **true** olarak **göster** ' i ayarlayın.

### <a name="kerberos-constrained-delegation"></a>Kerberos kısıtlanmış temsili

[SSO yöntemi olarak Kerberos kısıtlanmış temsili (KCD)](application-proxy-configure-single-sign-on-with-kcd.md)kullanan uygulamalar IÇIN, SSO yöntemi IÇIN listelenen SPN 'nin de bir joker karakter olması gerekebilir. Örneğin, SPN: `HTTP/*.adventure-works.com` . Yine de arka uç sunucularınızda (örneğin,) ayrı SPN 'Leri yapılandırmış olmanız gerekir `HTTP/expenses.adventure-works.com and HTTP/travel.adventure-works.com` .

## <a name="scenario-1-general-wildcard-application"></a>Senaryo 1: genel joker uygulama

Bu senaryoda, yayımlamak istediğiniz üç farklı uygulama vardır:

- `expenses.adventure-works.com`
- `hr.adventure-works.com`
- `travel.adventure-works.com`

Tüm üç uygulama:

- Tüm kullanıcılarınız tarafından kullanılır
- *Tümleşik Windows kimlik doğrulaması* kullan
- Aynı özelliklere sahip

Joker uygulamayı [Azure AD uygulama ara sunucusu kullanarak uygulama yayımlama](application-proxy-add-on-premises-application.md)bölümünde özetlenen adımları kullanarak yayımlayabilirsiniz. Bu senaryo şunları varsayar:

- Şu KIMLIĞE sahip bir kiracı: `000aa000-11b1-2ccc-d333-4444eee4444e`
- Çağrılan doğrulanmış bir etki alanı `adventure-works.com` yapılandırıldı.
- Öğesine  işaret eden bir CNAME `*.adventure-works.com` girişi `000aa000-11b1-2ccc-d333-4444eee4444e.tenant.runtime.msappproxy.net` oluşturuldu.

[Belgelenen adımları](application-proxy-add-on-premises-application.md)izleyerek kiracınızda yeni bir uygulama proxy 'si uygulaması oluşturursunuz. Bu örnekte, joker karakter aşağıdaki alanlardır:

- İç URL:

    ![Örnek: İç URL 'de joker karakter](./media/application-proxy-wildcard/42.png)

- Dış URL:

    ![Örnek: dış URL 'de joker karakter](./media/application-proxy-wildcard/43.png)

- İç uygulama SPN 'si:

    ![Örnek: SPN yapılandırmasında joker karakter](./media/application-proxy-wildcard/44.png)

Joker uygulamayı yayımlayarak, artık kullandığınız URL 'lere giderek üç uygulamanıza erişebilirsiniz (örneğin, `travel.adventure-works.com` ).

Yapılandırma aşağıdaki yapıyı uygular:

![Örnek yapılandırma tarafından uygulanan yapıyı gösterir](./media/application-proxy-wildcard/05.png)

| Renk | Description |
| ---   | ---         |
| Mavi  | Azure portal açıkça yayımlanmış ve görünür uygulamalar. |
| Tonlamalı  | Üst uygulama aracılığıyla erişilebilen uygulamalar. |

## <a name="scenario-2-general-wildcard-application-with-exception"></a>Senaryo 2: özel durum içeren genel joker uygulama

Bu senaryoda, `finance.adventure-works.com` yalnızca finans bölümünün erişimine açık olması gereken üç genel uygulamanın başka bir uygulamasına ek olarak sahip olursunuz. Geçerli uygulama yapısıyla, finans uygulamanıza joker uygulama ve tüm çalışanlar tarafından erişilebilir. Bunu değiştirmek için, finans 'yi daha kısıtlayıcı izinlerle ayrı bir uygulama olarak yapılandırarak uygulamanızı joker karakterinizden dışlayabilirsiniz.

Uygulamanın uygulama `finance.adventure-works.com` proxy sayfasında belirtilen uygulamaya özgü uç noktaya işaret eden BIR CNAME kaydı olduğundan emin olmanız gerekir. Bu senaryo için, `finance.adventure-works.com` öğesine işaret eder `https://finance-awcycles.msappproxy.net/` .

[Belgelenen adımları](application-proxy-add-on-premises-application.md)izleyerek, bu senaryo aşağıdaki ayarları gerektirir:

- **Iç URL**'de, bir joker karakter yerine **finans** ayarlarsınız.

    ![Örnek: İç URL 'de joker karakter yerine finans ayarla](./media/application-proxy-wildcard/52.png)

- **Dış URL**'de, bir joker karakter yerine **finans** ayarlarsınız.

    ![Örnek: dış URL 'de joker karakter yerine finans ayarla](./media/application-proxy-wildcard/53.png)

- İç uygulama SPN 'si, bir joker karakter yerine **finans** 'yi ayarlar.

    ![Örnek: SPN yapılandırmasında joker karakter yerine finans ayarlama](./media/application-proxy-wildcard/54.png)

Bu yapılandırma aşağıdaki senaryoyu uygular:

![Örnek senaryo tarafından uygulanan yapılandırmayı gösterir](./media/application-proxy-wildcard/09.png)

`finance.adventure-works.com`, ' Den daha özel BIR URL olduğu için `*.adventure-works.com` öncelik kazanır. Kullanıcılar `finance.adventure-works.com` finans kaynakları uygulamasında belirtilen deneyimle geziniyor. Bu durumda, yalnızca finans çalışanları erişebiliyor `finance.adventure-works.com` .

Finans için yayımlanmış birden fazla uygulamanız varsa ve `finance.adventure-works.com` doğrulanmış bir etki alanınız varsa, başka bir joker uygulama yayımlayabilirsiniz `*.finance.adventure-works.com` . Bu, genel ' ten daha belirgin olduğundan `*.adventure-works.com` , bir Kullanıcı finans etki alanındaki bir uygulamaya eriştiğinde öncelik kazanır.

## <a name="next-steps"></a>Sonraki adımlar

- **Özel etki alanları** hakkında daha fazla bilgi için bkz. [Azure AD uygulama ara sunucusu özel etki alanları ile çalışma](application-proxy-configure-custom-domain.md).
- **Uygulama yayımlama** hakkında daha fazla bilgi edinmek için bkz. [Azure kullanarak uygulama yayımlama ad uygulama ara sunucusu](application-proxy-add-on-premises-application.md)
