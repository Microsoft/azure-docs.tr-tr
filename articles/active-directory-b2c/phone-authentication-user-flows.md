---
title: Yerel hesap kimlik sağlayıcısını ayarlama
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C kiracınızdaki Kullanıcı akışlarını ayarlarken, yerel hesap kimlik doğrulaması için kullanabileceğiniz kimlik türlerini (e-posta, Kullanıcı adı, telefon numarası) tanımlayın.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/01/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: dd21c1dca0dd54331780ba98f9c53d5b99d6b4e9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100557222"
---
# <a name="set-up-phone-sign-up-and-sign-in-for-user-flows-preview"></a>Kullanıcı akışları için telefon kaydı ve oturum açma ayarlama (Önizleme)

> [!NOTE]
> Kullanıcı akışları için telefon kaydı ve oturum açma ve kurtarma e-posta özellikleri genel önizlemede.

E-posta ve Kullanıcı adının yanı sıra, yerel hesap kimliği sağlayıcınıza telefon kayıt ve oturum açma ekleyerek telefon numarasını kiracı genelinde bir kaydolma seçeneği olarak etkinleştirebilirsiniz. Yerel hesaplar için telefon kaydı ve oturum açma 'yı etkinleştirdikten sonra, Kullanıcı akışlarınıza telefon kaydı ekleyebilirsiniz.

Kullanıcı akışında telefon kaydı ve oturum açma kurulumu aşağıdaki adımları içerir:

