# QuartzWorkManager调度器
外部系统如何引用QuartzWorkManager
1、初始化要执行的work，将work注册在zookeeper上
2、通过zk开关来控制work的启动、停止、调度频率
 <bean id="scheduler" class="org.quartz.impl.StdSchedulerFactory" factory-method="getDefaultScheduler"/>
    <bean id="retryPolicy" class="org.apache.curator.retry.ExponentialBackoffRetry">
        <constructor-arg value="1000"/>
        <constructor-arg value="20"/>
        <constructor-arg value="10"/>
    </bean>
    <bean id="zkClient" class="org.apache.curator.framework.CuratorFrameworkFactory" factory-method="newClient">
        <constructor-arg type="java.lang.String" value="${zookeeper.group.hosts}"/>
        <constructor-arg ref="retryPolicy"/>
    </bean>
    <bean id="workerRegistered" class="com.XXXX.worker.WorkerRegistered">
        <constructor-arg ref="zkClient"/>
        <constructor-arg value="/com/XXX/worker"/>
        <constructor-arg ref="scheduler"/>
    </bean>
    <bean id="initWorker" class="com.XXXX.InitWorker" init-method="init">
    <property name="scheduler" ref="scheduler"/>
        <property name="workerRegistered" ref="workerRegistered"/>
        <property name="cron" value="${zookeeper.group.cron}"/>
        <property name="map">
            <map key-type="java.lang.String">
                <entry key="workerInput">
                    <map key-type="java.lang.String">
                        <entry key="xxxx" value-ref="xxxx"/>
                    </map>
                </entry>
        </property>
    </bean>
