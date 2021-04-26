---
title: MFA için Azure Güvenlik Merkezi 'nin güvenlik önerileri
description: Azure Güvenlik Merkezi 'ni kullanarak Azure abonelikleriniz için Multi-Factor Authentication 'ı nasıl zorlayacağınızı öğrenin
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/10/2021
ms.author: memildin
ms.openlocfilehash: 9af4f225b1b9ca5e8023a8d5b4bb7607762e4447
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102631906"
---
# <a name="manage-multi-factor-authentication-mfa-enforcement-on-your-subscriptions"></a>Aboneliklerinizde Multi-Factor Authentication (MFA) zorlamasını yönetme

Yalnızca kullanıcılarınızın kimliğini doğrulamak için parola kullanıyorsanız, bir saldırı vektörü açık bırakılır. Kullanıcılar genellikle zayıf parolalar kullanır veya birden fazla hizmet için bunları yeniden kullanır. [MFA](https://www.microsoft.com/security/business/identity/mfa) etkinken hesaplarınız daha güvenlidir ve kullanıcılar, tek oturum açma (SSO) ile neredeyse her uygulama için kimlik doğrulaması yapabilir.

Kuruluşunuzun sahip olduğu lisanslara göre Azure Active Directory (AD) kullanıcılarınız için MFA 'yı etkinleştirmenin birden çok yolu vardır. Bu sayfa, Azure Güvenlik Merkezi bağlamında her biri için ayrıntılar sağlar.


## <a name="mfa-and-security-center"></a>MFA ve Güvenlik Merkezi 

Güvenlik Merkezi MFA 'ya yüksek bir değer koyar. Güvenli puanınız için en çok katkıda bulunan güvenlik denetimi MFA 'yı **etkinleştirir**. 

MFA 'yı etkinleştirme denetimindeki öneriler, aboneliklerinizin kullanıcıları için önerilen uygulamaları karşıladığınızı sağlar:

- MFA, aboneliğinizde sahip izinleri olan hesaplarda etkinleştirilmelidir
- Aboneliğinizdeki yazma izinleri olan hesaplarda MFA etkinleştirilmelidir

MFA 'yı etkinleştirmenin ve Güvenlik Merkezi 'ndeki iki öneriyle uyumlu olmak için üç yol vardır: güvenlik Varsayılanları, Kullanıcı başına atama, koşullu erişim (CA) ilkesi. Bu seçeneklerin her biri aşağıda açıklanmıştır.

### <a name="free-option---security-defaults"></a>Ücretsiz seçenek-güvenlik Varsayılanları
Azure AD 'nin ücretsiz sürümünü kullanıyorsanız, kiracınızda Multi-Factor Authentication 'ı etkinleştirmek için [güvenlik varsayılanlarını](../active-directory/fundamentals/concept-fundamentals-security-defaults.md) kullanın.

### <a name="mfa-for-microsoft-365-business-e3-or-e5-customers"></a>Microsoft 365 İş, E3 veya E5 müşterileri için MFA
Microsoft 365 olan müşteriler **Kullanıcı başına atama** kullanabilir. Bu senaryoda, tüm oturum açma olayları için Azure AD MFA etkinleştirilmiş veya devre dışı bırakılmış olabilir. Kullanıcıların bir alt kümesi için veya bazı senaryolarda çok faktörlü kimlik doğrulamasını etkinleştirebilme özelliği yoktur ve yönetim, Office 365 portalından yapılır.

### <a name="mfa-for-azure-ad-premium-customers"></a>Azure AD Premium müşterileri için MFA
Gelişmiş bir kullanıcı deneyimi için, **koşullu erişim (CA) ilkesi** seçenekleri Için Azure AD Premium P1 veya P2 sürümüne yükseltin. CA ilkesini yapılandırmak için, [Azure Active Directory (ad) kiracı izinlerine](../active-directory/roles/permissions-reference.md)sahip olmanız gerekir.

CA ilkeniz şunları sağlamalıdır:
- MFA zorla
- Microsoft Azure Management App ID (797f4846-ba00-4fd7-ba43-dac1f8f63013) veya tüm uygulamaları ekleyin
- Microsoft Azure yönetimi uygulama KIMLIĞINI dışlayamazsınız

**Azure AD Premium P1** müşterileri, belirli senaryolar veya olaylar sırasında iş gereksinimlerinize uyacak şekilde kullanıcılara çok faktörlü kimlik doğrulaması yapmak IÇIN Azure AD ca 'yı kullanabilir. Bu işlevi içeren diğer lisanslar: Enterprise Mobility + Security E3, Microsoft 365 F1 ve Microsoft 365 E3.

**Azure AD Premium P2** , en güçlü güvenlik özelliklerini ve gelişmiş bir kullanıcı deneyimini sağlar. Bu lisans Azure AD Premium P1 özelliklerine [risk tabanlı koşullu erişim](../active-directory/conditional-access/howto-conditional-access-policy-risk.md) ekler. Risk tabanlı CA, kullanıcılarınızın desenlerine uyum sağlar ve çok faktörlü kimlik doğrulama istemlerini en aza indirir. Bu işlevi içeren diğer lisanslar: Enterprise Mobility + Security E5 veya Microsoft 365 E5.

[Azure koşullu erişim belgelerinde](../active-directory/conditional-access/overview.md)daha fazla bilgi edinin.

## <a name="identify-accounts-without-multi-factor-authentication-mfa-enabled"></a>Multi-Factor Authentication (MFA) etkin olmayan hesapları tanımla

MFA etkin olmadan Kullanıcı hesaplarının listesini, güvenlik merkezi öneri ayrıntıları sayfasından veya Azure Kaynak Grafiği ' ni kullanarak görüntüleyebilirsiniz.

### <a name="view-the-accounts-without-mfa-enabled-in-the-azure-portal"></a>Azure portal MFA etkin olmayan hesapları görüntüleme
Öneri ayrıntıları sayfasında, **sağlıksız kaynaklar** listesinden bir abonelik seçin veya **eylem al** ' ı seçin ve liste görüntülenir.

### <a name="view-the-accounts-without-mfa-enabled-using-azure-resource-graph"></a>Azure Kaynak Grafiği 'ni kullanarak MFA etkin olmayan hesapları görüntüleme
Hangi hesapların MFA 'nın etkin olduğunu görmek için aşağıdaki Azure Kaynak Grafiği sorgusunu kullanın. Sorgu, tüm sağlıksız kaynakları döndürür-önerinin tüm uygun olmayan kaynakları-hesaplar, aboneliğiniz üzerinde sahip izinleri olan hesaplarda MFA etkinleştirilmelidir. 

1. **Azure Kaynak Grafiği Gezginini** açın.

    :::image type="content" source="./media/security-center-identity-access/opening-resource-graph-explorer.png" alt-text="Azure Kaynak Grafiği Gezgini * * öneri sayfası başlatılıyor" :::

1. Aşağıdaki sorguyu girin ve **Sorguyu Çalıştır**' ı seçin.

    ```kusto
    securityresources
     | where type == "microsoft.security/assessments"
     | where properties.displayName == "MFA should be enabled on accounts with owner permissions on your subscription"
     | where properties.status.code == "Unhealthy"
    ```

1. `additionalData`Özelliği, MFA 'ya sahip olmayan hesapların Hesap nesne kimliklerinin listesini ortaya koyar. 

    > [!NOTE]
    > Hesaplar hesap sahiplerinin gizliliğini korumak için hesap adları yerine nesne kimlikleri olarak gösterilir.

> [!TIP]
> Alternatif olarak, güvenlik merkezi 'nin REST API yöntemi [değerlendirmesi-al](/rest/api/securitycenter/assessments/get)kullanabilirsiniz.


## <a name="faq---mfa-in-security-center"></a>SSS-Güvenlik Merkezi 'nde MFA

- [MFA 'yı zorlamak için zaten CA ilkesi kullanıyoruz. Neden hala güvenlik merkezi önerilerini alırız?](#were-already-using-ca-policy-to-enforce-mfa-why-do-we-still-get-the-security-center-recommendations)
- [MFA 'yı zorlamak için bir üçüncü taraf MFA aracı kullanıyoruz. Neden hala güvenlik merkezi önerilerini alırız?](#were-using-a-third-party-mfa-tool-to-enforce-mfa-why-do-we-still-get-the-security-center-recommendations)
- [Güvenlik Merkezi, abonelikte "MFA gerektirsin" olarak izin verilmeden Kullanıcı hesaplarını gösterir mi?](#why-does-security-center-show-user-accounts-without-permissions-on-the-subscription-as-requiring-mfa)
- [PıM ile MFA 'yı zorlıyoruz. PıM hesapları neden uyumsuz olarak gösteriliyor?](#were-enforcing-mfa-with-pim-why-are-pim-accounts-shown-as-noncompliant)
- [Hesapların bazılarını muaf edebilir veya kapatabilir miyim?](#can-i-exempt-or-dismiss-some-of-the-accounts)
- [Güvenlik Merkezi 'nin kimlik ve erişim korumalarının kısıtlamaları var mı?](#are-there-any-limitations-to-security-centers-identity-and-access-protections)

### <a name="were-already-using-ca-policy-to-enforce-mfa-why-do-we-still-get-the-security-center-recommendations"></a>MFA 'yı zorlamak için zaten CA ilkesi kullanıyoruz. Neden hala güvenlik merkezi önerilerini alırız?
Önerilerin neden hala oluşturulduğunu araştırmak için, MFA CA ilkenizde aşağıdaki yapılandırma seçeneklerini doğrulayın:

- Hesapları MFA CA ilkenizin **kullanıcıları** bölümüne (veya **gruplar** bölümündeki gruplardan birini) dahil edersiniz
- Azure yönetim uygulaması KIMLIĞI (797f4846-ba00-4fd7-ba43-dac1f8f63013) veya tüm uygulamalar, MFA CA ilkenizin **uygulamalar** bölümünde bulunur
- Azure yönetim uygulaması KIMLIĞI, MFA CA ilkenizin **uygulamalar** bölümünde dışlanmıyor

### <a name="were-using-a-third-party-mfa-tool-to-enforce-mfa-why-do-we-still-get-the-security-center-recommendations"></a>MFA 'yı zorlamak için bir üçüncü taraf MFA aracı kullanıyoruz. Neden hala güvenlik merkezi önerilerini alırız?
Güvenlik Merkezi 'nin MFA önerileri, üçüncü taraf MFA araçlarını desteklemez (örneğin, DUO).

Önerileriniz kuruluşunuz için önemli değilse, [güvenli puanınızdan kaynakları ve önerileri muaf tutmak](exempt-resource.md)konusunda açıklandığı gibi bunları "hafiflemeyen" olarak işaretlemeyi göz önünde bulundurun. Ayrıca [bir öneriyi devre dışı](tutorial-security-policy.md#disable-security-policies-and-disable-recommendations)bırakabilirsiniz.

### <a name="why-does-security-center-show-user-accounts-without-permissions-on-the-subscription-as-requiring-mfa"></a>Güvenlik Merkezi, abonelikte "MFA gerektirsin" olarak izin verilmeden Kullanıcı hesaplarını gösterir mi?
Güvenlik Merkezi 'nin MFA önerileri, [Azure RBAC](../role-based-access-control/role-definitions-list.md) rollerine ve [Klasik Azure abonelik yöneticileri](../role-based-access-control/classic-administrators.md) rolüne başvurur. Hesaplardan hiçbirinin böyle rolleri olmadığını doğrulayın.

### <a name="were-enforcing-mfa-with-pim-why-are-pim-accounts-shown-as-noncompliant"></a>PıM ile MFA 'yı zorlıyoruz. PıM hesapları neden uyumsuz olarak gösteriliyor?
Güvenlik Merkezi 'nin MFA önerileri şu anda PıM hesaplarını desteklemiyor. Bu hesapları Kullanıcılar/Gruplar bölümünde CA İlkesine ekleyebilirsiniz.

### <a name="can-i-exempt-or-dismiss-some-of-the-accounts"></a>Hesapların bazılarını muaf edebilir veya kapatabilir miyim?
MFA kullanmayan bazı hesapların muaf tutulması özelliği şu anda desteklenmemektedir.  

### <a name="are-there-any-limitations-to-security-centers-identity-and-access-protections"></a>Güvenlik Merkezi 'nin kimlik ve erişim korumalarının kısıtlamaları var mı?
Güvenlik Merkezi 'nin kimlik ve erişim korumalarının bazı sınırlamaları vardır:

- Kimlik önerileri 600 taneden fazla hesabı olan abonelikler için kullanılamaz. Bu gibi durumlarda, bu öneriler "kullanılamayan değerlendirmeler" altında listelenecektir.
- Bulut çözümü sağlayıcısı (CSP) ortağının yönetim aracılarında kimlik önerileri kullanılamaz.
- Kimlik önerileri, ayrıcalıklı kimlik yönetimi (PıM) sistemi ile yönetilen hesapları tanımlamaz. PıM aracı kullanıyorsanız, **erişimi yönet ve izinler** denetiminde yanlış sonuçlar görebilirsiniz.


## <a name="next-steps"></a>Sonraki adımlar
Diğer Azure Kaynak türleri için uygulanan öneriler hakkında daha fazla bilgi edinmek için aşağıdaki makaleye bakın:

- [Azure Güvenlik Merkezi'nde ağınızı koruma](security-center-network-recommendations.md)