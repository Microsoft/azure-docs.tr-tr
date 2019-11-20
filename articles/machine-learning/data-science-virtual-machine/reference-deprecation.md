---
title: 'Başvuru: DSVM görüntüsü kullanımdan kaldırıldı'
description: Azure Veri Bilimi Sanal Makinesi (DSVM) etkileyen kullanım dışı bırakma ayrıntıları
author: gvashishtha
ms.service: machine-learning
ms.subservice: data-science-vm
ms.author: gopalv
ms.date: 10/14/2019
ms.topic: reference
ms.openlocfilehash: 4968ac08b75141ed84994ca287215a34728232c4
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72333345"
---
# <a name="reference-deprecation-of-dsvm-images"></a>Başvuru: DSVM görüntülerinin kullanımdan kaldırılması

Aşağıda, Azure Veri Bilimi Sanal Makinesi yaklaşan kullanım dışı bırakımlar ile ilgili öneriler ele alınmaktadır.

## <a name="windows-2012-migrating-data-disks"></a>Windows 2012: veri disklerini geçirme

5 Kasım 2019 ' de Windows 2012 DSVM görüntüsünü desteklemeyi durduracağız. Mevcut Windows 2012 DSVM 'nizden bir veri diskini Windows 2016 DSVM 'ye geçirmek için aşağıdaki adımları uygulayın:

1. [Burada](./provision-vm.md#create-your-dsvm)gösterilen yönergeleri izleyerek yeni bir Windows 2016 dsvm oluşturun.
1. [Bu yönergeleri](../../virtual-machines/windows/detach-disk.md)kullanarak, Windows 2012 görüntüsünden mevcut veri disklerini ayırın.
1. [Bu yönergeleri](../../virtual-machines/windows/attach-disk-ps.md#attach-an-existing-data-disk-to-a-vm)kullanarak diski önceki adımdan Windows 2016 yansımanıza ekleyin.
