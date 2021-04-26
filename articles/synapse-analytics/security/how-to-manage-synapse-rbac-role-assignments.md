---
title: SYNAPSE Studio 'da SYNAPSE RBAC atamalarını yönetme
description: Bu makalede, SYNAPSE RBAC rollerinin AAD güvenlik ilkelerine nasıl atanacağı ve iptal edileceği açıklanmaktadır
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 12/1/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: bed0c00b8cb5718456302dff06e98ff2f7f2b4e8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100102198"
---
# <a name="how-to-manage-synapse-rbac-role-assignments-in-synapse-studio"></a>SYNAPSE Studio 'da SYNAPSE RBAC rol atamalarını yönetme

SYNAPSE RBAC, SYNAPSE kaynaklarının ve kod yapılarının erişimini ve kullanımını sağlamak üzere kullanıcılara, gruplara ve diğer güvenlik sorumlularına izinler atamak için roller kullanır.  [Daha fazla bilgi edinin](./synapse-workspace-synapse-rbac.md)

Bu makale, SYNAPSE RBAC rol atamalarının nasıl ekleneceğini ve silineceğini gösterir.

>[!Note]
>- SYNAPSE RBAC rol atamalarını yönetmek için, çalışma alanında veya yönetmek istediğiniz nesneleri içeren alt düzey bir kapsamda SYNAPSE Yöneticisi rolüne sahip olmanız gerekir. Çalışma alanında SYNAPSE yöneticisiyseniz, çalışma alanındaki tüm nesnelere erişim izni verebilirsiniz. 
>- Farklı bir AD kiracısından gelen **Konuk kullanıcılar** , SYNAPSE yönetici rolü atansa bile rol atamalarını göremez veya yönetemez.
>- SYNAPSE yöneticileri atanmamış veya size kullanılabilir olmayan bir çalışma alanına erişimi yeniden elde etmenize yardımcı olması için, çalışma alanında **Azure RBAC** rol atamalarını yönetme izinlerine sahip olan KULLANıCıLAR **SYNAPSE RBAC** rol atamalarını yönetebilir ve bu da SYNAPSE Yöneticisi veya diğer SYNAPSE rol atamalarının eklenmesine izin verir.
>- SQL havuzlara erişim SQL izinleri kullanılarak yönetilir.  SYNAPSE Yöneticisi ve SYNAPSE SQL yönetici rolleri dışında, SYNAPSE RBAC rolleri SQL havuzlarına erişim vermez.

>[!important]
>- SYNAPSE RBAC rol atamalarında yapılan değişikliklerin etkili olması 2-5 dakika sürebilir. 
>- Güvenlik gruplarının üyeliğini değiştirerek SYNAPSE RBAC izinlerini yönetiyorsanız, üyelikte yapılan değişiklikler Azure Active Directory kullanılarak yönetilir.  Grup üyeliklerinde yapılan değişikliklerin etkili olması 10-15 dakika veya daha uzun sürebilir.

## <a name="open-synapse-studio"></a>SYNAPSE Studio 'Yu açın  

Bir Kullanıcı, Grup, hizmet sorumlusu veya yönetilen kimliğe bir rol atamak için önce [SYNAPSE Studio 'yu açın](https://web.azuresynapse.net/) ve çalışma alanınızı seçin. 

![Çalışma alanında oturum aç](./media/common/login-workspace.png) 
 
 Çalışma alanınızı açtıktan sonra sol taraftaki **güvenlik** bölümünü genişletin ve **erişim denetimi**' ni seçin. 

 ![Sol taraftaki Güvenlik bölümünde Access Control seçin](./media/how-to-manage-synapse-rbac-role-assignments/left-nav-security-access-control.png)

Erişim denetimi ekranında geçerli rol atamaları listelenir.  Listeyi asıl adına veya e-postaya göre filtreleyebilir ve nesne türlerini, rolleri veya dahil edilen kapsamları seçmeli olarak filtreleyebilirsiniz. Bu ekrandan, rol atamaları ekleyebilir veya kaldırabilirsiniz.  

## <a name="add-a-synapse-role-assignment"></a>SYNAPSE rol ataması ekleme

Erişim denetimi ekranında, yeni bir rol ataması oluşturmak için **+ Ekle** ' yi seçin

![+ Ekle ' ye tıklayarak yeni bir rol ataması oluşturun](./media/how-to-manage-synapse-rbac-role-assignments/access-control-add.png)

Rol ataması Ekle sekmesinde, çalışma alanı kapsamında veya çalışma alanı öğe kapsamında rol atamaları oluşturabilirsiniz. 

## <a name="add-workspace-scoped-role-assignment"></a>Çalışma alanı kapsamlı rol ataması Ekle

İlk olarak, kapsam olarak **çalışma alanı** ' nı seçin, sonra **SYNAPSE RBAC rolünü** seçin.  Rolün atanacağı **sorumluyu** seçin ve ardından rol atamalarını oluşturun. 

![Çalışma alanı rol ataması Ekle-rol seçin](./media/how-to-manage-synapse-rbac-role-assignments/access-control-workspace-role-assignment.png) 

Atanan rol, çalışma alanındaki tüm ilgili nesneler için geçerli olacaktır.

## <a name="add-workspace-item-scoped-role-assignment"></a>Çalışma alanı öğesi kapsamlı rol ataması Ekle

Daha ayrıntılı bir kapsamda rol atamak için, kapsam olarak **çalışma alanı öğesini** seçin ve ardından kapsam **öğe türünü** seçin.       

![Çalışma alanı öğesi rolü ataması ekleme-öğe türünü seçme](./media/how-to-manage-synapse-rbac-role-assignments/access-control-add-workspace-item-assignment-select-item-type.png) 

Kapsam olarak kullanılacak özel **öğeyi** seçin ve ardından açılır listeden atanacak **rolü** seçin.  Açılan listede yalnızca seçili öğe türü için geçerli olan roller listelenir. [Daha fazla bilgi edinin](./synapse-workspace-synapse-rbac.md).  

![Çalışma alanı öğesi rolü ataması ekleme-rol seçme](./media/how-to-manage-synapse-rbac-role-assignments/access-control-add-workspace-item-assignment-select-role.png) 
 
Ardından rolün atanacağı **sorumluyu seçin** .  Birden çok sorumlusu seçerek seçebilirsiniz.  Rol atamalarını oluşturmak için **Uygula** ' yı seçin.

## <a name="remove-a-synapse-rbac-role-assignment"></a>SYNAPSE RBAC rol atamasını kaldırma

SYNAPSE RBAC erişimini iptal etmek için uygun rol atamalarını kaldırırsınız.  Erişim denetimi ekranında, kaldırılacak rol atamalarını bulmak için filtreleri kullanın.  Rol atamalarını denetleyip, **erişimi kaldır**' ı seçin.   

![Erişimi kaldırmak için bir rol atamasını silme](./media/how-to-manage-synapse-rbac-role-assignments/access-control-remove-access.png)

Rol atamalarındaki değişikliklerin etkili olması için 2-5 dakika olacağını unutmayın.   

## <a name="next-steps"></a>Sonraki adımlar

[Ortak görevleri gerçekleştirmek için gereken SYNAPSE RBAC rollerini anlayın](./synapse-workspace-understand-what-role-you-need.md)