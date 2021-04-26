---
title: Azure AD Connect bulut eşitlemesi yanlışlıkla silmeler
description: Bu konuda, silme işlemini engellemek için yanlışlıkla silme özelliğinin nasıl kullanılacağı açıklanmaktadır.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 01/25/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0da54bd28c1d9ea933e88b6c86cf6092c10d036a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98785247"
---
# <a name="accidental-delete-prevention"></a>Yanlışlıkla silme engellemesi

Aşağıdaki belge Azure AD Connect bulut eşitlemesine yönelik yanlışlıkla silme özelliğini açıklamaktadır.  Yanlışlıkla silme özelliği, çok sayıda kullanıcı ve grubu etkileyecek şirket içi dizininizde yanlışlıkla yapılan yapılandırma değişikliklerinden ve değişikliklerden korunmak için tasarlanmıştır.  Bu özellik şunları yapmanıza olanak sağlar:

- yanlışlıkla silmeleri otomatik olarak engelleme özelliğini yapılandırın. 
- Yapılandırmanın yürürlüğe alınacağı nesne (eşik) sayısını ayarlayın 
- söz konusu senaryo için söz konusu eşitleme işi karantinaya alındıktan sonra bir e-posta bildirimi almak için bir bildirim e-posta adresi ayarlayın 

Bu özelliği kullanmak için, eşitleme durmak gerekirse, bu özellik için eşiği ayarlarsınız.  Bu sayıya ulaşıldığında, eşitleme durdurulur ve belirtilen e-postaya bir bildirim gönderilir.  Bu bildirim, neler olduğunu araştırmanıza olanak sağlar.


## <a name="configure-accidental-delete-prevention"></a>Yanlışlıkla silme engellemesini yapılandırma
Yeni özelliği kullanmak için aşağıdaki adımları izleyin.


1.  Azure portalında **Azure Active Directory** seçeneğini belirleyin.
2.  **Azure AD Connect** seçin.
3.  **Bulut eşitlemesini Yönet**' i seçin.
4. **Yapılandırma** altında yapılandırmanızı seçin.
5. **Ayarlar** altında şunları girin:
    - **Bildirim e-postası** -e-posta bildirimleri için kullanılır
    - **Yanlışlıkla Silmeleri engelle** -özelliği etkinleştirmek için bu kutuyu işaretleyin
    - **Yanlışlıkla silme eşiği** -eşitlemeye durdurulacak nesne sayısını girin ve bildirim gönderin

![Yanlışlıkla silme](media/how-to-accidental-deletes/accident-1.png)

## <a name="recovering-from-an-accidental-delete-instance"></a>Yanlışlıkla silme örneğinden kurtarma
Yanlışlıkla silme işlemi yaşarsanız, bunu sağlama Aracısı yapılandırmanızın durumunda görürsünüz.  Bu, **silme eşiğinin aşıldığını** söylüyor.
 
![Yanlışlıkla silme durumu](media/how-to-accidental-deletes/delete-1.png)

**Delete eşiğini** tıklatarak, eşitleme durum bilgilerini görürsünüz.  Bu, ek ayrıntılar sağlar. 
 
 ![Eşitleme durumu](media/how-to-accidental-deletes/delete-2.png)

Üç nokta işaretine sağ tıklayıp aşağıdaki seçenekleri görürsünüz:
 - Sağlama günlüğünü görüntüle
 - Aracıyı görüntüle
 - Silmeleri izin ver

 ![Sağ tıklama](media/how-to-accidental-deletes/delete-3.png)

**Görünüm sağlama günlüğünü** kullanarak, **stageddelete** girdilerini görebilir ve silinen kullanıcılara girilen bilgileri inceleyebilirsiniz.
 
 ![Sağlama günlükleri](media/how-to-accidental-deletes/delete-7.png)

### <a name="allowing-deletes"></a>Silmeleri izin verme

**Silmeleri Izin ver** eylemi, yanlışlıkla silme eşiğini tetikleyen nesneleri siler.  Silmeleri kabul etmek için aşağıdaki yordamı kullanın.  

1. Üç noktaya sağ tıklayıp **silmeleri Izin ver**' i seçin.
2. Silinmelere izin vermek için onay üzerinde **Evet** ' e tıklayın.
 
 ![Onaylama sırasında Evet](media/how-to-accidental-deletes/delete-4.png)

3. Silme işlemlerinin kabul edildiğini ve durumun sonraki döngüde sağlıklı olarak geri dönediğine ilişkin onay görürsünüz. 
 
 ![Silmeleri kabul et](media/how-to-accidental-deletes/delete-8.png)

### <a name="rejecting-deletions"></a>Silme işlemleri reddediliyor

Silme işlemleri için izin vermek istemiyorsanız, aşağıdakileri yapmanız gerekir:
- silme işlemlerinin kaynağını araştırın
- sorunu düzeltemedi (örneğin, OU yanlışlıkla kapsam dışına taşındı ve artık kapsama yeniden eklediniz)
- Aracı yapılandırmasında **yeniden başlatma eşitlemesini** Çalıştır

## <a name="next-steps"></a>Sonraki adımlar 

- [Azure AD Connect bulut eşitleme sorunlarını giderme](how-to-troubleshoot.md)
- [Azure AD Connect bulut eşitleme hata kodları](reference-error-codes.md)
 

