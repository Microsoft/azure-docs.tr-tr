---
title: Koşullu erişim yönetilen cihaz gerektirir-Azure Active Directory
description: Cloud App Access için yönetilen cihazlar gerektiren Azure Active Directory (Azure AD) cihaz tabanlı koşullu erişim ilkelerinin nasıl yapılandırılacağını öğrenin.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 10/16/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: c11b58954eefda67f981d618b04ab2bd69fa6b43
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93077770"
---
# <a name="how-to-require-managed-devices-for-cloud-app-access-with-conditional-access"></a>Nasıl yapılır: koşullu erişimle Cloud App erişimi için yönetilen cihazlar gerektirme

Mobil ve bulut öncelikli bir dünyada Azure Active Directory (Azure AD), uygulamalara ve hizmetlere her yerden çoklu oturum açma imkanı sağlar. Yetkili kullanıcılar, mobil ve ayrıca kişisel cihazlar dahil olmak üzere çok çeşitli cihazlardan bulut uygulamalarınıza erişebilirler. Ancak, birçok ortamda yalnızca güvenlik ve uyumluluk standartlarınızı karşılayan cihazlar tarafından erişilmesi gereken en az bir uygulama vardır. Bu cihazlar yönetilen cihazlar olarak da bilinir. 

Bu makalede, yönetilen cihazların ortamınızdaki belirli bulut uygulamalarına erişmesi için gereken koşullu erişim ilkelerini nasıl yapılandırabileceğiniz açıklanmaktadır. 

## <a name="prerequisites"></a>Önkoşullar

Bulut uygulama erişimi için yönetilen cihazların gerekli olması, **Azure AD koşullu erişimi** Ile **Azure AD cihaz yönetimini** birbirine bağlalardır. Henüz bu alanlardan birini bilmiyorsanız, önce aşağıdaki konuları okumanız gerekir:

- **[Azure Active Directory Koşullu erişim](./overview.md)** -Bu makalede, koşullu erişime ve ilgili terimlere kavramsal bir genel bakış sunulmaktadır.
- **[Azure Active Directory cihaz yönetimine giriş](../devices/overview.md)** -Bu makalede, cihazları kurumsal denetim altında almak için sahip olduğunuz çeşitli seçeneklere genel bakış sunulmaktadır. 
- **Windows 10 Creators Update (sürüm 1703)** veya sonraki sürümlerde Chrome desteği için [Windows 10 hesapları uzantısını](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji)yükler. Koşullu erişim ilkesi cihaza özgü ayrıntılar gerektirdiğinde bu uzantı gereklidir.

>[!NOTE] 
> İlk cihaz kimlik doğrulamasından sonra en iyi uygulamayı almak için Azure AD cihaz tabanlı koşullu erişim ilkesini kullanmanızı öneririz. Bu, cihazın uyumluluk ve cihaz kod akışı dışında kalırsa kapatma oturumlarını içerir.


## <a name="scenario-description"></a>Senaryo açıklaması

Güvenlik ve üretkenlik arasındaki Bakiyenin dengelenmesi bir zorluktur. Bulut kaynaklarınıza erişmek için desteklenen cihazların uzalama, kullanıcılarınızın verimliliğini artırmaya yardımcı olur. Ters çevir tarafında, ortamınızdaki belirli kaynaklara bilinmeyen koruma düzeyi olan cihazların erişmesini istemezsiniz. Etkilenen kaynaklar için kullanıcıların yalnızca yönetilen bir cihaz kullanarak erişebilmesi gerekir. 

Azure AD koşullu erişimi sayesinde, erişim izni veren tek bir ilkeyle bu gereksinimi ele alabilirsiniz:

- Seçili bulut uygulamalarına
- Seçili kullanıcılar ve gruplar için
- Yönetilen bir cihaz gerektirme

## <a name="managed-devices"></a>Yönetilen cihazlar  

Basit koşullarda, yönetilen cihazlar bazı kurumsal denetim *sıralaması* altında bulunan cihazlardır. Azure AD 'de, yönetilen bir cihaz için önkoşul Azure AD 'ye kayıtlı. Bir cihazın kaydedilmesi cihaz nesnesi biçiminde cihaz için bir kimlik oluşturur. Bu nesne, Azure tarafından bir cihazla ilgili durum bilgilerini izlemek için kullanılır. Bir Azure AD yöneticisi olarak, bir cihazın durumunu değiştirmek (etkinleştirmek/devre dışı bırakmak) için bu nesneyi zaten kullanabilirsiniz.
  
