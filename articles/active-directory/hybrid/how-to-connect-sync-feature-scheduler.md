---
title: 'Azure AD Connect eşitleme: Zamanlayıcı | Microsoft Docs'
description: Bu konuda Azure AD Connect eşitleme ' deki yerleşik Zamanlayıcı özelliği açıklanmaktadır.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 6b1a598f-89c0-4244-9b20-f4aaad5233cf
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/01/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: ad7b0039602add7f4cd3cdd300bd829c4f148a79
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "90084745"
---
# <a name="azure-ad-connect-sync-scheduler"></a>Azure AD Connect eşitleme: Scheduler
Bu konuda Azure AD Connect Sync (eşitleme altyapısı) içinde yerleşik Zamanlayıcı açıklanmaktadır.

Bu özellik Build 1.1.105.0 ile tanıtılmıştır (2016 Şubat tarihinde yayımlanmıştır).

## <a name="overview"></a>Genel Bakış
Azure AD Connect, şirket içi dizininizde bir Zamanlayıcı kullanarak gerçekleşen eşitleme eşitleme değişikliklerini eşitler. Biri parola eşitleme ve diğeri nesne/öznitelik eşitleme ve bakım görevleri için olmak üzere iki Zamanlayıcı işlemi vardır. Bu konu, ikincisini ele almaktadır.

Önceki sürümlerde, nesne ve özniteliklerin Zamanlayıcı, eşitleme altyapısının dışında. Eşitleme işlemini tetiklemek için Windows Görev Zamanlayıcısı 'nı veya ayrı bir Windows hizmetini kullandı. Zamanlayıcı, eşitleme altyapısında yerleşik olarak bulunan 1,1 yayınlar ve bazı özelleştirmeye izin verir. Yeni varsayılan eşitleme sıklığı 30 dakikadır.

Zamanlayıcı iki görevden sorumludur:

* **Eşitleme çevrimi**. Değişiklikleri içeri aktarma, eşitleme ve dışarı aktarma işlemi.
* **Bakım görevleri**. Parola sıfırlama ve cihaz kayıt hizmeti (DRS) için anahtarları ve sertifikaları yenileyin. İşlem günlüğündeki eski girdileri temizle.

Scheduler her zaman çalışır, ancak bu görevlerden yalnızca birini çalıştıracak şekilde yapılandırılabilir. Örneğin, kendi eşitleme çevrimi işleminizin olması gerekiyorsa bu görevi Scheduler 'da devre dışı bırakabilir ancak bakım görevini yine de çalıştırabilirsiniz.

>[!IMPORTANT]
>Varsayılan olarak, her 30 dakikada bir eşitleme çevrimi çalıştırılır. Eşitleme cycley değiştirdiyseniz, bir eşitleme döngüsünün her 7 günde en az bir kez çalıştığından emin olmanız gerekir. 
>
>* Bir Delta eşitlemenin, son Delta eşitleden 7 gün içinde gerçekleşmesi gerekir.
>* Bir Delta eşitlemesi (tam eşitleme sonrasında), son tam eşitlemenin tamamlandığı zamandan itibaren 7 gün içinde gerçekleşecektir.
>
>Bunun yapılmaması, çözümlemek için tam eşitleme çalıştırmanızı gerektiren eşitleme sorunlarına neden olabilir. Bu, hazırlama modundaki sunucular için de geçerlidir.

## <a name="scheduler-configuration"></a>Zamanlayıcı yapılandırması
Geçerli yapılandırma ayarlarınızı görmek için PowerShell ve Çalıştır ' a gidin `Get-ADSyncScheduler` . Şu resme benzer bir şey gösterir:

![GetSyncScheduler](./media/how-to-connect-sync-feature-scheduler/getsynccyclesettings2016.png)

Bu cmdlet 'i çalıştırdığınızda **Eşitle komutunu veya cmdlet 'in kullanılamaz olduğunu** görürseniz, PowerShell modülü yüklenmez. Bu sorun, bir etki alanı denetleyicisinde veya varsayılan ayarlardan daha yüksek PowerShell kısıtlama düzeylerine sahip bir sunucuda Azure AD Connect çalıştırırsanız meydana gelebilir. Bu hatayı görürseniz, `Import-Module ADSync` cmdlet 'ini kullanılabilir hale getirmek için komutunu çalıştırın.

