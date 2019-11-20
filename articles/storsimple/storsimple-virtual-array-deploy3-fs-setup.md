---
title: StorSimple Sanal dizisini dosya sunucusu olarak ayarlama | Microsoft Docs
description: StorSimple Sanal dizi dağıtımında bu üçüncü öğretici, bir sanal cihazı dosya sunucusu olarak ayarlamanıza olanak sağlar.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: f609f6ff-0927-48bb-a68a-6d8985d2fe34
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/25/2019
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c2d93099f0f76f173cc7e77ab7f24f27d1560835
ms.sourcegitcommit: f5cc71cbb9969c681a991aa4a39f1120571a6c2e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68516772"
---
# <a name="deploy-storsimple-virtual-array---set-up-as-file-server-via-azure-portal"></a>StorSimple Sanal dizisi dağıtma-Azure portal aracılığıyla dosya sunucusu olarak ayarlama
![](./media/storsimple-virtual-array-deploy3-fs-setup/fileserver4.png)

## <a name="introduction"></a>Giriş

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

Bu makalede ilk kurulum 'un nasıl gerçekleştirileceği, StorSimple dosya sunucunuzun nasıl kaydedileceği, cihaz kurulumunun nasıl tamamlanacağı ve SMB paylaşımları oluşturup bağlanmakta olduğu açıklanır. Bu, sanal dizinizi bir dosya sunucusu veya bir Iscsı sunucusu olarak tamamen dağıtmak için gereken dağıtım öğreticilerinin serisinin son makaledir.

Kurulum ve yapılandırma işleminin tamamlanması yaklaşık 10 dakika sürebilir. Bu makaledeki bilgiler yalnızca StorSimple Sanal dizisinin dağıtımı için geçerlidir. StorSimple 8000 serisi cihazların dağıtımı için şuraya gidin: [Güncelleştirme 2 çalıştıran StorSimple 8000 serisi cihazınızı dağıtın](storsimple-deployment-walkthrough-u2.md).

## <a name="setup-prerequisites"></a>Kurulum önkoşulları
StorSimple Sanal dizinizi yapılandırmadan ve ayarlamadan önce şunları yaptığınızdan emin olun:

* Bir sanal dizi sağladınız ve bu ağa bağlı olarak [Hyper-V ' d e StorSimple Sanal dizisi sağlama](storsimple-virtual-array-deploy2-provision-hyperv.md) veya [VMware 'de StorSimple Sanal dizisi sağlama](storsimple-virtual-array-deploy2-provision-vmware.md).
* StorSimple Sanal dizilerini yönetmek için oluşturduğunuz StorSimple Aygıt Yöneticisi hizmetinden hizmet kayıt anahtarınız vardır. Daha fazla bilgi için bkz [. 2. Adım: StorSimple Sanal dizisi için](storsimple-virtual-array-deploy1-portal-prep.md#step-2-get-the-service-registration-key) hizmet kayıt anahtarını alın.
* Bu, var olan bir StorSimple Aygıt Yöneticisi hizmetine kaydolduğunuz ikinci veya sonraki sanal diziyse, hizmet veri şifreleme anahtarınız olmalıdır. Bu anahtar, ilk cihaz bu hizmete başarıyla kaydedildiğinde oluşturulmuştur. Bu anahtarı kaybettiyseniz, bkz. StorSimple Sanal diziniz için [hizmet veri şifreleme anahtarını edinme](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) .

## <a name="step-by-step-setup"></a>Adım adım kurulum
StorSimple Sanal dizinizi ayarlamak ve yapılandırmak için aşağıdaki adım adım yönergeleri kullanın.

## <a name="step-1-complete-the-local-web-ui-setup-and-register-your-device"></a>1\. adım: Yerel Web Kullanıcı arabirimi kurulumunu tamamlayıp cihazınızı kaydedin
#### <a name="to-complete-the-setup-and-register-the-device"></a>Kurulumu tamamlayıp cihazı kaydetme
1. Bir tarayıcı penceresi açın ve yerel Web Kullanıcı arabirimine bağlanın. Şunu yazın:
   
   `https://<ip-address of network interface>`
   
   Önceki adımda belirtilen bağlantı URL 'sini kullanın. Web sitesinin güvenlik sertifikasıyla ilgili bir sorun olduğunu belirten bir hata görürsünüz. **Bu Web sayfasına devam et ' e**tıklayın.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image2.png)
