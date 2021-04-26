---
title: Windows sanal masaüstü (klasik) sorunları tanılama-Azure
description: Sorunları tanılamak için Windows sanal masaüstü (klasik) Tanılama özelliğini kullanma.
author: Heidilohr
ms.topic: conceptual
ms.date: 05/13/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: e23a1e9a2a0118402df0d9b8869f170762a52284
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106444964"
---
# <a name="identify-and-diagnose-issues-in-windows-virtual-desktop-classic"></a>Windows sanal masaüstündeki sorunları tanımla ve Tanıla (klasik)

>[!IMPORTANT]
>Bu içerik, Windows sanal masaüstü nesneleri Azure Resource Manager desteklemeyen Windows sanal masaüstü (klasik) için geçerlidir. Azure Resource Manager Windows sanal masaüstü nesnelerini yönetmeye çalışıyorsanız, [Bu makaleye](../diagnostics-role-service.md)bakın.

Windows sanal masaüstü, yöneticinin sorunları tek bir arabirim üzerinden belirlemesine izin veren bir tanılama özelliği sunar. Windows sanal masaüstü rolleri, bir Kullanıcı sistemle her etkileşime geçtiğinde bir tanılama etkinliğini günlüğe kaydeder. Her günlük, işlem, hata iletileri, kiracı bilgileri ve Kullanıcı bilgilerine dahil olan Windows sanal masaüstü rolleri gibi ilgili bilgileri içerir. Tanılama etkinlikleri hem son kullanıcı hem de yönetim eylemleri tarafından oluşturulur ve üç ana demet halinde kategorilere ayrılabilir:

* Akış aboneliği etkinlikleri: Son Kullanıcı bu etkinlikleri, Microsoft Uzak Masaüstü uygulamaları aracılığıyla akışına bağlanmayı denediğinde tetikler.
* Bağlantı etkinlikleri: Son Kullanıcı, Microsoft Uzak Masaüstü uygulamalar aracılığıyla bir masaüstüne veya RemoteApp 'e bağlanmayı her denediğinde bu etkinlikleri tetikler.
* Yönetim etkinlikleri: yönetici, sistem üzerinde, konak havuzları oluşturma, kullanıcıları uygulama gruplarına atama ve rol atamaları oluşturma gibi yönetim işlemlerini gerçekleştirirken bu etkinlikleri tetikler.

Tanılama rolü hizmeti Windows sanal masaüstü 'nün bir parçası olduğundan, Windows sanal masaüstüne ulaşmayacak bağlantılar tanılama sonuçlarında gösterilmez. Son Kullanıcı ağ bağlantısı sorunlarıyla karşılaşıyorsa Windows sanal masaüstü bağlantısı sorunları ortaya çıkabilir.

Başlamak için, henüz yapmadıysanız PowerShell oturumunuzda kullanmak üzere [Windows sanal masaüstü PowerShell modülünü indirip içeri aktarın](/powershell/windows-virtual-desktop/overview/) . Bundan sonra hesabınızda oturum açmak için aşağıdaki cmdlet 'i çalıştırın:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="diagnose-issues-with-powershell"></a>PowerShell ile ilgili sorunları tanılama

Windows sanal masaüstü tanılaması yalnızca bir PowerShell cmdlet 'i kullanır, ancak sorunları daraltmak ve yalıtmak için birçok isteğe bağlı parametre içerir. Aşağıdaki bölümlerde, sorunları tanılamak için çalıştırabileceğiniz cmdlet 'ler listelenmektedir. Çoğu filtre birlikte uygulanabilir. Parantez içinde listelenen değerler, gibi `<tenantName>` , durumunuza uygulanan değerlerle değiştirilmelidir.

>[!IMPORTANT]
>Tanılama özelliği, tek kullanıcılı sorun giderme içindir. PowerShell kullanan tüm sorgular *-UserName* veya *-ActivityId* parametrelerini içermelidir. İzleme özellikleri için Log Analytics kullanın. Tanılama verilerini çalışma alanınıza gönderme hakkında daha fazla bilgi için bkz. [Tanılama özelliği için Log Analytics kullanma](diagnostics-log-analytics-2019.md) .

