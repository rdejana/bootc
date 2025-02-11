# Example2
In this example, example will reimplement example1 using Podman's CLI rather than the UI.

Open a terminal and change to the directory `bootc/example2`

Open the file `Containerfile` and review the contents.

```Dockerfile
FROM quay.io/fedora/fedora-bootc:42
# create a simple "secret"
RUN echo 'hello world' > /etc/mysecret
```
This is identical to the file used in example1.

## Building the image
In this example, the name of your image will be <host>/<repository>/example2 where host is your container registry host, e.g. quay.io, 
repository is the name organization or user.

Set the variable IMAGE_URL=<host>/<repository>/example2, replacing host and repository.


To build the image, run the command:
```bash
podman build -t $IMAGE_URL -f Containerfile .
```
You'll see output similar to:
```bash
STEP 1/2: FROM quay.io/fedora/fedora-bootc:42
STEP 2/2: RUN echo 'hello world' > /etc/mysecret
COMMIT xxxx/xxxx/example2
--> b9b35950028d
Successfully tagged xxxx/xxxx/example2:latest
b9b35950028dbf86c02b54ad2e67be3bb4f1196de7f3a52c69ddc6730eac336b
```
Now push the image: 
```bash
podman push $IMAGE_URL
```

## Building the disk image

### Config.json
In example1, we used the UI to add configure to our disk image build; in this example, we'll use a config.json file.
Review the contents of `config.json`
```bash
{
  "customizations": {
    "user": [
      {
        "name": "demo",
        "password": "changeit",
        "groups": [
          "wheel"
        ]
      }
    ],
    "kernel": {
      "append": "audit=0"
    }
  }
}

```
This file has the same customizations we used last time.

Before we build, let's define some variable to make the command easier.
In your terminal, set the following:
- BUILDER_NAME=example2-bootc-image-builder
- LOCAL_OUTPUT=<path to cloned repo>/output/example2
- CONFIG_JSON=<path to cloned repo>/example2/config.json
- BUILDER=quay.io/centos-bootc/bootc-image-builder:sha256-b4eb0793837e627b5cd08bbb641ddf7f22b013d5d2f4d7d593ca6261f2126550
- IMAGE_URL_TAG=<host>/<repository>/example2:latest
- TYPE=vmdk

```bash
podman run --rm --name $BUILDER_NAME --tty \
--privileged \
--security-opt \
label=type:unconfined_t \
-v $LOCAL_OUTPUT:/output/ \
-v \
/var/lib/containers/storage:/var/lib/containers/storage \
-v $CONFIG_JSON:/config.json:ro \
--label \
bootc.image.builder=true \
$BUILDER \
$IMAGE_URL_TAG \
--output \
/output/ \
--local \
--type $TYPE \
--target-arch arm64 --rootfs xfs
```

See https://github.com/osbuild/bootc-image-builder for additional details.

## Creating and running the VM
As with example1, start VMWare Fusion Start VMWare Fusion and create a new custom VM.
On the next screen, select `Linux` and `Fedora 64-bit Arm`, and press `Continue`.
On the next screen, select `Use an existing virtual disk` and select the image we built, e.g. <path to cloned repo>/output/example2/vmdk/disk.vmdk and press `Continue`.
Accept the default settings and press `Finsih`.  Save the VM and wait for it to start.

Log in with the demo/changeit.

You've now used Podman to create your bootable container.

Feel free to delete the VM and any resources created.