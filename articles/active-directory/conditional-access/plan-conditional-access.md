---
title: Azure Active Directory Koşullu erişim ilkelerini planlayın | Microsoft Docs
description: Bu makalede, Azure Active Directory için koşullu erişim ilkelerini nasıl planlayacağınızı öğreneceksiniz.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 01/25/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: martincoetzer
ms.collection: M365-identity-device-management
ms.openlocfilehash: 894530aa9624af18f2f33a061d5cde683e9f01be
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72880269"
---
# <a name="how-to-plan-your-conditional-access-deployment-in-azure-active-directory"></a>Nasıl yapılır: Azure Active Directory 'de koşullu erişim dağıtımınızı planlayın

Koşullu erişim dağıtımınızı planlamak, kuruluşunuzdaki uygulamalar ve kaynaklar için gerekli erişim stratejisini elde ettiğinizden emin olmak için kritik öneme sahiptir. Sizin seçtiğiniz koşullara göre kullanıcılarınıza erişim vermek veya erişimi engellemek için ihtiyaç duyduğunuz çeşitli ilkeleri tasarlamak üzere, dağıtımınızın planlama aşamasında en çok zaman harcaın. Bu belgede, güvenli ve etkin koşullu erişim ilkelerini uygulamak için gerçekleştirmeniz gereken adımlar açıklanmaktadır. Başlamadan önce, [Koşullu erişimin](overview.md) nasıl çalıştığını ve ne zaman kullanılacağını anladığınızdan emin olun.

## <a name="what-you-should-know"></a>Bilmeniz gerekenler

Koşullu erişimi, kuruluşunuzun uygulamalarına ve kaynaklarına erişimi, tek başına bir özellik yerine denetlemenize olanak tanıyan bir çerçeve olarak düşünün. Sonuç olarak, bazı koşullu erişim ayarları yapılandırmak için ek özellikler gerektirir. Örneğin, belirli bir [oturum açma risk düzeyine](../identity-protection/howto-identity-protection-configure-risk-policies.md)yanıt veren bir ilke yapılandırabilirsiniz. Ancak, oturum açma risk düzeyine dayalı bir ilke [Azure Active Directory kimlik korumasının](../identity-protection/overview-identity-protection.md) etkinleştirilmesini gerektirir.

Ek özellikler gerekliyse, ilgili lisansları da almanız gerekebilir. Örneğin, koşullu erişim Azure AD Premium P1 özelliği olduğunda, kimlik koruması bir Azure AD Premium P2 lisansı gerektirir.

İki tür koşullu erişim ilkesi vardır: taban çizgisi ve standart. [Temel ilke](baseline-protection.md) , önceden tanımlanmış bir koşullu erişim ilkesidir. Bu ilkelerin amacı, en azından güvenlik düzeyi etkin özelliğinin etkinleştirildiğinden emin olmaktır. Temel ilkeler. Temel ilkeler Azure AD 'nin tüm sürümlerinde kullanılabilir ve yalnızca sınırlı özelleştirme seçenekleri sağlar. Bir senaryo daha fazla esneklik gerektiriyorsa, temel ilkeyi devre dışı bırakın ve gereksinimlerinizi özel bir standart ilkeye uygulayın.

Standart bir koşullu erişim ilkesinde, tüm ayarları özelleştirerek ilkeyi iş gereksinimlerinize göre ayarlayabilirsiniz. Standart ilkeler Azure AD Premium P1 lisansı gerektirir.

## <a name="draft-policies"></a>Taslak ilkeleri

Azure Active Directory Koşullu erişim, bulut uygulamalarınızın korumasını yeni bir düzeye getirmenizi sağlar. Bu yeni düzeyde, bulut uygulamasına nasıl erişebileceğiniz bir statik erişim yapılandırması yerine dinamik ilke değerlendirmesini temel alır. Bir koşullu erişim ilkesiyle, bir erişim koşuluna (Bu**durumda**) bir yanıt (**bunu yapın**) tanımlarsınız.

