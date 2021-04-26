---
title: Azure AD Connect bulut eşitlemesi için AADCloudSyncTools PowerShell modülü
description: Bu makalede Azure AD Connect bulut sağlama aracısının nasıl yükleneceği açıklanır.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/30/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: aa358b0c9d7747584deabe761160d3bcbcde8feb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99593189"
---
# <a name="aadcloudsynctools-powershell-module-for-azure-ad-connect-cloud-sync"></a>Azure AD Connect bulut eşitlemesi için AADCloudSyncTools PowerShell modülü

AADCloudSyncTools modülü, Azure AD Connect bulut eşitleme dağıtımlarınızın yönetilmesine yardımcı olmak için kullanabileceğiniz bir dizi kullanışlı araç sağlar.

## <a name="pre-requisites"></a>Ön koşullar
Aşağıdaki önkoşulların olması gerekir:

- Bu modülün tüm önkoşulları kullanılarak otomatik olarak yüklenebilir `Install-AADCloudSyncToolsPrerequisites`
- Bu modül, MSAL kimlik doğrulamasını kullanır, bu nedenle MSAL.PS modülünün yüklü olmasını gerektirir. Doğrulamak için, bir PowerShell penceresinde yürütün `Get-module MSAL.PS -ListAvailable` . Modül doğru şekilde yüklenirse bir yanıt alırsınız. `Install-AADCloudSyncToolsPrerequisites`En son msal.PS sürümünü yüklemek için kullanabilirsiniz.
- AzureAD PowerShell modülü Bu modülün herhangi bir işlevi için önkoşul olmamasına karşın, Ayrıca, kullanılarak otomatik olarak yüklenmesi gerekir `Install-AADCloudSyncToolsPrerequisites` .
- Modülleri PowerShell 'den el ile yüklemek için TLS 1,2 zorlaması gerekir. Modül yükleyebilmeniz için, PowerShell oturumunda aşağıdakileri kullanmadan önce ayarlayın
  ```
   Install-Module:
  [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12 
  ```


## <a name="install-the-aadcloudsynctools-powershell-module"></a>AADCloudSyncTools PowerShell modülünü yükler
AADCloudSyncTools modülünü yüklemek ve kullanmak için aşağıdaki adımları kullanın:

1. Windows PowerShell 'i yönetici ayrıcalıklarıyla açın
2. Aşağıdakileri yazın veya kopyalayıp yapıştırın: `Import-module -Name "C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\Utility\AADCloudSyncTools"`
3. ENTER tuşuna basın.
4. Modülün alındığını doğrulamak için aşağıdakileri girin veya kopyalayıp yapıştırın: `Get-module AADCloudSyncTools`
5. Artık modülle ilgili bilgileri görmeniz gerekir.
6. Ardından, AADCloudSyncTools modülünü yüklemek için önkoşulları çalıştırın: `Install-AADCloudSyncToolsPrerequisites`
7. İlk çalıştırmada, PoweShellGet modülü yoksa, yüklenir. Yeni PowershellGet modülünü yüklemek için PowerShell penceresini kapatın ve yönetici ayrıcalıklarıyla yeni bir PowerShell oturumu açın. 
8. Adım 3 ' ü kullanarak modülü yeniden içeri aktarın.
9. `Install-AADCloudSyncToolsPrerequisites`Msal ve AzureAD modüllerini yüklemek için çalıştırın
11. Tüm ön yeniden QS yükleme modülünü başarıyla yüklemiş olmalıdır ![](media/reference-powershell/install-1.png)


## <a name="aadcloudsynctools--cmdlets"></a>AADCloudSyncTools cmdlet 'Leri
### <a name="connect-aadcloudsynctools"></a>Connect-AADCloudSyncTools
Microsoft Graph erişmek için Azure AD yöneticisinin belirtecini istemek üzere MSAL.PS modülünü kullanır 


### <a name="export-aadcloudsynctoolslogs"></a>Export-AADCloudSyncToolsLogs
Sıkıştırılmış bir dosyadaki tüm sorun giderme verilerini aşağıdaki şekilde dışa aktarır ve paketler:
 1. Start-AADCloudSyncToolsVerboseLogs ile ayrıntılı bir izleme başlatır.  Bu izleme günlüklerini C:\ProgramData\Microsoft\Azure AD Connect Provisioning Agent\Trace klasöründe bulabilirsiniz.
 2. 3 dakika boyunca bir izleme günlüğü toplar.
   -TracingDurationMins ile farklı bir saat belirtebilir veya-SkipVerboseTrace ile ayrıntılı izlemeyi atlayabilirsiniz
 3. Stop-AADCloudSyncToolsVerboseLogs ile ayrıntılı izlemeyi durduruyor
 4. Son 24 saat için Olay Görüntüleyicisi günlüklerini toplar
 5. Tüm aracı günlüklerini, ayrıntılı günlükleri ve Olay Görüntüleyici günlüklerini kullanıcının Belgeler klasörü altındaki sıkıştırılmış bir ZIP dosyasında sıkıştırır. 
 </br>-OutputPath ile farklı bir çıkış klasörü belirtebilirsiniz \<folder path\>

