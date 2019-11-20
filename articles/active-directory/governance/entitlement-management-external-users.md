---
title: Azure AD Yetkilendirme Yönetimi 'nde dış kullanıcılara erişimi yönetir-Azure Active Directory
description: Azure Active Directory yetkilendirme yönetiminde dış kullanıcılara erişimi yönetmek için belirtebileceğiniz ayarlar hakkında bilgi edinin.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/26/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: e76e5c5d2cfcfd983f2b5cdc279f0c13fa6706e4
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73832700"
---
# <a name="govern-access-for-external-users-in-azure-ad-entitlement-management"></a>Azure AD yetkilendirme yönetiminde dış kullanıcılara erişimi yönetir

Azure AD Yetkilendirme Yönetimi, kuruluşunuzun dışındaki kişilerle işbirliği yapmak için [Azure AD işletmeden işletmeye (B2B)](../b2b/what-is-b2b.md) kullanır. Azure AD B2B ile dış kullanıcılar kendi giriş dizinlerinde kimlik doğrular, ancak dizininizde bir temsili vardır. Dizininizdeki Gösterim, kullanıcıya kaynaklarınıza erişim atanmasına olanak sağlar.

Bu makalede, dış kullanıcılara erişimi yönetmek için belirtebileceğiniz ayarlar açıklanmaktadır.

## <a name="how-entitlement-management-can-help"></a>Yetkilendirme Yönetimi nasıl yardımcı olabilir

[Azure AD B2B](../b2b/what-is-b2b.md) davet deneyimini kullanırken, kaynak dizininize getirmek istediğiniz dış konuk kullanıcıların e-posta adreslerini zaten bilmeniz ve ile çalışmanız gerekir. Bu, daha küçük veya kısa süreli bir proje üzerinde çalışırken ve tüm katılımcıları zaten öğrendiğinizde harika bir şekilde çalışır, ancak birlikte çalışmak istediğiniz çok sayıda kullanıcınız varsa veya katılımcılar zaman içinde değişiyorlarsa yönetimi daha zordur.  Örneğin, başka bir kuruluşla çalışıyor ve bu kuruluşla tek bir iletişim noktasına sahip olabilirsiniz, ancak bu kuruluştan daha fazla ek kullanıcının da erişime ihtiyacı olacaktır.

Yetkilendirme Yönetimi sayesinde, belirttiğiniz kuruluşların kullanıcılarının bir erişim paketini kendi kendinize isteyebilmesini sağlayan bir ilke tanımlayabilirsiniz. Onayın gerekli olup olmadığını ve erişim için bir sona erme tarihi belirtebilirsiniz. Onay gerekiyorsa, dış kuruluştan bir veya daha fazla kullanıcıyı bir veya daha fazla kullanıcı için bir veya daha fazla kullanıcıyı kendi kuruluşlarından hangi dış kullanıcıların erişmesi gerektiğini öğrendiklerinden, bunları onaylayan olarak belirleyebilirsiniz. Erişim paketini yapılandırdıktan sonra, erişim paketinin bağlantısını dış kuruluşta kişi kişiniz (sponsor) gönderebilirsiniz. Bu iletişim, dış kuruluştaki diğer kullanıcılarla paylaşabilir ve bu bağlantıyı, erişim paketini istemek için kullanabilir. Bu kuruluştan, dizininizden zaten davet edilmiş olan kullanıcılar da bu bağlantıyı kullanabilir.

Bir istek onaylandığında, Yetkilendirme Yönetimi kullanıcıya gerekli erişimi sağlayacak ve bu kullanıcılar zaten dizininizden değilse kullanıcıyı davet edebilir. Azure AD, bunlar için otomatik olarak bir B2B Konuk hesabı oluşturur. Bir yöneticinin daha önce hangi kuruluşların işbirliği için izin verileceğini sınırlı olabileceğini, diğer kuruluşlara yönelik olarak bir [B2B izin verme veya reddetme listesi](../b2b/allow-deny-list.md) ayarlanmasına izin vermek veya engellemek için  Kullanıcıya izin verilenler veya engellenenler listesi tarafından izin verilmezse, bunlar davet edilmeyecektir.

