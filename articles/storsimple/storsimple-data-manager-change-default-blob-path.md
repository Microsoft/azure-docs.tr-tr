---
title: Blob yolunu varsayılana Değiştir
description: Varsayılan bir BLOB dosya yolunu yeniden adlandırmak ve Blobları farklı bir konuma taşımak için bir Azure işlevi ayarlamayı öğrenin.
author: alkohli
ms.service: storsimple
ms.topic: how-to
ms.date: 01/16/2018
ms.author: alkohli
ms.openlocfilehash: 6af095c7abdb9aa61e57d543ff2ab2f9192dadc8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96011447"
---
# <a name="change-a-blob-path-from-the-default-path"></a>Blob yolunu varsayılan yoldan değiştirme

StorSimple Veri Yöneticisi hizmeti verileri dönüştürzaman, varsayılan olarak, dönüştürülmüş blob 'ları hedef deponun oluşturulması sırasında belirtilen şekilde bir depolama kapsayıcısına koyar. Blob 'lar bu konuma ulaştığında, bu Blobları alternatif bir konuma taşımak isteyebilirsiniz. Bu makalede, bir Azure işlevinin varsayılan bir BLOB dosya yolunu yeniden adlandırmak için nasıl ayarlanacağı ve bu nedenle Blobları farklı bir konuma taşımanın nasıl yapılacağı açıklanır.

## <a name="prerequisites"></a>Önkoşullar

StorSimple Veri Yöneticisi hizmetinizde doğru şekilde yapılandırılmış bir iş tanımına sahip olduğunuzdan emin olun.

## <a name="create-an-azure-function"></a>Azure işlevi oluşturma

Bir Azure işlevi oluşturmak için aşağıdaki adımları gerçekleştirin:

1. [Azure Portal](https://portal.azure.com/) gidin.

2. **+ Kaynak oluştur**’a tıklayın. **Arama** kutusuna **işlev uygulaması** yazın ve **ENTER** tuşuna basın. Görünen uygulamalar listesinden **işlev uygulaması** ' nı seçin ve tıklayın.

    ![Arama kutusuna "İşlev Uygulaması" yazın](./media/storsimple-data-manager-change-default-blob-path/search-function-app.png)

3. **Oluştur**’a tıklayın.

    ![İşlev Uygulaması penceresi "Oluştur" düğmesi](./media/storsimple-data-manager-change-default-blob-path/create-function-app.png)

4. **İşlev uygulaması** yapılandırma dikey penceresinde aşağıdaki adımları uygulayın:

    1. Benzersiz bir **uygulama adı** girin.
    2. Açılan listeden **aboneliği** seçin. Bu abonelik, StorSimple Veri Yöneticisi hizmetinize ilişkili olan ile aynı olmalıdır.
    3. Yeni kaynak grubu **Oluştur** ' u seçin.
    4. **Barındırma planı** açılan listesi Için **Tüketim planı**' nı seçin.
    5. İşlevinizin çalıştığı bir konum belirtin. StorSimple Veri Yöneticisi hizmetinin ve iş tanımıyla ilişkili depolama hesabının bulunduğu aynı bölgeyi istiyorsunuz.
    6. Mevcut bir depolama hesabını seçin veya yeni bir depolama hesabı oluşturun. Bir depolama hesabı, işlev için dahili olarak kullanılır.

        ![Yeni İşlev Uygulaması yapılandırma verileri girin](./media/storsimple-data-manager-change-default-blob-path/function-app-parameters.png)

    7. **Oluştur**’a tıklayın. İşlev uygulaması oluşturuldu.
     
        ![İşlev Uygulaması oluşturuldu](./media/storsimple-data-manager-change-default-blob-path/function-app-created.png)

5. **İşlevler**' i seçin ve **+ yeni işlev**' e tıklayın.

    ![+ Yeni işlev ' e tıklayın](./media/storsimple-data-manager-change-default-blob-path/create-new-function.png)

6. Dil için **C#** ' ı seçin. Şablon kutucukları dizisinde **Queuetrigger-CSharp** kutucuğunda **C#** ' ı seçin.

7. **Kuyruk tetikleyicisinde**:

    1. İşleviniz için bir **ad** girin.
    2. **Sıra adı** kutusuna, veri dönüştürme işi tanım adınızı yazın.
    3. **Depolama hesabı bağlantısı** altında **Yeni**' ye tıklayın. Depolama hesapları listesinden iş tanımınızdan ilişkilendirilen hesabı seçin. Bağlantı adını (vurgulanmış) bir yere getirin. Ad, daha sonra Azure işlevinde gereklidir.

        ![Yeni bir C# işlevi oluşturma](./media/storsimple-data-manager-change-default-blob-path/new-function-parameters.png)

    4. **Oluştur**’a tıklayın. **İşlev** oluşturulur.

     
10. Işlev penceresinde _. CSX_ dosyasını çalıştırın.

    ![Yeni bir C# işlevi oluştur 2](./media/storsimple-data-manager-change-default-blob-path/new-function-run-csx.png)
    
    Aşağıdaki adımları uygulayın.

    1. Aşağıdaki kodu yapıştırın:

        ```
        using System;
        using System.Configuration;
        using Microsoft.WindowsAzure.Storage.Blob;
        using Microsoft.WindowsAzure.Storage.Queue;
        using Microsoft.WindowsAzure.Storage;
        using System.Collections.Generic;
        using System.Linq;

        public static void Run(QueueItem myQueueItem, TraceWriter log)
        {
            CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConfigurationManager.AppSettings["STORAGE_CONNECTIONNAME"]);

            string storageAccUriEndswith = "windows.net/";
            string uri = myQueueItem.TargetLocation.Replace("%20", " ");
            log.Info($"Blob Uri: {uri}");

            // Remove storage account uri string
            uri = uri.Substring(uri.IndexOf(storageAccUriEndswith) + storageAccUriEndswith.Length);

            string containerName = uri.Substring(0, uri.IndexOf("/")); 

            // Remove container name string
            uri = uri.Substring(containerName.Length + 1);

            // Current blob path
            string blobName = uri; 

            string volumeName = uri.Substring(containerName.Length + 1);
            volumeName = uri.Substring(0, uri.IndexOf("/"));

            // Remove volume name string
            uri = uri.Substring(volumeName.Length + 1);

            string newContainerName = uri.Substring(0, uri.IndexOf("/")).ToLower();
            string newBlobName = uri.Substring(newContainerName.Length + 1);

            log.Info($"Container name: {containerName}");
            log.Info($"Volume name: {volumeName}");
            log.Info($"New container name: {newContainerName}");

            log.Info($"Blob name: {blobName}");
            log.Info($"New blob name: {newBlobName}");

            // Create the blob client.
            CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

            // Container reference
            CloudBlobContainer container = blobClient.GetContainerReference(containerName);
            CloudBlobContainer newContainer = blobClient.GetContainerReference(newContainerName);
            newContainer.CreateIfNotExists();

            if(!container.Exists())
            {
                log.Info($"Container - {containerName} not exists");
                return;
            }

            if(!newContainer.Exists())
            {
                log.Info($"Container - {newContainerName} not exists");
                return;
            }

            CloudBlockBlob blob = container.GetBlockBlobReference(blobName);
            if (!blob.Exists())
            {
                // Skip to copy the blob to new container, if source blob doesn't exist
                log.Info($"The specified blob does not exist.");
                log.Info($"Blob Uri: {blob.Uri}");
                return;
            }

            CloudBlockBlob blobCopy = newContainer.GetBlockBlobReference(newBlobName);
            if (!blobCopy.Exists())
            {
                blobCopy.StartCopy(blob);
                // Delete old blob, after copy to new container
                blob.DeleteIfExists();
                log.Info($"Blob file path renamed completed successfully");
            }
            else
            {
                log.Info($"Blob file path renamed already done");
                // Delete old blob, if already exists.
                blob.DeleteIfExists();
            }
        }

        public class QueueItem
        {
            public string SourceLocation {get;set;}
            public long SizeInBytes {get;set;}
            public string Status {get;set;}
            public string JobID {get;set;}
            public string TargetLocation {get; set;}
        }

        ```

    2. 11. satırdaki **STORAGE_CONNECTIONNAME** , depolama hesabı bağlantınızla değiştirin (bkz. adım 7c).

        ![Depolama bağlantı adını Kopyala](./media/storsimple-data-manager-change-default-blob-path/new-function-storage-connection-name.png)

    3. İşlevi **kaydedin** .

        ![İşlevi Kaydet](./media/storsimple-data-manager-change-default-blob-path/save-function.png)

12. İşlevi gerçekleştirmek için aşağıdaki adımları uygulayarak bir daha dosya ekleyin:

    1. **Dosyaları görüntüle**' ye tıklayın.

       !["Dosyaları görüntüle" bağlantısı](./media/storsimple-data-manager-change-default-blob-path/view-files.png)

    2. **+ Ekle**'ye tıklayın.
        
        ![Yeni işlev ekleme dosyası Ekle](./media/storsimple-data-manager-change-default-blob-path/new-function-add-file.png)
    
    3. **project.js** yazın ve ardından **ENTER** tuşuna basın. Dosyadaki **project.js** , aşağıdaki kodu yapıştırın:

        ```
        {
        "frameworks": {
            "net46":{
            "dependencies": {
                "windowsazure.storage": "8.1.1"
            }
            }
        }
        }

        ```

    
    4. **Kaydet**’e tıklayın.

        ![Yeni işlev projesi JSON 'ı](./media/storsimple-data-manager-change-default-blob-path/new-function-project-json.png)

Bir Azure işlevi oluşturdunuz. Bu işlev, veri dönüştürme işi tarafından her yeni blob oluşturulduğunda tetiklenir.

## <a name="next-steps"></a>Sonraki adımlar

[Verilerinizi dönüştürmek için StorSimple Veri Yöneticisi Kullanıcı arabirimini kullanma](storsimple-data-manager-ui.md)
