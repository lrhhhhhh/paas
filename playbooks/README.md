##  安装k3s
```shell
ansible-playbook -i inventory/hosts --become --become-user=root playbooks/k3s.yaml
```

## 更新traefik配置
```shell
ansible-playbook -i inventory/hosts -e "certResolver=disable"  --become --become-user=root playbooks/traefik.yaml 
```

## cert-manager
```shell
ansible-playbook -i inventory/hosts  --become --become-user=root playbooks/cert-manager.yaml 
```


## 安装ArgoCD
```shell
ansible-playbook -i inventory/hosts -e '{"public":"false", "uninstall":true}' --become --become-user=root playbooks/argocd.yaml
```

## registry
```shell
docker run --entrypoint htpasswd httpd:2 -Bbn testuser testpassword > ~/htpasswd && echo -n ~/htpasswd | base64  
ansible-playbook -i inventory/hosts -e '{"protocol":"http","htpasswd":"L2hvbWUvbHJoYW9vL2h0cGFzc3dk", "uninstall":"true"}' --become --become-user=root playbooks/registry.yaml 
```
todo: https（需要注入证书） 
docker 需要设置insecure

## dashboard
```shell
ansible-playbook -i inventory/hosts  -e '{"uninstall": true}' --become --become-user=root playbooks/dashboard.yaml
```

## tekton 
```shell
ansible-playbook -i inventory/hosts -e '{"public":false}' --become --become-user=root playbooks/tekton.yaml
```

## k8s-nfs 
```shell
ansible-playbook -i inventory/hosts playbooks/nfs.yml
```