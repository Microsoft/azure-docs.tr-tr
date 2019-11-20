---
title: "CSV 'ye Dönüştür: modül başvurusu"
titleSuffix: Azure Machine Learning
description: Bir veri kümesini, R veya Python betik modülleriyle yüklenebilen, verilelebilecek veya paylaşılabilen CSV biçimine dönüştürmek için Azure Machine Learning CSV 'ye dönüştürme modülünü nasıl kullanacağınızı öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: 999f78ab08e1a2c9dd6b28d853e49fbb559fab83
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73493842"
---
# <a name="convert-to-csv-module"></a>CSV modülüne Dönüştür

Bu makalede Azure Machine Learning tasarımcısında modül (Önizleme) açıklanmaktadır.

Bir veri kümesini, R veya Python betik modülleriyle yüklenebilen, verilelebilecek veya paylaşılabilen bir CSV biçimine dönüştürmek için bu modülü kullanın.

### <a name="more-about-the-csv-format"></a>CSV biçimi hakkında daha fazla bilgi 

"Virgülle ayrılmış değerler" için temsil eden CSV biçimi, birçok harici makine öğrenimi aracı tarafından kullanılan bir dosya biçimidir. CSV, R veya Python gibi açık kaynak dillerle çalışırken ortak bir değişim biçimidir.

Azure Machine Learning ' de çalışmalarınızın çoğunu olsanız bile, veri kümenizi dış araçlar 'da kullanmak üzere CSV 'ye dönüştürmeyi yararlı bulabileceğiniz zamanlar olabilir. Örneğin:

+ Excel ile açmak veya ilişkisel bir veritabanına içeri aktarmak için CSV dosyasını indirin.  
+ CSV dosyasını bulut depolama alanına kaydedin ve görselleştirmeler oluşturmak için Power BI adresinden bağlanın.  
+ Verileri R ve Python 'da kullanmak üzere hazırlamak için CSV biçimini kullanın. Doğrudan Python veya Jupyter Not defteri 'nden verilere erişmek için gereken kodu oluşturmak üzere modülün çıktısına sağ tıklayın. 

Bir veri kümesini CSV 'ye dönüştürdüğünüzde, dosya Azure ML çalışma alanınıza kaydedilir. Azure Storage yardımcı programını kullanarak dosyayı doğrudan açabilir ve kullanabilir veya modül çıktısına sağ tıklayıp CSV dosyasını bilgisayarınıza indirebilir veya R ya da Python kodunda kullanabilirsiniz.  

## <a name="how-to-configure-convert-to-csv"></a>CSV 'ye dönüştürme yapılandırma


1.  [CSV 'ye dönüştürme](./convert-to-csv.md) modülüne işlem hattınızı ekleyin. Bu modülü, tasarımcıda **veri biçimi dönüştürmeleri** grubunda bulabilirsiniz. 

2. Veri kümesini çıkaran herhangi bir modüle bağlayın.   
  
3.  İşlem hattını çalıştırma.

### <a name="results"></a>Sonuçlar
  

[CSV 'ye Dönüştür](./convert-to-csv.md)çıktısına çift tıklayın ve bu seçeneklerden birini belirleyin.  

 + **Sonuç veri kümesi-> indirme**: bir yerel klasöre KAYDEDEBILECEĞINIZ CSV biçimindeki verilerin bir kopyasını hemen açar. Bir klasör belirtmezseniz, varsayılan bir dosya adı uygulanır ve CSV dosyası yerel **indirmeler** kitaplığına kaydedilir.


 + **Sonuç veri kümesi-veri kümesi olarak kaydet >** : CSV dosyasını Azure ML çalışma alanına ayrı bir veri kümesi olarak geri kaydeder.

 + **Veri erişim kodu oluştur**: Azure ml, verileri Python kullanarak veya R kullanarak erişmeniz için iki kod kümesi oluşturur. Verilere erişmek için kod parçacığını uygulamanıza kopyalayın. (*Veri erişim kodu oluşturma yakında yakında sunulacaktır.* )

## <a name="next-steps"></a>Sonraki adımlar

Azure Machine Learning için [kullanılabilen modül kümesine](module-reference.md) bakın. 