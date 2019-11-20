---
title: Öğretici-Azure Active Directory B2C kiracı oluşturma
description: Azure portal kullanarak Azure Active Directory B2C kiracı oluşturarak uygulamalarınızı kaydetmeye nasıl hazırlanacağınızı öğrenin.
services: B2C
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/28/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: de29929c8fda476fe276f91d4a68ce4d632503fd
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71345217"
---
# <a name="tutorial-create-an-azure-active-directory-b2c-tenant"></a>Öğretici: Azure Active Directory B2C kiracı oluşturma

Uygulamalarınızın Azure Active Directory B2C (Azure AD B2C) ile etkileşime girebilmesi için, yönettiğiniz bir kiracıda kayıtlı olmaları gerekir.

Bu makalede şunları öğreneceksiniz:

> [!div class="checklist"]
> * Azure AD B2C kiracısı oluşturma
> * Kiracınızı aboneliğinize bağlama
> * Azure AD B2C kiracınızı içeren dizine geçiş yapın
> * Azure AD B2C kaynağını Azure portal *Sık kullanılanlara* ekleme

Bir uygulamayı sonraki öğreticide nasıl kaydedeceğinizi öğrenirsiniz.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="create-an-azure-ad-b2c-tenant"></a>Azure AD B2C kiracısı oluşturma

1. [Azure Portal](https://portal.azure.com/) oturum açın.
1. Aboneliğinizi içeren dizini kullandığınızdan emin olun.

    Üst menüden **Dizin + abonelik** filtresi ' ni seçin ve ardından aboneliğinizi içeren dizini seçin. Bu dizin Azure AD B2C kiracınızı içerecek olan dizinden farklıdır.

    ![Abonelik kiracısı seçiliyken Dizin + abonelik filtresi](media/tutorial-create-tenant/portal-01-select-directory.png)

1. Azure portal, sol üst köşedeki **kaynak oluştur** ' u seçin.
1. **Active Directory B2C**arayıp seçin ve ardından **Oluştur**' u seçin.
1. **Yeni Azure AD B2C Kiracısı oluştur**’u seçin.

    ![Azure portal ' de seçili yeni bir Azure AD B2C kiracı oluşturun](media/tutorial-create-tenant/portal-02-create-tenant.png)

1. Bir **kuruluş adı** ve **ilk etki alanı adı**girin. **Ülke veya bölgeyi** seçin (daha sonra değiştirilemez) ve ardından **Oluştur**' u seçin.

    Etki alanı adı, tam kiracı etki alanı adınızın bir parçası olarak kullanılır. Bu örnekte, kiracı adı *contosob2c.onmicrosoft.com*' dir:

    ![İçinde kiracı formunu örnek değerlerle oluşturun Azure portal](media/tutorial-create-tenant/portal-03-tenant-naming.png)

1. Kiracı oluşturma işlemi tamamlandıktan sonra, kiracı oluşturma sayfasının en üstündeki **yenı B2C kiracısı oluştur ' u veya mevcut kiracı** bağlantısı bağlantısını seçin.

    ![Azure portal bağlantı kiracı içerik haritası bağlantısı vurgulandı](media/tutorial-create-tenant/portal-04-select-link-sub-link.png)

1. **Mevcut bir Azure AD B2C kiracıyı Azure aboneliğime bağla**' yı seçin.

   ![Azure portal mevcut bir abonelik seçimini bağlama](media/tutorial-create-tenant/portal-05-link-subscription.png)

1. Oluşturduğunuz **Azure AD B2C kiracıyı** seçin, sonra **aboneliğinizi**seçin.

    **Kaynak grubu** için **Yeni oluştur**’u seçin. Kiracıyı içerecek kaynak grubu için bir **ad** girin, **kaynak grubu konumunu**seçin ve ardından **Oluştur**' u seçin.

    ![Azure portal abonelik ayarları formu bağlantısı](media/tutorial-create-tenant/portal-06-link-subscription-settings.png)

## <a name="select-your-b2c-tenant-directory"></a>B2C kiracı dizininizi seçin

Yeni Azure AD B2C kiracınızı kullanmaya başlamak için kiracıyı içeren dizine geçmeniz gerekir.

Azure portal üst menüsünde **Dizin + abonelik** filtresi ' ni seçin ve Azure AD B2C kiracınızı içeren dizini seçin.

İlk olarak listede yeni Azure B2C kiracınızı görmüyorsanız tarayıcı pencerenizi yenileyin ve sonra üst menüdeki **Dizin + abonelik** filtresini yeniden seçin.

![B2C kiracısı-Azure portal ' de seçili dizin](media/tutorial-create-tenant/portal-07-select-tenant-directory.png)

## <a name="add-azure-ad-b2c-as-a-favorite-optional"></a>Sık kullanılan olarak Azure AD B2C Ekle (isteğe bağlı)

İsteğe bağlı bu adım, Azure AD B2C kiracınızı aşağıda ve sonraki tüm öğreticilerde seçmenizi kolaylaştırır.

Kiracınızla çalışmak istediğinizde **tüm hizmetlerde** "Azure AD B2C" aramak yerine kaynağı sık kullanılanlara ekleyebilirsiniz. Daha sonra, Azure AD B2C kiracınıza hızlıca gitmek için sol **Sık Kullanılanlar** menüsünden bunu seçebilirsiniz.

Bu işlemi yalnızca bir kez gerçekleştirmeniz gerekir. Bu adımları gerçekleştirmeden önce, önceki bölümde açıklandığı gibi Azure AD B2C kiracınızı içeren dizine geçdiğinizden emin olun, [B2C kiracı dizininizi seçin](#select-your-b2c-tenant-directory).

1. [Azure Portal](https://portal.azure.com) sol menüsünde **tüm hizmetler** ' i seçin
1. Arama metin kutusuna *Azure AD B2C* girin
1. Sık kullanılanlarınıza Azure AD B2C eklenecek **yıldızı** seçin
1. *Azure AD B2C* artık **Sık Kullanılanlar** sol menüsünde görüntülenir. Daha sonra, aşağıdaki görüntüde gösterildiği gibi, isterseniz listede daha üst öğeyi seçip sürükleyebilirsiniz:

![Azure portal sık kullanılanlara eklemek Azure AD B2C ekleme adımları](media/tutorial-create-tenant/portal-08-favorite-b2c.png)

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, şu şekilde nasıl yapılacağını öğrendiniz:

> [!div class="checklist"]
> * Azure AD B2C kiracısı oluşturma
> * Kiracınızı aboneliğinize bağlama
> * Azure AD B2C kiracınızı içeren dizine geçiş yapın
> * Azure AD B2C kaynağını Azure portal *Sık kullanılanlara* ekleme

Ardından, yeni kiracınızda bir Web uygulamasını nasıl kaydedeceğinizi öğrenin.

> [!div class="nextstepaction"]
> [Uygulamalarınızı kaydedin >](tutorial-register-applications.md)