:::image type="content" source="./media/require-managed-devices/32.png" alt-text="Azure 'daki cihaz bölmesinin ekran görüntüsü D. etkinleştir ve devre dışı bırak öğeleri vurgulanır." border="false":::

Azure AD 'ye kayıtlı bir cihaz almak için üç seçeneğiniz vardır: 

- **Azure AD kayıtlı cihazlar** -Azure AD 'ye kayıtlı kişisel bir cihaz almak için
- **Azure AD 'ye katılmış cihazlar** -Azure AD 'ye kayıtlı şirket ıçı bir ad 'ye katılmamış bir kuruluş Windows 10 cihazını almak için. 
- **Karma Azure AD 'ye katılmış cihazlar** -Azure AD 'ye kayıtlı şirket ıçı bir ad 'ye katılmış bir Windows 10 veya desteklenen bir alt düzey cihaz almak için.

Bu üç seçenek, [cihaz kimliği](../devices/overview.md) nedir makalesinde açıklanmaktadır.

Yönetilen bir cihaz olması için, kayıtlı bir cihazın **karma bir Azure AD 'ye katılmış cihaz** ya da **uyumlu olarak işaretlenmiş bir cihaz** olması gerekir.  

:::image type="content" source="./media/require-managed-devices/47.png" alt-text="Azure A D Izin bölmesinin ekran görüntüsü. Cihazların uyumlu olması ve Azure 'un D 'ye katılması için onay kutuları olduğu gibi, erişime izin ver seçilidir." border="false":::
 
## <a name="require-hybrid-azure-ad-joined-devices"></a>Karma Azure AD 'ye katılmış cihazlar gerektir

Koşullu erişim ilkenizde, seçilen bulut uygulamalarının yalnızca yönetilen bir cihaz kullanılarak erişilebilmesini sağlamak için **karma Azure AD 'ye katılmış cihaz gerektir** seçeneğini belirleyebilirsiniz. 

:::image type="content" source="./media/require-managed-devices/10.png" alt-text="Azure A D Izin bölmesinin ekran görüntüsü. Erişim izni verme seçilidir. Cihazların karma olarak Azure 'a katılmış olmasını gerektiren bir onay kutusu da seçilidir." border="false":::

Bu ayar yalnızca, şirket içi bir AD 'ye katılmış Windows 7 veya Windows 8 gibi Windows 10 veya alt düzey cihazlar için geçerlidir. Bu cihazları yalnızca, kayıtlı bir Windows 10 cihazını almak için [otomatik bir işlem](../devices/hybrid-azuread-join-plan.md) olan karma BIR Azure AD JOIN kullanarak Azure AD 'ye kaydedebilirsiniz. 

:::image type="content" source="./media/require-managed-devices/45.png" alt-text="Ad, etkin durum, O, sürüm, ekleme türü, sahip, M D M ve bir cihazın uyumlu durumunu listeleme tablo. Uyumlu durum Hayır ' dır." border="false":::

Hibrit Azure AD 'ye katılmış cihaza yönetilen cihaz ne olur?  Şirket içi bir AD 'ye katılmış cihazlar için, bu cihazların üzerindeki denetimin **Configuration Manager** veya **Grup İlkesi (GP)** gibi yönetim çözümleri kullanılarak bunları yönetmek için zorlandığını kabul edilir. Azure AD 'nin bu yöntemlerin bir cihaza uygulanıp uygulanmadığı tespit etmek için bir yöntem olmadığından, karma bir Azure AD 'ye katılmış cihazın gerekli olması, yönetilen bir cihaz gerektiren görece zayıf bir mekanizmadır. Şirket içi etki alanına katılmış cihazlara uygulanan yöntemlerin, bu tür bir cihazın karma Azure AD 'ye katılmış bir cihaz olması halinde yönetilen bir cihaz oluşturması için yeterince güçlü olup olmadığını bir yönetici olarak öğreneceksiniz.

## <a name="require-device-to-be-marked-as-compliant"></a>Cihazın uyumlu olarak işaretlenmesini gerektir

Bir *cihazın uyumlu olarak işaretlenmesini gerektirme* seçeneği, yönetilen bir cihaz istemek için en güçlü formdur.

:::image type="content" source="./media/require-managed-devices/11.png" alt-text="Azure A D Izin bölmesinin ekran görüntüsü. Erişim izni verme seçilidir. Bir cihazın uyumlu olarak işaretlenmesini gerektiren onay kutusu da seçilidir." border="false":::

Bu seçenek, bir cihazın Azure AD 'ye kaydedilmesini ve ayrıca, tarafından uyumlu olarak işaretlenmesini gerektirir:
         
