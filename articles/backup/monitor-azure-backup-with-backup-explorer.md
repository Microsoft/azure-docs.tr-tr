---
title: Yedekleme Gezgini ile yedeklemelerinizi izleme
description: Bu makalede, kasaların, aboneliklerde, bölgelerde ve kiracılar genelinde yedeklemelerin gerçek zamanlı olarak izlenmesini gerçekleştirmek için yedekleme Gezgini 'nin nasıl kullanılacağı açıklanır.
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: 78d3117d363059b6a298740f7b5daa31e10751c8
ms.sourcegitcommit: af6eba1485e6fd99eed39e507896472fa930df4d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/04/2021
ms.locfileid: "106294068"
---
# <a name="monitor-your-backups-with-backup-explorer"></a>Yedekleme Gezgini ile yedeklemelerinizi izleme

Kuruluşlar buluta daha fazla makine yedekleştiğinde, bu yedeklemeleri verimli bir şekilde izlemek daha fazla önemli hale gelir. Başlamak için en iyi yol, işlem bilgilerini büyük bir emlak genelinde görüntülemek için merkezi bir konum kullanmaktır.

Yedekleme Gezgini, Azure Backup müşterilerine bu tek, merkezi bir konum sağlayan yerleşik bir Azure Izleyici çalışma kitabıdır. Yedekleme Gezgini, Azure 'da, kapsayıcı kiracılar, konumlar, abonelikler, kaynak grupları ve kasaların bulunduğu tüm yedekleme genelinde işlem etkinliklerini izlemenize yardımcı olur. Büyük ölçüde, yedekleme Gezgini aşağıdaki özellikleri sağlar:

* **Ölçekli perspektif**: yedekleme öğelerinin, işlerin, uyarıların, ilkelerin ve tüm emlak genelinde yedekleme için henüz yapılandırılmamış kaynakların toplanmış bir görünümünü alın.
* **Detaya gitme Analizi**: her bir iş, uyarı, ilke ve yedekleme öğesi hakkında ayrıntılı bilgileri tek bir yerde görüntüleyin.
* Eyleme dönüştürülebilir **arabirimler**: bir sorunu tanımladıktan sonra ilgili yedekleme öğesine veya Azure kaynağına sorunsuz bir şekilde giderek bu sorunu çözebilirsiniz.

Bu yetenekler, Azure Kaynak Grafiği ve Azure Izleyici çalışma kitapları ile yerel tümleştirme tarafından kullanıma hazır olarak sağlanır.

> [!NOTE]
>
> * Yedekleme Gezgini Şu anda yalnızca Azure sanal makineler (VM) verileri için kullanılabilir.
> * Yedekleme Gezgini, son 7 gün içinde yedeklemeleriniz hakkındaki bilgileri (en fazla) görüntülemek için bir işlemsel Pano olmak üzere tasarlanmıştır.
> * Yedekleme Gezgini Şu anda ulusal bulutlarda desteklenmiyor.
> * Şu anda, yedekleme Gezgini şablonunun özelleştirilmesi desteklenmez.
> * Azure Kaynak Grafiği verilerinde özel geliştirmeler yazmak önermiyoruz.
> * Yedekleme Gezgini Şu anda en fazla 1000 abonelik (kiracılar arasında) üzerinde yedeklemeleri izlemenize olanak sağlar.

## <a name="get-started"></a>başlarken

Kurtarma Hizmetleri kasalarınıza gidip **genel bakış** bölmesinde **yedek gezgin** bağlantısını seçerek yedekleme Gezgini 'ne erişebilirsiniz.

![Kasa hızlı bağlantısı](media/backup-azure-monitor-with-backup-explorer/vault-quick-link.png)

Bağlantıyı seçmek, erişiminiz olan tüm kasaların ve aboneliklerde toplu bir görünüm sağlayan yedekleme Gezgini 'ni açar. Azure açık bir hesabı kullanıyorsanız, erişiminiz olan tüm kiracılar genelinde verileri görüntüleyebilirsiniz. Daha fazla bilgi için, bu makalenin sonundaki "çapraz kiracı görünümleri" bölümüne bakın.

![Yedekleme Gezgini giriş sayfası](media/backup-azure-monitor-with-backup-explorer/explorer-landing-page.png)

## <a name="backup-explorer-use-cases"></a>Yedekleme Gezgini kullanım örnekleri

Yedekleme Gezgini, her biri belirli bir yedekleme yapıtı (örneğin, bir yedekleme öğesi, iş veya ilke) hakkında ayrıntılı bilgi sağlayan birden çok sekme görüntüler. Bu bölümde, sekmelerin her birine ilişkin kısa bir genel bakış sunulmaktadır. Videolar, her yedekleme yapıtı için, kullanılabilir denetimlerin açıklamalarıyla birlikte örnek kullanım durumları sağlar.

### <a name="the-summary-tab"></a>Özet sekmesi

**Özet** sekmesi, yedeklemeniz için genel koşula hızlı bir bakış sağlar. Örneğin, korunan öğe sayısını, korumanın etkinleştirilmediği öğe sayısını veya son 24 saat içinde kaç iş başarılı olduğunu görüntüleyebilirsiniz.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nQYd]

