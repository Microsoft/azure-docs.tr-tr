---
title: Windows 'da sesli yardımcılar-tasarım yönergeleri
titleSuffix: Azure Cognitive Services
description: Bir ses Aracısı deneyimi tasarlarken en iyi uygulamalar için yönergeler.
services: cognitive-services
author: cfogg6
manager: spencer.king
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/1/2020
ms.author: adamwa
ms.openlocfilehash: 76a29af80891142fcf7f56f93f5c7acc5c58ab04
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "92165154"
---
# <a name="design-assistant-experiences-for-windows-10"></a>Windows 10 için Tasarım Yardımcısı deneyimleri

Windows 10 ' da geliştirilen sesli yardımcılar, Windows 10 ' da sesli etkinleştirmeye yönelik en iyi deneyimler sağlamak için aşağıdaki kullanıcı deneyimi yönergelerini uygulamalıdır. Bu belge, geliştiricilere bir ses Yardımcısı için gereken anahtar çalışmayı anlamak için Windows 10 kabuğu ile tümleşecek şekilde rehberlik edecektir.

## <a name="contents"></a>İçindekiler

- [Windows 10 ' da desteklenen ses etkinleştirme görünümlerinin Özeti](#summary-of-voice-activation-views-supported-in-windows-10)
- [Gereksinimler Özeti](#requirements-summary)
- [İyi dinleme deneyimleri için en iyi uygulamalar](#best-practices-for-good-listening-experiences)
- [Uygulama içi ses etkinleştirme için tasarım kılavuzu](#design-guidance-for-in-app-voice-activation)
- [Kilit üzerinde ses etkinleştirme için tasarım kılavuzu](#design-guidance-for-voice-activation-above-lock)
- [Ses Etkinleştirme Önizlemesi için tasarım kılavuzu](#design-guidance-for-voice-activation-preview)

## <a name="summary-of-voice-activation-views-supported-in-windows-10"></a>Windows 10 ' da desteklenen ses etkinleştirme görünümlerinin Özeti

Windows 10, cihaz bağlamına bağlı olarak müşteri bağlamı için bir etkinleştirme deneyimi yaşar. Aşağıdaki Özet tablosu, ekran açık olduğunda kullanılabilen farklı görünümlere yönelik üst düzey bir genel bakıştır.

| Görünüm (kullanılabilirlik) | Cihaz bağlamı | Müşteri hedefi | Şu durumlarda görünür | Tasarım ihtiyaçları |
| --- | --- | --- | --- | --- |
| **Uygulama içi (19H1)** | Kilidin altında, yardımcı odağa sahiptir | Yardımcı uygulamayla etkileşim kurma | Yardımcısı, isteği uygulama içi işler | Ana uygulama içi görüntüleme dinleme deneyimi |
| **Kilidin üstünde (19H2)** | Kilit üzerinde, kimliği doğrulanmamış | Yardımcı ile etkileşim kurun, ancak bir uzaklıktan | Sistem kilitli ve yardımcı istekleri etkinleştirme | Uzak alan kullanıcı arabirimi için tam ekran görselleri. Kilit açmayı engellememe ilkeleri uygulayın. |
| **Ses Etkinleştirme Önizlemesi (20H1)** | Kilidin altında, yardımcının odağı yoktur | Yardımcı ile etkileşim kurma, ancak daha az zorma | Sistem, kilit ve yardımcı isteklerinin arka plan etkinleştirmesini | En az tuval. Ana uygulama görünümünde gerektiğinde yeniden boyutlandırın veya kapatın. |

## <a name="requirements-summary"></a>Gereksinimler Özeti

Farklı deneyimlere erişmek için en az çaba gerekir. Ancak, yardımcılar her bir görünüm için doğru tasarım kılavuzunu uygulamalıdır. Aşağıdaki tabloda, izlenmesi gereken gereksinimlerin bir denetim listesi verilmiştir.

| **Ses etkinleştirme görünümü** | **Yardımcı gereksinimler Özeti** |
| --- | --- |
| **Uygulama içi** | <ul><li>Uygulama içi isteği işleme</li><li>Dinleme durumları için UI göstergeleri sağlar</li><li>Pencere boyutu değişikliği olarak Kullanıcı arabirimi uyum</li></ul> |
| **Kilidin üstünde** | <ul><li>Kilit durumunu Algıla ve etkinleştirme iste</li><li>Windows kilit ekranına erişimi engelleyecek her zaman kalıcı UX sağlama</li><li>Tam ekran görselleri ve bir sesli ilk deneyim sağlayın</li><li>Aşağıdaki hatalı bir kılavuzu dikkate alarak</li><li>Aşağıdaki Gizlilik ve güvenlik konularını izleyin</li></ul> |
| **Ses Etkinleştirme Önizlemesi** | <ul><li>Kilit açma durumunu Algıla ve arka plan etkinleştirmeyi iste</li><li>Önizleme bölmesinde minimum dinleme UX 'i çizin</li><li>Sağ üst kısımdaki bir kapalı X çiz ve basıldığında akış sesini durdur</li><li>Yanıtları sağlamak için gerektiğinde ana yardımcı uygulama görünümünde yeniden boyutlandırma veya kapatma</li></ul> |

## <a name="best-practices-for-good-listening-experiences"></a>İyi dinleme deneyimleri için en iyi uygulamalar

Yardımcılar, müşterinin yardımcı durumunu anlayabilmesi için kritik geribildirim sağlamak üzere bir dinleme deneyimi derlemelidir. Aşağıda, bir yardımcı deneyim oluştururken göz önünde bulundurmanız gereken bazı durumlar verilmiştir. Bunlar, zorunlu rehberlik değil yalnızca olası önerilerdir.

- Yardım, konuşma girişi için kullanılabilir
- Yardımcı, etkinleştirme sürecinde (bir anahtar sözcük veya MIC düğmesine basma)
- Yardımcı, yardımcı buluta etkin bir şekilde ses akışı sağlar
- Yardım, müşterinin konuşmaya başlaması için hazırlanıyor
- Yardımcı, sözcüklerin söylemekte olduğunu duymakta
- Yardımcı, müşterinin konuşuyor olduğunu anlar
- Yardımcı, bir yanıtı işliyor ve hazırlıyor
- Yardımcı yanıt veriyor

Durumlar hızla değiştirilse bile, süreler Windows ekosistemi genelinde değişken olduğundan durumlar için UX sağlamak düşünülmektedir. Görsel geri bildirimin yanı sıra, korkileri de denilen kısa ses oylarından veya CHIRP 'leri &quot; &quot; çözümün parçası olabilir. Benzer şekilde, ses açıklamalarıyla bağlanmış görsel kartlar iyi yanıt seçenekleri için yapılır.

## <a name="design-guidance-for-in-app-voice-activation"></a>Uygulama içi ses etkinleştirme için tasarım kılavuzu

Yardımcı uygulama odağa sahip olduğunda, müşteri amacı uygulamayla etkileşime geçerek tüm ses etkinleştirme deneyimlerinin ana uygulama görünümü tarafından işlenmesi gerekir. Bu görünüm müşteri tarafından yeniden boyutlandırılabilir. Yardımcı kabuk etkileşimlerinin açıklanmasına yardımcı olmak için, bu belgenin geri kalanı contoso adlı bir mali hizmet Yardımcısı 'nın somut örneğini kullanır. Bu ve sonraki diyagramlarda, müşterinin söydiği, sağ taraftaki balonlarda yardım yanıtları ile sol taraftaki konuşma balonlarında görünür.

**Uygulama içi görünüm. Ses etkinleştirme başladığında ilk durum:** 
 ![ contoso finans Yardımcısı uygulamasının varsayılan tuvalinde açık olduğunu gösteren ekran görüntüsü. Sağ taraftaki bir çizgi konuşma balonu "contoso" diyor.](media/voice-assistants/windows_voice_assistant/initial_state.png)

**Uygulama içi görünüm. Başarılı bir ses etkinleştirme sonrasında, dinleme deneyimi başlar:** ![ Ses Yardımcısı dinlerken Windows 'da sesli yardımcının ekran görüntüsü](media/voice-assistants/windows_voice_assistant/listening.png)

**Uygulama içi görünüm. Tüm yanıtlar uygulama deneyiminde kalır.** ![ Windows 'da yardım yanıtları olarak sesli yardım 'ın ekran görüntüsü](media/voice-assistants/windows_voice_assistant/response.png)

## <a name="design-guidance-for-voice-activation-above-lock"></a>Kilit üzerinde ses etkinleştirme için tasarım kılavuzu

19H2 ile birlikte sunulan Windows ses etkinleştirme platformu üzerine inşa edilen yardımcılar kilit üzerinden yanıt verebilir.

### <a name="customer-opt-in"></a>Müşteri katılımı

Kilit üzerinde ses etkinleştirme, her zaman varsayılan olarak devre dışıdır. Müşteriler Windows ayarları>gizlilik>ses etkinleştirme ' ye tercih ediyor. Bu ayarı izleme ve isteme hakkında ayrıntılar için, [Yukarıdaki kilit uygulama kılavuzu](windows-voice-assistants-implementation-guide.md#detecting-above-lock-activation-user-preference)' na bakın.

### <a name="not-a-lock-screen-replacement"></a>Kilit ekranı değişikliği değil

Bildirimler veya diğer standart uygulama kilidi ekran tümleştirme noktaları yardımcı için kullanılabilir olmaya devam ederken, Windows kilit ekranı, bir ses etkinleştirme gerçekleşene kadar her zaman ilk müşteri deneyimini tanımlar. Ses etkinleştirme algılandıktan sonra, yardımcı uygulama, geçici olarak kilit ekranının üzerinde görünür. Kullanıcı karışıklığına engel olmak için, kilit yukarıdaki etkin olduğunda, yardımcı uygulama herhangi bir türde kimlik bilgisi veya oturum açmak için hiçbir zaman Kullanıcı arabirimini sunmalıdır.

![Windows kilit ekranının ekran görüntüsü](media/voice-assistants/windows_voice_assistant/lock_screen1.png)

### <a name="above-lock-experience-following-voice-activation"></a>Kilit deneyimini izleyen ses etkinleştirme sonrasında

Ekran açık olduğunda, yardım uygulaması kilit ekranının üstünde başlık çubuğu olmadan tam ekran olur. Güçlü ses birincil arabirimi ile daha büyük görseller ve güçlü ses açıklamaları, müşterinin Kullanıcı arabirimini okumak veya başka bir (bılgısayar olmayan) göreviyle uyumlu olması için çok uzakta olduğu durumlarda izin verir.

Ekran kapalı kaldığında, yardımcı uygulama, yardımcının etkinleştirmekte olduğunu göstermek ve yalnızca sesli bir deneyim sunmak için bir earcon oynayabilir.

![Kilit üzerinde ses Yardımcısı ekran görüntüsü](media/voice-assistants/windows_voice_assistant/above_lock_listening.png)

### <a name="dismissal-policies"></a>Ayırma ilkeleri

Bu bölümde, müşterilerin Windows bilgisayarlarını bir dahaki sefer daha kolay oturum açmasını kolaylaştırmak için bu bölümdeki dismissal kılavuzunu uygulaması gerekir. Aşağıda, yardımcı 'nın uygulaması gereken belirli gereksinimler verilmiştir:

- **Yukarıdaki kilidi gösteren tüm Yardımcısı** , yardımcıyı engelleyen sağ üst köşedeki bir X içermelidir.
- **Herhangi bir tuşa basmak, yardımcı uygulamasını da kapatması gerekir**. Klavye girişi, müşterinin oturum açmak istediği geleneksel bir kilit uygulaması sinyaldir. Bu nedenle, herhangi bir klavye/metin girişinin uygulamaya yönlendirilmemelidir. Bunun yerine, klavye girişi algılandığında uygulamanın kendini kapatması gerekir, böylece müşteri cihazlarıyla kolayca oturum açabilir.
- **Ekran kapalıysa, uygulamanın kendi kendine kapatması gerekir.** Böylece, müşterinin bir sonraki BILGISAYARı kullanması, oturum açma ekranının hazırlanmasını ve bekleme süresini sağlar.
- Uygulama &quot; kullanımda ise &quot; kilit üzerinde devam edebilir. &quot;kullanımda &quot; herhangi bir giriş veya çıktı oluşturur. Örneğin, müzik veya video akışı yaparken uygulama kilidin üzerine devam edebilir. &quot;Takip et &quot; ve diğer çoklu açma iletişim kutusu adımlarının, uygulamayı kilidin üzerinde tutmaya izin verilir.
- **Uygulamanın eksik olması üzerinde uygulama ayrıntıları** [Yukarıdaki kilit uygulama kılavuzunda](windows-voice-assistants-implementation-guide.md#closing-the-application)bulunabilir.

![Contoso finans Yardımcısı uygulamasının yukarıdaki kilit görünümünü gösteren ekran görüntüsü.](media/voice-assistants/windows_voice_assistant/above_lock_response.png)

![Windows kilit ekranı gösteren bir masaüstünün ekran görüntüsü.](media/voice-assistants/windows_voice_assistant/lock_screen2.png)

### <a name="privacy-amp-security-considerations-above-lock"></a>&amp;Kilit üzerinde gizlilik güvenliği konuları

Birçok bilgisayar taşınabilir ancak her zaman müşteri erişim dahilinde değildir. Bunlar, diğer kişilerin fiziksel erişimi olduğu otel odaları, uçak veya çalışma alanları için kısa bir süre içinde olabilir. Kilidin üzerinde etkinleştirilmiş yardımcılar hazırlanmamışsa, bu, bu, yani &quot; [kapalı MAID](https://en.wikipedia.org/wiki/Evil_maid_attack) &quot; saldırılarına maruz kalabilir.

Bu nedenle, yardımcılar deneyim güvende tutmaya yardımcı olmak için bu bölümdeki yönergeleri izlemelidir. Yukarıdaki etkileşim, Windows kullanıcısı kimliği doğrulanmamış olduğunda oluşur. Bu, genel olarak, **yardımcının girişinin de kimliği doğrulanmamış olarak kabul** edilmesidir.

- Yardımcılar, güvenli ve güvenle kilidin üzerine erişilmek üzere **onaylanan becerileri belirlemek için bir yetenek izin verilenler listesi uygulamalıdır** .
- Konuşmacı KIMLIĞI teknolojileri, hafifletmesini bazı riskler halinde bir rol oynayabilir, ancak konuşmacı KIMLIĞI Windows kimlik doğrulaması için uygun bir değişiklik değildir.
- İzin verilen yetenek listesi, üç eylem veya yetenek sınıfını göz önünde bulundurmalıdır:

| **Action sınıfı** | **Açıklama** | **Örnekler (tamamlanmamış bir liste değil)** |
| --- | --- | --- |
| Kimlik doğrulaması olmadan güvenli | Genel amaçlı bilgiler veya temel uygulama komutu ve denetimi | &quot;Ne zaman? &quot; , &quot; sonraki parçayı çal&quot; |
| Konuşmacı KIMLIĞIYLE güvenli | Kimliğe bürünme riski, kişisel bilgiler görüntülenir. | &quot;Sonraki randevumın ne&#39;? &quot; , &quot; alışveriş listemi gözden geçirin &quot; , &quot; çağrıyı yanıtlayın&quot; |
| Yalnızca Windows kimlik doğrulamasından sonra güvenli | Bir saldırganın müşteriye zarar vermek için kullanabileceği yüksek riskli eylemler | &quot;Daha fazla Market satın alma &quot; , &quot; (önemli) randevumu silme, ( &quot; &quot; Ortalama) kısa mesaj gönderme &quot; , &quot; (nefarli) Web sayfası başlatma&quot; |

Contoso söz konusu olduğunda, genel stok bilgileriyle ilgili genel bilgiler kimlik doğrulaması olmadan güvenlidir. Sahip olunan paylaşımların sayısı gibi müşteriye özgü bilgiler, konuşmacı KIMLIĞIYLE güvende olabilir. Ancak, satın alma veya satış stokları Windows kimlik doğrulaması olmadan hiçbir şekilde izin vermemelidir.

Deneyim, Web bağlantıları veya diğer uygulama uygulama başlatma işlemi daha güvenli hale getirmek için **Müşteri oturum açana kadar Windows tarafından her zaman engellenir.** En son bir risk azaltma olarak Microsoft, ciddi bir güvenlik sorunu zamanında giderilmemişse izin verilen etkin yardımcılar listesinden bir uygulamayı kaldırma hakkını saklı tutar.

## <a name="design-guidance-for-voice-activation-preview"></a>Ses Etkinleştirme Önizlemesi için tasarım kılavuzu

Aşağıdaki kilit, yardımcı uygulama odağa sahip _değilse_ , Windows, müşteriyi Flow 'da tutmaya yardımcı olmak için daha az zorlayıcı bir sesli etkinleştirme Kullanıcı arabirimi sağlar. Bu durum özellikle, tam uygulamayı başlattıkları takdirde yüksek ölçüde kesintiye uğrayabilecek etkinleştirmeler söz konusu olduğunda geçerlidir. Temel fikir, her bir yardımcının kabukta başka bir giriş ve Yardım görev çubuğu simgesi olması. Arka plan etkinleştirme isteği gerçekleştiğinde, Yardım görev çubuğu simgesinin üstünde küçük bir görünüm belirir. Yardımcılar, bu tuvalde küçük bir dinleme deneyimi sağlamalıdır. İstekleri işledikten sonra, yardımcılar bu görünümü bir bağlam içi yanıtı göstermek ya da daha büyük, daha ayrıntılı görseller göstermek için ana uygulama görünümünü devre dışı bırakmak için yeniden boyutlandırmayı seçebilirler.

- En düşük düzeyde kalmak için, önizlemede bir başlık çubuğu yoktur, bu nedenle **yardımcının, müşterilerin görünümü kapatması için sağ üst köşedeki bir X çizmesi gerekir.** Kapat düğmesine basıldığında çağrılacak belirli API 'Lerin [uygulamayı kapatma](windows-voice-assistants-implementation-guide.md#closing-the-application) bölümüne bakın.
- Yardımcılar, sesli etkinleştirme önizlemelerini desteklemek için, müşterileri ilk çalıştırma sırasında yardımcı görev çubuğuna sabitlemek üzere davet edebilir.

**Ses Etkinleştirme Önizlemesi: Ilk durum**

Contoso Yardımcısı, görev çubuğunda bir giriş içerir: Bu, su, dairesel simgesi.

![Görev çubuğu simgesi olarak Windows 'da sesli yardımcının ekran görüntüsü ön etkinleştirme](media/voice-assistants/windows_voice_assistant/pre_compact_view.png)

**Etkinleştirme ilerledikçe**, yardımcı arka plan etkinleştirmesini ister. Yardımcının küçük bir önizleme bölmesi (varsayılan genişlik 408 ve Yükseklik: 248) verilir. Sunucu tarafı sesli etkinleştirme, sinyalin yanlış pozitif olduğunu belirlerse, bu görünüm minimum kesinti için kapatılabilir.

![Etkinleştirme doğrulanırken Windows 'da ses Yardımcısı 'nın Kompakt görünümde ekran görüntüsü](media/voice-assistants/windows_voice_assistant/compact_view_activating.png)

**Son etkinleştirme onaylanırsa**, yardımcı, dinleme UX ' i sunar. Yardımcı, her zaman ses etkinleştirme önizlemesinin sağ üst köşesinde bir Kapat X çizmelidir.

![Windows dinlemesi için ses Yardımcısı 'nın Kompakt görünümde ekran görüntüsü](media/voice-assistants/windows_voice_assistant/compact_view_listening.png)

**Hızlı yanıtlar** , ses etkinleştirme önizlemede görüntülenebilir. Bir TryResizeView, yardımcılar farklı boyutlarda talep etmek için izin verir.

![Windows yanıtlama sırasında ses Yardımcısı 'nın Kompakt görünümde ekran görüntüsü](media/voice-assistants/windows_voice_assistant/compact_view_response.png)

**El ile**. Herhangi bir noktada, yardımcı, daha fazla bilgi, iletişim veya daha fazla ekran alanı gerektiren cevaplar sağlamak için ana uygulama görünümüne gelebilir. Uygulama ayrıntıları için lütfen [Compact View 'dan tam görünüme geçiş](windows-voice-assistants-implementation-guide.md#transition-from-compact-view-to-full-view) bölümüne bakın.

![Kompakt görünümü genişletmeden önce ve sonra Windows üzerinde ses Yardımcısı ekran görüntüleri](media/voice-assistants/windows_voice_assistant/compact_transition.png)

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Ses yardımcınızı geliştirmeye başlayın](how-to-windows-voice-assistants-get-started.md)