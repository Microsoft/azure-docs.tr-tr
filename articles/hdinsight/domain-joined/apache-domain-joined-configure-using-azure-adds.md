---
title: Azure AD ile kurumsal güvenlik-Azure HDInsight
description: Azure Active Directory Domain Services kullanarak HDInsight Kurumsal Güvenlik Paketi kümesi ayarlamayı ve yapılandırmayı öğrenin.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seodec18
ms.date: 10/02/2019
ms.openlocfilehash: d31de590d8dc40ff3d5c1dfab060ee4f045ba4d1
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73044659"
---
# <a name="enterprise-security-package-configurations-with-azure-active-directory-domain-services-in-hdinsight"></a>HDInsight 'ta Azure Active Directory Domain Services olan Kurumsal Güvenlik Paketi konfigürasyonları

Kurumsal Güvenlik Paketi (ESP) kümeleri, Azure HDInsight kümelerinde çoklu Kullanıcı erişimi sağlar. Etki alanı kullanıcılarının kümelerle kimlik doğrulamak ve büyük veri işleri çalıştırmak için etki alanı kimlik bilgilerini kullanabilmesi için, ESP içeren HDInsight kümeleri bir etki alanına bağlıdır.

Bu makalede, Azure Active Directory Domain Services (Azure AD DS) kullanarak bir HDInsight kümesini ESP ile yapılandırmayı öğreneceksiniz.

> [!NOTE]  
> ESP, şu küme türleri için HDInsight 3,6 ve 4,0 ' de genel kullanıma sunulmuştur: Apache Spark, Interactive, Hadoop ve HBase. Apache Kafka küme türü için ESP yalnızca en iyi çaba desteğiyle önizlemededir. ESP GA tarihi (1 Ekim 2018) öncesinde oluşturulan ESP kümeleri desteklenmez.

## <a name="enable-azure-ad-ds"></a>Azure AD-DS 'yi etkinleştirme

