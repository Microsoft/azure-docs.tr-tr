---
title: Azure Active Directory B2B Işbirliğiyle yönetilen işbirliğine geçiş
description: Azure AD B2B işbirliği ile yönetilen işbirliğine geçiş yapın.
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
ms.openlocfilehash: 4206ba7617032e34310682d1468e6b1b661b8c8a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101648596"
---
# <a name="transition-to-governed-collaboration-with-azure-active-directory-b2b-collaboration"></a>Azure Active Directory B2B işbirliğiyle yönetilen işbirliğine geçiş 

Bilgilerinizi denetim altına almak, kaynaklarınıza dışarıdan erişimin güvenliğini sağlamaya yönelik bir anahtardır. Bu makaleyle devam etmeden önce, şunları yaptığınızdan emin olun:

* [Güvenlik duruşunuzu tespit edin](1-secure-access-posture.md)

* [Geçerli durumlarınızı keşfetti](2-secure-access-current-state.md)

* [Bir güvenlik planı oluşturuldu](3-secure-access-plan.md)

* [Grupların ve güvenliğin birlikte nasıl çalıştığını anladım](4-secure-access-groups.md)

Bu şeyleri tamamladıktan sonra, denetimli işbirliği içine taşımaya hazırsınız demektir. Bu makale, tüm dış işbirliklerinizi [Azure ACTIVE DIRECTORY B2B işbirliğiyle](../external-identities/what-is-b2b.md) (Azure AD B2B) taşımanıza kılavuzluk eder. Azure AD B2B, [Azure AD dış kimliklerinin](../external-identities/compare-with-b2c.md)bir özelliğidir.

## <a name="control-who-your-organization-collaborates-with"></a>Kuruluşunuzun hangi kişilerle işbirliği olduğunu denetleme

Kullanıcılarınızın hangi kuruluşların işbirliği yapabileceğine ve kuruluşunuzun içinden kimlerin işbirliği başlatabileceğini kısıtlama konusunda karar vermelisiniz. Çoğu kuruluş, iş birimlerinin kim tarafından işbirliği yapmaya karar vermesini ve gerektiğinde onay ve daha fazla bakış sağlamak için izin verme yaklaşımını ele alır. Örneğin, bazı kamu, eğitim ve finans hizmetleri kuruluşları açık işbirliğine izin vermez. Bu bölümün geri kalanında anlatıldığı gibi işbirliği kapsamındaki Azure AD özelliklerini kullanmak isteyebilirsiniz.

### <a name="determine-collaboration-partners"></a>İşbirliği iş ortaklarını belirleme

İlk olarak, şu anda işbirliği yapmış olduğunuz kuruluşları ve bu kuruluşların kullanıcılarına ait etki alanlarını belgelendiğinizden emin olun. Bir işbirliği ortağının birden çok etki alanı olabilir. Örneğin, bir iş ortağı ayrı etki alanlarına sahip birden fazla iş birimine sahip olabilir.

Daha sonra, ile gelecekte işbirliği yapmak istediğinizi belirleme 

* herhangi bir etki alanı (en kapsamlı)

* açıkça Engellenenler hariç tüm etki alanları 

* yalnızca belirli etki alanları (en kısıtlayıcı)

> [!NOTE]
> İşbirliği ayarlarınızın daha kısıtlayıcı olması, kullanıcılarınızın onaylanan işbirliği çerçevesinden daha fazla gidecekleri bir olasılıktır. Güvenlik gereksinimlerinizin çok fazla kısıtlayıcı olması yerine bu işbirliğini daha iyi gözden geçirmelerini ve en büyük işbirliği yapmanızı öneririz. 

Ayrıca, tek bir etki alanıyla sınırlandırmaya, kullanıcıları için diğer ilişkisiz etki alanlarına sahip olan kuruluşların yetkili işbirliğini yanlışlıkla engelleyebileceğini unutmayın. Örneğin, bir kuruluş ile iş yapıyorsanız, contoso ile ilk iletişim noktası, ". com" etki alanı içeren bir e-postaya sahip olan ABD tabanlı çalışanların bir üyesi olabilir. Ancak ". com" etki alanına izin verirseniz, ". ca" etki alanı olan Kanada çalışanlarını yanlışlıkla atlayabilirsiniz. 

