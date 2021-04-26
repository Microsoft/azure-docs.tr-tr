---
title: Akıllı kilitleme ile saldırıları önleme-Azure Active Directory
description: Akıllı kilitleme Azure Active Directory, kuruluşunuzun Kullanıcı parolalarını tahmin etmeye çalışır hale zorlama saldırılarına karşı korunmasına nasıl yardımcı olduğunu öğrenin.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/20/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: a5f501c19da3c2ddc06ad89fe5649789477af7ec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99255382"
---
# <a name="protect-user-accounts-from-attacks-with-azure-active-directory-smart-lockout"></a>Azure Active Directory akıllı kilitleme özelliğiyle kullanıcı hesaplarını saldırılardan koruma

Akıllı kilitleme kullanıcılarınızın parolalarını tahmin etmeye çalışan veya bunun için deneme yanılma yöntemlerini kullanan kötü aktörleri engellemeye yardımcı olur. Akıllı kilitleme geçerli kullanıcılardan gelen oturum açma işlemlerini tanıyabilir ve bunlara, saldırganlara ve diğer bilinmeyen kaynaklara davrandığından farklı davranır. Saldırganlar engellenirken kullanıcılarınız hesaplarına erişmeye ve üretken olmaya devam eder.

## <a name="how-smart-lockout-works"></a>Akıllı kilitleme nasıl çalışacaktır?

Varsayılan olarak, akıllı kilitleme, hesabı 10 başarısız girişimden bir dakika sonra oturum açma denemelerinde kilitler. Sonraki denemelerde ilk ve daha uzun bir süre sonra, hesap, sonraki başarısız oturum açma denemesinden sonra bir dakika sonra kilitlenir. Bir saldırganın bu davranışı geçici olarak çözebileceği yolları en aza indirmek için, kilitleme süresinin ek başarısız oturum açma girişimleri üzerinden ne kadar büyüdüğü oranını açıklamıyoruz.

Akıllı kilitleme, aynı parolanın kilitleme sayacını arttırmaktan kaçınmak için son üç hatalı parola karmalarını izler. Birisi aynı hatalı parolayı birden çok kez girerse, bu davranış hesabın kilitlenmesine neden olmaz.

> [!NOTE]
> Karma izleme işlevselliği, kimlik doğrulama etkin olan müşteriler için, bulutta olmayan şirket içi bir işlem olduğu için kullanılamaz.

AD FS 2016 ve AF FS 2019 kullanan Federasyon dağıtımları, [AD FS Extranet kilitleme ve extranet akıllı kilitleme](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection)kullanarak benzer avantajları etkinleştirebilir.

Tüm Azure AD müşterileri için akıllı kilitleme her zaman açıktır ve bu varsayılan ayarlar, güvenlik ve kullanışlılığın doğru karışımını sunmaktadır. Akıllı kilitleme ayarlarının kuruluşunuza özgü değerlerle özelleştirilmesi, kullanıcılarınız için Azure AD Premium P1 veya daha yüksek lisanslar gerektirir.

Akıllı kilitleme kullanmak, orijinal bir kullanıcının hiçbir şekilde kilitlenmediğini garanti etmez. Akıllı kilitleme bir kullanıcı hesabını kilitlediğinde, orijinal kullanıcıyı kilitleyemedik. Kilitleme hizmeti, kötü aktörlerin orijinal bir kullanıcı hesabına erişmesini sağlamaya çalışır. Aşağıdaki noktalara dikkat edilmelidir:

* Her Azure AD veri merkezi, kilitlemeyi bağımsız olarak izler. Kullanıcı her veri merkezini ziyaret eden bir Kullanıcı (*threshold_limit * datacenter_count*) deneme sayısına sahiptir.
* Akıllı kilitleme, kötü bir aktör ve orijinal kullanıcı arasında ayrım yapmak için tanıdık konum ve bilinmeyen konum kullanır. Bilmediğiniz ve tanıdık konumların her ikisi ayrı kilitleme sayaçlarına sahiptir.

Akıllı kilitleme, şirket içi Active Directory Domain Services (AD DS) hesaplarını saldırganlar tarafından kilitlenmeden korumak için Parola karması eşitleme veya geçişli kimlik doğrulaması kullanan karma dağıtımlarla tümleştirilebilir. Azure AD 'de akıllı kilitleme ilkelerini uygun şekilde ayarlayarak, saldırılar şirket içi AD DS erişmeden önce bu saldırıları filtrelenebilir.

[Geçişli kimlik doğrulaması](../hybrid/how-to-connect-pta.md)kullanırken aşağıdaki noktalar geçerlidir:

* Azure AD kilitleme eşiği AD DS hesap kilitleme eşiğinden **daha azdır** . AD DS hesap kilitleme eşiği, Azure AD kilitleme eşiğinden en az iki veya üç kat daha uzun olacak şekilde değerleri ayarlayın.
* Azure AD kilitleme süresi, süreden sonra hesap kilitleme sayacını AD DS daha uzun bir süre önce ayarlanmalıdır. AD süresi dakika cinsinden ayarlandığında Azure AD süresi saniye cinsinden ayarlanır.

