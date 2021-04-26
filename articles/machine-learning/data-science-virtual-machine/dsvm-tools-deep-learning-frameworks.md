---
title: Ayrıntılı öğrenme & AI çerçeveleri
titleSuffix: Azure Data Science Virtual Machine
description: Azure Veri Bilimi Sanal Makinesi 'de kullanılabilir derin öğrenme çerçeveleri ve araçları.
keywords: veri bilimi araçları, veri bilimi sanal makinesi, veri bilimi için araçlar, linux veri bilimi
services: machine-learning
ms.service: data-science-vm
ms.custom: devx-track-python
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 07/17/2020
ms.openlocfilehash: 43a8004b6cdfe2e074304569b83360101784a87e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105568010"
---
# <a name="deep-learning-and-ai-frameworks-for-the-azure-data-science-vm"></a>Azure Veri Bilimi VM'si için derin öğrenme ve AI çerçeveleri
DSVM üzerindeki derin öğrenme çerçeveleri aşağıda listelenmiştir.

## <a name="caffe"></a>[Caffe](https://github.com/BVLC/caffe)

| Kategori | Değer |
| ------------- | ------------- |
| Desteklenen sürüm (ler) | |
| Desteklenen DSVM sürümleri      | Ubuntu 16.04    |
| DSVM 'de nasıl yapılandırılır/yüklenir?  | Caffe, ' da yüklüdür `/opt/caffe` .   Örnekler içinde `/opt/caffe/examples` .|
| Nasıl çalıştırılır?      | X2Go kullanarak sanal makinenizde oturum açın ve ardından yeni bir Terminal başlatın ve aşağıdakileri girin:<br/>`cd /opt/caffe/examples`<br/>`source activate root`<br/>`jupyter notebook`<br/><br/>Örnek Not defterleri ile yeni bir tarayıcı penceresi açılır. İkililer/seçenek/Caffe/Build/Install/bina 'da yüklenir.<br/><br/>Caffe 'nin yüklü sürümü Python 2,7 gerektirir ve varsayılan olarak etkinleştirilen Python 3,6 ile çalışmaz. Python 2,7 ' ye geçmek için, `source activate root` Anaconda ortamına geçiş yapmak üzere öğesini çalıştırın.|    

## <a name="caffe2"></a>[Caffe2](https://github.com/caffe2/caffe2)

| Kategori | Değer |
| ------------- | ------------- |
| Desteklenen sürüm (ler) | |
| Desteklenen DSVM sürümleri      | Ubuntu 16.04     |
| DSVM 'de nasıl yapılandırılır/yüklenir?  | Caffe2, [Python 2,7 (root) Conda ortamına yüklenir. |
| Nasıl çalıştırılır?      | Terminal: Python 'u başlatın ve Caffe2 içeri aktarın. <br/> * JupyterHub: [jupyterhub 'A bağlanın](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)ve ardından örnek not defterlerini bulmak için Caffe2 dizinine gidin. Bazı Not defterleri, Python kodunda Caffe2 kökünün ayarlanmış olmasını gerektirir; /seçenek/caffe2yazın. |

## <a name="chainer"></a>[Chainer](https://chainer.org/)

