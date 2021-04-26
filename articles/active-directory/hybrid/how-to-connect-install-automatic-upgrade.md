---
title: 'Azure AD Connect: otomatik yükseltme | Microsoft Docs'
description: Bu konuda Azure AD Connect eşitleme ' deki yerleşik otomatik yükseltme özelliği açıklanmaktadır.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 6b395e8f-fa3c-4e55-be54-392dd303c472
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/09/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3ec237af8cd0c79d5a7b62aad0bc6521e5cf3d7e
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106059250"
---
# <a name="azure-ad-connect-automatic-upgrade"></a>Azure AD Connect: Otomatik yükseltme
Azure AD Connect otomatik yükseltme, Azure AD Connect yeni sürümlerini düzenli olarak denetleyen bir özelliktir. Sunucunuz otomatik yükseltme için etkinleştirilmişse ve sunucunuzun uygun olduğu daha yeni bir sürüm bulunursa, bu yeni sürüme otomatik olarak yükseltme gerçekleştirir.
Otomatik yükseltmeyi gerçekleştiren aracının, indirilen sürümün dijital imzasına göre Azure AD Connect yeni derlemesini doğruladığını unutmayın.

## <a name="overview"></a>Genel Bakış
Azure AD Connect yüklemenizin her zaman güncel olduğundan emin olmak, **otomatik yükseltme** özelliğiyle hiçbir zaman daha kolay olmamıştı. Bu özellik, hızlı yüklemeler ve DirSync yükseltmeleri için varsayılan olarak etkindir. Yeni bir sürüm yayınlandığında, yüklemeniz otomatik olarak yükseltilir.
Otomatik yükseltme, aşağıdakiler için varsayılan olarak etkindir:

* Hızlı ayarlar yükleme ve DirSync yükseltmeleri.
* Express ayarlarının her zaman kullandığı SQL Express LocalDB 'yi kullanma. SQL Express ile DirSync, LocalDB de kullanır.
* AD hesabı, Hızlı ayarlar ve DirSync tarafından oluşturulan varsayılan MSOL_ hesabıdır.
* Meta veri deposunda 100.000 ' den az nesne var.

Otomatik yükseltmenin geçerli durumu PowerShell cmdlet 'i ile görüntülenebilir `Get-ADSyncAutoUpgrade` . Aşağıdaki durumlara sahiptir:

| Durum | Yorum |
| --- | --- |
| Etkin |Otomatik yükseltme etkindir. |
| Askıya alındı |Yalnızca sistem tarafından ayarlanır. Sistem **Şu anda** Otomatik yükseltmeleri almaya uygun değil. |
| Devre dışı |Otomatik yükseltme devre dışı bırakıldı. |

İle **etkin** ve **devre dışı** arasında geçiş yapabilirsiniz `Set-ADSyncAutoUpgrade` . Yalnızca sistem durumu **askıya alındı** olarak ayarlanmalıdır.  1.1.750.0 'ten önce, otomatik yükseltme durumu askıya alındı olarak ayarlandıysa Set-ADSyncAutoUpgrade cmdlet 'i otomatik yükseltmeyi engeller. Bu işlev artık, yeniden yükseltme engellenmemesi için değişti.

Otomatik yükseltme, yükseltme altyapısı için Azure AD Connect Health kullanıyor. Otomatik yükseltmenin çalışması için, proxy sunucunuzdaki URL 'Leri [Office 365 URL 'lerinde ve IP adresi aralıklarında](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)belgelendiği gibi **Azure AD Connect Health** açtığınızdan emin olun.


**Synchronization Service Manager** Kullanıcı arabirimi sunucuda çalışıyorsa, Kullanıcı arabirimi kapatılıncaya kadar yükseltme askıya alınır.

## <a name="troubleshooting"></a>Sorun giderme
Connect yüklemenizin kendisi beklendiği gibi yükseltmiyorsa, nelerin yanlış olduğunu öğrenmek için aşağıdaki adımları izleyin.

İlk olarak, otomatik yükseltmenin yeni bir sürüm Yayınlanma ilk günde denenmeye yönelik olması beklenmemelidir. Yüklemenizin hemen Yükseltilmemesi durumunda, bir yükseltme denenmeye başlamadan önce kasıtlı olarak rastgele bir değer vardır.

Bir şeyin doğru olmadığını düşünüyorsanız, `Get-ADSyncAutoUpgrade` Otomatik yükseltmenin etkinleştirildiğinden emin olmak için ilk ' i çalıştırın.

Durum askıya alınırsa, `Get-ADSyncAutoUpgrade -Detail` nedenini görüntülemek için kullanabilirsiniz.  Askıya alma nedeni herhangi bir dize değeri içerebilir, ancak genellikle yükseltilebilir Deresult (veya) dize değerini içerir `UpgradeNotSupportedNonLocalDbInstall` `UpgradeAbortedAdSyncExeInUse` .  Ayrıca, gibi bir bileşik değer de döndürülür `UpgradeFailedRollbackSuccess-GetPasswordHashSyncStateFailed` .

