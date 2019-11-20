---
title: Azure AD 'de eski cihazları yönetme | Microsoft Docs
description: Azure Active Directory ' de kayıtlı cihazların veritabanından eski cihazları kaldırmayı öğrenin.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: a2e92ca85c485f8c93fc9202b9084ec37d7506e1
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73175057"
---
# <a name="how-to-manage-stale-devices-in-azure-ad"></a>Nasıl yapılır: Azure AD 'de eski cihazları yönetme

İdeal koşullarda, kayıtlı cihazların yaşam döngüsünün tamamlanması için, bu cihazlara artık gerek kalmadığında kayıtlarının silinmesi gerekir. Bununla birlikte, örneğin kayıp, çalınmış, bozulmuş cihazlardan veya işletim sistemi yeniden yüklemelerinden dolayı ortamınızda eski cihazlar bulunur. BT yöneticisi olarak, büyük olasılıkla eski cihazları kaldırmak için bir yönteminiz olmasını istersiniz; böylelikle kaynaklarınızın gerçekten yönetilmesi gereken cihazların yönetimine odaklanmasını sağlayabilirsiniz.

Bu makalede, ortamınızdaki eski cihazları verimli bir yöntemle nasıl yönetebileceğinizi öğreneceksiniz.
  

## <a name="what-is-a-stale-device"></a>Eski cihaz nedir?

Eski cihaz, Azure AD'ye kayıtlı olan ama belirli bir zaman çerçevesinde herhangi bir bulut uygulamasına erişmek için kullanılmamış olan cihazdır. Eski cihazlar, kiracıdaki cihazlarınızı ve kullanıcılarınızı yönetme ve destekleme becerinizi etkiler çünkü: 

- Çift cihazlar yardım masanızın hangi cihazın şu anda etkin olduğunu belirlemesini zorlaştırabilir.
- Cihaz sayısındaki artış gereksiz cihaz geri yazmaları oluşturarak AAD Connect eşitlemelerinin süresini uzatır.
- Genel sağlık açısından ve uyumluluğun gereklerini karşılamak için, cihazların temiz durumda olmasını isteyebilirsiniz. 

Azure AD'deki eski cihazlar kuruluşunuzdaki cihazlara yönelik genel yaşam döngüsü ilkelerine müdahale edebilir.

## <a name="detect-stale-devices"></a>Eski cihazları algılama

Eski cihaz, belirli bir zaman çerçevesinde hiçbir bulut uygulamasına erişmek için kullanılmamış kayıtlı cihaz olarak tanımlandığından, eski cihazları algılamak için zaman damgasıyla ilgili bir özellik gerekir. Azure AD'de bu özellik **ApproximateLastLogonTimestamp** veya **etkinlik zaman damgası** olarak adlandırılır. Şimdi ile **etkinlik zaman damgasının** değeri arasındaki delta etkin cihazlar için tanımladığınız zaman çerçevesini aşıyorsa, cihazın eski olduğu düşünülür. Bu **etkinlik zaman damgası** şu anda genel önizleme aşamasındadır.

## <a name="how-is-the-value-of-the-activity-timestamp-managed"></a>Etkinlik zaman damgasının değeri nasıl yönetilir?  

Etkinlik zaman damgasının hesaplanması, bir cihazın kimlik doğrulama girişimiyle tetiklenir. Azure AD aşağıdaki durumlarda etkinlik zaman damgasını hesaplar:

- [Yönetilen cihazlar](../conditional-access/require-managed-devices.md) veya [onaylanan istemci uygulamaları](../conditional-access/app-based-conditional-access.md) gerektiren bir koşullu erişim ilkesi tetiklendi.
- Azure AD'ye katılmış veya hibrit Azure AD'ye katılmış Windows 10 cihazları ağda etkin olduğunda. 
- Intune tarafından yönetilen cihazlar hizmete giriş yaptığında.

Etkinlik zaman damgasının mevcut değeri ile geçerli değer arasındaki Delta 14 günden daha uzun (+/-5 gün sapması), varolan değer yeni değerle değiştirilmiştir.

## <a name="how-do-i-get-the-activity-timestamp"></a>Etkinlik zaman damgasını nasıl alabilirim?

Etkinlik zaman damgasının değerini almak için iki seçeneğiniz vardır:

- Azure portaldaki [cihazlar sayfasının](https://portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/Devices) **Etkinlik** sütunu

    ![Etkinlik zaman damgası](./media/manage-stale-devices/01.png)

- [Get-MsolDevice](https://docs.microsoft.com/powershell/module/msonline/get-msoldevice?view=azureadps-1.0) cmdlet'i

    ![Etkinlik zaman damgası](./media/manage-stale-devices/02.png)

## <a name="plan-the-cleanup-of-your-stale-devices"></a>Eski cihazların temizliğini planlama

Ortamınızda eski cihazları verimli bir şekilde temizlemek için, ilgili bir ilke tanımlamalısınız. Bu ilke, eski cihazlarla ilgili tüm konuları yakaladığınızdan emin olmanıza yardımcı olur. Aşağıdaki bölümlerde yaygın ilke konularına ilişkin örnekler sağlanmıştır. 

### <a name="cleanup-account"></a>Temizleme hesabı

Azure AD'de bir cihazı güncelleştirmek için, aşağıdaki rollerden birinin atandığı bir hesap gerekir:

- Genel Yönetici
- Bulut Cihaz Yöneticisi
- Intune Hizmet Yöneticisi

Temizleme ilkenizde, gerekli rollerin atandığı hesapları seçin. 

### <a name="timeframe"></a>Zaman çerçevesi

Eski cihaz için göstergeniz olacak bir zaman çerçevesi tanımlayın. Zaman dilimini tanımlarken, etkinlik zaman damgasını değere güncelleştirmek için belirtilen pencereyi çarpanlara ayırın. Örneğin, eski bir cihaz için 21 günden (varyansı içerir) daha küçük bir zaman damgasını göz önünde bulundurmamanız gerekir. Eski olmayan bir cihazın eski gibi görünmesine neden olabilecek senaryolar vardır. Örneğin, etkilenen cihazın sahibi, eski cihazlara ilişkin zaman çerçevenizi aşan bir süre  tatile çıkmış veya hastalık iznine ayrılmış olabilir.

### <a name="disable-devices"></a>Cihazları devre dışı bırakma

Eski gibi görünen bir cihazı hemen silmeniz önerilmez, çünkü yanlış pozitif göstergelerde silme işlemini geri almak mümkün değildir. En iyi yöntem, cihazı silmeden önce belirli bir bekleme süresince devre dışı bırakmaktır. İlkenizde, silmeden önce cihazı devre dışı bırakmak için bir zaman çerçevesi tanımlayın.

### <a name="mdm-controlled-devices"></a>MDM tarafından denetlenen cihazlar

Cihazınız Intune'un veya başka bir MDM çözümünün denetimi altındaysa, cihazı devre dışı bırakmadan veya silmeden önce yönetim sisteminden kaldırın.

### <a name="system-managed-devices"></a>Sistem tarafından yönetilen cihazlar

Sistem tarafından yönetilen cihazları silmeyin. Bunlar genellikle auto-pilot gibi cihazlardır. Silinen bu cihazlar yeniden sağlanmıyor. `get-msoldevice` cmdlet'i sistem tarafından yönetilen cihazları varsayılan olarak dışlar. 

### <a name="hybrid-azure-ad-joined-devices"></a>Hibrit Azure AD’ye katılmış cihazlar

Hibrit Azure AD'ye katılmış cihazlarınızın şirket içi eski cihaz yönetimi ilkelerinize uyması gerekir. 

Azure AD'yi temizlemek için:

- **Windows 10 cihazları** - Windows 10 cihazlarını şirket içi AD'nizde devre dışı bırakın veya silin, sonra da Azure AD Connect'in değişen cihaz durumunu Azure AD'ye eşitlemesini sağlayın.
- **Windows 7/8** -ŞIRKET içi ad 'nizin Windows 7/8 cihazlarını devre dışı bırakın veya silin. Windows 7/8 cihazlarını Azure AD'de devre dışı bırakmak veya silmek için Azure AD Connect kullanamazsınız. Bunun yerine, şirket içi kuruluşunuzda değişiklik yaptığınızda, Azure AD 'de devre dışı bırakmanız/silmeniz gerekir.

> [!NOTE]
>* Şirket içi AD 'de veya Azure AD 'de cihazları silmek, istemcide kaydı kaldırmaz. Yalnızca bir kimlik olarak cihaz kullanan kaynaklara erişimi engeller (örn. koşullu erişim). [İstemcide kayıt kaldırma](faq.md#hybrid-azure-ad-join-faq)hakkında daha fazla bilgi edinin.
>* Bir Windows 10 cihazını yalnızca Azure AD 'de silmek, Azure AD Connect 'i kullanarak cihazı şirket içi cihazınızdan yeniden eşitler, ancak "bekliyor" durumunda yeni bir nesne olarak yeniden eşitlenir. Cihazda yeniden kayıt gereklidir.
>* Windows 10/Server 2016 cihazları için eşitleme kapsamından cihaz kaldırma, Azure AD cihazını silecektir. Bunu eşitleme kapsamına geri eklemek, "bekleyen" durumuna yeni bir nesne yerleştirir. Cihazın yeniden kaydı gereklidir.
>* Windows 10 cihazlarının eşitlenmesi için Azure AD Connect kullanmıyorsanız (örn. yalnızca kayıt için AD FS kullanıyorsanız), Windows 7/8 cihazlarına benzer yaşam döngüsünü yönetmeniz gerekir.


### <a name="azure-ad-joined-devices"></a>Azure AD’ye katılmış cihazlar

Azure AD'ye katılmış cihazları Azure AD'de devre dışı bırakın veya silin.

> [!NOTE]
>* Bir Azure AD cihazının silinmesi, istemcide kaydı kaldırmaz. Yalnızca bir kimlik olarak cihaz kullanan kaynaklara erişimi engeller (örn. koşullu erişim). 
>* [Azure AD 'ye nasıl katılalacağı](faq.md#azure-ad-join-faq) hakkında daha fazla bilgi edinin 

### <a name="azure-ad-registered-devices"></a>Azure AD kayıtlı cihazlar

Azure AD'ye kayıtlı cihazları Azure AD'de devre dışı bırakın veya silin.

> [!NOTE]
>* Azure AD 'de kayıtlı bir cihazın Azure AD 'de silinmesi, istemcide kaydı kaldırmaz. Yalnızca bir kimlik olarak cihaz kullanan kaynaklara erişimi engeller (örn. koşullu erişim).
>* [İstemcide kayıt kaldırma](faq.md#azure-ad-register-faq) hakkında daha fazla bilgi edinin

## <a name="clean-up-stale-devices-in-the-azure-portal"></a>Eski cihazları Azure portalda temizleme  

Eski cihazları Azure portalda temizleyebilirsiniz ama bu işlemi PowerShell betiği kullanarak gerçekleştirmek daha verimli olur. Zaman damgası filtresini kullanmak ve auto-pilot gibi sistem tarafından yönetilen cihazları filtreleyip dışarıda bırakmak için en son PowerShell V1 modülünü kullanın. Bu noktada PowerShell V2'nin kullanılması önerilmez.

Tipik bir yordam aşağıdaki adımlardan oluşur:

1. [Connect-MsolService](https://docs.microsoft.com/powershell/module/msonline/connect-msolservice?view=azureadps-1.0) cmdlet'ini kullanarak Azure Active Directory'ye bağlanma
1. Cihaz listesini alma
1. [Disable-MsolDevice](https://docs.microsoft.com/powershell/module/msonline/disable-msoldevice?view=azureadps-1.0) cmdlet'ini kullanarak cihazı devre dışı bırakma. 
1. Cihazı silmeden önce seçtiğiniz yetkisiz kullanım süresinin tamamlanmasını bekleyin.
1. [Remove-MsolDevice](https://docs.microsoft.com/powershell/module/msonline/remove-msoldevice?view=azureadps-1.0) cmdlet'ini kullanarak cihazı kaldırma.

### <a name="get-the-list-of-devices"></a>Cihaz listesini alma

Tüm cihazları almak ve döndürülen verileri CSV dosyasında depolamak için:

```PowerShell
Get-MsolDevice -all | select-object -Property Enabled, DeviceId, DisplayName, DeviceTrustType, Approxi
mateLastLogonTimestamp | export-csv devicelist-summary.csv
```

Dizininizde çok sayıda cihaz varsa, döndürülen cihaz sayısını daraltmak için zaman damgası filtresini kullanın. Zaman damgası belirli bir tarihten daha eski olan tüm cihazları almak ve döndürülen verileri CSV dosyasında depolamak için: 

```PowerShell
$dt = [datetime]’2017/01/01’
Get-MsolDevice -all -LogonTimeBefore $dt | select-object -Property Enabled, DeviceId, DisplayName, DeviceTrustType, ApproximateLastLogonTimestamp | export-csv devicelist-olderthan-Jan-1-2017-summary.csv
```

## <a name="what-you-should-know"></a>Bilmeniz gerekenler

### <a name="why-is-the-timestamp-not-updated-more-frequently"></a>Zaman damgası neden daha sık güncelleştirilmiyor?

Zaman damgası cihaz yaşam döngüsü senaryolarını desteklemek için güncelleştirilir. Bu bir denetim değildir. Cihazda daha sık güncelleştirmeler için oturum açma denetim günlüklerini kullanın.

### <a name="why-should-i-worry-about-my-bitlocker-keys"></a>BitLocker anahtarlarımla ilgili olarak neden kaygılanmam gerekiyor?

Windows 10 cihazlarında BitLocker anahtarları yapılandırıldığında, bu anahtarlar Azure AD'deki cihaz nesnesinde depolanır. Eski cihazı silerseniz, cihazda depolanan BitLocker anahtarlarını da silersiniz. Eski cihazı silmeden önce, temizleme ilkenizin cihazınızın gerçek yaşam döngüsüyle uyumlu olup olmadığını saptamalısınız. 

### <a name="why-should-i-worry-about-windows-autopilot-devices"></a>Windows Autopilot cihazları hakkında neden endişelenmeliyim?

Bir Azure AD cihazı bir Windows Autopilot nesnesiyle ilişkilendirildiğinde, cihazın gelecekte yeniden kullanılması durumunda aşağıdaki üç senaryo meydana gelebilir:
- Windows Autopilot Kullanıcı odaklı dağıtımlarla, beyaz gloona kullanmadan yeni bir Azure AD cihazı oluşturulur, ancak bu, ZTDıD ile etiketlenmeyecektir.
- Windows Autopilot Self-dağıtım modu dağıtımları ile, bir Azure AD cihazının ilişkilendirilmesi bulunamadığı için bunlar başarısız olur.  (Bu, hiçbir "ımpster" cihazlarının kimlik bilgileri olmadan Azure AD 'ye katılmayı denediğinizden emin olmak için bir güvenlik mekanizmasıdır.) Hata, ZTDıD uyuşmazlığını gösterir.
- Windows Autopilot beyaz eldiven dağıtımlarında, ilişkili bir Azure AD cihazı bulunamadığı için bunlar başarısız olur. (Arka planda, beyaz eldiven dağıtımları aynı güvenlik mekanizmalarına zorlayabilmeleri için aynı kendi kendine dağıtım modu işlemini kullanır.)

### <a name="how-do-i-know-all-the-type-of-devices-joined"></a>Katılmış olan tüm cihaz türlerini nasıl bilebilirim?

Farklı türler hakkında daha fazla bilgi edinmek için, bkz. [Cihaz yönetimine genel bakış](overview.md).

### <a name="what-happens-when-i-disable-a-device"></a>Cihazı devre dışı bıraktığımda ne olur?

Cihazın Azure AD'de kimliğini doğrulaması için kullanılan tüm kimlik doğrulamaları reddedilir. Sık karşılaşılan örnekler:

- **Hibrit Azure AD'ye katılan cihaz** - Kullanıcılar cihazı şirket içi etki alanlarında oturum açmak için kullanabilir. Bununla birlikte, Office 365 gibi Azure AD kaynaklarına erişemez.
- **Azure AD'ye katılan cihaz** - Kullanıcılar cihazı oturum açmak için kullanamaz. 
- **Mobil cihazlar** - Kullanıcılar Office 365 gibi Azure AD kaynaklarına erişemez. 

## <a name="next-steps"></a>Sonraki adımlar

Azure portal'da cihaz yönetimine ilişkin genel bir bakış edinmek için bkz. [Azure portal'ı kullanarak cihazları yönetme](device-management-azure-portal.md)