### <a name="the-backup-items-tab"></a>Yedekleme öğeleri sekmesi

Her yedekleme öğelerinizi abonelik, kasa ve diğer özelliklere göre filtreleyebilir ve görüntüleyebilirsiniz. Bir yedekleme öğesinin adını seçerek söz konusu öğe için Azure bölmesini açabilirsiniz. Örneğin, tablosundan, son yedeklemenin *X* öğesi için başarısız olduğunu gözlemleyebilirsiniz. *X*' i seçerek öğenin **yedekleme** bölmesini açabilirsiniz, burada isteğe bağlı yedekleme işlemini tetikleyebilirsiniz.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nQYc]

### <a name="the-jobs-tab"></a>Işler sekmesi

Son 7 gün içinde tetiklenen tüm işlerin ayrıntılarını görüntülemek için **işler** sekmesini seçin. Burada *Iş işlemine*, *Iş durumuna* ve *hata koduna* göre filtreleyebilirsiniz (başarısız işler için).

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nOrh]

### <a name="the-alerts-tab"></a>Uyarılar sekmesi

Son 7 gün içinde kasalarınızda oluşturulan tüm uyarıların ayrıntılarını görüntülemek için **Uyarılar** sekmesini seçin. Uyarıları türe (*Yedekleme hatası* veya *geri yükleme hatası*), geçerli durum (*etkin* veya *Çözümlenmiş*) ve önem derecesi (*kritik*, *Uyarı* veya *bilgi*) göre filtreleyebilirsiniz. Ayrıca, Azure VM 'ye gidip tüm gerekli işlemleri gerçekleştirmeniz için bir bağlantı seçebilirsiniz.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nTxe]

### <a name="the-policies-tab"></a>Ilkeler sekmesi

Yedeklemeinizde oluşturulmuş tüm yedekleme ilkelerine ilişkin anahtar bilgileri görüntülemek için **ilkeler** sekmesini seçebilirsiniz. İlke tarafından belirtilen bekletme aralığı ve yedekleme sıklığı ile birlikte her ilkeyle ilişkili öğelerin sayısını görüntüleyebilirsiniz.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nLKV]

### <a name="the-backup-not-enabled-tab"></a>Yedekleme etkin değil sekmesi

Koruma gerektiren tüm makineler için yedekleme etkinleştirilmelidir. Yedekleme Gezgini ile, yedekleme yöneticileri bir kuruluştaki hangi makinelerin yedekleme tarafından henüz korunmuyor olduğunu hızlıca belirleyebilir. Bu bilgileri görüntülemek için **Yedekleme etkin değil** sekmesini seçin.

**Yedekleme etkin değil** bölmesinde, korumasız makinelerin listesini içeren bir tablo görüntülenir. Kuruluşunuz, üretim makinelerine ve test makinelerine veya çeşitli işlevlere sahip makinelere farklı Etiketler atayabilir. Her makine sınıfı ayrı bir yedekleme ilkesine ihtiyaç duyduğundan, etiketlere göre filtreleme, her birine özgü bilgileri görüntülemenize yardımcı olur. Herhangi bir makinenin adını seçtiğinizde, uygun bir yedekleme ilkesi uygulamayı seçebileceğiniz bu makinenin **yedekleme yapılandırma** bölmesine yönlendirilirsiniz.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nQXZ]

## <a name="export-to-excel"></a>Excel'e aktar

Herhangi bir tablo veya grafiğin içeriğini Excel elektronik tablosu olarak dışarı aktarabilirsiniz. İçerik, mevcut filtrelerinizin uygulandığı gibi olarak verilir. Ek tablo satırlarını dışarı aktarmak için, her sekmenin en üstündeki **sayfa başına satır** sayısı açılan listesini kullanarak sayfada görüntülenecek satır sayısını artırabilirsiniz.

## <a name="pin-to-the-dashboard"></a>Panoya sabitle

Azure portal panonuza sabitlemek için her bir tablonun veya grafiğin en üstünde "sabitle" simgesini seçebilirsiniz. Bu bilgilerin sabitlenmesi, sizin için en önemli bilgileri görüntüleyecek şekilde uyarlanmış bir özelleştirilmiş Pano oluşturmanıza yardımcı olur.

## <a name="cross-tenant-views"></a>Çapraz kiracı görünümleri

Çoklu kiracı ortamlarında abonelikler için temsilci erişimi olan bir Azure açık Kullanıcı kullanıyorsanız, varsayılan abonelik filtresini kullanabilirsiniz. Azure portal, ' ın sağ üst köşesindeki "filtre" simgesini seçerek verilerini görmek istediğiniz abonelikleri görüntüleyebilirsiniz. Bu özelliği kullandığınızda, yedek gezgin seçili aboneliklerinizde tüm kasaların bilgilerini toplar. Daha fazla bilgi edinmek için bkz. [Azure ışıklı kullanım nedir?](../lighthouse/overview.md).

## <a name="next-steps"></a>Sonraki adımlar

[Yedekleme verileriniz hakkında öngörü almak için Azure Izleyici 'yi nasıl kullanacağınızı öğrenin](./backup-azure-monitoring-use-azuremonitor.md)
