# Corrigindo erro `SysctlForbidden` no Kubernetes

O erro `SysctlForbidden` normalmente aparece no Kubernetes quando um Pod tenta definir um `sysctl` que não é permitido pelo cluster ou pelo nó.

## Exemplo do erro

```text
SysctlForbidden
```

---

# Como corrigir

## 1. Editar a configuração do Kubelet

Abra o arquivo de configuração do kubelet:

```bash
sudo vim /var/lib/kubelet/config.yaml
```

---

## 2. Adicionar os parâmetros permitidos

Adicione os seguintes parâmetros no arquivo:

```yaml
allowedUnsafeSysctls:
  - "net.ipv6.conf.all.disable_ipv6"
  - "net.ipv6.conf.default.disable_ipv6"
```

---

## 3. Reiniciar o serviço do Kubelet

```bash
sudo systemctl restart kubelet.service
```

---

## 4. Reiniciar o containerd

```bash
sudo systemctl restart containerd.service
```

---

# Validando a configuração

Após reiniciar os serviços, verifique se o kubelet voltou corretamente:

```bash
sudo systemctl status kubelet
```

E confira se o Pod consegue iniciar normalmente:

```bash
kubectl get pods -A
```
