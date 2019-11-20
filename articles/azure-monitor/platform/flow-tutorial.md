---
title: Microsoft Flow ile Azure Izleyici günlük işlemlerini otomatikleştirme
description: Azure Log Analytics bağlayıcısını kullanarak tekrarlanabilir işlemleri hızlı bir şekilde otomatikleştirmek için Microsoft Flow nasıl kullanabileceğinizi öğrenin.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: MGoedtel
ms.author: bwren
ms.date: 09/29/2017
ms.openlocfilehash: c74ff0f4d23df8f906870c3810a699db254d70b4
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72932325"
---
# <a name="automate-azure-monitor-log-processes-with-the-connector-for-microsoft-flow"></a>Microsoft Flow için bağlayıcı ile Azure Izleyici günlük işlemlerini otomatikleştirme
[Microsoft Flow](https://ms.flow.microsoft.com) , çeşitli hizmetler için yüzlerce eylemi kullanarak otomatik iş akışları oluşturmanıza olanak tanır. Bir eylemden çıkış, farklı hizmetler arasında tümleştirme oluşturmanıza olanak sağlayan başka bir eylem için giriş olarak kullanılabilir.  Microsoft Flow için Azure Log Analytics Bağlayıcısı, Azure Izleyici 'deki bir Log Analytics çalışma alanındaki günlük sorguları tarafından alınan verileri içeren iş akışları oluşturmanıza olanak tanır.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Örneğin, Azure Izleyici günlük verilerini Office 365 ' den bir e-posta bildiriminde kullanmak için Microsoft Flow kullanabilir, Azure DevOps 'da bir hata oluşturabilir veya bir bolluk iletisi postalayabilirsiniz.  Bir iş akışını basit bir zamanlamaya göre veya bir e-posta ya da tweet alınması gibi bağlı bir hizmette bazı bir eylemden tetikleyebilirsiniz.  

Bu makaledeki öğreticide, bir Azure Izleyici günlük sorgusunun sonuçlarını otomatik olarak e-posta ile gönderen bir akış oluşturma, Microsoft Flow Log Analytics bağlayıcısını nasıl kullanabileceğinizi gösteren bir örnek gösterilmektedir. 


## <a name="step-1-create-a-flow"></a>1\. Adım: akış oluşturma
1. [Microsoft Flow](https://flow.microsoft.com)oturum açın ve **Akışlarım**' ı seçin.
2. **Boş + oluştur**' a tıklayın.

## <a name="step-2-create-a-trigger-for-your-flow"></a>2\. Adım: akışınız için bir tetikleyici oluşturma
1. **Yüzlerce bağlayıcı ve tetikleyici ara**' ya tıklayın.
2. Arama kutusuna **Schedule** yazın.
3. **Zamanlama**' yı seçin ve sonra **zamanlama-yinelenme**' yi seçin.
4. **Sıklık** kutusunda **gün** ' yı seçin ve **Aralık** kutusuna **1**yazın.<br><br>![Microsoft Flow tetikleyici iletişim kutusu](media/flow-tutorial/flow01.png)


## <a name="step-3-add-a-log-analytics-action"></a>3\. Adım: Log Analytics eylem ekleme
1. **+ Yeni adım**' a ve ardından **Eylem Ekle**' ye tıklayın.
2. **Log Analytics**arayın.
3. Azure Log Analytics ' e tıklayın **– sorgu çalıştırın ve sonuçları görselleştirin**.<br><br>sorgu penceresini ![Log Analytics Çalıştır](media/flow-tutorial/flow02.png)

## <a name="step-4-configure-the-log-analytics-action"></a>4\. Adım: Log Analytics eylemini yapılandırma

1. Çalışma alanınızın abonelik KIMLIĞI, kaynak grubu ve çalışma alanı adı dahil ayrıntılarını belirtin.
2. **Sorgu** penceresine aşağıdaki günlük sorgusunu ekleyin.  Bu yalnızca bir örnek sorgudur ve verileri döndüren herhangi bir diğer ile değiştirebilirsiniz.
   ```
    Event
    | where EventLevelName == "Error" 
    | where TimeGenerated > ago(1day)
    | summarize count() by Computer
    | sort by Computer
   ```

2. **Grafik türü**Için **HTML tablosu** ' nu seçin.<br><br>![Log Analytics eylemi](media/flow-tutorial/flow03.png)

## <a name="step-5-configure-the-flow-to-send-email"></a>5\. Adım: akışı, e-posta gönderecek şekilde yapılandırma

1. **Yeni adım**' a ve ardından **+ Eylem Ekle**' ye tıklayın.
2. **Office 365 Outlook**için arama yapın.
3. **Office 365 Outlook – e-posta gönder ' e**tıklayın.<br><br>![Office 365 Outlook seçim penceresi](media/flow-tutorial/flow04.png)

4. **Kime** penceresinde bir alıcının e-posta adresini **ve ilgili e**-postanın konusunu belirtin.
5. **Gövde** kutusunda herhangi bir yere tıklayın.  Önceki eylemlerden alınan değerlerle **dinamik içerik** penceresi açılır.  
6. **Gövde**seçin.  Bu, Log Analytics eyleminde sorgunun sonuçlarınızdan oluşur.
6. **Gelişmiş seçenekleri göster**' e tıklayın.
7. Bu **HTML** kutusunda **Evet**' i seçin.<br><br>![Office 365 e-posta yapılandırma penceresi](media/flow-tutorial/flow05.png)

## <a name="step-6-save-and-test-your-flow"></a>6\. Adım: akışınızı kaydetme ve test etme
1. **Flow adı** kutusunda, akışınız için bir ad ekleyin ve ardından **akış oluştur**' a tıklayın.<br><br>akışı ![kaydet](media/flow-tutorial/flow06.png)
2. Akış artık oluşturulur ve belirttiğiniz zamanlama bir günden sonra çalışır. 
3. Akışı hemen test etmek için **Şimdi Çalıştır** ' a tıklayıp Flow ' u **çalıştırın**.<br><br>![çalıştırma akışı](media/flow-tutorial/flow07.png)
3. Akış tamamlandığında, belirttiğiniz alıcının postasını kontrol edin.  Aşağıdakine benzer bir gövde içeren bir e-posta almış olmanız gerekir:<br><br>![Örnek e-posta](media/flow-tutorial/flow08.png)


## <a name="next-steps"></a>Sonraki adımlar

- [Azure izleyici 'de günlük sorguları](../log-query/log-query-overview.md)hakkında daha fazla bilgi edinin.
- [Microsoft Flow](https://ms.flow.microsoft.com)hakkında daha fazla bilgi edinin.



