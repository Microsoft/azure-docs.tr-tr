---
title: Yönetim adresleri
description: Bir App Service Ortamı denetlemek için kullanılan yönetim adreslerini bulun. Asimetrik yönlendirme sorunlarından kaçınmak için bunları bir yol tablosunda yapılandırmaktır.
author: ccompy
ms.assetid: a7738a24-89ef-43d3-bff1-77f43d5a3952
ms.topic: article
ms.date: 03/22/2021
ms.author: ccompy
ms.custom: seodec18, references_regions, devx-track-azurecli
ms.openlocfilehash: 796ee38140e72a56f1f22b0594dd904a43ac53c0
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107865234"
---
# <a name="app-service-environment-management-addresses"></a>Yönetim adreslerini App Service Ortamı

App Service Ortamı (ASE), Azure sanal ağınızda (VNet) çalışan Azure App Service tek bir kiracı dağıtımsıdır.  ASE, VNet 'iniz üzerinde çalışırken, hizmeti yönetmek için Azure App Service tarafından kullanılan bir dizi ayrılmış IP adreslerinden yine de erişilebilir olmalıdır.  Ao söz konusu olduğunda, yönetim trafiği Kullanıcı denetimli ağdan geçer. Bu trafik engellenmişse veya hatalı yönlendiriliyorsa Ao, askıya alınır. Ao ağ bağımlılıkları hakkında daha fazla bilgi için, bkz. [ağ konularını ve App Service ortamı][networking]okuyun. Ao hakkında genel bilgi için, [App Service ortamı giriş][intro]ile başlayabilirsiniz.

Tüm ASE 'lerin yönetim trafiğinin geldiği ortak bir VIP 'si vardır. Bu adreslerden gelen yönetim trafiği, Ao 'un genel VIP 'si üzerinde 454 ve 455 bağlantı noktalarına gönderilir. Bu belge, Ao 'ya yönetim trafiği için App Service kaynak adreslerini listeler. Bu adresler, AppServiceManagement adlı IP hizmeti etiketinde de bulunur.

Aşağıda belirtilen adresler, yönetim trafiğiyle asimetrik yönlendirme sorunlarını önlemek için bir yol tablosunda yapılandırılabilir. Yollar, IP düzeyindeki trafik üzerinde çalışır ve trafik yönündeki bir tanıma sahip değildir veya trafik TCP yanıt iletisinin bir parçası olur. Bir TCP isteğinin yanıt adresi, gönderildiği adresten farklıysa, asimetrik bir yönlendirme sorununa sahip olursunuz. Ao yönetim trafiğiyle asimetrik yönlendirme sorunlarından kaçınmak için yanıtların gönderildiği adresten geri gönderilmesini güvence altına almanız gerekir. ASE 'nizi şirket içinde giden trafiğin gönderildiği bir ortamda çalışacak şekilde yapılandırma hakkında ayrıntılı bilgi için, [Zorlamalı tünel Ile ASE 'Nizi yapılandırma][forcedtunnel] makalesini okuyun

## <a name="list-of-management-addresses"></a>Yönetim adresleri listesi ##

| Region | Adresler |
|--------|-----------|
| Tüm ortak bölgeler | 13.66.140.0, 13.67.8.128, 13.69.64.128, 13.69.227.128, 13.70.73.128, 13.71.170.64, 13.71.194.129, 13.75.34.192, 13.75.127.117, 13.77.50.128, 13.78.109.0, 13.89.171.0, 13.94.141.115, 13.94.143.126, 13.94.149.179, 20.36.106.128, 20.36.114.64, 20.37.74.128, 23.96.195.3, 23.102.135.246, 23.102.188.65, 40.69.106.128, 40.70.146.128, 40.71.13.64, 40.74.100.64, 40.78.194.128, 40.79.130.64, 40.79.178.128, 40.83.120.64, 40.83.121.56, 40.83.125.161, 40.112.242.192, 51.107.58.192, 51.107.154.192, 51.116.58.192, 51.116.155.0, 51.120.99.0, 51.120.219.0, 51.140.146.64, 51.140.210.128, 52.151.25.45, 52.162.106.192, 52.165.152.214, 52.165.153.122, 52.165.154.193, 52.165.158.140, 52.174.22.21, 52.178.177.147, 52.178.184.149, 52.178.190.65, 52.178.195.197, 52.187.56.50, 52.187.59.251, 52.187.63.19, 52.187.63.37, 52.224.105.172, 52.225.177.153, 52.231.18.64, 52.231.146.128, 65.52.172.237, 65.52.250.128, 70.37.57.58, 104.44.129.141, 104.44.129.243, 104.44.129.255, 104.44.134.255, 104.208.54.11, 104.211.81.64, 104.211.146.128, 157.55.208.185, 191.233.50.128, 191.233.203.64, 191.236.154.88 |
| Microsoft Azure Kamu | 23.97.29.209, 13.72.53.37, 13.72.180.105, 52.181.183.11, 52.227.80.100, 52.182.93.40, 52.244.79.34, 52.238.74.16 |
| Azure Çin | 42.159.4.236, 42.159.80.125 |

## <a name="configuring-a-network-security-group"></a>Ağ güvenlik grubu yapılandırma

