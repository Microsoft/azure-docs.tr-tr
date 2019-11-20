---
title: Örnekler & izlenecek yollar
titleSuffix: Azure Data Science Virtual Machine
description: Bu örnekler ve izlenecek yollar sayesinde, Veri Bilimi Sanal Makinesi ortak görevleri ve senaryoları nasıl işleyeceğinizi öğrenin.
keywords: veri bilimi araçları, veri bilimi sanal makinesi, veri bilimi için araçlar, linux veri bilimi
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: ad58adb85077a27bce65595738ffdbd92bace9bd
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71802449"
---
# <a name="samples-on-azure-data-science-virtual-machines"></a>Azure veri bilimi sanal makinelerinde örnekler

Azure veri bilimi sanal makineleri (DSVMs) kapsamlı bir örnek kod kümesi içerir. Bu örneklerde, Python ve R gibi dillerdeki Jupyıter Not defterleri ve betikler bulunur.
> [!NOTE]
> Veri bilimi sanal makinelerinizde Jupyıter not defterlerini çalıştırma hakkında daha fazla bilgi için bkz. [Access jupi](#access-jupyter) Section.

## <a name="prerequisites"></a>Önkoşullar

Bu örnekleri çalıştırmak için bir Veri Bilimi Sanal Makinesi sağlamış olmanız gerekir. Bkz. [Windows](./provision-vm.md) ve [Ubuntu](./dsvm-ubuntu-intro.md)için hızlı başlangıçlara bakın.

## <a name="available-samples"></a>Kullanılabilir örnekler
| Örnek kategorisi | Açıklama | Konumlar |
| ------------- | ------------- | ------------- |
| R dili  | Örnekler, Azure tabanlı bulut veri depoları ile bağlanma ve açık kaynaklı R ve Microsoft Machine Learning Server karşılaştırma gibi senaryolar gösterir. Ayrıca, Microsoft Machine Learning Server ve SQL Server modelleri nasıl kullanıma sunulabileceğini açıklar. <br/> [R dili](#r-language) | <br/>`~notebooks` <br/> <br/> `~samples/MicrosoftR` <br/> <br/> `~samples/RSqlDemo` <br/> <br/> `~samples/SQLRServices`<br/> <br/>|
| Python dili  | Örnek olarak, Azure tabanlı bulut veri depoları ile bağlanma ve Azure Machine Learning ile çalışma gibi senaryolar açıklanmaktadır.  <br/> [Python dili](#python-language) | <br/>`~notebooks` <br/><br/>|
| Julia dili  | Julia 'da çizim ve derin öğrenme hakkında ayrıntılı bir açıklama sağlar. Ayrıca, Julia 'dan C ve Python 'un nasıl çağrılacağını açıklar. <br/> [Julia dili](#julia-language) |<br/> Windows:<br/> `~notebooks/Julia_notebooks`<br/><br/> Linux:<br/> `~notebooks/julia`<br/><br/> |
| Azure Machine Learning  | Machine Learning ile makine öğrenimi ve derin öğrenme modelleri oluşturmayı gösterir. Modelleri her yerde dağıtın. Otomatik makine öğrenimi ve akıllı hiper parametre ayarlamayı kullanın. Model yönetimi ve dağıtılmış eğitim ' i de kullanın. <br/> [Machine Learning](#azure-machine-learning) | <br/>`~notebooks/AzureML`<br/> <br/>|
| PyTorch Not defterleri  | PyTorch tabanlı sinir ağlarını kullanan derin öğrenme örnekleri. Not defterleri acemi 'ten ileri düzey senaryolar arasında değişir.  <br/> [PyTorch Not defterleri](#pytorch) | <br/>`~notebooks/Deep_learning_frameworks/pytorch`<br/> <br/>|
| TensorFlow  |  TensorFlow çerçevesi kullanılarak uygulanan çeşitli sinir ağ örnekleri ve teknikleri. <br/> [TensorFlow](#tensorflow) | <br/>`~notebooks/Deep_learning_frameworks/tensorflow`<br/><br/> |
| Microsoft Bilişsel Araç Seti <br/>   | Microsoft 'ta Cognitive Toolkit ekibi tarafından yayımlanan derin öğrenme örnekleri.  <br/> [Cognitive Toolkit](#cntk) | <br/> `~notebooks/DeepLearningTools/CNTK/Tutorials`<br/><br/> Linux:<br/> `~notebooks/CNTK`<br/> <br/>|
| Caffe2 | Caffe2 tabanlı sinir ağlarını kullanan derin öğrenme örnekleri. Birçok Not defteri, kullanıcılara Caffe2 ve verimli bir şekilde nasıl kullanılacağını öğrenmiştir. Örnek olarak, görüntü ön işleme ve veri kümesi oluşturma sayılabilir. Ayrıca gerileme ve önceden eğitilen modellerin nasıl kullanılacağı de dahil değildir. <br/> [Caffe2](#caffe2) | <br/>`~notebooks/Deep_learning_frameworks/caffe2`<br/><br/> |
| H2O   | Gerçek dünyada sorun senaryoları için H2O kullanan Python tabanlı örnekler. <br/> [H2O](#h2o) | <br/>`~notebooks/h2o`<br/><br/> |
| Parlak ml dili  | Apache Spark 2. x üzerinde Apache Spark için pySpark ve MMLSpark: Microsoft Machine Learning aracılığıyla Apache Spark MLLib araç seti özelliklerini kullanan örnekler.  <br/> [Parlak ml dili](#sparkml) | <br/>`~notebooks/SparkML/pySpark`<br/>`~notebooks/MMLSpark`<br/><br/>  |
| XGBoost | Sınıflandırma ve gerileme gibi senaryolar için XGBoost içindeki standart makine öğrenimi örnekleri. <br/> [XGBoost](#xgboost) | <br/>Windows:<br/>`\dsvm\samples\xgboost\demo`<br/><br/> |

<br/>

## <a name="access-jupyter"></a>Jupyıter 'a erişme 

Jupiter 'a erişmek için masaüstü veya uygulama menüsünde **Jupiter** simgesini seçin. Ayrıca, jupi 'nin bir DSVM 'nin Linux sürümünde de erişebilirsiniz. Bir Web tarayıcısından uzaktan erişmek için Ubuntu 'da `https://<Full Domain Name or IP Address of the DSVM>:8000` ' a gidin.

Özel durumlar eklemek ve Jupyter erişimini bir tarayıcıdan kullanılabilir hale getirmek için aşağıdaki kılavuzu kullanın:


![Jupyıter özel durumunu etkinleştir](./media/ubuntu-jupyter-exception.png)


Veri Bilimi Sanal Makinesi oturum açmak için kullandığınız parolayla oturum açın.
<br/>

**Jupyıter ana**
<br/>![Jupyıter ana @ no__t-1<br/>

## <a name="r-language"></a>R dili 
<br/>![R örnekleri](./media/r-language-samples.png)<br/>

## <a name="python-language"></a>Python dili
<br/>![Python örnekleri](./media/python-language-samples.png)<br/>

## <a name="julia-language"></a>Julia dili 
<br/>![Julia örnekleri](./media/julia-samples.png)<br/>

## <a name="azure-machine-learning"></a>Azure Machine Learning 
<br/>![Azure Machine Learning örnekleri](./media/azureml-samples.png)<br/>

## <a name="pytorch"></a>PyTorch
<br/>![PyTorch örnekleri](./media/pytorch-samples.png)<br/>

## <a name="tensorflow"></a>TensorFlow 
<br/>![TensorFlow örnekleri](./media/tensorflow-samples.png)<br/>


## <a name="cntk"></a>CNTK 
<br/>![CNTK örnekleri](./media/cntk-samples.png)<br/>


## <a name="caffe2"></a>Caffe2 
<br/>![caffe2 örnekleri](./media/caffe2-samples.png)<br/>

## <a name="h2o"></a>H2O 
<br/>![H2O örnekleri](./media/h2o-samples.png)<br/>

## <a name="sparkml"></a>Parlak ml 
<br/>![Mini ml örnekleri](./media/sparkml-samples.png)<br/>

## <a name="xgboost"></a>XGBoost 
<br/>![XGBoost örnekleri](./media/xgboost-samples.png)<br/>

