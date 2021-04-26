---
title: Azure Active Directory için dış erişim için bir güvenlik planı oluşturun
description: Kuruluşunuzun kaynaklarına yönelik dış erişim için güvenliği planlayın.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 266142240ba9e892c905ac8aa6521da5a14c4c3d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102554029"
---
# <a name="3-create-a-security-plan-for-external-access"></a>3. dış erişim için bir güvenlik planı oluşturun 

Artık, [istediğiniz güvenlik sonrası güvenlik duruşunu dış erişim için belirlediğinize](1-secure-access-posture.md) ve [geçerli işbirliği durumunuzu](2-secure-access-current-state.md)buldığınıza göre, bir dış Kullanıcı güvenliği ve idare planı oluşturabilirsiniz. 

Bu plan şunları belgelemelidir:

* Erişim için gruplandırılması gereken uygulamalar ve diğer kaynaklar.

* Dış kullanıcılar için uygun oturum açma koşulları. Bunlar cihaz durumu, oturum açma konumu, istemci uygulama gereksinimleri ve Kullanıcı riski içerebilir.

* Erişimin ne zaman incelenmek ve kaldırılacağı konusunda iş ilkeleri. 

* Gruplandırılacak ve benzer şekilde değerlendirilecek Kullanıcı popülasyonu.

Bu bölgeler belgelendikten sonra, bu planı uygulamak için Microsoft veya diğer herhangi bir kimlik sağlayıcısından (IDP) kimlik ve erişim yönetimi ilkeleri kullanabilirsiniz.

## <a name="document-resources-to-be-grouped-for-access"></a>Erişim için gruplandırılacak belge kaynakları

Kaynakları erişim için gruplamak için birden çok yol vardır. 

* Microsoft ekipleri dosyaları, konuşma iş parçacıklarını ve diğer kaynakları tek bir yerde gruplandırır. Microsoft ekipleri için bir dış erişim stratejisi formüle ihtiyacınız vardır. Bkz. [takımlara, OneDrive 'a ve SharePoint 'e güvenli erişim](9-secure-access-teams-sharepoint.md).

* Yetkilendirme Yönetimi erişim paketleri, erişim sağlayabilmeniz için tek bir uygulama ve diğer kaynak paketi oluşturmanızı sağlar. 

* Koşullu erişim ilkeleri, aynı erişim gereksinimlerine sahip en fazla 250 uygulamasına uygulanabilir.

Ancak, erişimi yöneteceksiniz, hangi uygulamaların birlikte gruplanacağını belgelemelidir. Dikkat edilmesi gerekenler şunlardır:

* **Risk profili**. Hatalı aktör bir uygulamaya erişim kazandığında işletmeniz için risk nedir? Her uygulamayı yüksek, orta veya düşük riskli olarak kodlamayı göz önünde bulundurun. Yüksek riskli uygulamaları düşük riskli uygulamalarla gruplandırma konusunda dikkatli olun. 

   * Asla dış kullanıcılarla paylaşılmaması gereken uygulamaları belgeleyin.

* **Uyumluluk çerçeveleri**. Herhangi bir uyumluluk çerçevesi bir uygulamayla Buluşmalıdır? Erişim ve gözden geçirme gereksinimleri nelerdir?

* **Belirli iş rollerine veya departmanlara yönelik uygulamalar**. Belirli bir iş rolü veya departmandaki tüm kullanıcılar erişime ihtiyaç duyduğundan, gruplandırılacak uygulamalar var mı?

* **İşbirliği odaklı uygulamalar**. Dış kullanıcılara hangi işbirliği odaklı uygulamalar erişebiliyor? Microsoft ekipleri ve SharePoint 'in erişilebilir olması gerekebilir. Word ve Excel gibi Office 365 ' de bulunan üretkenlik uygulamaları için, dış kullanıcılar kendi lisanslarını getirsin veya bunları lisanslamak ve erişim sağlamak zorunda kalacak mı?

Dış kullanıcılar için erişilebilir hale getirmek istediğiniz her uygulama ve kaynak gruplandırması için aşağıdakileri belgeleyin:

* Grubun açıklayıcı bir adı, örneğin *High_Risk_External_Access_Finance*. 

* Gruptaki tüm uygulama ve kaynakların listesini doldurun.

* Uygulama ve kaynak sahipleri ve iletişim bilgileri.

* Erişimin BT tarafından kontrol edilip edilmeyeceğini veya iş sahibine atanmış olup olmadığı.

* Tüm Önkoşullar, örneğin, bir arka plan denetimini veya bir eğitimi tamamlamak, erişim için.

* Kaynaklara erişmek için herhangi bir uyumluluk gereksinimi.