Yalnızca belirli işbirliği iş ortaklarına izin vermek isteyebileceğiniz durumlar vardır. Örneğin, bir üniversite sistemi yalnızca kendi Fakülteler 'nin bir kaynak kiracıya erişimine izin vermek isteyebilir. Ya da bir yarışmanın, gerekli bir çatı ile uyumluluk elde etmek için yalnızca belirli alt kuruluşların birbirleriyle işbirliği yapmasına izin vermek isteyebilirsiniz.

#### <a name="using-allow-and-deny-lists"></a>İzin verme ve reddetme listelerini kullanma

Belirli kuruluşlardan [B2B kullanıcılarına yönelik davetleri kısıtlamak](../external-identities/allow-deny-list.md) için bir izin verilenler listesi veya reddetme listesi kullanabilirsiniz. Yalnızca bir izin verme veya reddetme listesi kullanabilirsiniz, her ikisini birden kullanamazsınız.

* Bir [izin verilenler listesi](../external-identities/allow-deny-list.md) , işbirliğini yalnızca listelenen etki alanlarıyla sınırlandırır. diğer tüm etki alanları Reddedilenler listesinde etkin olarak bulunur.

* [Reddetme](../external-identities/allow-deny-list.md) listesi, reddetme listesinde olmayan herhangi bir etki alanıyla işbirliği yapılmasına izin verir.

> [!IMPORTANT]
> Bu listeler, dizininizde zaten olan kullanıcılar için geçerlidir. Bunlar Ayrıca OneDrive Iş için ve SharePoint 'i reddetme listelerine izin vermez.

Bazı kuruluşlar, Reddedilenler Listesi için kendi yönetilen güvenlik sağlayıcıları tarafından sağlanmış olan bilinen ' hatalı aktör ' etki alanlarının listesini kullanır. Örneğin, kuruluş yasal olarak contoso ile iş yapıyor ve bir. com etki alanı kullanıyorsa, contoso. org etki alanını kullanan ve contoso çalışanlarının taklit edilmeye yönelik bir sızdırma saldırısı gerçekleştirmeye çalışan ilişkisiz bir kuruluş olabilir. 

## <a name="control-how-external-users-gain-access"></a>Dış kullanıcıların nasıl erişim kazanmasına ilişkin denetim

Azure AD B2B kullanarak dış iş ortaklarıyla işbirliği yapmak için birçok yol vardır. İşbirliğine başlamak için iş ortağınızın kaynaklarınıza erişmesi için davet edersiniz veya bunu başka bir şekilde etkinleştirebilirsiniz. Kullanıcılara yanıt vererek erişim elde edilebilir:

* Bir [e-posta ile gönderilen davetiyeyi](../external-identities/redemption-experience.md)veya bir kaynağı [paylaşmak için doğrudan bağlantıyı](../external-identities/redemption-experience.md) benimseme.

* Oluşturduğunuz [bir uygulama üzerinden](../external-identities/self-service-sign-up-overview.md) erişim isteme

* [Erişim](../governance/entitlement-management-request-access.md) portalı 'nı kullanarak erişim isteme

Azure AD B2B 'yi etkinleştirdiğinizde, varsayılan olarak doğrudan bağlantılar ve e-posta davetleri aracılığıyla konuk kullanıcıları davet etme imkanını etkinleştirirsiniz. E-posta OTP ve bir Self Servis Portalı aracılığıyla davetiye Şu anda önizleme aşamasındadır ve dış kimlikler içinde etkinleştirilmesi gerekir | Azure AD portalındaki dış işbirliği ayarları.

### <a name="control-who-can-invite-guest-users"></a>Konuk kullanıcıları davet edebilen kişileri denetleme

Konuk kullanıcıları kaynaklara erişmek için kimler davet edebilen kişileri belirleme.

* En kısıtlayıcı ayar, yalnızca yöneticilerin ve bu kullanıcıların konuk davet eden [rolüne](../external-identities/delegate-invitations.md) konukları davet etmesini sağlamak içindir.

* Güvenlik gereksinimleriniz buna izin verirseniz, userType üyesi olan tüm kullanıcıların konukları davet etmesini öneririz.

* Kullanıcıların, Azure AD B2B kullanıcıları için varsayılan hesap türü olan ve diğer konukları davet edebilmeleri için, Konuk türünde kullanıcısız kullanıcılara mi istediğinizi saptayın. 