* **AllowedSyncCycleInterval**. Azure AD tarafından izin verilen eşitleme döngüleri arasındaki en kısa zaman aralığı. Bu ayardan daha sık eşitlenemez ve yine de desteklenemez.
* **CurrentlyEffectiveSyncCycleInterval**. Zamanlama Şu anda etkin. Allowedsyncınterval 'dan daha sık değilse, CustomizedSyncInterval (ayarlandıysa) ile aynı değere sahiptir. 1.1.281 önce bir yapı kullanırsanız ve CustomizedSyncCycleInterval değiştirirseniz, bu değişiklik bir sonraki eşitleme döngüsünden sonra devreye girer. Derleme 1.1.281, değişiklik hemen geçerli olur.
* **CustomizedSyncCycleInterval**. Scheduler 'ın varsayılan 30 dakikadan başka bir sıklıkta çalışmasını istiyorsanız, bu ayarı yapılandırın. Yukarıdaki resimde Zamanlayıcı, bunun yerine her saat çalışacak şekilde ayarlanmıştır. Bu ayarı Allowedsyncınterval değerinden daha düşük bir değere ayarlarsanız, ikincisi kullanılır.
* **NextSyncCyclePolicyType**. Delta ya da başlangıç. Sonraki çalıştırmanın yalnızca Delta değişikliklerini işlemesini mi yoksa bir sonraki çalıştırmanın tam içeri aktarma ve eşitleme yapması gerekip gerekmediğini tanımlar. İkincisi de yeni veya değiştirilmiş kuralları yeniden işleyebilir.
* **NextSyncCycleStartTimeInUTC**. Daha sonra Scheduler bir sonraki eşitleme döngüsünü başlatır.
* **Takip edilmemiş Aralık**. İşlem günlüklerinin tutulması gerekir. Bu Günlükler eşitleme hizmeti Yöneticisi 'nde incelenebilir. Varsayılan değer 7 gün boyunca bu günlükleri saklar.
* **SyncCycleEnabled**. Scheduler 'ın işlemin bir parçası olarak içeri aktarma, eşitleme ve dışarı aktarma işlemlerini çalıştırıp çalıştırmadığını gösterir.
* **MaintenanceEnabled**. Bakım işleminin etkin olup olmadığını gösterir. Sertifikaları/anahtarları güncelleştirir ve işlemler günlüğünü temizler.
* **Stagingmodeenabled**. [Hazırlama modunun](how-to-connect-sync-staging-server.md) etkin olup olmadığını gösterir. Bu ayar etkinleştirilirse, dışarı aktarmaları çalışır, ancak yine de içeri aktarma ve eşitleme çalıştırmaların çalışmasını önler.
* **SchedulerSuspended**. Bir yükseltme sırasında, Scheduler 'ın çalışmasını geçici olarak engellemek için Connect tarafından ayarlanır.

Bu ayarlardan bazılarını ile değiştirebilirsiniz `Set-ADSyncScheduler` . Aşağıdaki parametreler değiştirilebilir:

* CustomizedSyncCycleInterval
* NextSyncCyclePolicyType
* Takip edilmemiş Aralık
* SyncCycleEnabled
* MaintenanceEnabled

Daha önceki Azure AD Connect yapılarda **ıstagingmodeenabled** , set-ADSyncScheduler ' da kullanıma sunuldu. Bu özelliği ayarlamak **desteklenmez** . **SchedulerSuspended** özelliği yalnızca Connect ile değiştirilmelidir. Bunu PowerShell ile doğrudan ayarlamak **desteklenmez** .

Zamanlayıcı yapılandırması Azure AD 'de depolanır. Hazırlama sunucunuz varsa, birincil sunucu üzerindeki herhangi bir değişiklik hazırlama sunucusunu da etkiler (ısstagingmodeenabled hariç).

