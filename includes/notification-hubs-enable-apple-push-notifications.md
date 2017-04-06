

## <a name="generate-the-certificate-signing-request-file"></a>Sertifika İmzalama İsteği dosyası oluşturma
Apple Anında İletilen Bildirim Servisi (APNS), anında iletme bildirimlerinizi doğrulamak için sertifikaları kullanır. Bildirim gönderip almak için gereken bildirim sertifikasını oluşturacak bu talimatları uygulayın. Bu kavramlarla ilgili daha fazla bilgi için resmi [Apple Anında İletilen Bildirim Servisi](http://go.microsoft.com/fwlink/p/?LinkId=272584) belgelerine bakın.

İmzalı bir bildirim sertifikası oluşturmak için Apple tarafından kullanılan Sertifika İmzalama İsteği (CSR) dosyasını oluşturun.

1. Mac’inizde Anahtar Zinciri Erişimi aracını çalıştırın. **Yardımcı Programlar** klasöründen veya başlatma panelindeki **Diğer** klasöründen açılabilir.
2. **Anahtar Zinciri Erişimi**’ne tıklayın, **Sertifika Yardımcısı**’nı genişletin ve **Sertifika Yetkilisinden Sertifika İste...** öğesine tıklayın.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-request-cert-from-ca.png)
3. **Kullanıcı E-posta Adresi**’nizi ve **Ortak Ad**’ı seçin, **Diske kaydedilmiş**’in seçili olduğundan emin olun ve ardından **Devam**’a tıklayın. Gerekmediğinden **CA E-posta Adresi**’ni boş bırakın.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-csr-info.png)
4. Sertifika İmzalama İsteği (CSR) dosyası için **Farklı Kaydet**’e bir ad yazın, **Nerede**’de konum seçin ve ardından **Kaydet**’e tıklayın.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-save-csr.png)
   
      Böylece CSR dosyası istediğiniz konuma kaydedilir; varsayılan konum Masaüstü’dür. Bu dosya için seçilen konumu unutmayın.

Bundan sonra, uygulamanızı Apple’a kaydedeceksiniz; anında iletme bildirimini etkinleştirip bildirim sertifikası oluşturmak için dışarı aktarılan bu CSR dosyasını karşıya yükleyin.

## <a name="register-your-app-for-push-notifications"></a>Anında iletme bildirimleri için uygulamanızı kaydetme
iOS uygulamasına anında iletme bildirimleri gönderebilmek için uygulamanızı Apple’a kaydetmenin yanı sıra anında iletme bildirimlerini de kaydetmeniz gerekir.  

1. Uygulamanızı henüz kaydetmediyseniz, Apple Geliştirici Merkezi’ndeki <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">iOS Sağlama Portalı</a>’na gidin, Apple kimliğinizle oturum açın, **Tanımlayıcılar**’a, **Uygulama kimlikleri**’ne ve son olarak da **+** işaretine tıklayarak yeni uygulamayı kaydedin.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids.png)
      
2. Yeni uygulamanız için aşağıdaki üç alanı güncelleştirin ve ardından **Devam**’a tıklayın:
   
   * **Ad**: Uygulamanızı için **Uygulama Kimliği Açıklaması** bölümündeki **Ad** alanına açıklayıcı bir ad yazın.
   * **Paket Tanımlayıcı**: **Açık Uygulama Kimliği** bölümünün altında, **Paket Tanımlayıcı** öğesini `<Organization Identifier>.<Product Name>` biçiminde, [Uygulama Dağıtım Kılavuzu](https://developer.apple.com/library/mac/documentation/IDEs/Conceptual/AppDistributionGuide/ConfiguringYourApp/ConfiguringYourApp.html#//apple_ref/doc/uid/TP40012582-CH28-SW8)’nda söz edildiği gibi girin. *Kuruluş Tanımlayıcı* ve kullandığınız *Ürün Adı*, XCode projenizi oluşturduğunuzda kullanacağınız kuruluş tanımlayıcısı ve ürün adıyla eşleşmelidir. Aşağıdaki ekran görüntüsünde *NotificationHubs* kuruluş tanımlayıcısı, *GetStarted* da ürün adı olarak kullanılmıştır. Bunun, XCode projenizde kullanacağınız değerlerle eşleştiğinden emin olunması XCode ile doğru yayımlama profili kullanmanızı sağlar. 
   * **Anında İletme Bildirimleri**: **Anında İletme Bildirimleri**’ni **Uygulama Hizmetleri** bölümünde denetleyin.
     
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-appid-info.png)
     
      Böylece, Uygulama Kimliğiniz oluşturulur ve bilgiyi onaylamanız istenir. Yeni Uygulama Kimliğini onaylamak için **Kaydet**’e tıklayın.
     
      **Kaydet**’e tıkladıktan sonra **Kayıt tamamlandı** ekranını aşağıda gösterildiği gibi göreceksiniz. **Bitti**’ye tıklayın.
      
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-registration-complete.png)


