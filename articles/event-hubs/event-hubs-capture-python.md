---
title: 'Hızlı başlangıç: Python uygulamasından yakalanan verileri okuma-Azure Event Hubs'
description: "Hızlı başlangıç: Event Hubs yakalama özelliğini göstermek için Azure Python SDK 'sını kullanan betikler."
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: bdff820c-5b38-4054-a06a-d1de207f01f6
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.custom: seodec18
ms.date: 11/05/2019
ms.author: shvija
ms.openlocfilehash: ade4aa79b2de005bfecd7a5882f06cb491ea4e6d
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73717852"
---
# <a name="quickstart-event-hubs-capture-walkthrough-python"></a>Hızlı başlangıç: Event Hubs Capture izlenecek yol: Python

Capture, Azure Event Hubs 'ın bir özelliğidir. Olay Hub 'ınızdaki akış verilerini seçtiğiniz bir Azure Blob depolama hesabına otomatik olarak teslim etmek için yakalama ' yı kullanabilirsiniz. Bu özellik, gerçek zamanlı akış verilerinde toplu işleme yapmayı kolaylaştırır. Bu makalede, Python ile Event Hubs yakalama 'nın nasıl kullanılacağı açıklanır. Event Hubs yakalama hakkında daha fazla bilgi için bkz. [Azure Event Hubs aracılığıyla olayları yakalama][Overview of Event Hubs Capture].

Bu izlenecek yol, yakalama özelliğini göstermek için [Azure Python SDK 'sını](https://azure.microsoft.com/develop/python/) kullanır. *Sender.py* programı, sanal ortam TELEMETRISINI JSON biçiminde Event Hubs gönderir. Olay Hub 'ı, bu verileri toplu halde blob depolamaya yazmak için yakala özelliğini kullanır. *Capturereader.py* uygulaması bu Blobları okur, cihazlarınızın her biri için bir ekleme dosyası oluşturur ve verileri her bir cihaza *. csv* dosyalarına yazar.

Bu izlenecek yolda şunları yapabilirsiniz: 

> [!div class="checklist"]
> * Azure portal bir Azure Blob depolama hesabı ve kapsayıcısı oluşturun.
> * Event Hubs yakalamayı etkinleştirin ve depolama hesabınıza yönlendirin.
> * Bir Python betiği kullanarak Olay Hub 'ınıza veri gönderme.
> * Başka bir Python betiği kullanarak Event Hubs yakalamadan dosyaları okuyun ve işleyin.

## <a name="prerequisites"></a>Ön koşullar

- `pip` yüklenip güncelleştirildiğinden Python 3,4 veya sonraki bir sürümü.
  
- Azure aboneliği. Aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).
  
