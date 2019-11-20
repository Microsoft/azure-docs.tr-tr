---
title: PowerShell kullanarak Notification Hubs’ı Dağıtma ve Yönetme
description: Otomasyon için PowerShell kullanarak Notification Hubs oluşturma ve yönetme
services: notification-hubs
documentationcenter: ''
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 7c58f2c8-0399-42bc-9e1e-a7f073426451
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: powershell
ms.devlang: na
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 5af920249000cabbc63f0c9ab453738450875172
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/24/2019
ms.locfileid: "71213407"
---
# <a name="deploy-and-manage-notification-hubs-using-powershell"></a>PowerShell kullanarak Bildirim Hub 'larını dağıtma ve yönetme

## <a name="overview"></a>Genel Bakış

Bu makalede, PowerShell kullanarak Azure Notification Hubs oluşturma ve yönetme işlemlerinin nasıl kullanılacağı gösterilmektedir. Aşağıdaki genel otomasyon görevleri bu makalede gösterilmiştir.

- Bildirim Hub 'ı oluşturma
- Kimlik bilgilerini ayarla

Ayrıca, Bildirim Hub 'larınız için yeni bir Service Bus ad alanı oluşturmanız gerekiyorsa bkz. [PowerShell ile Service Bus yönetme](../service-bus-messaging/service-bus-powershell-how-to-provision.md).

Bildirim Hub 'Larının yönetimi, Azure PowerShell eklenen cmdlet 'ler tarafından doğrudan desteklenmez. PowerShell 'den en iyi yaklaşım, Microsoft. Azure. Notificationhub. dll derlemesine başvurmalıdır. Derleme, [Microsoft Azure Notification Hubs NuGet paketiyle](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)dağıtılır.

## <a name="prerequisites"></a>Önkoşullar

- Azure aboneliği. Azure abonelik tabanlı bir platformdur. Bir aboneliği edinme hakkında daha fazla bilgi için bkz. [satın alma seçenekleri], [Üye teklifleri], veya [ücretsiz deneme].
- Azure PowerShell olan bir bilgisayar. Yönergeler için bkz. [için Notification Hubs .NET API].
- PowerShell betikleri, NuGet paketleri ve .NET Framework genel olarak anlaşılmasıdır.

## <a name="including-a-reference-to-the-net-assembly-for-service-bus"></a>Service Bus için .NET derlemesine başvuru ekleme

Azure Notification Hubs yönetimi, Azure PowerShell PowerShell cmdlet 'lerine henüz eklenmemiştir. Notification Hub 'larını sağlamak için [Microsoft Azure Notification Hubs NuGet paketinde](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)sunulan .net istemcisini kullanabilirsiniz.

İlk olarak, betiğinizin bir Visual Studio projesinde NuGet paketi olarak yüklenen **Microsoft. Azure. Notificationhub. dll** derlemesini bulabileceği konusunda emin olun. Bu komut, esnek olması için aşağıdaki adımları gerçekleştirir:

1. Çağrıldığı yolu belirler.
2. Adlı `packages`bir klasör bulana kadar yolu geçer. Visual Studio projeleri için NuGet paketlerini yüklediğinizde bu klasör oluşturulur.
3. `packages` Adlı`Microsoft.Azure.NotificationHubs.dll`bir derlemenin klasörünü yinelemeli olarak arar.
4. Türlerin daha sonra kullanılmak üzere kullanılabilmesi için derlemeye başvurur.

Bu adımların bir PowerShell betiğine uygulanması aşağıda verilmiştir:

``` powershell
try
{
    # WARNING: Make sure to reference the latest version of Microsoft.Azure.NotificationHubs.dll
    Write-Output "Adding the [Microsoft.Azure.NotificationHubs.dll] assembly to the script..."
    $scriptPath = Split-Path (Get-Variable MyInvocation -Scope 0).Value.MyCommand.Path
    $packagesFolder = (Split-Path $scriptPath -Parent) + "\packages"
    $assembly = Get-ChildItem $packagesFolder -Include "Microsoft.Azure.NotificationHubs.dll" -Recurse
    Add-Type -Path $assembly.FullName

    Write-Output "The [Microsoft.Azure.NotificationHubs.dll] assembly has been successfully added to the script."
}

catch [System.Exception]
{
    Write-Error("Could not add the Microsoft.Azure.NotificationHubs.dll assembly to the script. Make sure you build the solution before running the provisioning script.")
}
```

## <a name="create-the-namespacemanager-class"></a>`NamespaceManager` Sınıfı oluşturma

Notification Hubs sağlamak için SDK 'dan [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager?view=azure-dotnet) sınıfının bir örneğini oluşturun.

Bağlantı dizesi sağlamak için kullanılan bir yetkilendirme kuralı almak için Azure PowerShell birlikte bulunan [Get-AzureSBAuthorizationRule] cmdlet 'ini kullanabilirsiniz. `NamespaceManager` Örneğe bir başvuru, `$NamespaceManager` değişkeninde depolanır. `$NamespaceManager`bir Bildirim Hub 'ı sağlamak için kullanılır.

``` powershell
$sbr = Get-AzureSBAuthorizationRule -Namespace $Namespace
# Create the NamespaceManager object to create the hub
Write-Output "Creating a NamespaceManager object for the [$Namespace] namespace..."
$NamespaceManager=[Microsoft.Azure.NotificationHubs.NamespaceManager]::CreateFromConnectionString($sbr.ConnectionString);
Write-Output "NamespaceManager object for the [$Namespace] namespace has been successfully created."
```

