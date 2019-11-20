---
title: Hızlı başlangıç-Azure Active Directory Koşullu erişim ile oturum riski algılandığında erişimi engelleyin | Microsoft Docs
description: Bu hızlı başlangıçta, oturum risklerine göre oturum açma işlemlerini engellemek için bir Azure Active Directory (Azure AD) koşullu erişim ilkesini nasıl yapılandırabileceğinizi öğreneceksiniz.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: quickstart
ms.date: 12/14/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 619f1ea3bae001d25eb520f43da33ca94a3160c8
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72880347"
---
# <a name="quickstart-block-access-when-a-session-risk-is-detected-with-azure-active-directory-conditional-access"></a>Hızlı başlangıç: koşullu erişim Azure Active Directory bir oturum riski algılandığında erişimi engelleyin  

Ortamınızı korumalı tutmak için, şüpheli kullanıcıların oturum açmasını engellemek isteyebilirsiniz. [Azure Active Directory (Azure AD) kimlik koruması](../active-directory-identityprotection.md) her oturum açmayı analiz eder ve bir oturum açma girişiminin Kullanıcı hesabının meşru sahibi tarafından gerçekleştirilme olasılığını hesaplar. Bu olasılık (düşük, orta, yüksek), [oturum açma risk düzeyleri](conditions.md#sign-in-risk)adlı bir hesaplanan değer biçiminde belirtilir. Oturum açma riski koşulunu ayarlayarak, belirli oturum açma risk düzeylerine yanıt vermek için bir koşullu erişim ilkesi yapılandırabilirsiniz.

Bu hızlı başlangıçta, yapılandırılmış bir oturum açma risk düzeyi algılandığında oturum açmayı engelleyen bir [koşullu erişim ilkesinin](../active-directory-conditional-access-azure-portal.md) nasıl yapılandırılacağı gösterilmektedir.

![İlke oluşturma](./media/app-sign-in-risk/1000.png)

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide senaryoyu tamamlamak için şunlar gereklidir:

- **Azure AD Premium P2 sürümüne erişim** -koşullu erişim Azure AD Premium P1 özelliği olsa da, bu hızlı başlangıçta senaryo kimlik koruması gerektirdiğinden bir P2 sürümü gerekir.
- **Kimlik koruması** -bu hızlı başlangıçtaki senaryo kimlik korumasının etkinleştirilmesini gerektirir. Kimlik korumasını nasıl etkinleştireceğinizi bilmiyorsanız, bkz. [Azure Active Directory kimlik koruması etkinleştirme](../identity-protection/overview-identity-protection.md).
- **Tor tarayıcısı** - [Tor tarayıcısı](https://www.torproject.org/projects/torbrowser.html.en) , gizliliğinizi çevrimiçi korumanıza yardımcı olacak şekilde tasarlanmıştır. Kimlik koruması, orta düzeyde risk düzeyine sahip anonim IP adreslerinden oturum açma işlemleri olarak bir Tor tarayıcısından oturum açma işlemlerini algılar. Daha fazla bilgi için bkz. [risk algılamalarını Azure Active Directory](../reports-monitoring/concept-risk-events.md).  
- **Charon adlı bir sınama hesabı** -bir sınama hesabı oluşturmayı bilmiyorsanız bkz. [bulut tabanlı kullanıcılar ekleme](../fundamentals/add-users-azure-active-directory.md#add-a-new-user).

## <a name="test-your-sign-in"></a>Oturum açma bilgilerinizi test etme

Bu adımın amacı, test hesabınızın Tor tarayıcısını kullanarak kiracınıza erişebildiğinizden emin olmaktır.

**Oturum açma bilgilerinizi test etmek için:**

1. [Azure Portal](https://portal.azure.com) Için **Charon**olarak oturum açın.
1. Oturumunuzu kapatın.

## <a name="create-your-conditional-access-policy"></a>Koşullu erişim ilkenizi oluşturma

Bu hızlı başlangıçtaki senaryo, **anonım IP adreslerinden** gelen bir oturum açma riskini tespit etmek Için bir Tor tarayıcısından oturum açma kullanır. Bu risk algılamada risk düzeyi Orta. Bu risk algılamasına yanıt vermek için oturum açma riski koşulunu orta olarak ayarlarsınız. Bir üretim ortamında, oturum açma risk koşulunu yüksek veya orta ve yüksek olarak ayarlamanız gerekir.

Bu bölüm, gerekli koşullu erişim ilkesinin nasıl oluşturulacağını gösterir. İlkenizde şunları ayarlayın:

| Ayar | Değer |
| --- | --- |
| Kullanıcılar ve gruplar | Alain Charon  |
| Bulut uygulamaları | Tüm bulut uygulamaları |
| Oturum açma riski | Orta |
| Semantiği | Erişimi engelle |

![İlke oluşturma](./media/app-sign-in-risk/130.png)

**Koşullu erişim ilkenizi yapılandırmak için:**

1. [Azure Portal](https://portal.azure.com) genel yönetici, güvenlik yöneticisi veya koşullu erişim Yöneticisi olarak oturum açın.
1. Azure portal sol gezinti çubuğunda **Azure Active Directory**' e tıklayın.

   ![Azure Active Directory](./media/app-sign-in-risk/02.png)

1. **Azure Active Directory** sayfasındaki **güvenlik** bölümünde **koşullu erişim**' e tıklayın.

   ![Koşullu Erişim](./media/app-sign-in-risk/03.png)

1. **Koşullu erişim** sayfasında, üstteki araç çubuğundan **Ekle**' ye tıklayın.

   ![Adı](./media/app-sign-in-risk/108.png)

1. **Yeni** sayfada, **ad** metin kutusuna **Orta riskli düzey için erişimi engelle**yazın.

   ![Adı](./media/app-sign-in-risk/104.png)

1. **Atama** bölümünde **Kullanıcılar ve gruplar**' a tıklayın.

   ![Kullanıcılar ve gruplar](./media/app-sign-in-risk/06.png)

1. **Kullanıcılar ve gruplar** sayfasında:

   ![Koşullu Erişim](./media/app-sign-in-risk/107.png)

   1. **Kullanıcı ve Grup Seç**' e tıklayın ve ardından **Kullanıcılar ve gruplar**' ı seçin.
   1. **Seç**'e tıklayın.
   1. **Seç** sayfasında, **Charon**' ı seçin ve ardından **Seç**' e tıklayın.
   1. **Kullanıcılar ve gruplar** sayfasında **bitti**' ye tıklayın.
1. **Bulut uygulamaları**' na tıklayın.

   ![Bulut uygulamaları](./media/app-sign-in-risk/08.png)

1. **Bulut uygulamaları** sayfasında:

   ![Koşullu Erişim](./media/app-sign-in-risk/109.png)

   1. **Tüm bulut uygulamaları**' na tıklayın.
   1. **Bitti**’ye tıklayın.
1. **Koşullar**' a tıklayın.

   ![Erişim denetimleri](./media/app-sign-in-risk/19.png)

1. **Koşullar** sayfasında:

   ![Oturum açma risk düzeyi](./media/app-sign-in-risk/21.png)

   1. **Oturum açma riski**' na tıklayın.
   1. **Yapılandırma**olarak **Evet**' e tıklayın.
   1. Oturum açma risk düzeyi olarak **Orta**' yı seçin.
   1. **Seç**'e tıklayın.
   1. **Koşullar** sayfasında **bitti**' ye tıklayın.
1. **Erişim denetimleri** bölümünde, **izin ver**' e tıklayın.

   ![Erişim denetimleri](./media/app-sign-in-risk/10.png)

1. **İzin** sayfasında:

   ![Koşullu Erişim](./media/app-sign-in-risk/105.png)

   1. **Erişimi engelle**' yi seçin.
   1. **Seç**'e tıklayın.
1. **Ilkeyi etkinleştir** bölümünde, **Açık**' a tıklayın.

   ![İlkeyi etkinleştir](./media/app-sign-in-risk/18.png)

1. **Oluştur**’a tıklayın.

## <a name="evaluate-a-simulated-sign-in"></a>Benzetimli bir oturum açmayı değerlendir

Koşullu erişim ilkenizi yapılandırdığınıza göre muhtemelen, beklendiği gibi çalışıp çalışmadığını bilmeniz gerekir. İlk adım olarak, test kullanıcılarınız için bir oturum açma benzetimi yapmak üzere koşullu erişim **ilke aracı** ' nı kullanın. Simülasyon, bu oturum açma işleminin ilkeleriniz üzerindeki etkisini tahmin eder ve bir simülasyon raporu oluşturur.  

Bu senaryo için **ilke İlkesi aracını** çalıştırdığınızda, **Orta risk düzeyi erişimini engelle** ayarı **uygulanacak ilkeler**altında listelenmelidir.

![Kullanıcı](./media/app-sign-in-risk/117.png)

**Koşullu erişim ilkenizi değerlendirmek için:**

1. [Koşullu erişim ilkeleri](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) sayfasında, üstteki menüden **What If**' ye tıklayın.  

   ![Ya](./media/app-sign-in-risk/14.png)

1. **Kullanıcı**' ya tıklayın, **Kullanıcılar** sayfasında **alan Charon** ' u seçin ve ardından **Seç**' e tıklayın.

   ![Kullanıcı](./media/app-sign-in-risk/116.png)

1. **Oturum açma riski**olarak **Orta**' yı seçin.

   ![Kullanıcı](./media/app-sign-in-risk/119.png)

1. **What If**' ye tıklayın.

## <a name="test-your-conditional-access-policy"></a>Koşullu erişim ilkenizi test etme

Önceki bölümde, benzetimli bir oturum açma değerlendirmesini öğrendiniz. Bir simülasyonu ek olarak, beklenen şekilde çalıştığından emin olmak için koşullu erişim ilkenizi da test etmelisiniz.

İlkenizi test etmek için, Tor tarayıcısı kullanılırken [Azure Portal](https://portal.azure.com) bir **alan** olarak oturum açmayı deneyin. Oturum açma denemeniz, koşullu erişim ilkeniz tarafından engellenmelidir.

![Çok faktörlü kimlik doğrulama](./media/app-sign-in-risk/118.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli değilse, test kullanıcısını, Tor tarayıcısını ve koşullu erişim ilkesini silin:

- Bir Azure AD kullanıcısını nasıl silebileceğinizi bilmiyorsanız bkz. [Azure AD 'de kullanıcıları silme](../fundamentals/add-users-azure-active-directory.md#delete-a-user).
- İlkenizi silmek için ilkenizi seçin ve sonra hızlı erişim araç çubuğunda **Sil** ' e tıklayın.

   ![Çok faktörlü kimlik doğrulama](./media/app-sign-in-risk/33.png)

- Tor tarayıcısını kaldırma yönergeleri için bkz. [kaldırma](https://tb-manual.torproject.org/uninstalling/).

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Belirli uygulamalar IÇIN MFA gerektirirken](app-based-mfa.md) [kabul edilmesi Için kullanım koşulları gerektir](require-tou.md)
> 
