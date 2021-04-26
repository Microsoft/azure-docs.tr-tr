---
title: Açık kaynaklı teknolojiler SSS
description: Azure App Service 'de açık kaynaklı teknolojiler hakkında sık sorulan soruların yanıtlarını alın.
author: genlin
manager: dcscontentpm
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.topic: article
ms.date: 10/31/2018
ms.author: genli
ms.custom: seodec18, devx-track-python
ms.openlocfilehash: 36dfbf0fda060a8f273fee64098d6234b575088c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97831848"
---
# <a name="open-source-technologies-faqs-for-web-apps-in-azure"></a>Azure 'da Web Apps yönelik açık kaynaklı teknolojiler SSS

Bu makalede, [Azure App Service Web Apps özelliğine](https://azure.microsoft.com/services/app-service/web/)yönelik açık kaynaklı teknolojilerle ilgili sorunlar hakkında sık sorulan soruların (SSS) yanıtları bulunur.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="how-do-i-turn-on-php-logging-to-troubleshoot-php-issues"></a>Php sorunlarını gidermek için PHP günlüğünü Nasıl yaparım? açın mi?

PHP günlüğünü açmak için:

1. **Kudu Web sitenizde** () oturum açın `https://*yourwebsitename*.scm.azurewebsites.net` .
2. Üstteki menüden **Hata Ayıkla konsol**  >  **cmd**' yi seçin.
3. **Site** klasörünü seçin.
4. **Wwwroot** klasörünü seçin.
5. Simgeyi seçin **+** ve ardından **yeni dosya**' yı seçin.
6. Dosya adını **.user.ini** olarak ayarlayın.
7. **.user.ini** yanındaki kurşun kalem simgesini seçin.
8. Dosyasında şu kodu ekleyin: `log_errors=on`
9. **Kaydet**’i seçin.
10. **Wp-config. php**' nin yanındaki kurşun kalem simgesini seçin.
11. Metni şu kodla değiştirin:
    ```php
    //Enable WP_DEBUG modedefine('WP_DEBUG', true);//Enable debug logging to /wp-content/debug.logdefine('WP_DEBUG_LOG', true);
    //Suppress errors and warnings to screendefine('WP_DEBUG_DISPLAY', false);//Suppress PHP errors to screenini_set('display_errors', 0);
    ```
12. Azure portal web uygulaması menüsünde Web uygulamanızı yeniden başlatın.

Daha fazla bilgi için bkz. [WordPress hata günlüklerini etkinleştirme](/archive/blogs/azureossds/logging-php-errors-in-wordpress-2).

## <a name="how-do-i-log-python-application-errors-in-apps-that-are-hosted-in-app-service"></a>App Service barındırılan uygulamalarda Python uygulama hatalarını günlüğe Nasıl yaparım?.
[!INCLUDE [web-sites-python-troubleshooting-wsgi-error-log](../../includes/web-sites-python-troubleshooting-wsgi-error-log.md)]

## <a name="how-do-i-change-the-version-of-the-nodejs-application-that-is-hosted-in-app-service"></a>App Service barındırılan Node.js uygulamasının sürümünü değiştirmek Nasıl yaparım?.

Node.js uygulamasının sürümünü değiştirmek için aşağıdaki seçeneklerden birini kullanabilirsiniz:

* Azure portal, **uygulama ayarlarını** kullanın.
  1. Azure portal web uygulamanıza gidin.
  2. **Ayarlar** dikey penceresinde **uygulama ayarları**' nı seçin.
  3. **Uygulama ayarları**' nda, anahtar olarak WEBSITE_NODE_DEFAULT_VERSION ve istediğiniz Node.js sürümünü değer olarak dahil edebilirsiniz.
  4. **Kudu konsolunuza** () gidin `https://*yourwebsitename*.scm.azurewebsites.net` .
  5. Node.js sürümünü denetlemek için aşağıdaki komutu girin:  
     ```
     node -v
     ```
* Iısnode. yıml dosyasını değiştirin. Iısnode. yıml dosyasındaki Node.js sürümünün değiştirilmesi yalnızca ıısnode 'un kullandığı çalışma zamanı ortamını ayarlar. Kudu cmd 'niz ve diğerleri, Azure portal **uygulama ayarları** 'nda ayarlanan Node.js sürümünü kullanmaya devam eder.

  Iısnode. yıml 'yi el ile ayarlamak için, uygulama kök klasörünüzde bir ıısnode. yıml dosyası oluşturun. Dosyasında, aşağıdaki satırı ekleyin:
  ```yml
  nodeProcessCommandLine: "D:\Program Files (x86)\nodejs\5.9.1\node.exe"
  ```
   
* Kaynak denetimi dağıtımı sırasında package.jskullanarak ıısnode. yıml dosyasını ayarlayın.
  Azure Kaynak denetimi dağıtım işlemi aşağıdaki adımları içerir:
  1. İçeriği Azure Web uygulamasına taşıın.
  2. Web uygulaması kök klasöründe bir tane (Deploy. cmd,. Deployment Files) yoksa varsayılan bir dağıtım betiği oluşturur.
  3. Dosya > altyapısında package.jsNode.js sürümden bahsettiğiniz bir ıısnode. yıml dosyası oluşturduğu bir dağıtım betiği çalıştırır `"engines": {"node": "5.9.1","npm": "3.7.3"}`
  4. Iısnode. yml dosyası aşağıdaki kod satırına sahiptir:
      ```yml
      nodeProcessCommandLine: "D:\Program Files (x86)\nodejs\5.9.1\node.exe"
      ```

## <a name="i-see-the-message-error-establishing-a-database-connection-in-my-wordpress-app-thats-hosted-in-app-service-how-do-i-troubleshoot-this"></a>App Service ' de barındırılan WordPress uygulamamda "veritabanı bağlantısı kurulurken hata oluştu" iletisini görüyorum. Nasıl yaparım? sorunu gidermi?

Azure WordPress uygulamanızda bu hatayı görürseniz, php_errors. log ve Debug. log ' u etkinleştirmek için, [WordPress hata günlüklerini etkinleştirme](/archive/blogs/azureossds/logging-php-errors-in-wordpress-2)bölümünde açıklanan adımları uygulayın.

Günlükler etkinleştirildiğinde, hata yeniden oluşturun ve sonra bağlantı dışında çalışıp çalışmadığını görmek için günlükleri kontrol edin:
```
[09-Oct-2015 00:03:13 UTC] PHP Warning: mysqli_real_connect(): (HY000/1226): User ‘abcdefghijk79' has exceeded the ‘max_user_connections’ resource (current value: 4) in D:\home\site\wwwroot\wp-includes\wp-db.php on line 1454
```

Debug. log veya php_errors. log dosyalarında bu hatayı görürseniz, uygulamanız bağlantı sayısını aşıyorsunuz. ClearDB üzerinde barındırıyorsanız, [hizmet planınızda](https://www.cleardb.com/pricing.view)bulunan bağlantı sayısını doğrulayın.

## <a name="how-do-i-debug-a-nodejs-app-thats-hosted-in-app-service"></a>App Service barındırılan bir Node.js uygulamasında hata ayıklama Nasıl yaparım??

1.  **Kudu konsolunuza** () gidin `https://*yourwebsitename*.scm.azurewebsites.net/DebugConsole` .
2.  Uygulama günlükleri klasörünüze gidin (D:\home\LogFiles\Application).
3.  logging_errors.txt dosyasında, içerik ' i kontrol edin.

## <a name="how-do-i-install-native-python-modules-in-an-app-service-web-app-or-api-app"></a>Yerel Python modüllerini bir App Service Web uygulamasına veya API uygulamasına Nasıl yaparım?.

Bazı paketler Azure 'da PIP kullanarak yüklenemeyebilir. Paket, Python paket dizininde bulunmayabilir veya bir derleyici gerekli olabilir (App Service bir derleyici, Web uygulamasını çalıştıran bilgisayarda, bu bilgisayarda kullanılabilir). App Service Web uygulamalarına ve API uygulamalarına yerel modüller yükleme hakkında daha fazla bilgi için bkz. [Python modüllerini App Service yükleme](/archive/blogs/azureossds/install-native-python-modules-on-azure-web-apps-api-apps).

## <a name="how-do-i-deploy-a-django-app-to-app-service-by-using-git-and-the-new-version-of-python"></a>Nasıl yaparım? bir Docgo uygulamasını git ve yeni Python sürümünü kullanarak App Service dağıtmak istiyor musunuz?

Docgo yükleme hakkında bilgi için bkz. [App Service Için docgo uygulaması dağıtma](/archive/blogs/azureossds/deploying-django-app-to-azure-app-services-using-git-and-new-version-of-python).

## <a name="where-are-the-tomcat-log-files-located"></a>Tomcat günlük dosyaları nerede bulunur?

Azure Marketi ve özel dağıtımlar için:

* Klasör konumu: D:\home\site\wwwroot\bin\apache-Tomcat-8.0.33\logs
* İlgilendiğiniz dosyalar:
    * Catalina. *yyyy-aa-gg*. log
    * Konak Yöneticisi. *yyyy-aa-gg*. log
    * e. *yyyy-aa-gg*. log
    * Manager. *yyyy-aa-gg*. log
    * site_access_log. *yyyy-aa-gg*. log


Portal **uygulama ayarları** dağıtımları için:

* Klasör konumu: D:\home\LogFiles
* İlgilendiğiniz dosyalar:
    * Catalina. *yyyy-aa-gg*. log
    * Konak Yöneticisi. *yyyy-aa-gg*. log
    * e. *yyyy-aa-gg*. log
    * Manager. *yyyy-aa-gg*. log
    * site_access_log. *yyyy-aa-gg*. log

## <a name="how-do-i-troubleshoot-jdbc-driver-connection-errors"></a>Nasıl yaparım? JDBC sürücü bağlantısı hatalarıyla ilgili sorun mu yaşıyorsunuz?

Tomcat günlüklerinde şu iletiyi görebilirsiniz:

```
The web application[ROOT] registered the JDBC driver [com.mysql.jdbc.Driver] but failed to unregister it when the web application was stopped. To prevent a memory leak,the JDBC Driver has been forcibly unregistered
```

Hatayı gidermek için:

1. Sqljdbc*. jar dosyasını App/lib klasörünüzden kaldırın.
2. Özel Tomcat veya Azure Market Tomcat Web sunucusunu kullanıyorsanız, bu. jar dosyasını Tomcat lib klasörüne kopyalayın.
3. Java 'yı Azure Portal ( **Java 1,8**  >  **Tomcat Server** seçin) ' ten etkinleştirirseniz, Sqljdbc. * jar dosyasını uygulamanıza paralel klasöre kopyalayın. Sonra, web.config dosyasına aşağıdaki Sınıfyolu ayarını ekleyin:

    ```xml
    <httpPlatform>
    <environmentVariables>
    <environmentVariablename ="JAVA_OPTS" value=" -Djava.net.preferIPv4Stack=true
    -Xms128M -classpath %CLASSPATH%;[Path to the sqljdbc*.jarfile]" />
    </environmentVariables>
    </httpPlatform>
    ```

## <a name="why-do-i-see-errors-when-i-attempt-to-copy-live-log-files"></a>Canlı günlük dosyalarını kopyalamaya çalışırken neden hata görüyorum?

Bir Java uygulaması (örneğin, Tomcat) için canlı günlük dosyalarını kopyalamaya çalışırsanız şu FTP hatasını görebilirsiniz:

```
Error transferring file [filename] Copying files from remote side failed.
    
The process cannot access the file because it is being used by another process.
```

Hata iletisi, FTP istemcisine bağlı olarak farklılık gösterebilir.

Tüm Java uygulamalarında bu kilitleme sorunu vardır. Uygulama çalışırken yalnızca kudu bu dosyanın indirilmesini destekler.

Uygulamanın durdurulması, bu dosyalara FTP erişimine izin verir.

Başka bir çözüm de bir zamanlamaya göre çalışan bir WebJob yazmak ve bu dosyaları farklı bir dizine kopyalar. Örnek bir proje için bkz. [Copylogsjob](https://github.com/kamilsykora/CopyLogsJob) projesi.

## <a name="where-do-i-find-the-log-files-for-jetty"></a>Jetty için günlük dosyalarını nerede bulabilirim?

Market ve özel dağıtımlar için günlük dosyası D:\home\site\wwwroot\bin\jetty-Distribution-9.1.2.v20140210\logs klasöründedir. Klasör konumunun, kullanmakta olduğunuz Jetty sürümüne bağlı olduğunu unutmayın. Örneğin, burada belirtilen yol Jetty 9.1.2 içindir. Jetty_ *YYYY_MM_DD*. stderrout. log ' a bakın.

Portal uygulama ayarı dağıtımları için günlük dosyası D:\home\logfilesyolunda bulunur. Jetty_ *YYYY_MM_DD*. stderrout. log dosyasına bakın

## <a name="can-i-send-email-from-my-azure-web-app"></a>Azure Web uygulamamda e-posta gönderebilir miyim?

App Service yerleşik bir e-posta özelliğine sahip değildir. Uygulamanızdan e-posta göndermeye yönelik bazı iyi yöntemler için bu [Stack Overflow tartışmaya](https://stackoverflow.com/questions/17666161/sending-email-from-azure)bakın.

## <a name="why-does-my-wordpress-site-redirect-to-another-url"></a>WordPress sitesi neden başka bir URL 'ye yönlendirilir?

Yakın zamanda Azure 'a geçiş yaptıysanız, WordPress eski etki alanı URL 'sine yeniden yönlendirebilir. Bunun nedeni MySQL veritabanındaki bir ayardır.

WordPress arkadaş +, yeniden yönlendirme URL 'sini doğrudan veritabanında güncelleştirmek için kullanabileceğiniz bir Azure Site uzantısıdır. WordPress arkadaş + kullanımı hakkında daha fazla bilgi için bkz. wordpress [arkadaş + Ile WordPress araçları ve MySQL geçişi](https://www.electrongeek.com/blog/2016/12/21/wordpress-buddy-site-extension-for-app-service-on-windows).

Alternatif olarak, yeniden yönlendirme URL 'sini SQL sorguları veya PHPMyAdmin kullanarak el ile güncelleştirmeyi tercih ediyorsanız, bkz. [WordPress: yanlış URL 'ye yönlendirme](/archive/blogs/azureossds/wordpress-redirecting-to-wrong-url).

## <a name="how-do-i-change-my-wordpress-sign-in-password"></a>WordPress oturum açma parolamı Nasıl yaparım? değiştirilsin mi?

WordPress oturum açma parolanızı unuttuysanız, bunu güncelleştirmek için WordPress arkadaş + kullanabilirsiniz. Parolanızı sıfırlamak için WordPress arkadaş + Azure Site uzantısını yükledikten sonra WordPress [arkadaş + Ile WordPress araçları ve MySQL geçişi](https://www.electrongeek.com/blog/2016/12/21/wordpress-buddy-site-extension-for-app-service-on-windows)bölümünde açıklanan adımları uygulayın.

## <a name="i-cant-sign-in-to-wordpress-how-do-i-resolve-this"></a>WordPress 'de oturum açamıyorum. Bu sorunu nasıl çözebilirim?

Son zamanlarda bir eklenti yükledikten sonra WordPress 'nin kilitlendiğini fark ederseniz, bir eklenti hatalı olabilir. WordPress arkadaş +, WordPress içindeki eklentileri devre dışı bırakmanızı sağlayacak bir Azure Site uzantısıdır. Daha fazla bilgi için bkz. wordpress [arkadaş + Ile WordPress araçları ve MySQL geçişi](https://www.electrongeek.com/blog/2016/12/21/wordpress-buddy-site-extension-for-app-service-on-windows).

## <a name="how-do-i-migrate-my-wordpress-database"></a>WordPress veritabanını Nasıl yaparım? geçirmek istiyor musunuz?

WordPress web sitenize bağlı MySQL veritabanını geçirmek için birden çok seçeneğiniz vardır:

* Geliştiriciler: [komut istemi veya PHPMyAdmin](/archive/blogs/azureossds/migrating-data-between-mysql-databases-using-kudu-console-azure-app-service) kullanın
* Geliştiriciler olmayanlar: [WordPress arkadaş +](https://www.electrongeek.com/blog/2016/12/21/wordpress-buddy-site-extension-for-app-service-on-windows) kullanın

## <a name="how-do-i-help-make-wordpress-more-secure"></a>Nasıl yaparım? WordPress 'in daha güvenli olmasına yardımcı olur mu?

WordPress için en iyi güvenlik yöntemleri hakkında bilgi edinmek için bkz. [Azure 'Da WordPress güvenliği Için en iyi uygulamalar](/archive/blogs/azureossds/best-practices-for-wordpress-security-on-azure).

## <a name="i-am-trying-to-use-phpmyadmin-and-i-see-the-message-access-denied-how-do-i-resolve-this"></a>PHPMyAdmin kullanmaya çalışıyorum ve "erişim engellendi" iletisini görüyorum. Bu sorunu nasıl çözebilirim?

Uygulama içi MySQL özelliği henüz bu App Service örneğinde çalışmıyorsa bu sorunla karşılaşabilirsiniz. Sorunu çözmek için, Web sitenize erişmeyi deneyin. Bu, uygulama içi MySQL işlemi dahil olmak üzere gerekli işlemleri başlatır. Uygulama içi MySQL çalıştığını doğrulamak için, Işlem Gezgini 'nde mysqld.exe işlemlerde listelendiğinden emin olun.

Uygulama içi MySQL çalıştığından emin olduktan sonra PHPMyAdmin kullanmayı deneyin.

## <a name="i-get-an-http-403-error-when-i-try-to-import-or-export-my-mysql-in-app-database-by-using-phpmyadmin-how-do-i-resolve-this"></a>PHPMyadmin kullanarak uygulama içi MySQL veritabanını içeri aktarmaya veya dışarı aktarmaya çalıştığımda bir HTTP 403 hatası alıyorum. Bu sorunu nasıl çözebilirim?

Chrome 'un eski bir sürümünü kullanıyorsanız, bilinen bir hata yaşıyor olabilirsiniz. Sorunu çözmek için Chrome 'ın daha yeni bir sürümüne yükseltin. Ayrıca, sorunun gerçekleşmediğinden Internet Explorer veya Microsoft Edge gibi farklı bir tarayıcı kullanmayı deneyin.