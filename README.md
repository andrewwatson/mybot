# Replace The Face Bot

This bot is based on the work of of https://github.com/zikes/mybot with some streamlining and some
packaging to use Docker containers and kubernetes to deploy and run.

It also uploads the resulting images to an S3 Bucket instead of serving them up itself
over HTTP to simplify the configuration and deployment.

## Docker build

To build the docker image, use the handy dandy build script I've provided.  It will
create the image which is based on the Golang 1.8 library image.  It will install the OpenCV
libraries and the other Go libraries it needs, copy the contents of the "faces" directory into
the image and the XML.

```
$ ./build.sh
```

## Kubernetes Deploy

### Secrets
You'll have to encode some Amazon/Slack credentials in kubernetes secrets to make them available
to your bot.

```
$ echo -n "actual token value" | base64
YWN0dWFsIHRva2VuIHZhbHVl
```

Take that value and substitute it into kubernetes-secrets-real.yaml file.  Then load it into the cluster:

```
$ kubectl create -f kubernetes-secrets-real.yaml
```

### Pods
The first time you deploy the face replace bot, you'll have to do it manually.  

```
$ kubectl create -f kubernetes-deployment.yaml
$ kubectl rollout status deployment/replace-the-face
deployment "replace-the-face" successfully rolled out

```

On subsequent deployments, though, the build.sh script can take care of updating
the pods on the fly

```
$ ROLL=1 ./build.sh
```