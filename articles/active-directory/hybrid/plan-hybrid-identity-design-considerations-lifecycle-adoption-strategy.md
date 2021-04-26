---
title: Karma kimlik tasarımı-yaşam döngüsü benimseme stratejisi Azure | Microsoft Docs
description: Her yaşam döngüsü aşamasına yönelik seçeneklere göre karma kimlik yönetimi görevlerini tanımlamaya yardımcı olur.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: 420b6046-bd9b-4fce-83b0-72625878ae71
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/30/2018
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: bf68406d4b0806e1d533e0bb8669a01939387989
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95997758"
---
# <a name="determine-hybrid-identity-lifecycle-adoption-strategy"></a>Karma kimlik yaşam döngüsü benimseme stratejisini belirleme
Bu görevde, karma kimlik [yönetimi görevlerini belirleme](plan-hybrid-identity-design-considerations-hybrid-id-management-tasks.md)bölümünde tanımladığınız iş gereksinimlerini karşılamak üzere karma kimlik çözümü kimlik yönetimi stratejisini tanımlayacaksınız.

Karma kimlik yönetimi görevlerini bu adımda daha önce sunulan uçtan uca kimlik yaşam döngüsüne göre tanımlamak için her yaşam döngüsü aşaması için kullanılabilen seçenekleri göz önünde bulundurmanız gerekir.

## <a name="access-management-and-provisioning"></a>Erişim yönetimi ve sağlama
İyi bir hesap erişim yönetimi çözümü sayesinde kuruluşunuz, kuruluş genelinde hangi bilgilere kimlerin erişebileceğini tam olarak izleyebilir.

Access Control, merkezi bir tek noktası sağlama sisteminin kritik bir işlevidir. Gizli bilgilerin korunmasının yanı sıra erişim denetimleri, onaylanmamış Yetkilendirmelere sahip olan veya artık gerekli olmayan mevcut hesapları açığa çıkarır. Artık kullanılmayan hesapları denetlemek için, sağlama sistemi hesap bilgilerini hesaplara sahip kullanıcılar hakkında yetkili bilgilerle birlikte bağlar. Yetkili Kullanıcı kimliği bilgileri genellikle insan kaynaklarının veritabanlarında ve dizinlerinde tutulur.

Karmaşık BT kuruluşlardaki hesaplar, yetkilileri tanımlayan yüzlerce parametre içerir ve bu ayrıntılar, sağlama sisteminiz tarafından denetlenebilir. Yetkili kaynaktan sağladığınız verilerle yeni kullanıcılar tanımlanabilir. Erişim isteği onay özelliği, kaynak sağlamayı kendileri için onaylama (veya reddetme) işlemlerini başlatır.

| Yaşam döngüsü yönetimi aşaması | Şirket içinde | Bulut | Hibrit |
| --- | --- | --- | --- |
| Hesap yönetimi ve sağlama |Active Directory® Etki Alanı Hizmetleri (AD DS) sunucu rolünü kullanarak, kullanıcı ve kaynak yönetimi için ölçeklenebilir, güvenli ve yönetilebilir bir altyapı oluşturabilir ve Microsoft® Exchange Server gibi dizin özellikli uygulamalar için destek sağlayabilirsiniz. <br><br> [AD DS bir kimlik Yöneticisi aracılığıyla grupları sağlayabilirsiniz](/previous-versions/mim/ff686261(v=ws.10)) <br>[AD DS kullanıcıları temin edebilirsiniz](/previous-versions/mim/ff686263(v=ws.10)) <br><br> Yöneticiler, güvenlik amacıyla paylaşılan kaynaklara Kullanıcı erişimini yönetmek için erişim denetimini kullanabilir. Active Directory ' de, erişim denetimi, tam denetim, yazma, okuma veya erişim yok gibi nesnelere farklı düzeylerde erişim veya izinler ayarlayarak nesne düzeyinde yönetilir. Active Directory erişim denetimi, farklı kullanıcıların Active Directory nesneleri nasıl kullanabileceğinizi tanımlar. Varsayılan olarak, Active Directory Nesnelerdeki izinler en güvenli ayara ayarlanır. |Bir Microsoft bulut hizmetine erişecek her kullanıcı için bir hesap oluşturmanız gerekir. Ayrıca, Kullanıcı hesaplarını değiştirebilir veya artık gerekli olmadığında silebilirsiniz. Varsayılan olarak, kullanıcıların yönetici izinleri yoktur, ancak bunları isteğe bağlı olarak atayabilirsiniz. <br><br> Azure Active Directory içinde, ana özelliklerden biri kaynaklara erişimi yönetme olanağıdır. Bu kaynaklar dizindeki roller aracılığıyla nesneleri yönetme izinlerinde olduğu gibi bir dizine veya SaaS uygulamaları, Azure hizmetleri ve SharePoint siteleri ya da şirket içi kaynaklar gibi dizin dışındaki kaynaklara ait olabilir. <br><br> Azure Active Directory erişim yönetimi çözümünün merkezinde güvenlik grubudur. Kaynak sahibi (veya dizinin yöneticisi), bir gruba sahip oldukları kaynaklara belirli bir erişim hakkı atayabilir. Grubun üyelerine erişim sağlanacaktır ve kaynak sahibi bir grubun Üyeler listesini bir Departman Yöneticisi veya bir yardım masası Yöneticisi gibi başka birisine yönetme hakkına temsilci seçebilir<br> <br> Azure AD 'de grupları yönetme bölümünde, grupları kullanarak erişimin yönetilmesi hakkında daha fazla bilgi verilmektedir. |Eşitleme ve Federasyon aracılığıyla Active Directory kimliklerini buluta genişletme |

