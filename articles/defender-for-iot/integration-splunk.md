---
title: Splunk tümleştirmesi hakkında
description: Güvenlik ve ağ dayanıklılığı açısından bir görünürlük olmaması için, IoT için Defender, IoT ve splunk için Defender ile ilgili Birleşik bir yaklaşım ve güvenlik gibi yerel bir tümleştirme sağlayan IoT, IIoT ve ICS tehdit izleme uygulaması için Defender 'ı geliştirmiştir.
ms.date: 1/4/2021
ms.topic: article
ms.openlocfilehash: 28bdc6deaac09d795c45460bb211126a105b80c8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104785929"
---
# <a name="defender-for-iot-and-ics-threat-monitoring-application-for-splunk"></a>Splunk için IoT ve ICS tehdit izleme uygulaması

, IoT için Defender, bir görüntü saati ve aracıları, kuralları ya da imzaları, özelleştirilmiş becerileri veya ortam hakkında daha önce bilgi sahibi olmadan, ICS cihazları, güvenlik açıkları ve tehditler hakkında doğrudan Öngörüler sunan, patentli, ICS kullanan kendi kendine eğitim altyapılarıyla IIoT, ICS ve SCADA riskini azaltır.

Güvenlik ve ağ dayanıklılığı açısından bir görünürlük olmaması için, IoT için Defender, IoT ve splunk için Defender ile ilgili Birleşik bir yaklaşım ve güvenlik gibi yerel bir tümleştirme sağlayan IoT, IIoT ve ICS tehdit izleme uygulaması için Defender 'ı geliştirmiştir.

> [!Note]
> Six 'e başvurular IoT için Azure Defender 'a başvurur.

## <a name="about-the-splunk-application"></a>Splunk uygulaması hakkında

Uygulama, endüstriyel ortamlarda dağıtılan özel OT protokollerine ve IIoT cihazlarına çok boyutlu görünürlüğe sahip SOC analistlerini ve şüpheli veya anormal davranışları hızlı bir şekilde algılamak için ICS kullanan davranış analizlerini sağlar. Uygulama aynı zamanda hem BT hem de olay yanıtının tek bir şirket SOC içinden yapılmasını da mümkün kılar. Bu, sürekli yakınsama ve akıllı makineler ve gerçek zamanlı zeka gibi yeni IIoT girişimlerini desteklemek için önemli bir evmidir.

Splunk uygulaması yerel olarak yüklenebilir veya bir bulutta çalıştırılabilir. IoT için Defender ile tümleştirme her iki dağıtımı da destekler.

## <a name="about-the-integration"></a>Tümleştirme hakkında

Yerel uygulama aracılığıyla, IoT ve splunk için Defender tümleştirmesi kullanıcıların şunları yapmanızı sağlar:

- Endüstriyel ve kritik altyapı kuruluşları için gereken süreyi azaltarak siber tehditleri algılayın, araştırın ve üzerinde işlem yapın.

- Güvenlikle ilgili gerçek zamanlı zeka riskleri elde edin.

- Splunk Enterprise Security Threat Intelligence depoları ile IoT uyarıları için Defender ile bağıntı kurun.

- Tek bölmesden izleme ve yanıt verme.

