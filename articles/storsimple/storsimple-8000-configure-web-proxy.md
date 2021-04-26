---
title: StorSimple 8000 serisi cihaz için Web proxy ayarlama | Microsoft Docs
description: StorSimple cihazınız için Web proxy ayarlarını yapılandırmak üzere StorSimple için Windows PowerShell nasıl kullanacağınızı öğrenin.
services: storsimple
documentationcenter: ''
author: alkohli
manager: ''
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/19/2017
ms.author: alkohli
ms.openlocfilehash: 48671e7558ea1bd613d33372c96fa3c563407e81
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96017228"
---
# <a name="configure-web-proxy-for-your-storsimple-device"></a>StorSimple cihazınız için Web proxy 'yi yapılandırma

## <a name="overview"></a>Genel Bakış

Bu öğreticide, StorSimple cihazınız için Web proxy ayarlarını yapılandırmak ve görüntülemek üzere StorSimple için Windows PowerShell nasıl kullanılacağı açıklanmaktadır. Web proxy ayarları, bulut ile iletişim kurulurken StorSimple cihazı tarafından kullanılır. Bir Web proxy sunucusu, başka bir güvenlik katmanı eklemek, içerik filtrelemek, önbelleğe bant genişliği gereksinimlerini kolaylaştırmak için, hatta analizler hakkında yardım almak için kullanılır.

Bu öğreticideki kılavuz yalnızca StorSimple 8000 serisi fiziksel cihazları için geçerlidir. Web proxy yapılandırması, StorSimple Cloud Appliance (8010 ve 8020) üzerinde desteklenmez.

Web proxy, StorSimple cihazınız için _isteğe bağlı_ bir yapılandırmadır. Web proxy 'yi yalnızca StorSimple için Windows PowerShell aracılığıyla yapılandırabilirsiniz. Yapılandırma, aşağıdaki gibi iki adımlı bir işlemdir:

1. Önce, Kurulum Sihirbazı veya StorSimple için Windows PowerShell cmdlet 'leri aracılığıyla Web proxy ayarlarını yapılandırırsınız.
2. Daha sonra, StorSimple için Windows PowerShell cmdlet 'leri aracılığıyla yapılandırılmış Web proxy ayarlarını etkinleştirirsiniz.

Web proxy yapılandırması tamamlandıktan sonra, yapılandırılan Web proxy ayarlarını hem Microsoft Azure StorSimple Device Manager hizmetinde hem de StorSimple için Windows PowerShell görüntüleyebilirsiniz.

Bu öğreticiyi okuduktan sonra şunları yapabilirsiniz:

* Kurulum sihirbazını ve cmdlet 'lerini kullanarak Web proxy 'yi yapılandırın.
* Cmdlet 'lerini kullanarak Web proxy 'yi etkinleştirin.
* Azure portal web proxy ayarlarını görüntüleyin.
* Web proxy yapılandırması sırasında hata giderme.


## <a name="configure-web-proxy-via-windows-powershell-for-storsimple"></a>Web proxy 'sini StorSimple için Windows PowerShell aracılığıyla yapılandırma

Web proxy ayarlarını yapılandırmak için aşağıdakilerden birini kullanın:

* Yapılandırma adımlarında size rehberlik etmek için Kurulum Sihirbazı.
* StorSimple için Windows PowerShell cmdlet 'leri.

Bu yöntemlerin her biri aşağıdaki bölümlerde ele alınmıştır.

## <a name="configure-web-proxy-via-the-setup-wizard"></a>Kurulum Sihirbazı aracılığıyla Web proxy 'yi yapılandırma

Web proxy yapılandırması adımlarında size rehberlik etmek için Kurulum Sihirbazı 'nı kullanın. Cihazınızda Web proxy 'yi yapılandırmak için aşağıdaki adımları gerçekleştirin.

#### <a name="to-configure-web-proxy-via-the-setup-wizard"></a>Kurulum Sihirbazı aracılığıyla Web proxy 'yi yapılandırmak için

