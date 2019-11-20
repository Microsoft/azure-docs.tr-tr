---
title: Azure blok zincirini dağıtma çalışma ekranı önizlemesi
description: Azure blok zinciri çalışma ekranı önizlemesi nasıl dağıtılır
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 09/05/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: brendal
manager: femila
ms.openlocfilehash: 2ea18c784c6b5cf61013c131360d20349e67b1e5
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/10/2019
ms.locfileid: "70845271"
---
# <a name="deploy-azure-blockchain-workbench-preview"></a>Azure blok zincirini dağıtma çalışma ekranı önizlemesi

Azure blok zinciri çalışma ekranı önizlemesi, Azure Marketi 'ndeki bir çözüm şablonu kullanılarak dağıtılır. Şablon, blok zinciri uygulamaları oluşturmak için gereken bileşenlerin dağıtımını basitleştirir. Bir kez dağıtıldıktan sonra, blok zinciri çalışma ekranı, Kullanıcı ve blok zinciri uygulamaları oluşturmak ve yönetmek için istemci uygulamalarına erişim sağlar.

Blok zinciri çalışma ekranı bileşenleri hakkında daha fazla bilgi için bkz. [Azure blok zinciri çalışma ekranı mimarisi](architecture.md).

[!INCLUDE [Preview note](./includes/preview.md)]

## <a name="prepare-for-deployment"></a>Dağıtıma hazırlanma

Blok zinciri çalışma ekranı, blok zinciri tabanlı bir uygulama oluşturmak için en sık kullanılan bir dizi ilgili Azure hizmeti ile birlikte bir blok zinciri defteri dağıtmanızı sağlar. Blockzincirine yönelik çalışma ekranı dağıtımı, Azure aboneliğinizdeki bir kaynak grubu içinde sağlanan aşağıdaki Azure hizmetlerine neden olur.

* App Service planı (Standart)
* Application Insights
* Event Grid
* Azure Key Vault
* Service Bus
* SQL veritabanı (Standart S0) + SQL mantıksal sunucusu
* Azure depolama hesabı (Standart LRS)
* 1 kapasiteye sahip sanal makine ölçek kümesi
* Sanal ağ kaynak grubu (Load Balancer, ağ güvenlik grubu, genel IP adresi, sanal ağ)
* Azure blok zinciri hizmeti. Önceki bir blok zinciri çalışma ekranı dağıtımı kullanıyorsanız Azure blok zinciri hizmetini kullanmak için Azure blok zinciri çalışma ekranı ' nu yeniden dağıtmaya dikkat edin.

Aşağıda, **myblockzincirin** kaynak grubunda oluşturulan örnek bir dağıtım verilmiştir.

![Örnek dağıtım](media/deploy/example-deployment.png)

