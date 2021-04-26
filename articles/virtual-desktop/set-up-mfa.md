---
title: Windows sanal masaüstü için Azure çok faktörlü kimlik doğrulamasını ayarlama-Azure
description: Windows sanal masaüstü 'nde daha yüksek güvenlik için Azure çok faktörlü kimlik doğrulamasını ayarlama.
author: Heidilohr
ms.topic: how-to
ms.date: 12/10/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 7ebf38226ff725865104707a3f28e7ce51a86c31
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106445661"
---
# <a name="enable-azure-multifactor-authentication-for-windows-virtual-desktop"></a>Windows sanal masaüstü için Azure çok faktörlü kimlik doğrulamasını etkinleştirme

>[!IMPORTANT]
> Bu sayfayı Windows sanal masaüstü (klasik) belgelerinden ziyaret ediyorsanız, işiniz bittiğinde [Windows sanal masaüstü (klasik) belgelerine döntığınızdan](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md) emin olun.

Windows sanal masaüstü için Windows istemcisi, Windows sanal masaüstünü yerel makineli tümleştirmeyle ilgili mükemmel bir seçenektir. Ancak, Windows sanal masaüstü hesabınızı Windows Istemcisi olarak yapılandırdığınızda, kendinizi ve kullanıcılarınızın güvenliğini sağlamak için uygulamanız gereken bazı ölçüler vardır.

İlk kez oturum açtığınızda, istemci kullanıcı adınızı, parolanızı ve Azure çok faktörlü kimlik doğrulamasını ister. Bundan sonra, bir sonraki oturum açışınızda istemci, Azure Active Directory (AD) Kurumsal uygulamanızdan belirtecinizi hatırlayacaktır. Oturum Ana bilgisayarı için kimlik bilgileri isteminde **Beni anımsa** ' yı seçtiğinizde kullanıcılarınız, kimlik bilgilerini yeniden girmeye gerek kalmadan istemciyi yeniden başlattıktan sonra oturum açabilirler.

Kimlik bilgilerini hatırlarken, kurumsal senaryolarda veya kişisel cihazlarda dağıtımları daha az güvenli hale da olabilir. Kullanıcılarınızı korumak için, istemcinin Azure çok faktörlü kimlik doğrulama kimlik bilgilerini daha sık sormayı sürdürdüğünden emin olabilirsiniz. Bu makalede, Windows sanal masaüstü için koşullu erişim ilkesinin bu ayarı etkinleştirmek üzere nasıl yapılandırılacağı gösterilir.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için yapmanız gerekenler şunlardır:

