---
title: Azure Not defteri projelerini yapılandırma ve yönetme
description: Proje meta verilerini, proje dosyalarını, projenin ortamını ve kurulum adımlarını Azure Notebooks Kullanıcı arabirimi ve doğrudan Terminal erişimi aracılığıyla yönetme.
services: app-service
documentationcenter: ''
author: kraigb
manager: barbkess
ms.assetid: 35dd6ff1-a14a-4a2e-b173-6d8467de3e89
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 05/13/2019
ms.author: kraigb
ms.openlocfilehash: fca98594be08f04b2f266f3aa574837ac024ecf4
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/05/2019
ms.locfileid: "71973122"
---
# <a name="manage-and-configure-projects"></a>Projeleri yönetme ve yapılandırma

Azure Notebooks bir proje aslında, Jupyter Not defterlerinin çalıştırıldığı temel Linux sanal makinesinin bir dosya klasörüyle ve açıklayıcı meta verilerle birlikte bir yapılandırmadır. Azure Notebooks içindeki proje panosu, dosyaları yönetmenizi ve proje özelliklerini yapılandırmanızı sağlar:

- Projenin çalıştırıldığı işlem katmanı, ücretsiz katman veya bir Azure sanal makinesi olabilir.
- Bir ad, açıklama, projeyi paylaşırken kullanılan tanımlayıcıyı ve projenin public veya Private olduğunu içeren proje meta verileri.
- Projenin Not defteri, verileri ve diğer dosya sistemleri gibi yönettiğiniz diğer dosyalar.
- Başlangıç betikleri ya da doğrudan Terminal aracılığıyla yönettiğiniz bir projenin ortamı.
- Terminal üzerinden erişebileceğiniz Günlükler.

> [!Note]
> Burada açıklanan yönetim ve yapılandırma özellikleri yalnızca projeyi başlangıçta oluşturan Proje sahibi tarafından kullanılabilir. Ancak, projeyi kendi hesabınıza kopyalayabilir, bu durumda sahip olursunuz ve projeyi istediğiniz şekilde yapılandırabilirsiniz.

Azure Notebooks, her bir not defteri veya başka bir dosya çalıştırdığınızda temel alınan sanal makineyi başlatır. Sunucu dosyaları otomatik olarak kaydeder ve 60 dakika etkinlik dışı kaldıktan sonra kapatır. Ayrıca, sunucuyu dilediğiniz zaman **kapatın** komutunu (klavye kısayolu: h) kullanabilirsiniz.

## <a name="compute-tier"></a>İşlem Katmanı

Varsayılan olarak, projeler **ücretsiz işlem** katmanında çalışır, bu da kötüye kullanımı engellemek için 4 GB bellek ve 1 GB veri ile sınırlıdır. Bu sınırlamaları atlayabilir ve bir Azure aboneliğinde sağladığınız farklı bir sanal makineyi kullanarak işlem gücünü artırabilirsiniz. Daha fazla bilgi için bkz. [veri bilimi sanal makinelerini kullanma](use-data-science-virtual-machine.md).

## <a name="edit-project-metadata"></a>Proje meta verilerini Düzenle

Proje panosunda **proje ayarları**' nı seçin ve ardından aşağıdaki tabloda açıklandığı gibi projenin meta verilerini içeren **bilgi** sekmesini seçin. Proje meta verilerini dilediğiniz zaman değiştirebilirsiniz.

