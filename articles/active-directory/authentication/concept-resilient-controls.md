---
title: Esnek erişim denetimi yönetim stratejisi oluşturma-Azure AD
description: Bu belge, bir kuruluşun öngörülemeyen kesintiler sırasında kilitleme riskini azaltmak için esnekliği sağlamak üzere benimseme gereken stratejiler hakkında rehberlik sağlar
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.workload: identity
ms.date: 06/08/2020
ms.author: martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: d7e4d0c41990fcc23dd19b5682997f6381bfdb20
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97937102"
---
# <a name="create-a-resilient-access-control-management-strategy-with-azure-active-directory"></a>Azure Active Directory ile dayanıklı bir erişim denetimi yönetim stratejisi oluşturma

>[!NOTE]
> The information contained in this document represents the current view of Microsoft Corporation on the issues discussed as of the date of publication. Microsoft 'un değişen piyasa koşullarını yanıtlaması gerektiğinden, Microsoft 'un kapsamında bir taahhüt olarak yorumlanmamalıdır ve Microsoft 'un, yayın tarihinden sonra sunulan bilgilerin doğruluğunu garanti edemez.

Multi-Factor Authentication (MFA) veya tek bir ağ konumu gibi tek bir erişim denetimine bağlı olan kuruluşlar, tek erişim denetimi kullanılamaz hale gelirse veya hatalı yapılandırılmış olması durumunda uygulamalarına ve kaynaklarına erişim arızalarına açıktır. Örneğin, doğal bir olağanüstü durum, telekomünikasyon altyapısının veya şirket ağlarının büyük segmentlerinin kullanılamamasına yol açabilir. Bu tür bir kesinti, son kullanıcıların ve yöneticilerin oturum açabilmasını önleyebilir.

Bu belge, bir kuruluşun aşağıdaki senaryolarla öngörülemeyen kesintiler sırasında kilitleme riskini azaltmak için esnekliği sağlamak üzere benimseme gereken stratejiler hakkında rehberlik sağlar:

 1. Kuruluşlar, risk azaltma stratejilerini veya yedek planları uygulayarak **kesintiye uğramadan önce** kilitleme riskini azaltmak için dayanıklılığı artırabilir.
 2. Kuruluşlar, risk azaltma stratejilerini ve acil durum planlarını ortadan kaldırarak, **kesintiye** uğradıklarında seçtikleri uygulamalara ve kaynaklara erişmeye devam edebilir.
 3. Kuruluşlar,  **kesintiden sonra** ve uygulandıkları tüm kıtları geri almadan önce günlükleri gibi bilgileri korudıklarından emin olmalıdır.
 4. Önleme stratejilerini veya alternatif planları uygulanmayan kuruluşlar, kesintiye uğramak için **acil durum seçeneklerini** uygulayabilir.

## <a name="key-guidance"></a>Anahtar Kılavuzu

Bu belgede dört temel kilit vardır:

* Acil durum erişim hesapları kullanarak yönetici kilitlemesini önleyin.
* Kullanıcı başına MFA yerine koşullu erişim kullanarak MFA uygulayın.
* Birden fazla koşullu erişim denetimi kullanarak Kullanıcı kilitlemeyi azaltın.
* Her bir kullanıcı için birden çok kimlik doğrulama yöntemini veya eşdeğerlerini sağlayarak Kullanıcı kilitlemeyi azaltabilirsiniz.

## <a name="before-a-disruption"></a>Kesintiden önce

Gerçek kesintiyi azaltıcı bir işlem, ortaya çıkabilecek erişim denetimi sorunlarıyla ilgilenirken kuruluşun birincil odağında olmalıdır. Azaltıcı bir gerçek olay için planlama ve erişim denetimlerinin ve işlemlerin kesintiler sırasında etkilenmemesini sağlamak için stratejiler de dahildir.

### <a name="why-do-you-need-resilient-access-control"></a>Neden esnek erişim denetimine ihtiyacınız var?

 Kimlik, uygulamalara ve kaynaklara erişen kullanıcıların Denetim düzledir. Kimlik sisteminiz, erişim denetimleri veya kimlik doğrulama gereksinimleri gibi hangi kullanıcılara ve hangi koşullarda, kullanıcıların uygulamalara erişim edindiğini denetler. Bir veya daha fazla kimlik doğrulama veya erişim denetimi gereksinimi, kullanıcıların öngörülemeyen koşullar nedeniyle kimlik doğrulaması yapabilmeleri durumunda, kuruluşlar aşağıdaki sorunlardan biri veya her ikisi ile karşılaşabilir:

* **Yönetici kilitleme:** Yöneticiler kiracıyı veya hizmetleri yönetemez.
* **Kullanıcı kilitleme:** Kullanıcılar uygulamalara veya kaynaklara erişemez.

### <a name="administrator-lockout-contingency"></a>Yönetici kilitlenme yedek

Kiracınıza yönetici erişiminin kilidini açmak için acil durum erişim hesapları oluşturmanız gerekir. *Kesme camı* olarak da bilinen bu acil durum erişim hesapları, normal ayrıcalıklı hesap erişimi yordamları kullanılabilir OLMADıĞıNDA Azure AD yapılandırmasını yönetme erişimine izin verir. [Acil durum erişim hesabı önerilerini]( ../users-groups-roles/directory-emergency-access.md)takip eden en az iki acil durum erişim hesabı oluşturulmalıdır.

### <a name="mitigating-user-lockout"></a>Kullanıcı kilitlemeyi azaltma

 Kullanıcı kilitleme riskini azaltmak için, kullanıcılara uygulamalara ve kaynaklara nasıl erişebilecekleri konusunda seçim yapmak amacıyla birden çok denetim ile koşullu erişim ilkeleri kullanın. Bir kullanıcıya, örneğin MFA ile oturum açma **veya** yönetilen bir cihazdan oturum açma veya şirket ağından **oturum açma arasında** seçim yaparak, erişim denetimlerinden biri kullanılamaz durumdaysa kullanıcının çalışmaya devam etmesi için başka seçenekleri vardır.

#### <a name="microsoft-recommendations"></a>Microsoft önerileri

Aşağıdaki erişim denetimlerini, kuruluş için mevcut koşullu erişim ilkelerinize ekleyin:

1. Farklı iletişim kanallarına dayanan her bir kullanıcı için birden çok kimlik doğrulama yöntemi sağlayın (örneğin, Microsoft Authenticator uygulaması (internet tabanlı), OATH belirteci (cihazda oluşturulan) ve SMS (telephonic). Aşağıdaki PowerShell betiği, kullanıcılarınızın kaydolmaları gereken ek yöntemleri: [Azure AD MFA kimlik doğrulama yöntemi analizi Için betik](/samples/azure-samples/azure-mfa-authentication-method-analysis/azure-mfa-authentication-method-analysis/)olarak önceden belirlemenize yardımcı olur.
2. Windows 10 cihazlarında Iş için Windows Hello 'Yu, doğrudan cihaz oturum açma işleminden MFA gereksinimlerini karşılayacak şekilde dağıtın.
3. Güvenilen cihazları [Azure AD hibrit JOIN](../devices/overview.md) veya [Microsoft Intune yönetilen cihazlar](/intune/planning-guide)aracılığıyla kullanın. Güvenilen cihazlar, kullanıcıya MFA sınaması olmadan ilkenin güçlü kimlik doğrulama gereksinimlerini karşılayabildiğinden, Kullanıcı deneyimini iyileştirir. Daha sonra MFA, yeni bir cihaz kaydedilirken ve güvenilir olmayan cihazlardan uygulamalara veya kaynaklara erişirken gerekecektir.
4. Kullanıcı veya oturum açma, sabit MFA ilkelerinin yerine risk altında olduğunda erişimi önleyen Azure AD kimlik koruması risk tabanlı ilkelerini kullanın.
5. Azure AD MFA NPS uzantısını kullanarak VPN erişimini koruyorsanız, VPN çözümünüzü bir [SAML uygulaması](../manage-apps/view-applications-portal.md) olarak Federasyonu ve uygulama kategorisini aşağıda önerilen şekilde saptamayı düşünün. 

>[!NOTE]
> Risk tabanlı ilkeler için [Azure AD Premium P2](https://azure.microsoft.com/pricing/details/active-directory/) lisansları gerekir.

Aşağıdaki örnek, kullanıcının uygulamalarına ve kaynaklarına erişmesi için dayanıklı bir erişim denetimi sağlamak üzere oluşturmanız gereken ilkeleri açıklar. Bu örnekte, erişim vermek istediğiniz hedef kullanıcıları içeren bir güvenlik grubu **Appusers** , çekirdek yöneticilerle **coreadmins** adlı bir grup ve acil durum erişim **hesapları ile acil durum, acil durum** erişimi adlı bir grup sağlamanız gerekir.
Bu örnek ilke kümesi, **appusers**'daki seçili kullanıcılara, güvenilen bir cihazdan bağlantı varsa veya örneğin MFA gibi güçlü kimlik doğrulaması sağlamasına izin verir. Acil durum hesaplarını ve çekirdek yöneticileri dışlar.

**CA risk azaltma ilkeleri kümesi:**

* İlke 1: hedef grupları dışındaki kişilere erişimi engelleyin
  * Kullanıcılar ve gruplar: tüm kullanıcıları dahil et. AppUsers, CoreAdmins ve acil bir Gencyaccess hariç tut
  * Bulut uygulamaları: tüm uygulamaları Ekle
  * Koşullar: (yok)
  * İzin denetimi: engelle
* İlke 2: MFA veya güvenilir cihaz gerektiren AppUsers 'a erişim Izni verin.
  * Kullanıcılar ve gruplar: AppUsers dahil. CoreAdmins ve acil bir Gencyaccess hariç tut
  * Bulut uygulamaları: tüm uygulamaları Ekle
  * Koşullar: (yok)
  * Verme denetimi: erişim verme, Multi-Factor Authentication gerektir, cihazın uyumlu olmasını gerektir. Birden çok denetim için: seçili denetimlerden birini gerektir.

### <a name="contingencies-for-user-lockout"></a>Kullanıcı kilitleme için kıdurumlar

Alternatif olarak, kuruluşunuz da yedek ilkeler oluşturabilir. Yedek ilkeler oluşturmak için iş sürekliliği, operasyonel maliyet, mali maliyet ve güvenlik riskleri arasında zorunluluğunu getirir kriterleri tanımlamanız gerekir. Örneğin, bir acil durum ilkesini yalnızca bir kullanıcı alt kümesi, bir uygulamalar alt kümesi veya bir konum alt kümesi için etkinleştirebilirsiniz. Bir risk azaltma yöntemi uygulandığında, acil durum ilkeleri yöneticilere ve son kullanıcılara uygulamalara ve kaynaklara erişim sağlayacak. Microsoft, yöneticilerin, ilkelerin açık olmaları gereken etkileri izleyebilmesi için, kullanımda olmadığında [yalnızca rapor modunda](../conditional-access/howto-conditional-access-insights-reporting.md) yedek ilkelerin etkinleştirilmesini önerir.

 Bir kesinti sırasında pozlandırmayı anlamak, riskinizi azaltmaya yardımcı olur ve planlama sürecinizin önemli bir parçasıdır. Acil durum planınızı oluşturmak için öncelikle kuruluşunuzun aşağıdaki iş gereksinimlerini saptayın:

1. Görev açısından kritik uygulamalarınızı daha önce belirleme: daha düşük bir risk/Güvenlik duruşunu kullanarak erişim vermeniz gereken uygulamalar nelerdir? Bu uygulamaların bir listesini oluşturun ve diğer paydaşlarınızın (iş, güvenlik, yasal, liderlik) tüm erişim denetimi dışarıda kaldığında, bu uygulamaların hala çalışmaya devam etmesi gerektiğini kabul etmesini sağlayın. Büyük olasılıkla şu kategoriler ile bitecaksınız:
   * **Kategori 1 görev açısından kritik uygulamalar** , birkaç dakikadan uzun bir süre içinde kullanılamayacak ve Örneğin kuruluşun gelirini doğrudan etkileyen uygulamalar.
   * 2. kategoriye, işletmenin birkaç saat içinde erişilebilir olması gereken **önemli uygulamalar** .
   * 3. Kategori, birkaç gün kesintiye uğraabilecek **düşük öncelikli uygulamalar** .
2. Kategori 1 ve 2 ' deki uygulamalar için, Microsoft, izin vermek istediğiniz erişim düzeyi türünü önceden planlamanızı önerir:
   * İndirmeleri kısıtlamak gibi tam erişime veya kısıtlı oturuma izin vermek istiyor musunuz?
   * Uygulamanın bir kısmına erişime izin vermek istiyor musunuz, ancak tüm uygulamayı değil mi?
   * Erişim denetimi geri yüklenene kadar bilgi çalışanı erişimine izin vermek ve yönetici erişimini engellemek istiyor musunuz?
3. Bu uygulamalar için, Microsoft ayrıca hangi erişim türlerini kasıtlı olarak açacağını ve hangilerinin kapatacağınıza ilişkin plan yapmanızı de önerir:
   * Yalnızca tarayıcı erişimine izin vermek ve çevrimdışı verileri kaydedebilen zengin istemcileri engellemek istiyor musunuz?
   * Yalnızca şirket ağı içindeki kullanıcılar için erişime izin vermek ve dış kullanıcıların engellenmesini önlemek istiyor musunuz?
   * Belirli ülkelerden veya bölgelerden yalnızca kesinti sırasında erişime izin vermek istiyor musunuz?
   * Farklı bir erişim denetimi yoksa, özellikle de görev açısından kritik uygulamalar için, özel durum ilkelerine ilke eklemek istiyor musunuz?

#### <a name="microsoft-recommendations"></a>Microsoft önerileri

Bir yedek koşullu erişim ilkesi, Azure AD MFA, üçüncü taraf MFA, risk tabanlı veya cihaz tabanlı denetimleri atlayacak bir **yedekleme ilkesidir** . Bir yedek ilke etkinleştirildiğinde beklenmeyen kesintiyi en aza indirmek için, ilke kullanımda olmadığında yalnızca rapor modunda kalmalıdır. Yöneticiler, koşullu erişim öngörüleri çalışma kitabını kullanarak, acil durum ilkelerinin olası etkisini izleyebilir. Kuruluşunuz, acil durum planınızı etkinleştirmeye karar verdiğinde, Yöneticiler ilkeyi etkinleştirebilir ve normal denetim tabanlı ilkeleri devre dışı bırakabilir.

>[!IMPORTANT]
> Kullanıcılarınız için güvenliği zorlayan ilkelerin devre dışı bırakılması, geçici olarak bile, acil durum planı çalışırken güvenlik duruşunuzu azaltır.

* Bir kimlik bilgisi türü veya bir erişim denetimi mekanizmasının uygulamalarınıza erişimi etkilediği bir geri dönüş ilkeleri kümesi yapılandırın. Yalnızca rapor olarak etki alanına katılmayı gerektiren bir ilke yapılandırma olarak, bir üçüncü taraf MFA sağlayıcısı gerektiren etkin bir ilke için yedekleme olarak.
* [Parola Kılavuzu](https://aka.ms/passwordguidance) teknik incelemesindeki UYGULAMALARı izleyerek MFA gerekli olmadığında, kötü aktör tahmin parolalarının riskini azaltın.
* Kullanıcıların, bir ortak parola ve daha çok tercih ettiğiniz koşulları kullanmayın olduğundan emin olmak için [Azure ad Self-Service parola sıfırlama (SSPR)](./tutorial-enable-sspr.md) ve [Azure AD parola korumasını](./howto-password-ban-bad-on-premises-deploy.md) dağıtın.
* Belirli bir kimlik doğrulama düzeyi yalnızca tam erişime geri düşmeniz yerine, uygulamalar içindeki erişimi kısıtlayan ilkeler kullanın. Örnek:
  * Exchange ve SharePoint 'e kısıtlı oturum talebi gönderen bir yedekleme ilkesi yapılandırın.
  * Kuruluşunuz Microsoft Cloud App Security kullanıyorsa, MCAS 'nin bulunduğu ilkeye geri dönerek MCAS, salt okuma erişimine Izin verir ancak karşıya yüklemelerden yararlanır.
* Kesintilerinizi bir kesinti sırasında bulmanın kolay olduğundan emin olmak için ilkelerinizi adlandırın. İlke adına aşağıdaki öğeleri ekleyin:
  * İlke için bir *etiket numarası* .
  * Gösterilecek metin, bu ilke yalnızca acil durumlar içindir. Örneğin: **acıl durumda etkinleştir**
  * İçin  geçerli olan kesinti. Örneğin: **MFA kesintisi sırasında**
  * İlkeleri etkinleştirmeniz gereken sırayı göstermek için bir *sıra numarası* .
  * İçin geçerli olduğu *uygulamalar* .
  * Uygulanan *denetimler* .
  * Gereken *koşullar* .
  
Acil durum ilkeleri için bu adlandırma standardı aşağıdaki gibi olacaktır: 

```
EMnnn - ENABLE IN EMERGENCY: [Disruption][i/n] - [Apps] - [Controls] [Conditions]
```

Aşağıdaki örnek: **görev açısından kritik Işbirliği uygulamalarına erişimi geri yüklemek Için örnek a-acil durum koşullu erişim ilkesi** tipik bir kurumsal öneme sahiptir. Bu senaryoda, kuruluş genellikle tüm Exchange Online ve SharePoint Online erişimi için MFA gerektirir ve bu durumda, müşterinin bir kesinti (Azure AD MFA, şirket içi MFA sağlayıcısı veya üçüncü taraf MFA) vardır. Bu ilke, belirli bir hedeflenmiş kullanıcıların, güvenilen Windows cihazlarından bu uygulamalara erişmesine izin vererek bu kesinti için güvenlik altına alınır. Ayrıca, bu kısıtlamalardan acil durum hesaplarını ve çekirdek yöneticileri de hariç tutar. Daha sonra hedeflenen kullanıcılar Exchange Online ve SharePoint Online 'a erişim kazanacaktır, diğer kullanıcılar da kesinti nedeniyle uygulamalara erişim sahibi olmaya devam eder. Bu örnek, hedef kullanıcılarla adlandırılmış bir ağ **konumu corpnetwork** ve bir güvenlik **grubu gerektirir** , çekirdek yöneticilerle **coreadmins** adlı bir grup ve acil durum erişim hesapları **ile acil durum adı adlı** bir grup gerekir. Acil durum, istenen erişimi sağlamak için dört ilke gerektirir. 

**Örnek A-görev açısından kritik Işbirliği uygulamalarına erişimi geri yüklemek için acil durum koşullu erişim ilkeleri:**

* İlke 1: Exchange ve SharePoint için etki alanına katılmış cihazlar gerektir
  * Ad: EM001-acıl ETKINLEŞTIRME: MFA kesintisi [1/4]-Exchange SharePoint-karma Azure AD JOIN gerektir
  * Kullanıcılar ve gruplar: Kıgencyaccess ekleyin. CoreAdmins ve acil bir Gencyaccess hariç tut
  * Bulut uygulamaları: Exchange Online ve SharePoint Online
  * Koşullar: any
  * İzin verme denetimi: etki alanına katılmış ıste
  * Durum: yalnızca rapor
* İlke 2: Windows dışındaki platformları engelleyin
  * Ad: EM002-acıl ETKINLEŞTIRME: MFA kesintisi [2/4]-Exchange SharePoint-Windows dışında erişimi engelleyin
  * Kullanıcılar ve gruplar: tüm kullanıcıları dahil et. CoreAdmins ve acil bir Gencyaccess hariç tut
  * Bulut uygulamaları: Exchange Online ve SharePoint Online
  * Koşullar: cihaz platformu tüm platformları Içerir, Windows 'u hariç tut
  * İzin denetimi: engelle
  * Durum: yalnızca rapor
* İlke 3: CorpNetwork dışındaki ağları engelleyin
  * Ad: EM003-acıl ETKINLEŞTIRME: MFA kesintisi [3/4]-Exchange SharePoint-şirket ağı dışında erişimi engelle
  * Kullanıcılar ve gruplar: tüm kullanıcıları dahil et. CoreAdmins ve acil bir Gencyaccess hariç tut
  * Bulut uygulamaları: Exchange Online ve SharePoint Online
  * Koşullar: konumlar her konum Içerir, CorpNetwork ağını hariç tutar
  * İzin denetimi: engelle
  * Durum: yalnızca rapor
* İlke 4: EAS 'yi açıkça engelle
  * Ad: EM004-acıl ETKINLEŞTIRME: MFA kesintisi [4/4]-Exchange-Block EAS tüm kullanıcılar için
  * Kullanıcılar ve gruplar: tüm kullanıcıları dahil et
  * Bulut uygulamaları: Exchange Online 'ı Ekle
  * Koşullar: Istemci uygulamaları: Exchange Active Sync
  * İzin denetimi: engelle
  * Durum: yalnızca rapor

Etkinleştirme sırası:

1. Mevcut MFA ilkesinden Kıgencyaccess, CoreAdmins ve acil bir Gencyaccess hariç tutun. Kıgencyaccess içindeki bir kullanıcının SharePoint Online ve Exchange Online 'a erişebileceğini doğrulayın.
2. Ilkeyi etkinleştir 1: dışlama gruplarında olmayan etki alanı katılmış cihazlardaki kullanıcıların Exchange Online ve SharePoint Online 'a erişebildiğini doğrulayın. Dışlama grubundaki kullanıcıların SharePoint Online 'a ve Exchange 'e herhangi bir cihazdan erişip erişebildiğini doğrulayın.
3. Ilkeyi etkinleştir 2: dışlama grubunda olmayan kullanıcıların, mobil cihazlarından SharePoint Online ve Exchange Online 'a alamıyor olduğunu doğrulayın. Dışlama grubundaki kullanıcıların, SharePoint 'e ve Exchange 'e herhangi bir cihazdan (Windows/iOS/Android) erişebileceğini doğrulayın.
4. Ilkeyi etkinleştir 3: dışlama gruplarında olmayan kullanıcıların, etki alanına katılmış bir makineyle bile SharePoint 'e ve Exchange 'e erişemez olduğunu doğrulayın. Dışlama grubundaki kullanıcıların SharePoint ve Exchange 'e herhangi bir ağdan erişebileceğini doğrulayın.
5. Ilkeyi etkinleştir 4: tüm kullanıcıların mobil cihazlardaki yerel posta uygulamalarından Exchange Online 'ı alamıyor olduğunu doğrulayın.
6. SharePoint Online ve Exchange Online için mevcut MFA ilkesini devre dışı bırakın.

Bu sonraki örnekte örnek **B-Salesforce 'a mobil erişime izin vermek için** bir iş uygulamasının erişimi geri yüklenir. Bu senaryoda, müşteri genellikle, mobil cihazlardan yalnızca uyumlu cihazlardan izin verilmesini sağlamak üzere satış çalışanlarının Salesforce (Azure AD ile çoklu oturum açma için yapılandırılmış) erişimine ihtiyaç duyar. Bu durumda, cihaz uyumluluğunu değerlendirmek için bir sorun olduğu ve kesinti, satış ekibinin anlaşmaları kapatmak için Salesforce 'a erişmesi gereken hassas bir süre içinde meydana gelir. Bu yedek ilkeler, kritik kullanıcılara bir mobil cihazdan Salesforce erişimi verir, böylece anlaşmaları kapatmaya ve işi kesintiye uğramayacak şekilde devam edebilirler. Bu örnekte, **Salesforceyedek** , erişimi sürdürmeniz gereken tüm satış çalışanlarını Içerir ve **salesadmins** 'nin gereken Salesforce yöneticileri vardır.

**Örnek B-yedek koşullu erişim ilkeleri:**

* İlke 1: Salesyedek takımda bulunmayan herkesi engelleyin
  * Ad: EM001-acıl durum: cihaz uyumluluğu kesintisi [1/2]-Salesforce-Salesforceyedek hariç tüm kullanıcıları engelle
  * Kullanıcılar ve gruplar: tüm kullanıcıları dahil et. SalesAdmins ve Salesforcedımı hariç tut
  * Bulut uygulamaları: Salesforce.
  * Koşullar: Yok
  * İzin denetimi: engelle
  * Durum: yalnızca rapor
* İlke 2: mobil dışındaki herhangi bir platformda Satış ekibini engelleyin (saldırı yüzeyini azaltmak için)
  * Ad: EM002-acıl ETKINLEŞTIRME: cihaz uyumluluğu kesintisi [2/2]-Salesforce-iOS ve Android dışındaki tüm platformları engelle
  * Kullanıcılar ve gruplar: Salesforcebir yedek Içerir. SalesAdmins hariç tut
  * Bulut uygulamaları: Salesforce
  * Koşullar: cihaz platformu tüm platformları Içerir, iOS ve Android 'i hariç tut
  * İzin denetimi: engelle
  * Durum: yalnızca rapor

Etkinleştirme sırası:

1. Satışsadmins ve Salesforce, Salesforce için mevcut cihaz uyumluluk ilkesinden dışlayın. Salesforceyedek grubundaki bir kullanıcının Salesforce 'a erişebileceğini doğrulayın.
2. Ilkeyi etkinleştir 1: Salespolicy dışındaki kullanıcıların Salesforce 'a erişemez olduğunu doğrulayın. SalesAdmins ve Salesforceyedek içindeki kullanıcıların Salesforce 'a erişebileceğini doğrulayın.
3. Ilkeyi etkinleştir 2: Salesyedek grubundaki kullanıcıların Salesforce Windows/Mac dizüstü bilgisayarlarıyla Salesforce 'a erişemediğinizi, ancak mobil cihazlarından erişmeye devam ettiğini doğrulayın. SalesAdmin 'in herhangi bir cihazdan Salesforce 'a erişmeye devam ettiğini doğrulayın.
4. Salesforce için mevcut cihaz Uyumluluk ilkesini devre dışı bırakın.

### <a name="contingencies-for-user-lockout-from-on-prem-resources-nps-extension"></a>Şirket içi kaynaklardan (NPS uzantısı) Kullanıcı kilitlemesi için kıdır

Azure AD MFA NPS uzantısını kullanarak VPN erişimini koruyorsanız, VPN çözümünüzü bir [SAML uygulaması](../manage-apps/view-applications-portal.md) olarak Federasyonu ve uygulama kategorisini aşağıda önerilen şekilde saptamayı düşünün. 

VPN ve Uzak Masaüstü Ağ Geçidi gibi şirket içi kaynakları korumak için Azure AD MFA NPS uzantısını dağıttıysanız, MFA ile bir acil durum durumunda MFA 'yı devre dışı bırakmak için hazır olduğunuzda önceden göz önünde bulundurmanız gerekir.

Bu durumda, NPS uzantısını devre dışı bırakabilirsiniz, sonuç olarak NPS sunucusu yalnızca birincil kimlik doğrulamasını doğrular ve kullanıcılara MFA 'yı zorlamayacaktır.

NPS uzantısını devre dışı bırak: 
-   HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\AuthSrv\Parameters kayıt defteri anahtarını bir yedekleme olarak dışarı aktarın. 
-   "AuthorizationDLLs" ve "ExtensionDLLs" kayıt defteri değerlerini parametreler anahtarından silin. 
-   Değişikliklerin etkili olması için ağ Ilkesi hizmeti (IAS) hizmetini yeniden başlatın 
-   VPN için birincil kimlik doğrulamanın başarılı olup olmadığını belirleme.

Hizmet kurtarıldıktan sonra kullanıcılarınıza MFA 'yı zorlamak için yeniden hazırsanız NPS uzantısını etkinleştirin: 
-   Kayıt defteri anahtarını yedekten içeri aktarma HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\AuthSrv\Parameters 
-   Değişikliklerin etkili olması için ağ Ilkesi hizmeti (IAS) hizmetini yeniden başlatın 
-   VPN için ikincil kimlik doğrulamasının yanı sıra birincil kimlik doğrulamanın başarılı olup olmadığını belirleme.
-   Acil durum penceresi sırasında hangi kullanıcıların oturum açdığını öğrenmek için NPS sunucusunu ve VPN günlüğünü gözden geçirin.

### <a name="deploy-password-hash-sync-even-if-you-are-federated-or-use-pass-through-authentication"></a>Şirket içinde doğrudan kimlik doğrulaması kullanıyor olsanız bile Parola karması eşitlemesini dağıtma

Aşağıdaki koşullar doğruysa Kullanıcı kilitleme de oluşabilir:

- Kuruluşunuz, geçişli kimlik doğrulama veya Federasyon ile karma kimlik çözümü kullanır.
- Şirket içi kimlik sistemleriniz (Active Directory, AD FS veya bağımlı bir bileşen) kullanılamaz. 
 
Kuruluşunuz, şirket içi kimlik sistemleriniz kapalıysa [Parola karması eşitlemesini kullanmaya geçiş](../hybrid/plan-connect-user-signin.md) yapmanızı sağladığından, [parola karma eşitlemesini etkinleştirmelidir](../hybrid/choose-ad-authn.md).

#### <a name="microsoft-recommendations"></a>Microsoft önerileri
 Kuruluşunuzun Federasyon veya geçişli kimlik doğrulaması kullandığından bağımsız olarak Azure AD Connect sihirbazını kullanarak parola karması eşitlemesini etkinleştirin.

>[!IMPORTANT]
> Kullanıcıları federe 'dan yönetilen kimlik doğrulamasına Parola karması eşitlemesini kullanacak şekilde dönüştürmek gerekli değildir.

## <a name="during-a-disruption"></a>Kesilme sırasında

Risk azaltma planını uygulamayı tercih ediyorsanız, tek bir erişim denetimi kesintisi yaşanmasını otomatik olarak sürdürbileceksiniz. Ancak, bir yedek plan oluşturmayı tercih ettiyseniz, erişim denetimi kesintisi sırasında acil durum ilkelerinizi etkinleştirebileceksiniz:

1. Belirli ağlardan hedeflenen kullanıcılara, belirli uygulamalara erişim izni veren acil durum ilkelerinizi etkinleştirin.
2. Düzenli denetim tabanlı ilkelerinizi devre dışı bırakın.

### <a name="microsoft-recommendations"></a>Microsoft önerileri

Bir kesinti sırasında hangi azaltmaları veya kıgenlerle kullanıldığına bağlı olarak, kuruluşunuz yalnızca parolalarla erişim izni verebilir. Hiçbir koruma, dikkatli bir şekilde ağırlıklı olması gereken önemli bir güvenlik riskidir. Kuruluşlar şunları sağlamalıdır:

1. Değişiklik denetimi stratejinizin bir parçası olarak, erişim denetimleri tam olarak çalışır duruma geldiğinde, uyguladığınız tüm kıtları geri almak için her değişikliği ve önceki durumu belgeleyin.
2. Kötü amaçlı aktörlerin, MFA 'yı devre dışı bıraktığınız sırada parola spreyi veya kimlik avı saldırıları aracılığıyla parola toplama girişiminde bulunduğunu varsayın Ayrıca, kötü aktörlerin bu pencere sırasında denenmeyen herhangi bir kaynağa daha önce erişim vermediği parolalara zaten sahip olması gerekebilir. Yöneticiler gibi kritik kullanıcılar için, MFA 'yı devre dışı bırakmadan önce parolalarını sıfırlayarak bu riski kısmen azaltabilirsiniz.
3. MFA 'nın devre dışı bırakıldığı zaman ne işe erişebileceğini belirlemek için tüm oturum açma etkinliklerini arşivleyin.
4. Bu pencere sırasında [raporlanan tüm risk algılamalarını önceliklendirme](../reports-monitoring/concept-sign-ins.md) .

## <a name="after-a-disruption"></a>Kesintiden sonra

Hizmet geri yüklendikten sonra, kesintiye uğramasından sonra, etkinleştirilen yedek planın bir parçası olarak yaptığınız değişiklikleri geri alın. 

1. Normal ilkeleri etkinleştir
2. Acil durum ilkelerinizi yalnızca rapor moduna geri devre dışı bırakın. 
3. Yaptığınız diğer değişiklikleri geri alın ve kesinti sırasında belgelenir.
4. Acil durum erişim hesabı kullandıysanız, kimlik bilgilerini yeniden oluşturmayı ve acil durum erişim hesabı yordamlarınızın bir parçası olarak yeni kimlik bilgileri ayrıntılarını fiziksel olarak güvenli hale getirmeyi unutmayın.
5. Şüpheli etkinlik kesintiye uğradıktan sonra [raporlanan tüm risk algılamalarını önceliklendirmeye](../reports-monitoring/concept-sign-ins.md) devam edin.
6. Bir kullanıcı kümesini hedeflemek için [PowerShell kullanılarak](/powershell/module/azuread/revoke-azureaduserallrefreshtoken) verilen tüm yenileme belirteçlerini iptal edin. Tüm yenileme belirteçleri iptal edildiğinde, kesinti sırasında kullanılan ayrıcalıklı hesaplar için önemlidir ve bunun yapılması, geri yüklenen ilkelerin denetimini yeniden kimlik doğrulaması ve karşılamaları için zorlayacaktır.

## <a name="emergency-options"></a>Acil durum seçenekleri

 Bir acil durum durumunda ve kuruluşunuz daha önce bir azaltma ya da acil durum planı uygulamadıysa, MFA 'yı zorlamak için koşullu erişim ilkelerini zaten kullanıyorsa, [Kullanıcı kilitleme bölümünün Kıgenine ilişkin](#contingencies-for-user-lockout) önerileri takip edin.
Kuruluşunuz Kullanıcı başına MFA eski ilkelerini kullanıyorsa, aşağıdaki alternatifi göz önünde bulundurun:

1. Şirket ağına giden IP adresiniz varsa, kimlik doğrulamasını yalnızca şirket ağına etkinleştirmek için güvenilir IP 'Ler olarak ekleyebilirsiniz.
   1. Giden IP adresi envanteriniz yoksa veya şirket ağı içinde ve dışında erişimi etkinleştirmek istiyorsanız 0.0.0.0/1 ve 128.0.0.0/1 belirterek tüm IPv4 adres alanını güvenilir IP 'Ler olarak ekleyebilirsiniz.

>[!IMPORTANT]
 > Erişimi engellemeyi kaldırmak için güvenilen IP adreslerini genişletirseniz, IP adresleriyle ilişkili risk algılamaları (örneğin, imkansız seyahat veya bilmediğiniz konumlar) oluşturulmaz.

>[!NOTE]
 > Azure AD MFA için [Güvenilen IP 'lerin](./howto-mfa-mfasettings.md) yapılandırılması yalnızca [Azure AD Premium lisanslarıyla](./concept-mfa-licensing.md)kullanılabilir.

## <a name="learn-more"></a>Daha fazla bilgi edinin

* [Azure AD Kimlik Doğrulaması Belgeleri](./howto-mfaserver-iis.md)
* [Azure AD 'de acil erişimli yönetim hesaplarını yönetme](../roles/security-emergency-access.md)
* [Azure Active Directory adlandırılmış konumları yapılandırma](../reports-monitoring/quickstart-configure-named-locations.md)
  * [Set-MsolDomainFederationSettings](/powershell/module/msonline/set-msoldomainfederationsettings)
* [Karma Azure Active Directory katılmış cihazları yapılandırma](../devices/hybrid-azuread-join-plan.md)
* [İş İçin Windows Hello Dağıtım Kılavuzu](/windows/security/identity-protection/hello-for-business/hello-deployment-guide)
  * [Parola Kılavuzu-Microsoft Research](https://research.microsoft.com/pubs/265143/microsoft_password_guidance.pdf)
* [Koşullu erişim Azure Active Directory koşullar nelerdir?](../conditional-access/concept-conditional-access-conditions.md)
* [Koşullu erişim Azure Active Directory erişim denetimleri nelerdir?](../conditional-access/controls.md)
* [Koşullu erişim yalnızca rapor modu nedir?](../conditional-access/concept-conditional-access-report-only.md)
