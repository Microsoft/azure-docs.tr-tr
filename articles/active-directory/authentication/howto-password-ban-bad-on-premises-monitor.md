---
title: Azure AD parola koruması 'nda izleme ve günlüğe kaydetme-Azure Active Directory
description: Azure AD parola koruması izlemeyi ve günlüğe kaydetmeyi anlama
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: a763f15b57bf7f23eeb52c81dd48de7f02adc5e4
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/08/2019
ms.locfileid: "68853550"
---
# <a name="azure-ad-password-protection-monitoring-and-logging"></a>Azure AD parola koruması izleme ve günlüğe kaydetme

Azure AD parola koruması, izleme ve raporlama dağıtımıyla sonra önemli görevlerdir. Bu makale, her bir hizmetin bilgileri günlüğe kaydettiği ve Azure AD parola korumasının kullanımını nasıl rapor açtığına dahil olmak üzere çeşitli izleme tekniklerini anlamanıza yardımcı olmak için ayrıntılı bir hale geçer.

İzleme ve raporlama olay günlüğü iletileri ya da PowerShell cmdlet 'leri çalıştırılarak yapılır. DC Aracısı ve proxy hizmetleri, olay günlüğü iletilerinin ikisini de günlüğe kaydeder. Aşağıda açıklanan tüm PowerShell cmdlet 'leri yalnızca proxy sunucuda mevcuttur (bkz. AzureADPasswordProtection PowerShell modülü). DC Aracısı yazılımı PowerShell modülünü yüklemez.

## <a name="dc-agent-event-logging"></a>DC Aracısı olay günlüğü

