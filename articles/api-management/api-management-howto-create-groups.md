---
title: Azure API Management 'de grupları kullanarak Geliştirici hesaplarını yönetme | Microsoft Docs
description: Azure API Management grupları kullanarak Geliştirici hesaplarını yönetmeyi öğrenin
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
ms.openlocfilehash: f83b91933e2aca4bf77fcab4a30f2123f4eb4ed0
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70073630"
---
# <a name="how-to-create-and-use-groups-to-manage-developer-accounts-in-azure-api-management"></a>Azure API Management geliştirici hesaplarını yönetmek için grupları oluşturma ve kullanma

API Management’te, ürünlerin geliştiricilere görünürlüğünü yönetmek için gruplar kullanılır. Ürünler öncelikle gruplara görünür hale getirilir ve ardından bu gruplardaki geliştiriciler gruplarıyla ilişkili ürünleri görüntüleyebilir ve bunlara abone olabilir. 

API Management şu sabit sistem gruplarına sahiptir:

* **Yöneticiler**: Azure aboneliği yöneticileri bu grubun üyesidir. Yöneticiler, geliştiriciler tarafından kullanılan API’leri, işlemleri ve ürünleri oluşturarak API Management hizmet örneklerini yönetir.
* **Geliştiriciler**: Kimliği doğrulanmış geliştirici portalı kullanıcıları bu gruba girer. Geliştiriciler, API'lerinizi kullanarak uygulama oluşturan müşterilerdir. Geliştiriciler, geliştirici portalına erişim iznine sahiptir ve bir API’nin işlemlerini çağıran uygulamalar oluşturur.
* **Konuklar**: Kimliği doğrulanmamış geliştirici portalı kullanıcıları (bir API Management örneğinin geliştirici portalını ziyaret eden olası müşteriler gibi) bu gruba girer. Bunlara API’leri görüntüleyebilme ancak çağıramama gibi bazı salt okunur erişimler verilebilir.

Bu sistem gruplarına ek olarak, yöneticiler özel gruplar oluşturabilir veya [ilişkili Azure Active Directory kiracılarındaki dış grupları kullanabilir][leverage external groups in associated Azure Active Directory tenants]. Özel ve dış gruplar, geliştiricilere görünürlük ve API ürünlerine erişim sağlayan sistem gruplarıyla birlikte kullanılabilir. Örneğin, belirli bir iş ortağı kuruluşla ilişkili geliştiriciler için özel bir grup oluşturabilir ve bunlara yalnızca ilgili API'leri içeren bir üründen API'lere erişim izni verebilirsiniz. Bir kullanıcı birden fazla grubun üyesi olabilir.

Bu kılavuzda, bir API Management örneğinin yöneticilerinin yeni gruplar ekleme ve bunları ürünlerle ve geliştiricilerle ilişkilendirme işlemlerinin nasıl yapılacağı gösterilmektedir.

Yayımcı portalındaki grupları oluşturma ve yönetmeye ek olarak, API Management REST API [Grup](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-group-entity) varlığını kullanarak gruplarınızı oluşturabilir ve yönetebilirsiniz.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="prerequisites"></a>Önkoşullar

Bu makaledeki görevleri tamamlayabilirsiniz: [Azure API Management örneği oluşturun](get-started-create-service-instance.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-group"> </a>Grup oluşturma

Bu bölümde, API Management hesabınıza yeni bir grup ekleme gösterilmektedir.

1. Ekranın solundaki **gruplar** sekmesini seçin.
2. **+ Ekle**' ye tıklayın.
3. Grup için benzersiz bir ad ve isteğe bağlı bir açıklama girin.
4. **Oluştur**’a basın.

    ![Yeni bir grup ekleyin](./media/api-management-howto-create-groups/groups001.png)

Grup oluşturulduktan sonra **gruplar** listesine eklenir. <br/>Grubun **adını** veya **açıklamasını** düzenlemek için Grup ve **ayarların**adına tıklayın.<br/>Grubu silmek için grubun adına tıklayın ve **Sil**' e basın.

Artık grup oluşturuldığına göre, ürünler ve geliştiricilerle ilişkilendirilebilir.

## <a name="associate-group-product"> </a>Bir grubu ürünle ilişkilendir

1. Soldaki **Ürünler** sekmesini seçin.
2. İstenen ürünün adına tıklayın.
3. **Access Control**tuşuna basın.
4. **+ Grup Ekle**' ye tıklayın.

    ![Bir grubu ürünle ilişkilendir](./media/api-management-howto-create-groups/groups002.png)
5. Eklemek istediğiniz grubu seçin.

    ![Bir grubu ürünle ilişkilendir](./media/api-management-howto-create-groups/groups003.png)

    Ürünlerden bir grubu kaldırmak için **Sil**' e tıklayın.

    ![Bir grubu silme](./media/api-management-howto-create-groups/groups004.png)

Bir ürün bir grupla ilişkilendirildikten sonra bu gruptaki geliştiriciler ürünü görüntüleyebilir ve bu ürüne abone olabilir.

> [!NOTE]
> Azure Active Directory grupları eklemek için bkz. [Azure API Management 'da Azure Active Directory kullanarak Geliştirici hesaplarını yetkilendirme](api-management-howto-aad.md).

## <a name="associate-group-developer"> </a>Grupları geliştiricilerle ilişkilendir

Bu bölümde, grupların üyelerle ilişkilendirilmesi gösterilmektedir.

1. Ekranın solundaki **gruplar** sekmesini seçin.
2. **Üyeler**' i seçin.

    ![Üye ekle](./media/api-management-howto-create-groups/groups005.png)
3. **+ Ekle** ' ye basın ve üye seçin.

    ![Üye ekle](./media/api-management-howto-create-groups/groups006.png)
4. **Seç**' e basın.

Geliştirici ve grup arasında ilişkilendirme eklendikten sonra **Kullanıcılar** sekmesinden görüntüleyebilirsiniz.

## <a name="next-steps"> </a>Sonraki adımlar

* Bir gruba bir geliştirici eklendikten sonra, bu grup ile ilişkili ürünleri görüntüleyebilir ve bunlara abone olabilirler. Daha fazla bilgi için bkz. [Azure 'da ürün oluşturma ve yayımlama API Management][How create and publish a product in Azure API Management],
* Yayımcı portalındaki grupları oluşturma ve yönetmeye ek olarak, API Management REST API [Grup](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-group-entity) varlığını kullanarak gruplarınızı oluşturabilir ve yönetebilirsiniz.

[Create a group]: #create-group
[Associate a group with a product]: #associate-group-product
[Associate groups with developers]: #associate-group-developer
[Next steps]: #next-steps

[How create and publish a product in Azure API Management]: api-management-howto-add-products.md

[Get started with Azure API Management]: get-started-create-service-instance.md
[Create an API Management service instance]: get-started-create-service-instance.md
[leverage external groups in associated Azure Active Directory tenants]: api-management-howto-aad.md
