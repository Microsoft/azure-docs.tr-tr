---
title: Azure Lab Services | sınıf Laboratuvarı VM 'lerinin parolalarını sıfırlama | Microsoft Docs
description: Azure Lab Services derslik laboratuvarlarında sanal makinelerin (VM) parolalarını sıfırlamayı öğrenin.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2019
ms.author: spelluru
ms.openlocfilehash: 7c757ef8508f9364a46116e6ddf19207f23a4b6f
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73583689"
---
# <a name="set-or-reset-password-for-virtual-machines-in-classroom-labs-students"></a>Sınıf laboratuvarlarında (öğrenciler) sanal makineler için parola ayarlama veya sıfırlama
Bu makalede, öğrencilerin VM 'Leri için parola ayarlama/sıfırlama işlemlerinin nasıl yapılacağı gösterilir. 

## <a name="enable-resetting-of-passwords"></a>Parolaların sıfırlanmasını etkinleştir
Laboratuvarın oluşturulması sırasında, laboratuvar sahibi **tüm sanal makineler için aynı parolayı kullan**seçeneğini etkinleştirebilir veya devre dışı bırakabilir. Bu seçenek etkinleştirilirse, öğrenciler parolayı sıfırlayamaz. Laboratuvardaki tüm VM 'Ler, eğitmen tarafından ayarlanan parolaya sahip olacaktır. 

Bu seçenek devre dışıysa, kullanıcıların VM 'yi ilk kez bağlamaya çalışırken bir parola ayarlaması gerekir. Öğrenciler ayrıca, bu makalenin son bölümünde gösterildiği gibi, parolayı daha sonra istediğiniz zaman sıfırlayabilirler. 

## <a name="reset-password-for-the-first-time"></a>Parolayı ilk kez Sıfırla
**Tüm sanal makineler için aynı parolayı kullan** seçeneği devre dışıysa, kullanıcılar (öğrenciler) **sanal makinelerim** sayfasında laboratuvar kutucuğunda **Bağlan** düğmesini seçtiğinde, Kullanıcı VM 'nin parolasını ayarlamak için aşağıdaki iletişim kutusunu görür: 

![Öğrenci parolasını sıfırlama](../media/how-to-set-virtual-machine-passwords/student-set-password.png)

## <a name="reset-password-later"></a>Parolayı daha sonra Sıfırla
Öğrenci ayrıca laboratuvar kutucuğunda taşma menüsüne (**dikey üç nokta**) tıklayarak ve **Parolayı Sıfırla**' yı seçerek parolayı ayarlayabilir. 

![Parolayı daha sonra Sıfırla](../media/how-to-set-virtual-machine-passwords/student-set-password-2.png)


## <a name="next-steps"></a>Sonraki adımlar
Bir laboratuvar sahibinin yapılandıray, diğer öğrenci kullanım seçenekleri hakkında bilgi edinmek için şu makaleye bakın: [öğrenci kullanımını yapılandırma](how-to-configure-student-usage.md).
