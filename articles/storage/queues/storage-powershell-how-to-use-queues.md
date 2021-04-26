---
title: PowerShell 'den Azure kuyruk depolama kullanma-Azure depolama
description: PowerShell aracılığıyla Azure kuyruk depolama üzerinde işlemler gerçekleştirin. Azure kuyruk depolama ile, HTTP/HTTPS tarafından erişilebilen çok sayıda ileti saklayabilirsiniz.
author: twooley
ms.author: twooley
ms.reviewer: dineshm
ms.date: 05/15/2019
ms.topic: how-to
ms.service: storage
ms.subservice: queues
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 1436114a070dff7120a7de10937cbfa58644d305
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106276255"
---
# <a name="how-to-use-azure-queue-storage-from-powershell"></a>PowerShell 'den Azure kuyruk depolama kullanma

Azure kuyruk depolama, dünyanın her yerinden HTTP veya HTTPS aracılığıyla erişilebilen çok sayıda iletiyi depolamaya yönelik bir hizmettir. Ayrıntılı bilgi için bkz. [Azure kuyruk depolama 'Ya giriş](storage-queues-introduction.md). Bu nasıl yapılır makalesi, genel kuyruk depolama işlemlerini içerir. Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:

> [!div class="checklist"]
>
> - Bir kuyruk oluşturma
> - Kuyruğu alma
> - İleti ekleme
> - İletiyi oku
> - İleti silme
> - Bir kuyruk silme

Bu nasıl yapılır Kılavuzu, Azure PowerShell ( `Az` ) modülü v 0.7 veya üstünü gerektirir. `Get-Module -ListAvailable Az`Yüklü olan sürümü bulmak için ' i çalıştırın. Yükseltmeniz gerekirse, bkz. [Azure PowerShell modülünü yükleme](/powershell/azure/install-az-ps).

Kuyruklar için veri düzlemi için PowerShell cmdlet 'leri yok. İleti ekleme, ileti okuma ve iletiyi silme gibi veri düzlemi işlemlerini gerçekleştirmek için, PowerShell 'de açığa çıkarılan .NET depolama istemci kitaplığını kullanmanız gerekir. Bir ileti nesnesi oluşturup, `AddMessage` Bu ileti üzerinde işlem gerçekleştirmek için gibi komutları kullanabilirsiniz. Bu makalede bunun nasıl yapılacağı gösterilmektedir.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Azure'da oturum açma

`Connect-AzAccount` komutuyla Azure aboneliğinizde oturum açın ve ekrandaki yönergeleri izleyin.

```powershell
Connect-AzAccount
```

## <a name="retrieve-list-of-locations"></a>Konumların listesini al

Kullanmak istediğiniz konumdan emin değilseniz, kullanılabilir konumları listeleyebilirsiniz. Liste görüntülendikten sonra, kullanmak istediğiniz öğeyi bulun. Bu alıştırma, kullanacaktır `eastus` . Bunu `location` daha sonra kullanmak üzere değişkende depolayın.

```powershell
Get-AzLocation | Select-Object Location
$location = "eastus"
```

## <a name="create-resource-group"></a>Kaynak grubu oluşturma

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) komutuyla bir kaynak grubu oluşturun.

Azure kaynak grubu, Azure kaynaklarının dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır. Kaynak grubu adını ileride kullanılmak üzere bir değişkende depolayın. Bu örnekte, `eastus`bölgesinde `howtoqueuesrg` adlı bir kaynak grubu oluşturulur.

```powershell
$resourceGroup = "howtoqueuesrg"
New-AzResourceGroup -ResourceGroupName $resourceGroup -Location $location
```

## <a name="create-storage-account"></a>Depolama hesabı oluştur

[New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount)kullanarak yerel olarak yedekli depolama (LRS) ile standart bir genel amaçlı depolama hesabı oluşturun. Kullanılacak depolama hesabını tanımlayan depolama hesabı bağlamını alın. Depolama hesabında bir işlem gerçekleştirirken, kimlik bilgilerini tekrar tekrar sağlamak yerine bağlama başvurursunuz.

```powershell
$storageAccountName = "howtoqueuestorage"
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -Location $location `
  -SkuName Standard_LRS

$ctx = $storageAccount.Context
```

## <a name="create-a-queue"></a>Bir kuyruk oluşturma

Aşağıdaki örnek, depolama hesabı adını ve erişim anahtarını içeren depolama hesabı bağlamını kullanarak Azure depolama 'ya bir bağlantı kurar. Ardından, adlı bir kuyruk oluşturmak için [New-AzStorageQueue](/powershell/module/az.storage/new-azstoragequeue) cmdlet 'ini çağırır `howtoqueue` .

```powershell
$queueName = "howtoqueue"
$queue = New-AzStorageQueue –Name $queueName -Context $ctx
```

Azure kuyruk depolama için adlandırma kuralları hakkında daha fazla bilgi için bkz. [adlandırma sıraları ve meta verileri](/rest/api/storageservices/naming-queues-and-metadata).

## <a name="retrieve-a-queue"></a>Kuyruğu alma

Belirli bir kuyruğu sorgulayabilir ve alabilir ve bir depolama hesabındaki tüm kuyrukların listesini alabilirsiniz. Aşağıdaki örneklerde, depolama hesabındaki tüm sıraların ve belirli bir sıranın nasıl alınacağını gösterilmektedir. Her iki komut de [Get-AzStorageQueue](/powershell/module/az.storage/get-azstoragequeue) cmdlet 'ini kullanır.

```powershell
# Retrieve a specific queue
$queue = Get-AzStorageQueue –Name $queueName –Context $ctx
# Show the properties of the queue
$queue

