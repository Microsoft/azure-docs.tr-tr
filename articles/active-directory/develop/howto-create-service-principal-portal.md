---
title: Portalda Azure uygulaması için kimlik oluşturma
titleSuffix: Microsoft identity platform
description: Kaynaklara erişimi yönetmek için Azure Resource Manager rol tabanlı erişim denetimiyle kullanılabilecek yeni bir Azure Active Directory uygulaması ve hizmet sorumlusu oluşturma işlemini açıklar.
services: active-directory
documentationcenter: na
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/14/2019
ms.author: ryanwi
ms.reviewer: tomfitz
ms.custom: aaddev, seoapril2019, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 03ae780f86512ac401fcb7f6936e8f74cb595ca7
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73473845"
---
# <a name="how-to-use-the-portal-to-create-an-azure-ad-application-and-service-principal-that-can-access-resources"></a>Nasıl yapılır: kaynaklara erişebilen bir Azure AD uygulaması ve hizmet sorumlusu oluşturmak için portalı kullanma

Bu makalede rol tabanlı erişim denetimiyle kullanılabilecek yeni bir Azure Active Directory (Azure AD) uygulaması ve hizmet sorumlusu oluşturma konusu gösterilmektedir. Kaynaklara erişmesi veya kaynakları değiştirmesi gereken bir kodunuz olduğunda, uygulama için bir kimlik oluşturabilirsiniz. Bu kimlik, hizmet sorumlusu olarak bilinir. Daha sonra, gerekli izinleri hizmet sorumlusuna atayabilirsiniz. Bu makalede, portalının hizmet sorumlusu oluşturmak için nasıl kullanılacağı gösterilir. Uygulamanın yalnızca bir kuruluş içinde çalıştırılması amaçlanan tek kiracılı bir uygulamaya odaklanır. Genellikle kuruluşunuzda çalışan iş kolu uygulamaları için tek kiracılı uygulamalar kullanırsınız.

> [!IMPORTANT]
> Hizmet sorumlusu oluşturmak yerine, uygulama kimliğiniz için Azure kaynakları için Yönetilen kimlikler kullanmayı göz önünde bulundurun. Kodunuz yönetilen kimlikleri destekleyen bir hizmette çalışıyorsa ve Azure AD kimlik doğrulamasını destekleyen kaynaklara eriştiğinde, Yönetilen kimlikler sizin için daha iyi bir seçenektir. Azure kaynakları için Yönetilen kimlikler hakkında daha fazla bilgi edinmek için şu anda hangi hizmetleri desteklediği hakkında daha fazla bilgi için bkz. [Azure kaynakları için Yönetilen kimlikler nelerdir?](../managed-identities-azure-resources/overview.md).

## <a name="create-an-azure-active-directory-application"></a>Azure Active Directory uygulaması oluşturma

