---
title: Farklı Azure depolama konumlarında örnek veriler-ekip veri bilimi Işlemi
description: Azure Blob kapsayıcıları, SQL Server ve Hive tablolarındaki örnek veriler, daha küçük ancak temsili ve daha yönetilebilir bir boyutla azaltılmasını sağlar.
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
ms.openlocfilehash: 878a2a47dfaddf1d3399a1dbd98865332838e148
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "93321752"
---
# <a name="sample-data-in-azure-blob-containers-sql-server-and-hive-tables"></a><a name="heading"></a>Azure blob kapsayıcıları, SQL Server ve Hive tablolarındaki örnek veriler

Aşağıdaki makalelerde, üç farklı Azure konumundan birinde depolanan verilerin nasıl örneklenmesi anlatılmaktadır:

* [**Azure Blob kapsayıcı verileri**](sample-data-blob.md) programlı bir şekilde indirilerek örneklenir ve örnek Python kodu ile örnekleme yapılır.
* [**SQL Server veriler**](sample-data-sql-server.md) hem SQL hem de Python programlama dili kullanılarak örneklenir. 
* Hive [**Tablo verileri**](sample-data-hive.md) , Hive sorguları kullanılarak örneklenir.

Bu örnekleme görevi, [ekip veri bilimi işlemindeki (TDSP)](./index.yml)bir adımdır.

**Örnek veriler neden**

Çözümlemeyi planladığınız veri kümesi büyükse, daha küçük ancak temsili ve daha yönetilebilir bir boyutla azaltmak için verileri düşürmek genellikle iyi bir fikirdir. Yeniden boyutlandırma, veri anlama, araştırma ve özellik mühendisliğini kolaylaştırabilir. Cortana Analytics Işlemindeki bu örnekleme rolü, veri işleme işlevlerinin ve makine öğrenimi modellerinin hızlı prototipini etkinleştirmektir.