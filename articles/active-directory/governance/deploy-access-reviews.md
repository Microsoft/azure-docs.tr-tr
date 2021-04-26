---
title: Azure Active Directory erişim gözden geçirmeleri dağıtımını planlayın
description: Başarılı bir erişim gözden geçirmeleri dağıtımı için Planlama Kılavuzu
services: active-directory
documentationCenter: ''
author: BarbaraSelden
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 12/23/2020
ms.author: barclayn
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3af783d7ff8be36c63af871ab4f2d214ca9f9405
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532583"
---
# <a name="planning-azure-active-directory-access-reviews-deployment"></a>Azure Active Directory erişim gözden geçirmeleri dağıtımını planlama

[Azure Active Directory (Azure AD) erişim gözden geçirmeleri](access-reviews-overview.md) , kuruluşunuzun, [kaynak erişimi yaşam döngüsünü](identity-governance-overview.md)yöneterek ağı daha güvenli tutmasına yardımcı olur. Erişim gözden geçirmeleriyle şunları yapabilirsiniz:

* Uygulamalar ve gruplar gibi belirli kaynaklara kimlerin erişebileceğini görmek için düzenli İncelemeleri zamanlayın veya geçici incelemeler yapın

* Öngörüler, uyumluluk veya ilke nedenleri için incelemeleri izleyin

* Belirli Yöneticiler, iş sahipleri veya son kullanıcılara, sürekli erişim gereksinimini kendi başınıza test edebilen bir değerlendirme yetkisi verin

* Kullanıcıların erişime sahip olmaya devam etmesi gerekip gerekmediğini etkili bir şekilde anlamak için öngörüleri kullanın

* Kullanıcıların kaynaklara erişimini kaldırma gibi gözden geçirme sonuçlarını otomatikleştirin

  ![Erişim gözden geçirmeleri akışını gösteren diyagram.](./media/deploy-access-review/1-planning-review.png)

Erişim gözden geçirmeleri [Azure AD Identity Governance](identity-governance-overview.md) bir yetenektir. Diğer yetenekler, [Yetkilendirme Yönetimi](entitlement-management-overview.md), [Privileged Identity Management](../privileged-identity-management/pim-configure.md) ve [kullanım koşullardır](../conditional-access/terms-of-use.md). Bunlar birlikte, kuruluşların bu dört soruyu ele vermesini sağlarlar:

* Hangi kullanıcıların hangi kaynaklara erişimi olmalıdır?

* Bu erişimi hangi kullanıcılar yapıyor?

* Erişimi yönetmek için etkili bir kurumsal denetim var mı?

* Denetçilerin çalıştığını doğrulaması yapılabilir mi?

Kuruluşunuzdaki kullanıcılar için istediğiniz idare stratejinizi elde ettiğinizden emin olmak için, erişim gözden geçirmeleri dağıtımınızı planlamak gereklidir.

### <a name="key-benefits"></a>Önemli avantajlar

Erişim Incelemelerini etkinleştirmenin önemli avantajları şunlardır:

* **Işbirliği denetimi**. Erişim gözden geçirmeleri, kuruluşların, kullanıcılarının ihtiyaç duyduğu tüm kaynaklara erişimi yönetmesine olanak tanır. Kullanıcıları paylaşabilir ve işbirliği yaparken kuruluşlar, bilgilerin yalnızca yetkili kullanıcılar arasında olduğundan emin olabilir.

* **Riski yönetin**. Erişim gözden geçirmeleri, kuruluşlara, veri sızıntısı ve veri taşıması riskini azaltarak veri ve uygulamalara erişimi incelemenin bir yolunu sağlar. Bu, dış iş ortağının şirket kaynaklarına erişimini düzenli olarak gözden geçirme özelliklerini içerir. 

* **Adres uyumluluğu ve idare**. Erişim gözden geçirmeleri ile gruplar, uygulamalar ve siteler için erişim yaşam döngüsünü yönetebilir ve yeniden ayarlayabilirsiniz. Kuruluşunuza özgü uyumluluk veya riske duyarlı uygulamalar için izleme incelemelerini denetleyebilirsiniz. 

* **Maliyeti azaltın**. Erişim Incelemeleri bulutta yerleşiktir ve gruplar, uygulamalar ve erişim paketleri gibi bulut kaynaklarıyla yerel olarak çalışmaktadır. Erişim Incelemelerini kullanmak, kendi araçlarınızı oluşturmadan veya şirket içi araç takımınızı yükseltirken daha pahalı bir maliyetlidir.

### <a name="training-resources"></a>Eğitim kaynakları

Aşağıdaki videolar, erişim gözden geçirmeleri hakkında bilgi edindiklerinde yararlı olabilir:

* [Azure AD 'de erişim gözden geçirmeleri nelerdir?](https://youtu.be/kDRjQQ22Wkk)

* [Azure AD 'de erişim Incelemeleri oluşturma](https://youtu.be/6KB3TZ8Wi40)

* [Azure AD 'de erişim gözden geçirmeleri nasıl etkinleştirilir](https://youtu.be/X1SL2uubx9M)

* [Erişimimi kullanarak erişimi gözden geçirme](https://youtu.be/tIKdQhdHLXU)

### <a name="licenses"></a>Lisanslar

Genel Yöneticiler veya Kullanıcı yöneticileri dışında, erişim gözden geçirmeleri oluşturacak veya gerçekleştirecek olan her kişi için geçerli bir Azure AD Premium (P2) lisansınızın olması gerekir. Daha fazla bilgi için bkz. [erişim gözden geçirmeleri lisans gereksinimleri](access-reviews-overview.md).

Ayrıca, [Yetkilendirme yaşam döngüsü yönetimi](entitlement-management-overview.md) veya ayrıcalıklı kimlik yönetimi gibi diğer kimlik idare özelliklerine de ihtiyacınız olabilir. Bu durumda, ilgili lisanslara de ihtiyacınız olabilir. Daha fazla bilgi için bkz. [Azure Active Directory fiyatlandırması](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="plan-the-access-reviews-deployment-project"></a>Erişim gözden geçirmeleri dağıtım projesini planlayın

Ortamınızdaki erişim Incelemelerini dağıtmaya yönelik stratejiyi öğrenmek için kuruluş ihtiyaçlarınızı göz önünde bulundurun.

### <a name="engage-the-right-stakeholders"></a>Doğru paydaşlara katılın

Teknoloji projeleri başarısız olduğunda, genellikle etki, sonuç ve sorumlulukların eşleşmeyen beklentileri nedeniyle bu, genellikle bu şekilde yapılır. Bu sınırları önlemek için, doğru paydaşları ve proje rollerinin açık [olduğundan emin olun](../fundamentals/active-directory-deployment-plans.md) .

Erişim gözden geçirmeleriyle, büyük olasılıkla kuruluşunuzun içindeki aşağıdaki ekiplerden temsilcileriniz olabilir:

* BT **YÖNETIMI** BT altyapınızı yönetir ve bulut yatırımlarınızı ve hizmet olarak yazılım (SaaS) uygulamalarınızı yönetir. Bu takım şunları olacak:

   * Microsoft 365 ve Azure AD dahil altyapı ve uygulamalara ayrıcalıklı erişimi gözden geçirin.

   * Güncel Erişim listelerini korumak için özel durum listeleri veya BT pilot projelerini korumak için kullanılan gruplar üzerinde erişim gözden geçirmeleri zamanlayın ve çalıştırın.

   * Hizmet sorumluları aracılığıyla kaynaklara programlı (betikleştirilmiş) erişimin yönetilecektir ve incelendiğinden emin olun.

* **Geliştirme ekipleri** , kuruluşunuz için uygulamalar oluşturup bakımını sürdürmektedir. Bu takım şunları olacak:

   * SaaS, PaaS ve IaaS kaynaklarında, geliştirilmiş çözümleri oluşturan bileşenlere kimlerin erişebileceğini ve bunları yönetebileceğini denetleyin.

   * İç uygulama geliştirmesi için uygulamalara ve araçlara erişebilen grupları yönetin.

   * Müşterileriniz için barındırılan üretim yazılımlarına veya çözümlerine erişimi olan ayrıcalıklı kimlikler iste

* **İş birimleri** projeleri ve kendi uygulamalarını yönetir. Bu takım şunları olacak: 

   * İç ve dış kullanıcılar için gruplara ve uygulamalara erişimi gözden geçirin ve onaylayın ya da reddedin.

   * Çalışanların ve iş ortakları gibi dış kimliklerin sürekli erişimini kanıtlayın ve gözden geçirme yapın.

* **Kurumsal idare** , kuruluşun iç ilke takip edilmesini ve yönetmeliklerle uyumlu olmasını sağlar. Bu takım şunları olacak:

   * Yeni erişim gözden geçirmeleri isteyin veya zamanlayın.

   * Belge ve kayıt uyumluluğun tutulması dahil olmak üzere, erişimi gözden geçirmeye yönelik işlemleri ve yordamları değerlendirin.

   * En kritik kaynakların geçmiş gözden geçirmeleriyle ilgili sonuçları gözden geçirin.

> [!NOTE]
> El ile değerlendirme gerektiren incelemeler için, uygun gözden geçirenleri planlayın ve ilke ve uyumluluk gereksinimlerinizi karşılayan döngüleri gözden geçirin. Gözden geçirme döngüleri çok sık olursa veya çok az sayıda gözden geçiren varsa, kalite kaybolabilir ve çok fazla veya çok az sayıda kişi erişebiliyor olabilir. 

### <a name="plan-communications"></a>İletişimi planlama

İletişim, tüm yeni iş sürecinin başarısı için önemlidir. Deneyimlerine nasıl ve ne zaman değişdikleri ve sorunlar yaşandıklarında nasıl destek kazanabilecekleri hakkında daha fazla iletişim kurun. 

#### <a name="communicate-changes-in-accountability"></a>Sorumlulubir şekilde değişiklikleri iletişim kurun

Erişim gözden geçirmeleri, gözden geçirme ve iş sahiplerine çalışmaya devam etme sorumluluğunu destekler. BT sürücülerinden erişim kararlarını daha doğru erişim kararlarından ayırın. Bu, kaynak sahibinin sorumluluğunda ve sorumluluğunda kültürel bir değişiklik. Bu değişikliği proaktif olarak iletmekte ve kaynak sahiplerinin eğitim aldığından ve iyi kararlar almak için öngörüleri kullanabildiğinden emin olun.

Açık olarak, altyapıyla ilgili tüm erişim kararlarının ve ayrıcalıklı rol atamalarının denetiminde kalmak gerekecektir. 

#### <a name="customize-email-communication"></a>E-posta iletişimini Özelleştir

Bir gözden geçirme zamanladığınızda, bu incelemeyi gerçekleştirecek kullanıcıları aday olarak görürsünüz. Bu gözden geçirenler, bunlara atanan yeni incelemeler için bir e-posta bildirimi ve bunlara atanan bir incelemenin süresi dolmadan önce anımsatıcılar alır.

Yöneticiler, gözden geçirme süresi dolmadan veya geçerlilik süresi dolmadan önce bu bildirimi yarı bir şekilde göndermek için seçim yapabilir. 

Gözden geçirenlere gönderilen e-posta, gözden geçirme üzerinde işlem yapması için teşvik eden özel bir kısa mesaj içerecek şekilde özelleştirilebilir. Şunları yapmak için ek metin kullanmanızı öneririz:

* Gözden geçirenlere kişisel bir ileti ekleyerek uyumluluk veya BT departmanınız tarafından gönderildiğini anlayabilirler.

* İncelemenin beklentilerini ve ek başvuru ya da eğitim malzemelerini içeren dahili bilgilere bir köprü veya başvuru ekleyin.

* [Kendi kendine erişimi nasıl gerçekleştireceğiniz](review-your-access.md) hakkında yönergeler için bir bağlantı ekleyin. 

  ![Gözden geçiren e-postası](./media/deploy-access-review/2-plan-reviewer-email.png)

İncelemeye başla ' yı seçtikten sonra, gözden geçirenler grup ve uygulama erişimi Incelemeleri için [Myaccess portalına](https://myapplications.microsoft.com/) yönlendirilir. Portal, gözden geçirdikleri kaynağa erişimi olan tüm kullanıcılara genel bakış ve son oturum açma ve erişim bilgilerine göre sistem önerilerini sağlar.

### <a name="plan-a-pilot"></a>Pilot planı planlayın

Müşterilerin ilk olarak küçük bir grupla ve kritik olmayan kaynakları hedeflemesini önermelerini öneririz. Piloting, gerektiğinde işlem ve iletişimleri ayarlamanıza, kullanıcıların ve gözden geçirenlerin güvenlik ve uyumluluk gereksinimlerini karşılamasına yardımcı olabilir.

Pilot ortamınızda şunları öneririz:

* Sonuçların otomatik olarak uygulanamadığını gözden geçirmeler ile başlayın ve etkileri kontrol edebilirsiniz.

* Tüm kullanıcıların Azure AD 'de geçerli bir e-posta adresi olduğundan emin olun ve uygun eylemi gerçekleştirmek için e-posta iletişimi alırlar. 

* Hızlı bir şekilde geri yüklemeniz gerektiğinde, pilot 'ın bir parçası olarak kaldırılan tüm erişimi belgeleyin.

* Tüm olayların düzgün denetlendiğinden emin olmak için Denetim günlüklerini izleyin.

Daha fazla bilgi için bkz. [pilot için en iyi uygulamalar](../fundamentals/active-directory-deployment-plans.md).

## <a name="introduction-to-access-reviews"></a>Erişim Incelemelerine giriş

Bu bölüm, gözden geçirdiklerinizi planlamadan önce bilmeniz gereken erişim gözden geçirme kavramlarını tanıtır.

### <a name="what-resource-types-can-be-reviewed"></a>Hangi kaynak türleri incelenebilir?

Kuruluşunuzun kaynaklarını Azure AD ile tümleştirdikten sonra (örneğin, kullanıcılar, uygulamalar ve gruplar), yönetilebilecek ve incelenebilir. 

İnceleme için tipik hedefler şunlardır:

* [Çoklu oturum açma Için Azure AD ile tümleştirilmiş uygulamalar](../manage-apps/what-is-application-management.md) (SaaS, iş kolu gibi).

* Grup [üyeliği](../fundamentals/active-directory-manage-groups.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context) (Azure AD ile eşitlenmiş veya Microsoft ekipleri dahil olmak üzere Azure AD 'de veya Microsoft 365 ile oluşturulmuş).

* Kaynakları (gruplar, uygulamalar ve siteler), erişimi yönetmek için tek bir pakete gruplayan [erişim paketi](./entitlement-management-overview.md) .

* Privileged Identity Management ' de tanımlanan [Azure AD rolleri ve Azure Kaynak rolleri](../privileged-identity-management/pim-resource-roles-assign-roles.md) .

### <a name="who-will-create-and-manage-access-reviews"></a>Erişim Incelemelerini kim oluşturacak ve yönetecektir?

Erişim Incelemesi oluşturmak, yönetmek veya okumak için gereken yönetim rolü, incelenmekte olan kaynağın türüne bağlıdır. Aşağıdaki tablo her kaynak türü için gereken rolleri gösterir:

| Kaynak türü| Erişim Incelemeleri oluşturma ve yönetme (oluşturucular)| Okuma erişimi gözden geçirme sonuçları |
| - | - | -|
| Grup veya uygulama| Genel Yönetici <p>Kullanıcı Yöneticisi| Oluşturucular ve Güvenlik Yöneticisi |
| Azure AD 'de ayrıcalıklı roller| Genel Yönetici <p>Ayrıcalıklı rol yöneticisi| Oluşturucular <p>Güvenlik okuyucusu<p>Güvenlik Yöneticisi |
| Azure 'da ayrıcalıklı roller (kaynaklar)| Genel Yönetici<p>Kullanıcı Yöneticisi<p>Kaynak sahibi| Oluşturucular |
| Erişim paketi| Genel Yönetici<p>Erişim paketi Oluşturucusu| Yalnızca genel yönetici |


Daha fazla bilgi için [Azure Active Directory Içindeki yönetici rolü izinleri](../roles/permissions-reference.md)bölümüne bakın.

### <a name="who-will-review-the-access-to-the-resource"></a>Kaynağa erişimi kim olarak gözden geçilecektir?

Erişim gözden geçirmesi Oluşturucusu, gözden geçirmeyi gerçekleştirecek olan oluşturma sırasında karar verir. İnceleme başladıktan sonra bu ayar değiştirilemez. Gözden geçirenler üç kişilik olarak temsil edilir:

* Kaynağın iş sahipleri olan kaynak sahipleri.

* Erişim Incelemeleri Yöneticisi tarafından seçilen ayrı ayrı seçili temsilciler kümesi.

* Son kullanıcılar, her kendi kendine atmaya sürekli erişim ihtiyacı olan son kullanıcılar.

Yönetici, bir erişim gözden geçirmesi oluştururken bir veya daha fazla gözden geçiren seçebilir. Tüm gözden geçirenler bir inceleme başlatabilir ve gerçekleştirebilir, kullanıcılar bir kaynağa erişmeye devam edebilir veya onları kaldırabilir. 

### <a name="components-of-an-access-review"></a>Erişim gözden geçirmesi bileşenleri

Erişim gözden Geçirlerinizi uygulamadan önce, kuruluşunuzla ilgili gözden geçirme türlerini planlamanız gerekir. Bunu yapmak için, gözden geçirmek istediğiniz işlem ve bu İncelemeleri temel alan eylemler hakkında iş kararları almanız gerekir.

Bir erişim gözden geçirme ilkesi oluşturmak için aşağıdaki bilgilere sahip olmanız gerekir.

* Gözden geçirilecek kaynaklar nelerdir?

* Erişimi incelenmekte olan.

* İnceleme ne sıklıkta oluşur?

* İncelemeyi kim gerçekleştirecek?

   * Nasıl incelenmek üzere bildirim alınacaktır?

   * Gözden geçirme için uygulanacak zaman çizelgeleri nelerdir?

* İncelemeye göre hangi otomatik eylemler zorunlu kılınmalıdır?

   * Gözden geçiren zaman yanıt vermezse ne olur?

* İnceleme temelinde ne el ile gerçekleştirilen eylemler elde edilecek?

* Gerçekleştirilen eylemlere göre hangi iletişimler gönderilir?


**Örnek erişim gözden geçirme planı**

| Bileşen| Değer |
| - | - |
| **Gözden geçirilecek kaynaklar**| Microsoft Dynamics 'e erişim |
| **İnceleme sıklığı**| Aylık |
| **Kim tarafından incelenmektedir**| Dynamics iş grubu program yöneticileri |
| **Bildirim**| Diğer ada gözden geçirmesinden önce 24 saat e-posta Dynamics-Pms<p>Teşvik özel iletisini, satın allarını güvenli hale getirmek için gözden geçirenlere ekleyin |
| **Zaman çizelgesi**| bildirimden 48 saat |
|**Otomatik eylemler**| Kullanıcıları güvenlik grubundan Dynamics-Access ' ten kaldırarak 90 gün içinde etkileşimli oturum açma olmayan herhangi bir hesaptan erişimi kaldırın. <p>*Zaman çizelgesi içinde incelenmeyen eylemler gerçekleştirin.* |
| **El ile gerçekleştirilen eylemler**| Gözden geçirenler, istenirse otomatik eylem öncesinde kaldırma onayı gerçekleştirebilir. |
| **İletişimler**| Kaldırılmış olan bir e-postayı kaldıran iç (üye) kullanıcılar gönderme ve yeniden erişim elde etme. |


 

### <a name="automate-actions-based-on-access-reviews"></a>Erişim gözden geçirmeleri temelinde eylemleri otomatikleştirin

Otomatik olarak sonuçları kaynak olarak Uygula seçeneğini ayarlayarak, erişim kaldırma işlemini otomatik hale getirebilirsiniz.

  ![Erişim gözden geçirmeleri planlama](./media/deploy-access-review/3-automate-actions-settings.png)

İnceleme tamamlandıktan ve sona erdikten sonra, gözden geçiren tarafından onaylanmamış kullanıcılar otomatik olarak kaynaktan kaldırılır veya devam eden erişimle saklanır. Bu, kendi grup üyeliklerini, uygulama atamasını kaldırmak veya ayrıcalıklarını ayrıcalıklı bir role yükseltmek için hakkını iptal etmek anlamına gelir.

Öneriler alın

Öneriler, gözden geçiren deneyimin bir parçası olarak gözden geçirenlere görüntülenir ve bir kişinin kiracının son oturum açmasını veya bir uygulamaya son erişimi gösterir. Bu bilgiler, gözden geçirenlerin doğru erişim kararına sahip olmasına yardımcı olur. Öneri al seçeneğinin belirlenmesi, erişim gözden geçirmesi 'nın önerilerini izlemelidir. Erişim Incelemesinin sonunda, sistem, gözden geçirenlerin yanıtladığı kullanıcılar için bu önerileri otomatik olarak uygular.

Öneriler, erişim incelemesinin ölçütlerine göre yapılır. Örneğin, 90 gün boyunca etkileşimli oturum açma olmadan erişimi kaldırmak için incelemeyi yapılandırırsanız, bu ölçüte uyan tüm kullanıcıların kaldırılmasını öneririz. Microsoft, önerileri geliştirmeyle sürekli olarak çalışmaktadır. 

### <a name="review-guest-user-access"></a>Konuk Kullanıcı erişimini gözden geçirme

Dış kuruluşlardan işbirliği iş ortaklarının kimliklerini gözden geçirmek ve temizlemek için erişim Incelemelerini kullanın. İş ortağı başına İnceleme yapılandırması, uyumluluk gereksinimlerini karşılayamaz.

Dış kimliklere aşağıdaki eylemlerden biri aracılığıyla şirket kaynaklarına erişim verilebilir:

* Gruba eklendi 

* Takımlara davet edildi 

* Kurumsal uygulama veya erişim paketine atandı

* Azure AD 'de veya bir Azure aboneliğinde ayrıcalıklı bir rol atandı

Bkz. [örnek komut dosyası](https://github.com/microsoft/access-reviews-samples/tree/master/ExternalIdentityUse). Betik, kiracıya davet edilen dış kimliklerin kullanıldığını gösterir. Azure AD 'de dış kullanıcının grup üyeliğini, rol atamalarını ve uygulama atamalarını görebilirsiniz. Betik, Azure AD dışında herhangi bir atamayı göstermez, örneğin, SharePoint kaynaklarına doğrudan hak ataması, grupların kullanılmadan kullanılamaz.

Gruplar veya uygulamalar için bir erişim Incelemesi oluştururken, gözden geçirenin yalnızca erişim izni olan herkese veya yalnızca Konuk kullanıcılara odaklanmasına izin verebilirsiniz. Yalnızca Konuk kullanıcılar ' ı seçerek, gözden geçirenlere kaynağa erişimi olan Azure AD B2B 'dan gelen dış kimliklerin odaklanmış bir listesi verilir.

 ![Konuk kullanıcıları gözden geçirme](./media/deploy-access-review/4-review-guest-users-admin-ui.png)

> [!IMPORTANT]
> Bu, userType üyesi olan dış üyeleri içermez. Bu, Azure AD B2B işbirliğinin dışında davet edilen kullanıcıların yanı sıra, paylaşılan içeriğe doğrudan SharePoint aracılığıyla erişebilen kullanıcılar da dahil değildir.

## <a name="plan-access-reviews-for-access-packages"></a>Erişim paketleri için erişim Incelemeleri planlayın

[Erişim paketleri](entitlement-management-overview.md) , Idare ve erişim gözden geçirme stratejinizi büyük ölçüde kolaylaştırabilir. Erişim paketi, kullanıcının bir proje üzerinde çalışması veya görevlerini gerçekleştirmesi için gereken erişime sahip tüm kaynakların bir paketidir. Örneğin, kuruluşunuzdaki geliştiricilerin gerek duyduğu tüm uygulamaları veya dış kullanıcıların erişimi olması gereken tüm uygulamaları içeren bir erişim paketi oluşturmak isteyebilirsiniz. Yönetici veya temsilci erişimi paketi Yöneticisi, kaynakları (gruplar veya uygulamalar) ve kullanıcıların bu kaynaklar için ihtiyaç duyduğu rolleri gruplandırır.

[Bir erişim paketi oluştururken](entitlement-management-access-package-create.md), kullanıcıların erişim paketi isteyebilecekleri koşulları, onay işleminin ne gibi göründüğünü ve bir kişinin ne sıklıkta erişim istemesi gerektiğini belirleyen bir veya daha fazla erişim ilkesi oluşturabilirsiniz. Erişim gözden geçirmeleri, bir erişim paketi ilkesi oluştururken veya düzenlenirken yapılandırılır.

Erişim gözden geçirmeleri için aşağı kaydırmak üzere yaşam döngüsü sekmesini açın.

 !["Yaşam döngüsü" sekmesinde "ilkeyi düzenleme" gösteren ekran görüntüsü.](./media/deploy-access-review/5-plan-access-packages-admin-ui.png)

## <a name="plan-access-reviews-for-groups"></a>Gruplar için erişim gözden geçirmeleri planlayın

Erişim paketlerinin yanı sıra, Grup üyeliğini gözden geçirmek, erişimi yönetirken en etkili yoldur. Kaynaklara erişimin [güvenlik grupları veya Microsoft 365 grupları](../fundamentals/active-directory-manage-groups.md)aracılığıyla atanmasını ve kullanıcıların erişim kazanmak için bu gruplara eklenmesini öneririz.

Tek bir gruba, tüm uygun kaynaklara erişim verilebilir. Grup erişimini tek tek kaynaklara veya uygulamaları ve diğer kaynakları gruplandıran bir erişim paketine atayabilirsiniz. Bu yöntemde, her uygulamaya bir bireyin olmak yerine gruba erişimi inceleyebilirsiniz. 

Grup üyeliği şu şekilde incelenebilir: 

* Yöneticiler

* Grup sahipleri

* Seçili kullanıcılar, gözden geçirme oluşturulduğunda değerlendirme özelliği verildi

* Grubun üyeleri, kendileri için attest

### <a name="group-ownership"></a>Grup sahipliği

Kimin erişmesi gerektiğini bilmemiz için en iyi şekilde, Grup sahiplerinin üyeliği incelemesini öneririz. Grupların sahipliği, Grup türüyle farklılık gösterir:

Microsoft 365 ve Azure AD 'de oluşturulan grupların bir veya daha fazla iyi tanımlanmış sahibi vardır. Çoğu durumda, bu sahipler kimin erişimi olması gerektiğini öğrendikleri için kendi grupları için kusursuz gözden geçirenler yapar. 

Örneğin, Microsoft ekipleri kullanıcılara SharePoint, Exchange, OneNote veya diğer Microsoft 365 hizmetlerindeki kaynaklara erişim izni vermek için temel yetkilendirme modeli olarak Microsoft 365 gruplarını kullanır. Ekibin Oluşturucusu otomatik olarak bir sahip olur ve bu grubun üyeliklerine karşı test edilmesinden sorumludur. 

Azure AD portalında veya Microsoft Graph aracılığıyla betik aracılığıyla el ile oluşturulan grupların sahip olması gerekmez. Bunları grubun "sahipler" bölümünde veya Graph aracılığıyla Azure AD portalı üzerinden tanımlamanızı öneririz.

Şirket içi Active Directory eşitlenen grupların Azure AD 'de sahibi olamaz. Bunlar için bir erişim Incelemesi oluştururken, bu kullanıcılara üye karar vermek için en uygun kişileri seçmelisiniz.

> [!NOTE]
> Grup sahipliğini ve üyeliğin düzenli olarak incelenmesi için sorumlulukları belirlemek üzere grupların nasıl oluşturulduğunu tanımlayan iş ilkeleri tanımlamayı öneririz. 

### <a name="review-membership-of-exclusion-groups-in-conditional-access-policies"></a>Koşullu erişim ilkelerinde dışlama gruplarının üyeliğini gözden geçirme 

Ağınızı güvenli tutmak için tasarlanan koşullu erişim ilkelerinin tüm kullanıcılar için uygulanmaması durumunda bazı zamanlar vardır. Örneğin, kurumsal ağda yalnızca kullanıcıların oturum açmasına izin veren bir koşullu erişim ilkesi, yaygın olarak taşınan satış ekibine uygulanmayabilir. Bu durumda, satış ekibi üyeleri bir gruba konur ve bu grup koşullu erişim ilkesinden dışlanır. 

Yanlış Üyeler gereksinimden dışlanıyorsa, dışlama potansiyel bir riski temsil ettiğinden, bu tür bir grup üyeliğini düzenli olarak gözden geçirin.

[Koşullu erişim ilkelerinden dışlanan kullanıcıları yönetmek Için Azure AD erişim gözden geçirmeleri](conditional-access-exclusion.md)' nı kullanabilirsiniz.

### <a name="review-external-users-group-memberships"></a>Dış kullanıcının grup üyeliklerini gözden geçirme

El ile çalışmayı ve ilgili olası hataları en aza indirmek için, bir kullanıcının özniteliklerine göre grup üyeliği atamak üzere [dinamik grupları](../enterprise-users/groups-create-rule.md) kullanmayı düşünün. Dış kullanıcılar için bir veya daha fazla dinamik grup oluşturmak isteyebilirsiniz. Dahili sponsor, grupta üyelik için bir gözden geçiren işlevi görebilir. 

Note: bir erişim Incelemesinin sonucu olarak bir gruptan çıkarılan dış kullanıcılar kiracıdan silinmez. 

El ile veya bir komut dosyası aracılığıyla kaldırılan bir kiracının silinebilirler.

### <a name="review-access-to-on-premises-groups"></a>Şirket içi gruplara erişimi gözden geçirme

Erişim gözden geçirmeleri, şirket içinde [Azure AD Connect](../hybrid/whatis-azure-ad-connect.md)ile eşitlediğiniz grupların grup üyeliklerini değiştiremez. Bunun nedeni, yetkilinin kaynağının şirket içinde olması.

Şirket içi grupların düzenli olarak gözden geçirmeleri planlamak ve sürdürmek için erişim Incelemelerini kullanmaya devam edebilirsiniz. Gözden geçirenler, daha sonra şirket içi grupta işlem görür. Bu strateji, tüm incelemeler için araç olarak erişim Incelemelerini korur.

Şirket içi gruplardaki bir erişim Incelemesinin sonuçlarını kullanabilir ve şunları yaparak daha fazla işlem yapabilirsiniz:

* Erişim gözden geçirmesi ve el ile eylemde CSV raporu indiriliyor.

* Tamamlanan erişim Incelemelerdeki sonuçlara ve kararlara programlı bir şekilde erişmek için Microsoft Graph kullanma.

Örneğin, Windows AD ile yönetilen bir grup için sonuçlara erişmek üzere bu [PowerShell örnek betiğini](https://github.com/microsoft/access-reviews-samples/tree/master/AzureADAccessReviewsOnPremises)kullanın. Betik, gerekli grafik çağrılarını özetler ve değişiklikleri yürütmek için Windows AD-PowerShell komutlarını dışarı aktarır.

## <a name="plan-access-reviews-for-applications"></a>Uygulamalar için erişim İncelemeleri planlayın 

Bir uygulamaya erişimi gözden geçirirken, çalışanların ve dış kimliklerin erişimini uygulamanın içindeki bilgi ve verilere göre gözden geçiyorsunuz. Erişim paketi veya Grup yerine belirli bir uygulamaya kimlerin erişebileceğini bilmeniz gerektiğinde bir uygulamayı gözden geçirmeyi seçin. 

Aşağıdaki senaryolarda uygulamalar için plan İncelemeleri yapmanızı öneririz:

* Kullanıcılara uygulamaya doğrudan erişim verilir (bir grup veya erişim paketinin dışında).

* Uygulama, kritik veya hassas bilgileri kullanıma sunar.

* Uygulamanın, test etmeniz gereken belirli uyumluluk gereksinimleri vardır.

* Uygunsuz erişimi kuşkulu.

Bir uygulamaya yönelik erişim gözden geçirmeleri oluşturmak için, Kullanıcı atamasını gerekli olarak belirleyin. özelliği Evet. Hayır olarak ayarlanırsa, dizininizdeki dış kimlikler dahil tüm kullanıcılar uygulamaya erişebilir ve uygulamaya erişimi gözden geçiremiyoruz. 

 ![Uygulama atamalarını planlayın](./media/deploy-access-review/6-plan-applications-assignment-required.png)

Daha sonra, erişimi olmasını istediğiniz [kullanıcıları ve grupları atamanız](../manage-apps/assign-user-or-group-access-portal.md) gerekir. 

### <a name="reviewers-for-an-application"></a>Bir uygulama için gözden geçirenler

Erişim incelemeleri bir grubun üyeleri veya bir uygulamaya atanmış kullanıcılar için olabilir. Azure AD 'deki uygulamaların sahibi olması gerekmez, bu nedenle uygulama sahibini gözden geçiren olarak seçme seçeneği mümkün değildir. Tüm erişimi gözden geçirmek yerine yalnızca uygulamaya atanan Konuk kullanıcıları gözden geçirmek için bir gözden geçirmeyi daha ayrıntılı bir şekilde tanımlayabilirsiniz.

## <a name="plan-review-of-azure-ad-and-azure-resource-roles"></a>Azure AD ve Azure Kaynak rollerinin incelenmesi Planlanın

[Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md) , KURULUŞLARıN Azure AD 'de kaynaklara ayrıcalıklı erişimi nasıl yöneteceğini basitleştirir. Bu, hem [Azure AD](../roles/permissions-reference.md) hem de [Azure kaynakları](../../role-based-access-control/built-in-roles.md) ' nda ayrıcalıklı rollerin listesini korur ve dizinin genel güvenliğini artırır.

Erişim gözden geçirmeleri, gözden geçirenlerin kullanıcıların hala bir rolde olması gerekip gerekmediğini kanıtlarına izin verir. Erişim paketlerine yönelik erişim gözden geçirmeleri gibi, Azure AD rolleri ve Azure kaynağı için yapılan incelemeler, PıM yönetici kullanıcı deneyimiyle tümleşiktir. Aşağıdaki rol atamalarını düzenli olarak gözden geçirmenizi öneririz:

* Genel Yönetici

* Kullanıcı Yöneticisi

* Ayrıcalıklı kimlik doğrulama Yöneticisi

* Koşullu Erişim Yöneticisi

* Güvenlik Yöneticisi

* Tüm Microsoft 365 ve Dynamics Service yönetim rolleri

Burada seçilen roller kalıcı ve uygun rolü içerir. 

Gözden geçirenler bölümünde, tüm kullanıcıları gözden geçirmek için bir veya daha fazla kişi seçin. Ya da üyelerin kendi erişimini incelemesini seçebilirsiniz.

 ![İlkeyi düzenleme](./media/deploy-access-review/7-plan-azure-resources-reviewers-selection.png)

## <a name="deploy-access-reviews"></a>Erişim gözden geçirmeleri dağıtma

Bir strateji ve Azure AD ile tümleştirilmiş kaynaklara erişimi gözden geçirmeyi planladıktan sonra aşağıdaki kaynakları kullanarak İncelemeleri dağıtın ve yönetin.

### <a name="review-access-packages"></a>Erişim paketlerini gözden geçirme

Yöneticiler, eski erişim riskini azaltmak için bir erişim paketine etkin atamaları olan kullanıcıların düzenli olarak incelemelerine olanak sağlayabilir. Aşağıdaki bağlantıdaki yönergeleri izleyin:

| Nasıl yapılır makaleleri| Açıklama |
| - | - |
| [Erişim Incelemeleri oluşturma](entitlement-management-access-reviews-create.md)| Erişim paketi incelemelerini etkinleştirin. |
| [Erişim gözden geçirmeleri gerçekleştirin](entitlement-management-access-reviews-review-access.md)| Erişim paketine atanan diğer kullanıcılar için erişim gözden geçirmeleri gerçekleştirin. |
| [Atanan erişim paketlerini kendi kendine gözden geçir](entitlement-management-access-reviews-self-review.md)| Atanan erişim paketlerini kendi kendine gözden geçirme |


> [!NOTE]
> Kendi kendini gözden geçiren ve bu kullanıcılara artık erişim ihtiyacı olmayan son kullanıcılar, erişim paketinden doğrudan kaldırılmaz. İnceleme sona erdiğinde veya bir yönetici gözden geçirmeyi durdurduklarında erişim paketinden kaldırılır.

### <a name="review-groups-and-apps"></a>Grupları ve uygulamaları gözden geçirme

Çalışanlar ve konuklar için gruplara ve uygulamalara erişim ihtiyacı büyük olasılıkla zaman içinde değişir. Yöneticiler, eski erişim atamalarıyla ilişkili riski azaltmak için Grup üyeleri veya uygulama erişimi için erişim İncelemeleri oluşturabilir. Aşağıdaki bağlantıdaki yönergeleri izleyin:

| Nasıl yapılır makaleleri| Açıklama |
| - | - |
| [Erişim Incelemeleri oluşturma](create-access-review.md)| Grup üyeleri veya uygulama erişimi için bir veya daha fazla erişim incelemesi oluşturun. |
| [Erişim gözden geçirmeleri gerçekleştirin](perform-access-review.md)| Bir grubun veya bir uygulamaya erişimi olan kullanıcıların üyeleri için erişim gözden geçirmesi gerçekleştirin. |
| [Erişiminizi kendi kendinize gözden geçirin](review-your-access.md)| Üyeler bir gruba veya uygulamaya kendi erişimini gözden geçirir |
| [Erişim Incelemesini Tamam](complete-access-review.md)| Erişim gözden geçirmesini görüntüleme ve sonuçları uygulama |
| [Şirket içi gruplar için işlem yapın](https://github.com/microsoft/access-reviews-samples/tree/master/AzureADAccessReviewsOnPremises)| Şirket içi gruplar için erişim gözden geçirmeleri üzerinde işlem yapmak için örnek PowerShell betiği. |


### <a name="review-azure-ad-roles"></a>Azure AD rollerini gözden geçirme

Eski rol atamalarıyla ilişkili riski azaltmak için, ayrıcalıklı Azure AD rollerinin erişimini düzenli olarak gözden geçirmeniz gerekir.

![Azure A D rollerinin "üyeliği gözden geçirme" listesini gösteren ekran görüntüsü.](./media/deploy-access-review/8-review-azure-ad-roles-picker.png)

Aşağıdaki bağlantılarda yer alan yönergeleri izleyin:

| Nasıl yapılır makaleleri | Açıklama |
| - | - |
 [Erişim Incelemeleri oluşturma](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| PıM 'de ayrıcalıklı Azure AD rolleri için erişim incelemeleri oluşturma |
| [Erişiminizi kendi kendinize gözden geçirin](../privileged-identity-management/pim-how-to-perform-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| Bir Yönetim rolüne atandıysanız, rolünüze erişimi onaylayın veya reddedin |
| [Erişim gözden geçirmesini tamamlar](../privileged-identity-management/pim-how-to-complete-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| Erişim gözden geçirmesini görüntüleme ve sonuçları uygulama |


### <a name="review-azure-resource-roles"></a>Azure Kaynak rollerini gözden geçirme

Eski rol atamalarıyla ilişkili riski azaltmak için, ayrıcalıklı Azure Kaynak rollerinin erişimini düzenli olarak gözden geçirmeniz gerekir. 

![Azure AD rollerini gözden geçirme](./media/deploy-access-review/9-review-azure-roles-picker.png)

Aşağıdaki bağlantılarda yer alan yönergeleri izleyin:

| Nasıl yapılır makaleleri| Açıklama |
| - | -|
| [Erişim Incelemeleri oluşturma](../privileged-identity-management/pim-resource-roles-start-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| PıM 'de ayrıcalıklı Azure Kaynak rolleri için erişim gözden geçirmeleri oluşturma |
| [Erişiminizi kendi kendinize gözden geçirin](../privileged-identity-management/pim-resource-roles-perform-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| Bir Yönetim rolüne atandıysanız, rolünüze erişimi onaylayın veya reddedin |
| [Erişim gözden geçirmesini tamamlar](../privileged-identity-management/pim-resource-roles-complete-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| Erişim gözden geçirmesini görüntüleme ve sonuçları uygulama |


## <a name="use-the-access-reviews-api"></a>Erişim gözden geçirmeleri API 'sini kullanma

Yeniden görüntülenebilen kaynaklarla etkileşim kurmak ve bunları yönetmek için bkz. [Graph API yöntemleri](/graph/api/resources/accessreviewsv2-root?view=graph-rest-beta&preserve-view=true) ve [rol ve uygulama izni yetkilendirme denetimleri](/graph/api/resources/accessreviewsv2-root?view=graph-rest-beta&preserve-view=true) . Microsoft Graph API 'sindeki erişim gözden geçirmeleri yöntemleri hem uygulama hem de Kullanıcı bağlamları için kullanılabilir. Uygulama bağlamında betikleri çalıştırırken, API 'yi (hizmet ilkesi) çalıştırmak için kullanılan hesaba, erişim gözden geçirmeleri bilgilerini sorgulamak için "AccessReview. Read. All" izninin verilmesi gerekir.

Erişim gözden geçirmeleri için Graph API kullanarak otomatikleştirilmesi için popüler erişim gözden geçirmeleri görevleri şunlardır:

* Erişim Incelemesi oluşturma ve başlatma

* Zamanlanan sonundan önce bir erişim Incelemesini el ile sonlandır

* Tüm çalışan erişim Incelemelerini ve bunların durumlarını listeleyin

* İnceleme serisinin geçmişine, her incelemede gerçekleştirilen kararlar ve eylemlere bakın.

* Erişim Incelemesinin kararlarını toplayın

* Gözden geçirenin sistem tarafından önerilenden farklı bir karar aldığı, tamamlanan incelemelerden kararlar toplayın.

Otomasyon için yeni Graph API sorguları oluştururken, [Graph Explorer](https://developer.microsoft.com/en-us/graph/graph-explorer)kullanmanızı öneririz. Grafik sorgularınızı betiklerine ve koda geçirmeden önce derleyebilir ve keşfedebilirsiniz. Bu, komut dosyanızın kodunu değiştirmeden, aradığınız sonuçları tam olarak elde etmeniz için sorgunuzu hızlı bir şekilde yinelemenize yardımcı olabilir.

## <a name="monitor-access-reviews"></a>Erişim Incelemelerini izleme

Erişim gözden geçirmeleri etkinlikleri kaydedilir ve [Azure AD 'nin denetim günlüklerinden](../reports-monitoring/concept-audit-logs.md)kullanılabilir. Kategori, etkinlik türü ve tarih aralığındaki denetim verilerini filtreleyebilirsiniz. Örnek bir sorgu aşağıda verilmiştir:

| Kategori| İlke |
| - | - |
| Etkinlik türü| Erişim gözden geçirmesi oluşturma |
| | Erişim gözden geçirmesini Güncelleştir |
| | Erişim gözden geçirmesi sona erdi |
| | Erişim gözden geçirmesini silme |
| | Onaylama kararı |
| | Reddetme kararı |
| | Kararmayı Sıfırla |
| | Kararı Uygula |
| Tarih aralığı| yedi gün |


Erişim gözden geçirmeleri hakkında daha gelişmiş sorgular ve analizler ve değişiklikleri izlemek ve gözden geçirmeleri tamamlamak için Azure AD denetim günlüklerinizi [azure Log Analytics](../reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md) veya Azure Olay Hub 'ına aktarmanız önerilir. Azure Log Analytics 'da depolandığında, [güçlü analiz dilini](../reports-monitoring/howto-analyze-activity-logs-log-analytics.md) kullanabilir ve kendi panolarınızı oluşturabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Aşağıdaki ilgili teknolojiler hakkında bilgi edinin.

* [Azure AD yetkilendirme yönetimi nedir?](entitlement-management-overview.md)

* [Azure AD Privileged Identity Management nedir?](../privileged-identity-management/pim-configure.md)