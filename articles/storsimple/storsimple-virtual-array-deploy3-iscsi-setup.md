---
title: Microsoft Azure StorSimple Sanal dizi Iscsı sunucusu kurulumu | Microsoft Docs
description: İlk kurulumun nasıl gerçekleştirileceğini, StorSimple Iscsı sunucunuzu nasıl kaydedeceğinizi ve cihaz kurulumunu nasıl tamamlayabileceğinizi açıklar.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 4db116d1-978b-48e8-b572-a719a8425dbc
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/25/2019
ms.author: alkohli
ms.openlocfilehash: 4560ca2b07826e2a071f515f147dfab8cbec3624
ms.sourcegitcommit: f5cc71cbb9969c681a991aa4a39f1120571a6c2e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68516809"
---
# <a name="deploy-storsimple-virtual-array--set-up-as-an-iscsi-server-via-azure-portal"></a>StorSimple Sanal dizisi dağıtma – Azure portal aracılığıyla bir Iscsı sunucusu olarak ayarlama

![iSCSI kurulum işlem akışı](./media/storsimple-virtual-array-deploy3-iscsi-setup/iscsi4.png)

## <a name="overview"></a>Genel Bakış

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

Bu dağıtım öğreticisi Microsoft Azure StorSimple Sanal dizisine yöneliktir. Bu öğreticide, İlk kurulumun nasıl gerçekleştirileceği, StorSimple Iscsı sunucunuzun nasıl kaydedileceği, cihaz kurulumunun nasıl tamamlanacağı ve ardından, StorSimple Sanal diziniz üzerinde bir Iscsı sunucusu olarak yapılandırılmış birimleri oluşturma, bağlama, başlatma ve biçimlendirme işlemleri açıklanmaktadır. 

Burada açıklanan yordamların tamamlanması yaklaşık 30 dakika ila 1 saat sürer. Bu makalede yayımlanan bilgiler yalnızca StorSimple Sanal dizileri için geçerlidir.

## <a name="setup-prerequisites"></a>Kurulum önkoşulları

StorSimple Sanal dizinizi yapılandırmadan ve ayarlamadan önce şunları yaptığınızdan emin olun:

* Sanal bir dizi sağladınız ve bu ağa bağlı [StorSimple Sanal dizisi dağıtma bölümünde açıklandığı şekilde, Hyper-V ' d e sanal dizi sağlama](storsimple-ova-deploy2-provision-hyperv.md) veya [StorSimple Sanal dizisi dağıtma-VMware 'de bir sanal dizi sağlama](storsimple-virtual-array-deploy2-provision-vmware.md).
* StorSimple Sanal dizlerinizi yönetmek için oluşturduğunuz StorSimple Aygıt Yöneticisi hizmetinden hizmet kayıt anahtarınız vardır. Daha fazla bilgi için bkz **. 2. Adım:**  [StorSimple Sanal dizisi dağıtma](storsimple-virtual-array-deploy1-portal-prep.md#step-2-get-the-service-registration-key)bölümünde hizmet kayıt anahtarını alın-portalı hazırlayın.
* Bu, var olan bir StorSimple Aygıt Yöneticisi hizmetine kaydolduğunuz ikinci veya sonraki sanal diziyse, hizmet veri şifreleme anahtarınız olmalıdır. Bu anahtar, ilk cihaz bu hizmete başarıyla kaydedildiğinde oluşturulmuştur. Bu anahtarı kaybettiyseniz, [StorSimple Sanal dizinizi yönetmek Için Web Kullanıcı arabirimini kullanma](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key)bölümündeki **hizmet veri şifreleme anahtarını edinme** bölümüne bakın.

## <a name="step-by-step-setup"></a>Adım adım kurulum

StorSimple Sanal dizinizi ayarlamak ve yapılandırmak için aşağıdaki adım adım yönergeleri kullanın:

* [1. Adım: Yerel Web Kullanıcı arabirimi kurulumunu tamamlayıp cihazınızı kaydedin](#step-1-complete-the-local-web-ui-setup-and-register-your-device)
* 2\. adım: Gerekli cihaz kurulumunu doldurun
* [Adım 3: Birim ekleme](#step-3-add-a-volume)
* [4. Adım: Bir birimi bağlama, başlatma ve biçimlendirme](#step-4-mount-initialize-and-format-a-volume)

## <a name="step-1-complete-the-local-web-ui-setup-and-register-your-device"></a>1\. adım: Yerel Web Kullanıcı arabirimi kurulumunu tamamlayıp cihazınızı kaydedin

#### <a name="to-complete-the-setup-and-register-the-device"></a>Kurulumu tamamlayıp cihazı kaydetme

1. Tarayıcı penceresini açın. Web Kullanıcı arabirimi türüne bağlanmak için:
   
    `https://<ip-address of network interface>`
   
    Önceki adımda belirtilen bağlantı URL 'sini kullanın. Web sitesinin güvenlik sertifikasıyla ilgili bir sorun olduğunu bildiren bir hata görürsünüz. **Bu Web sayfasına devam et ' e**tıklayın.
   
    ![Güvenlik sertifikası hatası](./media/storsimple-virtual-array-deploy3-iscsi-setup/image3.png)
2. Sanal cihazınızın Web Kullanıcı arabiriminde **Storsimpleadmin**olarak oturum açın. Adım 3 ' te değiştirdiğiniz cihaz yönetici parolasını girin: [StorSimple Sanal dizisinde sanal cihaz başlatın-Hyper-V ' d a sanal cihaz sağlayın](storsimple-virtual-array-deploy2-provision-hyperv.md) veya [StorSimple Sanal dizisi dağıtın-VMware 'de bir sanal cihaz sağlayın](storsimple-virtual-array-deploy2-provision-vmware.md).
   
    ![Oturum açma sayfası](./media/storsimple-virtual-array-deploy3-iscsi-setup/image4.png)
3. **Giriş** sayfasına yönlendirilirsiniz. Bu sayfada, sanal cihazı StorSimple Aygıt Yöneticisi hizmetiyle yapılandırmak ve kaydetmek için gereken çeşitli ayarlar açıklanmaktadır. **Ağ ayarları**, **Web proxy ayarları**ve **zaman ayarlarının** isteğe bağlı olduğunu unutmayın. Yalnızca **cihaz ayarları** ve **bulut ayarları**gereklidir.
   
    ![Giriş sayfası](./media/storsimple-virtual-array-deploy3-iscsi-setup/image5.png)
4. Ağ **ayarları** sayfasında, **ağ arabirimleri**altında, veri 0 sizin için otomatik olarak yapılandırılır. Her ağ arabirimi otomatik olarak bir IP adresi almak için varsayılan olarak ayarlanır (DHCP). Bu nedenle, bir IP adresi, alt ağ ve ağ geçidi otomatik olarak atanır (hem IPv4 hem de IPv6 için).
   
    Cihazınızı bir Iscsı sunucusu olarak dağıtmayı planlarken (blok depolama sağlamak için), **IP adresini otomatik olarak al** seçeneğini devre dışı bırakmanızı ve statik IP adreslerini yapılandırmanızı öneririz.
   
    ![Ağ ayarları sayfası](./media/storsimple-virtual-array-deploy3-iscsi-setup/image6.png)
   
    Cihazı sağlama sırasında birden fazla ağ arabirimi eklediyseniz, bunları buradan yapılandırabilirsiniz. Ağ arabiriminizi yalnızca IPv4 olarak veya hem IPv4 hem de IPv6 olarak yapılandırabileceğinizi aklınızda bulabilirsiniz. Yalnızca IPv6 konfigürasyonları desteklenmez.
5. Cihazınız bulut depolama hizmeti sağlayıcılarınız ile iletişim kurmaya çalıştığında veya bir dosya sunucusu olarak yapılandırıldıysa cihazınızı ada göre çözümlemek için kullanıldığından, DNS sunucuları gereklidir. **Ağ ayarları** sayfasında, **DNS sunucuları**altında:
   
   1. Birincil ve ikincil DNS sunucusu otomatik olarak yapılandırılır. Statik IP adreslerini yapılandırmayı seçerseniz, DNS sunucuları belirtebilirsiniz. Yüksek kullanılabilirlik için, birincil ve ikincil bir DNS sunucusu yapılandırmanızı öneririz.
   2. **Uygula**'ya tıklayın. Bu, ağ ayarlarını uygular ve doğrular.
6. **Cihaz ayarları** sayfasında:
   
   1. Cihazınıza benzersiz bir **ad** atayın. Bu ad 1-15 karakter olabilir ve harf, rakam ve kısa çizgi içerebilir.
   2. Oluşturmakta olduğunuz cihaz **türü** için ![ **iSCSI sunucusu** simgesi](./media/storsimple-virtual-array-deploy3-iscsi-setup/image7.png) iSCSI sunucusu simgesine tıklayın. Bir Iscsı sunucusu, blok depolama sağlamanıza olanak tanır.
   3. Bu cihazın etki alanına katılmış olmasını istediğinizi belirtin. Cihazınız bir Iscsı sunucusu ise, etki alanına katılma isteğe bağlıdır. Iscsı sunucunuzu bir etki alanına katmamaya karar verirseniz, **Uygula**' ya tıklayın, ayarların uygulanmasını bekleyin ve sonra bir sonraki adıma atlayın.
      
       Cihazı bir etki alanına katmak istiyorsanız. Bir **etki alanı adı**girin ve ardından **Uygula**' ya tıklayın.
      
      > [!NOTE]
      > Iscsı sunucunuza bir etki alanına katılırken, sanal diziniz Microsoft Azure Active Directory için kendi kuruluş biriminde (OU) olduğundan ve buna hiçbir Grup İlkesi nesnesi (GPO) uygulanmadığından emin olun.
      > 
      > 
   4. Bir iletişim kutusu görüntülenir. Etki alanı kimlik bilgilerinizi belirtilen biçimde girin. Onay simgesine tıklayın ![onay simgesi](./media/storsimple-virtual-array-deploy3-iscsi-setup/image15.png). Etki alanı kimlik bilgileri doğrulanacak. Kimlik bilgileri yanlışsa bir hata iletisi görürsünüz.
      
       ![kimlik bilgileri](./media/storsimple-virtual-array-deploy3-iscsi-setup/image8.png)
   5. **Uygula**'ya tıklayın. Bu işlem cihaz ayarlarını uygular ve doğrular.
7. (İsteğe bağlı olarak) Web Proxy sunucunuzu yapılandırın. Web proxy yapılandırması isteğe bağlı olsa da, bir Web proxy 'si kullanıyorsanız, burada yalnızca yapılandırabilirsiniz.
   
    ![Web proxy 'yi yapılandırma](./media/storsimple-virtual-array-deploy3-iscsi-setup/image9.png)
   
    **Web proxy** sayfasında:
   
   1. **Web proxy URL 'sini** şu biçimde sağlayın: *http:\//Host-IP adresi* veya *FQDN: bağlantı noktası numarası*. HTTPS URL 'Lerinin desteklenmediğini unutmayın.
   2. **Kimlik doğrulamasını** **temel** veya **yok**olarak belirtin.
   3. Kimlik doğrulaması kullanıyorsanız, ayrıca bir **Kullanıcı adı** ve **parola**sağlamanız gerekir.
   4. **Uygula**'ya tıklayın. Bu, yapılandırılmış Web proxy ayarlarını doğrular ve uygular.
8. (İsteğe bağlı olarak) cihazınız için saat dilimi ve birincil ve ikincil NTP sunucuları gibi zaman ayarlarını yapılandırın. Cihazınızın bulut hizmeti sağlayıcılarınız ile kimlik doğrulaması yapabilmesi için zaman eşitlemesini gerektiğinden NTP sunucuları gereklidir.
   
    ![Saat ayarları](./media/storsimple-virtual-array-deploy3-iscsi-setup/image10.png)
   
    **Zaman ayarları** sayfasında:
   
   1. Aşağı açılan listeden, cihazın dağıtıldığı coğrafi konuma göre **saat dilimini** seçin. Cihazınızın varsayılan saat dilimi PST ' dir. Cihazınız zamanlanan tüm işlemler için bu saat dilimini kullanır.
   2. Cihazınız için bir **BIRINCIL NTP sunucusu** belirtin veya Time.Windows.com varsayılan değerini kabul edin. Ağınızın, NTP trafiğini veri merkezinizden İnternete geçirilmesine izin vermesini sağlayın.
   3. İsteğe bağlı olarak, cihazınız için bir **IKINCIL NTP sunucusu** belirtin.
   4. **Uygula**'ya tıklayın. Bu, yapılandırılan zaman ayarlarını doğrular ve uygular.
9. Cihazınız için bulut ayarlarını yapılandırın. Bu adımda, yerel cihaz yapılandırmasını tamamlayacaksınız ve cihazı StorSimple Aygıt Yöneticisi hizmetinize kaydedecaksınız.
   
   1. 2\. **adımda aldığınız **hizmet kayıt anahtarını** girin:**  [StorSimple Sanal dizisi dağıtma](storsimple-virtual-array-deploy1-portal-prep.md#step-2-get-the-service-registration-key)bölümünde hizmet kayıt anahtarını alın-portalı hazırlayın.
   2. Bu hizmete kaydolduğunuz ilk cihaz bu değilse, **hizmet veri şifreleme anahtarını**sağlamanız gerekir. Bu anahtar, StorSimple Aygıt Yöneticisi hizmetiyle ek cihazlar kaydetmek için hizmet kayıt anahtarıyla birlikte gereklidir. Daha fazla bilgi için yerel Web Kullanıcı arabiriminizdeki [hizmet veri şifreleme anahtarını edinme](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) bölümüne bakın.
   3. **Kaydol**' a tıklayın. Bu işlem cihazı yeniden başlatacak. Cihaz başarıyla kaydedilmeden önce 2-3 dakika beklemeniz gerekebilir. Cihaz yeniden başlatıldıktan sonra oturum açma sayfasına yönlendirilirsiniz.
      
      ![Cihazı kaydet](./media/storsimple-virtual-array-deploy3-iscsi-setup/image11.png)
10. Azure portalına dönün.
11. Hizmetinizin **cihazlar** dikey penceresine gidin. Çok sayıda kaynağınız varsa **tüm kaynaklar**' a tıklayın, hizmet adı ' na tıklayın (gerekirse arama yapın) ve ardından **cihazlar**' a tıklayın.
12. **Cihazlar** dikey penceresinde, durum ' u arayarak cihazın hizmete başarıyla bağlandığını doğrulayın. Cihazın durumu **Kuruluma hazır** olmalıdır.
    
    ![Cihazı kaydet](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis1m.png)

## <a name="step-2-configure-the-device-as-iscsi-server"></a>2\. adım: Cihazı Iscsı sunucusu olarak yapılandırma

Gerekli cihaz kurulumunu tamamlamak için Azure portal aşağıdaki adımları gerçekleştirin.

#### <a name="to-configure-the-device-as-iscsi-server"></a>Cihazı Iscsı sunucusu olarak yapılandırmak için

1. StorSimple Aygıt Yöneticisi hizmetinize gidin ve **yönetim > cihazlar**' a gidin. **Cihazlar** dikey penceresinde, yeni oluşturduğunuz cihazı seçin. Bu cihaz, **ayarlamaya hazırlanıyor**olarak görünür.
   
    ![Cihazı Iscsı sunucusu olarak yapılandırma](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis1m.png) 
2. Cihaza tıklayın ve cihazın kuruluma hazırlandığını belirten bir başlık iletisi görürsünüz.
   
    ![Cihazı Iscsı sunucusu olarak yapılandırma](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis2m.png)  
3. Cihaz komut çubuğunda **Yapılandır** ' a tıklayın. Bu, **Yapılandır** dikey penceresini açar. **Yapılandır** dikey penceresinde aşağıdakileri yapın:
   
   * Iscsı sunucusu adı otomatik olarak doldurulur.
   * Bulut depolama şifrelemesi 'nin **etkin**olarak ayarlandığından emin olun. Bu, cihazdan buluta gönderilen verilerin şifrelenmesini sağlar.
   * 32 karakterlik bir şifreleme anahtarı belirtin ve daha sonra başvurmak üzere anahtar yönetim uygulamasına kaydedin.
   * Aygıtınızla kullanılacak bir depolama hesabı seçin. Bu abonelikte, mevcut bir depolama hesabını seçebilir veya farklı bir abonelikteki bir hesabı seçmek için **Ekle** ' ye tıklayabilirsiniz.
     
     ![Cihazı Iscsı sunucusu olarak yapılandırma](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis4m.png)
4. Iscsı sunucusunu ayarlamayı gerçekleştirmek için **Yapılandır** ' a tıklayın.
   
    ![Cihazı Iscsı sunucusu olarak yapılandırma](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis5m.png) 
5. Iscsı sunucusu oluşturma işleminin devam ettiğini size bildirilir. Iscsı sunucusu başarıyla oluşturulduktan sonra, **cihazlar** dikey penceresi güncelleştirilir ve ilgili cihaz durumu **çevrimiçi**olur.
   
    ![Cihazı Iscsı sunucusu olarak yapılandırma](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis9m.png)

## <a name="step-3-add-a-volume"></a>3\. adım: Birim ekleme

1. **Cihazlar** dikey penceresinde, yeni bir iSCSI sunucusu olarak yapılandırdığınız aygıtı seçin. Tıklayın **..** . (alternatif olarak, bu satıra sağ tıklayın) ve bağlam menüsünden **Birim Ekle**' yi seçin. Ayrıca komut çubuğundan **+ Birim Ekle** ' ye de tıklayabilirsiniz. Bu, **Birim Ekle** dikey penceresini açar.
   
    ![Birim ekleme](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis10m.png)
2. **Birim Ekle** dikey penceresinde aşağıdakileri yapın:
   
   * **Birim adı** alanına biriminiz için benzersiz bir ad girin. Ad 3 ile 127 arasında karakter içeren bir dize olmalıdır.
   * **Tür** açılan listesinde, **katmanlı** veya **yerel olarak sabitlenmiş** bir birim oluşturulup oluşturulmayacağını belirtin. Yerel garanti, düşük gecikme süreleri ve daha yüksek performans gerektiren iş yükleri için **yerel olarak sabitlenmiş** **birim**' i seçin. Diğer tüm veriler için **katmanlı** **birim**' i seçin.
   * **Kapasite** alanında birimin boyutunu belirtin. Katmanlı bir birim 500 GB ile 5 TB arasında olmalıdır ve yerel olarak sabitlenmiş bir birim 50 GB ile 500 GB arasında olmalıdır.
     
     Yerel olarak sabitlenmiş bir birim bol alanla sağlandı ve birimdeki birincil verilerin cihazda kalmasını ve buluta taşımamasını sağlar.
     
     Diğer yandan katmanlı bir birim ölçülü kaynak temin edilir. Katmanlı bir birim oluşturduğunuzda, alanın yaklaşık% 10 ' u yerel katmanda sağlanır ve alanın% 90 ' unun bulutta sağlanması gerekir. Örneğin, 1 TB 'lik bir birim sağladıysanız, 100 GB yerel alanda yer alır ve veri katmanları sırasında bulutta 900 GB kullanılır. Buna karşılık bu, cihazdaki tüm yerel alanı kapatdıysanız katmanlı bir paylaşımın sağlayamayacağı (% 10 ' u kullanılamadığından) Bu da geçerlidir.
     
     ![Birim ekleme](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis12.png)
   * **Bağlı konaklar**' a tıklayın, bu birime bağlamak istediğiniz iSCSI başlatıcısına karşılık gelen bir erişim denetimi kaydı (ACR) seçin ve ardından **Seç**' e tıklayın. <br><br> 
3. Yeni bir bağlı konak eklemek için **Yeni Ekle**' ye tıklayın, ana bilgisayar ve Iscsı tam adı (IQN) için bir ad girin ve ardından **Ekle**' ye tıklayın. IQN yoksa, ek A 'ya [gidin: Bir Windows Server konağının](#appendix-a-get-the-iqn-of-a-windows-server-host)IQN 'sini alın.
   
      ![Birim ekleme](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis15m.png)
4. Biriminiz yapılandırmayı tamamladığınızda **Tamam**' a tıklayın. Belirtilen ayarlarla bir birim oluşturulur ve bir bildirim görürsünüz. Varsayılan olarak, izleme ve yedekleme birim için etkinleştirilir.
   
     ![Birim ekleme](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis18m.png)
5. Birimin başarıyla oluşturulduğunu doğrulamak için **birimler** dikey penceresine gidin. Listelenen birimi görmeniz gerekir.
   
   ![Birim ekleme](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis20m.png)

## <a name="step-4-mount-initialize-and-format-a-volume"></a>4\. Adım: Bir birimi bağlayın, başlatın ve biçimlendirin

Bir Windows Server konağında StorSimple birimlerinizi bağlamak, başlatmak ve biçimlendirmek için aşağıdaki adımları gerçekleştirin.

#### <a name="to-mount-initialize-and-format-a-volume"></a>Birimi bağlamak, başlatmak ve biçimlendirmek için

1. **İSCSI başlatıcısı** uygulamasını uygun sunucuda açın.
2. **iSCSI Başlatıcısı Özellikleri** penceresinin **Keşif** sekmesinde, **Portal Bul**’a tıklayın.
   
    ![portalı bul](./media/storsimple-virtual-array-deploy3-iscsi-setup/image22.png)
3. **Hedef Portal Bul** iletişim kutusuna iSCSI etkin ağ arabiriminin IP adresini girin ve ardından **Tamam**’a tıklayın.
   
    ![IP adresi](./media/storsimple-virtual-array-deploy3-iscsi-setup/image23.png)
4. **iSCSI Başlatıcısı Özellikleri** penceresinin **Hedefler** sekmesinde **Bulunan hedefler**’i bulun. (Her birim bulunan bir hedef olacaktır.) Cihaz durumu **Etkin Olmayan** olarak görünmelidir.
   
    ![bulunan hedefler](./media/storsimple-virtual-array-deploy3-iscsi-setup/image24.png)
5. Bir hedef cihaz seçin ve ardından **Bağlan**' a tıklayın. Cihaz bağlandıktan sonra durum **Bağlandı** olarak değişmelidir. (Microsoft Iscsı başlatıcısını kullanma hakkında daha fazla bilgi için bkz. [Microsoft Iscsı Başlatıcısı 'Nı yükleme ve yapılandırma][1].
   
    ![hedef cihaz seçin](./media/storsimple-virtual-array-deploy3-iscsi-setup/image25.png)
6. Windows konağında Windows Logo + X tuşlarına basıp **Çalıştır**’a tıklayın.
7. **Çalıştır** iletişim kutusuna **Diskmgmt.msc** yazın. **Tamam**’a tıklayın, **Disk Yönetimi** iletişim kutusu görüntülenir. Sağ bölmede, konaktaki birimler gösterilir.
8. **Disk Yönetimi** penceresinde bağlanan birimler aşağıdaki çizimde gösterildiği gibi görünür. Bulunan birime sağ tıklayın (disk adına tıklayın) ve ardından **Çevrimiçi**’ne tıklayın.
   
    ![Disk Yönetimi](./media/storsimple-virtual-array-deploy3-iscsi-setup/image26.png)
9. Sağ tıklayın ve **Diski Başlat**' ı seçin.
   
    ![diski başlatma 1](./media/storsimple-virtual-array-deploy3-iscsi-setup/image27.png)
10. İletişim kutusunda, başlatılacak diskleri seçin ve ardından **Tamam**' a tıklayın.
    
    ![Diski Başlat 2](./media/storsimple-virtual-array-deploy3-iscsi-setup/image28.png)
11. Yeni Basit Birim Sihirbazı başlatılır. Bir disk boyutu seçin ve ardından **İleri**' ye tıklayın.
    
    ![Yeni Birim Sihirbazı 1](./media/storsimple-virtual-array-deploy3-iscsi-setup/image29.png)
12. Birime bir sürücü harfi atayın ve ardından **İleri**' ye tıklayın.
    
    ![Yeni Birim Sihirbazı 2](./media/storsimple-virtual-array-deploy3-iscsi-setup/image30.png)
13. Birimi biçimlendirmek için parametreleri girin. **Windows Server 'da yalnızca NTFS desteklenir.** Ayırma birimi boyutunu 64K olarak ayarlayın. Biriminiz için bir etiket girin. Bu adın, StorSimple Sanal diziniz üzerinde belirttiğiniz birim adıyla aynı olması önerilen en iyi uygulamadır.           **İleri**'ye tıklayın.
    
    ![Yeni Birim Sihirbazı 3](./media/storsimple-virtual-array-deploy3-iscsi-setup/image31.png)
14. Biriminizin değerlerini denetleyip **son**' a tıklayın.
    
    ![Yeni Birim Sihirbazı 4](./media/storsimple-virtual-array-deploy3-iscsi-setup/image32.png)
    
    Birimler, **disk yönetimi** sayfasında **çevrimiçi** olarak görünür.
    
    ![çevrimiçi birimler](./media/storsimple-virtual-array-deploy3-iscsi-setup/image33.png)

## <a name="next-steps"></a>Sonraki adımlar

[StorSimple Sanal dizinizi yönetmek](storsimple-ova-web-ui-admin.md)için yerel Web Kullanıcı arabirimini nasıl kullanacağınızı öğrenin.

## <a name="appendix-a-get-the-iqn-of-a-windows-server-host"></a>Ek A: Bir Windows Server konağının IQN’ini alın

Windows Server 2012 çalıştıran bir Windows konağının iSCSI Tam Adını (IQN) almak için aşağıdaki adımları gerçekleştirin.

#### <a name="to-get-the-iqn-of-a-windows-host"></a>Windows konağının IQN’sini almak için

1. Microsoft iSCSI başlatıcısını Windows konağında başlatın.
2. **iSCSI Başlatıcısı Özellikleri** penceresinin **Yapılandırma** sekmesinde, **Başlatıcı Adı** alanından dizeyi seçip kopyalayın.
   
    ![iSCSI başlatıcısı özellikleri](./media/storsimple-virtual-array-deploy3-iscsi-setup/image34.png)
3. Bu dizeyi kaydedin.

<!--Reference link-->
[1]: https://technet.microsoft.com/library/ee338480(WS.10).aspx



