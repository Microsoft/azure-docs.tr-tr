---
title: Azure veri paylaşımında olağanüstü durum kurtarma
description: Azure veri paylaşımında olağanüstü durum kurtarma
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 07/30/2020
ms.openlocfilehash: 1d7c9935d7e0d6bb2d457aa4c08f9b2b3e5fa910
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92218706"
---
# <a name="disaster-recovery-for-azure-data-share"></a>Azure veri paylaşımında olağanüstü durum kurtarma

Bu makalede, Azure veri paylaşımında bir olağanüstü durum kurtarma ortamının nasıl yapılandırılacağı açıklanmaktadır. Azure veri merkezine kesintiler nadir bir durumdur, ancak en son birkaç dakika ile saat arasında bir süre olabilir. Veri merkezi kesintileri, veri sağlayıcısı tarafından paylaşılmakta olan verilere bağlı ortamlarda kesintiye neden olabilir. Bu makalede açıklanan adımları izleyerek, veri sağlayıcıları veri, veri paylaşımınızı barındıran birincil bölge için veri merkezi kesintisi durumunda veri tüketicileriyle veri paylaşmaya devam edebilir. 

## <a name="achieving-business-continuity-for-azure-data-share"></a>Azure veri paylaşımında iş sürekliliği elde etmek

Veri merkezi kesintisi için hazırlanmak üzere, veri sağlayıcısı ikincil bir bölgede sağlanan bir veri paylaşma ortamına sahip olabilir. Bir veri merkezi kesintisi oluşması durumunda sorunsuz yük devretme sağlamak için ölçüler alınabilir. 

Veri sağlayıcıları ek bir bölgede ikincil Azure veri paylaşma kaynakları sağlayabilir. Bu veri paylaşımı kaynakları, birincil Azure veri paylaşımı kaynağında bulunan paylaşımları ve veri kümelerini içerecek şekilde yapılandırılabilir. DR ortamını yapılandırırken veya daha sonra bir kez veri tüketicilerini ikincil paylaşımlara davet edebilirler (ör. el ile yük devretme adımlarının bir parçası olarak).

Veri tüketicilerinin DR amaçları için sağlanan ikincil bir ortamda etkin paylaşma abonelikleri varsa, bu, yük devretmenin bir parçası olarak anlık görüntü zamanlamayı etkinleştirebilirler. Veri tüketicileri, DR amaçları için ikincil bir bölgeye abone olmak istemiyor, ikincil paylaşıma daha sonra davet edilebilir. 

Veri tüketicileri, DR amaçları için boşta olan etkin bir Share aboneliğine sahip olabilir ya da veri sağlayıcıları el ile yük devretme yordamlarının bir parçası olarak bunları daha sonra davet edebilir. 

## <a name="related-information"></a>İlgili bilgiler

- [İş sürekliliği ve olağanüstü durum kurtarma](../best-practices-availability-paired-regions.md)
- [BCDR stratejinize yüksek düzeyde kullanılabilirlik ekleme](/azure/architecture/solution-ideas/articles/build-high-availability-into-your-bcdr-strategy)

## <a name="next-steps"></a>Sonraki adımlar

Verileri paylaşmaya nasıl başlayacağınızı öğrenmek için [verilerinizi paylaşma](share-your-data.md) öğreticiye geçin.