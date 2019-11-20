---
title: Azure depolama ortamlarına veri yükleme-Team Data Science süreci
description: Verilerin depolandığı ve işlendiği çeşitli hedef ortamlara veri alma hakkında bilgi edinin.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/09/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 515decfafe46ad0c1b5b90743688abc26a975903
ms.sourcegitcommit: 87efc325493b1cae546e4cc4b89d9a5e3df94d31
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73053255"
---
# <a name="load-data-into-storage-environments-for-analytics"></a>Analiz için depolama ortamlarına veri yükleme

Ekip veri bilimi süreci, verilerin her bir aşamasında işlenmek veya analiz edilmesi için verilerin farklı depolama ortamlarına toplanmasını veya bu ortamlara yüklenmesini gerektirir. Genellikle işlenmek üzere kullanılan veri hedefleri Azure Blob depolama, SQL Azure veritabanları, Azure VM 'de SQL Server, HDInsight (Hadoop) ve Azure Machine Learning dahildir. 

Aşağıdaki makalelerde verilerin depolandığı ve işlendiği çeşitli hedef ortamlara veri alma işlemi açıklanır.

* [Azure Blob depolama alanı](move-azure-blob.md) /
* [Azure VM 'de SQL Server](move-sql-server-virtual-machine.md) için
* [Azure SQL veritabanı](move-sql-azure.md) 'na
* [Hive tablolarına](move-hive-tables.md)
* [SQL bölümlenmiş tablolara](parallel-load-sql-partitioned-tables.md)
* Şirket [içi SQL Server](move-sql-azure-adf.md)

Teknik ve iş ihtiyaçlarına ek olarak verilerinizin ilk konumu, biçimi ve boyutu, analizinizdeki hedeflere ulaşmak için verilerin alınması gereken hedef ortamları tespit eder. Tahmine dayalı bir model oluşturmak için gereken çeşitli görevleri elde etmek üzere çeşitli ortamlar arasında veri taşınmasını gerektiren bir senaryoda bu durum yaygın olmayan bir durumdur. Bu görev dizisi, örneğin veri araştırma, ön işleme, Temizleme, alt örnekleme ve model eğitimi gibi olabilir.
