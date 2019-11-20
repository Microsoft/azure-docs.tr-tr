---
title: Azure 'da IBM zD & T geliştirme ve test ortamı 'nı yükler | Microsoft Docs
description: Azure sanal makinesi (VM) hizmet olarak altyapı (IaaS) üzerinde IBM Z geliştirme ve test ortamı (zD & T) dağıtın.
services: virtual-machines-linux
ms.service: virtual-machines-linux
documentationcenter: ''
author: njray
ms.author: edprice
manager: edprice
editor: edprice
ms.topic: conceptual
ms.date: 04/02/2019
tags: ''
keywords: ''
ms.openlocfilehash: 67af4eae03b773fad9cf38964152c1fb9d623bd5
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72025945"
---
# <a name="install-ibm-zdt-devtest-environment-on-azure"></a>Azure 'da IBM zD & T geliştirme ve test ortamı 'nı yükler

IBM Z sistemlerinde anabilgisayar iş yükleri için bir geliştirme/test ortamı oluşturmak üzere Azure sanal makinesi (VM) altyapısına hizmet olarak (IaaS) IBM Z geliştirme ve test ortamı (zD & T) dağıtabilirsiniz.

ZD & T sayesinde, daha az kritik geliştirme ve test ortamlarınız için x86 platformunun maliyet tasarruflarından yararlanabilir ve sonra güncelleştirmeleri bir Z sistem üretim ortamına geri gönderebilirsiniz. Daha fazla bilgi için bkz. [IBM ZD & T yükleme yönergeleri](https://www-01.ibm.com/support/docview.wss?uid=swg24044565#INSTALL).

Azure ve Azure Stack aşağıdaki sürümleri destekler:

- zD & T Personal sürümü
- zD & T Paralel Sysplex
- zD & T Enterprise Edition

Tüm zD & T sürümleri, Windows Server değil yalnızca x86 Linux sistemlerinde çalışır. Enterprise Edition, Red Hat Enterprise Linux (RHEL) veya Ubuntu/Delk üzerinde desteklenmektedir. Azure için hem RHEL hem de Dey VM görüntüleri kullanılabilir.

Bu makalede, ortamların oluşturulması ve yönetilmesi için zD & T Enterprise Edition Web sunucusunu kullanabilmeniz için Azure 'da zD & T Enterprise Edition 'ı nasıl ayarlayabilmeniz gösterilmektedir. ZD & T yüklemesi herhangi bir ortam yüklemez. Bunları yükleme paketleri olarak ayrı oluşturmanız gerekir. Örneğin, uygulama geliştiricileri denetimli dağıtımlar (ADCD), test ortamlarının birim görüntüleridir. Bunlar, IBM 'den erişilebilen medya dağıtımındaki ZIP görüntülerinde bulunur. Bkz. [Azure 'da BIR ADCD ortamı ayarlama](demo.md).

Daha fazla bilgi için bkz. IBM bilgi merkezi 'nde [zd & T 'ye genel bakış](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zdt.overview.gs.doc/topics/c_product_overview.html) .

Bu makalede, Azure 'da Z geliştirme ve test ortamı (zD & T) Enterprise Edition 'ın nasıl ayarlanacağı gösterilmektedir. Daha sonra, Azure 'da Z tabanlı ortamlar oluşturmak ve yönetmek için zD & T Enterprise Edition Web sunucusunu kullanabilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

> [!NOTE]
> IBM, zD & T Enterprise Edition 'ın yalnızca geliştirme ve test ortamlarında yüklenmesine izin verir; üretim ortamları*değildir* .

- Azure aboneliği. Aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

