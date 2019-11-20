---
title: Microsoft Flow ile Azure Application Insights süreçlerini otomatikleştirin
description: Application Insights bağlayıcısını kullanarak tekrarlanabilir işlemleri hızlı bir şekilde otomatikleştirmek için Microsoft Flow nasıl kullanabileceğinizi öğrenin.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 08/29/2019
ms.openlocfilehash: ff0896498c0270b8eb43b762228916985f924def
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72678316"
---
# <a name="automate-azure-application-insights-processes-with-the-connector-for-microsoft-flow"></a>Microsoft Flow için bağlayıcı ile Azure Application Insights süreçlerini otomatikleştirin

Hizmetinizin düzgün çalışıp çalışmadığını denetlemek için telemetri verilerinizde aynı sorguları sürekli olarak çalıştırıp görmenizde emin misiniz? Eğilimleri ve anormallikleri bulmak ve sonra kendi iş akışlarınızı oluşturmak için bu sorguları otomatikleştirmeyi mi düşünüyorsunuz? Microsoft Flow için Azure Application Insights Bağlayıcısı, bu amaçlar için doğru araçtır.

Bu tümleştirmeyle, artık tek bir kod satırı yazmadan çok sayıda işlemi otomatikleştirebilirim. Bir Application Insights eylemi kullanarak akış oluşturduktan sonra akış, Application Insights Analytics sorgunuzu otomatik olarak çalıştırır.

Ek eylemleri de ekleyebilirsiniz. Microsoft Flow yüzlerce eylemi kullanılabilir hale getirir. Örneğin, otomatik olarak bir e-posta bildirimi göndermek veya Azure DevOps 'da hata oluşturmak için Microsoft Flow kullanabilirsiniz. Microsoft Flow için bağlayıcı için kullanılabilen birçok [şablondan](https://ms.flow.microsoft.com/connectors/shared_applicationinsights/?slug=azure-application-insights) birini de kullanabilirsiniz. Bu şablonlar akış oluşturma işlemini hızlandırır.

