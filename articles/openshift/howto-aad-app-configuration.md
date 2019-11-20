---
title: Azure Red Hat OpenShift için Azure Active Directory Tümleştirmesi | Microsoft Docs
description: Microsoft Azure Red Hat OpenShift kümenizdeki uygulamaları test etmek için bir Azure AD güvenlik grubu ve kullanıcı oluşturmayı öğrenin.
author: jimzim
ms.author: jzim
ms.service: container-service
manager: jeconnoc
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/13/2019
ms.openlocfilehash: 00609905d09f8d414660c21805c6efca5eb30843
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67669393"
---
# <a name="azure-active-directory-integration-for-azure-red-hat-openshift"></a>Azure Red Hat OpenShift için Azure Active Directory Tümleştirmesi

Azure Active Directory (Azure AD) kiracısı henüz oluşturmadıysanız,'ndaki yönergeleri izleyin [Azure Red Hat OpenShift için Azure AD kiracısı oluşturma](howto-create-tenant.md) bu yönergeleri ile devam etmeden önce.

Microsoft Azure Red Hat OpenShift kümenizi adına görevleri gerçekleştirmek için izinler gerekiyor. Kuruluşunuz zaten bir Azure AD kullanıcısı, Azure AD güvenlik grubu veya bir Azure AD uygulama kaydı hizmet sorumlusu kullanılacak yoksa, bunları oluşturmak için bu yönergeleri izleyin.

## <a name="create-a-new-azure-active-directory-user"></a>Yeni bir Azure Active Directory kullanıcısı oluşturma

