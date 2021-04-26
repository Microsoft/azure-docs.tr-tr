---
title: Öğretici-Azure Active Directory B2C kiracı oluşturma
description: Azure portal kullanarak Azure Active Directory B2C kiracı oluşturarak uygulamalarınızı kaydetmeye nasıl hazırlanacağınızı öğrenmek için bu öğreticiyi izleyin.
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 12/03/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: aa91851787bbb0f5570a4f439f794ee352bf0625
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104579697"
---
# <a name="tutorial-create-an-azure-active-directory-b2c-tenant"></a>Öğretici: Azure Active Directory B2C kiracısı oluşturma

Uygulamalarınızın Azure Active Directory B2C (Azure AD B2C) ile etkileşime girebilmesi için, yönettiğiniz bir kiracıda kayıtlı olmaları gerekir. 

> [!NOTE]
> Abonelik başına en fazla 20 kiracı oluşturabilirsiniz. Bu sınır, kaynaklarınız için hizmet reddi saldırıları gibi tehditlere karşı korunmaya yardımcı olur ve hem Azure portal hem de temel alınan kiracı oluşturma API 'sinde zorlanır. 20 ' den fazla kiracı oluşturmanız gerekiyorsa lütfen [Microsoft desteği](support-options.md)başvurun.
> 
> Daha önce silmeyi denediğiniz bir kiracı adını yeniden kullanmak istiyorsanız, ancak etki alanı adını girerken "zaten başka bir dizin tarafından kullanımda olan" hatasını görürseniz, [önce kiracıyı tamamen silmek için bu adımları izlemeniz](./faq.md?tabs=app-reg-ga#how-do-i-delete-my-azure-ad-b2c-tenant)gerekir. En az abonelik Yöneticisi 'nin bir rolü gereklidir. Kiracıyı sildikten sonra, etki alanı adını yeniden kullanabilmeniz için oturumunuzu kapatıp yeniden açmanız gerekebilir.

Bu makalede şunları öğreneceksiniz:

> [!div class="checklist"]
> * Azure AD B2C kiracısı oluşturma
> * Kiracınızı aboneliğinize bağlama
> * Azure AD B2C kiracınızı içeren dizine geçiş yapın
> * Azure AD B2C kaynağını Azure portal **Sık kullanılanlara** ekleme

Bir uygulamayı sonraki öğreticide nasıl kaydedeceğinizi öğrenirsiniz.

## <a name="prerequisites"></a>Ön koşullar

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="create-an-azure-ad-b2c-tenant"></a>Azure AD B2C kiracısı oluşturma

1. [Azure portalında](https://portal.azure.com/) oturum açın. Abonelik içinde en azından [katılımcı](../role-based-access-control/built-in-roles.md) rolü veya abonelik içindeki bir kaynak grubu atanmış bir Azure hesabıyla oturum açın.

1. Aboneliğinizi içeren dizini seçin.

    Azure portal araç çubuğunda **Dizin + abonelik** simgesini seçin ve ardından aboneliğinizi içeren dizini seçin. Bu dizin Azure AD B2C kiracınızı içerecek olan dizinden farklıdır.

    ![Abonelik kiracısı, Dizin + abonelik kiracısı seçiliyken abonelik filtresi](media/tutorial-create-tenant/portal-01-pick-directory.png)

1. Azure portal menüsünde veya **Giriş** sayfasında, **Kaynak oluştur**’u seçin.

   ![Kaynak Oluştur düğmesini seçin](media/tutorial-create-tenant/create-a-resource.png)

1. **Azure Active Directory B2C** araması yapın ve ardından **Oluştur**' u seçin.
2. **Yeni Azure AD B2C Kiracısı oluştur**’u seçin.

    ![Azure portal ' de seçili yeni bir Azure AD B2C kiracı oluşturun](media/tutorial-create-tenant/portal-02-create-tenant.png)

1. **Dizin Oluştur** sayfasında, aşağıdakileri girin:

   - **Kuruluş adı** -Azure AD B2C kiracınız için bir ad girin.
   - **İlk etki alanı adı** -Azure AD B2C kiracınız için bir etki alanı adı girin.
   - **Ülke veya bölge** -listeden ülkenizi veya bölgenizi seçin. Bu seçim daha sonra değiştirilemez.
   - **Abonelik** -listeden aboneliğinizi seçin.
   - **Kaynak grubu** -kiracıyı içerecek kaynak grubunu seçin veya arayın.

    ![İçinde kiracı formunu örnek değerlerle oluşturun Azure portal](media/tutorial-create-tenant/review-and-create-tenant.png)

1. **Gözden geçir ve oluştur**’u seçin.
1. Dizin ayarlarınızı gözden geçirin. Ardından **Oluştur**’u seçin. [Dağıtım hatalarını giderme](../azure-resource-manager/templates/common-deployment-errors.md)için.

Faturalama amacıyla birden çok Azure AD B2C kiracıyı tek bir Azure aboneliğine bağlayabilirsiniz. Bir kiracıyı bağlamak için Azure AD B2C kiracısında yönetici olmanız ve Azure aboneliği içinde en az bir katkıda bulunan rolü atanması gerekir. Bkz. [Azure AD B2C kiracıyı aboneliğe bağlama](billing.md#link-an-azure-ad-b2c-tenant-to-a-subscription).

## <a name="select-your-b2c-tenant-directory"></a>B2C kiracı dizininizi seçin

Yeni Azure AD B2C kiracınızı kullanmaya başlamak için kiracıyı içeren dizine geçmeniz gerekir.

Azure portal üst menüsünde **Dizin + abonelik** filtresi ' ni seçin ve Azure AD B2C kiracınızı içeren dizini seçin.

İlk olarak listede yeni Azure B2C kiracınızı görmüyorsanız tarayıcı pencerenizi yenileyin ve sonra üst menüdeki **Dizin + abonelik** filtresini yeniden seçin.

![B2C kiracısı-Azure portal ' de seçili dizin](media/tutorial-create-tenant/portal-07-select-tenant-directory.png)

## <a name="add-azure-ad-b2c-as-a-favorite-optional"></a>Sık kullanılan olarak Azure AD B2C Ekle (isteğe bağlı)

İsteğe bağlı bu adım, Azure AD B2C kiracınızı aşağıda ve sonraki tüm öğreticilerde seçmenizi kolaylaştırır.

Kiracınızla birlikte çalışmak istediğiniz her **durumda** *Azure AD B2C* için arama yapmak yerine kaynağı sık kullanılanlara ekleyebilirsiniz. Daha sonra, Azure AD B2C kiracınıza hızlıca gitmek için Portal menüsünün **Sık Kullanılanlar** bölümünden seçebilirsiniz.

Bu işlemi yalnızca bir kez gerçekleştirmeniz gerekir. Bu adımları gerçekleştirmeden önce, önceki bölümde açıklandığı gibi Azure AD B2C kiracınızı içeren dizine geçdiğinizden emin olun, [B2C kiracı dizininizi seçin](#select-your-b2c-tenant-directory).

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Azure portal menüsünde **tüm hizmetler**' i seçin.
1. **Tüm hizmetler** arama kutusunda **Azure AD B2C** arayın, arama sonucunun üzerine gelin ve ardından araç ipucunda yıldız simgesini seçin. **Azure AD B2C** artık **sık kullanılanlar** altındaki Azure Portal görüntülenir.
1. Yeni sık kullanılanlarınızın konumunu değiştirmek istiyorsanız, Azure portal menüsüne gidin, **Azure AD B2C**' i seçin ve ardından istediğiniz konuma aşağı veya aşağı sürükleyin.

    ![Azure AD B2C, Sık Kullanılanlar menüsü, Microsoft Azure portal](media/tutorial-create-tenant/portal-08-b2c-favorite.png)

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, şu şekilde nasıl yapılacağını öğrendiniz:

> [!div class="checklist"]
> * Azure AD B2C kiracısı oluşturma
> * Kiracınızı aboneliğinize bağlama
> * Azure AD B2C kiracınızı içeren dizine geçiş yapın
> * Azure AD B2C kaynağını Azure portal **Sık kullanılanlara** ekleme

Ardından, yeni kiracınızda bir Web uygulamasını nasıl kaydedeceğinizi öğrenin.

> [!div class="nextstepaction"]
> [Uygulamalarınızı kaydedin >](tutorial-register-applications.md)