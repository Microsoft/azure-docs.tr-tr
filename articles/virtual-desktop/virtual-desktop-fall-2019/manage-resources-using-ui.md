---
title: Yönetim aracını bir Azure Resource Manager şablonuyla dağıtma-Azure
description: Windows sanal masaüstü (klasik) kaynaklarını yönetmek için bir Azure Resource Manager şablonuyla Kullanıcı arabirimi aracı nasıl yüklenir.
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 1d562c483b340bee5f1c1aa5f63c068de6f54e42
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106444403"
---
# <a name="deploy-a-windows-virtual-desktop-classic-management-tool-with-an-azure-resource-manager-template"></a>Bir Azure Resource Manager şablonuyla Windows sanal masaüstü (klasik) Yönetim Aracı dağıtma

>[!IMPORTANT]
>Bu içerik, Windows sanal masaüstü nesneleri Azure Resource Manager desteklemeyen Windows sanal masaüstü (klasik) için geçerlidir.

Bu makaledeki yönergeler, Azure Resource Manager şablonu kullanarak Kullanıcı arabirimini nasıl dağıtacağınızı bildirir.

## <a name="important-considerations"></a>Önemli noktalar

Uygulamanın Windows sanal masaüstü ile etkileşime girmesine izin gerektirdiğinden, bu araç Işletmeler arası (B2B) senaryolarını desteklemez. Her Azure Active Directory (AAD) kiracının aboneliğinin kendi ayrı yönetim aracı dağıtımına ihtiyacı olacaktır.

