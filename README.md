# Tekton Deep Dive 2023 Mar

* create new namespace `tekton-deep-dive`
* create new app locally

```
quarkus create app <your-package>:tekton-deep-dive \
    --extension='resteasy-reactive-jackson'
```

* test new app locally

```
cd tekton-deep-dive && quarkus dev
```

* after the app started we can test the REST interface

```
curl http://localhost:8080/hello
```

* remove `maven-wrapper.jar` from `.mvn/wrapper/.gitignore` file
* create GitHub Repo `quarkus-app-deep-dive-cicd`, initialize git repo, commit and push it

```
git remote add origin git@github.com:<github-org>/quarkus-app-deep-dive-cicd.git
git branch -M main
git push -u origin main
```

* have a look at the Pipelines operator, installation method and the ui of OpenShift pipelines

* create Pipeline and it's tasks + webhooks etc. and have a look at all the resources (pipeline with tasks and steps, maven-cache, event-listener, trigger-template and trigger-binding)

```
oc apply -f ./pipeline
```

* create webhhok in GitHub `repository -> settings -> webhooks -> add webhook`
  * use Payload Url `http://el-quarkus-app-event-listener-tekton-deep-dive.apps.<openshit-url>/`
  * with content-type `application/json`

* Change code in implementation and tests, commit and push it to GitHub
* Have a look at the pipelinerun with tekton logs

```
tkn pipelinerun logs -f -n tekton-deep-dive --last
```

* after build tag and deploy the image with

```
oc tag quarkus-app-deep-dive-cicd:<tag> quarkus-app-deep-dive-cicd:latest
oc new-app --image-stream="quarkus-app-deep-dive-cicd:latest"
```

* expose the service

```
oc expose svc/quarkus-app-deep-dive-cicd
```

* add https support

```
oc patch route quarkus-app-deep-dive-cicd -p "{\"spec\":{\"tls\":{\"termination\": \"edge\"}}}"
```

* make a second change to the application and look at the acceleration of build with PVC usage

* tag the new built image as latest and have a look at the updated deployment

* install the nexus from https://github.com/marcoklaassen/nexus-ocp and configure it

* redeploy the pipeline and let is use the nexus mirror

```
oc apply -f ./pipeline-with-nexus
```
