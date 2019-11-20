---
title: Azure AD 'de OAuth2 örtük verme akışını anlama | Microsoft Docs
description: Azure Active Directory OAuth2 örtük verme akışının uygulaması ve uygulamanızın doğru olup olmadığı hakkında daha fazla bilgi edinin.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 90e42ff9-43b0-4b4f-a222-51df847b2a8d
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/15/2019
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: eb751d4cad036135865af9f97e159da104749388
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69532414"
---
# <a name="understanding-the-oauth2-implicit-grant-flow-in-azure-active-directory-ad"></a>Azure Active Directory (AD) OAuth2 örtük verme akışını anlama

[!INCLUDE [active-directory-develop-applies-v1](../../../includes/active-directory-develop-applies-v1.md)]

OAuth2 örtük verme, OAuth2 belirtiminde en uzun güvenlik sorunları listesine izin vermekle birlikte, ntoriou olur. Henüz, ADAL JS tarafından uygulanan yaklaşım ve SPA uygulamaları yazarken önerdiğimiz bir yaklaşım. Ne sağlar? Bu, tüm dengelerden bağımsız olarak, bir tarayıcıdan JavaScript aracılığıyla bir Web API 'SI kullanan uygulamalar için en iyi yaklaşım, örtük bir yöntemdir.

## <a name="what-is-the-oauth2-implicit-grant"></a>OAuth2 örtük verme nedir?

