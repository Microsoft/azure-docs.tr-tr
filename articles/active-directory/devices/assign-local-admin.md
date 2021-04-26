---
title: Azure AD 'ye katılmış cihazlarda yerel yöneticileri yönetme
description: Azure rollerini bir Windows cihazının yerel Yöneticiler grubuna atamayı öğrenin.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: ravenn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 806ff92fcf75ff8d1c8e092d7ff4435751a9e7db
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107529901"
---
# <a name="how-to-manage-the-local-administrators-group-on-azure-ad-joined-devices"></a>Azure AD 'ye katılmış cihazlarda yerel Yöneticiler grubunu yönetme

Bir Windows cihazını yönetmek için yerel Yöneticiler grubunun bir üyesi olmanız gerekir. Azure Active Directory (Azure AD) JOIN işleminin bir parçası olarak, Azure AD bu grubun üyeliğini bir cihazda güncelleştirir. Üyelik güncelleştirmesini, iş gereksinimlerinizi karşılayacak şekilde özelleştirebilirsiniz. Bir üyelik güncelleştirmesi, örneğin, yardım masası personelinizin bir cihazda yönetici hakları gerektiren görevler kullanmasını sağlamak istiyorsanız yararlıdır.

Bu makalede, yerel yönetici üyeliği güncelleştirmesinin nasıl çalıştığı ve bir Azure AD birleşimi sırasında nasıl özelleştirebileceğiniz açıklanmaktadır. Bu makalenin içeriği, **karma bir Azure AD 'ye katılmış** cihazlar için geçerlidir.

## <a name="how-it-works"></a>Nasıl çalışır?

Azure AD JOIN kullanarak bir Windows cihazını Azure AD 'ye bağladığınızda, Azure AD aşağıdaki güvenlik sorumlularını cihazdaki yerel Yöneticiler grubuna ekler:

- Azure AD Genel Yöneticisi rolü
- Azure AD Cihaz Yöneticisi rolü 
- Azure AD JOIN 'i gerçekleştiren Kullanıcı   

Yerel Yöneticiler grubuna Azure AD rolleri ekleyerek, cihazdaki herhangi bir şeyi değiştirmeden bir cihazı Azure AD 'de yönetebileceğini kullanıcıları güncelleştirebilirsiniz. Azure AD, en az ayrıcalık (PoLP) ilkesini desteklemek için Azure AD cihaz yöneticisi rolünü yerel Yöneticiler grubuna da ekler. Genel yöneticilere ek olarak, bir cihazı yönetmek için *yalnızca* cihaz yöneticisi rolüne atanan kullanıcılara da izin verebilirsiniz. 

## <a name="manage-the-global-administrators-role"></a>Genel Yöneticiler rolünü yönetme

Genel yönetici rolünün üyeliğini görüntülemek ve güncelleştirmek için bkz.:

- [Azure Active Directory bir yönetici rolünün tüm üyelerini görüntüleme](../roles/manage-roles-portal.md)
- [Azure Active Directory’de kullanıcılara yönetici rolü atama](../fundamentals/active-directory-users-assign-role-azure-portal.md)


## <a name="manage-the-device-administrator-role"></a>Cihaz Yöneticisi rolünü yönetme 

Azure portal, **cihazlar** sayfasında cihaz yöneticisi rolünü yönetebilirsiniz. **Cihazlar** sayfasını açmak için:

1. [Azure Portal](https://portal.azure.com) genel yönetici olarak oturum açın.
1. *Azure Active Directory*'yi bulun ve seçin.
1. **Yönet** bölümünde, **cihazlar**' a tıklayın.
1. **Cihazlar** sayfasında, **cihaz ayarları**' na tıklayın.

Cihaz Yöneticisi rolünü değiştirmek için, **Azure AD 'ye katılmış cihazlarda ek yerel Yöneticiler** yapılandırın.  

![Ek yerel Yöneticiler](./media/assign-local-admin/10.png)

>[!NOTE]
> Bu seçenek Azure AD Premium kiracı gerektirir. 

Cihaz yöneticileri tüm Azure AD 'ye katılmış cihazlara atanır. Cihaz yöneticilerini belirli bir cihaz kümesiyle kapsamyükleyemezsiniz. Cihaz Yöneticisi rolünü güncelleştirme, etkilenen kullanıcılar üzerinde bir etkisi olması gerekmez. Bir kullanıcının zaten oturum açmış olduğu cihazlarda, aşağıdaki *her iki* eylem gerçekleştiğinde ayrıcalık yükselme gerçekleşir:

- Azure AD 'nin uygun ayrıcalıklara sahip yeni bir birincil yenileme belirteci vermesi için 4 saat geçti. 
- Kullanıcı oturumu kapatır ve kendi profilini yenilemek için kilitleme/kilit açma değil, oturum kapatır.

>[!NOTE]
> Yukarıdaki eylemler, ilgili cihazda daha önce oturum açmamış kullanıcılar için geçerli değildir. Bu durumda, yönetici ayrıcalıkları cihazdaki ilk oturum açtıktan hemen sonra uygulanır. 

## <a name="manage-administrator-privileges-using-azure-ad-groups-preview"></a>Azure AD gruplarını kullanarak yönetici ayrıcalıklarını yönetme (Önizleme)

Windows 10 sürüm 2004 ' den başlayarak, Azure AD 'ye katılmış cihazlarda [Kısıtlanmış Gruplar](/windows/client-management/mdm/policy-csp-restrictedgroups) MDM ilkesiyle yönetici ayrıcalıklarını yönetmek IÇIN Azure AD gruplarını kullanabilirsiniz. Bu ilke, Azure AD 'ye katılmış bir cihazdaki tek tek kullanıcıları veya Azure AD gruplarını yerel Yöneticiler grubuna atamanıza izin verir ve farklı cihaz grupları için ayrı Yöneticiler yapılandırmak için ayrıntı düzeyi sağlar. 

>[!NOTE]
> Windows 10 20 H2 güncelleştirmesi başlatılıyor, Kısıtlanmış Gruplar ilkesi yerine [yerel kullanıcılar ve gruplar](/windows/client-management/mdm/policy-csp-localusersandgroups) ilkesini kullanmanızı öneririz


Şu anda, bu ilkeleri yönetmek için Intune 'da bir kullanıcı arabirimi yoktur ve [özel OMA-URI ayarları](/mem/intune/configuration/custom-settings-windows-10)kullanılarak yapılandırılması gerekir. Şu ilkelerden birini kullanmaya yönelik birkaç önemli noktalar: 

- İlke aracılığıyla Azure AD grupları eklemek, [gruplar için MICROSOFT Graph API](/graph/api/resources/group)'sini yürüterek grubun SID 'sinin elde edilebilir olmasını gerektirir. SID, API yanıtında özelliği tarafından tanımlanır `securityIdentifier` .
- Kısıtlanmış Gruplar ilkesi zorlandığında, grubun Üyeler listesinde olmayan geçerli üyeleri kaldırılır. Bu nedenle, bu ilkeyi yeni üyeler veya gruplarla uygulamak, cihazı, cihaz yöneticisi rolünü ve genel yönetici rolünü cihazdan katılmış olan mevcut yöneticiler olarak kaldırır. Mevcut üyeleri kaldırmayı önlemek için, onları kısıtlı gruplar ilkesindeki Üyeler listesinin bir parçası olarak yapılandırmanız gerekir. Grup üyeliğine artımlı güncelleştirmelere izin veren yerel kullanıcılar ve Gruplar ilkesini kullanıyorsanız bu sınırlama ele alınmalıdır
- Her iki ilkeyi kullanan yönetici ayrıcalıkları yalnızca bir Windows 10 cihaz-Yöneticiler, kullanıcılar, konuklar, Power Users, uzak masaüstü kullanıcıları ve uzaktan yönetim kullanıcıları için, yalnızca aşağıdaki bilinen gruplar için değerlendirilir. 
- Azure AD gruplarını kullanarak yerel yöneticileri yönetmek, karma Azure AD 'ye katılmış veya Azure AD 'ye kayıtlı cihazlar için geçerli değildir.
- Kısıtlı Gruplar ilkesi Windows 10 sürüm 2004 ' den önce mevcut olsa da, Azure AD gruplarını bir cihazın yerel Yöneticiler grubunun üyesi olarak desteklemez. 

## <a name="manage-regular-users"></a>Normal kullanıcıları yönetme

Azure AD, varsayılan olarak, Azure AD JOIN 'in cihazdaki yönetici grubuna eklediği kullanıcıyı ekler. Normal kullanıcıların yerel yönetici haline gelmesini engellemek istiyorsanız aşağıdaki seçeneklere sahipsiniz:

- [Windows Autopilot](/windows/deployment/windows-autopilot/windows-10-autopilot) -Windows Autopilot, birincil kullanıcının bir yerel yönetici haline gelmesini önleyen bir seçenek sunar. Bunu, [bir Autopilot profili oluşturarak](/intune/enrollment-autopilot#create-an-autopilot-deployment-profile)gerçekleştirebilirsiniz.
- [Toplu kayıt](/intune/windows-bulk-enroll) -bir toplu kayıt bağlamında gerçekleştirilen BIR Azure AD JOIN, otomatik olarak oluşturulan bir kullanıcı bağlamında gerçekleşir. Bir cihaz birleştirildikten sonra oturum açan kullanıcılar Yöneticiler grubuna eklenmez.   

## <a name="manually-elevate-a-user-on-a-device"></a>Bir cihazdaki kullanıcıyı el ile yükseltme 

Azure AD JOIN işlemini kullanmanın yanı sıra, düzenli bir kullanıcıyı belirli bir cihazda yerel yönetici olacak şekilde el ile de indirebilirsiniz. Bu adım, yerel Yöneticiler grubunun zaten bir üyesi olmanızı gerektirir. 

**Windows 10 1709** sürümünden başlayarak bu görevi **Ayarlar-> hesaplarından (diğer kullanıcılar >** gerçekleştirebilirsiniz. **İş veya okul kullanıcısı ekle**' yi seçin, kullanıcının **Kullanıcı HESABı** bölümüne ait UPN 'sini girin ve **Hesap türü** altında *yönetici* ' yi seçin.  
 
Ayrıca, komut istemi kullanarak da kullanıcı ekleyebilirsiniz:

- Kiracı kullanıcılarınız şirket içi Active Directory eşitlendiğinde, kullanın `net localgroup administrators /add "Contoso\username"` .
- Azure AD 'de kiracı kullanıcılarınız oluşturulduysa, şunu kullanın `net localgroup administrators /add "AzureAD\UserUpn"`

## <a name="considerations"></a>Dikkat edilmesi gerekenler 

Cihaz Yöneticisi rolüne Grup atayamazsınız, yalnızca bireysel kullanıcılara izin verilir.

Cihaz yöneticileri tüm Azure AD 'ye katılmış cihazlara atanır. Bunlar, belirli bir cihaz kümesinin kapsamına alınamaz.

Kullanıcıları Cihaz Yöneticisi rolünden kaldırdığınızda, sunucuda oturum açtıkları sürece bir cihazda yerel yönetici ayrıcalığına sahip olmaya devam eder. Yeni bir birincil yenileme belirteci verildiğinde, bir sonraki oturum açma işlemi sırasında ayrıcalık iptal edilir. Ayrıcalık yükselmesine benzer olan bu iptal, 4 saate kadar sürebilir.

## <a name="next-steps"></a>Sonraki adımlar

- Azure portal'da cihaz yönetimine ilişkin genel bir bakış edinmek için bkz. [Azure portal'ı kullanarak cihazları yönetme](device-management-azure-portal.md)
- Cihaz tabanlı koşullu erişim hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory cihaz tabanlı koşullu erişim ilkelerini yapılandırma](../conditional-access/require-managed-devices.md).
