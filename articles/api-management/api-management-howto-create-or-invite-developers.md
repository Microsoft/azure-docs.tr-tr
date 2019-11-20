---
title: Azure API Management Kullanıcı hesaplarını yönetme | Microsoft Docs
description: Azure API Management Kullanıcı oluşturmayı veya davet etmeyi öğrenin
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 02/13/2018
ms.author: apimpm
ms.openlocfilehash: 9de75b5537b3e267116882e7f151d893de575071
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70072460"
---
# <a name="how-to-manage-user-accounts-in-azure-api-management"></a>Azure API Management Kullanıcı hesaplarını yönetme

API Management, geliştiriciler API Management kullanarak kullanıma sunabileceğiniz API 'lerin kullanıcılardır. Bu kılavuzda, geliştiricilerin API Management örneğinizle birlikte kullanılabilir hale getirmek üzere API 'Leri ve ürünleri kullanmak üzere nasıl oluşturulacağı ve davet yapılacağı gösterilir. Kullanıcı hesaplarını programlı olarak yönetme hakkında daha fazla bilgi için [API Management Rest](/rest/api/apimanagement/) başvurusu içindeki [Kullanıcı varlık](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/user) belgelerine bakın.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="prerequisites"></a>Önkoşullar

Bu makaledeki görevleri tamamlayabilirsiniz: [Azure API Management örneği oluşturun](get-started-create-service-instance.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-developer"> </a>Yeni bir geliştirici oluşturun

Yeni bir kullanıcı eklemek için bu bölümdeki adımları izleyin:

1. Ekranın solundaki **Kullanıcılar** sekmesini seçin.
2. **+ Ekle**tuşuna basın.
3. Kullanıcı için uygun bilgileri girin.
4. **Ekle**’ye basın.

    ![Yeni kullanıcı ekleme](./media/api-management-howto-create-or-invite-developers/api-management-create-developer.png)

Varsayılan olarak, yeni oluşturulan geliştirici hesapları **etkindir**ve **geliştiriciler** grubuyla ilişkilendirilir. **Etkin** durumda olan geliştirici hesapları, aboneliklerine sahip oldukları tüm API 'lere erişmek için kullanılabilir. Yeni oluşturulan geliştiriciyi ek gruplarla ilişkilendirmek için bkz. [grupları geliştiricilerle ilişkilendirme][How to associate groups with developers].

## <a name="invite-developer"> </a>Geliştirici davet etme
Bir geliştiriciyi davet etmek için bu bölümdeki adımları izleyin:

1. Ekranın solundaki **Kullanıcılar** sekmesini seçin.
2. **+ Davet**' a basın.

Bir onay iletisi görüntülenir, ancak yeni davet edilen geliştirici daveti kabul edene kadar listede görünmez. 

Bir geliştirici davet edildiğinde, geliştiriciye bir e-posta gönderilir. Bu e-posta, bir şablon kullanılarak oluşturulur ve özelleştirilebilir. Daha fazla bilgi için bkz. [e-posta şablonlarını yapılandırma][Configure email templates].

Davet kabul edildiğinde, hesap etkin hale gelir.

## <a name="block-developer"></a> Geliştirici hesabını devre dışı bırakma veya yeniden etkinleştirme

Varsayılan olarak, yeni oluşturulan veya davet edilen geliştirici hesapları **etkindir**. Bir geliştirici hesabını devre dışı bırakmak için **Engelle**' ye tıklayın. Engellenen bir geliştirici hesabını yeniden etkinleştirmek için **Etkinleştir**' e tıklayın. Engellenen bir geliştirici hesabı geliştirici portalına erişemez veya API 'Leri çağırabilir. Bir kullanıcı hesabını silmek için **Sil**' e tıklayın.

Bir kullanıcıyı engellemek için aşağıdaki adımları izleyin.

1. Ekranın solundaki **Kullanıcılar** sekmesini seçin.
2. Engellemek istediğiniz kullanıcıya tıklayın.
3. **Blok**' a basın.

## <a name="reset-a-user-password"></a>Kullanıcı parolasını sıfırlama

Kullanıcı hesaplarıyla programlı bir şekilde çalışmak için [API Management REST API](/rest/api/apimanagement/) başvurusu içindeki Kullanıcı varlık belgelerine bakın. Bir kullanıcı hesabı parolasını belirli bir değere sıfırlamak için, [Kullanıcı güncelleştirme](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-user-entity#UpdateUser) işlemini kullanabilir ve istediğiniz parolayı belirtebilirsiniz.

## <a name="next-steps"> </a>Sonraki adımlar
Bir geliştirici hesabı oluşturulduktan sonra, bu rolü rollerle ilişkilendirebilir ve ürün ve API 'lere abone olabilirsiniz. Daha fazla bilgi için bkz. [grupları oluşturma ve kullanma][How to create and use groups].

[api-management-management-console]: ./media/api-management-howto-create-or-invite-developers/api-management-management-console.png
[api-management-add-new-user]: ./media/api-management-howto-create-or-invite-developers/api-management-add-new-user.png
[api-management-create-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-create-developer.png
[api-management-invite-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer.png
[api-management-new-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-new-developer.png
[api-management-invite-developer-window]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer-window.png
[api-management-invite-developer-confirmation]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer-confirmation.png
[api-management-pending-verification]: ./media/api-management-howto-create-or-invite-developers/api-management-pending-verification.png
[api-management-view-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-view-developer.png
[api-management-reset-password]: ./media/api-management-howto-create-or-invite-developers/api-management-reset-password.png


[Create a new developer]: #create-developer
[Invite a developer]: #invite-developer
[Deactivate or reactivate a developer account]: #block-developer
[Next steps]: #next-steps
[How to create and use groups]: api-management-howto-create-groups.md
[How to associate groups with developers]: api-management-howto-create-groups.md#associate-group-developer

[Get started with Azure API Management]: get-started-create-service-instance.md
[Create an API Management service instance]: get-started-create-service-instance.md
[Configure email templates]: api-management-howto-configure-notifications.md#email-templates
