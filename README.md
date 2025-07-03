# Redis Helm Charts - Private Repository Setup Guide
This guide explains how to build, package, and host Redis-related Helm charts in your own private repository.  
It covers cloning charts, packaging them, setting up an HTTP server (Apache), and configuring Helm clients to use your repository.

## 1. Clone Helm Charts
```shell
$ mkdir -p /var/www/html/helm
$ mkdir helm-repo
$ git clone https://github.com/ruo91/operator-redis-helm-chart.git
```

## 2. Create Helm Packages
Package each chart to generate .tgz archives:
```shell
$ helm package redis
$ helm package redis-operator
$ helm package redis-cluster
$ helm package redis-replication
$ helm package redis-sentinel
```

## 3. Generate Helm Repository Index
Create or update your Helm repository index with the correct URL:
```shell
$ helm repo index --url http://repos.example.com/helm-repo --merge index.yaml .
```

## 4. Configure Private Repository (Apache Example)
### 4.1 Apache Configuration
Edit your Apache HTTP server config:

```shell
Alias /helm-repo /var/www/html/helm-repo
<Directory "/var/www/html/helm-repo">
Options Indexes FollowSymLinks
AllowOverride None
Require all granted
</Directory>
```

### 4.2. Restart Apache
```shell
$ systemctl restart httpd
```

## 5. Add the Helm Repository on Client
On your client machine, add the private Helm repository:
```shell
$ helm repo add redis http://repos.example.com/helm-repo
$ helm repo add redis-operator http://repos.example.com/helm-repo
$ helm repo add redis-cluster http://repos.example.com/helm-repo
$ helm repo add redis-replication http://repos.example.com/helm-repo
$ helm repo add redis-sentinel http://repos.example.com/helm-repo
```

## 6. Update Helm Repositories
```shell
$ helm repo update
```

## 7. List Configured Helm Repositories
```shell
$ helm repo list
NAME URL
redis http://repos.example.com/helm-repo
redis-cluster http://repos.example.com/helm-repo
redis-operator http://repos.example.com/helm-repo
redis-replication http://repos.example.com/helm-repo
redis-sentinel http://repos.example.com/helm-repo
```

## 8. Install Redis Charts
### 8.1 Install Redis Operator
```shell
$ helm install redis redis/redis-operator --namespace openshift-operators
```

### 8.2 Install Redis Standalone
```shell
$ helm install redis redis/redis --namespace redis
```

## :pushpin: Notes
Replace http://repos.example.com/helm-repo with your actual repository URL.  
Ensure your HTTP server allows directory listing if you want Helm to fetch the index.  
You can customize the release name (e.g., redis) during helm install.
