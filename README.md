# Miscellaneous And Error Documentation

## Index
### Miscellaneous
- [Yarn 관리하는 Memory 설정](#Yarn-관리하는-Memory-설정)      
- [Terminal 디렉토리 및 파일 색상 변경](#Terminal-디렉토리-및-파일-색상-변경)  
- [Ubuntu Server 사용중인 노드 및 특정포트 종료](#Ubuntu-Server-사용중인-노드-및-특정포트-종료)
- [MySQL 명령어](#MySQL-명령어)
- [Ubuntu Server 설정](#Ubuntu-Server-설정)
### Error Documentation 
- [Hadoop 설치시 발생하는 오류 해결](#Hadoop-설치시-발생하는-오류-해결)
- [spark-shell 실행시 발생할 수 있는 경고](#spark-shell-실행시-발생할-수-있는-경고)
- [Cassandra 설정시 발생하는 오류 해결](#Cassandra-설정시-발생하는-오류-해결)   
- [Ignite Off-Heap Memory 부족 해결](#Ignite-Off-Heap-Memory-부족-해결)
- [Ignite Java Heap Space 부족 해결](#Ignite-JAVA-Heap-Space-부족-해결)

## Hadoop 설치시 발생하는 오류 해결

    1. 원인 : master에서 active standby 변경할 시 backup에서 바꾸는 경우.
       해결 : backup에서 namenode를 삭제하고, 다시 master에서 hdfs haadmin -getAllServiceState를 입력하고, 
              다시 backup에서 namenode를 실행하면 해결됨.
    
      
    2. 원인 1 : master에서 namenode 및 datanode가 실행이 안 될 경우
       원인 2 : namenode 알고있는 namenode id와 datanode 알고있는 namenode id가 서로 달라서 에러가 뜨는 것임.
       해결 : namenode format이 namenode, datanode가 실행되지 않을 때만 해야됨.
      
      
    3. 원인 : 갑자기 namenode가 동작하지 않을 경우.
       해결 : hadoop-namenode-master.log를 보면 됨.
              data/hdfs/namenode/current/VERSION 이 파일 권한이 없다는 경우가 많음.
              권한은 sudo chown -R user:user ./name
              그럼 chown 명령어로 ./name 밑에 있는 파일들을 전부 user 계정으로 사용자 및 그룹을 변경 시켜줌 
      
      
    4. 원인 : hdfs haadmin -getServiceState nn1 standby
              hdfs haadmin -getServiceState nn2 standby일 경우.
       해결 : 어떠한 방법을 해도 nn1이 active가 안되면 hdfs zkfc -formatZK을 입력하면 해결됨.
       
    5. 원인 : hdfs dfsadmin -report not working
       해결 : systemctl stop firewalld 방화벽을 해제 시 문제해결 


## spark-shell 실행시 발생할 수 있는 경고 

    1. 경고 메시지 :  Failed to created SparkJLineReader
       원인 : spark-shell을 실행시키는 user 홈 디렉토리에 Permission denied 경우 접근 권한이 없는 경우 발생함.
       해결 : spark-shell user 홈 디렉토리 chmod로 권한 추가함
       
       
    2. 경고 메시지 :  Service 'SparkUI' could not bind on port 4040. Attemption port 4041.
       원인 : Default로 기본 포트는 4040으로 시작하며 4041이 뜨는 이유는 
              spark-shell 실행 중이면 1증가로 4041포트로 접근하겠다는 경고임.
       해결 : 에러가 발생한 것이 아니기 때문에 신경을 안써도 됨.


## Yarn 관리하는 Memory 설정

    1. yarn.nodemanager.resource.cpu-vcores
    2. yarn.nodemanager.resource.memory-mb
    3. yarn.scheduler.minimum-allocation-mb
    4. yarn.scheduler.maximum-allocation-mb
    
## Cassandra 설정시 발생하는 오류 해결
    
    1. 경고 메시지 : Failed to connect to '127.0.0.1:7199': Connection refused (Connection refused)
       원인 : cassandra.yaml 구성 파일 오류 
       해결 : sudo rm -rf /var/lib/cassandra/data/system/*
    
    
## Terminal 디렉토리 및 파일 색상 변경
    export LS_COLORS="di=00;36:fi=00;37" 입력 후 source ~/.bashrc 해서 적용시키면 해결함. 
    
## Ubuntu Server 사용중인 노드 및 특정포트 종료
    sudo lsof -i :특정포트
    sudo kill -9 [프로세스ID]
    killall node 노드 서버 모두 종료 
       
## MySQL Error
    에러 : Data truncated for column
    원인 : 데이터의 일부분이 잘렸을 때 나오는 메세지로 컬럼 자료형의 범위를 초과 하였을 때 발생함. 
    해결 : DATE_FORMAT(sysdate(),'%Y%m%d')

## Ubuntu Server 설정  
    1. ubuntu server (sshd server active) 
       Since it is private, ssh server is disabled by default.
       ssh server 설치 명령어 : sudo apt install openssh-server 
       ssh 서버 시작 명령어 : 
                             - sudo systemctl enable sshd 
                             - sudo systemctl restart sshd 

## Ignite Off-Heap Memory 부족 해결
    원인 : Ignite 서버에서 start시 사용하는 Config용 XML파일에서 설정들을 로그 정보에서 제시해 준 3가지 방법으로 해결 
    해결 : 
            1. DataRegionConfiguration 설정 중 maxSize값을 늘려주면 해결 
            
            2. DataRegionConfiguration 설정 중 persistenceEnabled값을 추가해주면 해결 
               <property name="defaultDataRegionConfiguration">
                    <bean class="org.apache.ignite.configuration.DataRegionConfiguration">
                        <!-- 10 GB initial size. -->
                        <property name="initialSize" value="#{10L * 1024 * 1024 * 1024}"/>
                        <!-- 50 GB maximum size. -->
                        <property name="maxSize" value="#{50L * 1024 * 1024 * 1024}"/>
                        <property name="persistenceEnabled" value="true"/>
                        <!-- Increasing the buffer size to 1 GB. -->
                        <property name="checkpointPageBufferSize" value="#{1024L * 1024 * 1024}"/>
                    </bean>
               </property>
                    
            3. eviction 또는 expiration 정책을 설정 
               - eviction : 공간이 필요할 때 데이터를 특정 알고리즘에 따라 지우는 설정
               - expiration : 특정 기간 이후의 데이터를 지우는 설정

## Ignite Off-Heap Memory 부족 해결
    원인 : Ignite 서버에 과도한 요청이나 일정 범위를 벗어나는 데이터 요청이 들어오는 경우 Ignite에서 발생하는 에러 
    해결 : JVM 할당 메모리를 늘려주면 당장은 해결되지만, 리소스를 무한정 늘릴 수만은 없으니 서버 리소스 맞게 변경
           Ignite.sh 파일을 열어 JVM_OPTS 값을 변경
           
           vi Ignite.sh 
           JVM_OPTS="-Xms4g -Xmx8g -server -XX:MaxMetaspaceSize=4G" 서버 리소스에 맞게 할당하는 용량 늘려줌
