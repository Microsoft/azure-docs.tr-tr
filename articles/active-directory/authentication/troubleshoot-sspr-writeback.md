---
title: Self servis parola sıfırlama geri yazma sorunlarını giderme-Azure Active Directory
description: Azure Active Directory içinde self servis parola sıfırlama geri yazma için sık karşılaşılan sorunları ve çözüm adımlarını nasıl giderebileceğinizi öğrenin
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 08/26/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0620304de1866d24719b137836419502cd25bee9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98682246"
---
# <a name="troubleshoot-self-service-password-reset-writeback-in-azure-active-directory"></a>Azure Active Directory içinde self servis parola sıfırlama geri yazma sorunlarını giderme

Azure Active Directory (Azure AD) self servis parola sıfırlama (SSPR), kullanıcıların buluttaki parolalarını sıfırlamasına olanak sağlar. Parola geri yazma özelliği, buluttaki parola değişikliklerinin gerçek zamanlı olarak mevcut bir şirket içi dizine yazılmasına izin veren [Azure AD Connect](../hybrid/whatis-hybrid-identity.md) etkinleştirilmiş bir özelliktir.

SSPR geri yazma ile ilgili sorunlar yaşıyorsanız, aşağıdaki sorun giderme adımları ve genel hatalar yardımcı olabilir. Sorununuz için yanıt bulamazsanız, destek ekiplerimiz size daha fazla yardımcı olmak için [her zaman kullanılabilir](#contact-microsoft-support) .

## <a name="troubleshoot-connectivity"></a>Bağlantı sorunlarını giderme

Azure AD Connect için parola geri yazma ile ilgili sorunlar yaşıyorsanız, sorunu çözmeye yardımcı olabilecek aşağıdaki adımları gözden geçirin. Hizmetinizi kurtarmak için şu adımları sırasıyla izlemeniz önerilir:

* [Ağ bağlantısını onayla](#confirm-network-connectivity)
* [Azure AD Connect eşitleme hizmetini yeniden başlatın](#restart-the-azure-ad-connect-sync-service)
* [Parola geri yazma özelliğini devre dışı bırakma ve yeniden etkinleştirme](#disable-and-re-enable-the-password-writeback-feature)
* [En son Azure AD Connect sürümü yüklensin](#install-the-latest-azure-ad-connect-release)
* [Parola geri yazma sorunlarını giderme](#common-password-writeback-errors)

### <a name="confirm-network-connectivity"></a>Ağ bağlantısını onayla

En yaygın hata noktası, güvenlik duvarı veya ara sunucu bağlantı noktalarının veya boşta kalma zaman aşımlarının yanlış yapılandırılmalarından oluşur.

Azure AD Connect Version *1.1.443.0* ve üzeri için, *giden https* erişimi aşağıdaki adreslere gereklidir:

* *\*. passwordreset.microsoftonline.com*
* *\*. servicebus.windows.net*

Azure [gov uç noktaları](../../azure-government/compare-azure-government-global-azure.md#guidance-for-developers):

* *\*. passwordreset.microsoftonline.us*
* *\*. servicebus.usgovcloudapi.net*

Daha fazla ayrıntı düzeyi gerekiyorsa [Microsoft Azure veri MERKEZI IP aralıkları listesine](https://www.microsoft.com/download/details.aspx?id=41653)bakın. Bu liste her Çarşamba güncellenir ve sonraki Pazartesi devreye girer.

Daha fazla bilgi için bkz. [Azure AD Connect için bağlantı önkoşulları](../hybrid/how-to-connect-install-prerequisites.md).

### <a name="restart-the-azure-ad-connect-sync-service"></a>Azure AD Connect eşitleme hizmetini yeniden başlatın

Bağlantı sorunlarını veya hizmetle ilgili diğer geçici sorunları çözümlemek için Azure AD Connect eşitleme hizmetini yeniden başlatmak üzere aşağıdaki adımları izleyin:

1. Azure AD Connect çalıştıran sunucuda yönetici olarak **Başlat**' ı seçin.
1. Arama alanına *Services. msc* yazın ve **ENTER**' u seçin.
1. *Microsoft Azure AD eşitleme* girişi ' ni arayın.
1. Hizmet girişine sağ tıklayın, **Yeniden Başlat**' ı seçin ve işlemin bitmesini bekleyin.

    :::image type="content" source="./media/troubleshoot-sspr-writeback/service-restart.png" alt-text="GUI 'yi kullanarak Azure AD Eşitleme hizmetini yeniden başlatın" border="false":::

Bu adımlar, Azure AD ile bağlantınızı yeniden kurup bağlantı sorunlarınızı çözmelidir.

Azure AD Connect eşitleme hizmetini yeniden başlatmak sorununuzu gidermezse, sonraki bölümde Parola geri yazma özelliğini devre dışı bırakıp yeniden etkinleştirmeyi deneyin.

### <a name="disable-and-re-enable-the-password-writeback-feature"></a>Parola geri yazma özelliğini devre dışı bırakma ve yeniden etkinleştirme

Sorunları gidermeye devam etmek için aşağıdaki adımları tamamlayarak parola geri yazma özelliğini devre dışı bırakıp yeniden etkinleştirin:

1. Azure AD Connect çalıştıran sunucuda yönetici olarak, **Azure AD Connect Yapılandırma Sihirbazı**' nı açın.
1. **Azure AD 'ye Bağlan**' da Azure AD Genel yönetici kimlik bilgilerinizi girin.
1. **AD DS Bağlan**' da şirket içi Active Directory Domain Services Yönetici kimlik bilgilerinizi girin.
1. **Kullanıcılarınızı benzersiz olarak tanımlamak** için **İleri** düğmesini seçin.
1. **Isteğe bağlı özellikler**' de **parola geri yazma** onay kutusunu temizleyin.
1. **Yapılandırmaya hazırlanma** sayfasına ulaşana kadar herhangi bir şeyi değiştirmeden kalan Iletişim sayfalarında **İleri ' yi** seçin.
1. **Yapılandırmaya hazırlanma sayfasında** *parola geri yazma* seçeneğinin *devre dışı* olarak gösterildiği denetlenir. Değişikliklerinizi uygulamak için yeşil **Yapılandır** düğmesini seçin.
1. **Tamamlandı** **seçeneğini temizleyin ve ardından** Sihirbazı kapatmak için **son** ' u seçin.
1. **Azure AD Connect Yapılandırma Sihirbazı**'nı yeniden açın.
1. Hizmeti yeniden etkinleştirmek için, **Isteğe bağlı özellikler** sayfasında *parola geri yazma* seçeneğini belirleyerek bu kez 2-8 arasındaki adımları yineleyin.

Bu adımlar, Azure AD ile bağlantınızı yeniden kurup bağlantı sorunlarınızı çözmelidir.

Parola geri yazma özelliğinin devre dışı bırakılması ve yeniden etkinleştirilmesi sorunu çözmezse, sonraki bölümde Azure AD Connect yeniden yükleyin.

### <a name="install-the-latest-azure-ad-connect-release"></a>En son Azure AD Connect sürümü yüklensin

Azure AD Connect yeniden yüklemek, Azure AD ile yerel Active Directory Domain Services ortamınız arasındaki yapılandırma ve bağlantı sorunlarını çözebilir. Bu adımı yalnızca, bağlantıyı doğrulamaya ve sorun gidermeye yönelik önceki adımlara kalkışdıktan sonra gerçekleştirmenizi öneririz.

> [!WARNING]
> Kullanıma hazır eşitleme kurallarını özelleştirdiyseniz, *yükseltmeye devam etmeden önce bunları yedekleyin ve işlemi tamamladıktan sonra el ile yeniden dağıtın.*

1. En son Azure AD Connect sürümünü [Microsoft Indirme merkezi](https://go.microsoft.com/fwlink/?LinkId=615771)' nden indirin.
1. Azure AD Connect zaten yüklemişseniz, Azure AD Connect yüklemenizi en son sürüme güncelleştirmek için yerinde yükseltme gerçekleştirin.

    İndirilen paketi çalıştırın ve Azure AD Connect güncelleştirmek için ekrandaki yönergeleri izleyin.

Bu adımlar, Azure AD ile bağlantınızı yeniden kurup bağlantı sorunlarınızı çözmelidir.

Azure AD Connect sunucusunun en son sürümünü yüklemek sorununuzu gidermezse, en son sürümü yükledikten sonra son bir adım olarak parola geri yazma özelliğini devre dışı bırakıp yeniden etkinleştirmeyi deneyin.

## <a name="verify-that-azure-ad-connect-has-the-required-permissions"></a>Azure AD Connect gerekli izinlere sahip olduğunu doğrulayın

Azure AD Connect parola geri yazma işlemini gerçekleştirmek için **parola sıfırlama** izninin AD DS gerekir. Azure AD Connect belirli bir şirket içi AD DS kullanıcı hesabı için gerekli izinlere sahip olup olmadığını denetlemek için **Windows etkin izin** özelliğini kullanın:

1. Azure AD Connect sunucusunda oturum açın ve    >  **eşitleme hizmetini** Başlat ' a tıklayarak Synchronization Service Manager başlatın.
1. **Bağlayıcılar** sekmesinde şirket içi **Active Directory Domain Services** Bağlayıcısı ' nı seçin ve ardından **Özellikler**' i seçin.

    :::image type="content" source="./media/troubleshoot-sspr-writeback/synchronization-service-manager.png" alt-text="Özelliklerin nasıl düzenleneceğini gösteren Synchronization Service Manager" border="false":::
  
1. Açılır pencerede **Active Directory ormana Bağlan** ' ı seçin ve **Kullanıcı adı** özelliğini unutmayın. Bu özellik, dizin eşitlemesi gerçekleştirmek için Azure AD Connect tarafından kullanılan AD DS hesabıdır.

    Parola geri yazma işlemini gerçekleştirmek için Azure AD Connect AD DS hesabının parola sıfırlama iznine sahip olması gerekir. Aşağıdaki adımlarda bu kullanıcı hesabındaki izinleri kontrol edersiniz.

    :::image type="content" source="./media/troubleshoot-sspr-writeback/synchronization-service-manager-properties.png" alt-text="Eşitleme hizmeti Active Directory Kullanıcı hesabı bulma" border="false":::
  
1. Şirket içi etki alanı denetleyicisinde oturum açın ve **Active Directory Kullanıcıları ve bilgisayarları** uygulamasını başlatın.
1. **Görünüm** ' ü seçin ve **Gelişmiş Özellikler** seçeneğinin etkinleştirildiğinden emin olun.  

    :::image type="content" source="./media/troubleshoot-sspr-writeback/view-advanced-features.png" alt-text="Active Directory Kullanıcıları ve bilgisayarları gelişmiş özellikleri gösterir" border="false":::
  
1. Doğrulamak istediğiniz AD DS kullanıcı hesabını bulun. Hesap adına sağ tıklayın ve **Özellikler**' i seçin.  
1. Açılır pencerede **güvenlik** sekmesine gidin ve **Gelişmiş**' i seçin.  
1. **Yönetici Için gelişmiş güvenlik ayarları** açılır penceresinde, **etkin erişim** sekmesine gidin.
1. **Kullanıcı Seç**' i seçin, Azure AD Connect tarafından kullanılan AD DS hesabını seçin ve ardından **etkin erişimi görüntüle**' yi seçin.

    :::image type="content" source="./media/troubleshoot-sspr-writeback/view-effective-access.png" alt-text="Eşitleme hesabını gösteren geçerli erişim sekmesi" border="false":::
  
1. Aşağı kaydırın ve **sıfırlama parolasını** bulun. Girişte bir onay işareti varsa, AD DS hesabının seçili Active Directory Kullanıcı hesabının parolasını sıfırlama izni vardır.  

    :::image type="content" source="./media/troubleshoot-sspr-writeback/check-permissions.png" alt-text="Eşitleme hesabının parola sıfırlama iznine sahip olduğu doğrulanıyor" border="false":::

## <a name="common-password-writeback-errors"></a>Ortak parola geri yazma hataları

Parola geri yazma ile ilgili olarak aşağıdaki daha belirgin sorunlar meydana gelebilir. Bu hatalardan birine sahipseniz önerilen çözümü gözden geçirin ve parola geri yazma özelliğinin doğru şekilde çalışıp çalışmadığını denetleyin.

| Hata | Çözüm |
| --- | --- |
| Parola sıfırlama hizmeti şirket içinde başlamıyor. Hata 6800 Azure AD Connect makinenin uygulama olay günlüğünde görüntülenir. <br> <br> Ekleme, Federasyon, geçişli kimlik doğrulama veya parola karması ile eşitlenen kullanıcılar parolalarını sıfırlayamaz. | Parola geri yazma etkinleştirildiğinde, eşitleme altyapısı, bulut ekleme hizmetine iletişim kurarak yapılandırmayı (ekleme) gerçekleştirmek için geri yazma kitaplığını çağırır. Parola geri yazma sırasında veya Windows Communication Foundation (WCF) uç noktası başlatılırken karşılaşılan hatalar, Azure AD Connect makinenizde olay günlüğündeki hatalara neden olur. <br> <br> Azure AD Eşitleme (ADSync) hizmetinin yeniden başlatılması sırasında, geri yazma yapılandırıldıysa, WCF uç noktası başlatılır. Ancak, uç noktanın başlatılması başarısız olursa, olay 6800 ' i günlüğe kaydeder ve eşitleme hizmetinin başlamasını sağlar. Bu olayın varlığı, parola geri yazma uç noktasının başlamamasıdır. Bu olay 6800 için olay günlüğü ayrıntıları, PasswordResetService bileşeni tarafından oluşturulacak olay günlüğü girişleriyle birlikte, uç noktayı neden başlatamadığını gösterir. Bu olay günlüğü hatalarını gözden geçirin ve parola geri yazma hala çalışmıyorsa Azure AD Connect yeniden başlatmayı deneyin. Sorun devam ederse, parola geri yazma özelliğini devre dışı bırakıp yeniden etkinleştirmeyi deneyin.
| Kullanıcı parolayı sıfırlamayı veya parola geri yazma etkinken bir hesabın kilidini açmaya çalıştığında, işlem başarısız olur. <br> <br> Ayrıca, şunu içeren Azure AD Connect olay günlüğünde bir olay görürsünüz: "eşitleme altyapısı bir hata döndürdü HR = 800700CE, ileti = dosya adı veya uzantı çok uzun", kilit açma işlemi oluştuktan sonra. | Azure AD Connect için Active Directory hesabı bulun ve parolayı en fazla 256 karakter içerecek şekilde sıfırlayın. Sonra, **Başlat** menüsünden **eşitleme hizmetini** açın. **Bağlayıcılar** ' a gidin ve **Active Directory bağlayıcısını** bulun. Seçin ve ardından **Özellikler**' i seçin. **Kimlik bilgileri** sayfasına gidin ve yeni parolayı girin. Sayfayı kapatmak için **Tamam ' ı** seçin. |
| Azure AD Connect yükleme işleminin son adımında, parola geri yazma özelliğinin yapılandırılamadığını belirten bir hata görürsünüz. <br> <br> Azure AD Connect uygulama olay günlüğü, "Auth Token alma hatası" metnini içeren 32009 hatasını içerir. | Bu hata aşağıdaki iki durumda oluşur: <br><ul><li>Azure AD Connect yükleme işleminin başlangıcında sağlanmış olan genel yönetici hesabı için yanlış bir parola belirttiniz.</li><li>Azure AD Connect yükleme işleminin başlangıcında belirtilen genel yönetici hesabı için bir Federasyon kullanıcısı kullanmaya çalıştınız.</li></ul> Bu sorunu gidermek için, yükleme işleminin başlangıcında belirttiğiniz genel yönetici için bir Federasyon hesabı kullanmadığınız ve belirtilen parolanın doğru olduğundan emin olun. |
| Azure AD Connect makine olay günlüğü, PasswordResetService çalıştırılarak oluşturulan 32002 hatasını içerir. <br> <br> Hata şunu okur: "ServiceBus 'a bağlanılırken hata oluştu. Belirteç sağlayıcısı bir güvenlik belirteci sağlayamadı. " | Şirket içi ortamınız, Bulutta Azure Service Bus uç noktasına bağlanamaz. Bu hata normalde, belirli bir bağlantı noktasına veya web adresine giden bağlantıyı engelleyen bir güvenlik duvarı kuralı nedeniyle oluşur. Daha fazla bilgi için bkz. [bağlantı önkoşulları](../hybrid/how-to-connect-install-prerequisites.md) . Bu kuralları güncelleştirdikten sonra Azure AD Connect sunucusunu yeniden başlatın ve parola geri yazma yeniden çalışmaya başlaması gerekir. |
| Bir süredir çalıştıktan sonra, Federasyon, geçişli kimlik doğrulaması veya parola karması ile eşitlenen kullanıcılar parolalarını sıfırlayamaz. | Bazı ender durumlarda, parola geri yazma hizmeti Azure AD Connect yeniden başlatıldığında yeniden başlatılamaz. Bu durumlarda, ilk olarak parola geri yazma özelliğinin şirket içinde etkin olup olmadığını denetleyin. Azure AD Connect Sihirbazı 'nı veya PowerShell 'i kullanarak kontrol edebilirsiniz. Özellik etkin görünüyorsa, özelliği yeniden etkinleştirmeyi veya devre dışı bırakmayı deneyin. Bu sorun giderme adımı işe yaramazsa, Azure AD Connect bir kaldırma ve yeniden yükleme işlemini deneyin. |
| Federal, geçişli kimlik doğrulaması veya parola karması eşitlenmiş kullanıcılar parolalarını sıfırlamaya çalışan bir hata görür. Hata, bir hizmet sorunu olduğunu gösterir. <br ><br> Bu soruna ek olarak, parola sıfırlama işlemleri sırasında, yönetim aracısının şirket içi olay günlüklerinizin erişimini reddettiğini belirten bir hata görebilirsiniz. | Olay günlüğünde bu hataları görürseniz, yapılandırma sırasında sihirbazda belirtilen Active Directory Yönetim Aracısı (ADMA) hesabının parola geri yazma için gerekli izinlere sahip olduğunu doğrulayın. <br> <br> Bu izin verildiğinde, izinler `sdprop` etki alanı denetleyicisindeki (DC) arka plan görevi aracılığıyla aşağı doğru bir saat kadar sürebilir. <br> <br> Parola sıfırlamanın çalışması için, bu iznin, parolası sıfırlanan Kullanıcı nesnesinin güvenlik tanımlayıcısına damgalı olması gerekir. Bu izin Kullanıcı nesnesinde gösterilene kadar, parola sıfırlama, erişim reddedildi iletisiyle başarısız olmaya devam eder. |
| Federal, geçişli kimlik doğrulaması veya parolalarını sıfırlamaya çalışan Parola karması ile eşitlenen kullanıcılar, parolasını gönderdikten sonra bir hata oluştu. Hata, bir hizmet sorunu olduğunu gösterir. <br> <br> Bu soruna ek olarak, parola sıfırlama işlemleri sırasında, bir "nesne bulunamadı" hatasını belirten Azure AD Connect hizmetinden Olay günlüklerinizi bir hata görebilirsiniz. | Bu hata genellikle eşitleme altyapısının Azure AD bağlayıcı alanında Kullanıcı nesnesini veya bağlantılı meta veri deposu (MV) ya da Azure AD bağlayıcı alanı nesnesini bulamadığını gösterir. <br> <br> Bu sorunu gidermek için, kullanıcının geçerli Azure AD Connect örneği aracılığıyla Şirket içinden Azure AD 'ye eşitlendiğinden emin olun ve bağlayıcı uzayları ve MV içindeki nesnelerin durumunu inceleyin. Active Directory Sertifika Hizmetleri (AD CS) nesnesinin, "Microsoft.InfromADUserAccountEnabled.xxx" kuralı aracılığıyla MV nesnesine bağlı olduğunu onaylayın.|
| Federal, geçişli kimlik doğrulaması veya parola karması ile eşitlenmiş kullanıcılar parolalarını gönderdikten sonra bir hata görür. Hata, bir hizmet sorunu olduğunu gösterir. <br> <br> Bu soruna ek olarak, parola sıfırlama işlemleri sırasında, Azure AD Connect hizmetinden bir "çoklu eşleşme bulundu" hatası olduğunu belirten Olay günlüklerinizi bir hata görebilirsiniz. | Bu, eşitleme altyapısının, "Microsoft.InfromADUserAccountEnabled.xxx" aracılığıyla birden fazla AD CS nesnesine bağlandığını algıladığını belirtir. Bu, kullanıcının birden fazla ormanda etkin bir hesabı olduğu anlamına gelir. Bu senaryo parola geri yazma için desteklenmez. |
| Parola işlemleri bir yapılandırma hatasıyla başarısız oluyor. Uygulama olay günlüğü, "0x8023061f (Bu yönetim aracısında parola eşitleme etkin olmadığından işlem başarısız oldu)" metniyle birlikte Azure AD Connect hatası 6329 içeriyor. | Parola geri yazma özelliği zaten etkinleştirildikten sonra, Azure AD Connect yapılandırması yeni bir Active Directory ormanı eklemek (veya mevcut bir ormanı kaldırmak ve yeniden eklemek) için değiştirilirse bu hata oluşur. Bu son eklenen ormanlardaki kullanıcılar için parola işlemleri başarısız olur. Sorunu gidermek için, orman yapılandırma değişiklikleri tamamlandıktan sonra parola geri yazma özelliğini devre dışı bırakıp yeniden etkinleştirin. |

## <a name="password-writeback-event-log-error-codes"></a>Parola geri yazma olay günlüğü hata kodları

Parola geri yazma ile ilgili sorunları giderirken en iyi yöntem, Azure AD Connect makinenizde uygulama olay günlüğü 'nü incelemenizi kullanmaktır. Bu olay günlüğü, parola geri yazma için iki kaynaktan olayları içerir. *Passwordresetservice* kaynağı, parola geri yazma işlemiyle ilgili işlemleri ve sorunları açıklar. *ADSync* kaynağı, Active Directory Domain Services ortamınızda parola ayarlamayla ilgili işlemleri ve sorunları açıklar.

### <a name="if-the-source-of-the-event-is-adsync"></a>Olayın kaynağı ADSync ise

| Kod | Ad veya ileti | Description |
| --- | --- | --- |
| 6329 | BAıL: MMS (4924) 0x80230619: "bir kısıtlama, parolanın belirtilen geçerli bir şekilde değiştirilmesini engelliyor." | Bu olay, parola geri yazma hizmeti yerel dizininizde, etki alanının parola yaşı, geçmişi, karmaşıklık veya filtreleme gereksinimlerini karşılamayan bir parola ayarlamaya çalıştığında oluşur. <br> <br> En az bir parola yaşı varsa ve kısa süre önce bu zaman içindeki parolayı değiştirdiyseniz, etki alanındaki belirli bir yaşa ulaşıncaya kadar parolayı tekrar değiştiremezsiniz. Sınama amacıyla, minimum yaş 0 olarak ayarlanmalıdır. <br> <br> Parola geçmişi gereksinimleriniz etkinse, son *n* kez kullanılmamış bir parola seçmeniz gerekir, burada *n* parola geçmişi ayarıdır. Son *N* kez kullanılan bir parola seçerseniz, bu durumda bir hata görürsünüz. Sınama amacıyla parola geçmişi 0 olarak ayarlanmalıdır. <br> <br> Parola karmaşıklığı gereksinimleriniz varsa, Kullanıcı bir parolayı değiştirmeye veya sıfırlamaya çalıştığında bunların hepsi zorlanır. <br> <br> Parola Filtreleriniz etkinse ve Kullanıcı filtreleme ölçütlerine uymayan bir parola seçerse, sıfırlama veya değiştirme işlemi başarısız olur. |
| 6329 | MMS (3040): admaexport. cpp (2837): sunucuda LDAP parola ilkesi denetimi yoktur. | DC 'lerde LDAP_SERVER_POLICY_HINTS_OID Control (1.2.840.113556.1.4.2066) etkinleştirilmemişse bu sorun oluşur. Parola geri yazma özelliğini kullanmak için, denetimi etkinleştirmeniz gerekir. Bunu yapmak için, DC 'Ler Windows Server 2008R2 veya üzeri sürümlerde olmalıdır. |
| HR 8023042 | Eşitleme altyapısı bir hata döndürdü HR = 80230402, ileti = aynı bağlayıcı ile yinelenen girdiler bulunduğundan bir nesne alınamadı. | Birden çok etki alanında aynı kullanıcı KIMLIĞI etkinleştirildiğinde bu hata oluşur. Bu örnek, hesap ve kaynak ormanlarını eşitliyoruz ve aynı kullanıcı KIMLIĞININ her ormanda mevcut ve etkinleştirilmiş olduğunu bir örnektir. <br> <br> Bu hata, diğer ad veya UPN gibi benzersiz olmayan bir tutturucu özniteliği kullanırsanız ve iki kullanıcı aynı bağlayıcı özniteliğini paylaşıyorsa da oluşabilir. <br> <br> Bu sorunu çözmek için, etki alanlarınızda yinelenen bir kullanıcınız olmadığından ve her kullanıcı için benzersiz bir tutturucu özniteliği kullandığınızdan emin olun. |

### <a name="if-the-source-of-the-event-is-passwordresetservice"></a>Olayın kaynağı PasswordResetService ise

| Kod | Ad veya ileti | Description |
| --- | --- | --- |
| 31001 | PasswordResetStart | Bu olay, şirket içi hizmetin, buluttan kaynaklanan bir Federasyon, geçişli kimlik doğrulama veya parola karması ile eşitlenen Kullanıcı için bir parola sıfırlama isteği algıladığını gösterir. Bu olay, her parola sıfırlama geri yazma işlemindeki ilk olaydır. |
| 31002 | PasswordResetSuccess | Bu olay, bir kullanıcının parola sıfırlama işlemi sırasında yeni bir parola seçtiği anlamına gelir. Bu parolanın, kurumsal parola gereksinimlerini karşıladığını belirledik. Parola, yerel Active Directory ortamına başarıyla geri yazıldı. |
| 31003 | PasswordResetFail | Bu olay, bir kullanıcının bir parolayı seçtiği ve parolanın şirket içi ortama başarıyla ulaştığını gösterir. Ancak, yerel Active Directory ortamında parolayı ayarlamaya çalışırken bir hata oluştu. Bu hata çeşitli nedenlerden kaynaklanabilir: <br><ul><li>Kullanıcının parolası, etki alanı için yaş, geçmiş, karmaşıklık veya filtre gereksinimlerini karşılamıyor. Bu sorunu çözmek için yeni bir parola oluşturun.</li><li>ADMA hizmeti hesabı, söz konusu Kullanıcı hesabında yeni parolayı ayarlamak için uygun izinlere sahip değil.</li><li>Kullanıcının hesabı, parola ayarlama işlemlerine izin vermeyen etki alanı veya kuruluş Yöneticisi grubu gibi korumalı bir grupta bulunur.</li></ul>|
| 31004 | OnboardingEventStart | Bu olay, Azure AD Connect ile parola geri yazma özelliğini etkinleştirdiğinizde meydana gelir ve kuruluşunuzu parola geri yazma Web hizmetine eklemeyi başladık. |
| 31005 | OnboardingEventSuccess | Bu olay, ekleme işleminin başarılı olduğunu ve parola geri yazma özelliğinin kullanıma hazırlandığını gösterir. |
| 31006 | ChangePasswordStart | Bu olay, şirket içi hizmetin, buluttan kaynaklanan bir Federasyon, geçişli kimlik doğrulama veya parola karması ile eşitlenen Kullanıcı için bir parola değişiklik isteği algıladığını gösterir. Bu olay, her parola değiştirme geri yazma işlemindeki ilk olaydır. |
| 31007 | ChangePasswordSuccess | Bu olay, bir kullanıcının parola değiştirme işlemi sırasında yeni bir parola seçtiği anlamına gelir; parolanın kurumsal parola gereksinimlerini karşıladığını ve parolanın yerel Active Directory ortamına başarıyla geri yazıldığını belirledik. |
| 31008 | ChangePasswordFail | Bu olay, bir kullanıcının bir parolayı seçtiği ve parolanın şirket içi ortama başarıyla ulaştığını gösterir, ancak yerel Active Directory ortamında parolayı ayarlamaya çalıştık bir hata oluştu. Bu hata çeşitli nedenlerden kaynaklanabilir: <br><ul><li>Kullanıcının parolası, etki alanı için yaş, geçmiş, karmaşıklık veya filtre gereksinimlerini karşılamıyor. Bu sorunu çözmek için yeni bir parola oluşturun.</li><li>ADMA hizmeti hesabı, söz konusu Kullanıcı hesabında yeni parolayı ayarlamak için uygun izinlere sahip değil.</li><li>Kullanıcının hesabı, parola ayarlama işlemlerine izin vermeyen etki alanı veya kuruluş yöneticileri gibi korumalı bir grupta bulunur.</li></ul> |
| 31009 | Resetuserpasswordbyadmınstart | Şirket içi hizmet, bir kullanıcı adına yöneticiden kaynaklanan bir Federasyon, geçişli kimlik doğrulama veya parola karması ile eşitlenen Kullanıcı için parola sıfırlama isteği algıladı. Bu olay, bir yönetici tarafından başlatılan her parola sıfırlama geri yazma işlemindeki ilk olaydır. |
| 31010 | ResetUserPasswordByAdminSuccess | Yönetici, bir yönetici tarafından başlatılan parola sıfırlama işlemi sırasında yeni bir parola seçti. Bu parolanın, kurumsal parola gereksinimlerini karşıladığını belirledik. Parola, yerel Active Directory ortamına başarıyla geri yazıldı. |
| 31011 | ResetUserPasswordByAdminFail | Yönetici Kullanıcı adına bir parola seçti. Parola, şirket içi ortama başarıyla ulaştı. Ancak, yerel Active Directory ortamında parolayı ayarlamaya çalışırken bir hata oluştu. Bu hata çeşitli nedenlerden kaynaklanabilir: <br><ul><li>Kullanıcının parolası, etki alanı için yaş, geçmiş, karmaşıklık veya filtre gereksinimlerini karşılamıyor. Bu sorunu çözmek için yeni bir parola deneyin.</li><li>ADMA hizmeti hesabı, söz konusu Kullanıcı hesabında yeni parolayı ayarlamak için uygun izinlere sahip değil.</li><li>Kullanıcının hesabı, parola ayarlama işlemlerine izin vermeyen etki alanı veya kuruluş yöneticileri gibi korumalı bir grupta bulunur.</li></ul>  |
| 31012 | OffboardingEventStart | Bu olay, Azure AD Connect ile parola geri yazma özelliğini devre dışı bıraktığınızda oluşur ve kuruluşunuzu parola geri yazma Web hizmetine boşaltdığımızda olduğunu gösterir. |
| 31013| OffboardingEventSuccess| Bu olay, çıkarma işleminin başarılı olduğunu ve parola geri yazma özelliğinin başarıyla devre dışı bırakıldığını gösterir. |
| 31014| OffboardingEventFail| Bu olay, çıkarma işleminin başarılı olmadığını gösterir. Bunun nedeni, yapılandırma sırasında belirtilen bulutta veya şirket içi yönetici hesabında bir izin hatası olabilir. Bu hata, parola geri yazma özelliğini devre dışı bırakırken bir Federasyon bulutu genel Yöneticisi kullanmaya çalışıyorsanız da oluşabilir. Bu sorunu gidermek için, yönetim izinlerinizi denetleyin ve parola geri yazma özelliğini yapılandırırken bir Federasyon hesabı kullanmadığınız emin olun.|
| 31015| WriteBackServiceStarted| Bu olay parola geri yazma hizmetinin başarıyla başlatıldığını gösterir. Buluttan parola yönetim isteklerini kabul etmeye hazırlanıyor.|
| 31016| Writebackservicestomış| Bu olay parola geri yazma hizmetinin durdurulduğunu gösterir. Buluttan gelen parola yönetim istekleri başarılı olmayacaktır.|
| 31017| AuthTokenSuccess| Bu olay, oluşturma veya ekleme işlemini başlatmak üzere Azure AD Connect kurulumu sırasında belirtilen genel yönetici için bir yetkilendirme belirtecini başarıyla aldığını gösterir.|
| 31018| KeyPairCreationSuccess| Bu olay parola şifreleme anahtarını başarıyla oluşturduğumuz anlamına gelir. Bu anahtar, şirket içi ortamınıza gönderilmek üzere buluttan parolaları şifrelemek için kullanılır.|
| 31034| ServiceBusListenerError| Bu olay, kiracının Service Bus dinleyicisine bağlanılırken bir hata olduğunu gösterir. Hata iletisi "uzak sertifika geçersiz" içeriyorsa, Azure AD Connect sunucunuzun [Azure TLS sertifikası değişikliklerinde](../../security/fundamentals/tls-certificate-changes.md)açıklandığı gibi tüm gerekli kök CA 'lara sahip olduğundan emin olun. |
| 32000| UnknownError| Bu olay, parola yönetimi işlemi sırasında bilinmeyen bir hata oluştuğunu gösterir. Daha fazla ayrıntı için olaydaki özel durum metnine bakın. Sorun yaşıyorsanız, parola geri yazma özelliğini devre dışı bırakıp yeniden etkinleştirmeyi deneyin. Bu yardım yoksa, bir destek isteği açtığınızda belirtilen izleme KIMLIĞIYLE birlikte olay günlüğlerinizin bir kopyasını ekleyin.|
| 32001| ServiceError| Bu olay, bulut parola sıfırlama hizmetine bağlanılırken bir hata olduğunu gösterir. Bu hata genellikle şirket içi hizmet, parola sıfırlama Web hizmetine bağlanamadığı zaman oluşur.|
| 32002| ServiceBusError| Bu olay, kiracının Service Bus örneğine bağlanılırken bir hata olduğunu gösterir. Şirket içi ortamınızda giden bağlantıları engelliyorsanız bu durum oluşabilir. TCP 443 ve ile bağlantı kurulmasına izin verdiğinizden emin olmak için güvenlik duvarınızı denetleyin https://ssprdedicatedsbprodncu.servicebus.windows.net ve sonra yeniden deneyin. Hala sorun yaşıyorsanız, parola geri yazma özelliğini devre dışı bırakıp yeniden etkinleştirmeyi deneyin.|
| 32003| Inputvalidationerror| Bu olay, Web hizmeti API 'imize geçirilen girişin geçersiz olduğunu gösterir. İşlemi yeniden deneyin.|
| 32004| DecryptionError| Bu olay, buluttan gelen parolanın şifresini çözerken bir hata olduğunu gösterir. Bunun nedeni, bulut hizmeti ile şirket içi ortamınız arasında bir şifre çözme anahtarı uyumsuzluğu olabilir. Bu sorunu çözmek için, şirket içi ortamınızda parola geri yazma özelliğini devre dışı bırakıp yeniden etkinleştirin.|
| 32005| ConfigurationError| Ekleme sırasında, kiracıya özgü bilgileri şirket içi ortamınızdaki bir yapılandırma dosyasına kaydedebiliyoruz. Bu olay, bu dosyayı kaydederken bir hata olduğunu veya hizmetin başlatıldığı sırada dosyayı okurken bir hata olduğunu gösterir. Bu sorunu gidermek için, yapılandırma dosyasının yeniden yazmayı zorlamak üzere parola geri yazma özelliğini devre dışı bırakıp yeniden etkinleştirmeyi deneyin.|
| 32007| OnBoardingConfigUpdateError| Ekleme sırasında, buluttan verileri şirket içi parola sıfırlama hizmetine göndereceğiz. Daha sonra bu veriler, diskte güvenli bir şekilde depolanması için Eşitleme hizmetine gönderilmeden önce bellek içi bir dosyaya yazılır. Bu olay, bellekteki verileri yazma veya güncelleştirme ile ilgili bir sorun olduğunu gösterir. Bu sorunu gidermek için, bu yapılandırma dosyasının yeniden yazmayı zorlamak üzere parola geri yazma özelliğini devre dışı bırakıp yeniden etkinleştirmeyi deneyin.|
| 32008| Doğrulama hatası| Bu olay, parola sıfırlama Web hizmetinden geçersiz bir yanıt aldığınızı gösterir. Bu sorunu gidermek için parola geri yazma özelliğini devre dışı bırakıp yeniden etkinleştirmeyi deneyin.|
| 32009| AuthTokenError| Bu olay, Azure AD Connect kurulumu sırasında belirtilen genel yönetici hesabı için bir yetkilendirme belirteci alamadık. Bu hata, genel yönetici hesabı için belirtilen Hatalı Kullanıcı adı veya parola nedeniyle oluşabilir. Bu hata, belirtilen genel yönetici hesabı federe olduğunda da oluşabilir. Bu sorunu gidermek için yapılandırmayı doğru Kullanıcı adı ve parolayla yeniden çalıştırın ve yöneticinin yönetilen (salt bulut veya parola eşitlenmiş) hesap olduğundan emin olun.|
| 32010| CryptoError| Bu olay, parola şifreleme anahtarını oluştururken veya bulut hizmetinden gelen bir parolanın şifresini çözerken bir hata olduğunu gösterir. Bu hata büyük olasılıkla ortamınızla ilgili bir sorun olduğunu gösterir. Bu sorunu çözme hakkında daha fazla bilgi edinmek için olay günlüğlerinizin ayrıntılarına bakın. Ayrıca parola geri yazma hizmetini devre dışı bırakıp yeniden etkinleştirmeyi deneyebilirsiniz.|
| 32011| OnBoardingServiceError| Bu olay, şirket içi hizmetin ekleme işlemini başlatmak için parola sıfırlama Web hizmetiyle düzgün şekilde iletişim kuramadığını gösterir. Bu, bir güvenlik duvarı kuralı nedeniyle veya kiracınız için bir kimlik doğrulama belirteci alınırken bir sorun ortaya çıkabilir. Bu sorunu gidermek için TCP 443 ve TCP 9350-9354 ya da ile giden bağlantıları engellemediğinden emin olun https://ssprdedicatedsbprodncu.servicebus.windows.net . Ayrıca, eklemek için kullandığınız Azure AD yönetici hesabının Federasyonun olmadığından emin olun.|
| 32013| OffBoardingError| Bu olay, şirket içi hizmetin çıkarma işlemini başlatmak için parola sıfırlama Web hizmeti ile düzgün şekilde iletişim kuramadığını gösterir. Bu, bir güvenlik duvarı kuralı nedeniyle veya kiracınızın yetkilendirme belirtecini alırken bir sorun ortaya çıkabilir. Bu sorunu gidermek için, 443 veya ile giden bağlantıları engellemeden https://ssprdedicatedsbprodncu.servicebus.windows.net ve kullandığınız Azure Active Directory yönetici hesabının Federasyonun olmadığından emin olun.|
| 32014| ServiceBusWarning| Bu olay, kiracının Service Bus örneğine bağlanmayı yeniden denememiz gerektiğini gösterir. Normal koşullar altında bu bir sorun olmaması gerekir, ancak bu olayı birçok kez görürseniz, özellikle yüksek Gecikmeli veya düşük bant genişliğine sahip bir bağlantı olması halinde, ağ bağlantınızı Service Bus kontrol etmeyi göz önünde bulundurun.|
| 32015| ReportServiceHealthError| Parola geri yazma hizmetinizin sistem durumunu izlemek için, her beş dakikada bir parola sıfırlama Web hizmetinize sinyal verileri göndereceğiz. Bu olay, bu durum bilgilerini bulut Web hizmetine geri gönderirken bir hata olduğunu gösterir. Bu durum bilgileri, kişisel veriler içermez ve bulutta hizmet durumu bilgileri sağlayabilmemiz için yalnızca bir sinyal ve temel hizmet istatistiklerdir.|
| 33001| ADUnKnownError| Bu olay Active Directory tarafından döndürülen bilinmeyen bir hata olduğunu gösterir. Daha fazla bilgi için, ADSync kaynağından olaylar için Azure AD Connect Server olay günlüğünü denetleyin.|
| 33002| Adusernotfoun| Bu olay, bir parolayı sıfırlamaya veya değiştirmeye çalışan kullanıcının şirket içi dizinde bulunamadığını gösterir. Bu hata, Kullanıcı şirket içinde silindiğinde ve bulutta olmadığında ortaya çıkabilir. Bu hata, eşitleme ile ilgili bir sorun olduğunda da oluşabilir. Daha fazla bilgi için eşitleme günlüklerinizi ve son birkaç eşitleme çalıştırması ayrıntılarını kontrol edin.|
| 33003| ADMutliMatchError| Bir parola sıfırlama veya değişiklik isteği buluttan kaynaklanıyorsa, bu isteğin şirket içi ortamınızdaki bir kullanıcıya nasıl bağlanacağını öğrenmek için Azure AD Connect kurulum işlemi sırasında belirtilen bulut bağlayıcısını kullanırız. Bu olay, şirket içi dizininizde aynı bulut bağlayıcısı özniteliğiyle iki Kullanıcı bulduğumuz anlamına gelir. Daha fazla bilgi için eşitleme günlüklerinizi ve son birkaç eşitleme çalıştırması ayrıntılarını kontrol edin.|
| 33004| ADPermissionsError| Bu olay, Active Directory Yönetim Aracısı (ADMA) hizmet hesabının yeni bir parola ayarlamak için söz konusu hesapta uygun izinlere sahip olmadığını gösterir. Kullanıcının ormanındaki ADMA hesabının, ormandaki tüm nesnelerde parolayı sıfırlamasına izin olduğundan emin olun. İzinlerin nasıl ayarlanacağı hakkında daha fazla bilgi için bkz. 4. Adım: uygun Active Directory izinleri ayarlama. Bu hata, kullanıcının AdminCount özelliği 1 olarak ayarlandığında da oluşabilir.|
| 33005| ADUserAccountDisabled| Bu olay, şirket içinde devre dışı bırakılmış bir hesabın parolasını sıfırlama veya değiştirme girişiminde bulunulmadığını gösterir. Hesabı etkinleştirin ve işlemi yeniden deneyin.|
| 33006| ADUserAccountLockedOut| Bu olay, şirket içinde kilitlenen bir hesabın parolasını sıfırlama veya değiştirme girişiminde bulunulmadığını gösterir. Bir Kullanıcı kısa bir süre içinde çok fazla kez değişiklik veya sıfırlama işlemini denediğinde kilitleme işlemleri gerçekleşebilir. Hesabın kilidini açın ve işlemi yeniden deneyin.|
| 33007| ADUserIncorrectPassword| Bu olay, kullanıcının bir parola değiştirme işlemi gerçekleştirirken yanlış geçerli parolayı belirtdiğini gösterir. Doğru geçerli parolayı belirtip yeniden deneyin.|
| 33008| ADPasswordPolicyError| Bu olay, parola geri yazma hizmeti yerel dizininizde, etki alanının parola yaşı, geçmişi, karmaşıklık veya filtreleme gereksinimlerini karşılamayan bir parola ayarlamaya çalıştığında oluşur. <br> <br> En az bir parola yaşı varsa ve kısa süre önce bu zaman içindeki parolayı değiştirdiyseniz, etki alanındaki belirli bir yaşa ulaşıncaya kadar parolayı tekrar değiştiremezsiniz. Sınama amacıyla, minimum yaş 0 olarak ayarlanmalıdır. <br> <br> Parola geçmişi gereksinimleriniz etkinse, son *n* kez kullanılmamış bir parola seçmeniz gerekir, burada *n* parola geçmişi ayarıdır. Son *N* kez kullanılan bir parola seçerseniz, bu durumda bir hata görürsünüz. Sınama amacıyla parola geçmişi 0 olarak ayarlanmalıdır. <br> <br> Parola karmaşıklığı gereksinimleriniz varsa, Kullanıcı bir parolayı değiştirmeye veya sıfırlamaya çalıştığında bunların hepsi zorlanır. <br> <br> Parola Filtreleriniz etkinse ve Kullanıcı filtreleme ölçütlerine uymayan bir parola seçerse, sıfırlama veya değiştirme işlemi başarısız olur.|
| 33009| ADConfigurationError| Bu olay, Active Directory bir yapılandırma sorunu nedeniyle Şirket içi dizininize geri parola yazılırken bir sorun olduğunu gösterir. Oluşan hata hakkında daha fazla bilgi için, ADSync hizmetinden iletiler için Azure AD Connect makinenin uygulama olay günlüğünü denetleyin.|

## <a name="azure-ad-forums"></a>Azure AD forumları

Azure AD ve self servis parola sıfırlama hakkında genel sorularınız varsa, [Azure Active Directory Için Microsoft Q&soru sayfasında](/answers/topics/azure-active-directory.html)topluluğa yardım isteyebilirsiniz. Topluluk üyeleri; mühendisler, ürün yöneticileri, MVP 'ler ve BT uzmanlarına yönelik BT profesyonellerini içerir.

## <a name="contact-microsoft-support"></a>Microsoft desteğine başvurma

Bir sorunun yanıtını bulamazsanız, destek ekiplerimiz size daha fazla yardımcı olmak için her zaman kullanılabilir.

Size uygun bir şekilde yardımcı olması için, bir servis talebi açarken mümkün olduğunca fazla ayrıntı sağlamanızı isteyeceğiz. Bu ayrıntılar şunları içerir:

* **Hatanın genel açıklaması**: hata nedir? Fark edilen davranış nedir? Hatayı nasıl yeniden oluşturuyoruz? Mümkün olduğunca çok ayrıntı sağlayın.
* **Sayfa**: hatayı fark ettiğinizde hangi sayfayı kullanıyorsunuz? Mümkün olduğunda URL 'YI ve sayfanın ekran görüntüsünü ekleyin.
* **Destek kodu**: Kullanıcı hatayı gördüğünüzde oluşturulan destek kodu nedir?
   * Bu kodu bulmak için, hatayı yeniden oluşturun ve ardından ekranın alt kısmındaki **destek kodu** bağlantısını seçin ve destek mühendisine sonuçları veren GUID 'yi gönderin.

    :::image type="content" source="./media/troubleshoot-sspr-writeback/view-support-code.png" alt-text="Destek kodu, Web tarayıcı penceresinin sağ alt kısmında bulunur.":::

  * En altta destek kodu olmayan bir sayfadan karşılaşırsanız, F12 ' yi seçin ve SID ve CıD için arama yapın ve bu iki sonucu destek mühendisine gönderin.
* **Tarih, saat ve saat dilimi**: Hatanın gerçekleştiği *saat dilimiyle* kesin tarih ve saati dahil edin.
* **Kullanıcı kimliği**: hatayı seçen kullanıcı kim? *Kullanıcı \@ contoso.com* bir örnektir.
   * Bu bir Federasyon kullanıcısı mı?
   * Bu bir geçişli kimlik doğrulama kullanıcısı mı?
   * Bu, Parola karması ile eşitlenen bir Kullanıcı mı?
   * Bu yalnızca bulut kullanıcısı mı?
* **Lisanslama**: kullanıcıya BIR Azure AD lisansı atandı mi?
* **Uygulama olay günlüğü**: parola geri yazma özelliğini kullanıyorsanız ve hata şirket içi altyapınızda ise, Azure AD Connect sunucusundan uygulama olay günlüğlerinizin daraltılmış bir kopyasını ekleyin.

## <a name="next-steps"></a>Sonraki adımlar

SSPR hakkında daha fazla bilgi edinmek için bkz [. nasıl çalışır: Azure AD self servis parola sıfırlama](concept-sspr-howitworks.md) veya [self servis parola sıfırlama geri yazma özelliği Azure AD 'de nasıl çalışır?](concept-sspr-writeback.md).