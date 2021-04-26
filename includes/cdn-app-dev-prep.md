---
author: zhangmanling
ms.service: azure-cdn
ms.topic: include
ms.date: 11/21/2018
ms.author: mazha
ms.openlocfilehash: 4967991b0edaa854acbf6b308596859d662311fe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95993371"
---
## <a name="prerequisites"></a>Önkoşullar
CDN yönetim kodu yazmadan önce kodun Azure Resource Manager etkileşime geçmesini sağlamak için bazı hazırlıklar yapmanız gerekir. Bu hazırlığı yapmak için şunları yapmanız gerekir:

* Bu öğreticide oluşturulan CDN profilini içeren bir kaynak grubu oluşturun
* Uygulama için kimlik doğrulaması sağlamak üzere Azure Active Directory yapılandırma
* Yalnızca Azure AD kiracınızdaki yetkili kullanıcıların CDN profiliyle etkileşime geçmesini sağlamak için kaynak grubuna izinleri uygulayın

### <a name="creating-the-resource-group"></a>Kaynak grubunu oluşturma
1. [Azure portalında](https://portal.azure.com)oturum açın.
2. **Kaynak oluştur ' a** tıklayın.
3. **Kaynak grubu** araması yapın ve kaynak grubu bölmesinde **Oluştur**' a tıklayın.

    ![Yeni bir kaynak grubu oluşturma](./media/cdn-app-dev-prep/cdn-new-rg-1-include.png)
3. Kaynak grubunuzu *Cdnconsoleöğreticisi* olarak adlandırın.  Aboneliğinizi seçin ve yakınınızdaki bir konum seçin.  İsterseniz, kaynak grubunu portalda panoya sabitlemek için **panoya sabitle** onay kutusunu tıklayabilirsiniz.  Sabitleme daha sonra bulmayı kolaylaştırır.  Seçimlerinizi yaptıktan sonra **Oluştur**' a tıklayın.

    ![Kaynak grubu iletişim kutusunun ekran görüntüsü.](./media/cdn-app-dev-prep/cdn-new-rg-2-include.png)
4. Kaynak Grubu oluşturulduktan sonra, bunu panonuza sabitetmediyseniz, bu dosyayı, **Araştır**' a ve ardından **kaynak grupları**' na tıklayarak bulabilirsiniz.  Açmak için kaynak grubuna tıklayın.  **ABONELIK kimliğinizi** bir yere getirin. Daha sonra ihtiyacımız var.

    ![C D N konsol öğreticisi bölümünün ekran görüntüsü.](./media/cdn-app-dev-prep/cdn-subscription-id-include.png)

### <a name="creating-the-azure-ad-application-and-applying-permissions"></a>Azure AD uygulaması oluşturma ve izinleri uygulama
Azure Active Directory ile uygulama kimlik doğrulamasında iki yaklaşım vardır: bireysel kullanıcılar veya hizmet sorumlusu. Hizmet sorumlusu, Windows 'daki hizmet hesabına benzer.  CDN profilleriyle etkileşmek üzere belirli bir Kullanıcı izinleri vermek yerine, izinler hizmet sorumlusu için verilir.  Hizmet sorumluları genellikle otomatik, etkileşimli olmayan süreçler için kullanılır.  Bu öğretici etkileşimli bir konsol uygulaması yazıyor olsa da hizmet sorumlusu yaklaşımına odaklanacağız.

Hizmet sorumlusu oluşturmak, Azure Active Directory bir uygulama oluşturma dahil olmak üzere çeşitli adımlardan oluşur.  Bunu oluşturmak için [Bu öğreticiyi takip](../articles/active-directory/develop/howto-create-service-principal-portal.md)edeceğiz.

> [!IMPORTANT]
> [Bağlantılı öğreticideki](../articles/active-directory/develop/howto-create-service-principal-portal.md)tüm adımları izlediğinizden emin olun.  Bunu tam olarak açıklandığı gibi doldurmanız *önemlidir* .  Bu bilgilere daha sonra ihtiyaç duyduğumuz sürece **KIRACı kimliğinizi**, **kiracı etki alanı adını** (genellikle bir özel etki alanı belirtmediğiniz sürece bir *. onmicrosoft.com* etki alanı), **istemci kimliğini** ve **istemci kimlik doğrulama anahtarını** aklınızda olduğunuzdan emin olun.  **ISTEMCI kimliği** ve **istemci kimlik doğrulama anahtarınızı** koruma konusunda dikkatli olun, çünkü bu kimlik bilgileri, hizmet sorumlusu olarak işlemleri yürütmek üzere herkes tarafından kullanılabilir.
>
> Çok kiracılı uygulama yapılandırma adlı adıma geldiğinizde **Hayır**' ı seçin.
>
> [Uygulamayı bir role atama](../articles/active-directory/develop/howto-create-service-principal-portal.md#assign-a-role-to-the-application)adımına geldiğinizde, daha önce oluşturulmuş olan *Cdnconsoleöğretici adlı* kaynak grubunu kullanın, ancak **okuyucu** rolü yerine, **CDN profili katkıda** bulunan rolünü atayın.  Uygulamayı kaynak grubunuza **CDN profili katılımcısı** rolü atadıktan sonra, Bu öğreticiye dönün. 
>
>

Hizmet sorumlunuzu oluşturduktan ve **CDN profili katılımcısı** rolüne atadıktan sonra, kaynak grubunuz için **Kullanıcılar** dikey penceresi aşağıdaki görüntüye benzer görünmelidir.

![Kullanıcılar dikey penceresi](./media/cdn-app-dev-prep/cdn-service-principal-include.png)

### <a name="interactive-user-authentication"></a>Etkileşimli kullanıcı kimlik doğrulaması
Bir hizmet sorumlusu yerine, etkileşimli bireysel kullanıcı kimlik doğrulamasına sahip olmanız tercih ediyorsanız, işlem hizmet sorumlusu için buna benzer.  Aslında, aynı yordamı izlemeniz, ancak küçük değişiklikler yapmanız gerekir.

> [!IMPORTANT]
> Yalnızca bir hizmet sorumlusu yerine bireysel kullanıcı kimlik doğrulaması kullanmayı seçerseniz bu sonraki adımları izleyin.
>
>

1. Uygulamanızı oluştururken, **Web uygulaması** yerine **yerel uygulama**' yı seçin.

    ![Yerel uygulama](./media/cdn-app-dev-prep/cdn-native-application-include.png)
2. Sonraki sayfada, size **yeniden yönlendirme URI 'si** sorulur.  URI doğrulanmaz, ancak girdiğinizi unutmayın. Daha sonra ihtiyacınız olacak.
3. **İstemci kimlik doğrulama anahtarı** oluşturmaya gerek yoktur.
4. **CDN profili katkıda bulunan** rolüne bir hizmet sorumlusu atamak yerine, bireysel kullanıcılar veya gruplar atayacağız.  Bu örnekte, CDN  *tanıtım kullanıcısını* **CDN profili katkıda** bulunan rolüne atadım.  

    ![Bireysel kullanıcı erişimi](./media/cdn-app-dev-prep/cdn-aad-user-include.png)
