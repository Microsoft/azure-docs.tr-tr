---
title: Portalda Azure AD uygulaması & hizmet sorumlusu oluşturma
titleSuffix: Microsoft identity platform
description: Azure Resource Manager rol tabanlı erişim denetimiyle kaynaklara erişimi yönetmek için yeni bir Azure Active Directory App & hizmet sorumlusu oluşturun.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.date: 06/26/2020
ms.author: ryanwi
ms.reviewer: tomfitz
ms.custom: aaddev, seoapril2019, identityplatformtop40
ms.openlocfilehash: 3ccc340727a437b3b1e953ea5e742ecdf7f21d40
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107814091"
---
# <a name="how-to-use-the-portal-to-create-an-azure-ad-application-and-service-principal-that-can-access-resources"></a>Nasıl yapılır: Kaynaklara erişebilen bir Azure AD uygulaması ve hizmet sorumlusu oluşturmak için portalı kullanma

Bu makalede rol tabanlı erişim denetimiyle kullanılabilecek yeni bir Azure Active Directory (Azure AD) uygulaması ve hizmet sorumlusu oluşturma konusu gösterilmektedir. Kaynaklara erişmesi veya kaynakları değiştirmesi gereken uygulamalar, barındırılan hizmetler veya otomatikleştirilmiş araçlar varsa, uygulama için bir kimlik oluşturabilirsiniz. Bu kimlik, hizmet sorumlusu olarak bilinir. Kaynaklara erişim, hizmet sorumlusuna atanan roller tarafından kısıtlanır ve hangi kaynaklara erişilebileceklerini ve hangi düzeyde erişilebilir bir denetim sağlar. Güvenlik nedeniyle, otomatikleştirilmiş araçların kullanıcı kimliği ile oturum açmalarına izin vermek yerine her zaman hizmet sorumlularını kullanmanız önerilir.

Bu makalede, Azure portal hizmet sorumlusunu oluşturmak için portalın nasıl kullanılacağı gösterilir. Uygulamanın yalnızca bir kuruluş içinde çalıştırılması amaçlanan tek kiracılı bir uygulamaya odaklanır. Genellikle kuruluşunuzda çalışan iş kolu uygulamaları için tek kiracılı uygulamalar kullanırsınız.  [Hizmet sorumlusu oluşturmak için Azure PowerShell de kullanabilirsiniz](howto-authenticate-service-principal-powershell.md).

> [!IMPORTANT]
> Hizmet sorumlusu oluşturmak yerine, uygulama kimliğiniz için Azure kaynakları için Yönetilen kimlikler kullanmayı göz önünde bulundurun. Kodunuz yönetilen kimlikleri destekleyen bir hizmette çalışıyorsa ve Azure AD kimlik doğrulamasını destekleyen kaynaklara eriştiğinde, Yönetilen kimlikler sizin için daha iyi bir seçenektir. Azure kaynakları için Yönetilen kimlikler hakkında daha fazla bilgi edinmek için şu anda hangi hizmetleri desteklediği hakkında daha fazla bilgi için bkz. [Azure kaynakları için Yönetilen kimlikler nelerdir?](../managed-identities-azure-resources/overview.md).

## <a name="app-registration-app-objects-and-service-principals"></a>Uygulama kaydı, uygulama nesneleri ve hizmet sorumluları
Azure portal kullanarak doğrudan hizmet sorumlusu oluşturmanın bir yolu yoktur.  Azure portal aracılığıyla bir uygulamayı kaydettiğinizde, giriş dizininizde veya kiracınızda bir uygulama nesnesi ve hizmet sorumlusu otomatik olarak oluşturulur.  Uygulama kaydı, uygulama nesneleri ve hizmet sorumluları arasındaki ilişki hakkında daha fazla bilgi için, [Azure Active Directory Içindeki uygulama ve hizmet sorumlusu nesnelerini](app-objects-and-service-principals.md)okuyun.

## <a name="permissions-required-for-registering-an-app"></a>Bir uygulamayı kaydettirmek için gereken izinler

