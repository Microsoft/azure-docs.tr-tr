---
title: IBM zD & T v1 'de uygulama geliştiricileri denetimli dağıtımı (ADCD) ayarlama | Microsoft Docs
description: Azure sanal makinelerinde (VM) bir IBM Z geliştirme ve test ortamı (zD & T) ortamı çalıştırın.
services: virtual-machines-linux
ms.service: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
ms.author: edprice
editor: edprice
ms.topic: conceptual
ms.date: 02/22/2019
tags: ''
keywords: ''
ms.openlocfilehash: 66f80c79219090c27da37dfc1d9149df5604961f
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/06/2019
ms.locfileid: "68841381"
---
# <a name="set-up-an-application-developers-controlled-distribution-adcd-in-ibm-zdt-v1"></a>IBM zD & T v1 'de uygulama geliştiricileri denetimli dağıtımı (ADCD) ayarlama

Azure sanal makinelerinde (VM) bir IBM Z geliştirme ve test ortamı (zD & T) ortamı çalıştırabilirsiniz. Bu ortam IBM Z Serisi mimarisine öykünür. IBM Uygulama geliştiricileri denetimli dağıtımlar (Adcd 'Ler) adlı özelleştirilmiş paketler aracılığıyla kullanıma sunulan çeşitli Z Serisi işletim sistemlerini veya yüklemelerini (Z örnekleri veya paketleri olarak da bilinir) barındırabilir.

Bu makalede, Azure 'da bir zD & T ortamında bir ADCD örneğinin nasıl ayarlanacağı gösterilmektedir. Adcd 'Ler, zD & T 'de çalışan geliştirme ve test ortamları için tamamen Z Serisi işletim sistemi uygulamaları oluşturur.

ZD & T gibi, Adcd 'Ler yalnızca IBM müşterileri ve iş ortakları tarafından kullanılabilir ve yalnızca geliştirme ve test amaçlıdır. Bunlar, üretim ortamları için kullanılmamalıdır. [Passport Advantage](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.guide.adcd.doc/topics/installation_ps.html) veya [IBM PartnerWorld](https://www.ibm.com/partnerworld/public)aracılığıyla çok sayıda IBM yükleme paketi indirilebilir.

## <a name="prerequisites"></a>Önkoşullar

- Azure aboneliği. Aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

- Daha önce Azure 'da ayarlanmış [ZD & T ortamı][ibm-install-z] . Bu makalede, daha önce oluşturulan Ubuntu 16,04 VM görüntüsünü kullandığınız varsayılır.

- IBM PartnerWorld veya Passport avantajı aracılığıyla ADCD medyasına erişim.

- Bir [Lisans sunucusu](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.tools.user.guide.doc/topics/zdt_ee.html). Bu, IBM zD & T çalıştırmak için gereklidir. Bunu oluşturduğunuz şekilde, IBM 'den yazılım lisansınıza göre farklılık gösterir:

  - **Donanım tabanlı lisanslama sunucusu** , yazılımın tüm bölümlerine erişmek için gerekli olan Rational belirteçlerini IÇEREN bir USB donanım cihazı gerektirir. Bunu IBM 'den edinmeniz gerekir.

  - **Yazılım tabanlı lisanslama sunucusu** , lisanslama anahtarlarının yönetimi için merkezi bir sunucu ayarlamanızı gerektirir. Bu yöntem tercih edilir ve, yönetim sunucusunda IBM 'den aldığınız anahtarları ayarlamanızı gerektirir.

## <a name="download-the-installation-packages-from-passport-advantage"></a>Yükleme paketlerini Passport Advantage avantajlarından indirin

ADCD medyası için erişim gereklidir. Aşağıdaki adımlarda, bir IBM müşterisi olduğunuz varsayılmaktadır ve Passport Advantage kullanabilirsiniz. IBM iş ortakları [IBM PartnerWorld](https://www.ibm.com/partnerworld/public)kullanabilir.

> [!NOTE]
> Bu makalede, Windows BILGISAYARıN Azure portal erişmek ve IBM medyasını indirmek için kullanıldığı varsayılmaktadır. Mac veya Ubuntu Desktop kullanıyorsanız, IBM medyasını elde etmeye yönelik komutlar ve işlemler biraz farklı olabilir.

1. [Passport avantajı](https://www.ibm.com/software/howtobuy/passportadvantage/paocustomer)'nda oturum açın.

2. **Yazılım İndirmeleri** ve **medya erişimi**seçin.

3. **Program sunumu ve anlaşma numarasını**seçin ve **devam**' a tıklayın.

4. Bölüm açıklamasını veya parça numarasını girin ve **Bulucu**' ye tıklayın.

5. İsteğe bağlı olarak, ürünü adına göre görüntülemek ve görüntülemek için alfabetik sıra listesine tıklayın.

6. **İşletim sistemi alanındaki** **Tüm Işletim sistemlerini** ve **Diller alanındaki** **tüm dilleri** seçin. Ardından **Git**' e tıklayın.

7. Listeyi genişletmek ve indirilecek tek medyayı göstermek için **tek tek dosyaları seç** ' e tıklayın.

8. İndirmek istediğiniz paketleri doğrulayın, **İndir**' i seçin ve ardından dosyaları istediğiniz dizine indirin.

## <a name="upload-the-adcd-packages"></a>ADCD paketlerini karşıya yükle

Artık pakete sahip olduğunuza göre, bunları Azure 'da sanal makinenize yüklemeniz gerekir.

1. Azure portal, oluşturduğunuz Ubuntu VM ile bir **SSH** oturumu başlatın. Sanal makinenize gidin, **genel bakış** dikey penceresini seçin ve ardından **Bağlan**' ı seçin.

2. **SSH** sekmesini seçin ve ardından SSH komutunu panoya kopyalayın.

3. Kimlik bilgilerinizi ve tercih ettiğiniz [SSH istemcisini](/azure/virtual-machines/linux/use-remote-desktop) kullanarak sanal makinenizde oturum açın. Bu demo Windows 10 için Linux uzantıları kullanır ve bu, Windows komut istemine bash kabuğu ekler. PuTTY aynı zamanda çalışmaktadır.

4. Oturum açıldığında, IBM paketlerini karşıya yüklemek için bir dizin oluşturun. Linux 'ın büyük/küçük harfe duyarlı olduğunu aklınızda bulundurun. Örneğin, bu demo paketlerin karşıya yüklendiğini varsayar:

        /home/MyUserID/ZDT/adcd/nov2017/volumes

5. Dosyaları[WinSCP](https://winscp.net/eng/index.php)gıbı bir SSH istemcisi kullanarak karşıya yükleyin. SCP SSH 'nin bir parçası olduğundan, SSH 'nin kullandığı bağlantı noktası 22 ' yi kullanır. Yerel bilgisayarınız Windows değilse, SSH oturumunuza [SCP komutunu](http://man7.org/linux/man-pages/man1/scp.1.html) yazabilirsiniz.

6. Oluşturduğunuz Azure VM dizinine yükleme işlemini başlatın ve bu, zD & T için görüntü depolaması haline gelir.

    > [!NOTE]
    > Adcdtools ' ın bulunduğundan emin olun **. XML** , **Home/myuserıd/zdt/adcd/nov2017** dizinine yükleme bölümüne dahildir. Buna daha sonra ihtiyacınız olacak.

7. Dosyaların karşıya yüklenmesini bekleyin, bu işlem Azure bağlantınıza bağlı olarak biraz zaman alabilir.

8. Karşıya yüklemeler tamamlandığında, birimler dizinine gidin ve tüm **gz** birimlerinin sıkıştırmasını açın:

    ```
        gunzip \*.gz
    ```
    
![Açılan gz birimlerini gösteren dosya Gezgini](media/01-gunzip.png)

## <a name="configure-the-image-storage"></a>Görüntü depolamayı yapılandırma

Sonraki adım, zD & T 'leri karşıya yüklenen paketleri kullanacak şekilde yapılandırmaktır. ZD & T içindeki görüntü depolama işlemi görüntüleri takabilmeniz ve kullanmanıza olanak sağlar. SSH veya FTP kullanabilir.

1. **Zdtserver**'ı başlatın. Bunu yapmak için kök düzeyinde olmanız gerekir. Sırasıyla aşağıdaki iki komutu girin:
    ```
        sudo su -
        /opt/ibm/zDT/bin/startServer
    ```
2. Komutun URL çıkışını ve Web sunucusuna erişmek için bu URL 'yi kullanın. Şuna benzer:
     > https://(VM adınız veya IP adresiniz): 9443/ZDTMC/index. html
     >
     > Web erişiminizin 9443 numaralı bağlantı noktasını kullandığını unutmayın. Web sunucusunda oturum açmak için bunu kullanın. ZD & T için Kullanıcı KIMLIĞI **zdtadmin** ve parola **paroladır**.

    ![IBM zD & T Enterprise Edition hoş geldiniz ekranı](media/02-welcome.png)

3. **Hızlı başlangıç** sayfasında, **Yapılandır**altında, **görüntü depolaması**' nı seçin.

     ![IBM zD & T Enterprise Edition hızlı başlangıç ekranı](media/03-quickstart.png)

4. **Görüntü depolamayı Yapılandır** sayfasında **SSH dosya aktarım protokolü**' yi seçin.

5. **Ana bilgisayar adı**için **localhost** yazın ve görüntüleri karşıya yüklediğiniz dizin yolunu girin. Örneğin,/home/MyUserID/ZDT/adcd/nov2017/volumes.

6. VM 'nin **Kullanıcı kimliğini** ve **parolasını** girin. ZD & T kullanıcı KIMLIĞINI ve parolasını kullanmayın.

7. Erişiminizin olduğundan emin olmak için bağlantıyı test edin ve ardından, yapılandırmayı kaydetmek için **Kaydet** ' i seçin.

## <a name="configure-the-target-environments"></a>Hedef ortamları yapılandırma

Sonraki adım zD & T hedef ortamını yapılandırmaktır. Bu öykünülmüş barındırılan ortam, görüntülerinizin çalıştığı yerdir.

1. **Hızlı başlangıç** sayfasında, **Yapılandır**altında **hedef ortamlar**' ı seçin.

2. **Hedef ortamları Yapılandır** sayfasında, **hedef Ekle**' yi seçin.

3. **Linux**' u seçin. IBM iki tür ortamı, Linux ve bulutu (OpenStack) destekler, ancak bu demo Linux üzerinde çalışır.

4. **Hedef ortam ekle** sayfasında, **konak adı**için **localhost**girin. **SSH bağlantı noktasını** **22**olarak ayarlayın.

5. **Hedef ortam etiketi** kutusuna **mycıcs** gibi bir etiket girin.

     ![Hedef ortam ekleme ekranı](media/04-add-target.png)

## <a name="configure-adcd-and-deploy"></a>ADCD 'yi yapılandırma ve dağıtma

Önceki yapılandırma adımlarını tamamladıktan sonra, zD & T paketini ve hedef ortamı kullanacak şekilde yapılandırmanız gerekir. Yine, resim depolama sürecini zD & T ' de kullanarak görüntüleri bağlayabilir ve kullanmanıza olanak tanır. SSH veya FTP kullanabilir.

1. **Hızlı başlangıç** sayfasında, **Yapılandır**altında **adcd**' yi seçin. Bir ADCD paketinin bağlanması için tamamlanması gereken adımları söyleyen bir dizi yönerge görüntülenir. Bu, daha önce yaptığımız şekilde hedef dizini adlandırdığımızda açıklanmaktadır.

2. Tüm görüntülerin doğru dizinlere yüklendiği varsayılarak, sağ alt köşedeki (aşağıdaki ekran görüntüsünde adım 7 ' de gösterilen) **ADCD** bağlantısı ' na tıklayın.

     ![IBM zD & T Enterprise Edition-ADCD ekranını yapılandırma](media/05-adcd.png)

## <a name="create-the-image"></a>Görüntü oluşturma

Önceki yapılandırma adımı tamamlandığında, **ADCD bileşenlerini kullanarak görüntü oluştur** sayfası görüntülenir.

1. Bu birimdeki farklı paketleri göstermek için birimi (Bu durumda 2017 ' de) seçin.

2. Bu demo için **Müşteri bilgileri denetim sistemi (CICS)-5,3**' ı seçin.

3. **Görüntü adı** kutusuna resim Için **mycıcs Image**gibi bir ad yazın.

4. Sağ alt köşedeki **görüntü oluştur** düğmesini seçin.

     ![IBM zD & T Enterprise Edition-ADCD bileşenleri ekranını kullanarak bir görüntü oluşturma](media/06-adcd.png)

5. Görüntülenen pencerede, görüntünün başarıyla dağıtıldığını söyleyen **görüntüleri dağıt**' ı seçin.

6. **Bir görüntüyü hedef ortama dağıt** sayfasında, önceki sayfada oluşturduğunuz görüntüyü (**mycıcs Image**) ve daha önce oluşturulan hedef ortamı (**mycıcs**) seçin.

7. Sonraki ekranda VM için kimlik bilgilerinizi sağlayın (yani, ztadmin kimlik bilgisi değil).

8. Özellikler bölmesinde, çalışan görüntü için **Merkezi işlemcilerin (CPS)** sayısını, **sistem belleğı miktarını (GB)** ve **dağıtım dizinini** girin. Bu bir demo olduğundan, küçük tutun.

9. **Dağıtım sonrasında IPL komutunu otomatik olarak z/ç 'ye verilecek şekilde**kutunun seçildiğinden emin olun.

     ![Özellikler ekranı](media/07-properties.png)

10. **Tamam**' ı seçin.

11. Görüntüyü **hedef ortama dağıt** sayfasında **görüntüyü dağıt** ' ı seçin.

Görüntünüz artık dağıtabilir ve bir 3270 Terminal öykünücüsü tarafından takılmaya hazırdır.

> [!NOTE]
> Yeterli disk alanınız olmadığını söyleyen bir hata alırsanız, bölgenin 151 GB gerektirdiğini unutmayın.

Tebrikler! Artık Azure 'da bir IBM ana bilgisayar ortamı çalıştırıyorsunuz.

## <a name="learn-more"></a>Daha fazla bilgi edinin

- [Ana bilgisayar geçişi: Myon ve olgular](https://docs.microsoft.com/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/myths-and-facts)
- [Azure 'da IBM DB2 pureScale](https://docs.microsoft.com/azure/virtual-machines/linux/ibm-db2-purescale-azure)
- [Sorun giderme](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/)
- [Azure geçişine Demystifying ana bilgisayar](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/)

<!-- INTERNAL LINKS -->
[microfocus-get-started]: /microfocus/get-started.md
[microfocus-setup]: /microfocus/set-up-micro-focus-on-azure.md
[microfocus-demo]: /microfocus/demo.md
[ibm-get-started]: /ibm/get-started.md
[ibm-install-z]: install-ibm-z-environment.md
[ibm-demo]: /ibm/demo.md
