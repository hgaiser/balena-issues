# Building fails

Running the following fails:

```shell
balena build --fleet <fleet_name>
```

```
$ balena build --fleet h_gaiser/test
[Build]   Building services...
[Build]   base      Preparing...
[Build]   extension Preparing...
[Info]    Building for amd64/genericx86-64-ext
[Build]   base      Step 1/1 : FROM busybox:latest
[Build]   base       ---> ffe9d497c324
[Build]   base      Successfully built ffe9d497c324
[Build]   base      Successfully tagged base:latest
[Build]   Built 2 services in 0:04
[Error]   Build failed.
pull access denied for base, repository does not exist or may require 'docker login': denied: requested access to the resource is denied

Additional information may be available with the `--debug` flag.

For further help or support, visit:
https://www.balena.io/docs/reference/balena-cli/#support-faq-and-troubleshooting


✗ - status code 1
```

Presumably the build has started before the base image has been successfully taged.
Running `build` again directly after will succeed.
