---
title: Proje-özel çeviriciyi arama, düzenleme ve silme
titleSuffix: Azure Cognitive Services
description: Özel çevirici, projelerinizi verimli şekilde yönetmek için çeşitli yollar sunar. Birden çok proje oluşturabilir, kriterinize göre arama yapabilir, projelerinizi düzenleyebilirsiniz. Özel çevirmende bir projeyi silme de mümkündür.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: lajanuar
ms.topic: conceptual
ms.openlocfilehash: 308ca25d35011c67ded7300177149cd590462952
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "98896452"
---
# <a name="search-edit-and-delete-projects"></a>Projeleri arama, düzenleme ve silme

Özel çevirici, projelerinizi verimli şekilde yönetmenin birden çok yolunu sağlar. Birçok proje oluşturabilir, kriterinize göre arama yapabilir ve projelerinizi düzenleyebilirsiniz. Özel çevirmende bir projeyi silme de mümkündür.  

## <a name="search-and-filter-projects"></a>Projeleri arama ve filtreleme

Filtre aracı, farklı filtre koşullarına göre projelerde arama yapmanıza olanak tanır. Proje adı, durum, kaynak ve hedef dil gibi filtreler ve projenin kategorisi.

1. Filtre düğmesine tıklayın.

    ![Proje ara](media/how-to/how-to-search-project.png)

2. Şu alanların herhangi birine (veya tümüne) göre filtre uygulayabilirsiniz: proje adı, kaynak dili, hedef dil, kategori ve proje kullanılabilirliği.

3. Uygula ' ya tıklayın.

    ![Proje filtresi seçeneklerini ara](media/how-to/how-to-search-project-filters.png)

4. "Temizle" düğmesine dokunarak tüm projelerinizi görüntülemek için filtreyi temizleyin.

## <a name="edit-a-project"></a>Projeyi düzenleme

Özel çevirici, bir projenin adını ve açıklamasını düzenlemenize olanak tanır. Kategori, kaynak dili ve hedef dil gibi diğer proje meta verileri düzenleme için kullanılamaz. Aşağıdaki adımlarda projenin nasıl düzenleneceği açıklanır.

1. Bir projenin üzerine geldiğinizde görüntülenen kurşun kalem simgesine tıklayın.

    ![Projeyi Düzenle](media/how-to/how-to-edit-project.png)

2. İletişim kutusunda proje adı, projenin açıklaması, Kategori açıklaması ve bir model dağıtılmamışsa proje etiketi de değiştirebilirsiniz. Proje oluşturulduktan sonra kategori veya Dil çiftini değiştiremezsiniz.

    ![Projeyi Düzenle iletişim kutusu](media/how-to/how-to-edit-project-dialog.png)

3. "Kaydet" düğmesine tıklayın.

## <a name="delete-a-project"></a>Projeyi silme

Artık ihtiyaç kalmadığında bir projeyi silebilirsiniz. Projenin dağıtılmış, eğitim gönderilen, veri işleme, dağıtma vb. gibi etkin durumda modeller içermediğinden, silme işlemi başarısız olur. Aşağıdaki adımlarda projenin nasıl silineceği açıklanır.

1. Herhangi bir proje kaydında üzerine gelin ve çöp kutusu simgesine tıklayın.

   ![Projeyi Sil](media/how-to/how-to-delete-project.png)

2. Silmeyi onaylayın. Proje silindiğinde, bu proje içinde oluşturulan tüm modeller silinir. Projeyi silme, belgelerinizi etkilemez.

   ![Onay iletişim kutusunu Sil](media/how-to/how-to-delete-project-confirm.png)

## <a name="next-steps"></a>Sonraki adımlar

- Özel çeviri modelinizi oluşturmaya başlamak için [belgeleri karşıya yükleyin](how-to-upload-document.md) .
