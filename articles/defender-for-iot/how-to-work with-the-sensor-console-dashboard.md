---
title: Algılayıcı konsolu panosu ile çalışma
description: Pano, ağınızın güvenlik durumunu hızlı bir şekilde görüntülemenizi sağlar. Bu, ilgili cihazlarla ilgili bilgilerle birlikte bir zaman çizelgesinde tüm sisteminizin tehditlerine yönelik yüksek düzeyde bir genel bakış sağlar.
ms.date: 11/03/2020
ms.topic: article
ms.openlocfilehash: e9bc650e9c306d12ef63994852816c96e1bdf40a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104781543"
---
# <a name="the-dashboard"></a>Pano

Pano, ağınızın güvenlik durumunu hızlı bir şekilde görüntülemenizi sağlar. Bir zaman çizelgesinde, her bir sistem için tehditlere yönelik yüksek düzeyde bir genel bakış sağlar ve aşağıdakiler de dahil olmak üzere ilgili cihazlar hakkında bilgiler içerir:

- Farklı önem düzeylerindeki uyarılar:

- Kritik

- Ana

- İkincil

- Uyarılar

- Sayfanın merkezindeki iki gösterge, saniye başına paketleri (PPS) ve kabul edilmemiş uyarıları (UA) gösterir. **PPS** , saniye başına sistem tarafından onaylanan paketlerin sayısıdır. **UA** henüz onaylanmamış uyarı sayısıdır.

- Açıklamalarıyla birlikte bildirilmemiş uyarıların listesi.

- Uyarı açıklamasıyla zaman çizelgesi.

:::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/dashboard-alert-screen-v2.png" alt-text="Pano":::

## <a name="viewing-the-latest-alerts"></a>En son uyarıları görüntüleme

Sayfanın merkezindeki bildirilmemiş uyarılar (UA) ölçer, bu tür uyarıların sayısını gösterir. Uyarıların bir listesini görüntülemek için Pano sayfasının alt kısmındaki **daha fazla uyarı** ' yı seçin veya yan menüdeki **Uyarılar** ' ı seçin.

:::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/unhandled-alerts-list.png" alt-text="Kabul edilmemiş uyarılar":::

## <a name="status-boxes"></a>Durum kutuları

Her durum kutusu bu bölümde açıklanmaktadır.

| Durum kutusu ve ölçerler | Description |
| -------------- | -------------- |
| :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/critical-alert-status-box-v2.png" alt-text="Kritik uyarılar"::: | **Kritik uyarılar** -sayfanın üst ortasındaki kutu, kritik uyarı sayısını gösterir. Bu kutuyu, zaman çizelgesinde ve varsa, ölçüler altındaki listede bulunan uyarı açıklamalarını göstermek için seçin.                              |
| :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/major-alert-status-box-v2.png" alt-text="Büyük uyarılar"::: | **Büyük uyarılar** -sayfanın sağ üst kısmındaki kutu, önemli uyarı sayısını gösterir. Bu kutuyu, zaman çizelgesinde ve varsa, ölçüler altındaki listede bulunan uyarı açıklamalarını göstermek için seçin.                                     |
| :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/minor-alert-status-box-v2.png" alt-text="Küçük uyarılar"::: | **Küçük uyarılar** -sayfanın sol alt kısmındaki kutu, küçük uyarı sayısını gösterir. Bu kutuyu, zaman çizelgesinde ve varsa, ölçüler altındaki listede bulunan uyarı açıklamalarını göstermek için seçin.                                   |
| :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/warnings-alert-status-box-v2.png" alt-text="Uyarı uyarıları"::: | **Uyarı uyarıları** -sayfanın alt ortasındaki kutu, uyarı uyarılarının sayısını gösterir. Bu kutuyu, zaman çizelgesinde ve varsa, ölçüler altındaki listede bulunan uyarı açıklamalarını göstermek için seçin.                             |
| :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/all-alert-status-box-v2.png" alt-text="All Alerts"::: | **Tüm uyarılar** -sayfanın sağ alt tarafındaki kutu toplam kritik, ana, ikincil ve uyarı uyarılarının sayısını gösterir. Bu kutuyu, zaman çizelgesinde ve varsa, ölçüler altındaki listede bulunan uyarı açıklamalarını göstermek için seçin. |
| :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/packets-per-second-gauge-v2.png" alt-text="Paket/saniye"::: | **Saniyedeki paket sayısı (PPS)** -PPS ölçümü, ağın performansının göstergesidir. |
| :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/unacknowledged-events-gauge-v2.png" alt-text="Kabul edilmemiş Olaylar (UA)"::: | **Kabul edilmemiş olaylar** -Bu ölçüm, kabul edilmemiş olayların sayısını belirtir.

