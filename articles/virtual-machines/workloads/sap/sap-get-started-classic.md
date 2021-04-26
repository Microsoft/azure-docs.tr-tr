---
title: Linux sanal makinelerinde SAP kullanma | Microsoft Docs
description: Microsoft Azure’daki Linux sanal makinelerinde (VM) SAP çözümlerini kullanma hakkında bilgi edinin
services: virtual-machines-linux,virtual-network,storage
documentationcenter: saponazure
author: MSSedusch
manager: gwallace
editor: ''
tags: azure-service-management
keywords: ''
ms.assetid: f9cd93dc-71ad-48a4-8778-4e48aec484a6
ms.service: virtual-machines-sap
ms.topic: conceptual
ms.tgt_pltfrm: vm-linux
ms.workload: na
ms.date: 10/04/2016
ms.author: sedusch
ms.openlocfilehash: bf0ae8bdd800f5fa43df73eca312504f56fc009a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101675393"
---
# <a name="using-sap-on-linux-virtual-machines-in-azure"></a>Azure 'da Linux sanal makinelerinde SAP kullanma
Bulut Bilgi İşlem, BT sektöründeki küçük ölçekli şirketlerden büyük ve çok uluslu kuruluşlara kadar çok geniş ölçekte kullanılan ve her geçen gün daha önemli hale gelen bir terimdir. Microsoft Azure, Microsoft tarafından sunulan ve birçok yeni özelliğe sahip olan Bulut Hizmetleri Platformudur. Bu özellikler sayesinde müşteriler artık teknik veya mali kısıtlamalara takılmadan uygulamalarını Bulut Hizmetleri olarak hazırlayabilir ve kullanımdan kaldırabilir. Şirketler, zamanlarını ve bütçelerini donanım altyapısına harcamak yerine uygulamanın kendisine, iş süreçlerine ve hem müşteriler hem de kullanıcılar için fayda sağlamaya odaklanabilir.

Microsoft Azure sanal makinelerle, Microsoft kapsamlı bir hizmet olarak altyapı (IaaS) platformu sunar. SAP NetWeaver tabanlı uygulamalar, Azure Sanal Makinelerinde (IaaS) desteklenmektedir. Aşağıdaki teknik incelemeler, Azure 'daki Windows sanal makinelerinde SAP NetWeaver tabanlı uygulamaların nasıl planlanacağı ve uygulanacağı açıklanır. Ayrıca, [Windows sanal MAKINELERINDE](./get-started.md?toc=/azure/virtual-machines/windows/classic/toc.json)SAP NetWeaver tabanlı uygulamalar da uygulayabilirsiniz.

[!INCLUDE [virtual-machines-common-classic-sap-get-started](../../../../includes/virtual-machines-common-classic-sap-get-started.md)]

## <a name="sap-netweaver-on-azure-suse-linux-virtual-machines"></a>Azure SUSE Linux Sanal Makineleri SAP NetWeaver
Başlık: Microsoft Azure SUSE Linux VM 'lerinde SAP NetWeaver test etme

Özet: Bu noktada, Azure Linux VM 'lerinde SAP NetWeaver çalıştırmak için resmi bir SAP desteği yoktur. Yine de müşteriler bazı testler yapmak isteyebilir veya SAP desteği 'ne iletişim kurma gereksinimi olmadığı sürece Azure Linux VM 'lerinde SAP tanıtımı veya eğitim sistemlerini çalıştırmayı düşünebilirler. Bu makale, SAP çalıştırmak için Azure SUSE Linux VM 'lerinin ayarlanmasına ve yaygın olarak karşılaşılan olası bir durumu önlemek için bazı temel ipuçları sağlamanıza yardımcı olmalıdır.

Güncelleştirilme tarihi: Aralık 2015

[Bu makaleye buradan ulaşabilirsiniz](./sap-deployment-checklist.md?toc=/azure/virtual-machines/linux/toc.json)
