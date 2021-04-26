---
title: Microsoft Authenticator App-Azure Active Directory ile passwordless oturum açma
description: Microsoft Authenticator uygulamasını kullanarak Azure AD 'de parolasız oturum açmayı etkinleştirme
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 04/21/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: d586294f101c271f139867d0046576dc9a32f076
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107861798"
---
# <a name="enable-passwordless-sign-in-with-the-microsoft-authenticator-app"></a>Microsoft Authenticator uygulamayla passwordless oturum açmayı etkinleştirme 

Microsoft Authenticator uygulaması, herhangi bir Azure AD hesabında parola kullanılmadan oturum açmak için kullanılabilir. Microsoft Authenticator, cihazın PIN veya biyometri kullandığı bir cihaza bağlı bir kullanıcı kimlik bilgisini etkinleştirmek için anahtar tabanlı kimlik doğrulaması kullanır. [İş Için Windows Hello](/windows/security/identity-protection/hello-for-business/hello-identity-verification) , benzer bir teknoloji kullanır.

Bu kimlik doğrulama teknolojisi, mobil dahil olmak üzere herhangi bir cihaz platformunda kullanılabilir. Bu teknoloji, Microsoft kimlik doğrulama kitaplıkları ile tümleştirilen herhangi bir uygulama veya Web sitesiyle de kullanılabilir.

:::image type="content" border="false" source="./media/howto-authentication-passwordless-phone/phone-sign-in-microsoft-authenticator-app.png" alt-text="Kullanıcının oturum açma onayını onaylamasını isteyen tarayıcı oturum açma örneği.":::

Microsoft Authenticator uygulamadan telefonla oturum açmayı etkinleştiren kişiler, uygulamalarından bir sayıya dokunmasını isteyen bir ileti görür. İçin Kullanıcı adı veya parola istenmez. Uygulamada oturum açma işlemini gerçekleştirmek için, bir kullanıcının sonraki eylemleri tamamlaması gerekir:

1. Numarayı eşleştirin.
2. **Onayla**’yı seçin.
3. PIN veya biyometri sağlayın.

## <a name="prerequisites"></a>Önkoşullar

Microsoft Authenticator uygulamasıyla passwordless telefon oturum açma 'yı kullanmak için aşağıdaki önkoşulların karşılanması gerekir:

- Bir doğrulama yöntemi olarak izin verilen anında iletme bildirimleri ile Azure AD Multi-Factor Authentication.
- İOS 8,0 veya üzerini çalıştıran cihazlarda yüklü Microsoft Authenticator en son sürümü veya Android 6,0 veya üzeri.
- Microsoft Authenticator uygulamasının yüklü olduğu cihaz, Azure AD kiracısı içinde tek bir kullanıcıya kaydedilmelidir. 

> [!NOTE]
> Azure AD PowerShell kullanarak parolasız Microsoft Authenticator oturum açma 'yı etkinleştirdiyseniz, bu, tüm dizininiz için etkinleştirilmiştir. Bu yeni yöntemi kullanmayı etkinleştirirseniz, PowerShell ilkesinin yerini almaz. Yeni *kimlik doğrulama yöntemleri* menüsü aracılığıyla kiracınızdaki tüm kullanıcılar için etkinleştirmenizi öneririz, aksi halde yeni ilkede olmayan kullanıcılar artık parolasız oturum açamaz.

## <a name="enable-passwordless-authentication-methods"></a>Passwordless kimlik doğrulama yöntemlerini etkinleştir

Azure AD 'de passwordless kimlik doğrulamasını kullanmak için, önce Birleşik kayıt deneyimini etkinleştirin, ardından kullanıcıları parola daha az olan yönteme etkinleştirin.

### <a name="enable-the-combined-registration-experience"></a>Birleşik kayıt deneyimini etkinleştir

Passwordless kimlik doğrulama yöntemlerinin kayıt özellikleri, Birleşik kayıt özelliğini kullanır. Kullanıcıların birleştirilmiş kaydı tamamlamasını sağlamak için, [Birleşik güvenlik bilgileri kaydını etkinleştirmek](howto-registration-mfa-sspr-combined.md)için adımları izleyin.

