---
title: SYNAPSE Studio 'da SYNAPSE RBAC rol atamalarını gözden geçirme
description: Bu makalede SYNAPSE Studio kullanarak SYNAPSE RBAC rol atamalarının nasıl gözden geçirkullanılacağı açıklanmaktadır
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 12/1/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 9065ca9c7638f3d2bda36e4831b81963936f4b45
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100102164"
---
# <a name="how-to-review-synapse-rbac-role-assignments"></a>SYNAPSE RBAC rol atamalarını gözden geçirme

SYNAPSE RBAC rolleri, SYNAPSE kaynaklarının erişimini ve kullanımını etkinleştirmek üzere kullanıcılara, gruplara ve diğer güvenlik ilkelerine izinler atamak için kullanılır.  [Daha fazla bilgi edinin](./synapse-workspace-synapse-rbac.md)

Bu makalede, bir çalışma alanı için geçerli rol atamalarının nasıl inceleneceği açıklanır.

Herhangi bir Synapse RBAC rolüyle, erişiminiz olmayan nesneler için Atamalar da dahil olmak üzere tüm kapsamlar için SYNAPSE RBAC rol atamalarını listeleyebilirsiniz. Yalnızca bir Synapse Yöneticisi SYNAPSE RBAC erişimine izin verebilir.  

>[!Note]
>Konuk kullanıcılar (farklı bir AD kiracısından kullanıcılar) SYNAPSE yönetici rolü atansa bile rol atamalarını göremez veya yönetemez.    

## <a name="open-synapse-studio"></a>SYNAPSE Studio 'Yu açın  

Rol atamalarını gözden geçirmek için önce [SYNAPSE Studio 'yu açın](https://web.azuresynapse.net/) ve çalışma alanınızı seçin. 

![Çalışma alanında oturum aç](./media/common/login-workspace.png) 
 
 Çalışma alanınızı açtıktan sonra sol taraftaki **Yönet** hub 'ını seçin, ardından **güvenlik** bölümünü genişletin ve **erişim denetimi**' ni seçin. 

 ![Sol taraftaki Güvenlik bölümünde Access Control seçin](./media/how-to-manage-synapse-rbac-role-assignments/left-nav-security-access-control.png)

## <a name="review-workspace-role-assignments"></a>Çalışma alanı rol atamalarını gözden geçirme

Erişim denetimi ekranı, çalışma alanının tüm geçerli rol atamalarını role göre gruplanmış olarak listeler. Her atama asıl adı, sorumlu türünü, rolü ve kapsamını içerir.

![Access Control ekranı](./media/how-to-review-synapse-rbac-role-assignments/access-control-assignments.png)

Bir sorumluya farklı kapsamlara aynı rol atanırsa, her kapsam için bir tane olmak üzere sorumlu için birden çok atama görürsünüz.  

Bir güvenlik grubuna bir rol atanmışsa, gruba açıkça atanmış rolleri ve üst gruplardan devralınan rolleri görürsünüz.  

Listeyi asıl adına veya e-postaya göre filtreleyebilir ve nesne türlerini, rolleri ve kapsamları seçmeli olarak filtreleyebilirsiniz. size atanan rolleri görmek için ad filtresine ad veya e-posta diğer adınızı girin. Rollerinizi yalnızca bir Synapse Yöneticisi değiştirebilir.

>[!Important] 
>Rol atanmış bir grubun doğrudan veya dolaylı olarak bir üyesine sahipseniz, gösterilmemiş izinleriniz olabilir.

>[!tip]
>Azure portal Azure Active Directory kullanarak Grup üyeliklerinizi bulabilirsiniz.  

Yeni bir çalışma alanı oluşturursanız, siz ve çalışma alanı MSI hizmet sorumlusu, çalışma alanı kapsamında SYNAPSE yönetici rolüne otomatik olarak verilir.

## <a name="next-steps"></a>Sonraki adımlar

[SYNAPSE RBAC rol atamalarını yönetmeyi](./how-to-manage-synapse-rbac-role-assignments.md)öğrenin.

[Belirli görevleri gerçekleştirmeniz için gereken rolü](./synapse-workspace-understand-what-role-you-need.md) öğrenin