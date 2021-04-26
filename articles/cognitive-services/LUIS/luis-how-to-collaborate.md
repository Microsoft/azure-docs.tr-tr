---
title: Başkalarıyla işbirliği yap-LUSıS
titleSuffix: Azure Cognitive Services
description: Uygulama sahibi, yazma kaynağına katkıda bulunanlar ekleyebilir. Bu katkıda bulunanlar, modeli değiştirebilir, eğleyebilir ve uygulamayı yayımlayabilir.
services: cognitive-services
author: aahill
ms.author: aahi
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 01/21/2021
ms.openlocfilehash: 5ca13784fe2f9a6a5b448bc838bf508f01b0a9fe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "101095187"
---
# <a name="add-contributors-to-your-app"></a>Uygulamanıza katkıda bulunanlar ekleyin

Uygulama sahibi, uygulamalara katkıda bulunanlar ekleyebilir. Bu katkıda bulunanlar, modeli değiştirebilir, eğleyebilir ve uygulamayı yayımlayabilir. Hesabınızı [geçirdikten](luis-migration-authoring.md) sonra, _katkıda bulunanlar_ , **Access Control (IAM)** sayfasını kullanarak yazma kaynağı için Azure Portal yönetilir. Ortak çalışan e-posta adresini ve _katkıda_ bulunan rolünü kullanarak bir kullanıcı ekleyin.

## <a name="add-contributor-to-azure-authoring-resource"></a>Azure yazma kaynağına katkıda bulunan ekleme

LUSıS yazma deneyiminiz, LUıN portalındaki **Manage-> Azure kaynakları** sayfasında bir yazma kaynağına bağlıysa geçirdiniz.

1. Azure portal, Language Understanding (LUSıS) yazma kaynağını bulun. Türü vardır `LUIS.Authoring` .
1. Bu kaynağın **Access Control (IAM)** sayfasında **+ Ekle** ' yi seçin ve **rol ataması Ekle**' yi seçin.

    ![Azure portal, yazma kaynağına rol ataması ekleyin.](./media/luis-how-to-collaborate/authoring-resource-access-control-add-role.png)

