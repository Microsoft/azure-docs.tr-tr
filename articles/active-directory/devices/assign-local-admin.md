---
title: Azure AD 'ye katılmış cihazlarda yerel Yöneticiler grubunu yönetme | Microsoft Docs
description: Azure rollerini bir Windows cihazının yerel Yöneticiler grubuna atamayı öğrenin.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: ravenn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5372bf853092ea55952b45a1300d0f979f1b086c
ms.sourcegitcommit: f7f70c9bd6c2253860e346245d6e2d8a85e8a91b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73062269"
---
# <a name="how-to-manage-the-local-administrators-group-on-azure-ad-joined-devices"></a>Azure AD 'ye katılmış cihazlarda yerel Yöneticiler grubunu yönetme

Bir Windows cihazını yönetmek için yerel Yöneticiler grubunun bir üyesi olmanız gerekir. Azure Active Directory (Azure AD) JOIN işleminin bir parçası olarak, Azure AD bu grubun üyeliğini bir cihazda güncelleştirir. Üyelik güncelleştirmesini, iş gereksinimlerinizi karşılayacak şekilde özelleştirebilirsiniz. Bir üyelik güncelleştirmesi, örneğin, yardım masası personelinizin bir cihazda yönetici hakları gerektiren görevler kullanmasını sağlamak istiyorsanız yararlıdır.

Bu makalede, üyelik güncelleştirmesinin nasıl çalıştığı ve bir Azure AD birleşimi sırasında nasıl özelleştirileceği açıklanır. Bu makalenin içeriği, **karma** BIR Azure AD katılımı için geçerlidir.

## <a name="how-it-works"></a>Nasıl çalışır

Azure AD JOIN kullanarak bir Windows cihazını Azure AD 'ye bağladığınızda, Azure AD, cihazdaki yerel Yöneticiler grubuna aşağıdaki güvenlik ilkelerini ekler:

- Azure AD Genel Yöneticisi rolü
- Azure AD Cihaz Yöneticisi rolü 
- Azure AD JOIN 'i gerçekleştiren Kullanıcı   

Yerel Yöneticiler grubuna Azure AD rolleri ekleyerek, cihazdaki herhangi bir şeyi değiştirmeden bir cihazı Azure AD 'de yönetebileceğini kullanıcıları güncelleştirebilirsiniz. Şu anda, bir yönetici rolüne gruplar atayamazsınız.
Azure AD, en az ayrıcalık (PoLP) ilkesini desteklemek için Azure AD cihaz yöneticisi rolünü yerel Yöneticiler grubuna da ekler. Genel yöneticilere ek olarak, bir cihazı yönetmek için *yalnızca* cihaz yöneticisi rolüne atanan kullanıcılara da izin verebilirsiniz. 

## <a name="manage-the-global-administrators-role"></a>Genel Yöneticiler rolünü yönetme

Genel yönetici rolünün üyeliğini görüntülemek ve güncelleştirmek için bkz.:

- [Azure Active Directory bir yönetici rolünün tüm üyelerini görüntüleme](../users-groups-roles/directory-manage-roles-portal.md)
- [Azure Active Directory 'da yönetici rollerine Kullanıcı atama](../fundamentals/active-directory-users-assign-role-azure-portal.md)


## <a name="manage-the-device-administrator-role"></a>Cihaz Yöneticisi rolünü yönetme 

Azure portal, **cihazlar** sayfasında cihaz yöneticisi rolünü yönetebilirsiniz. **Cihazlar** sayfasını açmak için:

1. [Azure Portal](https://portal.azure.com) genel yönetici veya cihaz yöneticisi olarak oturum açın.
1. Sol gezinti çubuğunda **Azure Active Directory**' ye tıklayın. 
1. **Yönet** bölümünde, **cihazlar**' a tıklayın.
1. **Cihazlar** sayfasında, **cihaz ayarları**' na tıklayın.

Cihaz Yöneticisi rolünü değiştirmek için, **Azure AD 'ye katılmış cihazlarda ek yerel Yöneticiler**yapılandırın.  

![Ek yerel Yöneticiler](./media/assign-local-admin/10.png)

>[!NOTE]
> Bu seçenek Azure AD Premium kiracı gerektirir. 

Cihaz yöneticileri tüm Azure AD 'ye katılmış cihazlara atanır. Cihaz yöneticilerini belirli bir cihaz kümesiyle kapsamyükleyemezsiniz. Cihaz Yöneticisi rolünü güncelleştirme, etkilenen kullanıcılar üzerinde bir etkisi olması gerekmez. Bir kullanıcının zaten oturum açmış olduğu cihazlarda, aşağıdaki *iki* eylem gerçekleştiğinde ayrıcalık güncelleştirmesi gerçekleşir:

- Azure AD 'nin uygun ayrıcalıklara sahip yeni bir birincil yenileme belirteci vermesi için 4 saat geçti. 
- Kullanıcı oturumu kapatır ve kendi profilini yenilemek için kilitleme/kilit açma değil, oturum kapatır.

## <a name="manage-regular-users"></a>Normal kullanıcıları yönetme

Azure AD, varsayılan olarak, Azure AD JOIN 'in cihazdaki yönetici grubuna eklediği kullanıcıyı ekler. Normal kullanıcıların yerel yönetici haline gelmesini engellemek istiyorsanız aşağıdaki seçeneklere sahipsiniz:

- [Windows Autopilot](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-10-autopilot) -Windows Autopilot, birincil kullanıcının bir yerel yönetici haline gelmesini önleyen bir seçenek sunar. Bunu, [bir Autopilot profili oluşturarak](https://docs.microsoft.com/intune/enrollment-autopilot#create-an-autopilot-deployment-profile)gerçekleştirebilirsiniz.
- [Toplu kayıt](https://docs.microsoft.com/intune/windows-bulk-enroll) -bir toplu kayıt bağlamında gerçekleştirilen BIR Azure AD JOIN, otomatik olarak oluşturulan bir kullanıcı bağlamında gerçekleşir. Bir cihaz birleştirildikten sonra oturum açan kullanıcılar Yöneticiler grubuna eklenmez.   

## <a name="manually-elevate-a-user-on-a-device"></a>Bir cihazdaki kullanıcıyı el ile yükseltme 

Azure AD JOIN işlemini kullanmanın yanı sıra, düzenli bir kullanıcıyı belirli bir cihazda yerel yönetici olacak şekilde el ile de indirebilirsiniz. Bu adım, yerel Yöneticiler grubunun zaten bir üyesi olmanızı gerektirir. 

**Windows 10 1709** sürümünden başlayarak bu görevi **Ayarlar-> hesaplarından (diğer kullanıcılar >** gerçekleştirebilirsiniz. **İş veya okul kullanıcısı ekle**' yi seçin, kullanıcının **Kullanıcı HESABı** bölümüne ait UPN 'sini girin ve **Hesap türü** altında *yönetici* ' yi seçin.  
 
Ayrıca, komut istemi kullanarak da kullanıcı ekleyebilirsiniz:

- Kiracı kullanıcılarınız şirket içi Active Directory eşitlendiğinde, `net localgroup administrators /add "Contoso\username"`kullanın.
- Kiracı kullanıcılarınız Azure AD 'de oluşturulduysa, `net localgroup administrators /add "AzureAD\UserUpn"` kullanın

## <a name="considerations"></a>Dikkat edilmesi gerekenler 

Cihaz Yöneticisi rolüne Grup atayamazsınız, yalnızca bireysel kullanıcılara izin verilir.

Cihaz yöneticileri tüm Azure AD 'ye katılmış cihazlara atanır. Bunlar, belirli bir cihaz kümesinin kapsamına alınamaz.

Kullanıcıları Cihaz Yöneticisi rolünden kaldırdığınızda, sunucuda oturum açtıkları sürece bir cihazda yerel yönetici ayrıcalığına sahip olmaya devam eder. Bu ayrıcalık bir sonraki oturum açma işlemi sırasında veya yeni bir birincil yenileme belirteci verildiğinde 4 saat sonra iptal edilir.

## <a name="next-steps"></a>Sonraki adımlar

- Azure portal'da cihaz yönetimine ilişkin genel bir bakış edinmek için bkz. [Azure portal'ı kullanarak cihazları yönetme](device-management-azure-portal.md)
- Cihaz tabanlı koşullu erişim hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory cihaz tabanlı koşullu erişim ilkelerini yapılandırma](../conditional-access/require-managed-devices.md).
