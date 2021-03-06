DOCKER_REGISTRY=docker.io
DOCKER_PROJECT_ID=wojciech11
SERVICE_NAME=api-status
DOCKER_IMAGE_NAME=$(DOCKER_PROJECT_ID)/${SERVICE_NAME}
DOCKER_IMAGE_REPO_NAME=$(DOCKER_REGISTRY)/$(DOCKER_IMAGE_NAME)

docker_deploy:
	docker login --username=$${DOCKERHUB_USER} --password=$${DOCKERHUB_PASS}  && \
	for label in green blue; do \
		docker build -t $(DOCKER_IMAGE_NAME)-$${label} . -f Dockerfile-$${label} && \
		docker tag $(DOCKER_IMAGE_NAME)-$${label} $(DOCKER_IMAGE_REPO_NAME):$${label} && \
		docker push $(DOCKER_IMAGE_REPO_NAME):$${label} ; \
	done; \
	docker logout

docker_run_echo:
	for label in green blue ; do \
		echo "docker run --name $(SERVICE_NAME)-$${label} -p 8080:80 -d ${DOCKER_PROJECT_ID}/$(SERVICE_NAME)-$${label} "; \
	done

docker_stop_and_rm_echo:
	for label in green blue ; do \
		echo "docker stop $(SERVICE_NAME)-$${label}; docker rm $(SERVICE_NAME)-$${label}"; \
	done

apply:
	kubectl apply -f kube-nginx-blue.yaml
	kubectl apply -f kube-nginx-green.yaml
	kubectl apply -f kube-service-status.yaml

watch_curl:
	 export SVC_PORT=$$(kubectl get service api-status --output='jsonpath="{.spec.ports[0].nodePort}"' | tr -d '"') ; \
	 watch -n0.3 -x curl -s $$(minikube ip):$${SVC_PORT}

watch_get_po:
	 watch -n0.3 -x kubectl get po
