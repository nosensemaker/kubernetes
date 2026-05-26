# Corrigindo erro do `crictl` e validação do CRI no containerd

O erro acontece porque o `crictl` está tentando utilizar o socket antigo do Docker:

```text
unix:///var/run/dockershim.sock
```

Em ambientes Kubernetes utilizando `containerd`, é necessário configurar o endpoint correto.

---

# Como corrigir

## 1. Criar o arquivo de configuração do crictl

Execute o comando abaixo:

```bash
sudo tee /etc/crictl.yaml <<EOF
runtime-endpoint: unix:///run/containerd/containerd.sock
image-endpoint: unix:///run/containerd/containerd.sock
timeout: 10
debug: false
EOF
```

---

## 2. Testar a comunicação do crictl

```bash
sudo crictl info
```

Se estiver correto, o comando deve retornar as informações do runtime sem erros.

---

## 3. Validar se o plugin CRI está carregado no containerd

Execute:

```bash
sudo ctr plugins ls | grep cri
```

O resultado esperado deve conter:

```text
io.containerd.grpc.v1.cri    ok
```

---

## 4. Reiniciar os serviços

```bash
sudo systemctl restart containerd
sudo systemctl restart kubelet
```

---

# Possível erro durante inicialização

Em alguns casos pode aparecer a mensagem:

```text
container runtime status check may not have completed yet
```

Esse erro normalmente acontece quando:

* O `containerd` ainda está inicializando.
* O kubelet iniciou antes do runtime ficar disponível.
* O plugin CRI não carregou corretamente.

---

# Como validar os serviços

## Verificar status do containerd

```bash
sudo systemctl status containerd
```

## Verificar status do kubelet

```bash
sudo systemctl status kubelet
```

## Verificar logs do kubelet

```bash
journalctl -u kubelet -f
```
