---
title: Azure Data Factory eşleme veri akışı yeni dal dönüştürmesi
description: Azure Data Factory eşleme veri akışı yeni dal dönüştürmesi
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: de8cb74d788e3ca7599f226e4204c4b09112e70c
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387210"
---
# <a name="azure-data-factory-mapping-data-flow-new-branch-transformation"></a>Azure Data Factory eşleme veri akışı yeni dal dönüştürmesi



![Dal seçenekleri](media/data-flow/menu.png "Menü")

Dallandırma, veri akışınızda geçerli veri akışını alacak ve bunu başka bir akışa çoğaltacaktır. Aynı veri akışına karşı birden çok işlem ve dönüşüm kümesi gerçekleştirmek için yeni dalı kullanın.

Örnek: veri akışınız, seçili sütun kümesi ve veri türü dönüştürmeleri içeren bir kaynak dönüşümüyle bulunur. Ardından, bu kaynağı hemen takip eden bir türetilmiş sütun yerleştirebilirsiniz. Türetilmiş sütununda, yeni bir "tam ad" alanı oluşturmak için adı ve soyadı birleştiren yeni bir alan oluşturdunuz.

Bu yeni akışı bir dizi dönüşümle ve havuz kümesiyle ve bu akışın farklı bir dönüştürme kümesiyle aynı verileri dönüştürebileceğiniz bir kopyasını oluşturmak için kullanabilirsiniz. Kopyalanmış verileri ayrı bir dalda dönüştürerek, daha sonra bu verileri ayrı bir konuma havuza alabilirsiniz.

> [!NOTE]
> "Yeni dal", yalnızca dallandırma yapmaya çalıştığınız geçerli konumdan sonraki bir dönüşüm varsa + dönüştürme menüsünde bir eylem olarak gösterilir. Yani, select öğesinden sonra başka bir dönüşüm ekleyene kadar burada bir "yeni dal" seçeneği görmezsiniz

![Öğede](media/data-flow/branch2.png "Şube 2")
