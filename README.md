# envoy-adapter-demo

## Notes

cd ../envoy-adapter

---

	export CLI_HOME=$PWD/apigee-remote-service-cli
	export REMOTE_SERVICE_HOME=$PWD/apigee-remote-service-envoy

---

create SA with Apigee Analytics Agent role in GCP

create a key and download the json file

	export AX_SERVICE_ACCOUNT=<your-service-account-with-apigee-ax-agent-role>

---

	export ORG=your-apigeex-org
	export ENV=your-apigeex-env
	export RUNTIME=https://your-apigeex-hostname

## Apigee hybrid only

	export NAMESPACE=apigee 

---

	TOKEN=$(gcloud auth print-access-token);echo $TOKEN

---

cd apigee-remote-service-cli/

---

	./apigee-remote-service-cli provision --organization $ORG \
	--environment $ENV \
     	--runtime $RUNTIME \
	--analytics-sa $AX_SERVICE_ACCOUNT \
	--token $TOKEN > config-jg.yaml

---

mv config-jg.yaml ../../demos/common/

---

cd ../../demos/common/

---

mv config.yaml config.backup

mv config-jg.yaml config.yaml

---

### in the auth section of config.yaml

+ api_key_claim - example:

```
auth:
    api_key_claim: apiKey
```

+ modify the token endpoint for the 'jwt_provider_key' - example:

```
auth:
    api_key_claim: apiKey
    jwt_provider_key: http://34.140.148.116:8080/auth/realms/demo-ea/protocol/openid-connect/token
```

Doc:

https://cloud.google.com/apigee/docs/api-platform/envoy-adapter/v2.0.x/reference#configuration-file

---

Create a GKE cluster:

	gcloud services enable container.googleapis.com
	gcloud container clusters create envoy-gateway-cluster --zone europe-west1-b

---

	alias k=kubectl

	k create namespace apigee

	k apply -f config.yaml

---

	k create configmap standalone-envoy-config --from-file=./standalone-envoy-config.yaml -n apigee

---

	k apply -f standalone-envoy-manifest.yaml -n apigee

---
