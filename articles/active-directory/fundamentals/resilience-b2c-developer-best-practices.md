---
title: Azure AD B2C kullanarak geliştirici en iyi uygulamaları esnekliği | Microsoft Docs
description: Azure AD B2C kullanarak müşteri kimliği ve erişim yönetiminde geliştirici en iyi uygulamaları esnekliği
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: gargi-sinha
ms.author: gasinh
manager: martinco
ms.reviewer: ''
ms.date: 11/30/2020
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: ff7505e7c47b93f32efd9de60463873026247329
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98724934"
---
# <a name="resilience-through-developer-best-practices"></a>Geliştirici en iyi uygulamaları esnekliği

Bu makalede, büyük müşterilerle çalışırken deneyimimizi temel alan bazı dersleri paylaşıyoruz. Bu önerileri, hizmetlerinizin tasarımında ve uygulamalarında dikkate alabilirsiniz.

![Görüntü geliştirici deneyimi bileşenlerini gösterir](media/resilience-b2c-developer-best-practices/developer-best-practices-architecture.png)

## <a name="use-the-microsoft-authentication-library-msal"></a>Microsoft kimlik doğrulama kitaplığı 'nı (MSAL) kullanma

ASP.NET için [Microsoft kimlik doğrulama kitaplığı (msal)](../develop/msal-overview.md) ve [Microsoft Identity Web kimlik doğrulaması kitaplığı](../develop/reference-v2-libraries.md) , bir uygulamanın gerektirdiği belirteçleri alma, yönetme, önbelleğe alma ve yenilemeyi basitleştirir. Bu kitaplıklar, uygulama dayanıklılığı geliştiren özellikler dahil olmak üzere özellikle Microsoft kimliğini desteklemek için iyileştirilmiştir.

Geliştiriciler MSAL 'in en son sürümlerini benimsemeli ve güncel kalmalıdır. Uygulamalarınızdaki [kimlik doğrulama ve yetkilendirme esnekliği artırma](resilience-app-development-overview.md) bölümüne bakın. Mümkün olduğunda, kendi kimlik doğrulama yığınınızı uygulamaktan ve iyi belirlenen kitaplıkları kullanmaktan kaçının.

## <a name="optimize-directory-reads-and-writes"></a>Dizin okuma ve yazma işlemlerini iyileştirme

Microsoft Azure AD B2C dizin hizmeti günde milyarlarca kimlik doğrulamasını destekler. Saniye başına yüksek hızlı okuma için tasarlanmıştır. Bağımlılıkları en aza indirmek ve esnekliği artırmak için yazmanızı iyileştirin.

### <a name="how-to-optimize-directory-reads-and-writes"></a>Dizin okuma ve yazma işlemlerini iyileştirme

