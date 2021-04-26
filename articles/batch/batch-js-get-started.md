---
title: JavaScript için Azure Batch istemci kitaplığını kullanma
description: Azure Batch temel kavramlarını öğrenin ve JavaScript kullanarak basit bir çözüm oluşturun.
ms.topic: how-to
ms.date: 01/01/2021
ms.openlocfilehash: 1f53c793e4c676df319d46f30595ac330846dfa5
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106231610"
---
# <a name="get-started-with-batch-sdk-for-javascript"></a>JavaScript için Batch SDK 'sını kullanmaya başlama

JavaScript 'te [Azure Batch JAVASCRIPT SDK](https://github.com/Azure/azure-sdk-for-js/)kullanarak Batch istemcisi oluşturma temellerini öğrenin. Batch uygulaması için bir senaryoyu anlamak ve sonra JavaScript kullanarak ayarlamak için bir adım adım yaklaşım ele alır.

## <a name="prerequisites"></a>Önkoşullar

Bu makalede, JavaScript ve Linux hakkında bilgi sahibi olduğunuz varsayılmaktadır. Ayrıca, Batch ve Depolama hizmetleri oluşturmak için erişim haklarına sahip bir Azure hesabınız olduğu varsayılmıştır.

Bu makaledeki adımlara geçmeden önce [Azure Batch Teknik Genel Bakış](batch-technical-overview.md) makalesini okumanızı öneririz.

## <a name="understand-the-scenario"></a>Senaryoyu anlama

Burada, Python 'da yazılmış ve tüm CSV dosyalarını bir Azure Blob depolama kapsayıcısından indiren ve bunları JSON 'a dönüştüren basit bir komut dosyası sunuyoruz. Birden çok depolama hesabı kapsayıcısını paralel olarak işlemek için betiği bir Azure Batch işi olarak dağıtabiliriz.

## <a name="azure-batch-architecture"></a>Azure Batch mimarisi

Aşağıdaki diyagramda, Azure Batch ve istemcisi kullanarak Python betiğini ölçeklendirmemiz gösterilmektedir.

![Senaryo mimarisini gösteren diyagram.](./media/batch-js-get-started/batch-scenario.png)

JavaScript örneği, bir hazırlık göreviyle (daha sonra ayrıntılı olarak açıklanan) bir toplu iş dağıtır ve depolama hesabındaki kapsayıcıların sayısına bağlı olarak bir görev kümesi. Dosyaları GitHub deposundan indirebilirsiniz.

