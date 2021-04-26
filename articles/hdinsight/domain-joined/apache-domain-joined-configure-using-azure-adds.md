---
title: Azure Active Directory tümleştirme için kümeleri yapılandırma
titleSuffix: Azure HDInsight
description: Azure Active Directory Domain Services ve Kurumsal Güvenlik Paketi özelliğini kullanarak Active Directory ile tümleştirilmiş bir HDInsight kümesi ayarlamayı ve yapılandırmayı öğrenin.
ms.service: hdinsight
ms.topic: how-to
ms.custom: seodec18,seoapr2020, contperf-fy21q2
ms.date: 10/30/2020
ms.openlocfilehash: 6f478b97464cd47e9d0e04bfe83bd48a2b3bfe7c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104867109"
---
# <a name="configure-hdinsight-clusters-for-azure-active-directory-integration-with-enterprise-security-package"></a>Kurumsal Güvenlik Paketi ile Azure Active Directory tümleştirme için HDInsight kümelerini yapılandırma

Bu makalede, Azure Active Directory ile tümleştirilmiş bir HDInsight kümesi oluşturma ve yapılandırma sürecine bir Özet ve genel bakış sunulmaktadır. Bu tümleştirme, Kurumsal Güvenlik Paketi (ESP), Azure Active Directory Domain Services (Azure AD-DS) ve önceden mevcut şirket içi Active Directory adlı bir HDInsight özelliğini kullanır.

Azure 'da bir etki alanı ayarlamaya ve yapılandırmaya ve ardından Şirket içi kullanıcıları eşitlemeye yönelik ayrıntılı bir adım adım öğretici için bkz. [Azure HDInsight 'ta kurumsal güvenlik paketi kümeleri oluşturma ve yapılandırma](apache-domain-joined-create-configure-enterprise-security-cluster.md).

## <a name="background"></a>Arka Plan

Kurumsal Güvenlik Paketi (ESP), Azure HDInsight için Active Directory tümleştirme sağlar. Bu tümleştirme, etki alanı kullanıcılarının, HDInsight kümelerinde kimlik doğrulamak ve büyük veri işleri çalıştırmak için etki alanı kimlik bilgilerini kullanmasına olanak tanır.

> [!NOTE]  
> ESP, şu küme türleri için HDInsight 3,6 ve 4,0 ' de genel kullanıma sunulmuştur: Apache Spark, Interactive, Hadoop ve HBase. Apache Kafka küme türü için ESP yalnızca en iyi çaba desteğiyle önizlemededir. ESP GA tarihi (1 Ekim 2018) öncesinde oluşturulan ESP kümeleri desteklenmez.

## <a name="prerequisites"></a>Önkoşullar

Bir ESP etkin HDInsight kümesi oluşturabilmeniz için birkaç önkoşul vardır:

- Mevcut bir şirket içi Active Directory ve Azure Active Directory.
- Azure AD-DS 'yi etkinleştirin.
- Eşitlemenin tamamlandığından emin olmak için Azure AD-DS sistem durumunu kontrol edin.
- Yönetilen bir kimlik oluşturun ve yetkilendirme yapın.
- DNS ve ilgili sorunlar için ağ kurulumunu tamamen doldurun.

