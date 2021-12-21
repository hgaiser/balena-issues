# Build args

The file in `.balena/balena.yml` defines some build args.
These build args are probably only used in Dockerfiles.
We are building, tagging and pushing to our private registry, meaning no Dockerfiles exist.
The build arg defined in `.balena/balena.yml` has no effect on the `docker-compose.yaml` file that is defined in this repo.

Trying to build with this configuration gives the following error:

```
$ balena build --fleet h_gaiser/test
[Build]   Building services...
[Build]   base Preparing...
[Info]    Building for amd64/genericx86-64-ext
[Build]   Built 1 service in 0 seconds
[Error]   Build failed.
(HTTP code 400) unexpected - invalid tag format 

Additional information may be available with the `--debug` flag.

For further help or support, visit:
https://www.balena.io/docs/reference/balena-cli/#support-faq-and-troubleshooting


✗ - status code 1
```

Most likely the build arg `$BUSYBOX_VERSION` is not replaced in `docker-compose.yaml`, meaning it tries to pull in a version of `busybox` that doesn't exist.

Not sure if this is a docker-compose limitation, or a balena limitation.
