---
title: İşlevleri diğer Azure hizmetlerine bağlama
description: Diğer Azure hizmetlerine bağlanan bağlamaları Azure Işlevleri projenizdeki mevcut bir işleve eklemeyi öğrenin.
ms.topic: how-to
ms.date: 04/29/2020
ms.openlocfilehash: d1c6f5bb8ca5fcf995b8a8d326abbec96f1d2e35
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99258210"
---
# <a name="connect-functions-to-azure-services-using-bindings"></a>Bağlamaları kullanarak işlevleri Azure hizmetlerine bağlama

Bir işlev oluşturduğunuzda, bir tetikleyici şablonları kümesinden dile özgü tetikleyici kodu projenize eklenir. Giriş veya çıkış bağlamalarını kullanarak işlevinizi diğer hizmetlere bağlamak istiyorsanız, işlevinizdeki belirli bağlama tanımlarını eklemeniz gerekir. Bağlamalar hakkında daha fazla bilgi edinmek için bkz. [Azure işlevleri Tetikleyicileri ve bağlamaları kavramları](functions-triggers-bindings.md).

## <a name="local-development"></a>Yerel geliştirme       

İşlevleri yerel olarak geliştirirken, bağlama eklemek için işlev kodunu güncelleştirmeniz gerekir. Visual Studio Code kullanmak, bir işleve bağlama eklemeyi kolaylaştırabilir.  

### <a name="visual-studio-code"></a>Visual Studio Code

İşlevinizi geliştirmek için Visual Studio Code kullandığınızda ve işleviniz dosyada bir function.jskullanıyorsa, Azure Işlevleri uzantısı dosyadaki mevcut bir function.jsotomatik olarak bir bağlama ekleyebilir. Daha fazla bilgi için bkz. [giriş ve çıkış bağlamaları ekleme](functions-develop-vs-code.md#add-input-and-output-bindings).   

### <a name="manually-add-bindings-based-on-examples"></a>Örneklere göre el ile bağlama ekleyin

Mevcut bir işleve bağlama eklerken, diliniz tarafından kullanılıyorsa, hem işlev kodunu hem de yapılandırma dosyası function.jsgüncelleştirmeniz gerekir. Hem .NET sınıf kitaplığı hem de Java işlevleri function.jsyerine öznitelikleri kullanır, bu nedenle bunun yerine bunu güncelleştirmeniz gerekir.

Mevcut bir işlevi güncelleştirmede size rehberlik etmek için kullanabileceğiniz belirli bağlama türlerinin örneklerini bulmak için aşağıdaki tabloyu kullanın. İlk olarak, projenize karşılık gelen dil sekmesini seçin. 

[!INCLUDE [functions-bindings-code-example-chooser](../../includes/functions-bindings-code-example-chooser.md)]

## <a name="azure-portal"></a>Azure portalı

[Azure Portal](https://portal.azure.com)işlevlerinizi geliştirirken, belirli bir Işlev için **tümleştir** sekmesine giriş ve çıkış bağlamaları eklersiniz. Yeni bağlamalar, dilinize bağlı olarak dosyasına function.jsya da Yöntem özniteliklerine eklenir. Aşağıdaki makalelerde, portalda var olan bir işleve bağlama ekleme örnekleri gösterilmektedir:

+ [Kuyruk depolama çıkış bağlaması](functions-integrate-storage-queue-output-binding.md)
+ [Çıkış bağlamayı Azure Cosmos DB](functions-integrate-store-unstructured-data-cosmosdb.md)

## <a name="next-steps"></a>Sonraki adımlar

+ [Azure İşlevleri tetikleyicileri ve bağlama kavramları](functions-triggers-bindings.md)