### <a name="filter-diagnostic-activities-by-user"></a>Tanılama etkinliklerini kullanıcıya göre filtrele

**-UserName** parametresi, aşağıdaki örnek cmdlet 'inde gösterildiği gibi, belirtilen kullanıcı tarafından başlatılan tanılama etkinliklerinin listesini döndürür.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN>
```

**-UserName** parametresi, diğer isteğe bağlı filtreleme parametreleriyle de birleştirilebilir.

### <a name="filter-diagnostic-activities-by-time"></a>Tanılama etkinliklerini zamana göre filtrele

Döndürülen tanılama etkinlik listesini **-StartTime** ve **-bitişsaati** parametreleriyle filtreleyebilirsiniz. **-StartTime** parametresi, aşağıdaki örnekte gösterildiği gibi belirli bir tarihten başlayarak bir tanılama etkinlik listesi döndürür.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN> -StartTime "08/01/2018"
```

**-Bitişsaati** parametresi, için sonuçları almak istediğiniz belirli bir süreyi belirtmek için **-StartTime** parametresi ile bir cmdlet 'ine eklenebilir. Aşağıdaki örnek cmdlet 'i 1 Ağustos ve 10 Ağustos arasında bir tanılama etkinlikleri listesi döndürür.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN> -StartTime "08/01/2018" -EndTime "08/10/2018"
```

**-StartTime** ve **-bitişsaati** parametreleri, diğer isteğe bağlı filtreleme parametreleriyle de birleştirilebilir.

### <a name="filter-diagnostic-activities-by-activity-type"></a>Tanılama etkinliklerini etkinlik türüne göre filtrele

Ayrıca, **-ActivityType** parametresiyle etkinlik türüne göre tanılama etkinliklerini filtreleyebilirsiniz. Aşağıdaki cmdlet Son Kullanıcı bağlantılarının bir listesini döndürür:

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN> -ActivityType Connection
```

Aşağıdaki cmdlet yönetici yönetim görevlerinin bir listesini döndürür:

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityType Management
```

**Get-RdsDiagnosticActivities** cmdlet 'i Şu anda ActivityType öğesinin akışını belirtmeyi desteklemiyor.

### <a name="filter-diagnostic-activities-by-outcome"></a>Tanılama etkinliklerini sonuca göre filtrele

Döndürülen tanılama etkinlik listesini, **-Outcome** parametresiyle Outcome ile filtreleyebilirsiniz. Aşağıdaki örnek cmdlet, başarılı tanılama etkinliklerinin bir listesini döndürür.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN> -Outcome Success
```

Aşağıdaki örnek cmdlet, başarısız tanılama etkinliklerinin bir listesini döndürür.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -Outcome Failure
```

**-Outcome** parametresi, diğer isteğe bağlı filtreleme parametreleriyle de birleştirilebilir.

### <a name="retrieve-a-specific-diagnostic-activity-by-activity-id"></a>Etkinlik KIMLIĞINE göre belirli bir tanılama etkinliğini al

**-ActivityId** parametresi, aşağıdaki örnek cmdlet 'inde gösterildiği gibi, varsa, belirli bir tanılama etkinliğini döndürür.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityId <ActivityIdGuid>
```

### <a name="view-error-messages-for-a-failed-activity-by-activity-id"></a>Etkinlik KIMLIĞINE göre başarısız bir etkinliğin hata iletilerini görüntüle

Başarısız bir etkinliğin hata iletilerini görüntülemek için cmdlet 'ini **-detailed** parametresiyle çalıştırmanız gerekir. **Select-Object** cmdlet 'ini çalıştırarak hata listesini görebilirsiniz.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantname> -ActivityId <ActivityGuid> -Detailed | Select-Object -ExpandProperty Errors
```

### <a name="retrieve-detailed-diagnostic-activities"></a>Ayrıntılı tanılama etkinliklerini alın

**-Detailed** parametresi döndürülen her bir tanılama etkinliği için ek ayrıntılar sağlar. Her etkinliğin biçimi, etkinlik türüne göre farklılık gösterir. **-Detailed** parametresi, aşağıdaki örnekte gösterildiği gibi herhangi bir **Get-RdsDiagnosticActivities** sorgusuna eklenebilir.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityId <ActivityGuid> -Detailed
```

