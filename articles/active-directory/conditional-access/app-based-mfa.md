---
title: Hızlı başlangıç-Azure Active Directory Koşullu erişimi olan belirli uygulamalar için Multi-Factor Authentication (MFA) gerektir | Microsoft Docs
description: Bu hızlı başlangıçta, kimlik doğrulama gereksinimlerinizi Azure Active Directory (Azure AD) koşullu erişim kullanarak erişilen bulut uygulaması türüne nasıl sağlayabileceğinizi öğreneceksiniz.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: quickstart
ms.date: 01/30/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7a23f8fc10e0e5a19be1f93cc6d6e5e8e301f86d
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73474028"
---
# <a name="quickstart-require-mfa-for-specific-apps-with-azure-active-directory-conditional-access"></a>Hızlı başlangıç: koşullu erişim Azure Active Directory belirli uygulamalar için MFA gerektirme

Kullanıcılarınızın oturum açma deneyimini basitleştirmek için, kullanıcıların bir Kullanıcı adı ve parola kullanarak bulut uygulamalarınızda oturum açmalarına izin vermek isteyebilirsiniz. Ancak birçok ortamda, çok faktörlü kimlik doğrulaması (MFA) gibi daha güçlü bir hesap doğrulaması biçimi gerektirmesi için en az birkaç uygulama vardır. Bu ilke, kuruluşunuzun e-posta sistemine veya ık uygulamalarınıza erişim için doğru olabilir. Azure Active Directory (Azure AD) içinde, bu hedefi bir koşullu erişim ilkesiyle gerçekleştirebilirsiniz.

Bu hızlı başlangıçta, ortamınızdaki seçili bir bulut uygulaması için çok faktörlü kimlik doğrulaması gerektiren bir [Azure AD koşullu erişim ilkesinin](../active-directory-conditional-access-azure-portal.md) nasıl yapılandırılacağı gösterilmektedir.

![Azure portal örnek koşullu erişim ilkesi](./media/app-based-mfa/32.png)

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

Bu hızlı başlangıçta senaryoyu tamamlayabilmeniz için şunlar gerekir:

- **Azure AD Premium sürümüne erişim** -Azure AD koşullu erişim, Azure AD Premium bir özelliktir.
- **Isabella Simonsen adlı bir sınama hesabı** -bir sınama hesabı oluşturmayı bilmiyorsanız bkz. [bulut tabanlı kullanıcı ekleme](../fundamentals/add-users-azure-active-directory.md#add-a-new-user).

Bu hızlı başlangıçtaki senaryo, test hesabınız için Kullanıcı başına MFA 'nın etkin olmasını gerektirir. Daha fazla bilgi için, bkz. [bir kullanıcı için iki adımlı doğrulama gerektirme](../authentication/howto-mfa-userstates.md).

## <a name="test-your-experience"></a>Deneyiminizi test etme

Bu adımın amacı, bir koşullu erişim ilkesi olmadan deneyimin bir izlenimini sağlamaktır.

**Ortamınızı başlatmak için:**

1. Azure portal Isabella Simonsen olarak oturum açın.
1. Oturumunuzu kapatın.

## <a name="create-your-conditional-access-policy"></a>Koşullu erişim ilkenizi oluşturma

Bu bölüm, gerekli koşullu erişim ilkesinin nasıl oluşturulacağını gösterir. Bu hızlı başlangıçtaki senaryo şunları kullanır:

- MFA gerektiren bir bulut uygulaması için yer tutucu olarak Azure portal. 
- Koşullu erişim ilkesini test etmek için örnek Kullanıcı.  

İlkenizde şunları ayarlayın:

| Ayar | Değer |
| --- | --- |
| Kullanıcılar ve gruplar | Isabella Simonsen |
| Bulut uygulamaları | Microsoft Azure yönetimi |
| Erişim verme | Multi-Factor Authentication gerektir |

![Genişletilmiş koşullu erişim ilkesi](./media/app-based-mfa/31.png)

**Koşullu erişim ilkenizi yapılandırmak için:**

1. [Azure Portal](https://portal.azure.com) genel yönetici, güvenlik yöneticisi veya koşullu erişim Yöneticisi olarak oturum açın.
1. Azure portal, araması yapın ve **Azure Active Directory**seçin.

   ![Azure Active Directory](./media/app-based-mfa/02.png)

1. **Azure Active Directory** sayfasındaki **güvenlik** bölümünde **koşullu erişim**' e tıklayın.

   ![Koşullu Erişim](./media/app-based-mfa/03.png)

1. **Koşullu erişim** sayfasında, üstteki araç çubuğunda **Yeni ilke**' ye tıklayın.

   ![Ekle](./media/app-based-mfa/04.png)

1. **Yeni** sayfada, **ad** metin kutusuna, **Azure Portal erişim için MFA gerektir**yazın.

   ![Ad](./media/app-based-mfa/05.png)

1. **Atama** bölümünde **Kullanıcılar ve gruplar**' a tıklayın.

   ![Kullanıcılar ve gruplar](./media/app-based-mfa/06.png)

1. **Kullanıcılar ve gruplar** sayfasında, aşağıdaki adımları uygulayın:

   ![Kullanıcılar ve gruplar](./media/app-based-mfa/24.png)

   1. **Kullanıcı ve Grup Seç**' e tıklayın ve ardından **Kullanıcılar ve gruplar**' ı seçin.
   1. **Seç**'e tıklayın.
   1. **Seç** sayfasında, **Isabella Simonsen**' ı seçin ve ardından **Seç**' e tıklayın.
   1. **Kullanıcılar ve gruplar** sayfasında **bitti**' ye tıklayın.

1. **Bulut uygulamaları**' na tıklayın.

   ![Bulut uygulamaları](./media/app-based-mfa/08.png)

1. **Bulut uygulamaları** sayfasında, aşağıdaki adımları uygulayın:

   ![Bulut uygulamalarını seçin](./media/app-based-mfa/26.png)

   1. **Uygulama Seç**' e tıklayın.
   1. **Seç**'e tıklayın.
   1. **Seç** sayfasında, **Microsoft Azure Yönetim**' i seçin ve ardından **Seç**' e tıklayın.
   1. **Bulut uygulamaları** sayfasında **bitti**' ye tıklayın.

1. **Erişim denetimleri** bölümünde, **izin ver**' e tıklayın.

   ![Erişim denetimleri](./media/app-based-mfa/10.png)

1. **İzin** sayfasında, aşağıdaki adımları uygulayın:

   ![Semantiği](./media/app-based-mfa/11.png)

   1. **Erişim ver**' i seçin.
   1. **Multi-Factor Authentication gerektir**' i seçin.
   1. **Seç**'e tıklayın.

1. **Ilkeyi etkinleştir** bölümünde, **Açık**' a tıklayın.

   ![İlkeyi etkinleştir](./media/app-based-mfa/18.png)

1. **Oluştur**'a tıklayın.

## <a name="evaluate-a-simulated-sign-in"></a>Benzetimli oturum açmayı değerlendir

Koşullu erişim ilkenizi yapılandırdığınıza göre muhtemelen, beklendiği gibi çalışıp çalışmadığını bilmeniz gerekir. İlk adım olarak, test kullanıcılarınız için bir oturum açma benzetimi yapmak üzere koşullu erişim ilke Aracı ' nı kullanın. Simülasyonu, bu oturum açma ilkesinin ilkeleriniz üzerindeki etkisini tahmin eder ve bir simülasyon raporu oluşturur.  

**What If** ilkesi değerlendirme aracı 'nı başlatmak için, şunu ayarlayın:

- **Isabella Simonsen** Kullanıcı olarak
- Bulut uygulaması olarak **Microsoft Azure yönetimi**

**What If** tıklamak şunları gösteren bir benzetim raporu oluşturur:

- **Uygulanacak ilkelerde** **Azure Portal erişim için MFA gerektir**
- **Izin verme denetimleri**olarak **çok faktörlü kimlik doğrulaması gerektir** .

![Ne tür ilke aracı](./media/app-based-mfa/23.png)

**Koşullu erişim ilkenizi değerlendirmek için:**

1. [Koşullu erişim ilkeleri](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) sayfasında, üstteki menüden **What If**' ye tıklayın.  

   ![Ya](./media/app-based-mfa/14.png)

1. **Kullanıcılar**' a tıklayın, **Isabella Simonsen**' i seçin ve ardından **Seç**' e tıklayın.

   ![Kullanıcı](./media/app-based-mfa/15.png)

1. Bir bulut uygulaması seçmek için aşağıdaki adımları gerçekleştirin:

   ![Bulut uygulamaları](./media/app-based-mfa/16.png)

   1. **Bulut uygulamaları**' na tıklayın.
   1. **Bulut uygulamaları sayfasında**, **Uygulama Seç**' e tıklayın.
   1. **Seç**'e tıklayın.
   1. **Seç** sayfasında, **Microsoft Azure Yönetim**' i seçin ve ardından **Seç**' e tıklayın.
   1. Bulut uygulamaları sayfasında **bitti**' ye tıklayın.

1. **What If**' ye tıklayın.

## <a name="test-your-conditional-access-policy"></a>Koşullu erişim ilkenizi test etme

Önceki bölümde, benzetimli bir oturum açma değerlendirmesini öğrendiniz. Bir simülasyonu ek olarak, beklenen şekilde çalıştığından emin olmak için koşullu erişim ilkenizi da test etmelisiniz.

İlkenizi test etmek için, **Isabella Sıonsen** test hesabınızı kullanarak [Azure Portal](https://portal.azure.com) oturum açmayı deneyin. Ek güvenlik doğrulaması için hesabınızı ayarlamanızı gerektiren bir iletişim kutusu görmeniz gerekir.

![Multi-factor authentication](./media/app-based-mfa/22.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli değilse, test kullanıcısını ve koşullu erişim ilkesini silin:

- Bir Azure AD kullanıcısını nasıl silebileceğinizi bilmiyorsanız bkz. [Azure AD 'de kullanıcıları silme](../fundamentals/add-users-azure-active-directory.md#delete-a-user).
- İlkenizi silmek için ilkenizi seçin ve sonra hızlı erişim araç çubuğunda **Sil** ' e tıklayın.

    ![Multi-factor authentication](./media/app-based-mfa/33.png)

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Oturum riski algılandığında erişimi engellemek](app-sign-in-risk.md)
> [kabul etmek Için kullanım koşulları gerektir](require-tou.md)