Her etki alanı denetleyicisinde, DC Aracısı hizmet yazılımı her bir parola doğrulama işleminin sonuçlarını (ve diğer durumu) yerel bir olay günlüğüne yazar:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Admin`

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Operational`

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Trace`

DC Aracısı Yönetici günlüğü, yazılımın nasıl davranmakta olduğunu gösteren birincil bilgi kaynağıdır.

Izleme günlüğünün varsayılan olarak kapalı olduğunu unutmayın.

Çeşitli DC Aracısı bileşenleri tarafından günlüğe kaydedilen olaylar aşağıdaki aralıklar içinde yer almalıdır:

|Bileşen |Olay KIMLIĞI aralığı|
| --- | --- |
|DC aracısı parola filtresi dll 'si| 10000-19999|
|DC aracı hizmeti barındırma işlemi| 20000-29999|
|DC Aracısı hizmet ilkesi doğrulama mantığı| 30000-39999|

## <a name="dc-agent-admin-event-log"></a>DC Aracısı Yönetici olay günlüğü

### <a name="password-validation-outcome-events"></a>Parola doğrulama sonucu olayları

Her etki alanı denetleyicisinde, DC Aracısı hizmet yazılımı her bir parola doğrulamasının sonuçlarını DC Aracısı Yönetici olay günlüğüne yazar.

Başarılı bir parola doğrulama işlemi için, DC aracısı parola filtresi dll 'sinden genellikle bir olay günlüğe kaydedilir. Hatalı parola doğrulama işlemi için, biri DC aracı hizmetinden diğeri de DC aracısı parola filtresi dll 'sinden günlüğe kaydedilen genellikle iki olay vardır.

Bu durumları yakalamaya yönelik ayrık olaylar, aşağıdaki faktörlere göre günlüğe kaydedilir:

* Belirli bir parolanın ayarlanmış veya değiştirilmiş olup olmadığı.
* Verilen parola doğrulamanın başarılı veya başarısız olup olmadığı.
* Microsoft genel ilkesi, kuruluş ilkesi veya bir bileşim nedeniyle doğrulamanın başarısız olup olmadığı.
* Geçerli parola ilkesi için yalnızca denetim modunun etkin olup olmadığı.

Anahtar parolası-doğrulama ile ilgili olaylar aşağıdaki gibidir:

|   |Parola değişimi |Parola kümesi|
| --- | :---: | :---: |
|Geçiş |10014 |10015|
|Başarısız (müşteri parola ilkesi nedeniyle)| 10016, 30002| 10017, 30003|
|Başarısız (Microsoft parola ilkesi nedeniyle)| 10016, 30004| 10017, 30005|
|Başarısız (Birleşik Microsoft ve müşteri parola ilkeleri nedeniyle)| 10016, 30026| 10017, 30027|
|Yalnızca denetim geçişi (müşteri parolası ilkesi başarısız olur)| 10024, 30008| 10025, 30007|
|Yalnızca denetim geçişi (Microsoft parola ilkesi başarısız olur)| 10024, 30010| 10025, 30009|
|Yalnızca denetim geçişi (Microsoft ve müşteri parola ilkelerini birleştirmelidir)| 10024, 30028| 10025, 30029|

Yukarıdaki tabloda yer alan "birleştirilmiş ilkeler" e başvuran durumlar, bir kullanıcının parolasının hem Microsoft yasaklanmış parola listesi hem de müşteri yasaklanmış parola listesinden en az bir belirteç içerdiği durumlara başvururlar.

Bir çift olay çifti birlikte kaydedildiğinde, her iki olay da aynı CorrelationId 'ye sahip tarafından açıkça ilişkilendirilir.

### <a name="password-validation-summary-reporting-via-powershell"></a>PowerShell aracılığıyla parola doğrulama özeti raporlaması

Cmdlet `Get-AzureADPasswordProtectionSummaryReport` 'i, parola doğrulama etkinliğinin Özet görünümünü oluşturmak için kullanılabilir. Bu cmdlet 'in bir örnek çıktısı aşağıdaki gibidir:

```powershell
Get-AzureADPasswordProtectionSummaryReport -DomainController bplrootdc2
DomainController                : bplrootdc2
PasswordChangesValidated        : 6677
PasswordSetsValidated           : 9
PasswordChangesRejected         : 10868
PasswordSetsRejected            : 34
PasswordChangeAuditOnlyFailures : 213
PasswordSetAuditOnlyFailures    : 3
PasswordChangeErrors            : 0
PasswordSetErrors               : 1
```

Cmdlet 'in raporlama kapsamı – Forest,-Domain veya – DomainController parametrelerinden biri kullanılarak etkilenebilir. Bir parametre belirtilmemekle, – Forest anlamına gelir.

`Get-AzureADPasswordProtectionSummaryReport` Cmdlet, DC Aracısı Yönetici olay günlüğünü sorgulayarak ve ardından görüntülenen her bir sonuç kategorisine karşılık gelen olayların toplam sayısını sayarak işe yarar. Aşağıdaki tabloda, her bir sonuç ve onun karşılık gelen olay KIMLIĞI arasındaki eşlemeler yer almaktadır:

|Get-AzureADPasswordProtectionSummaryReport özelliği |Karşılık gelen olay KIMLIĞI|
| :---: | :---: |
|Passwordchangesdoğruladı |10014|
|Passwordsetsdoğrulanan |10015|
|Passwordchangesreddedildi |10016|
|Passwordsetsreddedildi |10017|
|Passwordchangesestonlyarızaları |10024|
|Passwordsetauditonlyarızaları |10025|
|PasswordChangeErrors |10012|
|PasswordSetErrors |10013|

`Get-AzureADPasswordProtectionSummaryReport` Cmdlet 'in PowerShell betik biçiminde gönderildiğini ve gerekirse doğrudan aşağıdaki konumda başvurulduğunu unutmayın:

`%ProgramFiles%\WindowsPowerShell\Modules\AzureADPasswordProtection\Get-AzureADPasswordProtectionSummaryReport.ps1`

> [!NOTE]
> Bu cmdlet, her etki alanı denetleyicisine bir PowerShell oturumu açarak işe yarar. Başarılı olmak için, her etki alanı denetleyicisinde PowerShell uzak oturum desteğinin etkinleştirilmesi ve istemcinin yeterli ayrıcalıklara sahip olması gerekir. PowerShell uzak oturum gereksinimleri hakkında daha fazla bilgi için bir PowerShell penceresinde ' Get-Help about_Remote_Troubleshooting ' öğesini çalıştırın.

> [!NOTE]
> Bu cmdlet, her DC Aracısı hizmetinin yönetici olay günlüğünü uzaktan sorgulayarak işe yarar. Olay günlükleri çok sayıda olay içeriyorsa, cmdlet 'in tamamlanması uzun zaman alabilir. Ayrıca, büyük veri kümelerinin toplu ağ sorguları, etki alanı denetleyicisi performansını etkileyebilir. Bu nedenle, bu cmdlet üretim ortamlarında dikkatle kullanılmalıdır.

### <a name="sample-event-log-message-for-event-id-10014-successful-password-change"></a>Olay KIMLIĞI 10014 için örnek olay günlüğü iletisi (başarılı parola değişikliği)

```text
The changed password for the specified user was validated as compliant with the current Azure password policy.

 UserName: BPL_02885102771
 FullName:
