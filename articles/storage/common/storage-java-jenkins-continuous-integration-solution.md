---
title: Jenkins sürekli tümleştirme çözümüyle Azure depolama kullanma
description: Bu öğreticide, Azure Blob hizmeti 'nin bir Jenkins sürekli tümleştirme çözümü tarafından oluşturulan derleme yapıtları için depo olarak nasıl kullanılacağı gösterilmektedir.
ms.topic: article
ms.author: tarcher
author: tarcher
services: devops
ms.service: storage
custom: jenkins
ms.date: 08/13/2019
ms.subservice: common
ms.openlocfilehash: 72756bd3eb12ca80f419a0d53db76e6637d884fc
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73839125"
---
# <a name="using-azure-storage-with-a-jenkins-continuous-integration-solution"></a>Jenkins sürekli tümleştirme çözümüyle Azure depolama kullanma

Bu makalede, bir Jenkins sürekli tümleştirme (CI) çözümü tarafından oluşturulan derleme yapıtlarının bir deposu olarak veya bir yapı işleminde kullanılacak indirilebilir dosyaların kaynağı olarak blob Storage 'ın nasıl kullanılacağı gösterilmektedir. Bu çözümü bulacağınız senaryolardan biri, çevik bir geliştirme ortamında (Java veya diğer diller kullanarak) kodlama yaparken, sürekli tümleştirme temelinde derlemeler çalışıyor ve derleme yapılarınız için bir depo gerekir, bu sayede Örneğin, bunları diğer kuruluş üyeleriyle, müşterilerinizle paylaşabilir veya bir arşiv tutabilirsiniz. Başka bir senaryo, derleme işinizin kendisi için başka dosyalar gerektirdiğinde, örneğin, derleme girişinin bir parçası olarak indirilecek bağımlılıklardır.

Bu öğreticide, Microsoft tarafından kullanılabilir olan Jenkins CI için Azure Storage eklentisi 'ni kullanacaksınız.

## <a name="jenkins-overview"></a>Jenkins genel bakış
Jenkins, geliştiricilerin kod değişikliklerini kolayca tümleştirmesini ve yapıların otomatik olarak ve sıklıkla üretilmiş olmasını sağlayarak geliştiricilerin verimliliğini artırarak bir yazılım projesinin sürekli tümleştirmesini sağlar. Derlemeler sürümlüdür ve derleme yapıtları çeşitli depolara yüklenebilir. Bu makalede, Azure Blob depolama 'nın derleme yapıtlarının deposu olarak nasıl kullanılacağı gösterilmektedir. Ayrıca Azure Blob depolamadan bağımlılıkların nasıl indirileceği gösterilmektedir.