| Ayar | Açıklama |
| --- | --- |
| Proje adı | Projeniz için Azure Notebooks görüntüleme amacıyla kullanılan kolay bir ad. Örneğin, "Python 'da Merhaba Dünya". |
| Proje Kimliği | Projeyi paylaşmak için kullandığınız URL 'nin bir parçası haline gelen özel bir tanımlayıcı. Bu KIMLIK yalnızca harf, rakam ve kısa çizgi kullanabilir, 30 karakterle sınırlıdır ve [ayrılmış bir proje kimliği](create-clone-jupyter-notebooks.md#reserved-project-ids)olamaz. Ne kullanacağınızdan emin değilseniz, yaygın olarak kullanılan bir kural, "My-Not-Project" (uzunluk sınırına uyması gerekirse kesildi) gibi boşlukların tire içine açık olduğu, proje adınızın küçük harfli bir sürümünü kullanmaktır. |
| Ortak proje | Ayarlanırsa, bağlantıya sahip olan kişilerin projeye erişmesine izin verir. Özel bir proje oluştururken, bu seçeneği temizleyin. |
| Klonları gizle | Ayarlanırsa, diğer kullanıcılar bu proje için yapılmış olan klonların bir listesini göremez. Klonların gizlenmesi, bir sınıfı öğretme için bir not defteri kullanırken olduğu gibi, aynı kuruluşun parçası olmayan birçok kişiyle paylaşılan projeler için yararlıdır. |

> [!Important]
>
> Proje KIMLIĞI değiştirildiğinde, daha önce paylaştığınız projenin tüm bağlantıları geçersiz hale gelebilir.

## <a name="manage-project-files"></a>Proje dosyalarını Yönet

Proje panosu, projenin klasör sisteminin içeriğini gösterir. Bu dosyaları yönetmek için çeşitli komutları kullanabilirsiniz.

### <a name="create-new-files-and-folders"></a>Yeni dosya ve klasörler oluşturma

**+ New** komutu (klavye kısayolu: n) yeni dosya veya klasör oluşturur. Komutunu kullanırken öncelikle oluşturulacak öğe türünü seçin:

| Öğe türü | Açıklama | Komut davranışı |
| --- | --- | --- |
| **Mini** | Jupyter Not defteri | Not defterinin dosya adını ve dilini belirttiğiniz bir açılan pencere görüntüler. |
| **Klasör** | Bir alt klasör | Projenin dosya listesinde klasör adını girdiğiniz bir düzenleme alanı oluşturur. |
| **Boş dosya** | İçine metin, veri vb. gibi içerik depolayabilen bir dosya. | Projenin dosya listesinde dosya adını girdiğiniz bir düzenleme alanı oluşturur. |
| **MARKDOWN** | Bir Markaşağı dosyası. | Projenin dosya listesinde dosya adını girdiğiniz bir düzenleme alanı oluşturur. |

### <a name="upload-files"></a>Dosyaları karşıya yükleme

**Karşıya yükle** komutu, diğer konumlardan veri içeri aktarmak için iki seçenek sunar: **URL 'den** ve **bilgisayardan**. Daha fazla bilgi için bkz. [Azure Not defteri projelerinde veri dosyalarıyla çalışma](work-with-project-data-files.md).

### <a name="select-file-specific-commands"></a>Dosyaya özgü komutları seçin

Projenin dosya listesindeki her öğe, sağ tıklama kısayol menüsü aracılığıyla komutlar sağlar:

![Dosya bağlam menüsündeki komutlar](media/project-file-commands.png)

