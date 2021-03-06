---
title: Azure HDInsight 'ta kimlik doğrulama sorunları
description: Azure HDInsight 'ta kimlik doğrulama sorunları
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/24/2020
ms.openlocfilehash: b5a55b3b5f9affcd9f34e1c0d4c439a7ada8c0b9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99095133"
---
# <a name="authentication-issues-in-azure-hdinsight"></a>Azure HDInsight 'ta kimlik doğrulama sorunları

Bu makalede, Azure HDInsight kümeleriyle etkileşim kurarken sorun giderme adımları ve olası çözümleri açıklanmaktadır.

Azure Data Lake (Gen1 veya Gen2) tarafından desteklenen güvenli kümeler üzerinde, etki alanı kullanıcıları HDI ağ geçidi aracılığıyla (Apache ambarı portalında oturum açmak gibi) küme hizmetlerinde oturum açtığında, HDI Gateway önce Azure Active Directory (Azure AD) ' dan bir OAuth belirteci edinmeye çalışır ve ardından Azure AD DS 'nden bir Kerberos bileti elde eder. Kimlik doğrulama, bu aşamaların her birinde başarısız olabilir. Bu makale, bu sorunlardan bazılarının hatalarını ayıklamaya yönelik olarak tasarlanmıştır.

Kimlik doğrulama başarısız olduğunda kimlik bilgileri istenir. Bu iletişim kutusunu iptal ederseniz hata iletisi yazdırılır. Yaygın hata iletilerinin bazıları şunlardır:

## <a name="invalid_grant-or-unauthorized_client-50126"></a>invalid_grant veya unauthorized_client, 50126

### <a name="issue"></a>Sorun

Hata kodu 50126 olan Federasyon kullanıcıları için oturum açma başarısız oluyor (bulut kullanıcıları için oturum açma başarılı olur). Hata iletisi şuna benzer:

```
Reason: Bad Request, Detailed Response: {"error":"invalid_grant","error_description":"AADSTS70002: Error validating credentials. AADSTS50126: Invalid username or password\r\nTrace ID: 09cc9b95-4354-46b7-91f1-efd92665ae00\r\n Correlation ID: 4209bedf-f195-4486-b486-95a15b70fbe4\r\nTimestamp: 2019-01-28 17:49:58Z","error_codes":[70002,50126], "timestamp":"2019-01-28 17:49:58Z","trace_id":"09cc9b95-4354-46b7-91f1-efd92665ae00","correlation_id":"4209bedf-f195-4486-b486-95a15b70fbe4"}
```

### <a name="cause"></a>Nedeni

Azure AD hata kodu 50126, `AllowCloudPasswordValidation` ilkenin kiracı tarafından ayarlanmadığını gösterir.

### <a name="resolution"></a>Çözüm

Azure AD kiracısının genel Yöneticisi, Azure AD 'nin ADFS ile desteklenen kullanıcılar için parola karmaları kullanmasına olanak sağlamalıdır.  `AllowCloudPasswordValidationPolicy` [Hdınsight 'Ta kurumsal güvenlik paketi kullanın](../domain-joined/apache-domain-joined-architecture.md)makalesinde gösterildiği gibi uygulayın.

---

## <a name="invalid_grant-or-unauthorized_client-50034"></a>invalid_grant veya unauthorized_client, 50034

### <a name="issue"></a>Sorun

Oturum açma 50034 hata koduyla başarısız oluyor. Hata iletisi şuna benzer:

```
{"error":"invalid_grant","error_description":"AADSTS50034: The user account Microsoft.AzureAD.Telemetry.Diagnostics.PII does not exist in the 0c349e3f-1ac3-4610-8599-9db831cbaf62 directory. To sign into this application, the account must be added to the directory.\r\nTrace ID: bbb819b2-4c6f-4745-854d-0b72006d6800\r\nCorrelation ID: b009c737-ee52-43b2-83fd-706061a72b41\r\nTimestamp: 2019-04-29 15:52:16Z", "error_codes":[50034],"timestamp":"2019-04-29 15:52:16Z","trace_id":"bbb819b2-4c6f-4745-854d-0b72006d6800", "correlation_id":"b009c737-ee52-43b2-83fd-706061a72b41"}
```

### <a name="cause"></a>Nedeni

Kullanıcı adı yanlış (yok). Kullanıcı Azure portal ' de kullanılan Kullanıcı adını kullanmıyor.

### <a name="resolution"></a>Çözüm

Bu portalda çalışacak Kullanıcı adını kullanın.

---

## <a name="invalid_grant-or-unauthorized_client-50053"></a>invalid_grant veya unauthorized_client, 50053

### <a name="issue"></a>Sorun

