---
title: StorSimple 8000 serisi dağıtım sorunlarını giderme | Microsoft Docs
description: İlk olarak StorSimple dağıtırken oluşan hataların nasıl tanılandığını ve düzeltileceğini açıklar.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: troubleshooting
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/25/2021
ms.author: alkohli
ms.openlocfilehash: f1bce5c0ecdab4a9c96c3f2cc0e9734247bdfd66
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102566354"
---
# <a name="troubleshoot-storsimple-device-deployment-issues"></a>StorSimple cihaz dağıtımı sorunlarını giderme
## <a name="overview"></a>Genel Bakış
Bu makale Microsoft Azure StorSimple dağıtımınız için yardımcı sorun giderme kılavuzu sağlar. Ortak sorunları, olası nedenleri ve StorSimple yapılandırdığınızda karşılaşabileceğiniz sorunları çözmenize yardımcı olmak için önerilen adımları açıklar. 

Bu bilgiler hem StorSimple 8000 serisi fiziksel cihaz hem de StorSimple Cloud Appliance için geçerlidir.

> [!NOTE]
> Cihaz yapılandırma ile ilgili, cihazı ilk kez dağıttığınızda gerçekleşebileceğini veya cihaz çalışır durumdayken daha sonra gerçekleşebileceğini gösteren ilgili sorunlar. Bu makalede, ilk kez dağıtım sorunlarını giderme adımları ele alınmaktadır. İşlemsel bir cihazda sorun gidermek için, [işletim cihazında sorun gidermek üzere Tanılama aracını kullanma](storsimple-8000-diagnostics.md)bölümüne gidin.

Bu makalede ayrıca StorSimple dağıtımlarının sorunlarını gidermeye yönelik araçlar açıklanmakta ve adım adım sorun giderme örneği sağlanmaktadır.

## <a name="first-time-deployment-issues"></a>İlk dağıtım sorunları
Cihazınızı ilk kez dağıttığınızda bir sorunla karşılaşırsanız aşağıdaki kılavuzu göz önünde bulundurun:

* Fiziksel bir cihazda sorun yaşıyorsanız, donanımın yüklendiğinden ve [storsimple 8100 cihazınızı yükleme](storsimple-8100-hardware-installation.md) veya [StorSimple 8600 cihazınızı yükleme](storsimple-8600-hardware-installation.md)konularında açıklandığı şekilde yapılandırıldığından emin olun.
* Dağıtım için önkoşulları denetleyin. [Dağıtım yapılandırma denetim listesinde](storsimple-8000-deployment-walkthrough-u2.md#deployment-configuration-checklist)açıklanan tüm bilgilere sahip olduğunuzdan emin olun.
* Sorunun açıklanmasını görmek için StorSimple sürüm notlarını gözden geçirin. Sürüm notları, bilinen yükleme sorunlarına yönelik geçici çözümler içerir. 

Cihaz dağıtımı sırasında, kullanıcıların, Kurulum Sihirbazı 'nı çalıştırırken ve cihazı StorSimple için Windows PowerShell aracılığıyla kaydettiklerinde meydana gelen en yaygın sorunları ortaya çıkar. (StorSimple cihazınızı kaydetmek ve yapılandırmak için StorSimple için Windows PowerShell kullanırsınız. Cihaz kaydı hakkında daha fazla bilgi için bkz. [3. Adım: cihazınızı yapılandırma ve StorSimple için Windows PowerShell ile kaydetme](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple).

Aşağıdaki bölümler, StorSimple cihazını ilk kez yapılandırdığınızda, içinde aldığınız sorunları çözmenize yardımcı olabilir.

## <a name="first-time-setup-wizard-process"></a>İlk kez Kurulum Sihirbazı işlemi
Aşağıdaki adımlarda Kurulum Sihirbazı işlemi özetlenmektedir. Ayrıntılı kurulum bilgileri için bkz. Şirket [Içi StorSimple cihazınızı dağıtma](storsimple-8000-deployment-walkthrough-u2.md).

1. Geri kalan adımlarda size yol gösterecek Kurulum Sihirbazı 'nı başlatmak için [Invoke-HcsSetupWizard](/previous-versions/windows/powershell-scripting/dn688135(v=wps.630)) cmdlet 'ini çalıştırın. 
2. Ağı yapılandırma: Kurulum Sihirbazı, StorSimple cihazınızda VERI 0 ağ arabirimi için ağ ayarlarını yapılandırmanıza olanak tanır. Aşağıdaki ayarlar dahildir:
   * Sanal IP (VIP), alt ağ maskesi ve ağ geçidi – [set-Hcsnetınterface](/previous-versions/windows/powershell-scripting/dn688161(v=wps.630)) cmdlet 'i arka planda yürütülür. StorSimple cihazınızda VERI 0 ağ arabirimi için IP adresini, alt ağ maskesini ve ağ geçidini yapılandırır.
   * Birincil DNS sunucusu – [set-HcsDnsClientServerAddress](/previous-versions/windows/powershell-scripting/dn688172(v=wps.630)) cmdlet 'i arka planda yürütülür. StorSimple çözümünüz için DNS ayarlarını yapılandırır.
   * NTP sunucusu: [set-HcsNtpClientServerAddress](/previous-versions/windows/powershell-scripting/dn688138(v=wps.630)) cmdlet 'i arka planda yürütülür. StorSimple çözümünüz için NTP sunucu ayarlarını yapılandırır.
   * İsteğe bağlı Web proxy – [set-HcsWebProxy](/previous-versions/windows/powershell-scripting/dn688154(v=wps.630)) cmdlet 'i arka planda yürütülür. StorSimple çözümünüz için Web proxy yapılandırmasını ayarlar ve etkinleştirilir.
3. Parolayı ayarlayın: sonraki adım, Cihaz Yöneticisi parolasını ayarladır.
   Cihaz Yöneticisi parolası, cihazınızda oturum açmak için kullanılır. Varsayılan cihaz parolası **Password1**’dir.
        
     > [!IMPORTANT]
     > Parolalar kayıt öncesinde toplanır, ancak yalnızca cihazı başarıyla kaydettikten sonra uygulanır. Parola uygulama başarısız olursa, gerekli parolalar (karmaşıklık gereksinimlerini karşılayan) toplanana kadar parolayı yeniden girmeniz istenir.
     
4. Cihazı kaydetme: son adım, cihazı Microsoft Azure çalıştıran StorSimple Device Manager hizmetine kaydetmadır. Kayıt, Azure portal [hizmet kayıt anahtarını almanızı](storsimple-8000-manage-service.md#get-the-service-registration-key) ve Kurulum Sihirbazı 'nda sağlamanızı gerektirir. **Cihaz başarıyla kaydedildikten sonra size bir hizmet veri şifreleme anahtarı sağlanır. Bu şifreleme anahtarını güvenli bir konumda tutmanız gerektiğinden emin olun çünkü bu, tüm gelecek cihazları hizmete kaydetmek için gerekli olacaktır.**

## <a name="common-errors-during-device-deployment"></a>Cihaz dağıtımı sırasında sık karşılaşılan hatalar
Aşağıdaki tablolarda şunları yaptığınızda karşılaşabileceğiniz yaygın hatalar listelenmektedir:

* Gerekli ağ ayarlarını yapılandırın.
* İsteğe bağlı Web proxy ayarlarını yapılandırın.
* Cihaz yönetici parolasını ayarlayın.
* Cihazı kaydedin.

## <a name="errors-during-the-required-network-settings"></a>Gerekli ağ ayarları sırasında hatalar
| Hayır. | Hata iletisi | Olası nedenler | Önerilen eylem |
| --- | --- | --- | --- |
| 1 |Invoke-HcsSetupWizard: Bu komut yalnızca etkin denetleyicide çalıştırılabilir. |Pasif denetleyicide yapılandırma gerçekleştiriliyor. |Etkin denetleyiciden bu komutu çalıştırın. Daha fazla bilgi için, bkz. [cihazınızda etkin bir denetleyiciyi tanımla](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device). |
| 2 |Invoke-HcsSetupWizard: cihaz için hazırlanma. |VERI 0 üzerinde ağ bağlantısıyla ilgili sorunlar var. |VERI 0 üzerinde fiziksel ağ bağlantısını denetleyin. |
| 3 |Invoke-HcsSetupWizard: ağ üzerindeki başka bir sistemle bir IP adresi çakışması var (HRESULT 'ten özel durum: 0x80070263). |0 VERILERI için sağlanan IP zaten başka bir sistem tarafından kullanılıyor. |Kullanımda olmayan yeni bir IP sağlayın. |
| 4 |Invoke-HcsSetupWizard: bir küme kaynağı başarısız oldu. (HRESULT özel durumu: 0x800713AE). |Yinelenen VIP. Sağlanan IP zaten kullanımda. |Kullanımda olmayan yeni bir IP sağlayın. |
| 5 |Invoke-HcsSetupWizard: geçersiz IPv4 adresi. |IP adresi yanlış bir biçimde sunulmaktadır. |Biçimi denetleyin ve IP adresinizi yeniden sağlayın. Daha fazla bilgi için bkz. [IPv4 adresleme][1]. |
| 6 |Invoke-HcsSetupWizard: geçersiz IPv6 adresi. |IP adresi yanlış bir biçimde sunulmaktadır. |Biçimi denetleyin ve IP adresinizi yeniden sağlayın. Daha fazla bilgi için bkz. [IPv6 adresleme][2]. |
| 7 |Invoke-HcsSetupWizard: uç nokta eşleştiricisinden kullanılabilir başka uç nokta yok. (HRESULT özel durumu: 0x800706D9) |Küme işlevi çalışmıyor. |Sonraki adımlar için [Microsoft desteği başvurun](storsimple-8000-contact-microsoft-support.md) . |

## <a name="errors-during-the-optional-web-proxy-settings"></a>İsteğe bağlı Web proxy ayarları sırasında hatalar
| Hayır. | Hata iletisi | Olası nedenler | Önerilen eylem |
| --- | --- | --- | --- |
| 1 |Invoke-HcsSetupWizard: geçersiz parametre (HRESULT 'ten özel durum: 0x80070057) |Proxy ayarları için belirtilen parametrelerden biri geçerli değil. |URI doğru biçimde sağlanmadı. Şu biçimi kullanın: http:// *\<IP address or FQDN of the web proxy server>* :*\<TCP port number>* |
| 2 |Invoke-HcsSetupWizard: RPC sunucusu kullanılamıyor (HRESULT özel durumu: 0x800706ba) |Kök nedeni aşağıdakilerden biridir:<ol><li>Küme yok.</li><li>Pasif denetleyici etkin denetleyici ile iletişim kuramaz ve komut pasif denetleyiciden çalıştırılır.</li></ol> |Kök nedenine bağlı olarak:<ol><li>Kümenin çalıştığından emin olmak için [Microsoft desteği başvurun](storsimple-8000-contact-microsoft-support.md) .</li><li>Etkin denetleyicideki komutu çalıştırın. Komutu pasif denetleyicisinden çalıştırmak istiyorsanız, pasif denetleyicinin etkin denetleyici ile iletişim kurabildiğinden emin olmanız gerekir. Bu bağlantı bozulur [Microsoft desteği başvurmanız](storsimple-8000-contact-microsoft-support.md) gerekecektir.</li></ol> |
| 3 |Invoke-HcsSetupWizard: RPC çağrısı başarısız oldu (HRESULT özel durumu: 0x800706in) |Küme çalışmıyor. |Kümenin çalıştığından emin olmak için [Microsoft desteği başvurun](storsimple-8000-contact-microsoft-support.md) . |
| 4 |Invoke-HcsSetupWizard: küme kaynağı bulunamadı (HRESULT özel durumu: 0x8007138f) |Küme kaynağı bulunamadı. Yükleme doğru olmadığında bu durum oluşabilir. |Cihazı fabrika varsayılan ayarlarına sıfırlamanız gerekebilir. Bir küme kaynağı oluşturmak için [Microsoft desteği başvurun](storsimple-8000-contact-microsoft-support.md) . |
| 5 |Invoke-HcsSetupWizard: küme kaynağı çevrimiçi değil (HRESULT özel durumu: 0x8007138c) |Küme kaynakları çevrimiçi değil. |Sonraki adımlar için [Microsoft desteği başvurun](storsimple-8000-contact-microsoft-support.md) . |

## <a name="errors-related-to-device-administrator-password"></a>Cihaz yönetici parolasıyla ilgili hatalar
Varsayılan cihaz yönetici parolası **Parola1**' dir. Bu parolanın ilk oturum açma işleminden sonra süresi dolar; Bu nedenle, bunu değiştirmek için Kurulum Sihirbazı 'nı kullanmanız gerekir. Cihazı ilk kez kaydettiğinizde yeni bir cihaz yöneticisi parolası sağlamalısınız. 

Parolalarınızın aşağıdaki gereksinimleri karşıladığından emin olun:

* Cihaz Yöneticisi parolanızın uzunluğu 8 ile 15 karakter arasında olmalıdır.
* Parolalar şu karakter türlerinden üçünü içermelidir: küçük harf, büyük harf, sayısal ve özel. 
* Parolanız son 24 parolayla aynı olamaz.

Ayrıca, parolaların her yıl dolacağını ve yalnızca cihazı başarıyla kaydettikten sonra değiştirilebileceğini unutmayın. Kayıt herhangi bir nedenden dolayı başarısız olursa, parolalar değiştirilmez.

Cihaz yönetici parolası hakkında daha fazla bilgi için StorSimple [Device Manager hizmetini kullanarak StorSimple parolanızı değiştirin](storsimple-8000-change-passwords.md).

Cihaz Yöneticisi ve StorSimple Snapshot Manager parolalarını ayarlarken aşağıdaki hatalardan bir veya daha fazlasına karşılaşabilirsiniz.

| Hayır. | Hata iletisi | Önerilen eylem |
| --- | --- | --- |
| 1 |Parola uzunluk üst sınırını aşıyor. |Cihaz Yöneticisi parolanızın uzunluğu 8 ile 15 karakter arasında olmalıdır. |
| 2 |Parola gerekli uzunluğu karşılamıyor. |Cihaz Yöneticisi parolanızın uzunluğu 8 ile 15 karakter arasında olmalıdır.|
| 3 |Parola küçük harfli karakterler içermelidir. |Parolalar şu 4 karakter türlerinden 3 ' ü içermelidir: küçük harf, büyük harf, sayısal ve özel. Parolanızın bu gereksinimleri karşıladığından emin olun. |
| 4 |Parola sayısal karakterler içermelidir. |Parolalar şu 4 karakter türlerinden 3 ' ü içermelidir: küçük harf, büyük harf, sayısal ve özel. Parolanızın bu gereksinimleri karşıladığından emin olun. |
| 5 |Parola, özel karakterler içermelidir. |Parolalar şu 4 karakter türlerinden 3 ' ü içermelidir: küçük harf, büyük harf, sayısal ve özel. Parolanızın bu gereksinimleri karşıladığından emin olun. |
| 6 |Parola şu 4 karakter türlerinden 3 ' ü içermelidir: büyük harf, küçük harf, sayısal ve özel. |Parolanız gerekli karakter türlerini içermiyor. Parolanızın bu gereksinimleri karşıladığından emin olun. |
| 7 |Parametre onaylamada eşleşmiyor. |Parolanızın tüm gereksinimleri karşıladığından ve doğru girdiğinizden emin olun. |
| 8 |Parolanız varsayılana uymuyor. |Varsayılan parola *Parola1*' dir. İlk kez oturum açtıktan sonra bu parolayı değiştirmeniz gerekir. |
| 9 |Girdiğiniz parola, cihaz parolasıyla eşleşmiyor. Lütfen parolayı yeniden yazın. |Parolayı denetleyip yeniden yazın. |

Parolalar cihaz kaydedilmeden önce toplanır, ancak kayıt başarıyla kaydedildikten sonra uygulanır. Parola kurtarma iş akışı, cihazın kaydedilmesini gerektirir.

> [!IMPORTANT]
> Genel olarak, parola uygulama girişimi başarısız olursa, yazılım sürekli olarak parolayı toplamaya çalışır. Nadir örneklerde, parola uygulanamıyor. Bu durumda, cihazı kaydedebilir ve devam edebilir, ancak parolalar değiştirilmez. Azure portal kaydından sonra cihaz yönetici parolasını değiştirebilirsiniz.


StorSimple Device Manager hizmeti aracılığıyla Azure portal parolayı sıfırlayabilirsiniz. Daha fazla bilgi için [Cihaz yönetici parolasını değiştirme](storsimple-8000-change-passwords.md#change-the-device-administrator-password)bölümüne gidin.

## <a name="errors-during-device-registration"></a>Cihaz kaydı sırasında karşılaşılan hatalar
Cihazı kaydetmek için Microsoft Azure çalıştıran StorSimple Device Manager hizmetini kullanırsınız. Cihaz kaydı sırasında aşağıdaki sorunlardan biri veya daha fazlası ile karşılaşabilirsiniz.

| Hayır. | Hata iletisi | Olası nedenler | Önerilen eylem |
| --- | --- | --- | --- |
| 1 |Hata 350027: cihaza StorSimple Device Manager kaydedilemedi. | |Birkaç dakika bekleyip işlemi yeniden deneyin. Sorun devam ederse, [Microsoft desteği başvurun](storsimple-8000-contact-microsoft-support.md). |
| 2 |Hata 350013: cihaz kaydedilirken bir hata oluştu. Bunun nedeni yanlış hizmet kayıt anahtarıdır. | |Lütfen cihazı doğru hizmet kayıt anahtarıyla yeniden kaydedin. Daha fazla bilgi için bkz [. hizmet kayıt anahtarını edinme.](storsimple-8000-manage-service.md#get-the-service-registration-key) |
| 3 |Hata 350063: StorSimple Device Manager hizmetine yönelik kimlik doğrulaması başarılı ancak kayıt başarısız oldu. Lütfen bir süre sonra işlemi yeniden deneyin. |Bu hata, ACS ile kimlik doğrulamanın geçtiğini ancak hizmete yapılan kaydolma çağrısının başarısız olduğunu gösterir. Bu, tek tek bir ağ hatası olabilir. |Sorun devam ederse lütfen [Microsoft desteği başvurun](storsimple-8000-contact-microsoft-support.md). |
| 4 |Hata 350049: kayıt sırasında hizmete erişilemedi. |Hizmete çağrı yapıldığında, bir Web özel durumu alınır. Bazı durumlarda, işlemi daha sonra yeniden denemeden bu durum düzeltilebilir. |Lütfen IP adresinizi ve DNS adını denetleyip işlemi yeniden deneyin. Sorun devam ederse [Microsoft desteği başvurun.](storsimple-8000-contact-microsoft-support.md) |
| 5 |Hata 350031: cihaz zaten kayıtlı. | |Herhangi bir işlem gerekli değil. |
| 6 |Hata 350016: cihaz kaydı başarısız oldu. | |Lütfen kayıt anahtarının doğru olduğundan emin olun. |
| 7 |Invoke-HcsSetupWizard: cihazınız kaydedilirken bir hata oluştu; Bunun nedeni yanlış IP adresi veya DNS adı olabilir. Lütfen ağ ayarlarınızı denetleyin ve yeniden deneyin. Sorun devam ederse [Microsoft desteği başvurun](storsimple-8000-contact-microsoft-support.md). (Hata 350050) |Cihazınızın dış ağa ping atabildiğini doğrulayın. Dış ağa bağlantınız yoksa kayıt bu hatayla başarısız olabilir. Bu hata aşağıdakilerden bir veya daha fazla bir bileşim olabilir:<ul><li>Yanlış IP</li><li>Yanlış alt ağ</li><li>Hatalı ağ geçidi</li><li>Yanlış DNS ayarları</li></ul> |Adım adım [sorun giderme örneğinde](#step-by-step-storsimple-troubleshooting-example)bulunan adımlara bakın. |
| 8 |Invoke-HcsSetupWizard: geçerli işlem, bir iç hizmet hatası nedeniyle başarısız oldu [0x1FBE2]. Lütfen bir süre sonra işlemi yeniden deneyin. Sorun devam ederse lütfen Microsoft Desteği başvurun. |Bu, tüm Kullanıcı görünmeyen hatalar için hizmetten veya aracıdan oluşan genel bir hatadır. En yaygın neden, ACS kimlik doğrulamasının başarısız olması olabilir. Hatanın olası nedeni, NTP sunucusu yapılandırmasıyla ilgili sorunların ve cihazdaki saatin doğru ayarlanmamasına neden olur. |Süreyi düzeltin (sorunlar varsa) ve ardından kayıt işlemini yeniden deneyin. Saat dilimini ayarlamak için Set-HcsSystem-TimeZone komutunu kullanırsanız, saat dilimindeki her bir sözcüğü (örneğin, "Pasifik Standart Saati") büyük harfle değiştirin.  Bu sorun devam ederse, sonraki adımlar için [Microsoft desteği başvurun](storsimple-8000-contact-microsoft-support.md) . |
| 9 |Uyarı: cihaz etkinleştirilemedi. Cihaz yöneticiniz ve StorSimple Snapshot Manager parolalar değişmemiştir. |Kayıt başarısız olursa, Cihaz Yöneticisi ve StorSimple Snapshot Manager parolaları değiştirilmez. | |

## <a name="tools-for-troubleshooting-storsimple-deployments"></a>StorSimple dağıtımlarının sorunlarını gidermeye yönelik araçlar
StorSimple, StorSimple çözümünüzün sorunlarını gidermek için kullanabileceğiniz çeşitli araçlar içerir. Bu araçlar şunları içerir:

* Destek paketleri ve cihaz günlükleri.
* Sorun giderme için tasarlanan cmdlet 'ler.

## <a name="support-packages-and-device-logs-available-for-troubleshooting"></a>Destek paketleri ve cihaz günlükleri sorun giderme için kullanılabilir
Destek paketi, Microsoft Desteği ekibine cihaz sorunlarını giderme konusunda yardımcı olabilecek tüm ilgili günlükleri içerir. Daha sonra destek personeli ile paylaşabileceğiniz şifreli bir destek paketi oluşturmak için StorSimple için Windows PowerShell kullanabilirsiniz.

### <a name="to-view-the-logs-or-the-contents-of-the-support-package"></a>Destek paketinin günlüklerini veya içeriğini görüntülemek için
1. [Destek paketi oluşturma ve yönetme](storsimple-8000-create-manage-support-package.md)konusunda açıklandığı gibi destek paketi oluşturmak için StorSimple için Windows PowerShell kullanın.
2. [Şifre çözme betiğini](https://gallery.technet.microsoft.com/scriptcenter/Script-to-decrypt-a-a8d1ed65) istemci bilgisayarınızda yerel olarak indirin.
3. Destek paketini açmak ve şifresini çözmek için bu [adım adım yordamı](storsimple-8000-create-manage-support-package.md#edit-a-support-package) kullanın.
4. Şifresi çözülen destek paketi günlükleri ETW/etbir biçimde. Bu dosyaları Windows Olay Görüntüleyicisi görüntülemek için aşağıdaki adımları uygulayabilirsiniz:
   
   1. Olay Görüntüleyicisi başlatmak için Windows istemciniz üzerinde **eventvwr** komutunu çalıştırın.
   2. **Eylemler** bölmesinde, **kaydedilmiş günlüğü aç** ' a tıklayın ve etbir/ETW biçimindeki (destek paketi) günlük dosyalarının üzerine gelin. Artık dosyayı görüntüleyebilirsiniz. Dosyayı açtıktan sonra sağ tıklayıp dosyayı metin olarak kaydedebilirsiniz.
      
      > [!IMPORTANT]
      > Bu dosyaları Windows PowerShell 'de açmak için **Get-WinEvent** cmdlet 'ini de kullanabilirsiniz. Daha fazla bilgi için Windows PowerShell cmdlet başvurusu belgelerindeki [Get-WinEvent](/powershell/module/microsoft.powershell.diagnostics/get-winevent) bölümüne bakın.
     
5. Günlükler Olay Görüntüleyicisi açıldığında, cihaz yapılandırmasıyla ilgili sorunları içeren aşağıdaki günlüklere bakın:
   
   * hcs_pfconfig/Işletimsel günlük
   * hcs_pfconfig/config
6. Günlük dosyalarında, Kurulum Sihirbazı tarafından çağrılan cmdlet 'lerle ilgili dizeleri arayın. Bu cmdlet 'lerin bir listesi için bkz. [ilk seferlik kurulum Sihirbazı işlemi](#first-time-setup-wizard-process) .
7. Sorunun nedenini belirleyemeyebilirsiniz, sonraki adımlar için [Microsoft desteği başvurabilirsiniz](storsimple-8000-contact-microsoft-support.md) . Yardım için Microsoft Desteği iletişime geçerek [destek Isteği oluşturma](storsimple-8000-contact-microsoft-support.md#create-a-support-request) bölümündeki adımları kullanın.

## <a name="cmdlets-available-for-troubleshooting"></a>Sorun giderme için kullanılabilen cmdlet 'ler
Bağlantı hatalarını algılamak için aşağıdaki Windows PowerShell cmdlet 'lerini kullanın.

* `Get-NetAdapter`: Ağ arabirimlerinin sistem durumunu algılamak için bu cmdlet 'i kullanın.
* `Test-Connection`: Ağın içindeki ve dışındaki ağ bağlantısını denetlemek için bu cmdlet 'i kullanın.
* `Test-HcsmConnection`: Başarıyla kaydedilen bir cihazın bağlantısını denetlemek için bu cmdlet 'i kullanın.
* `Sync-HcsTime`: Cihaz süresini göstermek ve NTP sunucusuyla zaman eşitlemeye zorlamak için bu cmdlet 'i kullanın.
* `Enable-HcsPing` ve `Disable-HcsPing` : Bu cmdlet 'leri, ana bilgisayarların StorSimple cihazınızdaki ağ arabirimlerine ping yapmasına izin vermek için kullanın. Varsayılan olarak, StorSimple ağ arabirimleri, ping isteklerine yanıt vermez.
* `Trace-HcsRoute`: Bu cmdlet 'i yol izleme aracı olarak kullanın. Her yönlendiriciye bir süre içinde son hedefe yönelik paketleri gönderir ve ardından her bir atlamada döndürülen paketlere göre sonuçları hesaplar. Bu `Trace-HcsRoute` nedenle, belirli bir yönlendiricide veya bağlantıda paket kaybı derecesini gösterdiği için, hangi yönlendiricilerin veya bağlantıların ağ sorunlarına neden olabileceğini belirtebilirsiniz.
* `Get-HcsRoutingTable`: Yerel IP yönlendirme tablosunu göstermek için bu cmdlet 'i kullanın.

## <a name="troubleshoot-with-the-get-netadapter-cmdlet"></a>Get-NetAdapter cmdlet ile sorun giderme
Bir ilk kez cihaz dağıtımı için ağ arabirimlerini yapılandırdığınızda, cihaz henüz hizmete kayıtlı olmadığından StorSimple Device Manager hizmeti kullanıcı arabiriminde donanım durumu kullanılamaz. Ayrıca, özellikle de hizmet eşitlemesini etkileyen sorunlar varsa, **donanım durumu** dikey penceresi cihazın durumunu her zaman doğru yansıtmayabilir. Bu durumlarda, `Get-NetAdapter` ağ arabirimlerinizin sistem durumunu ve durumunu öğrenmek için cmdlet 'ini kullanabilirsiniz.

### <a name="to-see-a-list-of-all-the-network-adapters-on-your-device"></a>Cihazınızdaki tüm ağ bağdaştırıcılarının listesini görmek için
1. StorSimple için Windows PowerShell başlatın ve ardından yazın `Get-NetAdapter` . 
2. `Get-NetAdapter`Ağ arabiriminizdeki durumu anlamak için cmdlet 'in çıkışını ve aşağıdaki yönergeleri kullanın.
   
   * Arabirim sağlıklı ve etkinse, **Ifındex** durumu **yukarı** olarak gösterilir.
   * Arabirim sağlıklı ancak fiziksel olarak bağlı değilse (bir ağ kablosu ile) **Ifındex** **devre dışı** olarak gösterilir.
   * Arabirim sağlıklı ancak etkinleştirilmemişse, **Ifındex** durumu **Notas** olarak gösterilir.
   * Arabirim yoksa, bu listede görünmez. StorSimple Device Manager hizmeti kullanıcı arabirimi bu arabirimi yine de başarısız bir durumda göstermeye devam eder.

Bu cmdlet 'in nasıl kullanılacağı hakkında daha fazla bilgi için, Windows PowerShell cmdlet başvurusunda [Get-NetAdapter](/powershell/module/netadapter/get-netadapter) sayfasına gidin.

Aşağıdaki bölümlerde, cmdlet 'inin çıkış örnekleri gösterilmektedir `Get-NetAdapter` .

 Bu örneklerde, Controller 0 pasif denetleyiciydi ve şu şekilde yapılandırılmıştır:

* VERI 0, VERI 1, VERI 2 ve VERI 3 ağ arabirimleri cihazda vardı.
* VERI 4 ve VERI 5 ağ arabirim kartları yoktu; Bu nedenle, çıkışta listelenmez.
* 0 VERISI etkinleştirildi.

Denetleyici 1 etkin denetleyiciydi ve şu şekilde yapılandırılmıştır:

* VERI 0, VERI 1, VERI 2, VERI 3, VERI 4 ve VERI 5 Ağ arabirimleri cihazda vardı.
* 0 VERISI etkinleştirildi.

**Örnek çıkış – denetleyici 0**

Aşağıdaki örnek veri, denetleyici 0 ' dan (pasif denetleyici) alınan çıktıdır. VERI 1, VERI 2 ve VERI 3 bağlı değil. VERI 4 ve VERI 5, cihazda bulunmadığından listelenmez.

```output
Controller0>Get-NetAdapter
Name                 InterfaceDescription                        ifIndex  Status
------               --------------------                        -------  ----------
DATA3                Mellanox ConnectX-3 Ethernet Adapter #2     17       NotPresent
DATA2                Mellanox ConnectX-3 Ethernet Adapter        14       NotPresent
Ethernet 2           HCS VNIC                                    13       Up
DATA1                Intel(R) 82574L Gigabit Network Co...#2     16       NotPresent
DATA0                Intel(R) 82574L Gigabit Network Conn...     15       Up
```


**Örnek çıkış – denetleyici 1**

Aşağıdaki örnek veri, denetleyici 1 ' den (etkin denetleyici) alınan çıktıdır. Yalnızca cihazdaki DATA 0 ağ arabirimi yapılandırılır ve çalışır.

```output
Controller1>Get-NetAdapter
Name                 InterfaceDescription                        ifIndex  Status
------               --------------------                        -------  ----------
DATA3                Mellanox ConnectX-3 Ethernet Adapter        18       NotPresent
DATA2                Mellanox ConnectX-3 Ethernet Adapter #2     19       NotPresent
DATA1                Intel(R) 82574L Gigabit Network Co...#2     16       NotPresent
DATA0                Intel(R) 82574L Gigabit Network Conn...     15       Up
Ethernet 2           HCS VNIC                                    13       Up
DATA5                Intel(R) Gigabit ET Dual Port Server...     14       NotPresent
DATA4                Intel(R) Gigabit ET Dual Port Serv...#2     17       NotPresent
```


## <a name="troubleshoot-with-the-test-connection-cmdlet"></a>Test-Connection cmdlet ile sorun giderme
`Test-Connection`StorSimple cihazınızın dış ağa bağlanıp bağlanamamadığını öğrenmek için cmdlet 'ini kullanabilirsiniz. DNS dahil tüm ağ parametreleri Kurulum sihirbazında doğru yapılandırılmışsa, `Test-Connection` Outlook.com gibi ağ dışındaki bilinen bir adrese ping eklemek için cmdlet 'ini kullanabilirsiniz.

Ping cmdlet 'i devre dışıysa, bağlantı sorunlarını gidermek için ping komutunu etkinleştirmeniz gerekir.

Cmdlet 'inden aşağıdaki çıkış örneklerine bakın `Test-Connection` .

> [!NOTE]
> İlk örnekte, cihaz yanlış bir DNS ile yapılandırılır. İkinci örnekte DNS doğru olur.

**Örnek çıkış – yanlış DNS**

Aşağıdaki örnek, DNS 'in çözümlenmediğini belirten ıPV4 ve ıPV6 adresleri için herhangi bir çıkış içermez. Dış ağa bağlantı yoktur ve doğru bir DNS sağlanması gerekir.

```output
Source        Destination     IPV4Address      IPV6Address
------        -----------     -----------      -----------
HCSNODE0      outlook.com
HCSNODE0      outlook.com
HCSNODE0      outlook.com
HCSNODE0      outlook.com
```

**Örnek çıkış – doğru DNS**

Aşağıdaki örnekte DNS, DNS 'nin doğru yapılandırıldığını belirten ıPV4 adresini döndürür. Çıktı, dış ağa bağlantı olduğunu onaylar.

```output
Source        Destination     IPV4Address      IPV6Address
------        -----------     -----------      -----------
HCSNODE0      outlook.com     132.245.92.194
HCSNODE0      outlook.com     132.245.92.194
HCSNODE0      outlook.com     132.245.92.194
HCSNODE0      outlook.com     132.245.92.194
```

## <a name="troubleshoot-with-the-test-hcsmconnection-cmdlet"></a>Test-HcsmConnection cmdlet ile sorun giderme
`Test-HcsmConnection`StorSimple Device Manager hizmetinize zaten bağlı ve kayıtlı olan bir cihaz için cmdlet 'ini kullanın. Bu cmdlet, kayıtlı bir cihaz ve ilgili StorSimple Device Manager hizmeti arasındaki bağlantıyı doğrulamanıza yardımcı olur. Bu komutu StorSimple için Windows PowerShell üzerinde çalıştırabilirsiniz.

### <a name="to-run-the-test-hcsmconnection-cmdlet"></a>Test-HcsmConnection cmdlet 'ini çalıştırmak için
1. Cihazın kayıtlı olduğundan emin olun.
2. Cihaz durumunu denetleyin. Cihaz devre dışı bırakılmışsa, bakım modunda veya çevrimdışı olduğunda aşağıdaki hatalardan birini görebilirsiniz:
   
   * ErrorCode. Cıdevicedecommissioned: cihazın devre dışı bırakıldığını gösterir.
   * ErrorCode. DeviceNotReady: cihazın bakım modunda olduğunu gösterir.
   * ErrorCode. DeviceNotReady: cihazın çevrimiçi olmadığını gösterir.
3. StorSimple Device Manager hizmetinin çalıştığını doğrulayın ( [Get-ClusterResource](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee461004(v=technet.10)) cmdlet 'ini kullanın). Hizmet çalışmıyorsa, aşağıdaki hatalarla karşılaşabilirsiniz:
   
   * ErrorCode. CiSApplianceAgentNotOnline
   * ErrorCode. Cıpowershellscripthcserror: Get-ClusterResource çalıştırdığınızda bir özel durum olduğunu gösterir.
4. Access Control Service (ACS) belirtecini denetleyin. Bir Web özel durumu oluşturursa, bir ağ geçidi sorunu, eksik proxy kimlik doğrulaması, yanlış DNS veya kimlik doğrulama hatası olabilir. Aşağıdaki hatalarla karşılaşabilirsiniz:
   
   * ErrorCode. CiSApplianceGateway: bir HttpStatusCode. BadGateway özel durumunu belirtir: ad çözümleyici Hizmeti ana bilgisayar adını çözümleyemedi.
   * ErrorCode. CiSApplianceProxy: bir HttpStatusCode. ProxyAuthenticationRequired özel durumu gösterir (HTTP durum kodu 407): istemci, ara sunucu ile kimlik doğrulaması yapamadı.
   * ErrorCode. CiSApplianceDNSError: bir WebExceptionStatus. NameResolutionFailure özel durumu belirtir: ad çözümleyici Hizmeti ana bilgisayar adını çözümleyemedi.
   * ErrorCode. CiSApplianceACSError: hizmetin bir kimlik doğrulama hatası döndürmediğini, ancak bağlantı olduğunu gösterir.
     
     Web özel durumu oluşturmadığından, gerecin başarısız olduğunu gösteren ErrorCode. CiSApplianceFailure olup olmadığını denetleyin.
5. Bulut hizmeti bağlantısını kontrol edin. Hizmet bir Web özel durumu oluşturursa, aşağıdaki hataları görebilirsiniz:
   
   * ErrorCode. CiSApplianceGateway: bir HttpStatusCode. BadGateway özel durumunu belirtir: ara sunucu sunucusu başka bir proxy 'den veya özgün sunucudan hatalı bir istek aldı.
   * ErrorCode. CiSApplianceProxy: bir HttpStatusCode. ProxyAuthenticationRequired özel durumu gösterir (HTTP durum kodu 407): istemci, ara sunucu ile kimlik doğrulaması yapamadı.
   * ErrorCode. CiSApplianceDNSError: bir WebExceptionStatus. NameResolutionFailure özel durumu belirtir: ad çözümleyici Hizmeti ana bilgisayar adını çözümleyemedi.
   * ErrorCode. CiSApplianceACSError: hizmetin bir kimlik doğrulama hatası döndürmediğini, ancak bağlantı olduğunu gösterir.
     
     Bir Web özel durumu oluşturmadığından, StorSimple Device Manager hizmetiyle ilgili bir sorun olduğunu bildiren ErrorCode. CiSApplianceSaasServiceError olup olmadığını denetleyin.
6. Azure Service Bus bağlantısını denetleyin. ErrorCode. CiSApplianceServiceBusError, cihazın Service Bus bağlanamadığını gösterir.

CiSCommandletLog0Curr. errlog ve CiSAgentsvc0Curr. errlog günlük dosyaları, özel durum ayrıntıları gibi daha fazla bilgiye sahip olacaktır.

Cmdlet 'in nasıl kullanılacağı hakkında daha fazla bilgi için, Windows PowerShell başvuru belgelerindeki [Test-HcsmConnection](/previous-versions/windows/powershell-scripting/dn715782(v=wps.630)) ' a gidin.

> [!IMPORTANT]
> Bu cmdlet 'i hem etkin hem de pasif denetleyici için çalıştırabilirsiniz.

Cmdlet 'inden aşağıdaki çıkış örneklerine bakın `Test-HcsmConnection` .

**Örnek çıktı – StorSimple güncelleştirme 3 çalıştıran cihaz başarıyla kaydedildi**

```output
Controller1>Test-HcsmConnection

Checking device registration state  ... Success
Device registered successfully

Checking primary NTP server [time.windows.com] ... Success

Checking web proxy  ... NOT SET

Checking primary IPv4 DNS server [10.222.118.154] ... Success
Checking primary IPv6 DNS server  ... NOT SET
Checking secondary IPv4 DNS server [10.222.120.24] ... Success
Checking secondary IPv6 DNS server  ... NOT SET

Checking device online  ... Success

Checking device authentication  ... This will take a few minutes.
Checking device authentication  ... Success

Checking connectivity from device to service  ... This will take a few minutes.

Checking connectivity from device to service  ... Success

Checking connectivity from service to device  ... Success

Checking connectivity to Microsoft Update servers  ... Success
Controller1>
```

**Örnek çıkış – çevrimdışı cihaz** 

Bu örnek, Azure portal **çevrimdışı** durumuna sahip bir cihazdan oluşur.

```output
Checking device registrationstate: Success
Device is registered successfully
Checking connectivity from device to SaaS.. Failure
```

Cihaz, geçerli Web proxy yapılandırmasını kullanarak bağlanamadı. Web proxy yapılandırması veya ağ bağlantısı sorunu ile ilgili bir sorun olabilir. Bu durumda, Web proxy ayarlarınızın doğru olduğundan ve Web proxy sunucularınızın çevrimiçi ve ulaşılabilir olduğundan emin olun.

## <a name="troubleshoot-with-the-sync-hcstime-cmdlet"></a>Sync-HcsTime cmdlet ile sorun giderme
Cihaz saatini göstermek için bu cmdlet 'i kullanın. Cihaz saati NTP sunucusuyla bir uzaklığa sahipse, bu cmdlet 'i kullanarak saati NTP sunucunuz ile eşitler.
- Cihaz ile NTP sunucusu arasındaki fark 5 dakikadan fazlaysa bir uyarı görürsünüz. 
- Fark 15 dakikayı aşarsa, cihaz çevrimdışı duruma geçer. Zaman eşitlemesini zorlamak için bu cmdlet 'i kullanmaya devam edebilirsiniz. 
- Ancak, fark 15 saati aşarsa, zamandan önce eşitleme yapamazsınız ve bir hata iletisi gösterilir.

**Örnek çıkış: eşitleme kullanarak zorlamalı zaman eşitleme-HcsTime**

```output
Controller0>Sync-HcsTime
The current device time is 4/24/2015 4:05:40 PM UTC.

Time difference between NTP server and appliance is 00.0824069 seconds. Do you want to resync time with NTP server?
[Y] Yes [N] No (Default is "Y"): Y
Controller0>
```

## <a name="troubleshoot-with-the-enable-hcsping-and-disable-hcsping-cmdlets"></a>Enable-HcsPing ve Disable-HcsPing cmdlet 'leriyle sorun giderme
Cihazınızdaki ağ arabirimlerinin ıCMP ping isteklerine yanıt vermesini sağlamak için bu cmdlet 'leri kullanın. Varsayılan olarak, StorSimple ağ arabirimleri, ping isteklerine yanıt vermez. Bu cmdlet 'in kullanılması, cihazınızın çevrimiçi ve erişilebilir olup olmadığını öğrenmenin en kolay yoludur.

**Örnek çıkış – Enable-HcsPing ve Disable-HcsPing**

```output
Controller0>
Controller0>Enable-HcsPing
Successfully enabled ping.
Controller0>
Controller0>
Controller0>Disable-HcsPing
Successfully disabled ping.
Controller0>
```

## <a name="troubleshoot-with-the-trace-hcsroute-cmdlet"></a>Trace-HcsRoute cmdlet ile sorun giderme
Bu cmdlet 'i yol izleme aracı olarak kullanın. Her yönlendiriciye bir süre içinde son hedefe yönelik paketleri gönderir ve ardından her bir atlamada döndürülen paketlere göre sonuçları hesaplar. Cmdlet 'i belirli bir yönlendiricide veya bağlantıda paket kaybı derecesini gösterdiği için, hangi yönlendiricilerin veya bağlantıların ağ sorunlarına neden olabileceğini belirtebilirsiniz.

**Trace-HcsRoute ile bir paketin yolunu izlemeyi gösteren örnek çıktı**

```output
Controller0>Trace-HcsRoute -Target 10.126.174.25

Tracing route to contoso.com [10.126.174.25]
over a maximum of 30 hops:
   0  HCSNode0 [10.126.173.90]
   1  contoso.com [10.126.174.25]

Computing statistics for 25 seconds...
            Source to Here   This Node/Link
Hop  RTT    Lost/Sent = Pct  Lost/Sent = Pct  Address
   0                                           HCSNode0 [10.126.173.90]
                                 0/ 100 =  0%   |
   1    0ms     0/ 100 =  0%     0/ 100 =  0%  contoso.com
[10.126.174.25]

Trace complete.
```

## <a name="troubleshoot-with-the-get-hcsroutingtable-cmdlet"></a>Get-HcsRoutingTable cmdlet ile sorun giderme
StorSimple cihazınız için yönlendirme tablosunu görüntülemek için bu cmdlet 'i kullanın. Yönlendirme tablosu, bir Internet Protokolü (IP) ağı üzerinden seyahat edilen veri paketlerinin nerede yönlendirilmeyeceğini belirlemede yardımcı olabilecek bir kurallar kümesidir.

Yönlendirme tablosu, verileri belirtilen ağlara yönlendiren arabirimleri ve ağ geçidini gösterir. Ayrıca, belirli bir hedefe ulaşmak için alınan yol için karar veren olan yönlendirme ölçüsünü de sağlar. Yönlendirme ölçümü ne kadar düşükse tercih daha yüksektir.

Örneğin, Internet 'e bağlı iki ağ arabirimi (DATA 2 ve DATA 3) olduğunu varsayalım. DATA 2 ve DATA 3 için yönlendirme ölçümleri sırasıyla 15 ve 261 ise, daha düşük yönlendirme ölçümüyle VERI 2, Internet 'e erişmek için kullanılan tercih edilen arabirimdir.

StorSimple cihazınızda güncelleştirme 1 ' i çalıştırıyorsanız, VERI 0 ağ arabiriminiz, bulut trafiği için en yüksek tercihe sahiptir. Bu yapılandırmayla, diğer bulut özellikli arabirimler olsa bile, bulut trafiğinin çoğu VERI 0 üzerinden yönlendirilir.<!--This implies that even if there are other cloud-enabled interfaces, the cloud traffic would be routed through DATA 0. - Preceding sentence replaces this one. Use of "This implies" is a bit murky. DATA 0 will be the preferred network interface? Is my translation OK?-->

`Get-HcsRoutingTable`Cmdlet 'i herhangi bir parametre belirtmeden çalıştırırsanız (aşağıdaki örnekte gösterildiği gibi), cmdlet hem IPv4 hem de IPv6 yönlendirme tablolarının çıktısını görüntüler. Alternatif olarak, `Get-HcsRoutingTable -IPv4` ilgili yönlendirme tablosunu da belirtebilir veya seçebilirsiniz `Get-HcsRoutingTable -IPv6`  .

```output
Controller0>
Controller0>Get-HcsRoutingTable
===========================================================================
Interface List
   14...00 50 cc 79 63 40 ......Intel(R) 82574L Gigabit Network Connection
   12...02 9a 0a 5b 98 1f ......Microsoft Failover Cluster Virtual Adapter
   13...28 18 78 bc 4b 85 ......HCS VNIC
   1...........................Software Loopback Interface 1
   21...00 00 00 00 00 00 00 e0 Microsoft ISATAP Adapter #2
   22...00 00 00 00 00 00 00 e0 Microsoft ISATAP Adapter #3
===========================================================================

IPv4 Route Table
===========================================================================
Active Routes:
Network Destination        Netmask          Gateway       Interface  Metric
            0.0.0.0          0.0.0.0  192.168.111.100  192.168.111.101     15
         127.0.0.0        255.0.0.0         On-link         127.0.0.1    306
         127.0.0.1  255.255.255.255         On-link         127.0.0.1    306
   127.255.255.255  255.255.255.255         On-link         127.0.0.1    306
      169.254.0.0      255.255.0.0         On-link     169.254.1.235    261
      169.254.1.235  255.255.255.255         On-link     169.254.1.235    261
   169.254.255.255  255.255.255.255         On-link     169.254.1.235    261
      192.168.111.0    255.255.255.0         On-link   192.168.111.101    266
   192.168.111.101  255.255.255.255         On-link   192.168.111.101    266
   192.168.111.255  255.255.255.255         On-link   192.168.111.101    266
         224.0.0.0        240.0.0.0         On-link         127.0.0.1    306
         224.0.0.0        240.0.0.0         On-link     169.254.1.235    261
         224.0.0.0        240.0.0.0         On-link   192.168.111.101    266
   255.255.255.255  255.255.255.255         On-link         127.0.0.1    306
   255.255.255.255  255.255.255.255         On-link     169.254.1.235    261
   255.255.255.255  255.255.255.255         On-link   192.168.111.101    266
===========================================================================
Persistent Routes:
   Network Address          Netmask  Gateway Address  Metric
            0.0.0.0          0.0.0.0  192.168.111.100       5
===========================================================================

IPv6 Route Table
===========================================================================
Active Routes:
   If Metric Network Destination      Gateway
   1    306 ::1/128                  On-link
   13    276 fd99:4c5b:5525:d80b::/64 On-link
   13    276 fd99:4c5b:5525:d80b::1/128
                                    On-link
   13    276 fd99:4c5b:5525:d80b::3/128
                                    On-link
   13    276 fe80::/64                On-link
   12    261 fe80::/64                On-link
   13    276 fe80::17a:4eba:7c80:727f/128
                                    On-link
   12    261 fe80::fc97:1a53:e81a:3454/128
                                    On-link
   1    306 ff00::/8                 On-link
   13    276 ff00::/8                 On-link
   12    261 ff00::/8                 On-link
   14    266 ff00::/8                 On-link
===========================================================================
Persistent Routes:
   None

Controller0>
```

## <a name="step-by-step-storsimple-troubleshooting-example"></a>Adım adım StorSimple sorun giderme örneği
Aşağıdaki örnekte, StorSimple dağıtımının adım adım sorunlarını giderme gösterilmektedir. Örnek senaryoda, cihaz kaydı, ağ ayarlarının veya DNS adının yanlış olduğunu belirten bir hata iletisiyle başarısız olur.

Döndürülen hata iletisi:

```output
Invoke-HcsSetupWizard: An error has occurred while registering the device. This could be due to incorrect IP address or DNS name. Please check your network settings and try again. If the problems persist, contact Microsoft Support.
+CategoryInfo: Not specified
+FullyQualifiedErrorID: CiSClientCommunicationErros, Microsoft.HCS.Management.PowerShell.Cmdlets.InvokeHcsSetupWizardCommand
```

Hataya aşağıdaki sorunlardan biri neden olmuş olabilir:

* Hatalı donanım yüklemesi
* Hatalı ağ arabirimi
* Yanlış IP adresi, alt ağ maskesi, ağ geçidi, birincil DNS sunucusu veya Web ara sunucusu
* Kayıt anahtarı yanlış
* Yanlış güvenlik duvarı ayarları

### <a name="to-locate-and-fix-the-device-registration-problem"></a>Cihaz kayıt sorununu bulmak ve gidermek için
1. Cihaz yapılandırmanızı denetleyin: etkin denetleyicide, öğesini çalıştırın `Invoke-HcsSetupWizard` .
   
   > [!NOTE]
   > Kurulum Sihirbazı etkin denetleyicide çalışmalıdır. Etkin denetleyiciye bağlı olduğunuzu doğrulamak için, seri konsolunda sunulan ana başlığa bakın. Başlık, denetleyici 0 veya denetleyici 1 ' e bağlanıp bağlanmadığınızı ve denetleyicinin etkin veya pasif olduğunu gösterir. Daha fazla bilgi için [cihazınızda etkin denetleyiciyi tanımla](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device)bölümüne gidin.
   
2. Cihazın doğru şekilde kabloda bulunduğundan emin olun: cihaz arka düzleminde ağ kablolamasını denetleyin. Kablo, cihaz modeline özeldir. Daha fazla bilgi için, [storsimple 8100 cihazınızı yüklemeye](storsimple-8100-hardware-installation.md) veya [StorSimple 8600 cihazınızı yüklemenize](storsimple-8600-hardware-installation.md)gidin.
   
   > [!NOTE]
   > 10 GbE ağ bağlantı noktası kullanıyorsanız, belirtilen QSFP-SFP bağdaştırıcılarını ve SFP kablolarını kullanmanız gerekir. Daha fazla bilgi için, [10 GbE bağlantı noktası için önerilen kablolar, anahtarlar ve alıcı vericiler listesine](storsimple-supported-hardware-for-10-gbe-network-interfaces.md)bakın.
  
3. Ağ arabiriminin sistem durumunu doğrulayın:
   
   * VERI 0 için ağ arabirimlerinin sistem durumunu algılamak üzere Get-NetAdapter cmdlet 'ini kullanın. 
   * Bağlantı çalışmıyorsa, `ifindex` durum arabirimin bittiğini gösterir. Daha sonra, bağlantı noktasının gerecine ve anahtara ağ bağlantısını denetlemeniz gerekir. Ayrıca, hatalı kablolar için de kural oluşturmanız gerekir. 
   * Etkin denetleyicideki VERI 0 bağlantı noktasının başarısız olduğundan şüphelenirseniz, denetleyici 1 ' deki DATA 0 bağlantı noktasına bağlanarak bu işlemi doğrulayabilirsiniz. Ağ kablosunu cihazın geri kaynağından denetleyici 0 ' dan sökün, kabloyu Controller 1 ' e bağlayın ve sonra Get-NetAdapter cmdlet 'ini yeniden çalıştırın.
   
     Bir denetleyicideki VERI 0 bağlantı noktası başarısız olursa, sonraki adımlar için [Microsoft desteği başvurun](storsimple-8000-contact-microsoft-support.md) . Sisteminizdeki denetleyiciyi değiştirmeniz gerekebilir.
4. Anahtarla bağlantıyı doğrulayın:
   
   * Birincil muhafazadaki denetleyici 0 ve denetleyici 1 üzerindeki VERI 0 ağ arabirimlerinin aynı alt ağda olduğundan emin olun. 
   * Hub veya yönlendiriciyi denetleyin. Genellikle, her iki denetleyicisi de aynı hub 'a veya yönlendiriciye bağlamanız gerekir. 
   * Bağlantı için kullandığınız anahtarların her iki denetleyici için de aynı vLAN 'daki VERI 0 ' a sahip olduğundan emin olun.
5. Kullanıcı hatalarını ortadan kaldırın:
   
   * Kurulum sihirbazını yeniden çalıştırın ( **Invoke-HcsSetupWizard**' ı çalıştırın) ve hata olmadığından emin olmak için değerleri yeniden girin. 
   * Kullanılan kayıt anahtarını doğrulayın. Aynı kayıt anahtarı, birden çok cihazı StorSimple Device Manager hizmetine bağlamak için kullanılabilir. Doğru kayıt anahtarını kullandığınızdan emin olmak için [hizmet kayıt anahtarını al](storsimple-8000-manage-service.md#get-the-service-registration-key) bölümündeki yordamı kullanın.
     
     > [!IMPORTANT]
     > Çalıştıran birden fazla hizmet varsa, cihazı kaydetmek için uygun hizmet kayıt anahtarının kullanıldığından emin olmanız gerekir. Yanlış StorSimple Device Manager hizmetine sahip bir cihaz kaydettiniz, sonraki adımlar için [Microsoft desteği başvurmanız](storsimple-8000-contact-microsoft-support.md) gerekir. Cihazın fabrika sıfırlamasını (veri kaybına neden olabilir), daha sonra amaçlanan hizmete bağlayabilmeniz gerekebilir.
     > 
     > 
6. Dış ağa bağlantınız olduğunu doğrulamak için Test-Connection cmdlet 'ini kullanın. Daha fazla bilgi için [Test-Connection cmdlet Ile sorun giderme](#troubleshoot-with-the-test-connection-cmdlet)bölümüne gidin.
7. Güvenlik Duvarı girişim olup olmadığını denetleyin. Sanal IP (VIP), alt ağ, ağ geçidi ve DNS ayarlarının tümünün doğru olduğunu doğruladıysanız ve hala bağlantı sorunları görüyorsanız, güvenlik duvarınızın cihazınız ve dış ağ arasındaki iletişimi engellemesi mümkündür. 80 ve 443 bağlantı noktalarının giden iletişim için StorSimple cihazınızda kullanılabilir olduğundan emin olun. Daha fazla bilgi için bkz. [StorSimple cihazınız Için ağ gereksinimleri](storsimple-8000-system-requirements.md#networking-requirements-for-your-storsimple-device).
8. Günlüklere bakın. [Sorun giderme Için destek paketleri ve cihaz günlüklerine](#support-packages-and-device-logs-available-for-troubleshooting)gidin.
9. Yukarıdaki adımlar sorunu çözmezse, yardım için [Microsoft desteği başvurun](storsimple-8000-contact-microsoft-support.md) .

## <a name="next-steps"></a>Sonraki adımlar
[Bir StorSimple cihazında sorun gidermek Için Tanılama aracını nasıl kullanacağınızı öğrenin](storsimple-8000-diagnostics.md).

<!--Link references-->

[1]: /previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd379547(v=ws.10)
[2]: /previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd392266(v=ws.10)