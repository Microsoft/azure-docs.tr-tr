---
title: Azure Active Directory Uygulama proxy'si joker karakteri uygulamalarında | Microsoft Docs
description: Joker karakter içeren uygulamalar Azure Active Directory Uygulama proxy kullanmayı öğrenin.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/06/2018
ms.author: mimart
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5d3b8176566593c5c9e9ff63a6ccbafcb2a35cd5
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67828002"
---
# <a name="wildcard-applications-in-the-azure-active-directory-application-proxy"></a>Azure Active Directory Uygulama proxy'si joker karakteri uygulamalarında

Azure Active Directory'de çok sayıda şirket içi yapılandırma (Azure AD), uygulamaları hızla yönetilemeyen olabilir ve birçoğu aynı ayarları gerekiyorsa yapılandırma hataları için gereksiz risklerini de beraberinde getirir. İle [Azure AD uygulama proxy'si](application-proxy.md), yayımlama ve pek çok uygulama aynı anda yönetmek için joker uygulama yayımlama kullanarak bu sorunu gidermek. Bu olanak tanıyan bir çözüm.

- Yönetim yükünüzü basitleştirin
- Olası yapılandırma hatalarını sayısını azaltın
- Daha fazla kaynaklarına güvenle erişmesini sağlayın

Bu makalede, ortamınızda joker uygulama yayımlamayı yapılandırmak gereken bilgileri sağlar.

## <a name="create-a-wildcard-application"></a>Bir joker uygulama oluşturma

Aynı yapılandırmaya sahip uygulamalar grubunuz varsa, bir joker (*) uygulama oluşturabilirsiniz. Bir joker uygulama için potansiyel adaylar, aşağıdaki ayarları paylaşan uygulamalar şunlardır:

- Onlara yönelik erişimi olan kullanıcı grubu
- SSO yöntemi
- Erişim Protokolü (http, https)

Hem, iç ve dış URL'leri şu biçimde olması durumunda joker karakterler içeren uygulamalar yayımlayabilmeniz için:

> http (s) :/ / *. \<etki alanı\>

Örneğin: `http(s)://*.adventure-works.com`.

İç ve dış URL, en iyi uygulama farklı etki alanlarını kullanabilirsiniz, ancak bunlar aynı olmalıdır. URL'lerinden biri joker karakter yoksa, uygulama yayımlama sırasında bir hata görürsünüz.

Farklı yapılandırma ayarları ile ek uygulamalarınız varsa, bu özel durumlar için joker karakter belirlenen Varsayılanların üzerine yazmak için ayrı uygulamalar olarak yayımlamanız gerekir. Joker karakter olmayan uygulamalar joker uygulamalar her zaman önceliklidir. Yapılandırma açısından bakıldığında, bunlar "Yeni" Normal uygulamalardır.

Bir joker uygulama oluşturma temel aynı [uygulama yayımlama akışını](application-proxy-add-on-premises-application.md) olan diğer tüm uygulamalar için kullanılabilir. Tek fark, URL'leri ve potansiyel olarak SSO yapılandırmasını bir joker karakter dahil edilir.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için bu gereksinimlerini karşılamanızın emin olun.

### <a name="custom-domains"></a>Özel etki alanları

Sırada [özel etki alanları](application-proxy-configure-custom-domain.md) olan joker karakter içeren uygulamalar için bir önkoşul oldukları diğer tüm uygulamaları için isteğe bağlı. Özel etki alanları oluşturmak, gerektirir:

1. Azure içinde doğrulanmış bir etki alanı oluşturun.
1. Bir SSL sertifikası PFX biçimi, uygulama ara sunucusuna yükleyin.

Uygulama oluşturmayı planladığınız eşleştirmek için joker karakter sertifika kullanmayı düşünmeniz gerekir. Alternatif olarak, yalnızca belirli uygulamaların listeleyen bir sertifika kullanabilirsiniz. Bu durumda, yalnızca sertifikasında listelenen uygulamalar bu joker uygulama erişilebilir.

Güvenlik nedeniyle, bu bir gereksinim ve joker karakter özel etki alanı için dış URL'yi kullanamayan uygulamalar için destekleyeceğiz değil.

### <a name="dns-updates"></a>DNS güncelleştirmeleri

Özel etki alanı kullanırken, bir DNS girişi için dış URL ile bir CNAME kaydı oluşturmanız gerekir (örneğin, `*.adventure-works.com`) dış uygulama ara sunucusu uç nokta URL'sine gidin. Joker karakter içeren uygulamalar için CNAME kaydı ilgili dış URL'lere işaret edecek şekilde gerekir:

> `<yourAADTenantId>.tenant.runtime.msappproxy.net`

