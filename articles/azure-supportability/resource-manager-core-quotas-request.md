---
title: Azure Resource Manager vCPU kota artışı istekleri | Microsoft Docs
description: Azure Resource Manager vCPU kota artışı istekleri
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/07/2019
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: c3248b10b4ad343e8776056d42ec153130f0061f
ms.sourcegitcommit: 961468fa0cfe650dc1bec87e032e648486f67651
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72248646"
---
# <a name="quota-increase-requests"></a>Kota artışı istekleri

Sanal makineler ve sanal makine ölçek kümeleri için Kaynak Yöneticisi vCPU kotaları, her bir bölgede her bir abonelik için iki katmanda zorlanır. 

İlk katman toplam bölgesel vCPU sayısı sınırı (tüm VM serileri genelinde) ve ikinci katman VM Serisi vCPU sayısı sınırı (D serisi vCPU 'Lar gibi). Yeni bir VM 'nin dağıtılması her seferinde, bu VM Serisi için yeni ve var olan vCPU 'ların toplamı, söz konusu VM Serisi için onaylanan vCPU kotasını aşmamalıdır. Ayrıca, tüm VM dizileri genelinde dağıtılan toplam yeni ve var olan vCPU sayısı, abonelik için onaylanan toplam bölgesel vCPU kotasını aşmamalıdır. Bu kotalardan biri aşılırsa, VM dağıtımına izin verilmez.
Azure portal VM Serisi için vCPU kota sınırı artışı isteyebilirsiniz. VM Serisi kotasının bir artışı toplam bölgesel vCPU sayısını aynı tutara göre otomatik olarak arttırır. 

Yeni bir abonelik oluşturulduğunda, varsayılan toplam bölgesel sanal CPU 'Lar, tüm tek VM serileri için varsayılan vCPU kotaları toplamına eşit olmayabilir. Bu, dağıtmak istediğiniz her bir VM Serisi için yeterli kotaya sahip bir aboneliğe neden olabilir, ancak tüm dağıtımlar için toplam bölgesel vCPU 'Lara yetecek kadar kota elde etmez. Bu durumda, toplam bölgesel vCPU sınırını açıkça artırmak için bir istek göndermeniz gerekir. Toplam bölgesel vCPU sayısı, bölgenin tüm VM serileri genelinde onaylanan kotanın toplamını aşamaz.

[Sanal makine vCPU kotaları sayfasında](https://docs.microsoft.com/azure/virtual-machines/windows/quotas) ve [Azure aboneliği ve hizmet limitleri](https://aka.ms/quotalimits) sayfasında kotalar hakkında daha fazla bilgi edinin. 

