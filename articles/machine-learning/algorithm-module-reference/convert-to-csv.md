---
title: "CSV 'ye Dönüştür: modül başvurusu"
titleSuffix: Azure Machine Learning
description: Bir veri kümesini daha sonra yeniden kullanılabilecek bir CSV dosyasına dönüştürmek için Azure Machine Learning tasarımcısında CSV 'ye dönüştürme modülünü nasıl kullanacağınızı öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: cc58689e30e9b03e490c0871f3decd50372371fa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "93421932"
---
# <a name="convert-to-csv-module"></a>CSV modülüne Dönüştür

Bu makalede Azure Machine Learning tasarımcısında bir modül açıklanmaktadır.

Bir veri kümesini, R veya Python betik modülleriyle yüklenebilen, verilelebilecek veya paylaşılabilen bir CSV biçimine dönüştürmek için bu modülü kullanın.

### <a name="more-about-the-csv-format"></a>CSV biçimi hakkında daha fazla bilgi 

"Virgülle ayrılmış değerler" için temsil eden CSV biçimi, birçok harici makine öğrenimi aracı tarafından kullanılan bir dosya biçimidir. CSV, R veya Python gibi açık kaynak dillerle çalışırken ortak bir değişim biçimidir.

Azure Machine Learning ' de çalışmalarınızın çoğunu olsanız bile, veri kümenizi dış araçlar 'da kullanmak üzere CSV 'ye dönüştürmeyi yararlı bulabileceğiniz zamanlar olabilir. Örnek:

+ Excel ile açmak veya ilişkisel bir veritabanına içeri aktarmak için CSV dosyasını indirin.  
+ CSV dosyasını bulut depolama alanına kaydedin ve görselleştirmeler oluşturmak için Power BI adresinden bağlanın.  
+ Verileri R ve Python 'da kullanmak üzere hazırlamak için CSV biçimini kullanın. 

Bir veri kümesini CSV 'ye dönüştürdüğünüzde, CSV Azure ML çalışma alanınıza kaydedilir. Azure Storage yardımcı programını kullanarak dosyayı doğrudan açabilir ve kullanabilirsiniz. Ayrıca, **CSV 'ye Dönüştür modülüne Dönüştür** ' i seçip çıktıyı görüntülemek için sağ paneldeki **çıktılar** sekmesinin altındaki histogram simgesini seçin. CSV 'yi sonuçlar klasöründen yerel bir dizine indirebilirsiniz.  

## <a name="how-to-configure-convert-to-csv"></a>CSV 'ye dönüştürme yapılandırma


1.  CSV 'ye dönüştürme modülüne işlem hattınızı ekleyin. Bu modülü, tasarımcıda **veri dönüştürme** grubunda bulabilirsiniz. 

2. Veri kümesini çıkaran herhangi bir modüle bağlayın.   
  
3.  İşlem hattını gönderme.

### <a name="results"></a>Sonuçlar
  

**CSV 'ye Dönüştür**' in sağ panelindeki **çıktılar** sekmesini seçin ve **bağlantı noktası çıkışları** altında Bu simgelerden birini seçin.  

+ **Veri kümesini kaydet**: simgeyi SEÇIN ve CSV dosyasını ayrı bir veri kümesi olarak YENIDEN Azure ML çalışma alanına kaydedin. Veri kümesi, modül ağacında bir modül olarak veri **kümeleri** bölümünün altında bulunabilir.

 + **Çıktıyı görüntüle**: göz simgesini seçin ve **Results_dataset** klasöre göz atarak data.csv dosyasını indirmek için yönergeleri izleyin.

## <a name="next-steps"></a>Sonraki adımlar

Azure Machine Learning için [kullanılabilen modül kümesine](module-reference.md) bakın. 