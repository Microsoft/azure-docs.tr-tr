---
title: 'ML Studio (klasik): çalışma alanlarını yönetme-Azure'
description: Azure Machine Learning Studio (klasik) çalışma alanlarına erişimi yönetin ve Machine Learning API Web hizmetlerini dağıtın ve yönetin
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 02/27/2017
ms.openlocfilehash: c41c5350e29555a11bb74eeca9af62a6b87cfced
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "101094272"
---
# <a name="manage-an-azure-machine-learning-studio-classic-workspace"></a>Azure Machine Learning Studio (klasik) çalışma alanını yönetme

**Uygulama hedefi:** ![ İçin geçerlidir. ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (klasik) ![ için geçerlidir.](../../../includes/media/aml-applies-to-skus/no.png)[ Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  


> [!NOTE]
> Web hizmetlerini Machine Learning Web Hizmetleri portalında yönetme hakkında bilgi için, bkz. [Azure Machine Learning Web Hizmetleri portalını kullanarak bir Web hizmetini yönetme](manage-new-webservice.md).
> 
> 

Azure portal Machine Learning Studio (klasik) çalışma alanlarını yönetebilirsiniz.



## <a name="use-the-azure-portal"></a>Azure portalını kullanma

Azure portal bir Studio (klasik) çalışma alanını yönetmek için:

1. Azure abonelik Yöneticisi hesabı kullanarak [Azure Portal](https://portal.azure.com/) oturum açın.
2. Sayfanın üst kısmındaki arama kutusuna "Machine Learning Studio (klasik) çalışma alanları" yazın ve ardından **Machine Learning Studio (klasik) çalışma alanları**' nı seçin.
3. Yönetmek istediğiniz çalışma alanına tıklayın.

Standart kaynak yönetimi bilgilerine ve kullanılabilir seçeneklere ek olarak şunları yapabilirsiniz:

- **Özellikleri** görüntüle-Bu sayfada çalışma alanı ve kaynak bilgileri görüntülenir ve bu çalışma alanının bağlı olduğu aboneliği ve kaynak grubunu değiştirebilirsiniz.
- **Depolama anahtarlarını yeniden eşitleme** -çalışma alanı anahtarları depolama hesabına tutar. Depolama hesabı anahtarları değiştirirse, anahtarları çalışma alanıyla senkronize etmek için yeniden Eşitle **tuşlarına** tıklayabilirsiniz.

Bu Studio (klasik) çalışma alanıyla ilişkili Web hizmetlerini yönetmek için Machine Learning Web Hizmetleri portalını kullanın. Tüm bilgiler için [Azure Machine Learning Web Hizmetleri portalını kullanarak bir Web hizmetini yönetme](manage-new-webservice.md) makalesine bakın.

> [!NOTE]
> Yeni Web hizmetlerini dağıtmak veya yönetmek için, Web hizmetinin dağıtıldığı abonelikte bir katkıda bulunan veya yönetici rolü atanması gerekir. Bir Machine Learning Studio (klasik) çalışma alanına başka bir kullanıcı davet ederseniz, Web hizmetlerini dağıtabilmeleri veya yönetebilmeleri için bunları abonelikte bir katkıda bulunan veya yönetici rolüne atamanız gerekir. 
> 
>Erişim izinlerini ayarlama hakkında daha fazla bilgi için bkz. [Azure Portal kullanarak Azure rolleri atama](../../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Sonraki adımlar
* [Azure Resource Manager şablonlarıyla Machine Learning dağıtma](deploy-with-resource-manager-template.md)hakkında daha fazla bilgi edinin.