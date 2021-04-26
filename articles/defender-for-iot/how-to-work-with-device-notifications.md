---
title: Cihaz bildirimleri ile çalışma
description: Bildirimler, bu etkinliği işlemeye yönelik önerilerle birlikte ilgilenmeniz gerekebilecek ağ etkinliği hakkında bilgiler sağlar.
ms.date: 12/12/2020
ms.topic: how-to
ms.openlocfilehash: c0c2fc5a4c01a8a31512cd43c340bf3fadc259b1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104781339"
---
# <a name="work-with-device-notifications"></a>Cihaz bildirimleri ile çalışma

Bildirimler, bu etkinliği işlemeye yönelik önerilerle birlikte ilgilenmeniz gerekebilecek ağ etkinliği hakkında bilgiler sağlar. Örneğin, hakkında bir bildirim alabilirsiniz:

- Etkin olmayan bir cihaz. Cihaz artık ağınızın bir parçası değilse, bunu kaldırabilirsiniz. Cihaz devre dışı ise (örneğin, yanlışlıkla ağla bağlantısı kesildiğinde), cihazı yeniden bağlayın ve bildirimi kapatın.

- Şu anda yalnızca bir MAC adresi tarafından tanımlanan cihazda bir IP adresi algılandı. Cihazın IP adresini yetkilendirerek yanıtlayın.

Bildirimlere yanıt vermek cihaz eşlemesinde, cihaz envanterinde ve veri araştırma sorgularında ve raporlarında belirtilen bilgileri geliştirir. Ayrıca, meşru ağ değişikliklerine ve olası ağ yapılandırması hatalarına yönelik öngörüler sağlar.

**Bildirimler ve uyarılar**

Ağ etkinliğinde bildirim almaya ek olarak, *Uyarılar* alabilirsiniz. Bildirimler, bir tehdit sunmaya ağ değişiklikleri veya çözümlenmemiş cihaz özellikleri hakkında bilgi sağlar. Uyarılar ağ sapmaları ve ağda tehdit oluşturabilecek değişiklikler hakkında bilgi sağlar.

Bildirimleri görüntülemek için:

1. Konsolun sol menü bölmesinden **cihaz Haritası** ' nı seçin.

2. **Bildirimler** simgesini seçin. Simgenin üzerindeki kırmızı sayı, bildirimlerin sayısını gösterir.

   :::image type="content" source="media/how-to-enrich-asset-information/notifications-alert-screenshot.png" alt-text="Bildirim simgesi.":::

   **Bildirimler** penceresi, sensörin algıladığı tüm bildirimleri görüntüler.

   :::image type="content" source="media/how-to-enrich-asset-information/notification-screen.png" alt-text="Bildirimler.":::

## <a name="filter-the-notifications-window"></a>Bildirimler penceresini filtrele

İlgilendiğiniz bildirimleri göstermek için arama filtrelerini kullanın.

| Filtreleme ölçütü | Description |
|--|--|
| Türe göre filtrele | İlgilendiğiniz belirli bir alanı kapsayan bildirimleri görüntüleyin. Örneğin, yalnızca etkin olmayan cihazlara ilişkin bildirimleri görüntüleyin. |
| Tarih aralığına göre filtrele | Belirli bir zaman aralığını kapsayan bildirimleri görüntüler. Örneğin, yalnızca geçen hafta boyunca gönderilen bildirimleri görüntüleyin. |
| Belirli bilgileri arayın | Belirli bildirimleri arayın. |

## <a name="notification-events-and-responses"></a>Bildirim olayları ve yanıtları

Aşağıdaki tabloda, aldığınız bildirim olay türleri ve bunları işleme seçenekleriyle birlikte açıklanmaktadır. Cihaz bilgilerini önerilen bir değerle güncelleştirebilir veya bildirimi kapatabilirsiniz. Bir bildirimi kapattığınızda, cihaz bilgileri önerilen bilgilerle güncellenmez. Trafik tekrar algılanırsa, bildirim yeniden gönderilir.

