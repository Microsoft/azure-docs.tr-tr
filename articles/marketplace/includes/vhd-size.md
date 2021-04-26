---
title: include dosyası
description: " dosyası"
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: include
author: emuench
ms.author: mingshen
ms.date: 10/09/2020
ms.openlocfilehash: f4e34e850391696506beed9f6f386f85528dff24
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92284813"
---
Bir işletim sistemi (ve isteğe bağlı ek hizmetler) ile önceden yapılandırılmış VM 'lerden birini seçtiyseniz, zaten standart bir Azure VM boyutu seçmiş olursunuz. Çözümünüzü önceden yapılandırılmış bir işletim sistemi ile başlatmak önerilen yaklaşımdır. Ancak, bir işletim sistemini el ile yüklüyorsanız, birincil VHD 'nizi VM yansımanıza göre boyutlandırmanız gerekir. İşletim sistemi disk boyutunun Linux veya Windows sınırları içinde olduğundan emin olun.

| İşletim Sistemi | VHD boyutu |
| --- | --- |
| Linux | 30 ila 1023 GB |
| Windows | 30 ila 250 GB |
|

Onaylanmış bir temel olarak verilen temel pencereler veya SQL Server görüntüleri zaten bu gereksinimleri karşıladığından, VHD 'nin biçimini veya boyutunu değiştirmeyin.

Veri diskleri 1 TB kadar büyük olabilir. Boyut ' a karar verirken, müşterilerin dağıtım sırasında bir görüntü içindeki VHD 'leri yeniden boyutlandıramayacağını unutmayın. Sabit biçimli VHD 'ler olarak veri diski VHD 'leri oluşturun. Ayrıca, genişletilebilir (seyrek/dinamik) olmalıdır. Veri diskleri başlangıçta boş olabilir veya veri içerebilir.