1. Seri konsol menüsünde, seçenek 1 ' i seçin, **tam erişim Ile oturum açın** ve **Cihaz Yöneticisi parolasını** belirtin. Bir Kurulum Sihirbazı oturumu başlatmak için aşağıdaki komutu yazın:
   
    `Invoke-HcsSetupWizard`
2. Bu, cihaz kaydı için Kurulum Sihirbazı 'nı ilk kez kullandıysanız, Web proxy yapılandırmasına ulaşana kadar gerekli tüm ağ ayarlarını yapılandırmanız gerekir. Cihazınız zaten kaydedilmişse, Web proxy yapılandırmasına ulaşana kadar yapılandırılmış tüm ağ ayarlarını kabul edin. Kurulum sihirbazında, Web proxy ayarlarını yapılandırmak isteyip istemediğiniz sorulduğunda **Evet** yazın.
3. **Web proxy URL 'si** için, Web ara sunucunuzun IP adresini veya tam etki alanı adını (FQDN) ve cihazınızın buluttan iletişim kurarken kullanmasını istediğiniz TCP bağlantı noktası numarasını belirtin. Şu biçimi kullanın:
   
    `http://<IP address or FQDN of the web proxy server>:<TCP port number>`
   
    Varsayılan olarak, 8080 numaralı TCP bağlantı noktası belirtilir.
4. Kimlik doğrulama türünü **NTLM**, **temel** veya **none** olarak seçin. Temel, proxy sunucusu yapılandırması için en az güvenli kimlik doğrulamadır. NT LAN Manager (NTLM), bir kullanıcının kimliğini doğrulamak için üç yönlü bir mesajlaşma sistemi (bazen ek bütünlük gerekliyse dördü) kullanan yüksek düzeyde güvenli ve karmaşık bir kimlik doğrulama protokolüdür. Varsayılan kimlik doğrulaması NTLM 'dir. Daha fazla bilgi için bkz. [temel](https://hc.apache.org/httpclient-3.x/authentication.html) ve [NTLM kimlik doğrulaması](https://hc.apache.org/httpclient-3.x/authentication.html). 
   
   > [!IMPORTANT]
   > **StorSimple Device Manager hizmetinde, cihazın ara sunucu yapılandırmasında temel veya NTLM kimlik doğrulaması etkinleştirildiğinde cihaz izleme grafikleri çalışmaz. İzleme grafiklerinin çalışması için, kimlik doğrulamasının NONE olarak ayarlandığından emin olmanız gerekir.**
  
5. Kimlik doğrulamasını etkinleştirdiyseniz, bir **Web Proxy Kullanıcı adı** ve bir **Web proxy parolası** sağlayın. Parolayı da onaylamanız gerekir.
   
    ![StorSimple Device1 'Ta Web proxy 'Yi yapılandırma](./media/storsimple-configure-web-proxy/IC751830.png)

Cihazınızı ilk kez kaydediyorsanız kaydıyla devam edin. Cihazınız zaten kaydedilmişse, sihirbaz çıkar. Yapılandırılan ayarlar kaydedilir.