2. Sanal dizinizdeki Web Kullanıcı arabiriminde **Storsimpleadmin**olarak oturum açın. Adım 3 ' te değiştirdiğiniz cihaz yönetici parolasını girin: [Hyper-V ' d a StorSimple Sanal dizisi sağlama](storsimple-virtual-array-deploy2-provision-hyperv.md) veya [VMware 'de StorSimple Sanal dizisi sağlama](storsimple-virtual-array-deploy2-provision-vmware.md)bölümünde sanal diziyi başlatın.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image3.png)
3. **Giriş** sayfasına yönlendirilirsiniz. Bu sayfada, StorSimple Aygıt Yöneticisi hizmetiyle sanal diziyi yapılandırmak ve kaydetmek için gereken çeşitli ayarlar açıklanmaktadır. **Ağ ayarları**, **Web proxy ayarları**ve **zaman ayarları** isteğe bağlıdır. Yalnızca **cihaz ayarları** ve **bulut ayarları**gereklidir.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image4.png)
4. Ağ **ayarları** sayfasında, **ağ arabirimleri**altında, veri 0 sizin için otomatik olarak yapılandırılır. Her ağ arabirimi, IP adresini otomatik olarak almak için varsayılan olarak ayarlanır (DHCP). Bu nedenle, bir IP adresi, alt ağ ve ağ geçidi otomatik olarak atanır (hem IPv4 hem de IPv6 için).
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image5.png)
   
   Cihazı sağlama sırasında birden fazla ağ arabirimi eklediyseniz, bunları buradan yapılandırabilirsiniz. Ağ arabiriminizi yalnızca IPv4 olarak veya hem IPv4 hem de IPv6 olarak yapılandırabileceğinizi aklınızda bulabilirsiniz. Yalnızca IPv6 konfigürasyonları desteklenmez.
5. Cihazınız bulut depolama hizmeti sağlayıcılarınız ile iletişim kurmaya çalıştığında veya dosya sunucusu olarak yapılandırıldığında cihazınızı ada göre çözümlemek için kullanıldığından, DNS sunucuları gereklidir. **Ağ ayarları** sayfasında, **DNS sunucuları**altında:
   
   1. Birincil ve ikincil DNS sunucusu otomatik olarak yapılandırılır. Statik IP adreslerini yapılandırmayı seçerseniz, DNS sunucuları belirtebilirsiniz. Yüksek kullanılabilirlik için, birincil ve ikincil bir DNS sunucusu yapılandırmanızı öneririz.
   2. Ağ ayarlarını uygulamak ve doğrulamak için **Uygula** ' ya tıklayın.
6. **Cihaz ayarları** sayfasında:
   
   1. Cihazınıza benzersiz bir **ad** atayın. Bu ad 1-15 karakter olabilir ve harf, rakam ve kısa çizgi içerebilir.
   2. Oluşturmakta olduğunuz cihaz **türü** için ![](./media/storsimple-virtual-array-deploy3-fs-setup/image6.png) **dosya sunucusu** simgesine tıklayın. Bir dosya sunucusu, paylaşılan klasörler oluşturmanıza izin verir.
   3. Cihazınız bir dosya sunucusu olduğundan, cihazı bir etki alanına eklemeniz gerekir. Bir **etki alanı adı**girin.
   4. **Uygula**'ya tıklayın.
7. Bir iletişim kutusu görüntülenir. Etki alanı kimlik bilgilerinizi belirtilen biçimde girin. Onay simgesine tıklayın. Etki alanı kimlik bilgileri doğrulanır. Kimlik bilgileri yanlışsa bir hata iletisi görürsünüz.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image7.png)
8. **Uygula**'ya tıklayın. Bu işlem cihaz ayarlarını uygular ve doğrular.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image8.png)
   
   > [!NOTE]
   > Sanal diziniz Active Directory için kendi kuruluş biriminde (OU) olduğundan ve kendisine hiçbir Grup İlkesi nesnesi (GPO) uygulandığından veya Devralınana emin olun. Grup ilkesi, StorSimple Sanal dizisine virüsten koruma yazılımı gibi uygulamalar yükleyebilirler. Ek yazılım yükleme desteklenmez ve verilerin bozulmasına yol açabilir. 
   > 
   > 
