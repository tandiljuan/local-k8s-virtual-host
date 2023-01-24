# Controls Tilt’s behavior with regard to its own version.
# https://docs.tilt.dev/api.html#api.version_settings
version_settings(constraint='>=0.30.11')

# Builds a docker image.
# https://docs.tilt.dev/api.html#api.docker_build
docker_build(
    'k3d-registry.potato.com:12345/potato/web:0.1',
    context='./potato-web'
)

# Call this with a path to a file that contains YAML, or with a Blob of YAML.
# https://docs.tilt.dev/api.html#api.k8s_yaml
k8s_yaml('potato-web.yaml')

# Configures or creates the specified Kubernetes resource.
# https://docs.tilt.dev/api.html#api.k8s_resource
k8s_resource(
    new_name='Potato WEB ingress',
    objects=['potato-web-ingress'],
    links=[
        'blue.potato.com',
        'green.potato.com'
    ]
)
