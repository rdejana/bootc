# Example1
We are going to start with a simple example and use Podman Desktop to build.

Change to the directory `bootc/example1`

Open the file `Containerfile` and review the contents.

```Dockerfile
FROM quay.io/fedora/fedora-bootc:42
# create a simple "secret"
RUN echo 'hello world' > /etc/mysecret
```
This containerfile uses the fedora-bootc image as a base and creates and populates simple 'secret'.


In Podman Desktop, navigate to `Images` and press `Build'.

![Images in Podman Desktop](../images/podmanImages.png)

In the build dialog, enter the path to example1's Containerfile,
the directory to build in, and your image name (with repository, e.g. quay.io/rdejana/example1).

I'll be using aarch64 as the platform.

![Building image in Podman Desktop](../images/imageBuild.png)

Press build.

Once the image is built successfully, we'll push the image to our registry.  From the `Images` screen, click the 3 dots for your image select 'Push Image'.


Now navigate to `Bootable Containers` and select `Disk Images` and then press `Build`.

![Building bootc image in Podman Desktop](../images/bootcBuild.png)

Select the image that was just built, an output directory (<path to cloned repo>/output/example2) , and `Virtual Machine Disk image (*.vmdk)` as the output type.
Note, because Fedora was used as the base, XFS is automatically selected as the filesystem.  The platform should be set to aarch64.

Expand `Interactive build config` and add the following:
- A user with the name `demo`, password `changeit`, in the group `wheel`
- In kernel, enter `audit=0`.  This is done for this example as an easy way to keep audit message from displaying on the console.

Press `Build`.  This may take some time to complete.

Start VMWare Fusion; you should have the `Select the Installation Method` screen appear.  If it doesn't, go to `File`, `New`.
Select `Create custom virtual machine` and press `Continue`.
On the next screen, select `Linux` and `Fedora 64-bit Arm`, and press `Continue`.
On the next screen, select `Use an existing virtual disk` and select the image we built, e.g. <path to cloned repo>/output/example1/vmdk/disk.vmdk and press `Continue`.
Accept the default settings and press `Finsih`.  Save the VM and wait for it to start.

Once it is started, enter the user and password (demo/changeit).  Let's change the password with the `passwd` command.
Now let's make sure that our "secret" is set:
`cat /etc/mysecret`

And you should see `hello world`.

Now imagine that we've added the wrong value for the secret.  Let's go back and edit the Containerfile change the value.

```Dockerfile
FROM quay.io/fedora/fedora-bootc:42
# create a simple "secret"
RUN echo 'this is the right secret' > /etc/mysecret
```

Now rebuild the image using the same process as before.  When the build is complete, push the image into your registry.

After the new image is push, switch back to the VM and run the command:
```bash
sudo bootc update
```
You should see new layers being downloaded.  Once they are downloaded, run:
```bash
sudo bootc status
```
and you'll see output similar to the following that indicates that a new version is staged.

Reboot the VM with the command `sudo reboot`

When the VM restarts, log in with your new password (not `changeit`) and verify that the secret has been updated.

You may be asking, 'Why did the password change stick if this is an image based VM?' and that is a good question.

With bootable containers, the `/etc` directory is mutable by default.  Changes to contents of `/etc` in a container images, any added, update, or removed files, will be applied on upgrade, locally modified files "win" and are no updated.

The directory `/var/` is also locally mutable and is used for logs, home directories, container images, and host application state.

See https://docs.fedoraproject.org/en-US/bootc/filesystem/ for additional details.


Congratulations, you've built and upgraded your first bootable container VM.

Feel free to delete the VM and any resources created.
