---
title: Azure Active Directory (Azure AD) JOIN uygulamasını planlayın | Microsoft Docs
description: Ortamınızdaki Azure AD 'ye katılmış cihazları uygulamak için gereken adımları açıklar.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9c8219dd9ec971303fb62cf828da91ee877f4ca9
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73882915"
---
# <a name="how-to-plan-your-azure-ad-join-implementation"></a>Nasıl yapılır: Azure AD JOIN Uygulamanızı planlayın

Azure AD katılımı, kullanıcılarınızın üretken ve güvenli tutulması sırasında şirket içi Active Directory katılmanız gerekmeden cihazları doğrudan Azure AD 'ye katmanıza olanak sağlar. Azure AD JOIN, hem ölçekli hem de kapsamlı dağıtımlar için kurumsal olarak hazırlanmıştır.   

Bu makale, Azure AD JOIN uygulamanızı planlamak için gereken bilgileri sağlar.
 
## <a name="prerequisites"></a>Ön koşullar

Bu makalede, [Azure Active Directory 'de cihaz yönetimine giriş](../device-management-introduction.md)hakkında bilgi sahibi olduğunuz varsayılır.

## <a name="plan-your-implementation"></a>Uygulamanızı planlama

Azure AD JOIN uygulamanızı planlamak için şunu öğrenmeniz gerekir:

|   |   |
|---|---|
|![İşaretli][1]|Senaryolarınızı gözden geçirin|
|![İşaretli][1]|Kimlik altyapınızı gözden geçirin|
|![İşaretli][1]|Cihaz yönetimini değerlendirin|
|![İşaretli][1]|Uygulamalar ve kaynaklarla ilgili önemli noktaları anlama|
|![İşaretli][1]|Sağlama seçeneklerinizi anlayın|
|![İşaretli][1]|Kurumsal durum dolaşımı yapılandırma|
|![İşaretli][1]|Koşullu erişimi yapılandırma|

## <a name="review-your-scenarios"></a>Senaryolarınızı gözden geçirin 

Karma Azure AD katılımı belirli senaryolar için tercih edilebilir, ancak Azure AD katılımı, Windows ile bulut öncelikli bir modele geçiş yapmanızı sağlar. Cihaz yönetileninizi modernleştirin ve cihazla ilgili BT maliyetlerini azaldıysanız, Azure AD katılımı bu hedefleri elde etmek için harika bir temel sağlar.  
 
Hedefleriniz aşağıdaki ölçütlere göre hizalandıysanız Azure AD 'ye katılmayı göz önünde bulundurmanız gerekir:

- Kullanıcılarınız için üretkenlik paketi olarak Microsoft 365 benimsediolursunuz.
- Cihazları bir bulut cihaz yönetimi çözümüyle yönetmek istiyorsunuz.
- Coğrafi olarak dağıtılan kullanıcılar için cihaz sağlamayı basitleştirmek istiyorsunuz.
- Uygulama altyapınızı modernleştirin planlayın.

## <a name="review-your-identity-infrastructure"></a>Kimlik altyapınızı gözden geçirin  

Azure AD katılımı, hem yönetilen hem de Federasyon ortamlarında çalışmaktadır.  

### <a name="managed-environment"></a>Yönetilen ortam

Yönetilen bir ortam, [Parola karması eşitlemesi](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-password-hash-synchronization) aracılığıyla ya da kesintisiz çoklu oturum açma Ile [kimlik doğrulama](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta-quick-start) yoluyla dağıtılabilir.

Bu senaryolar, kimlik doğrulaması için bir federasyon sunucusu yapılandırmanızı gerektirmez.

### <a name="federated-environment"></a>Federasyon ortamı

Federasyon ortamının hem WS-Trust hem de WS-beslenir protokollerini destekleyen bir kimlik sağlayıcısı olmalıdır:

- **WS-beslenir:** Bu protokol, bir cihazın Azure AD 'ye katılması için gereklidir.
- **WS-Trust:** Bu protokol, bir Azure AD 'ye katılmış cihazda oturum açmak için gereklidir.

AD FS kullanırken, aşağıdaki WS-Trust uç noktalarını etkinleştirmeniz gerekir: `/adfs/services/trust/2005/usernamemixed`
 `/adfs/services/trust/13/usernamemixed`
 `/adfs/services/trust/2005/certificatemixed`
 `/adfs/services/trust/13/certificatemixed`

Kimlik sağlayıcınız bu protokolleri desteklemiyorsa Azure AD katılımı yerel olarak çalışmaz. Windows 10 1809 ' den başlayarak, kullanıcılarınız [Windows 10 ' da Web oturumu açma](https://docs.microsoft.com/windows/whats-new/whats-new-windows-10-version-1809#web-sign-in-to-windows-10)yoluyla SAML tabanlı bir kimlik sağlayıcısı Ile BIR Azure AD 'ye katılmış cihazda oturum açabilirler. Şu anda, Web oturumu açma bir önizleme özelliğidir ve üretim dağıtımları için önerilmez.

>[!NOTE]
> Şu anda Azure AD JOIN, [birincil kimlik doğrulama yöntemi olarak dış kimlik doğrulama sağlayıcılarıyla yapılandırılmış AD FS 2019](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/additional-authentication-methods-ad-fs#enable-external-authentication-methods-as-primary)ile çalışmıyor. Azure AD JOIN, birincil yöntem olarak parola kimlik doğrulaması varsayılan olarak, bu senaryoda kimlik doğrulama hatalarıyla sonuçlanır


### <a name="smartcards-and-certificate-based-authentication"></a>Smartcards ve sertifika tabanlı kimlik doğrulaması

Cihazları Azure AD 'ye katmak için akıllı kartlar veya sertifika tabanlı kimlik doğrulaması kullanamazsınız. Ancak, akıllı kartlar AD FS yapılandırılmışsa Azure AD 'ye katılmış cihazlarda oturum açmak için kullanılabilir.

**Öneri:** Windows 10 cihazlarına güçlü, parola açısından daha az kimlik doğrulama için Iş için Windows Hello 'Yu uygulayın.

### <a name="user-configuration"></a>Kullanıcı Yapılandırması

İçinde kullanıcı oluşturursanız:

- **Şirket içi Active Directory**, [Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-whatis)kullanarak bunları Azure AD ile eşitlemeniz gerekir. 
- **Azure AD**, ek bir kurulum gerekli değildir.

Azure AD UPN 'lerden farklı olan şirket içi UPN 'ler Azure AD 'ye katılmış cihazlarda desteklenmez. Kullanıcılarınız şirket içi UPN kullanıyorsa, Azure AD 'de birincil UPN 'sini kullanmaya geçmeniz gerekir.

## <a name="assess-your-device-management"></a>Cihaz yönetimini değerlendirin

### <a name="supported-devices"></a>Desteklenen cihazlar

Azure AD katılımı:

- Yalnızca Windows 10 cihazlarına uygulanabilir. 
- , Windows 'un veya diğer işletim sistemlerinin önceki sürümleri için geçerli değildir. Windows 7/8.1 cihazlarınız varsa, Azure AD JOIN 'i dağıtmak için Windows 10 ' a yükseltmeniz gerekir.
- FIPS modunda TPM içeren cihazlarda desteklenmez.
 
**Öneri:** Güncel özelliklerden yararlanmak için her zaman en son Windows 10 sürümünü kullanın.

### <a name="management-platform"></a>Yönetim platformu

Azure AD 'ye katılmış cihazlar için cihaz yönetimi, Intune ve MDM CSP 'Ler gibi bir MDM platformunu temel alır. Windows 10 ' da, tüm uyumlu MDM çözümleriyle birlikte çalışarak yerleşik bir MDM Aracısı vardır.

> [!NOTE]
> Şirket içi Active Directory bağlı olmadıkları için, Azure AD 'ye katılmış cihazlarda Grup ilkeleri desteklenmez. Azure AD 'ye katılmış cihazların yönetimi yalnızca MDM aracılığıyla yapılabilir

Azure AD 'ye katılmış cihazları yönetmeye yönelik iki yaklaşım vardır:

- **Yalnızca MDM** -cihaz, Intune gıbı bir MDM sağlayıcısı tarafından özel olarak yönetilir. Tüm ilkeler MDM kayıt sürecinin bir parçası olarak dağıtılır. Azure AD Premium veya EMS müşterileri için MDM kaydı, bir Azure AD birleştirmenin parçası olan otomatikleştirilmiş bir adımdır.
- **Ortak yönetim** -bir cıhaz, MDM sağlayıcısı ve SCCM tarafından yönetilir. Bu yaklaşımda, SCCM Aracısı belirli yönleri yönetmek için MDM tarafından yönetilen bir cihaza yüklenir.

Grup ilkeleri kullanıyorsanız, MDM [geçiş çözümleme aracı 'nı (MVADE)](https://github.com/WindowsDeviceManagement/MMAT)kullanarak MDM ilke eşliği değerlendirin. 

Grup ilkeleri yerine bir MDM çözümü kullanıp kullanmayacağınızı öğrenmek için desteklenen ve desteklenmeyen ilkeleri gözden geçirin. Desteklenmeyen ilkeler için aşağıdakileri göz önünde bulundurun:

- Azure AD 'ye katılmış cihazlar veya kullanıcılar için gereken desteklenmeyen ilkeler var mı?
- Desteklenmeyen ilkeler bulut tabanlı dağıtımda uygulanabilir mi?

MDM Çözümünüz Azure AD Uygulama Galerisi aracılığıyla kullanılamıyorsa, [MDM ile Azure Active Directory tümleştirme](https://docs.microsoft.com/windows/client-management/mdm/azure-active-directory-integration-with-mdm)' de özetlenen işlemden sonra ekleyebilirsiniz. 

Ortak yönetim sayesinde, ilkeler MDM platformunuz aracılığıyla teslim edilirken cihazlarınızın belirli yönlerini yönetmek için SCCM 'yi kullanabilirsiniz. Microsoft Intune, SCCM ile ortak yönetime izin verebilir. Daha fazla bilgi için bkz. [Windows 10 cihazlar Için ortak yönetim](https://docs.microsoft.com/sccm/core/clients/manage/co-management-overview). Intune dışında bir MDM ürünü kullanıyorsanız, lütfen uygulanabilir ortak yönetim senaryolarında MDM sağlayıcınızla görüşün.

**Öneri:** Yalnızca Azure AD 'ye katılmış cihazlar için MDM yönetimini göz önünde bulundurun.

## <a name="understand-considerations-for-applications-and-resources"></a>Uygulamalar ve kaynaklarla ilgili önemli noktaları anlama

Daha iyi bir kullanıcı deneyimi ve erişim denetimi için uygulamaların Şirket içinden buluta geçirilmesini öneririz. Ancak, Azure AD 'ye katılmış cihazlar hem şirket içi hem de bulut uygulamalarına sorunsuz şekilde erişim sağlayabilir. Daha fazla bilgi için bkz. [Azure AD 'ye katılmış CIHAZLARDA SSO, şirket içi kaynaklara yönelik olarak nasıl kullanılır](azuread-join-sso.md).

Aşağıdaki bölümlerde farklı uygulama ve kaynak türleri için konular listelenmektedir.

### <a name="cloud-based-applications"></a>Bulut tabanlı uygulamalar

Azure AD uygulama galerisine bir uygulama eklenirse, kullanıcılar Azure AD 'ye katılmış cihazlar aracılığıyla SSO alır. Ek yapılandırma gerekmez. Kullanıcılar hem Microsoft Edge hem de Chrome tarayıcılarında SSO alır. Chrome için [Windows 10 hesapları uzantısını](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji)dağıtmanız gerekir. 

Tüm Win32 uygulamaları:

- Belirteç istekleri için web hesabı Yöneticisi 'ni (WAM) kullan, Azure AD 'ye katılmış cihazlarda SSO da alır. 
- WAM 'ye dayanmayın, kullanıcılardan kimlik doğrulaması için istemde bulunabilir. 

### <a name="on-premises-web-applications"></a>Şirket içi web uygulamaları

Uygulamalarınız özel olarak oluşturulup/veya şirket içinde barındırılıyorsa, bunları tarayıcınızın güvenilen sitelerine eklemeniz gerekir:

- Windows tümleşik kimlik doğrulamasının çalışmasını etkinleştir 
- Kullanıcılara hiçbir istem dışı SSO deneyimi sağlar. 

AD FS kullanıyorsanız, bkz. [AD FS ile çoklu oturum açmayı doğrulama ve yönetme](https://docs.microsoft.com/previous-versions/azure/azure-services/jj151809(v%3dazure.100)). 

**Öneri:** Daha iyi bir deneyim için bulutta barındırmayı (örneğin, Azure) ve Azure AD ile tümleştirmeyi düşünün.

### <a name="on-premises-applications-relying-on-legacy-protocols"></a>Eski protokollerde bulunan şirket içi uygulamalar

Cihazın bir etki alanı denetleyicisine erişimi varsa, kullanıcılar Azure AD 'ye katılmış cihazlardan SSO alır. 

**Öneri:** Bu uygulamalar için güvenli erişimi etkinleştirmek üzere [Azure AD uygulaması proxy](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy) dağıtın.

### <a name="on-premises-network-shares"></a>Şirket içi ağ paylaşımları

Bir cihazın şirket içi etki alanı denetleyicisine erişimi olduğunda kullanıcılarınızın Azure AD 'ye katılmış cihazlarından SSO 'SU vardır.

### <a name="printers"></a>Yazıcılarınızı

Yazıcılar için, Azure AD 'ye katılmış cihazlarda yazıcı bulmak için [hibrit bulut yazdırma](https://docs.microsoft.com/windows-server/administration/hybrid-cloud-print/hybrid-cloud-print-deploy) 'yı dağıtmanız gerekir. 

Yazıcılar yalnızca bulut ortamında otomatik olarak keşfedilmeden, kullanıcılarınız, yazıcıların UNC yolunu da kullanarak doğrudan ekleyebilir. 

### <a name="on-premises-applications-relying-on-machine-authentication"></a>Makine kimlik doğrulamasına bağlı şirket içi uygulamalar

Azure AD 'ye katılmış cihazlar makine kimlik doğrulamasına bağlı olan şirket içi uygulamaları desteklemez. 

**Öneri:** Bu uygulamaları devre dışı bırakmayı ve modern alternatiflerine geçmeyi göz önünde bulundurun.

### <a name="remote-desktop-services"></a>Uzak Masaüstü Hizmetleri

Azure AD 'ye katılmış cihazlara yönelik Uzak Masaüstü bağlantısı konak makinenin Azure AD 'ye katılmış veya hibrit Azure AD 'ye katılmış olmasını gerektirir. Katılmamış veya Windows dışı bir cihazdan uzak masaüstü desteklenmez. Daha fazla bilgi için bkz. [uzak Azure AD 'ye katılmış bilgisayara bağlanma](https://docs.microsoft.com/windows/client-management/connect-to-remote-aadj-pc)

## <a name="understand-your-provisioning-options"></a>Sağlama seçeneklerinizi anlayın

Aşağıdaki yaklaşımlardan yararlanarak Azure AD katılımı sağlayabilirsiniz:

- **Self-Service ın OOBE/Settings** -self servis modunda, kullanıcılar, Windows kutudan çıkar DENEYIMI (OOBE) sırasında veya Windows AYARLARıNDAN Azure AD JOIN işlemi üzerinden gider. Daha fazla bilgi için bkz. [iş cihazınızı kuruluşunuzun ağına ekleme](https://docs.microsoft.com/azure/active-directory/user-help/user-help-join-device-on-network). 
- **Windows Autopilot** -Windows Autopilot, BIR Azure AD katılımı gerçekleştirmek üzere daha sorunsuz bir deneyim için cihazların önceden yapılandırılmasını sağlar. Daha fazla bilgi için bkz. [Windows Autopilot 'e genel bakış](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-10-autopilot). 
- Toplu **kayıt** -toplu kayıt, cihazları yapılandırmak için bir toplu sağlama aracı kullanarak yönetici tarafından yönetilen BIR Azure AD katılımı sağlar. Daha fazla bilgi için bkz. [Windows cihazları Için toplu kayıt](https://docs.microsoft.com/intune/windows-bulk-enroll).
 
Bu üç yaklaşımdan oluşan bir karşılaştırma 
 
|   | Self Servis kurulumu | Windows Autopilot | Toplu kayıt |
| --- | --- | --- | --- |
| Ayarlama için Kullanıcı etkileşimi gerektir | Evet | Evet | Hayır |
| BT çabaları gerektir | Hayır | Evet | Evet |
| Geçerli akışlar | OOBE & ayarları | Yalnızca OOBE | Yalnızca OOBE |
| Birincil Kullanıcı için yerel yönetici hakları | Evet, varsayılan olarak | Yapılandırılabilir | Hayır |
| Cihaz OEM desteği gerektir | Hayır | Evet | Hayır |
| Desteklenen sürümler | 1511 + | 1709 + | 1703 + |
 
Yukarıdaki tabloyu inceleyerek dağıtım yaklaşımınızı veya yaklaşımlarınızı seçin ve iki yaklaşımı benimsemeye yönelik aşağıdaki konuları gözden geçirin:  

- Kullanıcılarınız, kurulum aşamasından sonra mı gidebilirler? 
   - Self servis, bu kullanıcılar için en iyi şekilde çalışabilir. Kullanıcı deneyimini geliştirmek için Windows Autopilot göz önünde bulundurun.  
- Kullanıcılarınız uzak veya şirket içi şirket içinde mi? 
   - Self servis veya Autopilot, sorunsuz bir kurulum için uzak kullanıcılara en iyi şekilde çalışır. 
- Kullanıcı odaklı veya yönetici tarafından yönetilen bir yapılandırmayı tercih ediyor musunuz? 
   - Toplu kayıt, Yönetici odaklı dağıtım için kullanıcılara teslim etmeden önce cihazları ayarlamaya daha iyi çalışır.     
- 1-2 OEM 'lerden cihaz satın alırken veya OEM cihazlarının geniş bir dağıtımına mi sahipsiniz?  
   - Autopilot 'i de destekleyen sınırlı OEM 'lerden satın alıyorsa, Autopilot ile sıkı tümleştirmeden faydalanabilirsiniz. 

## <a name="configure-your-device-settings"></a>Cihaz ayarlarınızı yapılandırın

Azure portal, kuruluşunuzda Azure AD 'ye katılmış cihazların dağıtımını denetlemenize olanak tanır. İlgili ayarları yapılandırmak için, **Azure Active Directory sayfasında**`Devices > Device settings`' i seçin.

### <a name="users-may-join-devices-to-azure-ad"></a>Kullanıcılar cihazları Azure AD 'ye katabilir

Bu seçeneği, dağıtımınızın kapsamına ve Azure AD 'ye katılmış bir cihaz kurulumuna izin vermek istediğiniz herhangi bir seçeneğe göre **Tüm** veya **Seçili** olarak ayarlayın. 

![Kullanıcılar cihazları Azure AD 'ye katabilir](./media/azureadjoin-plan/01.png)

### <a name="additional-local-administrators-on-azure-ad-joined-devices"></a>Azure AD 'ye katılmış cihazlarda ek yerel Yöneticiler

**Seçili** ' i seçin ve tüm Azure AD 'ye katılmış cihazlarda yerel Yöneticiler grubuna eklemek istediğiniz kullanıcıları seçin. 

![Azure AD 'ye katılmış cihazlarda ek yerel Yöneticiler](./media/azureadjoin-plan/02.png)

### <a name="require-multi-factor-auth-to-join-devices"></a>Cihazlara katılması için çok faktörlü kimlik doğrulaması gerektir

Cihazların Azure AD 'ye katılırken MFA gerçekleştirmesini gerektiriyorsa, **"Evet"** seçeneğini belirleyin. MFA kullanarak cihazları Azure AD 'ye katılan kullanıcılar için, cihazın bir ikinci faktör haline gelir.

![Cihazlara katılması için çok faktörlü kimlik doğrulaması gerektir](./media/azureadjoin-plan/03.png)

## <a name="configure-your-mobility-settings"></a>Mobility ayarlarınızı yapılandırın

Mobility ayarlarınızı yapılandırmadan önce, önce bir MDM sağlayıcısı eklemeniz gerekebilir.

**MDM sağlayıcısı eklemek için**:

1. **Azure Active Directory sayfasında**, **yönet** bölümünde, `Mobility (MDM and MAM)`' a tıklayın. 
1. **Uygulama Ekle**' ye tıklayın.
1. Listeden MDM sağlayıcınızı seçin.

   ![Uygulama ekleme](./media/azureadjoin-plan/04.png)

İlgili ayarları yapılandırmak için MDM sağlayıcınızı seçin. 

### <a name="mdm-user-scope"></a>MDM Kullanıcı kapsamı

Dağıtımınızın kapsamına göre **bazılarını** veya **Tümünü** seçin. 

![MDM Kullanıcı kapsamı](./media/azureadjoin-plan/05.png)

Kapsamınızı temel alarak aşağıdakilerden biri olur: 

- **Kullanıcı MDM kapsamında**: Azure AD Premium bir aboneliğiniz varsa, MDM kaydı Azure AD JOIN ile birlikte otomatikleştirilir. Tüm kapsamlı kullanıcıların MDM 'niz için uygun bir lisansı olmalıdır. MDM kaydı bu senaryoda başarısız olursa, Azure AD JOIN de geri alınacaktır.
- **Kullanıcı MDM kapsamında**değil: kullanıcılar MDM kapsamında değilse, hiçbir MDM kaydı olmadan Azure AD JOIN işlemi tamamlanır. Bu, yönetilmeyen bir cihaza neden olur.

### <a name="mdm-urls"></a>MDM URL 'Leri

MDM yapılandırmanızla ilgili üç URL vardır:

- MDM kullanım koşulları URL 'SI
- MDM bulma URL 'SI 
- MDM uyumluluk URL 'SI

![Uygulama ekleme](./media/azureadjoin-plan/06.png)

Her URL 'de önceden tanımlanmış bir varsayılan değer bulunur. Bu alanlar boşsa, daha fazla bilgi için lütfen MDM sağlayıcınızla iletişim kurun.

### <a name="mam-settings"></a>MAM ayarları

MAM, Azure AD 'ye katılması için geçerlidir. 

## <a name="configure-enterprise-state-roaming"></a>Kurumsal durum dolaşımı yapılandırma

Kullanıcıların ayarlarını cihazlar arasında eşitleyebilmesi için Azure AD 'de durum dolaşımı etkinleştirmek istiyorsanız, bkz. [Azure Active Directory Enterprise State Roaming etkinleştirme](enterprise-state-roaming-enable.md). 

**Öneri**: karma Azure AD 'ye katılmış cihazlar için bile bu ayarı etkinleştirin.

## <a name="configure-conditional-access"></a>Koşullu erişimi yapılandırma

Azure AD 'ye katılmış cihazlarınız için yapılandırılmış bir MDM sağlayıcısına sahipseniz, sağlayıcı cihaz yönetimi altında olduğu anda cihazı uyumlu olarak işaretler. 

![Uyumlu cihaz](./media/azureadjoin-plan/46.png)

Bu uygulamayı, [bulut uygulaması için yönetilen cihazların koşullu erişime sahip olmasını gerektirmek](../conditional-access/require-managed-devices.md)için kullanabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [İlk çalıştırma sırasında Azure AD ile yeni bir Windows 10 cihazına katılarak](azuread-joined-devices-frx.md) [iş cihazınızı kuruluşunuzun ağına ekleme](https://docs.microsoft.com/azure/active-directory/user-help/user-help-join-device-on-network)
> 

<!--Image references-->
[1]: ./media/azureadjoin-plan/12.png
