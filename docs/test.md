# Testing

Типы тестов, которые есть в этой репе:
- unit
- e2e

Для юнит тестов можно использовать тестовый контейнер, образ для которого можно собрать командой:
```
make docker-build-unit-test
```
Для e2e тестов можно использовать тестовый контейнер, образ для которого можно собрать командой:
```
make docker-build-e2e-test
```

## unit

Запускаются через ```make docker-run-unit-test```. Используют встроенную в Go поддержку тестирования.

## e2e

Для запуска тестов понадобится:
- [создать](https://docs.cloud.croc.ru/ru/services/kubernetes.html#creating) Kubernetes кластер в облаке с установленным aws-nlb-provider
- Задать переменные окружения:
  - `KUBECONFIG_FILE` - путь до файла конфигурации кубернетес на хост системе для монтирования в контейнер
  - `AWS_ENDPOINT_UNSECURE` - true/false в зависимости от того продакшн это или тестинг(подписаны ли сертификаты или самоподписаны)
  - `AWS_SECRET_ACCESS_KEY` - access key nlb пользователя кластера kubernetes
  - `AWS_ACCESS_KEY_ID` - key id nlb пользователя кластера kubernetes
- Запустить контейнер, выполнив: `make docker-run-e2e-test` 
- внутри контейнера запустить тесты - выполнив команду ```ginkgo --no-color -v --timeout 2h --fail-on-pending /service.test -- --kubeconfig=/kubeconfig --cluster-name=$CLUSTER_NAME --aws-region=$AWS_REGION --aws-vpc-id=$AWS_VPC_ID --test-image-registry=$IMAGE_REGISTRY --ip-family=ipv4 --ec2-endpoint=$AWS_EC2_ENDPOINT --elb-endpoint=$AWS_ELB_ENDPOINT```
Где:
- `$CLUSTER_NAME` - имя тестируемого кластера kubernetes
- `$AWS_REGION` - регион используемые в облаке(по дефолту k2)
- `$AWS_VPC_ID` - vpc ID в которой создан кластер kubernetes и где будут запущены тесты
- `$IMAGE_REGISTRY` - регистри для образов используемых в тестах(сейчас они доступны только в registry.cloud.croc.ru/kaas)
- `$AWS_EC2_ENDPOINT` - ec2 ендпоинт облака где развернут кластер kubernetes
- `$AWS_ELB_ENDPOINT` - elb ендпоинт облака где развернут кластер kubernetes
