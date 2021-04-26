---
title: Azure Active Directory 'de risk tabanlı kullanıcı oturum açma koruması
description: Bu öğreticide, hesabında riskli oturum açma davranışı algılandığında kullanıcıları korumak için Azure kimlik koruması 'nı nasıl etkinleştireceğinizi öğreneceksiniz.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 07/13/2020
ms.author: justinha
author: justinha
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 97aabfa39954aa6ba937166eb54c05ac4874ea7e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96741347"
---
# <a name="tutorial-use-risk-detections-for-user-sign-ins-to-trigger-azure-ad-multi-factor-authentication-or-password-changes"></a>Öğretici: Azure AD Multi-Factor Authentication veya parola değişikliklerini tetiklemek için Kullanıcı oturum açma işlemlerinin risk algılamalarını kullanın

Kullanıcılarınızı korumak için, riskli davranışlara otomatik olarak yanıt veren Azure Active Directory (Azure AD) içinde risk tabanlı ilkeler yapılandırabilirsiniz. Azure AD Kimlik Koruması ilkeler, oturum açma girişimini otomatik olarak engelleyebilir veya Azure AD Multi-Factor Authentication için parola değişikliği veya istem gerektirme gibi ek eylemler gerektirebilir. Bu ilkeler, kuruluşunuz için ek bir koruma katmanı olarak mevcut Azure AD koşullu erişim ilkeleriyle birlikte çalışır. Kullanıcılar bu ilkelerden birinde riskli bir davranışı hiçbir şekilde tetikleyemeyebilir, ancak güvenliğinizi tehlikeye atabilir, kuruluşunuz korunur.

> [!IMPORTANT]
> Bu öğreticide, risk tabanlı Azure AD Multi-Factor Authentication nasıl etkinleştireceğinizi gösteren bir yönetici gösterilmektedir.
>
> BT ekibiniz Azure AD Multi-Factor Authentication kullanma özelliğini etkinleştirmemişse veya oturum açma sırasında sorun yaşıyorsanız, ek yardım için yardım masasına ulaşın.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Azure AD Kimlik Koruması için kullanılabilir ilkeleri anlayın
> * Azure AD Multi-Factor Authentication kaydını etkinleştirme
> * Risk tabanlı parola değişikliğini etkinleştirme
> * Risk tabanlı Multi-Factor Authentication'ı etkinleştirme
> * Kullanıcı oturum açma girişimleri için risk tabanlı ilkeleri test etme

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlayabilmeniz için aşağıdaki kaynaklar ve ayrıcalıklar gereklidir:

* En az bir Azure AD Premium P2 veya deneme lisansı etkin çalışan bir Azure AD kiracısı.
    * Gerekirse, [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* *Genel yönetici* ayrıcalıklarına sahip bir hesap.
* Self servis parola sıfırlama ve Azure AD Multi-Factor Authentication için yapılandırılmış Azure AD
    * Gerekirse, [Azure AD SSPR 'yi etkinleştirme öğreticisini doldurun](tutorial-enable-sspr.md).
    * Gerekirse, [Azure AD Multi-Factor Authentication etkinleştirme öğreticisini doldurun](tutorial-enable-azure-mfa.md).

## <a name="overview-of-azure-ad-identity-protection"></a>Azure AD Kimlik Koruması genel bakış

Her gün, Microsoft Kullanıcı oturum açma girişimlerinin bir parçası olarak anonim olarak toplanan sinyalleri toplayıp analiz eder. Bu sinyaller, iyi Kullanıcı oturum açma davranışının düzenlerini oluşturmaya ve olası riskli oturum açma girişimlerini belirlemesine yardımcı olur. Azure AD Kimlik Koruması, Kullanıcı oturum açma girişimlerini gözden geçirebilir ve kuşkulu davranış varsa ek eylem gerçekleştirebilir:

Aşağıdaki eylemlerden bazıları Azure AD Kimlik Koruması risk algılamayı tetikleyebiliriz:

* Sızdırılan kimlik bilgilerine sahip kullanıcılar.
* Anonim IP adreslerinden oturum açma işlemleri.
* Normal konumlara imkansız seyahat.
* Virüslü cihazlardan oturum açma işlemleri.
* Şüpheli etkinlikteki IP adreslerinden oturum açma işlemleri.
* Bilmediğiniz konumlardan oturum açma işlemleri.

Kullanıcıları korumak ve kuşkulu etkinliklere yanıt vermek için Azure AD Kimlik Koruması ' de aşağıdaki üç ilke kullanılabilir. İlke zorlamayı açmak veya devre dışı bırakmak için, ilkenin uygulanacağı kullanıcıları veya grupları seçebilirsiniz ve oturum açma sırasında veya ek eylem isteminde erişimi engellemek isteyip istemediğinize karar verebilirsiniz.

* Kullanıcı riski ilkesi
    * Güvenliği aşılmış kimlik bilgilerine sahip olabilecek Kullanıcı hesaplarını tanımlar ve yanıtlar. Kullanıcıdan yeni bir parola oluşturmasını isteyebilir.
* Oturum açma risk ilkesi
    * Şüpheli oturum açma girişimlerini tanımlar ve yanıtlar. Kullanıcıdan Azure AD Multi-Factor Authentication kullanarak ek doğrulama formları sağlamasını isteyebilir.
* Çok faktörlü kimlik doğrulaması kayıt ilkesi
    * Kullanıcıların Azure AD Multi-Factor Authentication kayıtlı olduğundan emin olur. Bir oturum açma risk ilkesi MFA isterse, kullanıcının zaten Azure AD Multi-Factor Authentication kayıtlı olması gerekir.

Bir ilke kullanıcısını veya bir oturum açma ilkesini etkinleştirdiğinizde, risk düzeyi için eşiği ( *düşük ve üstü*, *Orta ve üstü* ya da *yüksek*) seçebilirsiniz. Bu esneklik, şüpheli oturum açma olayları için herhangi bir denetimi zorunlu tutmanın nasıl olmasını istediğinize karar vermenize olanak tanır.

Azure AD Kimlik Koruması hakkında daha fazla bilgi için bkz. [Azure AD kimlik koruması nedir?](../identity-protection/overview-identity-protection.md)

## <a name="enable-mfa-registration-policy"></a>MFA kayıt ilkesini etkinleştir

Azure AD Kimlik Koruması, kullanıcıların Azure AD Multi-Factor Authentication kaydettirilmesini sağlamaya yardımcı olabilecek bir varsayılan ilke içerir. Oturum açma olaylarını korumak için ek ilkeler kullanırsanız, kullanıcıların MFA için zaten kayıtlı olması gerekir. Bu ilkeyi etkinleştirdiğinizde, kullanıcıların her oturum açma olayında MFA gerçekleştirmesini gerektirmez. İlke yalnızca bir kullanıcının kayıt durumunu denetler ve gerekirse bunları önceden kaydolmalarını ister.

Ek Azure AD Kimlik Koruması İlkeleri için etkinleştirilecek kullanıcılar için MFA kayıt ilkesinin etkinleştirilmesi önerilir. Bu ilkeyi etkinleştirmek için aşağıdaki adımları izleyin:

1. [Azure Portal](https://portal.azure.com) bir genel yönetici hesabı kullanarak oturum açın.
1. **Azure Active Directory** arayıp seçin, **güvenlik**' i seçin, sonra *koru* menü başlığı altında **kimlik koruması**' nı seçin.
1. Sol taraftaki menüden **MFA kayıt ilkesini** seçin.
1. Varsayılan olarak, ilke *tüm kullanıcılar* için geçerlidir. İsterseniz, **atamalar**' ı seçin, ardından ilkenin uygulanacağı kullanıcıları veya grupları seçin.
1. *Denetimler* altında, **erişim**' i seçin. *Azure AD MFA kaydı gerektir* seçeneğinin işaretli olduğundan emin olun ve ardından **Seç**' i seçin.
1. **Ilkeyi** *Açık* olarak ayarlayın, sonra **Kaydet**' i seçin.

    ![Kullanıcıların Azure portal MFA 'ya kaydolmesinin nasıl istendiğinin ekran görüntüsü](./media/tutorial-risk-based-sspr-mfa/enable-mfa-registration.png)

## <a name="enable-user-risk-policy-for-password-change"></a>Parola değişikliği için Kullanıcı risk ilkesini etkinleştir

Microsoft, kullanıcı adı ve parola çiftlerini bulma amacıyla araştırmacılar, kolluk kuvvetleri, Microsoft'taki çeşitli güvenlik ekipleri ve diğer güvenilir kaynaklarla birlikte çalışmalar yapmaktadır. Bu çiftlerin biri ortamınızdaki bir hesapla eşleştiğinde, risk tabanlı bir parola değişikliği istenebilir. Bu ilke ve eylem, daha önce açığa çıkarılan kimlik bilgilerinin artık çalışmadıklarından emin olmak için kullanıcının oturum açmasını sağlamak üzere parolasını güncelleştirmesini gerektirir.

Bu ilkeyi etkinleştirmek için aşağıdaki adımları izleyin:

1. Sol taraftaki menüden **Kullanıcı risk ilkesini** seçin.
1. Varsayılan olarak, ilke *tüm kullanıcılar* için geçerlidir. İsterseniz, **atamalar**' ı seçin, ardından ilkenin uygulanacağı kullanıcıları veya grupları seçin.
1. *Koşullar* altında koşullar ' ı seçin **> risk düzeyi seçin** *ve ardından orta ve üst*' i seçin.
1. **Seç**' i ve sonra **bitti**' yi seçin.
1. *Erişim* altında, **erişim**' i seçin. **Erişime Izin ver** ve *parola iste değişikliği* seçeneğinin işaretli olduğundan emin olun ve ardından **Seç**' i seçin.
1. **Ilkeyi** *Açık* olarak ayarlayın, sonra **Kaydet**' i seçin.

    ![Azure portal Kullanıcı risk ilkesini etkinleştirme ekran görüntüsü](./media/tutorial-risk-based-sspr-mfa/enable-user-risk-policy.png)

## <a name="enable-sign-in-risk-policy-for-mfa"></a>MFA için oturum açma risk ilkesini etkinleştir

Çoğu kullanıcının izlenebilecek normal bir davranışı vardır. Bu norm dışında kalan kullanıcılar, başarıyla oturum açmalarına olanak tanımak riskli olabilir. Bunun yerine, bu kullanıcıyı engellemek veya bir Multi-Factor Authentication gerçekleştirmesini istemeniz gerekebilir. Kullanıcı MFA sınamasını başarıyla tamamlarsa, bunu geçerli bir oturum açma girişimine göz önünde bulundurun ve uygulamaya veya hizmete erişim izni verebilirsiniz.

Bu ilkeyi etkinleştirmek için aşağıdaki adımları izleyin:

1. Sol taraftaki menüden **oturum açma risk ilkesini** seçin.
1. Varsayılan olarak, ilke *tüm kullanıcılar* için geçerlidir. İsterseniz, **atamalar**' ı seçin, ardından ilkenin uygulanacağı kullanıcıları veya grupları seçin.
1. *Koşullar* altında koşullar ' ı seçin **> risk düzeyi seçin** *ve ardından orta ve üst*' i seçin.
1. **Seç**' i ve sonra **bitti**' yi seçin.
1. *Erişim* altında **bir denetim Seç**' i seçin. **Erişime Izin ver** ve *Multi-Factor Authentication gerektir* seçeneğinin işaretli olduğundan emin olun ve ardından **Seç**' i seçin.
1. **Ilkeyi** *Açık* olarak ayarlayın, sonra **Kaydet**' i seçin.

    ![Azure portal oturum açma risk ilkesini etkinleştirme ekran görüntüsü](./media/tutorial-risk-based-sspr-mfa/enable-sign-in-risk-policy.png)

## <a name="test-risky-sign-events"></a>Riskli imza olaylarını test etme

Çoğu kullanıcı oturum açma olayı, önceki adımlarda yapılandırılmış risk tabanlı ilkeleri tetiklemez. Kullanıcı hiçbir şekilde ek MFA istemi görmeyebilir veya parolalarını sıfırlayamaz. Kimlik bilgileri güvende kalırsa ve davranışları tutarlı kalırsa, oturum açma olayları başarılı olur.

Önceki adımlarda oluşturulan Azure AD Kimlik Koruması ilkelerini test etmek için riskli davranışın veya olası saldırıların benzetimini yapmak için bir yol gerekir. Bu testleri yapmak için adımlar, doğrulamak istediğiniz Azure AD Kimlik Koruması ilkeye göre farklılık gösterir. Senaryolar ve adımlar hakkında daha fazla bilgi için bkz. [Azure AD kimlik koruması risk algılamalarını öykünme](../identity-protection/howto-identity-protection-simulate-risk.md).

## <a name="clean-up-resources"></a>Kaynakları temizleme

Testleri tamamladıysanız ve risk tabanlı ilkelerin etkinleştirilmesini istemiyorsanız, devre dışı bırakmak istediğiniz her ilkeye dönün ve **ilkeyi** devre *dışı* olarak ayarlayın.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, Azure AD Kimlik Koruması için risk tabanlı kullanıcı ilkeleri etkinleştirdiniz. Şunları öğrendiniz:

> [!div class="checklist"]
> * Azure AD Kimlik Koruması için kullanılabilir ilkeleri anlayın
> * Azure AD Multi-Factor Authentication kaydını etkinleştirme
> * Risk tabanlı parola değişikliğini etkinleştirme
> * Risk tabanlı Multi-Factor Authentication'ı etkinleştirme
> * Kullanıcı oturum açma girişimleri için risk tabanlı ilkeleri test etme

> [!div class="nextstepaction"]
> [Azure AD Kimlik Koruması hakkında daha fazla bilgi edinin](../identity-protection/overview-identity-protection.md)