- [Hızlı başlangıç: Azure Portal kullanarak bir olay hub 'ı oluşturmak](event-hubs-create.md)için bir Active Event Hubs ad alanı ve Olay Hub 'ı oluşturulur. Bu kılavuzda daha sonra kullanmak üzere ad alanınızı ve Olay Hub 'ınızın adını bir yere unutmayın. 
  
  > [!NOTE]
  > Kullanmak için bir depolama Kapsayıcınız zaten varsa, Olay Hub 'ını oluştururken yakalamayı etkinleştirebilir ve depolama kapsayıcısını seçebilirsiniz. 
  > 
  
- Event Hubs paylaşılan erişim anahtarı adı ve birincil anahtar değeri. Event Hubs sayfanızda **paylaşılan erişim ilkeleri** altında bu değerleri bulun veya oluşturun. Varsayılan erişim anahtarı adı **RootManageSharedAccessKey**' dir. Bu izlenecek yolda daha sonra kullanmak için erişim anahtarı adını ve birincil anahtar değerini kopyalayın. 

## <a name="create-an-azure-blob-storage-account-and-container"></a>Azure Blob depolama hesabı ve kapsayıcısı oluşturma

Yakalama için kullanılacak bir depolama hesabı ve kapsayıcı oluşturun. 

1. [Azure portalında][Azure portal] oturum açın.
2. Sol gezinti bölmesinde **depolama hesapları**' nı seçin ve **depolama hesapları** ekranında **Ekle**' yi seçin.
3. Depolama hesabı oluşturma ekranında bir abonelik ve kaynak grubu seçin ve depolama hesabına bir ad verin. Diğer seçimleri varsayılan olarak bırakabilirsiniz. **Gözden geçir + oluştur**' u seçin, ayarları gözden geçirin ve ardından **Oluştur**' u seçin. 
   
   ![Depolama hesabı oluştur][1]
   
4. Dağıtım tamamlandığında **Kaynağa Git**' i seçin ve depolama hesabına **genel bakış** ekranında **kapsayıcılar**' ı seçin.
5. **Kapsayıcılar** ekranında **+ kapsayıcı**' yı seçin. 
6. **Yeni kapsayıcı** ekranında, kapsayıcıya bir ad verin ve ardından **Tamam**' ı seçin. Daha sonra izlenecek yolda kullanılacak kapsayıcı adını bir yere unutmayın. 
7. **Kapsayıcılar** ekranının sol gezinti bölmesinde **erişim tuşları**' nı seçin. Daha sonra izlenecek yol içinde kullanmak için **depolama hesabı adını**ve **KEY1**altındaki **anahtar** değerini kopyalayın.
 
## <a name="enable-event-hubs-capture"></a>Event Hubs yakalamayı etkinleştir

1. Azure portal, **tüm kaynaklardaki**Event Hubs ad alanını seçip sol gezinmede **Olay Hub 'larını** seçip Olay Hub 'ınızı seçerek Olay Hub 'ınıza gidin. 
2. Olay Hub 'ına **genel bakış** ekranında **yakalama olayları**' nı seçin.
3. **Yakalama** ekranında **Açık**' ı seçin. Ardından, **Azure depolama kapsayıcısı**altında **kapsayıcı Seç**' i seçin. 
4. **Kapsayıcılar** ekranında, kullanmak istediğiniz depolama kapsayıcısını seçin ve ardından **Seç**' i seçin. 
5. **Yakalama** ekranında **Değişiklikleri Kaydet**' i seçin. 

## <a name="create-a-python-script-to-send-events-to-event-hub"></a>Olayları Olay Hub 'ına göndermek için bir Python betiği oluşturma
Bu betik, 200 olaylarını Olay Hub 'ınıza gönderir. Olaylar JSON ile gönderilen basit çevresel okumalar.

1. [Visual Studio Code][Visual Studio Code]gibi en sevdiğiniz Python düzenleyicisini açın.
2. *Sender.py*adlı yeni bir dosya oluşturun. 
3. Aşağıdaki kodu *Sender.py*' ye yapıştırın. Event Hubs \<ad alanı >, \<AccessKeyName >, \<birincil anahtar değeri > ve \<eventhub > için kendi değerlerinizi değiştirin.
   
   ```python
   import uuid
   import datetime
   import random
   import json
   from azure.servicebus.control_client import ServiceBusService
   
   sbs = ServiceBusService(service_namespace='<namespace>', shared_access_key_name='<AccessKeyName>', shared_access_key_value='<primary key value>')
   devices = []
   for x in range(0, 10):
       devices.append(str(uuid.uuid4()))
   
   for y in range(0,20):
       for dev in devices:
           reading = {'id': dev, 'timestamp': str(datetime.datetime.utcnow()), 'uv': random.random(), 'temperature': random.randint(70, 100), 'humidity': random.randint(70, 100)}
           s = json.dumps(reading)
           sbs.send_event('<eventhub>', s)
       print(y)
   ```
4. Dosyayı kaydedin.

## <a name="create-a-python-script-to-read-capture-files"></a>Yakalama dosyalarını okumak için bir Python betiği oluşturma

Bu betik yakalanan dosyaları okur ve yalnızca bu cihaz için verileri yazmak üzere cihazlarınızın her biri için bir dosya oluşturur.

1. Python Düzenleyicinizde *capturereader.py*adlı yeni bir dosya oluşturun. 
2. Aşağıdaki kodu *capturereader.py*' ye yapıştırın. \<storageaccount >, \<depolama hesabı erişim anahtarı > ve \<storagecontainer > için kaydedilmiş değerlerinizi değiştirin.
   
   ```python
   import os
   import string
   import json
   import avro.schema
   from avro.datafile import DataFileReader, DataFileWriter
   from avro.io import DatumReader, DatumWriter
   from azure.storage.blob import BlockBlobService
   
   def processBlob(filename):
       reader = DataFileReader(open(filename, 'rb'), DatumReader())
       dict = {}
       for reading in reader:
           parsed_json = json.loads(reading["Body"])
           if not 'id' in parsed_json:
               return
           if not parsed_json['id'] in dict:
               list = []
               dict[parsed_json['id']] = list
           else:
               list = dict[parsed_json['id']]
               list.append(parsed_json)
       reader.close()
       for device in dict.keys():
           deviceFile = open(device + '.csv', "a")
           for r in dict[device]:
               deviceFile.write(", ".join([str(r[x]) for x in r.keys()])+'\n')
   
   def startProcessing(accountName, key, container):
       print('Processor started using path: ' + os.getcwd())
       block_blob_service = BlockBlobService(account_name=accountName, account_key=key)
       generator = block_blob_service.list_blobs(container)
       for blob in generator:
           #content_length == 508 is an empty file, so only process content_length > 508 (skip empty files)
           if blob.properties.content_length > 508:
               print('Downloaded a non empty blob: ' + blob.name)
               cleanName = str.replace(blob.name, '/', '_')
               block_blob_service.get_blob_to_path(container, blob.name, cleanName)
               processBlob(cleanName)
               os.remove(cleanName)
           block_blob_service.delete_blob(container, blob.name)
   startProcessing('<storageaccount>', '<storage account access key>', '<storagecontainer>')
   ```

## <a name="run-the-python-scripts"></a>Python betiklerini çalıştırma

1. Yolunda Python içeren bir komut istemi açın ve Python önkoşul paketlerini yüklemek için şu komutları çalıştırın:
   
   ```cmd
   pip install azure-storage
   pip install azure-servicebus
   pip install avro-python3
   ```
   
   `azure-storage` veya `azure`daha önceki bir sürümüne sahipseniz `--upgrade` seçeneğini kullanmanız gerekebilir.
   
   Ayrıca, aşağıdaki komutu çalıştırmanız gerekebilir. Bu komutun çalıştırılması çoğu sistemde gerekli değildir. 
   
   ```cmd
   pip install cryptography
   ```
   
2. *Sender.py* ve *capturereader.py*kaydettiğiniz dizinden şu komutu çalıştırın:
   
   ```cmd
   start python sender.py
   ```
   
   Komut, göndereni çalıştırmak için yeni bir Python işlemi başlatır.
   
3. Yakalama çalışmayı bitirdiğinde şu komutu çalıştırın:
   
   ```cmd
   python capturereader.py
   ```

   Yakalama işlemcisi, boş olmayan tüm Blobları depolama hesabı kapsayıcısından indirir ve sonuçları *. csv* dosyası olarak yerel dizine yazar. 

## <a name="next-steps"></a>Sonraki adımlar

Event Hubs hakkında daha fazla bilgi edinmek için bkz.: 

* [Event Hubs yakalamaya genel bakış][Overview of Event Hubs Capture]
* [Event Hubs kullanan örnek uygulamalar](https://github.com/Azure/azure-event-hubs/tree/master/samples)
* [Event Hubs’a genel bakış][Event Hubs overview]

[Azure portal]: https://portal.azure.com/
[Overview of Event Hubs Capture]: event-hubs-capture-overview.md
[1]: ./media/event-hubs-archive-python/event-hubs-python1.png
[About Azure storage accounts]:../storage/common/storage-create-storage-account.md
[Visual Studio Code]: https://code.visualstudio.com/
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