Web proxy artık etkin. [Web proxy 'Yi etkinleştir](#enable-web-proxy) adımını atlayıp doğrudan [Azure Portal Web proxy ayarlarını görüntüleme](#view-web-proxy-settings-in-the-azure-portal)sayfasına gidebilirsiniz.

## <a name="configure-web-proxy-via-windows-powershell-for-storsimple-cmdlets"></a>StorSimple için Windows PowerShell cmdlet 'leri aracılığıyla Web proxy yapılandırma

Web proxy ayarlarını yapılandırmanın alternatif bir yolu StorSimple için Windows PowerShell cmdlet 'lerini kullanmaktır. Web proxy 'yi yapılandırmak için aşağıdaki adımları gerçekleştirin.

#### <a name="to-configure-web-proxy-via-cmdlets"></a>Cmdlet 'ler aracılığıyla Web proxy 'yi yapılandırmak için
1. Seri konsol menüsünde, seçenek 1 ' i seçin, **tam erişim Ile oturum açın**. İstendiğinde, **Cihaz Yöneticisi parolasını** belirtin. Varsayılan parola `Password1` .
2. Komut istemine şunları yazın:
   
    `Set-HcsWebProxy -Authentication NTLM -ConnectionURI "<http://<IP address or FQDN of web proxy server>:<TCP port number>" -Username "<Username for web proxy server>"`
   
    İstendiğinde parolayı sağlayın ve onaylayın.
   
    ![StorSimple Device3 'Ta Web proxy 'Yi yapılandırma](./media/storsimple-configure-web-proxy/IC751831.png)

Web proxy artık yapılandırıldı ve etkinleştirilmesi gerekiyor.

## <a name="enable-web-proxy"></a>Web proxy 'yi etkinleştir

Web proxy varsayılan olarak devre dışıdır. StorSimple cihazınızda Web proxy ayarlarını yapılandırdıktan sonra, Web proxy ayarlarını etkinleştirmek için StorSimple için Windows PowerShell kullanın.

> [!NOTE]
> **Web proxy 'yi yapılandırmak için Kurulum Sihirbazı 'nı kullandıysanız bu adım gerekli değildir. Web proxy, Kurulum Sihirbazı oturumundan sonra otomatik olarak varsayılan olarak etkinleştirilir.**


Cihazınızda Web proxy 'sini etkinleştirmek için StorSimple için Windows PowerShell aşağıdaki adımları gerçekleştirin:

#### <a name="to-enable-web-proxy"></a>Web proxy 'yi etkinleştirmek için
1. Seri konsol menüsünde, seçenek 1 ' i seçin, **tam erişim Ile oturum açın**. İstendiğinde, **Cihaz Yöneticisi parolasını** belirtin. Varsayılan parola `Password1` .
2. Komut istemine şunları yazın:
   
    `Enable-HcsWebProxy`
   
    StorSimple cihazınızda Web proxy yapılandırmasını artık etkinleştirdiniz.
   
    ![StorSimple Device4 'Ta Web proxy 'Yi yapılandırma](./media/storsimple-configure-web-proxy/IC751832.png)

## <a name="view-web-proxy-settings-in-the-azure-portal"></a>Azure portal web proxy ayarlarını görüntüleme

Web proxy ayarları Windows PowerShell arabirimi aracılığıyla yapılandırılır ve Portal içinden değiştirilemez. Ancak, bu yapılandırılan ayarları portalda görüntüleyebilirsiniz. Web proxy 'yi görüntülemek için aşağıdaki adımları gerçekleştirin.

#### <a name="to-view-web-proxy-settings"></a>Web proxy ayarlarını görüntülemek için
1. **StorSimple Device Manager hizmeti > cihazlara** gidin. Bir cihazı seçip tıklatın ve ardından **cihaz ayarları > ağ**' a gidin.

    ![Ağ seçeneğine tıklayın](./media/storsimple-8000-configure-web-proxy/view-web-proxy-1.png)

2. **Ağ ayarları** dikey penceresinde **Web proxy** kutucuğuna tıklayın.

    ![Web proxy 'ye tıklayın](./media/storsimple-8000-configure-web-proxy/view-web-proxy-2.png)

3. **Web proxy** dikey penceresinde, StorSimple cihazınızda yapılandırılmış Web proxy ayarlarını gözden geçirin.
   
    ![Web proxy ayarlarını görüntüle](./media/storsimple-8000-configure-web-proxy/view-web-proxy-3.png)


## <a name="errors-during-web-proxy-configuration"></a>Web proxy yapılandırması sırasında hatalar

Web proxy ayarları yanlış yapılandırılmışsa, StorSimple için Windows PowerShell kullanıcıya hata iletileri görüntülenir. Aşağıdaki tabloda bu hata iletilerinin bazıları, olası nedenleri ve önerilen eylemler açıklanmaktadır.

| Seri No. | HRESULT hata kodu | Olası kök nedeni | Önerilen eylem |
|:--- |:--- |:--- |:--- |
| 1. |0x80070001 |Komut pasif denetleyiciden çalıştırılır ve etkin denetleyici ile iletişim kuramaz. |Etkin denetleyicide komutunu çalıştırın. Bu komutu pasif denetleyiciden çalıştırmak için pasif bilgisayardan etkin denetleyiciye olan bağlantıyı çözmeniz gerekir. Bu bağlantı bozulur Microsoft Desteği uygulamanız gerekir. |
| 2. |0x800710dd-işlem tanımlayıcısı geçerli değil |Proxy ayarları StorSimple Cloud Appliance desteklenmez. |Proxy ayarları StorSimple Cloud Appliance desteklenmez. Bunlar yalnızca bir StorSimple fiziksel cihazında yapılandırılabilir. |
| 3. |0x80070057-geçersiz parametre |Proxy ayarları için belirtilen parametrelerden biri geçerli değil. |URI doğru biçimde sağlanmadı. Aşağıdaki biçimi kullanın: `http://<IP address or FQDN of the web proxy server>:<TCP port number>` |
| 4. |0x800706ba-RPC sunucusu kullanılamıyor |Kök nedeni aşağıdakilerden biridir:</br></br>Küme yok. </br></br>DataPath hizmeti çalışmıyor.</br></br>Komut pasif denetleyiciden çalıştırılır ve etkin denetleyici ile iletişim kuramaz. |Kümenin yukarı ve DataPath hizmetinin çalıştığından emin olmak için Microsoft Desteği çalışır.</br></br>Etkin denetleyicideki komutu çalıştırın. Komutu pasif denetleyicisinden çalıştırmak istiyorsanız, pasif denetleyicinin etkin denetleyici ile iletişim kurabildiğinden emin olmanız gerekir. Bu bağlantı bozulur Microsoft Desteği uygulamanız gerekir. |
| 5. |0x800706in-RPC çağrısı başarısız oldu |Küme çalışmıyor. |Kümenin çalıştığından emin olmak için Microsoft Desteği çalışır. |
| 6. |0x8007138f-küme kaynağı bulunamadı |Platform hizmeti küme kaynağı bulunamadı. Yükleme düzgün olmadığında bu durum oluşabilir. |Cihazınızda fabrika sıfırlaması yapmanız gerekebilir. Bir platform kaynağı oluşturmanız gerekebilir. Sonraki adımlar için Microsoft Desteği'ne başvurun. |
| 7. |0x8007138c-küme kaynağı çevrimiçi değil |Platform veya DataPath küme kaynakları çevrimiçi değil. |DataPath ve platform hizmeti kaynağının çevrimiçi olduğundan emin olmak için Microsoft Desteği başvurun. |

> [!NOTE]
> * Yukarıdaki hata iletileri listesi ayrıntılı değildir.
> * Web proxy ayarlarıyla ilgili hatalar, StorSimple Device Manager hizmetinizde Azure portal gösterilmez. Yapılandırma tamamlandıktan sonra Web proxy ile ilgili bir sorun varsa, cihaz durumu klasik portalda **çevrimdışı** olarak değişir. |

## <a name="next-steps"></a>Sonraki Adımlar
* Cihazınızı dağıtma veya Web proxy ayarlarını yapılandırma sırasında herhangi bir sorunla karşılaşırsanız, [StorSimple cihaz dağıtımınızda sorun giderme](./storsimple-8000-troubleshoot-deployment.md)bölümüne bakın.
* StorSimple Device Manager hizmetini kullanmayı öğrenmek için, StorSimple cihazınızı yönetmek için StorSimple [Device Manager hizmetini kullanma](storsimple-8000-manager-service-administration.md)bölümüne gidin.