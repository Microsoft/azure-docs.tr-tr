---
title: Azure Databricks için bölgesel olağanüstü durum kurtarma
description: Bu makalede Azure Databricks ' de olağanüstü durum kurtarma yapmaya yönelik bir yaklaşım açıklanmaktadır.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 03/13/2019
ms.openlocfilehash: b9a5dbd8e24659493bbbefd50c3e234dca3dbdd9
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/16/2019
ms.locfileid: "74129331"
---
# <a name="regional-disaster-recovery-for-azure-databricks-clusters"></a>Azure Databricks kümeleri için bölgesel olağanüstü durum kurtarma

Bu makalede, Azure Databricks kümeleri ve bu tasarımı gerçekleştirmek için gereken adımlar için yararlı bir olağanüstü durum kurtarma mimarisi açıklanmaktadır.

## <a name="azure-databricks-architecture"></a>Azure Databricks mimarisi

Yüksek düzeyde, Azure portal bir Azure Databricks çalışma alanı oluşturduğunuzda, seçili Azure bölgesinde (örneğin, Batı ABD), aboneliğinizde bir Azure kaynağı olarak bir [yönetilen gereç](../managed-applications/overview.md) dağıtılır. Bu gereç, aboneliğinizde bulunan bir [ağ güvenlik grubu](../virtual-network/manage-network-security-group.md) ve bir Azure depolama hesabı Ile bir [Azure sanal ağında](../virtual-network/virtual-networks-overview.md) dağıtılır. Sanal ağ, Databricks çalışma alanına çevre düzeyi güvenliği sağlar ve ağ güvenlik grubu üzerinden korunur. Çalışma alanı içinde, çalışan ve sürücü VM 'si türü ve Databricks çalışma zamanı sürümünü sağlayarak Databricks kümeleri oluşturabilirsiniz. Kalıcı veriler, Azure Blob depolama veya Azure Data Lake Storage olabilen depolama hesabınızda kullanılabilir. Küme oluşturulduktan sonra, işleri Belirli bir kümeye ekleyerek Not defterleri, REST API 'Leri, ODBC/JDBC uç noktaları aracılığıyla çalıştırabilirsiniz.

Databricks denetim düzlemi, Databricks çalışma alanı ortamını yönetir ve izler. Küme oluşturma gibi herhangi bir yönetim işlemi denetim düzleden başlatılacak. Zamanlanan işler gibi tüm meta veriler, hata toleransı için coğrafi çoğaltma ile bir Azure veritabanında depolanır.

![Databricks mimarisi](media/howto-regional-disaster-recovery/databricks-architecture.png)

Bu mimarinin avantajlarından biri, kullanıcıların hesabındaki herhangi bir depolama kaynağına Azure Databricks bağlanamadır. Önemli bir avantaj, hem işlem (Azure Databricks) hem de depolamanın birbirinden bağımsız olarak ölçeklendirilebileceğiyle ilgili bir avantajdır.

## <a name="how-to-create-a-regional-disaster-recovery-topology"></a>Bölgesel olağanüstü durum kurtarma topolojisi oluşturma

Yukarıdaki mimari açıklamasında fark etmeksizin, Azure Databricks ile büyük bir veri işlem hattı için kullanılan birçok bileşen vardır: Azure depolama, Azure veritabanı ve diğer veri kaynakları. Azure Databricks, büyük veri ardışık düzeni için *işlem* olur. Daha *kısa* bir süre içinde, verileriniz Azure Storage 'da kullanılmaya devam ederken, *işlem* (Azure Databricks kümesi), ihtiyacınız olmadığında işlem için ödeme yapmak zorunda kalmayacak şekilde sonlandırılabilir. İşlerin yüksek gecikme süresi yaşamaması için *işlem* (Azure Databricks) ve depolama kaynaklarının aynı bölgede olması gerekir.  

