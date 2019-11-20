---
title: Yönetim birimleri yönetimi (Önizleme)-Azure AD | Microsoft Docs
description: Azure Active Directory izinlerin daha ayrıntılı olarak temsili için yönetim birimleri kullanma
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: article
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 11/13/2019
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3c4958975a080a98fb900bf317229ba9eda795d5
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74028421"
---
# <a name="administrative-units-management-in-azure-active-directory-preview"></a>Azure Active Directory 'de yönetim birimleri yönetimi (Önizleme)

Bu makalede Azure Active Directory (Azure AD) içindeki yönetim birimleri açıklanmaktadır. Yönetim birimi, diğer Azure AD kaynakları için bir kapsayıcı olabilecek bir Azure AD kaynağıdır. Bu önizleme sürümünde, bu kaynaklar yalnızca Kullanıcı olabilir. Örneğin, yönetim birimi kapsamlı bir kullanıcı hesabı Yöneticisi, profil bilgilerini güncelleştirebilir, parolaları sıfırlayabilir ve yalnızca kendi yönetim biriminde kullanıcılar için lisans atayabilir.

Yönetim birimlerini, kullanıcıların alt kümeleri üzerinde yönetici izinleri atamak ve kullanıcıların bir alt kümesine ilke uygulamak için kullanabilirsiniz. Bölgesel yöneticilerle izinleri devretmek veya ayrıntılı bir düzeyde ilke ayarlamak için yönetim birimlerini kullanabilirsiniz.

## <a name="deployment-scenario"></a>Dağıtım senaryosu

Yönetim birimleri bağımsız bölümler içeren kuruluşlarda yararlı olabilir. Birçok otonom okuldan (Iş kolu, mühendisin Okulu vb.) oluşan büyük bir üniversitenin örneğini, her birinin erişimi denetleyen, kullanıcıları yöneten ve okulların ilkelerini ayarlayabilen kendi BT yöneticilerine sahip olduğu bir örnek olarak düşünün. Merkezi bir yönetici, Iş Okulu için yönetim birimi oluşturabilir ve yalnızca iş okul öğrencilerine ve personeline göre doldurabilir. Daha sonra merkezi yönetici, Iş Okulu BT personelini, iş okul yönetim birimindeki yalnızca Azure AD kullanıcıları üzerinde yönetici izinleri veren kapsamlı bir role ekleyebilir.

## <a name="license-requirements"></a>Lisans gereksinimleri

Yönetim birimlerini kullanmak için her yönetim birimi Yöneticisi için bir Azure Active Directory Premium lisansı gerekir. Daha fazla bilgi için bkz. [Azure AD Premium kullanmaya](../fundamentals/active-directory-get-started-premium.md)başlama.

## <a name="managing-administrative-units"></a>Yönetim birimlerini yönetme

Bu önizleme sürümünde, yönetim birimlerini oluşturabileceğiniz ve yönetebileceğiniz tek yol, Windows PowerShell cmdlet 'leri için Azure Active Directory modülünü [yönetim birimleriyle çalışma](https://docs.microsoft.com/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0) konusunda açıklandığı şekilde kullanmaktır

Yazılım gereksinimleri hakkında daha fazla bilgi ve Azure AD modülünü yükleme ve sözdizimi, parametre açıklamaları ve örnekler de dahil olmak üzere yönetim birimlerini yönetmek için Azure AD modülü cmdlet 'leri hakkında başvuru bilgileri için bkz. [Azure Active Directory PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0).

## <a name="next-steps"></a>Sonraki adımlar

[Azure Active Directory sürümleri](../fundamentals/active-directory-whatis.md)
