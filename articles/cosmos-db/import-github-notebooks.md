---
title: Bir GitHub deposundan not defterlerini Azure Cosmos DB içine aktarın ve çalıştırın
description: GitHub 'a bağlanmayı ve not defterlerini bir GitHub deposundan Azure Cosmos hesabınıza aktarmayı öğrenin. İçeri aktardıktan sonra, bunları çalıştırabilir, düzenleyebilir ve değişiklikleri GitHub 'a geri kaydedebilirsiniz.
author: deborahc
ms.author: dech
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 02/22/2021
ms.openlocfilehash: f7002b3968e91447a26315f31347ad469aa5daca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101690660"
---
# <a name="import-notebooks-from-a-github-repo-into-azure-cosmos-db"></a>Not defterlerini GitHub deposundan Azure Cosmos DB içine aktarma
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos hesaplarınız için [Not defteri desteğini etkinleştirdikten](enable-notebooks.md) sonra, yeni not defterleri oluşturabilir, yerel bilgisayarınızdan yeni not defterleri yükleyebilir veya mevcut not defterlerini GitHub hesaplarınızdan içeri aktarabilirsiniz. Bu makalede, Not defteri çalışma alanınızı GitHub 'a bağlama ve not defterlerini bir GitHub deposundan Azure Cosmos hesabınıza aktarma işlemi gösterilmektedir. İçeri aktardıktan sonra, bunları çalıştırabilir, değişiklikler yapabilir ve değişiklikleri GitHub 'a geri kaydedebilirsiniz.

## <a name="get-notebooks-from-github"></a>GitHub 'dan Not al

Azure Cosmos DB içindeki not defterlerini okumak, yazmak ve paylaşmak için kendi GitHub Depolarınıza veya diğer genel GitHub depolarına bağlanabilirsiniz. Bir GitHub hesabına bağlanmak için aşağıdaki adımları kullanın:

1. [Azure Portal](https://portal.azure.com/) açın ve Azure Cosmos hesabınıza gidin.

1. **Veri Gezgini** sekmesini açın. Bu sekme, tüm mevcut veritabanlarınızı, Kapsayıcılarınızı ve not defterlerinizi gösterir.

1. **GitHub 'A Bağlan** menü öğesini seçin.

1. Yalnızca **ortak depoya** veya **genel ve özel** depolara bağlanmayı seçebileceğiniz bir sekme açılır.  Gerekli seçeneği belirledikten sonra, **erişimi Yetkilendir**' i seçin. GitHub hesabınızdaki depolara erişmek için Azure Cosmos DB yetkilendirme gerekir.

   :::image type="content" source="./media/import-github-notebooks/authorize-access-github.png" alt-text="GitHub Depolarınıza erişmek için Azure Cosmos DB yetkilendirme":::

1. Yetkilendirmeyi doğrulayabileceğiniz "github.com" Web sayfasına yönlendirilirsiniz. **Yetkilendir Azulersmosdbnotebook** düğmesini seçin ve komut istemine GitHub hesabı parolanızı girin.

1. Yetkilendirme başarılı olduktan sonra, sizi Azure Cosmos hesabınıza geri götürür. Daha sonra GitHub hesabınızdan tüm ortak/özel depoları görebilirsiniz. Mevcut listeden bir depoyu seçebilirsiniz veya doğrudan URL 'sini kullanarak bir depoyu ekleyebilirsiniz.

1. Gerekli depoyu seçtiğinizde depo girişi, **ayrılmış** depolardan **sabitlenmiş** depolar bölümüne gider. Gerekirse, not defterlerini içeri aktarmak için ilgili deponun belirli bir dalını de seçebilirsiniz.

   :::image type="content" source="./media/import-github-notebooks/choose-repo-branch.png" alt-text="Bir depo ve dal seçin":::

1. İçeri aktarma işlemini gerçekleştirmek için **Tamam ' ı** seçin. Deponuzın seçili dalında bulunan tüm not defterleri Azure Cosmos hesabınıza aktarılır.

Bir GitHub hesabıyla tümleştirdikten sonra yalnızca Azure Cosmos hesabınızda bulunan depoların ve Not defterlerinin listesini görebilirsiniz. Birden çok Kullanıcı Azure Cosmos DB hesapta oturum açıp kendi hesaplarını ekleyebilse bile bu ifade geçerlidir. Diğer bir deyişle, birden çok Kullanıcı, Not defteri çalışma alanını GitHub 'a bağlamak için aynı Azure Cosmos hesabını kullanabilir. Ancak, her Kullanıcı yalnızca içeri aktardıkları depoların ve Not defterlerinin listesini görür. Başkaları tarafından içeri aktarılan Not defterleri sizin için görünür değildir.

GitHub hesabınızın Not defteri çalışma alanından bağlantısını kesmek için **Veri Gezgini** sekmesini açın, `…` **GitHub depoları** ' nı seçin ve **GitHub ' dan bağlantıyı kes**' i seçin.

## <a name="edit-a-notebook-and-push-changes-to-github"></a>Bir not defteri düzenleyin ve değişiklikleri GitHub 'a gönderin

Mevcut bir not defterini düzenleyebilir veya depoya yeni bir not defteri ekleyebilir ve değişiklikleri GitHub 'a geri kaydedebilirsiniz.

Mevcut bir not defterini düzenledikten sonra **Kaydet**' i seçin. Yaptığınız değişiklikler için COMMIT iletisini girebileceğiniz bir iletişim kutusu açılır. **Yürüt** ' ü seçin ve GitHub 'daki Not defteri güncelleştirilir. GitHub hesabınızda oturum açarak ve tamamlama geçmişini doğrulayarak güncelleştirmeleri doğrulayabilirsiniz.

Değişiklikleri kaydettikten sonra normal GitHub akışında, genellikle değişiklikleri uzak bir ile gönderirsiniz. Bununla birlikte, bu durumda COMMIT seçeneği, güncelleştirmelerinizi GitHub 'a "hazırlama, yürütme ve gönderme" amacını sunar.

:::image type="content" source="./media/import-github-notebooks/commit-changes-github.png" alt-text="Not defterlerini Düzenle ve değişiklikleri GitHub 'a Kaydet":::

Not defterini düzenledikten sonra [Not defteri galerisinde yayımlayabilirsiniz](publish-notebook-gallery.md). 

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Cosmos DB Jupyıter Not defterlerinin](cosmosdb-jupyter-notebooks.md) avantajları hakkında bilgi edinin.
* [Not defteri örnekleri galerisini keşfet](https://cosmos.azure.com/gallery.html)
* [Not defterlerini Azure Cosmos DB Not defteri galerisine yayımlama](publish-notebook-gallery.md)
* [Python Not defteri özelliklerini ve komutlarını kullanma](use-python-notebook-features-and-commands.md)
* [C# Not defteri özelliklerini ve komutlarını kullanma](use-csharp-notebook-features-and-commands.md)