```

### <a name="sample-event-log-message-for-event-id-10017-and-30003-failed-password-set"></a>Olay KIMLIĞI 10017 ve 30003 için örnek olay günlüğü iletisi (başarısız parola kümesi)

10017:

```text
The reset password for the specified user was rejected because it did not comply with the current Azure password policy. Please see the correlated event log message for more details.

 UserName: BPL_03283841185
 FullName:
```

30003:

```text
The reset password for the specified user was rejected because it matched at least one of the tokens present in the per-tenant banned password list of the current Azure password policy.

 UserName: BPL_03283841185
 FullName:
```

### <a name="sample-event-log-message-for-event-id-30001-password-accepted-due-to-no-policy-available"></a>Olay KIMLIĞI 30001 için örnek olay günlüğü iletisi (kullanılabilir ilke olmaması nedeniyle parola kabul edildi)

```text
The password for the specified user was accepted because an Azure password policy is not available yet

UserName: SomeUser
FullName: Some User

This condition may be caused by one or more of the following reasons:%n

1. The forest has not yet been registered with Azure.

   Resolution steps: an administrator must register the forest using the Register-AzureADPasswordProtectionForest cmdlet.

2. An Azure AD password protection Proxy is not yet available on at least one machine in the current forest.

   Resolution steps: an administrator must install and register a proxy using the Register-AzureADPasswordProtectionProxy cmdlet.

3. This DC does not have network connectivity to any Azure AD password protection Proxy instances.

   Resolution steps: ensure network connectivity exists to at least one Azure AD password protection Proxy instance.

4. This DC does not have connectivity to other domain controllers in the domain.

   Resolution steps: ensure network connectivity exists to the domain.
```

### <a name="sample-event-log-message-for-event-id-30006-new-policy-being-enforced"></a>Olay KIMLIĞI 30006 için örnek olay günlüğü iletisi (zorlanmakta olan yeni ilke)

```text
The service is now enforcing the following Azure password policy.

 Enabled: 1
 AuditOnly: 1
 Global policy date: ‎2018‎-‎05‎-‎15T00:00:00.000000000Z
 Tenant policy date: ‎2018‎-‎06‎-‎10T20:15:24.432457600Z
 Enforce tenant policy: 1
```

### <a name="sample-event-log-message-for-event-id-30019-azure-ad-password-protection-is-disabled"></a>Olay KIMLIĞI 30019 için örnek olay günlüğü iletisi (Azure AD parola koruması devre dışı)

```text
The most recently obtained Azure password policy was configured to be disabled. All passwords submitted for validation from this point on will automatically be considered compliant with no processing performed.

No further events will be logged until the policy is changed.%n

```

## <a name="dc-agent-operational-log"></a>DC Aracısı Işlem günlüğü

DC Aracısı hizmeti, işlemsel ilgili olayları aşağıdaki günlüğe de kaydeder:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Operational`

## <a name="dc-agent-trace-log"></a>DC Aracısı Izleme günlüğü

