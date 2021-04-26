---
title: Azure Data Lake için U-SQL UıDO Programlama Kılavuzu
description: İyi USQL betiği oluşturmanızı sağlamak için U-SQL UDO programlamasına Azure Data Lake Analytics hakkında bilgi edinin.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: 02360c68e5e830ceee69075fd5532b126d85bec2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96512657"
---
# <a name="u-sql-user-defined-objects-overview"></a>U-SQL Kullanıcı tanımlı nesnelere genel bakış


## <a name="u-sql-user-defined-objects-udo"></a>U-SQL: Kullanıcı tanımlı nesneler: UDO
U-SQL, Kullanıcı tanımlı nesneler veya UDO olarak adlandırılan özel programlama nesneleri tanımlamanıza olanak sağlar.

U-SQL içindeki UDO 'in bir listesi aşağıda verilmiştir:

* Kullanıcı tanımlı ayıklayıcıları
    * Satırı satıra göre Ayıkla
    * Özel yapılandırılmış dosyalardan veri ayıklama uygulamak için kullanılır

* Kullanıcı tanımlı çıktıcılar
    * Satıra göre çıkış satırı
    * Özel veri türlerini veya özel dosya biçimlerini çıkarmak için kullanılır

* Kullanıcı tanımlı işlemciler
    * Tek satır alın ve bir satır oluşturun
    * Sütun sayısını azaltmak veya var olan bir sütun kümesinden türetilen değerlerle yeni sütunlar üretmek için kullanılır

* Kullanıcı tanımlı appliers
    * Tek satır alın ve 0-n satır oluşturun
    * Dış/çapraz uygulama ile kullanılır

* Kullanıcı tanımlı combiners
    * Satır kümelerini birleştirir--Kullanıcı tanımlı birleşimler

* Kullanıcı tanımlı azaltıcının
    * N satır al ve bir satır üret
    * Satır sayısını azaltmak için kullanılır

UıDO genellikle aşağıdaki U-SQL deyimlerinin bir parçası olarak U-SQL komut dosyasında açıkça çağırılır:

* AYIKLA
* ÇıKTıLARıN
* IŞLE
* BILE
* AZAL

> [!NOTE]  
> UDO 'lar 0,5 GB bellek kullanmak üzere sınırlandırılmıştır.  Bu bellek sınırlaması yerel yürütmeler için uygulanmaz.

## <a name="next-steps"></a>Sonraki adımlar
* [U-SQL programlama kılavuzu-genel bakış](data-lake-analytics-u-sql-programmability-guide.md)
* [U-SQL programlama kılavuzu-UDT ve UDADGG](data-lake-analytics-u-sql-programmability-guide-UDT-AGG.md)