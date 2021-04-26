---
title: Veri araştırma raporları oluşturma
description: protokoller, bellenim sürümleri veya programlama komutları gibi çeşitli katmanlarda ağ cihazlarınızla ilgili kapsamlı ve ayrıntılı bilgiler oluşturun.
ms.date: 01/20/2021
ms.topic: how-to
ms.openlocfilehash: 99754959e7a3a08b4d763b85b0b9315476969774
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104779078"
---
# <a name="sensor-data-mining-queries"></a>Algılayıcı veri araştırma sorguları

## <a name="about-sensor-data-mining-queries"></a>Algılayıcı veri araştırma sorguları hakkında

Veri araştırma araçları, çeşitli katmanlarda ağ cihazlarınızla ilgili kapsamlı ve ayrıntılı bilgiler oluşturmanıza imkan tanır. Örneğin, aşağıdakileri temel alarak bir sorgu oluşturabilirsiniz:

- Zaman dönemleri

- İnternet bağlantıları

- Bağlantı noktaları

- Protokoller

- Bellenim sürümleri

- Programlama komutları

- Cihazın eylemsizlik durumu

Filtreleri temel alarak rapor üzerinde ince ayar yapabilirsiniz. Örneğin, bellenim 'un güncelleştirildiği belirli bir alt ağı sorgulayabilirsiniz.

:::image type="content" source="media/how-to-generate-reports/active-device-list-v2.png" alt-text="Etkin cihazların listesi.":::

Sorguları yönetmek için çeşitli araçlar mevcuttur. Örneğin, dışa aktarabilir ve kaydedebilirsiniz.

> [!NOTE]
> Yöneticiler ve Güvenlik analistlerinin veri araştırma seçeneklerine erişimi vardır.

### <a name="dynamic-updates"></a>Dinamik güncelleştirmeler

Oluşturduğunuz veri araştırma sorguları, her açışınızda dinamik olarak güncelleştirilir. Örnek:

- 1 Haziran 'da cihazlarda bellenim sürümleri için bir rapor oluşturursanız ve raporu 10 Haziran 'da yeniden açarsanız, bu rapor 10 Haziran 'da doğru olan bilgilerle güncelleştirilir.

- 1 Haziran 'da son 30 gün içinde bulunan yeni cihazları algılamaya yönelik bir rapor oluşturursanız ve raporu 30 Haziran 'da açarsanız, sonuçlar son 30 gün boyunca görüntülenir.

### <a name="data-mining-use-cases"></a>Veri madenciliği kullanım örnekleri

Çeşitli güvenlik ekiplerine yönelik kapsamlı güvenlik ihtiyaçlarını karşılamak için sorguları kullanabilirsiniz:

- **SOC olay yanıtı**: anında olay yanıtıyla başa çıkmak için bir raporu gerçek zamanlı olarak oluşturun. Örneğin, veri madenciliği, düzeltme eki gerektirebilecek cihazların bir listesi için rapor oluşturabilir.

- **Forensics**: araştırma raporlarının geçmiş verilerini temel alan bir rapor oluşturun.

- **BT ağ bütünlüğü**: genel ağ güvenliğinin artırılmasına yardımcı olan bir rapor oluşturun. Örneğin, zayıf kimlik doğrulama kimlik bilgileri olan cihazları listeleyen bir rapor oluşturabilirsiniz.

- **Görünürlük**: ağınızın tüm temel parametrelerini görüntülemek için tüm sorgu öğelerini içeren bir rapor oluşturun.

## <a name="data-mining-storage"></a>Veri araştırma depolaması

Veri araştırma bilgileri, bir cihazın silindiği durumlar dışında sürekli olarak kaydedilir ve saklanır. Veri araştırma sonuçları, güvenli bir sunucuya aktarılabilir ve dışarıdan depolanabilir. Ayrıca, algılayıcı, verilerin devamlılığını ve korunmasını sağlamak için otomatik günlük yedeklemeler gerçekleştirir.


## <a name="predefined-data-mining-queries"></a>Önceden tanımlanmış veri araştırma sorguları

Aşağıdaki önceden tanımlanmış sorgular kullanılabilir. Bu sorgular gerçek zamanlı olarak oluşturulur.

- **Cdirme**: son 24 saat içinde bilinen güvenlik açıklarına sahip olan cihazların listesi.

- **Dışlanan CCR 'ler**: el ile dışlanan tüm nesnelerin listesi. VA raporlarında ve saldırı vektörlerine daha doğru sonuçlar elde etmek için, Cvileri ekleyerek ve dışlayarak CVE listesini el ile özelleştirebilirsiniz.

- **Internet etkinliği**: internet 'e bağlı cihazlar.

