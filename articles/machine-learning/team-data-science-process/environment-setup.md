---
title: Azure Team Data Science Işleminde veri bilimi ortamlarını ayarlama
description: Team Data Science Işleminde kullanılmak üzere Azure 'da veri bilimi ortamları ayarlayın.
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
ms.openlocfilehash: 557550627fe2c39571a848723e5d716324fee240
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "93321170"
---
# <a name="set-up-data-science-environments-for-use-in-the-team-data-science-process"></a>Team Data Science Process içinde kullanım için veri bilimi ortamlarını ayarlama
Ekip veri bilimi Işlemi, verilerin depolanması, işlenmesi ve çözümlenmesi için çeşitli veri bilimi ortamlarını kullanır. Azure Blob depolama, birkaç tür Azure sanal makinesi, HDInsight (Hadoop) kümesi ve Azure Machine Learning çalışma alanı içerirler. Hangi ortamın kullanılacağına ilişkin karar, modellenecek verilerin türüne ve miktarına ve buluttaki verilerin hedef hedefine bağlıdır. 

* Bu kararı verirken göz önünde bulundurmanız gereken sorular hakkında yönergeler için bkz. [Azure Machine Learning veri bilimi ortamınızı planlayın](plan-your-environment.md). 
* Gelişmiş analizler yaparken karşılaşabileceğiniz bazı senaryolardan bir katalog için bkz [. Team Data Science Işlemi senaryoları](plan-sample-scenarios.md)

Aşağıdaki makalelerde, ekip veri bilimi Işlemi tarafından kullanılan çeşitli veri bilimi ortamlarının nasıl ayarlanacağı açıklanır.

* [Azure depolama hesabı](../../storage/common/storage-account-create.md)
* [HDInsight (Hadoop) kümesi](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md)
* [Azure Machine Learning Studio (klasik) çalışma alanı](../classic/create-workspace.md)

**Microsoft veri bilimi sanal makinesi (DSVM)** Ayrıca bir Azure sanal MAKINESI (VM) görüntüsü olarak da kullanılabilir. Bu VM önceden yüklenir ve veri analizi ve makine öğrenimi için yaygın olarak kullanılan çeşitli popüler araçlarla yapılandırılır. DSVM hem Windows hem de Linux 'ta kullanılabilir. Daha fazla bilgi için bkz. [Linux ve Windows için bulut tabanlı veri bilimi sanal makinesi giriş](../data-science-virtual-machine/overview.md).

Şunları oluşturmayı öğrenin:

- [Windows DSVM](../data-science-virtual-machine/provision-vm.md)
- [Ubuntu DSVM](../data-science-virtual-machine/dsvm-ubuntu-intro.md)
- [CentOS DSVM](../data-science-virtual-machine/release-notes.md)