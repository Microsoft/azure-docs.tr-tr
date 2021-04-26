---
title: Azure Red Hat OpenShift için Azure Active Directory Tümleştirmesi
description: Microsoft Azure Red Hat OpenShift kümenizdeki uygulamaları test etmeye yönelik bir Azure AD güvenlik grubu ve Kullanıcı oluşturmayı öğrenin.
author: jimzim
ms.author: jzim
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: f0bf28d61d4c9ad95a485fb4b60e370c16ace16c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100633334"
---
# <a name="azure-active-directory-integration-for-azure-red-hat-openshift"></a>Azure Red Hat OpenShift için Azure Active Directory Tümleştirmesi

> [!IMPORTANT]
> Azure Red Hat Openshıft 3,11, 30 Haziran 2022 tarihinde kullanımdan kaldırılacaktır. Yeni Azure Red Hat OpenShift 3,11 kümelerinin oluşturulmasına yönelik destek, 30 Kasım 2020 ile devam eder. Aşağıdaki kullanımdan kaldırma, kalan Azure Red Hat Openshıft 3,11 kümeleri güvenlik açıklarını engellemek için kapatılacak.
> 
> [Azure Red Hat OpenShift 4 kümesi oluşturmak](tutorial-create-cluster.md)için bu kılavuzu izleyin.
> Belirli sorularınız varsa [lütfen bizimle iletişime geçin](mailto:arofeedback@microsoft.com).

Henüz bir Azure Active Directory (Azure AD) kiracısı oluşturmadıysanız, bu yönergelere devam etmeden önce [Azure Red Hat OpenShift Için Azure AD kiracısı oluşturma](howto-create-tenant.md) bölümündeki yönergeleri izleyin.

Microsoft Azure Red Hat OpenShift, kümeniz adına görevleri gerçekleştirmek için izinler gerektirir. Kuruluşunuzun hizmet sorumlusu olarak kullanılacak bir Azure AD kullanıcısı, Azure AD güvenlik grubu veya bir Azure AD uygulama kaydı yoksa, bunları oluşturmak için bu yönergeleri izleyin.

## <a name="create-a-new-azure-active-directory-user"></a>Yeni bir Azure Active Directory kullanıcısı oluşturma

