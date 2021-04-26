---
title: Azure portal Kullanıcı tarafından atanan yönetilen kimliği yönetme-Azure AD
description: Kullanıcı tarafından atanan yönetilen kimlik için rol oluşturma, listeleme, silme ve atama ile ilgili adım adım yönergeler.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/26/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: fd4cccdfc3a2cf8dd3827776028a7738ae769673
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98184839"
---
# <a name="create-list-delete-or-assign-a-role-to-a-user-assigned-managed-identity-using-the-azure-portal"></a>Azure portal kullanarak Kullanıcı tarafından atanan yönetilen kimlik için rol oluşturma, listeleme, silme veya atama

Azure kaynakları için Yönetilen kimlikler, Azure Active Directory yönetilen bir kimlik ile Azure hizmetleri sağlar. Kodunuzda kimlik bilgileri gerekmeden Azure AD kimlik doğrulamasını destekleyen hizmetlere kimlik doğrulaması yapmak için bu kimliği kullanabilirsiniz. 

Bu makalede, Azure portal kullanarak Kullanıcı tarafından atanan yönetilen kimlik için rol oluşturma, listeleme, silme veya atamayı öğreneceksiniz.

## <a name="prerequisites"></a>Önkoşullar

- Azure kaynakları için Yönetilen kimlikler hakkında bilginiz varsa [genel bakış bölümüne](overview.md)bakın. **[Sistem tarafından atanan ve Kullanıcı tarafından atanan yönetilen kimlik arasındaki farkı](overview.md#managed-identity-types)gözden geçirdiğinizden emin** olun.
- Henüz bir Azure hesabınız yoksa, devam etmeden önce [ücretsiz bir hesaba kaydolun](https://azure.microsoft.com/free/).

## <a name="create-a-user-assigned-managed-identity"></a>Kullanıcı tarafından atanan yönetilen kimlik oluşturma

Kullanıcı tarafından atanan yönetilen kimlik oluşturmak için hesabınıza [yönetilen kimlik katılımcısı](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) rol ataması gerekir.

1. Kullanıcı tarafından atanan yönetilen kimliği oluşturmak için Azure aboneliğiyle ilişkili bir hesabı kullanarak [Azure Portal](https://portal.azure.com) oturum açın.
2. Arama kutusuna *Yönetilen kimlikler* yazın ve **Hizmetler**' ın altında, **Yönetilen kimlikler**' e tıklayın.
3. **Ekle** ' ye tıklayın ve **Kullanıcı tarafından atanan yönetilen kimlik oluştur** altında aşağıdaki alanlara değerleri girin:
    - **Abonelik**: Kullanıcı tarafından atanan yönetilen kimliği oluşturmak için aboneliği seçin.
    - **Kaynak grubu**: Kullanıcı tarafından atanan yönetilen kimliği oluşturmak için bir kaynak grubu seçin veya yeni bir kaynak grubu oluşturmak Için **Yeni oluştur** ' a tıklayın.
    - **Bölge**: Kullanıcı tarafından atanan yönetilen kimliği dağıtmak için bir bölge seçin, örneğin **Batı ABD**.
    - **Ad**: Kullanıcı tarafından atanan yönetilen kimliğinizin adı, örneğin UAI1.
    ![Kullanıcı tarafından atanan yönetilen kimlik oluşturma](./media/how-to-manage-ua-identity-portal/create-user-assigned-managed-identity-portal.png)
4. Değişiklikleri gözden geçirmek için **gözden geçir + oluştur** ' a tıklayın.
5. **Oluştur**’a tıklayın.

## <a name="list-user-assigned-managed-identities"></a>Kullanıcı tarafından atanan yönetilen kimlikleri listeleme

Kullanıcı tarafından atanan yönetilen kimliği listelemek/okumak için hesabınızın [yönetilen kimlik işleci](../../role-based-access-control/built-in-roles.md#managed-identity-operator) veya [yönetilen kimlik katılımcısı](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) rolü ataması gerekir.

1. Kullanıcı tarafından atanan yönetilen kimlikleri listelemek için Azure aboneliğiyle ilişkili bir hesabı kullanarak [Azure Portal](https://portal.azure.com) oturum açın.
2. Arama kutusuna *Yönetilen kimlikler* yazın ve hizmetler ' in altında, **Yönetilen kimlikler**' e tıklayın.
3. Aboneliğiniz için Kullanıcı tarafından atanan yönetilen kimliklerin bir listesi döndürülür.  Kullanıcı tarafından atanan yönetilen kimliğin ayrıntılarını görmek için adına tıklayın.

![Kullanıcı tarafından atanan yönetilen kimliği listeleme](./media/how-to-manage-ua-identity-portal/list-user-assigned-managed-identity-portal.png)

## <a name="delete-a-user-assigned-managed-identity"></a>Kullanıcı tarafından atanan yönetilen kimliği silme

Kullanıcı tarafından atanan bir yönetilen kimliği silmek için hesabınıza [yönetilen kimlik katılımcısı](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) rol ataması gerekir.

Kullanıcı tarafından atanan bir kimlik silindiğinde, atandığı VM veya kaynaktan kaldırılmaz.  Kullanıcı tarafından atanan kimliği bir VM 'den kaldırmak için bkz. [Kullanıcı tarafından atanan bir yönetilen kimliği BIR VM 'Den kaldırma](./qs-configure-portal-windows-vm.md#remove-a-user-assigned-managed-identity-from-a-vm).

1. Kullanıcı tarafından atanan yönetilen kimliği silmek için Azure aboneliğiyle ilişkili bir hesabı kullanarak [Azure Portal](https://portal.azure.com) oturum açın.
2. Kullanıcı tarafından atanan yönetilen kimliği seçin ve **Sil**' e tıklayın.
3. Onay kutusunun altında, **Evet**' i seçin.

![Kullanıcı tarafından atanan yönetilen kimliği Sil](./media/how-to-manage-ua-identity-portal/delete-user-assigned-managed-identity-portal.png)

## <a name="assign-a-role-to-a-user-assigned-managed-identity"></a>Kullanıcı tarafından atanan yönetilen kimliğe rol atama 

Kullanıcı tarafından atanan yönetilen kimliğe bir rol atamak için hesabınızın [Kullanıcı erişimi yönetici](../../role-based-access-control/built-in-roles.md#user-access-administrator) rolü ataması gerekir.

1. Kullanıcı tarafından atanan yönetilen kimlikleri listelemek için Azure aboneliğiyle ilişkili bir hesabı kullanarak [Azure Portal](https://portal.azure.com) oturum açın.
2. Arama kutusuna *Yönetilen kimlikler* yazın ve hizmetler ' in altında, **Yönetilen kimlikler**' e tıklayın.
3. Aboneliğiniz için Kullanıcı tarafından atanan yönetilen kimliklerin bir listesi döndürülür.  Rol atamak istediğiniz kullanıcı tarafından atanan yönetilen kimliği seçin.
4. **Erişim denetimi (IAM)** öğesini seçin ve ardından **rol ataması Ekle**' yi seçin.

   ![Kullanıcı tarafından atanan yönetilen kimlik başlangıcı](./media/how-to-manage-ua-identity-portal/assign-role-screenshot1.png)

5. Rol ataması Ekle dikey penceresinde aşağıdaki değerleri yapılandırın ve **Kaydet**' e tıklayın:
   - **Rol** -atanacak rol
   - Kullanıcı tarafından atanan yönetilen kimliği atamak için kaynağa **erişim atama**
   - **Select** -erişim atanacak üye
   
   ![Kullanıcı tarafından atanan yönetilen kimlik ıAM](./media/how-to-manage-ua-identity-portal/assign-role-screenshot2.png)
