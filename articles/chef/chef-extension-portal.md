---
title: Azure portal Chef istemcisini yükleyip
description: Chef istemcinizi Azure portal dağıtma ve yapılandırma hakkında bilgi edinin
keywords: Azure, Chef, DevOps, istemci, yüklemesi, Portal
ms.date: 05/15/2018
ms.topic: article
ms.openlocfilehash: f8707c2fe39fb794381af298c24d27704b1ec255
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158267"
---
# <a name="install-the-chef-client-from-the-azure-portal"></a>Azure portal Chef istemcisini yükleyip
Chef istemci uzantısını, Azure portal doğrudan bir Linux veya Windows makinesine ekleyebilirsiniz. Bu makalede, yeni bir Linux sanal makinesini kullanarak işlem adım adım açıklanmaktadır.

## <a name="prerequisites"></a>Önkoşullar

- **Azure aboneliği**: Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) oluşturun.

- **Chef**: etkin bir Chef hesabınız yoksa, [barındırılan Chef 'in ücretsiz deneme](https://manage.chef.io/signup)sürümü için kaydolun. Bu makaledeki yönergelerden daha fazla bilgi almak için Chef hesabınızdan aşağıdaki değerlere ihtiyacınız olacaktır:
  - organization_validation anahtarı
  - rapor
  - run_list

## <a name="install-the-chef-extension-on-a-new-linux-virtual-machine"></a>Chef uzantısını yeni bir Linux sanal makinesine yükler
Bu bölümde öncelikle Azure portal bir Linux makinesi oluşturmak için kullanacaksınız. İşlem sırasında, Chef uzantısının yeni sanal makineye nasıl yükleneceğini de göreceksiniz.

1. [Azure portala](https://portal.azure.com) gidin.

1. Soldaki menüden **sanal makineler** seçeneğini belirleyin. **Sanal makineler** seçeneği yoksa, **tüm hizmetler** ' i seçin ve ardından **sanal makineler**' i seçin.

1. **Sanal makineler** sekmesinde, **Ekle**' yi seçin.

    ![Azure portal yeni bir sanal makine ekleyin](./media/chef-extension-portal/add-vm.png)

1. **İşlem** sekmesinde, istediğiniz işletim sistemini seçin. Bu demo için **Ubuntu Server** seçilidir.

1. **Ubuntu sunucusu** sekmesinde **ubuntu Server 16,04 LTS**' yi seçin.

    ![Ubuntu sanal makinesi oluştururken, ihtiyacınız olan sürümü belirtin](./media/chef-extension-portal/ubuntu-server-version.png)

1. **Ubuntu Server 16,04 LTS** sekmesinde **Oluştur**' u seçin.

    ![Ubuntu, ürünü hakkında ek bilgiler sağlar](./media/chef-extension-portal/create-vm.png)

1. **Sanal makine oluştur** sekmesinde **temel bilgiler**' i seçin.

1. **Temel bilgiler** sekmesinde, aşağıdaki değerleri belirtin ve ardından **Tamam**' ı seçin.

   - **Ad** -yeni sanal makine için bir ad girin.
   - **VM disk türü** -depolama disk türü için **SSD** veya **HDD** belirtin. Azure 'daki sanal makine disk türleri hakkında daha fazla bilgi için, [bir disk türü seçme](../virtual-machines/windows/disks-types.md)makalesine bakın.
   - **Kullanıcı adı** -sanal makinede yönetici ayrıcalıklarına sahip bir Kullanıcı adı girin.
   - **Kimlik doğrulama türü** - **parola**seçin. **SSH ortak anahtarı**' nı da seçebilir ve bir SSH ortak anahtar değeri sağlayabilirsiniz. Bu demo (ve ekran görüntüleri) amaçları için **parola** seçilidir.
   - **Parola ve parolayı** **Onayla** -Kullanıcı için bir parola girin.
   - **Azure Active Directory Ile oturum açın** - **devre dışı**seçeneğini belirleyin.
   - **Abonelik** -birden fazla varsa, istenen Azure aboneliğini seçin.
   - **Kaynak grubu** -kaynak grubunuz için bir ad girin.
   - **Konum** - **Doğu ABD**seçin.

     ![Sanal makine oluşturmaya yönelik temel bilgiler sekmesi](./media/chef-extension-portal/add-vm-basics.png)

1. **Boyut seçin** sekmesinde, sanal makine için boyutu seçin ve ardından **Seç**' i seçin.

1. **Ayarlar** sekmesinde, önceki sekmelerde seçtiğiniz değerlere göre değerlerin büyük bir kısmının sizin için doldurulması gerekir. **Uzantılar**'ı seçin.

     ![Uzantılar, ayarlar sekmesi aracılığıyla sanal makinelere eklenir](./media/chef-extension-portal/add-vm-select-extensions.png)

1. **Uzantılar** sekmesinde **uzantı Ekle**' yi seçin.

     ![Bir sanal makineye uzantı eklemek için uzantı Ekle ' yi seçin](./media/chef-extension-portal/add-vm-add-extension.png)

1. **Yeni kaynak** sekmesinde **Linux Chef uzantısını (1.2.3)** seçin.

     ![Chef, Linux ve Windows sanal makineleri için uzantılara sahiptir](./media/chef-extension-portal/select-linux-chef-extension.png)

1. **Linux Chef uzantısı** sekmesinde **Oluştur**' u seçin.

1. **Uzantı yüklensin** sekmesinde, aşağıdaki değerleri belirtin ve ardından **Tamam**' ı seçin.

    - **Chef sunucu URL 'si** -kuruluş adını Içeren Chef sunucu URL 'sini girin, örneğin, *https://api.chef.io/organization/mycompany* .
    - **Chef düğüm adı** -Chef düğüm adını girin. Bu, herhangi bir değer olabilir.
    - **Çalıştırma listesi** -makineye eklenen Chef çalıştırma listesini girin. Bu boş bırakılabilir.
    - **Doğrulama Istemci adı** -Chef doğrulama istemci adını girin. Örneğin, *tarhma doğrulayıcısı*.
    - **Doğrulama anahtarı** -makinelerinizi önyükleme sırasında kullanılan doğrulama anahtarını içeren bir dosya seçin.
    - **Istemci yapılandırma dosyası** -Chef-Client için bir yapılandırma dosyası seçin. Bu boş bırakılabilir.
    - **Chef istemci sürümü** -yüklenecek Chef istemcisinin sürümünü girin. Bu boş bırakılabilir. Boş değer en son sürümü yüklenir.
    - **SSL doğrulama modu** - **hiçbiri** veya **eşi**seçin. Demo için *hiçbiri* seçilmedi.
    - **Chef ortamı** -bu düğümün üyesi olması gereken Chef ortamını girin. Bu boş bırakılabilir.
    - **Şifrelenmiş Databag gizli** -bu makinenin erişimi olması gereken şifreli Databag için gizli anahtarı içeren bir dosya seçin. Bu boş bırakılabilir.
    - **Chef sunucu SSL sertifikası** -Chef SUNUCUNUZA atanan SSL sertifikasını seçin. Bu boş bırakılabilir.

      ![Chef sunucusunu Linux sanal makinesine yükleme](./media/chef-extension-portal/install-extension.png)

1. **Uzantılar** sekmesine Dönzaman **Tamam**' ı seçin.

1. **Ayarlar** sekmesine Dönzaman **Tamam**' ı seçin.

1. **Oluştur** sekmesine dönzaman (seçtiğiniz ve girdiğiniz seçeneklerin özetini temsil eder), bilgileri ve **kullanım koşulları**doğrulayın ve **Oluştur**' u seçin.

Sanal makineyi Chef uzantısıyla oluşturma ve dağıtma işlemi tamamlandığında, bir bildirim işlemin başarısını veya başarısızlığını gösterir. Ayrıca, yeni sanal makine için kaynak sayfası, oluşturulduktan sonra otomatik olarak Azure portal açılır.

![Chef sunucusunu Linux sanal makinesine yükleme](./media/chef-extension-portal/resource-created.png)

## <a name="next-steps"></a>Sonraki adımlar

- [Chef kullanarak Azure 'da Windows sanal makinesi oluşturma](/azure/virtual-machines/windows/chef-automation)