### <a name="customizedsynccycleinterval"></a>CustomizedSyncCycleInterval
Sözdizimi `Set-ADSyncScheduler -CustomizedSyncCycleInterval d.HH:mm:ss`  
d-gün, SS-saat, mm-dakika, SS-saniye

Örnek: `Set-ADSyncScheduler -CustomizedSyncCycleInterval 03:00:00`  
Zamanlayıcıyı her 3 saatte bir çalışacak şekilde değiştirir.

Örnek: `Set-ADSyncScheduler -CustomizedSyncCycleInterval 1.0:0:0`  
Değişiklikler Zamanlayıcı 'yı günlük olarak çalışacak şekilde değiştirir.

### <a name="disable-the-scheduler"></a>Zamanlayıcıyı devre dışı bırak  
Yapılandırma değişiklikleri yapmanız gerekiyorsa, Scheduler 'ı devre dışı bırakmak isteyebilirsiniz. Örneğin, [filtreleme yapılandırdığınızda](how-to-connect-sync-configure-filtering.md) veya [eşitleme kurallarında değişiklikler yaptığınızda](how-to-connect-sync-change-the-configuration.md).

Zamanlayıcıyı devre dışı bırakmak için çalıştırın `Set-ADSyncScheduler -SyncCycleEnabled $false` .

![Zamanlayıcıyı devre dışı bırak](./media/how-to-connect-sync-feature-scheduler/schedulerdisable.png)

Değişikliklerinizi yaptığınızda, Scheduler 'ı ile yeniden etkinleştirmeyi unutmayın `Set-ADSyncScheduler -SyncCycleEnabled $true` .

## <a name="start-the-scheduler"></a>Zamanlayıcıyı başlatma
Zamanlayıcı varsayılan olarak 30 dakikada bir çalıştırılır. Bazı durumlarda, zamanlanmış döngüler arasında veya farklı bir tür çalıştırmanız gereken bir eşitleme döngüsü çalıştırmak isteyebilirsiniz.

### <a name="delta-sync-cycle"></a>Delta eşitleme çevrimi
Bir Delta eşitleme çevrimi aşağıdaki adımları içerir:


- Tüm bağlayıcılarda Delta içeri aktarma
- Tüm bağlayıcılarda Delta eşitleme
- Tüm bağlayıcılarda dışarı aktar

### <a name="full-sync-cycle"></a>Tam eşitleme çevrimi
Tam eşitleme çevrimi aşağıdaki adımları içerir:

- Tüm bağlayıcılarda tam Içeri aktarma
- Tüm bağlayıcılarda tam eşitleme
- Tüm bağlayıcılarda dışarı aktar

Bu, hemen eşitlenmesi gereken bir acil değişiklik olabilir ve bu nedenle bir döngüyü el ile çalıştırmanız gerekir. 

Bir eşitleme döngüsünü el ile çalıştırmanız gerekiyorsa, PowerShell 'den çalıştırın `Start-ADSyncSyncCycle -PolicyType Delta` .

Tam eşitleme döngüsünü başlatmak için `Start-ADSyncSyncCycle -PolicyType Initial` bir PowerShell isteminden çalıştırın.   

Tam eşitleme döngüsünü çalıştırmak çok zaman alabilir, bu işlemin nasıl iyileştirileceği hakkında bilgi edinmek için sonraki bölümü okuyun.

### <a name="sync-steps-required-for-different-configuration-changes"></a>Farklı yapılandırma değişiklikleri için gereken eşitleme adımları
Farklı yapılandırma değişiklikleri, değişikliklerin tüm nesnelere doğru bir şekilde uygulandığından emin olmak için farklı eşitleme adımları gerektirir.

- Kaynak dizinden içeri aktarılacak daha fazla nesne veya öznitelik eklendi (eşitleme kuralları eklenerek/değiştirilerek)
    - Bu kaynak dizin için bağlayıcı üzerinde tam bir Içeri aktarma gereklidir
- Eşitleme kurallarında değişiklikler yapıldı
    - Bağlayıcı üzerinde değiştirilen eşitleme kuralları için tam eşitleme gerekiyor
