---
title: Python uygulamasından Azure Event Hubs yakalanan verileri okuma (en son)
description: Bu makalede, bir olay hub 'ına gönderilen verileri yakalamak ve bir Azure depolama hesabından yakalanan olay verilerini okumak için Python kodu yazma yöntemi gösterilmektedir.
ms.topic: quickstart
ms.date: 01/04/2021
ms.openlocfilehash: acc2ce04add5fd837e9edc789e9616a9f04fb4b9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97883206"
---
# <a name="capture-event-hubs-data-in-azure-storage-and-read-it-by-using-python-azure-eventhub"></a>Azure depolama 'da Event Hubs verileri yakalayın ve Python kullanarak okuyun (Azure-eventhub)

Bir olay hub 'ına bir olay hub 'ına gönderilen verilerin bir Azure depolama hesabında veya Azure Data Lake Storage Gen 1 veya Gen 2 ' ye göre yakalanabilmesi için bir olay hub 'ı yapılandırabilirsiniz. Bu makalede, bir olay hub 'ına olayları göndermek ve **Azure Blob depolamadan** yakalanan verileri okumak için Python kodu yazma yöntemi gösterilmektedir. Bu özellik hakkında daha fazla bilgi için bkz. [Event Hubs Capture özelliğine genel bakış](event-hubs-capture-overview.md).

