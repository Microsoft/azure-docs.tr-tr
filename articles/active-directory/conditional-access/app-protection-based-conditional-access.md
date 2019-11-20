---
title: Azure Active Directory | için koşullu erişimle Cloud App erişimi için uygulama koruma ilkesi iste | Microsoft Docs
description: Azure Active Directory ' de koşullu erişim ile Cloud App erişimi için uygulama koruma ilkesi yapmayı isteme hakkında bilgi edinin.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 4/4/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3e8b7cc1f3a8431986ffbaac604ec5863236f112
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68357098"
---
# <a name="require-app-protection-policy-for-cloud-app-access-with-conditional-access-preview"></a>Koşullu erişimle Cloud App erişimi için uygulama koruma ilkesi gerektir (Önizleme)

Çalışanlarınız hem kişisel hem de iş görevleri için mobil cihazları kullanır. Çalışanlarınızın üretken olduğundan emin olmaya devam ederken, veri kaybını da engellemek isteyebilirsiniz. Azure Active Directory (Azure AD) koşullu erişimi sayesinde, bulut uygulamalarınıza erişimi kısıtlayarak kurumsal verilerinizi koruyabilirsiniz. Önce bir uygulama koruma ilkesiyle istemci uygulamaları kullanın.

Bu makalede, verilere erişim verilmeden önce bir uygulama koruma ilkesi gerektirebilecek koşullu erişim ilkelerinin nasıl yapılandırılacağı açıklanır.

## <a name="overview"></a>Genel Bakış

[Azure AD koşullu erişimi](overview.md)sayesinde yetkili kullanıcıların kaynaklarınıza nasıl erişeceğine ilişkin ince ayar yapabilirsiniz. Örneğin, bulut uygulamalarınıza erişimi güvenilir cihazlarla sınırlayabilirsiniz.