- Kullanıcı kimliği olarak bir telefon numarası kabul etmek için yerel hesap kimlik sağlayıcınızdaki [Telefon kayıt ve oturum açma kiracı genelinde yapılandırma](#configure-phone-sign-up-and-sign-in-tenant-wide) . 

- Kullanıcıların kendi telefon numaralarını kullanarak uygulamanıza kaydolmalarına olanak tanımak için [Kullanıcı akışınıza telefon kaydı ekleyin](#add-phone-sign-up-to-a-user-flow) .

- Kullanıcıların telefonlarına sahip olmadıkları zaman, hesaplarını kurtarmak için kullanılabilecek bir e-posta belirtmesini sağlamak üzere [Kurtarma e-posta istemi 'ni (Önizleme) etkinleştirin](#enable-the-recovery-email-prompt-preview) .

Telefon kaydı ile bir Kullanıcı akışı yapılandırdığınızda, Multi-Factor Authentication (MFA) varsayılan olarak devre dışıdır. Kullanıcı akışlarında MFA 'yı telefon kayıt ile etkinleştirebilirsiniz, ancak birincil tanımlayıcı olarak bir telefon numarası kullanıldığından, tek seferlik geçiş kodu ikinci kimlik doğrulama faktörü için kullanılabilen tek seçenektir.

## <a name="configure-phone-sign-up-and-sign-in-tenant-wide"></a>Telefon kayıt ve oturum açma kiracı genelinde yapılandırma

E-posta kaydı, varsayılan olarak yerel hesap kimliği sağlayıcısı ayarlarınızda etkindir. E-posta kaydı, Kullanıcı adı veya telefon numarası seçerek veya seçimini kaldırarak kiracınızda destekleyeceği kimlik türlerini değiştirebilirsiniz.

1. [Azure portalında](https://portal.azure.com) oturum açın.

2. Üst menüdeki **Dizin + abonelik** filtresini seçerek ve Azure AD kiracınızı içeren dizini seçerek Azure AD B2C kiracınızı içeren dizini kullandığınızdan emin olun.

3. Azure portal sol üst köşesindeki **tüm hizmetler** ' i seçin ve ardından **Azure AD B2C**' i arayıp seçin.

4. **Yönet** altında **kimlik sağlayıcıları**' nı seçin.

5. Kimlik sağlayıcısı listesinde **yerel hesap**' ı seçin.

   ![Kimlik sağlayıcıları yerel hesap Seç](media/phone-authentication-user-flows/identity-provider-local-account.png)

1. **Yerel IDP 'Yi Yapılandır** sayfasında, tüketicilerin Azure AD B2C kiracınızda yerel hesaplarını oluşturmak için kullanabileceği izin verilen kimlik türlerinden biri olarak seçili **olduğundan emin olun** .

   ![İzin verilen kimlik türlerini seçin](media/phone-authentication-user-flows/configure-local-idp.png)

1. **Kaydet**’i seçin.

## <a name="add-phone-sign-up-to-a-user-flow"></a>Kullanıcı akışına telefon kaydı ekleme

Yerel hesaplar için kimlik seçeneği olarak telefon kaydı ekledikten sonra, **Önerilen** en son kullanıcı akış sürümleri oldukları sürece bu hesabı Kullanıcı akışlarına ekleyebilirsiniz. Aşağıda, Yeni Kullanıcı akışlarına telefon kaydı eklemeyi gösteren bir örnek verilmiştir. Ancak, önerilen mevcut sürüm Kullanıcı akışlarına telefon kaydı da ekleyebilirsiniz (   >  *Kullanıcı akış adı*  >  **kimlik sağlayıcıları**  >  **yerel hesap telefonu kaydolma** Kullanıcı akışları seçin. 

Telefon kaydolma 'nın yeni bir Kullanıcı akışına nasıl ekleneceğini gösteren bir örnek aşağıda verilmiştir.

1. [Azure portalında](https://portal.azure.com) oturum açın.
2. Portal araç çubuğunda **Dizin + abonelik** simgesini seçin ve ardından Azure AD B2C kiracınızı içeren dizini seçin.

    ![B2C kiracısı, dizin ve abonelik bölmesi, Azure portal](./media/phone-authentication-user-flows/directory-subscription-pane.png)

3. Azure portal, araması yapın ve **Azure AD B2C** seçin.
4. **İlkeler** altında **Kullanıcı akışları**' nı seçin ve ardından **Yeni Kullanıcı akışı**' nı seçin.

    ![Yeni Kullanıcı akışı düğmesi vurgulanmış şekilde portalda Kullanıcı akışları sayfası](./media/phone-authentication-user-flows/signup-signin-user-flow.png)

5. **Kullanıcı akışı oluştur** sayfasında, **oturum aç ve kullanıcı akışını oturum aç** ' ı seçin.

    ![Kaydolma ve oturum açma akışı vurgulanmış bir Kullanıcı akış sayfası seçin](./media/phone-authentication-user-flows/select-user-flow-type.png)

6. **Sürüm seçin** altında **Önerilen**' i seçin ve ardından **Oluştur**' u seçin. (Kullanıcı akışı sürümleri hakkında[daha fazla bilgi edinin](user-flow-versions.md) .)

    ![Kullanıcı akışı oluştur düğmesi](./media/phone-authentication-user-flows/select-version.png)

7. Kullanıcı akışı için bir **ad** girin. Örneğin, *signupsignin1*.
8. **Kimlik sağlayıcıları** bölümünde, **yerel hesaplar** altında **telefon kaydı**' nı seçin.

   ![Kullanıcı akış telefonu kaydolma seçeneği seçildi](media/phone-authentication-user-flows/user-flow-phone-signup.png)

9. **Sosyal kimlik sağlayıcıları**' nın altında, bu kullanıcı akışı için izin vermek istediğiniz diğer kimlik sağlayıcılarını seçin.

   > [!NOTE]
   > Multi-Factor Authentication (MFA), kaydolma Kullanıcı akışları için varsayılan olarak devre dışıdır. Bir telefon kayıt Kullanıcı akışı için MFA 'yı etkinleştirebilirsiniz, ancak bir telefon numarası birincil tanımlayıcı olarak kullanıldığından, ikinci kimlik doğrulama faktörü için tek seferlik bir geçiş kodu kullanılabilir tek seçenektir.

1. **Kullanıcı öznitelikleri ve belirteç talepleri** bölümünde, kayıt sırasında kullanıcıdan toplamak ve göndermek istediğiniz talepleri ve öznitelikleri seçin. Örneğin, **daha fazla göster**' i seçin ve ardından **ülke/bölge**, **görünen ad** ve **posta kodu** için öznitelikler ve talepler ' i seçin. **Tamam**’ı seçin.

1. Kullanıcı akışını eklemek için **Oluştur** ' u seçin. *B2C_1* bir ön eki otomatik olarak ada eklenir.

## <a name="enable-the-recovery-email-prompt-preview"></a>Kurtarma e-posta istemi 'ni (Önizleme) etkinleştirme

Telefon kaydı ve Kullanıcı akışlarınız için oturum açma etkinleştirdiğinizde, kurtarma e-posta özelliğini etkinleştirmek de iyi bir fikirdir. Bu özellikle, bir Kullanıcı, telefonlarını olmadığında hesabını kurtarmak için kullanılabilecek bir e-posta adresi sağlayabilir. Bu e-posta adresi yalnızca hesap kurtarma için kullanılır. Oturum açmak için kullanılamaz.

- Kurtarma e-postası istemi **Açık** olduğunda, ilk kez kaydolan bir kullanıcının bir yedekleme e-postasını doğrulaması istenir. Daha önce bir kurtarma e-postası sağlanmayan bir kullanıcının, bir sonraki oturum açma işlemi sırasında bir yedekleme e-postasını doğrulaması istenmez.

- Kurtarma e-postası **kapalıyken**, oturum açan veya oturum açan bir Kullanıcı, kurtarma e-postası istemi gösterilmez.
 
Kullanıcı akış özelliklerindeki kurtarma e-posta İstemi ' ni etkinleştirebilirsiniz.

> [!NOTE]
> Başlamadan önce, yukarıda açıklandığı gibi Kullanıcı akışınıza [telefon kaydı](#add-phone-sign-up-to-a-user-flow) eklediğinizden emin olun.

### <a name="to-enable-the-recovery-email-prompt"></a>Kurtarma e-posta istemi 'ni etkinleştirmek için

1. [Azure portalında](https://portal.azure.com) oturum açın.
2. Portal araç çubuğunda **Dizin + abonelik** simgesini seçin ve ardından Azure AD B2C kiracınızı içeren dizini seçin.
3. Azure portal, araması yapın ve **Azure AD B2C** seçin.
4. Azure AD B2C, **ilkeler** altında **Kullanıcı akışları**' nı seçin.
5. Listeden Kullanıcı akışını seçin.
6. **Ayarlar** bölümünde **Özellikler**’i seçin.
7. **Telefon numarası kaydolma ve oturum açma (Önizleme) için kurtarma e-postası Isteğini etkinleştir ' in** yanındaki şunları seçin:

   - Hem oturum açma hem de oturum açma sırasında kurtarma e-posta uyarısını göstermek için **üzerinde** .
   - **Kapatın** ve kurtarma e-posta istemi 'ni gizleyin.

    ![Kullanıcıların, kurtarma e-postasını etkinleştirme özellikli akışı özellikleri](./media/phone-authentication-user-flows/recovery-email-settings.png)

8. **Kaydet**’i seçin.

### <a name="to-test-the-recovery-email-prompt"></a>Kurtarma e-posta istemi 'ni test etmek için

Kullanıcı akışınızda telefon kaydı ve oturum açma ve kurtarma e-posta istemi etkinleştirildikten sonra Kullanıcı deneyimini test etmek için **Kullanıcı akışını Çalıştır** ' ı kullanabilirsiniz.

1. **İlkeler**  >  **Kullanıcı akışları**' nı seçin ve ardından oluşturduğunuz kullanıcı akışını seçin. Kullanıcı akışına Genel Bakış sayfasında, **Kullanıcı akışını Çalıştır**' ı seçin.

2. **Uygulama** için adım 1 ' de kaydettiğiniz Web uygulamasını seçin. **Yanıt URL 'si** gösterilmesi gerekir `https://jwt.ms` .

3. **Kullanıcı akışını Çalıştır** ' ı seçin ve aşağıdaki davranışı doğrulayın:

   - İlk kez kaydolan bir kullanıcının bir kurtarma e-postası sağlaması istenir. 
   - Zaten kaydolan ancak bir kurtarma e-postası sağlanmayan bir kullanıcının oturum açması sırasında bir tane sağlaması istenir.

4. Bir e-posta adresi girin ve ardından **doğrulama kodu gönder**' i seçin. Verdiğiniz e-posta gelen kutusuna bir kodun gönderildiğini doğrulayın. Kodu alın ve **doğrulama kodu** kutusuna girin. **Kodu doğrula**' yı seçin.

## <a name="next-steps"></a>Sonraki adımlar

- [Dış kimlik sağlayıcıları Ekle](add-identity-provider.md)
- [Kullanıcı akışı oluşturma](tutorial-create-user-flows.md)