DC Aracısı hizmeti ayrıntılı hata ayıklama düzeyi izleme olaylarını aşağıdaki günlüğe de kaydedebilir:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Trace`

İzleme günlüğü varsayılan olarak devre dışıdır.

> [!WARNING]
> Etkinleştirildiğinde, Izleme günlüğü yüksek hacimli olaylar alır ve etki alanı denetleyicisi performansını etkileyebilir. Bu nedenle, bu gelişmiş günlüğün yalnızca bir sorun daha derin bir araştırma gerektirdiğinde ve daha sonra yalnızca en az bir süre için etkinleştirilmesi gerekir.

## <a name="dc-agent-text-logging"></a>DC Aracısı metin günlüğü

DC Aracısı hizmeti, aşağıdaki kayıt defteri değerini ayarlayarak bir metin günlüğüne yazmak üzere yapılandırılabilir:

```text
HKLM\System\CurrentControlSet\Services\AzureADPasswordProtectionDCAgent\Parameters!EnableTextLogging = 1 (REG_DWORD value)
```

Metin günlüğe kaydetme varsayılan olarak devre dışıdır. Bu değerde yapılan değişikliklerin etkili olması için DC Aracısı hizmetinin yeniden başlatılması gerekiyor. Etkinleştirildiğinde, DC Aracısı hizmeti şu konumda bulunan bir günlük dosyasına yazılır:

`%ProgramFiles%\Azure AD Password Protection DC Agent\Logs`

> [!TIP]
> Metin günlüğü, Izleme günlüğüne kaydedilen hata ayıklama düzeyi girdileri alır, ancak genellikle gözden geçirmek ve analiz etmek daha kolay bir biçimdedir.

> [!WARNING]
> Bu günlük etkinleştirildiğinde, yüksek hacimli olaylar alır ve etki alanı denetleyicisi performansını etkileyebilir. Bu nedenle, bu gelişmiş günlüğün yalnızca bir sorun daha derin bir araştırma gerektirdiğinde ve daha sonra yalnızca en az bir süre için etkinleştirilmesi gerekir.

## <a name="dc-agent-performance-monitoring"></a>DC Aracısı performans izleme

DC aracı hizmeti yazılımı, **Azure AD parola koruması**adlı bir performans sayacı nesnesi yüklüyor. Aşağıdaki performans sayaçları Şu anda kullanılabilir:

|Performans sayacı adı | Açıklama|
| --- | --- |
|İşlenen parolalar |Bu sayaç, son yeniden başlatmadan bu yana işlenen toplam parola sayısını (kabul edilen veya reddedilen) görüntüler.|
|Kabul edilen parolalar |Bu sayaç, son yeniden başlatmadan bu yana kabul edilen toplam parola sayısını görüntüler.|
|Parolalar reddedildi |Bu sayaç, son yeniden başlatmadan bu yana reddedilen toplam parola sayısını görüntüler.|
|Parola filtresi istekleri devam ediyor |Bu sayaç, şu anda sürmekte olan parola filtresi isteklerinin sayısını görüntüler.|
|En yüksek parola filtresi istekleri |Bu sayaç, son yeniden başlatmadan bu yana eşzamanlı parola filtresi isteklerinin en yüksek sayısını görüntüler.|
|Parola filtresi istek hataları |Bu sayaç, son yeniden başlatmadan bu yana bir hata nedeniyle başarısız olan parola filtresi isteklerinin toplam sayısını görüntüler. Azure AD parola koruması DC Aracısı hizmeti çalışmadığı zaman hatalar meydana gelebilir.|
|Parola filtresi isteği/sn |Bu sayaç, parolaların işlenme hızını görüntüler.|
|Parola filtresi isteği işleme süresi |Bu sayaç, bir parola filtresi isteğini işlemek için gereken ortalama süreyi gösterir.|
|En yoğun parola filtresi isteği işleme süresi |Bu sayaç, son yeniden başlatmadan bu yana en yüksek parola filtresi isteği işleme süresini görüntüler.|
|Denetim modu nedeniyle parola kabul edildi |Bu sayaç normalde reddedilen toplam parola sayısını görüntüler, ancak parola ilkesi denetim modunda olacak şekilde yapılandırıldığından (son yeniden başlatmadan bu yana) kabul edilir.|

## <a name="dc-agent-discovery"></a>DC Aracısı bulma

`Get-AzureADPasswordProtectionDCAgent` Cmdlet 'i, bir etki alanında veya ormanda çalışan çeşitli DC aracıları hakkındaki temel bilgileri göstermek için kullanılabilir. Bu bilgiler, çalışan DC Aracısı Hizmetleri tarafından kaydedilen serviceConnectionPoint nesnesinden alınır.

Bu cmdlet 'in bir örnek çıktısı aşağıdaki gibidir:

```powershell
Get-AzureADPasswordProtectionDCAgent
ServerFQDN            : bplChildDC2.bplchild.bplRootDomain.com
Domain                : bplchild.bplRootDomain.com
Forest                : bplRootDomain.com
PasswordPolicyDateUTC : 2/16/2018 8:35:01 AM
HeartbeatUTC          : 2/16/2018 8:35:02 AM
```

Çeşitli özellikler, her DC Aracısı hizmeti tarafından yaklaşık saat temelinde güncelleştirilir. Veriler hala Active Directory çoğaltma gecikmesine tabidir.

Cmdlet 'inin sorgusunun kapsamı, – Forest veya – Domain parametreleri kullanılarak etkilenebilir.

HeartbeatUTC değeri eski alırsa, bu etki alanı denetleyicisindeki Azure AD parola koruması DC aracısının çalışmadığını veya kaldırıldığını ya da makinenin indirgenmiş olduğunu ve artık bir etki alanı denetleyicisi olmadığını belirten bir belirti olabilir.

PasswordPolicyDateUTC değeri eski alırsa bu durum, söz konusu makinedeki Azure AD parola koruması DC aracısının düzgün çalışmadığını belirten bir belirti olabilir.

## <a name="dc-agent-newer-version-available"></a>DC Aracısı daha yeni sürümü kullanılabilir

DC Aracısı yazılımının daha yeni bir sürümünün kullanılabilir olduğunu algılayarak, DC aracı hizmeti bir 30034 uyarı olayını Işletimsel günlüğe günlüğe kaydeder, örneğin:

```text
An update for Azure AD Password Protection DC Agent is available.

