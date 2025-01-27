---
title: Gerenciar recursos para o cluster Apache Spark no Azure HDInsight
description: Saiba como usar o recurso gerenciar recursos para clusters Spark no Azure HDInsight para melhorar o desempenho.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/23/2018
ms.author: hrasheed
ms.openlocfilehash: ac0109ff8c5dd7f6013acefbe5ee08a13494cb77
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2019
ms.locfileid: "71001711"
---
# <a name="manage-resources-for-apache-spark-cluster-on-azure-hdinsight"></a>Gerenciar recursos para o cluster Apache Spark no Azure HDInsight 

Saiba como acessar as interfaces como a interface do usuário do [Apache Ambari](https://ambari.apache.org/) , a interface do usuário do [amApache Hadoop yarn](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) e o servidor de histórico do Spark associado ao cluster [Apache Spark](https://spark.apache.org/) e como ajustar a configuração do cluster para obter um desempenho ideal.

**Pré-requisitos:**

* Um cluster Apache Spark no HDInsight. Para obter instruções, consulte o artigo sobre como [Criar clusters do Apache Spark no Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="open-the-ambari-web-ui"></a>Abra a interface do usuário da Web do amAmbari

O Apache Ambari é usado para monitorar o cluster e fazer alterações de configuração. Para obter mais informações, consulte [gerenciar clusters de Apache Hadoop no HDInsight usando o portal do Azure](../hdinsight-administer-use-portal-linux.md#open-the-apache-ambari-web-ui)

## <a name="open-the-spark-history-server"></a>Abrir o servidor de histórico do Spark

O servidor de histórico do Spark é a interface do usuário da Web para aplicativos Spark concluídos e em execução. É uma extensão da interface do usuário da Web do Spark.

**Para abrir a interface do usuário da Web do servidor de histórico do Spark**

1. No [portal do Azure](https://portal.azure.com/), abra o cluster Spark. Para obter mais informações, consulte [listar e mostrar clusters](../hdinsight-administer-use-portal-linux.md#showClusters).
2. Em **links rápidos**, clique em **painel do cluster**e clique em servidor de **histórico do Spark**

    ![Servidor de histórico do Spark](./media/apache-spark-resource-manager/launch-history-server.png "Servidor de Histórico do Spark")

    Quando solicitado, insira as credenciais de administrador para o cluster Spark. Você também pode abrir o servidor de histórico do Spark navegando até a seguinte URL:

    ```
    https://<ClusterName>.azurehdinsight.net/sparkhistory
    ```

    Substitua `<ClusterName>` pelo nome do cluster Spark.

A interface do usuário da Web do servidor de histórico do Spark é semelhante a:

![Servidor de histórico do HDInsight Spark](./media/apache-spark-resource-manager/hdinsight-spark-history-server.png)

## <a name="open-the-yarn-ui"></a>Abrir a interface do usuário do amYarn

É possível usar a interface do usuário do YARN para monitorar aplicativos que estão em execução no momento no cluster Spark.

1. No [portal do Azure](https://portal.azure.com/), abra o cluster Spark. Para obter mais informações, consulte [listar e mostrar clusters](../hdinsight-administer-use-portal-linux.md#showClusters).
2. Em **links rápidos**, clique em **painel do cluster**e em **yarn**.

    ![Iniciar Interface do usuário do YARN](./media/apache-spark-resource-manager/hdi-launch-apache-yarn.png)

   > [!TIP]  
   > Como alternativa, você também pode iniciar a interface do usuário do amYARN da interface do usuário do amAmbari. Para iniciar a interface do usuário do amAmbari, clique em **painel do cluster**e clique em **painel do cluster HDInsight**. Na interface do usuário do amAmbari, clique em **yarn**, clique em **links rápidos**, clique no Gerenciador de recursos ativo e clique em **interface do usuário do Gerenciador de recursos**.

## <a name="optimize-clusters-for-spark-applications"></a>Otimizar clusters para aplicativos Spark

Os três parâmetros principais que podem ser usados para a configuração do Spark dependendo dos requisitos do aplicativo são `spark.executor.instances`, `spark.executor.cores` e `spark.executor.memory`. Um executor é um processo iniciado para um aplicativo Spark. Ele é executado no nó de trabalho e é responsável por realizar as tarefas do aplicativo. O número padrão de executores e os tamanhos de executor para cada cluster é calculado com base no número de nós de trabalho e no tamanho do nó de trabalho. Essas informações são armazenadas em `spark-defaults.conf` nos nós de cabeçalho do cluster.

Os três parâmetros de configuração podem ser configurados no nível do cluster (para todos os aplicativos que são executados no cluster) ou também podem ser especificados para cada aplicativo individual.

### <a name="change-the-parameters-using-ambari-ui"></a>Alterar os parâmetros usando a interface de usuário do Ambari
1. Na interface de usuário do Ambari, clique em **Spark**, em **Configurações** e expanda **spark-defaults personalizados**.

    ![Definir parâmetros usando Ambari personalizado](./media/apache-spark-resource-manager/set-parameters-using-ambari.png "Definir parâmetros usando Ambari personalizado")
2. Os valores padrão são bons para que quatro aplicativos Spark sejam executados simultaneamente no cluster. Você pode alterar esses valores da interface do usuário, conforme mostrado na seguinte captura de tela:

    ![Definir parâmetros usando Ambari](./media/apache-spark-resource-manager/set-executor-parameters.png "Definir parâmetros usando Ambari")

3. Clique em **Salvar** para salvar as alterações na configuração. Na parte superior da página, será solicitado que você reinicie todos os serviços afetados. Clique em **Reiniciar**.

    ![Reinicializar serviços](./media/apache-spark-resource-manager/apache-ambari-restart-services.png)

### <a name="change-the-parameters-for-an-application-running-in-jupyter-notebook"></a>Alterar os parâmetros de um aplicativo em execução no Jupyter Notebook
Para aplicativos em execução no bloco de notas Jupyter, você pode usar a mágica `%%configure` para fazer as alterações na configuração. Idealmente, você deve fazer essas alterações no início do aplicativo antes de executar sua primeira célula de código. Isso garante que a configuração seja aplicada à sessão Livy quando for criada. Se quiser alterar a configuração em uma fase posterior no aplicativo, você deverá usar o parâmetro `-f` . No entanto, ao fazer isso, todo o progresso no aplicativo é perdido.

O trecho a seguir mostra como alterar a configuração de um aplicativo em execução no Jupyter.

    %%configure
    {"executorMemory": "3072M", "executorCores": 4, "numExecutors":10}

Os parâmetros de configuração devem ser passados como uma cadeia de caracteres JSON e devem estar na próxima linha após a mágica, conforme mostrado na coluna de exemplo.

### <a name="change-the-parameters-for-an-application-submitted-using-spark-submit"></a>Alterar os parâmetros de um aplicativo enviado usando Spark-Submit
O comando a seguir é um exemplo de como alterar os parâmetros de configuração para um aplicativo em lote que é enviado usando `spark-submit`.

    spark-submit --class <the application class to execute> --executor-memory 3072M --executor-cores 4 –-num-executors 10 <location of application jar file> <application parameters>

### <a name="change-the-parameters-for-an-application-submitted-using-curl"></a>Alterar os parâmetros de um aplicativo enviado usando a ondulação
O comando a seguir é um exemplo de como alterar os parâmetros de configuração para um aplicativo do lote que é enviado usando a ondulação.

    curl -k -v -H 'Content-Type: application/json' -X POST -d '{"file":"<location of application jar file>", "className":"<the application class to execute>", "args":[<application parameters>], "numExecutors":10, "executorMemory":"2G", "executorCores":5' localhost:8998/batches

### <a name="change-these-parameters-on-a-spark-thrift-server"></a>Alterar esses parâmetros em um servidor Spark thrift
O servidor Spark Thrift fornece acesso JDBC/ODBC a um cluster Spark e é usado para atender a consultas SQL do Spark. Ferramentas como Power BI, tableau etc. Use o protocolo ODBC para se comunicar com o servidor Spark Thrift para executar consultas SQL do Spark como um aplicativo Spark. Quando um cluster Spark é criado, duas instâncias do servidor Spark Thrift são iniciadas, uma em cada nó de cabeçalho. Cada servidor Spark Thrift é visível como um aplicativo Spark na interface do usuário do amYARN.

O Servidor Thrift Spark usa a alocação dinâmica de executor e, portanto, `spark.executor.instances` não é usado. Em vez disso, o servidor Thrift do Spark usa `spark.dynamicAllocation.minExecutors` e `spark.dynamicAllocation.maxExecutors` para especificar a contagem de executores. Os parâmetros de configuração `spark.executor.cores` e `spark.executor.memory` são usados para modificar o tamanho do executor. Você pode alterar esses parâmetros, conforme mostrado nas seguintes etapas:

* Expanda a categoria **spark-thrift-sparkconf avançada** para atualizar os parâmetros `spark.dynamicAllocation.minExecutors`, `spark.dynamicAllocation.maxExecutors` e `spark.executor.memory`.

    ![Configurar o servidor Thrift do Spark](./media/apache-spark-resource-manager/spark-thrift-server-1.png "Configurar o servidor Thrift Spark")
* Expanda a categoria **spark-thrift-sparkconf personalizada** para atualizar o parâmetro `spark.executor.cores`.

    ![Configurar o parâmetro de servidor Spark thrift](./media/apache-spark-resource-manager/spark-thrift-server-2.png "Configurar o parâmetro de servidor Spark thrift")

### <a name="change-the-driver-memory-of-the-spark-thrift-server"></a>Alterar a memória do driver do servidor Spark thrift
A memória do driver do servidor Spark Thrift está configurada para 25% do tamanho da RAM do nó de cabeçalho, desde que o tamanho total da RAM do nó de cabeçalho seja maior que 14 GB. Você pode usar a interface do usuário do amAmbari para alterar a configuração da memória do driver, conforme mostrado na seguinte captura de tela:

* Na interface de usuário do Ambari, clique em **Spark**, em **Configurações**, expanda **spark-env avançado** e forneça o valor para **spark_thrift_cmd_opts**.

    ![Configurar a RAM do servidor Spark thrift](./media/apache-spark-resource-manager/spark-thrift-server-ram.png)

## <a name="reclaim-spark-cluster-resources"></a>Recuperar recursos do cluster Spark
Devido à alocação dinâmica do Spark, os únicos recursos consumidos pelo servidor Thrift são os recursos para os dois mestres de aplicativo. Para recuperar esses recursos, você deve interromper os serviços do servidor Thrift em execução no cluster.

1. Na interface de usuário do Ambari, no painel esquerdo, clique em **Spark**.
2. Na página seguinte, clique em **Servidor Thrift Spark**.

    ![Reiniciar o Thrift Server1](./media/apache-spark-resource-manager/restart-thrift-server-1.png "Reiniciar o Thrift Server1")
3. Você deve ver as duas cabeçalho em que o servidor Spark Thrift está em execução. Clique em um dos cabeçalho.

    ![Reiniciar o Thrift Servidor2](./media/apache-spark-resource-manager/restart-thrift-server-2.png "Reiniciar o Thrift Servidor2")
4. A próxima página lista todos os serviços em execução naquele cabeçalho. Na lista, clique no botão suspenso próximo ao servidor Thrift Spark e clique em **Parar**.

    ![Reiniciar Thrift Server3](./media/apache-spark-resource-manager/restart-thrift-server-3.png "Reiniciar Thrift Server3")
5. Repita essas etapas também no outro cabeçalho.

## <a name="restart-the-jupyter-service"></a>Reiniciar o serviço Jupyter
Inicie a interface do usuário da Web do amAmbari, conforme mostrado no início do artigo. No painel de navegação esquerdo, clique em **Jupyter**, em **Ações de Serviço** e em **Reiniciar Tudo**. Isso inicia o serviço Jupyter em todos os cabeçalho.

![Reiniciar Jupyter](./media/apache-spark-resource-manager/apache-ambari-restart-jupyter.png "Reiniciar Jupyter")

## <a name="monitor-resources"></a>Monitorar recursos
Inicie a interface do usuário do amYarn conforme mostrado no início do artigo. Na tabela de métricas de cluster na parte superior da tela, verifique os valores das colunas **memória usada** e **total de memória** . Se os dois valores estiverem próximos, talvez não haja recursos suficientes para iniciar o próximo aplicativo. O mesmo se aplica às colunas **VCores usadas** e **VCores total** . Além disso, no modo de exibição principal, se houver um aplicativo continuaram em **aceito** estado e não a transição para **executando** nem **falha** estado, isso também pode ser uma indicação de que ele não estiver obtendo recursos suficientes para iniciar.

![Limite de recursos](./media/apache-spark-resource-manager/apache-ambari-resource-limit.png "Limite de recursos")

## <a name="kill-running-applications"></a>Eliminar aplicativos em execução
1. Na interface do usuário do amYarn, no painel esquerdo, clique em **em execução**. Na lista de aplicativos em execução, determine o aplicativo a ser eliminado e clique na **ID**.

    ![Kill App1](./media/apache-spark-resource-manager/apache-ambari-kill-app1.png "Kill App1")

2. Clique em **eliminar aplicativo** no canto superior direito e, em seguida, clique em **OK**.

    ![Eliminar App2](./media/apache-spark-resource-manager/apache-ambari-kill-app2.png "Eliminar App2")

## <a name="see-also"></a>Consulte também
* [Rastrear e depurar trabalhos em execução em um cluster do Apache Spark no HDInsight](apache-spark-job-debugging.md)

### <a name="for-data-analysts"></a>Para analistas de dados

* [Apache Spark com Machine Learning: Use o Spark no HDInsight para analisar a temperatura de edifício usando dados HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark com Machine Learning: Use o Spark no HDInsight para prever os resultados da inspeção de alimentos](apache-spark-machine-learning-mllib-ipython.md)
* [Análise de log do site usando Apache Spark no HDInsight](apache-spark-custom-library-website-log-analysis.md)
* [Análise de dados de telemetria do Application insights usando o Apache Spark no HDInsight](apache-spark-analyze-application-insight-logs.md)
* [Usar Caffe em Azure HDInsight Spark para aprendizado profundo distribuído](apache-spark-deep-learning-caffe.md)

### <a name="for-apache-spark-developers"></a>Para desenvolvedores de Apache Spark

* [Criar um aplicativo autônomo usando Scala](apache-spark-create-standalone-application.md)
* [Executar trabalhos remotamente em um cluster Apache Spark usando o Apache Livy](apache-spark-livy-rest-interface.md)
* [Usar o plug-in de ferramentas do HDInsight para IntelliJ IDEA para criar e enviar aplicativos do Spark escalar](apache-spark-intellij-tool-plugin.md)
* [Usar o plug-in de ferramentas do HDInsight para IntelliJ IDEA para depurar aplicativos Apache Spark remotamente](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Usar notebooks do Apache Zeppelin com um cluster Apache Spark no HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels disponíveis para o Jupyter Notebook no cluster Apache Spark para HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Usar pacotes externos com blocos de notas Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalar o Jupyter em seu computador e conectar-se a um cluster HDInsight Spark](apache-spark-jupyter-notebook-install-locally.md)
