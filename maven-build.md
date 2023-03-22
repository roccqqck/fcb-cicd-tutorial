### references.
https://spring.io/guides/topicals/spring-boot-docker/

https://github.com/GoogleContainerTools/jib

https://github.com/GoogleContainerTools/jib/tree/master/jib-maven-plugin

https://github.com/GoogleContainerTools/jib/tree/master/examples/spring-boot

https://docs.docker.com/language/java/build-images/

# git clone
```
        stage('取得 Source Code') {
            steps {
                sh 'git -c http.sslVerify=false clone https://${gitlab_user}:${gitlab_token}@${gitlab}/gitlab/${project}/${repository}.git'
                dir("${repository}") {
                    sh 'git checkout ${tag}'
                }
            }
        } 
```



# maven clean and compile
```
        stage ('Clean up') {
            steps {
                dir("${repository}") {
                    sh "mvn clean -e ${SSL_INSECURE} ${MAVEN_PROFILE}"
                }      
            }
        }

        stage('Compile') {
            steps {
                dir("${repository}") {
                    sh "mvn compile -e ${SSL_INSECURE} ${MAVEN_PROFILE}"
                }      
            }
        }

```

# lint

```
        stage('檢查程式碼排版') {
            steps {
                 dir("${repository}") {
                    sh '[ ! -z "$(git status -s)" ] && exit 1 || echo "Good to go!"'
                 }    
            }
        }
```




# unit test

```
        stage('單元測試unit test') {
              steps {
                  dir("${repository}"){
                      sh "mvn test ${MAVEN_PROFILE} -e ${SSL_INSECURE} "
                  }
              }
        } 
```



# jib build image and push

```
        stage('建置 Image') {
            steps {
                dir("${repository}") {
                    sh 'mvn -Djib.from.auth.username=${HARBOR_ROBOT_NAME} -Djib.from.auth.password=${HARBOR_ROBOT_TOKEN} -Djib.to.auth.username=${HARBOR_ROBOT_NAME} -Djib.to.auth.password=${HARBOR_ROBOT_TOKEN} ${MAVEN_PROFILE} jib:build -e'
                }
            }
        }

```



# pom.xml
### jib configuration
```
        <plugin>
          <groupId>com.google.cloud.tools</groupId>
          <artifactId>jib-maven-plugin</artifactId>
          <version>${jib-maven-plugin.version}</version>
          <configuration>
            <from>
              <image>${docker-base-image}</image>
            </from>
            <to>
              <image>${docker-image-repository}/${docker-image-project}/${project.artifactId}</image>
              <tags>
                <tag>${project.version}</tag>
              </tags>
            </to>
        
          </configuration>
        </plugin>
```
```
  <groupId>tw.com.fcb.ibank</groupId>
  <artifactId>ibank-fe-remittance</artifactId>
  <version>0.0.29</version>
  <description>FCB iBank Fe Remittance</description>

  <properties>
    <!-- plugin configuration -->
    <docker-base-image>harbor.softleader.com.tw/library/zulu-openjdk-alpine:11-jre-taipei</docker-base-image>
    <docker-image-repository>harbor.softleader.com.tw</docker-image-repository>
    <docker-image-project>firstbank</docker-image-project>

    <softleader.central.repository>https://repo.maven.apache.org/maven2</softleader.central.repository>
    <softleader.releases.repository>http://svn.softleader.com.tw:8084/repository/maven-releases/</softleader.releases.repository>
    <softleader.snapshots.repository>http://svn.softleader.com.tw:8084/repository/maven-snapshots/</softleader.snapshots.repository>
  </properties>

<profiles>

    <profile>
      <id>fcb-dev</id>
      <properties>
        <softleader.releases.repository>https://devnexus.firstbank.com.tw/repository/maven-public/</softleader.releases.repository>
        <softleader.snapshots.repository>https://devnexus.firstbank.com.tw/repository/maven-public/</softleader.snapshots.repository>
        <softleader.central.repository>https://devnexus.firstbank.com.tw/repository/maven-public/</softleader.central.repository>
        <docker-base-image>devharbor.firstbank.com.tw/openjdk/zulu-openjdk-alpine:11-jre-taipei</docker-base-image>
        <docker-image-repository>devharbor.firstbank.com.tw</docker-image-repository>
        <docker-image-project>ibank</docker-image-project>
      </properties>
    </profile>

    <profile>
      <id>fcb-prd</id>
      <properties>
        <softleader.releases.repository>https://prdnexus.firstbank.com.tw/repository/maven-public/</softleader.releases.repository>
        <softleader.snapshots.repository>https://prdnexus.firstbank.com.tw/repository/maven-public/</softleader.snapshots.repository>
        <softleader.central.repository>https://prdnexus.firstbank.com.tw/repository/maven-public/</softleader.central.repository>
        <docker-base-image>prdharbor.firstbank.com.tw/openjdk/zulu-openjdk-alpine:11-jre-taipei</docker-base-image>
        <docker-image-repository>prdharbor.firstbank.com.tw</docker-image-repository>
        <docker-image-project>ibank</docker-image-project>
      </properties>
    </profile>

  </profiles>

```

