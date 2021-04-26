---
title: Azure AD Yetkilendirme Yönetimi 'nde bir erişim paketi için atamaları görüntüleme, ekleme ve kaldırma-Azure Active Directory
description: Azure Active Directory yetkilendirme yönetiminde bir erişim paketi için atamaları görüntüleme, ekleme ve kaldırma hakkında bilgi edinin.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6e00fe3761824462252ce4984beb754385f3eca9
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532150"
---
# <a name="view-add-and-remove-assignments-for-an-access-package-in-azure-ad-entitlement-management"></a>Azure AD yetkilendirme yönetiminde bir erişim paketi için atamaları görüntüleme, ekleme ve kaldırma

Azure AD Yetkilendirme Yönetimi 'nde, paketlere kimlerin atandığını, ilkelerini ve durumunu görebilirsiniz. Bir erişim paketinin uygun bir ilkesi varsa, kullanıcıyı da bir erişim paketine doğrudan atayabilirsiniz. Bu makalede erişim paketleri için atamaları görüntüleme, ekleme ve kaldırma işlemlerinin nasıl yapılacağı açıklanır.

## <a name="prerequisites"></a>Önkoşullar

Azure AD yetkilendirme yönetimini kullanmak ve kullanıcılara paketlere erişim atamak için aşağıdaki lisanslardan birine sahip olmanız gerekir:

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5 lisansı

## <a name="view-who-has-an-assignment"></a>Kimin atamaya sahip olduğunu görüntüleme

**Önkoşul rolü:** Genel yönetici, Kullanıcı Yöneticisi, Katalog sahibi, Paket Yöneticisi veya erişim paketi atama Yöneticisi

1. Azure portal, **Azure Active Directory** ' a ve ardından **kimlik** Yönetimi ' ne tıklayın.

1. Sol menüde, **erişim paketleri** ' ne tıklayın ve ardından erişim paketini açın.

1. Etkin atamaların listesini görmek için **atamalar** ' a tıklayın.

    ![Erişim paketine yönelik atamaların listesi](./media/entitlement-management-access-package-assignments/assignments-list.png)

1. Ek ayrıntıları görmek için belirli bir atamaya tıklayın.

1. Tüm kaynak rollerinin düzgün şekilde sağlandığı atamaların listesini görmek için, filtre durumuna tıklayın ve **teslim etme**' yi seçin.

    **İstek** sayfasında, kullanıcının karşılık gelen isteğini bularak teslim hatalarıyla ilgili ek ayrıntıları görebilirsiniz.

1. Süre biten atamaları görmek için, filtre durumuna tıklayın ve süre **bitti**' yi seçin.

1. Filtrelenmiş listenin CSV dosyasını indirmek için **İndir**' e tıklayın.

### <a name="viewing-assignments-programmatically"></a>Atamaları programlı görüntüleme

Ayrıca, Microsoft Graph kullanarak bir erişim paketindeki atamaları alabilirsiniz.  Temsilci izni olan bir uygulamayla uygun bir roldeki kullanıcı, `EntitlementManagement.ReadWrite.All` [Accesspackageatamaları listelemek](/graph/api/accesspackageassignment-list?view=graph-rest-beta&preserve-view=true)için API 'yi çağırabilir.

## <a name="directly-assign-a-user"></a>Doğrudan kullanıcı atama

Bazı durumlarda, kullanıcıların erişim paketini isteme işlemini istememeleri için bir erişim paketine doğrudan belirli kullanıcıları atamak isteyebilirsiniz. Kullanıcıları doğrudan atamak için, erişim paketinin yönetici doğrudan atamalarına izin veren bir ilkesi olması gerekir.

**Önkoşul rolü:** Genel yönetici, Kullanıcı Yöneticisi, Katalog sahibi, Paket Yöneticisi veya erişim paketi atama Yöneticisi

1. Azure portal, **Azure Active Directory** ' a ve ardından **kimlik** Yönetimi ' ne tıklayın.

1. Sol menüde, **erişim paketleri** ' ne tıklayın ve ardından erişim paketini açın.

1. Sol taraftaki menüde **atamalar**' a tıklayın.

1. Erişim paketine Kullanıcı Ekle ' yi açmak için **yeni atama** ' ya tıklayın.

    ![Atamalar-erişim paketine Kullanıcı ekleme](./media/entitlement-management-access-package-assignments/assignments-add-user.png)

1. Bu erişim paketini atamak istediğiniz kullanıcıları seçmek için **Kullanıcı Ekle** ' ye tıklayın.

1. **Ilke Seç** listesinde, kullanıcıların gelecekteki istekleri ve yaşam döngüsünün tarafından yönetilecektir ve izlendiğini belirten bir ilke seçin. Seçili kullanıcıların farklı ilke ayarlarına sahip olmasını istiyorsanız yeni ilke **Oluştur** ' a tıklayarak yeni ilke ekleyebilirsiniz.

1. Seçili kullanıcıların atamasının başlamasını ve bitmesini istediğiniz tarih ve saati ayarlayın. Bir bitiş tarihi sağlanmazsa, ilkenin yaşam döngüsü ayarları kullanılacaktır.

1. İsteğe bağlı olarak kayıt tutmaya yönelik doğrudan atamanız için bir gerekçe sağlar.

1. Seçili kullanıcıları doğrudan erişim paketine atamak için **Ekle** ' ye tıklayın.

    Birkaç dakika sonra, atamalar listesinde kullanıcıları görmek için **Yenile** ' ye tıklayın.

### <a name="directly-assigning-users-programmatically"></a>Kullanıcı aracılığıyla doğrudan kullanıcı atama

Ayrıca, Microsoft Graph kullanarak bir kullanıcıyı doğrudan bir erişim paketine atayabilirsiniz.  Temsilci izni olan bir uygulamayla uygun bir roldeki kullanıcı, `EntitlementManagement.ReadWrite.All` [bir Accesspackageatamaisteği oluşturmak](/graph/api/accesspackageassignmentrequest-post?view=graph-rest-beta&preserve-view=true)için API 'yi çağırabilir.

## <a name="remove-an-assignment"></a>Atama kaldırma

**Önkoşul rolü:** Genel yönetici, Kullanıcı Yöneticisi, Katalog sahibi, Paket Yöneticisi veya erişim paketi atama Yöneticisi

1. Azure portal, **Azure Active Directory** ' a ve ardından **kimlik** Yönetimi ' ne tıklayın.

1. Sol menüde, **erişim paketleri** ' ne tıklayın ve ardından erişim paketini açın.

1. Sol taraftaki menüde **atamalar**' a tıklayın.
 
1. Atamasını erişim paketinden kaldırmak istediğiniz kullanıcının yanındaki onay kutusuna tıklayın. 

1. Sol bölmenin üst kısmındaki **Kaldır** düğmesine tıklayın. 
 
    ![Atamalar-kullanıcıları erişim paketinden kaldır](./media/entitlement-management-access-package-assignments/remove-assignment-select-remove-assignment.png)

    Atamanın kaldırıldığını bildiren bir bildirim görüntülenir. 

## <a name="next-steps"></a>Sonraki adımlar

- [Erişim paketi için isteği ve ayarları değiştirme](entitlement-management-access-package-request-policy.md)
- [Raporları ve günlükleri görüntüleme](entitlement-management-reports.md)