---
title: Paralel iş yükü çalıştırma - Azure Batch Python
description: Öğretici - Batch Python istemci kitaplığını kullanarak Azure Batch’te ffmpeg ile paralel medya dosyaları işleme
services: batch
author: laurenhughes
manager: gwallace
ms.service: batch
ms.devlang: python
ms.topic: tutorial
ms.date: 11/29/2018
ms.author: lahugh
ms.custom: mvc
ms.openlocfilehash: d06cf74b2a29af3fea2c24facac2899d09a0a84f
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71090779"
---
# <a name="tutorial-run-a-parallel-workload-with-azure-batch-using-the-python-api"></a>Öğretici: Python API 'sini kullanarak Azure Batch ile paralel iş yükü çalıştırma

Büyük ölçekli paralel ve yüksek performanslı bilgi işlem (HPC) toplu işlerini Azure’da verimli bir şekilde çalıştırmak için Azure Batch’i kullanın. Bu öğreticide, Batch kullanarak paralel iş yükü çalıştırmaya ilişkin bir Python örneği açıklanmaktadır. Genel bir Batch uygulaması iş akışı hakkında bilgi alacak ve Batch ve Depolama kaynakları ile programlı olarak etkileşimde bulunmayı öğreneceksiniz. Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:

> [!div class="checklist"]
> * Batch ve Depolama hesapları ile kimlik doğrulaması
> * Depolama hizmetine giriş dosyaları yükleme
> * Bir uygulamayı çalıştırmak için işlem düğümleri havuzu oluşturma
> * Giriş dosyalarını işlemek için bir iş ve görevler oluşturma
> * Görev yürütmeyi izleme
> * Çıkış dosyalarını alma

