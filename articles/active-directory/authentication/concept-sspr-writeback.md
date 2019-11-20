---
title: Azure AD SSPR ile şirket içi parola geri yazma tümleştirmesi-Azure Active Directory
description: Şirket içi AD infratstructure 'e geri yazılan bulut parolalarını alın
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 07069d22d57540c6a16472bc7278821e14f1f18e
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68561285"
---
# <a name="what-is-password-writeback"></a>Parola geri yazma nedir?

Bulut tabanlı parola sıfırlama yardımcı programının harika olması, ancak çoğu şirketin kullanıcıların mevcut olduğu şirket içi bir dizini vardır. Microsoft, geleneksel şirket içi Active Directory (AD) bulutta parola değişiklikleriyle eşitlenmiş halde tutmayı nasıl destekler? Parola geri yazma özelliği, buluttaki parola değişikliklerinin gerçek zamanlı olarak mevcut bir şirket içi dizine yazılmasına izin veren [Azure AD Connect](../hybrid/whatis-hybrid-identity.md) etkinleştirilmiş bir özelliktir.

Parola geri yazma, şunu kullanan ortamlarda desteklenir:

* [Active Directory Federasyon Hizmetleri](../hybrid/how-to-connect-fed-management.md)
* [Parola karması eşitleme](../hybrid/how-to-connect-password-hash-synchronization.md)
* [Doğrudan kimlik doğrulama](../hybrid/how-to-connect-pta.md)

> [!WARNING]
> Parola geri yazma işlemi, [Azure Access Control hizmeti (ACS) 7 kasım 2018 tarihinde devre dışı bırakıldığında,](../develop/active-directory-acs-migration.md)Azure AD Connect sürümlerini kullanan müşteriler için çalışmayı durdurur. Azure AD Connect sürümleri, bu işlev için ACS 'ye bağlı olduğundan, 1.0.8641.0 ve üzeri sürümler artık parola geri yazmaya izin vermez.
>
> Hizmette kesintiye uğramamak için Azure AD Connect önceki bir sürümünden daha yeni bir sürüme yükseltme yapın Azure AD Connect makalesine [bakın: Önceki bir sürümden en son sürüme yükseltme](../hybrid/how-to-upgrade-previous-version.md)
>

Parola geri yazma şunları sağlar:

* **Şirket içi Active Directory parola Ilkeleri zorlaması**: Kullanıcı parolasını sıfırladığında, bu dizine işlemeden önce şirket içi Active Directory ilkenizi karşıladığından emin olmak denetlenir. Bu gözden geçirme geçmişi, karmaşıklığı, yaşı, parola filtrelerini ve yerel Active Directory tanımladığınız diğer parola kısıtlamalarını denetlemeyi içerir.
* **Sıfır gecikme geri bildirimi**: Parola geri yazma, zaman uyumlu bir işlemdir. Parolanız ilkeyi karşılamıyorsa veya herhangi bir nedenden dolayı sıfırlanamadı veya değiştirilamadığında kullanıcılarınız anında bilgilendirilir.
* **, Erişim panelinden ve Office 365 ' de parola değişikliklerini destekler**: Federasyon veya parola karması eşitlenen kullanıcılar, zaman aşımına uğradığı veya süre dolmayan parolalarını değiştirmek için bu parolalar, yerel Active Directory ortamınıza geri yazılır.
* **, Bir yönetici tarafından Azure Portal tarafından sıfırlanırsa parola geri yazma özelliğini destekler**: Bir yönetici kullanıcının parolasını [Azure Portal](https://portal.azure.com)her sıfırladığında, bu kullanıcı federe veya parola karması eşitlendiğinde, parola şirket içine geri yazılır. Bu işlevsellik şu anda Office Yönetim Portalı 'nda desteklenmiyor.
* **Tüm gelen güvenlik duvarı kuralları gerektirmez**: Parola geri yazma, temel bir iletişim kanalı olarak Azure Service Bus geçişini kullanır. Tüm iletişimler 443 numaralı bağlantı noktasından dışarıya gönderilir.

> [!NOTE]
> Şirket içi AD içindeki korumalı gruplar içinde bulunan yönetici hesapları parola geri yazma ile birlikte kullanılabilir. Yöneticiler buluttaki parolalarını değiştirebilir, ancak unutulan bir parolayı sıfırlamak için parola sıfırlama kullanamaz. Korumalı gruplar hakkında daha fazla bilgi için, bkz. [Active Directory korunan hesaplar ve gruplar](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/appendix-c--protected-accounts-and-groups-in-active-directory).

## <a name="licensing-requirements-for-password-writeback"></a>Parola geri yazma için lisans gereksinimleri

**Şirket içi geri yazma Ile self servis parola sıfırlama/değiştirme/kilit açma, Azure AD 'nin Premium bir özelliğidir**. Lisanslama hakkında daha fazla bilgi için [Azure Active Directory fiyatlandırma sitesine](https://azure.microsoft.com/pricing/details/active-directory/)bakın.

Parola geri yazma özelliğini kullanmak için kiracınızda aşağıdaki lisanslardan birine atanmış olmanız gerekir:

* Azure AD Premium P1
* Azure AD Premium P2
* Enterprise Mobility + Security E3 veya a3
* Enterprise Mobility + Security E5 veya a5
* Microsoft 365 E3 veya a3
* Microsoft 365 E5 veya a5
* Microsoft 365 F1
* Microsoft 365 İş

> [!WARNING]
> Tek başına Office 365 lisans planları, *"Şirket içi geri yazma Ile self servis parola sıfırlama/değiştirme/kilit açma" desteği vermez* ve bu işlevin çalışması için önceki planlardan birine sahip olmanızı gerektirir.

## <a name="how-password-writeback-works"></a>Parola geri yazma nasıl kullanılır

Bir Federasyon veya parola karması eşitlendiğinde Kullanıcı, bulutta parolalarını sıfırlamayı veya değiştirmeyi denediğinde aşağıdaki eylemler gerçekleşir:

1. Kullanıcının ne tür bir parola olduğunu görmek için bir denetim gerçekleştirilir. Parola şirket içinde yönetiliyorsa:
   * Geri yazma hizmetinin çalışır duruma getirilmediğini görmek için bir denetim gerçekleştirilir. Varsa, Kullanıcı devam edebilir.
   * Geri yazma hizmeti kapalıysa, Kullanıcı parolasının Şu anda sıfırlanıp sıfırlanmama konusunda bilgilendirilir.
1. Ardından, Kullanıcı uygun kimlik doğrulama kapılarını geçirir ve **Parolayı Sıfırla** sayfasına ulaşır.
1. Kullanıcı yeni bir parola seçer ve bunu onaylar.
1. Kullanıcı **Gönder**' i seçtiğinde, düz metin parolası, geri yazma kurulum işlemi sırasında oluşturulan bir simetrik anahtarla şifrelenir.
1. Şifrelenmiş parola, kiracı 'ya özgü Service Bus geçişinizi (geri yazma kurulum işlemi sırasında sizin için ayarlanan) bir HTTPS kanalı üzerinden gönderilen bir yüke dahil edilmiştir. Bu geçiş, yalnızca şirket içi yüklemenizin bildiği rastgele oluşturulmuş bir parolayla korunmaktadır.
1. İleti Service Bus 'a ulaştıktan sonra, parola sıfırlama uç noktası otomatik olarak uyandırır ve bekleyen bir sıfırlama isteğine sahip olduğunu görür.
1. Daha sonra hizmet, bulut bağlayıcısı özniteliğini kullanarak kullanıcıyı arar. Bu aramanın başarılı olması için:

   * Kullanıcı nesnesi Active Directory bağlayıcı alanında bulunmalıdır.
   * Kullanıcı nesnesi, karşılık gelen meta veri deposu (MV) nesnesine bağlanmalıdır.
   * Kullanıcı nesnesi, karşılık gelen Azure Active Directory bağlayıcı nesnesiyle bağlantılı olmalıdır.
   * Active Directory bağlayıcı nesnesinden MV 'ya bağlantı, bağlantıda eşitleme kuralına `Microsoft.InfromADUserAccountEnabled.xxx` sahip olmalıdır.
   
   Çağrı buluttan geldiğinde, eşitleme altyapısı Azure Active Directory bağlayıcı alanı nesnesini aramak için **Cloudtutturucu** özniteliğini kullanır. Daha sonra MV nesnesine geri bağlantıyı izler ve sonra Active Directory nesnesine geri bağlantıyı izler. Aynı kullanıcı için birden çok Active Directory nesne (çok ormanlı) olabileceğinden, eşitleme altyapısı doğru olanı seçmek için `Microsoft.InfromADUserAccountEnabled.xxx` bağlantıyı kullanır.

1. Kullanıcı hesabı bulduktan sonra, uygun Active Directory ormanında parolayı doğrudan sıfırlama girişimi yapılır.
1. Parola ayarlama işlemi başarılı olursa, kullanıcıya parolasını değiştirme bildirilir.
   > [!NOTE]
   > Kullanıcının parola karması, Parola karması eşitlemesi kullanılarak Azure AD 'ye eşitlenirse, şirket içi parola ilkesinin, bulut parola ilkesinden daha zayıf olması olasılığı vardır. Bu durumda, şirket içi ilke zorlanır. Bu ilke, çoklu oturum açma sağlamak için Parola karması eşitlemesini veya Federasyonu kullanıyor olmanız durumunda, şirket içi ilkenizin bulutta zorlanmasını sağlar.

1. Parola ayarlama işlemi başarısız olursa, bir hata kullanıcıdan yeniden denemesini ister. İşlem şu nedenle başarısız olabilir:
    * Hizmet kapatıldı.
    * Seçtiğiniz parola, kuruluşun ilkelerine uymuyor.
    * Kullanıcı yerel Active Directory bulunamıyor.

      Hata iletileri kullanıcılara yönetici müdahalesi olmadan çözüm deneyebilmeleri için rehberlik sağlar.

## <a name="password-writeback-security"></a>Parola geri yazma güvenliği

Parola geri yazma, yüksek oranda güvenli bir hizmettir. Bilgilerinizin korunduğundan emin olmak için, aşağıda açıklandığı gibi dört katmanlı bir güvenlik modeli etkinleştirilir:

* **Kiracıya özgü hizmet-Bus geçişi**
   * Hizmeti ayarladığınızda, kiracıya özgü bir Service Bus geçişi, Microsoft 'un hiçbir zaman erişimi olmayan rastgele oluşturulmuş güçlü bir parola ile korunmuş şekilde ayarlanır.
* **Kilitlenmiş, şifreli olarak güçlü, parola şifreleme anahtarı**
   * Service Bus geçişi oluşturulduktan sonra, parolayı kablo üzerine geldiğinde şifrelemek için kullanılan güçlü bir simetrik anahtar oluşturulur. Bu anahtar, yalnızca bulutta bulunan gizli depounuzda bulunur, bu da dizindeki diğer tüm parolalar gibi yoğun bir şekilde kilitlidir ve denetlenir.
* **Endüstri standardı Aktarım Katmanı Güvenliği (TLS)**
   1. Bulutta bir parola sıfırlama veya değiştirme işlemi oluştuğunda, düz metin parolası ortak anahtarınızla şifrelenir.
   1. Şifrelenmiş parola, Service Bus geçişinizdeki Microsoft SSL sertifikalarını kullanarak, şifreli bir kanal üzerinden gönderilen bir HTTPS iletisine yerleştirilir.
   1. İleti Service Bus 'a ulaştıktan sonra, şirket içi aracılarınız uyandırır ve daha önce oluşturulan güçlü parolayı kullanarak Service Bus 'ın kimliğini doğrular.
   1. Şirket içi aracı, şifreli iletiyi alır ve özel anahtarı kullanarak şifresini çözer.
   1. Şirket içi aracı, AD DS SetPassword API 'SI aracılığıyla parolayı ayarlamaya çalışır. Bu adım, bulutta Active Directory Şirket içi parola ilkenizin (karmaşıklık, yaş, geçmiş ve filtreler gibi) zorlanmasını sağlar.
* **İleti süre sonu ilkeleri**
   * Şirket içi hizmetiniz çalışmadığından ileti Service Bus 'da yer alıyorsa, zaman aşımına uğrar ve birkaç dakika sonra kaldırılır. İletinin zaman aşımı ve kaldırılması, güvenliği daha da artırır.

### <a name="password-writeback-encryption-details"></a>Parola geri yazma şifreleme ayrıntıları

Bir kullanıcı parola sıfırlama gönderdikten sonra, sıfırlama isteği, şirket içi ortamınıza ulaşmadan önce birkaç şifreleme adımı üzerinden geçer. Bu şifreleme adımları en yüksek hizmet güvenilirliğini ve güvenliğini güvence altına aldığından. Bunlar aşağıdaki gibi açıklanmıştır:

* **1. Adım: 2048 bit RSA anahtarıyla**parola şifreleme: Kullanıcı şirket içine geri yazılacak bir parola gönderdikten sonra, gönderilen parola 2048 bitlik bir RSA anahtarıyla şifrelenir.
* **2. Adım: AES-GCM**ile paket düzeyinde şifreleme: Tüm paket, parola artı gerekli meta veriler, AES-GCM kullanılarak şifrelenir. Bu şifreleme, temel ServiceBus kanalına doğrudan erişimi olan herkesin içerikleri görüntülemesini veya bunlarla müdahale etmesini engeller.
* **Adım 3: TLS/SSL**üzerinden tüm iletişimler oluşur: ServiceBus ile iletişimin tamamı bir SSL/TLS kanalında yapılır. Bu şifreleme, yetkisiz üçüncü taraflardan içerik güvenliğini sağlar.
* **Her altı ayda bir otomatik anahtar al**: Her altı ayda bir veya parola geri yazma işlemi devre dışı bırakıldığında tüm anahtarlar geçer ve en yüksek hizmet güvenliği ve güvenliğini sağlamak için Azure AD Connect yeniden etkinleştirilir.

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

Daha önce açıklanan iletinin her birinin boyutu genellikle 1 KB 'tır. Aşırı yüklerin altında bile parola geri yazma hizmeti, bant genişliğinin saniyede birkaç kilobit tüketiyor. Her ileti gerçek zamanlı olarak gönderdiğinden parola güncelleştirme işlemi tarafından yalnızca gerekli olduğunda ve ileti boyutu kadar küçük olduğu için geri yazma özelliği bant genişliği kullanımını ölçülebilir bir etkisi için çok küçük.

## <a name="supported-writeback-operations"></a>Desteklenen geri yazma işlemleri

Parolalar aşağıdaki durumlarda geri yazılır:

* **Desteklenen son kullanıcı işlemleri**
   * Herhangi bir Son Kullanıcı self servis gönüllü parola değiştirme işlemi
   * Herhangi bir Son Kullanıcı self servis zorla parola değiştirme işlemi, örneğin parola süre sonu
   * [Parola sıfırlama portalından](https://passwordreset.microsoftonline.com) kaynaklanan herhangi bir Son Kullanıcı self servis parola sıfırlaması
* **Desteklenen yönetici işlemleri**
   * Tüm yönetici self servis gönüllü parola değiştirme işlemi
   * Herhangi bir yönetici self servis zorla parola değiştirme işlemi, örneğin parola süre sonu
   * [Parola sıfırlama portalından](https://passwordreset.microsoftonline.com) kaynaklanan tüm yönetici self servis parola sıfırlaması
   * Yönetici tarafından başlatılan son kullanıcı parolası [Azure Portal](https://portal.azure.com) sıfırlandı

## <a name="unsupported-writeback-operations"></a>Desteklenmeyen geri yazma işlemleri

Parolalar aşağıdaki durumlardan herhangi birine *geri yazılmaz:*

* **Desteklenmeyen Son Kullanıcı işlemleri**
   * PowerShell sürüm 1, sürüm 2 veya Azure AD Graph API kullanarak kendi parolalarını sıfırlayan herhangi bir Son Kullanıcı
* **Desteklenmeyen yönetici işlemleri**
   * Yönetici tarafından başlatılan son kullanıcı parolası PowerShell sürüm 1, sürüm 2 veya Azure AD Graph API sıfırladı
   * [Microsoft 365 Yönetim merkezinden](https://admin.microsoft.com) yönetici tarafından başlatılan son kullanıcı parolası sıfırlama

> [!WARNING]
> Şirket içi Active Directory Active Directory Kullanıcıları ve bilgisayarları gibi yönetim araçları veya Active Directory Yönetim Merkezi desteklenmediğinden, "kullanıcının bir sonraki oturum açışında parolayı değiştirmesi gerekir" onay kutusunun kullanılması desteklenmez. Şirket içi bir parolayı değiştirirken bu seçeneği kontrol etmez.

## <a name="next-steps"></a>Sonraki adımlar

Öğreticiyi kullanarak parola geri yazmayı etkinleştirin: [Parola geri yazma etkinleştiriliyor](tutorial-enable-writeback.md)