1. Geliştirici Merkezi’nde, Uygulama Kimlikleri altında henüz yeni oluşturmuş olduğunuz , uygulama kimliğini bulup satırına tıklayın.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids2.png)
   
      Uygulama kimliğinin tıklatılması uygulama ayrıntılarını görüntüler. Alttaki **Düzenle** düğmesine tıklayın.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-edit-appid.png)
      
2. Ekranın altına gidin ve **Geliştirme bildirimi SSL Sertifikası** altındaki **sertifika Oluştur...** düğmesine tıklayın.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-create-cert.png)
   
      "iOS Sertifikası Ekle" yardımcısı görüntülenir.
   
   > [!NOTE]
   > Bu öğretici geliştirme sertifikası kullanır. Aynı işlem üretim sertifika kaydedildiğinde de kullanılır. Bildirimleri gönderirken aynı sertifika türünü kullandığınızdan kesinlikle emin olun.
   > 
   > 
3. **Dosya Seç**’e tıklayın, ilk görevde oluşturduğunuz CSR dosyasını kaydettiğiniz konuma göz atın ve **Oluştur**’a tıklayın.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-cert-choose-csr.png)
4. Portal tarafından sertifika oluşturulduktan sonra **İndir** düğmesine ve **Bitti**’ye tıklayın.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-download-cert.png)
   
      Böylece sertifika indirilir ve bilgisayarınızın İndirmeler klasörüne kaydedilir.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-downloaded.png)
   
   > [!NOTE]
   > Varsayılan olarak, indirilen geliştirme sertifikası dosyası **aps_development.cer** olarak adlandırılır.
   > 
   > 
5. İndirilen **aps_development.cer** bildirim sertifikasına çift tıklayın.
   
      Yeni sertifika Anahtar Zinciri’ne aşağıda gösterildiği gibi yüklenir:
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-in-keychain.png)
   
   > [!NOTE]
   > Sertifikanızdaki ad farklı olabilir, ancak **Apple Geliştirme iOS Bildirim Hizmetleri:** ile önek alacaktır.
   > 
   > 
6. Anahtar Zinciri Erişimi’nde **Sertifikalar** kategorisinde oluşturduğunuz yeni bildirim sertifikasına sağ tıklayın. **Dışarı Aktar**’a tıklayın, dosyaya ad verin, **.p12** biçimini seçin ve **Kaydet**’e tıklayın.
   
    ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-export-cert-p12.png)
   
    Dışarı aktarılan .p12 sertifikanın dosya adını ve konumunu not edin. APNS kimlik doğrulamasını etkinleştirmek için kullanılacaktır.
   
   > [!NOTE]
   > Bu öğretici bir QuickStart.p12 dosyası oluşturur. Dosyanızın adı ve konumu farklı olabilir.
   > 
   > 

## <a name="create-a-provisioning-profile-for-the-app"></a>Uygulama için bir sağlama profili oluşturun
1. <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">iOS Sağlama Portalı</a>’na geri dönün, **Sağlama Profilleri**’ni ve **Tümü**’nü seçin, ardından da yeni profil oluşturmak için **+** düğmesine tıklayın. Böylece, **iOS Hazırlama Profili** Sihirbazı başlatılır
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-provisioning-profile.png)
2. **iOS Uygulaması Geliştirme**’yi, **Geliştirme** altında hazırlama profili türü olarak seçin ve **Devam**’a tıklayın. 
3. Daha sonra, az önce **Uygulama Kimliği** açılan listesinden oluşturduğunuz uygulama kimliğini seçin ve **Devam**’a tıklayın
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-select-appid-for-provisioning.png)
4. **Sertifikaları Seç** ekranında, kod imzalama için kullanılan normal geliştirme sertifikanızı seçin ve **Devam**’a tıklayın. Bu, yeni oluşturduğunuz bildirim sertifikası değildir.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-select-cert.png)
5. Sonra da, testte kullanmak üzere **Cihazlar**’a ve **Devam**’a tıklayın
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-select-devices.png)
6. Son olarak, profil için **Profil Adı**’nda bir ad seçin, **Oluştur**’a tıklayın.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-name-profile.png)
7. Yeni sağlama profili oluşturulduğunda indirmek için buna tıklayın ve Xcode geliştirme makinesine yükleyin. Sonra da **Bitti**’ye tıklayın.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-profile-ready.png)