Kendi bölgesel olağanüstü durum kurtarma topolojinizi oluşturmak için aşağıdaki gereksinimleri izleyin:

   1. Ayrı Azure bölgelerinde birden çok Azure Databricks çalışma alanı sağlayın. Örneğin, Doğu ABD2 içinde birincil Azure Databricks çalışma alanını oluşturun. İkincil olağanüstü durum kurtarma Azure Databricks çalışma alanını Batı ABD gibi ayrı bir bölgede oluşturun.

   2. [Coğrafi olarak yedekli depolama](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage)kullanın. Azure Databricks ilişkili veriler, Azure depolama 'da varsayılan olarak depolanır. Databricks işlerinin sonuçları da Azure Blob depolama alanında depolanır, böylece işlenen veriler dayanıklı olur ve küme sonlandırıldıktan sonra yüksek oranda kullanılabilir kalır. Depolama ve Databricks kümesi birlikte bulunduğundan, birincil bölge artık erişilebilir değilse, verilerin ikincil bölgede erişilebilir olması için coğrafi olarak yedekli depolamayı kullanmanız gerekir.

   3. İkincil bölge oluşturulduktan sonra, kullanıcılar, Kullanıcı klasörleri, Not defterleri, küme yapılandırması, iş yapılandırması, kitaplıklar, depolama, init betikleri ve erişim denetimini yeniden yapılandırmanız gerekir. Ek ayrıntılar aşağıdaki bölümde açıklanmıştır.

## <a name="detailed-migration-steps"></a>Ayrıntılı geçiş adımları

1. **Bilgisayarınızda Databricks komut satırı arabirimini ayarlama**

   Bu makalede, Azure Databricks REST API üzerinde kolay bir Kullanıcı sarmalayıcısı olduğundan, otomatik adımların çoğu için komut satırı arabirimini kullanan bir dizi kod örneği gösterilmektedir.

   Herhangi bir geçiş adımını gerçekleştirmeden önce, masaüstü bilgisayarınıza veya işi planladığınız bir sanal makineye databricks-CLI ' yı yükleyebilirsiniz. Daha fazla bilgi için bkz. [Databricks CLI 'Yi Install](/azure/databricks/dev-tools/databricks-cli)

   ```bash
   pip install databricks-cli
   ```

   > [!NOTE]
   > Bu makalede sunulan Python betikleri, Python 2.7 + < 3. x ile birlikte çalışmak için beklenmektedir.

2. **İki profil yapılandırın.**

   Birincil çalışma alanı için bir tane ve ikincil çalışma alanı için bir tane yapılandırın:

   ```bash
   databricks configure --profile primary
   databricks configure --profile secondary
   ```

   Bu makaledeki kod blokları, ilgili çalışma alanı komutunu kullanarak sonraki her adımdaki profiller arasında geçiş yapar. Oluşturduğunuz profillerin adlarının her kod bloğu için yerine geldiğinden emin olun.

   ```python
   EXPORT_PROFILE = "primary"
   IMPORT_PROFILE = "secondary"
   ```

   Gerekirse komut satırına el ile geçiş yapabilirsiniz:

   ```bash
   databricks workspace ls --profile primary
   databricks workspace ls --profile secondary
   ```

3. **Azure Active Directory kullanıcıları geçirme**

   Aynı Azure Active Directory kullanıcılarını birincil çalışma alanında var olan ikincil çalışma alanına el ile ekleyin.

4. **Kullanıcı klasörlerini ve not defterlerini geçirme**

   Kullanıcı başına iç içe geçmiş klasör yapısını ve not defterlerini içeren korumalı Kullanıcı ortamlarını geçirmek için aşağıdaki python kodunu kullanın.

   > [!NOTE]
   > Bu adımda, temeldeki API tarafından desteklenmeyen kitaplıklar bu şekilde kopyalanmaz.

   Aşağıdaki Python betiğini kopyalayıp bir dosyaya kaydedin ve Databricks komut satırımsonra çalıştırın. Örneğin, `python scriptname.py`.

   ```python
   from subprocess import call, check_output

   EXPORT_PROFILE = "primary"
   IMPORT_PROFILE = "secondary"

   # Get a list of all users
   user_list_out = check_output(["databricks", "workspace", "ls", "/Users", "--profile", EXPORT_PROFILE])
   user_list = user_list_out.splitlines()

   # Export sandboxed environment (folders, notebooks) for each user and import into new workspace.
   # Libraries are not included with these APIs / commands.

   for user in user_list:
     print "Trying to migrate workspace for user " + user

     call("mkdir -p " + user, shell=True)
     export_exit_status = call("databricks workspace export_dir /Users/" + user + " ./" + user + " --profile " + EXPORT_PROFILE, shell=True)

     if export_exit_status==0:
       print "Export Success"
       import_exit_status = call("databricks workspace import_dir ./" + user + " /Users/" + user + " --profile " + IMPORT_PROFILE, shell=True)
       if import_exit_status==0:
         print "Import Success"
       else:
         print "Import Failure"
     else:
       print "Export Failure"

   print "All done"
   ```