- [Filtre](how-to-connect-sync-configure-filtering.md) değişikliği, farklı sayıda nesne dahil edilmelidir
    - Eşitleme altyapısına zaten aktarılmış olan özniteliklere dayalı öznitelik tabanlı filtreleme kullanmıyorsanız, her AD bağlayıcısının bağlayıcı üzerinde tam Içeri aktarma gereklidir

### <a name="customizing-a-sync-cycle-run-the-right-mix-of-delta-and-full-sync-steps"></a>Eşitleme döngüsünü özelleştirme Delta ve tam eşitleme adımlarının sağ karışımını çalıştırın
Tam eşitleme döngüsünü çalıştırmayı önlemek için, belirli bağlayıcıları aşağıdaki cmdlet 'leri kullanarak tam bir adım çalıştırmak üzere işaretleyebilirsiniz.

`Set-ADSyncSchedulerConnectorOverride -Connector <ConnectorGuid> -FullImportRequired $true`

`Set-ADSyncSchedulerConnectorOverride -Connector <ConnectorGuid> -FullSyncRequired $true`

`Get-ADSyncSchedulerConnectorOverride -Connector <ConnectorGuid>` 

Örnek: bağlayıcı "AD Ormanı A" Bağlayıcısı için eşitleme kurallarında değişiklik yaptıysanız, yeni özniteliklerin içeri aktarılmasını gerektirmeyen bir Delta eşitleme döngüsünü çalıştırmak için aşağıdaki cmdlet 'leri çalıştırarak bu bağlayıcı için tam eşitleme adımı da olan

`Set-ADSyncSchedulerConnectorOverride -ConnectorName “AD Forest A” -FullSyncRequired $true`

`Start-ADSyncSyncCycle -PolicyType Delta`

Örnek: "AD Ormanı A" Bağlayıcısı için eşitleme kurallarında değişiklikler yaptıysanız, artık yeni bir öznitelik içeri aktarılmaları gerekir, bu da tam Içeri aktarma, bu bağlayıcı için tam eşitleme adımı olan bir Delta eşitleme döngüsünü çalıştırmak için aşağıdaki cmdlet 'leri çalıştırırsınız.

`Set-ADSyncSchedulerConnectorOverride -ConnectorName “AD Forest A” -FullImportRequired $true`

`Set-ADSyncSchedulerConnectorOverride -ConnectorName “AD Forest A” -FullSyncRequired $true`

`Start-ADSyncSyncCycle -PolicyType Delta`


## <a name="stop-the-scheduler"></a>Zamanlayıcıyı durdur
Zamanlayıcı Şu anda bir eşitleme döngüsünü çalıştırıyorsa, durdurmanız gerekebilir. Örneğin, Yükleme Sihirbazı 'nı başlatırsanız ve bu hatayı alırsanız:

![Ekran görüntüsü, yapılandırma hata iletisini değiştirebir şekilde görünür.](./media/how-to-connect-sync-feature-scheduler/synccyclerunningerror.png)

Bir eşitleme çevrimi çalışırken, yapılandırma değişiklikleri yapamazsınız. Zamanlayıcı işlemi bitirene kadar bekleyebilirsiniz, ancak değişikliklerinizi hemen yapabilmeniz için bunu da durdurabilirsiniz. Geçerli döngüyü durdurmak zararlı değildir ve bekleyen değişiklikler sonraki çalıştırma ile işlenir.

1. Zamanlayıcı cmdlet 'ini kullanarak Scheduler 'ın geçerli döngüsünü durdurmasını bildirerek başlayın `Stop-ADSyncSyncCycle` .
2. 1.1.281 önce bir yapı kullanırsanız, Scheduler 'ı durdurmak geçerli bağlayıcının geçerli görevinden durdurulmaz. Bağlayıcının durdurulmasına zorlamak için aşağıdaki işlemleri gerçekleştirin:

   ![Ekran görüntüsünde, bağlayıcılar seçiliyken ve çalıştırılan bir bağlayıcının, durdur eylemiyle işaretlenmiş Synchronization Service Manager gösterir.](./media/how-to-connect-sync-feature-scheduler/stopaconnector.png)

   * Başlangıç menüsünden **eşitleme hizmetini** başlatın. **Bağlayıcılar**' a gidin, bağlayıcıyı **çalışır** durumda vurgulayın ve eylemlerden **Durdur** ' u seçin.

