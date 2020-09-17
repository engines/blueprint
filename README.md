Spaces Blueprint Format
=======================

# Blueprint Object Structure

Components in a blueprint are optional. The top-level components are:

* `title`
* `description`
* `license`
* `configuration`
  - a list of configuration variables a running component
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
  - repositories