9. (İsteğe bağlı olarak) Web Proxy sunucunuzu yapılandırın. Web proxy yapılandırması isteğe bağlı olsa da, bir Web proxy 'si kullanıyorsanız, burada yalnızca yapılandırabilirsiniz.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image9.png)
   
   **Web proxy** sayfasında:
   
   1. **Web proxy URL 'sini** şu biçimde sağlayın: *&lt;http://Host-IP adresi veya FQDN&gt;:P ort sayı*. HTTPS URL 'Lerinin desteklenmediğini unutmayın.
   2. **Kimlik doğrulamasını** **temel** veya **yok**olarak belirtin.
   3. Kimlik doğrulaması kullanıyorsanız, ayrıca bir **Kullanıcı adı** ve **parola**sağlamanız gerekir.
   4. **Uygula**'ya tıklayın. Bu, yapılandırılmış Web proxy ayarlarını doğrular ve uygular.
10. (İsteğe bağlı olarak) cihazınız için saat dilimi ve birincil ve ikincil NTP sunucuları gibi zaman ayarlarını yapılandırın. Cihazınızın bulut hizmeti sağlayıcılarınız ile kimlik doğrulaması yapabilmesi için zaman eşitlemesini gerektiğinden NTP sunucuları gereklidir.
    
    ![](./media/storsimple-virtual-array-deploy3-fs-setup/image10.png)
    
    **Zaman ayarları** sayfasında:
    
    1. Açılan listeden, cihazın dağıtıldığı coğrafi konuma göre **saat dilimini** seçin. Cihazınızın varsayılan saat dilimi PST ' dir. Cihazınız zamanlanan tüm işlemler için bu saat dilimini kullanır.
    2. Cihazınız için bir **BIRINCIL NTP sunucusu** belirtin veya Time.Windows.com varsayılan değerini kabul edin. Ağınızın, NTP trafiğini veri merkezinizden İnternete geçirilmesine izin vermesini sağlayın.
    3. İsteğe bağlı olarak, cihazınız için bir **IKINCIL NTP sunucusu** belirtin.
    4. **Uygula**'ya tıklayın. Bu, yapılandırılan zaman ayarlarını doğrular ve uygular.