- Kullanıcılara Azure Active Directory Premium P1 veya P2 içeren bir lisans atayın.
- Kullanıcılarınız Grup üyeleri olarak atanmış bir Azure Active Directory grubu.
- Tüm kullanıcılarınız için Azure çok faktörlü kimlik doğrulamasını etkinleştirin. Bunun nasıl yapılacağı hakkında daha fazla bilgi için, bkz. [bir kullanıcı için iki adımlı doğrulama gerektirme](../active-directory/authentication/howto-mfa-userstates.md#view-the-status-for-a-user).

> [!NOTE]
> Aşağıdaki ayar [Windows Sanal Masaüstü Web istemcisi](https://rdweb.wvd.microsoft.com/arm/webclient/index.html)için de geçerlidir.

## <a name="create-a-conditional-access-policy"></a>Koşullu erişim ilkesi oluşturma

Windows sanal masaüstüne bağlanırken çok faktörlü kimlik doğrulaması gerektiren bir koşullu erişim ilkesi oluşturmak için aşağıdaki adımları uygulayın:

1. **Azure Portal** genel yönetici, güvenlik yöneticisi veya koşullu erişim Yöneticisi olarak oturum açın.
2. **Azure Active Directory**  >  **güvenlik**  >  **koşullu erişimi**'ne gidin.
3. **Yeni ilke**' yi seçin.
4. İlkenize bir ad verin. Kuruluşların ilkelerinin adları için anlamlı bir standart oluşturmasını öneririz.
5. **Atamalar** altında **Kullanıcılar ve gruplar**’ı seçin.
6. **Ekle**' nin altında, **Kullanıcılar ve gruplar**  >  **Kullanıcılar ve gruplar** ' ı seçin > [Önkoşullar](#prerequisites) aşamasında oluşturduğunuz grubu seçin.
7. **Bitti** seçeneğini belirleyin.
8. **Bulut uygulamaları veya eylemler** altında  >  , **Uygulama Seç**' i seçin.
9. Kullanmakta olduğunuz Windows sanal masaüstü sürümüne göre aşağıdaki uygulamalardan birini seçin.
   
   - Windows sanal masaüstü 'Nü (klasik) kullanıyorsanız şu uygulamaları seçin:
       
       - **Windows sanal masaüstü** (uygulama kimliği 5a0aa725-4958-4b0c-80a9-34562e23f3b7)
       - Web istemcisinde ilkeler ayarlamanıza olanak tanıyan **Windows sanal masaüstü istemcisi** (App ID fa4345a4-A730-4230-84a8-7d9651b86739)
       
        Bundan sonra, adım 11 ' e atlayın.

   - Windows sanal masaüstü kullanıyorsanız, bunun yerine bu uygulamayı seçin:
       
       -  **Windows sanal masaüstü** (uygulama kimliği 9cdead84-a844-4324-93f2-b2e6bb768d07)
       
        Bundan sonra, 10. adıma gidin.

   >[!IMPORTANT]
   > Windows sanal masaüstü Azure Resource Manager sağlayıcısı (50e95039-B200-4007-bc97-8d5790743a63) adlı uygulamayı seçmeyin. Bu uygulama yalnızca kullanıcı akışını almak için kullanılır ve çok faktörlü kimlik doğrulamasına sahip olmamalıdır.
   > 
   > Windows sanal masaüstü 'Nü (klasik) kullanıyorsanız, koşullu erişim ilkesi tüm erişimi engelliyorsa ve yalnızca Windows sanal masaüstü uygulama kimliklerini dışlayıp, bu hatayı, ilkeye 9cdead84-a844-4324-93f2-b2e6bb768d07 uygulama KIMLIĞINI ekleyerek çözebilirsiniz. Bu uygulama KIMLIĞI eklenmediğinden Windows sanal masaüstü (klasik) kaynaklarının akış keşfi engellenir.

10. **Koşullar**  >  **istemci uygulamaları**' na gidin ve ilkeyi uygulamak istediğiniz yeri seçin:
    
    - İlkenin Web istemcisine uygulanmasını istiyorsanız **tarayıcı** ' yı seçin.
    - İlkeyi diğer istemcilere uygulamak istiyorsanız **mobil uygulamalar ve Masaüstü istemcileri '** ni seçin.
    - İlkeyi tüm istemcilere uygulamak istiyorsanız her iki onay kutusunu da seçin.
   
    > [!div class="mx-imgBorder"]
    > ![Istemci uygulamaları sayfasının ekran görüntüsü. Kullanıcı mobil uygulamalar ve Masaüstü istemcileri onay kutusunu seçti.](media/select-apply.png)

11. Uygulamanızı seçtikten sonra **Seç**' i seçin ve **bitti**' yi seçin.

    > [!div class="mx-imgBorder"]
    > ![Bulut uygulamaları veya eylemler sayfasının ekran görüntüsü. Windows sanal masaüstü ve Windows sanal masaüstü Istemci uygulamaları kırmızı renkle vurgulanır.](media/cloud-apps-enterprise.png)

    >[!NOTE]
    >Seçmek istediğiniz uygulamanın uygulama KIMLIĞINI bulmak için **Kurumsal uygulamalar** ' a gidin ve uygulama türü açılan menüsünde **Microsoft uygulamaları** ' nı seçin.

12. **Erişim denetimleri**  >  **izni** altında **erişim ver**' i seçin, **Multi-Factor Authentication gerektir**' i seçin ve ardından öğesini **seçin**.
13. **Erişim denetimleri**  >  **oturumu**' nun altında, **oturum açma sıklığı**' nı seçin, değeri istemler arasında istediğiniz zamana ayarlayın ve ardından **Seç**' i seçin. Örneğin, bir bağlantı en son bir saat sonra başlatıldığında, değeri **1** ' e ve birim- **saat** olarak ayarlamak için çok faktörlü kimlik doğrulaması gerekir.
14. Ayarlarınızı doğrulayın ve **ilke** ayarını **Açık** olarak ayarlayın.
15. İlkenizi etkinleştirmek için **Oluştur** ' u seçin.

>[!NOTE]
>Tarayıcınız aracılığıyla Windows sanal masaüstü 'nde oturum açmak için Web istemcisini kullandığınızda günlük, istemci uygulama KIMLIĞINI a85cf173-4192-42f8-81fa-777a763e6e2c (Windows sanal masaüstü istemcisi) olarak listeler. Bunun nedeni, istemci uygulamasının koşullu erişim ilkesinin ayarlandığı sunucu uygulama KIMLIĞINE dahili olarak bağlı olması. 

## <a name="next-steps"></a>Sonraki adımlar

- [Koşullu erişim ilkeleri hakkında daha fazla bilgi edinin](../active-directory/conditional-access/concept-conditional-access-policies.md)

- [Kullanıcı oturum açma sıklığı hakkında daha fazla bilgi edinin](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md#user-sign-in-frequency)