## <a name="using-the-timeline"></a>Zaman çizelgesini kullanma

Uyarılar tarih ve saat bilgilerini içeren dikey bir zaman çizelgesi üzerinde görüntülenir.

Zaman çizelgesi grafik olarak görüntülenir:

- Kritik Uyarılar

- Büyük uyarılar

- Küçük uyarılar

- Uyarı uyarıları

:::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/timeline-of-events.png" alt-text="Zaman çizelgesi grafiği":::

## <a name="viewing-alerts"></a>Uyarıları görüntüleme

Uyarı kutusunun altındaki aşağı ok **V** ' yi seçerek uyarı girişini ve cihaz bilgilerini görüntüleyin.

:::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/extended-alert-screen.png" alt-text="Uyarı girdisi ve cihaz bilgileri":::

- Fiziksel mod eşlemesini görüntüleyecek aygıtı seçin. Tabi cihazlar vurgulanır.

- Uyarıyla ilgili ek ayrıntıları göstermek için uyarı kutusunda herhangi bir yere tıklayın. Bir açılan pencere aşağıdakine benzer şekilde görüntülenir

- Uyarı hakkında bir CSV dosyasını dışarı aktarmak için :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/excel-icon.png" alt-text="Excel"::: ' i seçin.

- Yalnızca Yöneticiler ve Güvenlik analistleri — tüm ilişkili uyarıları **bildirmek** Için :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/approve-all-icon.png" alt-text="Tümünü Onayla"::: ' yı seçin.

- Bir uyarı raporunu PDF dosyası olarak indirmek için :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/pdf-icon.png" alt-text="PDF 'yi":::seçin.

- Sabitlemek için :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/pin-icon.png" alt-text="sabitle ' yi":::seçin veya uyarıyı kaldırın. Sabitlemeyi seçmek, **uyarıları uyarı** ekranındaki **sabitlenmiş uyarılar** penceresine ekler.

- Ağ protokol analizini içeren ilgili PCAP dosyasını indirerek uyarıyı araştırmak için :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/download-icon.png" alt-text="İndir"::: ' i seçin.

- Yalnızca uyarı ile ilgili paketleri içeren ilişkili filtrelenmiş bir PCAP dosyasını indirmek için :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/cloud-download-icon.png" alt-text="bulut"::: ' u seçin, böylece çıkış dosyası boyutunu azaltır ve daha odaklanmış bir analize izin verilir. [Wireshark](https://www.wireshark.org/)kullanarak görüntüleyebilirsiniz.

- İstenen uyarının sırasında olay zaman çizelgesine gitmek için :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/navigate-icon.png" alt-text="Gezinti"::: ' yi seçin. Bu, belirli bir uyarı etrafında oluşabilecek diğer olayları değerlendirmenizi sağlar.

- Yalnızca Yöneticiler ve Güvenlik analistleri-uyarının durumunu kabul edilmemiş olarak kabul edildi olarak değiştirir. Algılanan etkinliği onaylamak için öğren ' i seçin.

:::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/unauthorized-internet-connectivity-detection-v3.png" alt-text="Yetkisiz Internet bağlantısı algılandı":::

## <a name="see-also"></a>Ayrıca bkz.

[Sensörinizdeki uyarılarla çalışma](how-to-work-with-alerts-on-your-sensor.md)