CNAME doğru yapılandırdığınızdan, kullanabileceğinizi doğrulamak için [nslookup](https://docs.microsoft.com/windows-server/administration/windows-commands/nslookup) hedef uç noktalardan biri, örneğin, üzerinde `expenses.adventure-works.com`.  Yanıtınız zaten belirtilen diğer ad içermelidir (`<yourAADTenantId>.tenant.runtime.msappproxy.net`).

## <a name="considerations"></a>Dikkat edilmesi gerekenler

Joker karakter içeren uygulamalar için hesaba atmanız gereken bazı noktalar aşağıda verilmiştir.

### <a name="accepted-formats"></a>Kabul edilen biçimler

Joker karakter içeren uygulamalar için **İç URL** olarak biçimlendirilmelidir `http(s)://*.<domain>`.

![İç URL için http (s) biçimi kullanmak :/ / *. \<etki alanı >](./media/application-proxy-wildcard/22.png)

Yapılandırırken bir **dış URL**, aşağıdaki biçimi kullanmanız gerekir: `https://*.<custom domain>`

![Dış URL biçimi https://* kullanın. \<özel etki alanı >](./media/application-proxy-wildcard/21.png)

Joker karakter, birden fazla joker karakterler veya diğer normal ifade dizeleri diğer konumlarını desteklenmez ve hatalara neden oluyor.

### <a name="excluding-applications-from-the-wildcard"></a>Joker karakter ' uygulamaları hariç

Bir uygulama tarafından joker uygulamadan hariç tutabilirsiniz

- Normal bir uygulama olarak özel durum uygulama yayımlama
- Joker karakter yalnızca DNS ayarlarınızı aracılığıyla belirli uygulamalar için etkinleştirme

Normal bir uygulama olarak bir uygulama yayımlama, joker karakter bir uygulama hariç tutmak için tercih edilen yöntemdir. Joker karakter içeren uygulamalar, özel durumlar en baştan uygulanmasını sağlamak için önce hariç tutulan uygulamalar yayımlamanız gerekir. En belirgin uygulama her zaman – olarak yayımlanan uygulama öncelikli olur `budgets.finance.adventure-works.com` uygulamanın önceliklidir `*.finance.adventure-works.com`, hangi sırayla önceliklidir uygulamanın `*.adventure-works.com`.

Ayrıca, DNS Yönetimi aracılığıyla belirli uygulamalar için yalnızca iş için joker karakter sınırlayabilirsiniz. En iyi uygulama, bir joker karakter içeren ve biçimi, yapılandırdığınız dış URL ile eşleşen bir CNAME girişi oluşturmanız gerekir. Ancak, joker karakterler için bunun yerine belirli bir uygulama URL'lerini işaret edebilir. Örneğin, yerine, `*.adventure-works.com`, işaret `hr.adventure-works.com`, `expenses.adventure-works.com` ve `travel.adventure-works.com individually` için `000aa000-11b1-2ccc-d333-4444eee4444e.tenant.runtime.msappproxy.net`.

Bu seçeneği kullanırsanız, ayrıca başka bir CNAME girişi için bir değer gerekir `AppId.domain`, örneğin, `00000000-1a11-22b2-c333-444d4d4dd444.adventure-works.com`de aynı konuma gelin. Bulabilirsiniz **AppID** joker uygulama, uygulama özellikleri sayfasında:

![Uygulamanın özellik sayfasında uygulama Kimliğini bulun](./media/application-proxy-wildcard/01.png)

### <a name="setting-the-homepage-url-for-the-myapps-panel"></a>Giriş sayfası URL'si MyApps paneli için ayarlama

Joker uygulama içinde yalnızca tek bir kutucuk ile temsil edilen [MyApps paneli](https://myapps.microsoft.com). Bu kutucuk, varsayılan olarak gizlidir. Kutucuğu Göster ve belirli bir sayfada kullanıcı land olması için:

1. İçin yönergeleri izleyin [giriş sayfası URL ayarını](application-proxy-configure-custom-home-page.md).
1. Ayarlama **Göster uygulama** için **true** uygulama özellikleri sayfasında.

### <a name="kerberos-constrained-delegation"></a>Kısıtlı Kerberos temsilcisi seçme

Kullanan uygulamalar için [kerberos Kısıtlı temsilci (KCD) SSO yöntemi olarak](application-proxy-configure-single-sign-on-with-kcd.md), SPN listelenen SSO yöntemi bir joker karakter oluşturmanız da gerekebilir. Örneğin, SPN olabilir: `HTTP/*.adventure-works.com`. Hala tek SPN'ler, arka uç sunucuları üzerinde yapılandırılmış olması gerekir (örneğin, `http://expenses.adventure-works.com and HTTP/travel.adventure-works.com`).

## <a name="scenario-1-general-wildcard-application"></a>Senaryo 1: Genel joker uygulama

Bu senaryoda, yayımlamak istediğiniz üç farklı uygulamaları vardır:

- `expenses.adventure-works.com`
- `hr.adventure-works.com`
- `travel.adventure-works.com`

Üç uygulama:

- Tüm kullanıcılar tarafından kullanılır
- Kullanım *tümleşik Windows kimlik doğrulaması*
- Aynı özelliklere sahiptir

Özetlenen adımları kullanarak joker uygulama yayımlayabilirsiniz [Azure AD uygulama ara sunucusu kullanarak uygulama yayımlama](application-proxy-add-on-premises-application.md). Bu senaryo varsayar:

- Şu Kimliğe sahip bir kiracı: `000aa000-11b1-2ccc-d333-4444eee4444e`
- Doğrulanmış bir etki alanı adında `adventure-works.com` yapılandırıldı.
- A **CNAME** işaret giriş `*.adventure-works.com` için `000aa000-11b1-2ccc-d333-4444eee4444e.tenant.runtime.msappproxy.net` oluşturuldu.

Aşağıdaki [belgelenen adımları](application-proxy-add-on-premises-application.md), kiracınızda yeni bir uygulama proxy'si uygulaması oluşturun. Bu örnekte, aşağıdaki alanlarda joker karakterdir:

- İç URL:

    ![Örnek: İç URL joker karakter](./media/application-proxy-wildcard/42.png)

- Dış URL:

    ![Örnek: Dış URL'yi joker karakter](./media/application-proxy-wildcard/43.png)

- İç uygulama SPN'si:

    ![Örnek: Joker karakter SPN yapılandırma](./media/application-proxy-wildcard/44.png)

Joker uygulama yayımladığınızda, artık üç uygulamalarınız için kullanılan URL'leri giderek erişebilirsiniz (örneğin, `travel.adventure-works.com`).

Aşağıdaki yapı yapılandırmasını uygular:

![Örnek Yapılandırması tarafından uygulanan yapısını gösterir](./media/application-proxy-wildcard/05.png)

| Renk | Açıklama |
| ---   | ---         |
| Mavi  | Uygulamaları açıkça yayımlanan ve Azure portalında görünür. |
| Gri  | Uygulamaları üst uygulama üzerinden erişilebilir olabilir. |

## <a name="scenario-2-general-wildcard-application-with-exception"></a>Senaryo 2: Özel durum ile genel joker uygulama

Bu senaryoda, ayrıca üç genel için başka bir uygulama, uygulamanız `finance.adventure-works.com`, yalnızca olacağı Finans bölme erişilebilir. Geçerli uygulama yapısıyla Finans uygulamanız aracılığıyla joker uygulama ve tüm çalışanlar tarafından erişilebilir olacaktır. Bunu değiştirmek için uygulamanızın, joker karakter daha kısıtlayıcı izinlerle ayrı bir uygulama olarak, Finans yapılandırarak hariç tutun.

CNAME kayıtları, işaret ettiği olmadığından emin olmak gereken `finance.adventure-works.com` uygulama için uygulama proxy'si sayfasında belirtilen uygulama belirli uç. Bu senaryo için `finance.adventure-works.com` işaret `https://finance-awcycles.msappproxy.net/`.

Aşağıdaki [belgelenen adımları](application-proxy-add-on-premises-application.md), bu senaryo aşağıdaki ayarları gerektirir:

- İçinde **İç URL**, ayarladığınız **Finans** yerine bir joker karakter.

    ![Örnek: İç URL joker karakter yerine Finans ayarlayın](./media/application-proxy-wildcard/52.png)

- İçinde **dış URL**, ayarladığınız **Finans** yerine bir joker karakter.

    ![Örnek: Dış URL'yi joker karakter yerine Finans ayarlayın](./media/application-proxy-wildcard/53.png)

- Ayarladığınız iç uygulama SPN'si **Finans** yerine bir joker karakter.

    ![Örnek: Joker karakter yerine Finans SPN yapılandırmasında ayarlayın](./media/application-proxy-wildcard/54.png)

Bu yapılandırma aşağıdaki senaryoyu uygular:

![Örnek senaryo tarafından uygulanan yapılandırmasını gösterir](./media/application-proxy-wildcard/09.png)

Çünkü `finance.adventure-works.com` daha fazla belirli bir URL olduğundan `*.adventure-works.com`, önceliği alır. Kullanıcılar için gezinme `finance.adventure-works.com` Finans kaynakları uygulamada belirtilen deneyime sahiptir. Bu durumda, yalnızca finans çalışanlarıyla erişebilir `finance.adventure-works.com`.

Birden çok uygulama için finans yayımlanan varsa ve sahip olduğunuz `finance.adventure-works.com` doğrulanmış bir etki alanı başka bir joker uygulama yayımlayabilirsiniz `*.finance.adventure-works.com`. Bu genel daha belirli olduğundan `*.adventure-works.com`, bir kullanıcı bir uygulama Finans etki alanındaki erişirse öncelik kazanır.

## <a name="next-steps"></a>Sonraki adımlar

- Hakkında daha fazla bilgi edinmek için **özel etki alanları**, bkz: [Azure AD uygulama proxy'sinde özel etki alanları ile çalışma](application-proxy-configure-custom-domain.md).
- Hakkında daha fazla bilgi edinmek için **uygulamaları yayımlama**, bkz: [Azure AD uygulama ara sunucusu kullanarak uygulama yayımlama](application-proxy-add-on-premises-application.md)
