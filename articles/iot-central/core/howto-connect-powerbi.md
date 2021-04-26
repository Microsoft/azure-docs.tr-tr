---
title: Azure IoT Central verilerinizi Power BI panosunda görselleştirin | Microsoft Docs
description: IoT Central verilerinizi görselleştirmek ve analiz etmek için Azure IoT Central Power BI çözümünü kullanın.
ms.service: iot-central
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 10/4/2019
ms.topic: conceptual
ms.openlocfilehash: ea4a47f1ba3eac39820e839a10330840f57afe42
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105629079"
---
# <a name="visualize-and-analyze-your-azure-iot-central-data-in-a-power-bi-dashboard"></a>Azure IoT Central verilerinizi Power BI panosunda görselleştirin ve çözümleyin

*Bu konu, Yöneticiler ve çözüm geliştiricileri için geçerlidir.*

> [!Note] 
> Bu çözüm [eski veri dışa aktarma özelliklerini](./howto-export-data-legacy.md)kullanır. En son veri dışa aktarma kullanarak Power BI bağlanma hakkında daha fazla yönergeler için güncel kalın.

:::image type="content" source="media/howto-connect-powerbi/iot-continuous-data-export.png" alt-text="Power BI çözümü işlem hattı":::

IoT cihazlarınızın performansını izlemek için güçlü bir Power BI panosu oluşturmak üzere Azure IoT Central v3 için Power BI çözümünü kullanın. Power BI panonuzda şunları yapabilirsiniz:

- Cihazların zaman içinde ne kadar veri gönderdiğini izleme
- Farklı telemetri akışları arasında veri birimlerini karşılaştırın
- Belirli cihazlar tarafından gönderilen verilere filtre uygula
- Bir tabloda en son telemetri verilerini görüntüleme

Bu çözüm, [eski veri dışa aktarma](./howto-export-data-legacy.md) Azure Blob Storage hesabınızdan verileri okuyan bir işlem hattı oluşturur. İşlem hattı, verileri işlemek ve dönüştürmek için Azure Işlevleri, Azure Data Factory ve Azure SQL veritabanı 'nı kullanır. verileri, bir PBIX dosyası olarak yüklediğiniz bir Power BI raporunda görselleştirebilir ve çözümleyebilirsiniz. Tüm kaynaklar Azure aboneliğinizde oluşturulur, böylece her bileşeni gereksinimlerinize uyacak şekilde özelleştirebilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

Bu nasıl yapılır kılavuzundaki adımları tamamlayabilmeniz için etkin bir Azure aboneliğine ihtiyacınız vardır. Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

Çözümü ayarlamak için aşağıdaki kaynaklar gereklidir:

- Sürüm 3 IoT Central uygulaması. Uygulamanızın sürümünü nasıl denetleyeceğinizi öğrenmek için, bkz. [uygulamanız hakkında](./howto-get-app-info.md). IoT Central uygulama oluşturmayı öğrenmek için bkz. [Azure IoT Central uygulaması oluşturma](./quick-deploy-iot-central.md).
- Telemetri, cihaz ve cihaz şablonlarını Azure Blob depolamaya aktarmak üzere yapılandırılan, eski sürekli veri dışa aktarma. Daha fazla bilgi için bkz. [eski veri dışa aktarma belgeleri](howto-export-data-legacy.md).
  - Yalnızca IoT Central uygulamanızın blob kapsayıcısına veri dışarı aktarıldığınızdan emin olun.
  - [CIHAZLARıNıZıN JSON kodlu iletiler gönderilmesi gerekir](../../iot-hub/iot-hub-devguide-messages-d2c.md). Cihazların `contentType:application/JSON` `contentEncoding:utf-8` `contentEncoding:utf-16` `contentEncoding:utf-32` ileti sistemi özelliklerinde ve veya ya da belirtmesi gerekir.