Şimdi kimlik oluşturmaya doğrudan atlayalım. Bir sorunla karşılaşırsanız, hesabınızın kimlik oluşturup oluşturerişebildiğinizden emin olmak için [gerekli izinleri](#required-permissions) denetleyin.

1. [Azure Portal](https://portal.azure.com)aracılığıyla Azure hesabınızda oturum açın.
1. **Azure Active Directory**'yi seçin.
1. **Uygulama kayıtları**'nı seçin.
1. **Yeni kayıt**seçeneğini belirleyin.
1. Uygulamayı adlandırın. Uygulamayı kimlerin kullanabileceğinizi belirleyen desteklenen bir hesap türü seçin. **Yeniden yönlendirme URI 'si**altında, oluşturmak istediğiniz uygulama türü için **Web** ' i seçin. Erişim belirtecinin gönderildiği URI 'yi girin. [Yerel bir uygulama](../manage-apps/application-proxy-configure-native-client-application.md)için kimlik bilgileri oluşturamazsınız. Bu türü otomatik bir uygulama için kullanamazsınız. Değerleri ayarladıktan sonra **Kaydet**' i seçin.

   ![Uygulamanız için bir ad yazın](./media/howto-create-service-principal-portal/create-app.png)

Azure AD uygulamanızı ve hizmet sorumlusunu oluşturdunuz.

## <a name="assign-the-application-to-a-role"></a>Uygulamayı bir role atama

Aboneliğinizdeki kaynaklara erişmek için uygulamayı bir role atamanız gerekir. Hangi rolün uygulama için doğru izinleri sunduğunu belirleyin. Kullanılabilir roller hakkında daha fazla bilgi edinmek için bkz. [RBAC: yerleşik roller](../../role-based-access-control/built-in-roles.md).

Kapsamı, abonelik, kaynak grubu veya kaynak düzeyinde ayarlayabilirsiniz. İzinler, daha düşük kapsam düzeylerine devralınır. Örneğin, bir kaynak grubu için okuyucu rolüne bir uygulama eklemek, kaynak grubunu ve içerdiği kaynakları okuyabileceği anlamına gelir.

1. Azure portal, uygulamayı atamak istediğiniz kapsam düzeyini seçin. Örneğin, abonelik kapsamında bir rol atamak için, **abonelikleri**arayıp seçin ya da **giriş** sayfasında **abonelikler** ' i seçin.

   ![Örneğin, abonelik kapsamında bir rol atayın](./media/howto-create-service-principal-portal/select-subscription.png)

1. Uygulamayı atamak için belirli bir abonelik seçin.

   ![Atama için abonelik seçin](./media/howto-create-service-principal-portal/select-one-subscription.png)

   Aradığınız aboneliği görmüyorsanız **genel abonelikler filtresi**' ni seçin. Portal için istediğiniz aboneliğin seçildiğinden emin olun.

1. **Erişim denetimi (IAM)** öğesini seçin.
1. **Rol ataması Ekle**' yi seçin.
1. Uygulamaya atamak istediğiniz rolü seçin. Örneğin, uygulamanın **yeniden başlatma**gibi eylemleri yürütmesine izin vermek için örnekleri **başlatın** ve **durdurun** , **katkıda bulunan** rolünü seçin.  [Kullanılabilir roller](../../role-based-access-control/built-in-roles.md) hakkında daha fazla bilgi için, varsayılan olarak Azure AD uygulamaları kullanılabilir seçeneklerde gösterilmez. Uygulamanızı bulmak için adı arayın ve seçin.

   ![Uygulamaya atanacak rolü seçin](./media/howto-create-service-principal-portal/select-role.png)

1. Rolü atamaya son vermek için **Kaydet** ' i seçin. Uygulamanızı bu kapsam için bir role atanan kullanıcılar listesinde görürsünüz.

Hizmet sorumlusu ayarlanır. Betikleri veya uygulamalarınızı çalıştırmak için kullanmaya başlayabilirsiniz. Sonraki bölümde, programlama yoluyla oturum açarken gereken değerlerin nasıl alınacağı gösterilmektedir.

## <a name="get-values-for-signing-in"></a>Oturum açmak için değerleri Al

Programlı olarak oturum açtığınızda, kimlik doğrulama isteğinizle kiracı KIMLIĞINI geçirmeniz gerekir. Ayrıca uygulamanız için KIMLIĞE ve kimlik doğrulama anahtarına ihtiyacınız vardır. Bu değerleri almak için aşağıdaki adımları kullanın:

1. **Azure Active Directory**'yi seçin.
1. Azure AD 'de **uygulama kayıtları** uygulamanızı seçin.
1. Dizin (kiracı) KIMLIĞINI kopyalayın ve uygulama kodunuzda depolayın.

    ![Dizini (kiracı KIMLIĞI) kopyalayın ve uygulama kodunuzda depolayın](./media/howto-create-service-principal-portal/copy-tenant-id.png)

1. **Uygulama kimliği**'ni kopyalayın ve bunu uygulama kodunuzda depolayın.

   ![Uygulama (istemci) KIMLIĞINI Kopyala](./media/howto-create-service-principal-portal/copy-app-id.png)

## <a name="certificates-and-secrets"></a>Sertifikalar ve gizlilikler
Daemon uygulamaları, Azure AD 'de kimlik doğrulaması yapmak için iki kimlik bilgileri biçimi kullanabilir: sertifikalar ve uygulama gizli dizileri.  Bir sertifika kullanmanızı öneririz, ancak yeni bir uygulama gizli anahtarı da oluşturabilirsiniz.

### <a name="upload-a-certificate"></a>Sertifikayı karşıya yükle

Varsa, var olan bir sertifikayı kullanabilirsiniz.  İsteğe bağlı olarak, sınama amacıyla otomatik olarak imzalanan bir sertifika oluşturabilirsiniz. PowerShell 'i açın ve bilgisayarınızdaki Kullanıcı sertifika deposunda kendinden imzalı bir sertifika oluşturmak için aşağıdaki parametrelerle [Yeni bir SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate) çalıştırın: 

```powershell
$cert=New-SelfSignedCertificate -Subject "CN=DaemonConsoleCert" -CertStoreLocation "Cert:\CurrentUser\My"  -KeyExportPolicy Exportable -KeySpec Signature
```

Bu sertifikayı, Windows Denetim Masası 'ndan erişilebilen [Kullanıcı sertifikasını Yönet](/dotnet/framework/wcf/feature-details/how-to-view-certificates-with-the-mmc-snap-in) MMC ek bileşenini kullanarak bir dosyaya dışarı aktarın.

Sertifikayı karşıya yüklemek için:

1. **Sertifikalar & parolaları**' nı seçin.
1. **Sertifikayı karşıya yükle** ' yi seçin ve sertifikayı (mevcut bir sertifika ya da verdiğiniz otomatik olarak imzalanan sertifika) seçin.

    ![Sertifikayı karşıya yükle ' yi seçin ve eklemek istediğiniz birini seçin](./media/howto-create-service-principal-portal/upload-cert.png)

1. **Add (Ekle)** seçeneğini belirleyin.

Sertifikayı uygulama kayıt portalı 'nda uygulamanıza kaydettikten sonra, sertifikayı kullanmak için istemci uygulama kodunu etkinleştirmeniz gerekir.

### <a name="create-a-new-application-secret"></a>Yeni bir uygulama parolası oluştur

Bir sertifika kullanmayı tercih ederseniz, yeni bir uygulama parolası oluşturabilirsiniz.

1. **Sertifikalar & parolaları**' nı seçin.
1. Istemci gizli dizileri **-> yeni istemci parolası**' nı seçin.
1. Gizli anahtar ve süre için bir açıklama sağlayın. İşiniz bittiğinde **Ekle**' yi seçin.

   İstemci gizliliğini kaydettikten sonra, istemci parolasının değeri görüntülenir. Anahtarı daha sonra alamadığı için bu değeri kopyalayın. Uygulama kimliğiyle oturum açmak için anahtar değerini uygulama kimliğiyle birlikte sağlarsınız. Anahtarı, uygulamanızın alabileceği bir konumda depolayın.

   ![Daha sonra geri alamadığı için gizli değeri kopyalayın](./media/howto-create-service-principal-portal/copy-secret.png)

## <a name="configure-access-policies-on-resources"></a>Kaynaklarda erişim ilkeleri yapılandırma
Unutmayın, uygulamanızın erişmesi gereken kaynaklarda ek izinler yapılandırmanız gerekebilir. Örneğin, uygulamanıza anahtarlar, gizlilikler veya sertifikalara erişim sağlamak için [bir anahtar kasasının erişim ilkelerini de güncelleştirmeniz](/azure/key-vault/key-vault-secure-your-key-vault#data-plane-and-access-policies) gerekir.  

1. [Azure Portal](https://portal.azure.com), anahtar kasanıza gidin ve **erişim ilkeleri**' ni seçin.  
1. **Erişim Ilkesi Ekle**' yi seçin, ardından uygulamanıza vermek istediğiniz anahtar, gizli dizi ve sertifika izinlerini seçin.  Daha önce oluşturduğunuz hizmet sorumlusunu seçin.
1. Erişim ilkesini eklemek için **Ekle** ' yi seçin ve sonra değişikliklerinizi yürütmek için **kaydedin** .
    ![erişim ilkesi Ekle](./media/howto-create-service-principal-portal/add-access-policy.png)

## <a name="required-permissions"></a>Gerekli izinler

Azure AD kiracınızla bir uygulamayı kaydetmek ve uygulamayı Azure aboneliğinizdeki bir role atamak için yeterli izinlere sahip olmanız gerekir.

### <a name="check-azure-ad-permissions"></a>Azure AD izinlerini denetle

1. **Azure Active Directory**'yi seçin.
1. Rolünüzü aklınızda edin. **Kullanıcı** rolüne sahipseniz, yönetici olmayanların uygulamaları kaydedebiliyorsanız emin olmanız gerekir.

   ![Rolünüzü bulun. Bir Kullanıcı kullanıyorsanız, yönetici olmayan uygulamaları kaydedebilirler](./media/howto-create-service-principal-portal/view-user-info.png)

1. Sol bölmede **Kullanıcı ayarları**' nı seçin.
1. **Uygulama kayıtları** ayarını denetleyin. Bu değer yalnızca bir yönetici tarafından ayarlanabilir. **Evet**olarak ayarlanırsa, Azure AD kiracısındaki tüm kullanıcılar bir uygulamayı kaydedebilir.

Uygulama kayıtları ayarı **Hayır**olarak ayarlandıysa, yalnızca yönetici rolüne sahip kullanıcılar bu tür uygulamaları kaydedebilir. Kullanılabilir yönetici rolleri ve Azure AD 'de her role verilen belirli izinler hakkında bilgi edinmek için bkz. [kullanılabilir roller](../users-groups-roles/directory-assign-admin-roles.md#available-roles) ve [rol izinleri](../users-groups-roles/directory-assign-admin-roles.md#role-permissions) . Hesabınız Kullanıcı rolüne atanırsa, ancak uygulama kaydı ayarı Yönetici kullanıcılarla sınırlı ise, yöneticinizden uygulama kayıtlarının tüm yönlerini oluşturup yönetebilen yönetici rollerinden birine atamasını veya kullanıcıların şunları yapmasını isteyin uygulamaları kaydedin.

### <a name="check-azure-subscription-permissions"></a>Azure abonelik izinlerini denetle

Azure aboneliğinizde, bir role AD uygulaması atamak için hesabınızın `Microsoft.Authorization/*/Write` erişimi olmalıdır. Bu eylemin izni, [Sahip](../../role-based-access-control/built-in-roles.md#owner) rolüyle veya [Kullanıcı Erişimi Yöneticisi](../../role-based-access-control/built-in-roles.md#user-access-administrator) rolüyle verilir. Hesabınız **katkıda bulunan** rolüne atanırsa, yeterli izne sahip değilsiniz. Hizmet sorumlusunu bir role atamaya çalışırken bir hata alıyorsunuz.

Abonelik izinlerinizi denetlemek için:

1. Sağ üst köşedeki hesabınızı seçin ve **...-> Izinlerim**' i seçin.

   ![Hesabınızı ve Kullanıcı izinlerinizi seçin](./media/howto-create-service-principal-portal/select-my-permissions.png)

1. Aşağı açılan listeden, hizmet sorumlusunu oluşturmak istediğiniz aboneliği seçin. Ardından, **bu aboneliğin tüm erişim ayrıntılarını görüntülemek için buraya tıklayın '** ı seçin.

   ![Hizmet sorumlusunu oluşturmak istediğiniz aboneliği seçin](./media/howto-create-service-principal-portal/view-details.png)

1. Atanan rollerinizi görüntülemek için **rol atamaları** ' nı seçin ve bir rol IÇIN bir ad uygulaması atamak için yeterli izinlere sahip olup olmadığınızı belirleyin. Aksi takdirde, abonelik yöneticinizden sizi Kullanıcı erişimi Yöneticisi rolüne eklemesini isteyin. Aşağıdaki görüntüde, Kullanıcı sahip rolüne atanır, bu da kullanıcının yeterli izinlere sahip olduğu anlamına gelir.

   ![Bu örnek, kullanıcının sahip rolüne atandığını gösterir](./media/howto-create-service-principal-portal/view-user-role.png)

## <a name="next-steps"></a>Sonraki adımlar

* Güvenlik ilkelerini belirtme hakkında bilgi edinmek için bkz. [Azure rol tabanlı Access Control](../../role-based-access-control/role-assignments-portal.md).  
* Kullanıcılara verilebilecek veya reddedilmiş olabilecek eylemlerin bir listesi için bkz. [Azure Resource Manager kaynak sağlayıcısı işlemleri](../../role-based-access-control/resource-provider-operations.md).
