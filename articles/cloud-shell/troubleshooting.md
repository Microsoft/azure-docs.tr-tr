---
title: Azure Cloud Shell sorunlarını giderme | Microsoft Docs
description: Sorun giderme Azure Cloud Shell
services: azure
documentationcenter: ''
author: maertendMSFT
manager: hemantm
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/24/2018
ms.author: damaerte
ms.openlocfilehash: eea64520dd5440467c911b6de42d8c8c31fc1bde
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "87543461"
---
# <a name="troubleshooting--limitations-of-azure-cloud-shell"></a>Azure Cloud Shell & kısıtlamaları sorunlarını giderme

Azure Cloud Shell sorunları gidermeye yönelik bilinen çözümler şunlardır:

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="general-troubleshooting"></a>Genel sorun giderme

### <a name="error-running-azuread-cmdlets-in-powershell"></a>PowerShell 'de AzureAD cmdlet 'leri çalıştırma hatası

- **Ayrıntılar**: Cloud Shell gibi azuread cmdlet 'lerini çalıştırdığınızda `Get-AzureADUser` bir hata görebilirsiniz: `You must call the Connect-AzureAD cmdlet before calling any other cmdlets` . 
- **Çözüm**: `Connect-AzureAD` cmdlet 'ini çalıştırın. Daha önce, Cloud Shell PowerShell başlangıcında bu cmdlet 'i otomatik olarak çalıştırdık. Başlangıç süresini hızlandırmak için cmdlet artık otomatik olarak çalışmaz. PowerShell 'deki $PROFILE dosyasına ekleyerek önceki davranışı geri yüklemeyi tercih edebilirsiniz `Connect-AzureAD` .

### <a name="early-timeouts-in-firefox"></a>FireFox 'ta erken zaman aşımları

- **Ayrıntılar**: Cloud Shell, tarayıcıya giriş/çıkış geçirmek için açık bir WebSocket kullanır. FireFox, Cloud Shell erken zaman aşımlarını erken bir şekilde kapatan WebSocket 'yi kapatan önceden ayarlanmış ilkelere sahiptir.
- **Çözüm**: Firefox 'u açın ve URL kutusunda "about: config" adresine gidin. "Network. WebSocket. Timeout. PING. Request" araması yapın ve değeri 0 ' dan 10 ' a değiştirin.

### <a name="disabling-cloud-shell-in-a-locked-down-network-environment"></a>Kilitli ağ ortamında Cloud Shell devre dışı bırakma

- **Ayrıntılar**: Yöneticiler, kullanıcıları için Cloud Shell erişimini devre dışı bırakmak isteyebilir. Cloud Shell, `ux.console.azure.com` etki alanına erişimi (portal.Azure.com, Shell.Azure.com, Visual Studio Code Azure Hesap uzantısı ve docs.Microsoft.com dahil Cloud Shell giriş noktalarına erişimi durdurabilen) kullanır. ABD kamu bulutunda giriş noktası, `ux.console.azure.us` karşılık gelen bir Shell.Azure.us yoktur.
- **Çözüm**: `ux.console.azure.com` ağa erişimi veya `ux.console.azure.us` ağ ayarlarına erişimi ortamınıza sınırlayın. Cloud Shell simgesi yine Azure portal var, ancak hizmete başarıyla bağlanmayacak.

### <a name="storage-dialog---error-403-requestdisallowedbypolicy"></a>Depolama Iletişim kutusu-hata: 403 RequestDisallowedByPolicy

- **Ayrıntılar**: Cloud Shell aracılığıyla bir depolama hesabı oluştururken, yöneticiniz tarafından verilen bir Azure ilke ataması nedeniyle başarısız olur. Hata iletisi şunları içerir: `The resource action 'Microsoft.Storage/storageAccounts/write' is disallowed by one or more policies.`
- **Çözüm**: depolama oluşturmayı reddetmekten Azure ilke atamasını kaldırmak veya güncelleştirmek için Azure yöneticinize başvurun.

### <a name="storage-dialog---error-400-disallowedoperation"></a>Depolama Iletişim kutusu-hata: 400 DisallowedOperation

- **Ayrıntılar**: Azure Active Directory aboneliği kullanılırken, depolama alanı oluşturamazsınız.
- **Çözüm**: depolama kaynakları oluşturma yeteneğine sahip bir Azure aboneliği kullanın. Azure AD abonelikleri Azure kaynakları oluşturamaz.

### <a name="terminal-output---error-failed-to-connect-terminal-websocket-cannot-be-established-press-enter-to-reconnect"></a>Terminal çıkışı-hata: terminalin bağlantısı kurulamadı: WebSocket oluşturulamıyor. `Enter`Yeniden bağlanmak için basın.
- **Ayrıntılar**: Cloud Shell Cloud Shell altyapısına WebSocket bağlantısı kurma olanağı gerektirir.
- **Çözüm**: *. Console.Azure.com adresindeki etki alanlarına https istekleri ve WebSocket istekleri göndermeyi etkinleştirmek için ağ ayarlarınızı yapılandırdığınızdan emin olun.

