---
title: Azure işleme başvuru mimarileri-Azure Batch
description: Azure Batch ve diğer Azure hizmetlerini kullanarak, buluta gömülmüş şirket içi bir işleme grubunu genişletmek için mimariler
services: batch
ms.service: batch
author: davefellows
manager: gwallace
ms.author: lahugh
ms.date: 02/07/2019
ms.topic: conceptual
ms.custom: seodec18
ms.openlocfilehash: fa2d59b2a6d2dea72276ab38a5cb1ca7bfb579a4
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68323120"
---
# <a name="reference-architectures-for-azure-rendering"></a>Azure işleme için başvuru mimarileri

Bu makalede, Azure 'da şirket içi bir işleme grubu olarak genişleyen senaryolar veya "veri bloğu" için üst düzey mimari diyagramları gösterilmektedir. Örneklerde Azure işlem, ağ ve depolama hizmetleri için farklı seçenekler gösterilmektedir.

## <a name="hybrid-with-nfs-or-cfs"></a>NFS veya CFS ile karma

Aşağıdaki diyagramda aşağıdaki Azure hizmetlerini içeren bir karma senaryo gösterilmektedir:

* **İşlem** -Azure Batch havuzu veya sanal makine ölçek kümesi.

* Şirket içi **ağ** : Azure ExpressRoute veya VPN. Azure: Azure sanal ağı.

* **Depolama** -giriş ve çıkış dosyaları: Azure VM 'lerini kullanarak NFS veya CFS, Azure Dosya Eşitleme veya RSync aracılığıyla şirket içi depolamayla eşitlenir. Alternatif olarak: NFS kullanarak şirket içi NAS cihazlarındaki dosyaları girmek veya çıkış yapmak için avere vFXT.

  ![Bulut patlaması-NFS veya CFS ile karma](./media/batch-rendering-architectures/hybrid-nfs-cfs-avere.png)

## <a name="hybrid-with-blobfuse"></a>Blobsigortası ile karma

Aşağıdaki diyagramda aşağıdaki Azure hizmetlerini içeren bir karma senaryo gösterilmektedir:

* **İşlem** -Azure Batch havuzu veya sanal makine ölçek kümesi.

* Şirket içi **ağ** : Azure ExpressRoute veya VPN. Azure: Azure sanal ağı.

* **Depolama** -giriş ve çıkış dosyaları: Azure Blobsigortası aracılığıyla işlem kaynaklarına bağlanmış BLOB depolama alanı.

  ![Blobsigortası ile bulut patlaması-karma](./media/batch-rendering-architectures/hybrid-blob-fuse.png)

## <a name="hybrid-compute-and-storage"></a>Karma işlem ve depolama

Aşağıdaki diyagramda hem işlem hem de depolama için tam bağlantılı bir karma senaryo gösterilmektedir ve aşağıdaki Azure hizmetleri bulunur:

* **İşlem** -Azure Batch havuzu veya sanal makine ölçek kümesi.

* Şirket içi **ağ** : Azure ExpressRoute veya VPN. Azure: Azure sanal ağı.

* **Depolama** -şirket içi: Avere vFXT. Azure Data Box BLOB depolama alanına veya şirket içi avere FXT 'ye yönelik şirket içi dosyaların, NAS hızlandırma için isteğe bağlı olarak arşivlenmesi.

  ![Bulut patlaması-karma işlem ve depolama](./media/batch-rendering-architectures/hybrid-compute-storage-avere.png)


## <a name="next-steps"></a>Sonraki adımlar

* Azure Batch ile [işleme yöneticilerini](batch-rendering-render-managers.md) kullanma hakkında daha fazla bilgi edinin.

* [Azure 'Da işleme](batch-rendering-service.md)seçenekleri hakkında daha fazla bilgi edinin.