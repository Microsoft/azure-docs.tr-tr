---
title: include dosyası
description: include dosyası
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/08/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 11d2172d085fe9b47587f4084908f99d7b54437e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92103246"
---
Azure portal klasik VNET oluştururken, görüntülediğiniz ad PowerShell için kullandığınız tam ad değildir. Örneğin, portalda **TestVNet1** adında görünen bir VNet, ağ yapılandırma dosyasında çok daha uzun bir ada sahip olabilir. "ClassicRG" adlı kaynak grubundaki bir VNet için şu şekilde görünebilir: **Grup Classicrg TestVNet1**. Bağlantılarınızı oluştururken, ağ yapılandırma dosyasında gördüğünüz değerlerin kullanılması önemlidir.

Aşağıdaki adımlarda, Azure hesabınıza bağlanır ve bağlantılarınız için gerekli olan değerleri almak için ağ yapılandırma dosyasını indirmeniz ve görüntülemeniz gerekir.

1. Azure hizmet yönetimi (SM) PowerShell cmdlet 'lerinin en son sürümünü indirip yükleyin. Çoğu kişi yerel olarak Kaynak Yöneticisi modüller, ancak hizmet yönetimi modüllerine sahip değildir. Hizmet yönetimi modülleri eski ve ayrıca yüklü olmalıdır. Daha fazla bilgi için bkz. [hizmet yönetimi cmdlet 'Lerini yükler](/powershell/azure/servicemanagement/install-azure-ps).

1. PowerShell konsolunuzu yükseltilmiş haklarla açın ve hesabınıza bağlanın. Bağlanmanıza yardımcı olması için aşağıdaki örnekleri kullanın. PowerShell hizmeti yönetim modülünü kullanarak bu komutları yerel olarak çalıştırmanız gerekir. Hesabınıza bağlanın. Bağlanmanıza yardımcı olması için aşağıdaki örneği kullanın:

   ```powershell
   Add-AzureAccount
   ```
1. Hesapla ilişkili abonelikleri kontrol edin.

   ```powershell
   Get-AzureSubscription
   ```
1. Birden fazla aboneliğiniz varsa, kullanmak istediğiniz aboneliği seçin.

   ```powershell
   Select-AzureSubscription -SubscriptionId "Replace_with_your_subscription_ID"
   ```
1. Bilgisayarınızda bir dizin oluşturun. Örneğin, C:\AzureVNet
1. Ağ yapılandırma dosyasını dizine aktarın. Bu örnekte, ağ yapılandırma dosyası **C:\azurenet dizinine** aktarılmalıdır.

   ```powershell
   Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
   ```
1. Dosyayı bir metin düzenleyici ile açın ve sanal ağlarınız ve sitelerinizin adlarını görüntüleyin. Bu adlar, bağlantılarınızı oluştururken kullandığınız adlar olacaktır.<br>**VNET** adları **virtualnetworksite Name =** olarak listelenir.<br>**Site** adları **LocalNetworkSiteRef Name =** olarak listelenir