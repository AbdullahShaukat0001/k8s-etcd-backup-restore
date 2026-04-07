Kubernetes Etcd Backup & Restore (Simple Guide)
What is etcd?

Kubernetes has a brain

That brain is called etcd

It remembers everything:

pods
namespaces
configs
secrets

If etcd is gone… cluster forgets everything

Step 1: Check etcd
ps -ef | grep etcd

If you see etcd running → good

Step 2: Take Backup
ETCDCTL_API=3 etcdctl snapshot save /tmp/etcd-backup.db \
  --endpoints=https://127.0.0.1:2379 \
  --cacert=/etc/kubernetes/pki/etcd/ca.crt \
  --cert=/etc/kubernetes/pki/etcd/server.crt \
  --key=/etc/kubernetes/pki/etcd/server.key

Backup created!

Step 3: Verify Backup
ETCDCTL_API=3 etcdctl snapshot status /tmp/etcd-backup.db

If output comes → backup is OK

Step 4: Stop API Server
mv /etc/kubernetes/manifests/kube-apiserver.yaml /tmp/

Cluster goes to sleep

Step 5: Restore Backup
ETCDCTL_API=3 etcdctl snapshot restore /tmp/etcd-backup.db \
  --data-dir=/var/lib/etcd-restore
Step 6: Update etcd config

Edit file:

vi /etc/kubernetes/manifests/etcd.yaml

Change:

--data-dir=/var/lib/etcd-restore

Important: also update volume path if needed!

Step 7: Start API Server again
mv /tmp/kube-apiserver.yaml /etc/kubernetes/manifests/

Cluster is back

Step 8: Verify
kubectl get ns
kubectl get nodes
Important Learning

If you create something AFTER backup…

It will NOT come back after restore

Because backup = old state

What I Learned
etcd = brain
backup = memory
restore = time travel
If this helped you

Give this repo a ⭐
