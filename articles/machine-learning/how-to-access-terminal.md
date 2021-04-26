---
title: Çalışma alanınızdaki bir işlem örneği terminaline erişme
titleSuffix: Azure Machine Learning
description: Terminal 'yi git işlemleri için bir işlem örneği üzerinde kullanın, paketleri yükler ve çekirdekler ekleyin.
services: machine-learning
author: abeomor
ms.author: osomorog
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.date: 02/05/2021
ms.openlocfilehash: 6f6c8e309fde92b0c2cf6fabac3489557b0b6b93
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106062395"
---
# <a name="access-a-compute-instance-terminal-in-your-workspace"></a>Çalışma alanınızdaki bir işlem örneği terminaline erişin

Çalışma alanınızdaki bir işlem örneğinin terminaline şu şekilde erişin:

* Git ve sürüm dosyalarındaki dosyaları kullanın. Bu dosyalar, çalışma alanı dosya sisteminizde depolanır, tek bir işlem örneğiyle sınırlı değildir.
* Paketleri işlem örneğine yükler.
* İşlem örneğinde ek çekirdekler oluşturun.

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği. Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://aka.ms/AMLFree) oluşturun.
* Machine Learning çalışma alanı. Bkz. [Azure Machine Learning çalışma alanı oluşturma](how-to-manage-workspace.md).

## <a name="access-a-terminal"></a>Bir terminale erişme

Terminale erişmek için:

1. Çalışma alanınızı [Azure Machine Learning Studio](https://ml.azure.com)'da açın.
1. Sol tarafta, **Not defterleri**' ni seçin.
1. **Açık Terminal** görüntüsünü seçin.

    :::image type="content" source="media/how-to-use-terminal/open-terminal-window.png" alt-text="Terminal penceresini aç":::

1. Bir işlem örneği çalışırken, bu işlem örneği için Terminal penceresi görüntülenir.
1. İşlem örneği çalıştırıldığında, bir işlem örneği başlatmak veya oluşturmak için sağdaki **işlem** bölümünü kullanın.
    :::image type="content" source="media/how-to-use-terminal/start-or-create-compute.png" alt-text="Bir işlem örneği başlatın veya oluşturun":::

Yukarıdaki adımlara ek olarak terminalden da erişebilirsiniz:

* RStudio: sol üst taraftaki **Terminal** sekmesini seçin.
* Jupyter Laboratuvarı: Başlatıcı sekmesinde **diğer** başlığın altında bulunan **Terminal** kutucuğunu seçin.
* Jupyter: Dosyalar sekmesinde sağ üstteki **yeni>Terminal** ' i seçin.
* İşlem örneği oluşturulduğunda SSH erişimini etkinleştirdiyseniz makineye SSH.

## <a name="copy-and-paste-in-the-terminal"></a>Terminalde Kopyala ve Yapıştır

> * Windows: `Ctrl-Insert` kopyalayıp `Ctrl-Shift-v` yapıştırmak için veya kullanın `Shift-Insert` .
> * Mac OS: `Cmd-c` kopyalamak ve `Cmd-v` yapıştırmak için.
> * FireFox/IE Pano izinlerini düzgün şekilde desteklemiyor olabilir.

## <a name="use-files-from-git-and-version-files"></a><a name=git></a> Git ve sürüm dosyalarındaki dosyaları kullan

Terminalden tüm git işlemlerine erişin. Tüm git dosyaları ve klasörleri, çalışma alanı dosya sisteminizde depolanacak. Bu depolama, bu dosyaları çalışma alanınızdaki herhangi bir işlem örneğinden kullanmanıza olanak sağlar.

> [!NOTE]
> Dosya ve klasörlerinizi **~/CloudFiles/Code/Users** klasörünün altında herhangi bir yere ekleyerek tüm jupi ortamlarınızda görünür olmaları gerekir.

[Git depoları çalışma alanı dosya sisteminize kopyalama](concept-train-model-git-integration.md#clone-git-repositories-into-your-workspace-file-system)hakkında daha fazla bilgi edinin.

## <a name="install-packages"></a>Paketleri yükler

 Paketleri bir terminal penceresinden yükler. Python paketlerini **python 3,6-AzureML** ortamına yükler.  R paketlerini **r** ortamına yükler.

Ya da paketleri doğrudan Jupyter Notebook veya RStudio 'Ya yükleyebilirsiniz:

* RStudio sağ alt köşedeki **paketler** sekmesini veya sol üstteki **konsol** sekmesini kullanın.  
* Python: Jupyter Notebook bir hücrede Install kodu ekleyin ve yürütün.

> [!NOTE]
> Bir not defteri içinde paket yönetimi için **% PIP** veya **% Conda** Magic işlevlerini kullanarak paketleri, tüm paketlere (Şu anda çalışan çekirdeklerdeki paketler dahil), tüm paketlere başvuran **! PIP** veya **! Conda** yerine **çalışmakta olan çekirdeğe** otomatik olarak yükler.

## <a name="add-new-kernels"></a>Yeni çekirdekler Ekle

> [!WARNING]
>  İşlem örneğini özelleştirirken, **azureml_py36** Conda ortamını veya **Python 3,6-azureml** çekirdeğini sildiğinizden emin olun. Bu, Jupyıter/jupen Terlab işlevselliği için gereklidir

İşlem örneğine yeni bir Jupyter çekirdeği eklemek için:

1. Yeni bir ortam oluşturmak için Terminal penceresini kullanın.  Örneğin, aşağıdaki kod oluşturulur `newenv` :

    ```shell
    conda create --name newenv
    ```

1. Ortamı etkinleştirin.  Örneğin, oluşturduktan sonra `newenv` :

    ```shell
    conda activate newenv
    ```

1. Yeni ortama privand ipykernel paketini yükleyip bu Conda env için bir çekirdek oluşturun

    ```shell
    conda install pip
    conda install ipykernel
    python -m ipykernel install --user --name newenv --display-name "Python (newenv)"
    ```

[Kullanılabilir Jupyter kernels](https://github.com/jupyter/jupyter/wiki/Jupyter-kernels) 'leri yüklenebilir.

## <a name="manage-terminal-sessions"></a>Terminal oturumlarını yönetme

 Tüm etkin Terminal oturumlarının listesini görmek için Terminal araç çubuğunda **etkin oturumları görüntüle** ' yi seçin. Etkin oturum olmadığında bu sekme devre dışı bırakılır.

İşlem örneğinizin kaynaklarını korumak için kullanılmayan oturumları kapatın.