> [!NOTE]  
> Yalnızca kiracı yöneticileri Azure AD-DS 'yi etkinleştirme ayrıcalıklarına sahiptir. Küme depolaması Azure Data Lake Storage (ADLS) Gen1 veya Gen2 ise, yalnızca temel Kerberos kimlik doğrulamaları kullanarak kümeye erişmesi gereken kullanıcılar için Multi-Factor Authentication (MFA) seçeneğini devre dışı bırakmanız gerekir. Belirli kullanıcılar için MFA 'yı yalnızca HDInsight küme VNET IP aralığına erişirken devre dışı bırakmak için [Güvenilen IP 'leri](../../active-directory/authentication/howto-mfa-mfasettings.md#trusted-ips) veya [koşullu erişimi](../../active-directory/conditional-access/overview.md) kullanabilirsiniz. Koşullu erişim kullanıyorsanız lütfen HDInsight VNET üzerinde AD hizmeti uç noktasının etkinleştirildiğinden emin olun.
>
> Küme depolama alanı Azure Blob depolama (te) ise, MFA 'yı devre dışı bırakın.

Bir HDInsight kümesini ESP ile oluşturabilmeniz için AzureAD-DS ' i etkinleştirmek bir önkoşuldur. Daha fazla bilgi için bkz. [Azure Portal kullanarak Azure Active Directory Domain Services etkinleştirme](../../active-directory-domain-services/tutorial-create-instance.md). 

Azure AD-DS etkinleştirildiğinde, tüm kullanıcılar ve nesneler varsayılan olarak Azure Active Directory (AAD) ile Azure AD-DS arasında eşitlemeye başlar. Eşitleme işleminin uzunluğu, Azure AD 'deki nesne sayısına bağlıdır. Eşitleme, yüzlerce binlerce nesne için birkaç gün sürebilir. 

Azure AD-DS ile kullandığınız etki alanı adı, HDInsight ile çalışmak için 39 karakter veya daha az olmalıdır.

Yalnızca HDInsight kümelerine erişmesi gereken grupları eşitlemeyi tercih edebilirsiniz. Yalnızca belirli grupları eşitlemeye yönelik bu seçenek *kapsamlı eşitleme*olarak adlandırılır. Yönergeler için bkz. [Azure AD 'den yönetilen etki alanınızı kapsamlı eşitlemeyi yapılandırma](../../active-directory-domain-services/scoped-synchronization.md) .

Güvenli LDAP etkinleştirildiğinde, etki alanı adını sertifikaya konu adı ve konu diğer adı ' na koyun. Örneğin, etki alanı adınız *contoso100.onmicrosoft.com*ise, sertifikanın konu adı ve konu diğer adında tam adın bulunduğundan emin olun. Daha fazla bilgi için bkz. [Azure AD DS tarafından yönetilen etki alanı için GÜVENLI LDAP yapılandırma](../../active-directory-domain-services/tutorial-configure-ldaps.md). Aşağıda, otomatik olarak imzalanan bir sertifika oluşturma ve etki alanı adının (*contoso100.onmicrosoft.com*) hem konu adı hem de DnsName (konu alternatif adı) olarak sahip olduğu bir örnek verilmiştir:

```powershell
$lifetime=Get-Date
New-SelfSignedCertificate -Subject contoso100.onmicrosoft.com `
  -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
  -Type SSLServerAuthentication -DnsName *.contoso100.onmicrosoft.com, contoso100.onmicrosoft.com
```

## <a name="check-azure-ad-ds-health-status"></a>Azure AD-DS sistem durumunu denetle
**Yönet** kategorisinin altında **sistem** durumu ' nu seçerek Azure Active Directory Domain Services sistem durumunu görüntüleyin. Azure AD-DS durumunun yeşil (çalışıyor) ve eşitlemenin tamam olduğundan emin olun.

![Azure Active Directory Domain Services sistem durumu](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-health.png)

## <a name="create-and-authorize-a-managed-identity"></a>Yönetilen kimlik oluşturma ve yetkilendirme

Etki alanı Hizmetleri işlemlerini basitleştirmek ve güvenli hale getirmek için **Kullanıcı tarafından atanan yönetilen kimlik** kullanılır. HDInsight etki alanı Hizmetleri katılımcısı rolünü yönetilen kimliğe atadığınızda, etki alanı Hizmetleri işlemlerini okuyabilir, oluşturabilir, değiştirebilir ve silebilir. HDInsight Kurumsal Güvenlik Paketi için OU 'Lar ve hizmet sorumluları oluşturma gibi bazı etki alanı Hizmetleri işlemleri gereklidir. Yönetilen kimlikler herhangi bir abonelikte oluşturulabilir. Genel olarak yönetilen kimlikler hakkında daha fazla bilgi için bkz. [Azure kaynakları Için Yönetilen kimlikler](../../active-directory/managed-identities-azure-resources/overview.md). Yönetilen kimliklerin Azure HDInsight 'ta nasıl çalıştığı hakkında daha fazla bilgi için bkz. [Azure HDInsight 'Ta Yönetilen kimlikler](../hdinsight-managed-identities.md).

ESP kümelerini ayarlamak için, henüz bir tane yoksa, Kullanıcı tarafından atanan bir yönetilen kimlik oluşturun. Yönergeler için [Azure Portal kullanarak Kullanıcı tarafından atanan yönetilen kimlik için rol oluşturma, listeleme, silme veya atama](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) konusuna bakın. Daha sonra, Azure AD-DS erişim denetimindeki yönetilen kimliğe **HDInsight etki alanı Hizmetleri katılımcısı** rolünü atayın (Bu rol atamasını yapmak için AAD-DS yönetici ayrıcalıkları gerekir).

![Azure Active Directory Domain Services Access Control](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-configure-managed-identity.png)

**HDInsight etki alanı Hizmetleri katılımcısı** rolünü atamak, bu KIMLIğIN, AAD-DS etki alanındaki OU 'lar oluşturma, OU 'lar vb. gibi etki alanı Hizmetleri işlemlerini gerçekleştirmek için uygun (adına) erişimi olmasını sağlar.

Yönetilen kimlik oluşturulduktan ve doğru rol verildikten sonra, AAD-DS Yöneticisi bu yönetilen kimliği kimlerin kullanacağınızı ayarlayabilir. Yönetilen kimlik kullanıcıları ayarlamak için yönetici portalda yönetilen kimliği seçip **genel bakış**altında **Access Control (IAM)** öğesine tıklayın. Ardından, sağ tarafta, **yönetilen kimlik operatörü** rolünü HDInsight ESP kümeleri oluşturmak isteyen kullanıcılara veya gruplara atayın. Örneğin, AAD-DS Yöneticisi, aşağıdaki görüntüde gösterildiği gibi **sjmsi** tarafından yönetilen kimlik için bu rolü **Pazarlama ekibi** grubuna atayabilir. Bu, kuruluştaki doğru kişilerin, ESP kümeleri oluşturmak amacıyla bu yönetilen kimliği kullanmak için erişimine sahip olmasını sağlar.

![HDInsight Yönetilen kimlik Işleci rol ataması](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-managed-identity-operator-role-assignment.png)

## <a name="networking-considerations"></a>Ağ konusunda dikkat edilmesi gerekenler

> [!NOTE]  
> Azure AD-DS Azure Resource Manager tabanlı vNET 'te dağıtılmalıdır. Klasik sanal ağlar Azure AD-DS için desteklenmez. Daha fazla bilgi için bkz. [Azure Portal kullanarak Azure Active Directory Domain Services etkinleştirme](../../active-directory-domain-services/tutorial-create-instance-advanced.md#create-and-configure-the-virtual-network).

Azure AD-DS 'yi etkinleştirdikten sonra, AD sanal makinelerinde (VM 'Ler) bir yerel etki alanı ad hizmeti (DNS) sunucusu çalışır. Azure AD-DS Sanal ağınızı (VNET) bu özel DNS sunucularını kullanacak şekilde yapılandırın. Doğru IP adreslerini bulmak için, **Yönet** kategorisi altında **Özellikler** ' i seçin ve **sanal ağ ÜZERINDE IP adresi**altında listelenen IP adreslerine bakın.

![Yerel DNS sunucularının IP adreslerini bulma](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-dns1.png)

**Ayarlar** kategorisi altındaki **DNS sunucuları** ' nı seçerek, bu özel IP 'LERI kullanmak IÇIN Azure AD-DS VNET 'teki DNS sunucularının yapılandırmasını değiştirin. Ardından **özel**' in yanındaki radyo düğmesine tıklayın, aşağıdaki metin kutusuna Ilk IP adresini girin ve **Kaydet**' e tıklayın. Aynı adımları kullanarak ek IP adresleri ekleyin.

![VNET DNS yapılandırması güncelleştiriliyor](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-vnet-configuration.png)

Hem Azure AD-DS örneği hem de HDInsight kümesini aynı Azure sanal ağına yerleştirmek daha kolay. Farklı sanal ağlar kullanmayı planlıyorsanız, etki alanı denetleyicisinin HDI VM 'Leri tarafından görülebilmesi için bu sanal ağları eşdüzey yapmanız gerekir. Daha fazla bilgi için bkz. [sanal ağ eşlemesi](../../virtual-network/virtual-network-peering-overview.md). 

Sanal ağlar eşlendikten sonra, HDInsight VNET 'i özel bir DNS sunucusu kullanacak şekilde yapılandırın ve Azure AD-DS özel IP 'lerini DNS sunucusu adresleri olarak girin. Her iki sanal ağ da aynı DNS sunucularını kullandıklarında, özel etki alanı adınız doğru IP 'ye çözümlenir ve HDInsight 'tan erişilecektir. Örneğin, etki alanı adınız `contoso.com` ise, bu adımdan sonra, `ping contoso.com` doğru Azure AD-DS IP 'si olarak çözümlenmelidir.

![Eşlenen VNET için özel DNS sunucuları yapılandırma](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-peered-vnet-configuration.png)

HDInsight alt ağınızda Ağ güvenlik grupları (NSG) kuralları kullanıyorsanız, hem gelen hem de giden trafik için [gerekli IP 'lere](../hdinsight-management-ip-addresses.md) izin vermeniz gerekir.

Ağınızın doğru şekilde ayarlanmış olup olmadığını **test etmek Için** HDInsight VNET/subnet 'e bir Windows sanal makinesi ekleyin ve etki alanı adına (bir IP 'ye çözümlenmelidir) ve ardından Azure AD-DS etki alanına erişmek için **Ldp. exe** ' yi çalıştırın. Ardından, istemci ve sunucu arasında tüm gerekli RPC çağrılarının başarılı olduğunu **onaylamak için bu WINDOWS VM 'sini etki alanına katın** . Ayrıca, depolama hesabınıza veya kullanabileceğiniz herhangi bir dış VERITABANıNA (örneğin, dış Hive meta veri deposu veya Ranger DB) ağ erişimini onaylamak için **nslookup** ' ı da kullanabilirsiniz.
AAD 'ler bir NSG tarafından güvenli hale getirildiyse, tüm [gerekli bağlantı NOKTALARıNıN](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772723(v=ws.10)#communication-to-domain-controllers) AAD-DS alt ağ ağ güvenlik grubu kurallarında da listelendiğinden emin olmanız gerekir. Bu Windows VM 'ye katılan etki alanı başarılı olursa, sonraki adıma devam edebilir ve ESP kümeleri oluşturabilirsiniz.

## <a name="create-a-hdinsight-cluster-with-esp"></a>ESP ile HDInsight kümesi oluşturma

Önceki adımları doğru ayarladıktan sonra, bir sonraki adım, HDInsight kümesini ESP etkin olarak oluşturmaktır. Bir HDInsight kümesi oluşturduğunuzda, **güvenlik + ağ** sekmesinden kurumsal güvenlik paketi etkinleştirebilirsiniz. Dağıtım için bir Azure Resource Manager şablonu kullanmayı tercih ederseniz, Portal deneyimini bir kez daha kullanın ve daha sonra yeniden kullanmak üzere **gözden geçir + oluştur** sayfasında önceden doldurulmuş şablonu indirin.

> [!NOTE]  
> ESP küme adlarının ilk altı karakteri ortamınızda benzersiz olmalıdır. Örneğin, farklı VNET 'lerde birden çok ESP kümeniz varsa, küme adlarında ilk altı karakterin benzersiz olmasını sağlayan bir adlandırma Convension seçmeniz gerekir.

![Azure HDInsight Enterprise güvenlik paketi etki alanı doğrulaması](./media/apache-domain-joined-configure-using-azure-adds/azure-portal-cluster-security-networking-esp.png)

ESP 'yi etkinleştirdikten sonra, Azure AD-DS ile ilgili ortak yapılandırma hataları otomatik olarak algılanır ve onaylanır. Bu hataları düzelttikten sonra, bir sonraki adımla devam edebilirsiniz:

![Azure HDInsight Enterprise güvenlik paketi etki alanı doğrulaması başarısız oldu](./media/apache-domain-joined-configure-using-azure-adds/azure-portal-cluster-security-networking-esp-error.png)

ESP ile bir HDInsight kümesi oluşturduğunuzda, aşağıdaki parametreleri sağlamanız gerekir:

- **Küme Yöneticisi kullanıcısı**: EŞITLENMIŞ Azure AD-DS 'nizden kümeniz için bir yönetici seçin. Bu etki alanı hesabının Azure AD-DS 'de zaten eşitlenmiş ve kullanılabilir olması gerekir.

- **Küme erişim grupları**: kullanıcılarını eşitlemek istediğiniz ve kümeye erişebilen güvenlik GRUPLARı Azure AD-DS ' de kullanılabilir olmalıdır. Örneğin, HiveUsers grubu. Daha fazla bilgi için bkz. [Grup oluşturma ve Azure Active Directory üye ekleme](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

- **LDAPS URL 'si**: bir örnek `ldaps://contoso.com:636` ' dir.

Oluşturduğunuz yönetilen kimlik, yeni bir küme oluştururken Kullanıcı tarafından atanan yönetilen kimlik açılır listesinden seçilebilir.

![Azure HDInsight ESP Active Directory Domain Services yönetilen kimliği](./media/apache-domain-joined-configure-using-azure-adds/azure-portal-cluster-security-networking-identity.png).

## <a name="next-steps"></a>Sonraki adımlar

* Hive ilkelerini yapılandırmak ve Hive sorgularını çalıştırmak için bkz. [HDInsight kümeleri için Apache Hive ILKELERINI ESP Ile yapılandırma](apache-domain-joined-run-hive.md).
* ESP ile HDInsight kümelerine bağlanmak üzere SSH kullanmak için bkz. [Linux, Unix veya OS X 'Ten HDInsight 'Ta Linux tabanlı Apache Hadoop Ile SSH kullanma](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).
