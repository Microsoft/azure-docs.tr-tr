---
title: Azure AD Domain Services için kapsamlı eşitleme | Microsoft Docs
description: Azure AD 'den Azure Active Directory Domain Services yönetilen bir etki alanına kapsamlı eşitlemeyi yapılandırmak için Azure portal nasıl kullanacağınızı öğrenin
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 9389cf0f-0036-4b17-95da-80838edd2225
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 01/20/2021
ms.author: justinha
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 34692f5e563e4931a27ea59db84d9c88f27817da
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98660907"
---
# <a name="configure-scoped-synchronization-from-azure-ad-to-azure-active-directory-domain-services-using-the-azure-portal"></a>Azure AD 'den kapsamlı eşitlemeyi Azure Active Directory Domain Services Azure portal kullanarak yapılandırma

Azure Active Directory Domain Services (Azure AD DS), kimlik doğrulama hizmetleri sağlamak için kullanıcıları ve grupları Azure AD 'den eşitler. Karma bir ortamda, şirket içi Active Directory Domain Services (AD DS) ortamından kullanıcılar ve gruplar önce Azure AD Connect kullanılarak Azure AD ile eşitlenebilir ve sonra Azure AD DS yönetilen bir etki alanıyla eşitlenir.

Varsayılan olarak, bir Azure AD dizininden tüm kullanıcılar ve gruplar yönetilen bir etki alanıyla eşitlenir. Belirli gereksinimleriniz varsa, bunun yerine yalnızca tanımlı bir kullanıcı kümesini eşitlemeyi tercih edebilirsiniz.

Bu makalede, kapsamlı eşitlemenin nasıl yapılandırılacağı ve ardından Azure portal kullanarak kapsamlı kullanıcı kümesini nasıl değiştirileceği veya devre dışı bırakacaksınız. Ayrıca, [PowerShell kullanarak bu adımları tamamlayabilirsiniz][scoped-sync-powershell].

## <a name="before-you-begin"></a>Başlamadan önce

Bu makaleyi tamamlayabilmeniz için aşağıdaki kaynaklar ve ayrıcalıklar gereklidir:

* Etkin bir Azure aboneliği.
    * Azure aboneliğiniz yoksa [bir hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Abonelikle ilişkili bir Azure Active Directory kiracısı, şirket içi bir dizinle veya yalnızca bulut diziniyle eşitlenir.
    * Gerekirse, [bir Azure Active Directory kiracı oluşturun][create-azure-ad-tenant] veya [bir Azure aboneliğini hesabınızla ilişkilendirin][associate-azure-ad-tenant].
* Azure AD kiracınızda etkinleştirilmiş ve yapılandırılmış Azure Active Directory Domain Services yönetilen bir etki alanı.
    * Gerekirse, [Azure Active Directory Domain Services yönetilen bir etki alanı oluşturmak ve yapılandırmak][tutorial-create-instance]için öğreticiyi doldurun.
* Azure AD DS eşitleme kapsamını değiştirmek için Azure AD kiracınızda *genel yönetici* ayrıcalıklarına sahip olmanız gerekir.

## <a name="scoped-synchronization-overview"></a>Kapsamlı eşitlemeye genel bakış

Varsayılan olarak, bir Azure AD dizininden tüm kullanıcılar ve gruplar yönetilen bir etki alanıyla eşitlenir. Yönetilen etki alanına yalnızca birkaç kullanıcının erişmesi gerekiyorsa yalnızca bu kullanıcı hesaplarını eşitlemeniz yeterlidir. Bu kapsamlı eşitleme grup tabanlıdır. Grup tabanlı kapsamlı eşitleme yapılandırdığınızda, yalnızca belirttiğiniz gruplara ait olan kullanıcı hesapları yönetilen etki alanına eşitlenir. İç içe gruplar yalnızca seçtiğiniz belirli gruplar ile eşitlenmez.

Yönetilen etki alanını oluşturmadan önce veya sonra eşitleme kapsamını değiştirebilirsiniz. Eşitleme kapsamı, 2565bd9d-dad50-47d4-8B85-4c97f669dc36 uygulama tanımlayıcısına sahip bir hizmet sorumlusu tarafından tanımlanır. Kapsam kaybını engellemek için hizmet sorumlusunu silmeyin veya değiştirmeyin. Yanlışlıkla silinirse, eşitleme kapsamı kurtarılamaz. 

Eşitleme kapsamını değiştirirseniz aşağıdaki uyarıları aklınızda bulundurun:

- Tam eşitleme gerçekleşir.
- Yönetilen etki alanında artık gerekli olmayan nesneler silinir. Yönetilen etki alanında yeni nesneler oluşturulur.

Eşitleme işlemi hakkında daha fazla bilgi edinmek için bkz. [Azure AD Domain Services eşitlemeyi anlama][concepts-sync].

## <a name="enable-scoped-synchronization"></a>Kapsamlı eşitlemeyi etkinleştir

Azure portal kapsamlı eşitlemeyi etkinleştirmek için aşağıdaki adımları izleyin:

1. Azure portal, araması yapın ve **Azure AD Domain Services** seçin. *Aaddscontoso.com* gibi yönetilen etki alanınızı seçin.
1. Sol taraftaki menüden **eşitleme** ' yi seçin.
1. *Eşitleme türü* için **kapsamlı** öğesini seçin.
1. **Grupları seç**' i seçin, ardından eklenecek grupları arayıp seçin.
1. Tüm değişiklikler yapıldığında, **eşitleme kapsamını kaydet**' i seçin.

Eşitleme kapsamını değiştirmek, yönetilen etki alanının tüm verileri yeniden eşitlemesine neden olur. Yönetilen etki alanında artık gerekli olmayan nesneler silinir ve yeniden eşitleme işleminin tamamlanması biraz zaman alabilir.

## <a name="modify-scoped-synchronization"></a>Kapsamlı eşitlemeyi değiştirme

Kullanıcıları yönetilen etki alanıyla eşitlenmesi gereken grupların listesini değiştirmek için aşağıdaki adımları izleyin:

1. Azure portal, araması yapın ve **Azure AD Domain Services** seçin. *Aaddscontoso.com* gibi yönetilen etki alanınızı seçin.
1. Sol taraftaki menüden **eşitleme** ' yi seçin.
1. Bir grup eklemek için üstteki **grupları + seç** ' i seçin ve ardından eklenecek grupları seçin.
1. Bir grubu eşitleme kapsamından kaldırmak için, şu anda eşitlenmiş gruplar listesinden seçin ve **grupları kaldır**' ı seçin.
1. Tüm değişiklikler yapıldığında, **eşitleme kapsamını kaydet**' i seçin.

Eşitleme kapsamını değiştirmek, yönetilen etki alanının tüm verileri yeniden eşitlemesine neden olur. Yönetilen etki alanında artık gerekli olmayan nesneler silinir ve yeniden eşitleme işleminin tamamlanması biraz zaman alabilir.

## <a name="disable-scoped-synchronization"></a>Kapsamlı eşitlemeyi devre dışı bırak

Yönetilen bir etki alanı için grup tabanlı kapsamlı eşitlemeyi devre dışı bırakmak için aşağıdaki adımları izleyin:

1. Azure portal, araması yapın ve **Azure AD Domain Services** seçin. *Aaddscontoso.com* gibi yönetilen etki alanınızı seçin.
1. Sol taraftaki menüden **eşitleme** ' yi seçin.
1. *Eşitleme türünü* **Kapsamsız** olarak değiştirin ve ardından **eşitleme kapsamını kaydet** **' i** seçin.

Eşitleme kapsamını değiştirmek, yönetilen etki alanının tüm verileri yeniden eşitlemesine neden olur. Yönetilen etki alanında artık gerekli olmayan nesneler silinir ve yeniden eşitleme işleminin tamamlanması biraz zaman alabilir.

## <a name="next-steps"></a>Sonraki adımlar

Eşitleme işlemi hakkında daha fazla bilgi edinmek için bkz. [Azure AD Domain Services eşitlemeyi anlama][concepts-sync].

<!-- INTERNAL LINKS -->
[scoped-sync-powershell]: powershell-scoped-synchronization.md
[concepts-sync]: synchronization.md
[tutorial-create-instance]: tutorial-create-instance.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