Bu öğreticide, [ffmpeg](https://ffmpeg.org/) açık kaynak aracını kullanarak MP4 medya dosyalarını MP3 biçimine paralel şekilde dönüştürürsünüz. 

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Önkoşullar

* [Python sürüm 2.7 veya 3.3 ya da üzeri](https://www.python.org/downloads/)

* [pip](https://pip.pypa.io/en/stable/installing/) paket yöneticisi

* Bir Azure Batch hesabı ve bağlı bir Azure Depolama hesabı. Bu hesapları oluşturmak için [Azure portalı](quick-create-portal.md) veya [Azure CLI](quick-create-cli.md) kullanan Batch hızlı başlangıçlarına bakın.

## <a name="sign-in-to-azure"></a>Azure'da oturum açma

[https://portal.azure.com](https://portal.azure.com) adresinden Azure portalında oturum açın.

[!INCLUDE [batch-common-credentials](../../includes/batch-common-credentials.md)] 

## <a name="download-and-run-the-sample"></a>Örneği indirme ve çalıştırma

### <a name="download-the-sample"></a>Örneği indirme

GitHub’dan [örnek uygulamayı indirin veya kopyalayın](https://github.com/Azure-Samples/batch-python-ffmpeg-tutorial). Örnek uygulama deposunu bir Git istemcisi ile kopyalamak için aşağıdaki komutu kullanın:

```
git clone https://github.com/Azure-Samples/batch-python-ffmpeg-tutorial.git
```

`batch_python_tutorial_ffmpeg.py` dosyasını içeren dizine gidin.

Python ortamınızda `pip` kullanarak gerekli paketleri yükleyin.

```bash
pip install -r requirements.txt
```

`config.py` dosyasını açın. Batch ve depolama hesabı kimlik bilgilerini, hesaplarınıza özel değerlerle güncelleştirin. Örneğin:


```Python
_BATCH_ACCOUNT_NAME = 'mybatchaccount'
_BATCH_ACCOUNT_KEY = 'xxxxxxxxxxxxxxxxE+yXrRvJAqT9BlXwwo1CwF+SwAYOxxxxxxxxxxxxxxxx43pXi/gdiATkvbpLRl3x14pcEQ=='
_BATCH_ACCOUNT_URL = 'https://mybatchaccount.mybatchregion.batch.azure.com'
_STORAGE_ACCOUNT_NAME = 'mystorageaccount'
_STORAGE_ACCOUNT_KEY = 'xxxxxxxxxxxxxxxxy4/xxxxxxxxxxxxxxxxfwpbIC5aAWA8wDu+AFXZB827Mt9lybZB1nUcQbQiUrkPtilK5BQ=='
```

### <a name="run-the-app"></a>Uygulamayı çalıştırma

Betiği çalıştırmak için:

```
python batch_python_tutorial_ffmpeg.py
```

Örnek uygulamayı çalıştırdığınızda, konsol çıktısı aşağıdakine benzer. Yürütme sırasında, havuzun işlem düğümleri başlatıldığı sırada `Monitoring all tasks for 'Completed' state, timeout in 00:30:00...` konumunda bir duraklama yaşarsınız. 
   
```
Sample start: 11/28/2018 3:20:21 PM

Container [input] created.
Container [output] created.
Uploading file LowPriVMs-1.mp4 to container [input]...
Uploading file LowPriVMs-2.mp4 to container [input]...
Uploading file LowPriVMs-3.mp4 to container [input]...
Uploading file LowPriVMs-4.mp4 to container [input]...
Uploading file LowPriVMs-5.mp4 to container [input]...
Creating pool [LinuxFFmpegPool]...
Creating job [LinuxFFmpegJob]...
Adding 5 tasks to job [LinuxFFmpegJob]...
Monitoring all tasks for 'Completed' state, timeout in 00:30:00...
Success! All tasks completed successfully within the specified timeout period.
Deleting container [input]....

Sample end: 11/28/2018 3:29:36 PM
Elapsed time: 00:09:14.3418742
```

Havuz, işlem düğümleri, iş ve görevleri izlemek için Azure portalında Batch hesabınıza gidin. Örneğin, havuzunuzdaki işlem düğümlerinin ısı haritasını görmek için **Havuzlar** > *LinuxFFmpegPool* öğesine tıklayın.

Görevler çalıştırılırken ısı haritası aşağıdakine benzer:

![Havuz ısı haritası](./media/tutorial-parallel-python/pool.png)

Uygulama varsayılan yapılandırmasında çalıştırıldığında tipik yürütme süresi yaklaşık **5 dakikadır**. En uzun süreyi havuz oluşturma işlemi alır. 

[!INCLUDE [batch-common-tutorial-download](../../includes/batch-common-tutorial-download.md)]

## <a name="review-the-code"></a>Kodu gözden geçirin

Aşağıdaki bölümlerde örnek uygulama, Batch hizmetinde iş yükünü işlemeyi gerçekleştiren adımlara ayrılmıştır. Örnekteki her bir kod satırı ele alınmadığı için, bu makalenin geri kalanını okurken Python koduna bakın.

### <a name="authenticate-blob-and-batch-clients"></a>Blob ve Batch istemcilerinde kimlik doğrulaması

Bir depolama hesabıyla etkileşimde bulunmak için uygulama, [azure-storage-blob](https://pypi.python.org/pypi/azure-storage-blob) paketini kullanarak bir [BlockBlobService](/python/api/azure-storage-blob/azure.storage.blob.blockblobservice.blockblobservice) nesnesi oluşturur.

```python
blob_client = azureblob.BlockBlobService(
    account_name=_STORAGE_ACCOUNT_NAME,
    account_key=_STORAGE_ACCOUNT_KEY)
```

Uygulama, Batch hizmetinde havuz, iş ve görevleri oluşturup yönetmek üzere bir [BatchServiceClient](/python/api/azure.batch.batchserviceclient) nesnesi oluşturur. Örnekteki Batch istemcisi, paylaşılan anahtar kimlik doğrulaması kullanır. Batch ayrıca bireysel kullanıcıların ya da katılımsız bir uygulamanın kimlik doğrulamasını yapmak için [Azure Active Directory](batch-aad-auth.md) aracılığıyla kimlik doğrulamayı destekler.

```python
credentials = batchauth.SharedKeyCredentials(_BATCH_ACCOUNT_NAME,
                                             _BATCH_ACCOUNT_KEY)

batch_client = batch.BatchServiceClient(
    credentials,
    base_url=_BATCH_ACCOUNT_URL)
```

### <a name="upload-input-files"></a>Giriş dosyalarını karşıya yükleme

Uygulama, giriş MP4 dosyaları için bir depolama kapsayıcısı ve görev çıkışı için bir kapsayıcı oluşturmak üzere `blob_client` başvurusunu kullanır. Daha sonra, yerel `InputFiles` dizinindeki MP4 dosyalarını kapsayıcıya yüklemek üzere `upload_file_to_container` işlevini çağırır. Depolama alanındaki dosyalar, Batch hizmetinin daha sonra işlem düğümlerine indirebileceği Batch [ResourceFile](/python/api/azure-batch/azure.batch.models.resourcefile) nesneleri olarak tanımlanır.

```python
blob_client.create_container(input_container_name, fail_on_exist=False)
blob_client.create_container(output_container_name, fail_on_exist=False)
input_file_paths = []

for folder, subs, files in os.walk(os.path.join(sys.path[0], './InputFiles/')):
    for filename in files:
        if filename.endswith(".mp4"):
            input_file_paths.append(os.path.abspath(
                os.path.join(folder, filename)))

# Upload the input files. This is the collection of files that are to be processed by the tasks.
input_files = [
    upload_file_to_container(blob_client, input_container_name, file_path)
    for file_path in input_file_paths]
```

### <a name="create-a-pool-of-compute-nodes"></a>İşlem düğümleri havuzu oluşturma

Ardından örnek, `create_pool` çağrısıyla Batch hesabında bir işlem düğümü havuzu oluşturur. Bu tanımlı işlev; düğüm sayısını, VM boyutunu ve havuz yapılandırmasını ayarlamak üzere Batch [PoolAddParameter](/python/api/azure-batch/azure.batch.models.pooladdparameter) sınıfını kullanır. Burada [Virtualmachineconfiguration](/python/api/azure-batch/azure.batch.models.virtualmachineconfiguration) nesnesi, Azure Marketi 'Nde yayınlanan Ubuntu Server 18,04 LTS görüntüsüne bir [ImageReference](/python/api/azure-batch/azure.batch.models.imagereference) belirtir. Batch, Azure Market’te çok çeşitli VM görüntülerinin yanı sıra özel VM görüntülerini destekler.

Düğüm sayısı ve VM boyutu, tanımlı sabitler kullanılarak ayarlanır. Batch, adanmış düğümleri ve [düşük öncelikli düğümleri](batch-low-pri-vms.md) destekler ve havuzlarınızda bunlardan birini ya da her ikisini birden kullanabilirsiniz. Adanmış düğümler, havuzunuz için ayrılmıştır. Düşük öncelikli düğümler ise Azure’daki fazlalık VM kapasitesinden indirimli bir fiyat karşılığında sunulur. Azure’da yeterli kapasite yoksa düşük öncelikli düğümler kullanılamaz duruma gelir. Örnek, varsayılan olarak *Standard_A1_v2* boyutunda yalnızca 5 düşük öncelikli düğüm içeren bir havuz oluşturur. 

Fiziksel düğüm özelliklerine ek olarak, bu havuz yapılandırması bir [StartTask](/python/api/azure-batch/azure.batch.models.starttask) nesnesi içerir. StartTask, her düğümü havuza katıldığında ve her yeniden başlatıldığında yürütecektir. Bu örnekte StartTask, ffmpeg paketini ve bağımlılıkları düğümlere yüklemek için Bash kabuk komutları çalıştırır.

[Pool.add](/python/api/azure-batch/azure.batch.operations.pooloperations) yöntemi, havuzu Batch hizmetine gönderir.

```python
new_pool = batch.models.PoolAddParameter(
    id=pool_id,
    virtual_machine_configuration=batchmodels.VirtualMachineConfiguration(
        image_reference=batchmodels.ImageReference(
            publisher="Canonical",
            offer="UbuntuServer",
            sku="18.04-LTS",
            version="latest"
        ),
        node_agent_sku_id="batch.node.ubuntu 18.04"),
    vm_size=_POOL_VM_SIZE,
    target_dedicated_nodes=_DEDICATED_POOL_NODE_COUNT,
    target_low_priority_nodes=_LOW_PRIORITY_POOL_NODE_COUNT,
    start_task=batchmodels.StartTask(
        command_line="/bin/bash -c \"apt-get update && apt-get install -y ffmpeg\"",
        wait_for_success=True,
        user_identity=batchmodels.UserIdentity(
            auto_user=batchmodels.AutoUserSpecification(
                scope=batchmodels.AutoUserScope.pool,
                elevation_level=batchmodels.ElevationLevel.admin)),
    )
)
batch_service_client.pool.add(new_pool)
```

### <a name="create-a-job"></a>İş oluştur

Bir Batch işi, üzerinde görevlerin çalıştırılacağı bir havuz ve iş için öncelik ile zamanlama gibi isteğe bağlı ayarları belirtir. Örnek, `create_job` çağrısıyla bir iş oluşturur. Bu tanımlı işlev, havuzunuzda bir iş oluşturmak üzere [JobAddParameter](/python/api/azure-batch/azure.batch.models.jobaddparameter) sınıfını kullanır. [Job.add](/python/api/azure-batch/azure.batch.operations.joboperations) yöntemi, havuzu Batch hizmetine gönderir. Başlangıçta iş hiçbir görev içermez.

```python
job = batch.models.JobAddParameter(
    id=job_id,
    pool_info=batch.models.PoolInformation(pool_id=pool_id))

batch_service_client.job.add(job)
```

### <a name="create-tasks"></a>Görev oluşturma

Uygulama, `add_tasks` çağrısıyla iş içinde görevler oluşturur. Bu tanımlı işlev, [TaskAddParameter](/python/api/azure-batch/azure.batch.models.taskaddparameter) sınıfını kullanarak görev nesnelerinin bir listesini oluşturur. Her görev, bir `command_line` parametresi kullanarak giriş `resource_files` nesnesini işlemek üzere ffmpeg çalıştırır. ffmpeg, daha önce havuz oluşturulduğunda her bir düğüme yüklenmiştir. Burada komut satırı, her bir giriş MP4 (video) dosyasını bir MP3 (ses) dosyasına dönüştürmek için ffmpeg çalıştırır.

Örnek, komut satırını çalıştırdıktan sonra MP3 dosyası için bir [OutputFile](/python/api/azure-batch/azure.batch.models.outputfile) nesnesi oluşturur. Her bir görevin çıkış dosyaları (bu örnekte bir tane), görevin `output_files` özelliği kullanılarak bağlı depolama hesabındaki bir kapsayıcıya yüklenir.

Sonra uygulama, [task.add_collection](/python/api/azure-batch/azure.batch.operations.taskoperations) yöntemi ile görevleri işe ekler ve işlem düğümleri üzerinde çalışmak üzere kuyruğa alır. 

```python
tasks = list()

for idx, input_file in enumerate(input_files):
    input_file_path = input_file.file_path
    output_file_path = "".join((input_file_path).split('.')[:-1]) + '.mp3'
    command = "/bin/bash -c \"ffmpeg -i {} {} \"".format(
        input_file_path, output_file_path)
    tasks.append(batch.models.TaskAddParameter(
        id='Task{}'.format(idx),
        command_line=command,
        resource_files=[input_file],
        output_files=[batchmodels.OutputFile(
            file_pattern=output_file_path,
            destination=batchmodels.OutputFileDestination(
                container=batchmodels.OutputFileBlobContainerDestination(
                    container_url=output_container_sas_url)),
            upload_options=batchmodels.OutputFileUploadOptions(
                upload_condition=batchmodels.OutputFileUploadCondition.task_success))]
    )
    )
batch_service_client.task.add_collection(job_id, tasks)
```

### <a name="monitor-tasks"></a>Görevleri izleme

Bir işe görevler eklendiğinde, Batch bu görevleri ilişkili havuzdaki işlem düğümleri üzerinde yürütülmek üzere otomatik olarak kuyruğa alır ve zamanlar. Belirttiğiniz ayarlara göre, Batch tüm kuyruğa alma, zamanlama, yeniden deneme görevlerini ve diğer görev yönetimi sorumluluklarını yerine getirir. 

Görevin yürütülüşünün izlenmesi için birçok yaklaşım vardır. Bu örnekteki `wait_for_tasks_to_complete` işlevi, belirli bir durum (bu örnekte tamamlanmış durum) için bir süre boyunca görevleri izlemek üzere [TaskState](/python/api/azure-batch/azure.batch.models.taskstate) nesnesini kullanır.

```python
while datetime.datetime.now() < timeout_expiration:
    print('.', end='')
    sys.stdout.flush()
    tasks = batch_service_client.task.list(job_id)

    incomplete_tasks = [task for task in tasks if
                        task.state != batchmodels.TaskState.completed]
    if not incomplete_tasks:
        print()
        return True
    else:
        time.sleep(1)
...
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Görevleri çalıştırdıktan sonra, uygulama kendi oluşturduğu giriş depolama kapsayıcısını otomatik olarak siler ve Batch havuzu ve işini silme seçeneğini sunar. BatchClient'ın [JobOperations](/python/api/azure-batch/azure.batch.operations.joboperations) ve [PoolOperations](/python/api/azure-batch/azure.batch.operations.pooloperations) sınıflarının her ikisi de, silmeyi onaylamanız durumunda çağrılan silme yöntemleri içerir. İşlerin ve görevlerin kendileri için sizden ücret alınmasa da işlem düğümleri için ücret alınır. Bu nedenle, havuzları yalnızca gerektiğinde ayırmanız önerilir. Havuzu sildiğinizde düğümler üzerindeki tüm görev çıkışları silinir. Ancak, giriş ve çıkış dosyaları depolama hesabında kalır.

Kaynak grubunu, Batch hesabını ve depolama hesabını artık gerekli değilse silin. Azure portalında bu işlemi yapmak için Batch hesabına ait kaynak grubunu seçin ve **Kaynak Grubunu Sil**’e tıklayın.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunlar hakkında bilgi edindiniz:

> [!div class="checklist"]
> * Batch ve Depolama hesapları ile kimlik doğrulaması
> * Depolama hizmetine giriş dosyaları yükleme
> * Bir uygulamayı çalıştırmak için işlem düğümleri havuzu oluşturma
> * Giriş dosyalarını işlemek için bir iş ve görevler oluşturma
> * Görev yürütmeyi izleme
> * Çıkış dosyalarını alma

Batch iş yüklerini zamanlamak ve işlemek üzere Python API kullanmaya ilişkin daha fazla örnek için GitHub üzerindeki örneklere bakın.

> [!div class="nextstepaction"]
> [Batch Python örnekleri](https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch)