| Kategori | Değer |
| ------------- | ------------- |
| Desteklenen sürüm (ler) | 5.2 |
| Desteklenen DSVM sürümleri      | Ubuntu 16.04    |
| DSVM 'de nasıl yapılandırılır/yüklenir?  | Chainer, Python 3,5 ' ye yüklenir. |
| Nasıl çalıştırılır?      | Terminal: Python 3,5 ortamını etkinleştirin, çalıştırın `python` ve ardından `import chainer` . <br/> * JupyterHub: [jupyterhub 'A bağlanın](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)ve ardından örnek not defterlerini bulmak Için Chainer dizinine gidin.| 

## <a name="cuda-cudnn-nvidia-driver"></a>[CUDA, cuDNN, NVIDIA Sürücüsü](https://developer.nvidia.com/cuda-toolkit)

| Kategori | Değer |
| ------------- | ------------- |
| Desteklenen sürüm (ler) | 10.0.130|
| Desteklenen DSVM sürümleri      | Windows Server 2019<br>Ubuntu 18.04<br> Windows 2016<br> Ubuntu 16.04  |
| DSVM 'de nasıl yapılandırılır/yüklenir?  |_NVIDIA-SMI_ sistem yolunda kullanılabilir.  |
| Nasıl çalıştırılır?      | Bir komut istemi (Windows üzerinde) veya bir Terminal (Linux 'ta) açın ve ardından _NVIDIA-SMI_' yi çalıştırın. |


## <a name="horovod"></a>[Horovod](https://github.com/uber/horovod)

| Kategori | Değer |
| ------------- | ------------- |
| Desteklenen sürüm (ler) | 0.16.1|
| Desteklenen DSVM sürümleri      | Ubuntu 18.04<br> Ubuntu 16.04   |
| DSVM 'de nasıl yapılandırılır/yüklenir?  | Horovod, Python 3,5 ' de yüklü |
| Nasıl çalıştırılır?      | Terminalde doğru ortamı etkinleştirin ve Python 'u çalıştırın. |

## <a name="keras"></a>[Keras](https://keras.io/)

| Kategori | Değer |
| ------------- | ------------- |
| Desteklenen sürüm (ler) | 2.2.4 |
| Desteklenen DSVM sürümleri      | Windows Server 2019<br>Ubuntu 18.04<br> Windows 2016 <br> Ubuntu 16.04   |
| DSVM 'de nasıl yapılandırılır/yüklenir?  | Keras, Python 3,6 ' ye Windows üzerinde ve Linux 'ta Python 3,5 ' de yüklendi |
| Nasıl çalıştırılır?      | Terminalde doğru ortamı etkinleştirin ve Python 'u çalıştırın. |

## <a name="microsoft-cognitive-toolkit-cntk"></a>[Microsoft Cognitive Toolkit (CNTK)](/cognitive-toolkit/)

| Kategori | Değer |
| ------------- | ------------- |
| Desteklenen sürüm (ler) | 2.5.1 |
| Desteklenen DSVM sürümleri      | Windows 2016 <br> Ubuntu 16.04   |
| DSVM 'de nasıl yapılandırılır/yüklenir?  | CNTK, [Windows 2016](dsvm-tools-languages.md#python-windows-server-2016-edition) ' de Python 3,6 ' de ve [Linux](./dsvm-tools-languages.md#python-linux-edition)üzerinde Python 3,5 ' de yüklüdür) |
| Nasıl çalıştırılır?      | Terminal: doğru ortamı etkinleştirin ve Python 'U çalıştırın. <br/>Jupyter: [Jupyter](provision-vm.md) veya [Jupyterhub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)'a bağlanın ve ardından örnekler için cntk dizinini açın. |

## <a name="mxnet"></a>[MXNet](https://mxnet.apache.org/)
| Kategori | Değer |
| ------------- | ------------- |
| Desteklenen sürüm (ler) | 1.3.0 |
| Desteklenen DSVM sürümleri      | Windows 2016 <br> Ubuntu 16.04    |
| DSVM 'de nasıl yapılandırılır/yüklenir?  | MXNet, Windows 'da `C:\dsvm\tools\mxnet` ve `/dsvm/tools/mxnet` Ubuntu 'da yüklüdür. Python bağlamaları [Windows 2016](dsvm-tools-languages.md#python-windows-server-2016-edition) ' de Python 3,6 ' ye yüklenir ve [Linux](./dsvm-tools-languages.md#python-linux-edition)üzerinde Python 3,5 ' de bulunur), Ubuntu dsvm 'ye de R bağlamaları dahildir. |
| Nasıl çalıştırılır?      | Terminal: doğru Conda ortamını etkinleştirip çalıştırın `import mxnet` . <br/>Jupyter: [Jupyter](provision-vm.md#access-the-dsvm) veya [Jupyterhub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)'a bağlanın ve sonra `mxnet` örnekler için dizini açın. |

## <a name="mxnet-model-server"></a>[MXNet Model Server](https://github.com/awslabs/mxnet-model-server#quick-start)

| Kategori | Değer |
| ------------- | ------------- |
| Desteklenen sürüm (ler) | 1.0.1 |
| Desteklenen DSVM sürümleri      | Windows 2016 <br> Ubuntu 16.04    |
| DSVM 'de nasıl yapılandırılır/yüklenir?  | MXNet model sunucusu, [Windows 2016](dsvm-tools-languages.md#python-windows-server-2016-edition) ' de Python 3,6 ' de ve [Linux](./dsvm-tools-languages.md#python-linux-edition)üzerinde Python 3,5 ' de yüklüdür) |
| Nasıl çalıştırılır?      | Terminal: `sudo systemctl stop jupyterhub` her ikisi de aynı bağlantı noktasını dinlemek için, önce JupyterHub hizmetini durdurmak üzere çalıştırın. Ardından, doğru Conda ortamını etkinleştirip çalıştırın `mxnet-model-server --start --models squeezenet=https://s3.amazonaws.com/model-server/model_archive_1.0/squeezenet_v1.1.mar` |