If autoupgrade is enabled, this message may be ignored.

If autoupgrade is disabled, refer to the following link for the latest version available:

https://aka.ms/AzureADPasswordProtectionAgentSoftwareVersions

Current version: 1.2.116.0
```

Yukarıdaki olay, yeni yazılımın sürümünü belirtmiyor. Bu bilgi için olay iletisindeki bağlantıya gitmeniz gerekir.

> [!NOTE]
> Yukarıdaki olay iletisindeki "Oto Upgrade" başvurusu olmasına rağmen, DC aracı yazılımı şu anda bu özelliği desteklememektedir.

## <a name="proxy-service-event-logging"></a>Proxy hizmeti olay günlüğü

Proxy hizmeti, aşağıdaki olay günlüklerine minimum bir olay kümesi yayar:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Admin`

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Operational`

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Trace`

Izleme günlüğünün varsayılan olarak kapalı olduğunu unutmayın.

> [!WARNING]
> Etkinleştirildiğinde, Izleme günlüğü yüksek hacimli olaylar alır ve bu, proxy konağın performansını etkileyebilir. Bu nedenle, bu günlük yalnızca bir sorun daha derin bir araştırma gerektirdiğinde ve daha sonra yalnızca en az bir süre için etkinleştirilmelidir.

Olaylar, aşağıdaki aralıklar kullanılarak çeşitli proxy bileşenleri tarafından günlüğe kaydedilir:

|Bileşen |Olay KIMLIĞI aralığı|
| --- | --- |
|Proxy hizmeti barındırma işlemi| 10000-19999|
|Proxy hizmeti Core iş mantığı| 20000-29999|
|PowerShell cmdlet'leri| 30000-39999|

## <a name="proxy-service-text-logging"></a>Proxy hizmeti metin günlüğü

Proxy hizmeti, aşağıdaki kayıt defteri değerini ayarlayarak bir metin günlüğüne yazmak üzere yapılandırılabilir:

HKLM\System\CurrentControlSet\Services\AzureADPasswordProtectionProxy\Parameters! EnableTextLogging = 1 (REG_DWORD değeri)