Bu öğelerin her biri aşağıda ayrıntılı olarak ele alınacaktır. Tüm bu adımları tamamlamaya yönelik bir anlatım için bkz. [Azure HDInsight 'ta kurumsal güvenlik paketi kümeleri oluşturma ve yapılandırma](apache-domain-joined-create-configure-enterprise-security-cluster.md).

### <a name="enable-azure-ad-ds"></a>Azure AD DS’yi etkinleştirme

Azure AD DS etkinleştirilmesi, ESP ile bir HDInsight kümesi oluşturabilmeniz için bir önkoşuldur. Daha fazla bilgi için bkz. [Azure Portal kullanarak Azure Active Directory Domain Services etkinleştirme](../../active-directory-domain-services/tutorial-create-instance.md).

Azure AD DS etkinleştirildiğinde, tüm kullanıcılar ve nesneler, varsayılan olarak Azure Active Directory (Azure AD) ile Azure AD DS arasında eşitlemeye başlar. Eşitleme işleminin uzunluğu, Azure AD 'deki nesne sayısına bağlıdır. Eşitleme, yüzlerce binlerce nesne için birkaç gün sürebilir.

Azure AD DS ile birlikte kullandığınız etki alanı adı, HDInsight ile çalışmak için 39 karakter veya daha az olmalıdır.

Yalnızca HDInsight kümelerine erişmesi gereken grupları eşitlemeyi tercih edebilirsiniz. Yalnızca belirli grupları eşitlemeye yönelik bu seçenek *kapsamlı eşitleme* olarak adlandırılır. Yönergeler için bkz. [Azure AD 'den yönetilen etki alanınızı kapsamlı eşitlemeyi yapılandırma](../../active-directory-domain-services/scoped-synchronization.md).

Güvenli LDAP 'yi etkinleştirirken etki alanı adını konu adına koyun. Ve sertifikadaki Konu diğer adı. Etki alanı adınız *contoso100.onmicrosoft.com* ise, sertifikanın konu adı ve konu diğer adında tam adın bulunduğundan emin olun. Daha fazla bilgi için bkz. [Azure AD DS yönetilen etki alanı için GÜVENLI LDAP yapılandırma](../../active-directory-domain-services/tutorial-configure-ldaps.md).

Aşağıdaki örnek, kendinden imzalı bir sertifika oluşturur. *Contoso100.onmicrosoft.com* etki alanı adı hem `Subject` (konu adı) hem de `DnsName` (konu alternatif adı).

```powershell
$lifetime=Get-Date
New-SelfSignedCertificate -Subject contoso100.onmicrosoft.com `
  -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
  -Type SSLServerAuthentication -DnsName *.contoso100.onmicrosoft.com, contoso100.onmicrosoft.com
```

> [!NOTE]  
> Yalnızca kiracı yöneticileri Azure AD DS etkinleştirme ayrıcalıklarına sahiptir. Küme depolama alanı Azure Data Lake Storage 1. veya Gen2 ise, yalnızca temel Kerberos kimlik doğrulamasını kullanarak kümeye erişmesi gereken kullanıcılar için Azure AD Multi-Factor Authentication devre dışı bırakmanız gerekir.
>
> Belirli kullanıcılar için Multi-Factor Authentication devre dışı bırakmak için [Güvenilen IP](../../active-directory/authentication/howto-mfa-mfasettings.md#trusted-ips) veya [koşullu erişimi](../../active-directory/conditional-access/overview.md) , *yalnızca* HDInsight kümesinin sanal ağı için IP aralığına erişirken kullanabilirsiniz. Koşullu erişim kullanıyorsanız, HDInsight sanal ağında Active Directory Hizmeti uç noktasının etkinleştirildiğinden emin olun.
>
> Küme depolama alanı Azure Blob depoise Multi-Factor Authentication devre dışı bırakın.

### <a name="check-azure-ad-ds-health-status"></a>Azure AD DS sistem durumunu denetle

**Yönet** kategorisinde **sistem** durumu ' nu seçerek Azure Active Directory Domain Services sistem durumunu görüntüleyin. Azure AD DS durumunun yeşil (çalışıyor) olduğundan ve eşitlemenin tamamlantığınızdan emin olun.

:::image type="content" source="./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-health.png" alt-text="Azure AD DS sistem durumu" border="true":::

### <a name="create-and-authorize-a-managed-identity"></a>Yönetilen kimlik oluşturma ve yetkilendirme

Güvenli etki alanı Hizmetleri işlemlerini basitleştirmek için *Kullanıcı tarafından atanan bir yönetilen kimlik* kullanın. **HDInsight etki alanı Hizmetleri katılımcısı** rolünü yönetilen kimliğe atadığınızda, etki alanı Hizmetleri işlemlerini okuyabilir, oluşturabilir, değiştirebilir ve silebilir.

Kurumsal Güvenlik Paketi, OU 'Lar ve hizmet sorumluları oluşturma gibi bazı etki alanı Hizmetleri işlemleri, HDInsight için de gereklidir. Herhangi bir abonelikte Yönetilen kimlikler oluşturabilirsiniz. Genel olarak yönetilen kimlikler hakkında daha fazla bilgi için bkz. [Azure kaynakları Için Yönetilen kimlikler](../../active-directory/managed-identities-azure-resources/overview.md). Yönetilen kimliklerin Azure HDInsight 'ta nasıl çalıştığı hakkında daha fazla bilgi için bkz. [Azure HDInsight 'Ta Yönetilen kimlikler](../hdinsight-managed-identities.md).

ESP kümelerini ayarlamak için, henüz bir tane yoksa, Kullanıcı tarafından atanan bir yönetilen kimlik oluşturun. Bkz [`Create, list, delete, or assign a role to a user-assigned managed identity by using the Azure portal`](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) ..

Ardından, **HDInsight etki alanı Hizmetleri katılımcısı** rolünü Azure AD DS **erişim denetimindeki** yönetilen kimliğe atayın. Bu rol atamasını yapmak için Azure AD DS yönetici ayrıcalıklarına sahip olmanız gerekir.

:::image type="content" source="./media/apache-domain-joined-configure-using-azure-adds/hdinsight-configure-managed-identity.png" alt-text="Azure Active Directory Domain Services Access Control" border="true":::

**HDInsight etki alanı Hizmetleri katılımcısı** rolünü atamak, bu kimliğin `on behalf of` Azure AD DS etki alanında etki alanı Hizmetleri işlemlerine uygun () erişimi olmasını sağlar. Bu işlemler OU 'Ları oluşturmayı ve silmeyi içerir.

Yönetilen kimliğe rol verildiğinde Azure AD DS Yöneticisi kendisini kimin kullandığını yönetir. İlk olarak, yönetici portalda yönetilen kimliği seçer. Daha sonra **genel bakış** altında **Access Control (IAM)** öğesini seçer. Yönetici, **yönetilen kimlik operatörü** rolünü, ESP kümeleri oluşturmak isteyen kullanıcılara veya gruplara atar.

Örneğin, Azure AD DS Yöneticisi, bu rolü **sjmsi** tarafından yönetilen kimlik Için **Pazarlama ekibi** grubuna atayabilirler. Aşağıdaki görüntüde bir örnek gösterilmektedir. Bu atama, kuruluştaki doğru kişilerin ESP kümeleri oluşturmak için yönetilen kimliği kullanmasını sağlar.

:::image type="content" source="./media/apache-domain-joined-configure-using-azure-adds/hdinsight-managed-identity-operator-role-assignment.png" alt-text="HDInsight Yönetilen kimlik Işleci rol ataması" border="true":::

### <a name="network-configuration"></a>Ağ yapılandırması

> [!NOTE]  
> Azure AD DS Azure Resource Manager tabanlı bir sanal ağda dağıtılmalıdır. Klasik sanal ağlar Azure AD DS için desteklenmez. Daha fazla bilgi için bkz. [Azure Portal kullanarak Azure Active Directory Domain Services etkinleştirme](../../active-directory-domain-services/tutorial-create-instance-advanced.md#create-and-configure-the-virtual-network).

Azure AD DS 'yi etkinleştirin. Ardından, yerel bir etki alanı adı sistemi (DNS) sunucusu Active Directory sanal makinelerde (VM) çalışır. Azure AD DS Sanal ağınızı bu özel DNS sunucularını kullanacak şekilde yapılandırın. Doğru IP adreslerini bulmak için, **Yönet** kategorisinde **Özellikler** ' i seçin ve **sanal ağdaki IP adresi** bölümüne bakın.

:::image type="content" source="./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-dns1.png" alt-text="Yerel DNS sunucularının IP adreslerini bulma" border="true":::

Azure AD DS sanal ağındaki DNS sunucularının yapılandırmasını değiştirin. Bu özel IP 'Leri kullanmak için **Ayarlar** kategorisinde **DNS sunucuları** ' nı seçin. Ardından **özel** seçeneğini belirleyin, metin kutusuna ilk IP adresini girin ve **Kaydet**' i seçin. Aynı adımları kullanarak daha fazla IP adresi ekleyin.

:::image type="content" source="./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-vnet-configuration.png" alt-text="Sanal ağ DNS yapılandırması güncelleştiriliyor" border="true":::

Aynı Azure sanal ağına hem Azure AD DS örneğini hem de HDInsight kümesini yerleştirmek daha kolay. Farklı sanal ağlar kullanmayı planlıyorsanız, etki alanı denetleyicisinin HDInsight VM 'lerine görebilmesi için bu sanal ağları eşdüzey yapmanız gerekir. Daha fazla bilgi için bkz. [sanal ağ eşlemesi](../../virtual-network/virtual-network-peering-overview.md).

Sanal ağlar eşlendikten sonra, HDInsight sanal ağını özel bir DNS sunucusu kullanacak şekilde yapılandırın. Ve Azure AD DS özel IP 'lerini DNS sunucusu adresleri olarak girin. Her iki sanal ağ da aynı DNS sunucularını kullandıklarında, özel etki alanı adınız doğru IP 'ye çözümlenir ve HDInsight 'tan erişilecektir. Örneğin, etki alanı adınız ise `contoso.com` , bu adımdan sonra, `ping contoso.com` doğru Azure AD DS IP 'ye çözümlenmelidir.

:::image type="content" source="./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-peered-vnet-configuration.png" alt-text="Eşlenmiş bir sanal ağ için özel DNS sunucuları yapılandırma" border="true":::

HDInsight alt ağınızda Ağ güvenlik grubu (NSG) kuralları kullanıyorsanız, hem gelen hem de giden trafik için [gerekli IP 'lere](../hdinsight-management-ip-addresses.md) izin vermeniz gerekir.

Ağ kurulumunuzu test etmek için HDInsight sanal ağına/alt ağına bir Windows sanal makinesi ekleyin ve etki alanı adına ping gönderin. (Bir IP 'ye çözümlenmelidir.) Azure AD DS etki alanına erişmek için **ldp.exe** çalıştırın. Ardından, istemci ve sunucu arasında tüm gerekli RPC çağrılarının başarılı olduğunu onaylamak için bu Windows VM 'sini etki alanına katın.

Depolama hesabınıza ağ erişimini onaylamak için **nslookup** 'ı kullanın. Ya da kullanabileceğiniz herhangi bir dış veritabanı (örneğin, dış Hive meta veri deposu veya Ranger DB). Bir NSG Azure AD DS ' i güvenlik altına alıyorsa, Azure AD DS alt ağının NSG kurallarında [gerekli bağlantı noktalarına](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772723(v=ws.10)#communication-to-domain-controllers) izin verildiğinden emin olun. Bu Windows VM 'ye katılan etki alanı başarılı olursa, sonraki adıma devam edebilir ve ESP kümeleri oluşturabilirsiniz.

## <a name="create-an-hdinsight-cluster-with-esp"></a>ESP ile HDInsight kümesi oluşturma

Önceki adımları doğru ayarladıktan sonra, bir sonraki adım, HDInsight kümesini ESP etkin olarak oluşturmaktır. Bir HDInsight kümesi oluşturduğunuzda, **güvenlik + ağ** sekmesinde kurumsal güvenlik paketi etkinleştirebilirsiniz. Dağıtım için bir Azure Resource Manager şablonu için Portal deneyimini bir kez kullanın. Ardından daha sonra yeniden kullanmak üzere **gözden geçir + oluştur** sayfasında önceden doldurulmuş şablonu indirin.

Ayrıca, küme oluşturma sırasında [HDıNSIGHT kimlik Aracısı](identity-broker.md) özelliğini de etkinleştirebilirsiniz. KIMLIK Aracısı özelliği, Multi-Factor Authentication kullanarak ve Azure AD DS parola karmaları gerekmeden gerekli Kerberos biletlerini alarak, ambarı 'nda oturum açmanızı sağlar.

> [!NOTE]  
> ESP küme adlarının ilk altı karakteri ortamınızda benzersiz olmalıdır. Örneğin, farklı sanal ağlarda birden çok ESP kümeniz varsa, küme adlarında ilk altı karakterin benzersiz olmasını sağlayan bir adlandırma kuralı seçin.

:::image type="content" source="./media/apache-domain-joined-configure-using-azure-adds/azure-portal-cluster-security-networking-esp.png" alt-text="Azure HDInsight için etki alanı doğrulama Kurumsal Güvenlik Paketi" border="true":::

ESP 'yi etkinleştirdikten sonra, Azure AD DS ile ilgili ortak yapılandırma hataları otomatik olarak algılanır ve onaylanır. Bu hataları düzelttikten sonra, bir sonraki adımla devam edebilirsiniz.

:::image type="content" source="./media/apache-domain-joined-configure-using-azure-adds/azure-portal-cluster-security-networking-esp-error.png" alt-text="Azure HDInsight Kurumsal Güvenlik Paketi etki alanı doğrulaması başarısız oldu" border="true":::

ESP ile bir HDInsight kümesi oluşturduğunuzda, aşağıdaki parametreleri sağlamanız gerekir:

* **Küme Yöneticisi kullanıcısı**: eşitlenmiş Azure AD DS örneğinden kümeniz için bir yönetici seçin. Bu etki alanı hesabı zaten eşitlenmiş ve Azure AD DS kullanılabilir olmalıdır.

* **Küme erişim grupları**: kullanıcılarını eşitlemek istediğiniz ve kümeye erişebilen güvenlik grupları Azure AD DS 'da kullanılabilir olmalıdır. HiveUsers grubu bir örnektir. Daha fazla bilgi için bkz. [Grup oluşturma ve Azure Active Directory üye ekleme](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

* **LDAPS URL 'si**: bir örnektir `ldaps://contoso.com:636` .

Oluşturduğunuz yönetilen kimlik, yeni bir küme oluştururken, **Kullanıcı tarafından atanan yönetilen kimlik** listesinden seçilebilir.

:::image type="content" source="./media/apache-domain-joined-configure-using-azure-adds/azure-portal-cluster-security-networking-identity.png" alt-text="Azure HDInsight tarafından yönetilen kimlik ACTIVE DIRECTORY DOMAIN SERVICES ESP" border="true":::.

## <a name="next-steps"></a>Sonraki adımlar

* Hive ilkelerini yapılandırmak ve Hive sorgularını çalıştırmak için bkz. [HDInsight kümeleri için Apache Hive ILKELERINI ESP Ile yapılandırma](apache-domain-joined-run-hive.md).
* ESP ile HDInsight kümelerine bağlanmak üzere SSH kullanmak için bkz. [Linux, Unix veya OS X 'Ten HDInsight 'Ta Linux tabanlı Apache Hadoop Ile SSH kullanma](../hdinsight-hadoop-linux-use-ssh-unix.md#authentication-domain-joined-hdinsight).