![Konuk davet ayarlarının ekran görüntüsü.](media/secure-external-access/5-guest-invite-settings.png)

 

### <a name="collect-additional-information-about-external-users"></a>Dış kullanıcılar hakkında ek bilgiler toplama

Azure AD Yetkilendirme Yönetimi kullanıyorsanız, dış kullanıcıların yanıt vermesi için soruları yapılandırabilirsiniz. Daha sonra bu sorular bir karar vermek için onaylayanlara gösterilir. Her bir [erişim paketi ilkesi](../governance/entitlement-management-access-package-approval-policy.md) için farklı soru kümelerini, onaylayanlara onaylarımız erişim için ilgili bilgileri kullanabilmesi için yapılandırabilirsiniz. Örneğin, bir erişim paketinin satıcı erişimine yönelik olması amaçlanıyorsa, istek sahibine satıcı sözleşmesi numarası istenebilir. Tedarikçilere yönelik olarak tasarlanmış farklı bir erişim paketi, kaynak ülkesini isteyebilir.

Self Servis Portalı kullanıyorsanız, kullanıcıların kaydoldukları kullanıcılar hakkında ek öznitelikler toplamak için [API bağlayıcılarını](../external-identities/api-connectors-overview.md) kullanabilirsiniz. Daha sonra bu öznitelikleri, erişim atamak için kullanabilirsiniz. Örneğin, kaydolma işlemi sırasında kendi üretici KIMLIKLERINI topladığınızda, bu özniteliği, söz konusu tedarikçinin bir gruba veya erişim paketine dinamik olarak atamak için kullanabilirsiniz. Azure portal özel öznitelikler oluşturabilir ve bunları self servis kaydolma Kullanıcı akışlarınızda kullanabilirsiniz. Ayrıca, [MICROSOFT Graph API](../../active-directory-b2c/microsoft-graph-operations.md)'sini kullanarak bu öznitelikleri okuyabilir ve yazabilirsiniz. 

### <a name="troubleshoot-invitation-redemption-to-azure-ad-users"></a>Azure AD kullanıcılarına yönelik davet yeniden satın alma sorunlarını giderme

Konuk kullanıcıların Azure AD kullanarak bir işbirliği iş ortağından davet edilmesi, bir daveti benimseyecektir.

* Bir izin verilenler listesi kullanıyorsanız ve kullanıcının etki alanı bir izin verilenler listesine dahil edilmemişse.

* İşbirliği ortağının ana kiracısında, dış kullanıcılarla işbirliğini önleyen kiracı kısıtlamaları varsa.

* Kullanıcı ortağın Azure AD kiracısının bir parçası değilse. Örneğin, contoso.com 'de yalnızca Active Directory (ya da şirket içi IDP) olan kullanıcılar varsa, yalnızca e-posta OTP işlemi aracılığıyla davetleri kullanabileceksiniz. daha fazla bilgi için bkz. [davet kullanım akışı](../external-identities/redemption-experience.md).

## <a name="control-what-external-users-can-access"></a>Dış kullanıcıların ne erişebileceğini denetleme

Çoğu kuruluş tek parçalı değildir. Diğer bir deyişle, dış kullanıcılarla paylaşılması gereken bazı kaynaklar vardır ve bazıları dış kullanıcıların erişmesini istemezsiniz. Bu nedenle, hangi dış kullanıcıların erişebileceğini kontrol etmeniz gerekir. Belirli kaynaklara [erişimi denetlemek Için Yetkilendirme Yönetimi ve erişim paketleri](6-secure-access-entitlement-managment.md) kullanmayı göz önünde bulundurun.

Varsayılan olarak, Konuk kullanıcılar, kiracı üyeleri ve grup üyelikleri dahil diğer iş ortakları hakkındaki bilgileri ve öznitelikleri görebilirler. Bu bilgilere dış Kullanıcı erişimini kısıtlamak için güvenlik gereksinimlerinizin çağrılacağını göz önünde bulundurun.

![Dış işbirliği ayarlarını yapılandırma ekran görüntüsü.](media/secure-external-access/5-external-collaboration-settings.png)

Konuk kullanıcılar için aşağıdaki kısıtlamaları öneririz.

