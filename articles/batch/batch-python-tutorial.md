---
title: "Öğretici - Python için Azure Batch SDK’sını kullanma | Microsoft Belgeleri"
description: "Temel Azure Batch kavramlarını öğrenin ve Python kullanarak basit bir çözüm derleyin."
services: batch
documentationcenter: python
author: tamram
manager: timlt
editor: 
ms.assetid: 42cae157-d43d-47f8-88f5-486ccfd334f4
ms.service: batch
ms.devlang: python
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-compute
ms.date: 02/27/2017
ms.author: tamram
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: d1c327e90ca3343703784c22aba93280d4599723
ms.lasthandoff: 04/03/2017


---
# <a name="get-started-with-the-batch-sdk-for-python"></a>Python için Batch SDK'sını kullanmaya başlama

> [!div class="op_single_selector"]
> * [.NET](batch-dotnet-get-started.md)
> * [Python](batch-python-tutorial.md)
>
>

Python’da yazılmış küçük bir Batch uygulamasından söz ettiğimizden, [Azure Batch][azure_batch] ve [Batch Python][py_azure_sdk] istemcisi hakkında temel bilgileri alın. İki örnek betiğin buluttaki Linux sanal makinelerde paralel bir iş yükünü işlemek için Batch hizmetini nasıl kullandıklarına; dosya hazırlığı ve alımı için [Azure Depolama](../storage/storage-introduction.md) ile nasıl etkileşime girdiklerine bakacağız. Ortak Batch uygulama iş akışını öğrenmenin yanı sıra işler, görevler, havuzlar ve işlem düğümü gibi başlıca Batch bileşenleri hakkında da temel bir anlayış kazanacaksınız.

![Batch çözümü iş akışı (temel)][11]<br/>

## <a name="prerequisites"></a>Ön koşullar
Bu makalede, Python ve Linux alışkanlığına sahip olduğunuz varsayılmaktadır. Azure’ün yanı sıra Batch ve Storage hizmetleri için aşağıda belirtilen hesap oluşturma gerekliliklerini karşılayabildiğiniz de varsayılmaktadır.

