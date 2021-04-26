---
title: StorSimple 8000 serisi cihaz yapılandırmasını değiştirme | Microsoft Docs
description: Zaten dağıtılmış olan bir StorSimple cihazını yeniden yapılandırmak için StorSimple Device Manager hizmetinin nasıl kullanılacağını açıklar.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 09/28/2017
ms.author: alkohli
ms.openlocfilehash: 5eb5c351462279fe5c4f790e052f73201d211cfb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94961184"
---
# <a name="use-the-storsimple-device-manager-service-to-modify-your-storsimple-device-configuration"></a>StorSimple cihaz yapılandırmanızı değiştirmek için StorSimple Device Manager hizmetini kullanın

## <a name="overview"></a>Genel Bakış

**Ayarlar** dikey penceresindeki Azure Portal **cihaz ayarları** bölümü, StorSimple Device Manager hizmeti tarafından yönetilen bir StorSimple cihazında yeniden yapılandırabilmeniz için tüm cihaz parametrelerini içerir. Bu öğreticide, aşağıdaki cihaz düzeyindeki görevleri gerçekleştirmek için **Ayarlar** dikey penceresini nasıl kullanabileceğiniz açıklanmaktadır:

* Cihaz kolay adını değiştir
* Cihaz zaman ayarlarını değiştir
* İkincil DNS ata
* Ağ arabirimlerini değiştirme
* IP 'Leri değiştirme veya yeniden atama

## <a name="modify-device-friendly-name"></a>Cihaz kolay adını değiştir

Azure portal kullanarak cihaz adını değiştirebilir ve seçtiğiniz benzersiz bir kolay adı atayabilirsiniz. Cihazın kolay adını değiştirmek için cihazınızdaki **Genel ayarlar** dikey penceresini kullanın. Kolay ad, herhangi bir karakter içerebilir ve en fazla 64 karakter uzunluğunda olabilir.

> [!NOTE] 
> Cihaz kurulumu tamamlanmadan önce Azure portal yalnızca cihaz adını değiştirebilirsiniz. Minimum cihaz kurulumu tamamlandıktan sonra, cihaz adını değiştiremezsiniz.

![Genel ayarlarda cihaz adı](./media/storsimple-8000-modify-device-config/modify-general-settings3.png)

StorSimple Device Manager hizmetine bağlı bir StorSimple cihazına varsayılan ad atanır. Varsayılan ad genellikle cihazın seri numarasını yansıtır. Örneğin, 8600-SHX0991003G44HT gibi 15 karakter uzunluğunda varsayılan bir cihaz adı şunları gösterir:

* **8600**  – cihaz modelini gösterir.
* **SHX** : üretim sitesini belirtir.
* **0991003** -belirli bir ürünü gösterir.
* **G44HT**-son 5 basamak, benzersiz seri numaraları oluşturmak için artırılır. Bu sıralı bir küme olmayabilir.

## <a name="modify-device-description"></a>Cihaz açıklamasını değiştir

Cihaz açıklamasını değiştirmek için cihazınızdaki **Genel ayarlar** dikey penceresini kullanın.

![Genel ayarlarda cihaz açıklaması](./media/storsimple-8000-modify-device-config/modify-general-settings4.png)

Bir cihaz açıklaması genellikle cihazın sahibini ve fiziksel konumunu belirlemenize yardımcı olur. Açıklama alanı 256 karakterden daha az karakter içermelidir.

## <a name="modify-time-settings"></a>Zaman ayarlarını değiştir

Cihazınızın, bulut depolama hizmeti sağlayıcınızda kimlik doğrulaması için zaman eşitlemesini gerekir. Cihaz saati ayarlarını değiştirmek için cihazınızdaki **Genel ayarlar** dikey penceresini kullanın.