| Bildirim olayı türleri | Description | Yanıtlar |
|--|--|--|
| Yeni IP algılandı | Yeni bir IP adresi cihazla ilişkili. Beş senaryo algılanmayabilir: <br /><br /> Bir cihazla ek bir IP adresi ilişkilendirildi. Bu cihaz, var olan bir MAC adresiyle de ilişkilendirilir.<br /><br /> Var olan bir MAC adresini kullanan bir cihaz için yeni bir IP adresi algılandı. Şu anda cihaz bir IP adresi kullanarak iletişim kurmamaktadır.<br /> <br /> NetBIOS adı kullanan bir cihaz için yeni bir IP adresi algılandı. <br /><br /> Bir MAC adresiyle ilişkili bir cihaz için yönetim arabirimi olarak bir IP adresi algılandı. <br /><br /> Sanal IP adresi kullanan bir cihaz için yeni bir IP adresi algılandı. | **Cihaza ek IP ayarlama** (cihazları birleştirme) <br /> <br />**Mevcut IP 'yi Değiştir** <br /> <br /> **At**<br /> Bildirimi kaldırın. |
| Etkin olmayan cihazlar | Cihazda 60 günden daha uzun bir süre için trafik algılanmadı. | **Silme** <br /> Bu cihaz ağınızın bir parçası değilse, kaldırın. <br /><br />**At** <br /> Cihaz ağınızın bir parçasıysa bildirimi kaldırın. Cihaz etkin değilse (örneğin, ağdan yanlışlıkla bağlantısı kesildiğinde), bildirimi kapatın ve cihazı yeniden bağlayın. |
| Yeni OT cihazları | Bir alt ağ, bir ICS alt ağında tanımlanmayan bir OT cihazı içerir. <br /><br /> En az bir OT cihazı içeren her alt ağ, bir ICS alt ağı olarak tanımlanabilir. Bu, haritadaki OT ve BT cihazlarının birbirinden ayırt edilmesine yardımcı olur. | **ICS alt ağı olarak ayarla** <br /> <br /> **At** <br />Cihaz alt ağın parçası değilse bildirimi kaldırın. |
| Hiçbir alt ağ yapılandırılmadı | Ağınızda Şu anda yapılandırılmış alt ağ yok. <br /><br /> Haritada daha iyi temsil için alt ağları ve aynı şekilde, OT ve BT cihazlarını ayırt etme özelliğini yapılandırın. | **Alt ağlar yapılandırmasını açın** ve alt ağları yapılandırın. <br /><br />**At** <br /> Bildirimi kaldırın. |
| İşletim sistemi değişiklikleri | Cihazla bir veya daha fazla yeni işletim sistemi ilişkilendirilmiş. | Cihazla ilişkilendirmek istediğiniz yeni işletim sisteminin adını seçin.<br /><br /> **At** <br /> Bildirimi kaldırın. |
| Yeni alt ağlar | Yeni alt ağlar bulundu. | **Learn**<br />Alt ağı otomatik olarak ekleyin.<br />**Alt ağ yapılandırmasını aç**<br />Tüm eksik alt ağ bilgilerini ekleyin.<br />**At**<br />Bildirimi kaldırın. |
| Cihaz türü değişiklikleri | Cihazla yeni bir cihaz türü ilişkilendirildi. | **{...} Olarak ayarla**<br />Yeni türü cihazla ilişkilendirin.<br />**At**<br />Bildirimi kaldırın. |

## <a name="respond-to-many-notifications-simultaneously"></a>Aynı anda birçok bildirimi yanıtla

Aynı anda birkaç bildirimi işlemeniz gerekebilir. Örnek:

- Büyük bir ağ sunucuları kümesine bir işletim sistemi yükseltmesi olduysa, tüm yükseltilen sunucular için yeni sunucu sürümlerini öğrenmek üzere sensöre talimat verebilirsiniz. 

- Belirli bir satırdaki bir cihaz grubu kullanıma alındı ve artık etkin değilse, bu cihazları konsolundan kaldırmak için sensöre talimat verebilirsiniz.

Algılayıcıya yeni algılanan bilgileri birden çok cihaza uygulamayı veya yoksaymayı söyleyebilirsiniz.   

Bildirimleri göstermek ve bildirimleri işlemek için:

1. **Türe göre filtrele, tarih aralığı** seçeneği veya **Tümünü Seç** seçeneğini kullanın. Gerektiğinde bildirimlerin seçimini kaldırın.

2. Yeni algılanan bilgilerin seçili cihazlara uygulanmasını **öğrenin**' i seçerek sensöre bildirin. Ya da, algılayıcı ' ı seçerek sensöre yeni algılanan bilgileri yoksaymasını **söyleyin.** Aynı anda bir şekilde öğrenmekte ve kapatabilmeniz gereken bildirimlerin sayısı ile birlikte, tek yapmanız gereken bildirimlerin sayısı gösterilir.

**Yeni IP 'ler** ve **hiçbir alt ağ** yapılandırılmadı olayı aynı anda işlenemez. El ile onay gerektirir.

## <a name="see-also"></a>Ayrıca bkz.

[Uyarıları görüntüleme](how-to-view-alerts.md)
