---
title: Azure Active Directory geçişli kimlik doğrulama güvenliğini derinlemesine bakış | Microsoft Docs
description: Bu makalede Azure Active Directory (Azure AD) geçişli kimlik doğrulamanın şirket içi hesaplarınızı nasıl koruduğu açıklanmaktadır
services: active-directory
keywords: Azure AD Connect geçişli kimlik doğrulaması, Active Directory yüklemesi, Azure AD, SSO, çoklu oturum açma için gerekli bileşenler
documentationcenter: ''
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/15/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: d4f9686be08de2589cddadf741dadf243d0e7895
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72174447"
---
# <a name="azure-active-directory-pass-through-authentication-security-deep-dive"></a>Azure Active Directory geçişli kimlik doğrulama güvenliğini derinlemesine bakış

Bu makalede Azure Active Directory (Azure AD) geçişli kimlik doğrulamanın nasıl çalıştığı hakkında daha ayrıntılı bir açıklama sunulmaktadır. Özelliğin güvenlik yönlerine odaklanır. Bu makale, güvenlik ve BT yöneticileri, uyumluluk ve güvenlik ofislerini ve BT güveninden sorumlu ve küçük ve orta ölçekli kuruluşlarda ya da büyük kuruluşlarda uyumluluktan sorumlu olan diğer BT uzmanlarına yöneliktir.

Ele alınan konular şunları içerir:
- Kimlik doğrulama aracılarının nasıl yükleneceği ve kaydedileceği hakkında ayrıntılı teknik bilgiler.
- Kullanıcı oturumu sırasında parolaların şifrelenmesi hakkında ayrıntılı teknik bilgiler.
- Şirket içi kimlik doğrulama aracıları ve Azure AD arasındaki kanalların güvenliği.
- Kimlik doğrulama aracılarının nasıl güvende tutulacağını gösteren ayrıntılı teknik bilgiler.
- Güvenlikle ilgili diğer konular.

## <a name="key-security-capabilities"></a>Anahtar güvenlik özellikleri

