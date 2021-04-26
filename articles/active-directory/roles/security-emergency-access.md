---
title: Acil durum erişimi yönetici hesaplarını yönetme-Azure AD
description: Bu makalede, acil durum erişim hesaplarının Azure Active Directory (Azure AD) kuruluşunuzdan yanlışlıkla kilitlenmesini önlemeye yardımcı olmak için nasıl kullanılacağı açıklanır.
services: active-directory
author: markwahl-msft
manager: daveba
ms.author: rolyon
ms.date: 11/05/2020
ms.topic: conceptual
ms.service: active-directory
ms.subservice: roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: d6a1e4b3b44004ec6d03c293bbd10617b3d3af69
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98740831"
---
# <a name="manage-emergency-access-accounts-in-azure-ad"></a>Azure AD'deki acil durum erişim hesaplarını yönetme

Oturum açabilmeniz veya başka bir kullanıcının hesabını yönetici olarak etkinleştirediğinizden Azure Active Directory (Azure AD) kuruluşunuzun yanlışlıkla kilitlenmesini engellemeniz önemlidir. Kuruluşunuzda iki veya daha fazla *acil durum erişim hesabı* oluşturarak yönetim erişiminin yanlışlıkla ne kadar etkili olduğunu azaltabilirsiniz.

Acil durum erişim hesapları yüksek ayrıcalıklı ve belirli kişilere atanmazlar. Acil durum erişim hesaplarının kullanım alanı, normal yönetici hesaplarının kullanılamadığı acil veya "camı kırın" senaryolarıyla sınırlıdır. Acil durum hesabı kullanımını sınırlandırma amacını yalnızca kesinlikle gerekli olduğu zamanlarda korumanızı öneririz.

Bu makalede, Azure AD 'de acil durum erişim hesaplarının yönetilmesine yönelik yönergeler sağlanmaktadır.

## <a name="why-use-an-emergency-access-account"></a>Neden bir acil durum erişim hesabı kullanılmalıdır?

Bir kuruluşun bir acil durum erişim hesabını aşağıdaki durumlarda kullanması gerekebilir:

- Kullanıcı hesapları federe ve bir hücre ağ sonu veya kimlik sağlayıcı kesintisi nedeniyle Federasyon Şu anda kullanılamıyor. Örneğin, ortamınızdaki kimlik sağlayıcısı ana bilgisayarı kapalıysa, Azure AD kimlik sağlayıcısına yeniden yönlendirirse kullanıcılar oturum açamıyor olabilir.
- Yöneticiler Azure AD Multi-Factor Authentication üzerinden kaydedilir ve tek tek cihazları kullanılamaz veya hizmet kullanılamaz. Kullanıcılar bir rolü etkinleştirmek için Multi-Factor Authentication tamamlayamayabilir. Örneğin, bir hücre ağ kesintisi, cihazları için kaydoldukları tek iki kimlik doğrulama mekanizması olan telefon çağrılarını veya alma metin iletilerini yanıtlamayı engellemektedir.
- En son genel yönetici erişimine sahip kişi kuruluştan ayrıldı. Azure AD, son genel yönetici hesabının silinmesini önler, ancak hesabın şirket içinde silinmesini veya devre dışı bırakılmasını engellemez. Her iki durum da kuruluşun hesabı kurtaramıyor olabilir.
- Bir mobil telefonun veya başka ağların kullanılamadığı, doğal olağanüstü durum acil durum acil durumları. 

## <a name="create-emergency-access-accounts"></a>Acil durum erişim hesapları oluşturma

İki veya daha fazla acil durum erişim hesabı oluşturun. Bu hesaplar, \* . onmicrosoft.com etki alanını kullanan ve şirket içi bir ortamdan Federasyon veya eşitlenmemiş olan yalnızca bulut hesapları olmalıdır.

Bu hesapları yapılandırırken, aşağıdaki gereksinimlerin karşılanması gerekir:

- Acil durum erişim hesapları, kuruluştaki herhangi bir kullanıcıyla ilişkilendirilmemelidir. Hesaplarınızın çalışanların sağladığı herhangi bir cep telefonlarıyla, bireysel çalışanlarla veya çalışanlara özgü diğer kimlik bilgileriyle birlikte seyahat eden donanım belirteçlerine bağlı olmadığından emin olun. Bu önlem, kimlik bilgileri gerektiğinde tek bir çalışanın ulaşılamaz olduğu örnekleri ele alır. Kayıtlı tüm cihazların Azure AD ile iletişim kurmak için birden çok yolu olan bilinen, güvenli bir konumda tutulduğundan emin olmanız önemlidir.
- Bir acil durum erişim hesabı için kullanılan kimlik doğrulama mekanizması, diğer acil erişim hesapları da dahil olmak üzere diğer yönetim hesaplarınız tarafından kullanılan verilerden farklı olmalıdır.  Örneğin, normal yönetici oturum açma işlemi şirket içi MFA aracılığıyla kullanılıyorsa, Azure AD MFA farklı bir mekanizma olacaktır.  Ancak, Azure AD MFA, yönetim hesaplarınız için kimlik doğrulamanın birincil parçasıysa, özel denetimler aracılığıyla bir üçüncü taraf MFA sağlayıcısıyla koşullu erişim kullanma gibi, bunlar için farklı bir yaklaşım düşünün.
- Cihazın veya kimlik bilgisinin süresi dolmaz veya kullanım olmaması nedeniyle otomatik temizleme kapsamında olmaması gerekir.  
- Acil durum erişim hesaplarınız için genel yönetici rolü atamasını kalıcı hale getirin. 

### <a name="exclude-at-least-one-account-from-phone-based-multi-factor-authentication"></a>Telefon tabanlı Multi-Factor Authentication 'dan en az bir hesabı hariç tut

Güvenliği aşılmış bir paroladan kaynaklanan bir saldırının riskini azaltmak için, Azure AD tüm bireysel kullanıcılar için Multi-Factor Authentication gerektirmenizi önerir. Bu grup, güvenliği aşılan hesabı önemli bir etkiye sahip olan yöneticileri ve diğer tüm diğer (örneğin, finans Officers) içerir.

Ancak, acil durum erişim hesaplarınızdan en az birinin, diğer acil olmayan hesaplarınızla aynı Multi-Factor Authentication mekanizmasına sahip olmaması gerekir. Bu, üçüncü taraf Multi-Factor Authentication çözümlerini içerir. Azure AD ve diğer bağlı hizmet olarak yazılım (SaaS) uygulamaları için [her yönetici için çok faktörlü kimlik doğrulaması](../authentication/howto-mfa-userstates.md) gerektiren bir koşullu erişim ilkeniz varsa, acil durum erişim hesaplarını bu gereksinimden dışlamayacaksınız ve bunun yerine farklı bir mekanizma yapılandırmalısınız. Ayrıca, hesapların Kullanıcı başına Multi-Factor Authentication ilkesi olmadığından emin olun.

### <a name="exclude-at-least-one-account-from-conditional-access-policies"></a>Koşullu erişim ilkelerinden en az bir hesabı hariç tut

Acil bir durum sırasında, bir ilkenin sorunu çözmesi için erişiminizi engelleyebileceği bir ilke istemezsiniz. Tüm koşullu erişim ilkelerinden en az bir acil durum erişim hesabı dışlanmalıdır.

## <a name="federation-guidance"></a>Federasyon Kılavuzu

Bazı kuruluşlar AD etki alanı hizmetlerini ve ADFS veya benzer kimlik sağlayıcısını Azure AD 'ye federasyona eklemek için kullanır. [Yönetim ayrıcalıklarına sahip şirket içi hesap](../fundamentals/protect-m365-from-on-premises-attacks.md)olmamalıdır. Azure AD dışında yönetim ayrıcalıklarına sahip hesaplar için bir veya daha fazla kimlik doğrulaması, bu sistem (ler) in kesilmesi veya güvenliğinin kesilmesi durumunda gereksiz risk ekler.

## <a name="store-account-credentials-safely"></a>Hesap kimlik bilgilerini güvenli bir şekilde depolayın

Kuruluşlar, acil durum erişim hesapları için kimlik bilgilerinin güvenli tutulduğundan ve yalnızca bunları kullanma yetkisine sahip olan bireyler için bilindiğinden emin olunması gerekir. Bazı müşteriler akıllı kart kullanır ve diğerleri parola kullanır. Acil durum erişim hesabı için bir parola genellikle iki veya üç parçaya ayrılır, ayrı kağıda yazılır ve güvenli ve ayrı konumlarda olan güvenli, firelııres 'de depolanır.

Parolalar kullanılıyorsa, hesapların parolasının sona ermemesi için güçlü parolalara sahip olduğundan emin olun. İdeal olarak, parolaların en az 16 karakter uzunluğunda ve rastgele oluşturulmuş olması gerekir.

