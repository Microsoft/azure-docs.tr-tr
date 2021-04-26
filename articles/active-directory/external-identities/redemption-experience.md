---
title: B2B işbirliği ile davet kullanım-Azure AD
description: Son kullanıcılar için, gizlilik koşullarına yönelik anlaşma dahil olmak üzere Azure AD B2B işbirliği daveti kullanım deneyimini açıklar.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 03/04/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4cd0febe5ffbc1b17718043d5fc97b804f87cc46
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103199736"
---
# <a name="azure-active-directory-b2b-collaboration-invitation-redemption"></a>Azure Active Directory B2B işbirliği davetini sıfırlama

Bu makalede, konuk kullanıcıların kaynaklarınızda ve iletişim kurdukları onay sürecinde erişebileceği yollar açıklanmaktadır. Konuğa bir davet e-postası gönderirseniz, davet, konuğun uygulamanıza veya portala erişim sağlamak için yararlanana bir bağlantı içerir. Davet e-postası, konuklarınızın kaynaklarınıza erişebileceği yollarla yalnızca biridir. Alternatif olarak, dizininize Konuk ekleyebilir ve paylaşmak istediğiniz portalın ya da uygulamanın doğrudan bağlantısını sağlayabilirsiniz. Konukların, kullandıkları yöntemden bağımsız olarak, bir ilk kez onay süreci aracılığıyla yapılır. Bu işlem, konukların gizlilik koşullarını kabul etmesini ve ayarlamış olduğunuz tüm [kullanım koşullarını](../conditional-access/terms-of-use.md) kabul etmesini sağlar.