| Komut | Klavye kısayolu | Eylem |
| --- | --- | --- |
| Çalıştırın | r (veya öğesine tıklayın) | Bir not defteri dosyası çalıştırır. Diğer dosya türleri görüntülenmek üzere açılır.  |
| Bağlantıyı Kopyala | Iz | Dosyaya bir bağlantıyı panoya kopyalar. |
| Jupyıter laboratuvarında Çalıştır | uygulanmaz | JupyterLab içinde, Jupyter 'ın normal olarak sağladığı daha fazla geliştirici odaklı bir arabirim olan bir not defteri çalıştırır. |
| Önizleme | Lama | Dosyanın HTML önizlemesini açar; Not defterleri için Önizleme, Not defterini salt okunurdur. Daha fazla bilgi için [Önizleme](#preview) bölümüne bakın. |
| Dosyayı Düzenle | kaydedemiyorum | Dosyayı düzenlenmek üzere açar. |
| İndirin | TID | Dosya veya klasörün içeriğini içeren bir ZIP dosyasını indirir. |
| Yeniden adlandır | a | Dosya veya klasör için yeni bir ad ister. |
| Sil | x | Onay ister, sonra dosyayı projeden kalıcı olarak kaldırır. Silme işlemleri geri alınamaz. |
| Taşı | m | Bir dosyayı aynı projede farklı bir klasöre taşıın. |

#### <a name="preview"></a>Önizleme

Bir dosyanın veya Not defterinin önizlemesi, içeriklerin salt okuma görünümüdür; Not defteri hücrelerinin çalıştırılması devre dışı bırakıldı. Önizleme, dosya veya Not defteri bağlantısı olan ancak Azure Notebooks oturum açmamış herkese gösterilir. Oturum açıldıktan sonra, Kullanıcı Not defterini kendi hesaplarına kopyalayabilir veya Not defterini yerel bilgisayarlarına indirebilir.

Önizleme sayfası klavye kısayollarıyla çeşitli araç çubuğu komutlarını destekler:

| Komut | Klavye kısayolu | Eylem |
| --- | --- | --- |
| Paylaşın | s | Bir bağlantı alabileceğiniz, sosyal medya paylaşma, katıştırma için HTML alma ve e-posta gönderme için bir paylaşım açılan penceresini görüntüler. |
| Kopyala | ,  | Not defterini hesabınıza kopyalayın. |
| Çalıştırın | sağ | İzin verildiyse, Not defterini çalıştırır. |
| İndirin | TID | Not defterinin bir kopyasını indirir. |

## <a name="configure-the-project-environment"></a>Proje ortamını yapılandırma

Not defterlerinizin çalıştığı temeldeki sanal makinenin ortamını yapılandırmanın üç yolu vardır:

- Tek seferlik başlatma betiği Ekle
- Kurulum adımlarını belirtmek için projenin ortam ayarlarını kullanın
- Sanal makineye bir terminal üzerinden erişin.

Tüm proje yapılandırması formları, sanal makine her başlatıldığında uygulanır ve bu nedenle proje içindeki tüm not defterlerini etkiler.

### <a name="one-time-initialization-script"></a>Bir kerelik başlatma betiği

İlk kez Azure Notebooks proje için bir sunucu oluşturduğunda, projede *aznbsetup.sh*adlı bir dosya arar. Bu dosya varsa, Azure Notebooks çalıştırır. Betiğin çıkışı proje klasörünüzde *. aznbsetup. log*olarak depolanır.

### <a name="environment-setup-steps"></a>Ortam kurulum adımları

Ortamı yapılandıran bireysel adımları oluşturmak için projenin ortam ayarlarını kullanabilirsiniz.

Proje panosunda proje **ayarları**' nı seçin ve ardından proje için kurulum adımlarını eklemek, kaldırmak ve değiştirmek istediğiniz **ortam** sekmesini seçin:

![Ortam sekmesi seçiliyken proje ayarları açılır penceresi](media/project-settings-environment-steps.png)

Bir adım eklemek için, önce **+ Ekle**' yi seçin ve ardından **işlem** açılır listesinden bir adım türü seçin:

![Yeni bir ortam için işlem Seçici Kurulum adımı](media/project-settings-environment-details.png)

Bundan sonra proje, seçtiğiniz işlem türüne bağlıdır:

- **Requirements. txt**: ikinci açılan listede, projede zaten olan bir *requirements. txt* dosyası seçin. Ardından görüntülenen üçüncü açılan listeden bir Python sürümü seçin. Bir bir not defteri sunucusunu başlatırken Azure Notebooks, *requirements. txt* dosyası kullanarak, *requirements. txt* dosyasıyla @no__t 1 ' i çalıştırır. Paketleri Not defterinin içinden açıkça yüklemeniz gerekmez.

- **Kabuk betiği**: ikinci aşağı açılan listede, ortamı başlatmak için çalıştırmak istediğiniz komutları içeren, projede bir bash Shell betiği (genellikle *. sh* uzantılı bir dosya) seçin.

- **Environment. yml**: ikinci açılan listede, bir Conda ortamı kullanarak Python projeleri için bir *ortamlar. yml* dosyası seçin.

Adım ekleme işiniz bittiğinde **Kaydet**' i seçin.

### <a name="use-the-terminal"></a>Terminali kullanma

Proje panosunda, **Terminal** komutu sunucuya doğrudan erişim sağlayan bir Linux terminali açar. Terminal içinde verileri indirebilir, dosyaları düzenleyebilir veya yönetebilir, süreçler inceleyebilir ve hatta vi ve nano gibi araçları kullanabilirsiniz.

> [!Note]
> Projenizin ortamında başlangıç betikleriniz varsa, terminali açmak kurulumun hala devam ettiğini belirten bir ileti görüntüleyebilir.

Terminalde herhangi bir standart Linux komutu verebilirsiniz. Ayrıca, *anaconda2_501*, *anaconda3_420*, *Anaconda3_501*, *ifsharp*ve *R*gibi sanal makinede bulunan farklı ortamları görmek için, bir projeyle birlikte `ls` ' ı da kullanabilirsiniz.projeyi içeren klasör:

![Azure Notebooks 'de Proje terminali](media/project-terminal.png)

Belirli bir ortamı etkilemek için, önce dizinleri bu ortam klasörüyle değiştirin.

Python ortamları için, her bir ortamın *bin* klasöründe `pip` ve `conda` ' i bulabilirsiniz. Ortamlar için yerleşik diğer adları da kullanabilirsiniz:

```bash
# Anaconda 2 5.3.0/Python 2.7: python27
python27 -m pip install <package>

# Anaconda 3 4.2.0/Python 3.5: python35
python35 -m pip install <package>

# Anaconda 3 5.3.0/Python 3.6: python36
python36 -m pip install <package>
```

Sunucuda yapılan değişiklikler, *Proje* klasöründe oluşturduğunuz dosyalar ve klasörler hariç yalnızca geçerli oturum için geçerlidir. Örneğin, proje klasöründeki bir dosyanın düzenlenmesiyle oturumlar arasında kalıcı hale getirilir, ancak `pip install` olan paketler değildir.

> [!Note]
> @No__t-0 veya `python3` kullanırsanız, Python 'un sistem tarafından yüklenen sürümlerini Not defterleri için kullanılmayan, çağırır. @No__t-0 gibi işlemler için izinleriniz yok, bu nedenle sürüme özgü diğer adları kullandığınızdan emin olun.

## <a name="access-notebook-logs"></a>Not defteri günlüklerine erişin

Bir not defteri çalıştırırken sorunlarla karşılaşırsanız, Jupyter 'dan alınan çıkış *. NB. log*adlı bir klasörde depolanır. Bu günlüklere, **Terminal** komutu veya proje panosu aracılığıyla erişebilirsiniz.

Genellikle, yerel olarak jupi çalıştırdığınızda bir terminal penceresinden başlatılmış olabilirsiniz. Terminal penceresi, çekirdek durumu gibi çıktıyı gösterir.

Günlükleri görüntülemek için terminalde aşağıdaki komutu kullanın:

```bash
cat .nb.log
```

Ayrıca, bir Python not defterindeki kod hücresinden komutunu da kullanabilirsiniz:

```bash
!cat .nb.log
```

## <a name="next-steps"></a>Sonraki adımlar

- [Nasıl yapılır: proje veri dosyalarıyla çalışma](work-with-project-data-files.md)
- [Bir not defterinde bulut verilerine erişme](access-data-resources-jupyter-notebooks.md)
