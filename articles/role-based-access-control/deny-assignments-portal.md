---
title: Azure portal-Azure RBAC kullanarak Azure reddetme atamalarını listeleme
description: Azure portal ve Azure rol tabanlı erişim denetimi (Azure RBAC) kullanarak belirli kapsamlardaki belirli Azure Kaynak eylemlerine erişimi reddedilmiş kullanıcıları, grupları, hizmet sorumlularını ve yönetilen kimlikleri nasıl listeleyeceğinizi öğrenin.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 8078f366-a2c4-4fbb-a44b-fc39fd89df81
ms.service: role-based-access-control
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/10/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 1e644d81c2a47ec07833010ae93f4dbb0c57474b
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106077848"
---
# <a name="list-azure-deny-assignments-using-the-azure-portal"></a>Azure portal kullanarak Azure reddetme atamalarını listeleyin

[Azure reddetme atamaları](deny-assignments.md) , bir rol ataması erişim izni veriyorsa bile kullanıcıların belirli Azure Kaynak eylemlerini gerçekleştirmesini engeller. Bu makalede, Azure portal kullanarak reddedilen atamaları listeleme açıklanmaktadır.

> [!NOTE]
> Kendi reddetme atamalarınızı doğrudan oluşturamazsınız. Atamaları reddetme hakkında daha fazla bilgi için bkz. [Azure atamaları reddetme](deny-assignments.md).

## <a name="prerequisites"></a>Önkoşullar

Reddetme atama hakkında bilgi almak için, şunları yapmanız gerekir:

- `Microsoft.Authorization/denyAssignments/read`[Azure yerleşik rollerinin](built-in-roles.md)çoğunda bulunan izin.

## <a name="list-deny-assignments"></a>Ret atamalarını listeleme

Abonelik veya yönetim grubu kapsamındaki atamaları Reddet ' i listelemek için bu adımları izleyin.

1. Azure portal, **tüm hizmetler** ve ardından **Yönetim grupları** veya **abonelikler**' e tıklayın.

1. Listelemek istediğiniz yönetim grubuna veya aboneliğe tıklayın.

1. **Erişim denetimi (IAM)** öğesine tıklayın.

1. **Atamaları Reddet** sekmesine tıklayın (veya atamaları kaldır kutucuğunda **görüntüle** düğmesine tıklayın).

    Bu kapsamda herhangi bir reddetme ataması varsa veya bu kapsama devralınmışsa, bunlar listelenecektir.

    ![Erişim denetimi-atamaları Reddet sekmesi](./media/deny-assignments-portal/access-control-deny-assignments.png)

1. Ek sütunları göstermek için **Sütunları Düzenle**' ye tıklayın.

    ![Atamaları Reddet-sütunlar](./media/deny-assignments-portal/deny-assignments-columns.png)

    | Sütun | Açıklama  |
    | --- | --- |
    | **Ad** | Reddetme atamasının adı. |
    | **Sorumlu türü** | Kullanıcı, Grup, sistem tanımlı grup veya hizmet sorumlusu. |
    | **Reddedildi**  | Reddetme atamasında bulunan güvenlik sorumlusunun adı. |
    | **Numarasını** | Reddetme ataması için benzersiz tanımlayıcı. |
    | **Dışlanan sorumlular** | Reddetme atamasından dışlanan güvenlik sorumluları olup olmadığı. |
    | **Alt öğeler için uygulanmıyor** | Reddetme atamasının alt kapsamlara devralınıp alınmayacağını belirtir. |
    | **Korunan sistem** | Reddetme atamasının Azure tarafından yönetilip yönetilmediği. Şu anda her zaman Evet. |
    | **Kapsam** | Yönetim grubu, abonelik, kaynak grubu veya kaynak. |

1. Etkin öğelerin herhangi birine onay işareti ekleyin ve seçili sütunları göstermek için **Tamam** ' ı tıklatın.

## <a name="list-details-about-a-deny-assignment"></a>Reddetme atama hakkındaki ayrıntıları listeleyin

Reddetme atama hakkında ek ayrıntıları listelemek için aşağıdaki adımları izleyin.

1. Önceki bölümde açıklandığı gibi **atamaları Reddet** bölmesini açın.

1. **Kullanıcılar** dikey penceresini açmak için atama adına izin verme ' ye tıklayın.

    ![Atama reddetme-kullanıcılar](./media/deny-assignments-portal/deny-assignment-users.png)

    **Kullanıcılar** dikey penceresi aşağıdaki iki bölümü içerir.

    | Reddetme ayarı  | Description |
    | --- | --- |
    | **Reddetme ataması için geçerlidir**  | Reddetme atamasının geçerli olduğu güvenlik sorumluları. |
    | **Atama dışladığı reddetme** | Reddetme atamasından dışlanan güvenlik sorumluları. |

    **Sistem tarafından tanımlanan sorumlu** , BIR Azure AD dizinindeki tüm kullanıcıları, grupları, hizmet sorumlularını ve yönetilen kimlikleri temsil eder.

1. Reddedilen izinlerin bir listesini görmek için, **Reddedilen izinler**' e tıklayın.

    ![Atama reddi Izinlerini Reddet](./media/deny-assignments-portal/deny-assignment-denied-permissions.png)

    | Eylem türü | Description |
    | --- | --- |
    | **Eylemler**  | Yönetim işlemleri reddedildi. |
    | **NotActions** | Yönetim işlemleri Reddedilenler yönetim işleminden dışlandı. |
    | **Veri eylemleri**  | Reddedilen veri işlemleri. |
    | **NotDataActions** | Veri işlemleri, reddedilen veri işleminden dışlandı. |

    Önceki ekran görüntüsünde gösterilen örnek için aşağıdakiler geçerli izinlerdir:

    - İşlem işlemleri dışında veri düzleminde tüm depolama işlemleri reddedilir.

1. Reddetme atamasının özelliklerini görmek için **Özellikler**' e tıklayın.

    ![Atamayı reddetme-Özellikler](./media/deny-assignments-portal/deny-assignment-properties.png)

    **Özellikler** dikey penceresinde, reddetme atama adı, kimlik, açıklama ve kapsamı görebilirsiniz. **Alt öğe anahtarı için uygulanmıyor** , reddetme atamasının alt kapsamlara devralınıp alınmayacağını belirtir. **Sistem korumalı** anahtar, bu reddetme atamasının Azure tarafından yönetilip yönetilmediğini belirtir. Şu anda bu, her durumda **Evet** 'tir.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure reddetme atamalarını anlama](deny-assignments.md)
* [Azure PowerShell kullanarak Azure reddetme atamalarını listeleme](deny-assignments-powershell.md)
