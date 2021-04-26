---
title: Verileri Azure Blob depolama alanı 'na veya sunucudan taşıma-Team Data Science Işlemi
description: Azure Depolama Gezgini, AzCopy, Python ve SSIS kullanarak Azure Blob Storage 'a ve bu depolama alanından veri taşıma.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 5148084fa22266b1352046c7d8737b9804c5f4d0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "93311854"
---
# <a name="move-data-to-and-from-azure-blob-storage"></a>Azure Blob depolamadan veri taşıma

Ekip veri bilimi süreci, verilerin her bir aşamasında işlenmek veya analiz edilmesi için verilerin farklı depolama ortamlarına toplanmasını veya bu ortamlara yüklenmesini gerektirir.

## <a name="different-technologies-for-moving-data"></a>Veri taşımaya yönelik farklı teknolojiler

Aşağıdaki makalelerde, farklı teknolojiler kullanılarak verilerin Azure Blob depolama alanına ve içine nasıl taşınacağı açıklanır.

* [Azure depolama-gezgin](move-data-to-azure-blob-using-azure-storage-explorer.md)
* [AzCopy](../../storage/common/storage-use-azcopy-v10.md)
* [Python](../../storage/blobs/storage-quickstart-blobs-python.md)
* [SSIS](move-data-to-azure-blob-using-ssis.md)

Sizin için en uygun yöntem, senaryonuza bağlıdır. Azure Machine Learning makalesinde gelişmiş analizler [Için senaryolar](plan-sample-scenarios.md) , gelişmiş analiz işleminde kullanılan çeşitli veri bilimi iş akışları için ihtiyaç duyduğunuz kaynakları belirlemenize yardımcı olur.

> [!NOTE]
> Azure Blob depolamaya yönelik kapsamlı bir giriş için [Azure Blob temelleri](../../storage/blobs/storage-quickstart-blobs-dotnet.md) ve [Azure Blob hizmeti](/rest/api/storageservices/Blob-Service-Concepts)' ne bakın.
> 
> 

## <a name="using-azure-data-factory"></a>Azure Data Factory'yi kullanma

Alternatif olarak, [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) için kullanabilirsiniz: 

* Azure Blob depolamadan veri yükleyen bir işlem hattı oluşturma ve zamanlama 
* Bunu yayımlanmış bir Azure Machine Learning Web hizmetine geçirin, 
* tahmine dayalı analiz sonuçlarını alın ve 
* sonuçları depolamaya yükleyin. 

Daha fazla bilgi için bkz. [Azure Data Factory ve Azure Machine Learning kullanarak tahmine dayalı işlem hatları oluşturma](../../data-factory/transform-data-using-machine-learning.md).

## <a name="prerequisites"></a>Önkoşullar
Bu makalede, bu hesap için bir Azure aboneliğiniz, bir depolama hesabınız ve karşılık gelen depolama anahtarınız olduğunu varsaymaktadır. Verileri karşıya yüklemeden/indirmeden önce Azure depolama hesabı adınızı ve hesap anahtarınızı bilmeniz gerekir.

* Bir Azure aboneliği ayarlamak için bkz. [ücretsiz bir aylık deneme sürümü](https://azure.microsoft.com/pricing/free-trial/).
* Depolama hesabı oluşturma ve hesap ve anahtar bilgilerini alma hakkında yönergeler için bkz. [Azure Storage hesapları hakkında](../../storage/common/storage-account-create.md).