## <a name="nvidia-system-management-interface-nvidia-smi"></a>[NVIDIA sistem yönetimi arabirimi (NVIDIA-SMI)](https://developer.nvidia.com/nvidia-system-management-interface)

| Kategori | Değer |
| ------------- | ------------- |
| Desteklenen sürüm (ler) |  |
| Desteklenen DSVM sürümleri      | Windows Server 2019<br>Ubuntu 18.04<br> Windows 2016<br> Ubuntu 16.04   |
| Ne için? | GPU etkinliğini sorgulamak için NVıDıA aracı |
| DSVM 'de nasıl yapılandırılır/yüklenir?  | `nvidia-smi` sistem yolunda. |
| Nasıl çalıştırılır?      | **GPU 'nun bulunduğu** bir sanal makinede, bir komut istemi açın (Windows üzerinde) veya bir terminalde (Linux 'ta) ve ardından komutunu çalıştırın `nvidia-smi` . |

## <a name="pytorch"></a>[PyTorch](https://pytorch.org/)

| Kategori | Değer |
| ------------- | ------------- |
| Desteklenen sürüm (ler) | 1.2.0 (Ubuntu 16,04), 1.7.1 (Ubuntu 18,04, Windows 2019) |
| Desteklenen DSVM sürümleri      | Windows Server 2019<br>Ubuntu 18.04<br> Ubuntu 16.04 |
| DSVM 'de nasıl yapılandırılır/yüklenir?  | Py37_pytorch Conda ortamı altında [Python 3,7](dsvm-tools-languages.md#python-linux-edition) ' ye yüklenmiştir. Örnek Jupkiter Not defterleri dahil edilmiştir ve örnekler/dsvm/Samples/pytorch. |
| Nasıl çalıştırılır?      | Terminal: doğru ortamı etkinleştirin ve sonra Python 'u çalıştırın.<br/>* [Jupyterhub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine): bağlanın ve ardından örnekler Için PyTorch dizinini açın.  |

## <a name="tensorflow"></a>[TensorFlow](https://www.tensorflow.org/)

| Kategori | Değer |
| ------------- | ------------- |
| Desteklenen sürüm (ler) | 1.13 |
| Desteklenen DSVM sürümleri      | Windows Server 2019<br>Ubuntu 18.04<br> Windows 2016 <br> Ubuntu 16.04 |
| DSVM 'de nasıl yapılandırılır/yüklenir?  | [Windows 2016](dsvm-tools-languages.md#python-windows-server-2016-edition) üzerinde [Linux](dsvm-tools-languages.md#python-linux-edition) ve Python 3,6 ' de Python 3,5 ' ye yüklendi |
| Nasıl çalıştırılır?      | Terminal: doğru ortamı etkinleştirin ve sonra Python 'u çalıştırın. <br/> * Jupyter: [Jupyter](provision-vm.md) veya [Jupyterhub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)'a bağlanın ve örnekler için TensorFlow dizinini açın.   |

## <a name="tensorflow-serving"></a>[TensorFlow Serving](https://www.tensorflow.org/tfx/guide/serving)

| Kategori | Değer |
| ------------- | ------------- |
| Desteklenen sürüm (ler) | 1.12 |
| Desteklenen DSVM sürümleri      | Ubuntu 16.04 |
| DSVM 'de nasıl yapılandırılır/yüklenir?  | tensorflow_model_server terminalde mevcuttur. |
| Nasıl çalıştırılır?      |  Örnekler [çevrimiçi](https://www.tensorflow.org/tfx/guide/serving)olarak kullanılabilir.   |


## <a name="theano"></a>[Theano](https://github.com/Theano/Theano)

| Kategori | Değer |
| ------------- | ------------- |
| Desteklenen sürüm (ler) | 1.0.3 |
| Desteklenen DSVM sürümleri      | Ubuntu 16.04 |
| DSVM 'de nasıl yapılandırılır/yüklenir?  |Bu, Python 2,7 ' de (_root_) ve Python 3,5 (_py35_) ortamında yüklenir. |
| Nasıl çalıştırılır?      |  Terminal: istediğiniz Python sürümünü etkinleştirin (root veya py35), Python 'u çalıştırın ve ardından Ano içeri aktarın.<br/>* Jupyter: Python 2,7 veya 3,5 çekirdeğini seçin ve sonra da Ano içeri aktarın.  <br/>Son matematik çekirdek kitaplığı (MKL) hatasını geçici olarak çözmek için öncelikle MKL iş parçacığı katmanını aşağıdaki şekilde ayarlamanız gerekir:<br/><br/>`export MKL_THREADING_LAYER=GNU`  |