Şirketinizin verilerini korumaya yardımcı olmak için [Intune uygulama koruma ilkelerini](https://docs.microsoft.com/intune/app-protection-policy) kullanabilirsiniz. Intune uygulama koruma ilkeleri bir mobil cihaz yönetimi (MDM) çözümü gerektirmez. Cihazları cihaz yönetimi çözümünde kaydederek veya kaydetmeden Şirket verilerinizi koruyabilirsiniz.

Koşullu erişim Azure Active Directory, bulut uygulamalarınıza erişimi, Intune 'un Azure AD 'ye bir uygulama koruma ilkesi alındığında bildirdiği istemci uygulamalarına kısıtlar. Örneğin, Exchange Online 'a erişimi bir Intune uygulama koruma ilkesine sahip Outlook uygulamasıyla kısıtlayabilirsiniz.

Koşullu erişim terminolojisinde, bu istemci uygulamalarının ilke bir *Uygulama koruma ilkesiyle*korunduğu bilinmektedir.  

![Koşullu Erişim](./media/app-protection-based-conditional-access/05.png)

İlkeyle korunan istemci uygulamalarının listesi için bkz. [Uygulama koruma ilkesi gereksinimi](technical-reference.md#approved-client-app-requirement).

Uygulama koruma tabanlı koşullu erişim ilkelerini, [cihaz tabanlı koşullu erişim ilkeleri](require-managed-devices.md)gibi diğer ilkelerle birleştirebilirsiniz. Bu şekilde, hem kişisel hem de kurumsal cihazların verilerini koruma konusunda esneklik sağlayabilirsiniz.

> [!NOTE]
> Koşullu erişim uygulama koruma ilkeleri B2B kullanıcılarına uygulanamıyor çünkü bu, davet eden kuruluşun B2B kullanıcısının ana kuruluşuna görünürlüğü yoktur.

## <a name="benefits-of-app-protection-based-conditional-access-requirement"></a>Uygulama koruma tabanlı koşullu erişim gereksiniminin avantajları

İOS ve Android için Intune tarafından yönetilen bir cihaz için raporlanan uyumluluğa benzer şekilde, Intune artık uygulama koruma ilkesi uygulanmışsa Azure AD 'ye rapor verdi. Koşullu erişim, bu ilkeyi bir erişim denetimi olarak kullanabilir. Bu yeni koşullu erişim ilkesi, uygulama koruma ilkesi güvenliği artırır. Yönetici hatalarına karşı koruma sağlar, örneğin:

- Intune lisansı olmayan kullanıcılar.
- Intune uygulama koruma ilkesi alabilen kullanıcılar.
- İlke almak üzere yapılandırılmayan Intune uygulama koruma ilkesi uygulamaları.

## <a name="before-you-begin"></a>Başlamadan önce

Bu makalede, hakkında bilgi sahibi olduğunuz varsayılmaktadır:

- [Uygulama koruma ilkesi gereksinimi](technical-reference.md#app-protection-policy-requirement) teknik başvurusu.
- [Onaylanan istemci uygulaması gereksinimi](technical-reference.md#approved-client-app-requirement) teknik başvurusu.
- [Azure Active Directory ' deki Koşullu erişimin](overview.md)temel kavramları.
- [Koşullu erişim ilkesini yapılandırma](app-based-mfa.md).

## <a name="prerequisites"></a>Önkoşullar

Uygulama koruma tabanlı bir koşullu erişim ilkesi oluşturmak için şunları yapmanız gerekir:

- Bir Enterprise Mobility + Security veya Azure Active Directory Premium aboneliğine ve Intune 'a sahip olmanız gerekir.
- Kullanıcıların Enterprise Mobility + Security veya Azure AD + Intune lisansına sahip olduğundan emin olun.
- İstemci uygulamasının, uygulama koruma ilkesi almak için Intune 'da yapılandırıldığından emin olun.
- Kullanıcıların Intune uygulama koruma ilkesini almak için Intune 'da yapılandırıldığından emin olun.

## <a name="app-protection-based-policy-for-exchange-online"></a>Exchange Online için uygulama koruma tabanlı ilke

Bu senaryo, Exchange Online 'a erişmek için uygulama koruma tabanlı bir koşullu erişim ilkesinden oluşur.

### <a name="scenario-playbook"></a>Senaryo PlayBook

Bu senaryo, bir kullanıcının şu şekilde olduğunu varsayar:

- Exchange 'e bağlanmak için iOS veya Android 'de yerel bir posta uygulaması kullanarak e-postayı yapılandırır.
- Erişimin yalnızca Outlook uygulaması kullanılarak kullanılabileceğini gösteren bir e-posta alır.
- Bağlantıyı kullanarak uygulamayı indirir.
- Outlook uygulamasını açar ve Azure AD kimlik bilgileriyle oturum açar.
- Devam etmek için **Microsoft Authenticator uygulamasını** ya da **Intune şirket portalı** yüklemesi istenir.
- Uygulamayı kurar ve devam etmek için Outlook uygulamasına geri döner.
- Bir cihazı kaydetmesi istenir.
- , Bir Intune uygulama koruma ilkesi alabilir.
- E-postaya erişebilir.

Şirket verilerine erişmek için uygulama üzerinde herhangi bir Intune uygulama koruma ilkesi olması gerekir. İlkeler kullanıcıdan uygulamayı yeniden başlatmasını veya ek bir PIN kullanmasını isteyebilir. İlkeler uygulama ve platform için yapılandırılmışsa bu durum budur.

### <a name="configuration"></a>Yapılandırma

**1. Adım: Exchange Online için bir Azure AD koşullu erişim ilkesi yapılandırma**

Bu adımdaki koşullu erişim ilkesi için aşağıdaki bileşenleri yapılandırın:

![Koşullu Erişim](./media/app-protection-based-conditional-access/01.png)

1. Koşullu erişim ilkenizin adını girin.
1. **Atamalar**' ın altında, **Kullanıcılar ve gruplar**' da her bir koşullu erişim ilkesi için en az bir kullanıcı veya grup seçin.
1. **Bulut uygulamaları**' nda **Office 365 Exchange Online**' ı seçin.

   ![Koşullu Erişim](./media/app-protection-based-conditional-access/07.png)

1. **Koşullar**' da **cihaz platformlarını** ve **istemci uygulamalarını yapılandırma (Önizleme)** :
   1. **Cihaz platformları**' nda **Android** ve **iOS**' ı seçin.

      ![Koşullu Erişim](./media/app-protection-based-conditional-access/03.png)

   1. **İstemci uygulamaları (Önizleme)** bölümünde **mobil uygulamalar ve Masaüstü istemcileri** ve **modern kimlik doğrulama istemcileri**' ni seçin.

      ![Koşullu Erişim](./media/app-protection-based-conditional-access/91.png)

1. **Erişim denetimleri**altında **Uygulama koruma ilkesi gerektir (Önizleme)** seçeneğini belirleyin.

   ![Koşullu Erişim](./media/app-protection-based-conditional-access/05.png)

**2. Adım: ActiveSync (EAS) ile Exchange Online için bir Azure AD koşullu erişim ilkesi yapılandırma**

Bu adımdaki koşullu erişim ilkesi için aşağıdaki bileşenleri yapılandırın:

![Koşullu Erişim](./media/app-protection-based-conditional-access/06.png)

1. Koşullu erişim ilkenizin adını girin.
1. **Atamalar**' ın altında, **Kullanıcılar ve gruplar**' da her bir koşullu erişim ilkesi için en az bir kullanıcı veya grup seçin.
1. **Bulut uygulamaları**' nda **Office 365 Exchange Online**' ı seçin.

   ![Koşullu Erişim](./media/app-protection-based-conditional-access/07.png)

1. **Koşullarda**, **istemci uygulamalarını yapılandırın (Önizleme)** . 

   1. **İstemci uygulamaları (Önizleme)** bölümünde **mobil uygulamalar ve Masaüstü Istemcileri** ve **Exchange ActiveSync istemcileri**' ni seçin.

      ![Koşullu Erişim](./media/app-protection-based-conditional-access/92.png)

   1. **Erişim denetimleri**altında **Uygulama koruma ilkesi gerektir (Önizleme)** seçeneğini belirleyin.

      ![Koşullu Erişim](./media/app-protection-based-conditional-access/05.png)

**Adım 3: İOS ve Android istemci uygulamaları için Intune uygulama koruma ilkesini yapılandırma**

![Koşullu Erişim](./media/app-protection-based-conditional-access/09.png)

Daha fazla bilgi için bkz. [Microsoft Intune uygulamaları ve verileri koruma](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).

## <a name="app-protection-based-or-compliant-device-policy-for-exchange-online"></a>Exchange Online için uygulama koruma tabanlı veya uyumlu cihaz ilkesi

Bu senaryo, Exchange Online 'a erişmek için uygulama koruma tabanlı veya uyumlu bir cihaz koşullu erişim ilkesinden oluşur.

### <a name="scenario-playbook"></a>Senaryo PlayBook

Bu senaryo şunları varsayar:
 
- Bir Kullanıcı, şirket cihazlarıyla veya olmadan zaten kayıtlı.
- Uygulama korumalı bir uygulama kullanılarak Azure AD 'ye kaydolmayan ve kayıtlı olmayan kullanıcıların, kaynaklara erişmek için bir cihazı kaydetmesi gerekir.
- Uygulama korumalı uygulamasını kullanan kayıtlı kullanıcıların, cihazı yeniden kaydetmesi gerekmez.
- Kullanıcı kayıtlı değilse bir Intune uygulama koruma ilkesi alabilir.
- Kullanıcı, kayıtlı değilse Outlook ve Intune uygulama koruma ilkesi ile e-postaya erişebilir.
- Cihaz kaydedildiyse Kullanıcı Outlook ile e-postaya erişebilir.

### <a name="configuration"></a>Yapılandırma

**1. Adım: Exchange Online için bir Azure AD koşullu erişim ilkesi yapılandırma**

Bu adımdaki koşullu erişim ilkesi için aşağıdaki bileşenleri yapılandırın:

![Koşullu Erişim](./media/app-protection-based-conditional-access/62.png)

1. Koşullu erişim ilkenizin adını girin.
1. **Atamalar**' ın altında, **Kullanıcılar ve gruplar**' da her bir koşullu erişim ilkesi için en az bir kullanıcı veya grup seçin.
1. **Bulut uygulamaları**' nda **Office 365 Exchange Online**' ı seçin. 

   ![Koşullu Erişim](./media/app-protection-based-conditional-access/07.png)

1. **Koşullarda**, **cihaz platformlarını** ve **istemci uygulamaları (Önizleme)** yapılandırın. 
 
   1. **Cihaz platformları**' nda **Android** ve **iOS**' ı seçin.

      ![Koşullu Erişim](./media/app-protection-based-conditional-access/03.png)

   1. **İstemci uygulamaları (Önizleme)** bölümünde **mobil uygulamalar ve Masaüstü istemcileri** ve **modern kimlik doğrulama istemcileri**' ni seçin.

      ![Koşullu Erişim](./media/app-protection-based-conditional-access/91.png)

5. **Erişim denetimleri**altında, aşağıdaki seçenekleri belirleyin:
   - **Cihazın uyumlu olarak işaretlenmesini gerektir**
   - **Uygulama koruma ilkesi gerektir (Önizleme)**
   - **Seçili denetimlerden birini gerektir**   
 
      ![Koşullu Erişim](./media/app-protection-based-conditional-access/11.png)

**2. Adım: ActiveSync ile Exchange Online için bir Azure AD koşullu erişim ilkesi yapılandırma**

Bu adımdaki koşullu erişim ilkesi için aşağıdaki bileşenleri yapılandırın:

![Koşullu Erişim](./media/app-protection-based-conditional-access/06.png)

1. Koşullu erişim ilkenizin adını girin.
1. **Atamalar**' ın altında, **Kullanıcılar ve gruplar**' da her bir koşullu erişim ilkesi için en az bir kullanıcı veya grup seçin.
1. **Bulut uygulamaları**' nda **Office 365 Exchange Online**' ı seçin. 

   ![Koşullu Erişim](./media/app-protection-based-conditional-access/07.png)

1. **Koşullarda**, **istemci uygulamalarını yapılandırın (Önizleme)** . 

   **İstemci uygulamaları (Önizleme)** bölümünde **mobil uygulamalar ve Masaüstü Istemcileri** ve **Exchange ActiveSync istemcileri**' ni seçin.

   ![Koşullu Erişim](./media/app-protection-based-conditional-access/92.png)

1. **Erişim denetimleri**altında, aşağıdaki seçenekleri belirleyin:
   - **Cihazın uyumlu olarak işaretlenmesini gerektir**
   - **Uygulama koruma ilkesi gerektir (Önizleme)**
   - **Seçili denetimlerden birini gerektir**

      ![Koşullu Erişim](./media/app-protection-based-conditional-access/11.png)

**Adım 3: İOS ve Android istemci uygulamaları için Intune uygulama koruma ilkesini yapılandırma**

![Koşullu Erişim](./media/app-protection-based-conditional-access/09.png)

Daha fazla bilgi için bkz. [Microsoft Intune uygulamaları ve verileri koruma](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).

## <a name="app-protection-based-and-compliant-device-policy-for-exchange-online"></a>Exchange Online için uygulama koruma tabanlı ve uyumlu cihaz ilkesi

Bu senaryo, Exchange Online 'a erişmek için uygulama koruma tabanlı ve uyumlu bir cihaz koşullu erişim ilkesinden oluşur.

### <a name="scenario-playbook"></a>Senaryo PlayBook

Bu senaryo, bir kullanıcının şu şekilde olduğunu varsayar:
 
- Exchange 'e bağlanmak için iOS veya Android 'de yerel bir posta uygulaması kullanarak e-postayı yapılandırır.
- Erişimin cihazının kaydedilmesini gerektirdiğini belirten bir e-posta alır.
- Intune Şirket Portalı indirir ve portalda oturum açar.
- Postayı denetler ve Outlook uygulamasını kullanması istenir.
- Outlook uygulamasını indirir.
- Outlook uygulamasını açar ve kayıt sırasında kullanılan kimlik bilgilerini girer.
- , Bir Intune uygulama koruma ilkesi alabilir.
- Outlook ve bir Intune uygulama koruma ilkesiyle e-postaya erişebilir.

Şirket verilerine erişim verilmeden önce herhangi bir Intune uygulama koruma ilkesi etkinleştirilir. İlkeler kullanıcıdan uygulamayı yeniden başlatmasını veya ek bir PIN kullanmasını isteyebilir. İlkeler uygulama ve platform için yapılandırılmışsa bu durum budur.

### <a name="configuration"></a>Yapılandırma

**1. Adım: Exchange Online için bir Azure AD koşullu erişim ilkesi yapılandırma**

Bu adımdaki koşullu erişim ilkesi için aşağıdaki bileşenleri yapılandırın:

![Koşullu Erişim](./media/app-protection-based-conditional-access/01.png)

1. Koşullu erişim ilkenizin adını girin.
1. **Atamalar**' ın altında, **Kullanıcılar ve gruplar**' da her bir koşullu erişim ilkesi için en az bir kullanıcı veya grup seçin.
1. **Bulut uygulamaları**' nda **Office 365 Exchange Online**' ı seçin. 

   ![Koşullu Erişim](./media/app-protection-based-conditional-access/07.png)

1. **Koşullarda**, **cihaz platformlarını** ve **istemci uygulamaları (Önizleme)** yapılandırın. 
   1. **Cihaz platformları**' nda **Android** ve **iOS**' ı seçin.

      ![Koşullu Erişim](./media/app-protection-based-conditional-access/03.png)

   1. **İstemci uygulamaları (Önizleme)** bölümünde **mobil uygulamalar ve Masaüstü istemcileri** ve **modern kimlik doğrulama istemcileri**' ni seçin.

      ![Koşullu Erişim](./media/app-protection-based-conditional-access/91.png)

1. **Erişim denetimleri**altında, aşağıdaki seçenekleri belirleyin:
   - **Cihazın uyumlu olarak işaretlenmesini gerektir**
   - **Uygulama koruma ilkesi gerektir (Önizleme)**
   - **Seçili tüm denetimleri gerektir**   
 
      ![Koşullu Erişim](./media/app-protection-based-conditional-access/13.png)

**2. Adım: ActiveSync ile Exchange Online için bir Azure AD koşullu erişim ilkesi yapılandırma**

Bu adımdaki koşullu erişim ilkesi için aşağıdaki bileşenleri yapılandırın:

![Koşullu Erişim](./media/app-protection-based-conditional-access/06.png)

1. Koşullu erişim ilkenizin adını girin.
1. **Atamalar**' ın altında, **Kullanıcılar ve gruplar**' da her bir koşullu erişim ilkesi için en az bir kullanıcı veya grup seçin.
1. **Bulut uygulamaları**' nda **Office 365 Exchange Online**' ı seçin. 

   ![Koşullu Erişim](./media/app-protection-based-conditional-access/07.png)

1. **Koşullarda**, **istemci uygulamalarını yapılandırın (Önizleme)** . 

   **İstemci uygulamaları (Önizleme)** bölümünde **mobil uygulamalar ve Masaüstü Istemcileri** ve **Exchange ActiveSync istemcileri**' ni seçin.

   ![Koşullu Erişim](./media/app-protection-based-conditional-access/92.png)

1. **Erişim denetimleri**altında, aşağıdaki seçenekleri belirleyin:
   - **Cihazın uyumlu olarak işaretlenmesini gerektir**
   - **Uygulama koruma ilkesi gerektir (Önizleme)**
   - **Seçili tüm denetimleri gerektir**   
 
      ![Koşullu Erişim](./media/app-protection-based-conditional-access/13.png)

**Adım 3: İOS ve Android istemci uygulamaları için Intune uygulama koruma ilkesini yapılandırma**

![Koşullu Erişim](./media/app-protection-based-conditional-access/09.png)

Daha fazla bilgi için bkz. [Microsoft Intune uygulamaları ve verileri koruma](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).

## <a name="app-protection-based-or-app-based-policy-for-exchange-online-and-sharepoint-online"></a>Exchange Online ve SharePoint Online için uygulama koruma tabanlı veya uygulama tabanlı ilke

Bu senaryo, Exchange Online ve SharePoint Online 'a erişim için uygulama koruma tabanlı veya onaylanan uygulamalar ilkesinden oluşur.

### <a name="scenario-playbook"></a>Senaryo PlayBook

Bu senaryo, bir kullanıcının şu şekilde olduğunu varsayar:

- Uygulama koruma İlkesi gereksinimini ya da onaylanan uygulamalar gereksinimini destekleyen uygulamalar listesinde bulunan istemci uygulamalarını yapılandırır.  
- , Uygulama koruma İlkesi gereksinimini karşılayan istemci uygulamalarını kullanır ve bir Intune uygulama koruma ilkesi alabilir.
- , Intune uygulama koruma ilkesini destekleyen onaylanan uygulamalar İlkesi gereksinimini karşılayan istemci uygulamalarını kullanır.
- E-postaya veya belgelere erişmek için uygulamayı açar.
- Outlook uygulamasını açar ve Azure AD kimlik bilgileriyle oturum açar.
- Henüz yüklenmemişse iOS kullanımı için Microsoft Authenticator veya Android kullanımı için Intune Şirket Portalı yüklemesi istenir.
- Uygulamayı yükleyebilir ve devam etmek için Outlook uygulamasına geri dönebilir.
- Bir cihazı kaydetmesi istenir.
- , Bir Intune uygulama koruma ilkesi alabilir.
- Outlook ve bir Intune uygulama koruma ilkesiyle e-postaya erişebilir.
- , Uygulama koruma ilkesi gereksinimi üzerine değil, ancak onaylanan uygulama gereksiniminde listelenen bir uygulamayla sitelere ve belgelere erişebilir.

Şirket verilerine erişim verilmeden önce herhangi bir Intune uygulama koruma ilkesi gerekir. İlkeler kullanıcıdan uygulamayı yeniden başlatmasını veya ek bir PIN kullanmasını isteyebilir. İlkeler uygulama ve platform için yapılandırılmışsa bu durum budur.

**Açıklamalar**

- Uygulama koruma tabanlı ve uygulama tabanlı koşullu erişim ilkelerini desteklemek istiyorsanız bu senaryoyu kullanabilirsiniz.
- Bu *veya* ilkede, uygulama koruma ilkesi gereksinimi olan uygulamalar, onaylanan istemci uygulamaları gereksiniminden önce önce erişim için değerlendirilir.

### <a name="configuration"></a>Yapılandırma

**1. Adım: Exchange Online için bir Azure AD koşullu erişim ilkesi yapılandırma**

Bu adımdaki koşullu erişim ilkesi için aşağıdaki bileşenleri yapılandırın:

![Koşullu Erişim](./media/app-protection-based-conditional-access/62.png)

1. Koşullu erişim ilkenizin adını girin.
1. **Atamalar**' ın altında, **Kullanıcılar ve gruplar**' da her bir koşullu erişim ilkesi için en az bir kullanıcı veya grup seçin.
1. **Bulut uygulamaları**' nda **Office 365 Exchange Online**' ı seçin. 

   ![Koşullu Erişim](./media/app-protection-based-conditional-access/02.png)

1. **Koşullarda**, **cihaz platformlarını** ve **istemci uygulamaları (Önizleme)** yapılandırın. 
   1. **Cihaz platformları**' nda **Android** ve **iOS**' ı seçin.

      ![Koşullu Erişim](./media/app-protection-based-conditional-access/03.png)

   1. **İstemci uygulamaları (Önizleme)** bölümünde **mobil uygulamalar ve Masaüstü istemcileri** ve **modern kimlik doğrulama istemcileri**' ni seçin.

      ![Koşullu Erişim](./media/app-protection-based-conditional-access/91.png)

1. **Erişim denetimleri**altında, aşağıdaki seçenekleri belirleyin:
   - **Onaylanan istemci uygulaması gerektir**
   - **Uygulama koruma ilkesi gerektir (Önizleme)**
   - **Seçili denetimlerden birini gerektir**
 
      ![Koşullu Erişim](./media/app-protection-based-conditional-access/12.png)

**2. Adım: İOS ve Android istemci uygulamaları için Intune uygulama koruma ilkesini yapılandırma**

![Koşullu Erişim](./media/app-protection-based-conditional-access/09.png)

Daha fazla bilgi için bkz. [Microsoft Intune uygulamaları ve verileri koruma](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).

## <a name="next-steps"></a>Sonraki adımlar

- Koşullu erişim ilkesini nasıl yapılandıracağınızı öğrenmek isterseniz bkz. [koşullu erişim Azure Active Directory belirli uygulamalar IÇIN MFA gerektirme](app-based-mfa.md).
- Ortamınız için koşullu erişim ilkelerini yapılandırmaya hazırsanız, bkz. [Azure Active Directory Koşullu erişim Için en iyi yöntemler](best-practices.md).
