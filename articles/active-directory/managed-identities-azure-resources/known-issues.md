---
title: Yönetilen kimliklerle ilgili bilinen sorunlar-Azure Active Directory
description: Azure kaynakları için yönetilen kimliklerle ilgili bilinen sorunlar.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.assetid: 2097381a-a7ec-4e3b-b4ff-5d2fb17403b6
ms.service: active-directory
ms.subservice: msi
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 04/08/2021
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: 8d2b6323a15595e57e7e89c6a83f9f718422e1d7
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107226931"
---
# <a name="known-issues-with-managed-identities"></a>Yönetilen kimliklerle ilgili bilinen sorunlar

Bu makalede, Yönetilen kimlikler ve bunların nasıl ele alınacağını gösteren birkaç sorun ele alınmaktadır. Yönetilen kimlikler hakkında sık sorulan sorular, [sık sorulan sorular](managed-identities-faq.md) makalemiz bölümünde belgelenmiştir.
## <a name="vm-fails-to-start-after-being-moved"></a>VM taşındıktan sonra başlatılamaz 

Bir VM 'yi bir kaynak grubundan veya aboneliğinden çalışan bir durumda taşırsanız, taşıma sırasında çalışmaya devam eder. Ancak, taşıma işleminden sonra VM durdurulur ve yeniden başlatılırsa başlatılamaz. Bu sorun, VM 'nin Azure kaynakları kimliği için yönetilen kimliklere yönelik başvuruyu güncelleştirmediği ve eski kaynak grubunda ona işaret etmeye devam ettiğinden meydana gelir.

**Geçici çözüm** 
 
Azure kaynakları için yönetilen kimliklerin doğru değerlerini kullanabilmesi için VM 'de bir güncelleştirme tetikleyin. Azure kaynak kimliği için yönetilen kimliklerin başvurusunu güncelleştirmek üzere bir VM özelliği değişikliği yapabilirsiniz. Örneğin, aşağıdaki komutla sanal makinede yeni bir etiket değeri ayarlayabilirsiniz:

```azurecli-interactive
az vm update -n <VM Name> -g <Resource Group> --set tags.fixVM=1
```
 
Bu komut, VM 'de 1 değeri ile yeni bir "fixVM" etiketi ayarlar. 
 
Bu özelliği ayarlayarak, VM Azure kaynakları Kaynak URI 'SI için doğru yönetilen kimliklerle güncelleştirilir ve sonra VM 'yi başlatabilmelisiniz. 
 
VM başlatıldıktan sonra, etiket aşağıdaki komut kullanılarak kaldırılabilir:

```azurecli-interactive
az vm update -n <VM Name> -g <Resource Group> --remove tags.fixVM
```

## <a name="transferring-a-subscription-between-azure-ad-directories"></a>Azure AD dizinleri arasında abonelik aktarma

Yönetilen kimlikler bir abonelik taşındığında/başka bir dizine aktarıldığında güncellenmez. Sonuç olarak, var olan sistem tarafından atanan veya Kullanıcı tarafından atanan Yönetilen kimlikler bozulur. 

Başka bir dizine taşınmış bir abonelikte Yönetilen kimlikler için geçici çözüm:

 - Sistem tarafından atanan Yönetilen kimlikler için: devre dışı bırakın ve yeniden etkinleştirin. 
 - Kullanıcı tarafından atanan Yönetilen kimlikler için: silin, yeniden oluşturun ve bunları gereken kaynaklara yeniden ekleyin (örneğin, sanal makineler)

Daha fazla bilgi için bkz. [Azure aboneliğini farklı bir Azure AD dizinine aktarma](../../role-based-access-control/transfer-subscription.md).


## <a name="next-steps"></a>Sonraki adımlar

[Yönetilen kimlikleri destekleyen Hizmetleri](services-support-managed-identities.md) ve [sık sorulan sorular](managed-identities-faq.md) listesini içeren makalemizi inceleyebilirsiniz
