---
title: MySQL için Azure veritabanı için sertifika döndürme
description: MySQL için Azure veritabanı 'nı etkileyecek kök sertifika değişikliklerinin yakında değişiklikler hakkında bilgi edinin
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 04/08/2021
ms.openlocfilehash: 4b218a9481fdd5970fd6fc8fa6a1d071161e5b58
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107313373"
---
# <a name="understanding-the-changes-in-the-root-ca-change-for-azure-database-for-mysql-single-server"></a>MySQL için Azure veritabanı tek sunucu için kök CA değişiklik değişikliklerini anlama

MySQL için Azure veritabanı tek sunucu, standart bakım ve güvenlik en iyi uygulamalarının bir parçası olarak **15 şubat 2021 (02/15/2021)** tarihinde kök sertifika değişikliğini başarıyla tamamladı. Bu makalede, değişiklikler hakkında daha fazla ayrıntı, etkilenen kaynaklar ve uygulamanızın veritabanı sunucunuza bağlantıyı korumasından emin olmak için gereken adımlar verilmektedir.

> [!NOTE]
> Bu makale yalnızca [MySQL Için Azure veritabanı-tek sunucu](single-server-overview.md) için geçerlidir. [MySQL Için Azure veritabanı-esnek sunucu](flexible-server/overview.md), SSL üzerinden iletişim kurmak için gereken sertifika, [DigiCert genel kök CA](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem) 'dır
>
> Bu makale, Microsoft 'un artık kullandığı bir terim olan _bağımlı_ dönem başvuruları içerir. Terim yazılımlardan kaldırıldığında, bu makaleden kaldıracağız.
>

#### <a name="why-is-a-root-certificate-update-required"></a>Kök sertifika güncelleştirmesi neden gereklidir?