### <a name="enable-passwordless-phone-sign-in-authentication-methods"></a>Passwordless telefon oturum açma kimlik doğrulama yöntemlerini etkinleştir

Azure AD, oturum açma işlemi sırasında hangi kimlik doğrulama yöntemlerinin kullanılabileceğini seçmenizi sağlar. Kullanıcılar, kullanmak istedikleri yöntemler için kayıt kaydeder.

Parolasız telefon oturumu açma kimlik doğrulama yöntemini etkinleştirmek için aşağıdaki adımları izleyin:

1. [Azure Portal](https://portal.azure.com) bir *genel yönetici* hesabıyla oturum açın.
1. *Azure Active Directory* arayıp seçin, sonra **güvenlik**  >  **kimlik doğrulama yöntemleri**  >  **ilkelerine** gidin.
1. **Microsoft Authenticator** altında aşağıdaki seçenekleri belirleyin:
   1. **Etkinleştir** -Evet veya Hayır
   1. **Hedef** -tüm kullanıcılar veya kullanıcıları seçin
1. Her eklenen grup veya Kullanıcı varsayılan olarak, hem passwordless hem de anında bildirim modlarında ("Any" modunda) Microsoft Authenticator kullanılmak üzere etkinleştirilir. Bunu değiştirmek için her satır için:
   1. Buraya gidin **...**  >  Öğesini **yapılandırın**.
   1. **Kimlik doğrulama modu** için-Any, passwordless veya Push
1. Yeni ilkeyi uygulamak için **Kaydet**' i seçin.

## <a name="user-registration-and-management-of-microsoft-authenticator"></a>Microsoft Authenticator Kullanıcı kaydı ve yönetimi

Kullanıcılar, aşağıdaki adımları kullanarak kendilerini Azure AD 'nin passwordless kimlik doğrulama yöntemine kaydeder:

1. [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) adresine göz atın.
1. Oturum açın, ardından **Yöntem ekle > Authenticator uygulaması**' nı ve ardından **Ekle**' yi seçerek Authenticator uygulamasını ekleyin.
1. Microsoft Authenticator uygulamasını cihazınıza yüklemek ve yapılandırmak için yönergeleri izleyin.
1. Doğrulayıcı yapılandırmasını tamamladıktan sonra **bitti** ' yi seçin.
1. **Microsoft Authenticator**, kayıtlı hesabın açılan menüsünde **telefonla oturum açmayı etkinleştir** ' i seçin.
1. Parolasız telefon oturumu açma hesabı kaydını silmek için uygulamadaki yönergeleri izleyin.

Bir kuruluş, kullanıcıların bir parola kullanmadan telefonlarıyla oturum açmasını yönlendirebilir. Microsoft Authenticator uygulamasını yapılandırma ve telefonla oturum açmayı etkinleştirme hakkında daha fazla bilgi için, bkz. [Microsoft Authenticator uygulamasını kullanarak hesaplarınızda oturum açma](../user-help/user-help-auth-app-sign-in.md).

> [!NOTE]
> İlke tarafından telefonla oturum açma kullanımına izin verilmeyen kullanıcılar artık Microsoft Authenticator uygulamasında etkinleştirememektedir.

## <a name="sign-in-with-passwordless-credential"></a>Passwordless kimlik bilgileriyle oturum açın

Bir Kullanıcı, aşağıdaki tüm eylemler tamamlandıktan sonra, passwordless oturum açma kullanmaya başlayabilir:

- Yönetici kullanıcının kiracısını etkinleştirdi.
- Kullanıcı, telefonla oturum açma özelliğini etkinleştirmek için Microsoft Authenticator uygulamasını güncelleştirmiştir.

Kullanıcı telefon oturum açma işlemini ilk kez başlattığında, Kullanıcı aşağıdaki adımları gerçekleştirir:

1. Oturum açma sayfasında adını girer.
2. **İleri**' ye seçer.
3. Gerekirse, **oturum açmak Için başka yollar** seçer.
4. **Microsoft Authenticator uygulamamda bir Isteği Onayla** seçimini seçer.

Daha sonra kullanıcıya bir sayı sunulur. Uygulama, kullanıcıdan bir parola girerek değil, uygun sayıyı seçerek kimlik doğrulamasını ister.

Kullanıcı parolasız telefonla oturum açmayı kullandıktan sonra, uygulama bu yöntem aracılığıyla kullanıcıya kılavuzluk eder. Ancak, Kullanıcı başka bir yöntem seçme seçeneğini görür.

:::image type="content" border="false" source="./media/howto-authentication-passwordless-phone/web-sign-in-microsoft-authenticator-app.png" alt-text="Microsoft Authenticator uygulamasını kullanarak tarayıcı oturum açma örneği.":::

## <a name="known-issues"></a>Bilinen Sorunlar

Aşağıdaki bilinen sorunlar var.

### <a name="not-seeing-option-for-passwordless-phone-sign-in"></a>Parolasız telefon oturumu açma seçeneği görünmüyor

Bir senaryoda, bir Kullanıcı, bekleyen bir passwordless telefon oturum açma doğrulamasına sahip olabilir. Henüz Kullanıcı yeniden oturum açmayı deneyebilir. Bu durumda, Kullanıcı yalnızca parola girme seçeneğini görebilir.

Bu senaryoyu çözmek için aşağıdaki adımlar kullanılabilir:

1. Microsoft Authenticator uygulamasını açın.
2. Tüm bildirim istemlerini yanıtlayın.

Ardından Kullanıcı, passwordless telefon oturum açma 'yı kullanmaya devam edebilir.

### <a name="federated-accounts"></a>Federasyon hesapları

Bir Kullanıcı herhangi bir passwordless kimlik bilgisini etkinleştirmediğinde, Azure AD oturum açma işlemi oturum açma ipucunu kullanmayı durduruyor \_ . Bu nedenle işlem artık kullanıcıyı bir Federasyon oturum açma konumuna doğru hızlandırmıyor.

Bu mantık genellikle, karma Kiracıdaki bir kullanıcının oturum açma doğrulaması için Active Directory Federasyon Hizmetleri 'ne (AD FS) yönlendirilmesine engel olur. Ancak Kullanıcı, **bunun yerine parolanızı kullanma** seçeneğini saklar.

### <a name="azure-mfa-server"></a>Azure MFA sunucusu

Bir son kullanıcı, şirket içi Azure MFA sunucusu aracılığıyla çok faktörlü kimlik doğrulaması (MFA) için etkinleştirilebilir. Kullanıcı yine de tek bir parolasız telefonla oturum açma kimlik bilgisi oluşturup kullanabilir.

Kullanıcı, Microsoft Authenticator uygulamasının birden çok yüklemesini (5 +), passwordless telefon oturum açma kimlik bilgisiyle yükseltmeyi denerse, bu değişiklik bir hata oluşmasına neden olabilecek.

### <a name="device-registration"></a>Cihaz kaydı

Bu yeni güçlü kimlik bilgisini oluşturabilmeniz için önkoşul vardır. Bir önkoşul, Microsoft Authenticator uygulamasının yüklü olduğu cihazın Azure AD kiracısında tek bir kullanıcıya kaydolmalıdır.

Şu anda bir cihaz yalnızca tek bir kiracıda kaydedilebilir. Bu sınır, telefonla oturum açma için Microsoft Authenticator uygulamasındaki yalnızca bir iş veya okul hesabının etkinleştiribileceği anlamına gelir.

> [!NOTE]
> Cihaz kaydı, cihaz yönetimi veya mobil cihaz yönetimi (MDM) ile aynı değildir. Cihaz kaydı, Azure AD dizininde yalnızca bir cihaz KIMLIĞINI ve kullanıcı KIMLIĞINI bir araya ilişkilendirir.

## <a name="next-steps"></a>Sonraki adımlar

Azure AD kimlik doğrulaması ve parolasız yöntemler hakkında bilgi edinmek için aşağıdaki makalelere bakın:

- [Passwordless kimlik doğrulamasının nasıl çalıştığını öğrenin](concept-authentication-passwordless.md)
- [Cihaz kaydı hakkında bilgi edinin](../devices/overview.md#getting-devices-in-azure-ad)
- [Azure AD Multi-Factor Authentication hakkında bilgi edinin](../authentication/howto-mfa-getstarted.md)