[:::image type="content" source="media/integration-splunk/splunk-mainpage-v2.png" alt-text="Splunk aracının ana sayfası.":::](media/integration-splunk/splunk-mainpage-v2.png#lightbox)

:::image type="content" source="media/integration-splunk/alerts.png" alt-text="Splunk 'daki uyarılar sayfası.":::

Uygulama, splunk yöneticilerinin IoT için Defender 'ın gönderdiği OT uyarılarını çözümlemesine izin verir ve aşağıdakiler gibi ayrıntılar da dahil olmak üzere tüm OT güvenlik dağıtımını izler:

- Beş analiz altyapısından hangisi uyarı algıladı.

- Uyarıyı oluşturan protokol.

- IoT algılayıcısı için hangi Defender uyarı üretti.

- Uyarının önem düzeyi.

- İletişimin kaynağı ve hedefi.

## <a name="requirements"></a>Gereksinimler

### <a name="version-requirements"></a>Sürüm gereksinimleri

Aşağıdaki sürümler gereksinimlerdir.

- İçin Defender sürüm 2,4 ve üzeri.

- Splunkbase sürüm 11 ve üzeri.

- Splunk Enterprise sürüm 7,2 ve üzeri.
  
## <a name="download-the-application"></a>Uygulamayı indirme

[Splunkbase](https://splunkbase.splunk.com/app/4313/)'Den *splunk uygulaması IÇIN Six ICS tehdit izlemeyi* indirin.

## <a name="splunk-permission-requirements"></a>Splunk izin gereksinimleri

Aşağıdaki splunk izni gereklidir:

- *Yönetici* Kullanıcı rolü izinleri olan herhangi bir kullanıcı.

## <a name="send-defender-for-iot-alerts-to-splunk"></a>IoT uyarıları için Defender 'ı splunk 'a gönder

IoT uyarıları için Defender, aşağıdakiler dahil olmak üzere çok çeşitli güvenlik olayları hakkında bilgi sağlar:

- Öğrenilen ana hat ağ etkinliğinin sapmaları.

- Kötü amaçlı yazılım algılamaları.

- Şüpheli işletimsel değişikliklere göre algılama.

- Ağ bozukluklar.

- Protokol belirtimlerinden protokol sapmaları.

:::image type="content" source="media/integration-splunk/address-scan.png" alt-text="Algılama ekranı.":::

İçin Defender 'ı IoT için, uyarı bilgilerinin splunk kurumsal panosunda görüntülendiği splunk sunucusuna göndermek üzere yapılandırabilirsiniz.

:::image type="content" source="media/integration-splunk/alerts-and-details.png" alt-text="Tüm uyarıları ve bunların ayrıntılarını görüntüleyin.":::

Aşağıdaki uyarı bilgileri splunk sunucusuna gönderilir.

- Uyarının tarih ve saati.

- Olay tespit eden IoT altyapısı için Defender: protokol Ihlali, Ilke Ihlali, kötü amaçlı yazılım, anomali veya operasyonel altyapı.

- Uyarı başlığı.

- Uyarı iletisi.

- Uyarının önem derecesi: uyarı, Ikincil, büyük veya kritik.

- Kaynak cihaz adı.

- Kaynak cihaz IP adresi.

- Hedef cihaz adı.

- Hedef cihazın IP adresi.

- IoT Platformu IP adresi (ana bilgisayar) için Defender.

- IoT platform gereci (kaynak türü) için Defender adı.

Örnek çıktı aşağıda gösterilmiştir:

| Saat | Olay |
|--|--|
| 7/23/15<br />9:28:31.000 PM | **IoT Platformu Için Defender uyarısı**: bir cıhaz bir PLC komutu tarafından durduruldu<br /><br />**Tür**: işlemsel ihlal <br /><br />**Önem derecesi**: Ana <br /><br />**Kaynak adı**: my_device1 <br /><br />**Kaynak IP 'si**: 192.168.110.131 <br /><br />**Hedef adı**: my_device2<br /><br /> **Hedef IP 'si**: 10.140.33.238 <br /><br />**İleti**: bir ağ aygıtı stop PLC komutu kullanılarak durduruldu. Bu cihaz, bir başlangıç komutu gönderilene kadar çalışmaz. 192.168.110.131, PLC stop komutu kullanılarak 10.140.33.238 (Siemens S7 Device) tarafından durduruldu.<br /><br />**Ana bilgisayar**: 192.168.90.43 <br /><br />**SourceType**: Sensor_Agent |

## <a name="define-alert-forwarding-rules"></a>Uyarı iletme kurallarını tanımlayın

Splunk sunucularına uyarı bilgileri göndermek için, IoT *Iletme kuralları* Için Defender 'ı kullanın.

Uyarı kurallarını ' ye göre özelleştirmek için seçenekler kullanılabilir:

- Belirli protokoller algılandı.

- Etkinliğin önem derecesi.

- Olayları algılayan IoT altyapısı için Defender.

Bir iletme kuralı oluşturmak için:

1. Algılayıcı veya şirket içi yönetim konsolu sol bölmesinde, Iletme ' yi seçin **.**

    :::image type="content" source="media/integration-splunk/forwarding.png" alt-text="Mavi düğme Iletme uyarısı oluştur ' u seçin.":::

1. **Iletme kuralları oluştur**' u seçin. **Iletme kuralı oluştur** penceresinde kural parametrelerini tanımlayın.

    :::image type="content" source="media/integration-splunk/forwarding-rule.png" alt-text="İletme kuralınız için kurallar oluşturun.":::

    | Parametre | Açıklama |
    |--|--|
    | **Ad** | İletme kuralı adı. |
    | **Önem derecesi seçin** | İletilmek üzere en az güvenlik düzeyi olay. Örneğin, ara seçilirse, bu önem düzeyinin üzerindeki küçük uyarılar ve herhangi bir uyarı iletilir. |
    | **Protokoller** | Varsayılan olarak, tüm protokoller seçilidir. Belirli bir protokolü seçmek için, **özel** ' i seçin ve bu kuralın uygulandığı protokolü seçin. |
    | **Yapılarının** | Varsayılan olarak, tüm güvenlik motorları dahil edilir. Bu kuralın uygulandığı belirli bir güvenlik altyapısını seçmek için, **özel** ' i seçin ve altyapıyı seçin. |
    | **Sistem bildirimleri** | Sensöri çevrimiçi/çevrimdışı durumu ilet. Bu seçenek yalnızca merkezi yöneticisinde oturum açtıysanız kullanılabilir. |

1. Defender 'ın IoT için varlık bilgilerini splunk 'a göndermesini bildirmek üzere **eylem**' i seçin ve ardından **splunk sunucusuna gönder**' i seçin.

1. Aşağıdaki splunk parametrelerini girin.

    :::image type="content" source="media/integration-splunk/parameters.png" alt-text="Bu ekranda girmeniz gereken splunk parametreleri.":::

    | Parametre | Açıklama |
    |--|--|
    | **Konak** | Splunk sunucu adresi |
    | **Bağlantı noktası** | 8089 |
    | **Kullanıcı adı** | Splunk sunucusu Kullanıcı adı |
    | **Parola** | Splunk sunucu parolası |

1. **Gönder** ' i seçin

## <a name="next-steps"></a>Sonraki adımlar

[Uyarı bilgilerini iletmeyi](how-to-forward-alert-information-to-partners.md)öğrenin.