Azure AD kiracınızla bir uygulamayı kaydetmek ve uygulamaya Azure aboneliğinizdeki bir rolü atamak için yeterli izinlere sahip olmanız gerekir.

### <a name="check-azure-ad-permissions"></a>Azure AD izinlerini denetle

1. **Azure Active Directory** seçin.
1. Rolünüzü aklınızda edin. **Kullanıcı** rolüne sahipseniz, yönetici olmayanların uygulamaları kaydedebiliyorsanız emin olmanız gerekir.

   ![Rolünüzü bulun. Bir Kullanıcı kullanıyorsanız, yönetici olmayan uygulamaları kaydedebilirler](./media/howto-create-service-principal-portal/view-user-info.png)

1. Sol bölmede **Kullanıcı ayarları**' nı seçin.
1. **Uygulama kayıtları** ayarını denetleyin. Bu değer yalnızca bir yönetici tarafından ayarlanabilir. **Evet** olarak ayarlanırsa, Azure AD kiracısındaki tüm kullanıcılar bir uygulamayı kaydedebilir.

Uygulama kayıtları ayarı **Hayır** olarak ayarlandıysa, yalnızca yönetici rolüne sahip kullanıcılar bu tür uygulamaları kaydedebilir. Kullanılabilir yönetici rolleri ve Azure AD 'de her role verilen belirli izinler hakkında bilgi edinmek için bkz. [Azure AD yerleşik rolleri](../roles/permissions-reference.md#all-roles) . Hesabınız Kullanıcı rolüne atanırsa, ancak uygulama kaydı ayarı Yönetici kullanıcılarla sınırlı ise, yöneticinizden uygulama kayıtlarının tüm yönlerini oluşturup yönetebilen yönetici rollerinden birini atamasını veya kullanıcıların uygulamaları kaydetmesini sağlamak için bunu yapmasını isteyin.

### <a name="check-azure-subscription-permissions"></a>Azure abonelik izinlerini denetle

