---
title: Daemon uygulamasının güvenliğini sağlama
titleSuffix: Azure Maps
description: Güvenilen bir Daemon uygulamasını yapılandırmak üzere kimlik doğrulamasını yönetmek için Azure portal kullanın.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/12/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 2dd04f404330a6c86e2df09da610e16ba9b721f3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92895656"
---
# <a name="secure-a-daemon-application"></a>Daemon uygulamasının güvenliğini sağlama

Aşağıdaki kılavuz, güvenilir ve güvenli bir ortamda barındırılan arka plan işlemleri, zamanlayıcılar ve işlere yöneliktir. Azure Web Işleri, Azure Işlev uygulamaları, Windows Hizmetleri ve diğer güvenilir arka plan hizmetleri örnekleri bulunur.

> [!Tip]
> Microsoft, üretim uygulamaları için Azure Active Directory (Azure AD) ve Azure rol tabanlı erişim denetimi (Azure RBAC) uygulamasını önerir. Kavramlara genel bakış için bkz. [Azure Maps kimlik doğrulaması](./azure-maps-authentication.md).

[!INCLUDE [authentication details](./includes/view-authentication-details.md)]

## <a name="scenario-shared-key-authentication"></a>Senaryo: paylaşılan anahtar kimlik doğrulaması

Azure haritalar hesabı oluşturduktan sonra, birincil ve ikincil anahtarlar oluşturulur. [Azure haritalar 'ı çağırmak için paylaşılan anahtar kimlik doğrulaması](./azure-maps-authentication.md#shared-key-authentication)kullandığınızda, birincil anahtarı abonelik anahtarı olarak kullanmanızı öneririz. İkincil bir anahtarı, kayan anahtar değişiklikleri gibi senaryolarda kullanabilirsiniz. Daha fazla bilgi için bkz. [Azure haritalar 'Da kimlik doğrulaması](./azure-maps-authentication.md).

### <a name="securely-store-shared-key"></a>Paylaşılan anahtarı güvenli bir şekilde depola

Birincil ve ikincil anahtar, haritalar hesabına yönelik tüm API 'Ler için yetkilendirmeye izin verir. Uygulamalar anahtarları Azure Key Vault gibi güvenli bir depoda depolamalıdır. Paylaşılan anahtarı uygulama yapılandırmasında düz metin olarak depolamayı önlemek için uygulamanın paylaşılan anahtarı Azure Key Vault gizli anahtar olarak alması gerekir. Bir Azure Key Vault yapılandırmayı anlamak için, bkz. [Azure Key Vault Geliştirici Kılavuzu](../key-vault/general/developers-guide.md).

Aşağıdaki adımlarda bu süreç ana hatlarıyla verilmiştir:

1. Azure Key Vault oluşturun.
2. Bir Azure AD hizmet sorumlusu oluşturun bir uygulama kaydı veya yönetilen kimlik oluşturarak, oluşturulan sorumlu Azure Key Vault erişiminden sorumludur.
3. Hizmet sorumlusu erişimini Azure anahtar gizli dizileri iznine atayın `Get` .
4. Geliştirici olarak gizli dizi izinlerine geçici olarak erişim atayın `Set` .
5. Key Vault gizli dizileri içinde paylaşılan anahtarı ayarlayın ve arka plan uygulamasının yapılandırma olarak gizli dizi KIMLIĞINE başvurun ve gizli dizi `Set` izninizi kaldırın.
6. Azure Key Vault paylaşılan anahtar gizli anahtarını almak için Daemon uygulamasında Azure AD kimlik doğrulamasını uygulayın.
7. Paylaşılan anahtarla REST API Azure haritaları oluşturun.

> [!Tip]
> Uygulama Azure ortamında barındırılıyorsa, Azure Key Vault kimlik doğrulaması yapmak için bir gizli dizi yönetiminin maliyetini ve karmaşıklığını azaltmak üzere yönetilen bir kimlik uygulamalısınız. [Yönetilen kimlik üzerinden bağlanmak için aşağıdaki Azure Key Vault öğreticiye](../key-vault/general/tutorial-net-create-vault-azure-web-app.md)bakın.

Daemon uygulaması, paylaşılan anahtarı güvenli bir depolamadan almaktan sorumludur. Azure Key Vault uygulamayla, gizliliğe erişmek için Azure AD aracılığıyla kimlik doğrulaması gerekir. Bunun yerine, paylaşılan anahtar kimlik doğrulamasını kullanmaya yönelik ek karmaşıklık ve işletimsel gereksinimlerin sonucu olarak Azure AD kimlik doğrulamasını Azure Maps 'e doğrudan yönlendiririz.