### <a name="set-your-cloud-shell-connection-to-support-using-tls-12"></a>Cloud Shell bağlantınızı TLS 1,2 ile desteklemek üzere ayarlama
 - **Ayrıntılar**: Cloud Shell BAĞLANTıNıZ için TLS sürümünü tanımlamak üzere tarayıcıya özgü ayarları ayarlamanız gerekir.
 - **Çözüm**: tarayıcınızın güvenlik ayarlarına gidin ve "TLS 1,2 kullan" seçeneğinin yanındaki onay kutusunu işaretleyin.

## <a name="bash-troubleshooting"></a>Bash sorunlarını giderme

### <a name="cannot-run-the-docker-daemon"></a>Docker Daemon çalıştırılamıyor

- **Ayrıntılar**: Cloud Shell, arka plan programının çalıştırılmasına izin verilmedikçe kabuk ortamınızı barındırmak için bir kapsayıcıyı kullanır.
- **Çözüm**: uzak bir Docker konağından Docker kapsayıcılarını yönetmek için varsayılan olarak yüklenen [Docker-Machine](https://docs.docker.com/machine/overview/)' i kullanın.

## <a name="powershell-troubleshooting"></a>PowerShell sorunlarını giderme

### <a name="gui-applications-are-not-supported"></a>GUI uygulamaları desteklenmez

- **Ayrıntılar**: BIR kullanıcı GUI uygulamasını başlattığında, istem döndürmez. Örneğin, biri iki öğeli kimlik doğrulaması etkin olan özel bir GitHub deposunu kopyalamışsa, iki öğeli kimlik doğrulamasının tamamlanmasıyla ilgili bir iletişim kutusu görüntülenir.
- **Çözüm**: kabuğu kapatın ve yeniden açın.

### <a name="troubleshooting-remote-management-of-azure-vms"></a>Azure VM 'lerinin uzaktan yönetiminde sorun giderme
> [!NOTE]
> Azure VM 'lerinin herkese açık bir IP adresi olmalıdır.

- **Ayrıntılar**: WinRM Için varsayılan Windows Güvenlik Duvarı ayarları nedeniyle, Kullanıcı aşağıdaki hatayı görebilir: `Ensure the WinRM service is running. Remote Desktop into the VM for the first time and ensure it can be discovered.`
- **Çözüm**: `Enable-AzVMPSRemoting` hedef makinede PowerShell uzaktan iletişim özelliklerinin tüm yönlerini etkinleştirmek için ' i çalıştırın.

### <a name="dir-does-not-update-the-result-in-azure-drive"></a>`dir` Azure Drive 'daki sonucu güncelleştirmez

- **Ayrıntılar**: varsayılan olarak, Kullanıcı deneyimini iyileştirmek için, sonuçları `dir` Azure sürücüde önbelleğe alınır.
- **Çözüm**: bir Azure kaynağını oluşturduktan, güncelleştirdikten veya kaldırdıktan sonra, `dir -force` Azure sürücüsündeki sonuçları güncelleştirmek için ' i çalıştırın.

## <a name="general-limitations"></a>Genel sınırlamalar

Azure Cloud Shell aşağıdaki bilinen sınırlamalara sahiptir:

### <a name="quota-limitations"></a>Kota sınırlamaları

Azure Cloud Shell bölge başına her kiracı için 20 eşzamanlı kullanıcı sınırı vardır. Sınırdan daha fazla eşzamanlı oturum açmaya çalışırsanız, "Kota üzerinde Kiracı Kullanıcı" hatası görüntülenir. Bundan daha fazla oturum açmanız gerekiyorsa (örneğin eğitim oturumları için), bir kota artışı istemek için beklenen kullanımınızın üzerinde desteğe başvurun.

Cloud Shell ücretsiz bir hizmet olarak sağlanır ve genel amaçlı bilgi işlem platformu olarak değil, Azure ortamınızı yapılandırmak için kullanılmak üzere tasarlanmıştır. Aşırı sayıda otomatik kullanım, Azure hizmet koşulları 'nda ihlal edildiğinde düşünülebilir ve erişimin engellenmesi Cloud Shell neden olabilir.

### <a name="system-state-and-persistence"></a>Sistem durumu ve kalıcılık

Cloud Shell oturumunuzu sağlayan makine geçicidir ve oturumunuz 20 dakika etkin kaldıktan sonra geri dönüştürülür. Cloud Shell, bir Azure dosya paylaşımının bağlanmasını gerektirir. Sonuç olarak, aboneliğiniz Cloud Shell erişmek için depolama kaynaklarını ayarlayabilmelidir. Diğer konular şunlardır:

- Bağlı depolama ile, yalnızca dizin içerisindeki değişiklikler `clouddrive` kalıcı hale getirilir. Bash 'de `$HOME` dizininiz de kalıcı hale getirilir.
- Azure dosya paylaşımları yalnızca [atanan bölgeniz](persisting-shell-storage.md#mount-a-new-clouddrive)içinden bağlanabilir.
  - Bash içinde, `env` bölge kümesini olarak bulmak için öğesini çalıştırın `ACC_LOCATION` .
- Azure dosyaları yalnızca yerel olarak yedekli depolama ve coğrafi olarak yedekli depolama hesaplarını destekler.

### <a name="browser-support"></a>Tarayıcı desteği

Cloud Shell, aşağıdaki tarayıcıların en son sürümlerini destekler:

- Microsoft Edge
- Microsoft Internet Explorer
- Google Chrome
- Mozilla Firefox
- Apple Safari
  - Özel modda Safari desteklenmez.

### <a name="copy-and-paste"></a>Kopyala ve yapıştır

[!INCLUDE [copy-paste](../../includes/cloud-shell-copy-paste.md)]

### <a name="usage-limits"></a>Kullanım sınırları

Cloud Shell, etkileşimli kullanım örneklerine yöneliktir. Sonuç olarak, uzun süre çalışan etkileşimli olmayan oturumlar uyarı vermeden sonlandırılır.

### <a name="user-permissions"></a>Kullanıcı izinleri

İzinler, sudo erişimi olmayan normal kullanıcılar olarak ayarlanır. Dizininizin dışındaki tüm yüklemeler `$Home` kalıcı olmaz.

## <a name="bash-limitations"></a>Bash sınırlamaları

### <a name="editing-bashrc"></a>Düzenleniyor. bashrc

Bu işlemi yaparken dikkatli olun. bashrc, bunu yapmak Cloud Shell beklenmeyen hatalara neden olabilir.

## <a name="powershell-limitations"></a>PowerShell sınırlamaları

### <a name="preview-version-of-azuread-module"></a>AzureAD modülünün önizleme sürümü

Şu anda, `AzureAD.Standard.Preview` .NET Standard tabanlı bir modülün önizleme sürümü kullanılabilir. Bu modül ile aynı işlevleri sağlar `AzureAD` .

## <a name="personal-data-in-cloud-shell"></a>Cloud Shell kişisel veriler

Kişisel verilerinizi önemli ölçüde Azure Cloud Shell, Azure Cloud Shell hizmeti tarafından yakalanan ve depolanan veriler, en son kullanılan kabuğunuz, tercih ettiğiniz yazı tipi boyutu, tercih edilen yazı tipi türü ve bulut sürücüsünü geri yükleyen dosya paylaşma ayrıntıları gibi deneyiminize yönelik varsayılanlar sağlamak için kullanılır. Bu verileri dışarı aktarmak veya silmek için aşağıdaki yönergeleri kullanın.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

### <a name="export"></a>Dışarı Aktarma
Kullanıcı ayarlarını **dışarı aktarmak** için, tercih edilen kabuk, yazı tipi boyutu ve yazı tipi türü gibi Cloud Shell kaydeder ve aşağıdaki komutları çalıştırın.

1. [![Başlatma Azure Cloud Shell etiketli bir düğmeyi gösteren resim.](https://shell.azure.com/images/launchcloudshell.png)](https://shell.azure.com)

2. Bash veya PowerShell 'de aşağıdaki komutları çalıştırın:

Bash

  ```
  token="Bearer $(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s | jq -r ".access_token")"
  curl https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -H Authorization:"$token" -s | jq
  ```

PowerShell:

  ```powershell
  $token= ((Invoke-WebRequest -Uri "$env:MSI_ENDPOINT`?resource=https://management.core.windows.net/" -Headers @{Metadata='true'}).content |  ConvertFrom-Json).access_token
  ((Invoke-WebRequest -Uri https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -Headers @{Authorization = "Bearer $token"}).Content | ConvertFrom-Json).properties | Format-List
```

### <a name="delete"></a>Sil
Kullanıcı ayarlarınızı **silmek** için Cloud Shell tercih edilen kabuk, yazı tipi boyutu ve yazı tipi türü gibi, aşağıdaki komutları çalıştırın. Bir sonraki başlatmanızda Cloud Shell bir dosya paylaşımının yeniden eklenmesi istenir. 

>[!Note]
> Kullanıcı ayarlarınızı silerseniz, gerçek Azure dosyaları paylaşma silinmez. Bu eylemi gerçekleştirmek için Azure dosyalarınıza gidin.

1. [![Başlatma Azure Cloud Shell etiketli bir düğmeyi gösteren resim.](https://shell.azure.com/images/launchcloudshell.png)](https://shell.azure.com)

2. Bash veya PowerShell 'de aşağıdaki komutları çalıştırın:

Bash

  ```
  token="Bearer $(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s | jq -r ".access_token")"
  curl -X DELETE https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -H Authorization:"$token"
  ```

PowerShell:

  ```powershell
  $token= ((Invoke-WebRequest -Uri "$env:MSI_ENDPOINT`?resource=https://management.core.windows.net/" -Headers @{Metadata='true'}).content |  ConvertFrom-Json).access_token
  Invoke-WebRequest -Method Delete -Uri https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -Headers @{Authorization = "Bearer $token"}
  ```
## <a name="azure-government-limitations"></a>Azure Kamu sınırlamaları
Azure Kamu Azure Cloud Shell yalnızca Azure portal üzerinden erişilebilir.

>[!Note]
> Exchange Online için GCC-High veya kamu DoD bulutlarına bağlanma Şu anda desteklenmiyor.