MySQL için Azure veritabanı kullanıcıları, [burada](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem)bulunan MySQL sunucusuna bağlanmak için yalnızca önceden tanımlanmış sertifikayı kullanabilir. Bununla birlikte, [sertifika yetkilisi (CA) tarayıcı Forumu](https://cabforum.org/),   CA satıcıları tarafından verilen birden çok sertifikanın uyumsuz olması için en son yayımlanmış raporlar.

Sektörün uyumluluk gereksinimleri uyarınca, CA satıcıları uyumlu olmayan CA 'Lar için CA sertifikalarını iptal etmeyi, sunucuların uyumlu CA 'lar tarafından verilen sertifikaları kullanmasını ve bu uyumlu CA 'ların CA sertifikaları tarafından imzalanmasını gerektirir. MySQL için Azure veritabanı, bu uyumlu olmayan sertifikalardan birini kullandığından, MySQL sunucularınızda olası tehdidi en aza indirmek için sertifikayı uyumlu sürüme döndürmemiz gerekiyordu.

Yeni sertifika dışarı alınır ve 15 Şubat 2021 (02/15/2021) itibariyle geçerli olur.

#### <a name="what-change-was-performed-on-february-15-2021-02152021"></a>15 Şubat 2021 ' de hangi değişiklik yapıldı (02/15/2021)?

15 Şubat 2021 ' de, [Baltimorecyıbertrustroot kök](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) sertifikası aynı [baltimorecerbertrustroot kök sertifikasının](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) **uyumlu bir sürümüyle** değiştirilmiştir ve bu da mevcut müşterilerin sunucu bağlantılarının hiçbir etkisi yoktur. Bu değişiklik sırasında, [Baltimorecyıbertrustroot kök sertifikası](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) [DigiCertGlobalRootG2](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem) ile **değiştirilmez** ve bu değişiklik, müşterilerin değişikliği yapmasına daha fazla zaman tanımak için ertelenir.

#### <a name="do-i-need-to-make-any-changes-on-my-client-to-maintain-connectivity"></a>Bağlantıyı sürdürmek için istemcimde herhangi bir değişiklik yapmam gerekiyor mu?

İstemci tarafında değişiklik yapılması gerekmez. Aşağıdaki önceki önerimizi izlediyseniz, **Baltimorecyıbertrustroot sertifikası** birleştirilmiş CA sertifikasından kaldırılmadığı sürece bağlanmaya devam edebilirsiniz. **Bağlantıyı sürdürmek için, daha fazla bildirimde bulunana CA Sertifikanızda Baltimorecyıbertrustroot ' yi korumanızı öneririz.**

###### <a name="previous-recommendation"></a>Önceki öneri

Sertifikaların beklenmedik şekilde iptal edilmesine veya iptal edilmiş bir sertifikayı güncelleştirmeye neden olduğu için uygulamanızın kullanılabilirliğinin kesintiye uğramasını önlemek için aşağıdaki adımları kullanın. Bu düşünce, geçerli sertifikayı ve yenisini birleştiren yeni bir *. pem* dosyası oluşturmak ve SSL sertifika doğrulaması sırasında, izin verilen değerlerden biri kullanılacaktır. Aşağıdaki adımlara bakın:

1. Aşağıdaki bağlantılardan BaltimoreCyberTrustRoot & DigiCertGlobalRootG2 kök CA 'sını indirin:

    * [https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem)
    * [https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem)

2. Hem **Baltimorecyıbertrustroot** hem de **DigiCertGlobalRootG2** sertifikaları dahil olmak üzere bir birleştirilmiş CA sertifika deposu oluşturun.

    * Java (MySQL Connector/J) kullanıcıları için şunu yürütün:

      ```console
      keytool -importcert -alias MySQLServerCACert -file D:\BaltimoreCyberTrustRoot.crt.pem -keystore truststore -storepass password -noprompt
      ```

      ```console
      keytool -importcert -alias MySQLServerCACert2 -file D:\DigiCertGlobalRootG2.crt.pem -keystore truststore -storepass password -noprompt
      ```

      Ardından orijinal anahtar deposu dosyasını yeni oluşturulan yeni bir anahtar ile değiştirin:

      * System. setProperty ("javax. net. SSL. trustStore", "path_to_truststore_file");
      * System. setProperty ("javax. net. SSL. trustStorePassword", "Password");

    * .NET (MySQL Connector/NET, MySQLConnector) kullanıcıları için, Windows sertifika deposunda, güvenilen kök sertifika yetkililerinde **Baltimorecyıbertrustroot** ve **DigiCertGlobalRootG2** 'ın aynı olduğundan emin olun. Herhangi bir sertifika yoksa, eksik sertifikayı içeri aktarın.

      :::image type="content" source="media/overview/netconnecter-cert.png" alt-text="MySQL için Azure veritabanı .NET sertifika diyagramı":::

    * Linux üzerinde SSL_CERT_DIR kullanarak .NET kullanıcıları için, SSL_CERT_DIR tarafından belirtilen dizinde **Baltimorecrivbertrustroot** ve **DigiCertGlobalRootG2** 'ın aynı olduğundan emin olun. Herhangi bir sertifika yoksa, eksik sertifika dosyasını oluşturun.

    * Diğer (MySQL Client/MySQL çalışma ekranı/C/C++/Go/Python/Ruby/PHP/NodeJS/Perl/Swift) kullanıcıları için iki CA sertifika dosyasını aşağıdaki biçimde birleştirebilirsiniz:

      ```
      -----BEGIN CERTIFICATE-----
      (Root CA1: BaltimoreCyberTrustRoot.crt.pem)
      -----END CERTIFICATE-----
      -----BEGIN CERTIFICATE-----
      (Root CA2: DigiCertGlobalRootG2.crt.pem)
      -----END CERTIFICATE-----
      ```

3. Özgün kök CA ped dosyasını birleştirilmiş kök CA dosyası ile değiştirin ve uygulamanızı/istemcinizi yeniden başlatın.

   Daha sonra, yeni sertifika sunucu tarafında dağıtıldıktan sonra, CA PEI dosyanızı DigiCertGlobalRootG2. CRT. ped olarak değiştirebilirsiniz.

> [!NOTE]
> Lütfen sertifika değişikliği yapılıncaya kadar **Baltidaha fazla sertifikayı** bırakıp değiştirmeyin. Değişiklik yapıldıktan sonra bir iletişim göndereceğiz ve sonra da **Baltidaha fazla sertifikayı** bırakmak güvenli olacaktır.

#### <a name="why-was-baltimorecybertrustroot-certificate-not-replaced-to-digicertglobalrootg2-during-this-change-on-february-15-2021"></a>15 Şubat 2021 ' de bu değişiklik sırasında Baltimorecyıbertrustroot sertifikası neden DigiCertGlobalRootG2 ile değiştirilmedi?

Bu değişiklik için müşteri hazırlığını değerlendirdik ve çok sayıda müşterinin bu değişikliği yönetmek için ek sağlama süresi arıyor olması gerçekleştirilmiş. Müşterilere hazırlık için daha fazla sağlama süresi sağlamak üzere, sertifika değişikliğini en az bir yıl için DigiCertGlobalRootG2 olarak ertelemenizi ve müşterilere ve son kullanıcılara yeterli sağlama süresi sağladığını karardık.

Kullanıcılara yönelik önerimiz, birleştirilmiş bir sertifika oluşturmak ve sunucunuza bağlanmak, ancak bunu kaldırmak üzere bir iletişim gönderene kadar Baltimoreconbertrustroot sertifikasını kaldırmayın.

#### <a name="what-if-we-removed-the-baltimorecybertrustroot-certificate"></a>Baltimorecyıbertrustroot sertifikasını kaldırdık ne olursa?

MySQL sunucusu için Azure veritabanı 'na bağlanırken bağlantı hatalarıyla karşılaşacaksınız. Bağlantıyı sürdürmek için [Baltimorecrivbertrustroot](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) SERTIFIKASı ile [SSL 'yi yapılandırmanız](howto-configure-ssl.md) gerekir.