![Neden ve yanıt](./media/plan-conditional-access/10.png)

Bu planlama modelini kullanarak uygulamak istediğiniz her koşullu erişim ilkesini tanımlayın. Planlama alıştırması:

- Her ilke için yanıtları ve koşulları ana hatlarıyla oluşturmanıza yardımcı olur.
- Kuruluşunuz için iyi belgelenmiş bir koşullu erişim ilkesi kataloğuna neden olur. 

Katalogunuz, ilke uygulamanızın kuruluşunuzun iş gereksinimlerini yansıtmayı değerlendirmek için kullanabilirsiniz. 

Kuruluşunuz için koşullu erişim ilkeleri oluşturmak için aşağıdaki örnek şablonu kullanın:

|*Bu* durumda:|*Bunu yapmak için*şunu yapın:|
|-|-|
|Bir erişim denemesi yapılır:<br>-Bir bulut uygulamasına *<br>-kullanıcılara ve gruplara göre*<br>Kullanarak<br>-Koşul 1 (örneğin, Corp ağı dışında)<br>-Condition 2 (örneğin, cihaz platformları)|Uygulamaya erişimi engelleyin|
|Bir erişim denemesi yapılır:<br>-Bir bulut uygulamasına *<br>-kullanıcılara ve gruplara göre*<br>Kullanarak<br>-Koşul 1 (örneğin, Corp ağı dışında)<br>-Condition 2 (örneğin, cihaz platformları)|(Ve) ile erişim izni verin:<br>-Gereksinim 1 (örneğin, MFA)<br>-Gereksinim 2 (örneğin, cihaz uyumluluğu)|
|Bir erişim denemesi yapılır:<br>-Bir bulut uygulamasına *<br>-kullanıcılara ve gruplara göre*<br>Kullanarak<br>-Koşul 1 (örneğin, Corp ağı dışında)<br>-Condition 2 (örneğin, cihaz platformları)|(Veya) ile erişim izni verin:<br>-Gereksinim 1 (örneğin, MFA)<br>-Gereksinim 2 (örneğin, cihaz uyumluluğu)|

**Bu durumda** , en azından bir bulut uygulamasına (**ne**) erişmeye çalışan sorumlusu (**kim**) tanımlar. Gerekirse, bir erişim denemesinin **nasıl** gerçekleştirileceğini de dahil edebilirsiniz. Koşullu erişim 'de, kimin, ne ve nasıl koşul olarak bilindiklerinizi tanımlayan öğeler. Daha fazla bilgi için bkz. [koşullu erişim Azure Active Directory koşullar nelerdir?](conditions.md) 

Bunu **yaptığınızda**, ilkenizin yanıtını bir erişim koşulu olarak tanımlarsınız. Yanıtınız içinde, çok faktörlü kimlik doğrulaması (MFA) gibi ek gereksinimlere sahip olan erişimi engeller veya erişim izni verirsiniz. Kapsamlı bir genel bakış için bkz. [Azure Active Directory Koşullu erişim içindeki erişim denetimleri nelerdir?](controls.md)  

Erişim denetimleriniz ile koşulların birleşimi, koşullu erişim ilkesini temsil eder.

![Neden ve yanıt](./media/plan-conditional-access/51.png)

