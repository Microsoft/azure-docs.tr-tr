---
title: Veri etiketlerini al
titleSuffix: Azure Machine Learning
description: Bu makalede, Machine Learning için verileri etiketlemek üzere etiketleme projelerini oluşturma ve çalıştırma hakkında bilgi verilmektedir.
author: lobrien
ms.author: laobri
ms.service: machine-learning
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: ca3486610d6cf71ba315e407b58a2a2551ad6ee1
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73837477"
---
# <a name="get-labels-for-data"></a>Veri etiketlerini al

Büyük miktarlarda verilerin etiketlenmesi, genellikle makine öğrenimi projelerinde bir zahmetli 'e sahiptir. Görüntü sınıflandırması veya nesne algılama gibi bir bilgisayar vizyonu bileşeni olan ML projeleri genellikle binlerce görüntü ve karşılık gelen Etiketler gerektirir. 
 
Azure Machine Learning, etiketleme projelerini oluşturmak, yönetmek ve izlemek için size merkezi bir konum sağlar. Verilerin etiketlenmesi, verileri, etiketleri ve takım üyelerini koordine etmenize yardımcı olmak için etiketleme görevlerini daha verimli bir şekilde yönetmenizi sağlar. Şu anda desteklenen görevler, çok etiketli veya çok sınıflı ve sınırlanmış kutular kullanılarak nesne tanımlaması olan görüntü sınıflandırmasıdır.

Azure ilerlemeyi izler ve tamamlanmamış etiketleme görevlerinin kuyruğunu korur. Etiketleyiciler katılmak için bir Azure hesabı gerektirmez. Microsoft hesabı (MSA) veya [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis)kimlik doğrulamasından sonra, zaman içinde izin verdiği kadar çok veya az etiketleme yapabilir. Klavye kısayollarını kullanarak etiketleri atayabilir ve değiştirebilir. 

Projeyi başlatabilir ve durdurabilir, kişiler ve takımlar ekleyebilir, kaldırabilir ve ilerlemeyi izleyebilirsiniz. Etiketli verileri COCO biçiminde veya bir Azure ML veri kümesi olarak dışa aktarabilirsiniz. 

Bu makalede aşağıdakileri nasıl yapacağınızı öğreneceksiniz:

> [!div class="checklist"]
> * Proje oluşturma
> * Projenin verilerini ve yapısını belirtin
> * Projede çalışan takımları ve kişileri yönetme
> * Projeyi çalıştırma ve izleme
> * Etiketleri dışarı aktarma 

## <a name="prerequisites"></a>Önkoşullar

