---
title: PıM-Azure Active Directory kullanmaya başlama | Microsoft Docs
description: Azure portal Azure AD Privileged Identity Management (PıM) kullanmayı nasıl etkinleştireceğinizi ve kullanmaya başlamanızı öğrenin.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: pim
ms.topic: conceptual
ms.workload: identity
ms.date: 04/09/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: b0ba7846b60ca6649b4342d5096e92dfd8c96601
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72756304"
---
# <a name="start-using-privileged-identity-management"></a>Privileged Identity Management'ı kullanmaya başlama

Privileged Identity Management (PıM) ile Azure Active Directory (Azure AD) kuruluşunuzda erişimi yönetebilir, denetleyebilir ve izleyebilirsiniz. Bu kapsam, Azure kaynaklarına, Azure AD 'ye ve Office 365 veya Microsoft Intune gibi diğer Microsoft çevrimiçi hizmetler erişimi içerir.

Bu makalede Privileged Identity Management kullanmaya nasıl etkinleştirileceği ve kullanılacağı açıklanmaktadır.

## <a name="prerequisites"></a>Önkoşullar

Privileged Identity Management kullanmak için aşağıdaki lisanslardan birine sahip olmanız gerekir:

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5

Daha fazla bilgi için bkz. [Privileged Identity Management kullanılacak lisans gereksinimleri](subscription-requirements.md).

## <a name="first-person-to-use-pim"></a>PıM 'yi kullanmak için ilk kişi

