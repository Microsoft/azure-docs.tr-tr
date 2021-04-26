---
title: Hızlı başlangıç-Azure CLı ile ilk Batch işinizi çalıştırma
description: Bu hızlı başlangıçta, Batch hesabı oluşturma ve Azure CLı ile Batch işi çalıştırma işlemi gösterilmektedir.
ms.topic: quickstart
ms.date: 08/13/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 8d3005233320a7ba0d00f186944a0a8c0c456647
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765314"
---
# <a name="quickstart-run-your-first-batch-job-with-the-azure-cli"></a>Hızlı başlangıç: Azure CLI ile ilk Batch işinizi çalıştırma

Batch hesabı, bir işlem düğümleri Havuzu (sanal makineler) ve havuzdaki görevleri çalıştıran bir iş oluşturmak için Azure CLı kullanarak Azure Batch kullanmaya başlayın. Her örnek görev, havuz düğümlerinden biri üzerinde temel bir komut çalıştırır.

Azure CLI, komut satırından veya betik içindeki Azure kaynaklarını oluşturmak ve yönetmek için kullanılır. Bu hızlı başlangıcı tamamladıktan sonra, Batch hizmetinin temel kavramlarını anlayacak ve Batch’i daha büyük ölçekte daha gerçekçi iş yükleri ile denemeye hazır olacaksınız.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Bu hızlı başlangıç, Azure CLı 'nin sürüm 2.0.20 veya üstünü gerektirir. Azure Cloud Shell kullanılıyorsa, en son sürüm zaten yüklüdür.

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