## <a name="role-based-access-control"></a>Rol tabanlı erişim denetimi
Azure rol tabanlı erişim denetimi (Azure RBAC), kullanıcılara erişim izni vermek için iş işlemlerinizi ve kurallarınızı değerlendirmek, test etmek ve zorlamak üzere roller ve sağlama ilkeleri kullanır. Anahtar yöneticileri, sağlama ilkeleri oluşturur ve kullanıcılara roller atayabilir ve bu rollerin kaynaklarına yönelik yetkilendirmeler tanımlar. Azure RBAC, kimlik yönetimi çözümünü yazılım tabanlı işlemleri kullanacak ve sağlama sürecinde Kullanıcı el ile etkileşimini azaltacak şekilde genişletir.
Azure RBAC, şirketin Azure portal erişimi olduğunda bir bireyin yapabilecekleri işlem sayısını kısıtlayabilmesini sağlar. Portala erişimi denetlemek için Azure RBAC kullanarak, BT yöneticileri CA 'sı, aşağıdaki erişim yönetimi yaklaşımlarını kullanarak erişim sağlar:

* **Grup tabanlı rol atama**: Azure AD gruplarına, yerel Active Directory eşitlenemeyen erişim atayabilirsiniz. Bu, kuruluşunuzun araç yönetiminde yaptığı mevcut yatırımlardan ve grupları yönetmek için süreçlerden yararlanmanızı sağlar. Azure AD Premium için temsilci Grup Yönetimi özelliğini de kullanabilirsiniz.
* **Azure 'da yerleşik rolden yararlanın**: kullanıcıların ve grupların yalnızca işlerini yapmak için gereken görevleri yapmak için izne sahip olduğundan emin olmak Için, sahip, katkıda bulunan ve okuyucu olmak üzere üç rol kullanabilirsiniz.
* **Kaynaklara ayrıntılı erişim**: belirli bir abonelik, kaynak grubu veya bir Web sitesi ya da veritabanı gibi tek bir Azure kaynağı için kullanıcılara ve gruplara roller atayabilirsiniz. Bu şekilde, kullanıcıların ihtiyaç duydukları tüm kaynaklara erişimi olduğundan ve yönetilmesi gerekmeyen kaynaklara erişimleri olduğundan emin olabilirsiniz.

## <a name="provisioning-and-other-customization-options"></a>Sağlama ve diğer özelleştirme seçenekleri
Takımınız, kimlik çözümünü ne kadar özelleştireceğinize karar vermek için iş planlarını ve gereksinimleri kullanabilir. Örneğin, büyük bir kuruluş, coğrafi bölgelerde yaygın olarak kullanılan uygulamaları artımlı olarak sağlamaya yönelik bir zaman çizgisi temelinde iş akışları ve özel bağdaştırıcılar için aşamalı bir dağıtım planı gerektirebilir. Bir diğer özelleştirme planı, bir kuruluşun tamamında, başarılı sınamadan sonra iki veya daha fazla uygulamanın sağlanmasını sağlayabilir. Kullanıcı-uygulama etkileşimi özelleştirilebilir ve kaynakları sağlamaya yönelik yordamlar otomatik sağlamayı karşılayacak şekilde değiştirilebilir.

Hizmeti veya bileşeni kaldırmak için sağlamayı kaldırabilirsiniz. Örneğin, bir hesabın sağlamasını kaldırma, hesabın bir kaynaktan silindiği anlamına gelir.

