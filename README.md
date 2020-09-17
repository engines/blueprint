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

Include any number of key-value pairs, for example:

```
"configuration": {
  "relayhost": "^^domain.fqdn^^:465"
}
```

The value of the above may be interpolated anywhere else in the blueprint (including the ancillary files)
be writing `^^configuration.relayhost^^`

# `bindings`

The general form of thise component is:

```
"bindings": [
  {
    "identifier": "",
    "descriptor": {},
    "variables": {}
  }
]
```

If `identifier` is omitted, it defaults to the descriptor's identifier.

Commonly, `variables` may be omitted also, in which case the default values in the lower
turtle's `binding_achor` will stand, unless overridden by the user. A simple example
of a binding is therefore:

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

The value of binding variables (included those accepted from the lower turtle's
`binding_anchor` may be interpolated anywhere else in the blueprint (including the ancillary files).
For example, `postgres.hostname`

# `binding_anchor`

A `binding_anchor` defines the variables a higher turtle must supply winding binding
to this one where the anchor appears. Commonly, default values are provided which
can be overridden by the higher turtle and/or the user.

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

Where interpolation appears, those values are resolved for and scoped in *above turtle*.
All above turtles have the own resolved values.

# images & containers

`images` and `containers` components have a very similar layout. `images` has
a specification for things done at packer time while `containers` components
specify things for provisioning. Under the hood `images` will generate Packer
stanzas (instructions) while `containers` will generate Terraform stanzas.

A simplistic example:

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

A new LXD image will be generated (in this example, with nothing more than the contents of the input image)
and used to generate components.

The example below, provisions docker images without packing an intermediary image:

```
"containers": [
  {
    "type": "docker",
    "image": "postgres:12.1"
  }
]
```

The example below packs some scripts and builds an LXD image without provisioning any containers.
Such an approach allows for layering up and number of images in an imaged-based
workflow approach.

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

We'd expect real-life blueprints to exploit various combinations of `images` and `containers`
depending on the blueprint developer's needs, intentions and design choices.

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

# Ancillary files

A blueprint designer may include files under certain directories which Spaces knows how
to process.

* `injections`
  - process and copy all directories and folders into the file structure of an image
  (copying late into containers is yet to be implemented)

* `packing`
  - scripts
    pack the scripts into the build

* `provisioning`
    - scripts
      - TBD
    - terraform
      expected to contain Terraform `.tf` which will be copied for inclusion in
      Terraform `plan` and `apply` actions
