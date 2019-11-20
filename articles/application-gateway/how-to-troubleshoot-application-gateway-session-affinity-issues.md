---
title: Oturum benzeşimi sorunlarını giderme
titleSuffix: Azure Application Gateway
description: Bu makale, Azure Application Gateway oturum benzeşimi sorunlarını giderme hakkında bilgi sağlar.
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: absha
ms.openlocfilehash: 9f14521c15c3497bed4ffbeba44cb5d78ee4df7b
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74047978"
---
# <a name="troubleshoot-azure-application-gateway-session-affinity-issues"></a>Azure Application Gateway oturum benzeşimi sorunlarını giderme

Azure Application Gateway ile oturum benzeşimi sorunlarını tanılamayı ve çözümlemeyi öğrenin.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Genel Bakış

Tanımlama bilgilerine dayalı oturum benzeşimi özelliği, bir kullanıcı oturumunu aynı sunucuda tutmak istediğinizde kullanışlıdır. Ağ geçidi ile yönetilen tanımlama bilgilerini kullanan Application Gateway, sonraki trafiği işleme amacıyla bir kullanıcı oturumundan aynı sunucuya yönlendirebilir. Bu, oturum durumunun bir kullanıcı oturumuna ait sunucuya yerel olarak kaydedildiği durumlarda önemlidir.

## <a name="possible-problem-causes"></a>Olası sorun nedenleri

Tanımlama bilgisi tabanlı oturum benzeşiminin korunmasında sorun aşağıdaki başlıca nedenlerden dolayı oluşabilir:

- "Tanımlama bilgisi tabanlı benzeşim" ayarı etkin değil
- Uygulamanız tanımlama bilgisi tabanlı benzeşimi işleyemez
- Uygulama, tanımlama bilgisi tabanlı benzeşim kullanıyor, ancak yine de arka uç sunucuları arasında sıçramaların isteği

### <a name="check-whether-the-cookie-based-affinity-setting-is-enabled"></a>"Tanımlama bilgisi tabanlı benzeşim" ayarının etkinleştirilip etkinleştirilmediğini denetleyin

Bazen "tanımlama bilgisi tabanlı benzeşim" ayarını etkinleştirmeyi unuttuğunuzda oturum benzeşimi sorunları ortaya çıkabilir. Azure portal HTTP ayarları sekmesinde "tanımlama bilgisi tabanlı benzeşim" ayarını etkinleştirmiş olup olmadığınızı öğrenmek için yönergeleri izleyin:

1. [Azure Portal](https://portal.azure.com/) oturum açın.

2. **Sol gezinti** bölmesinde, **tüm kaynaklar**' a tıklayın. Tüm kaynaklar dikey penceresinde uygulama ağ geçidi adına tıklayın. Seçtiğiniz abonelikte zaten çeşitli kaynaklar varsa, uygulama ağ geçidi adını **ada göre filtrele...** olarak girebilirsiniz. girebilirsiniz.

3. **Ayarlar**altında **http ayarları** sekmesini seçin.

   ![sorun giderme-oturum benzeşimi-sorunlar-1](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-1.png)

4. Tanımlama bilgisi tabanlı benzeşim için **etkin** ' i seçmiş olup olmadığınızı kontrol etmek için sağ taraftaki **Appgatewaybackendhttpsettings** ' e tıklayın.

   ![sorun giderme-oturum-benzeşim-sorunlar-2](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-2.jpg)



Ayrıca, aşağıdaki yöntemlerden birini kullanarak "Using**ıebasedadinity**" değerinin "**BackendHttpSettingsCollection**" altında *etkin*olarak ayarlandığını denetleyebilirsiniz:

- PowerShell 'de [Get-AzApplicationGatewayBackendHttpSetting](https://docs.microsoft.com/powershell/module/az.network/get-azapplicationgatewaybackendhttpsetting) komutunu çalıştırın
- Azure Resource Manager şablonunu kullanarak JSON dosyasına bakın

```
"cookieBasedAffinity": "Enabled", 
```

### <a name="the-application-cannot-handle-cookie-based-affinity"></a>Uygulama, tanımlama bilgisi tabanlı benzeşimi işleyemez

#### <a name="cause"></a>Nedeni

Uygulama ağ geçidi, bir tanımlama bilgisi kullanarak yalnızca oturum tabanlı benzeşim gerçekleştirebilir.

#### <a name="workaround"></a>Geçici çözüm

Uygulama, tanımlama bilgisi tabanlı benzeşimi işleyemez, dış veya iç Azure yük dengeleyici ya da başka bir üçüncü taraf çözümü kullanmanız gerekir.

### <a name="application-is-using-cookie-based-affinity-but-requests-still-bouncing-between-back-end-servers"></a>Uygulama, tanımlama bilgisi tabanlı benzeşim kullanıyor, ancak yine de arka uç sunucuları arasında sıçramaların isteği

#### <a name="symptom"></a>Belirti

Internet Explorer 'da bir kısa ad URL 'SI kullanarak Application Gateway eriştiğinizde, tanımlama bilgisi tabanlı benzeşim ayarını etkinleştirdiniz, örneğin: [http://website](http://website/) , istek hala arka uç sunucuları arasında sıçraılır.

Bu sorunu belirlemek için yönergeleri izleyin:

1. Application Gateway arkasındaki uygulamaya bağlanan "Istemci" üzerinde bir Web hata ayıklayıcısı izlemesi gerçekleştirin (Bu örnekte Fiddler kullanıyoruz).
    **İpucu** Fiddler 'in nasıl kullanılacağını bilmiyorsanız, alt kısımdaki "**ağ trafiğini toplamak ve Web hata ayıklayıcısını kullanarak çözümlemek**istiyorum" seçeneğini işaretleyin.

2. İstemci tarafından sunulan tanımlama bilgilerinin ARRAffinity ayrıntılarına sahip olup olmadığını öğrenmek için oturum günlüklerini denetleyin ve çözümleyin. Tanımlama bilgisi kümesi içinde "**ARRAffinity =** *ARRAffinityValue*" gibi ARRAffinity ayrıntılarını bulamazsanız, istemcinin Application Gateway tarafından belirtilen ARRA tanımlama bilgisine yanıt verme anlamına gelir.
    Örneğin:

    ![sorun giderme-oturum-benzeşim-sorunlar-3](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-3.png)

    ![sorun giderme-oturum-benzeşim-sorunlar-4](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-4.png)

Uygulama, yanıt alınana kadar her istekte tanımlama bilgisini ayarlamaya çalışır.

#### <a name="cause"></a>Nedeni

Bu sorun, Internet Explorer ve diğer tarayıcıların tanımlama bilgisini kısa ad URL 'SI ile depolayabileceği veya kullanamadığı için oluşur.

#### <a name="resolution"></a>Çözüm

Bu sorunu gidermek için Application Gateway'e FQDN kullanarak erişmeniz gerekir. Örneğin, [http://website.com](https://website.com/) veya [http://appgw.website.com](http://appgw.website.com/) kullanın.

## <a name="additional-logs-to-troubleshoot"></a>Sorun gidermek için ek Günlükler

Ek Günlükler toplayabilir ve ilgili tanımlama bilgisi tabanlı oturum benzeşimi sorunlarını gidermek için bunları çözümleyebilirsiniz

### <a name="analyze-application-gateway-logs"></a>Application Gateway günlüklerini çözümle

Application Gateway günlüklerini toplamak için yönergeleri izleyin:

Azure portaldan günlüğe kaydetmeyi etkinleştirme

1. [Azure Portal](https://portal.azure.com/), kaynağınız bulun ve ardından **tanılama günlükleri**' ne tıklayın.

   Application Gateway için üç günlük mevcuttur: erişim günlüğü, performans günlüğü, güvenlik duvarı günlüğü

2. Veri toplamaya başlamak için **tanılamayı aç**' a tıklayın.

   ![sorun giderme-oturum benzeşimi-sorunlar-5](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-5.png)

3. Tanılama **ayarları** dikey penceresi tanılama günlükleri için ayarları sağlar. Bu örnekte, günlükleri Log Analytics depolar. Çalışma alanınızı ayarlamak için **Log Analytics** altında **Yapılandır** ' a tıklayın. Tanılama günlüklerini kaydetmek için Event Hubs'ı veya depolama hesabını da kullanabilirsiniz.

   ![sorun giderme-oturum-benzeşim-sorunlar-6](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-6.png)

4. Ayarları onaylayın ve **Kaydet**' e tıklayın.

   ![sorun giderme-oturum benzeşimi-sorunlar-7](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-7.png)

#### <a name="view-and-analyze-the-application-gateway-access-logs"></a>Application Gateway erişim günlüklerini görüntüleyin ve çözümleyin

1. Application Gateway kaynak görünümü altındaki Azure portal **izleme** bölümünde **tanılama günlükleri** ' ni seçin.

   ![sorun giderme-oturum-benzeşim-sorunlar-8](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-8.png)

2. Sağ tarafta, **günlük kategorileri** altındaki aşağı açılan listeden "**Applicationgatewayaccesslog**" öğesini seçin.  

   ![sorun giderme-oturum benzeşimi-sorunlar-9](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-9.png)

3. Application Gateway erişim günlüğü listesinde, analiz etmek ve dışarı aktarmak istediğiniz günlüğe tıklayın ve sonra JSON dosyasını dışarı aktarın.

4. Adım 3 ' te dışarıya verdiğiniz JSON dosyasını CSV dosyasına dönüştürüp Excel, Power BI veya başka bir veri görselleştirme aracında görüntüleyin.

5. Aşağıdaki verileri denetleyin:

- **Clienentip**: bağlantı istemcisinden ALıNAN istemci IP adresidir.
- **ClientPort** -bu, istek için bağlanan istemciden gelen kaynak bağlantı noktasıdır.
- **Requestquery** – bu, isteğin alındığı hedef sunucuyu belirtir.
- **Sunucu-yönlendirildi**: isteğin alındığı arka uç havuzu örneği.
- **X-AzureApplicationGateway-log-ID**: istek için kullanılan bağıntı kimliği. Arka uç sunucularındaki trafik sorunlarını gidermek için kullanılabilir. Örneğin: X-AzureApplicationGateway-CACHE-HIT = 0 & SERVER-YÖNLENDIRILDI = 10.0.2.4.

  - **Sunucu-durumu**: arka uçtan alınan Application Gateway http yanıt kodu.

  ![sorun giderme-oturum benzeşimi-sorunlar-11](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-11.png)

Aynı Clienentip ve Istemci bağlantı noktasından iki öğe geliyorsa ve bunlar aynı arka uç sunucusuna gönderiliyorsa, Application Gateway doğru şekilde yapılandırılır.

Aynı Clienentip ve Istemci bağlantı noktasından iki öğe geliyorsa ve bunlar farklı arka uç sunucularına gönderiliyorsa, isteğin arka uç sunucuları arasında sıçramakta olduğu anlamına gelir. "**uygulama, tanımlama bilgisi tabanlı benzeşim kullanıyor, ancak yine de arka uç sunucuları arasında sıçramaları**, sorun giderme için en altta yer alan" ' ı seçin.

### <a name="use-web-debugger-to-capture-and-analyze-the-http-or-https-traffics"></a>HTTP veya HTTPS traffics yakalamak ve çözümlemek için Web hata ayıklayıcısını kullanma

Fiddler gibi Web hata ayıklama araçları, Internet ve test bilgisayarları arasındaki ağ trafiğini yakalayarak Web uygulamalarında hata ayıklamanıza yardımcı olabilir. Bu araçlar, tarayıcı tarafından alındığında/gönderirken gelen ve giden verileri incelemenizi sağlar. Fiddler, bu örnekte, özellikle kimlik doğrulama türü için Web uygulamalarıyla istemci tarafı sorunlarını gidermenize yardımcı olabilecek HTTP yeniden yürütme seçeneğine sahiptir.

Seçtiğiniz Web hata ayıklayıcıyı kullanın. Bu örnekte, Fiddler 'ı kullanarak http veya https traffics yakalayın ve analiz edeceğiz, yönergeleri izleyin:

1. <https://www.telerik.com/download/fiddler>konumundaki Fiddler aracını indirin.

    > [!NOTE]
    > Yakalama bilgisayarında .NET 4 yüklüyse Fiddler4 öğesini seçin. Aksi takdirde Fiddler2 öğesini seçin.

2. Kurulum yürütülebilirini sağ tıklatın ve yüklemek için yönetici olarak çalıştırın.

    ![sorun giderme-oturum benzeşimi-sorunlar-12](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-12.png)

3. Fiddler 'i açtığınızda bu, trafiği otomatik olarak yakalamaya başlar (sol alt köşedeki yakalamaya dikkat edin). Trafik yakalamayı başlatmak veya durdurmak için F12 tuşuna basın.

    ![sorun giderme-oturum benzeşimi-sorunlar-13](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-13.png)

4. Büyük olasılıkla, şifresi çözülmüş HTTPS trafiğiyle ilgilenirsiniz ve **araçlar** > **Fiddler seçeneklerini**belirleyerek https şifre ÇÖZMEYI etkinleştirebilir ve " **https trafiğinin şifresini çöz**" kutusunu işaretleyin.

    ![sorun giderme-oturum benzeşimi-sorunlar-14](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-14.png)

5. **X** (Icon >) ' i tıklayarak ve ardından **Tümünü Kaldır** ekran görüntüsü ' nü seçerek sorunu tekrar denemeden önce önceki ilgisiz oturumları kaldırabilirsiniz: 

    ![sorun giderme-oturum benzeşimi-sorunlar-15](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-15.png)

6. Sorunu yeniden oluşturduktan sonra **dosya** > seçerek dosyayı gözden geçirme için kaydedin > **tüm oturumları Kaydet..** . 

    ![sorun giderme-oturum benzeşimi-sorunlar-16](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-16.png)

7. Sorunun ne olduğunu belirlemek için oturum günlüklerini denetleyin ve çözümleyin.

    Örnekler için:

- **Örnek A:** İsteğin istemciden gönderildiği bir oturum günlüğü bulur ve Application Gateway genel IP adresine gider, ayrıntıları görüntülemek için bu günlüğe tıklayın.  Sağ tarafta, alttaki kutuda yer alan veriler Application Gateway istemciye geri dönşeydir. "RAW" sekmesini seçin ve istemcinin bir "**set-Cookie: ARRAffinity =** *ARRAffinityValue*" alıp almadıklarını belirleyin. Tanımlama bilgisi yoksa, oturum benzeşimi ayarlı değildir veya Application Gateway tanımlama bilgisini istemciye geri uygulamamakta değildir.

   > [!NOTE]
   > Bu ARRAffinity değeri tanımlama bilgisi-id ' dır, Application Gateway istemcinin belirli bir arka uç sunucusuna gönderilmesi için ayarlanır.

   ![sorun giderme-oturum-benzeşim-sorunlar-17](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-17.png)

- **Örnek B:** Sonraki oturum günlüğü, daha önce gelen Application Gateway, istemci, ARRAAFFINITY olarak ayarlanmış olan geri yanıt verir. ARRAffinity tanımlama bilgisi kimliği eşleşiyorsa, paketin daha önce kullanılan arka uç sunucusuna gönderilmesi gerekir. İstemcinin ARRAffinity tanımlama bilgisinin değiştirilip değiştirilmediğini görmek için, sonraki birkaç http iletişim satırını kontrol edin.

   ![sorun giderme-oturum benzeşimi-sorunlar-18](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-18.png)

> [!NOTE]
> Aynı iletişim oturumu için tanımlama bilgisi değişmemelidir. Sağ taraftaki en üstteki kutuyu işaretleyin, istemcinin tanımlama bilgisini kullanıp kullanmadığını ve Application Gateway geri gönderip göndermediğini görmek için "tanımlama bilgileri" sekmesini seçin. Aksi halde istemci tarayıcısı, konuşmalar için tanımlama bilgisi kullanmıyor ve kullanmaz. Bazen, istemci olabilir.

 

## <a name="next-steps"></a>Sonraki adımlar

Yukarıdaki adımlar sorunu çözmezse, bir [destek bileti](https://azure.microsoft.com/support/options/)açın.