Jenkins hakkında daha fazla bilgi için [Jenkins](https://wiki.jenkins-ci.org/display/JENKINS/Meet+Jenkins)ile ilgili daha fazla bilgi bulabilirsiniz.

## <a name="benefits-of-using-the-blob-service"></a>Blob hizmetini kullanmanın avantajları
Çevik geliştirme derlemesi yapıtlarınızı barındırmak için blob hizmetini kullanmanın avantajları şunlardır:

* Yapı yapılarınızın ve/veya indirilebilen bağımlılıkların yüksek kullanılabilirliği.
* Jenkins CI çözümünüz derleme yapıtlarınızı karşıya yüklerken performans.
* Müşterilerinizin ve iş ortaklarınızın yapı yapıtlarınızı indirme performansı.
* Anonim erişim, sona erme tabanlı paylaşılan erişim imzası erişimi, özel erişim vb. arasında seçim içeren Kullanıcı erişim ilkeleri üzerinde denetim.

## <a name="prerequisites"></a>Ön koşullar
* Jenkins sürekli tümleştirme çözümü.
  
    Şu anda bir Jenkins CI çözümünüz yoksa, aşağıdaki tekniği kullanarak bir Jenkins CI çözümü çalıştırabilirsiniz:
  
  1. Java etkin bir makinede, <https://jenkins-ci.org>'tan Jenkins. war indirin.
  2. Jenkins. war içeren klasöre açılan bir komut isteminde şu komutu çalıştırın:
     
      `java -jar jenkins.war`

  3. Tarayıcınızda, Azure Storage eklentisini yüklemek ve yapılandırmak için kullanacağınız Jenkins panosunu açmak üzere `http://localhost:8080/` açın.
     
      Tipik bir Jenkins CI çözümü bir hizmet olarak çalışacak şekilde ayarlanacağından, bu öğretici için Jenkins War, komut satırında çalıştırıldığında yeterli olacaktır.
* Bir Azure hesabı. <https://www.azure.com>bir Azure hesabı için kaydolabilirsiniz.
* Bir Azure depolama hesabı. Henüz bir depolama hesabınız yoksa [depolama hesabı oluşturma](../common/storage-quickstart-create-account.md)kısmındaki adımları kullanarak bir tane oluşturabilirsiniz.
* Jenkins CI çözümüyle benzerlik yapmanız önerilir, ancak aşağıdaki içerik, blob hizmetini Jenkins CI derleme yapıtları için bir depo olarak kullanırken gereken adımları göstermek üzere temel bir örnek kullanacaktır.

## <a name="how-to-use-the-blob-service-with-jenkins-ci"></a>Jenkins CI ile blob hizmetini kullanma
Blob hizmetini Jenkins ile birlikte kullanmak için Azure Storage eklentisini yüklemeniz, eklentiyi depolama hesabınızı kullanacak şekilde yapılandırmanız ve ardından derleme yapılarınızı depolama hesabınıza yükleyen bir oluşturma sonrası eylemi oluşturmanız gerekir. Bu adımlar aşağıdaki bölümlerde açıklanmıştır.

## <a name="how-to-install-the-azure-storage-plugin"></a>Azure Storage eklentisini yüklemek
1. Jenkins panosu içinde **Jenkins 'ı Yönet**' i seçin.
2. **Jenkins 'ı Yönet** sayfasında, **Eklentileri Yönet**' i seçin.
3. **Available** (Kullanılabilir) sekmesini seçin.
4. **Yapıt uploaders** bölümünde **Microsoft Azure depolama eklentisi**' ni işaretleyin.
5. **Yeniden başlatma yapmadan yükle** veya **Şimdi indir ve yeniden başlattıktan sonra Yükle '** yi seçin.
6. Jenkins 'i yeniden başlatın.

## <a name="how-to-configure-the-azure-storage-plugin-to-use-your-storage-account"></a>Azure Storage eklentisini depolama hesabınızı kullanacak şekilde yapılandırma
1. Jenkins panosu içinde **Jenkins 'ı Yönet**' i seçin.
2. **Jenkins 'ı Yönet** sayfasında, **sistemi Yapılandır**' ı seçin.
3. **Microsoft Azure depolama hesap yapılandırması** bölümünde:
   1. [Azure Portal](https://portal.azure.com)edinebilmeniz için depolama hesabınızın adını girin.
   2. Depolama hesabı anahtarınızı, ayrıca [Azure Portal](https://portal.azure.com)bilgiler kişilerden girin.
   3. Genel Azure bulutu kullanıyorsanız, **BLOB hizmeti uç nokta URL 'si** için varsayılan değeri kullanın. Farklı bir Azure bulutu kullanıyorsanız, depolama hesabınız için [Azure Portal](https://portal.azure.com) belirtilen uç noktayı kullanın. 
   4. Depolama hesabınızı doğrulamak için **depolama kimlik bilgilerini doğrula** ' yı seçin. 
   5. Seçim Jenkins CI 'niz için kullanılabilir hale getirdiğiniz ek depolama hesaplarınız varsa, **daha fazla depolama hesabı ekle**' yi seçin.
   6. Ayarlarınızı kaydetmek için **Kaydet** ' i seçin.

## <a name="how-to-create-a-post-build-action-that-uploads-your-build-artifacts-to-your-storage-account"></a>Derleme yapılarınızı depolama hesabınıza yükleyen bir oluşturma sonrası eylem oluşturma
Eğitici bir amaç için, önce birkaç dosya oluşturacak bir iş oluşturmanız ve ardından dosyaları depolama hesabınıza yüklemek için oluşturma sonrası eylemini eklemeniz gerekir.

1. Jenkins panosu içinde **Yeni öğe**' yi seçin.
2. İşi **myjob**olarak adlandırın, **ücretsiz stil yazılım projesi oluştur**' u seçin ve ardından **Tamam**' ı seçin.
3. İş yapılandırmasının **derleme** bölümünde **derleme adımı ekle** ' yi seçin ve **Windows Batch komutunu Yürüt**' ü seçin.
4. **Komut**' de aşağıdaki komutları kullanın:

    ```   
    md text
    cd text
    echo Hello Azure Storage from Jenkins > hello.txt
    date /t > date.txt
    time /t >> date.txt
    ```

5. İş yapılandırmasının **Derleme sonrası eylemleri** bölümünde, **oluşturma sonrası eylem Ekle** ' yi seçin ve **yapıtları Azure Blob depolama alanına yükle**' yi seçin.
6. **Depolama hesabı adı**için kullanılacak depolama hesabını seçin.
7. **Kapsayıcı adı**için kapsayıcı adını belirtin. (Kapsayıcı, derleme yapıtları karşıya yüklenirken henüz yoksa oluşturulur.) Ortam değişkenlerini kullanabilirsiniz. bu nedenle, bu örnekte kapsayıcı adı olarak `${JOB_NAME}` girin.
   
    **İpucu**
   
    **Windows Batch komutu yürütme** için bir betik girdiğiniz **komut** bölümünün altında, Jenkins tarafından tanınan ortam değişkenlerine yönelik bir bağlantı bulunur. Ortam değişkeni adlarını ve açıklamalarını öğrenmek için bu bağlantıyı seçin. **BUILD_URL** ortam değişkeni gibi özel karakterler içeren ortam değişkenlerine kapsayıcı adı veya ortak sanal yol olarak izin verilmez.
8. Bu örnek için **Varsayılan olarak yeni kapsayıcıyı genel yap** ' ı seçin. (Özel bir kapsayıcı kullanmak istiyorsanız, erişime izin vermek için paylaşılan erişim imzası oluşturmanız gerekir, bu da bu makalenin kapsamı dışındadır. Paylaşılan erişim imzaları [(SAS) kullanarak](storage-sas-overview.md), paylaşılan erişim imzaları hakkında daha fazla bilgi edinebilirsiniz.)
9. Seçim Derleme yapıtları karşıya yüklenmeden önce kapsayıcının içerik seçimini yapmak istiyorsanız, kapsayıcıyı **karşıya yüklemeden önce Sil** ' i seçin (kapsayıcının içeriğini temizlemek istemiyorsanız işaretini kaldırın).
10. **Karşıya yüklenecek yapıtların listesi**için `text/*.txt`girin.
11. **Karşıya yüklenen yapıtlar Için ortak sanal yol**için, Bu öğreticinin amaçları doğrultusunda `${BUILD\_ID}/${BUILD\_NUMBER}`girin.
12. Ayarlarınızı kaydetmek için **Kaydet** ' i seçin.
13. Jenkins panosunda, **Myjob**'u çalıştırmak Için **Şimdi Oluştur** ' u seçin. Durum için konsol çıkışını inceleyin. Oluşturma sonrası eylemi, derleme yapılarını karşıya yüklemeye başladığında, Azure depolama için durum iletileri konsol çıktısına dahil edilir.
14. İşi başarılı bir şekilde tamamladıktan sonra, genel blobu açarak derleme yapıtlarını inceleyebilirsiniz.
    1. [Azure portalında](https://portal.azure.com) oturum açın.
    2. **Depolama**’yı seçin.
    3. Jenkins için kullandığınız depolama hesabı adını seçin.
    4. **Kapsayıcıları**seçin.
    5. Jenkins işini oluştururken atadığınız iş adının küçük harfli sürümü olan **myjob**adlı kapsayıcıyı seçin. Azure depolama 'da kapsayıcı adları ve BLOB adları küçük harfle (ve büyük/küçük harfe duyarlıdır). **Myjob**adlı kapsayıcının blob listesi içinde, **Hello. txt** ve **date. txt**' yi görmeniz gerekir. Bu öğelerin her biri için URL 'YI kopyalayın ve tarayıcınızda açın. Derleme yapıtı olarak karşıya yüklenen metin dosyasını görürsünüz.

İş başına yapıtları Azure Blob depolamaya yükleyen bir oluşturma sonrası eylem oluşturulabilir. Yapıtları Azure Blob depolamaya yüklemeye yönelik tek derleme sonrası eylemi, ayırıcı olarak noktalı virgül kullanarak **karşıya yüklenecek yapıt listesi** içinde farklı dosyalar (joker karakterler dahil) ve dosya yolları belirtebilir. Örneğin, Jenkins derlemeniz, çalışma alanınızın **derleme** klasöründe jar dosyaları ve txt dosyaları üretirse ve Ikisini de Azure Blob depolamaya yüklemek istiyorsanız, **karşıya yüklenecek yapıtların listesi** için şu değeri kullanın: `build/\*.jar;build/\*.txt`. Blob adı içinde kullanılacak yolu belirtmek için çift iki nokta sözdizimini de kullanabilirsiniz. Örneğin, blob yolundaki **bildirimleri** kullanarak karşıya yüklemek için blob yolundaki **IKILI** dosyaları ve txt dosyalarını kullanarak jars 'ın karşıya yüklenmesini Istiyorsanız, **karşıya yüklenecek yapıtların listesi** için şu değeri kullanın: `build/\*.jar::binaries;build/\*.txt::notices`.

## <a name="how-to-create-a-build-step-that-downloads-from-azure-blob-storage"></a>Azure Blob depolamadan indirilen bir derleme adımı oluşturma
Aşağıdaki adımlar, Azure Blob depolamadan öğeleri indirmek için bir derleme adımını yapılandırmak için, derlemenize öğe eklemek istiyorsanız yararlı olur. Bu düzenin kullanılmasına örnek olarak, Azure Blob depolamada kalıcı hale getirmek isteyebileceğiniz JARs vardır.

1. İş yapılandırmasının **derleme** bölümünde **derleme adımı ekle** ' yi seçin ve **Azure Blob depolama alanından indir**' i seçin.
2. **Depolama hesabı adı**için kullanılacak depolama hesabını seçin.
3. **Kapsayıcı adı**için, indirmek istediğiniz bloblara sahip kapsayıcının adını belirtin. Ortam değişkenlerini kullanabilirsiniz.
4. **BLOB adı**için blob adını belirtin. Ortam değişkenlerini kullanabilirsiniz. Ayrıca, blob adının ilk harflerini belirttikten sonra joker karakter olarak bir yıldız işareti kullanabilirsiniz. Örneğin, **proje\\** * adları **Project**ile başlayan tüm blob 'ları belirtebilir.
5. Seçim **İndirme yolu**Için, Azure Blob depolamadan dosyaları Indirmek Istediğiniz Jenkins makinesinde yolunu belirtin. Ortam değişkenleri de kullanılabilir. ( **İndirme yolu**için bir değer sağlamazsanız, Azure Blob depolama alanındaki dosyalar iş çalışma alanına indirilir.)

Azure Blob depolama alanından indirmek istediğiniz ek öğeleriniz varsa, ek derleme adımları oluşturabilirsiniz.

Bir derlemeyi çalıştırdıktan sonra, beklediğiniz Blobların başarıyla indirilip indirilmediğini görmek için derleme geçmişi konsol çıkışını denetleyebilir veya indirme konumunuza bakabilirsiniz.  

## <a name="components-used-by-the-blob-service"></a>Blob hizmeti tarafından kullanılan bileşenler
Bu bölüm, blob hizmeti bileşenlerine genel bir bakış sağlar.

* **Depolama Hesabı**: Tüm Azure Depolama erişimi bir depolama hesabı üzerinden yapılır. Depolama hesabı, bloblara erişim için ad alanının en yüksek düzeyidir. Bir hesap, toplam boyutu 100 TB altında olduğu sürece sınırsız sayıda kapsayıcı içerebilir.
* **Kapsayıcı**: kapsayıcı bir blob kümesi gruplandırması sağlar. Tüm bloblar bir kapsayıcıda olmalıdır. Bir hesapta sınırsız sayıda kapsayıcı olabilir. Kapsayıcıda sınırsız sayıda blob depolanabilir.
* **BLOB**: herhangi bir tür ve boyuttaki dosya. Azure depolama 'da depolanabilecek iki tür blob vardır: blok ve sayfa Blobları. Çoğu dosya blok bloblardır. Tek bir Blok Blobu boyutu 200 GB 'a kadar olabilir. Bu öğretici blok bloblarını kullanır. Sayfa Blobları, başka bir blob türü, boyutu 1 TB 'ye kadar olabilir ve bir dosyadaki bayt aralıkları sıklıkla değiştirildiğinde daha etkilidir. Blob 'lar hakkında daha fazla bilgi için bkz. [blok bloblarını, ekleme bloblarını ve sayfa Bloblarını anlama](https://msdn.microsoft.com/library/azure/ee691964.aspx).
* **URL biçimi**: Bloblar aşağıdaki URL biçimi kullanılarak adreslenebilir:
  
    `http://storageaccount.blob.core.windows.net/container_name/blob_name`
  
    (Yukarıdaki biçim Global Azure bulutu için geçerlidir. Farklı bir Azure bulutu kullanıyorsanız, URL uç noktanızı öğrenmek için [Azure Portal](https://portal.azure.com) içindeki uç noktayı kullanın.)
  
    Yukarıdaki biçimde `storageaccount`, depolama hesabınızın adını temsil eder, `container_name` kapsayıcının adını temsil eder ve `blob_name` sırasıyla Blobun adını temsil eder. Kapsayıcı adı içinde, **/** eğik çizgiyle ayırarak birden çok yola sahip olabilirsiniz. Bu öğretici için kullanılan örnek kapsayıcı adı **Myjob**idi ve ortak sanal yol için **$ {BUILD\_ID}/$ {BUILD\_Number}** kullanılmıştır ve bu, blob 'un aşağıdaki biçimde bir URL 'ye sahip olmasına neden olur:
  
    `http://example.blob.core.windows.net/myjob/2014-04-14_23-57-00/1/hello.txt`

## <a name="troubleshooting-the-jenkins-plugin"></a>Jenkins eklentisiyle ilgili sorunları giderme

Jenkins eklentileriyle ilgili hatalarla karşılaşırsanız [Jenkins JIRA](https://issues.jenkins-ci.org/) sayfasında söz konusu bileşenle ilgili sorun bildirebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar
* [Jenkins 'i karşılayın](https://wiki.jenkins-ci.org/display/JENKINS/Meet+Jenkins)
* [Java için Azure depolama SDK 'Sı](https://github.com/azure/azure-storage-java)
* [Azure Depolama İstemcisi SDK Başvurusu](https://javadoc.io/doc/com.microsoft.azure/azure-core/0.8.0/index.html)
* [Azure Depolama Hizmetleri REST API'si](https://msdn.microsoft.com/library/azure/dd179355.aspx)
* [Azure Depolama Ekibi Blog’u](https://blogs.msdn.com/b/windowsazurestorage/)

Daha fazla bilgi için bkz. [Java geliştiricileri için Azure](/java/azure).
