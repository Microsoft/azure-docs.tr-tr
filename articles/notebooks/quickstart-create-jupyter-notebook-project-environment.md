---
title: Özel bir ortamla Azure Notebooks projesi oluşturma
description: Azure Notebooks, belirli bir yüklü paketler ve başlatma betikleri kümesiyle yapılandırılmış yeni bir proje oluşturun.
services: app-service
documentationcenter: ''
author: kraigb
manager: barbkess
ms.assetid: e049f591-27a7-440f-a1a3-c5bef25e8a28
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: 6ec0d2fae285889a731e9b881ea90f15b96c85f2
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/05/2019
ms.locfileid: "71973079"
---
# <a name="quickstart-create-a-project-with-a-custom-environment"></a>Hızlı başlangıç: özel bir ortamla bir proje oluşturma

Azure Notebooks bir proje, belirli kurulum komutlarıyla yapılandırılabilecek bir ortamla birlikte Not defterleri, veri dosyaları, belgeler, resimler gibi bir dosya koleksiyonudur. Projeyi proje ile tanımlayarak, projeyi kendi Azure Notebooks olarak klonlayan herkes gerekli ortamı yeniden oluşturmak için gereken tüm bilgileri içerir.

## <a name="create-a-project"></a>Proje oluşturma

1. [Azure Notebooks](https://notebooks.azure.com) gidin ve oturum açın. (Ayrıntılar için bkz. [hızlı başlangıç-Azure Notebooks Için oturum açma](quickstart-sign-in-azure-notebooks.md)).

1. Ortak profil sayfanızda sayfanın en üstündeki **Projelerim** ' nı seçin:

    ![Projelerim tarayıcı penceresinin üst kısmında bağlantı](media/quickstarts/my-projects-link.png)

1. **Projelerim** sayfasında **+ Yeni proje** ' yi seçin (klavye kısayolu: n); düğme yalnızca tarayıcı penceresi dar ise **+** olarak görünebilir:

    ![Projelerim sayfasında yeni proje komutu](media/quickstarts/new-project-command.png)

1. Görüntülenen **Yeni proje oluştur** açılır penceresinde aşağıdaki ayrıntıları girin veya ayarlayın, sonra **Oluştur**' u seçin.

    - **Proje adı**: özel bir ortama sahip proje
    - **Proje kimliği**: proje-özel-ortam
    - **Ortak proje**: (işaretsiz)
    - **README.MD oluşturma**: (işaretsiz)

1. Birkaç dakika sonra Azure Notebooks yeni projeye götürür. **+ Yeni** açılan listesini (yalnızca **+** olarak görünebilir) seçip **Not defteri**' ni seçerek projeye bir not defteri ekleyin.

1. Not defterine özel ortam gibi bir ad verin *. ipynb*, dil için **Python 3,6** ' i seçin ve **Yeni**' yi seçin.

## <a name="add-a-custom-setup-step"></a>Özel Kurulum adımı ekleme

1. Proje sayfasında **proje ayarları**' nı seçin.

    ![Proje ayarları komutu](media/quickstarts/project-settings-command.png)

1. **Proje ayarları** açılır penceresinde **ortam** sekmesini seçin ve ardından **ortam kurulum adımları**altında **+ Ekle**' yi seçin:

    ![Yeni ortam Kurulum adımı ekleme komutu](media/quickstarts/environment-add-command.png)

1. **+ Add** komutu, bir işlem tarafından tanımlanan bir adım ve projenizdeki dosyalardan seçilen bir hedef dosya oluşturur. Aşağıdaki işlemler desteklenir:

    | Çalışmasını | Description |
    | --- | --- |
    | Requirements. txt | Python projeleri, bağımlılıklarını bir requirements. txt dosyasında tanımlar. Bu seçenekle, projenin dosya listesinden uygun dosyayı seçin ve görüntülenen ek açılan listede Python sürümünü de seçin. Gerekirse, projeye geri dönmek için **iptal** ' i seçin, dosyayı karşıya yükleyin veya oluşturun, ardından **proje ayarları** > **ortam** sekmesine dönüp yeni bir adım oluşturun. Bu adımla birlikte, projede bir not defterini çalıştırmak otomatik olarak çalıştırılır `pip install -r <file>` |
    | Kabuk betiği | Ortamı başlatmak için çalıştırmak istediğiniz komutları içeren bir bash kabuğu betiğini (genellikle *. sh* uzantılı bir dosya) göstermek için kullanın. |
    | Environment. yıml | Bir ortamı yönetmek için Conda kullanan bir Python projesi, bağımlılıkları tanımlayacak *ortamlar. yıml* dosyasını kullanır. Bu seçenekle, projenin dosya listesinden uygun dosyayı seçin. |

1. Herhangi bir kurulum adımını kaldırmak için adımın sağındaki **X** seçeneğini belirleyin.

1. Tüm kurulum adımları yerinde olduğunda, **Kaydet**' i seçin. (Değişiklikleri atmak için **iptal** ' i seçin).

1. Ortamınızı test etmek için yeni bir not defteri oluşturun ve çalıştırın, sonra, bir Python `import` deyimi kullanma gibi, ortamdaki bir pakete bağlı olan deyimlerle bir kod hücresi oluşturun. İfade başarılı olursa, gerekli paket ortama başarıyla yüklendi.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Azure Notebooks projeleri yönetme ve yapılandırma](configure-manage-azure-notebooks-projects.md)

> [!div class="nextstepaction"]
> [Öğretici: doğrusal regresyon yapmak için bir Jupyter Not defteri çalıştırma](tutorial-create-run-jupyter-notebook.md)