Dış kullanıcının en son sonsuza kadar erişimini istemediğiniz için ilkede 180 gün gibi bir sona erme tarihi belirtirsiniz. 180 gün sonra, erişimleri genişletilmemişse, Yetkilendirme Yönetimi ilgili erişim paketiyle ilişkili tüm erişimi kaldırır. Varsayılan olarak, Yetkilendirme Yönetimi üzerinden davet edilen kullanıcının başka bir erişim paketi ataması yoksa, son atamalarını kaybederlerse, Konuk hesabının 30 gün boyunca oturum açması engellenir ve daha sonra kaldırılır. Bu, gereksiz hesapların uzamasını önler. Aşağıdaki bölümlerde açıklandığı gibi, bu ayarlar yapılandırılabilir.

## <a name="how-access-works-for-external-users"></a>Access 'in dış kullanıcılar için nasıl çalıştığı

Aşağıdaki diyagram ve adımlar, dış kullanıcılara bir erişim paketine erişim izni verme hakkında genel bakış sağlar.

![Dış kullanıcıların yaşam döngüsünü gösteren diyagram](./media/entitlement-management-external-users/external-users-lifecycle.png)

1. Azure AD dizini veya işbirliği yapmak istediğiniz etki alanı için [bağlı bir kuruluş eklersiniz](entitlement-management-organization.md) .

