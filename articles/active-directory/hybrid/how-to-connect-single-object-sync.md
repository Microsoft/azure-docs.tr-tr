---
title: 'Tek nesne eşitleme Azure AD Connect '
description: Sorun giderme için Active Directory bir nesneyi Azure AD 'ye eşitlemeyi öğrenin.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/19/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 373cebee4e7f95062791d8bc68bfee7d845e1465
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104726099"
---
# <a name="azure-ad-connect-single-object-sync"></a>Tek nesne eşitleme Azure AD Connect 

Tek nesne eşitleme aracı Azure AD Connect, tek bir nesneyi Active Directory Azure Active Directory ile eşitlemek için kullanılabilen bir PowerShell cmdlet 'i. Oluşturulan rapor, nesne eşitleme sorunlarını araştırmak ve sorunlarını gidermek için kullanılabilir. 

> [!NOTE]
> Araç Azure Active Directory Active Directory eşitlemeyi destekler. Azure Active Directory Active Directory ile eşitlemeyi desteklemez. 
>
> Araç, bir nesne değişikliği ekleme ve güncelleştirme eşitlemesini destekler. Bir nesne değişikliği silme işlemini eşitlemeyi desteklemez. 

## <a name="how-it-works"></a>Nasıl çalışır?
Tek nesne eşitleme aracı, kaynak bağlayıcısını ve içeri aktarma bölümünü bulmak için girdi olarak Active Directory ayırt edici bir ad gerektirir. Azure Active Directory değişiklikleri dışarı aktarır. Araç, **Provisioningobjectsummary** kaynak türüne benzer bir JSON çıkışı üretir. 

Tek nesne eşitleme aracı aşağıdaki adımları gerçekleştirir: 

 1. Nesnenin (kaynak) etki alanının (Active Directory Bağlayıcısı ve bölüm) eşitleme kapsamında olup olmadığını belirleme. 
 2. Nesne (hedef) etki alanının (Azure Active Directory Bağlayıcısı ve bölüm) eşitleme kapsamında olup olmadığını belirleme. 
 3. Nesnenin, eşitleme kapsamında kuruluş birimi olup olmadığını belirleme. 
 4. Nesnenin bağlayıcı hesabı kimlik bilgileri kullanılarak erişilebilir olup olmadığını belirleme. 
 5. Nesnenin eşitleme kapsamında olup olmadığını belirleme. 
 6. Grup filtrelemesi etkinse nesnenin eşitleme kapsamında olup olmadığını belirleme. 
 7. Nesneyi Active Directory Active Directory bağlayıcı alanına aktarın. 
 8. Nesneyi Azure Active Directory Azure Active Directory bağlayıcı alanına aktarın. 
 9. Nesneyi Active Directory bağlayıcı alanından eşitleyin. 
 10. Nesneyi Azure Active Directory bağlayıcı alanından Azure Active Directory 'ye aktarın. 

JSON çıkışına ek olarak, araç eşitleme işleminin tüm ayrıntılarına sahip bir HTML raporu oluşturur. HTML raporu **C:\programdata\aadconnect\adsyncobjectdiagnostics\ ADSyncSingleObjectSyncResult- <date> . htm** konumundadır. Bu HTML raporu, gerekirse daha fazla sorun giderme yapmak için destek ekibiyle paylaşılabilir. 

HTML raporunda şunlar vardır: 

|Tab|Description|
|-----|-----|
|Adımlar|Bir nesneyi eşitlemeye yönelik adımları özetler. Her adım sorun giderme için ayrıntılar içerir. Içeri aktarma, eşitleme ve dışarı aktarma adımları, ad, çok değerli, tür, değer, değer ekleme, değer silme, işlem, eşitleme kuralı, eşleme türü ve veri kaynağı gibi ek öznitelik bilgilerini içerir.| 
|& önerisi sorunlarını giderme|Hata kodunu ve nedenini sağlar. Hata bilgileri yalnızca bir hata oluşursa kullanılabilir.| 
|Değiştirilen özellikler|Eski değeri ve yeni değeri gösterir. Eski değer yoksa veya yeni değer silinirse, bu hücre boştur. Çok değerli öznitelikler için, sayıyı gösterir. Öznitelik adı, adımlar sekmesine bir bağlantıdır: nesne Azure Active Directory bağlayıcı alanından Azure Active Directory: ad, ad, tür, değer, değer ekleme, değer silme, işlem, eşitleme kuralı, eşleme türü ve veri kaynağı gibi özniteliğin ek ayrıntılarını içeren öznitelik bilgileri.| 
|Özet|Kaynak ve hedef sistemlerdeki nesne için ne olduğuna ve tanımlayıcılara bir genel bakış sağlar.| 