11. Cihazınız için bulut ayarlarını yapılandırın. Bu adımda, yerel cihaz yapılandırmasını tamamlayacaksınız ve cihazı StorSimple Aygıt Yöneticisi hizmetinize kaydedecaksınız.
    
    1. 2\. [adımda aldığınız **hizmet kayıt anahtarını** girin: StorSimple Sanal dizisi için](storsimple-virtual-array-deploy1-portal-prep.md#step-2-get-the-service-registration-key) hizmet kayıt anahtarını alın.
    2. Bu hizmete kayıt ilk cihazınız ise, **hizmet veri şifreleme anahtarı**görüntülenir. Bu anahtarı kopyalayın ve güvenli bir konuma kaydedin. Bu anahtar, StorSimple Aygıt Yöneticisi hizmetiyle ek cihazlar kaydetmek için hizmet kayıt anahtarıyla birlikte gereklidir. 
       
       Bu hizmete kaydolduğunuz ilk cihaz bu değilse, hizmet veri şifreleme anahtarını sağlamanız gerekir. Daha fazla bilgi için yerel Web Kullanıcı arabiriminizdeki [hizmet veri şifreleme anahtarını](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) edinme bölümüne bakın.
    3. **Kaydol**' a tıklayın. Bu işlem cihazı yeniden başlatacak. Cihaz başarıyla kaydedilmeden önce 2-3 dakika beklemeniz gerekebilir. Cihaz yeniden başlatıldıktan sonra oturum açma sayfasına yönlendirilirsiniz.
       
       ![](./media/storsimple-virtual-array-deploy3-fs-setup/image13.png)
12. Azure portalına dönün. **Tüm kaynaklara**gidin, storsimple Aygıt Yöneticisi hizmetinizi arayın.
    
    ![](./media/storsimple-virtual-array-deploy3-fs-setup/searchdevicemanagerservice1.png) 
13. Filtrelenmiş listede, StorSimple Aygıt Yöneticisi hizmetinizi seçin ve ardından **yönetim > cihazlar**' a gidin. **Cihazlar** dikey penceresinde, cihazın hizmete başarıyla bağlandığını ve durumu **ayarlamaya hazırlanıyor**olduğunu doğrulayın.
    
    ![Dosya sunucusunu yapılandırma](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs2m.png)

## <a name="step-2-configure-the-device-as-file-server"></a>2\. adım: Cihazı dosya sunucusu olarak yapılandırma
Gerekli cihaz kurulumunu tamamlamak için [Azure Portal](https://portal.azure.com/) aşağıdaki adımları gerçekleştirin.

#### <a name="to-configure-the-device-as-file-server"></a>Cihazı dosya sunucusu olarak yapılandırmak için
1. StorSimple Aygıt Yöneticisi hizmetinize gidin ve **yönetim > cihazlar**' a gidin. **Cihazlar** dikey penceresinde, yeni oluşturduğunuz cihazı seçin. Bu cihaz, **ayarlamaya hazırlanıyor**olarak görünür.
   
   ![Dosya sunucusunu yapılandırma](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs2m.png) 
2. Cihaza tıklayın ve cihazın kuruluma hazırlandığını belirten bir başlık iletisi görürsünüz.
   
    ![Dosya sunucusunu yapılandırma](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs3m.png)
3. Komut çubuğunda **Yapılandır** ' ı tıklatın. Bu, **Yapılandır** dikey penceresini açar. **Yapılandır** dikey penceresinde aşağıdakileri yapın:
   
   1. Dosya sunucusu adı otomatik olarak doldurulur.
    
   2. Bulut depolama şifrelemesi 'nin **etkin**olarak ayarlandığından emin olun. Bu, buluta gönderilen tüm verileri şifreler. 
    
   3. Şifreleme için Kullanıcı tanımlı anahtarla 256 bitlik bir AES anahtarı kullanılır. Bir 32 karakter anahtarı belirtip anahtarı onaylamak için yeniden girin. Anahtarı daha sonra başvurmak üzere bir anahtar yönetim uygulamasına kaydedin.
    
   4. Aygıtınızla kullanılacak depolama hesabı kimlik bilgilerini belirtmek için **gerekli ayarları Yapılandır** ' a tıklayın. Yapılandırılmış depolama hesabı kimlik bilgileri yoksa **Yeni Ekle** ' ye tıklayın. **Kullandığınız depolama hesabının blok bloblarını desteklediğinden emin olun. Sayfa Blobları desteklenmez.** [Blok Blobları ve sayfa Blobları](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)hakkında daha fazla bilgi.
   
      ![Dosya sunucusunu yapılandırma](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs6m.png) 
4. **Depolama hesabı kimlik bilgileri ekleme** dikey penceresinde aşağıdakileri yapın: 

    1. Depolama hesabı hizmetle aynı abonelikte ise geçerli abonelik ' ı seçin. Diğer, depolama hesabının hizmet aboneliğinin dışında olduğunu belirtir. 
    
    2. Açılan listeden var olan bir depolama hesabını seçin. 
    
    3. Konum, belirtilen depolama hesabına göre otomatik olarak doldurulur. 
    
    4. Cihaz ve bulut arasında güvenli bir ağ iletişim kanalı olduğundan emin olmak için SSL 'yi etkinleştirin.
    
    5. Bu depolama hesabı kimlik bilgilerini eklemek için **Ekle** ' ye tıklayın. 
   
        ![Dosya sunucusunu yapılandırma](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs8m.png)

5. Depolama hesabı kimlik bilgileri başarıyla oluşturulduktan sonra, **yapılandırma** dikey penceresi belirtilen depolama hesabı kimlik bilgilerini görüntüleyecek şekilde güncelleştirilir. **Yapılandır**'a tıklayın.
   
   ![Dosya sunucusunu yapılandırma](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs11m.png)
   
   Bir dosya sunucusunun oluşturulduğunu görürsünüz. Dosya sunucusu başarıyla oluşturulduktan sonra bilgilendirilir.
   
   ![Dosya sunucusunu yapılandırma](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs13m.png)
   
   Cihaz durumu da **çevrimiçi**olarak değişir.
   
   ![Dosya sunucusunu yapılandırma](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs14m.png)
   
   Bir paylaşma eklemeye devam edebilirsiniz.

## <a name="step-3-add-a-share"></a>3\. adım: Paylaşım ekleme
Paylaşım oluşturmak için [Azure portalında](https://portal.azure.com/) aşağıdaki adımları gerçekleştirin.

#### <a name="to-create-a-share"></a>Bir paylaşma oluşturmak için
1. Önceki adımda yapılandırdığınız dosya sunucusu cihazını seçin ve **..** . öğesine tıklayın. (veya sağ tıklayın). Bağlam menüsünde, **paylaşma Ekle**' yi seçin. Alternatif olarak, cihaz komut çubuğunda **+ paylaşma Ekle** ' ye tıklayabilirsiniz.
   
   ![Paylaşım ekleme](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs15m.png)
2. Aşağıdaki paylaşma ayarlarını belirtin:

   1. Paylaşımınız için benzersiz bir ad. Ad 3 ile 127 arasında karakter içeren bir dize olmalıdır.
    
   2. Paylaşıma yönelik isteğe bağlı bir **Açıklama** . Açıklama, paylaşma sahiplerini belirlemesine yardımcı olur.
    
   3. Paylaşıma yönelik bir **tür** . Tür **katmanlı veya** **yerel olarak sabitlenmiş**olabilir, katmanlı varsayılan olarak. Yerel garanti, düşük gecikme süreleri ve daha yüksek performans gerektiren iş yükleri için **yerel olarak sabitlenmiş** bir paylaşımın seçin. Diğer tüm veriler için **katmanlı** bir paylaşma seçin.
      Yerel olarak sabitlenmiş bir paylaşımın sağlanması bol alanla ve paylaşımdaki birincil verilerin cihaza yerel olarak kalmasını ve buluta taşımamasını sağlar. Diğer yandan katmanlı bir paylaşımın ölçülü kaynak sağlanmış olur. Katmanlı bir paylaşma oluşturduğunuzda alanın% 10 ' u yerel katmanda sağlanır ve alanın% 90 ' unun bulutta sağlanması gerekir. Örneğin, 1 TB 'lik bir birim sağladıysanız, 100 GB yerel alanda yer alır ve veri katmanları sırasında bulutta 900 GB kullanılır. Bu da, cihazdaki tüm yerel alanı kapatdıysanız katmanlı bir paylaşma sağlayacağınızı gösterir.
   
   4. **Varsayılan tam Izinleri ayarla** alanında, kullanıcıya veya bu paylaşıma erişen gruba izinleri atayın. *John\@contoso.com* biçimindeki Kullanıcı veya Kullanıcı grubu adını belirtin. Yönetici ayrıcalıklarının bu paylaşımlara erişmesine izin vermek için bir Kullanıcı grubu (tek bir kullanıcı yerine) kullanmanızı öneririz. Burada izinleri atadıktan sonra, Dosya Gezgini'ni kullanarak bu izinlerde değişiklik yapabilirsiniz.
   
   5. Paylaşma oluşturmak için **Ekle** ' ye tıklayın. 
    
       ![Paylaşım ekleme](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs18m.png)
   
       Paylaşım oluşturma işleminin devam ettiği size bildirilir.
   
       ![Paylaşım ekleme](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs19m.png)
   
      Paylaşım belirtilen ayarlarla oluşturulduktan sonra, **Paylaşımlar** dikey penceresi yeni paylaşımı yansıtacak şekilde güncelleşilir. Varsayılan olarak, izleme ve yedekleme, paylaşma için etkinleştirilmiştir.
   
      ![Paylaşım ekleme](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs22m.png)

## <a name="step-4-connect-to-the-share"></a>4\. Adım: Paylaşıma bağlanma
Şimdi, önceki adımda oluşturduğunuz bir veya daha fazla paylaşımlara bağlanmanız gerekir. Bu adımları, StorSimple Sanal diziniz ile bağlantılı Windows Server konağında gerçekleştirin.

#### <a name="to-connect-to-the-share"></a>Paylaşıma bağlanmak için
1. \+ ![](./media/storsimple-virtual-array-deploy3-fs-setup/image22.png) R tuşuna basın. Çalıştır penceresinde, dosya sunucusu *adını* dosya sunucunuza atadığınız cihaz adı ile değiştirerek yol olarak *&#92; &#92; &lt;dosya sunucusu adını&gt;* belirtin.           **Tamam**'ı tıklatın.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image23.png)
2. Bu, dosya Gezgini 'ni açar. Artık oluşturduğunuz paylaşımları klasörler olarak görebiliyor olmalısınız. İçeriğini görmek için paylaşımı (klasörü) seçin ve çift tıklayın.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image24.png)
3. Artık bu paylaşımlara dosya ekleyebilir ve bir yedekleme gerçekleştirebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar
[StorSimple Sanal dizinizi yönetmek](storsimple-ova-web-ui-admin.md)için yerel Web Kullanıcı arabirimini nasıl kullanacağınızı öğrenin.

