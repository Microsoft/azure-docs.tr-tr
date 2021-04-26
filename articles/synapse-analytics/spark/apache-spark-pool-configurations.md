---
title: Apache Spark havuzu kavramları
description: Azure SYNAPSE Analytics 'teki Apache Spark havuz boyutlarına ve yapılandırmalara giriş.
services: synapse-analytics
author: mlee3gsd
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 12/2/2020
ms.author: martinle
ms.reviewer: euang
ms.openlocfilehash: 6422c33f17879aa8ec4844cc6de63411528a388b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104606166"
---
# <a name="apache-spark-pool-configurations-in-azure-synapse-analytics"></a>Azure SYNAPSE Analytics 'te Apache Spark havuzu yapılandırması

Spark havuzu, bir Spark örneği örneği oluşturulurken işlem kaynağı gereksinimlerini ve ilişkili davranış özelliklerini tanımlayan meta veri kümesidir. Bu özellikler, ad, düğüm sayısı, düğüm boyutu, ölçekleme davranışı ve yaşam süresi dahil değildir ancak bunlarla sınırlı değildir. İçindeki bir Spark havuzu herhangi bir kaynak tüketmez. Spark havuzları oluşturmayla ilgili bir maliyet yoktur. Ücretler yalnızca hedef Spark havuzunda bir Spark işi yürütüldüğünde ve Spark örneği isteğe bağlı olarak örneklendirildiğinde tahakkuk edilir.

Spark havuzu oluşturmayı okuyabilir ve bunların tüm özelliklerini nasıl görebileceğiniz [SYNAPSE Analytics 'Te Spark havuzlarıyla çalışmaya başlayın](../quickstart-create-apache-spark-pool-portal.md)

## <a name="nodes"></a>Düğümler

Apache Spark havuz örneği bir baş düğüm ve bir Spark örneğinde en az üç düğüm olan iki veya daha fazla çalışan düğümünden oluşur.  Baş düğüm, Livy, Yarn Kaynak Yöneticisi, Zookeeper ve Spark sürücüsü gibi ek yönetim hizmetleri çalıştırır.  Tüm düğümler düğüm Aracısı ve Yarn düğüm Yöneticisi gibi hizmetleri çalıştırır. Tüm çalışan düğümleri Spark yürütücü hizmetini çalıştırır.

## <a name="node-sizes"></a>Düğüm boyutları

Bir Spark havuzu, 8 sanal çekirdek ve 64 GB bellek içeren küçük bir işlem düğümünden, düğüm başına 64 sanal Core ve 432 GB bellek içeren bir xxx büyük işlem düğümüne kadar olan düğüm boyutları ile tanımlanabilir. Düğüm boyutları, örneğin yeniden başlatılması gerekebilse de havuz oluşturulduktan sonra değiştirilebilir.

|Boyut | Sanal Çekirdek | Bellek|
|-----|------|-------|
|Küçük|4|32 GB|
|Orta|8|64 GB|
|Büyük|16|128 GB|
|XLarge|32|256 GB|
|Xxx büyük|64|432 GB|

## <a name="autoscale"></a>Otomatik Ölçeklendirme

Apache Spark havuzları, işlem kaynaklarını etkinlik miktarına göre otomatik olarak ölçeklendirme ve azaltma olanağı sunar.  Otomatik Ölçeklendirme özelliği etkinleştirildiğinde, ölçeklendirmek için en düşük ve en fazla düğüm sayısını ayarlayabilirsiniz.
Otomatik Ölçeklendirme özelliği devre dışı bırakıldığında, ayarlanan düğüm sayısı düzeltilmeye devam eder.  Bu ayar havuz oluşturulduktan sonra değiştirilebilir, ancak örneğin yeniden başlatılması gerekebilir.

## <a name="automatic-pause"></a>Otomatik duraklama

Otomatik duraklatma özelliği, bir Apache Spark havuzunun genel maliyetini azaltarak bir küme boşta kalma süresinden sonra kaynakları serbest bırakır.  Bu özellik etkinleştirildiğinde, boşta kalma süresi (dakika) sayısı ayarlanabilir.  Otomatik duraklatma özelliği otomatik ölçeklendirme özelliğinden bağımsızdır. Otomatik ölçeklendirme etkin veya devre dışı olduğunda kaynaklar duraklatılamaz.  Bu ayar havuz oluşturulduktan sonra değiştirilebilir, ancak örneğin yeniden başlatılması gerekebilir.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Synapse Analytics](../index.yml)
* [Apache Spark belgeleri](https://spark.apache.org/docs/2.4.5/)