Bu yönetim aracı bir örnektir. Microsoft, önemli güvenlik ve kalite güncelleştirmeleri sağlayacaktır. [Kaynak kodu GitHub ' da kullanılabilir](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy). Müşterilerin ve iş ortaklarının, aracı iş ihtiyaçlarına uyacak şekilde özelleştirmesi önerilir.

Aşağıdaki tarayıcılar yönetim aracıyla uyumludur:
- Google Chrome 68 veya üzeri
- Microsoft Edge 40,15063 veya üzeri
- Mozilla Firefox 52,0 veya üzeri
- Safari 10 veya üzeri (yalnızca macOS)

## <a name="what-you-need-to-deploy-the-management-tool"></a>Yönetim aracını dağıtmak için gerekenler

Yönetim aracını dağıtmadan önce, bir uygulama kaydı oluşturmak ve yönetim kullanıcı arabirimini dağıtmak için bir Azure Active Directory (Azure AD) kullanıcısına sahip olmanız gerekir. Bu Kullanıcı şunları sağlamalıdır:

- Azure AD Multi-Factor Authentication (MFA) devre dışı
- Azure aboneliğinizde kaynak oluşturma izniniz var
- Azure AD uygulaması oluşturma izniniz vardır. [Gerekli izinlere](../../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)sahip olan yönergeleri izleyerek, kullanıcılarınızın gerekli izinlere sahip olup olmadığını denetlemek için bu adımları izleyin.

Yönetim aracını dağıttıktan ve yapılandırdıktan sonra, her şeyin çalıştığından emin olmak için kullanıcıdan yönetim kullanıcı arabirimini başlatması gerektiğini öneririz. Yönetim Kullanıcı arabirimini başlatan kullanıcının Windows sanal masaüstü kiracı 'sını görüntülemesine veya düzenlemesine izin veren bir rol ataması olmalıdır.

## <a name="deploy-the-management-tool"></a>Yönetim aracını dağıtma

Başlamadan önce, temsil edilen Azure Active Directory (AAD) için [Windows sanal masaüstü onay sayfasını](https://rdweb.wvd.microsoft.com) ziyaret ederek sunucu ve istemci uygulamalarının onay aldığından emin olun.

Azure Kaynak Yönetimi şablonunu dağıtmak için aşağıdaki yönergeleri izleyin:

1. [GitHub Azure RDS-Templates sayfasına](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy)gidin.
2. Şablonu Azure’a dağıtın.
    - Kurumsal abonelikte dağıtım yapıyorsanız, aşağı kaydırın ve **Azure 'A dağıt**' ı seçin.
    - ' Yi bir bulut çözümü sağlayıcısı aboneliğine dağıtıyorsanız, Azure 'a dağıtmak için aşağıdaki yönergeleri izleyin:
        1. Aşağı kaydırın ve **Azure 'A dağıt**' a sağ tıkladıktan sonra **bağlantı konumunu Kopyala**' yı seçin.
        2. Not Defteri gibi bir metin Düzenleyicisi açın ve bağlantıyı buraya yapıştırın.
        3. <https://portal.azure.com/>Diyez etiketinden sonra ve önce (#), bir at işareti (@) ve ardından kiracı etki alanı adını girin. Şöyle bir örnek: <https://portal.azure.com/@Contoso.onmicrosoft.com#create/> .
        4. Bulut çözümü sağlayıcısı aboneliğine yönetici/katkıda bulunan izinleri olan bir kullanıcı olarak Azure portal oturum açın.
        5. Metin düzenleyicisine kopyaladığınız bağlantıyı adres çubuğuna yapıştırın.
3. Parametreleri girerken şunları yapın:
    - **Isserviceprincipal** parametresi için **false**' ı seçin.
    - Kimlik bilgileri için Multi-Factor Authentication devre dışı olan Azure AD kimlik bilgilerinizi girin. Bu kimlik bilgileri, Azure AD uygulaması ve Azure kaynakları oluşturmak için kullanılacaktır. Daha fazla bilgi edinmek için [Yönetim Aracı 'nı dağıtmak için](#what-you-need-to-deploy-the-management-tool)gerekenler bölümüne bakın.
    - **ApplicationName** için, uygulamanız için Azure Active Directory kaydedilecek benzersiz bir ad kullanın. Bu ad Web uygulaması URL 'SI için de kullanılacaktır. Örneğin, "Apr3UX" gibi bir ad kullanabilirsiniz.
4. Parametreleri girdikten sonra hüküm ve koşulları kabul edin ve **satın al**' ı seçin.

## <a name="provide-consent-for-the-management-tool"></a>Yönetim aracı için onay sağlayın

GitHub Azure Resource Manager şablonu tamamlandıktan sonra, Azure portal bir App Service planıyla birlikte iki uygulama hizmeti içeren bir kaynak grubu bulacaksınız.

Oturum açmadan önce yönetim aracını kullanmadan önce, yönetim aracı ile ilişkili yeni Azure AD uygulaması için onay sağlamanız gerekir. Onay sağlanması, yönetim aracının, araçta oturum açmış olan kullanıcı adına Windows sanal masaüstü yönetimi çağrıları yapmasını sağlar.

> [!div class="mx-imgBorder"]
> ![UI yönetim aracına izin verdiğiniz zaman girilen izinleri gösteren ekran görüntüsü.](../media/management-ui-delegated-permissions.png)

Araçta oturum açmak için hangi kullanıcıyı kullanabileceğinizi öğrenmek için [Azure Active Directory Kullanıcı ayarları sayfanıza](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/) gidin ve **kullanıcıların kendi adına şirket verilerine erişen uygulamalara izin verebilmeleri** için değeri göz önünde bulabilirsiniz.

> [!div class="mx-imgBorder"]
> ![Kullanıcıların yalnızca kendi kullanıcıları için izin verip veremediğini gösteren ekran görüntüsü.](../media/management-ui-user-consent-allowed.png)

- Değer **Evet** olarak ayarlanırsa, Azure Active Directory herhangi bir kullanıcı hesabıyla oturum açabilir ve yalnızca bu kullanıcı için onay sağlayabilirsiniz. Bununla birlikte, yönetim aracında daha sonra başka bir kullanıcıyla oturum açarsanız, aynı onayı tekrar gerçekleştirmeniz gerekir.
- Değer **Hayır** olarak ayarlanırsa, Azure Active Directory genel yönetici olarak oturum açmanız ve dizindeki tüm kullanıcılar için yönetici onayı sağlamanız gerekir. Başka hiçbir Kullanıcı onay istemi istemez.


İzin sağlamak için hangi kullanıcıyı kullanacağınızı belirledikten sonra, araca onay sağlamak için aşağıdaki yönergeleri izleyin:

1. Azure kaynaklarınıza gidin, şablonda verdiğiniz adı taşıyan Azure App Services kaynağını seçin (örneğin, Apr3UX) ve onunla ilişkili URL 'ye gidin. Örneğin,  `https://rdmimgmtweb-210520190304.azurewebsites.net` .
2. Uygun Azure Active Directory Kullanıcı hesabını kullanarak oturum açın.
3. Bir genel yönetici ile kimlik doğrulaması yaptıysanız, artık **kuruluşunuz adına izin** vermek için onay kutusunu işaretleyebilirsiniz. Onay sağlamak için **kabul et** ' i seçin.

   > [!div class="mx-imgBorder"]
   > ![Kullanıcının veya yöneticinin göreceği tam izin sayfasını gösteren ekran görüntüsü.](../media/management-ui-consent-page.png)

Bu işlem sizi yönetim aracına götürür.

## <a name="use-the-management-tool"></a>Yönetim aracını kullanma

Kuruluş için veya belirli bir kullanıcı için onay sağladıktan sonra yönetim aracına dilediğiniz zaman erişebilirsiniz.

Aracı başlatmak için aşağıdaki yönergeleri izleyin:

1. Şablonda belirttiğiniz adı taşıyan Azure App Services kaynağını seçin (örneğin, Apr3UX) ve onunla ilişkili URL 'ye gidin. Örneğin,  `https://rdmimgmtweb-210520190304.azurewebsites.net` .
2. Windows sanal masaüstü kimlik bilgilerinizi kullanarak oturum açın.
3. Bir kiracı grubu seçmeniz istendiğinde, açılan listeden **varsayılan kiracı grubu** ' nu seçin.
4. **Varsayılan kiracı grubu**' nu seçtiğinizde, pencerenizin sol tarafında bir menü görünür. Bu menüde, kiracı grubunuzun adını bulun ve seçin.

  > [!NOTE]
  > Özel bir kiracı grubunuz varsa, açılan listeden seçim yapmak yerine adı el ile girin.

## <a name="report-issues"></a>Sorun bildirme

Yönetim Aracı veya diğer Windows Sanal Masaüstü araçlarıyla ilgili herhangi bir sorun yaşıyorsanız, GitHub 'da raporlamak için [Uzak Masaüstü Hizmetleri Azure Resource Manager şablonlarındaki](https://github.com/Azure/RDS-Templates/blob/master/README.md) yönergeleri izleyin.

## <a name="next-steps"></a>Sonraki adımlar

Yönetim aracına nasıl dağıtılacağını ve bağlandığınızı öğrendiğinize göre, Azure hizmeti yardımı 'nı kullanarak hizmet sorunlarını ve sistem durumu Danışma belgelerini nasıl izleyeceğinizi öğrenebilirsiniz. Daha fazla bilgi edinmek için bkz. [hizmet uyarılarını ayarlama öğreticisi](set-up-service-alerts-2019.md).