- Power BI Desktop (en son sürüm). Bkz. [Power BI İndirmeleri](https://powerbi.microsoft.com/downloads/).
- Power BI Pro (panoyu başkalarıyla paylaşmak istiyorsanız).

> [!NOTE]
> Sürüm 2 IoT Central uygulaması kullanıyorsanız, bkz. Azure IoT Central verilerinizi önceki sürümler belge sitesinde [bir Power BI panosunda görselleştirme ve çözümleme](/previous-versions/azure/iot-central/core/howto-connect-powerbi) .

## <a name="install"></a>Yükleme

İşlem hattını ayarlamak için **Microsoft AppSource** sitesindeki [Azure IoT Central v3 için Power BI çözümü](https://appsource.microsoft.com/product/web-apps/iot-central.power-bi-solution-iot-central) sayfasına gidin. **Şimdi al**' ı seçin ve yönergeleri izleyin.

PBIX dosyasını açtığınızda, sayfayı okuyun ve kapak sayfasındaki yönergeleri izleyin. Bu yönergeler, raporunuzu SQL veritabanınıza nasıl bağlayacağınız açıklanır.

## <a name="report"></a>Rapor

PBIX dosyasında **cihazlar ve telemetri** raporu bulunur ve cihazlar tarafından gönderilen telemetrinin geçmiş bir görünümünü gösterir. Farklı telemetri türlerinin bir dökümünü sağlar ve ayrıca cihazlar tarafından gönderilen en son Telemetriyi gösterir.

:::image type="content" source="media/howto-connect-powerbi/report.png" alt-text="Power BI cihazlar ve telemetri raporu":::

## <a name="pipeline-resources"></a>İşlem hattı kaynakları

Azure portal işlem hattını oluşturan tüm Azure kaynaklarına erişebilirsiniz. Tüm kaynaklar, işlem hattını ayarlarken oluşturduğunuz kaynak grubunda bulunur.

:::image type="content" source="media/howto-connect-powerbi/azure-deployment.png" alt-text="Kaynak grubunun Azure portal görünümü":::

Aşağıdaki listede, ardışık düzendeki her bir kaynağın rolü açıklanmaktadır:

### <a name="azure-functions"></a>Azure İşlevleri

Azure Işlevi uygulaması, BLOB depolama alanına yeni bir dosya yazdığında IoT Central her seferinde tetiklenir. İşlevler, Azure Data Factory kullandığı ara SQL tablolarını doldurmak için telemetri, cihaz ve cihaz şablonları Bloblarından veri ayıklar.

### <a name="azure-data-factory"></a>Azure Data Factory

Azure Data Factory, SQL veritabanı 'na bağlı hizmet olarak bağlanır. Verileri işlemek ve analiz tablolarında depolamak için saklı yordamları çalıştırır.

Azure Data Factory, en son veri toplu iş kümesini SQL tablolarına (örneğin, atlayan **pencere tetikleyicisi** için en az sayı) yüklenecek şekilde dönüştürmek üzere 15 dakikada bir çalışır.

### <a name="azure-sql-database"></a>Azure SQL Veritabanı

Azure Data Factory, Power BI için bir dizi analiz tablosu oluşturur. Bu şemaları Power BI araştırabilir ve kendi görselleştirmelerinizi oluşturmak için kullanabilirsiniz.

## <a name="estimated-costs"></a>Tahmini maliyetler

Microsoft AppSource sitesindeki [Azure IoT Central v3 sayfasına yönelik Power BI çözümü](https://appsource.microsoft.com/product/web-apps/iot-central.power-bi-solution-iot-central) , dağıttığınız kaynaklar için bir maliyet tahmin aracı bağlantısı içerir.

## <a name="next-steps"></a>Sonraki adımlar

Artık Power BI verilerinizi görselleştirmeyi öğrendiğinize göre, önerilen sonraki adım [cihazların nasıl yönetileceğini](howto-manage-devices.md)öğrenmektir.