### <a name="accounts"></a>Hesaplar
* **Azure hesabı**: Henüz bir Azure aboneliğiniz yoksa, [ücretsiz Azure hesabı oluşturun][azure_free_account].
* **Batch hesabı**: Azure aboneliğiniz olduktan sonra, [Azure Batch hesabı oluşturun](batch-account-create-portal.md).
* **Storage hesabı**: Bkz. [Azure Storage hesapları hakkında](../storage/storage-create-storage-account.md) sayfası, [Storage hesabı oluşturma](../storage/storage-create-storage-account.md#create-a-storage-account) bölümü.

### <a name="code-sample"></a>Kod örneği
Python eğitmen [kod örneği][github_article_samples] GitHub’daki [azure-batch-samples][github_samples] deposunda bulunan çok sayıda Batch kod örneğinden biridir. Örneklerin tümünü, depo giriş sayfasındaki **Kopyala veya indir > ZIP’i İndir**’e veya [azure-batch-samples-master.zip][github_samples_zip] doğrudan indirme bağlantısına tıklayarak indirebilirsiniz. ZIP dosyasının içeriğini ayıkladıktan sonra Bu eğitmene yönelik iki betik `article_samples` dizininde yer alır:

`/azure-batch-samples/Python/Batch/article_samples/python_tutorial_client.py`<br/>
`/azure-batch-samples/Python/Batch/article_samples/python_tutorial_task.py`

### <a name="python-environment"></a>Python ortamı
*python_tutorial_client.py* örnek betiğini yerel iş istasyonunuzda çalıştırmak için, bir **2.7** veya **3.3+** sürümüyle uyumlu **Python yorumlayıcı** gerekir. Betik Linux ve Windows'da test edilmiştir.

### <a name="cryptography-dependencies"></a>şifreleme bağımlılıkları
[Şifreleme][crypto] kitaplığı için `azure-batch` ve `azure-storage` Python paketlerinin gerektirdiği bağımlılıkları yüklemeniz gerekir. Aşağıdaki işlemlerden platformunuz için uygun olan birini gerçekleştirin veya daha fazla bilgi için [şifreleme yüklemesi][crypto_install] ayrıntılarına bakın:

* Ubuntu

    `apt-get update && apt-get install -y build-essential libssl-dev libffi-dev libpython-dev python-dev`
* CentOS

    `yum update && yum install -y gcc openssl-dev libffi-devel python-devel`
* SLES/OpenSUSE

    `zypper ref && zypper -n in libopenssl-dev libffi48-devel python-devel`
* Windows

    `pip install cryptography`

> [!NOTE]
> Linux üzerinde 3.3+ Python yüklüyorsanız Python bağımlılıkları için python3 eşdeğerlerini kullanın. Örneğin, Ubuntu üzerinde: `apt-get update && apt-get install -y build-essential libssl-dev libffi-dev libpython3-dev python3-dev`
>
>

### <a name="azure-packages"></a>Azure paketleri
Ardından, **Azure Batch** ve **Azure Storage** Python paketlerini yükleyin. Her iki paketi de burada bulunan **pip** ve *requirements.txt* dosyalarını kullanarak yükleyebilirsiniz:

`/azure-batch-samples/Python/Batch/requirements.txt`

Batch ve Storage paketlerini yüklemek için aşağıdaki **pip** komutunu yayımlayın:

`pip install -r requirements.txt`

Ayrıca, [azure-batch][pypi_batch] ve [azure-storage][pypi_storage] Python paketlerini el ile de yükleyebilirsiniz:

`pip install azure-batch`<br/>
`pip install azure-storage`

> [!TIP]
> Ayrıcalığı olmayan bir hesap kullanıyorsanız, komutlarınıza `sudo` önekini eklemeniz gerekebilir. Örneğin, `sudo pip install -r requirements.txt`. Python paketlerini yükleme hakkında daha fazla bilgi için python.org sayfasındaki [Paketleri Yükleme][pypi_install] bölümüne bakın.
>
>

## <a name="batch-python-tutorial-code-sample"></a>Batch Python eğitmen kodu örneği
Batch Python eğitmen kodu örneği, iki Python betiği ve birkaç veri dosyasından oluşur.

* **python_tutorial_client.py**: İşlem düğümlerinde paralel iş yükünü yürütmek için Batch ve Storage hizmetleriyle etkileşime girer (sanal makineler). *python_tutorial_client.py* betikleri yerel iş istasyonunda çalışır.
* **python_tutorial_task.py**: Asıl işi gerçekleştirmek için Azure’deki işlem düğümlerinde çalışan betik. Örnekte, *python_tutorial_task.py* metni (girdi dosyası), Azure Storage’dan indirilen dosyada ayrıştırıyor. Ardından, girdi dosyasında ilk üç sözcüğün göründüğü listenin bulunduğu bir metin dosyası (çıktı dosyası) oluşturur. *python_tutorial_task.py*, çıktı dosyasını oluşturduktan sonra dosyayı Azure Storage’a yükler. İş istasyonunuzda çalışan istemci betiğini indirilmek üzere kullanıma açık tutar. *python_tutorial_task.py* betiği, Batch hizmetinde paralel olarak birden çok işlem düğümünde çalışır.
* **./data/taskdata\*.txt**: Bu üç metin dosyası işlem düğümlerinde çalışan görevler için girdi dosyaları sağlar.

Aşağıdaki diyagram, istemci ve görev betikleri tarafından gerçekleştirilen birincil işlemleri gösterir. Bu temel iş akışı, Batch’le oluşturulan çok sayıda işlem çözümünün tipik halidir. Batch hizmetinde erişilebilir olan özelliklerin hepsini göstermese de, neredeyse tüm Batch senaryoları bu iş akışının bölümlerini içerir.

![Batch örnek iş akışı][8]<br/>

[**1. Adım.**](#step-1-create-storage-containers) Azure Blob Storage’da **kapsayıcılar** oluşturun.<br/>
[**2. Adım.**](#step-2-upload-task-script-and-data-files) Kapsayıcılara görev betiğini ve girdi dosyalarını yükleyin.<br/>
[**3. Adım.**](#step-3-create-batch-pool) Batch **havuzu** oluşturun.<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;**3a.** **StartTask** havuzu görev betiğini (python_tutorial_task.py), göreve katıldıkları için düğümlere indirir.<br/>
[**4. Adım.**](#step-4-create-batch-job) Batch **işi** oluşturun.<br/>
[**5. Adım**](#step-5-add-tasks-to-job) İşe **görevler** ekleyin.<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;**5a.** Görevler, düğümlerde yürütmek üzere zamanlanır.<br/>
    &nbsp;&nbsp;&nbsp;&nbsp;**5b.** Her görev kendi girdi verilerini Azure Storage’dan indirip yürütmeye başlar.<br/>
[**6. Adım**](#step-6-monitor-tasks) Görevleri izleyin.<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;**6a.** Görevlerin tamamlanmasıyla, kendi çıktı verilerini Azure Storage’a yükler.<br/>
[**7. Adım**](#step-7-download-task-output) Storage’dan görev çıktısını indirin.

Yukarıda belirtildiği gibi, her Batch çözümü tam olarak bu adımları gerçekleştirmese ve çok daha fazlasını içerebilse de; bu örnek, Batch çözümünde bulunan ortak işlemleri göstermektedir.

## <a name="prepare-client-script"></a>İstemci komut dosyasını hazırlama
Örneği çalıştırmadan önce, Batch ve Storage hesabı kimlik bilgilerinizi *python_tutorial_client.py* öğesine ekleyin. Bu işi henüz yapmadıysanız, en sık kullandığınız düzenleyicide dosyayı açıp aşağıdaki satırları kendi kimlik bilgilerinizle güncelleştirin.

```python
# Update the Batch and Storage account credential strings below with the values
# unique to your accounts. These are used when constructing connection strings
# for the Batch and Storage client objects.

# Batch account credentials
batch_account_name = "";
batch_account_key  = "";
batch_account_url  = "";

# Storage account credentials
storage_account_name = "";
storage_account_key  = "";
```

Batch ve Depolama hesabı kimlik bilgilerinizi [Azure portalındaki][azure_portal] hizmetlere ilişkin hesap dikey pencerelerinde bulabilirsiniz:

![Portalda Batch kimlik bilgileri][9]
![Portalda Depolama kimlik bilgileri][10]<br/>

Aşağıdaki bölümlerde, Batch hizmetindeki iş yükünü işlemek için betikler tarafından kullanılan adımları analiz ediyoruz. Makalenin kalanında işinizi yaparken düzenleyicinizdeki betiklere düzenli olarak başvurmanızı öneriyoruz.

1. Adımla başlamak için aşağıdaki **python_tutorial_client.py** satırına gidin:

```python
if __name__ == '__main__':
```

## <a name="step-1-create-storage-containers"></a>1. Adım: Storage kapsayıcıları oluşturma
![Azure Depolama'da kapsayıcı oluşturma][1]
<br/>

Azure Storage ilet etkileşimde bulunmak için Batch’te yerleşik destek bulunur. Storage hesabınızdaki kapsayıcılar, Batch hesabınızda çalışan görevler için gerekli dosyaları sağlar. Kapsayıcılar ayrıca görevlerin oluşturduğu çıktı verilerini depolamak için bir yer sağlar. *python_tutorial_client.py* betiğinin yapacağı ilk şey [Azure Blob Storage](../storage/storage-introduction.md#blob-storage)’da üç kapsayıcı oluşturmaktır:

* **uygulama**: Bu kapsayıcı, *python_tutorial_task.py* görevlerinin çalıştırdığı Python betiğini depolayacaktır.
* **girdi**: Görevler, işlemek için veri dosyalarını *girdi* kapsayıcısından yükleyecektir.
* **çıktı**: Görevler girdi dosyası işlemeyi tamamladıklarında, sonuçları*çıktı* kapsayıcısına yüklerler.

Bir Depolama hesabıyla etkileşime geçmek ve kapsayıcı oluşturmak amacıyla, "blob istemcisi" adlı [BlockBlobService][py_blockblobservice] nesnesini oluşturmak için [azure-storage][pypi_storage] paketini kullanıyoruz. Daha sonra, blob istemcisini kullanarak Storage hesabında üç kapsayıcı oluşturuyoruz.

```python
 # Create the blob client, for use in obtaining references to
 # blob storage containers and uploading files to containers.
 blob_client = azureblob.BlockBlobService(
     account_name=_STORAGE_ACCOUNT_NAME,
     account_key=_STORAGE_ACCOUNT_KEY)

 # Use the blob client to create the containers in Azure Storage if they
 # don't yet exist.
 app_container_name = 'application'
 input_container_name = 'input'
 output_container_name = 'output'
 blob_client.create_container(app_container_name, fail_on_exist=False)
 blob_client.create_container(input_container_name, fail_on_exist=False)
 blob_client.create_container(output_container_name, fail_on_exist=False)
```

Kapsayıcılar oluşturulduktan sonra uygulama artık görevler tarafından kullanılacak dosyaları karşıya yükleyebilir.

> [!TIP]
> [Python’dan Azure Blob depolama kullanma](../storage/storage-python-how-to-use-blob-storage.md), Azure Storage kapsayıcıları ve blob'larla çalışma hakkında kapsamlı bilgi sağlar. Batch’le çalışmaya başladığınızda okuma listenizin en üstüne yakın olması gerekir.
>
>

## <a name="step-2-upload-task-script-and-data-files"></a>2. Adım: Görev betiğini ve veri dosyalarını karşıya yükleme
![Görev uygulamasını ve girdi (veriler) dosyalarını kapsayıcılara yükleme][2]
<br/>

Dosyayı karşıya yükleme işleminde, *python_tutorial_client.py* önce **uygulama** ve **girdi** dosya yolları koleksiyonunu yerel makinede oldukları gibi tanımlar. Sonra da, bir önceki adımda oluşturduğunuz kapsayıcılara bu dosyaları yükler.

```python
 # Paths to the task script. This script will be executed by the tasks that
 # run on the compute nodes.
 application_file_paths = [os.path.realpath('python_tutorial_task.py')]

 # The collection of data files that are to be processed by the tasks.
 input_file_paths = [os.path.realpath('./data/taskdata1.txt'),
                     os.path.realpath('./data/taskdata2.txt'),
                     os.path.realpath('./data/taskdata3.txt')]

 # Upload the application script to Azure Storage. This is the script that
 # will process the data files, and is executed by each of the tasks on the
 # compute nodes.
 application_files = [
     upload_file_to_container(blob_client, app_container_name, file_path)
     for file_path in application_file_paths]

 # Upload the data files. This is the data that will be processed by each of
 # the tasks executed on the compute nodes in the pool.
 input_files = [
     upload_file_to_container(blob_client, input_container_name, file_path)
     for file_path in input_file_paths]
```

Liste anlamayı kullanarak, `upload_file_to_container` işlevi koleksiyondaki her dosya için çağrılır ve iki [ResourceFile][py_resource_file] koleksiyonu doldurulur. `upload_file_to_container` İşlevi aşağıda görünür:

```python
def upload_file_to_container(block_blob_client, container_name, file_path):
    """
    Uploads a local file to an Azure Blob storage container.

    :param block_blob_client: A blob service client.
    :type block_blob_client: `azure.storage.blob.BlockBlobService`
    :param str container_name: The name of the Azure Blob storage container.
    :param str file_path: The local path to the file.
    :rtype: `azure.batch.models.ResourceFile`
    :return: A ResourceFile initialized with a SAS URL appropriate for Batch
    tasks.
    """
    blob_name = os.path.basename(file_path)

    print('Uploading file {} to container [{}]...'.format(file_path,
                                                          container_name))

    block_blob_client.create_blob_from_path(container_name,
                                            blob_name,
                                            file_path)

    sas_token = block_blob_client.generate_blob_shared_access_signature(
        container_name,
        blob_name,
        permission=azureblob.BlobPermissions.READ,
        expiry=datetime.datetime.utcnow() + datetime.timedelta(hours=2))

    sas_url = block_blob_client.make_blob_url(container_name,
                                              blob_name,
                                              sas_token=sas_token)

    return batchmodels.ResourceFile(file_path=blob_name,
                                    blob_source=sas_url)
```

### <a name="resourcefiles"></a>ResourceFiles
[ResourceFile][py_resource_file], görev çalıştırılmadan önce işlem düğümüne yüklenecek, Azure Depolama’da yer alan bir dosyaya bağlantısı olan URL’ye sahip Batch’teki görevleri sağlar. [ResourceFile][py_resource_file].**blob_source** özelliği, Azure Depolama’da olduğu gibi dosyanın tam URL'sini belirtir. URL’de, dosyaya güvenli erişim sağlayan bir paylaşılan erişim imzası da (SAS) bulunabilir. Batch’teki çoğu görev türünde *ResourceFiles* özelliği vardır; bu özellikte şunlar bulunur:

* [CloudTask][py_task]
* [StartTask][py_starttask]
* [JobPreparationTask][py_jobpreptask]
* [JobReleaseTask][py_jobreltask]

Bu örnek JobPreparationTask veya JobReleaseTask görev türlerini kullanmaz; ancak, bununla ilgili daha fazla bilgiyi [Azure Batch işlem düğümlerinde iş hazırlama ve tamamlama görevlerini çalıştırma](batch-job-prep-release.md) makalesinden edinebilirsiniz.

### <a name="shared-access-signature-sas"></a>Paylaşılan erişim imzası (SAS)
Paylaşılan erişim imzalar, Azure Storage'da kapsayıcılara ve blob’lara güvenli erişim sağlayan dizelerdir. *python_tutorial_client.py* betiği hem blob, hem de kapsayıcı paylaşılan erişim imzalarını kullanır ve Storage hizmetinden bu paylaşılan erişim imzalarının nasıl alındığını gösterir.

* **Blob paylaşılan erişim imzaları**: Havuza ait StartTask, Storage’dan görev betiğini ve girdi veri dosyalarını indirdiğinde blob paylaşılan erişim imzalarını kullanır (bkz. [3. Adım](#step-3-create-batch-pool); aşağıda). *python_tutorial_client.py* betiğindeki `upload_file_to_container` işlevinde her blob'un paylaşılan erişim imzasını alan kod bulunur. Bu işlemi, Depolama modülünde [BlockBlobService.make_blob_url][py_make_blob_url] çağırarak gerçekleştirir.
* **Kapsayıcı paylaşılan erişim imzası**: Hesaplama düğümünde her görev işini bitirdiğinde, kendi çıktı dosyasını Azure Storage’daki *çıktı* kapsayıcısına yükler. Bunu yapmak için, *python_tutorial_task.py*, kapsayıcıya yazma erişimi sağlayan kapsayıcı paylaşılan erişim imzasını kullanır. *python_tutorial_client.py* öğesindeki `get_container_sas_token` işlevi, daha sonra görevlere komut satırı bağımsız değişkeni olarak geçecek kapsayıcının paylaşılan erişim imzasını alır. 5. Adım, [İşe görev ekleme](#step-5-add-tasks-to-job), kapsayıcı SAS kullanımını tartışıyor.

> [!TIP]
> Storage hesabınızdaki verilere güvenli erişim sağlama hakkında daha fazla bilgi için paylaşılan erişim imzalarındaki iki parçalı seriyi kullanıma alın, [1. Parça: SAS modelini anlama](../storage/storage-dotnet-shared-access-signature-part-1.md) ve [2. Parça: Blob hizmetiyle SAS oluşturma ve kullanma](../storage/storage-dotnet-shared-access-signature-part-2.md).
>
>

## <a name="step-3-create-batch-pool"></a>3. Adım: Batch havuzu oluşturma
![Batch havuzu oluşturma][3]
<br/>

Batch **havuzu**, Batch’in işe ait görevleri yürüttüğü işlem düğümlerinin (sanal makineler) koleksiyonudur.

Görev betiğini ve veri dosyalarını Storage hesabına yükledikten sonra, *python_tutorial_client.py*, Batch Python modülünü kullanarak Batch hizmetiyle etkileşimi başlatır. Bunu yapmak için bir [BatchServiceClient][py_batchserviceclient] oluşturulur:

```python
 # Create a Batch service client. We'll now be interacting with the Batch
 # service in addition to Storage.
 credentials = batchauth.SharedKeyCredentials(_BATCH_ACCOUNT_NAME,
                                              _BATCH_ACCOUNT_KEY)

 batch_client = batch.BatchServiceClient(
     credentials,
     base_url=_BATCH_ACCOUNT_URL)
```

Ardından, `create_pool` çağrısıyla Batch hesabında işlem düğümü havuzu oluşturacak.

```python
def create_pool(batch_service_client, pool_id,
                resource_files, publisher, offer, sku):
    """
    Creates a pool of compute nodes with the specified OS settings.

    :param batch_service_client: A Batch service client.
    :type batch_service_client: `azure.batch.BatchServiceClient`
    :param str pool_id: An ID for the new pool.
    :param list resource_files: A collection of resource files for the pool's
    start task.
    :param str publisher: Marketplace image publisher
    :param str offer: Marketplace image offer
    :param str sku: Marketplace image sku
    """
    print('Creating pool [{}]...'.format(pool_id))

    # Create a new pool of Linux compute nodes using an Azure Virtual Machines
    # Marketplace image. For more information about creating pools of Linux
    # nodes, see:
    # https://azure.microsoft.com/documentation/articles/batch-linux-nodes/

    # Specify the commands for the pool's start task. The start task is run
    # on each node as it joins the pool, and when it's rebooted or re-imaged.
    # We use the start task to prep the node for running our task script.
    task_commands = [
        # Copy the python_tutorial_task.py script to the "shared" directory
        # that all tasks that run on the node have access to.
        'cp -r $AZ_BATCH_TASK_WORKING_DIR/* $AZ_BATCH_NODE_SHARED_DIR',
        # Install pip and the dependencies for cryptography
        'apt-get update',
        'apt-get -y install python-pip',
        'apt-get -y install build-essential libssl-dev libffi-dev python-dev',
        # Install the azure-storage module so that the task script can access
        # Azure Blob storage
        'pip install azure-storage']

    # Get the node agent SKU and image reference for the virtual machine
    # configuration.
    # For more information about the virtual machine configuration, see:
    # https://azure.microsoft.com/documentation/articles/batch-linux-nodes/
    sku_to_use, image_ref_to_use = \
        common.helpers.select_latest_verified_vm_image_with_node_agent_sku(
            batch_service_client, publisher, offer, sku)

    new_pool = batch.models.PoolAddParameter(
        id=pool_id,
        virtual_machine_configuration=batchmodels.VirtualMachineConfiguration(
            image_reference=image_ref_to_use,
            node_agent_sku_id=sku_to_use),
        vm_size=_POOL_VM_SIZE,
        target_dedicated=_POOL_NODE_COUNT,
        start_task=batch.models.StartTask(
            command_line=
            common.helpers.wrap_commands_in_shell('linux', task_commands),
            run_elevated=True,
            wait_for_success=True,
            resource_files=resource_files),
        )

    try:
        batch_service_client.pool.add(new_pool)
    except batchmodels.batch_error.BatchErrorException as err:
        print_batch_exception(err)
        raise
```

Havuz oluşturduğunuzda, havuz için bazı özellikler belirten bir [PoolAddParameter][py_pooladdparam] tanımlarsınız:

* Havuza ait **Kimlik** (*kimlik* - gerekli)<p/>Batch’deki çoğu varlık gibi, yeni havuzunuzda da, Batch hesabınızın içinde benzersiz bir kimlik olmalıdır. Kodunuz, kendi kimliğini kullanarak bu havuza başvurur ve Azure [portalında][azure_portal] havuz bu kimlikle tanımlanır.
* **İşlem düğümleri sayısı** (*target_dedicated* - gerekli)<p/>Bu özellik, havuzda kaç VM dağıtılması gerektiğini belirtir. Tüm Batch hesaplarının, bir Batch hesabında bir dizi **çekirdekle** (bu nedenle de işlem düğümleriyle) sınırlanan varsayılan bir **kotasının** olduğunu unutmamak önemlidir. [Kota artırma](batch-quota-limit.md#increase-a-quota) (Batch hesabınızdaki en yüksek çekirdek sayısı gibi) hakkında varsayılan kotalar ve yönergeleri [Azure Batch hizmeti için kotalar ve sınırlar](batch-quota-limit.md)’da bulabilirsiniz. Kendinizi "Neden havuzum X düğümden fazlasına ulaşamıyor?" sorusunu sorarken bulursanız nedeni çekirdek kotası olabilir.
* Düğümler için **işletim sistemi** (*virtual_machine_configuration* **veya** *cloud_service_configuration* - gerekli)<p/>*python_tutorial_client.py* öğesinde [VirtualMachineConfiguration][py_vm_config] kullanarak Linux için havuz oluşturuyoruz. `common.helpers` içindeki `select_latest_verified_vm_image_with_node_agent_sku` işlevi [Azure Sanal Makineler Marketi][vm_marketplace] görüntüleriyle çalışmayı kolaylaştırır. Market görüntülerini kullanma hakkında daha fazla bilgi için bkz. [Azure Batch havuzlarında Linux işlem düğümlerini hazırlama](batch-linux-nodes.md).
* **İşlem düğümlerinin boyutu** (*vm_size* - gerekli)<p/>[VirtualMachineConfiguration][py_vm_config] için Linux düğümleri belirlediğimizden, bir VM boyutunu (bu örnekte `STANDARD_A1`) [Azure’de sanal makine boyutları](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)’nda belirttik. Bir kez daha, daha fazla bilgi için bkz. [Azure Batch havuzlarında Linux işlem düğümlerini hazırlama](batch-linux-nodes.md).
* **Görev Başlat** (*start_task* - gerekli değil)<p/>Yukarıdaki fiziksel düğüm özellikleriyle birlikte, havuz için ayrıca bir [StartTask][py_starttask] belirtebilirsiniz (gerekli değildir). StartTask, her düğümü havuza katıldığında ve her yeniden başlatıldığında yürütecektir. StartTask, özellikle görevlerinizin çalıştırdığı uygulamaları yükleme gibi işlerin yürütülmesi için, işlem düğümlerinin hazırlanmasında yararlıdır.<p/>Bu örnek uygulamasında StartTask, Storage’dan indirilen dosyaları (StartTask’ın **resource_files** özelliği kullanılarak belirtilir), StartTask *çalışma dizininden*, erişilebilir düğümdeki tüm görevlerin çalıştığı *paylaşılan* dizine kopyalar. Aslında, `python_tutorial_task.py` uygulamasını, düğüm havuza katılmış olduğundan her düğüme kopyalar; bu nedenle düğümde çalışan görevler buna erişebilir.

`wrap_commands_in_shell` yardımcı işlevi çağrısını fark edebilirsiniz. Bu işlev ayrı komutların koleksiyonunu alır ve görevin komut satırı özelliğine uygun tek bir komut satırı oluşturur.

Yukarıdaki kod parçacığında dikkat çeken bir şey de, StartTask’ın **command_line** özelliğinde iki ortam değişkenin kullanılmasıdır: `AZ_BATCH_TASK_WORKING_DIR` ve `AZ_BATCH_NODE_SHARED_DIR`. Batch havuzundaki her işlem düğümü, Batch’e özel bazı ortam değişkenleriyle yapılandırılmıştır. Görev tarafından yürütülen işlemlerin bu ortam değişkenlerine erişimi vardır.

> [!TIP]
> Görev çalışma dizinleri hakkında bilgilerin yanı sıra, Batch havuzundaki işlem düğümlerinde bulunan ortam değişkenleri hakkında da daha fazla bilgi bulmak için bkz. [Azure Batch özelliklerine genel bakış](batch-api-basics.md)’ın **Görevler için ortam ayarları** ve **Dosyalar ve dizinler**.
>
>

## <a name="step-4-create-batch-job"></a>4. Adım: Batch işi oluşturma
![Batch işi oluşturma][4]<br/>

Batch **işi**, görevler koleksiyonudur ve işlem düğümlerinin bir havuzuyla ilişkilidir. İşteki görevler ilişkili havuzunun işlem düğümlerini yürütür.

İşi yalnızca ilgili iş yüklerinde görevlerin düzenlenmesi ve izlenmesi için değil, aynı zamanda işin (ve buna bağlı olarak görevlerin) en uzun çalışma süresinin yanı sıra Batch hesabındaki diğer işlerle bağlantılı olarak iş önceliği gibi bazı kısıtlamalar getirmek için de kullanabilirsiniz. Ancak bu örnekte, iş yalnızca 3. adımda oluşturulan havuzla ilişkilendirilmektedir. Yapılandırılmış başka ek özellik yoktur.

Tüm Batch işleri belirli bir havuzla ilişkilidir. Bu ilişkilendirme, iş görevlerinin hangi düğümleri yürüttüğünü belirtir. Havuzu, aşağıdaki kod parçacığında gösterildiği gibi [PoolInformation][py_poolinfo] özelliğini kullanarak belirtirsiniz.

```python
def create_job(batch_service_client, job_id, pool_id):
    """
    Creates a job with the specified ID, associated with the specified pool.

    :param batch_service_client: A Batch service client.
    :type batch_service_client: `azure.batch.BatchServiceClient`
    :param str job_id: The ID for the job.
    :param str pool_id: The ID for the pool.
    """
    print('Creating job [{}]...'.format(job_id))

    job = batch.models.JobAddParameter(
        job_id,
        batch.models.PoolInformation(pool_id=pool_id))

    try:
        batch_service_client.job.add(job)
    except batchmodels.batch_error.BatchErrorException as err:
        print_batch_exception(err)
        raise
```

İş oluşturulduğuna göre, artık çalışmak için görevler eklenir.

## <a name="step-5-add-tasks-to-job"></a>5. Adım: İşe görev ekleme
![İşe görev ekleme][5]<br/>
*(1) Görevler işe eklenir, (2) görevler düğümlerde çalışmak üzere zamanlanır ve (3) görevler işlemek üzere veri dosyalarını indirir*

Batch **görevleri**, işlem düğümlerinde yürütülen tek tek iş birimleridir. Görevde bir komut satırı vardır; bu komut satırında belirttiğiniz betikleri veya yürütülebilir dosyaları çalıştırır.

Aslına bakılırsa, çalışmayı gerçekleştirmek için görevlerin işe eklenmesi gerekir. Her [CloudTask][py_task], bir komut satırı özelliği ve komut satırı otomatik olarak yürütülmeden önce görevin düğüme yüklediği [ResourceFiles][py_resource_file] (havuzdaki StartTask gibi) kullanılarak yapılandırılır. Örnekte, her görev yalnızca bir dosya işliyor. Bu nedenle, kendi ResourceFiles koleksiyonunda tek bir öğe bulunmaktadır.

```python
def add_tasks(batch_service_client, job_id, input_files,
              output_container_name, output_container_sas_token):
    """
    Adds a task for each input file in the collection to the specified job.

    :param batch_service_client: A Batch service client.
    :type batch_service_client: `azure.batch.BatchServiceClient`
    :param str job_id: The ID of the job to which to add the tasks.
    :param list input_files: A collection of input files. One task will be
     created for each input file.
    :param output_container_name: The ID of an Azure Blob storage container to
    which the tasks will upload their results.
    :param output_container_sas_token: A SAS token granting write access to
    the specified Azure Blob storage container.
    """

    print('Adding {} tasks to job [{}]...'.format(len(input_files), job_id))

    tasks = list()

    for input_file in input_files:

        command = ['python $AZ_BATCH_NODE_SHARED_DIR/python_tutorial_task.py '
                   '--filepath {} --numwords {} --storageaccount {} '
                   '--storagecontainer {} --sastoken "{}"'.format(
                    input_file.file_path,
                    '3',
                    _STORAGE_ACCOUNT_NAME,
                    output_container_name,
                    output_container_sas_token)]

        tasks.append(batch.models.TaskAddParameter(
                'topNtask{}'.format(input_files.index(input_file)),
                wrap_commands_in_shell('linux', command),
                resource_files=[input_file]
                )
        )

    batch_service_client.task.add_collection(job_id, tasks)
```

> [!IMPORTANT]
> `$AZ_BATCH_NODE_SHARED_DIR` gibi ortam değişkenlerine eriştiklerinde veya düğüme ait `PATH` öğesinde bulunmayan bir uygulama yürüttüklerinde görev komut satırları `/bin/sh -c MyTaskApplication $MY_ENV_VAR` ile olduğu gibi kabuğu açıkça çağırmalıdır. Görevleriniz düğümün `PATH` seçeneğinde bir uygulama yürütüyorsa ve herhangi bir ortam değişkenine başvurmuyorsa bu gereksinim gerekli değildir.
>
>

Yukarıdaki kod parçacığında, `for` döngüsü içinde görevle ilgili komut satırının, beş komut satırı bağımsız değişkeniyle *python_tutorial_task.py* dosyasına geçirilecek şekilde oluşturulduğunu görürsünüz:

1. **filepath**: Düğümde yer aldığından dosyanın yerel yolu budur. `upload_file_to_container` ResourceFile nesnesi yukarıda 2. adımda oluşturulduğunda dosya adı bu özellik için kullanılır (ResourceFile oluşturucuda `file_path` parametresi). Dosyanın düğümde *python_tutorial_task.py* ile aynı dizinde bulunabileceğini belirtir.
2. **numwords**: ilk *N* sayıda sözcüğün çıktı dosyasına yazılması gerekir.
3. **storageaccount**: Görev çıktısının yüklenmiş olması gereken kapsayıcıya sahip Storage hesabının adı.
4. **storagecontainer**: Çıktı dosyalarının yüklenmiş olması gereken Storage kapsayıcısının adı.
5. **sastoken**: Azure Storage’da **çıktı** kapsayıcısına yazma erişimi sağlayan paylaşılan erişim imzası (SAS). *python_tutorial_task.py* betiği, kendi BlockBlobService başvurusunu oluşturduğunda bu paylaşılan erişim imzasını kullanır. Depolama hesabı için erişim anahtarı gerekmeden kapsayıcıya yazma erişimi sağlar.

```python
# NOTE: Taken from python_tutorial_task.py

# Create the blob client using the container's SAS token.
# This allows us to create a client that provides write
# access only to the container.
blob_client = azureblob.BlockBlobService(account_name=args.storageaccount,
                                         sas_token=args.sastoken)
```

## <a name="step-6-monitor-tasks"></a>6. Adım: Görevleri izleme
![Görevleri izleme][6]<br/>
*Betik (1) tamamlama durumu için görevleri izler, (2) görevler de sonuç verilerini Azure Depolama’ya yükler*

Görevler bir projeye eklendiğinde, otomatik olarak kuyruğa alınır ve işle ilişkili havuzun içindeki işlem düğümlerinde zamanlanırlar. Belirttiğiniz ayarlar temelinde, Batch tüm kuyruğa alınan, zamanlanan, yeniden denenen ve sizle ilgili diğer görev yönetimi görevlerini işler.

Görevin yürütülüşünün izlenmesi için birçok yaklaşım vardır. *python_tutorial_client.py* ’deki `wait_for_tasks_to_complete` işlevi bazı durumlarda görevleri izlemenin basit bir örneğini sağlar; burada [tamamlandı][py_taskstate] durumu.

```python
def wait_for_tasks_to_complete(batch_service_client, job_id, timeout):
    """
    Returns when all tasks in the specified job reach the Completed state.

    :param batch_service_client: A Batch service client.
    :type batch_service_client: `azure.batch.BatchServiceClient`
    :param str job_id: The id of the job whose tasks should be to monitored.
    :param timedelta timeout: The duration to wait for task completion. If all
    tasks in the specified job do not reach Completed state within this time
    period, an exception will be raised.
    """
    timeout_expiration = datetime.datetime.now() + timeout

    print("Monitoring all tasks for 'Completed' state, timeout in {}..."
          .format(timeout), end='')

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

    print()
    raise RuntimeError("ERROR: Tasks did not reach 'Completed' state within "
                       "timeout period of " + str(timeout))
```

## <a name="step-7-download-task-output"></a>7. Adım: Görev çıktısı indirme
![Storage'dan görev çıktısını indirme][7]<br/>

Artık iş tamamlandı, görevlere ait çıktı Azure Storage’dan indirilebilir. *python_tutorial_client.py* içinde `download_blobs_from_container` çağrısıyla yapılır:

```python
def download_blobs_from_container(block_blob_client,
                                  container_name, directory_path):
    """
    Downloads all blobs from the specified Azure Blob storage container.

    :param block_blob_client: A blob service client.
    :type block_blob_client: `azure.storage.blob.BlockBlobService`
    :param container_name: The Azure Blob storage container from which to
     download files.
    :param directory_path: The local directory to which to download the files.
    """
    print('Downloading all files from container [{}]...'.format(
        container_name))

    container_blobs = block_blob_client.list_blobs(container_name)

    for blob in container_blobs.items:
        destination_file_path = os.path.join(directory_path, blob.name)

        block_blob_client.get_blob_to_path(container_name,
                                           blob.name,
                                           destination_file_path)

        print('  Downloaded blob [{}] from container [{}] to {}'.format(
            blob.name,
            container_name,
            destination_file_path))

    print('  Download complete!')
```

> [!NOTE]
> *python_tutorial_client.py* ’deki `download_blobs_from_container` çağrısı, dosyaların giriş dizininize indirilmesi gerektiğini belirtir. Bu çıktı konumunu değiştirmekten çekinmeyin.
>
>

## <a name="step-8-delete-containers"></a>8. Adım: Sil kapsayıcıları
Azure Storage’da yer alan veriler için ücretlendirildiğinizden, Batch işleriniz için artık gerekmeyen blobları kaldırmak iyi bir fikirdir. *python_tutorial_client.py* ’de üç [BlockBlobService.delete_container][py_delete_container] çağrısıyla yapılır:

```python
# Clean up storage resources
print('Deleting containers...')
blob_client.delete_container(app_container_name)
blob_client.delete_container(input_container_name)
blob_client.delete_container(output_container_name)
```

## <a name="step-9-delete-the-job-and-the-pool"></a>9. Adım: İşi ve havuzu silme
Son adımda, *python_tutorial_client.py* betiği tarafından oluşturulan işi ve havuzu silmeniz istenir. İşlerin ve görevlerin kendileri için sizden ücret istenmese de, işlem düğümleri için *ücret* istenecektir. Bu nedenle, düğümleri yalnızca gerektiğinde ayırmanız önerilir. Kullanılmayan havuzların silinmesi bakım işleminizin bir parçası olabilir.

BatchServiceClient'ın [JobOperations][py_job] ve [PoolOperations][py_pool] öğelerinin her ikisine de, silmeyi onaylamanız durumunda karşılık gelecek silme yöntemleri vardır:

```python
# Clean up Batch resources (if the user so chooses).
if query_yes_no('Delete job?') == 'yes':
    batch_client.job.delete(_JOB_ID)

if query_yes_no('Delete pool?') == 'yes':
    batch_client.pool.delete(_POOL_ID)
```

> [!IMPORTANT]
> İşlem kaynaklarının ücretli olduğunu unutmayın; kullanılmayan havuzların silinmesi masrafları azaltacaktır. Bunun yanı sıra, bir havuzun silinmesinin, bu havuz içindeki tüm işlem düğümlerini de sileceğini unutmayın; bu nedenle, düğümlerdeki veriler de havuz silindikten sonra kurtarılamayacaktır.
>
>

## <a name="run-the-sample-script"></a>Örnek betiği çalıştırma
Eğitmen [kod örneği][github_article_samples] içindeki *python_tutorial_client.py* betiğini çalıştırdığınızda, konsol çıktısı aşağıdakine benzer. Havuzun işlem düğümleri oluşturulurken, başlatılırken ve havuzun görev başlatma komutları yürütülürken `Monitoring all tasks for 'Completed' state, timeout in 0:20:00...` konumunda duraklama olur. Havuzunuzu, işlem düğümlerinizi, işinizi ve görevlerinizi yürütme sırasında ve sonrasında izlemek için [Azure portalını][azure_portal] kullanın. Uygulamanın oluşturduğu Depolama kaynaklarını (kapsayıcılar ve bloblar) görüntülemek için [Azure portalı][azure_portal] veya [Microsoft Azure Storage Gezgini][storage_explorer] birini kullanın.

> [!TIP]
> `azure-batch-samples/Python/Batch/article_samples` dizininden *python_tutorial_client.py* betiğini çalıştırın. Bu dosya `common.helpers` modül içeri aktarımı için göreli bir yol kullanır; bu nedenle betiği bu dizinden çalıştırmazsanız `ImportError: No module named 'common'` seçeneğini görebilirsiniz.
>
>

Varsayılan yapılandırmasında örnek çalıştırıldığında tipik yürütme süresi **yaklaşık 5-7 dakika arasıdır**.

```
Sample start: 2016-05-20 22:47:10

Uploading file /home/user/py_tutorial/python_tutorial_task.py to container [application]...
Uploading file /home/user/py_tutorial/data/taskdata1.txt to container [input]...
Uploading file /home/user/py_tutorial/data/taskdata2.txt to container [input]...
Uploading file /home/user/py_tutorial/data/taskdata3.txt to container [input]...
Creating pool [PythonTutorialPool]...
Creating job [PythonTutorialJob]...
Adding 3 tasks to job [PythonTutorialJob]...
Monitoring all tasks for 'Completed' state, timeout in 0:20:00..........................................................................
  Success! All tasks reached the 'Completed' state within the specified timeout period.
Downloading all files from container [output]...
  Downloaded blob [taskdata1_OUTPUT.txt] from container [output] to /home/user/taskdata1_OUTPUT.txt
  Downloaded blob [taskdata2_OUTPUT.txt] from container [output] to /home/user/taskdata2_OUTPUT.txt
  Downloaded blob [taskdata3_OUTPUT.txt] from container [output] to /home/user/taskdata3_OUTPUT.txt
  Download complete!
Deleting containers...

Sample end: 2016-05-20 22:53:12
Elapsed time: 0:06:02

Delete job? [Y/n]
Delete pool? [Y/n]

Press ENTER to exit...
```

## <a name="next-steps"></a>Sonraki adımlar
Farklı işlem senaryolarıyla deneyim kazanmak için *python_tutorial_client.py* ve *python_tutorial_task.py* öğelerinde değişiklik yaparken kendinizi rahat hissedin. Örneğin, uzun soluklu görevlerin benzetimini gerçekleştirmek ve bunları portalda izlemek için *python_tutorial_task.py* öğesine bir yürütme gecikmesi eklemeye çalışın. Daha fazla görev eklemeye veya işlem düğüm sayısını ayarlamaya çalışın. Yürütme süresini hızlandırmak için var olan havuzun kullanımını denetlemek ve izin vermek için mantık ekleyin.

Batch çözümünün temel iş akışı hakkında artık bilginiz olduğuna göre, Batch hizmetinin ek özelliklerinin derinliklerine dalma zamanı gelmiştir.

* Hizmetle yeni tanışıyorsanız önerdiğimiz [Azure Batch özelliklerine genel bakış](batch-api-basics.md) makalesini gözden geçirin.
* [Batch öğrenme yolu][batch_learning_path]’ndaki **Ayrıntılı geliştirme** altında diğer Batch geliştirmesi makalelerine başlayın.
* [TopNWords][github_topnwords] örneğinde Batch’le "ilk N sözcük" iş yükünü işlemenin farklı uygulamalarını kullanıma alın.

[azure_batch]: https://azure.microsoft.com/services/batch/
[azure_free_account]: https://azure.microsoft.com/free/
[azure_portal]: https://portal.azure.com
[batch_learning_path]: https://azure.microsoft.com/documentation/learning-paths/batch/
[blog_linux]: http://blogs.technet.com/b/windowshpc/archive/2016/03/30/introducing-linux-support-on-azure-batch.aspx
[crypto]: https://cryptography.io/en/latest/
[crypto_install]: https://cryptography.io/en/latest/installation/
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_samples_zip]: https://github.com/Azure/azure-batch-samples/archive/master.zip
[github_topnwords]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/TopNWords
[github_article_samples]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch/article_samples

[nuget_packagemgr]: https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c
[nuget_restore]: https://docs.nuget.org/consume/package-restore/msbuild-integrated#enabling-package-restore-during-build

[py_account_ops]: http://azure-sdk-for-python.readthedocs.org/en/latest/ref/azure.batch.operations.html#azure.batch.operations.AccountOperations
[py_azure_sdk]: https://pypi.python.org/pypi/azure
[py_batch_docs]: http://azure-sdk-for-python.readthedocs.org/en/latest/ref/azure.batch.html
[py_batch_package]: https://pypi.python.org/pypi/azure-batch
[py_batchserviceclient]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.html#azure.batch.BatchServiceClient
[py_blockblobservice]: http://azure.github.io/azure-storage-python/ref/azure.storage.blob.blockblobservice.html#azure.storage.blob.blockblobservice.BlockBlobService
[py_cloudtask]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.CloudTask
[py_computenodeuser]: http://azure-sdk-for-python.readthedocs.org/en/latest/ref/azure.batch.models.html#azure.batch.models.ComputeNodeUser
[py_cs_config]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.CloudServiceConfiguration
[py_delete_container]: http://azure.github.io/azure-storage-python/ref/azure.storage.blob.baseblobservice.html#azure.storage.blob.baseblobservice.BaseBlobService.delete_container
[py_gen_blob_sas]: http://azure.github.io/azure-storage-python/ref/azure.storage.blob.baseblobservice.html#azure.storage.blob.baseblobservice.BaseBlobService.generate_blob_shared_access_signature
[py_gen_container_sas]: http://azure.github.io/azure-storage-python/ref/azure.storage.blob.baseblobservice.html#azure.storage.blob.baseblobservice.BaseBlobService.generate_container_shared_access_signature
[py_image_ref]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.ImageReference
[py_imagereference]: http://azure-sdk-for-python.readthedocs.org/en/latest/ref/azure.batch.models.html#azure.batch.models.ImageReference
[py_job]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.operations.html#azure.batch.operations.JobOperations
[py_jobpreptask]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.JobPreparationTask
[py_jobreltask]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.JobReleaseTask
[py_list_skus]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.operations.html#azure.batch.operations.AccountOperations.list_node_agent_skus
[py_make_blob_url]: http://azure.github.io/azure-storage-python/ref/azure.storage.blob.baseblobservice.html#azure.storage.blob.baseblobservice.BaseBlobService.make_blob_url
[py_pool]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.operations.html#azure.batch.operations.PoolOperations
[py_pooladdparam]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.PoolAddParameter
[py_poolinfo]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.PoolInformation
[py_resource_file]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.ResourceFile
[py_samples_github]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch/
[py_starttask]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.StartTask
[py_starttask]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.StartTask
[py_task]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.CloudTask
[py_taskstate]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.TaskState
[py_vm_config]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.VirtualMachineConfiguration
[pypi_batch]: https://pypi.python.org/pypi/azure-batch
[pypi_storage]: https://pypi.python.org/pypi/azure-storage
[pypi_install]: https://packaging.python.org/installing/
[storage_explorer]: http://storageexplorer.com/
[visual_studio]: https://www.visualstudio.com/products/vs-2015-product-editions
[vm_marketplace]: https://azure.microsoft.com/marketplace/virtual-machines/

[1]: ./media/batch-python-tutorial/batch_workflow_01_sm.png "Azure Depolama’da kapsayıcı oluşturma"
[2]: ./media/batch-python-tutorial/batch_workflow_02_sm.png "Görev uygulamasını ve girdi (veriler) dosyalarını kapsayıcılara yükleme"
[3]: ./media/batch-python-tutorial/batch_workflow_03_sm.png "Batch havuzu oluşturma"
[4]: ./media/batch-python-tutorial/batch_workflow_04_sm.png "Batch işi oluşturma"
[5]: ./media/batch-python-tutorial/batch_workflow_05_sm.png "İşe görev ekleme"
[6]: ./media/batch-python-tutorial/batch_workflow_06_sm.png "Görevleri izleme"
[7]: ./media/batch-python-tutorial/batch_workflow_07_sm.png "Depolama’dan görev çıkışını indirme"
[8]: ./media/batch-python-tutorial/batch_workflow_sm.png "Batch çözümü iş akışı (tam diyagram)"
[9]: ./media/batch-python-tutorial/credentials_batch_sm.png "Portalda Batch kimlik bilgileri"
[10]: ./media/batch-python-tutorial/credentials_storage_sm.png "Portalda Depolama kimlik bilgileri"
[11]: ./media/batch-python-tutorial/batch_workflow_minimal_sm.png "Batch çözümü iş akışı (minimal diyagram)"

