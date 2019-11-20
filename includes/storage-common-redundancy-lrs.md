---
title: include dosyası
description: include dosyası
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 09/23/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 8908ded31b96aac50db1fc25e92c2c0a8e960453
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/24/2019
ms.locfileid: "71219052"
---
Yerel olarak yedekli depolama (LRS), verilerinizi tek bir veri merkezi içinde üç kez çoğaltır. LRS, belirli bir yıl boyunca nesnelerin en az% 99,999999999 (11 nines) dayanıklılığı sağlar. LRS, en düşük maliyetli çoğaltma seçeneğidir ve diğer seçeneklere kıyasla en az dayanıklılık sağlar.

Veri merkezi düzeyinde bir olağanüstü durum oluşursa (örneğin, yangın veya taşması), LRS kullanan bir depolama hesabındaki tüm çoğaltmalar kaybolabilir veya kurtarılamaz olabilir. Bu riski azaltmak için Microsoft, bölgesel olarak yedekli depolama (ZRS), coğrafi olarak yedekli depolama (GRS) veya coğrafi bölge-yedekli depolama (GZRS) kullanmanızı önerir.

LRS depolama hesabına yönelik yazma isteği, yalnızca veriler üç kopyaya yazıldıktan sonra başarıyla döndürülür.

Aşağıdaki senaryolarda LRS 'yi kullanmak isteyebilirsiniz:

* Uygulamanız veri kaybı oluşursa kolayca yeniden oluşturabileceğiniz verileri depoluyorsa LRS 'yi kabul edebilirsiniz.
* Bazı uygulamalar veri idare gereksinimleri nedeniyle yalnızca bir ülke/bölge dahilinde verileri çoğaltmaya kısıtlıdır. Bazı durumlarda, GRS hesapları için verilerin çoğaltılacağı eşleştirilmiş bölgeler başka bir ülkede/bölgede olabilir. Eşleştirilmiş bölgeler hakkında daha fazla bilgi için bkz. [Azure bölgeleri](https://azure.microsoft.com/regions/).