- **Etkin olmayan cihazlar**: son yedi gün için iletilmedi olan cihazlar.

- **Etkin cihazlar**: son 24 saat içindeki etkin ağ cihazları.

- **Uzaktan erişim**: uzak oturum protokolleriyle iletişim kuran cihazlar.

- **Programlama komutları**: Endüstriyel programlama gönderen cihazlar.

Bu raporlara,, RO kullanıcıları ve diğer kullanıcıların bunları görüntüleyebileceği **raporlar** ekranından otomatik olarak erişilebilir. RO kullanıcıları veri araştırma raporlarına erişemez.

:::image type="content" source="media/how-to-generate-reports/data-mining-screeshot-v2.png" alt-text="Veri araştırma ekranı.":::

## <a name="create-a-data-mining-query"></a>Veri araştırma sorgusu oluşturma

Veri araştırma raporu oluşturmak için:

1. Yan menüden **veri araştırma** ' yı seçin. Önceden tanımlanmış önerilen raporlar otomatik olarak görünür.

 :::image type="content" source="media/how-to-generate-reports/data-mining-screeshot-v2.png" alt-text="Yan bölmeden veri araştırma ' yı seçin.":::

2. :::image type="icon" source="media/how-to-generate-reports/plus-icon.png" border="false"::: öğesini seçin.

3. **Yeni rapor** ' ı seçin ve raporu tanımlayın.

   :::image type="content" source="media/how-to-generate-reports/create-new-report-screen.png" alt-text="Bu ekranı doldurarak yeni bir rapor oluşturun.":::

   Aşağıdaki parametreler kullanılabilir:

   - Bir rapor adı ve açıklama sağlayın.

   - Kategoriler için şunlardan birini seçin:

      - Ağınızdaki tüm cihazlarla ilgili tüm rapor sonuçlarını görüntülemek için **Kategoriler (tümü)** .

      - Standart kategorileri seçmek için **genel** ' i seçin.

   - İlgilendiğiniz belirli rapor parametrelerini seçin.

   - Bir sıralama düzeni (**order by**) seçin. Sonuçları etkinliğe veya kategoriye göre sıralayın.

   - **Rapor sonuçlarını rapor sayfasından erişilebilen** bir rapor olarak kaydetmek Için rapor **sayfalarına kaydet** ' i seçin. Bu, RO kullanıcılarının oluşturduğunuz raporu çalıştırmasını sağlar.

   - Daha fazla filtre eklemek için **filtreler (Ekle)** seçeneğini belirleyin. Joker karakter istekleri desteklenir.

   - Bir cihaz grubu belirtin (cihaz eşlemesinde tanımlanmıştır).

   - Bir IP adresi belirtin.

   - Bir bağlantı noktası belirtin.

   - Bir MAC adresi belirtin.

4. **Kaydet**’i seçin. Rapor sonuçları **veri araştırma** sayfasında açılır.

:::image type="content" source="media/how-to-generate-reports/data-mining-page.png" alt-text="Veri araştırma sayfasında görülen sonuçları raporla.":::

### <a name="manage-data-mining-reports"></a>Veri araştırma raporlarını yönetme

Aşağıdaki tabloda veri madenciliği için yönetim seçenekleri açıklanmaktadır:

| Simge görüntüsü | Description |
|--|--|
| :::image type="icon" source="media/how-to-generate-reports/edit-a-simulation-icon.png" border="false"::: | Rapor parametrelerini düzenleyin. |
| :::image type="icon" source="media/how-to-generate-reports/export-as-pdf-icon.png" border="false"::: | PDF olarak dışa aktar. |
| :::image type="icon" source="media/how-to-generate-reports/csv-export-icon.png" border="false"::: |CSV olarak dışa aktarın. |
| :::image type="icon" source="media/how-to-generate-reports/information-icon.png" border="false"::: | Son değiştirme tarihi gibi ek bilgileri göster. Bir sorgu sonucu anlık görüntüsü oluşturmak için bu özelliği kullanın. Örneğin, ekip liderleriyle veya SOC analistleriyle ilgili daha fazla araştırma için bunu yapmanız gerekebilir. |
| :::image type="icon" source="media/how-to-generate-reports/pin-icon.png" border="false"::: | **Raporlar** sayfasında görüntüleyin veya **raporlar** sayfasında gizleyin. :::image type="content" source="media/how-to-generate-reports/hide-reports-page.png" alt-text="Raporlarınızı gizleyin veya ortaya çıkar."::: |
| :::image type="icon" source="media/how-to-generate-reports/delete-simulation-icon.png" border="false"::: | Raporu silin. |