* Yerel dosyalarda veya zaten Azure Storage 'da etiketlemek istediğiniz veriler
* Uygulamak istediğiniz etiket kümesi
* Etiketleme için yönergeler
* Azure aboneliği. Azure aboneliğiniz yoksa başlamadan önce ücretsiz bir hesap oluşturun. [Azure Machine Learning ücretsiz veya ücretli sürümünü](https://aka.ms/AMLFree) bugün deneyin
* Azure Machine Learning çalışma alanı. Bkz. [Azure Machine Learning çalışma alanı oluşturma](how-to-manage-workspace.md).

## <a name="create-a-labeling-project"></a>Etiketleme projesi oluşturma

Etiketleme projeleri [Azure Machine Learning](https://ml.azure.com/)yönetilir. **Etiketleme projeleri** sayfası, projelerinizi, ekiplerinizi ve kişilerinizi yönetmenizi sağlar. Bir projede kendisine atanmış bir veya daha fazla takım vardır ve bir takıma bir veya daha fazla kişi atanmış olur. 

Verileriniz zaten Azure Blob depolamada depolanıyorsa, etiketleme projenizi oluşturmadan önce bunları bir veri deposu olarak kullanılabilir hale getirebilirsiniz. Bilgi için bkz. [veri depoları oluşturma ve kaydetme](https://docs.microsoft.com/azure/machine-learning/service/how-to-access-data#create-and-register-datastores). 

Bir proje oluşturmak için **Proje Ekle**' yi seçin. Buna uygun bir ad verin ve **etiketleme görev türünü**seçin. 

![Proje oluşturma Sihirbazı etiketleme](media/how-to-create-labeling-projects/labeling-creation-wizard.png)

* Bir sınıf kümesinden bir **_veya daha fazla_** etiketin bir görüntüye uygulanabileceğini gösteren projeler için **görüntü sınıflandırması çoklu etiketi** ' ni seçin. Örneğin, bir köpek fotoğrafı hem *köpek* hem de *gündüz* ile etiketlenebilir
* Bir resim kümesinden yalnızca **tek bir sınıfın** bir görüntüye uygulanabileceğini gösteren projeler Için **görüntü sınıflandırması çoklu sınıfı** ' nı seçin
* Görevin, bir resim içindeki bir nesneye bir sınıf atamak ve nesneyi çevreleyen bir sınırlayıcı kutu belirtmek için olduğu projeler için **nesne tanımlama (sınırlayıcı kutu)** seçeneğini belirleyin.

Üzerinde taşımaya hazırsanız **İleri ' yi** seçin.

## <a name="specify-data-to-be-labeled"></a>Etiketlenecek verileri belirtin

Verilerinizi içeren bir veri kümesi zaten oluşturduysanız, **mevcut bir veri kümesi Seç** açılır listesinden bunu seçebilirsiniz. Ya da var olan bir Azure veri deposu seçmek veya yerel dosyaları karşıya yüklemek için **bir veri kümesi oluştur** ' u seçin. 

### <a name="create-a-dataset-from-an-azure-datastore"></a>Azure veri deposundan veri kümesi oluşturma

Yerel dosyaların doğrudan karşıya yüklenmesini seçmek birçok kullanım durumu için çok iyi olsa da, önemli miktarda veriyi aktarmak için [Azure Depolama Gezgini](https://azure.microsoft.com/features/storage-explorer/) hem daha güçlü hem de daha hızlı olur. Dosyaları taşımanın varsayılan yolu olarak Azure Depolama Gezgini önerilir.

Azure Blob depolamada zaten depoladığınız verilerden veri kümesi oluşturmak için:

1. **Veri kümesi** ve veri **deposundan** oluştur seçeneğini belirleyin
1. Veri kümeniz için bir **ad** atama
1. **Veri kümesi türü** olarak "dosya" seçeneğini belirtmelisiniz  
1. Veri deposunu seçin 
1. Verileriniz BLOB depolama içindeki bir alt klasördeyse, yolu seçmek için **Araştır** ' ı seçin. 
    * Ayrıca, seçili yolun alt klasörlerindeki tüm dosyaları dahil etmek için yoldan sonra `/**` ekleyebilirsiniz
    * Geçerli kapsayıcıya ve alt klasörlerdeki tüm verileri dahil etmek için `**/*.*` kullanın
1. Veri kümeniz için bir açıklama sağlayın
1. **İleri** Seç 
1. Ayrıntıları onaylayın. Ayarları değiştirmek için **geri** 'yi seçebilir veya veri kümesini oluşturmak için **Oluştur** ' a seçebilirsiniz

### <a name="create-a-dataset-by-uploading-data"></a>Verileri karşıya yükleyerek veri kümesi oluşturma

Verilerinizi doğrudan karşıya yüklemek istiyorsanız:

1. **Veri kümesi oluştur** ve **yerel dosyalar** ' ı seçin
1. Veri kümeniz için bir **ad** atama
1. **Veri kümesi türü** olarak "dosya" seçeneğini belirtmelisiniz
1. **Gelişmiş ayarlar**' ı seçerseniz, veri deposunu, kapsayıcıyı ve yolunu verilerinize göre özelleştirebilirsiniz
1. Karşıya yüklenecek yerel dosyaları seçmek için **Gözden** geçirme ' yi seçin
1. Veri kümeniz için bir açıklama sağlayın
1. **İleri** Seç 
1. Ayrıntıları onaylayın. Ayarları değiştirmek için **geri** 'yi seçebilir veya veri kümesini oluşturmak için **Oluştur** ' a seçebilirsiniz

Veriler, Azure ML çalışma alanınızın varsayılan blob deposuna (`workspaceblobstore`) yüklenir.

## <a name="specify-label-classes"></a>Etiket sınıfları belirtme

**Etiket sınıfları** sayfasında, verilerinizi sınıflandırmak için kullanılan sınıf kümesini belirtirsiniz. Etiketlerinizin doğruluğu ve hızı, aralarında düzgün bir şekilde seçim yaptıkları kolaylıklar tarafından etkilenerek, bu sınıflara düşünce sunun. Örneğin, bitkiler veya hayvanlar için tam Genus ve türler yazmak yerine, alan kodlarını kullanmak veya genabd 'yi kısaltan daha iyi olabilir. 

Yeni bir satır eklemek için **+** düğmesini kullanarak her satır için bir etiket girin. 3 ' ten fazla veya 4 etiketinize sahipseniz, ancak 10 ' dan az, bunları "1:", "2:", vb. olarak eklemeyi düşünün. 

## <a name="describe-the-labeling-task"></a>Etiketleme görevini açıkla

Etiketleme görevinin önemli olduğunu açıkça açıklamalıdır. **Etiketleme yönergeleri** sayfası, Etiketleyiciler için sunulacak yönergeler için bir dış siteye bağlantı kurmanıza olanak sağlar. Yönergeleri görev yönelimli ve hedef kitleye uygun halde tutun. 

* Göreceğiniz Etiketler nelerdir ve bunlar arasında nasıl seçim yapılır? Başvurmaları gereken bir başvuru metni var mı?
* Uygun bir etiket yoksa ne yapmalı? 
* Birden çok etiket uygun görünüyorsa ne yapmalı?
* Bir etikete hangi güven eşiğinin uygulanmaları gerekir? Belirli değillerse "en iyi tahminlerinden" istediklerinizi ister misiniz?
* Kısmen veya çakışan nesnelerle ne yapmalı?
* İlgilendiğiniz bir nesne görüntünün kenarıyla kırpıldıklarında ne yapmalı?
* Gönderdikten sonra bir görüntüyle ilgili bir hata yaptığını düşünüyorsanız ne yapmalı? 

Sınırlayıcı kutular ile diğer önemli sorular şunlardır:

* Bu görev için sınırlayıcı kutu nasıl tanımlanır? Nesnenin tamamıyla iç kısmı olacak şekilde, kendi boyutuna yakın bir şekilde kırpılmalıdır veya bazı açıklığı kabul edilebilir mi? 
* Etiketleyici 'nin sınırlayıcı kutuları tanımlamak için uygulanmasını beklediğiniz ve tutarlılık düzeyi nedir?

>[!Note]
> Etiketleyici 'nin 1-9 arası ilk 9 etiketi arasından seçim yapabilmesini sağlayın. 

## <a name="initialize-the-labeling-project"></a>Etiketleme projesini başlatma

Başlatıldıktan sonra etiketleme projesinin bazı yönleri sabittir: görev türünü veya veri kümesini değiştiremezsiniz. Etiketler ' i değiştirebilir ve görev açıklamasının URL 'sini değiştirebilirsiniz. Projeyi oluşturmadan önce ayarları dikkatle gözden geçirin. Projeyi gönderdikten sonra **etiketleme** giriş sayfasına geri yönlendirilirsiniz, bu da projeyi **başlatılıyor**olarak gösterir. Bu sayfa, bir süre sonra yeniden yenilenmez, bu nedenle bir süre sonra el ile yenilendikten sonra proje **oluşturulur**. 

## <a name="manage-teams-and-people"></a>Takımları ve kişileri yönetme

Etiketleme projesi varsayılan bir ekibi alır ve size varsayılan bir üye olarak ekler. Her etiketleme projesi yeni bir varsayılan takım alır, ancak takımlar projeler arasında paylaşılabilir. Projelerin birden fazla takımı olabilir. Takım oluşturmak **takımlar** sayfasında **Takım Ekle** ' ye kadar yapılır. 

Kişiler, **kişiler** sayfasında yönetilir. E-posta adreslerine girilen kişileri ekleyebilir ve kaldırabilirsiniz. Her bir etiketleyici, Microsoft hesabını kullanarak kimlik doğrulaması yapmak veya onu kullanıyorsanız Azure Active Directory.  

Bir kişi ekledikten sonra, bunları bir veya daha fazla takıma atayabilirsiniz. **Takımlar** sayfasına gidin, ilgilendiğiniz belirli bir ekibi seçin ve ardından **kişileri ata** veya kişileri istediğiniz şekilde **Kaldır** ' ı kullanın.

Ekipte herkese bir e-posta göndermek isterseniz, takım **ayrıntıları** sayfasını getirmek üzere ekibi seçerek bunu yapabilirsiniz. Bu sayfada **e-posta ekibi** düğmesi, e-posta düzenleyicinizi ekipteki herkesin adresleriyle açar.

## <a name="run-and-monitor-the-project"></a>Projeyi çalıştırma ve izleme

Proje başlatıldıktan sonra Azure bu uygulamayı çalıştırmaya başlayacaktır. Ana **etiketleme** sayfasında, projeye tıkladığınızda, **proje ayrıntılarına**yönlendirilirsiniz. **Pano** sekmesi etiketleme görevinde ilerlemeyi gösterir. 

Veri sekmesinde **veri** kümeniz ' ne bakabilir ve etiketli verileri gözden geçirebilirsiniz. Hatalı etiketlenmiş veriler görürseniz, bu etiketi **seçebilir** ve **Reddet**' i seçerek etiketleri kaldırır ve verileri etiketli sıraya geri koyar. 

**Takım** sekmesinde, bu projeye takımlar atayabilir veya atamayı kaldırabilirsiniz. 

Projeyi çevrimdışı veya çevrimiçi duruma almak isterseniz, projenin çalışma durumuna geçiş yapmak için **duraklat**/**Başlat** düğmesini seçin.

**Etiket verileri**' ni seçerek doğrudan **proje ayrıntıları** sayfasından verileri etiketleyebilir. Yalnızca proje çalışırken verileri etiketleyebilir. 

## <a name="export-the-labels"></a>Etiketleri dışarı aktarma

İstediğiniz zaman, Machine Learning deneme için etiket verilerini dışarı aktarabilirsiniz. Resim etiketleri [Coco biçiminde](http://cocodataset.org/#format-data) veya BIR Azure ML veri kümesi olarak aktarılabilir. Etiketleme projenizin **proje ayrıntıları** sayfasında **dışarı aktar** düğmesini bulacaksınız.

COCO dosyası, Azure ML çalışma alanının varsayılan Blob deposunda **dışarı aktarma/Coco**içindeki bir klasörde oluşturulur. Azure Machine Learning **veri kümeleri** bölümünde, AKTARıLMıŞ Azure ML veri kümesine erişebilirsiniz. Veri kümesi ayrıntıları sayfası, Python 'daki etiketlerinize erişmek için örnek kod de sağlar.

![Aktarılmış veri kümesi](media/how-to-create-labeling-projects/exported-dataset.png)

## <a name="next-steps"></a>Sonraki adımlar

* [Resim sınıflandırması veya nesne algılaması](how-to-label-images.md) için etiket resimleri
* [Azure Machine Learning ve Studio](../compare-azure-ml-to-studio-classic.md) hakkında daha fazla bilgi edinin