1. Dizininizde, [dizininizde olmayan kullanıcılar için](entitlement-management-access-package-create.md#for-users-not-in-your-directory)bir ilke içeren bir erişim paketi oluşturun.

1. Bir [erişim portalı bağlantısını](entitlement-management-access-package-settings.md) , kendi kullanıcılarına erişim paketi istemek için paylaştıkları dış kuruluştan gönderebilirsiniz.

1. Dış Kullanıcı (Bu örnekteki**Istek sahibi** ), erişim paketine [erişim istemek](entitlement-management-request-access.md) için erişim portalı bağlantısını kullanır. Kullanıcının oturum açması, bağlı kuruluşta tanımlanan dizinin veya etki alanının kimlik doğrulama türüne bağlıdır.

1. Onaylayan [, isteği onaylar](entitlement-management-request-approve.md) (veya istek otomatik olarak onaylandı).

1. İstek, [teslim durumuna](entitlement-management-process.md)geçer.

1. B2B davet işlemini kullanarak, dizininizde bir Konuk Kullanıcı hesabı (Bu örnekte**Istek sahibi a (konuk)** ) oluşturulur. Bir [izin verilenler listesi veya reddetme listesi](../b2b/allow-deny-list.md) tanımlanmışsa, liste ayarı uygulanır.

1. Konuk kullanıcıya erişim paketindeki tüm kaynaklara erişim atanır. Azure AD 'de ve diğer Microsoft Online Services ya da bağlı SaaS uygulamalarına yapılan değişikliklerin yapılması biraz zaman alabilir. Daha fazla bilgi için bkz. [değişiklikler uygulandığında](entitlement-management-access-package-resources.md#when-changes-are-applied).

1. Dış Kullanıcı, erişiminin [teslim](entitlement-management-process.md)edildiğini belirten bir e-posta alır.

1. Kaynaklara erişmek için dış kullanıcı e-postadaki bağlantıya tıklayabilir veya doğrudan davet işlemini tamamlamaya yönelik dizin kaynaklarından herhangi birine erişmeyi deneyebilir.

1. İlke ayarlarına bağlı olarak, zaman geçtikçe, dış Kullanıcı için erişim paketi atamasının süresi dolar ve dış kullanıcının erişimi kaldırılır.

1. Dış Kullanıcı ayarlarının yaşam döngüsüne bağlı olarak, dış kullanıcının artık herhangi bir erişim paketi ataması yoksa, dış kullanıcının oturum açması engellenir ve Konuk Kullanıcı hesabı dizininizden kaldırılır.

## <a name="settings-for-external-users"></a>Dış kullanıcılar için ayarlar

Kuruluşunuzun dışındaki kişilerin erişim paketleri isteyebildiğini ve bu erişim paketlerindeki kaynaklara erişim izni almasını sağlamak için doğrulamanız gereken bazı ayarlar düzgün bir şekilde yapılandırılmıştır.

### <a name="enable-catalog-for-external-users"></a>Dış kullanıcılar için kataloğu etkinleştir

- Varsayılan olarak, [Yeni bir katalog](entitlement-management-catalog-create.md)oluşturduğunuzda, dış kullanıcıların katalogda erişim paketleri istemesine izin vermek için etkinleştirilir. **Dış kullanıcılar Için etkinleştirildiğinden** emin olun, **Evet**olarak ayarlayın.

    ![Katalog ayarlarını Düzenle](./media/entitlement-management-shared/catalog-edit.png)

### <a name="configure-your-azure-ad-b2b-external-collaboration-settings"></a>Azure AD B2B dış işbirliği ayarlarınızı yapılandırın

- Konuklarınızın dizininize diğer konukları davet etmesini sağlamak, Konuk davetlerini yetkilendirme yönetiminin dışında gerçekleşebileceği anlamına gelir. Konukların yalnızca doğru şekilde yönetilen davetlere izin vermek için **Hayır** 'a **davet edebilmeleri** önerilir.
- B2B izin verilenler listesini kullanıyorsanız, Yetkilendirme Yönetimi 'ni kullanarak ortak hale getirmek istediğiniz etki alanının listeye eklendiğinden emin olmanız gerekir. Alternatif olarak, B2B reddetme listesini kullanıyorsanız, ortak hale getirmek istediğiniz herhangi bir etki alanının listeye eklenmemiş olduğundan emin olmanız gerekir.
- **Tüm kullanıcılar** için bir yetkilendirme yönetimi ilkesi oluşturursanız (tüm bağlı kuruluşlar + tüm yeni dış kullanıcılar), sahip olduğunuz tüm B2B izin verme veya reddetme listesi ayarları öncelikli olur. Bu nedenle, bir tane kullanıyorsanız, bu ilkeye dahil etmek istediğiniz etki alanlarını izin verilenler listenize dahil ettiğinizden emin olun ve reddetme listesi kullanıyorsanız bunları reddetme listenizden dışlayabilirsiniz.
- **Tüm kullanıcıları** (tüm bağlı kuruluşlar + tüm yeni dış kullanıcılar) içeren bir yetkilendirme yönetimi ilkesi oluşturmak istiyorsanız, öncelikle dizininiz için bir kerelik geçiş kodu kimlik doğrulamasını etkinleştirmeniz gerekir. Daha fazla bilgi için bkz. [e-posta bir kerelik geçiş kodu doğrulaması (Önizleme)](../b2b/one-time-passcode.md#opting-in-to-the-preview).
- Azure AD B2B dış işbirliği ayarları hakkında daha fazla bilgi için bkz. [B2B dış Işbirliğini etkinleştirme ve konukları kimlerin davet edebileceğini yönetme](../b2b/delegate-invitations.md).

    ![Azure AD dış işbirliği ayarları](./media/entitlement-management-external-users/collaboration-settings.png)

### <a name="review-your-conditional-access-policies"></a>Koşullu erişim ilkelerinizi gözden geçirin

- Yeni konuk kullanıcıların, dizininizde oturum açabilmelerini engelleyecek şekilde, yeni konuk kullanıcıların karşılayamayacak tüm koşullu erişim ilkelerinden konukları hariç tutdığınızdan emin olun. Örneğin, konukların kayıtlı bir cihazı yoktur, bilinen bir konumda değildir ve Multi-Factor Authentication (MFA) için yeniden kaydolmak istemezsiniz. bu nedenle, bu gereksinimlerin bir koşullu erişim ilkesinde eklenmesi, konukların yetkilendirme kullanmasını engeller yönetme. Daha fazla bilgi için bkz. [koşullu erişim Azure Active Directory koşullar nelerdir?](../conditional-access/conditions.md).

    ![Azure AD koşullu erişim ilkesi dışlama ayarları](./media/entitlement-management-external-users/conditional-access-exclude.png)

### <a name="review-your-sharepoint-online-external-sharing-settings"></a>SharePoint Online dış paylaşım ayarlarınızı gözden geçirin

- Dış kullanıcılara yönelik erişim paketlerinize SharePoint Online siteleri eklemek istiyorsanız, kuruluş düzeyinde dış paylaşım ayarınızdaki **herkesin** (oturum açma gerektirmez) veya **yeni ve mevcut konuklarınızın** (konukların oturum açması veya bir doğrulama kodu sağlaması gerekir) ayarlanmış olduğundan emin olun. Daha fazla bilgi için bkz. [dış Paylaşımı açma veya kapatma](https://docs.microsoft.com/sharepoint/turn-external-sharing-on-or-off#change-the-organization-level-external-sharing-setting).

- Yetkilendirme Yönetimi dışında herhangi bir dış paylaşımı kısıtlamak istiyorsanız, dış paylaşım ayarını **var olan konuklara**ayarlayabilirsiniz. Daha sonra, yalnızca Yetkilendirme Yönetimi üzerinden davet edilen yeni kullanıcılar bu sitelere erişim kazanabilecektir. Daha fazla bilgi için bkz. [dış Paylaşımı açma veya kapatma](https://docs.microsoft.com/sharepoint/turn-external-sharing-on-or-off#change-the-organization-level-external-sharing-setting).

- Site düzeyi ayarlarının konuk erişimi (daha önce listelenen seçenek seçimlerini de) etkinleştirdiğinizden emin olun. Daha fazla bilgi için bkz. [site için dış Paylaşımı açma veya kapatma](https://docs.microsoft.com/sharepoint/change-external-sharing-site).

### <a name="review-your-office-365-group-sharing-settings"></a>Office 365 grup Paylaşım ayarlarınızı gözden geçirin

- Dış kullanıcılara yönelik erişim paketlerinize Office 365 grupları eklemek istiyorsanız, Konuk erişimine izin vermek için **kullanıcıların kuruluşa yeni konuklar eklemesine izin** ver ' in **Açık** olarak ayarlandığından emin olun. Daha fazla bilgi için bkz. [Office 365 gruplarına konuk erişimini yönetme](https://docs.microsoft.com/office365/admin/create-groups/manage-guest-access-in-groups?view=o365-worldwide#manage-guest-access-to-office-365-groups).

- Dış kullanıcıların Office 365 grubuyla ilişkili SharePoint Online sitesine ve kaynaklarına erişmesini istiyorsanız SharePoint Online dış paylaşımını etkinleştirdiğinizden emin olun. Daha fazla bilgi için bkz. [dış Paylaşımı açma veya kapatma](https://docs.microsoft.com/sharepoint/turn-external-sharing-on-or-off#change-the-organization-level-external-sharing-setting).

- PowerShell 'in dizin düzeyinde Office 365 grupları için konuk ilkesinin nasıl ayarlanacağı hakkında bilgi için bkz. [örnek: gruplar Için Konuk ilkesini dizin düzeyinde yapılandırma](../users-groups-roles/groups-settings-cmdlets.md#example-configure-guest-policy-for-groups-at-the-directory-level).

### <a name="review-your-teams-sharing-settings"></a>Takımlarınızın paylaşım ayarlarını gözden geçirin

- Dış kullanıcılara yönelik erişim paketlerinize takımlar eklemek istiyorsanız, Konuk erişimine izin vermek için **Microsoft ekiplerinde Konuk erişimine Izin ver** ' in **Açık** olarak ayarlandığından emin olun. Daha fazla bilgi için bkz. [Microsoft ekipleri Yönetim merkezinde konuk erişimini yapılandırma](/microsoftteams/set-up-guests#configure-guest-access-in-the-teams-admin-center).

## <a name="manage-the-lifecycle-of-external-users"></a>Dış kullanıcıların yaşam döngüsünü yönetme

Bir dış Kullanıcı, bir erişim paketi isteği ile dizininizden ne olacağını, artık herhangi bir erişim paketi ataması olmadığını seçebilirsiniz. Bu durum, Kullanıcı tüm erişim paketi atamalarını yeniden alıyorsa veya son erişim paketi atamasının süresi dolarsa meydana gelir. Varsayılan olarak, bir dış kullanıcının artık herhangi bir erişim paketi ataması yoksa, dizininizde oturum açması engellenir. 30 gün sonra, Konuk Kullanıcı hesabı dizininizden kaldırılır.

**Önkoşul rolü:** Genel yönetici veya Kullanıcı Yöneticisi

1. Azure portal, **Azure Active Directory** ' a ve ardından **kimlik**Yönetimi ' ne tıklayın.

1. Sol taraftaki menüde, **Yetkilendirme Yönetimi** bölümünde, **Ayarlar**' a tıklayın.

1. **Düzenle**’ye tıklayın.

    ![Dış kullanıcıların yaşam döngüsünü yönetme ayarları](./media/entitlement-management-external-users/settings-external-users.png)

1. **Dış kullanıcıların yaşam döngüsünü Yönet** bölümünde dış kullanıcılar için farklı ayarları seçin.

1. Dış Kullanıcı herhangi bir erişim paketine son atamasını kaybederse, bu dizinde oturum açmasını engellemek isterseniz, **blok dış kullanıcıyı bu dizine oturum açmasını, bu dizinde** **Evet**olarak ayarlayın.

    > [!NOTE]
    > Bir kullanıcının bu dizinde oturum açması engellenirse, Kullanıcı erişim paketini yeniden isteyemeyecek veya bu dizinde ek erişim isteyemeyecektir. Daha sonra diğer erişim paketlerine erişim istemesi gerekiyorsa, bunları oturum açmasını engellemeyi yapılandırmayın.

1. Dış Kullanıcı herhangi bir erişim paketine son atamasını kaybederse, bu dizindeki Konuk Kullanıcı hesabını kaldırmak istiyorsanız, **dış kullanıcıyı kaldır** seçeneğini **Evet**olarak ayarlayın.

    > [!NOTE]
    > Yetkilendirme Yönetimi yalnızca, yetkilendirme yönetimi aracılığıyla davet edilen hesapları kaldırır. Ayrıca, bir kullanıcının bu dizindeki ve paket atamalarındaki kaynaklara eklenmiş olması durumunda bile bu dizinden oturum açması ve bu dizinden kaldırılması engellendiğine unutmayın. Konuk, erişim paketi atamaları almadan önce bu dizinde mevcutsa kalır. Bununla birlikte, Konuk bir erişim paketi ataması aracılığıyla davet ediliyorsa ve davet edildikten sonra bir OneDrive Iş veya SharePoint Online sitesine atandıktan sonra yine de kaldırılacaktır.

1. Bu dizindeki Konuk Kullanıcı hesabını kaldırmak istiyorsanız, kaldırılmadan önce geçecek gün sayısını ayarlayabilirsiniz. Konuk Kullanıcı hesabını, herhangi bir erişim paketine en son atamasını yitirdikleri anda kaldırmak istiyorsanız, **bu dizinden dış Kullanıcı kaldırılmadan önce geçecek gün sayısını** **0**olarak ayarlayın.

1. **Kaydet** düğmesine tıklayın.

## <a name="next-steps"></a>Sonraki adımlar

- [Bağlı kuruluş ekleme](entitlement-management-organization.md)
- [Dizininizde bulunmayan kullanıcılar için](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)
- [Sorun giderme](entitlement-management-troubleshoot.md)