#### <a name="create-customized-directories"></a>Özelleştirilmiş dizinler oluşturma 

Kategoriler için dizinler oluşturarak veri madenciliği sorguları için kapsamlı bilgiler düzenleyebilirsiniz. Örneğin, protokoller veya konumlar için dizinler oluşturabilirsiniz.

Yeni bir dizin oluşturmak için:

1. :::image type="icon" source="media/how-to-generate-reports/plus-icon.png" border="false":::Yeni bir dizin eklemek için seçin.

2. Yeni Dizin formunu göstermek için **Yeni Dizin** ' i seçin.

3. Yeni dizini adlandırın.

4. Gerekli raporları yeni dizine sürükleyin. İstediğiniz zaman raporu, ana görünüme geri sürükleyebilirsiniz.

5. Açmak, düzenlemek veya silmek için yeni dizine sağ tıklayın.

#### <a name="create-snapshots-of-report-results"></a>Rapor sonuçlarının anlık görüntülerini oluşturma

Daha fazla araştırma için bazı sorgu sonuçlarını kaydetmeniz gerekebilir. Örneğin, sonuçları çeşitli güvenlik ekipleriyle paylaşmanız gerekebilir.

Anlık görüntüler, rapor sonuçları içine kaydedilir ve dinamik sorgular oluşturmaz.

:::image type="content" source="media/how-to-generate-reports/report-results-report.png" alt-text="Görüntüsünü.":::

Anlık görüntü oluşturmak için:

1. Gerekli raporu açın.

2. Bilgi simgesini seçin :::image type="icon" source="media/how-to-generate-reports/information-icon.png" border="false"::: .

3. **Yeni al**' ı seçin.

4. Anlık görüntü için bir ad girin ve **Kaydet**' i seçin.

:::image type="content" source="media/how-to-generate-reports/take-a-snapshot.png" alt-text="Anlık görüntü alın.":::

#### <a name="customize-the-cve-list"></a>CVE listesini özelleştirme

CVE listesini aşağıdaki şekilde el ile özelleştirebilirsiniz:

  - Cvileri dahil etme/hariç tutma

  - CVE Puanını değiştirme

CVE raporunda el ile değişiklikler yapmak için:

1.  Yan menüden **veri araştırma**' yı seçin.

2. :::image type="icon" source="media/how-to-generate-reports/plus-icon.png" border="false"::: **Veri araştırma** penceresinin sol üst köşesindeki seçimi yapın. Ardından **Yeni rapor**' ı seçin.

   :::image type="content" source="media/how-to-generate-reports/create-a-new-report-screen.png" alt-text="Yeni bir rapor oluşturun.":::

3. Sol bölmeden aşağıdaki seçeneklerden birini seçin:

   - **Bilinen güvenlik açıkları**: her iki seçeneği de seçer ve sonuçları raporun iki tablosuna, biri bir tane, diğeri de hariç tutulan cele alır.

   - **Cves**: tüm cves listesini sunmak için bu seçeneği belirleyin.

   - **Dışlanan CAS**'ler: tüm dışlanan nesnelerin bir listesini temsil etmek için bu seçeneği belirleyin.

4. **Ad** ve **Açıklama** bilgilerini doldurup **Kaydet**' i seçin. Yeni rapor, **veri araştırma** penceresinde görünür.

5. Cvileri dışlamak için, veri araştırma raporunu Cdirme 'Ler için açın. Tüm CVEs 'ler görüntülenir.

   :::image type="content" source="media/how-to-generate-reports/cves.png" alt-text="C V E raporu.":::

6. Listedeki öğeleri seçmeyi etkinleştirmek için :::image type="icon" source="media/how-to-generate-reports/enable-selecting-icon.png" border="false"::: özelleştirmek Istediğiniz Cvileri seçin ve seçin. **İşlemler** çubuğu en altta görünür.

   :::image type="content" source="media/how-to-generate-reports/operations-bar-appears.png" alt-text="Veri madenciliği Işlemler çubuğunun ekran görüntüsü.":::

7. Dışlamak istediğiniz Cvileri seçin ve ardından **kayıtları Sil**' i seçin. Seçtiğiniz canlar, CVEs listesinde görünmez ve bir tane oluşturduğunuzda dışlanan Cdirme listesinde görünür.

8. Dışlanan CCR 'Ler listesine dahil etmek için, hariç tutulan celik 'ler için raporu oluşturun ve bu listeden silmek istediğiniz öğeleri ekleyin.

9. Puanı değiştirmek istediğiniz Ckları seçin ve ardından **CVE Puanını Güncelleştir**' i seçin.

   :::image type="content" source="media/how-to-generate-reports/set-new-score-screen.png" alt-text="CVE Puanını güncelleştirin.":::

