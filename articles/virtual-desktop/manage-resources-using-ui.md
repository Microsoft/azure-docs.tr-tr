---
title: Yönetim aracını dağıtma-Azure
description: Windows sanal masaüstü kaynaklarını yönetmek için bir kullanıcı arabirimi aracı nasıl yüklenir.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 11/09/2019
ms.author: helohr
ms.openlocfilehash: c7ef648dd2610c337bc9146e7a52c04d91907c8e
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/10/2019
ms.locfileid: "73904904"
---
# <a name="tutorial-deploy-a-management-tool"></a>Öğretici: bir yönetim aracı dağıtın

Yönetim Aracı, Microsoft sanal masaüstü kaynaklarını yönetmek için bir kullanıcı arabirimi (UI) sağlar. Bu öğreticide, yönetim aracını dağıtmayı ve bu sunucuya bağlanmayı öğreneceksiniz.

>[!NOTE]
>Bu yönergeler, kuruluşunuzun mevcut işlemleriyle kullanılabilecek Windows sanal masaüstüne özgü bir yapılandırma içindir.

## <a name="important-considerations"></a>Önemli konular

Uygulamanın Windows sanal masaüstü ile etkileşime girmesine izin gerektirdiğinden, bu araç Işletmeler arası (B2B) senaryolarını desteklemez. Her Azure Active Directory (AAD) kiracının aboneliğinin kendi ayrı yönetim aracı dağıtımına ihtiyacı olacaktır.

