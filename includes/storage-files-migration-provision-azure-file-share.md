---
title: include dosyası
description: include dosyası
services: storage
author: fauhse
ms.service: storage
ms.topic: include
ms.date: 2/20/2020
ms.author: fauhse
ms.custom: include file
ms.openlocfilehash: c003e765574d764b6653823e8554d0718ea85e0e
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106081124"
---
Azure dosya paylaşma, Bulutta Azure depolama hesabında depolanır.
Burada başka bir performans konuları vardır.

Yüksek düzeyde etkin paylaşımlarınız (çok sayıda kullanıcı ve/veya uygulama tarafından kullanılan paylaşımlar) varsa, iki Azure dosya paylaşımı bir depolama hesabının performans sınırına ulaşabilirler.

En iyi yöntem, depolama hesaplarını her bir dosya paylaşımıyla dağıtmaktır.
Birden çok Azure dosya paylaşımını aynı depolama hesabında havuza alabilirsiniz, bu durumda arşiv paylaşımlarınız olabilir veya bunlar içinde düşük günlük etkinlikleri bekleyebilir.

Bu konular, bulut erişimine (bir Azure VM aracılığıyla) Azure Dosya Eşitleme kıyasla daha fazlasını uygular. Yalnızca bu paylaşımlar üzerinde Azure Dosya Eşitleme kullanmayı planlıyorsanız, birkaçını tek bir Azure depolama hesabına göre gruplamak iyidir.

Paylaşımlarınızın bir listesini yaptıysanız, her bir paylaşımı bulunacağı depolama hesabıyla eşlemeniz gerekir.

Önceki aşamada, uygun paylaşım sayısını belirlediniz. Bu adımda, depolama hesaplarının dosya paylaşımlarına eşlenme oluşturmuş olursunuz. Artık Azure depolama hesaplarını uygun sayıda Azure dosya paylaşımına dağıtın.

Her depolama hesabınızın bölgesinin aynı olduğundan ve zaten dağıttığınız depolama eşitleme hizmeti kaynağının bölgesiyle eşleştiğinden emin olun.

> [!CAUTION]
> 100-TiB sınırına sahip bir Azure dosya paylaşımının oluşturulması durumunda bu paylaşımda yalnızca yerel olarak yedekli depolama veya bölgesel olarak yedekli depolama yedekliliği seçenekleri kullanılabilir. 100-TiB dosya paylaşımlarını kullanmadan önce depolama yedeklerinizin ihtiyaçlarını göz önünde bulundurun.

Azure dosya paylaşımları yine de varsayılan olarak 5-TiB sınırı ile oluşturulmuştur. Yeni depolama hesapları oluşturmakta olduğunuz için, [100-TiB limitleriyle Azure dosya paylaşımlarına izin veren depolama hesapları oluşturma kılavuzunu](../articles/storage/files/storage-files-how-to-create-large-file-share.md)izlediğinizden emin olun.

Bir depolama hesabı dağıttığınızda, Azure Storage 'ın yedekliliğe göre başka bir dikkat edin. Bkz. [Azure Storage artıklık seçenekleri](../articles/storage/common/storage-redundancy.md).

Kaynaklarınızın adları da önemlidir. Örneğin, bir Azure depolama hesabına HR departmanı için birden çok paylaşım gruplandırdıysanız depolama hesabını uygun şekilde adlandırın. Benzer şekilde, Azure dosya paylaşımlarınızı adlandırırken, şirket içi karşılıkları için kullanılan adlara benzer adlar kullanmanız gerekir.