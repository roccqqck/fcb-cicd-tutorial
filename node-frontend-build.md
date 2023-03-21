### references.
https://cli.vuejs.org/guide/deployment.html#docker-nginx


# git clone 
```
        stage('取得 Source Code') {
            steps {
                sh 'git -c http.sslVerify=false clone --branch ${tag} --depth 1 --single-branch https://${gitlab_user}:${gitlab_token}@${gitlab}/gitlab/${project}/${repository}.git'
                dir("${repository}") {
                    sh 'git checkout ${tag}'
                }
            }
        }
```


# docker pull and build

```
        stage('建置 Image') {
            steps {
                dir("${repository}") {
                    sh 'docker login ${harbor} -u ${harbor_user} -p ${harbor_token}'
                    sh 'docker pull devharbor.firstbank.com.tw/docker.io/library/node:16-bullseye-slim'
                    sh 'docker pull devharbor.firstbank.com.tw/docker.io/bitnami/nginx:1.22'
                    sh 'docker build --rm -f Dockerfile-${docker_env} -t ${harbor}/${project}/${image}:${tag} --progress=plain .'
                }
            }
        }
```




# multistage dockerfile

```
# Stage 1: Compile and Build angular codebase

# Use official node image as the base image
FROM devharbor.firstbank.com.tw/docker.io/library/node:16-bullseye-slim as build



# Stage 2: Serve app with nginx server
FROM devharbor.firstbank.com.tw/docker.io/bitnami/nginx:1.22


```


# custom npm default registry
```
    ## custom npm default registry
    npm config set registry https://devnexus.firstbank.com.tw/repository/npm-group/ ; \
    npm config set strict-ssl false ; \
    npm config set audit false ; \
```

# npm build
```
    npm install --verbose ; \
    npm run build ; \
    ls -alF
```




# copy static files to nginx
```
FROM devharbor.firstbank.com.tw/docker.io/bitnami/nginx:1.22


COPY --from=build /app/dist/i-leo-bank  /web

```