## <a name="provisioning-a-new-notification-hub"></a>Yeni bir Bildirim Hub 'ı sağlama

Yeni bir Bildirim Hub 'ı sağlamak için [Notification Hubs için .NET API]'sini kullanın.

Betiğinin bu bölümünde dört yerel değişken ayarlarsınız.

1. `$Namespace`: Bunu, Bildirim Hub 'ı oluşturmak istediğiniz ad alanı adına ayarlayın.
2. `$Path`: Bu yolu, yeni Bildirim Hub 'ının adına ayarlayın.  Örneğin, "MyHub".
3. `$WnsPackageSid`: Bunu Windows [Geliştirme Merkezi](https://developer.microsoft.com/en-us/windows)' nden Windows uygulamanız IÇIN paket SID 'sine ayarlayın.
4. `$WnsSecretkey`: Windows [Geliştirme Merkezi](https://developer.microsoft.com/en-us/windows)' nden Windows uygulamanızın gizli anahtarı olarak ayarlayın.

Bu değişkenler, ad alanınızı bağlamak ve Windows uygulaması için WNS kimlik bilgileriyle Windows Bildirim Hizmetleri (WNS) bildirimlerini işlemek üzere yapılandırılmış yeni bir Bildirim Hub 'ı oluşturmak için kullanılır. Paket SID 'SI ve gizli anahtarı alma hakkında daha fazla bilgi için, [Notification Hubs](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) kullanmaya başlama öğreticisine bakın.

- Betik parçacığı, Bildirim Hub `NamespaceManager` 'ının `$Path` varolup olmadığını kontrol etmek için nesnesini kullanır.
- Yoksa, komut dosyası WNS kimlik bilgileriyle oluşturulur `NotificationHubDescription` ve bunu `NamespaceManager` sınıf `CreateNotificationHub` yöntemine geçirir.

``` powershell
$Namespace = "<Enter your namespace>"
$Path  = "<Enter a name for your notification hub>"
$WnsPackageSid = "<your package sid>"
$WnsSecretkey = "<enter your secret key>"

$WnsCredential = New-Object -TypeName Microsoft.Azure.NotificationHubs.WnsCredential -ArgumentList $WnsPackageSid,$WnsSecretkey

# Query the namespace
$CurrentNamespace = Get-AzureSBNamespace -Name $Namespace

# Check if the namespace already exists
if ($CurrentNamespace)
{
    Write-Output "The namespace [$Namespace] in the [$($CurrentNamespace.Region)] region was found."

    # Create the NamespaceManager object used to create a new notification hub
    $sbr = Get-AzureSBAuthorizationRule -Namespace $Namespace
    Write-Output "Creating a NamespaceManager object for the [$Namespace] namespace..."
    $NamespaceManager = [Microsoft.Azure.NotificationHubs.NamespaceManager]::CreateFromConnectionString($sbr.ConnectionString);
    Write-Output "NamespaceManager object for the [$Namespace] namespace has been successfully created."

    # Check to see if the Notification Hub already exists
    if ($NamespaceManager.NotificationHubExists($Path))
    {
        Write-Output "The [$Path] notification hub already exists in the [$Namespace] namespace."  
    }
    else
    {
        Write-Output "Creating the [$Path] notification hub in the [$Namespace] namespace."
        $NHDescription = New-Object -TypeName Microsoft.Azure.NotificationHubs.NotificationHubDescription -ArgumentList $Path;
        $NHDescription.WnsCredential = $WnsCredential;
        $NamespaceManager.CreateNotificationHub($NHDescription);
        Write-Output "The [$Path] notification hub was created in the [$Namespace] namespace."
    }
}
else
{
    Write-Host "The [$Namespace] namespace does not exist."
}
```

## <a name="additional-resources"></a>Ek Kaynaklar

- [Service Bus’ı PowerShell ile yönetme](../service-bus-messaging/service-bus-powershell-how-to-provision.md)
- [PowerShell betiği kullanarak Service Bus kuyrukları, konuları ve abonelikleri oluşturma](https://blogs.msdn.com/b/paolos/archive/2014/12/02/how-to-create-a-service-bus-queues-topics-and-subscriptions-using-a-powershell-script.aspx)
- [PowerShell betiği kullanarak Service Bus ad alanı ve Olay Hub 'ı oluşturma](https://blogs.msdn.com/b/paolos/archive/2014/12/01/how-to-create-a-service-bus-namespace-and-an-event-hub-using-a-powershell-script.aspx)

Kullanıma sunulan bazı betikler de indirilebilir:

- [Service Bus PowerShell betikleri](https://code.msdn.microsoft.com/windowsazure/Service-Bus-PowerShell-a46b7059)

[Satın alma seçenekleri]: https://azure.microsoft.com/pricing/purchase-options/
[Üye teklifleri]: https://azure.microsoft.com/pricing/member-offers/
[Ücretsiz deneme]: https://azure.microsoft.com/pricing/free-trial/
[için Notification Hubs .NET API]: /powershell/azureps-cmdlets-docs
[Notification Hubs için .NET API]: https://docs.microsoft.com/dotnet/api/overview/azure/notification-hubs?view=azure-dotnet
[Get-AzureSBNamespace]: https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azuresbnamespace
[New-AzureSBNamespace]: https://docs.microsoft.com/powershell/module/servicemanagement/azure/new-azuresbnamespace
[Get-AzureSBAuthorizationRule]: https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azuresbauthorizationrule
