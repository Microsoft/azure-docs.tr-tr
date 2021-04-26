---
title: include dosyası
description: include dosyası
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/05/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: f4af8a150b062526f08c1d15581ec26e2fe12d8c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95992846"
---
- Artımlı anlık görüntüler şu anda abonelikler arasında taşınamaz.
- Şu anda, belirli bir anda belirli bir anlık görüntü ailesinin en fazla beş anlık görüntüsüne sahip SAS URI 'Leri oluşturabilirsiniz.
- Bu diskin aboneliği dışındaki belirli bir disk için artımlı bir anlık görüntü oluşturamazsınız.
- Her beş dakikada bir disk başına en fazla yedi Artımlı anlık görüntü oluşturulabilir.
- Tek bir disk için toplam 200 Artımlı anlık görüntü oluşturulabilir.
- 4 TB 'lik sınırın üzerinde üst diskin boyutunu değiştirdikten önce ve sonra alınan anlık görüntüler arasındaki değişiklikleri alamaz. Örneğin, bir diskin boyutu 2 TB olduğunda Artımlı anlık görüntü anlık görüntüsü (a) gerçekleştiyse. Artık diskin boyutunu 6 TB olarak artırdınız ve sonra başka bir artımlı anlık görüntü anlık görüntüsü (b) sürdü. Snapshot-a ve Snapshot-b arasındaki değişiklikleri alamaz. Yeniden boyutlandırma sonrasında oluşturulan anlık görüntünün tam kopyasını yeniden indirmeniz gerekir. Bundan sonra, anlık görüntü-b ' t a ve anlık görüntü-b ' t a sonra oluşturulan değişiklikleri alabilirsiniz. 
