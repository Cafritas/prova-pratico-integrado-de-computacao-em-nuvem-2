# Prova Prática Integrada – Computação em Nuvem

**Instituição:** Anhanguera
**Disciplina:** Computação em Nuvem
**Professora:** Simone Tatiane do Canto
**Aluna:** Carine Mesquita Freitas

## 1. Fundamentos da Computação em Nuvem

### Questão 1 – Qual é a diferença entre IaaS e SaaS? Cite um exemplo de utilização de cada modelo.

IaaS é um modelo que oferece recursos de infraestrutura pela internet, como servidores, armazenamento e redes. Um exemplo é o Amazon EC2, utilizado para criar servidores virtuais.

SaaS é um programa disponibilizado pela internet, sem a necessidade de instalação no computador do usuário. Um exemplo é o Google Drive, utilizado para armazenar e compartilhar arquivos.

### Questão 2 – Uma empresa utiliza servidores próprios e também recursos de um provedor de nuvem pública. Qual modelo de implementação em nuvem essa empresa utiliza?

A empresa utiliza o modelo de nuvem híbrida, pois combina sua infraestrutura própria (nuvem privada) com os recursos oferecidos por um provedor de nuvem pública.

### Questão 3 – Qual é a principal diferença entre uma máquina virtual e um contêiner?

A máquina virtual possui um sistema operacional próprio, funcionando de forma independente. Já o contêiner compartilha o sistema operacional da máquina hospedeira, sendo mais leve, rápido e consumindo menos recursos.
## 2. Execução de Aplicativo com Docker

**Objetivo:** Executar um servidor NGINX utilizando o Docker, verificar seu funcionamento e personalizar a página web.

### Evidência 1 – Contêiner em execução

Comando utilizado:

```bash
docker ps
```

Descrição: o comando docker ps lista os contêineres em execução. O resultado esperado é uma linha com o contêiner nginx-carine, imagem nginx, status "Up" e porta 8080->80/tcp.

### Evidência 2 – Página personalizada com meu nome

Para executar o NGINX e personalizar a página, foram utilizados os seguintes comandos:

```bash
docker run -d --name nginx-carine -p 8080:80 nginx
echo '<h1>Pagina personalizada - Carine Mesquita Freitas</h1>' > index.html
docker cp index.html nginx-carine:/usr/share/nginx/html/index.html
```

Para verificar o funcionamento:

```bash
curl http://localhost:8080
```

Descrição: o resultado esperado é o texto "Pagina personalizada - Carine Mesquita Freitas", que mostra que a página foi substituída com sucesso.
## 3. Execução da Aplicação no Kubernetes

**Objetivo:** Criar um Deployment e executar um Pod utilizando o Kubernetes.

Para criar a página personalizada, foi utilizado:

```bash
echo '<h1>Aplicacao Kubernetes - Carine Mesquita Freitas</h1>' > index.html
```

Em seguida, foi criado um configmap:

```bash
kubectl create configmap pagina-carine --from-file=index.html
```

Foi criado o arquivo de configuração do Deployment, salvo como deployment.yaml:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-carine
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nginx-carine
  template:
    metadata:
      labels:
        app: nginx-carine
    spec:
      containers:
      - name: nginx
        image: nginx
        ports:
        - containerPort: 80
        volumeMounts:
        - name: pagina
          mountPath: /usr/share/nginx/html
      volumes:
      - name: pagina
        configMap:
          name: pagina-carine
```

Para aplicar o arquivo:

```bash
kubectl apply -f deployment.yaml
```

Comando utilizado para listar os Pods:

```bash
kubectl get pods
```

Para verificar a página personalizada:

```bash
kubectl exec deployment/nginx-carine -- curl -s http://localhost
```

### Evidência 3 – Pod em execução e mensagem personalizada

Descrição: o comando kubectl get pods lista os Pods. O resultado esperado é um Pod nginx-carine-xxxxx com READY 1/1 e STATUS Running. O comando kubectl exec deve retornar "Aplicacao Kubernetes - Carine Mesquita Freitas".

## 4. Escalabilidade e Autorrecuperação

Para aumentar a quantidade de Pods da aplicação para três, foi utilizado o comando:

```bash
kubectl scale deployment nginx-carine --replicas=3
```

Comando utilizado para listar os Pods:

```bash
kubectl get pods
```

### Evidência 4 – Três Pods em execução

Descrição: após kubectl scale --replicas=3, o comando kubectl get pods deve listar três Pods nginx-carine, todos com STATUS Running.

### Evidência 5 – Autorrecuperação

Primeiro, foi consultada a lista de Pods:

```bash
kubectl get pods
```

Depois, foi excluído um dos Pods:

```bash
kubectl delete pod NOME_DO_POD
```

Em seguida, foi realizada uma nova consulta:

```bash
kubectl get pods
```

O Kubernetes identifica a exclusão e cria automaticamente outro Pod para manter a quantidade de réplicas definida no Deployment.

Descrição: após kubectl delete pod, o Pod excluído fica em Terminating e o Deployment cria um novo Pod automaticamente. Na nova consulta, aparecem novamente três Pods, sendo um deles recém-criado, com AGE de poucos segundos.

## 5. Disponibilização da Aplicação

**Objetivo:** Criar um Service e verificar a comunicação HTTP dentro do cluster Kubernetes.

### Evidência 6 – Service e acesso HTTP

Para disponibilizar a aplicação dentro do cluster, foi criado um Service:

```bash
kubectl expose deployment nginx-carine --name=nginx-carine --port=80 --target-port=80 --type=ClusterIP
```

Comando para listar os Services:

```bash
kubectl get services
```

Para testar a comunicação HTTP dentro do cluster:

```bash
kubectl run teste --image=curlimages/curl --rm -it --restart=Never -- curl -s http://nginx-carine
```

O comando realiza uma requisição HTTP ao serviço NGINX e apresenta o conteúdo da página personalizada.

Descrição: o comando kubectl get services deve mostrar o Service nginx-carine, tipo ClusterIP, porta 80. O teste com curl deve retornar o conteúdo da página personalizada com o nome Carine Mesquita Freitas.

## 6. Interpretação dos Resultados

### Questão 1 – Qual a diferença entre uma imagem Docker e um contêiner em execução?

A imagem Docker é um modelo que contém tudo o que é necessário para executar uma aplicação. Já o contêiner é a instância dessa imagem em funcionamento, executando a aplicação de forma isolada.

### Questão 2 – Qual a função de um Deployment no Kubernetes?

O Deployment é responsável por gerenciar os Pods da aplicação, controlar a quantidade de réplicas e garantir que elas permaneçam em execução. Também permite atualizar e administrar as aplicações.

### Questão 3 – O que aconteceu quando você excluiu um Pod? Por que isso aconteceu?

Quando excluí um Pod, o Kubernetes identificou que a quantidade de réplicas estava abaixo do esperado e criou automaticamente outro Pod para substituí-lo. Isso aconteceu porque o Deployment mantém o número de réplicas configurado.

### Questão 4 – Qual é a finalidade de um Service no Kubernetes?

O Service permite a comunicação com os Pods por meio de um endereço e uma porta estáveis. Ele também encaminha as requisições para os Pods disponíveis, facilitando o acesso à aplicação dentro do cluster.

## 7. Conclusão

Durante a atividade prática, aprendi a utilizar o Docker para executar e personalizar uma aplicação com NGINX. Também aprendi a criar e gerenciar Pods e Deployments no Kubernetes. Consegui compreender como funciona a escalabilidade e a recuperação automática dos Pods. Por fim, aprendi a criar um Service e testar a comunicação HTTP dentro do cluster.
