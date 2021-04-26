---
title: Hive-Team Data Science Işlemini kullanarak Azure HDInsight Hadoop Analytics
description: Tahmine dayalı analiz yapmak için Azure HDInsight Hadoop üzerinde Hive kullanımını adım adım sunan Team Data Science süreci örnekleri.
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
ms.openlocfilehash: f72ea6ed5f0eec076d181ef56c99c4f1308a7741
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "75864171"
---
# <a name="hdinsight-hadoop-data-science-walkthroughs-using-hive-on-azure"></a>Azure 'da Hive kullanarak HDInsight Hadoop veri bilimi talimatları 

Bu izlenecek yollar, tahmine dayalı analiz yapmak için bir HDInsight Hadoop kümesiyle Hive kullanır. Bunlar, Team Data Science Işleminde özetlenen adımları izler. Ekip veri bilimi sürecine genel bir bakış için bkz. [veri bilimi işlemi](overview.md). Azure HDInsight 'a giriş için bkz. [Azure HDInsight 'A giriş, Hadoop teknoloji yığını ve Hadoop kümeleri](../../hdinsight/hadoop/apache-hadoop-introduction.md).

Ekip veri bilimi sürecini yürütecek olan ek veri bilimi talimatları, kullandıkları **platforma** göre gruplandırılır. Bu örneklerin bir öğesi için [ekip veri bilimi sürecini yürüten Izlenecek yollara](walkthroughs.md) bakın.


## <a name="predict-taxi-tips-using-hive-with-hdinsight-hadoop"></a>HDInsight Hadoop ile Hive kullanarak TAXI ipuçlarını tahmin etme

[HDInsight Hadoop kümeleri kullan](hive-walkthrough.md) izlenecek yolu, New York Vergilenicisinin verilerini kullanır: 

- Bir ipucunun ödenip ödenmediğini belirtir 
- İpucu tutarlarının dağılımı

Senaryo, [Azure HDInsight Hadoop kümesiyle](https://azure.microsoft.com/services/hdinsight/)Hive kullanılarak uygulanır. Genel olarak kullanılabilen bir NYC taksi seyahat ve tarifeli havayolu veri kümesinden verileri nasıl depolayacağınızı, keşfedeceğinizi ve özellik mühendisini öğreneceksiniz. Ayrıca, modelleri derlemek ve dağıtmak için Azure Machine Learning de kullanabilirsiniz.

## <a name="predict-advertisement-clicks-using-hive-with-hdinsight-hadoop"></a>HDInsight Hadoop ile Hive kullanarak tanıtım tıklamalarını tahmin etme

[1 TB 'lık bir veri kümesi Kılavuzu üzerindeki kullanım Azure HDInsight Hadoop kümeleri](hive-criteo-walkthrough.md) , bir ipucunun ödenip ödenmediğini ve beklenen miktarları tahmin etmek için [genel kullanıma açık](https://labs.criteo.com/downloads/download-terabyte-click-logs/) bir veri kümesi kullanır. Senaryo, örnek verileri depolamak, araştırmak, özellik mühendisi ve kapatmak için bir [Azure HDInsight Hadoop kümesiyle](https://azure.microsoft.com/services/hdinsight/) Hive kullanılarak uygulanır. Kullanıcının bir tanıtıma tıklayıp tıklamadığını tahmin eden bir ikili sınıflandırma modeli oluşturmak, eğmek ve puan almak için Azure Machine Learning kullanır. İzlenecek yol, bu modellerden birinin bir Web hizmeti olarak nasıl yayımlanacağını gösterir.


## <a name="next-steps"></a>Sonraki adımlar

Ekip veri bilimi sürecini oluşturan anahtar bileşenleriyle ilgili bir tartışma için bkz. [Team Data Science işlemine genel bakış](overview.md).

Veri bilimi projelerinizi yapılandırmak için kullanabileceğiniz Team Data Science Işlem yaşam döngüsünün bir tartışması için bkz. [Team Data Science işlem yaşam döngüsü](lifecycle.md). Yaşam döngüsü, başlangıçtan sonuna kadar olan adımları özetler, bu da projeler genellikle yürütüldüğünde izler. 

