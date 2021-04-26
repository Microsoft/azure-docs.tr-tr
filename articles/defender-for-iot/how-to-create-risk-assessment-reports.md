---
title: Risk değerlendirmesi raporları oluşturma
description: Tek tek sensörler tarafından algılanan ağ risklerine veya tüm sensörler tarafından algılanan risklerin toplam görünümüne ilişkin Öngörüler elde edin.
ms.date: 12/17/2020
ms.topic: how-to
ms.openlocfilehash: 853157ef1b97fefdd15785b2a71c7ccc5d06a9a9
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104784263"
---
# <a name="risk-assessment-reporting"></a>Risk değerlendirmesi raporlaması

## <a name="about-risk-assessment-reports"></a>Risk değerlendirmesi raporları hakkında

Risk değerlendirmesi raporları şunları sağlar:

- Kuruluş sensörleri tarafından algılanan cihazların genel güvenlik puanı.

- Tek bir algılayıcı tarafından algılanan her ağ aygıtı için bir güvenlik puanı.

- Güvenlik açığı bulunan cihazların, geliştirme ve güvenli cihazlara gerek duyduğu cihazların dökümü.

-  Güvenlik ve işletimsel sorunlarla ilgili Öngörüler:

    - Yapılandırma sorunları

    - Güvenlik düzeyine göre öncelikli cihaz güvenlik açığı

    - Ağ güvenlik sorunları

    - Ağ işlem sorunları

    - ICS ağlarına bağlantılar

    - Internet bağlantıları

    - Endüstriyel kötü amaçlı yazılım göstergeleri

    - Protokol sorunları

    - Saldırı vektörleri

### <a name="risk-mitigation"></a>Risk azaltma

Raporlar, güvenlik puanınızı iyileştirmenize yardımcı olacak öneriler sağlar. Örneğin, en son güvenlik güncelleştirmelerini yüklemek için bellenimi en son sürüme yükseltin veya uyarıları izleyin.

## <a name="about-security-scores"></a>Güvenlik puanları hakkında

Her raporda genel ağ güvenlik puanı oluşturulur. Puan yüzde 100 güvenlik yüzdesini temsil eder. Örneğin, %30 puanı ağınızın %30 güvenli olduğunu gösteriyor.

Risk değerlendirmesi puanları, paket incelemesi, davranış modelleme motorları ve bir SCADA özgü durum makinesi tasarımıyla öğrenilen bilgileri temel alır.

**Güvenli cihazlar** %90 üzerinde güvenlik puanı olan cihazlardır.

**Geliştirme gerektiren cihazlar**: %70 ve %89 arasında güvenlik puanı olan cihazlar.

Güvenlik puanına sahip cihazlar %70 altına sahip **cihazlardır.**

## <a name="create-risk-assessment-reports"></a>Risk değerlendirmesi raporları oluşturma

PDF risk değerlendirmesi raporu oluşturun. Rapor adı otomatik olarak risk-assessment-report-1.pdf olarak oluşturulur. Numara, oluşturduğunuz her yeni rapor için güncelleştirilir.  Oluşturma zamanı ve günü görüntülenir.

### <a name="create-a-sensor-risk-assessment-report"></a>Algılayıcı risk değerlendirmesi raporu oluşturma

Oturum açtığınız algılayıcı tarafından yapılan algılamaları temel alarak bir risk değerlendirmesi raporu oluşturun.

Bir rapor oluşturmak için:

1. Algılayıcı konsolunda oturum açın.
1. Yan menüde **risk değerlendirmesi** ' ni seçin.
1. **Rapor oluştur**' u seçin. Rapor, arşivlenmiş Raporlar bölümünde görünür.
1. Arşivlenmiş Raporlar bölümünde raporu, indirmek için seçin.

:::image type="content" source="media/how-to-generate-reports/risk-assessment.png" alt-text="Risk değerlendirmesi görünümü.":::

Şirket logosunu içeri aktarmak için:

- **Logoyu Içeri aktar**' ı seçin.

### <a name="create-an-on-premises-management-console-risk-assessment-report"></a>Şirket içi yönetim konsolu risk değerlendirmesi raporu oluşturma

Şirket içi yönetim konsolunuz tarafından yönetilen sensörlerden oluşan algılamaları temel alan bir risk değerlendirmesi raporu oluşturun. 

Bir rapor oluşturmak için:

1. Yan menüde **risk değerlendirmesi** ' ni seçin.

2. **Algılayıcı Seç** açılan listesinden bir algılayıcı seçin.

3. **Rapor oluştur**' u seçin.

4. **Arşivlenmiş raporlar** bölümünden **İndir** ' i seçin.

Şirket logosunu içeri aktarmak için:

- **Logoyu Içeri aktar**' ı seçin.

:::image type="content" source="media/how-to-generate-reports/import-logo-screenshot.png" alt-text="Risk değerlendirmesi görünümü aracılığıyla logonuzu içeri aktarın.":::

## <a name="see-also"></a>Ayrıca bkz.

[Saldırı vektörü raporlaması](how-to-create-attack-vector-reports.md)