* **Dizinlere gözatma gruplarına ve diğer özelliklere konuk erişimini sınırlayın**

   * Konuk, üye olmadıkları grupları okuyabilme yeteneğini kısıtlamak için dış işbirliği ayarlarını kullanın.

* **Yalnızca çalışan uygulamalara erişimi engelleyin**.

   * Yalnızca Konuk olmayan kullanıcılar için uygun olan Azure AD ile tümleşik uygulamalara erişimi engellemek için bir koşullu erişim ilkesi oluşturun. 

* **Azure Portal erişimini engelleyin. Nadir olarak gereken özel durumları** yapabilirsiniz. 

   * Tüm konuk ve dış kullanıcıları içeren bir koşullu erişim ilkesi oluşturun ve [erişimi engellemek için bir ilke uygulayın](../../role-based-access-control/conditional-access-azure-management.md).

 

## <a name="remove-users-who-no-longer-need-access"></a>Artık erişmesi gerekmeyen kullanıcıları kaldırma

[Artık erişmesi gerekmeyen kullanıcıları gözden geçirebilmek ve kaldırabilmeniz](../governance/access-reviews-external-users.md)için geçerli erişimi değerlendirin. Kiracınızdaki dış kullanıcıları Konuk olarak ve üye hesaplarıyla dahil edin. 

Bazı kuruluşlar satıcılar, iş ortakları ve yükleniciler gibi harici kullanıcıları üye olarak eklemiştir. Bu üyelerin belirli bir özniteliği veya ile başlayan Kullanıcı adları olabilir, örneğin

* v-satıcılar için

* p-iş ortakları için

* c-yükleniciler için

Hala erişim gerekip gerekmediğini öğrenmek için üye hesapları olan tüm dış kullanıcıları değerlendirin. Bu durumda, sonraki bölümde açıklandığı gibi bu kullanıcıların Azure AD B2B 'ye geçişini yapın.

Ayrıca, Yetkilendirme Yönetimi veya Azure AD B2B aracılığıyla davet edilmeyen Konuk kullanıcılarınıza de sahip olabilirsiniz

Bu kullanıcıları bulmak için şunları yapabilirsiniz:

* [Yetkilendirme Yönetimi aracılığıyla davet edilen konuk kullanıcıları bulun](../governance/access-reviews-external-users.md).

   * [Örnek bir PowerShell betiği](https://github.com/microsoft/access-reviews-samples/tree/master/ExternalIdentityUse) sağlıyoruz.

Bu kullanıcılar aşağıdaki bölümde açıklandığı gibi Azure AD B2B kullanıcılarına geçiş yapın.

## <a name="transition-your-current-external-users-to-b2b"></a>Geçerli dış kullanıcılarınızı B2B 'ya geçirme

Azure AD B2B kullanmıyorsanız, büyük olasılıkla kiracınızdaki çalışan olmayan kullanıcılarınız vardır. Bu hesapları Azure AD B2B dış kullanıcı hesaplarına geçirmenize sonra UserType değerlerini Konuk olarak değiştirmeniz önerilir. Bu, Azure AD ve Microsoft 365 birçok yönden yararlanmanızı sağlar ve dış kullanıcıları farklı şekilde değerlendirmenize olanak tanır. Bu yolların bazıları şunlardır:

* Koşullu erişim ilkelerine konuk kullanıcıların kolayca dahil edilmesi veya dışlanması

* Erişim paketlerinde ve erişim Incelemeleriyle konuk kullanıcıların kolayca dahil edilmesi veya dışlanması

* Takımlara, SharePoint 'e ve diğer kaynaklara yönelik dış erişimi kolayca dahil etme veya hariç tutma.

Bu iç kullanıcıları, geçerli erişimini, UPN 'sini ve grup üyeliklerini koruyarak geçiş yapmak için bkz. [Harici KULLANıCıLARı B2B işbirliğiyle davet etme](../external-identities/invite-internal-users.md).

## <a name="decommission-undesired-collaboration-methods"></a>İstenmeyen işbirliği yöntemlerinin yetkisini alma

Yönetilen işbirliğine geçişinizi tamamlayabilmeniz için, istenmeyen işbirliği yöntemlerinin yetkisini almalısınız. Yetkisini alma işlemi, işbirliğini ve güvenlik duruşunuzu kullanmayı istediğiniz denetim derecesini temel alır. Son Kullanıcı denetimiyle ilgili bilgi edinmek için bkz. [dış erişim için güvenlik durunuzu belirleme](1-secure-access-posture.md).

Değerlendirmek isteyebileceğiniz işbirliği Araçlar aşağıda verilmiştir.

### <a name="direct-invitation-through-microsoft-teams"></a>Microsoft ekipleri aracılığıyla doğrudan davet

Varsayılan olarak takımlar dış erişime izin verir, bu da kuruluşun tüm dış etki alanlarıyla iletişim kurabildiği anlamına gelir. Yalnızca takımlar için belirli etki alanlarını kısıtlamak veya izin vermek istiyorsanız, bunu [takımlar yönetici portalında](https://admin.teams.microsoft.com/company-wide-settings/external-communications)yapabilirsiniz. 


### <a name="direct-sharing-through-sharepoint-and-onedrive"></a>SharePoint ve OneDrive aracılığıyla doğrudan paylaşma

SharePoint ve OneDrive aracılığıyla doğrudan paylaşım, yetkilendirme yönetimi işleminin dışındaki kullanıcıları ekleyebilirler. Bu yapılandırmalara derinlemesine bir bakış için bkz. [Microsoft ekipleri, SharePoint ve OneDrive iş Ile erişimi yönetme](9-secure-access-teams-sharepoint.md) . isterseniz, [kullanıcının kişisel OneDrive kullanımını da engelleyebilirsiniz](/office365/troubleshoot/group-policy/block-onedrive-use-from-office) .

### <a name="sending-documents-through-email"></a>E-posta ile belge gönderme

Kullanıcılarınız, belgeleri dış kullanıcılara e-posta ile gönderir. Duyarlılık etiketlerini kullanarak bu belgelere erişimi nasıl kısıtlamak ve şifrelemek istediğinizi göz önünde bulundurun. Daha fazla bilgi için bkz. duyarlılık etiketleriyle erişimi yönetme.

### <a name="unsanctioned-collaboration-tools"></a>Tasdikli işbirliği araçları

İşbirliği araçlarının yatay bir çoğunluğu çok büyük. Kullanıcılarınız, Google Docs, DropBox, bolluk veya zoom gibi platformlar da dahil olmak üzere resmi görevlerin dışında çok büyük bir şekilde kullanıyor. Bu tür araçların, güvenlik duvarı düzeyindeki bir kurumsal ağdan kullanımını ve kuruluş tarafından yönetilen cihazlar için mobil uygulama yönetimini engellemek mümkündür. Ancak, bu da bu platformların tasdikli örneklerini engeller ve yönetilmeyen cihazlardan erişimi engellemez. Gerekli olan tüm kullanımları istemediğiniz platformları engelleyin ve kullanmanız gereken platformlar için tasdiksiz kullanım olmadan iş ilkeleri oluşturun. 

Tasdiksiz uygulamaları yönetme hakkında daha fazla bilgi için bkz.:

* [Bağlı uygulamaları yönetme](/cloud-app-security/governance-actions)

* [Bir uygulamayı tasdikleme ve tasdiksiz etme.](/cloud-app-security/governance-discovery)

 
### <a name="next-steps"></a>Sonraki adımlar

Kaynaklara dışarıdan erişimi güvenli hale getirmek için aşağıdaki makalelere bakın. Eylemleri listelenen sırayla yapmanızı öneririz.

1. [Dış erişim için güvenlik durunuzu belirleme](1-secure-access-posture.md)

2. [Geçerli durumlarınızı bulma](2-secure-access-current-state.md)

3. [İdare planı oluşturma](3-secure-access-plan.md)

4. [Güvenlik için grupları kullanma](4-secure-access-groups.md)

5. [Azure AD B2B 'ye geçiş](5-secure-access-b2b.md) (burada bulabilirsiniz.)

6. [Yetkilendirme yönetimiyle güvenli erişim](6-secure-access-entitlement-managment.md)

7. [Koşullu erişim ilkeleriyle güvenli erişim](7-secure-access-conditional-access.md)

8. [Duyarlılık etiketleriyle güvenli erişim](8-secure-access-sensitivity-labels.md)

9. [Microsoft ekipleri, OneDrive ve SharePoint 'e güvenli erişim](9-secure-access-teams-sharepoint.md)