Metin günlüğe kaydetme varsayılan olarak devre dışıdır. Bu değerde yapılan değişikliklerin etkili olması için proxy hizmeti 'nin yeniden başlatılması gerekiyor. Etkinleştirildiğinde proxy hizmeti şu konumda bulunan bir günlük dosyasına yazılır:

`%ProgramFiles%\Azure AD Password Protection Proxy\Logs`

> [!TIP]
> Metin günlüğü, Izleme günlüğüne kaydedilen hata ayıklama düzeyi girdileri alır, ancak genellikle gözden geçirmek ve analiz etmek daha kolay bir biçimdedir.

> [!WARNING]
> Bu günlük etkinleştirildiğinde, yüksek hacimli olaylar alır ve makinenin performansını etkileyebilir. Bu nedenle, bu gelişmiş günlüğün yalnızca bir sorun daha derin bir araştırma gerektirdiğinde ve daha sonra yalnızca en az bir süre için etkinleştirilmesi gerekir.

## <a name="powershell-cmdlet-logging"></a>PowerShell cmdlet günlüğü

Durum değişikliğine neden olan PowerShell cmdlet 'leri (örneğin, Register-AzureADPasswordProtectionProxy), normalde bir sonuç olayını Işlemsel günlüğe kaydeder.

Ayrıca, Azure AD parola koruması PowerShell cmdlet 'lerinin çoğu altında bulunan bir metin günlüğüne yazılır:

`%ProgramFiles%\Azure AD Password Protection Proxy\Logs`

Bir cmdlet hatası oluşursa ve nedeni and\veya çözümü açık değilse, bu metin günlükleri de uygulanabilir.

## <a name="proxy-discovery"></a>Proxy bulma

`Get-AzureADPasswordProtectionProxy` Cmdlet 'i, bir etki alanında veya ormanda çalışan çeşitli Azure AD parola koruma proxy hizmetleriyle ilgili temel bilgileri göstermek için kullanılabilir. Bu bilgiler, çalışan proxy hizmetleri tarafından kaydedilen serviceConnectionPoint nesnesinden alınır.

Bu cmdlet 'in bir örnek çıktısı aşağıdaki gibidir:

```powershell
Get-AzureADPasswordProtectionProxy
ServerFQDN            : bplProxy.bplchild2.bplRootDomain.com
Domain                : bplchild2.bplRootDomain.com
Forest                : bplRootDomain.com
HeartbeatUTC          : 12/25/2018 6:35:02 AM
```

Çeşitli özellikler her bir proxy hizmeti tarafından yaklaşık saat temelinde güncelleştirilir. Veriler hala Active Directory çoğaltma gecikmesine tabidir.

Cmdlet 'inin sorgusunun kapsamı, – Forest veya – Domain parametreleri kullanılarak etkilenebilir.

HeartbeatUTC değeri eski alırsa bu, söz konusu makinedeki Azure AD parola koruma proxy 'Sinin çalışmadığını veya kaldırıldığını belirten bir belirti olabilir.

## <a name="proxy-agent-newer-version-available"></a>Proxy aracısı daha yeni sürümü kullanılabilir

Proxy hizmeti, proxy yazılımının daha yeni bir sürümünün kullanılabilir olduğunu algılayarak bir 20002 uyarı olayını Işlem günlüğüne kaydeder, örneğin:

```text
An update for Azure AD Password Protection Proxy is available.

If autoupgrade is enabled, this message may be ignored.

If autoupgrade is disabled, refer to the following link for the latest version available:

https://aka.ms/AzureADPasswordProtectionAgentSoftwareVersions

Current version: 1.2.116.0
.
```

Yukarıdaki olay, yeni yazılımın sürümünü belirtmiyor. Bu bilgi için olay iletisindeki bağlantıya gitmeniz gerekir.

Bu olay, proxy aracısı otomatik yükseltme etkinleştirilmiş olarak yapılandırılmış olsa bile, bu olay yayınlanır.

## <a name="next-steps"></a>Sonraki adımlar

[Azure AD parola koruması sorunlarını giderme](howto-password-ban-bad-on-premises-troubleshoot.md)

Genel ve özel yasaklanmış parola listeleri hakkında daha fazla bilgi için bkz. [Hatalı parolalar](concept-password-ban-bad.md)
