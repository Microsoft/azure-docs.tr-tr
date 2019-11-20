---
title: Azure Active Directory | için koşullu erişimle Cloud App erişimi için onaylanan istemci uygulamaları gerektirme | Microsoft Docs
description: Azure Active Directory ' de koşullu erişim ile Cloud App erişimi için onaylanan istemci uygulamaları yapmayı isteme hakkında bilgi edinin.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 06/13/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 45eeef7d96f194e224e5b44421e73eb5ee5d9c0d
ms.sourcegitcommit: f5cc71cbb9969c681a991aa4a39f1120571a6c2e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68515143"
---
# <a name="how-to-require-approved-client-apps-for-cloud-app-access-with-conditional-access"></a>Nasıl Yapılır: Koşullu erişimle Cloud App erişimi için onaylanan istemci uygulamaları gerektir 

Çalışanlarınız hem kişisel hem de iş görevleri için mobil cihazları kullanır. Çalışanlarınızın üretken olduğundan emin olmaya devam ederken, veri kaybını da engellemek isteyebilirsiniz. Azure Active Directory (Azure AD) koşullu erişimi sayesinde, bulut uygulamalarınıza erişimi, Şirket verilerinizi koruyabilecek onaylanan istemci uygulamalarıyla sınırlandırabilirsiniz.  

Bu konuda, onaylanan istemci uygulamaları gerektiren koşul erişim ilkelerinin nasıl yapılandırılacağı açıklanmaktadır.

## <a name="overview"></a>Genel Bakış

[Azure AD koşullu erişimi](overview.md)sayesinde yetkili kullanıcıların kaynaklarınıza nasıl erişeceğine ilişkin ince ayar yapabilirsiniz. Örneğin, bulut uygulamalarınıza erişimi güvenilir cihazlarla sınırlayabilirsiniz.