10. Yeni puanı girin ve **Tamam**' ı seçin. Güncelleştirilmiş puan, seçtiğiniz Cide görüntülenir.



## <a name="sensor-reports-based-on-data-mining"></a>Veri madenciliği temel alan algılayıcı raporları

**Raporlar** seçeneğinden erişilen düzenli raporlar, önceden tanımlanmış veri araştırma raporlarıdır. Veri madenciliği, veri madenciliği sorgu sonuçlarının statik temsili olan dinamik sorgular değildir.

Veri madenciliği sorgu sonuçları, salt okuma kullanıcıları için kullanılamaz. Yalnızca okuma kullanıcılarının veri araştırma sorguları tarafından oluşturulan bilgilere erişimi olmasını isteyen yöneticiler ve Güvenlik analistleri, bilgileri rapor olarak kaydetmelidir.

Raporlar, veri madenciliği sorgu sonuçları tarafından oluşturulan bilgileri yansıtır. Bu, raporlar görünümünde kullanılabilen varsayılan veri araştırma raporlarını içerir. Yönetici ve Güvenlik analistleri Ayrıca özel veri araştırma sorguları oluşturabilir ve bunları rapor olarak kaydedebilir. Bu raporlar Ayrıca, RO kullanıcıları için de kullanılabilir.

Bir rapor oluşturmak için:

1. Yan menüdeki **raporlar** ' ı seçin.

2. Görüntülenecek gerekli raporu seçin. Seçim, **programlama komutları** ve **Uzaktan erişim** gibi **özel** veya **otomatik üretilmiş** raporlar olabilir.

3. Ekranın sağ üst köşesindeki simgelerden birini seçerek raporu dışarı aktarabilirsiniz:

   :::image type="icon" source="media/how-to-generate-reports/export-to-pdf-icon.png" border="false"::: PDF dosyasına dışarı aktarın.

   :::image type="icon" source="media/how-to-generate-reports/export-to-csv-icon.png" border="false"::: Bir CSV dosyasına aktarın.

> [!NOTE] 
> RO kullanıcısı yalnızca kendileri için oluşturulan raporları görebilir.

:::image type="content" source="media/how-to-generate-reports/select-a-report-screen.png" alt-text="Oluşturulacak raporu seçin.":::

:::image type="content" source="media/how-to-generate-reports/remote-access-report.png" alt-text="Uzaktan erişim raporu oluşturuldu.":::

## <a name="on-premises-management-console-reports-based-on-data-mining-reports"></a>Veri araştırma raporlarına dayalı şirket içi yönetim konsolu raporları

Şirket içi yönetim konsolu, kendisine bağlı her bir algılayıcı için raporlar oluşturmanıza olanak sağlar. Raporlar, gerçekleştirilen algılayıcı veri araştırma sorgularını temel alır.

Aşağıdaki raporları oluşturabilirsiniz:

- **Etkin cihazlar (son 24 saat)**: 24 saat içinde ağ etkinliğini gösteren cihazların listesini gösterir.

- **Etkin olmayan cihazlar (son 7 gün)**: son yedi gün içinde ağ etkinliği olmadığını gösteren cihazların listesini gösterir.

- **Programlama komutları**: son 24 saat içinde programlama komutları gönderen cihazların listesini gösterir.

- **Uzaktan erişim**: uzak kaynaklara son 24 saat içinde eriştiği cihazların listesini gösterir.

:::image type="content" source="media/how-to-generate-reports/reports-view.png" alt-text="Raporlar görünümünün ekran görüntüsü.":::

Şirket içi yönetim konsolundan algılayıcıyı seçtiğinizde, bu algılayıcı üzerinde yapılandırılan tüm özel raporlar rapor listesinde görüntülenir. Her algılayıcı için, bu algılayıcı üzerinde yapılandırılmış bir varsayılan rapor veya özel rapor oluşturabilirsiniz.

Bir rapor oluşturmak için:

1. Sol bölmede **raporlar**' ı seçin. **Raporlar** penceresi görüntülenir.

2. **Algılayıcılar** açılan listesinden, raporu oluşturmak istediğiniz algılayıcıyı seçin.

   :::image type="content" source="media/how-to-generate-reports/sensor-drop-down-list.png" alt-text="Algılayıcılar görünümünün ekran görüntüsü.":::

3. Sağ açılan listeden, oluşturmak istediğiniz raporu seçin.

4. Rapor sonuçlarının bir PDF 'sini oluşturmak için öğesini seçin :::image type="icon" source="media/how-to-generate-reports/pdf-report-icon.png" border="false"::: .
