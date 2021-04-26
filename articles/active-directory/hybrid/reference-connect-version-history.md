---
title: 'Azure AD Connect: sürüm sürümü geçmişi | Microsoft Docs'
description: Bu makalede Azure AD Connect ve Azure AD Eşitleme tüm sürümleri listelenir.
services: active-directory
author: billmath
manager: daveba
ms.assetid: ef2797d7-d440-4a9a-a648-db32ad137494
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.date: 03/16/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f67bc46b4f612d3d2f377070d5d8280512e0e3df
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107576372"
---
# <a name="azure-ad-connect-version-release-history"></a>Azure AD Connect: Sürüm yayınlama geçmişi
Azure Active Directory (Azure AD) ekibi, yeni özellikler ve işlevlerle Azure AD Connect düzenli olarak güncelleştirir. Tüm eklemeler tüm izleyiciler için geçerli değildir.

Bu makale, yayımlanan sürümleri izlemenize ve değişikliklerin en son sürümde ne olduğunu anlamanıza yardımcı olmak için tasarlanmıştır.



Bu tablo, ilgili konuların listesidir:

Konu |  Ayrıntılar
--------- | --------- |
Azure AD Connect yükseltme adımları | [Önceki bir sürümden en son](how-to-upgrade-previous-version.md) Azure AD Connect sürüme yükseltmek için farklı yöntemler.
Gerekli izinler | Bir güncelleştirmeyi uygulamak için gereken izinler için bkz. [hesaplar ve izinler](reference-connect-accounts-permissions.md#upgrade).
İndir| [Azure AD Connect indirin](https://go.microsoft.com/fwlink/?LinkId=615771).

>[!NOTE]
>Azure AD Connect yeni bir sürümünü serbest bırakmak, hizmetin işlem işlevlerini sağlamak için çeşitli kalite denetimi adımı gerektiren bir işlemdir ve bu işlemden sonra yeni bir yayının sürüm numarası ve yayın durumu en son durumu yansıtacak şekilde güncelleştirilir.
Bu işlemden sonra, yayının sürüm numarası, "1.3. X. 0" içinde olduğu gibi küçük Yayın numarası konumunda bir "X" ile gösterilir. bu belgedeki sürüm notlarının, "1,3." ile başlayan tüm sürümler için geçerli olduğunu gösterir. Yayın işlemini sonuçlandırdıktan hemen sonra yayın sürümü numarası en son yayınlanan sürüme güncelleştirilir ve yayın durumu "indirme ve otomatik yükseltme için yayımlandı" olarak güncelleştirilir.
Azure AD Connect sürümlerinin hepsi otomatik yükseltme için kullanılabilir hale getirilmeyecektir. Sürüm durumu, bir yayının otomatik yükseltme veya yalnızca indirme için kullanılabilir duruma getirilmeyeceğini gösterir. Azure AD Connect sunucunuzda otomatik yükseltme etkinleştirildiyse, bu sunucu otomatik olarak yükseltme için yayınlanan en son Azure AD Connect sürümüne yükseltilir. Azure AD Connect yapılandırmalarının tümünün otomatik yükseltme için uygun olmadığına unutmayın. 

Otomatik yükseltmenin kullanımını netleştirmek için, tüm önemli güncelleştirmeleri ve kritik düzeltmeleri size göndermek amacıyla tasarlanmıştır. Tüm sürümlerin kritik güvenlik sorununa yönelik bir düzeltme gerektirmediğinden/içermediği için bu en son sürüm olması gerekir (yalnızca bir örnek vardır). Bu şekilde, otomatik yükseltme ile sunulan yeni bir sürüm ile ilgilenilecek bir sorun. Böyle bir sorun yoksa, otomatik yükseltme kullanılarak gönderilen hiçbir güncelleştirme yoktur ve genel olarak en son otomatik yükseltme sürümünü kullanıyorsanız, iyi durumda olmanız gerekir.
Bununla birlikte, tüm en son özellikleri ve güncelleştirmeleri beğenmezseniz, bu sayfayı kontrol etmenin en iyi yolu, uygun gördüğünüz gibi bunları yüklemektir. 

[Otomatik yükseltme](how-to-connect-install-automatic-upgrade.md) hakkında daha fazla bilgi için lütfen bu bağlantıyı izleyin

>[!IMPORTANT]
> 1 Nisan 2024 ' den itibaren, 1 Mayıs 2018-sürüm 1.1.751.0 ve daha eski bir sürümü ile yayınlanan Azure AD Connect sürümlerini devre dışı bırakacağız. 
>
> En iyi destek deneyimini almak için Azure AD Connect yeni bir sürümünü çalıştırdığınızdan emin olmanız gerekir. 
>
>Kullanımdan kaldırılan bir Azure AD Connect sürümü çalıştırırsanız en son güvenlik düzeltmeleri, performans iyileştirmeleri, sorun giderme ve tanılama araçları ve hizmet geliştirmeleri bulunmayabilir ve destek gerekiyorsa kuruluşunuzun ihtiyaç duyduğu hizmet düzeyini sağlayamayabilir.
>

>
>Azure AD Connect en son sürüme nasıl yükselteceğiniz hakkında daha fazla bilgi edinmek için lütfen [Bu makaleye](./how-to-upgrade-previous-version.md) bakın.
>
>Kullanımdan kaldırılan sürümlerde sürüm geçmişi bilgileri için bkz. [Azure AD Connect sürüm sürümü geçmişi Arşivi](reference-connect-version-history-archive.md)

## <a name="1640"></a>1.6.4.0

>[!NOTE]
> Azure AD Connect Sync v2 uç nokta API 'SI şu Azure ortamlarında kullanılabilir:
> - Azure ticari
> - Azure Çin bulutu
> - Azure ABD kamu bulutu Azure Almanya bulutu 'nda kullanıma sunulacaktır

### <a name="release-status"></a>Yayın durumu
3/31/2021: yalnızca indirme için yayınlandı, otomatik yükseltme için kullanılamaz

### <a name="bug-fixes"></a>Hata düzeltmeleri
- Bu sürüm, sürüm 1.6.2.4 ' deki bir hatayı düzeltir, bu yayına yükselttikten sonra Azure AD Connect Health özelliği doğru şekilde kaydedilmemiş ve çalışmadı. Derleme 1.6.2.4 dağıtan müşteriler, Azure AD Connect sunucusunu bu derleme ile güncelleştirmek istiyor ve bu durum, sistem durumu özelliğini doğru olarak kaydeder. 

## <a name="1624"></a>1.6.2.4
>[!IMPORTANT]
> Güncelleştirme 30 Mart 2021: Bu derlemede bir sorun keşfettik. Bu derlemeyi yükledikten sonra, sistem sağlığı hizmetleri kayıtlı değildir. Bu derlemeyi yüklememenizi öneririz. Kısa süre içinde bir düzeltme yayımlayacaktır.
> Bu derlemeyi zaten yüklediyseniz, [Bu makalede](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-agent-install#manually-register-azure-ad-connect-health-for-sync) gösterildiği gibi cmdlet 'Ini kullanarak sistem sağlığı hizmetlerini el ile kaydedebilirsiniz.

>[!NOTE]
> - Bu sürüm yalnızca indirileceği için kullanılabilir hale getirilir.
> - Bu sürüme yükseltme, eşitleme kuralı değişiklikleri nedeniyle tam eşitleme gerektirecektir.
> - Bu sürüm, AADConnect sunucusunu yeni v2 uç noktasına varsayılan olarak alır. Bu uç noktanın, Almanya Ulusal bulutu 'nda desteklenmediğini ve bu sürümü bu ortamda dağıtmanız gerekiyorsa, v1 uç noktasına geri dönmek için [Bu yönergeleri](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-endpoint-api-v2#rollback) izlemeniz gerekir. Bunun yapılmaması eşitlemede hatalara neden olur.

### <a name="release-status"></a>Yayın durumu
3/19/2021: karşıdan yüklenmek üzere yayınlandı, otomatik yükseltme için kullanılamaz

### <a name="functional-changes"></a>İşlevsel değişiklikler

 - Yazılan geri gruplardaki üyeliği 50.000 üyelerine sınırlamak için varsayılan eşitleme kuralları güncelleştirildi.
   - Grup geri yazma işleminde üyelik sayısını sınırlandırmak için yeni varsayılan eşitleme kuralları eklendi (AD-grup geri yazma üye sınırına kadar) ve grup eşitlemesi Azure Active Directory (AAD grubu writeup üye sınırı) grupları.
   - Yazılan geri gruplardaki üyeleri 50.000 'a sınırlamak için ' Out to ad-Group soainaad-Exchange ' kuralına üye özniteliği eklendi
 - Eşitleme kuralları, grup geri yazma v2 'yi destekleyecek şekilde güncelleştirildi-"from AAD-Group SOAInAAD" kuralı klonlandığında ve AADConnect yükseltildikten sonra.
     -Güncelleştirilmiş kural varsayılan olarak devre dışı bırakılacak, bu nedenle targetWritebackType null olacaktır.
     - AADConnect, dağıtım grupları olarak tüm bulut gruplarını (geri yazma için etkinleştirilen Azure Active Directory güvenlik grupları dahil) geri alacak.
   -"AD-grup SOAInAAD" kuralı klonlanır ve AADConnect yükseltilir.
     - Güncelleştirilmiş kural varsayılan olarak devre dışı bırakılır. Ancak, eklenen yeni bir "AD-grup SOAInAAD-Exchange" eşitleme kuralı etkinleştirilecek.
     - Kopyalanmış özel eşitleme kuralının önceliğine bağlı olarak, AADConnect posta ve Exchange özniteliklerini de akacaktır.
     - Kopyalanmış özel eşitleme kuralı bazı posta ve Exchange özniteliklerini akiçermiyorsa, yeni Exchange Eşitleme kuralı bu öznitelikleri ekler.
 - [Seçmeli Parola karması eşitleme](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-selective-password-hash-synchronization) desteği eklendi
 - Yeni [tek nesne eşitleme cmdlet 'i](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-single-object-sync)eklendi. Azure AD Connect eşitleme yapılandırmanızın sorunlarını gidermek için bu cmdlet 'i kullanın. 
 -  Azure AD Connect artık hizmeti yapılandırmak için karma kimlik yöneticisi rolünü desteklemektedir.
 - Aadconnectheauagent 3.1.83.0 olarak güncelleştirildi
 - Çok sayıda yeni veya geliştirilmiş cmdlet içeren [Adsynctools PowerShell modülünün](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-adsynctools)yeni sürümü. 
 
   - Clear-ADSyncToolsMsDsConsistencyGuid
   - ConvertFrom-ADSyncToolsAadDistinguishedName
   - ConvertFrom-ADSyncToolsImmutableID
   - ConvertTo-ADSyncToolsAadDistinguishedName
   - ConvertTo-ADSyncToolsCloudAnchor
   - ConvertTo-ADSyncToolsImmutableID
   - Export-ADSyncToolsAadDisconnectors
   - Export-ADSyncToolsObjects
   - Export-ADSyncToolsRunHistory
   - Get-ADSyncToolsAadObject
   - Get-ADSyncToolsMsDsConsistencyGuid
   - Import-ADSyncToolsObjects
   - Import-ADSyncToolsRunHistory
   - Remove-ADSyncToolsAadObject
   - Search-ADSyncToolsADobject
   - Set-ADSyncToolsMsDsConsistencyGuid
   - Trace-ADSyncToolsADImport
   - Trace-ADSyncToolsLdapQuery

 - Belirteç alma hataları için hata günlüğü güncelleştirildi.
 - Bağlantılı bilgiler hakkında daha fazla ayrıntı sağlamak için yapılandırma sayfasındaki ' daha fazla bilgi ' bağlantısı güncelleştirildi.
 - Eski eşitleme Kullanıcı arabirimindeki CS arama sayfasından açık sütun kaldırıldı
 - Daha önceki bir adımda kimlik bilgileri sağlanmadıysa, kullanıcıdan kimlik bilgilerini istemek veya ADSyncConfig modülünü kullanarak kendi izinlerini yapılandırmak için Grup geri yazma akışına ek kullanıcı arabirimi eklenmiştir.
 - Bir DC 'de ADSync hizmet hesabı için MSA 'yi otomatik olarak oluşturun. 
 -  Mevcut cmdlet 'lerde Azure Active Directory DirSync özellik grubu geri yazma v2 'yi ayarlama ve alma özelliği eklendi:
    - Set-ADSyncAADCompanyFeature
    - Get-ADSyncAADCompanyFeature
 - AWS API sürümünü okumak için 2 cmdlet eklendi
    - Get-ADSyncAADConnectorImportApiVersion-AWS API sürümünü içeri aktarma
    - Get-ADSyncAADConnectorExportApiVersion-AWS API sürümünü dışarı aktarmayı almak için

 - Eşitleme kurallarında yapılan değişiklikler artık hizmette sorun gidermeye yardımcı olmak için izlenir. "Get-ADSyncRuleAudit" cmdlet 'i izlenen değişiklikleri alacaktır.
 - ADSyncAdmin grubundaki bir kullanıcının AD DS bağlayıcı hesabını değiştirmesine izin vermek için [Adsyncconfig PowerShell modülündeki](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account#using-the-adsyncconfig-powershell-module) Add-ADSyncADDSConnectorAccount cmdlet 'i güncelleştirildi. 

### <a name="bug-fixes"></a>Hata düzeltmeleri
 - Beyaz bir arka planda daha fazla parlaklık gereksinimini karşılamak için devre dışı ön plan rengi güncelleştirildi. Renk parlaklığını karşılamak için devre dışı bir sayfa seçildiğinde, ön plan metin rengini beyaza ayarlamak için gezinti ağacı için ek koşullar eklendi.
 - Set-ADSyncPasswordHashSyncPermissions cmdlet 'i için ayrıntı düzeyini artırın (set-ADSyncPasswordHashSyncPermissions), isteğe bağlı "ADobjectDN" parametresini içerecek şekilde güncelleştirildi. 
 - Erişilebilirlik hata çözümü. Ekran okuyucu artık, ormanlar listesini "**orman listesi listesi**" yerine "**Ormanlar listesi**" olarak tutan UX öğesini tanımlıyor.
 - Azure AD Connect sihirbazındaki bazı öğeler için ekran okuyucusu çıkışı güncelleştirildi. Kontrast gereksinimlerini karşılamak için düğme üzerine gelme rengi güncelleştirildi. Karşıtlık gereksinimlerinin yerine getirmek için Synchronization Service Manager başlık rengi güncelleştirildi.
 - Özel uzantı özniteliklerine sahip olan, zaman uyumlu yapılandırmadan AADConnect 'i yükleme ile ilgili bir sorun düzeltildi-eşitleme kuralı uygulanırken hedef şemadaki uzantı özniteliklerinin denetlenmesini atlamak için bir koşul eklediniz.
 - Grup geri yazma özelliği etkinse, yüklemeye uygun izinler eklenir.
 - İçeri aktarma sırasında yinelenen varsayılan eşitleme kuralı önceliğini düzeltir
 - Sistem durumu portalı aracılığıyla onarılan çakışan bir nesne için v2 API Delta içeri aktarma sırasında hazırlama hatasına neden olan bir sorun düzeltildi.
 - Eşitleme altyapısında, CS nesnelerinin tutarsız bir bağlantı durumuna sahip olması nedeniyle oluşan bir sorun düzeltildi
 - Get-ADSyncConnectorStatistics çıkışına içeri aktarma sayaçları eklendi.
 - Pass2 Sihirbazı sırasında bazı köşe durumlarında, erişilebilir etki alanı devre dışı (daha önce seçili) sorunu düzeltildi.
 - Özel kuralda Yinelenen öncelik varsa, ilke içeri ve dışarı aktarma işlemi başarısız olur 
 - Etki alanı seçim mantığındaki bir hata düzeltildi.
 - Kaynak bağlantısı olarak mS-DS-ımbu GUID kullanırsanız ve AD-grup birleşimi kuralından Içine klonlamak için Build 1.5.18.0 ile ilgili bir sorunu düzeltir.
 - Yeni AADConnect yüklemeleri, kullanılabilir bir tane varsa ve farklı bir tane geçirilmemişse, bulutta depolanan dışarı aktarma silme eşiğini kullanır.
 - AADConnect 'in karma olarak birleştirilmiş cihazların AD displayName değişikliklerini okumadığı sorun düzeltildi

## <a name="15450"></a>1.5.45.0

### <a name="release-status"></a>Yayın durumu
07/29/2020: indirilmek üzere yayınlandı

### <a name="functional-changes"></a>İşlevsel değişiklikler
Bu bir hata çözme sürümüdür. Bu sürümde işlevsel değişiklik yok.

### <a name="fixed-issues"></a>Düzeltilen sorunlar

- "Active Directory" içinde AZUREADSSOACC bilgisayar hesabı zaten mevcutsa, yöneticinin "sorunsuz çoklu oturum açma" özelliği etkinleştiremediğinde sorun düzeltildi.
- Sistem durumu portalı aracılığıyla onarılan çakışan bir nesne için v2 API Delta içeri aktarma sırasında hazırlama hatasına neden olan bir sorun düzeltildi.
- İçeri/dışarı aktarma yapılandırmasında devre dışı özel kuralın etkin olduğu şekilde içe aktarıldığı bir sorun düzeltildi.

## <a name="15420"></a>1.5.42.0

### <a name="release-status"></a>Yayın durumu
07/10/2020: indirilmek üzere yayınlandı

### <a name="functional-changes"></a>İşlevsel değişiklikler
Bu sürüm, mevcut bir Azure AD Connect sunucusunun yapılandırmasını ' a dışarı aktarma işlevselliğinin genel önizlemesini içerir. Ardından, özgün sunucunun bir kopyasını oluşturmak için yeni bir Azure AD Connect sunucusu yüklenirken kullanılabilen JSON dosyası.

Bu yeni özelliğin ayrıntılı bir açıklaması [Bu makalede](./how-to-connect-import-export-config.md) bulunabilir

### <a name="fixed-issues"></a>Düzeltilen sorunlar
- Yükseltme sırasında yerelleştirilmiş derlemelerde yerel DB boyutuyla ilgili yanlış bir uyarı olacağı bir hata düzeltildi.
- Hesap adı/etki alanı adı takas için uygulama olaylarında yanlış bir hata olduğu bir hata düzeltildi.
- Azure AD Connect DC 'ye yüklenemediğinde bir hata düzeltildi, "üye bulunamadı" hatası veriliyor.


## <a name="15300"></a>1.5.30.0

### <a name="release-status"></a>Yayın durumu
05/07/2020: indirilmek üzere yayınlandı

### <a name="fixed-issues"></a>Düzeltilen sorunlar
Bu düzeltme derlemesi, seçilmemiş etki alanlarının yalnızca bir alt kapsayıcı seçildiyse sihirbaz kullanıcı arabiriminden yanlış bir şekilde seçili olduğu bir sorunu düzeltir.


>[!NOTE]
>Bu sürüm, yeni Azure AD Connect Sync v2 uç nokta API 'sini içerir.  Bu yeni v2 uç noktası şu anda genel önizlemededir.  Yeni v2 uç nokta API 'sini kullanmak için bu sürüm veya üzeri gereklidir.  Ancak, yalnızca bu sürümü yüklemek v2 uç noktasını etkinleştirmez. V2 uç noktasını etkinleştirmediğiniz takdirde v1 uç noktasını kullanmaya devam edersiniz.  Bunu etkinleştirmek ve genel önizlemeyi kabul etmek için [Azure AD Connect Sync v2 ENDPOINT API (Genel Önizleme)](how-to-connect-sync-endpoint-api-v2.md) altındaki adımları izlemeniz gerekir.  

## <a name="15290"></a>1.5.29.0

### <a name="release-status"></a>Yayın durumu
04/23/2020: indirilmek üzere yayınlandı

### <a name="fixed-issues"></a>Düzeltilen sorunlar
Bu düzeltme derlemesi, MFA 'ya sahip bir kiracı yöneticisinin DSSO 'yi etkinleştiremediğinden Build 1.5.20.0 'ta tanıtılan bir sorunu düzeltir.

## <a name="15220"></a>1.5.22.0

### <a name="release-status"></a>Yayın durumu
04/20/2020: indirilmek üzere yayınlandı

### <a name="fixed-issues"></a>Düzeltilen sorunlar
Bu düzeltme derlemesi, **' ın ad-grup birleşimi** kuralından klonlanmış ve **' ın ad-grup ortak** kuralından kopyalanmamış olması halinde derleme 1.5.20.0 'deki bir sorunu düzeltir.

## <a name="15200"></a>1.5.20.0

### <a name="release-status"></a>Yayın durumu
04/09/2020: indirilmek üzere yayınlandı

### <a name="fixed-issues"></a>Düzeltilen sorunlar
- Bu düzeltme derlemesi, Grup filtreleme özelliği etkinse ve kaynak bağlantısı olarak mS-DS-IM1.5.18.0 GUID kullanıyorsanız derleme ile ilgili bir sorunu düzeltir.
- ADSyncConfig PowerShell modülündeki bir sorun düzeltildi, burada set-ADSync * Permissions cmdlet 'lerinde kullanılan DSACLS komutunun çağrılması aşağıdaki hatalardan birine neden olur:
     - `GrantAclsNoInheritance : The parameter is incorrect.   The command failed to complete successfully.`
     - `GrantAcls : No GUID Found for computer …`

> [!IMPORTANT]
> **' Yı ad grubu Ile Birleştir** eşitleme kuralından klondıysanız ve ' ı **ad-grup ortak** eşitleme kuralıyla klonlamadıysanız ve yükseltmeyi planlarsanız, yükseltmenin bir parçası olarak aşağıdaki adımları izleyin:
> 1. Yükseltme sırasında, **yapılandırma tamamlandığında eşitleme Işlemini Başlat** seçeneğinin işaretini kaldırın.
> 2. Kopyalanmış JOIN eşitleme kuralını düzenleyin ve aşağıdaki iki dönüşümü ekleyin:
>     - Doğrudan akışı `objectGUID` olarak ayarlayın `sourceAnchorBinary` .
>     - İfade akışını `ConvertToBase64([objectGUID])` olarak ayarlayın `sourceAnchor` .     
> 3. Kullanarak Scheduler 'ı etkinleştirin `Set-ADSyncScheduler -SyncCycleEnabled $true` .



## <a name="15180"></a>1.5.18.0

### <a name="release-status"></a>Yayın durumu
04/02/2020: indirilmek üzere yayınlandı

### <a name="functional-changes-adsyncautoupgrade"></a>İşlevsel değişiklikler ADSyncAutoUpgrade 

- Grup nesnelerine yönelik mS-DS-ımlarımguıd özelliği için destek eklendi. Bu, grupları ormanlar arasında taşımanızı veya ad grubu ObjectID 'nin değiştiği Azure AD 'ye, örneğin bir Calamity sonrasında bir AD sunucusu yeniden oluşturulduğunda Azure AD 'ye yeniden bağlantı kurmanıza olanak tanır. Daha fazla bilgi için bkz. [grupları ormanlar arasında taşıma](how-to-connect-migrate-groups.md).
- MS-DS-ımbu Guid özniteliği tüm eşitlenmiş gruplar üzerinde otomatik olarak ayarlanır ve bu özelliği etkinleştirmek için herhangi bir şey yapmanız gerekmez. 
- Artık kullanımda olmadığından Get-ADSyncRunProfile kaldırıldı. 
- Daha fazla bağlam sağlamak üzere AD DS bağlayıcı hesabı için bir kurumsal yönetici veya etki alanı yönetici hesabı kullanmaya çalışırken gördüğünüz uyarıyı değiştirdi. 
- Bağlayıcı alanından nesneleri kaldırmak için yeni bir cmdlet eklendi eski CSDelete.exe Aracı kaldırılır ve yeni Remove-ADSyncCSObject cmdlet 'i ile değiştirilmiştir. Remove-ADSyncCSObject cmdlet 'i bir CsObject öğesini giriş olarak alır. Bu nesne Get-ADSyncCSObject cmdlet 'i kullanılarak alınabilir.

>[!NOTE]
>Eski CSDelete.exe aracı kaldırılmıştır ve yeni Remove-ADSyncCSObject cmdlet 'i ile değiştirilmiştir 

### <a name="fixed-issues"></a>Düzeltilen sorunlar

- Özelliği devre dışı bıraktıktan sonra Azure AD Connect Sihirbazı 'nı yeniden çalıştırmak için Grup geri yazma ormanı/OU seçicisinde hata düzeltildi. 
- Gerekli DCOM kayıt defteri değerlerinin yeni bir yardım bağlantısıyla eksik olması halinde görüntülenecek yeni bir hata sayfası eklendi. Bilgiler günlük dosyalarına da yazılır. 
- Hesap, kullanılmadan önce tüm hizmet Çoğaltmalarından yayılmadığından, Dizin uzantılarının veya PHS 'nin etkinleştirilmesinde Azure Active Directory eşitleme hesabının oluşturulmasına neden olan bir sorun düzeltildi. 
- Yedek karakterleri doğru bir şekilde işlemeyen Eşitleme hataları sıkıştırma yardımcı programında hata düzeltildi. 
- Otomatik yükseltmede bir hata düzeltildi ve bu durum, Scheduler askıya alınma durumunda sunucuyu bıraktı. 

## <a name="14380"></a>1.4.38.0
### <a name="release-status"></a>Yayın durumu
12/9/2019: indirme için yayın. Otomatik yükseltme ile kullanılamaz.
### <a name="new-features-and-improvements"></a>Yeni özellikler ve geliştirmeler
- Azure AD Domain Services için Parola karması eşitlemesini, Kerberos karmalarının doldurmaya yönelik doğru hesaba göre güncelleştirdik.  Bu, Azure AD 'den Azure AD Domain Services 'ye parola eşitleme sırasında bir performans geliştirmesi sağlar.
- Kimlik doğrulama Aracısı ve Service Bus arasındaki güvenilir oturumlar için destek ekledik.
- Bu sürüm, kimlik doğrulama Aracısı ve bulut hizmetleri arasındaki iletişim için TLS 1,2 zorlar.
- Kimlik doğrulama Aracısı ve bulut hizmetleri arasında WebSocket bağlantıları için bir DNS önbelleği ekledik.
- Aracı bağlantısını test etmek için belirli bir aracıyı buluttan hedefleyebilme özelliği ekledik.

### <a name="fixed-issues"></a>Düzeltilen sorunlar
- Release 1.4.18.0, DSSO PowerShell cmdlet 'inin PS çalıştırırken belirtilen yönetici kimlik bilgileri yerine oturum açma Windows kimlik bilgilerini kullandığı bir hata yaşadı. Bunun sonucunda, Azure AD Connect Kullanıcı arabirimi aracılığıyla birden çok ormanda DSSO 'yu etkinleştirmek mümkün değildir. 
- Azure AD Connect Kullanıcı arabirimi aracılığıyla tüm ormanlarda aynı anda DSSO 'yu etkinleştirmek için bir çözüm yapıldı

## <a name="14320"></a>1.4.32.0
### <a name="release-status"></a>Yayın durumu
11/08/2019: indirilmek üzere yayınlandı. Otomatik yükseltme ile kullanılamaz.

>[!IMPORTANT]
>Bu Azure AD Connect sürümünde bir iç şema değişikliği nedeniyle, MSOnline PowerShell kullanarak AD FS güven ilişkisi yapılandırma ayarlarını yönetiyorsanız, MSOnline PowerShell modülünüzü 1.1.183.57 veya üzeri sürümüne güncelleştirmeniz gerekir

### <a name="fixed-issues"></a>Düzeltilen sorunlar

Bu sürüm, mevcut karma Azure AD 'ye katılmış cihazlarda bir sorunu düzeltir. Bu sürüm, bu sorunu gideren yeni bir cihaz eşitleme kuralı içerir.
Bu kural değişikliğinin, kullanılmayan cihazların Azure AD 'den silinmesine neden olabileceğini unutmayın. Bu cihaz nesneleri, koşullu erişim yetkilendirmesi sırasında Azure AD tarafından kullanılmadığından bu sorun için bir neden değildir. Bazı müşteriler için, bu kural değişikliği üzerinden silinecek cihaz sayısı, silme eşiğini aşabilir. Azure AD 'de cihaz nesneleri silme işlemini dışarı aktarma silme eşiğini aştıktan sonra, silme işlemlerinin devam eden bir şekilde tamamlanmasına izin verilmesi önerilir. [Silme eşiğini aştıklarında akışa silme izni verme](how-to-connect-sync-feature-prevent-accidental-deletes.md)

## <a name="14250"></a>1.4.25.0

### <a name="release-status"></a>Yayın durumu
9/28/2019: kiracı seçmek için otomatik yükseltme için yayınlandı. İndirileceği için kullanılamaz.

Bu sürüm, önceki sürümden 1.4.18.0 'e otomatik olarak yükseltilen bazı sunucuların, self servis parola sıfırlama (SSPR) ve parola geri yazma ile ilgili sorun yaşadığı bir hatayı düzeltir.

### <a name="fixed-issues"></a>Düzeltilen sorunlar

Belirli koşullarda, 1.4.18.0 sürümüne otomatik olarak yükseltilen sunucular, yükseltme tamamlandıktan sonra self servis parola sıfırlama ve parola geri yazma özelliğini yeniden etkinleştirmemiştir. Bu otomatik yükseltme sürümü, self servis parola sıfırlama ve parola geri yazma sorununu gidermeye ve yeniden etkinleştirmesine izin veren düzeltmeleri.

Yedek karakterleri doğru bir şekilde işlemeyen Eşitleme hataları sıkıştırma yardımcı programında bir hata düzeltildi.

## <a name="14180"></a>1.4.18.0

>[!WARNING]
>Bu Azure AD Connect sürümüne yükselttikten sonra bazı müşterilerin mevcut karma Azure AD 'ye katılmış cihazlarda bir sorunla karşılaşmakta olduğu bir olay araştırıyoruz. Bu sorunların temel nedeni tam olarak anlaşılana ve hafiflene kadar, karma Azure AD JOIN 'i dağıtan müşterilere bu sürüme yükseltmeyi erteleyin. Daha fazla bilgi mümkün olan en kısa sürede sunulacaktır.

>[!IMPORTANT]
>Bu Azure AD Connect, bazı müşteriler Windows cihazlarının bazı veya tümünün Azure AD 'den kaybolabileceğini görebilir. Bu cihaz kimlikleri, koşullu erişim yetkilendirmesi sırasında Azure AD tarafından kullanılmadığından bu sorun için bir neden değildir. Daha fazla bilgi için bkz [. Azure AD Connect 1.4. xx. x Device disappearnce 'ı anlama](reference-connect-device-disappearance.md)


### <a name="release-status"></a>Yayın durumu
9/25/2019: yalnızca otomatik yükseltme için yayınlandı.

### <a name="new-features-and-improvements"></a>Yeni özellikler ve geliştirmeler
- Yeni sorun giderme araçları, "kullanıcının eşitlemesine yönelik", "Grup eşitlenmiyor" veya "grup üyesi eşitlenmiyor" senaryolarındaki sorunları gidermeye yardımcı olur.
- Azure AD Connect sorun giderme betiğine National bulutlar desteği ekleyin.
- Müşteriler MIIS_Service için kullanımdan kaldırılan WMI bitiş noktaları kaldırılmıştır. Tüm WMI işlemleri artık PS cmdlet 'leri aracılığıyla yapılmalıdır.
- AZUREADSSOACC nesnesinde kısıtlanmış temsilciyi sıfırlayarak güvenlik iyileştirmesi.
- Bir eşitleme kuralı eklerken/düzenlenirken, kuralda bağlayıcı şemasında bulunan ancak bağlayıcıya eklenmemiş öznitelikler varsa, bu öznitelikler otomatik olarak bağlayıcıya eklenir. Kuralın etkilediği nesne türü için de aynı değer geçerlidir. Bağlayıcıya herhangi bir şey eklenirse, bağlayıcı sonraki eşitleme döngüsüne tam içeri aktarma işlemi için işaretlenir.
- Yeni Azure AD Connect dağıtımlarında, bağlayıcı hesabı olarak bir kuruluş veya etki alanı yöneticisi kullanmak artık desteklenmemektedir. Bağlayıcı hesabı olarak bir kuruluş veya etki alanı Yöneticisi kullanan geçerli Azure AD Connect dağıtımları Bu sürümden etkilenmez.
- Eşitleme yöneticisinde, kural oluşturma/düzenleme/silme üzerinde tam eşitleme çalıştırılır. Tam içeri aktarma veya tam eşitleme çalışmaya devam ederseniz, kullanıcıya bildiren bir açılan pencere, herhangi bir kural değişikliği görünür.
- Parola hatalarının "bağlayıcılar > Özellikler > bağlantı" sayfasına yönelik risk azaltma adımları eklendi.
- Bağlayıcı Özellikleri sayfasında Sync Service Manager için kullanımdan kaldırma uyarısı eklendi. Bu uyarı, kullanıcıya Azure AD Connect Sihirbazı aracılığıyla değişiklikler yapılması gerektiğini bildirir.
- Kullanıcının parola ilkesiyle ilgili sorunlar için yeni hata eklendi.
- Etki alanı ve OU filtrelerine göre grup filtrelemesinin yanlış yapılandırılmasını önleyin. Girilen grubun etki alanı/OU 'su zaten filtrelenirse ve sorun çözümlenene kadar kullanıcının ileri doğru hareket etmesini önlemek için Grup filtreleme bir hata gösterir.
- Kullanıcılar artık Synchronization Service Manager Kullanıcı arabirimindeki Active Directory Domain Services veya Windows Azure Active Directory için bağlayıcı oluşturamaz.
- Synchronization Service Manager özel UI denetimleri için sabit erişilebilirlik.
- Azure AD Connect 'de tüm oturum açma yöntemleri için altı Federasyon yönetim görevi etkinleştirildi.  (Daha önce, tüm oturum açma işlemleri için yalnızca "güncelleştirme AD FS TLS/SSL sertifikası" görevi mevcuttur.)
- Tüm Azure AD etki alanlarının ve kullanıcılarının yönetilen kimlik doğrulamasına dönüştürüleceği, Federasyon 'dan PHS 'ye veya PTA 'ya oturum açma yöntemi değiştirilirken bir uyarı eklendi.
- "Azure AD ve AD FS güveni sıfırlama" görevinden belirteç imzalama sertifikaları kaldırıldı ve bu sertifikaları güncelleştirmek için ayrı bir alt görev eklendi.
- AD FS grubuna yönelik TLS veya belirteç imzalama sertifikalarını güncelleştirmek için alt görevler içeren "sertifikaları Yönet" adlı yeni bir Federasyon yönetim görevi eklendi.
- Yöneticilerin AD FS grubu için yeni bir birincil sunucu belirtmesini sağlayan "birincil sunucu belirtin" adlı yeni bir Federasyon yönetimi alt görevi eklendi.
- Bir AD FS sunucusu dağıtmak, bir Web uygulaması ara sunucusu dağıtmak ve birincil sunucuyu belirtmek için alt görevler içeren "sunucuları Yönet" adlı yeni bir Federasyon yönetim görevi eklendi.
- Geçerli AD FS ayarlarını görüntüleyen "Federasyon yapılandırmasını görüntüle" adlı yeni bir Federasyon yönetim görevi eklendi.  (Bu ek olarak, AD FS ayarları "çözümünüzü gözden geçirin" sayfasından kaldırılmıştır.)

### <a name="fixed-issues"></a>Düzeltilen sorunlar
- Kendisine karşılık gelen kişi nesnesini alan bir Kullanıcı nesnesinin kendi kendine başvurmasının (ör. Kullanıcı kendi Yöneticisi olduğu) senaryoya yönelik eşitleme hatası sorunu çözüldü.
- Yardım açılanları şimdi klavye odağında gösteriliyor.
- Otomatik yükseltme için, 6 saatten daha fazla çakışan uygulama çalışıyorsa, sonlandırın ve yükseltmeye devam edin.
- Dizin uzantıları seçerken bir müşterinin nesne başına 100 ' i seçmesi için öznitelik sayısını sınırlayın. Bu, Azure 'un nesne başına en fazla 100 uzantı özniteliklerine sahip olduğu için hata verme sırasında oluşmasını engeller.
- AD bağlantı betiğini daha sağlam hale getirmek için bir hata düzeltildi.
- Mevcut bir adlandırılmış yöneltme WCF hizmetini daha sağlam bir makineye yüklemek Azure AD Connect bir hata düzeltildi.
- İlk yüklendiğinde ADSync hizmetine izin verilmeyen Grup ilkeleri etrafında geliştirilmiş Tanılamalar ve sorun giderme.
- Bir Windows bilgisayarı için görünen adın yanlış yazıldığı bir hata düzeltildi.
- Bir Windows bilgisayarı için işletim sistemi türünün yanlış yazıldığı bir hatayı düzeltir.
- Windows 10 olmayan bilgisayarların beklenmedik şekilde eşitlendiğinden oluşan hata düzeltildi. Bu değişikliğin etkisinin, daha önce eşitlenen Windows-10 olmayan bilgisayarların artık silineceğini unutmayın. Bu, Windows bilgisayarlarının eşitlenmesi yalnızca Windows-10 cihazlarında kullanılan karma Azure AD etki alanına katılması için kullanıldığından, hiçbir özelliği etkilemez.
- ADSync PowerShell modülüne birkaç yeni (iç) cmdlet eklendi.


## <a name="13210"></a>1.3.21.0
>[!IMPORTANT]
>Daha önceki bir sürümden Azure AD Connect yükseltirken, Microsoft 365 portalın Azure AD Connect başarıyla yükseltilmesine rağmen güncelleştirilmiş sürümü yansıtmadığından oluşan bilinen bir sorun vardır.
>
> Bu sorunu çözmek için **ADSync** modülünü içeri aktarmanız ve sonra `Set-ADSyncDirSyncConfiguration` Azure AD Connect sunucusunda PowerShell cmdlet 'ini çalıştırmanız gerekir.  Aşağıdaki adımları kullanabilirsiniz:
>
>1. PowerShell 'i yönetici modunda açın.
>2. `Import-Module "ADSync"` öğesini çalıştırın.
>3. `Set-ADSyncDirSyncConfiguration -AnchorAttribute ""` öğesini çalıştırın.
 
### <a name="release-status"></a>Yayın durumu 

05/14/2019: indirilmek üzere yayınlandı

### <a name="fixed-issues"></a>Düzeltilen sorunlar 

- Microsoft Azure Active Directory Connect Build 1.3.20.0 'ta bulunan ayrıcalık yükselmesi güvenlik açığı düzeltildi.  Bu güvenlik açığı belirli koşullar altında, bir saldırganın ayrıcalıklı bir hesap bağlamında iki PowerShell cmdlet 'i yürütmesine ve ayrıcalıklı eylemler gerçekleştirmesine izin verebilir.  Bu güvenlik güncelleştirmesi, bu cmdlet 'leri devre dışı bırakarak sorunu giderir. Daha fazla bilgi için bkz. [güvenlik güncelleştirmesi](https://portal.msrc.microsoft.com/security-guidance/advisory/CVE-2019-1000).


## <a name="next-steps"></a>Sonraki adımlar
[Şirket içi kimliklerinizi Azure Active Directory ile tümleştirme](whatis-hybrid-identity.md) hakkında daha fazla bilgi edinin.