- Yalnızca IBM müşterileri ve iş ortakları tarafından kullanılabilen medyaya erişmeniz gerekir. Daha fazla bilgi için, IBM temsilcinizle iletişim kurun veya [ZD & T](https://www.ibm.com/us-en/marketplace/z-systems-development-test-environment) Web sitesinde iletişim bilgilerini görüntüleyin.

- Bir [Lisans sunucusu](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.tools.user.guide.doc/topics/zdt_ee.html). Bu, ortamlara erişim için gereklidir. Bunu oluşturduğunuz şekilde, IBM 'den yazılım lisansınıza göre farklılık gösterir:

     - **Donanım tabanlı lisanslama sunucusu** , yazılımın tüm bölümlerine erişmek için gerekli olan Rational belirteçlerini IÇEREN bir USB donanım cihazı gerektirir. Bunu IBM 'den edinmeniz gerekir.

     - **Yazılım tabanlı lisanslama sunucusu** , lisanslama anahtarlarının yönetimi için merkezi bir sunucu ayarlamanızı gerektirir. Bu yöntem tercih edilir ve, yönetim sunucusunda IBM 'den aldığınız anahtarları ayarlamanızı gerektirir.

## <a name="create-the-base-image-and-connect"></a>Temel görüntüyü oluşturma ve bağlanma

1. Azure portal, istediğiniz işletim sistemi yapılandırması ile [BIR VM oluşturun](/azure/virtual-machines/linux/quick-create-portal) . Bu makalede Ubuntu 16,04 çalıştıran bir B4ms VM (4 vCPU ve 16 GB bellek ile) varsayılır.

2. VM oluşturulduktan sonra SSH için 22 numaralı gelen bağlantı noktalarını, FTP için 21, Web sunucusu için 9443 ' yi açın.

3. **Bağlan** DÜĞMESI aracılığıyla VM 'Nin **genel bakış** dikey penceresinde gösterilen SSH kimlik bilgilerini alın. **SSH** sekmesini SEÇIN ve SSH oturum açma komutunu panoya kopyalayın.

4. Yerel BILGISAYARıNıZDAN bir [bash kabuğu](/azure/cloud-shell/quickstart) 'nda oturum açın ve komutu yapıştırın. **SSH @ no__t-1User ID @ no__t-2 @ no__t-3 @ no__t-4ıP Address @ no__t-5**biçiminde olacaktır. Kimlik bilgileriniz istendiğinde, Giriş dizininize bir bağlantı kurmak için bu bilgileri girin.

## <a name="copy-the-installation-file-to-the-server"></a>Yükleme dosyasını sunucuya Kopyala

Web sunucusunun yükleme dosyası **Zdt @ no__t-1ınstall @ no__t-2EE\_V12.0.0.1.tgz**. IBM tarafından sağlanan medyaya dahildir. Bu dosyayı Ubuntu sanal makinenize yüklemeniz gerekir.

1. Yeni oluşturulan görüntüde her şeyin güncel olduğundan emin olmak için komut satırından aşağıdaki komutu girin:

    ```
    sudo apt-get update
    ```

2. Yüklenecek dizini oluşturun:

    ```
    mkdir ZDT
    ```

3. Yerel makinenizden dosyayı sanal makineye kopyalayın:

    ```
    scp ZDT_Install_EE_V12.0.0.1.tgz  your_userid@<IP Address /ZDT>   =>
    ```
    
> [!NOTE]
> Bu komut, yükleme dosyasını ana dizininizdeki ZDT dizinine kopyalar ve bu, istemcinizin Windows veya Linux çalışmasına bağlı olarak farklılık gösterir.

## <a name="install-the-enterprise-edition"></a>Enterprise Edition 'ı yükleyin

1. ZDT dizinine gidin ve aşağıdaki komutları kullanarak ZDT @ no__t-0ınstall @ no__t-1EE\_V12.0.0.1.tgz dosyasını açın:

    ```
    cd ZDT
    chmod 755 ZDT\_Install\_EE\_V12.0.0.0.tgz
    ```

2. Yükleyiciyi çalıştırın:

    ```
    ./ZDT_Install_EE_V12.0.0.0.x86_64
    ```

3. Enterprise Server 'ı yüklemek için **1** ' i seçin.

4. **ENTER** tuşuna basın ve lisans sözleşmelerini dikkatle okuyun. Lisansın sonunda devam etmek için **Evet** girin.

5. Yeni oluşturulan kullanıcı için parolayı değiştirmeniz istendiğinde, **ibmsys1**, **sudo passwd ibmsys1** komutunu kullanın ve yeni parolayı girin.

6. Yüklemenin başarılı olup olmadığını doğrulamak için

    ```
    dpkg -l | grep zdtapp
    ```

7. Çıkışın, paket gaz 'nin başarıyla yüklendiğini gösteren **zdtapp 12.0.0.0**dizesini içerdiğini doğrulayın

### <a name="starting-enterprise-edition"></a>Enterprise Edition başlatılıyor

Web sunucusu başlatıldığında, yükleme işlemi sırasında oluşturulan zD & T Kullanıcı KIMLIĞI altında çalıştığını aklınızda bulundurun.

1. Web sunucusunu başlatmak için aşağıdaki komutu çalıştırmak üzere kök kullanıcı KIMLIĞINI kullanın:

    ```
    sudo /opt/ibm/zDT/bin/startServer
    ```

2. URL çıkışını betiğe kopyalayın, şöyle görünür:

    ```
    https://<your IP address or domain name>:9443/ZDTMC/login.htm
    ```

3. ZD & T yüklemenizin yönetim bileşenini açmak için URL 'YI bir Web tarayıcısına yapıştırın.

## <a name="next-steps"></a>Sonraki adımlar

[IBM zD & T v1 'de uygulama geliştiricileri denetimli dağıtımı (ADCD) ayarlama](./demo.md)
