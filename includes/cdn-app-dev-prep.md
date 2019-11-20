---
author: zhangmanling
ms.service: azure-cdn
ms.topic: include
ms.date: 11/21/2018
ms.author: mazha
ms.openlocfilehash: f21a768733456a6c00e5a87612f3055dd76d416c
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67594136"
---
## <a name="prerequisites"></a>Önkoşullar
CDN yönetim kodu yazmadan önce Azure Resource Manager ile etkileşim kurmak kod etkinleştirmek için bazı hazırlıkları yapmanız gerekir. Bu hazırlık yapmanız için gereken:

* Bu öğreticide oluşturduğunuz CDN profilini içerecek bir kaynak grubu oluşturun
* Uygulama için kimlik doğrulaması sağlamak için Azure Active Directory'yi yapılandırma
* Böylece yalnızca yetkili kullanıcıların Azure AD kiracınızdan CDN profili ile etkileşim kurabilir kaynak grubuna izinler geçerlidir

### <a name="creating-the-resource-group"></a>Kaynak grubunu oluşturma
1. [Azure Portal](https://portal.azure.com)’da oturum açın.
2. **Kaynak oluştur**’a tıklayın.
3. Arama **kaynak grubu** ve kaynak grubu bölmesinden **Oluştur**.

    ![Yeni bir kaynak grubu oluşturma](./media/cdn-app-dev-prep/cdn-new-rg-1-include.png)
3. Kaynak grubunuzu adlandırın *CdnConsoleTutorial*.  Aboneliğinizi seçin ve size yakın bir konum seçin.  İsterseniz, tıklayabilirsiniz **panoya Sabitle** portalda kaynak grubunu panoya sabitlemek için onay kutusu.  Sabitleme, daha sonra bulmayı kolaylaştırır.  Seçimlerinizi yaptıktan sonra tıklayın **Oluştur**.

    ![Kaynak grubu adlandırma](./media/cdn-app-dev-prep/cdn-new-rg-2-include.png)
4. Panonuza sabitlemediyseniz kaynak grubu oluşturduktan sonra bunu tıklayarak bulabilirsiniz **Gözat**, ardından **kaynak grupları**.  Açmak için kaynak grubuna tıklayın.  Not, **abonelik kimliği**. Daha sonra ihtiyacımız.

    ![Kaynak grubu adlandırma](./media/cdn-app-dev-prep/cdn-subscription-id-include.png)

### <a name="creating-the-azure-ad-application-and-applying-permissions"></a>Azure AD uygulaması oluşturma ve izinleri
Azure Active Directory ile uygulama kimlik doğrulamasına iki yaklaşım vardır: Tek tek kullanıcılar veya hizmet sorumlusu. Bir hizmet sorumlusu, Windows hizmet hesabında benzerdir.  Belirli bir kullanıcı bir CDN profili ile etkileşim kurmak için izinleri vermek yerine, izinler, bunun yerine hizmet sorumlusuna verilir.  Hizmet sorumluları, otomatik, etkileşimli olmayan işlemler için kullanılır.  Bu öğreticide, bir etkileşimli bir konsol uygulaması yazma olsa da, hizmet sorumlusu yaklaşıma odaklanacağız.

Bir hizmet sorumlusu oluşturma işlemi, bir Azure Active Directory uygulaması oluşturma gibi birkaç adımdan oluşur.  Oluşturmak için oluşturacağız [bu öğreticiden yararlanın](../articles/active-directory/develop/howto-create-service-principal-portal.md).

> [!IMPORTANT]
> Tüm adımları izlediğinizden emin olun [bağlantılı öğretici](../articles/active-directory/develop/howto-create-service-principal-portal.md).  Bu *önemli* , tam olarak açıklandığı gibi tamamlayın.  Not aldığınızdan emin olun, **Kiracı kimliği**, **Kiracı etki alanı adı** (genellikle bir *. onmicrosoft.com* etki alanı özel bir etki alanı belirtmediğiniz sürece), **istemci kimliği** , ve **istemci kimlik doğrulama anahtarı**daha sonra bu bilgiye ihtiyacımız gibi.  Koruma sağlamak dikkatli olun, **istemci kimliği** ve **istemci kimlik doğrulama anahtarı**, bu kimlik bilgileri herkes tarafından hizmet sorumlusu olarak işlemlerini yürütmek için kullanılabilir.
>
> Yapılandırma çok kiracılı uygulama adlı adım aldığınızda seçin **Hayır**.
>
> Adım aldığınızda [uygulamanızı bir role atama](../articles/active-directory/develop/howto-create-service-principal-portal.md#assign-the-application-to-a-role), daha önce oluşturduğunuz kaynak grubunu kullanın *CdnConsoleTutorial*, ancak yerine **okuyucu** rolü atama **CDN profili katkıda bulunanı** rol.  Uygulama atandıktan sonra **CDN profili katkıda bulunanı** , kaynak grubu, Bu öğretici için dönüş rolü. 
>
>

Hizmet sorumlusu oluşturup atanan **CDN profili katkıda bulunanı** rolü **kullanıcılar** , kaynak grubu dikey penceresinde aşağıdaki görüntüye benzer görünmelidir.

![Kullanıcılar dikey penceresi](./media/cdn-app-dev-prep/cdn-service-principal-include.png)

### <a name="interactive-user-authentication"></a>Etkileşimli kullanıcı kimlik doğrulaması
Bir hizmet sorumlusu yerine etkileşimli kullanıcı kimlik doğrulaması yerine yoktur, bir hizmet sorumlusu için benzer bir işlemdir.  Aslında, yordamın aynısını izleyin, ancak birkaç küçük değişiklik gerekir.

> [!IMPORTANT]
> Yalnızca bir hizmet sorumlusu yerine tek tek kullanıcı kimlik doğrulaması kullanmayı tercih sonraki adımları izleyin.
>
>

1. Yerine, uygulamanızı oluştururken **Web uygulaması**, seçin **yerel uygulama**.

    ![Yerel uygulama](./media/cdn-app-dev-prep/cdn-native-application-include.png)
2. Sonraki sayfada, sizden istenir bir **yeniden yönlendirme URI'si**.  URI doğrulanması gerekmez, ancak ne girdiğiniz unutmayın. Daha sonra ihtiyacınız.
3. Oluşturmaya gerek yoktur. bir **istemci kimlik doğrulama anahtarı**.
4. Hizmet sorumlusu atamak yerine **CDN profili katkıda bulunanı** rolü yapacağımız bireysel kullanıcılara ve gruplara atamak için.  Bu örnekte, ı atadığınız görebilirsiniz *CDN Tanıtımı kullanıcı* için **CDN profili katkıda bulunanı** rol.  

    ![Tek tek kullanıcı erişimi](./media/cdn-app-dev-prep/cdn-aad-user-include.png)