Azure aboneliğinizde, hesabınız bir `Microsoft.Authorization/*/Write` ad uygulamasına rol atamak için erişime sahip olmalıdır. Bu eylemin izni, [Sahip](../../role-based-access-control/built-in-roles.md#owner) rolüyle veya [Kullanıcı Erişimi Yöneticisi](../../role-based-access-control/built-in-roles.md#user-access-administrator) rolüyle verilir. Hesabınıza **katkıda bulunan** rolü atanırsa, yeterli izne sahip değilsiniz. Hizmet sorumlusu bir rol atamaya çalışırken bir hata alırsınız.

Abonelik izinlerinizi denetlemek için:

1. **Abonelik** arayın ve seçin veya **giriş** sayfasında **abonelikler** ' i seçin.

   ![Arayın](./media/howto-create-service-principal-portal/select-subscription.png)

1. Hizmet sorumlusunu oluşturmak istediğiniz aboneliği seçin.

   ![Atama için abonelik seçin](./media/howto-create-service-principal-portal/select-one-subscription.png)

   Aradığınız aboneliği görmüyorsanız **genel abonelikler filtresi**' ni seçin. Portal için istediğiniz aboneliğin seçildiğinden emin olun.

1. **Izinlerim**' i seçin. Ardından, **bu aboneliğin tüm erişim ayrıntılarını görüntülemek için buraya tıklayın '** ı seçin.

   ![Hizmet sorumlusunu oluşturmak istediğiniz aboneliği seçin](./media/howto-create-service-principal-portal/view-details.png)

1. Atanan rollerinizi görüntülemek için **rol atamalarında** **Görünüm** ' ü seçin ve bir ad uygulamasına rol atamak için yeterli izinlere sahip olup olmadığınızı belirleyin. Aksi takdirde, abonelik yöneticinizden sizi Kullanıcı erişimi Yöneticisi rolüne eklemesini isteyin. Aşağıdaki görüntüde, Kullanıcı sahip rolüne atanır, bu da kullanıcının yeterli izinlere sahip olduğu anlamına gelir.

   ![Bu örnek, kullanıcıya sahip rolü atandığını gösterir](./media/howto-create-service-principal-portal/view-user-role.png)

## <a name="register-an-application-with-azure-ad-and-create-a-service-principal"></a>Bir uygulamayı Azure AD 'ye kaydetme ve hizmet sorumlusu oluşturma

Şimdi kimlik oluşturmaya doğrudan atlayalım. Bir sorunla karşılaşırsanız, hesabınızın kimlik oluşturup oluşturerişebildiğinizden emin olmak için [gerekli izinleri](#permissions-required-for-registering-an-app) denetleyin.

1. <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>aracılığıyla Azure hesabınızda oturum açın.
1. **Azure Active Directory** seçin.
1. **Uygulama kayıtları**’nı seçin.
1. **Yeni kayıt** seçeneğini belirleyin.
1. Uygulamayı adlandırın. Uygulamayı kimlerin kullanabileceğinizi belirleyen desteklenen bir hesap türü seçin. **Yeniden yönlendirme URI 'si** altında, oluşturmak istediğiniz uygulama türü için **Web** ' i seçin. Erişim belirtecinin gönderildiği URI 'yi girin. [Yerel bir uygulama](../manage-apps/application-proxy-configure-native-client-application.md)için kimlik bilgileri oluşturamazsınız. Bu türü otomatik bir uygulama için kullanamazsınız. Değerleri ayarladıktan sonra **Kaydet**' i seçin.

   ![Uygulamanız için bir ad yazın](./media/howto-create-service-principal-portal/create-app.png)

Azure AD uygulamanızı ve hizmet sorumlusunu oluşturdunuz.

> [!NOTE]
> Azure AD 'de aynı ada sahip birden çok uygulamayı kaydedebilirsiniz, ancak uygulamalar farklı uygulama (istemci) kimliklerine sahip olmalıdır.

## <a name="assign-a-role-to-the-application"></a>Uygulamaya bir rol atama

Aboneliğinizdeki kaynaklara erişmek için uygulamaya bir rol atamanız gerekir. Hangi rolün uygulama için doğru izinleri sunduğunu belirleyin. Kullanılabilir roller hakkında bilgi edinmek için bkz. [Azure yerleşik rolleri](../../role-based-access-control/built-in-roles.md).

Kapsamı, abonelik, kaynak grubu veya kaynak düzeyinde ayarlayabilirsiniz. İzinler, daha düşük kapsam düzeylerine devralınır. Örneğin, bir kaynak grubu için *okuyucu* rolüne bir uygulama eklemek, kaynak grubunu ve içerdiği kaynakları okuyabileceği anlamına gelir.

1. Azure portal, uygulamayı atamak istediğiniz kapsam düzeyini seçin. Örneğin, abonelik kapsamında bir rol atamak için, **abonelikleri** arayıp seçin ya da **giriş** sayfasında **abonelikler** ' i seçin.

   ![Örneğin, abonelik kapsamında bir rol atayın](./media/howto-create-service-principal-portal/select-subscription.png)

1. Uygulamayı atamak için belirli bir abonelik seçin.

   ![Atama için abonelik seçin](./media/howto-create-service-principal-portal/select-one-subscription.png)

   Aradığınız aboneliği görmüyorsanız **genel abonelikler filtresi**' ni seçin. Portal için istediğiniz aboneliğin seçildiğinden emin olun.

1. **Erişim denetimi (IAM)** öğesini seçin.
1. **Rol ataması ekle**’yi seçin.
1. Uygulamaya atamak istediğiniz rolü seçin. Örneğin, uygulamanın **yeniden başlatma** gibi eylemleri yürütmesine izin vermek için örnekleri **başlatın** ve **durdurun** , **katkıda bulunan** rolünü seçin.  [Kullanılabilir roller](../../role-based-access-control/built-in-roles.md) hakkında daha fazla bilgi için, varsayılan olarak Azure AD uygulamaları kullanılabilir seçeneklerde gösterilmez. Uygulamanızı bulmak için adı arayın ve seçin.

   ![Uygulamaya atanacak rolü seçin](./media/howto-create-service-principal-portal/select-role.png)

1. Rolü atamaya son vermek için **Kaydet** ' i seçin. Uygulamanızı bu kapsam için bir role sahip olan kullanıcılar listesinde görürsünüz.

Hizmet sorumlusu ayarlanır. Betikleri veya uygulamalarınızı çalıştırmak için kullanmaya başlayabilirsiniz. Hizmet sorumlunuzu yönetmek için (izinler, Kullanıcı onaylı izinler, hangi kullanıcıların onaylı olduğunu görün, izinleri gözden geçirin, oturum açma bilgilerini görüntüleyin ve daha fazla), **kurumsal uygulamalara** gidin.

Sonraki bölümde, programlama yoluyla oturum açarken gereken değerlerin nasıl alınacağı gösterilmektedir.

## <a name="get-tenant-and-app-id-values-for-signing-in"></a>Oturum açmak için kiracı ve uygulama KIMLIĞI değerlerini al

Programlı olarak oturum açtığınızda, kiracı KIMLIĞINI kimlik doğrulama isteğinizle ve uygulama KIMLIĞIYLE geçirin.  Ayrıca bir sertifika veya bir kimlik doğrulama anahtarı (aşağıdaki bölümde açıklanmıştır) gerekir. Bu değerleri almak için aşağıdaki adımları kullanın:

1. **Azure Active Directory** seçin.
1. Azure AD 'de **uygulama kayıtları** uygulamanızı seçin.
1. Dizin (kiracı) KIMLIĞINI kopyalayın ve uygulama kodunuzda depolayın.

    ![Dizini (kiracı KIMLIĞI) kopyalayın ve uygulama kodunuzda depolayın](./media/howto-create-service-principal-portal/copy-tenant-id.png)

    Dizin (kiracı) KIMLIĞI, varsayılan dizine genel bakış sayfasında da bulunabilir.

1. **Uygulama kimliği**'ni kopyalayın ve bunu uygulama kodunuzda depolayın.

   ![Uygulama (istemci) KIMLIĞINI Kopyala](./media/howto-create-service-principal-portal/copy-app-id.png)

## <a name="authentication-two-options"></a>Kimlik doğrulaması: Iki seçenek

Hizmet sorumluları için kullanılabilen iki kimlik doğrulama türü vardır: parola tabanlı kimlik doğrulaması (uygulama gizli dizisi) ve sertifika tabanlı kimlik doğrulama. *Bir sertifika kullanmanızı öneririz*, ancak bir uygulama gizli anahtarı da oluşturabilirsiniz.

### <a name="option-1-upload-a-certificate"></a>Seçenek 1: bir sertifikayı karşıya yükle

Varsa, var olan bir sertifikayı kullanabilirsiniz.  İsteğe bağlı olarak, *yalnızca sınama amacıyla* otomatik olarak imzalanan bir sertifika oluşturabilirsiniz. Otomatik olarak imzalanan bir sertifika oluşturmak için PowerShell 'i açın ve bilgisayarınızdaki Kullanıcı sertifika deposunda sertifikayı oluşturmak için aşağıdaki parametrelerle [Yeni bir SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate) çalıştırın:

```powershell
$cert=New-SelfSignedCertificate -Subject "CN=DaemonConsoleCert" -CertStoreLocation "Cert:\CurrentUser\My"  -KeyExportPolicy Exportable -KeySpec Signature
```

Bu sertifikayı, Windows Denetim Masası 'ndan erişilebilen [Kullanıcı sertifikasını Yönet](/dotnet/framework/wcf/feature-details/how-to-view-certificates-with-the-mmc-snap-in) MMC ek bileşenini kullanarak bir dosyaya dışarı aktarın.

1. **Başlat** menüsünden **Çalıştır** ' ı seçin ve ardından **certmgr. msc** yazın.

   Geçerli Kullanıcı için Sertifika Yöneticisi aracı görünür.

1. Sertifikalarınızı görüntülemek için **Sertifikalar** ' ın altında, sol bölmedeki **Kişisel** Dizin ' i genişletin.
1. Oluşturduğunuz sertifikaya sağ tıklayın, **Tüm görevler->dışarı aktar**' ı seçin.
1. Sertifika Verme Sihirbazı ' nı izleyin.  Özel anahtarı dışarı aktarmayın ve ' a aktarın. CER dosyası.

Sertifikayı karşıya yüklemek için:

1. **Azure Active Directory** seçin.
1. Azure AD 'de **uygulama kayıtları** uygulamanızı seçin.
1. **Sertifikalar & parolaları**' nı seçin.
1. **Sertifikayı karşıya yükle** ' yi seçin ve sertifikayı (mevcut bir sertifika ya da verdiğiniz otomatik olarak imzalanan sertifika) seçin.

    ![Sertifikayı karşıya yükle ' yi seçin ve eklemek istediğiniz birini seçin](./media/howto-create-service-principal-portal/upload-cert.png)

1. **Add (Ekle)** seçeneğini belirleyin.

Sertifikayı uygulama kayıt portalı 'nda uygulamanıza kaydettikten sonra, sertifikayı kullanmak için istemci uygulama kodunu etkinleştirin.

### <a name="option-2-create-a-new-application-secret"></a>2. seçenek: yeni bir uygulama parolası oluşturma

Bir sertifika kullanmayı tercih ederseniz, yeni bir uygulama parolası oluşturabilirsiniz.

1. **Azure Active Directory** seçin.
1. Azure AD 'de **uygulama kayıtları** uygulamanızı seçin.
1. **Sertifikalar & parolaları**' nı seçin.
1. Istemci gizli dizileri **-> yeni istemci parolası**' nı seçin.
1. Gizli anahtar ve süre için bir açıklama sağlayın. İşiniz bittiğinde **Ekle**' yi seçin.

   İstemci gizliliğini kaydettikten sonra, istemci parolasının değeri görüntülenir. Anahtarı daha sonra alamayamayacağından bu değeri kopyalayın. Uygulama kimliğiyle oturum açmak için anahtar değerini uygulama kimliğiyle birlikte sağlarsınız. Anahtarı, uygulamanızın alabileceği bir konumda depolayın.

   ![Daha sonra geri alamadığı için gizli değeri kopyalayın](./media/howto-create-service-principal-portal/copy-secret.png)

## <a name="configure-access-policies-on-resources"></a>Kaynaklarda erişim ilkeleri yapılandırma
Unutmayın, uygulamanızın erişmesi gereken kaynaklarda ek izinler yapılandırmanız gerekebilir. Örneğin, uygulamanıza anahtarlar, gizlilikler veya sertifikalara erişim sağlamak için [bir anahtar kasasının erişim ilkelerini de güncelleştirmeniz](../../key-vault/general/security-features.md#privileged-access) gerekir.

1. <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>, anahtar kasanıza gidin ve **erişim ilkeleri**' ni seçin.
1. **Erişim Ilkesi Ekle**' yi seçin, ardından uygulamanıza vermek istediğiniz anahtar, gizli dizi ve sertifika izinlerini seçin.  Daha önce oluşturduğunuz hizmet sorumlusunu seçin.
1. Erişim ilkesini eklemek için **Ekle** ' yi seçin ve sonra değişikliklerinizi yürütmek için **kaydedin** .
    ![Erişim İlkesi Ekle](./media/howto-create-service-principal-portal/add-access-policy.png)

## <a name="next-steps"></a>Sonraki adımlar
* [Hizmet sorumlusu oluşturmak için Azure PowerShell kullanmayı](howto-authenticate-service-principal-powershell.md)öğrenin.
* Güvenlik ilkelerini belirtme hakkında bilgi edinmek için bkz. [Azure rol tabanlı erişim denetimi (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md).
* Kullanıcılara verilebilecek veya reddedilmiş olabilecek eylemlerin bir listesi için bkz. [Azure Resource Manager kaynak sağlayıcısı işlemleri](../../role-based-access-control/resource-provider-operations.md).
* **Microsoft Graph** kullanarak uygulama kayıtlarıyla çalışma hakkında daha fazla bilgi için bkz. [uygulamalar](/graph/api/resources/application) API başvurusu.
