---
title: include dosyası
description: include dosyası
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/26/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 6c54bdc2738f9455926882c439ff8942c1c0a5a6
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107759777"
---
Azure dosya paylaşımları, paylaşılan bir depolama havuzunu temsil eden üst düzey nesneler olan *depolama hesaplarına* dağıtılır. Bu depolama havuzu, birden çok dosya paylaşımının yanı sıra blob kapsayıcıları, kuyrukları veya tabloları gibi diğer depolama kaynaklarını dağıtmak için kullanılabilir. Bir depolama hesabına dağıtılan tüm depolama kaynakları, bu depolama hesabı için uygulanan limitleri paylaşır. Geçerli depolama hesabı sınırları için bkz. [Azure dosyaları ölçeklenebilirlik ve performans hedefleri](../articles/storage/files/storage-files-scale-targets.md).

Azure dosya dağıtımları için kullanacağınız iki ana depolama hesabı türü vardır: 
- **Genel amaçlı sürüm 2 (GPv2) depolama hesapları**: GPv2 depolama hesapları, Azure dosya paylaşımlarını standart/sabit disk tabanlı (HDD tabanlı) donanımda dağıtmanıza olanak tanır. GPv2 depolama hesapları, Azure dosya paylaşımlarını depolamanın yanı sıra blob kapsayıcıları, kuyrukları veya tabloları gibi diğer depolama kaynaklarını da depolayabilirler. 
- **FileStorage depolama hesapları**: FileStorage depolama hesapları, Azure dosya paylaşımlarını Premium/katı hal disk tabanlı (SSD tabanlı) donanımda dağıtmanıza olanak tanır. FileStorage hesapları yalnızca Azure dosya paylaşımlarını depolamak için kullanılabilir; başka depolama kaynakları (blob kapsayıcıları, kuyruklar, tablolar vb.) bir FileStorage hesabında dağıtılabilir. Yalnızca FileStorage hesapları, SMB ve NFS dosya paylaşımlarını dağıtabilir.

Azure portal, PowerShell veya CLı içinde karşılaşabileceğiniz birkaç farklı depolama hesabı türü vardır. İki depolama hesabı türü, BlockBlobStorage ve BlobStorage depolama hesapları Azure dosya paylaşımları içeremez. Görebileceğiniz diğer iki depolama hesabı türü, her ikisi de Azure dosya paylaşımları içerebilen genel amaçlı sürüm 1 (GPv1) ve klasik depolama hesaplarıdır. GPv1 ve klasik depolama hesaplarında Azure dosya paylaşımları bulunabilir, ancak Azure dosyalarının birçok yeni özelliği yalnızca GPv2 ve FileStorage depolama hesaplarında kullanılabilir. Bu nedenle, yalnızca Yeni dağıtımlar için GPv2 ve FileStorage depolama hesapları kullanmayı ve ortamınızda zaten varsa GPv1 ve klasik depolama hesaplarını yükseltmeyi öneririz.  