Blok zinciri çalışma ekranının maliyeti, temel alınan Azure hizmetlerinin maliyetinin toplamıdır. Azure hizmetleri fiyatlandırma bilgileri, [Fiyatlandırma hesaplayıcısı](https://azure.microsoft.com/pricing/calculator/)kullanılarak hesaplanabilir.

## <a name="prerequisites"></a>Önkoşullar

Azure blok zinciri çalışma ekranı, Azure AD yapılandırması ve uygulama kayıtları gerektirir. Dağıtımdan önce Azure AD [yapılandırmalarının el ile](#azure-ad-configuration) veya bir betik dağıtımı çalıştırmadan önce bu yapılandırmayı seçebilirsiniz. Blok zinciri çalışma ekranı 'nı yeniden dağıtıyorsanız Azure [ad yapılandırması ' na bakın.](#azure-ad-configuration)

> [!IMPORTANT]
> Çalışma ekranının, bir Azure AD uygulamasını kaydetmek için kullandığınız kiracı ile aynı kiracıya dağıtılması gerekmez. Çalışma ekranı, kaynakları dağıtmak için yeterli izinlere sahip olduğunuz bir kiracıda dağıtılmalıdır. Azure AD kiracılar hakkında daha fazla bilgi için bkz. [Active Directory kiracı alma](../../active-directory/develop/quickstart-create-new-tenant.md) ve [uygulamaları Azure Active Directory tümleştirme](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md).

## <a name="deploy-blockchain-workbench"></a>Blok zinciri dağıtma çalışma ekranı

Önkoşul adımları tamamlandıktan sonra, blok zinciri çalışma ekranına dağıtmaya hazırlanın. Aşağıdaki bölümler Framework 'ün nasıl dağıtılacağını özetler.

1. [Azure Portal](https://portal.azure.com) oturum açın.
2. Sağ üst köşede hesabınızı seçin ve Azure blok zinciri 'ni dağıtmak istediğiniz Azure AD kiracısına geçiş yapın.
3. Sol bölmede **kaynak oluştur**' u seçin. Market arama çubuğunda arama yapın. `Azure Blockchain Workbench` 

    ![Market arama çubuğu](media/deploy/marketplace-search-bar.png)

4. **Azure blok zinciri çalışma ekranı**' nı seçin.

    ![Market arama sonuçları](media/deploy/marketplace-search-results.png)

5. **Oluştur**’u seçin.
6. Temel ayarları doldurun.

    ![Azure blok zinciri oluşturma çalışma ekranı](media/deploy/blockchain-workbench-settings-basic.png)

    | Ayar | Açıklama  |
    |---------|--------------|
    | Kaynak ön eki | Dağıtımınız için kısa benzersiz tanımlayıcı. Bu değer, kaynak adlandırma için temel olarak kullanılır. |
    | VM Kullanıcı adı | Kullanıcı adı tüm sanal makineler (VM) için yönetici olarak kullanılır. |
    | Kimlik doğrulaması türü | VM 'lere bağlanmak için bir parola veya anahtar kullanmak istiyorsanız seçin. |
    | istemcisiyle yönetilen bir cihaz için) | Parola, VM 'lere bağlanmak için kullanılır. |
    | SSH | **Ssh-rsa** ile başlayan tek satırlı BIÇIMDEKI bir RSA ortak anahtarı kullanın veya çok satırlı ped biçimini kullanın. Linux ve OS X veya Windows `ssh-keygen` üzerinde PuTTYgen kullanarak SSH anahtarları oluşturabilirsiniz. SSH anahtarları hakkında daha fazla bilgi için bkz. [Azure 'Da Windows Ile SSH anahtarlarını kullanma](../../virtual-machines/linux/ssh-from-windows.md). |
    | Veritabanı ve blok zinciri parolası | Dağıtımın bir parçası olarak oluşturulan veritabanına erişim için kullanılacak parolayı belirtin. Parola aşağıdaki dört gereksinimden üçünü karşılamalıdır: uzunluğun 12 & 72 karakter, 1 küçük harf karakter, 1 büyük harf karakter, 1 sayı ve 1 özel karakter (sayı işareti (#), yüzde (%), virgül (,), yıldız (*), arka tırnak işareti içinde olması gerekir. (\`), çift tırnak ("), tek tırnak ('), tire (-) ve semicolumn (;) |
    | Dağıtım Bölgesi | Blok zinciri çalışma ekranı kaynaklarının nereye dağıtılacağını belirtin. En iyi kullanılabilirlik için bu, **konum** ayarıyla eşleşmelidir. |
    | Subscription | Dağıtımınız için kullanmak istediğiniz Azure aboneliğini belirtin. |
    | Kaynak grupları | **Yeni oluştur** ' a tıklayarak yeni bir kaynak grubu oluşturun ve benzersiz bir kaynak grubu adı belirtin. |
    | Location | Çerçeveyi dağıtmak istediğiniz bölgeyi belirtin. |

7. Temel ayar yapılandırma bölümünü sona erdirin **Tamam ' ı** seçin.

8. **Gelişmiş ayarlar**' da, yeni bir blok zinciri ağı oluşturmak veya var olan yetkili bir blok zinciri ağını kullanmak istediğinizi seçin.

    **Yeni oluştur**:

    *Yeni oluştur* seçeneği, varsayılan temel SKU Ile bir Azure blok zinciri hizmeti çekirdek defteri dağıtır.

    ![Yeni blok zinciri ağı için Gelişmiş ayarlar](media/deploy/advanced-blockchain-settings-new.png)

    | Ayar | Açıklama  |
    |---------|--------------|
    | Azure blok zinciri hizmeti Fiyatlandırma Katmanı | Blok zinciri çalışma ekranı için kullanılan **temel** veya **Standart** Azure blok zinciri hizmet katmanını seçin |
    | Azure Active Directory ayarları | **Daha sonra Ekle**' yi seçin.</br>Not: [Azure AD 'yi önceden yapılandırmayı](#azure-ad-configuration) veya yeniden dağıtımını seçerseniz, *Şimdi eklemeyi*seçin. |
    | VM seçimi | Blok zinciri ağınız için tercih edilen depolama performansı ve VM boyutu ' nu seçin. Azure Ücretsiz katmanı gibi düşük hizmet limitlerine sahip bir aboneliğiniz varsa *Standart DS1 v2* gibi daha küçük bir VM boyutu seçin. |

    **Mevcut olanı kullan**:

    *Mevcut olanı kullan* seçeneği, bir Ethereum yetki kanıtı (POA) blok zinciri ağı belirtmenize olanak tanır. Uç noktalar aşağıdaki gereksinimlere sahiptir.

   * Uç nokta, bir Ethereum yetki kanıtı (PoA) blok zinciri ağı olmalıdır.
   * Uç noktanın ağ üzerinden genel olarak erişilebilir olması gerekir.
   * PoA blok zinciri ağı, gaz fiyatının sıfıra ayarlanmış olacak şekilde yapılandırılmalıdır.

     > [!NOTE]
     > Blok zinciri çalışma ekranı hesapları komik değildir. Fonlar gerekliyse, işlemler başarısız olur.

     ![Mevcut blok zinciri ağı için Gelişmiş ayarlar](media/deploy/advanced-blockchain-settings-existing.png)

     | Ayar | Açıklama  |
     |---------|--------------|
     | Ethereum RPC uç noktası | Mevcut bir PoA blok zinciri ağının RPC uç noktasını sağlayın. Uç nokta https://veya http://ile başlar ve bir bağlantı noktası numarasıyla biter. Örneğin, `http<s>://<network-url>:<port>` |
     | Azure Active Directory ayarları | **Daha sonra Ekle**' yi seçin.</br>Not: [Azure AD 'yi önceden yapılandırmayı](#azure-ad-configuration) veya yeniden dağıtımını seçerseniz, *Şimdi eklemeyi*seçin. |
     | VM seçimi | Blok zinciri ağınız için tercih edilen depolama performansı ve VM boyutu ' nu seçin. Azure Ücretsiz katmanı gibi düşük hizmet limitlerine sahip bir aboneliğiniz varsa *Standart DS1 v2* gibi daha küçük bir VM boyutu seçin. |

9. Gelişmiş ayarları bitirirecek **Tamam ' ı** seçin.

10. Parametrelerinizin doğru olduğunu doğrulamak için Özeti gözden geçirin.

    ![Özet](media/deploy/blockchain-workbench-summary.png)

11. Koşulları kabul etmek ve Azure blok zinciri çalışma sunucunuzu dağıtmak için **Oluştur** ' u seçin.

Dağıtım 90 dakikaya kadar sürebilir. İlerlemeyi izlemek için Azure portal kullanabilirsiniz. Yeni oluşturulan kaynak grubunda, dağıtılan yapıtların durumunu görmek için **dağıtımlar > genel bakış** ' ı seçin.

> [!IMPORTANT]
> Dağıtım sonrası, Active Directory ayarları ' nı doldurmanız gerekir. **Daha sonra Ekle**' yi seçerseniz, [Azure AD yapılandırma betiğini](#azure-ad-configuration-script)çalıştırmanız gerekir.  **Şimdi Ekle**' yi seçerseniz, [yanıt URL 'sini yapılandırmanız](#configuring-the-reply-url)gerekir.

## <a name="blockchain-workbench-web-url"></a>Blok zinciri Web URL 'SI

Blok zinciri çalışma ekranı dağıtımı tamamlandıktan sonra, yeni bir kaynak grubu blok zinciri çalışma ekranı kaynaklarınızı içerir. Blok zinciri çalışma ekranı hizmetlerine bir Web URL 'SI üzerinden erişilir. Aşağıdaki adımlarda, dağıtılan çerçevenin Web URL 'sini alma yöntemi gösterilmektedir.

1. [Azure Portal](https://portal.azure.com) oturum açın.
2. Sol taraftaki Gezinti bölmesinde **kaynak grupları** ' nı seçin.
3. Blok zinciri çalışma ekranı dağıtımı sırasında belirttiğiniz kaynak grubu adını seçin.
4. Listeyi türe göre alfabetik olarak sıralamak için **tür** sütun başlığını seçin.
5. **App Service**türünde iki kaynak vardır. "-Api" soneki *olmadan* App Service türünde kaynağı seçin.

    ![App Service listesi](media/deploy/resource-group-list.png)

6. App Service **Essentials** bölümünde, dağıtılan blok zinciri çalışma EKRANıNA Web URL 'sini temsil eden **URL** değerini kopyalayın.

    ![App Service temelleri](media/deploy/app-service.png)

Özel bir etki alanı adını blok zinciri çalışma ekranı ile ilişkilendirmek için, [Traffic Manager kullanarak Azure App Service Web uygulaması için özel etki alanı adı yapılandırma](../../app-service/web-sites-traffic-manager-custom-domain-name.md)konusuna bakın.

## <a name="azure-ad-configuration-script"></a>Azure AD yapılandırma betiği

Blok zinciri çalışma ekranı dağıtımınızı tamamlayacak Azure AD 'nin yapılandırılması gerekir. Yapılandırmayı yapmak için bir PowerShell betiği kullanacaksınız.

1. Bir tarayıcıda, [blok zinciri çalışma ekranı web URL](#blockchain-workbench-web-url)'sine gidin.
2. Cloud Shell kullanarak Azure AD ayarlama yönergelerini görürsünüz. Komutu kopyalayın ve Cloud Shell başlatın.

    ![AAD betiğini Başlat](media/deploy/launch-aad-script.png)

3. Blok zinciri çalışma ekranı 'nı dağıttığınız Azure AD kiracısını seçin.
4. Cloud Shell, komutunu yapıştırın ve çalıştırın.
5. İstendiğinde, blok zinciri çalışma ekranı için kullanmak istediğiniz Azure AD kiracısını girin. Bu, blok zinciri çalışma ekranı için kullanıcıları içeren kiracı olacaktır.

    > [!IMPORTANT]
    > Kimliği doğrulanmış kullanıcı, kiracıda Azure AD uygulama kayıtları oluşturmak ve yetkilendirilmiş uygulama izinleri vermek için izinler gerektirir. Kiracının yöneticisinden Azure AD yapılandırma betiğini çalıştırmasını veya yeni bir kiracı oluşturmasını isteyebilirsiniz.

    ![Azure AD kiracısını girin](media/deploy/choose-tenant.png)

6. Azure AD kiracısında bir tarayıcı kullanarak kimlik doğrulaması yapmanız istenir. Web URL 'sini bir tarayıcıda açın, kodu girin ve kimlik doğrulaması yapın.

    ![Kodla kimlik doğrulama](media/deploy/authenticate.png)

7. Betik birkaç durum iletisi verir. Kiracı başarıyla sağlandıysa **başarı** durumu iletisi alırsınız.
8. Blok zinciri çalışma ekranı URL 'sine gidin. Dizine okuma izinleri vermeyi onaylarınız istenir. Bu, blok zinciri Web uygulamasının Kiracıdaki kullanıcılara erişimini sağlar. Kiracı yöneticisiyseniz, kuruluşun tamamı için onay seçebilirsiniz. Bu seçenek, Kiracıdaki tüm kullanıcılar için onay kabul eder. Aksi takdirde, her kullanıcıdan blok zinciri çalışma ekranı Web uygulamasının ilk kullanımı için onay istenir.
9. Onay **kabul et** ' i seçin.

     ![Kullanıcı profillerini okuma onayı](media/deploy/graph-permission-consent.png)

10. Onay sonrasında, blok zinciri çalışma ekranı web uygulaması kullanılabilir.

## <a name="azure-ad-configuration"></a>Azure AD yapılandırması

Dağıtımdan önce Azure AD ayarlarını el ile yapılandırmayı veya doğrulamayı seçerseniz, bu bölümdeki tüm adımları izleyin. Azure AD ayarlarını otomatik olarak yapılandırmak isterseniz, blok zinciri çalışma ekranı 'nı dağıttıktan sonra [Azure AD yapılandırma betiği](#azure-ad-configuration-script) ' ni kullanın.

### <a name="blockchain-workbench-api-app-registration"></a>Blockchain Workbench API'si uygulama kaydı

Blok zinciri çalışma ekranı dağıtımı, bir Azure AD uygulamasının kaydedilmesini gerektirir. Uygulamayı kaydettirmek için bir Azure Active Directory (Azure AD) kiracısına sahip olmanız gerekir. Mevcut bir kiracıyı kullanabilir veya yeni bir kiracı oluşturabilirsiniz. Mevcut bir Azure AD kiracısı kullanıyorsanız, uygulamaları kaydetmek, Graph API izinleri vermek ve bir Azure AD kiracısı içinde Konuk erişimine izin vermek için yeterli izinlere sahip olmanız gerekir. Mevcut bir Azure AD kiracısında yeterli izinlere sahip değilseniz yeni bir kiracı oluşturun.


1. [Azure Portal](https://portal.azure.com) oturum açın.
2. Sağ üst köşede hesabınızı seçin ve istediğiniz Azure AD kiracısına geçiş yapın. Kiracı, çalışma sunucusunun dağıtıldığı aboneliğin abonelik kiracısı ve uygulama kaydetmek için yeterli izinlere sahip olması gerekir.
3. Sol taraftaki gezinti bölmesinde **Azure Active Directory** hizmetini seçin. Yeni **uygulama kayıtları** > **uygulama kaydı**' nı seçin.

    ![Uygulama kaydı](media/deploy/app-registration.png)

4. Uygulama için bir **ad** ve **oturum açma URL 'si** girin. Dağıtım sırasında değerler değiştiğinden bu yana yer tutucu değerlerini kullanabilirsiniz. 

    ![Uygulama kaydı oluştur](media/deploy/app-registration-create.png)

    |Ayar  | Value  |
    |---------|---------|
    |Name | `Blockchain API` |
    |Uygulama türü |Web uygulaması/API|
    |Oturum açma URL'si | `https://blockchainapi` |

5. Azure AD uygulamasını kaydetmek için **Oluştur** ' u seçin.

### <a name="modify-manifest"></a>Bildirimi Değiştir

Daha sonra, blok zinciri çalışma ekranı yöneticilerini belirtmek için bildirimi Azure AD içindeki uygulama rollerini kullanacak şekilde değiştirmeniz gerekir.  Uygulama bildirimleri hakkında daha fazla bilgi için bkz. [uygulama bildirimi Azure Active Directory](../../active-directory/develop/reference-app-manifest.md).

1. Kaydettiğiniz uygulama için, kayıtlı uygulama ayrıntıları bölmesinde **bildirim** ' ı seçin.
2. Bir GUID oluşturun. [GUID] PowerShell komutunu kullanarak bir GUID oluşturabilirsiniz:: NewGuid () veya New-GUID cmdlet 'i. Başka bir seçenek de GUID Oluşturucu Web sitesi kullanmaktır.
3. Bildirimin **Approles** bölümünü güncelleşeceğiz. Bildirim Düzenle bölmesinde **Düzenle** ' yi seçin ve belirtilen JSON `"appRoles": []` ile değiştirin. **Kimlik** alanı değerini, oluşturduğunuz GUID ile değiştirdiğinizden emin olun. 

    ![Bildirimi düzenle](media/deploy/edit-manifest.png)

    ``` json
    "appRoles": [
         {
           "allowedMemberTypes": [
             "User",
             "Application"
           ],
           "displayName": "Administrator",
           "id": "<A unique GUID>",
           "isEnabled": true,
           "description": "Blockchain Workbench administrator role allows creation of applications, user to role assignments, etc.",
           "value": "Administrator"
         }
       ],
    ```

    > [!IMPORTANT]
    > Blok zinciri çalışma ekranı yöneticilerini belirlemek için **yönetici** değeri gereklidir.

4. Bildirimde ayrıca **Oauth2AllowImplicitFlow** değerini **true**olarak değiştirin.

    ``` json
    "oauth2AllowImplicitFlow": true,
    ```

5. Bildirim değişikliklerini kaydetmek için **Kaydet** ' i seçin.

### <a name="add-graph-api-required-permissions"></a>Gerekli Graph API izinleri ekleme

API uygulamasının dizine erişmesi için kullanıcıdan izin istemesi gerekir. API uygulaması için aşağıdaki gerekli izni ayarlayın:

1. Blok zinciri API 'SI uygulama kaydında **ayarlar > gerekli izinler ' i seçin > BIR apı > Microsoft Graph seçin**.

    ![Bir API seçin](media/deploy/client-app-select-api.png)

    Tıklayın **seçin**.

2. **Temsilci izinleri**altında **erişimi etkinleştir** ' de, **tüm kullanıcıların temel profillerini oku '** nı seçin.

    ![Erişimi etkinleştir](media/deploy/client-app-read-perms.png)

    **Kaydet** ' i seçin ve **bitti**' yi seçin.

3. **Gerekli izinler**' de, **izin ver** ' i seçin ve doğrulama istemi için **Evet** ' i seçin.

   ![İzinleri verme](media/deploy/client-app-grant-permissions.png)

   İzin verilmesi, blok zinciri çalışma ekranının dizindeki kullanıcılara erişmesine izin verir. Okuma izni, blok zinciri çalışma ekranına üye aramak ve eklemek için gereklidir.

### <a name="get-application-id"></a>Uygulama KIMLIĞINI al

Dağıtım için uygulama KIMLIĞI ve kiracı bilgileri gereklidir. Dağıtım sırasında kullanılacak bilgileri toplayın ve depolayın.

1. Kaydettiğiniz uygulama için **Ayarlar** > **Özellikler**' i seçin.
2. **Özellikler** bölmesinde, dağıtım sırasında daha sonra kullanmak üzere aşağıdaki değerleri kopyalayın ve saklayın.

    ![API uygulaması özellikleri](media/deploy/app-properties.png)

    | Depolanacak ayar  | Dağıtımda kullan |
    |------------------|-------------------|
    | Uygulama Kimliği | Azure Active Directory Kurulum > uygulama KIMLIĞI |

### <a name="get-tenant-domain-name"></a>Kiracı etki alanı adını al

Uygulamaların kaydedildiği Active Directory kiracı etki alanı adını toplayın ve depolayın. 

Sol taraftaki gezinti bölmesinde **Azure Active Directory** hizmetini seçin. **Özel etki alanı adları**'nı seçin. Etki alanı adını kopyalayıp depolayın.

![Etki alanı adı](media/deploy/domain-name.png)

### <a name="guest-user-settings"></a>Konuk Kullanıcı ayarları

Azure AD kiracınızda Konuk kullanıcılarınız varsa, blok zinciri çalışma ekranı Kullanıcı atamasının ve yönetiminin düzgün şekilde çalıştığından emin olmak için ek adımları izleyin.

1. Azure AD kiracınızı değiştirin ve **dış işbirliği ayarlarını yönetmek > Azure Active Directory > Kullanıcı ayarları**' nı seçin.
2. **Konuk Kullanıcı izinlerinin ayarlanması,** **Hayır**ile sınırlıdır.
    ![Dış işbirliği ayarları](media/deploy/user-collaboration-settings.png)

## <a name="configuring-the-reply-url"></a>Yanıt URL 'sini yapılandırma

Azure blok zinciri çalışma ekranı dağıtıldıktan sonra, dağıtılan blok zinciri çalışma ekranı web URL 'sinin Azure Active Directory (Azure AD) istemci uygulaması **yanıt URL** 'sini yapılandırmanız gerekir.

1. [Azure Portal](https://portal.azure.com) oturum açın.
2. Azure AD istemci uygulamasını kaydettiğiniz kiracıda olduğunuzu doğrulayın.
3. Sol taraftaki gezinti bölmesinde **Azure Active Directory** hizmetini seçin. **Uygulama kayıtları**'nı seçin.
4. Önkoşul bölümünde kaydettiğiniz Azure AD istemci uygulamasını seçin.
5. **Yanıt URL 'leri > ayarlar**' ı seçin.
6. Azure blok zinciri **Web URL 'Sini al** bölümünde aldığınız Azure blok zinciri çalışma ekranı dağıtımının ana Web URL 'sini belirtin. Yanıt URL 'SI ön `https://`ekine sahiptir. Örneğin, `https://myblockchain2-7v75.azurewebsites.net`

    ![Yanıt URL'leri](media/deploy/configure-reply-url.png)

7. İstemci kaydını güncelleştirmek için **Kaydet** ' i seçin.

## <a name="remove-a-deployment"></a>Bir dağıtımı kaldır

Bir dağıtıma artık gerek kalmadığında, blok zinciri çalışma ekranı kaynak grubunu silerek bir dağıtımı kaldırabilirsiniz.

1. Azure portal sol gezinti bölmesindeki **kaynak grubu** ' na gidin ve silmek istediğiniz kaynak grubunu seçin. 
2. **Kaynak grubunu sil**'i seçin. Kaynak grubu adını girip **Sil**' i seçerek silmeyi doğrulayın.

    ![Kaynak grubunu sil](media/deploy/delete-resource-group.png)

## <a name="next-steps"></a>Sonraki adımlar

Bu nasıl yapılır makalesinde, Azure blok zinciri çalışma ekranı 'nı dağıttınız. Bir blok zinciri uygulaması oluşturmayı öğrenmek için, sonraki nasıl yapılır makalesine ilerleyin.

> [!div class="nextstepaction"]
> [Azure blok zinciri çalışma ekranı 'nda bir blok zinciri uygulaması oluşturma](create-app.md)