## <a name="monitor-sign-in-and-audit-logs"></a>Oturum açma ve denetim günlüklerini izleme

Kuruluşlar, acil durum hesaplarından oturum açma ve denetim günlüğü etkinliğini izlemeli ve bildirimleri diğer yöneticilere tetiklemelidir. Çıkış camı hesaplarında etkinliği izlerken, bu hesapların yalnızca test veya gerçek durumlar için kullanıldığını doğrulayabilirsiniz. Oturum açma günlüklerini izlemek ve cam hesapları her oturum açtığında yönetici ve SMS uyarılarını tetiklemeniz için Azure Log Analytics kullanabilirsiniz.

### <a name="prerequisites"></a>Önkoşullar

1. Azure [ad oturum açma günlüklerini](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) Azure Izleyici 'ye gönderin.

### <a name="obtain-object-ids-of-the-break-glass-accounts"></a>Kesme camı hesaplarının nesne kimliklerini alma

1. Kullanıcı Yöneticisi rolüne atanan bir hesapla [Azure Portal](https://portal.azure.com) oturum açın.
1. **Azure Active Directory**  >  **kullanıcıları**' nı seçin.
1. Kesme camı hesabını arayın ve kullanıcının adını seçin.
1. Daha sonra kullanabilmeniz için nesne KIMLIĞI özniteliğini kopyalayın ve kaydedin.
1. İkinci kesme camı hesabı için önceki adımları tekrarlayın.

### <a name="create-an-alert-rule"></a>Uyarı kuralı oluşturma

1. Azure Izleyici 'de Izleme katılımcısı rolüne atanan bir hesapla [Azure Portal](https://portal.azure.com) oturum açın.
1. **Tüm hizmetler**' i seçin, ara ' ya "Log Analytics" yazın ve **Log Analytics çalışma alanları**' nı seçin.
1. Bir çalışma alanı seçin.
1. Çalışma alanınızda **Uyarılar**  >  **Yeni uyarı kuralı**' nı seçin.
    1. **Kaynak** altında, aboneliğin uyarı kuralını ilişkilendirmek istediğiniz bir tane olduğunu doğrulayın.
    1. **Koşul** altında **Ekle**' yi seçin.
    1. **Sinyal adı** altında **özel günlük araması** ' nı seçin.
    1. **Arama sorgusu** altında, iki kesme camı hesabının nesne kimliklerini ekleyerek aşağıdaki sorguyu girin.
        > [!NOTE]
        > Eklemek istediğiniz her ek kesme camı hesabı için sorguya başka bir "veya UserID = =" Objectguıd "" ekleyin.

        ![Kesme camı hesaplarının nesne kimliklerini bir uyarı kuralına ekleyin](./media/security-emergency-access/query-image1.png)

    1. **Uyarı mantığı** altına şunu girin:

        - Temel alan: sonuç sayısı
        - İşleç: büyüktür
        - Eşik değeri: 0

    1. **Temelinde değerlendirilen**' ın altında, sorgunun ne kadar süre içinde çalışmasını Istediğinize ilişkin **süreyi (dakika cinsinden)** ve sorgunun ne sıklıkta çalıştırılmasını istediğinizi gösteren **sıklığı (dakika cinsinden** ) seçin. Sıklık, dönemden küçük veya bu değere eşit olmalıdır.

        ![Uyarı mantığı](./media/security-emergency-access/alert-image2.png)

    1. **Bitti** seçeneğini belirleyin. Artık bu uyarının tahmini aylık maliyetini görüntüleyebilirsiniz.
1. Uyarı tarafından bildirilecek Kullanıcı bir eylem grubu seçin. Bir tane oluşturmak isterseniz, bkz. [eylem grubu oluşturma](#create-an-action-group).
1. Eylem grubunun üyelerine gönderilen e-posta bildirimini özelleştirmek için, **eylemleri Özelleştir** altında Eylemler ' i seçin.
1. **Uyarı ayrıntıları**' nın altında, uyarı kuralı adını belirtin ve isteğe bağlı bir açıklama ekleyin.
1. Etkinliğin **önem derecesi düzeyini** ayarlayın. Bunu **kritik (sev 0)** olarak ayarlamanızı öneririz.
1. **Oluşturma sonrasında kuralı etkinleştir** altında, **Evet** olarak ayarlayın.
1. Uyarıları bir süredir devre dışı bırakmak için **uyarıları bastır** onay kutusunu seçin ve uyarı vermeden önce bekleme süresini girip **Kaydet**' i seçin.
1. **Uyarı kuralı oluştur**’a tıklayın.

### <a name="create-an-action-group"></a>Bir eylem grubu oluşturun

1. **Eylem grubu oluştur**' u seçin.

    ![bildirim eylemleri için bir eylem grubu oluşturma](./media/security-emergency-access/action-group-image3.png)

1. Eylem grubu adını ve kısa bir ad girin.
1. Aboneliği ve kaynak grubunu doğrulayın.
1. Eylem türü altında **e-posta/SMS/Push/Voice**' ı seçin.
1. **Genel yönetici bildirimi** gibi bir eylem adı girin.
1. **Eylem türünü** **e-posta/SMS/Push/Voice** olarak seçin.
1. Yapılandırmak istediğiniz bildirim yöntemlerini seçmek için **Ayrıntıları Düzenle** ' yi seçin ve gerekli iletişim bilgilerini girin ve ardından **Tamam** ' ı seçerek ayrıntıları kaydedin.
1. Tetiklemek istediğiniz herhangi bir ek eylem ekleyin.
1. **Tamam**’ı seçin.

## <a name="validate-accounts-regularly"></a>Hesapları düzenli olarak doğrula

Personel üyelerini acil durum erişim hesapları kullanmak ve acil durum erişim hesaplarını doğrulamak için eğitedığınızda, en azından, düzenli aralıklarla aşağıdaki adımları uygulayın:

- Güvenlik izleme personelinin, hesap denetimi etkinliğinin devam ettiğini unutmayın.
- Bu hesapları kullanmak için acil durum kesme camı işleminin belgelendiği ve güncel olduğundan emin olun.
- Bir acil durum sırasında bu adımları gerçekleştirmek zorunda olabilecek yöneticilerin ve güvenlik ofislerin işlemde eğitildiğinden emin olun.
- Acil durum erişim hesaplarınız için hesap kimlik bilgilerini belirli bir parolayla güncelleştirin ve ardından acil durum erişim hesaplarının oturum açıp yönetim görevlerini gerçekleştirebilecek olduğunu doğrulayın.
- Kullanıcıların Multi-Factor Authentication veya self servis parola sıfırlama (SSPR), herhangi bir kullanıcının cihazına veya kişisel ayrıntılarına kaydolmadığından emin olun. 
- Hesaplar bir cihaza Multi-Factor Authentication için kaydedilmişse, oturum açma veya rol etkinleştirme sırasında kullanılmak üzere cihaza bir acil durum sırasında kullanması gerekebilecek tüm yöneticiler tarafından erişilebildiğinden emin olun. Ayrıca, cihazın ortak hata modunu paylaşmayan en az iki ağ yolundan iletişim kurabildiğini doğrulayın. Örneğin, cihaz hem bir özelliğin kablosuz ağı hem de bir hücre sağlayıcı ağı üzerinden internet ile iletişim kurabilir.

Bu adımlar, düzenli aralıklarla ve anahtar değişiklikleri için gerçekleştirilmelidir:

- En az 90 günde bir
- BT çalışanındaki iş değişikliği, bir kalkış veya yeni bir işe yönelik son değişiklik olduğunda
- Kuruluştaki Azure AD abonelikleri değiştiğinde

## <a name="next-steps"></a>Sonraki adımlar

- [Azure AD'de karma ve bulut dağıtımları için ayrıcalıklı erişim güvenliğini sağlama](security-planning.md)
- [Azure AD 'yi kullanarak Kullanıcı ekleme](../fundamentals/add-users-azure-active-directory.md) ve [yeni kullanıcıyı genel yönetici rolüne atama](../fundamentals/active-directory-users-assign-role-azure-portal.md)
- Henüz kaydolmadıysanız [Azure AD Premium Için kaydolun](../fundamentals/active-directory-get-started-premium.md)
- [Bir kullanıcı için iki aşamalı doğrulama gerektirme](../authentication/howto-mfa-userstates.md)
- Kullanıyorsanız, [Microsoft 365 Genel Yöneticiler için ek korumalar yapılandırın](/office365/enterprise/protect-your-global-administrator-accounts)Microsoft 365
- [Genel Yöneticiler için erişim gözden geçirmesi başlatın](../privileged-identity-management/pim-how-to-start-security-review.md) ve [var olan genel yöneticileri daha özel yönetici rollerine](permissions-reference.md) geçirin