![Genel ayarlar 'da cihaz açıklaması 2](./media/storsimple-8000-modify-device-config/modify-general-settings2.png)

 Aşağı açılan listeden saat diliminizi seçin. En fazla iki ağ zaman Protokolü (NTP) sunucusu belirtebilirsiniz:

 - **BIRINCIL NTP sunucusu** -yapılandırma gereklidir ve cihazınızı yapılandırmak için StorSimple için Windows PowerShell kullandığınızda belirtilir. Varsayılan Windows Server **Time.Windows.com** 'i NTP sunucunuz olarak belirtebilirsiniz. Birincil NTP sunucusu yapılandırmasını Azure portal aracılığıyla görüntüleyebilirsiniz, ancak bunu değiştirmek için Windows PowerShell arabirimini kullanmanız gerekir. `Set-HcsNTPClientServerAddress`Cihazınızın BIRINCIL NTP sunucusunu değiştirmek için cmdlet 'ini kullanın. Daha fazla bilgi için, [set-HcsNTPClientServerAddress](/previous-versions/windows/powershell-scripting/dn688138(v=wps.630)) cmdlet sözdizimi ' ne gidin.

- **IKINCIL NTP sunucusu** -yapılandırma isteğe bağlıdır. İkincil bir NTP sunucusunu yapılandırmak için portalı kullanabilirsiniz.

NTP sunucusunu yapılandırırken ağınızın, NTP trafiğinin veri merkezinizden Internet 'e geçmesine izin verdiğinden emin olun. Genel bir NTP sunucusu belirtirken, ağ güvenlik duvarlarınızın ve diğer güvenlik cihazlarının, NTP trafiğinin dış ağa ve dış ağa gitmesini sağlayacak şekilde yapılandırıldığından emin olmanız gerekir. Çift yönlü NTP trafiğine izin verilmezse, bir iç NTP sunucusu kullanmanız gerekir (bir Windows etki alanı denetleyicisi bu işlevi sağlar). Cihazınız zamandan eşitlenemezse, bulut depolama sağlayıcınızla iletişim kuramamayabilir.

Genel NTP sunucularının listesini görmek için, [NTP sunucuları Web](https://support.ntp.org/bin/view/Servers/WebHome)sitesine gidin.

### <a name="what-happens-if-the-device-is-deployed-in-a-different-time-zone"></a>Cihaz farklı bir saat diliminde dağıtılırsa ne olur?

Cihaz farklı bir saat diliminde dağıtılmışsa, cihaz saat dilimi değişir. Tüm yedekleme ilkelerinin cihaz saat dilimini kullanması durumunda, yedekleme ilkeleri yeni saat dilimine göre otomatik olarak ayarlar. Kullanıcı müdahalesi gerekli değildir.

## <a name="modify-dns-settings"></a>DNS ayarlarını değiştir

Cihazınız bulut depolama hizmeti sağlayıcınızla iletişim kurmaya çalıştığında bir DNS sunucusu kullanılır. Yapılandırılmış DNS ayarlarını görüntülemek ve değiştirmek için cihazınızdaki **ağ ayarları** dikey penceresini kullanın. 

![Ağ ayarlarındaki DNS ayarları](./media/storsimple-8000-modify-device-config/modify-network-settings1.png)

Yüksek kullanılabilirlik için, ilk cihaz dağıtımı sırasında hem birincil hem de ikincil DNS sunucularını yapılandırmanız gerekir.

**BIRINCIL DNS sunucusu** -ilk kurulum SıRASıNDA birincil DNS sunucusunu belirtmek için StorSimple için Windows PowerShell kullanırsınız. Birincil DNS sunucusunu yalnızca Windows PowerShell arabirimi aracılığıyla yeniden yapılandırabilirsiniz. `Set-HcsDNSClientServerAddress`Cihazınızın BIRINCIL DNS sunucusunu değiştirmek için cmdlet 'ini kullanın. Daha fazla bilgi için [set-HcsDNSClientServerAddress](/previous-versions/windows/powershell-scripting/dn688138(v=wps.630)) cmdlet sözdizimi ' ne gidin.

**IKINCIL DNS sunucusu** -ikincil DNS sunucusunu değiştirmek için, `Set-HcsDNSClientServerAddress` Azure Portal StorSimple cihazınızın Windows PowerShell arabirimindeki cmdlet 'ini kullanın. 

Azure portal ikincil DNS sunucusunu değiştirmek için aşağıdaki adımları gerçekleştirin.

1. StorSimple Cihaz Yöneticisi hizmetinize gidin. Cihaz listesinden cihazınızı seçin ve tıklatın.

2. **Ayarlar** dikey penceresinde **cihaz ayarları > ağ ' a** gidin. Bu, **ağ ayarları** dikey penceresini açar. **DNS ayarları** Kutucuğu ' na tıklayın. İkincil DNS sunucusu IP adresini değiştirin.

    ![İkincil DNS sunucusu IP adresini değiştir](./media/storsimple-8000-modify-device-config/modify-secondary-dns1.png)

4. Komut çubuğundan **Kaydet** ' e tıklayın ve onay sorulduğunda, **Tamam**' a tıklayın.

    ![Değişiklikleri kaydetme ve onaylama](./media/storsimple-8000-modify-device-config/modify-secondary-dns-2.png)



## <a name="modify-network-interfaces"></a>Ağ arabirimlerini değiştirme

Cihazınız, dört/adet 1 GbE ve ikisi de 10 GbE olan altı adet cihaz ağ arabirimine sahiptir. Bu arabirimler VERI 0 – VERI 5 olarak etiketlenir. Veri 0, VERI 1, VERI 4 ve VERI 5, 1 GbE, VERI 2 ve VERI 3 ise 10 GbE ağ arabirimlerdir.

Kullanılacak arabirimlerin her birini yapılandırmak için **ağ ayarları** dikey penceresini kullanın.

![Ağ ayarları aracılığıyla ağ arabirimlerini yapılandırma](./media/storsimple-8000-modify-device-config/modify-network-settings3.png)

Yüksek kullanılabilirlik sağlamak için cihazınızda en az iki Iscsı arabirimi ve bulut özellikli iki arabirim olması önerilir. Kullanılmayan arabirimlerin devre dışı bırakılmalarını öneririz.

Her ağ arabirimi için aşağıdaki parametreler görüntülenir:

* **Hız** – Kullanıcı tarafından yapılandırılabilir bir parametre değil. Veri 0, VERI 1, VERI 4 ve VERI 5 her zaman 1 GbE, ancak DATA 2 ve DATA 3 10 GbE arabirimlerdir.
  
  > [!NOTE]
  > Hız ve çift yönlü her zaman otomatik olarak anlaşma yapılır. Jumbo çerçeveleri desteklenmez.
  
* **Arabirim durumu** – bir arabirim etkinleştirilebilir veya devre dışı bırakılabilir. Etkinleştirilirse, cihaz arabirimini kullanmaya çalışır. Yalnızca ağa bağlı olan ve kullanılan arabirimlerin etkinleştirilmesini öneririz. Kullanmadığınız tüm arabirimleri devre dışı bırakın.
* **Arabirim türü** – Bu parametre, iSCSI trafiğini bulut depolama trafiğinden ayırmanıza olanak tanır. Bu parametre aşağıdakilerden biri olabilir:
  
  * **Bulut etkin** – etkinleştirildiğinde, cihaz, bulut ile iletişim kurmak için bu arabirimi kullanır.
  * **iSCSI etkin** – etkinleştirildiğinde cihaz, iSCSI ana bilgisayarıyla iletişim kurmak için bu arabirimi kullanır.
    
    Iscsı trafiğini bulut depolama trafiğinden ayırmanızı öneririz. Ayrıca, konağınız aygıtınızla aynı alt ağ içinde ise, bir ağ geçidi atamanız gerekmez; Ancak, ana bilgisayarınız cihazından farklı bir alt ağdaysa, bir ağ geçidi atamanız gerekir.
* **IP adresi** : ağ arabirimlerinden herhangi birini yapılandırırken BIR sanal IP (VIP) yapılandırmanız gerekir. Bu, IPv4 veya IPv6 ya da her ikisi olabilir. Hem IPv4 hem de IPv6 adres aileleri cihaz ağ arabirimleri için desteklenir. IPv4 kullanırken, nokta ondalık gösteriminde 32 bitlik bir IP adresi (*xxx.xxx.xxx.xxx*) belirtin. IPv6 kullanırken, yalnızca 4 basamaklı bir önek sağlamanız yeterlidir ve bu önek temelinde cihaz ağ arabiriminiz için 128 bitlik bir adres otomatik olarak oluşturulur.
* **Alt ağ** : Bu, alt ağ maskesini belirtir ve Windows PowerShell arabirimi aracılığıyla yapılandırılır.
* **Ağ geçidi** – bu, aynı IP adresi alanı (alt ağ) içinde olmayan düğümlerle iletişim kurmayı denediğinde bu arabirim tarafından kullanılması gereken varsayılan ağ geçidindir. Varsayılan ağ geçidi, alt ağ maskesi tarafından belirlendiği şekilde, arabirim IP adresi ile aynı adres alanında (alt ağ) olmalıdır.
* **Sabıt IP adresi** : Bu alan yalnızca veri 0 arabirimini yapılandırırken kullanılabilir. Cihaz güncelleştirmeleri veya sorun giderme gibi işlemler için doğrudan cihaz denetleyicisine bağlanmanız gerekebilir. Sabit IP adresi, cihazınızdaki etkin ve pasif denetleyiciye erişmek için kullanılabilir.

> [!NOTE]
> * Doğru işlemi sağlamak için, her bir cihaz arabiriminin bağlandığı anahtardaki arabirim hızını ve çift yönlü olarak doğrulayın. Anahtar arabirimleri, Gigabit Ethernet (1000 Mbps) için anlaşma ya da yapılandırılmış olmalıdır ve tam çift yönlü olmalıdır. Daha yavaş hızlarda veya yarı çift yönlü olarak çalışan arabirimler, performans sorunlarına neden olur.
> * Kesintilerin ve kapalı kalma süresini en aza indirmek için, cihazınızın Iscsı ağ arabiriminin bağlanacağınız her anahtar bağlantı noktası üzerinde portfast 'yi etkinleştirmenizi öneririz. Bu, yük devretme durumunda ağ bağlantısının hızlı bir şekilde kurulabileceğini güvence altına alacak.

### <a name="configure-data-0"></a>VERILERI Yapılandır 0

VERI 0 varsayılan olarak bulutta etkindir. VERI 0 yapılandırılırken, her bir denetleyici için bir tane olmak üzere iki sabit IP adresi yapılandırmanız da gerekir. Bu sabit IP adresleri, cihaz denetleyicilerine doğrudan erişmek için kullanılabilir ve cihaz üzerine güncelleştirmeler yüklediğinizde, çöp toplamanın düzgün çalışması için veya denetleyicilere sorun giderme amacıyla eriştiğinizde yararlı olabilir.

VERI 0 ayarları dikey penceresi aracılığıyla sabit IP denetleyicilerini yeniden yapılandırabilirsiniz.

![Ağ arabirimini yapılandırma-VERILER 0](./media/storsimple-8000-modify-device-config/modify-network-settings2.png)

> [!NOTE]
> Denetleyiciye yönelik sabit IP adresleri, cihazdaki güncelleştirmelere hizmet vermek için kullanılır ve Space geri kazanma algoritmaları (çöp toplama) düzgün şekilde çalışır. Bu nedenle, sabit IP’lerin yönlendirilebilir ve İnternet’e bağlanabilir olması gerekir.

### <a name="configure-data-1---data-5"></a>VERI yapılandırma 1-VERI 5

DATA 1-DATA 5 Ağ arabirimleri için, aşağıdaki ekran görüntüsünde gösterildiği gibi tüm ağ ayarlarını yapılandırabilirsiniz:

![Ağ arabirimlerini yapılandırma VERI 1-VERI 5](./media/storsimple-8000-modify-device-config/modify-network-settings4.png)


## <a name="swap-or-reassign-ips"></a>IP 'Leri değiştirme veya yeniden atama

Şu anda denetleyicideki herhangi bir ağ arabirimine kullanımda olan bir VIP atanırsa (aynı cihaz veya ağdaki başka bir cihaz tarafından), denetleyicinin yük devri yapılır. Bir cihaz ağ arabirimi için VIP 'leri takas veya yeniden atarsanız, yinelenen bir IP durumu oluşturabileceğiniz için uygun bir yordamı izlemeniz gerekir.

Ağ arabirimlerinin herhangi birine ait VIP 'leri değiştirmek veya yeniden atamak için aşağıdaki adımları gerçekleştirin:

#### <a name="to-reassign-ips"></a>IP 'Leri yeniden atamak için

1. Her iki arabirimin IP adresini temizleyin.
2. IP adresleri temizlendikten sonra, yeni IP adreslerini ilgili arabirimlere atayın.

## <a name="next-steps"></a>Sonraki adımlar

* [StorSimple cihazınız IÇIN MPIO yapılandırma](storsimple-8000-configure-mpio-windows-server.md)hakkında bilgi edinin.
* StorSimple [cihazınızı yönetmek Için storsimple Device Manager hizmetini nasıl kullanacağınızı](storsimple-8000-manager-service-administration.md)öğrenin.