[az group create](/cli/azure/group#az_group_create) komutuyla bir kaynak grubu oluşturun. Azure kaynak grubu, Azure kaynaklarının dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır.

Aşağıdaki örnek, *eastus2* konumunda *quickstartbatch-RG* adlı bir kaynak grubu oluşturur.

```azurecli-interactive
az group create \
    --name QuickstartBatch-rg \
    --location eastus2
```

## <a name="create-a-storage-account"></a>Depolama hesabı oluşturma

Azure Depolama hesabını Batch hesabınıza bağlayabilirsiniz. Bu hızlı başlangıç için gerekli olmamasına karşın, depolama hesabı uygulamaları dağıtmak ve çoğu gerçek yaşam iş yükleri için giriş ve çıkış verilerini depolamak üzere yararlıdır. Kaynak grubunuzda [az storage account create](/cli/azure/storage/account#az_storage_account_create) komutuyla bir depolama hesabı oluşturun.

```azurecli-interactive
az storage account create \
    --resource-group QuickstartBatch-rg \
    --name mystorageaccount \
    --location eastus2 \
    --sku Standard_LRS
```

## <a name="create-a-batch-account"></a>Batch hesabı oluşturma

[az batch account create](/cli/azure/batch/account#az_batch_account_create) komutuyla bir Batch hesabı oluşturun. İşlem kaynakları (işlem düğümleri havuzları) ve Batch işleri oluşturmak için bir hesaba sahip olmanız gerekir.

Aşağıdaki örnek, *Quickstartbatch-RG* içinde *mybatchaccount* adlı bir Batch hesabı oluşturur ve oluşturduğunuz depolama hesabını bağlar.  

```azurecli-interactive
az batch account create \
    --name mybatchaccount \
    --storage-account mystorageaccount \
    --resource-group QuickstartBatch-rg \
    --location eastus2
```

İşlem havuzlarını ve işlerini oluşturmak ve yönetmek için, Batch ile kimlik doğrulaması yapmalısınız. [az batch account login](/cli/azure/batch/account#az_batch_account_login) komutuyla hesapta oturum açın. Oturumunuz açıldıktan sonra, `az batch` komutlarınız bu hesabın bağlamını kullanır.

```azurecli-interactive
az batch account login \
    --name mybatchaccount \
    --resource-group QuickstartBatch-rg \
    --shared-key-auth
```

## <a name="create-a-pool-of-compute-nodes"></a>İşlem düğümleri havuzu oluşturma

Batch hesabınız olduğuna göre, [az batch pool create](/cli/azure/batch/pool#az_batch_pool_create) komutunu kullanarak örnek Linux işlem düğümleri havuzu oluşturun. Aşağıdaki örnekte, Ubuntu 16.04 LTS çalıştıran iki boyutlu *Standard_A1_v2* düğümlere sahip *mypool* adlı bir havuz oluşturur. Önerilen düğüm boyutu, bu hızlı örnek için performans ile maliyetin iyi bir dengesini sunar.
 
```azurecli-interactive
az batch pool create \
    --id mypool --vm-size Standard_A1_v2 \
    --target-dedicated-nodes 2 \
    --image canonical:ubuntuserver:16.04-LTS \
    --node-agent-sku-id "batch.node.ubuntu 16.04"
```

Batch, havuzu hemen oluşturur ancak işlem düğümlerinin ayrılması ve başlatılması birkaç dakika sürer. Bu süre boyunca, havuz `resizing` durumunda olur. Havuzun durumunu görmek için [az batch pool show](/cli/azure/batch/pool#az_batch_pool_show) komutunu çalıştırın. Bu komut, havuzun tüm özelliklerini gösterir. Dilerseniz belirli özellikler için sorgu gönderebilirsiniz. Aşağıdaki komut havuzun ayırma durumunu alır:

```azurecli-interactive
az batch pool show --pool-id mypool \
    --query "allocationState"
```

Havuzun durumu değişirken iş ve görevleri oluşturmak için aşağıdaki adımlarla devam edin. Ayırma durumu `steady` olduğunda ve düğümler çalıştığında havuz tamamen görev çalıştırmaya hazır olur.

## <a name="create-a-job"></a>Bir iş oluşturma

Bir havuza sahip olduktan sonra üzerinde çalıştıracak bir iş oluşturun. Batch işi bir veya daha fazla görevin mantıksal grubudur. Bir iş, öncelik gibi görevler arasında ortak olan ayarları ve görevlerin çalıştırılacağı havuzu içerir. [az toplu işlem işi oluşturma](/cli/azure/batch/job#az_batch_job_create) komutunu kullanarak Batch işi oluşturma. Aşağıdaki örnek, *mypool* havuzunda *myjob* adlı işi oluşturur. Başlangıçta iş hiçbir görev içermez.

```azurecli-interactive
az batch job create \
    --id myjob \
    --pool-id mypool
```

## <a name="create-tasks"></a>Görev oluşturma

Şimdi ise [az batch task create](/cli/azure/batch/task#az_batch_task_create) komutunu kullanarak işin içinde çalıştırılacak bazı görevler oluşturun. Bu örnekte, dört türdeş görev oluşturursunuz. Her görev, bir işlem düğümündeki Batch ortam değişkenlerini görüntülemek için bir `command-line` çalıştırır ve 90 saniye bekler. Batch kullandığınızda bu komut satırı, uygulamanızı veya betiğinizi belirttiğiniz yerdir. Batch, işlem düğümlerine uygulama ve betik dağıtmanın birkaç yolunu sağlar.

Aşağıdaki Bash betiği, 4 paralel görev (*mytask1* ile *mytask4* arasında) oluşturur.

```azurecli-interactive
for i in {1..4}
do
   az batch task create \
    --task-id mytask$i \
    --job-id myjob \
    --command-line "/bin/bash -c 'printenv | grep AZ_BATCH; sleep 90s'"
done
```

Komut çıktısı her bir görev için ayarları gösterir. Batch, işlem düğümlerine görevleri dağıtır.

## <a name="view-task-status"></a>Görev durumunu görüntüleme

Bir görev oluşturduktan sonra Batch, görevi havuzda çalışmak üzere kuyruğa alır. Görevi çalıştıracak bir düğüm kullanılabilir olduğunda, görev çalışır.

Batch görevlerinin durumunu görüntülemek için [az batch task show](/cli/azure/batch/task#az_batch_task_show) komutunu kullanın. Aşağıdaki örnek, havuz düğümlerinden biri üzerinde çalışan *mytask1* hakkındaki ayrıntıları gösterir.

```azurecli-interactive
az batch task show \
    --job-id myjob \
    --task-id mytask1
```

Komut çıktısı birçok ayrıntıyı içerir, ancak görev komut satırlarından `exitCode` ve `nodeId` komutlarını not edin. 0’daki `exitCode` komutu, görev komut satırının başarıyla tamamlandığını belirtir. `nodeId`, görevin çalıştırıldığı havuz düğümünün kimliğini belirtir.

## <a name="view-task-output"></a>Görev çıktısını görüntüleme

İşlem düğümünde görev tarafından oluşturulan dosyaları listelemek için [az batch task file list](/cli/azure/batch/task) komutunu kullanın. Aşağıdaki komut, *mytask1* tarafından oluşturulan dosyaları listeler:

```azurecli-interactive
az batch task file list \
    --job-id myjob \
    --task-id mytask1 \
    --output table
```

Çıktı aşağıdakine benzer:

```
Name        URL                                                                                         Is Directory      Content Length
----------  ------------------------------------------------------------------------------------------  --------------  ----------------
stdout.txt  https://mybatchaccount.eastus2.batch.azure.com/jobs/myjob/tasks/mytask1/files/stdout.txt  False                  695
certs       https://mybatchaccount.eastus2.batch.azure.com/jobs/myjob/tasks/mytask1/files/certs       True
wd          https://mybatchaccount.eastus2.batch.azure.com/jobs/myjob/tasks/mytask1/files/wd          True
stderr.txt  https://mybatchaccount.eastus2.batch.azure.com/jobs/myjob/tasks/mytask1/files/stderr.txt  False                     0

```

Çıktı dosyalarından birini yerel bir dizine indirmek [az batch task file download](/cli/azure/batch/task) komutunu kullanın. Bu örnekte, görev çıktısı `stdout.txt` durumundadır.

```azurecli-interactive
az batch task file download \
    --job-id myjob \
    --task-id mytask1 \
    --file-path stdout.txt \
    --destination ./stdout.txt
```

`stdout.txt` dosyasının içeriğini bir metin düzenleyicisinde görüntüleyebilirsiniz. İçerik, düğüm üzerinde ayarlanmış Azure Batch ortam değişkenlerini gösterir. Kendi Batch işlerinizi oluşturduğunuzda, görev komut satırlarında bu görev değişkenlerine ve komut satırları tarafından çalıştırılan uygulama ve betiklere başvurabilirsiniz. Örnek:

```
AZ_BATCH_TASK_DIR=/mnt/batch/tasks/workitems/myjob/job-1/mytask1
AZ_BATCH_NODE_STARTUP_DIR=/mnt/batch/tasks/startup
AZ_BATCH_CERTIFICATES_DIR=/mnt/batch/tasks/workitems/myjob/job-1/mytask1/certs
AZ_BATCH_ACCOUNT_URL=https://mybatchaccount.eastus2.batch.azure.com/
AZ_BATCH_TASK_WORKING_DIR=/mnt/batch/tasks/workitems/myjob/job-1/mytask1/wd
AZ_BATCH_NODE_SHARED_DIR=/mnt/batch/tasks/shared
AZ_BATCH_TASK_USER=_azbatch
AZ_BATCH_NODE_ROOT_DIR=/mnt/batch/tasks
AZ_BATCH_JOB_ID=myjobl
AZ_BATCH_NODE_IS_DEDICATED=true
AZ_BATCH_NODE_ID=tvm-257509324_2-20180703t215033z
AZ_BATCH_POOL_ID=mypool
AZ_BATCH_TASK_ID=mytask1
AZ_BATCH_ACCOUNT_NAME=mybatchaccount
AZ_BATCH_TASK_USER_IDENTITY=PoolNonAdmin
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Batch öğreticileri ve örnekleri ile devam etmek istiyorsanız, bu hızlı başlangıçta kullanılan Batch hesabını ve bağlı depolama hesabını kullanın. Batch hesabının kendisi için herhangi bir ücret alınmaz.

Zamanlanmış bir iş olmasa bile, düğümler çalışırken havuzlar için sizden ücret alınır. Artık havuz gerekmiyorsa, [az Batch Pool Delete](/cli/azure/batch/pool#az_batch_pool_delete) komutuyla silin. Havuzu sildiğinizde düğümler üzerindeki tüm görev çıkışları silinir.

```azurecli-interactive
az batch pool delete --pool-id mypool
```

Artık gerekli değilse, [az group delete](/cli/azure/group#az_group_delete) komutunu kullanarak kaynak grubunu, Batch hesabını, havuzları ve tüm ilgili kaynakları kaldırabilirsiniz. Kaynakları aşağıda gösterildiği gibi silin:

```azurecli-interactive
az group delete --name QuickstartBatch-rg
```

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta bir Batch hesabı, Batch havuzu ve Batch işi oluşturdunuz. İş, örnek görevler çalıştırdı ve düğümlerden biri üzerinde oluşturulan çıktıyı görüntülediniz. Batch hizmetinin temel kavramlarını anladıktan sonra, Batch’i daha büyük ölçekte daha gerçekçi iş yükleri ile denemeye hazırsınız. Azure Batch hakkında daha fazla bilgi için Azure Batch öğreticilerine devam edin.

> [!div class="nextstepaction"]
> [Azure Batch öğreticileri](./tutorial-parallel-dotnet.md)
