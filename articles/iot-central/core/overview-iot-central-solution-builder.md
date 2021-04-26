---
title: Azure IoT Central için çözüm oluşturma | Microsoft Docs
description: Azure IoT Central, IoT çözümlerinin oluşturulmasını kolaylaştıran bir IoT uygulama platformudur. Bu makalede, IoT Central ile tümleşik çözümler oluşturmaya genel bakış sunulmaktadır.
author: dominicbetts
ms.author: dobett
ms.date: 02/11/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: e762b8c2e2d7f72b89629c520560b205cedcd036
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107728568"
---
# <a name="iot-central-solution-builder-guide"></a>IoT Central çözüm Oluşturucu Kılavuzu

*Bu makale, çözüm oluşturucuları için geçerlidir.*

IoT Central bir uygulama, yaşam döngüsü boyunca milyonlarca cihazı izlemenize ve yönetmenize olanak sağlar. Bu kılavuz, Tümleşik çözümler oluşturmak için IoT Central kullanan çözüm oluşturuculara yöneliktir. IoT Central bir uygulama cihazları yönetmenizi, cihaz telemetrisini incelemenize ve diğer arka uç hizmetleriyle tümleştirietmenize olanak tanır.

Çözüm Oluşturucu:

- IoT Central Web Kullanıcı arabirimindeki panoları ve görünümleri yapılandırır.
- , Bağlı cihazlardan iş öngörüleri türetmede yerleşik kuralları ve analiz araçlarını kullanır.
- , Diğer arka uç hizmetleriyle IoT Central bütünleştirmek için veri dışa aktarma ve kural yeteneklerini kullanır.

## <a name="configure-dashboards-and-views"></a>Panoları ve görünümleri yapılandırma

IoT Central bir uygulama, operatörlerin uygulamayı görüntülemek ve uygulamayla etkileşim kurmak için kullandığı bir veya daha fazla panoya sahip olabilir. Bir çözüm Oluşturucusu olarak, Varsayılan panoyu özelleştirebilir ve özel panolar oluşturabilirsiniz:

- Özelleştirilmiş panoların bazı örneklerini görüntülemek için bkz. [sektör odaklı şablonlar](concepts-app-templates.md#industry-focused-templates).
- Panolar hakkında daha fazla bilgi edinmek için bkz. [birden çok pano oluşturma ve yönetme](howto-create-personal-dashboards.md) ve [uygulama panosunu yapılandırma](howto-add-tiles-to-your-dashboard.md).

Bir cihaz bir IoT Central bağladığında cihaz, cihaz türü için bir cihaz şablonuyla ilişkilendirilir. Bir cihaz şablonunda, bir işlecin tek tek cihazları yönetmek için kullandığı özelleştirilebilir görünümler vardır. Bir çözüm geliştiricisi olarak, bir cihaz türü için kullanılabilir görünümleri oluşturabilir ve özelleştirebilirsiniz. Daha fazla bilgi için bkz. [Görünüm ekleme](howto-set-up-template.md#add-views).

## <a name="use-built-in-rules-and-analytics"></a>Yerleşik kurallar ve çözümlemeler kullanın

Çözüm geliştiricisi, özelleştirilebilir eylemler çalıştıran bir IoT Central uygulamasına kurallar ekleyebilir. Kurallar, bir eylemin ne zaman çalıştırılacağını öğrenmek için bir cihazdan gelen verilere göre koşulları değerlendirir. Kurallar hakkında daha fazla bilgi için bkz.

- [Öğretici: Azure IoT Central uygulamanızda kural oluşturma ve bildirimleri ayarlama](tutorial-create-telemetry-rules.md)
- [Azure IoT Central'da kurallar üzerinde web kancası eylemleri oluşturma](howto-create-webhooks.md)
- [Birden çok eylemi bir veya daha fazla kuralla çalışacak şekilde gruplandırma](howto-use-action-groups.md)

IoT Central, bir işlecin bağlı cihazlardan akan verileri çözümlemek için kullanabileceği yerleşik analiz özellikleri vardır. Daha fazla bilgi edinmek için bkz. analiz [kullanarak cihaz verilerini çözümleme](howto-create-analytics.md).

## <a name="integrate-with-other-services"></a>Diğer hizmetlerle tümleştirme

Bir çözüm Oluşturucusu olarak, diğer hizmetle tümleştirilecek IoT Central içindeki veri dışa aktarma ve kural yeteneklerini kullanabilirsiniz. Daha fazla bilgi edinmek için şu makalelere bakın:

- [Veri dışarı aktarma kullanarak IoT verilerini bulut hedeflerine dışarı aktarma](howto-export-data.md)
- [IoT Central için veri dönüştürme](howto-transform-data.md)
- [Azure IoT Central uygulamanızı diğer bulut hizmetleriyle bütünleştirmek için iş akışlarını kullanma](howto-configure-rules-advanced.md)
- [Stream Analytics, Azure İşlevleri ve SendGrid kullanarak özel kurallarla Azure IoT Central’ın kapsamını genişletme](howto-create-custom-rules.md)
- [Azure Databricks kullanarak özel analizlerle Azure IoT Central’ın kapsamını genişletme](howto-create-custom-analytics.md)
- [Azure IoT Central verilerinizi Power BI panosunda görselleştirin ve çözümleyin](howto-connect-powerbi.md)

## <a name="next-steps"></a>Sonraki adımlar

IoT Central kullanma hakkında daha fazla bilgi edinmek istiyorsanız, önerilen sonraki adımlar, [Azure IoT Central uygulaması oluşturma](./quick-deploy-iot-central.md)ile başlayarak hızlı başlangıç adımlarını denemenize olanak sağlar.