Bunlar, bu özelliğin önemli güvenlik yönlerinden oluşur:
- Kiracılar arasında oturum açma istekleri yalıtımı sağlayan güvenli bir çok kiracılı mimari üzerine kurulmuştur.
- Şirket içi parolalar hiçbir biçimde bulutta depolanmaz.
- Parola doğrulama isteklerini dinleyen ve yanıt veren şirket içi kimlik doğrulama aracıları, yalnızca ağınız içinden giden bağlantılar yapar. Bu kimlik doğrulama aracılarını bir çevre ağına (DMZ) yüklemek için gerekli değildir. En iyi yöntem olarak, kimlik doğrulama aracılarını çalıştıran tüm sunucuları katman 0 sistemleri olarak değerlendirin (bkz. [başvuru](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)).
- Yalnızca standart bağlantı noktaları (80 ve 443), kimlik doğrulama aracılarından Azure AD 'ye giden iletişim için kullanılır. Güvenlik duvarınızdaki gelen bağlantı noktalarını açmanıza gerek yoktur. 
  - 443 numaralı bağlantı noktası, kimliği doğrulanmış tüm giden iletişimler için kullanılır.
  - Bağlantı noktası 80 yalnızca sertifika Iptal listelerini (CRL 'Ler) indirmek için kullanılır. Bu özellik tarafından kullanılan sertifikaların hiçbirinin iptal edildiğinden emin olun.
  - Ağ gereksinimlerinin tam listesi için, [Azure Active Directory geçişli kimlik doğrulaması: hızlı başlangıç](how-to-connect-pta-quick-start.md#step-1-check-the-prerequisites)' a bakın.
- Oturum açma sırasında kullanıcıların sağladığı parolalar, şirket içi kimlik doğrulama aracıları Active Directory karşı doğrulama için kabul etmeden önce bulutta şifrelenir.
- Azure AD ile şirket içi kimlik doğrulama Aracısı arasındaki HTTPS kanalı, karşılıklı kimlik doğrulaması kullanılarak güvenli hale getirilir.
- Multi-Factor Authentication (MFA) dahil olmak üzere [Azure AD koşullu erişim ilkeleriyle](../active-directory-conditional-access-azure-portal.md)sorunsuz çalışarak kullanıcı hesaplarınızı korur, [eski kimlik doğrulamasını engellemeyi](../conditional-access/conditions.md) ve [deneme yanılma saldırılarına zorlar](../authentication/howto-password-smart-lockout.md).

## <a name="components-involved"></a>Dahil edilen bileşenler

Azure AD operasyonel, hizmet ve veri güvenliği hakkında genel Ayrıntılar için bkz. [Güven Merkezi](https://azure.microsoft.com/support/trust-center/). Kullanıcı oturumu açma için doğrudan kimlik doğrulama kullandığınızda aşağıdaki bileşenler yer alır:
- **Azure AD STS**: oturum açma isteklerini işleyen ve kullanıcıların tarayıcılarına, istemcilerine veya hizmetlerine güvenlik belirteçleri veren, durum bilgisi olmayan bir güvenlik belirteci HIZMETI (STS).
- **Azure Service Bus**: Kurumsal mesajlaşma ile bulut özellikli iletişim sağlar ve bulut ile şirket içi çözümlere bağlanmanıza yardımcı olan iletişimi geçirir.
- **Azure AD Connect kimlik doğrulama Aracısı**: parola doğrulama isteklerini dinleyen ve yanıt veren şirket içi bir bileşendir.
- **Azure SQL veritabanı**: meta verileri ve şifreleme anahtarları da dahil olmak üzere kiracınızın kimlik doğrulama aracılarıyla ilgili bilgileri tutar.
- **Active Directory**: Kullanıcı hesaplarınızın ve parolalarının depolandığı şirket içi Active Directory.

## <a name="installation-and-registration-of-the-authentication-agents"></a>Kimlik doğrulama aracılarının yüklenmesi ve kaydı

Kimlik doğrulama aracıları, aşağıdakilerden biri olduğunda Azure AD 'ye yüklenir ve kaydedilir:
   - [Azure AD Connect aracılığıyla doğrudan kimlik doğrulamayı etkinleştir](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-quick-start#step-2-enable-the-feature)
   - [Oturum açma isteklerinin yüksek oranda kullanılabilir olmasını sağlamak için daha fazla kimlik doğrulama aracısı ekleyin](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-quick-start#step-4-ensure-high-availability) 
   
Kimlik doğrulama aracısının çalışması için üç ana aşama gerekir:

1. Kimlik doğrulama Aracısı yüklemesi
2. Kimlik doğrulama Aracısı kaydı
3. Kimlik doğrulama Aracısı başlatma

Aşağıdaki bölümlerde bu aşamalar ayrıntılı olarak ele alınmaktadır.

### <a name="authentication-agent-installation"></a>Kimlik doğrulama Aracısı yüklemesi

Yalnızca genel Yöneticiler, bir kimlik doğrulama aracısını (Azure AD Connect veya tek başına) Şirket içi sunucuda yükleyebilir. Yükleme, **Denetim masası** > **Programlar** > **Programlar ve Özellikler** listesine iki yeni giriş ekler:
- Kimlik doğrulama Aracısı uygulaması. Bu uygulama [NetworkService](https://msdn.microsoft.com/library/windows/desktop/ms684272.aspx) ayrıcalıklarıyla çalışır.
- Kimlik Doğrulama aracısını otomatik olarak güncelleştirmek için kullanılan Güncelleştirici uygulaması. Bu uygulama [LocalSystem](https://msdn.microsoft.com/library/windows/desktop/ms684190.aspx) ayrıcalıklarıyla çalışır.

### <a name="authentication-agent-registration"></a>Kimlik doğrulama Aracısı kaydı

Kimlik Doğrulama aracısını yükledikten sonra, kendisini Azure AD 'ye kaydetmeniz gerekir. Azure AD her kimlik doğrulama aracısına, Azure AD ile güvenli iletişim için kullanabileceği benzersiz, dijital kimlik bir sertifika atar.

Kayıt yordamı ayrıca kimlik doğrulama aracısını kiracınızla bağlar. Bu, Azure AD 'nin bu belirli kimlik doğrulama aracısının kiracınız için parola doğrulama isteklerini işlemek için yetkilendirilmiş tek bir olduğunu bilmesini sağlar. Bu yordam, kaydettiğinizde her yeni kimlik doğrulama Aracısı için yinelenir.

Kimlik doğrulama aracıları, kendilerini Azure AD 'ye kaydetmek için aşağıdaki adımları kullanır:

![Aracı kaydı](./media/how-to-connect-pta-security-deep-dive/pta1.png)

1. Azure AD, önce genel yöneticinin kimlik bilgileriyle Azure AD 'de oturum açmasını ister. Oturum açma sırasında, kimlik doğrulama Aracısı genel yönetici adına kullanılabilecek bir erişim belirteci alır.
2. Kimlik doğrulama Aracısı daha sonra bir anahtar çifti oluşturur: ortak anahtar ve özel anahtar.
    - Anahtar çifti standart RSA 2048 bit şifrelemesi aracılığıyla oluşturulur.
    - Özel anahtar, kimlik doğrulama aracısının bulunduğu şirket içi sunucuda kalır.
3. Kimlik doğrulama Aracısı, istekte yer alan aşağıdaki bileşenlerle HTTPS üzerinden Azure AD 'ye bir "kayıt" isteği getirir:
    - Adım 1 ' de alınan erişim belirteci.
    - 2\. adımda oluşturulan ortak anahtar.
    - Sertifika Imzalama Isteği (CSR veya sertifika Isteği). Bu istek, Azure AD ile sertifika yetkilisi (CA) olarak bir dijital kimlik sertifikası için geçerlidir.
4. Azure AD, kayıt isteğindeki erişim belirtecini doğrular ve isteğin genel bir yöneticiden geldiğini doğrular.
5. Daha sonra Azure AD, kimlik doğrulama aracısına bir dijital kimlik sertifikası atar ve geri gönderir.
    - Azure AD 'deki kök sertifika, sertifikayı imzalamak için kullanılır. 

      > [!NOTE]
      > Bu CA Windows güvenilen kök sertifika yetkilileri deposunda _değil_ .
    - CA yalnızca doğrudan kimlik doğrulama özelliği tarafından kullanılır. CA yalnızca kimlik doğrulama Aracısı kaydı sırasında CSR imzalamak için kullanılır.
    -  Diğer Azure AD hizmetlerinden hiçbiri bu CA 'yı kullanmaz.
    - Sertifikanın konusu (ayırt edici ad veya DN) kiracı KIMLIĞINIZ olarak ayarlanır. Bu DN, kiracınızı benzersiz bir şekilde tanımlayan bir GUID 'dir. Bu DN, sertifikayı yalnızca kiracınızla birlikte kullanılmak üzere kapsamlar.
6. Azure AD, kimlik doğrulama aracısının ortak anahtarını yalnızca Azure AD 'nin erişimi olan bir Azure SQL veritabanında depolar.
7. Sertifika (5. adımda verilen), Windows sertifika deposundaki (özellikle [CERT_SYSTEM_STORE_LOCAL_MACHINE](https://msdn.microsoft.com/library/windows/desktop/aa388136.aspx#CERT_SYSTEM_STORE_LOCAL_MACHINE) konumunda) Şirket içi sunucuda depolanır. Hem kimlik doğrulama Aracısı hem de Güncelleştirici uygulamalar tarafından kullanılır.

### <a name="authentication-agent-initialization"></a>Kimlik doğrulama Aracısı başlatma

Kimlik doğrulama Aracısı başladığında, kayıt sonrasında veya bir sunucu yeniden başlatıldıktan sonra ilk kez, Azure AD hizmeti ile güvenli bir şekilde iletişim kurmak ve parola doğrulama isteklerini kabul etmek için bir yol gerekir.

![Aracı başlatma](./media/how-to-connect-pta-security-deep-dive/pta2.png)

Kimlik doğrulama aracılarının nasıl başlatıldığı aşağıda verilmiştir:

1. Kimlik doğrulama Aracısı, Azure AD 'ye giden bir önyükleme isteği yapar. 
    - Bu istek 443 bağlantı noktası üzerinden yapılır ve karşılıklı kimliği doğrulanmış bir HTTPS kanalı üzerinden yapılır. İstek, kimlik doğrulama Aracısı kaydı sırasında verilen sertifikayı kullanır.
2. Azure AD, kiracınız için benzersiz olan ve kiracı KIMLIĞINIZ tarafından tanımlanan Azure Service Bus kuyruğuna bir erişim anahtarı sağlayarak isteğe yanıt verir.
3. Kimlik doğrulama Aracısı kalıcı bir giden HTTPS bağlantısı (443 numaralı bağlantı noktası üzerinden) kuyruğa yapar. 
    - Kimlik doğrulama Aracısı artık parola doğrulama isteklerini almak ve işlemek için hazırdır.

Kiracınızda kayıtlı birden fazla kimlik doğrulama aracınız varsa, başlatma yordamı her birinin aynı Service Bus kuyruğuna bağlanmasını sağlar. 

## <a name="process-sign-in-requests"></a>Oturum açma isteklerini işle 

Aşağıdaki diyagramda, geçişli kimlik doğrulamanın Kullanıcı oturum açma isteklerini nasıl işlediği gösterilmektedir.

![Oturum açma işlemi](./media/how-to-connect-pta-security-deep-dive/pta3.png)

Doğrudan kimlik doğrulaması, bir Kullanıcı oturum açma isteğini aşağıdaki şekilde işler: 

1. Bir Kullanıcı bir uygulamaya (örneğin, [Outlook Web App](https://outlook.office365.com/owa)) erişmeyi dener.
2. Kullanıcı önceden oturum açmamışsa, uygulama tarayıcıyı Azure AD oturum açma sayfasına yönlendirir.
3. Azure AD STS hizmeti, **Kullanıcı oturum açma** sayfasıyla geri yanıt verir.
4. Kullanıcı Kullanıcı adını **Kullanıcı oturum açma** sayfasına girer ve sonra **İleri** düğmesini seçer.
5. Kullanıcı parolasını **Kullanıcı oturum açma** sayfasına girer ve ardından **oturum aç** düğmesini seçer.
6. Kullanıcı adı ve parola, bir HTTPS POST isteğinde Azure AD STS 'ye gönderilir.
7. Azure AD STS, kiracınızda kayıtlı tüm kimlik doğrulama aracılarının ortak anahtarlarını Azure SQL veritabanından alır ve parolayı kullanarak şifreler.
    - Kiracınızda kayıtlı "N" kimlik doğrulama aracıları için "N" şifrelenmiş parola değerleri üretir.
8. Azure AD STS, Kullanıcı adı ve şifreli parola değerlerinden oluşan parola doğrulama isteğini kiracınıza özgü Service Bus kuyruğuna koyar.
9. Başlatılmış kimlik doğrulama aracıları Service Bus kuyruğuna kalıcı olarak bağlandığından, kullanılabilir kimlik doğrulama aracılarından biri parola doğrulama isteğini alır.
10. Kimlik doğrulama Aracısı ortak anahtarına özgü şifrelenmiş parola değerini bir tanımlayıcı kullanarak bulur ve onun özel anahtarını kullanarak şifresini çözer.
11. Kimlik doğrulama Aracısı, **LOGON32_LOGON_NETWORK**olarak ayarlanan **dwlogontype** PARAMETRESI ile [Win32 LogonUser API](https://msdn.microsoft.com/library/windows/desktop/aa378184.aspx) 'sini kullanarak kullanıcı adını ve parolayı şirket içi Active Directory karşı doğrulamaya çalışır. 
    - Bu API, kullanıcıların bir Federasyon oturum açma senaryosunda oturum açması için Active Directory Federasyon Hizmetleri (AD FS) (AD FS) tarafından kullanılan API 'dir.
    - Bu API, etki alanı denetleyicisini bulmak için Windows Server 'daki standart çözümleme işlemini kullanır.
12. Kimlik doğrulama Aracısı başarı, Kullanıcı adı veya parola yanlış veya parolanın geçerliliği aşıldığı gibi Active Directory sonucunu alır.

   > [!NOTE]
   > Kimlik doğrulama Aracısı oturum açma işlemi sırasında başarısız olursa, tüm oturum açma isteği bırakılır. Bir kimlik doğrulama aracısından, şirket içi bir kimlik doğrulama aracısına yönelik oturum açma isteklerini el ile kapatma. Bu aracılar, birbirleriyle değil, yalnızca bulutla iletişim kurar.
   
13. Kimlik doğrulama Aracısı, 443 numaralı bağlantı noktası üzerinden karşılıklı kimlik doğrulamalı bir HTTPS kanalı üzerinden sonucu Azure AD STS 'ye geri iletir. Karşılıklı kimlik doğrulama, kayıt sırasında kimlik doğrulama aracısına daha önce verilen sertifikayı kullanır.
14. Azure AD STS, bu sonucun kiracınızdaki belirli oturum açma isteğiyle ilişkili olduğunu doğrular.
15. Azure AD STS, yapılandırılan oturum açma yordamıyla devam eder. Örneğin, parola doğrulaması başarılı olduysa, Kullanıcı Multi-Factor Authentication veya uygulamaya geri yönlendirilmek isteyebilir.

## <a name="operational-security-of-the-authentication-agents"></a>Kimlik doğrulama aracılarının işletimsel güvenliği

Doğrudan kimlik doğrulamanın güvenli kalmasını sağlamak için, Azure AD kimlik doğrulama aracılarının sertifikalarını düzenli aralıklarla yeniler. Azure AD, yenilemeler tetikler. Yenilemeler, kimlik doğrulama aracılarının kendilerine tabidir.

![İşletimsel Güvenlik](./media/how-to-connect-pta-security-deep-dive/pta4.png)

Azure AD ile bir kimlik doğrulama aracısının güvenini yenilemek için:

1. Kimlik doğrulama Aracısı, sertifikasını yenileme zamanının olup olmadığını kontrol etmek için Azure AD 'yi birkaç saatte bir düzenli olarak ping yapar. Sertifika, süresi dolmadan 30 gün önce yenilenir.
    - Bu denetim, karşılıklı kimliği doğrulanmış bir HTTPS kanalı üzerinden yapılır ve kayıt sırasında verilen sertifikayı kullanır.
2. Hizmet yenileme zamanının olduğunu gösteriyorsa, kimlik doğrulama Aracısı yeni bir anahtar çifti oluşturur: ortak anahtar ve özel anahtar.
    - Bu anahtarlar standart RSA 2048 bit şifrelemesi aracılığıyla oluşturulur.
    - Özel anahtar, şirket içi sunucudan hiçbir şekilde ayrılmayacaktır.
3. Kimlik doğrulama Aracısı daha sonra isteğe dahil edilen aşağıdaki bileşenlerle HTTPS üzerinden Azure AD 'ye bir "sertifika yenilemesi" isteği getirir:
    - Windows sertifika deposundaki CERT_SYSTEM_STORE_LOCAL_MACHINE konumundan alınan mevcut sertifika. Bu yordamda yer alan genel yönetici yok, bu nedenle genel yönetici adına erişim belirteci gerekmez.
    - 2\. adımda oluşturulan ortak anahtar.
    - Sertifika Imzalama Isteği (CSR veya sertifika Isteği). Bu istek, sertifika yetkilisi olarak Azure AD ile yeni bir dijital kimlik sertifikası için geçerlidir.
4. Azure AD, sertifika yenileme isteğindeki mevcut sertifikayı doğrular. Sonra, isteğin kiracınızda kayıtlı bir kimlik doğrulama aracısından geldiğini doğrular.
5. Mevcut sertifika hala geçerliyse, Azure AD yeni bir dijital kimlik sertifikasını imzalar ve yeni sertifikayı kimlik doğrulama aracısına geri yayınlar. 
6. Mevcut sertifikanın süresi dolmuşsa Azure AD, kimlik doğrulama aracısını kiracınızın kayıtlı kimlik doğrulama aracıları listesinden siler. Daha sonra genel bir yöneticinin yeni bir kimlik doğrulama aracısını el ile yüklemesi ve kaydetmesi gerekir.
    - Sertifikayı imzalamak için Azure AD kök CA 'sını kullanın.
    - Kiracınızı benzersiz bir şekilde tanımlayan GUID olan kiracı KIMLIĞINIZLE sertifikanın konusunu (ayırt edici ad veya DN) ayarlayın. DN, sertifikayı yalnızca kiracınızla kapsamlar.
6. Azure AD, kimlik doğrulama aracısının yeni ortak anahtarını yalnızca erişimi olan bir Azure SQL veritabanında depolar. Ayrıca, kimlik doğrulama aracısıyla ilişkili eski ortak anahtarı geçersiz kılar.
7. Yeni sertifika (5. adımda verilen), daha sonra Windows sertifika depolama alanındaki sunucuda (özellikle [CERT_SYSTEM_STORE_CURRENT_USER](https://msdn.microsoft.com/library/windows/desktop/aa388136.aspx#CERT_SYSTEM_STORE_CURRENT_USER) konumunda) depolanır.
    - Güven yenileme yordamı etkileşimli olmayan bir şekilde yapıldığından (genel yönetici mevcut olmadığında), kimlik doğrulama aracısının artık CERT_SYSTEM_STORE_LOCAL_MACHINE konumundaki mevcut sertifikayı güncelleştirme erişimi yoktur. 
    
   > [!NOTE]
   > Bu yordam, sertifikayı CERT_SYSTEM_STORE_LOCAL_MACHINE konumundan kaldırmaz.
8. Yeni sertifika bu noktadan kimlik doğrulaması için kullanılır. Sertifikanın sonraki yenilenmesi, CERT_SYSTEM_STORE_LOCAL_MACHINE konumundaki sertifikanın yerini alır.

## <a name="auto-update-of-the-authentication-agents"></a>Kimlik doğrulama aracılarının otomatik güncelleştirilmesi

Yeni bir sürüm (hata onarımları veya performans geliştirmeleriyle) yayınlandığında, Güncelleştirici uygulaması kimlik doğrulama aracısını otomatik olarak güncelleştirir. Güncelleştirici uygulaması, kiracınız için herhangi bir parola doğrulama isteğini işlemez.

Azure AD, yazılımın yeni sürümünü imzalı bir **Windows Installer paketi (MSI)** olarak barındırır. MSI, Özet algoritması olarak SHA256 ile [Microsoft Authenticode](https://msdn.microsoft.com/library/ms537359.aspx) kullanılarak imzalanır. 

![Otomatik güncelleştirme](./media/how-to-connect-pta-security-deep-dive/pta5.png)

Bir kimlik doğrulama aracısını otomatik olarak güncelleştirmek için:

1. Güncelleştirici uygulaması, kimlik doğrulama aracısının yeni bir sürümünün mevcut olup olmadığını denetlemek için saatte bir Azure AD 'ye ping atar. 
    - Bu denetim, kayıt sırasında verilen aynı sertifikayı kullanarak karşılıklı kimliği doğrulanmış bir HTTPS kanalı üzerinden yapılır. Kimlik doğrulama Aracısı ve Güncelleştirici, sunucuda depolanan sertifikayı paylaşır.
2. Yeni bir sürüm varsa, Azure AD imzalı MSI 'yi Güncelleştirici 'e geri döndürür.
3. Güncelleştirici, MSI 'nin Microsoft tarafından imzalandığını doğrular.
4. Güncelleştirici, MSI 'yi çalıştırır. Bu eylem aşağıdaki adımları içerir:

   > [!NOTE]
   > Güncelleştirici [yerel sistem](https://msdn.microsoft.com/library/windows/desktop/ms684190.aspx) ayrıcalıklarıyla çalışır.

    - Kimlik doğrulama Aracısı hizmetini durduruyor
    - Kimlik doğrulama aracısının yeni sürümünü sunucuya yükleme
    - Kimlik doğrulama Aracısı hizmetini yeniden başlatır

>[!NOTE]
>Kiracınızda kayıtlı birden fazla kimlik doğrulama aracınız varsa, Azure AD sertifikaları yenilemez veya aynı anda güncelleştiremez. Bunun yerine, Azure AD, oturum açma isteklerinin yüksek oranda kullanılabilirliğini sağlamak için tek seferde bir kez yapılır.
>


## <a name="next-steps"></a>Sonraki adımlar
- [Geçerli sınırlamalar](how-to-connect-pta-current-limitations.md): hangi senaryoların desteklendiğini ve hangilerinin desteklenmediğini öğrenin.
- [Hızlı başlangıç](how-to-connect-pta-quick-start.md): Azure AD geçişli kimlik doğrulaması ile çalışmaya başlayın.
- [AD FS 'den geçişli kimlik doğrulamaya geçiş](https://aka.ms/adfstoptadpdownload) -AD FS (veya diğer Federasyon teknolojileri) üzerinden doğrudan kimlik doğrulamaya geçiş yapmak için ayrıntılı kılavuz.
- [Akıllı kilitleme](../authentication/howto-password-smart-lockout.md): Kullanıcı hesaplarını korumak için kiracınızda akıllı kilitleme özelliğini yapılandırın.
- [Nasıl çalıştığı](how-to-connect-pta-how-it-works.md): Azure AD geçişli kimlik doğrulamanın nasıl çalıştığı hakkında temel bilgileri öğrenin.
- [Sık sorulan sorular](how-to-connect-pta-faq.md): sık sorulan soruların yanıtlarını bulun.
- [Sorun giderme](tshoot-connect-pass-through-authentication.md): doğrudan kimlik doğrulama özelliğiyle yaygın sorunları çözmeyi öğrenin.
- [Azure AD sorunsuz SSO](how-to-connect-sso.md): Bu tamamlayıcı özellik hakkında daha fazla bilgi edinin.