Bu hızlı başlangıç, yakalama özelliğini göstermek için [Azure Python SDK 'sını](https://azure.microsoft.com/develop/python/) kullanır. *Sender.py* UYGULAMASı, JSON biçimindeki Olay Hub 'larına sanal çevresel telemetri gönderir. Olay Hub 'ı, bu verileri toplu halde blob depolamaya yazmak için yakala özelliğini kullanacak şekilde yapılandırılmıştır. *Capturereader.py* uygulaması bu Blobları okur ve her cihaz için bir ekleme dosyası oluşturur. Uygulama daha sonra verileri CSV dosyalarına yazar.

Bu hızlı başlangıçta: 

> [!div class="checklist"]
> * Azure portal bir Azure Blob depolama hesabı ve kapsayıcısı oluşturun.
> * Azure portal kullanarak Event Hubs bir ad alanı oluşturun.
> * Yakalama özelliğini etkin bir olay hub 'ı oluşturun ve depolama hesabınıza bağlayın.
> * Bir Python betiği kullanarak Olay Hub 'ınıza veri gönderme.
> * Başka bir Python betiği kullanarak Event Hubs yakalamadan dosyaları okuyun ve işleyin.

## <a name="prerequisites"></a>Önkoşullar

- PıP ve aşağıdaki paketler yüklü Python. Bu makaledeki kod, bu sürümlere karşı test edilmiştir. 
    - Python 3.7
    - Azure-eventhub 5.2.0
    - Azure-Storage-blob 12.6.0
    - avro-python3 1.10.1
- Azure aboneliği. Bir tane yoksa, başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/) .  
- Etkin bir Event Hubs ad alanı ve Olay Hub 'ı.
[Ad alanında bir Event Hubs ad alanı ve bir olay hub 'ı oluşturun](event-hubs-create.md). Event Hubs ad alanının adını, Olay Hub 'ının adını ve ad alanı için birincil erişim anahtarını kaydedin. Erişim anahtarını almak için bkz. [Event Hubs bağlantı dizesi edinme](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Varsayılan anahtar adı *RootManageSharedAccessKey*' dir. Bu hızlı başlangıçta yalnızca birincil anahtar gereklidir. Bağlantı dizesine ihtiyacınız yoktur.  
- Bir Azure depolama hesabı, depolama hesabındaki bir blob kapsayıcısı ve depolama hesabına yönelik bir bağlantı dizesi. Bu öğeleriniz yoksa şunları yapın:  
    1. [Azure depolama hesabı oluşturma](../storage/common/storage-account-create.md?tabs=azure-portal)  
    1. [Depolama hesabında bir blob kapsayıcısı oluşturma](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)  
    1. [Bağlantı dizesini depolama hesabına al](../storage/common/storage-configure-connection-string.md)

    Bu hızlı başlangıçta kullanmak için bağlantı dizesini ve kapsayıcı adını kaydettiğinizden emin olun.  
- Olay Hub 'ı için yakalama özelliğini etkinleştirin. Bunu yapmak için [Azure Portal kullanarak Event Hubs yakalamayı etkinleştirme](event-hubs-capture-enable-through-portal.md)' deki yönergeleri izleyin. Önceki adımda oluşturduğunuz depolama hesabını ve BLOB kapsayıcısını seçin. Ayrıca, bir olay hub 'ı oluşturduğunuzda özelliği etkinleştirebilirsiniz.  

## <a name="create-a-python-script-to-send-events-to-your-event-hub"></a>Olay Hub 'ınıza olayları göndermek için bir Python betiği oluşturma
Bu bölümde, bir olay hub 'ına 200 olayları (10 cihaz * 20 olay) gönderen bir Python betiği oluşturacaksınız. Bu olaylar JSON biçiminde gönderilen örnek bir çevre okumasından oluşan bir örnektir. 

1. [Visual Studio Code][Visual Studio Code]gibi en sevdiğiniz Python düzenleyicisini açın.
2. *Sender.py* adlı bir komut dosyası oluşturun. 
3. Aşağıdaki kodu *Sender.py*' ye yapıştırın. 
   
    ```python
    import time
    import os
    import uuid
    import datetime
    import random
    import json
    
    from azure.eventhub import EventHubProducerClient, EventData
    
    # This script simulates the production of events for 10 devices.
    devices = []
    for x in range(0, 10):
        devices.append(str(uuid.uuid4()))
    
    # Create a producer client to produce and publish events to the event hub.
    producer = EventHubProducerClient.from_connection_string(conn_str="EVENT HUBS NAMESAPCE CONNECTION STRING", eventhub_name="EVENT HUB NAME")
    
    for y in range(0,20):    # For each device, produce 20 events. 
        event_data_batch = producer.create_batch() # Create a batch. You will add events to the batch later. 
        for dev in devices:
            # Create a dummy reading.
            reading = {'id': dev, 'timestamp': str(datetime.datetime.utcnow()), 'uv': random.random(), 'temperature': random.randint(70, 100), 'humidity': random.randint(70, 100)}
            s = json.dumps(reading) # Convert the reading into a JSON string.
            event_data_batch.add(EventData(s)) # Add event data to the batch.
        producer.send_batch(event_data_batch) # Send the batch of events to the event hub.
    
    # Close the producer.    
    producer.close()
    ```
4. Betiklerdeki aşağıdaki değerleri değiştirin:  
    * `EVENT HUBS NAMESPACE CONNECTION STRING`Event Hubs ad alanınız için bağlantı dizesiyle değiştirin.  
    * `EVENT HUB NAME`Olay Hub 'ınızın adıyla değiştirin.  
5. Olayları Olay Hub 'ına göndermek için betiği çalıştırın.  
6. Azure portal, Olay Hub 'ının iletileri aldığını doğrulayabilirsiniz. **Ölçümler** bölümünde **iletiler** görünümüne geçin. Grafiği güncelleştirmek için sayfayı yenileyin. Sayfanın iletilerin alındığını görüntülemesi birkaç saniye sürebilir. 

    [![Olay Hub 'ının iletileri aldığını doğrulama](./media/get-started-capture-python-v2/messages-portal.png)](./media/get-started-capture-python-v2/messages-portal.png#lightbox)

## <a name="create-a-python-script-to-read-your-capture-files"></a>Yakalama dosyalarınızı okumak için bir Python betiği oluşturun
Bu örnekte yakalanan veriler Azure Blob depolama alanında depolanır. Bu bölümdeki betik, Azure Depolama hesabınızdaki yakalanan veri dosyalarını okur ve kolayca açıp görüntüleyebilmeniz için CSV dosyaları oluşturur. Uygulamanın geçerli çalışma dizininde 10 dosya görürsünüz. Bu dosyalar 10 cihaz için çevresel ayarları içerir. 

1. Python Düzenleyicinizde *capturereader.py* adlı bir komut dosyası oluşturun. Bu betik yakalanan dosyaları okur ve her bir cihaz için verileri yalnızca o cihaza yazmak üzere bir dosya oluşturur.
2. Aşağıdaki kodu *capturereader.py*' ye yapıştırın. 
   
    ```python
    import os
    import string
    import json
    import uuid
    import avro.schema
    
    from azure.storage.blob import ContainerClient, BlobClient
    from avro.datafile import DataFileReader, DataFileWriter
    from avro.io import DatumReader, DatumWriter
    
    
    def processBlob2(filename):
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
            filename = os.getcwd() + '\\' + str(device) + '.csv'
            deviceFile = open(filename, "a")
            for r in dict[device]:
                deviceFile.write(", ".join([str(r[x]) for x in r.keys()])+'\n')
    
    def startProcessing():
        print('Processor started using path: ' + os.getcwd())
        # Create a blob container client.
        container = ContainerClient.from_connection_string("AZURE STORAGE CONNECTION STRING", container_name="BLOB CONTAINER NAME")
        blob_list = container.list_blobs() # List all the blobs in the container.
        for blob in blob_list:
            # Content_length == 508 is an empty file, so process only content_length > 508 (skip empty files).        
            if blob.size > 508:
                print('Downloaded a non empty blob: ' + blob.name)
                # Create a blob client for the blob.
                blob_client = ContainerClient.get_blob_client(container, blob=blob.name)
                # Construct a file name based on the blob name.
                cleanName = str.replace(blob.name, '/', '_')
                cleanName = os.getcwd() + '\\' + cleanName 
                with open(cleanName, "wb+") as my_file: # Open the file to write. Create it if it doesn't exist. 
                    my_file.write(blob_client.download_blob().readall()) # Write blob contents into the file.
                processBlob2(cleanName) # Convert the file into a CSV file.
                os.remove(cleanName) # Remove the original downloaded file.
                # Delete the blob from the container after it's read.
                container.delete_blob(blob.name)
    
    startProcessing()    
    ```
3. `AZURE STORAGE CONNECTION STRING`Azure depolama hesabınızın bağlantı dizesiyle değiştirin. Bu hızlı başlangıçta oluşturduğunuz kapsayıcının adı *yakala*. Kapsayıcı için farklı bir ad kullandıysanız, *yakalamayı* depolama hesabındaki kapsayıcının adıyla değiştirin. 

## <a name="run-the-scripts"></a>Betikleri çalıştırma
1. Yolunda Python içeren bir komut istemi açın ve ardından Python önkoşul paketlerini yüklemek için şu komutları çalıştırın:
   
   ```
   pip install azure-storage-blob
   pip install azure-eventhub
   pip install avro-python3
   ```

    > [!NOTE]
    > Bu makaledeki kod, bu sürümlere karşı test edilmiştir. 
    > - Python 3.7
    > - Azure-eventhub 5.2.0
    > - Azure-Storage-blob 12.6.0
    > - avro-python3 1.10.1
2. Dizininizi *Sender.py* ve *capturereader.py* kaydettiğiniz dizinle değiştirin ve şu komutu çalıştırın:
   
   ```
   python sender.py
   ```
   
   Bu komut, göndereni çalıştırmak için yeni bir Python işlemi başlatır.
3. Yakalamanın çalışması için birkaç dakika bekleyin ve ardından özgün komut pencerenizde aşağıdaki komutu girin:
   
   ```
   python capturereader.py
   ```

   Bu yakalama işlemcisi, depolama hesabı ve kapsayıcısından tüm Blobları indirmek için yerel dizini kullanır. Bu, boş olmayan her türlü işlemi işler ve sonuçları yerel dizine CSV dosyaları olarak yazar.

## <a name="next-steps"></a>Sonraki adımlar
[GitHub 'Da Python örneklerine](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples)göz atın. 


[Azure portal]: https://portal.azure.com/
[Overview of Event Hubs Capture]: event-hubs-capture-overview.md
[1]: ./media/event-hubs-archive-python/event-hubs-python1.png
[About Azure storage accounts]:../storage/common/storage-create-storage-account.md
[Visual Studio Code]: https://code.visualstudio.com/
[Event Hubs overview]: ./event-hubs-about.md