* Örneğin, belirli kaynaklar için çok faktörlü kimlik doğrulaması gerektiren ek sorunlar.

* Erişimin ne sıklıkla incelendiğini, kim tarafından ve nerede açıklanacağını öğrenin.

Bu tür bir idare planı, ayrıca iç erişim için de tamamlanmalıdır.

## <a name="document-sign-in-conditions-for-external-users"></a>Dış kullanıcılar için belge oturum açma koşulları.

Planınızın bir parçası olarak, dış kullanıcılarınızın kaynaklara erişim için oturum açma gereksinimlerini belirlemelisiniz. Oturum açma gereksinimleri genellikle kaynakların risk profilini ve kullanıcıların oturum açma riskini değerlendirmesine bağlıdır.

Oturum açma koşulları, [Azure AD koşullu erişim](../conditional-access/overview.md) 'de yapılandırılır ve bir koşula ve sonuca göre yapılır. Örneğin, çok faktörlü kimlik doğrulaması gerekli olduğunda

**Kaynak risk tabanlı oturum açma koşulları.**

| Uygulama risk profili| Multi-Factor Authentication 'ı tetikleyerek bu ilkeleri göz önünde bulundurun |
| - |-|
| Düşük risk| Belirli uygulama kümeleri için MFA gerektir |
| Med riski| Diğer riskler mevcut olduğunda MFA gerektir |
| Yüksek riskli| Her zaman dış kullanıcılar için MFA gerektir |