- **Oturum açma sırasında dizine yazma Işlevlerinden kaçının**: özel ilkeleriniz üzerinde bir ön koşul (if yan tümcesi) olmadan oturum açma sırasında hiç bir yazma yürütün. Oturum açma sırasında yazma gerektiren bir kullanım durumu, [kullanıcı parolalarının tam zamanında geçişini](https://github.com/azure-ad-b2c/user-migration/tree/master/seamless-account-migration)gerektirir. Her oturum açma için yazma gerektiren herhangi bir senaryoyu kullanmaktan kaçının.

  - Bir Kullanıcı yolculuğunda [önkoşulları](../../active-directory-b2c/userjourneys.md) şöyle görünür:

  ``
  <Precondition Type="ClaimEquals" ExecuteActionsIf="true"> 
  <Value>requiresMigration</Value>
  ...
  < Precondition/>
  ``
  - [BIR CAPTCHA sistemiyle tümleştirerek bot tabanlı oturum açma](https://github.com/azure-ad-b2c/samples/tree/master/policies/captcha-integration)işlemleri oluşturma.

  - Kaydolma ve oturum açma benzetimi için bir [Yük testi örneği](../../active-directory-b2c/best-practices.md#testing) kullanın. 

- **Azaltmayı anlayın**: Dizin hem uygulama hem de kiracı düzeyi azaltma kurallarını uygular. Okuma/yazma, yazma/gönderme, güncelleştirme/YERLEŞTIRME ve silme/SILME işlemleri için daha fazla hız sınırı vardır ve her işlem farklı sınırlara sahiptir.

  - Oturum açma sırasında yazma, yeni kullanıcılar için bir GÖNDERIN altına düşecek veya mevcut kullanıcıları YERLEŞTIRMEYECEKTIR.

  - Her oturum açma işleminde bir Kullanıcı oluşturan veya güncelleştiren özel bir ilke, uygulama düzeyinde bir YERLEŞTIRME veya GÖNDERI oranı sınırına ulaşabilirler. Azure AD veya Microsoft Graph aracılığıyla Dizin nesneleri güncelleştirilirken de aynı sınırlar geçerlidir. Benzer şekilde, her oturum açma üzerinde okuma sayısını en düşük düzeyde tutmak için okumaları inceleyin.

  - Dizin yazma oranını tahmin etmek ve azaltmaktan kaçınmak için tepe yükü tahmin edin. Yoğun trafik tahminleri kaydolma, oturum açma ve Multi-Factor Authentication (MFA) gibi eylemler için tahminleri içermelidir. Azure AD B2C sistemini ve uygulamanızı yoğun trafiğe karşı test ettiğinizden emin olun. Aşağı akış uygulamalarınızın veya hizmetlerinizin ne zaman azalmadan yükü azaltma olmadan işleyebilir Azure AD B2C mümkündür.

  - Geçiş zaman çizelgenizi anlayın ve planlayın. Microsoft Graph kullanarak Azure AD B2C kullanıcıları geçirmeyi planlarken, kullanıcıların geçişini tamamlaması için gereken süreyi hesaplamak üzere uygulamayı ve kiracı sınırlarını göz önünde bulundurun. Kullanıcı oluşturma işinizi veya komut dosyanızı iki uygulama kullanarak böldüğünüz takdirde, uygulama başına sınırı kullanabilirsiniz. Yine de her kiracı eşiğinin altında kalması gerekir.

  - Geçiş işinizin diğer uygulamalarda etkilerini anlayın. Diğer bağlı uygulamalar tarafından sunulan canlı trafiği göz önünde bulundurun. böylece, kiracı düzeyinde ve kaynak üzerinde etkin uygulamanız için bir azalma olup olmadığından emin olun. Daha fazla bilgi için [Microsoft Graph azaltma Kılavuzu](/graph/throttling)' na bakın.
  
## <a name="extend-token-lifetimes"></a>Belirteç ömrünü Genişlet

Olası bir olayda, Azure AD B2C kimlik doğrulama hizmeti yeni oturum açma işlemlerini ve oturum açma işlemlerini tamamlayamadığı zaman, oturum açan kullanıcılar için de risk azaltma sağlayabilirsiniz. [Yapılandırma](../../active-directory-b2c/configure-tokens.md)sayesinde, zaten oturum açmış olan kullanıcıların, Kullanıcı uygulamadan oturum açana veya [oturum](../../active-directory-b2c/session-behavior.md) zaman aşımına uğramasından kaynaklanan bir kesinti olmadan uygulamayı kullanmaya devam etmesine izin verebilirsiniz.

İş gereksinimleriniz ve istenen son kullanıcı deneyimi, hem Web hem de tek sayfalı uygulamalar (maça 'Lar) için belirteç yenileme sıklığınızı dikte edecektir.

### <a name="how-to-extend-token-lifetimes"></a>Belirteç ömrünü genişletme

- **Web uygulamaları**: kimlik doğrulama belirtecinin oturum açma başlangıcında doğrulandığı Web uygulamaları için, uygulama, oturum geçerliliğini genişletmeye devam etmek üzere oturum tanımlama bilgisine bağımlıdır.

  - Kullanıcıların, oturumları Kullanıcı etkinliğine göre yenilemeye devam edecek olan sıralı oturum sürelerini uygulayarak oturum açmış kalmasını sağlar. Uzun süreli bir belirteç verme kesintisi varsa, bu oturum süreleri uygulamada bir kerelik yapılandırması olarak daha da artırılabilir. Oturumun ömrünü izin verilen en fazla süre olarak tutun.

- **Maça**'lar: bir spa, API 'lere çağrı yapmak için erişim belirteçlerine bağlı olabilir. SPA, genellikle yenileme belirtecine neden olmayan örtük akışı kullanır. SPA, Azure AD B2C ile hala etkin bir oturum varsa, yetkilendirme uç noktasına karşı yeni belirteç isteklerini gerçekleştirmek için bir gizli iframe kullanabilir. Maça, kullanıcının uygulamayı kullanmaya devam etmesine izin vermek için birkaç seçenek mevcuttur.

  - Erişim belirtecinin geçerlilik süresini, iş gereksinimlerinizi karşılayacak şekilde genişletin.

  - Kimlik doğrulama proxy 'si olarak bir API ağ geçidi kullanmak için uygulamanızı oluşturun. Bu yapılandırmada, SPA herhangi bir kimlik doğrulaması olmadan yüklenir ve API çağrıları API ağ geçidine yapılır. API Gateway, bir ilkeye göre [yetkilendirme kodu izni](https://oauth.net/2/grant-types/authorization-code/) kullanarak kullanıcıyı bir oturum açma işlemiyle gönderir ve kullanıcının kimliğini doğrular. Daha sonra, API ağ geçidi ve istemci arasındaki kimlik doğrulama oturumu bir kimlik doğrulama tanımlama bilgisi kullanılarak tutulur. API 'Ler API ağ geçidi veya sertifikalar, istemci kimlik bilgileri ya da API anahtarları gibi başka bir doğrudan kimlik doğrulama yöntemi tarafından edinilen belirteci kullanarak API Gateway 'den hizmet verilir.

  - Kod değişimi (PKCE) ve çıkış noktaları arası kaynak paylaşımı (CORS) desteği için düzeltme anahtarıyla, örtük [kimlik doğrulama kodu verme AKıŞıNA](../../active-directory-b2c/implicit-flow-single-page-application.md) [Spa 'yı geçirin](https://developer.microsoft.com/identity/blogs/msal-js-2-0-supports-authorization-code-flow-is-now-generally-available/) . Uygulamanızı, Web uygulamalarının dayanıklılığını sağlamak için MSAL.js 1. x ' ten MSAL.js 2. x ' e geçirin.

  - Mobil uygulamalar için hem yenileme hem de erişim belirteci yaşam sürelerinin genişletilmesi önerilir.

- **Arka uç veya mikro hizmet uygulamaları**: arka uç (daemon) uygulamaları etkileşimli değil ve bir kullanıcı bağlamında olmadığından, belirteç hırsızlığı 'nın aday olması büyük ölçüde azalır. Öneri, güvenlik ve ömür arasında bir denge kurmak ve uzun bir belirteç ömrü ayarlamak.

## <a name="configure-single-sign-on"></a>Çoklu oturum açmayı yapılandırma

[Çoklu oturum açma (SSO)](../manage-apps/what-is-single-sign-on.md)ile kullanıcılar tek bir hesapla oturum açıp birden çok uygulamaya erişim sağlar. Uygulama, platform veya etki alanı adından bağımsız olarak bir Web, mobil veya tek sayfalı uygulama (SPA) olabilir. Kullanıcı başlangıçta bir uygulamaya oturum açtığında, Azure AD B2C [tanımlama bilgisi tabanlı bir oturumu](../../active-directory-b2c/session-behavior.md)devam ettirir.

Sonraki kimlik doğrulama istekleri üzerine Azure AD B2C, tanımlama bilgisi tabanlı oturumu okuyup doğrular ve kullanıcıdan tekrar oturum açmasını istemeden bir erişim belirteci yayınlar. SSO, ilke veya uygulama üzerinde sınırlı bir kapsamla yapılandırılmışsa, diğer ilkelere ve uygulamalara daha sonra erişmek için yeni kimlik doğrulaması gerekir.

### <a name="how-to-configure-sso"></a>SSO 'yu yapılandırma

Kiracınızdaki birden fazla uygulamanın ve Kullanıcı akışının aynı kullanıcı oturumunu paylaşmasına izin vermek için SSO 'yu kiracı genelinde (varsayılan) olarak [yapılandırın](../hybrid/how-to-connect-sso-quick-start.md) . Kiracı genelinde yapılandırma, kimlik doğrulaması için en çok dayanıklılık sağlar.  

## <a name="safe-deployment-practices"></a>Güvenli dağıtım uygulamaları

En yaygın kesintiler hizmet, kod ve yapılandırma değişiklerdir. Sürekli tümleştirme ve sürekli teslim (CICD) işlemleri ve araçları, büyük ölçekte hızlı dağıtımla ilgili yardımcı olur ve test etme ve üretime dağıtım sırasında insan hatalarını azaltır. Hata azaltma, verimlilik ve tutarlılık için CICD 'yi benimseyin. [Azure Pipelines](/azure/devops/pipelines/apps/cd/azure/cicd-data-overview) , CICD 'nin bir örneğidir.

## <a name="web-application-firewall"></a>Web uygulaması güvenlik duvarı

Uygulamalarınızı dağıtılmış hizmet reddi (DDoS) saldırıları, SQL ınjler, siteler arası betik oluşturma, uzaktan kod yürütme ve [OWASP en iyi 10](https://owasp.org/www-project-top-ten/)' da belgelendiği gibi bilinen güvenlik açıklarına karşı koruyun. Web uygulaması güvenlik duvarı (WAF) dağıtımı, yaygın güvenlik açıklarından ve güvenlik açıklarına karşı savunabilirsiniz.

- Saldırılara karşı merkezi koruma sağlayan Azure [WAF](../../web-application-firewall/overview.md)' i kullanın.

- Azure AD B2C kullanırken [çok katmanlı koruma sağlamak Için Azure AD kimlik koruması ve koşullu erişim](../../active-directory-b2c/conditional-access-identity-protection-overview.md) ile WAF kullanın.  

## <a name="secrets-rotation"></a>Gizli dizi dönüşü

Azure AD B2C uygulamalar, API 'Ler, ilkeler ve şifreleme için gizli dizileri kullanır. Gizli dizi güvenli kimlik doğrulaması, dış etkileşimler ve depolama. Ulusal Standartlar ve Teknoloji Enstitüsü (NıST), belirli bir anahtarın bir cryptoperiod tarafından kullanılmak üzere yetkilendirildiği zaman aralığını çağırır. İş gereksinimlerinizi karşılamak için [cryptoperiod](https://csrc.nist.gov/publications/detail/sp/800-57-part-1/rev-5/final) 'ın doğru uzunluğunu seçin. Geliştiricilerin süre sonunu el ile ayarlaması ve gizli dizileri zaman aşımı süresi dolduktan sonra döndürmesi gerekir.

### <a name="how-to-implement-secret-rotation"></a>Gizli dizi dönüşü uygulama

- Azure AD kimlik doğrulamasını destekleyen herhangi bir hizmette kimlik doğrulaması yapmak için desteklenen kaynaklar için [Yönetilen kimlikler](../managed-identities-azure-resources/overview.md) kullanın. Yönetilen kimlikler kullandığınızda, kimlik bilgilerinin dönmesi dahil olmak üzere kaynakları otomatik olarak yönetebilirsiniz.

- Azure AD B2C ' de [yapılandırılan tüm anahtar ve sertifikaların](../../active-directory-b2c/policy-keys-overview.md) envanterini alın. Bu liste, özel ilkeler, [API 'ler](../../active-directory-b2c/secure-rest-api.md), imzalama kimliği BELIRTECI ve SAML sertifikalarında kullanılan anahtarları dahil ediyor olabilir.

- CICD kullanarak, tahmini en yüksek mevsimi iki ay içinde sona ermek üzere olan gizli dizileri döndürün. Bir sertifikayla ilişkili önerilen en büyük şifre süresi bir yıldır.

- Parolalar ve sertifikalar gibi API erişimi kimlik bilgilerini proaktif olarak izleyin ve döndürün.

## <a name="test-rest-apis"></a>Test REST API 'Leri

Dayanıklılık bağlamında, REST API 'lerinin test edilmesi, HTTP kodlarının, yanıt yükünün, üstbilgilerinin ve performansının doğrulanmasını içermesi gerekir. Test, yalnızca mutlu yol testleri içermemelidir, ancak API 'nin sorun senaryolarını sorunsuz bir şekilde işlediğini de denetler.

### <a name="how-to-test-apis"></a>API 'Leri test etme

Test planınızın [KAPSAMLı API testlerini](../../active-directory-b2c/best-practices.md#testing)içermesini öneririz. Promosyon veya tatil trafiği nedeniyle yaklaşan bir aşırı yük için planlama yapıyorsanız, yük testinizi Yeni tahminlerle düzeltmeniz gerekir. Bir geliştirici ortamında API 'lerinizin yük testini ve Content Delivery Network (CDN) yürütün ve üretimde değildir.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure AD B2C geliştiricileri için esnekliği kaynakları](resilience-b2c.md)
  - [Dayanıklı son kullanıcı deneyimi](resilient-end-user-experience.md)
  - [Dış işlemlerle esnek arabirimler](resilient-external-processes.md)
  - [İzleme ve analiz aracılığıyla esnekliği](resilience-with-monitoring-alerting.md)
- [Kimlik doğrulama altyapınızda esnekliği oluşturma](resilience-in-infrastructure.md)
- [Uygulamalarınızda kimlik doğrulama ve yetkilendirme esnekliği artırın](resilience-app-development-overview.md)