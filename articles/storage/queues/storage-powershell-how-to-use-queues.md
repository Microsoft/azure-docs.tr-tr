---
title: Azure kuyruk depolamada PowerShell ile Azure depolama üzerinde işlem gerçekleştirme
description: PowerShell ile Azure kuyruk depolama üzerinde işlem gerçekleştirme
author: mhopkins-msft
ms.author: mhopkins
ms.date: 05/15/2019
ms.service: storage
ms.subservice: queues
ms.topic: conceptual
ms.reviewer: cbrooks
ms.openlocfilehash: bf5cf668620eb08e0d808c2052eac59b15af740c
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68721220"
---
# <a name="perform-azure-queue-storage-operations-with-azure-powershell"></a>Azure PowerShell ile Azure kuyruk depolama işlemlerini gerçekleştirme

Azure kuyruk depolama, dünyanın her yerinden HTTP veya HTTPS aracılığıyla erişilebilen çok sayıda iletiyi depolamaya yönelik bir hizmettir. Ayrıntılı bilgi için bkz. [Azure kuyruklarına giriş](storage-queues-introduction.md). Bu nasıl yapılır makalesi, genel kuyruk depolama işlemlerini içerir. Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:

> [!div class="checklist"]
>
> * Kuyruk oluştur
> * Kuyruğu alma
> * İleti ekleme
> * İletiyi oku
> * İletiyi silme
> * Bir kuyruk silme

Bu nasıl yapılır, Azure PowerShell modülünü az sürüm 0,7 veya üstünü gerektirir. Sürümü bulmak için `Get-Module -ListAvailable Az` komutunu çalıştırın. Yükseltmeniz gerekirse, bkz. [Azure PowerShell modülünü yükleme](/powershell/azure/install-Az-ps).

Kuyruklar için veri düzlemi için PowerShell cmdlet 'leri yok. İleti ekleme, ileti okuma ve iletiyi silme gibi veri düzlemi işlemlerini gerçekleştirmek için, PowerShell 'de açığa çıkarılan .NET depolama istemci kitaplığını kullanmanız gerekir. Bir ileti nesnesi oluşturun ve ardından bu ileti üzerinde işlem yapmak için AddMessage gibi komutları kullanabilirsiniz. Bu makalede bunun nasıl yapılacağı gösterilmektedir.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Azure'da oturum açma

`Connect-AzAccount` komutuyla Azure aboneliğinizde oturum açın ve ekrandaki yönergeleri izleyin.

```powershell
Connect-AzAccount
```

## <a name="retrieve-list-of-locations"></a>Konumların listesini al

Kullanmak istediğiniz konumdan emin değilseniz, kullanılabilir konumları listeleyebilirsiniz. Liste görüntülendikten sonra, kullanmak istediğiniz öğeyi bulun. Bu alıştırma, **eastus**kullanır. Bunu daha sonra kullanmak üzere değişken **konumunda** saklayın.

```powershell
Get-AzLocation | select Location
$location = "eastus"
```

## <a name="create-resource-group"></a>Kaynak grubu oluşturma

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) komutuyla bir kaynak grubu oluşturun.

Azure kaynak grubu, Azure kaynaklarının dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır. Kaynak grubu adını ileride kullanılmak üzere bir değişkende depolayın. Bu örnekte, *eastus* bölgesinde *howtoqueuesrg* adlı bir kaynak grubu oluşturulur.

```powershell
$resourceGroup = "howtoqueuesrg"
New-AzResourceGroup -ResourceGroupName $resourceGroup -Location $location
```

## <a name="create-storage-account"></a>Depolama hesabı oluştur

[New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount)kullanarak yerel olarak yedekli depolama (LRS) ile standart bir genel amaçlı depolama hesabı oluşturun. Kullanılacak depolama hesabını tanımlayan depolama hesabı bağlamını alın. Depolama hesabında bir işlem gerçekleştirirken, kimlik bilgilerini tekrar tekrar sağlamak yerine bağlama başvurursunuz.

```powershell
$storageAccountName = "howtoqueuestorage"
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -Location $location `
  -SkuName Standard_LRS