Ağ güvenlik grupları ile, bireysel adresler veya kendi yapılandırmanızın saklanması konusunda endişelenmeniz gerekmez. AppServiceManagement adlı ve tüm adreslerle güncel tutulan bir IP hizmeti etiketi vardır. NSG 'unuzda bu IP hizmeti etiketini kullanmak için portala gidin, ağ güvenlik grupları kullanıcı arabirimini açın ve gelen güvenlik kuralları ' nı seçin. Gelen yönetim trafiği için önceden var olan bir kuralınız varsa, bunu düzenleyin. Bu NSG, Ao 'iyle oluşturulmadıysa veya tümü yeni ise **Ekle**' yi seçin. Kaynak açılan bölümünde **hizmet etiketi**' ni seçin.  Kaynak hizmet etiketi altında **Appservicemanagement**' ı seçin. Kaynak bağlantı noktası aralıklarını \* , hedef bağlantı noktası aralığını  **454-455**, **TCP** protokolüne ve **izin** verilecek eyleme ayarlayın. Kuralı yapıyorsanız önceliğini ayarlamanız gerekir. 

![hizmet etiketiyle NSG oluşturma][1]

## <a name="configuring-a-route-table"></a>Rota tablosu yapılandırma

Tüm gelen yönetim trafiğinin aynı yoldan geri gidebilmesini sağlamak için, yönetim adresleri bir sonraki Internet atlaması ile bir yol tablosuna yerleştirilebilir. Zorlamalı tünel yapılandırılırken bu yollar gereklidir. Yol tablosu oluşturmak için Portal, PowerShell veya Azure CLı kullanabilirsiniz.  Bir PowerShell isteminden Azure CLı kullanarak bir rota tablosu oluşturma komutları aşağıda verilmiştir. 

```azurepowershell-interactive
$rg = "resource group name"
$rt = "route table name"
$location = "azure location"
$managementAddresses = "13.66.140.0", "13.67.8.128", "13.69.64.128", "13.69.227.128", "13.70.73.128", "13.71.170.64", "13.71.194.129", "13.75.34.192", "13.75.127.117", "13.77.50.128", "13.78.109.0", "13.89.171.0", "13.94.141.115", "13.94.143.126", "13.94.149.179", "20.36.106.128", "20.36.114.64", "20.37.74.128", "23.96.195.3", "23.102.135.246", "23.102.188.65", "40.69.106.128", "40.70.146.128", "40.71.13.64", "40.74.100.64", "40.78.194.128", "40.79.130.64", "40.79.178.128", "40.83.120.64", "40.83.121.56", "40.83.125.161", "40.112.242.192", "51.107.58.192", "51.107.154.192", "51.116.58.192", "51.116.155.0", "51.120.99.0", "51.120.219.0", "51.140.146.64", "51.140.210.128", "52.151.25.45", "52.162.106.192", "52.165.152.214", "52.165.153.122", "52.165.154.193", "52.165.158.140", "52.174.22.21", "52.178.177.147", "52.178.184.149", "52.178.190.65", "52.178.195.197", "52.187.56.50", "52.187.59.251", "52.187.63.19", "52.187.63.37", "52.224.105.172", "52.225.177.153", "52.231.18.64", "52.231.146.128", "65.52.172.237", "65.52.250.128", "70.37.57.58", "104.44.129.141", "104.44.129.243", "104.44.129.255", "104.44.134.255", "104.208.54.11", "104.211.81.64", "104.211.146.128", "157.55.208.185", "191.233.50.128", "191.233.203.64", "191.236.154.88"

az network route-table create --name $rt --resource-group $rg --location $location
foreach ($ip in $managementAddresses) {
    az network route-table route create -g $rg --route-table-name $rt -n $ip --next-hop-type Internet --address-prefix ($ip + "/32")
}
```

Rota tablonuz oluşturulduktan sonra, Ao alt ağınızda ayarlamanız gerekir.  

## <a name="get-your-management-addresses-from-api"></a>API 'den yönetim adreslerinizi alın ##

Aşağıdaki API çağrısıyla, ATıCı ile eşleşen yönetim adreslerini listeleyebilirsiniz.

```http
get /subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Web/hostingEnvironments/<ASE Name>/inboundnetworkdependenciesendpoints?api-version=2016-09-01
```

API, ASE 'nizin tüm gelen adreslerini içeren bir JSON belgesi döndürür. Adres listesi, AX 'niz ve Ao alt ağı adres aralığının kendisi tarafından kullanılan VIP 'yi, yönetim adreslerini içerir.  

[Armistemcisiyle](https://github.com/projectkudu/ARMClient) API 'yi çağırmak için aşağıdaki komutları kullanın, ancak abonelik kimliğiniz, kaynak GRUBUNUZ ve Ao adı ' nı değiştirin.  

```azurepowershell-interactive
armclient login
armclient get /subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Web/hostingEnvironments/<ASE Name>/inboundnetworkdependenciesendpoints?api-version=2016-09-01
```

<!--IMAGES-->
[1]: ./media/management-addresses/managementaddr-nsg.png

<!-- LINKS -->
[networking]: ./network-info.md
[intro]: ./intro.md
[forcedtunnel]: ./forced-tunnel-support.md