Ayrıca, bir yükseltilebilir Deresult (' AADHealthEndpointNotDefined ' veya ' Dirsyncinplaceyükseldenonlocaldb ') olmayan bir sonuç elde etmek mümkündür.

Ardından, gerekli URL 'Leri ara sunucunuzda veya güvenlik duvarınızda açtığınızdan emin olun. Otomatik güncelleştirme, [genel bakış](#overview)bölümünde açıklandığı gibi Azure AD Connect Health kullanıyor. Proxy kullanıyorsanız, sistem durumunun bir [proxy sunucu](how-to-connect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy)kullanacak şekilde yapılandırıldığından emin olun. Ayrıca, Azure AD ile [sistem durumu bağlantısını](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service) test edin.

Azure AD 'nin bağlantısının doğrulanması ile Eventlogs 'a bakmamız zaman alabilir. Olay Görüntüleyicisini başlatın ve **uygulama** olay günlüğüne bakın. Kaynak **Azure AD Connect yükseltmesi** ve olay kimliği aralığı **300-399** için bir EventLog filtresi ekleyin.  
!["Olay kaynakları" ve "dahil etme/hariç tutma" olay kimlikleri kutusu vurgulanmış "geçerli günlüğü filtrele" penceresini gösteren ekran görüntüsü.](./media/how-to-connect-install-automatic-upgrade/eventlogfilter.png)  

Artık otomatik yükseltme durumuyla ilişkili Eventlogs ' u görebilirsiniz.  
![Otomatik yükseltme için olay günlüğü filtresi](./media/how-to-connect-install-automatic-upgrade/eventlogresult.png)  

Sonuç kodu, duruma genel bakış içeren bir ön eke sahiptir.

| Sonuç kodu öneki | Description |
| --- | --- |
| Başarılı |Yükleme başarıyla yükseltildi. |
| Yükseltme Iptal edildi |Geçici bir koşul yükseltmeyi durdurdu. Yeniden denenecek ve beklenmek, daha sonra başarılı olur. |
| Yükseltilebilir nominal destekleniyor |Sistemin otomatik olarak yükseltilmesini engelleyen bir yapılandırması vardır. Durumun değiştiğini görmek için yeniden denenir, ancak beklentide sistemin el ile yükseltilmesi gerekir. |

Burada, bulduğunuz en yaygın mesajların bir listesi verilmiştir. Tümünü listelemez, ancak sonuç iletisi sorunun ne olduğu konusunda açık olmalıdır.

| Sonuç Iletisi | Description |
| --- | --- |
| **Yükseltme Iptal edildi** | |
| UpgradeAbortedCouldNotSetUpgradeMarker |Kayıt defterine yazılamadı. |
| UpgradeAbortedInsufficientDatabasePermissions |Yerleşik Yöneticiler grubunun veritabanı izinleri yok. Bu sorunu gidermek için Azure AD Connect en son sürümüne el ile yükseltin. |
| UpgradeAbortedInsufficientDiskSpace |Bir yükseltmeyi desteklemek için yeterli disk alanı yok. |
| UpgradeAbortedSecurityGroupsNotPresent |Eşitleme altyapısı tarafından kullanılan tüm güvenlik grupları bulunamadı ve çözümlenemedi. |
| UpgradeAbortedServiceCanNotBeStarted |NT Service **Microsoft Azure AD eşitleme** başlatılamadı. |
| UpgradeAbortedServiceCanNotBeStopped |NT Service **Microsoft Azure AD eşitleme** durdurulamadı. |
| UpgradeAbortedServiceIsNotRunning |NT Service **Microsoft Azure AD eşitleme** çalışmıyor. |
| UpgradeAbortedSyncCycleDisabled |[Zamanlayıcının](how-to-connect-sync-feature-scheduler.md) synccycle seçeneği devre dışı bırakıldı. |
| UpgradeAbortedSyncExeInUse |[Eşitleme Hizmeti Yöneticisi Kullanıcı arabirimi](how-to-connect-sync-service-manager-ui.md) sunucuda açıktır. |
| UpgradeAbortedSyncOrConfigurationInProgress |Yükleme Sihirbazı çalışıyor veya zamanlayıcı dışında bir eşitleme zamanlandı. |
| **Yükseltilebilir nominal destekleniyor** | |
| UpgradeNotSupportedCustomizedSyncRules |Yapılandırmaya kendi özel kurallarınızı eklediniz. |
| UpgradeNotSupportedInvalidPersistedState |Yükleme bir hızlı ayarlar veya DirSync yükseltmesi değildir. |
| 1 $ Supportednonlocaldbınstall yükseltildi |SQL Server Express LocalDB veritabanı kullandeğilsiniz. |
|Yükseltilebilir nominal Tsupportedlocaldbsizeaşıldı|Yerel DB boyutu 8 GB 'den büyük veya buna eşit|
|Yükseltildi ınttrsupportedadadhealthuploaddisabled|Sistem durumu verileri karşıya yüklemeleri portaldan devre dışı bırakıldı|

## <a name="next-steps"></a>Sonraki adımlar
[Şirket içi kimliklerinizi Azure Active Directory ile tümleştirme](whatis-hybrid-identity.md) hakkında daha fazla bilgi edinin.