$ctx = $storageAccount.Context
```

## <a name="create-a-queue"></a>Kuyruk oluştur

Aşağıdaki örnek, depolama hesabı adını ve erişim anahtarını içeren depolama hesabı bağlamını kullanarak Azure depolama 'ya bir bağlantı kurar. Ardından, ' SıraAdı ' adlı bir sıra oluşturmak için [New-AzStorageQueue](/powershell/module/az.storage/New-AzStorageQueue) cmdlet 'ini çağırır.

```powershell
$queueName = "howtoqueue"
$queue = New-AzStorageQueue –Name $queueName -Context $ctx
```

Azure kuyruk hizmeti için adlandırma kuralları hakkında daha fazla bilgi için bkz. [adlandırma sıraları ve meta verileri](https://msdn.microsoft.com/library/azure/dd179349.aspx).

## <a name="retrieve-a-queue"></a>Kuyruğu alma

Belirli bir kuyruğu sorgulayabilir ve alabilir ve bir depolama hesabındaki tüm kuyrukların listesini alabilirsiniz. Aşağıdaki örneklerde, depolama hesabındaki tüm sıraların ve belirli bir sıranın nasıl alınacağını gösterilmektedir. Her iki komut de [Get-AzStorageQueue](/powershell/module/az.storage/Get-AzStorageQueue) cmdlet 'ini kullanır.

```powershell
# Retrieve a specific queue
$queue = Get-AzStorageQueue –Name $queueName –Context $ctx
# Show the properties of the queue
$queue

# Retrieve all queues and show their names
Get-AzStorageQueue -Context $ctx | select Name
```

## <a name="add-a-message-to-a-queue"></a>Kuyruğa ileti ekleme

Kuyruktaki gerçek iletileri etkileyen işlemler, PowerShell 'de kullanıma sunulan .NET depolama istemci kitaplığını kullanır. Bir kuyruğa ileti eklemek için, [Microsoft. Azure. Storage. Queue. CloudQueueMessage](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.queue._cloud_queue_message) sınıfının ileti nesnesinin yeni bir örneğini oluşturun. Ardından [AddMessage](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.queue._cloud_queue.addmessage) yöntemini çağırın. Bir CloudQueueMessage, bir dizeden (UTF-8 biçiminde) ya da bir bayt dizisinden oluşturulabilir.

Aşağıdaki örnek, kuyruğunuza nasıl bir ileti ekleneceğini gösterir.

```powershell
# Create a new message using a constructor of the CloudQueueMessage class
$queueMessage = New-Object -TypeName "Microsoft.Azure.Storage.Queue.CloudQueueMessage,$($queue.CloudQueue.GetType().Assembly.FullName)" `
  -ArgumentList "This is message 1"
# Add a new message to the queue
$queue.CloudQueue.AddMessageAsync($QueueMessage)

# Add two more messages to the queue
$queueMessage = New-Object -TypeName "Microsoft.Azure.Storage.Queue.CloudQueueMessage,$($queue.CloudQueue.GetType().Assembly.FullName)" `
  -ArgumentList "This is message 2"
$queue.CloudQueue.AddMessageAsync($QueueMessage)
$queueMessage = New-Object -TypeName "Microsoft.Azure.Storage.Queue.CloudQueueMessage,$($queue.CloudQueue.GetType().Assembly.FullName)" `
  -ArgumentList "This is message 3"
$queue.CloudQueue.AddMessageAsync($QueueMessage)
```

