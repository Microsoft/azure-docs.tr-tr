---
title: Logic Apps kullanarak Azure Application Insights süreçlerini otomatikleştirin.
description: Mantıksal uygulamanıza Application Insights bağlayıcısını ekleyerek tekrarlanabilir işlemleri nasıl hızlı bir şekilde otomatikleştirebileceğinizi öğrenin.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 03/11/2019
ms.openlocfilehash: 8211598071d0835a32f9e25cfcf4e34576702770
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72677613"
---
# <a name="automate-application-insights-processes-by-using-logic-apps"></a>Logic Apps kullanarak Application Insights süreçlerini otomatikleştirin

Hizmetinizin düzgün çalışıp çalışmadığını denetlemek için telemetri verilerinizde aynı sorguları sürekli olarak çalıştırıp görmenizde emin misiniz? Eğilimleri ve anormallikleri bulmak ve sonra kendi iş akışlarınızı oluşturmak için bu sorguları otomatikleştirmeyi mi düşünüyorsunuz? Logic Apps için Azure Application Insights Bağlayıcısı, bu amaçla doğru araçtır.

Bu tümleştirmeyle, tek bir kod satırı yazmadan çok sayıda işlemi otomatikleştirebilirsiniz. Application Insights sürecini hızla otomatikleştirmek için Application Insights bağlayıcısıyla bir mantıksal uygulama oluşturabilirsiniz. 

Ek eylemleri de ekleyebilirsiniz. Azure App Service Logic Apps özelliği yüzlerce eylemi kullanılabilir hale getirir. Örneğin, bir mantıksal uygulama kullanarak, otomatik olarak bir e-posta bildirimi gönderebilir veya Azure DevOps 'da bir hata oluşturabilirsiniz. Mantıksal uygulamanızı oluşturma sürecini hızlandırmaya yardımcı olması için kullanılabilen birçok [şablondan](https://docs.microsoft.com/azure/logic-apps/logic-apps-use-logic-app-templates) birini de kullanabilirsiniz. 

## <a name="create-a-logic-app-for-application-insights"></a>Application Insights için bir mantıksal uygulama oluşturma

Bu öğreticide, bir Web uygulamasının verilerinde öznitelikleri gruplandırmak için Analytics oto kümesi algoritmasını kullanan bir mantıksal uygulama oluşturmayı öğreneceksiniz. Akış, sonuçları otomatik olarak e-posta ile gönderir ve Application Insights analizlerini nasıl kullanabileceğinizi ve Logic Apps birlikte nasıl kullanabileceğinizi gösteren bir örnektir. 