İçinde [Azure portalında](https://portal.azure.com), kiracınıza kullanıcı adınızı üst altında göründüğünden emin olun portal'ın sağ:

![Kiracı portalı ekran görüntüsü, sağ üst bölümde listelenen](./media/howto-create-tenant/tenant-callout.png) yanlış kiracıya gösterilirse, sağ üst kullanıcı adınıza tıklayın ve ardından tıklayın **dizini Değiştir**, doğru kiracıdan seçip **tüm Dizinleri** listesi.

Azure Red Hat OpenShift kümenize oturum açmak için yeni bir Azure Active Directory genel yönetici kullanıcı oluşturun.

1. Git [kullanıcıların tüm kullanıcılar](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers) dikey penceresi.
2. Tıklayın **+ yeni kullanıcı** açmak için **kullanıcı** bölmesi.
3. Girin bir **adı** bu kullanıcı için.
4. Oluşturma bir **kullanıcı adı** oluşturduğunuz Kiracı adını temel alarak ile `.onmicrosoft.com` sonuna eklenir. Örneğin: `yourUserName@yourTenantName.onmicrosoft.com`. Bu kullanıcı adı yazın. Kümeniz için oturum açmanız gerekir.
5. Tıklayın **dizin rolü** dizin rolü bölmesini açın ve **genel yönetici** ve ardından **Tamam** bölmesinin alt kısmındaki.
6. İçinde **kullanıcı** bölmesinde tıklayın **Göster parola** ve geçici parolasını kaydedin. İlk kez oturum sonra sıfırlayın istenir.
7. Bölmenin altında tıklatın **Oluştur** kullanıcı oluşturmak için.

## <a name="create-an-azure-ad-security-group"></a>Azure AD güvenlik grubu oluşturma

Küme Yönetici erişimi vermek için bir Azure AD güvenlik grubu üyelik OpenShift grubu "osa müşteri-Yönetici" eşitlenir. Belirtilmezse, küme yönetici erişimi yok verilir.

1. Açık [Azure Active Directory grupları](https://portal.azure.com/#blade/Microsoft_AAD_IAM/GroupsManagementMenuBlade/AllGroups) dikey penceresi.
2. Tıklayın **+ yeni grup**.
3. Grup adı ve açıklama girin.
4. Ayarlama **grup türü** için **güvenlik**.
5. Ayarlama **üyelik türü** için **atanan**.

    Bu güvenlik grubuna önceki adımda oluşturduğunuz Azure AD kullanıcısını ekleyin.

6. Tıklayın **üyeleri** açmak için **üyelerini seçin** bölmesi.
7. Üye listesinde, yukarıda oluşturduğunuz Azure AD kullanıcısını seçin.
8. Portalın en altında tıklayın **seçin** ardından **Oluştur** güvenlik grubu oluşturun.

    Grup Kimliği değeri yazın.

9. Grup oluşturulduğunda, tüm grupları listesinde görürsünüz. Yeni gruba tıklayın.
10. Aşağı açılan sayfada kopyalama **nesne kimliği**. Bu değer anılacaktır `GROUPID` içinde [Azure Red Hat OpenShift küme oluşturma](tutorial-create-cluster.md) öğretici.

## <a name="create-an-azure-ad-app-registration"></a>Bir Azure AD uygulama kaydı oluşturma

Bir Azure Active Directory (Azure AD) uygulama kayıt istemcisi gt;(yok) küme oluşturma işleminin parçası olarak otomatik olarak oluşturabilir `--aad-client-app-id` bayrak `az openshift create` komutu. Bu öğreticide Azure AD uygulama kaydını bütünlük oluşturma işlemini gösterir.

Kuruluşunuz zaten bir hizmet sorumlusu olarak kullanılacak bir Azure Active Directory (Azure AD) uygulama kaydı yoksa oluşturmak için bu yönergeleri izleyin.

1. Açık [uygulama kayıtları dikey penceresinde](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview) tıklatıp **+ yeni kaydı**.
2. İçinde **bir uygulamayı kaydetme** bölmesinde, uygulama kaydı için bir ad girin.
3. Altında olduğundan emin olun **desteklenen hesap türleri** , **hesapları yalnızca kuruluş bu dizinde** seçilir. Bu en güvenli seçenektir.
4. Küme URI'si biliyoruz sonra bir yeniden yönlendirme URI'si daha sonra ekleyeceğiz. Tıklayın **kaydetme** Azure AD uygulama kaydı oluşturmak için.
5. Aşağı açılan sayfada kopyalama **uygulama (istemci) kimliği**. Bu değer anılacaktır `APPID` içinde [Azure Red Hat OpenShift küme oluşturma](tutorial-create-cluster.md) öğretici.

![Uygulama nesnesi sayfasının ekran görüntüsü](./media/howto-create-tenant/get-app-id.png)

### <a name="create-a-client-secret"></a>İstemci gizli dizi oluşturma

Uygulamanızı Azure Active Directory kimlik doğrulaması için bir istemci gizli anahtarı oluşturun.

1. İçinde **Yönet** bölümü uygulama kayıtları sayfanın tıklayın **sertifikaları ve parolaları**.
2. Üzerinde **sertifikaları ve parolaları** bölmesinde tıklayın **+ yeni gizli**.  **İstemci gizli dizi eklemek** bölmesi görünür.
3. Sağlayan bir **açıklama**.
4. Ayarlama **Expires** , tercih süresi **2 yıl içinde**.
5. Tıklayın **Ekle** ve anahtar değeri görünür **istemci gizli dizileri** sayfasının bölümünde.
6. Anahtar değerini kopyalayın. Bu değer anılacaktır `SECRET` içinde [Azure Red Hat OpenShift küme oluşturma](tutorial-create-cluster.md) öğretici.

![Sertifikaları ve parolaları bölmesinin ekran görüntüsü](./media/howto-create-tenant/create-key.png)

Azure uygulama nesneleri hakkında daha fazla bilgi için bkz. [uygulaması ve Azure Active Directory'de Hizmet sorumlusu nesneleri](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals).

Yeni bir oluşturma hakkında bilgi edinmek için Azure AD uygulaması, bakın [bir uygulamayı Azure Active Directory v1.0 uç noktası ile kaydetme](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app).

## <a name="add-api-permissions"></a>API izinleri ekleme

1. İçinde **Yönet** bölümünde **API izinleri**.
2. Tıklayın **iznini ekleyin** seçip **Azure Active Directory Graph'i** ardından **temsilci izinleri**
3. Genişletin **kullanıcı** emin olun ve aşağıdaki listede **User.Read** etkinleştirilir.
4. Yukarı kaydırın ve **uygulama izinleri**.
5. Genişletin **dizin** etkinleştir ve listenin altındaki **Directory.ReadAll**
6. Tıklayın **izinleri eklemek** değişiklikleri kabul etmek için.
7. API izinleri paneli artık hem göstermelidir *User.Read* ve *Directory.ReadAll*. Lütfen uyarı unutmayın **yönetici onayı gerekli** yanındaki sütuna *Directory.ReadAll*.
8. Eğer *Azure aboneliğinin Yöneticisi*, tıklayın **vermek için yönetici onayı *abonelik adı***  aşağıda. Kök kullanıcı değilseniz *Azure aboneliğinin Yöneticisi*, yöneticinizden izin isteyin.
![API izinleri bölmesinin ekran görüntüsü. Eklenen User.Read ve Directory.ReadAll izinleri Directory.ReadAll için yönetici onayı gerekli](./media/howto-aad-app-configuration/permissions-required.png)

> [!IMPORTANT]
> Eşitleme küme yöneticileri grubunun, sadece onay verildikten sonra çalışır. Bir onay işareti ve bir ileti yeşil bir daire görürsünüz "için verilen *abonelik adı*" içinde *yönetici onayı gerekli* sütun.

Yöneticileri ve diğer rolleri yönetme hakkında daha fazla bilgi için bkz [ekleme veya değiştirme Azure aboneliği yöneticileri](https://docs.microsoft.com/azure/billing/billing-add-change-azure-subscription-administrator).

## <a name="resources"></a>Kaynaklar

* [Uygulamalar ve Azure Active Directory'de Hizmet sorumlusu nesneleri](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals)
* [Hızlı Başlangıç: Azure Active Directory v1.0 uç noktası ile bir uygulamayı kaydetme](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app)

## <a name="next-steps"></a>Sonraki adımlar

Tüm karşılamanızın varsa [Azure Red Hat OpenShift önkoşulları](howto-setup-environment.md), ilk kümenizi oluşturmak hazırsınız!

Öğreticiyi deneyin:
> [!div class="nextstepaction"]
> [Azure Red Hat OpenShift kümesi oluşturma](tutorial-create-cluster.md)
