# Altserver-docker

A dockerized version of [AltServer-Linux](https://github.com/NyaMisty/AltServer-Linux) with Wi-Fi sync capability. 

## Run the server

### prerequisites

The only dependency is docker, but it is important to note two things:

- You might not be able to refresh apps if **usbmuxd** is running in the _host_ system, the docker guest already includes it and binds to the usb interface. Make sure your host system is **NOT** running it.
- You might need to be running the **avahi-daemon** in the host system.

### docker compose (recommended)

To start up the application, run the docker-compose stack:

```shell
docker compose up -d --build
```

### Using `docker run`

Just run `run.sh`, or manually in your terminal:

```shell
docker build . -t altserver-docker && \
docker stop altserver || true && \
docker rm altserver || true && \
sudo docker run -d \
  -v "./lib:/root/.config/Provision/lib" \
  -v "./logs:/altserver/logs" \
  -v "./bin:/altserver/bin" \
  -v "/dev/bus/usb:/dev/bus/usb" \
  -v "/var/lib/lockdown:/var/lib/lockdown" \
  -v "/var/run:/var/run" \
  -v "/sys/fs/cgroup:/sys/fs/cgroup:ro" \
  --name altserver \
  --network host \
  --privileged \
  altserver-docker 
```

## Logs

Logs will be stored in the directory where the container is ran inside `./logs`

## Optional environment variables

It's possible to override which architecture of altserver, netmuxd and anisette-server are downloaded by setting the following environment variables in the docker compose file:

```yaml
  environment:
    - OVERRIDE_ALTSERVER_ARCH=x86_64
    - OVERRIDE_NETMUXD_ARCH=x86_64
    - OVERRIDE_ANISETTE_ARCH=x86_64
```

or alternatively adding them in the `docker run` command before the image name:

```shell
  -e OVERRIDE_ALTSERVER_ARCH=x86_64 \
  -e OVERRIDE_NETMUXD_ARCH=x86_64 \
  -e OVERRIDE_ANISETTE_ARCH=x86_64 \
```

You can check for which architectures are available by checking the releases of each project:

- [AltServer-Linux](https://github.com/NyaMisty/AltServer-Linux/releases)
- [netmuxd](https://https://github.com/jkcoxson/netmuxd/releases)
- [Provision](https://github.com/Dadoum/Provision/releases) (this is the anisette-server)

## Provision libraries

Provision automatically pulls the libraries it requires (libCoreADI.so and libstoreservicescore.so) from the apple music android apk, but this requires a 60+MB download it does automatically, so I decided to include these in the root of the repo already and have the `docker-entrypoint.sh` decompress them where Provision normally would. This is optional and can be removed/commented from the `docker-entrypoint.sh` if you prefer to have Provision download and pull them from the apk.

## Updating lib.tar.xz

To update lib.tar.xz just run:

```shell
bash scripts/provision-deps-manual-download.sh
```

from the root of the repo.

## Credits

I authored this repo but I did NOT author the projects it depends on. In particular:

- [AltServer-Linux](https://github.com/NyaMisty/AltServer-Linux)
- [netmuxd](https://https://github.com/jkcoxson/netmuxd/)
- [Provision](https://github.com/Dadoum/Provision/)
- [Altstore](https://altstore.io)

And some other projects which inspired me to create this repo:

- [altserverd](https://github.com/hkfuertes/altserverd) which I took inspiration and some code from.
- [AltServer-Linux-PyScript](https://github.com/powenn/AltServer-Linux-PyScript/issues) which I audited and took inspiration from.

## Contributing

Feel free to open an issue or a PR if you have any suggestions or improvements to this repo. I'm always open to feedback and contributions!
