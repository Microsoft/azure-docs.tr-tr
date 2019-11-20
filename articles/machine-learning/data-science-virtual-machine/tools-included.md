---
title: DSVM'ye Dahil Edilen Araçlar
titleSuffix: Azure Data Science Virtual Machine tools
description: Windows ve Ubuntu DSVM görüntülerine dahil olan araçların listesi
keywords: veri bilimi araçları, veri bilimi sanal makinesi, veri bilimi için araçlar, linux veri bilimi
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: overview
ms.date: 10/10/2019
ms.openlocfilehash: 594879df6e1e88c861a2730160d050194cc884ea
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73891071"
---
# <a name="what-tools-are-included-on-the-azure-data-science-virtual-machine"></a>Azure Veri Bilimi Sanal Makinesi hangi araçlar dahildir?

Aşağıda, her aracın nasıl yapılandırıldığını anlamak için bağlantılarla birlikte Veri Bilimi Sanal Makinesi eklenen araçların güncel bir listesini sunuyoruz.


| **Araç**                                                           | **Windows DSVM** | **Linux DSVM** | **Kullanım notları** |
| :------------------------------------------------------------------ |:-------------------:|:------------------:|:------:|
| Popüler paketlerin önceden yüklü olduğu [Microsoft R Open](https://mran.microsoft.com/open/)   |<span class='green-check'>&#9989;</span></br> 3\.4.3                    |<span class='green-check'>&#9989;</span></br> 3\.4.3  | [DSVM üzerinde R](./dsvm-languages.md#r)           |
| [Microsoft Machine Learning Server (R, Python)](https://docs.microsoft.com/machine-learning-server/) Geliştirici sürümü şunları içerir: <br />  &nbsp;&nbsp;&nbsp;&nbsp; geri alınamaz [/iptal edilebilir](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-revoscaler) paralel ve dağıtılmış yüksek performanslı Framework (R ve Python)<br />  &nbsp;&nbsp;&nbsp;&nbsp; [Microsoftml](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-the-microsoftml-package), Microsoft 'un yeni son teknoloji ürünü makine öğrenimi algoritmaları <br />  &nbsp;&nbsp;&nbsp;&nbsp; [R ve Python işlemleştirme](https://docs.microsoft.com/machine-learning-server/what-is-operationalization)                                            |<span class='green-check'>&#9989;</span>  </br> (9.3.0)                 |<span class='green-check'>&#9989;</span> | |
| [Microsoft Office](https://www.microsoft.com/p/office-365-proplus/CFQ7TTC0K8R0) Paylaşılan etkinleştirme ile ProPlus: Excel, Word ve PowerPoint   |<span class='green-check'>&#9989;</span>                       |<span class='red-x'>&#10060;</span>              | |
| Popüler paketler önceden yüklenmiş [Anaconda Python](https://www.continuum.io/) 2,7 ve 3,5    |<span class='green-check'>&#9989;</span>                       |<span class='green-check'>&#9989;</span></br> (4,2)        | |
| Julia diline yönelik popüler paketlerin önceden yüklü olduğu [JuliaPro](https://juliacomputing.com/products/juliapro.html)                         |<span class='green-check'>&#9989;</span> </br> (0.6.4)                   |<span class='green-check'>&#9989;</span></br> (0.6.2)              | [DSVM 'de Julia](./dsvm-languages.md#julia) |
| İlişkisel veritabanları                                                            | [SQL Server 2017](https://www.microsoft.com/sql-server/sql-server-2017) <br/> Developer Edition| [PostgreSQL](https://www.postgresql.org/) (CentOS),<br/>[SQL Server 2017](https://www.microsoft.com/sql-server/sql-server-2017) <br/> Developer Edition (Ubuntu) | [DSVM üzerinde SQL Server](./dsvm-data-platforms.md#sql-server-2017-developer-edition) |
| Veritabanı araçları                                                       |  SQL Server Management Studio <br/> SQL Server Integration Services<br/> [BCP, sqlcmd](https://docs.microsoft.com/sql/tools/command-prompt-utility-reference-database-engine)<br />  ODBC/JDBC sürücüleri|  [SQUIRREL SQL](http://squirrel-sql.sourceforge.net/) (Sorgulama Aracı), <br />  BCP, sqlcmd <br />  ODBC/JDBC sürücüleri| |
| SQL Server Machine Learning Hizmetleri (R, Python) ile ölçeklenebilir veritabanı Analizi |<span class='green-check'>&#9989;</span>    | <span class='red-x'>&#10060;</span>                | |
| Aşağıdaki çekirdekler ile [sunucu Jupyter Notebook](https://jupyter.org/) :                                  |<span class='green-check'>&#9989;</span></br> 5\.5.0   |<span class='green-check'>&#9989;</span> | [Jupyter Notebook örnekleri](./dsvm-samples-and-walkthroughs.md) | 
|     &nbsp;&nbsp;&nbsp;&nbsp; R |<span class='green-check'>&#9989;</span></br> 3\.4.3 |<span class='green-check'>&#9989;</span></br> 3\.4.3 | [R jupi örnekleri](./dsvm-samples-and-walkthroughs.md#r-language) |
|     &nbsp;&nbsp;&nbsp;&nbsp; Python |<span class='green-check'>&#9989;</span></br> 03 |<span class='green-check'>&#9989;</span> | [Python Jupyıter örnekleri](./dsvm-samples-and-walkthroughs.md#python-language) |
|     &nbsp;&nbsp;&nbsp;&nbsp; Julia |<span class='green-check'>&#9989;</span></br> (0.6.4) |<span class='green-check'>&#9989;</span></br> (0.6.2) | [Julia Jupyıter örnekleri](./dsvm-samples-and-walkthroughs.md#julia-language) |
|     &nbsp;&nbsp;&nbsp;&nbsp; PySpark |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [pySpark Jupyıter örnekleri](./dsvm-samples-and-walkthroughs.md#sparkml)
|     &nbsp;&nbsp;&nbsp;&nbsp; [parlak Magic](https://github.com/jupyter-incubator/sparkmagic) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> (Yalnızca Ubuntu) | |
|     &nbsp;&nbsp;&nbsp;&nbsp; parlak r     |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | |
| JupyterHub (çok kullanıcılı Not defteri sunucusu)|<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | |
| JupyterLab (çok kullanıcılı Not defteri sunucusu) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> | |
| Geliştirme araçları, IDEs ve kod düzenleyicileri:| | |
| &nbsp;&nbsp;&nbsp; &nbsp;git eklentisi, Azure HDInsight (Hadoop), Azure Data Lake, SQL Server Veri Araçları, [Node. js](https://github.com/Microsoft/nodejstools), [Python](https://aka.ms/ptvs)ve [Visual Studio için R araçları (Rtvs)](https://microsoft.github.io/RTVS-docs/) Ile [Visual Studio 2017 (Community Edition)](https://www.visualstudio.com/community/) |<span class='green-check'>&#9989;</span> |<span class='red-x'>&#10060;</span>   | [DSVM 'de Visual Studio](./dsvm-tools-development.md#visual-studio-community-2017) |
| &nbsp;&nbsp;&nbsp;&nbsp; [Visual Studio Code](https://code.visualstudio.com/) |<span class='green-check'>&#9989;</span></br> (1.31.1) |<span class='green-check'>&#9989;</span></br> (1,31) | [DSVM üzerinde Visual Studio Code](./dsvm-tools-development.md#visual-studio-code) |
| &nbsp;&nbsp;&nbsp;&nbsp; [rstudio masaüstü](https://www.rstudio.com/products/rstudio/#Desktop) |<span class='green-check'>&#9989;</span></br> (1.2.50 xx) |<span class='green-check'>&#9989;</span></br> (1.1.456) | [DSVM üzerinde RStudio Masaüstü](./dsvm-tools-development.md#rstudio--desktop) |
| &nbsp;&nbsp;&nbsp;&nbsp; [rstudio Server](https://www.rstudio.com/products/rstudio/#Server) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | [DSVM üzerinde RStudio sunucusu](./dsvm-tools-development.md#rstudio--server)
| &nbsp;&nbsp;&nbsp;&nbsp; [Pyın Community Edition](https://www.jetbrains.com/pycharm/) |<span class='green-check'>&#9989;</span></br> (19.2.3)  |<span class='green-check'>&#9989;</span></br> (2018.2.3) | [DSVM üzerinde Pydüğmesinden](./dsvm-tools-development.md#pycharm)
| &nbsp;&nbsp;&nbsp;&nbsp; [atom](https://atom.io/) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> (1.26.1) | |
| &nbsp;&nbsp;&nbsp;&nbsp; [Juno (JULIA IDE)](https://junolab.org/)|<span class='green-check'>&#9989;</span> |<span class='red-x'>&#10060;</span> | [DSVM 'deki Juno](./dsvm-tools-development.md#juno)
| &nbsp;&nbsp;&nbsp;&nbsp; [VIM](https://www.vim.org) |<span class='green-check'>&#9989;</span></br> (8.1.5) |<span class='green-check'>&#9989;</span></br> (7.4.1689) | |
| &nbsp;&nbsp;&nbsp;&nbsp; [Emacs](https://www.gnu.org/software/emacs) | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span></br> (24.5.1) | |
| [Git](https://git-scm.com/) ve git bash &nbsp;&nbsp;&nbsp;&nbsp; |<span class='green-check'>&#9989;</span></br> (2.20.1) |<span class='green-check'>&#9989;</span></br> (0.6.2) | |
| &nbsp;&nbsp;&nbsp;&nbsp; [OpenJDK](https://openjdk.java.net) |<span class='green-check'>&#9989;</span></br> (1.8.0 _201) |<span class='green-check'>&#9989;</span></br> (1.8.0 _222) | |
| &nbsp;&nbsp;&nbsp;&nbsp; .NET Framework |<span class='green-check'>&#9989;</span></br> 4\.7.2 |<span class='red-x'>&#10060;</span>   | |
| [Power BI Desktop](https://powerbi.microsoft.com/) |<span class='green-check'>&#9989;</span></br> (2.73.55 xx) |<span class='red-x'>&#10060;</span>   |
| Azure ve Cortana Intelligence Hizmet paketine erişim SDK’ları |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [DSVM 'de masaüstü Power BI](./dsvm-tools-development.md#power-bi-desktop) |
| Veri taşıma ve yönetim araçları: | | |
| &nbsp;&nbsp;&nbsp;&nbsp; [Azure Depolama Gezgini](https://azure.microsoft.com/features/storage-explorer/) |<span class='green-check'>&#9989;</span></br> (1.10.1) |<span class='green-check'>&#9989;</span></br> (0.7.20160129.1) | |
| &nbsp;&nbsp;&nbsp;&nbsp; [Azure CLI](https://docs.microsoft.com/cli/azure) |<span class='green-check'>&#9989;</span></br> (2.0.56) |<span class='green-check'>&#9989;</span></br> (2.0.58) | |
| &nbsp;&nbsp;&nbsp;&nbsp; [AzCopy](https://docs.microsoft.com/azure/storage/storage-use-azcopy) |<span class='green-check'>&#9989;</span></br> (8.1.0) |<span class='red-x'>&#10060;</span>   | [DSVM üzerinde AzCopy](./dsvm-tools-ingestion.md#azcopy)
| &nbsp;&nbsp;&nbsp;&nbsp; [blobu sigortası sürücüsü](https://github.com/Azure/azure-storage-fuse) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> 1\.0.2 | [DSVM 'de blobsigortası](./dsvm-tools-ingestion.md#blobfuse)
| &nbsp;&nbsp;&nbsp;&nbsp; [AdlCopy (Azure Data Lake Storage)](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob) |<span class='green-check'>&#9989;</span></br> (1.0.13) |<span class='red-x'>&#10060;</span>   | [DSVM 'de AdlCopy](./dsvm-tools-ingestion.md#adlcopy)
| &nbsp;&nbsp;&nbsp;&nbsp; [Azure Cosmos DB veri geçiş aracı](https://docs.microsoft.com/azure/documentdb/documentdb-import-data) |<span class='green-check'>&#9989;</span> |<span class='red-x'>&#10060;</span>   | [DSVM üzerinde Cosmos DB](./dsvm-tools-ingestion.md#azure-cosmos-db-data-migration-tool) |
| &nbsp;&nbsp;&nbsp;&nbsp; UNIX/Linux komut satırı araçları |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | |
| Veri araştırması için [Apache detaya gitme](https://drill.apache.org) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span></br>(1.14.0) | [DSVM 'de Apache detaya gitme](./dsvm-tools-explore-and-visualize.md#apache-drill)
| Machine Learning araçları: ||||
| [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) (R, Python) ile &nbsp;&nbsp;&nbsp;&nbsp; tümleştirmesi |<span class='green-check'>&#9989;</span></br> (0.2.7) |<span class='green-check'>&#9989;</span></br> (1.0.45) | [Azure ML SDK 'Sı](./dsvm-ml-data-science-tools.md#azure-machine-learning-sdk-for-python)
| &nbsp;&nbsp;&nbsp;&nbsp; [Xgboost](https://github.com/dmlc/xgboost) |<span class='green-check'>&#9989;</span></br> (0,81) |<span class='green-check'>&#9989;</span></br> (0,80) | [DSVM 'de XGBoost](./dsvm-ml-data-science-tools.md#xgboost)
| &nbsp;&nbsp;&nbsp;&nbsp; [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span></br> (8,1) | [DSVM 'de Vowpal Wabbit](./dsvm-ml-data-science-tools.md#vowpal-wabbit)
| &nbsp;&nbsp;&nbsp;&nbsp; [WEKA](https://www.cs.waikato.ac.nz/ml/weka/) |<span class='green-check'>&#9989;</span></br> (3,8) |<span class='green-check'>&#9989;</span></br> (3.8.0) |
| &nbsp;&nbsp;&nbsp;&nbsp; [Rattle](https://togaware.com/rattle/) |<span class='green-check'>&#9989;</span></br> 5\.1.0 |<span class='green-check'>&#9989;</span></br> 5\.2.2 | [DSVM üzerinde WEKA](./dsvm-ml-data-science-tools.md#weka) |
| &nbsp;&nbsp;&nbsp;&nbsp; [Lightgbm](https://github.com/Microsoft/LightGBM) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> 2\.1.2 'yi | [DSVM üzerinde LightGBM](./dsvm-ml-data-science-tools.md#lightgbm)
| &nbsp;&nbsp;&nbsp;&nbsp; [Catboost](https://tech.yandex.com/catboost/) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> (0.10.3) | |
| &nbsp;&nbsp;&nbsp;&nbsp; [H2O](https://www.h2o.ai/h2o/), [parlak su](https://www.h2o.ai/sparkling-water/) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> (3.20.0.7) | [DSVM üzerinde H20](./dsvm-ml-data-science-tools.md#h2o) |
| GPU veya CPU üzerinde çalışan derinlemesine öğrenme araçları: |  |  |  |
| &nbsp;&nbsp;&nbsp;&nbsp; [Caffe](https://github.com/BVLC/caffe) ve [Caffe2](https://github.com/facebookarchive/caffe2) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | DSVM 'de [Caffe](./dsvm-deep-learning-ai-frameworks.md#caffe) ve [Caffe2](./dsvm-deep-learning-ai-frameworks.md#caffe2) |
| &nbsp;&nbsp;&nbsp;&nbsp; [Chainer](https://chainer.org) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> 5\.2.0 | [DSVM üzerinde Chainer](./dsvm-deep-learning-ai-frameworks.md#chainer) |
| &nbsp;&nbsp;&nbsp;&nbsp; [CUDA, cuDNN, NVIDIA sürücü](https://developer.nvidia.com/cuda-toolkit) |<span class='green-check'>&#9989;</span></br> (10.0.130) |<span class='green-check'>&#9989;</span></br> (10.0.130) | [DSVM üzerinde CUDA, cuDNN, NVıDıA sürücüsü](./dsvm-deep-learning-ai-frameworks.md#cuda-cudnn-nvidia-driver) |
| &nbsp;&nbsp;&nbsp;&nbsp; [Horovod](https://github.com/horovod/horovod) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> (0.16.1) | [DSVM 'de horovod](./dsvm-deep-learning-ai-frameworks.md#horovod) |
| &nbsp;&nbsp;&nbsp;&nbsp; [keras](https://keras.io) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> (2.2.4) | [DSVM üzerinde keras](./dsvm-deep-learning-ai-frameworks.md#keras) |
| &nbsp;&nbsp;&nbsp;&nbsp; [Microsoft Cognitive Toolkit (CNTK)](https://docs.microsoft.com/cognitive-toolkit/) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span></br> 2\.5.1 | [DSVM üzerinde CNTK](./dsvm-deep-learning-ai-frameworks.md#microsoft-cognitive-toolkit-cntk) |
| &nbsp;&nbsp;&nbsp;&nbsp; [Mxnet](https://mxnet.apache.org/) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span></br> (1.3.0) | [DSVM üzerinde MXNet](./dsvm-deep-learning-ai-frameworks.md#mxnet) |
| &nbsp;&nbsp;&nbsp;&nbsp; [Mxnet model sunucusu](https://github.com/awslabs/mxnet-model-server#quick-start) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span></br> 1\.0.1 | [DSVM 'de MXNet model sunucusu](./dsvm-deep-learning-ai-frameworks.md#mxnet-model-server) |
| &nbsp;&nbsp;&nbsp;&nbsp; [NVIDIA sistem yönetimi arabirimi (NVIDIA-SMI)](https://developer.nvidia.com/nvidia-system-management-interface) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span></br>| [DSVM 'de NVIDIA-SMI](./dsvm-deep-learning-ai-frameworks.md#nvidia-system-management-interface-nvidia-smi) |
| &nbsp;&nbsp;&nbsp;&nbsp; [Pytorch](https://pytorch.org) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> 1\.2.0 | [DSVM üzerinde PyTorch](./dsvm-deep-learning-ai-frameworks.md#pytorch) |
| &nbsp;&nbsp;&nbsp;&nbsp; [TensorFlow](https://www.tensorflow.org) |<span class='green-check'>&#9989;</span></br> (1,13) |<span class='green-check'>&#9989;</span></br> (1,13) | [DSVM 'de TensorFlow](./dsvm-deep-learning-ai-frameworks.md#tensorflow) |
| &nbsp;&nbsp;&nbsp;&nbsp; [TensorFlow hizmeti](https://www.tensorflow.org/tfx/guide/serving) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> 1\.12.0 | [DSVM 'ye hizmet veren TensorFlow](./dsvm-deep-learning-ai-frameworks.md#tensorflow-serving) |
| &nbsp;&nbsp;&nbsp;&nbsp; [Theano](https://github.com/Theano/Theano) | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span></br> (1.0.3) | [DSVM üzerindeki Ano](./dsvm-deep-learning-ai-frameworks.md#theano)