- Intune
- Windows 10 cihazlarını Azure AD tümleştirmesi aracılığıyla yöneten bir üçüncü taraf mobil cihaz yönetimi (MDM) sistemi. Windows 10 dışındaki cihaz işletim sistemi türleri için üçüncü taraf MDM sistemleri desteklenmez.
 
:::image type="content" source="./media/require-managed-devices/46.png" alt-text="Ad, etkin durum, O, sürüm, ekleme türü, sahip, M D M ve bir cihazın uyumlu durumunu listeleme tablo. Uyumlu durum vurgulanır." border="false":::

Uyumlu olarak işaretlenen bir cihaz için şunları varsayabilirsiniz: 

- İş gücünüzün şirket verilerine erişmek için kullandığı mobil cihazlar yönetilir
- İş gücünüzün kullandığı mobil uygulamalar yönetiliyor
- Şirket bilgileriniz, iş gücünüzün eriştiği ve paylaştığı bir şekilde denetim sağlanmasına yardımcı olur.
- Cihaz ve uygulamaları, şirket güvenlik gereksinimleriyle uyumludur

### <a name="scenario-require-device-enrollment-for-ios-and-android-devices"></a>Senaryo: iOS ve Android cihazlar için cihaz kaydı gerektir

Bu senaryoda, Contoso tüm mobil erişiminin Microsoft 365 kaynaklara kayıtlı bir cihaz kullanması gerektiğini kararmıştır. Tüm kullanıcıları Azure AD kimlik bilgileriyle oturum açın ve bunlara Azure AD Premium P1 veya P2 ve Microsoft Intune içeren lisanslar atanır.

Kuruluşlar, kayıtlı bir mobil cihazın kullanımını gerektirmek için aşağıdaki adımları tamamlamalıdır.

1. **Azure Portal** genel yönetici, güvenlik yöneticisi veya koşullu erişim Yöneticisi olarak oturum açın.
1. **Azure Active Directory**  >  **güvenlik**  >  **koşullu erişimi**'ne gidin.
1. **Yeni ilke**' yi seçin.
1. İlkenize bir ad verin. Kuruluşların ilkelerinin adları için anlamlı bir standart oluşturmasını öneririz.
1. **Atamalar** altında **Kullanıcılar ve gruplar** ' ı seçin.
   1. **Ekle**' nin altında, **tüm kullanıcılar** ' ı veya bu Ilkeyi uygulamak istediğiniz belirli **kullanıcıları ve grupları** seçin. 
   1. **Bitti** seçeneğini belirleyin.
1. **Bulut uygulamaları veya eylemleri**  >  **dahil**, **Office 365**' i seçin.
1. **Koşullar**' ın altında **cihaz platformları**' nı seçin.
   1. **Yapılandır** 'ı **Evet** olarak ayarlayın.
   1. **Android** ve **iOS** dahil edin.
1. **Erişim denetimleri**  >  **izni** altında, aşağıdaki seçenekleri belirleyin:
   - **Cihazın uyumlu olarak işaretlenmesini gerektir**
1. Ayarlarınızı doğrulayın ve **ilke** ayarını **Açık** olarak ayarlayın.
1. İlkenizi oluşturmak ve etkinleştirmek için **Oluştur** ' u seçin.

### <a name="known-behavior"></a>Bilinen davranış

[Cihaz kodu OAuth akışını](../develop/v2-oauth2-device-code.md)kullanırken, yönetilen cihaz izin verme denetimi veya cihaz durumu koşulu desteklenmez. Bunun nedeni, kimlik doğrulaması gerçekleştiren cihazın cihaz durumunu cihaza bir kod sağlamak ve belirteçteki cihaz durumunun kimlik doğrulaması gerçekleştiren cihaza kilitli olması nedeniyle oluşur. Bunun yerine Multi-Factor Authentication izin iste denetimini kullanın.

Windows 7, iOS, Android, macOS ve bazı üçüncü taraf web tarayıcılarında, Azure AD, cihaz Azure AD 'ye kaydedildiğinde sağlanan bir istemci sertifikası kullanarak cihazı tanımlar. Kullanıcı tarayıcıda ilk kez oturum açtığında, kullanıcıdan sertifikayı seçmesi istenir. Son Kullanıcı, tarayıcıyı kullanmaya devam edebilmek için bu sertifikayı seçmelidir.

## <a name="next-steps"></a>Sonraki adımlar

[Koşullu erişim ilkelerinin etkisini, yalnızca rapor modunda daha geniş bir şekilde etkinleştirmeden önce değerlendirin](concept-conditional-access-report-only.md).
