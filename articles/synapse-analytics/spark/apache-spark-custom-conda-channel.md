---
title: Paket yönetimi için özel Conda kanalı oluşturma
description: Paket yönetimi için özel bir Conda kanalı oluşturmayı öğrenin
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: midesa
ms.reviewer: jrasnick
ms.subservice: spark
ms.openlocfilehash: 26b6adefd2d334c9fe570bfa7e63bb06b55b9d20
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588777"
---
# <a name="create-a-custom-conda-channel-for-package-management"></a>Paket yönetimi için özel bir Conda kanalı oluşturma 
Python paketleri yüklenirken, Conda Package Manager paketleri aramak için kanalları kullanır. Çeşitli nedenlerle özel bir Conda kanalı oluşturmanız gerekebilir. Örneğin, şunları görebilirsiniz:

- çalışma alanınız veri dışlanan korumalı ve giden bağlantılar engellenir.  
- ortak depolara yüklemek istemediğiniz paketleriniz vardır.
- çalışma alanınızdaki kullanıcılar için ne gibi alternatif depo ayarlamak istiyorsunuz.

Bu makalede, Azure Data Lake Storage hesabınızda özel Conda kanalını oluşturmanıza yardımcı olacak adım adım bir kılavuz sunuyoruz.

## <a name="set-up-your-local-machine"></a>Yerel makinenizi ayarlama

1. Yerel makinenize Conda 'yi kurun. Aynı çalışma zamanında kullanılan Conda sürümünü belirlemek için [Azure SYNAPSE Spark çalışma zamanına](./apache-spark-version-support.md) başvurabilirsiniz.
   
2. Özel bir kanal oluşturmak için Conda-Build ' ı kurun.
```
conda install conda-build
```
3. ' Deki tüm paketleri, hizmeti sağlamak istediğiniz platforma göre düzenleyin. Bu örnekte, yerel makinenize Anaconda Arşivi yükleyeceğiz.

```
sudo wget https://repo.continuum.io/archive/Anaconda3-4.4.0-Linux-x86_64.sh 
sudo chmod +x Anaconda3-4.4.0-Linux-x86_64.sh  
sudo bash Anaconda3-4.4.0-Linux-x86_64.sh -b -p /usr/lib/anaconda3 
export PATH="/usr/lib/anaconda3/bin:$PATH" 
sudo chmod 777 -R /usr/lib/anaconda3a.  
```
## <a name="mount-the-storage-account-onto-your-machine"></a>Depolama hesabını makinenize bağlama
Sonra, Azure Data Lake Storage 2. hesabını yerel makinenize bağlayacağız. Bu işlem, bir de bir. Ancak, ADLSg2 hesabı için bir örnek ekleyeceğiz 
 
Depolama hesabını yerel makinenize bağlama hakkında daha fazla bilgi için [Bu sayfayı](https://github.com/Azure/azure-storage-fuse#blobfuse )ziyaret edebilirsiniz. 

1. Microsoft ürünleri için Linux yazılım deposundan blobsigortası yükleyebilirsiniz.

```
wget https://packages.microsoft.com/config/ubuntu/16.04/packages-microsoft-prod.deb 
sudo dpkg -i packages-microsoft-prod.deb 
sudo apt-get update 
sudo apt-get install blobfuse fuse 
export AZURE_STORAGE_ACCOUNT=<<myaccountname>
export AZURE_STORAGE_ACCESS_KEY=<<myaccountkey>>
export AZURE_STORAGE_BLOB_ENDPOINT=*.dfs.core.windows.net 
```

2. Bağlama noktanızı oluşturun ( ```mkdir /path/to/mount``` ) ve blobsigortası ile bir blob kapsayıcısı bağlayın. Bu örnekte, **myContainer** değişkeni için **privatechannel** değerini kullanalım.
   
```
blobfuse /path/to/mount --container-name=mycontainer --tmp-path=/mnt/blobfusetmp --use-adls=true --log-level=LOG_DEBUG 
sudo mkdir -p /mnt/blobfusetmp
sudo chown <myuser> /mnt/blobfusetmp
```
## <a name="create-the-channel"></a>Kanalı oluşturma
Sonraki adımlarda, özel bir Conda kanalı oluşturacağız. 

1. Yerel makinenizde, Özel kanalınıza yönelik tüm paketleri düzenlemek için bir dizin oluşturun.
   
```
mkdir /home/trusted-service-user/privatechannel 
cd ~/privatechannel/ 
mkdir channel1/linux64 
```

2. Tüm ```tar.bz2``` paketleri dizininden https://repo.anaconda.com/pkgs/main/linux-64/ alt dizine düzenleyin. Tüm bağımlı tar. bz2 paketlerini de eklediğinizden emin olun. 

```
cd channel1 
mkdir noarch 
echo '{}' > noarch/repodata.json 
bzip2 -k noarch/repodata.json 

// Create channel 

conda index channel1/noarch 
conda index channel1/linux-64 
conda index channel1 
```

Daha fazla bilgi için, özel kanallar oluşturmak üzere [Conda Kullanıcı kılavuzunu da ziyaret](https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/create-custom-channels.html) edebilirsiniz. 

## <a name="storage-account-permissions"></a>Depolama hesabı izinleri
Artık depolama hesabındaki izinleri doğrulamamız gerekir. Bu izinleri ayarlamak için, özel kanalın oluşturulacağı yola gidin. Ardından, ```privatechannel``` okuma, listeleme ve yürütme izinlerine sahip olan için BIR SAS belirteci oluşturun. 

Kanal adı artık bu işlemden oluşturulan blob SAS URL 'SI olacaktır.  

## <a name="create-a-sample-conda-environment-configuration-file"></a>Örnek Conda ortam yapılandırma dosyası oluşturma
Son olarak, bir örnek Conda dosyası oluşturarak yükleme işlemini doğrulayın ```environment.yml``` . DEP etkin bir çalışma alanında varsa, bu ``nodefaults`` kanalı ortam dosyanızda belirtmeniz gerekir.

Örnek bir Conda yapılandırma dosyası aşağıda verilmiştir:
```
name: sample 
channels: 
  - https://<<storage account name>>.blob.core.windows.net/privatechannel/channel?<<SAS Token>
  - nodefaults 
dependencies: 
  - openssl 
  - ncurses 
```
Örnek Conda dosyasını oluşturduktan sonra, bir sanal Conda ortamı oluşturabilirsiniz. 

```
conda env create –file sample.yml  
source activate env 
conda list 
```
Özel kanalınızı doğruladığınıza göre, Apache Spark havuzunuzdaki kitaplıkları güncelleştirmek için [Python havuzu yönetimi](./apache-spark-manage-python-packages.md) işlemini kullanabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar
- Varsayılan kitaplıkları görüntüleme: [Apache Spark sürüm desteği](apache-spark-version-support.md)
- Python paketlerini yönetme: [Python paket yönetimi](./apache-spark-manage-python-packages.md)