Bu yönetim aracı bir örnektir. Microsoft, önemli güvenlik ve kalite güncelleştirmeleri sağlayacaktır. [Kaynak kodu GitHub ' da kullanılabilir](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy). Müşterilerin ve iş ortaklarının, aracı iş ihtiyaçlarına uyacak şekilde özelleştirmesi önerilir.

Aşağıdaki tarayıcılar yönetim aracıyla uyumludur:
- Google Chrome 68 veya üzeri
- Microsoft Edge 40,15063 veya üzeri
- Mozilla Firefox 52,0 veya üzeri
- Safari 10 veya üzeri (yalnızca macOS)

## <a name="what-you-need-to-run-the-azure-resource-manager-template"></a>Azure Resource Manager şablonunu çalıştırmak için gerekenler

Azure Resource Manager şablonu dağıtmadan önce, yönetim Kullanıcı ARABIRIMINI dağıtmak için Azure Active Directory bir kullanıcıya ihtiyacınız vardır. Bu Kullanıcı şunları sağlamalıdır:

- Azure Multi-Factor Authentication (MFA) devre dışı
- Azure aboneliğinizde kaynak oluşturma izniniz var
- Azure AD uygulaması oluşturma izniniz vardır. Kullanıcının [gerekli izinlere](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#required-permissions)sahip olup olmadığını denetlemek için bu adımları izleyin.

Azure Resource Manager şablonu dağıttıktan sonra, doğrulamak üzere yönetim kullanıcı arabirimini başlatmak isteyeceksiniz. Bu Kullanıcı şunları sağlamalıdır:
- Windows sanal masaüstü kiracınızı görüntülemek veya düzenlemek için bir rol ataması yapın

## <a name="run-the-azure-resource-manager-template-to-provision-the-management-ui"></a>Yönetim Kullanıcı arabirimini sağlamak için Azure Resource Manager şablonunu çalıştırma

Başlamadan önce, temsil edilen Azure Active Directory (AAD) için [Windows sanal masaüstü onay sayfasını](https://rdweb.wvd.microsoft.com) ziyaret ederek sunucu ve istemci uygulamalarının onay aldığından emin olun.

Azure Kaynak Yönetimi şablonunu dağıtmak için aşağıdaki yönergeleri izleyin:

1. [GitHub Azure RDS-templates sayfasına](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy)gidin.
2. Şablonu Azure 'a dağıtın.
    - Kurumsal abonelikte dağıtım yapıyorsanız, aşağı kaydırın ve **Azure 'A dağıt**' ı seçin. Bkz. [şablon parametreleri Için rehberlik](#guidance-for-template-parameters).
    - ' Yi bir bulut çözümü sağlayıcısı aboneliğine dağıtıyorsanız, Azure 'a dağıtmak için aşağıdaki yönergeleri izleyin:
        1. Aşağı kaydırın ve **Azure 'A dağıt**' a sağ tıkladıktan sonra **bağlantı konumunu Kopyala**' yı seçin.
        2. Not Defteri gibi bir metin Düzenleyicisi açın ve bağlantıyı buraya yapıştırın.
        3. <https://portal.azure.com/> ve diyez etiketinden (#) önce, bir at işareti (@) ve ardından kiracı etki alanı adını girin. Şöyle bir örnek: <https://portal.azure.com/@Contoso.onmicrosoft.com#create/>.
        4. Bulut çözümü sağlayıcısı aboneliğine yönetici/katkıda bulunan izinleri olan bir kullanıcı olarak Azure portal oturum açın.
        5. Metin düzenleyicisine kopyaladığınız bağlantıyı adres çubuğuna yapıştırın.

### <a name="guidance-for-template-parameters"></a>Şablon parametreleri Kılavuzu
Araç yapılandırmasına yönelik parametrelerin nasıl girilmesi aşağıda verilmiştir:

- Bu, RD Aracısı URL 'sidir: https:\//rdbroker.wvd.microsoft.com/
- Bu, kaynak URL 'sidir: https:\//mrs-prod.ame.gbl/mrs-RDInfra-prod
- Azure 'da oturum açmak için AAD kimlik bilgilerinizi MFA devre dışı olarak kullanın. [Azure Resource Manager şablonunu çalıştırmak Için gerekenler](#what-you-need-to-run-the-azure-resource-manager-template)bölümüne bakın.
- Yönetim aracı için Azure Active Directory kaydedilecek uygulama için benzersiz bir ad kullanın; Örneğin, Apr3UX.

## <a name="provide-consent-for-the-management-tool"></a>Yönetim aracı için onay sağlayın

GitHub Azure Resource Manager şablonu tamamlandıktan sonra, Azure portal bir App Service planıyla birlikte iki uygulama hizmeti içeren bir kaynak grubu bulacaksınız.

Oturum açmadan ve yönetim aracını kullanmadan önce, yönetim aracı ile ilişkili yeni Azure Active Directory uygulamasına izin vermeniz gerekir. Onay sunarak, yönetim aracının, araçta oturum açan kullanıcı adına Windows sanal masaüstü yönetimi çağrıları yapmasına izin vermiş olursunuz.

![UI yönetim aracına izin verdiğiniz zaman girilen izinleri gösteren ekran görüntüsü.](media/management-ui-delegated-permissions.png)

Araçta oturum açmak için hangi kullanıcıyı kullanabileceğinizi öğrenmek için [Azure Active Directory Kullanıcı ayarları sayfanıza](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/) gidin ve **kullanıcıların kendi adına şirket verilerine erişen uygulamalara izin verebilmeleri**için değeri göz önünde bulabilirsiniz.

![Kullanıcıların yalnızca kendi kullanıcıları için izin verip veremediğini gösteren ekran görüntüsü.](media/management-ui-user-consent-allowed.png)

- Değer **Evet**olarak ayarlanırsa, Azure Active Directory herhangi bir kullanıcı hesabıyla oturum açabilir ve yalnızca bu kullanıcı için onay sağlayabilirsiniz. Bununla birlikte, yönetim aracında daha sonra başka bir kullanıcıyla oturum açarsanız, aynı onayı tekrar gerçekleştirmeniz gerekir.
- Değer **Hayır**olarak ayarlanırsa, Azure Active Directory genel yönetici olarak oturum açmanız ve dizindeki tüm kullanıcılar için yönetici onayı sağlamanız gerekir. Başka hiçbir Kullanıcı onay istemi istemez.


İzin sağlamak için hangi kullanıcıyı kullanacağınızı belirledikten sonra, araca onay sağlamak için aşağıdaki yönergeleri izleyin:

1. Azure kaynaklarınıza gidin, şablonda verdiğiniz adı taşıyan Azure App Services kaynağını seçin (örneğin, Apr3UX) ve onunla ilişkili URL 'ye gidin. Örneğin, <https://rdmimgmtweb-210520190304.azurewebsites.net>.
2. Uygun Azure Active Directory Kullanıcı hesabını kullanarak oturum açın.
3. Bir genel yönetici ile kimlik doğrulaması yaptıysanız, artık **kuruluşunuz adına izin**vermek için onay kutusunu işaretleyebilirsiniz. Onay sağlamak için **kabul et** ' i seçin.
   
   ![Kullanıcının veya yöneticinin göreceği tam izin sayfasını gösteren ekran görüntüsü.](media/management-ui-consent-page.png)

Bu işlem sizi yönetim aracına götürür.

## <a name="use-the-management-tool"></a>Yönetim aracını kullanma

Kuruluş için veya belirli bir kullanıcı için onay sağladıktan sonra yönetim aracına dilediğiniz zaman erişebilirsiniz.

Aracı başlatmak için aşağıdaki yönergeleri izleyin:

1. Şablonda belirttiğiniz adı taşıyan Azure App Services kaynağını seçin (örneğin, Apr3UX) ve onunla ilişkili URL 'ye gidin. Örneğin, <https://rdmimgmtweb-210520190304.azurewebsites.net>.
2. Windows sanal masaüstü kimlik bilgilerinizi kullanarak oturum açın.
3. Bir kiracı grubu seçmeniz istendiğinde, açılan listeden **varsayılan kiracı grubu** ' nu seçin.
4. Varsayılan kiracı grubu ' nu seçtiğinizde, pencerenizin sağ tarafında bir menü görünür. Bu menüde, kiracı grubunuzun adını bulun ve seçin.

> [!NOTE]
> Özel bir kiracı grubunuz varsa, açılan listeden seçim yapmak yerine adı el ile girin.

## <a name="report-issues"></a>Sorunları raporla

Yönetim Aracı veya diğer Windows Sanal Masaüstü araçlarıyla ilgili herhangi bir sorunla karşılaşırsanız, GitHub 'da raporlamak için [Uzak Masaüstü Hizmetleri ARM şablonlarındaki](https://github.com/Azure/RDS-Templates/blob/master/README.md) yönergeleri izleyin.

## <a name="next-steps"></a>Sonraki adımlar

Yönetim aracına nasıl dağıtılacağını ve bağlandığınızı öğrendiğinize göre, hizmet sorunlarını ve sistem durumu belgelerini izlemek için Azure hizmet durumunu nasıl kullanacağınızı öğrenebilirsiniz.

> [!div class="nextstepaction"]
> [Hizmet uyarıları ayarlama öğreticisi](./set-up-service-alerts.md)
