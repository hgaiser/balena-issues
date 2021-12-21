FROM busybox:latest

# Place something in /mnt/some-volume as well.
# This shouldn't be done like this.. all data in `some-volume` should be created by the `config` service.
# This is just a demonstration that this can go wrong in an unexpected way.
# This shouldn't be an error, because we depend on the `config` service.
# However this seems to create a race condition.
# Whichever service starts first "owns" the volume.
# The other service cannot populate the volume with data anymore.
RUN mkdir -p /mnt/some-volume && echo "Foo" > /mnt/some-volume/EXTENSION_WAS_HERE

CMD while true; do sleep 1; done