## <a name="frequently-asked-questions"></a>Sık sorulan sorular

#### <a name="if-im-not-using-ssltls-do-i-still-need-to-update-the-root-ca"></a>SSL/TLS kullanmıyorsanız yine de kök CA 'yı güncelleştirmem gerekir mi?

  SSL/TLS kullanmıyorsanız hiçbir eylem gerekmez.

#### <a name="if-im-using-ssltls-do-i-need-to-restart-my-database-server-to-update-the-root-ca"></a>SSL/TLS kullanıyorum, kök CA 'yı güncelleştirmek için veritabanı sunucusunu yeniden başlatmem gerekir mi?

Hayır, yeni sertifikayı kullanmaya başlamak için veritabanı sunucusunu yeniden başlatmanız gerekmez. Bu kök sertifika, istemci tarafı bir değişiklik ve gelen istemci bağlantılarının veritabanı sunucusuna bağlanabildiklerinden emin olmak için yeni sertifikayı kullanması gerekir.

#### <a name="how-do-i-know-if-im-using-ssltls-with-root-certificate-verification"></a>Nasıl yaparım? kök sertifika doğrulaması ile SSL/TLS mi kullandığım hakkında bilgi mı var?

Bağlantı dizenizi inceleyerek bağlantılarınızın kök sertifikayı doğrulayıp doğrulamayıp belirleyemeyeceğini belirleyebilirsiniz.

* Bağlantı dizeniz veya içeriyorsa `sslmode=verify-ca` `sslmode=verify-identity` , sertifikayı güncelleştirmeniz gerekir.
* Bağlantı dizeniz,, `sslmode=disable` veya içerdiğinde, `sslmode=allow` `sslmode=prefer` `sslmode=require` sertifikaları güncelleştirmeniz gerekmez.
* Bağlantı dizeniz sslmode 'u belirtmezse, sertifikaları güncelleştirmeniz gerekmez.

Bağlantı dizesini soyutlayan bir istemci kullanıyorsanız, sertifikaları doğrulayıp doğrulamadığını anlamak için istemci belgelerini gözden geçirin.

#### <a name="what-is-the-impact-of-using-app-service-with-azure-database-for-mysql"></a>MySQL için Azure veritabanı ile App Service kullanmanın etkisi nedir?