Dizininizde Privileged Identity Management ilk kişiyseniz, otomatik olarak dizinde [Güvenlik Yöneticisi](../users-groups-roles/directory-assign-admin-roles.md#security-administrator) ve [ayrıcalıklı rol yöneticisi](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) rolleri atanır. Yalnızca ayrıcalıklı rol yöneticileri, kullanıcıların Azure AD rol atamalarını yönetebilir. Ayrıca, ilk bulma ve atama deneyiminde size yol gösteren [Güvenlik sihirbazını](pim-security-wizard.md) çalıştırmayı tercih edebilirsiniz.

## <a name="enable-pim"></a>PıM 'yi etkinleştir

Dizininizde Privileged Identity Management kullanmaya başlamak için önce Privileged Identity Management etkinleştirmeniz gerekir.

1. [Azure Portal](https://portal.azure.com/) , dizininizin genel yöneticisi olarak oturum açın.

    Bir dizin için Privileged Identity Management etkinleştirmek üzere bir Microsoft hesabı (örneğin, @outlook.com) değil, bir kuruluş hesabı (örneğin, @yourdomain.com) içeren bir genel yönetici olmanız gerekir.

1. **Tüm hizmetler** ' e tıklayın ve **Azure AD Privileged Identity Management** hizmeti bulun.

    ![Tüm hizmetlerde Azure AD Privileged Identity Management](./media/pim-getting-started/pim-all-services-find.png)

1. Privileged Identity Management hızlı başlangıcı 'nı açmak için tıklayın.

1. Listede **PIM Için izin**' yı tıklatın.

    ![Privileged Identity Management etkinleştirmek için Privileged Identity Management onayı](./media/pim-getting-started/consent-pim.png)

1. Azure MFA ile kimliğinizi doğrulamak için **kimliğimi doğrula** ' ya tıklayın. Bir hesap seçmeniz istenir.

    ![Kimliğinizi doğrulamak için bir hesap penceresi seçin](./media/pim-getting-started/pick-account.png)

1. Doğrulama için daha fazla bilgi gerekiyorsa, işlem boyunca size kılavuzluk edilir. Daha fazla bilgi için bkz. [iki adımlı doğrulamayla ilgili yardım alın](https://go.microsoft.com/fwlink/p/?LinkId=708614).

    ![Kuruluşunuzun daha fazla bilgi ihtiyacı varsa, daha fazla bilgi gerekli penceresi](./media/pim-getting-started/more-information-required.png)

    Örneğin, telefon doğrulaması sağlamanız istenebilir.

    ![Sizinle nasıl iletişim kurasoran ek güvenlik doğrulama sayfası](./media/pim-getting-started/additional-security-verification.png)

1. Doğrulama işlemini tamamladıktan sonra **onay** düğmesine tıklayın.

1. Görüntülenen iletide, Privileged Identity Management hizmetine izin vermek için **Evet** ' i tıklatın.

    ![Onay işlemini tamamlamaya yönelik Privileged Identity Management ileti onayı](./media/pim-getting-started/consent-pim-message.png)

## <a name="sign-up-pim-for-azure-ad-roles"></a>Azure AD rolleri için PıM kaydolma

Dizininiz için Privileged Identity Management etkinleştirildikten sonra, Azure AD rollerini yönetmek için Privileged Identity Management kaydolmanız gerekir.

1. **Azure AD Privileged Identity Management**açın.

1. **Azure AD rolleri**' ne tıklayın.

    ![Azure AD rolleri için Privileged Identity Management kaydolma](./media/pim-getting-started/sign-up-pim-azure-ad-roles.png)

1. **Kaydol**' a tıklayın.

1. Görüntülenen iletide, Azure AD rollerini yönetmek için Privileged Identity Management kaydolmak üzere **Evet** ' e tıklayın.

    ![Azure AD rolleri iletisi için Privileged Identity Management kaydolma](./media/pim-getting-started/sign-up-pim-message.png)

    Kaydolma tamamlandığında, Azure AD seçenekleri etkinleştirilir. Portalı yenilemeniz gerekebilir.

    Privileged Identity Management korunacak Azure kaynaklarını bulma ve seçme hakkında daha fazla bilgi için bkz. [Privileged Identity Management içinde yönetilecek Azure kaynaklarını bulma](pim-resource-roles-discover-resources.md).

## <a name="navigate-to-your-tasks"></a>Görevlerinize gitme

Privileged Identity Management ayarladıktan sonra kimlik yönetimi görevlerinizi başlatabilirsiniz.

![Görevleri gösterme ve seçenekleri yönetme Privileged Identity Management gezinti penceresi](./media/pim-getting-started/pim-quickstart-tasks.png)

| Görev + Yönet | Açıklama |
| --- | --- |
| **Rollerim**  | Size atanan uygun ve etkin rollerin listesini görüntüler. Burada atanan uygun rolleri etkinleştirebilirsiniz. |
| **İsteklerim** | Uygun rol atamalarını etkinleştirmek için bekleyen isteklerinizi görüntüler. |
| **İstekleri onaylama** | Dizininizdeki, onaylamanız için belirlediğiniz kullanıcılara uygun rolleri etkinleştirme isteklerinin listesini görüntüler. |
| **Erişimi gözden geçir** | Kendinize veya başka birine yönelik erişimi gözden geçirirken, tamamlanmak üzere atadığınız etkin erişim incelemelerini listeler. |
| **Azure AD rolleri** | Azure AD rol atamalarını yönetmek için ayrıcalıklı rol yöneticilerinin bir panosunu ve ayarlarını görüntüler. Bu pano, ayrıcalıklı rol yöneticisi olmayan kullanıcılar için devre dışıdır. Bu kullanıcılar Görünümüm adlı özel bir panoya erişebilir. Görünümüm panosu, tüm kiracı değil yalnızca panoya erişen kullanıcı hakkında bilgileri görüntüler. |
| **Azure kaynakları** | Azure Kaynak rolü atamalarını yönetmek için ayrıcalıklı rol yöneticilerinin bir panosunu ve ayarlarını görüntüler. Bu pano, ayrıcalıklı rol yöneticisi olmayan kullanıcılar için devre dışıdır. Bu kullanıcılar Görünümüm adlı özel bir panoya erişebilir. Görünümüm panosu, tüm kiracı değil yalnızca panoya erişen kullanıcı hakkında bilgileri görüntüler. |

## <a name="add-a-pim-tile-to-the-dashboard"></a>Panoya PıM kutucuğu ekleme

Privileged Identity Management açmayı kolaylaştırmak için Azure portal panonuza bir Privileged Identity Management kutucuğu eklemeniz gerekir.

1. [Azure Portal](https://portal.azure.com/)’ında oturum açın.

1. **Tüm hizmetler** ' e tıklayın ve **Azure AD Privileged Identity Management** hizmeti bulun.

    ![Tüm hizmetlerde Azure AD Privileged Identity Management](./media/pim-getting-started/pim-all-services-find.png)

1. Privileged Identity Management hızlı başlangıcı 'nı açmak için tıklayın.

1. Privileged Identity Management hızlı başlangıç dikey penceresini panoya sabitlemek için **panoya sabitle dikey penceresini** işaretleyin.

    ![Privileged Identity Management dikey pencereyi panoya sabitlemek için raptiye simgesi](./media/pim-getting-started/pim-quickstart-pin-to-dashboard.png)

    Azure panosu 'nda şöyle bir kutucuk göreceksiniz:

    ![Panoda Privileged Identity Management hızlı başlangıç kutucuğu](./media/pim-getting-started/pim-quickstart-dashboard-tile.png)

## <a name="next-steps"></a>Sonraki adımlar

- [Privileged Identity Management Azure AD rolleri atama](pim-how-to-add-role-to-user.md)
- [Privileged Identity Management içinde yönetilecek Azure kaynaklarını bulma](pim-resource-roles-discover-resources.md)
