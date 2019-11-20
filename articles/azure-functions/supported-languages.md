---
title: Azure Işlevlerinde desteklenen diller
description: Hangi dillerin desteklendiğini (GA) ve hangilerinin önizlemede olduğunu öğrenin.
author: ggailey777
manager: gwallace
ms.service: azure-functions
ms.topic: conceptual
ms.date: 08/02/2018
ms.author: glenga
ms.openlocfilehash: eac67c43915edb0aed273df40e7b08ca846062bf
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72802463"
---
# <a name="supported-languages-in-azure-functions"></a>Azure Işlevlerinde desteklenen diller

Bu makalede, Azure Işlevleri ile kullanabileceğiniz diller için sunulan destek düzeyleri açıklanmaktadır.

## <a name="levels-of-support"></a>Destek düzeyleri

Üç destek düzeyi vardır:

* **Genel olarak kullanılabilir (GA)** -üretim kullanımı için tam olarak desteklenir ve onaylanır.
* **Önizleme** -henüz desteklenmiyor, ancak gelecekte GA durumuna ulaşması bekleniyor.
* **Deneysel** -desteklenmez ve gelecekte bırakılmış olabilir; Son önizleme veya GA durumu garantisi yoktur.

## <a name="languages-by-runtime-version"></a>Çalışma zamanı sürümüne göre diller 

[Azure işlevleri çalışma zamanının iki sürümü](functions-versions.md) kullanılabilir. Aşağıdaki tabloda, her çalışma zamanı sürümünde hangi dillerin desteklendiği gösterilmektedir.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

### <a name="experimental-languages"></a>Deneysel diller

1\. x sürümündeki deneysel diller iyi ölçeklendirmez ve tüm bağlamaları desteklemez.

Sizin için herhangi bir resmi destek bulunmadığından, sizin kullandığınız her şey için deneysel özellikleri kullanmayın. Deneysel dillerle ilgili sorunlar için destek durumları açılmamalıdır. 

Sürüm 2. x çalışma zamanı deneysel dilleri desteklemez. Yeni diller için destek yalnızca dilin üretimde desteklenme sırasında eklenir. 

### <a name="language-extensibility"></a>Dil genişletilebilirliği

Sürüm 2. x ile başlayarak, çalışma zamanı [dil genişletilebilirliği](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Language-Extensibility)sunacak şekilde tasarlanmıştır. 2\. x çalışma zamanının JavaScript ve Java dilleri, bu genişletilebilirlik ile oluşturulmuştur.

## <a name="next-steps"></a>Sonraki adımlar

Desteklenen dillerde işlevleri geliştirme hakkında daha fazla bilgi edinmek için aşağıdaki kaynaklara bakın:

+ [C#sınıf kitaplığı Geliştirici Başvurusu](functions-dotnet-class-library.md)
+ [C#betik geliştirici başvurusu](functions-reference-csharp.md)
+ [Java geliştirici başvurusu](functions-reference-java.md)
+ [JavaScript geliştirici başvurusu](functions-reference-node.md)
+ [PowerShell geliştirici başvurusu](functions-reference-powershell.md)
+ [Python geliştirici başvurusu](functions-reference-python.md)
+ [TypeScript geliştirici başvurusu](functions-reference-node.md#typescript)