5. **Küme yapılandırmasını geçirme**

   Not defterleri geçirildikten sonra, küme yapılandırmalarının isteğe bağlı olarak yeni çalışma alanına geçişini sağlayabilirsiniz. Her şey yerine seçmeli küme yapılandırma geçişi yapmak istemediğiniz müddetçe, databricks-CLI kullanan neredeyse tamamen otomatikleştirilmiş bir adımdır.

   > [!NOTE]
   > Ne yazık ki, küme yapılandırma uç noktası oluşturma işlemi yapılmaz ve bu komut dosyası her bir kümeyi hemen oluşturmaya çalışır. Aboneliğinizde yeterli kullanılabilir çekirdek yoksa, küme oluşturma başarısız olabilir. Yapılandırma başarıyla aktarıldığı sürece hata yoksayılabilir.

   Aşağıdaki komut dosyası, daha sonra iş geçişi için kullanılabilecek (var olan kümeleri kullanacak şekilde yapılandırılmış işler için) eskileri yeni küme kimliklerine bir eşleme yazdırır.

   Aşağıdaki Python betiğini kopyalayıp bir dosyaya kaydedin ve Databricks komut satırımsonra çalıştırın. Örneğin, `python scriptname.py`.

   ```python
   from subprocess import call, check_output
   import json, os

   EXPORT_PROFILE = "primary"
   IMPORT_PROFILE = "secondary"

   # Get all clusters info from old workspace
   clusters_out = check_output(["databricks", "clusters", "list", "--profile", EXPORT_PROFILE])
   clusters_info_list = clusters_out.splitlines()

   # Create a list of all cluster ids
   clusters_list = []
   ##for cluster_info in clusters_info_list: clusters_list.append(cluster_info.split(None, 1)[0])

   for cluster_info in clusters_info_list: 
      if cluster_info != '':
         clusters_list.append(cluster_info.split(None, 1)[0])

   # Optionally filter cluster ids out manually, so as to create only required ones in new workspace

   # Create a list of mandatory / optional create request elements
   cluster_req_elems = ["num_workers","autoscale","cluster_name","spark_version","spark_conf","node_type_id","driver_node_type_id","custom_tags","cluster_log_conf","spark_env_vars","autotermination_minutes","enable_elastic_disk"]

   print(str(len(clusters_list)) + " clusters found in the primary site" )

   print ("---------------------------------------------------------")
   # Try creating all / selected clusters in new workspace with same config as in old one.
   cluster_old_new_mappings = {}
   i = 0
   for cluster in clusters_list:
      i += 1
      print("Checking cluster " + str(i) + "/" + str(len(clusters_list)) + " : " + cluster)
      cluster_get_out = check_output(["databricks", "clusters", "get", "--cluster-id", cluster, "--profile", EXPORT_PROFILE])
      print ("Got cluster config from old workspace")

       # Remove extra content from the config, as we need to build create request with allowed elements only
      cluster_req_json = json.loads(cluster_get_out)
      cluster_json_keys = cluster_req_json.keys()

      #Don't migrate Job clusters
      if cluster_req_json['cluster_source'] == u'JOB' : 
         print ("Skipping this cluster as it is a Job cluster : " + cluster_req_json['cluster_id'] )
         print ("---------------------------------------------------------")
         continue

      for key in cluster_json_keys:
         if key not in cluster_req_elems:
            cluster_req_json.pop(key, None)

      # Create the cluster, and store the mapping from old to new cluster ids

      #Create a temp file to store the current cluster info as JSON
      strCurrentClusterFile = "tmp_cluster_info.json" 

      #delete the temp file if exists
      if os.path.exists(strCurrentClusterFile) : 
         os.remove(strCurrentClusterFile)

      fClusterJSONtmp = open(strCurrentClusterFile,"w+")
      fClusterJSONtmp.write(json.dumps(cluster_req_json))
      fClusterJSONtmp.close()

      #cluster_create_out = check_output(["databricks", "clusters", "create", "--json", json.dumps(cluster_req_json), "--profile", IMPORT_PROFILE])
      cluster_create_out = check_output(["databricks", "clusters", "create", "--json-file", strCurrentClusterFile , "--profile", IMPORT_PROFILE])
      cluster_create_out_json = json.loads(cluster_create_out)
      cluster_old_new_mappings[cluster] = cluster_create_out_json['cluster_id']

      print ("Cluster create request sent to secondary site workspace successfully")
      print ("---------------------------------------------------------")

      #delete the temp file if exists
      if os.path.exists(strCurrentClusterFile) : 
         os.remove(strCurrentClusterFile)

   print ("Cluster mappings: " + json.dumps(cluster_old_new_mappings))
   print ("All done")
   print ("P.S. : Please note that all the new clusters in your secondary site are being started now!")
   print ("       If you won't use those new clusters at the moment, please don't forget terminating your new clusters to avoid charges")
   ```

