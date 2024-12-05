## container-modification

# Modifying a Container Image with Skopeo and Umoci

This guide documents the steps taken to modify a container image using `skopeo` and `umoci`. The modification involves adding a custom message to the image's filesystem.

## Prerequisites

Ensure the following tools are installed on your system:
- `skopeo`
- `umoci`
- `docker` (optional, for verifying results)

## Steps to Modify the Container Image

1. **Copy the Container Image**  
   Use `skopeo` to copy the OpenSUSE container image from a Docker registry to an OCI format:
   ```bash
   skopeo copy docker://opensuse/amd64:42.2 oci:opensuse:42.2
   ```

2. **Unpack the Image**  
   Use `umoci` to unpack the OCI image into a directory bundle:
   ```bash
   sudo umoci unpack --image opensuse:42.2 bundle
   ls -l bundle
   cd bundle/
   ```

3. **Apply the Modification**  
   Add a custom message to the root filesystem of the image:
   ```bash
   sudo echo "sandor r. bakos was here" > bundle/rootfs/my_change
   ```

   Confirm the modification:
   ```bash
   cd bundle/rootfs/
   ls
   ```

   Adjust permissions if necessary:
   ```bash
   sudo chmod 777 bundle/
   sudo chmod 777 bundle/rootfs/my_change
   ```

4. **Repack the Image**  
   Repack the modified bundle into a new OCI image:
   ```bash
   sudo umoci repack --image opensuse:new bundle
   ```

5. **Export and Import the Image into Docker**  
   Export the root filesystem as a TAR file and import it into Docker:
   ```bash
   sudo tar -cvf opensuse-new.tar -C bundle/rootfs .
   docker import opensuse-new.tar opensuse:new
   ```

6. **Verify and Run the Image in Docker**  
   List Docker images to verify the import and run the container interactively:
   ```bash
   docker images
   docker run -it opensuse:new /bin/bash
   ```

## Additional Commands Used

Here are some additional commands used during the process:
```bash
ls -l
cd bundle/
chmod 777 bundle/
sudo touch my_change
nano my_change
clear
```

## Notes

- **Permission Handling**: If you encounter permission issues, ensure directories and files have the necessary permissions using `chmod`.
- **Custom Changes**: The `my_change` file in the root filesystem contains the message "sandor r. bakos was here".
- **Adaptability**: This process can be adapted for other container images and modifications.

## Acknowledgment

This modification was performed by Sandor R. Bakos.  
Feel free to adapt and use this guide for similar tasks.
