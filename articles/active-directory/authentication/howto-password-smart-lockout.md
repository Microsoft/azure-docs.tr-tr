---
title: Azure AD akıllı kilitleme-Azure Active Directory kullanarak deneme yanılma saldırıları engelleniyor
description: Azure Active Directory akıllı kilitleme, parolaların tahmin edilmeye çalıştığı deneme yanılma saldırılarına karşı kuruluşunuzun korunmasına yardımcı olur
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: a762009a7aaf1a965333ac573efe55d792c3f04b
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70125013"
---
# <a name="azure-active-directory-smart-lockout"></a>Akıllı kilitleme Azure Active Directory

Akıllı kilitleme, kullanıcılarınızın parolalarını tahmin etmeye çalışan kötü aktörlerin kilitlenmesini sağlar veya almak için deneme yanılma yöntemlerini kullanır. Geçerli kullanıcılardan gelen oturum açma işlemlerini algılayabilir ve bunları saldırganlar ve diğer bilinmeyen kaynaklardan farklı şekilde ele alabilir. Akıllı kilitleme saldırganları kilitler, böylece kullanıcılarınız hesaplarına erişmeye devam eder ve üretken olabilirler.

Varsayılan olarak, akıllı kilitleme, hesabı 10 başarısız girişimden bir dakika sonra oturum açma denemelerinde kilitler. Sonraki denemelerde ilk ve daha uzun bir süre sonra, hesap, sonraki başarısız oturum açma denemesinden sonra bir dakika sonra kilitlenir.

Akıllı kilitleme, aynı parolanın kilitleme sayacını arttırmaktan kaçınmak için son üç hatalı parola karmalarını izler. Birisi aynı hatalı parolayı birden çok kez girerse, bu davranış hesabın kilitlenmesine neden olmaz.

 > [!NOTE]
 > Karma izleme işlevselliği, kimlik doğrulama etkin olan müşteriler için, bulutta olmayan şirket içi bir işlem olduğu için kullanılamaz.

AD FS 2016 ve AF FS 2019 kullanan Federasyon dağıtımları, [AD FS Extranet kilitleme ve extranet akıllı kilitleme](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection)kullanarak benzer avantajları etkinleştirebilir.

Akıllı kilitleme, güvenlik ve kullanışlılığın doğru karışımını sunan bu varsayılan ayarlarla tüm Azure AD müşterileri için her zaman açıktır. Akıllı kilitleme ayarlarının kuruluşunuza özgü değerlerle özelleştirilmesi, kullanıcılarınız için ücretli Azure AD lisanslarına ihtiyaç duyar.

Akıllı kilitleme kullanmak, orijinal bir kullanıcının hiçbir şekilde kilitlenmeyeceğini garanti etmez. Akıllı kilitleme bir kullanıcı hesabını kilitlediğinde, orijinal kullanıcıyı kilitlemeleri için en iyi şekilde deneme yaptık. Kilitleme hizmeti, kötü aktörlerin orijinal bir kullanıcı hesabına erişmesini sağlamaya çalışır.  

* Her Azure Active Directory veri merkezi, kilitlemeyi bağımsız olarak izler. Kullanıcı her veri merkezini ziyaret eden bir Kullanıcı (threshold_limit * datacenter_count) deneme sayısına sahip olacaktır.
* Akıllı kilitleme, kötü bir aktör ve orijinal kullanıcı arasında ayrım yapmak için tanıdık konum ve bilinmeyen konum kullanır. Bilmediğiniz ve tanıdık konumların her ikisi de ayrı kilitleme sayaçlarına sahip olur.

Akıllı kilitleme karma dağıtımlarla tümleştirilebilir, şirket içi Active Directory hesapların saldırganlar tarafından kilitlenmesini sağlamak için Parola karması eşitlemesi veya geçişli kimlik doğrulaması kullanılarak tümleştirilir. Azure AD 'de akıllı kilitleme ilkelerini uygun şekilde ayarlayarak, saldırılar şirket içi Active Directory erişmeden önce bu saldırıları filtrelenebilir.

[Doğrudan kimlik doğrulaması](../hybrid/how-to-connect-pta.md)kullanırken şunları yaptığınızdan emin olun:

* Azure AD kilitleme eşiği Active Directory hesap kilitleme eşiğinden **daha azdır** . Active Directory hesap kilitleme eşiği, Azure AD kilitleme eşiğinden en az iki veya üç kat daha uzun olacak şekilde değerleri ayarlayın. 
* Azure AD kilitleme süresi, süreden sonra hesap kilitleme sayacını Active Directory daha uzun bir süre önce ayarlanmalıdır. AD süresi dakika cinsinden ayarlandığında Azure AD süresinin saniye cinsinden ayarlandığını unutmayın. 

Örneğin, Azure AD Sayacınızı AD 'den daha yüksek olmasını istiyorsanız, şirket içi AD 'niz 1 dakikaya (60 saniye) ayarlandığında Azure AD 120 saniye (2 dakika) olacaktır.

> [!IMPORTANT]
> Şu anda, bir yönetici akıllı kilitleme özelliği tarafından kilitlenmişse kullanıcıların bulut hesaplarının kilidini açamaz. Yöneticinin kilitleme süresinin dolmasını beklemesi gerekir. Ancak Kullanıcı, güvenilen bir cihazdan veya konumdan self servis parola sıfırlama (SSPR) kullanarak kilidini açabilir.

## <a name="verify-on-premises-account-lockout-policy"></a>Şirket içi hesap kilitleme ilkesini doğrulama

Şirket içi Active Directory hesabı kilitleme ilkenizi doğrulamak için aşağıdaki yönergeleri kullanın:

1. Grup ilkesi yönetim aracını açın.
2. Kuruluşunuzun hesap kilitleme ilkesini (örneğin, **varsayılan etki alanı ilkesi**) içeren Grup ilkesini düzenleyin.
3. **Bilgisayar yapılandırma** > **ilkelerine** **gidin Windows ayarları** **güvenlik ayarları** **Hesap**ilkeleriHesap > kilitleme >  >  >  **İlke**.
4. **Hesap kilitleme eşikinizi** doğrulayın ve değerlerden **sonra hesap kilitleme sayacını sıfırlayın** .

![Şirket içi Active Directory hesabı kilitleme ilkesini değiştirme](./media/howto-password-smart-lockout/active-directory-on-premises-account-lockout-policy.png)

## <a name="manage-azure-ad-smart-lockout-values"></a>Azure AD akıllı kilitleme değerlerini yönetme

Kuruluş gereksinimlerinize bağlı olarak, akıllı kilitleme değerlerinin özelleştirilmesi gerekebilir. Akıllı kilitleme ayarlarının kuruluşunuza özgü değerlerle özelleştirilmesi, kullanıcılarınız için ücretli Azure AD lisanslarına ihtiyaç duyar.

Kuruluşunuzun akıllı kilitleme değerlerini denetlemek veya değiştirmek için aşağıdaki adımları kullanın:

1. [Azure Portal](https://portal.azure.com) oturum açın ve **Azure Active Directory** > **kimlik doğrulama yöntemleri** > **parola koruması**' na gidin.
1. İlk kilitlenmeden önce bir hesapta kaç tane başarısız oturum açma izni verileceğini temel alarak **kilitleme eşiğini**ayarlayın. Varsayılan değer 10 ' dur.
1. **Kilitleme süresini saniye cinsinden**, her kilitin saniye cinsinden uzunluğu olarak ayarlayın. Varsayılan değer 60 saniyedir (bir dakika).

> [!NOTE]
> Kilitleme sonrasında ilk oturum açma işlemi başarısız olursa, hesap yeniden kilitlenir. Bir hesap sürekli olarak kilitlense, kilitleme süresi artar.

![Azure portal Azure AD akıllı kilitleme ilkesini özelleştirme](./media/howto-password-smart-lockout/azure-active-directory-custom-smart-lockout-policy.png)

## <a name="how-to-determine-if-the-smart-lockout-feature-is-working-or-not"></a>Akıllı kilitleme özelliğinin çalışıp çalışmadığını belirleme

Akıllı kilitleme eşiği tetiklendiğinde, hesap kilitliyken şu iletiyi alırsınız:

**Hesabınız yetkisiz kullanımı engellemek için geçici olarak kilitlidir. Daha sonra tekrar deneyin ve sorun yaşamaya devam ediyorsanız yöneticinizle iletişime geçin.**

## <a name="next-steps"></a>Sonraki adımlar

* [Azure AD kullanarak kuruluşunuzda hatalı parolaların nasıl yapılacağını öğrenin.](howto-password-ban-bad.md)
* [Kullanıcıların kendi hesaplarının kilidini açmalarına izin vermek için self servis parola sıfırlamayı yapılandırın.](quickstart-sspr.md)
