# Spaces Blueprint Format

Components in a blueprint are optional. The top-level components are:

* `title`
* `description`
* `license`
* `configuration`
  - a list of configuration variables of a running component
  - used for values applying to a container's existence, free of consideration about turtles above or below
  - may contain default values
  - values may be changed by a user
* `bindings`
  - an array of objects, each nominating a turtle below that this blueprint depends on  
* `binding_anchor`
  - a list of variables, stating what data is needed from turtles above in a corresponding `bindings` item
  - may contain default values
  - turtles above should supply values for non-default values and may override default values
  - values may be changed by a user
* `images`
  - the component that defines packing for an image
  - output images may be saved or become input for the `containers` component
* `containers`
  - the component that defines provisioning for a component
  - in the case where no packing is necessary, takes specific image name
  - defaults to taking the output of `images` component
* `packages`
  - james & richard?
* `os_packages`
  - aka system packages
* `modules`
- james & richard?
* `repositories`
- james?

# `configuration`

```
"configuration": {
  "relayhost": "^^domain.fqdn^^:465"
}
```

# `bindings`

```
"bindings": [
  {
    "identifier": "",
    "descriptor": {},
    "variables": {}
  }
]
```

```
"bindings": [
  {
    "descriptor": {
      "repository": "https://github.com/MarkRatjens/postgres.git",
      "branch": "hashidocker"
    }
  }
]
```

# `binding_anchor`

```
"binding_anchor": {
  "variables": {
    "hostname": "^^identifier^^.^^universe.host^^",
    "port": "5432",
    "name": "^^identifier^^",
    "username": "^^identifier^^_user",
    "password": "^^random(10)^^"
  }
}
```

# images & containers

```
"images": [
  {
    "type": "lxd",
    "image": "engines/beowulf/base/latest/ci"
  }
],
"containers": [
  {
    "type": "lxd"
  }
]
```

```
"containers": [
  {
    "type": "docker",
    "image": "postgres:12.1"
  }
]
```

```
"images": [
  {
    "type": "lxd",
    "image": "devuan/beowulf/debootstrap",
    "scripts": {
      "shell": [
        "provision-files",
        "locale",
        "apt",
        "packages",
        "supervisor",
        "sshd",
        "cleanup"
      ]
    }
  }
]
```

## `images`
## `containers`


# `packages`

```
"packages": [
  {
    "descriptor": {
      "value": "https://github.com/publify/publify.git"
    }
  }
]
```

```
"packages": [
  {
    "descriptor": {
      "repository": "http://files.phpmyadmin.net/phpMyAdmin/4.6.4/phpMyAdmin-4.6.4-all-languages.tar.gz",
      "extraction": "tar -xzpf",
      "extracted_path": "phpMyAdmin-4.6.4-all-languages"
    }
  }
]
```

# `os_packages`

```
"os_packages": [
  {
    "type": "debian",
    "name": "postfix"
  }
]
```

# `modules`

```
"modules": [
  {
    "name": "django",
    "type": "python"
  },
  {
    "name": "node-red",
    "type": "npm"
  }
```

# `repositories`

```
"repositories": [
  {
    "descriptor": {
      "identifier": "opencpn",
      "repository": "ppa:opencpn/opencpn",
      "key_id": "",
      "key_url": ""
    }
  }
]
```
