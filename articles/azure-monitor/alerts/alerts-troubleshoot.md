---
title: Azure Izleyici uyarıları ve bildirimleri sorunlarını giderme
description: Azure Izleyici uyarıları ve olası çözümlerle ilgili yaygın sorunlar.
author: ofirmanor
ms.author: ofmanor
ms.topic: reference
ms.date: 03/16/2020
ms.openlocfilehash: 08ac23fbb29829bf2a7a46e41f9a7f646ad97915
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104586327"
---
# <a name="troubleshooting-problems-in-azure-monitor-alerts"></a>Azure Izleyici uyarıları sorunlarını giderme

Bu makalede, Azure Izleyici uyarı ve bildirimlerinde yaygın sorunlar ele alınmaktadır.

İzleme verilerinizde önemli koşullar bulunduğunda Azure Izleyici uyarıları size önceden bildirimde bulunur. Bunlar, sisteminizin kullanıcıları tarafından bildirilmeksizin sorunları tanımlamanızı ve adreslerinizi belirlemenizi sağlar. Uyarı hakkında daha fazla bilgi için bkz. [Microsoft Azure uyarılara genel bakış](./alerts-overview.md).

Uyarı tetiklenmesi veya beklendiğinde tetiklemeden sorun yaşıyorsanız, aşağıdaki makalelere bakın. Azure portal "harekete geçirilir" uyarıları görebilirsiniz.

