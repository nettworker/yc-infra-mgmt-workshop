## Практическое задание #3. Работа с Terraform

Список задач практического задания:
* 3.1 [Развёртывание Managed Service for Kubernetes кластера](#h3-1)
* 3.2 [Подключение к кластеру Kubernetes и проверка его состония](#h3-2)

### 3.1 Развёртывание Managed Service for Kubernetes кластера <a id="h3-1"/></a>

В процессе выполнения будут развёрнуты слеудующие облачные ресурсы: 
* зональный кластер Kubernetes с одним `master node`
* одна группа узлов c одним `worker node` в группе

Подготовка входных данных для развёртывания:
```bash
cd ~/labs/lab-03-terraform
cp /usr/local/etc/terraform.rc ~/.terraformrc

export TF_VAR_sa_id=$(yc iam service-account list --limit=1 --format=json | jq -r .[].id)
vm_id=$(curl -s http://169.254.169.254/latest/meta-data/instance-id)
export TF_VAR_subnet_id=$(yc compute instance get --id=$vm_id --format=json | jq -r .network_interfaces[0].subnet_id)
export TF_VAR_net_id=$(yc vpc subnet get $TF_VAR_subnet_id --format=json | jq -r .network_id)
export TF_VAR_zone_id=$(yc vpc subnet get $TF_VAR_subnet_id --format=json | jq -r .zone_id)
```

Запуск развёртывания кластера Kubernetes:
```bash
terraform init
terraform plan
terraform apply

Enter a value: yes
```

Время развёртывания кластера и группы узлов составляет примерно 10-12 минут.

`Документация:`
* [YC Terraform provider. Кластер Kubernetes](https://registry.tfpla.net/providers/yandex-cloud/yandex/latest/docs/resources/kubernetes_cluster)
* [YC Terraform provider. Группа узлов кластера Kubernetes](https://registry.tfpla.net/providers/yandex-cloud/yandex/latest/docs/resources/kubernetes_node_group)
* [Создание кластера Kubernetes](https://cloud.yandex.ru/docs/managed-kubernetes/operations/kubernetes-cluster/kubernetes-cluster-create)


### 3.2 Подключение к кластеру Kubernetes и проверка его состояния <a id="h3-2"/></a>

```bash
# Проверить состояние кластера и группы узлов
yc k8s cluster list
yc k8s node-group list

# Получить данные для конфигурации kubectl
yc k8s cluster get-credentials --name=k8s --internal

# Проверить состояние кластера с помощью kubectl
kubectl cluster-info --kubeconfig /home/admin/.kube/config

# Настроить autocomplete для kubectl
cat << EOF >> $HOME/.bashrc
# kubectl
source <(kubectl completion bash)
alias k=kubectl
complete -o default -F __start_kubectl k
EOF
source $HOME/.bashrc

# Проверить состояние узлов кластера
kubectl get nodes
k get -A pods
k get ns

# Проверить работу kubectl autocomplete
k get apise <press Tab> --> k get apiservices.apiregistration.k8s.io
```

`Поздравляем! Вы успешно справились с заданием!`

### [ << задание 2 ](../lab-02-yc/README.md) || [задание 4 >>](../lab-04-crossplane/README.md)
### [ << оглавление ](../README.md)