[Azure Portal](https://portal.azure.com), kiracınızın portalın sağ üst köşesindeki Kullanıcı adınızın altında göründüğünden emin olun:

![Kiracı 'nın sağ üst köşesinde listelenen portalın ekran görüntüsü ](./media/howto-create-tenant/tenant-callout.png) yanlış kiracı görüntüleniyorsa, sağ üst köşedeki Kullanıcı adına tıklayın, ardından **Dizin Değiştir**' e tıklayın ve **tüm dizinler** listesinden doğru kiracıyı seçin.

Azure Red Hat OpenShift kümenizde oturum açmak için yeni bir Azure Active Directory ' Owner ' kullanıcısı oluşturun.

1. [Kullanıcılar-tüm kullanıcılar](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers) dikey penceresine gidin.
2. **+ Yeni Kullanıcı** ' ya tıklayarak **Kullanıcı** bölmesini açın.
3. Bu Kullanıcı için bir **ad** girin.
4. Sonuna eklenmiş olarak, oluşturduğunuz kiracının adına göre bir **Kullanıcı adı** oluşturun  `.onmicrosoft.com` . Örneğin, `yourUserName@yourTenantName.onmicrosoft.com`. Bu Kullanıcı adını yazın. Kümenizin kümenizde oturum açması için bu gereklidir.
5. Dizin **rolü ' nü tıklatıp Dizin** rolü bölmesini **açın ve sonra** bölmenin altındaki **Tamam** ' a tıklayın.
6. **Kullanıcı** bölmesinde, **parolayı göster** ' e tıklayın ve geçici parolayı kaydedin. İlk kez oturum açtıktan sonra, bunu sıfırlamanız istenir.
7. Kullanıcı oluşturmak için bölmenin alt kısmındaki **Oluştur** ' a tıklayın.

## <a name="create-an-azure-ad-security-group"></a>Azure AD güvenlik grubu oluşturma

Küme Yöneticisi erişimi sağlamak için bir Azure AD güvenlik grubundaki Üyelikler, OpenShift grubu "OSA-Müşteri-yöneticileri" ile eşitlenir. Belirtilmemişse, hiçbir Küme Yöneticisi erişimi verilmez.

1. [Azure Active Directory grupları](https://portal.azure.com/#blade/Microsoft_AAD_IAM/GroupsManagementMenuBlade/AllGroups) dikey penceresini açın.
2. **+ Yeni Grup**' a tıklayın.
3. Bir grup adı ve açıklama sağlayın.
4. **Grup türünü** **güvenlik** olarak ayarlayın.
5. **Üyelik türünü** **atandı** olarak ayarlayın.

    Önceki adımda oluşturduğunuz Azure AD kullanıcısını bu güvenlik grubuna ekleyin.

6. Üyeler **' i** tıklatarak **üyeleri Seç** bölmesini açın.
7. Üyeler listesinde, yukarıda oluşturduğunuz Azure AD kullanıcısını seçin.
8. Portalın alt kısmındaki **Seç** ' e tıklayın ve sonra güvenlik grubunu oluşturmak için **Oluştur** ' a tıklayın.

    Grup KIMLIĞI değerini yazın.

9. Grup oluşturulduğunda, bu dosyayı tüm gruplar listesinde görürsünüz. Yeni gruba tıklayın.
10. Görüntülenen sayfada, **nesne kimliğini** kopyalayın. `GROUPID` [Azure Red Hat OpenShift kümesi oluşturma](tutorial-create-cluster.md) öğreticisinde bu değere başvuracağız.

> [!IMPORTANT]
> Bu grubu OSA-müşteri-Yöneticiler OpenShift grubuyla eşitlemek için Azure CLı kullanarak kümeyi oluşturun. Azure portal Şu anda bu grubu ayarlamak için bir alan eksik.

## <a name="create-an-azure-ad-app-registration"></a>Azure AD uygulama kaydı oluşturma

Komutun bayrağını atlayarak, kümeyi oluşturmanın bir parçası olarak otomatik olarak bir Azure Active Directory (Azure AD) uygulama kayıt istemcisi oluşturabilirsiniz `--aad-client-app-id` `az openshift create` . Bu öğreticide, Azure AD uygulama kaydını tamamlanma için nasıl oluşturacağınız gösterilmektedir.

Kuruluşunuzda hizmet sorumlusu olarak kullanılacak bir Azure Active Directory (Azure AD) uygulama kaydı yoksa, bir tane oluşturmak için bu yönergeleri izleyin.

1. [Uygulama kayıtları dikey penceresini](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview) açın ve **+ Yeni kayıt**' ye tıklayın.
2. **Uygulama kaydetme** bölmesinde, uygulama kaydınız için bir ad girin.
3. **Desteklenen hesap türleri** altında **yalnızca bu kuruluş dizinindeki hesapların** seçili olduğundan emin olun. Bu en güvenli seçenektir.
4. Kümenin URI 'sini öğrendikten sonra yeniden yönlendirme URI 'SI ekleyeceğiz. Azure AD uygulama kaydını oluşturmak için **Kaydet** düğmesine tıklayın.
5. Görüntülenen sayfada, **uygulama (istemci) kimliğini** kopyalayın. `APPID` [Azure Red Hat OpenShift kümesi oluşturma](tutorial-create-cluster.md) öğreticisinde bu değere başvuracağız.

![Uygulama nesnesi sayfasının ekran görüntüsü](./media/howto-create-tenant/get-app-id.png)

### <a name="create-a-client-secret"></a>İstemci parolası oluşturma

Azure Active Directory için uygulamanızın kimliğini doğrulamak için bir istemci gizli dizisi oluşturun.

1. Uygulama kayıtları sayfasının **Yönet** bölümünde **Sertifikalar & parolaları**' na tıklayın.
2. **Sertifikalar & gizlilikler** bölmesinde **+ yeni istemci parolası**' na tıklayın.  **İstemci gizli dizisi Ekle** bölmesi görüntülenir.
3. Bir **Açıklama** girin.
4. **Zaman aşımı** süresini tercih ettiğiniz süre (örneğin **2 yıl**) olarak ayarlayın.
5. **Ekle** ' ye tıkladıktan sonra, sayfanın **istemci gizli** dizileri bölümünde anahtar değeri görünür.
6. Anahtar değerini kopyalayın. `SECRET` [Azure Red Hat OpenShift kümesi oluşturma](tutorial-create-cluster.md) öğreticisinde bu değere başvuracağız.

![Sertifikalar ve gizlilikler bölmesinin ekran görüntüsü](./media/howto-create-tenant/create-key.png)

Azure uygulama nesneleri hakkında daha fazla bilgi için [Azure Active Directory Içindeki uygulama ve hizmet sorumlusu nesneleri](../active-directory/develop/app-objects-and-service-principals.md)bölümüne bakın.

Yeni bir Azure AD uygulaması oluşturma hakkında ayrıntılı bilgi için bkz. [Azure Active Directory v 1.0 uç noktası ile uygulama kaydetme](../active-directory/develop/quickstart-register-app.md).

## <a name="add-api-permissions"></a>API izinleri ekleme

[//]: # (Microsoft Graph olarak değiştirmeyin. Microsoft Graph ile çalışmaz.)
1. **Yönet** bölümünde **API izinleri** ' ne tıklayın.
2. **Izin Ekle** ' ye tıklayın ve ardından **Azure Active Directory grafik** **' i seçin**.
> [!NOTE]
> "Microsoft Graph" kutucuğunu değil, "Azure Active Directory Graf" öğesini seçtiğinizden emin olun.

3. Aşağıdaki listeden **Kullanıcı** ' yı genişletin ve **Kullanıcı. oku** iznini etkinleştirin. **Kullanıcı. Read** varsayılan olarak etkinleştirilmişse, **kullanıcı. Read** **Azure Active Directory Graph** iznine sahip olduğundan emin olun.
4. Yukarı kaydırın ve **Uygulama izinleri**' ni seçin.
5. Aşağıdaki listede yer alan **dizini** genişletin ve **Directory. ReadAll** öğesini etkinleştirin.
6. Değişiklikleri kabul etmek için **Izin Ekle** ' ye tıklayın.
7. API izinleri panelinde artık *User. Read* ve *Directory. ReadAll* gösterilmelidir. Lütfen *Directory. ReadAll*' ın yanındaki **yönetici onayı gerekli** sütununda uyarı ' yı unutmayın.
8. *Azure abonelik yöneticisiyseniz*, aşağıdaki ***abonelik adı* için yönetici onayı ver** ' e tıklayın. *Azure abonelik Yöneticisi* değilseniz, yöneticinizden onay isteyin.

![API izinleri bölmesinin ekran görüntüsü. User. Read ve Directory. ReadAll izinleri eklendi, dizin için yönetici onayı gerekli. ReadAll](./media/howto-aad-app-configuration/permissions-required.png)

> [!IMPORTANT]
> Küme yöneticileri grubunun eşitlenmesi, yalnızca onay verildikten sonra çalışır. *Yönetici onayı gerekli* sütununda onay işareti olan yeşil bir daire ve " *abonelik adı* için verildi" iletisi görüntülenir.

Yöneticileri ve diğer rolleri yönetme hakkında daha fazla bilgi için bkz. [Azure abonelik yöneticileri ekleme veya değiştirme](../cost-management-billing/manage/add-change-subscription-administrator.md).

## <a name="resources"></a>Kaynaklar

* [Azure Active Directory içindeki uygulamalar ve hizmet sorumlusu nesneleri](../active-directory/develop/app-objects-and-service-principals.md)
* [Hızlı Başlangıç: Azure Active Directory v1.0 uç noktasına uygulama kaydetme](../active-directory/develop/quickstart-register-app.md)

## <a name="next-steps"></a>Sonraki adımlar

Tüm [Azure Red Hat OpenShift önkoşullarını](howto-setup-environment.md)karşıladıysanız, ilk kümenizi oluşturmaya hazırsınız demektir!

Öğreticiyi deneyin:
> [!div class="nextstepaction"]
> [Azure Red Hat OpenShift kümesi oluşturma](tutorial-create-cluster.md)