Daha fazla bilgi için, bkz. [bir ilkenin çalışması için gerekli olanlar](best-practices.md#whats-required-to-make-a-policy-work).

Bu noktada, ilkelerinize yönelik bir adlandırma standardına karar vermek iyi bir zamandır. Adlandırma standardı, ilkeleri bulmanıza ve Azure yönetim portalında açmadan amaçlarını anlamanıza yardımcı olur. İlkeyi göstermek için adlandırın:

- Sıra numarası
- Bu uygulama için geçerli olan bulut uygulaması
- Yanıt
- Kim için geçerli
- Ne zaman geçerliyse (varsa)
 
![Adlandırma standardı](./media/plan-conditional-access/11.png)

Açıklayıcı bir ad, koşullu erişim uygulamanıza genel bakış sağlamanıza yardımcı olmakla kalmaz, bir konuşmadaki ilkeye başvurmanız gerekirse sıra numarası yararlı olur. Örneğin, telefonda bir yönetici konuşmanız durumunda, bir sorunu gidermek için bu kişiden ilke EM063 açmasını isteyebilirsiniz.

Örneğin, aşağıdaki ad, ilkenin Dynamics CRP uygulamasını kullanarak dış ağlardaki pazarlama kullanıcıları için MFA gerektirdiğini belirtir:

`CA01 - Dynamics CRP: Require MFA For marketing When on external networks`

Etkin ilkelerinize ek olarak [kesinti/acil durum senaryolarında ikincil dayanıklı erişim denetimleri](../authentication/concept-resilient-controls.md)olarak davranan devre dışı ilkeler de uygulamanız önerilir. Acil durum ilkelerine yönelik adlandırma standartlarınız birkaç öğe içermelidir: 

- Adın diğer ilkeler arasında öne çıkmasını sağlamak için başlangıçta `ENABLE IN EMERGENCY`.
- İçin uygulanması gereken kesilme adı.
- Yöneticinin hangi sıra ilkelerinin etkin olması gerektiğini bilmesini sağlamak için bir sıralama sıra numarası. 

Örneğin, aşağıdaki ad bu ilkenin dört ' un ilk ilkesi olduğunu, MFA kesintisi varsa etkinleştirmelisiniz:

`EM01 - ENABLE IN EMERGENCY, MFA Disruption[1/4] - Exchange SharePoint: Require hybrid Azure AD join For VIP users`

## <a name="plan-policies"></a>Plan ilkeleri

Koşullu erişim ilkesi çözümünüzü planlarken, aşağıdaki sonuçları elde etmek için ilkeler oluşturmanız gerekip gerekmediğini değerlendirin. 

### <a name="block-access"></a>Erişimi engelle

Erişimi engelleme seçeneği güçlü olur çünkü:

- Trumps bir kullanıcının diğer tüm atamaları
- , Tüm kuruluşunuzun kiracınızda oturum açmasını engelleme gücüne sahiptir
 
Tüm kullanıcılar için erişimi engellemek isterseniz, ilkeden en az bir kullanıcıyı (genellikle acil durum erişim hesapları) dışarıda bırakmanız gerekir. Daha fazla bilgi için bkz. [kullanıcıları ve grupları seçme](block-legacy-authentication.md#select-users-and-cloud-apps).  

### <a name="require-mfa"></a>MFA gerektirme

Kullanıcılarınızın oturum açma deneyimini basitleştirmek için, kullanıcıların bir Kullanıcı adı ve parola kullanarak bulut uygulamalarınızda oturum açmalarına izin vermek isteyebilirsiniz. Bununla birlikte, genellikle daha güçlü bir hesap doğrulama biçimi gerektirirken önerilebilir olan en az bir senaryo vardır. Koşullu erişim ilkesiyle, MFA gereksinimini belirli senaryolara sınırlayabilirsiniz. 

MFA 'nın erişime ihtiyacı olan yaygın kullanım örnekleri:

- [Yöneticiler tarafından](howto-baseline-protect-administrators.md)
- [Belirli uygulamalara](app-based-mfa.md) 
- [Ağ konumları ' ndan güvenmediğiniz](untrusted-networks.md).

### <a name="respond-to-potentially-compromised-accounts"></a>Tehlikede riskli hesaplara yanıt verme

Koşullu erişim ilkeleriyle, riskli olabilecek kimliklerden oturum açma işlemleri için otomatik yanıtlar uygulayabilirsiniz. Bir hesabın tehlikeye düşmesi olasılığı, risk düzeyleri biçiminde ifade edilir. Kimlik koruması ile hesaplanan iki risk düzeyi vardır: oturum açma riski ve Kullanıcı riski. Bir oturum açma riskine yanıt uygulamak için iki seçeneğiniz vardır:

- Koşullu erişim ilkesinde [oturum açma risk koşulu](conditions.md#sign-in-risk)
- Kimlik koruması 'nda [oturum açma risk ilkesi](../identity-protection/howto-sign-in-risk-policy.md) 

Daha fazla özelleştirme seçeneği sağladığından, oturum açma riskini koşul olarak adresleme tercih edilen yöntemdir.

Kullanıcı risk düzeyi yalnızca kimlik koruması 'nda [Kullanıcı risk ilkesi](../identity-protection/howto-user-risk-policy.md) olarak kullanılabilir. 

Daha fazla bilgi için bkz. [Azure Active Directory kimlik koruması nedir?](../identity-protection/overview.md) 

### <a name="require-managed-devices"></a>Yönetilen cihazları gerektirme

Bulut kaynaklarınıza erişmek için desteklenen cihazların uzalama, kullanıcılarınızın verimliliğini artırmaya yardımcı olur. Ters çevir tarafında, ortamınızdaki belirli kaynaklara bilinmeyen koruma düzeyi olan cihazların erişmesini istemezsiniz. Etkilenen kaynaklar için kullanıcıların yalnızca yönetilen bir cihaz kullanarak erişebilmesi gerekir. Daha fazla bilgi için bkz. [koşullu erişim ile bulut uygulama erişimi için yönetilen cihazlar gerektirme](require-managed-devices.md). 

### <a name="require-approved-client-apps"></a>Onaylı istemci uygulamaları gerektirme

Kendi cihazlarını getir (BYOD) senaryolarında yapmanız gereken ilk kararlardan biri, tüm cihazı veya yalnızca üzerindeki verileri yönetmeniz gerekip gerekmediğini belirtir. Çalışanlarınız hem kişisel hem de iş görevleri için mobil cihazları kullanır. Çalışanlarınızın üretken olduğundan emin olmaya devam ederken, veri kaybını da engellemek isteyebilirsiniz. Azure Active Directory (Azure AD) koşullu erişimi sayesinde, bulut uygulamalarınıza erişimi, Şirket verilerinizi koruyabilecek onaylanan istemci uygulamalarıyla sınırlandırabilirsiniz. Daha fazla bilgi için bkz. [koşullu erişim ile Cloud App erişimi için onaylanan istemci uygulamaları gerektirme](app-based-conditional-access.md).

### <a name="block-legacy-authentication"></a>Eski kimlik doğrulamasını engelleme

Azure AD, eski kimlik doğrulama dahil olmak üzere en yaygın olarak kullanılan kimlik doğrulama ve yetkilendirme protokollerini destekler. Eski kimlik doğrulaması kullanan uygulamaların kiracının kaynaklarına erişmesini nasıl önleyebilirim? Öneri yalnızca bir koşullu erişim ilkesiyle engellenmeniz önerilir. Gerekirse, yalnızca belirli kullanıcıların ve belirli ağ konumlarının eski kimlik doğrulamasına dayalı uygulamalar kullanmasına izin verebilirsiniz. Daha fazla bilgi için bkz. [Azure AD 'de koşullu erişim ile eski kimlik doğrulamasını engelleme](block-legacy-authentication.md).

## <a name="test-your-policy"></a>İlkenizi test etme

Bir ilkeyi üretime almadan önce, beklendiği gibi davrandığını doğrulamak için test etmelisiniz.

1. Test kullanıcıları oluşturma
1. Test planı oluşturma
1. İlkeyi yapılandırma
1. Benzetimli bir oturum açmayı değerlendir
1. İlkenizi test etme
1. Temizleme

### <a name="create-test-users"></a>Test kullanıcıları oluşturma

Bir ilkeyi test etmek için ortamınızdaki kullanıcılara benzer bir kullanıcı kümesi oluşturun. Test kullanıcıları oluşturmak, gerçek kullanıcıları etkilemeden ve uygulama ve kaynaklara erişimleri büyük olasılıkla kesintiye uğramadan önce ilkelerinizin beklendiği gibi çalıştığını doğrulamanızı sağlar. 

Bazı kuruluşların bu amaçla test kiracılar vardır. Ancak, bir ilkenin sonucunu tam olarak test etmek için bir test kiracısındaki tüm koşulların ve uygulamaların yeniden oluşturulması zor olabilir. 

### <a name="create-a-test-plan"></a>Test planı oluşturma

Test planı, beklenen sonuçlarla gerçek sonuçlar arasında bir karşılaştırma yapmak için önemlidir. Bir şeyi test etmeden önce her zaman bir beklenmelidir. Aşağıdaki tabloda örnek test çalışmaları özetlenmektedir. CA ilkelerinizin nasıl yapılandırıldığına bağlı olarak senaryoları ve beklenen sonuçları ayarlayın.

|İlke |Senaryo |Beklenen sonuç | Sonuç |
|---|---|---|---|
|[İş olmadığında MFA gerektir](https://docs.microsoft.com/azure/active-directory/conditional-access/untrusted-networks)|Güvenilen bir konum/iş sırasında yetkili kullanıcı *uygulama* oturumunu kapatır|Kullanıcıdan MFA istenmez| |
|[İş olmadığında MFA gerektir](https://docs.microsoft.com/azure/active-directory/conditional-access/untrusted-networks)|Yetkili Kullanıcı, güvenilir bir konum/iş üzerinde olmadığı sürece *uygulamada* oturum açar|Kullanıcıdan MFA yapması istenir ve başarıyla oturum açabilir| |
|[MFA gerektir (yönetici için)](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-baseline-protect-administrators)|Genel yönetici *uygulamada* oturum açar|Yöneticiye MFA sorulur| |
|[Riskli oturum açma işlemleri](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-sign-in-risk-policy)|Kullanıcı bir [Tor tarayıcısı](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection-playbook) kullanarak *uygulamada* oturum açar|Yöneticiye MFA sorulur| |
|[Cihaz yönetimi](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices)|Yetkili Kullanıcı yetkili bir cihazdan oturum açmaya çalışır|Erişim Izni verildi| |
|[Cihaz yönetimi](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices)|Yetkili kullanıcı yetkisiz bir cihazdan oturum açmaya çalışır|Erişim engellendi| |
|[Riskli kullanıcılar için parola değiştirme](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-user-risk-policy)|Yetkili Kullanıcı güvenliği tehlikeye giren kimlik bilgileriyle oturum açmaya çalışır (yüksek riskli oturum açma)|İlkenize göre kullanıcıdan parolayı değiştirmesi veya erişimin engellenmesi istenir| |

### <a name="configure-the-policy"></a>İlkeyi yapılandırma

Koşullu erişim ilkelerinin yönetilmesi, el ile gerçekleştirilen bir görevdir. Azure portal, koşullu erişim ilkelerinizi tek bir merkezi konumda yönetebilirsiniz. koşullu erişim sayfası. Koşullu erişim sayfasına yönelik bir giriş noktası, **Active Directory** gezinti bölmesindeki **güvenlik** bölümüdür. 

![Koşullu Erişim](media/plan-conditional-access/03.png)

Koşullu erişim ilkeleri oluşturma hakkında daha fazla bilgi edinmek istiyorsanız bkz. [koşullu erişim Azure Active Directory belirli uygulamalar IÇIN MFA gerektirme](app-based-mfa.md). Bu hızlı başlangıç şunları yapmanıza yardımcı olur:

- Kullanıcı arabirimi hakkında bilgi sahibi olun.
- Koşullu erişimin nasıl çalıştığına ilişkin bir ilk izlenim alın. 

### <a name="evaluate-a-simulated-sign-in"></a>Benzetimli bir oturum açmayı değerlendir

Koşullu erişim ilkenizi yapılandırdığınıza göre muhtemelen, beklendiği gibi çalışıp çalışmadığını bilmeniz gerekir. İlk adım olarak, test kullanıcılarınız için bir oturum açma benzetimi yapmak üzere koşullu erişim [ilke aracı](what-if-tool.md) ' nı kullanın. Simülasyon, bu oturum açma işleminin ilkeleriniz üzerindeki etkisini tahmin eder ve bir simülasyon raporu oluşturur.

>[!NOTE]
> Sanal bir çalıştırma, koşullu erişim ilkesinin etkisinden oluşan bir izlenim sağlarken, gerçek bir test çalıştırmasının yerini almaz.

### <a name="test-your-policy"></a>İlkenizi test etme

Test planınıza göre test çalışmalarını çalıştırın. Bu adımda, her bir ilkenin doğru bir şekilde davrandığından emin olmak için test kullanıcılarınız için her bir ilkenin uçtan uca bir testini çalıştırırsınız. Her bir testi yürütmek için yukarıda oluşturulan senaryoları kullanın.

Bir ilkenin dışlama ölçütünü test ettiğinizden emin olmak önemlidir. Örneğin, MFA gerektiren bir ilkeden Kullanıcı veya grup dışlayabilirsiniz. Diğer ilkelerin birleşimi bu kullanıcılar için MFA gerektirebileceğinden, dışlanan kullanıcılardan MFA için istenirse test edin.

### <a name="cleanup"></a>Temizleme

Temizleme yordamı aşağıdaki adımlardan oluşur:

1. İlkeyi devre dışı bırakın.
1. Atanan kullanıcıları ve grupları kaldırın.
1. Test kullanıcılarını silin.  

## <a name="move-to-production"></a>Üretime taşıma

Ortamınız için yeni ilkeler hazırlandığınızda, bunları aşamalar halinde dağıtın:

- Son kullanıcılara iç değişiklik iletişimi sağlayın.
- Küçük bir Kullanıcı kümesiyle başlayın ve ilkenin beklendiği gibi davrandığını doğrulayın.
- Bir ilkeyi daha fazla Kullanıcı içerecek şekilde genişlettiğinizde, tüm yöneticileri dışlayacak şekilde devam edin. Yönetici dışlama, bir değişiklik gerekliyse birisinin bir ilkeye hala erişmesini sağlar.
- Yalnızca gerekli olduğunda bir ilkeyi tüm kullanıcılara uygulayın.

En iyi uygulama olarak, şu olan en az bir kullanıcı hesabı oluşturun:

- İlke yönetimine ayrılmıştır
- Tüm ilkeleriniz hariç tutuldu

## <a name="rollback-steps"></a>Geri alma adımları

Yeni uygulanan ilkelerinizi geri almanız gerekiyorsa, geri almak için aşağıdaki seçeneklerden birini veya daha fazlasını kullanın:

1. **Ilkeyi devre dışı bırakma** -bir ilkeyi devre dışı bırakmak, bir Kullanıcı oturum açmaya çalıştığında uygulanmasının mümkün olmadığından emin olur. Her zaman geri dönüp ilkeyi kullanmak istediğinizde etkinleştirebilirsiniz.

   ![ilkeyi devre dışı bırak](media/plan-conditional-access/07.png)

1. Bir **Kullanıcı/grubu Ilkeden hariç tutma** -bir Kullanıcı uygulamaya erişe, kullanıcıyı ilkeden dışlamayı seçebilirsiniz

   ![Exluce kullanıcıları](media/plan-conditional-access/08.png)

   > [!NOTE]
   > Bu seçenek, yalnızca kullanıcının güvendiği durumlarda gelişigüzel bir şekilde kullanılmalıdır. Kullanıcı, ilke veya gruba mümkün olan en kısa sürede yeniden eklenmelidir.

1. **Ilkeyi Sil** -ilke artık gerekmiyorsa, silin.

## <a name="next-steps"></a>Sonraki adımlar

Mevcut bilgilere genel bir bakış edinmek için [Azure AD koşullu erişim belgelerine](index.yml) göz atın.