> [!IMPORTANT]
> Anahtar yeniden oluşturmayı basitleştirmek için, uygulamaların tek seferde bir anahtar kullanmasını öneririz. Uygulamalar daha sonra kullanılmayan anahtarı yeniden oluşturabilir ve yeni oluşturulan anahtarı Azure Key Vault gibi güvenli bir gizli depolama alanına dağıtabilir.

## <a name="scenario-azure-ad-role-based-access-control"></a>Senaryo: Azure AD rol tabanlı erişim denetimi

Azure haritalar hesabı oluşturulduktan sonra, `x-ms-client-id` Azure Portal kimlik doğrulaması ayrıntıları sayfasında Azure Maps değeri mevcuttur. Bu değer, REST API istekleri için kullanılacak hesabı temsil eder. Bu değer, uygulama yapılandırmasında depolanmalıdır ve HTTP istekleri yapılmadan önce alınmalıdır. Senaryonun amacı, Daemon uygulamasının Azure AD 'de kimlik doğrulaması yapmasını ve Azure haritalar REST API 'Lerini çağırmasını sağlar.

> [!Tip]
> Yönetilen kimlik bileşenlerinin avantajlarını etkinleştirmek için Azure sanal makinelerinde, sanal makine ölçek kümelerinde veya uygulama hizmetlerinde barındırılmasını öneririz.

### <a name="daemon-hosted-on-azure-resources"></a>Azure kaynaklarında barındırılan Daemon

Azure kaynakları üzerinde çalışırken, düşük maliyetli, minimum kimlik bilgisi yönetimi çabasında Azure yönetilen kimliklerini yapılandırın. 

Uygulamanın yönetilen bir kimliğe erişimini sağlamak için [yönetilen kimliklere genel bakış](../active-directory/managed-identities-azure-resources/overview.md) bölümüne bakın.

Yönetilen kimlik avantajları:

* Azure sistem tarafından yönetilen x509 sertifikası ortak anahtar şifrelemesi kimlik doğrulaması.
* İstemci gizli dizileri yerine x509 sertifikalarıyla Azure AD güvenliği.
* Azure, yönetilen kimlik kaynağıyla ilişkili tüm sertifikaları yönetir ve yeniler.
* Azure Key Vault gibi güvenli bir gizli dizi hizmeti gereksinimini kaldırarak Basitleştirilmiş kimlik bilgisi operasyonel yönetimi. 

### <a name="daemon-hosted-on-non-azure-resources"></a>Azure dışı kaynaklarda barındırılan Daemon

Azure olmayan bir ortamda Yönetilen kimlikler üzerinde çalışırken, kullanılabilir değildir. Bu nedenle, Daemon uygulaması için bir Azure AD uygulama kaydı aracılığıyla bir hizmet sorumlusu yapılandırmanız gerekir.

1. Azure Portal Azure hizmetleri listesinde,   >    >  **Yeni kayıt** uygulama kayıtları Azure Active Directory ' ni seçin.  

    > [!div class="mx-imgBorder"]
    > ![Uygulama kaydı](./media/how-to-manage-authentication/app-registration.png)

2. Uygulamanızı zaten kaydolduysanız bir sonraki adımla devam edin. Uygulamanızı kaydolmadıysanız, bir **ad** girin, bir **destek hesabı türü** seçin ve ardından **Kaydet**' i seçin.  

    > [!div class="mx-imgBorder"]
    > ![Uygulama kaydı ayrıntıları](./media/how-to-manage-authentication/app-create.png)