### <a name="get-aadcloudsynctoolsinfo"></a>Get-AADCloudSyncToolsInfo
Azure AD kiracı ayrıntılarını ve iç değişkenlerin durumunu gösterir

### <a name="get-aadcloudsynctoolsjob"></a>Get-AADCloudSyncToolsJob
, AD2AAD hizmet sorumlularını almak ve eşitleme Işi bilgilerini geri döndürmek için Graf kullanır.
Bir parametre olarak belirli eşitleme Işi KIMLIĞI kullanılarak da çağrılabilir.

### <a name="get-aadcloudsynctoolsjobschedule"></a>Get-AADCloudSyncToolsJobSchedule
, AD2AAD hizmet sorumlularını almak için Graf kullanır ve eşitleme Işinin zamanlamasını döndürür.
Bir parametre olarak belirli eşitleme Işi KIMLIĞI kullanılarak da çağrılabilir.

### <a name="get-aadcloudsynctoolsjobschema"></a>Get-AADCloudSyncToolsJobSchema
, AD2AAD hizmet sorumlularını almak için Graf kullanır ve eşitleme Işinin şemasını döndürür.

### <a name="get-aadcloudsynctoolsjobscope"></a>Get-AADCloudSyncToolsJobScope
, Belirtilen eşitleme Işi KIMLIĞI için eşitleme Işinin şemasını almak ve tüm filtre grubu kapsamlarını çıktılar için Graf kullanır.

### <a name="get-aadcloudsynctoolsjobsettings"></a>Get-AADCloudSyncToolsJobSettings
, AD2AAD hizmet sorumlularını almak için Graf kullanır ve eşitleme Işinin ayarlarını döndürür.
Bir parametre olarak belirli eşitleme Işi KIMLIĞI kullanılarak da çağrılabilir.

### <a name="get-aadcloudsynctoolsjobstatus"></a>Get-AADCloudSyncToolsJobStatus
, AD2AAD hizmet sorumlularını almak için Graf kullanır ve eşitleme Işinin durumunu döndürür.
Bir parametre olarak belirli eşitleme Işi KIMLIĞI kullanılarak da çağrılabilir.

### <a name="get-aadcloudsynctoolsserviceprincipal"></a>Get-AADCloudSyncToolsServicePrincipal
AD2AAD ve/veya SyncFabric için hizmet sorumlusunu almak üzere Graf kullanır.
Parametresiz, yalnızca AD2AAD hizmet sorumlusu döndürülür.

### <a name="install-aadcloudsynctoolsprerequisites"></a>Install-AADCloudSyncToolsPrerequisites
PowerShellGet v 2.2.4.1 veya üzeri ve Azure AD ve MSAL.PS modüllerinin varlığını denetler ve eksik ise bunları kurar.

### <a name="invoke-aadcloudsynctoolsgraphquery"></a>Invoke-AADCloudSyncToolsGraphQuery
Parametre olarak belirtilen URI, yöntem ve gövde için bir Web isteği çağırır

### <a name="repair-aadcloudsynctoolsaccount"></a>Repair-AADCloudSyncToolsAccount
Geçerli hesabı (varsa) silmek için Azure AD PowerShell 'i kullanır ve eşitleme hesabı kimlik doğrulamasını Azure AD 'de yeni bir eşitleme hesabıyla sıfırlar.

### <a name="restart-aadcloudsynctoolsjob"></a>Restart-AADCloudSyncToolsJob
Tam eşitlemeyi yeniden başlatır.

### <a name="resume-aadcloudsynctoolsjob"></a>Resume-AADCloudSyncToolsJob
Önceki filigrandan eşitlemeye devam eder.

### <a name="start-aadcloudsynctoolsverboselogs"></a>Start-AADCloudSyncToolsVerboseLogs
Ayrıntılı izlemeyi etkinleştirmek ve AADConnectProvisioningAgent hizmetini yeniden başlatmak için ' AADConnectProvisioningAgent.exe.config ' değerini değiştirerek hizmetin yeniden başlatılmasını engellemek için-SkipServiceRestart kullanabilirsiniz, ancak herhangi bir yapılandırma değişikliği etkili olmayacaktır.  Bu izleme günlüklerini C:\ProgramData\Microsoft\Azure AD Connect Provisioning Agent\Trace klasöründe bulabilirsiniz.

### <a name="stop-aadcloudsynctoolsverboselogs"></a>Stop-AADCloudSyncToolsVerboseLogs
Ayrıntılı izlemeyi devre dışı bırakmak için ' AADConnectProvisioningAgent.exe.config ' öğesini değiştirir ve AADConnectProvisioningAgent hizmetini yeniden başlatır. Hizmetin yeniden başlatılmasını engellemek için-SkipServiceRestart kullanabilirsiniz, ancak herhangi bir yapılandırma değişikliği etkili olmayacaktır.

### <a name="suspend-aadcloudsynctoolsjob"></a>Suspend-AADCloudSyncToolsJob
Eşitlemeyi duraklatır.

## <a name="next-steps"></a>Sonraki adımlar 

- [Sağlama nedir?](what-is-provisioning.md)
- [Azure AD Connect bulut eşitlemesi nedir?](what-is-cloud-sync.md)