## <a name="common-error-scenarios"></a>Yaygın hata senaryoları

Hata senaryoları, hizmete ve dışarıdan Windows sanal masaüstüne göre kategorize edilir.

* İç sorun: Kiracı Yöneticisi tarafından hafiflemeyecek ve bir destek sorunu olarak çözülmesi gereken senaryoları belirtir. [Windows sanal masaüstü teknik topluluğu](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)aracılığıyla geri bildirim sağlarken, etkinliğin kimliği ve yaklaşık zaman çerçevesini sorun oluştuğunda ekleyin.
* Dış sorun: Sistem Yöneticisi tarafından hafiflebilen senaryolarla ilgilidir. Bunlar Windows sanal masaüstü dışında.

Aşağıdaki tabloda, yöneticilerinizin çalıştırabilecek yaygın hatalar listelenmektedir.

>[!NOTE]
>Bu liste, en yaygın hataları içerir ve sıradan bir temposunda güncellenir. En güncel bilgilere sahip olduğunuzdan emin olmak için, bu makaleye ayda en az bir kez tekrar kontrol ettiğinizden emin olun.

### <a name="external-management-error-codes"></a>Dış yönetim hata kodları

|Sayısal kod|Hata kodu|Önerilen çözüm|
|---|---|---|
|1322|Connectionfailednomappingofsıdinad|Kullanıcı Azure Active Directory üyesi değil. [Active Directory Yönetim Merkezi](/windows-server/identity/ad-ds/get-started/adac/active-directory-administrative-center) içindeki yönergeleri izleyerek bunları ekleyin.|
|3|UnauthorizedAccess|Yönetim PowerShell cmdlet 'ini çalıştırmaya çalışan kullanıcının Kullanıcı adını bu şekilde veya yanlış yazmış.|
|1000|TenantNotFound|Girdiğiniz kiracı adı var olan kiracılar ile eşleşmiyor. Yazım hataları için kiracı adını gözden geçirin ve yeniden deneyin.|
|1006|TenantCannotBeRemovedHasSessionHostPools|Bir kiracıyı nesneler içerdiği sürece silemezsiniz. Önce oturum ana bilgisayar havuzlarını silip yeniden deneyin.|
|2000|HostPoolNotFound|Girdiğiniz ana bilgisayar havuzu adı mevcut konak havuzlarıyla eşleşmiyor. Yazım hataları için konak havuzu adını gözden geçirin ve yeniden deneyin.|
|2005|HostPoolCannotBeRemovedHasApplicationGroups|Bir konak havuzunu nesneler içerdiği sürece silemezsiniz. Önce konak havuzundaki tüm uygulama gruplarını kaldırın.|
|2004|HostPoolCannotBeRemovedHasSessionHosts|Oturum Ana bilgisayar havuzunu silmeden önce tüm oturum Konakları kaldırın.|
|5001|SessionHostNotFound|Sorguladığınız oturum ana bilgisayarı çevrimdışı olabilir. Konak havuzunun durumunu kontrol edin.|
|5008|SessionHostUserSessionsExist |Amaçlanan yönetim etkinliğinizi yürütmeden önce oturum ana bilgisayarındaki tüm kullanıcıları imzalamanız gerekir.|
|6000|AppGroupNotFound|Girdiğiniz uygulama grubu adı var olan herhangi bir uygulama grubuyla eşleşmiyor. Yazım hataları için uygulama grubu adını gözden geçirin ve yeniden deneyin.|
|6022|RemoteAppNotFound|Girdiğiniz RemoteApp adı herhangi bir RemoteApps ile eşleşmiyor. Yazım hataları için RemoteApp adını gözden geçirin ve yeniden deneyin.|
|6010|PublishedItemsExist|Yayımlamaya çalıştığınız kaynağın adı zaten var olan bir kaynakla aynı. Kaynak adını değiştirip yeniden deneyin.|
|7002|Süs Otvalidwhitespace|Adda boşluk kullanmayın.|
|8000|Invalidauthorizationrolescope|Girdiğiniz rol adı, var olan herhangi bir rol adıyla eşleşmiyor. Yazım hataları için rol adını gözden geçirin ve yeniden deneyin. |
|8001|UserNotFound |Girdiğiniz Kullanıcı adı varolan kullanıcı adlarıyla eşleşmiyor. Yazım hatalarını gözden geçirin ve yeniden deneyin.|
|8005|UserNotFoundInAAD |Girdiğiniz Kullanıcı adı varolan kullanıcı adlarıyla eşleşmiyor. Yazım hatalarını gözden geçirin ve yeniden deneyin.|
|8008|TenantConsentRequired|Kiracınıza onay sağlamak için [buradaki](tenant-setup-azure-active-directory.md#grant-permissions-to-windows-virtual-desktop) yönergeleri izleyin.|

### <a name="external-connection-error-codes"></a>Dış bağlantı hata kodları

|Sayısal kod|Hata kodu|Önerilen çözüm|
|---|---|---|
|-2147467259|Connectionfailedadderrornosuchmember|Kullanıcı Active Directory üyesi değil. [Active Directory Yönetim Merkezi](/windows-server/identity/ad-ds/get-started/adac/active-directory-administrative-center) içindeki yönergeleri izleyerek bunları ekleyin.|
|-2147467259|ConnectionFailedAdTrustedRelationshipFailure|Oturum Ana bilgisayarı Active Directory doğru şekilde katılmadı.|
|-2146233088|Connectionfaileduserhasvalidsessionbutrdshıssağlıksız|Oturum Konağı kullanılamadığından bağlantı başarısız oldu. Oturum konağının sistem durumunu kontrol edin.|
|-2146233088|ConnectionFailedClientDisconnect|Bu hatayla sık karşılaşırsanız, kullanıcının bilgisayarının ağa bağlı olduğundan emin olun.|
|-2146233088|Connectionfailednohealthyırdshavailable|Ana bilgisayar kullanıcısının bağlanmaya çalıştığı oturum sağlıklı değil. Sanal makinede hata ayıklayın.|
|-2146233088|ConnectionFailedUserNotAuthorized|Kullanıcının yayımlanan uygulamaya veya masaüstüne erişim izni yok. Hata, yönetici yayımlanmış kaynakları kaldırdıktan sonra görünebilir. Kullanıcıdan uzak masaüstü uygulamasında akışı yenilemesini isteyin.|
|2|FileNotFound|Kullanıcının erişmeye çalıştığı uygulama yanlış yüklenmiş veya hatalı bir yola ayarlı.|
|3|Invalidcredentials|Kullanıcının girdiği Kullanıcı adı veya parola, mevcut kullanıcı adları veya parolalarla eşleşmiyor. Yazım hataları bilgilerini gözden geçirin ve yeniden deneyin.|
|8|Connectionkopuk|Istemci ve ağ geçidi veya sunucu bağlantısı kesilen bağlantı. Beklenmedik bir şekilde gerçekleşmediği takdirde eyleme gerek yoktur.|
|14|UnexpectedNetworkDisconnect|Ağ bağlantısı bırakıldı. Kullanıcıdan tekrar bağlanmasını isteyin.|
|24|Smarconnectfailed|Konak sanal makinesinde RD Ağ Geçidi doğrudan bir görüş satırı yoktur. Ağ geçidi IP adresinin çözümlenebildiğinden emin olun.|
|1322|Connectionfailednomappingofsıdinad|Kullanıcı Active Directory üyesi değil. [Active Directory Yönetim Merkezi](/windows-server/identity/ad-ds/get-started/adac/active-directory-administrative-center) içindeki yönergeleri izleyerek bunları ekleyin.|

## <a name="next-steps"></a>Sonraki adımlar

Windows sanal masaüstü içindeki roller hakkında daha fazla bilgi için bkz. [Windows sanal masaüstü ortamı](environment-setup-2019.md).

Windows sanal masaüstü için kullanılabilir PowerShell cmdlet 'lerinin listesini görmek için [PowerShell başvurusuna](/powershell/windows-virtual-desktop/overview)bakın.
