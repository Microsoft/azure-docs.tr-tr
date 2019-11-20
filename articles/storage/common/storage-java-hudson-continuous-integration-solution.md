---
title: Hudson 'U BLOB depolama ile kullanma | Microsoft Docs
description: Hudson 'ın, derleme yapıtları için bir depo olarak Azure Blob depolama ile nasıl kullanılacağını açıklar.
services: storage
author: seguler
ms.service: storage
ms.devlang: Java
ms.topic: article
ms.date: 08/13/2019
ms.author: tarcher
ms.subservice: common
ms.openlocfilehash: 75b0ea106be04cd77b18bfed8487edb6a7b7f65b
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73839180"
---
# <a name="using-azure-storage-with-a-hudson-continuous-integration-solution"></a>Hudson Sürekli Tümleştirme çözümüyle Azure Depolama'yı kullanma
## <a name="overview"></a>Genel Bakış
Aşağıdaki bilgiler, bir Hudson sürekli tümleştirme (CI) çözümü tarafından oluşturulan derleme yapıtlarının bir deposu veya bir yapı işleminde kullanılacak indirilebilir dosyaların kaynağı olarak nasıl kullanılacağını gösterir. Bu faydalı bulduğunuz senaryolardan biri, çevik bir geliştirme ortamında (Java veya diğer diller kullanarak) kodlama yaparken, sürekli tümleştirmeye dayalı olarak çalışır ve derleme yapılarınız için bir depo gerekir, böylece Örneğin, bunları diğer kuruluş üyeleriyle, müşterilerinizle paylaşabilirsiniz veya bir arşivi saklayın.  Başka bir senaryo, derleme işinizin kendisi için başka dosyalar gerektirdiğinde, örneğin, derleme girişinin bir parçası olarak indirilecek bağımlılıklardır.

Bu öğreticide, Microsoft tarafından sağlanan Hudson CI için Azure Storage eklentisi 'ni kullanacaksınız.

## <a name="introduction-to-hudson"></a>Hudson 'a giriş
Hudson, geliştiricilerin kod değişikliklerini kolayca tümleştirmesini ve yapıların otomatik olarak ve sıklıkla üretilmiş olmasını sağlayarak geliştiricilerin verimliliğini artırarak bir yazılım projesinin sürekli tümleştirmesini sağlar. Derlemeler sürümlüdür ve derleme yapıtları çeşitli depolara yüklenebilir. Bu makalede, Azure Blob depolama 'nın derleme yapıtlarının deposu olarak nasıl kullanılacağı gösterilir. Ayrıca Azure Blob depolamadan bağımlılıkların nasıl indirileceği gösterilmektedir.

