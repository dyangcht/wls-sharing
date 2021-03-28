# Deploy an application to WebLogic container image

### Testing application
There is a test application "hrapp2". It can be opened by JDeveloper 12c

### Deploy Oracle Database to OCP
```
$ cd domain-home-in-image
$ oc new-project database-namespace
$ kubectl create secret docker-registry regsecret \
        --docker-server=container-registry.oracle.com \
        --docker-username=yangorcl@gmail.com \
        --docker-password='PASSWORD' \
        --docker-email=yangorcl@gmail.com \
        -n database-namespace
$ oc apply -f db.yaml
$ cd ..
```

### Archiving the application
```
$ git clone https://github.com/dyangcht/wls-sharing.git
$ cd wls-sharing
$ cp hrapp2/deploy/hrapp2.ear domain-home-in-image/docker-images/OracleWebLogic/samples/12213-deploy-application
$ cd domain-home-in-image/docker-images/OracleWebLogic/samples/12213-deploy-application
$ rm -f archive.zip
$ jar -cvf archive.zip hrapp2.ear
```

### Gnereate the container image
```
$ docker build --build-arg APPLICATION_NAME=hrapp2 --build-arg APPLICATION_PKG=archive.zip -t dyangcht/12213-domain-with-app:v1.1 .
$ docker push dyangcht/12213-domain-with-app:v1.1
```

### Redeploy the domain to OCP
```
$ cd ../../../../
$ oc delete -f outputs/weblogic-domains/sample-domain1/domain.yaml
$ oc apply -f outputs/weblogic-domains/sample-domain1/domain.yaml
```

### Regenerate the domain file
```
$ ./create-domain.sh -i my-inputs.yaml -u weblogic -p welcome1 -o outputs
```
Then you can check the file `outputs/weblogic-domains/sample-domain1/domain.yaml` you created. The user name and password have to match to the following credential.

### Create a credentials for WebLogic Console Admin
```
$ create-weblogic-credentials.sh -u weblogic -p welcome1 -n sample-domain1-ns -d sample-domain1
```
It creates a secret called "sample-domain1-weblogic-credentials". You can check the name `webLogicCredentialsSecret` in the domain.yaml




`Reference: `https://oracle.github.io/weblogic-kubernetes-operator/ <br>