- [Örnek Kod](https://github.com/Azure-Samples/azure-batch-samples/blob/master/JavaScript/Node.js/sample.js)
- [Hazırlık görevi kabuk betikleri](https://github.com/Azure-Samples/azure-batch-samples/blob/master/JavaScript/Node.js/startup_prereq.sh)
- [JSON işlemcisine giden Python csv’si](https://github.com/Azure-Samples/azure-batch-samples/blob/master/JavaScript/Node.js/processcsv.py)

> [!TIP]
> Belirtilen bağlantıdaki JavaScript örneği bir Azure işlev uygulaması olarak dağıtılacak belirli kodu içermiyor. Bu kodu oluşturmak için aşağıdaki bağlantılardan yararlanabilirsiniz.
> - [İşlev uygulaması oluşturma](../azure-functions/functions-get-started.md)
> - [Zamanlayıcı tetikleyicisi işlevi oluşturma](../azure-functions/functions-bindings-timer.md)

## <a name="build-the-application"></a>Uygulama oluşturma

Şimdi, JavaScript istemcisini oluşturma konusunda adım adım işlem adımını takip etmemize izin verin:

### <a name="step-1-install-azure-batch-sdk"></a>1. Adım: Azure Batch SDK’sını yükleme

JavaScript için Azure Batch SDK 'Yı NPM install komutunu kullanarak yükleyebilirsiniz.

`npm install azure-batch`

Bu komut, Azure-Batch JavaScript SDK 'sının en son sürümünü yüklenir.

>[!Tip]
> Bir Azure İşlev uygulamasında npm yükleme komutlarını çalıştırmak için Azure işlevinin Ayarlar sekmesindeki "Kudu Console" bölümüne gidebilirsiniz. Bu durumda, JavaScript için Azure Batch SDK 'Yı yüklemek için.

### <a name="step-2-create-an-azure-batch-account"></a>2. Adım: Azure Batch hesabı oluşturma

Bunu [Azure Portal](batch-account-create-portal.md) veya komut satırından ([PowerShell](batch-powershell-cmdlets-get-started.md)  / [Azure CLI](/cli/azure)) oluşturabilirsiniz.

Azure CLI aracılığıyla oluşturmak için kullanılacak komutlar aşağıdadır.

Bir Kaynak Grubu oluşturun. İçinde Batch hesabını oluşturmak istediğiniz bir Kaynak Grubunuz zaten varsa bu adımı atlayın.

`az group create -n "<resource-group-name>" -l "<location>"`

Ardından bir Azure Batch hesabı oluşturun.

`az batch account create -l "<location>"  -g "<resource-group-name>" -n "<batch-account-name>"`

Her Batch hesabının ilgili erişim anahtarları vardır. Bu anahtarlar Azure Batch hesabında yeni kaynaklar oluşturmak için gerekir. Üretim ortamı için iyi bir uygulama bu anahtarları depolamak için Azure Key Vault kullanmaktır. Daha sonra uygulama için bir Hizmet sorumlusu oluşturabilirsiniz. Uygulama, bu hizmet sorumlusunu kullanarak, Key Vault’taki anahtarlara erişmek için bir OAuth belirteci oluşturabilir.

`az batch account keys list -g "<resource-group-name>" -n "<batch-account-name>"`

Sonraki adımlarda kullanılacak anahtarı kopyalayın ve saklayın.

### <a name="step-3-create-an-azure-batch-service-client"></a>3. Adım: Azure Batch hizmet istemcisi oluşturma

Aşağıdaki kod parçacığı ilk olarak Azure-Batch JavaScript modülünü içeri aktarır ve sonra bir Batch hizmeti istemcisi oluşturur. İlk olarak, önceki adımda kopyaladığınız Batch hesabınızın anahtarıyla bir SharedKeyCredentials nesnesi oluşturmanız gerekir.

```javascript
// Initializing Azure Batch variables

var batch = require('azure-batch');

var accountName = '<azure-batch-account-name>';

var accountKey = '<account-key-downloaded>';

var accountUrl = '<account-url>'

// Create Batch credentials object using account name and account key

var credentials = new batch.SharedKeyCredentials(accountName,accountKey);

// Create Batch service client

var batch_client = new batch.ServiceClient(credentials,accountUrl);

```

Azure Batch URI’sı, Azure portal'ın genel bakış sekmesinde bulunur. Biçimi şöyledir:

`https://accountname.location.batch.azure.com`

Ekran görüntüsüne bakın:

![Azure batch uri](./media/batch-js-get-started/batch-uri.png)

### <a name="step-4-create-an-azure-batch-pool"></a>4. Adım: Azure Batch havuzu oluşturma

Bir Azure Batch havuzu birden çok VM’den (Batch Düğümleri olarak da bilinir) oluşur. Azure Batch hizmeti, görevleri bu düğümlere dağıtır ve yönetir. Havuzunuz için aşağıdaki yapılandırma parametrelerini tanımlayabilirsiniz.

- Sanal Makine Türü görüntüsü
- Sanal Makine düğümlerinin boyutu
- Sanal Makine düğümlerinin sayısı

> [!TIP]
> Sanal Makine düğümlerinin sayısı ve boyutu büyük ölçüde görevlere ve paralel olarak çalıştırmak istediğiniz görev sayısına bağlıdır. İdeal sayısını ve boyutunu belirlemek için test etmenizi öneririz.

Aşağıdaki kod parçacığı, yapılandırma parametresi nesnelerini oluşturur.

```javascript
// Creating Image reference configuration for Ubuntu Linux VM
var imgRef = {publisher:"Canonical",offer:"UbuntuServer",sku:"14.04.2-LTS",version:"latest"}

// Creating the VM configuration object with the SKUID
var vmconfig = {imageReference:imgRef,nodeAgentSKUId:"batch.node.ubuntu 14.04"}

// Setting the VM size to Standard F4
var vmSize = "STANDARD_F4"

//Setting number of VMs in the pool to 4
var numVMs = 4
```

> [!TIP]
> Azure Batch’te kullanılabilen Linux VM görüntüleri ve bu görüntülerin SKU kimliklerinin bir listesi için bkz. [Sanal makine görüntüleri listesi](batch-linux-nodes.md#list-of-virtual-machine-images).

Havuzun yapılandırması tanımlandığında, Azure Batch havuzunu oluşturabilirsiniz. Batch havuz komutu Azure sanal makine düğümleri oluşturur ve bu düğümleri, yürütülecek görevleri almaya hazır olacak şekilde hazırlar. Her havuz, sonraki adımlarda başvuru için benzersiz bir kimliğe sahip olmalıdır.

Aşağıdaki kod parçacığı bir Azure Batch havuzu oluşturur.

```javascript
// Create a unique Azure Batch pool ID
var poolid = "pool" + customerDetails.customerid;
var poolConfig = {id:poolid, displayName:poolid,vmSize:vmSize,virtualMachineConfiguration:vmconfig,targetDedicatedComputeNodes:numVms,enableAutoScale:false };
// Creating the Pool for the specific customer
var pool = batch_client.pool.add(poolConfig,function(error,result){
    if(error!=null){console.log(error.response)};
});
```

Oluşturulan havuzun durumunu kontrol edip bu havuza bir iş göndermeden önce durumunun "etkin" olduğundan emin olun.

```javascript
var cloudPool = batch_client.pool.get(poolid,function(error,result,request,response){
        if(error == null)
        {

            if(result.state == "active")
            {
                console.log("Pool is active");
            }
        }
        else
        {
            if(error.statusCode==404)
            {
                console.log("Pool not found yet returned 404...");

            }
            else
            {
                console.log("Error occurred while retrieving pool data");
            }
        }
        });
```

Pool.get işlevi tarafından döndürülen örnek bir sonuç nesnesi aşağıdadır.

```
{ id: 'processcsv_201721152',
  displayName: 'processcsv_201721152',
  url: 'https://<batch-account-name>.centralus.batch.azure.com/pools/processcsv_201721152',
  eTag: '<eTag>',
  lastModified: 2017-03-27T10:28:02.398Z,
  creationTime: 2017-03-27T10:28:02.398Z,
  state: 'active',
  stateTransitionTime: 2017-03-27T10:28:02.398Z,
  allocationState: 'resizing',
  allocationStateTransitionTime: 2017-03-27T10:28:02.398Z,
  vmSize: 'standard_a1',
  virtualMachineConfiguration:
   { imageReference:
      { publisher: 'Canonical',
        offer: 'UbuntuServer',
        sku: '14.04.2-LTS',
        version: 'latest' },
     nodeAgentSKUId: 'batch.node.ubuntu 14.04' },
  resizeTimeout:
   { [Number: 900000]
     _milliseconds: 900000,
     _days: 0,
     _months: 0,
     _data:
      { milliseconds: 0,
        seconds: 0,
        minutes: 15,
        hours: 0,
        days: 0,
        months: 0,
        years: 0 },
     _locale:
      Locale {
        _calendar: [Object],
        _longDateFormat: [Object],
        _invalidDate: 'Invalid date',
        ordinal: [Function: ordinal],
        _ordinalParse: /\d{1,2}(th|st|nd|rd)/,
        _relativeTime: [Object],
        _months: [Object],
        _monthsShort: [Object],
        _week: [Object],
        _weekdays: [Object],
        _weekdaysMin: [Object],
        _weekdaysShort: [Object],
        _meridiemParse: /[ap]\.?m?\.?/i,
        _abbr: 'en',
        _config: [Object],
        _ordinalParseLenient: /\d{1,2}(th|st|nd|rd)|\d{1,2}/ } },
  currentDedicated: 0,
  targetDedicated: 4,
  enableAutoScale: false,
  enableInterNodeCommunication: false,
  taskSlotsPerNode: 1,
  taskSchedulingPolicy: { nodeFillType: 'Spread' } }
```

### <a name="step-4-submit-an-azure-batch-job"></a>4. Adım: Azure Batch işi gönderme

Azure Batch işi, benzer görevlerden oluşan bir mantıksal gruptur. Senaryomuzdaki ise "csv'yi JSON’a işle" işidir. Burada her görev her Azure Depolama kapsayıcısında bulunan csv dosyalarını işliyor olabilir.

Bu görevler paralel olarak çalışır ve Azure Batch hizmeti tarafından düzenlenmiş birden çok düğüm arasında dağıtılır.

> [!TIP]
> Aynı anda tek bir düğümde çalışabilen en fazla görev sayısını belirtmek için [Taskslotspernode](https://azure.github.io/azure-sdk-for-node/azure-batch/latest/Pool.html#add) özelliğini kullanabilirsiniz.

#### <a name="preparation-task"></a>Hazırlık görevi

Oluşturulan VM düğümleri boş Ubuntu düğümleridir. Çoğunlukla, önkoşul olarak bir dizi program yüklemeniz gerekir.
Genellikle, Linux düğümleri için gerçek görevleri çalıştırmadan önce önkoşulları yükleyen bir kabuk betiğine sahip olabilirsiniz. Ancak bu, programlanabilir herhangi bir yürütülebilir dosya da olabilir.

Bu örnekteki [kabuk betiği](https://github.com/shwetams/azure-batchclient-sample-nodejs/blob/master/startup_prereq.sh) Python için Azure depolama SDK'sını ve Python PIP’i yükler.

Betiği Azure Depolama Hesabına yüklemeyebilir ve betiğe erişmek için bir SAS URI'sı oluşturabilirsiniz. Bu işlem, Azure Storage JavaScript SDK 'Sı kullanılarak otomatik olarak da yapılabilir.

> [!TIP]
> Bir işin hazırlama görevi, yalnızca ilgili görevi çalıştırması gereken VM düğümlerinde çalışır. Önkoşulların, üzerinde çalışan görevlerden bağımsız olarak tüm düğümlere yüklenmesini istiyorsanız havuz eklerken [startTask](https://azure.github.io/azure-sdk-for-node/azure-batch/latest/Pool.html#add) özelliğini kullanabilirsiniz. Aşağıdaki hazırlık görev tanımını referans olarak kullanabilirsiniz.

Hazırlık görevi, Azure Batch işi gönderildiği sırada belirtilir. Hazırlık görevi yapılandırma parametreleri şunlardır:

- **ID**: Hazırlık görevi için benzersiz bir tanımlayıcıdır.
- **commandLine**: Görevin yürütülebilir dosyasını çalıştıran komut satırıdır.
- **resourceFiles**: Bu görevin çalışması için indirilmesi gereken dosyaların ayrıntılarını sağlayan nesneler dizisidir.  Seçenekleri aşağıda verilmiştir.
  - blobSource: Dosyanın SAS URI'sıdır.
  - filePath: Dosyanın indirileceği ve kaydedileceği için yerel yoldur.
  - fileMode: Yalnızca Linux düğümlerinde kullanılan fileMode, sekizli biçimdedir ve varsayılan değeri 0770’tir.
- **waitForSuccess**: True olarak ayarlandığında hazırlık görevi hatalarında görev çalışmaz.
- **runElevated**: Görevi çalıştırmak için yükseltilmiş ayrıcalıklar gerekiyorsa true olarak ayarlayın.

Aşağıdaki kod parçacığında, hazırlama görevi betik yapılandırma örneği gösterilmektedir:

```javascript
var job_prep_task_config = {id:"installprereq",commandLine:"sudo sh startup_prereq.sh > startup.log",resourceFiles:[{'blobSource':'Blob SAS URI','filePath':'startup_prereq.sh'}],waitForSuccess:true,runElevated:true}
```

Görevleri çalıştırmak yüklenecek herhangi bir önkoşul yoksa hazırlama görevlerini atlayabilirsiniz. Aşağıdaki kod görünen adı "process csv files" olan bir iş oluşturur.

 ```javascript
 // Setting up Batch pool configuration
 var pool_config = {poolId:poolid}
 // Setting up Job configuration along with preparation task
 var jobId = "processcsvjob"
 var job_config = {id:jobId,displayName:"process csv files",jobPreparationTask:job_prep_task_config,poolInfo:pool_config}
 // Adding Azure batch job to the pool
 var job = batch_client.job.add(job_config,function(error,result){
     if(error != null)
     {
         console.log("Error submitting job : " + error.response);
     }});
```

### <a name="step-5-submit-azure-batch-tasks-for-a-job"></a>5. Adım: Bir iş için Azure Batch görevlerini gönderme

İşlem csv işimiz oluşturulduğuna göre bu iş için görevleri oluşturabiliriz. Dört kapsayıcımız olduğunu varsayarsak, her kapsayıcı için bir tane olmak üzere dört görev oluşturmamız gerekir.

[Python betiğine](https://github.com/shwetams/azure-batchclient-sample-nodejs/blob/master/processcsv.py) baktığımızda iki parametre kabul ettiğini görüyoruz:

- kapsayıcı adı: Dosyaların indirileceği Depolama kapsayıcısı
- desen: Dosya adı için isteğe bağlı bir desen parametresi

"con1", "con2", "con3" ve "con4" olmak üzere dört kapsayıcımız olduğunu varsayarsak, aşağıdaki kod daha önce oluşturduğumuz "process csv" Azure batch işine görev göndermeyi gösterir.

```javascript
// storing container names in an array
var container_list = ["con1","con2","con3","con4"]
    container_list.forEach(function(val,index){

           var container_name = val;
           var taskID = container_name + "_process";
           var task_config = {id:taskID,displayName:'process csv in ' + container_name,commandLine:'python processcsv.py --container ' + container_name,resourceFiles:[{'blobSource':'<blob SAS URI>','filePath':'processcsv.py'}]}
           var task = batch_client.task.add(poolid,task_config,function(error,result){
                if(error != null)
                {
                    console.log(error.response);
                }
                else
                {
                    console.log("Task for container : " + container_name + "submitted successfully");
                }



           });

    });
```

Kod, havuza birden çok görev ekler. Oluşturulan tüm görevler, oluşturulan VM havuzunda bir düğüm üzerinde yürütülür. Görev sayısı bir havuzdaki veya taskSlotsPerNode özelliğindeki VM sayısını aşarsa, görevler bir düğüm kullanılabilir duruma gelene kadar bekler. Bu düzenleme Azure Batch tarafından otomatik olarak gerçekleştirilir.

Portalda görevler ve iş durumları hakkında ayrıntılı görünümler bulunur. Ayrıca, Azure JavaScript SDK 'sında List ve Get işlevlerini de kullanabilirsiniz. Ayrıntılar belgeler [bağlantısında](https://azure.github.io/azure-sdk-for-node/azure-batch/latest/Job.html) verilmiştir.

## <a name="next-steps"></a>Sonraki adımlar

- [Batch hizmeti iş akışı ve](batch-service-workflow-features.md) havuzlar, düğümler, işler ve görevler gibi birincil kaynaklar hakkında bilgi edinin.
- Batch API 'sini araştırmak için [Batch JavaScript başvurusuna](/javascript/api/overview/azure/batch) bakın.