Dizininize bir Konuk kullanıcı eklediğinizde, Konuk Kullanıcı hesabının başlangıçta **Pendingkabulünü** olarak ayarlanan bir izin durumu (PowerShell 'de görüntülenebilir) vardır. Bu ayar, Konuk davetinizi kabul edene ve gizlilik ilkenize ve kullanım koşullarınıza karşı kabul edilene kadar kalır. Bundan sonra, onay durumu **kabul edildi** olarak değişir ve izin sayfaları artık konuğa sunulmaz.

   > [!IMPORTANT]
   > - **4 ocak 2021 tarihinden itibaren** Google, [WebView oturum açma desteğini kullanımdan](https://developers.googleblog.com/2020/08/guidance-for-our-effort-to-block-less-secure-browser-and-apps.html)kaldırır. Gmail ile Google Federasyonu veya self servis kaydolma kullanıyorsanız, [iş kolu yerel uygulamalarınızı uyumluluk için test](google-federation.md#deprecation-of-webview-sign-in-support)etmeniz gerekir.
   > - Microsoft, **2021 Ekim 'Den itibaren**, B2B işbirliği senaryoları Için YÖNETILMEYEN Azure AD hesapları ve kiracılar oluşturarak artık davetlerin kullanımını desteklememektedir. Hazırlık aşamasında, müşterilerin [e-posta bir kerelik geçiş kodu kimlik doğrulamasını](one-time-passcode.md)kabul etmelerini öneririz. Bu genel önizleme özelliğiyle ilgili geri bildirimlerinize hoş geldiniz ve işbirliği yapmak için daha fazla yol oluşturmak heyecanlıyız.

## <a name="redemption-and-sign-in-through-a-common-endpoint"></a>Ortak bir uç nokta ile yeniden satın alma ve oturum açma

Konuk kullanıcılar artık ortak bir uç nokta (URL) aracılığıyla çok kiracılı veya Microsoft ilk taraf uygulamalarınızda oturum açabilir `https://myapps.microsoft.com` . Daha önce, ortak bir URL, Konuk kullanıcıyı kimlik doğrulaması için kaynak kiracınız yerine kendi ana kiracısına yönlendirebilir, bu nedenle kiracıya özgü bir bağlantı gerekiyordu (örneğin `https://myapps.microsoft.com/?tenantid=<tenant id>` ). Artık Konuk kullanıcı uygulamanın ortak URL 'sine gidebilir, **oturum açma seçeneklerini** belirleyebilir ve sonra **bir kuruluşta oturum aç**' ı seçebilir. Kullanıcı daha sonra kuruluşunuzun adını yazdığında.

![Ortak uç nokta oturum açma](media/redemption-experience/common-endpoint-flow-small.png)

Daha sonra Kullanıcı, e-posta adresiyle oturum açabilecekleri veya yapılandırdığınız bir kimlik sağlayıcısını seçebileceğiniz kiracı uç noktanıza yönlendirilir.

## <a name="redemption-through-a-direct-link"></a>Doğrudan bir bağlantı üzerinden ödeme

Davet e-postasına veya uygulamanın ortak URL 'sine alternatif olarak, bir konuğa uygulamanız veya portalınızın doğrudan bağlantısını sağlayabilirsiniz. Önce [Azure Portal](./b2b-quickstart-add-guest-users-portal.md) veya [PowerShell](./b2b-quickstart-invite-powershell.md)aracılığıyla konuk kullanıcıyı dizininize eklemeniz gerekir. Ardından doğrudan oturum açma bağlantıları dahil olmak üzere [kullanıcılara uygulama dağıtmak için özelleştirilebilen bir yol](../manage-apps/end-user-experiences.md)kullanabilirsiniz. Bir konuk davet e-postası yerine doğrudan bağlantı kullandığında, yine de ilk kez onay deneyiminde gezinir.

> [!NOTE]
> Doğrudan bağlantı kiracıya özgüdür. Diğer bir deyişle, bir kiracı KIMLIĞI veya doğrulanmış etki alanı içerir, bu sayede Konuk, paylaşılan uygulamanın bulunduğu kiracınızda kimlik doğrulaması yapılabilir. Kiracı bağlamıyla doğrudan bağlantı örnekleri aşağıda verilmiştir:
 > - Uygulamalar erişim paneli: `https://myapps.microsoft.com/?tenantid=<tenant id>`
 > - Doğrulanmış bir etki alanı için uygulamalar erişim paneli: `https://myapps.microsoft.com/<;verified domain>`
 > - Azure portal: `https://portal.azure.com/<tenant id>`
 > - Tek uygulama: bkz. [doğrudan oturum açma bağlantısı](../manage-apps/end-user-experiences.md#direct-sign-on-links) kullanma

Davet e-postasında doğrudan bağlantı üzerinden önerilen bazı durumlar vardır. Bu özel durumlar kuruluşunuz için önemliyse, davet e-postasını hala gönderen yöntemleri kullanarak kullanıcıları davet etmenizi öneririz:
 - Kullanıcının Federasyon kuruluşunda bir Azure AD hesabı, MSA veya e-posta hesabı yoktur. Tek seferlik geçiş kodu özelliğini kullanmıyorsanız, bir MSA oluşturma adımlarında gezinmek için konuğun davet e-postasını sağlaması gerekir.
 - Bazen davet eden Kullanıcı nesnesinin bir e-posta adresi olmayabilir (örneğin, bir Outlook ilgili kişisi nesnesi). Bu durumda, Kullanıcı davet e-postasında kullanım URL 'sini tıklamalıdır.
 - Kullanıcı, davet edilen e-posta adresinin diğer adıyla oturum açabilir. (Diğer ad, bir e-posta hesabıyla ilişkili ek bir e-posta adresidir.) Bu durumda, Kullanıcı davet e-postasında kullanım URL 'sini tıklamalıdır.

## <a name="redemption-through-the-invitation-email"></a>Davet e-postası üzerinden ödeme

[Azure Portal kullanarak](./b2b-quickstart-add-guest-users-portal.md)dizininize bir Konuk kullanıcı eklediğinizde, işlemde konuğa bir davet e-postası gönderilir. Ayrıca, [PowerShell kullanırken](./b2b-quickstart-invite-powershell.md) , dizininize Konuk kullanıcıları eklemek için davet e-postaları gönderilmesini de tercih edebilirsiniz. İşte e-postadaki bağlantıyı kullandıklarında konuğun deneyiminden oluşan bir açıklama.

1. Konuk, **Microsoft davetlerden** gönderilen bir [davet e-postası](./invitation-email-elements.md) alır.
2. Konuk, e-postadaki **daveti kabul et** ' i seçer.
3. Konuk, dizininizde oturum açmak için kendi kimlik bilgilerini kullanır. Konukta dizininizden federe olabilecek bir hesap yoksa, [e-posta bir kerelik geçiş kodu (OTP)](./one-time-passcode.md) özelliği etkin değildir; konuğa bir kişisel [MSA](https://support.microsoft.com/help/4026324/microsoft-account-how-to-create) veya BIR [Azure AD Self Servis hesabı](../enterprise-users/directory-self-service-signup.md)oluşturması istenir. Ayrıntılar için [davet kullanım akışına](#invitation-redemption-flow) bakın.
4. Konuk, aşağıda açıklanan [onay deneyimine](#consent-experience-for-the-guest) göre yapılır.
## <a name="invitation-redemption-flow"></a>Davet teminat akışı

Bir Kullanıcı [davet e-postasında](invitation-email-elements.md) **daveti kabul et** BAĞLANTıSıNA tıkladığında, Azure AD aşağıda gösterildiği gibi kullanım akışına göre daveti otomatik olarak alır:

![Kullanım akışı diyagramını gösteren ekran görüntüsü](media/redemption-experience/invitation-redemption-flow.png)

**Kullanıcının Kullanıcı asıl adı (UPN) hem var olan bir Azure AD hem de kişisel MSA hesabıyla eşleşiyorsa, kullanıcıdan hangi hesabı kullanmak istediğini seçmesi istenir.*

1. Azure AD, kullanıcının [mevcut bir Azure AD kiracısında](./what-is-b2b.md#easily-invite-guest-users-from-the-azure-ad-portal)bulunup bulunmadığını belirlemede Kullanıcı tabanlı bulma işlemini gerçekleştirir.

2. Yönetici [doğrudan Federasyonu](./direct-federation.md)etkinleştirmişse, Azure AD kullanıcının etki alanı son ekinin YAPıLANDıRıLMıŞ bir SAML/WS-beslik kimlik sağlayıcısının etki alanıyla eşleşip eşleşmediğini denetler ve kullanıcıyı önceden yapılandırılmış kimlik sağlayıcısına yönlendirir.

3. Bir yönetici [Google Federasyonu](./google-federation.md)etkinleştirmişse, Azure AD kullanıcının etki alanı sonekinin gmail.com veya googlemail.com olup olmadığını denetler ve kullanıcıyı Google 'a yönlendirir.

4. Kullanım süreci, kullanıcının Just-In-Time (JıT) kampanyaya için mevcut bir kişisel [Microsoft hesabı (MSA)](https://support.microsoft.com/help/4026324/microsoft-account-how-to-create) olup olmadığını denetler, ancak davetiye e-posta bağlantısı satın alma için kullanılamaz. Kullanıcının zaten var olan bir MSA varsa, var olan MSA ile oturum açacaksınız.

5. Kullanıcının **giriş dizini** tanımlandıktan sonra, Kullanıcı oturum açmak için ilgili kimlik sağlayıcısına gönderilir.  

6. Davet edilen kullanıcı için 1 ile 4 arasındaki adımlar başarısız olursa, Azure AD, davet eden kiracının guests için [bir kerelik geçiş kodu (OTP)](./one-time-passcode.md) özelliğinin etkinleştirilip etkinleştirilmediğini belirler.

7. [Konuklar için bir kerelik geçiş kodu etkinleştirilirse](./one-time-passcode.md#when-does-a-guest-user-get-a-one-time-passcode), davet edilen e-posta aracılığıyla kullanıcıya bir geçiş kodu gönderilir. Kullanıcı Azure AD oturum açma sayfasında bu geçiş kodunu alır ve girer.

8. Konuklarınız için bir kerelik geçiş kodu devre dışıysa Azure AD, bir tüketici hesabına ait olup olmadığını öğrenmek için etki alanı sonekini denetler. Öyleyse, kullanıcıdan kişisel [Microsoft hesabı](https://support.microsoft.com/help/4026324/microsoft-account-how-to-create)oluşturması istenir. Aksi takdirde, kullanıcıdan bir [Azure AD Self Servis hesabı](../enterprise-users/directory-self-service-signup.md)oluşturması istenir.

9. Azure AD, e-postaya erişimi doğrulayarak bir [Azure AD Self Servis hesabı](../enterprise-users/directory-self-service-signup.md) oluşturmaya çalışır. Hesap, e-postaya bir kod göndererek ve kullanıcının bunu alıp Azure AD 'ye göndermesini sağlayarak yapılır. Ancak, davet edilen kullanıcının kiracısı federe ise veya davet edilen kullanıcının kiracısında AllowEmailVerifiedUsers alanı false olarak ayarlandıysa, Kullanıcı kullanım alanını tamamlayamaz ve Flow bir hatayla sonuçlanır. Daha fazla bilgi için bkz. [B2B işbirliği Azure Active Directory sorun giderme](./troubleshoot.md#the-user-that-i-invited-is-receiving-an-error-during-redemption).

10. Kullanıcıdan bir kişisel [Microsoft hesabı (MSA)](https://support.microsoft.com/help/4026324/microsoft-account-how-to-create)oluşturması istenir.

11. Doğru kimlik sağlayıcısında kimlik doğrulamasından geçtikten sonra, Kullanıcı [onay deneyimini](#consent-experience-for-the-guest)tamamlamaya YÖNELIK Azure AD 'ye yeniden yönlendirilir.  

Tam zamanında (JıT) kampanyaya için, satın alma, kiralanan bir uygulama bağlantısı aracılığıyla olduğunda, 8 ile 10 arasındaki adımlar kullanılamaz. Bir Kullanıcı 6. adıma ulaşırsa ve tek seferlik geçiş kodu özelliği etkinleştirilmemişse, Kullanıcı bir hata iletisi alır ve daveti kullanmayabilir. Bu hatayı engellemek için, yöneticiler e- [posta tek seferlik geçiş kodunu etkinleşmelidir](./one-time-passcode.md#when-does-a-guest-user-get-a-one-time-passcode) veya kullanıcının bir davet bağlantısına tıklamasını sağlar.

## <a name="consent-experience-for-the-guest"></a>Konuk için onay deneyimi

Bir konuk, bir iş ortağı kuruluşundaki kaynaklara ilk kez erişmek üzere oturum açtığında, bunlar aşağıdaki sayfalara kılavuzluk ederler. 

1. Konuk, davet eden kuruluşun gizlilik bildirimini açıklayan **Izinleri gözden geçirme** sayfasını inceler. Bir kullanıcının devam etmesi için bilgilerin kullanımını davet eden kuruluşun gizlilik ilkelerine uygun olarak **kabul etmesi** gerekir.

   ![İzinleri gözden geçir sayfasını gösteren ekran görüntüsü](media/redemption-experience/review-permissions.png) 

   > [!NOTE]
   > Kiracı Yöneticisi olarak kuruluşunuzun gizlilik bildirimine nasıl bağlayabileceğiniz hakkında bilgi için bkz. [nasıl yapılır: kuruluşunuzun gizlilik bilgilerini Azure Active Directory ekleme](../fundamentals/active-directory-properties-area.md).

2. Kullanım koşulları yapılandırılırsa, Konuk açılıp kullanım koşullarını inceler ve **kabul et**' i seçer. 

   ![Yeni kullanım koşullarını gösteren ekran görüntüsü](media/redemption-experience/terms-of-use-accept.png) 

   **Dış kimliklerden** [kullanım koşullarını](../conditional-access/terms-of-use.md) yapılandırabilirsiniz  >  **kullanım koşulları**.

3. Aksi belirtilmediği takdirde, Konuk, konuk tarafından erişebilen uygulamaları listeleyen uygulamalar erişim paneline yönlendirilir.

   ![Uygulamalar erişim panelini gösteren ekran görüntüsü](media/redemption-experience/myapps.png) 

Dizininizde, Konuk **davetinin kabul edilebilir** değeri **Evet** olarak değişir. Bir MSA oluşturulduysa, Konuk **kaynağı** **Microsoft hesabını** gösterir. Konuk Kullanıcı hesabı özellikleri hakkında daha fazla bilgi için bkz. [Azure AD B2B işbirliği kullanıcısının özellikleri](user-properties.md). 

## <a name="next-steps"></a>Sonraki adımlar

- [Azure AD B2B işbirliği nedir?](what-is-b2b.md)
- [Azure portal Azure Active Directory B2B işbirliği kullanıcıları ekleyin](add-users-administrator.md)
- [Bilgi çalışanları, Azure Active Directory B2B işbirliği kullanıcılarını nasıl ekler?](add-users-information-worker.md)
- [PowerShell kullanarak Azure Active Directory B2B işbirliği kullanıcıları ekleme](customize-invitation-api.md#powershell)
- [Kuruluştan konuk kullanıcı olarak ayrılma](leave-the-organization.md)
