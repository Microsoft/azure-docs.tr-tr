---
title: Azure 'da Jupyıter not defterlerini oluşturma ve kopyalama
description: Azure Notebooks projeler, başka bir kaynaktan yeni veya kopya oluşturabileceğiniz bir not defteri ve ilgili dosya koleksiyonunu yönetir.
services: app-service
documentationcenter: ''
author: kraigb
manager: barbkess
ms.assetid: 9b6a49e2-1d71-4c0b-9e5d-16e059427e38
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 02/25/2019
ms.author: kraigb
ms.openlocfilehash: 3e0c49d1141d976558ece325baaa99fc9981275e
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73496707"
---
# <a name="create-and-clone-projects"></a>Projeleri oluşturma ve kopyalama

Azure Notebooks Jupyıter not defterlerinizi ve ilgili dosyaları *Projeler*adlı mantıksal gruplar halinde düzenler. Önce bir kapsayıcı olarak bir proje oluşturun, ardından diğer proje dosyalarının yanı sıra bir klasör içinde bir veya daha fazla not defteri oluşturun veya kopyalayın. (Bu işlem [öğreticide](tutorial-create-run-jupyter-notebook.md)gösterilmiştir.)

Proje ayrıca, özel kurulum adımları ve paket yüklemesi dahil olmak üzere dizüstü bilgisayarların çalıştığı sunucuyu etkileyen meta verileri ve diğer yapılandırma ayarlarını da korur. Daha fazla bilgi için bkz. [projeleri yönetme ve yapılandırma](configure-manage-azure-notebooks-projects.md).

## <a name="use-the-my-projects-dashboard"></a>Projelerim panosunu kullanma

`https://notebooks.azure.com/<userID>/projects` konumundaki **Projelerim** panonuz, projeleri görüntülediğiniz, yönettiğiniz ve oluşturduğunuz yerdir:

[Azure Notebooks içindeki projelerim panosunu ![](media/my-projects-dashboard.png)](media/my-projects-dashboard.png#lightbox)

Panoda yapabilecekleriniz, Kullanıcı KIMLIĞININ sahibi olan hesapla oturum açmış olmanıza bağlı olarak değişir:

| Komut | Kimler kullanabilir? | Açıklama |
| --- | --- | --- |
| **Çalışmaz** | Sahip | Proje sunucusunu başlatır ve proje klasörünü Jupyıter içinde açar. (Daha yaygın olarak, önce bir proje klasörüne giderek sonra buradan bir not defteri başlatabilirsiniz.) |
| **İndir** | Kişiyle | Seçili projenin bir kopyasını ZIP dosyası olarak indirir. |
| **Paylaş** | Kişiyle | Seçilen bir projenin URL 'sini elde edebilir, sosyal medya paylaşabilir, URL ile bir e-posta gönderebilir ve bir "başlatma Not defteri" rozeti (bkz. [bir başlatma rozet alma](#obtain-a-launch-badge)) ile birlikte IÇIN hem HTML hem de markı kodu elde edebilirsiniz. |
| **Silme** | Sahip | Seçilen projeyi siler. Bu işlem geri alınamaz. |
| **Den** | Sahip | Proje sunucusunu başlatır ve ardından bu sunucu için bash terminaliyle yeni bir tarayıcı penceresi açar. |
| **+ Yeni proje** | Sahip | Yeni bir proje oluşturur. Bkz. [Yeni proje oluşturma](#create-a-new-project). |
| **GitHub deposunu karşıya yükle** | Sahip | GitHub 'dan bir projeyi içeri aktarır. [GitHub 'dan bir projeyi Içeri aktarın](#import-a-project-from-github). |
| **Oluşturulacak** | Kişiyle | Seçili bir projeyi kendi hesabınıza kopyalar. Henüz yoksa oturum açmanızı ister. Bkz. [projeyi kopyalama](#clone-a-project). |

### <a name="obtain-a-launch-badge"></a>Başlatma rozetini edinin

**Share** komutunu kullandığınızda ve **ekleme** sekmesini seçtiğinizde, "Not defteri başlatma" rozetini oluşturan HTML kodunu veya Marku 'yi kopyalayabilirsiniz:

![Not defteri rozetini Başlat](https://notebooks.azure.com/launch.png)

Azure Notebooks projeniz yoksa, aşağıdaki şablonları kullanarak doğrudan GitHub 'dan klonların bulunduğu bir bağlantı oluşturabilirsiniz, uygun Kullanıcı adı ve depo adlarını değiştirebilirsiniz:

```html
<a href="https://notebooks.azure.com/import/gh/<GitHub_username>/<repository_name>"><img src="https://notebooks.azure.com/launch.png" /></a>
```

```markdown
[![Azure Notebooks](https://notebooks.azure.com/launch.png)](https://notebooks.azure.com/import/gh/<GitHub_username>/<repository_name>)
```

## <a name="create-a-new-project"></a>Yeni bir proje oluşturma

**+ Yeni proje** komutunu kullandığınızda Azure Notebooks **Yeni bir proje oluştur** açılır penceresi görüntüler. Bu açılan pencerede, aşağıdaki bilgileri girin ve ardından **Oluştur**' u seçin:

| Alan | Açıklama |
| --- | --- |
| Proje adı | Projeniz için Azure Notebooks görüntüleme amacıyla kullanılan kolay bir ad. Örneğin, "Not Defterim projem". |
| Proje Kimliği | Bir projeyi paylaşmak için kullandığınız URL 'nin bir parçası haline gelen özel bir tanımlayıcı (form `https://notebooks.azure.com/<user_id>/projects/<project_id>`). Bu KIMLIK yalnızca harf, rakam ve kısa çizgi kullanabilir, 30 karakterle sınırlıdır ve [ayrılmış bir proje kimliği](#reserved-project-ids)olamaz. Ne kullanacağınızdan emin değilseniz, yaygın olarak kullanılan bir kural, "My-Not-Project" (uzunluk sınırına uyması gerekirse kesildi) gibi boşlukların tire içine açık olduğu, proje adınızın küçük harfli bir sürümünü kullanmaktır. |
| Genel | Ayarlanırsa, bağlantıya sahip olan kişilerin projeye erişmesine izin verir. Özel bir proje oluştururken, bu seçeneği temizleyin. |
| Bu projeyi bir BENIOKU ile Başlat | Ayarlanırsa, projede varsayılan bir *README.MD* dosyası oluşturur. İsterseniz, projeniz için belgeler sağladığınız bir *README.MD* dosyası. |

### <a name="reserved-project-ids"></a>Ayrılmış proje kimlikleri

Aşağıdaki ayrılmış sözcükler kendileri proje kimliği olarak kullanılamaz. Bununla birlikte, bu ayrılmış sözcükler, daha uzun proje kimliklerinin bir parçası olarak kullanılabilir.

| | | | | | |
| --- | --- | --- | --- | --- | --- |
| bilgi | account | yönetimine | API | lenemeyen | ders |
| content | pano | Explorer | SSS | Yardım | html |
| sayfa | Aktarmaya | Kitaplığı | yönetim | yeni | Mini |
| bilgisayarların | belgesini | önizleme | Fiyat | Profilinizi | aramanız |
| status | Support | test | | | |

Bu sözcüklerden birini proje KIMLIĞI olarak kullanmaya çalışırsanız, **Yeni proje oluştur** ve **proje ayarları** açılır pencere sayısı "kitaplık kimliği ayrılmış bir tanımlayıcıdır."

Proje KIMLIĞI aynı zamanda bir projenin URL 'sinin parçası olduğundan, ad engelleyici yazılım "duyurusu" gibi belirli anahtar sözcüklerin kullanımını engelleyebilir. Böyle durumlarda, proje KIMLIĞINDE farklı bir kelime kullanın.

## <a name="import-a-project-from-github"></a>GitHub 'dan proje içeri aktarma

Tüm genel GitHub deposunu, herhangi bir veri ve *README.MD* dosyası dahil olmak üzere proje olarak kolayca içeri aktarabilirsiniz. **GitHub depoyu karşıya yükle** komutunu kullanın, açılan pencerede aşağıdaki ayrıntıları sağlayın ve **içeri aktar**' ı seçin:

| Alan | Açıklama |
| --- | --- |
| GitHub deposu | Github.com üzerindeki kaynak deponun adı. Örneğin, Azure bilişsel hizmetler için Jupyıter not defterlerini [https://github.com/Microsoft/cognitive-services-notebooks](https://github.com/Microsoft/cognitive-services-notebooks)kopyalamak için, "Microsoft/bilişsel hizmetler-not defterleri" yazın.  |
| Yinelemeli olarak Kopyala | GitHub depoları birden çok alt depo içerebilir. Üst depoyu ve tüm alt öğelerini kopyalamak istiyorsanız bu seçeneği ayarlayın. Bir deponun çok sayıda alt öğesi olması mümkün olduğundan, ihtiyacınız olduğunu bilmiyorsanız bu seçeneği açık bırakın. |
| Proje adı | Projeniz için Azure Notebooks görüntüleme amacıyla kullanılan kolay bir ad. |
| Proje Kimliği | Bir projeyi paylaşmak için kullandığınız URL 'nin bir parçası haline gelen özel bir tanımlayıcı (form `https://notebooks.azure.com/<user_id>/projects/<project_id>`). Bu KIMLIK yalnızca harf, rakam ve kısa çizgi kullanabilir, 30 karakterle sınırlıdır ve [ayrılmış bir proje kimliği](#reserved-project-ids)olamaz. Ne kullanacağınızdan emin değilseniz, yaygın olarak kullanılan bir kural, "My-Not-Project" (uzunluk sınırına uyması gerekirse kesildi) gibi boşlukların tire içine açık olduğu, proje adınızın küçük harfli bir sürümünü kullanmaktır. |
| Genel | Ayarlanırsa, bağlantıya sahip olan kişilerin projeye erişmesine izin verir. Özel bir proje oluştururken, bu seçeneği temizleyin. |

Bir depoyu GitHub 'dan içeri aktarmak Ayrıca geçmişini içeri aktarır. Yeni değişiklikleri yürütmek, GitHub 'dan değişiklikleri çekmek vb. için terminaldeki standart git komutlarını kullanabilirsiniz.

## <a name="clone-a-project"></a>Proje kopyalama

Kopyalama, var olan bir projenin bir kopyasını kendi hesabınızda oluşturur. buradan, bu durumda herhangi bir not defteri veya projedeki diğer bir dosyayı çalıştırabilir ve değiştirebilirsiniz. Ayrıca, orijinal projeyi rahatsız etmeden denemeleri veya başka bir iş yaptığınız projelerin kopyalarını oluşturmak için kopyalamayı da kullanabilirsiniz.

Bir projeyi kopyalamak için:

1. **Projelerim** panosunda, istenen projeye sağ tıklayın ve **Kopyala** ' yı (klavye kısayolu: c) seçin.

    ![Proje bağlam menüsünde kopyalama komutu](media/clone-command.png)

1. **Projeyi Kopyala** açılır penceresinde, kopya için bir ad ve kimlik girin ve kopyanın genel olup olmadığını belirtin. Bu ayarlar, [Yeni bir proje](#create-a-new-project)için ile aynıdır.

    ![Projeyi Kopyala açılan penceresi](media/clone-project.png)

1. **Kopyala düğmesini seçtikten** sonra Azure Notebooks doğrudan kopyaya gider.

## <a name="next-steps"></a>Sonraki adımlar

- [Örnek not defterlerini keşfet](azure-notebooks-samples.md)
- [Nasıl yapılır: projeleri yapılandırma ve yönetme](configure-manage-azure-notebooks-projects.md)
- [Nasıl yapılır: bir not defteri içinden paket yüklemesi](install-packages-jupyter-notebook.md)
- [Nasıl yapılır: bir slayt gösterisi sunma](present-jupyter-notebooks-slideshow.md)
- [Nasıl yapılır: veri dosyalarıyla çalışma](work-with-project-data-files.md)
- [Nasıl yapılır: veri kaynaklarına erişme](access-data-resources-jupyter-notebooks.md)
- [Nasıl yapılır: Azure Machine Learning kullanma](use-machine-learning-services-jupyter-notebooks.md)