Zamanlayıcı hala etkin ve sonraki fırsatta yeniden başlatılıyor.

## <a name="custom-scheduler"></a>Özel Zamanlayıcı
Bu bölümde belgelenen cmdlet 'ler yalnızca Build [1.1.130.0](reference-connect-version-history.md) ve üzeri sürümlerde kullanılabilir.

Yerleşik Zamanlayıcı gereksinimlerinizi karşılamadığı takdirde, PowerShell kullanarak bağlayıcıları zamanlayabilirsiniz.

### <a name="invoke-adsyncrunprofile"></a>Invoke-ADSyncRunProfile
Bir bağlayıcının profilini şu şekilde başlatabilirsiniz:

```
Invoke-ADSyncRunProfile -ConnectorName "name of connector" -RunProfileName "name of profile"
```

[Bağlayıcı adları](how-to-connect-sync-service-manager-ui-connectors.md) için kullanılacak adlar ve [profil adlarını Çalıştır](how-to-connect-sync-service-manager-ui-connectors.md#configure-run-profiles) [Synchronization Service Manager Kullanıcı arabiriminde](how-to-connect-sync-service-manager-ui.md)bulabilirsiniz.

![Çalıştırma profilini çağır](./media/how-to-connect-sync-feature-scheduler/invokerunprofile.png)  

`Invoke-ADSyncRunProfile`Cmdlet zaman uyumludur, diğer bir deyişle bağlayıcı işlemi başarıyla veya bir hata ile tamamlayana kadar denetimi döndürmez.

Bağlayıcılarınızı zamanladığınızda, bu öneriler aşağıdaki sırayla planlansın:

1. (Tam/Delta) Active Directory gibi şirket içi dizinlerden içeri aktarma
2. (Tam/Delta) Azure AD 'den içeri aktarma
3. (Tam/Delta) Active Directory gibi şirket içi dizinlerden eşitleme
4. (Tam/Delta) Azure AD 'den eşitleme
5. Azure AD 'ye aktar
6. Active Directory gibi şirket içi dizinlere dışarı aktarma

Bu sıra, yerleşik Scheduler 'ın bağlayıcıları nasıl çalıştırmasından dolayı yapılır.

### <a name="get-adsyncconnectorrunstatus"></a>Get-ADSyncConnectorRunStatus
Ayrıca, meşgul veya boşta olup olmadığını görmek için eşitleme altyapısını de izleyebilirsiniz. Bu cmdlet, eşitleme altyapısı boşta ise ve bir bağlayıcı çalıştırmışsa boş bir sonuç döndürür. Bir bağlayıcı çalışıyorsa, bağlayıcının adını döndürür.

```
Get-ADSyncConnectorRunStatus
```

![Bağlayıcı çalışma durumu](./media/how-to-connect-sync-feature-scheduler/getconnectorrunstatus.png)  
Yukarıdaki resimde, ilk satır eşitleme altyapısının boşta olduğu bir durumdur. Azure AD bağlayıcısının çalıştırıldığı ikinci satır.

## <a name="scheduler-and-installation-wizard"></a>Zamanlayıcı ve Yükleme Sihirbazı
Yükleme Sihirbazı 'nı başlatırsanız Zamanlayıcı geçici olarak askıya alınır. Bu davranış, yapılandırma değişikliklerini yaptığınız varsayılır ve eşitleme altyapısı etkin bir şekilde çalışıyorsa bu ayarların uygulanamadığını varsaymaz. Bu nedenle, eşitleme altyapısının herhangi bir eşitleme eylemi gerçekleştirmesini durdurduğundan, Yükleme Sihirbazı 'nı açık bırakmayın.

## <a name="next-steps"></a>Sonraki adımlar
[Azure AD Connect eşitleme](how-to-connect-sync-whatis.md) yapılandırması hakkında daha fazla bilgi edinin.

[Şirket içi kimliklerinizi Azure Active Directory ile tümleştirme](whatis-hybrid-identity.md) hakkında daha fazla bilgi edinin.
