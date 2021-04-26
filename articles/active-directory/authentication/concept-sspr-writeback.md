---
title: Self servis parola sıfırlama ile şirket içi parola geri yazma-Azure Active Directory
description: Azure Active Directory 'teki parola değiştirme veya sıfırlama olaylarının şirket içi dizin ortamına nasıl geri yazılabileceğini öğrenin
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4b8a84da331568d36b6f6910054fdb2aea76f490
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530334"
---
# <a name="how-does-self-service-password-reset-writeback-work-in-azure-active-directory"></a>Self servis parola sıfırlama geri yazma özelliği Azure Active Directory nasıl çalışır?

Azure Active Directory (Azure AD) self servis parola sıfırlama (SSPR), kullanıcıların buluttaki parolalarını sıfırlamasına izin verir, ancak çoğu şirket, kullanıcılarının bulunduğu şirket içi Active Directory Domain Services (AD DS) ortamına da sahiptir. Parola geri yazma özelliği, buluttaki parola değişikliklerinin gerçek zamanlı olarak mevcut bir şirket içi dizine yazılmasına izin veren [Azure AD Connect](../hybrid/whatis-hybrid-identity.md) etkinleştirilmiş bir özelliktir. Bu yapılandırmada, kullanıcılar bulutta SSPR kullanarak parolalarını değiştirirken veya sıfırlarsa da, güncelleştirilmiş parolalar şirket içi AD DS ortamına geri yazılır.

> [!IMPORTANT]
> Bu kavramsal makalede, self servis parola sıfırlama geri yazmanın nasıl çalıştığı bir yöneticiye açıklanmaktadır. Self servis parola sıfırlama için zaten kayıtlı bir son kullanıcı varsa ve hesabınıza geri dönmek istiyorsanız adresine gidin https://aka.ms/sspr .
>
> BT ekibiniz kendi parolanızı sıfırlama özelliğini etkinleştirmediyseniz, ek yardım için yardım masasına ulaşın.

Parola geri yazma, aşağıdaki karma kimlik modellerini kullanan ortamlarda desteklenir:

* [Parola karması eşitleme](../hybrid/how-to-connect-password-hash-synchronization.md)
* [Geçişli kimlik doğrulaması](../hybrid/how-to-connect-pta.md)
* [Active Directory Federation Services (Active Directory Federasyon Hizmetleri)](../hybrid/how-to-connect-fed-management.md)

Parola geri yazma özelliği aşağıdaki özellikleri sağlar:

* Şirket **içi Active Directory Domain Services (AD DS) parola Ilkeleri zorlaması**: bir Kullanıcı parolasını sıfırladığında, bu dizine işlemeden önce şirket içi AD DS ilkenizi karşıladığından emin olmak denetlenir. Bu gözden geçirme geçmişi, karmaşıklığı, yaşı, parola filtrelerini ve AD DS tanımladığınız diğer parola kısıtlamalarını denetlemeyi içerir.
* **Sıfır gecikme geri bildirimi**: parola geri yazma işlemi zaman uyumlu bir işlemdir. Kullanıcılar, parolaları ilkeyi karşılamıyorsa veya herhangi bir nedenden dolayı sıfırlanıp değiştirimezse anında bilgilendirilir.
* , **Erişim panelinden parola değişikliklerini destekler ve Microsoft 365**: Federasyon veya parola karması eşitlenen kullanıcılar, son kullanma zamanı veya süre dolmayan parolalarını değiştirmek için bu parolalar geri AD DS olarak yazılır.
* **, Bir yönetici tarafından Azure Portal bir yönetici tarafından sıfırlanırsa parola geri yazma özelliğini destekler**: yönetici, bir kullanıcının [Azure Portal](https://portal.azure.com)parolasını sıfırladığında, bu kullanıcı federe veya parola karması eşitlendiğinde, parola şirket içine geri yazılır. Bu işlevsellik şu anda Office Yönetim Portalı 'nda desteklenmiyor.
* **Tüm gelen güvenlik duvarı kuralları gerektirmez**: parola geri yazma, temel bir iletişim kanalı olarak Azure Service Bus geçişini kullanır. Tüm iletişimler 443 numaralı bağlantı noktasından dışarıya gönderilir.

> [!NOTE]
> Şirket içi AD içindeki korumalı gruplar içinde bulunan yönetici hesapları parola geri yazma ile birlikte kullanılabilir. Yöneticiler buluttaki parolalarını değiştirebilir, ancak unutulan bir parolayı sıfırlamak için parola sıfırlama kullanamaz. Korumalı gruplar hakkında daha fazla bilgi için, bkz. [AD DS korunan hesaplar ve gruplar](/windows-server/identity/ad-ds/plan/security-best-practices/appendix-c--protected-accounts-and-groups-in-active-directory).

SSPR geri yazma 'yı kullanmaya başlamak için aşağıdaki öğreticiyi izleyin:

> [!div class="nextstepaction"]
> [Öğretici: Self servis parola sıfırlama (SSPR) geri yazmayı etkinleştirme](./tutorial-enable-sspr-writeback.md)

## <a name="how-password-writeback-works"></a>Parola geri yazma nasıl çalışır?

Bir Federasyon veya parola karması eşitlendiğinde Kullanıcı, bulutta parolalarını sıfırlamayı veya değiştirmeyi denediğinde aşağıdaki eylemler gerçekleşir:

1. Kullanıcının ne tür bir parola olduğunu görmek için bir denetim gerçekleştirilir. Parola şirket içinde yönetiliyorsa:
   * Geri yazma hizmetinin çalışır duruma getirilmediğini görmek için bir denetim gerçekleştirilir. Varsa, Kullanıcı devam edebilir.
   * Geri yazma hizmeti kapalıysa, Kullanıcı parolasının Şu anda sıfırlanıp sıfırlanmama konusunda bilgilendirilir.
1. Ardından, Kullanıcı uygun kimlik doğrulama kapılarını geçirir ve **Parolayı Sıfırla** sayfasına ulaşır.
1. Kullanıcı yeni bir parola seçer ve bunu onaylar.
1. Kullanıcı **Gönder**' i seçtiğinde, düz metin parolası, geri yazma kurulum işlemi sırasında oluşturulan bir simetrik anahtarla şifrelenir.
1. Şifrelenmiş parola, kiracı 'ya özgü Service Bus geçişinizi (geri yazma kurulum işlemi sırasında sizin için ayarlanan) bir HTTPS kanalı üzerinden gönderilen bir yüke dahil edilmiştir. Bu geçiş, yalnızca şirket içi yüklemenizin bildiği rastgele oluşturulmuş bir parolayla korunmaktadır.
1. İleti Service Bus 'a ulaştıktan sonra, parola sıfırlama uç noktası otomatik olarak uyandırır ve bekleyen bir sıfırlama isteğine sahip olduğunu görür.
1. Daha sonra hizmet, bulut bağlayıcısı özniteliğini kullanarak kullanıcıyı arar. Bu aramanın başarılı olması için aşağıdaki koşulların karşılanması gerekir:

   * Kullanıcı nesnesi AD DS bağlayıcı alanında bulunmalıdır.
   * Kullanıcı nesnesi, karşılık gelen meta veri deposu (MV) nesnesine bağlanmalıdır.
   * Kullanıcı nesnesi, karşılık gelen Azure AD Bağlayıcısı nesnesine bağlanmalıdır.
   * AD DS bağlayıcı nesnesinden MV 'ya bağlantı, bağlantıda eşitleme kuralına sahip olmalıdır `Microsoft.InfromADUserAccountEnabled.xxx` .

   Çağrı buluttan geldiğinde, eşitleme altyapısı Azure AD bağlayıcı alanı nesnesini aramak için **Cloudtutturucu** özniteliğini kullanır. Daha sonra MV nesnesine geri bağlantıyı izler ve sonra AD DS nesnesine geri bağlantıyı izler. Aynı kullanıcı için birden çok AD DS nesne (çok ormanlı) olabileceğinden, eşitleme altyapısı `Microsoft.InfromADUserAccountEnabled.xxx` doğru olanı seçmek için bağlantıyı kullanır.

1. Kullanıcı hesabı bulduktan sonra, uygun AD DS ormanında parolayı doğrudan sıfırlama girişimi yapılır.
1. Parola ayarlama işlemi başarılı olursa, kullanıcıya parolasını değiştirme bildirilir.

   > [!NOTE]
   > Kullanıcının parola karması, Parola karması eşitlemesi kullanılarak Azure AD 'ye eşitlenirse, şirket içi parola ilkesinin bulut parolası ilkesinden daha zayıf olması ihtimaline sahip olur. Bu durumda, şirket içi ilke zorlanır. Bu ilke, çoklu oturum açma sağlamak için Parola karması eşitlemesini veya Federasyonu kullanıyor olmanız durumunda, şirket içi ilkenizin bulutta zorlanmasını sağlar.

1. Parola ayarlama işlemi başarısız olursa, bir hata kullanıcıdan yeniden denemesini ister. İşlem aşağıdaki nedenlerden dolayı başarısız olabilir:
    * Hizmet kapatıldı.
    * Seçtiğiniz parola, kuruluşun ilkelerini karşılamıyor.
    * Kullanıcı yerel AD DS ortamında bulunamıyor.

   Hata iletileri kullanıcılara yönetici müdahalesi olmadan çözüm deneyebilmeleri için rehberlik sağlar.

## <a name="password-writeback-security"></a>Parola geri yazma güvenliği

Parola geri yazma, yüksek oranda güvenli bir hizmettir. Bilgilerinizin korunduğundan emin olmak için dört katmanlı bir güvenlik modeli aşağıdaki şekilde etkindir:

* **Kiracıya özgü hizmet-Bus geçişi**
   * Hizmeti ayarladığınızda, kiracıya özgü bir Service Bus geçişi, Microsoft 'un hiçbir zaman erişimi olmayan rastgele oluşturulmuş güçlü bir parola ile korunmuş şekilde ayarlanır.
* **Kilitlenmiş, şifreli olarak güçlü, parola şifreleme anahtarı**
   * Service Bus geçişi oluşturulduktan sonra, parolayı kablo üzerine geldiğinde şifrelemek için kullanılan güçlü bir simetrik anahtar oluşturulur. Bu anahtar, yalnızca bulutta bulunan gizli depounuzda bulunur, bu da dizindeki diğer tüm parolalar gibi yoğun bir şekilde kilitlidir ve denetlenir.
* **Endüstri standardı Aktarım Katmanı Güvenliği (TLS)**
   1. Bulutta bir parola sıfırlama veya değiştirme işlemi oluştuğunda, düz metin parolası ortak anahtarınızla şifrelenir.
   1. Şifrelenmiş parola, Service Bus geçişinizdeki Microsoft TLS/SSL sertifikaları kullanılarak şifreli bir kanal üzerinden gönderilen bir HTTPS iletisine yerleştirilir.
   1. İleti Service Bus 'a ulaştıktan sonra, şirket içi aracılarınız uyandırır ve daha önce oluşturulan güçlü parolayı kullanarak Service Bus 'ın kimliğini doğrular.
   1. Şirket içi aracı, şifreli iletiyi alır ve özel anahtarı kullanarak şifresini çözer.
   1. Şirket içi aracı, AD DS SetPassword API 'SI aracılığıyla parolayı ayarlamaya çalışır. Bu adım, bulutta AD DS şirket içi parola ilkenizin (karmaşıklık, yaş, geçmiş ve filtreler gibi) zorlanmasını sağlar.
* **İleti süre sonu ilkeleri**
   * Şirket içi hizmetiniz çalışmadığından ileti Service Bus 'da yer alıyorsa, zaman aşımına uğrar ve birkaç dakika sonra kaldırılır. İletinin zaman aşımı ve kaldırılması, güvenliği daha da artırır.

### <a name="password-writeback-encryption-details"></a>Parola geri yazma şifreleme ayrıntıları

Bir kullanıcı parola sıfırlama gönderdikten sonra, sıfırlama isteği, şirket içi ortamınıza ulaşmadan önce birkaç şifreleme adımı üzerinden geçer. Bu şifreleme adımları en yüksek hizmet güvenilirliğini ve güvenliğini güvence altına aldığından. Bunlar aşağıdaki gibi açıklanmıştır:

1. **2048 BIT RSA anahtarıyla parola şifreleme**: bir Kullanıcı, şirket içine geri yazılacak bir parola gönderdikten sonra, gönderilen parola 2048 bit RSA anahtarıyla şifrelenir.
1. **AES-GCM Ile paket düzeyinde şifreleme**: tüm paket, parola artı gerekli meta VERILER, AES-GCM kullanılarak şifrelenir. Bu şifreleme, temeldeki Service Bus kanalına doğrudan erişimi olan herkesin içerikleri görüntülemesini veya bunlarla müdahale etmesini engeller.
1. **TLS/SSL üzerinden gerçekleştirilen tüm iletişimler**: Service Bus ile olan tüm ILETIŞIMLER bir SSL/TLS kanalında gerçekleşir. Bu şifreleme, yetkisiz üçüncü taraflardan içerik güvenliğini sağlar.
1. **Altı ayda bir otomatik anahtar geçişi**: tüm anahtarlar altı ayda bir veya her bir parola geri yazma özelliği devre dışı bırakılır ve en yüksek hizmet güvenliği ve güvenliğini sağlamak için Azure AD Connect yeniden etkinleştirilir.

### <a name="password-writeback-bandwidth-usage"></a>Parola geri yazma bant genişliği kullanımı

Parola geri yazma, yalnızca aşağıdaki koşullarda istekleri şirket içi aracıya geri gönderen düşük bant genişliğine sahip bir hizmettir:

* Özellik etkinleştirildiğinde veya Azure AD Connect aracılığıyla devre dışı bırakıldığında iki ileti gönderilir.
* Hizmet çalıştığı sürece bir hizmet sinyali olarak her beş dakikada bir ileti gönderilir.
* Her yeni parola gönderildiğinde iki ileti gönderilir:
   * İlk ileti, işlemi gerçekleştirmek için bir istekte bulunur.
   * İkinci ileti işlemin sonucunu içerir ve aşağıdaki koşullarda gönderilir:
      * Kullanıcı self servis parola sıfırlama sırasında yeni bir parola gönderildiğinde her seferinde.
      * Kullanıcı parolası değiştirme işlemi sırasında her yeni parola gönderildiğinde.
      * Yönetici tarafından başlatılan bir kullanıcı parola sıfırlama sırasında (yalnızca Azure Yönetim portallarından) yeni bir parola gönderildiğinde her seferinde.

#### <a name="message-size-and-bandwidth-considerations"></a>İleti boyutu ve bant genişliği konuları

Daha önce açıklanan iletinin her birinin boyutu genellikle 1 KB 'tır. Aşırı yüklerin altında bile parola geri yazma hizmeti, bant genişliğinin saniyede birkaç kilobit tüketiyor. Her ileti gerçek zamanlı olarak gönderildiği ve yalnızca bir parola güncelleştirme işlemi için gerektiğinde ve ileti boyutu çok küçük olduğu için geri yazma özelliğinin bant genişliği kullanımı ölçülebilir bir etkiye sahip olamaz.

## <a name="supported-writeback-operations"></a>Desteklenen geri yazma işlemleri

Parolalar aşağıdaki durumlarda geri yazılır:

* **Desteklenen son kullanıcı işlemleri**
   * Herhangi bir Son Kullanıcı self servis gönüllü parola değiştirme işlemi.
   * Herhangi bir Son Kullanıcı self servis zorla parola değiştirme işlemi, örneğin parola süre sonu.
   * [Parola sıfırlama portalından](https://passwordreset.microsoftonline.com)kaynaklanan herhangi bir Son Kullanıcı self servis parola sıfırlaması.

* **Desteklenen yönetici işlemleri**
   * Tüm yönetici self servis gönüllü parola değiştirme işlemi.
   * Herhangi bir yönetici self servis zorla parola değiştirme işlemi, örneğin parola süre sonu.
   * [Parola sıfırlama portalından](https://passwordreset.microsoftonline.com)kaynaklanan tüm yönetici self servis parola sıfırlaması.
   * Yönetici tarafından başlatılan son kullanıcı parolası [Azure Portal](https://portal.azure.com)sıfırlandı.
   * [MICROSOFT Graph API](/graph/api/passwordauthenticationmethod-resetpassword?tabs=http)'den yönetici tarafından başlatılan son kullanıcı parolası sıfırlandı.

## <a name="unsupported-writeback-operations"></a>Desteklenmeyen geri yazma işlemleri

Parolalar aşağıdaki durumların hiçbirinde geri yazılmadı:

* **Desteklenmeyen Son Kullanıcı işlemleri**
   * PowerShell sürüm 1, sürüm 2 veya Microsoft Graph API 'sini kullanarak kendi parolalarını sıfırlayan herhangi bir son kullanıcı.
* **Desteklenmeyen yönetici işlemleri**
   * PowerShell sürüm 1, sürüm 2 veya Microsoft Graph API 'sinden yönetici tarafından başlatılan son kullanıcı parolası sıfırlanır ( [MICROSOFT Graph API](/graph/api/passwordauthenticationmethod-resetpassword?tabs=http) desteklenir).
   * [Microsoft 365 Yönetim merkezinden](https://admin.microsoft.com)yönetici tarafından başlatılan son kullanıcı parolası sıfırlandı.
   * Herhangi bir yönetici parola geri yazma için kendi parolasını sıfırlamak üzere parola sıfırlama aracını kullanamaz.

> [!WARNING]
> Şirket içi AD DS Active Directory Kullanıcıları ve bilgisayarları gibi yönetim araçları veya Active Directory Yönetim Merkezi Azure AD Connect önizleme özelliği olarak desteklenmek üzere "Kullanıcı bir sonraki oturum açışında parolayı değiştirmeli" onay kutusunun kullanılması. Daha fazla bilgi için bkz. [Azure AD Connect eşitleme ile parola karması eşitlemeyi uygulama](../hybrid/how-to-connect-password-hash-synchronization.md).

## <a name="next-steps"></a>Sonraki adımlar

SSPR geri yazma 'yı kullanmaya başlamak için aşağıdaki öğreticiyi izleyin:

> [!div class="nextstepaction"]
> [Öğretici: Self servis parola sıfırlama (SSPR) geri yazmayı etkinleştirme](./tutorial-enable-sspr-writeback.md)