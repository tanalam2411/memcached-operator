load('ext://restart_process', 'docker_build_with_restart')


local_resource("Create Kind Cluster and image registry", cmd='./hack/kind-with-registry.sh')

local_resource("make generate manifests install", cmd="make generate manifests install")
local_resource("watch apis", cmd="make generate manifests install", deps=["api"], ignore=['*/*/zz_generated.deepcopy.go'])


local_resource("Build Manager", cmd="CGO_ENABLED=0 GOOS=${TARGETOS:-linux} GOARCH=${TARGETARCH:-amd64} go build -o bin/manager main.go",
    deps = [
        "main.go",
        "go.mod",
        "go.sum",
        "api",
        "controllers",
        "pkg",
    ],
)

docker_build_with_restart(
    "controller:latest",
    ".",
    dockerfile="tilt.Dockerfile",
    entrypoint=["/manager"],
    only=[
        './bin'
    ],
    live_update=[
        sync('./bin/manager', '/manager')
    ]
)


IMG = 'controller:latest'
manifests = local("cd config/manager && kustomize edit set image controller="+IMG+"; cd ../..; kustomize build config/default")

objects = decode_yaml_stream(manifests)
for o in objects:
    if o.get('kind') == 'Deployment' and o.get('metadata').get('name') == 'memcached-operator-controller-manager':
        o['spec']['template']['spec']['securityContext']['runAsNonRoot'] = False
manifests = encode_yaml_stream(objects)

k8s_yaml(manifests)

k8s_yaml(kustomize("./config/samples"))

tiltfile_path = config.main_path
if config.tilt_subcommand == 'up':
    print("{a}  Operator Local Development with Tilt  {b}".format(1, a="*"*10, b="*"*10))
    print("""
    \033[32m\033[32mKubernetes Operator Local Development with Tilt!\033[0m
    """)
    local("echo K8s Current Context :: $(kubectl config current-context)", quiet=False, echo_off=True)
    local("echo  Current Namespace :: $(kubectl config view --minify -o jsonpath='{..namespace}')", quiet=False, echo_off=True)
    local("echo GO Version :: $(go version | cut -d' ' -f3)", quiet=False, echo_off=True)
    local('echo Operator SDK Version :: $(operator-sdk version | cut -d\\\" -f2)', quiet=False, echo_off=True)
    print("{a}".format(1, a="*"*60))
if config.tilt_subcommand == 'down':
    print("Tilt Down ...")