[Azure Depolama Gezgini](https://storageexplorer.com)kullanıyorsanız, Azure hesabınıza bağlanabilir ve depolama hesabındaki kuyrukları görüntüleyebilir ve sıradaki iletileri görüntülemek için bir kuyruğun detayına gidebilirsiniz.

## <a name="read-a-message-from-the-queue-then-delete-it"></a>Kuyruktaki bir iletiyi okuyun ve silin

İletiler en iyi deneme-ilk çıkar sırasına göre okundu. Bu garanti edilmez. İletiyi kuyruktan okuduğunuzda, kuyruğa bakıyor diğer tüm işlemlerin görünmez hale gelir. Bu, bir donanım veya yazılım arızası nedeniyle kodunuzun iletiyi işleyememesi durumunda, kodunuzun başka bir örneğinin aynı mesajı almasını ve yeniden denemesini sağlar.  

Bu **geçersiz zaman aşımı** , iletinin işlenmek üzere yeniden kullanılabilmesi için önce ne kadar süreyle görünmez kalabileceğini tanımlar. Varsayılan değer 30 saniyedir.

Kodunuz, sıradaki bir iletiyi iki adımda okur. [Microsoft. Azure. Storage. Queue. CloudQueue. GetMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessage) yöntemini çağırdığınızda sıradaki bir sonraki iletiyi alırsınız. **GetMessage**’dan dönen bir ileti bu kuyruktaki kod okuyan iletilere karşı görünmez olur. İletiyi kuyruktan kaldırmayı tamamlaması için [Microsoft. Azure. Storage. Queue. CloudQueue. DeleteMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.deletemessage) yöntemini çağırın.

Aşağıdaki örnekte, üç kuyruk iletisini okuyun ve ardından 10 saniye (geçersiz zaman aşımı) bekleyin. Ardından, bu üç iletiyi yeniden okuyarak, **deleteMessage**çağırarak iletileri okuduktan sonra silin. İletiler silindikten sonra kuyruğu okumaya çalışırsanız, $queueMessage NULL olarak döndürülür.

```powershell
# Set the amount of time you want to entry to be invisible after read from the queue
# If it is not deleted by the end of this time, it will show up in the queue again
$invisibleTimeout = [System.TimeSpan]::FromSeconds(10)

# Read the message from the queue, then show the contents of the message. Read the other two messages, too.
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result

# After 10 seconds, these messages reappear on the queue.
# Read them again, but delete each one after reading it.
# Delete the message.
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result
$queue.CloudQueue.DeleteMessageAsync($queueMessage.Result.Id,$queueMessage.Result.popReceipt)
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result
$queue.CloudQueue.DeleteMessageAsync($queueMessage.Result.Id,$queueMessage.Result.popReceipt)
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result
$queue.CloudQueue.DeleteMessageAsync($queueMessage.Result.Id,$queueMessage.Result.popReceipt)
```

## <a name="delete-a-queue"></a>Bir kuyruk silme

Bir kuyruğu ve içerdiği tüm iletileri silmek için Remove-AzStorageQueue cmdlet 'ini çağırın. Aşağıdaki örnek, Remove-AzStorageQueue cmdlet 'ini kullanarak Bu alıştırmada kullanılan sıranın nasıl silineceğini gösterir.

```powershell
# Delete the queue
Remove-AzStorageQueue –Name $queueName –Context $ctx
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu alıştırmada oluşturduğunuz tüm varlıkları kaldırmak için kaynak grubunu kaldırın. Bu, ayrıca grubun içerdiği tüm kaynakları da siler. Bu durumda, oluşturulan depolama hesabı ve kaynak grubunun kendisi kaldırılır.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Sonraki adımlar

Bu nasıl yapılır makalesinde, aşağıdakiler dahil olmak üzere PowerShell ile temel sıra depolama yönetimi hakkında bilgi edindiniz:

> [!div class="checklist"]
>
> * Kuyruk oluştur
> * Kuyruğu alma
> * İleti ekleme
> * Sonraki iletiyi okuyun
> * İletiyi silme
> * Bir kuyruk silme

### <a name="microsoft-azure-powershell-storage-cmdlets"></a>Microsoft Azure PowerShell Storage cmdlet 'leri

* [Depolama PowerShell cmdlet’leri](/powershell/module/az.storage)

### <a name="microsoft-azure-storage-explorer"></a>Microsoft Azure Depolama Gezgini

* [Microsoft Azure Depolama Gezgini](../../vs-azure-tools-storage-manage-with-storage-explorer.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json), Microsoft’un Windows, macOS ve Linux üzerinde Azure Depolama verileriyle görsel olarak çalışmanızı sağlayan ücretsiz ve tek başına uygulamasıdır.