Kullanıcı hesabı kilitli, hata kodu 50053. Hata iletisi şuna benzer:

```
{"error":"unauthorized_client","error_description":"AADSTS50053: You've tried to sign in too many times with an incorrect user ID or password.\r\nTrace ID: 844ac5d8-8160-4dee-90ce-6d8c9443d400\r\nCorrelation ID: 23fe8867-0e8f-4e56-8764-0cdc7c61c325\r\nTimestamp: 2019-06-06 09:47:23Z","error_codes":[50053],"timestamp":"2019-06-06 09:47:23Z","trace_id":"844ac5d8-8160-4dee-90ce-6d8c9443d400","correlation_id":"23fe8867-0e8f-4e56-8764-0cdc7c61c325"}
```

### <a name="cause"></a>Nedeni

Hatalı parola ile çok fazla oturum açma denemesi.

### <a name="resolution"></a>Çözüm

30 dakika bekleyin, kimlik doğrulamaya çalışıyor olabilecek tüm uygulamaları durdurun.

---

## <a name="invalid_grant-or-unauthorized_client-50053-2"></a>invalid_grant veya unauthorized_client, 50053 (#2)

### <a name="issue"></a>Sorun

Parolanın kullanım geçerliliği, hata kodu 50053. Hata iletisi şuna benzer:

```
{"error":"user_password_expired","error_description":"AADSTS50055: Password is expired.\r\nTrace ID: 241a7a47-e59f-42d8-9263-fbb7c1d51e00\r\nCorrelation ID: c7fe4a42-67e4-4acd-9fb6-f4fb6db76d6a\r\nTimestamp: 2019-06-06 17:29:37Z","error_codes":[50055],"timestamp":"2019-06-06 17:29:37Z","trace_id":"241a7a47-e59f-42d8-9263-fbb7c1d51e00","correlation_id":"c7fe4a42-67e4-4acd-9fb6-f4fb6db76d6a","suberror":"user_password_expired","password_change_url":"https://portal.microsoftonline.com/ChangePassword.aspx"}
```

### <a name="cause"></a>Nedeni

Parolanın zaman aşımına uğradı.

### <a name="resolution"></a>Çözüm

Azure portal parolayı değiştirin (Şirket içi sisteminizde) ve eşitlemenin yakalanması için 30 dakika bekleyin.

---

## <a name="interaction_required"></a>interaction_required

### <a name="issue"></a>Sorun

Hata iletisi Al `interaction_required` .

### <a name="cause"></a>Nedeni

Kullanıcıya koşullu erişim ilkesi veya MFA uygulanıyor. Etkileşimli kimlik doğrulaması henüz desteklenmediğinden, kullanıcı veya küme MFA'dan / Koşullu erişimden muaf tutulmalıdır. Kümeyi muaf tutmayı tercih ederseniz (IP adresi tabanlı muafiyet ilkesi), bu sanal ağ için AD 'nin etkinleştirildiğinden emin olun `ServiceEndpoints` .

### <a name="resolution"></a>Çözüm

Azure Active Directory Domain Services kullanarak, koşullu erişim ilkesini kullanın ve [Kurumsal güvenlik paketi Ile HDInsight kümesi yapılandırma](./apache-domain-joined-configure-using-azure-adds.md)bölümünde GÖSTERILDIĞI gibi MFA 'dan HDInsight kümelerini muaf tutabilirsiniz.

---

## <a name="sign-in-denied"></a>Oturum açma reddedildi

### <a name="issue"></a>Sorun

Oturum açma reddedildi.

### <a name="cause"></a>Nedeni

Bu aşamaya ulaşmak için, OAuth kimlik doğrulaması bir sorun değildir, ancak Kerberos kimlik doğrulaması olur. Bu küme ADLS tarafından desteklenir, Kerberos kimlik doğrulaması denenmeden önce OAuth oturum açma işlemi başarılı oldu. Bulunan kümeler üzerinde OAuth oturum açma denenmez. Kerberos hata benzeri parola karmalarının eşitlenmemiş, Kullanıcı hesabının Azure AD DS 'da kilitlediği ve bu şekilde devam etmesinin pek çok nedeni olabilir. Parola karmaları yalnızca Kullanıcı parolayı değiştirdiğinde eşitlenir. Azure AD DS örneğini oluşturduğunuzda, oluşturulduktan sonra değiştirilen parolaları eşitlemeye başlar. Kullanılmadan önce ayarlanmış olan parolaları geriye dönük olarak eşitler.

### <a name="resolution"></a>Çözüm

Parolaların eşitlenmiş olabileceğini düşünüyorsanız, parolayı değiştirmeyi deneyin ve birkaç dakika eşitlemesini bekleyin.