Şirketinizin verilerini korumaya yardımcı olmak için [Intune uygulama koruma ilkelerini](https://docs.microsoft.com/intune/app-protection-policy) kullanabilirsiniz. Intune uygulama koruma ilkeleri, mobil cihaz yönetimi (MDM) çözümü gerektirmez ve bu sayede, cihazları bir cihaz yönetimi çözümüne kaydederek veya kaydetmeden Şirket verilerinizi koruyabilirsiniz.

Koşullu erişim Azure Active Directory, bulut uygulamalarınıza erişimi, Intune uygulama koruma ilkelerini destekleyen istemci uygulamalarıyla sınırlamanıza olanak sağlar. Örneğin, Exchange Online 'a erişimi Outlook uygulamasıyla kısıtlayabilirsiniz.

Koşullu erişim terminolojisinde, bu istemci uygulamaları **onaylanan istemci uygulamaları**olarak bilinir.  

![Koşullu Erişim](./media/app-based-conditional-access/05.png)

Onaylanan istemci uygulamalarının listesi için bkz. [onaylanan istemci uygulaması gereksinimi](technical-reference.md#approved-client-app-requirement).

Uygulama tabanlı koşullu erişim ilkelerini, hem kişisel hem de kurumsal cihazların verilerini koruma konusunda esneklik sağlamak için [cihaz tabanlı koşullu erişim ilkeleri](require-managed-devices.md) gibi diğer ilkelerle birleştirebilirsiniz.

## <a name="before-you-begin"></a>Başlamadan önce

Bu konuda, hakkında bilgi sahibi olduğunuz varsayılmaktadır:

- [Onaylanan istemci uygulaması gereksinimi](technical-reference.md#approved-client-app-requirement) teknik başvurusu.
- [Azure Active Directory ' deki Koşullu erişimin](overview.md)temel kavramları.
- [Koşullu erişim ilkesini yapılandırma](app-based-mfa.md).
- [Koşullu erişim ilkelerinin geçişi](best-practices.md#policy-migration).

## <a name="prerequisites"></a>Önkoşullar

Uygulama tabanlı bir koşullu erişim ilkesi oluşturmak için bir Enterprise Mobility + Security veya Azure Active Directory Premium aboneliğiniz olmalıdır ve kullanıcılar EMS veya Azure AD lisansına sahip olmalıdır. 

## <a name="exchange-online-policy"></a>Exchange Online ilkesi 

Bu senaryo, Exchange Online 'a erişim için uygulama tabanlı bir koşullu erişim ilkesinden oluşur.

### <a name="scenario-playbook"></a>Senaryo PlayBook

Bu senaryo, bir kullanıcının şu şekilde olduğunu varsayar:

- Exchange 'e bağlanmak için iOS veya Android 'de yerel bir posta uygulaması kullanarak e-postayı yapılandırır
- Erişimin yalnızca Outlook uygulaması kullanılarak kullanılabileceğini gösteren bir e-posta alır
- Bağlantıyı kullanarak uygulamayı indirir
- Outlook uygulamasını açar ve Azure AD kimlik bilgileriyle oturum açar
- Devam etmek için Authenticator (iOS) veya Şirket Portalı (Android) yüklenmesi istenir
- Uygulamayı yükleyebilir ve devam etmek için Outlook uygulamasına geri dönebilir
- Bir cihazı kaydetmesi istenir
- E-postaya erişebiliyor

Tüm Intune uygulama koruma ilkeleri, şirket verilerine erişim sırasında etkinleştirilir ve kullanıcıdan uygulamayı yeniden başlatmasını isteyebilir, ek bir PIN (uygulama ve platform için yapılandırılmışsa) kullanabilirsiniz.

### <a name="configuration"></a>Yapılandırma 

**1. adım-Exchange Online için bir Azure AD koşullu erişim ilkesi yapılandırma**

Bu adımdaki koşullu erişim ilkesi için aşağıdaki bileşenleri yapılandırmanız gerekir:

1. Koşullu erişim ilkenizin **adı** .
1. **Kullanıcılar ve gruplar**: Her koşullu erişim ilkesinde en az bir kullanıcı veya grup seçili olmalıdır.
1. **Bulut uygulamaları:** Bulut uygulamaları olarak **Office 365 Exchange Online**' ı seçmeniz gerekir.
1. **Durumunda** **Koşullar**olarak, **cihaz platformlarını** ve **istemci uygulamalarını**yapılandırmanız gerekir:
   1. **Cihaz platformları**olarak **Android** ve **iOS**' ı seçin.
   1. **İstemci uygulamaları (Önizleme)** olarak **mobil uygulamalar ve Masaüstü uygulamaları** ve **modern kimlik doğrulama istemcileri**' ni seçin.
1. **Erişim denetimleri**olarak, **onaylanmış istemci uygulaması (Önizleme)** seçeneğini seçmiş olmanız gerekir.

   ![Koşullu Erişim](./media/app-based-conditional-access/05.png)

**2. adım-Active Sync (EAS) ile Exchange Online için bir Azure AD koşullu erişim ilkesi yapılandırma**

Bu adımdaki koşullu erişim ilkesi için aşağıdaki bileşenleri yapılandırmanız gerekir:

1. Koşullu erişim ilkenizin **adı** .
1. **Kullanıcılar ve gruplar**: Her koşullu erişim ilkesinde en az bir kullanıcı veya grup seçili olmalıdır.
1. **Bulut uygulamaları:** Bulut uygulamaları olarak **Office 365 Exchange Online**' ı seçmeniz gerekir.
1. **Durumunda** **Koşullar**olarak, **istemci uygulamaları (Önizleme)** yapılandırmanız gerekir. 
   1. **İstemci uygulamaları (Önizleme)** olarak **mobil uygulamalar ve Masaüstü Istemcileri** ve **Exchange ActiveSync istemcileri**' ni seçin.
   1. **Erişim denetimleri**olarak, **onaylanmış istemci uygulaması (Önizleme)** seçeneğini seçmiş olmanız gerekir.

      ![Koşullu Erişim](./media/app-based-conditional-access/05.png)

**3. adım-iOS ve Android istemci uygulamaları için Intune uygulama koruma ilkesini yapılandırma**

Daha fazla bilgi için bkz. [Microsoft Intune uygulamaları ve verileri koruma](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune) .

## <a name="exchange-online-and-sharepoint-online-policy"></a>Exchange Online ve SharePoint Online ilkesi

Bu senaryo, Exchange Online ve SharePoint Online 'a onaylanan uygulamalarla erişim için mobil uygulama yönetimi ilkesiyle koşullu erişim içerir.

### <a name="scenario-playbook"></a>Senaryo PlayBook

Bu senaryo, bir kullanıcının şu şekilde olduğunu varsayar:

- Bağlanmak ve şirket sitelerini görüntülemek için SharePoint uygulamasını kullanmayı dener
- Outlook uygulaması kimlik bilgileriyle aynı kimlik bilgileriyle oturum açmaya çalışır
- Kaynaklara yeniden kaydolmak ve kaynaklara erişim sağlamak zorunda değildir

### <a name="configuration"></a>Yapılandırma

**1. adım-Exchange Online ve SharePoint Online için bir Azure AD koşullu erişim ilkesi yapılandırma**

Bu adımdaki koşullu erişim ilkesi için aşağıdaki bileşenleri yapılandırmanız gerekir:

1. Koşullu erişim ilkenizin **adı** .
1. **Kullanıcılar ve gruplar**: Her koşullu erişim ilkesinde en az bir kullanıcı veya grup seçili olmalıdır.
1. **Bulut uygulamaları:** Bulut uygulamaları olarak **office 365 Exchange Online** ve **Office 365 SharePoint Online**' ı seçmeniz gerekir. 
1. **Durumunda** **Koşullar**olarak, **cihaz platformlarını** ve **istemci uygulamalarını**yapılandırmanız gerekir:
   1. **Cihaz platformları**olarak **Android** ve **iOS**' ı seçin.
   1. **İstemci uygulamaları (Önizleme)** olarak **mobil uygulamalar ve Masaüstü istemcileri** ve **modern kimlik doğrulama istemcileri**' ni seçin.
1. **Erişim denetimleri**olarak, **onaylanmış istemci uygulaması (Önizleme)** seçeneğini seçmiş olmanız gerekir.

   ![Koşullu Erişim](./media/app-based-conditional-access/05.png)

**2. adım-Active Sync (EAS) ile Exchange Online için bir Azure AD koşullu erişim ilkesi yapılandırma**

Bu adımdaki koşullu erişim ilkesi için aşağıdaki bileşenleri yapılandırmanız gerekir:

1. Koşullu erişim ilkenizin **adı** .
1. **Kullanıcılar ve gruplar**: Her koşullu erişim ilkesinde en az bir kullanıcı veya grup seçili olmalıdır.
1. **Bulut uygulamaları:** Bulut uygulamaları olarak **Office 365 Exchange Online**' ı seçmeniz gerekir. Çevrimiçi 
1. **Durumunda** **Koşullar**olarak, **istemci uygulamalarını**yapılandırmanız gerekir:
   1. **İstemci uygulamaları (Önizleme)** olarak **mobil uygulamalar ve Masaüstü Istemcileri** ve **Exchange ActiveSync istemcileri**' ni seçin.
   1. **Erişim denetimleri**olarak, **onaylanmış istemci uygulaması (Önizleme)** seçeneğini seçmiş olmanız gerekir.

      ![Koşullu Erişim](./media/app-based-conditional-access/05.png)

**3. adım-iOS ve Android istemci uygulamaları için Intune uygulama koruma ilkesini yapılandırma**

![Koşullu Erişim](./media/app-based-conditional-access/09.png)

Daha fazla bilgi için bkz. [Microsoft Intune uygulamaları ve verileri koruma](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune) .

## <a name="app-based-or-compliant-device-policy-for-exchange-online-and-sharepoint-online"></a>Exchange Online ve SharePoint Online için uygulama tabanlı veya uyumlu cihaz ilkesi

Bu senaryo, Exchange Online 'a erişmek için uygulama tabanlı veya uyumlu bir cihaz koşullu erişim ilkesinden oluşur.

### <a name="scenario-playbook"></a>Senaryo PlayBook

Bu senaryo şunları varsayar:
 
- Bazı kullanıcılar zaten kaydolmuş (Şirket cihazlarıyla veya olmayan)
- Uygulama korumalı bir uygulama kullanılarak Azure AD 'ye kayıtlı ve kayıtlı olmayan kullanıcıların, kaynaklara erişmek için bir cihazı kaydetmesi gerekir
- Uygulama korumalı uygulamasını kullanan kayıtlı kullanıcıların, cihazı yeniden kaydetmesi gerekmez

### <a name="configuration"></a>Yapılandırma

**1. adım-Exchange Online ve SharePoint Online için bir Azure AD koşullu erişim ilkesi yapılandırma**

Bu adımdaki koşullu erişim ilkesi için aşağıdaki bileşenleri yapılandırmanız gerekir:

1. Koşullu erişim ilkenizin **adı** .
1. **Kullanıcılar ve gruplar**: Her koşullu erişim ilkesinde en az bir kullanıcı veya grup seçili olmalıdır.
1. **Bulut uygulamaları:** Bulut uygulamaları olarak **office 365 Exchange Online** ve **Office 365 SharePoint Online**' ı seçmeniz gerekir. 
1. **Durumunda** **Koşullar**olarak, **cihaz platformlarını** ve **istemci uygulamalarını**yapılandırmanız gerekir. 
   1. **Cihaz platformları**olarak **Android** ve **iOS**' ı seçin.
   1. **İstemci uygulamaları (Önizleme)** olarak **mobil uygulamalar ve Masaüstü istemcileri** ve **modern kimlik doğrulama istemcileri**' ni seçin.
1. **Erişim denetimleri**olarak, aşağıdakilerin seçili olması gerekir:
   - **Cihazın uyumlu olarak işaretlenmesini gerektir**
   - **Onaylanan istemci uygulaması gerektir (Önizleme)**
   - **Seçili denetimlerden birini gerektir**   
 
      ![Koşullu Erişim](./media/app-based-conditional-access/11.png)

**2. adım-Active Sync (EAS) ile Exchange Online için bir Azure AD koşullu erişim ilkesi yapılandırma**

Bu adımdaki koşullu erişim ilkesi için aşağıdaki bileşenleri yapılandırmanız gerekir:

1. Koşullu erişim ilkenizin **adı** .
1. **Kullanıcılar ve gruplar**: Her koşullu erişim ilkesinde en az bir kullanıcı veya grup seçili olmalıdır.
1. **Bulut uygulamaları:** Bulut uygulamaları olarak **Office 365 Exchange Online**' ı seçmeniz gerekir. 
1. **Durumunda** **Koşullar**olarak, **istemci uygulamalarını**yapılandırmanız gerekir. 
   1. **İstemci uygulamaları (Önizleme)** olarak **mobil uygulamalar ve Masaüstü Istemcileri** ve **Exchange ActiveSync istemcileri**' ni seçin.
1. **Erişim denetimleri**olarak, **onaylanmış istemci uygulaması (Önizleme)** seçeneğini seçmiş olmanız gerekir.
 
   ![Koşullu Erişim](./media/app-based-conditional-access/11.png)

**3. adım-iOS ve Android istemci uygulamaları için Intune uygulama koruma ilkesini yapılandırma**

![Koşullu Erişim](./media/app-based-conditional-access/09.png)

Daha fazla bilgi için bkz. [Microsoft Intune uygulamaları ve verileri koruma](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune) .

## <a name="app-based-and-compliant-device-policy-for-exchange-online-and-sharepoint-online"></a>Exchange Online ve SharePoint Online için uygulama tabanlı ve uyumlu cihaz ilkesi

Bu senaryo, Exchange Online 'a erişmek için uygulama tabanlı ve uyumlu bir cihaz koşullu erişim ilkesinden oluşur.

### <a name="scenario-playbook"></a>Senaryo PlayBook

Bu senaryo, bir kullanıcının şu şekilde olduğunu varsayar:
 
- Exchange 'e bağlanmak için iOS veya Android 'de yerel bir posta uygulaması kullanarak e-postayı yapılandırır
- Erişimin cihazınızın kaydedilmesini gerektirdiğini belirten bir e-posta alır
- Şirket portalı 'nı indirir ve şirket portalı 'nda oturum açar
- Postayı denetler ve Outlook uygulamasını kullanması istenir
- Outlook uygulamasını indirir
- Outlook uygulamasını açar ve kayıt sırasında kullanılan kimlik bilgilerini girer
- Kullanıcı e-postaya erişebiliyor

Tüm Intune uygulama koruma ilkeleri, şirket verilerine erişim sırasında etkinleştirilir ve kullanıcıdan uygulamayı yeniden başlatmasını isteyebilir, ek bir PIN (uygulama ve platform için yapılandırılmışsa)

### <a name="configuration"></a>Yapılandırma

**1. adım-Exchange Online ve SharePoint Online için bir Azure AD koşullu erişim ilkesi yapılandırma**

Bu adımdaki koşullu erişim ilkesi için aşağıdaki bileşenleri yapılandırmanız gerekir:

1. Koşullu erişim ilkenizin **adı** .
1. **Kullanıcılar ve gruplar**: Her koşullu erişim ilkesinde en az bir kullanıcı veya grup seçili olmalıdır.
1. **Bulut uygulamaları:** Bulut uygulamaları olarak **office 365 Exchange Online** ve **Office 365 SharePoint Online**' ı seçmeniz gerekir. 
1. **Durumunda** **Koşullar**olarak, **cihaz platformlarını** ve **istemci uygulamalarını**yapılandırmanız gerekir. 
   1. **Cihaz platformları**olarak **Android** ve **iOS**' ı seçin.
   1. **İstemci uygulamaları (Önizleme)** olarak **mobil uygulamalar ve Masaüstü uygulamaları** ve **modern kimlik doğrulama istemcileri**' ni seçin.
1. **Erişim denetimleri**olarak, aşağıdakilerin seçili olması gerekir:
   - **Cihazın uyumlu olarak işaretlenmesini gerektir**
   - **Onaylanan istemci uygulaması gerektir (Önizleme)**
   - **Seçili tüm denetimleri gerektir**   
 
      ![Koşullu Erişim](./media/app-based-conditional-access/13.png)

**2. adım-Active Sync (EAS) ile Exchange Online için bir Azure AD koşullu erişim ilkesi yapılandırma**

Bu adımdaki koşullu erişim ilkesi için aşağıdaki bileşenleri yapılandırmanız gerekir:

1. Koşullu erişim ilkenizin **adı** .
1. **Kullanıcılar ve gruplar**: Her koşullu erişim ilkesinde en az bir kullanıcı veya grup seçili olmalıdır.
1. **Bulut uygulamaları:** Bulut uygulamaları olarak **Office 365 Exchange Online**' ı seçmeniz gerekir. 
1. **Durumunda** **Koşullar**olarak, **istemci uygulamaları (Önizleme)** yapılandırmanız gerekir. 
   1. **İstemci uygulamaları (Önizleme)** olarak **mobil uygulamalar ve Masaüstü Istemcileri** ve **Exchange ActiveSync istemcileri**' ni seçin.

   ![Koşullu Erişim](./media/app-based-conditional-access/92.png)

1. **Erişim denetimleri**olarak, aşağıdakilerin seçili olması gerekir:
   - **Cihazın uyumlu olarak işaretlenmesini gerektir**
   - **Onaylanan istemci uygulaması gerektir (Önizleme)**
   - **Seçili tüm denetimleri gerektir**   

**3. adım-iOS ve Android istemci uygulamaları için Intune uygulama koruma ilkesini yapılandırma**

![Koşullu Erişim](./media/app-based-conditional-access/09.png)

Daha fazla bilgi için bkz. [Microsoft Intune uygulamaları ve verileri koruma](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune) .

## <a name="next-steps"></a>Sonraki adımlar

Koşullu erişim ilkesini nasıl yapılandıracağınızı öğrenmek isterseniz bkz. [koşullu erişim Azure Active Directory belirli uygulamalar IÇIN MFA gerektirme](app-based-mfa.md).

Ortamınız için koşullu erişim ilkelerini yapılandırmaya hazırsanız, [Azure Active Directory Koşullu erişim için en iyi yöntemlere](best-practices.md)bakın. 