<!--The Application Insights connector also works with [Azure Power Apps](https://powerapps.microsoft.com/) and [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/?v=17.23h). -->

## <a name="create-a-flow-for-application-insights"></a>Application Insights için akış oluşturma

Bu öğreticide, bir Web uygulamasının verilerinde öznitelikleri gruplandırmak için Analytics oto kümesi algoritmasını kullanan bir akış oluşturmayı öğreneceksiniz. Akış, sonuçları otomatik olarak e-posta ile gönderir ve Microsoft Flow ve Application Insights analizlerini birlikte nasıl kullanabileceğinizi gösteren bir örnektir.

### <a name="step-1-create-a-flow"></a>1\. Adım: akış oluşturma

1. [Microsoft Flow](https://flow.microsoft.com)oturum açın ve **Akışlarım**' ı seçin.
2. **Boş**' a, sonra da **Yeni** ' ye tıklayın.

    ![Zamanlanan boş kaynaktan yeni akış oluştur](./media/automate-with-flow/1-create.png)

### <a name="step-2-create-a-trigger-for-your-flow"></a>2\. Adım: akışınız için bir tetikleyici oluşturma

1. Açılan pencerede **zamanlanan bir akış oluşturun**, akışlarınızın adını ve akışınızı ne sıklıkta çalıştırmak istediğinizi doldurun.

    ![Sıklık ve aralığa girerek zamanlama tekrarını ayarlama](./media/automate-with-flow/2-schedule.png)

1. **Oluştur**’a tıklayın.

### <a name="step-3-add-an-application-insights-action"></a>3\. Adım: Application Insights eylem ekleme

1. **Application Insights**arayın.
2. **Azure Application Insights, analiz sorgusu Görselleştir**' e tıklayın.

    ![Bir eylem seçin: Azure Application Insights analiz sorgusunu görselleştirin](./media/automate-with-flow/3-visualize.png)

3. **Yeni adım**'ı seçin.

### <a name="step-4-connect-to-an-application-insights-resource"></a>4\. Adım: Application Insights kaynağına bağlanma

Bu adımı gerçekleştirmek için, kaynağınız için bir uygulama KIMLIĞI ve bir API anahtarı gerekir. Aşağıdaki diyagramda gösterildiği gibi bunları Azure portal alabilirsiniz:

![Azure portal uygulama KIMLIĞI](./media/automate-with-flow/5apiaccess.png)

![Azure portal API anahtarı](./media/automate-with-flow/6apikey.png)

Uygulama KIMLIĞI ve API anahtarı ile birlikte bağlantınız için bir ad sağlayın.

   ![Microsoft Flow bağlantı penceresi](./media/automate-with-flow/4-connection.png)

Bağlantı kutusu doğru görünmüyorsa ve bunun yerine sorguyu girmeye devam ederseniz, kutunun sağ üst köşesindeki üç noktaya tıklayın. Ardından bağlantılarımı seçin veya mevcut bir tane kullanın.

**Oluştur**’a tıklayın.

### <a name="step-5-specify-the-analytics-query-and-chart-type"></a>5\. Adım: analiz sorgusunu ve grafik türünü belirtme
Bu örnek sorgu son gün içindeki başarısız istekleri seçer ve bunları işlemin bir parçası olarak oluşan özel durumlarla ilişkilendirir. Analytics bunları operation_Id tanımlayıcısına göre ilişkilendirir. Sorgu daha sonra sonuçları, oto küme algoritmasını kullanarak kesimler.

Kendi sorgularınızı oluştururken, bunları akışınıza eklemeden önce analizler üzerinde düzgün çalıştığını doğrulayın.

- Aşağıdaki analiz sorgusunu ekleyin ve HTML tablosu grafik türünü seçin. Sonra **yeni adım**' ı seçin.

    ```
    requests
    | where timestamp > ago(1d)
    | where success == "False"
    | project name, operation_Id
    | join ( exceptions
        | project problemId, outerMessage, operation_Id
    ) on operation_Id
    | evaluate autocluster()
    ```
    
    ![Analiz sorgu yapılandırma penceresi](./media/automate-with-flow/5-query.png)

### <a name="step-6-configure-the-flow-to-send-email"></a>6\. Adım: akışı e-posta gönderecek şekilde yapılandırma

1. **Office 365 Outlook**için arama yapın.
2. **Office 365 Outlook-e-posta gönder ' e**tıklayın.

    ![Office 365 Outlook seçim penceresi](./media/automate-with-flow/6-outlook.png)

1. **E-posta gönder** penceresinde:

   a. Alıcının e-posta adresini yazın.

   b. E-posta için bir konu yazın.

   c. **Gövde** kutusunda herhangi bir yere tıklayın ve ardından sağ tarafta açılan dinamik Içerik menüsünde **gövde**' yi seçin.

   e. **Gelişmiş seçenekleri göster** ' i seçin

1. Dinamik içerik menüsünde:

    a. **Ek adı**' nı seçin.

    b. **Ek içeriğini**seçin.
    
    c. Bu **HTML** kutusunda **Evet**' i seçin.

    ![Office 365 Outlook yapılandırması](./media/automate-with-flow/7-email.png)

### <a name="step-7-save-and-test-your-flow"></a>7\. Adım: akışınızı kaydetme ve test etme

**Kaydet** düğmesine tıklayın.

Tetikleyicinin bu eylemi çalıştırmasını bekleyebilir veya üst kısımdaki **![beaker test simgesine ](./media/automate-with-flow/testicon.png) tıklayabilirsiniz** .

**Test**seçildikten sonra:

1. **Tetikleme eylemini yapacağım ' ı**seçin.
2. **Akış Çalıştır**' ı seçin.

Akış çalıştığında, e-posta listesinde belirttiğiniz alıcılar aşağıdaki gibi bir e-posta iletisi alır.

![Örnek e-posta](./media/automate-with-flow/flow9.png)

## <a name="next-steps"></a>Sonraki adımlar

- [Analiz sorguları](../../azure-monitor/log-query/get-started-queries.md)oluşturma hakkında daha fazla bilgi edinin.
- [Microsoft Flow](https://ms.flow.microsoft.com)hakkında daha fazla bilgi edinin.

<!--Link references-->