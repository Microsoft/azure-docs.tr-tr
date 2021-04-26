---
title: Programlı olarak Azure abonelikleri oluşturma
description: Bu makale, program aracılığıyla Azure abonelikleri oluşturmak için sağlanan seçenekleri anlamanıza yardımcı olur.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 03/11/2021
ms.reviewer: andalmia
ms.author: banders
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: ce08ebf473b11eecae327c7de050c791f5bc1b1a
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107379018"
---
# <a name="create-azure-subscriptions-programmatically"></a>Programlı olarak Azure abonelikleri oluşturma

Bu makale, program aracılığıyla Azure abonelikleri oluşturmak için sağlanan seçenekleri anlamanıza yardımcı olur.

Çeşitli REST API'leri kullanarak aşağıdaki Azure sözleşme türleri için abonelik oluşturabilirsiniz:

- Kurumsal Anlaşma (EA)
- Microsoft Müşteri Sözleşmesi (MCA)
- Microsoft İş Ortağı Sözleşmesi (MPA)

REST API 'Leri ile diğer anlaşma türleri için program aracılığıyla ek abonelikler oluşturamazsınız.

Farklı sözleşmeler ve API sürümleri için abonelik oluşturma gereksinimleri ve ayrıntıları birbirinden farklıdır. Aşağıda sizin durumunuza uyan makalelere bakın:

En son API'ler:

- [EA abonelikleri oluşturma](programmatically-create-subscription-enterprise-agreement.md)
- [MCA abonelikleri oluşturma](programmatically-create-subscription-microsoft-customer-agreement.md)
- [MPA abonelikleri oluşturma](programmatically-create-subscription-microsoft-partner-agreement.md)

Bu makalelerde Ayrıca bir Azure Resource Manager şablonuyla (ARM şablonu) aboneliklerin nasıl oluşturulacağı gösterilmektedir. ARM şablonu, abonelik oluşturma işleminin otomatikleştirilmesine yardımcı olur.

Hala [Önizleme API 'lerini](programmatically-create-subscription-preview.md)kullanıyorsanız, bunlarla abonelik oluşturmaya devam edebilirsiniz. 

## <a name="next-steps"></a>Sonraki adımlar

* Aboneliği oluşturduktan sonra, diğer kullanıcılara ve hizmet sorumlularına bu olanağı verebilirsiniz. Daha fazla bilgi için bkz. [Azure Kurumsal abonelikleri oluşturma erişimi verme (önizlemi)](grant-access-to-create-subscription.md).
* Yönetim gruplarını kullanarak çok fazla sayıda aboneliği yönetme hakkında daha fazla bilgi için bkz. [Kaynaklarınızı Azure yönetim gruplarıyla düzenleme](../../governance/management-groups/overview.md).