Örneğin, Azure AD sayaçlarınızın AD DS daha yüksek olmasını istiyorsanız, şirket içi AD 'niz 1 dakikaya (60 saniye) ayarlandığında Azure AD 120 saniye (2 dakika) olacaktır.

> [!IMPORTANT]
> Şu anda, bir yönetici akıllı kilitleme özelliği tarafından kilitlenmişse kullanıcıların bulut hesaplarının kilidini açamaz. Yöneticinin kilitleme süresinin dolmasını beklemesi gerekir. Ancak Kullanıcı, güvenilen bir cihazdan veya konumdan self servis parola sıfırlama (SSPR) kullanarak kilidini açabilir.

## <a name="verify-on-premises-account-lockout-policy"></a>Şirket içi hesap kilitleme ilkesini doğrulama

Şirket içi AD DS hesabı kilitleme ilkenizi doğrulamak için, etki alanına katılmış bir sistemden yönetici ayrıcalıklarına sahip aşağıdaki adımları uygulayın:

1. Grup ilkesi yönetim aracını açın.
2. Kuruluşunuzun hesap kilitleme ilkesini (örneğin, **varsayılan etki alanı ilkesi**) içeren Grup ilkesini düzenleyin.
3. **Bilgisayar yapılandırma**  >  **ilkeleri**  >  **Windows ayarları**  >  **güvenlik ayarları**  >  **hesap ilkeleri**  >  **Hesap kilitleme ilkesi**' ne gidin.
4. **Hesap kilitleme eşikinizi** doğrulayın ve değerlerden **sonra hesap kilitleme sayacını sıfırlayın** .

![Şirket içi Active Directory hesabı kilitleme ilkesini değiştirme](./media/howto-password-smart-lockout/active-directory-on-premises-account-lockout-policy.png)

## <a name="manage-azure-ad-smart-lockout-values"></a>Azure AD akıllı kilitleme değerlerini yönetme

Kurumsal gereksinimlerinize bağlı olarak, Azure AD akıllı kilitleme değerlerini özelleştirebilirsiniz. Akıllı kilitleme ayarlarının kuruluşunuza özgü değerlerle özelleştirilmesi, kullanıcılarınız için Azure AD Premium P1 veya daha yüksek lisanslar gerektirir.

Kuruluşunuzun akıllı kilitleme değerlerini denetlemek veya değiştirmek için aşağıdaki adımları izleyin:

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. *Azure Active Directory* arayıp seçin, sonra **güvenlik**  >  **kimlik doğrulama yöntemleri**  >  **parola koruması**' nı seçin.
1. İlk kilitlenmeden önce bir hesapta kaç tane başarısız oturum açma izni verileceğini temel alarak **kilitleme eşiğini** ayarlayın.

    Varsayılan değer 10 ' dur.

1. **Kilitleme süresini saniye cinsinden**, her kilitin saniye cinsinden uzunluğu olarak ayarlayın.

    Varsayılan değer 60 saniyedir (bir dakika).

> [!NOTE]
> Kilitleme sonrasında ilk oturum açma işlemi başarısız olursa, hesap yeniden kilitlenir. Bir hesap sürekli olarak kilitlense, kilitleme süresi artar.

![Azure portal Azure AD akıllı kilitleme ilkesini özelleştirme](./media/howto-password-smart-lockout/azure-active-directory-custom-smart-lockout-policy.png)

## <a name="how-to-determine-if-the-smart-lockout-feature-is-working-or-not"></a>Akıllı kilitleme özelliğinin çalışıp çalışmadığını belirleme

Akıllı kilitleme eşiği tetiklendiğinde, hesap kilitliyken şu iletiyi alırsınız:

*Hesabınız yetkisiz kullanımı engellemek için geçici olarak kilitlidir. Daha sonra tekrar deneyin ve sorun yaşamaya devam ediyorsanız yöneticinizle iletişime geçin.*

Akıllı kilitlemeyi test ettiğinizde, Azure AD kimlik doğrulama hizmeti 'nin coğrafi olarak dağıtılan ve yük dengeli olmasından dolayı oturum açma istekleriniz farklı veri merkezleri tarafından işlenebilir. Bu senaryoda, her Azure AD veri merkezi kilitlemeyi bağımsız olarak izlediği için, bu, bir kilitlenmeye neden olan, tanımlı kilitleme eşiği sayısından daha fazla zaman alabilir. Kullanıcı bir kilitleme gerçekleşmeden önce her bir veri merkezini ziyaret ediyorsa, kullanıcının (*threshold_limit * datacenter_count*) hatalı girişim sayısı vardır.

## <a name="next-steps"></a>Sonraki adımlar

Deneyimi daha da özelleştirmek için, [Azure AD parola koruması için özel yasaklanmış parolalar yapılandırabilirsiniz](tutorial-configure-custom-password-protection.md).

Kullanıcıların bir Web tarayıcısından parolalarını sıfırlamasına veya değiştirmesine yardımcı olmak için [Azure AD self servis parola sıfırlamayı yapılandırabilirsiniz](tutorial-enable-sspr.md).