## <a name="prerequisites"></a>Önkoşullar 

Tek nesne eşitleme aracını kullanabilmeniz için, Azure AD Connect veya üzeri 2021 Mart sürümünü kullanmanız gerekir. 

### <a name="run-the-single-object-sync-tool"></a>Tek nesne eşitleme aracını çalıştırma 

Tek nesne eşitleme aracını çalıştırmak için aşağıdaki adımları gerçekleştirin: 

 1. Yönetici olarak çalıştır seçeneğiyle Azure AD Connect sunucunuzda yeni bir Windows PowerShell oturumu açın. 

 2. [Yürütme Ilkesini](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy) RemoteSigned veya Kısıtlanmamış olarak ayarlayın. 

 3. Hiçbir eşitleme işleminin çalışmadığını doğruladıktan sonra eşitleme zamanlayıcısını devre dışı bırakın. 

     `Set-ADSyncScheduler -SyncCycleEnabled $false` 

 4. AdSync tanılama modülünü içeri aktarma 

     `Import-module -Name "C:\Program Files\Microsoft Azure AD Sync\Bin\ADSyncDiagnostics\ADSyncDiagnostics.psm1"` 

 5. Tek nesne eşitleme cmdlet 'ini çağır. 

     `Invoke-ADSyncSingleObjectSync -DistinguishedName "CN=testobject,OU=corp,DC=contoso,DC=com" | Out-File -FilePath ".\output.json"` 

 6. Eşitleme zamanlayıcısını yeniden etkinleştirin. 

     `Set-ADSyncScheduler -SyncCycleEnabled $true`

|Tek nesne eşitleme giriş parametreleri|Description| 
|-----|----|
|DistinguishedName 'dir|Bu gerekli bir dize parametresidir. </br></br>Bu, eşitleme ve sorun giderme gerektiren Active Directory nesnenin ayırt edici adıdır.| 
|StagingMode|Bu, isteğe bağlı bir anahtar parametresidir.</br></br>Bu parametre Azure Active Directory değişikliklerinin verilmesini engellemek için kullanılabilir.</br></br>**Note**: cmdlet eşitleme işlemini yürütmeyecektir. </br></br>**Not**: Azure AD Connect hazırlama sunucusu, değişiklikleri Azure Active Directory dışarı aktarmaz.|
|NoHtmlReport|Bu, isteğe bağlı bir anahtar parametresidir.</br></br>Bu parametre, HTML raporunun oluşturulmasını engellemek için kullanılabilir. 

## <a name="single-object-sync-throttling"></a>Tek nesne eşitleme daraltma 

Tek nesne **Eşitleme Aracı,** nesne eşitleme sorunlarını araştırmaya ve sorun gidermeye yöneliktir. Zamanlayıcı tarafından çalıştırılan eşitleme döngüsünü değiştirmek **için tasarlanmamıştır.** Azure Active Directory ve dışarı aktarma Azure Active Directory, azaltma sınırlarına tabidir. Azaltma sınırına ulaştığınızda lütfen 5 dakika sonra yeniden deneyin. 

## <a name="next-steps"></a>Sonraki adımlar
- [Nesne eşitleme sorunlarını giderme](tshoot-connect-objectsync.md)
- [Eşitleme sırasında nesne sorunlarını giderme](tshoot-connect-object-not-syncing.md)
- [Azure AD Connect nesne ve özniteliklerin uçtan uca sorun giderme](https://docs.microsoft.com/troubleshoot/azure/active-directory/troubleshoot-aad-connect-objects-attributes)
