---
title: Azure AD B2C kullanarak dayanıklı son kullanıcı deneyimi | Microsoft Docs
description: Azure AD B2C kullanarak son kullanıcı deneyiminde esnekliği oluşturma yöntemleri
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
ms.openlocfilehash: 53cdbfb3ba531330c37f86233cbed990da4efaf4
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107258393"
---
# <a name="resilient-end-user-experience"></a>Dayanıklı son kullanıcı deneyimi

Kaydolma ve oturum açma Son Kullanıcı deneyimi aşağıdaki öğelerden oluşur:

- Kullanıcının CSS, HTML ve JavaScript gibi etkileşimde bulunduğu arabirimler

- Oluşturduğunuz Kullanıcı akışları ve özel ilkeler (kaydolma, oturum açma ve profil düzenleme gibi)

- Uygulamanıza yönelik kimlik sağlayıcıları (IDPs); Örneğin, yerel hesap Kullanıcı adı/parola, Outlook, Facebook ve Google

![Görüntü Son Kullanıcı deneyimi bileşenlerini gösterir](media/resilient-end-user-experiences/end-user-experience-architecture.png)

## <a name="choose-between-user-flow-and-custom-policy"></a>Kullanıcı akışı ve özel ilke arasında seçim yapın  

En yaygın kimlik görevlerini ayarlamanıza yardımcı olması için Azure AD B2C yerleşik yapılandırılabilir [Kullanıcı akışları](../../active-directory-b2c/user-flow-overview.md)sağlar. Size en fazla esneklik sunan kendi [özel ilkelerinizi](../../active-directory-b2c/custom-policy-overview.md)da oluşturabilirsiniz. Ancak, özel ilkelerin yalnızca karmaşık senaryolara yönelik olarak kullanılması önerilir.

### <a name="how-to-decide-between-user-flow-and-custom-policy"></a>Kullanıcı akışı ve özel ilke arasında karar verme

İş gereksinimlerinize göre karşılanabileceği yerleşik Kullanıcı akışları ' nı seçin. Microsoft tarafından kapsamlı bir şekilde test edildiğinden, bu kimlik Kullanıcı akışlarının ilke düzeyi işlevsel, performans veya ölçeğini doğrulamak için gereken testi en aza indirmenize izin verebilirsiniz. Uygulamalarınızın işlevselliği, performansı ve ölçeği için yine de test etmeniz gerekir.

İş gereksinimleriniz nedeniyle [özel ilkeler](../../active-directory-b2c/user-flow-overview.md) ' i seçmeniz gerekir, uygulama düzeyinde teste ek olarak işlevsel, performans veya ölçek için ilke düzeyinde test gerçekleştirdiğinizden emin olun.

