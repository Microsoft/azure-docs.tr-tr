---
title: Azure Cloud Shell Genel Bakış | Microsoft Docs
description: Azure Cloud Shell Genel Bakış.
services: ''
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 09/03/2019
ms.author: damaerte
ms.openlocfilehash: acdf52022d8498a186ed4cff90284cd6e299d6c0
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70240853"
---
# <a name="overview-of-azure-cloud-shell"></a>Azure Cloud Shell Genel Bakış
Azure Cloud Shell, Azure kaynaklarını yönetmeye yönelik etkileşimli, kimliği doğrulanmış, tarayıcıda erişilebilen bir kabuktur.
Bu, bash veya PowerShell gibi çalıştığınız yönteme en uygun kabuk deneyimini seçme esnekliğini sağlar.

Aşağıya tıklayarak shell.azure.com 'tan deneyin.

[![Ekleme başlatma](https://shell.azure.com/images/launchcloudshell.png "Azure Cloud Shell Başlat")](https://shell.azure.com)

Cloud Shell simgesini kullanarak Azure portal deneyin.

![Portal başlatma](media/overview/portal-launch-icon.png)

## <a name="features"></a>Özellikler

### <a name="browser-based-shell-experience"></a>Tarayıcı tabanlı kabuk deneyimi
Cloud Shell, Azure yönetim görevleriyle birlikte oluşturulan tarayıcı tabanlı bir komut satırı deneyimine erişim sağlar.
Cloud Shell, yerel bir makineden yalnızca bulutun sağlayabilebir şekilde Untethered işe faydalanır.

### <a name="choice-of-preferred-shell-experience"></a>Tercih edilen kabuk deneyimi seçimi
Kullanıcılar kabuk açılan menüsünden bash veya PowerShell arasında seçim yapabilir.

![Bash Cloud Shell](media/overview/overview-bash-pic.png)

![Cloud Shell’de PowerShell](media/overview/overview-ps-pic.png)

### <a name="authenticated-and-configured-azure-workstation"></a>Kimliği doğrulanmış ve yapılandırılmış Azure iş istasyonu
Cloud Shell, Microsoft tarafından yönetiliyor ve bu nedenle popüler komut satırı araçları ve dil desteğiyle birlikte geldi. Cloud Shell Ayrıca, Azure CLı veya Azure PowerShell cmdlet 'leri aracılığıyla kaynaklarınıza anında erişim için otomatik olarak kimlik doğrulaması gerçekleştirir.

[Cloud Shell yüklü araçların tam listesini görüntüleyin.](features.md#tools)

### <a name="integrated-cloud-shell-editor"></a>Tümleşik Cloud Shell Düzenleyicisi
Cloud Shell, açık kaynak Monako düzenleyicisine bağlı olarak tümleşik bir grafik metin düzenleyicisi sunar. Azure CLI veya Azure PowerShell ile sorunsuz dağıtım için `code .` çalıştırarak yapılandırma dosyalarını oluşturmanız ve düzenlemeniz yeterlidir.

[Cloud Shell Düzenleyicisi hakkında daha fazla bilgi edinin](using-cloud-shell-editor.md).

### <a name="integrated-with-docsmicrosoftcom"></a>Docs.microsoft.com ile tümleşik

[Docs.Microsoft.com](https://docs.microsoft.com)üzerinde barındırılan belgelerden doğrudan Cloud Shell kullanabilirsiniz. [Microsoft Learn](https://docs.microsoft.com/learn/), [Azure POWERSHELL](https://docs.microsoft.com/powershell/azure/overview) ve [Azure CLI belgelerinde](https://docs.microsoft.com/cli/azure) tümleşiktir-tam kapsamlı kabuk deneyimini açmak için bir kod parçacığında "dene" düğmesine tıklayın. 

### <a name="multiple-access-points"></a>Birden çok erişim noktası
Cloud Shell, tarafından kullanılabilen esnek bir araçtır:
* [portal.azure.com](https://portal.azure.com)
* [shell.azure.com](https://shell.azure.com)
* [Azure CLı belgeleri](https://docs.microsoft.com/cli/azure)
* [Azure PowerShell belgeleri](https://docs.microsoft.com/powershell/azure/overview)
* [Azure mobil uygulaması](https://azure.microsoft.com/features/azure-portal/mobile-app/)
* [Visual Studio Code Azure Hesap uzantısı](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)

### <a name="connect-your-microsoft-azure-files-storage"></a>Microsoft Azure dosyaları depolama alanınızı bağlama
Cloud Shell makineler geçicidir, ancak dosyalarınız iki şekilde kalıcı hale getirilir: bir disk görüntüsü ile ve adlı `clouddrive`bağlı dosya paylaşımından.  İlk başlatma sırasında, sizin adınıza bir kaynak grubu, depolama hesabı ve Azure dosya paylaşımının oluşturulmasını ister Cloud Shell. Bu bir kerelik bir adımdır ve tüm oturumlar için otomatik olarak eklenir. Tek bir dosya paylaşma eşleştirilebilir ve Cloud Shell hem Bash hem de PowerShell tarafından kullanılır.

[Yeni veya mevcut bir depolama hesabını](persisting-shell-storage.md) nasıl bağlayacağınızı veya [Cloud Shell ' de kullanılan Kalıcılık mekanizmaları](persisting-shell-storage.md#how-cloud-shell-storage-works)hakkında bilgi edinmek için daha fazla bilgi edinin.

## <a name="concepts"></a>Kavramlar
* Cloud Shell, oturum başına ve kullanıcı bazında belirtilen geçici bir konakta çalıştırılır
* Etkileşimli etkinlik olmadan 20 dakika sonra zaman aşımı Cloud Shell
* Cloud Shell, bir Azure dosya paylaşımının bağlanmasını gerektirir
* Cloud Shell hem Bash hem de PowerShell için aynı Azure dosya paylaşımının kullanır
* Cloud Shell Kullanıcı hesabı başına bir makine atanır
* Cloud Shell dosya paylaşımınızda bulunan 5 GB bir görüntü kullanarak $HOME devam ediyor
* İzinler Bash 'te normal bir Linux kullanıcısı olarak ayarlanır

Cloud Shell ve PowerShell 'deki [Bash](features.md) özellikleri hakkında daha fazla bilgi edinin [Cloud Shell](features-powershell.md).

## <a name="pricing"></a>Fiyatlandırma
Cloud Shell barındıran makine, bağlı bir Azure dosyaları paylaşımının önkoşul olarak ücretsizdir. Normal depolama ücretleri uygulanır.

## <a name="next-steps"></a>Sonraki adımlar
[Bash Cloud Shell hızlı başlangıç](quickstart.md) <br>
[PowerShell Cloud Shell hızlı başlangıç](quickstart-powershell.md)