1. **Rol ataması Ekle** penceresinde, katkıda bulunan **rolünü** seçin. **Erişim ata** seçeneği IÇINDE **Azure AD Kullanıcı, Grup veya hizmet sorumlusu**' nı seçin. **Seç** seçeneğinde, kullanıcının e-posta adresini girin. Kullanıcı aynı etki alanı için 1 ' den fazla e-posta adresi tarafından biliniyorsa, _birincil_ e-posta hesabını girin.

    ![Azure AD için katkıda bulunan rolüne kullanıcının e-postasını ekleyin](./media/luis-how-to-collaborate/add-role-assignment-for-contributor.png)

    Kullanıcının e-postası bulunduğunda, hesabı seçin ve **Kaydet**' i seçin.

    Bu rol atamasıyla ilgili sorun yaşıyorsanız, [Azure rolleri](../../role-based-access-control/role-assignments-portal.md) ve [Azure erişim denetimi sorunlarını giderme](../../role-based-access-control/troubleshooting.md#problems-with-azure-role-assignments)' yi gözden geçirin.

## <a name="view-the-app-as-a-contributor"></a>Uygulamayı katkıda bulunan olarak görüntüleme

Katkıda bulunan olarak eklendikten sonra, [Luo portalında oturum açın](sign-in-luis-portal.md).

[!INCLUDE [switch azure directories](includes/switch-azure-directories.md)]

### <a name="users-with-multiple-emails"></a>Birden çok e-posta içeren kullanıcılar

Bir LUO uygulamasına katkıda bulunanlar eklerseniz, tam e-posta adresini belirtirsiniz. Azure Active Directory (Azure AD), tek bir kullanıcının birbirinin yerine birden fazla e-posta hesabına sahip olmasına izin verdiğinden, lug, kullanıcının katkıda bulunan ' i eklerken belirtilen e-posta adresiyle oturum açmasını gerektirir.

<a name="owner-and-collaborators"></a>

### <a name="azure-active-directory-resources"></a>Azure Active Directory kaynakları

Kuruluşunuzda [Azure Active Directory](../../active-directory/index.yml) (Azure AD) kullanıyorsanız, Language UNDERSTANDING (lusıs), BASIS kullanmak istediklerinde kullanıcılarınızın erişimi hakkındaki bilgilere izin ister. LUBUNUN gerektirdiği kaynaklar en az düzeydedir.

Yönetici onayı olan veya yönetici onayı gerektirmeyen bir hesapla kaydolmayı denediğinizde ayrıntılı açıklamayı görürsünüz, örneğin yönetici onayı:

* Kurumsal hesabınızla uygulamada oturum açmanıza olanak tanır ve uygulamanın profilinizi okumasını sağlayabilirsiniz. Ayrıca, uygulamanın temel şirket bilgilerini okumasına de olanak tanır. Bu, LUO 'NUN Kullanıcı KIMLIĞI, e-posta, ad gibi temel profil verilerini okumasına izin verir
* Uygulamanın Şu anda kullanmadığınız durumlarda bile verilerinizi görmesini ve güncelleştirmesini sağlar. Kullanıcının erişim belirtecini yenilemek için izin gerekir.


### <a name="azure-active-directory-tenant-user"></a>Kiracı Kullanıcı Azure Active Directory

LUSıS, standart Azure Active Directory (Azure AD) onay akışını kullanır.

Kiracı yöneticisinin, Azure AD 'de LUO 'NUN kullanılmasına erişim izni olan kullanıcıyla doğrudan çalışması gerekir.

* İlk olarak, Kullanıcı LUO 'da oturum açar ve yönetici onayı gerektiren açılır iletişim kutusunu görür. Devam etmeden önce Kullanıcı Kiracı Yöneticisi ile iletişim kurar.
* İkincisi, kiracı yöneticisi LUO 'da oturum açar ve bir onay akışı açılır iletişim kutusu görür. Bu, yöneticinin kullanıcı için izin vermesi gereken iletişim kutusu budur. Yönetici izni kabul ettiğinde, Kullanıcı LUO ile devam edebilir. Kiracı Yöneticisi LUO 'da oturum açamayacak, yönetici LUSıS 'e [izin](https://account.activedirectory.windowsazure.com/r#/applications) verebilir. Bu sayfada, listeyi adı içeren öğelere filtreleyebilirsiniz `LUIS` .

Kiracı Yöneticisi yalnızca belirli kullanıcıların LUSıS kullanmasını istiyorsa, olası birçok çözüm vardır:
* "Yönetici onayı" (Azure AD 'nin tüm kullanıcılarına onay) verme, ancak ardından kurumsal uygulama özellikleri altında "Kullanıcı Ataması gerekli" olarak ayarlanır ve son olarak yalnızca istenen kullanıcıları uygulamaya atayın/ekleyin. Bu yöntemde, yönetici uygulamaya "yönetici onayı" sağlamaya devam eder, ancak buna erişebilen kullanıcıları denetlemek mümkündür.
* İkinci bir çözüm, her kullanıcıya onay sağlamak için [Microsoft Graph Azure AD kimlik ve erişim YÖNETIMI API](/graph/azuread-identity-access-management-concept-overview) 'sini kullanmaktır.

Azure Active Directory Kullanıcıları ve onayı hakkında daha fazla bilgi edinin:
* [Uygulamanızı](../../active-directory/develop/howto-restrict-your-app-to-a-set-of-users.md) bir Kullanıcı kümesiyle sınırlayın

## <a name="next-steps"></a>Sonraki adımlar

* Sürümlerin uygulama yaşam döngüsünü denetlemek için [nasıl](luis-how-to-manage-versions.md) kullanılacağını öğrenin.
* Kaynak üzerinde [yazma kaynağı](luis-how-to-azure-subscription.md#authoring-key) ve [katkıda bulunanlar](luis-how-to-azure-subscription.md#contributions-from-other-authors) dahil olmak üzere kavramları anlayın.
* Yazma ve çalışma zamanı kaynakları [oluşturmayı](luis-how-to-azure-subscription.md) öğrenin
* Yeni [yazma kaynağına](luis-migration-authoring.md) geçir