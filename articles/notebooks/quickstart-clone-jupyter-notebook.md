---
title: Azure Notebooks ile bir Jupyter Not defterini GitHub 'dan kopyalama
description: Bir GitHub deposundan hızlı bir şekilde Jupyter Not defterini kopyalayın ve Azure Notebooks hesabınızda çalıştırın.
services: app-service
documentationcenter: ''
author: kraigb
manager: barbkess
ms.assetid: d7122b78-6daa-4bea-883b-ff832cfecef3
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: ed239c470a49fc045bd436668f83dff32eac3a66
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/05/2019
ms.locfileid: "71970127"
---
# <a name="quickstart-clone-a-notebook"></a>Hızlı başlangıç: bir not defterini kopyalama

Birçok veri bilimcileri ve geliştiricileri, not defterlerini birçok farklı proje türü için depolama ve sürüm denetimi sağlayan ücretsiz bir hizmet olan [GitHub depolarında](https://github.com)depolar. GitHub, genellikle yerel olarak çalıştırılan jupi not defterlerinde işbirliği yapma yöntemi olarak kullanılır. Bu gibi durumlarda, her ortak çalışan deponun yerel bir kopyasını tutar ve not defterlerini Bu kopyadan çalıştırır.

Kopyalama, bunun yerine Azure Notebooks hesabınızda bir GitHub Not Defteri kopyası oluşturur. Bu kopya, özgün deposundan bağımsızdır; değişiklikler yalnızca Azure Notebooks hesabınızda depolanır ve orijinali etkilemez. Klonunuz bulutta olduğundan, projeyi yerel kopya yapmamanız gereken diğer ortak çalışanlarla veya kendi bilgisayarlarına Jupyıter yüklemiş bir şekilde paylaşabilirsiniz. Ayrıca, bir not defterini yalnızca kendi projesi için bir başlangıç noktası olarak kopyalayabilir veya veri dosyalarını elde edebilirsiniz.

## <a name="clone-azure-cognitive-services-notebooks"></a>Azure bilişsel hizmetler not defterlerini Kopyala

1. [Azure Notebooks](https://notebooks.azure.com) gidin ve oturum açın. (Ayrıntılar için bkz. [hızlı başlangıç-Azure Notebooks Için oturum açma](quickstart-sign-in-azure-notebooks.md)).

1. Ortak profil sayfanızda sayfanın en üstündeki **Projelerim** ' nı seçin:

    ![Projelerim tarayıcı penceresinin üst kısmında bağlantı](media/quickstarts/my-projects-link.png)

1. **Projelerim** sayfasında, yukarı ok düğmesini (klavye kısayolu: U; tarayıcı penceresi yeterince geniş olduğunda **GitHub deposu karşıya yükle** olarak görünür) seçin:

    ![Projelerim sayfasında GitHub deposu komutunu karşıya yükle](media/quickstarts/upload-github-repo-command.png)

1. Görüntülenen **GitHub deposunu karşıya yükle** ' de, aşağıdaki ayrıntıları girin veya ayarlayın ve ardından **içeri aktar**' ı seçin:

   - **GitHub deposu**: Microsoft/bilişsel hizmetler-not defterleri (Bu ad, [https://github.com/Microsoft/cognitive-services-notebooks](https://github.com/Microsoft/cognitive-services-notebooks)' de Azure bilişsel hizmetler Için Jupyter not defterlerini klonlar.
   - **Yinelemeli olarak Kopyala**: (işaretsiz)
   - **Proje adı**: bilişsel hizmetler kopyası
   - **Proje kimliği**: bilişsel hizmetler-Kopyala
   - **Genel**: (işaretsiz)

     ![Depo bilgilerini toplamak için GitHub deposu yükleme açılan penceresini karşıya yükleyin](media/quickstarts/upload-github-repo-popup.png)

1. İşlem tamamlanırken sabırlı olun; bir depoyu kopyalamak birkaç dakika sürebilir.

1. Kopyalama işlemi tamamlandıktan sonra, Azure Notebooks tüm dosyaların kopyalarını görebileceğiniz yeni projeye götürür.

    [![](media/quickstarts/completed-clone.png#lightbox)(media/quickstarts/completed-clone.png "Tamamlanmış bir kopyanın görünümü")](media/quickstarts/completed-clone.png#lightbox)

## <a name="share-a-notebook"></a>Bir not defteri paylaşma

1. Klonlanan proje kopyanızı paylaşmak için, **paylaşma** denetimini kullanın veya bir bağlantı alın, bağlantıyı içeren HTML veya markı kodu alın veya şu bağlantıyla bir e-posta iletisi oluşturun:

    ![Proje paylaşma komutu](media/quickstarts/share-project-command.png)

1. Projeyi kopyalarken **ortak** seçeneği temizlediğiniz için kopya özeldir. Kopyanızı ortak hale getirmek için **proje ayarları**' nı seçin, açılan pencerede **ortak proje** seçeneğini ayarlayın ve ardından **Kaydet**' i seçin.

1. Projede çalıştırmak için bir not defteri seçin. Örneğin, Azure bilişsel hizmetler deposundaki her bir not defteri, kendi kendine dahil olan kendi hızlı başlangıçsıdır. Aşağıdaki görüntüde, bir bilişsel hizmetler API 'SI abonelik anahtarı eklendikten ve "Puppies" arama terimini "bies" olarak değiştirerek Bingimagesearchapı Not defterinin kullanılması sonucu gösterilmektedir:

    ![GitHub 'dan kopyalanmış Jupyter Not defterini çalıştırma](media/quickstarts/clone-notebook-result.png)

1. Not defterini çalıştırmayı tamamladığınızda **dosya** >  ' i seçin ve sonra da bir not defteri 'ni ve tarayıcı penceresini kapatmak için**Durdur** ' u seçin.

1. Projede tek bir not defterini paylaşmak için not defterine sağ tıklayıp **Bağlantıyı Kopyala** ' yı seçin (klavye kısayolu: y):

    ![Tek bir not defterine bağlantı kopyalamak için bağlam menüsü komutu](media/quickstarts/copy-link-to-individual-notebook.png)

1. Not defterleri dışındaki dosyaları düzenlemek için, projedeki dosyaya sağ tıklayın ve **dosyayı Düzenle** ' yi seçin (klavye kısayolu: ı). Varsayılan eylem, **Çalıştır** (klavye kısayolu: r) yalnızca dosya içeriğini gösterir ve düzenlenmesine izin vermez.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Öğretici: doğrusal regresyon yapmak için bir Jupyter Not defteri çalıştırma](tutorial-create-run-jupyter-notebook.md)
