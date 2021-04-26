---
title: Sorun giderme için bir tarayıcı izlemesi yakalama
description: Azure portal sorunları gidermeye yardımcı olması için bir tarayıcı izağından ağ bilgilerini yakalayın.
ms.date: 03/25/2021
ms.topic: troubleshooting
ms.openlocfilehash: 63bc3e93187274db607b5f60216de461729801dc
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105563505"
---
# <a name="capture-a-browser-trace-for-troubleshooting"></a>Sorun giderme için bir tarayıcı izlemesi yakalama

Azure portal bir sorunla ilgili sorun yaşıyorsanız ve Microsoft destek 'e başvurmanız gerekiyorsa, önce bir tarayıcı izlemesi ve bazı ek bilgiler yakalamanızı öneririz. Topladığınız bilgiler, sorun gerçekleştiği sırada Portal hakkında önemli ayrıntıları sağlayabilir. Kullandığınız tarayıcıda geliştirici araçları için bu makaledeki adımları izleyin: Google Chrome veya Microsoft Edge (Kmıum), Microsoft Edge (EdgeHTML), Apple Safari veya Firefox.

## <a name="google-chrome-and-microsoft-edge-chromium"></a>Google Chrome ve Microsoft Edge (Kmıum)

Google Chrome ve Microsoft Edge (Kmıum), her ikisi de [kmıum açık kaynak projesine](https://www.chromium.org/Home)dayalıdır. Aşağıdaki adımlarda, iki tarayıcıda çok benzeyen Geliştirici araçlarının nasıl kullanılacağı gösterilmektedir. Daha fazla bilgi için bkz. [Chrome DevTools](https://developers.google.com/web/tools/chrome-devtools) ve [Microsoft Edge (kmıum) geliştirici araçları](/microsoft-edge/devtools-guide-chromium).

1. [Azure portalında](https://portal.azure.com) oturum açın. İzlemeye _başlamadan önce_ , izlemenin, oturum açma ile ilgili hassas bilgiler içermediği şekilde oturum açması önemlidir. 

1. [Adımlar Kaydedicisi](https://support.microsoft.com/help/22878/windows-10-record-steps)'ni kullanarak portalda aldığınız adımları kaydetmeye başlayın.

1. Portalda, sorunun oluştuğu yerin hemen öncesindeki adıma gidin.

1. F12 tuşuna basın veya ![ tarayıcı ayarları simgesini gösteren ekran görüntüsü ' nü seçin.](media/capture-browser-trace/chromium-icon-settings.png) > **Diğer araçlar**  >  **Geliştirici Araçları**.

1. Varsayılan olarak tarayıcı, izleme bilgilerini yalnızca şu anda yüklü olan sayfa için tutar. Yeniden üretme, birden fazla sayfaya gitse bile tarayıcının tüm izleme bilgilerini tutabilmesi için aşağıdaki seçenekleri ayarlayın:

    1. **Ağ** sekmesini seçin ve **günlüğü koru**' yı seçin.

          ![Ağ sekmesinde günlüğü koru seçeneğini vurgulayan ekran görüntüsü.](media/capture-browser-trace/chromium-network-preserve-log.png)

    1. **Konsol** sekmesini seçin, **Konsol ayarları**' nı seçin ve **günlüğü koru**' yı seçin. Ayarlar bölmesini kapatmak için **Konsol ayarları** ' nı yeniden seçin.

          ![Konsol sekmesinde günlüğü koru seçeneğini vurgulayan ekran görüntüsü.](media/capture-browser-trace/chromium-console-preserve-log.png)

1. **Ağ** sekmesini seçin, sonra **ağ günlüğü kaydını durdur** ve **Temizle**' yi seçin.

    !["Ağ günlüğü kaydını Durdur" ve "Temizle" ekran görüntüsü](media/capture-browser-trace/chromium-stop-clear-session.png)

1. **Ağ günlüğünü kaydet**' i seçin ve ardından portalda sorunu yeniden oluşturun.

    ![Ağ günlüğünün nasıl kaydedilecek gösteren ekran görüntüsü.](media/capture-browser-trace/chromium-start-session.png)

    Aşağıdaki görüntüye benzer oturum çıkışını görürsünüz.

    ![Oturum çıkışını gösteren ekran görüntüsü.](media/capture-browser-trace/chromium-browser-trace-results.png)

1. Beklenmeyen Portal davranışını yeniden oluşturduktan sonra, **ağ günlüğü kaydını durdur**' u seçin, ardından **har 'u dışarı aktar** ' ı seçin ve dosyayı kaydedin.

    ![Ağ sekmesinde HAR 'un nasıl dışarı aktarılacağını gösteren ekran görüntüsü.](media/capture-browser-trace/chromium-network-export-har.png)

1. Adımları kaydediciyi durdurun ve kaydı kaydedin.

1. Tarayıcı geliştirici araçları bölmesine geri dönerek **konsol** sekmesini seçin. İletilerden birine sağ tıklayın, ardından **Farklı Kaydet...** öğesini seçin ve konsol çıkışını bir metin dosyasına kaydedin.

    ![Konsol sekmesini ve farklı kaydet 'i vurgulayan ekran görüntüsü... Menü](media/capture-browser-trace/chromium-console-select.png)

1. HAR dosyasını, konsol çıkışını ve ekran kaydını. zip gibi sıkıştırılmış bir biçimde paketleyin ve Microsoft desteği ile paylaşabilirsiniz.

## <a name="microsoft-edge-edgehtml"></a>Microsoft Edge (EdgeHTML)

Aşağıdaki adımlarda, Microsoft Edge 'de (EdgeHTML) Geliştirici araçlarının nasıl kullanılacağı gösterilmektedir. Daha fazla bilgi için bkz. [Microsoft Edge (EdgeHTML) geliştirici araçları](/microsoft-edge/devtools-guide).

1. [Azure portalında](https://portal.azure.com) oturum açın. İzlemeye _başlamadan önce_ , izlemenin, oturum açma ile ilgili hassas bilgiler içermediği şekilde oturum açması önemlidir. 

1. [Adımlar Kaydedicisi](https://support.microsoft.com/help/22878/windows-10-record-steps)'ni kullanarak portalda aldığınız adımları kaydetmeye başlayın.

1. Portalda, sorunun oluştuğu yerin hemen öncesindeki adıma gidin.

1. F12 tuşuna basın veya ![ tarayıcı ayarları simgesinin ekran görüntüsünü seçin.](media/capture-browser-trace/edge-icon-settings.png) > **Diğer araçlar**  >  **Geliştirici Araçları**.

1. Varsayılan olarak tarayıcı, izleme bilgilerini yalnızca şu anda yüklü olan sayfa için tutar. Yeniden üretme, birden fazla sayfaya gitse bile tarayıcının tüm izleme bilgilerini tutabilmesi için aşağıdaki seçenekleri ayarlayın:

    1. **Ağ** sekmesini seçin ve ardından **Gezinti sırasında girdileri temizle** seçeneğini temizleyin.

          !["Gezinte girdilerin Işaretini kaldır" ekran görüntüsü](media/capture-browser-trace/edge-network-clear-entries.png)

    1. **Konsol** sekmesini seçin ve **günlüğü koru**' yı seçin.

          !["Günlüğü koru" ekran görüntüsü](media/capture-browser-trace/edge-console-preserve-log.png)

1. **Ağ** sekmesini seçin, sonra **profil oluşturma oturumunu durdur** ' u ve **oturumu temizle**' yi seçin.

    !["Profil oluşturma oturumunu Durdur" ve "oturumu temizle" ekran görüntüsü](media/capture-browser-trace/edge-stop-clear-session.png)

1. **Profil oluşturma oturumunu Başlat**' ı seçin ve ardından portalda sorunu yeniden oluşturun.

    !["Profil oluşturma oturumu Başlat" ekran görüntüsü](media/capture-browser-trace/edge-start-session.png)

    Aşağıdaki görüntüye benzer oturum çıkışını görürsünüz.

    ![Profil oluşturma oturumunun çıkışını gösteren ekran görüntüsü.](media/capture-browser-trace/edge-browser-trace-results.png)

1. Beklenmeyen Portal davranışını yeniden oluşturduktan sonra, **profil oluşturma oturumunu durdur**' u seçin, ardından **har olarak dışarı aktar** ' ı seçin ve dosyayı kaydedin.

    !["HAR olarak dışarı aktar" ekran görüntüsü](media/capture-browser-trace/edge-network-export-har.png)

1. Adımları kaydediciyi durdurun ve kaydı kaydedin.

1. Tarayıcı geliştirici araçları bölmesine geri dönün, **konsol** sekmesini seçin ve pencereyi genişletin. İmlecinizi konsol çıkışının başlangıcına yerleştirin ve ardından çıktının tüm içeriğini sürükleyin ve seçin. Sağ tıklayın ve **Kopyala**' yı seçin ve konsol çıkışını bir metin dosyasına kaydedin.

    ![Kopyala menü seçeneğini vurgulayan ekran görüntüsü.](media/capture-browser-trace/edge-console-select.png)

1. HAR dosyasını, konsol çıkışını ve ekran kaydını. zip gibi sıkıştırılmış bir biçimde paketleyin ve Microsoft desteği ile paylaşabilirsiniz.

## <a name="apple-safari"></a>Apple Safari

Aşağıdaki adımlarda, Apple Safari 'de Geliştirici araçlarının nasıl kullanılacağı gösterilmektedir. Daha fazla bilgi için bkz. [Safari geliştirici araçları genel bakış](https://support.apple.com/guide/safari-developer/safari-developer-tools-overview-dev073038698/11.0/mac).

1. Apple Safari 'de geliştirici araçlarını etkinleştirme:

    1. **Safari**' yi seçin ve **Tercihler**' i seçin.

        ![Safari tercihleri ekran görüntüsü](media/capture-browser-trace/safari-preferences.png)

    1. **Gelişmiş** sekmesini seçin ve ardından **menü çubuğunda geliştirmesi menüsünü göster**' i seçin.

        ![Safari gelişmiş tercihlerinin ekran görüntüsü](media/capture-browser-trace/safari-show-develop-menu.png)

1. [Azure portalında](https://portal.azure.com) oturum açın. İzlemeye _başlamadan önce_ , izlemenin, oturum açma ile ilgili hassas bilgiler içermediği şekilde oturum açması önemlidir. 

1. Portalda aldığınız adımları kaydetmeye başlayın. Daha fazla bilgi için bkz. [Mac 'inizde ekran kaydetme](https://support.apple.com/HT208721).

1. Portalda, sorunun oluştuğu yerin hemen öncesindeki adıma gidin.

1. **Geliştir**' i seçin, sonra **Web denetçisini göster**' i seçin.

    !["Web denetçisini göster" ekran görüntüsü](media/capture-browser-trace/safari-show-web-inspector.png)

1. Varsayılan olarak tarayıcı, izleme bilgilerini yalnızca şu anda yüklü olan sayfa için tutar. Yeniden üretme, birden fazla sayfaya gitse bile tarayıcının tüm izleme bilgilerini tutabilmesi için aşağıdaki seçenekleri ayarlayın:

    1. **Ağ** sekmesini seçin ve **günlüğü koru**' yı seçin.

          ![Günlüğü koru seçeneğini gösteren ekran görüntüsü.](media/capture-browser-trace/safari-network-preserve-log.png)

    1. **Konsol** sekmesini seçin ve **günlüğü koru**' yı seçin.

          ![Konsol sekmesinde günlüğü koru sekmesini gösteren ekran görüntüsü.](media/capture-browser-trace/safari-console-preserve-log.png)

1. **Ağ** sekmesini seçin ve ardından **ağ öğelerini temizle**' yi seçin.

    !["Ağ öğelerini temizle" ekran görüntüsü](media/capture-browser-trace/safari-clear-session.png)

1. Portalda sorunu yeniden üretin. Aşağıdaki görüntüye benzer oturum çıkışını görürsünüz.

    ![Sorunu yeniden oluşturduktan sonra çıktıyı gösteren ekran görüntüsü.](media/capture-browser-trace/safari-browser-trace-results.png)

1. Beklenmeyen Portal davranışını yeniden oluşturduktan sonra, **dışarı aktar** ' ı seçin ve dosyayı kaydedin.

    !["Dışarı aktar" ekran görüntüsü](media/capture-browser-trace/safari-network-export-har.png)

1. Ekran kaydedicisini durdurun ve kaydı kaydedin.

1. Tarayıcı geliştirici araçları bölmesine geri dönün, **konsol** sekmesini seçin ve pencereyi genişletin. İmlecinizi konsol çıkışının başlangıcına yerleştirin ve ardından çıktının tüm içeriğini sürükleyin ve seçin. Çıktıyı kopyalamak ve bir metin dosyasına kaydetmek için Command-C komutunu kullanın.

    ![Çıktıyı görüntüleyip kopyalayabilmeniz için önemli ekran görüntüsü.](media/capture-browser-trace/safari-console-select.png)

1. HAR dosyasını, konsol çıkışını ve ekran kaydını. zip gibi sıkıştırılmış bir biçimde paketleyin ve Microsoft desteği ile paylaşabilirsiniz.

## <a name="firefox"></a>Firefox

Aşağıdaki adımlarda, Firefox 'ta Geliştirici araçlarının nasıl kullanılacağı gösterilmektedir. Daha fazla bilgi için bkz. [Firefox geliştirici araçları](https://developer.mozilla.org/docs/Tools).

1. [Azure portalında](https://portal.azure.com) oturum açın. İzlemeye _başlamadan önce_ , izlemenin, oturum açma ile ilgili hassas bilgiler içermediği şekilde oturum açması önemlidir. 

1. Portalda aldığınız adımları kaydetmeye başlayın. Windows 'da [Adım Kaydedicisi](https://support.microsoft.com/help/22878/windows-10-record-steps) 'ni kullanın veya [Ekran Mac 'Inizde nasıl kaydedilecek hakkında bilgi için](https://support.apple.com/HT208721)bkz..

1. Portalda, sorunun oluştuğu yerin hemen öncesindeki adıma gidin.

1. F12 tuşuna basın veya ![ tarayıcı ayarları simgesinin ekran görüntüsünü seçin ](media/capture-browser-trace/firefox-icon-settings.png)  >  **Web geliştirici**  >  **geçiş araçları**' nı seçin.

1. Varsayılan olarak tarayıcı, izleme bilgilerini yalnızca şu anda yüklü olan sayfa için tutar. Yeniden üretme, birden fazla sayfaya gitse bile tarayıcının tüm izleme bilgilerini tutabilmesi için aşağıdaki seçenekleri ayarlayın:

    1. **Ağ** sekmesini seçin ve ardından **günlükleri kalıcı** yap ' ı seçin.

          ![Kalıcı Günlükler seçeneğini vurgulayan ekran görüntüsü.](media/capture-browser-trace/firefox-network-persist-logs.png)

    1. **Konsol** sekmesini seçin, **Konsol ayarları**' nı seçin ve ardından **günlükleri kalıcı** yap ' ı seçin.

          !["Kalıcı günlüklerin" ekran görüntüsü](media/capture-browser-trace/firefox-console-persist-logs.png)

1. **Ağ** sekmesini seçin ve ardından **Temizle**' yi seçin.

    !["Clear" ekran görüntüsü](media/capture-browser-trace/firefox-clear-session.png)

1. Portalda sorunu yeniden üretin. Aşağıdaki görüntüye benzer oturum çıkışını görürsünüz.

    ![Tarayıcı izleme sonuçlarının ekran görüntüsü](media/capture-browser-trace/firefox-browser-trace-results.png)

1. Beklenmeyen Portal davranışını yeniden oluşturduktan sonra **Tümünü har olarak kaydet**' i seçin.

    !["Dışarı aktarma HAR" ekran görüntüsü](media/capture-browser-trace/firefox-network-export-har.png)

1. Windows 'da veya Mac 'teki ekran kaydındaki adımları kaydediciden durdurun ve kaydı kaydedin.

1. Tarayıcı geliştirici araçları bölmesine geri dönerek **konsol** sekmesini seçin. İletilerden birine sağ tıklayın, ardından **görünür Iletileri dışarı aktar**' ı seçin ve konsol çıkışını bir metin dosyasına kaydedin.

    ![Konsol çıktısının ekran görüntüsü](media/capture-browser-trace/firefox-console-select.png)

1. HAR dosyasını, konsol çıkışını ve ekran kaydını. zip gibi sıkıştırılmış bir biçimde paketleyin ve Microsoft desteği ile paylaşabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

[Azure portal’a genel bakış](azure-portal-overview.md)