Bugün, [KIRACıNıZDAKI B2B kullanıcıları için Multi-Factor Authentication 'ı zorunlu](../external-identities/b2b-tutorial-require-mfa.md)kılabilirsiniz. 

**Kullanıcı ve cihaz tabanlı oturum açma koşulları**.

| Kullanıcı veya oturum açma riski| Bu ilkeleri göz önünde bulundurun |
| - | - |
| Cihaz| Uyumlu cihaz gerektirme |
| Mobil uygulamalar| Onaylanan uygulamalar gerektir |
| Kimlik koruması yüksek risk gösterir| Kullanıcının parolayı değiştirmesini iste |
| Ağ konumu| Belirli bir IP adresi aralığından, son derece gizli projelere oturum açma gerektir |

Günümüzde, cihaz durumunu bir ilkeye giriş olarak kullanmak için cihazın kayıtlı olması veya kiracınıza katılması gerekir. 

[Kimlik koruması risk tabanlı ilkeler](../conditional-access/howto-conditional-access-policy-risk.md) kullanılabilir. Ancak, kullanıcıların giriş kiracısında sorunlar azaltılmalıdır.

[Ağ konumları](../conditional-access/howto-conditional-access-policy-location.md)için, sahip olduğunuz HERHANGI bir IP adresi aralığına erişimi kısıtlayabilirsiniz. Yalnızca dış iş ortaklarının, kuruluşunuzda site üzerinde olduklarında bir uygulamaya erişmesini istiyorsanız bunu kullanabilirsiniz.

[Koşullu erişim ilkeleri hakkında daha fazla bilgi edinin](../conditional-access/overview.md).

## <a name="document-access-review-policies"></a>Belge erişimi gözden geçirme ilkeleri

Kaynaklara erişimi gözden geçirmeniz gerektiğinde ve dış kullanıcılar için hesap erişimini kaldırmanız gerektiğinde iş ilkelerinizi belgeleyin. Bu kararlara yönelik girişler şunlar olabilir:

* Her türlü uyumluluk çerçevesi için ayrıntılı gereksinimler.

* Dahili iş ilkeleri ve süreçler

* Kullanıcı davranışı

İlkeleriniz gereksinimlerinize göre çok özelleştirilirken, aşağıdakileri göz önünde bulundurun:

* **Yetkilendirme Yönetimi erişim gözden geçirmeleri**. Yetkilendirme Yönetimi 'ndeki işlevleri kullanarak

   * [Erişim paketleri otomatik olarak sona erer](../governance/entitlement-management-access-package-lifecycle-policy.md)ve bu nedenle dış Kullanıcı dahil edilen kaynaklara erişebilir.

   * Erişim gözden geçirmeleri için [gerekli bir gözden geçirme sıklığı](../governance/entitlement-management-access-reviews-create.md) ayarlayın.

   * Tüm kullanıcıları tek bir iş ortağından gruplamak için [bağlı kuruluşlar](../governance/entitlement-management-organization.md) kullanıyorsanız, iş sahibi ve iş ortağı temsilcisiyle düzenli İncelemeleri zamanlayın.

* **Grupları Microsoft 365**. Dış kullanıcıların davet ettiği Microsoft 365 Gruplar için bir [Grup süre sonu ilkesi](/microsoft-365/solutions/microsoft-365-groups-expiration-policy) ayarlayın. 

* **Diğer seçenekler**. Dış kullanıcıların, yetkilendirme yönetim erişimi paketleri veya Microsoft 365 gruplarının dışında erişimi varsa, hesapların ne zaman etkin hale getirilmeli veya silinmeleri gerektiğini gözden geçirmek için iş sürecini ayarlayın. Örnek:

   * 90 gün boyunca oturum açmamış herhangi bir hesap için oturum açma yeteneğini kaldırın.

   * Erişim ihtiyaçlarını değerlendirin ve dış kullanıcılarla her projenin sonunda işlem yapın.

 

## <a name="determine-your-access-control-methods"></a>Erişim denetimi yöntemlerinizi belirleme

Ne erişimi denetlemek istediğinize, bu varlıkların ortak erişim için nasıl gruplanacağına ve gerekli oturum açma ve erişim gözden geçirme ilkelerine sahip olduğunuza göre, planınızı nasıl gerçekleştireceğinize karar verebilirsiniz. 

Örneğin, [Yetkilendirme Yönetimi](../governance/entitlement-management-overview.md)gibi bazı işlevler yalnızca bir Azure AD Premium 2 (P2) lisanslarıyla kullanılabilir. Microsoft 365 E5 ve Office 365 E5 lisansları Azure AD P2 lisanslarını içerir. 

Office 365 ve Azure AD Microsoft 365 'nin diğer birleşimleri, dış kullanıcıları yönetmeye yönelik bazı işlevleri de etkinleştirir. Daha fazla bilgi için bkz. [Information Protection](/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-tenantlevel-services-licensing-guidance/microsoft-365-security-compliance-licensing-guidance) .

> [!NOTE]
> Lisanslar Kullanıcı başına. Bu nedenle, tüm kullanıcılar için bu lisansları etkinleştirmeden Yöneticiler ve iş sahipleri için erişim denetimi, Azure AD P2 veya Microsoft 365 E5 düzeyinde dahil olmak üzere belirli kullanıcılara sahip olabilirsiniz. İlk 50.000 dış kullanıcınız ücretsizdir. Diğer iç kullanıcılarınız için P2 lisanslarını etkinleştirmezseniz, erişim paketleri gibi yetkilendirme yönetimi işlevlerini kullanamazlar. 


## <a name="govern-access-with-azure-ad-p2-and-microsoft--office-365-e5"></a>Azure AD P2 ve Microsoft/Office 365 E5 ile erişimi yönetir
Azure AD P2 ve Microsoft 365 E5, güvenlik ve idare araçlarının eksiksiz paketine sahiptir.

### <a name="provisioning-signing-in-reviewing-access-and-deprovisioning-bolded-entries-are-preferred-methods"></a>Sağlama, oturum açma, erişim gözden geçirme ve sağlamayı kaldırma. Kalın girdiler tercih edilen yöntemlerdir

| Özellik| Dış Kullanıcı sağlama| Oturum açma yeniden QS 'yi zorlayın.| Erişimi gözden geçirme| Erişim sağlamayı kaldırma |
| - | - | - | - | - |
| Azure AD B2B Işbirliği| E-posta, OTP, self servis aracılığıyla davet edin| | **İş ortağı başına Dönemsel gözden geçirme**| Hesabı kaldır<br>Oturum açmayı kısıtla |
| Yetkilendirme Yönetimi| **Atama veya self servis erişimi aracılığıyla Kullanıcı ekleme**| | Erişim gözden geçirmeleri|**Veya kaldırma, erişim paketi**|
| Office 365 Grupları| | | Grup üyeliklerini gözden geçirme| Grup sona erme veya silme<br> Kaldırma formu grubu |
| Azure AD güvenlik grupları| | **Koşullu erişim ilkeleri** (gerekli olduğu gibi, güvenlik gruplarına dış kullanıcılar ekleyin)| |  |



 ### <a name="access-to-resources-bolded-entries-are-preferred-methods"></a>Kaynaklara erişim. Kalın girdiler tercih edilen yöntemlerdir

|Özellik | Uygulama & kaynak erişimi| SharePoint & OneDrive erişimi| Takımlar erişimi| E-posta & belge güvenliği |
| - |-|-|-|-|
| Yetkilendirme Yönetimi| **Atama veya self servis erişimi aracılığıyla Kullanıcı ekleme**| **Erişim paketleri**| **Erişim paketleri**|  |
| Office 365 Grubu| | Gruba eklenen siteler (ve ilişkili içerik) erişimi| Gruba dahil olan takımlara (ve ilişkili içeriğe) erişim|  |
| Duyarlılık etiketleri| | **El ile ve erişimi otomatik olarak sınıflandırma ve kısıtlama**| **El ile ve erişimi otomatik olarak sınıflandırma ve kısıtlama**| **El ile ve erişimi otomatik olarak sınıflandırma ve kısıtlama** |
| Azure AD güvenlik grupları| **Erişim paketlerine dahil olan erişim için koşullu erişim ilkeleri**| | |  |


### <a name="entitlement-management"></a>Yetkilendirme Yönetimi 

[Yetkilendirme Yönetimi erişim paketleri](../governance/entitlement-management-access-package-create.md) , gruplara ve takımlara, uygulamalara ve SharePoint sitelerine erişimi sağlamayı ve sağlamayı sağlamayı etkinleştirir. Hangi bağlı kuruluşların erişimine izin verileceğini, self servis isteklerine izin verilip verilmeyeceğini ve erişim vermek için hangi onay iş akışlarının (varsa) gerekli olduğunu tanımlayabilirsiniz. Erişimin gerekenden daha uzun süre kalmemesini sağlamak için, her bir erişim paketine yönelik süre sonu ilkelerini ve erişim incelemelerini tanımlayabilirsiniz. 

 

## <a name="govern-access-with-azure-ad-p1-and-microsoft--office-365-e3"></a>Azure AD P1 ve Microsoft/Office 365 E3 ile erişimi yönetir
Azure AD P1 ve Microsoft 365 E3 ile güçlü idare elde edebilirsiniz

### <a name="provisioning-signing-in-reviewing-access-and-deprovisioning"></a>Sağlama, oturum açma, erişim gözden geçirme ve sağlamayı kaldırma


|Özellik | Dış Kullanıcı sağlama| Oturum açma gereksinimlerini zorla| Erişimi gözden geçirme| Erişim sağlamayı kaldırma |
| - |-|-|-|-|
| Azure AD B2B Işbirliği| **E-posta, OTP, self servis aracılığıyla davet edin**| Doğrudan B2B Federasyonu| **İş ortağı başına Dönemsel gözden geçirme**| Hesabı kaldır<br>Oturum açmayı kısıtla |
| Microsoft veya Office 365 grupları| | | | Grup sonu veya silme.<br>Gruptan kaldırma. |
| Güvenlik grupları| | **Dış kullanıcıları güvenlik gruplarına ekleme (kuruluş, takım, proje vb.)**| |  |
| Koşullu Erişim ilkeleri| | **Dış kullanıcılar için oturum açma koşullu erişim ilkeleri**| |  |


 ### <a name="access-to-resources"></a>Kaynaklara erişim.

|Özellik | Uygulama & kaynak erişimi| SharePoint & OneDrive erişimi| Takımlar erişimi| E-posta & belge güvenliği |
| - |-|-|-|-|
| Microsoft veya Office 365 grupları| | **Gruba (ve ilişkili içeriğe) dahil edilen site (ler) e erişim**|**Microsoft 365 grubuna (ve ilişkili içeriğe) dahil olan takımlara erişim**|  |
| Duyarlılık etiketleri| | Erişimi el ile sınıflandırma ve kısıtlama| Erişimi el ile sınıflandırın ve kısıtlayın.| Kısıtlamak ve şifrelemek için el ile sınıflandır |
| Koşullu erişim Ilkeleri| Erişim denetimi için koşullu erişim ilkeleri| | |  |
| Ek Yöntemler| | SharePoint site erişimini güvenlik gruplarıyla sınırlandırın.<br>Doğrudan paylaşıma izin vermeyin.| **Ekipler içinden dış davetleri kısıtlama**|  |


### <a name="next-steps"></a>Sonraki adımlar

Kaynaklara dışarıdan erişimi güvenli hale getirmek için aşağıdaki makalelere bakın. Eylemleri listelenen sırayla yapmanızı öneririz.

1. [Dış erişim için güvenlik durunuzu belirleme](1-secure-access-posture.md)

2. [Geçerli durumlarınızı bulma](2-secure-access-current-state.md)

3. [Bir idare planı oluşturun](3-secure-access-plan.md) (burada bulabilirsiniz.)

4. [Güvenlik için grupları kullanma](4-secure-access-groups.md)

5. [Azure AD B2B 'ye geçiş](5-secure-access-b2b.md)

6. [Yetkilendirme yönetimiyle güvenli erişim](6-secure-access-entitlement-managment.md)

7. [Koşullu erişim ilkeleriyle güvenli erişim](7-secure-access-conditional-access.md)

8. [Duyarlılık etiketleriyle güvenli erişim](8-secure-access-sensitivity-labels.md)

9. [Microsoft ekipleri, OneDrive ve SharePoint 'e güvenli erişim](9-secure-access-teams-sharepoint.md)