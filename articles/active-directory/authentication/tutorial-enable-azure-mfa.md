---
title: Azure AD Multi-Factor Authentication’ı etkinleştirme
description: Bu öğreticide, bir Kullanıcı grubu için Azure AD Multi-Factor Authentication etkinleştirmeyi ve oturum açma olayı sırasında ikincil faktör isteğini test yapmayı öğreneceksiniz.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 07/13/2020
ms.author: justinha
author: justinha
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: f2e8bf2ccbf7a53563013c7ba653a6f6e8905337
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97881319"
---
# <a name="tutorial-secure-user-sign-in-events-with-azure-ad-multi-factor-authentication"></a>Öğretici: Azure AD Multi-Factor Authentication ile Kullanıcı oturum açma olaylarını güvenli hale getirme

Multi-Factor Authentication (MFA), bir kullanıcının ek tanımlama biçimleri için oturum açma olayı sırasında istendiği bir işlemdir. Bu istem, cep telefonlarına bir kod girmek veya bir parmak izi taraması sağlamak olabilir. İkinci bir kimlik doğrulaması formu gerektirdiğinde, bu ek faktörün bir saldırganın alması veya yinelemesi kolay olmadığı için güvenlik artar.

Azure AD Multi-Factor Authentication ve koşullu erişim ilkeleri, belirli oturum açma olayları sırasında kullanıcılara MFA 'yı etkinleştirme esnekliği sunar. [Kiracınızda Multi-Factor Authentication 'ı yapılandırma ve](https://www.youtube.com/watch?v=qNndxl7gqVM) uygulama konusunda bir video aşağıda verilmiştir (**önerilir**)

> [!IMPORTANT]
> Bu öğreticide, Azure AD Multi-Factor Authentication nasıl etkinleştirileceği bir yönetici gösterilmektedir.
>
> BT ekibiniz Azure AD Multi-Factor Authentication kullanma özelliğini etkinleştirmemişse veya oturum açma sırasında sorun yaşıyorsanız, ek yardım için yardım masasına ulaşın.

Bu öğreticide şunların nasıl yapıldığını öğrenirsiniz:

> [!div class="checklist"]
> * Bir Kullanıcı grubu için Azure AD Multi-Factor Authentication etkinleştirmek üzere koşullu erişim ilkesi oluşturma
> * MFA için istemde bulunan ilke koşullarını yapılandırın
> * MFA işlemini Kullanıcı olarak test etme

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlayabilmeniz için aşağıdaki kaynaklar ve ayrıcalıklar gereklidir:

* En az bir Azure AD Premium P1 veya deneme lisansı etkin çalışan bir Azure AD kiracısı.
    * Gerekirse, [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* *Genel yönetici* ayrıcalıklarına sahip bir hesap.
* Yönetici olmayan ve Kullanıcı tarafından bildiğiniz, *testuser* gibi bir parola. Bu öğreticide bu hesabı kullanarak Son Kullanıcı Azure AD Multi-Factor Authentication deneyimini test edersiniz.
    * Bir kullanıcı oluşturmanız gerekiyorsa bkz. [hızlı başlangıç: Azure Active Directory yeni kullanıcı ekleme](../fundamentals/add-users-azure-active-directory.md).
* Yönetici olmayan kullanıcının, *MFA-test-Group* gibi üyesi olduğu bir grup. Bu öğreticide bu grup için Azure AD Multi-Factor Authentication etkinleştirirsiniz.
    * Bir grup oluşturmanız gerekiyorsa bkz. [Grup oluşturma ve Azure Active Directory üye ekleme](../fundamentals/active-directory-groups-create-azure-portal.md).

## <a name="create-a-conditional-access-policy"></a>Koşullu erişim ilkesi oluşturma

Azure AD Multi-Factor Authentication etkinleştirmek ve kullanmak için önerilen yol, koşullu erişim ilkeleriyle birlikte gerçekleştirilir. Koşullu erişim, bir kullanıcıya bir uygulama veya hizmete erişim verilmeden önce olaylara oturum açma ve ek eylemler isteme konusunda tepki veren ilkeler oluşturup tanımlamanızı sağlar.

![Koşullu erişimin, oturum açma işleminin güvenliğini sağlamak için nasıl çalıştığı hakkında genel bakış Diyagramı](media/tutorial-enable-azure-mfa/conditional-access-overview.png)

Koşullu erişim ilkeleri, kullanıcıların her yerde ve her yerde üretken olmalarını ve ayrıca kuruluşunuzu korumalarına olanak sağlamak amacıyla, parçalı ve belirli bir amaca sahip olabilir. Bu öğreticide, Kullanıcı Azure portal oturum açtığında MFA istemek için temel bir koşullu erişim ilkesi oluşturalım. Bu serinin sonraki bir öğreticide, Azure AD Multi-Factor Authentication, risk tabanlı bir koşullu erişim ilkesi kullanarak yapılandırırsınız.

İlk olarak, bir koşullu erişim ilkesi oluşturun ve Kullanıcı grubunuzu aşağıdaki şekilde atayın:

1. *Genel yönetici* izinlerine sahip bir hesap kullanarak [Azure Portal](https://portal.azure.com) oturum açın.
1. **Azure Active Directory** bulun ve seçin ve ardından sol taraftaki menüden **güvenlik** ' i seçin.
1. **Koşullu erişim**' i seçin ve **+ Yeni ilke**' yi seçin.
1. İlke için *MFA pilot* gibi bir ad girin.
1. **Atamalar**' ın altında **Kullanıcılar ve gruplar**' ı seçin **ve ardından kullanıcıları ve grupları seçin** radyo düğmesini seçin.
1. **Kullanıcılar ve gruplar** kutusunu işaretleyin, ardından mevcut Azure AD kullanıcılarına ve gruplarına göz atmayı **seçin** .
1. *MFA-test-Group* gıbı Azure AD grubunuza gözatıp seçin ve ardından **Seç**' i seçin.

    [![Koşullu erişim ilkesiyle ](media/tutorial-enable-azure-mfa/select-group-for-conditional-access-cropped.png) kullanmak IÇIN Azure AD grubunuzu seçin](media/tutorial-enable-azure-mfa/select-group-for-conditional-access.png#lightbox)

1. Gruba koşullu erişim ilkesini uygulamak için **bitti**' yi seçin.

## <a name="configure-the-conditions-for-multi-factor-authentication"></a>Multi-Factor Authentication için koşulları yapılandırma

Oluşturulan koşullu erişim ilkesi ve Kullanıcı grubu atanmış bir test grubu ile, artık ilkeyi tetikleyen bulut uygulamalarını veya eylemlerini tanımlayın. Bu bulut uygulamaları veya eylemleri, daha fazla işlem gerektirirken MFA için istemde bulunan senaryolardır. Örneğin, bir Finans uygulamasına erişim veya yönetim araçlarının kullanımı için ek bir doğrulama istemi olması gerektiğine karar verebilirsiniz.

Bu öğretici için, koşullu erişim ilkesini Kullanıcı Azure portal oturum açtığında MFA gerektirecek şekilde yapılandırın.

1. **Bulut uygulamaları veya eylemleri**’ni seçin. Koşullu erişim ilkesini *tüm bulut uygulamalarına* uygulamayı seçebilir veya *uygulamaları seçebilirsiniz*. Esneklik sağlamak için, belirli uygulamaları ilkeden de dışlayabilirsiniz.

    Bu öğretici için, *dahil et* sayfasında, **uygulamaları seçin** radyo düğmesini seçin.

1. **Seç**' i seçin, ardından kullanılabilecek kullanılabilir oturum açma olaylarının listesine gözatabilirler.

    Bu öğreticide, ilkenin Azure portal oturum açma olaylarına uygulanması için **Microsoft Azure Yönetim** ' i seçin.

1. Uygulama Seç ' i uygulamak için **Seç**' i ve sonra **bitti**' yi seçin.

    ![Koşullu erişim ilkesine dahil edilecek Microsoft Azure yönetim uygulamasını seçin](media/tutorial-enable-azure-mfa/select-azure-management-app.png)

Erişim denetimleri, kullanıcının onaylanan bir istemci uygulamasına ihtiyacı olan veya karma Azure AD 'ye katılmış bir cihaz kullanan bir kullanıcıya erişim izni verilmesi için gerekli olan gereksinimleri tanımlamanızı sağlar. Bu öğreticide, Azure portal oturum açma olayı sırasında MFA gerektirecek şekilde erişim denetimlerini yapılandırın.

1. *Erişim denetimleri* altında, **izin** ver ' i seçin ve ardından **erişim izni ver** düğmesinin seçili olduğundan emin olun.
1. **Multi-Factor Authentication gerektir** kutusunu işaretleyin ve ardından **Seç**' i seçin.

Koşullu erişim ilkeleri *yalnızca raporun* kullanıcıları nasıl etkilediğini görmek istiyorsanız veya şu anda kullanım ilkesini kullanmak Istemiyorsanız *devre dışı* olarak ayarlanabilir. Bu öğretici için Kullanıcı bir test grubu hedeflediğinden, ilkeyi etkinleştirip Azure AD Multi-Factor Authentication test etmenizi sağlar.

1. *Ilkeyi etkinleştir* **' i açık** yap olarak ayarlayın.
1. Koşullu erişim ilkesini uygulamak için **Oluştur**' u seçin.

## <a name="test-azure-ad-multi-factor-authentication"></a>Azure AD Multi-Factor Authentication test etme

Koşullu erişim ilkenizi ve Azure AD Multi-Factor Authentication işlemini görelim. İlk olarak, MFA gerektirmeyen bir kaynakta aşağıdaki gibi oturum açın:

1. InPrivate veya ınbilito modunda yeni bir tarayıcı penceresi açın ve şu konuma gidin [https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)
1. Yönetici olmayan test Kullanıcı ile, *testuser* gibi oturum açın. MFA 'yı tamamlamanızı isteyen bir istem yoktur.
1. Tarayıcı penceresini kapatın.

Şimdi Azure portal oturum açın. Azure portal, koşullu erişim ilkesinde ek doğrulama gerektirecek şekilde yapılandırıldığından, bir Azure AD Multi-Factor Authentication istemi alırsınız.

1. InPrivate veya ınbilito modunda yeni bir tarayıcı penceresi açın ve konumuna gidin [https://portal.azure.com](https://portal.azure.com) .
1. Yönetici olmayan test Kullanıcı ile, *testuser* gibi oturum açın. Azure AD Multi-Factor Authentication için kaydolmanız ve kullanmanız gerekir. İşlemi tamamlayıp Azure portal başarıyla oturum açabildiğinizi doğrulamak için istemleri izleyin.

    ![Oturum açmak için, tarayıcı istemlerini izleyin ve ardından kayıtlı Multi-Factor Authentication komut isteminde](media/tutorial-enable-azure-mfa/azure-multi-factor-authentication-browser-prompt.png)

1. Tarayıcı penceresini kapatın.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu öğreticinin bir parçası olarak yapılandırılmış Azure AD Multi-Factor Authentication etkinleştirmek için artık koşullu erişim ilkesini kullanmak istemiyorsanız, aşağıdaki adımları kullanarak ilkeyi silin:

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. **Azure Active Directory** bulun ve seçin ve ardından sol taraftaki menüden **güvenlik** ' i seçin.
1. **Koşullu erişim**' i seçin ve ardından, *MFA pilot* gibi oluşturduğunuz ilkeyi seçin
1. **Sil**' i seçin ve ilkeyi silmek istediğinizi onaylayın.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, Azure AD Multi-Factor Authentication seçili bir Kullanıcı grubu için koşullu erişim ilkelerini kullanarak etkinleştirdiniz. Şunları öğrendiniz:

> [!div class="checklist"]
> * Azure AD Kullanıcı grubu için Azure AD Multi-Factor Authentication etkinleştirmek üzere koşullu erişim ilkesi oluşturma
> * MFA için istemde bulunan ilke koşullarını yapılandırın
> * MFA işlemini Kullanıcı olarak test etme

> [!div class="nextstepaction"]
> [Self servis parola sıfırlama için parola geri yazmayı etkinleştirme (SSPR)](./tutorial-enable-sspr-writeback.md)