Sağlama kaynaklarının karma modeli, her ikisi de Azure AD tarafından desteklenen isteği ve rol tabanlı yaklaşımları birleştirir. Çalışanların veya yönetilen sistemlerin bir alt kümesi için, bir işletme, rol tabanlı atamayla erişimi otomatik hale getirmek isteyebilir. Bir iş, istek tabanlı bir model aracılığıyla diğer tüm erişim isteklerini veya özel durumları da işleyebilir. Bazı işletmeler el ile atama ile başlayabilir ve daha sonra tam rol tabanlı bir dağıtım amacıyla bir karma modele doğru şekilde geliştilir.

Diğer şirketler, tam rol tabanlı sağlama elde etmek ve bir karma yaklaşımı istenen amaç olarak hedeflemek için iş nedenlerini pratik bir şekilde bulabilir. Hala diğer şirketler yalnızca istek tabanlı sağlama ile memnun olabilir ve rol tabanlı, otomatik sağlama ilkelerini tanımlamak ve yönetmek için ek efor yatırmaya yatırım yapmak istemezsiniz.

## <a name="license-management"></a>Lisans yönetimi
Azure AD 'de grup tabanlı lisans yönetimi, yöneticilerin kullanıcıları bir güvenlik grubuna atamasını sağlar ve Azure AD, otomatik olarak grup üyelerine lisans atar. Bir Kullanıcı daha sonra gruba eklendiyse veya gruptan kaldırılırsa, bir lisans otomatik olarak atanır veya uygun şekilde kaldırılır.

Şirket içi AD 'den eşitlediğiniz grupları veya Azure AD 'de yönetimi kullanabilirsiniz. Bu Azure AD Premium Self-Service Grup yönetimi ile eşleştirme, uygun karar mekanizmalarının lisans atamasını kolayca devredebilir. Lisans çakışmaları ve eksik konum verileri gibi sorunların otomatik olarak sıralandığından emin olabilirsiniz.

## <a name="self-regulating-user-administration"></a>Kendi kendine düzenleme Kullanıcı Yönetimi
Kuruluşunuz tüm iç kuruluşlarda kaynak sağlamaya başladığında, kendi kendine düzenleme Kullanıcı yönetimi özelliğini uygularsınız. Kullanıcıları kuruluş sınırları genelinde sağlamanın olumlu ve avantajlarından yararlanabilirsiniz. Bu ortamda, kullanıcının durumundaki bir değişiklik, kuruluş sınırları ve coğrafi bölgelerde erişim haklarıyla otomatik olarak yansıtılır. Sağlama maliyetlerini azaltabilir, erişim ve onay süreçlerini kolaylaştırabilirsiniz. Uygulama, kuruluşunuzda uçtan uca erişim yönetimi için rol tabanlı erişim denetimi uygulamanın tam potansiyelini daha gerçekçi hale getirir. Yönetici maliyetlerini, Kullanıcı sağlamasını yönetmek için otomatikleştirilmiş yordamlar aracılığıyla azaltabilirsiniz. Güvenlik ilkesi zorlamayı otomatikleştirerek güvenliği iyileştirebilir, büyük Kullanıcı popülasyonu için Kullanıcı yaşam döngüsü yönetimi ve kaynak sağlamayı kolaylaştırabilir ve merkezileştirin.

> [!NOTE]
> Daha fazla bilgi için bkz. Self Servis uygulama erişim yönetimi için Azure AD ayarlama
> 
> 

Lisans tabanlı (Yetkilendirme tabanlı) Azure AD Hizmetleri, Azure AD dizin/hizmet kiracınızda bir aboneliği etkinleştirerek çalışır. Abonelik etkin olduktan sonra hizmet özellikleri, dizin/hizmet yöneticileri tarafından yönetilebilir ve lisanslı kullanıcılar tarafından kullanılabilir. 

## <a name="integration-with-other-3rd-party-providers"></a>Diğer 3. taraf sağlayıcılarıyla tümleştirme

Azure Active Directory, binlerce SaaS uygulamasına ve şirket içi Web uygulamalarına çoklu oturum açma ve gelişmiş uygulama erişim güvenliği sağlar. Daha fazla bilgi için bkz. [uygulamaları Azure Active Directory tümleştirme](../develop/quickstart-register-app.md)

## <a name="define-synchronization-management"></a>Eşitleme yönetimini tanımlama
Şirket içi dizinlerinizin Azure AD ile tümleştirilmesi, kullanıcılarınızın hem bulut kaynaklarına hem de şirket içi kaynaklara erişmesi için ortak bir kimlik oluşturarak daha üretken olmalarını sağlar. Bu tümleştirmeyle, kullanıcılar ve kuruluşlar aşağıdakilerden faydalanabilir:

* Kuruluşlar, kullanıcılara Windows Server Active Directory ve ardından Azure Active Directory bağlanarak şirket içi veya bulut tabanlı hizmetler genelinde ortak bir karma kimlik sağlayabilir.
* Yöneticiler, uygulama kaynağı, cihaz ve Kullanıcı kimliği, ağ konumu ve Multi-Factor Authentication 'a göre koşullu erişim sağlayabilir.
* Kullanıcılar, Azure AD 'deki hesaplar aracılığıyla Microsoft 365, Intune, SaaS uygulamaları ve üçüncü taraf uygulamalar aracılığıyla ortak kimliklerini kullanabilir.
* Geliştiriciler, bulut tabanlı uygulamalar için uygulamaları Active Directory Şirket içi veya Azure ile tümleştirerek ortak kimlik modelinden yararlanan uygulamalar oluşturabilir

Aşağıdaki şekilde, kimlik eşitleme sürecinin üst düzey bir görünümüne örnek verilmiştir.

![Sync](./media/plan-hybrid-identity-design-considerations/identitysync.png)

Kimlik eşitlemesi işlemi

Eşitleme seçeneklerini karşılaştırmak için aşağıdaki tabloyu gözden geçirin:

| Eşitleme yönetimi seçeneği | Avantajlar | Dezavantajlar |
| --- | --- | --- |
| Eşitleme tabanlı (DirSync veya AADConnect aracılığıyla) |Şirket içi ve buluttan eşitlenen kullanıcılar ve gruplar <br>  **İlke denetimi**: hesap ilkeleri, yöneticiye, bulutta ek görevler gerçekleştirmeye gerek kalmadan parola ilkeleri, iş istasyonu, kısıtlama, kilit denetimleri ve daha fazlasını yönetme olanağı veren Active Directory aracılığıyla ayarlanabilir.  <br>  **Erişim denetimi**: bulut hizmetine erişimi kısıtlayabilir, böylece hizmetlere şirket ortamından, çevrimiçi sunuculardan veya her ikisiyle erişilebilir. <br>  Azaltılmış destek çağrıları: Kullanıcıların anımsaması daha az parola varsa, bunları unutmaları daha az olabilir. <br>  Güvenlik: çoklu oturum açma bölümünde kullanılan tüm sunucu ve hizmetler, şirket içinde ana ve denetimli olduğundan, Kullanıcı kimlikleri ve bilgiler korunur. <br>  Güçlü kimlik doğrulaması desteği: bulut hizmetiyle güçlü kimlik doğrulaması (iki öğeli kimlik doğrulaması da denir) kullanabilirsiniz. Ancak, güçlü kimlik doğrulaması kullanırsanız, çoklu oturum açma kullanmanız gerekir. | |
| Federasyon tabanlı (AD FS aracılığıyla) |Güvenlik belirteci hizmeti (STS) tarafından etkinleştirildi. Bir STS 'yi bir Microsoft bulut hizmeti ile çoklu oturum açma erişimi sağlamak üzere yapılandırdığınızda, şirket içi STS 'niz ve Azure AD kiracınızda belirttiğiniz Federasyon etki alanı arasında bir federasyon güveni oluşturacaksınız. <br> Son kullanıcıların birden fazla kaynağa erişim elde etmek için aynı kimlik bilgileri kümesini kullanmasına izin verir <br>Son kullanıcılar birden çok kimlik bilgileri kümesini sürdürmek zorunda değildir. Henüz, kullanıcıların kimlik bilgilerini katılan kaynakların her birine sağlaması gerekir., B2B ve B2C senaryoları desteklenir. |Şirket içi AD FS sunucularında adanmış dağıtım ve bakım için özel personel gerektirir. STS 'niz için AD FS kullanmayı planlıyorsanız güçlü kimlik doğrulaması kullanımı konusunda kısıtlamalar vardır. Daha fazla bilgi için bkz. [AD FS 2,0 Gelişmiş seçeneklerini yapılandırma](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/hh237448(v=ws.10)). |

> [!NOTE]
> Daha fazla bilgi için bkz. Şirket [içi kimliklerinizi Azure Active Directory tümleştirme](whatis-hybrid-identity.md).
> 
> 

## <a name="see-also"></a>Ayrıca Bkz.
[Tasarım konularına genel bakış](plan-hybrid-identity-design-considerations-overview.md)