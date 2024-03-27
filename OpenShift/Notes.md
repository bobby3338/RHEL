decomment /etc/containers/registries.conf

mkdir -pv ~/.config/containers

~/.config/containers/registries.conf

unqualified-search-registries= ['registry.access.redhat.io'] [[registry]] location="registry.ocp4.example.com:8443"

podman login -u developer -p developer registry.ocp4.com:8443

decoding cat $XDG_RUNTIME_DIR/containers/auth.json echo "auth" | base64 -d developer:developer

podman image inspect skopeo inspect docker://

skopeo copy --dest-tls-verify=false docker://* docker://*

oc login -u admin -p redhatocp https://api.ocp4.example.com:6443

oc login -u 
(oc whoami -t) default-route-openshift-image-registry.apps.ocp4.example.com

skopeo copy

podman logout --all

podman image inspect simple-server --format '{{.Config.Cmd}}' podman image tag simole-server simple-server:0.1