Hudson ile ilgili daha fazla bilgi için bkz. [Hudson](https://wiki.eclipse.org/Hudson-ci/Meet_Hudson).

## <a name="benefits-of-using-the-blob-service"></a>Blob hizmetini kullanmanın avantajları
Çevik geliştirme derlemesi yapıtlarınızı barındırmak için blob hizmetini kullanmanın avantajları şunlardır:

* Yapı yapılarınızın ve/veya indirilebilen bağımlılıkların yüksek kullanılabilirliği.
* Hudson CI çözümünüz derleme yapıtlarınızı karşıya yüklerken performans.
* Müşterilerinizin ve iş ortaklarınızın yapı yapıtlarınızı indirme performansı.
* Anonim erişim, sona erme tabanlı paylaşılan erişim imzası erişimi, özel erişim vb. arasında seçim içeren Kullanıcı erişim ilkeleri üzerinde denetim.

## <a name="prerequisites"></a>Ön koşullar
Blob hizmetini Hudson CI çözümünüz ile kullanmak için aşağıdakiler gerekir:

* Bir Hudson sürekli tümleştirme çözümü.
  
    Şu anda bir Hudson CI çözümünüz yoksa, aşağıdaki tekniği kullanarak bir Hudson CI çözümü çalıştırabilirsiniz:
  
  1. Java etkin bir makinede, <http://hudson-ci.org/>'den Hudson WAR 'i indirin.
  2. Hudson WAR dosyasını içeren klasöre açılan bir komut isteminde, Hudson WAR ' ı çalıştırın. Örneğin, 3.1.2 sürümünü indirdiyseniz:
     
      `java -jar hudson-3.1.2.war`

  3. Tarayıcınızda `http://localhost:8080/`açın. Bu işlem, Hudson panosunu açar.
  4. Hudson ' u ilk kez kullandığınızda `http://localhost:8080/`ilk kurulumunu doldurun.
  5. İlk kurulumu tamamladıktan sonra, Hudson WAR 'ın çalışan örneğini iptal edin, Hudson WAR ' ı yeniden başlatın ve Azure Storage eklentisini yüklemek ve yapılandırmak için kullanacağınız Hudson panosunu `http://localhost:8080/`yeniden açın.
     
      Tipik bir Hudson CI çözümü bir hizmet olarak çalışacak şekilde ayarlanacağından, komut satırında Hudson War çalıştırılması Bu öğretici için yeterli olacaktır.
* Bir Azure hesabı. <https://www.azure.com>bir Azure hesabı için kaydolabilirsiniz.
* Bir Azure depolama hesabı. Henüz bir depolama hesabınız yoksa [depolama hesabı oluşturma](../common/storage-quickstart-create-account.md)kısmındaki adımları kullanarak bir tane oluşturabilirsiniz.
* Aşağıdaki içerik, Hudson CI derleme yapıtları için bir depo olarak blob hizmetini kullanırken gereken adımları göstermek üzere temel bir örnek kullanacağı için, Hudson CI çözümünün kullanılması önerilir, ancak gerekli değildir.

## <a name="how-to-use-the-blob-service-with-hudson-ci"></a>Hudson CI ile blob hizmetini kullanma
Blob hizmetini Hudson ile kullanmak için Azure Storage eklentisini yüklemeniz, eklentiyi depolama hesabınızı kullanacak şekilde yapılandırmanız ve ardından derleme yapılarınızı depolama hesabınıza yükleyen bir oluşturma sonrası eylemi oluşturmanız gerekir. Bu adımlar aşağıdaki bölümlerde açıklanmıştır.

## <a name="how-to-install-the-azure-storage-plugin"></a>Azure Storage eklentisini yüklemek
1. Hudson panosu içinde, **Hudson**' u Yönet ' e tıklayın.
2. **Hudson sayfasını yönet** sayfasında, **Eklentileri Yönet**' e tıklayın.
3. **Kullanılabilir** sekmesine tıklayın.
4. **Diğerleri**' e tıklayın.
5. **Yapıt uploaders** bölümünde **Microsoft Azure depolama eklentisi**' ni seçin.
6. **Yükle**'ye tıklayın.
7. Yükleme tamamlandıktan sonra Hudson ' u yeniden başlatın.

## <a name="how-to-configure-the-azure-storage-plugin-to-use-your-storage-account"></a>Azure Storage eklentisini depolama hesabınızı kullanacak şekilde yapılandırma
1. Hudson panosu içinde, **Hudson**' u Yönet ' e tıklayın.
2. **Hudson sayfasını yönet** sayfasında **sistemi Yapılandır**' a tıklayın.
3. **Microsoft Azure depolama hesap yapılandırması** bölümünde:
   
    a. [Azure Portal](https://portal.azure.com)edinebilmeniz için depolama hesabınızın adını girin.
   
    b. Depolama hesabı anahtarınızı, ayrıca [Azure Portal](https://portal.azure.com)bilgiler kişilerden girin.
   
    c. Genel Azure bulutu kullanıyorsanız, **BLOB hizmeti uç nokta URL 'si** için varsayılan değeri kullanın. Farklı bir Azure bulutu kullanıyorsanız, depolama hesabınız için [Azure Portal](https://portal.azure.com) belirtilen uç noktayı kullanın.
   
    d. Depolama hesabınızı doğrulamak için **depolama kimlik bilgilerini doğrula** ' ya tıklayın.
   
    e. Seçim Hudson CI 'niz için kullanılabilir hale getirdiğiniz ek depolama hesaplarınız varsa, **daha fazla depolama hesabı ekle**' ye tıklayın.
   
    f. Ayarlarınızı kaydetmek için **Kaydet** ' e tıklayın.

## <a name="how-to-create-a-post-build-action-that-uploads-your-build-artifacts-to-your-storage-account"></a>Derleme yapılarınızı depolama hesabınıza yükleyen bir oluşturma sonrası eylem oluşturma
Yönergeler için, ilk olarak birkaç dosya oluşturacak bir iş oluşturmalı ve sonra dosyaları depolama hesabınıza yüklemek için derleme sonrası eylemini ekleyeceğiz.

1. Hudson panosu içinde **yeni iş**' a tıklayın.
2. İşi **myjob**olarak adlandırın, **ücretsiz stil yazılım işi oluştur ' a**tıklayın ve ardından **Tamam**' a tıklayın.
3. İş yapılandırmasının **derleme** bölümünde **derleme adımı ekle** ' ye tıklayın ve **Windows Batch komutunu Yürüt**' ü seçin.
4. **Komut**' de aşağıdaki komutları kullanın:

    ```   
        md text
        cd text
        echo Hello Azure Storage from Hudson > hello.txt
        date /t > date.txt
        time /t >> date.txt
    ```

5. İş yapılandırması **oluşturma sonrası eylemler** bölümünde, **BLOB depolama Microsoft Azure Için yapıtları karşıya yükle**' ye tıklayın.
6. **Depolama hesabı adı**için kullanılacak depolama hesabını seçin.
7. **Kapsayıcı adı**için kapsayıcı adını belirtin. (Kapsayıcı, derleme yapıtları karşıya yüklenirken henüz yoksa oluşturulur.) Bu örnekte ortam değişkenlerini kullanabilirsiniz. bu nedenle, kapsayıcı adı olarak **$ {JOB_NAME}** girin.
   
    **İpucu**
   
    **Windows Batch komutu yürütme** için bir betik girdiğiniz **komut** bölümünün altında, Hudson tarafından tanınan ortam değişkenlerine yönelik bir bağlantı bulunur. Ortam değişkeni adlarını ve açıklamalarını öğrenmek için bu bağlantıya tıklayın. **BUILD_URL** ortam değişkeni gibi özel karakterler içeren ortam değişkenlerine kapsayıcı adı veya ortak sanal yol olarak izin verilmez.
8. Bu örnek için **Varsayılan olarak yeni kapsayıcıyı genel yap** ' a tıklayın. (Özel bir kapsayıcı kullanmak istiyorsanız, erişime izin vermek için paylaşılan erişim imzası oluşturmanız gerekir. Bu makalenin kapsamı dışındadır. Paylaşılan erişim imzaları [(SAS) kullanarak](storage-sas-overview.md), paylaşılan erişim imzaları hakkında daha fazla bilgi edinebilirsiniz.)
9. Seçim Kapsayıcının, derleme yapıtları karşıya yüklenmeden önce içeriğin temizlenmesini istiyorsanız **karşıya yüklemeden önce kapsayıcıyı temizle** ' ye tıklayın (kapsayıcının içeriğini temizlemek istemiyorsanız işaretini işaretsiz bırakın).
10. **Karşıya yüklenecek yapıtların listesi**için **metin/*. txt**yazın.
11. **Karşıya yüklenen yapıtlar Için ortak sanal yol**için, **$ {BUILD\_ID}/$ {BUILD\_Number}** girin.
12. Ayarlarınızı kaydetmek için **Kaydet** ' e tıklayın.
13. Hudson panosunda, **Myjob**'u çalıştırmak Için **Şimdi Oluştur** ' a tıklayın. Durum için konsol çıkışını inceleyin. Oluşturma sonrası eylemi, derleme yapılarını karşıya yüklemeye başladığında, Azure depolama için durum iletileri konsol çıktısına dahil edilir.
14. İşi başarılı bir şekilde tamamladıktan sonra, genel blobu açarak derleme yapıtlarını inceleyebilirsiniz.
    
    a. [Azure portalında](https://portal.azure.com) oturum açın.
    
    b. **Depolama**' ya tıklayın.
    
    c. Hudson için kullandığınız depolama hesabı adına tıklayın.
    
    d. **Kapsayıcılar**' a tıklayın.
    
    e. Hudson işini oluşturduğunuzda atadığınız iş adının küçük harfli sürümü olan **myjob**adlı kapsayıcıya tıklayın. Azure depolama 'da kapsayıcı adları ve BLOB adları küçük harfle (ve büyük/küçük harfe duyarlıdır). **Myjob** adlı kapsayıcının blob listesi içinde **Hello. txt** ve **date. txt**' i görmeniz gerekir. Bu öğelerin her biri için URL 'YI kopyalayın ve tarayıcınızda açın. Derleme yapıtı olarak karşıya yüklenen metin dosyasını görürsünüz.

İş başına yapıtları Azure Blob depolamaya yükleyen bir oluşturma sonrası eylem oluşturulabilir. Yapıtları Azure Blob depolamaya yüklemeye yönelik tek derleme sonrası eylemi, ayırıcı olarak noktalı virgül kullanarak **karşıya yüklenecek yapıt listesi** içinde farklı dosyalar (joker karakterler dahil) ve dosya yolları belirtebilir. Örneğin, Hudson derlemeniz, çalışma alanınızın **derleme** klasöründe jar dosyaları ve txt dosyaları üretirse ve Ikisini de Azure Blob depolamaya yüklemek istiyorsanız, şu değeri **yükleyecek yapıtların listesi** için aşağıdakileri kullanın: **Build/\*. jar; Build/\*. txt**. Blob adı içinde kullanılacak yolu belirtmek için çift iki nokta sözdizimini de kullanabilirsiniz. Örneğin, blob yolundaki **bildirimleri** kullanarak karşıya yüklemek için blob yolundaki **IKILI** dosyaları ve txt dosyalarını kullanarak jars 'ın karşıya yüklenmesini istiyorsanız, şu değeri **yükleyecek yapıtların listesi** için şunu kullanın: **Build/\*. jar:: ikililer; Build/\*. txt:: bildirimler**.

## <a name="how-to-create-a-build-step-that-downloads-from-azure-blob-storage"></a>Azure Blob depolamadan indirilen bir derleme adımı oluşturma
Aşağıdaki adımlarda, Azure Blob depolamadan öğeleri indirmek için bir derleme adımının nasıl yapılandırılacağı gösterilmektedir. Bu, derlemenize öğe eklemek istiyorsanız (örneğin, Azure Blob depolamada tutacağınız), yararlı olabilir.

1. İş yapılandırmasının **derleme** bölümünde **derleme adımı ekle** ' ye tıklayın ve **Azure Blob depolama alanından indir**' i seçin.
2. **Depolama hesabı adı**için kullanılacak depolama hesabını seçin.
3. **Kapsayıcı adı**için, indirmek istediğiniz bloblara sahip kapsayıcının adını belirtin. Ortam değişkenlerini kullanabilirsiniz.
4. **BLOB adı**için blob adını belirtin. Ortam değişkenlerini kullanabilirsiniz. Ayrıca, blob adının ilk harflerini belirttikten sonra joker karakter olarak bir yıldız işareti kullanabilirsiniz. Örneğin, **proje\\** * adları **Project**ile başlayan tüm blob 'ları belirtebilir.
5. Seçim **İndirme yolu**Için, Azure Blob depolamadan dosyaları Indirmek Istediğiniz Hudson makinesindeki yolu belirtin. Ortam değişkenleri de kullanılabilir. ( **İndirme yolu**için bir değer sağlamazsanız, Azure Blob depolama alanındaki dosyalar iş çalışma alanına indirilir.)

Azure Blob depolama alanından indirmek istediğiniz ek öğeleriniz varsa, ek derleme adımları oluşturabilirsiniz.

Bir derlemeyi çalıştırdıktan sonra, beklediğiniz Blobların başarıyla indirilip indirilmediğini görmek için derleme geçmişi konsol çıkışını denetleyebilir veya indirme konumunuza bakabilirsiniz.

## <a name="components-used-by-the-blob-service"></a>Blob hizmeti tarafından kullanılan bileşenler
Aşağıdakiler blob hizmeti bileşenlerine genel bir bakış sağlar.

* **Depolama hesabı**: tüm Azure depolama erişimi bir depolama hesabı üzerinden yapılır. Bu, bloblara erişim için ad alanının en yüksek düzeyidir. Bir hesap, toplam boyutu 100 TB altında olduğu sürece sınırsız sayıda kapsayıcı içerebilir.
* **Kapsayıcı**: kapsayıcı bir blob kümesi gruplandırması sağlar. Tüm bloblar bir kapsayıcıda olmalıdır. Bir hesapta sınırsız sayıda kapsayıcı olabilir. Kapsayıcıda sınırsız sayıda blob depolanabilir.
* **BLOB**: herhangi bir tür ve boyuttaki dosya. Azure depolama 'da depolanabilecek iki tür blob vardır: blok ve sayfa Blobları. Çoğu dosya blok bloblardır. Tek bir Blok Blobu boyutu 200 GB 'a kadar olabilir. Bu öğretici blok bloblarını kullanır. Sayfa Blobları, başka bir blob türü, boyutu 1 TB 'ye kadar olabilir ve bir dosyadaki bayt aralıkları sıklıkla değiştirildiğinde daha etkilidir. Blob 'lar hakkında daha fazla bilgi için bkz. [blok bloblarını, ekleme bloblarını ve sayfa Bloblarını anlama](https://msdn.microsoft.com/library/azure/ee691964.aspx).
* **URL biçimi**: Bloblar aşağıdaki URL biçimi kullanılarak adreslenebilir:
  
    `http://storageaccount.blob.core.windows.net/container_name/blob_name`
  
    (Yukarıdaki biçim Global Azure bulutu için geçerlidir. Farklı bir Azure bulutu kullanıyorsanız, URL uç noktanızı öğrenmek için [Azure Portal](https://portal.azure.com) içindeki uç noktayı kullanın.)
  
    Yukarıdaki biçimde `storageaccount`, depolama hesabınızın adını temsil eder, `container_name` kapsayıcının adını temsil eder ve `blob_name` sırasıyla Blobun adını temsil eder. Kapsayıcı adı içinde, **/** eğik çizgiyle ayırarak birden çok yola sahip olabilirsiniz. Bu öğreticideki örnek kapsayıcı adı **Myjob**idi ve ortak sanal yol için **$ {BUILD\_ID}/$ {BUILD\_Number}** kullanıldı ve BLOB 'un aşağıdaki formun URL 'sini içermesiyle sonuçlanır:
  
    `http://example.blob.core.windows.net/myjob/2014-05-01_11-56-22/1/hello.txt`

## <a name="next-steps"></a>Sonraki adımlar
* [Hudson ile tanışın](https://wiki.eclipse.org/Hudson-ci/Meet_Hudson)
* [Java için Azure depolama SDK 'Sı](https://github.com/azure/azure-storage-java)
* [Azure Depolama İstemcisi SDK Başvurusu](https://javadoc.io/doc/com.microsoft.azure/azure-core/0.8.0/index.html)
* [Azure Depolama Hizmetleri REST API'si](https://msdn.microsoft.com/library/azure/dd179355.aspx)
* [Azure Depolama Ekibi Blog’u](https://blogs.msdn.com/b/windowsazurestorage/)

Daha fazla bilgi için bkz. [Java geliştiricileri için Azure](/java/azure).
