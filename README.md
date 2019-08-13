# K8S Pihole

[![Build status](https://dev.azure.com/thomas-illiet/k8s-pihole/_apis/build/status/k8s-pihole)](https://dev.azure.com/thomas-illiet/k8s-pihole/_build/latest?definitionId=7)

A sample deployment for pihole on a kubernetes cluster.
I'm running an on premise kubernetes cluster and i'm using MetaLB for loadbalancing.

## Installation

```shell
read -a ServiceIp -p 'Service IP: ' 
read -a NfsServer -p 'NFS Server: ' 
read -a NfsPath -p 'NFS Path: ' 

sed -i "s~#{nfs.server}#~$NfsServer~g" definition/02-pv.yaml
sed -i "s~#{nfs.path}#~$NfsPath~g" definition/02-pv.yaml
sed -i "s~#{service.ip}#~$ServiceIp~g" definition/05-service.yaml
```

```shell
$ kubectl apply -f ./definition/
```

## Testing DNS

Test by hitting the pihole service IP. Get the IP:

```shell
$ kubectl get service -n netboot-pihole 
NAME         TYPE           CLUSTER-IP       EXTERNAL-IP    PORT(S)                     AGE
pihole-tcp   LoadBalancer   10.98.250.7      192.168.1.22   80:31117/TCP,53:32707/TCP   37h
pihole-udp   LoadBalancer   10.109.168.121   192.168.1.22   53:30153/UDP                37h
```
  
The query the pihole-udp IP:
```shell
$ dig @192.168.1.22 powershell-du-zero.fr                                                                                          root@SRV-K8S-MASTER01

; <<>> DiG 9.9.4-RedHat-9.9.4-74.el7_6.2 <<>> @192.168.1.22 powershell-du-zero.fr
; (1 server found)
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 15153
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 512
;; QUESTION SECTION:
;powershell-du-zero.fr.         IN      A

;; ANSWER SECTION:
powershell-du-zero.fr.  59      IN      A       90.90.92.210

;; Query time: 161 msec
;; SERVER: 192.168.1.22#53(192.168.1.22)
;; WHEN: Tue Aug 13 13:03:59 CEST 2019
;; MSG SIZE  rcvd: 66
```
