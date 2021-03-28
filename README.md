# Deploy an application to WebLogic container image

### Testing application
There is a test application "hrapp2". It can be opened by JDeveloper 12c

```
$ git clone https://github.com/dyangcht/wls-sharing.git
$ cd wls-sharing
$ cp hrapp2/deploy/hrapp2.ear domain-home-in-image/docker-images/OracleWebLogic/samples/12213-deploy-application
$ cd domain-home-in-image/docker-images/OracleWebLogic/samples/12213-deploy-application
$ rm -f archive.zip
$ jar -cvf archive.zip hrapp2.ear
$ docker build --build-arg APPLICATION_NAME=hrapp2 --build-arg APPLICATION_PKG=archive.zip -t dyangcht/12213-domain-with-app:v1.1 .
$ docker push dyangcht/12213-domain-with-app:v1.1
$ cd ../../../../
$ oc delete -f outputs/weblogic-domains/sample-domain1/domain.yaml
$ oc apply -f outputs/weblogic-domains/sample-domain1/domain.yaml
```
