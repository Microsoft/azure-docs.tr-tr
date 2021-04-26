---
title: Synapse Studio sorunlarını giderme
description: Azure SYNAPSE Studio sorunlarını giderme
author: julieMSFT
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: 7d91001024ad547e52fe48ee30749fee9a4fb4a1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98116186"
---
# <a name="azure-synapse-studio-troubleshooting"></a>Azure SYNAPSE Studio sorunlarını giderme

Bu sorun giderme kılavuzu, ağ bağlantısı sorunları üzerinde bir destek bileti açılırken hangi bilgilerin sağlanmasında yönergeler sağlar. Uygun bilgilerle sorunu daha çabuk çözebiliriz.

## <a name="serverless-sql-pool-service-connectivity-issue"></a>Sunucusuz SQL havuzu hizmet bağlantısı sorunu

### <a name="symptom-1"></a>Belirti 1

"Connect to" açılan menüsünde "sunucusuz SQL havuzu" seçeneği gri renkte.

![symptom1](media/troubleshooting-synapse-studio/symptom1v2.png)

### <a name="symptom-2"></a>Belirti 2

Sorguyu "sunucusuz SQL havuzu" ile çalıştırmak, "sunucu bağlantısı kurulamadı" hata iletisini verir.

![Belirti 2](media/troubleshooting-synapse-studio/symptom2.png)
 

## <a name="troubleshooting-steps"></a>Sorun giderme adımları

> [!NOTE] 
>    Aşağıdaki sorun giderme adımları, Kmıum Edge ve Chrome içindir. Aynı sorun giderme adımlarıyla diğer tarayıcıları (FireFox gibi) kullanabilirsiniz, ancak "Geliştirici Aracı" penceresi bu TSG 'deki ekran görüntüleriyle farklı bir düzene sahip olabilir. Mümkünse, belirli bir durumda yanlış bilgi gösterebileceğinden, sorun giderme için klasik kenar kullanmayın.

"Tanılama bilgileri" panelini açın, "tanılamayı Indir" düğmesini seçin. Hata raporlama için indirilen bilgileri saklayın. Bunun yerine, "oturum KIMLIĞI" ni kopyalayabilir ve destek bileti açılırken ekleyebilirsiniz.

![Tanılama-bilgi](media/troubleshooting-synapse-studio/diagnostic-info-download.png)

Sorun gidermeye başlamak için, Azure SYNAPSE Studio 'da gerçekleştirdiğiniz işlemi yeniden deneyin.

- Belirti 1 için "SQL komut dosyası" sekmesinde "veritabanı kullan" açılan menüsünün sağ tarafındaki "Yenile" düğmesini seçin ve "sunucusuz SQL havuzu" nı görüp görmeyeceğinizi denetleyin.
- Belirti 2 ' de, başarıyla yürütülüp yürütülmediğini görmek için sorguyu yeniden çalıştırmayı deneyin.

Sorun devam ederse, "Geliştirici Araçları" (DevTools) öğesini açmak için tarayıcınızda F12 tuşuna basın.

"Geliştirici Araçları" penceresinde "ağ" paneline geçin. Gerekirse "ağ" panelinde araç çubuğunda "Temizle" düğmesini seçin.
"Ağ" panelinde "önbelleği devre dışı bırak" onay işaretli olduğundan emin olun.

Azure SYNAPSE Studio 'da gerçekleştirdiğiniz işlemi yeniden deneyin. "Geliştirici Araçları" içindeki "ağ" listesinde gösterilen yeni öğeler görebilirsiniz. Destek bileti sağlamak için geçerli sisteminizin saatini aklınızda yapın.

![Ağ-Panel 1](media/troubleshooting-synapse-studio/network-panel.png)

URL sütunu aşağıdaki Düzenle eşleşen öğeyi bul:

`https://[*A*]-ondemand.database.windows.net:1443/databases/[*B*]/query?api-version=2018-08-01-preview&application=ArcadiaSqlOnDemandExplorer`

Burada [*A*] çalışma alanınızın adıdır ve "-OnDemand" "-sqlod" olabilir ve burada [*B*], "ana" gibi bir veritabanı adı olmalıdır. Aynı URL değerine ancak farklı yöntem değerlerine sahip en fazla iki öğe olmalıdır; Seçenekler ve GÖNDERI. Bu iki öğenin durum sütunu altında "200" veya "20X" olup olmadığını kontrol edin; burada "x" herhangi bir tek basamak olabilir.

Bunlardan birinin "20X" ve dışında bir şey varsa:

- Durum "(başarısız)" ile başlar, "durum" sütununu genişletebilirsiniz ya da tüm metni görmek için işaretçinizi durum metninin üzerine getirin. Destek bileti açılırken metin ve/veya ekran görüntüsü ekleyin.

    ![durum metni](media/troubleshooting-synapse-studio/status-text.png)

    - ERR_NAME_NOT_RESOLVED görürseniz ve çalışma alanınızı 10 dakika içinde oluşturduysanız, 10 dakika bekleyin ve sorunun hala mevcut olup olmadığını görmek için yeniden deneyin.
    - ERR_INTERNET_DISCONNECTED veya ERR_NETWORK_CHANGED görürseniz, bılgısayar ağ bağlantınızın sorun olduğunu belirtebilir. Ağ bağlantınızı denetleyip işlemi yeniden deneyin.
    - "SSL" içeren ERR_CONNECTION_RESET, ERR_SSL_PROTOCOL_ERROR veya diğer hata kodları görürseniz, bu, yerel SSL yapılandırmanızın sorun olduğunu veya ağ yöneticinizin sunucusuz SQL havuzu sunucusuna erişimi engellediği anlamına gelebilir. Bir destek bileti açın ve hata kodunu açıklamaya ekleyin.
    - ERR_NETWORK_ACCESS_DENIED görürseniz, yerel güvenlik duvarı ilkenizin *. database.windows.net etki alanına veya 1443 uzak bağlantı noktasına erişiminin engellenip engellenmediğini yönetici ile denetlemeniz gerekebilir.
    - İsteğe bağlı olarak, bilgisayarınızda bir ağ yapılandırması sorununu denemek için aynı işlemi, farklı bir makine ve/veya ağ ortamında hemen deneyin.

- Durum "40X", "50x" veya diğer numaralardan, ayrıntıları görmek için öğe (ler) i seçin. Öğe ayrıntılarını sağ tarafta görmeniz gerekir. "Yanıt üst bilgisi" bölümünü bulun; ardından "erişim-denetim-izin-Origin" adlı bir öğenin var olup olmadığını denetleyin. Bu durumda, aşağıdaki değerlerden birine sahip olup olmadığını kontrol edin:

    - `*` (tek yıldız işareti)
    - https://web.azuresynapse.net/ (veya tarayıcınızın adres çubuğundaki metnin başladığı diğer değer)

Yanıt üst bilgisi yukarıdaki değerlerden birini içeriyorsa, hata bilgilerini zaten topladığımız anlamına gelir. Gerekirse bir destek bileti açabilir ve isteğe bağlı olarak öğe ayrıntılarının ekran görüntüsünü ekleyebilirsiniz.

Üstbilgiyi göremiyorsanız veya üst bilgide yukarıda listelenen değerlerden biri yoksa, bileti açtığınızda öğe ayrıntılarının ekran görüntüsünü ekleyin.

 
![öğe ayrıntıları](media/troubleshooting-synapse-studio/item-details.png)
 
Yukarıdaki adımlar sorununuzu çözmezse, bir destek bileti açmanız gerekebilir. Destek biletinizi gönderirken, bu kılavuzun başlangıcında "oturum KIMLIĞI" veya "tanılama bilgileri" ni ekleyin.

Sorunu bildirirken, isteğe bağlı olarak "Geliştirici Araçları" içindeki "konsolunuzun" sekmesinin ekran görüntüsünü alabilir ve destek biletini iliştirebilirsiniz. Tüm iletiyi yakalamak gerekirse içeriği kaydırın ve birden fazla ekran görüntüsü alın.

![Geliştirici araç konsolu](media/troubleshooting-synapse-studio/developer-tool-console.png)

Ekran görüntüleri iliştirirken, ekran görüntülerini aldığınız zaman (veya tahmini bir zaman aralığı) belirtin. Sorunu ararken bize yardımcı olacak.

Belirli tarayıcıların "konsol" sekmesinde zaman damgalarını göstermesini destekler. Kmıum Edge/Chrome için, "Geliştirici Araçları" içindeki "Ayarlar" iletişim kutusunu açın ve "Tercihler" sekmesinde "zaman damgalarını göster" seçeneğini işaretleyin.

![Geliştirici Aracı Konsol ayarları](media/troubleshooting-synapse-studio/developer-tool-console-settings.png)

![zaman damgasını göster](media/troubleshooting-synapse-studio/show-time-stamp.png)

## <a name="next-steps"></a>Sonraki adımlar
Yukarıdaki adımlar sorununuzu çözmeye yardımcı değilse [destek bileti oluşturun](../sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json)