- [Microsoft Azure 'de Azure Izleyici ölçüm uyarıları sorunlarını giderme](alerts-troubleshoot-metric.md)  
- [Microsoft Azure 'de Azure Izleyici günlüğü uyarıları sorunlarını giderme](alerts-troubleshoot-log.md)

Uyarı Azure portal göre istendiği şekilde tetiklenmiştir, ancak uygun bildirimler gerçekleşmediğinden, sorunu gidermek için bu makalenin geri kalanında yer alan bilgileri kullanın.

## <a name="action-or-notification-on-my-alert-did-not-work-as-expected"></a>Uyarımda eylem veya bildirim beklendiği gibi çalışmadı

Azure portal tetiklenen bir uyarı görebilir, ancak bazı eylemler veya bildirimleriyle ilgili bir sorun yaşıyorsanız, aşağıdaki bölümlere bakın.

## <a name="did-not-receive-expected-email"></a>Beklenen e-posta alınamadı

Azure portalında tetiklenen bir uyarı görebiliyorsanız ama bu konuda yapılandırdığınız e-postayı almadıysanız şu adımları izleyin:

1. **E-posta bir [eylem kuralı](../alerts/alerts-action-rules.md)tarafından gizlendi** mu?

    Portalda tetiklenen uyarıya tıklayarak denetleyin ve gizlenen [eylem grupları](./action-groups.md) için geçmiş sekmesine bakın:

    ![Uyarı eylem kuralı gizleme geçmişi](media/alerts-troubleshoot/history-action-rule.png)

1. **"E-posta Azure Resource Manager rolü" eyleminin türüdür mi?**

    Bu eylem yalnızca abonelik kapsamındaki ve *Kullanıcı* türünde Azure Resource Manager rol atamalarına bakar.  Rolü kaynak veya kaynak grubu düzeyinde değil abonelik düzeyinde atadığınızdan emin olun.

1. **E-posta sunucunuz ve posta kutunuz dış e-postaları kabul ediyor mu**

    Şu üç adresten gelen e-postaların engellenmediğinden emin olun:
      - azure-noreply@microsoft.com  
      - azureemail-noreply@microsoft.com
      - alerts-noreply@mail.windowsazure.com

    İç posta listeleri veya dağıtım listeleri, dışarıdaki e-posta adreslerinden gelen e-postaları engelliyor olabilir. Yukarıdaki e-posta adreslerinden gelen postalara izin vermeniz gerekir.  
    Test etmek için eylem grubuna normal bir iş e-posta adresi (posta listesi değil) ekleyin ve e-postanın eklediğiniz adrese gönderilip gönderilmediğine bakın.

1. **E-posta gelen kutusu kuralları veya bir istenmeyen posta Filtresi tarafından işlendi mi?**

    Bu e-postaları silen veya başka bir klasöre taşıyan bir gelen kutusu kuralı bulunmadığını doğrulayın. Örneğin, gelen kutusu kuralları söz konusu gönderenleri veya ilgili belirli sözcükleri yakalayabilir.

    Ayrıca şunları denetleyin:

   - e-posta istemcinizin (Outlook, Gmail gibi) istenmeyen posta ayarları
      - e-posta sunucunuzun gönderici sınırları/istenmeyen posta ayarları/karantina ayarları (Exchange, Microsoft 365, G-Suite gibi)
      - varsa, e-posta güvenlik gerecinizin ayarları (Barrampada gibi).

1. **Yanlışlıkla eylem grubundan aboneliği kaldırtınız mı?**

    Uyarı e-postalarında eylem grubu aboneliğinden çıkma bağlantısı bulunur. Bu eylem grubunun aboneliğinden yanlışlıkla çıkıp çıkmadığınızı kontrol etmek için şunlardan birini yapın:

    1. Eylem grubunu portalda açın ve Durum sütununu kontrol edin:

    ![eylem grubu durum sütunu](media/alerts-troubleshoot/action-group-status.png)

    2. E-postalarınızın arasında abonelikten çıkma onayını arayın:

    ![uyarı eylem grubu aboneliğinden çıkma](media/alerts-troubleshoot/unsubscribe-action-group.png)

    Yeniden abone olmak için lütfen size gönderilen abonelikten çıkma onayındaki bağlantıyı kullanın veya e-posta adresini eylem grubundan kaldırıp tekrar ekleyin. 
 
1. **Tek bir e-posta adresine giderken çok sayıda e-posta olduğundan sınırlı derecelendirdiniz mi?**

    Her bir e-posta adresine gönderilen e-posta sayısı saatte 100 e-posta ile [sınırlandırılmıştır](alerts-rate-limiting.md). Bu eşiği geçerseniz ek e-posta bildirimleri gönderilmeden bırakılır.  E-posta adresinize geçici olarak hız sınırlaması getirildiğini bildiren bir ileti alıp almadığınızı kontrol edin: 
 
   ![E-posta hızı sınırlandırıldı](media/alerts-troubleshoot/email-paused.png)

   Hız sınırı olmadan büyük miktarlarda bildirim almak isterseniz web kancası, mantıksal uygulama, Azure işlevi veya otomasyon runbook’ları gibi farklı bir eylem kullanmayı göz önünde bulundurun; bunların hiçbirinde hız sınırı yoktur. 

## <a name="did-not-receive-expected-sms-voice-call-or-push-notification"></a>Beklenen SMS, sesli çağrı veya anında iletme bildirimi almadı

Portalda tetiklenen bir uyarı görebiliyorsanız ama bu konuda yapılandırdığınız SMS’i, sesli aramayı veya anında iletme bildirimini almadıysanız şu adımları izleyin: 

1. **Eylem bir [eylem kuralı](../alerts/alerts-action-rules.md)tarafından gizlendi mu?**

    Portalda tetiklenen uyarıya tıklayarak denetleyin ve gizlenen [eylem grupları](./action-groups.md) için geçmiş sekmesine bakın: 

    ![Uyarı eylem kuralı gizleme geçmişi](media/alerts-troubleshoot/history-action-rule.png)

   Bu istenmeden olduysa eylem kuralını değiştirebilir, devre dışı bırakabilir veya silebilirsiniz.
 
1. **SMS/Voice: telefon numaranız doğru mu?**

   SMS’de ülke kodu veya telefon numarasında yazım hataları olup olmadığını denetleyin.
 
1. **SMS/Voice: hız sınırlı mi?**

    SMS’lerin ve sesli aramaların, telefon numarası başına beş dakikada bir bildirime denk gelen bir hız sınırı vardır. Bu eşiği geçerseniz bildirimler bırakılır.

      - Sesli arama – arama geçmişinizi gözden geçirin ve son beş dakikada Azure’dan farklı bir arama alıp almadığınıza bakın.
      - SMS - SMS geçmişinizi kontrol edip telefon numaranızda hız sınırı olduğunu belirten iletiyi bulun.

    Hız sınırı olmadan büyük miktarlarda bildirim almak isterseniz web kancası, mantıksal uygulama, Azure işlevi veya otomasyon runbook’ları gibi farklı bir eylem kullanmayı göz önünde bulundurun; bunların hiçbirinde hız sınırı yoktur. 
 
1. **SMS: yanlışlıkla eylem grubundan aboneliği Kaldırtınız mı?**

    SMS geçmişinizi açın ve bu özel eylem grubundan (devre dışı bırakma action_group_short_name yanıtı kullanılarak) veya tüm eylem gruplarından (durdurma yanıtı kullanılarak) SMS teslimini kabul ettiyseniz emin olun. Yeniden abone olmak için uygun SMS komutunu gönderin (ENABLE eylem_grubu_kısa_adı veya START) ya da eylem grubundan SMS eylemini kaldırın ve sonra yeniden ekleyin.  Daha fazla bilgi için bkz. [Eylem gruplarında SMS uyarı davranışı](alerts-sms-behavior.md).

1. **Telefonunuzdaki bildirimleri yanlışlıkla engellemiş musunuz?**

   Cep telefonlarının çoğu belirli telefon numaralarından veya kısa kodlardan gelen aramaları veya SMS’leri engellemenize ya da belirli uygulamalardan (Azure mobil uygulaması gibi) gelen anında iletme bildirimlerini engellemenize izin verir. Telefonunuzda bildirimleri yanlışla engellemiş olup olmadığınızı denetlemek için, telefonun işletim sistemine ve modeline özgü belgelerde arama yapın ya da farklı bir telefonla ve telefon numarasıyla test edin.

## <a name="expected-another-type-of-action-to-trigger-but-it-did-not"></a>Tetiklenecek başka bir eylem türü bekleniyordu, ancak 
   
Portalda tetiklenmiş bir uyarı görebiliyorsanız ama uyarının yapılandırılmış eylemi tetiklenmediyse şu adımları izleyin:

1. **Eylem bir eylem kuralı tarafından gizlendi mu?**

    Portalda tetiklenen uyarıya tıklayarak denetleyin ve gizlenen [eylem grupları](./action-groups.md) için geçmiş sekmesine bakın:

    ![Uyarı eylem kuralı gizleme geçmişi](media/alerts-troubleshoot/history-action-rule.png)
 
    Bu istenmeden olduysa eylem kuralını değiştirebilir, devre dışı bırakabilir veya silebilirsiniz.

1. **Web kancası tetiklenemedi mi?**

    1. **Kaynak IP adresleri engellendi mi?**
    
       Web kancasının çağrıldığı [IP adreslerini](./action-groups.md#action-specific-information) izin verilenler listenize ekleyin.

    1. **Web kancası uç noktanız düzgün çalışıyor mu?**

       Yapılandırdığınız web kancası uç noktasının doğru olduğunu ve uç noktanın düzgün çalıştığını onaylayın. Web kancası günlüklerinizi denetleyin veya inceleyebilmek için kodunu izleyin (örneğin gelen yükü günlüğe kaydedin).

    1. **Slack veya Microsoft Teams çağrısı mı yapıyorsunuz?**  
    Bu uç noktalardan her biri belirli bir JSON biçimi bekler. Onun yerine [bu yönergeleri](../alerts/action-groups-logic-app.md) izleyerek bir mantıksal uygulama eylemi yapılandırın.

    1. **Web kancası yanıt vermiyor veya hata verdi mı?** 

        Web kancası için zaman aşımı süremiz 10 saniyedir. HTTP uç noktası yanıt vermediğinde veya şu HTTP durum kodları döndürüldüğünde web kancası çağrısı en çok iki kere daha denenir: 408, 429, 503, 504. İlk yeniden deneme 10 saniye sonra yapılır. İkinci ve son yeniden deneme 100 saniye sonra yapılır. İkinci yeniden deneme başarısız olursa uç nokta hiçbir eylem grubu için 30 dakika boyunca bir daha çağrılmaz.

## <a name="action-or-notification-happened-more-than-once"></a>Eylem veya bildirim birden çok kez gerçekleşti 

Bir uyarının bildirimini (e-posta veya SMS gibi) birden çok kez aldıysanız veya uyarının eylemi (web kancası veya Azure işlevi gibi) birden çok kez tetiklendiyse şu adımları izleyin: 

1. **Gerçekten aynı uyarı mı?** 

    Bazı durumlarda yaklaşık aynı zamanda birbirine benzer birden çok uyarı tetiklenir. Dolayısıyla aynı uyarı eylemlerini birden çok kez tetikliyor gibi görünebilir. Örneğin etkinlik günlüğü uyarı kuralı, olay durumu alanında filtreleme yapmayarak hem bir olay başlatıldığında hem de olay bittiğinde (başarılı veya başarısız) tetiklenecek şekilde yapılandırılabilir. 

    Bu eylemlerin veya bildirimlerin farklı uyarılardan gelip gelmediğini denetlemek için uyarı ayrıntılarını inceleyin; örneğin zaman damgasına ve olay kimliğine veya bağıntı kimliğine bakın. Alternatif olarak portaldaki tetiklenmiş uyarıların listesini denetleyin. Bu durumda, uyarı kuralı mantığını uyarlamanız veya başka türlü uyarı kaynağını yapılandırmanız gerekir. 

1. **Eylem birden çok eylem grubunda yineleme mi?** 

    Bir uyarı tetiklendiğinde bu eylem gruplarından her biri bağımsız olarak işlenir. Dolayısıyla bir eylem (örneğin e-posta adresi) birden çok tetiklenmiş eylem grubunda yer alıyorsa, eylem grubu başına bir kez çağrılabilir. 

    Hangi eylem gruplarının tetiklendiğini denetlemek için uyarı geçmişi sekmesine bakın. Orada hem uyarı kuralında tanımlanan eylem gruplarını hem de eylem kuralları tarafından uyarıya eklenen eylem gruplarını görebilirsiniz: 

    ![Birden çok eylem grubunda yinelenen eylem](media/alerts-troubleshoot/action-repeated-multi-action-groups.png)

## <a name="action-or-notification-has-an-unexpected-content"></a>Eylem veya bildirimin beklenmedik bir içeriği vardır

Uyarıyı aldıysanız ancak bazı alanlarının eksik veya hatalı olduğunu düşünüyorsanız şu adımları izleyin: 

1. **Eylem için doğru biçimi mi seçdunuz?** 

    Her eylem türü (e-posta, Web kancası vb.) iki formata sahiptir: varsayılan, eski biçimi ve [daha yeni ortak şema biçimi](../alerts/alerts-common-schema.md). Bir eylem grubu oluşturduğunuzda her bir eylem için istediğiniz biçimi belirtmeniz gerekir. Eylem grubu içindeki eylemler farklı biçimlere sahip olabilir. 

    Örneğin, Web kancası eylemi için: 

    ![Web kancası eylemi şema seçeneği](media/alerts-troubleshoot/webhook.png)

    Eylem düzeyinde belirtilen biçimin beklediğiniz şey olup olmadığını kontrol edin. Örneğin belirli bir biçimdeki uyarılara (web kancası, işlev, mantıksal uygulama vb.) yanıt veren bir kod geliştirmiş olabilirsiniz ancak eylemin ilerleyen bölümlerinde başka bir kişi (veya siz) farklı bir biçim belirtmiş olabilir.  

    Ayrıca [etkinlik günlüğü uyarıları](../alerts/activity-log-alerts-webhook.md), [günlük araması uyarıları](../alerts/alerts-log-webhook.md) (Application Insights ve günlük analizi), [ölçüm uyarıları](alerts-metric-near-real-time.md#payload-schema), [ortak uyarı şeması](../alerts/alerts-common-schema-definitions.md) ve kullanım dışı olan [klasik ölçüm uyarıları](./alerts-webhooks.md) için yük biçimini (JSON) kontrol edin.

 
1. **Etkinlik günlüğü uyarıları: etkinlik günlüğünde bilgiler var mı?** 

    [Etkinlik günlüğü uyarıları](./activity-log-alerts.md) Azure etkinlik günlüğüne yazılan olayları temel alan uyarılardır; Azure kaynaklarını oluşturma, güncelleştirme veya silme, hizmet durumu ve kaynak sistem durumu olayları veya Azure Danışmanı Ile Azure ilkesinden bulguları gibi olaylar. Etkinlik günlüğünü temel alan bir uyarı aldıysanız ancak ihtiyacınız olan bazı alanlar eksik veya hatalıysa öncelikle etkinlik günlüğündeki olayları kontrol edin. Azure, aradığınız alanları etkinlik günlüğü olayına yazmadıysa bu alanlar ilgili uyarıda da mevcut olmayacaktır. 

## <a name="action-rule-is-not-working-as-expected"></a>Eylem grubu beklendiği gibi çalışmıyor 

Portalda tetiklenmiş bir uyarı görebiliyorsanız ama ilgili eylem kuralı beklendiği gibi çalışmazsa şu adımları izleyin: 

1. **Eylem kuralı etkin mi?** 

    İlgili eylem rolünün etkinleştirildiğini doğrulamak için eylem kuralı durum sütununa bakın. 

    ![sel](media/alerts-troubleshoot/action-rule-status.png) 

    Etkinleştirilmediyse, eylem kuralını seçerek ve Etkinleştir’e tıklayarak etkinleştirebilirsiniz. 

1. **Hizmet durumu uyarısı mı?** 

    Hizmet durumu uyarıları (izleme hizmeti = "Hizmet Durumu") eylem kurallarından etkilenmez. 

1. **Eylem kuralı uyarınız üzerinde işlem yaptı mı?** 

    Portalda tetiklenen uyarıya tıklayarak ve geçmiş sekmesine bakarak eylem kuralının eyleminizi işleyip işlemediğini denetleyin.

    Burada tüm eylem gruplarını gizleyen bir eylem kuralı örneği verilmiştir: 
 
     ![Uyarı eylem kuralı gizleme geçmişi](media/alerts-troubleshoot/history-action-rule.png)

    Burada başka bir eylem grubu ekleyen bir eylem kuralı örneği verilmiştir:

    ![Birden çok eylem grubunda yinelenen eylem](media/alerts-troubleshoot/action-repeated-multi-action-groups.png)
 

1. **Eylem kuralı kapsamı ve filtresi tetiklenen uyarıyla eşleşiyor mu?** 

    Eylem kuralının tetiklenmesi gerektiğini düşünüyorsanız ama tetiklenmediyse veya tetiklenmemesi gerektiğini düşünüyorsanız ama tetiklendiyse, eylem kuralı kapsamını ve filtre koşullarını dikkatle inceleyin ve tetiklenen uyarının özellikleriyle karşılaştırın. 


## <a name="how-to-find-the-alert-id-of-a-fired-alert"></a>Tetiklenen bir uyarının uyarı KIMLIĞINI bulma

Belirli bir tetiklenen uyarı (örneğin, e-posta bildirimini almadıysanız) ile ilgili bir durum açılırken, uyarı KIMLIĞINI sağlamanız gerekir. 

Bulmak için şu adımları izleyin:

1. Azure portal, tetiklenen uyarılar listesine gidin ve söz konusu uyarıyı bulun. Bu filtreleri bulmanıza yardımcı olması için kullanabilirsiniz. 

1. Uyarı ayrıntılarını açmak için uyarıya tıklayın. 

1. İlk sekmenin uyarı alanlarını (Özet sekmesi) bulana kadar aşağı kaydırın ve kopyalayın. Bu alan Ayrıca, kullanabileceğiniz bir "panoya kopyala" yardımcı düğmesine de sahiptir.  

    ![Uyarı KIMLIĞINI bul](media/alerts-troubleshoot/get-alert-id.png)

## <a name="problem-creating-updating-or-deleting-action-rules-in-the-azure-portal"></a>Azure portal eylem kuralları oluşturma, güncelleştirme veya silme sorunu

Bir [eylem kuralı](../alerts/alerts-action-rules.md)oluşturmaya, güncelleştirmeye veya silmeye çalışırken bir hata alırsanız, şu adımları izleyin: 

1. **Bir izin hatası mı alıyorsunuz?**  

     [Izleme katılımcısı yerleşik rolüne](../../role-based-access-control/built-in-roles.md#monitoring-contributor)ya da eylem kurallarıyla ve uyarılarla ilgili belirli izinlere sahip olmanız gerekir.

1. **Eylem kuralı parametrelerini doğrulamadınız mı?**  

    [Eylem kuralı belgelerini](../alerts/alerts-action-rules.md)veya [PowerShell set-azactionrule komutunu eylem kuralını](/powershell/module/az.alertsmanagement/set-azactionrule) denetleyin. 


## <a name="next-steps"></a>Sonraki adımlar
- Günlük uyarısı kullanılıyorsa, bkz. [sorun giderme günlüğü uyarıları](./alerts-troubleshoot-log.md).
- Yukarıdaki kılavuzdan sorununuzu çözmişseniz olup olmadığınızı denetlemek için [Azure Portal](https://portal.azure.com) geri dönün