Quintessential [OAuth2 yetkilendirme kodu verme](https://tools.ietf.org/html/rfc6749#section-1.3.1) , iki ayrı uç nokta kullanan yetkilendirme vericisinden yararlanır. Yetkilendirme uç noktası, bir yetkilendirme kodu ile sonuçlanan Kullanıcı etkileşimi aşaması için kullanılır. Belirteç uç noktası daha sonra istemci tarafından bir erişim belirtecinin kodunu ve genellikle bir yenileme belirtecini değiş tokuş etmek için kullanılır. Yetkilendirme sunucusunun istemcinin kimliğini doğrulayabilmesi için, Web uygulamalarının belirteç uç noktasına kendi uygulama kimlik bilgilerini sunması gerekir.

[OAuth2 örtük verme](https://tools.ietf.org/html/rfc6749#section-1.3.2) , diğer yetkilendirmelerden oluşan bir değişkendir. Bir istemcinin, bir erişim belirteci almasına izin verir (ve [OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html)kullanılırken id_token), belirteç uç noktası ile iletişim kurmadan veya istemcinin kimliğini doğrulamadan doğrudan yetkilendirme uç noktasından. Bu değişken, bir Web tarayıcısında çalışan JavaScript tabanlı uygulamalar için tasarlandı: özgün OAuth2 belirtiminde belirteçler bir URI parçasında döndürülür. Bu, belirteç bitlerini istemcideki JavaScript kodu için kullanılabilir hale getirir, ancak sunucuya doğru yeniden yönlendirmelere dahil edilmezler. OAuth2 örtük izin ' da, yetkilendirme uç noktası önceden sağlanan yeniden yönlendirme URI 'sini kullanarak doğrudan istemciye erişim belirteçleri verir. Ayrıca, belirteç uç noktasıyla iletişim kurmak için JavaScript uygulamasının gerekli olduğu durumlarda gerekli olan çapraz kaynak çağrıları için tüm gereksinimleri ortadan kaldırma avantajına sahiptir.

OAuth2 örtük verme 'nin önemli bir özelliği, bu akışların istemciye hiçbir zaman yenileme belirteçleri döndürmemesinden kaynaklanmaktadır. Sonraki bölümde bunun gerekli olmadığını ve aslında bir güvenlik sorunu olduğunu gösterir.

## <a name="suitable-scenarios-for-the-oauth2-implicit-grant"></a>OAuth2 örtük verme için uygun senaryolar

OAuth2 belirtimi, örtük verme 'nin, Kullanıcı Aracısı uygulamalarının (örneğin, bir tarayıcıda yürütülen JavaScript uygulamaları) etkinleştirilmesi için bu özelliği kaldırdığınızı bildirir. Bu tür uygulamaların tanımlanması, JavaScript kodunun sunucu kaynaklarına (genellikle bir Web API) erişmek ve uygulama kullanıcı deneyiminin uygun şekilde güncelleştirilmesi için kullanılır. Gmail veya Outlook Web Erişimi gibi uygulamalar düşünün: gelen kutunuzdan bir ileti seçtiğinizde, yalnızca ileti görselleştirme paneli yeni seçimi görüntüleyecek şekilde değişir, ancak sayfanın geri kalanı değiştirilmemiş olarak kalır. Bu özellik geleneksel yeniden yönlendirme tabanlı Web Apps 'in aksine, her kullanıcı etkileşimi tam sayfa geri gönderme işlemiyle ve yeni sunucu yanıtının tam sayfa işlemesi ile sonuçlanır.

Üst sınırına yönelik JavaScript tabanlı yaklaşımdan uygulamalar tek sayfalı uygulamalar veya maça olarak adlandırılır. Fikir, bu uygulamaların yalnızca bir ilk HTML sayfası ve ilişkili JavaScript 'e sahip olduğu ve JavaScript aracılığıyla gerçekleştirilen Web API çağrıları tarafından çalıştırılmakta olan bir sonraki etkileşimi sunan bir uygulamadır. Bununla birlikte, uygulamanın genellikle geri gönderme yapılır ancak zaman zaman JS çağrıları gerçekleştirdiği karma yaklaşımlar, seyrek erişimli değildir. örtülü akış kullanımı hakkındaki tartışma, bunlar için de geçerlidir.

Yeniden yönlendirme tabanlı uygulamalar, genellikle istekleri tanımlama bilgileri aracılığıyla güvenli hale getirmeye karşın bu yaklaşım, JavaScript uygulamaları için de çalışmaz. Tanımlama bilgileri, JavaScript çağrıları diğer etki alanlarına yönlendirilirken, yalnızca için oluşturulan etki alanına karşı çalışır. Aslında bu durum genellikle şu şekilde olacaktır: Microsoft Graph API, Office API 'si, Azure API 'SI çağıran uygulamaları, uygulamanın sunulduğu etki alanının dışında bulundurduğunu düşünün. JavaScript uygulamalarına yönelik büyümekte olan bir eğilim, iş işlevlerini uygulamak için üçüncü taraf Web API 'Lerinde% 100 bağlı bir arka uç gerektirmez.

Şu anda, bir Web API 'sine yapılan çağrıları korumanın tercih edilen yöntemi, her çağrının bir OAuth2 erişim belirteciyle birlikte olduğu OAuth2 taşıyıcı belirteç yaklaşımını kullanmaktır. Web API 'SI, gelen erişim belirtecini inceler ve gerekli kapsamlar içinde bulunursa, istenen işleme erişim izni verir. Örtük akış, JavaScript uygulamalarının bir Web API 'sine erişim belirteçleri elde etmek için kullanışlı bir mekanizma sağlar ve tanımlama bilgilerine göre çok sayıda avantaj sunar:

* Belirteçler, çapraz kaynak çağrılarına gerek kalmadan güvenilir bir şekilde elde edilebilir: belirteçlerin döndürüleceği yeniden yönlendirme URI 'sinin zorunlu kaydı, belirteçlerin hatalı olarak döndürülmediğini garanti eder
* JavaScript uygulamaları, istedikleri sayıda Web API 'si için, her bir etki alanında kısıtlama olmadan, gerek duydukları sayıda erişim belirteci elde edebilir
* Oturum veya yerel depolama gibi HTML5 özellikleri, belirteç önbelleğe alma ve ömür yönetimi üzerinde tam denetim verir, ancak tanımlama bilgileri yönetimi uygulama için donuk olur
* Erişim belirteçleri siteler arası istek sahteciliğini önleme (CSRF) saldırılarına karşı savunmasız değildir

Örtük verme akışı, genellikle güvenlik nedenleriyle yenileme belirteçleri vermez. Yenileme belirteci, erişim belirteçleri olarak en dar kapsamlı değildir, bu nedenle çok daha fazla güç verilmeye devam ediyor ve çok daha fazla Zararsızmış olabilir. Örtük akışta, belirteçler URL 'de dağıtılır, bu nedenle, geri alma riski yetkilendirme kodu verenden daha yüksektir.

Ancak, bir JavaScript uygulamasının, kullanıcıya kimlik bilgileri için sürekli olarak sormadan erişim belirteçlerini yenilemeye yönelik başka bir mekanizması vardır. Uygulama, Azure AD 'nin yetkilendirme uç noktasına karşı yeni belirteç istekleri gerçekleştirmek için gizli bir iframe kullanabilir: tarayıcının hala Azure AD etki alanına karşı etkin bir oturum (okuma: bir oturum tanımlama bilgisi vardır) olduğu sürece, kimlik doğrulama isteği Kullanıcı etkileşimi gerektirmeden başarıyla meydana gelir.

Bu model, JavaScript uygulamasına, erişim belirteçlerini bağımsız olarak yenileme ve hatta yeni bir API için yeni bir API (Kullanıcı tarafından daha önce kendisine onay verilmiş olan) için yeni bir API alma özelliği verir. Bu, yenileme belirteci gibi yüksek değerli yapıtları alma, sürdürme ve koruma yükünü ortadan kaldırır. Sessiz yenilemeyi mümkün kılan yapıt, Azure AD oturum tanımlama bilgisi uygulama dışında yönetilir. Bu yaklaşımın bir diğer avantajı, Azure AD 'de oturum açmış uygulamalardan herhangi birini kullanarak, tarayıcı sekmelerinden birinde çalışan bir kullanıcının Azure AD 'den oturumu açmasını sağlayabilir. Bu, Azure AD oturum tanımlama bilgisinin silinmesine neden olur ve JavaScript uygulaması, oturum açan kullanıcı için belirteçleri yenileme özelliğini otomatik olarak kaybeder.

## <a name="is-the-implicit-grant-suitable-for-my-app"></a>Örtük izin uygulamam için uygun midir?

Örtük verme, diğer izin verenden daha fazla risk sunar ve dikkat etmeniz gereken alanların iyi belgelendiğinden (örneğin, [örtük akışta kaynak sahibine taklit etmek Için erişim belirtecinin kötüye kullanımı][OAuth2-Spec-Implicit-Misuse] ve [OAuth 2,0 tehdit modeli ve güvenliği) Dikkat edilecek noktalar][OAuth2-Threat-Model-And-Security-Implications]). Ancak, daha yüksek riskli profil büyük ölçüde, uzak bir kaynak tarafından tarayıcıya hizmet veren etkin kodu çalıştıran uygulamaların etkinleştirilmesi amaçlıyordu. SPA mimarisi planlarken, arka uç bileşenleri yoksa veya JavaScript aracılığıyla bir Web API 'SI çağırmak istiyorsanız, belirteç alımı için örtük akışın kullanılması önerilir.

Uygulamanız yerel bir istemcili ise, örtük akış harika bir uyum değildir. Azure AD oturum tanımlama bilgisinin bir yerel istemci bağlamında yokluğu, uygulamanızı uzun süreli bir oturum koruma yoluyla kaldırır. Yani, uygulamanız yeni kaynaklar için erişim belirteçleri alırken Kullanıcı tarafından tekrar tekrar sorulacak.

Arka uç içeren ve arka uç kodundan bir API kullanan bir Web uygulaması geliştiriyorsanız, örtük akış de uygun değildir. Diğer izinler size daha fazla güç verir. Örneğin, OAuth2 istemci kimlik bilgileri verme, Kullanıcı temsilcilerinin aksine uygulamanın kendisine atanan izinleri yansıtan belirteçleri elde etme yeteneği sağlar. Bu, bir kullanıcı oturumunda etkin bir şekilde olmadığında ve bu şekilde devam ettiğinizde, istemcinin kaynaklara programlı erişim sağlama yeteneğine sahip olduğu anlamına gelir. Ancak, bu tür izinler daha yüksek güvenlik garantisi verir. Örneğin, erişim belirteçleri Kullanıcı tarayıcısından hiçbir şekilde geçiş yapmayın, tarayıcı geçmişine kaydedilmeleri ve bu şekilde devam eder. İstemci uygulaması, belirteç istenirken güçlü kimlik doğrulaması da gerçekleştirebilir.

## <a name="next-steps"></a>Sonraki adımlar

* Geliştirici kaynaklarının tüm listesi için, protokoller ve OAuth2 yetkilendirmesi için başvuru bilgileri de dahil olmak üzere Azure AD tarafından desteklenen [Azure AD Geliştirici Kılavuzu][AAD-Developers-Guide] ' na bakın.
* Uygulama Tümleştirme sürecinde daha ayrıntılı bilgi için bkz. [bir uygulamayı Azure AD ile tümleştirme][ACOM-How-To-Integrate] .

<!--Image references-->

<!--Reference style links in use-->
[AAD-Developers-Guide]:azure-ad-developers-guide.md
[ACOM-How-And-Why-Apps-Added-To-AAD]: active-directory-how-applications-are-added.md
[ACOM-How-To-Integrate]: active-directory-how-to-integrate.md
[OAuth2-Spec-Implicit-Misuse]: https://tools.ietf.org/html/rfc6749#section-10.16
[OAuth2-Threat-Model-And-Security-Implications]: https://tools.ietf.org/html/rfc6819