# Retrieve all queues and show their names
Get-AzStorageQueue -Context $ctx | Select-Object Name
```

## <a name="add-a-message-to-a-queue"></a>Kuyruğa ileti ekleme

Kuyruktaki gerçek iletileri etkileyen işlemler, PowerShell 'de kullanıma sunulan .NET depolama istemci kitaplığını kullanır. Bir kuyruğa ileti eklemek için ileti nesnesinin, sınıfının yeni bir örneğini oluşturun [`Microsoft.Azure.Storage.Queue.CloudQueueMessage`](/java/api/com.microsoft.azure.storage.queue.cloudqueuemessage) . Sonra, yöntemini çağırın [`AddMessage`](/java/api/com.microsoft.azure.storage.queue.cloudqueue.addmessage) . Bir `CloudQueueMessage` dizeden (UTF-8 biçiminde) ya da bir bayt dizisinden oluşturulabilir.

Aşağıdaki örnek, kuyruğunuza nasıl bir ileti ekleneceğini gösterir.

```powershell
# Create a new message using a constructor of the CloudQueueMessage class
$queueMessage = [Microsoft.Azure.Storage.Queue.CloudQueueMessage]::new("This is message 1")

# Add a new message to the queue
$queue.CloudQueue.AddMessageAsync($QueueMessage)

# Add two more messages to the queue
$queueMessage = [Microsoft.Azure.Storage.Queue.CloudQueueMessage]::new("This is message 2")
$queue.CloudQueue.AddMessageAsync($QueueMessage)

$queueMessage = [Microsoft.Azure.Storage.Queue.CloudQueueMessage]::new("This is message 3")
$queue.CloudQueue.AddMessageAsync($QueueMessage)
```

[Azure Depolama Gezgini](https://storageexplorer.com)kullanıyorsanız, Azure hesabınıza bağlanabilir ve depolama hesabındaki kuyrukları görüntüleyebilir ve sıradaki iletileri görüntülemek için bir kuyruğun detayına gidebilirsiniz.

## <a name="read-a-message-from-the-queue-then-delete-it"></a>Kuyruktaki bir iletiyi okuyun ve silin

İletiler en iyi deneme-ilk çıkar sırasına göre okundu. Bu garanti edilmez. İletiyi kuyruktan okuduğunuzda, kuyruğa bakıyor diğer tüm işlemlerin görünmez hale gelir. Bu, bir donanım veya yazılım arızası nedeniyle kodunuzun iletiyi işleyememesi durumunda, kodunuzun başka bir örneğinin aynı mesajı almasını ve yeniden denemesini sağlar.

Bu **geçersiz zaman aşımı** , iletinin işlenmek üzere yeniden kullanılabilmesi için önce ne kadar süreyle görünmez kalabileceğini tanımlar. Varsayılan değer 30 saniyedir.

Kodunuz, sıradaki bir iletiyi iki adımda okur. Yöntemini çağırdığınızda sıradaki bir [`Microsoft.Azure.Storage.Queue.CloudQueue.GetMessage`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessage) sonraki iletiyi alırsınız. Öğesinden döndürülen bir ileti, `GetMessage` Bu kuyruktan gelen diğer kod okuma iletileri için görünmez hale gelir. İletiyi kuyruktan kaldırmayı tamamlaması için [`Microsoft.Azure.Storage.Queue.CloudQueue.DeleteMessage`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.deletemessage) yöntemini çağırın.

Aşağıdaki örnekte, üç kuyruk iletisini okuyun ve ardından 10 saniye (geçersiz zaman aşımı) bekleyin. Daha sonra, bu üç iletiyi yeniden okuyarak iletileri çağırarak iletileri silin `DeleteMessage` . İletiler silindikten sonra kuyruğu okumaya çalışırsanız, `$queueMessage` olarak döndürülür `$null` .

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

Bir kuyruğu ve içerdiği tüm iletileri silmek için `Remove-AzStorageQueue` cmdlet 'ini çağırın. Aşağıdaki örnek, cmdlet 'ini kullanarak Bu alıştırmada kullanılan sıranın nasıl silineceğini gösterir `Remove-AzStorageQueue` .

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
> - Bir kuyruk oluşturma
> - Kuyruğu alma
> - İleti ekleme
> - Sonraki iletiyi okuyun
> - İleti silme
> - Bir kuyruk silme

### <a name="microsoft-azure-powershell-storage-cmdlets"></a>Microsoft Azure PowerShell Storage cmdlet 'leri

- [Depolama PowerShell cmdlet’leri](/powershell/module/az.storage)

### <a name="microsoft-azure-storage-explorer"></a>Microsoft Azure Depolama Gezgini

- [Microsoft Azure Depolama Gezgini](../../vs-azure-tools-storage-manage-with-storage-explorer.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json), Microsoft’un Windows, macOS ve Linux üzerinde Azure Depolama verileriyle görsel olarak çalışmanızı sağlayan ücretsiz ve tek başına uygulamasıdır.
