ConfigMaps - configurations
Secrets - Sensitive

why?
Without config, it needs to be hardcoded
env:
  - name: App
    value: frontend
configurations changed = redeploy, not scalable across environments

With config - config lives outside the pod spec

envFrom
  - configMapRef:
       name: frontend-config
  - secretRef:
       name: frontend-secret

3 Ways to inject Config
=> env
=> Volume mounts
=> Cli

envFrom - inject all keys at once instead of one by one


By default k8s secrets are base64 encoded, not ecrypted

Secrets - 4types
Opaque                              → generic, user-defined (default)
kubernetes.io/tls                   → TLS cert + key (ingress, mTLS)
kubernetes.io/dockerconfigjson      → private registry image pull
kubernetes.io/service-account-token → SA tokens (auto-created by k8s)

Kubernetes Secrets store sensitive information. Opaque is the default type for general-purpose data such as database passwords and API keys. The TLS type stores a certificate and private key for HTTPS. The Docker config JSON type stores private registry authentication information for pulling images. The service-account-token type stores a token associated with a Kubernetes service account, although modern Kubernetes generally prefers short-lived projected tokens


IMMUTABLE:
Immutable config maps and secrets, data cannot be updated, must delete and recreate

volume - with no subpath, live update
eg: pod running => update configMap => env variabled inside pod is unchanged 

voume - with subpath, not live updated
eg: Pod running => update ConfigMap => file inside pod UPDATES automatically 

VOLUME MOUNTS:
mountPath: /usr/share/nginx/html
mountPath: /usr/share/nginx/html/index.html  # FAILS — can't mount dir over file

mountPath: /usr/share/nginx/html/index.html
subPath: index.html   # file-over-file = OK. But no live updates.

with items: - listed keys mounts
items:
  - key: index.html
    path: index.html

without items - all keys mounted as files

RBAC ON SECRETS:

ServiceAccount (app-sa) = a student ID card used by an application.
Role (secret-reader) = permission rules.
RoleBinding = connects the ID card to those permission rules.

# Who can read secrets in a namespace? (audit question in prod)
kubectl get rolebindings,clusterrolebindings -A | grep secret

# Check what a specific SA can do
kubectl auth can-i get secrets --as=system:serviceaccount:production:app-sa -n production

Configuration       Can read Secrets in production?
Role                allows get secrets + RoleBinding in production	Yes, within production
ClusterRole allows  get secrets + RoleBinding in production	Yes, within production only
ClusterRole allows  get secrets + ClusterRoleBinding	Yes, across all namespaces