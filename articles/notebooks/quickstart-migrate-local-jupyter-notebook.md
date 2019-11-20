---
title: Yerel bir Jupyter Not defterini Azure Notebooks geçirme
description: Bir Jupyter Not defterini yerel bilgisayarınızdan veya bir Web URL 'sinden Azure Notebooks hızlı bir şekilde aktarın ve işbirliği için paylaşabilirsiniz.
services: app-service
documentationcenter: ''
author: kraigb
manager: barbkess
ms.assetid: 2e935425-3923-4a33-89b2-0f2100b0c0c4
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: 2151d5b04b0fe5d47e8e2ddfb750ac279b6f74fa
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/05/2019
ms.locfileid: "71970197"
---
# <a name="quickstart-migrate-a-local-jupyter-notebook"></a>Hızlı başlangıç: yerel bir Jupyter Not defterini geçirme

Kendi bilgisayarınızda yerel olarak oluşturduğunuz jupi not defterlerine yalnızca sizin erişebiliyordu. Dosyalarınızı çeşitli yollarla paylaşabilirsiniz, ancak alıcıların not defterinizin kendi yerel kopyaları vardır ve bu durumda yaptıkları değişiklikleri eklemeniz zordur. Ayrıca, not defterlerini GitHub gibi paylaşılan bir çevrimiçi depoda da saklayabilirsiniz, ancak bunu yapmak, her işbirriminin sizinkiyle aynı yapılandırmaya sahip kendi yerel Jupyıter yüklemesine sahip olmasını gerektirir.

Yerel veya depo tabanlı not defterlerinizi Azure Notebooks geçirerek, bunları ortak çalışanlarla birlikte paylaşabileceğiniz bulutta deporsınız. Bu ortak çalışanlarla, Not defterinizi görüntülemek ve çalıştırmak için yalnızca bir tarayıcı gerekir ve bu kişiler Azure Notebooks [oturum](quickstart-sign-in-azure-notebooks.md) açtıklarında de değişiklik yapabilirler.

Bu hızlı başlangıçta, yerel bilgisayarınızdan veya başka bir erişilebilir dosya URL 'sinden bir not defteri geçirme işlemi gösterilir. Not defterlerini bir GitHub deposundan geçirmek için bkz. [hızlı başlangıç: Not defterini kopyalama](quickstart-clone-jupyter-notebook.md).

## <a name="create-a-project-on-azure-notebooks"></a>Azure Notebooks proje oluşturma

1. [Azure Notebooks](https://notebooks.azure.com) gidin ve oturum açın. (Ayrıntılar için bkz. [hızlı başlangıç-Azure Notebooks Için oturum açma](quickstart-sign-in-azure-notebooks.md)).

1. Ortak profil sayfanızda sayfanın en üstündeki **Projelerim** ' nı seçin:

    ![Projelerim tarayıcı penceresinin üst kısmında bağlantı](media/quickstarts/my-projects-link.png)

1. **Projelerim** sayfasında **+ Yeni proje** ' yi seçin (klavye kısayolu: n); düğme yalnızca tarayıcı penceresi dar ise **+** olarak görünebilir:

    ![Projelerim sayfasında yeni proje komutu](media/quickstarts/new-project-command.png)

1. Görüntülenen **Yeni proje oluştur** açılır penceresinde, **Proje adı** ve **proje kimliği** alanlarına geçirdiğiniz Not defteri için uygun değerleri girin, **ortak proje** seçeneklerini temizleyin ve **bir Readme.MD oluşturun**ve ardından **Oluştur**' u seçin.

## <a name="upload-the-local-notebook"></a>Yerel Not defterini karşıya yükle

1. Proje sayfasında **karşıya yükle** ' yi seçin (yalnızca tarayıcı pencereniz küçük olduğunda yukarı ok olarak görünebilir), sonra 1 ' i seçin. Görüntülenen açılan pencerede, Not defteriniz yerel dosya **sisteminizde veya Not defteriniz çevrimiçi olduğunda** **bilgisayardan** ' i seçin:

    ![Bir URL 'den veya yerel bilgisayardan Not defteri yükleme komutu](media/quickstarts/upload-from-computer-url-command.png)

   (Not defteriniz bir GitHub deponuz olduğunda, [hızlı başlangıç: bir not defteri kopyalama](quickstart-clone-jupyter-notebook.md) bölümündeki adımları izleyin.)

   - **Bilgisayardan**kullanıyorsanız, *. ipynb* dosyalarınızı sürükleyip açılan pencereye bırakın veya **dosyaları seç**' i seçin ve ardından içeri aktarmak istediğiniz dosyaları seçin. Ardından **karşıya yükle**' yi seçin. Karşıya yüklenen dosyalara yerel dosyalarla aynı ad verilir. (Herhangi bir *. ipynb_checkpoints* klasörünün içeriğini karşıya yüklemeniz gerekmez.)

     ![Açılan bilgisayardan karşıya yükle](media/quickstarts/upload-from-computer-popup.png)

   - **URL 'den**kullanıyorsanız, dosya **URL 'si** alanına kaynak adresini ve **dosya adı** alanına projenizdeki not defterine atamak için dosya adını girin. Ardından **karşıya yükle**' yi seçin. Ayrı URL 'Ler içeren birden çok dosyanız varsa, açılan pencerenin, daha sonra başka bir dosya için yeni alanlar sağladığı ilk URL 'YI denetlemek için **+ Dosya Ekle** komutunu kullanın.

     ![URL açılan penceresinden karşıya yükle](media/quickstarts/upload-from-url-popup.png)

1. İçeriğini ve işlemini doğrulamak için karşıya yüklenen yeni not defterinizi açın ve çalıştırın. İşiniz bittiğinde, **dosya** >  ' i seçin**ve** Not defterini kapatmak için Kapat ' ı kapatın.

1. Karşıya yüklenen Not defterinize bir bağlantı paylaşmak için, projedeki dosyaya sağ tıklayın ve **Bağlantıyı Kopyala** ' yı (klavye kısayolu: y) seçin ve ardından bu bağlantıyı uygun iletiye yapıştırın. Alternatif olarak, proje sayfasında **paylaşma** denetimini kullanarak projeyi bir bütün olarak paylaşabilirsiniz.

1. Not defterleri dışındaki dosyaları düzenlemek için, projedeki dosyaya sağ tıklayın ve **dosyayı Düzenle** ' yi seçin (klavye kısayolu: ı). Varsayılan eylem, **Çalıştır** (klavye kısayolu: r) yalnızca dosya içeriğini gösterir ve düzenlenmesine izin vermez.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Öğretici: doğrusal regresyon yapmak için bir Jupyter Not defteri çalıştırma](tutorial-create-run-jupyter-notebook.md)