SSH 'yi bir ile deneyin etki alanına katılmış bir makineden aynı kullanıcı kimlik bilgilerini kullanarak kimlik doğrulamaya (kinit) gerek duyarsınız. Yerel bir kullanıcıyla baş/kenar düğümüne SSH ekleyin ve ardından kinit ' i çalıştırın.

---

## <a name="kinit-fails"></a>kinit başarısız

### <a name="issue"></a>Sorun

Kinit başarısız olur.

### <a name="cause"></a>Nedeni

Olmadığına.

### <a name="resolution"></a>Çözüm

Başarılı olması için, hesabınızı bilmeniz gerekir `sAMAccountName` (Bu, bölge olmadan kısa hesap adıdır). `sAMAccountName` genellikle hesap ön eki olur (Bob gibi `bob@contoso.com` ). Bazı kullanıcılar için farklı olabilir. Bilgilerinizi öğrenmek için dizine gözatıp araymanıza gerek duyarsınız `sAMAccountName` .

Şunları bulmanın yolları `sAMAccountName` :

* Yerel ambarı yöneticisini kullanarak ambarı 'nda oturum açabilirseniz, kullanıcı listesine bakın.

* [Etki alanına katılmış bir Windows makineniz](../../active-directory-domain-services/tutorial-create-management-vm.md)varsa, gezinmek Için standart Windows ad Araçları ' nı kullanabilirsiniz. Bu, etki alanında bir çalışan hesabı gerektirir.

* Baş düğümden, ' yi aramak için SAMBA komutlarını kullanabilirsiniz. Bu, geçerli bir Kerberos oturumu gerektirir (başarılı kinit). net ads arama "(userPrincipalName = Bob *)"

    Arama/tarama sonuçları sizi `sAMAccountName` özniteliği göstermelidir. Ayrıca,, vb. gibi diğer özniteliklere de bakabilirsiniz. `pwdLastSet` `badPasswordTime` `userPrincipalName` Bu özelliklerin beklediğiniz şekilde eşleşip eşleşmeyeceğini görebilirsiniz.

---

## <a name="kinit-fails-with-preauthentication-failure"></a>kinit ön kimlik doğrulama hatasıyla başarısız oluyor

### <a name="issue"></a>Sorun

Kinit hata vererek başarısız olur `Preauthentication` .

### <a name="cause"></a>Nedeni

Yanlış Kullanıcı adı veya parola.

### <a name="resolution"></a>Çözüm

Kullanıcı adınızı ve parolanızı denetleyin. Ayrıca yukarıda açıklanan diğer özellikleri de denetleyin. Ayrıntılı hata ayıklamayı etkinleştirmek için, `export KRB5_TRACE=/tmp/krb.log` kinit 'i denemeden önce oturumdan çalıştırın.

---

## <a name="job--hdfs-command-fails-due-to-tokennotfoundexception"></a>TokenNotFoundException nedeniyle iş/işlem komutu başarısız oluyor

### <a name="issue"></a>Sorun

İş/IBir komut nedeniyle başarısız oldu `TokenNotFoundException` .

### <a name="cause"></a>Nedeni

İş/komutun başarılı olması için gerekli OAuth erişim belirteci bulunamadı. ADLS/ABFS sürücüsü, depolama istekleri yapmadan önce kimlik bilgisi hizmetinden OAuth erişim belirtecini almaya çalışır. Bu belirteç, aynı kullanıcıyı kullanarak ambarı portalında oturum açtığınızda kaydedilir.

### <a name="resolution"></a>Çözüm

İşi çalıştırmak için kimliği kullanılan Kullanıcı adı aracılığıyla, ambarı portalında bir kez başarıyla oturum açtığınızdan emin olun.

---

## <a name="error-fetching-access-token"></a>Erişim belirteci getirilirken hata oluştu

### <a name="issue"></a>Sorun

Kullanıcı hata iletisini alır `Error fetching access token` .

### <a name="cause"></a>Nedeni

Bu hata, kullanıcılar ACL 'Leri kullanarak ADLS 2. erişmeyi denediğinde ve Kerberos belirtecinin süresi dolduğunda zaman zaman oluşur.

### <a name="resolution"></a>Çözüm

* Azure Data Lake Storage 1. için, tarayıcı önbelleğini temizleyin ve yeniden ambarı 'nda oturum açın.

* Azure Data Lake Storage 2. için, `/usr/lib/hdinsight-common/scripts/RegisterKerbTicketAndOAuth.sh <upn>` kullanıcının oturum açmaya çalıştığı kullanıcı için şunu çalıştırın

---

## <a name="next-steps"></a>Sonraki adımlar

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]