6. **İş yapılandırmasını geçirme**

   Önceki adımda küme yapılandırmalarının geçişini yaptıysanız, iş yapılandırmalarının yeni çalışma alanına geçirilmesini tercih edebilirsiniz. Tüm işler için yapmak yerine seçmeli iş yapılandırma geçişini yapmak istemediğiniz müddetçe, databricks-CLI kullanılarak tamamen otomatikleştirilmiş bir adımdır.

   > [!NOTE]
   > Zamanlanan bir iş için yapılandırma, "zamanlama" bilgilerini de içerir. bu sayede, varsayılan olarak, her bir yapılandırılmış zamanlamaya göre, geçirilir almaz çalışmaya başlar. Bu nedenle, aşağıdaki kod bloğu geçiş sırasında tüm zamanlama bilgilerini kaldırır (eski ve yeni çalışma alanları genelinde yinelenen çalıştırmaları önlemek için). Cutover için hazırsanız bu işlerin zamanlamalarını yapılandırın.

   İş yapılandırması, yeni veya var olan bir küme için ayarları gerektirir. Mevcut küme kullanılıyorsa, aşağıdaki komut dosyası/Code, eski küme KIMLIĞINI yeni küme KIMLIĞIYLE değiştirmeye çalışır.

   Aşağıdaki Python betiğini kopyalayın ve bir dosyaya kaydedin. `old_cluster_id` ve `new_cluster_id`değerini, önceki adımda yapılan küme geçişinin çıktısı ile değiştirin. Bunu databricks-CLI komut satırında çalıştırın, örneğin `python scriptname.py`.

   ```python
   from subprocess import call, check_output
   import json

   EXPORT_PROFILE = "primary"
   IMPORT_PROFILE = "secondary"

   # Please replace the old to new cluster id mappings from cluster migration output
   cluster_old_new_mappings = {"old_cluster_id": "new_cluster_id"}

   # Get all jobs info from old workspace
   try:
     jobs_out = check_output(["databricks", "jobs", "list", "--profile", EXPORT_PROFILE])
     jobs_info_list = jobs_out.splitlines()
   except:
     print "No jobs to migrate"
     sys.exit(0)

   # Create a list of all job ids
   jobs_list = []
   for jobs_info in jobs_info_list:
     jobs_list.append(jobs_info.split(None, 1)[0])

   # Optionally filter job ids out manually, so as to create only required ones in new workspace

   # Create each job in the new workspace based on corresponding settings in the old workspace

   for job in jobs_list:
     print "Trying to migrate " + job

     job_get_out = check_output(["databricks", "jobs", "get", "--job-id", job, "--profile", EXPORT_PROFILE])
     print "Got job config from old workspace"

     job_req_json = json.loads(job_get_out)  
     job_req_settings_json = job_req_json['settings']

     # Remove schedule information so job doesn't start before proper cutover
     job_req_settings_json.pop('schedule', None)

     # Replace old cluster id with new cluster id, if job configured to run against an existing cluster
     if 'existing_cluster_id' in job_req_settings_json:
       if job_req_settings_json['existing_cluster_id'] in cluster_old_new_mappings:
         job_req_settings_json['existing_cluster_id'] = cluster_old_new_mappings[job_req_settings_json['existing_cluster_id']]
       else:
         print "Mapping not available for old cluster id " + job_req_settings_json['existing_cluster_id']
         continue

     call(["databricks", "jobs", "create", "--json", json.dumps(job_req_settings_json), "--profile", IMPORT_PROFILE])
     print "Sent job create request to new workspace successfully"

   print "All done"
   ```