MySQL için Azure veritabanı 'na bağlanan Azure Uygulama Hizmetleri için, uygulamanızla birlikte SSL kullanma yönteminize bağlı olarak iki olası senaryo vardır.

* Bu yeni sertifika platform düzeyinde App Service eklendi. Uygulamanızdaki App Service platforma dahil olan SSL sertifikalarını kullanıyorsanız hiçbir işlem gerekmez. En yaygın senaryo budur.
* Kodunuzda SSL sertifika dosyasının yolunu açıkça dahil ediyorsanız, yeni sertifikayı indirmeniz ve yukarıda belirtilen şekilde birleştirilmiş bir sertifika oluşturmanız ve sertifika dosyasını kullanmanız gerekir. Bu senaryonun iyi bir örneği, [App Service belgelerinde](../app-service/tutorial-multi-container-app.md#configure-database-variables-in-wordpress)paylaşıldığından App Service özel kapsayıcılar kullandığınızda oluşur. Bu, yaygın olmayan bir senaryodur ancak bunu kullanan bazı kullanıcıları gördük.

#### <a name="what-is-the-impact-of-using-azure-kubernetes-services-aks-with-azure-database-for-mysql"></a>MySQL için Azure veritabanı ile Azure Kubernetes Hizmetleri 'ni (AKS) kullanmanın etkisi nedir?

Azure Kubernetes Services (AKS) kullanarak MySQL için Azure veritabanı 'na bağlanmaya çalışıyorsanız, adanmış bir müşterilerin ana bilgisayar ortamından erişime de benzer. [Buradaki](../aks/ingress-own-tls.md)adımlara bakın.

#### <a name="what-is-the-impact-of-using-azure-data-factory-to-connect-to-azure-database-for-mysql"></a>MySQL için Azure veritabanı 'na bağlanmak üzere Azure Data Factory kullanmanın etkisi nedir?

Bağlayıcı, Azure Integration Runtime kullanan bir bağlayıcı için Azure 'da barındırılan ortamdaki Windows sertifika deposundaki sertifikaları kullanır. Bu sertifikalar zaten yeni uygulanan sertifikalarla uyumludur ve bu nedenle herhangi bir eylem gerekmez.

Şirket içinde barındırılan Integration Runtime kullanan bir bağlayıcı için, bağlantı dizinizdeki SSL sertifikası dosyasının yolunu açıkça eklediğiniz yerde, [yeni sertifikayı](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem) indirmeniz ve bağlantı dizesini kullanmak için güncelleştirmeniz gerekir.

#### <a name="do-i-need-to-plan-a-database-server-maintenance-downtime-for-this-change"></a>Bu değişiklik için bir veritabanı sunucusu bakım kapalı kalma süresi planlıyorum mi?

Hayır. Değişiklik yalnızca istemci tarafında veritabanı sunucusuna bağlanmak için olduğundan, bu değişiklik için veritabanı sunucusu için gerekli bakım kapalı kalma süresi yoktur.

#### <a name="if-i-create-a-new-server-after-february-15-2021-02152021-will-i-be-impacted"></a>15 Şubat 2021 (02/15/2021) sonrasında yeni bir sunucu oluştururum, bundan etkilenmem gerekir mi?

15 Şubat 2021 (02/15/2021) ' den sonra oluşturulan sunucular için, uygulamalarınızın SSL kullanarak bağlanması için [Baltimorecyıbertrustroot](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) 'yi kullanmaya devam edersiniz.

#### <a name="how-often-does-microsoft-update-their-certificates-or-what-is-the-expiry-policy"></a>Microsoft sertifikalarını ne sıklıkla güncelleştiriyor veya süre sonu ilkesi nedir?

MySQL için Azure veritabanı tarafından kullanılan bu sertifikalar, güvenilen sertifika yetkilileri (CA) tarafından sağlanır. Bu nedenle, bu sertifikaların desteklenmesi CA tarafından bu sertifikaların desteğine bağlıdır. [Baltimorecerbertrustroot](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) sertifikası 2025 ' de sona ermek üzere zamanlandı. bu nedenle, Microsoft 'un süre sonu öncesinde bir sertifika değişikliği gerçekleştirmesi gerekir. Ayrıca, bu önceden tanımlanmış sertifikalarda öngörülemeyen hatalar varsa, Microsoft 'un her zaman güvenli ve uyumlu olduğundan emin olmak için, 15 Şubat 2021 ' de gerçekleştirilen değişikliğe benzer şekilde Microsoft 'un sertifika döndürmesini yapması gerekir.

#### <a name="if-im-using-read-replicas-do-i-need-to-perform-this-update-only-on-source-server-or-the-read-replicas"></a>Okuma çoğaltmaları kullanıyorum, bu güncelleştirmeyi yalnızca kaynak sunucuda veya okuma çoğaltmalarıyla gerçekleştirmem gerekir mi?

Bu güncelleştirme istemci tarafı değişikliği olduğundan, istemci Çoğaltma sunucusundan veri okumak için kullanılıyorsa, bu istemciler için değişiklikleri de uygulamanız gerekir.

#### <a name="if-im-using-data-in-replication-do-i-need-to-perform-any-action"></a>Veri çoğaltma 'yı kullanıyorsam herhangi bir eylem gerçekleştirmem gerekir mi?

MySQL için Azure veritabanı 'na bağlanmak üzere [veri çoğaltma](concepts-data-in-replication.md) 'yı kullanıyorsanız, göz önünde bulundurmanız gereken iki şey vardır:

* Veri çoğaltma bir sanal makineden (Şirket içi veya Azure sanal makinesi) MySQL için Azure veritabanı 'na ait ise, çoğaltmayı oluşturmak için SSL 'nin kullanıldığını denetlemeniz gerekir. **BAĞıMLı durumu göster** ' i çalıştırın ve aşağıdaki ayarı denetleyin.  

    ```azurecli-interactive
    Master_SSL_Allowed            : Yes
    Master_SSL_CA_File            : ~\azure_mysqlservice.pem
    Master_SSL_CA_Path            :
    Master_SSL_Cert               : ~\azure_mysqlclient_cert.pem
    Master_SSL_Cipher             :
    Master_SSL_Key                : ~\azure_mysqlclient_key.pem
    ```

    CA_file, SSL_Cert ve SSL_Key için sertifikanın sağlandığını görürseniz, [yeni sertifikayı](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem) ekleyerek ve birleştirilmiş bir sertifika dosyası oluşturarak dosyayı güncelleştirmeniz gerekir.

* Veri çoğaltma iki MySQL için Azure veritabanı sunucusu arasında ise, **çağrı MySQL.az_replication_change_master** yürüterek çoğaltmayı sıfırlamanız ve son parametre [master_ssl_ca](howto-data-in-replication.md#link-source-and-replica-servers-to-start-data-in-replication)yeni ikili kök sertifikayı sağlamanız gerekir.

#### <a name="is-there-a-server-side-query-to-determine-whether-ssl-is-being-used"></a>SSL kullanılıp kullanılmadığını tespit etmek için sunucu tarafı bir sorgu var mı?

Sunucuya bağlanmak için SSL bağlantısı kullanıp kullandığınızı doğrulamak için [SSL doğrulaması](howto-configure-ssl.md#step-4-verify-the-ssl-connection)' na başvurun.

#### <a name="is-there-an-action-needed-if-i-already-have-the-digicertglobalrootg2-in-my-certificate-file"></a>Sertifika dosyasında DigiCertGlobalRootG2 zaten varsa gerekli bir eylem var mı?

Hayır. Sertifika dosyanızda zaten **DigiCertGlobalRootG2** varsa herhangi bir işlem yapmanız gerekmez.

#### <a name="what-if-i-have-further-questions"></a>Başka sorularım varsa ne yapmalıyım?

Sorular için [Microsoft Q&A](mailto:AzureDatabaseforMySQL@service.microsoft.com)'daki topluluk uzmanlarının yanıtlarını alın. Destek planınız varsa ve teknik yardıma ihtiyacınız varsa [bizimle iletişime geçin](mailto:AzureDatabaseforMySQL@service.microsoft.com).
