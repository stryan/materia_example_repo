# What is this repo?

This is an example [Materia](https://primamateria.systems) repository. It is useable both as-is or as a template for your own repository.

## To use as-is

0. (OPTIONAL) Fork this repository and modify the [MANIFEST.toml](./MANIFEST.toml) to have [Hosts.TargetHost] instead of [Hosts.localhost].
1. Install Materia on the target host.
2. On the target host, run `mkdir -p /etc/materia /var/lib/materia` to create default directories.
3. On the target host, enable the podman socket: `sudo systemctl enable --now podman.socket`
3. Copy [the key.txt](./key.txt) file to `/etc/materia/key.txt` on the target host.
4. On the target host, run the following podman command:
```
podman run --name materia --rm \
	--hostname localhost \
	--network host \
	--security-opt label=disable \
	--env SOPS_AGE_KEY_FILE=/etc/materia/key.txt \
    --env MATERIA_SOPS__SUFFIX=enc \
    --env MATERIA_SOPS__BASE_DIR=attributes \
	--env MATERIA_SOURCE__URL="git://github.com/stryan/materia_example_repo" \
	-v /run/dbus/system_bus_socket:/run/dbus/system_bus_socket \
	-v /run/podman/podman.sock:/run/podman/podman.sock \
	-v /var/lib/materia:/var/lib/materia \
	-v /etc/containers/systemd:/etc/containers/systemd \
	-v /etc/systemd/system:/etc/systemd/system \
	-v /etc/materia:/etc/materia:ro \
	ghcr.io/stryan/materia:latest update
	```

5. That's it! You should now see `freshrss` and `podman_exporter` running in `podman ps` and `systemctl list-units` on the target host.

## To use as an oci image source

There's a mise task to create a basic OCI image from this repository. Simply run `mise r build-image IMAGENAME`, where "IMAGENAME" is the full name of the image to be pushed i.e. ghcr.io/stryan/materia-example-repo:latest.