Karar vermenize yardımcı olması için [Kullanıcı akışlarını ve özel ilkeleri karşılaştıran](../../active-directory-b2c/user-flow-overview.md#comparing-user-flows-and-custom-policies) makaleye bakın.

## <a name="choose-multiple-idps"></a>Birden çok IDPs seçin

Facebook gibi bir [dış kimlik sağlayıcısı](../../active-directory-b2c/technical-overview.md#external-identity-providers) kullanırken, dış sağlayıcının kullanılamaz hale gelmesi durumunda bir geri dönüş planına sahip olduğunuzdan emin olun.

### <a name="how-to-set-up-multiple-idps"></a>Birden çok IDPs ayarlama

Dış kimlik sağlayıcısı kayıt işleminin bir parçası olarak, kullanıcının cep telefonu numarası veya e-posta adresi gibi doğrulanmış bir kimlik talebi ekleyin. Doğrulanan talepleri temel Azure AD B2C Dizin örneğine yürütün. Dış sağlayıcı kullanılamıyorsa, doğrulanmış kimlik talebine geri dönün ve telefon numarasına bir kimlik doğrulama yöntemi olarak geri dönün. Başka bir seçenek de kullanıcının oturum açmasını sağlamak için kullanıcıyı bir kerelik geçiş kodu göndermektir.

 [Alternatif kimlik doğrulama yolları oluşturmak](https://github.com/azure-ad-b2c/samples/tree/master/policies/idps-filter)için aşağıdaki adımları izleyin:

 1. Kaydolma ilkenizi, yerel hesap ve dış IDPs 'ye kaydolma izni verecek şekilde yapılandırın.

 2. Kullanıcıların oturum açtıklarında [Diğer kimliği hesabına](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies/tree/master/account-linking) bağlanmasına izin vermek için bir profil ilkesi yapılandırın.

 3. Bir kesinti sırasında kullanıcıların [Alternatif BIR ıDP 'ye geçiş](../../active-directory-b2c/customize-ui-with-html.md#configure-dynamic-custom-page-content-uri) yapmasına izin verin ve bunları bilgilendirin.

## <a name="availability-of-multi-factor-authentication"></a>Multi-Factor Authentication 'ın kullanılabilirliği

[Multi-Factor Authentication (MFA) için bir telefon hizmeti](../../active-directory-b2c/phone-authentication-user-flows.md)kullanırken, alternatif bir hizmet sağlayıcısını kabul ettiğinizden emin olun. Yerel Telco veya telefon hizmet sağlayıcısı, hizmetinde kesintiler yaşayabilir.

### <a name="how-to-choose-an-alternate-mfa"></a>Alternatif MFA seçme  

Azure AD B2C hizmeti, zaman tabanlı bir kerelik geçiş kodları (OTPs) sunmak için yerleşik bir telefon tabanlı MFA sağlayıcısı kullanır. Kullanıcının önceden kayıtlı telefon numarasına bir sesli çağrı ve kısa mesaj biçiminde olur. Aşağıdaki alternatif yöntemler çeşitli senaryolar için kullanılabilir:

**Kullanıcı akışlarını** kullanırken, esnekliği oluşturmak için iki yöntem vardır:

- **Kullanıcı akış yapılandırmasını değiştirme**: telefon tabanlı OTP teslimatındaki kesintiyi tespit ederek, OTP teslim yöntemini telefon tabanlı olarak e-posta tabanlı olarak değiştirin ve kullanıcı akışını yeniden dağıtın ve uygulamaları değiştirmeden bırakın.

![ekran görüntüsünde oturum açma kaydı gösterilir](media/resilient-end-user-experiences/create-sign-in.png)

- **Uygulamaları Değiştir**: kaydolma ve oturum açma gibi her bir kimlik görevinde, iki Kullanıcı akışı kümesi tanımlayın. İlk olarak, telefon tabanlı OTP 'yi ve ikincisi e-posta tabanlı OTP 'yi kullanacak şekilde yapılandırın. Telefon tabanlı OTP tesliminde bir kesinti algılandıktan sonra, ilk Kullanıcı kümesinden diğerine geçiş yapmak için uygulamaları değiştirin ve yeniden dağıtın, böylece Kullanıcı değişmeden bırakılır.  

**Özel ilkeler** kullanılırken, esnekliği oluşturmak için dört yöntem vardır. Aşağıdaki liste karmaşıklık sırasına göre ve güncelleştirilmiş ilkeleri yeniden dağıtmanız gerekir.

- **Telefon tabanlı OTP veya e-posta tabanlı OTP 'nin Kullanıcı seçimini etkinleştirin**: her iki seçeneği de kullanıcılara sunun ve kullanıcıların seçeneklerden birini kendi kendine seçmesini sağlar. İlkelerde veya uygulamalarda değişiklik yapmanız gerekmez.

- **Telefon tabanlı OTP ve e-posta tabanlı OTP arasında dinamik geçiş**: kaydolma sırasında hem telefon hem de e-posta bilgilerini toplayın. Telefon kesintiden e-posta tabanlı OTP teslimine kadar bir telefon kesintisi sırasında özel ilkeyi koşulsuz bir şekilde değiştirin. İlkelerde veya uygulamalarda değişiklik yapmanız gerekmez.

- **Bir Authenticator uygulaması kullanın**: bir [Authenticator uygulaması](https://github.com/azure-ad-b2c/samples/tree/master/policies/custom-mfa-totp)kullanmak için özel ilkeyi güncelleştirin. Normal MFA 'niz telefon tabanlı ya da e-posta tabanlı OTP ise, kimlik doğrulayıcı uygulamasını kullanmak üzere geçiş yapmak için özel ilkelerinizi yeniden dağıtın.

>[!Note]
>Kullanıcıların, kaydolma sırasında kimlik doğrulayıcı Uygulama tümleştirmesini yapılandırması gerekir.

- **Güvenlik sorularını kullan**: Yukarıdaki yöntemlerin hiçbiri uygulanabilir değilse, güvenlik sorularını bir yedekleme olarak uygulayın. Ekleme veya profil düzenleme sırasında kullanıcılar için güvenlik soruları ayarlayın ve yanıtları dizin dışında ayrı bir veritabanında saklayın. Bu yöntem, "sahip olduğunuz bir şey" (örneğin, telefon) için MFA gereksinimini karşılamıyor, ancak "bildiğiniz bir şeyi" sağlar.

## <a name="use-a-content-delivery-network"></a>İçerik teslim ağı kullanma

İçerik teslim ağları (CDNs), Özel Kullanıcı akışı Kullanıcı arabirimi depolaması için blob depolarından daha iyi performans ve daha ucuz bir işlemdir. Web sayfası içeriği, yüksek oranda kullanılabilir sunuculardan oluşan coğrafi olarak dağıtılmış bir ağdan daha hızlı dağıtılır.  

CDN 'nin kullanılabilirliğini ve uçtan uca senaryo ve yük testi aracılığıyla içerik dağıtımının performansını düzenli olarak test edin. Promosyon veya tatil trafiği nedeniyle yaklaşan bir aşırı yük için planlama yapıyorsanız, yük testi tahminlerinizi gözden geçirin.
  
## <a name="next-steps"></a>Sonraki adımlar

- [Azure AD B2C geliştiricileri için esnekliği kaynakları](resilience-b2c.md)
  
  - [Dış işlemlerle esnek arabirimler](resilient-external-processes.md)
  - [Geliştirici en iyi uygulamaları esnekliği](resilience-b2c-developer-best-practices.md)
  - [İzleme ve analiz aracılığıyla esnekliği](resilience-with-monitoring-alerting.md)
- [Kimlik doğrulama altyapınızda esnekliği oluşturma](resilience-in-infrastructure.md)
- [Uygulamalarınızda kimlik doğrulama ve yetkilendirme esnekliği artırın](resilience-app-development-overview.md)