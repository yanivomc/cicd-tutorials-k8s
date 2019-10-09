### Creating a Secret with a Docker Config
Run the following command, substituting the appropriate uppercase values:

kubectl create secret docker-registry <name> --docker-server=DOCKER_REGISTRY_SERVER --docker-username=DOCKER_USER --docker-password=DOCKER_PASSWORD --docker-email=DOCKER_EMAIL


### Add ImagePullSecrets to a service account
First, create an imagePullSecret, as described here. Next, verify it has been created. For example:
~~~
kubectl get secrets myregistrykey
The output is similar to this:

NAME             TYPE                              DATA    AGE
myregistrykey    kubernetes.io/.dockerconfigjson   1       1d
Next, modify the default service account for the namespace to use this secret as an imagePullSecret.
~~~
~~~
kubectl patch serviceaccount default -p '{"imagePullSecrets": [{"name": "myregistrykey"}]}'
Interactive version requires manual edit:

kubectl get serviceaccounts default -o yaml > ./sa.yaml
The output of the sa.yaml file is similar to this:

apiVersion: v1
kind: ServiceAccount
metadata:
  creationTimestamp: 2015-08-07T22:02:39Z
  name: default
  namespace: default
  resourceVersion: "243024"
  uid: 052fb0f4-3d50-11e5-b066-42010af0d7b6
secrets:
- name: default-token-uudge
Using your editor of choice (for example vi), open the sa.yaml file, delete line with key resourceVersion, add lines with imagePullSecrets: and save.
~~~
~~~
The output of the sa.yaml file is similar to this:

apiVersion: v1
kind: ServiceAccount
metadata:
  creationTimestamp: 2015-08-07T22:02:39Z
  name: default
  namespace: default
  uid: 052fb0f4-3d50-11e5-b066-42010af0d7b6
secrets:
- name: default-token-uudge
imagePullSecrets:
- name: myregistrykey
Finally replace the serviceaccount with the new updated sa.yaml file
~~~
~~~
kubectl replace serviceaccount default -f ./sa.yaml
~~~
Now, any new pods created in the current namespace will have this added to their spec:
~~~
spec:
  imagePullSecrets:
  - name: myregistrykey
~~~


#Let's start Pipelines!
Follow [this](https://www.google.com) link for Tutorials 