### <a name="step-1-create-a-logic-app"></a>1\. Adım: mantıksal uygulama oluşturma
1. [Azure Portal](https://portal.azure.com)’ında oturum açın.
1. **Kaynak oluştur ' a**tıklayın, **Web ve mobil**' yi seçin ve ardından **mantıksal uygulama**' yı seçin.

    ![Yeni mantıksal uygulama penceresi](./media/automate-with-logic-apps/1createlogicapp.png)

### <a name="step-2-create-a-trigger-for-your-logic-app"></a>2\. Adım: mantıksal uygulamanız için bir tetikleyici oluşturma
1. **Logic App Designer** penceresinde, **ortak bir tetikleyiciden başla**' nın altında **yinelenme**' yi seçin.

    ![Mantıksal uygulama Tasarımcısı penceresi](./media/automate-with-logic-apps/2logicappdesigner.png)

1. **Aralık** kutusunda **1** ve sonra**Sıklık** kutusu ' nu, **gün**' yı seçin.

    ![Logic App Designer "yinelenme" penceresi](./media/automate-with-logic-apps/3recurrence.png)

### <a name="step-3-add-an-application-insights-action"></a>3\. Adım: Application Insights eylem ekleme
1. **Yeni adım**' a tıklayın.

1. **Eylem seçin** arama kutusuna **Azure Application Insights**yazın.

1. **Eylemler**' ın altında **Azure Application Insights, analiz sorgusunu görselleştirin**' i tıklatın.

    ![Logic App Designer "eylem seçin" penceresi](./media/automate-with-logic-apps/4visualize.png)

### <a name="step-4-connect-to-an-application-insights-resource"></a>4\. Adım: Application Insights kaynağına bağlanma

Bu adımı gerçekleştirmek için, kaynağınız için bir uygulama KIMLIĞI ve bir API anahtarı gerekir. Aşağıdaki diyagramda gösterildiği gibi bunları Azure portal alabilirsiniz:

![Azure portal uygulama KIMLIĞI](./media/automate-with-logic-apps/5apiaccess.png)

![Azure portal uygulama KIMLIĞI](./media/automate-with-logic-apps/6apikey.png)

Bağlantınız, uygulama KIMLIĞI ve API anahtarı için bir ad girin.

![Logic App Designer akış bağlantısı penceresi](./media/automate-with-logic-apps/7connection.png)

### <a name="step-5-specify-the-analytics-query-and-chart-type"></a>5\. Adım: analiz sorgusunu ve grafik türünü belirtme
Aşağıdaki örnekte sorgu, son gün içindeki başarısız istekleri seçer ve bunları işlemin bir parçası olarak oluşan özel durumlarla ilişkilendirir. Analytics, başarısız istekleri operation_Id tanımlayıcısına göre karşılıklı olarak ilişkilendirir. Sorgu daha sonra sonuçları, oto küme algoritmasını kullanarak kesimler. 

Kendi sorgularınızı oluştururken, bunları akışınıza eklemeden önce analizler üzerinde düzgün çalıştığını doğrulayın.

1. **Sorgu** kutusuna aşağıdaki analiz sorgusunu ekleyin:

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

1. **Grafik türü** kutusunda **HTML tablosu**' nu seçin.

    ![Analiz sorgu yapılandırma penceresi](./media/automate-with-logic-apps/8query.png)

### <a name="step-6-configure-the-logic-app-to-send-email"></a>6\. Adım: mantıksal uygulamayı e-posta gönderecek şekilde yapılandırma

1. **Yeni adım**' a tıklayın.

1. Arama kutusuna **Office 365 Outlook**yazın.

1. **Office 365 Outlook-e-posta gönder ' e**tıklayın.

    ![Office 365 Outlook seçimi](./media/automate-with-logic-apps/9sendemail.png)

1. **E-posta gönder** penceresinde şunları yapın:

   a. Alıcının e-posta adresini yazın.

   b. E-posta için bir konu yazın.

   c. **Gövde** kutusunda herhangi bir yere tıklayın ve ardından sağ tarafta açılan dinamik Içerik menüsünde **gövde**' yi seçin.
    
   d. **Yeni parametre Ekle** açılan düğmesine tıklayın ve ekler ' i ve HTML ' yi seçin.

      ![Office 365 Outlook yapılandırması](./media/automate-with-logic-apps/10emailbody.png)

      ![Office 365 Outlook yapılandırması](./media/automate-with-logic-apps/11emailparameter.png)

1. Dinamik içerik menüsünde şunları yapın:

    a. **Ek adı**' nı seçin.

    b. **Ek içeriğini**seçin.
    
    c. Bu **HTML** kutusunda **Evet**' i seçin.

      ![Office 365 e-posta Yapılandırması ekranı](./media/automate-with-logic-apps/12emailattachment.png)

### <a name="step-7-save-and-test-your-logic-app"></a>7\. Adım: mantıksal Uygulamanızı kaydetme ve test etme
* Değişikliklerinizi kaydetmek için **Kaydet** ' e tıklayın.

Tetikleyicinin mantıksal uygulamayı çalıştırmasını bekleyebilir veya **Çalıştır**' ı seçerek mantıksal uygulamayı hemen çalıştırabilirsiniz.

![Mantıksal uygulama oluşturma ekranı](./media/automate-with-logic-apps/13save.png)

Mantıksal uygulamanız çalıştığında, e-posta listesinde belirttiğiniz alıcılar aşağıdakine benzer bir e-posta alır:

![Mantıksal uygulama e-posta iletisi](./media/automate-with-logic-apps/flow9.png)

## <a name="next-steps"></a>Sonraki adımlar

- [Analiz sorguları](../../azure-monitor/log-query/get-started-queries.md)oluşturma hakkında daha fazla bilgi edinin.
- [Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps) hakkında daha fazla bilgi edinin.



<!--Link references-->