7. **Kitaplıkları geçirme**

   Şu anda kitaplıkları bir çalışma alanından diğerine geçirmeye yönelik basit bir yol yoktur. Bunun yerine, bu kitaplıkları yeni çalışma alanına el ile yeniden yükleyin. Çalışma alanına ve [KITAPLıKLARA CLI](https://github.com/databricks/databricks-cli#libraries-cli)'ye özel kitaplıkları yüklemek Için [dBFS CLI](https://github.com/databricks/databricks-cli#dbfs-cli-examples) 'nın birleşimini kullanarak otomatikleştirebilmek mümkündür.

8. **Azure Blob depolamayı geçirme ve Azure Data Lake Storage bağlama**

   Tüm [Azure Blob depolama](/azure/databricks/data/data-sources/azure/azure-storage) ve [Azure Data Lake Storage (Gen 2)](/azure/databricks/data/data-sources/azure/azure-datalake-gen2) bağlama noktalarını Not defteri tabanlı bir çözüm kullanarak el ile yeniden bağlayın. Depolama kaynakları birincil çalışma alanına bağlanmış ve ikincil çalışma alanında tekrarlanmalıdır. Bağlama için dış API yok.

9. **Küme başlangıç betiklerini geçir**

   Tüm küme başlatma betikleri, [dBFS CLI](https://github.com/databricks/databricks-cli#dbfs-cli-examples)kullanılarak eskileri yeni çalışma alanına geçirilebilir. İlk olarak, gerekli betikleri `dbfs:/dat abricks/init/..` yerel masaüstünüze veya sanal makinenize kopyalayın. Sonra, bu betikleri aynı yoldaki yeni çalışma alanına kopyalayın.

   ```bash
   // Primary to local
   dbfs cp -r dbfs:/databricks/init ./old-ws-init-scripts --profile primary

   // Local to Secondary workspace
   dbfs cp -r old-ws-init-scripts dbfs:/databricks/init --profile secondary
   ```

10. **Erişim denetimini el ile yeniden yapılandırın ve yeniden uygulayın.**

    Mevcut birincil çalışma alanınız Premium katmanını (SKU) kullanacak şekilde yapılandırıldıysa, büyük olasılıkla [Access Control özelliğini](/azure/databricks/administration-guide/access-control/index)de kullanıyor olabilirsiniz.

    Access Control özelliğini kullanıyorsanız, erişim denetimini kaynaklara (Not defterleri, kümeler, Işler, tablolar) el ile yeniden uygulayın.

## <a name="disaster-recovery-for-your-azure-ecosystem"></a>Azure Ekosisteminiz için olağanüstü durum kurtarma

Diğer Azure hizmetlerini kullanıyorsanız, bu hizmetler için olağanüstü durum kurtarma en iyi uygulamalarını da uyguladığınızdan emin olun. Örneğin, bir dış Hive meta veri deposu örneği kullanmayı seçerseniz, [azure SQL Server](../sql-database/sql-database-disaster-recovery.md), [Azure HDInsight](../hdinsight/hdinsight-high-availability-linux.md)ve/veya [MySQL için Azure veritabanı](../mysql/concepts-business-continuity.md)için olağanüstü durum kurtarmayı göz önünde bulundurmanız gerekir. Olağanüstü durum kurtarma hakkında genel bilgi için bkz. [Azure uygulamaları Için olağanüstü durum kurtarma](https://docs.microsoft.com/azure/architecture/resiliency/disaster-recovery-azure-applications).

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla bilgi için bkz. [Azure Databricks belgeleri](index.yml).