3. Azure haritalar 'a temsil edilen API izinleri atamak için uygulamaya gidin. **Uygulama kayıtları** altında, **API izinleri**  >  **izin Ekle**' yi seçin. **Kuruluşumun kullandığı API 'ler** altında **Azure haritaları**' nı arayıp seçin.

    > [!div class="mx-imgBorder"]
    > ![Uygulama API 'SI izinleri ekleme](./media/how-to-manage-authentication/app-permissions.png)

4. **Azure haritalar**' ın yanındaki onay kutusunu işaretleyin ve ardından **izin Ekle**' yi seçin.

    > [!div class="mx-imgBorder"]
    > ![Uygulama API 'SI izinlerini seçin](./media/how-to-manage-authentication/select-app-permissions.png)

5. Bir istemci parolası oluşturmak veya sertifikayı yapılandırmak için aşağıdaki adımları izleyin.

    * Uygulamanız sunucu veya uygulama kimlik doğrulaması kullanıyorsa, uygulama kayıt sayfanızda, **sertifikalar & parolalar**' a gidin. Sonra da bir ortak anahtar sertifikası yükleyin veya **yeni istemci parolası**' nı seçerek bir parola oluşturun.

        > [!div class="mx-imgBorder"]
        > ![İstemci parolası oluşturma](./media/how-to-manage-authentication/app-keys.png)

    * **Ekle**' yi seçtikten sonra, parolayı kopyalayın ve Azure Key Vault gibi bir hizmette güvenli bir şekilde saklayın. Sertifikayı veya gizli anahtarı güvenli bir şekilde depolamak için [Azure Key Vault geliştirici kılavuzunu](../key-vault/general/developers-guide.md) gözden geçirin. Azure AD 'den belirteçleri almak için bu gizli anahtarı kullanacaksınız.

        > [!div class="mx-imgBorder"]
        > ![İstemci parolası ekleme](./media/how-to-manage-authentication/add-key.png)

### <a name="grant-role-based-access-for-the-daemon-application-to-azure-maps"></a>Azure Maps 'a Daemon uygulaması için rol tabanlı erişim verme

Oluşturulan yönetilen kimliği veya hizmet sorumlusunu bir veya daha fazla Azure Maps rol tanımına atayarak *Azure rol tabanlı erişim denetimi (Azure RBAC)* verirsiniz. Azure haritalar için kullanılabilen Azure rol tanımlarını görüntülemek için **erişim denetimi 'ne (IAM)** gidin. **Roller**' i seçin ve ardından *Azure Maps* ile başlayan roller için arama yapın. Bu Azure Maps rolleri, erişim sağlayabilmeniz için kullanabileceğiniz rollerdir.

> [!div class="mx-imgBorder"]
> ![Kullanılabilir rolleri görüntüle](./media/how-to-manage-authentication/how-to-view-avail-roles.png)

1. **Azure haritalar hesabınıza** gidin. **Erişim denetimi (IAM)** > **Rol atamaları**’nı seçin.

    > [!div class="mx-imgBorder"]
    > ![Azure RBAC kullanarak erişim verme](./media/how-to-manage-authentication/how-to-grant-rbac.png)

2. **Rol atamaları** sekmesinde bir rol ataması **ekleyin** . 

    > [!div class="mx-imgBorder"]
    > ![Ekran görüntüsü Ekle seçili olan toplama atamalarını gösterir.](./media/how-to-manage-authentication/add-role-assignment.png)

3. **Azure haritalar veri okuyucusu** veya **Azure haritalar veri katılımcısı** gibi yerleşik bir Azure Maps rol tanımı seçin. **Erişim ata**' nın altında, **Kullanıcı tarafından atanan yönetilen kimlik** sistemine atanmış yönetilen kimlik ile **Azure AD Kullanıcı, Grup veya hizmet sorumlusu** veya yönetilen kimlik ' i seçin  /  . Sorumluyu seçin. Sonra **Kaydet**'i seçin.

    > [!div class="mx-imgBorder"]
    > ![Rol ataması ekleme](./media/how-to-manage-authentication/how-to-add-role-assignment.png)

4. Rol atamasının rol atama sekmesinde uygulandığını doğrulayabilirsiniz.

## <a name="request-token-with-managed-identity"></a>Yönetilen kimliğe sahip istek belirteci

Barındırma kaynağı için yönetilen bir kimlik yapılandırıldıktan sonra Azure SDK 'sını kullanın veya Azure Maps için bir belirteç almak üzere REST API, [erişim belirteci alma](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)hakkında ayrıntılara bakın. Kılavuzun ardından, beklenmek, REST API isteklerinde kullanılabilecek bir erişim belirtecinin döndürüldüğünden olacaktır.

## <a name="request-token-with-application-registration"></a>Uygulama kaydıyla istek belirteci

Uygulamanızı kaydettikten ve Azure Maps ile ilişkilendirdikten sonra, erişim belirteçleri isteyebilirsiniz.

* Azure AD kaynak KIMLIĞI `https://atlas.microsoft.com/`
* Azure AD Uygulaması KIMLIĞI
* Azure AD Kiracı Kimliği
* Kayıt istemci parolasını Azure AD Uygulaması

İstek:

```http
POST /<Azure AD Tenant ID>/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=<Azure AD App ID>&resource=https://atlas.microsoft.com/&client_secret=<client secret>&grant_type=client_credentials
```

Yanıt:

```json
{
    "token_type": "Bearer",
    "expires_in": "...",
    "ext_expires_in": "...",
    "expires_on": "...",
    "not_before": "...",
    "resource": "https://atlas.microsoft.com/",
    "access_token": "ey...gw"
}
```

Daha ayrıntılı örnekler için bkz. [Azure AD Için kimlik doğrulama senaryoları](../active-directory/develop/authentication-vs-authorization.md).

## <a name="next-steps"></a>Sonraki adımlar

Azure haritalar hesabınız için API kullanım ölçümlerini bulun:
> [!div class="nextstepaction"]
> [Kullanım ölçümlerini görüntüleme](how-to-view-api-usage.md)

Azure AD 'nin Azure Maps ile nasıl tümleştirileceğini gösteren örnekleri araştırma:
> [!div class="nextstepaction"]
> [Azure haritalar örnekleri](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)